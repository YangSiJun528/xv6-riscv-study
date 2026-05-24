# xv6 Kernel 1-2 요약: 소개와 기본 구조

## 출처

- `docs/reference/scripts/ko/01-xv6-kernel-1-intro-and-overview-fWUJKH0RNFE.md`
- `docs/reference/scripts/ko/02-xv6-kernel-2-general-features-yfqxa-TYdFU.md`

## 핵심

xv6는 MIT의 교육용 Unix-like 커널이다. Linux/Unix의 모든 복잡도를 담지는 않지만, 프로세스,
시스템 콜, 파일 시스템, 가상 메모리, 인터럽트, 스케줄링을 작은 코드 안에서 볼 수 있다.

강의와 코드는 RISC-V xv6 기준이다. 보통 실제 보드가 아니라 QEMU 위에서 실행하지만, 구조상으로는
공유 메모리 멀티코어 머신에서 도는 베어 메탈 커널이다.

## 제공하는 것과 뺀 것

제공:

- 프로세스, 프로세스별 가상 주소 공간, 페이지 테이블
- Unix-like 파일/디렉터리, 파이프, 기본 사용자 프로그램
- 타이머 인터럽트 기반 멀티태스킹
- `fork`, `wait`, `exit`, `exec`, `open`, `read`, `write` 등 기본 시스템 콜

생략:

- 로그인, 사용자 ID, 파일 권한
- 마운트, 여러 파일 시스템, 디스크 페이징
- 네트워크, 소켓, 풍부한 디바이스 드라이버

## 실행 환경

- `hart`: RISC-V hardware thread. xv6 학습에서는 CPU/core와 거의 같은 의미로 보면 된다.
- 주요 장치: UART, QEMU disk, timer interrupt, PLIC.
- 물리 메모리: `KERNBASE`부터 `PHYSTOP`까지, 강의 기준 128MB 고정.
- 페이지 크기: `PGSIZE` = 4096바이트.
- 페이지 테이블: RISC-V Sv39 3단계 구조.

## 사용자 주소 공간

```text
                             MAXVA +---------------------------------+
                                   | TRAMPOLINE              -kr-x   |
         TRAMPOLINE = MAXVA-PGSIZE +---------------------------------+
                                   | TRAPFRAME               -krw-   |
 TRAPFRAME = TRAMPOLINE - PGSIZE   +---------------------------------+
                                   |                                 |
                                   | unused                  -----   |
                                   |                                 |
                             p->sz +---------------------------------+
                                   | heap                    ukrw-   |
                                   |              ^                  |
                                   |              |                  |
                                   |              |                  |
                         stack_top +---------------------------------+
                                   | user stack              ukrw-   |
                                   |              |                  |
                                   |              |                  |
                                   |              v                  |
                         stackbase +---------------------------------+
                                   | guard page              -krw-   |
        PGROUNDUP(elf_end) = guard +---------------------------------+
                                   | data + bss              ukrw-   |
                                   |                                 |
                                   | text                    ukr-x   |
                              0x0  +---------------------------------+
```

권한 문자열은 `ukrwx` 순서다. 빠진 권한은 `-`로 표시한다.
예: `ukrw-`는 user/kernel 접근 가능, 읽기/쓰기 가능, 실행 불가다.

- `TRAMPOLINE`: trap 진입/복귀 코드. 모든 프로세스가 같은 물리 페이지를 공유한다.
- `TRAPFRAME`: trap 때 저장할 레지스터 공간. 프로세스마다 별도 물리 페이지를 쓴다.
- `heap`: `sbrk`로 높은 주소 방향으로 확장된다. 현재 끝은 `p->sz`다.
- `user stack`: `exec.c`가 `USERSTACK * PGSIZE` 크기로 만든다. 이는 startup 때 정해지며, growth는 제공하지 않는다.
- `guard page`: user 접근을 막아 stack overflow를 trap으로 잡는다.
- `text`, `data + bss`: ELF에서 읽어 온 사용자 코드와 데이터다.

`stack_top`과 `stackbase`는 매크로가 아니라 `exec.c`에서 계산되는 값이다. 크기와 정렬은
`USERSTACK`, `PGSIZE`, `PGROUNDUP`이 결정한다.

## 스케줄링과 동시성

xv6의 스케줄러는 모든 CPU가 공용 process table을 훑어 실행 가능한 프로세스를 찾는 단순한 구조다.
동시성은 주로 세 가지로 다룬다.

- `spinlock`: 공유 커널 자료구조 보호
- `sleep`/`wakeup`: 조건 대기와 깨우기
- interrupt off: 현재 CPU에서 인터럽트 재진입 방지

한 CPU에서 interrupt를 꺼도 다른 CPU는 계속 실행된다. 멀티코어 공유 상태에는 여전히 락이 필요하다.

## 정리

xv6는 실사용 OS가 아니라 운영체제 핵심 구조를 읽기 좋게 줄인 커널이다. 고정 크기 배열과 단순한
선형 탐색을 자주 쓰기 때문에 확장성은 낮지만, 전체 흐름을 코드로 추적하기 쉽다.
