# xv6 Kernel-3: Startup + Organization

| Field | Value |
| --- | --- |
| Video ID | `6hhJ6JN95As` |
| URL | <https://www.youtube.com/watch?v=6hhJ6JN95As> |
| Source | `docs/reference/scripts/original-raw/03-xv6_Kernel-3_-_Startup_+_Organization-6hhJ6JN95As.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to
introduce the main function and talk a little bit about the startup procedure before that I'm going
to go over the file organization and I'll end by going over a couple of small files that we can take
care of quickly let's begin with the files that are included with the xv6 system

uh here I've listed out all of the files so you can see they're not too many of them the
organization is pretty straightforward you've got two directories kernel and user these are the
files that are in the kernel directory basically it's just a bunch of C code files some header files
there are several assembly language files there's entry.s which I'll discuss base

a little bit colonelvac there is switch.s very interesting one trampoline.s there's also initcode.s
there's also a file here that's used by the linker as well in the user directory you have the code
for the initial process as well as the code for the user application programs the shell and user
mode programs like cat echo and so on so in addition uh you've got the make file

to build all these files and you've got a readme in a license file so it's pretty straightforward
organization the x36 system runs on a multi-core computer and when it begins each core will start
executing all at once they all begin at the same time it's a shared memory system so all cores will
share the exact same memory and they will all begin executing the

same exact code and that code is in a file called entry .S it's not a very long file just a few
lines and that code will then transfer control to a c function called start in the file start.c and
that will then transfer control to the main function the assembly code that's in the entry file
basically gets things set up so that we can execute c programs it will

initialize the stack pointer register the sp register and it will initialize the tp register each
core will share main memory so they will be accessing the same set of global variables but each core
will need its own stack they can't overlap that wouldn't work at all so there's a separate stack
space for each of the course and the code and entry.s will initialize the stack pointer register

for the core appropriately also there's a tp register that stands for thread pointer but the tp
register actually will contain the core number the number of the core 0 1 2 or so on instead of some
sort of a thread pointer and that register will stay constant on that core throughout so that allows
the code to ask at any time what core am I running on so once those are set up

we transfer control to the start function in another video I talk about the different modes that the
RISC-V processor can execute in it can execute in machine mode supervisor mode and user mode the all
of the kernel runs in supervisor mode except for a tiny bit of code which is in this start.c file
and that so initially when the system begins execution it begins executing in machine mode and

the code here will take care of a few bookkeeping things and then switch to supervisor mode and the
cores will remain in supervisor mode after that okay now let's take a look at the main function here
is the code for main.c it's not very long and it contains nothing more than the main function so
let's see what happens remember that each core will begin executing this code in parallel so there

all cores will begin with this if statement CPU id is a short function that basically looks at and
returns the value of the tp register so on core 0 this function will return 0 and core 0 will then
execute this code here all other cores will execute this code here instead so what does the code do
well core 0 is tasked with initializing things so you see a lot of

calls to init and it annette this and that that and then knit some other stuff it also prints out
this message that uh the kernel is booting there is a global variable or a shared variable here it's
in the memory space so of course all cores will have access to it and it's used for synchronization
this keyword here volatile is a little bit of c magic that says that this variable is used for

synchronization possibly by multiple cores or concurrent threads and it is in fact used to control
the begin beginning of the other course so it is initialized to zero or false if you will and once
core zero is done initializing it will change it to true all the other cores go into this tight loop
where they're testing it and they keep testing until it is found

to be true and then they execute this code here and they start out by printing hart something is
starting the term hart is synonymous with core at least for these videos and so basically saying
core one is starting core two is starting and so on and they are pulling up their own core or CPU id
right here the last thing that core zero does is it starts the code for

the init process so that's what's going on here and then it sets started to one and then the other
product the other course will do some initialization here km in it trapping it click init these are
per core initialization things and they happen in core zero as well k a k v m uh trapping it and
clicking it happen here for core zero once all that happens on all of the cores

each core will then call the scheduler function and what scheduler will do is we'll look for a
process to execute so at that point all the cores will start executing processes we also see this
sync synchronize function here this is again a little bit of compiler magic compilers will try to
optimize things and what this is doing is telling the compiler to chill

out and not do that optimization the compiler might rearrange code in order to try to achieve
greater performance and efficiency what the synchronize does is tell the compiler make sure you
finish everything above it first before you start anything after it so it's saying please finish all
of this initialization before you change this variable to one you may not

be able to understand what I'm doing here you're saying to the compiler but I'm telling you you need
to finish doing every single thing above this before you think about changing that variable to one
likewise down here it's telling the compiler the same thing don't start executing this stuff until
you have completed this while loop here okay there are several small files that

I want to take a quick look at and get these out of the way types.h contains just these type defs
here you're probably familiar with uh these abbreviations for familiar types so on the architecture
we're using the RISC-V architecture with the tool chain it's a 64-bit machine integers will be 32
bits so we define unsigned 8-bit values unsigned 16-bit values unsigned 32-bit values and

unsigned 64-bit values we use this type uint 64 quite a bit for addresses and pointers okay next
file I want to look at is param dot h there are a number of things that are hard coded into the
kernel so let me just go through these quickly in proc the maximum number of processes is just set
to 64. the number of cores is eight then you have other things the number of open files the number
of

open files per system number of inodes number of devices the device number root dev max argument uh
this is the maximum number of arguments that you could have on a on an exact system call max op
blocks log size in buff fs size max path that's the maximum number of characters in a files path
name finally I want to look at a file called desk.h and this uh is

listed out here on these four pages so you can see what's going on but basically this is just for
the compiler contains a bunch of uh function prototypes so for example uh the file console.c
contains uh at least these three functions which might be used in in other files as well so this is
basically uh just the function prototypes there's nothing much of interest here we'll encounter

all these files later so let's go through all these pages on the last page there is a preprocessor
macro perhaps you've seen this in other contexts but this is the number of elements and you would
have a variable here and an array variable and what it does is it just asks how big is the entire
array and how big is a single element and it just gives you the number of elements

okay that's it for this video I'll see you in the next video
