# xv6 Kernel 10 코드 정리: Context Switching

## 읽는 순서

- `docs/reference/scripts/ko/10-xv6-kernel-10-context-switching-1sSanF_y8FY.md`
- `kernel/proc.h`: `trapframe`, `context`
- `kernel/proc.c`: `scheduler`, `sched`, `yield`
- `kernel/swtch.S`: kernel thread context switch
- `kernel/trampoline.S`, `kernel/trap.c`: user/kernel trap return

## 두 종류의 전환

```text
user <-> kernel 전환:
  trap / sret
  user register 전체와 user pc를 trapframe에 저장/복원

kernel thread <-> scheduler 전환:
  swtch()
  kernel context의 callee-saved register만 context에 저장/복원
```

trap은 privilege mode를 바꾸지만, `swtch()`는 이미 kernel mode 안에서 kernel thread 실행 흐름만
바꾼다.

## 저장되는 상태

```c++
// kernel/proc.h
struct trapframe {
  uint64 kernel_satp;
  uint64 kernel_sp;
  uint64 kernel_trap;
  uint64 epc; // saved user pc
  uint64 kernel_hartid;

  // user register 전체
  uint64 ra;
  uint64 sp;
  uint64 gp;
  uint64 tp;
  uint64 a0;
  uint64 a1;
  uint64 a7;
  /* ... */
};

struct context {
  uint64 ra;
  uint64 sp;

  // callee-saved register만 저장한다.
  uint64 s0;
  uint64 s1;
  /* ... */
  uint64 s11;
};
```

`trapframe`은 user mode 재개용이고, `context`는 kernel thread가 scheduler와 오갈 때 쓰인다.

## scheduler

```c++
void
scheduler(void)
{
  struct cpu *c = mycpu();
  c->proc = 0;

  for (;;) {
    intr_on();
    intr_off();

    for (struct proc *p = proc; p < &proc[NPROC]; p++) {
      acquire(&p->lock);
      if (p->state == RUNNABLE) {
        p->state = RUNNING;
        c->proc = p;

        // scheduler context -> process kernel context
        swtch(&c->context, &p->context);

        c->proc = 0;
      }
      release(&p->lock);
    }
  }
}
```

각 CPU는 자기 scheduler loop를 돈다. 실행할 process를 찾으면 그 process의 kernel context로
`swtch()`한다.

## yield와 sched

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

void
sched(void)
{
  struct proc *p = myproc();

  if (!holding(&p->lock))
    panic("sched p->lock");
  if (mycpu()->noff != 1)
    panic("sched locks");
  if (intr_get())
    panic("sched interruptible");

  // process kernel context -> scheduler context
  swtch(&p->context, &mycpu()->context);
}
```

timer interrupt에서 `yield()`가 호출되면 process는 `RUNNABLE`로 돌아가고 scheduler에게 CPU를
넘긴다.

## `swtch.S`

```asm
# void swtch(struct context *old, struct context *new)
# a0 = old, a1 = new
swtch:
        # 현재 kernel context 저장
        sd ra, 0(a0)
        sd sp, 8(a0)
        sd s0, 16(a0)
        sd s1, 24(a0)
        sd s11, 104(a0)

        # 다음 kernel context 복원
        ld ra, 0(a1)
        ld sp, 8(a1)
        ld s0, 16(a1)
        ld s1, 24(a1)
        ld s11, 104(a1)

        # 복원된 ra로 돌아간다.
        ret
```

`swtch()`는 `a`/`t` register를 저장하지 않는다. RISC-V calling convention상 caller-saved이고,
`swtch()` 호출을 넘어서 보존해야 할 kernel state는 `context`에 있는 값으로 충분하다.

## 전체 흐름

```text
user process 실행
  -> timer interrupt
  -> trampoline.S/uservec가 user register를 trapframe에 저장
  -> usertrap()
  -> yield()
  -> sched()
  -> swtch(&p->context, &cpu->context)
  -> scheduler()
  -> 다른 RUNNABLE process 선택
  -> swtch(&cpu->context, &next->context)
  -> prepare_return() + trampoline.S/userret
  -> sret로 user mode 재개
```

멀티코어에서는 각 CPU가 scheduler를 가진다. process 상태와 context는 shared memory에 있으므로
`p->lock`으로 보호한다.
