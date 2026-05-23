# xv6-riscv

MIT xv6-riscv를 기반으로 운영체제의 전체 흐름을 학습하기 위한 개인 저장소입니다.

## macOS 환경 준비

```sh
xcode-select --install
brew tap riscv-software-src/riscv
brew install riscv-tools qemu
```

```sh
xcode-select -p
riscv64-unknown-elf-gcc --version
qemu-system-riscv64 --version
```

## 빌드와 실행

```sh
make
make fs.img
make qemu
```

QEMU 종료:

```text
Ctrl-a x
```

## xv6 셸 명령

```sh
ls
cat README
usertests -q
usertests
```

## 테스트와 정리

```sh
python3 test-xv6.py -q usertests
python3 test-xv6.py usertests
python3 test-xv6.py crash
make fmt
make clean
```

## CLion Makefile 설정

CLion에서 Makefile 프로젝트로 열면 `all` 타깃을 기본으로 찾습니다.
이 저장소의 `all`은 `clion-index`를 가리키며, `clion-index`는 커널과
`fs.img`를 모두 의존하도록 구성되어 있습니다. 이 설정으로 CLion이
`kernel/`, `user/`, `mkfs/`의 컴파일 명령을 함께 수집할 수 있습니다.

| 항목 | 값 |
| --- | --- |
| Build target | `all` 또는 `clion-index` |
| Clean target | `clean` |

## 참고

`README` 파일명은 xv6의 `fs.img` 생성과 `usertests` 호환을 위해 유지합니다.
