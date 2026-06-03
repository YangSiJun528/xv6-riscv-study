# xv6 Kernel 30 코드 정리: The File System

## 읽는 순서

- `docs/reference/scripts/ko/30-xv6-kernel-30-the-file-system-o9sYiWj1F28.md`
- `kernel/fs.h`: on-disk file system format
- `kernel/fs.c`: file system layer 개요와 block/inode/path 함수
- `kernel/file.h`: in-memory inode와 file descriptor 객체
- `kernel/param.h`, `mkfs/mkfs.c`: 현재 file system image 크기와 layout 계산

## 핵심

강의 30번의 핵심은 파일 시스템 코드를 세부 추적하기 전에, 디스크 어디에 무엇이 놓이고 각 계층이 어떻게 이어지는지 잡는 것이다. xv6 파일 시스템은 하나의 디스크 장치 위에 고정 크기 block들을 순서대로 놓고, 그 위에 log, inode 배열, bitmap, file data block을 배치한다.

## 디스크 레이아웃

`kernel/fs.h`의 주석과 `struct superblock` 기준 레이아웃은 다음 순서다.

```text
[ boot block | super block | log | inode blocks | free bitmap | data blocks ]
```

현재 repo에서 `mkfs/mkfs.c`가 만드는 실제 값은 다음과 같다.

| 영역 | 블록 |
| --- | --- |
| boot block | 0 |
| super block | 1 |
| log | 2..32 |
| inode blocks | 33..45 |
| free bitmap | 46 |
| data blocks | 47..1999 |

검증된 주요 값:

| 항목 | 현재 값 | 근거 |
| --- | ---: | --- |
| `BSIZE` | 1024 bytes | `kernel/fs.h` |
| `FSSIZE` | 2000 blocks | `kernel/param.h` |
| `FSMAGIC` | `0x10203040` | `kernel/fs.h` |
| `ROOTINO` | 1 | `kernel/fs.h` |
| `MAXOPBLOCKS` | 10 | `kernel/param.h` |
| `LOGBLOCKS` | 30 | `kernel/param.h` |
| mkfs `nlog` | 31 blocks | log header + 30 data blocks |
| `NINODES` | 200 | `mkfs/mkfs.c` |
| `IPB` | 16 dinodes/block | `1024 / sizeof(struct dinode)` |
| inode blocks | 13 | `NINODES / IPB + 1` |
| bitmap blocks | 1 | `FSSIZE / BPB + 1` |
| data blocks | 1953 | `2000 - 47` |

superblock은 블록 1에 있고 `fsinit()`이 `readsb(dev, &sb)`로 읽는다. `sb.magic != FSMAGIC`이면 panic을 내고, 이후 `initlog(dev, &sb)`로 로그를 초기화한다. 커널은 superblock을 읽지만 정상 동작 중 수정하지 않는다.

## 디스크 구조와 메모리 구조

디스크에 영속 저장되는 구조와 커널 메모리의 캐시 구조를 구분해야 한다.

| 구분 | 구조체 | 위치 | 역할 |
| --- | --- | --- | --- |
| on-disk | `struct superblock` | block 1 | 전체 크기, log/inode/bitmap 시작점 |
| on-disk | `struct dinode` | inode blocks | 파일 타입, 링크 수, 크기, 데이터 블록 주소 |
| on-disk | `struct dirent` | directory file data | 이름과 inode 번호의 배열 원소 |
| in-memory | `struct inode` | `itable` | dinode 복사본 + `dev`, `inum`, `ref`, lock, valid |
| in-memory | `struct file` | `ftable` | 열린 파일 디스크립터의 커널 객체 |

`dinode`에는 inode 번호가 필드로 저장되지 않는다. inode 번호는 inode 배열 안의 위치다. 반대로 `struct inode`는 메모리에서 어떤 디스크 inode를 가리키는지 알아야 하므로 `dev`와 `inum`을 가진다.

## 파일 크기와 블록 주소

현재 repo의 `kernel/fs.h`:

```c++
#define NDIRECT   12
#define NINDIRECT (BSIZE / sizeof(uint))
#define MAXFILE   (NDIRECT + NINDIRECT)
```

