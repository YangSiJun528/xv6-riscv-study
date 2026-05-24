# xv6 Kernel 4 코드 정리: Spinlocks

## 읽는 순서

- `kernel/spinlock.h`: lock 구조
- `kernel/proc.h`, `kernel/proc.c`: CPU별 interrupt nesting 상태
- `kernel/spinlock.c`: `initlock`, `acquire`, `release`, `push_off`, `pop_off`

## 자료구조

```c++
// kernel/spinlock.h
struct spinlock {
  uint locked;     // 0이면 unlocked, 1이면 locked
  char *name;      // debugging용 이름
  struct cpu *cpu; // 현재 lock을 들고 있는 CPU
};

// kernel/proc.h
struct cpu {
  struct proc *proc;      // 현재 실행 중인 process
  struct context context; // scheduler context

  int noff;               // push_off() 중첩 깊이
  int intena;             // 첫 push_off() 전 interrupt 상태
};
```

`locked`가 실제 상호 배제 상태다. `name`, `cpu`, `noff`, `intena`는 버그 검사와 interrupt 복구를
위한 보조 상태다.

## 현재 CPU 찾기

```c++
// kernel/proc.c
int
cpuid()
{
  int id = r_tp(); // start.c가 tp에 hart id를 저장한다.
  return id;
}

struct cpu *
mycpu(void)
{
  int id = cpuid();
  return &cpus[id];
}
```

`mycpu()`는 interrupt가 꺼진 상태에서 안전하다. 그래서 `acquire()`는 먼저 `push_off()`를 호출한다.

## 초기화

```c++
void
initlock(struct spinlock *lk, char *name)
{
  lk->name = name;
  lk->locked = 0;
  lk->cpu = 0;
}
```

## 획득

```c++
void
acquire(struct spinlock *lk)
{
  // interrupt handler가 같은 CPU에서 같은 lock을 다시 잡는 일을 막는다.
  push_off();

  if (holding(lk))
    panic("acquire");

  // atomic swap:
  //   이전 값이 0이면 내가 lock 획득
  //   이전 값이 1이면 누군가 들고 있으므로 반복
  //
  // acquire ordering:
  //   critical section 안의 load/store가 lock 획득 전으로 올라가지 못하게 한다.
  while (__atomic_exchange_n(&lk->locked, 1, __ATOMIC_ACQUIRE) != 0)
    ;

  lk->cpu = mycpu();
}
```

단순한 `if (locked == 0) locked = 1`은 두 CPU가 동시에 통과할 수 있다. 그래서 atomic exchange가
필요하다.

## 해제

```c++
void
release(struct spinlock *lk)
{
  if (!holding(lk))
    panic("release");

  lk->cpu = 0;

  // release ordering:
  //   critical section의 수정이 lock 해제 뒤로 밀리지 못하게 한다.
  __atomic_store_n(&lk->locked, 0, __ATOMIC_RELEASE);

  // acquire()에서 끈 interrupt 상태를 복구한다.
  pop_off();
}
```

## 보유 확인

```c++
int
holding(struct spinlock *lk)
{
  int r;
  r = (lk->locked && lk->cpu == mycpu());
  return r;
}
```

재진입 `acquire()`와 잘못된 `release()`를 잡는 검사다.

## interrupt nesting

```c++
void
push_off(void)
{
  int old = intr_get();

  intr_off();
  if (mycpu()->noff == 0)
    mycpu()->intena = old;
  mycpu()->noff += 1;
}

void
pop_off(void)
{
  struct cpu *c = mycpu();

  if (intr_get())
    panic("pop_off - interruptible");
  if (c->noff < 1)
    panic("pop_off");

  c->noff -= 1;
  if (c->noff == 0 && c->intena)
    intr_on();
}
```

`push_off()`가 중첩되어도 마지막 `pop_off()` 전까지 interrupt는 다시 켜지지 않는다.

## 사용 패턴

```c++
acquire(&lock);

// 짧은 critical section.
// 공유 자료구조를 읽고 쓰되, 오래 기다리거나 sleep하지 않는다.

release(&lock);
```

스핀락은 짧은 커널 임계 구역용이다. lock을 오래 들고 있으면 다른 CPU가 바쁜 대기를 계속한다.
