# xv6 Kernel 16 코드 정리: Scheduling + swtch.S

## 읽는 순서

- `docs/reference/scripts/ko/16-xv6-kernel-16-scheduling-plus-swtch.s--O_JX5mMMHY.md`
- `kernel/proc.c`: `yield`, `sched`, `scheduler`, `mycpu`, `myproc`
- `kernel/swtch.S`: kernel context switch
- `kernel/proc.h`: `struct context`, `struct cpu`, `struct proc`

## 핵심

Scheduling은 user register를 저장하는 trapframe 문제가 아니라, kernel 안에서 실행 흐름을 바꾸는 문제다.
`swtch()`는 현재 kernel thread의 `context`를 저장하고, 다음 kernel thread의 `context`를 로드한다.

```text
process kernel thread -> scheduler thread -> another process kernel thread
```

## CPU별 scheduler

각 CPU는 자기 `scheduler()` loop를 가진다. runnable process를 찾으면 해당 process의 kernel context로
전환한다.

```c++
void
scheduler(void)
{
  struct cpu *c = mycpu();
  c->proc = 0;

  for (;;) {
    intr_on();
    intr_off();

    for (p = proc; p < &proc[NPROC]; p++) {
      acquire(&p->lock);
      if (p->state == RUNNABLE) {
        p->state = RUNNING;
        c->proc = p;
        swtch(&c->context, &p->context);
        c->proc = 0;
      }
      release(&p->lock);
    }
  }
}
```

현재 repo는 runnable process를 못 찾으면 `wfi`로 interrupt를 기다린다. 그래서 loop 시작에서 interrupt를
잠깐 켜서 device interrupt가 sleeping process를 깨울 수 있게 한다.

## `yield()`와 `sched()`

timer interrupt 등으로 현재 process가 CPU를 양보할 때 `yield()`가 호출된다.

```c++
void
yield(void)
{
  struct proc *p = myproc();

  acquire(&p->lock);
  p->state = RUNNABLE;
  sched();
  release(&p->lock);
}
```

`sched()`는 process context에서 scheduler context로 넘어가는 공통 함수다.

```c++
void
sched(void)
{
  struct proc *p = myproc();

  if (!holding(&p->lock))
    panic("sched p->lock");
  if (p->state == RUNNING)
    panic("sched RUNNING");
  if (intr_get())
    panic("sched interruptible");

  swtch(&p->context, &mycpu()->context);
}
```

`sched()`에 들어올 때는 process state가 이미 `RUNNING`이 아니어야 한다. 즉 `yield()`는 `RUNNABLE`,
`sleep()`은 `SLEEPING`, `kexit()`은 `ZOMBIE`로 바꾼 뒤 `sched()`에 들어간다.

## `swtch.S`

`swtch(old, new)`의 인자는 RISC-V 호출 규약상 `a0`, `a1`에 들어온다.

- `a0`: 현재 context를 저장할 위치
- `a1`: 다음 context를 읽어올 위치

```asm
swtch:
        # 현재 kernel 실행 재개에 필요한 register를 old context에 저장한다.
        sd ra, 0(a0)
        sd sp, 8(a0)
        sd s0, 16(a0)
        sd s1, 24(a0)
        # ...
        sd s11, 104(a0)

        # 다음 context에서 같은 register들을 로드한다.
        ld ra, 0(a1)
        ld sp, 8(a1)
        ld s0, 16(a1)
        ld s1, 24(a1)
        # ...
        ld s11, 104(a1)

        # 로드된 ra로 return하므로, 다른 실행 흐름으로 돌아간다.
        ret
```

`a*`, `t*` register는 caller-saved라서 `swtch()`가 저장하지 않는다.

- caller-saved: 호출한 쪽이 계속 필요하면 call 전에 직접 stack 등에 저장한다. RISC-V에서는 `a0-a7`,
  `t0-t6`, `ra`.
- callee-saved: 호출받은 쪽이 사용했다면 return 전에 원래 값으로 복구한다. RISC-V에서는 `s0-s11`, `sp`.

`swtch()`는 일반 함수처럼 호출되지만, return하는 실행 흐름이 바뀐다. 그래서 kernel thread를 다시 시작하는 데
필요한 `ra`, `sp`, `s0-s11`만 context에 저장한다. `ra`는 ABI상 caller-saved지만, 여기서는 "나중에 어디로
return할지" 자체가 context의 일부라서 저장한다.

## lock이 특이하게 보이는 이유

`p->lock`은 process의 `state`와 `context`를 scheduler와 process 사이에서 넘겨주는 동안 보호한다.
그래서 보통 함수처럼 "잡은 함수가 같은 함수에서 푼다"가 아니라, `swtch()`를 건너 반대쪽 실행 흐름에서 풀릴 수 있다.
이게 가능한 이유는 `swtch()`가 같은 CPU 안에서 process kernel thread와 scheduler thread를 바꾸기 때문이다.
xv6 spinlock도 `holding()`을 검사할 때 "현재 CPU가 이 lock을 들고 있는가"를 본다.

```text
process가 CPU를 양보하는 경우:

1. process: acquire(p->lock)
2. process: p->state = RUNNABLE / SLEEPING / ZOMBIE
3. process: swtch(&p->context, &cpu->context)
4. scheduler: swtch() 다음 줄에서 재개
5. scheduler: c->proc = 0
6. scheduler: release(p->lock)

scheduler가 process를 실행하는 경우:

1. scheduler: acquire(p->lock)
2. scheduler: p->state == RUNNABLE 확인
3. scheduler: p->state = RUNNING, c->proc = p
4. scheduler: swtch(&cpu->context, &p->context)
5. process: 이전 sched()/forkret 지점에서 재개
6. process: release(p->lock)
```

즉 이 lock은 process를 실행하는 전체 시간을 보호하는 lock이 아니다. `state`를 바꾸고, `context`를 저장/복원하고,
"이 process를 지금 어느 CPU가 실행 중인가"를 넘겨주는 짧은 구간을 보호한다. 다만 개념적으로는 CPU가 lock을
마음대로 푼다기보다, process와 scheduler가 같은 CPU에서 `p->lock`을 넘겨받는 handoff라고 보는 편이 정확하다.

코드 확인 위치:
`kernel/proc.c`, `kernel/swtch.S`, `kernel/proc.h`
