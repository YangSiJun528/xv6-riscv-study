# xv6 Kernel 26 코드 정리: Traps in kernel mode

## 읽는 순서

- `docs/reference/scripts/ko/26-xv6-kernel-26-traps-in-kernel-mode-Aj3DLErovD8.md`
- `kernel/trap.c`: `usertrap`, `prepare_return`, `kerneltrap`, `devintr`, `clockintr`
- `kernel/kernelvec.S`: kernel mode trap entry/return
- `kernel/proc.c`: `scheduler`, `yield`, `sched`, `myproc`
- `kernel/riscv.h`: trap 관련 CSR helper와 `SSTATUS_*` bit

## 핵심

user mode trap은 trampoline의 `uservec`에서 user register를 process별 `trapframe`에 저장한 뒤 `usertrap()`으로 간다.
kernel mode trap은 이미 kernel page table과 kernel stack 위에서 실행 중이므로, `kernelvec`이 현재 kernel stack에
필요한 register를 저장하고 `kerneltrap()`을 호출한다.

```text
kernel code
  -> trap hardware: sepc/scause/sstatus 갱신, stvec으로 jump
  -> kernelvec.S: 현재 kernel stack에 register 저장
  -> trap.c:kerneltrap()
  -> devintr()
  -> timer interrupt이면 yield() 가능
  -> sepc/sstatus 복원
  -> kernelvec.S: register 복원
  -> sret로 interrupted kernel PC 복귀
```

## user trap과 kernel trap 차이

| 구분 | user mode trap | kernel mode trap |
| --- | --- | --- |
| `stvec` | user 실행 중에는 trampoline의 `uservec` | kernel 실행 중에는 `kernelvec` |
| 저장 위치 | process의 `trapframe` | 현재 process의 kernel stack |
| page table | 처음에는 user page table, `uservec`에서 kernel page table로 전환 | 이미 kernel page table |
| C handler | `usertrap()` | `kerneltrap()` |
| 처리 범위 | syscall, device interrupt, user page fault, kill 처리 | device/timer interrupt만 정상 처리, 예외는 panic |
| return | `prepare_return()` + `userret` + `sret` | `kernelvec`에서 복원 후 `sret` |

`usertrap()`은 user mode에서 왔는지 확인한다.

```c++
if ((r_sstatus() & SSTATUS_SPP) != 0)
  panic("usertrap: not from user mode");
```

반대로 `kerneltrap()`은 supervisor mode에서 왔고, trap handler 진입 후 interrupt가 꺼져 있는지 확인한다.

```c++
if ((sstatus & SSTATUS_SPP) == 0)
  panic("kerneltrap: not from supervisor mode");
if (intr_get() != 0)
  panic("kerneltrap: interrupts enabled");
```

## `stvec` 전환

hart 초기화 때는 kernel trap vector가 설치된다.

```c++
void
trapinithart(void)
{
  w_stvec((uint64)kernelvec);
}
```

user trap으로 kernel에 들어오면, 이후 kernel code에서 발생하는 trap은 `kerneltrap()`으로 가야 하므로
`usertrap()` 초반에 `stvec`을 `kernelvec`으로 바꾼다.

```c++
w_stvec((uint64)kernelvec);
```

user mode로 돌아가기 직전에는 다시 `uservec`으로 돌린다. 이때 kernel code가 `uservec`으로 trap되는 상황을 피하려고
먼저 interrupt를 끈다.

```c++
intr_off();
uint64 trampoline_uservec = TRAMPOLINE + (uservec - trampoline);
w_stvec(trampoline_uservec);
```

## `kernelvec.S`: kernel stack에 저장

현재 repo의 `kernelvec.S`는 stack에 256바이트를 만들고 C 함수 호출이 덮을 수 있는 caller-saved register들을
저장한다. 강의 스크립트는 "모든 일반 register"처럼 설명하지만, 현재 코드는 `s0..s11` 같은 callee-saved register를
직접 저장하지 않고 C calling convention에 맡긴다.

