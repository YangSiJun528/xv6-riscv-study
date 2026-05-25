# xv6 Kernel 11 코드 정리: Memory Layout

## 읽는 순서

- `docs/reference/scripts/ko/11-xv6-kernel-11-memory-layout-NWicJxVENjg.md`
- `kernel/memlayout.h`: 물리 주소와 특수 가상 주소 배치
- `kernel/vm.c`: kernel page table mapping
- `kernel/proc.h`, `kernel/trampoline.S`: trapframe/trampoline 사용 이유

## 핵심

이 강의는 "메모리 안에 무엇이 어디에 놓이는가"를 보는 강의다. 중요한 축은 세 개다.

- physical memory: QEMU가 제공하는 실제 주소 공간
- kernel virtual address space: 커널이 사용하는 하나의 page table
- user virtual address space: process마다 다른 page table

## physical memory

```text
0x00001000  boot ROM
0x02000000  CLINT
0x0c000000  PLIC
0x10000000  UART
0x10001000  virtio disk
0x80000000  kernel load address, RAM 시작
PHYSTOP     xv6가 쓰는 RAM 끝
```

현재 repo에서 `KERNBASE`는 `0x80000000`, `PHYSTOP`은 `KERNBASE + 128MB`다. `end`부터
`PHYSTOP` 전까지는 `kalloc()`이 4KB page 단위로 관리한다.

## kernel virtual memory

커널은 하나의 kernel page table을 만들고 모든 CPU가 공유한다. `kvmmake()`는 UART, virtio, PLIC,
kernel text, kernel data/RAM, trampoline, kernel stack들을 매핑한다.

RAM/MMIO 대부분은 direct mapping이다.

```text
kernel VA 0x80000000 -> PA 0x80000000
kernel VA UART0      -> PA UART0
kernel VA VIRTIO0    -> PA VIRTIO0
kernel VA PLIC       -> PA PLIC
```

그래서 커널은 RAM이나 memory-mapped device를 다룰 때 같은 숫자를 virtual address처럼 써도 된다.
다만 kernel page table 전체가 direct map인 것은 아니다. 같은 kernel page table 안에 direct-map
영역과 high virtual address 영역이 함께 있다.

- `KERNBASE..PHYSTOP`, `UART0`, `VIRTIO0`, `PLIC`: VA와 PA 숫자가 같은 direct map
- `Kstack`: process마다 하나씩 있는 kernel stack을 높은 kernel VA에 매핑
- `Trampoline`: kernel text 안의 trampoline code를 `MAXVA - PGSIZE`에 한 번 더 매핑

```text
VA addr      Virtual Addresses                       PA addr      Physical Addresses
             ----------------------------------                   ----------------------------------
MAXVA        +--------------------------------+      2^56 - 1     +--------------------------------+
             |  Trampoline               R-X  |                   |  Unused                        |
             +--------------------------------+                   |                                |
             |  Guard page               ---  |                   |                                |
             +--------------------------------+                   |                                |
             |  Kstack 0                 RW-  |                   |                                |
             +--------------------------------+                   |                                |
             |  Guard page               ---  |                   |                                |
             +--------------------------------+                   |                                |
             |  Kstack 1                 RW-  |                   |                                |
             +--------------------------------+                   |                                |
             |  ...                           |                   |                                |
             |                                |                   |  ...                           |
             |                                |                   |                                |
PHYSTOP      +--------------------------------+      PHYSTOP      +--------------------------------+
             |  Free memory              RW-  |                   |  Physical memory RAM           |
             |  kalloc page region            |                   |  128 MB                        |
             |                                |                   |                                |
             |  ...                           |                   |  ...                           |
             |                                |                   |                                |
             +--------------------------------+                   |                                |
             |  Kernel data              RW-  |                   |                                |
             +--------------------------------+                   |                                |
             |  Kernel text              R-X  |                   |                                |
0x80000000   +--------------------------------+      0x80000000   +--------------------------------+
             |  Unused                        |                   |  Unused and IO area            |
             |                                |                   |                                |
             |                                |                   |                                |
             |  ...                           |                   |  ...                           |
             |                                |                   |                                |
             +--------------------------------+                   +--------------------------------+
             |  VIRTIO disk              RW-  |                   |  VIRTIO disk        1 page     |
0x10001000   +--------------------------------+      0x10001000   +--------------------------------+
             |  UART0                    RW-  |                   |  UART0              1 page     |
0x10000000   +--------------------------------+      0x10000000   +--------------------------------+
             |  Unused                        |                   |  Unused                        |
             |                                |                   |                                |
             |  ...                           |                   |  ...                           |
             |                                |                   |                                |
             +--------------------------------+                   +--------------------------------+
             |  PLIC                     RW-  |                   |  PLIC               64 MB      |
             |                                |                   |                                |
0x0C000000   +--------------------------------+      0x0C000000   +--------------------------------+
             |  Unused                        |                   |  Unused                        |
             |                                |                   |                                |
             |  ...                           |                   |  ...                           |
             |                                |                   |                                |
             |                                |                   +--------------------------------+
             |                                |                   |  CLINT                         |
             |                                |      0x02000000   +--------------------------------+
             |                                |                   |  Unused                        |
             |                                |                   |                                |
             |                                |                   |  ...                           |
             |                                |                   |                                |
             |                                |                   +--------------------------------+
             |                                |                   |  boot ROM                      |
             |                                |      0x1000       +--------------------------------+
             |                                |                   |  Unused                        |
             |                                |                   |  ...                           |
0            +--------------------------------+      0            +--------------------------------+
```

## user virtual memory

process마다 user page table은 다르지만, 큰 모양은 같다.

```text
                             MAXVA +---------------------------------+
                                   | TRAMPOLINE              -kr-x   |
         TRAMPOLINE = MAXVA-PGSIZE +---------------------------------+
                                   | TRAPFRAME               -krw-   |
 TRAPFRAME = TRAMPOLINE - PGSIZE   +---------------------------------+
                                   |                                 |
                                   | unused                  -----   |
                                   |                                 |
                             p->sz +---------------------------------+
                                   | heap                    ukrw-   |
                                   |              ^                  |
                                   |              |                  |
                                   |              |                  |
                         stack_top +---------------------------------+
                                   | user stack              ukrw-   |
                                   |              |                  |
                                   |              |                  |
                                   |              v                  |
                         stackbase +---------------------------------+
                                   | guard page              -krw-   |
        PGROUNDUP(elf_end) = guard +---------------------------------+
                                   | data + bss              ukrw-   |
                                   |                                 |
                                   | text                    ukr-x   |
                              0x0  +---------------------------------+
```

권한 표기는 `user/kernel/read/write/execute` 순서다. `u`가 없으면 user mode에서는 접근할 수 없다.

`TRAMPOLINE`은 모든 process가 같은 physical page를 공유한다. `TRAPFRAME`은 같은 virtual address에
있지만 process마다 다른 physical page를 가리킨다.

## trampoline과 trapframe

`trampoline`은 page table을 바꾸는 중에도 계속 실행되어야 하는 작은 코드 영역이다. 그래서 user page
table과 kernel page table 양쪽에서 같은 virtual address에 매핑된다.

`trapframe`은 process별 저장 공간이다. trap entry에서는 user register를 여기에 저장하고, user로
돌아갈 때는 여기에서 register를 복원한다.

코드 확인 위치:
`kernel/memlayout.h`, `kernel/vm.c`, `kernel/proc.h`, `kernel/trampoline.S`
