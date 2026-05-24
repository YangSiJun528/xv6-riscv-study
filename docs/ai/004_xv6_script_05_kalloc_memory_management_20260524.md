# xv6 Kernel 5 코드 정리: kalloc 메모리 관리

## 읽는 순서

- `kernel/memlayout.h`, `kernel/riscv.h`, `kernel/kernel.ld`: 범위와 page 단위
- `kernel/kalloc.c`: physical page allocator

## 범위와 단위

```c++
// kernel/memlayout.h
#define KERNBASE 0x80000000L
#define PHYSTOP  (KERNBASE + 128 * 1024 * 1024)

// kernel/riscv.h
#define PGSIZE  4096
#define PGSHIFT 12
#define PGROUNDUP(sz)  (((sz) + PGSIZE - 1) & ~(PGSIZE - 1))
#define PGROUNDDOWN(a) (((a)) & ~(PGSIZE - 1))
```

```ld
/* kernel/kernel.ld */
PROVIDE(end = .); /* kernel image가 끝난 첫 주소 */
```

allocator는 `end`부터 `PHYSTOP` 전까지의 물리 메모리를 4KB page 단위로 관리한다.

## 전체 구조

```c++
extern char end[]; // kernel.ld가 정의한다.

// free page 자체를 linked-list node로 쓴다.
struct run {
  struct run *next;
};

struct {
  struct spinlock lock; // freelist 보호
  struct run *freelist; // free page list head
} kmem;
```

빈 page의 앞부분을 `struct run`으로 해석한다. 그래서 별도 metadata page가 필요 없다.

## 초기화

```c++
void
kinit()
{
  initlock(&kmem.lock, "kmem");
  freerange(end, (void *)PHYSTOP);
}

void
freerange(void *pa_start, void *pa_end)
{
  char *p;

  // kernel 끝 주소가 page boundary가 아닐 수 있으므로 올림한다.
  p = (char *)PGROUNDUP((uint64)pa_start);

  // 완전한 4KB page만 free list에 등록한다.
  for (; p + PGSIZE <= (char *)pa_end; p += PGSIZE)
    kfree(p);
}
```

## 해제

```c++
void
kfree(void *pa)
{
  struct run *r;

  // page 정렬, kernel 영역 침범, PHYSTOP 초과를 막는다.
  if (((uint64)pa % PGSIZE) != 0 || (char *)pa < end || (uint64)pa >= PHYSTOP)
    panic("kfree");

  // use-after-free를 빨리 드러내기 위한 junk pattern.
  memset(pa, 1, PGSIZE);

  r = (struct run *)pa;

  acquire(&kmem.lock);
  r->next = kmem.freelist;
  kmem.freelist = r;
  release(&kmem.lock);
}
```

## 할당

```c++
void *
kalloc(void)
{
  struct run *r;

  acquire(&kmem.lock);
  r = kmem.freelist;
  if (r)
    kmem.freelist = r->next;
  release(&kmem.lock);

  // caller가 0으로 초기화되어 있다고 착각하는 버그를 드러낸다.
  if (r)
    memset((char *)r, 5, PGSIZE);

  return (void *)r;
}
```

free list가 비면 `0`을 반환한다. xv6의 `kalloc()`은 작은 객체 allocator가 아니라 4KB page allocator다.

## free list

```text
before:

kmem.freelist -> [page A] -> [page B] -> [page C] -> 0

after kalloc():

return page A
kmem.freelist -> [page B] -> [page C] -> 0

after kfree(page A):

kmem.freelist -> [page A] -> [page B] -> [page C] -> 0
```

핵심은 `free page 자체를 list node로 사용`, `kmem.lock으로 공유 list 보호`, `end..PHYSTOP만 관리`다.
