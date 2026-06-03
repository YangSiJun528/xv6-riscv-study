# xv6 Kernel 27 코드 정리: PLIC

## 읽는 순서

- `docs/reference/scripts/ko/27-xv6-kernel-27-plic-platform-level-interrupt-controller-jK3TLK4CpWk.md`
- `kernel/memlayout.h`: PLIC, UART, VirtIO MMIO 주소와 IRQ 번호
- `kernel/plic.c`: PLIC priority, enable, threshold, claim, complete
- `kernel/trap.c`: supervisor external interrupt dispatch
- `kernel/uart.c`: UART interrupt handler
- `kernel/virtio_disk.c`: VirtIO disk completion interrupt handler
- `kernel/start.c`: external interrupt와 timer interrupt enable

## 핵심

PLIC(Platform-Level Interrupt Controller)은 UART, VirtIO disk 같은 외부 장치 interrupt를 hart의 supervisor external
interrupt로 전달하는 장치다. xv6는 PLIC 자체도 MMIO register block으로 보고, 정해진 주소에 load/store해서
초기화하고 interrupt를 claim/complete한다.

```text
UART / VirtIO
    |
    v
PLIC
    |
    v
hart의 supervisor external interrupt
    |
    v
trap.c: devintr()
```

현재 repo 기준으로 PLIC이 처리하는 장치는 두 개다.

```c++
#define UART0_IRQ   10
#define VIRTIO0_IRQ 1
```

IRQ 번호 0은 "claim할 장치가 없음"을 뜻하는 값으로 쓰이고, 실제 장치 interrupt source는 1번부터 시작한다.

timer interrupt는 PLIC을 거치지 않는다. `devintr()`에서 external interrupt는
`scause == 0x8000000000000009L`, timer interrupt는 `scause == 0x8000000000000005L`로 별도 처리된다.

## PLIC 내부 모델

스크립트가 강조하는 PLIC의 핵심은 외부 interrupt source와 hart target 사이의 중재다.

```text
device interrupt line
-> gateway
-> source interrupt pending
-> enable / priority / threshold check
-> hart의 supervisor external interrupt pending
-> claim / complete
```

장치 신호는 먼저 gateway에 들어오고, gateway는 해당 interrupt source가 pending이라는 상태를 기억한다. PLIC은 그
source가 target에 enable되어 있고, source priority가 target threshold보다 높을 때 그 target에 external interrupt를
알린다.

여기서 target은 단순한 "core"보다 정확히는 hart와 privilege mode의 조합이다. 스크립트는 일반 PLIC이 machine mode와
supervisor mode target을 모두 가질 수 있다고 설명하지만, 현재 xv6는 S-mode target만 사용한다. 그래서
`memlayout.h`의 실제 매크로도 `PLIC_SENABLE`, `PLIC_SPRIORITY`, `PLIC_SCLAIM`만 정의되어 있다.

PLIC은 interrupt가 필요하다는 사실과 IRQ 번호를 관리할 뿐이다. 실제 입력 byte, disk 완료 항목, device acknowledge는
UART FIFO/register나 VirtIO ring/MMIO register 같은 장치 쪽 상태에 남아 있고, handler가 그것을 읽고 정리한다.

## PLIC MMIO 주소

`memlayout.h`는 QEMU `virt` machine의 PLIC 시작 주소와 supervisor-mode target용 register 주소 계산식을 정의한다.

```c++
#define PLIC                 0x0c000000L
#define PLIC_PRIORITY        (PLIC + 0x0)
#define PLIC_PENDING         (PLIC + 0x1000)
#define PLIC_SENABLE(hart)   (PLIC + 0x2080 + (hart) * 0x100)
#define PLIC_SPRIORITY(hart) (PLIC + 0x201000 + (hart) * 0x2000)
#define PLIC_SCLAIM(hart)    (PLIC + 0x201004 + (hart) * 0x2000)
```

`vm.c`는 PLIC range를 kernel page table에 직접 매핑한다.

```c++
kvmmap(kpgtbl, PLIC, PLIC, 0x4000000, PTE_R | PTE_W);
```

`0x4000000`은 64MB다. 스크립트 말미의 "4MB만 매핑했다"는 언급은 현재 source와는 맞지 않는다.

## `plicinit()`: 장치 priority 켜기

