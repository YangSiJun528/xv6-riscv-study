# xv6 Kernel-8: RISC-V Page Tables

| Field | Value |
| --- | --- |
| Video ID | `g7B0WS5Xu-A` |
| URL | <https://www.youtube.com/watch?v=g7B0WS5Xu-A> |
| Source | `docs/reference/scripts/original-raw/08-xv6_Kernel-8_-_RiscV_Page_Tables-g7B0WS5Xu-A.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to talk
about address translation and describe the page table architecture that's used in the RISC-V
processor at least as far as we need to know to understand the xv6 kernel the register that we need
to know about here is satp the supervisor register for address translation pointer

it is a control and status register and it will be set to point to the page table page tables are
kept in main memory and at any one time there is one page table that is in use and that is the one
that's pointed to by the satp register virtual address translation in the RISC-V core is always
turned on that is after initialization is complete so initially satp is zero and when it's

zero then there is no translation occurring but in the initialization phase we will set satp to
point to the page table that we want to use it's always turned on when we're running in supervisor
and user mode it's not turned on in machine mode there are several page tables there is one page
table for the kernel all cores will share that page table and it provides a one-to-one mapping uh

pretty much one-to-one there are a couple of exceptions but it will map all of physical memory so
that the kernel code can access any location in memory without having to do any sort of computation
about the address there are also a number of memory mapped I o devices and the kernels page table
will map those directly in addition to this one page table there

is also a single page table for every user mode process now with the risk five there are a number of
options for how page tables are implemented uh these options are called sv32 sv39 sv48 one of them
is a two-level implementation scheme uh another is the three-level architecture and the sv-48 is the
four-level architecture sv 32 is available for 32-bit processors

we are only concerned with a 64-bit processor and xb6 will use the sv-39 scheme so the processors
processor implements the sv 39 scheme so we have three level page tables there's some other options
that I won't go into every load store or fetch to or from main memory will walk the page table table
at least conceptually so we'll go through the page table to find

the address translation to use that's conceptual walking the page table would involve several memory
accesses and that's going to lead to incredible inefficiency so for performance reasons a real
processor is going to have something called translation look aside buffers these are registers that
are in the core and generally they are transparent or I should say invisible to the programmers

and basically what these registers will do is is act as caches for recent page table entries the
only thing we need to know about as kernel programmers is that whenever we change the page table
that is whenever we update the satp register we need to somehow flush the cache we need to empty out
all the tlbs for this there is an instruction in the RISC-V instruction set called s fence

vma in the kernel there is a function with the name s fence vma that does really nothing more than
just execute this instruction okay let's look at virtual addresses for the 30 the sv 39 scheme a
virtual address is 39 bits okay and that address can be divided into an offset all pages will be 4
kilobytes in length and since 2 to the 12th is 4k we need exactly 12 bits to offset into

the page the remaining 27 bits are divided into three fields which will access the level 1 level
sorry level 2 level 1 and level 0 of the page table the bits above this are ignored and when we
access the page table we will get a page table entry and that page table entry will contain a number
of bits that will control whether the access should be allowed or not it

can be marked read in which case reading is okay writable if we're writing to the page we need to
have this bit set and x stands for executable there's also a u-bit to indicate whether the page is
accessible in user mode or only in supervisor mode and there's a final bit v which stands for valid
to indicate whether this page table entry is valid or not there's some other bits here but we

don't care about those and finally there is the physical page number and the address translation
hardware will take the virtual address use these indexes to look up the right entry and retrieve the
page table entry and then it will take the 44 bits of the page number and the 12 bits of the offset
and put them together to form the physical address which will

be 56 bits so with this scheme we can support up to two to the 56 bytes of main physical memory okay
let's look at the page table itself remember we have these three fields and each is nine bits so the
page table looks like this here's our satp register it points to a tree each node in the tree is a
four kilobyte page in main memory and the tree has three levels

and the leaf level at the leaf we have the actual data pages so all of these nodes and the leaves
are four k byte pages and each one of the interior nodes of the tree that is each one of the three
levels in the page table will contain 512 entries each entry is 64 bits and of course uh 64 bits uh
is eight bytes and eight times five hundred and twelve is four thousand ninety six

so we have room for 512 entries in each node and each entry is a page table entry which will have
some bits and a pointer to the next level node and so the way it works is the hardware we'll look at
the first nine bit field here and use that as an ax as an index into this root node recall that uh
two to the ninth is 512 so nine bits is exactly what we need of course 12 bits

uh will allow us any access to access to any byte in the data page since 2 to the 12th is 4096.

so we use the first index here the level two index the index here we get a pointer we need we use
the next index to index into that we use the and that gives us a pointer to the next page we use
that index to access into this page and that gives us the final page table entry which is shown here
and which will be used to check the access of the data page and to build the physical address

okay that's it for the page table scheme see you in the next video
