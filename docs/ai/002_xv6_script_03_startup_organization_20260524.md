# xv6 Kernel 3 코드 정리: Startup + Organization

## 읽는 순서

- `kernel/entry.S`: QEMU가 점프하는 첫 커널 코드
- `kernel/start.c`: machine mode에서 supervisor mode로 전환
- `kernel/main.c`: CPU 0 초기화와 나머지 CPU 합류
- `kernel/types.h`, `kernel/param.h`: 기본 타입과 고정 한계값

## `kernel/entry.S`

```asm
.section .text
.global _entry
_entry:
        # QEMU는 각 hart를 0x80000000의 _entry로 보낸다.
        # C 코드를 부르려면 먼저 hart별 stack이 필요하다.
        #
        # sp = stack0 + ((mhartid + 1) * 4096)
        # stack은 아래로 자라므로 hart마다 4KB 구간이 분리된다.
        la sp, stack0
        li a0, 1024*4
        csrr a1, mhartid
        addi a1, a1, 1
        mul a0, a0, a1
        add sp, sp, a0

        # 최소 실행 환경을 만들었으니 C 초기화 코드로 간다.
        call start

spin:
        # start()는 돌아오면 안 된다.
        j spin
```

## `kernel/start.c`

```c++
void main();
void timerinit();

// entry.S가 hart별 stack으로 사용한다.
__attribute__((aligned(16))) char stack0[4096 * NCPU];

void
start()
{
  // mret 이후 privilege mode를 supervisor로 만든다.
  unsigned long x = r_mstatus();
  x &= ~MSTATUS_MPP_MASK;
  x |= MSTATUS_MPP_S;
  w_mstatus(x);

  // mret가 main()으로 점프하게 한다.
  w_mepc((uint64)main);

  // paging은 main()의 kvminithart()에서 켠다.
  w_satp(0);

  // trap/interrupt를 supervisor mode로 위임한다.
  w_medeleg(0xffff);
  w_mideleg(0xffff);
  w_sie(r_sie() | SIE_SEIE | SIE_STIE);

  // supervisor mode가 물리 메모리에 접근할 수 있게 한다.
  w_pmpaddr0(0x3fffffffffffffull);
  w_pmpcfg0(0xf);

  // timer interrupt와 현재 hart id를 준비한다.
  timerinit();
  w_tp(r_mhartid());

  // supervisor mode로 내려가 main()을 실행한다.
  asm volatile("mret");
}

void
timerinit()
{
  // supervisor timer compare 사용을 허용하고 첫 tick을 예약한다.
  w_menvcfg(r_menvcfg() | (1L << 63));
  w_mcounteren(r_mcounteren() | 2);
  w_stimecmp(r_time() + 1000000);
}
```

## `kernel/main.c`

```c++
// CPU 0이 전역 초기화를 끝냈는지 알리는 공유 플래그.
volatile static int started = 0;

void
main()
{
  if (cpuid() == 0) {
    // CPU 0만 전역 커널 자료구조를 만든다.
    consoleinit();
    printfinit();
    printf("\n");
    printf("xv6 kernel is booting\n");
    printf("\n");

    kinit();            // physical page allocator
    kvminit();          // kernel page table 생성
    kvminithart();      // 현재 CPU에서 paging 켜기
    procinit();         // process table
    trapinit();         // trap vector
    trapinithart();     // 현재 CPU trap 설정
    plicinit();         // interrupt controller
    plicinithart();     // 현재 CPU interrupt 설정
    binit();            // buffer cache
    iinit();            // inode table
    fileinit();         // file table
    virtio_disk_init(); // QEMU disk
    userinit();         // 첫 user process

    // 다른 CPU가 started를 보기 전에 초기화 결과가 먼저 보이게 한다.
    __atomic_thread_fence(__ATOMIC_SEQ_CST);
    started = 1;
  } else {
    // 나머지 CPU는 CPU 0의 전역 초기화를 기다린다.
    while (started == 0)
      ;
    __atomic_thread_fence(__ATOMIC_SEQ_CST);

    // CPU별 초기화만 수행한다.
    printf("hart %d starting\n", cpuid());
    kvminithart();
    trapinithart();
    plicinithart();
  }

  // 모든 CPU는 여기서 process 실행 루프에 들어간다.
  scheduler();
}
```

## 기본 정의

```c++
// kernel/types.h
typedef unsigned int   uint;
typedef unsigned short ushort;
typedef unsigned char  uchar;
typedef unsigned char  uint8;
typedef unsigned short uint16;
typedef unsigned int   uint32;
typedef unsigned long  uint64;
typedef uint64         pde_t;

// kernel/param.h
#define NPROC       64   // 최대 process 수
#define NCPU        8    // 최대 CPU 수
#define NOFILE      16   // process당 open file 수
#define NFILE       100  // system 전체 open file 수
#define MAXARG      32   // exec 인자 수
#define MAXPATH     128  // path 길이
#define USERSTACK   1    // user stack page 수
```

흐름은 `_entry -> start() -> main() -> scheduler()`다. CPU 0은 전역 초기화, 다른 CPU는 대기 후
CPU별 초기화만 수행한다.
