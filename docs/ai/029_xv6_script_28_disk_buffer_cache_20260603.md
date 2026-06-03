# xv6 Kernel 28 코드 정리: Disk Buffer Cache

## 읽는 순서

- `docs/reference/scripts/ko/28-xv6-kernel-28-disk-buffer-cache-1ajlGSLOqJE.md`
- `kernel/buf.h`: `struct buf`
- `kernel/bio.c`: buffer cache 본체
- `kernel/defs.h`: `bread`, `bwrite`, `brelse`, `bpin`, `bunpin` 선언
- `kernel/virtio_disk.c`: 실제 disk read/write와 interrupt 완료 처리

## sector와 block

disk는 실제 장치 관점에서는 sector 단위로 읽고 쓸 수 있다. 반면 file system은 더 큰 고정 단위인 block으로 disk를
다룬다. xv6 코드에서 file system block 크기는 `BSIZE`이고, 현재 값은 1024 bytes다.

현재 VirtIO driver는 xv6 block 번호를 512-byte sector 번호로 바꿔 장치에 넘긴다.

```c++
uint64 sector = b->blockno * (BSIZE / 512);
```

즉 현재 설정에서는 xv6 block 하나가 512-byte sector 두 개에 해당한다. buffer cache의 `struct buf` 하나는 sector
하나가 아니라 xv6 file system block 하나, 즉 `BSIZE` bytes를 담는다.

## 버퍼 캐시가 필요한 이유

버퍼 캐시는 단순히 disk I/O를 줄이는 cache만은 아니다. xv6에서는 세 가지 역할을 한다.

- 같은 block을 반복해서 읽을 때 disk I/O를 줄인다.
- 특정 disk block마다 커널 안의 공유 in-memory copy를 하나로 모은다.
- 여러 process가 같은 block을 읽고 쓸 때 `struct buf`의 sleeplock을 synchronization boundary로 쓴다.

`bio.c` 주석의 interface 규칙은 단순하다. 특정 block이 필요하면 `bread()`로 locked buffer를 얻고, 데이터를 바꿨으면
`bwrite()`로 disk에 쓴다. 사용이 끝나면 `brelse()`를 호출하며, 그 뒤에는 그 buffer를 사용하면 안 된다.

## 자료구조

현재 repo의 `struct buf`는 `kernel/buf.h`에 있다.

```c++
struct buf {
  int valid; // has data been read from disk?
  int disk;  // does disk "own" buf?
  uint dev;
  uint blockno;
  struct sleeplock lock;
  uint refcnt;
  struct buf *prev; // LRU cache list
  struct buf *next;
  uchar data[BSIZE];
};
```

`valid`는 `data[]`가 해당 disk block의 유효한 내용을 담는지 표시한다. `disk`는 `virtio_disk_rw()`와
`virtio_disk_intr()`가 I/O 진행 중인 buffer를 재우고 깨울 때 쓴다. `dev`, `blockno`는 이 buffer가 어느 device의 어느
block을 캐시하는지 나타낸다. `refcnt`가 0이면 재활용 가능하고, 0보다 크면 사용 중이거나 pin된 상태다.

현재 xv6-riscv에는 예전 xv6의 `B_BUSY` 같은 buffer flag가 없다. 독점 사용은 buffer별 `sleeplock lock`으로 표현하고,
재활용 가능 여부는 `refcnt`로 표현한다.

`bio.c`의 `bcache`는 전역 buffer cache다.

```c++
struct {
  struct spinlock lock;
  struct buf buf[NBUF];
  struct buf head;
} bcache;
```

`NBUF`는 `param.h`에서 `MAXOPBLOCKS * 3`, 즉 30이다. `buf[]`는 고정 크기 buffer 배열이고, 모든 buffer는
`head`를 sentinel로 하는 원형 doubly linked list에 걸린다. 주석 기준으로 `head.next`는 most recent, `head.prev`는
least recent다.

## Lock 역할 분리

`bcache.lock`은 cache list와 buffer metadata를 짧게 보호하는 spinlock이다. 주로 `prev`, `next`, `refcnt`, `dev`,
`blockno`를 다룰 때 잡는다. 이 lock을 잡은 채 disk I/O를 기다리면 안 된다.

