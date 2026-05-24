# xv6 Kernel 9 코드 정리: RISC-V Trap Processing

## 읽는 순서

- `docs/reference/scripts/ko/09-xv6-kernel-9-riscv-trap-processing-hSjJ94PoLXc.md`
- `kernel/riscv.h`: trap 관련 CSR
- `kernel/trap.c`: user/kernel trap 처리
- `kernel/trampoline.S`, `kernel/kernelvec.S`: low-level entry/return
- `kernel/start.c`: delegation, timer 설정

## trap의 의미

RISC-V 문맥에서는 `trap`이 상위 개념이다.

```text
trap = exception 또는 interrupt

exception: 현재 instruction 때문에 동기적으로 발생
  예: ecall, illegal instruction, page fault, access fault

interrupt: 외부 사건 때문에 비동기적으로 발생
  예: timer, UART, virtio disk
```

trap이 발생하면 현재 실행 흐름을 잠시 멈추고 trap handler로 제어가 넘어간다.

## hardware가 해주는 일

trap 때 hardware가 모든 register를 저장해주지는 않는다. 최소한의 제어 정보만 CSR에 저장하고 handler로
jump한다.

- 현재 PC를 `sepc`에 저장
- trap 원인을 `scause`에 저장
- fault 주소 같은 부가 정보를 `stval`에 저장할 수 있음
- 이전 privilege mode를 `sstatus.SPP`에 저장
- 이전 interrupt enable 상태를 `sstatus.SPIE`에 저장
- interrupt를 끄고 supervisor mode로 전환
- `stvec`이 가리키는 handler로 jump

일반 register 저장은 xv6의 assembly entry code가 직접 한다.

## trap handler가 둘인 이유

xv6는 trap이 어디서 났는지에 따라 다른 entry를 사용한다.

- user mode에서 난 trap: `trampoline.S`의 `uservec`
- kernel mode에서 난 trap: `kernelvec.S`의 `kernelvec`

user mode에서 trap이 나면 user page table이 켜진 상태로 kernel에 들어오기 때문에 trampoline이 필요하다.
trampoline은 user page table과 kernel page table 양쪽에서 같은 virtual address에 매핑되어 있어 page
table 전환 중에도 실행될 수 있다.

> [추가]  
> trampoline = page table을 바꾸는 중에도 계속 실행될 수 있도록 
> user page table과 kernel page table의 같은 virtual address에 매핑된 코드

## user trap 흐름

```text
user code 실행
  -> syscall / interrupt / page fault
  -> hardware가 sepc/scause/sstatus 등을 설정
  -> stvec이 가리키는 uservec으로 jump
  -> user register 전체를 trapframe에 저장
  -> kernel page table로 전환
  -> usertrap() 실행
  -> 원인별 처리
  -> trapframe과 CSR을 복귀용으로 준비
  -> userret
  -> user register 복원
  -> sret로 user mode 복귀
```

syscall은 `ecall` exception이다. 이때 `sepc`는 `ecall` 명령어 주소를 가리키므로, xv6는
`trapframe->epc += 4` 해서 복귀 시 다음 instruction으로 가게 한다.

## interrupt enable과 pending

interrupt는 비동기 사건이라 interrupt enable bit의 영향을 받는다. 꺼져 있으면 즉시 handler로 가지 않고
pending 상태로 남을 수 있다. 반면 exception은 현재 instruction 실행 결과이므로 interrupt enable과
별개로 처리된다.

xv6가 spinlock을 잡을 때 interrupt를 끄는 이유도 여기와 연결된다. 같은 CPU에서 interrupt handler가
같은 lock을 다시 잡는 상황을 막기 위한 것이다.

## delegation과 timer

RISC-V는 원래 trap을 machine mode에서 처리할 수 있지만, xv6는 대부분의 exception/interrupt를
supervisor mode로 위임한다. 이를 위해 `medeleg`, `mideleg`를 초기화한다.

강의는 machine timer interrupt가 `timervec`을 거쳐 supervisor software interrupt를 만든다고 설명한다.
현재 repo는 Sstc 기반으로 supervisor timer interrupt를 직접 처리하는 경로라서 세부 구현이 다르다.
큰 구조는 같지만 timer 부분은 현재 source 기준으로 봐야 한다.

코드 확인 위치:
`kernel/riscv.h`, `kernel/start.c`, `kernel/trap.c`, `kernel/trampoline.S`, `kernel/kernelvec.S`
