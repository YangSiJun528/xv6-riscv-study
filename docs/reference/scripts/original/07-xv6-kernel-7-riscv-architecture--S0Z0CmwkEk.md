# xv6 Kernel-7: RISC-V Architecture

| Field | Value |
| --- | --- |
| Video ID | `-S0Z0CmwkEk` |
| URL | <https://www.youtube.com/watch?v=-S0Z0CmwkEk> |
| Source | `docs/reference/scripts/original-raw/07-xv6_Kernel-7_-_RiscV_Architecture--S0Z0CmwkEk.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this and the next couple of
videos I'll be talking about the RISC-V processor architecture I'll be going into it in enough
detail so that you can understand the xv6 kernel but I won't be going into great detail I won't
assume that you have any prior knowledge about the RISC-V architecture but I'm not going to go into
it in

enough enough depth for you to become an assembly language programmer let's start with the registers
the RISC-V instruction set architecture has 32 general purpose registers and a program counter all
of which are 64 bits so let me go through these registers here are the register names and we start
with the first one which is hardwired to be zero so we won't need to

save it when we do context switches between processes ra is the return address the RISC-V uses kind
of a clever system for function calls and returns when a call is made the return address is saved in
this register rather than being pushed onto the stack and then the return instruction simply copies
the value from the return address register back into the pc

so for many functions we don't need to access main memory at all sp is the stack pointer the stack
grows downward tp is the so-called thread pointer in the xv6 kernel it will contain the core number
which is the hart id the hardware thread so thread here I guess might stand for hardware thread gp
is a global pointer it's used by the compiler and will be set and not changed

basically it just is used to make accessing global and shared variables quick and efficient then we
have the a registers which are used to pass arguments to functions a0 is used for a return value if
the function returns something the a registers and these t registers can be used freely within a
function to do whatever the function needs to do in addition we have 12 so-called callee

saved registers the caller will assume that any function it calls will not modify these so if a
function wants to use any of these registers then that function must save them before using them so
typically they would be pushed onto the stack and that function must restore these registers before
returning so this is the entire state these uh 31 registers here and the program counter

it's the entire state of a user mode thread user code has no access to the status register so the
status register is invisible in user mode code at each context switch that is when we are ending one
process's time slice and about to begin the time slice of another process the kernel will need to
save the state of the previous thread okay the previous processes registers will be

saved somewhere by the kernel and then before the next time slice begins the colonel will need to
load the registers to constitute the state of the next process at any time the risk processor is in
one of three modes there's machine mode m there's supervisor mode s and there's user mode u so each
core has its own set of registers and each core is running in exactly one mode at

any one time machine mode is the highest and most powerful mode with the greatest privileges and
after the core starts up or or is reset it will go into machine mode in the xv6 kernel machine mode
is not used very much there is some code that is executing in machine mode on startup that does some
initialization stuff the other thing that we need machine mode for is for dealing

with timer interrupts timer interrupts require the handler to run in machine mode so there is a
little bit of code that will run in machine mode uh that code immediately converts the interrupt
into an interrupt to the supervisor mode code and then it basically returns quickly so this handler
is short and quick and it's just basically converting the interrupt the timer

interrupt into an interrupt to code running in supervisor mode so supervisor mode is where all the
action happens all the kernel code runs in this mode of course some instructions are privileged and
they cannot be executed in user mode so privileged instructions can only be executed in machine and
supervisor mode and finally of course we have user mode all user code runs in this mode in fact

we can say that if code runs in supervisor mode it is kernel code and vice versa likewise any and
all code that runs in user mode is a user code it's part of a user application so privilege
instructions are of course not allowed in user mode and if a user program tries to do something
that's privileged it will cause a trap and the kernel will abort that process

okay now let's move on to supervisor mode and machine mode we'll not be going over the individual
instructions of the RISC-V processor of course we have the usual instructions add and move and load
data from memory and store data to memory and test and branch and so on but I won't be going over
those in addition to the general purpose registers there are a number of control and status
registers

csrs in fact the architecture accommodates up to 4k of these registers that's a lot of registers
there's a lot of complexity here and I'm going to sort of present a simplified model hopefully
everything I say is correct and true but I'm going to leave out some details that I think are not
necessary for understanding the xy6 kernel I'm only going to discuss 19 of these

csrs we have a couple of privileged instructions we have three privileged instructions that are
important we have one to read a csr okay so this example will take the value in a cs register which
happens to be called s status and move it into the a0 register we have a write instruction takes the
value in some register and moves it into a some control and status register in this

example we're writing into the s status register and finally we have a swap instruction so here you
see register a0 it happens to be the same so this is going to simultaneously write from or copy the
value from m scratch to a0 and copy from this register here which is also a 0 into the csr this is
done atomically so either both of these copies happen or neither of them happen

well I don't think there's any way that they would not happen they both happen simultaneously
without any interleaving of other instructions okay so here are the 19 registers and I'll just uh
mention that some of them are used and accessible only in machine mode and other others the ones
that start with s are accessible in both machine and supervisor mode one register contains the core
number

another is the status register we have the trap vector which is basically the address of the handler
that will be invoked when a trap occurs when a trap occurs the previous program counter will be
saved in the epc exception previous uh exception program counter register also the cause of the trap
will be saved in an s-cause register and if there's any additional information there's an st

val register which might be updated as well there's also an m cause register but we don't care about
that one there's a work register that can be used by our trap handlers satp is fairly important it
will contain a pointer to the page table so atp is address translation pointer and then there are a
number of registers that allow us to enable interrupts selectively both in

machine mode and in supervisor mode we can also find out which registers are sorry which interrupts
are pending at any one point and we'll see that later finally exceptions and interrupts can be
delegated from machine mode to supervisor mode I'll talk about that later and then we've got
something called physical memory protection and we'll talk about that so before I get going any
further I want

to discuss some terminology we have exceptions and interrupts and these are both kinds of traps so
trap is a more general term and we talk about having a trap handler to either handle an exception or
to handle an interrupt exceptions are synchronous which means they are caused by some instruction
you know some instruction is executed and that instruction has an

exception the system call instruction happens to be named e-call in risk five and that would cause
an exception also any instruction that causes some kind of an error will cause an exception this
could be an illegal instruction or an alignment error or some kind of page fault or memory access
something like that on the other hand are asynchronous they come from someplace besides the current

instruction so one example of that is the timer interrupt another example is when a device
interrupts so we have two two devices we have the asynchronous received transmit unit uh which is
the serial communication device and we have the disk and those can interrupt at any time the timer
interrupt will only interrupt code running in machine mode or I should say

the handler has to run in machine mode it can happen at any time of course but the handler happens
to run in machine mode and we'll talk about how that's dealt with uh later finally we have something
called a software interrupt uh when a timer interrupt occurs if the handler is running in machine
mode it needs to notify the supervisor code it needs to notify the kernel so it

causes what's known as a software interrupt at the supervisor level so a software interrupt will be
caused by code running in machine mode whenever a timer interrupt occurs and then that software
interrupt will be handled by code that runs in supervisor mode that is the kernel has a interrupt
handler for the software interrupt that will do what it needs to do for a timer

interrupt finally in this video let me talk about two registers uh three registers that are fairly
easy to discuss the hart is synonymous in these videos at least with the core or the processor
number so this register m hart id contains the core number each core has its own separate set of
registers and the hart id register is hardwired it cannot be modified and it can be

queried to find out what core the code is running on the kernel will immediately move this value
into the tp register during startup and it will never change at that point it will stay constant so
there's a function CPU id which is used all over the place and all it does is return the value
that's in this tp register now I say tp never changes it never changes in the kernel however

all registers are accessible in user mode code and the user code could easily overwrite tp with some
other value whenever the kernel goes from kernel code into user code it first saves all the kernels
registers including tp then the user code has a time slice and runs until something happens some
kind of trap occurs and at that point the kernel becomes active and the first thing it will do is

restore its own registers including tp so tp never changes within the kernel we've also got these
two registers there's a system for physical memory protection it is not used in the xv6 kernel but
we need to deal with it anyway since it's there basically it's going to be able to limit access to
physical memory for any code running in supervisor or user mode so machine mode

can basically uh partition memory into areas and keep the code that's in those areas out of you know
keep it from interfering with other code the intended usage for this system is to support secure
bootstrapping and hypervisor code hypervisor is some code like an operating system where each of the
user threads the users are host os's so the idea with a hypervisor

is it's going to use this physical memory protection scheme to isolate the memory of one operating
system's uh use from another operating system in our case with xv6 this is loaded during startup
while we're running in machine mode and basically it marks all of physical main memory as fully
accessible read write and execute and then these registers are not changed after that

okay in the next video I'm going to going to talk about the status registers and page tables so I'll
see you in the next video
