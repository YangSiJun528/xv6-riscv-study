# xv6 Kernel-24: Exit Wait Kill SysCalls

| Field | Value |
| --- | --- |
| Video ID | `ZHc8CHqi3Ws` |
| URL | <https://www.youtube.com/watch?v=ZHc8CHqi3Ws> |
| Source | `docs/reference/scripts/original-raw/24-xv6_Kernel-24_-_Exit_Wait_Kill_SysCalls-ZHc8CHqi3Ws.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I will talk about
the exit wait and kill system calls I will also describe the reparent function and review sleep and
wake up as they relate to these system calls let's begin by reviewing the exit system call as I am
sure you remember there is no return exit is used to terminate a process

and we provide the exit status as a value to the exit and that is returned to some other process on
unix and linux the exit status is an 8-bit positive number and on xv6 it's a 32-bit integer but
that's not really relevant on unix and linux the upper bits are used to communicate additional
information to the other process such as uh what signals cause the process to terminate and so on

but in xv6 there are no signals so we can just use 32 bits but in both the unix linux and xv6 0 is
used to indicate that the process terminated with success whatever that might mean for the process
and any other positive value is used to indicate that there was an error in the process so what
happens with exit well if there's a parent waiting for a child to terminate

then that parent is first awakened and then the exit status is somehow copied from here to the
parent code and the parent then will be responsible for cleaning up the process after a process
terminates it stops executing instructions we need to free up its data structures and in particular
we need to return its proc struct to an unused state but we can't do that

in the child process itself because it's no longer executing instructions so it's the responsibility
of the parent to perform that cleanup operation but if there is no parent waiting then the
terminating process must freeze and wait so at that point it will become what we say is a zombie in
other words it stopped living it stopped executing instructions but it's not yet fully dead

its data structures are still persisting because we need some place to store the exit status at some
point in the future its parent will finally issue a call to the weight system call and at that point
the operation can be completed now there could be a problem if a process exits without waiting for
any of its children and what happens then well all of the children of a and

terminate of a terminated process are moved okay we say they are re-parented and they're moved to
the init process so they're no longer children of the process that's terminating but instead they
become children of this init process what does a knit do well it does some initialization and then
it goes into an infinite loop waiting for children to exit and so whenever one of these I guess we

can call them adopted children uh issues an exit system call and it will then collect that exit
status and that will allow the uh zombies to finish dying and it will allow some process to clean up
after a process has died now let's look at the weight system call weight which is available both in
xv6 and all unix and linux systems is passed the address of some location

and it returns the process id of some child that has exited okay if there are no children of the
parent uh then it returns minus one but assuming there are uh children then it will wait for one to
terminate if waiting is required and it will grab the exit status from that child and store it so
the argument here is a pointer to some place to store that exit status

unix and linux systems have an additional system call that's not available in xv6 called weight pid
that gives you more control for example you can wait for a specific process to terminate and also
you can avoid sleeping so there's a parameter that allows you to return immediately if there are no
zombie children waiting okay so how does weight work what does

it do well first it looks for a child which has already exited in other words it looks for a zombie
child and if it finds one it grabs the exit status from that child and then it cleans up after that
child in particular it gets rid of the proc structure calls free proc which changes it back to an
unused proc so it can be recycled and then of course it returns the process id of the

terminated child but if there are no zombie children okay then the weight system call will need to
sleep so it calls the sleep function and when some child finally terminates within the exit system
calls for for that child the wake up function will be invoked and this will wake the parent back up
and the parent will then loop back up and look for the zombie child

if the parent is awoken for some other reason then there won't be a zombie child and it would just
go back to sleep remember that the sleep and the wake up functions communicate with this channel
which is an uninterpreted value that the sleep and the wake up don't really look at but it's used to
coordinate so which processes should a wake-up call awaken well that's determined by

whichever channel is passed to the wake-up call and for this application we use the address of the
parents proc structure as the code or the number if you will that that is passed to the sleep so
this wake up will only wake up processes the the parent process it won't wake up any other processes
okay next let's see how this looks in code whenever the exit system call is invoked

the kernel will go into this function sysexit which we'll just call exit to do the work this
function exit will never return so we won't get to this point there's a single argument so we invoke
argent to retrieve a register a0 from the user's registers and place that in a local variable n that
value is then passed to the exit function so here's the exit function

and all of these functions exit kill weight and re-parent are in the file proc dot c so exit is past
the status value and what does it do remember that the global variable init proc points to the proc
structure for the initial process and here we're just making sure that that initial process is not
trying to exit I haven't talked about the file system yet but you can see here that we're

closing the open files for this process we go through the open file array okay and for each one
that's open we are closing it and clearing it out we're also closing out the current working
directory with this code here now let's get to the good stuff in the rest of this function we can
notice that there are no if statements or loops so it goes straight through and among other things
we can

see that the state of the process is turned into a zombie state so all terminating processes will
become a zombie and the parent will ultimately clean up and take care of returning the proc
structure to the unused status so it could be recycled we also save the status that's the argument
right here in a field in the proc structure called x exit state or x state

and whenever we access these fields we need to be holding the lock on the proc structure so we've
acquired it before that point before this we see that we're invoking re-parent so if this process
has any children they have to be moved they have to be orphaned if you will and adopted by the
initial process so that happens in the re-parent function and then we might have a parent that is

waiting for us maybe there's a parent sleeping and maybe not but if there is one sleeping we need to
wake it up okay so here we are calling wake up and as the channel we're passing a pointer to our
parents prop structure so before we access the parent field we need to be holding this lock called
weight lock and so we've acquired it here the reparent function will also be

accessing the parent field and the re-parent function requires us to already be holding weight lock
when it's called so we acquire the weight lock here and then call re-parent and wake up the parent
process the parent process might get awoken at this point but the first thing it does we'll see is
acquire the weight lock or try to acquire the weight lock so it

won't really make any progress until after we release the weight lock so we don't release it until
after we've become a zombie so that the parent process when it does wake up will find us being a
zombie find this process as having a state of zombie and the status will already have been set and
so finally we jump into scad and skid is the scheduler and we set our

status to zombie first and the scheduler will never schedule anything unless it's runnable so this
process will never be scheduled again so we'll never execute this last instruction here in sked we
are required to be holding the lock on the process and no other locks so that acquire is uh done
right here and satisfies the requirement for skid okay next let's take a look at uh the

re-parent function and uh we see that the collar must hold weight lock and all it's going to do it's
passed a pointer to the uh process that is exiting okay that we can call that the parent process and
it runs through the proc array looking for children so basically it uh for every potential child it
looks at its parent pointer and asks whether that points to p and if so we've got a child

and so we modify its parent pointer to point to the proc structure for the initial process so that's
where the re-parenting occurs and then since we've added a new child to the knit proc to the initial
process we need to wake that process up in case it's sleeping because it will be in a tight loop
waiting on children to terminate so that it can get rid of them so we issue

the wake up call for the init process in case it's sleeping and that's all reparent does next let's
take a look at the weight system call the kernel invokes cis weight which will then call weight to
do the work the weight system call has a single argument so here we invoke arg adder to fetch the
value of the user register a0 and we place it in a local variable

previously in sys exit which is right here we called argent the only difference is that argent
retrieves 32 bits and arg adder retrieves a 64-bit value and that is the virtual address into which
we will store the exit status weight will return the process id of the child that is terminated and
then we return that to the user mode code so here's the code for weight and let's

start by looking at the general structure we see that it's passed the virtual address into which we
store the exits code we've got an infinite loop here okay an infinite loop and it uh initializes a
local variable have kids to zero and then it looks through all of the children for one that's a
zombie okay and if it finds a zombie it does this stuff and so um

here's where it looks through all the children and if it finds no children after it finds no zombie
children then it asks have we found any children at all okay if ever we find a child we set half
kids to one here but if we get down here and we find that we have no children then we immediately
return minus one this function at the beginning acquires weight loss because it's going to be

looking at the parent pointers and so we release a weight lock before returning that minus one also
if ever any other nasty processes have set our killed flag then this process needs to terminate
itself and so if killed has been set again we release the lock and we return minus one the kernel
code that invoked uh this was originally called from uservac and as we

return through cis weight to sys call to the userback function before we return to user mode code
we'll check the killed flag and if it has been set we will exit then and so we will never actually
return a minus one to the to the process that invoked the weight system call the weight system call
simply will not return at all okay so um if we have not found a zombie

but we do have children then we sleep so here we are sleeping um on uh the channel and for the
channel we're using the address of the proc structure of this very process okay that was obtained
right up here at the very beginning we grab our own uh an address of our own proc structure and uh
then we sleep using that of the channel the child any child that exits will then

do a wake up using the address of its parent that is that same address and wake us back up and then
we'll repeat the infinite loop the for loop the outer loop and again look for a zombie child um
we're passing uh we're as the second argument the weight lock and remember that with sleep and wake
up they work in such a way that the lock that is passed should be held

and it will be released but it will be released and the process will be put to sleep as an atomic
operation so we won't miss any signals and the weight lock will be held until we are certainly all
the way asleep okay now let's assume that a child has exited and so this process is woken up from
the sleep the outer loop will be repeated okay and what happens well we initialize half kids to zero

again and we are going to search through the proc array for any children so we go through the proc
array and for each one call it uh np we check to see whether it is a child of ours in other words
does its parent pointer point to this process and if so we found a child so we'll set have kids to
one and then check to see whether it is a zombie anytime we access the state field of

course we need to acquire the lock so we acquire the lock on np and then look at the state field and
if we found a zombie well this is the one we are going to take care of so we grab its process id and
that's what we'll be returning down here and then we grab its exit status here so here we're
grabbing its exit status if adder is null we can skip the copy so we check for that if we

actually have been provided with a virtual address in which to store the exit state then we will do
the copy out and so here we're using the function copy out which copies from the kernel's memory
into the virtual address of some process that is the parents process is the one we're copying into
at the virtual address in this variable adder and so if there's any problem with that

we will uh release the two locks we're holding the lock on the child and the weight lock and return
minus one but assuming the copy out works okay then we go ahead and clean up after the child process
the zombie child is now ready to be laid to rest we can say so we take care of the final action
which is to free up the data structure that's associated with a child we call

free proc to do that and then we release the lock on the child at this point the child proc
structure is now unused and can be recycled and then we release the weight lock which we're still
holding and return next let's take a look at the kill system call first we get into the cis kill
function which we'll call kill to do the work kill takes a single argument which is

the process id of the target process that we want to uh terminate we retrieve the value of user
register a0 by calling arg and place that in a local variable and pass that to kill kill will return
zero if everything's okay and minus one if we've been passed a process id that doesn't exist okay so
here's the code for kill it's uh pretty straightforward uh where

we've got a single loop here that will search for a matching process id so let's look at that in a
little bit more detail here we're going through the proc array one by one and for each proc p for
each process p we are looking at its pid field to see if it is the one we are searching for and if
so we are going to set its killed field to true we are also going to look at its state

field now these three fields pid killed at state are among the fields in the proc structure which
are protected by the lock so if we are going to read them or modify them then we need to first
acquire the lock so here we are acquiring the lock on this proc structure and then we are releasing
the lock if we do the if statement we release it here and everything's okay we return zero

if we find that it doesn't match it's not the right process then we release the lock and repeat the
loop okay and if we go all the way through the loop without finding a matching process we return
minus one okay uh we set the killed flag here to true and then we check its uh state if this process
happens to be sleeping okay it's not running or runnable then

uh it is waiting on something and we don't know what it's waiting on but we don't really care so we
need to just wake that process so we can wake it by changing its state to runnable and now if it's
sleeping that means it was in the kernel thread for that process and so whatever's gonna whatever
system call it was in the middle of doing or why it was sleeping

it's going to wake back up and finish that system call okay it might be you know might end up
returning an error or something because it just got woken up arbitrarily but with any process with
any system called before we return to executing a user mode we always check the killed flag so
whatever this process was doing whatever system call it was in the middle of

executing yeah it finished but and maybe it didn't do the right thing but nonetheless uh before it
returns it will uh not return it will see that its kill flag has been set and it will then call and
exit so that process will never return to user mode so here we wake it from sleeping otherwise it
might just persist and stay sleeping forever if the condition never arises we want to

kill it immediately so that's what we do here okay that's it for these system calls I will see you
in the next video
