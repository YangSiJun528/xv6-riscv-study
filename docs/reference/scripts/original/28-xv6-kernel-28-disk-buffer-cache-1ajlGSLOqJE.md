# xv6 Kernel-28: Disk Buffer Cache

| Field | Value |
| --- | --- |
| Video ID | `1ajlGSLOqJE` |
| URL | <https://www.youtube.com/watch?v=1ajlGSLOqJE> |
| Source | `docs/reference/scripts/original-raw/28-xv6_Kernel-28_-_Disk_Buffer_Cache-1ajlGSLOqJE.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

In this and the next couple of videos I'll be describing how the unix file system is implemented and
I'll talk about the system calls like open close read and write this video is part of a playlist on
the xv6 operating system kernel x36 is a simplified version of unix there's a lot of complexity here
and in a complex software system we can start by describing the low level functions

first and then gradually work our way up to the higher level functions in another approach we can
start with the high level functions and describe those first followed by successive levels of
greater detail I will go with the first approach and talk about the low level functions first in
this video I'll be talking about the disk system and how we interface to the

disk and I'll talk about the buffer caching system I'll cover the code in files buff.h and buffer io
or bio dot c so let's begin by talking about the disk whenever we transfer data to or from main
memory it's in units of bytes or words with a disc we're transferring units in terms of a larger
thing called a block the block is a fixed size chunk of bytes in the case of xv6 our block size

is 1024 and that's fixed by this constant b size other systems like units use a different block size
but in xv6 or in any any operating system the block size is always fixed now the disk is viewed as
nothing more than a numbered sequence of blocks so the blocks are numbered from 0 1 2 on up to some
maximum the second block that is the block number 1 is special and it contains something

called the super block the super block is fixed and doesn't change and it contains a number of
parameters one of the parameters contained in the super block is the size or the number of blocks on
the disk and so we can read that and know how many blocks are being available on this disk for the
file system to be stored in the disk actually may read or write bytes in another unit called the
sector

so block and sector are sometimes used synonymously but they really are different things generally
speaking the sector size is a bit smaller than the block a typical example is that in unix the block
size is 4k or 1096 while individual disks may have a smaller sector size one particular model of
disk might have a sector size of 512 while some other kind of disk might have a different

sector size but by having the operating system work in terms of blocks we can ignore the different
sector sizes of the individual models of disk drive whenever the kernel wants to read or write from
the disk it reads or writes an entire block and that will cause the reading or writing of a number
of sectors by the device drivers for example if the sector size of one

particular disk drive is 512 then every time the kernel does a read or write it will transfer 4096
bytes and that will cause the reading or the writing of well eight sectors on a disk which is a
rotational device we have a number of different delays we may have to move the head the read write
head in or out to other tracks and we may also have to wait for the disk to

rotate so that the indicated data that the desired sector is underneath the read write head by
grouping sectors into a block we can make sure that whenever we read most of the sectors are read
sequentially we have to do a seek to the proper track and proper spot on the track to read the first
sector but the remaining seven sectors will be read sequentially which improves performance

quite a bit of course there's a penalty in many cases the last block in a file will only be
partially filled and the minimum minimum file size will be the block size 4096. so we could have
some wasted space but we do have an increase in performance now the device that is implemented by
QEMU is the virt io disk device remember that xv6 will be emulated by something called the QEMU
emulator and

the kimo emulator will uh interface to a vert I o disk uh this vert io stuff is an attempt to
standardize the interface between device drivers and actual hardware and there are a number of
different interfaces there is uh vert I o disk and usb and UART and some other stuff that are not
used by the xv6 system but uh a kimo will provide an interface to something called the vert I o disk
I

won't go over it in this video but the thing we need to be concerned about is that the disk driver
provides one function that can be used for the read and the write operations so this function which
is called vert I o disk read write can either perform a read or a write and which of those it does
is determined by the second argument and the first argument is a pointer to a

buffer the buffer is an example or an instance of a buff structure and one of these buffers will
contain enough space for the block in our case 1024 bytes as well as the particular number of the
block that we want to read or write it contains some other stuff as well now with the virt I o desk
and the xv6 system there is no error reporting this function does not return anything

and if any errors should occur then they would be masked and taken care of in these in this function
for example if there's a reed failure a checksum failure or something the sector or the entire block
would be re re-read until we finally can get the data uh in the emulator of course the disk is
emulated with a file on the host system so there's probably not really going to

be any errors anyway but in any case this function has no error reporting the function may sleep for
example if we want to read a buffer this function will start the read operation going and then it
will go to sleep somewhere else there is a trap handler so when the operation is complete the disc
will cause a trap and the handler for the disc will become active and among other things it will

wake up this function at that point the process that had invoked the read or the right operation
will be reawaken and will return so in almost all cases uh this function will sleep another thing
that's important to remember or to know is that this function will not reorder the operations it
will do them in exactly the order in which this function was called and we'll

see later that that's important when it comes to atomic transactions but for now just remember that
it does the operations in the order in which the function is involved now this buffer that's pointed
to here is an instance of a structure called buff and these structures are used as a cache for
blocks on the disk there are a fixed number of buffers that are pre-allocated at kernel startup time

and that's controlled by this constant inbound turns out we have exactly 30 buffers that can contain
data and each one has enough space for exactly one block of data in our case of course it's 1024
bytes in addition or we have the block number for which this particular block of data is a cache so
which block on the disk is held in the buffer and there's some other

variables some other fields that are used for synchronization we'll get to those in a second the
buffers can either be free or in use so we have a free list of buffers and buffers that are not free
are in use in this diagram I'm showing how the buffers are organized they are organized in a
circular linked list circular linked lists are also called doubly linked lists so here we have

individual buffer structures and one is special and that is the head of the list it will not contain
any other data it's just used for its next and previous pointers so I've indicated that with an x
then we have the buffers in the cache we have 30 buffers in the cache I've only shown four here for
simplicity these are organized from most recently used to least recently used so we can

locate the most recently used by following the head pointer sorry the next pointer of the head and
we can locate the least recently used by following the previous pointer in the head with a circular
linked list we can easily remove an element for example we can remove this element and move it to
the front of the list if it is now to be the most recently used

element so let's take a look at the detail of what's in these buffers and I'm showing that here we
see the next pointer and the previous pointer here we have a reference count if the reference count
is zero it means this buffer is not currently in use and it can be recycled if you will or used for
something else but if it's greater than 0 then we can't use it for anything else

the block number shows which block is represented in the data portion down here we have space for
the entire block of 1024 bytes and block number here is the number of which block is being stored
here the device number is right here in xv6 we just have a single disk device so we don't really see
much change in that it's more or less a constant of one but in a more complex unix system it

wouldn't be the case we also have a ballot field and that indicates whether the data field contains
the data from the disk or whether it contains garbage whether it doesn't contain that and if it
doesn't contain it well of course we need to read it in from the desk if we want to use that data we
also have a field called disk which is used entirely within the disk driver

in other words it's only used in the vert I o disk read write function so we don't really need to
think too much about it but basically it tells whether a disk operation is currently in progress or
not and we have a sleep lock which will protect the data as well as the valid bit and the disc bit
now here is uh where we allocate the buffers so we have a structure called b

cache with three fields uh the first field is a spinlock called lock and the second field is called
head and that contains an entire buffer structure which I've shown here so when we refer to the head
structure we can just refer to it as be cash dot head and we can also refer to the lock as be cash
dot lock okay and then the third field is an array in our case we have 30 elements in this

array and these are the buffers we do not access this array directly instead we go through the
pointers here from the head element the array is there to allocate the buffers and it's only
accessed during initialization when we build the initial circular linked list so all 30 buffers are
always on this list although from time to time we will remove one and insert it at a different

location the spinlock is protecting the linked list essentially okay it's protecting the fields next
previous ref count dev and block number so every time we want to allocate a buffer or release a
buffer we will need to acquire this the spinlock which we can refer to as be cash dot lock so now
let's take a look at the file buff.h which contains nothing more than

the definition of the buff structure and I'll try to line these up here I showed the fields in an
order that makes sense to me but you can see it's the same set of fields so we see the next and the
previous fields pointing to other buff structures we see the reference count we see the device
number we see the block number here we see the sleep lock and the valid

flag and the disk flag and then here we see the block of data the 1024 byte area that will contain
the block now let's talk about the file bufferio dot c and we'll start with the definition of this
structure called be cache and I showed that right here so you can see it's exactly the same we have
the spinlock we have the array of in our case 30 buffers shown right here and then we have the

head buffer structure and these are not pointers but these structures are just included directly I
want to also just read the comment that occurs at the top of this file because I think it's useful
the buffer cache is a link list of bus structures holding the cached copies of disk block contents
caching the disk blocks in memory will reduce the number of disk reads and also

provides a synchronization point for blocks that are used by multiple processes and here's the
important point the interface in order to get a buffer for a particular disk block we should call
the function b read and after changing the data in that buffer we can call b write to write it back
out to disk and we can continue to use it but when we're done using the data in that buffer

when we're done using the buffer itself we should call this function b release and then we should
not use the buffer after we release it and only one process at a time can use a buffer and so we
need to remember not to hold them for longer than necessary okay let's uh then move on to the
initialization function so let's look at this uh function b inet which is called during kernel
startup

it initializes the buffer cache lock that's the spinlock and then it creates a linked list of the
buffers that is it uh establishes this circular linked list it starts by creating an empty linked
list where the head points the previous pointer and the next pointer both point to the head itself
and then it goes through the array so here we're going through the

uh buff array from the zero element all the way up to the 29th element one by one and for each one
of them we are initializing the sleep block in that buffer structure okay and we're also adding it
to the linked list so I can just show this really quickly by simulating it or doing the instructions
one by one so we've got a a linked list here that uh points to some

here's the head and we've got some lists so far we've got a new structure b that we want to add so
we're going to make the next pointer point to the head dot next head dot next is this one so we make
our next pointer point here like that and then the next thing we do is we make our next pointer
point to the head sorry whoops we make our previous pointer point to the head so here we are

doing that okay and then in the next step we make the thing that the head is pointing to next okay
which is this element here we make its previous pointer point to b okay so I'll do that by rerouting
this pointer like that and then we make the head's next pointer point to b so um we reroute that to
here so at that point we've added this into the linked list

by the way there are several fields in the buff structure that are used but not uh initialized the
reference count the device number and the block number it turns out are initialized implicitly to
zero and we will rely on that initialization now let's take a look at the b read function this is
coming out of the bufferio.c file this will return a locked buffer with

the contents of the indicated disk block in that buffer okay it's going to search the cache and if
we find that we already have a buffer containing that disc block then we'll return that otherwise
we're going to allocate a new buffer and read in the data from the disk into the block before
returning and before we return we will grab the sleep lock for this buffer and we'll

also increment the reference count for that buffer a reference count of zero indicates that a
particular buffer is free and available for reuse but if the reference count is greater than zero
then that buffer is in use this function is past the device number and block number that we want to
get so the device number in xv6 doesn't change it's always one but the block number

tells which block on the disk we want to read in so this function will first call b get beget will
search the circular linked list of buffers to see whether we already have a cached copy of this
particular block and if so it will return a pointer to that buffer but if we don't already have one
then b get will return a pointer to a newly allocated buffer in other words it'll

find a free buffer with reference count of zero and return a pointer to that in either case it will
increment the reference count and grab the lock so now we have a buffer with the reference count
incremented and the sleep lock set then we look at the valid flag if we found an existing copy in
the cache of the write block then valid will be set to true but if we had to allocate a

new buffer then valid will be false so we need to read in the value we need to read in the block
from the disk so here we call vert I o disk read write this parameter 0 indicates that we want to be
doing a read and we pass it a pointer to b which will already have the device and block number
filled in by the b get function and then we set the valid flag to one

after that and return a pointer to the buffer okay now let's take a look at the b get function this
is going to first look through the buffer cache to see whether we already have a block with the
right data in it and if we do then we're going to return a pointer to that buffer but if we don't
we're going to allocate a free buffer so in either case in either case we are

going to return a pointer to a buffer which will be locked and which will have its reference count
incremented so here we are past the device and the block number to indicate which block we need to
read in and we return a pointer to the buffer that we have read that data into or maybe not read it
might not be valid um so we acquire the lock okay we're going to be accessing this circular

linked list here we're showing the next and previous pointers and the reference count and these are
protected by the spinlock in be cash we're only going to be reading the next and previous pointers
but when fields are protected by a lock we need to grab that lock and acquire it even though we're
only reading it the reference count we will be both reading and modifying

so here we acquire the lock and you can see before we return whether we release the lock whether we
return here okay we release the lock or if we return down here we release the lock if there's an
error we don't really worry too much about it okay in the first loop what we're doing is we're going
through the circular list uh in the forward order so you can see

we're following the next pointer we're starting with the first element in the list in other words we
look at the head element and follow its next pointer and we keep going until we wrap around and get
to the head again and what we're doing is we're asking is this buffer a match in other words does
its device and block number match the device and block number that we are searching

for and if so we found an existing copy of that buffer and so we've got the data already in the
cache we increment the reference count and of course release the lock in b cache and then finally we
acquire the lock that's in this particular buffer and return the pointer to the buffer now I should
point out right here that I mentioned earlier that the device block

number and reference count fields are implicitly initialized to zero and we don't actually do that
explicitly when we initialize things at the very beginning and be init and this is why because we're
reading these things before they ever get initialized so we're assuming they're zero to start with
and we never look for block number zero on device number zero

okay if we search the list and we don't find a match then we need to grab a buffer that's currently
unused so we're going to look through the list for a buffer with reference count zero remember that
a reference count of zero means that it's free however we are going to use the uh list in the
reverse order we're going to search using the previous field so we're searching it in the

reverse order we're starting with the head's previous pointer and then we keep going until we wrap
around and reach the head again okay and if we find one with the reference count of zero then we're
going to grab it we're going to change its device and block number to the device and block number
that we're searching for and since it may contain data from some

other disk block we will set it's valid to zero and we will essentially initialize uh its reference
count to one which is nothing more than actually incrementing it from zero and finally we release
the lock on the b cache and then acquire the sleep lock for the buffer itself before returning if uh
we can't find any free buffer we've got a problem we have pre-allocated enough buffers so this

should never happen but if it were to uh we would have an error message here now um I just want to
point out when you're maintaining a uh least recently used list uh we can do it sort of a couple of
ways every time we use something we can move it to the front of the list or every time we're done
using it we could move it to the rear of the list and actually six uses the latter

approach every time we're done with a buffer that is every time we release the buffer we will uh
remove it from wherever it is on the list and we will move it to the tail of the list that's why we
don't see any change in the position of the buffer on the list with this but later we'll see in the
b release function that we move it to the tail of the list now let's take a look at the b write

function it's passed a pointer to a buffer and it will write the contents of the block in that
buffer back to its location on the disk every buffer that we write to the disk must first be
acquired by a call to the b read function so at this point this buffer will have its device and
block number fields set as well as the 1024 bytes of data in the block and furthermore the b read

function will always return a buffer that is locked so we check to make sure that we are actually
holding the lock on this buffer and if not print an error message then we call the vert io disk read
write function with an argument of 1 here to perform the write operation this will invoke the disk
driver function that will write the block in that buffer back

to disk and it won't return until after that disk write is complete at which time b write returns
now the way we use buffers is we first always call b read and that will get the data from the disk
into the buffer so at this point uh the buffer will be locked and its device and block number will
be set and it will contain data then we can modify some or all of the

bytes in that block and then invoke b write to write the modified block back to disk we can then
modify some more bytes in that buffer and call b write another time and we can keep doing this
because as you can see the b write function doesn't release the buffer itself so when we get ready
to release the buffer we call this function b release and since that buffer

will have been acquired with a call to b read we had better be holding the lock on it okay so we
first check to make sure we are in fact holding the sleep lock on that buffer and then we release
that lock then we decrement the reference count and check to see if it's gone to zero now every time
we access the reference count or the previous or next field as we will

do here we need to be holding the b cache lock remember that the b cache lock in my picture here
protects these fields so we need to grab that lock first and we do that at this point here and then
subsequently we will release the b cache lock here at this point after getting the lock we decrement
the reference count if it's now zero then this buffer is free it's

not in use by anybody it still contains the data from that particular block and the device and block
number fields are still accurate along with the valid flag so it might be that a future call to be
read will be able to reuse it but on the other hand a future call to be read might actually need
this buffer for a different block and will not use the existing data

so if it's gone to zero then it's no longer in use and it's now the least recently used buffer so
with these statements here we modify the next and previous fields of this buffer as well as the head
buffer and I won't go over those in detail but basically what we're doing is we're taking the buffer
de-linking it from where it is and then moving it to the end that is the least recently used

end of this circular linked list I just want to comment here that the the uh comment that you see
here moved to the head of the most recently used list I feel is completely wrong and it should say
move it to the tail of that list so at this point the buffer is released and b release returns now
there are two other functions that are of interest that we can cover right

now the pin and unpin functions which will pin a buffer or unpin the buffer by pinning it all we're
doing is we're incrementing the reference count and unpinning it is decrementing the reference count
whenever we have a buffer that we want to make sure doesn't get released prematurely we can call
this b pin function to increment the reference count and then when we're done with that

buffer uh and we want to allow it to be released uh by others then we can decrement the reference
count in order to modify the reference count we must be holding the b cache lock so you can see we
acquire and release it here and we acquire and release it here as well okay that's it for the buffer
functions and I will see you in the next video
