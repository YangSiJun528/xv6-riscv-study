# xv6 Kernel 21 코드 정리: Process creation

## 읽는 순서

- `docs/reference/scripts/ko/21-xv6-kernel-21-process-creation-Grv5HTe4560.md`
- `kernel/proc.h`: `struct proc`, `struct trapframe`, `struct context`
- `kernel/proc.c`: `proc_mapstacks`, `procinit`, `allocproc`, `freeproc`, `proc_pagetable`, `proc_freepagetable`, `userinit`, `forkret`
- `kernel/exec.c`: 현재 repo의 첫 user program 적재 경로인 `kexec`
- `kernel/trap.c`: user mode 복귀 준비 함수 `prepare_return`

## 핵심

21강은 process를 "실행 중인 프로그램"으로만 보지 않고, 커널이 관리하는 리소스 묶음으로 보는 강의다.

```text
process =
  proc[] slot
  + kernel stack
  + trapframe
  + user page table
  + user memory
  + scheduler context
  + 열린 파일/cwd 같은 부가 상태
```

생성 코드는 이 리소스를 붙이고, 해제 코드는 같은 리소스를 반대 순서로 뗀다. 이 대응을 먼저 잡으면
`allocproc()`, `freeproc()`, `proc_pagetable()`, `proc_freepagetable()`이 훨씬 읽기 쉽다.

```text
부팅 때 slot마다 준비:
  proc[] entry
  kernel stack page
  p->kstack

allocproc() 때 준비:
  pid
  state = USED
  trapframe page
  empty user page table
  context.ra = forkret
  context.sp = kernel stack top

proc_pagetable() 때 추가:
  TRAMPOLINE mapping
  TRAPFRAME mapping

freeproc() 때 제거:
  trapframe page
  user page table
  user memory
  proc metadata
```

주의할 점은 kernel stack이다. xv6는 process가 끝날 때마다 kernel stack을 free하지 않는다. `proc_mapstacks()`가
부팅 때 `proc[]` slot마다 하나씩 만들어 두고, slot이 재사용될 때 같은 stack을 다시 쓴다.

## `struct proc`: 리소스 묶음의 중심

```c++
struct proc {
  struct spinlock lock;

  enum procstate state;
  int pid;
  struct proc *parent;        // wait_lock으로 보호

  uint64 kstack;              // slot에 붙어 재사용되는 kernel stack VA
  uint64 sz;                  // user memory size
  pagetable_t pagetable;      // user page table
  struct trapframe *trapframe;
  struct context context;

  void *chan;
  int killed;
  int xstate;
  struct file *ofile[NOFILE];
  struct inode *cwd;
  char name[16];
};
```

`trapframe`과 `context`는 둘 다 register 저장소지만 역할이 다르다.

```text
trapframe:
  user/kernel trap 경계에서 user register를 저장한다.

context:
  scheduler와 process kernel thread 사이의 swtch() 상태를 저장한다.
```

## 부팅 때 준비되는 것

`proc_mapstacks()`는 process slot마다 kernel stack page를 하나 할당해 높은 kernel VA에 매핑한다. `procinit()`은
그 VA를 각 `proc`에 저장하고 slot을 `UNUSED`로 둔다.

```c++
// proc_mapstacks()
for (p = proc; p < &proc[NPROC]; p++) {
  char *pa = kalloc();
  uint64 va = KSTACK((int)(p - proc));
  kvmmap(kpgtbl, va, (uint64)pa, PGSIZE, PTE_R | PTE_W);
}

// procinit()
for (p = proc; p < &proc[NPROC]; p++) {
  initlock(&p->lock, "proc");
  p->state = UNUSED;
  p->kstack = KSTACK((int)(p - proc));
}
```

여기까지는 특정 user program이 생긴 것이 아니다. process slot과 kernel stack을 미리 준비한 상태다.

## `allocproc()`와 `freeproc()`

`allocproc()`은 `UNUSED` slot 하나를 `USED`로 바꾸고, process가 처음 kernel에서 실행될 수 있는 최소 상태를 만든다.

```c++
found:
  p->pid = allocpid();
  p->state = USED;

  p->trapframe = (struct trapframe *)kalloc();
  p->pagetable = proc_pagetable(p);

  memset(&p->context, 0, sizeof(p->context));
  p->context.ra = (uint64)forkret;
  p->context.sp = p->kstack + PGSIZE;

  return p;
```

`context.ra = forkret` 때문에, 이 process가 처음 스케줄되면 `swtch()`의 `ret`이 `forkret()`으로 간다.

