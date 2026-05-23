# xv6 Kernel-27: PLIC: Platform Level Interrupt Controller

| Field | Value |
| --- | --- |
| Video ID | `jK3TLK4CpWk` |
| URL | <https://www.youtube.com/watch?v=jK3TLK4CpWk> |
| Source | `docs/reference/scripts/original-raw/27-xv6_Kernel-27_-_PLIC_-_Platform_Level_Interrupt_Controller-jK3TLK4CpWk.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

What happens when an I o device wants to interrupt a processor core well if this question has been
bothering you then this is the video for you in this video I will describe and explain the platform
level interrupt controller or the click this is a circuit that's particular to the RISC-V processor
ecosystem although other computers have similar kinds of circuits so

if you are interested in the subject generally this will be a helpful video this video is part of a
series on the xv6 operating system kernel but this video is pretty much just about the click and
doesn't talk too much about the kernel itself however at the very end of this video I will go over
the code in the file click dot c let's start with a high level overview

of the problem here we see some I o devices we may have some disks here's a keyboard we may have
some other devices like a UART or a usb card or network card and we have cores down here and from
time to time an I o device will require attention and it will need to interrupt one of the cores so
for example when a human types a character the keyboard might send an interrupt and it needs to

be routed to some core and that core will then uh talk to the keyboard directly it will run an
interrupt handler so the core will be executing along it will get the interrupt a trap will occur
and then the interrupt handler code will run that code will then ask the keyboard what character was
entered and probably add it to some buffer somewhere and then that interrupt handler will say

I'm done with the interrupt and go back to whatever it was doing before it was interrupted for disks
typically they're busy doing a read or write operation which takes a relatively long time and when
the operation finishes uh the disk will then send an interrupt and so some core will need to be
interrupted and that will cause a trap in the core and that core will then talk

to the disk directly and perhaps wake up some waiting threads that we're waiting on the operation to
complete and it may also start that disk on another operation and then go back to doing whatever it
was doing so again when a device needs attention it sends an interrupt the interrupt is routed to
one of the cores that causes a trap whatever their core is doing will

be suspended and that core will then run an interrupt handler which is some code that will talk
directly to the device so it will go around the plate and so the question is how does the click
route the individual interrupts to the various cores we have to assume that not all cores can deal
with all devices for example there might not be a direct connection between some devices and some

cores in a large system so the first thing we have inside the click shown here in red is a large
enable matrix and this is a matrix of bits and it shows which devices can send interrupts to which
cores so for example this keyboard here can send an interrupt to either this core or this core but
not this core and so on this big enable matrix is generally set up

during the initialization phase and won't change now the click is connected along with the io
devices and the cores and the main memory to some other interconnects here I've shown the cores and
main memory and typically there's a bus down here so if a core wants to read or write to main memory
it sends a request on this bus and data is sent either to the main

memory or to the core if the core wants to manipulate a particular I o device it generally uses
memory mapped I o registers so these devices are each mapped into some addresses into the physical
memory address space and for this core to communicate with this disk it will either read or I should
show read this way or we'll write data to the memory mapped registers

up above I've shown the interrupt lines and those are essentially wires that are used to transmit
the interrupt so when an interrupt occurs the signal will go over the wire to the click and the
click will then notify one or more cores that some device is requesting attention the click itself
is also memory mapped so for example to set it up during the initialization phase to to write in
that

enabled matrix there are registers inside the click so one of the cores during initialization will
do writes to these memory mapped registers in the click also the communication to the click to
indicate which interrupts are accepted and when the trap handler is complete are done using memory
mapped references now some devices will be integrated on the same chip as the cores and others

will be external devices typically in a processor you'll have several cores maybe four cores on one
ship and the click will likely be integrated on the same circuit chip as the cores in the old days
it might have been a separate uh chip but then we didn't have multiple cores on a single chip either
some of the devices may be integrated on the same circuit chip as the cores and the click

and others may be external for example a UART device is almost always going to be integrated on the
same chip as your cores but other devices like disks and obviously a keyboard are not going to be on
the same chip as the cores the main memory uh may or may not be on the same chip here's another view
of the platform level interrupt controller I've shown the cores over here and the

devices over here these arrows represent wires whereby a device can transmit a signal to the click
to indicate that it needs attention in other words when an interrupt is signaled by the device a
signal will go over the wire and the first thing it encounters inside the click is something called
a gateway and the gateway could just be thought of as a single bit

when an interrupt arrives uh a bit in the gateway is set to remember that that interrupt has
happened and that's called the source interrupt pending bit and at some point that bit will get
cleared but while the bit is set it means there is an interrupt pending and it need and the click
will at that point channel it to one of the core one or more of the cores

now the signal coming in can either be level sensitive or edge triggered perhaps you already know
these terms but I'll discuss them in a second but first I want to talk about what happens uh when an
interrupt occurs okay so when an interrupt occurs the source interrupt pending bit becomes one and
at that point the click will notify all of the cores that are enabled

for that particular device so the matrix of all the enabled cores will be consulted to determine
which cores are going to be notified here's the matrix so for example when an interrupt comes in for
the keyboard these two cores will be notified but not this one what does that notification mean well
it means an external interrupt will be raised in that core now remember with RISC-V

a core can deal with three kinds of asynchronous interrupts one is a timer interrupt one is what's
called a software interrupt and the third is an external interrupt so the click will cause an
external interrupt to become pending in that core if that core has interrupts enabled then a trap
will occur immediately but if interrupts are disabled then the trap can't occur at that time

but when the trap occurs the trap processing will invoke a trap handler routine so the trap handler
code will start executing and the first thing it will do presumably is to interact with the click to
claim the interrupt and this claim operation basically is the code telling the click that hey I want
that particular interrupt I accept it I've got it I'm claiming it

uh as far as the other course that you told about this particular interrupt they don't get it okay
and this claim operation is done by reading a memory mapped register in the click so the trap
handler will do a load from some word in the plix address space and that click will then return a
particular value for that memory load okay the click will return the id of the

interrupting device as a result of this claim operation and we'll also at that point the clear the
source interrupt pending bit because the interrupt is being taken care of might clear it later but
that's details what if there are multiple cores that are enabled only one should get the interrupt
okay so only one claim will return the id of the interrupting device

the click will make sure that the others get a zero when they read this memory map register so when
they get a zero they'll say oh I didn't get it okay I can go back to doing whatever I was doing
whereas the handler code in the core that actually got the id will then go on to communicate with
the device and take care of whatever operation is required so at that point the handler code will

run and deal with the device and then at some point at some future time the handler code is done and
it can signal interrupt completion that's another operation that it does by communicating to the
click and once that interrupt is marked as completed then the next interrupt from that device can
occur so the interrupt completion operation is done by the core writing to a memory mapped

register in the click and then once the interrupt completion is done the click will look at the
source interrupt pending bit and see whether there is another pending uh interrupt from that same
device and if that device is requesting another interrupt or or when that device is requesting
another interrupt and the gateway bit is set again then the click will once again signal another
interrupt

and the process will repeat now I want to describe what level sensitive and edge triggered mean for
those of you who are not electrical engineers I'm talking about the signal that travels along this
wire from the device to the gateway in the case of a level sensitive signal we see that over time
the wire is low carrying a zero and then it rises at some point and has a one and then later

falls what the click will do is check the value of that wire if it's high it will cause an interrupt
when it checks okay so whenever the handler completes its operation it will check this wire and if
it's high it will cause an interrupt to be sent to one of the cores the handler will execute and at
some point will complete the operation we'll complete the processing of that

interrupt and at that point the click will check again to see whether the wire is high or not and if
it's high again or still high doesn't matter what happened in the middle here but if it's high at
that point then the click will signal another interrupt and then the handler code will run and
complete and then once again the click will check and in the third case

it finds out that it's low so no interrupt is signaled at this time now in the case of an edge
triggered signal we have the signal going from low to high and what the click is watching for is
that transition when it goes from low to high it doesn't really care too much about when it goes
from high back to low and the rising edge is what signals the interrupt

and there are really two options or two variations on the gateway in one case the gateway has a
single bit and the rising edge will set that bit to one and that will then cause the interrupt and
that bit will stay set at one until the handler completes and if there are additional rising edges
those will be ignored but at some point the handler will signal that the

processing is complete and at that point the click will clear the bit and then the next edge can set
the bit and cause another interrupt in the second option the gateway contains a counter and the idea
with this counter is that each rising edge will increment the counter so we're keeping a count of
how many interrupt signals have come in from the device and then each time the handler

claims a an interrupt then the click will decrement the counter and then once the handler indicates
that the handling of the interrupt is complete the click will then take a look at the counter and if
it's still positive the click will immediately signal another interrupt otherwise it will just wait
until a rising edge causes a counter to increment from zero to one

next let's get into some of the details and complications that arise with the plick first of all
devices are numbered starting at 1 and going up to 1023.

the number zero is used for no device at all next each core can contain more than one hart or
hardware thread the core might be a super scalar core or sometimes we say simultaneous
multi-threading and the idea is the core is running two threads simultaneously another thing is the
core in RISC-V can be executing in machine mode supervisor mode user mode the spec for the click
also talks about a

hypervisor mode but the interrupt will be sent to a particular mode okay so it may be sent to
supervisor mode or it may be sent to machine mode okay and we call these things targets so you know
each hart is a separate target and each mode is a separate target within that and the specification
can deal with up to 15 782 different targets numbered with numbers

now the specification goes up to these numbers 1023 and 15 781 although a particular implementation
may not be able to handle quite that many devices or targets okay next each external device that can
generate an interrupt is assigned a priority some are high priority devices and some are low
priority devices we want to assign a high priority number a large value to a device that we want

to have serviced immediately and others are very low priority typically slow devices have low
priority you know a keyboard for example doesn't need a super high priority because people just
can't type very fast but a rotating disk might need a very high priority if you are going to try to
start another read operation before the disc spins all the way around if you don't start the

operation soon enough on the same track the disc may have to do a full rotation to get back to the
right spot each core well I really should say target so each one of these targets is assigned a
threshold okay and here's the key a device will only interrupt a core if the device priority exceeds
the course threshold next let's talk about how a core controls the platform level interrupt

controller it does it by reading and writing to memory mapped I o registers so the click will occupy
a number of bytes in the physical address space of the processor and a core can do loads and stores
to addresses within that range of of memory so-called memory mapped registers to send or receive
data to and from the click first of all we have 123 words of four bytes each that are

used in to store the device priorities these are initialized at startup and we can set the
priorities of all the devices by writing or doing a store into these particular locations we also
have the priority thresholds for each of the targets so we have a bunch of words one for each
possible target and then we also have that big bit matrix 1023 devices times

15782 targets that's a bunch of bits so during initialization these memory locations will be written
into to basically configure we might even say program the platform level interrupt controller
although I don't really like to use the word program unless there's actual code being executed and
the platform level interrupt controller is not executing any code by any stretch of the

imagination then we have the pending bits so this is a allows a core to query to see which devices
currently have a pending bit and then finally we have and this is the probably the most important
one these are the claim words so for each target there is a word and you can either load or store to
this location and what the core will do is a load to the addresses associated with that

target to claim an interrupt so when an external interrupt is signaled on a particular core that
core will load from the word that corresponds to it a 4 byte value and that will be the id of the
device that is requesting an interrupt or it might be zero if some other core got to that interrupt
first and this same word is also used to do the complete operation so that's done

by storing into the word and in that case we store the id of the device whose interrupt we have just
completed a core might be uh handling several interrupts simultaneously and it can claim several and
complete several independently okay so um all told these memory mapped locations uh are mapped out
into a range of 64 megabytes it's really quite large of the physical address

space there are a lot of reserved bits or unused bits and the actual mapping I'm not going to go
into too too much in this video next let's look at how the platform level interrupt controller is
implemented in our system we're talking about the xv6 kernel and that will be run on QEMU which is
an emulator so the click is not actually implemented physically it's simply virtually

emulated by QEMU the number of cores in xv6 is determined by this constant in CPU which happens to
be eight so we have eight cores and QEMU is only going to be able to send the interrupts to machine
mode or supervisor mode and so with two different modes and eight cores we have 16 different targets
and they are numbered as shown here in our implementation of xv6 we are not

going to use machine interrupts at all all the interrupts will happen in supervisor mode QEMU in two
devices one is the virtual I o disk device and the other is the UART device and QEMU will give these
numbers one and ten so during the initialization phase the code will set the priority of both these
devices to one and then for each core it will set the course threshold to zero so the

threshold won't stop any particular interrupt from getting through and we will als also enable the
bit set the enable bit for both the devices so this means that any device can interrupt any core or
more precisely when a device is ready for an interrupt when it signals an interrupt all cores will
have an interrupt signal the benefit of signaling lots of cores

when you have an interrupt is that the first core that is free will get to it and take care of it
and so it will presumably get serviced more quickly if other cores are busy and can't claim the
interrupt then they won't get it but the one that can claim it first is the one that we want to
actually get it okay now we're ready to take a look at the code in xv6 and as you can see it's

pretty short we're looking at the code in click dot c and we have four routines we have two for
initialization and then one for the claim and one for the complete operation clickinet is executed
only once by core zero during startup and remember what we need to do for initialization we need to
set the priority of both devices to one so that's what's going on

here click is the address of the plex memory map region and then we add this to get to the
particular register for the priority for this device and that device and we just store a one into
those locations and so that's done once by core zero and clicking it hart is executed once for each
core so the first thing it does is determine what core it's running on so

it gets the CPU id and then we need to do two operations we need to and set the enable bit in the
enable matrix to one for both of the devices for this particular core and then we need to set this
course threshold to zero so that's what's going on here in the we use this uh macro click s enable
to get the address for the word we need to write to update the enable matrix so

what we're going to do is write into the enable matrix for this particular core and we're going to
write into it a word and that word will contain two one bits one in position ten for the UART and
one in position one for the disk and so that will set the enable bits so that these two devices are
allowed to interrupt this particular core so we write that and then

we use this other macro here to determine the memory mapped I o address for the priority register
and at this point we store zero into that word to set the priority threshold for this particular
core to be zero so these two macros click s enable and click s priority are coming from memory
layout.h and we can see them down here click is the starting address for the

memory mapped region for the plick and uh here we have a number of macros that aren't actually used
in the code we only use the s enable s priority and s claim we don't use the machine mode macros and
so each one of these just gives the address for this particular course register the enable register
the priority register and the claim register by the way when I was looking at the

code that creates the virtual address space for the kernel I noticed that we only memory map four
megabytes bytes and not 64 megabytes I think that's actually an error in the code it doesn't cause
any harm but I wanted to point that out okay so we have these two functions when a handler for some
interrupt is activated in the function device interrupt or dev

enter will be called and it will immediately invoke this click claim and that basically asks the
click which device is interrupted and the click will then return the number of that device and so
here we see that going on click claim for this particular core is the location of the claim register
and if the click wants the click will return the device id and that will be saved here

if some other core got to the click first and claimed the interrupt before this particular core then
this particular load from that memory mapped register will return zero in the dev enter or the
device interrupt function if we get something that's non-zero well we'll either get a 1 to indicate
it's the UART or 10 to indicate it's the disk and then we'll call the

appropriate handler routine for that device but if we get a zero we immediately return so one core
will handle the uh interrupt and other cores that get zero will immediately return go back to doing
whatever they were doing if we do service the device that is if we get something that's non-zero
then the dev enter the device interrupt function will then end by calling the

click complete function click complete will then store into the same location right the same
location the code for the interrupt that it is completing okay that's it for the click um I hope you
found that interesting I will see you in the next video
