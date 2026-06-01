# xv6 Kernel 20 코드 정리: VM functions code walkthrough

## 읽는 순서

- `docs/reference/scripts/ko/20-xv6-kernel-20-vm-functions-code-walkthru-BB0Tbyu3XMQ.md`
- `kernel/vm.c`
- `kernel/riscv.h`
- `kernel/proc.c`
- `kernel/exec.c`

## 먼저 볼 구조

20강은 19강에서 본 VM helper들을 코드 기준으로 따라간다. 핵심은 "PTE 주소를 찾는 함수"와 "그 PTE를 채우거나 지우는 함수"를
구분하는 것이다.

```text
walk()
  page table tree를 따라 내려가 leaf PTE의 주소를 찾는다.

mappages()
  walk()로 찾은 leaf PTE에 PA와 permission을 써 넣는다.

uvmunmap()
  leaf PTE를 0으로 만들고, 선택적으로 data page를 kfree()한다.

freewalk()
  leaf mapping이 모두 제거된 page table tree의 index page들을 kfree()한다.
```

## `walk()`: leaf PTE 주소 찾기

```c++
for (int level = 2; level > 0; level--) {
  pte_t *pte = &pagetable[PX(level, va)];
  if (*pte & PTE_V) {
    pagetable = (pagetable_t)PTE2PA(*pte);
  } else {
    if (!alloc || (pagetable = (pde_t *)kalloc()) == 0)
      return 0;
    memset(pagetable, 0, PGSIZE);
    *pte = PA2PTE(pagetable) | PTE_V;
  }
}
return &pagetable[PX(0, va)];
```

읽는 법:

- level 2, level 1까지만 내려간다.
- 중간 PTE가 valid면 그 PTE가 가리키는 다음 page table page로 이동한다.
- 없고 `alloc`이 참이면 새 page table page를 만든다.
- 마지막에는 level 0 page table 안의 PTE 주소를 반환한다.
- 반환된 PTE가 valid인지, 어떤 PA를 가리킬지는 caller가 결정한다.

## `mappages()`: PTE 채우기

`mappages()`는 물리 page의 내용을 바꾸는 함수가 아니다. page table 안의 leaf PTE를 채워서 주소 변환 규칙을
추가하는 함수다.

```c++
if ((va % PGSIZE) != 0)
  panic("mappages: va not aligned");
if ((size % PGSIZE) != 0)
  panic("mappages: size not aligned");

a = va;                         // 이번에 mapping할 virtual page
last = va + size - PGSIZE;      // 마지막 virtual page
for (;;) {
  // a에 해당하는 leaf PTE의 주소를 찾는다.
  // alloc=1이라 중간 page table page가 없으면 새로 만든다.
  if ((pte = walk(pagetable, a, 1)) == 0)
    return -1;

  // 이미 valid mapping이 있으면 같은 VA를 두 번 매핑하려는 것이다.
  if (*pte & PTE_V)
    panic("mappages: remap");

  // 이 한 줄이 실제 mapping 생성이다.
  // leaf PTE에 physical page number, permission, valid bit를 넣는다.
  *pte = PA2PTE(pa) | perm | PTE_V;

  if (a == last)
    break;

  // 다음 virtual page는 다음 physical page를 가리키게 한다.
  a += PGSIZE;
  pa += PGSIZE;
}
```

현재 repo는 `va`와 `size`가 page-aligned라고 강하게 요구한다. `walk(..., 1)`이므로 필요한 page table page는
만든다. leaf PTE가 이미 valid면 같은 VA를 두 번 매핑하는 것이므로 panic한다.

예를 들어 `size == 2 * PGSIZE`이면 loop는 두 번 돈다.

```text
1회차:
  a  = va
  pa = pa
  PTE for a = PA2PTE(pa) | perm | PTE_V

2회차:
  a  = va + PGSIZE
  pa = pa + PGSIZE
  PTE for a = PA2PTE(pa) | perm | PTE_V
```

즉 virtual page와 physical page를 page 단위로 나란히 연결한다.

```text
VA page 0  -> PA page 0
VA page 1  -> PA page 1
VA page 2  -> PA page 2
```

이 mapping이 만들어진 뒤에야 CPU가 해당 VA에 접근할 때 PA로 번역할 수 있다.

## `mappages()`가 실제로 쓰이는 곳

`mappages()`는 "새 주소 공간에 이 page를 붙인다"가 필요한 곳에서 쓰인다.

Kernel page table direct map:

