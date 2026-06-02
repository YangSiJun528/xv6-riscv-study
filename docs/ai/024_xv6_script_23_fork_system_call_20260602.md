# xv6 Kernel 23 코드 정리: fork system call

## 읽는 순서

- `docs/reference/scripts/ko/23-xv6-kernel-23-fork-system-call-MnTQi1IZTUM.md`
- `kernel/sysproc.c`: `sys_fork`
- `kernel/proc.c`: `kfork`, `allocproc`, `forkret`
- `kernel/vm.c`: `uvmcopy`
- `kernel/trap.c`, `kernel/trampoline.S`: child가 user mode로 복귀하는 경로

## 핵심

`fork()`는 부모와 거의 같은 새 process를 만든다. 차이는 반환값이다.

```text
parent:
  fork() returns child pid

child:
  fork() returns 0
```

둘 다 같은 user code 위치, 같은 user memory 내용, 같은 열린 파일들을 가지고 이어서 실행한다. xv6에서는 copy-on-write가
아니라 `uvmcopy()`로 부모의 user memory를 새 physical page들에 복사한다.

## syscall entry

user `fork()`는 syscall path를 거쳐 `sys_fork()`에 도달하고, `sys_fork()`는 실제 구현인 `kfork()`를 호출한다.

```c++
uint64
sys_fork(void)
{
  return kfork();
}
```

## `kfork()` 전체 흐름

```text
kfork
  -> allocproc()                    새 proc slot/trapframe/page table/context
  -> uvmcopy()                      부모 user memory 복사
  -> trapframe copy                 부모 user register 복사
  -> child trapframe a0 = 0         child의 fork 반환값
  -> filedup/idup                   열린 파일과 cwd reference 증가
  -> parent 설정                    wait_lock 필요
  -> RUNNABLE                       scheduler가 고를 수 있게 함
  -> parent에는 child pid 반환
```

현재 repo 코드에서 핵심 부분만 보면 다음과 같다.

```c++
int
kfork(void)
{
  struct proc *p = myproc();
  struct proc *np;

  // 1. child process 껍데기를 만든다.
  if ((np = allocproc()) == 0)
    return -1;

  // 2. parent user address space를 child page table로 복사한다.
  if (uvmcopy(p->pagetable, np->pagetable, p->sz) < 0) {
    freeproc(np);
    release(&np->lock);
    return -1;
  }
  np->sz = p->sz;

  // 3. user register 상태를 그대로 복사한다.
  *(np->trapframe) = *(p->trapframe);

  // 4. child에서는 fork()가 0을 반환해야 하므로 a0만 바꾼다.
  np->trapframe->a0 = 0;
```

부모와 자식은 같은 위치에서 실행을 재개한다. 그 위치는 `usertrap()`이 `ecall`을 처리하며 `epc += 4` 해둔
`fork` syscall 다음 명령어다. 다만 부모의 `a0`에는 `syscall()`이 넣은 child pid가, 자식의 `a0`에는 위 코드가
넣은 0이 들어간다.

## memory copy: `uvmcopy()`

현재 repo는 lazy allocation을 고려한다. 부모의 address space 크기 `sz` 안에 아직 실제 physical page가 없는
구간이 있을 수 있으므로, PTE가 없거나 invalid이면 건너뛴다.

```c++
for (i = 0; i < sz; i += PGSIZE) {
  if ((pte = walk(old, i, 0)) == 0)
    continue;                                  // lazy: 아직 PTE page 없음
  if ((*pte & PTE_V) == 0)
    continue;                                  // lazy: 아직 physical page 없음

  pa = PTE2PA(*pte);
  flags = PTE_FLAGS(*pte);

  mem = kalloc();                              // child용 새 physical page
  memmove(mem, (char *)pa, PGSIZE);            // parent page 내용 복사
  mappages(new, i, PGSIZE, (uint64)mem, flags);
}
```

이 구현은 부모와 자식이 같은 physical page를 공유하지 않는다. 그래서 fork 직후 한쪽이 memory를 수정해도 다른 쪽에는
영향이 없다.

## file/cwd reference 복사

```c++
for (i = 0; i < NOFILE; i++)
  if (p->ofile[i])
    np->ofile[i] = filedup(p->ofile[i]);
np->cwd = idup(p->cwd);
```

