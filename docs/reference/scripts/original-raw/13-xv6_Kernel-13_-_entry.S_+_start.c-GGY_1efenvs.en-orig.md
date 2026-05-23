# xv6 Kernel-13: entry.S + start.c

| Field | Value |
| --- | --- |
| Video ID | `GGY_1efenvs` |
| URL | <https://www.youtube.com/watch?v=GGY_1efenvs> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.439 - 00:00:03.030` | this video is part of a series on the |
| `00:00:03.040 - 00:00:05.590` | xv6 operating system kernel |
| `00:00:05.600 - 00:00:07.190` | in this video i'm going to talk about |
| `00:00:07.200 - 00:00:09.589` | the kernel startup procedures |
| `00:00:09.599 - 00:00:12.070` | i'll go over the code in the file |
| `00:00:12.080 - 00:00:14.910` | entry.s and the file |
| `00:00:14.920 - 00:00:17.990` | start.c entry.s is pretty short it |
| `00:00:18.000 - 00:00:19.269` | contains a few assembly code |
| `00:00:19.279 - 00:00:20.790` | instructions |
| `00:00:20.800 - 00:00:22.630` | start.c contains |
| `00:00:22.640 - 00:00:26.310` | two functions start and timer init |
| `00:00:26.320 - 00:00:28.390` | all of this code is executed in machine |
| `00:00:28.400 - 00:00:30.870` | mode and at the very end of it we switch |
| `00:00:30.880 - 00:00:34.150` | into supervisor mode and jump to the |
| `00:00:34.160 - 00:00:35.670` | main function |
| `00:00:35.680 - 00:00:38.229` | i'll also talk about timer interrupts |
| `00:00:38.239 - 00:00:40.069` | we'll see how they're set up in the |
| `00:00:40.079 - 00:00:42.069` | timer init function |
| `00:00:42.079 - 00:00:43.510` | and we'll look at the code for the |
| `00:00:43.520 - 00:00:45.190` | interrupt handler which is called |
| `00:00:45.200 - 00:00:47.430` | timerback and that comes from a file |
| `00:00:47.440 - 00:00:50.310` | called kernelvec.s |
| `00:00:50.320 - 00:00:51.910` | and i won't talk about all the assembly |
| `00:00:51.920 - 00:00:54.229` | code that's in kernelvec.s but i'll talk |
| `00:00:54.239 - 00:00:57.990` | about this this part of it |
| `00:00:58.000 - 00:01:01.510` | let's begin with uh start dot c |
| `00:01:01.520 - 00:01:04.789` | and i want to focus on this uh |
| `00:01:04.799 - 00:01:06.070` | definition |
| `00:01:06.080 - 00:01:07.990` | stack 0 |
| `00:01:08.000 - 00:01:11.190` | is a stack of bytes and |
| `00:01:11.200 - 00:01:12.870` | remember we're running on a multi-core |
| `00:01:12.880 - 00:01:16.230` | system in fact the constant in cpu is |
| `00:01:16.240 - 00:01:17.749` | set to eight so we can handle up to |
| `00:01:17.759 - 00:01:19.590` | eight cores |
| `00:01:19.600 - 00:01:22.950` | each core will need its own stack |
| `00:01:22.960 - 00:01:25.670` | we will allocate a 4k page for each |
| `00:01:25.680 - 00:01:28.070` | stack and that's what's going on here so |
| `00:01:28.080 - 00:01:29.670` | we're executing |
| `00:01:29.680 - 00:01:31.749` | the number of cores and one page for |
| `00:01:31.759 - 00:01:34.149` | each |
| `00:01:34.159 - 00:01:37.590` | this bit of c magic here basically says |
| `00:01:37.600 - 00:01:40.710` | be sure and align this array on a 16 |
| `00:01:40.720 - 00:01:41.749` | byte |
| `00:01:41.759 - 00:01:43.030` | boundary |
| `00:01:43.040 - 00:01:45.030` | okay it's just normally a byte array so |
| `00:01:45.040 - 00:01:47.190` | it might not be aligned if we don't put |
| `00:01:47.200 - 00:01:49.109` | this in here that's what we're |
| `00:01:49.119 - 00:01:50.710` | doing i'm going to come back and talk |
| `00:01:50.720 - 00:01:52.550` | about timer scratch |
| `00:01:52.560 - 00:01:54.789` | in a second but let's first uh take a |
| `00:01:54.799 - 00:01:55.830` | look at |
| `00:01:55.840 - 00:01:56.630` | the |
| `00:01:56.640 - 00:01:59.109` | entry dot c file |
| `00:01:59.119 - 00:02:01.830` | here is the entire file it's not too |
| `00:02:01.840 - 00:02:02.870` | long |
| `00:02:02.880 - 00:02:06.789` | and let's take a quick look at this |
| `00:02:06.799 - 00:02:08.949` | this section command |
| `00:02:08.959 - 00:02:10.790` | indicates that this all of this code |
| `00:02:10.800 - 00:02:13.030` | will go into a so-called section |
| `00:02:13.040 - 00:02:14.949` | sometimes i use the word segment |
| `00:02:14.959 - 00:02:18.150` | that is a text segment that is its |
| `00:02:18.160 - 00:02:20.630` | executable code and we see some assembly |
| `00:02:20.640 - 00:02:22.470` | instructions here |
| `00:02:22.480 - 00:02:25.670` | we are defining a label underscore entry |
| `00:02:25.680 - 00:02:27.030` | and we're saying |
| `00:02:27.040 - 00:02:29.589` | to the assembler be sure and make this a |
| `00:02:29.599 - 00:02:32.150` | publicly known global symbol |
| `00:02:32.160 - 00:02:34.869` | okay so that it can be |
| `00:02:34.879 - 00:02:37.830` | seen outside of just the assembly uh |
| `00:02:37.840 - 00:02:39.830` | there's another label down here which is |
| `00:02:39.840 - 00:02:41.110` | local so |
| `00:02:41.120 - 00:02:42.710` | that's what we're doing with the dot |
| `00:02:42.720 - 00:02:43.750` | global |
| `00:02:43.760 - 00:02:45.110` | command |
| `00:02:45.120 - 00:02:47.430` | okay so what do we do |
| `00:02:47.440 - 00:02:48.949` | load address |
| `00:02:48.959 - 00:02:51.030` | okay we're putting into the stack |
| `00:02:51.040 - 00:02:52.710` | pointer register |
| `00:02:52.720 - 00:02:55.589` | the addre well the goal here is to |
| `00:02:55.599 - 00:02:57.830` | initialize the stack pointer register |
| `00:02:57.840 - 00:03:00.309` | and so uh here is the computation that |
| `00:03:00.319 - 00:03:02.790` | this code is doing uh the address of |
| `00:03:02.800 - 00:03:05.190` | stack 0 that is that array |
| `00:03:05.200 - 00:03:06.790` | and then we're taking our |
| `00:03:06.800 - 00:03:11.270` | core number it's called heart id in the |
| `00:03:11.280 - 00:03:13.270` | risk 5 world |
| `00:03:13.280 - 00:03:16.470` | we're multiplying it by |
| `00:03:16.480 - 00:03:17.589` | 1k |
| `00:03:17.599 - 00:03:20.070` | and so that's what's happening here we |
| `00:03:20.080 - 00:03:22.390` | load the address of the array |
| `00:03:22.400 - 00:03:24.149` | into sp |
| `00:03:24.159 - 00:03:25.910` | here we load four |
| `00:03:25.920 - 00:03:28.789` | kilobytes into register a0 load |
| `00:03:28.799 - 00:03:31.670` | immediate and here we are |
| `00:03:31.680 - 00:03:33.670` | reading from the control and status |
| `00:03:33.680 - 00:03:35.430` | register |
| `00:03:35.440 - 00:03:38.949` | called mhart id that is the |
| `00:03:38.959 - 00:03:40.390` | control register that contains the |
| `00:03:40.400 - 00:03:43.430` | number of the core 0 through 7. and then |
| `00:03:43.440 - 00:03:45.430` | we add 1 to that so now we have a number |
| `00:03:45.440 - 00:03:47.350` | between 1 and 8. |
| `00:03:47.360 - 00:03:49.110` | and then we multiply |
| `00:03:49.120 - 00:03:51.110` | that by the 4k |
| `00:03:51.120 - 00:03:53.670` | that we computed in a0 so we multiply by |
| `00:03:53.680 - 00:03:55.910` | 4 k and |
| `00:03:55.920 - 00:03:59.509` | then we add that a0 to the stack pointer |
| `00:03:59.519 - 00:04:01.110` | and that gives us |
| `00:04:01.120 - 00:04:04.229` | the point at the very top of the |
| `00:04:04.239 - 00:04:05.830` | page for |
| `00:04:05.840 - 00:04:08.229` | the stack for this core |
| `00:04:08.239 - 00:04:11.830` | okay so now we've got sp set up and |
| `00:04:11.840 - 00:04:14.309` | we're jumping to start |
| `00:04:14.319 - 00:04:16.710` | here we see a call |
| `00:04:16.720 - 00:04:20.390` | we do not expect start to return |
| `00:04:20.400 - 00:04:22.790` | but if for some reason there's a bug and |
| `00:04:22.800 - 00:04:23.830` | it should |
| `00:04:23.840 - 00:04:25.670` | then we would |
| `00:04:25.680 - 00:04:27.510` | resume execution here |
| `00:04:27.520 - 00:04:29.909` | this is a jump instruction and we just |
| `00:04:29.919 - 00:04:31.670` | go into an incident loop and essentially |
| `00:04:31.680 - 00:04:33.990` | lock up the core |
| `00:04:34.000 - 00:04:35.590` | okay next |
| `00:04:35.600 - 00:04:38.550` | let's look at start dot c |
| `00:04:38.560 - 00:04:42.310` | uh |
| `00:04:42.320 - 00:04:44.950` | it is right here |
| `00:04:44.960 - 00:04:46.710` | uh |
| `00:04:46.720 - 00:04:48.070` | we |
| `00:04:48.080 - 00:04:50.629` | see the entire function |
| `00:04:50.639 - 00:04:51.909` | right here |
| `00:04:51.919 - 00:04:53.510` | and it |
| `00:04:53.520 - 00:04:54.830` | uh |
| `00:04:54.840 - 00:04:58.150` | will take care of several |
| `00:04:58.160 - 00:05:00.550` | of the registers |
| `00:05:00.560 - 00:05:04.230` | and set things up for executing in |
| `00:05:04.240 - 00:05:07.189` | uh maine and it will end essentially |
| `00:05:07.199 - 00:05:09.670` | with a jump to maine |
| `00:05:09.680 - 00:05:12.629` | so this is executing in machine mode |
| `00:05:12.639 - 00:05:14.150` | these functions that start with r |
| `00:05:14.160 - 00:05:15.350` | underscore |
| `00:05:15.360 - 00:05:17.670` | read various registers |
| `00:05:17.680 - 00:05:19.830` | and the ones that start with w |
| `00:05:19.840 - 00:05:21.830` | underscore will write to the register so |
| `00:05:21.840 - 00:05:23.830` | here you can see where we're updating |
| `00:05:23.840 - 00:05:26.710` | the status register we read it into some |
| `00:05:26.720 - 00:05:27.909` | variable |
| `00:05:27.919 - 00:05:28.790` | we |
| `00:05:28.800 - 00:05:30.230` | zero out |
| `00:05:30.240 - 00:05:32.070` | the two bits that tell the previous |
| `00:05:32.080 - 00:05:33.830` | privilege code |
| `00:05:33.840 - 00:05:36.469` | for machine load okay so |
| `00:05:36.479 - 00:05:38.629` | and then we set those two bits with an |
| `00:05:38.639 - 00:05:39.510` | or |
| `00:05:39.520 - 00:05:43.350` | to say the previous mode was supervisor |
| `00:05:43.360 - 00:05:45.189` | well we weren't previously in any mode |
| `00:05:45.199 - 00:05:47.270` | we've just started but that will see |
| `00:05:47.280 - 00:05:49.590` | that that's what we need when we finally |
| `00:05:49.600 - 00:05:51.990` | execute the system return instruction |
| `00:05:52.000 - 00:05:54.629` | down here |
| `00:05:54.639 - 00:05:57.430` | we write to the |
| `00:05:57.440 - 00:05:59.749` | mepc register |
| `00:05:59.759 - 00:06:02.070` | the address of the main function |
| `00:06:02.080 - 00:06:03.189` | so |
| `00:06:03.199 - 00:06:05.510` | again that the mret instruction down |
| `00:06:05.520 - 00:06:08.469` | here will return us to |
| `00:06:08.479 - 00:06:10.550` | whatever was previously executing |
| `00:06:10.560 - 00:06:12.230` | nothing was previously executing but by |
| `00:06:12.240 - 00:06:14.550` | setting the status and the epc |
| `00:06:14.560 - 00:06:17.590` | we're faking it so we're going to be |
| `00:06:17.600 - 00:06:19.670` | if you will returning to the first |
| `00:06:19.680 - 00:06:23.110` | instruction the main function |
| `00:06:23.120 - 00:06:26.070` | the satp register |
| `00:06:26.080 - 00:06:27.670` | normally contains a pointer to the page |
| `00:06:27.680 - 00:06:30.390` | table we initialize it with zero there |
| `00:06:30.400 - 00:06:32.309` | is no paging going on in machine mode |
| `00:06:32.319 - 00:06:35.350` | but when we go into supervisor mode |
| `00:06:35.360 - 00:06:36.629` | the |
| `00:06:36.639 - 00:06:39.270` | value of satp will determine whether |
| `00:06:39.280 - 00:06:42.070` | paging is happening or not so we want to |
| `00:06:42.080 - 00:06:43.749` | zero that out to make sure that there is |
| `00:06:43.759 - 00:06:45.189` | no paging |
| `00:06:45.199 - 00:06:48.550` | when main starts |
| `00:06:48.560 - 00:06:51.589` | now we have these two uh delegation |
| `00:06:51.599 - 00:06:53.909` | registers one for exceptions |
| `00:06:53.919 - 00:06:56.150` | and one for interrupts and we're writing |
| `00:06:56.160 - 00:06:57.990` | something into these registers |
| `00:06:58.000 - 00:06:59.830` | we're writing all ones |
| `00:06:59.840 - 00:07:01.909` | and basically that means that all |
| `00:07:01.919 - 00:07:04.469` | interrupts and exceptions are |
| `00:07:04.479 - 00:07:07.350` | delegated to supervisor mode so when an |
| `00:07:07.360 - 00:07:09.909` | exception or when an interrupt occurs it |
| `00:07:09.919 - 00:07:11.830` | will not be handled in machine mode |
| `00:07:11.840 - 00:07:14.230` | instead it will cause an interrupt to |
| `00:07:14.240 - 00:07:16.790` | the supervisor mode uh code and it will |
| `00:07:16.800 - 00:07:18.629` | get handled there |
| `00:07:18.639 - 00:07:20.710` | timer interrupts cannot be delegated so |
| `00:07:20.720 - 00:07:22.150` | they're handled differently but all |
| `00:07:22.160 - 00:07:24.390` | other interrupts and we really expect |
| `00:07:24.400 - 00:07:26.950` | them only from uh the disk and the |
| `00:07:26.960 - 00:07:30.950` | serial io device and um so that's what's |
| `00:07:30.960 - 00:07:35.270` | going on there |
| `00:07:35.280 - 00:07:39.189` | finally uh this is the um |
| `00:07:39.199 - 00:07:42.390` | value for the interrupts enabled uh |
| `00:07:42.400 - 00:07:45.589` | i'm sorry the um |
| `00:07:45.599 - 00:07:48.150` | register called s-i-e which is whether |
| `00:07:48.160 - 00:07:50.550` | um interrupts are enabled |
| `00:07:50.560 - 00:07:52.950` | and uh we are reading the current value |
| `00:07:52.960 - 00:07:54.790` | and then setting some bits and then |
| `00:07:54.800 - 00:07:57.589` | writing it back and what we're saying is |
| `00:07:57.599 - 00:08:01.990` | that we're setting uh them for um |
| `00:08:02.000 - 00:08:03.670` | devices uh |
| `00:08:03.680 - 00:08:05.830` | are allowed to interrupt uh the timer is |
| `00:08:05.840 - 00:08:09.189` | allowed to interrupt and uh |
| `00:08:09.199 - 00:08:11.350` | i'm sorry the |
| `00:08:11.360 - 00:08:13.350` | although it doesn't happen and this is |
| `00:08:13.360 - 00:08:14.950` | where the software interrupts the |
| `00:08:14.960 - 00:08:16.710` | so-called software interrupt these are |
| `00:08:16.720 - 00:08:18.070` | all enabled |
| `00:08:18.080 - 00:08:19.189` | um |
| `00:08:19.199 - 00:08:21.990` | and so in particular we're allowing uh |
| `00:08:22.000 - 00:08:25.350` | though the delegation to go into |
| `00:08:25.360 - 00:08:28.150` | supervisor mode and we're telling the |
| `00:08:28.160 - 00:08:29.589` | supervisor |
| `00:08:29.599 - 00:08:32.149` | interrupts enabled register that we want |
| `00:08:32.159 - 00:08:33.190` | to |
| `00:08:33.200 - 00:08:35.829` | enable these sorts of |
| `00:08:35.839 - 00:08:38.310` | interrupts |
| `00:08:38.320 - 00:08:39.990` | next we have this physical memory |
| `00:08:40.000 - 00:08:41.029` | protection |
| `00:08:41.039 - 00:08:42.790` | and |
| `00:08:42.800 - 00:08:45.269` | we don't really use the |
| `00:08:45.279 - 00:08:47.590` | physical memory protection system |
| `00:08:47.600 - 00:08:49.910` | basically we want to give the supervisor |
| `00:08:49.920 - 00:08:52.389` | mode access to all of physical memory so |
| `00:08:52.399 - 00:08:55.509` | we write in these constants uh here to |
| `00:08:55.519 - 00:08:57.030` | do that and i'm not really going to go |
| `00:08:57.040 - 00:08:59.030` | into that but basically this more or |
| `00:08:59.040 - 00:09:01.670` | less disables the system |
| `00:09:01.680 - 00:09:04.389` | and then we call this timer timer init |
| `00:09:04.399 - 00:09:06.230` | function to uh |
| `00:09:06.240 - 00:09:07.350` | enable that |
| `00:09:07.360 - 00:09:08.870` | to set up things for the clock |
| `00:09:08.880 - 00:09:10.310` | interrupts |
| `00:09:10.320 - 00:09:12.630` | and uh |
| `00:09:12.640 - 00:09:13.910` | then we |
| `00:09:13.920 - 00:09:17.030` | read the m heart id register |
| `00:09:17.040 - 00:09:18.310` | and |
| `00:09:18.320 - 00:09:20.230` | that is the core number and we save it |
| `00:09:20.240 - 00:09:22.230` | in the tp register so we write it to the |
| `00:09:22.240 - 00:09:25.269` | tp register and that will allow |
| `00:09:25.279 - 00:09:27.990` | our my cpu function |
| `00:09:28.000 - 00:09:29.350` | to work |
| `00:09:29.360 - 00:09:32.550` | later on to determine what cpu any code |
| `00:09:32.560 - 00:09:33.910` | is running on |
| `00:09:33.920 - 00:09:36.070` | and then finally we have some assembly |
| `00:09:36.080 - 00:09:39.190` | code we have the mret instruction |
| `00:09:39.200 - 00:09:41.269` | so we've already said we want to return |
| `00:09:41.279 - 00:09:43.430` | to supervisor mode we want to return to |
| `00:09:43.440 - 00:09:44.949` | this pc |
| `00:09:44.959 - 00:09:45.829` | and |
| `00:09:45.839 - 00:09:46.550` | so |
| `00:09:46.560 - 00:09:48.710` | this mret instruction here |
| `00:09:48.720 - 00:09:50.230` | will |
| `00:09:50.240 - 00:09:51.910` | resume executing |
| `00:09:51.920 - 00:09:54.870` | uh at the main function the first |
| `00:09:54.880 - 00:09:57.110` | instruction the main function |
| `00:09:57.120 - 00:09:58.310` | and |
| `00:09:58.320 - 00:10:00.790` | it will be in supervisor mode |
| `00:10:00.800 - 00:10:02.949` | so that's how we get into main but |
| `00:10:02.959 - 00:10:05.190` | before we do that uh |
| `00:10:05.200 - 00:10:07.910` | let's look at this timer annette |
| `00:10:07.920 - 00:10:08.949` | function |
| `00:10:08.959 - 00:10:11.590` | and i have that right |
| `00:10:11.600 - 00:10:12.550` | here |
| `00:10:12.560 - 00:10:15.430` | so let's take a look at that that is uh |
| `00:10:15.440 - 00:10:17.829` | from start dot |
| `00:10:17.839 - 00:10:20.710` | and uh |
| `00:10:20.720 - 00:10:23.670` | it's also fairly short to hear it is in |
| `00:10:23.680 - 00:10:25.110` | its entirety |
| `00:10:25.120 - 00:10:27.190` | but let's go through this |
| `00:10:27.200 - 00:10:31.269` | um |
| `00:10:31.279 - 00:10:35.110` | so uh before i do that i want to talk |
| `00:10:35.120 - 00:10:36.389` | about |
| `00:10:36.399 - 00:10:37.590` | uh |
| `00:10:37.600 - 00:10:38.550` | the |
| `00:10:38.560 - 00:10:39.750` | variable |
| `00:10:39.760 - 00:10:40.870` | uh |
| `00:10:40.880 - 00:10:43.430` | that is |
| `00:10:43.440 - 00:10:45.030` | in start |
| `00:10:45.040 - 00:10:47.430` | dot c that we didn't look at called |
| `00:10:47.440 - 00:10:49.190` | timer scratch |
| `00:10:49.200 - 00:10:51.590` | so let me just point this out here timer |
| `00:10:51.600 - 00:10:53.670` | scratch |
| `00:10:53.680 - 00:10:55.110` | is |
| `00:10:55.120 - 00:10:56.630` | well it's a |
| `00:10:56.640 - 00:10:58.230` | two dimensional array |
| `00:10:58.240 - 00:11:00.230` | we've got uh well in our case we've got |
| `00:11:00.240 - 00:11:02.310` | eight cpus eight cores |
| `00:11:02.320 - 00:11:06.710` | and each one of those has uh five |
| `00:11:06.720 - 00:11:08.550` | 64-bit words |
| `00:11:08.560 - 00:11:11.030` | so this is going to create an array |
| `00:11:11.040 - 00:11:14.150` | of five words for each core so here i'm |
| `00:11:14.160 - 00:11:17.350` | showing this timer scratch array with |
| `00:11:17.360 - 00:11:18.630` | all of its |
| `00:11:18.640 - 00:11:20.710` | eight elements one for each core |
| `00:11:20.720 - 00:11:22.630` | and each element is going to look like |
| `00:11:22.640 - 00:11:23.750` | this |
| `00:11:23.760 - 00:11:25.190` | okay it's going to have |
| `00:11:25.200 - 00:11:27.190` | five double words i'm showing the actual |
| `00:11:27.200 - 00:11:29.990` | offset here this is index 0 and x1 and |
| `00:11:30.000 - 00:11:31.750` | x2 3 and 4 |
| `00:11:31.760 - 00:11:34.949` | but these are the byte offsets into |
| `00:11:34.959 - 00:11:35.910` | this |
| `00:11:35.920 - 00:11:38.949` | uh element of the array if you will |
| `00:11:38.959 - 00:11:40.310` | and so |
| `00:11:40.320 - 00:11:42.310` | we need that |
| `00:11:42.320 - 00:11:44.550` | so the first thing we do in timer in it |
| `00:11:44.560 - 00:11:46.630` | is figure out what core we're running on |
| `00:11:46.640 - 00:11:48.310` | so that just uh |
| `00:11:48.320 - 00:11:49.509` | reads the |
| `00:11:49.519 - 00:11:50.230` | uh |
| `00:11:50.240 - 00:11:52.230` | core number |
| `00:11:52.240 - 00:11:54.870` | and we're gonna be using that core |
| `00:11:54.880 - 00:11:58.069` | number here |
| `00:11:58.079 - 00:12:03.430` | remember that |
| `00:12:03.440 - 00:12:05.190` | this |
| `00:12:05.200 - 00:12:07.030` | core local interrupter |
| `00:12:07.040 - 00:12:10.629` | has a couple of registers on it and the |
| `00:12:10.639 - 00:12:12.389` | time compare register |
| `00:12:12.399 - 00:12:13.180` | is |
| `00:12:13.190 - 00:12:14.870` | [Music] |
| `00:12:14.880 - 00:12:17.590` | one of them so there are |
| `00:12:17.600 - 00:12:21.110` | there is one register per core so this |
| `00:12:21.120 - 00:12:22.949` | macro function here |
| `00:12:22.959 - 00:12:26.310` | will give us the address of the register |
| `00:12:26.320 - 00:12:29.269` | for this particular core |
| `00:12:29.279 - 00:12:31.750` | and what we're going to store into it |
| `00:12:31.760 - 00:12:34.150` | is the current time |
| `00:12:34.160 - 00:12:37.590` | okay remember this |
| `00:12:37.600 - 00:12:40.150` | is a preprocessor expression that gives |
| `00:12:40.160 - 00:12:42.710` | us the address of the m time register so |
| `00:12:42.720 - 00:12:45.269` | we get the current time okay we're |
| `00:12:45.279 - 00:12:46.790` | indirect here we're getting the current |
| `00:12:46.800 - 00:12:49.670` | time from this address and we're adding |
| `00:12:49.680 - 00:12:50.790` | the interval |
| `00:12:50.800 - 00:12:53.750` | our timer interrupts will occur every |
| `00:12:53.760 - 00:12:56.870` | well what is this one million cycles |
| `00:12:56.880 - 00:12:58.470` | okay every million cycles we're going to |
| `00:12:58.480 - 00:13:00.310` | have a timer interrupt and so this is |
| `00:13:00.320 - 00:13:03.030` | setting that register to this time plus |
| `00:13:03.040 - 00:13:05.190` | a million this is the current time plus |
| `00:13:05.200 - 00:13:06.629` | a million |
| `00:13:06.639 - 00:13:08.710` | okay now |
| `00:13:08.720 - 00:13:10.389` | prepare for uh |
| `00:13:10.399 - 00:13:12.629` | the |
| `00:13:12.639 - 00:13:15.910` | we need to set up some of the uh |
| `00:13:15.920 - 00:13:17.430` | when the timer interrupt occurs we're |
| `00:13:17.440 - 00:13:20.870` | going to be using this piece of memory |
| `00:13:20.880 - 00:13:23.030` | uh during the interrupt handler and we |
| `00:13:23.040 - 00:13:26.069` | need to store two things at this point |
| `00:13:26.079 - 00:13:27.670` | the address of |
| `00:13:27.680 - 00:13:31.829` | the m-time compare register for our core |
| `00:13:31.839 - 00:13:32.790` | and |
| `00:13:32.800 - 00:13:34.949` | we store the interval the interval is a |
| `00:13:34.959 - 00:13:38.069` | constant and you know i'm not 100 uh |
| `00:13:38.079 - 00:13:40.069` | sure why we really need to store that |
| `00:13:40.079 - 00:13:41.590` | constant there |
| `00:13:41.600 - 00:13:44.790` | but uh perhaps uh in some future version |
| `00:13:44.800 - 00:13:45.829` | we could have |
| `00:13:45.839 - 00:13:47.509` | different cores running at different |
| `00:13:47.519 - 00:13:48.550` | intervals |
| `00:13:48.560 - 00:13:50.949` | but that's the uh thing we're storing |
| `00:13:50.959 - 00:13:52.150` | there so |
| `00:13:52.160 - 00:13:54.069` | what we're doing here is we're getting a |
| `00:13:54.079 - 00:13:56.310` | pointer to |
| `00:13:56.320 - 00:13:59.590` | uh what well the timer scratch |
| `00:13:59.600 - 00:14:00.389` | uh |
| `00:14:00.399 - 00:14:03.430` | array that's this array here |
| `00:14:03.440 - 00:14:04.550` | okay |
| `00:14:04.560 - 00:14:07.990` | the element for this particular core |
| `00:14:08.000 - 00:14:12.069` | and uh we get the uh address for the |
| `00:14:12.079 - 00:14:14.710` | zero byte uh the first the first part of |
| `00:14:14.720 - 00:14:17.030` | that so we're getting an address a |
| `00:14:17.040 - 00:14:19.110` | pointer to the address for |
| `00:14:19.120 - 00:14:20.710` | these five words |
| `00:14:20.720 - 00:14:22.870` | for the core that we're running on |
| `00:14:22.880 - 00:14:26.230` | okay and then we're setting that |
| `00:14:26.240 - 00:14:27.189` | to |
| `00:14:27.199 - 00:14:29.750` | restoring that address in scratch |
| `00:14:29.760 - 00:14:31.430` | and then we're setting |
| `00:14:31.440 - 00:14:32.310` | the |
| `00:14:32.320 - 00:14:33.670` | third and fourth or |
| `00:14:33.680 - 00:14:35.990` | index three and four elements okay |
| `00:14:36.000 - 00:14:38.710` | zero one two we're setting three and |
| `00:14:38.720 - 00:14:41.350` | four we're setting three to |
| `00:14:41.360 - 00:14:42.790` | the um |
| `00:14:42.800 - 00:14:44.629` | address of our |
| `00:14:44.639 - 00:14:45.829` | time |
| `00:14:45.839 - 00:14:49.110` | in time compare register so again this |
| `00:14:49.120 - 00:14:51.509` | this preprocessor function here this |
| `00:14:51.519 - 00:14:52.870` | macro function |
| `00:14:52.880 - 00:14:54.629` | given a core number uh computes the |
| `00:14:54.639 - 00:14:57.990` | address of the register for this core |
| `00:14:58.000 - 00:14:59.110` | and |
| `00:14:59.120 - 00:15:02.389` | we need to keep updating it so when we |
| `00:15:02.399 - 00:15:04.389` | initialize it here that means we're |
| `00:15:04.399 - 00:15:06.550` | going to get an interrupt in 1 million |
| `00:15:06.560 - 00:15:08.710` | cycles but after that we're going to |
| `00:15:08.720 - 00:15:09.750` | need to |
| `00:15:09.760 - 00:15:11.430` | add a million to it for the next |
| `00:15:11.440 - 00:15:14.470` | interrupt and store the updated value |
| `00:15:14.480 - 00:15:15.670` | so |
| `00:15:15.680 - 00:15:17.030` | we're storing the address of the |
| `00:15:17.040 - 00:15:18.710` | register here so we don't have to |
| `00:15:18.720 - 00:15:20.710` | recompute it if you will each time we |
| `00:15:20.720 - 00:15:22.389` | get the interrupt |
| `00:15:22.399 - 00:15:24.790` | and we're saving the interval which is a |
| `00:15:24.800 - 00:15:26.710` | million cycles |
| `00:15:26.720 - 00:15:29.590` | okay and finally we're writing into |
| `00:15:29.600 - 00:15:31.910` | the in scratch register |
| `00:15:31.920 - 00:15:34.230` | there is uh an in-scratch register for |
| `00:15:34.240 - 00:15:35.910` | each core it's one of the control and |
| `00:15:35.920 - 00:15:38.550` | status registers and so |
| `00:15:38.560 - 00:15:40.550` | this core the core we're running on will |
| `00:15:40.560 - 00:15:43.590` | contain a pointer to |
| `00:15:43.600 - 00:15:45.189` | the block |
| `00:15:45.199 - 00:15:45.990` | for |
| `00:15:46.000 - 00:15:47.110` | that |
| `00:15:47.120 - 00:15:49.749` | core so that's what i'm indicating here |
| `00:15:49.759 - 00:15:51.829` | that we're saving in this in scratch |
| `00:15:51.839 - 00:15:53.269` | register |
| `00:15:53.279 - 00:15:55.189` | the same scratch register will only be |
| `00:15:55.199 - 00:15:57.350` | accessed in the interrupt handler for |
| `00:15:57.360 - 00:15:59.110` | the timer interrupts |
| `00:15:59.120 - 00:16:01.670` | uh while we're executing in supervisor |
| `00:16:01.680 - 00:16:03.670` | mode in other words throughout the |
| `00:16:03.680 - 00:16:05.829` | entire rest of the kernel |
| `00:16:05.839 - 00:16:08.389` | this register will be inaccessible |
| `00:16:08.399 - 00:16:10.150` | only when we're running in machine mode |
| `00:16:10.160 - 00:16:12.230` | will this register be accessible and the |
| `00:16:12.240 - 00:16:14.069` | only time we're in machine mode is |
| `00:16:14.079 - 00:16:17.030` | during this startup and the executing of |
| `00:16:17.040 - 00:16:19.030` | the execution of the timer in init |
| `00:16:19.040 - 00:16:20.389` | function |
| `00:16:20.399 - 00:16:21.350` | and |
| `00:16:21.360 - 00:16:23.509` | then we will be in machine mode when the |
| `00:16:23.519 - 00:16:26.470` | actual timer interrupt occurs |
| `00:16:26.480 - 00:16:28.389` | so that's why what we're storing an in |
| `00:16:28.399 - 00:16:30.150` | scratch |
| `00:16:30.160 - 00:16:32.310` | mt back |
| `00:16:32.320 - 00:16:34.710` | this is a well this is another register |
| `00:16:34.720 - 00:16:36.550` | and we're writing into this |
| `00:16:36.560 - 00:16:37.670` | register |
| `00:16:37.680 - 00:16:39.990` | the trap vector address that is the |
| `00:16:40.000 - 00:16:41.829` | address of the code that should be |
| `00:16:41.839 - 00:16:42.949` | executed |
| `00:16:42.959 - 00:16:43.829` | when |
| `00:16:43.839 - 00:16:45.509` | a trap occurs |
| `00:16:45.519 - 00:16:47.749` | when that trap is handled by machine |
| `00:16:47.759 - 00:16:50.470` | mode code |
| `00:16:50.480 - 00:16:54.389` | the core will handle it by jumping to |
| `00:16:54.399 - 00:16:56.230` | whatever address we give it so here |
| `00:16:56.240 - 00:16:58.949` | we're storing in the mtvec register the |
| `00:16:58.959 - 00:16:59.990` | address |
| `00:17:00.000 - 00:17:02.150` | of the timervec function which i'm going |
| `00:17:02.160 - 00:17:04.069` | to show you that code in just one second |
| `00:17:04.079 - 00:17:05.029` | here |
| `00:17:05.039 - 00:17:06.949` | and so now when we get the interrupt we |
| `00:17:06.959 - 00:17:10.390` | will jump to timer back |
| `00:17:10.400 - 00:17:11.510` | then |
| `00:17:11.520 - 00:17:13.110` | what do we do we |
| `00:17:13.120 - 00:17:14.870` | read the status register this is the |
| `00:17:14.880 - 00:17:17.110` | machine mode status register and we |
| `00:17:17.120 - 00:17:18.870` | update it and write it back |
| `00:17:18.880 - 00:17:20.309` | and what do we do |
| `00:17:20.319 - 00:17:24.630` | we set the interrupts enabled bit so now |
| `00:17:24.640 - 00:17:26.949` | machine mode interrupts can occur so now |
| `00:17:26.959 - 00:17:28.710` | at this point the timer interrupts can |
| `00:17:28.720 - 00:17:30.150` | occur |
| `00:17:30.160 - 00:17:32.470` | and uh |
| `00:17:32.480 - 00:17:34.549` | we also have a |
| `00:17:34.559 - 00:17:36.630` | uh more |
| `00:17:36.640 - 00:17:39.029` | control over individual timer interrupts |
| `00:17:39.039 - 00:17:40.950` | so we are |
| `00:17:40.960 - 00:17:43.190` | reading the mie register that's for |
| `00:17:43.200 - 00:17:45.029` | interrupt enable this is an entire |
| `00:17:45.039 - 00:17:47.270` | register and the bits in it tell us more |
| `00:17:47.280 - 00:17:49.909` | specifically what interrupts are enabled |
| `00:17:49.919 - 00:17:52.710` | and so we set the one for the timer |
| `00:17:52.720 - 00:17:54.070` | interrupts |
| `00:17:54.080 - 00:17:55.590` | to enabled |
| `00:17:55.600 - 00:17:58.470` | and then we're done initializing and of |
| `00:17:58.480 - 00:18:00.870` | course we were in the the |
| `00:18:00.880 - 00:18:04.230` | in the start function so uh after we uh |
| `00:18:04.240 - 00:18:06.390` | do the timer interrupt timer and hit |
| `00:18:06.400 - 00:18:08.230` | call we uh |
| `00:18:08.240 - 00:18:11.270` | write our tp and then we uh |
| `00:18:11.280 - 00:18:12.710` | jump to |
| `00:18:12.720 - 00:18:16.870` | the main function in supervisor mode |
| `00:18:16.880 - 00:18:18.950` | now so then the kernel executes along in |
| `00:18:18.960 - 00:18:21.590` | supervisor mode and everything's fine |
| `00:18:21.600 - 00:18:23.990` | until the timer interrupt occurs |
| `00:18:24.000 - 00:18:26.310` | and the timer interrupt will throw us |
| `00:18:26.320 - 00:18:29.110` | into machine mode and will be handled by |
| `00:18:29.120 - 00:18:30.710` | the machine mode |
| `00:18:30.720 - 00:18:33.430` | code that's located at timervec |
| `00:18:33.440 - 00:18:36.070` | and so we're going to look at that next |
| `00:18:36.080 - 00:18:38.549` | what that's going to do is hand off the |
| `00:18:38.559 - 00:18:41.669` | interrupt to a supervisor mode |
| `00:18:41.679 - 00:18:43.830` | interrupt controller so it's basically |
| `00:18:43.840 - 00:18:45.029` | going to |
| `00:18:45.039 - 00:18:46.789` | this code is going to be executed when |
| `00:18:46.799 - 00:18:49.190` | the timer interrupt occurs and what it's |
| `00:18:49.200 - 00:18:51.830` | going to do is it's going to cause |
| `00:18:51.840 - 00:18:53.190` | a |
| `00:18:53.200 - 00:18:54.870` | software interrupt at the supervisor |
| `00:18:54.880 - 00:18:56.710` | level so that's what's going on down |
| `00:18:56.720 - 00:18:57.510` | here |
| `00:18:57.520 - 00:19:00.310` | so let's uh take a look at this |
| `00:19:00.320 - 00:19:01.350` | um |
| `00:19:01.360 - 00:19:04.150` | this uh timer vect is the starting |
| `00:19:04.160 - 00:19:06.150` | address of the first instruction |
| `00:19:06.160 - 00:19:07.510` | and |
| `00:19:07.520 - 00:19:09.510` | all instructions need to be four byte |
| `00:19:09.520 - 00:19:11.740` | aligned at the risk five |
| `00:19:11.750 - 00:19:13.430` | [Music] |
| `00:19:13.440 - 00:19:15.029` | we're not talking about compressed |
| `00:19:15.039 - 00:19:16.390` | instructions here |
| `00:19:16.400 - 00:19:18.870` | uh that's for another video another time |
| `00:19:18.880 - 00:19:20.630` | uh the timer vec |
| `00:19:20.640 - 00:19:21.510` | uh |
| `00:19:21.520 - 00:19:23.750` | symbol needs to be exported so it's |
| `00:19:23.760 - 00:19:26.230` | visible outside of this particular file |
| `00:19:26.240 - 00:19:28.710` | and so that the linker can |
| `00:19:28.720 - 00:19:29.830` | find it |
| `00:19:29.840 - 00:19:33.029` | and so that when we are actually |
| `00:19:33.039 - 00:19:36.230` | compiling and linking the code the um |
| `00:19:36.240 - 00:19:38.710` | where is it here the um |
| `00:19:38.720 - 00:19:43.510` | the use of this uh timer that symbol |
| `00:19:43.520 - 00:19:46.230` | in the timer init function uh the linker |
| `00:19:46.240 - 00:19:47.029` | will |
| `00:19:47.039 - 00:19:48.870` | plug in the right address okay so that's |
| `00:19:48.880 - 00:19:50.549` | why we export |
| `00:19:50.559 - 00:19:53.430` | and make this a global uh symbol |
| `00:19:53.440 - 00:19:56.230` | okay so what do we do here |
| `00:19:56.240 - 00:19:58.150` | well now let's come back to |
| `00:19:58.160 - 00:19:59.110` | this |
| `00:19:59.120 - 00:20:00.789` | keep in mind we've just had an interrupt |
| `00:20:00.799 - 00:20:02.470` | who knows what was going on we could |
| `00:20:02.480 - 00:20:03.990` | have been in user mode we could have |
| `00:20:04.000 - 00:20:05.909` | executed user code we could have been |
| `00:20:05.919 - 00:20:08.789` | anywhere in the kernel doing anything so |
| `00:20:08.799 - 00:20:10.870` | the point is the registers that are in |
| `00:20:10.880 - 00:20:11.830` | use |
| `00:20:11.840 - 00:20:14.470` | must not be disturbed this function will |
| `00:20:14.480 - 00:20:16.950` | use registers one two and three |
| `00:20:16.960 - 00:20:19.029` | and zero i guess |
| `00:20:19.039 - 00:20:21.029` | and those are the only registers that |
| `00:20:21.039 - 00:20:23.510` | will be updated and they will be |
| `00:20:23.520 - 00:20:25.510` | restored so here what we're doing is |
| `00:20:25.520 - 00:20:27.990` | we're saving the registers |
| `00:20:28.000 - 00:20:29.909` | at the time of the interrupt in scratch |
| `00:20:29.919 - 00:20:31.029` | points here |
| `00:20:31.039 - 00:20:32.950` | so the first thing we do is this read |
| `00:20:32.960 - 00:20:35.270` | write which is a swap |
| `00:20:35.280 - 00:20:36.549` | okay so what it's going to do is it's |
| `00:20:36.559 - 00:20:38.950` | going to exchange the value of a0 and in |
| `00:20:38.960 - 00:20:39.909` | scratch |
| `00:20:39.919 - 00:20:41.270` | in other words |
| `00:20:41.280 - 00:20:43.270` | a0 will be written in scratch and in |
| `00:20:43.280 - 00:20:45.990` | scratch will be read into a0 |
| `00:20:46.000 - 00:20:46.789` | okay |
| `00:20:46.799 - 00:20:49.830` | and then uh down here we're gonna undo |
| `00:20:49.840 - 00:20:50.710` | that |
| `00:20:50.720 - 00:20:54.070` | so for the duration of this function a0 |
| `00:20:54.080 - 00:20:55.750` | will point to |
| `00:20:55.760 - 00:20:56.789` | uh |
| `00:20:56.799 - 00:20:57.909` | this |
| `00:20:57.919 - 00:21:01.990` | but then at the end we'll restore a0 |
| `00:21:02.000 - 00:21:03.990` | for the duration of this program we are |
| `00:21:04.000 - 00:21:07.190` | saving a zero in the m scratch register |
| `00:21:07.200 - 00:21:09.430` | and we're using a0 during this function |
| `00:21:09.440 - 00:21:12.470` | to point to this five word |
| `00:21:12.480 - 00:21:13.750` | area |
| `00:21:13.760 - 00:21:15.510` | the next thing we do is we save a one |
| `00:21:15.520 - 00:21:16.630` | two and three because we're going to be |
| `00:21:16.640 - 00:21:17.830` | using them here |
| `00:21:17.840 - 00:21:21.510` | so we save them in offset 0 8 and 16. so |
| `00:21:21.520 - 00:21:23.270` | here we have a store |
| `00:21:23.280 - 00:21:24.710` | double word |
| `00:21:24.720 - 00:21:30.070` | of a1 into offset 0 from a0 a0 points at |
| `00:21:30.080 - 00:21:31.590` | that at this point |
| `00:21:31.600 - 00:21:33.990` | it points to the beginning of this 0 |
| `00:21:34.000 - 00:21:36.630` | bytes offset we store a1 then we store |
| `00:21:36.640 - 00:21:38.470` | a2 and a3 |
| `00:21:38.480 - 00:21:40.549` | so we store our |
| `00:21:40.559 - 00:21:42.710` | registers here and then right before we |
| `00:21:42.720 - 00:21:45.110` | return we restore those registers from |
| `00:21:45.120 - 00:21:46.710` | the same location so this is a load |
| `00:21:46.720 - 00:21:47.909` | double word |
| `00:21:47.919 - 00:21:52.310` | so we're loading from this uh |
| `00:21:52.320 - 00:21:53.510` | location |
| `00:21:53.520 - 00:21:54.870` | into a3 |
| `00:21:54.880 - 00:21:56.710` | and from this location into a2 and from |
| `00:21:56.720 - 00:21:58.950` | this location to a1 and then we're ready |
| `00:21:58.960 - 00:21:59.669` | to |
| `00:21:59.679 - 00:22:02.630` | execute the mret which will go back to |
| `00:22:02.640 - 00:22:04.630` | executing |
| `00:22:04.640 - 00:22:06.470` | when the interrupt occurs our program |
| `00:22:06.480 - 00:22:08.710` | counter will be saved automatically in |
| `00:22:08.720 - 00:22:10.830` | the mepc |
| `00:22:10.840 - 00:22:13.830` | register and the previous status and |
| `00:22:13.840 - 00:22:14.870` | interrupt |
| `00:22:14.880 - 00:22:16.870` | status will be saved in in the status |
| `00:22:16.880 - 00:22:19.590` | register and then as a result of the |
| `00:22:19.600 - 00:22:20.710` | mret |
| `00:22:20.720 - 00:22:22.710` | pc will be reloaded with whatever was |
| `00:22:22.720 - 00:22:25.590` | saved so we see some stuff going on with |
| `00:22:25.600 - 00:22:28.070` | the trap and the emirate that's not |
| `00:22:28.080 - 00:22:30.230` | really visible here but that will be |
| `00:22:30.240 - 00:22:31.430` | happening |
| `00:22:31.440 - 00:22:33.750` | now here's the meat of it here |
| `00:22:33.760 - 00:22:35.990` | schedule the next timer interrupt |
| `00:22:36.000 - 00:22:37.510` | and by adding |
| `00:22:37.520 - 00:22:40.549` | this interval that's 1 million to the |
| `00:22:40.559 - 00:22:43.830` | hardware register called m time compare |
| `00:22:43.840 - 00:22:46.950` | so fortunately we've saved the address |
| `00:22:46.960 - 00:22:50.390` | of our m-time compare register that is |
| `00:22:50.400 - 00:22:52.149` | the register for this core |
| `00:22:52.159 - 00:22:55.270` | at location 24. and the interval at 32. |
| `00:22:55.280 - 00:22:57.190` | so that's what we're doing here |
| `00:22:57.200 - 00:23:00.149` | we're loading from |
| `00:23:00.159 - 00:23:02.470` | that |
| `00:23:02.480 - 00:23:05.029` | from this place |
| `00:23:05.039 - 00:23:07.110` | into uh register |
| `00:23:07.120 - 00:23:10.470` | a1 so now a1 points to the hardware |
| `00:23:10.480 - 00:23:11.430` | register |
| `00:23:11.440 - 00:23:14.549` | we load the value which is 1 million |
| `00:23:14.559 - 00:23:17.029` | into a2 |
| `00:23:17.039 - 00:23:19.590` | and then |
| `00:23:19.600 - 00:23:21.990` | what we're doing is we are loading the |
| `00:23:22.000 - 00:23:24.230` | current time the uh |
| `00:23:24.240 - 00:23:27.430` | the current value of m time compare |
| `00:23:27.440 - 00:23:29.590` | right that's in a1 |
| `00:23:29.600 - 00:23:30.470` | okay |
| `00:23:30.480 - 00:23:33.990` | with zero offset we're loading the that |
| `00:23:34.000 - 00:23:35.669` | into a3 |
| `00:23:35.679 - 00:23:37.430` | and then we're adding |
| `00:23:37.440 - 00:23:40.310` | a2 a2 contains 1 million |
| `00:23:40.320 - 00:23:43.750` | so this is where we add 1 million to |
| `00:23:43.760 - 00:23:46.149` | the previous value of the end time |
| `00:23:46.159 - 00:23:47.830` | compare register |
| `00:23:47.840 - 00:23:50.149` | and finally we need to store it back so |
| `00:23:50.159 - 00:23:51.029` | here |
| `00:23:51.039 - 00:23:53.110` | uh we loaded it |
| `00:23:53.120 - 00:23:54.630` | we added to it |
| `00:23:54.640 - 00:23:56.549` | and then we stored it back |
| `00:23:56.559 - 00:23:58.950` | okay so now that will essentially |
| `00:23:58.960 - 00:24:01.669` | schedule uh another interrupt to occur |
| `00:24:01.679 - 00:24:04.789` | in 1 million cycles |
| `00:24:04.799 - 00:24:06.710` | one million cycles minus however many |
| `00:24:06.720 - 00:24:08.070` | cycles have been |
| `00:24:08.080 - 00:24:10.470` | executed to this point of course |
| `00:24:10.480 - 00:24:14.149` | um to this point where we load |
| `00:24:14.159 - 00:24:15.590` | uh |
| `00:24:15.600 - 00:24:17.190` | actually i |
| `00:24:17.200 - 00:24:18.950` | sorry i misspoke we're just adding a |
| `00:24:18.960 - 00:24:21.750` | million to the time compare so uh |
| `00:24:21.760 - 00:24:24.070` | uh not less anything it's just added |
| `00:24:24.080 - 00:24:26.070` | adding a million to it so we get a timer |
| `00:24:26.080 - 00:24:28.470` | interrupt every million cycles |
| `00:24:28.480 - 00:24:31.590` | um and finally we have to |
| `00:24:31.600 - 00:24:34.230` | raise a supervisor software interrupt |
| `00:24:34.240 - 00:24:36.870` | this will interrupt the kernel |
| `00:24:36.880 - 00:24:38.630` | it will only interrupt the kernel if the |
| `00:24:38.640 - 00:24:41.029` | kernel is ready so it depends on whether |
| `00:24:41.039 - 00:24:43.110` | interrupts are enabled in supervisor |
| `00:24:43.120 - 00:24:46.549` | mode but in any case we we raise the |
| `00:24:46.559 - 00:24:49.909` | interrupt to be handled either |
| `00:24:49.919 - 00:24:51.350` | you know |
| `00:24:51.360 - 00:24:53.750` | whenever we go back to supervisor uh |
| `00:24:53.760 - 00:24:55.029` | mode |
| `00:24:55.039 - 00:24:57.510` | uh or whenever the supervisor mode code |
| `00:24:57.520 - 00:25:01.430` | uh allows it but we do that by uh |
| `00:25:01.440 - 00:25:04.630` | loading this value into a1 |
| `00:25:04.640 - 00:25:07.110` | and then writing it into the uh |
| `00:25:07.120 - 00:25:09.510` | interrupts pending register for |
| `00:25:09.520 - 00:25:12.830` | supervisor mode so here is the |
| `00:25:12.840 - 00:25:15.830` | particular uh bit position |
| `00:25:15.840 - 00:25:19.750` | that is corresponds to |
| `00:25:19.760 - 00:25:23.510` | supervisor software interrupts and we're |
| `00:25:23.520 - 00:25:26.390` | storing that with a right to the control |
| `00:25:26.400 - 00:25:28.789` | and status register called |
| `00:25:28.799 - 00:25:31.430` | sip supervisor interrupt pending that |
| `00:25:31.440 - 00:25:33.590` | makes the interrupt pending |
| `00:25:33.600 - 00:25:36.149` | we're in machine mode so it won't happen |
| `00:25:36.159 - 00:25:37.269` | immediately |
| `00:25:37.279 - 00:25:40.149` | uh then when we return to uh |
| `00:25:40.159 - 00:25:42.870` | supervisor mode code it depends on |
| `00:25:42.880 - 00:25:44.470` | whether the |
| `00:25:44.480 - 00:25:46.710` | interrupts are enabled in supervisor |
| `00:25:46.720 - 00:25:48.149` | mode if the interrupts are currently |
| `00:25:48.159 - 00:25:50.549` | enabled in supervisor mode then the |
| `00:25:50.559 - 00:25:52.549` | interrupt would happen immediately but |
| `00:25:52.559 - 00:25:55.430` | if the kernel is doing something uh that |
| `00:25:55.440 - 00:25:57.909` | can't be interrupted interrupted and it |
| `00:25:57.919 - 00:26:01.350` | has temporarily disabled interrupts then |
| `00:26:01.360 - 00:26:03.269` | that interrupt the software the |
| `00:26:03.279 - 00:26:05.110` | supervisor software interrupt will |
| `00:26:05.120 - 00:26:07.750` | remain pending but at some future point |
| `00:26:07.760 - 00:26:09.350` | the colonel |
| `00:26:09.360 - 00:26:12.630` | will enable interrupts and |
| `00:26:12.640 - 00:26:15.190` | this software interrupt will occur |
| `00:26:15.200 - 00:26:16.070` | the |
| `00:26:16.080 - 00:26:17.510` | kernel will then |
| `00:26:17.520 - 00:26:19.909` | have a trap in supervisor mode and it |
| `00:26:19.919 - 00:26:22.950` | will interpret the software interrupt |
| `00:26:22.960 - 00:26:24.789` | as uh |
| `00:26:24.799 - 00:26:26.310` | being a timer interrupt because that's |
| `00:26:26.320 - 00:26:28.390` | the only thing that could be causing it |
| `00:26:28.400 - 00:26:29.350` | and |
| `00:26:29.360 - 00:26:30.230` | we'll |
| `00:26:30.240 - 00:26:31.830` | basically switch out |
| `00:26:31.840 - 00:26:35.190` | the current user mode thread we'll do a |
| `00:26:35.200 - 00:26:37.029` | context switch |
| `00:26:37.039 - 00:26:38.710` | it will you know do the processing it |
| `00:26:38.720 - 00:26:40.710` | would do at the end of a time slice |
| `00:26:40.720 - 00:26:42.549` | which we'll talk about in some future |
| `00:26:42.559 - 00:26:43.510` | video |
| `00:26:43.520 - 00:26:45.029` | okay thanks for watching and i'll see |
| `00:26:45.039 - 00:26:48.520` | you in the next video |
