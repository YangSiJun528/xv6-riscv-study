# xv6 Kernel-26: Traps in Kernel Mode

| Field | Value |
| --- | --- |
| Video ID | `Aj3DLErovD8` |
| URL | <https://www.youtube.com/watch?v=Aj3DLErovD8> |
| Source | `docs/reference/scripts/original-raw/26-xv6_Kernel-26_-_Traps_in_Kernel_Mode-Aj3DLErovD8.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'll talk about what
happens when a trap occurs while we're executing in kernel mode kernel mode is sometimes also called
supervisor mode I'll look at the assembly code in the file kernelvec.s and I will look at the code
in the file trap.c in a particular kernel trap and device interrupt

so let's uh review this picture that I've shown earlier where we have code executing in user mode
and then a trap occurs and we go down here and ultimately execute an sred and return to user mode
when the trap occurs we will take an immediate jump the hardware will take a jump to uservac there's
a special register called stvac and as part of the hardware processing

the value in that register will be moved into the program counter this register will contain the
address of the first instruction of userback so this just does a jump to userback userback is
assembly code it immediately saves all the registers and basically just jumps to the function user
trap which then looks at the situation and determines whether we have a program exception a device
or

timer interrupt or it's a system call ultimately we come down to user trap rat which then invokes
user ret and does the sret instruction first they will save the registers and get everything
restored and then go back to executing in user mode at this point when the trap occurs interrupts
are disabled so if a device interrupts anywhere along this pathway until

interrupts are re-enabled that interrupt will remain pending and will not be dealt with until the
moment that the interrupts are re-enabled and you see that interrupts are enabled along the pathway
that deals with the system call so while the process is executing the code that implements the
system call a lot of things can happen but interrupts are enabled so it may be that

we get an interrupt and uh if we do then uh we will go off and deal with the interrupt by executing
the device handler and then we will come back to this process and complete the system call and then
finally come down here to user trap red the first thing we do in user trap rat is disable the
interrupts and so the next thing we do is we restore this register sdvac to point to uservac so

that when we are executing in user mode any the next trap will go to user back as it should so we
also see that interrupts remain disabled along the device path so all the device handlers will
execute with interrupts disabled we also see that interrupts are disabled along the timer interrupt
pathway so we will more or less complete this yield and then go straight back here now what

will yield do yield may switch to another process and what happens in that process well we don't
know but most likely interrupts will be enabled at some point so an interrupt may occur but when we
come back from the yield things will be just as they were when we left and in particular the
interrupts will be disabled and we immediately uh disable them again if a device

interrupts during this phase right here it will remain pending that interrupt will not be executed
but it will be uh it will be pending and the minute we enable interrupts right here it will occur if
we don't take that pathway then we don't enable interrupts until we go back to the user mode code so
if the device interrupt occurs anywhere else in particular

happens down here then that interrupt will remain pending until we return to user mode code and then
the first thing that the user mode code will do is have another interrupt and deal with the device I
went over the function user trap in an earlier video in this series but I wanted to show that right
at the beginning of this function we are writing to the control and status

register called st vac and we are storing in it the address of the first instruction of the assembly
code called kernel vac okay so uh that's what I showed right here in the diagram that we're setting
steven and I also want to look at um what happens at the bottom here when we restore st vac after we
disable the interrupts so in user trap red which is right here this is user trap rep

the first thing we do is turn interrupts off and then again we write into the st vac and we're
writing the address of the user vac so there's where we are restoring it when we get a trap while
executing in user mode the first thing we do is save all of the registers in the processes trapframe
I discussed that at length in an earlier video in this series when we get a trap while running in

kernel mode we do things a bit differently we don't have any fixed place to store them instead we
save the registers on the kernel's stack okay so now let's take a look at the code in kernelvec
kernelvec.s and it is the first code that is executed after a trap in kernel mode we make it known
to the assembler that the label kernelvec and the function kernel trap which we

will be calling are externally known labels so the first thing we do is we subtract 256 from the
stack pointer this is an ad immediate and we subtract 256 uh and from the stack pointer and that
allocates bytes on the stack then we say each of the general purpose registers ra sp gptp and so on
now we don't have to save the zero register the one that contains zero at

all times so actually we only need 248 bytes but nonetheless this code pushes 256 bytes then we call
the kernel trap function and that will go off and execute the device handler if it was a timer
interrupt it will do a yield and at some point the yield will come back and eventually we'll return
from kernel trap and then we will restore the registers so previously we saw an sd which was a

store double word here's a load double word so we're loading all of the registers registers back
into the registers and you see all the way down here and then we pop the 256 bytes off the stack by
adding and then we do an sret to return to the executing code that was interrupted with the trap one
thing you will notice is that we do not restore the tp register remember

that the tp register is used to hold the number of the core that we're executing on the trap could
have been caused by a timer interrupt in which case we will be executing a yield yield will go off
and make this process uh runnable instead of running and it will then sit on the ready queue for a
while and ultimately some core will select this process to run and then it will be rescheduled and

the yield will return however it may not return on the same core the tp is supposed to hold the
number of the core we're executing on and that will be set appropriately uh by the scheduler I mean
so when we come back to it we'll have a tp register for that core so we don't want to overwrite that
with the core number that we were executing on at the time the trap occurred because we

may resume this process on a different core I think that's kind of cool okay so the code in
kernelvec has just saved all the general purpose registers and invoked kernel trap which is shown
here the first thing the kernel trap function does is save the value in several of the control and
status registers the program counter the status register and the cause register are fetched and

saved here we will need the program counter in the status in order to return to the interrupted
process the cause register will contain a number that tells exactly what caused the trap so the
first thing we do or after that is do some sanity checking we make sure that we were really
executing in kernel mode when we were interrupted by looking at the status register and looking at
the previous

privilege mode field and making sure that it is supervisor or kernel mode then we make sure that
interrupts are in fact disabled and assuming everything is good there we then call this function
device interrupt and device interrupt will look at the cause register and determine whether it's the
the UART or the disc uh that is causing the interrupt and if so it will execute the appropriate
handler

routine for that device and return one if it is a timer interrupt it won't do anything but it will
return a two and if it's anything else it will return a zero that is a problem so here we are saving
the value returns and if it returns zero we print out the cause register and we also print out the
program counter and the value of this st val register at the time of the interrupt oh

I should have highlighted bold faced these function calls but in any case that we then panic now if
it was a timer interrupt then it will have returned two so we call the yield function yield will go
away uh it will invoke the scheduler and our process will be changed from running to runnable and
then at some later time we will get a chance to run again and

we will change back from runnable to running and this might actually even occur on a different core
and so we might resume executing on some other core so before we return we need to restore the value
of this register for the program counter and the status these will be used as part of the srep
process the sred instruction will copy the value of scpc into the program

counter and the copy s status into the status register so we need to have those set up before we
return to kernel vec now the other thing I want to discuss is why do we do this test here if we had
a time interrupt we're also making before we call yield we're making sure that the state is running
so here we are asking what is the current process and if it's not no

and then we follow that to the state and make sure it's running and so you might ask well how could
we have been interrupted from any process that wasn't running and to see the answer to that let's go
back to look at the scheduler code here's the scheduler code so remember that this will be executing
a for loop here where it goes through all the proc structures in the

proc array looking for one that's runnable looking for one that's runnable and if it finds one it
changes it to running and switches into it and it just keeps looking and if when it gets to the end
of that array or if it doesn't find anything in the array it will then loop back to this infinite
loop we turn interrupts on here okay I discussed that earlier why that's

necessary but at this point when interrupts are turned on it may be that we will have a trap it
could be that a device tried to interrupt earlier and that interrupt has been remain remaining
pending so the moment we turn interrupts on here boom we get a trap okay we're not actually in any
process we're in the scheduler okay so no process will have a state of running

in fact the last thing we do before calling switch is change our our state to runnable or maybe
sleeping so we don't want to have the yield function executed while we're in the scheduler that
would really mess things up so that's why we we do that here but notice that from this acquire on
through the switch interrupts will be disabled remember that when we acquire a spinlock we

disable interrupts until they are uh released till the spinlock is released and likewise uh in the
code that call switch that comes back we will also acquire the lock which will disable interrupts
until this point so it's possible that interrupts will be re-enabled at this point and we might have
that trap occurring here as well next let's talk about the device

interrupt function it begins by reading the s cause register to see what exactly caused the trap
this function will return two if it was a timer interrupt one if it was the UART or the disk or zero
if it was something else that was unrecognized so here we are looking at the s cause register and if
it's this value then we've got an external interrupt the platform level interrupt controller

makes its need to be taken care of known by causing an external interrupt so if we have an external
interrupt uh it must be the click so then we handle it here and otherwise we handle it here we call
this function click claim which will return a code to indicate which device is demanding attention
and then we check that number if it has this value it must have been

the UART so we call the function here which is the interrupt handler for the UART device we've had
this value then we call this function which is the interrupt handler for the disk device and if it's
zero we ignore it but if it's anything else we print some sort of an error message so uh then we
need to call click complete the click allows each device to raise at

most one interrupt at a time tell the click the device is now allowed to interrupt again and we
return the code of 1.

otherwise we check the s calls register and if it has this value then we must have a software
interrupt and remember that when we have a timer interrupt the machine mode code gets uh gets that
and it then simulates this software interrupt at supervisor level so that's what's happening here
and uh then what are we going to do if it was a timer interrupt we are going to check

whether we are running on core 0 and if so we call this function clock interrupt and I'll look at
that in just a second but regardless we acknowledge the software interrupt by clearing the bit in
the interrupt uh pending word in that register so we read the register clear the bit and write it
back and then we return the code of two and if it was anything else then we'd return

zero so what does this clock interrupt function do well all it does is increment this global
variable ticks that's essentially our clock x36 doesn't have any kind of a real-time clock we're
just counting the number of ticks on core zero and so that global variable is protected by a
spinlock called pixlock so we acquire that and then we increment it and if anybody has been

waiting if any other processes are waiting on a particular time coming to be then we wake that
process up or all of the processes up and then we release the spinlock okay that's it for kernel vac
and what happens when a trap occurs in kernel mode I'll see you in the next video
