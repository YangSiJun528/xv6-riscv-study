# xv6 Kernel-23: Fork System Call

| Field | Value |
| --- | --- |
| Video ID | `MnTQi1IZTUM` |
| URL | <https://www.youtube.com/watch?v=MnTQi1IZTUM> |
| Source | `docs/reference/scripts/original-raw/23-xv6_Kernel-23_-_Fork_System_Call-MnTQi1IZTUM.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'll describe how
the fork system call works whenever a process wants to create another process it uses the fork
system call and this is the only way that processes are created with the exception of the creation
of the initial process the process that does the creating is said to be the parent process and it

creates a child process the child process is a clone of the parent process and almost exactly
identical to the parent process when the parent makes the fork system call the kernel becomes active
and then at some point the fork system call returns and when it returns both processes now exist and
so in both processes there will be a return from the system call

one major difference is that the fork system call returns the child's process id while the fork
system call returns zero in the child process the code in the both processes the parent and the
child will typically take a look at the return value and that code can then determine whether it is
running in the child or the parent process and can then take actions differently depending on
whether

it needs to to behave as a child or parent if there is any difficulty such as we run out of memory
and we can't allocate the pages for the child the fork system call will return -1 in the parent so
what's involved in creating the child process well we do several things and then we return the
process id of the child the first thing we do is call the alec proc function

this will search for an unused proc structure and grab it and begin the initialization process it
starts by assigning a new process id to that proc structure it also creates an empty address space
and adds mappings for the trampoline and trapframe pages then after alec proc returns we need to
copy the virtual address space so every data page from the parent

process is copied and added to the child's virtual address space the permissions on each data page
in the child will be set to be exactly the same as the permissions on the corresponding page in the
parents virtual address space and then we initialize and set a few of the fields in the proc
structure the size field is the number of bytes in the virtual address space that is

since the virtual address space starts at zero that's just the same as the break address at the top
of the heap so that's copied next we copy the trapframe when the system call was made in the parent
process all the registers of the user process were saved as well as the program counter and those
were saved in the trapframe and by copying the trapframe it means

that the child process when it's scheduled again and runs in user mode will have exactly the same
values in all of its registers and it will begin executing at the same spot namely directly after
the e-call instruction that did the fork system call and then we modify register a0 to set it to 0
so that the child process will see a return value of 0.

each process has a name and we copy the name field so the child process will be uh given the same
name as the parent process each process has a number of open files we copy all of the file
descriptors for open files in the parent process into the child process so that any file that was
open in the parent process will then be open in the child process we also copy the current working

directory so the child will be in the same working directory and we then set the parent pointer in
the ch in the child's proc structure to point to the parents proc structure this is the other
difference between the child process and the parent process they each have a different location in
the parent-child hierarchy of processes and finally we set the state to runnable

and at that point we're done creating the child process it's runnable and it will be scheduled when
it's given a chance so at this point the system call is able to just simply return the process id of
the child that has been created next let's look at that in code so here is the fork process in the
file proc dot c the system call initially invoke invokes cis fork cis fork simply calls fork and

whatever fork returns cis fork will return so we begin by getting a pointer to the parents proc
structure and then we call the alec proc function if there are any problems then we return -1 and
we're done but otherwise we save a pointer np is a new process I guess so we have a pointer to that
process alec proc will also acquire the lock on the newly created

proc structure and it will initialize the virtual address space and in this step we invoke uvm copy
to copy all of the data pages from the parent into the child's virtual address space so these are
pointers to the page tables and that's the size of the address space which indicates the number of
pages that need to be copied if there are any problems then we

free this proc structure this zeros out all the fields and returns everything to the free memory
pool and then we release the lock on that structure and return minus one but assuming everything's
good we keep going uh here we make a copy of the size field and here we copy the trapframe so this
is where we are copying all of the registers that were valid in user mode

as well as the program counter from the parent to the child so the child can resume executing it
exactly the same place and here we modify a0 to clear it to zero so that the return value will be
zero in this uh code here we are running through the open files of the parent and for each file that
is open we make a duplicate of that and add it to the child here

and here we're making a duplicate of the current working directory and copying that so the child
will have the same current working directory here we are copying the parent's name into the name
field of the child and here we grab the process idea of the child and we're going to be returning it
right down here then we see that we are updating the parent pointer

of the child to point to the parents proc structure whenever the parent pointer is accessed either
read or modified we must be holding a lock called weight lock so we acquire that lock here and then
we release it here and finally we set the state of this process to runnable and release the lock
that was acquired up in alec proc and then return at that point

the child process has been created and will get scheduled whenever uh the scheduler gets around to
it we also see something else going on here we see uh during this code right here we first release
the lock on the child and then reacquire it and we also notice that we don't just return the pid
field here we copy it to a variable and then return that this is because we need to acquire this

field while we are holding a lock on the process it's possible that you know the minute we release
that lock this proc structure will get scheduled it will run it will exit and uh another uh fort
call will grab this process and a new pid will be assigned highly unlikely that all the secret
sequence of events could occur but in an operating system we have to assume that

anything that can occur will occur and so if we just simply grab the pid field from the proc
structure after releasing the lock we could in fact get the wrong number so that's why we grab the
process id while we're still holding the lock but still we haven't re uh figured out why we are
releasing this lock and then reacquiring it so for that I want to go back and remind you about
deadlock

so here's a simple deadlock situation we have two processes and we have two locks and I'm going to
show that lock a is currently held by process one with a solid arrow and I'm going to show that lock
b is currently held by process two with a solid arrow now let's assume that process 1 wants to
acquire lock b that is it has called the acquire function and it's trying to obtain a

lock on the lock b which is impossible because it's held by process 2.

so the acquire function will be spinning here meanwhile process 2 is wanting lock a so it calls
acquire and it is spinning tied up in the acquire function waiting on lock a this is a deadlock
deadlock in general occurs whenever we have a cycle in one of these graphs that involves processes
and locks here I'm showing a very simple cycle but we can have more complex cycles but this gives
the idea

of what deadlock is there's a cyclic weight situation process one can't proceed because it can't get
lock b process 2 can't proceed because it can't get lock a and so they're both frozen now in our
situation we I haven't yet talked about the code and exit and wake up but we do have this situation
the weight lock is concerned whenever um we are basically looking at the parent

child hierarchy and when a process exits we need to notify uh its parent so the weight lock will be
acquired in the exit um function and we also have a function wake up and wake up goes through all of
the potential parent processes it goes through all processes in fact and grabs all of the locks on
those processes so um we have this situation where at any at some point in

this these two functions we might have a situation where the weight lock is held and we are trying
to acquire the lock on a particular process now in fork we are needing to grab the weight lock to
modify the parent the parent field so we see that uh right here we are calling acquire to grab the
weight lock okay now what if we were holding a lock on some process okay

then we would have the arrow going here and that would be our deadlock situation and we can't allow
that so this is uh an explanation for why we must release the lock on the process before we attempt
to acquire the lock called weight lock next let's take a look at what happens in the child process
at some point the scheduler will select the child process and will invoke switch

switch will load the registers from context these are the registers that were saved previously in
most cases but in this case they have been just initialized and then switch will do a return well
register r a was loaded from context and this return will effectively just jump to the instruction
that's pointed to by ra when the fork system call created the child it called alec proc

and among other things that function initialized the context for this process it zeroed out all the
registers and it saved in r a the address of this function called forkrat and it's saved in the sp
register the stack pointer that is in fact the page that's pointed to by the k-stack field in the
proc structure and since stacks grow downward we save the pointer to the top of that

page so we have a fresh stack and a starting address in this function called for crept which we'll
look at in just a second but before that let's review what happens in a trap so remember this
picture we're showing a trap and we save the registers in uservec usertrap figures out what's going
on let's say it's a timer interrupt and we call yield because I want to look at yield a little bit
more

and yield does some stuff as we saw it called switch eventually but ultimately yield returns and we
come back and then user trap rep is invoked user ret is invoked and these restore the registers and
set things back up and then return to the user mode with the esret instruction so let's look at this
pathway in a different way let's assume we've got a timer interrupt

so we've got a trap that goes into uservac and usertrap saves the user mode registers and then calls
the yield function well what does yield do yield basically just calls skid after acquiring a lock on
the process here here's the code for yield to review it so you see yield it acquires the lock well
in the case of yield it changes the state to runnable uh in

the case of a child process it's already runnable so uh keep that in mind but then it calls sked and
then after sched comes back it releases the lock and then returns so uh we call scan here and what
does skid do well skid does some error checking uh here's the sched function it does some error
checking primarily and essentially just calls switch and then returns

and so uh when switch returns uh we are back in sched sched returns and then uh yield uh becomes
active uh does its last statement which is to unlock the process and then it returns to user trap
rep which immediately calls user trap ret and then that calls user rep and then that calls that
executes the srett instruction which pushes back in user mode switch does uh

some things basically it unlocks the process and goes off and invokes a scheduler and may execute
some other processes but eventually it requires the lock and at that point switch will load all the
registers and return to ra okay and normally that would be a return address in the sked function but
in the case of a child process we've pre-loaded ra with forecret so

when switch returns it will be returning not to some address in scad where it can finish this
process here but it will be essentially jumping to fork red well what does forecret do it unlocks
the lock on the process and calls user trap rent so essentially it does this right here it takes us
into this point so we can complete the return process user trap rep will load the user

registers remember we've saved a copy of the parents user registers we've also modified a0 to b0 but
this is how the user registers were saved and they will be loaded by user trapret and it will call
user rep and ultimately the s-red instruction will happen and we will resume execution after the
call to fork or more precisely after the e-call instruction so just to complete this let's take a a

look at fork reps code here is the four code for four gret and you can see it does exactly what we
said um it is uh going to um release the lock on the process and invoke user trap ret this code here
is only executed the first time for the first time slice and that would be for the init process when
uh it gets going and this just does a little initialization it'll set first to

zero and from then on this code will never get executed so we can ignore that it won't be involved
for any child process okay that's how the fork system call works I'll see you in the next video
