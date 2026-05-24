# xv6 Kernel 9 코드 정리: RISC-V Trap Processing

## 읽는 순서

- `docs/reference/scripts/ko/09-xv6-kernel-9-riscv-trap-processing-hSjJ94PoLXc.md`
- `kernel/riscv.h`: `sstatus`, `stvec`, `sepc`, `scause`, delegation CSR
- `kernel/trap.c`: user/kernel trap 처리
- `kernel/kernelvec.S`, `kernel/trampoline.S`: low-level register save/restore
- `kernel/start.c`: trap delegation, timer setup

## trap이란

```text
trap = exception 또는 interrupt

exception: 현재 instruction 때문에 동기적으로 발생
  ecall, illegal instruction, page fault, access fault

interrupt: 외부 사건으로 비동기적으로 발생
  timer, UART, virtio disk
```

## hardware가 하는 일

```text
trap 발생
  -> 현재 pc를 sepc에 저장
  -> 원인을 scause에 저장
  -> 추가 fault 주소 등을 stval에 저장할 수 있음
  -> 이전 privilege mode를 sstatus.SPP에 저장
  -> interrupt enable 상태를 sstatus.SPIE에 저장
  -> interrupt를 끄고 supervisor mode로 전환
  -> stvec이 가리키는 handler로 jump
```

```c++
// kernel/riscv.h
#define SSTATUS_SPP  (1L << 8) // previous mode: 1=S, 0=U
#define SSTATUS_SPIE (1L << 5) // previous interrupt enable
#define SSTATUS_SIE  (1L << 1) // supervisor interrupt enable

static inline void w_stvec(uint64 x) { asm volatile("csrw stvec, %0" : : "r"(x)); }
static inline void w_sepc(uint64 x)  { asm volatile("csrw sepc, %0" : : "r"(x)); }
static inline uint64 r_scause()      { uint64 x; asm volatile("csrr %0, scause" : "=r"(x)); return x; }
static inline uint64 r_stval()       { uint64 x; asm volatile("csrr %0, stval" : "=r"(x)); return x; }
```

## trap vector 선택

```c++
// kernel/trap.c
void
trapinithart(void)
{
  // kernel mode에서 난 trap은 kernelvec.S로 간다.
  w_stvec((uint64)kernelvec);
}

void
prepare_return(void)
{
  intr_off();

  // user mode에서 다음 trap이 나면 trampoline의 uservec으로 들어오게 한다.
  uint64 trampoline_uservec = TRAMPOLINE + (uservec - trampoline);
  w_stvec(trampoline_uservec);
}
```

`stvec`은 현재 상황에 맞게 바뀐다. kernel 안에서는 `kernelvec`, user로 돌아가기 전에는
`uservec`을 가리킨다.

## user trap

```c++
uint64
usertrap(void)
{
  if ((r_sstatus() & SSTATUS_SPP) != 0)
    panic("usertrap: not from user mode");

  // 이제 kernel 안이므로 kernel trap vector를 사용한다.
  w_stvec((uint64)kernelvec);

  struct proc *p = myproc();
  p->trapframe->epc = r_sepc(); // user pc 저장

  if (r_scause() == 8) {
    // ecall은 4바이트 instruction이므로 다음 instruction으로 복귀한다.
    p->trapframe->epc += 4;
    intr_on();
    syscall();
  } else if (devintr() != 0) {
    // device/timer interrupt
  } else {
    setkilled(p);
  }

  prepare_return();
  return MAKE_SATP(p->pagetable); // trampoline.S userret에 전달
}
```

## user로 돌아가기

```c++
void
prepare_return(void)
{
  struct proc *p = myproc();

  p->trapframe->kernel_satp = r_satp();
  p->trapframe->kernel_sp = p->kstack + PGSIZE;
  p->trapframe->kernel_trap = (uint64)usertrap;
  p->trapframe->kernel_hartid = r_tp();

  // sret가 user mode로 내려가게 준비한다.
  unsigned long x = r_sstatus();
  x &= ~SSTATUS_SPP; // previous mode = user
  x |= SSTATUS_SPIE; // user mode에서 interrupt enable
  w_sstatus(x);

  w_sepc(p->trapframe->epc);
}
```

```asm
# kernel/trampoline.S
userret:
        # user page table로 전환한다.
        csrw satp, a0
        sfence.vma zero, zero

        # trapframe에서 user register를 복원한다.
        li a0, TRAPFRAME
        ld ra, 40(a0)
        ld sp, 48(a0)
        ld a7, 168(a0)

        # prepare_return()이 설정한 sstatus/sepc를 사용해 user mode로 복귀한다.
        sret
```

## kernel trap

```asm
# kernel/kernelvec.S
kernelvec:
        addi sp, sp, -256
        sd ra, 0(sp)
        sd a0, 72(sp)
        sd a7, 128(sp)

        call kerneltrap

        ld ra, 0(sp)
        ld a0, 72(sp)
        ld a7, 128(sp)
        addi sp, sp, 256
        sret
```

kernel trap은 이미 kernel stack 위에서 실행되므로, `kernelvec.S`가 필요한 caller-saved register를
stack에 저장한 뒤 `kerneltrap()`을 호출한다.

## timer interrupt 소스 대조

강의는 machine `timervec`이 supervisor software interrupt를 만든다고 설명한다. 현재 소스에는
`timervec`이 없고, Sstc 기반 supervisor timer interrupt를 직접 처리한다.

```c++
// kernel/trap.c
void
clockintr()
{
  if (cpuid() == 0) {
    acquire(&tickslock);
    ticks++;
    wakeup(&ticks);
    release(&tickslock);
  }
  w_stimecmp(r_time() + 1000000); // 다음 timer interrupt 예약
}

int
devintr()
{
  if (r_scause() == 0x8000000000000005L) {
    clockintr();
    return 2;
  }
  return 0;
}
```

trap의 큰 구조는 강의와 같지만, timer interrupt 구현은 현재 tree의 `stimecmp` 경로를 기준으로
봐야 한다.
