# xv6 Kernel 6 코드 정리: Syscalls from Userland

## 읽는 순서

- `docs/reference/scripts/ko/06-xv6-kernel-6-syscalls-from-userland-RdxHGyeoyqI.md`
- `user/user.h`, `user/usys.pl`: user 프로그램이 보는 syscall API와 stub 생성
- `kernel/syscall.h`, `kernel/syscall.c`: syscall 번호, dispatch, 인자 추출
- `user/kill.c`: user program 예시
- `kernel/proc.c`: 현재 소스의 첫 user process 시작 방식

## user program

```c++
// user/kill.c
int
main(int argc, char **argv)
{
  if (argc < 2) {
    fprintf(2, "usage: kill pid...\n");
    exit(1);
  }

  for (int i = 1; i < argc; i++)
    kill(atoi(argv[i])); // user syscall wrapper 호출

  exit(0);
}
```

user code는 `kill()`을 그냥 C 함수처럼 호출한다. 실제로는 `usys.pl`이 만든 assembly stub이
`a7`에 syscall 번호를 넣고 `ecall`을 실행한다.

## user API와 syscall stub

```c++
// user/user.h
int fork(void);
int exit(int) __attribute__((noreturn));
int kill(int);
int exec(const char *, char **);
int open(const char *, int);
int write(int, const void *, int);
char *sys_sbrk(int, int);
```

```perl
# user/usys.pl
sub entry {
    my $name = shift;
    print ".global $name\n";
    print "$name:\n";
    print " li a7, SYS_${name}\n";
    print " ecall\n";
    print " ret\n";
}
```

생성되는 stub의 모양은 다음과 같다.

```asm
open:
        # syscall 번호는 a7에 둔다.
        li a7, SYS_open

        # user mode에서 trap을 발생시켜 kernel로 들어간다.
        ecall

        # kernel이 a0에 반환값을 넣고 돌아오면 C caller에게 반환한다.
        ret
```

인자는 RISC-V 호출 규약대로 `a0`, `a1`, `a2` ... 에 이미 들어 있다. stub은 인자 레지스터를
건드리지 않는다.

## syscall 번호와 dispatch

```c++
// kernel/syscall.h
#define SYS_fork   1
#define SYS_exit   2
#define SYS_kill   6
#define SYS_exec   7
#define SYS_open   15
#define SYS_write  16
#define SYS_close  21
```

```c++
// kernel/syscall.c
static uint64 (*syscalls[])(void) = {
  [SYS_fork]  sys_fork,
  [SYS_exit]  sys_exit,
  [SYS_kill]  sys_kill,
  [SYS_exec]  sys_exec,
  [SYS_open]  sys_open,
  [SYS_write] sys_write,
  [SYS_close] sys_close,
};

void
syscall(void)
{
  struct proc *p = myproc();
  int num = p->trapframe->a7; // user stub이 넣은 syscall 번호

  if (num > 0 && num < NELEM(syscalls) && syscalls[num])
    p->trapframe->a0 = syscalls[num](); // 반환값도 a0
  else
    p->trapframe->a0 = -1;
}
```

## syscall 인자

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
  return -1;
}
```

kernel은 trapframe에 저장된 user register에서 인자를 읽는다. pointer/string 인자는 `copyin`,
`copyinstr`가 page table 권한을 확인하며 kernel buffer로 복사한다.

## 첫 user process

강의는 `initcode.s`가 첫 user code라고 설명한다. 현재 소스에는 `user/initcode.S`가 없고,
첫 process가 처음 scheduling될 때 kernel이 `/init`을 직접 `kexec()`한다.

```c++
// kernel/proc.c
void
userinit(void)
{
  struct proc *p = allocproc();
  initproc = p;
  p->cwd = namei("/");
  p->state = RUNNABLE;
  release(&p->lock);
}

void
forkret(void)
{
  struct proc *p = myproc();

  // 첫 실행 때 file system 초기화 후 /init을 현재 process image로 적재한다.
  p->trapframe->a0 = kexec("/init", (char *[]){"/init", 0});

  prepare_return();
  ((void (*)(uint64))trampoline_userret)(MAKE_SATP(p->pagetable));
}
```

흐름은 같다. user process가 syscall wrapper를 호출하면 `ecall`로 kernel에 들어가고, kernel은
`a7` 번호로 handler를 찾아 실행한 뒤 결과를 `a0`에 넣어 user mode로 돌아간다.
