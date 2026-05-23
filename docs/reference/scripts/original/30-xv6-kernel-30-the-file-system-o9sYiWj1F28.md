# xv6 Kernel-30: The File System

| Field | Value |
| --- | --- |
| Video ID | `o9sYiWj1F28` |
| URL | <https://www.youtube.com/watch?v=o9sYiWj1F28> |
| Source | `docs/reference/scripts/original-raw/30-xv6_Kernel-30_-_The_File_System-o9sYiWj1F28.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a video course on the xv6 operating system kernel there's a lot of code
involved with the file system and I've broken it up into a number of different videos so far I've
already talked about the buffer cache system and the disk logging system in future videos I'll talk
about the code but in this video I'm going to focus on how the file system is represented on

disk so in this video I'll talk about the disk organization and layout for directories and files
previously when I talked about the logging system I showed this schematic image of the disk where
each one of these squares is a block on disk and we had some stuff including the log area as well as
a main block area let's uh simplify that picture and just look at the

disk block area so we just have a sequential uh set of disk blocks on the disk and the code that
we've looked at already provides a number of functions that we can use to access these blocks and
here I'm showing what it would look like in the code to access the blocks on the disk in particular
we have the function b read which can be used to read a block in from the disk and we

provide a block number and it allocates a buffer from the cache and reads the disk block into memory
and returns a buffer pointer a pointer to that buffer in memory and then we have the log write
function where we provide a pointer to a buffer and it will write that buffer data back out onto the
disk we can read or update the data in that buffer any time after we

call b read and we can call log right multiple times so we may write it and then modify it some more
and write it again when we're done with that particular buffer we need to call this function b
release and between the b read call and the release the buffer will be locked which enforces the
fact that we have exclusive use of that buffer only this thread can access

that particular buffer now a collection of writes are uh included in a transaction the call to begin
op signals where the transaction begins and at the end of the transaction we should invoke this
function in op there can be a number of calls to log right within the transaction either to the same
buffer or to different buffers and those are all grouped together and

the logging system will enforce the fact that either all of the rights within the transaction are
performed or none of them are so it's an all or nothing situation a file system contains a single
tree of directories as well as a bunch of files that are located in those directories and all of
these are on one single device like a disk or a usb drive the directories are organized as a tree

that is there are no cycles and it's not a dag a dag is a directed acyclic graph so here's an
example of a file system and I've shown the directories in red and the files that are not
directories in green and you can see that if you just look at the red it is a tree in other words
every director directory has a single parent except for the root the other files that are not
directories

are shown in green and in some cases you can see that some of these files have multiple parents and
of course you remember that we refer to files by their path names the file itself does not have a
name instead we refer to it or access it based on its path name so for example this file here could
be referred to as slash a slash f slash k or we could also refer to the same file

using another path name every directory is itself a file um and so we have several different types
of files both directories and regular files in xv6 we also have a file type called device in fact
there's only one device that is located in the directory tree and that is the console but in general
we could have other devices as well those are the only three types of files

that xv6 implements but unix systems and linux systems implement other types as well posix is the
standard for unix and linux systems and it specifies other kinds of files so we typically see
devices we have two different kinds of devices actually we have a character device and a block
device we also have symbolic links which are sometimes called soft links we have

named pipes and sockets but none of those are available in xv6 and as I said files don't have names
we use path names to refer to an individual file I want to take a moment to talk about the
difference between hard links and symbolic links symbolic links are sometimes called soft links just
to be clear xv6 and every unix and linux system implements hard links but

xv6 does not implement symbolic links so a regular file can have multiple hard links pointing to it
that is multiple parents and in my example here this file has multiple hard links that refer to it
in other words this file can be located as an entry in both this directory and this directory here
every directory on the other hand has exactly one parent except of course the

root node so each directory can only have one hard link pointing to it that is the directory tree
has to be a tree whereas if we include the other files we have a directed acyclic graph so in xv6 we
have regular files directories and devices symbolic link is another kind of file and that's not
included in xv6 but it is a type of file so what's going on with the

symbolic link well we have a file okay but the file doesn't contain data it contains another path
name and when encountered the kernel will automatically use that path name to locate what we can
call the target file so for example this file here might be a symbolic link okay it doesn't contain
any data directly but instead what it contains here is a path name so if we refer to

this file okay with the path name c h what the kernel will do is it will go down and see that it's a
symbolic file a symbolic link and it will then use that second path name which might refer to let's
say this file over here and then instead of accessing the data here when you do a read or write for
example you'll be reading and writing data from this file here

now a path name is with hard length is either valid or invalid okay if we say c g we get to this
file it was a c b there is no b entry in this directory so that's a bad path name we have another
situation with symbolic links a symbolic link can become broken okay so we might have a perfectly
valid path name to get down here but the path name here might indicate a file

that doesn't exist it might be a bad path name with hard links the files must be on the same file
system or within the same file system as the directory that points to them so everything is in or on
one device with the hard links one advantage of symbolic links is that the path name can point to a
file that's on another device in another file system and we can run into problems if we try to

trace down or locate and follow a symbolic path name that is located on a file system that's not
even available so that's another thing that can go wrong with symbolic links next I want to define
the terms I number and inode as you know user mode programs refer to files with path names but the
path names aren't necessarily unique and they can change instead the kernel uses a number to

identify and work with files and that's called the I number it's a small integer and it's assigned
to the file when that file is created and throughout the life of the file its inem I number will
never change after the file is deleted the I number is no longer needed and can be recycled or
reused for a new file and that also happens and it's also the case that the root

directory has a fixed and known I number and that number is one this is a mistake the I number of
the root is one so that the kernel can locate the root directory and files are numbered from one not
from zero the I number of zero is reserved to indicate an unused directory entry associated with
every file are a number of attributes the most important one is the I number

which is used to uniquely identify each file the type tells which kind of file we're dealing with in
xv6 we can have a directory file a regular file or a device in other unix and linux systems we have
other types as well directories and regular files contain data and so if we have a directory or a
regular file we need to know how much data we have and so we have the size in

bytes of the data we also need to keep track of how many hard links point to this file okay that's
the number of parents in the tree for example this file has two hard links pointing to it if there
were no hard links pointing to it then there is no path name by which we could refer to a file and
so we can think of the number of hard links as a reference count when the number of

pointers to an object goes to zero in an object-oriented system the object space is reclaimed by the
garbage collector well a similar thing is going on with the xv6 kernel when the number of hard
lengths goes to zero the kernel will automatically delete the file in the case of directories and
regular files there is additional data and so we need to know where on disk

that data is stored so we basically have a list of the block numbers that contain the files data in
other systems not in xv6 but in other unix linux systems we have additional information including
the timestamp of when the file was created and when it was last modified and so on we also have
information about who owns the file and some bits that indicate the

permissions and to tell how other users can access this file the attributes are kept on disk in a
small fixed size structure called the inode okay the inode is kept on disk it's not kept in any file
but these are kept in a separate area of the disk so somewhere on the disk there is essentially an
array indexed by I number of these inode structures whenever a file is in use by the kernel

the kernel needs to read in the inode structure from disk and keep it in memory so the kernel
maintains a cache of inodes in memory so let's take a look at how the inode structure on disk is
defined so here we have a structure that shows the on disk inode we don't see the I number as I said
these things are kept in an array that's indexed by the I number so the I number

is implicit the type field tells what kind of file this is and we have several constants to indicate
whether it's a directory a regular file or a device we also use the type code to indicate whether
the inode is free so some of the inodes on disk in this inode array are in use and have a type of
directory file or device but others are free and unused and available

for future files and we use a type of xero for those if the file is a device then we also have the
major and minor numbers being valid and what are the major and minor numbers this is essentially the
device number so the device number is a 32-bit number and it's broken up into two 16-bit fields the
major and the minor number the major number is uh an identifier to

tell what kind of device it is and the minor number tells which device among several it is for
example if we have three hewlett packard printers the major number would indicate that this is a
hewlett-packard printer and that will be used to indicate what kind of what routines we should use
to access the printer the minor number would tell whether this is printer number

one two or three or whatever then we have the number of hard links and then we have the size and
bytes if it is a directory or a regular file and finally if it is we have the blocks on disk where
those where that data will be kept so this is an array of block numbers here previously when I
talked about disk logging I presented this schematic diagram for what the disk looks like

and I just had the main block area here but now I want to present another view of the disk that is
more accurate and more detailed let's go through this thing the boot record is a block that is
neither read nor modified by the kernel it's used just during the boot and startup process in order
to load the kernel into memory the super block contains a number of

parameters it is read by the kernel but not modified among other things that tells where the other
items on the disk are located then we have the log which I talked about elsewhere and won't go into
any more detail on here then we have an area where the inode structures are stored we have the
bitmap and we have the data blocks remember that each inode is stored in a

fixed size structure and these are packed into an array and this array is kept in these blocks a
small number of inodes will fit into each block so depending on how many inodes we have including
both the ones that are in use and the ones that are free we have a number of blocks that are
allocated just to the storage of inodes and then we have a bitmap the bitmap is

contains one bit for every block on the disk and that bit indicates whether that block is in use or
free it's really useful for the data blocks okay we consider all of these blocks up to this point
here to be in use so the bitmap will indicate that all of these blocks up to this point are in use
and all of these blocks here are either in use or free indicated by a

bit in this bitmap okay so there's one bit for all of the blocks in this particular file system we
only have a thousand blocks so this bitmap would only be as big as required to contain a thousand
bits that'll all fit into one block of course but I've shown three blocks here for illustrative
purposes and the bit is either one to indicate the block is in use or zero to indicate

that it's free and finally we have the data blocks this is where the files and directories are
actually stored now let's look at the superblock uh the superblock is essentially a block that
contains this structure here and the structure has a number of fields okay so let's see what's
stored in this block on disk first of all we have a magic number this is just a constant and the
operating

system when it reads in the super block will check this number to make sure it has the right value
and if it doesn't that might indicate that wow this is not the kind of file system we expect or
something is dreadfully wrong this disk doesn't contain a file system at all or something else is
really the matter the size field in our example will be a thousand

it tells how many blocks are available on the disk okay the number of data blocks is 959 in our case
so we have that many blocks here and we have 200 inodes so how many blocks is that well the kernel
will compute that and figure that out but in any case we have enough space to store 200 inodes and
that means that this file system can accommodate up to 200 files we cannot have more than 200

files in log is the number of blocks that are allocated to the uh log and then we have an indication
of where that starts it starts at block number two and then where the I nodes start block number 32
and then where the bitmap starts block number 38.

uh the size of the bid bitmap will be determined by the number of uh blocks and as I said with a
thousand blocks we can actually fit the bitmap into a single block but um I've shown three here even
though that wouldn't be the case so uh this superblock is read in from disk at startup by this
function read sb and then it never changes and so here I have the structure

that contains these and you see this is exactly what we saw before we have the magic number we have
the size in blocks the number of inodes this number of blocks in the log where the log starts where
the inode blocks start and where the bitmap blocks start and so that's read in at startup time in my
example file system I showed the edges as being labeled and that is

important because it emphasizes path names but that might not be the way you think of directories
here I'm showing the edges labeled but you might think of it as a directory containing a bunch of
names and those names each refer to a directory or file somehow well directories are stored in files
so for every directory there is a file and that file contains a number of blocks

and the directory information is stored in those blocks here's what we have in xv6 in x36 a
directory is represented as an array and each array entry has both a name and a reference to the
file the name has space for up to 14 characters and the reference to the file is an I number the
original unix system had this 14 character limit for file names in xv6 we

can have up to 14 characters for the file name and if it's shorter we would terminate the file name
with a null character when we want to look up a file we have to go through this array linearly and
that's a bit time consuming in modern unix and linux systems we have a different organization for
directories that will accommodate longer file names and as you know some directories contain

hundreds of files so we have different data structures that allow for faster lookups but essentially
it's still the same idea you have a name and for each name you have an I number so that pretty much
completes the information about how the file system is stored on disk so next let's take a look at
how the mkfs function or program which can be used to create or build a file system from

scratch mkfs or makefile system is a standalone c program it's not part of the xv6 kernel and it's
not a user mode program that runs under the xv6 kernel instead it's a separate c program it's
compiled on your host computer and it runs on that computer just as the QEMU emulator runs on that
host computer when you run the make file it compiles the xv6 kernel and it compiles all of

the user mode programs and it also compiles this mkfs program and runs this program as well and what
does this make file system program do well it's going to create the disk image file the disk image
file is named fs.image and this is a file on the host computer and it will be used by QEMU to
emulate the disk so all of the contents of the disk are kept in this file system image file and

whenever a read or write command is issued to the disk kimo will actually go to this file on the
host system to get or put data so our disk in xv6 is initialized to have a thousand blocks and each
block is 1024 bytes so that means there is a thousand k bytes in this file one byte in the file for
every byte on the emulated disk make file system will initialize the

disk okay we'll create the entire file system it will create the directory tree and it will put a
number of files into the directory tree so that when the kernel begins that is at startup time the
kernel will find the file system already exists so what does it do well make file system will write
the super block to the file or that is to the image file actually it doesn't write anything for

the boot record because that's not really needed and it will also initialize all the inodes it will
initialize the bitmap area of the emulated disk it will create a directory structure and initialize
some directories and for each of the user files that is the user mode executables like cat echo the
initial program and the shell and so on it will create a file and add that to

the directory of the file system so that when make file system is done this image file will contain
an entire complete file system ready to go so that the kernel can boot up and find the file system
real unix and linux systems also have a similar program called make file system and these will
initialize not an image file but a device and they will write whatever is necessary to initialize
the

organization for the file structure on that device the file system organization that we've talked
about for xv6 is fairly simplified and these make file system programs for other systems will create
file systems uh with very different and more complex uh file systems uh but so these are complicated
programs to create the initial file system on a device but in any case uh the make file

system program that we are looking at here in xv6 we'll make use of some parameters I will go to the
file param.h and get things like the file system size that's the number of blocks to create in the
file system as well as the number of blocks to add to the log and we also have a number of
predefined parameters or macros and constants for example the number of

bits per block that uh we can store for the bitmap or the number of inodes that we can pack into a
block in the inode array that will be represented on disk these constants are needed by the
makefilesystem.c program to create the image file okay that wraps it up I'll see you in the next
video