`plicinit()`은 boot CPU가 한 번 호출한다. xv6는 UART와 VirtIO disk interrupt priority를 `1`로 둔다.
PLIC priority가 0이면 그 interrupt는 사실상 disabled이므로, non-zero 값으로 만든다.

```c++
void
plicinit(void)
{
  *(uint32 *)(PLIC + UART0_IRQ * 4) = 1;
  *(uint32 *)(PLIC + VIRTIO0_IRQ * 4) = 1;
}
```

## `plicinithart()`: hart별 enable과 threshold

`plicinithart()`는 각 hart에서 호출된다. 현재 hart의 S-mode target에 대해 UART와 VirtIO IRQ enable bit를 켜고,
priority threshold를 0으로 둔다.

```c++
void
plicinithart(void)
{
  int hart = cpuid();

  *(uint32 *)PLIC_SENABLE(hart) = (1 << UART0_IRQ) | (1 << VIRTIO0_IRQ);
  *(uint32 *)PLIC_SPRIORITY(hart) = 0;
}
```

threshold가 0이고 두 장치 priority가 1이므로, 두 장치 interrupt는 이 hart로 전달될 수 있다. `main.c`는 CPU 0에서
`plicinit()`과 `plicinithart()`를 호출하고, 나머지 hart에서는 `plicinithart()`만 호출한다.

xv6는 두 장치를 모든 hart의 S-mode target에 enable한다. 따라서 여러 hart가 같은 external interrupt pending 알림을
받을 수 있지만, 실제 IRQ를 가져가는 hart는 claim 경쟁에서 이긴 하나다.

## claim / complete

장치가 interrupt를 올리면 PLIC은 enable된 hart에 external interrupt를 알린다. trap handler는 먼저 claim
register를 읽어 어떤 IRQ를 처리할지 얻는다.

```c++
int
plic_claim(void)
{
  int hart = cpuid();
  int irq = *(uint32 *)PLIC_SCLAIM(hart);
  return irq;
}
```

여러 hart가 같은 interrupt를 볼 수 있어도, 실제로 claim해서 non-zero IRQ를 받는 hart는 하나다. 다른 hart가 먼저
claim했다면 이 hart의 claim read는 0을 돌려줄 수 있다. 여러 interrupt가 pending이면 PLIC은 target의 enable,
priority, threshold 조건에 맞는 interrupt 중 하나를 돌려준다.

처리가 끝나면 같은 claim/complete register에 IRQ 번호를 write한다.

```c++
void
plic_complete(int irq)
{
  int hart = cpuid();
  *(uint32 *)PLIC_SCLAIM(hart) = irq;
}
```

`trap.c` 주석처럼 PLIC은 한 장치가 한 번에 하나의 interrupt만 올리게 하므로, complete를 해야 그 장치가 다음
interrupt를 다시 올릴 수 있다.

## level-triggered와 edge-triggered

스크립트는 PLIC gateway가 장치 신호를 기억하는 방식도 구분한다.

```text
level-triggered:
  interrupt line이 high인 동안 "서비스 필요" 상태
  complete 후에도 line이 high이면 다시 pending 가능

edge-triggered:
  low -> high 전이를 event로 기록
  단일 pending bit 구현이면 처리 중 추가 edge는 합쳐질 수 있음
  counter gateway 구현이면 edge 수를 셀 수도 있음
```

현재 xv6 코드는 level/edge 방식을 직접 설정하지 않는다. xv6가 하는 일은 QEMU `virt` machine의 PLIC MMIO register를
초기화하고, trap이 오면 claim/complete를 수행하는 것이다. 그래서 driver는 interrupt 한 번에 장치 쪽 상태를 가능한 만큼
비운다. `uartintr()`는 receive byte가 없을 때까지 읽고, `virtio_disk_intr()`는 used ring의 완료 항목을 끝까지 훑는다.

## external interrupt 처리 흐름

`start.c`는 supervisor external interrupt와 supervisor timer interrupt를 모두 켠다.

```c++
w_sie(r_sie() | SIE_SEIE | SIE_STIE);
```

외부 장치 interrupt가 들어오면 `devintr()`는 `scause == 0x8000000000000009L`인지 확인한다. 이 값은 supervisor
external interrupt다.

