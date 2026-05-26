# xv6 Kernel 15 코드 정리: Trampoline and Trapframe

## 읽는 순서

- `docs/reference/scripts/ko/15-xv6-kernel-15-trampoline-and-trapframe-_p_MvMnwJbE.md`
- `kernel/trampoline.S`: `uservec`, `userret`
- `kernel/trap.c`: `usertrap()`, `prepare_return()`
- `kernel/proc.h`: `struct trapframe`

## 핵심

이 강의는 14번에서 본 trap 흐름을 실제 코드로 따라간다. 큰 흐름은 다음과 같다.

```text
user code
  -> trap 발생
  -> uservec
  -> usertrap - 트랩 처리
  -> prepare_return
  -> userret
  -> sret
  -> user code
```

강의의 `usertrapret()` 역할은 현재 repo에서 `prepare_return()`과 `usertrap()`의 return 흐름으로 나뉘어 있다.
`usertrap()`이 user page table의 `satp` 값을 반환하면, `trampoline.S`에서 바로 이어지는 `userret`이 실행된다.

## trap 직후 상태

trap이 발생하면 hardware는 interrupt를 끄고 supervisor mode로 들어가며, user PC를 `sepc`에 저장하고
`stvec`에 있는 주소로 점프한다. xv6는 user mode에서 trap이 나면 `stvec`이 `uservec`을 가리키도록 준비해 둔다.

이때 중요한 점은 `satp`가 아직 user page table이라는 것이다. 그래서 `uservec`과 `userret`이 들어 있는
trampoline page는 user page table과 kernel page table 양쪽에서 같은 virtual address에 매핑된다.

## trampoline page의 label

`kernel/trampoline.S`에는 `uservec`과 `userret` 두 루틴이 들어 있다. `trampoline`은 page 시작 지점을 가리키는
label이고, `uservec`과 `userret`은 그 page 안의 코드 위치다.

그래서 xv6는 `TRAMPOLINE + (uservec - trampoline)` 같은 방식으로 실제 실행할 virtual address를 계산한다.
page table이 user/kernel 중 어느 쪽이든, trampoline page는 같은 virtual address에 있으므로 이 계산이 계속
유효하다.

## `uservec`: user 상태 저장

`uservec`은 아직 user page table에서 실행된다. 현재 repo는 모든 process의 user page table에 `TRAPFRAME`
가상 주소를 같은 위치로 매핑해 둔다. 단, 그 가상 주소가 가리키는 physical page는 process마다 다르다.

강의에서는 `sscratch`에 trapframe pointer가 들어 있다고 설명하지만, 현재 repo는 고정된 `TRAPFRAME` virtual
address를 사용한다. 여기서 `sscratch`는 user `a0`를 잃지 않기 위한 임시 저장소다.

```asm
uservec:
        # user a0도 저장해야 하므로 sscratch에 잠시 보관한다.
        csrw sscratch, a0
        li a0, TRAPFRAME

        # user register들을 trapframe에 저장한다.
        sd ra, 40(a0)
        sd sp, 48(a0)
        sd gp, 56(a0)
        sd tp, 64(a0)
        sd a1, 120(a0)
        sd a7, 168(a0)

        # 마지막으로 원래 user a0도 저장한다.
        csrr t0, sscratch
        sd t0, 112(a0)
```

여기서는 register들을 먼저 저장한다. user PC는 hardware가 `sepc`에 저장해 두었고, 조금 뒤 `usertrap()`에서
`trapframe->epc`로 옮긴다.

## `uservec`: kernel 실행 준비

user register를 저장한 뒤에는 trapframe 앞부분에 미리 준비된 kernel 진입 정보를 읽는다.

```asm
        ld sp, 8(a0)     # process kernel stack
        ld tp, 32(a0)    # hart id
        ld t0, 16(a0)    # usertrap()
        ld t1, 0(a0)     # kernel page table

        sfence.vma zero, zero
        csrw satp, t1
        sfence.vma zero, zero

        jalr t0          # usertrap() 호출
```

이 `sp`는 기존 kernel register를 복구하는 것이 아니라, 이 trap을 처리할 process의 kernel stack top을 새로
로드하는 것이다. `satp`를 kernel page table로 바꾼 뒤에는 kernel 주소 공간에서 C 코드인 `usertrap()`을 실행할 수
있다.

## `usertrap`: trap 원인 처리