`BSIZE=1024`, `sizeof(uint)=4`이므로 `NINDIRECT=256`, `MAXFILE=268` blocks다. 최대 파일 크기는 `268 * 1024 = 274432` bytes다. `dinode.addrs[NDIRECT + 1]`의 앞 12개는 직접 블록 주소, 마지막 1개는 간접 블록 주소다.

## 계층 흐름

디스크 블록 접근은 먼저 buffer cache를 지난다. `bread(dev, blockno)`는 해당 블록의 `struct buf`를 가져오고, 사용자는 `bp->data`를 읽거나 고친 뒤 `brelse(bp)`로 놓는다. 수정한 파일 시스템 블록은 직접 `bwrite()`하지 않고 `log_write(bp)`로 로그에 등록한다.

그 위에서 `kernel/fs.c` 주석은 파일 시스템을 다섯 계층으로 설명한다.

1. Blocks: bitmap으로 원시 디스크 블록을 할당/해제한다. `balloc()`은 `BBLOCK(b, sb)`로 bitmap 블록을 읽고 비트를 세운 뒤, 새 블록을 0으로 초기화한다.
2. Log: 여러 블록 갱신을 트랜잭션으로 묶는다. 파일 시스템 system call은 `begin_op()`/`end_op()` 사이에서 실행되고, 수정된 버퍼는 `log_write()`로 기록된다.
3. Files/Inodes: `ialloc()`, `iupdate()`, `readi()`, `writei()`가 inode 메타데이터와 파일 내용을 다룬다.
4. Directories: 디렉터리는 `struct dirent` 배열을 내용으로 가진 특수한 파일이다. `dirlookup()`은 선형 검색하고 `dirlink()`는 빈 엔트리에 이름과 inode 번호를 쓴다.
5. Names/Paths: `namei()`와 `nameiparent()`가 `/a/b/c` 같은 문자열을 inode로 변환한다. 절대 경로는 `ROOTDEV`, `ROOTINO`에서 시작하고 상대 경로는 프로세스의 `cwd`에서 시작한다.

그 위에 file descriptor 계층이 있다. `sys_open()`은 pathname을 `namei()` 또는 `create()`로 inode까지 해석한 뒤 `filealloc()`으로 `struct file`을 얻고, 프로세스 fd table에 연결한다. 이후 `read()`/`write()`는 fd에서 `struct file`을 찾고, `fileread()`/`filewrite()`가 `readi()`/`writei()` 또는 device/pipe 함수로 내려간다.

## 이름, 링크, 디렉터리

파일 자체에는 이름이 없다. 이름은 디렉터리 파일 안의 `dirent { inum, name[DIRSIZ] }`에 저장된다. `DIRSIZ=14`이고, 이름이 정확히 14글자면 NUL 종료가 없을 수 있다.

inode의 `nlink`는 그 inode를 가리키는 디렉터리 엔트리 수다. 일반 파일은 여러 hard link를 가질 수 있다. 디렉터리는 `.`와 `..` 때문에 구조적으로 특별하지만, xv6는 디렉터리 트리를 유지하려고 일반적인 디렉터리 hard link 생성을 허용하지 않는다. `ROOTINO=1`은 커널이 루트 디렉터리를 찾기 위한 고정 inode 번호이며, inode 0은 빈 directory entry를 나타내는 데 쓰인다.

## 현재 repo 차이

- 강의 예시는 파일 시스템 크기를 1000 blocks로 설명하지만, 현재 repo의 `FSSIZE`는 2000 blocks다.
- 강의 예시의 superblock 값은 대략 `nblocks=959`, `inodestart=32`, `bmapstart=38`로 설명된다. 현재 `mkfs` 값은 `nblocks=1953`, `inodestart=33`, `bmapstart=46`이다.
- 강의의 log 시작은 현재도 block 2로 같지만, 현재 `mkfs`는 `LOGBLOCKS=30`에 header block을 더해 log 영역을 31 blocks로 잡는다.
- 현재 repo도 xv6 기본처럼 파일 타입은 `T_DIR`, `T_FILE`, `T_DEVICE`뿐이고 symbolic link 타입은 없다.