```asm
kernelvec:
        addi sp, sp, -256

        sd ra, 0(sp)
        sd gp, 16(sp)
        sd t0, 32(sp)
        # ...
        sd a0, 72(sp)
        sd a7, 128(sp)
        # ...
        sd t6, 240(sp)

        call kerneltrap

        ld ra, 0(sp)
        ld gp, 16(sp)
        ld t0, 32(sp)
        # ...
        ld a0, 72(sp)
        ld a7, 128(sp)
        # ...
        ld t6, 240(sp)

        addi sp, sp, 256
        sret
```

`sp`는 stack frame을 push/pop하면서 원래 값으로 돌아가므로 별도 slot을 쓰지 않는다. `tp`는 hart id를 담기 때문에
복원하지 않는다. timer interrupt에서 `yield()`한 process가 나중에 다른 hart에서 이어질 수 있으므로, 예전 hart id로
덮으면 안 된다.

## `kerneltrap()`: 원인 처리와 복귀 CSR 보존

`kerneltrap()`은 먼저 `sepc`, `sstatus`, `scause`를 읽어 둔다. `yield()` 중 다른 trap이 발생하면 hart의 CSR 값이
바뀔 수 있으므로, 돌아가기 전에 `sepc`와 `sstatus`를 복원해야 한다.

```c++
uint64 sepc = r_sepc();
uint64 sstatus = r_sstatus();
uint64 scause = r_scause();

if ((which_dev = devintr()) == 0) {
  printf("scause=0x%lx sepc=0x%lx stval=0x%lx\n",
         scause, r_sepc(), r_stval());
  panic("kerneltrap");
}

if (which_dev == 2 && myproc() != 0)
  yield();

w_sepc(sepc);
w_sstatus(sstatus);
```

현재 repo는 강의 스크립트가 말하는 `myproc()->state == RUNNING` 검사를 하지 않고 `myproc() != 0`만 확인한다.
scheduler 안에서는 `c->proc == 0`이므로 timer interrupt가 와도 `yield()`하지 않는다.

## scheduler에서 온 timer interrupt

스크립트는 scheduler가 runnable process를 찾기 전에 interrupt를 켜는 순간 pending interrupt가 처리될 수 있다고
설명한다. 현재 repo의 `scheduler()`도 같은 이유로 loop마다 interrupt를 잠깐 켠 뒤 다시 끈다.

```c++
intr_on();
intr_off();
```

이때 scheduler는 특정 process를 실행 중인 상태가 아니므로 `c->proc == 0`이다. timer interrupt가 여기서 들어왔는데
`yield()`를 호출하면 넘겨줄 현재 process가 없어서 scheduler 상태가 꼬인다. 그래서 현재 코드는 `kerneltrap()`에서
`myproc() != 0`일 때만 timer interrupt에 대해 `yield()`한다.

또 scheduler가 process를 고르고 `swtch()`하기 전후에는 `p->lock`을 잡고 있다. `acquire()`는 `push_off()`로 현재
hart의 interrupt를 끄므로, process 상태와 CPU 전환을 만지는 구간에서는 timer interrupt가 끼어들지 않는다.

## `devintr()`: external interrupt와 timer interrupt

`devintr()`은 `scause`로 interrupt 종류를 나눈다.

```c++
if (scause == 0x8000000000000009L) {
  int irq = plic_claim();
  if (irq == UART0_IRQ)
    uartintr();
  else if (irq == VIRTIO0_IRQ)
    virtio_disk_intr();
  if (irq)
    plic_complete(irq);
  return 1;
} else if (scause == 0x8000000000000005L) {
  clockintr();
  return 2;
}
```

강의 스크립트는 timer interrupt를 machine mode가 supervisor software interrupt로 넘기는 흐름처럼 설명하지만,
현재 repo의 `devintr()`은 supervisor timer interrupt 값 `0x8000000000000005L`을 직접 처리한다. `clockintr()`은
hart 0에서만 `ticks`를 증가시키고 `wakeup(&ticks)`를 호출하며, 모든 hart에서 다음 timer interrupt를 예약한다.