```c++
kvmmap(kpgtbl, UART0, UART0, PGSIZE, PTE_R | PTE_W);
kvmmap(kpgtbl, KERNBASE, KERNBASE, (uint64)etext - KERNBASE, PTE_R | PTE_X);
```

여기서는 VA와 PA 숫자를 같게 둔다. paging이 켜져도 kernel이 `UART0`나 `KERNBASE` 주소를 그대로 쓸 수 있게
하기 위한 mapping이다.

Process마다 필요한 trampoline/trapframe:

```c++
mappages(pagetable, TRAMPOLINE, PGSIZE, (uint64)trampoline, PTE_R | PTE_X);
mappages(pagetable, TRAPFRAME, PGSIZE, (uint64)(p->trapframe), PTE_R | PTE_W);
```

`TRAMPOLINE`은 모든 process가 같은 trampoline code physical page를 가리킨다. `TRAPFRAME`은 같은 VA지만
process마다 다른 `p->trapframe` physical page를 가리킨다.

User memory allocation:

```c++
mem = kalloc();
memset(mem, 0, PGSIZE);
mappages(pagetable, a, PGSIZE, (uint64)mem, PTE_R | PTE_U | xperm);
```

여기서는 새 physical page를 user virtual address `a`에 붙인다. `memset()`은 실제 page 내용을 초기화하는 일이고,
`mappages()`는 그 page를 user VA로 접근 가능하게 만드는 일이다. 둘은 다른 작업이다.

## `kvmmake()`: kernel page table 만들기

```c++
kvmmap(kpgtbl, UART0, UART0, PGSIZE, PTE_R | PTE_W);
kvmmap(kpgtbl, VIRTIO0, VIRTIO0, PGSIZE, PTE_R | PTE_W);
kvmmap(kpgtbl, PLIC, PLIC, 0x4000000, PTE_R | PTE_W);

kvmmap(kpgtbl, KERNBASE, KERNBASE, (uint64)etext - KERNBASE, PTE_R | PTE_X);
kvmmap(kpgtbl, (uint64)etext, (uint64)etext, PHYSTOP - (uint64)etext,
       PTE_R | PTE_W);

kvmmap(kpgtbl, TRAMPOLINE, (uint64)trampoline, PGSIZE, PTE_R | PTE_X);
proc_mapstacks(kpgtbl);
```

kernel page table은 대부분 direct map이다. 예외적으로 `TRAMPOLINE`과 kernel stack 영역은 높은 VA에 따로 잡힌다.
`proc_mapstacks()`는 process마다 kernel stack page를 하나씩 할당하고, stack 사이에는 invalid guard page를 남긴다.

`kvminithart()`는 이 page table을 hardware에 설치한다.

```c++
sfence_vma();
w_satp(MAKE_SATP(kernel_pagetable));
sfence_vma();
```

`satp`를 바꾸면 page table 기준이 바뀐다. `sfence_vma()`는 이전 TLB entry를 정리한다.

## `uvmalloc()` / `uvmdealloc()`: user memory 크기 변경

```c++
oldsz = PGROUNDUP(oldsz);
for (a = oldsz; a < newsz; a += PGSIZE) {
  mem = kalloc();
  if (mem == 0) {
    uvmdealloc(pagetable, a, oldsz);
    return 0;
  }
  memset(mem, 0, PGSIZE);
  if (mappages(pagetable, a, PGSIZE, (uint64)mem,
               PTE_R | PTE_U | xperm) != 0) {
    kfree(mem);
    uvmdealloc(pagetable, a, oldsz);
    return 0;
  }
}
return newsz;
```

`oldsz`와 `newsz`는 byte 단위 size라 page boundary가 아닐 수 있다. 그래서 실제 page allocation 시작점은
`PGROUNDUP(oldsz)`다. 실패하면 지금까지 할당한 page들을 `uvmdealloc()`으로 되돌린다.

`uvmdealloc()`은 줄어드는 경우에만 page를 제거한다.

```c++
if (PGROUNDUP(newsz) < PGROUNDUP(oldsz)) {
  int npages = (PGROUNDUP(oldsz) - PGROUNDUP(newsz)) / PGSIZE;
  uvmunmap(pagetable, PGROUNDUP(newsz), npages, 1);
}
```

## `uvmunmap()`과 `freewalk()`: data page와 page-table page 구분

`uvmunmap()`은 leaf mapping을 제거한다. `do_free`가 참이면 leaf가 가리키는 physical data page도 해제한다.

```c++
if ((pte = walk(pagetable, a, 0)) == 0)
  continue;
if ((*pte & PTE_V) == 0)
  continue;
if (do_free) {
  uint64 pa = PTE2PA(*pte);
  kfree((void *)pa);
}
*pte = 0;
```

