# xv6 Kernel-19: VM Functions: Overview

| Field | Value |
| --- | --- |
| Video ID | `rhq08Lr-Yo4` |
| URL | <https://www.youtube.com/watch?v=rhq08Lr-Yo4> |
| Source | `docs/reference/scripts/original-raw/19-xv6_Kernel-19_-_VM_Functions_-_Overview-rhq08Lr-Yo4.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to go over
a number of helper functions for virtual memory management these come from the file vm.c and they're
used to manipulate the page table structures they're fairly straightforward and can be understood in
isolation for example they don't do any locking or sleeping

however there there's quite a bit of material and I'm going to break it into two videos in this
video I'll do a general overview of the functions and in the next video I'll do a walkthrough of the
actual code so let's begin with remembering what page tables look like a page table is represented
as a tree and in the case of xv6 they are three level trees and we can refer to the root node as the

root page and all the pages that comprise the page table we can call index pages at the bottom level
the index pages point to data pages so so let's look in more detail at what these look like and here
we see a picture of the page table one branch in the tree and here's the root node and we have a
number of page table entries each pointing to the next level

and at the lowest level we have the data pages so remember that a virtual address looks like this we
can use the field in the upper part of the address to index into the pages one two and three and the
offset is used to index into the data page itself the page table entries have this format namely
they contain a pointer that's a page align and this is used to go down

one level in the tree they also have a number of bits the read write and execute bits as well as a
valid bit and a bit to indicate whether the page is accessible in user mode these bits are valid in
the last page table entry the one at the lowest level here in the upper index pages at these two
levels only the valid bit matters and the other bits are zero okay now let's go through the various

functions and we'll start with the walk function walk is past a page table or more precisely a
pointer to the root node of a page table and a virtual address and it will walk the tree and it will
return the address of the page table entry that points to the data page in other words it's going to
be passed a pointer to the root and it's going to walk down the tree and return a pointer

to the page table entry that points to the data page if there are missing index pages in the page
table then we have the option to create them this third parameter alec is indicating whether we
should create index pages if they are needed if there's any problem for example we can't allocate a
page this function will return the null pointer the next function to look at is map

pages and it's used to add page table entries to a page table so it's past a page table and a
virtual address and that tells where the pages are going to be added in the virtual address space it
has also passed a physical address which points to a number of pages so size gives the number of
pages that are being pointed to and this function will add a mapping or multiple mappings in this

case three mappings to the page table so that these virtual pages are mapped to these physical pages
the mappings will be given the permissions that are given by the last argument read write executable
accessible in user mode if there's a problem this function will return minus one otherwise zero it's
often times the case with the xv6 kernel functions that if there's a

problem the function will return -1 and if everything's okay it will return 0.

for the kernel there is exactly one page table and all cores will share that page table and when we
create that page table we use this function kvm map kernel virtual memory map and it's basically the
same as map pages except that we do not expect errors when we're creating the kernels page table so
this function just adds a call to panic in case map pages returns a

minus one error code and as I said it's used to build the kernels page table so the function kb make
is the one that calls kb map to create and initialize the kernel's page table it adds all of
physical memory to the page table with a direct mapping and it adds all the memory mapped io devices
to the page table again with a direct mapping and it creates a mapping for

the trampoline page the trampoline page exists somewhere in the physical memory but in addition
there's a second mapping for that from the highest page in the virtual address space to that
physical page and finally it calls prop max that map stacks uh here's a picture of the virtual
address space for the kernel and up at the top we see the trampoline page and we also see

one page for each process and these pages are mapped into some virtual sorry some physical pages
that are obtained from k alec each process will need a page for its stack to be used when that
process is executing in kernel mode and so we allocate 64 pages and we add those mappings to the
page table for the kernel recall that we've got this preprocessor macro function called k

stack which is passed a number between 0 and 63 and returns a pointer to returns the address of the
corresponding kernel page table page the function kb annette will call kb kvm make to create the
page table for the kernel and it will assign it to some global variable kernel page table this
function is called during initialization by core 0 to create the page table for the kernel and then

subsequently each and every core we'll call init hart which will use that page table and write the
page table address into the satp register this is the control and status register and when the satp
register is set to point to a page table that effectively turns on paging for that core next we have
the walk address function which is used to take a virtual app virtual address and convert it into

a physical address so this will use the page table and walk it to find which page that virtual
address is mapped into and then it will translate the virtual address into a physical address the
page on which this virtual address occurs must be marked valid and it must have you permission set
and if there's any problem it this function will return no or zero since uh the

no pages in virtual any virtual address space are mapped to the very first page in the physical
memory no virtual address can have a physical address of zero so it's safe to use zero as the result
of this call next let's look at the uvm create function this is a function that will create an empty
virtual address space essentially it's just going to allocate

one physical page and clear it out to zero and return that as the root index page for the page table
uvm is user virtual memory and we've also got user virtual memory init which is used to create the
first user virtual address space this is a little bit special um it's going to allocate a single
page map it into virtual address zero and mark that page as readable writable

executable and accessible in user mode and then it's going to fill it with some code okay it's going
to fill it with the code for this function init code or should say this process init code a knit
code is represented in the kernel as an array of a bunch of bytes 52 bytes spelled out in hex and
what this function is going to do is simply going to copy that sequence of bytes which is pointed to
by

source with a length of size into that first page now what are these bytes well they are
instructions and where do they come from originally they come from a file called initcode.s which is
the very first user mode process that ever executes and it's written in assembly code but basically
what it does is it calls the exact system call passing it a file name

which is the name of the init process as well as the zero argument that should not uh ever return
this init function uh this init file should exist and so there should be no problem but if for some
reason it does return this code we'll call the exit system call which itself should never return but
uh that is followed by a go to to this loop label and that is all assembled and the bytes

then are extracted from the executable and placed in this array where they can be copied into this
single page virtual address space the next function to look at is uvm aluc which will add pages to
an existing virtual address space it's passed a pointer to the existing address space and the size
of the old virtual address space as well as the size we'd like to bring it up to

so it calls k alec to allocate physical pages and it calls map pages to add page table entries to
the page table the new pages will be marked readable writable executable and accessible in user mode
and so here's uh an attempt to show this with pictures here's the old virtual address space and uh
we want to add as many pages as necessary to bring it up to new

the old and the new pointers on the old and the new sizes need not be page aligned and we will add
as many pages as necessary to make sure that the virtual address space is at least as big as the new
size and we will return the new size if there are any problems this function will call k free and
uvm the alec which I'll discuss in a second to basically return all the pages to the free pool

and restore the page table to what it was before as well as returning a zero to indicate that it has
failed so here is an example use of it here's a user's virtual address space we've got the code and
the data at the lowest address we've got the stack page and a guard page to catch stack overflow and
then zero or more heap pages this is the so-called break point that's

how big the user thinks the address space is but of course we also have the trampoline page and the
trapframe page up in high memory so when the user program wants to grow the heap we will ultimately
end up calling uvm alec uh using this point as the old and enlarge the heap to whatever the user
needs it to be we've also got uvm unmap and this is going to go through each

page and set its page table entry to zero which includes the valid bit and so it will by definition
change this page table entry to an invalid entry and so we have the pointer to the page table we
want to modify and the virtual address as well as the number of pages we'd like to remove from the
virtual address space and we also have a fourth argument do free and this boolean tells whether or

not to free the data pages so if it's true then this function will also call kfree to return the
data pages that are being unmapped to the free pool next let's look at the function uvm dialog which
is very similar to the previous function the previous function I talked about uvm unmap worked in
terms of the number of pages whereas this one works in terms of the

old size and the new size and it's used to shrink back an address space and all it does really is
called uvm unmapped to remove the pages and to free the data pages so it calls uvm unmap with the do
free argument set to true the old size and the new size do not need to be page aligned as this
particular example shows and also the new size can be greater than the old size in which case we
don't

do anything it's okay next I want to talk about free walk which is used to remove a page table to
free up all the index pages that are involved in a page table it's passed a pointer to the root of
the page table tree and it will walk the entire tree and free the index pages but it will not free
the data pages this is actually a recursive function and it's passed a pointer to any index

node and what it does is it calls itself on the children of that node to delete the subtrees and
then it frees the root page the top page now in kernel coding we need to be careful with recursive
functions the kernel doesn't have a very large stack and typically they're a fixed size and rather
small stack so um we need to make sure that this thing doesn't recurse too deeply

in fact the tree is only three levels deep so we can be certain that it won't recurse very far and
we can be sure that the stack usage is bounded and we won't get into any trouble there next let's
look at the uvm free function which will basically free the uh virtual address space and uh it's
passed a pointer to the page table and the current size of the virtual address space

so here I'm showing the user's virtual address space and we see the code in the data we see a stack
page and a guard page to catch overflow and finally zero or more pages that are allocated to the
heap and what this thing is going to do is free all the pages below size and it's going to walk the
page table and free all the index pages a user address space also has a

trampoline page and a trap page the tripling page is shared by all virtual address spaces so we
don't want to free that and the trap page is also pre-allocated there's one for each process so
that's not free either the next function is uvm copy remember that when we have a fork system call
we need to copy the entire virtual address space of the process that

invokes the fork and we need to create a second address space for the child process and uvm copy is
going to take care of that for us it will copy all pages up to size from the old address space and
put them into a new address space so it's past the pointer to the old page table and to the new page
table as well as the size so here is a picture of the old address

space we've got the code the data the guard the stack the heap pages as well as the trampoline and
trapframe and here is the new address space before we call this function and it will already be set
up with the topmost page pointing to the trampoline page and it will have its own trapframe page and
what this will do is copy all of the data pages so here I've

indicated the data page is being copied and it will create mappings for each one of these the
permissions that were valid here will be applied to the new page table so these will have the same
exact permissions thing I wanted to mention is that the guard page is actually mapped to a physical
page in memory it will be marked as not accessible in user mode so over here it will also be

marked as not accessible in user mode but to keep things simple we copy the page even though this
data page will never be accessed so in order to do this the function calls walk to walk the page
table chaotic to get new pages new physical memory pages memory move to copy the data and map pages
to add mappings to the new page table if anything goes wrong for example if we

run out of memory then it basically undoes everything it calls kfree and uv m unmap to uh remove
every uh entry from the page table and uh return all the pages next let's look at uvm clear and this
is uh used only to mark the guard page as not accessible in user mode that's the only place this is
used so it's past the pointer to the page table and the virtual address of the

guard page and it will mark that page by clearing the u-bit in the page table entry and as I said
it's used for marking the stack the stax guard page in a user address space from time to time the
kernel needs to move bytes into or out of the virtual address space of the user processes for
example when the system call open happens it's past a pointer to the string that is the path

name or the file name that we want to be opening and that string is located in the user's virtual
address space the kernel needs to get it the problem is that that string may cross multiple pages it
may lie on more than one page so in this example I'm showing the block of bytes that we want to
access in red and you can see that this block of bytes spans uh four pages

well that's what it looks like in the virtual address space it's all contiguous at least the user
sees it that way but the actual physical pages in memory can be spread all over and so this data is
not contiguous in physical memory and so the functions that we're going to look at copy in and copy
out basically are used to copy the data into a buffer in the kernel or from the

buffer back in such a way that it will be contiguous in the virtual address space so let's start
with copy in it's past a page table and the virtual address from which to get some bytes as well as
the number of bytes we want to obtain and it's passed a place like the address of this buffer here
where it wants to store the bytes and what it will do is it will

copy bytes from the user's virtual address space to this kernel area pointed to by destination so
it's going to copy the individual pieces I mean it would start by copying um well let's say memory
goes up this way it starts by copying this piece and then this piece and then this piece and finally
the last the last piece copy out is the reverse it's used to move data from this buffer back into
the

virtual address space it's past a page table and the virtual address where the data is supposed to
be placed as well as a pointer to the source that is the buffer and the number of bytes if anything
goes and it copies bytes from the kernel area back to the user's virtual address space if anything
goes wrong for example the virtual address is not valid then this function will return a minus

one if there's an error or zero if it's all okay in the case of the open system call we are looking
for a string that will be null terminated so we only want to copy bytes up to the null byte and so
there's a variation on copy in called copy and string and it's similar it's passed a source virtual
address and the destination uh that is the address of a buffer into

which it's going to be placed as well as the maximum number of bytes to copy this is probably going
to be the buffer length and so it will start copying and it will go up to max length but it will
stop after it copies the null terminating byte at the end of the string if it goes all the way to
the maximum length without encountering the terminating null byte

then it will stop copying and it will turn a minus one to indicate that the there was some sort of
an error okay as I mentioned uh this this is an overview of these functions and in the um next video
I'll be walking through the code and it's safe if you want to just skip that if you feel like you
understand these functions well enough uh you can skip the next video which

walks through the code in detail and proceed with just this overview of what these functions do okay
that's it thank you and see you in the next video or the one after that
