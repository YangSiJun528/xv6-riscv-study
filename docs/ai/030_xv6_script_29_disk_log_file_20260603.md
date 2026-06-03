# xv6 Kernel 29 코드 정리: Disk Log File

## 읽는 순서

- `docs/reference/scripts/ko/29-xv6-kernel-29-disk-log-file-MCc4Wpwekno.md`
- `kernel/log.c`: transaction log 구현
- `kernel/fs.h`: on-disk layout와 superblock
- `kernel/param.h`: `MAXOPBLOCKS`, `LOGBLOCKS`, `NBUF`
- `kernel/bio.c`, `kernel/buf.h`: buffer cache와 `bpin()`/`bunpin()`

## 1부: 개념

### 왜 log가 필요한가

파일 시스템 갱신은 보통 disk block 여러 개를 함께 바꾼다. 예를 들어 파일을 키우면 다음 작업이 한 묶음으로 일어난다.

```text
1. data block 하나를 할당
2. bitmap block 수정
3. inode의 size/addrs 수정
4. 실제 data block 내용 수정
```

중간에 crash가 나서 일부 block만 disk에 남으면 파일 시스템이 깨질 수 있다.

```text
bitmap에는 할당됨으로 표시됨
하지만 inode는 그 block을 가리키지 않음
-> block leak

inode는 block을 가리킴
하지만 bitmap에는 free로 남아 있음
-> 같은 block이 두 파일에 중복 할당될 수 있음
```

xv6 log의 목표는 multi-block update를 atomic하게 만드는 것이다.

```text
transaction 안의 모든 block update가 반영됨
또는
transaction이 없었던 것처럼 남음
```

### Disk layout

`kernel/fs.h`의 on-disk layout은 다음 순서다.

```text
disk blocks

0          1          2              3..32          33..45        46          47..1999
+----------+----------+--------------+--------------+-------------+-----------+-----------+
| boot     | super    | log header   | log data     | inode       | bitmap    | data      |
| block    | block    | block        | blocks       | blocks      | block     | blocks    |
+----------+----------+--------------+--------------+-------------+-----------+-----------+
                      ^              ^
                      |              |
                      sb.logstart    logged block contents
```

현재 repo 값은 `mkfs/mkfs.c` 기준이다.

```text
BSIZE      = 1024 bytes
FSSIZE     = 2000 blocks
LOGBLOCKS  = 30 data log blocks
nlog       = LOGBLOCKS + 1 = 31 blocks
log area   = block 2..32
```

log 영역은 "일반 파일"이 아니다. disk 안에 고정으로 잡아 둔 복구용 영역이다. 그래서 강의 제목의 "Disk Log File"은
파일 시스템 안의 pathname을 가진 file이라기보다, disk에 있는 log 영역으로 이해하면 된다.

### Log header

log header는 log 영역의 첫 block이다. 내용은 `struct logheader` 형태다.

```c++
struct logheader {
  int n;                 // 이번 transaction에 포함된 block 수
  int block[LOGBLOCKS];  // 각 log data block의 home block 번호
};
```

예를 들어 header가 다음 상태라고 하자.

```text
log header:
  n = 2
  block[0] = 103
  block[1] = 106

log data:
  log block 0에는 home block 103의 새 내용
  log block 1에는 home block 106의 새 내용
```

그러면 recovery/commit은 log data block을 읽어서 home location인 block 103, 106에 복사하면 된다.

### Redo log 흐름

xv6 log는 physical redo log다. inode, bitmap, directory의 의미를 해석하지 않고, "몇 번 disk block의 새 bytes"만
기록한다.

```text
1. file system code가 buffer cache block을 수정
2. log_write(bp)가 home block 번호를 log header에 등록하고 buffer를 pin
3. commit 때 수정된 buffer 내용을 log data block에 복사
4. log header를 disk에 씀
   -> 이 순간이 commit point
5. log data block들을 home location에 복사
6. log header의 n을 0으로 지워 log를 비움
```

crash 기준으로 보면 commit point가 중요하다.

```text
header write 전 crash:
  disk header의 n이 0
  log data는 무시됨
  transaction은 없었던 것처럼 처리

header write 후 crash:
  disk header의 n이 non-zero
  recovery가 log data를 home location에 replay
  transaction은 반영된 것으로 처리
```

### 목적과 한계

xv6 log에서 말하는 crash는 실행 중이던 kernel/process/memory 상태가 갑자기 사라지는 상황이다. 예를 들면 전원 종료,
panic/reset, QEMU 종료 같은 경우다.

xv6 log의 목적은 정상적인 kernel code가 여러 disk block을 갱신하는 도중 crash가 나도, 재부팅 후 disk file system에
"일부만 반영된 update"가 남지 않게 하는 것이다. 결과는 둘 중 하나여야 한다.

