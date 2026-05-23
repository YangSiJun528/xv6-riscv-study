# xv6 Kernel-25: Sleeplocks

| Field | Value |
| --- | --- |
| Video ID | `nc03pTD53Ro` |
| URL | <https://www.youtube.com/watch?v=nc03pTD53Ro> |
| Source | `docs/reference/scripts/original-raw/25-xv6_Kernel-25_-_Sleeplocks-nc03pTD53Ro.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

In this video I'm going to describe sleeplocks and contrast them with spinlocks this video is part
of a series on the xv6 operating system kernel with any locking system there are two primary
functions acquire and release sometimes these are called lock and unlock but for spinlocks the
functions are called acquire and release and they're each passed a pointer to a

structure representing the spinlock the key with the spinlock is that it must not be held for very
long and in particular between the acquire function and executing the release function you are not
allowed to go to sleep so there is no time slicing and what the acquire function will do is loop in
a tight loop looking for the lock or waiting for the lock to be released and

this is very practical on multi-core systems because the lock can generally be held by a core
another core and it will release the lock quickly because it must not hold the lock for very long so
the busy loop or the spin loop inside the acquire function won't have to wait for very long before
it finds that the lock has been released so the acquire will never wait for very

long and this also happens to imply that the release will always be performed on the same core that
did the corresponding acquire but the problem is what if you need to wait for a long time between
acquiring a lock and releasing it and in particular what if you need to execute the sleep function
and suspend the execution of the process between the acquire and the

corresponding release well if you were to hold the lock for a very long time then with a spinlock
the acquire would have to loop in its tight loop waiting for that lock to be released and that would
essentially tie up the core and so this is just not going to work instead what we have are
sleeplocks and sleeplocks are very similar to spinlocks in that they have an acquire and release

function however you get to hold the lock while you go to sleep in the case of the xv6
implementation the acquire functions called acquire sleep and there's a corresponding release sleep
both of these are passed a pointer to a structure that represents the sleep lock there's also a
function called a knit sleep lock which is used to initialize the structure each structure also
carries a name field

which is initialized and never changed and that can be used for debugging it's not really relevant
to the functionality of it and there's also a function to check to see whether the current process
is holding a given lock and that's called holding sleep and it returns true if the current process
is holding the lock the way this is used is just for error checking and if it ever

returns true the kernel immediately panics okay here's the representation of a sleep lock the
structure contains four fields and the first field is a spinlock which is just used to protect the
remaining fields as I said the name is initialized and never changed so it's not really protecting
the name field but it's protecting the key field which is this boolean called locked

if true the sleep lock is being held and if it's true then the pid will contain the process id of
the process that's actually holding the lock and if it's false the lock is free and unheld okay now
let's take a look at the code the sleeplock.h file contains the structure that is used to represent
a sweep block and nothing more that structure contains the fields lk

locked name and pid and here we see that lk is a spinlock the locked is the boolean that's true if
and only if the lock is held and then we have the name string and the pid uh the process id that is
currently holding the lock if any that are used for debugging the file sleep lock dot c contains the
functions the initialize function is passed a pointer to one of these structures as

well as a name string and it initializes all four fields lk lock name and pid the lk field is a
spinlock so we have to call this function to initialize all spinlocks we give it an arbitrary name
we save the name field and we set the locked to indicate that this sleep lock is currently not held
it's in the unlocked state and we also clear out the process id the acquire function has passed a

pointer to a sleep lock and essentially what it's going to do is it's going to acquire the lk
spinlock set the locked field to true to indicate that it's held and also save the process id of the
process that invoked this function and then release the spinlock but we also need to check the lock
field because it's possible this sleep lock is currently being held by some other

process if it's false we immediately skip the while loop but if it's true we go to sleep and then
when we wake back up we check the lock field again and if it's unlocked this time we then can
proceed otherwise we keep sleeping until eventually we can find that it's unlocked and we can
proceed now remember how the sleep works it's passed a channel and a spinlock

and the spinlock must be held upon call to the sleep function and what sleep will do is it will
release the spinlock and go to sleep as one atomic operation and it does this so that no wake ups uh
will be missed uh so uh it won't uh release the spinlock until it's certain that it is asleep uh the
channel is an arbitrary number uninterpreted by the sleep and

wake-up functions that's just used to coordinate the wake up and sleep so we're using the address of
the sleep lock and when any process releases a sleep lock it will notify all processes that are
sleeping using that same channel number that is it will notify all processes that are waiting for
that lock to be released and so when we are reawakened the sweep

function will reacquire the lock and then we can check the lock field again now as several processes
are reawakened that is if several processes are trying to acquire this sleep lock only one of them
will be given the spinlock the others will have to spin waiting for it but whoever gets it will then
be able to check the locked field and possibly proceed if it's unlocked the others when

they get the spinlock we'll find that someone else got there first okay here's the release function
it acquires the spinlock and then sets the status to unlocked also clears out the process id field
and releases the lock but before it releases it it also wakes up all the other processes or any
other process that is waiting for this particular sleep lock and finally we have the holding sleep

function which is just used for debugging this thing returns a boolean and if it ever returns uh
false we will be calling panic but basically it just takes a look at the lock field and make sure
that it is set but before we look at the lock lock field or the pid field we need to obtain this
sleep lock so it obtains the lk sleep lock sorry we need to obtain the spinlock so before it looks
at

these two fields it obtains the lk spinlock and then it checks these fields and if there's any uh
and then gets that and returns that uh true if it's locked by the current process and then of course
it releases the spinlock before returning okay that's it for sleeplocks see you in the next video
