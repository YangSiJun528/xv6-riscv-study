# xv6 Kernel-12: Linking the Kernel

| Field | Value |
| --- | --- |
| Video ID | `ZW0BHOYDYFc` |
| URL | <https://www.youtube.com/watch?v=ZW0BHOYDYFc> |
| Source | `docs/reference/scripts/original-raw/12-xv6_Kernel-12_-_Linking_the_Kernel-ZW0BHOYDYFc.en-orig.md` |
| Status | machine-cleaned from YouTube `en-orig` automatic captions; review recommended |

---

## Corrected Transcript

This video is part of a series on the xv6 operating system kernel in this video I'm going to talk
about the linking of the object files to produce the image file I'm going to talk about the file
kernel.ld this is not a c program and not an assembly program instead it is a linker script file
which gives commands to the linker to tell how to put things in memory

so I'm going to start with talking about how memory is used by the kernel so here is a picture of uh
the memory uh of the kernel code and data and the linker will essentially place all the code in
memory and figure out where everything is going it doesn't actually put it in memory it just figures
out where in memory it's going the linker is normally something

that you don't think too much about as a c programmer the defaults that are used for the linker will
usually work for most user level programs but for building a kernel we need to be more specific and
include some commands and so I'll be talking about the file that gives those commands to the linker
and what the linker will do is we'll take all of the data from the object

files and combine it into the executable file so here in green I'm showing what comes from the
object files from the various parts of the kernel and the linker will figure out where in memory to
place all of that material and then it will build the executable file which contains all of the data
to be loaded in memory later when we get ready to load the kernel and run the kernel

uh QEMU the emulator that will be that is being used will read the executable file and put this
stuff in memory at the addresses that the linker has chosen so remember that when you compile
something a c program it produces assembly code and the assembly code is compiled or sorry assembled
and that produces an object file or maybe you just write assembly code

like we have in this file entry.s and the assembler produces object file an object file for that the
object files contain a number of segments or sometimes I call them sections but each segment will
contain a bunch of data that should be placed somewhere together in memory but the compiler and the
assembler don't really know where in memory that segment will be placed

so there can be several kinds of segments and these are given names uh the dot text is a name the
dot read only data dot ro data is a name dot data dot bss these are the different kinds of segments
and I'm not sure whether you want to call these the names of the segments or sort of generic types
of the segments but it's a doesn't really matter the dot text segments will contain

executable code the data segments will contain the variables of the program these are the things
that will be both read and updated and may have initial values we also have read-only data and that
kind of a segment will contain data that is be initialized but it will never be updated at runtime
so it's read-only and we also have something called the bss segments

these contain data that is not initialized with any particular value instead it is to be zero filled
or initialized with zeros when loaded into memory and so these segments don't need to don't have any
initializing data so they could be quite small in the object files and the executable files since
they don't have the data okay so as a result of compiling and assembling a bunch of

pieces all the all the files that we've talked about and we'll talk about in future videos we have a
bunch of segments we have a bunch of text segments data segments and so on the linker figures out
where in memory to place these things and it will place them starting at this location here which is
the two gigabyte uh boundary and it will put all the text segments

together all the data segments together all the other segments all the bss segments together and the
read-only data segments together we also have a special segment which we give a specific name to
this is for the trampoline code and that's called the sec for the trampoline section or segment and
that has to be placed specially on the linker's command line we'll specify a number of different

object files and the segments will be grouped together all the text segments from each of the object
files will be grouped together and they will be placed in order according to the order of the object
files as they are listed on the command line the command line to the linker will list the object
file for this entry code remember we'll we'll go into depth on the entry

file it's an assembly language file that is very short but it's the first thing that gets executed
yeah the object file for entry is listed first so that code will be the very first code and it
contains this file contains a label called underscore entry and it's actually the label of the very
first instruction in that file so that text segment will go first

the linker will place it first and so we will begin execution by jumping to the very first location
of the kernel that's the so-called entry point and then it will include the text segments from all
the other c files there are a bunch of them more than I've shown here we have this special uh
trampoline segment and uh it'll place that and then it will group all the

read-only segments together from the different object files all the data segments and all the bss
segments and it will produce an executable file that has four segments it will have it will combine
all the text segments into one dot text segment all the read-only data segments into one segment all
the data segments into one data segment and all the bss segments

into one bss segment in the course of placing these things in memory the blinker will figure out the
addresses of everything and this will allow it to fill in a lot of material that's uh that cannot be
filled in until this point for example when we load a variable in our code with the address of some
variable well the address of that variable is not known by