```text
commit point 전 crash:
  작업 전 file system 상태

commit point 후 crash:
  recovery가 log를 replay해서 작업 후 file system 상태
```

```text
잡는 것:
  write_log() 도중 crash
    log data block 일부만 써졌을 수 있음
    하지만 log header가 아직 n=0이면 recovery가 무시한다

  install_trans() 도중 crash
    home block 일부만 갱신됐을 수 있음
    하지만 log header가 아직 n>0이면 recovery가 다시 replay한다

못 잡는 것:
  실행 중이던 process/kernel 상태
  crash 직전의 memory 내용
  log header block 자체가 찢겨 써진 경우
  disk가 완료된 write의 순서/내용을 보장하지 않는 경우
```

즉 log data blocks를 쓰는 것까지가 commit은 아니다. `write_log()`는 준비 단계이고, disk log header를 쓰는
`write_head()`가 commit point다. xv6 log는 crash 자체나 memory 상태를 복구하는 장치가 아니라, commit point를 기준으로
file system update가 작업 전 또는 작업 후 상태 중 하나로만 보이게 하는 crash-consistency 장치다.

### Buffer cache와 log의 관계

파일 시스템 코드는 block을 수정할 때 보통 이렇게 한다.

```c++
bp = bread(...);
... bp->data 수정 ...
log_write(bp);
brelse(bp);
```

`log_write()`는 즉시 disk home location에 쓰지 않는다. 대신 block 번호를 log header에 기록하고, `bpin(bp)`으로
buffer cache에서 그 block이 재활용되지 않게 만든다.

```text
buffer cache:
  수정된 최신 block contents를 메모리에 유지

log header:
  어떤 home block들이 수정됐는지 번호만 기록

commit:
  buffer cache의 최신 contents를 log area와 home location에 순서대로 쓴다
```

## 2부: 코드로 읽는 `log.c`

### 자료구조

현재 repo의 `struct log`에는 `size` 필드가 없다. log data block 수 제한은 `LOGBLOCKS` 상수로 처리한다.

```c++
struct log {
  struct spinlock lock;
  int start;        // disk에서 log header block 번호
  int outstanding;  // 진행 중인 FS system call 수
  int committing;   // commit 중이면 새 begin_op()는 기다린다
  int dev;          // log가 있는 disk device
  struct logheader lh; // memory copy of log header
};
```

`outstanding`은 "아직 `end_op()`까지 가지 않은 file-system operation 수"다. xv6는 여러 FS syscall의 update를 모아,
`outstanding == 0`이 되는 순간 한 번에 commit한다.

### `initlog()`: log 위치 잡고 recovery

```c++
void
initlog(int dev, struct superblock *sb)
{
  if (sizeof(struct logheader) >= BSIZE)
    panic("initlog: too big logheader");

  initlock(&log.lock, "log");
  log.start = sb->logstart;  // superblock에서 log 시작 block을 가져온다.
  log.dev = dev;

  recover_from_log();        // 이전 boot에서 남은 committed log가 있으면 replay한다.
}
```

`fsinit()`이 superblock을 읽고 magic을 확인한 뒤 `initlog(dev, &sb)`를 호출한다. recovery는 boot 때 항상 수행된다.

### `begin_op()`: 새 FS operation 시작 제한

`begin_op()`은 file-system syscall이 log transaction에 들어가기 전에 호출된다. log 공간이 부족할 것 같거나 commit 중이면
현재 process를 재운다.

```c++
void
begin_op(void)
{
  acquire(&log.lock);
  while (1) {
    if (log.committing) {
      sleep(&log, &log.lock); // commit 중이면 새 operation은 섞이지 않는다.
    } else if (log.lh.n + (log.outstanding + 1) * MAXOPBLOCKS > LOGBLOCKS) {
      // 지금 진행 중인 operation들과 새 operation이 최대로 block을 쓰면
      // log data block 30개를 넘을 수 있으므로 기다린다.
      sleep(&log, &log.lock);
    } else {
      log.outstanding += 1;   // 이 FS operation이 transaction에 들어왔다.
      release(&log.lock);
      break;
    }
  }
}
```

여기서 sleep하는 것은 "log 객체가 잔다"가 아니라, 이 syscall을 실행하던 process가 `SLEEPING` 상태로 들어간다는 뜻이다.
나중에 `end_op()`가 `wakeup(&log)`를 호출하면 다시 조건을 검사한다.

### `log_write()`: 수정된 block 등록과 pin

`log_write()`는 `bwrite()`를 대체한다. caller는 이미 `bread()`로 buffer를 얻고 `bp->data`를 수정한 상태다.

