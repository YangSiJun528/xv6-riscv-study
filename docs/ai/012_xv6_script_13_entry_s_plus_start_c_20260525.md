# xv6 Kernel 13 코드 정리: entry.S + start.c

## 읽는 순서

- `docs/reference/scripts/ko/13-xv6-kernel-13-entry.s-plus-start.c-GGY_1efenvs.md`
- `kernel/entry.S`: 처음 실행되는 assembly
- `kernel/start.c`: machine mode에서 supervisor mode로 내려가기

## 핵심

이 강의는 커널이 처음 CPU를 잡은 뒤 C 코드로 넘어가는 흐름이다.

각 hart가 같은 부팅 경로를 지나므로, 멀티코어를 전제로 읽어야 한다.

```text
QEMU -> _entry -> start() -> mret -> main()
```

## `_entry`

QEMU는 각 hart를 `0x80000000`으로 보낸다. 이 위치에는 linker script 때문에 `_entry`가 놓인다. C
함수를 호출하려면 먼저 stack이 필요하므로, `_entry`는 hart별 stack을 고르고 `start()`로 넘어간다.

```asm
.section .text
.global _entry
_entry:
        # QEMU는 kernel을 0x80000000에 load하고 각 hart를 여기로 보낸다.
        # kernel.ld가 _entry를 0x80000000에 배치한다.

        # C 코드를 호출하려면 먼저 stack pointer가 필요하다.
        # stack0는 start.c에 있고, hart마다 4096 byte씩 나눠 쓴다.
        #
        # sp = stack0 + ((mhartid + 1) * 4096)
        # stack은 아래로 자라므로 hart 0은 stack0 + 4096에서 시작한다.
        la sp, stack0
        li a0, 1024*4
        csrr a1, mhartid
        addi a1, a1, 1
        mul a0, a0, a1
        add sp, sp, a0

        # stack이 준비됐으므로 machine mode C 코드로 간다.
        call start

spin:
        # start()는 돌아오면 안 된다.
        j spin
```

## `start()`

`start()`는 machine mode에서 실행된다. 여기서 supervisor mode로 내려갈 상태를 만든 뒤, `mret`으로
`main()`에 진입한다.

```c++
void main();
void timerinit();

// entry.S가 hart별 stack으로 사용한다.
// NCPU개 hart에 대해 각각 4096 byte stack을 둔다.
__attribute__((aligned(16))) char stack0[4096 * NCPU];

void
start()
{
  // mret 이후 privilege mode가 supervisor mode가 되도록 만든다.
  // 실제로 "이전 실행"이 있었던 것은 아니고, mret가 참고할 상태를 미리 만드는 것이다.
  unsigned long x = r_mstatus();
  x &= ~MSTATUS_MPP_MASK;
  x |= MSTATUS_MPP_S;
  w_mstatus(x);

  // mret가 돌아갈 PC를 main으로 설정한다.
  w_mepc((uint64)main);

  // 아직 kernel page table을 켜지 않는다.
  // paging은 main() 이후 kvminithart()에서 켠다.
  w_satp(0);

  // exception/interrupt를 supervisor mode로 위임한다.
  // 이후 대부분의 trap은 machine mode가 아니라 supervisor mode kernel이 처리한다.
  w_medeleg(0xffff);
  w_mideleg(0xffff);
  w_sie(r_sie() | SIE_SEIE | SIE_STIE);

  // supervisor mode가 physical memory에 접근할 수 있게 PMP를 열어 둔다.
  w_pmpaddr0(0x3fffffffffffffull);
  w_pmpcfg0(0xf);

  // timer interrupt를 준비한다.
  timerinit();

  // xv6는 tp register에 hart id를 저장해 cpuid()에서 사용한다.
  int id = r_mhartid();
  w_tp(id);

  // supervisor mode로 내려가 main()부터 실행한다.
  asm volatile("mret");
}
```

## `timerinit()`

강의는 machine timer interrupt가 `timervec`을 거쳐 supervisor software interrupt를 만드는 흐름도
설명한다. 현재 repo는 그 방식이 아니라 Sstc를 사용한다. 그래서 현재 코드에는 `timervec`/`timer_scratch`
초기화가 없고, supervisor timer compare인 `stimecmp`를 바로 준비한다.

```c++
void
timerinit()
{
  // Sstc extension을 켜서 supervisor mode에서 stimecmp를 쓸 수 있게 한다.
  w_menvcfg(r_menvcfg() | (1L << 63));

  // supervisor mode가 time/stimecmp를 사용할 수 있게 허용한다.
  w_mcounteren(r_mcounteren() | 2);

  // 첫 timer interrupt를 예약한다.
  // 이후 interrupt 처리 쪽에서 다음 tick을 다시 예약한다.
  w_stimecmp(r_time() + 1000000);
}
```

## 현재 repo의 timer 차이

강의의 timer 설명은 xv6의 이전 구현을 기준으로 이해하면 된다. 현재 repo의 time slicing은
machine-level `timervec` 없이 supervisor timer interrupt로 처리된다.

코드 확인 위치:
`kernel/entry.S`, `kernel/start.c`, `kernel/trap.c`
