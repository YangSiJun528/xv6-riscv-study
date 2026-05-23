# xv6 Kernel-5: kalloc Mem Management

| Field | Value |
| --- | --- |
| Video ID | `wPXE08HBNnE` |
| URL | <https://www.youtube.com/watch?v=wPXE08HBNnE> |
| Source | `docs/reference/scripts/original-raw/05-xv6_Kernel-5_-_kalloc_Mem_Management-wPXE08HBNnE.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to look at
memory management and how the xv6 kernel manages free memory it's pretty straightforward and simple
all memory management is in terms of 4k blocks or we can also call them pages so four kilobyte pages
and they're maintained on a free list we have two important functions kealic

and kfree which just take things off of the free list in the case of k alec or add a free block back
to the free list in the case of k-free and that's about all there is to it but let's go into this in
a little bit more detail uh first of all uh the all this is coming from the file k alec dot c and uh
here is the file we've got some includes uh here is our pointer to the free list

and we've got this little structure here called run which is just a pointer to the next block so
free list is a pointer to a linked list of four kilobyte pages this structure here groups the free
list with a spinlock called simply lock this is used to associate the lock with the free list
whenever we manipulate the free list we need to grab the lock we need to acquire or set if

you will the lock and then when we're done looking at the free list we need to release that lock
we've also got a variable here this is set by the linker when the program is being built and it's
set to point to the first address after the kernel the kernel's executable code its text section and
its data section and this is the first usable memory and we'll use that in initialization

so let's just look at the initialization here we initialize the lock and then we call free range to
free everything between the first available byte and the top of physical memory which is a constant
our happens to be 128 megabytes okay let's uh look at the allocation routine what it does is it
acquires the spinlock here manipulates the free list and then releases the lock and finally it
returns

the pointer okay so in more detail it grabs the first thing off the free list if it got something
that is if memory is not exhausted then it can keep going and what it'll do is modify the free list
to point to the next item so if the free list points to this item we grab it and then redirect free
list to point to the the second what was the second item on the

list finally we return after releasing the spinlock we return the pointer before we return it if we
got something then we set every byte in the page to this value of five just filling it with junk
here is the routine k free and it's passed a pointer to a page and what it does well it starts off
with a little bit of error checking make sure that this pa physical address

is aligned on a page boundary it makes sure that it's not too small and not too large okay so it's
not below the starting address and it's not above uh the ending address and if that's okay then it
fills the page it starts by filling the page with junk in this case junk happens to be one and the
purpose of that is to hopefully bring out or or trigger any bugs in the

uh rest of the kernel code we don't want to use a page after we return it to the free list so by
setting every byte in it to some value we can hopefully confuse any program code that tries to use
data from that page then we acquire the we're going to use r as our pointer we acquire the um
spinlock we manipulate and we add it to the free list and then we release our spinlock

so adding it to the free list if our is if this is our free block here then we simply set the next
pointer of it to point to the first element of the free list and then we redirect the free list to
point to this now let's go back and look at the initialization again we are initializing all of
memory from this starting point to the physical top the top of physical

memory and we have a function here that will do that it starts by it's past this starting address it
rounds it up to the nearest page boundary because it will not necessarily be page aligned and then
it does a loop and what it's doing here is it's adding each page to the free list by calling k free
and it's just a loop here incrementing p by 4 096 bytes until

um p finally exceeds the end point
