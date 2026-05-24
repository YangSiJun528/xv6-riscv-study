# xv6 Kernel 5 코드 정리: kalloc 메모리 관리

## 읽는 순서

- `kernel/kalloc.c`: 물리 페이지 allocator 본체
- `kernel/memlayout.h`: 물리 메모리 범위
- `kernel/riscv.h`: 페이지 크기와 page alignment 매크로
- `kernel/kernel.ld`: 커널 이미지 끝 주소 `end`

## 관련 상수와 linker symbol

### `kernel/memlayout.h`

```c
// kernel은 0x80000000부터 물리 메모리에 올라간다.
#define KERNBASE 0x80000000L

// xv6가 사용하는 물리 메모리의 끝.
// 강의 기준으로 KERNBASE부터 128MB까지를 RAM으로 본다.
#define PHYSTOP  (KERNBASE + 128 * 1024 * 1024)
```

### `kernel/riscv.h`

```c
#define PGSIZE  4096 // page 하나의 크기, byte 단위
#define PGSHIFT 12   // page 안 offset bit 수. 2^12 = 4096

// sz를 다음 page 경계로 올림한다.
// kinit에서 커널 끝 주소가 page boundary가 아닐 수 있으므로 필요하다.
#define PGROUNDUP(sz)  (((sz) + PGSIZE - 1) & ~(PGSIZE - 1))

// a를 이전 page 경계로 내림한다.
#define PGROUNDDOWN(a) (((a)) & ~(PGSIZE - 1))
```

### `kernel/kernel.ld`

```ld
  .bss : {
    . = ALIGN(16);
    *(.sbss .sbss.*)
    . = ALIGN(16);
    *(.bss .bss.*)
  }

  /* end는 kernel image가 끝난 첫 주소다.
     kalloc.c는 이 주소부터 PHYSTOP까지를 free page 후보로 본다. */
  PROVIDE(end = .);
```

## `kernel/kalloc.c`: 전체 구조

```c
// user process, kernel stack, page-table page, pipe buffer 등에 쓰는
// 물리 메모리 allocator.
//
// xv6는 전체 4096바이트 page 단위로만 할당한다.

#include "types.h"
#include "param.h"
#include "memlayout.h"
#include "spinlock.h"
#include "riscv.h"
#include "defs.h"

void freerange(void *pa_start, void *pa_end);

extern char end[]; // kernel 바로 다음 첫 주소.
                   // kernel.ld가 정의한다.

// free list의 node다.
// 별도 metadata를 할당하지 않고, 비어 있는 page의 앞부분을 struct run으로 사용한다.
struct run {
  struct run *next;
};

struct {
  struct spinlock lock; // freelist를 여러 CPU가 동시에 건드리지 못하게 보호한다.
  struct run *freelist; // 사용 가능한 4KB page들의 linked list head
} kmem;
```

핵심 아이디어는 “빈 페이지 자체를 linked list node로 쓴다”는 것이다. 사용 중인 페이지에는 별도 관리
정보를 붙이지 않고, free 상태일 때만 그 페이지 앞부분을 `struct run`으로 해석한다.

## 초기화: `kinit`과 `freerange`

```c
void
kinit()
{
  // free list를 보호할 spinlock 초기화
  initlock(&kmem.lock, "kmem");

  // kernel image 끝부터 PHYSTOP까지의 물리 메모리를 free page로 등록한다.
  freerange(end, (void *)PHYSTOP);
}

void
freerange(void *pa_start, void *pa_end)
{
  char *p;

  // pa_start가 page 경계에 딱 맞지 않을 수 있으므로 다음 page 경계로 올린다.
  p = (char *)PGROUNDUP((uint64)pa_start);

  // page 하나씩 kfree()에 넘겨 free list에 넣는다.
  // p + PGSIZE <= pa_end 조건은 완전한 4KB page만 free하게 한다.
  for (; p + PGSIZE <= (char *)pa_end; p += PGSIZE)
    kfree(p);
}
```

초기화 시점에는 아직 free list가 비어 있다. `freerange()`가 사용 가능한 물리 메모리를 4KB씩 잘라
`kfree()`에 넣으면서 free list를 만든다.

## 해제: `kfree`

```c
// pa가 가리키는 물리 메모리 page를 해제한다.
//
// 보통은 kalloc()이 반환했던 page를 다시 넘겨야 한다.
// 예외는 allocator 초기화 때 kinit()이 아직 할당된 적 없는 page들을
// free list에 등록하는 경우다.
void
kfree(void *pa)
{
  struct run *r;

  // 잘못된 page를 free하면 free list가 망가지므로 강하게 검사한다.
  if (((uint64)pa % PGSIZE) != 0 || (char *)pa < end || (uint64)pa >= PHYSTOP)
    panic("kfree");

  // dangling reference를 빨리 잡기 위해 junk 값으로 채운다.
  // free한 page를 누군가 계속 사용하면 기존 데이터가 아니라 1로 채워진 값을 보게 된다.
  memset(pa, 1, PGSIZE);

  // 이 page는 이제 free 상태이므로, page 앞부분을 struct run node로 사용한다.
  r = (struct run *)pa;

  // free list는 공유 자료구조이므로 lock으로 보호한다.
  acquire(&kmem.lock);

  // list 앞에 push:
  //   r->next = old head
  //   head = r
  r->next = kmem.freelist;
  kmem.freelist = r;

  release(&kmem.lock);
}
```

`kfree()`는 page를 free list 맨 앞에 붙인다. 해제한 page를 값 1로 채우는 부분은 성능을 위한 것이
아니라 버그 탐지를 위한 장치다.

## 할당: `kalloc`

```c
// 물리 메모리 4096바이트 page 하나를 할당한다.
//
// 반환값:
//   성공: kernel이 사용할 수 있는 page pointer
//   실패: 0
void *
kalloc(void)
{
  struct run *r;

  // free list에서 첫 page를 하나 pop한다.
  acquire(&kmem.lock);
  r = kmem.freelist;
  if (r)
    kmem.freelist = r->next;
  release(&kmem.lock);

  // 새로 받은 page를 junk 값으로 채운다.
  // caller가 page 내용이 0이라고 잘못 가정하는 버그를 드러내기 쉽다.
  if (r)
    memset((char *)r, 5, PGSIZE);

  return (void *)r;
}
```

`kalloc()`은 free list가 비어 있으면 `0`을 반환한다. xv6의 allocator는 page 단위 allocator라서,
작은 객체 크기에 맞춰 쪼개 주는 기능은 없다.

## free list 동작 그림

```text
초기 free list:

kmem.freelist
    |
    v
  [page A] -> [page B] -> [page C] -> 0

kalloc() 한 번 뒤:

반환값 = page A

kmem.freelist
    |
    v
  [page B] -> [page C] -> 0

kfree(page A) 뒤:

kmem.freelist
    |
    v
  [page A] -> [page B] -> [page C] -> 0
```

## 코드에서 기억할 포인트

- xv6 커널 메모리 할당은 전부 4KB page 단위다.
- free page 자체를 linked list node로 사용한다.
- free list는 모든 CPU가 공유하므로 `kmem.lock`으로 보호한다.
- `kfree()`는 1, `kalloc()`은 5로 page를 채워 use-after-free나 초기화 가정 버그를 드러낸다.
- `end`부터 `PHYSTOP`까지가 초기 free memory 범위다.