the compiler and not known by the assembler and will only be determined at length time and so at
that point the linker will go back and modify the code to insert the exact number and we may store
we may initialize some uh variables with addresses and the linker will go into the exact location of
that variable and and modify or or initialize that data to be the correct address

only the linker can do this because only the linker knows where things will actually go in memory
okay now let's look at this uh oh one other thing I want to mention is after the uh after we finish
the linking and begin the emulation and QEMU loads this thing into memory and the kernel actually
begins executing uh then the kernel will see where the pages are and it will mark

those pages as either executable or not and either writeable or not and readable so the kernel once
it's executing will build a page table that will describe these these the data that's in memory and
it will mark everything that was in the text segment as executable and it will mark everything else
as read write the linker will also determine the values for a couple of important

variables and in particular e-text is the boundary at the end of the code and the beginning of the
data portion of the kernel and end is the point at the very end of the data section uh so it will
set these variables uh and we'll also set this variable underscore trampoline that can only be done
by the linker and once the linker figures out where in memory things will be placed

it will place it will define those values and it will fill in those values in the code wherever
those symbols are used these little tick marks here indicate page boundaries by the way and we'll
see how the script file for the linker causes things to be aligned properly so now let's take a look
at this file kernel.ld this is written in a sort of a special language that the linker will
understand

and as I said most times when you use the linker you don't have a script file and the defaults or
the default commands will be used and you don't have to worry about this stuff but this is
essentially a sequence of commands and it's saying that produce an output executable file for the
RISC-V architecture that's what we're dealing with here and this symbol named underscore entry

which is defined by uh an assembly code statement in the entry...S file that will be the entry point
for the program okay and then it's saying in the output file create several sections create a text
segment a read-only data segment a data segment and a bss segment so it's saying create the text
segment read-only data and bss segments okay so dot indicates the current location or the an

address and so what this is saying is begin at this fixed location so it's telling the linker to put
things at this memory location here so start there and work up in memory and now it says for the
text segment in order to create that grab all of the dot text segments and anything that begins uh
with you this is a wild card here that the astro secures a wild card grab all of the segments

from all the files and throw them in to this segment that we're creating this star here is saying uh
it'..S file names let's think from all the object files that were on the command line uh grab those
segments okay so um it does it grabs them and and puts and puts them into the text segment this is
the text segment that we're creating here and these are the text segments that are

coming from all the files here normally they're just called dot text but this wild card picks up
anything else that we have these are done in order as they are specified uh on the command line so
the first object file on the command line will be the one for entry the entry.ob entry.o for the
entry dot ..S file and that will go first and that will put our entry right at the

very beginning next we say uh dot equals a line and this is basically forcing it to move up to the
next page boundary so that's the linker will then insert some uh unused memory here up to the next
page boundary and that gets us up to here and then it says define this label underscore trampoline
as that as the new current location so that the tells us this the linker that we

want to name a symbol underscore trampoline and we want its value to be that address right there and
then we're saying include the segment that's called sec uh section the trampoline section uh from
whatever file it comes from and so any and all segments with this name and there will only be one is
then placed here and then we have another align so we skip up to the next page boundary skip

up to the next page boundary and that's what I'm showing here and then it says uh um assert that the
current location minus trampoline is exactly one page and if it's not we print out an error message
that uh the trampoline code uh was larger than one page it won't be but we check that right here by
doing the subtraction and then finally we define a symbol e-text

at the current location so that's what we're doing here now we build the read-only data segment and
it's we do alignment this is a 16-byte alignment we see that occurring several places these segments
should be aligned on quad word boundaries 16 byte or 128 bit boundaries and we're saying pick up any
uh any segments that begin with uh .S read only data or dot ro data and some

variations [Music] then that creates the read-only data segment then we have the data segment and it
again it says do some alignment we're not doing page alignment we're just doing a byte alignment and
pick up everything from remember this is a wild card for the file and it's picking up all the uh dot
data segments and we have some variations on the different names uh I don't want to

go into details I'm not sure I even understand all these details uh bss again we have a similar
thing going on we're picking up all the segments or the sections that uh have names that start with
dot b s s and finally we are defining a name uh end so here we're defining the name end at wherever
we end so we might not be on a page boundary because there was no alignment here for a page uh

alignment so we just mark in right there oops we mark end right there uh so uh I'm indicating that
it may not be on a page boundary uh and uh that then allows the linker to uh produce the executable
file that we want and it will be loaded into memory at the right when if it's loaded into memory at
this address then the code will work properly so that's it for the linker uh if you

didn't understand all that uh don't worry about it uh I think you can understand the kernel and
isolation from understanding this uh linker script file okay I will see you in the next video
