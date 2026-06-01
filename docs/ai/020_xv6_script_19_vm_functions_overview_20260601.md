# xv6 Kernel 19 코드 정리: VM functions overview

## 읽는 순서

- `docs/reference/scripts/ko/19-xv6-kernel-19-vm-functions-overview-rhq08Lr-Yo4.md`
- `kernel/vm.c`: page table helper 함수들
- `kernel/riscv.h`: Sv39 주소/PTE bit 매크로
- `kernel/proc.c`: process page table 생성/해제, fork, grow
- `kernel/exec.c`: ELF load, user stack, guard page
- `kernel/sysproc.c`: `sbrk()`와 lazy allocation

## 핵심

19강은 `vm.c` 함수들을 "무엇을 하는가" 기준으로 훑는 강의다. 이 함수들은 대부분 page table 트리를 조작하는
작은 helper이고, 직접 lock을 잡거나 sleep하지 않는다.

큰 분류는 네 가지다.

```text
page table 탐색/매핑:
  walk, walkaddr, mappages, uvmunmap, freewalk

kernel page table:
  kvmmake, kvmmap, kvminit, kvminithart

user address space 생명주기:
  uvmcreate, uvmalloc, uvmdealloc, uvmfree, uvmcopy, uvmclear

kernel <-> user copy:
  copyin, copyout, copyinstr
```

## Sv39 page table 기본

xv6는 RISC-V Sv39 page table을 쓴다. 하나의 page table page에는 512개의 64-bit PTE가 있고,
virtual address는 세 개의 9-bit index와 12-bit page offset으로 나뉜다.

```text
VA:
  level-2 index | level-1 index | level-0 index | page offset
       9 bits   |      9 bits   |      9 bits   |   12 bits
```

`kernel/riscv.h`의 핵심 매크로:

```c++
#define PGSIZE  4096
#define PTE_V   (1L << 0)
#define PTE_R   (1L << 1)
#define PTE_W   (1L << 2)
#define PTE_X   (1L << 3)
#define PTE_U   (1L << 4)

#define PA2PTE(pa) ((((uint64)pa) >> 12) << 10)
#define PTE2PA(pte) (((pte) >> 10) << 12)
#define PTE_FLAGS(pte) ((pte) & 0x3FF)
#define PX(level, va) ((((uint64)(va)) >> PXSHIFT(level)) & PXMASK)
```

상위 level의 PTE는 다음 page table page를 가리킨다. 이때 보통 `PTE_V`만 켜져 있고 `R/W/X`는 꺼져 있다.
leaf PTE는 실제 data page를 가리키고 `R/W/X/U` 같은 권한 bit가 의미를 가진다.

## Mapping이 왜 필요한가

물리 page는 실제 byte가 저장되는 공간이고, 가상 주소는 CPU와 프로그램이 사용하는 주소 이름이다. 둘은 자동으로
연결되지 않는다. page table에 "이 VA는 저 PA를 가리킨다"는 PTE가 있어야 CPU가 virtual address를 physical
address로 번역할 수 있다.

```text
physical page:
  실제 데이터가 저장되는 4KB 메모리 조각

virtual page:
  process/kernel이 load/store/fetch에 사용하는 4KB 주소 구간

PTE:
  virtual page -> physical page 번역 정보 + 권한 bit
```

따라서 "물리 영역을 수정하면 가상 영역이 된다"가 아니다. 물리 page에 byte를 써도, 어떤 page table이 그 page를
가리키는 PTE를 갖고 있지 않으면 해당 virtual address로는 접근할 수 없다.

예를 들어 `kalloc()`이 물리 page 하나를 줬다고 하자.

```text
kalloc() -> PA 0x80012000
```

이 page를 user virtual address `0x4000`에서 쓰게 하려면 page table에 mapping을 추가해야 한다.

```text
before:
  user VA 0x4000 -> no valid PTE -> 접근하면 page fault

mappages(pagetable, 0x4000, PGSIZE, 0x80012000, PTE_R | PTE_W | PTE_U)

after:
  user VA 0x4000 -> PA 0x80012000, R/W/User 허용
```