```c++
if (scause == 0x8000000000000009L) {
  int irq = plic_claim();

  if (irq == UART0_IRQ) {
    uartintr();
  } else if (irq == VIRTIO0_IRQ) {
    virtio_disk_intr();
  } else if (irq) {
    printf("unexpected interrupt irq=%d\n", irq);
  }

  if (irq)
    plic_complete(irq);

  return 1;
}
```

정리하면 다음 순서다.

```text
device raises interrupt
-> PLIC routes it to an enabled S-mode target
-> trap enters kernel
-> devintr()
-> plic_claim()
-> uartintr() or virtio_disk_intr()
-> plic_complete(irq)
```

## UART interrupt

UART interrupt handler는 두 방향을 모두 처리한다. 먼저 `ISR`를 읽어 UART interrupt를 acknowledge하고, transmit 쪽이
idle이면 `uartwrite()`에서 자던 writer를 깨운다. 그 다음 receive 쪽에 준비된 byte를 모두 읽어 `consoleintr(c)`로
넘긴다.

```c++
void
uartintr(void)
{
  ReadReg(ISR);

  acquire(&tx_lock);
  if (ReadReg(LSR) & LSR_TX_IDLE) {
    tx_busy = 0;
    wakeup(&tx_chan);
  }
  release(&tx_lock);

  while (1) {
    int c = uartgetc();
    if (c == -1)
      break;
    consoleintr(c);
  }
}
```

여기서 PLIC은 "UART가 interrupt를 냈다"는 사실만 알려 준다. 실제 UART register를 읽고 쓰는 일은 `uart.c`가 한다.

## VirtIO disk interrupt

`virtio_disk_rw()`는 요청 descriptor를 avail ring에 넣고 장치에 알린 뒤, buffer의 `b->disk`가 0이 될 때까지 잔다.
disk가 요청을 끝내면 VirtIO interrupt가 PLIC을 거쳐 `virtio_disk_intr()`로 들어온다.

```c++
while (b->disk == 1) {
  sleep(b, &disk.vdisk_lock);
}
```

interrupt handler는 먼저 VirtIO 장치의 interrupt status를 acknowledge한다.

```c++
*R(VIRTIO_MMIO_INTERRUPT_ACK) = *R(VIRTIO_MMIO_INTERRUPT_STATUS) & 0x3;
```

그 다음 used ring을 훑으면서 완료된 buffer를 깨운다.

```c++
while (disk.used_idx != disk.used->idx) {
  int id = disk.used->ring[disk.used_idx % NUM].id;

  struct buf *b = disk.info[id].b;
  b->disk = 0;
  wakeup(b);

  disk.used_idx += 1;
}
```

즉 PLIC complete는 PLIC에게 끝났다고 알리는 것이고, VirtIO interrupt acknowledge는 VirtIO 장치에게 interrupt를
봤다고 알리는 별도 동작이다.

## CLINT / timer interrupt와의 차이

강의에서 비교하는 핵심은 "외부 장치 interrupt는 PLIC, timer interrupt는 별도 timer 경로"라는 점이다.

현재 source에서는 `memlayout.h` 주석에 CLINT 주소가 남아 있지만, timer interrupt를 CLINT MMIO로 직접 다루지는 않는다.
`start.c`가 Sstc의 `stimecmp`를 설정하고, `trap.c`의 `clockintr()`가 다음 timer interrupt 시점을 다시 쓴다.

```c++
void
timerinit()
{
  w_menvcfg(r_menvcfg() | (1L << 63));
  w_mcounteren(r_mcounteren() | 2);
  w_stimecmp(r_time() + 1000000);
}
```

```c++
} else if (scause == 0x8000000000000005L) {
  clockintr();
  return 2;
}
```

timer interrupt에는 `plic_claim()`이나 `plic_complete()`가 없다. `clockintr()`는 CPU 0에서만 `ticks`를 증가시키고,
마지막에 `w_stimecmp(r_time() + 1000000)`로 다음 interrupt를 예약한다.

```c++
void
clockintr()
{
  if (cpuid() == 0) {
    acquire(&tickslock);
    ticks++;
    wakeup(&ticks);
    release(&tickslock);
  }

  w_stimecmp(r_time() + 1000000);
}
```

그래서 xv6 interrupt 경로는 이렇게 나뉜다.

```text
UART / VirtIO interrupt:
  PLIC -> supervisor external interrupt -> devintr() -> plic_claim/complete

timer interrupt:
  stimecmp/time -> supervisor timer interrupt -> devintr() -> clockintr()
```
