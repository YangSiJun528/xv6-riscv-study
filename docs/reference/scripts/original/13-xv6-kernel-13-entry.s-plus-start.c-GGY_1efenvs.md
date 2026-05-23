# xv6 Kernel-13: entry.S + start.c

| Field | Value |
| --- | --- |
| Video ID | `GGY_1efenvs` |
| URL | <https://www.youtube.com/watch?v=GGY_1efenvs> |
| Source | `docs/reference/scripts/original-raw/13-xv6_Kernel-13_-_entry.S_+_start.c-GGY_1efenvs.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to talk
about the kernel startup procedures I'll go over the code in the file entry.s and the file start.c
entry.s is pretty short it contains a few assembly code instructions start.c contains two functions
start and timer init all of this code is executed in machine mode and at the very end of it we
switch

into supervisor mode and jump to the main function I'll also talk about timer interrupts we'll see
how they're set up in the timer init function and we'll look at the code for the interrupt handler
which is called timerback and that comes from a file called kernelvec.s and I won't talk about all
the assembly code that's in kernelvec.s but I'll talk about this this part of it

let's begin with uh start dot c and I want to focus on this uh definition stack 0 is a stack of
bytes and remember we're running on a multi-core system in fact the constant in CPU is set to eight
so we can handle up to eight cores each core will need its own stack we will allocate a 4k page for
each stack and that's what's going on here so we're executing the number of cores and one page for

each this bit of c magic here basically says be sure and align this array on a 16 byte boundary okay
it's just normally a byte array so it might not be aligned if we don't put this in here that's what
we're doing I'm going to come back and talk about timer scratch in a second but let's first uh take
a look at the entry dot c file here is the entire file it's not too

long and let's take a quick look at this this section command indicates that this all of this code
will go into a so-called section sometimes I use the word segment that is a text segment that is its
executable code and we see some assembly instructions here we are defining a label underscore entry
and we're saying to the assembler be sure and make this a publicly known global symbol

okay so that it can be seen outside of just the assembly uh there's another label down here which is
local so that's what we're doing with the dot global command okay so what do we do load address okay
we're putting into the stack pointer register the addre well the goal here is to initialize the
stack pointer register and so uh here is the computation that

this code is doing uh the address of stack 0 that is that array and then we're taking our core
number it's called hart id in the RISC-V world we're multiplying it by 1k and so that's what's
happening here we load the address of the array into sp here we load four kilobytes into register a0
load immediate and here we are reading from the control and status register

called mhart id that is the control register that contains the number of the core 0 through 7. and
then we add 1 to that so now we have a number between 1 and 8.

and then we multiply that by the 4k that we computed in a0 so we multiply by 4 k and then we add
that a0 to the stack pointer and that gives us the point at the very top of the page for the stack
for this core okay so now we've got sp set up and we're jumping to start here we see a call we do
not expect start to return but if for some reason there's a bug and

it should then we would resume execution here this is a jump instruction and we just go into an
incident loop and essentially lock up the core okay next let's look at start dot c uh it is right
here uh we see the entire function right here and it uh will take care of several of the registers
and set things up for executing in uh maine and it will end essentially

with a jump to maine so this is executing in machine mode these functions that start with r
underscore read various registers and the ones that start with w underscore will write to the
register so here you can see where we're updating the status register we read it into some variable
we zero out the two bits that tell the previous privilege code for machine load okay so

and then we set those two bits with an or to say the previous mode was supervisor well we weren't
previously in any mode we've just started but that will see that that's what we need when we finally
execute the system return instruction down here here we write to the mepc register the address of
the main function so again that the mret instruction down here will return us to

whatever was previously executing nothing was previously executing but by setting the status and the
epc we're faking it so we're going to be if you will returning to the first instruction the main
function the satp register normally contains a pointer to the page table we initialize it with zero
there is no paging going on in machine mode but when we go into supervisor mode

the value of satp will determine whether paging is happening or not so we want to zero that out to
make sure that there is no paging when main starts now we have these two uh delegation registers one
for exceptions and one for interrupts and we're writing something into these registers we're writing
all ones and basically that means that all interrupts and exceptions are

delegated to supervisor mode so when an exception or when an interrupt occurs it will not be handled
in machine mode instead it will cause an interrupt to the supervisor mode uh code and it will get
handled there timer interrupts cannot be delegated so they're handled differently but all other
interrupts and we really expect them only from uh the disk and the

serial io device and um so that's what's going on there finally uh this is the um value for the
interrupts enabled uh I'm sorry the um register called s-I-e which is whether um interrupts are
enabled and uh we are reading the current value and then setting some bits and then writing it back
and what we're saying is that we're setting uh them for um devices uh

are allowed to interrupt uh the timer is allowed to interrupt and uh I'm sorry the although it
doesn't happen and this is where the software interrupts the so-called software interrupt these are
all enabled um and so in particular we're allowing uh though the delegation to go into supervisor
mode and we're telling the supervisor interrupts enabled register that we want

to enable these sorts of interrupts next we have this physical memory protection and we don't really
use the physical memory protection system basically we want to give the supervisor mode access to
all of physical memory so we write in these constants uh here to do that and I'm not really going to
go into that but basically this more or less disables the system

and then we call this timer timer init function to uh enable that to set up things for the clock
interrupts and uh then we read the m hart id register and that is the core number and we save it in
the tp register so we write it to the tp register and that will allow our my CPU function to work
later on to determine what CPU any code is running on and then finally we have some assembly

code we have the mret instruction so we've already said we want to return to supervisor mode we want
to return to this pc and so this mret instruction here will resume executing uh at the main function
the first instruction the main function and it will be in supervisor mode so that's how we get into
main but before we do that uh let's look at this timer annette

function and I have that right here so let's take a look at that that is uh from start dot and uh
it's also fairly short to hear it is in its entirety but let's go through this um so uh before I do
that I want to talk about uh the variable uh that is in start dot c that we didn't look at called
timer scratch so let me just point this out here timer scratch is

well it's a two dimensional array we've got uh well in our case we've got eight cpus eight cores and
each one of those has uh five 64-bit words so this is going to create an array of five words for
each core so here I'm showing this timer scratch array with all of its eight elements one for each
core and each element is going to look like this okay it's going to have

five double words I'm showing the actual offset here this is index 0 and x1 and x2 3 and 4 but these
are the byte offsets into this uh element of the array if you will and so we need that so the first
thing we do in timer in it is figure out what core we're running on so that just uh reads the uh
core number and we're gonna be using that core number here remember that

this core local interrupter has a couple of registers on it and the time compare register is [Music]
one of them so there are there is one register per core so this macro function here will give us the
address of the register for this particular core and what we're going to store into it is the
current time okay remember this is a preprocessor expression that gives

us the address of the m time register so we get the current time okay we're indirect here we're
getting the current time from this address and we're adding the interval our timer interrupts will
occur every well what is this one million cycles okay every million cycles we're going to have a
timer interrupt and so this is setting that register to this time plus

a million this is the current time plus a million okay now prepare for uh the we need to set up some
of the uh when the timer interrupt occurs we're going to be using this piece of memory uh during the
interrupt handler and we need to store two things at this point the address of the m-time compare
register for our core and we store the interval the interval is a

constant and you know I'm not 100 uh sure why we really need to store that constant there but uh
perhaps uh in some future version we could have different cores running at different intervals but
that's the uh thing we're storing there so what we're doing here is we're getting a pointer to uh
what well the timer scratch uh array that's this array here okay the element for this particular
core

and uh we get the uh address for the zero byte uh the first the first part of that so we're getting
an address a pointer to the address for these five words for the core that we're running on okay and
then we're setting that to restoring that address in scratch and then we're setting the third and
fourth or index three and four elements okay zero one two we're setting three and

four we're setting three to the um address of our time in time compare register so again this this
preprocessor function here this macro function given a core number uh computes the address of the
register for this core and we need to keep updating it so when we initialize it here that means
we're going to get an interrupt in 1 million cycles but after that we're going to

need to add a million to it for the next interrupt and store the updated value so we're storing the
address of the register here so we don't have to recompute it if you will each time we get the
interrupt and we're saving the interval which is a million cycles okay and finally we're writing
into the in scratch register there is uh an in-scratch register for

each core it's one of the control and status registers and so this core the core we're running on
will contain a pointer to the block for that core so that's what I'm indicating here that we're
saving in this in scratch register the same scratch register will only be accessed in the interrupt
handler for the timer interrupts uh while we're executing in supervisor

mode in other words throughout the entire rest of the kernel this register will be inaccessible only
when we're running in machine mode will this register be accessible and the only time we're in
machine mode is during this startup and the executing of the execution of the timer in init function
and then we will be in machine mode when the actual timer interrupt occurs

so that's why what we're storing an in scratch mt back this is a well this is another register and
we're writing into this register the trap vector address that is the address of the code that should
be executed when a trap occurs when that trap is handled by machine mode code the core will handle
it by jumping to whatever address we give it so here we're storing in the mtvec register the

address of the timervec function which I'm going to show you that code in just one second here and
so now when we get the interrupt we will jump to timer back then what do we do we read the status
register this is the machine mode status register and we update it and write it back and what do we
do we set the interrupts enabled bit so now machine mode interrupts can occur so now

at this point the timer interrupts can occur and uh we also have a uh more control over individual
timer interrupts so we are reading the mie register that's for interrupt enable this is an entire
register and the bits in it tell us more specifically what interrupts are enabled and so we set the
one for the timer interrupts to enabled and then we're done initializing and of

course we were in the the in the start function so uh after we uh do the timer interrupt timer and
hit call we uh write our tp and then we uh jump to the main function in supervisor mode now so then
the kernel executes along in supervisor mode and everything's fine until the timer interrupt occurs
and the timer interrupt will throw us into machine mode and will be handled by

the machine mode code that's located at timervec and so we're going to look at that next what that's
going to do is hand off the interrupt to a supervisor mode interrupt controller so it's basically
going to this code is going to be executed when the timer interrupt occurs and what it's going to do
is it's going to cause a software interrupt at the supervisor

level so that's what's going on down here so let's uh take a look at this um this uh timer vect is
the starting address of the first instruction and all instructions need to be four byte aligned at
the risk five [Music] we're not talking about compressed instructions here uh that's for another
video another time uh the timer vec uh symbol needs to be exported so it's

visible outside of this particular file and so that the linker can find it and so that when we are
actually compiling and linking the code the um where is it here the um the use of this uh timer that
symbol in the timer init function uh the linker will plug in the right address okay so that's why we
export and make this a global uh symbol okay so what do we do here

well now let's come back to this keep in mind we've just had an interrupt who knows what was going
on we could have been in user mode we could have executed user code we could have been anywhere in
the kernel doing anything so the point is the registers that are in use must not be disturbed this
function will use registers one two and three and zero I guess

and those are the only registers that will be updated and they will be restored so here what we're
doing is we're saving the registers at the time of the interrupt in scratch points here so the first
thing we do is this read write which is a swap okay so what it's going to do is it's going to
exchange the value of a0 and in scratch in other words a0 will be written in scratch and in

scratch will be read into a0 okay and then uh down here we're gonna undo that so for the duration of
this function a0 will point to uh this but then at the end we'll restore a0 for the duration of this
program we are saving a zero in the m scratch register and we're using a0 during this function to
point to this five word area the next thing we do is we save a one

two and three because we're going to be using them here so we save them in offset 0 8 and 16. so
here we have a store double word of a1 into offset 0 from a0 a0 points at that at this point it
points to the beginning of this 0 bytes offset we store a1 then we store a2 and a3 so we store our
registers here and then right before we return we restore those registers from

the same location so this is a load double word so we're loading from this uh location into a3 and
from this location into a2 and from this location to a1 and then we're ready to execute the mret
which will go back to executing when the interrupt occurs our program counter will be saved
automatically in the mepc register and the previous status and interrupt

status will be saved in in the status register and then as a result of the mret pc will be reloaded
with whatever was saved so we see some stuff going on with the trap and the emirate that's not
really visible here but that will be happening now here's the meat of it here schedule the next
timer interrupt and by adding this interval that's 1 million to the hardware register called m time
compare

so fortunately we've saved the address of our m-time compare register that is the register for this
core at location 24. and the interval at 32.

so that's what we're doing here we're loading from that from this place into uh register a1 so now
a1 points to the hardware register we load the value which is 1 million into a2 and then what we're
doing is we are loading the current time the uh the current value of m time compare right that's in
a1 okay with zero offset we're loading the that into a3 and then we're adding

a2 a2 contains 1 million so this is where we add 1 million to the previous value of the end time
compare register and finally we need to store it back so here uh we loaded it we added to it and
then we stored it back okay so now that will essentially schedule uh another interrupt to occur in 1
million cycles one million cycles minus however many cycles have been

executed to this point of course um to this point where we load uh actually I sorry I misspoke we're
just adding a million to the time compare so uh uh not less anything it's just added adding a
million to it so we get a timer interrupt every million cycles um and finally we have to raise a
supervisor software interrupt this will interrupt the kernel it will only interrupt the kernel if
the

kernel is ready so it depends on whether interrupts are enabled in supervisor mode but in any case
we we raise the interrupt to be handled either you know whenever we go back to supervisor uh mode uh
or whenever the supervisor mode code uh allows it but we do that by uh loading this value into a1
and then writing it into the uh interrupts pending register for

supervisor mode so here is the particular uh bit position that is corresponds to supervisor software
interrupts and we're storing that with a right to the control and status register called sip
supervisor interrupt pending that makes the interrupt pending we're in machine mode so it won't
happen immediately uh then when we return to uh supervisor mode code it depends on

whether the interrupts are enabled in supervisor mode if the interrupts are currently enabled in
supervisor mode then the interrupt would happen immediately but if the kernel is doing something uh
that can't be interrupted interrupted and it has temporarily disabled interrupts then that interrupt
the software the supervisor software interrupt will remain pending but at some future point

the colonel will enable interrupts and this software interrupt will occur the kernel will then have
a trap in supervisor mode and it will interpret the software interrupt as uh being a timer interrupt
because that's the only thing that could be causing it and we'll basically switch out the current
user mode thread we'll do a context switch it will you know do the processing it

would do at the end of a time slice which we'll talk about in some future video okay thanks for
watching and I'll see you in the next video