```c++
void
log_write(struct buf *b)
{
  int i;

  acquire(&log.lock);
  if (log.lh.n >= LOGBLOCKS)
    panic("too big a transaction");
  if (log.outstanding < 1)
    panic("log_write outside of trans");

  // 같은 block이 이미 log에 있으면 새 slot을 쓰지 않는다.
  // 이것이 log absorption이다.
  for (i = 0; i < log.lh.n; i++) {
    if (log.lh.block[i] == b->blockno)
      break;
  }

  log.lh.block[i] = b->blockno; // log slot i는 home block b->blockno를 뜻한다.

  if (i == log.lh.n) {          // 처음 등장한 block이면
    bpin(b);                    // commit 전까지 buffer cache에서 재활용되지 않게 한다.
    log.lh.n++;                 // log에 포함된 서로 다른 block 수 증가
  }
  release(&log.lock);
}
```

absorption 덕분에 같은 transaction에서 같은 disk block을 여러 번 수정해도 log slot은 하나만 쓴다. commit 때는 buffer
cache에 남아 있는 마지막 contents가 log data block으로 복사된다.

### `end_op()`: 마지막 operation이면 commit

```c++
void
end_op(void)
{
  int do_commit = 0;

  acquire(&log.lock);
  log.outstanding -= 1;

  if (log.outstanding == 0) {
    do_commit = 1;        // 내가 마지막 FS operation이다.
    log.committing = 1;   // 새 begin_op()가 들어오지 못하게 막는다.
  } else {
    wakeup(&log);         // log 공간 예약이 줄었으니 기다리던 begin_op()를 깨울 수 있다.
  }
  release(&log.lock);

  if (do_commit) {
    commit();             // disk I/O 때문에 sleep할 수 있으므로 lock 없이 호출한다.
    acquire(&log.lock);
    log.committing = 0;
    wakeup(&log);         // commit이 끝났으니 새 begin_op()를 깨운다.
    release(&log.lock);
  }
}
```

핵심은 commit을 `log.lock`을 잡은 채 하지 않는다는 점이다. commit은 `bread()`, `bwrite()`, `brelse()`를 호출하고,
disk interrupt를 기다리며 sleep할 수 있다.

### `commit()`: log에 쓰고, header로 commit하고, home에 설치

```c++
static void
commit()
{
  if (log.lh.n > 0) {
    write_log();      // 1. buffer cache의 수정 block들을 log data blocks에 쓴다.
    write_head();     // 2. disk log header를 쓴다. 이 순간이 commit point다.
    install_trans(0); // 3. log data blocks를 home locations에 복사한다.
    log.lh.n = 0;
    write_head();     // 4. disk log header를 지워 transaction 완료를 표시한다.
  }
}
```

commit의 순서가 crash consistency를 만든다. 특히 `write_head()`가 `write_log()` 뒤에 와야 한다. header가 먼저 쓰이면
log data block들이 아직 완성되지 않았는데 recovery가 replay할 수 있다.

### `write_log()`: cache block -> log block

```c++
static void
write_log(void)
{
  int tail;

  for (tail = 0; tail < log.lh.n; tail++) {
    struct buf *to = bread(log.dev, log.start + tail + 1); // log data block
    struct buf *from = bread(log.dev, log.lh.block[tail]); // home block의 cached contents
    memmove(to->data, from->data, BSIZE);                  // 새 내용을 log 영역에 복사
    bwrite(to);                                            // log block을 disk에 쓴다
    brelse(from);
    brelse(to);
  }
}
```

`from`은 `log_write()`에서 pin된 home buffer다. 그래서 commit 전에 다른 block으로 재활용되지 않는다.

### `write_head()`: 진짜 commit point

```c++
static void
write_head(void)
{
  struct buf *buf = bread(log.dev, log.start); // log header block
  struct logheader *hb = (struct logheader *)(buf->data);

  hb->n = log.lh.n;
  for (int i = 0; i < log.lh.n; i++) {
    hb->block[i] = log.lh.block[i];
  }

  bwrite(buf); // disk header에 n과 home block 목록을 쓴다.
  brelse(buf);
}
```

첫 번째 `write_head()`는 commit point다. 두 번째 `write_head()`는 `n = 0`을 disk에 써서 log를 clear한다.

### `install_trans()`: log block -> home block

```c++
static void
install_trans(int recovering)
{
  int tail;

  for (tail = 0; tail < log.lh.n; tail++) {
    struct buf *lbuf = bread(log.dev, log.start + tail + 1); // log block
    struct buf *dbuf = bread(log.dev, log.lh.block[tail]);   // home block
    memmove(dbuf->data, lbuf->data, BSIZE);                  // log 내용을 home으로 복사
    bwrite(dbuf);                                            // home location에 반영

    if (recovering == 0)
      bunpin(dbuf); // 정상 commit이면 log_write() 때 pin한 home buffer를 푼다.

    brelse(lbuf);
    brelse(dbuf);
  }
}
```