파일 descriptor table은 `proc`마다 따로 있지만, 그 안의 `struct file` 객체는 reference count를 올려 공유한다.
따라서 fork 후 부모와 자식은 같은 open file object를 가리킬 수 있다.

## parent 설정과 lock 순서

```c++
// allocproc()은 np->lock을 잡은 채 반환한다.
// child는 아직 USED 상태라 scheduler가 실행할 수 없다.
// 여기까지 pid, trapframe, page table, context가 준비되어 있다.
if ((np = allocproc()) == 0)
  return -1;

// np->lock을 잡은 상태로 child의 실행 상태를 대부분 채운다.
// 부모 user memory를 child page table로 복사한다.
if (uvmcopy(p->pagetable, np->pagetable, p->sz) < 0) {
  freeproc(np);
  release(&np->lock);
  return -1;
}
np->sz = p->sz;

// 부모 trapframe을 복사하되, child의 fork() 반환값만 0으로 바꾼다.
*(np->trapframe) = *(p->trapframe);
np->trapframe->a0 = 0;

// fd table entry와 cwd는 새 객체를 만드는 것이 아니라 ref count를 올려 공유한다.
for (i = 0; i < NOFILE; i++)
  if (p->ofile[i])
    np->ofile[i] = filedup(p->ofile[i]);
np->cwd = idup(p->cwd);

safestrcpy(np->name, p->name, sizeof(p->name));

// np->lock을 놓기 전에 부모가 반환할 pid를 저장한다.
pid = np->pid;

// parent는 wait_lock 보호 대상이다.
// wait_lock은 p->lock보다 먼저 잡아야 하므로 np->lock을 놓고 이동한다.
release(&np->lock);

acquire(&wait_lock);
np->parent = p;
release(&wait_lock);

// 모든 초기화와 parent 설정이 끝난 뒤에야 RUNNABLE로 공개한다.
// 이 시점 이후 scheduler가 child를 실행할 수 있다.
acquire(&np->lock);
np->state = RUNNABLE;
release(&np->lock);

// parent의 fork() 반환값은 child pid다.
return pid;
```

`np->pid`를 `np->lock`을 놓기 전에 `pid`에 저장하는 이유는, lock을 놓은 뒤에는 이 proc이 실행되고 종료되어 slot이
재사용될 가능성까지 고려해야 하기 때문이다.

`parent`는 `wait_lock`으로 보호된다. 그런데 `wait_lock`은 `exit/wait` 쪽에서 `p->lock`보다 먼저 잡는 lock이다.
그래서 `kfork()`도 `np->lock`을 잡은 채 `wait_lock`을 잡지 않는다. 이 순서를 지켜야 lock 순환 대기를 피할 수 있다.

## child의 첫 실행

`allocproc()`은 새 process의 kernel context를 이렇게 만들어 둔다.

```c++
p->context.ra = (uint64)forkret;
p->context.sp = p->kstack + PGSIZE;
```

scheduler가 child를 처음 고르면 다음 흐름이 된다.

```text
scheduler()
  -> swtch(&c->context, &p->context)
  -> forkret()
  -> prepare_return()
  -> trampoline.S:userret
  -> sret
  -> user mode에서 fork() 다음 명령어 실행
```

`forkret()`의 `first` 블록은 첫 process에서만 `/init`을 `kexec()`하기 위한 코드다. 일반 fork child는 `first == 0`이므로
바로 `prepare_return()` 경로로 간다.

## 현재 repo 기준 차이

- 강의: 커널 내부 함수명을 `fork()`로 설명한다.
- 현재 repo: 실제 구현 이름은 `kfork()`이고, `sys_fork()`가 이를 호출한다.
- 강의: `usertrapret()`로 user 복귀를 설명한다.
- 현재 repo: `prepare_return()`과 trampoline `userret`이 그 역할을 나눠 가진다.
- 현재 repo: lazy allocation 때문에 `uvmcopy()`가 missing/invalid PTE를 `continue`한다.

## 한 줄 정리

`fork()`는 `allocproc()`으로 child 껍데기를 만들고, 부모의 user memory/register/file 상태를 복제한 뒤, child의
`trapframe->a0`만 0으로 바꿔서 같은 지점에서 다른 반환값으로 다시 시작하게 만든다.
