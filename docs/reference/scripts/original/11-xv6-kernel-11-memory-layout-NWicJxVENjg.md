# xv6 Kernel-11: Memory Layout

| Field | Value |
| --- | --- |
| Video ID | `NWicJxVENjg` |
| URL | <https://www.youtube.com/watch?v=NWicJxVENjg> |
| Source | `docs/reference/scripts/original-raw/11-xv6_Kernel-11_-_Memory_Layout-NWicJxVENjg.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to talk
about how memory is laid out and I'm going to walk through the file memlayout.h I'll talk about the
general organization of physical memory I'll talk about the kernels virtual address space and the
kernel's page table and I will describe and motivate the need for the trampoline and trapframe

pages let's begin with this uh picture of a user thread which is executing instructions at some
point a trap occurs and we start executing instructions in the kernel and then at some other point
the kernel code will execute the cis rep or system return instruction and go back into user code the
user code executes in user mode and the kernel code executes in

supervisor mode RISC-V calls it uh supervisor mode sometimes I use the term kernel mode same idea so
let's look more closely at what happens at the time of the trap and the sret instruction so here
we're executing in user mode and we have a trap and then we have the context switch so that's when
we go into executing kernel code and it's running in what risk five calls the

supervisor mode or kernel mode and then at some later time we have the sysret instruction and we
return to executing the user code in user mode in the user's virtual address space so here's the
trap instruction this is what happens in the hardware and then down here we have the s rep
instruction and so the trap will be handled in hardware and what will it do well the core will
switch

into kernel mode or supervisor mode if you will it will disable interrupts it will save the program
counter in a control and status register called sepc and then it will load the program counter from
another csr called s t vec this register will contain the address of the first instruction in this
uservec routine and so effectively this is a jump to the uservec routine

the uservec routine is written in assembly language and it will begin by saving the user registers
the general purpose registers and the program counter are very important to the user program and
they need to be saved immediately so later we can restore them we can't really do anything without
using general purpose registers so this has to be more or less the first thing

we do saving the user registers and then we load a few of the critical kernel registers we need to
load the kernel stack pointer register and we also need to load the tp register which contains the
core number and finally we need to switch into the kernel's address space so there's another control
and status register called satp and we load that with the address of the

kernels page table thereby switching all execution into a different virtual address space and
finally we jump to this user trap function which is written in c uh later we get ready to when we
get ready to return to the user code we uh call this user trap rat instruction uh sorry routine and
this uh user trap rep function will call a routine called user rat user rep is coded in assembly
language

and it will restore the satp register which will switch us back into the user's virtual address
space and then directly before executing the sret instruction it will restore the user's registers
and finally the s red instruction will switch the mode into user mode and it will restore the
program counter from whatever is in the s epc register and finally enable interrupts and from

then on we execute in user mode now I said these things were functions but not really a function is
called and then it returns whereas with these things there's not really a return uservec ends with a
jump to user trap user trap is coded as a c function but there's never any return from it it calls
this and it calls that and eventually it calls a function user trap ret

and user trap will call user rep user read will never return to user trap rat so in that sense it's
not really a function but it's just a block of code likewise user ret is not going to have a normal
kind of return instead instead it's executing this esret instruction okay let's look at the what so
the problem is that the code that saves the user registers and loads the kernel registers

is running in the user's address space so these instructions and the memory locations that they
refer to have to be in the user's virtual address space likewise down here after we restore satp
we're running in the user's address space so the instructions have to be in that address space and
the memory locations from which we get the old register contents also needs

to be in the user's address space so let's look at the user's address space we showed this picture
earlier but the the user's virtual address space contains the code and the data of the program as
well as a page for stack and maybe some pages for a heap and then up at the very top we have this
thing called the trampoline page and we also have a trapframe page

these pages are in every virtual address space and they're at the exact same location namely the top
two pages what goes on below that is you know different between you know depends on the user program
so where exactly the stack page is can vary but these two pages are always at the same location the
users page table will mark these two pages as not accessible in user mode

so if the user code tries to access them it will get an error so they're sort of invisible to the
user code the trampoline page will contain code so it's marked it's marked readable and executable
and the trapframe page will contain data so it's marked readable and writable okay so here is
another picture showing what's going on and I'll come back to this in a second but we've got the

virtual address space for all of the user processes there are up to 64 user processes and each one
will have a trapframe page and a trampoline page the trampoline in blue the trapframe in red there
is exactly one virtual address space for the kernel and all the kernel uh code will use the same
address space all the cores uh will share this one virtual address space

and it contains a number of things but in particular I want to point out it contains the trampoline
page at the very top of the uh address space so before I talk more about that let's uh oops let's uh
look at the trampoline page and the trapframe page as I said there is one trampoline page and it
contains code and in particular it contains this uservac and this user rep

functions so it contains uservac and user wreck these two assembly language routines and it contains
nothing else it is mapped into the very same address namely the top page of all virtual address
spaces and by that I mean the 64 virtual address spaces for one for each of the user processes and
the kernel address space and as I said it's marked readable and executable

but it cannot be accessed when running in user mode there is a track frame page and each process has
its own page so each page is different there are 64 processes some of them may be dormant and not
executing but there can be at most 64 active processes and each virtual address space will have its
own trapframe and that will contain the data and in particular it will contain the area

where we will save the users registers so marked read writable not accessible in user mode so in
this picture down here I'm showing the top of all the 64 virtual address spaces for the user
processes and the top page is the trampoline page and the second to the top page is the trapframe
page and the idea here is that the page table maps all of these trapframe pages into

the same physical page sorry these trampoline pages are all mapped into the same physical address
but the trapframe pages down here in blue are each mapped to a different physical page so they are
not shared it's okay to share code because it doesn't change and it can be shared without any
problem but each user process will need its own area in which to save the registers

so now let's take a look at this picture which is the uh physical memory on the right hand side and
the kernel's virtual address space on the left hand side so let's start out with the physical memory
it starts at zero and goes all the way up to this enormous number of 256 exabytes the vast majority
of it majority of it is unused and not allocated nothing is up there

the computer that xv6 is intended to run on will have 128 megabytes of installed physical main
memory the RAM and that's in this region here it will be located at this particular address which
happens to be the two gigabyte boundary and below that we have space for the memory mapped devices
so we see the serial communication device it gets a page we see the disk device that gets a

page we see the platform level interrupt controller that gets four megabytes and we also see uh core
local interrupt controller down here is the boot rom after the kernel is executing the the booting
process is over so this is not accessed at all by the kernel now over on the left hand side we see
the virtual address space for the kernel and the first thing to note is that

all the physical memory is direct mapped into the virtual address space that means the kernel can
provide an address and it doesn't need to really make a distinction between whether it's a virtual
address or physical memory address the same number can be used for physical locations even though
virtual addressing is turned on it will go to the same spot and likewise all the stuff down here

is direct mapped so to access the virtual disk and to access the serial communication device and the
platform level interrupt controller the kernel can just use physical addresses and since they're
direct mapped there won't be a problem the core local interrupt controller is only accessed in
machine mode remember that when we're in machine mode there is no virtual addressing page the

page table is not active and we only use physical addresses the core local interrupt controller is
only accessed uh in machine mode so there's not actually a need for it to be mapped into the virtual
address space now the other thing we want to talk about is what's going on up here at the top of the
virtual address space at 256 gigabytes minus one page we have the trampoline page

and then we have a number of pages called stack pages kernel stack pages there is one for each user
process so there are 64 of these things all of these pages the trampoline and the in the stack pages
are separated by a guard page that guard page is not mapped it has it's not readable writable or
executable so any attempt to access it will cause an error and that just catches any stack

overflow from the kernel stack areas within the kernel uh in the physical main memory we're going to
see the kernel code here and the read-only data followed by the read-write data for the kernel this
would be the variables that are used in the kernel and then above that we have the rest of uh
physical memory and that will be used for the page allocator we have these two functions k alec

and k free and these this memory here is initially divided into pages and those pages are all kept
on a free list every time we call the k alec function it will allocate a page of free memory from
that free list so one of these pages will be allocated for use by something and then when we're done
using that page we can call the k-free routine and it will return it

to the free area here and it will be put back on the free list and used uh the next time we need a
page so the most of the physical memory will be occupied with this uh in this page region that's
used by k alec and k free the this picture here which attempts to show things a little bit
differently again we've got physical memory here somewhere and we've got over here the virtual

address spaces we have 64 user processes so I'm showing only three of them here but there's a
virtual address space for each of the user processes there's exactly one virtual address space for
the kernel so all the cores will share this one virtual address space and this one page table for
the kernel we see the trampoline pages in blue here at the top of all of the virtual outer

spaces and at the top of each of the user mode virtual outer spaces there is a trapframe shown in
red now these pages are mapped somewhere into physical memory the trampoline page will be mapped
into code so it's a page that's actually part of the uh kernel's text area so the kernel code is in
this area here in physical memory here so the trampoline page will be mapped somewhere

into the text area the trapframe pages will be allocated when the kernel starts up and they will be
allocated somewhere in the free region so they will be allocated somewhere in this region here where
the pages that are used by k alec and k free are kept and so I'm showing them maps somewhere in that
region of course all the physical memory is direct map so the

kernel can access them directly if it needs to but they the page tables for each one of the user
processes will map those trapframe pages into one of the pages that was obtained with a call to k
alec we've also got these kernel stack pages up here okay for each of the 64 processes we need a
kernel stack and that is because here when we uh first go into kernel mode the trap occurs and we
begin

executing we save the user registers and we load the kernel registers each process will need a
separate stack obviously two separate threads cannot share the same stack they each need an
individual stack so each one of the 64 threads that are associated with the 64 user processes will
have its own stack so when the user mode code switches over into uh executing in kernel mode

it will need to access its kernel stack and so here we have one stack page for each of the 64 user
processes at startup there are 64 pages that are allocated from the free pool by calling k alex and
each time chaolic is called that page is mapped into one of these regions up here and here I'm
showing the guard pages separating those pages okay now I think we're ready to actually

look at the code for mem layout dot h so uh we've got just two pages here let's start with this one
we've got some comments and we begin with the address of the serial device so that's this address
here and so you see that address and that's just defined with a define constant here and then we
have the page for the disk device and that is shown right here so

that's this address here that we're uh defining this is the core local interrupter and it's mapped
into some particular address and that's uh shown here okay it's uh not used when we're executing in
kernel mode but it is used in machine mode so we give it an address here that will be used for
timers and timer interrupts those are those are handled the the timer interrupt is

handled with code that runs in machine mode and the device that's creating those interrupts is is
accessed here now these memory map devices have what are called register sometimes hardware
registers to be not to be confused with the general purpose registers in the core so that's what's
going on here there is one register called m-time okay and where is that located well it's

at this starting location plus some constant and that hardware register contains uh the number of
cycles since uh the boot occurred so we the kernel code can read from that address and effectively
goes to this device and gets uh the current value so that device is constantly updating the value
that's stored at this so-called memory location we can just read it

when we need the current time this one here in time compare that is a function recall that with the
c preprocessor it matters whether you've got a space here or not if we have a space then this is
just what gets substituted if you do not have a space then this is a parameter or an argument if you
will and this is a defining a function so given a particular core number

we will have the expression here that computes the address of one of the registers there are eight
cores and so there are eight hardware registers and where are they located well the starting address
plus some value and each register is 8 bytes so this expression here computes the address of one of
these hardware registers this register will be loaded by the

kernel and it will tell when to give the next interrupt so the kernel writes to this location and
then when an interrupt sorry when that time is reached an interrupt will be generated automatically
by this device and for the platform level interrupt controller we've got a similar kind of thing
going on we've got a starting address and then we've got some various uh

different functions and you can see we've got some different hardware registers here defined as a
function of the core number okay hart id is just the number zero through seven of the core and these
expressions compute various addresses within that device okay moving on to the second page of mim
layout we have a definition of where the kernel is to be loaded as I said this number

here is two gigabytes that's the kernel base right here where the kernel is loaded we also have the
top of physical main memory 128 megabytes of main memory past the starting location is the top of
physical main memory fizz top is so that's giving this address right here this constant trampoline
gives the address of the trampoline page which is the maximum virtual address

256 gigabytes less one page so that's this this point right here and then finally we have the
address of the stack pages okay that's a function given a process number 0 through 63 we compute an
address here and you can work out this uh algebra here but basically we're doing it in units of two
pages because of the card pages so every two pages we have the address

of a stack page and finally we have a constant trapframe which is just the address of the trapframe
page so it's just the address of the trampoline page minus 4096 which is this value right here so
that's what's going on with this definition here okay that's it for memory layout see you in the
next video
