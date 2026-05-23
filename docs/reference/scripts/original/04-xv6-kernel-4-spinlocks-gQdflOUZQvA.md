# xv6 Kernel-4: Spinlocks

| Field | Value |
| --- | --- |
| Video ID | `gQdflOUZQvA` |
| URL | <https://www.youtube.com/watch?v=gQdflOUZQvA> |
| Source | `docs/reference/scripts/original-raw/04-xv6_Kernel-4_-_Spinlocks-gQdflOUZQvA.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to
describe spinlocks and tell you how they're implemented in the xv6 kernel first let's start off with
remembering what spinlocks are the idea is that the spinlock is represented with a single word and
I've called that word locked here and that word has one of two values it's either zero or

one if it's zero that means the lock is free or sometimes we say it's unlocked or it has been
released and if the value is one then the lock is said to be held or acquired or locked these are
the common values that are typically used and xv6 does that as well okay so here is the structure
that xv6 uses for a spinlock there are two other fields here name and

CPU which you can see are just used for debugging so here's what a spinlock looks like in addition
to the key field that tells the state of the lock we have a pointer to a string which could be used
for debugging and we have this field called CPU which points to some other structure every core has
a structure associated with it called a CPU structure and this field contains a

pointer to the structure for the CPU that's currently holding the lock okay so the important
functions on a spinlock or for or any lock really are acquire and release in addition we have a
function that is uh to be called when the lock is first created that initializes the lock uh
basically it's past the name that we associate with the lock once the name is set it does not change

we've also got a function that's used for error checking to determine whether the current core is
holding the lock each of these functions is passed a pointer to one of these spinlock structs so
it's past a pointer like this pointer here okay uh to acquire the lock typically what we do is we
just set we want to set the field to one but before we do that we need to check

to make sure that it's not currently being held so our first pass at coding the acquire function
might look like this check to see whether the lock is free and if it is free then set it to one and
if it's not free then loop back and keep trying so these are spinlocks they spin it's a tight loop
that keeps checking until it finds that the field is zero it sets it to one and then

at that point the lock is acquired well of course this has a problem if there are other threads that
are executing concurrently in the case of xc6 we've got multiple cores so this one particular field
in memory may be accessed simultaneously by other cores or perhaps if there's uh thread switching
going on within the single core perhaps some other thread within the same core is trying to access

the same memory location and we could have both uh we could have two threads simultaneously check
this field and just happen to find that they are both uh that if the lock is unlocked and then
simultaneously both set the lock to one and and so that's going to cause a problem because they both
now think they are holding the lock so we have to uh find another way to do it this code will

not work and for that the RISC-V architecture has an instruction called amo swap atomic memory
operation swap and this single instruction will do two things it will copy a value into a word of
memory and at the same time it will retrieve the previous value of that word and it will do it
without interruption so it will not allow any other threads or any other

instructions whether from this core or another core to do anything between the retrieval of the
value and the setting of the value so here's how we're going to use that atomic memory swap
operation to correct the problem that this code has we're going to execute the atomic swap operation
which I'm indicating schematically here we're going to write a 1 into the location and we're also

going to retrieve the old value the previous value if the previous value was 0 then we're done we
have acquired the lock but if the previous value was not 0 well it must have been one because there
are no other options then somebody else was previously holding the lock so we have to loop back and
try again so then we spin until finally we find the previous value was zero

and we then have acquired the lock the release operation is pretty straightforward we just copy the
unlocked value of zero into the word uh this doesn't it's hard to imagine how copying value into a
single location of memory can be anything but atomic so in some systems this is uh implemented as
just a single memory store operation okay now let's look at the code for

the acquire operation uh first of all we can take care of the init operation this is code that's
coming from the spinlock.c file initialize the spinlock we're passed a pointer to the structure and
the name we set the name field and it never changes after that we also set the CPU field to null
because the lock is not held and we set its initial value to zero

here is the code for the acquire function and here right here we see the while loop that does
exactly what I described previously this magic sync lock test and set is going to be turned into
some assembly code and the comment here is indicating that the amo swap instruction is going to be
happening and it talks about some registers but essentially we're passing it a pointer to the word

that we want to update and we're passing it the new value which is one and this function is going to
return the old value and we're checking to see whether it is zero or not and if it was not zero uh
then we repeat this while loop and if it was zero then we've acquired the lock and we're done now
there are a couple of other things I want to mention about this function

first once we acquire the lock we are storing into the CPU field every core has a structure
associated with it so there are in fact eight cores so there are eight structures and this function
here will retrieve a pointer or return a pointer to the structure for the core that's currently
executing so we're just saving that here up here we are uh checking to see whether before we

acquire the lock whether we already whether it's already um being held by us so we'll see the
holding function in just a second and if it is then something's drastically the matter so we um
cause an error message then we've got this uh synchronized thing going on here so tell the c
compiler and the processor not to move loads or stores past this point to ensure that the critical

sections memory references happen strictly after the lock is acquired this is a fence instruction so
we want to prevent the compiler for doing up from doing optimizations it might in fact reorder
things and that is not acceptable the anything that's supposed to be done after we acquire this lock
after this while loop completes must be done after that so that's what this sync synchronize does

it forces the compiler to emit code and the processor to execute that code in such a way that
everything that is supposed to happen before this point is completed and everything that is supposed
to happen after this point is not started until after this point we see one other thing here that's
kind of unusual and that is push off the comment says disable interrupts to

avoid deadlock holy smoke what's that I mean uh isn't this a lock situation here why why are
interrupts involved at all I'll come back to that in a minute so I'm gonna just uh leave you in
suspense but first uh let's take a look at the release operation we do a quick check to make sure
that we are in fact holding this lock and if not we print an error message and

then we're going to be releasing it down here and so we might as well go ahead and set the CPU field
to null at this point and here we are copying uh the zero into this field they're using an amo swap
instruction to do it with this sync glock release but like I said it's hard to imagine how a and a
normal stored of memory could be anything less than atomic and we've got

pop off well we've got the synchronize here up here which makes sure that anything that's supposed
to happen before we release the lock actually finishes and that anything that's and only after we
finish that do we actually set the locked field to zero so pop off is a partner a dual of uh push
off and so what it does is it enables interrupts and I'll come back to that in a second

but um first let's look at this holding function just to complete this uh code this just checks to
see whether the current core that's executing this is holding the lock okay it looks at the value of
the field and if it's one and the CPU is the same as this core then it returns true and otherwise it
returns false a spinlock should never be held for a long period of time

imagine a situation where one core is holding a lock and another core wants to acquire that lock
well the acquire function is going to be spinning in a tight loop waiting for the lock to become
free so as a general rule of thumb you should always uh plan to release the lock soon after it's
acquired in particular we don't want the thread that's holding the lock to go to sleep

or to get time sliced there are other techniques for locking in xv6 we have the sleep and the wake
up function and these can be used in situations where the lock needs to be held for a long period of
time locks are used to protect shared data actually the xv6 documentation has some interesting
wisdom they point out that locks really protect constraints and I think that's a nice

way to put it but in other in any case we usually or almost always see this particular pattern here
we acquire the lock then we access the shared data and then we release the lock the code here is
often called a critical section in other words it's critical it can only be executed by one thread
at a time so therefore this code is protected by a lock let's look at a quick example and I'm

going to imagine input a character input that's coming from somewhere and going to someplace else so
perhaps we have a keyboard and every time the user types on the keyboard an interrupt handler wakes
up and adds a character to a shared buffer so the buffer is the shared memory item and it's going to
be a fifo queue so the data goes in one end and there's some other thread that is

reading from the buffer when it needs a character it just needs to access this shared data structure
which we will need to protect and in this example we will protect it with a spinlock so here's the
code that we might see in the handler it acquires the spinlock adds a character to the buffer and
then releases the lock the thread that wants to get input is going to acquire the lock

so it can access the shared buffer remove a character and release the lock I'm not going to be
concerned with what happens if the buffer is completely full or completely empty the handler is
called when someone types a character on the keyboard and interrupt handlers begin with the trap
processing which will disable interrupts and then they'll do some stuff basically this stuff here

in the case of our example and then they will return to the interrupted code with the s rep
instruction which will re-enable interrupts so we might imagine a thread t that's running like this
an interrupt happens as a result of a key being pressed the handler runs and then when it's done
adding the character to the buffer it returns to the interrupted thread whatever it was and

continues well so you can imagine this situation if t happens to be this thread here that acquires a
lock and then at just the wrong moment the interrupt comes in from the keyboard and the handler is
invoked and the first thing the handler does is try to acquire that lock well it will wait until the
lock is released but unfortunately the thread that is holding the lock

is waiting for the handler to complete so we've got a deadlock situation operating systems we need
to remember that if anything if any combination of events can possibly theoretically happen it's
best to assume that it will happen no matter how unlikely or how rarely you think the event is our
first approach to solving the problem of deadlock is to disable interrupts in the acquire

function and then to re-enable them in the release function so we don't have a situation like this
if t acquires a lock it will disable interrupts so it uh cannot have a situation where some other
thread on that same core is trying to acquire the lock this also has an additional benefit we don't
want to hold spinlocks for very long so by disabling interrupts we are

preventing time slicing from happening while the lock is held and we are preventing interrupt
processing from occurring basically we're allowing the core to focus on the thread that is holding
the lock and to complete its critical section without interruption quickly and then release the lock
but now we have another problem what if interrupts are already disabled

for example in our handler code we have interrupts disabled and for a number of reasons we don't
want to re-enable interrupts prematurely before we get to the system return instruction we might
also have several spinlocks that we need to acquire and so we have perhaps three acquire functions
being called in a row and for each one of those we'll have three release functions

but the first release will uh disable sorry we'll re-enable interrupts and that might not be quite
what we want so essentially we want this release to return the interrupt status to whatever it was
before the acquire happened and we want to accommodate nested calls that is we want to accommodate
several acquire function calls followed by several release function

calls and our solution to this problem is simple we're going to use a counter and that counter
happens to be called in off and it's specific to a core this variable will be held in the CPU
structure each core has its own CPU structure and each one will contain its own counter so the
acquire function will increment the counter and then disable the interrupts

perhaps they were already disabled but they will be after the acquire for sure and what's release
going to do well release is going to decrement the count and to handle the nested case if it goes
back to zero then it's time to consider re-enabling them more precisely we need to ask whether they
were previously enabled before the first call to acquire so if the count is zero and they were

previously enabled then we will re-enable them that previously enabled status is kept in a variable
called interrupt enable int ena I guess and so we all we will remember the previous interrupt status
before the first call to acquire in this variable which is a part of the CPU structure okay now we
can take a look at the code for push off and pop off remember that in the acquire function

shown here the first thing we did was call push off to disable interrupts to avoid deadlock so now
we know what that means and in the release we also uh end up calling pop off here's the code for
release and right before we return we re-enable interrupts if appropriate so let's take a look at
push off and pop off push-off is called by acquire we immediately turn interrupts off we

disable interrupts well first we find out what the previous status of the interrupts was that's
called old and then here we're accessing the CPU structure and if this is the first call to acquire
that is if the counter is already is previously zero then we're going to save the old status but in
any case we're going to increment the counter pop off is as you'd expect

pretty similar we are accessing the counter and the int enabled variable here so we start by getting
a pointer to the CPU structure and then we decrement the counter at this point here and if it went
to zero and interrupts were previously enabled then we turn them back on at this point we also have
some uh error checking here if for some reason we call pop off and

interrupts are are already enabled something's drastically the matter so we print an error message
and we also make sure that the counter is not zero or smaller that would also be some sort of an
error condition okay that's it for spinlocks uh see you in the next video
