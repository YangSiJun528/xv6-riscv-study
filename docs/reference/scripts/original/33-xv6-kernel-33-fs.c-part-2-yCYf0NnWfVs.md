# xv6 Kernel-33: fs.c Part-2

| Field | Value |
| --- | --- |
| Video ID | `yCYf0NnWfVs` |
| URL | <https://www.youtube.com/watch?v=yCYf0NnWfVs> |
| Source | `docs/reference/scripts/original-raw/33-xv6_Kernel-33_-_fs.c_Part-2-yCYf0NnWfVs.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xb6 operating system kernel in an earlier video I described
the inode system and talked about the general organization of the file fs.c I talked about the data
structures and I gave an overview of all of the functions in that file in the previous video to this
video I started walking through the code of the functions that are in fs.c and in this

video I will finish walking through those functions let me just put on the screen for a visual
reference what functions were covered in the previous video so we covered all these functions and
here are some more that we covered I will start with bmap in this video but in the previous video I
covered eye trunk and Stat I after I talk about bmap I will go ahead

and talk about the remaining functions starting with read I and so on and as I said we are working
toward the name I function and I will end by talking about the name I function which is the function
that is past a pointer to a string that contains a path and we'll find that file on the disk and
read an inode into the in-memory cache and return a pointer to that so let's get

started from time to time we want to read data from write data to a file and we need to know where
on disk that data is stored so which block number on disk do we need to read or write to get the
data and that's the function of this bmap function it's passed a file or more precisely a pointer to
some inode describing a file as well as the block number relative to the beginning of the

file so let's say we want to write some bytes in well let's say the seventh Block in the file we
need to convert that seven block number into the actual block number on disk return the disk block
address of the inth Block in the file and if that block has not been allocated to the file then this
function will allocate and zero a new block so looking at the picture here uh what

we are going to do let's say we want to get the seventh block we'll need to go to the cache uh inode
well let me use this picture over here because it's got the indirect block and if it's in one of the
first 12 blocks we just need to access the inode and get the block number and find out where on disk
that block is stored if it's uh greater than uh 12 here if it's 12 or greater we need to

access the indirect block and get the block number from the indirect array so that's what we are
doing here uh first we're asking whether the block number that we're looking for is less than well
12 that is if it's 0 through 11. in that case we can use one of the direct pointers so we are going
to look into the inode and the array and get the element from that array and save it and

then return it okay if that array contains a zero pointer then that block hasn't been allocated so
we need to call B Alec we will allocate or find a free block and set it to all zeros and return the
block number on disk which we will then store into the array so that it will be there in the future
and in either case we return the block number however if the block that we're looking

for is 12 or greater then we are going to need to follow this pointer and look in the indirect block
so for example if we're looking for a block number 14 well this is uh 12 13 14 it's this one we need
to take the block number 14 and subtract off these first 12 to get an index into this array that is
we need the index of two so that's what's happening in this line here we're

subtracting off 12 from the block number and now we've got an index that's between 0 and 255. or at
least we hope it is the first thing we do is check to make sure that our index is not larger than
255 and if it is well we've tried to access a block that is beyond the maximum file size that should
have been checked by whoever calls this function and if not we uh issue a panic

um so then we need to load the indirect block and we then go to the array and this is the last
element of the array and ask whether it's zero or not if it is 0 then we need to allocate the block
of indirect pointers so here we allocate that indirect block and set it all to zeros and we save the
block number of the indirect Block in the array so we're allocating this block here and saving a

pointer in this element here and that happens here and we in either case we get the address of the
indirect block and here we are reading the indirect block into some buffer and setting a to point to
where that data is in that buffer so that's the beginning of this array and then we're using our
value here to index into that array and we are saving the element so that's the block number that

we're interested in and if it's null then that block hasn't been allocated so we have to call bialic
again to allocate a block and set it to zero and we will store the block address of the freshly
allocated block into the array and we need to update the indirect block if we have modified it so
that's where we have the log right here but otherwise we don't need to update the indirect block

we just use the address that we retrieved and return that so here we are releasing the indirect
block or more precisely releasing the buffer that contains the block of indirect pointers now uh you
can see that we are modifying the I node here okay we could be modifying the array directly either
here or um here and so if we are modifying it uh we need to update the I node on disk so

uh that's the responsibility of a caller of bmap so let's keep that in mind at some point we're
going to want to transfer bytes from the file to memory and that's the purpose of the read I
function so it's going to read data from the file that's described by the inode pointed to by IP
we're going to get the data from this offset within the file and we're going

to transfer in bytes we're going to move those into memory and the address that we're going to move
them to is given by the destination argument here that could either be a virtual address or a
physical address and that's determined by this argument here if this argument is a 1 then the
destination address is an address in the virtual address space of the currently

executing process if this is zero then destination is a physical address in the kernel address space
we begin with a couple of checks first if the offset is beyond the end of the file then we return
zero by the way this function Returns the number of bytes successfully transferred this test here is
written in an odd way if you subtract offset from both sides

you see that it's really just checking to see whether the number of bytes that we want to transfer
is less than zero and if it is again we return 0.

offset may be within the file but the extent of the amount of data that we're transferring may go
beyond the end of the file and if that's the case then we need to reduce in so that we only transfer
as many bytes as the file has so here we compute the number of bytes between the offset and the end
of the file and that is our new in so the invites that we want to transfer

can be spread over several blocks in the file and we need to read each block individually and get
the bytes out of that block and we'll so we'll call B read a number of times and for each one we'll
transfer using this either copy out function so this Loop will transfer a number of bytes each time
and then it will finally terminate and return the total number of

bytes transferred so we start total at zero and we end when we transfer uh n bytes and then we
return the number of bytes that we've transferred so the first thing we need to do is figure out for
this particular chunk for the next chunk where it is in the file and where it is on the disk so the
current offset from which we're getting data is given by offset and as we go

through we'll Advance offset and we'll Advance the destination pointer as we do each chunk we'll
compute the number of bytes we're going to transfer here that's M and increment the Source the
offset within the file as well as the destination pointer and the number of bytes transferred so we
take the offset where we're at right now and figure out what Block it's in so we

compute the block number that contains offset right here and call bmap bmap Will map this block
number which is relative to the beginning of the file into a block number on the disk and then we
call B read to read that into a buffer which is pointed to by this buffer pointer here now we figure
out how many bytes we want to transfer in the next step and so um we don't want to go over the end
of

this block we don't want to go past the end of this block so that's what we do here we take the
offset and see how much is left in this particular block with this expression here and we don't want
to transfer bytes beyond what we're supposed to transfer so we look at how many bytes we've
transferred so far and subtract that from M and that's how many bytes we have

left to transfer total and we take the minimum of those two and that will be the number of bytes
we're going to transfer so then we call either copy out and we cover that earlier but basically it's
passed a source address here a number of bytes to copy and a destination address and then a flag to
tell whether those bytes will be copied into a user's virtual address space or into the kernel

space into a physical memory so that's just the parameter that we had up here okay and we are
copying M bytes and where are we copying them from well we're copying here's the where the data is
in the buffer and we look at the offset and we figure out where that is so we offset into the
Buffer's data and that's where we begin copying our M bytes and destinations where we copy

them to and if anything goes wrong this function will return a minus one and we immediately abort
that could be if the destination address is not actually in the user's virtual address space it's
not an allocated page for example that could cause a problem and if that's the case then we
immediately release the buffer and return a minus one and break out of here

so we set total to minus one and then would turn out but if the copy is okay then we just continue
we release the buffer that we allocated here and then iterate and do the next Chunk we just copied M
bytes so we add m to the total bytes we've copied so far as well as from The Source address within
the file that is the offset and the destination address for the bytes are being copied

too now one thing I want to point out is we're calling bmap here and as I mentioned earlier bmap can
modify the inode in memory if the data is not allocated in the file it will allocate it so it may
allocate new blocks and add them to the file and that will modify the inode and it is the
responsibility of the caller of bmap to write that modified inode back to the disk and that

doesn't happen here and I think that is a bona fide bug in this code but you know maybe maybe
there's something I don't see yes there definitely is something that I didn't see at first after
looking at this code I've come to believe that it's just fine as it is in Unix we have the F seek
system call and if this is a file here you can use SC to seek to a certain point beyond the

current end of the file and then you can do a write the posit specification says that the bytes in
between here that were never actually written will be initialized or assumed to be zero so that if
later you go back and you do a read in this region here the read system call should return the zero
bytes that were supposed to be there I made the assumption that xv6 works the

same way and that you could have these uh gaps like this but in fact that's not the case in xv6
there is no fseq system call so it's impossible to do something like this you cannot start writing
beyond the end of the file in fact with xv6 writing will always start at position zero I suppose
that if you don't want to start writing at position zero you will have

to do a bunch of reads to get to whichever position you'd like to start writing at so with actually
six the blocks in the file are always allocated so let's assume that we have a file and it goes from
this point up to some size and so here's the file in blue and what this means is that all the blocks
and each one of these is supposed to be a block will be allocated and the

last block might not be fully used but all the blocks in here will be allocated and none of the
blocks Beyond this point will be allocated so that means that the read-I system call when it calls
bmap will only be reading blocks that are already in the file and that means there will never be an
update to the inode so there's no reason or no necessity to call the I update function

like there is in right as we will see in a second this uh points out that a lot of times we make
assumptions about the code that we are either reading or writing and these assumptions are often
below the level of Consciousness we have these assumptions in the back of our mind but they may not
be clearly articulated if we want to prove in a mathematically strict way that the code is correct
then

we may be required to make all of our assumptions explicit and write them down doing that proving a
program is correct and making all these assumptions explicit is an excellent way to find bugs but
with the xv6 operating system kernel we are not going to go into that level of analysis okay so
let's move on to the right eye function to write data to a file we have the

right eye function so we will write data to the file that's described by this inode and the caller
must hold a lock on that I node the data is coming from memory at the location given by this
argument source and that can either be a virtual address in the address space of the currently
running process or it can be a physical address in kernel space as determined by this argument here

we are placing the data in the file at the location given by this offset here and we'll be copying n
bytes this function will return the number of bytes successfully written and it might be less than
the requested in if there's an error of some kind so it's very similar the code is very similar to
the read eye function we start off by asking whether the offset

is beyond the end of the file and if so we return -1 in this case and we also ask whether the number
of bytes to be transferred is less than zero and again that's considered to be an error so we return
-1 and we also ask whether we will be going beyond the maximum file size so max file is the maximum
number of blocks in a file times the block size and if offset plus n is

greater than that then we've got to return an error indication the loop here is roughly the same as
we saw in read I we're going to break the transfer into a number of chunks of size m and so we start
with the total number of bytes transferred so far that's zero and we'll stop when we transferred all
n bytes and we'll ultimately return this total number and we compute the number of bytes we're

going to transfer as M and in each iteration of the loop we'll be adding m to the offset in the file
that we're writing to as well as the source address where we're getting the bytes from and we'll
increment the total number of bytes that we've written so far by m and again this is exactly the
same as in read I when you take the current offset in the file figure out what Block it's

in Call bmap to determine the block number on disk and then call B read to get that block into a
buffer 0.2 by BP then we compute M here we are limiting in if we are going past the total number of
bytes we need to write then we stop there and uh so n minus total is the number of bytes left to
right so we don't want to write any more bytes than that and we also don't

want to go beyond the end of this block so here we compute how many bytes are left from offset in
this particular block and then we use the either copy in function which will copy from this location
in memory source which is either in Virtual address space or kernel outer space as given by this
parameter here and we'll copy n bytes sorry M bytes and where will we be copying them to well

we've read in the buffer that contains the range of bytes that we are going to be writing to and so
here we compute where that is so here's the the data in the buffer and here's the offset within that
data and that's where we're moving data to and after we copy the data from memory to the buffer we
need to write this buffer back to memory sorry back to the file so here we invoke the log right

function to do that and then we release the buffer if the either copy in function has a problem it
will return -1 and we then release the buffer and break now we need to update the size field so um
here we are updating the size attribute of this file if the offset that we're at now after we end
this Loop exceeds the current size of the file then we need to grow the

file or at least make a note that the file has already grown bmap will be the function that actually
adds records or adds blocks to the file so uh write the inode back to the disk even if the size
didn't change because the loop above might have called bmap well it did call bmap and that bmap
might have added a new block to the file and therefore modified the array of block pointers so

we do call eye update here and finally we return the total number of bytes transferred next let's
take a look at the directory lookup function it's passive directory or more precisely a pointer to a
cached copy of the inode for that directory file and a pointer to a string and it looks that name up
in the directory and if it defines it it allocates a cached

version of the inode for that file and returns a pointer to that it also has the capability of
returning the offset within the directory at which it found this name and that could be useful if
for example we want to delete an entry from a directory so let me remind you what the directories
look like we have this directory entry structure that's 16 bytes in length it has two

Fields the inum field is only two bytes and it has the I number of the file and then we have 14
bytes for the name of the file if the name is shorter than 14 bytes it will be followed by a null
byte and a directory is just a file that contains an array of these so here's an example directory
some of the entries will have an eye number of zero and those are essentially unused

entries in the directory so this directory seems to have three files in it and so we have the file
name if it's shorter than 14 characters it is followed by a null byte and if it is 14 characters
then there is no null byte but in any case there's the I number that's associated with these names
for the ones that are zero we don't really care about what characters we

have for the name Okay so what do we do in this function first of all we check the file that were
passed the directory to make sure that its type is in fact a directory and if not there's a serious
problem then we're going to go through the file so offset will start at zero and it will be
incremented by the size of these directory entries so we have this local

variable called d e directory entry and that's a 16 byte value and so we're going to increment the
offset in units of 16. so here here here and we're going to end when we get to the end of this file
the size of the directory file itself and for each iteration of the loop what we're going to do is
we're going to call the read-eye function so we're going to read from this directory file okay and

where are we going to read we're going to read from this offset and we're going to read well it's 16
the directory entry is 16. we're going to read 16 bytes and we're going to place those bytes in this
d e variable here and we make sure that we've got 16 bytes and if we didn't well something's really
wrong so we panic if the eye number of this directory entry is zero then we skip then go to

the next one with a continue otherwise we compare the name that were passed okay this string here
with the name in the directory entry and see whether they are equal so at this point let me look at
this name compare okay that's a little function here that is past two strings and it uses string and
compare to compare up to 14 characters well it Compares up to the first null byte and

in any case no more than 14 bytes and so if the name is equal then we have found it okay the two
names are equal then we have uh the entry that we're looking for and so we grab the I number that's
associated with it also if this P off argument is non-null then we store the current offset of this
directory entry in the variable that's pointed to here and once we've gotten the I number for

this entry we then call iget recall that I get will uh allocate an inode in the cache of inodes and
it will initialize it with the appropriate device and I number and from then on we can lock it if we
want to but at least we've got the pointer to we've got a pointer to the inode cached in memory if
this Loop goes all the way through and ultimately reaches the size of the

directory file and still hasn't found a match then we return 0 to indicate that we haven't found
anything so next we have the directory link function which is used to add an entry to a directory
it's passed DP which is a pointer to the inode for the directory as well as the name and I number to
be added to the directory it will return 0 if everything's okay

and minus one if there's a problem the first thing it does is it calls directory lookup to determine
whether this name is already in the directory if it finds something then it will set IP to point to
the inode for that file it will call iget to do that within directory lookup if that's the case then
well that's a problem we need to return minus one but we are not interested in

the file that we've already found so we call I put to undo the I get if you will then we look for an
empty directory entry so we're going to look through the directory file starting with offset 0 and
incrementing by the size of these directory entries and we're going to stop when we get to the size
of the file after we've looked at them all and for each offset we will call reduy

to get from the directory file uh 16 bytes and we'll transfer it from the current offset within the
file to this de variable this directory entry variable here is a local variable of 16 bytes and
that's in physical memory not virtual address space so that's the flag of zero here for that and if
for some reason we're unable to transfer the data unable to transfer a full 16 bytes then

there's something dreadfully to matter and we panic um but otherwise we then check the directory
entries I number to determine whether it's free if it's zero then we found a spot in the directory
that we can use and we break with that offset otherwise we go all the way up to the end of the file
and offset will be equal to the size of the file because the uh

the size of the directories is a multiple of 16. and uh in either case we are now ready to create
the direct reentry and write it to the file so here we are copying the name into the directory entry
variable and we copy it up to 14 characters and we copy the I number into the directory entry and
then we write that directory entry so uh we're writing it here's where we're writing

uh from and this is a address in kernel space so the flag is zero and we're writing to the offset
this could be the offset of an entry that is unused or it could be an offset beyond the end of the
file and it doesn't matter which this will grow the file if necessary and so we write 16 bytes
that's the size of the directory entry if there's anything wrong we panic but otherwise we're done

and we return 0 To indicate success in order to open a file we need to be able to deal with path
names the next function we want to look at is called skip element this is purely a string processing
function and helps us parse a path name it doesn't involve inodes at all it's passed a pointer to a
string containing a path name such as this one right here and it will identify the

first file name in that path and Advance the pointer to the second file name and it will return a
pointer to the second file name it will also save the first file name so the second argument is a
pointer to a 14 byte area remember that file names can be at most 14 characters and it will save the
first component in that area if there is no file name in The Path

then it will return null okay so let's take a look at the code here one thing I want to mention is
that it ignores leading slashes and if there are multiple slashes separating the file names then it
ignores that and treats them just as one okay so here we're past a pointer to the path and a pointer
to the 14 byte area to say the first file name So It Begins by skipping past any

leading slashes and ignoring them and then if there's nothing found that is if we're positioned at
the null byte at the end of the string then there is no file name in The Path so we just returned
zero now we're positioned at the beginning of the first file name so we save a pointer to that and
in this while loop here we advance our pointer to whatever comes after that file name

so we search for the next slash or the next null character and stop when we hit something either of
those and at that point we know where the first file name begins and where it ends so we can compute
its length now we're going to move it into this name area okay that that's our second argument here
and we need to check to see whether it's greater than 14 uh is

if it's 14 or greater than 14 bytes in length then we don't need to worry about the null we just
move it into the name field and uh directory size is this 14 constant uh if it is uh 13 or fewer
characters we need to add a null character after the end of it so here we do it that way and uh
finally we advance the pointer to the next thing beyond the slashes so if

we ended pointing to a slash then we advance to the second file name or the end of the string and uh
return the pointer the results next I want to look at the name I function this is sort of the
culmination of all the functions we've been looking at name I is passed a pointer to a path and it
locates that file on disk and returns a pointer to the inode for that

file and if there's any problem such as the file is not found or the path name is no good then it
returns no the actual work is done by this name X function there's another function name I parent
which is very similar to name I except it stops one level earlier and both the name I and name my
parent call namex to do the work so let's take a look at name I and name I parent here

okay we're going so uh name I calls namex and name my parent calls name X and the only difference is
they pass a flag to say which is involved here now name X requires a 14 byte field a 14 byte memory
area in which to save each file name in The Path and so uh in the case of name I parent we're trying
to extract that so it's passed in but for name I we have a local variable that's

the 14 byte field and we're passing in the address of that variable okay so let's uh take a look at
name X it is past a pointer to the path and then this flag name I parent if this flag is zero then
we will return the inode for the parent okay and copy the final file name into the name area okay
otherwise we will open that file directly okay so this name X is only called from

name I and name my parent so I think the way to look at this thing is to imagine we're calling it
from name I first and I'll look at the second case uh a bit later but so imagine that this flag is
zero right and we have a 14 byte area that we can use within this name X function so here we start
by seeing whether the path begins with a slash if it does then we're we need to start with the root

directory okay so here we uh have the device on which the file system is kept and the inode number
which is one for the root directory and we just get a pointer to the inode for that here and let's
say we've got just a slash and nothing else then uh this while okay so here's a while loop that
processes each element in the path name but let's say that there's nothing then we immediately

exit the loop and let's we're assuming that this flag is zero so we skip this and we would just
return a pointer to the I node for the root doesn't lock it but it does add it to the cache so this
flag is called somewhat confusingly name I parent and uh so the flag is called name I parent and
we're going to assume that that is false when we look at this code here

now if it doesn't start with a slash then we need to begin processing the path from the current
working directory so the structure for this particular process that we're running in Has a Field
called current working directory and that is a pointer to an inode so that's the current working
directory and we need to use that we haven't seen this idupe uh function before but basically all
this

does is just increment the reference count for this I node in fact let me just stop and show you the
code for that here here's the idupe function pass the pointer to an inode and it acquires the
spinlock for the cache increments the reference count and then releases the lock okay so uh that's
what idoop is doing right here and uh I get will increase the reference

count for the root directory so regardless of whether we do this or we do this we've got an I know
that's been added to the cache and we've incremented the reference count for that so that's what
will be true when we return down here okay so whether we start with a slash or not we are ready to
start looking at the path so we're going to call the skip element function that we just talked

about to locate the first file name in The Path and to advance the path pointer to the next element
so that's what happens here okay if there is no next element then we are done okay and we exit the
loop but uh otherwise we need to look in the directory uh that we're pointed to at the beginning of
this for the file that has this name so here we are identifying

the name and and and saving it in this name variable and at that point the next thing we're going to
do is call directory lookup but before we do that we need to lock that inode okay so we lock it here
and then we call directory lookup before we do that we make sure that the file that we are thinking
as a directory is in fact a directory so we check its type and if it's not a directory

something is the matter and so we are going to abort and return a zero to indicate that we had
problems but remember that we've got the uh the directory it has been had its reference count
increased and we've locked it so we need to undo both of those things so we unlock it and decrease
the reference count and before we return now I said let's just take a look at the

case where this flag this name I parent flag is false so let's skip that for now and now we're ready
to do the directory lookup so we've got the first or the I should say the next file name in the path
in this string here and this is our inode of the directory and so we're going to do a directory
lookup so remember that this is the offset into the directory this is a pointer to a

place where we can save the offset if we want to we're going to pass in null because we don't care
where in the directory this name is found so we don't need that information we pass in a null rather
than the address of an of a variable in which to store it so um here is going to this is going to
return a pointer to the inode uh for this file so that's what the next I node

is and we'll see here we're going to advance to that okay so if everything goes well then we return
a pointer to an I node but if there's a problem we return null and again we've got this directory
that we're looking into locked and we've increased its reference count so we need to undo those two
things and return a null indication to indicate there was a problem okay we couldn't

find the file so if there's a problem with a path name and we can't find one of the files in the
path name we considered an error in return null but assuming everything's okay then we're done with
this directory we now have uh the next thing in the path name could be the file that we're looking
for that is the thing at the end of the path name or it could be another

directory in other words we could be looking at the bin directory or we could be down here looking
at the file that we want to get at but in any case we are going to advance to that to that so we uh
unlock the directory and then move to the next thing the next file in the path and then we loop back
up okay if that was it okay if that was the last thing in the past then this

thing is going to return zero and we'll exit and um we'll exit the loop here and then return the
pointer to the file that we found otherwise the while loop will continue and look at the next
element in the path name okay so now let's take a look at the name I parent function here we're
passing in one so in this case we just want to stop prematurely so again we start with something
let's

say we start with the root directory and we set IP 2.2 the the next directory or the next thing in
the path and let's say there's nothing else okay this returns zero well there is no second thing and
that's a problem so um if this thing is true which it will be for a name I parent situation then
we've got a problem we need to return zero it was not at least one file name

in The Path so uh we uh get we unlock the um sorry we haven't locked it because the lock occurs in
the loop okay um we uh do not have IP locked at this point so we don't need to unlock it we just
need to decrement its reference count before we return now okay in the loop what do we see um it's
the same thing we're going through it and we get the next thing in

the path okay we look in the directory it's that is pointed to by IP and identify the next file and
if we are wanting to stop early and we've come to the end of the path all right we've Advanced path
to a point to the thing after name and if there is nothing after name then we can stop okay we're
done okay we've identified the last thing in the file and IP at this point points to

the directory so we unlock the directory and return it okay I think that's it for all the functions
in the file fs.c I will see you in the next video
