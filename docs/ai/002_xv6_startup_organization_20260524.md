# xv6 Kernel 3 코드 정리: Startup + Organization

## 읽는 순서

- `kernel/entry.S`: QEMU가 처음 점프하는 커널 진입점
- `kernel/start.c`: machine mode에서 supervisor mode로 전환
- `kernel/main.c`: 코어 0의 전역 초기화와 다른 코어들의 대기/합류
- `kernel/types.h`, `kernel/param.h`: 기본 타입과 xv6의 고정 한계값

## `kernel/entry.S`

```asm
        # QEMU는 -kernel 옵션으로 커널을 0x80000000에 올린다.
        # 그리고 각 hart, 즉 CPU/core가 그 주소로 점프하게 한다.
        # kernel.ld는 아래 코드가 정확히 0x80000000에 배치되도록 만든다.
.section .text
.global _entry
_entry:
        # C 코드를 실행하려면 먼저 스택이 필요하다.
        # stack0은 start.c에 선언되어 있고,
        # CPU마다 4096바이트짜리 스택을 하나씩 가진다.
        #
        # 계산식:
        #   sp = stack0 + ((hartid + 1) * 4096)
        #
        # hart 0은 stack0 + 4096을 스택 꼭대기로 사용한다.
        # 스택은 아래 방향으로 자라므로, 각 hart가 서로 다른 4KB 구간을 쓴다.
        la sp, stack0
        li a0, 1024*4
        csrr a1, mhartid
        addi a1, a1, 1
        mul a0, a0, a1
        add sp, sp, a0

        # 이제 C 함수 호출이 가능한 최소 환경이 되었으므로 start()로 이동한다.
        call start

spin:
        # start()가 정상적으로 돌아오면 안 된다.
        # 혹시 돌아오면 여기서 영원히 돈다.
        j spin
```

핵심은 `entry.S`가 운영체제의 “진짜 시작점”이라는 점이다. 여기서는 복잡한 초기화를 하지 않고,
각 코어가 자기 스택을 갖게 만든 뒤 `start()`로 넘긴다.

## `kernel/start.c`: supervisor mode로 넘어가기

```c
#include "types.h"
#include "param.h"
#include "memlayout.h"
#include "riscv.h"
#include "defs.h"

void main();
void timerinit();

// entry.S는 CPU마다 별도 스택이 필요하다.
// NCPU는 param.h에 정의된 최대 CPU 수이고, 각 CPU마다 4096바이트를 준다.
__attribute__((aligned(16))) char stack0[4096 * NCPU];

// entry.S가 machine mode 상태에서 stack0을 잡고 여기로 점프한다.
void
start()
{
  // mret 명령으로 돌아갈 때 이전 privilege mode를 supervisor로 설정한다.
  // 즉, 아래쪽의 asm volatile("mret") 이후에는 supervisor mode가 된다.
  unsigned long x = r_mstatus();
  x &= ~MSTATUS_MPP_MASK;
  x |= MSTATUS_MPP_S;
  w_mstatus(x);

  // mret가 점프할 주소를 main으로 설정한다.
  // gcc -mcmodel=medany가 필요하다는 원 주석은,
  // 커널이 고정된 낮은 주소에 있지 않아도 주소 계산이 가능해야 한다는 뜻이다.
  w_mepc((uint64)main);

  // 아직 페이지 테이블을 켜지 않는다.
  // main()의 kvminithart()에서 나중에 paging을 활성화한다.
  w_satp(0);

  // interrupt와 exception 처리를 supervisor mode에 위임한다.
  // 이후 대부분의 trap 처리는 supervisor mode 커널 코드가 맡는다.
  w_medeleg(0xffff);
  w_mideleg(0xffff);
  w_sie(r_sie() | SIE_SEIE | SIE_STIE);

  // Physical Memory Protection 설정:
  // supervisor mode가 전체 물리 메모리에 접근할 수 있게 한다.
  w_pmpaddr0(0x3fffffffffffffull);
  w_pmpcfg0(0xf);

  // timer interrupt가 오도록 설정한다.
  timerinit();

  // 현재 hart id를 tp 레지스터에 저장한다.
  // xv6의 cpuid()는 나중에 이 tp 값을 읽어서 현재 CPU 번호를 알아낸다.
  int id = r_mhartid();
  w_tp(id);

  // supervisor mode로 전환하고 main()으로 점프한다.
  asm volatile("mret");
}

// 각 hart가 timer interrupt를 만들도록 요청한다.
void
timerinit()
{
  // sstc 확장, 즉 stimecmp 사용을 켠다.
  w_menvcfg(r_menvcfg() | (1L << 63));

  // supervisor mode에서 stimecmp와 time을 사용할 수 있게 허용한다.
  w_mcounteren(r_mcounteren() | 2);

  // 첫 timer interrupt 시점을 예약한다.
  // 이후 timer interrupt는 scheduler가 프로세스를 선점하는 기반이 된다.
  w_stimecmp(r_time() + 1000000);
}
```