그 뒤 user code가 `0x4000`에 write하면 CPU가 page table을 보고 실제로는 `0x80012000`에 write한다.
`mappages()`는 data page 내용을 복사하지 않는다. 연결표인 PTE를 만드는 함수다.

이 구분이 필요한 이유는 같은 물리 page를 다른 virtual address에 매핑할 수도 있고, 같은 physical address라도
page table마다 다르게 보이게 만들 수도 있기 때문이다. xv6의 `TRAMPOLINE`이 대표 예다. 같은 trampoline code
physical page가 kernel page table과 각 user page table의 높은 virtual address에 매핑된다.

## 탐색과 매핑

`walk(pagetable, va, alloc)`은 page table tree를 따라 내려가서 level-0 PTE의 주소를 반환한다.

- `alloc == 0`: 중간 page table page가 없으면 실패.
- `alloc != 0`: 필요한 중간 page table page를 `kalloc()`으로 만들고 0으로 초기화.
- 반환값은 data page가 아니라 "data page를 가리킬 PTE의 주소"다.

`mappages(pagetable, va, size, pa, perm)`은 `va..va+size` 범위의 virtual page들이 `pa`부터 시작하는
physical page들을 가리키도록 PTE를 채운다.

`size`가 여러 page이면 `va`와 `pa`를 같이 4KB씩 증가시키며 여러 mapping을 만든다.

```text
mappages(pagetable, 0x4000, 2*PGSIZE, 0x80012000, perm)

creates:
  VA 0x4000 -> PA 0x80012000
  VA 0x5000 -> PA 0x80013000
```

현재 repo에서는 보통 `uvmalloc()`처럼 `kalloc()`으로 page 하나를 얻고 `PGSIZE`만큼 매핑하는 형태가 많다.
kernel direct map처럼 연속된 큰 범위를 매핑할 때는 `size`가 여러 page가 된다.

현재 repo 기준 주의점:

- `va`와 `size`는 page-aligned여야 한다. 아니면 panic.
- 이미 valid인 PTE에 다시 mapping하려 하면 panic.
- 새 PTE는 `PA2PTE(pa) | perm | PTE_V`가 된다.

`walkaddr(pagetable, va)`는 user virtual address가 실제로 user 접근 가능한 page인지 확인하고 physical
address를 반환한다. `PTE_V`와 `PTE_U`를 검사한다. 실패하면 0을 반환한다.

`walk()`와 `mappages()`를 구분해서 읽으면 편하다.

```text
walk():
  PTE가 들어갈 "칸의 주소"를 찾는다.

mappages():
  그 칸에 PA와 permission을 써서 valid mapping으로 만든다.

walkaddr():
  이미 만들어진 mapping을 따라가 user VA가 가리키는 PA를 얻는다.
```

## Kernel page table

kernel page table은 모든 CPU가 공유한다. `kvmmake()`가 만든다.

```text
UART0, VIRTIO0, PLIC       direct map, RW
kernel text                direct map, R-X
kernel data + free memory  direct map, RW
TRAMPOLINE                 high VA -> trampoline PA, R-X
process kernel stacks      high VA, guard page 사이에 배치
```

`kvmmap()`은 `mappages()` wrapper다. kernel page table 초기화 중 실패는 복구 대상이 아니므로 `panic()`한다.

여기서 UART, VIRTIO, PLIC도 mapping이 필요하다. MMIO 주소도 CPU 입장에서는 physical address space에 있는
주소이므로, paging이 켜진 뒤 kernel VA로 접근하려면 kernel page table에 PTE가 있어야 한다. xv6는 이 장치들을
VA와 PA 숫자가 같게 direct map한다.

`kvminit()`은 전역 `kernel_pagetable`을 만들고, `kvminithart()`는 `satp`에 이 page table을 설치한다.
`sfence_vma()`는 stale TLB entry를 비우기 위해 쓰인다.

## User address space

`uvmcreate()`는 빈 user page table root page를 만든다. 이 자체는 user memory를 하나도 매핑하지 않는다.
`proc_pagetable()`이 이 빈 page table 위에 `TRAMPOLINE`, `TRAPFRAME` mapping을 추가한다.

