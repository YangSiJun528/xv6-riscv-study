# xv6 Kernel-18: uart.c and console.c

| Field | Value |
| --- | --- |
| Video ID | `ec8A0u6SjDg` |
| URL | <https://www.youtube.com/watch?v=ec8A0u6SjDg> |
| Source | `docs/reference/scripts/original-raw/18-xv6_Kernel-18_-_uart.c_and_console.c-ec8A0u6SjDg.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'll talk about the
serial input output system and I will cover the files UART.c and console.c let's begin with an
overview of uh the system uh the 16550a chip is something that comes from the early days of
computing but the interface that it provides and the protocol that it uses is still in

use today and the risk emulator that xv6 runs on uh uses this particular chip so the kernel is
interfacing to something that emulates a 16 550a chip and this is the hardware in this dark box here
and it interfaces with the display and keyboard somehow and to send a character to the display
there's a register and you write the kernel code will write to some particular location a byte and
that will

ultimately appear on the display and likewise to get a character from the keyboard the kernel will
read from some particular bite and this is what goes on inside the kernel the chip itself may or may
not contain some fifo cues to make the communication go a little bit more smoothly but these
internal fifo cues can be ignored the kernel provides an output queue

called the UART transmit buffer and an input queue called a console buffer each of these cues is
protected by its own lock so to do output uh the kernel code will call a function UART put c which
will add a character to the output fifoq and to read data from the serial input we call a function
console read which will get characters from the input buffer we also

have some other pathways here in particular whenever a character is read from the input it will
automatically be echoed by the kernel software and immediately displayed and that echoing will
bypass the output buffer in case the output buffer is full or we would you know if it were full we
would have to grab the lock and acquire the lock and that might delay the echoing so this basically
goes

to the front of the line shall we say and outputs the character immediately so the interface to the
whole system basically is in these three functions here printf is used by the kernel to print error
messages and those error messages are considered sort of critical and we want those to go to the
front of the line too and be output immediately but the console read and console write

functions are used by the user mode programs to get data from the input and write data to the output
and so those will use the buffering here so let me start by talking about the output buffer and here
is a picture of it um it's called the UART transmit buffer and it's a fifo buffer it has a limited
size it's actually 32 not eight but you get the idea and there are two indexes into this

buffer a read index and a write index so to add to this buffer we store a byte there and increment w
and to get a character out of this buffer we read at the r index and then increment r and if there's
nothing in the buffer it's indicated when r and w catch up to each other and so when they're equal
that's an empty buffer situation now this is a wrap around buffer and so here's the

code we actually use to store something into the buffer we use the w and we store something in into
the buffer using that array but and then we increment after that but we do it modulo the size of the
buffer and likewise to read something we use the r index incrementing it after we've fetched the
thing but we take the index modulo the size so r and w just keep increasing okay

and so we could have a situation like this our buffer here is eight characters long and it stores a
b c d e f and so w uh is continuing to advance here but whenever we use w we're doing it mod modulo
the size of the buffer so we're really effectively looking at w right here and likewise with the
read index and using this technique where w and r just keep incrementing

is helpful because we can distinguish a full buffer situation from an empty buffer situation so
here's what a full buffer situation looks like uh when w exceeds r by exactly the size of the buffer
then we have a full condition and here the buffer is full of a b c d e f g h okay and if we take w
modulo uh the size of the buffer it would come back to five but uh here we can check for a full

buffer and uh you can ask yourself what would happen when these uh indexes r and w uh ultimately
wrap around you know after lots of input and output they're gonna wrap around and you can um think
about when under what situations this might be uh safe and might not cause a bug but uh one other
thing we need to think about is the fact that this buffer will be accessed by multiple processes and

multiple cores so it needs to be protected by a lock and it is the name of the lock is the UART
transmit lock and uh now let's take a look at the reed buffer it's a little bit different we have
three indexes into this buffer so here's the buffer some some array and uh called console buffer and
we've got indexes for read write and the end I guess as well as a lock that uh

guards this particular buffer and again it's a fifo buffer so we're reading when we take stuff out
of the buffer from the index r uh however when we add stuff we're adding it at the e index so we add
here but we also have the capability when we're typing input to hit a control u and that will erase
everything that's the last line of input that's in the buffer

so that's why we have this uh third index so here we've typed in w-o-r-l-d but we haven't hit the
new line yet so if we hit a control u at this point what is going to happen is that e will be backed
up okay to this point here if we get a control sorry if we get a new line we'll instead of ctrl u
we'll store it here and we'll advance w to this point and we'll

advance both w and e to this point here and so we will have in the buffer between the read and index
and the right index a number of lines each one being terminated by a newline character when we read
from the buffer we'll read a whole line and then stop okay so that's how the three indexes are used
so now let's look a little bit at the um the hardware um here I mentioned that the hardware uh

contains a couple of fifo cues that are essentially hidden and we won't worry too much about these
and to write a character we we ride a bike to some location and to read from the device we read a
byte from some location well those locations those are memory mapped locations and the way it works
is we have this device this UART device being memory mapped into some location some

address in the physical memory address space and in particular the address is indicated by this
constant UART 0 and it happens to be equated to some particular address and at that address there
are several bytes and these bytes are considered to be I o registers if you will and the key
register is the one that's offset zero and if we store into that particular location that's the
transmit

hold register we will be sending a byte to the output and if we read from that same location here
I'm saying store into this location at offset zero and here I'm saying load from the location at
offset zero that register has a different name the read register read hold register and we are
getting a byte from the keyboard so those two are our main registers there are some other registers
that we

uh need when we set things up and in particular uh we want to enable interrupts so we have to write
some special stuff to this register and if we want to use the on-chip fifos we need to write some
stuff to that register and we the line control register is used for setting the baud rate that's the
transmission rate up which would be relevant for real hardware

um and we also have another important one is the line status register this has two bits well that
are important to us one bit tells us whether there is any input in the read hold register so we can
read this register all we want but is are we getting new data or are we getting the previous
character over and over so we might want to check this bit here and likewise that

the the other bit tells us whether the transmit hold register is ready to receive the next output
byte so if we write to this register prematurely that by might not actually make it to the output it
might might get lost next let's look at the functions that are in the UART c file and there are five
of them that I want to look at we'll start with UART put c sync and

this is used to send a character to the hardware output as soon as possible and it's used by printf
to print error messages we want to print them to the display as quickly as possible bypassing the
output queue and for echoing the input characters again we want to bypass the fifo queue here next
we have UART put c this will be called by console.write and it will add a character to the output q

and in order to do that it will first have to acquire the lock and it will have to test for the
condition of the buffer having some extra space in it so it may wait but after it does that after it
puts a character in it will call you art start and what UART start does is handle this link here it
sends the next character to the hardware if there is a character in the buffer

and if the hardware is ready to receive another character on the input side we have UART get c which
is a little function that will read the next byte from the hardware if it is ready and just return
it it will not wait instead if there is no data ready from the keyboard it will just return a minus
one code and finally we have this UART interrupt function whenever an input character is available

to be fetched from the hardware or whenever the hardware is ready to receive the next output
character the hardware will generate an interrupt and that interrupt will then invoke the UART
interrupt function so it's called when the hardware interrupts and what this function will do is it
will retrieve as many input characters from the hardware as are available and for each one of

these it will call console interrupt which is another function function passing it one character for
each call and it will use that function to add those characters to the buffer and it will also call
UART start to send the next output character from the output buffer to the hardware so every time
the output is able to display a character and is ready for the next character an

interrupt will occur and the UART interrupt function will move the next character from the fifo
queue into the hardware okay next let's move on to the functions in console.c so the functions we
want to talk about are these four functions here the console.write function will be used to call
UART put c character by character and add characters to the output buffer so

that's what happens and since you are put c may sleep this function may sleep as well console read
will wait for input to be ready in the console input in particular it uh waits until the right index
has been moved to some place beyond the next new line or control d for into file and so that means
there's some input ready to be retrieved and what it will do is it will

copy characters from the input buffer and move them into the user space somewhere until it
encounters a new line character or the into file control d character and as I mentioned uh in the
UART interrupt function uh we call this function console interrupt for each input character and what
a console interrupt is going to do it is add a character to the input buffer so it's going to

add the character here and advance e it's also going to process uh characters like backspace and
control u as well as echoing the character to the output if it hits a new line or the into file
character then it's going to say okay we've got enough so that we can enable the uh input reader to
get something and so it's going to advance w to e and then it's also going to wake up

any sleeping functions that are waiting for input so it's it might wake up a console read function
right the console read function sleeps until there is something available that is r and w are not
the same and so a console read function may be sleeping so once we read all the way up to a new line
or the end of file we call wake up to wake up any anybody that's

to wake up all the functions that are waiting and finally we've got a function to initialize things
okay if uh you just want an idea of what's going on you can probably stop here next I'm going to
look at the code in more detail okay now that I've introduced the main organization of these two
files I'm going to go over the code that's in the UART.c and the console.c files

let's start with UART.c and we see the offsets for the registers remember in this diagram here I
showed the offsets for the register such as lsrs at offset 5 and we see lsr at offset 5. here we see
the transmit hold register and receive hold register and we have a little macro function here that
is used to add the base address of the UART memory map device to the offset

and we also have a couple functions here one is to go out and retrieve the value from this memory
mapped register and a second function write register which uh writes value v into memory mapped
register I had the uh transmit buffer okay that's this buffer here with its read and write indices
and that's defined right here the UART transmit buffer and the size is

32 here here is the read and the right here are the read and write indices and here is the lock that
protects this buffer we've also going to be referencing a variable called panicked and we'll see
that in a second but let's first move to the second page which is the initial initialize function so
here we are writing to what's called the interrupt enable register to disable

interrupts from this device so it won't be interrupting at least for the duration of this code and
uh then we are doing something with these four writes that are setting the baud rate to 38.4 k and
um so we do that that gets this device initialized properly and we also uh reset the internal fifos
in this device in this picture here I indicated that the device might have

some internal uh buffering going on and so here we are uh initializing and uh enabling that uh
internal buffering and finally we are writing to the interrupt enable register to cause it to
interrupt whenever we get a character received or whenever the transmit channel is ready to receive
another output character and we are also initializing the lock all right moving on to the next page

we have um actually I think I want to do this one first the UART put c sync because I think that's
simpler you can see that what it's doing it's got a while loop here and it's just reading the line
status register and checking the bit to see whether it's set and as long as it's clear it it does a
busy loop and the minute this bit is set that is the hardware's way of saying

it's ready to receive another byte to transmit and so at that point we write the character that it's
passed into the output register we also see a couple of other things we see it uh disabling
interrupts and re-enabling interrupts and we also see this code here panicked is a global variable
that gets set to true after any hardware error or sorry any kernel error so whenever we call the

panic function after printing the message we set the panicked flag and what this does is frees up
all future output so if that flag has already been set from a previous error we have an infinite
loop here this core when it hits this it's disabled the interrupts it's just going to go into an
infinite loop and lock up this core and the purpose of that is to once you get an error message you
don't

want to see anything else you don't see any other output if the thing is looping or something you
don't want to see a bunch of output you want to freeze up the other cores and prevent them from
doing any output so now let's take a look at user r UART put c and that is the function here I
described it with this uh [Music] picture here that's going to grab the

lock on the output buffer and add a byte to that so we see that we are acquiring the lock first and
we'll release it down below and again we have this panicked stuff if if we've had an error message
already this will just basically freeze up the core but assuming we haven't had that then what we've
got here is a while loop and we check right here and this is to check to see whether the

buffer is full uh remember in my diagram here I said that if the difference between r and w is the
size of the buffer then we have a full condition and that's what's being tested here if read plus
the size of the buffer is equal to the right then the buffer is full and we don't have an ability to
add another character to the buffer we don't want to lose that character we want to

wait so here we go to sleep and the thing that we're using for the channel to sleep on is the
address of the read index variable so this is just a number that we use to coordinate the sleep here
with the wake up that happens in the uh UART start function uh so uh that's the channel right there
and we're passing at the lock as we do with uh the sleep function

and once we are able to uh once the buffer is not full we come down and execute this code here which
will um add the character c to the transmit buffer at location w okay and it will do it mod the
buffer size and then increment w and we also call UART start in case the hardware is ready to
receive the next output byte we want to get it going on that so we call your start here

and then we release the lock and exit the loop and return so next let's look at um UART start and
that is here so we have a while loop here and what we're doing is checking to see whether the buffer
is empty okay if the buffer is empty there's no nothing to be sent to the output so if the read and
write index are equal then we immediately just give up and return

but assuming there is something in the buffer we need to keep going so here we read the status
register the line status register and we ask whether the bit that indicates that the hardware is
ready to receive a byte is is clear or not and so if the hardware is ready to receive the next byte
um then we can proceed but if it's not we just return immediately if it's not

ready then they will interrupt us later when it is ready to receive a byte but um if it's not ready
we we there's nothing we can do so we just return okay so the hardware is ready at this point uh
when we get to this point so we get a character from the buffer okay so we are going to advance so
we're going to grab the the read index and mod buffer size grab that character and

then increment the read index and we have now started up the output and we have freed a position in
the output buffer so we might want to wake up a waiting UART put c remember we just did a sleep on
that on that so here's where we do a wake up and again we're using the address of the receive or the
read index as our channel key and we also need to send that character

actually to the hardware so that's done at this point here next we have the um go through the get c
the UART getc function and that is called to get an input character get a character from the
keyboard if one is ready but not wait so here what we do is we read an input character from the
hardware but if none is wait if none is ready then we return minus one and we just read the status
register

and check the bit and if it says there's something ready in the read holding register then we grab
it otherwise we return minus one and finally we have this UART interrupt and whenever the the
hardware is uh ready to uh receive the next output character or because some input has arrived and
is ready to be fetched uh there's an interrupt and so this is going to be called

from the the function device interrupt which is in trap.c and so here we read the incoming
characters as many as are there and here we send uh characters to the next character to the output
so here we um call the UART get c to read a character and um if there's nothing there then we're
done reading all the input characters otherwise we call console interrupt console we're we're in the

interrupt uh handler so console interrupt will not sleep if there are too many characters and no
room in the input buffer they just get dropped we don't wait uh we just drop them and that will
happen in the console interrupt okay then uh we call UART start to send characters to the output
buffer next I want to walk through the code in console.c and I'm going to start with this macro

function c here from time to time we need to deal with control characters so for example right here
we're asking whether the character is equal to a control d and we also are concerned with things
like uh control p and control u and uh control h so remember what these control characters are these
are ascii characters it's a way of specifying uh any ascii value

that has a very low number so they start with the ampersand and ctrl a corresponds to number one
control b is two and so on control d corresponds to number four and normally we're used to uh using
things like backslash escapes to deal with control characters for example the new line characters
backslash n well that's equivalent to uh decimal 10 or hex a okay you can also type control

j uh to to achieve the same thing so you can try that on your keyboard if you type ctrl j it's
probably equivalent to hitting the enter or return key we also have control d which is an important
one and that is equivalent to number four so control d is number four here and so what this macro is
doing it's just allowing us to specify with a letter and it's just subtracting the

ampersand which is the control for control 0 here and so that's what's going on there this defined
constant backspace is a number that's greater than any ascii uh number uh the ascii codes go all the
way up to hex 7f or 127. and this is 2 decimal 256 so it's not a nasty code um now let's move into
cons put c uh constant c is basically called to um send a character directly to

the output so it actually sits between printf and UART put c sync printf actually doesn't call this
directly but it calls console put c and console.c just calls you output c so it's past the character
and then it passes it on through where it is then sent directly to the output hardware however it's
also used for echoing and for that purpose it checks to see

whether we have a special case of the backspace when the user types the backspace key or the delete
key the old serialio terminals required the terminal to back up and print a blank to overwrite the
character so for example if the user types a w and then a backspace it displays a w and then the
terminal needs to back up one character display a bank blank to get rid of the w and then

back up one more more time so that the next character will go where the w had gone before so that's
what's going on here if we have that situation we send to the output terminal to the display a
backspace character followed by a blank character followed by another backspace character okay next
let's move on to um this the second page and remember that our output buffer

um was defined I I pictured it here like this uh and it has these three indices and we see that
going on here here is the cons uh buffer of 128 bytes and here are our three indexes and we've also
got a cons lock a lock associated with this that protects the buffer and the indexes so next let's
look at the write function and it's called to send characters to

the output buffer so it's called here whenever we have a bunch of characters and we want to send
them to the output it's got to add them to the buffer one by one and so it will call UART put c for
each individual character to add it to the buffer and that's what's going on here it's passed a
pointer source and the count of characters to send the output and it's got a loop that does uh that

many times and uh for each character it calls UART put c now source points to the place where the
characters are to be found and n is the number of characters we want to send to the output these
characters can either be located in the kernel's address space or they can be located in user
virtual memory and this boolean flag right here tells which so this determines whether those
characters

will be gotten from the virtual address space which requires mapping or from a direct mapped address
and so that's what this function here is doing it is past a place to put the characters and the
number of characters to get and it's getting one character at a time and it's getting them from this
address okay so we're incrementing through the source buffer one by one and we're getting one

character at a time and we're passing it this flag to tell whether they're coming from a user
address space or not and the local variable c is just a place where we put them before we send them
to the output and then we return the number of characters that we successfully wrote and if anything
goes wrong for example the user provides a virtual address that's no

good or that then we return -1 from this and that will cause us to break and so we'll stop sending
characters to the output if we run into a bad virtual address okay let's move on to the next page
and this is the console read function and it too is passed a buffer here's the address of the buffer
and here's the number of characters in the buffer we want to get all the characters from this

buffer and sorry we want to read from the keyboard and send the data to the user by placing those
characters in this buffer so uh we will be getting characters out of the buffer here and so we need
to acquire the lock on that so we've got a while loop here and what we're going to do is we're going
to get characters out of the buffer and that's going on right here you can see

that we're grabbing a character from the buffer using the our index and then we're incrementing it
and let me skip this part right here but going down further we decrement our count and when we've
gotten as many characters as we want we then terminate the loop release the lock and we return and
we need to return how many characters we actually successfully read

so that's what's going on with target okay this target is saved here local variable which if we want
for example 100 characters we remember that we want 100 characters and then we decrement down and if
something goes wrong we execute this we exit the loop early uh we won't have gone all the way to
zero but if we get all the characters uh then n will be decremented all the

way to zero and we can just return target but if we exit the loop prematurely then we return
something less than target so as we are getting the characters let's go back to this point where we
get the characters um we get a character c and then here what we're going to do is use this function
either copy out and it's going to move well we're moving one character at a time and where are we

putting them well this is the um the place where we're getting them from okay that's this local
variable c buff here and destination is the address that was provided for us that's where we are to
place them and user destination is a boolean that tells whether this is a the user's virtual address
space or not so we're passing that and copy out will then move one in this case

one character from here to the destination and if that worked okay then it won't return a minus one
but if we had any problems we'll break and uh we'll just we won't have succeeded in uh decker in
moving that character so we don't decrement in instead we return uh some number short of our target
and if we do succeed uh in moving the character into the user's address space

then we increment uh the pointer uh for the next time around the loop uh if we had just moved the
new line we've then we've properly read a full complete line and we need to return at that point so
after moving the new line into the user's buffer we break out of the loop here now there's one other
thing moving back up to this point in the loop we are checking to see whether we got a control

d now if this is the um first uh character that uh we've gotten okay in other words uh this is the
first time through the loop n and target are equal then um this will not be true um and we will
simply break uh we will have gotten no characters so um the uh target here will be returned as zero
and uh then the next time around we'll read some more characters so

that's the way unix works uh when we get a control d we have to return an empty string a string of
length zero uh but then after that we can keep going on the other hand if we've already moved some
characters into the user's buffer then we basically need to not do the control d here we need to
stop okay and that's so we break but we also need to remember that we have hit a control d

and so the next time around we're going to need to return a line of zero characters so that's why we
decrement this r here so that the next time we're called in this in this function we can return zero
characters okay moving on to the next function in this file we have the console interrupt function
and this is past a single character remember how this is used uh whenever

the UART interrupt function is um getting a character from the keyboard it's going to call this
function for each character so uh going back to that UART interrupt function uh it's basically
looking to see whether there are any input characters ready from the hardware and if there are for
each one it's going to call this function so we've got a character and uh we need to do something
with it

okay the character is c and we are going to be putting it into the input buffer so we need to
acquire the lock that protects the input buffer the console buffer and uh we acquire it then we have
a switch statement and we look at what kind of character it is it is and um then following down here
after the switch statement we release the lock on the buffer and return

so we do a couple things if we see that the input character is a control p we handle it especially
by printing the process list we call proc dump so that could be useful for debugging the kernel and
seeing what's going on it's not uh if it is a let's do control h the backspace character will send a
control h that is an ascii 08 character to the hardware and the delete key

generally will send the ascii character hex 7f so regardless of which of these two characters is
with two keys is pressed or which character we receive we want to treat them by backing up and so we
ask here do we have anything uh in the input in other words have we gotten anything uh is e greater
than w if so then we want to back up e one byte and we want to

call cons put c with this special backspace code which will obliterate the character by doing a
backspace and then planck and then printing another backspace so that's how we deal with a backspace
or delete key and if we get a control u we do something similar we ask we basically are going to do
the same thing repeatedly until we back up e uh to the next control uh n that is

sorry a new line character or until we hit w so that's what's going on with this loop as long as e
we can back up some that is e and w are different and we haven't yet hit the new line character then
we do the same thing we decrement a and call cons put c with this backspace code now let's look at
what happens for everything else if the character is a null character

we ignore it and if the buffer is completely full we ignore it okay so we just drop the character if
the buffer is full here we're asking um doing the buffer full test with this right here if you hit
uh the enter key or the return key uh on different terminals can either return the so-called ascii
return code or the ascii newline code this is decimal 10 and this is decimal

13.

also 0x0d or 0x 0a some of us have memorized these and if it is return we convert it to the newline
character with this otherwise we keep it whatever it is then we echo it uh back to the um output and
then we store it into the buffer so here we're using e so we add it here whether it's a new line or
something else we add it here and increment e and so that's what's going on at this point

and then we determine whether we've got something that we need to wake up the reader okay so if it
was a control a new line or it was a control d or the buffer was full then we need to wake up any
sleeping console read function okay so that's what we do here first of all we update w so that moves
w all the way up to here which means that the reader can read as

much as as is available and then we do a wake up so this is using uh as a channel the address of the
read index variable and so that uh concludes this function uh we've got uh so we just we've got one
more function after that here's the end of that switch statement and the release and then we've got
an initialize function that is going to initialize the console lock

and it's also going to call the UART initialize function um don't want to really get into that this
here but we're saving pointers to the console read and console.write functions okay that's it see
you in the next video
