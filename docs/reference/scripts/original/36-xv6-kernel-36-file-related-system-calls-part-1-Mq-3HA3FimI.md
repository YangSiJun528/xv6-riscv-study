# xv6 Kernel-36: File-Related System Calls-Part 1

| Field | Value |
| --- | --- |
| Video ID | `Mq-3HA3FimI` |
| URL | <https://www.youtube.com/watch?v=Mq-3HA3FimI> |
| Source | `docs/reference/scripts/original-raw/36-xv6_Kernel-36_-_File-Related_System_Calls-Part_1-Mq-3HA3FimI.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this and the next video I'll be
going over the code in the functions that handle the file related system calls these functions are
coming from sysfile.c and I will also cover file read and file write which come from file.c there's
quite a bit of code here so I'm breaking it into two different videos

this is part one and in this video I'll be covering the following system calls read write close link
unlink f-stat change directory and dupe in part two I will cover the open system call make node make
directory and pipe the exec system call is a little bit complicated so I'll be covering that in a
subsequent video ok let's get started so let's take a look at the read and the

write system call each of these takes three arguments and the user mode code will begin by placing
these arguments into registers a0 a1 a2 and so on it will also place a code number in register a7
and that code number is used by the kernel to determine which system call is wanted then the user
mode code will execute the system call instruction in RISC-V that instruction is named

e-call and at that point the kernel will get control and it will begin by saving the user registers
someplace then it will look at the value in a7 and determine which system call is involved and if
it's a read system call the kernel will then invoke the sys read function there's one of these cis
functions for every system call the cis read will begin by locating the arguments and grabbing them

from wherever they were stored initially so we have these functions rgfd argant and arg address and
each is passed a an argument the first argument tells which argument we're interested in so a0 a1
and a2 these things aren't done in order really uh so this is the first argument the second argument
and the third third argument the argant function will go to register

a2 in this case grab a 32-bit value and store it well here's the address of where to store it and
that's the local variable in number of bytes the arg address function will go to register where
register a1 is stored and grab a 64-bit value and store it in local variable p and finally this arg
fd will obtain the value that was in a0 at the time of the system call

and interpret it as a file descriptor it will check to make sure it's a legal file descriptor and
return minus one if there's a problem and then it will get a pointer to the file structure and it
will store that pointer in f it can also uh obtain the file descriptor number itself uh but that's
not needed so this address here is just null if there's a problem with the arguments

we return minus one but otherwise we're going to call this function file read to actually do the
work passing it a pointer to the file structure as well as the virtual address and the number of
bytes and file file read will return the number of bytes transferred or minus one if there's a
problem so before I look at file read I want to just look at the cis write function which is exactly

identical right it gets the arguments here except it calls file write instead of file read okay so
let's take a look at file read the function file read is coming from file.c and as I said it's
passed a pointer to the file structure as well as the virtual address and the number of bytes so it
checks that file structure and looks at the readable flag to make sure

that this file descriptor describes a file that is actually readable and if not there's a problem it
returns -1 and that would then get returned to the user mode code otherwise it looks at the type if
it's a pipe device or inode if it's a pipe it's going to call the pipe read function which I covered
in an earlier video and it's passed a pointer to the pipe

structure and it will perform the read and store the bytes in this virtual address and return the
number of bytes successfully read from the pipe or possibly minus one and then that will get
returned if the file structure is a device then we look at the major number in the file structure
that's the device number and we make sure it's uh legal and we also look into this device switch

array we use it as an index into that array and remember that that array contains a structure that
has two fields both of those point two functions so we're gonna be using the read function so we
look at that field make sure it's not null here if there are any problems we return -1 but otherwise
we use the major number as an index into the array get the read

value which is a pointer to some function and then we invoke that function so that will be the
console read operation we passed a virtual address and a 1 to indicate that this is a virtual and
not physical address as well with the number of bytes finally if it's an inode then we will use the
read I function which I've also covered that's passed a pointer to the inode as

well as a address and a flag which indicates that this happens to be a virtual address as well as
where in the file we want to begin the reading and so we go to the file sorry we go to the I sorry
we go to the file structure and we get the current offset and the number of bytes if anything goes
wrong we return a minus one but otherwise we return the number of bytes red and if we

read any bytes this was positive then we need to increment the offset that's stored in the file
structure and we do that here now read I expects the inode to be locked so we need to lock it first
and then unlock it after we're done with it if it's anything else then we panic but in any case we
return our number of bytes that we've read now I want to take a look at the file

write function it's used for the write system call and this function is very similar to the file
read function that we just looked at it's passed a pointer to a file structure as well as the
virtual address and the number of bytes begins by checking the file structure to make sure that this
file can be written to and returns -1 if there's a problem and then it looks at the type to

determine whether this file is a pipe a device or a directory or regular file and if it's a pipe it
calls pipe right previously we called pipe read here and that will return the number of bytes
transferred or minus one if there's a problem and then at the bottom of the state but we return that
value if it's a device similarly to file read we check the major number to make sure it's legal we

look into this device switch array using that as an index and that gives us a structure that has
these two fields read and write we make sure the right field is not null and if everything's okay
then we're going to use that value okay so we index into the array and get the right field that's a
pointer to a function we're going to call that function so we're passing it

an address an indication that that's a virtual address and the number of bytes and again returns the
number of bytes transferred and we return that now in the third case it's an fdi node for the file
type which means it's a directory file or regular file we might have a very large right and these
rights are going to have to go into transactions previously the read

did not go into a transaction because if it's uh interrupted by a system crash for example it's no
no problem the disk is still consistent but here we are doing a write operation so we need to
include all of the writing in a transaction so we'll see a begin up and an end down here that
transaction is limited it must be limited to a certain number of blocks we can't

write too much otherwise we might fill up the log file and that could cause problems so we need to
break the right into a series of chunks and the first thing we do is we compute the size the maximum
size of any chunk so that computation happens here so let's just read this write a few blocks at a
time to avoid exceeding the maximum log transaction size include and

we're going to be writing the inode possibly up writing an indirect block and the actual blocks that
are involved and the right uh the blocks the series of bytes that we're writing may extend over a
block boundary so we're going to add two for that as well and I'm not quite a 100 comfortable with
this computation here but it is what it is and uh don't want to bog down on that I do want to say

this really the comment says this really belongs lower down since right eye might be writing to a
device like the console but that's not really what right eye does right eye only writes to files on
the disk so we compute the maximum chunk size and in this while loop here okay uh what we're going
to do is uh break the entire sequence of bytes that we need to write

into a number of chunks so n is the number of bytes to be written and n one will be the size of this
particular chunk and I will be the number of bytes that we've written so far so we're starting I out
at zero and we just keep looping until we have written a full n bytes and here we see how many bytes
we have left to write so we compute n1 but we don't want to write any more than

maximum number of bytes so we cut in one off at max if it exceeds maximum and then we're going to
write that chunk that will go into a transaction so here's the begin up and the end up and we begin
by locking the inode here and then unlocking it here and then we call right eye to perform this
particular chunk to write this particular chunk here's the inode that we're writing it

to here's the virtual address where we are getting it and here's a one to indicate that that's a
virtual address and here is the offset within the file that we are writing to and the number of
bytes that this particular write will be if everything's okay it returns the number of bytes written
and if that's greater than zero we need to increment the offset and

then we see whether we've got a problem if we didn't write all n one bytes then um the r the number
that we did write will be less and so we are done we need to break out of this loop something went
wrong um and so we break out on the loop otherwise we increment I and keep going and then we
determine whether we wrote all of the bytes did we write all n bytes if so we

return that number otherwise we return to minus one next let's take a look at the closed system call
which is passed a file descriptor so here's the sys function for the closed system call and it
begins by obtaining its argument arg fd is used to get the argument in a register a0 which is the
first argument that's expected to be a file descriptor and it will store the file descriptor

integer itself in local variable fd and a pointer to the file structure in f if everything's okay
then it will go into the proc structure and go into the o file array and no out the entry that's
indicated by fd and then we'll call file close to do the work I discussed file close in a previous
video but let's take a look at what's going on here so here's a process

and here's the proc structure for that process so the first thing sys close will do is go to the
particular entry and it will change it to null and then it will call file close and file close will
decrement the reference count and if the reference count goes to zero then it will look at the ip
and the pipe pointers if it points to an inode uh then it will go into that inode

decrement the reference count and if that goes to zero then it will close out the I node if it
contains a pipe pointer then it will follow that and it will look to see whether this is the last
pointer if the other pointer has been shut down already and it will free the pipe structure but if
the reference count doesn't go all the way down to zero then files will not do anything

else okay now let's take a look at the dupe system call it's past the file descriptor of a file
that's already open and it will allocate a new file descriptor for this file and return that new
file descriptor or minus one if there are no more slots left so uh before we look at the sysdup
function let's look at fb alec if the alec has passed a pointer to a file structure that's the file
that's

already open and it goes to the proc structure and then in this loop it runs through the o file
array from zero to the end looking for an entry that is currently not in use and if it finds one
then it will store a pointer to this file structure in that entry and return the file descriptor
that is the number the small integer that indicates uh which entry was used

and if there are no more slots then it returns minus one so looking at the picture uh we see that
it's going to go through the array until it finds a null and then it's going to store a pointer to
some file structure and return whichever index it stored it into now we can look at cis dupe it's
passed a single argument and argh fd goes and gets the first argument

and that should be a file descriptor to an open file so it returns or saves into f a pointer to the
file structure and if that's okay then it calls fd alec and that will find an empty slot in the
array and return the index of that empty slot as well as filling it in and at this point we call
file dupe file duke was covered in an earlier video but essentially what it's going to do

it's passed a pointer to a file structure and it will lock that structure increment the reference
count and then unlock it in return and then finally sysdup will return the file descriptor that was
allocated now one thing about unix that's very important and that is that uh the user has this idea
that these file descriptors are allocated in some order and zero one zero is used for standard n

one for standard out two for uh standard error and that we rely on the fact that dupe will fill in
uh the first empty slot in this ordered array here and that's used by the shell program that is
relied on by the shell program next let's look at the f-stat function it's passed the file
descriptor of an open file and a virtual address and it saves some information about that file

at that address in the user's address space I already covered the sys stat function but I'll just
quickly review it here takes two arguments it uses arg fd to get a pointer to the file structure for
the open file and it obtains the virtual address here as the second argument and then it calls the
file stat function which will copy information about this particular file

to the virtual address given here next let's take a look at the change directory system call every
process has this idea of a current working directory and from time to time we can use the system
call to change that directory it's passed a string which is a path name that describes a file which
better exist and better be a directory and if so it will change the current working

directory to that file so in this picture I showed the o file array I did not show the current
working directory field but it's there and it points not to a file structure but directly to an
inode structure so now let's take a look at the function sys change directory that does this system
call first it gets the argument the argument is a pointer to a string in the user's

virtual address space and the arg string function will retrieve that string from the user space 0
indicates that it's the first argument that we're interested in there is only one argument path is a
local variable here and we're going to move care the string up to the null byte uh into this array
here and we'll stop at the maximum length if we reach that so the second thing we do is we invoke

this name I function we covered that earlier but basically it's passed a string which is a path name
and it will open that file and allocate an inode and return a pointer to that inode so ip is a
pointer to an inode and if that's successful then we keep going but if there's either a problem with
a string or a problem finding that file then we will immediately return minus one

now you can see that I've drawn a line between this begin op and this end off and also between this
lock and unlock sometimes when we have a complex series of nested loops or if statements uh using
lines like this can help match the braces and so here I'm doing it to make sure or to show how these
things are matched up and to make sure they are in fact matched

so the name I function will increment the reference count for the I node okay so that was done in
the I get function and that's part of name I so if this thing is successful then the reference count
for the inode is incremented and we need to ultimately do an I put such as this down here to match
that but if there's a problem then it won't be incremented so then when we get

ready to return here we're going to jump out and we can see that we're crossing this single line
here so we need to take care of that so there's the end up that matches with that begin up but
assuming everything's okay then we need to check that file and make sure that it is a directory file
before we look at any field in that inode we need to lock the inode so here

we're locking it and checking the type field to make sure that it's okay and if it's not okay then
well we have done the get within the name I function and we've done the lock and we've also got the
begin up sort of open so we need to uh undo all of that so we unlock put and in the op and return to
minus one but assuming that it is a directory then everything's okay

and we're going to proceed and return zero here now we've got an existing current working directory
and that field will point to an inode for some other directory and we have incremented the reference
count for that inode to keep it from being deleted and so we're done with that now we no longer are
going to point to it so we need to do the put function for that so here we're

sort of releasing our pointer and uh this one we've incremented the pointer so in some sense we've
incremented one pointer and decremented the other uh reference count we've incremented one reference
count and decremented the other reference count so in some ways these are sort of matched but in any
case um we are now done with our transaction and the final thing we

do is store a pointer to the inode in the current working directory field of the proc structure and
then we return zero next let's take a look at the link system call it's passed pointers to two
strings the first is the path name of an existing file and what this function is going to do is it's
going to add an entry into a directory so that the new path name will be valid and will refer

to the same file if everything's okay it will return 0 and it will return -1 if there are any
problems so here is the syslink function and it begins by dealing with the arguments we have two
arrays here new and old in which we store these path name strings we need to copy them from the
user's virtual address space into these arrays first and we do that with the call to the arg

string function so here we're calling it for the first argument and here for the second argument and
we copy the first path name into the old array up to the null byte or up to max path characters and
here we are copying the second argument string into the new array up to the null byte uh with no
more than max path bytes copied if there's any problem here we

immediately return -1 now we're going to be updating the file system so we need to put this into a
transaction this begin op is where the transaction begins and down here is the matching end up and
we return zero to indicate that everything's okay so you can see that I'm using this line system
where I match I get with I put I lock with I unlock and I begin with

sorry begin off with endop the next thing we do is we take the old path name and we invoke this name
I function that I talked about earlier this is going to go out to the file system and locate this
file and then it's going to allocate an inode structure in the inode cache and if successful it will
return a pointer to that inode structure and increment the reference count

but if there's a problem it will return minus one so we're inside this begin up we mean we need to
immediately end the transaction and return -1 but if it is successful then we will have increment of
the reference count for that inode which is what iget does and so that's why this line is here the
next thing we're going to do is check the type of the old file the

existing file to make sure that it's not a directory the directories must be organized in a tree so
we can't add additional links to a directory and that's why we're checking here if it is a directory
then we're going to abort but before we check the type field we need to lock that inode so we lock
it here check it if there's a problem we immediately unlock it and then

call I put to undo the increment of the reference count so we decrement the reference count and then
we end the transaction so we're essentially breaking out going through all three of these lines here
and then we return -1 but assuming that it's not a directory then things are looking good so we need
to increment the n-link field in the inode that's the number of hard links

that point to this file and since we just updated the inode we need to write that back to the disk
so here we're calling I update and then we unlock this inode this won't actually get written to the
disk of course until we end the transaction down here but for now we're done with it so we update
the disk and unlock this inode next we need to process the new path

name so here's an example new path name it consists of a directory followed by a name at the very
end and we talked about name I parent before but it's past this path name what it's going to do is
locate this directory and allocate an inode for it and return the inode or return a pointer to the
inode as dp and so it's incremented the reference count for the inode that describes the

directory and it's also going to isolate the final name in this path name and it's going to store it
in this variable here name is an array here that's limited to well directory size is 14 bytes so it
will store the final item in this path name into this name variable if there's a problem like the
string is all messed up and empty or this directory doesn't exist then this thing

will return -1 and we will jump out and go to a label called bad if there is a problem we will not
increment the reference count for this dp inode so that's why I'm skipping this line here but we
still have the I the reference count for the old file and the begin op sort of pending so that's why
they're I'm crossing two lines here now let's look at this bad

label right now oops here it is and so I'm showing the two lines here that we have to cross to come
into this label and we need to decrement the number of hard lengths we previously incremented it on
the assumption that everything was going to work out but we've got a problem now so we need to
decrement the end link field and in order to do that we need to hold

a lock on that inode so here we lock the inode and here we uh decrement it and here we update it on
disk and then at this point we unlock it and we also decrement the reference count for this existing
file and finally we in the transaction and return -1 but assuming that we did not take that jump and
everything's still looking good we now need to modify this directory to

add this name so we're going to lock the directory dp and then we are going to check to make sure
that this directory is located on the same device that the existing file is located on remember that
hard links cannot cross from one file system to another they have to be on the same device and so
that's this check right here and if there's a problem we're going to go to

bad but if that's okay then we call the directory link function which I discussed before it's passed
a pointer to an inode which describes a directory like dp here and it's passed a name array as well
as an inode number an I number and so this will add a new entry to this directory okay and if
everything's okay it'll return zero otherwise it will return minus one if anything went wrong with

that then we need to uh go to bad okay we uh will need we will need to deal with the lock that we
have on dp as well as the fact that we've incremented the reference count for this inode so we
unlock and put here and then we go to bad and so we still have these two things to do we need to uh
do the matching I put and end up which we saw happens in bad we do the put here and the end up

okay but if that didn't happen and the directory link returned zero then everything was okay well
we're done now we can unlock the directory and here we incremented the reference count for that
inode so we do the corresponding put and then we do the corresponding put for the old file and end
this transaction and finally we return 0.

next let's look at the unlink system call it's passed a pointer to a string which is a path name and
it will remove a hard link to that file return 0 if everything's okay and -1 if there's a problem
before looking at the code of sys unlink let's look at the code for is directory empty it's a little
helper function and it's going to be past the inode for a directory and it's going to go through

that directory and ask whether it contains anything so you see it loops through this directory in
units of well d e is the is a directory entry and remember that a directory entry contains a 14 byte
file name and a two byte inode number so the size of this thing is 16.

so it's in going to increment in units of 16 but it's not starting at zero instead we're going to
skip the entry for dot and dot dot every directory must contain these two so instead of starting at
and going 0 16 32 48 and so on we're going to start at 32 and increment in units of 16. so that's
what's going on here and we end when we get to the size of the directory and for each of these

offsets we're going to read from the directory so we're providing the pointer to the inode for the
directory and we are going to move from this offset in the file 16 bytes and we're going to place
them into this local variable d e and this is a variable in kernel space and this flag here
indicates that it is a physical address and not an address in virtual memory

and we should return the number of bytes that we've asked for so we're reading 16 bytes and we
should return 16 and if that's not the case then is the matter and we panic but otherwise we look
into the directory entry that we just read here and ask is the I number zero unused entries will
have an I number of zero and um if it's not zero then there is something in the directory besides
the

dot and dot dot entries so we return false is directly empty well it's false that's not true if we
find something but if we go all the way through and find nothing we return true here is the cis
unlink function it's kind of long you can see that we begin a transaction here and we go down here
and continuing on to the next page we ultimately in the transaction and

return zero if everything's okay we've also got a bad label where we return minus one so [Music]
let's begin with the argument we're passed a pointer to a string which is the path name and we call
arg string to copy from the user's virtual address space into a local variable path so that's what
happens here and if there's any problem with that we immediately return -1 and then we begin

the transaction we need to be running a transaction because we're going to be updating the disk when
we remove the file from a directory then we begin by calling name I parent name I parent which I
covered earlier has passed a path name such as this right here and it's going to identify the final
component in the path name uh xxx the file name that we're removing

and then it's going to identify the directory that it's located in and it's going to return dp the
directory pointer which is a pointer to an inode structure for this directory if it's in this form
then it will return a pointer to the root directory and in this form it'll return a pointer to the
current working directory so now dp points to an inode for the directory and the name

field is a local variable here and we are copying the final component in this path name into this
name array here if something goes wrong then it will return dp equal to null and we immediately end
this transaction and return minus one but assuming uh that this directory exists then we can go
ahead and lock it the next thing we do is we check to see whether we're trying to remove the dot

or dot dot entry these cannot be removed from a directory with this function so we do a compare to
see whether name happens to be dot or dot dot and if either of these is true then we go to bad so
we've started a transaction we've done the I get function to increment the reference count for the
inode and we've locked the inode so let's go to bat and see what happens

there so here's bad we are going to unlock the directory do an I put which will decrement the
reference count and then we're going to end the transaction and return minus one so assuming that
it's not dot or dot dot we can proceed and now we're going to call directory lookup directory lookup
is passed a pointer to a vi node for some directory such as here and uh a name

and we're going to look this name up in the directory and if we find it we're going to allocate an
inode for that file and return a pointer to that inode we're also going to store the offset into
this directory file of where that directory entry occurred so we have a local variable offset and
we're going to save the offset within the directory file of this entry for this particular name

if there's a problem and we couldn't find the name in the directory then we will go to bad okay we
will not increment the reference count for ip uh but still we have to unlock the directory and
decrement the reference account for the directory and in the transaction so that's what happens
there but assuming that the name is in the directory we have incremented the

reference count for this inode now we need to lock it we're going to be uh modifying its end link
first of all we're going to be looking at its end link so we need to lock it before that we do a
quick check to make sure that the end link is at least one I mean we can't uh we would have some
kind of a consistency error otherwise and so we panic if it's zero or smaller

then we look at the type of the file that we're removing if we are removing a file you know xxx and
that is a actually a directory itself then it better be empty we can only remove it if it's an empty
file uh an empty directory so here we're calling the is directory empty function so if it is a
directory and it is not empty then we've got a problem so at this

point we've locked ip an increment of the reference count so we need to unlock and put the ip and
then we can go to bad to take care of the other stuff okay so that's assuming that error hasn't
happened we don't have any more error conditions and we can keep on going so we have a local
variable d e which is a directory entry right 16 bytes 14 bytes for the name and two bytes for the

I number what we're going to do is set it all to zero including the I number in particular and so
we've got the directory entry being set to zero and now we're going to write that directory entry
into the directory so here's the right eye function we're passing it the inode of the directory file
and we are writing from this directory entry that's all zeros that's in kernel space so this flag
says

it's kernel space and not a virtual address and we're writing to the offset that's the offset we
saved with the call to directory lookup and we are writing well directory entries are 16 bytes so
we're writing 16 bytes and this thing will return the number of bytes that we wrote it better be 16
I mean the directory entry is there we found it earlier and we've

had the thing locked since then so um it better still be there so we panic if there's a if it's not
and then uh let me just skip this for a second now we're done with the directory we're going to
unlock it and decrement the reference count and then we're going to go to the inode for the file
itself and decrement the number of hard links to it that will involve an update to the disk

so we need to um do an unl an eye update for this particular inode here and then finally we can
unlock this file and decrement the reference count and we're done we can end the operation in the
transaction and return zero but I skipped over this part and that I want to try to describe here so
let's imagine that uh we're trying to remove the bin entry from this directory so here's

a directory slash user let's say and it's got only one file in it and that file is bin and so we
have a directory entry 14 bytes plus two byte inode number which points to some other file and that
other file is a directory file okay it happens to be a directory um and it's pointed to by ip or the
inode for this file is pointed to by ip so uh with directories we have the dot

and the dot dot going on so the dot entry always contains the inode uh the I number of the file
itself okay so that's that's the dot entry and the dot dot contains the inode of the parent so this
is a tree we have only a single parent for directories so we can have uh a parent pointer and that's
the dot dot pointer now the question is how many links are there to this particular

directory well of course we have the one link that is for the directory in which it lives okay uh we
don't count this pointer here but we do count this pointer so we've got one directory we could have
you know other directories as well in this directory but it looks like we just got this one bin and
so we've got a back pointer from bin and so that's the second hard link to

this directory so in link will be two if we had something besides bin we might have actually
additional pointers from others but what this is showing is that the number of hard lengths for a
directory includes the hard link from the parent in the directory tree as well as one hard link from
each of the children directories in the directory tree so we've got to decrement

okay we're eliminating this file so we're removing bin from this directory I already showed where we
decrement the end link for ip but we also have to decrement the n-link for dp so we decrement the in
link for ip here and we increment or we decrement the in link for dp here and here we update the
directory if if that's the case we have to update this inode on disk as well so we have to call

I update here so after we're done with all that we have eliminated both this hard link and this hard
link and adjusted this in link pointer and this one because we've also eliminated that one and then
we can return zero that everything worked out okay there's a subtle point that I wanted to mention
about this code whenever you're locking multiple things it can be very critical what order you

lock them in if you lock them in the wrong order there might be a possibility of deadlock and I've
drawn my lines to match the locks with the unlocks to make sure that everything we lock gets
unlocked but it doesn't really matter which order you unlock things in generally speaking as long as
you get around to unlocking everything that you've logged so here I'm showing the unlocks reversed
in

order okay and the code that I showed before I wanted to I kind of messed up my lines we're locking
dp here and then within that we're locking ip but and then at the bottom of this function we are
unlocking dp and then we're unlocking ip so actually we have uh this situation here and not this
situation so really I suppose I ought to draw these lines crossing or something

but I did want to mention that okay that's enough code to put in one video in the next video I will
continue going over the functions in sysfile.c I will see you in part two
