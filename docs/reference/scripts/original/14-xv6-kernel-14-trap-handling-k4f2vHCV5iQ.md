# xv6 Kernel-14: Trap Handling

| Field | Value |
| --- | --- |
| Video ID | `k4f2vHCV5iQ` |
| URL | <https://www.youtube.com/watch?v=k4f2vHCV5iQ> |
| Source | `docs/reference/scripts/original-raw/14-xv6_Kernel-14_-_Trap_Handling-k4f2vHCV5iQ.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this image we're executing in
user code we get a trap and we execute something in the kernel and then we execute a system return
instruction and go back to user mode in this video I'm going to talk about what happens when we're
in kernel mode I'll talk about the data structures in particular and I'll

do a code walk through of the proc file so let's uh begin with this uh thing that I call the road
map and it shows everything that happens between the Trap and the SR instruction and let's go
through this more carefully here uh the Trap could be caused as a result of an asynchronous
interrupt either the timer is requiring attention or an IO device is requiring

attention or it could be a synchronous exception due to a system call by the user mode code or some
sort of a program error the hardware will do several things it will disable interrupts it will
switch to supervisor mode it will save the program counter in scpc it will save some information
about what exactly caused the trap in this s cause register and it will load the PC

with the address of the userve function uh there is a control and Status register um called stvc and
that contains the address uh of the code that's supposed to handle any kind of a trap and so uh
that's how we Branch to this assembly code that is located in the trampoline page so this trampoline
page recall is mapped into all address spaces both the user address space and

the Kernel's address space and it does the initial saving of the user's state so what does it do in
particular well it saves all the general purpose registers and the program counter in the trapframe
each process has its own unique trapframe but they're mapped into the second highest page in the
user's address space this code is executed in the user's address space so then we get

ready to start executing kernel code and every thread needs its own stack uh so we uh have to
initialize the stack pointer register uh and we also initialize the TP register which contains the
core number these two registers would have other values in the user code we can't trust what values
they might have had so we need to load those but we don't really need to load anything else uh we

then load thep control and Status register with the pointer to the Kernel's page table so that will
switch us effectively into the Kernel's virtual address bace and at that point we then jump into
this function called user trap and this is C code and what does it do well it's got a n statement
and it figures out what exactly is going on but first it stores a pointer to the another

trap Vector in stvc previously stvc contained a pointer to this function here but if a trap occurs
while we're executing in kernel mode we want to handle it a different way so we update St back here
and then we look at the cause register the s cause register and determine whether it was some sort
of uh program exception if there's an error we print a message and

call a function exit which will terminate this uh process Al together if a device needs uh handling
well we call this device interrupt uh function here to take care of that uh there's a Boolean called
killed that's associated with each process and if it gets set we need to terminate that process so
if for some reason this process has been killed some somebody

else wanted this process to die well they would have set that Boolean to true and at this point we
check it and if it's uh the case that this process should die after handling the device interrupt we
then call exit but probably we just continue down here if uh the timer interrupted then it's time to
have the uh end of this time slice and give some other processes uh a chance to run

so we check the killed thing and if so we just exit uh but otherwise we call yield yield will
eventually return but what it will do is it will um cause the execution or allow the execution of
other processes it will invoke the scheduler and some other processes which I've hinted at here will
execute but eventually uh we'll get another time slice and return to yield and then yield will
return and

then we'll continue and go back into the user mode uh process if the cause of the interrupt was or
because of the Trap was a system call then we enable the interrupts okay so we This Thread itself
may be interrupted uh for example there could be a device requiring attention while we're dealing
with this system call interrupts are enabled uh and so that might actually occur we might uh uh go

off and do some other stuff but uh in any case we handle the system call and then we come back and
if that point somebody has asked for us this process to die then we call exit but otherwise we
continue and then finally whatever the case is we call a c function user trap r or user trap return
and it will begin the process of returning to the user mode code it will disable the

interrupts in in case they got enabled here and it will set stve to point to user VC so now uh this
is in preparation for the next trap so we need to have it point to user Fin and then we need to um
save into our trapframe values for the stack pointer and we need to save the core number that we're
executing on remember we we loaded the these here well here we're we're setting them up and getting

them ready to go for when the next trap occurs the SR instruction will rely on the scpc register so
we uh retrieve the saved program counter and we put it into the scpc register and then we jump into
some assembly code and this assembly code is located in the trampoline page it's called user rep and
this will complete the process and execute the SR instruction it will now that we're

executing the trampoline page uh we are in that top page that lives in every user add every address
space and so we can switch from the Colonel's address space to the users address space and we do
that by loading the satp register with a pointer to the users page table and then finally we restore
the users registers and we also um set a couple of bits in the status register uh the SR

instruction will use the status register and we set bits to to tell the SR instruction we want to go
into user mode we want to the previous privilege level uh will be user mode so we will be returning
to user mode and the previous interrupts enable flag we set to enabled so this will be copied into
the current interrupts enabled flag so when we execute the S interrupts will then

become enabled and we will be in user mode you running with uh the users registers and uh the users
page table and so one okay so let's now take a look at some of the data structures that are involved
uh first of all let's take a look at this trapframe while we're executing in user mode there's a
control and Status register called sratch and it will point to this

trapframe and that will be useful this contains the place where we will save the user mode registers
so up here when we save the uh registers in the PC and the trapframe that's going to happen and that
uh information will be saved here the 31 general purpose registers um will be saved here remembering
that the 302nd register that is register zero is is always uh

hardwire to zero so we only have to save 31 of them and the program counter the previous program
counter will be saved here so that's the state of the user mode thread and then uh we load up the
state for the kernel thread so uh we have a pointer to the Colonel's page table that's nice and
handy to have and that's right there we have a pointer to the stack that this particular process

will be using uh and then we have the address of uh user trap that's actually not really part of the
state of the supervisor thread but we have that in this trapframe as well and finally we have the
hart ID or or the core number if you will which we will need to load into the TP register when the
Trap occurs so that's what's going on on the trapframe we have some other crucial

and important data structures we have a data structure called CPU there is an array called CPUs and
there are eight of these one per core and each one of these elements in this array is a structure
and that structure contains these fields um if a user mode process is running on that c or then we
will have a pointer to the proc structure that describes it okay if nothing is running

on that CPU or I should say if the schedu or thread is running this proc field will be zero or null
and uh I talked earlier about uh interrupts and how we can sort of stack them uh we remember whether
initially they were enabled or not and then if we want to disable them then we uh remember the
initial value and uh increment a counter and then when we uh back off we can

decrement the counter and ultimately return to the uh original state of the interrupts enabled uh
bit and finally we have another register save area um called context now one thing I didn't really
make clear was that um when we're executing a process we're executing either in user mode or in
kernel mode but we may switch and I don't show this here but we may switch

over to the supervisor thread okay and we leave that process behind so we're really uh in all of
this uh what that we're talking about here we are executing as part of a process okay but in here
perhaps in here or or in the in the yield we will maybe go into another processes uh uh time slice
so uh we will have other contact switches that are different a context switch occurring

here is different than a context switch occurring here and so if we leave this process and go into
the schedu thread we will need to save the registers and so that's what this register save area is
all about here so uh then let me point out we don't need to save all of the registers um we only
need to save uh some of the registers um and we'll talk about that later if um

we are executing if this core is executing a processor thread then we'll have a pointer to a
structure that describes that process and it's got a number of fields so let me go through these
quickly we've got a lock field we have the current state of the process it could be unused used it
could be sleeping it could be runnable which means it's ready to go and and it could

be scheduled but it's just waiting for a Time slice it could be actually running or after the uh
process terminates we call exit it becomes a zombie for a little while before the uh structure is
then recycled and becomes unused um if we are waiting on some lock if this if this thread is
sleeping if it has the status of sleeping then um this channel uh describes exactly what it is

we're waiting on here's that killed Boolean that I mentioned after the process terminates it has an
exit status and that's what this U field is and every process has an ID number uh that's
sequentially given an ID number this lock protects these fields so let me zoom in a bit uh this lock
this spinlock will protect these fields in other words if any of these fields are going

to be modified or accessed we need to uh grab the spinlock we also have a pointer to our parents so
this is a pointer to one of these other proc structures uh there are up to 64 processes well there
are 64 processes some of them May be unused but we could have up to 64 processes uh running and
runnable at once and for each one of them there is a structure and this is

the structure and so this I'm showing just the structure for one process and parent points to
whichever structure is the parent process represents the parent process for this parent for this
process uh there is a stack for the kernel aspect of this process so when we're are executing here
okay when we're executing here we need to have a stack for the kernel to use each of the 64

processes needs a separate stack so that's uh given here by this this there's a pointer to that
stack here this is the size of the virtual address space and here's a pointer to the page table for
the user's address space and then we have a pointer to our trapframe page each process has a unique
page in physical memory and so this points to that page it will be mapped in they will

all be mapped into the second highest page in in uh the address bases but this is a pointer to the
physical page and then we have another one of these context things so this is another save area so
when we're switching back and forth between the the scheduler thread and the process we can use
these two save areas and uh we have an array here for open open files and the current working

directory we even have a space for the name of this process so we can give processes names okay now
uh that we've seen pictures of those let's go ahead and look at the definitions in this uh file proc
so this is a context um that was used both here and here and you can see it contains the ra that's
the return address Reg register and that tells you know that's the PC if you will where we

go back to and then the stack pointer and then we only need to worry about the S registers there are
12 s registers and and we only need to worry about those so we only need to have an area to save
those next let's look at the CPU structure this CPU structure here and we see that uh it contains a
pointer to the pro the process if it's it's running or null if not and we have an area for the

context the save area that I showed here and then we have uh these two fields that are the depth of
the push off every time we call push off it increments that that counter and uh this remembers uh
whether interrupts were enabled before the initial push off and then here we have our uh array of
well we have eight CPUs so it's an array of eight elements of these of these

structures uh the next thing we want to look at is the the um trapframe so I'm not going to read
through this information here but here's what's going on in the trapframe we're going to be
accessing this in assembly code so we actually care about these offsets the trapframe is a page and
this will be page aligned uh and these are the offsets uh given in comments here of

each of these fields you can see that each of these fields is a 64 bit number right and here are the
fields that I I I mentioned uh let me get my trapframe picture here and so you can kind of we can go
along um we've got the satp uh the stack pointer this trap I did these in a slightly different order
the saved PC and then the core number uh and then we have uh you can

count them we have all uh 31 registers here um and uh okay so we have all 31 registers there um just
in case you're curious uh they listed in sort of a a strange order for example we have some T's here
we have some s's here we have some A's here we have more S's and we have uh more T's down down here
that's the actual way they're organized in the RISC-V processor but uh

we just care about we we just access them by name and we don't really care which this is register X1
X2 X3 and and so on um so next let's take a look at the proc structure okay so that's this structure
here uh we're going to look at this structure here and um it's uh shown uh whoops let's see if I can
zoom in there uh the uh State down here um is a proc State and as I said here are the states

uh unused used used sleeping runnable running and zombie so when the process is actually running on
the core it has a status of or a state if you will of running when it's ready to run and uh but
unfortunately there's not an available core for it just sitting around waiting for a Time slice it
it's runnable um if it has waited on something uh it will go into a state of

sleeping and uh if it's terminated it will become a zombie until it can be recycled these structures
are recycled we only have 64 of them so we need to uh reuse them after that process is killed we
need to reuse that structure it remains in the zombie state for a little while until the exit status
this exit State uh can be retrieved by um the parent or whoever Waits on it um and

then at that point it can be recycled and it becomes unused and then used as sort of intermed
mediate between unused and something else so um here we have this the lock that's very important we
need to lock the process and in particular we need to lock it whenever we're going to be using uh
these fields here's our State field listed up above uh then we have this channel field it

could be uh zero but I mean it's it's only relevant when we're uh asleep and it tells what we're
sleeping on we'll get into how that works later but basically it's just an identifier um and uh uh
that identifies some different things that we might be waiting on and uh here's the Boolean that
indicates whether someone has asked for this process to be terminated here

is an integer that uh is used to uh transfer an exit status uh from the process itself to whoever
Waits on it um and then this is an integer that's just the process ID number here is a pointer to
another proc structure that is the parents process weight lock must be held when dealing with this
uh field and then we have some so called private Fields uh the lock does not need to be held when

we are dealing with these this is the address of the colonel stack uh here is the um size of the the
uh uh virtual of the uh virtual address space um here is a pointer to the page table that describes
the virtual OS space here is a pointer to the U uh data page that the trampoline is going to use
this will get mapped into the second highest page but here here's a pointer to that um here is

the context area so when we switch um this process will will be using that and finally an array
array that is uh used for open files number of files is the number of files that could be open
simultaneously and current working directory and then we have a field of 16 bytes for a process name
that we can can use okay I think that covers uh our road map and hopefully uh this image uh will

help you understand what's going on uh in the next video I plan to uh discuss uh the user and user
trap functions as well as user trap R and user R see you in the next video
