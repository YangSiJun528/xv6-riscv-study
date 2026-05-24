# xv6 Kernel 10 코드 정리: Context Switching

## 읽는 순서

- `docs/reference/scripts/ko/10-xv6-kernel-10-context-switching-1sSanF_y8FY.md`
- `kernel/proc.h`: `trapframe`, `context`, process/CPU state
- `kernel/proc.c`: scheduler, yield, sched
- `kernel/swtch.S`: kernel context switch
- `kernel/trampoline.S`, `kernel/trap.c`: user/kernel trap return

## 핵심 그림

사용자 프로그램은 계속 실행되는 것처럼 보이지만, 실제로는 timer interrupt, syscall, device interrupt,
exception 때문에 user/kernel 경계를 자주 넘는다.

```text
user 실행
  -> trap으로 kernel 진입
  -> kernel 처리
  -> 같은 process로 돌아가거나
  -> scheduler를 거쳐 다른 process로 돌아감
```

따라서 context switching을 이해하려면 두 종류의 전환을 구분해야 한다.

## 1. user/kernel 전환

trap과 `sret`으로 일어나는 전환이다.

- user mode에서 kernel mode로 들어온다.
- user register 전체와 user PC를 `trapframe`에 저장한다.
- 처리가 끝나면 `trapframe`에서 register를 복원하고 `sret`로 user mode에 돌아간다.
- user program 입장에서는 interrupt가 끼어들었더라도 중단된 지점에서 계속 실행되는 것처럼 보인다.

즉 `trapframe`은 user 실행 상태 저장소다.

## 2. kernel 내부 context switch

이미 kernel mode 안에 들어온 뒤, 현재 process의 kernel 실행 흐름에서 scheduler 실행 흐름으로 바꾸거나
반대로 돌아올 때 일어난다.

- `swtch(old, new)`가 담당한다.
- 전체 register를 저장하지 않고 `ra`, `sp`, `s0-s11`만 저장/복원한다.
- `p->context`는 process의 kernel 실행 상태다.
- `cpu->context`는 해당 CPU의 scheduler 실행 상태다.

즉 `context`는 kernel 실행 상태 저장소다. `swtch()` 입장에서는 process인지 scheduler인지 구분하지 않고,
현재 context를 저장하고 다음 context를 복원할 뿐이다.

## scheduler 관점

각 CPU는 자기 scheduler loop를 가진다. scheduler는 runnable process를 찾고, 그 process의
kernel context로 `swtch()`한다.

```text
scheduler
  -> runnable process 선택
  -> process의 kernel context로 swtch
  -> process가 user mode로 복귀
  -> timer/syscall 등으로 다시 kernel 진입
  -> yield/sleep/wait 등에서 scheduler context로 swtch
```

timer interrupt가 들어오면 현재 process는 trap을 통해 kernel에 들어온다. 이후 xv6가 CPU를 넘기기로 하면
`yield()`가 process를 `RUNNABLE`로 바꾸고 scheduler로 context switch한다.

## multi-core에서 중요한 점

process는 특정 CPU에 영구히 묶이지 않는다. 어떤 process가 CPU 0에서 실행되다가 나중에 CPU 1에서 다시
실행될 수 있다.

process의 실행 상태는 shared memory 안의 `trapframe`과 `context`에 저장된다. 여러 CPU가 같은 process를
동시에 건드리면 안 되므로 process state와 context switch 주변은 lock으로 보호된다.

## 정리

```text
일반 함수 호출:
  C ABI가 register/stack 사용 규칙을 관리

user/kernel 경계:
  trapframe이 user register 전체와 user PC를 저장

kernel scheduling 경계:
  context가 kernel 실행 재개에 필요한 최소 register만 저장
```

코드 확인 위치:
`kernel/proc.h`, `kernel/proc.c`, `kernel/swtch.S`, `kernel/trap.c`, `kernel/trampoline.S`
