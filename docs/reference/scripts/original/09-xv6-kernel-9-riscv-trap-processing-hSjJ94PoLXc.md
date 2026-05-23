# xv6 Kernel-9: RISC-V Trap Processing

| Field | Value |
| --- | --- |
| Video ID | `hSjJ94PoLXc` |
| URL | <https://www.youtube.com/watch?v=hSjJ94PoLXc> |
| Source | `docs/reference/scripts/original-raw/09-xv6_Kernel-9_-_RiscV_Trap_Processing-hSjJ94PoLXc.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to talk
about the RISC-V architecture and I'm going to focus on the status register and how traps are
handled by the hardware so recall that we use this terminology there are two kinds of traps there
are exceptions and there are interrupts a system call is a type of exception and

any kind of a program error is an exception as well the system call instruction in risk five is
e-call and program errors can include things like illegal instructions or alignment errors and so on
we also have devices that can interrupt the interrupt can occur when we're executing in user mode or
supervisor mode and regardless of what mode we're in when it happens we begin executing some

handler code running in supervisor mode so st vec is a control and status register it contains a
pointer to the handler code in particular it contains the address of the first instruction of some
code that will handle whatever trap has occurred there are two pieces of code in xv6 that handle
traps kernelvec handles traps that occur while executing in supervisor mode

and uservac handles traps that occur when running user mode code so let's begin with the status word
and I'm going to focus on the status word that code that's running in supervisor mode would see the
s status word the xv6 kernel runs almost entirely in supervisor mode so this is our status word that
we're concerned with things are a little bit more complicated

than I'm going to go into but I'm only going to focus on three bits and I think that's all you need
to understand the xv6 kernel there's a bit that controls whether interrupts are enabled or not so
that's called s I e for interrupts enabled when an interrupt occurs we need to save the value of
this bit and it's saved in another bit in the status word called

s-p-I-e previous interrupts-enabled bit we also need to remember what mode we were executing in when
the trap occurred it could be we were executing in user mode or it could be that this was an
exception that occurred in supervisor mode the trap can occur as a result of an exception or an
interrupt obviously if it's an interrupt then the previous value was enabled otherwise

we wouldn't be handling it but it might be that we have an exception that's occurring while
interrupts are disabled so in any case we need to save the previous value of this bit so that when
we return from the interrupted code we can restore the bit to whatever it was before one technique
in kernels is to temporarily change the interrupt enabled bit to disable all interrupts

and that can prevent other threads from interfering with one particular critical section however xv6
is a multi-core operating system so it doesn't have any impact on what the other cores are doing
they may be changing memory uh simultaneously so we have locks to handle those situations however
interrupt disabling does occur in the kernel okay what happens when a trap occurs

well the first question to ask is whether interrupts are enabled or disabled if interrupts are
disabled then the trap will remain pending that is it will not be immediately processed by the
hardware and the handler code will not run until at some later time the kernel re-enables interrupts
and and then at that point the trap crossing will occur now if it's an exception it's going to

be handled immediately regardless of whether interrupts are disabled or not but in any case when the
trap is handled the hardware will do this it will do these things and then it will resume executing
so the previous instruction is completed and then the hardware will save a copy of the program
counter in a control and status register called sepc then it will branch to the first

instruction the handler code by copying the value in s-t back to the program counter then it will
save in the s cause register information about what exactly happened what caused the trap handler to
be invoked and it may save additional information in a csr called st val there are several causes
that we will be concerned with in the xv6 kernel if it's a system call the cause will be

eight uh if there's an interrupt from an external device will be nine if there's a software
interrupt and I'll discuss those in a second but they are related to timer interrupts then it will
be a one anything else is a program exception and some sort of an error in the code the hardware
will immediately save the previous mode whether we were executing in user mode or supervisor mode
when the

exception or interrupt occurred that will be saved in this bit here of the status word and we will
also save the previous value of the interrupt enabled bit in spie finally we will disable interrupts
and change the mode to supervisor if it was not already supervisor at that point the hardware phase
of the trap processing is done and we begin executing the first instruction of the

handler code the handler code will do a lot of things who knows what exactly we'll see that with the
kernel but at some point in the future we'll be ready to return to the interrupted code for that we
will use the risk instruction called s ret for return from supervisor mode what this will do is
restore the interrupt enabled bit from the saved copy return to the whatever mode we were in

previously and restore the program counter to whatever we whatever it was before and then we will
resume executing the code that was interrupted now that's what happens in supervisor mode and most
of the kernel runs in supervisor mode but there's a little bit of the kernel that runs in machine
mode and so now I'm going to talk about trap processing in machine mode it's

essentially the same idea there's another register called m status actually it might be a mirror
copy of the same register so there might be it might actually be implemented as one register but for
all intents and purposes you can think of it as a separate register and like the s status register
it has bits for disabling or enabling interrupts and for saving the previous value of the

interrupts enabled bit and finally it has a field for saving the previous mode level we could have
been interrupted in user mode supervisor mode or machine mode so we have two bits to save that if
we're executing in machine mode then we will not be having any interrupt to supervisor mode so
interrupts and traps only go upward so there is no we need two bits for machine mode

interrupts but for supervisor mode interrupts we only need one bit because we could not be coming
from machine mode to supervisor mode the only interrupt that we deal with at machine mode level is
the timer interrupt and we have a mechanism for delegating all other traps all other interrupts and
exceptions to be handled at supervisor mode for some reason we cannot

delegate the timer interrupts so we're going to see that it gets sort of mutated into something
called a software interrupt so interrupts are always enabled at the machine mode level so whenever a
trap sorry whenever a timer interrupt occurs it will be handled whenever any trap of any other kind
happens it will be automatically delegated to supervisor mode and whether or not it's handled

immediately or whether it becomes pending will be determined by whether interrupts are enabled or
disabled at the super supervisor mode level when a timer interrupt occurs a machine mode handler
will run and that code will create or force or synthesize something called a software interrupt and
that will interrupt the supervisor mode code and the handler at machine mode

will continue running and it will re-enable interrupts and return to the interrupted code if the
interrupted code was supervisor code it will return to supervisor mode if the interrupted code was a
user program it will return to that if interrupts are enabled at supervisor level then an interrupt
will occur immediately at that level otherwise if the colonel has

momentarily disabled interrupts at supervisor mode level then the interrupt will remain pending
until the kernel is ready to deal with it the trap processing at machine mode level happens pretty
much the same way it's exactly the same idea as I said the only cause of interrupts are timer
interrupts every other sort of interrupt is delegated at the hardware level

immediately to supervisor mode and so we don't do this processing for anything but timer interrupts
like the st vac register there's also an mt vac register which contains the address of the machine
mode trap handler in xv6 that code is a function called timer vac and that will handle the timer
interrupts the hardware will take the following actions after completing the previous

instruction it will save the program counter in a register called mepc it will load the program
counter with mtvec which will force a jump to the handler the m cause and mt val registers are
ignored it's a timer interrupt and there's no further information about it that we're interested in
the hardware will save the previous mode whether we were executing in user mode

supervisor mode or machine mode and we'll save that in these two bits and then it will save the
previous value of interrupts enabled in this bit and it will then disable interrupts and switch to
machine mode we'll then execute the timervec code and what will it do well it will synthesize or
cause an interrupt at the supervisor level so it's going to do something that will create an
interrupt

that at that level that interrupt is not a timer interrupt but it's a so-called software interrupt
and then this code which is fairly short will execute an instruction called m return return for
machine mode and it will restore the interrupts enabled bit it will restore the mode to whatever it
was previously and it will restore the program counter thereby returning to the code that was

interrupted what's going to happen after that well we've raised this software interrupt and what
happens will depend on whether interrupts are enabled or disabled at the soft at the supervisor mode
level if the interrupts are disabled then the software interrupt will become pending and it will
just have to wait until the kernel is ready to enable interrupts

if interrupts are enabled then the trap will occur at supervisor level immediately so we have this
mie bit that enables interrupts at machine mode and we have this s s-I-e bit which enables or
disables interrupts at supervisor mode level and these are global enabling bits there's also so the
interrupts can be all interrupts can be enabled or disabled by changing these

bits in the status registers we also have a facility at for doing more selective control in the risk
architecture it's not really needed but I want to cover it because we have these registers and they
are touched during initialization so the mie for uh machine mode interrupts enabled is a register
it's rather confusing because there is a bit called m-I-e in the status word

but this is a separate register likewise there's a bit called s-I-e in the status register s-I-e is
here and we have a register called s I e as well but these are separate things for the selective
control we have a bunch of different bits going on here but we're only interested in this one bit
this stands for machine mode timer interrupt enable and we will set it to

one during the initialization phase this happens in the start function which is executed in machine
mode we also have a way to selectively enable and disable interrupts at the supervisor mode level
that's taken care of in this sie register and the bits that we're interested in are these three bits
we have one to enable or disable device interrupts that is interrupts from

hardware devices we have one to interrupt sorry to enable or disable software interrupts and we have
something for timer interrupts but uh I'm not sure why that's not usable here we can't seem to
delegate timer interrupts down here but so but this bit is uh just want to mention it anyway and so
what we'll do in the initialization is to set all these bits to one

we also have a register called sip for interrupts pending this has the same format so when a device
is interrupting that bit will be changed to one when there's an interrupt pending and if there are
no interrupts it will be zero so we have the ability to force an interrupt to become pending or to
happen by setting this bit to one and that's what the timer vec code which is

executing at machine mode will do it will set this bit here to one in the sip register thereby
forcing the software interrupt to occur so I'm saying that here the timer interrupt handler which is
running in machine mode will set the ssie bit to 1 and that will force a software interrupt to
happen at the kernel level in supervisor mode so now we have the ability to delegate

traps a trap being either an interrupt or an exception so all traps in the risk architecture will go
to machine mode handlers but there's also a facility to sort of bypass the machine mode handler and
go straight to a handler that's executing at supervisor mode level and that's what we do xv6
delegates all traps to supervisor mode so normally it would be handled by a

handler running in machine mode but we're going to delegate things to supervisor mode and there are
two registers m e delegation and m I delegation which are used to delegate exceptions and interrupts
so xv6 will set these bits in these two registers to delegate traps so that when an interrupt or an
exception occurs it will be immediately handled in supervisor mode

by the kernel code for some reason the timer interrupts can't be delegated so as I said it's handled
a bit differently by doing this software interrupt thing so these two registers me deleg and mi
deleg are initialized during startup in which it happens in the start function which executes in
machine mode they are set to one and they never change when a trap occurs

because these interrupts and exceptions are delegated nothing happens in machine mode instead it
immediately becomes a supervisor level interrupt or exception so let me just point out these
registers so here is the register for delegating exceptions and on the next slide we have delegating
interrupts and we have a bunch of different kinds of exceptions that might occur

we have page faults for stores and loads and instruction fetches we have environmental calls the
system call we have different access faults we have faults related to misalignment uh if an
instruction is illegal and we attempt to execute an illegal instruction we would have an exception
for that and so on so there is an instruction uh that is executed to set the me deleg register

and basically we're just setting all the bits to one this is not exactly legal assembly code but I
think it gives you the idea it writes into the control and status register called me deleg a value
of all ones for delegating the interrupts we have a register called mi delegation and it contains
some bits and we have the ability to delegate device interrupts we have the ability to

delegate timer interrupts which apparently can't happen for some reason we don't have that ability
but in any case they're set to one and for software interrupts we uh could delegate those as well
the initialization is very simple it basically just sets all these bits to one so we have this going
on okay that's it for this video see you in the next one
