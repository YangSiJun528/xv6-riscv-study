# xv6 Kernel 25 코드 정리: Sleeplocks

## 읽는 순서

- `docs/reference/scripts/ko/25-xv6-kernel-25-sleeplocks-nc03pTD53Ro.md`
- `kernel/sleeplock.h`: sleeplock 구조체
- `kernel/sleeplock.c`: `initsleeplock`, `acquiresleep`, `releasesleep`, `holdingsleep`
- `kernel/proc.c`: `sleep(chan, lk)`, `wakeup(chan)`
- `kernel/bio.c`: buffer cache에서 sleeplock 사용
- `kernel/fs.c`, `kernel/file.h`: inode lock으로 sleeplock 사용

## 핵심

sleeplock은 오래 잡을 수 있는 lock이다. lock을 얻지 못한 process는 CPU를 계속 태우며 spin하지 않고
`sleep()`으로 잠든다. 그래서 disk I/O처럼 기다림이 길 수 있는 구간을 보호하는 데 쓰인다.

spinlock은 짧은 critical section을 보호한다. `acquire()`는 interrupt를 끄고 atomic swap 루프를 돌며 lock을
얻을 때까지 기다린다. 따라서 spinlock을 잡은 채 오래 기다리거나 sleep하면 안 된다.

강의의 핵심 대비는 기다리는 방식이다. spinlock은 다른 core가 곧 `release()`할 것이라는 전제에서 짧게 spin한다.
반대로 lock을 잡은 뒤 disk I/O처럼 오래 기다리거나 process가 sleep해야 하는 구간이면, 기다리는 쪽이 CPU를
낭비하지 않도록 sleeplock을 쓴다. sleeplock은 lock 소유자가 sleep할 수 있고, release는 나중에 깨어난 뒤 수행될
수 있다.

## 구조체

현재 repo의 `struct sleeplock`은 다음 필드로 구성된다.

```c++
struct sleeplock {
  uint locked;        // Is the lock held?
  struct spinlock lk; // spinlock protecting this sleep lock

  // For debugging:
  char *name; // Name of lock.
  int pid;    // Process holding lock
};
```

`locked`와 `pid`를 안전하게 읽고 쓰기 위해 내부 spinlock `lk`를 사용한다. 이 spinlock은 sleeplock 자체의 상태를
짧게 보호하기 위한 것이지, 실제 긴 작업 전체를 보호하는 lock이 아니다.

`initsleeplock()`은 내부 spinlock을 `initlock(&lk->lk, "sleep lock")`으로 초기화하고, `name`, `locked = 0`,
`pid = 0`을 설정한다. `name`은 기능보다는 디버깅용 이름이고, 초기화 뒤 바뀌지 않는다.

## acquire/release 흐름

`acquiresleep()`은 내부 spinlock을 잡고 `locked`를 확인한다. 이미 잠겨 있으면 sleeplock 주소 `lk`를 channel로
삼아 잔다.

```c++
void
acquiresleep(struct sleeplock *lk)
{
  acquire(&lk->lk);
  while (lk->locked) {
    sleep(lk, &lk->lk);
  }
  lk->locked = 1;
  lk->pid = myproc()->pid;
  release(&lk->lk);
}
```

`sleep(chan, lk)`는 `p->lock`을 잡은 뒤 기존 lock을 release하고 process를 `SLEEPING`으로 바꾼다. 깨어나면 다시
원래 lock을 acquire한다. 이 순서 때문에 unlock과 sleep 사이에서 `wakeup()`을 놓치지 않는다.

`releasesleep()`은 상태를 비우고 같은 channel에서 자는 process를 깨운다.

```c++
void
releasesleep(struct sleeplock *lk)
{
  acquire(&lk->lk);
  lk->locked = 0;
  lk->pid = 0;
  wakeup(lk);
  release(&lk->lk);
}
```

여러 process가 동시에 깨어날 수 있으므로 `acquiresleep()`은 `if`가 아니라 `while (lk->locked)`로 다시 검사한다.
깨어난 process들은 내부 spinlock `lk->lk`를 하나씩 얻어 상태를 확인하고, 먼저 도착한 하나만 `locked = 1`로 바꾼다.
나머지는 이미 다시 잠긴 것을 보고 같은 channel에서 다시 잔다.

## spinlock과 sleeplock 차이

| 구분 | spinlock | sleeplock |
| --- | --- | --- |
| 기다리는 방식 | busy-wait | `sleep()` 후 `wakeup()` |
| 보호 대상 | 짧은 critical section | 오래 걸릴 수 있는 자원 사용 |
| sleep 가능 여부 | 잡은 채 sleep하면 안 됨 | lock을 잡은 process가 sleep 가능 |
| 구현 | atomic swap, interrupt disable | 상태 보호용 내부 spinlock + sleep channel |
| xv6 예 | `bcache.lock`, `itable.lock`, `p->lock` | `buf.lock`, `inode.lock` |

## xv6에서 쓰이는 곳

### buffer cache

`kernel/bio.c`의 각 `struct buf`는 `struct sleeplock lock`을 가진다. `bget()`은 buffer cache 목록과 `refcnt`는
`bcache.lock` spinlock으로 보호하고, 특정 disk block buffer를 반환할 때는 `acquiresleep(&b->lock)`으로 그 buffer
자체를 잠근다.

`bread()`는 locked buffer를 얻은 뒤, 내용이 아직 유효하지 않으면 `virtio_disk_rw(b, 0)`로 disk read를 한다.
`virtio_disk_rw()`는 I/O 완료 interrupt를 기다리며 `sleep(b, &disk.vdisk_lock)`할 수 있으므로 buffer를 오래 잡을 수
있는 sleeplock이 맞다.

`bwrite()`와 `brelse()`는 현재 process가 buffer sleeplock을 잡고 있는지 `holdingsleep(&b->lock)`으로 확인하고,
아니면 panic한다.

### inode

`kernel/file.h`의 `struct inode`에도 `struct sleeplock lock`이 있다. `ref`, `dev`, `inum`은 inode table의
spinlock인 `itable.lock`으로 보호하고, `valid`, `size`, `type`, `addrs` 같은 inode 본문 필드는 `ip->lock`
sleeplock으로 보호한다.

`ilock()`은 `acquiresleep(&ip->lock)` 후 inode가 아직 valid하지 않으면 disk에서 dinode를 읽어 채운다. 이 과정도
`bread()`와 `brelse()`를 거치므로 sleep 가능한 lock이 필요하다. `iunlock()`은 `holdingsleep()`으로 소유 여부를
검사한 뒤 `releasesleep(&ip->lock)`을 호출한다.

## 주의점

- sleeplock 내부의 `lk`는 sleeplock 상태 보호용 spinlock이다.
- `sleep(lk, &lk->lk)`의 channel 값은 sleeplock 구조체 주소다.
- `wakeup(lk)`은 같은 channel에서 자던 모든 process를 runnable로 만들 수 있다.
- 깨어난 process가 모두 lock을 얻는 것은 아니므로 반드시 `while`로 조건을 다시 확인한다.
- `holdingsleep()`은 내부 spinlock으로 `locked`와 `pid`를 확인해 현재 process가 lock 소유자인지 반환한다. `bwrite()`,
  `brelse()`, `iunlock()`은 이 값이 false이면 panic한다.
- `kernel/file.c`는 `sleeplock.h`를 include하지만 직접 `acquiresleep()`을 호출하지는 않는다. 실제 inode sleeplock
  사용은 주로 `kernel/fs.c`에 있다.