각 `buf.lock`은 buffer 내용 자체를 보호하는 sleeplock이다. `data[]`와 `valid`를 다루는 긴 구간에서 쓰이고,
`virtio_disk_rw()`가 interrupt 완료를 기다리며 sleep할 수 있으므로 spinlock이 아니라 sleeplock이 맞다. `disk` 필드는
VirtIO driver가 `disk.vdisk_lock` 아래에서 I/O 완료 대기를 조정할 때 사용한다.

## 코드로 읽는 함수 흐름

### `binit()`

`binit()`은 고정 크기 buffer 배열을 원형 doubly linked list로 엮는다.

```c++
void
binit(void)
{
  struct buf *b;

  initlock(&bcache.lock, "bcache"); // list/refcnt/dev/blockno 보호용 spinlock

  // sentinel head 하나로 빈 원형 list를 만든다.
  bcache.head.prev = &bcache.head;
  bcache.head.next = &bcache.head;

  for (b = bcache.buf; b < bcache.buf + NBUF; b++) {
    // 새 buffer를 head 바로 뒤에 끼운다.
    b->next = bcache.head.next;
    b->prev = &bcache.head;

    // 각 disk block buffer의 내용 사용은 sleeplock으로 직렬화한다.
    initsleeplock(&b->lock, "buffer");

    bcache.head.next->prev = b;
    bcache.head.next = b;
  }
}
```

명시적으로 설정하지 않은 `refcnt`, `dev`, `blockno`, `valid` 등은 전역 변수 초기값 0에 의존한다.

### `bget(dev, blockno)`

`bget()`은 cache hit를 찾거나, 없으면 unused buffer 하나를 재활용한다. 반환 시점에는 해당 buffer의 sleeplock이 잡혀 있다.

```c++
static struct buf *
bget(uint dev, uint blockno)
{
  struct buf *b;

  acquire(&bcache.lock); // cache list와 refcnt를 보는 동안 잡는다.

  // 1. cache hit 확인: 같은 dev/blockno를 가진 buffer가 이미 있나?
  for (b = bcache.head.next; b != &bcache.head; b = b->next) {
    if (b->dev == dev && b->blockno == blockno) {
      b->refcnt++;              // 재활용되지 못하게 먼저 붙잡는다.
      release(&bcache.lock);    // 긴 대기 가능성이 있는 sleeplock 전에 spinlock은 놓는다.
      acquiresleep(&b->lock);   // 이 block buffer의 data[] 독점 사용권
      return b;
    }
  }

  // 2. cache miss: least-recently-used 쪽에서 안 쓰는 buffer를 찾는다.
  for (b = bcache.head.prev; b != &bcache.head; b = b->prev) {
    if (b->refcnt == 0) {
      b->dev = dev;             // 이제 이 buffer는 새 disk block의 cache entry다.
      b->blockno = blockno;
      b->valid = 0;             // data[]는 아직 해당 block 내용이 아니다.
      b->refcnt = 1;            // 다른 block으로 재활용되지 않게 표시한다.
      release(&bcache.lock);
      acquiresleep(&b->lock);
      return b;
    }
  }

  panic("bget: no buffers");
}
```

중요한 점은 lookup과 allocation이 모두 `bcache.lock` 아래에서 일어난다는 것이다. 그래서 같은 disk block에 대해
cache 안에 active copy가 둘 생기지 않는다.

### `bread(dev, blockno)`

`bread()`는 buffer cache의 public read interface다.

```c++
struct buf *
bread(uint dev, uint blockno)
{
  struct buf *b;

  b = bget(dev, blockno);   // locked buffer를 얻는다.
  if (!b->valid) {          // miss로 재활용된 buffer면 data[]가 아직 틀린 block 내용이다.
    virtio_disk_rw(b, 0);   // disk에서 block을 읽어 b->data에 채운다.
    b->valid = 1;           // 이제 data[]가 dev/blockno의 유효한 copy다.
  }
  return b;                 // caller는 b->lock을 잡은 상태로 받는다.
}
```

### `bwrite(b)`

`bwrite()`는 이미 locked buffer의 `data[]`를 disk의 원래 block 위치에 쓴다.

```c++
void
bwrite(struct buf *b)
{
  if (!holdingsleep(&b->lock))
    panic("bwrite");        // caller가 bread/bget으로 얻은 locked buffer여야 한다.

  virtio_disk_rw(b, 1);     // b->data를 disk로 쓴다.
}
```

