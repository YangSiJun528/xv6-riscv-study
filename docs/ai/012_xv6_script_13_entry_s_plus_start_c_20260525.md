# xv6 Kernel 13 코드 정리: entry.S + start.c

## 읽는 순서

- `docs/reference/scripts/ko/13-xv6-kernel-13-entry.s-plus-start.c-GGY_1efenvs.md`
- `kernel/entry.S`: 처음 실행되는 assembly
- `kernel/start.c`: machine mode에서 supervisor mode로 내려가기
- `kernel/main.c`: supervisor mode 초기화 시작점

## 핵심

이 강의는 커널이 처음 CPU를 잡은 뒤 C 코드로 넘어가는 흐름이다.

```text
QEMU -> _entry -> start() -> mret -> main()
```

## `_entry`

QEMU는 각 hart를 `0x80000000`으로 보낸다. 이 위치에는 linker script 때문에 `_entry`가 놓인다.

`entry.S`가 하는 일은 최소한이다.

- `mhartid`로 현재 hart 번호를 읽는다.
- `stack0` 안에서 hart별 4KB stack을 고른다.
- `sp`를 설정한다.
- `start()`를 호출한다.

C 함수를 호출하려면 먼저 stack이 필요하므로, `_entry`는 stack 준비용 코드라고 보면 된다.

## `start()`

`start()`는 machine mode에서 실행되며, supervisor mode의 `main()`으로 들어갈 준비를 한다.

- `mstatus.MPP`를 supervisor로 설정
- `mepc`를 `main`으로 설정
- `satp`를 0으로 두어 paging off
- exception/interrupt를 supervisor mode로 위임
- supervisor interrupt enable bit 준비
- PMP를 설정해 supervisor가 physical memory에 접근 가능하게 함
- timer interrupt 준비
- `tp`에 hart id 저장
- `mret` 실행

`mret`은 여기서는 "이전 실행으로 복귀"라기보다, 미리 만들어 둔 상태로 `main()`에 진입하는 장치다.

## 현재 repo의 timer 차이

강의는 machine timer interrupt가 `timervec`을 거쳐 supervisor software interrupt를 만드는 흐름을
설명한다.

현재 repo는 그 방식이 아니다. `start.c`의 `timerinit()`은 Sstc를 켜고 `stimecmp`를 설정한다. 즉
time slicing의 timer interrupt는 machine-level `timervec` 없이 supervisor timer interrupt로
처리된다.

코드 확인 위치:
`kernel/entry.S`, `kernel/start.c`, `kernel/main.c`, `kernel/trap.c`
