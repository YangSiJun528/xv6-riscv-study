# xv6 Kernel 24 코드 정리: exit/wait/kill syscalls

## 읽는 순서

- `docs/reference/scripts/ko/24-xv6-kernel-24-exit-wait-kill-syscalls-ZHc8CHqi3Ws.md`
- `kernel/sysproc.c`: `sys_exit`, `sys_wait`, `sys_kill`
- `kernel/proc.c`: `kexit`, `kwait`, `kkill`, `reparent`, `sleep`, `wakeup`
- `kernel/proc.h`: `enum procstate`, `struct proc`
- `kernel/trap.c`: user mode 복귀 전 `killed()` 확인
- `kernel/pipe.c`: kill된 sleeping process가 깨어나 실패로 빠지는 예

## 핵심

`exit`는 process를 즉시 완전히 없애지 않는다. 종료 상태를 `p->xstate`에 저장하고 `ZOMBIE`로 만든 뒤 scheduler로
넘어간다. 부모가 나중에 `wait`로 좀비 자식을 수거할 때 `freeproc()`이 실제 정리를 한다.

```c++
enum procstate { UNUSED, USED, SLEEPING, RUNNABLE, RUNNING, ZOMBIE };
```

`kill`도 즉시 제거가 아니다. 대상 process의 `killed` flag를 세우고, sleeping 상태라면 깨워서 다음 안전한
커널 경계에서 `kexit(-1)`로 이어지게 만든다.

## syscall wrapper

```c++
uint64
sys_exit(void)
{
  int n;
  argint(0, &n);
  kexit(n);
  return 0;                                   // 도달하지 않음
}

uint64
sys_wait(void)
{
  uint64 p;
  argaddr(0, &p);
  return kwait(p);
}

uint64
sys_kill(void)
{
  int pid;
  argint(0, &pid);
  return kkill(pid);
}
```

현재 repo는 커널 내부 구현 이름에 `k` prefix를 붙인다.

```text
sys_exit -> kexit
sys_wait -> kwait
sys_kill -> kkill
```

## `kexit(status)`: 좀비로 남기고 부모 깨우기

```c++
void
kexit(int status)
{
  struct proc *p = myproc();

  if (p == initproc)
    panic("init exiting");

  // 1. 열린 파일과 cwd 정리
  for (int fd = 0; fd < NOFILE; fd++) {
    if (p->ofile[fd]) {
      fileclose(p->ofile[fd]);
      p->ofile[fd] = 0;
    }
  }
  begin_op();
  iput(p->cwd);
  end_op();
  p->cwd = 0;

  // 2. parent/child 관계는 wait_lock으로 보호
  acquire(&wait_lock);
  reparent(p);
  wakeup(p->parent);

  // 3. 자기 상태는 p->lock으로 보호
  acquire(&p->lock);
  p->xstate = status;
  p->state = ZOMBIE;

  release(&wait_lock);

  // 4. 다시 실행되지 않고 scheduler로 넘어간다.
  sched();
  panic("zombie exit");
}
```

자식보다 부모가 먼저 죽으면 `reparent()`가 그 자식들을 `initproc` 밑으로 옮긴다. `init`은 반복적으로 `wait()`를
호출하므로 고아 process의 좀비도 결국 수거된다.

```c++
void
reparent(struct proc *p)
{
  for (pp = proc; pp < &proc[NPROC]; pp++) {
    if (pp->parent == p) {
      pp->parent = initproc;
      wakeup(initproc);
    }
  }
}
```

## `kwait(addr)`: 좀비 자식 수거

`wait(addr)`는 종료한 자식의 pid를 반환하고, `addr != 0`이면 종료 상태를 부모의 user address space에 복사한다.

```c++
int
kwait(uint64 addr)
{
  acquire(&wait_lock);

  for (;;) {
    havekids = 0;

    for (pp = proc; pp < &proc[NPROC]; pp++) {
      if (pp->parent == p) {
        acquire(&pp->lock);
        havekids = 1;

        if (pp->state == ZOMBIE) {
          pid = pp->pid;

          if (addr != 0 &&
              copyout(p->pagetable, addr, (char *)&pp->xstate,
                      sizeof(pp->xstate)) < 0) {
            release(&pp->lock);
            release(&wait_lock);
            return -1;
          }

          freeproc(pp);                       // proc slot을 UNUSED로 되돌림
          release(&pp->lock);
          release(&wait_lock);
          return pid;
        }

        release(&pp->lock);
      }
    }

    if (!havekids || killed(p)) {
      release(&wait_lock);
      return -1;
    }

    sleep(p, &wait_lock);                     // child exit의 wakeup(p->parent)를 기다림
  }
}
```

`sleep(p, &wait_lock)`에서 channel로 쓰는 값은 부모 자신의 `struct proc *`다. 자식이 `kexit()`에서
`wakeup(p->parent)`를 호출하면 같은 channel에서 자던 부모가 깨어난다.

## `kkill(pid)`: 죽이라고 표시하기

```c++
int
kkill(int pid)
{
  for (p = proc; p < &proc[NPROC]; p++) {
    acquire(&p->lock);
    if (p->pid == pid) {
      p->killed = 1;

      // sleep 중이면 usertrap()의 killed check까지 갈 수 있게 깨운다.
      if (p->state == SLEEPING)
        p->state = RUNNABLE;

      release(&p->lock);
      return 0;
    }
    release(&p->lock);
  }
  return -1;
}
```

대상 process는 바로 사라지지 않는다. `trap.c`는 syscall 처리 전후, interrupt/page fault 처리 후에 `killed(p)`를
확인하고, 설정되어 있으면 `kexit(-1)`한다.

```c++
if (killed(p))
  kexit(-1);
```

그래서 `pipe.c`, `sys_pause()`처럼 sleep할 수 있는 코드는 깨어난 뒤 `killed()`를 확인하고 실패로 빠지는 경로를
갖는다.

## lock 규칙

`parent` 관계는 `wait_lock`으로 보호된다. 개별 process의 `state`, `chan`, `killed`, `xstate`, `pid`는
`p->lock`으로 보호된다.

중요한 순서는 `wait_lock` 다음 `p->lock`이다.

```text
kexit:
  acquire(wait_lock)
  acquire(p->lock)
  state = ZOMBIE
  release(wait_lock)
  sched()

kwait:
  acquire(wait_lock)
  acquire(child->lock)
  if child is ZOMBIE: freeproc(child)
```

`kexit()`는 자기 상태를 `ZOMBIE`로 바꾸기 전까지 `wait_lock`을 놓지 않는다. 따라서 부모가 wakeup되어 먼저
실행되더라도 `wait_lock`에서 막히고, 이후에는 자식을 `ZOMBIE`로 관찰할 수 있다. 이것이 lost wakeup을 피하는
핵심이다.

## 현재 repo 기준 차이

- 강의: `exit`, `wait`, `kill`이라는 커널 내부 이름을 주로 말한다.
- 현재 repo: 실제 구현 이름은 `kexit`, `kwait`, `kkill`이다.
- 강의 스크립트의 `weightlock` 표현은 현재 코드 기준 `wait_lock`이다.
- 현재 repo의 `sys_pause()`는 전통적인 `sleep` syscall 위치에 있고, sleep 중 `killed()`를 확인한다.

## 한 줄 정리

`exit`는 process를 `ZOMBIE`로 남기고, `wait`는 그 좀비를 수거하며, `kill`은 즉시 죽이는 것이 아니라 다음
커널 복귀 경계에서 `exit(-1)`로 이어지게 표시한다.