PLIC를 거치는 external interrupt에서는 `plic_claim()`이 실제 IRQ 번호를 반환한다. 처리 후에는 `plic_complete(irq)`를
호출해야 한다. PLIC는 장치별로 한 번에 하나의 interrupt만 outstanding으로 두므로, complete는 해당 장치가 다음
interrupt를 다시 올릴 수 있음을 PLIC에 알리는 단계다.

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

  w_stimecmp(r_time() + 1000000);
}
```

## timer interrupt와 `yield()`

timer interrupt는 `devintr()`에서 `2`로 표시된다. user mode에서 timer interrupt를 받으면 `usertrap()`은 처리 후
`yield()`로 CPU를 양보할 수 있다.

```c++
if (which_dev == 2)
  yield();
```

kernel mode에서 timer interrupt를 받았을 때도 현재 process가 있으면 `kerneltrap()`이 `yield()`를 호출한다.

```c++
if (which_dev == 2 && myproc() != 0)
  yield();
```

`yield()`는 process 상태를 `RUNNABLE`로 바꾸고 scheduler로 전환한다.

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

따라서 kernel mode trap에서 시작했더라도 timer interrupt 때문에 다른 process가 먼저 실행될 수 있다. 나중에 원래
process가 다시 선택되면 `sched()` 이후로 돌아오고, `kerneltrap()`은 저장해 둔 `sepc`/`sstatus`를 복원한 뒤
`kernelvec`으로 돌아간다.

## nested interrupt 주의점

trap에 진입하면 hardware가 interrupt enable bit를 끄므로 `kerneltrap()`은 interrupt가 꺼진 상태를 전제로 한다.
device handler와 timer handler도 이 상태에서 실행된다. pending interrupt는 interrupt가 다시 켜질 때까지 기다린다.

`usertrap()`의 syscall 경로는 예외다. syscall 처리 중에는 오래 걸리는 kernel code를 실행할 수 있으므로 interrupt를
다시 켠다. 다만 interrupt가 `sepc`, `scause`, `sstatus`를 덮을 수 있기 때문에, `usertrap()`은 필요한 CSR 처리를
끝낸 뒤에야 `intr_on()`을 호출한다.

```c++
p->trapframe->epc = r_sepc();
p->trapframe->epc += 4;
intr_on();
syscall();
```

이때는 이미 `stvec`이 `kernelvec`을 가리키므로, syscall 실행 중 device/timer interrupt가 오면 kernel trap 경로로
들어가 handler를 실행한 뒤 원래 syscall 코드로 돌아온다.

반대로 user mode로 돌아가기 직전에는 `prepare_return()`이 `intr_off()`를 먼저 호출한다. `stvec`을 `uservec`으로
바꾼 뒤 아직 kernel mode에 있는 짧은 구간에서 interrupt가 발생하면 kernel trap이 user trap entry로 들어가게 되므로
위험하다.

## 현재 repo 기준 차이

- 강의: `usertrapret()` 이름으로 user 복귀를 설명한다.
- 현재 repo: `prepare_return()`이 CSR과 trapframe을 준비하고, trampoline의 `userret`이 실제 `sret`를 수행한다.
- 강의: timer interrupt를 software interrupt 경유로 설명한다.
- 현재 repo: `devintr()`이 supervisor timer interrupt를 직접 처리하고 `clockintr()`에서 `w_stimecmp()`로 다음 tick을 예약한다.
- 강의: `kernelvec`이 일반 register를 모두 저장한다고 설명한다.
- 현재 repo: `kernelvec.S`는 caller-saved register를 저장하고, `tp`는 hart 이동 가능성 때문에 복원하지 않는다.
- 강의: timer interrupt에서 process state가 `RUNNING`인지 확인한다고 설명한다.
- 현재 repo: `kerneltrap()`은 `which_dev == 2 && myproc() != 0`만 확인한다.

## 한 줄 정리

kernel mode trap은 trampoline/trapframe 경로가 아니라 현재 kernel stack의 `kernelvec` 경로로 들어오며, timer interrupt는
`kerneltrap()` 안에서 `yield()`를 유발할 수 있으므로 `sepc`와 `sstatus`를 저장했다가 반드시 복원해야 한다.
