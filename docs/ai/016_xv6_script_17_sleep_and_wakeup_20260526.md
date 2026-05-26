# xv6 Kernel 17 코드 정리: sleep and wakeup

## 읽는 순서

- `docs/reference/scripts/ko/17-xv6-kernel-17-sleep-and-wakeup-rUSEYGBF3PY.md`
- `kernel/proc.c`: `sleep()`, `wakeup()`
- `kernel/sysproc.c`: `sys_pause()`
- `kernel/trap.c`: `clockintr()`

## 핵심

`sleep(chan, lock)`은 "조건이 아직 만족되지 않았으니 CPU를 포기하고 기다리겠다"는 kernel primitive다.
`wakeup(chan)`은 같은 channel에서 자는 process들을 다시 `RUNNABLE`로 만든다.

핵심 문제는 단순히 재우고 깨우는 것이 아니라, **조건 검사와 sleep 사이에서 wakeup을 놓치지 않는 것**이다.

## channel

channel은 "무엇을 기다리는가"를 나타내는 식별자다. xv6는 보통 기다리는 공유 변수의 주소를 channel로 쓴다.

```text
sleep(&ticks, &tickslock)
wakeup(&ticks)
```

`sleep/wakeup`은 `&ticks`의 의미를 해석하지 않는다. 그냥 같은 channel 값에서 자는 process를 깨운다.

## 기본 사용 패턴

현재 repo의 user-level sleep syscall 이름은 `sleep`이 아니라 `pause`다. `sys_pause()`는 `ticks`가 충분히
증가할 때까지 기다린다.

```c++
uint64
sys_pause(void)
{
  int n;
  uint ticks0;

  argint(0, &n);
  acquire(&tickslock);
  ticks0 = ticks;

  while (ticks - ticks0 < n) {
    if (killed(myproc())) {
      release(&tickslock);
      return -1;
    }
    sleep(&ticks, &tickslock);
  }

  release(&tickslock);
  return 0;
}
```

전형적인 패턴은 이 모양이다.

```text
acquire(condition lock)
while condition is false:
  sleep(channel, condition lock)
release(condition lock)
```

깨어난 뒤에도 `while`로 조건을 다시 검사해야 한다. `wakeup()`은 같은 channel의 process를 깨울 뿐이고, 조건이
정말 만족됐는지는 caller가 다시 확인해야 한다.

## 깨우는 쪽

timer interrupt가 `ticks`를 증가시키고, 같은 channel인 `&ticks`로 자는 process들을 깨운다.

```c++
void
clockintr()
{
  if (cpuid() == 0) {
    acquire(&tickslock);
    ticks++;
    wakeup(&ticks);
    release(&tickslock);
  }
}
```

기본 아이디어는 조건을 바꾸는 쪽도 같은 condition lock을 잡고, 조건을 바꾼 뒤 `wakeup(chan)`을 호출한다는 것이다.

## lost wakeup

아래 순서가 가능하면 process가 영원히 잠들 수 있다.

```text
process A: 조건 확인 -> 아직 false
process B: 조건을 true로 변경
process B: wakeup(chan)
process A: 이제 sleep(chan)에 들어감
```

이 경우 wakeup은 이미 지나갔고, A는 아직 sleeping 상태가 아니었으므로 깨워지지 않는다.

## `sleep()`이 막는 방법

`sleep()`은 caller가 넘긴 condition lock을 그냥 놓고 자지 않는다. 먼저 자기 process의 `p->lock`을 잡고,
그 다음 condition lock을 놓는다.

```c++
void
sleep(void *chan, struct spinlock *lk)
{
  struct proc *p = myproc();

  acquire(&p->lock);
  release(lk);

  p->chan = chan;
  p->state = SLEEPING;

  sched();

  p->chan = 0;
  release(&p->lock);
  acquire(lk);
}
```

순서가 중요하다.

```text
1. caller는 condition lock을 잡은 상태에서 조건을 확인한다.
2. sleep()은 p->lock을 먼저 잡는다.
3. 그 다음 condition lock을 놓는다.
4. chan/state를 설정하고 sched()로 CPU를 넘긴다.
5. 나중에 깨어나면 condition lock을 다시 잡고 caller에게 돌아간다.
```

`p->lock`은 `chan/state` 변경과 `wakeup()`의 검사를 연결한다. condition lock을 놓은 뒤 wakeup이 오더라도,
`wakeup()`은 같은 `p->lock`을 잡고 확인하므로 sleep이 `SLEEPING` 상태를 만들기 전후가 섞이지 않는다.

## `wakeup()`

```c++
void
wakeup(void *chan)
{
  for (p = proc; p < &proc[NPROC]; p++) {
    if (p != myproc()) {
      acquire(&p->lock);
      if (p->state == SLEEPING && p->chan == chan) {
        p->state = RUNNABLE;
      }
      release(&p->lock);
    }
  }
}
```

`wakeup()`은 모든 process를 훑고, 같은 channel에서 `SLEEPING`인 process만 `RUNNABLE`로 바꾼다.

## 정리

```text
condition lock:
  기다리는 조건 자체를 보호한다. 예: tickslock이 ticks를 보호.

p->lock:
  process의 chan/state 전환을 보호한다.

sleep(chan, lock):
  lock을 안전하게 놓고 SLEEPING 상태로 들어간다.

wakeup(chan):
  같은 chan에서 잠든 process를 RUNNABLE로 바꾼다.
```

코드 확인 위치:
`kernel/proc.c`, `kernel/sysproc.c`, `kernel/trap.c`
