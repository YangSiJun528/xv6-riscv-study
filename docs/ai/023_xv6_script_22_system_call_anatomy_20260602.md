# xv6 Kernel 22 코드 정리: Anatomy of a system call

## 읽는 순서

- `docs/reference/scripts/ko/22-xv6-kernel-22-anatomy-of-a-system-call-w7Q66ItKrn8.md`
- `user/user.h`, `user/ulib.c`, `user/usys.pl`: user side wrapper/stub
- `kernel/syscall.h`, `kernel/syscall.c`: syscall number와 dispatch table
- `kernel/trampoline.S`, `kernel/trap.c`: `ecall` 이후 trap 진입/복귀
- `kernel/sysproc.c`, `kernel/proc.c`, `kernel/vm.c`: 실제 syscall 구현

## 핵심

시스템 콜은 사용자 코드에서는 함수 호출처럼 보이지만, 실제로는 정해진 register 약속과 `ecall`로 커널에 들어가는
경로다.

```text
a7       = syscall number
a0..a5   = syscall arguments
ecall    = user mode에서 supervisor trap 발생
trapframe->a0 = syscall return value
sret     = 다시 user mode로 복귀
```

`sbrk(n)`를 기준으로 보면 현재 repo의 흐름은 다음과 같다.

```text
user code
  -> sbrk(n) in user/ulib.c
  -> sys_sbrk(n, SBRK_EAGER) stub
  -> li a7, SYS_sbrk; ecall; ret
  -> trampoline.S:uservec
  -> trap.c:usertrap()
  -> syscall.c:syscall()
  -> sysproc.c:sys_sbrk()
  -> proc.c:growproc() 또는 lazy sz 증가
  -> trap.c:prepare_return()
  -> trampoline.S:userret
  -> sret
```

## user side: wrapper와 stub

현재 repo의 `sbrk()`는 바로 `ecall`하지 않는다. `ulib.c`의 wrapper가 eager/lazy mode를 두 번째 인자로 붙여
`sys_sbrk()` stub을 호출한다.

```c++
char *
sbrk(int n)
{
  return sys_sbrk(n, SBRK_EAGER);
}

char *
sbrklazy(int n)
{
  return sys_sbrk(n, SBRK_LAZY);
}
```

`user/usys.pl`은 assembly stub을 생성한다. 대부분은 `fork`, `exit` 같은 syscall 이름 그대로 stub을 만들지만,
현재 repo는 `sbrk`만 `sys_sbrk`라는 이름으로 만든다.

```perl
sub entry {
    my $prefix = "sys_";
    my $name = shift;
    if ($name eq "sbrk") {
      print ".global $prefix$name\n";
      print "$prefix$name:\n";
    } else {
      print ".global $name\n";
      print "$name:\n";
    }
    print " li a7, SYS_${name}\n";             // syscall number
    print " ecall\n";                          // kernel trap
    print " ret\n";                            // a0에 담긴 반환값으로 user 함수 복귀
}
```

`SYS_sbrk` 번호는 `kernel/syscall.h`에 있다.

```c++
#define SYS_sbrk   12
```

## trap entry: `ecall`에서 `usertrap()`까지

`ecall`이 발생하면 CPU는 supervisor mode로 들어오고, 현재 `stvec`가 가리키는 trampoline `uservec`에서 시작한다.
이 시점에는 아직 user page table을 쓰고 있으므로, `uservec`은 모든 process의 user page table에 같은 VA로
매핑되어 있어야 한다.

```asm
csrw sscratch, a0
li a0, TRAPFRAME

# user register를 이 process의 trapframe에 저장한다.
sd ra, 40(a0)
sd a7, 168(a0)
csrr t0, sscratch
sd t0, 112(a0)

# trapframe에 미리 저장된 kernel 값으로 전환한다.
ld sp, 8(a0)
ld t0, 16(a0)
ld t1, 0(a0)
csrw satp, t1
jalr t0
```

`jalr t0`의 목적지는 `trapframe->kernel_trap`, 즉 `usertrap()`이다.

## `usertrap()`: syscall인지 판단

RISC-V에서 user `ecall`의 `scause`는 8이다. `sepc`는 `ecall` 명령어 주소를 가리키므로, 복귀할 때 다시
`ecall`을 실행하지 않도록 4 증가시킨다.

```c++
if (r_scause() == 8) {
  if (killed(p))
    kexit(-1);

  // ecall 다음 명령어로 돌아가기 위해 PC를 넘긴다.
  p->trapframe->epc += 4;

  // sepc/scause/sstatus 처리가 끝난 뒤 interrupt를 켠다.
  intr_on();

  syscall();
}
```

