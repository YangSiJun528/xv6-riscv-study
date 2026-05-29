# xv6 Kernel 18 코드 정리: UART와 console 역할 분리

## 먼저 결론

`uart.c`와 `console.c`는 둘 다 콘솔 입출력에 관여하지만, 보는 층이 다르다.

```text
user process
  read/write("console")
      |
      v
console.c  파일/device 계층 + terminal 입력 정책
      |
      v
uart.c     UART 하드웨어 register를 다루는 driver
      |
      v
UART0      MMIO register block at 0x10000000
```

- `UART0`는 버퍼가 아니라 UART 장치 register block의 시작 주소다.
- `uart.c`는 byte를 UART register에 쓰거나 UART register에서 읽는다.
- `console.c`는 그 byte stream 위에 `console` 파일, line buffering, echo, backspace, `Ctrl-D` 같은 의미를 얹는다.
- `uartwrite()`의 sleep은 이전 byte 전송이 끝날 때까지 `tx_busy`로 기다리는 것이다.
- `consoleread()`의 sleep은 완성된 입력 줄이 생길 때까지 `cons.buf`에서 기다리는 것이다.

`consoleintr()`라는 이름도 주의해야 한다. 실제 하드웨어 interrupt handler는 `uartintr()`이고,
`consoleintr(c)`는 UART에서 받은 문자 하나에 console 입력 정책을 적용하는 함수다.

## 읽는 순서

- `docs/reference/scripts/ko/18-xv6-kernel-18-uart.c-and-console.c-ec8A0u6SjDg.md`
- `kernel/uart.c`: 16550a UART MMIO driver
- `kernel/console.c`: console device, line editing, line-buffered read/write
- `kernel/file.c`: `FD_DEVICE` read/write dispatch
- `kernel/trap.c`: PLIC external interrupt에서 `uartintr()`로 들어오는 경로

## 버퍼와 sleep

버퍼가 하나만 있는 것이 아니다. 층마다 의미가 다르다.

| 구분 | 위치 | sleep 여부 |
| --- | --- | --- |
| UART 하드웨어 내부 FIFO | 16550a 장치 내부. `FCR`로 enable/clear | xv6가 FIFO 길이를 직접 관리하며 sleep하지는 않는다. |
| UART 전송 대기 상태 | `uart.c`의 `tx_busy`, `tx_chan` | `uartwrite()`가 전송 완료 interrupt를 기다리며 sleep한다. |
| console 입력 버퍼 | `console.c`의 `cons.buf`, `r/w/e` | `consoleread()`가 완성된 줄을 기다리며 sleep한다. |

현재 repo의 출력 sleep은 “software transmit circular buffer가 가득 차서”가 아니다. `uartwrite()`가 byte 하나를
`THR`에 쓴 뒤 `tx_busy = 1`로 표시하고, UART transmit interrupt가 `tx_busy = 0`으로 바꿔 줄 때까지
다음 byte를 보내는 쪽이 기다리는 구조다.

반대로 `printf()`나 입력 echo는 `uartwrite()`를 쓰지 않는다. `consputc()` -> `uartputc_sync()` 경로를 타며,
sleep하지 않고 `LSR_TX_IDLE`이 set될 때까지 busy-wait한다.

## 주석 붙여 읽는 핵심 코드

전체 코드를 다 외울 필요는 없다. 아래 몇 군데만 이해하면 나머지는 자연스럽게 읽힌다.

`uartwrite()`는 console 출력 byte를 실제 UART로 보내는 sleep 가능한 경로다.

```c++
void
uartwrite(char buf[], int n)
{
  acquire(&tx_lock);              // tx_busy를 보호한다.

  int i = 0;
  while (i < n) {
    while (tx_busy != 0) {        // 이전 byte가 아직 전송 중이면
      sleep(&tx_chan, &tx_lock);  // UART interrupt가 깨워 줄 때까지 잔다.
    }

    WriteReg(THR, buf[i]);        // offset 0에 write: UART transmit 입구에 byte를 넣는다.
    i += 1;
    tx_busy = 1;                  // 이제 UART가 이 byte를 전송 중이라고 표시한다.
  }

  release(&tx_lock);
}
```

