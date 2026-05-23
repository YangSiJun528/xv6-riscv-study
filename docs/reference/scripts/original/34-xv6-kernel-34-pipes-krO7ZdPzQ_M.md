# xv6 Kernel-34: Pipes

| Field | Value |
| --- | --- |
| Video ID | `krO7ZdPzQ_M` |
| URL | <https://www.youtube.com/watch?v=krO7ZdPzQ_M> |
| Source | `docs/reference/scripts/original-raw/34-xv6_Kernel-34_-_Pipes-krO7ZdPzQ_M.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

Welcome to another video in a series on the xv6 operating system kernel in this video I'll be
talking about pipes and I'll do a Code walkthrough of the file pipe.c remember that in Unix a pipe
is a little bit like a file you can write data to it and you can read data from it however a file is
kept on disk while a pipe is entirely within memory so here we have a process let's call it

w writing data to the pipe and some other process R can be reading data from that pipe the data is
kept in some buffer inside the Kernel's memory and that buffer has a limited size so if process W
writes too many bytes the kernel will need to suspend it until the buffer is no longer full and
likewise if process R tries to read bytes but there are no bytes yet sitting in the buffer

then the kernel will need to suspend process R until process W writes the bytes into the pipe for
every open pipe the kernel will allocate and use exactly one structure of type pipe which I'm
showing here so this structure has a spinlock and that spinlock protects all the remaining fields in
the structure it has a buffer and that buffer is 512 bytes in our case the actual size is

determined by a constant which is defined to be 512.

and it has two indexes read and write which tell where in the buffer we are and a couple of Boolean
Flags called read open and right open which I will discuss a bit later Now with uh inodes the kernel
maintains a cache of inodes some of which are free and unused and whenever we want to allocate a new
inode we search through that array and find an unused inode and

then we allocate it and use that one with pipes things are done a little bit differently instead we
don't have a cache or an array of unused pipe structures and whenever we need a new pipe structure
the kernel will call K Alec and allocate an entire page and then when it's done with that structure
it will return that page to the free pool remember that our pages in xv6 are 4K

bytes in length so the structure itself will occupy just a little bit more than half a k the
remaining through almost three and a half K of the page will simply be unused and wasted for a
production operating system you might want to think about a slightly different organization
particularly if you think that we are going to have a lot of pipes but for xv6 we're not going

to have so many pipes and this is quite practical now next I want to talk about the code here and
show that the code corresponds to this exactly here we have the spinlock here's the definition for
the structure pipe coming from file pipe.c and we have the spinlock we have the buffer of size pipe
size we have the two indexes and we have the two Boolean Flags here

okay how are these indexes used well the in read tells where in the buffer the next byte to be
fetched for the reader is and the inrite index tells where in the buffer we should put the next byte
that is written to the pipe and so here in red I'm showing that this buffer contains uh several
bytes and we will add to the buffer at this point and we will retrieve from the buffer at this

point advancing the pointers buffer is a circular buffer so before we access the buffer we are going
to take the value modulo 512 so we have a wraparound situation here's another situation where we
have one two three four five six seven eight nine bytes in the buffer and they wrap around here you
can think of the buffer as an infinite sequence of bytes so in read and in

right can be Advanced well Beyond 512.

in other words we can write thousands and thousands of bytes into the buffer as long as we keep up
with the reading and never have more than 512 bytes in the buffer so these two situations are
equivalent views of the same situation an empty buffer situation will look like this whenever the
read index catches up to the right index we have an empty buffer okay the next character or the

next byte that we're going to write into the buffer will go into this position and in retails where
to read it and we haven't written that byte yet when the buffer is full we have a full 512 bytes in
the buffer so the right pointer will exceed the read index by exactly 512. and we can think of it
this way or we can think of it this way now in the past I wrote some code like

this and I stored I performed the module operator before storing the indexes so these indexes would
only range from 0 to 511 and uh that's really not a very good way to do things because it makes the
empty situation uh identical or appear to be identical to the full situation so it would require
some other data when reading the xb6 code I realize that this is really a much better way of

doing things so we allow in read and inrite to exceed 512 perhaps by quite a lot and we only take
the modular operator before accessing the data in the buffer and this is a superior way of doing
things that I learned by reading the xv6 code and that points out that you really can learn new
tricks by looking at the way someone else does something that is by reading other code

okay next let's get into talking about how this pipe structure is used we'll Begin by assuming that
we've got a process called p every process is represented by a proc structure and this object right
here is used to represent process p a process can have a number of files open at any one time the
user code refers to the individual files using a file descriptor the file

descriptor is a small number 0 1 2 and so on and it's used as an index into this array by the kernel
So within the proc structure we've got this o file array and it contains pointers to file objects so
for every open file there is a pointer in this array and it points to this file object and of course
the user process doesn't get to use pointers in the kernel space instead

it passes in a file descriptor so for example if it wants to read from file number two it passes in
a two and the kernel will then follow it to this file structure here it looks like we've got all the
file descriptors in use but if some of the files are closed and these entries are unused then there
would be a null stored in that particular intrigue the file object starts with a type field

and I'm going to be talking about pipes so the type for this example is pipe but in most cases the
file structure is going to point to an inode so in any case the file structures contain a pointer
and that pointer will point to some object it could be a pipe or it could be an inode normally it
would be an inode for a regular file or directory there are also a couple of flags the

readable flag and the writable flag within the file structure to tell whether the file can be read
or written or perhaps both in the case of a pipe we have two file structures okay we have one for
the read end and one for the right end and so the file structure for the read end well it would have
a type of FDA pipe and the readable flag would be true but it would

not be writable and for the right end of the file of the pipe we would have a file structure again
with type FD pipe and it would be marked writable but not readable and so we could have two pointers
or we will have two pointers pointing to the pipe and two file structures one for each end of the
pipe now these file structures contain a reference count too I didn't show it

here but the file structure can be pointed to by a number of different processes right this one
particular file could be opened by lots of different processes and so we have a number of different
pointers here and the reference count keeps track of those and when it goes to zero we can reclaim
the space used by the file structure and add it back to the free pool

the pipe structures on the other hand will have exactly two pointers pointing to them so in the pipe
structure we have these two fields that I showed earlier the read open and the right open flag and
so the read open slide will will be true if and only if there is a file object pointing to this pipe
structure and likewise the right open flag will be true if and only if there's a file for

the Right End pointing to this pipe structure and when both the read open and the right open are
false we can say there's no pointer pointing to this pipe object and then it can be returned in that
case we would go ahead and free the entire page that it sits on by calling the K free function okay
so what I want to do is assume that this process P has called the pipe

system call to create the pipe and to allocate two file objects and set up these pointers so after
the pipe system called This is the situation we have the the system call will find some empty slots
in the file descriptor array and it will use those and it will return those numbers in this case 2
and 4 to the user mode process so it can refer to either the read-in or the right end

now let's assume that this process Forks a couple of children one child will be a reader process and
the other child will be a writer process so whenever we Fork a process we make a full copy of this
array okay so we copy all of the open files so if a file was open in the parent it will be open in
the child and so initially both pointer two and pointer 4 here will

be set to point to these two file objects but we're going to assume that the reader process
immediately closes the right end so we decrement the count to this the reference count for this
object and set this pointer to zero because that pointer no longer exists and process p is also
going to Fork the right process and again the entire o file array will be copied and the reference
counts

increased for all the file objects and then we're going to assume that process W will immediately
close the read end so we'll end up following this pointer decrementing the reference count and then
setting that to null so now we have this situation by the way my indexes don't quite line up here
but I think you get the idea so at this point we have this situation where

process R Points to a file object for the read end of the pipe and process W points to a file object
for the right end of the pipe the parent process still has these pointers and perhaps it will go
ahead and close them or not but I don't know but in any case at this point process W can send data
to process R by issuing a system called a write system call on the file for file descriptor 4

while the reader can read data from the pipe by issuing a read system call using file descriptor 2.

so here is the pipe allocate function and what it's going to do is allocate the pipe structure and
these two file objects and return pointers to the two file objects that were allocated so it has to
return two pointers so that's done as follows the first argument at zero is a pointer to a pointer
and that's for the read end and the second argument is another

pointer to a pointer for the right end and you can think of it this way if you're not familiar with
this pointer to pointer stuff F0 points to an area where we will store a pointer to the read end
file object and F1 points to an area where we will store the pointer to the other file object that
we allocate and this function will return zero if everything's okay and minus one if

there's a problem so we start by initializing these two variables to zero okay so that they don't
point to anything and then we call file Outlook to allocate the read end and save that in this
variable here and we call file Outlook again and save the pointer to this object in this location
here and if there was a problem with either of these and we get null back

then we're going to go to this bad label down below where we clean up and the next thing we do is we
call kl to allocate a page in memory and if there's a problem with that again we get a null back and
we go to bad now if we go to bad let's take a look at the bad label down here we basically need to
clean up so we check to see whether we've allocated either of these file

objects and if we've allocated the read end well we call file close to get rid of it and if we've
allocated the right end we call file close to get rid of it we have this code here that checks Pi
that's the pointer to the page that we've allocated but if you look at this code I don't think you
can ever actually call K free here because Pi is set to zero here and if we go to

bad here Pi will be zero it won't get executed and likewise the only way we can take this jump is if
K Alec returned no in which case Pi will be zero so I think this test is actually um Superfluous and
cannot actually be used but in any case moving on the first the next thing we do is we allocate the
pipe structure so remember that we have a number of fields in the

pipe structure that we need to initialize we need to initialize the spinlock as well as the two
indexes into the buffer area and these two flags and we're doing that right here here we are
initializing the two indexes here we're initializing the two flags and here we are initializing a
lock associated with this pipe then we need to initialize the two file objects so for the read end
we set its

type 2 FD pipe and we make it readable but not writable and we set its pointer to point to the pipe
object that we just allocated then we allocate then we initialize the right end so we set the type
to fdpipe and we set it to writable but not readable and again we set its pointer to point to the
pipe object and finally we return 0 because everything's okay here is the pipe read function in

addition to the pipe allocate function there's also a read a write and a closed function so we only
have those four functions to deal with the pipe read function is past a pointer to one of these pipe
objects as well as an address and a byte count this address is a virtual address and the byte count
tells us how many bytes we want to get out of the pipe at most

and place into the user's virtual address space this function will return the number of bytes that
we actually read and this could be zero if the pipe has been closed down and if there's a problem uh
it might return minus one okay since we're going to be accessing the counters the in read and the in
right counters we need to First acquire the spinlock that protects this pipe

structure so we acquire that lock right here and then in this Loop here we are going to be checking
to see whether the pipe is empty right we have two situations that we need to watch for one is
whether the pipe is empty and the other is whether the pipe is full if the pipe is empty then the
reader will have to go to sleep and wait for a rider and if the pipe is full then a writer would
have to

go to sleep and wait for a reader to take some of the bites out of the pipe so here we're checking
to see whether these two things are equal and if the two indexes are the same we have a situation
like this where we have an empty pipe so if we still have a writer okay then we will go to sleep and
wait for that Rider to put something in if we don't have a writer then we've got

a problem um there's no point in going to sleep if the right open is false it means there is no file
object that is pointing to this pipe and so no bytes will ever be written and we will never be
awakened from any sleep we would do so we better not go to sleep instead we'll just return zero down
here so um let's say that the pipe is empty and we do have a rider then we will go

to sleep okay so the sleep is needs a a channel and the channel number that we're using that is the
code or identifier that we're using to the sleep function is uh what is the address of the read
index so we're passing it the address of the read field within the pipe structure that is currently
empty and that will be the channel That the writer will wake up on

it will issue a wake-up call using this channel to wake up this process when we call sleep we need
to be holding a spinlock and we are holding the spinlock and that will be momentarily released but
then when we are reawakened it will be reacquired and we'll loop back and again check to see whether
the pipe is empty it could be that some other process is another reader and

grabbed those bytes and then the pipe is still empty so we make these checks again and assuming that
there are bytes in the pipe then we can proceed down here before we go to sleep we also check to see
whether this process has been killed by some other process so we're grabbing a pointer to our proc
structure and we're checking the killed field here and if that's been set to true then we need

to you know terminate after the system call that's involved with this pipe reading and so we
immediately release the lock and return -1 to indicate that something is wrong okay so assuming
that's not the case we go to sleep eventually we wake up and we find that the either the file is no
longer empty or there is no writer anymore in which case we need to go

ahead and return something so what we're going to do is we're going to Loop through each byte we
need to find up to n bytes and we're going to ultimately return the number of bytes that we have
transferred and we're going to transfer each one byte at a time so we check here to see whether the
pipe is empty we could get to this situation after looping through this a couple of

times in which case we've emptied the pipe or it could be that on the first iteration we got into
here because there's no writer and so the pipe is empty and furthermore there's no Rider so in that
case we need to return zero immediately but normally we would get at least a couple of bytes so um I
will not be zero so um if the pipe has something in it then what we're going to do here is we're

going to go to the buffer area in the pipe structure using in read as the index and remember it's a
circular pipe and so we need to do mod the pipe size which happens to be 512. and then we use that
as an index to grab a byte of data advancing the index and we save the byte in ch and then we're
going to transfer that one character to the virtual address space so that's the copy out

function right here it's best to pointer to the page table that describes the user's virtual address
space as well as where we want to place that byte so this is the virtual address that was our
argument here offset by how many bytes we've read and we are passing a pointer to this byte that we
just retrieved as well as a byte count of one and if that works okay then we loop back but

otherwise we break out of this Loop and we've had we're done in any case we have red bites well
maybe we haven't actually retrieved any bytes and incremented the in read um we can't it can't hurt
to wake up a process but in any case we must be sure to wake up any writers because we have probably
removed some bites from the pipe and so the pipe is no longer empty

so we use the address of the right index for the writer so while this while readers will sleep on
the read index and wake up on the right index Riders will sleep on the right index and wake up on
the read index well after waking up any waiting waking up all waiting writers we will release the
spinlock and return the number of bytes that we have moved into the user's

virtual address space uh at this line okay next let's move on to the right function here is the pipe
right function like the pipe read function it's past a pointer to the pipe structure as well as an
address and a byte count the address is a virtual address and the user's address space and N is the
number of bytes to transfer from that virtual address base to the pipe it Returns the number of

bytes that have been moved into the pipe in the case of pipe Reed the number returned may be less
than the desired number the pipe read function will try to read at least one thing but will return
after getting at least one byte if there is nothing else waiting in the pipe on the other hand pipe
right will try to write all n bytes and will normally return in if there's some sort of an

error it may return -1 and if there's a problem with the page table it might return a value less
than n but normally it will return all in bytes so the structure of this function is a loop and each
iteration of the loop will try to move exactly one byte and that happens right here and we increment
I so I starts out as the count I is the count of bytes that we have

moved and it will keep being incremented and ultimately when we exit this loop I will be equal to n
and we will return in the number of bytes moved uh it may be that we execute exit prematurely if
there's a problem with the page table and we return a smaller in a smaller value than n so let's
take a look at this first of all we check in the body of the loop to see whether we have any readers

if the read open flag of the pipe structure is false then there are no readers so we immediately
return minus one likewise if we have been killed then the killed field of this process will have
been set and we need to abort immediately and return -1 but in either case we release the spinlock
first before returning so assuming everything's good we next need to check to see whether the pipe
is

full and here we have that check so in read plus the pipe size which is 512 if that equals the right
index then we've got a pipe full situation that is shown in this picture here where in read plus 512
is equal to in right if we've got a pipe full situation then we need to go to sleep but and we will
use as the channel to the sleep function the address of the right index in the

pipe structure okay and readers will wake up using that channel remember that the here's a pipe read
function and remember that when we do a wake up we wake up on the right index for the pipe that we
want to wake up before we go to sleep though it may be that we have some readers that are waiting
and so we need to wake up any sleeping readers it may be that this

Loop has looped through we started we might have started with an empty pipe um and the readers were
waiting to be awakened and in maybe a very large number greater than 512 and we may have executed
this Loop enough times to fill up the pipe in which case uh we need to go to sleep because the pipe
is full but we've got readers that went to sleep when the pipe

was empty so we need to wake up any readers that are out there first before we go to sleep okay
assuming that the pipe is not full then we can go ahead and move a bite to the pipe so that's what's
Happening Here we use the copy in function to copy bytes from a virtual address space as described
by this page table here to some place that's local variable CH and where are

we copying from well it's the virtual address plus the number of bytes already copied and we're
moving one byte at a time and if anything goes wrong then we abort the pipe right function with
however many bytes we've successfully copied we break out of this Loop and return the number of
bytes we've copied so far but assuming everything's okay then we've got a bite from the user's
virtual

address space and in this next line we move it into the buffer for this pipe so we are using the
right index and we are taking it mod 512 the pipe size and we're moving the byte into that location
in the in the buffer and we are then incrementing the right index as well as incrementing I and then
we loop back now after we're done filling the pipe there may have been some readers that

were sleeping and so we need to issue a wake-up call and again we're issuing the wake-up call using
as the channel the address of the in read index which is what any Sleepers for this pipe will have
gone to sleep on and finally we release the spinlock for this pipe and return the number of bytes
that we have transferred finally let's look at the pipe close function remember that from time to
time

a user process will call the close system call providing a file descriptor for example fault
descriptor number two and what that means is the user wants to remove this pointer and eliminate
this file from this process there is a reference count associated with each of these file objects
and if it does not go to zero when we eliminate this pointer then we don't do anything

but eventually it will go to zero when there are no more pointers to the file object and at that
point we need to return the file object to the free pool and eliminate this pointer here and so at
that point we will call the pipe close function on this object here so whenever we close the read
end or close the right end we will call this pipe close function so let's take a look at it it

is passed a pointer to the pipe structure and a flag to indicate whether we are closing the right
end or the read end and so that's this argument here We Begin by acquiring the lock for the pipe
object and then if we are closing the right end then we will set the right open flag in the pipe
structure to zero okay again that's this one right here to indicate that we have lost the pointer

from the file object that's associated with the right end now there could be readers or processes
that are reading and they are waiting because they found that the pipe was empty and so they're just
waiting for something to go into the pipe so we need to wake those up remember that a reader will be
sleeping if it finds that the pipe is empty so going to the pipe Reed

again we see that if the pipe is empty then we will be sleeping but we also check to make sure that
we do have a writer and if we eliminate the right end if we close down the right end we need to wake
this uh sleeping reader back up and it will check and then it will find that there is no uh right
and open and it will go ahead and return zero so that's what's going on here if

we're closing the right end we set the flag to zero to indicate that we do not have any right end
and we wake up any waiting readers on the other hand if we're closing the read end then we set the
read open flag to zero and wake up any sleeping writers for the same reason finally if we have
closed both the read end and the right end in other words if now both of these flags are zero then
we

are done with a pipe object all together so at that point we release the lock and call K free to
return the page that contained the pipe object back to the free pool of pages if the uh if there's
still one of the ends open then we will just release the spinlock for this pipe object and return
okay that's it for pipe.c I will see you in the next video
