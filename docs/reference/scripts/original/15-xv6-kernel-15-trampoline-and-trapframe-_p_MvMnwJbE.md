# xv6 Kernel-15: Trampoline and Trapframe

| Field | Value |
| --- | --- |
| Video ID | `_p_MvMnwJbE` |
| URL | <https://www.youtube.com/watch?v=_p_MvMnwJbE> |
| Source | `docs/reference/scripts/original-raw/15-xv6_Kernel-15_-_Trampoline_and_Trapframe-_p_MvMnwJbE.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system in this video I'm going to go over the
assembly code in the trampoline...S file this includes the code called userback and the code called
user return I'll also go over some of the code in the trap.c file I will talk about the usertrap
function and the usertrap return function let's begin with what I call

the map um user code is executing and we get a trap and we execute the uservec assembly code first
and then we go into the user trap function and then we deal with the trap whatever kind of trap it
was and then we call the user trap return function and that then goes into the user rep assembly
code function which ends by executing the system return or sret instruction

when the trap occurs interrupts are disabled by the hardware and we change into supervisor mode and
the current program counter is saved in the scpc register the st bec register is assumed to contain
the address of the trap handler and so the hardware will also load that into the program counter
that basically causes a jump to the uservec code because that is the address that

the sdvec contains and the uservec needs to begin by saving the entire state of the user mode code
needs to say the registers and the program counter and those are saved in the trapframe we also get
ready to execute kernel code by loading the sp and tp registers and then we switch over to executing
in the kernel address space so this initial stuff happens when the page table is set to the user's

address space but after we load the satp register the code will now be executing in the kernels
space the trampoline page is mapped into the uppermost page of all address spaces so this won't
affect the code that's running here but it will allow us then to jump to code that's in the kernel's
address space but not the user's address space and so let's start by taking a look at

what's going on in userback so here is code from the trampoline..S file this is assembly code and
this code contains two pieces this file contains two pieces of code one is called the uservec
function and it takes a little bit of a page and a half and then we have another one called user rat
which I'll talk about later those are the only two things in this trampoline file

when this is assembled it will be placed into a section or sometimes I say the word segment named
sec okay and that will be placed by the linker into a single page by itself and it will be page
aligned we define a label here trampoline lowercase and that is the address in physical memory of
where this page is located and we have something called userback as well that

is the address of the start of this code we'll look at in a second since this is aligned on a page
boundary this alignment statement here will have no effect we're already aligned on a four byte
boundary so user back and trampoline will be the same number so when we compute the offset of
uservac from the beginning of the page we'll subtract trampoline from userback it's a zero uh

the offset however when we calculate the offset of user ret which is further down in this file the
offset will be greater than zero okay so that's why we have the same address being assigned two
different labels here this is the start of the page in physical memory this is the start of this
particular routine within that page and we export these two labels with the

global directive okay we've got to save the registers initially we could ask what's happening and
what is true while the user code is executing and one thing that's true is that the control and
status register called s scratch contains a pointer to this um to the trapframe and so we this is
handy because we really can't do anything with registers we can't really

execute any code because we have to save the registers first but we do have this instruction control
and status register read write which will swap the values in a0 and s scratch so after this
instruction is executed s scratch will contain the user's value of a0 and a 0 will contain a pointer
to the trapframe so now we can use that pointer and store all the remaining registers so we

begin storing the registers here and we store all of the registers except a0 itself notice that
these are the offsets here and this is a store double word of this register into this offset from
this pointer and you can see that offset 2 is left out for a 0. so we store everything except a0 and
then following us down we store all the registers and now that we've stored the registers we

have a little bit more flexibility we've stored t0 so we can use that this reads from the scratch
register into t0 and this stores from t0 into offset 112 so now we stored a zero now let's take a
look at um the trapframe and in this image I'm showing how the trapframe is laid out and so we've
got a number of uh locations where we save the state of the user mode thread and we just saved the

31 general purpose registers into the register save area here we've not yet saved the user's program
counter but we will we've also got several locations here that we need to load so that we can
execute kernel code and so uh actually uh so that's what we're doing here we're loading the stack
pointer from this location in the track frame we're locating uh loading

the thread pointer uh and we're loading the satp register so let's go through that um we grab the
value of the sp that's stored in the trapframe and put it into the sp register the kernel code we
will not have so we have the trap executing here and once this trap happens we're running in kernel
mode and so we're going to execute all of this stuff we're going to

handle the trap whether it's a timer interrupt or a device handler or whatever it is system call
we're going to handle that in kernel mode as part of this little thread from here to here before we
return to the user's code you can think of this as part of the processes thread or you can think of
it as like a little mini thread but we're starting with a fresh stack right

here and we're not retrieving the previous values of registers so I don't think it's right to think
of this as continuing a kernel thread instead we're starting a new thread we're only loading the
stack pointer and tp the other registers are will have values that are left over from the user mode
code the stack pointer that we will be loading perhaps I should have said load

rather than restore because we're not retrieving a previous value as much as we are initializing the
stack pointer we are initializing it to what's called the kernels stack page every process each of
our 64 processes will have a single page set aside for the kernel stack and that is what we're
loading into sp here or more precisely we're loading a pointer to

the [Music] top of that page remember that stacks grow downward so we're essentially loading sp with
a pointer to a completely empty stack so that's what happens at this point here we've been executing
and we are currently executing on some particular core but we don't know what core we're executing
on however before we went into the user mode code we would have saved

in this field right here the number of the core that we're executing on so the so-called hart id and
so at this point we are loading that back into the so-called thread pointer register uh by the way
if you're watching I actually reverse these so the offsets don't exactly match up but uh the idea is
there so we're loading tp with the core number so now we can

make use of that and what we're going to do down here is we're going to jump into the user trap
function so we're going to do a jump via register of t0 here we are loading t0 and we have
previously saved into this field the address of the user trap function so this instruction here is
preparing for this jump down here at this point we're ready to switch to the kernel's address space

uh we've uh set everything up so that we can now execute kernel code in kernel's address space so
here we're getting the pointer to the kernel's page table which we've previously saved and we are
storing it into the control and status register called satp so we retrieve it into register t1 and
then write it into this register and then we do a fence instruction to make

sure that everything that is supposed to be executed before that is executed to completion before
that and every instruction that's supposed to be executed afterwards is delayed until after the satp
register is loaded the RISC-V processor calls a function by jumping to the first address after
saving the return address in register ra return address so that's how a call is normally made

however in this case we're simply jumping we're not saving the return address and that's because
user track rep does not return so now let's go take a look at user trap return sorry user trap and
we have that code here so this is coming from the trap dot c file and there's some other things
there are some other functions in this file which I will not cover in this

video but I will look at user trap at this point so this is expressed as a c function but it's a c
function that will not return what will it do well at the end of this function it will [Music] call
another function and that function will not return so we'll get there um here we're doing a sanity
check we're looking at what's in the status register and in particular we're looking at the

previous privilege level bit and that tells whether the the core was running in user mode or
supervisor mode when the trap occurred and it had better have been running in user mode or else
something is dreadfully the matter so now we're running a kernel mode what if we get another trap
okay a device might need service for example and might cause an interrupt so here we write into

the st back a new address here we're writing into it the address of a function that will handle
kernel interrupts and then we grab a pointer to the proc structure I showed a picture of the proc uh
structure earlier and it was this picture here okay so we've got information such as the pointer to
the page table and and a pointer to the trapframe and some other

things and in particular we're interested in this field trapframe which is a pointer to the physical
address of the trapframe page for this process in memory each of our 64 processes will have its own
page in physical memory for trap for the trapframe those will be mapped into the second to the
highest page in the user's address space but we're no longer running in the

user's address space so we need the physical address and this field it contains a pointer to the
trapframe in physical memory so we use we can use that now uh previously we have not yet stored the
the program count uh sorry the program counter from the user's address space so here we're storing
that okay so this is the last bit of state of the user mode process that we need to store

so that's done at this point where we read the control and status register and then save its value
in my roadmap picture I showed a big if statement okay so now we're in user trap and we're going to
do this if statement so that's what's going on next so we look at the value of the s cause register
to determine what's going on if it's eight that means the trap was as

a result of a system call so we do this bit of code here we'll ultimately call a function system
called to deal with it but first of all we take a look at this trap sorry this killed uh field
that's a field in the proc it's a boolean if anybody wants this particular process to die it can set
that boolean field to one so we check it right now to see whether this process needs to commit
suicide and

if it does we call a function exit exit will not return so that's that but let's assume that we
don't need to commit suicide and we keep going the instruction that does the system call in risk
five is called the environment call instruction e-call instruction and our risk five core will have
saved the pointer to that instruction in the epc register but when we return we don't want to

execute this instruction again we want to increment the pc to point to the instruction directly
after the e-call so the instructions in the RISC-V processor are four bytes long so we increment it
by four to adjust and now we're ready to now we're ready to turn interrupts on so at this point the
kernel code can be interrupted by another trap so for example if a device needs

handling it can happen while we're handling the system call okay we've saved the state of the user
thread in here and if we get interrupted we're going to have to save the state of the kernel thread
somewhere else but we're not going to worry too much about that right now so we call this function
and depending on what kind of a system call it is we deal with it and um if it's the

uh exit system call we may not in any case we return here and keep going then if it's not a system
call we call this function device interrupt and that then looks at the cause register to determine
which device requires handling and it returns a code if it is a normal device such as the serial com
device or the disk device it returns one if it was a timer interrupt

or more precisely if it was a software interrupt which is how machine mode code will translate the
timer interrupt then it returns a two and if it's anything else it's an error and it returns zero so
here we're saying is it an error or not if it is not an error and it is a device then it will be
handled here if it is a timer interrupt well we may update a counter to keep

track of the current time but basically we just return two and we save that in this switch device
variable which we'll then check right down here but if it is zero then we print an error message and
set this killed boolean to indicate that we need to kill ourselves in the future down here the error
message is just going to print out the process id it's going to print out the location in

the user's address space at which the error occurred and uh it's going to print out the value of the
st val which may contain additional information uh if you're not familiar with the formatting code
percent p it basically prints out a I paused for a moment because I saw that we were reading from
these control and status registers and uh I momentarily forgot that we turned interrupts on but

we only turned them on if we are in a system call if we are in the device interrupt the interrupts
are not turned on so the device handlers will execute with interrupts disabled and likewise this
error message has interrupts disabled so these val these registers are still okay to be read
directly okay if at any point in the system call or or here we've set killed to true then we

self-terminate finally if we had a timer interrupt then we call the yield function yield function
can be considered uh like a no op so we call this function and then we return from it however we may
not return from it immediately yield we'll call the scheduler and it may schedule some other threads
to run and it may be quite a while before this yield function

ultimately returns but at some point we assume this scheduler will give the processor back to this
particular thread and while it will save all registers it will restore them and so yield will simply
return however it will not necessarily return on the same core we may have gotten switched to a
different core so let's continue with this uh function and I'll show it like this

line them up so after this test then we call user trap return user trap return is right here so this
is effectively a go to if you will or or just falls through um we also will invoke user trap return
from um the fork process but I don't want to go into that right now but that's why this is set out
as a separate function rather than having it just simply fall

through so now now we are whoops we are down here and we have considered doing the yield and we are
now ready to return to the user mode thread so we're now in user trap return and we need to disable
interrupts and we need to set up some stuff and then go into the assembly code in the trampoline
page so let's walk through what happens at that point interrupts may have been turned on

if we had a system call for example and so we need to turn interrupts off we're going to be messing
with some control and status registers and we cannot allow the core to be interrupted while we are
dealing with the control and status registers we are grabbing a pointer to the proc structure as
well up here so previously we had set the st vac register to point to

kernel vac which will handle any interrupts and traps that occur if we're executing in kernel mode
now we're going to restore that by writing into the sdvac the address of the uservec code so any
future traps will jump to uservac and what is that address well it is the offset from trampoline
which as I said was zero plus the address of the trampoline page which is the highest page in memory

so next we uh look at that trapframe in other words we've got a pointer to the proc structure and we
follow it to the physical address of the trap of our trapframe the trapframe page for this process
and we're going to then set the fields these fields here these four fields that we need when the
next trap occurs so that's what we're doing at this point we are currently executing in the

kernel's address space so we get the pointer to the kernels page table and we store it here into the
satp field this is not actually a pointer but it's basically you can think of it as a pointer there
are a couple of bits that are set but we will restore it as it is here then we set the sp field the
stack pointer every process has a stack okay and so that is shown here

I didn't actually draw a little uh pointer to a page like I did down here but it's the same idea
we've got a pointer to the stack page for this kernel and here we are initializing the register to
point to the address just beyond that page so that is effectively an empty stack and then we
initialize the trap field to point to the user trap function and that is the C code that we saw and

finally we are getting the number of the current core by reading the current thread pointer register
remember we did a yield so it's possible that we're not running on the same core anymore so we need
to retrieve the core number of the of the core that we are running on and we store it in the field
called hart id now we're going to go back to the user code with the srett instruction

and so we need to prepare for the srat instruction the sred instruction will use the status register
so here what we're doing is we're we're telling it that the previous privilege mode was user mode so
we're clearing this bit and so the srep instruction will then return us to the previous privilege
mode that is it will change us into user mode we're also saying that the previous

interrupt enable bit um was enabled okay so that when the s-red instruction occurs it will enable
interrupts and we then write that into the status register okay we we read the status register here
update it and write it back the srd instruction will also use the scpc register to restore the
program counters for the user mode program so we are retrieving the value

that we saved here and we're moving it into the epc register and then we have a variable satp this
is a variable not a register and we are taking the pointer to the current page table for this user
to the page table for this user process okay so we've got this field page table in the proc
structure that points to the page table and we are essentially storing the

address of the page table in this variable however the satp register has sort of a couple of fields
in it and make satp is a preprocessor preprocessor macro that is past the address of the page table
and it will shift out the offset bits and it will set some bits in the upper field to indicate that
we are using RISC-V sv 39 mode you know a few details that are related

to the hardware but you can think of it as essentially grabbing a pointer to the user's page table
and finally we've got this thing down here let me try to parse this for you a little bit what we're
doing here in the second line is calling a function okay and we're passing it to arguments so what
is the function that we're calling well we are determining it right here

and it is the user ret code or more precisely user rep minus trampoline is the offset within the
trampoline page of the user rep code and then we're adding it to the address of the highest page in
memory the trampoline page and so this gives us our address and remember that trampoline page is
mapped into all address spaces um we are executing in the kernel's address

space so that's fine it's also mapped into the user's address space but so it doesn't matter which
what the address it doesn't matter what value of essay of the satp register we have but in any case
we get the pointer to the user rep code and then we call it and this casting thing here is basically
uh the way of telling the compiler that we that fn is a uh pointer to a function

okay that returns void and has two arguments okay the two arguments are trapframe the pointer uh the
address of the second highest page in memory and the value that we will need to be storing into the
satp register so now let's look at user ret in the trampoline page okay so here we go uh there we go
and the comment here tells us that we are calling it with uh

two arguments the risk five will implement the call or the c compiler will implement the call by
moving the two arguments into a0 and a1 we'll also save the return address in register r a the
return address register however we'll never be returning so we don't care about that and at this
point a1 contains the pointer to the page table for the user address space

we are writing into the cs register uh the control and status register called satp that value here
so now we are switched into the user's address space we do the fence here and a0 points to the
trapframe page okay so we can make use of a0 here so the first thing we do is we grab the user's
value for register a0 that was stored at offset 112. and we moved that into register t0 so we loaded

into register t0 and then we write it into the scratch register so now the scratch register contains
the users a0 whereas a0 contains a pointer to the trapframe and then we load all of the registers
that were previously saved except a0 here at offset 112.

okay we load all the registers and then continuing on at this point we swap the contents of the
scratch register and a0 so now a0 is the user's value which is what we need and the scratch register
contains a pointer to the trapframe which we will need to have which we expect to have when the next
trap occurs and so finally we're ready to do the esret instruction

so wrapping it up and going back to my image I call the road map so we're down here I just want to
make a note about this I I kind of um did things in the order that I thought made logical sense the
s status register was actually pre-loaded up in user trap rat but in any case you can see what we've
done we've disabled interrupts we've restored st back to point to

the uservac code namely this code up here um we haven't really saved the uh we've been we've set up
sp and tp so that they will be usable when we have the next trap and we loaded the scpc register and
then we set the satp register to point to the user's page table so now we're executing in the user's
virtual address space we restored all the user registers we

loaded the status register or we basically changed these bits with that up here actually and then
finally we're ready to execute the s red and so that will then change the program counter back to
what it was and we will continue and it will change the mode to user mode and enable interrupts so
now we're right back where we left off when the traffic when the trap occurred up here and we're

ready for the next trap okay that's it for the trampoline page see you in the next video