`uartintr()`는 실제 UART interrupt handler다. 출력 쪽 writer를 깨우고, 입력 쪽 byte는 console로 넘긴다.

```c++
void
uartintr(void)
{
  ReadReg(ISR);                   // UART interrupt acknowledge

  acquire(&tx_lock);
  if (ReadReg(LSR) & LSR_TX_IDLE) {
    tx_busy = 0;                  // THR이 다음 byte를 받을 수 있는 상태
    wakeup(&tx_chan);             // uartwrite()에서 잔 writer를 깨운다.
  }
  release(&tx_lock);

  while (1) {
    int c = uartgetc();           // RHR에서 준비된 입력 byte를 하나 꺼낸다.
    if (c == -1)
      break;                      // 더 이상 읽을 입력이 없음
    consoleintr(c);               // byte 해석은 console 계층에 맡긴다.
  }
}
```

`consoleintr()`는 입력 문자 하나를 line discipline에 맞게 처리한다.

```c++
void
consoleintr(int c)
{
  acquire(&cons.lock);            // cons.buf, r, w, e를 보호한다.

  switch (c) {
  case C('U'):                    // Ctrl-U: 현재 편집 중인 줄 삭제
    while (cons.e != cons.w &&
           cons.buf[(cons.e - 1) % INPUT_BUF_SIZE] != '\n') {
      cons.e--;                   // 아직 read에 공개되지 않은 edit 영역만 되돌린다.
      consputc(BACKSPACE);        // 화면에서도 지워 보이게 echo한다.
    }
    break;

  case C('H'):
  case '\x7f':                    // Backspace/Delete: 한 글자 삭제
    if (cons.e != cons.w) {
      cons.e--;
      consputc(BACKSPACE);
    }
    break;

  default:
    if (c != 0 && cons.e - cons.r < INPUT_BUF_SIZE) {
      c = (c == '\r') ? '\n' : c; // carriage return은 newline으로 정규화한다.
      consputc(c);                // 입력 echo
      cons.buf[cons.e++ % INPUT_BUF_SIZE] = c;

      if (c == '\n' || c == C('D') || cons.e - cons.r == INPUT_BUF_SIZE) {
        cons.w = cons.e;          // 여기까지는 read가 가져가도 되는 입력이 됐다.
        wakeup(&cons.r);          // consoleread()를 깨운다.
      }
    }
    break;
  }

  release(&cons.lock);
}
```

`consoleread()`는 `consoleintr()`가 한 줄을 완성할 때까지 기다렸다가 user buffer로 복사한다.

```c++
while (cons.r == cons.w) {        // read 가능한 입력이 아직 없음
  if (killed(myproc())) {
    release(&cons.lock);
    return -1;
  }
  sleep(&cons.r, &cons.lock);     // consoleintr()의 wakeup(&cons.r)을 기다린다.
}

c = cons.buf[cons.r++ % INPUT_BUF_SIZE]; // 공개된 입력에서 한 byte 소비

if (c == C('D')) {                // EOF
  if (n < target)
    cons.r--;                     // 이미 일부를 읽었다면 ^D는 다음 read가 보게 남겨 둔다.
  break;
}

cbuf = c;
if (either_copyout(user_dst, dst, &cbuf, 1) == -1)
  break;                          // user/kernel destination으로 한 byte 복사

if (c == '\n')
  break;                          // console read는 한 줄 단위로 반환한다.
```

## UART: 하드웨어 byte 전송

UART는 memory-mapped I/O 장치다. `kernel/memlayout.h`는 QEMU `virt` 플랫폼의 UART 주소를
`0x10000000`으로 하드코딩한다. `kernel/vm.c`는 이 주소를 kernel page table에 `PTE_R | PTE_W`로 매핑한다.

