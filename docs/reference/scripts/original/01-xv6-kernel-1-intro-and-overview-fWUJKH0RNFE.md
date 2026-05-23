# xv6 Kernel-1: Intro and Overview

| Field | Value |
| --- | --- |
| Video ID | `fWUJKH0RNFE` |
| URL | <https://www.youtube.com/watch?v=fWUJKH0RNFE> |
| Source | `docs/reference/scripts/original-raw/01-xv6_Kernel-1_-_Intro_and_Overview-fWUJKH0RNFE.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This is the first in a series of videos on the xv6 operating system kernel this is a very short but
very sweet Unix-like operating system that's used for educational purposes it was developed at MIT
and used in other places as well this is an operating system that's used by students primarily in an
operating system course and there are two implementations of this kernel

one for the x86 architecture and one for the RISC-V architecture in this series of videos I'm going
to be talking about the RISC-V version the RISC-V version that is used is a 64-bit processor and
whether using the x86 version or the RISC-V version you're probably going to be using it in an
emulated fashion using an emulator like QEMU most likely you don't have a spare

computer sitting around probably a spare RISC-V processor is even less likely so instead you'll be
running the operating system if you choose to run it uh under an emulator but in any case it's meant
to run on a bare machine and in fact it's a multi-core operating system so QEMU is capable of
emulating multi-core systems as I said it's short and very sweet it's only about 6000 lines of code

most of it is written in the C programming language with maybe about 300 lines in assembly language
in this video series what I'm going to do is do a walkthrough of more or less all of the code to
give you an idea of what's going on with it the code is very simple and well-written and clean code
and I've read a lot of C code and written a lot of C code and I'm still learning new

coding techniques and I think this is an example that is worth studying in addition of course as an
operating system kernel it illustrates some of the basic concepts that you'd be learning in an
operating systems course so that's another good reason to study this code in detail for this video
series I'm not going to assume that you have any knowledge of the RISC-V

instruction set architecture but I will assume that you have had some assembly language coding I
intend to walk through the assembly language instructions line by line so I will hold your hand
there so don't worry have you had an operating system class maybe maybe you're in an operating
system class right now that is using the xv6 system in any case uh I've taught a few

operating system classes and I'll probably go over some of the concepts as we encounter them this is
going to be a long series so buckle your seat belts it's not for the faint of hart I'm assuming that
you've got some brains and furthermore I'm assuming that you're interested in this particular system
and can focus for the amount of time that it's going to be

for this video series so let's uh look at some of the features uh that the kernel has it's got
processes these processes run in their own virtual address spaces so there are page tables for a
page table for each address space to support the virtual address spaces the operating system
supports files Unix-like files and the directory hierarchy you can pipe data from

one program to another program of course there's a timer interrupt so there is multitasking the the
various processes are running in parallel with time slicing there are 21 system calls that are
implemented in xv6 this is not a lot the production unix systems have more like 300 system calls
maybe 500 system calls but this is enough to give you the core ideas of unix

there are a number of user programs that are supplied with this kernel and these can illustrate the
capabilities of this operating system the operating system can run a simple shell program in fact I
made a video that talks about the shell program in detail you can look for that if you want other
common unix programs cat echo grep kill which is used to terminate a

process ln which is used to create a hard link from one file to another and ls which is used to list
out the contents of a directory you can create directories you can remove files and wc is for
counting the words in a file as well as the characters so all in all I think that this can really be
considered a true unix system although it's pretty short and simple

there's a lot that's missing okay definitely all the complexity of a real operating system is is not
there a real operating system like linux may have you know as much as a hundred times as much code
and you know we're talking a million lines of the kernel and you know when you add the device
drivers in you can go up to many millions of lines of code so this is just too much

for any human to study really and if you want to find out how kernels work this is really the
operating system for you but there there are some things that are missing from your typical unix or
linux system there are no user ids and no login sequence no verification there are no protection
bits associated with files you know the read write execute protections that's not here

the mount command is just not available so you just have one file system in a real system the
virtual address spaces can be paged out to disk so that you can run more processes than will fit in
physical main memory that's not present in xv6 there's no support for networks no sockets or
anything like that in fact there's no way for processes to communicate or synchronize amongst
themselves

there are two device drivers but a real operating system a real world operating system is going to
have many more device drivers to support all kinds of different bits of hardware that you might find
and lastly there is only a limited amount of user code I listed the approximately 10 programs that
are distributed with it but a real usable linux or using unix system is

going to have lots and lots of apps so let's uh go over some of the system calls that are present
I'll go over these in more detail later when we encounter them but I just wanted to kind of list
them out here so you could see what we've got so fork this these are familiar from any unix or linux
system the parameters are slightly different um in some cases but

the idea is is there in concept anyway fork is used to create a new process weight is used to wait
for a child process to terminate exit is for terminating a process pipe is for creating pipes and
then we've got open close read and write for dealing with files we've got kill to terminate a
process we've got exec which is passed a file name and we'll read in that

file presumably it's an executable file and we'll load it into memory creating a new virtual address
space and execute it we can make inodes we can create links hard links and we can remove hard links
and unlink files thereby possibly removing them if it's the last link we can get information about
files uh we can change directory so we do have a notion of the current working

directory dupe is used for copying file descriptors now we can get program id sorry the process id
for the current process we can grow the heap so that's this function here this system call here and
we can put a process to sleep for a while we can also see how long the kernel has been running so in
the next videos and in this series I'll be going through the code in quite

a bit more detail but I just wanted to start with giving you an idea of what this operating system
kernel has and what its capabilities are so you can determine whether you want to make the
commitment for watching these videos as I said this series is not for the faint of hart it's not for
the amateurs it's for people who really want to look at an operating system kernel in

detail and understand a rather large but not too large body of code okay let's get started with the
next video
