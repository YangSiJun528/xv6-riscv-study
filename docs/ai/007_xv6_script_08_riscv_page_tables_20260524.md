# xv6 Kernel 8 코드 정리: RISC-V Page Tables

## 읽는 순서

- `docs/reference/scripts/ko/08-xv6-kernel-8-riscv-page-tables-g7B0WS5Xu-A.md`
- `kernel/riscv.h`: Sv39, PTE bit, 주소 index macro
- `kernel/vm.c`: kernel page table, walk, mappages
- `kernel/memlayout.h`: physical/device address와 trampoline 위치

## Sv39 주소 구조

```text
virtual address:

  38              30 29              21 20              12 11          0
 +------------------+------------------+------------------+-------------+
 | level-2 index    | level-1 index    | level-0 index    | page offset |
 | 9 bits           | 9 bits           | 9 bits           | 12 bits     |
 +------------------+------------------+------------------+-------------+

page size = 4096 bytes
PTE count per page-table page = 512
```

```c++
// kernel/riscv.h
#define PGSIZE  4096
#define PGSHIFT 12

#define PXMASK         0x1FF
#define PXSHIFT(level) (PGSHIFT + (9 * (level)))
#define PX(level, va)  ((((uint64)(va)) >> PXSHIFT(level)) & PXMASK)
```

## PTE format

```c++
#define PTE_V (1L << 0) // valid
#define PTE_R (1L << 1) // readable
#define PTE_W (1L << 2) // writable
#define PTE_X (1L << 3) // executable
#define PTE_U (1L << 4) // user mode 접근 가능

#define PA2PTE(pa) ((((uint64)pa) >> 12) << 10)
#define PTE2PA(pte) (((pte) >> 10) << 12)
#define PTE_FLAGS(pte) ((pte) & 0x3FF)
```

CPU는 load/store/fetch 때 page table을 따라가고, leaf PTE의 `V/R/W/X/U` bit로 접근 가능 여부를
검사한다. OS별 메모리 의미는 모르고, PTE bit만 본다.

## `satp`와 TLB

```c++
#define SATP_SV39 (8L << 60)
#define MAKE_SATP(pagetable) (SATP_SV39 | (((uint64)pagetable) >> 12))

void
kvminithart()
{
  sfence_vma();                    // 이전 page table write 정리
  w_satp(MAKE_SATP(kernel_pagetable)); // 현재 hart의 page table 선택
  sfence_vma();                    // stale TLB 제거
}
```

`satp`가 현재 hart의 page table root를 가리킨다. `satp`를 바꾸면 TLB에 남은 옛 변환을 버리기 위해
`sfence.vma`가 필요하다.

## kernel page table

```c++
pagetable_t
kvmmake(void)
{
  pagetable_t kpgtbl = (pagetable_t)kalloc();
  memset(kpgtbl, 0, PGSIZE);

  // MMIO device 직접 매핑
  kvmmap(kpgtbl, UART0, UART0, PGSIZE, PTE_R | PTE_W);
  kvmmap(kpgtbl, VIRTIO0, VIRTIO0, PGSIZE, PTE_R | PTE_W);
  kvmmap(kpgtbl, PLIC, PLIC, 0x4000000, PTE_R | PTE_W);

  // kernel text는 read/execute, data/RAM은 read/write
  kvmmap(kpgtbl, KERNBASE, KERNBASE, (uint64)etext - KERNBASE, PTE_R | PTE_X);
  kvmmap(kpgtbl, (uint64)etext, (uint64)etext, PHYSTOP - (uint64)etext,
         PTE_R | PTE_W);

  // trap entry/return 코드
  kvmmap(kpgtbl, TRAMPOLINE, (uint64)trampoline, PGSIZE, PTE_R | PTE_X);

  proc_mapstacks(kpgtbl);
  return kpgtbl;
}
```

kernel page table은 모든 CPU가 공유한다. 대부분 physical address와 같은 virtual address로 직접
매핑한다.

## page table walk

```c++
pte_t *
walk(pagetable_t pagetable, uint64 va, int alloc)
{
  if (va >= MAXVA)
    panic("walk");

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
}
```

`walk()`는 level 2 -> 1 -> 0 순서로 내려간다. `alloc`이 true이면 빠진 중간 page-table page를
새로 만든다.

## mapping 추가와 user 확인

```c++
int
mappages(pagetable_t pagetable, uint64 va, uint64 size, uint64 pa, int perm)
{
  uint64 a = va;
  uint64 last = va + size - PGSIZE;

  for (;;) {
    pte_t *pte = walk(pagetable, a, 1);
    if (*pte & PTE_V)
      panic("mappages: remap");
    *pte = PA2PTE(pa) | perm | PTE_V;

    if (a == last)
      break;
    a += PGSIZE;
    pa += PGSIZE;
  }
}

uint64
walkaddr(pagetable_t pagetable, uint64 va)
{
  pte_t *pte = walk(pagetable, va, 0);
  if (pte == 0 || (*pte & PTE_V) == 0 || (*pte & PTE_U) == 0)
    return 0;
  return PTE2PA(*pte);
}
```

`walkaddr()`는 user page만 물리 주소로 바꾼다. `PTE_U`가 없으면 user pointer로 인정하지 않는다.