이 주소 범위는 RAM을 점유한다는 뜻이 아니라 physical address space 일부를 장치 register가 차지한다는 뜻이다.
RISC-V xv6는 port-mapped I/O가 아니라 MMIO를 사용한다.

```c++
#define UART0 0x10000000L

#define Reg(reg) ((volatile unsigned char *)(UART0 + (reg)))
#define ReadReg(reg)     (*(Reg(reg)))
#define WriteReg(reg, v) (*(Reg(reg)) = (v))
```

MMIO register block은 단순한 “명령 포트”라기보다 장치 내부 register들을 주소 공간에 노출한 것이다.
어떤 register는 설정값을 저장하고, 어떤 register는 status를 읽게 해 주고, 어떤 register는 data path의 입구가 된다.

출력 byte를 `WriteReg(THR, c)`로 쓰면 그 값은 RAM에 저장되는 것이 아니다. CPU store가 UART 장치로 전달되고,
UART의 transmit holding register, 또는 그 뒤의 UART 내부 transmit FIFO에 들어간다. xv6는 이 내부 FIFO의 각 칸을
직접 주소로 접근하지 않는다. xv6가 접근하는 것은 offset 0의 `THR`이라는 입구뿐이다.

입력도 반대 방향이다. 키보드/serial 입력은 UART 내부 receive 쪽에 들어오고, xv6는 offset 0의 `RHR`을 읽어 다음
byte를 꺼낸다. user `read()`가 기다리는 `cons.buf`는 이보다 위에 있는 kernel software buffer다.

주요 register는 다음과 같다.

```c++
#define RHR 0 // receive holding register: input byte read
#define THR 0 // transmit holding register: output byte write
#define IER 1 // interrupt enable register
#define FCR 2 // FIFO control register
#define ISR 2 // interrupt status register
#define LCR 3 // line control register
#define LSR 5 // line status register
```

offset 0은 읽으면 `RHR`, 쓰면 `THR`이다. `uartgetc()`는 `LSR_RX_READY`를 보고 준비된 입력 byte만 읽고,
`uartwrite()`는 `THR`에 byte를 쓴 뒤 `tx_busy`로 전송 중 상태를 기록한다.

`uartintr()`는 UART interrupt를 acknowledge한 뒤 두 일을 한다.

- 출력 쪽: `LSR_TX_IDLE`이면 전송이 끝났다고 보고 `tx_busy = 0`, `wakeup(&tx_chan)`.
- 입력 쪽: `uartgetc()`로 준비된 byte를 모두 읽어 `consoleintr(c)`에 넘김.

크기는 관점에 따라 다르다.

- xv6가 kernel page table에 매핑하는 UART MMIO 범위: `PGSIZE`, 즉 4096 bytes.
- xv6 메모리 배치에서 다음 장치 `VIRTIO0`는 `0x10001000`이므로, `UART0`부터 다음 장치 전까지도 4096 bytes다.
- 실제 16550a register interface는 byte register 몇 개다. 이 repo의 `uart.c`는 offset `0`, `1`, `2`, `3`, `5`만 쓴다.

즉 xv6는 page table 단위 때문에 4 KiB를 매핑하지만, 실제 코드가 사용하는 UART register offset은 그중 앞쪽 몇 byte뿐이다.

## Console: 파일 인터페이스와 입력 정책

`console.c`는 UART register offset이나 baud rate를 직접 다루지 않는다. 대신 xv6의 file/device 계층에
`console`을 연결하고, terminal처럼 보이게 만드는 정책을 맡는다.

```c++
devsw[CONSOLE].read = consoleread;
devsw[CONSOLE].write = consolewrite;
```

`kernel/file.c`의 `fileread()`/`filewrite()`는 file type이 `FD_DEVICE`이면 `devsw[f->major]`에 등록된 함수를
호출한다. 그래서 user program은 `console`을 일반 파일처럼 `read()`/`write()`할 수 있다.

출력 경로는 다음과 같다.

```text
write syscall
-> filewrite()
-> devsw[CONSOLE].write
-> consolewrite()
-> uartwrite()
-> WriteReg(THR, byte)
```

