# xv6 Kernel-37: File-Related System Calls-Part 2

| Field | Value |
| --- | --- |
| Video ID | `TYmHsRS_JWI` |
| URL | <https://www.youtube.com/watch?v=TYmHsRS_JWI> |
| Source | `docs/reference/scripts/original-raw/37-xv6_Kernel-37_-_File-Related_System_Calls-Part_2-TYmHsRS_JWI.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel there are a number of functions
that implement the file related system calls and these are coming out of the file sysfile dot c
there are quite a few functions and in the previous video that is part one I covered some of them
and in this video I will continue and in particular I will be covering the following system calls

open make node make directory and pipe the exact system call is a bit complicated and I will handle
that in a subsequent video okay let's continue with these functions now let's look at the create
function this function is used to create a file and add it to some directory in the file system it's
passed a type code that indicates what kind of file we want to create

either a directory file a regular file or a device file if we're creating a device file we need the
major and the minor numbers so these will be something for other kinds of files for directory files
and regular files these will be passed in as zero and not used we're given the path name here as an
argument and that tells not only uh what directory we're adding it to but

the name as well so for example with this file name we can see that we're adding a file called xxx
to some directory in this example user bin on success we will return a pointer to the newly created
file or more precisely to an inode for the newly created file and that inode will be locked as well
if there's a problem we'll return null create is called from exactly three

places it's called from the open system call it's called from the make node system call and it's
called from make directory and for the uh make node system call it's for adding a device file and so
type will be device for make directory we're creating a directory so type will be directory and
finally the open system call has the option to open a file and create it if

it doesn't exist so when called from open the type will be file so we passed in this path and the
first thing we do is call name I parent to parse this path name so it will identify the final thing
in the path name and store it in this variable name so this local variable name will contain xxx and
then the prefix of the path name will be a directory and that directory

uh will be open and the reference count will for that inode will be incremented and dp will be set
to a pointer to the inode if everything's okay then we'll keep going but if we had a problem then
this will return null and will immediately return null so we're going to be modifying this directory
we begin by locking it the next thing we do is we look up xxx

in this directory to see if it's already there okay if it's already there this will return and a
pointer to the inode for that file and we'll set ip and it will be something that's non-zero so we
do this code if the file already exists and if this thing returns no then we've uh we don't have a
file so we'll go on here to create the file but it might be okay for us to have the

file in particular if we are called from open then it would be the case that we've had the flag to
create the file if it doesn't exist and if that's the case we might have an existing regular file
okay so we check the type field of the file that does exist and if it's a regular file well that's
okay it already exists we'll go with that or if it's a device file that too is

okay so we'll go with that and in that case we'll return ip uh but if we were called to make a
device file or make a directory then this is going to be false and we'll return null but if we
return if we find that it's okay we will return ip first we have to lock it before we check the type
field and then we will return with it locked and um before we return we will unlock the

directory okay and we'll return either way here and so uh if it's not okay that is if we're called
from the make node system call or the make directory system call and we've already got something
then um uh we need to return no so we're going to both unlock the directory and unlock the file that
already exists and just return null okay so we've got the directory file locked at this

point and we've determined that the file doesn't exist so at this point we call I alec I alec will
create we'll allocate an inode and we'll increment the reference count and we'll set its type to
whatever were passed in here and so now we've got the inode in existence and assuming that that
works successfully and doesn't return no we keep on going now we lock the file that we've just

created because we're going to access some fields first of all we've uh we're going to set its hard
link count to one and we're going to fill in the major and minor numbers which will be important if
it happens to be a device file that we just created then we're going to see whether the thing that
we're creating is a directory file if we have just created a directory file

we need to add entries into the directory for the dot and dot dot files so that's what we're going
to do down here but remember that the hard link count in a directory situation will include the hard
link from a child to its parent so here we are increasing the count of hard links to the directory
that contains xxx and that's what happens here and that's why

we do that and then since we've modified the directory that we're adding to we update that and then
we are going to add entries for dot and dot dot so we call directory link first for dot providing
the I number of the file that we just created and then for dot dot providing the I number for the
directory itself and if something goes wrong there and we panic but otherwise we keep going

now at this point we need to add xxx to the directory itself so here we're calling directory link
yet again passing in a pointer or to the inode for the directory that we're adding xxx2 as well as
the name xxx and the I number of the file that we just created so presumably that will return
something and if there is a problem then it will return negative one and uh we can

panic but otherwise uh we're pretty much done we still got the directory itself locked so we need to
unlock it and we also incremented the reference count when we called name I parent so we need to
undo that as well with a put so we do that here and then we return a pointer to the newly created
file and it will be locked at this point now let's look at the make directory

system call it's passed a path name and it's just going to create a directory file so here's the
code for sys make directory and basically it's just going to call create which we looked at so it's
pretty straightforward we begin by putting everything in a transaction so we have a begin up and an
end up and if there's a problem we'll return minus one otherwise we'll return zero to

indicate that everything's okay we begin by looking at the first argument and it is the path name so
we're going to call argument string to copy from the user's virtual address space into this local
variable path the path name and then we're going to call create providing it that path name and the
type code will indicate that we want to create a directory file

and we pass in the major and minor numbers as zero and if that works it will set ip to be a pointer
to the inode for the newly created directory file if there's a problem it will return null and we
immediately end the transaction and return one but if it's exceeded well we don't really need to do
anything more so we have locked this uh inode so we need to unlock it and then decrement the

reference count for the inode and so we do that here in our transaction and then return 0.

now let's take a look at the make node system call it's very similar it's passed a path name and
we'll create a file but it will create a device file with the given major and minor numbers
returning zero if everything's okay and minus one otherwise so here is the code for make node and
it's really quite similar we have a transaction and if everything's okay we return

zero otherwise we return minus one we have three arguments uh the first one is the path name so we
call argument string to grab that and put that string move it from the user's virtual address space
into this local variable path and then we get the next argument and move that into the local
variable major and then we call argument argent to get the last argument as a number an integer

and move it into the minor variable now that we've got those we can just call create and provided
the path and this time the type is device and we provided the given major and minor numbers and
again if there's a problem uh it will return null in which case we terminate our transaction and
return -1 but if everything's okay it will return a pointer to the inode for the

newly created file and that file is locked so we need to unlock it and decrease the reference count
and in the transaction and return 0 for success we are now in a position to look at the code for the
open system call remember that in unix open is passed a path name that's the name of the file to be
opened as well as an integer it's called the flags value and that value

has some bits defined in it that tell what to do in certain situations and the system call returns
the file descriptor that's the small integer that we'll be using in system calls like read write
close and so on now this flags value has this definition here for xv6 in real unix this is a little
bit more complicated so you can look at the documentation for that if you're concerned about what's

going on in production operating systems but here's what xv6 does it has the flag word defined with
several bits if this bit is one then the file should be opened right only so write only is that bit
if this bit is one then the file should be open for reading and writing and that's this bit and if
this bit is set we should create the file if it doesn't exist and if this bit is set we should

truncate the file to size 0 if it exists and finally we have this read only which is no bit set at
all okay now let's try to walk through the code for the sys open function which implements the open
system call it will be returning the file descriptor integer it begins by looking at its arguments
the function calls arg string to move the path name which is the first argument to

the variable path here which is a local variable from the user's virtual address space and then it
calls argent for the second argument and it stores the flag integer in this local variable called o
mode or open mode if there's anything wrong we immediately return minus one otherwise we start a
transaction we're going to possibly be creating a file so we will be modifying the disk so we need

to have a transaction going now the next thing we have is an if statement we look at the create bit
in the flags value and if that is set we do this we call create and if it's not set then we call
name I create will be past the path name and it will try to open the file if it already exists and
if it doesn't exist it will create it and it will create it as a regular

file if it creates it or opens it successfully then it will set ip to point to an inode representing
that file and it will not only increment the reference account for that inode but it will also lock
that inode so I'm drawing two lines here if there's a problem though it will return null and we
immediately in the transaction and return minus one otherwise if the create bit is not set

then the file had better exist so we called name I and provided the path name and it will find that
file and allocate an inode in memory and return a pointer to that inode with the reference count
incremented so that's why I draw one line here if there's any problem that like the file doesn't
exist then it will return no and we in the transaction will return

minus one next we lock that inode so in either case we've got the file locked if the file already
existed then we check to see if it's a directory file we're allowed to open directory files but only
in read own only mode so here we're checking to make sure that the flags bit is the flag's word is
all zeros that is the um right only or the read write flag uh

flags are not set we're not trying to create it and it's uh we're not truncating it so we're only
allowed to open it in read-only mode and if anything's wrong there we immediately unlock it and we
decrement the reference count in the transaction and return minus one if the file was opened and it
was a device type oh well that's okay we do a quick check to make sure that the

major number is in the legal range here and if there's a problem there we would uh unlock it
decrease the reference count in the transaction and return minus one now we're going to we've got an
inode either for a directory or regular file or a device file and what we need to do is find a slot
in the open file array for the proc structure for this process and we need to fill in that value
with a

pointer to a file structure so we need to allocate a file structure as well and set its ip pointer
to point to this inode whether it's a directory file a regular file or in this case a device file so
that's what we're doing here we call file alloc to create the file structure and we set f to point
to that file structure and then we call fdabloc and fdalock will

look through the array and find an empty slot and set that to point to the file structure and
increase the reference count for the file structure so if there is any problem with either of these
that is we can't allocate the file structure and it returns null or there were no available slots in
the open file array then we will get rid of the file structure if

we allocated it by calling file close and in either case we unlock the inode and decrement the
reference count in the transaction and return -1 okay but assuming everything's okay we continue if
the type of the inode that we've just opened is device then we've got sort of this situation down
here where the inode points to a device what we need to do is set the type of

the file structure that we've allocated to device and copy the major number to the file structure so
that's what we're doing here we are setting the type to device and copying the major number from the
inode to the file structure otherwise we've got a directory file or a regular file and so in that
case we need to set the type of the file structure to inode and we need to initialize the

offset to zero and that's what we do right here we set the type to inode and we initialize the
offset to zero finally we need to fill in the ipointer in the file structure to point to the inode
whether it's a directory or regular file or whether it's a device file we need to set the ip field
to point to the inode structure and we do that here then we need to set the readable and

writable flags in the file structure so here we are looking to see whether um we have right only uh
we make sure that that bit is zero if it's zero then either we have a read only file or a read write
file and in that case um in either of those cases the readable flag should be set to true so that's
what we're doing here and then we look at the write only bit

if it's set or if the read write bit is set then we are allowed to write to this file so we can set
the writable flag to true and finally we look at the truncate bit if the truncate bit is set in the
flags value and we're looking at a regular file then we can call I truncate and that will truncate
the size of the file to zero and release any space it's occupying

now at this point we've got the file the ip the inode locked and we've got the reference count uh
incremented so we need to unlock the inode but we will keep the reference count incremented okay and
return the file descriptor uh we keep the reference count incremented because we now have a pointer
we stored a pointer to the file structure in the uh inode and we stored a pointer to

the uh sorry we stored a pointer to the file structure in this open file array and we stored a
pointer to the inode in the ip field so we have we do have a pointer to the inode so we need to keep
the inode's reference count incremented to indicate this pointer has been added to the inode and so
I believe that ends the code for the open system file for the open

so I believe that ends the code for the open system call next let's take a look at the pipe system
call in unix systems including xv6 the pipe system call is passed a pointer to an array and that
array has two elements it will create the pipe and it will return the file descriptors for the read
and the right end in this array it will return zero if everything's okay

at minus one if there's a problem so let's uh look at what's going on here here I'm showing a proc
structure for some process and it's got an open file array that has some elements already filled in
and the system call will allocate a pipe structure as well as two file structures shown here and
here and then it will fill in pointers from the open file array to

these file structures so here we've created a pipe structure and this is the read end this file
structure represents the read end and the readable flag is set to true and the writable flag is set
to false and you can tell that this is the right end because the writable flag is true and the
readable flag is set to false but both contain pointers to the pipe structure and the reference
count for

both is one and then what this system call will do is it will search the open file array and find
the first unused slot and make that point to the read end and it will find the next unused slot and
make that point to the right end and then it will save in this array the file descriptor for the
read end well that was two so we'll save two here and then it will save in the second slot

of this array the file descriptor it used for the right end which was five this array is a fixed
size array of two elements each one is big enough to hold an integer so the total size of this array
is eight bytes at least in xv6 next let's look at the code for cis pipe which implements the pipe
system call there's no activity on the disk so we don't see any transactions in this

particular function we're going to begin by calling arg adder to get our first argument that's the
virtual address of this fd array so we are going to store that virtual address into this local
variable fb array and if there's any problem we return immediately next we call pipealoc and what
pipe alec is going to do is allocate this pipe structure as well as these two file

structures and set this thing up here and it will return pointers to the read end and the right end
that is the pointers to the uh file structures for the read end and the right end by setting these
variables here there's any problem that returns -1 and we return immediately now we have got these
two these three structures allocated and we're ready to fill in these pointers here

so we're going to call fd alec to find a slot in the open file array and set it to point to the read
file structure and then we're going to call fdalic again to find another slot in the open file array
and fill it in to point to the file structure that represents the right end and then we're going to
save these two file descriptors in ft 0 and fd1 now I have no idea why we're setting fd

0 to -1 here because whatever happens this the first thing we do is call fbalik and it's going to
return something and modify fd0 but in any case here if anything goes wrong that is either of these
returns a negative one then we've got a problem and we need to deal with it so if we've got a slot
for the read end but failed on this on the right end then fd 0 will be something

greater than or equal to 0 and so we need to eliminate that we need to restore it to be no so that's
what we do here but in either case we close both file structures and remember that with the pipes
when we close a an end of the pipe it will eliminate that file structure and if it's the last
pointer to that pipe structure it will free up the pipe structure itself so on

the second call to file close we close the last end of the pipe and that will free the pipe
structure as well then we return minus -1 but assuming that we were able to store these pointers
into the open file array um we now have fd0 being 2 and fd 1 being five so we need to store those
into the fd array in the user space so this value right here is the virtual

address of this fd array and page table indicates the virtual address space so we're using copy out
to copy from the kernel space namely the value that's stored at fd0 and the size of fd0 well that's
an integer so that would be four bytes and so we're going to copy four bytes into the fd array at
offset zero okay and then we're going to copy the value of fd1

okay and uh we're gonna copy again four bytes and we're gonna copy it to the virtual address space
and this time we're going to copy it into offset four that is the beginning of fd array plus the
size of the first element so the beginning of fd array plus the size of the first element will be
the second the address of the second element in the array so here we're storing

the two and the five the two file descriptors that is into this fd array in user space and uh if
there's anything wrong here well we need to return those file descriptors to null so we store a 0 in
this element here and a 0 in this element there and then we close the read file descriptor and the
write file descriptor as before and return minus one but if these two

copy outs worked fine then we skip that and return zero to indicate success okay the final file uh
the final system call that we haven't talked about is the exec and that's pretty involved so I'm
gonna do that in the next video that's all for this video I'll see you in the next video
