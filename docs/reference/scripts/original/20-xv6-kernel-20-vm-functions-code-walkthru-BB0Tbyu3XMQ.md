# xv6 Kernel-20: VM Functions: Code Walkthru

| Field | Value |
| --- | --- |
| Video ID | `BB0Tbyu3XMQ` |
| URL | <https://www.youtube.com/watch?v=BB0Tbyu3XMQ> |
| Source | `docs/reference/scripts/original-raw/20-xv6_Kernel-20_-_VM_Functions_-_Code_Walkthru-BB0Tbyu3XMQ.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the XV6 operating system kernel.

In this video, I'm going to talk about the virtual memory helper functions that are found in the
file vm.c.

In the previous video, I looked at the same file and I did an overview talking about each function
individually and describing what it did.

In this video, I'm going to look at the functions again a second time, but this time I'm going to
actually do a code walk-through and see how they achieve their functionality.

You can watch both videos if you want the greatest understanding of this material, or if you've
already watched the previous video, then it's quite reasonable to consider skipping this video. This
video is quite long, over an hour long, so you may want to just skip this video. But for greater
understanding, watch them both. Or if you have not yet watched the overview of

function, then you might want to just watch this function this video alone, and that's also
reasonable and acceptable.

Okay, let's get going.

Okay, let's take a look at the walk function. It's passed a pointer to the root of the page table
tree, a virtual address, and a boolean called alloc.

And it walks the tree and returns the address of the page table entry.

So, here is a picture of a page table and our page table consists of three levels of index pages and
the data page.

We're passed a pointer to the root of this tree and we're going to walk this edge here to this index
page, and then we're going to walk this edge to this index page, and finally we're going to return a
pointer to the page table entry in the level zero index page. We do not follow this pointer down
here and don't do anything with a data page.

If in the course of walking that tree, either the index page at this level or the index page at this
level is missing, we have the option to allocate them and fill them in. So, that's what the last
parameter alloc is all about. And if it's true and we need to, then we will allocate and initialize
index pages in the tree.

So, here is the uh code for this function.

And we see that we are passed a pointer to the root of the page table, the virtual address, and this
boolean alloc.

First, we check to make sure that the virtual address is not exceeding the maximum allowable number
and if so, we print an error message.

Okay, so this is organized as a a loop here, a for loop, and you can see that level starts at two
and it's decremented. And So, it only iterates twice. The first time level will be equal to two, and
the second time level will be equal to one, and then it exits the loop and uh does this last thing.
So, what the loop is going to do, it it's pa- it starts with

page table pointing to one of these index nodes, and the loop body will go down, grab this pointer,
and chase it down here, and at the end of the loop, at the beginning of the iteration of the next
iteration, page table will point to this node.

Okay, so in the first iteration, it moves page table from here to here, and in the second iteration,
it moves it from from here to here, and then finally, uh we go down and we get the pointer to this
page table entry.

Okay, so let's take a look at that in a little bit more detail.

We have another variable called PTE. So, you can see the first thing we do is we um ex- extract the
uh index field. Now, uh remember that a uh virtual address uh looks like this. Okay? It has three
fields for the level two, level one, and level zero index, as well as the um 12-bit offset. And we
uh uh extract We start by extracting the level two index here.

And uh then we um index into the page table. And uh we use that to index in here and get this
pointer. And that's what we do here. PTE now points to this this page table entry. And then um here
what we do is we look at the the page table entry. We fetch that page table entry and get a pointer
to the next one. So, that's what's going on here. We fetch the the page table entry and that that

gives us a pointer to the next one. And we set page table to that. Then we iterate. Okay, let's look
at that in a a little bit more detail. So, here we see a call to this macro function called PX.

And what does PX do? Well, here's another picture of the virtual address with the nine-bit fields.
And PX is passed uh this virtual address plus a a level number, two, zero, or one. And then it
extracts those nine bits and moves them down uh here. So, it returns a number between zero and 511,
which we can use to index into the uh index page.

So, PX first we extract level uh uh the field for level two. And we use that as an index to index
into uh this page, and that gives us a pointer to this page table entry here.

And we uh fetch it, and um then we use uh the page table entry, we fetch the page table entry itself
right here, and call this function PTE to PA. And I have a picture of that uh right here.

Here's PTE to physical address. And the page table entry consists of 44 bits, which is the pointer,
plus 10 bits, the valid bit, read, write, execute, and accessible in user mode.

So, it's got 10 10 bits here, of which uh a few of them are unused. It extracts this 44 bits, shifts
it a little bit, and adds in zeros here to give you a physical address. So, PTE to physical address.

There's another function, which we'll come to in a second, called physical address to PTE, which
basically takes the physical address and creates a page table entry.

But, here we are taking the page table entry and turning it into an address, and setting page table
to point to it.

So, in the first iteration, we um go down here, and we fetch this this this PTE This is the PTE, and
we build a pointer, and now page table points to the next one. And then uh we check it. Uh here, uh
before we do that, we check the uh page table entry, and we check the valid bit, okay? So, that's
what's going on here. If the valid bit is set, then we can go ahead

and access it and use it as a pointer.

If it's not set, then we've got a problem. We may have the this page table entry may not be valid,
and we may have to allocate this index page right here. So, when we fetch the page table entry right
here, we check it, and if the valid bit is one, then we can go ahead and get the pointer to the next
index page. But if it's not one, then we may have to allocate. So, um we first check our

allocate parameter here.

And if it is false, then it's this not alloc will be true and we immediately abort by returning a
null.

Um but if it is true, we keep going and here we see the call to K alloc. So, uh we are Let's assume
that we on the first iteration of the loop had a problem. So, that means this was not a valid page
table entry. So, we're allocating the index the level one index page with this K alloc.

And uh So, we do that and we now have a pointer to the next one. So, we set page table to point to
the level one index page that we just allocated. If we had a problem and it returns null, then again
we return and abort uh here by returning zero immediately. But if the K alloc worked, then we
initialize the new index page to all zeros.

The call to memset here will initialize the new level one index page to all zeros. So, that means
all the valid bits for all the page table entries in this would be zero.

And um then we build the page table entry.

We build this page table entry here and store it. Okay, remember it was not valid before and it
contained no pointer. But now what we're going going to do is we're going to take the pointer to the
new page we just allocated.

That's page table.

And we're going to convert it physical address to page table entry. So, that's um Whoops.

That's this page here uh showing that we have a physical address and physical address to page table
entry will convert it to a page table entry by shifting it and setting all these bits to zero.

Uh but we don't want them all to be zero. We want the valid bit to be set.

So, here we are oring in the valid bit, and we store that. Okay? Uh we store it right here. Okay?
And now we have page table pointing to this new index page, and we've uh we've we've uh fixed the uh
pointer there.

So, then we iterate, and we may have to uh allocate another page. Um if we've allocated this one,
then obviously the next one will also need to be allocated.

But in any case, we iterate, um and in the second iteration, we index down here, follow the pointer,
and end the loop with a pointer to the level zero index page. So, when we end the loop, we've got a
pointer to the level zero index page. And now what we do is we extract with our PX function the
level zero field. So, here's the level zero field. We extract those nine

bits, and that gives us uh the index into Okay, here's the level zero page.

We we use that uh last field to index in there, and that gives us a pointer to the page table entry.
And that's what's going on right here, and we return uh the address of that page table entry.

We just looked at the function walk, which is used to walk the uh the page table and allocate index
pages as necessary, and find a pointer to the page table entry in the level zero index page.

Now we're going to look at the function map pages, which is used to fill in and create this page
table entry.

So, map pages is passed a pointer to the root of the page table, a virtual address, the size, and a
physical address, as well as a collection of permissions.

And the virtual address, along with the size, will determine how many virtual pages are to be added
to the virtual address space.

And we assume that PA points to a physical address where there are the the same number of blocks of
physical memory uh pre-allocated and and to be used. So, what this function will do is it will add
page table entries so that each one of these virtual uh pages points to the corresponding physical
page in memory.

And each page will have its permission set identically according to this last parameter, which can
set the pages to readable, writable, executable, or accessible in user mode.

Okay, so here is the uh function for uh map pages.

I want to start out by saying uh it creates page table entries for the virtual addresses starting at
the virtual address um that refer to the physical addresses starting at PA.

It says VA might not be page aligned. It turns out the way that map pages is used in XV6 is such
that VA is always exactly page aligned.

Size is in bytes, uh and it returns zero on success, minus one if there's a problem.

Okay, so uh here we have the arguments, pointer to the page table, a virtual address, size in bytes,
physical address, and the permissions that these pages should have.

We start by making sure that size is not zero, so a quick error message there.

Um as I said, the virtual address will always be page aligned, so this call to page round down will
not actually do anything.

It will just copy the eight into the local variable A.

And A will then increment through the virtual pages one by one.

And PA will increment through the physical pages one by one. So, here's the loop executes once for
each page and we see at the bottom of it it's incrementing A by page size and physical address also
by page size.

Before we start the loop, we compute the address of the last page. So, we take the virtual address
plus size minus one.

So, that's the last byte in the range of virtual memory. And by rounding it down, we determine which
page that last byte is on. So, here we check to see whether the page that we just added was the last
page and if so, we terminate the loop and then return zero.

So, what do we do for each one of these virtual addresses?

We call walk to walk the page table allocating any index pages as necessary.

So, we see that the alloc parameter to walk is set to true.

And if there's a problem and that returns null, then we immediately return minus one. But otherwise,
we get a pointer to the page table entry. PTE here is a pointer to the page table entry.

That page table entry had better not be valid. So, here what we're doing is we're testing the valid
bit.

And if that is set to true, then we call panic and there's a problem.

But otherwise, we call this function PA to PTE. And that's a macro preprocessor function.

And that takes the physical address and it builds the page table entry.

So, uh here is a picture of the uh physical address and if it's a page-aligned address, which PA
will be, then the offset will be all zeros.

At PA physical address to page table entry will ignore these bits anyway, but what it'll do is it'll
extract the 44 bits here, shift them, and it'll set these bits to zero. So, these are the um read,
write, executable, valid, and the user bit. And those are all set to zero uh by PA to PTE.

Uh and then we OR in There we go. Then we OR in the permissions that we are supposed to have, that
is from the last parameter perm here, as well as setting the valid bit. And then we store this
newly-built page table entry in the uh level zero index page where uh it's supposed to go.

And then we just repeat that for each one of the virtual pages, and finally uh exit and return zero.

Next, we're going to look at the functions that build the kernel's page table, and they call map
pages, but they use this little wrapper function KVM map. All it does is call map pages, and you can
see that the parameters are the same, the page table, the virtual address, the physical address, the
size.

Okay, they're not in the same order, and the permissions.

And if there is a problem with map pages, this thing immediately invokes panic.

Next, let's take a look at the function KVM make, which is going to create and initialize the
kernel's page table.

It will call KVM map to do the work, and that's a little wrapper function that we just looked at
that does nothing more than call map pages.

This function will create direct mappings for the physical memory, the IO devices, and it will also
map the trampoline page, and it will create uh mappings for pages for the uh stacks for each of the
different processes. I'll I'll talk more about that in a second. But, basically, uh if you remember
this picture here, it's going to create the mappings that are

described here. This is physical main memory here, and this is the kernel's virtual address space.

We see the uh IO devices here.

We see the kernel's uh code and data, and we see the physical RAM here, and those are direct mapped,
and uh we see some other stuff. So, let's get uh into the code here.

So, here is the code for KVM make.

It's going to uh re- create the page table. So, here it allocates the top-most index page, the level
two index page, and with memset, it zeros it out, so it has no entries, and uh it sets uh that local
variable uh kernel's page table, and that will be used in all the calls to KVM map.

the uh final bit of this function, it will return a pointer to that.

So, let's um see here, we've created the uh top-level index page and zeroed it out, and then we
start calling KVM map to add the mappings. The first one is for the serial device, the uh universal
asynchronous receive transmit unit, and it's direct mapped, so the virtual address is here, the
physical address is there, and it's only one page in size, so the page size is just the 4K page

size, and it will be marked readable and writable. These are the permissions.

And uh so, that creates this mapping, this mapping here.

And then, in the next line, we, uh, create the mapping for the disk device.

Again, one page, readable and writable.

And so, that's the disk page here, uh, single page over in physical, uh, the physical address space.
And then next, we have the, uh, platform level interrupt controller. Um, and it's mapped direct. Its
size is a little bit larger. It's it's in fact 4 MB.

It's direct mapped into its addresses.

Again, readable and writable.

Um, now, let's take a look at the kernel. Uh, the kernel has been loaded into physical memory, and
um, the address at which it's loaded is called, uh, kernel base. And there's another address called
e-text, which is where the data begins. And so, uh, first of all, we create a mapping for the code,
uh, portion, which is readable and executable, but not writable.

And that goes from kernel base to, uh, this address here, e-text. So, that's what's going on here.
Uh, the virtual address and the physical address are one in the same. It's a direct mapping, and the
size is e-text minus the kernel base. And again, it's readable and executable.

Uh, then, the remaining the remainder of physical memory, uh, from here on up to the top of physical
memory, versus top, um, will be mapped readable and writable. And that will include the kernel's
data, as well as, um, the remaining memory, which can be used for the, which will be used by the
kalloc and kfree routines. So, um, here we're mapping, uh, that. We're giving the

virtual address as e-text. The physical address is the same, and the size is, uh, the from the top
of memory down to e-text.

And it's readable and writable.

Next, we're mapping the trampoline page.

And this is not direct map. Instead, um the virtual address will be trampoline, which is the address
of the highest page in the virtual address space. So, it's this this address right here.

And it will be mapped to the physical address of where the trampoline code is.

Um and uh it's a single page, readable and executable.

Okay, uh next uh going to the next page, uh we see uh the last little bit of this function we call
proc_map_stacks.

Now, let me go back to this page here, or this diagram shows over here that this is the virtual
address space of the kernel's address space. And we see in the topmost page, the trampoline page.

And then we have a number of pages, one for each of the 64 processes, and these will hold the stacks
for each one of the processes.

They are each a page in length, and they're each separated by a single guard page.

We have a preprocessor function called kstack.

And it's passed a number 0 through 63, and it will return the address that is the virtual address of
this page. So, for example, if we call it with two, it would return this address this virtual
address right here. If we call it with one, it will return this address here. So, it tells us where
these pages go in the virtual address space.

So, now we're ready to So, we In this function, we call proc_map_stacks.

Up until now, all this code has been coming from vm.c.

But, proc uh sorry, proc_map_stacks is in the uh file proc.c.

So, here it is. Okay, so it's passed a pointer to the kernel's page table. It's not going to return
anything. It's just going to update that page table.

So, it's going to iterate uh the number of processes happens to be 64. And we've got this proc array
with a pointer which is a number of proc structures. And we're basically just going to alloc to
iterate through that array. So, that's what's going on with this loop here. We're iterating through
each of the 64 proc structures. And for each one of them, we're going to allocate a page,

okay, in physical memory. That is, we're going to allocate a page in the Kalloc region somewhere, a
physical page, for the stack.

But then we're going to map it somewhere up here. And here we see that same picture with the
trampoline page and the uh stack pages separated by guard pages.

Okay, so what we're going to grab a physical page, okay, from the free memory, so up here somewhere.

And then we're going to create a mapping from that to there. Now, these arrows here, um I don't like
these arrows because they're pointing someplace else.

They actually should be pointing into the uh free memory region here.

Okay, so uh here we are allocating for each proc for each uh process uh a a page. And then uh if
there's a problem, we panic. But if not, we um This This actually computes the number um here. It's
kind of a funny way C works, but uh we're computing the number 0 to 63 and we're calling Kstack to
get the address, that is, the virtual address where we want to map it. And

then we call KVM map giving it the virtual address that we just computed, as well as the physical
address of the page itself. Uh it's one page in length, and it will be readable and writable.

Okay, that wraps it up for KVM make.

Next, let's take a look at two short functions, KVM init and KVM init hart.

I'll just go straight to the code on these.

KVM init is called once by core zero during initialization. So, it's only called by one core, and it
calls KVM make to create the kernel's page table and saves a pointer to it in this global variable
kernel page table.

Then, when during as part of the initialization process, each core will call KVM init hart. And what
that will do is it will set the control and status register called SATP. So, this is writing to that
SATP register the address of the kernel's page table.

That will effectively turn on paging and install the page table, if you will.

Make SATP um is just a little thing that mangles this pointer a little bit into the format that the
uh RISC-V processor requires.

And we also see uh fence instruction to make sure that uh anything that we do that we should do
before we uh change this control status register is actually done before that, and anything that
must be done after that is is actually done after that.

Next, let's look at this walk address function.

It's passed a page table, and it uses that to map a virtual address into a physical address.

Uh the virtual address must be valid, and it must have user permission set.

Uh this virtual address has to be a page-aligned address, and it returns the address of the physical
page where that's located in memory. And if there is any problem, it returns zero. So, now let's
look at the code for uh walk address here.

Um and you see we we have an error check. If the virtual address is too large, we just return zero.

Then we call the walk function with the virtual address to get a pointer to the page table entry. Uh
we do not allocate uh index pages if they don't exist.

Um if it returns something valid, then okay, we proceed. But if it returns null, we immediately
return zero.

Then we check the valid bit to make sure that it is set. If it's not set, we return zero. And we
check the um bit to see if it's accessible if that page is accessible in user mode. And if that is
not set, again we return zero. And finally, we call this uh little PTE to physical address function.
Uh we uh retrieve the page table entry here and uh extract the

pointer to the physical page and return the physical address.

The function UVM create is used to create and return an empty virtual address space. So, it creates
a page table with only the root, that is the level two index page.

So, here's the code.

UVM create returns a pointer to a page table.

Uh it's only used to create virtual address spaces for uh user processes, so that's why it's called
UVM create. And you can see it allocates a page here, and that will be the level two index page, the
root page. If there's a problem, it returns null.

Otherwise, it calls memset to initialize all the page table entries in the root index page to zero
and returns a pointer to that page.

The UVM init function is used to create the virtual address space for the first process.

So, the first process will contain code that comes from init code.s. It's assembly code.

But basically, it's a very short program that does an exact system call and invokes another program
and gets things rolling.

Uh this assembly language program is assembled separately and we extract all the bytes. There are
actually 52 bytes.

And in hex, they are specified directly in the kernel code in this array of bytes called init code.
So, what this function is going to do is allocate a single page and it's going to then copy these
bytes into that page.

Uh so, it takes a parameter, source and size, which points to these bytes and the size is the 52,
the number of them.

And page table. And it will allocate one page, map it into virtual address zero with read, write,
executable, and user mode permissions. And that then that program has then been loaded into the
first page of the virtual address space.

So, here let's take a look at the code for UVM init.

Uh we see it's passed a pointer to a page table and a pointer to the bytes, the 52 bytes, as long
along with their size. And what does it do? It makes sure that they're going to fit into a single
page here, and if not, it panics. And then it allocates a page of physical memory by calling KAlloc.

And it sets all of that page to zeros.

And then it maps that page. So here is the pointer, the physical address of the page that we just
allocated here.

And it's size is zero It is page size here, and we're mapping it into virtual address zero with the
call to map pages.

And we're passing it the permissions of write, read, executable, and accessible in user mode. And
finally, we are moving this number this these 52 bytes from wherever they are in this array, this
initcode array.

We're moving them into the page that we just allocated. So here we're moving them from source to
that page.

By the way, the virtual address space for the initial process is quite different from the virtual
address spaces for all the other processes. We sort of cheat a little bit. Recall that a virtual
address space normally looks like this with the stack down here and code and heap and and so on.
Well, with the initial process, we just have a single page allocated at location zero in the

virtual address space. So we only have one page, and we load into that page the code for the initial
process.

That's those 52 bytes from the initcode.s assembly code.

And we also use that same page for the stack. Of course, every process will need a stack, and we
will position the stack pointer at the very top of that page. So, the stack doesn't have any more
than one page to grow downward. Of course, it really won't need that. But, in any case, that's how
we set up this initial process's virtual address space.

It's got a single page marked read, write, executable, and accessible in user mode. And the vast
majority of that space is just unallocated. Of course, every virtual address space for user process
will need a trampoline and a trapframe page.

Now, let's take a look at UVM alloc, which is used to grow a virtual address space.

Here's a picture of a virtual address space. And as the process executes, it may want to enlarge its
heap area. We have this point called a break point.

More precisely, it's the address of the first byte beyond the heap. So, since the virtual address
space starts at zero, the break is actually nothing more than the size of bytes that we've
allocated.

And what this function is going to do is going to allow us to grow the size of the virtual address
space. So, we're going to go from some old size to some larger new size.

The break point need not be page aligned.

And so, here I'm showing an old size that happens to fall in the middle of a page. And we might want
to grow it. The user might want to grow it to some new location, to some new size. It's also not
page aligned.

So, what we're going to have to do is we're going to have to increase this old size to the next page
boundary. And then we're going to say, "Is it less than the new size?" No. So, we're going to have
to allocate this page here, this page right here.

And then is it increment it to the next page boundary?

And we're going to say, "Is that Have we exceeded new yet?" No, we haven't. So, we're going to have
to allocate this page. And so, we're going to allocate two pages here. The new size will still be uh
set to here, but we will go ahead and allocate two pages.

So, that's what UVM alloc does.

Pass it a pointer to a page table, the old size, that is what it was before, and the new size. And
it returns the new size.

Uh the uh pages are added to the virtual address space at it calls K alloc to get new physical
pages, and it zeros those out, and it call calls map pages to add them to the page table. Uh the new
pages will be marked as read, write, executable, and have user permission set. And as I said, the
old and the new need not be page aligned.

Um if there are any problems, uh it will basically undo everything it did. So, it will call K free
to uh deallocate um what it's what it's allocated, and it will also use UVM dealloc, um which I'll
talk about in a second. But, uh it will return zero if it fails to actually enlarge the uh address
space and instead leaves it just the way it is.

So, let's take a look at the code for UVM alloc here.

We're passed a pointer to the page table, uh the old size, and the new size, and we're going to
return the new size.

First thing we do is we do a check to make sure that we are trying to grow the uh address space.

And if we are not trying to grow it, we just return the old size.

Then, uh we round the old size up to the next page boundary. So, that's what I talked about here,
rounding it up to uh the next page boundary. So, page round up.

And then we're going to walk through the pages. In my example, I did this page and this page, so I
did two pages.

So, A is going to start out at old size and we're going to increase it by page size until it exceeds
new size.

And so, that's what's going on with the loop here. So, each iteration of the loop will allocate one
new page and add it to the virtual address space.

So, the first thing we do for each page is we allocate a page of physical memory by calling kalloc.

And if there are problems, if kalloc has no more memory, it'll it will return null and we will then
have an error situation. So, I'll describe UVM thealloc later, but basically it undoes everything
and and um this is the new size here. So, we revert to whatever the old size was.

Uh so, this is the the current size and for the call to UVM thealloc is the the current size and
this is the new size.

And so, what we've done is we've uh managed to get this far as A here and we then retract everything
or undo everything and return zero. But, if kalloc succeeds, uh then mem will not be null and so,
we've got a page. So, we uh zero it out by calling memset. You know, we don't want any data from
some previous page which is which is which was in some other processes virtual address space

perhaps to uh be We don't want the data on that page to become visible to the new process because
that would mean data leak leakage and it would be a security violation. So, we zero out that page.

And then we add an entry to the page table for that virtual address. So, A is the virtual address
here that we're adding. So, A is the uh virtual address first here and then on the next iteration
here.

And we call map_pages to add uh that and the physical address is the uh address of the page we got
from K Alloc. So, that's the physical address.

Of course, it's 4K bytes. And we pass in permissions to make this page readable, writable,
executable, and accessible in user mode. And if everything's okay, then we go on to the next page.
However, if there's a problem, then we free that page that we allocated here, okay? With a call to K
Free. And then we also deallocate all the other pages. So, again, we call UVM Dealloc to undo

everything we've done so far and return zero.

Um when the loop finishes, everything's okay, we return the new size that we were passed.

Before we look at the UVM Dealloc, let's look at UVM Unmap.

It's passed a pointer to a page table, as well as a number of virtual address pages. So, virtual
address here is a page-aligned address, and we have the number of pages that need to be unmapped.

And for each page, it will locate its page table entry and clear the valid bit.

In addition, there's another parameter, do free, and if that's true, then this function will call K
Free on each of the data pages and return them to the data the free data pool.

Okay, here's the code.

Remove in pages of mapping starting from the virtual address VA, which must be page-aligned.

And the mappings must exist.

And optionally free the physical memory.

So, here's a pointer to the page table, the virtual address, the number of pages, and the boolean.

And the first thing we do is make sure that VA is page-aligned, and if it's not, we call panic.

Then, what we're going going to do is we're going to iterate through all of the virtual pages. So,
we start with the virtual address VA and we successfully increment it by 4K by the page size until
we uh reach the limit. In other words, VA plus the number of pages times the page size.

So, for each virtual page, which has a virtual address A, what are we going to do?

Well, we start by calling walk and here's the pointer to the page table in the virtual address and
we and walk will return a pointer to the page table entry. PTE is a pointer to the page table entry.

Uh if there's any problem with that, it'll return null and we panic. The pages that we are unmapping
had better be mapped.

Uh so, the next thing we do is we retrieve the page table entry and we check the valid bit. And if
it is zero, uh then that page is not mapped. That's a problem. It had better be one.

Next, we look at the page table entry and this function here PTE flags will isolate the the flag
bits.

And uh the flag bits, you know, the readable, writable, executable, user mode, and valid bits.

Uh we are we check to make sure that the valid bit is one. And here we're checking to make sure that
something else is one besides. It better not just be one. And this says the message is uh that it's
not a leaf. So, exactly what's going on there? Well, let's uh look at this uh pay picture of the
page table. And here we have a page table entry pointing to another index

page and here we have a page table entry pointing to another index page. And here we have a page
table entry pointing to a data page.

Uh in the case of these page table entries, they had better be valid if there is in fact a pointer.
And the other bits, the readable, writable, executable, and user mode bits had better be zero.

Down here, if the page table entry is valid, then it had better point to a data page, and that data
page needs to be at least readable or writable or executable and have the user mode bit possibly
set. So, the other bits besides the valid bit can't all be zero. So, that's what we're checking for
here. We are making sure that there's something some bit besides just the valid bit that is

uh set.

And so, what we're going to do with this page table entry is just set it to zero, which will clear
the valid bit among other things.

And so, that's what we do here. But before we do that, we ask whether we are required to free the
data page, and if so, we use this page table entry to physical address to extract the pointer to the
actual page in memory.

Uh here, remember this picture. This is the page table entry, and we're using the PTE to physical
address, and it's going to ignore the bits and do a little shifting and replace that offset with
zero to give us an address that we can use in the call to K free. So, here we are using that
physical address that was so obtained and returning that page to the free

pool.

Next, I want to talk about UVM dealloc.

Uh previously, I talked about UVM alloc, and remember it's passed an old size and a new size, and
it's essentially used when the user process needs to grow the virtual address space. So, it needs to
increase the break point.

And so, UVM alloc adds pages to the virtual address space. And if anything goes wrong, it calls UVM
dealloc to remove pages. So, it has it can have a problem and returns zero.

UVM dealloc will not fail.

So, it's passed the old size and the new size. And so, here's an example there where we are taking
the virtual address space and shrinking it. So, in this particular example, we are removing two
pages. Old size and new size need not be page aligned.

And we will also call K free to get rid of the data pages.

So, when we call UVM unmap, we'll pass it the do free parameter being true.

So, the way it's going to work is it's going to take the old size and round it up to the next page
boundary here, and round new size up to the next page boundary here. And then it's going to subtract
them to determine the number of pages that have to be freed.

So, let's take a look at the code for UVM dealloc.

It's passed the pointer to the page table, the old size, and the new size.

First, it checks to make sure that we are decreasing the size of the space. So, if new size is less
than old size, then we are shrinking the address space and we can keep on going. But otherwise,
we're not asking to shrink the size, so we just return the old size.

Um and we don't shrink anything.

Next, I think the best way to understand this is to look at the computation of in pages.

So, the number of pages, we round up the old size and we round up the new size.

Okay, so we round these numbers up here.

So, we have a pointer here for old size and a pointer here. We find the difference, that's the
number of bytes, and then divide by the page size to get the number of pages.

Here we are checking to make sure we get something.

Um if these happen to be equal, uh then we won't do this um because that would be a zero here. So,
we make sure that we have a positive number of pages here.

And then we call UVM unmap. Again, we uh round up the new size. So, we're going to be freeing these
two pages. So, we round new size up to this point and then we have the number of pages is two. So,
we pass it number of pages and this is the do free argument to UVM unmap to free the data pages. And
finally, we return the new size.

Next, I want to talk about UVM free.

This is used to basically get rid of a virtual address space. So, here's a virtual address space. Uh
it has some size which shows how many pages have been allocated down in this region.

And of course, it has the trampoline and trapframe pages uh mapped in the upper two pages of the
virtual address space.

What it's going to do is call UVM unmap with the parameter of size, and that's going to unmap and
free all of the pages that are here. So, all of the data pages will be returned to the free pool and
all of the page table entries that point to those uh data pages will be uh cleared out and made
invalid.

Uh it will leave the trampoline uh and trapframe pages uh in physical memory. It will not attempt to
return those to the free pool. Of course, we don't want to do that because they are uh in use. Um
and the they will the trapframe page has to continue to exist for this process for each process and
the trapframe page is of course shared by every virtual address space so we can't

free that page either.

Um then it will call this function free walk which will eliminate the entire page table.

Um so let's take a look at the code for UVM free.

Uh here we see it. We're passed a pointer to a page table as well as the size and what we're going
to do is call UVM unmap assuming there are more than zero bytes.

Um and we'll pass it the virtual address of zero and uh take the size and divide it by the page size
to get the number of pages.

This is the do free parameter that will out that will call K free for all of the data pages.

And then uh we call the free walk function.

Next, let's talk about the free walk function. It will free all the pages in the page table or more
precisely it will go through the page table tree and for each index page it will call K free to
return it to the free pool.

It does not free the data pages. It uh assumes that all the data pages have been previously freed
and all uh the entries the page table entries that point to data pages have been uh removed.

Uh so um I wanted to uh mention for the UVM free uh this uh frees all the data pages and clears out
the page table entries for all the pages below the size. It doesn't do anything for the uh two pages
at the very top. These pages are not to be freed because they need to be used.

And before we call UVM free, we will um separately call UVMunmap to remove the entries for the
trampoline and the trapframe pages without actually freeing them. And so, when we get ready to call
free walk, we can assume that there are no data pages in the page table.

The way this thing works is recursively.

It's a past initially a pointer to the root of the page table.

And for uh that for each page it's called on, it goes through and looks at all of the children and
calls itself recursively.

Each recursive call will completely free the entire subtree. And so, it it frees all of the children
and then finally it frees the top page.

Okay? So, now let's take a look at the code for free walk.

It's past a pointer to an index page.

And uh it goes through that index page. In the initially it's past a pointer to the root, but in the
recursive calls down here it's past pointers to it's past a pointer to the child index page.

So, it's got a loop. It's going to uh iterate through and look at all 512 entries in the index page.
So, here it grabs the index uh the ith uh page table entry and looks at it and calls itself
recursively on that child.

And then finally after it's uh called itself recursively on all of its children, it frees that node
itself.

Now, let me um mention here that uh remember that in the index pages uh in the upper pages here, we
we the valid bit being set. So, in uh level one and level two, the valid bit is set, but none of the
other flag bits, the readable, writable, or executable are set. They're all zero.

Whereas, down here, we should if we have the valid bit set, then we have a pointer to a data page,
and it must be either readable, writable, or executable, or or some combination.

So, we're assuming that all the data pages have been freed, and all of the page table entries for
them have been set to zero. So, they'll have a zero valid bit.

Okay, so you That's what's going on here.

We uh grab the next page table entry, and then we look at it.

Is the valid bit set? Okay, if the valid bit is set, then we're assuming that we point down to
another index page.

Okay, so we check here to make sure that the readable, writable, and executable bits are zero. So,
if it is valid, and uh the bits are all zero, then we've got a valid page table entry pointing down
to a lower level. So, we need to chase that pointer. And so, here we call the page table entry to
physical address, which turns the 44 bits, that shifts them, and

gives us a pointer to the child index page. And then we call ourselves recursively on that child.
And then finally, we set that page table entry to zero.

Um So, we have here a second check. We're asking whether we've got a page table entry that is
non-zero. Well, we're looking at the valid bit. If the valid bit is set, but one of these bits is
not is one, then this won't have been true. So, we're saying that if we have a valid bit, but we've
got readable, writable, or executable, then something's wrong. We should We We might have that
situation

where we've got a valid bit and a pointer to a data page with readable, writable, or executable set,
but we assume that we've removed all the data pages, so we better not have that situation. So,
that's what's going on here. We are We've detected page table entry that points to a data page here.

And the other thing I want to ask is I'm not sure it's really necessary that we zero out each page
table entry. We're going to be calling kfree on this page anyway, so there's not really any need to
zero out the entries as we process them, but I don't think that hurts anything.

Next, let's take a look at the function UVM copy. This is used in the fork system call.

Remember that when we have a fork, we need to take the virtual address space of the process that's
doing the fork and make an exact copy of that virtual address space, and UVM copy is what we'll use
to do that.

So, in this diagram, I'm trying to show what's going on. Here's the existing virtual address space,
and it's got a bunch of stuff in it up to size, as well as a pointer to the trampoline page and to
the trapframe page for this process.

And UVM copy assumes that we've got a second address space that's already created, and it assumes
that it has the top two pages mapped into the trampoline page, which is shared by all virtual
address spaces, and the trapframe page is allocated and somewhere else. And so that is mapped into
this virtual address space as well.

And what this function UVM copy is going to do is copy all pages up from zero up to size, okay, into
the new address space. So here I'm showing the new address space before this function, and here I'm
showing it after the function. So what it's going to do is it's going to make copies of all of these
data pages. So here it's going to make a copy of the page, and then it's

going to add mappings for each one of those pages to the page table.

And also the permissions that will be in the new page table will be identical to the uh permissions
that were in the old page table.

And if anything goes wrong, it's going to sort of free up all the pages that allocates and return an
error code.

Okay, it'll return minus one if there's a problem.

So So it's passed the old page table, the new page table, and the size of the old page old virtual
address space.

Here's the code. So let's take a look at this. Uh It's passed pointer to the old page table and the
new page table, as well as the size, and it returns zero if everything's okay or minus one if there
is an error.

So it's going to loop here through all of the pages from virtual address zero all the way up to
size, and it's going to go in increments of pages. So it's going to increment by 4K here until it
reaches or exceeds size.

And for each one of the virtual pages in the old space, um it's going to obtain the page table
entry. So, it calls walk. Oh, I forgot to bold it. Anyway, it calls walk on the um old address space
with the virtual address I.

And uh uh it gets a pointer to that um and PTE in this case is a pointer.

And if there is any problem, um it uh just calls panic. The page table entry should exist. And
furthermore, uh it should be marked valid. So, here we fetch the page table entry and check the
valid bit to make sure that it is not zero.

And then we proceed to look at the page table entry and extract the address. So, this is page table
entry to physical address. So, we extract the uh page number and turn it into a the a pointer to the
uh page to the physical page the physical data page.

And uh we also take the page table entry and extract the permissions. So, this extracts the uh bits,
the readable, writable, executable, user mode bits and puts them in flags.

Now, we're ready to copy the data page.

So, the first thing we do is call K alloc to get a new data page. So, for the first time we're
looking at this data page down here and we call K alloc to allocate a page here and then we're going
to copy the data. Okay?

So, if um let me just zoom out here. If we have any problem here, we're going to do a go to to this
label down here.

You don't see go-tos in C programs very often, but you know, here you have one.

Uh no comment about that. But in any case, uh we call KAlloc, and if everything's okay, then we copy
from the old page, right? We got the address of the old data page here, or the data page from the
old virtual address space, and we're going to copy it into the page that we just allocated with the
KAlloc here.

And it's a 4K page size.

And then we're going to add the mapping, so we have a pointer to the new page table and the virtual
address, uh the page size uh is what we've got, and we've got a pointer to the um the data page that
we've just created, as well as the flags. So, this tells us to add a mapping to the new virtual
address space for this page.

And if there's no problem, we keep going, but if there is a problem, we immediately free the page
that we allocated with KAlloc, and we go to air again. So, um then we keep looping like this through
all of the pages in the old address space. So, we loop through all of these pages, copying each one
of them and adding an entry to the page table.

And then finally, if we make it through all that without any problems, we return zero.

But if we have a problem, then we need to unmap everything and return minus one. So, this is the new
address space, and from virtual address zero, that is from the beginning, um through I, okay? I we
need the I is incremented by page size here, so it goes in units of pages, and so we divide by page
size to determine the number of pages to unmap, and we remove

them from the virtual we remove them from the page table, and the do free argument here indicates
that we return them to the free pool by calling K free.

So, if anything goes wrong, we basically undo what we've done and return minus one.

Next, let's look at the UVM clear function.

It's passed a virtual address, and it will mark the corresponding page in the page table as not
accessible in user mode.

It's used in only one spot, and that's for marking the guard page in a user address space as not
accessible. So, here's a picture of an address space.

Got the code and data. We've got one page for the stack, the user stack, and then we've got this one
page called a guard page here, and so UVM clear is used to mark this particular page as not
accessible in user mode. So, if the user program tries to access this page, then it would the the
XV6 kernel will catch that error and and throw that process out. And that would happen if

the stack, which is growing downward from this point, gets too big and runs into the guard page.

And so, the code for UVM clear is pretty straightforward. You might say it's clear.

Here's a pointer to the page table and the virtual address.

We walk the page table to find a pointer to the page table entry. This is a pointer.

And if there's a problem, we immediately panic because we are only calling this on the guard page
that should already be there.

And then we are updating the page table entry. So, what we're doing is we're ending it with um a
word that has all ones except for where the U bit is uh which has a zero there. So, that's a way to
clear uh this particular bit in the page table entry.

Next, let's take a look at the copy in function.

It's passed a page table that describes a virtual address space and a virtual address within that
space.

And it's passed as well a destination location and a length which is the number of bytes that needs
to be copied.

So, here in this picture is a virtual address space and the source virtual address is at the
beginning of the red zone here and the length is the number of bytes. And you can see in this
example, we are trying to grab a number of bytes and they overlap several pages.

And when we actually look at where these bytes are in memory, there's a virtual uh there's a page
table that maps each of these virtual pages into some physical page. So, for example, the first page
is is actually located right here.

And what we want to do is we want to copy these bytes which are actually located in physical memory
at you know, here, here, here, and here. And we want to copy them into some contiguous place in the
kernel's address space.

So, that's the destination location.

Okay, so we're going to copy bytes from the user space to the kernel area and we'll return minus one
if there's any kind of a problem. It may be that the user process gave us an invalid virtual address
um or so.

Let's take a look at the code now. This is uh I think kind of tricky code to to look at. So, um
let's see if we can work through this. Here's the page table.

And here's the destination pointer. Uh here's the length in bytes and the virtual address, the
source uh virtual address from where it's coming from.

So, first of all, we're going to copy in chunks and uh length is the number of bytes we have left to
go. So, while we have more bytes left to copy, uh we'll keep doing this loop. And so, each iteration
of the loop is going to copy a piece from one page. So, this piece, then this piece, then this
piece, and then finally this piece.

So, we will be decrementing length, okay? We will determine how many bytes to copy, and then we will
copy that and decrement length, and we'll increment the destination pointer uh by the same amount.

Um so, here you see the actual uh call to memmove, which does the movement from destination, and it
moves n bytes. So, I think we've taken care of destination and length.

And if we manage to copy all the pieces and exit this loop, uh we have no more bytes to copy, we've
decremented length all the way to zero, then we return uh zero. If there's any problem, we're going
to return a minus So, uh the tricky part is the source, uh and we have two um variables here, the
virtual address zero, VA0, and and PA0.

So, let's see how this works. We're going to start by taking the source location.

Let's go source uh VA here.

Um source virtual address, we're going to round it down to the nearest uh page boundary. So, that
gives us this virtual address. And then we're going to call walk address on that to turn it into the
physical page. So, that gives us the pointer to the page where it's kept in physical memory.

Uh we still don't know this address, but we know this address here.

And we want to copy this bit here first.

That's the first thing we copy.

So, um we check here to see whether we had any problem. For example, if that the virtual address is
not mapped or um it's not accessible in user mode, then we would have a problem here and we just uh
return minus one.

So, source VA minus VA0, what that's doing is determining the offset. Source VA is this this address
here and VA0 is this address. So, we're returning we're computing the offset here. So, that's what
what what's what's happening here.

And um page size uh is the amount remaining. So, that's the amount we have to copy, okay?

So, we've determined N, the amount we have to copy. Now, uh we check to make sure we haven't
exceeded the length, okay? So, in this case we haven't for the first uh chunk that we have to copy.
Um but if we do, we we shorten N to the length if it So, we don't want to copy more bytes than we
are required to copy.

Now, we have to compute where to copy from. So, we take the physical address, that is the address of
the uh physical page where the memory is located, and then we add to it source VA minus VA0. That's
this offset right here. So, we're we're taking this address here and adding this offset and that
gives us the location here.

So, now we're ready to do the copy and we do the copy.

And then um here we are incrementing source VA to um the next unit. So, VA zero is here and we're
adding 1K to it. So, now we are are pointed here. Okay, we've copied this much and we're now pointed
to this place and we're ready to iterate the loop.

And I think that uh covers it. Uh that is copy in.

Next, let's look at copy out. We just looked at copy in and copy out does uh the same thing in
reverse.

Okay, so uh copy in takes uh string that's spread possibly spread across several pages in the
virtual address space and copies it to some uh contiguous locations in uh the kernel's address space
and copy out does the exact reverse. It takes uh something in uh a buffer in in the kernel's address
space and moves it into the virtual address space spreading it across uh several pages as necessary.

So, you see that it uh is passed a source location and a length in bytes and it uh copies that many
bytes to the destination virtual address in the virtual address space.

Copies bytes from the kernel area to the user space returning minus one if there is any problem.

And we went over the code for uh copy in and the code for copy out is uh really uh almost identical.

Um so, here's copy in.

I I just want to like compare these two.

I just line them up. You have the exact same number of lines. Um the we can see copy in and we've
got the while loop and we've got the page round down and walk address and check to make sure PA0 is
not zero and computation of in and adjustment for length and then the memory move and then uh
updating of the variables and then returning zero. So, in copy in we see

destination. Here it's changed to source. And here we see the source virtual address and here it's
changed to destination virtual address.

And we go when we go through this we see that that's really all that's happening.

Source virtual address changed to destination virtual address.

Um and then everything's the same.

Here the computation of how big the chunk we want to copy is.

Okay, this is the offset and this is subtracting from the page size as a whole and that's just uh
source virtual address is changed to destination virtual address.

And then in the move here we were copying it from the virtual address space to the destination.

Um and here we're copying it from the source from the kernel's address space and the destination is
then this chunk of the virtual address space. And again we see destination changed to source here.

And source virtual address changed to destination virtual address. So, there's a a nice symmetry
there.

The last function I want to look at is copy in string.

So, to motivate it imagine that you've got system call such as open that provides a file name. That
file name will be located in the user's virtual address space, and presumably it's a string with a
variable number of characters terminated with a null character.

And the kernel needs to get that file name, and it's wants to it'll need to copy that string into
some buffer. Well, the kernel will have a fixed-size buffer, and we don't know exactly how many
characters will be in that string before we hit the final null. We don't want to allow the kernel to
overrun the buffer.

That would be disastrous, and so we need a slight variation on this copy in. So, we want to have a
version of copy in that will copy up until the null character, but won't overflow the buffer. So,
copy in string is passed a page table, and the pointer to a pointer to the the destination area, as
well as the max length. So, how many bytes we have in the buffer to copy to.

And the source from where in the virtual address space the characters will be coming.

And what it does is it it's like copy in. It copies bytes from the user space to the kernel area,
but it stops copying after the null character. And if the null byte is not reached before we copy
max length characters, it stops, so it doesn't overrun the buffer. And it also returns minus one in
that case to let us know there was an error.

So, now let's look at the code. And first I want to look review copy in because it's really quite
similar.

So, we've got you know, this stuff here is is pretty much the same. It's the memory move where
there's a difference. Okay, so uh take a look at copy in string.

Again, it's a pointer to page uh pointer to the page table, uh the destination area, uh the source
virtual address, and the maximum number of characters to copy.

So, we've got a while loop here, and uh instead of length, previously we had length and the while
loop uh copied until we had length characters copied.

Here we have max Here we have max, and so the while loop copies until we exceed max. And so, we will
be decrementing uh max uh as we go, and uh as long as it's uh greater than zero, we've still got
more characters to copy. But, uh we've also got a flag here called got null.

Initially, we haven't seen the null byte at the end of the string, so it it's uh initialized to
false.

And the minute we do see the null byte, we'll set that flag to true, and that will cause this loop
to terminate.

And um then uh if everything went well, and we've we saw the null character, we return zero, but if
we didn't see it, we return minus one.

Um So, uh this part here is pretty similar to uh copy in string. So, let's just take a quick look at
that. Uh we are try to line these up a little bit better. Uh we are uh copying we are rounding the
source location, the source virtual address down to the nearest page, and we're figuring out uh what
is the physical address of that page in in memory, and uh we're checking for an

error here, and then we're figuring out how many bytes to copy in that first chunk, and possibly uh
making sure that we don't over copy here. Um but, then we hit the mem move and that's replaced by a
loop that copies individual characters.

So, um in in copy n, we are copying from this location, physical address plus the offset here.

And so that's what we're doing here, physical address plus the offset. And that's where we copy
from. And so P is going to be our our pointer.

And instead of just uh copying n bytes here into destination, what we're going to do is a a while
loop and we're going to copy we're going to try to copy n bytes in this while loop. And we um are um
looking at the the uh characters are coming from the pointer P. So, we see if we've got uh the null
character and if so, uh we copy it uh to the destination and we set our flag and we are done with

this loop. Um but uh if uh uh we uh don't have the null, then we just copy the byte and then we uh
decrement our counter n, okay? And we also decrement max, which is the number of characters we have
to go, incrementing P and incrementing the destination pointer. So, we're doing these um by one
here. Uh of course, we do the loop uh a bunch of times n times. Here,

uh when we adjust the pointers, we do it uh all at once. Uh we did n uh iterations here, we copied n
bytes, so we can just adjust the the length in the destination uh by n all at once. Uh so, here
we're uh adjusting the destination one by one and the length uh decrementing it one by one.

And uh but then uh we sort of continue the same way. So here in copy in we are figuring out the
virtual address of the next chunk we need to copy and we do that the same way here. And so I think
that finishes it and that is all that we have for the file vm.c.