`usertrap()`은 이제 kernel page table과 kernel stack에서 실행된다. 핵심은 `sepc`에 있던 user PC를 저장하고,
`scause`를 보고 syscall, device interrupt, page fault 등을 분기 처리하는 것이다.

아래 코드는 핵심 흐름만 줄인 것이다.

```c++
uint64
usertrap(void)
{
  int which_dev = 0;

  if ((r_sstatus() & SSTATUS_SPP) != 0)
    panic("usertrap: not from user mode");

  // kernel 안에서 발생하는 trap은 kernelvec으로 받는다.
  w_stvec((uint64)kernelvec);

  struct proc *p = myproc();

  // user PC를 trapframe에 저장한다.
  p->trapframe->epc = r_sepc();

  if (r_scause() == 8) {
    // syscall은 ecall 다음 instruction으로 돌아가야 한다.
    p->trapframe->epc += 4;
    intr_on();
    syscall();
  } else if ((which_dev = devintr()) != 0) {
    // device interrupt
  } else if ((r_scause() == 15 || r_scause() == 13) &&
             vmfault(p->pagetable, r_stval(), (r_scause() == 13) ? 1 : 0) != 0) {
    // page fault 처리
  } else {
    setkilled(p);
  }

  if (which_dev == 2)
    yield();

  prepare_return();
  return MAKE_SATP(p->pagetable);
}
```

timer interrupt면 `yield()`를 호출할 수 있고, 이 경우 scheduler를 거쳐 나중에 다시 돌아올 수 있다.

## `prepare_return`: 다음 trap과 이번 return 준비

강의의 `usertrapret()`에 해당하는 준비 작업은 현재 repo의 `prepare_return()`에 있다.

```c++
void
prepare_return(void)
{
  struct proc *p = myproc();

  // stvec과 sstatus/sepc를 바꾸는 동안 interrupt를 받지 않게 한다.
  intr_off();

  // user mode에서 다음 trap이 나면 다시 uservec으로 들어오게 한다.
  uint64 trampoline_uservec = TRAMPOLINE + (uservec - trampoline);
  w_stvec(trampoline_uservec);

  // 다음 uservec 진입 때 필요한 kernel 상태를 trapframe에 저장한다.
  p->trapframe->kernel_satp = r_satp();
  p->trapframe->kernel_sp = p->kstack + PGSIZE;
  p->trapframe->kernel_trap = (uint64)usertrap;
  p->trapframe->kernel_hartid = r_tp();

  // sret가 user mode와 user PC로 돌아가게 한다.
  unsigned long x = r_sstatus();
  x &= ~SSTATUS_SPP;
  x |= SSTATUS_SPIE;
  w_sstatus(x);
  w_sepc(p->trapframe->epc);
}
```

여기서 저장하는 `kernel_*` 값들은 지금 당장 복귀용이 아니라, 다음번 user trap entry에서 `uservec`이 다시 사용할
값들이다. `sstatus`와 `sepc`는 곧 실행할 `sret`가 사용할 값이다.

## `userret`: user 상태 복원

`usertrap()`이 반환한 값은 `a0`에 담긴 user page table용 `satp` 값이다. `userret`은 먼저 user page table로
전환한 뒤, `TRAPFRAME`에서 user register를 복원한다.

```asm
userret:
        sfence.vma zero, zero
        csrw satp, a0
        sfence.vma zero, zero

        li a0, TRAPFRAME

        ld ra, 40(a0)
        ld sp, 48(a0)
        ld gp, 56(a0)
        ld tp, 64(a0)
        ld a1, 120(a0)
        ld a7, 168(a0)

        ld a0, 112(a0)

        sret
```

`sret`는 `prepare_return()`이 설정한 `sepc`와 `sstatus`를 사용한다. 결과적으로 PC는 trap 이전 user code 위치로
돌아가고, privilege mode도 user mode로 바뀐다.

## trapframe 역할

`trapframe`은 두 종류의 값을 함께 담는다.

- user 상태: user register들과 user PC(`epc`)
- kernel 진입 정보: 다음 trap 때 `uservec`이 로드할 `kernel_satp`, `kernel_sp`, `kernel_trap`, `kernel_hartid`

정리하면 trampoline은 user page table과 kernel page table 사이를 건너는 짧은 공통 코드이고, trapframe은 그
과정에서 user 상태와 다음 kernel 진입 정보를 보관하는 process별 저장소다.

코드 확인 위치:
`kernel/trampoline.S`, `kernel/trap.c`, `kernel/proc.h`
