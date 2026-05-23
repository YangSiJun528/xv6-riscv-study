# xv6 Kernel-6: Syscalls from Userland

| Field | Value |
| --- | --- |
| Video ID | `RdxHGyeoyqI` |
| URL | <https://www.youtube.com/watch?v=RdxHGyeoyqI> |
| Source | `docs/reference/scripts/original-raw/06-xv6_Kernel-6_-_Syscalls_from_Userland-RdxHGyeoyqI.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to talk
about system calls and how they can be made from user mode code I'll start by looking at a small c
program that makes a couple of system calls and then I'll look at the program called init code which
is the very first code that is executed in user mode this program is written in

assembly code so be prepared we'll see a little assembly code in this video but let's start with the
c program here is the code for kill it is a command that I'm sure you're familiar with we see that
it makes calls to some library functions fprintf and a2i are used here it also makes some calls to
exit and kill which are system calls it passes arguments uh in this case it passes a one two this is

to call exit system calls can return values but we don't see that happening here this program
includes user.h these two are not so interesting but user.h is relevant here because it contains the
function prototypes for both the library functions and the system calls so here's a code for user.h
okay and it does a pretty short program a short file it contains

function prototypes for the various system calls and it contains function prototypes for the library
functions so you can see each one of the 21 system calls that xv6 supports is listed here so for
example we see exit and we see kill each of these takes a single parameter and we also see that the
system calls return values this attribute stuff here is a bit of

compiler magic that tells the compiler that this particular function is guaranteed never to return
and so this might allow the compiler to do some optimizations also see function prototypes for the
library functions the xv6 system doesn't include a whole lot of library functions a normal uh
operating system would have hundreds of functions but we do see prototypes for

fprintf printf some string functions like string length and we nee we see uh a to I and some other
things we're not going to look at the code for these right now but these are in a file called ulib.c
we're more interested in the function code functions for these system calls there is a small
function for each one of these and these are all collected into one file called usys.s

which is an assembly language program the function codes for each one of these functions that is in
the assembly language is actually generated automatically by a perl script so let's take a look at
that so here we've got uses.pl and this script is going to generate some assembly code and here I'm
showing what the assembly code is generated and it goes into this file usys.s which will

be assembled and linked with the program like that we're compiling like kill dot c so what do we see
here well we uh start with a couple print statements uh a comment here and then a pound sign include
and here I'm showing what gets produced our comment and the pound sign include we're including
something called syscall.h and then for each of the 21 system calls

we're going to generate some code here and what do we do well for each one of them we create a dot
global statement here's the dot global with the particular name in this example it's it's open so
the name that we're dealing with is open then we have a label open colon which is right here and
then we have three instructions li is load immediate e call is environment call

and rep is returned so we see those li ecal and rep li a7 and then again cis underscore and then our
name sys underscore open so for each one of these we'll have a very small function this function
will execute a load immediate instruction which will move some constant into a7 sysopen is just a
number and we see that we are including syscall.h well here is this call.h

all it has is a collection of defined statements and for each of the 21 system calls it associates a
number in our example we're looking at open so we see that open has a system has a number of 15.

so all this does is move a 15 into register a7 and then then execute the equal instruction ecall
will end execution in user mode switch to kernel mode and we'll uh exec the kernel will execute some
code that will do whatever this is involved for opening a file and ultimately it will return to user
mode and execute the next instruction which is a return now we are passing

arguments in the open system call so if we look at open here we see that we pass two arguments
pointing to the file name and uh the mode that we want to open that file in and we return a value
arguments are passed in the a registers the first argument in a0 the second argument in a1 and if
there are additional arguments in a2 a3 and so on a return value will appear in a0

and the kernel will do that it will expect arguments in a0 1 2 and so on and upon return from the
kernel the result will be an a0 and that's why this little bit of code here doesn't mess with a0 a1
a2 and so on they are assumed to already be in the registers when this is invoked and the result
from the kernel in a0 will stay in a0 and it will be returned to

whoever invoked the open system call okay next let's take a look at initcode.s this is a user mode
program in fact it's somewhat special because it is the very first code that's executed in user mode
by the kernel and here we see the code for this program it's pretty short uh it includes
systemcall.h so in fact we make a couple system calls here we call the

exact system call and the exit system call now what does this little program do I've tried to show
it in this form here it simply calls the exact system call passing a pointer to the file name and a
pointer to an array and the array contains two elements this first argument is a pointer to a
sequence of bytes backslash I n I t nullbyte and our v is a pointer to an array of

two elements the first is a pointer to back flash in it and the second thing is the null the
terminating null these are each one byte and this is actually uh eight bytes a double word it's not
shown very clearly because they look like the same size here if exec returns which normally it would
not but if for some reason this file doesn't exist then it would return and what do we do then

well we invoke exit we're not even and then if exit should return which it definitely will not do
this program contains a go to statement which we'll call it again so we don't expect that to happen
so now let's look at the actual code we're including syscall.h that is our file that includes
numbers that associates a value with each one of the cis constants so here we're using cis exec

that's number seven we're also using cis exit that's number two okay and what do we have well we are
executing three instructions and then making the system call la loads the address into a0 of
variable init which is down here this second instruction loads a 1 with rgv which is the address of
this down here here we have exactly what's going on right here init

and these bytes are in our v and these two words so here we have init backslash init null byte and
here we have our v which is a contains the address uh of init and it contains a zero with an
alignment statement here so we load in the two arguments and we set a seven to the code number for
the exact system call and we make the e call e stands for environment call and then

if that should return we then have the code to call the exit system call we load into a7 the code
number which was two for the exit system call and then we perform the system call and if that should
return we just jump here and keep repeating it exit happens to take a parameter it takes a single
argument which is the exit code or return code we don't even bother to load a0 with

anything so who knows what it would actually be but hopefully exit sorry exec will never return okay
that's it I'll see you in the next video
