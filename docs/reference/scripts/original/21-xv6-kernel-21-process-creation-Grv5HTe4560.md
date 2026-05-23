# xv6 Kernel-21: Process Creation

| Field | Value |
| --- | --- |
| Video ID | `Grv5HTe4560` |
| URL | <https://www.youtube.com/watch?v=Grv5HTe4560> |
| Source | `docs/reference/scripts/original-raw/21-xv6_Kernel-21_-_Process_Creation-Grv5HTe4560.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to talk
about processes and in particular the data structure that is used to represent a process I'll talk
about how the array of proc structures is initialized and how we set up the data for the initial
first process this stuff comes from the file proc.c which also contains a bunch of other

stuff so in addition to this material that I'll be talking about it also contains the code for the
scheduler for yield and so on as well as the code for the sleep and wake up functions I've covered
these things in previous videos the file also contains code for functions like fork and weight and
kill and I'll cover those in a future video so let's begin with

the file itself and um I want to start with these two fields uh there's a process id counter that's
initialized to one and there's a spinlock called pid lock so right off the bat we can take a look at
this function aloc pid so whenever we need a new process id we can call this function and it's
pretty straightforward it returns the new process id and in order to access to read and

update the next pid global variable we need to acquire the lock first so this acquires the lock and
then it reads the current value into a local variable and increments the global variable and then
releases the lock so this is pretty straightforward use of acquire and release okay now that that's
out of the way we can go into the first function that I want to cover

which is proc net before I do that let me remind you of the structures that are relevant for this
video we've got an array of proc structures so here I show one of them and we have 64 of them in
total one for each process and remember that they have a spinlock and some fields such as the state
whether it's running or runnable or so on uh channel if it's sleeping a bullion

for whether the process has been killed and some other things we've also got a pointer to the area
in virtual memory where the stack is located so I'll come to that in a second the size of the
address space that is the break point for where the top of the heap is with this particular process
point of the page table a pointer to the trapframe a context for saving it and a few other

things such as the name field so I'm not going to go over that just now but I do want to talk about
this function proc knit which initially initializes the proc array this is called once by core zero
only during the initialization of the kernel and it initializes that lock for the process id that we
just saw and it also initializes another lock called weight pid

sorry weight lock and then it goes through this proc array okay so it's going through the proc array
and for each one of them it initializes the spinlock okay so it goes through oops it goes through
this array and for each one it initializes the spinlock and it also initializes this k stack okay so
at this point I'll talk about what the virtual address space looks like

for the kernel remember that the kernel's virtual address space like all outer spaces will have a
trampoline page mapped into the uppermost page and then it has a series of guard pages and stack
pages so for each of the 64 processes there is one page in the virtual address space for the stack
that will be used when that process is running in kernel mode so uh

at this point here uh in the proconet function we are determining the virtual address this is a
preprocessor macro function given a number between 0 and 63. it determines the virtual address for
that stack page and it simply saves it in the k stack field of this proc structure so here's the
proc structure again here's the case stack field and it just says the virtual address and I I added
this

dashed line to indicate this is a virtual address uh as opposed to these links here the page table
points to a physical address somewhere somewhere in physical memory as well as and the trapframe
pointer which points to the actual physical address of the trapframe the trapframe of course will be
mapped into the second highest page of the virtual address space but this pointer

right here is to the actual physical page that was returned from k alec we'll see that happening in
a second uh there are a couple of other functions in this that I've already mentioned but I'll just
uh review them CPU id returns the number of the core that's currently executing this function by
just returning the value of the tp register the my CPU uses that

current core number and returns a pointer to the CPU struct okay so here is the array of CPU structs
and here I'm showing one there are up to eight cores uh that's a fixed constant and each one of them
has a CPU structure and uh so we're uh returning a pointer to the one for the current core and uh
finally we have the my proc uh routine which uh gets the pointer to the current CPU struct and

then follows the proc field to get a pointer to the structure that represents the procedure that's
currently executing okay each CPU struct each CPU struct has a proc field that points to a proc
structure each core at any one point in time is either executing the scheduler code or it's
executing one of the processes if it's executing a process then this pointer is valid it

points to a proc struct if the CPU is executing a the scheduler code then this will be null okay so
those functions are pretty straightforward next let's take a look at proc dump I think proc dump is
simple basically this is for debugging and it's just going to go through the proc array and print it
out print out information so um it's past nothing and it returns nothing

and what does it do well as I said it's a for loop that goes through the entire array of all 64 proc
structures here and if that struct is unused okay in other words if the state here is unused and we
just don't print out anything so we continue and repeat the loop body otherwise we look up the state
in the array that we have here we have a little array that

maps the constants unused sleeping runnable running and zombie into short strings and so we grab the
string here and called state and we're here we're just checking to make sure it's a valid a valid
number otherwise we use that and then we print a single line so this is going to print a line for
the process with the process id its current state and the name okay the name field

is a fixed length uh field and we can store a short name in that field and so here we we print the
name okay so that's uh pretty straightforward that's proc dub now we're going to get into the alec
uh function um so let's look at what this function does okay first of all it's past nothing when we
need a procedure when we need a proc struct uh to uh create a new process we call alec proc

and it's going to find one and initialize it and return a pointer to it okay look in the process
table for an unused proc if found initialize it and return with the lock held okay and if there's a
problem it returns no so here's what it's going to do this is an outline of what it does what the
code does it first searches for an unused proc structure then it calls k alec to allocate a trap

frame page for that process then it creates a new page table adding mappings for the trampoline page
which of course is shared by all virtual address spaces and a mapping for the trapframe page that
just got allocated and then it sets up a the context or maybe I should say it initializes the
context in preparation for the first time slice of this process recall that in the

proc structure we have this context area and this is the register save area so we do some
initialization there and uh the address space that got created we you know we've created a page
table here but we didn't put anything in it so there's no code or data yet uh alec proc this alec
proc function is not responsible for that but uh it'll be added later and if there are any problems

this function has to undo everything return all allocated pages back to the free pool by calling
kfree and then it returns no okay so let's take a look at the code here um here we are looking for a
proc structure that has a status or state of unused so we're just looping in this for loop through
the entire array of all 64 elements and for each we acquire the lock if it's uh

unused then we have found something uh another go-to here to this label down here but if it's in use
then we release the lock and keep looking okay and if we can't find anything we return our null okay
let's say we find something well that's good uh so here we fill in the process id each process will
get a new identifier no telling what happens when this variable rolls over we don't worry about

that sort of thing in xv6 because it's not going to ever happen in our lifetime and we set the state
to used so that's so one of the states that we can have in the um I just remember that the used
state is not listed here okay um well in any case uh it goes into this state of being used and
[Music] will the caller of this will be responsible for making it running or sorry runnable

at some future time okay so then we allocate the trapframe page so here we're calling k alec and if
anything goes wrong well we're going to release the lock and return no and we're also going to call
this free proc function I'm going to cover that one next but basically just it's called anytime
there's a problem we see it being called down here as well it's

basically going to make the proc structure unused again and zero out all the fields so that it could
be recycled okay so now we create the page table so here we call a proc page table and I'll cover
that uh in a moment here but that's going to create the page table and um add a couple mappings but
if that's all okay then we keep going but otherwise we call this free proc to undo what we've

done release the lock and return null just as we did up here alec proc is called in two places one
is user init to create the first process the init process and the other place is the fork function
which is used for the fork system call and in any case what's going to happen after the process is
created is it's going to be scheduled to be run so we need to kind of set things up for

that so remember that in the proc structure we have the save area for the registers this would be
the registers that the kernels the the process is going to use when it starts running and of course
it will start running when it gets scheduled in kernel mode so you've got the registers that will be
used or restored I should say and the ra and sp registers the return address and

the stack pointer okay so this is a new process it's not getting rescheduled it's getting scheduled
for the first time so we need to have a return address and a stack pointer for its initial
scheduling so we set the context well first of all we we clear out all the registers so mimset just
writes a zero to all of the registers in the register save area and then we initialize

the ra register to point to this fork ret function I'll look at that in just one second and we
initialize the sp register to point to the stack page remember that each process has a page in
virtual memory and for example process two has this page at this address in virtual memory and
stacks grow downward so we want to initialize the stack pointer to point to

the top of this so that it can begin to grow downward from this zone so we initialize the stack
pointer so when this process is scheduled that is when it has its first time slice it will begin
executing at the address given here for correct with a stack what we're going to do after we call
alec proc is basically fill in the code and the data and we also need to release the lock

remember that we have acquired a lock up here we need to release the lock and then this thread can
go on and do whatever it wants to do but at some point the scheduler will select this process and it
will acquire the lock and it will for that process and it will schedule it and then it will load the
registers from the context and that will include the return address

and so this process will begin every process including the inet process we'll begin by executing the
fork rep function so here's the fork rep function and we can see what it does it well remember when
we first get scheduled we are holding the process lock so we have to release that lock and uh we
have nothing more to do except the um return from the trap well we

haven't really had a trap but we begin by returning from the trap in the case of a fork we have had
the trap in the case of the initial process we are going to fake the trap but in any case we do a
trap return here this other other stuff in fork gret you can see that you've got a global variable
first or static variable I should say that's initialized to true so

this will be done one time the first time that any process is scheduled it will execute this code
then set first to false and it says the file system initialization must be run in the context of a
regular process because it calls sleep and thus it cannot be run from the main function so that's
what's going on here when we are just about to schedule the initial process we will

see that first is one and we will invoke this file system init function and take care of that before
we do the user trap rat to the [Music] first bite of the initial process okay next let's take a look
at um free proc so if anything goes wrong uh and we'll call free proc and when we're done with a
process we'll also call free proc so what's it gonna do um well it's

passed a pointer to the procedure that you know we're abandoning and it looks at the trapframe
pointer okay looks at the trapper and pointer oops here we go and it asks if it's not null then it
points to something so return that to the free pool so that happens here and sets the track frame to
null and then it looks at the page table pointer okay it looks at this pointer here

to the page table and it needs to um basically destroy that page table and so we'll uh discuss page
table and proc page table and proc free page table in a second here but basically it's going to call
proc free page table to undo that page table return everything to the free pool and then it uh
zeroes out uh uh some of the remaining fields um you know it this is not strictly

necessary but it does all of this it's a null in the name field and so on but in most important it
sets the state to unused so at this point this process is the proc structure is unused okay so in uh
alec proc so returning to the function alec brock we found a proc structure and um set up the
trapframe and then we call proc page table okay to create an empty user page table so now

let's look at proc page table so create a huge a user page table for a given process so it's passed
a pointer to the proc structure and it's going to set up the page table and return a pointer to that
page table and as we saw in alec proc it'll just store that in the page table field okay so what
does it do well we've covered uvm create to create an empty page table

okay so that just uh sort of creates the user page table and if there's any problem we return zero
next we map we create a mapping for the trampoline page okay so the trampoline address this is the
address of the highest page in the virtual address space it's one page in length and we map it to
the trampoline page this is the page in the kernel's code area marking it read

and executable and so we've discussed map pages so that adds a mapping to the page table for the
trampoline page and if there is a problem with that then we free the page table that we've created
and just return okay next we map the trapframe to the page the second highest page and we call map
pages again uh the trapframe address is the second highest page

again it's one page in length and where is that page well we've allocated a page by calling k alec
earlier and set trapframe to point to the physical page in physical memory and so that's what we're
using right here the trapframe and we're making it readable and writable and if there is any problem
well we unmap the trampoline page and then again free the page table and

return zero okay now what about uh the converse the free proc free page table well we've discussed
these functions in the videos on the virtual memory functions so we basically remove the mapping for
the trampoline page without freeing the data page itself and we remove the mapping for the trapframe
page without freeing the trapframe page itself and then we um call uvm free to

completely obliterate the uh page table to return all the data pages to the free pool and to return
all the index pages to the page to the free pool so where we call um where we call this is in free
proc so here's our function free proc and you see we we're already freeing the trapframe page here
so that's why we don't uh ask for it to be freed at this point right here

because we've already freed it and we call a free page table here and set page table to null so this
is where we use proc free page table okay now let's talk about the this function here which is user
init user init is called to set up the first user process so let's walk through the code it's not
too long so the first thing it does is it calls alec proc to find a proc structure and

sort of set it up so now we've got a pointer to the proc structure and this is called the init proc
so we're going to save a pointer to that next we're going to allocate a single page and copy in the
en code for the initial uh process so uh here we are calling uvm init which we've discussed earlier
but it's passed a pointer to the page table okay so alec proc would have

set up this page table but not filled in any code or data and uvm init has passed a pointer to some
bytes and the number of bytes the count uh and what is that well this is a knit code right here okay
this is this array and it contains the bytes and uh I think they counted out 52 of these things um
you can check me on that let's uh eight times six forty eight four nine fifty

two one two fifty two page uh bytes so here we are um going to whoops going to copy in those 52
bytes into page 0 of the virtual address space that is being pointed to by this page table we're
also going to set up the size field that's one of the fields in the proc structure right here this
tells how big the the virtual address space is from zero to the break point and here we just have a

single page so that's not very much okay prepare for the very first return from the kernel to the
user here's the trapframe the epc was where we saved the program counter when a trap occurs in a
running user process we save the program counter and we save it here we save all the registers as
well okay we save all the general purpose registers including the stack pointer

okay and so when we get ready to return to the users process we return to executing at this program
counter here after restoring all the registers so what we're doing here is we are setting the
program counter to zero so for the for this process and only for this process we'll start executing
it location zero for all other processes when they first begin executing

well they were created as a result of before construction and they will begin executing directly
after the the fork uh called to the fork system call and so we don't need to do that for the other
processes but for the first process the init process we set its program counter to zero and we also
set its stack pointer to the page size okay we're going to remember that the initial process will

have exactly one page which will contain both the code and the stack so by setting sp to the page
size we are setting it to the top of that initial page and so hopefully the initial process won't
grow its stack very much in fact it won't grow it at all but we do set it up anyway and then finally
we copy into the name field of the proc structure so here's the proc

structure again and we have this name field down here it's a fixed number of bytes a small number of
bytes and we are copying in these characters save string copy is something that will copy a string
from one place to another place and it won't overrun the target area so we're passing in the maximum
number of characters to copy with this size of parameter here so that way we don't over

accidentally overrun this uh array here and we've got a couple of other fields uh the current
working directory down here of the proc structure is initialized uh to point to um this this uh a
slash so that's what that is and then finally we set the state whoops set the state to runnable and
release the lock remember alec proc returns with the lock set and so now we've allocated a

a function sorry allocated a proc structure and changed its uh initialized its page table and
everything got it all ready to go we set it state to runnable and we're done with it we can ignore
it from here on out uh this this thread that's running this can ignore it and so we just release the
lock and the scheduler will then find this process when it's looking for something to run and it
will

see that it's runnable and it will just schedule it and the initial code will then start executing
and of course what it does is not much just looking up to this code here here's the code of course
you can't read this because it's the machine code for the RISC-V processor but in any case what it
does is calls performs a system call for the exec system call passing it a

string of slash init and that's all it does and um we are basically uh getting this code this is the
unix command octal dump basically we're octal dumping it and putting it into a file and then copying
it into here so there you have it and I'll cover the rest of this uh file the proc.c file in a
future video
