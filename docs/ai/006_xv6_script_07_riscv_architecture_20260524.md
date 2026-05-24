# xv6 Kernel 7 코드 정리: RISC-V Architecture

## 읽는 순서

- `docs/reference/scripts/ko/07-xv6-kernel-7-riscv-architecture--S0Z0CmwkEk.md`
- `kernel/riscv.h`: CSR, interrupt, page table 관련 inline assembly
- `kernel/proc.h`: trapframe/context가 저장하는 register
- `kernel/start.c`, `kernel/proc.c`: privilege mode와 `tp` 사용

## register 묶음

```text
zero  항상 0
ra    return address
sp    stack pointer
gp    global pointer
tp    thread pointer, xv6 kernel에서는 hart id 보관
a0-a7 argument/return value, syscall 번호는 a7
t0-t6 caller-saved temporary
s0-s11 callee-saved register
pc    program counter
```

user register 전체는 trap 때 `trapframe`에 저장된다. kernel thread끼리의 context switch는
callee-saved register만 `context`에 저장한다.

```c++
// kernel/proc.h
struct context {
  uint64 ra;
  uint64 sp;
  uint64 s0;
  uint64 s1;
  uint64 s2;
  /* ... */
  uint64 s11;
};

struct trapframe {
  uint64 kernel_satp;
  uint64 kernel_sp;
  uint64 kernel_trap;
  uint64 epc;
  uint64 kernel_hartid;
  uint64 ra;
  uint64 sp;
  uint64 gp;
  uint64 tp;
  /* ... user registers ... */
  uint64 a0;
  uint64 a1;
  uint64 a7;
};
```

## privilege mode

```text
M-mode: machine. 부팅 초기, trap delegation, PMP 설정.
S-mode: supervisor. xv6 kernel 대부분이 실행되는 mode.
U-mode: user. user program 실행 mode.
```

```c++
// kernel/start.c
void
start()
{
  // mret가 돌아갈 privilege를 supervisor로 설정한다.
  unsigned long x = r_mstatus();
  x &= ~MSTATUS_MPP_MASK;
  x |= MSTATUS_MPP_S;
  w_mstatus(x);

  // mret 후 main()부터 supervisor mode로 실행한다.
  w_mepc((uint64)main);

  // exception/interrupt 처리를 supervisor mode로 위임한다.
  w_medeleg(0xffff);
  w_mideleg(0xffff);

  asm volatile("mret");
}
```

## CSR 접근

CSR은 Control and Status Register의 약자다. 일반-purpose register와 달리 CPU의 제어 상태를
읽고 쓰는 특수 register다. 예를 들어 현재 privilege mode, interrupt enable 여부, trap handler
주소, trap 원인 같은 값을 담는다.

```c++
// kernel/riscv.h
static inline uint64
r_sstatus()
{
  uint64 x;
  asm volatile("csrr %0, sstatus" : "=r"(x));
  return x;
}

static inline void
w_sstatus(uint64 x)
{
  asm volatile("csrw sstatus, %0" : : "r"(x));
}
```

CSR은 현재 privilege mode에 따라 접근 가능 여부가 다르다. user mode가 privileged CSR이나 명령을
실행하려 하면 trap이 난다.

## hart id와 `tp`

```c++
// kernel/start.c
int id = r_mhartid();
w_tp(id);

// kernel/proc.c
int
cpuid()
{
  return r_tp();
}
```

각 hart는 자기 register set을 가진다. xv6는 부팅 때 `mhartid`를 `tp`에 복사하고, kernel 안에서
`tp`를 현재 CPU 번호처럼 사용한다.

## trap 종류

```text
exception: 현재 instruction 때문에 발생한다.
  예: ecall, illegal instruction, page fault, alignment fault

interrupt: 현재 instruction 바깥에서 비동기로 발생한다.
  예: timer, UART, virtio disk
```

## 소스 대조 메모

강의는 timer interrupt를 machine `timervec`이 받아 supervisor software interrupt로 바꾸는 흐름을
설명한다. 현재 소스는 Sstc를 켜고 `stimecmp`를 사용한다.

```c++
// kernel/start.c
void
timerinit()
{
  w_menvcfg(r_menvcfg() | (1L << 63)); // Sstc 사용
  w_mcounteren(r_mcounteren() | 2);     // supervisor에서 time/stimecmp 허용
  w_stimecmp(r_time() + 1000000);       // 첫 timer interrupt 예약
}
```

따라서 현재 tree에서는 `timervec`보다 supervisor timer interrupt 경로를 기준으로 읽는 편이 맞다.
