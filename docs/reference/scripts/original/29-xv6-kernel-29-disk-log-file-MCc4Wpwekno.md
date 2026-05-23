# xv6 Kernel-29: Disk Log File

| Field | Value |
| --- | --- |
| Video ID | `MCc4Wpwekno` |
| URL | <https://www.youtube.com/watch?v=MCc4Wpwekno> |
| Source | `docs/reference/scripts/original-raw/29-xv6_Kernel-29_-_Disk_Log_File-MCc4Wpwekno.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

Welcome to another video in a video playlist describing the xv6 operating system kernel in this
video I'm going to describe the disk log file system and I'll be covering the code in the file dot c
this code is pretty complicated so I'm going to start with a motivating example the problem is that
we may have complex data structures and crashes can occur and updates require several different

operations in order to perform the entire update consistently so for example here I have a singly
linked list okay it's linked like this and I'd like to swap the order of these two nodes and so I
need to update one two three pointers in order to do this particular update so I need three
different rights to make the change and if a crash occurs in the middle of

this operation then the data structure can be left in what we call an inconsistent state in other
words the linked list is all messed up and while this example is probably in memory and the whole
thing just goes away at the time of the crash what we're really getting at here is a problem with
the file system in that case the data structure is kept on disk and that data structure

is used to represent files and directories as well as inodes and free maps and so on in my example
each update was a modification of a single pointer but with our file system each right will be a
right of a single of an entire block to the disk and a particular update may involve several blocks
in other words it may require several blocks to be written to the disk

and a crash of the system either a hardware crash or a software crash must not be allowed to mess up
the file system we need to keep the file system in a consistent state so in this particular example
if we are going to swap these two elements we either need to do none of the changes in which case
the update hasn't occurred or we need to do all of the changes in which case the

update has occurred let's look at a more realistic example from the file system let's assume we want
to increase the size of some file we will have to allocate and remove a block from some pool of free
blocks and then we'll have to add that block to the file that we are growing and each one of these
will involve an update to the data structure so we assume that we have to have at least two

disk rights now assume that the computer crashes and that one of these rights has completed but the
other one has not completed so we might have a data structure that is corrupted in an inconsistent
state so how are we going to guard against this well we might try removing from the free pool first
before we add the block to the file but if the crash occurs after the first

write then we've got one block that has been removed from the pre-pool but not added to any file so
essentially we've lost a block it's become unaccounted for so that's not going to work so maybe we
can reverse the order and add the block to the file first that is perform the write that will update
the file data structure and then update the free pool data structure to indicate that we've

allocated one of the blocks but then if the crash occurs after the first write then we've got a
different problem we've now got a block that's still in the free pool yet has already been added to
a file and that's a disaster as well one block is both in a file and in the free pool so neither of
these two approaches works this problem is more general you know imagine a

bank computer a system that's uh involved with bank accounts and money and imagine that the
operation of the transaction is to remove say a million dollars from one account and put it into a
second account and this operation is going to involve two different updates one removes the million
dollars from one account and the other update uh adds the million dollars to the second

account well you can't tolerate a crash I mean if you do them in one order you may have lost a
million dollars and if you do them in the other order you may have fabricated a million dollars out
of thin air and neither of those solutions is is acceptable so this problem of transactions and is a
general problem and we're going to see how it's handled here in the xv6

operating system with an operation involving two writes our goal is that either both rights are
completed and the disk is updated or if a crash happens neither of the rights is actually performed
so it's either all or nothing we don't have a situation where one right is done to the disk but the
other right is not done more generally we can talk about transactions and we will

group several updates and in this case we're talking about disk rights and we will group several
disk rights into a single transaction and either the transaction occurs and the disk is updated or
the transaction does not occur before the crash and none of the rights occur all or nothing what
this looks like in xv6 involves these functions begin op b read log write and end up

and the begin op function will be called first and that will start what we call a transaction and
then the end up will end the transaction and between these two we will have a number of rights we
will also have some reads as well but it's the rights that we're sort of focusing on and when the
end op function is called then the transaction is complete and it signals to the system

where the transaction is and it will either all happen or not happen at all now let's take a look at
this example I've got two transactions here's one and here's the other and they're being executed in
two separate threads concurrently so each begins with a begin operation and ends with an end
operation and there are a number of reads and writes in the transaction

and so I'm showing in this transaction we are reading block number 103 and writing it back to the
disk and then here we're reading block 108 and writing 108 and in this transaction we're reading
block 103 but we're not writing it and then we're reading 106 and writing 106. so whatever we write
into block 106 could be dependent on 103 and I'm showing by the positioning here

that this read occurs after this write so here with this read we must be getting the version of the
block that was written by this write operation here however we can't actually write this block to
the disk until after the end operation so we have to keep it in a buffer and delay the actual write
to the disk while at the same time delivering to this read operation the data that was

written here so we have to get the new version and the other thing is when this transaction ends we
can't just simply write block 106 to the disk because it's dependent on 103 as well so we can't
actually perform the disk update until we're ready to uh perform this end operation we've got to
wait we can't write block 106 unless we're we are also writing blocks 103 and 108. so in a

sense these things are all linked together and have to be either committed together or not committed
so what we're going to do for the right operation is keep the block in the buffer cache in other
words we're not going to write it to the disk immediately we're just going to keep it in memory and
that solves this problem here for the read we will use the version of

the block that is in the buffer cache if there is a block sitting there and otherwise we will read
the data from the disk okay at the end operation we will not perform the rights until all of the
transactions are ended okay here we have two separate transactions and at this one we're not going
to do anything really it's only at this point here where we will be doing the rights

okay we also have a requirement that we want to avoid the exhaustion of the free buffers and log
records okay so we can't start operations can't start transactions if we don't have enough memory or
resources to complete the transaction we also don't want to delay writing forever that would be
starvation if we for example have a sequence of transactions that are trying to be

started we have a bunch of transactions and there's always one in progress it might be the situation
where we never have a situation where we can actually do the commit okay so we can't allow that so
we will have to delay some begin operations if we have inadequate resources or starvation is a
possibility and finally we have this idea of a commit and at the time of the commit

that is when we perform all of the right operations that we've accumulated so far so here at this
end we won't actually do the commit okay this transaction is over but it's not yet committed it's
only at this point here when all transactions are completed that we can perform the commit operation
here I'm showing in schematic form the disk each one of these little squares

represents a block on the disk and the disk is nothing more than a sequential set of blocks starting
with block 0 and going up to some maximum we'll be talking about the log which consists of a header
and some log blocks and we won't be too concerned with the first two blocks the first block is the
boot record or the master boot record and the second block is the superblock

the superblock contains a number of fixed parameters it's read-only and at startup time the kernel
will read in the superblock and this superblock contains parameters such as where the log is located
how big the log is where the main block area begins and how many blocks there are on the disk by the
way xv6 talks about a disk but file systems can be stored on not

only rotating disks but solid-state storage devices as well and the organization that doesn't really
uh matter I mean it's it's similar for solid state as it is for disks the solid state uh device is
broken up into a number of blocks and so on but we'll just stay focused uh on the disk here the log
consists of a header and a small uh fixed sized area which contains a number

of log blocks in our case it happens to be 30.

and that's followed by the main block area the main block area is where the file system is stored
and the file system contains all kinds of stuff the directories the files the inodes the log system
is not going to be concerned at all with the organization of the file system so from our point of
view in this video the main block area is just a large collection of blocks

we don't really know how it's organized the header block is uh read in at startup time into memory
and stored in a structure so here's the structure that's kept in memory and from time to time this
structure will be written back to the header block on disk the header contains a counter n which
tells how many of these log blocks are in use and for every one of these

log blocks there's an entry in an array so this number n tells how many of the array elements are in
use so for example at any one point in time we might have say three log blocks in use and the
remaining are yet to be used so in that case n would be three and the first three elements here
would contain information about which block is stored in these positions on the disk

I'm also showing here the buffer cache remember that the buffer cache is a circularly linked list a
doubly linked list with a header buffer and a number of buffers I'm also showing uh individual
blocks in the main block area these are from the previous example I'm going to try to work through
it and show how this thing gets updated but blocks 103 106 and 108 contain some data

initially here is the example that we're going to be walking through every transaction begins with a
begin operation and ends with end and can contain reads and writes so let's take a look at what all
these operations do read write begin and end the read operation is really just b read which we
discussed in the previous video what we'll do is we'll search the buffer

cache to see whether the block that we want is in memory and if so we'll just use that otherwise
we'll allocate a buffer from the buffer cache and read in the data from the disk the write operation
is something new and it's implemented with a function called log right the first thing it will do is
it will pin the buffer that we're trying to write remember that we only do a write

after we've read a particular block so we have a buffer and we want to write the buffer out so we
won't actually write it at this time instead we'll just do something we call pinning it which
basically sets a flag to indicate that that buffer should not be freed and then we will find the
next available slot in the log okay if there are three elements in the log then n will be three

and we increment to 4 and we are able to use the next entry in the array so we add an entry to the
array and increment the counter but first it's possible that the block that we're writing has
already been written and therefore is already located in the log so we search the log and if we find
it we just reuse that log slot and we do not increment n every transaction must begin with this

operation and it's actually done in the function called begin op if there is another thread in the
middle of a commit operation then the begin operation will just wait okay we'll go to sleep and wait
until the commit is done and then it will proceed it will also check how many slots are available on
the log if we don't have enough resources to continue this to

complete the biggest possible transaction then there could be a problem so the begin operation will
just sleep and once other things have finished it will be reawakened finally when everything's okay
it will increment a counter and that counter just tells how many transactions are currently in
progress the end operation function end up begins by decrementing

this counter if it goes to zero that means this is the last transaction and we can go ahead and do
the commit but if there are other transactions currently in progress then the counter will not go to
zero so this end will just return immediately without doing anything further the commit operation
will then perform all the writes to the disk it will actually do writing of the blocks to the

disk in the write operation there is no actual write to the disk okay we just save everything in
memory and then we will empty the log and finally since this transaction is committed and we've
released the log blocks we can then wake up any sleeping operations that are stuck on begin I want
to mention one thing by uh saving the rights until the uh commit operation

we are bunching them up so it's very typical for a program to write sequential bytes to a particular
disk block and we don't want to do a write to the disk for each time a program writes a single byte
instead we just use the same buffer in memory and they get saved up and then at the time of the
commit we do a write that writes the entire block okay now I'm going to walk through this

example here and I'm going to execute it by updating the disk on this piece of paper here to make it
more interesting I'm going to change this from block 108 to block 106.

and we begin with the read of block 103 well we start by searching the beak the buffer cache for
that block we find out it's not there so we perform a disk read and allocate a buffer and read block
103 into that buffer so I'll put an x right there next we write that same block so we will allocate
an element in the log I'll change this n to 1 and this first element will set to 103.

this particular block will go into the log right here but we don't write it just yet the next thing
is a read of block 106 so again the b read function will search the buffer cache find it's not there
grab a free buffer and read in block 106 to this point okay we'll put it right there I forgot to
mention that with this right here we'll also pin this buffer so I'll

indicate that a buffer is pinned by putting a little check mark underneath it okay now that next
transaction uh begins by reading 103 and that call to be read we'll search the buffer cache find
that we already have it so a disk read will not be necessary it'll return a pointer to this buffer
right here and the next thing we do is read block 106 and again we have it in the buffer

cache so we return a pointer to this buffer here and then we do a write of 106.

at that point uh we will search to see whether 106 is already in the log we find out it's not so we
allocate another element in the log and so we increment in to 2 and we write 106 here okay and we
also pin that buffer and finally we end well I haven't shown the incrementing or the decrementing of
the counters but this is not the last transaction so we really don't do anything

and then the other thread continues and it tries to write block 106.

so again what it will do is it will search the log and this time it finds that it is there so it
doesn't really have to do anything in is not incremented and this this thing is already pinned so it
really doesn't do anything okay and finally we come to the end operation here and at that point we
find it's the last transaction and now we're ready to commit so next let's take a look at what

happens in the commit the commit has two phases in the first it's going to move the updated versions
of the blocks into the log area on the disk and then in the second it's going to write to the main
block area of the disk so the commit operation will begin by going through the array in the log
header and for each block okay in our case there are only two blocks block 103 and

106 we will copy that block from the cache to the log area on the disk okay and then at that point
after they've all been copied we will write the header to the disk so I can indicate the uh first
we're going through this loop here we've got two elements in it and we're going to write the new
version of this block and that block maybe I should put a prime here to

indicate that it's been updated so we write x prime here and we write y prime there okay and then
finally we write the header back to this block on disk so at that point we are committing okay if a
crash occurs before this right of the header to the disk then it will be as if none of those
transactions ever happened when we boot back up and do the recovery on the other

hand if the crash happens after this right then these transactions will be carried out and they will
succeed okay so now after the header has been updated on the disk we're going to go through the
array and we're going to write the block to the main block area so we're going to go through the
header array here again there are two elements and we're going to write x

prime and y prime back to the disk so at that point we actually write it to the main area here so
I'm going to update it by indicating a prime here and a prime there so now the main block area has
the updated versions of both x and y and then we update the counter to zero and write the header to
disk so I suppose I could show that here as well we change that to zero

so these are effectively uh erased or not important anymore and then we write that block back here
the data is still here in the log but of course it's no longer needed okay what happens if we have a
crash and then we reboot well the system begins by reading the log header it doesn't know whether
the previous session uh ended with a crash or not so regardless of whether there was a

crash in the past or not whenever we boot the system we begin by reading in the log header if the
counter is zero then we do nothing any in progress transaction if there was one that was lost
because of a crash will just be ignored so that transaction or that commit will not happen and all
the transactions that are involved in it will be essentially lost on the other hand if n is non-zero
then

we have managed to do this we have managed to write a header to the disk at this point so we need to
replay the log and make sure that everything got written to the main block area and that's what we
do here we go through the array and for each element in it we write from the log area back to the
main block area so uh I've already erased let's go back here and say that we have a crash

okay so the count is still 2 and we still have 103 and 106.

in this header so we read in the header we find out that it is not zero so at that point we need to
replay the log so we'll go through and for each one of these we'll read in this block here and write
it to the appropriate place in the main block area and then once we've done that we can set into
zero and write the header back out to disk with the zero the commit operation is done in several

different functions I'll just mention them commit write log there's a right head and then there's an
install trans or install transaction the stuff that happens on the reboot well um we initially uh
execute this init log function that will initialize the variables of the log and that's called from
fsnet file system inet that's called in the init process as one

of the first things it does the first time it has a time slice and it will invoke recover from log
install trends and it also calls read head and right head okay I think we're ready to take a look at
the code next the file log.c begins with a comment and then it gets into the data definitions this
structure called log header is what I showed right here within plus the

array so here we see a field n and we see the array log size is a fixed constant parameter it tells
how big to make the log all of the variables associated with the logging are contained in this
structure called log and there's exactly one instance of this structure also called log and we see a
number of different things we see a spinlock that protects these

two fields outstanding and committing and then we see start and size which tell where on the disk
the log will occur okay these are constants and they don't change and we see a field called device
uh typically we'll have several devices and each will have a separate log in xv6 we only have one
device so yet we still have this field here the outstanding field is a counter every time we do a

begin operation that counter is incremented and every time we do an end operation that counters
decremented and if it's zero we do a commit if we are in the middle of committing the committing
flag will be set to true and it will cause other begin operations to wait and finally we have an
instance of the log header structure itself right here okay now let's take a look at the init

log function this is called on kernel startup in particular it's called from this function fs init
which itself is called from uh forecret the first time that fork rep returns we're going to be
accessing the disk possibly if we do some recovery and that might involve sleeping so we need to be
running in a user process so that's why we are doing this from within you poor grant

the first thing we do is initialize the spinlock called block and then we initialize the fixed
fields the start size and device fields we're past the device number of which we are initializing as
well as a pointer to the super block previously the super block has been read in and here we just
pick up values from the super block to initialize the start and size fields and we initialize the
device

field and then finally we call recover from log this will read the header block in and if n is
non-zero it will do the recovery stuff okay let's take a look at the begin operation function this
is called at the beginning of every transaction this says called at the start of every file system
system call well every file system system call is a single transaction so that's why that says that

essentially what it does is it increments this outstanding counter to indicate that yet another
transaction is in progress this counter is protected by a spinlock and here we acquire that spinlock
and after we do the increment we release the spinlock and we return we break out of this loop and
then return but before we can increment the counter we need to perform a couple of checks

first we check to see whether some other thread is in the middle of committing and if so we go to
sleep the first argument to the sleep function is the channel and we use as a channel the address of
the log structure itself and of course the second argument is the spinlock that will be temporarily
released while we are asleep and reacquired once we are reawakened

so if nobody's committing then we make a check to make sure that there is enough space in the log
for the blocks that will be involved in this transaction so we look at the current value of n and we
have this parameter max op blocks which happens to be 10 and that's the maximum number of blocks
that any transaction is allowed to update so we look at how many

transactions are currently in progress plus this current transaction that we're trying to start and
we figure that they might all use the maximum number and we're asking whether that would exceed the
size of the log and if so again we go to sleep and we use the address of the log structure as the
channel and after somebody commits after some other thread commits will get reawakened

and if both those are okay in other words if both those tests are false then we can go ahead and
increment the outstanding counter and return after we wake up we will basically need to check and
make sure that both these conditions are true just because we've been reawakened we don't know
exactly what's true and what's not true so once we wake up from either of these two

sleeps this while loop will repeat and we won't be incrementing the outstanding unless we pass this
test and this test while holding the lock and if we pass both of these then we can go ahead and
increment the outstanding counter and release the lock and return now let's take a look at this
function log right which is passed a pointer to a buffer in the comment here we have an example

we would first call b read with a block number to read in the data from the disk and that gives us a
pointer to a buffer then we modify the data in that buffer and after that we call this function log
right to write it out to the disk it doesn't actually do the write but it schedules it we might do
some more modification and call log write again but ultimately

we need to release this buffer and we call b release so the caller has modified the data in the
buffer and is done we record the block number in the log we also pin this buffer we do that by
increasing the reference count for this buffer we saw that in the previous video and then the commit
operation later will actually perform the right to the disk so here's the code we're going to be

modifying the log so we need to acquire the spinlock and we acquire that here and then before we
return we release the spinlock we do a couple of checks first we make sure that we have enough room
in the log we've already checked that with the begin operation but we do another check right here to
make sure that we have enough room in the log and we also make sure that we are in the

middle of some transaction the begin operation will have incremented this counter and we make sure
that uh it's not zero so if everything's okay then we scan through the array looking to see whether
we've got an entry in the array that happens to match this particular block number so from 0 to n
minus 1 we ask is the array does the array contain an entry that has the block that we are

currently looking at and if so we set I to that otherwise we set I to n that is we exit when I
becomes equal to n that's the next available entry then we update the array okay if I comes out if
we didn't find it then I is equal to n so we uh update the next available entry by storing the block
number if we already found it then we just overwrite it so that doesn't

really do anything if I is equal to n then that means we did not find it and we're adding a new
entry at the element at the end of the array so we need to increment in and we also invoke buffer
pin on this particular buffer to make sure that we will not reuse this buffer for some other block
until after it's been written to the disk and then again we release the spinlock

and return here's the function end operation it's called at the end of each file system system call
that is it's called at the end of every transaction and if this is the last transaction and there
are no other outstanding transactions then it will perform the commit operation so we have this
counter outstanding which tells how many transactions are currently in progress and we are

decrementing it right here this counter and the committing flag are both protected by the lock so we
acquire the spinlock here and then we will release it down here if the outstanding counter goes to
zero after we decrement it then we need to go ahead and do the commit okay but we better not already
be doing a commit because this transaction was in progress up until this point so we do a

little error check here to make sure we aren't committing already but if the outstanding counter is
decremented to zero then we set this do commit flag right that's a local variable here do commit and
we check it down here and if it's true we'll execute this code and we also set the committing flag
so that we know so that every other threads will know that we are committing

however if we did not go to zero then it means there are other transactions still running and there
may also be transactions that are waiting for uh enough log space to begin so there could be threads
that are in the begin operation that are sleeping so we do a wake-up here to wake up any sleeping
begin operations we check to make sure that there's enough reserved log space based on how

many transactions are in action and since we are now finished with this transaction it means that we
no longer have to reserve uh additional log space and it may be that uh there's enough log space for
one of these transactions to begin so that's why we're calling wake up here and then we release the
lock now if we need to do a commit then we are going to call this commit

function which I'll talk about in just one second and then we're going to clear the committing flag
after it completes we have to reacquire the lock and then release it in order to modify this flag
and again we also call the wake up function to wake up any begin operations that happen to be
sleeping okay here is the commit function it's called from exactly one place

and it first checks the counter in now presumably there were some rights in this transaction and it
will be greater than zero but if it happens to be the case that there were no calls to the right log
the log write function then now this would be 0 and we don't need to do anything so here we're going
to call the right log function okay previously I talked about the log

write function it's a little bit confusing but the right log function which is right here is only
called from this one place that will write all the blocks from the buffer cache to the log area on
the disk and then we call this function right head which will copy that header structure to the disk
block where it's in the log file on the disk and that performs the commit

after this completes then this transaction or all of these transactions are committed and even if we
crash directly after that the log will be replayed and we will do the rights to the disk if we crash
before this right head completes then it will be as if no transactions have occurred then we call
install transaction and that will run through the log and it will write each

block to the actual location in the main block area of the disk and then we set in to zero in the in
memory copy of the log header structure and then write that log header structure back to the disk
and that essentially sits in on the disk back to zero and at that point the commit is done but now
let's look at this write log function and as I said it's called only

from here in commit what it's going to do is go through the array in the header okay so we're going
to go through uh this array here from 0 on up to n minus 1 and for each one of those we are going to
uh write it to the log area of the disk okay so we're going to write from the buffer cache okay so
for example 103 is the first one we look at we're going we've got that it's pinned

okay so it will be in the cache and we're going to write it to this location here and that's what's
going on here so the first call uh well let's look at this call from that's the from location and we
are looking at the log okay here's the array and tail is our index and we will find it in the cache
because it was pinned okay and so this just gives us a pointer to

the buffer okay it doesn't actually read from the disk this read function right here is needed uh
because we are going to do a write every call to b write needs to be preceded by a b read to get the
buffer so here we are asking uh using the index tail here for the block number in the log area so
that will give us the block number in this case 0 1 2 3 for this particular

block and we will read it in although that's not really uh I suppose could be optimized in some
other kernel and then we're going to be writing it uh here so before we write it we move all of the
data from the buffer that came out of the cache okay to this new buffer that we just got okay and so
we move an entire block into the two buffer and then we write it to the

log area and then we release both buffers both the cache buffer and the cache and we created a new
buffer in the cache for this particular writing operation and we release that as well so to review
the commit function we are going to write the log that is we are going to take every block that is
indicated by the array in this case we have two of them and then we're going to copy that

data two blocks on the disk in the log area and then in the second step we are going to write the
header so we copy this data structure here to this block here then we do the install transaction
function which will go ahead and perform the rights to the main block area then we set into 0 and
write the header a second time so let's take a look at the right head

function here it is and essentially what it's going to do is write the in memory log header to the
disk the comment says this is the true point at which the current transaction commits well that
applies to the first use of right head here but I don't think it really applies to the second use
but in any case what it's going to do is get a buffer for the appropriate block on the disk okay

log.start that's this block number here it would be 0 1 2 it would be 2 and once we have a buffer
pointed to by buff we're going to copy the n and copy the array into that buffer and then we're
going to write it and release the buffer okay so more precisely we're calling b read to read in this
disk block into a buffer so it's going to actually read this into some

buffer in the buffer cache we don't really need to perform that read and perhaps some other kernels
could optimize that away but in any case then we get a pointer into the data of that buffer okay
into the block part of that buffer and we treat that as a pointer to one of these log header
structures and then we're going to copy first we copy in from the in memory log header to this area
in the

buffer and then we go through the array up to n minus one and copy from the log header structure
here to the uh buffer that we have just allocated and then we write the buffer and release the
buffer now let's take a look at this install transaction function it says copy committed blocks from
the log to their home location so what it's going to do it's going to go through the

array the header right here and for each block in the log like this one right here it will copy it
to its home location in the main block area and then it will do the next block and so on this
function is called in two places it's called when the commit happens and it's called at startup okay
so if we're calling it during the commit operation from the commit function recovering will

be false if we're calling it on startup recovering will be true okay so here's what it's going to do
we assume we've already read in the log header structure that it's in memory at this point and we go
through the array from zero to n minus one with the index tail and we're going to read a block from
the log okay so here we're using the start of the log plus

the index plus one for the header block okay so that's where we're computing the block number here
so the first one would be this block number here and we are reading it in from the disk into a
buffer and then we are going to be writing to a location on disk so we need to have a b read for
that and that's what the second b read does it is uh reading from the disk and where

is it reading from well we go to the log header and we use tail as an index into the array okay so
we use a tail into an as an index we find out the block number 103 in this case and so we're going
to get a buffer for this particular block and then we will move a block of data that in our case
it's 1024 bytes from the log block okay l buff to the buffer that's going to be written out the

debuff okay and then we write the debuff and uh if we are called from commit then we know that this
particular buffer okay has been pinned so we need to unpin it okay if we are recovering after a
crash then it won't have been pinned so we do not call the unpin function but in either case we
release both these two buffers now I should add that when we perform this first read here

we are reading from this here that uh may actually still be in the buffer cache okay it's not this
one right here because this one points to this this block number okay this buffer points to this
block number but in order to write this we had another buffer here in the buffer cache for this
particular block so it may be that this b read here doesn't actually do a disc

read here in this case we have to call b read before we can call b write and so this will read in an
entire block okay so when we are preparing to write to this block here we first call b read for 103
and that's what's what's happening here now let's take a look at what happens on startup the
function init log is called as part of the very first time slice and it runs

as part of a user mode process it begins by initializing the spinlock that protects our log
variables and initializing several constants that won't change after this and then it calls this
function recover from log so what's recover from log going to do it's going to begin by reading the
log header from disk and moving it to the in memory copy and then it's going to check in

if it's 0 then it indicates there's nothing in the log area and we can proceed immediately but if it
is greater than 0 as in this example it's 2 it indicates that the log area contains a transaction
that hasn't been completely finished so it will go through the array and for each element it will
read a block from the log area and write it to the appropriate location in the main

block area and so it will do that for each of the blocks and update the main block area and finally
it will set into 0 and then write the log header back to disk thereby essentially clearing it now it
may be that the crash occurred in the middle of committing a transaction and some of these blocks
have already been written to the main block area but this recover from log will repeat that work

it won't matter that we read this block and write it a second time to the main block area so let's
take a look at recover from log and here it is it begins by calling read head and then it calls
install trans with the recovering argument set to 1 to indicate that we're being called as part of
the startup and not as part of a commit then it sets into 0 and writes the log header

structure back to disk let's take a look at read head that's right here and it's going to read the
log header from disk into the in memory log header structure and so it has a read here and it's
providing the address or the block number I should say of the log header that's the start of the log
which is uh in our example it's two so it reads from this block

into some buffer and that is a pointer to the buffer and then um we get a pointer to the data area
in that buffer lh's pointer with c data is an array so this expression here is just equivalent to
the address of the first element of the array and so that's where we're going to be copying from and
we're copying into the log header the in-memory copy of the log

header so we first copy in from the buffer lh into the log header and then we go through the array
and copy each of the array elements from the buffer to the log header that's in memory if n is zero
we don't do any work so we only copy as many as is necessary and finally we release this buffer now
we could consider just using a memory move function call to do this copying and in

that case we would be copying from the buffer to the in-memory log header area and we're copying the
entire structure that is we would copy the entire array but normally the previous session will not
have ended with a crash so in will normally be zero so we really only need to copy in in most cases
and can skip copying the array okay so now let's take a look at

in recover from log we read the header and then we call install trans so we can take a look at that
again and we have this recovering argument that will be set to true if we are being called on
startup and again what it does is it reads from the log area on desk and it writes to okay here's
the right debuff it writes to whatever address the array tells us to write 2. so here

we're going into the log header and into the array this is our tail as our index from 0 to n minus 1
in this array and we are looking at the array to find the address okay so first we would find 103
and that tells us we want to write to 103.

so we get a buffer for block number 103 and then we write to that block uh first we move the entire
data block 1024 bytes from the buffer that we just read in l buff okay to the destination buffer and
then we write now the recovering here will be true on startup so we will not need to unpin the
buffer recall that these buffers in the buffer cache when we first read in the old value of

block 103 into the buffer we pinned it okay and so if we are in the middle of a commit we need to do
the unpin but on recovery these blocks in the buffer are not pinned so we don't do the unpin and
then finally we release both of these buffers so that they could be reused in some way now so I
think we've looked at everything but I wanted to make one comment

this knit log function calls recover from log recover from log is only called from that one place
and it consists of four lines it's pretty short and it calls read head read head is only called from
this particular location and again it is quite short so we could consider doing some inlining we
could take all of this code here and move it into the recover from log

function and or we could take the recover from log function and move it directly into the init log
function recover from log is pretty short and it's only four lines so I would prefer to in my if I
were writing this code I would do the in lighting I think that uh I might also consider inlining
read head as well the resulting function init log would then be still well less than a page

it's an aesthetic question and it kind of depends on how you feel about things you could make the
argument that recover from log is a separate enough function separate enough operation it's a
discrete and different uh operation so it makes sense to pull it out and put it in its own function
and document it separately the argument that I would make is that every time you have a separate
function

introduced it requires a little extra effort a little extra mental effort not much but a little bit
if you were to approach this code without having seen it before and are trying to read it and you
encounter some function like this um you have to say well where is this thing called from and oh it
calls this function here where is that so you have a little bit of extra mental

effort not much but a little bit you've got to go find where read head is so you got to shuffle
through some pages or whatever and locate it but more importantly when you're looking at a function
you have to ask where is it called from so if I see recover from log is it called where is it called
from so that requires a little bit of a search okay and then you might find out that

it's only called from one place read head it's not clear at all that it's a call from only one place
so you might uh spend some time trying to search for where else it is being used these are minor
things and again it's an aesthetic question so um the code in log.c is fairly complex and what it
does is fairly complex but I think we've gone through all of this

file and so I can say thanks for watching I'll see you in the next video
