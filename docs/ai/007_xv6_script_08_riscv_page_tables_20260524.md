# xv6 Kernel 8 코드 정리: RISC-V Page Tables

## 읽는 순서

- `docs/reference/scripts/ko/08-xv6-kernel-8-riscv-page-tables-g7B0WS5Xu-A.md`
- `kernel/riscv.h`: Sv39, PTE bit, `satp`, `sfence.vma`
- `kernel/vm.c`: page table 생성, walk, mapping
- `kernel/memlayout.h`: kernel/device/trampoline 주소 배치

## 핵심

Page table은 virtual address를 physical address로 바꾸는 하드웨어 자료구조다. xv6는 RISC-V
Sv39 방식을 사용하고, 초기화 이후 supervisor/user mode에서는 주소 변환이 항상 켜져 있다고 보면 된다.

현재 hart가 사용할 page table root는 `satp` CSR이 가리킨다. `satp`가 0이면 주소 변환이 꺼져 있고,
커널 초기화 후에는 kernel page table 또는 process별 user page table을 가리킨다.

## page table이 여러 개인 이유

- kernel page table
  - 모든 CPU가 공유한다.
  - 대부분 physical address와 virtual address를 같게 직접 매핑한다.
  - RAM뿐 아니라 UART, virtio, PLIC 같은 memory-mapped device도 매핑한다.
- user page table
  - process마다 따로 있다.
  - 각 process에게 독립된 virtual address space를 제공한다.
  - `PTE_U`가 있는 page만 user mode에서 접근 가능하다.

즉 page table은 주소 변환뿐 아니라 process isolation과 접근 권한 제어도 담당한다.

## Sv39 구조

Sv39의 virtual address는 실질적으로 39bit를 사용한다.

```text
virtual address = level-2 index | level-1 index | level-0 index | page offset
                    9 bits        9 bits          9 bits          12 bits
```

page size는 4KB라서 offset이 12bit다. 나머지 27bit는 9bit씩 나뉘어 3단계 page table을
따라 내려가는 index로 쓰인다. 각 page-table page는 4KB이고, PTE 하나가 8byte라서 512개 entry를
담는다.

## 주소와 PTE 해석

### virtual address

Page table walk의 입력이다. `L2/L1/L0` index로 PTE를 찾고, offset은 최종 주소에 그대로 붙는다.

```text
63        39 38        30 29        21 20        12 11         0
+-----------+------------+------------+------------+------------+
| sign ext  | L2 index   | L1 index   | L0 index   | page offset|
| 25 bits   | 9 bits     | 9 bits     | 9 bits     | 12 bits    |
+-----------+------------+------------+------------+------------+
```

### page table entry(PTE)

Page table page 안의 64bit entry다. PPN은 다음 page table page나 최종 physical page를 가리키고,
flags는 접근 권한을 담는다.

```text
63        54 53                                             10 9          0
+-----------+-------------------------------------------------+------------+
| reserved  | PPN(Physical Page Number)                       | flags      |
| 10 bits   | 44 bits                                         | 10 bits    |
+-----------+-------------------------------------------------+------------+
```

### physical address

Page table walk의 결과다. PTE의 PPN과 virtual address의 offset을 합쳐 만든다.

```text
63        56 55        30 29        21 20        12 11         0
+-----------+-------------------------------------------------+------------+
| unused    | PPN(Physical Page Number)                       | page offset|
| 8 bits    | 44 bits                                         | 12 bits    |
+-----------+-------------------------------------------------+------------+
```

정리하면 `virtual address index -> PTE 선택 -> PTE의 PPN + 원래 offset -> physical address` 흐름이다.

## PTE가 담는 것

PTE(Page Table Entry)는 다음 두 종류의 정보를 담는다.

- 다음 level page table 또는 최종 physical page의 주소
- 접근 권한 bit
  - `V`: valid
  - `R`: read
  - `W`: write
  - `X`: execute
  - `U`: user mode 접근 허용

CPU는 load/store/instruction fetch 때 page table walk를 수행하고, leaf PTE의 권한 bit를 검사한다.
OS가 의도한 "코드", "데이터", "user memory" 같은 의미는 모르고 PTE bit만 본다.

## TLB와 `sfence.vma`

Page table walk는 메모리 접근을 여러 번 해야 하므로 느리다. 그래서 CPU는 최근 변환 결과를 TLB에
캐시한다.

문제는 page table이나 `satp`를 바꾼 뒤에도 TLB에 옛 변환이 남을 수 있다는 점이다. xv6는 page table
전환 전후에 `sfence.vma`를 실행해서 stale TLB entry를 버린다.

## xv6에서 중요하게 볼 점

- `kvmmake()`는 kernel page table을 만든다.
- `walk()`는 Sv39의 3단계 page table을 따라 내려간다.
- `mappages()`는 virtual page와 physical page의 mapping을 추가한다.
- `walkaddr()`는 user pointer가 실제 user page인지 확인할 때 쓰인다.
- trampoline은 user page table과 kernel page table 양쪽에서 같은 virtual address에 매핑되어야 한다.

코드 확인 위치:
`kernel/riscv.h`, `kernel/vm.c`, `kernel/memlayout.h`
