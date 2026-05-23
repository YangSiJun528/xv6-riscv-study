# xv6 Kernel-38: Exec System Call

| Field | Value |
| --- | --- |
| Video ID | `bp0qo4-ozEg` |
| URL | <https://www.youtube.com/watch?v=bp0qo4-ozEg> |
| Source | `docs/reference/scripts/original-raw/38-xv6_Kernel-38_-_Exec_System_Call-bp0qo4-ozEg.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel and is in fact the last video in
that series in this video I'm going to describe how the exec system call works this is essentially a
culmination of all you've learned so far it's a pretty long video but uh I think you'll see that
it's really quite interesting what happens in the exec system call

I will go over the elf file format at least in vague outline and then I will cover the sys exec
function and the exec function from file sysfile.c and exec.c okay let's get started in order to
understand the exact system call we need to understand how the executable file is laid out the
executable file will be in the elf file format there are a number of different file

formats that are used on different systems the elf format is used in the unix world in the apple
world we have a file format called mock o and for windows we have the portable executable file
format these are all sort of the same but the details different for each one the elf file will begin
with a fixed format file header and that tells where the other parts of the file are located

the header is followed by an array of program headers each program header is exactly the same they
have the same size and so on and that will be followed by a number of segments the actual code
that's to be loaded into memory will be located in these segments and these segments can have
different sizes as you can see here each segment will be pointed to by exactly one program header
and that will

contain information about where in the executable file the segment is located the segments can be
followed by some other stuff for example we can have something called the section headers in the
case of relocatable files this would contain information about how to relocate the machine code
that's in the segments we could also have some information about debugging and so on

uh I won't be looking at this section header stuff at all now let's take a look at the file header
here and I'm showing it in more detail here there are a number of fields and let's go through some
of these the first is the magic number and this should contain exactly these four bytes these can be
checked to make sure we're dealing with an l file if there's a

mismatch here then clearly this file is not an elf format file of course if the magic number is
correct the file could still contain errors either due to bugs or perhaps malicious code the next
field here indicates whether this is a 32-bit file or whether the data in this file is 64 bits and
the next field indicates whether the data will be in little indian format or

big indian format I'm not showing the exact sizes of these fields here and I'm not showing the
offset within the file header these will actually change there are different versions of the elf
file depending on whether it's 32 bits or 64 bits but let's just move on the executable code could
be for one kind of a processor or for another kind of processor and this field

called machine indicates whether it's for uh for example the x86 architecture or the x86 64
architecture there's also a code for the RISC-V architecture so we would expect this field to
contain hex f3 the executable code might be intended to run on a different kind of operating system
so this field called os application binary interface contains a code to indicate which

operating system that this file is meant to be run on there's not actually a code for the x86 but we
don't even look at this field anyway so that's that's okay the elf file format is normally used for
executable files and it's also used for relocatable files when the compiler runs it will compile the
source code and produce an object file that is a dot o file and that file will

be a relocatable file and in the unix world the relocatable file will also be an elf file format
file so this indicates whether we've got a relocatable file or an executable file the relocatable
files the object files will be processed by the linker which will then produce a file in the
executable format the l file format is also used for other things for example for core files

when a program crashes or has some kind of an error sometimes we want to capture the contents of
main memory for the purposes of debugging in that case we can create what's called a core file and
that core file can also be stored using the else file format but we will only be looking at
executable files the program header offset and program header number indicate where in the file

we can find the array of program headers so the offset says where in the file this array begins
that's this location here and the number indicates how many program headers we can expect to find in
the elf file and finally we have the entry point this contains the address at which execution should
begin this might be the first byte of the main function but typically there will also be some

prolog code in the executable file the prolog code will be the code that actually calls the main
function and so in that case the entry point would be the address of the first byte of the prolog
code so after the kernel loads the executable into memory it will then jump to the entry point and
execution will then begin in the xv6 system we ignore a lot of these fields and in

fact we will only be looking at the magic number the entry point and the program header offset and
number to find the program headers the other fields uh will be completely ignored so now let's take
a look at the program headers so we have in this example four of them and each one of these things
has a fixed format and here I'm showing the program header structure

it has a number of fields and as I said it's a fixed size the first field is the is the type field
and there are a number of different types but we will only be concerned with uh loadable segments uh
that's indicated by a code number of one the flags field indicates whether this segment should be
marked as executable writable or readable so these particular bits in

this flags word indicates whether the segment when it's loaded into memory should be marked as
executable and or writable and or readable the offset field tells where in the l file we can find
the data so this points to this segment and the file size field indicates how much data is in that
segment that is how big the segment is the data in this segment will then be

moved into main memory by the kernel when the executable file is loaded so the virtual address field
tells where in memory to place this there's also a physical address field in most unix systems we
are dealing with virtual memory so certainly in xv6 we are only concerned with the virtual address
so that's where in the virtual address space we're going to load or move this data segment

the physical address will be ignored in xv6 as I said the file size tells how many bytes are in the
file for the segment the memory size tells how many bytes in the virtual memory address space are to
be occupied by the segment the memory size can actually be larger than the file size it cannot be
smaller but it can be equal to or larger than the file size if it is larger

then we are the kernel is supposed to fill in the remaining bytes with zeros so if we actually have
zero bytes in the file we can use the program header to indicate a region of memory region of
virtual outer space that should be entirely filled with zeros so in that case the file size would be
zero and the entire segment in the virtual memory would be zero filled

finally we have an alignment field that's actually ignored in fact we assume that the segment will
be loaded into a an address that is page aligned but in other systems we might have different
alignment requirements for the segments in xv6 all of our segments will be aligned beginning on a
page boundary the file elf.h contains a number of definitions that are related to the elf

file format so let's take a look at that we begin with a structure that defines the layout of the
file header in the elf file and so here we see a number of fields that I showed there's a loose
correspondence here we see the magic number we skip over a bunch of stuff here we have the type
field the machine field here's the skip some stuff we have the entry uh the program header all set
and down

here we have the programs header count we have some additional stuff for the section headers and
some other things that we will ignore as I said before we only are concerned with the magic field
the entry field and the program header offset and number we have a constant up here which is the
magic number I showed it here but we're giving it in little indian format so we have

seven f four five four c and four six okay given right here and uh then we have the program header
okay so let me show the program header I described it here and the correspondence is straightforward
we have the type the flags the offset the virtual address the physical address which we don't use
the file size and the memory size and finally the alignment value which we also ignore

uh we have some other constants in the program header we have a type field and here is the code
number for an executable segment a program loadable executable segment and here we have the codes
for the bits here in the flags word or we have the executable writable and readable bits and those
are just given with these constants here so here is the exact system call

it's passed two arguments the first is a pointer to a path name that describes the executable file
that we like to load and execute and the second argument is a pointer to an array and these are the
arguments that will be passed to the executable file this array contains pointers to strings that
are null terminated and it ends with an entry that is a null pointer and

these are in the virtual address space in unix systems there are several other variants of the exact
system call but xv6 has just this one and this is the main one that gives you the idea of what's
going on so when this system call happens the kernel will go into this sysex function this is coming
out of the file sysfile.c and what it needs to do is get these

strings this argument stuff from the virtual address space of the process that invoke the exec
system call and then it will call the function exec to actually do the work so let's step through
the sysex function the first thing it's going to do is copy the path argument and we saw the
argument string function earlier and it's going to copy that into a local

variable called path the second thing it's going to do is get the second argument this is an a0 this
is an a1 and it's going to copy the address of the array that's a virtual address into this local
variable UART so at this point I can just draw this line here to indicate that that gets stored the
next thing it's going to do well we've got this rgv array and

it's fixed size it can accommodate up to 32 uh elements actually that would be 31 arguments because
the last argument has to be followed with a null pointer and that's given by this max arg constant
so the next thing we do here is we set all entries in that array to zero so here I'm showing the
array so let me just draw it like that and we're setting all the elements to

null okay later we'll go ahead and change them then we have a loop here which is going to walk
through all of the arguments so it starts at I zero and just increments okay and the first thing we
do is check to make sure we haven't gotten more arguments than we can accommodate so we have to have
room for um uh we have room for up to 31 arguments plus the final

null so here we're checking to see whether we have gone past uh the last element and if so we go to
this label bad okay I'll come to that in just a second but basically we're going to return -1 at the
label bad next what we do is we go into the virtual address space okay and we fetch the value that
is stored at some particular location and what location are we

interested in well we're interested in the address of the user's array plus I times the size of the
uh entries okay so this is the user's array and we add I and so we go in and we get a pointer okay
to one of these strings and then we store it in a local variable called u arg okay so urg is a local
variable here uh let me just uh illustrate this um for one of the

examples let's assume we've gone through a couple of times and now we're on uh I equals uh two here
so we've already filled in a couple of elements but um on the iteration of this loop where I is now
two we uh fetch this pointer and store it in uarg okay so uh we go to the this element here and we
fetch that so now you are points to this string that's it sorry you are points to this string here

the second element uh if there's a problem then we go to bad but um then we check to see whether um
we got something okay if we got the last element here if we got a null then we are done we need to
store a null in the arg v array and we do that and then we break out of the loop otherwise what
we're going to do is copy the string remember that these strings

are in the virtual address space and we need to move them into kernel's memory somewhere so the rgv
array is a local memory for this particular function and what we're going to do is allocate a page
for each and every string so for this string we're going to allocate an entire page and copy it so
here we're uh here's the page we allocate and we're going to copy all the characters up to

the terminating null into this page so that's what's going on um here we allocate the page okay and
uh if we uh failed with the allocation then we go to this bad label but otherwise we call fetch
string and fetch string is past the virtual address okay and it's past the um place where we're
going to copy it to okay that's uh the pointer to the uh page okay up here uh after we allocated the

page we saved it uh so I forgot to draw that line so let's go ahead and save the pointer to this
page this newly allocated page and then we call fetch string which will copy up to a full page worth
of bytes into the page that we just allocated in kernel space this particular function will copy the
final null byte and it will return -1 if there wasn't enough room to copy the

entire string plus the null byte and if there's a problem with that we go to bad okay so now we can
take a look at bad that is just going to loop through this array loop through the rv array and for
every element that is not null we will just call kfree to get rid of these pages return them to the
free pool and then we'll return minus one but assuming that uh we go through all

the strings and finally exit when we hit the null pointer we will then call the exact function
passing it the local variable path okay that's here and the rv array the pointer to that array and
we call exec exec ideally will not return it will result in the executable file being loaded the
previous process will have its memory returned and we will never come back here but if there's

any problem this function will return a minus one and then we can return -1 to the user process that
invoked the exact system call but before we do that again we need to go through this array and free
all of the elements that have been allocated next let's take a look at the exact function from the
file exec.c this function will complete the work of the exact system call

it's passed a string which is the path name of the executable file and a pointer to an array that
array contains pointers to strings and those are the arguments to pass to the new executable program
it will return -1 if there is a problem but if everything goes okay then it will begin executing the
new program in the new virtual address space so it begins by starting a transaction

and calling the name I function name I is passed the path name of a file and it will go out and make
sure that that file exists and load an inode for that file into memory return a pointer to that
inode if anything goes wrong then the transaction will be ended and we'll just return minus one but
if everything's okay then the inode will have its reference count

incremented and I've shown that with this line here and then the next thing we do is we lock that
inode after that we begin by reading in the header from the elf file so we've got this local
variable here called elf which will contain a header structure and we read in from offset 0 the
structure and we place it into this local variable and this flag indicates

that that variable is in kernel space and this is the inode pointer and if there's anything wrong we
will jump to this bad label which I'll discuss in a moment but if everything's okay we next check
the magic number for the uh elf file and the magic number is this uh constant of seven f four five
four c four six and uh assuming it's okay then we keep on going

and the next thing we do is we call proc page table now this code is executing within a process and
that process has a virtual address space and if anything goes wrong we will have to return -1 and
return to executing the code that's in that virtual address space but if things go okay then we're
going to create a new virtual address space and load the executable program into

that virtual address space so here we're creating the second or the new virtual address space and
this local variable page table will point to the newly created page table this function here needs
to have a pointer to the current proc structure because it needs to add a trapframe to the top of
this virtual address space and it needs to know which physical page

to use for that particular uh page in the virtual address space if there is any problem in
allocating this page table then again we will jump to this bad label but otherwise we keep going so
let's continue here and the next thing we see is a for loop and what this for loop is going to do is
go through the program headers remember that the elf file contains an array of program

headers so that's what this loop is going to go through here we see offset and that's the offset of
the first program header and we are incrementing by the size of the program headers here we've also
got this index I which is being incremented and that allows us to stop after we've done each of the
program headers we call redi here to read in the first or the next

program header into this local variable ph so going back to the top of the exact function we see the
local variable ph here which is big enough to hold a structure for the program header and this is
the inode and this address here is in kernel memory this is the offset the current offset for the
program header and if there's any problem we go to the bad label

then we check the type field of the program header the type field had better be a 1 to indicate that
it is a loadable segment there might be some other kinds of segments although I don't know what
other kind of segment we might find in an executable file but if we find something besides that we
would just continue and repeat this loop that is we would skip anything that is

not a loadable segment but if it is a loadable segment we continue here and the next thing we check
is memsize and we make sure that it is not less than file size okay remember that in the file we
have a segment and the size of that segment is file size and we're going to initialize a chunk of
memory in the virtual address space and the size of that chunk is mem size and it better be

at least as large as file size so it ought to be able to contain all of these these bytes here so
we're checking that to make sure that we don't have a problem there next we're adding v adder plus
min size and checking it and what's going on there well remember uh that v adder and memsize are
unsigned integers and just to refresh your memory whenever we have unsigned integers and we add them

there's a possibility of overflow and if overflow occurs then the answer will be incorrect and if
overflow does not occur then the answer will be correct and if the answer is correct and there's no
overflow then the sum will be greater than or equal to a and will be greater than or equal to b
because these are zero or larger however if and only if there is overflow

then a plus b will be less than a and less than b so what we're doing here is checking to see
whether we've got overflow so that's what's going on here remember that the kernel can't trust the
code that's running in a process a user process and it can't trust what's going on inside an
executable file so we need to make sure that we don't have overflow when we add these two things
together

down here otherwise the kernel might be uh get get confused and we might have a problem so that's
why we're checking for overflow here the address where we're going to be loading the segment in
virtual address space had better be a multiple of page size that is it had better be page aligned
and we're checking that here then we call uvm alec uvm alec is used to grow a virtual

address space so here's the pointer to the page table for this new address space that we're creating
and here's the old size and here's the new size so we're providing a size here that it is at least
large enough to contain the segment that we're going to be loading so this is the virtual address
right plus the mem size so we're making sure it's at least this big

okay and uh this will return the new size if everything is okay if there's a problem it will return
zero so that's why we're using this local variable here cz one we uh if there's a problem and it
returns zero okay then we're going to jump to bad and bad will need the size variable the old value
so that's why we're using a temporary here but if everything's okay we can go ahead

and update the size variable now this xv6 code happens to be changing and since I made the video
where I discussed the uvm alec function the code has actually been modified and now the uvm alec
function takes an additional parameter in the video where I covered this function I did not have
this additional parameter but now it has this additional parameter the way I describe uvmalik it
allocates

the pages and for each page it will mark them as accessible in user mode readable writeable and
executable however that is changed and now the current version of uvm alec will mark each page as
accessible in user mode and readable and it will take this additional argument and this additional
argument will tell whether to mark it as executable and or writeable

so this little helper function flags to perm is given right here so let's take a quick look at that
remember that in the program header we have a flags word and that flags word contains some bits and
the first bit is the executable and the second bit is writable okay so flags two perm is going to
take such a word and it's going to look at the rightmost bit and the second bit and if

the executable bit is set then it will build a permissions where the page table entry bit for
executable is set and then it looks at the second bit and if that is set it also will set the page
table entry writeable bit and then it will return that new permissions and that can then be passed
to the uh uvm alec function so that's what's happening with this last argument

here so um let's at this point take a look at bad if anything goes wrong here we jump to this bad
label which I have right here so here's the end of this function we may have a transaction in
progress so here's what happens we first look at the page table and if we've managed to get far
enough to allocate a page table then we need to return all the pages that are involved

back to the free pool and so we call proc free page table with this page table and the current size
and so that's why we had to use this cz1 temporary variable because we we need size right here and
this will free the page table and return everything to the free pool likewise if we've got the inode
for the executable file still open then we need to call put and unlock and we need to end

the transaction and then return -1 we will see okay going back to this loop here after the loop ends
we unlock we call the put and unlock an endop but there are other places where we call bad like down
here where we will be outside of this transaction so that's why we check it that way so um after we
allocate space in the virtual address space that we're creating

we need to actually load the bytes or move the bytes from the file to the virtual address space so
we have this helper function load segment and that's what it does so the file size field tells how
many bytes are in the file and ph offset tells um where in the file those bytes begin so we're
moving it the bytes from the file and we're moving them to here's the pointer to the inode for the

file and where are we moving them to in this new virtual outer space well the v adder field tells
where we're moving them to if anything goes wrong this load segment function will return to -1 and
we can exit to the bad label but if it succeeds then we are done with this loop and we can go on to
the next program header and after we've done all the program headers we're done with the executable

file we can then uh put and unlock it and in the transaction and we then clear out the inode pointer
so that in the bad label we won't redo these two actions okay now let's take a look at this load
segment function here is the load segment function it's passed page table which is a pointer to the
page table describing the virtual address space that is being

created for this executable program and it will load a program segment into that virtual address at
location va and that va address must be page aligned and furthermore the pages in the virtual
address space that are going to be written to must already be present in that virtual address space
if there is any problem it returns -1 and otherwise it returns 0.

so in addition to a pointer to the page table describing the new virtual address space it takes the
address at which we will be loading the data ip points to the inode for the executable file offset
is the place in that file where we are going to get the bytes and size is the number of bytes that
we're going to be moving into the virtual address space so this function contains a loop and

each iteration of this loop will move one page of data from the file into the virtual address space
I will be the number of bytes we've moved so far so we're just going to loop in units of page size
here until we've moved as many as size bytes the first thing we do is we take a look at the virtual
address space and we determine where we are going to move the next chunk of

data so we call walk address and we pass it the virtual address plus I that's the number we've
already moved up to now and that will give us the physical address of the page in uh kernel memory
where that page is mapped to so uh once we have the physical address we check to make sure that we
really have something we have already called uvm aluc so that page ought to be in the virtual
address

space and if it's not we print an error message here then we look at how many bytes we have left to
go so I is the number that we've moved so far up till now and if the remaining amount of bytes is
less than one page worth of data then we don't want to move a full page worth of data instead we
compute the number of bytes to move as size minus I otherwise we're

going to move a full page of data and finally we call read I and read I is past the inode from the
file from which we're going to read as well as the offset in that file so this is our offset
parameter plus the number we've moved already and n is the number of bytes we're going to be moving
on this particular move and pa is the physical address this flag indicates

that that's a physical address and not a virtual address if there's any problem we return -1
otherwise we loop back and move the next chunk of data from the file into the virtual address space
and when we're done we return 0.

so let's return to the exact function here's the loop where we're going through all of the program
headers and for each one we are loading a segment and after we're done with this loop we're done
with the executable file so we call unlock put and then we in the transaction and then we move down
here here we are getting a pointer to the proc structure we actually did that

earlier in this function so this statement is unnecessary and then we're capturing the size of the
existing virtual address space and we will be using that later on and then let's see what's going on
with this so here is a picture of the virtual address space when we created it it had two frames uh
two pages at the very top of the virtual address space for the trampoline and

trapframe and then we loaded a bunch of executable segments down here somewhere and apparently in
this example we require four pages so we have a number of pages and size might not be page aligned
at this point so what we're going to do is round it up to the nearest page boundary and then we're
going to allocate two pages one for the guard page and one for the user stack page

so that's what's going on next here we see that we are rounding size up to the next page boundary
and here we're calling uvm alec and the old size is size at this point and then we're adding two
additional pages and growing the virtual address space by two pages and if there's a problem there
we go to bad but otherwise we have enlarged the size of the virtual address space

these pages pages will be marked as readable and accessible in user mode by the uvm alec and this uh
new argument to uvm alec in this case we'll mark the pages as writable as well so we can go ahead
and mark these pages as accessible in user mode readable and writeable accessible in user mode
readable and writeable the next thing we do here is call uvm clear and we're passing it the size

pointer minus two pages so at this point the size pointer has been incremented by two pages and
decrementing two pages we're pointing at the guard page so we call uvm clear which is going to mark
this thing as not accessible in user mode and if the user mode program tries to grow the stack it
grows downward and grow it beyond one page it will try to access this guard

page and that will abort the user program next we need to capture the pointer the stack pointer the
stack is empty and it will be growing downward and we also capture a variable called stack base
which will allow us to detect whether we are going to push stuff on the stack and overflow it within
this function so here we clear the user mode bit and here we capture the

stack pointer and the stack base pointer so continuing with the exec function the next thing we're
going to do is deal with the argument strings so remember that the exec function is called with a
pointer arc v to an array and that array contains pointers to strings and each string has been
placed in a separate page in the kernel space so here's what we'd like to get to okay

here is a picture of the stack and we start with an empty stack and the stack will grow downward in
this direction and we want to push each one of the four argument strings onto the stack and we want
to push an array with pointers to those strings and then we will pass to the main function of the
new program both arg c which will be 4 in this case as well as

rgb which is a pointer to an array of pointers to the strings so let's walk through this code we are
going to count the number of arguments with the variable argc starts at zero and we're going to go
through the r v array and for each one of the strings we're going to push that string onto the stack
so first of all we make sure that we don't have too many arguments and if so

we go to this bad label then we look at the string and we figure out how long it is and then we add
one for the terminating null byte and then we subtract the stack pointer and we also want the stack
pointer to remain aligned on 16 byte boundaries so this line here will lower the stack pointer if
necessary to the next 16 byte boundary and then we check to make sure that we haven't overflowed the

stack by looking at the stack base value and go to bad if that's the case and then we are ready to
copy from the string okay here is a pointer to the string in sitting in its page by itself here's
the length of the string plus the null byte and we're going to copy it to where the stack pointer
now is and so here in this diagram these tick marks here show 16 byte boundaries

and so we figure out how long this string is and then we drop that down to the next 16 byte boundary
and we copy the string in there possibly leaving some unused bytes um after it now we've got this
other variable called u stack that's a local variable in the exact function and um we want to save a
pointer to the string that we just pushed onto the stack in that array

so that's what's going on here I'm showing the u stack array here uh normally I in the past I've
shown arrays uh with the first elements at the top and then indexes increasing as we go down the
page like this but for this case I want to show it in the reverse way so this is the first element
here to the first string I'm doing it this way because uh I'm showing the stack growing downward

with low addresses down here and higher addresses up higher on the page and so we add a pointer to
this u-stack array for the first string and for the other strings as well so we go through this loop
repeating for each one of the arguments saving a pushing it onto the stack and then saving a pointer
to it in this u-stack array and finally when we're all done we

save a null pointer after the last pointer so that's this null pointer here now we need to push that
u stack array onto the stack so here we are calculating the size of it this is the number of
elements plus the terminating null so 4 plus 1 times the size of these pointers and so we subtract
that from the stack and then with this line just like before we round

it down to the next 16 byte boundary and check to make sure that we haven't overflowed the stack if
everything's okay then we can now copy the entire array so the size of this array is just rxc plus
one for the null pointer and we copy that to sp and here's the where the the array is located so we
copy it to the current value of the stack pointer and if there are problems with that we

jump too bad so that's what's happening here we have figured out how big this array is uh dropped
down to the next 16 byte boundary and then copied the elements up to and including the first null
byte uh the first null pointer into uh this area here in the stack so again we may have some space
after that array now the stack pointer is pointing here and we when we call the main program for

the new program we will end up passing it argc that's the count of the arguments in a0 as well as a
pointer to this array and that will be in register a1 okay so we've just copied the usdac array into
the new virtual address space at location sp if anything goes wrong then we jump to our bad label
but otherwise nothing else can go wrong and we will proceed down to

the return here so we are committed at this point to executing the new program sp at this point will
point to the rgv array that we've pushed onto the stack in the new virtual address space and that
needs to be saved uh in register a1 so that the new program will get it a0 and a1 will be the
arguments to the function the first function that executes in the new

program so we need to save sp that is the pointer to the rv array on the stack in the trapframe for
register a1 we have a field called name in the proc structure and what this does right here is save
the program name for debugging so the path name might look like this and we run through the path
name with variable s up to the null byte and we increment through it and for each

uh character we ask is it a slash and if it is then we're going to save a pointer to the character
after the slash so for example when we hit this slash we set last to point to this position here and
after this last slash is encountered we have a pointer to the final file name in the entire path
name if there are no slashes well we initialized last to point to the

beginning of the path name so now we will copy from last through the null byte into this name field
in the proc structure and we use save string copy to copy up to as many characters as there are in
this field now we are ready to switch out the old virtual address space and switch to the new
virtual address space and make sure we start executing at the beginning of

the new program so we have previously saved the size of the old virtual address space and here we're
saving a pointer to the old page table for the previous virtual address space and here we are going
to call proc free page table with the pointer to the old page table and its size to free all of the
pages that are associated with the previous address space and here we are going to switch over to

the new virtual address space by replacing the page table with the new page table that we just
created which has size indicated right here we will also update the program counter we saved the
executing program counter of the previous program in the trapframe at this location but here we're
going to overwrite it with the entry address for the new program now previously we had gotten into
the

kernel with an environmental call instruction so the program counter was pointed to the place where
we should return from that but the entry point of the new program is the beginning of some function
so previously we would be returning a0 as a return value for from the ecall function but now we are
going to return a0 and a1 as well and those will be arguments to the code

presumably a function that begins executing at this location here we also need to update the stack
pointer so here is the stack pointer that we have computed for our new stack in the new virtual
address space and we are saving that in the track frame so that when we return to the a program uh
it will have a new stack pointer as well as a new program counter then we

free the page tables uh the old page table and return now remember that this exact function was
called from the cis exec function and uh previously I told you that the exact function here would
not return unless there was an error and if there was an error it would free all those pages that we
allocated but I kind of lied it will return even if there's no error so if it does return

without an error then it will return a value that is the r count the rxc value and that will then
get returned to the program in register a0 but whether there's an error or not we will do this loop
which we'll call k3 for each one of the pages that we allocated for the arg for temporary storage of
the argument strings and at this point we return I I'm not sure we should call

it return but we uh go back as if we are returning from an environment call and we resume executing
in the user users process at location epc with the new stack pointer and the new page table the proc
structure also contains some other fields for example it contains the o file which is the array of
pointers to the open files and it contains the field cwd which is the current working

directory those are not changed here so the newly executing program will begin execution with all
files that were open in the previous program still being open in the new program and with the same
current working directory okay that wraps it up for this video and that also wraps it up for the
entire playlist so if you have watched all the videos in this series then um kudos to

you uh by all means leave me a comment uh on this video so I will know that and um well thanks for
watching and I hope you've learned something about the kernel and uh thank you very much
