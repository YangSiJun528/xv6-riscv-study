# xv6 Kernel-31: Inodes

| Field | Value |
| --- | --- |
| Video ID | `yzyCoL9SA2M` |
| URL | <https://www.youtube.com/watch?v=yzyCoL9SA2M> |
| Source | `docs/reference/scripts/original-raw/31-xv6_Kernel-31_-_Inodes-yzyCoL9SA2M.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'll be talking
about inodes I'll talk about how they are represented and how they are stored in an array on the
disk and I'll be talking about how they are represented in memory we have a cache in memory of the
inodes that we're currently using I'll go over some definitions from the

files fs.h and file.h and then I'll look at the code in fs fs.c there are a lot of functions 25
functions in this file and in this video I will just do an overview of these functions I will
actually walk through the code of these functions in the next video okay let's get started
previously I presented this picture which is a schematic showing the layout of the disk

in this picture each one of these small squares represents a disk block and we see the area where
the log is kept that's here we see the area where the inode array is kept and that's here we also
have a bitmap which is kept here and we have the data block area the data block area contains the
blocks that store the data of the regular files and the directories

in the bitmap there's a single bit for each one of these blocks and that bit tells whether the block
is free or in use the blocks from zero all the way up to here are considered to be in use so within
the bitmap the bits corresponding to these blocks will be a 1 to indicate that those blocks are in
use whenever we want to get a free block to add to a directory or file

we will look in the bitmap and it will only find a free block somewhere in this area okay next what
I want to do is take a closer look at what's going on with the inodes so here I am showing uh the
disk again here's the log here's the bitmap here the data blocks this area is where the inode array
is kept and it starts at some particular block number and that's

value is in the super block so there's a field in the super block structure called inode start that
allows us to find where this array begins this array contains a number of structures each element in
the array is an inode structure and I'll talk about those in a second they are packed together as
tightly as possible I'm indicating that we can get four of these structures per block

and uh here's a block right here and here's a block and each one of these is a block containing four
inode structures with a little bit of wasted space at the end there will be wasted space if we
cannot get an even number of inodes packed into a block and in reality these inodes are not quite
that big so we can actually get more than just four but here I'm showing you that they are packed

together like this with uh some unused space now let's take a look at one particular inode and see
what that structure looks like so I covered this before but let me remind you that it has a type
field that tells whether it's a regular file a directory or a device if it's device it's got a major
number and a minor number and in any case regardless of what kind

of file it is we have in link which is the number of hard links that point to it so if this
particular file is it is in say let's say four directories then the end link will be four this tells
how many directories point to this particular file if it is a regular file or a directory then it
will have a size the device files would ignore the size and this array down here

and finally for regular files and directories we have to have the actual data and so we have an
array here of block numbers each block number can be thought of as a pointer to a block up in this
data area here and that array has a fixed size which happens to be 12. so we have 12 blocks here
from 0 to 11 and beyond that if the file grows beyond a size of 12 blocks

what we do is we use an indirect block so the next pointer okay the 13th pointer points to another
block on disk somewhere in here and that block is filled with block numbers or pointers to other
blocks and since the block number is four bytes and the block size is uh 1024 we can pack in 256
pointers into the indirect block so with the 256 blocks that are pointed to

by the indirect block and these 12 blocks that imposes the maximum size on any file it's uh rather
limited in xv6 here it's given by these two constants the number of direct pointers is 12 and the
number of pointers we can squeeze into a an indirect block is 256.

and production unix and linux systems we have other ways of accommodating bigger files for example
we might have a pointer that points to a double indirect block so each of these pointers points to
an indirect block it points to the block itself we can also have triple in direction and with that
we have a potential maximum file size that's absolutely huge but xv6 just has this single

block of indirection and that's good enough for us so we don't actually store the inode number
that's implicit so this is the inode for file number six and uh we will then need to read it into
memory where we can work on it and for that we have a cache of inodes that are stored in memory so
there's an array or I should say a variable called I table that's the structure and it contains two

fields a lock and an array called inode and that array contains a number of uh structures that look
like this and it's uh fixed uh to have a size of 50. so we have up to 50 inodes out of the 200 that
we can be actively working on so if we are working on a file that is it's open and we're accessing
it what the kernel will do is it will read the structure from the disk into one of these cached

structures the cache inode contains all the fields that are in the inode on disk type major minor in
link size in the address array type major minor in link size and the address array as well as some
additional fields for one thing we need the I number as well as the device number in xv6 we only
have one device that can contain file systems but in other unix systems we will have

several devices that are currently being used so in order to identify the file we need not only the
device number but the I number and the I numbers are unique on each file system that if we have
several file systems uh the I numbers we could have some confusion because I number six uh just says
it's uh I number six it doesn't say which file system that's in

so it could be either this file system or some other file system so that's why we have the device
number here the reference count is the number of pointers to this structure or you can think of it
as the number of threads that are currently using this cached inode within this cache of 50 uh
inodes the reference number will tell which ones are free and which ones are in use if

the reference number is zero then it's free and it can be used to cache a different inode but if
it's in use the reference number will be something greater than zero the structure also contains a
lock oh I've labeled these wrong the lock field is a sleep lock okay and the valid field is a flag
and the sleep lock protects this so whenever the kernel wants to read or

update any of the fields in the inode it must be holding the sleep lock called lock at some point uh
the particular element in the cache is allocated to some inode and the I number is filled in along
with the device and the reference count but the data may not be read in immediately so the valid
flag tells whether the data has been read in from disk or not and if it's zero then we

need to go ahead and read in from disk if we're going to look at it the uh fields up here the device
number I inode number and reference count are protected by a spinlock in this eye table structure
and that spinlock protects all of these so when we allocate an element from the cache to be used for
a particular I node we will need to first grab the spinlock

and then set the I I number and the device as well as incrementing the reference count to 1.

there's another variable called device switch and that's an array it happens to be containing 10
elements as given by this constant number of devices each element in the array is a structure and
I've shown one of them enlarged here and for each device there is an element in this array and what
does the array contain well it contains pointers two functions so

for example element number one is the console you know whenever we do a reader write to the serial
output we need to access this device and so for the console device we use these two particular
functions to perform the read and the right for other devices we might have different functions so
these are essentially the device drivers that are specific to device number one

so for the console device the read function uh takes a destination address this is a buffer in
kernel memory where well it's a buffer where we're going to be storing the characters that we read
here we have a byte count that's how many characters we want to read and here we have a flag the
first argument is either 0 or 1 to indicate whether this destination address is a

physical address in kernel address space or whether it's a virtual address in some user's virtual
address and it returns the number of bytes that we have successfully read likewise there's a console
write function it takes the address of where the characters are and how many bytes there are that we
want to write out to the console as well as a flag to tell whether this address is in the kernel

space or the user's virtual address space and it returns the number of bytes written in xv6 this
array is not really used very much in fact the only element that has anything in it is device number
one and so that's the console device it's a bit confusing because the um file system is also on
device number one but that's just the way it is so we're only using this uh device array for the

console but here you get the idea that for different kinds of devices we could have different
routines to be used for them so the major number would be used to index into this device to tell us
which functions to use and the minor number would probably be passed to the driver to indicate which
device to send it to now let's take a look at the file fs.h in the real world there are several

kinds of file systems that are in common use and here's some examples ext4 the file system used by
minix xfs are some common ones we might call the file system format used by xv6 x36 fs a real
operating system is going to need to be able to handle several different file system formats xv6 can
only handle one and the definitions that are related to the on disk file system formatting are

contained in this file fs.h so let's go through and see what it's got first of all we need to be
able to locate the root directory and that is inode number one the block size for the blocks on this
disc is 1024.

the second block that is block numbers one is uh the superblock it's a block that's read in at
startup and it contains a number of values and these values never change and this block is never
updated this just basically tells us where the log and the bitmap and the inode array are on the
disk here's the magic number that's related to this field inodes contain 12 direct pointers okay

that's this number here and the indirect block will contain a number of pointers given by this
constant here we take the block size and divide it by the size of a block number and that's the 256
number I referred to earlier uh the files have a maximum size determined by the number of direct
pointers plus the number of indirect pointers so that's 12 plus 256 and given

that the block size is 200 is is 1024 bytes that imposes a maximum size on every block the inode as
it's kept on disk has this uh structure here uh the type the major and minor numbers the number of
hard lengths the size and bytes and this array with 12 direct pointers and one indirect block which
is pointed to by the 13th element in this array let's go to the next page which is here

this constant here is the number of inodes we can squeeze into one block okay here I indicated it
was four and maybe uh the block size is evenly divisible by the size of the inode structure uh or
maybe it's not in which case we have a little bit of extra space but in any case it's not really
four it's given by this inodes per block value here that is determined by the size of the block and

the size of the inode structure okay which block on disk will contain a particular inode so this
function this macro preprocessor function I block is passed an inode number and a pointer to the
superblock which is a structure in memory that has things like where the inode array starts in my
example I started it at 32 and so we just do a little bit of division here by the inodes per block

and add 32.

the bitmap will contain a number of bits in each block and the number of bits in each block is just
1024 bytes times 8 bits per byte so given a particular bit number or block number where which block
in the bitmap will contain that bit okay so b is the block number and this uh b block will compute b
divided by the number of uh bits per block and where the bitmap

starts the directory contains a bunch of files and each file can have up to 14 characters in its
name and so that's directory size here and the directories will contain a number of these directory
entries which has each one of these structures has an inode number and 14 characters so it can
accommodate a file name size up to 14.

okay now I also want to take a look in this file file.h which contains a number of different things
but in particular it contains the in memory inode so remember that our cache is made up of 50 it
turns out to be 50 of these inode structures and we have uh the fields that I talked about in this
picture here so here is the structure as I showed it before device inode number and the reference

count so device I know inode number and the reference count and then we have a sleep lock and here's
the sleep lock called lock and this sleep lock protects everything below that okay so we have the
type major minor in like the number of hard lengths size and the uh pointers uh it also protects the
valid field uh I didn't really draw this quite correctly but the sleep lock corrects the valid

flag as well and then I mentioned the device switch array and that's given right here these are the
structures and each one is a uh structure that contains two pointers a reed and a right field so
that's uh was shown in this picture here this structure here contains two fields called read and
write and each of these is a pointer to a function okay the function takes three arguments

and returns uh an integer value and the array itself is declared elsewhere okay that's it for oh oh
wait we also have this console which is the number in the array of the console device there are a
couple of other definitions that I wanted to cover and these are coming out of the file fs.c not dot
h here is the super block variable sb where the super block is read into at startup time

then we have a macro function called minimum which is defined pretty much as you would expect it to
be remember that as a kernel programmer you have to define everything you're going to use you can't
rely on library functions that you would normally rely on as a regular programmer there's something
else that I wanted to mention and that's the eye table structure

here is the picture of it that I used this is where we have the cache of inodes and it has a
spinlock and it has this array of inode structures that we will use as our cache and I also wanted
to mention something from the file file.c and that is this array I discussed previously called
device switch which has 10 as it turns out elements I won't talk about the rest of this

stuff right now but I'll cover this in a later video there are a lot of functions in fs.c in fact
they're about 25 functions the code in some of these functions is pretty complicated and before I
get into the code itself I want to just introduce these functions by name and talk a little bit
about each one of them the first three functions are concerned with initialization

I init is for initializing the cache of inodes uh file system init is uh past the device number and
it initializes things and among other things it calls read sb which will read in the super block
from the disk and store it in this super block structure the function b0 is past a block number and
it will go out to disk and write all zeros to that block b alec is used when we need to allocate

a block for example when we're growing a file and so it will search the bitmap to find a block that
is currently free and it will change the bitmap to indicate that it's now in use and return the
number of that block to free the block after we're done with it we can call b free which is past a
block number and it will change the bit in the bitmap to indicate that the block

is now free the I alloc function is used when we're creating a new file and we say which device we
want to create it on and the type of the new file this could be a regular file a directory or a
device file and it returns a pointer to the inode that is a to a structure in the inode cache what
it will do is it will read the disk to locate an unused inode in the inode array and so

at that point it determines what the I number of the new file will be remember that the I notes in
this array will have a type value of 0 to indicate that they're free and so this function will set
the type on the disk to indicate that that I node is now used and then it will call I get and return
uh a pointer to the inode and so what does I get do well at this point

we've got the I number of the new file and we allocate a structure in the inode cache okay if we
already have a structure for that particular I number then we go ahead and use that one but in
either case we increment the reference count to indicate that this particular inode in the cache is
in use we will not actually read in the inode data from the disk okay remember there's

a valid flag in the inode structure and so that may remain zero to indicate that there's no valid
data and each inode in the cache has a lock associated with it and I get will not set the lock okay
if we want to set the lock we can call I lock which is pass the pointer to the inode and it will set
the sleep lock in the inode furthermore if the data in that node is

has not yet been read in from disk then the valid flag will be false or zero and this function will
also read in the data from the inode structure on disk and store it in the cache version and there
is an I unlock function which will simply unlock the inode next we look at the I update function and
this is passed a pointer to an inode in the inode cache and after we've made

changes to the inode we need to write that inode back to disk and that's what this function will do
from time to time we need to increment the reference count and so we can call I dupe to increment
that reference count remember that the reference count is protected by the lock for the entire cache
the itable.lock field so it'll have to set that in order to increment the reference count

and the I put function is passed pointer to an inode and it will decrement the reference count now
if uh this particular thread is the last thread that's using that inode in the cache then the
reference count will have gone to zero and at that point we can also ask or this function will also
ask whether there are any hard links to this file and in link is zero it means there are

no references to this file from any directory so at that point this file is not accessible there is
no path name which can get to it so this file needs to be released and freed the space associated
with it has to be freed so what it will do in that case is lock the inode truncate the file to a
length of zero which will free all the data blocks and then it will call this I update function

here to write the modified inode back to disk and at that point the uh the file is freed the
function I unlock put is just calling I unlock followed by I put and then we have this bmap function
uh it's passed a pointer to an inode so we've got some file that we're concerned about and we've got
the inode cached in the inode cache and we are trying to get some data out

of the file or perhaps write data into the file so we figure out which block in the file with the
first byte of the file being in block 0 of that file so we have the block number relative to the
beginning of the file and we need to translate it to a block on the disk and that's what bmap does
so it will consult the the array of block pointers of direct block pointers and if it is a block

beyond the 12th block it will look at the block of in direction and it will uh find a pointer to
locate the block on the disk so it will figure out the block number on the desk if that block is not
actually allocated in the file okay it's not in the file then this function will also allocate a
free block and add it to the file and it will also update the inode

by adding it to either the array of direct pointers or the block of indirect pointers next let's
look at the function I truncate which is passed a pointer to an inode which is in the inode cache
and it will truncate this file to size 0.

it will free all the data blocks in this file and it will free the indirection block too if there is
one and set the size attribute to zero and call I update to update the inode on the disk this is
called among other places from the I put function remember with I put we basically call this when
we're done with a particular file and it will decrement the reference count and if there are no

hard links to it then it will truncate the file to link zero it calls I update as well as I truncate
and you might think well gee that is a duplication of effort it's writing to the disk twice but
remember we're using the disk logging system and um so there will only actually be one right to the
disk the reason we call I update here a second time is because we are also

setting the type field to zero which I did not include in this so let's add that there and we need
to set the type field to zero and then write that to the disk so that the on disk inode structure
will have a type of zero which will indicate that this particular inode is free and can be recycled
okay the next function is stat I and this is passed a pointer to an inode

and to a structure called a stat structure and all it's going to do is copy data from the inode to
someplace else now we want to look at the read eye and right eye functions these are used to
actually move data into or out of a file so read eye is called with a pointer to at inode that's in
the cache and a destination address of where to put the data in memory

as well as an offset in the file of where to get the data and the number of bytes to transfer this
destination address is either a physical address or it is a virtual address in some user's virtual
address space and that's determined by this flag zero if destination address is a physical address
one if it's a virtual address and it'll return the number of bytes

read the right eye is just the other direction and here we have the same arguments a pointer to an
inode the flag the source from where we're getting the data and the number of bytes and where in the
file we will be writing it to and then it returns the number of bytes correctly written and then we
have the function directory lookup and this is passed a pointer to an inode

okay that's a directory file or at least it should be a directory file and a name okay this is uh
something that's uh 14 characters or fewer and we also have an offset variable and we'll pass the
address of that so what it's going to do is look this name up in the file that's pointed to by this
inode here and if we find this name in the directory then we're going to call I get

which will add an inode for this file into the inode cache it will not lock it okay and it will then
return the inode of the file that we just looked up in addition we've got this offset variable and
so this function will also save the offset in the directory of the directory entry for this
particular file so it will save the location of this directory entry within the directory

array the directory file in this variable offset and if the file is not found it will return zero
the next function to look at is directory link which is used to add an entry to a directory so it's
passed a pointer to an inode that represents the directory as well as a pointer to a name and the I
number and it will add a name number pair to this file it will return zero if everything went

okay and minus one if there's a failure and the failure can happen if there is already an entry for
that name in the directory and this function will do reads from the disk as necessary to check and
after it adds the entry it will write that back to the disk by the way all of these functions that
we've talked about so far are reading from the disk and many of them are

writing as well with a log write operation these reads and the corresponding writes and the releases
the b release function calls are all part of transactions but none of these functions include the
transaction itself remember the transaction begins with a call to the begin op function and ends
with a call to the end up function none of these functions in fs.c in the file

fs.c contain calls to begin up or end up but they are all used within code that is part of a
transaction the next function skip element is not a function that accesses the disk it doesn't do
any reading or writing it's simply a string processing function it's passed a pointer to a path name
string somewhere in memory such as this uh string right here and so it's passed a pointer to the

first character and it will parse it essentially it will find uh where the first element is and it
will store that in a variable called name so it's passed an address of some 14 byte area and it will
store whatever the first element in this path name is and in this case it's the characters usr in
the name variable and it will also advance the pointer to the next element in the

path name and return a pointer to the next element so it'll return a pointer to this if the path
name is empty and there are no more elements it will return zero okay finally we have this name I
function and this is what we've sort of been working toward we're passed a pointer to a path name
and it's going to find that file on disk move the inode into the inode cache and

return a pointer to the cached inode so the inode will be cached and the reference count of that
inode will be incremented it will not be locked and if the file doesn't exist if this path name is
no good then this function will return null or zero there's also a variation of this that will do
exactly the same thing it's passed a pointer to a path name and it returns a pointer to the inode in

the cache but it will stop one level earlier so basically if it's past this path name here it would
return a pointer to you the directory user slash bin okay and it will save cat in this name variable
so we'll stop one level earlier in the path name save the last file name that is cap in this name
variable and it will return the inode of the parent directory

and both of these are quite similar and all they do is call the function name x which actually does
the work of both the name I and the name I parent function so name x is passed a pointer to a path
name and a flag to tell whether it's uh doing the name I function or the name I parent function and
uh the name variable and does the work of both name I and name my parent and name x is not called

from anywhere else okay that's it for this video in the next video I will start looking at the code
for these functions see you there
