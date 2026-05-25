# xv6 Kernel 14 코드 정리: Trap Handling

## 읽는 순서

- `docs/reference/scripts/ko/14-xv6-kernel-14-trap-handling-k4f2vHCV5iQ.md`
- `kernel/trampoline.S`: user trap entry/return
- `kernel/trap.c`: user/kernel trap 처리
- `kernel/proc.h`: `trapframe`, `cpu`, `proc`

## 핵심

이 강의는 trap 처리의 전체 지도와, 그때 쓰이는 kernel 자료구조를 설명한다. 세부 코드를 한 줄씩 보기보다
"어떤 상태를 어디에 저장하는가"를 보는 쪽이 중요하다.

## user trap roadmap

```text
user code
  -> trap 발생
  -> hardware가 sepc/scause/sstatus 등을 설정
  -> stvec이 가리키는 uservec 실행
  -> user register를 trapframe에 저장
  -> kernel stack, kernel tp, kernel page table 준비
  -> usertrap() 실행
  -> syscall/device/page fault/timer 등 원인 처리
  -> user return 준비
  -> userret에서 user page table과 register 복원
  -> sret
  -> user code 재개
```

강의의 `usertrapret` 역할은 현재 repo에서 `prepare_return()`과 `trampoline.S`의 `userret`으로 나뉘어
있다.

## `trapframe`

`trapframe`은 user mode 상태를 저장하는 process별 공간이다.

- user register 전체 저장
- saved user PC 저장
- 다음 trap entry 때 필요한 kernel page table, kernel stack, hart id, handler 주소 저장

trap entry/return은 아직 page table을 바꾸는 중간에 실행되므로, `trapframe`과 `trampoline`이 특별한
위치에 매핑된다.

## `cpu`

`struct cpu`는 CPU별 상태다.

- 현재 CPU에서 실행 중인 `proc`
- scheduler로 돌아가기 위한 `context`
- interrupt off 중첩 상태

process는 여러 CPU 중 어디서든 다시 실행될 수 있지만, scheduler context는 CPU별로 따로 있다.

## `proc`

`struct proc`은 process 하나의 kernel-side 상태다.

- `state`: `UNUSED`, `USED`, `SLEEPING`, `RUNNABLE`, `RUNNING`, `ZOMBIE`
- `kstack`: process가 kernel mode에서 쓸 stack
- `pagetable`: user address space
- `trapframe`: user register 저장 page
- `context`: scheduler와 process kernel thread 사이의 switch 저장소

`p->lock`은 `state`, `chan`, `killed`, `xstate`, `pid` 같은 scheduling/exit 관련 상태를 보호한다.

코드 확인 위치:
`kernel/proc.h`, `kernel/trap.c`, `kernel/trampoline.S`