`bwrite()`는 buffer를 release하지 않는다. 같은 locked buffer를 더 수정하거나, 끝나면 caller가 `brelse()`한다.

### `brelse(b)`

`brelse()`는 buffer 사용 종료 지점이다. 호출 뒤에는 그 buffer를 더 쓰면 안 된다.

```c++
void
brelse(struct buf *b)
{
  if (!holdingsleep(&b->lock))
    panic("brelse");

  releasesleep(&b->lock);       // block data[] 독점 사용을 끝낸다.

  acquire(&bcache.lock);
  b->refcnt--;                  // 이 caller의 cache reference를 내려놓는다.
  if (b->refcnt == 0) {
    // 아무도 쓰지 않으면 MRU 쪽(head.next)으로 옮긴다.
    b->next->prev = b->prev;
    b->prev->next = b->next;
    b->next = bcache.head.next;
    b->prev = &bcache.head;
    bcache.head.next->prev = b;
    bcache.head.next = b;
  }

  release(&bcache.lock);
}
```

miss 때 재활용은 반대편인 `head.prev`에서 시작한다. 즉 현재 repo는 release된 buffer를 head 쪽, 재활용 후보 검색은
tail 쪽으로 둔다.

### `bpin(b)` / `bunpin(b)`

`bpin()`과 `bunpin()`은 log가 수정된 buffer를 commit 전까지 재활용하지 못하게 붙잡는 데 쓴다.

```c++
void
bpin(struct buf *b)
{
  acquire(&bcache.lock);
  b->refcnt++;          // refcnt가 0이 아니면 bget()의 재활용 대상이 아니다.
  release(&bcache.lock);
}

void
bunpin(struct buf *b)
{
  acquire(&bcache.lock);
  b->refcnt--;          // commit 후 pin을 풀어 다시 재활용 가능하게 한다.
  release(&bcache.lock);
}
```

## VirtIO란

VirtIO는 guest OS와 virtual device가 통신하기 위한 표준 인터페이스다. xv6는 QEMU 위에서 실행되고, QEMU는 disk를
VirtIO block device로 제공한다. 그래서 xv6는 실제 SATA/NVMe 같은 장치 규칙을 직접 다루지 않고, `virtio_disk.c`의
VirtIO driver를 통해 block read/write 요청을 보낸다.

역할을 나누면 다음과 같다.

```text
bio.c:
  file system block cache
  dev/blockno에 해당하는 struct buf를 찾고 잠근다

virtio_disk.c:
  struct buf 하나를 VirtIO disk 요청으로 바꾼다
  device에 요청을 알리고, 완료 interrupt가 올 때까지 기다린다

QEMU VirtIO block device:
  host의 disk image file을 실제 저장소처럼 읽고 쓴다
```

즉 buffer cache는 "어느 block을 메모리에 캐시할 것인가"를 관리하고, VirtIO driver는 "그 block I/O를 가상 디스크 장치에
어떻게 요청할 것인가"를 처리한다.

## VirtIO와의 연결

`virtio_disk_rw(b, write)`는 buffer 하나를 장치 I/O 요청 하나로 넘긴다. I/O 요청을 virtqueue에 넣은 뒤 `b->disk = 1`로
표시하고, 완료 interrupt가 올 때까지 `sleep(b, &disk.vdisk_lock)`으로 잔다. `virtio_disk_intr()`는 used ring을 훑어
완료된 buffer의 `b->disk = 0`을 설정하고 `wakeup(b)`로 깨운다.

## 현재 repo 차이

- 스크립트는 file 이름을 `bufferio.c`처럼 말하지만 현재 파일은 `kernel/bio.c`다.
- 인터페이스 함수 이름은 `brelease`가 아니라 `brelse`다.
- 구식 xv6의 `B_BUSY` flag는 현재 `struct buf`에 없다. 현재 코드는 `refcnt`와 `struct sleeplock lock`으로 상태를 나눈다.
- 스크립트 말미는 release된 buffer를 least-recently-used 끝으로 옮긴다고 설명하지만, 현재 `bio.c`는 `brelse()`에서
  `refcnt == 0`인 buffer를 `head.next`로 옮기며 주석도 "most-recently-used list"라고 한다.
- 강의 예시는 block 하나가 여러 sector로 나뉠 수 있다고 일반 설명한다. 현재 설정에서는 xv6 block 1개가 512-byte sector
  2개다.