`start()`는 부팅 초기의 특권 모드 정리 코드다. xv6 커널 대부분은 machine mode가 아니라
supervisor mode에서 실행되므로, 여기서 trap 위임, 물리 메모리 접근 권한, timer interrupt, `tp`
레지스터를 준비한 뒤 `main()`으로 넘어간다.

## `kernel/main.c`: 코어 0과 나머지 코어의 역할 분리

```c
#include "types.h"
#include "param.h"
#include "memlayout.h"
#include "riscv.h"
#include "defs.h"

// 모든 CPU가 공유하는 시작 플래그다.
// volatile은 컴파일러가 이 값을 레지스터에만 캐시하거나
// 반복문을 임의로 최적화하지 못하게 하는 힌트다.
volatile static int started = 0;

// start()는 모든 CPU에서 supervisor mode로 전환한 뒤 main()으로 점프한다.
void
main()
{
  if (cpuid() == 0) {
    // CPU 0만 전체 시스템 초기화를 수행한다.
    consoleinit();
    printfinit();
    printf("\n");
    printf("xv6 kernel is booting\n");
    printf("\n");

    kinit();            // 물리 페이지 allocator 초기화
    kvminit();          // 커널 page table 생성
    kvminithart();      // 현재 CPU에서 paging 켜기
    procinit();         // process table 초기화
    trapinit();         // trap vector 초기화
    trapinithart();     // 현재 CPU에 kernel trap vector 설치
    plicinit();         // interrupt controller 초기화
    plicinithart();     // 현재 CPU가 device interrupt를 받게 설정
    binit();            // buffer cache 초기화
    iinit();            // inode table 초기화
    fileinit();         // file table 초기화
    virtio_disk_init(); // QEMU가 에뮬레이션하는 disk 초기화
    userinit();         // 첫 user process 생성

    // 위 초기화들이 started = 1보다 반드시 먼저 완료되게 한다.
    // 다른 CPU들은 started를 보고 출발하므로 순서가 중요하다.
    __atomic_thread_fence(__ATOMIC_SEQ_CST);
    started = 1;
  } else {
    // CPU 0이 전역 초기화를 끝낼 때까지 나머지 CPU는 기다린다.
    while (started == 0)
      ;

    // started를 본 뒤, 그 이전 초기화 결과도 확실히 보이게 한다.
    __atomic_thread_fence(__ATOMIC_SEQ_CST);

    printf("hart %d starting\n", cpuid());

    // CPU마다 따로 해야 하는 초기화만 수행한다.
    kvminithart();  // 현재 CPU에서 paging 켜기
    trapinithart(); // 현재 CPU에 kernel trap vector 설치
    plicinithart(); // 현재 CPU가 device interrupt를 받게 설정
  }

  // 모든 CPU는 마지막에 scheduler로 들어간다.
  // 여기부터 각 CPU는 실행 가능한 process를 찾아 돌린다.
  scheduler();
}
```

`main()`의 구조는 단순하다. CPU 0은 공용 커널 자료구조를 초기화하고 첫 유저 프로세스를 만든다.
다른 CPU들은 그 작업이 끝날 때까지 기다렸다가 CPU별 초기화만 수행한다. 이후 모든 CPU가
`scheduler()`에 들어가 프로세스를 실행할 준비를 한다.

## `kernel/types.h`: xv6가 쓰는 기본 정수 타입

```c
typedef unsigned int uint;
typedef unsigned short ushort;
typedef unsigned char uchar;

typedef unsigned char uint8;     // 8비트 부호 없는 정수
typedef unsigned short uint16;   // 16비트 부호 없는 정수
typedef unsigned int uint32;     // 32비트 부호 없는 정수
typedef unsigned long uint64;    // 64비트 부호 없는 정수, 주소/포인터에도 자주 사용

typedef uint64 pde_t;            // page directory entry 타입 이름
```

## `kernel/param.h`: xv6의 고정 한계값

```c
#define NPROC       64                // 최대 process 수
#define NCPU        8                 // 최대 CPU 수
#define NOFILE      16                // process 하나가 열 수 있는 file 수
#define NFILE       100               // system 전체 open file 수
#define NINODE      50                // 활성 i-node 최대 개수
#define NDEV        10                // 최대 major device 번호
#define ROOTDEV     1                 // root file system disk의 device 번호
#define MAXARG      32                // exec 인자 최대 개수
#define MAXOPBLOCKS 10                // file system 작업 하나가 쓰는 block 최대 개수
#define LOGBLOCKS   (MAXOPBLOCKS * 3) // on-disk log의 data block 최대 개수
#define NBUF        (MAXOPBLOCKS * 3) // disk block cache 크기
#define FSSIZE      2000              // file system 크기, block 단위
#define MAXPATH     128               // file path 문자열 최대 길이
#define USERSTACK   1                 // user stack page 수
```

`param.h`를 보면 xv6의 설계 의도가 잘 보인다. 동적으로 크기를 늘리는 복잡한 구조보다는, 작은 고정
상수와 배열을 사용해 커널 전체를 이해하기 쉽게 만든다.
