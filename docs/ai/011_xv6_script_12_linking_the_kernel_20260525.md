# xv6 Kernel 12 코드 정리: Linking the Kernel

## 읽는 순서

- `docs/reference/scripts/ko/12-xv6-kernel-12-linking-the-kernel-ZW0BHOYDYFc.md`
- `kernel/kernel.ld`: linker script
- `Makefile`: kernel link command와 object file 순서
- `kernel/entry.S`, `kernel/trampoline.S`: 특별히 배치되는 code

## 핵심

링커는 여러 object file을 하나의 kernel image로 합치고, 각 symbol의 최종 주소를 결정한다. 커널은
일반 프로그램보다 배치 위치가 중요하므로 `kernel.ld`로 직접 지시한다.

## section 배치

object file에는 보통 다음 section들이 들어 있다.

- `.text`: 실행 코드
- `.rodata`: read-only data
- `.data`: 초기값이 있는 writable data
- `.bss`: 0으로 초기화될 writable data
- `trampsec`: trampoline code

`kernel.ld`는 이 section들을 `0x80000000`부터 순서대로 배치한다.

```text
addr / symbol                         kernel image layout                         range / meaning
                                      +---------------------------------+
end                                   |  end of kernel image           |
                                      +---------------------------------+
                                      |  .bss                           |
                                      |  .bss                           |  [data_end, end)
                                      |                                 |  zero-filled R/W segment
                                      |                                 |
                                      +---------------------------------+
                                      |  .data                          |
                                      |  .data                          |  [rodata_end, data_end)
                                      |  .data                          |  initialized R/W data segment
                                      |                                 |
                                      +---------------------------------+
                                      |  .rodata                        |
                                      |  .rodata                        |  [etext, rodata_end)
                                      |                                 |  read-only segment
                                      +---------------------------------+
etext                                 |  end of executable text         |
                                      +---------------------------------+
                                      |  alignment padding              |
                                      +---------------------------------+
_trampoline + PGSIZE                  |  end of trampoline page         |
                                      +---------------------------------+
                                      |  trampsec                       |  [_trampoline,
                                      |  trampoline code                |   _trampoline + PGSIZE)
_trampoline                           |  trampoline page start          |
                                      +---------------------------------+
                                      |  alignment padding              |
                                      +---------------------------------+
                                      |  .text                          |
                                      |  .text from other .c files      |
                                      |                                 |  [_entry, etext)
                                      +---------------------------------+  R/X executable segment
                                      |  .text                          |
                                      |  entry.S                        |
_entry = 0x80000000                   |  ENTRY POINT                    |
                                      +---------------------------------+
```

`rodata_end`, `data_end`는 실제 linker symbol이 아니라 설명용 경계 이름이다. 현재 `kernel.ld`가 실제로
제공하는 주요 symbol은 `_entry`, `_trampoline`, `etext`, `end`다.

## `_entry`가 앞에 오는 이유

QEMU는 kernel을 `0x80000000`에 load하고 그 주소로 jump한다. 그래서 linker script는
`ENTRY(_entry)`를 지정하고, `.text` 시작에 `kernel/entry.o(_entry)`를 먼저 둔다.

즉 첫 실행 흐름은 linker가 만든 배치에 의존한다.

```text
QEMU -> 0x80000000 -> _entry
```

## linker symbol

링커는 C 코드가 직접 계산하기 어려운 주소들을 symbol로 제공한다.

- `etext`: executable kernel text 끝
- `_trampoline`: trampoline page 시작
- `end`: kernel image 끝, page allocator 시작 기준

`vm.c`는 `etext`로 text/data 권한을 나누고, `kalloc.c`는 `end` 이후 물리 page를 allocator에 넣는다.

코드 확인 위치:
`kernel/kernel.ld`, `Makefile`, `kernel/vm.c`, `kernel/kalloc.c`
