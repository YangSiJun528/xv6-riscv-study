# xv6 Kernel-35: File Descriptors and Open Files

| Field | Value |
| --- | --- |
| Video ID | `oWuVGDese4k` |
| URL | <https://www.youtube.com/watch?v=oWuVGDese4k> |
| Source | `docs/reference/scripts/original-raw/35-xv6_Kernel-35_-_File_Descriptors_and_Open_Files-oWuVGDese4k.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'll be talking
about file descriptors and open files and I'll be going over the code in the file called file.c
let's begin by talking about the word file this term is ambiguous and fuzzy and can be confusing
because it has a couple of related meanings the most common use is when we talk

about a regular file on the disk so for example we might say that a directory contains several files
as well as directories as if the directory is something different but in fact a directory is a kind
of file as well and in fact within the file system a file can either be a directory file a regular
file or a device file these things each have an inode that refers to them

and they can be referenced using path names c programmers will be familiar with a structure called
file in capital letters this is a user mode only structure in other words it lives only in the
user's virtual address space and the kernel doesn't know about it at all there are a number of
library functions like f open f read f write and f close that use this file structure but these

are not system calls instead to get their work done they use system calls and like open close read
and write which are system calls so these communicate with the kernel by referring to files using a
file descriptor the file descriptor is a small integer and each small integer refers to a different
file but in this sense a file descriptor can also refer to a pipe as well as a

directory regular or device or device file so that's yet another meaning of file in this video we'll
be looking at a structure whose name is file and to make things even more confusing we have a couple
of files in the kernel which are called file.h and file.c and we'll be looking at the code in those
files to show how the file structure is used I've created this diagram

we've got two processes and each process is represented by a proc structure and we've got four file
structures here and then we've got a couple of inode structures and a pipe structure so let's start
with the proc structure it has a field called o file which is an array and this array contains well
it contains 15 elements as determined by this constant number of open files

and these are the file descriptors so this array is indexed by the file descriptor when a system
call like read or write makes a system call to the kernel will be passed a file descriptor and it
will use that as an index into this array and then it will follow this pointer to the file structure
that is being referred to so the kernel will allocate these pointers and allocate

slots in this array whenever an open system call is made and so the user code doesn't get to choose
which elements are used it is just returned to the file descriptor and then it must provide the same
file descriptor in the read write and close system calls so the file structure begins with a type
field and let's go through these fields and see what it's got it's got a reference

count which is just the number of pointers that are pointing to this structure and then it's got two
flags to tell whether this file is either readable or writable or perhaps both and then it's got two
pointers the pipe pointer will point to a pipe structure and the ip pointer if used will point to an
inode structure and then there's an offset and a major so let's quickly take a look at the

structure as defined in the file file.h and we see exactly that we see the type field which can
either be pipe inode or device and it can also be none for unused structures and then we've got the
reference count we've got the two readable and writable flags and then we've got a pointer to a pipe
structure and then we've got a pointer to an inode structure and the

offset and a device major number so a file that's open in some process can either be a pipe in which
case the type field will be pipe or it can be a directory or a regular file in which case the type
field will be fd underscore inode it'll be inode or it can be a device in which case the type field
is device if it is a pipe structure well each pipe has a read end and a right end as

determined by the settings of the readable and writable flag so this is pointing to the read end of
this pipe and the pipe field will point to the structure and the remaining fields will not be used
if it is a directory or a regular file then the pipe field will not be used but the ip field will
point to the inode and the offset field will contain an integer that tells where in that file

the next read or write will occur and the major field is unused now this file is open for writing
only so the next write operation will happen at offset 47.

and finally if it's a device then the ip pointer will point to an inode but the offset will be
unused and instead the major number which is in the inode will be copied into the major field of the
file structure now I want to remind you that in unix file descriptor 0 was always used for standard
n and file descriptor 1 is used for standard out and file descriptor 2 is

used for standard error so the parent process here apparently has standard in being open to a pipe
that's the read end which is what we would expect and for standard out the standard out will go to a
file and that file is described by this inode it happens to have a size of 900 but we're writing
starting at the beginning of this file and we've made it up to location 47 at

this point and for some reason standard error is closed which would not really be normal the kernel
will allocate entries into the o file array on each calls to the open system call and it will use
the next available unused slot so apparently standard error was open but it has been closed now
let's take a look at what happens when this parent forks a child so

the fork process will copy the proc structure into a new proc structure and in particular it will
copy the open file array into the child's proc structure okay so that means that every file that is
open in the parent such as this pipe right here or this file right here will be open in the child so
the red lines indicate that these pointers have just been copied so this pointer here is

copied as well and initially uh file descriptor number two is copied it was closed so it's a zero
here now let's say that the child decides to open a file the open system call will find the next
available unused file descriptor which happens to be two so it will use this slot right here and it
will open the file we've provided a path name to the open system call and it turns out that that

path name happens to refer to the exact same file that file descriptor 1 refers to file descriptor 1
had this file open for writing and was positioned on offset 47 but when we open it here apparently
we're opening it for read on reading and read only and the offset is initialized to zero so any read
using file descriptor two will come from the file starting at offset

zero and if we write to the file using offset one it will write characters starting at offset 47 and
increment them so this the file structure shows that there's a sort of an intermediate layer between
the proc structure and the inodes or pipes and that intermediate structure is used for a number of
things and in particular it's used for the readable and writable flags so you can

have one file that's open for writing by one process and for a different access protection in this
case reading by another process and it also contains a field for the offset so one file could be
what sorry one process could be at one point in the file using its file structure and another
process could be at another point in that file using its offset file structures are a critical
kernel

resource so next we need to look at how this resource is managed by the kernel there are a fixed
number of file structures that are allocated startup time and they are kept in an array shown here
this array has size 100 the exact size is determined by this constant infile every structure in this
array is either in use or is free and available for future use

there's a variable called f table that contains a structure and that structure has two fields lock
and file lock is a spinlock and file is the array of 100 structures the file structures uh have
these fields we saw these before we have a type field a reference count the readable and writable
flags the pipe pointer the ip pointer the offset and the major number

the reference count tells whether the structure is in use or not if a structure is free and unused
and available for future use then the reference count will be zero on the other hand if it's in use
then the reference count will be one or greater so the reference count keeps track of how many
pointers are pointing to this file structure the where are these pointers coming from

well remember that a process will have a number of open files and each open file is given a file
descriptor and that's a slot in this array called o-file so the o-file arrays contain pointers to
the file structures so that's where they're coming from and reference count counts the number of
pointers to this particular object the type field can either be fd none fbi

node fd device or fd pipe if the structure is unused then its type field will be none but really the
reference count is what determines whether the structure is unused or whether it's in use the pipe
field will contain a pointer to a pipe structure if and only if the type is fb pipe the ip pointer
will contain a pointer to an inode and that will be used if and

only if the type is either fdi node or fd device if it is inode then that means that this is
pointing to a device file or a regular file and so the offset will be valid on the other hand if the
inode is a device type then the major number will be used okay so this pin lock protects the
reference count we need to acquire the spinlock before allocating structures

and every time we increment or decrement the reference count we better first acquire the spinlock
offset will be changed and since there can be multiple processes referring to this object it needs
to be protected by a lock however there is no lock in this structure instead the offset field is
protected by the lock that is in the inode structure so if this thing is a directory file or

a regular file then ip will contain a pointer to an inode and remember that inodes contain a sleep
block so that's the lock that has to be acquired before the offset is read or modified so that's
sort of unusual and uh the other fields in this structure don't change they're initialized when the
structure is allocated and they don't change so therefore they don't

need a lock so the other fields do not change once the file structure is allocated and the reference
count goes from zero to one okay let's begin by taking a look at the code that initializes this data
structure and this is coming from the file file dot c so we see the definition of the variable f
table that's got two fields lock and file file is the array of these 100 file

structures remember that within with c when you define a variable the name of the variable comes
after the type and before the semicolon sometimes we see a name here and an example of that would be
the file structure in that case we're defining a new name which we can use in other places to refer
to this particular structure but in this example we see no variable

being created in fact that name file is used right here and this is where we are actually creating
some instances of the file structure but for this example the structure will be anonymous we don't
ever need to refer to this type anywhere else this function filenet is called from the main function
as it's running on core zero only so it'll get executed and it simply

initializes the spinlock that goes along with this f table structure so that's the spinlock right
here that protects all the reference fields in the file structures these things are assumed to be
initialized to zero so we don't see code to actually initialize those whenever we are ready to open
a file we need to locate an unused file structure in the array of 100 file structures and

allocate it and that's the purpose of the file alec function so it will acquire the spinlock here
and then before returning whether we return here or here it will release the spinlock and what is it
going to do well it's going to go through the file array in the f table function in the f table
variable and find one where the reference count is zero and then it's going to increment

the reference count to one and return a pointer to that file structure and if for some reason there
are no more available it will return the null pointer now let's take a look at the file dupe
function from time to time we will have a process and we will fork it so here's the parent process
and we will need to create a child process remember that the proc structure contains this array

of open files and pointing to various file structures and we are copying this array and therefore we
are adding pointers uh to each of the file objects that were pointed to by the parent and so we need
to go through these file structures and increment the reference counts and that's the purpose of the
file dupe function it begins by acquiring the lock on the f

table and then it increments the reference count releases the lock and returns it also does a quick
check to make sure that we are already holding a pointer to that particular file object next at some
point we are going to close files and we need to return these file structures to the free pool
because they are no longer in use so we have this file close function

that's passed the pointer to some file structure the first thing it's going to do is decrement the
reference count so here we acquire the lock on the table of 100 file structures uh and we decrement
the reference count right here and uh then if it has not gone to zero that is er we're
pre-decrementing it here and if it's still not zero then there's nothing to be done we don't need to
free

anything there is another pointer still in existence so uh we can just release the lock and return
immediately we've also got a check here to make sure that the reference count is not already zero
but if the reference count has gone to zero then we need to do additional work and that's shown here
so this ff variable is a local variable in this function that is a file structure so we're going

to copy the entire file structure right here and then we set its reference count to zero thereby
making it a free and unused file structure in the table of a hundred file structures and we're also
setting its type to fd none and then at that point we can release the lock however this file
structure may be pointing to some other structures that we need to take care of

it may be pointing to a pipe or it may be pointing to an inode so that's why we made a copy of it so
that we can access the pipe pointer here or the ip pointer here so if the type of the structure that
we just released was fd pipe then it will contain a pointer to a pipe structure right there will be
exactly two pointers to a pipe structure one from the read

end and one from the right end and whichever one we are we need to execute the pipe close function
to possibly free that structure okay so here we're calling pipe close which we looked at before and
is this the read end or the right end well we're passing it the value of the writable flag to
determine that on the other hand if the type was fdi node or fd device

then we are going to need to deal with the inode that's pointed to so for that we will use the I put
function which we covered earlier but essentially it's going to decrement the reference count for
this inode and if there are no other pointers to it it will free the inode structure in memory and
furthermore if they're uh if we're freeing the inode structure and there are no hard links

left that is the in link field is zero then this will also remove the file from the disk so disk
operations uh are involved here so we need to have these inside of a transaction so here we have the
begin op and the end up that uh define where the transaction occurs there are three more functions
in the file file.c there'..S file staff there'..S file read and there's a file

write function in the remainder of this video I'll just talk about file stat and I'll cover the
others in a future video file stat is used for the f-stat system call so let's quickly review what
the f-stat system call does this uh system call is available in both unix and xv6 and has more or
less the same definition it's passed a small integer which indicates which file we're interested in

and a pointer to some place in the user's virtual address space and what it will do is it will move
into the user's virtual address space some information about this file the memory area is laid out
according to this stat structure and contains the device number the I number the type of the file
the number of hard links and the size of the file and then it will return zero if everything

was okay or minus one if there was an error in unix we have the error number variable and the kernel
will also set that to indicate what caused the error if there was an error but in xv6 we don't mess
with the error we don't have the error number system going on we don't use that at all okay so when
a system call occurs the kernel will get control from the user's program and it will look into a

register to determine which system call it is and then it will invoke one of these cis functions to
actually do the work so in the case of the f-stat system call the kernel will then invoke sys f-stat
which is coming from file sysfile.c these functions are passed no arguments and they return the
value that should be returned to the user's code so they return either zero or minus

one in this case so what does this thing do well it needs to first obtain the arguments that the
user code was trying to pass in arguments are passed in registers and those registers got saved so
we have to call a function and functions like arg int and arg address were reviewed in a video a
long time ago by me and what they do is they're past the number of the argument you want to

retrieve so here we are retrieving the first argument zero and the second argument number one and in
the case of arg address that will be a virtual address so we're passed where to store that okay so
this is a local variable and this arg address function will find the saved register and move the
value into the location st here and if there is any problem it will return -1 and

we'll immediately return -1 the arg fd function I have not looked at but it's a similar kind of
thing it's passed the register number that is the argument number that we want to get and a place
where we want to store it now this is a file descriptor so we're past and the address of f and so
this function here will obtain a pointer to the file structure for this file

for this and so let's take a quick look at arg fd here's the code fetch the nth system call argument
as a file descriptor return both the file descriptor and the pointer to the corresponding file
structure okay so it is passed the number of the argument and it's passed two pointers okay that's
where to store the file descriptor that's a small number and this is where to store a pointer to the

file structure so it's going to start by calling argent and passing it in so that gets the first
argument or or the nth argument I should say and stores it in this local variable fd and if there's
a problem with that it returns -1 then it checks this file descriptor that we just retrieved okay to
make sure it's valid is it zero or greater but not greater than uh the number of files

that we have uh the greatest possible number of open files and then we look at the o file array and
see whether it's null or not okay so here we're going to the current procedures uh proc structure
and we're looking at the open file array in my picture here is the proc structure for a procedure
and we are looking at its o file structure we make sure that the

index is not less than zero or greater than the number of entries that we have here determined by
the number in o file and then we check to make sure that it's not a null pointer and if so we're
going to trace it and return a pointer to the file structure so that's what's going on here we check
it to make sure it's within range and not null and then we are grabbing it this pointer and

storing it in f if we want to capture the file descriptor itself then this will be a pointer to
where to store that file descriptor a small integer but otherwise it will be null so if it's null we
don't do anything but if it is a pointer then we store the file descriptor there and this argument
will be appointed to where does where to store the pointed to the

file this structure and so if this is null we don't do anything but if it is a pointer then we store
the pointer to the file structure there and then we return 0.

so now we're ready to go back to system we have a pointer to the file structure okay we were not
interested in the actual file descriptor number so we didn't bother saving that there was a problem
we returned -1 and so now we call file stat to do the work we're passing in a pointer to the file
structure as well as a virtual address st of the stat structure so

now we're back in file.c and here is the file stat function so we're uh getting a pointer to the
current procedure and then we are looking at this file structure f is it an inode or is it a device
well if so then it's I p pointer will be valid and so we can return something but if it's a pipe
then we have a problem so we return -1 so if it is pointing to an I node that is if the

type is inode or device it's either a regular file or a directory file or it's a device in either
case the ip pointer will point to something so we're going to lock the inode that's pointed to and
then we're going to use this stat I function which I talked about earlier and that is going to copy
the data from the inode into the virtual address space at this virtual address

and then we are sorry it's going to copy it into the kernel space and so here we have a variable in
the kernel space where we are going to be storing the data and then we unlock the inode and here we
copy it into the virtual address space and one thing that xv6 does is it does a lot of copying from
here you know copies that maybe you could eliminate so maybe we could copy

directly into the virtual address space with a different design but in any case we're using stat I
to copy it first to this stat structure here and then we're using copy out to copy it into the
virtual address space so we are copying to the virtual address space described by this page table
and here is the virtual address that we're copying it to and then here's where we're copying it

from namely this local st variable and we're copying however many bytes that structure takes and if
there's a problem we return minus one otherwise return zero and everything's okay so I'm going to
talk about the read and write functions in the next video so I will see you in the next video