현재 repo는 mapping이 없어도 `continue`한다. lazy allocation 때문에 address range 안에 아직 실제 page가 없을 수 있기 때문이다.

`freewalk()`는 page table page만 해제한다. leaf mapping이 남아 있으면 panic한다.

```c++
if ((pte & PTE_V) && (pte & (PTE_R | PTE_W | PTE_X)) == 0) {
  uint64 child = PTE2PA(pte);
  freewalk((pagetable_t)child);
  pagetable[i] = 0;
} else if (pte & PTE_V) {
  panic("freewalk: leaf");
}
```

따라서 순서는 중요하다.

```text
1. uvmunmap(): data page leaf mapping 제거
2. freewalk(): 비어 있는 page table tree 제거
```

## `uvmcopy()`: fork의 주소 공간 복사

```c++
for (i = 0; i < sz; i += PGSIZE) {
  if ((pte = walk(old, i, 0)) == 0)
    continue;
  if ((*pte & PTE_V) == 0)
    continue;
  pa = PTE2PA(*pte);
  flags = PTE_FLAGS(*pte);
  if ((mem = kalloc()) == 0)
    goto err;
  memmove(mem, (char *)pa, PGSIZE);
  if (mappages(new, i, PGSIZE, (uint64)mem, flags) != 0) {
    kfree(mem);
    goto err;
  }
}
```

부모의 각 mapped page마다 새 physical page를 할당하고 내용을 복사한다. permission bit도 그대로 복사한다.
현재 repo는 missing/invalid PTE를 건너뛴다. 이것도 lazy allocation과 맞물린 동작이다.

## `uvmclear()`: guard page 만들기

```c++
pte = walk(pagetable, va, 0);
if (pte == 0)
  panic("uvmclear");
*pte &= ~PTE_U;
```

`exec()`는 user stack 아래 page를 guard page로 두고 `PTE_U`를 지운다. page는 매핑되어 있지만 user mode에서는
접근할 수 없다. stack이 아래로 넘치면 user trap이 난다.

## `copyin()` / `copyout()` / `copyinstr()`

이 함수들은 user VA가 여러 physical page에 흩어져 있을 수 있다는 사실을 처리한다.

```c++
va0 = PGROUNDDOWN(srcva);
pa0 = walkaddr(pagetable, va0);
n = PGSIZE - (srcva - va0);
if (n > len)
  n = len;
memmove(dst, (void *)(pa0 + (srcva - va0)), n);
```

한 loop는 한 page 안에서 가능한 chunk만 복사한다. 그 다음 `srcva = va0 + PGSIZE`로 다음 page로 넘어간다.

방향만 다르다.

```text
copyin:     user VA -> kernel buffer
copyout:    kernel buffer -> user VA
copyinstr:  user VA -> kernel buffer, '\0'을 만나면 stop
```

현재 repo의 `copyin()`과 `copyout()`은 `walkaddr()`가 실패하면 `vmfault()`를 호출해 lazy page를 실제로
할당할 수 있다. `copyinstr()`는 이 fallback 없이 실패한다.

`copyout()`에는 read-only user text page에 쓰는 것을 막는 검사도 있다.

```c++
pte = walk(pagetable, va0, 0);
if ((*pte & PTE_W) == 0)
  return -1;
```

## 현재 repo에서 추가된 lazy allocation

강의 원형보다 현재 repo는 lazy `sbrk()` 실험 코드가 더 들어가 있다.

```text
sys_sbrk(SBRK_LAZY):
  p->sz만 증가

user가 그 VA를 실제 접근:
  trap.c 또는 copyin/copyout
  -> vmfault()
  -> kalloc()
  -> mappages(..., PTE_W | PTE_U | PTE_R)
```

`vmfault()`는 `va >= p->sz`이면 실패하고, 이미 매핑된 page도 실패 처리한다. 새로 할당하는 page는 zero-fill한다.

## 한 줄 정리

```text
walk는 PTE의 위치를 찾고, mappages/uvmunmap은 leaf mapping을 만들고 지운다.
uvmalloc/uvmdealloc/uvmcopy는 이 primitive들을 묶어 process address space 단위 작업을 만든다.
copyin/copyout/copyinstr은 user VA의 page 경계를 넘나들며 kernel buffer와 안전하게 복사한다.
```

코드 확인 위치:
`kernel/vm.c`, `kernel/riscv.h`, `kernel/proc.c`, `kernel/exec.c`, `kernel/sysproc.c`, `kernel/trap.c`
