# xv6 Kernel-32: fs.c Part-1

| Field | Value |
| --- | --- |
| Video ID | `LTAOiYPemZM` |
| URL | <https://www.youtube.com/watch?v=LTAOiYPemZM> |
| Source | `docs/reference/scripts/original-raw/32-xv6_Kernel-32_-_fs.c_Part-1-LTAOiYPemZM.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I will cover the
functions and walk through the code in the file fs dot c there are a large number of functions and
I've broken this into two separate videos each is about 45 minutes long and this is the first video
in that series in the previous video I introduced the data structures and gave a summary

outline of the functions and in this video I'll go over the code in the functions but here let me
just review this list these are the functions that I will be covering in this video and I just will
present this material here on the screen so you can have a reference as to what is covered in this
video these functions are all covered b map I will cover in part two

however in this video I will cover eye trunk and stat I and then the other functions read I and so
on will be covered in the next video and in the next video we will end up talking about the main
function name I which is sort of what we're working toward it's passed a pointer to a path name
string and it figures out which file on the disk that is and returns a

pointer to an inode representing that file okay let's get going let's start with the functions that
are involved with initialization so here's the main function uh this is from file main.c and recall
that it tests the core and all of these functions are executed on core zero that is they're executed
only once and these functions are executed by the other cores

and after all this we start scheduling right here we see b init which initializes the data that's
associated with the buffers and then we call I init remember that the inode cache consists of an
array it happens to have 50 structures in it and the structures are inodes and there is one spinlock
that protects the entire array and each structure has its own sleep block called lock

so here is the code for I annette and you see we're initializing the one lock that's associated with
the I table and then we're going through the entire array right here and initializing each of the
sleep blocks these structures also have a reference count and if this is zero that indicates that
this element is unused and available for uh use in the future and

the reference field is initialized to zero by default okay going back to main we see a call to file
in it this uh I haven't talked about this yet but essentially this initializes a lock as well and we
do some initialization related to the device and then we create the init process so here we create
the first user process and at some point we will start scheduling okay and one of the cores

will pick up this process and we'll begin the very first time slice we can't do any disk I o until
we are actually running in a user process because the disk io stuff needs to sleep so let's take a
look at what happens when uh init is first executed well remember that four cred from the file proc
dot c will be called to schedule the first time slice and it contains this code here that asks

whether this is the first time it's ever been called right there's this local variable sorry this is
a static variable it's initialized to true and so the first time this function is called by any
process it will execute this code here and then set first to zero and what does that do well it
calls fs annette so let's take a look at fsnet now we're running inside of a process

and here is fsnet oh uh shows read sb2 the first thing it does is it calls read sb which will read
in the super block from the disk so here we see a call to be read and this will be the first read we
do and sets bp to point to the buffer and then it moves data from the buffer and how much does it
move well the size of the super block structure and it moves it into this

super block structure but we're passing it the address of that super block structure and then we
release that buffer so after this point here we have the super block variables uh stored in that
super block structure and we can access things like the magic field so we immediately check it to
make sure that the device contains a file system of the type that we are expecting

that the file system has been properly initialized and so on and then we call a net log and I
discussed the logging system uh in a previous video but this is where that gets called so after this
we can create transactions that is we can call begin op and end up and log right next let's take a
look at the function b alec every time we create or grow a regular

file or a disk file we need to find an unused block on the desk and zero it out and then add it to
the file so b alec is called to allocate and zero out a disk block and it will return the number of
the block that it found so basically it's going to search through the bitmap for an unused block
remember that our bitmap here can in general consist of several

blocks okay with only a tiny disk with a thousand blocks in it it'll only have one block but in
general this code will search a number of blocks okay so this code can accommodate a much larger
disk so it's got let's look at the structure of this thing it's got two nested for loops okay and
the first one will go through the blocks and for each block in the bitmap it will

read that block and then the next loop will go through all the bits in the block looking for one
that is free okay so um here this outer loop has an index variable of b and it's being incremented
by the number of bits in a block well they're 1020 bytes with eight bits each so it's b goes from
zero and then eight k and then 16k and so on okay so each one of these will um

b block and the first thing it does is it reads that block in from the device okay this function b
block is passed a block number as well as a pointer to the super block and it will figure out which
block on disk contains that bit so we read the first block and then the second block using the bit
at the beginning of the block as the index here this inner loop

runs through the bits within that block so it goes from zero up to eight thousand one hundred and
ninety one eight one nine one so it uh that's this loop index here it's incremented by one however
uh so the actual bit that we're looking at is this number plus the index within to the within the
block okay and that's b plus b I and of course we also stop if we

hit the size of the disk if we search through all of the bits in the bitmap up to well in this case
we have only uh 1 000 blocks so if we search up to this point without finding anything then we
immediately abort um and um that causes us to panic and say that we're out of blocks that the disc
is full there are no free blocks so once we have a bit number within the block that's bi

we need to figure out which bit within the word or within the byte I should say so we are using mod
8 to figure out which bit within the byte and divide by eight to figure out which byte within the
block so here we create a mask all zeros except for one one bit in the appropriate position and so
that's our mask and it's a byte although it's declared as a 32-bit uh value

we're only concerned about the eight bits in it and then we here we are looking at uh one individual
byte and anding it with the mask to look at one particular bit and if that is zero then this
indicates it's a free block okay zero is used to indicate free and the one bit is just to indicate
in use so if we find a free block then the first thing we do is we set the bet so

here we're taking the same word and we're ordering it with the mask to uh set that one bit to a one
and then we are writing the block back we've changed this one block in the bitmap so we need to
write it back to the disk and we do that here and then we release that block and finally we zero
that block so um here we are using b plus bi that's the actual block

number that we found that is free so we're passing this block number to the b0 function and uh then
we return the block number here and I just want to say that after we've searched this entire block
and not found any free bits then we release that block and we move on to the next block in the
bitmap so this b0 function is right here it's only called from uh b alec where I just

showed you and so you can more or less forget about it after this um but what it does is it is past
the block number okay and it uh reads that block getting a buffer pointer to the buffer that
contains that data and here we are updating the data and in fact we're using this function memset to
move a bunch of zeros how many zeros well uh the block size is 1024 so we're moving

that many zero bytes into this data area and then we're writing that block back to the disk and
finally we're releasing that buffer now so these functions here are calling b read log write and b
release okay so that means they must be within a transaction so we'll see that all of the functions
in this file here are supposed to be in a transaction they will call be read and log right and

we assume that all of this is happening within some transaction so this code outside of the file
fs.c will begin the transaction and then call these functions or some of these functions and then
ultimately call the end op function to terminate the transaction as a professor I can't help but
insert a little commentary at this point this algorithm is pretty straightforward

and easy to understand and for our purposes of xv6 it's adequate but I have some questions about
efficiency for one thing it's searching every bit in the block one by one so this inner loop is
executing 8k times and for each execution it's building this mask and then doing a little bit of
computation now when we allocate a block we need to search through all the bits until we

find a bit that is zero and so we might have a bunch of blocks that are in use so we go through a
bunch of bits that are one one approach would be to look at entire words at a time rather than
individual bits at a time so the idea is that we would have a loop that would loop through all the
words in the block and it would look at each word and ask is this word equal to a word with all one

bits and if it is then it doesn't contain any zero bits so there are no free blocks represented by
that word so we can move on to the next word so it searches word by word instead of bit by bit and
then when we finally find a word that is not all ones it must contain a zero bit so at that point we
search the word and find out which block is actually free but still there's a problem this is

doing a linear search if the disk is almost entirely full we have to search through the entire
bitmap and for large devices that could be time consuming so we don't really like to do linear
searches in the first place I want to just mention another approach to managing free lists so the
idea is that um we keep a linked list of pointers and so each one of these structures represents

a block on disk okay and each block contains 256 words shall we say the first word points to the
next block in the linked list and the remaining words in the block point to free blocks okay so um
in order to get a free block we go to this linked list and maybe these have been allocated but here
is the first block so we can allocate that one and that's pretty quick because we just

keep a pointer it's a stack basically when we return blocks to the free pool we push onto this stack
and then when we allocate blocks we pop off of the stack and then when we get to the end okay after
we allocate this block the next allocate well we just allocate this block itself the entire block
and now this block becomes the head of the list and we begin with our stack pointer here

and when we want to allocate a block we allocate this one and then this one and then this one these
blocks will be kept on disk and um you can see that uh the these are uh each one of these out it
requires a block but we have a bunch of free blocks anyway so it's not really a cost to uh store
this uh linked list on disk as we consume blocks as blocks are allocated we

consume the blocks that are used for the linked list as well and we would also like to kick one or
two of these blocks in memory to make this process faster so we can keep allocating here we can
allocate this block and then we can allocate this block and we can keep going but at some point we
have to read in the next block from disk and likewise when we're turning blocks we

can fill up this block and after this block is completely full well we need to write this one back
to disk and we just have we're turning a block so we actually have a place to write it so we have a
slightly complicated algorithm but it's going to be much more efficient because returning a block
will normally involve just writing a number into this and advancing the stack

pointer and allocating a block will be just grabbing the next element on this and decrementing or
increasing the stack pointer the one aspect of the free block being kept as a bid map is from time
to time you want to allocate a number of blocks and you want them to be close together on the disk
for example when we allocate the blocks for a file we would like all those blocks to be

close together because if one is red it's quite likely that the others will be needed as well this
approach here doesn't really deal with that whereas with the bitmap if you're trying to allocate
let's say 10 blocks altogether then you can search the bitmap for a place where you find 10 0 bits
that is 10 free blocks altogether and so there you know we can go down a

rabbit hole here but I just want to point out that this code is simple and easy to understand and
adequate for the xv6 teaching kernel but in reality things are going to get quite a bit more
complicated so now let's turn to looking at the corresponding free routine from time to time files
are deleted or their size is reduced and we need to return the blocks in the file to the

free pool and for that we have the befree function when we allocate a block from the free pool we
have to search the bitmap to find an unused block but when we free a block we just need to find the
corresponding bit in the bitmap and change it so b3 is passed the block number and it calls b block
with that block number to determine which block in the bitmap will contain

the corresponding bit for that block and then we read that block from the bitmap area in to a buffer
and here we compute the bit within that block so this is bits per block and we use the mod operator
to figure out a number between 0 and 8191 within that block then we use the mod and the divide
operator to figure out uh which bit within a byte and which byte within

the block so here we are making a mask all zeros except for a single one bit in the corresponding
position using the mod operator and here we are figuring out which byte within the data block will
contain that bit and we're looking at that so we're adding it with the mask to determine the value
of the bit zero is used to indicate tree and if it's already zero we've got a problem so

we panic but otherwise we set the same bite by anding it with the knot of the mask right so we turn
all the zero bits into one and so that preserves them but the bit that we're interested in here
turns into a zero and by ending it we clear it so this sets the or I should say clears the bit that
we're interested in to zero and then we write that buffer back to disk and release the buffer

next I want to talk about the I get and the I put functions as well as the I lock and I unlock
functions whenever we want to access an existing file we'll call I get and I lock and then when
we're done with it we'll call unlock and I put so I get is past an inode number as well as the
device in which we want to find this file and it returns a pointer to that inode in the cache so it
will

look in the cache to see if we already have that inode open if you will and if we do we'll use that
one otherwise it will allocate a new entry in the cache and devote it to storing that particular
inode but in any case it will increment the reference count to indicate that that structure in the
cache is used for that particular inode it won't read in the inode from disk and

it won't lock the inode to do that we call I lock which will block the node and then if it has not
yet been read in from the desk it will go ahead and read it in so then when we're done using this
particular inode we call unlock and it will unlock the node and we can call I put okay I put will
decrement the reference count so if it was already there when we

called I get we incremented the reference count and then when we're done with it we call I put and
it decrements the reference count and it checks to see whether we have no more hard links to it and
if so it gets rid of the file uh but otherwise um the file will stay in the cache if it uh
decremented the reference count to zero then that node in the or that structure in the cache is

now available for reuse but if there are other users of the inode in the cache then the reference
count will not yet be zero and it will uh persist for the other users so let's first take a look at
the get function which is here I get find the inode with the number inum on this particular device
and return the in-memory copy okay but it will not lock that and it will not necessarily

read it in from disk okay so in order to access the inode cache we need to lock the itable lock and
then we have a loop that's going to search the cached inodes so here we start with 0 and we go all
the way up through all 50 of them and we look at each one of them so what we're doing here is in
this picture we're acquiring this spinlock and then we're going through

all 50 of these structures and we're looking for one that matches the device and I number okay so
we're looking for one that in this loop we are looking for one whose device matches the device we're
on and whose I number matches and we're looking for something that is not free so if it's uh the
reference count is greater than zero then we've got one and we increment the reference count and we

release the lock and return a pointer to that inode okay but we might not find one so as we go
through all 50 of these structures we look for one that has a reference count of zero and if so we
save a pointer to that one in this variable empty and here we're checking to see whether we've
already found one and we only save the first one and then we exit this loop if we haven't found

one and we are recycling or allocating a new entry in the cache for this particular inode okay so we
make sure we have enough we actually found one otherwise we've run out of inodes in our cache and
that's calls for panic but if we uh find one then we are pointing to it and then we set its device
and I number to indicate that it's in use and we set its reference count also to indicate

it's in use so now we have allocated it and we said it's valid to zero now previously I said that
the valid field was protected by the sleep lock of the structure itself so this sleep lock cult lock
here protects everything below it including the valid field so is this is this cool that we're
modifying the valid without holding the sleep lock on this well we are the only process that is

accessing this inode okay because the reference count is one here we just set it to one and we are
holding the I table lock so no other process could conceivably get a pointer to this particular
cache because the reference count is protected by the lock so setting valid to zero here is safe
okay next let's look at the lock function the I lock function so it's passed a pointer to

one of these cached inode structures and it is making sure that the reference count is really one or
greater and if not it panics okay now we are going to modify the fields of it so we need to begin by
acquiring the sleep lock and this will lock it and once we've gotten the sleep lock we check the
valid bit if the valid flag indicates that we have not yet read the

data in from disk then we need to do that so here we call b read and we're reading from the device
and the call to I block which I should have bolded here is uh called to compute which block on the
disk this I number uh the inode is kept in and then we read that block and here what we're doing is
we're moving everything from the inode structure in that block

into uh the cached version pointed to by ip so we're getting a pointer to the inode in the block so
here we're accessing the data of the block and we're figuring out exactly where in that block this
particular inode is stored this is inodes per block so we're figuring out that so back in this
picture we're just basically computing where in the block we need to read from

and that's the pointer for the source and then we're copying the type major minor in link size field
as well as the address array here okay so here we're copying the address uh field from dip to the
cached copy and finally we're releasing the buffer that we use to read in the inode from disk and
setting the valid flag to true and we're also making sure that the type

that we just read in is not zero we shouldn't be reading in a I know that's not allocated okay so
unlock is pretty straightforward the I unlock function has passed a pointer to one of these cache
inodes and it just releases the sleep lock okay it does a little checking here to make sure that we
have a pointer and we're actually holding the sleep block and the reference count can't be zero we

better be referring to it and so then we release the sleep log so next let's take a look at the I
put function okay remember what the I put function is going to do it's going to decrement the
reference count and if that reference count is now zero that means no other processes are using the
cached copy and the number of hard links to this file is zero then we're going to eliminate the file
we're

going to truncate it to link zero set its type to 0 and update everything on the desk and finally
unlock the inode with a reference count of 0.

the cached structure will now be free to be reused for some other inode so let's take a look at the
I put function when a process is done with a file it's done with the cached copy the inode and we
need to allow other processes to use the memory and the cache for other inodes so let me read this
what we're going to do is drop a reference to an in-memory cached copy of an inode

if that was the last reference um then the cached space the space in the cache can be recycled and
furthermore if that was the last hard link to the file then this inode is not reachable and so we
need to free both the inode and all of its data and its block of indirection as well if it has one
so as is true of all the functions in this fs.c file this function must be inside a

transaction okay so it's passed a pointer to the cached copy of the inode and it's going to acquire
a lock on the cache because we're going to be modifying the reference count so the way this is
actually organized is a bit different than I showed in my out in my handwritten stuff we have the if
statement first followed by the decrement of the reference count and

then we release the lock on the cache uh the reason we want to decrement it last is because if it
turns out we're going to be deleting the file we want to maintain a reference count of one so here
we're looking to see whether we have a reference count of one that is we are the last process that
is accessing this cached copy of that inode and if that's the case and the number of hard lengths

is zero uh then we can go ahead and delete the file okay so we will be uh truncating it to link zero
and setting its type to zero in order to modify the type we need to be holding this inode's sleep
lock so here we acquire the sweep block and here we release the sleep lock and then we call I trunk
which will look at this inode and it will look at all the data blocks it points to either

directly or indirectly and it will eliminate all of those or I should say it will return those to
the free pool and update the bitmap and so on and uh then we set its type to zero so that makes it
an unused inode previously its type was either regular file directory or device and by setting its
type to zero we indicate that it is free but this is the in memory cached version

so here we write that to the disk so at this point we are updating the disk and now the inode that's
stored on the disk will also have a type code of zero and um we then we set the valid bit to zero so
um now here we are releasing the uh lock for the cache and then reacquiring it right here uh we need
to be holding the lock for the cash before we access the reference

count so we need to reacquire it if we release it um why do we release it well as far as I can tell
there's not a deadlock concern we're not releasing it for that reason um and I think we could it
would be legal or valid or correct to just hold on to this lock you know after all we've acquired
this lock in this lock and we're not acquiring any more locks we can release this lock here or

not but there's no problem with holding on to it as far as I can tell but it's uh if we do release
it we need to reacquire it of course but it's just a question of politeness okay we're going to be
calling eye trunk and that's going to be doing some disk writes or more precisely it's going to be
calling log right and b read which will need to allocate buffers and uh

modify the log and so on could end the update uh it's the same thing we're going to be um calling
logwrite to update the log file all of this stuff could take a little while the cache of inodes is
somewhat of a bottleneck it could be that the cache holds the inodes for some very important and
popular files and so we'd like to release this spinlock as soon as we can I think that's why it's

released here but then of course it's reacquired now then we have the question of why is valid being
set to zero here we've already set type to zero which indicates that this inode is unused but if you
look right here there's a moment of vulnerability okay we've released the spinlock and we've
released the sleep lock so at this point right here we're not holding any locks

and we haven't looked at I alec yet but what I alec is going to do is look on the disk and find an
inode whose type is zero and so it might find this very inode the item with the same number that we
just uh wrote out here and it might say okay I'm going to recycle that for some other file and then
what it will do is um call iget to see if there's already a cached version in

memory okay and so um there is a cached version in memory so we need to make sure that it doesn't
get used so that is why we need to set valid to zero right here okay the reference count is still
one so when we look for it in the cache in the cache I alec will find it or it might find it and try
to reuse the cached version well the cached version um is not correct

I alec will have modified the type on the disk and it needs to read that back in so the type in
memory is also adjusted and we can't do that here because we don't know what the type is so um I
think that's why we are setting valid to false right here so I hope that explains uh I put the other
function we have down here is called I unlock put and all it does is

unlock an inode and call put so it's very common that after we've modified or used a file we are
ready to both un unlock its inode and uh get rid of our reference to it we're all done with it so
this is a common idiom next let's take a look at the ialoc function this is called whenever we want
to create a file we pass it the device it indicates where we want to create the file and the

type which will either be a regular file a directory file or a device type and what this will do is
find an unused inode on the device and mark it as allocated by setting its type field to whatever we
were passed here and then it will call I get here to return a cached copy of that inode okay so
let's go through the code here we are going to uh loop through all

possible I numbers we skip 0 because 0 is not a valid I number and so we go from 1 all the way up to
our maximum number in our example we have 199 possible inodes so we loop from 1 to 199 and for each
one of those we need to read it from disk okay so we'll call b read I block is past the I number and
it will determine which block this inode is stored on so the first one number one will be stored

on this block which happens to be block 32 and so we'll use block 32 for several and then we'll go
to block 3 3 and so on so then we compute once the b read returns it has a pointer to the buffer
that we've read in and we then figure out where in that buffer we can find this particular inode so
here's the pointer to the data of the buffer and we take the I number

modulo the inodes per block and that tells where in the buffer we could find the on this data for
this inode and then we look at its type field and if that's zero okay then we found one that's free
okay if it's not 0 then we release this buffer and loop back to the next I number and call b read
again now most likely when we call b read the next time uh the block that we need is just sitting in

the buffer cache okay we just read it previously for the the inode before this and this new inode is
probably on the same block so we don't actually do a disc read for most of them so we just loop
through the inodes in one block ultimate eventually we hope to find one that has a type of zero if
we don't and we get all the way up to 199 then we'll exit this loop and panic

that we have already allocated as many files as we possibly can I think earlier I said we have a
maximum of 200 files but since we don't use zero we have a maximum of 199 files um okay so let's say
we find an inode with type equal to zero then what we're going to do here is zero out all the fields
in the inode on disk so we copied zeros into this buffer and here we're doing that this is the

size of this is the size of the inode on disk and then we set the type field to whatever argument we
were passed and we write that buffer back to disk and release it and now that we've got the inode
updated on disk we call I get and it I get will look through the cache and find an empty slot for
this inode and then it will fill in the device and I number as well as setting the

reference count to 1 and it will return a pointer to this um in memory cached version of the inode
the valid flag will still be zero because we set it to zero earlier and um so unused entries in the
cache we'll have a valid flag being equal to zero and in uh that when we call lock when we call
ilock we will go ahead and read the data in from disk and copy it into this area

I alec is only called from the create system call and that system call will immediately call ilock
so it will read the data from the disk okay and it will copy it into the cached structure and since
the block is probably still sitting in the buffer it won't involve a second read of the disk
whenever we update the inode associated with any file we need to write those

changes back to the disk and that's the function of the I update function so it's passed a pointer
to some cached inode and it will write it to disk so copy a modified in-memory cache version of an
inode back to the disk it must be called after every change to a field in the inode that is stored
on the desk and the caller of this function had better be holding the sleep lock associated

with this inode so we saw that this was called in the I put routine so here's the I put routine
remember we uh if we're going to delete the file we set its type to zero and then immediately write
that to disk so here's an example where we call I update this is the code is pretty straightforward
first we invoke iblock giving it the I number of this particular inode and it

computes which block contains that inode and then we read that block from disk into a buffer okay
then we compute where in that buffer we can find this particular inode so we take the I number
modulo the inodes per block and then add that as an offset to where the data is for that block and
uh that gives us a pointer to into the buffer and so then we set the type the

major the minor the end link and the size right we set the type major minor in link and size and
then we set the uh address array so we copy it from the cached copy in the itable cache okay so
we're copying it from this area here into the buffer and then we'll write the buffer back to disk so
that's what we're doing with these copies and here we're copying the

address array the size of this array is uh 13 that is the number of direct pointers plus one more
and then we write that buffer back to memory and release the buffer from time to time we need to
truncate a file to link zero that is we need to get rid of all of its data one place we do this is
in the I put function so here's the I put function and if we've determined that the number of hard

links to this file is 0 we're going to go ahead and delete the file so we call I trunk or I truncate
here and then we go ahead and set the type to zero and so on another place that I trunk is called is
in the open system call code we have the option there of truncating the file to length 0.

so let's take a look at what this file does the caller must hold the sleep lock on the cached
version of the inode so it's passed a pointer to the inode it's cached and it's going to go through
that inode and look at all the data blocks it points to so here's the picture it's passed a pointer
to one of the cached copies and it's going to go through this array here and let me use

this array up here because it has the blocks so we're going to go through the direct pointers and if
they are non-zero we're going to call b3 to return this block to the free pool and then we're going
to go through the block of indirect pointers so that's what's happening here we go through the
direct pointers all in our case we have 12 of them and so it's from 0 to 11 and we look at each
entry

in the array in this inode and if it is non-zero then it has a pointer and so we need to call b3
using that block number and what this will do is update the bitmap to change the bit for this block
from one meaning in use to zero meaning free and then we zero out the element in the array and so
after we've done all the direct pointers we need to ask whether there is

a block of indirect pointers so we ask whether this pointer here is zero or not and then if it is
not zero we need to read this block and then go through all of these pointers for each one of these
and then free the block itself so that's what's happening here we ask uh is the last element okay
that's the 13th element or we have 12 direct pointers so element number 12 which is the 13th

element of the array if it is non-zero then we have an indirect block so here we call b read to read
that indirect block into some buffer and here we find where in that buffer uh the data is actually
stored okay and that we will consider to be an array okay it's considered to be this array here and
we're going to index through it so in this loop here we are

indexing from 0 up to well in our case we have we go from 0 to 255 and we look at each element in
the array and ask if it's non-zero or not and then if there is a pointer there then we call b3
providing that block number and that will free the block so we go through this array one by one and
free these blocks and then finally we release the buffer that contains the

indirect block okay that's this buffer and then we need to free that block itself so again here is
the address or the block number of the indirect block and we just call b free with that block number
to free the indirect block and we set the final element of the array to zero then we set the size in
the inode to zero and we update the cache version of the inode by copying it back

to disk okay we've also got this function here stat I uh which is simply going to copy some
information from a cached inode to someplace else okay so we've got a stat structure and we assume
that we are holding the lock on the cached inode because we are reading the fields in that cached
inode and we're just copying them uh into this stat structure okay we've just covered about half of

the functions in the file fs.c and the remaining functions will be covered in the next video so I'll
see you there
