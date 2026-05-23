# xv6 Kernel-2: General Features

| Field | Value |
| --- | --- |
| Video ID | `yfqxa-TYdFU` |
| URL | <https://www.youtube.com/watch?v=yfqxa-TYdFU> |
| Source | `docs/reference/scripts/original-raw/02-xv6_Kernel-2_-_General_Features-yfqxa-TYdFU.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

Hi welcome to another video in a series on the xv6 operating system kernel xv6 is an educational
kernel and in this video I am going to talk about some general features of the kernel it's meant to
run on a shared memory multiprocessor in other words it's meant to run on a system with multiple
cores but which all share a single range of main memory in this video and in the code I'm going

to be using the terms CPU core and hart synonymously so in the in the code sometimes you see CPU
sometimes you see hart and I tend to say core but they all mean a hardware processor capable of
executing a single thread of control this term hart which stands for hardware thread is something
that I first encountered when reading the RISC-V documentation the idea is normally a core will
execute

a single hardware thread but for some high performance cores you might have say two hardware threads
for example with the intel hyper threading architectures you might have two threads running on a
single core the idea is that these two threads are being interwoven the instructions are being
executed concurrently or possibly interwoven in such a way as to share some of the

processing hardware the idea is to increase the performance per transistor so this term hart appears
uh but it for our purposes we're going to be running it on a system with uh multiple cores and so
these terms are all synonymous there's just a single hardware thread per core okay main memory is
shared sometimes we call it RAM in a real operating system the issues regarding the caching of main

memory L1 L2 and so on uh are important and the kernel needs to sort of work around the caching
schemes and caching system in order to improve performance in this kernel all concerns about caching
are completely ignored the main memory is 128 megabytes and this is just hardwired into the kernel's
code it's fixed with a #define a typical real world operating system

kernel is going to look at the amount of memory that's available on the machine when it starts up
and it's going to configure itself to use all the physical memory but with xv6 it's just hardwired
in with the 128 megabytes the xv6 system can handle a couple of different devices the UART is the
device that handles serial communication this stands for universal asynchronous

receive transmit unit in the old days it was a separate chip but now it's integrated on the
processor chip along with the cores and so on this is the device that provides a communication
channel for printing and reading input from a keyboard typically if you program with the arduino
you're probably going to be familiar with this essentially it can send a byte stream

in one direction and receive a byte string coming back in the other direction the xv6 system uh has
one disk drive and that will be emulated of course with the file on your on your host uh laptop but
there's a disk uh device there are also timer interrupts uh the UART and the disk are shared between
all the cores uh but each core has its own timer interrupt so these are

local to the core the processors uh in the real world contained something called a plick or platform
level interrupt controller and what this does is a separate chip or separate circuit that deals with
interrupts coming in from all the different devices that might be on a system and it's figuring out
which core should be interrupted which core should be told about the

interrupt and allowed to uh you know handle the interrupt and deal with it the emulator will emulate
the uh platform level interrupt controller and there's also a core local interrupt controller which
is also part of the emulation and there is one of these core local interrupt controllers for each
core so the operating system has to deal with these things as well

memory management is pretty simple memory physical memory is divided into pages and the page size is
fixed with the pound sign defined at four kilobytes memory is allocated at least for the kernel from
a free list there's a free list of unused pages and it's a simple linked list and whenever the
kernel needs more memory it allocates the page from the free list

and when that page is no longer needed it is returned by adding it to the front of the free list
very basic memory allocation scheme there are no objects in the operating system in xv6 there are
several different structures and of course they're pointers but with an object-oriented programming
language typically you would out be allocating variable-sized objects

none of that's going on in a real-world kernel there are other aspects for there are other
techniques for allocating memory to accommodate variable size chunks of memory but that doesn't
happen in xv6 there's no malloc for example the virtual address spaces are handled with page tables
the page tables are three levels I've shown diagrammatically a three level

page table down at the bottom we have the data pages there's one table per process and in addition
there's one page table for the kernel itself which maps all of the physical memory that table for
the kernel is shared by all the cores the page table hardware can accommodate marking the data pages
as either readable writable executable and u stands for user mode uh

access and v stands for valid so a page may or may not be writable it may or may or may not be
executable it may or not may or may not be valid it can also be marked you or or not you and that
determines whether the page can be accessed when the processor is running in user mode well I just
use the word processor here and probably I should have said core so

sometimes I guess I use the word processor synonymously with with CPU and core it shows my age
perhaps so some pages may be restricted so that they can only be accessed when the core is running
in kernel mode and others other pages can be accessed by user mode code the scheduler for xv6 is
quite simple it's a basically a round robin scheduler each process is given a time slice and

then it goes back it becomes dormant sitting on the ready queue the size of all time slices are
fixed it happens to be fixed at one million cycles all the cores share a single ready queue
sometimes a ready queue is called a ready list or a run list or a run queue I'll use all those terms
probably but in any case there's only one of these things now the cores share the single ready queue

and so when a core is ready to run a process it will select a process the next runnable process from
the ready queue and it will give it a time slice on that core and then it will return it to the
ridicue after the time slice ends and so this ready queue is actually an array and what's happening
is each core for scheduling is going through that array linearly when it gets to the

bottom of the array it goes back up to the top and what it's doing is it's searching for a process
that is runnable that is it's ready to go and it's ready for its next time slice and when it finds
one it gives it a time slice and then after the time slice ends it uh returns it to the ready queue
as a runnable process and moves on to the next process and so it just keeps going through each

core keeps going through that array repeatedly and so I said this is a round robin scheduler it's
not quite round robin because what can happen is a particular process might be given a time slice
say on core number one and then after the time slice completes the core will put it back on the
ready queue and then move on to find another runnable process but immediately or very soon after
some

other core perhaps core number four might be searching through the array and it might happen to come
to that same process p and so it will give it a time slice and then put it back on the ready q when
the time slice ends so what happens is this single process p might be given a time slice on core
number one and then immediately after given another time slice on core number four

and so normally with round robin after process p is given a time slice it needs to wait until all
other runnable processes have a chance to run but with xv6 and the multiple cores it's sort of round
robin within each core so it's not quite a proper round-robin scheduling but it's it's it's simple
and it's probably just about as effective equally equally efficient

the boot sequence is uh pretty basic the emulator which will be emulating the RISC-V processor and
running our kernel basically skips all the boot sequence altogether what it's going to do is it's
going to load the kernel from some executable file on your host laptop and it's going to put it into
the memory or I should say the fixed physical memory that the sorry the

emulated uh physical memory of the risk processor it's emulating and it's going to put that kernel
code at a fixed location in fact this is the actual location that it uses there's no support in xv6
for booting a bootloader or a master boot record or bios or anything like that uh this xb6 uses a
couple of techniques for locking and concurrency control spinlocks are used

and there are two functions sleep and wake up with spinlocks uh you have two functions they're
called acquire and release and there's a single word in memory that word is zero if the lock is free
or unheld and it is one if the lock is busy or is locked is being held by some process and what a
choir will do is just basically in a tight loop wait for that word

to become zero unlocked and then when it finds that it's unlocked it will set it to one and release
just simply sets the word back to zero uh sleep and wake up well when a particular thread executes
the sleep function it will end its time slice and it will be placed back on the ready queue with a
status of runable I'm sorry with the status of not runnable and

it will then sleep until it's been until it is woken up so when a process executes the sleep
function that process will no longer be runnable it will go into a blocked or sleep state and it
will not be scheduled later some other process will execute the wake up function and wake up one or
more processes or zero or more processes and if our process happens to get woken

up then it will be changed back from a status of sleeping or blocked to runnable and then it will
get time slices after that there's also a third technique that is sometimes used and that is the
selective disabling of interrupts so each core has a status control word and there is a bit in that
control word that can be either set or cleared which either enables and allows interrupts or

disables and prevents interrupts by disabling interrupts a thread running on one core can prevent
being interrupted when a timer interrupt goes off or when an I o device calls for an interrupt so we
can use this technique on one core to prevent being interrupted by another thread on that same core
however xv6 is a multi-core system and disabling interrupts on one core has no impact on

other cores so a thread on another core can be modifying memory simultaneously xv6 has a number of
fixed limits these are declared with pound sign defines for example the number of processes is just
a fixed number as I said the ready queue is stored in an array and that array is allocated with a
fixed size the number of files open this the maximum number of files

that can be opened simultaneously as a fixed number and so on the kernel tends to use arrays and not
linked lists so much and so in several situations we are running through these arrays with a linear
search for example there's a function to kill a process and it's passed the process id and what it
does is it does a linear search of the array of processes actually this this ready queue here is

is not a separate uh data structure there's a there's a single array of processes and some of them
are marked runnable and some of them are not runnable and so when we want to find a process that's
runnable we basically just go through the process array looking for one that has a status of
runnable and likewise when we want to kill a process we just run through the array

looking for one that has a matching process id okay now let me talk about the user address space so
this is the virtual address space that a user mode program sees and here I'm showing the address
starting at location zero going up to the maximum virtual address the kernel will allocate this
address space in units of pages each one of these is a 4k page and so

when the exact system call is used the kernel will go out to the file system and find the executable
file which is in elf format and it will allocate several pages an integral number of pages here and
it will read in the data and the code into this region of memory those pages will then be marked
read write and executable by the kernel you also have a page allocated for the stack

we see here that the stack only gets one page four kilobytes of memory and so xv6 is somewhat
limited in this aspect a process that wants to grow its stack beyond this will not be able to and so
xv6 cannot support that and when a process tries to grow its stack beyond that it will simply be
aborted the process will be terminated and the way the kernel does that

is kind of clever it allocates what is called a guard page right here this card page is not readable
not writable in fact it's not accessible in user mode so if the user mode code tries to access this
page for its virtual address space it will immediately cause an exception and the process will be
thrown out and terminated the heap starts after the stack and grows

in units of pages this is called the break if you program in linux or unix you may be familiar with
this concept as the user mode code allocates things on its heap this boundary here will be moved up
the kernel will be invoked to allocate more pages and the break point will be moved up and will
consume some of the unused space these pages will be marked read and write

I should say that in a typical linux or unix system the stack is usually put out in high memory and
it grows down and it's capable of growing and memory is only filled up when these two when the heap
and the stack run into each other that's not done with xv6 we have just a single stack page down
here however we have something interesting going on we have two pages up at the

very top of the virtual memory space virtual memory happens to be 256 gigabytes so this unused space
here is actually huge and the heap may not take up very much of that and certainly it's not going to
take it all up because we only have 128 megabytes of physical memory and we're not going to be able
to accommodate anything larger than that any virtual address space that exceeds 128 megabytes

these pages up here are marked not accessible in user mode so user code cannot access these pages
user mode cannot read them or write them or execute any code from them these are used during trap
and exception processing so the trampoline page contains code so it's executable and when an
exception or interrupt occurs we're going to be executing code in the trampoline page

we'll discuss that later the trapframe is readable and writeable and that is where the registers
will be saved when a trap that is when an exception or an interrupt occurs the registers the entire
state of this user process will be saved and it will be saved in the trapframe by code in the
trampoline page there is as each virtual address space has its own trapframe page so it's they're
all

mapped to the same location but uh they're different physical memory pages so each process has its
own trapframe the trampoline page is shared by all processes so the exact same page of physical
memory is mapped into this into the same spot in all of the virtual address spaces one thing I was
going to mention was that I said that the kernel doesn't have a very sophisticated

memory allocation system it just has uh pages and they are kept in a free list and so all
allocations are in units of one page or four hundred and four thousand and ninety six bytes four k
bytes however we accommodate heaps for the user mode programs so a user mode program is free to
implement malloc with and allocate things on the heap in fact it's free to

implement whatever complex garbage collection algorithm it wants to but it just has this uh memory
that can be grown here to allocate its memory to to accommodate its memory needs so uh I mentioned
that this uh not you here means that uh the guard page is not ex valid when it's accessed in user
mode likewise the trampoline and trapframe pages are not accessible in user mode

c programmers will be familiar with the main function which takes a couple of arguments arg c and r
v and these point to the arguments that are passed into the program when it begins executing you may
also be familiar with something called arg e for environment but that is not accommodated with xv6
that doesn't xv6 doesn't use any environment variables just rxe and

rxv and how does that happen well when an exact system call is made the code in the kernel will set
up the virtual address space here and among other things it will allocate a page for the stack and
it will push onto the stack the arguments we'll get into the details of exactly what's pushed later
but basically it's going to push all the arguments onto the stack and then set the stack pointer to

be something besides right here it will allocate these things on the stack essentially pushing them
onto the stack and moving them into the registers so when the first instruction of the user program
is executed it will already find several things on the stack it will find its the arguments on the
stack now I mentioned that uh we could only have 256 gigabytes of virtual address space I

want to uh mention that the RISC-V architecture it's a pretty complex architecture and there's
several different options uh for page tables there are three options called sv32 sv-39 sv 48 the
version of the architecture being used for xv6 is the sv 39 version the first one is a two level
page table scheme the second one is a three level page table scheme and sp 48 is a four

level page table scheme so xv6 uses sv39 architecture which provides for our three level page tables
okay and with that particular scheme virtual addresses are 39 bits well 39 bits happens to be 2 to
the 39 happens to be 512 gigabytes so with this scheme we could actually access 512 gigabytes um two
to the 39 expressed in hex is this number here and so you can see uh

that expressed down here here are 39 bits and the first bit beyond that would be in here right here
a 8 is 1 0 0 0 and then 0 is 0 0 0 0 and so on so um it turns out that xv6 only uses 38 bits okay it
does not use the full 39 bits it's one bit shy and so two to the 38 is actually 256 gigabytes and
that number is four zero zero zero zero zero zero zero that is equated to our maximum number so

this our address range is from zero to three f that's three is one one and then f one one one one so
three f one one one one one one and then fff ffff and then fff so our maximum address is this and um
so maximum virtual address here is actually the address of the byte just beyond our virtual address
okay uh that's it for this video I'll see you on the next one
