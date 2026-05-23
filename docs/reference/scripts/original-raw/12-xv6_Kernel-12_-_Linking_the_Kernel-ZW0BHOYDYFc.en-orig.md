# xv6 Kernel-12: Linking the Kernel

| Field | Value |
| --- | --- |
| Video ID | `ZW0BHOYDYFc` |
| URL | <https://www.youtube.com/watch?v=ZW0BHOYDYFc> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.120 - 00:00:02.629` | this video is part of a series on the |
| `00:00:02.639 - 00:00:05.430` | xv6 operating system kernel |
| `00:00:05.440 - 00:00:06.950` | in this video i'm going to talk about |
| `00:00:06.960 - 00:00:10.310` | the linking of the object files to |
| `00:00:10.320 - 00:00:12.870` | produce the image file |
| `00:00:12.880 - 00:00:14.549` | i'm going to talk about the file |
| `00:00:14.559 - 00:00:16.550` | kernel.ld |
| `00:00:16.560 - 00:00:19.349` | this is not a c program and not an |
| `00:00:19.359 - 00:00:22.950` | assembly program instead it is a linker |
| `00:00:22.960 - 00:00:25.189` | script file which gives commands to the |
| `00:00:25.199 - 00:00:27.109` | linker to tell how to put things in |
| `00:00:27.119 - 00:00:28.150` | memory |
| `00:00:28.160 - 00:00:30.230` | so i'm going to start with talking about |
| `00:00:30.240 - 00:00:33.670` | how memory is used by the kernel |
| `00:00:33.680 - 00:00:36.470` | so here is |
| `00:00:36.480 - 00:00:38.229` | a picture of uh |
| `00:00:38.239 - 00:00:39.910` | the memory |
| `00:00:39.920 - 00:00:41.110` | uh of the |
| `00:00:41.120 - 00:00:43.430` | kernel code and data |
| `00:00:43.440 - 00:00:46.549` | and the linker will essentially place |
| `00:00:46.559 - 00:00:49.830` | all the code in memory and |
| `00:00:49.840 - 00:00:51.990` | figure out where everything is going it |
| `00:00:52.000 - 00:00:54.150` | doesn't actually put it in memory it |
| `00:00:54.160 - 00:00:56.830` | just figures out where in memory it's |
| `00:00:56.840 - 00:00:59.990` | going the linker is normally something |
| `00:01:00.000 - 00:01:01.750` | that you don't think too much about as a |
| `00:01:01.760 - 00:01:03.670` | c programmer |
| `00:01:03.680 - 00:01:05.429` | the defaults |
| `00:01:05.439 - 00:01:07.910` | that are used for the linker will |
| `00:01:07.920 - 00:01:09.830` | usually work for most user level |
| `00:01:09.840 - 00:01:11.429` | programs |
| `00:01:11.439 - 00:01:14.550` | but for building a kernel we need to be |
| `00:01:14.560 - 00:01:17.030` | more specific and include some commands |
| `00:01:17.040 - 00:01:18.630` | and so i'll be talking about the file |
| `00:01:18.640 - 00:01:21.350` | that gives those commands to the linker |
| `00:01:21.360 - 00:01:23.830` | and what the linker will do is we'll |
| `00:01:23.840 - 00:01:25.990` | take all of the data from the object |
| `00:01:26.000 - 00:01:27.350` | files |
| `00:01:27.360 - 00:01:28.950` | and combine it |
| `00:01:28.960 - 00:01:30.230` | into |
| `00:01:30.240 - 00:01:33.030` | the executable file |
| `00:01:33.040 - 00:01:35.510` | so here in green i'm showing what comes |
| `00:01:35.520 - 00:01:37.670` | from the object files |
| `00:01:37.680 - 00:01:41.030` | from the various parts of the kernel and |
| `00:01:41.040 - 00:01:43.190` | the linker will figure out where in |
| `00:01:43.200 - 00:01:45.990` | memory to place all of that material |
| `00:01:46.000 - 00:01:48.069` | and then it will build |
| `00:01:48.079 - 00:01:50.789` | the executable file which contains all |
| `00:01:50.799 - 00:01:53.749` | of the data to be loaded in memory |
| `00:01:53.759 - 00:01:55.670` | later when we get ready to load the |
| `00:01:55.680 - 00:01:57.590` | kernel and run the kernel |
| `00:01:57.600 - 00:01:58.630` | uh |
| `00:01:58.640 - 00:02:01.190` | kimu the emulator that will be that is |
| `00:02:01.200 - 00:02:04.069` | being used will read the executable file |
| `00:02:04.079 - 00:02:06.870` | and put this stuff in memory at the |
| `00:02:06.880 - 00:02:10.550` | addresses that the linker has chosen |
| `00:02:10.560 - 00:02:12.070` | so remember that when you compile |
| `00:02:12.080 - 00:02:13.190` | something |
| `00:02:13.200 - 00:02:16.070` | a c program it produces assembly code |
| `00:02:16.080 - 00:02:18.070` | and the assembly code is compiled or |
| `00:02:18.080 - 00:02:20.150` | sorry assembled and that produces an |
| `00:02:20.160 - 00:02:21.510` | object file |
| `00:02:21.520 - 00:02:23.670` | or maybe you just write assembly code |
| `00:02:23.680 - 00:02:26.390` | like we have in this file entry.s |
| `00:02:26.400 - 00:02:27.190` | and |
| `00:02:27.200 - 00:02:30.150` | the assembler produces object file |
| `00:02:30.160 - 00:02:31.910` | an object file for |
| `00:02:31.920 - 00:02:33.910` | that |
| `00:02:33.920 - 00:02:36.070` | the object files contain a number of |
| `00:02:36.080 - 00:02:37.670` | segments or sometimes i call them |
| `00:02:37.680 - 00:02:38.710` | sections |
| `00:02:38.720 - 00:02:39.589` | but |
| `00:02:39.599 - 00:02:41.030` | each segment |
| `00:02:41.040 - 00:02:43.910` | will contain a bunch of data that should |
| `00:02:43.920 - 00:02:46.949` | be placed somewhere together in memory |
| `00:02:46.959 - 00:02:48.390` | but |
| `00:02:48.400 - 00:02:50.710` | the compiler and the assembler don't |
| `00:02:50.720 - 00:02:53.270` | really know where in memory that segment |
| `00:02:53.280 - 00:02:55.990` | will be placed |
| `00:02:56.000 - 00:02:57.589` | so there can be several kinds of |
| `00:02:57.599 - 00:03:00.710` | segments and these are given names |
| `00:03:00.720 - 00:03:04.949` | uh the dot text is a name the dot read |
| `00:03:04.959 - 00:03:09.030` | only data dot ro data is a name dot data |
| `00:03:09.040 - 00:03:11.509` | dot bss these are the different kinds of |
| `00:03:11.519 - 00:03:13.110` | segments and |
| `00:03:13.120 - 00:03:14.229` | i'm not sure whether you want to call |
| `00:03:14.239 - 00:03:16.149` | these the names of the segments or sort |
| `00:03:16.159 - 00:03:18.309` | of generic types of the segments but |
| `00:03:18.319 - 00:03:19.509` | it's a |
| `00:03:19.519 - 00:03:22.390` | doesn't really matter |
| `00:03:22.400 - 00:03:24.630` | the dot text segments will contain |
| `00:03:24.640 - 00:03:26.309` | executable code |
| `00:03:26.319 - 00:03:29.030` | the data segments will contain the |
| `00:03:29.040 - 00:03:31.670` | variables of the program these are |
| `00:03:31.680 - 00:03:34.390` | the things that will be both read and |
| `00:03:34.400 - 00:03:38.309` | updated and may have initial values |
| `00:03:38.319 - 00:03:41.670` | we also have read-only data and |
| `00:03:41.680 - 00:03:43.830` | that kind of a segment will contain data |
| `00:03:43.840 - 00:03:45.670` | that is |
| `00:03:45.680 - 00:03:48.149` | be initialized but it will never be |
| `00:03:48.159 - 00:03:51.270` | updated at runtime so it's read-only |
| `00:03:51.280 - 00:03:52.789` | and we also have something called the |
| `00:03:52.799 - 00:03:55.030` | bss segments |
| `00:03:55.040 - 00:03:56.630` | these contain |
| `00:03:56.640 - 00:03:58.309` | data that |
| `00:03:58.319 - 00:03:59.990` | is not initialized with any particular |
| `00:04:00.000 - 00:04:01.750` | value instead |
| `00:04:01.760 - 00:04:04.869` | it is to be zero filled or initialized |
| `00:04:04.879 - 00:04:07.589` | with zeros when loaded into memory and |
| `00:04:07.599 - 00:04:08.470` | so |
| `00:04:08.480 - 00:04:10.550` | these segments don't need to don't have |
| `00:04:10.560 - 00:04:12.070` | any initializing data so they could be |
| `00:04:12.080 - 00:04:14.149` | quite small in the object files and the |
| `00:04:14.159 - 00:04:16.229` | executable files |
| `00:04:16.239 - 00:04:18.469` | since they don't have the data |
| `00:04:18.479 - 00:04:21.430` | okay so |
| `00:04:21.440 - 00:04:23.670` | as a result of |
| `00:04:23.680 - 00:04:25.350` | compiling and assembling a bunch of |
| `00:04:25.360 - 00:04:27.510` | pieces all the all the files that we've |
| `00:04:27.520 - 00:04:29.270` | talked about and we'll talk about in |
| `00:04:29.280 - 00:04:30.629` | future videos |
| `00:04:30.639 - 00:04:33.030` | we have a bunch of segments |
| `00:04:33.040 - 00:04:34.629` | we have a bunch of text segments data |
| `00:04:34.639 - 00:04:36.310` | segments and so on |
| `00:04:36.320 - 00:04:38.390` | the linker figures out where in memory |
| `00:04:38.400 - 00:04:40.230` | to place these things |
| `00:04:40.240 - 00:04:41.749` | and it will |
| `00:04:41.759 - 00:04:44.070` | place them starting at this location |
| `00:04:44.080 - 00:04:46.629` | here which is the two gigabyte uh |
| `00:04:46.639 - 00:04:47.670` | boundary |
| `00:04:47.680 - 00:04:49.590` | and it will put all the text segments |
| `00:04:49.600 - 00:04:50.550` | together |
| `00:04:50.560 - 00:04:52.390` | all the data segments together all the |
| `00:04:52.400 - 00:04:54.230` | other segments all the bss segments |
| `00:04:54.240 - 00:04:56.230` | together and the read-only data segments |
| `00:04:56.240 - 00:04:57.270` | together |
| `00:04:57.280 - 00:04:59.270` | we also have a special segment which we |
| `00:04:59.280 - 00:05:01.510` | give a specific name to this is for the |
| `00:05:01.520 - 00:05:03.350` | trampoline code and that's called the |
| `00:05:03.360 - 00:05:04.950` | sec |
| `00:05:04.960 - 00:05:06.790` | for the trampoline |
| `00:05:06.800 - 00:05:08.550` | section or segment |
| `00:05:08.560 - 00:05:14.070` | and that has to be placed specially |
| `00:05:14.080 - 00:05:16.629` | on the linker's command line |
| `00:05:16.639 - 00:05:18.469` | we'll specify a number of different |
| `00:05:18.479 - 00:05:19.830` | object files |
| `00:05:19.840 - 00:05:21.029` | and |
| `00:05:21.039 - 00:05:22.150` | the |
| `00:05:22.160 - 00:05:24.150` | segments will be grouped together all |
| `00:05:24.160 - 00:05:25.909` | the text segments from each of the |
| `00:05:25.919 - 00:05:28.310` | object files will be grouped together |
| `00:05:28.320 - 00:05:31.270` | and they will be placed in order |
| `00:05:31.280 - 00:05:32.790` | according to the order |
| `00:05:32.800 - 00:05:34.870` | of the object files as they are listed |
| `00:05:34.880 - 00:05:36.870` | on the command line |
| `00:05:36.880 - 00:05:39.270` | the command line to the linker will list |
| `00:05:39.280 - 00:05:42.070` | the object file for this entry code |
| `00:05:42.080 - 00:05:43.029` | remember |
| `00:05:43.039 - 00:05:46.950` | we'll we'll go into depth on the entry |
| `00:05:46.960 - 00:05:50.150` | file it's an assembly language |
| `00:05:50.160 - 00:05:52.710` | file that is very short but it's the |
| `00:05:52.720 - 00:05:55.189` | first thing that gets executed yeah the |
| `00:05:55.199 - 00:05:58.390` | object file for entry is listed first so |
| `00:05:58.400 - 00:06:00.309` | that code will be the very first code |
| `00:06:00.319 - 00:06:01.990` | and it contains |
| `00:06:02.000 - 00:06:03.909` | this file contains |
| `00:06:03.919 - 00:06:06.870` | a label called underscore entry and it's |
| `00:06:06.880 - 00:06:08.309` | actually the |
| `00:06:08.319 - 00:06:10.550` | label of the very first instruction in |
| `00:06:10.560 - 00:06:11.510` | that |
| `00:06:11.520 - 00:06:14.550` | file so that text segment will go first |
| `00:06:14.560 - 00:06:16.870` | the linker will place it first |
| `00:06:16.880 - 00:06:19.430` | and so we will begin execution by |
| `00:06:19.440 - 00:06:22.070` | jumping to the very first |
| `00:06:22.080 - 00:06:24.790` | location of the kernel that's the |
| `00:06:24.800 - 00:06:26.950` | so-called entry point and then it will |
| `00:06:26.960 - 00:06:28.309` | include the text segments from all the |
| `00:06:28.319 - 00:06:30.629` | other c files there are a bunch of them |
| `00:06:30.639 - 00:06:32.710` | more than i've shown here |
| `00:06:32.720 - 00:06:35.510` | we have this special |
| `00:06:35.520 - 00:06:38.469` | uh trampoline segment and uh it'll place |
| `00:06:38.479 - 00:06:40.309` | that and then it will group all the |
| `00:06:40.319 - 00:06:41.909` | read-only segments together from the |
| `00:06:41.919 - 00:06:43.830` | different object files |
| `00:06:43.840 - 00:06:46.230` | all the data segments and all the bss |
| `00:06:46.240 - 00:06:47.830` | segments and it will produce an |
| `00:06:47.840 - 00:06:50.390` | executable file that has |
| `00:06:50.400 - 00:06:51.189` | four |
| `00:06:51.199 - 00:06:53.189` | segments it will have it will combine |
| `00:06:53.199 - 00:06:56.150` | all the text segments into one dot text |
| `00:06:56.160 - 00:06:57.189` | segment |
| `00:06:57.199 - 00:06:59.270` | all the read-only data segments into one |
| `00:06:59.280 - 00:07:01.670` | segment all the data segments into one |
| `00:07:01.680 - 00:07:03.909` | data segment and all the bss segments |
| `00:07:03.919 - 00:07:05.350` | into one bss |
| `00:07:05.360 - 00:07:06.550` | segment |
| `00:07:06.560 - 00:07:07.990` | in the course of placing these things in |
| `00:07:08.000 - 00:07:10.469` | memory the |
| `00:07:10.479 - 00:07:12.870` | blinker will figure out the addresses of |
| `00:07:12.880 - 00:07:14.950` | everything and this will allow it to |
| `00:07:14.960 - 00:07:18.390` | fill in a lot of material that's uh |
| `00:07:18.400 - 00:07:20.390` | that cannot be filled in until this |
| `00:07:20.400 - 00:07:21.270` | point |
| `00:07:21.280 - 00:07:23.350` | for example when we load a variable in |
| `00:07:23.360 - 00:07:24.790` | our code with |
| `00:07:24.800 - 00:07:26.790` | the address of some variable well the |
| `00:07:26.800 - 00:07:28.390` | address of that variable is not known by |
| `00:07:28.400 - 00:07:29.990` | the compiler and not known by the |
| `00:07:30.000 - 00:07:32.629` | assembler and will only be determined at |
| `00:07:32.639 - 00:07:35.589` | length time and so at that point the |
| `00:07:35.599 - 00:07:38.790` | linker will go back and modify the code |
| `00:07:38.800 - 00:07:41.270` | to insert the exact number |
| `00:07:41.280 - 00:07:42.870` | and we may store |
| `00:07:42.880 - 00:07:45.189` | we may initialize some uh variables with |
| `00:07:45.199 - 00:07:46.309` | addresses |
| `00:07:46.319 - 00:07:48.070` | and the linker |
| `00:07:48.080 - 00:07:49.110` | will |
| `00:07:49.120 - 00:07:51.990` | go into the exact location of that |
| `00:07:52.000 - 00:07:53.670` | variable and and |
| `00:07:53.680 - 00:07:57.430` | modify or or initialize that data to be |
| `00:07:57.440 - 00:07:59.749` | the correct address |
| `00:07:59.759 - 00:08:01.670` | only the linker can do this because only |
| `00:08:01.680 - 00:08:03.189` | the linker knows where things will |
| `00:08:03.199 - 00:08:06.309` | actually go in memory |
| `00:08:06.319 - 00:08:09.990` | okay now let's look at this uh |
| `00:08:10.000 - 00:08:14.070` | oh one other thing i want to mention is |
| `00:08:14.080 - 00:08:15.270` | after the |
| `00:08:15.280 - 00:08:18.150` | uh after we finish the linking and begin |
| `00:08:18.160 - 00:08:20.869` | the emulation and kimu loads this thing |
| `00:08:20.879 - 00:08:22.869` | into memory and the kernel actually |
| `00:08:22.879 - 00:08:24.390` | begins executing |
| `00:08:24.400 - 00:08:27.589` | uh then the kernel will |
| `00:08:27.599 - 00:08:30.150` | see where the pages are and it will mark |
| `00:08:30.160 - 00:08:32.230` | those pages as either |
| `00:08:32.240 - 00:08:35.509` | executable or not and either writeable |
| `00:08:35.519 - 00:08:38.709` | or not and readable so |
| `00:08:38.719 - 00:08:40.230` | the kernel once it's executing will |
| `00:08:40.240 - 00:08:42.790` | build a page table |
| `00:08:42.800 - 00:08:45.509` | that will describe these these |
| `00:08:45.519 - 00:08:47.590` | the data that's in memory and it will |
| `00:08:47.600 - 00:08:49.030` | mark everything that was in the text |
| `00:08:49.040 - 00:08:52.150` | segment as executable and it will mark |
| `00:08:52.160 - 00:08:55.110` | everything else as read write |
| `00:08:55.120 - 00:08:57.110` | the linker will also determine |
| `00:08:57.120 - 00:08:59.110` | the values for a couple of important |
| `00:08:59.120 - 00:09:01.750` | variables and in particular e-text is |
| `00:09:01.760 - 00:09:04.630` | the boundary at the end of the code and |
| `00:09:04.640 - 00:09:06.949` | the beginning of the data portion of the |
| `00:09:06.959 - 00:09:08.710` | kernel and |
| `00:09:08.720 - 00:09:10.470` | end is the |
| `00:09:10.480 - 00:09:12.870` | point at the very end of the |
| `00:09:12.880 - 00:09:14.470` | data section |
| `00:09:14.480 - 00:09:16.389` | uh so it |
| `00:09:16.399 - 00:09:17.190` | will |
| `00:09:17.200 - 00:09:19.590` | set these variables |
| `00:09:19.600 - 00:09:21.430` | uh and we'll also set this variable |
| `00:09:21.440 - 00:09:23.430` | underscore trampoline |
| `00:09:23.440 - 00:09:25.910` | that can only be done by the linker and |
| `00:09:25.920 - 00:09:27.269` | once the linker figures out where in |
| `00:09:27.279 - 00:09:29.190` | memory things will be placed |
| `00:09:29.200 - 00:09:32.389` | it will place it will define those |
| `00:09:32.399 - 00:09:35.110` | values and it will fill in those values |
| `00:09:35.120 - 00:09:38.070` | in the code wherever those |
| `00:09:38.080 - 00:09:41.350` | symbols are used |
| `00:09:41.360 - 00:09:43.190` | these little tick marks here indicate |
| `00:09:43.200 - 00:09:46.230` | page boundaries by the way and we'll see |
| `00:09:46.240 - 00:09:47.110` | how |
| `00:09:47.120 - 00:09:48.150` | the |
| `00:09:48.160 - 00:09:50.150` | script file for the linker causes things |
| `00:09:50.160 - 00:09:52.710` | to be aligned properly so now let's take |
| `00:09:52.720 - 00:09:55.750` | a look at this file kernel.ld |
| `00:09:55.760 - 00:09:57.509` | this is written in a sort of a special |
| `00:09:57.519 - 00:09:59.910` | language that the linker will understand |
| `00:09:59.920 - 00:10:01.509` | and as i said most |
| `00:10:01.519 - 00:10:02.949` | times when you use the linker you don't |
| `00:10:02.959 - 00:10:05.350` | have a script file and the defaults or |
| `00:10:05.360 - 00:10:07.430` | the default commands will be used and |
| `00:10:07.440 - 00:10:09.590` | you don't have to worry about this stuff |
| `00:10:09.600 - 00:10:10.310` | but |
| `00:10:10.320 - 00:10:11.670` | this is essentially a sequence of |
| `00:10:11.680 - 00:10:13.670` | commands and it's saying that |
| `00:10:13.680 - 00:10:16.470` | produce an output executable file for |
| `00:10:16.480 - 00:10:17.910` | the risk 5 architecture that's what |
| `00:10:17.920 - 00:10:19.430` | we're dealing with here |
| `00:10:19.440 - 00:10:22.949` | and this symbol named underscore entry |
| `00:10:22.959 - 00:10:25.190` | which is defined by |
| `00:10:25.200 - 00:10:27.910` | uh an assembly code statement in the |
| `00:10:27.920 - 00:10:29.670` | entry.s file |
| `00:10:29.680 - 00:10:31.269` | that will be the entry point for the |
| `00:10:31.279 - 00:10:33.030` | program okay |
| `00:10:33.040 - 00:10:34.710` | and then it's saying |
| `00:10:34.720 - 00:10:36.630` | in the output file create several |
| `00:10:36.640 - 00:10:38.790` | sections create a text segment |
| `00:10:38.800 - 00:10:40.710` | a read-only data segment a data segment |
| `00:10:40.720 - 00:10:43.350` | and a bss segment so it's saying |
| `00:10:43.360 - 00:10:46.310` | create the text segment read-only data |
| `00:10:46.320 - 00:10:48.230` | and bss segments |
| `00:10:48.240 - 00:10:49.990` | okay so |
| `00:10:50.000 - 00:10:50.790` | dot |
| `00:10:50.800 - 00:10:53.269` | indicates the current location or the an |
| `00:10:53.279 - 00:10:55.590` | address and so what this is saying is |
| `00:10:55.600 - 00:10:57.269` | begin at this |
| `00:10:57.279 - 00:10:59.910` | fixed location so it's telling the |
| `00:10:59.920 - 00:11:02.630` | linker to put things at this memory |
| `00:11:02.640 - 00:11:05.750` | location here so start there and work up |
| `00:11:05.760 - 00:11:07.910` | in memory |
| `00:11:07.920 - 00:11:10.949` | and now it says for the text segment |
| `00:11:10.959 - 00:11:12.630` | in order to create that |
| `00:11:12.640 - 00:11:14.710` | grab |
| `00:11:14.720 - 00:11:17.110` | all of the dot text segments and |
| `00:11:17.120 - 00:11:20.150` | anything that begins uh with you this is |
| `00:11:20.160 - 00:11:22.230` | a wild card here that the astro secures |
| `00:11:22.240 - 00:11:24.310` | a wild card grab all of the segments |
| `00:11:24.320 - 00:11:27.190` | from all the files and throw them in |
| `00:11:27.200 - 00:11:30.230` | to this segment that we're creating this |
| `00:11:30.240 - 00:11:32.949` | star here is saying uh it's file names |
| `00:11:32.959 - 00:11:34.630` | let's think from all the object files |
| `00:11:34.640 - 00:11:36.949` | that were on the command line uh grab |
| `00:11:36.959 - 00:11:38.230` | those segments |
| `00:11:38.240 - 00:11:39.190` | okay so |
| `00:11:39.200 - 00:11:41.670` | um it does it grabs them and and puts |
| `00:11:41.680 - 00:11:44.069` | and puts them into the text segment this |
| `00:11:44.079 - 00:11:46.710` | is the text segment that we're creating |
| `00:11:46.720 - 00:11:47.750` | here |
| `00:11:47.760 - 00:11:49.670` | and these are the text segments that are |
| `00:11:49.680 - 00:11:51.590` | coming from all the files |
| `00:11:51.600 - 00:11:53.030` | here |
| `00:11:53.040 - 00:11:54.629` | normally they're just called dot text |
| `00:11:54.639 - 00:11:57.030` | but this wild card picks up anything |
| `00:11:57.040 - 00:12:00.790` | else that we have |
| `00:12:00.800 - 00:12:02.310` | these are done in order as they are |
| `00:12:02.320 - 00:12:05.350` | specified uh on the command line so the |
| `00:12:05.360 - 00:12:06.870` | first object file on the command line |
| `00:12:06.880 - 00:12:09.430` | will be the one for entry |
| `00:12:09.440 - 00:12:13.430` | the entry.ob entry.o for the entry |
| `00:12:13.440 - 00:12:15.829` | dot s file and that will go first and |
| `00:12:15.839 - 00:12:17.430` | that will put our entry right at the |
| `00:12:17.440 - 00:12:19.670` | very beginning |
| `00:12:19.680 - 00:12:21.350` | next we say uh |
| `00:12:21.360 - 00:12:23.829` | dot equals a line and this is basically |
| `00:12:23.839 - 00:12:25.509` | forcing it to move up to the next page |
| `00:12:25.519 - 00:12:28.310` | boundary so that's the linker will then |
| `00:12:28.320 - 00:12:29.829` | insert some |
| `00:12:29.839 - 00:12:32.710` | uh unused memory here up to the next |
| `00:12:32.720 - 00:12:34.629` | page boundary and that gets us up to |
| `00:12:34.639 - 00:12:35.590` | here |
| `00:12:35.600 - 00:12:37.829` | and then it says define this label |
| `00:12:37.839 - 00:12:39.990` | underscore trampoline as that |
| `00:12:40.000 - 00:12:43.350` | as the new current location so that |
| `00:12:43.360 - 00:12:46.069` | the tells us this the linker that we |
| `00:12:46.079 - 00:12:48.069` | want to name a symbol underscore |
| `00:12:48.079 - 00:12:50.230` | trampoline and we want its value to be |
| `00:12:50.240 - 00:12:52.389` | that address right there |
| `00:12:52.399 - 00:12:54.389` | and then we're saying include the |
| `00:12:54.399 - 00:12:57.350` | segment that's called sec |
| `00:12:57.360 - 00:13:00.230` | uh section the trampoline section |
| `00:13:00.240 - 00:13:02.870` | uh from whatever file it comes from and |
| `00:13:02.880 - 00:13:05.350` | so any and all segments with this name |
| `00:13:05.360 - 00:13:07.590` | and there will only be one is then |
| `00:13:07.600 - 00:13:09.190` | placed here |
| `00:13:09.200 - 00:13:10.790` | and then we have another align so we |
| `00:13:10.800 - 00:13:13.030` | skip up to the next page boundary skip |
| `00:13:13.040 - 00:13:14.870` | up to the next page boundary and that's |
| `00:13:14.880 - 00:13:17.030` | what i'm showing here and then it says |
| `00:13:17.040 - 00:13:19.110` | uh |
| `00:13:19.120 - 00:13:20.470` | um |
| `00:13:20.480 - 00:13:23.829` | assert that the current location minus |
| `00:13:23.839 - 00:13:26.790` | trampoline is exactly one page and if |
| `00:13:26.800 - 00:13:28.790` | it's not we print out an error message |
| `00:13:28.800 - 00:13:31.509` | that uh the trampoline code uh was |
| `00:13:31.519 - 00:13:34.790` | larger than one page it won't be but we |
| `00:13:34.800 - 00:13:36.790` | check that right here by doing the |
| `00:13:36.800 - 00:13:39.430` | subtraction and then finally we define a |
| `00:13:39.440 - 00:13:41.189` | symbol e-text |
| `00:13:41.199 - 00:13:43.110` | at the current location |
| `00:13:43.120 - 00:13:43.910` | so |
| `00:13:43.920 - 00:13:45.750` | that's what we're doing here now we |
| `00:13:45.760 - 00:13:48.550` | build the read-only data segment and |
| `00:13:48.560 - 00:13:49.670` | it's |
| `00:13:49.680 - 00:13:52.310` | we do alignment this is a 16-byte |
| `00:13:52.320 - 00:13:53.829` | alignment |
| `00:13:53.839 - 00:13:56.230` | we see that occurring several places |
| `00:13:56.240 - 00:13:58.949` | these segments should be |
| `00:13:58.959 - 00:14:00.710` | aligned on |
| `00:14:00.720 - 00:14:02.470` | quad word boundaries |
| `00:14:02.480 - 00:14:05.269` | 16 byte or 128 bit boundaries |
| `00:14:05.279 - 00:14:07.990` | and we're saying pick up any uh |
| `00:14:08.000 - 00:14:10.230` | any segments that begin |
| `00:14:10.240 - 00:14:11.910` | with uh |
| `00:14:11.920 - 00:14:13.030` | dot |
| `00:14:13.040 - 00:14:16.389` | s read only data or dot ro data and some |
| `00:14:16.399 - 00:14:17.850` | variations |
| `00:14:17.860 - 00:14:19.350` | [Music] |
| `00:14:19.360 - 00:14:21.829` | then that creates the read-only data |
| `00:14:21.839 - 00:14:24.069` | segment then we have the data segment |
| `00:14:24.079 - 00:14:26.629` | and it again it says do some alignment |
| `00:14:26.639 - 00:14:28.069` | we're not doing page alignment we're |
| `00:14:28.079 - 00:14:30.470` | just doing a byte alignment |
| `00:14:30.480 - 00:14:32.550` | and pick up everything from remember |
| `00:14:32.560 - 00:14:34.870` | this is a wild card for the file and |
| `00:14:34.880 - 00:14:36.790` | it's picking up all the uh dot data |
| `00:14:36.800 - 00:14:39.269` | segments and we have some variations on |
| `00:14:39.279 - 00:14:41.350` | the different names uh i don't want to |
| `00:14:41.360 - 00:14:42.949` | go into details i'm not sure i even |
| `00:14:42.959 - 00:14:45.189` | understand all these details |
| `00:14:45.199 - 00:14:47.269` | uh bss |
| `00:14:47.279 - 00:14:49.189` | again we have a similar thing going on |
| `00:14:49.199 - 00:14:51.670` | we're picking up all the segments or the |
| `00:14:51.680 - 00:14:54.150` | sections that uh have names that start |
| `00:14:54.160 - 00:14:56.870` | with dot b s s |
| `00:14:56.880 - 00:14:58.550` | and finally |
| `00:14:58.560 - 00:15:00.470` | we are defining a name |
| `00:15:00.480 - 00:15:02.949` | uh end so here we're defining the name |
| `00:15:02.959 - 00:15:05.430` | end at wherever we end so we might not |
| `00:15:05.440 - 00:15:06.710` | be on a page boundary because there was |
| `00:15:06.720 - 00:15:09.269` | no alignment here for a page uh |
| `00:15:09.279 - 00:15:13.189` | alignment so we just mark in right there |
| `00:15:13.199 - 00:15:15.670` | oops we mark end right there |
| `00:15:15.680 - 00:15:17.509` | uh so uh i'm indicating that it may not |
| `00:15:17.519 - 00:15:21.110` | be on a page boundary uh |
| `00:15:21.120 - 00:15:24.949` | and uh that then allows the linker to uh |
| `00:15:24.959 - 00:15:27.829` | produce the executable file that we want |
| `00:15:27.839 - 00:15:30.870` | and it will be loaded into memory at the |
| `00:15:30.880 - 00:15:31.670` | right |
| `00:15:31.680 - 00:15:33.350` | when if it's loaded into memory at this |
| `00:15:33.360 - 00:15:34.470` | address |
| `00:15:34.480 - 00:15:37.430` | then the code will work properly |
| `00:15:37.440 - 00:15:40.550` | so that's it for the linker uh if you |
| `00:15:40.560 - 00:15:42.069` | didn't understand all that uh don't |
| `00:15:42.079 - 00:15:43.749` | worry about it uh i think you can |
| `00:15:43.759 - 00:15:45.749` | understand the kernel and isolation from |
| `00:15:45.759 - 00:15:49.670` | understanding this uh linker script file |
| `00:15:49.680 - 00:15:53.959` | okay i will see you in the next video |