실패하거나 좀비 process를 수거할 때는 `freeproc()`이 같은 리소스를 되돌린다.

```c++
static void
freeproc(struct proc *p)
{
  if (p->trapframe)
    kfree((void *)p->trapframe);
  p->trapframe = 0;

  if (p->pagetable)
    proc_freepagetable(p->pagetable, p->sz);
  p->pagetable = 0;

  p->sz = 0;
  p->pid = 0;
  p->parent = 0;
  p->name[0] = 0;
  p->chan = 0;
  p->killed = 0;
  p->xstate = 0;
  p->state = UNUSED;
}
```

`freeproc()`은 `p->kstack`을 지우지 않는다. kernel stack은 slot 소유라 남겨 두고, trapframe/page table/user memory만
process 생명주기에 맞춰 해제한다.

## `proc_pagetable()`과 `proc_freepagetable()`

`proc_pagetable()`은 빈 user page table을 만들고, user/kernel 전환에 필요한 두 mapping만 붙인다.

```c++
pagetable = uvmcreate();

mappages(pagetable, TRAMPOLINE, PGSIZE, (uint64)trampoline, PTE_R | PTE_X);
mappages(pagetable, TRAPFRAME, PGSIZE, (uint64)(p->trapframe), PTE_R | PTE_W);
```

이 시점의 page table에는 아직 user code/data가 없다. `TRAMPOLINE`은 모든 process가 같은 trampoline code page를
가리키고, `TRAPFRAME`은 같은 VA지만 process마다 다른 `p->trapframe` page를 가리킨다.

해제할 때도 이 차이를 유지한다.

```c++
void
proc_freepagetable(pagetable_t pagetable, uint64 sz)
{
  uvmunmap(pagetable, TRAMPOLINE, 1, 0);
  uvmunmap(pagetable, TRAPFRAME, 1, 0);
  uvmfree(pagetable, sz);
}
```

- `TRAMPOLINE`: shared code라 mapping만 제거한다.
- `TRAPFRAME`: mapping만 제거한다. 실제 trapframe page는 `freeproc()`이 `kfree()`한다.
- user memory `0..sz`: `uvmfree()`가 user pages와 page-table pages를 정리한다.

즉 page table 안의 모든 PA를 똑같이 free하지 않는다. 무엇이 shared이고, 무엇이 process 소유인지 구분한다.

## 첫 user process

강의의 전통적인 xv6는 `userinit()`이 `initcode`를 VA 0에 복사한다. 현재 repo는 다르다.

```c++
void
userinit(void)
{
  struct proc *p = allocproc();
  initproc = p;

  p->cwd = namei("/");
  p->state = RUNNABLE;

  release(&p->lock);
}
```

현재 repo의 `userinit()`은 `/init`을 아직 load하지 않는다. 첫 process가 처음 CPU를 얻어 `forkret()`에 들어갔을
때 파일 시스템을 초기화하고 `/init`을 `kexec()`한다.

```c++
void
forkret(void)
{
  release(&p->lock);

  if (first) {
    fsinit(ROOTDEV);
    first = 0;

    p->trapframe->a0 = kexec("/init", (char *[]){"/init", 0});
    if (p->trapframe->a0 == -1)
      panic("exec");
  }

  prepare_return();
  ((void (*)(uint64))trampoline_userret)(satp);
}
```

`kexec()`은 `/init` ELF segment와 user stack을 새 page table에 올리고, `trapframe->epc`와 `trapframe->sp`를
설정한다. 그 뒤 `prepare_return()`과 trampoline `userret`을 거쳐 user mode에서 `/init`이 시작된다.

## 현재 repo 기준 차이

- 강의: `userinit()`이 `uvminit()`으로 `initcode`를 VA 0에 복사한다.
- 현재 repo: `uvminit()`/`initcode` 경로가 없고, 첫 process가 `forkret()`에서 `kexec("/init")`한다.
- 강의: `usertrapret()` 이름을 사용한다.
- 현재 repo: 같은 역할이 `prepare_return()`과 trampoline `userret`으로 나뉜다.
- 강의: `fork()`라는 커널 내부 이름을 쓴다.
- 현재 repo: syscall wrapper는 `sys_fork()`, 실제 커널 구현은 `kfork()`다.

## 한 줄 정리

process creation은 `proc` slot에 리소스를 붙여 실행 가능한 껍데기를 만들고, 실패나 종료 시 그 리소스를 어느
소유권 기준으로 되돌릴지 정하는 코드다. 현재 repo의 첫 user program은 그 껍데기가 처음 `forkret()`에 도달했을 때
`kexec("/init")`으로 채워진다.