`uvmalloc(pagetable, oldsz, newsz, xperm)`은 `oldsz`에서 `newsz`까지 필요한 page를 할당하고 매핑한다.

- `oldsz`와 `newsz`는 page-aligned일 필요가 없다.
- `oldsz`를 `PGROUNDUP(oldsz)`로 올린 뒤 page 단위로 할당한다.
- 새 page는 0으로 초기화한다.
- 현재 repo는 permission을 `PTE_R | PTE_U | xperm`로 만든다.

이때 실제 순서는 `kalloc()`으로 physical page를 확보하고, `mappages()`로 user VA에 붙이는 것이다.

```text
1. kalloc() -> 새 physical page
2. memset(page, 0, PGSIZE) -> 이전 사용자의 데이터가 보이지 않게 초기화
3. mappages() -> user VA가 이 physical page를 가리키도록 PTE 생성
```

`uvmdealloc()`은 반대로 user address space를 줄인다. page boundary 차이가 있을 때만 `uvmunmap(..., do_free=1)`로
data page를 해제한다.

`uvmfree()`는 size 아래의 user page들을 먼저 해제하고, 그 뒤 `freewalk()`로 page table page들을 해제한다.
`TRAMPOLINE`과 `TRAPFRAME`은 `proc_freepagetable()`에서 먼저 unmap된다. 실제 trampoline page는 공유 code이고,
trapframe page는 `freeproc()`에서 따로 해제된다.

`uvmcopy()`는 `fork()`에서 부모 user memory를 자식 page table로 복사한다. 현재 repo는 lazy allocation을 고려해서
없는 PTE나 invalid PTE는 `continue`한다.

`uvmclear()`는 leaf PTE의 `PTE_U`를 지운다. `exec()`가 user stack 아래 guard page를 user mode 접근 불가로
만들 때 사용한다.

## Kernel과 user 사이 복사

kernel은 user pointer를 직접 신뢰할 수 없다. 그래서 system call 인자나 buffer를 다룰 때 `copyin`,
`copyout`, `copyinstr`를 쓴다.

- `copyin`: user VA 범위에서 kernel buffer로 복사
- `copyout`: kernel buffer에서 user VA 범위로 복사
- `copyinstr`: user VA의 null-terminated string을 kernel buffer로 복사

핵심은 user virtual memory에서는 연속이어도 physical memory에서는 page마다 흩어질 수 있다는 점이다. 그래서 이 함수들은
page boundary마다 `walkaddr()`로 physical page를 다시 찾고, 한 page 안에서 가능한 chunk만 복사한다.

## 강의 설명과 현재 repo의 차이

- 강의는 `uvminit()`과 initcode 복사를 설명하지만, 현재 repo에는 `uvminit()`이 없다. `userinit()`은 빈 process를 만들고,
  `forkret()`에서 `kexec("/init", ...)`로 `/init`을 실행한다.
- 강의의 `uvmalloc()` 설명은 새 page를 `R/W/X/U`로 만든다고 말하지만, 현재 repo는 `xperm` 인자로 execute/write 권한을
  조절한다.
- 현재 repo는 lazy `sbrk()`가 있다. `sys_sbrk(..., SBRK_LAZY)`는 `p->sz`만 늘리고 실제 page는
  `vmfault()`나 `copyin`/`copyout` 경로에서 필요할 때 할당된다.
- 현재 `uvmunmap()`과 `uvmcopy()`는 lazy allocation 때문에 없는 mapping을 panic하지 않고 건너뛸 수 있다.

## 한 줄 정리

```text
vm.c 함수들은 page table tree를 만들고, 걷고, leaf mapping을 추가/삭제하고,
user address space와 kernel buffer 사이의 복사를 page 단위로 안전하게 수행하는 helper들이다.
```

코드 확인 위치:
`kernel/vm.c`, `kernel/riscv.h`, `kernel/proc.c`, `kernel/exec.c`, `kernel/sysproc.c`
