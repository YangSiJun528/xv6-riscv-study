# xv6 Kernel 18 코드 정리: uart.c and console.c

## 읽는 순서

- `docs/reference/scripts/ko/18-xv6-kernel-18-uart.c-and-console.c-ec8A0u6SjDg.md`
- `kernel/uart.c`: UART MMIO driver
- `kernel/console.c`: console read/write, line editing
- `kernel/file.c`: device file read/write dispatch
- `kernel/trap.c`: UART interrupt가 들어오는 경로

## 핵심

UART는 memory-mapped I/O 장치다. xv6는 UART register 주소에 byte를 쓰거나 읽어서 console 입출력을
처리한다.

```text
write syscall -> filewrite -> consolewrite -> uartwrite -> UART THR
UART interrupt -> uartintr -> consoleintr -> console input buffer
read syscall -> fileread -> consoleread -> user buffer
```

강의는 transmit circular buffer를 설명하지만, 현재 repo의 `uart.c`는 그 구조가 아니다. 현재 코드는
`tx_busy`와 `tx_chan`으로 "UART가 전송 중인지"를 추적하고, 전송 완료 interrupt에서 writer를 깨운다.

## UART MMIO register

```c++
#define Reg(reg) ((volatile unsigned char *)(UART0 + (reg)))

#define ReadReg(reg)     (*(Reg(reg)))
#define WriteReg(reg, v) (*(Reg(reg)) = (v))

#define RHR 0 // receive holding register
#define THR 0 // transmit holding register
#define LSR 5 // line status register
```

같은 offset 0이라도 read하면 `RHR`, write하면 `THR` 의미가 된다.

## 출력 경로

user가 console file에 write하면 `consolewrite()`가 batch 단위로 kernel buffer에 복사한 뒤 `uartwrite()`를
호출한다.

```c++
int
consolewrite(int user_src, uint64 src, int n)
{
  char buf[32];
  int i = 0;

  while (i < n) {
    int nn = sizeof(buf);
    if (nn > n - i)
      nn = n - i;
    if (either_copyin(buf, user_src, src + i, nn) == -1)
      break;
    uartwrite(buf, nn);
    i += nn;
  }
  return i;
}
```

현재 `uartwrite()`는 UART가 바쁘면 sleep하고, 전송 가능해지면 `THR`에 다음 byte를 쓴다.

```c++
void
uartwrite(char buf[], int n)
{
  acquire(&tx_lock);

  int i = 0;
  while (i < n) {
    while (tx_busy != 0) {
      sleep(&tx_chan, &tx_lock);
    }

    WriteReg(THR, buf[i]);
    i += 1;
    tx_busy = 1;
  }

  release(&tx_lock);
}
```

`printf()`나 입력 echo처럼 sleep하면 안 되는 출력은 `uartputc_sync()`를 쓴다. 이 함수는 hardware가
준비될 때까지 busy-wait한 뒤 바로 `THR`에 쓴다.

## UART interrupt

UART interrupt는 입력이 도착했거나, 출력 장치가 다음 byte를 받을 준비가 되었을 때 발생한다.

```c++
void
uartintr(void)
{
  ReadReg(ISR); // acknowledge

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

출력 쪽은 sleeping writer를 깨우고, 입력 쪽은 준비된 문자를 모두 `consoleintr()`로 넘긴다.

## console input buffer

console은 line 단위 입력을 제공한다. 내부 buffer에는 세 index가 있다.

```c++
struct {
  struct spinlock lock;
  char buf[INPUT_BUF_SIZE];
  uint r; // read index
  uint w; // write index: read 가능한 끝
  uint e; // edit index: 아직 편집 중인 끝
} cons;
```

- `r`: `consoleread()`가 읽을 위치
- `w`: 완성된 입력의 끝
- `e`: 사용자가 입력 중인 편집 위치

`Ctrl-U`, backspace 때문에 `e`가 필요하다. newline이나 `Ctrl-D`가 들어오면 `w = e`가 되고,
`consoleread()`를 깨운다.

## 입력 경로

```c++
void
consoleintr(int c)
{
  acquire(&cons.lock);

  switch (c) {
  case C('U'):
    // 현재 줄 삭제
    break;
  case C('H'):
  case '\x7f':
    // 한 글자 삭제
    break;
  default:
    consputc(c);                     // echo
    cons.buf[cons.e++ % INPUT_BUF_SIZE] = c;
    if (c == '\n' || c == C('D') || cons.e - cons.r == INPUT_BUF_SIZE) {
      cons.w = cons.e;
      wakeup(&cons.r);
    }
    break;
  }

  release(&cons.lock);
}
```

`consoleread()`는 `cons.r == cons.w`인 동안 잘 입력된 한 줄이 없다고 보고 sleep한다.

```c++
while (cons.r == cons.w) {
  if (killed(myproc())) {
    release(&cons.lock);
    return -1;
  }
  sleep(&cons.r, &cons.lock);
}
```

## 정리

```text
UART:
  hardware register를 MMIO로 읽고 쓴다.

uartwrite:
  user write path. UART가 바쁘면 sleep한다.

uartputc_sync:
  printf/echo path. sleep하지 않고 busy-wait한다.

consoleintr:
  interrupt input path. line editing 후 완성된 입력을 깨운다.

consoleread:
  read syscall path. 완성된 line이 생길 때까지 sleep한다.
```

코드 확인 위치:
`kernel/uart.c`, `kernel/console.c`, `kernel/file.c`, `kernel/trap.c`
