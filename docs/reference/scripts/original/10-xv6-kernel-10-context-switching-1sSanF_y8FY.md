# xv6 Kernel-10: Context Switching

| Field | Value |
| --- | --- |
| Video ID | `1sSanF_y8FY` |
| URL | <https://www.youtube.com/watch?v=1sSanF_y8FY> |
| Source | `docs/reference/scripts/original-raw/10-xv6_Kernel-10_-_Context_Switching-1sSanF_y8FY.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I will talk about
context switching I'll talk about how traps and system return instructions are used to implement
time slices and I'll talk about how the system switches from one thread to another let's start with
this picture here and I'm showing time flowing down the page here's a user thread and

this indicates instructions are being executed until at some point a trap occurs and then we switch
into the kernel and execute instructions in kernel mode user thread instructions are executed in
user mode and anything executed in kernel mode is by definition part of the kernel the switch from
user mode to kernel mode occurs as the result of a trap this can

be an interrupt such as from a device that is requesting attention or it could be requested by the
user thread itself in the form of a system call or perhaps the user thread has a program exception
some sort of an error and that results in a trap as well after the kernel executes and is ready to
return to the user thread the s-ret or system return instruction is executed

and that takes us back into user mode this could be a device requesting attention so if it's an
interrupt the user thread will be unaware that this trap has occurred and every and the user thread
will just uh be executing instructions and really have no clue that the trap and return happened at
the point of the trap all the registers of the user thread should be

saved and will be saved and at the system return those registers will be just restored or reloaded
if you will and so the user thread just picks up where it left off so the user thread experiences
over time a series of time slices it happened here's one and here's another and here's another it
this trap could be the result of a timer interrupt in which case uh the

kernel will go off in this region and do something else with some other threads but eventually we'll
come back to user thread x so if the trap is a timer interrupt we have the end of the time slice and
at some later time the kernel will decide to give the user thread another time slice and then we'll
return to the user thread so uh what here's a different view of it

and here I'm showing the scheduler thread in red on the left and we've got several threads okay x is
executing down here and thread y is executing over here and from the point of view of thread x you
say it executes along it gets a trap and then at some later time a return happens and it continues
executing and then it traps and then at some other point a return happens and it resumes

while the kernel is active it may choose to run a time slice for thread y so the system return goes
back to thread y and that executes until thread y gets a trap so one interesting thing to note in
this picture the trap is followed by the return sort of like a call is followed by a return so the
user thread can imagine that it is calling the kernel particularly with

a system call and that the kernel eventually returns to it here it's sort of different because the
return and trap are sort of reversed so this trap is not followed immediately by return but it's
followed by some other stuff so from the scheduler's point of view we go into thread x and begin its
time slice with the system return instruction and the time slice ends with a trap

instruction I want to look at a little bit what happens um when the trap occurs let's go back to
this diagram when the trap occurs the kernel executes and then there's a system return this is uh
this diagram is what I call the road map my road map and that's pretty complicated but basically we
have user code executing it executes and there's a trap and then down at the

bottom there's a return and then the user code resumes so a lot of stuff is going on here and I'll
come back to this in detail but among other things the trap happens it could be as a result of a
timer interrupt or a device interrupt those are both asynchronous the in the sense that they happen
uh from outside the user code and the user code will be unaware that they've

occurred or it could be a system call the user code might have asked for something from the kernel
or it could be that the user code has some sort of an error condition so we see you know saving the
registers and the program counter happens really early and then way down here at the bottom before
we do the system return we see restoring user registers and then

the sret instruction and what goes on here well we have a big if statement and we might have a
device requesting attention so here we have to run the the handler for this particular device maybe
the user code asks for a system call so we have to deal with that maybe it was a timer interrupt or
maybe it was a program exception but at least in these three cases we all

come down here and ultimately go back to the user code now one thing I want to talk about next is
the um fact that we could have uh things going on on a multi-core system so here I just showed the
scheduler uh executing thread x and then thread y and then thread x that's uh for a single core
system in this picture I'm showing more or less the same thing but for two cores

so here you see the scheduler for one core in blue okay and over here we have another core shown in
red and here we have some processes x y z and w and you can see the scheduler decides to give
process z a time slice at this point and so it does a system return into process z until process c
finally has a trap and we go back to the scheduler for this core and then maybe it selects some

other cores and does some other stuff meanwhile this core is executing along and and perhaps at this
point it decides to give process c a time slice so at that point it does a system return into
process z and process z gets another time slice from the point of view of process z we see that it
got a time slice on this core and then it had to wait for a while and

then it got a time slice on the other core there could be additional cores as well so the time
slices for any process can happen on any core it's just a matter of well more or less chance in the
case of xv6 so what happens at this trap this is the trap where we go back into the kernel and at
this point the registers for process z will be saved at this context switch

all of z's state on the core on the core is saved namely its general purpose registers and its
program counter and then at some later time core this red core over here decides to give z a time
slice so it does another context switch into process z and it will at that point load registers into
the core zero that will the registers that process z needs to execute

now at this point when the state of process z is saved it will be saved into memory so it's shared
memory shared amongst all the cores and at this context switch that shared memory is accessed and
the state is loaded from the shared memory back into the registers of a core that shared memory
where we are storing the state of process c is of course critical and needs to be protected with

locks and will be protected with locks so while that state is being saved at this contact switch a
lock will be held and only after that it's done is that lock released and freed up and so that
prevents core this red core over here from trying to start the time slice of process c too soon so
only after the red core is able to acquire the lock can it then begin to

load process these registers and do the context switch into process z now I showed the uh time is
flowing down the page here we do a trap into the kernel and then we do a return back from the point
of view of the user's thread sometimes we see it drawn differently here in this diagram up here time
is flowing to the right so we see the trap occurring from user mode into kernel

mode and then at the system return we have a context switch from kernel mode back into user mode if
it's a simple uh operation then we sort of have this picture for example if it's a device it's
requesting service the trap is a an interrupt we're able to handle the device and deal with the uh
device without doing any further contact switches and then we go straight back

into the interrupted user mode also in the case of some system calls it may be that we can service
the system call without doing any further context switches and go straight back to the user mode
code and that's pretty efficient but in other situations we can't so here I've drawn a slightly more
a different situation here we are executing along in user mode

and we do our trap and now we're executing in the in the kernel but it in some sense it's the same
process okay it's not we're not really in the scheduler thread yet the scheduler thread is indicated
in brown as a separate thread down here so here we're in the thread the kernel thread for this user
process and if we decide that we want to schedule another thread then we we will

do a second context switch so we will then go into this scheduler thread select another another
process and then go to that processes kernel thread so each process has a user portion and a kernel
portion I'm showing one thread in red here okay this is the user portion that executes in user mode
and this is the kernel portion that executes in kernel mode but it's all part of one

thread if you will and I've also shown a second thread in blue here so the scheduler thread is a
different thread and there's a context switch from the process into the scheduler's thread and then
another context switch back and this is a little bit different than the ones that are going on here
when we switch from user mode into kernel mode we need to save all of the

general purpose registers in the program counter we need to save everything okay down here when
we're switching to the scheduler thread we're already in the kernel and we can make some assumptions
we don't need to save quite all of the registers and it's a little bit different this context switch
and this contact switch are both handled in a an assembly language function

called swtch or switch I guess we can say that's assembly code it's called in these two functions
when the scheduler chooses which uh process to go to it will cause it that's done in this function
called scheduler and it will call switch to to to make that context switch there's another function
which I guess we can call skedge s-c-h-e-d and that is invoked by

the kernel portion of a processes thread to go into the scheduler so it too will call this switch
function so the switch function basically will save registers from one thread and load the registers
for the next thread and as I said it's written in assembly code okay that's it for context switching
and the trap and system returns we'll come back to the gigantic road map later

in future videos
