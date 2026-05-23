# xv6 Kernel-16: Scheduling + swtch.S

| Field | Value |
| --- | --- |
| Video ID | `-O_JX5mMMHY` |
| URL | <https://www.youtube.com/watch?v=-O_JX5mMMHY> |
| Source | `docs/reference/scripts/original-raw/16-xv6_Kernel-16_-_Scheduling_+_swtch.S--O_JX5mMMHY.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel scheduling is probably the most
interesting part of the kernel in my opinion and in this video I'm going to take a look at it I will
look at these three functions I'll pronounce this one skid so I'll look at functions yield skid and
scheduler and I'll also look at some helper functions I'll look at CPU id my CPU and

my proc and I will go over the assembly code that's in the switch...S file so let's begin with these
helper functions since they're pretty straightforward and easy to get out of the way first the cpuid
function will simply return the value in the tp register that register will always contain the
number of the core that we're currently executing on since we could have interrupts and

things could change at any moment we really need to be called with interrupts disabled to make sure
that the value is not out of date the minute we return it the my CPU function will get the
identifier for the number for the current core and then it will look up the CPU structure in the
array so remember this picture we had showing that there's one CPU struct for

each of the eight cores and so all we're doing here is returning a pointer to the structure that
describes the data for this particular core and the myproc function will go to that structure we'll
get that structure so it'll call my CPU and it will go to the proc field and follow it and return
that so the CPU structure will contain a pointer to the currently executing procedure

sorry process and we'll return a pointer to the proc structure it could very well be null if we are
not executing any process at the moment but we return that now my CPU has to be called with
interrupts disabled and myproc is called from all over the place so one thing we do is call push off
to disable interrupts here that will also increment the counter then we call

papa to restore the interrupts to decrement the counter and if it goes back to zero to restore the
interrupt status to whatever it was before okay now let's take a look at this road map that we
showed earlier and this shows everything that happens between the trap and the srep instruction user
code is being executed and we get a trap and we see what's going on and in

the case of a timer interrupt we will call the yield function so I'm going to be focusing on the
yield function I kind of hinted at some stuff here but I will now show this area here in more detail
after yield returns we go back to the user mode function and continue executing one thing I will
notice that when the trap occurs interrupts are disabled and along this path where we

call yield interrupts remain disabled and so interrupts are disabled for the yield function so uh
looking at that area more closely we see that the trap happens and we have a lot of stuff going on
and we determine that it's not a system call and not a device that needs interrupt handling and we
end up calling the yield function and so this is what happens and

ultimately we return from yield and basically do some other stuff and execute the s red and go back
to the user mode code yield calls skid so here I'm kind of indicating that yield will call skid not
much happens in yield and sched just a few statements are executed really um and we call a function
switch so this is the processor thread so we are executing in user mode for the

particular process p and then we are executing here in kernel mode for process p and at this point
when the switch occurs we switch from process p to the scheduler thread and then we execute the
scheduler thread the scheduler thread may do some other stuff it may invoke some other it may do
time slices for other processes but eventually it will come back and

the call to switch here will return and we'll continue executing in the sched function and then the
yield function and then we'll do some restoring of the user state and return so let's take a look at
what happens here in the yield and scad functions before we call switch we're going to examine and
modify the state of this process p while we are running the state is

running it is it's running but we're going to change it to runnable uh which means it's no longer
running and waiting for another time slice so we acquire the lock on process p itself and change its
state to runnable and we also save this uh field interrupts enabled of the CPU structure okay let me
just uh review the CPU structure not only do we have the pointer to the proc that we're executing

but we have two fields that are used by push-offs and pop-off in-off is a counter and interrupts
enabled is the previous status and then we also have this context area where we will save registers
so we're going to save that and then later we will restore that field and release the lock on p and
return to uh the user mode code so after we call switch some other stuff happens and

switch ultimately will return and I'm indicating it right here and then we come back and finish the
execution of the sked function and the execution the yield function and so on now what happens in
the scheduler thread well once we resume executing the scheduler thread we will change the CPU's
proc field to null because we're no longer executing this particular process and we'll release

that lock and then we'll do some other stuff and eventually uh we will choose to run p again in the
interim we may get some other processes time slices but eventually we'll come around and this core
or perhaps some other core will choose to give p a time slice and at that point we will acquire the
lock on p again and change its state to running and we will then

set the proc field for the current CPU remember it could be a different core that we're executing on
we'll set the current CPU structs proc field to point to the process that we are now starting which
is p and then we'll use switch to go back into the scad function and then the yield function okay
switches is where the interesting stuff happens so I want to talk about that next

in the CPU structure we have a register straight save area where we can save registers r a s p and
the s registers and also in the proc structure we have a register save area where we can save those
same registers when we switch from the process p will save the current registers of process p in the
proc structure in that register save area and we'll load the registers for

from the CPU field from the CPU structs register save area and when we go back from the scheduler
thread to the thread of process p we will save the schedulers uh registers in the CPU struct for
that particular core and we will load the registers from process p's register save area okay next
let's take a look at switch switch is written in assembly code and we see here that it's past two

pointers both pointers are pointers to the context so essentially we're passing a pointer to this
context here and that context there in the proc structure in the RISC-V system whenever a call is
made to a function we will or the I should say the instruction that does the call will save the
current program counter in the return address register the ra register and it will

jump to the first instruction of the proce of the function that we want to call the return
instruction and there's one right here will take the value that's in the ra register and move it
into the pc thereby going back to the caller if a function doesn't call any other functions then we
can leave the return address in that register and we don't need to save it on the stack

and in this particular function we see nothing is pushed or popped onto the stack so that's what's
happening here in the risk five uh calling conventions we assume that any function can update and
use and modify and overwrite the a registers and the t registers so uh anybody who calls switch will
not be able to make any assumptions about the values of the s

sorry the a and the t registers after it returns so that's why we don't bother saving them but what
do we do we save all the other registers we save the ra and the stack pointer and we save all the s
registers and where do we save them well the first parameter the pointer to the old context where we
save the state of the previously executing thread is in a0

the pointer to the context for the thread we're going to be executing next is in the context pointed
to by the new argument which is in a1 so a0 is where we save everything here we see a store double
word and we store registers r a s p and the s registers the gp register is not used in the xv6
kernel so we don't bother with that the tp register contains the number of

the core we're currently executing on and that won't change we are executing on a certain core when
we do this saving and we are still executing on that core so we don't change the tp register so
after we save the state of the previously executing thread we then load the state of the next thread
using the pointer to the new context and we load those exact same registers

so here we're saving the return address from the function that called us and here we're loading the
return address from some earlier thing we saved and so this return will not return directly instead
it will return to something else so that's it's kind of kind of weird so in this picture here that I
showed here we're calling switch and here we're returning from switch

but this call that we are executing here to switch doesn't return immediately to the same thread it
returns to some other thread likewise this call here returns over to this thread okay so that's
switch now let's take a look at the yield function and see how and walk through that code yield give
up the CPU for one scheduling round so the primary thing it does is it

calls skid okay but before it does that does it gets a pointer to the current process we're
executing the process p gets a pointer to the the proc and then it acquires the lock remember that
the proc structure has a lock field that protects things like the state we're going to be changing
the state from running to runnable so that's what we do we acquire the lock

and then we change the state to runnable then we call sked and we go away and maybe quite a while
later after some other processes have had time slices ultimately the scheduler function we'll call
switch again and we'll return back to this thread so at some point sched will return and at that
point we release the lock that we acquired here yield is called in exactly two places

it's called from user trap and kernel trap so whenever a trap occurs when we're running in user mode
we uh execute user trap and if we've had a timer interrupt we will call yield and likewise if we're
executing in supervisor mode we have a function called kernel trap and if the timer has gone off we
will call yield but in any case uh we will be calling it from a trap handler

interrupts will be disabled and at that time no locks will be held now here we do an acquire and
then a release as you remember the acquire is a spinlock and what it will do is it will disable
interrupts and then it will remember whether they were enabled previously or not well they are not
so it will save that status in this interrupts enabled previously field

will always be false and since no locks are held uh it will increment the counter for the locks the
in off counter and it will increment it from zero and so it will be one so at this point when we
call sked the in off field will be one and interrupts will be disabled okay so now let's take a look
at what happens in skid so we are grabbing a pointer to the my proc field

and we are asking uh uh doing some error checking here we make sure that we are holding the lock
okay well we acquired it right here sked can be called sked can be called from some other places as
well but let's just focus on this call from yield so we make sure that we're holding the lock we
make sure that n off is one and we make sure that the state is not running

and then we also make sure that interrupts are disabled so if interrupts are enabled this would
return true and we would print an error message so now we're going to save the value of int ena
interrupts enabled so in this particular case when we're calling it from yield it will always be
false so we're going to save that state but if we call schedule sked from someplace

else it might be true so in any case we save the previous value of int ena interrupts enabled and we
save it in a local variable and then we restore it later after the call to switch from that same
location is this local variable kept in a register or is it kept on us on the stack we don't know
exactly what the compiler will do but it doesn't really matter all

we know is that the state of this thread will be entirely saved and whether this local variable is
on the stack or in a register it doesn't really matter the point is that is saved in this in in some
data that this thread has and it will be restored so then we restore the value of it and then we
return and release the lock now let's take a look at the scheduler

function and uh see what it looks like so it's uh pretty short it just goes from here down to here
and it contains an infinite loop okay throughout this function c is set to the um pointer to the CPU
structure for this particular core each core will execute its a single thread for the scheduler
function and this function will you know be executed on one core of course it'll

be executed on the other course simultaneously but let's just focus on this one particular core so
we get a pointer to the CPU struct that describes this core and then we've got an infinite loop here
and uh inside of that we've got another loop so let's look at this loop first what is this doing
well it's going through this proc array one by one okay so it's uh here's our array

we have 64 processes and it's going through each one of them one by one and looking at the proc
structures and we see that's going here and for each one it asks whether it's runnable or not and if
so it calls switch so we acquire the lock for that process right we're going to be looking at the
state variable and changing the state variable so we need to hold the lock

while we're doing that so we acquire the lock on this particular process and if it is runnable we do
this if it's not runnable we release the lock and move on and look at the next one and when we've
gone through them all we then repeat the outer loop and uh start from the beginning beginning again
if the process is runnable then we're going to change its state to running

okay we've selected it we're also going to store in the CPU struct a pointer to the relevant proc
structure so that we know which process we're running and then we're going to call switch and here
we see the call to switch we're providing it a pointer to the context for the current core and a
pointer to the context for the new process that we're going to be executing so we're passing

switch for the old argument a pointer to the area in which to save the registers for the scheduler
thread and in the new argument we're passing it a pointer to the context area for the proc structure
and that's where to load the registers for the process that we're about to start executing so we
call switch and then after switch comes back we're no longer executing that process so finally

we set the proc field to null and so let me just show how that's working here now let me look at it
this way here so here I'm showing it from the user's point of view we have a trap we go into switch
the scheduler executes give some others some time slots time slices and eventually we come back
execute the s return back in the user the user's code but now let's look at it

from the scheduler's point of view we are choosing some process p to run acquiring the lock on p and
changing its state to running and we are also updating the proc field and then we're switching into
that process and then at some point later that process will give up the CPU okay timer if nothing
else a timer interrupt will happen and cause a trap or it may

be something else but in any case the timer interrupt causes a trap and we uh come back into yield
and then we call switch again only when we call switch in yield we will provide the context in the
reverse order so just let me go in there uh we call yield we call sked and in sked uh calls switch
and this time the old is the processes context so that's where we're going to

save the registers from the processes registers we're going to save the processes state and we're
going to load the registers for the scheduler thread so we're going to go to the my CPU struct and
use its context so uh so then switch comes back so we set proc to p and invoke switch then uh some
something goes on eventually get the timer interrupt yield happens and we have

switch and then we're back in the scheduler thread so we then finish off our loop by setting the
proc field to null and we release the lock on p so we see exactly that going on here we call switch
we come back we set proc to null and then we release the uh lock for p and then we iterate now one
thing uh I do say something that we say choose p I say here choose p and then acquire the

lock on p well in order to choose p we have to find one that's runnable and we can't look at the
state without locking it first so actually we do things in a slightly different order we acquire the
lock then we look at the state to see if we found one that's runnable but that's just a minor detail
the switch function is called in exactly two places it's called in the sked function

to go into the scheduler function and it's called in the scheduler function to go back into some
process okay so whenever it's called in scad it is always switching from a process to the scheduler
thread and when it's called in the scheduler thread it's always going from the schedule thread into
a process so if we look at the code for sked here we see that switch is being called

and it's being passed a pointer to the old context where it saves the state of the previously
running thread and a pointer to the new context so it will save the current registers in the context
for the process that is that it is leaving and it will load the registers from the context area for
the CPU that is the scheduler thread okay that's going in this direction okay in this direction

it's called from the scheduler function okay I'm showing that right here here's the scheduler
function and we call switch here and it's going from the scheduler function or the scheduler thread
to the process to some process that's now going to be running so it will save the scheduler's
registers in the context associated with the current CPU the current core and it

will load the registers for the process that's going to be executing next another thing I want to
mention is that we always have acquires and releases of locks being paired so if we look in the
yield function for example we see an acquire and a release so here's the yield function and we see
that this locked is being acquired here and released here and if we look in the scheduler function

we see a similar kind of thing going on so here's the scheduler function and we're acquiring the
lock and we're releasing it and it would seem that this acquire in this release are paired up but
that's not really quite right so here we have the lock being acquired in the yield function and it's
a spinlock we shouldn't hold spinlocks for very long and we see it being

released very soon after in the scheduler function and here we see the lock being acquired in the
scheduler function and then after just a few instructions being released in the yield function so
they are paired up but they're paired in sort of a funny way for example we might be acquiring this
lock when running on one core and release it pretty soon after and then later it may

be that some other core decides to choose p and to run it so it will acquire that lock and then
begin that process running and then the yield function will release that lock so in fact the lock
could be acquired on one core and released on another core another thing I want to point out is that
whenever we call the switch function and we switch from a process to

the scheduler thread we will always have interrupts disabled and we know that for sure because well
here's the sched function where we call switch and right before that we check to make sure that
interrupts are disabled so interrupts are always disabled when we come back into the scheduler
function so if we look at the scheduler function we see that we are going um

here we call switch but then at some point we come back okay so we return from switch from after
executing a processor's time slice and at that point or this point here interrupts will be disabled
and we release the lock but that interrupts may remain disabled and we loop back now uh if we go
into another process right if we go into another process we choose another runnable process and we

switch into it then we're going to do the switch and we come back here now this yield was being
executed as part of the user trap function and interrupts are disabled at the time the trap occurs
and they will remain disabled for the yield function so this acquire here will remember the previous
state of the interrupts and this release will then not re-enable the interrupts at this point

but we restore the state of the user registers and so on and then we execute the srat instruction
and at that point interrupts will be enabled and so they will become enabled when we go into the
next process but it's possible that there won't be a next process I mean it's it's possible that all
of the processes on the in the array are not runnable so we we might not be able to

find one that's runnable and what what would we do we would just um if we go through the entire
array and don't find anything that's runnable then we uh come out of this this for loop here and we
go back to iterate this while loop here and we see that we're turning interrupts on so we could get
into a situation where there are no runnable processes it could

be that we're waiting for um that all the processes are are sleeping or waiting on something it
could we could be waiting on user input for example and so there may be no runnable processes and we
could get into a situation then well if they're disabled when we come out of this switch and we
don't re-enable them here um then they stay disabled because we we're never

finding a runnable process so that's what's going on here so you see this comment avoid deadlock by
ensuring the devices can interrupt so we do turn the interrupts on if we go through the entire array
and uh every time we go through the entire array once we turn them on at least for a tiny amount of
time but it's long enough for the interrupt to occur and the trap to be uh

handled and that will possibly wake up some other processes so that's what's going on right here all
right this this uh stuff with the process switching I I just find it the most interesting part of
the kernel and I hope you've enjoyed this video as much as I've enjoyed making it all right I will
plan to continue making videos although there may be a slight delay uh look

forward to seeing you in the next video