`usertrap()`은 syscall 처리 전후에 `killed(p)`를 확인한다. 따라서 `kill()`로 표시된 프로세스는 user mode로
돌아가기 전에 `kexit(-1)`로 종료될 수 있다.

## `syscall()`: 번호로 함수 찾기

`syscall()`은 trapframe에 저장된 `a7`을 syscall table index로 사용한다. syscall handler의 반환값은
`trapframe->a0`에 저장된다.

```c++
static uint64 (*syscalls[])(void) = {
  [SYS_fork]    sys_fork,
  [SYS_exit]    sys_exit,
  [SYS_wait]    sys_wait,
  [SYS_kill]    sys_kill,
  [SYS_sbrk]    sys_sbrk,
  [SYS_pause]   sys_pause,
  [SYS_uptime]  sys_uptime,
  // ...
};

void
syscall(void)
{
  int num = p->trapframe->a7;

  if (num > 0 && num < NELEM(syscalls) && syscalls[num]) {
    p->trapframe->a0 = syscalls[num]();        // user 함수의 반환값처럼 보임
  } else {
    p->trapframe->a0 = -1;
  }
}
```

인자는 `a0..a5`에 들어 있다. `argraw()`는 trapframe에서 해당 register 값을 읽는다.

```c++
static uint64
argraw(int n)
{
  struct proc *p = myproc();
  switch (n) {
  case 0: return p->trapframe->a0;
  case 1: return p->trapframe->a1;
  case 2: return p->trapframe->a2;
  case 3: return p->trapframe->a3;
  case 4: return p->trapframe->a4;
  case 5: return p->trapframe->a5;
  }
  panic("argraw");
}
```

`argint()`/`argaddr()`는 register 값을 가져올 뿐이고, pointer가 실제로 유효한지는 보통 `copyin()`/`copyout()`이
검사한다.

## `sys_sbrk()`: 현재 repo의 실제 예

강의는 전통적인 `sbrk(n)`만 설명하지만, 현재 repo는 lazy allocation 실험 코드가 들어 있어서 인자를 두 개 받는다.

```c++
uint64
sys_sbrk(void)
{
  uint64 addr = myproc()->sz;
  int n, t;

  argint(0, &n);                               // 늘릴 byte 수
  argint(1, &t);                               // SBRK_EAGER 또는 SBRK_LAZY

  if (t == SBRK_EAGER || n < 0) {
    if (growproc(n) < 0)
      return -1;                               // 즉시 page 할당/해제
  } else {
    if (addr + n < addr)
      return -1;
    if (addr + n > TRAPFRAME)
      return -1;
    myproc()->sz += n;                         // page는 아직 할당하지 않음
  }
  return addr;                                 // 기존 break
}
```

lazy mode에서는 실제 physical page가 즉시 생기지 않는다. 이후 user가 그 VA를 만져 page fault가 나면
`trap.c`의 `vmfault()` 경로가 page를 할당한다. `copyin()`/`copyout()`도 lazy page를 만나면 `vmfault()`를
시도한다.

## user return

`prepare_return()`은 다음 user trap을 위해 `stvec`, `trapframe->kernel_*`, `sstatus`, `sepc`를 설정한다.
그 뒤 trampoline `userret`이 user page table로 바꾸고 register를 복원한다.

```asm
csrw satp, a0
li a0, TRAPFRAME

ld a7, 168(a0)
ld a0, 112(a0)                                # syscall return value
sret                                           # user mode + sepc로 복귀
```

## 현재 repo 기준 차이

- 강의: `sbrk` stub이 직접 `SYS_sbrk`를 호출하는 흐름으로 설명한다.
- 현재 repo: `sbrk(n)`/`sbrklazy(n)` wrapper가 `sys_sbrk(n, mode)`를 호출한다.
- 강의: `usertrapret()` 이름을 사용한다.
- 현재 repo: `prepare_return()`이 복귀 준비를 하고, trampoline `userret`이 최종 `sret`을 수행한다.
- 현재 repo: lazy allocation 때문에 `sys_sbrk()`와 `copyin()`/`copyout()`이 강의 원형보다 더 복잡하다.

## 한 줄 정리

시스템 콜은 `a7=번호`, `a0..a5=인자`, `ecall`, trapframe 저장, syscall table dispatch, `a0=반환값`,
`sret` 복귀라는 약속으로 user 함수 호출처럼 보이게 만든 커널 진입 경로다.