`consolewrite()`는 user/kernel source에서 작은 batch를 `either_copyin()`으로 kernel buffer에 복사한 뒤
`uartwrite()`에 넘긴다. user pointer 처리와 device file 경계는 console이 맡고, 실제 byte 전송은 UART가 맡는다.

입력 경로는 두 단계로 나뉜다. 먼저 interrupt가 console buffer를 채운다.

```text
UART input arrival
-> devintr()
-> uartintr()
-> uartgetc()
-> consoleintr(c)
-> cons.buf
-> wakeup(&cons.r)
```

그 다음 user `read()`가 console buffer를 소비한다.

```text
read syscall
-> fileread()
-> devsw[CONSOLE].read
-> consoleread()
-> cons.buf
-> either_copyout()
-> user buffer
```

## Console input buffer의 `r/w/e`

console은 입력을 line 단위로 제공한다. 그래서 단순한 read/write index 두 개만으로는 부족하고, 편집 중인 끝을
나타내는 `e`가 추가로 필요하다.

```c++
struct {
  struct spinlock lock;
  char buf[INPUT_BUF_SIZE];
  uint r; // Read index
  uint w; // Write index: read 가능한 입력의 끝
  uint e; // Edit index: 아직 편집 중인 입력의 끝
} cons;
```

- `r`: `consoleread()`가 다음에 읽을 위치
- `w`: user에게 공개된 입력의 끝
- `e`: 사용자가 현재 편집 중인 줄의 끝

`consoleintr(c)`는 입력 문자 하나를 받아 다음 정책을 적용한다.

- `Ctrl-P`: `procdump()`
- `Ctrl-U`: 현재 줄 삭제. `e`를 `w`나 직전 newline까지 되돌림
- `Ctrl-H` 또는 `0x7f`: 한 글자 삭제
- `\r`: `\n`으로 변환
- 일반 문자: echo 후 `cons.buf[cons.e++ % INPUT_BUF_SIZE]`에 저장
- newline, `Ctrl-D`, buffer full: `cons.w = cons.e`, `wakeup(&cons.r)`

즉 `w`는 “이제 read가 가져가도 되는 지점”이고, `e`는 “아직 사용자가 고칠 수 있는 지점”이다.
backspace와 `Ctrl-U`는 `e`만 되돌린다. newline이나 `Ctrl-D`가 들어오면 `w`가 `e`까지 전진하고,
잠든 `consoleread()`가 깨어난다.

## 강의 설명과 현재 repo의 차이

강의 스크립트는 UART software transmit circular buffer와 `uartputc()`/`uartstart()` 구조를 설명한다.
하지만 현재 repo의 `kernel/uart.c`에는 그 software transmit buffer가 없다.

현재 구현은 다음처럼 더 단순하다.

- `uartwrite()`가 byte마다 UART `THR`에 직접 쓴다.
- UART가 전송 중이면 `tx_busy`가 1이고 writer는 `sleep(&tx_chan, &tx_lock)`한다.
- UART transmit interrupt가 오면 `uartintr()`가 `LSR_TX_IDLE`을 확인하고 writer를 깨운다.
- `FCR`로 UART 내부 FIFO는 enable하지만, xv6 쪽 software transmit FIFO는 쓰지 않는다.

따라서 스크립트의 “UART transmit buffer에 넣고 `uartstart()`가 하드웨어로 보낸다”는 설명은 개념적으로만
읽고, 이 repo에서는 “`uartwrite()`가 직접 `THR`에 쓰고 interrupt가 다음 byte writer를 깨운다”로 고쳐 읽어야 한다.

## 한 줄 정리

```text
UART는 실제 장치 register와 byte를 주고받는 하드웨어 driver다.
console은 그 byte stream 위에 파일 인터페이스, line buffering, editing, echo, EOF 의미를 얹는 계층이다.
```

코드 확인 위치:
`kernel/uart.c`, `kernel/console.c`, `kernel/file.c`, `kernel/trap.c`, `kernel/memlayout.h`, `kernel/vm.c`