recovery 중에는 이전 실행의 buffer pin이 남아 있지 않으므로 `bunpin()`하지 않는다.

### `recover_from_log()`: boot 때 replay

```c++
static void
recover_from_log(void)
{
  read_head();       // disk header를 memory log.lh로 읽는다.
  install_trans(1);  // n > 0이면 log data를 home location에 replay한다.
  log.lh.n = 0;
  write_head();      // replay 후 disk header를 clear한다.
}
```

`log.lh.n == 0`이면 `install_trans(1)` loop가 아무 일도 하지 않는다. `n > 0`이면 이전 실행이 commit point까지는
도달했다는 뜻이므로 replay한다.

## 3부: 실행 예시 흐름

예시는 두 block을 수정하는 transaction이다. home block 103과 106이 수정된다고 하자.

먼저 위치를 구분해야 한다.

```text
bp->data 수정:
  caller가 이미 buffer cache 안의 bytes를 바꾼다

log_write(bp):
  log.lh.block[]에 "어느 home block이 바뀌었는지" 번호만 기록한다
  실제 bytes는 buffer cache의 bp->data에 남고, bpin(bp)으로 재활용을 막는다

commit/write_log():
  pinned buffer contents를 disk log data blocks에 쓴다

commit/write_head():
  disk log header를 써서 commit을 확정한다

commit/install_trans():
  disk log data blocks를 원래 home blocks에 복사한다
```

### 1. FS operation 시작

```text
begin_op()
  log.outstanding = 1
  log.lh.n = 0
```

이 시점에는 아직 disk log 영역도, home location도 바뀌지 않았다.

### 2. block 103 수정

```text
bp = bread(dev, 103)
bp->data 수정
log_write(bp)
```

`log_write()` 후 memory log header는 다음 상태가 된다.

```text
memory log.lh:
  n = 1
  block[0] = 103

buffer cache:
  block 103의 최신 내용이 pinned 상태로 남음

disk:
  아직 안 바뀜
```

### 3. block 106 수정

```text
bp = bread(dev, 106)
bp->data 수정
log_write(bp)
```

```text
memory log.lh:
  n = 2
  block[0] = 103
  block[1] = 106

buffer cache:
  block 103 pinned
  block 106 pinned

disk:
  아직 안 바뀜
```

`log_write()`는 disk에 쓰지 않는다. disk write는 commit 때 한다.

### 4. FS operation 종료

```text
end_op()
  log.outstanding = 0
  commit() 시작
```

마지막 outstanding operation이 끝났으므로 commit이 진행된다.

### 5. `write_log()`: log data blocks에 복사

```text
disk log area:

block 2      log header   아직 n=0
block 3      block 103의 새 내용
block 4      block 106의 새 내용
```

여기서 crash가 나면 header가 아직 commit되지 않았으므로 recovery는 log data를 무시한다.

### 6. `write_head()`: commit point

```text
disk log header at block 2:
  n = 2
  block[0] = 103
  block[1] = 106
```

이 write가 완료되면 transaction은 committed 상태다. 이후 crash가 나면 recovery는 block 3, 4의 내용을 home block
103, 106으로 replay한다.

### 7. `install_trans(0)`: home location에 설치

```text
copy:
  log data block 3 -> home block 103
  log data block 4 -> home block 106

then:
  bunpin(block 103)
  bunpin(block 106)
```

여기서 crash가 나서 home block 하나만 갱신됐더라도, disk log header는 아직 `n=2`다. 다음 boot의 recovery가 다시 둘 다
replay하므로 작업 후 상태로 맞춰진다.

### 8. log clear

```text
log.lh.n = 0
write_head()
```

disk log header의 `n`이 0이 되면 recovery는 할 일이 없다고 판단한다. log data block 3, 4에 오래된 bytes가 남아 있어도
header가 0이면 무시된다.

## 현재 repo 차이

- 스크립트는 log 구조체에 `size` 필드가 있는 것처럼 설명하지만, 현재 `kernel/log.c`의 `struct log`에는 `size`가 없다.
  log 한도 검사는 `LOGBLOCKS` 상수로 한다.
- superblock에는 `nlog`가 있고 `mkfs`는 header 포함 `LOGBLOCKS + 1`개 log block을 만들지만, 현재 `initlog()`는
  `sb->nlog`를 저장하지 않고 `sb->logstart`만 사용한다.
- 스크립트의 "log block 30개" 표현은 현재 repo 기준으로는 data log block `LOGBLOCKS == 30`개라는 뜻으로 읽어야 한다.
  disk의 log 영역 전체는 header 1개를 포함해 31 block이다.
- 현재 `fsinit()`은 `initlog()` 뒤에 `ireclaim(dev)`도 호출해 orphaned inode를 정리한다. 스크립트의 log 설명 중심 흐름에는
  이 단계가 거의 나오지 않는다.
