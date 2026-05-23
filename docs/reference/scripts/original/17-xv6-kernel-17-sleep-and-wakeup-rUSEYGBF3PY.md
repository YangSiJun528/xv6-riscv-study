# xv6 Kernel-17: sleep and wakeup

| Field | Value |
| --- | --- |
| Video ID | `rUSEYGBF3PY` |
| URL | <https://www.youtube.com/watch?v=rUSEYGBF3PY> |
| Source | `docs/reference/scripts/original-raw/17-xv6_Kernel-17_-_sleep_and_wakeup-rUSEYGBF3PY.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to talk
about the sleep and wake up functions I will use as an example the system called sleep which is
dealt with in this function here any process can call sleep to put itself to sleep and that will in
the current time slice and change its status from running or runnable to a status of sleeping and
the

process will then remain sleeping until some other process calls wake up so we have this problem
though of exactly which processes to wake up when some process calls wake up so when we go to sleep
we need to provide some kind of information about when we want to be woken up and then wake up
should not wake up every single sleeping process but it should only wake

up a certain subset of those processes and the question is how do we identify which processes to
wake up and with xv6 we have something called a channel the channel is just a simple number um it
could be anything really it's uninterpreted by the sleep and the wake up functions and for example
it might be the number 37 so a process could sleep on the number 37

and then when wake up is called it's past a channel that is some number if it's uh waking up on 37
it will wake up that process but if wake up is called with some other number like uh 54 it will only
wake up the processes that are sleeping on 54.

so that's how the channel works there are other approaches used in different systems sometimes you
provide a list of threads here so you sleep on a particular set or list of threads and then wake up
is applied to a particular list of sleeping threads and it will wake up everything on that list some
systems also have condition variables and a condition variable is really

fundamentally nothing more than a list of sleeping threads but this is how xv6 works the channel is
a field and it's just kept in the proc structure so every process has a field that will store the
number that it's sleeping on if its status is sleeping and then the way wake up works is it goes
through all the processes in other words it iterates through the entire

proc array and it will wake up any process that is sleeping with the matching channel number so next
let's uh show how these functions are used so I'm going to look at a typical usage so in code we
would like to check conditions and only proceed if the condition is true otherwise we want to wait
for that condition to become true and so here's the typical pattern we see

in this while loop we are checking to see whether the condition is true and if it's not yet true
then we call sleep and we're going to wait until the condition is updated it may be that some
process will some other process will update variables and those variables are somehow involved with
that condition the other process may not really know what condition we're waiting on but

we just basically wake up the other process will wake up any process that's waiting on those
variables that are involved in that condition so it may wake up a little too many processes uh it
may wake up several processes that are waiting for some changes in the shared data it may be that
the condition is true or not true it may be that one of them will grab the condition and find that
it's

true while others will find that it's false and go back to sleep so we have to recheck the condition
upon waking up however there's a problem with this particular pattern here and that is we're
concerned about what would happen if the condition might become true sometime between being checked
and the sleep being executed we've got other processes running both

on this core another course and it could be that our time swipe ends right here and other processes
run and um they may make the condition true and furthermore they may issue a wake up and it we would
miss it because we haven't yet executed our sleep so we don't want to miss any wake ups after
checking this condition it could really be bad if there are no further wake ups in which case we go
to

sleep and never wake up so here's an example from the code that we will look at in a second the
sleep system call is handled in this function and here's an outline of what the code does first of
all it grabs the argument to the system call which is how long we want to sleep and that's measured
in a unit called ticks every time the timer interrupt goes off it's said to be a tick

and there's some global variable that is shared amongst all cores and it contains the current tick
count that's the the time that represents the current time it's updated by core zero whenever core
zero gets a timer interrupt so it's a global shared data and we're grabbing the the time that we
start the weight and then we here's our condition we're checking to see whether

n ticks have gone by and if not then we go to sleep and we sleep until this shared variable is
incremented again we also have a little check in here to see whether the process has been killed and
if so we abort our weighting but that's beside the point so ticks is a global shared variable and it
is protected or we could say guarded by a lock if you want to look at or modify ticks

you need to first acquire ticks lock and so we have these requirements that we've got to hold ticks
lock while we're looking at ticks so for example right here when we check the condition and we don't
certainly don't want to miss a wake up but we've also got this other condition that we cannot hold
locks while sleeping remember that spinlocks must be released quickly and in particular we

cannot hold a spinlock while we're sleeping so we've got a bit of a problem here and the solution is
this our code is going to look uh like this we're going to uh acquire the lock in this case it's the
tix lock whatever lock is required in order to check the condition must be acquired before we look
at the condition so we acquire the lock and then we check the condition again we go to sleep and

then uh wake up and check it again but the difference is that we are now passing a pointer to the
lock that is being held to the sleep function and what's the sleep function going to do well it must
release that lock before we go to sleep but it's going to do that in an atomic way so the lock will
be released and we will go to sleep in such a way that we can be guaranteed

that we won't miss any wake ups and then at some later point the uh some other process will change
our status from sleeping back to runnable and the scheduler will select us and we'll be changed to
running and at that point this process will wake up still in the sleep function and the sleep
function will then reacquire this lock before returning so once we loop back and we are holding the

lock when we check the condition again and we keep loop looping until finally the condition is found
to be true in which case we exit the loop and then we do something with our shared data and finally
we must release the lock so the acquire and the release are paired here and in a sense they're also
paired here okay so now let's look at the function cis sleep and see what that actually

looks like in code and here you see a call to this function that will obtain our first argument and
store it in this variable in and then we're acquiring ticks lock here we're accessing the shared
variable ticks and grabbing the starting time which is a local variable here's the condition uh
that's being checked and then here we see a call to sleep and we're providing a pointer to the ticks

lock and finally after the loop exits we release the ticks lock and return we also see a check to
see whether the killed flag has been set and if so we immediately release the lock and return and uh
the code that calls us will then notice that kill killed has been set and terminate the process but
that happens elsewhere um so what about the channel here we are using

the address of the shared variable here as our channel so when we sleep we sleep and we pass a the
address of the tix global variable and that's okay uh it is a number before we look at how sleep is
implemented let's uh review yield because they're really quite similar um both will stop the process
from running and put it back on the uh you know in a dormant

state um and uh so yield will change the state to runnable okay but first it acquires a lock on the
process so when we call sched here we have to be holding a lock on this process sched will release
that lock and then when the process is scheduled again it will reacquire the lock and then we come
back here and release the lock and keep going but that's what yield does and sleep um is

is really quite similar so here's the code for sleep so we see uh the acquire of the lock this
acquires the processes lock and here we uh change its state we don't change it to runnable we change
it to sleeping so when the scheduler encounters this process uh from now on it will see that it is
not runnable and it will ignore it so it will remain sleeping until some other process changes it
back

to runnable so we change our state to sleeping we call skid here and then when sched returns we
release the lock and return however we see a couple of other things going on we see that we are past
a pointer to some spinlock and here we are releasing that spinlock so we acquire the lock to the
process and the order here is critical we acquire the lock to the process first

and then we release the spinlock and so we're not holding the spinlock while we are sleeping but
then after we come back we reacquire the spinlock and it doesn't really matter this order here is is
not critical but so we release the lock first when in doubt we prefer to release locks as soon as
possible before we do acquiring so let's see the code oh we also have the channel that's passed and

here it's just a pointer to some address and we see that we're storing the channel in the field of
the proc structure and then after we are woken back up we clear that field just as a matter of
tidying up next let's take a look at the wake up function the wake up function is passed a channel
and it goes through all the processes and wakes up any and all that are

sleeping on that channel it has a loop and it goes through the proc array one by one and for each
process it asks is the state of that process sleeping and is the channel field of that process equal
to the channel that we are using as an argument and if so it changes the state to runnable so that
the next time the scheduler encounters that process it will be given a time slice

the field state and channel and some others are protected by the lock on the process which means we
need to acquire the lock before we access those fields and we see that acquire occurring here and
the corresponding release right here we also see this test right here which is asking is the current
process that we're looking at equal to the current process that's running this code itself

the comment says that this function must be called without holding a lock on any process and so if
that's true then um this test is really uh superfluous uh we are looking at the state of the process
and obviously the current process the one that is running will have a state of running and not
sleeping so it would seem that this test is really unnecessary it may be that this is an artifact of

the history of this code or it may be that somewhere in the xv6 code that I'm not aware of this
function is actually called while holding a lock on the current process in any case this test is
here and it certainly doesn't hurt anything other than maybe taking a little bit of extra time now I
want to go back to the uh pseudocode that we looked at for how showing how sleep is used

uh remember that we are typically using sleep in a loop where we're looking for some condition and
this condition is checked by looking at some shared variables and we acquire a lock before looking
at the shared variables and we need to release the lock before we go to sleep before we actually
begin the sleeping state because these are spinlocks and we cannot uh allow them to be held while we

are sleeping we need to release them quickly uh without any uh delays like a sleeping process would
have so we need to release the lock before we actually go to sleep and that has to happen in the
sleep function and it does happen in the sleep function and we said that this has to be atomic so I
wanted to just comment on why it is atomic in other words if a wake up comes along

that matches our channel here it will either execute before this group of instructions or after this
group of two instructions it won't execute uh in the middle so we will either still be holding the
lock in which case we can be certain that our condition is still false and so we don't need to be
awoken or it happens after here in which case we will be properly awakened and then reacquire the

lock and go to check again the condition so the way that works is is that the within the sleep
function recall that we are acquiring a lock on the process uh before we're releasing the spinlock
okay and likewise notice that in the wake up function we are acquiring a lock on the process before
we consider possibly waking it up so it's that acquire that the spinlock

sorry this uh this lock on the process can only be held by uh one thread at a time so either the
wake up gets there first and acquires a lock on the process in which case the um the sleep code is
is still somewhere above this acquire and still holding the spinlock lk or the acquire in the um
sleep function gets there first and acquires the lock in which case it

releases the um spinlock lk but also sets the channel and the state fields before actually going to
sleep and so later uh well this this lock on the process will be immediately released once we get
into the sched function and at that point wake up can then acquire the lock for that process and can
be sure that it will be found to be sleeping and the channel to have been set so then

it will wake it up so that's how we guarantee the atomicity atomicity of these two operations here
okay that's it for this video see you in the next video
