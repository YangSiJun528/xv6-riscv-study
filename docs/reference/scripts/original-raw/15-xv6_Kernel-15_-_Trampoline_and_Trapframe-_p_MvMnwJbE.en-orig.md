# xv6 Kernel-15: Trampoline and Trapframe

| Field | Value |
| --- | --- |
| Video ID | `_p_MvMnwJbE` |
| URL | <https://www.youtube.com/watch?v=_p_MvMnwJbE> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.560 - 00:00:01.990` | this video is part of a series on the |
| `00:00:02.000 - 00:00:04.390` | xv6 operating system |
| `00:00:04.400 - 00:00:05.910` | in this video i'm going to go over the |
| `00:00:05.920 - 00:00:09.110` | assembly code in the trampoline.s file |
| `00:00:09.120 - 00:00:12.150` | this includes the code called userback |
| `00:00:12.160 - 00:00:15.110` | and the code called user return |
| `00:00:15.120 - 00:00:16.790` | i'll also go over some of the code in |
| `00:00:16.800 - 00:00:19.269` | the trap.c file |
| `00:00:19.279 - 00:00:21.990` | i will talk about the usertrap function |
| `00:00:22.000 - 00:00:25.990` | and the usertrap return function |
| `00:00:26.000 - 00:00:28.070` | let's begin with what i call |
| `00:00:28.080 - 00:00:29.189` | the |
| `00:00:29.199 - 00:00:30.310` | map |
| `00:00:30.320 - 00:00:32.549` | um |
| `00:00:32.559 - 00:00:34.870` | user code is executing |
| `00:00:34.880 - 00:00:36.950` | and we get a trap |
| `00:00:36.960 - 00:00:38.709` | and we execute |
| `00:00:38.719 - 00:00:42.389` | the uservec assembly code first |
| `00:00:42.399 - 00:00:44.630` | and then we go into the user trap |
| `00:00:44.640 - 00:00:45.830` | function |
| `00:00:45.840 - 00:00:48.389` | and then we deal with the trap whatever |
| `00:00:48.399 - 00:00:50.310` | kind of trap it was |
| `00:00:50.320 - 00:00:51.830` | and then |
| `00:00:51.840 - 00:00:55.910` | we call the user trap return function |
| `00:00:55.920 - 00:00:57.510` | and that then goes into the user rep |
| `00:00:57.520 - 00:00:59.189` | assembly code function |
| `00:00:59.199 - 00:01:02.069` | which ends by executing the system |
| `00:01:02.079 - 00:01:09.190` | return or sret instruction |
| `00:01:09.200 - 00:01:11.190` | when the trap occurs |
| `00:01:11.200 - 00:01:13.270` | interrupts are disabled by the hardware |
| `00:01:13.280 - 00:01:15.510` | and we change into supervisor mode and |
| `00:01:15.520 - 00:01:16.310` | the |
| `00:01:16.320 - 00:01:18.950` | current program counter is saved in the |
| `00:01:18.960 - 00:01:22.870` | scpc register |
| `00:01:22.880 - 00:01:24.469` | the st bec |
| `00:01:24.479 - 00:01:25.910` | register is assumed to contain the |
| `00:01:25.920 - 00:01:28.630` | address of the trap handler |
| `00:01:28.640 - 00:01:31.109` | and so the hardware will also |
| `00:01:31.119 - 00:01:33.670` | load that into the program counter |
| `00:01:33.680 - 00:01:34.789` | that |
| `00:01:34.799 - 00:01:37.910` | basically causes a jump to the uservec |
| `00:01:37.920 - 00:01:40.149` | code because that is the address that |
| `00:01:40.159 - 00:01:42.710` | the sdvec contains |
| `00:01:42.720 - 00:01:44.389` | and the uservec |
| `00:01:44.399 - 00:01:46.469` | needs to begin by saving the entire |
| `00:01:46.479 - 00:01:49.350` | state of the user mode code needs to say |
| `00:01:49.360 - 00:01:52.469` | the registers and the program counter |
| `00:01:52.479 - 00:01:55.990` | and those are saved in the trap frame |
| `00:01:56.000 - 00:01:58.469` | we also get ready to execute kernel code |
| `00:01:58.479 - 00:02:01.910` | by loading the sp and tp registers |
| `00:02:01.920 - 00:02:04.469` | and then we switch over to executing in |
| `00:02:04.479 - 00:02:06.709` | the kernel address space |
| `00:02:06.719 - 00:02:09.350` | so this initial stuff happens |
| `00:02:09.360 - 00:02:12.949` | when the page table is set to the user's |
| `00:02:12.959 - 00:02:14.229` | address space |
| `00:02:14.239 - 00:02:17.350` | but after we load the satp register |
| `00:02:17.360 - 00:02:19.350` | the code will now be executing in the |
| `00:02:19.360 - 00:02:21.589` | kernels space |
| `00:02:21.599 - 00:02:23.990` | the trampoline page is mapped into the |
| `00:02:24.000 - 00:02:26.229` | uppermost page of all |
| `00:02:26.239 - 00:02:28.229` | address spaces so this won't affect the |
| `00:02:28.239 - 00:02:30.949` | code that's running here but it will |
| `00:02:30.959 - 00:02:32.790` | allow us then to |
| `00:02:32.800 - 00:02:34.630` | jump to code that's in the kernel's |
| `00:02:34.640 - 00:02:36.710` | address space but not the user's address |
| `00:02:36.720 - 00:02:37.589` | space |
| `00:02:37.599 - 00:02:40.390` | and so let's start by taking a look at |
| `00:02:40.400 - 00:02:42.070` | what's going on in |
| `00:02:42.080 - 00:02:49.110` | userback |
| `00:02:49.120 - 00:02:51.910` | so here is code from the trampoline.s |
| `00:02:51.920 - 00:02:54.630` | file this is assembly code |
| `00:02:54.640 - 00:02:56.070` | and |
| `00:02:56.080 - 00:02:59.190` | this code contains two pieces this file |
| `00:02:59.200 - 00:03:01.190` | contains two pieces of code one is |
| `00:03:01.200 - 00:03:02.869` | called the uservec |
| `00:03:02.879 - 00:03:06.390` | function and it takes a little bit of a |
| `00:03:06.400 - 00:03:08.309` | page and a half and then we have another |
| `00:03:08.319 - 00:03:09.430` | one called |
| `00:03:09.440 - 00:03:12.550` | user rat which i'll talk about later |
| `00:03:12.560 - 00:03:13.830` | those are the only two things in this |
| `00:03:13.840 - 00:03:18.630` | trampoline file |
| `00:03:18.640 - 00:03:21.430` | when this is assembled it will be placed |
| `00:03:21.440 - 00:03:23.190` | into a section |
| `00:03:23.200 - 00:03:26.070` | or sometimes i say the word segment |
| `00:03:26.080 - 00:03:28.789` | named sec okay and that will be |
| `00:03:28.799 - 00:03:31.670` | placed by the linker into |
| `00:03:31.680 - 00:03:33.990` | a single page by itself |
| `00:03:34.000 - 00:03:36.470` | and it will be page aligned |
| `00:03:36.480 - 00:03:38.550` | we define a label here |
| `00:03:38.560 - 00:03:40.550` | trampoline lowercase |
| `00:03:40.560 - 00:03:42.789` | and that is the address in |
| `00:03:42.799 - 00:03:45.110` | physical memory of where |
| `00:03:45.120 - 00:03:47.589` | this page is located and we have |
| `00:03:47.599 - 00:03:50.149` | something called userback as well that |
| `00:03:50.159 - 00:03:53.030` | is the address of the start of this code |
| `00:03:53.040 - 00:03:55.190` | we'll look at in a second |
| `00:03:55.200 - 00:03:57.830` | since this is aligned on a page boundary |
| `00:03:57.840 - 00:03:59.990` | this alignment statement here will have |
| `00:04:00.000 - 00:04:02.149` | no effect we're already aligned on a |
| `00:04:02.159 - 00:04:05.190` | four byte boundary so user back and |
| `00:04:05.200 - 00:04:08.309` | trampoline will be the same number |
| `00:04:08.319 - 00:04:10.309` | so when we compute the offset of uservac |
| `00:04:10.319 - 00:04:11.589` | from the beginning of the page we'll |
| `00:04:11.599 - 00:04:13.509` | subtract |
| `00:04:13.519 - 00:04:16.469` | trampoline from userback it's a zero uh |
| `00:04:16.479 - 00:04:19.670` | the offset however when we calculate the |
| `00:04:19.680 - 00:04:22.870` | offset of user ret which is further down |
| `00:04:22.880 - 00:04:24.150` | in this file |
| `00:04:24.160 - 00:04:25.830` | the offset will be |
| `00:04:25.840 - 00:04:28.070` | greater than zero okay so that's why we |
| `00:04:28.080 - 00:04:29.510` | have the same |
| `00:04:29.520 - 00:04:30.790` | address being assigned two different |
| `00:04:30.800 - 00:04:32.230` | labels here |
| `00:04:32.240 - 00:04:33.990` | this is the start of the page in |
| `00:04:34.000 - 00:04:35.430` | physical memory |
| `00:04:35.440 - 00:04:36.870` | this is the start of this particular |
| `00:04:36.880 - 00:04:39.350` | routine within that page |
| `00:04:39.360 - 00:04:41.189` | and we export these two labels with the |
| `00:04:41.199 - 00:04:43.510` | global directive |
| `00:04:43.520 - 00:04:46.310` | okay we've got to save the registers |
| `00:04:46.320 - 00:04:47.830` | initially |
| `00:04:47.840 - 00:04:49.749` | we could ask what's happening |
| `00:04:49.759 - 00:04:52.230` | and what is true while the user code is |
| `00:04:52.240 - 00:04:54.390` | executing and one thing that's true |
| `00:04:54.400 - 00:04:56.469` | is that the |
| `00:04:56.479 - 00:04:58.629` | control and status register called s |
| `00:04:58.639 - 00:05:01.909` | scratch contains a pointer to |
| `00:05:01.919 - 00:05:04.469` | this um |
| `00:05:04.479 - 00:05:07.350` | to the |
| `00:05:07.360 - 00:05:09.510` | trap frame |
| `00:05:09.520 - 00:05:12.150` | and so we |
| `00:05:12.160 - 00:05:13.990` | this is handy because we really can't do |
| `00:05:14.000 - 00:05:15.670` | anything with registers we can't really |
| `00:05:15.680 - 00:05:17.749` | execute any code because we have to save |
| `00:05:17.759 - 00:05:20.550` | the registers first but we do have this |
| `00:05:20.560 - 00:05:22.950` | instruction control and status register |
| `00:05:22.960 - 00:05:25.749` | read write which will swap |
| `00:05:25.759 - 00:05:29.029` | the values in a0 and s scratch |
| `00:05:29.039 - 00:05:29.909` | so |
| `00:05:29.919 - 00:05:32.790` | after this instruction is executed |
| `00:05:32.800 - 00:05:35.430` | s scratch will contain the user's value |
| `00:05:35.440 - 00:05:36.950` | of a0 |
| `00:05:36.960 - 00:05:39.110` | and a 0 will contain a pointer to the |
| `00:05:39.120 - 00:05:41.270` | trap frame so now we can use that |
| `00:05:41.280 - 00:05:42.310` | pointer |
| `00:05:42.320 - 00:05:43.510` | and |
| `00:05:43.520 - 00:05:46.469` | store all the remaining registers so we |
| `00:05:46.479 - 00:05:48.870` | begin storing the registers here and we |
| `00:05:48.880 - 00:05:50.950` | store all of the registers |
| `00:05:50.960 - 00:05:53.110` | except a0 itself |
| `00:05:53.120 - 00:05:54.310` | notice that |
| `00:05:54.320 - 00:05:57.590` | these are the offsets here and this is a |
| `00:05:57.600 - 00:05:59.909` | store double word of this register into |
| `00:05:59.919 - 00:06:03.110` | this offset from this pointer |
| `00:06:03.120 - 00:06:04.550` | and you can see that |
| `00:06:04.560 - 00:06:05.590` | offset |
| `00:06:05.600 - 00:06:08.469` | 2 is left out for a 0. so we store |
| `00:06:08.479 - 00:06:10.790` | everything except a0 |
| `00:06:10.800 - 00:06:11.990` | and then |
| `00:06:12.000 - 00:06:13.990` | following us down we store all the |
| `00:06:14.000 - 00:06:15.350` | registers |
| `00:06:15.360 - 00:06:16.230` | and |
| `00:06:16.240 - 00:06:17.990` | now that we've stored the registers we |
| `00:06:18.000 - 00:06:20.230` | have a little bit more flexibility we've |
| `00:06:20.240 - 00:06:22.629` | stored t0 so we can use that |
| `00:06:22.639 - 00:06:23.830` | this |
| `00:06:23.840 - 00:06:26.710` | reads from the scratch register into t0 |
| `00:06:26.720 - 00:06:30.390` | and this stores from t0 into offset 112 |
| `00:06:30.400 - 00:06:33.670` | so now we stored a zero |
| `00:06:33.680 - 00:06:37.270` | now let's take a look at um |
| `00:06:37.280 - 00:06:38.070` | the |
| `00:06:38.080 - 00:06:40.710` | trap frame and in this image |
| `00:06:40.720 - 00:06:42.390` | i'm showing how the trap frame is laid |
| `00:06:42.400 - 00:06:43.590` | out |
| `00:06:43.600 - 00:06:46.710` | and so we've got a number of |
| `00:06:46.720 - 00:06:47.670` | uh |
| `00:06:47.680 - 00:06:49.990` | locations where we save the state of the |
| `00:06:50.000 - 00:06:53.029` | user mode thread and we just saved the |
| `00:06:53.039 - 00:06:55.510` | 31 general purpose registers into the |
| `00:06:55.520 - 00:06:57.990` | register save area here we've not yet |
| `00:06:58.000 - 00:06:59.749` | saved the |
| `00:06:59.759 - 00:07:02.550` | user's program counter but we will we've |
| `00:07:02.560 - 00:07:05.350` | also got several locations here |
| `00:07:05.360 - 00:07:07.189` | that we need to load so that we can |
| `00:07:07.199 - 00:07:09.749` | execute kernel code |
| `00:07:09.759 - 00:07:11.110` | and so |
| `00:07:11.120 - 00:07:13.990` | uh actually uh |
| `00:07:14.000 - 00:07:16.309` | so that's what we're doing here we're |
| `00:07:16.319 - 00:07:19.270` | loading the stack pointer |
| `00:07:19.280 - 00:07:20.230` | from |
| `00:07:20.240 - 00:07:22.469` | this location in |
| `00:07:22.479 - 00:07:23.270` | the |
| `00:07:23.280 - 00:07:25.749` | track frame we're locating uh loading |
| `00:07:25.759 - 00:07:26.550` | the |
| `00:07:26.560 - 00:07:28.469` | thread pointer |
| `00:07:28.479 - 00:07:29.430` | uh |
| `00:07:29.440 - 00:07:32.550` | and we're loading the satp register so |
| `00:07:32.560 - 00:07:34.150` | let's go through that |
| `00:07:34.160 - 00:07:35.589` | um |
| `00:07:35.599 - 00:07:37.909` | we grab the |
| `00:07:37.919 - 00:07:39.749` | value of the sp that's stored in the |
| `00:07:39.759 - 00:07:42.469` | trap frame and put it into the sp |
| `00:07:42.479 - 00:07:44.469` | register |
| `00:07:44.479 - 00:07:47.430` | the |
| `00:07:47.440 - 00:07:49.110` | kernel code |
| `00:07:49.120 - 00:07:50.309` | we will not have |
| `00:07:50.319 - 00:07:51.670` | so |
| `00:07:51.680 - 00:07:54.710` | we have the trap executing here and once |
| `00:07:54.720 - 00:07:56.469` | this trap happens we're running in |
| `00:07:56.479 - 00:07:58.230` | kernel mode and so we're going to |
| `00:07:58.240 - 00:08:00.790` | execute all of this stuff we're going to |
| `00:08:00.800 - 00:08:02.550` | handle the trap whether it's a timer |
| `00:08:02.560 - 00:08:05.110` | interrupt or a device handler or |
| `00:08:05.120 - 00:08:07.749` | whatever it is system call |
| `00:08:07.759 - 00:08:08.950` | we're going to handle that in kernel |
| `00:08:08.960 - 00:08:09.830` | mode |
| `00:08:09.840 - 00:08:13.110` | as part of this little thread |
| `00:08:13.120 - 00:08:14.150` | from |
| `00:08:14.160 - 00:08:16.629` | here to here before we return to the |
| `00:08:16.639 - 00:08:19.270` | user's code |
| `00:08:19.280 - 00:08:21.589` | you can think of this as part of the |
| `00:08:21.599 - 00:08:24.710` | processes thread or you can think of it |
| `00:08:24.720 - 00:08:26.950` | as like a little mini thread |
| `00:08:26.960 - 00:08:27.909` | but |
| `00:08:27.919 - 00:08:29.670` | we're starting with a fresh stack right |
| `00:08:29.680 - 00:08:32.149` | here and we're not |
| `00:08:32.159 - 00:08:33.909` | retrieving the previous values of |
| `00:08:33.919 - 00:08:35.190` | registers |
| `00:08:35.200 - 00:08:36.870` | so i don't think it's right to think of |
| `00:08:36.880 - 00:08:39.350` | this as continuing a kernel thread |
| `00:08:39.360 - 00:08:41.350` | instead we're starting a new thread |
| `00:08:41.360 - 00:08:44.070` | we're only loading the stack pointer |
| `00:08:44.080 - 00:08:44.949` | and |
| `00:08:44.959 - 00:08:46.310` | tp |
| `00:08:46.320 - 00:08:48.070` | the other registers |
| `00:08:48.080 - 00:08:49.190` | are |
| `00:08:49.200 - 00:08:51.190` | will have values that are left over from |
| `00:08:51.200 - 00:08:54.230` | the user mode code |
| `00:08:54.240 - 00:08:55.590` | the stack pointer that we will be |
| `00:08:55.600 - 00:08:57.670` | loading perhaps i should have said load |
| `00:08:57.680 - 00:08:59.190` | rather than restore because we're not |
| `00:08:59.200 - 00:09:01.670` | retrieving a previous value as much as |
| `00:09:01.680 - 00:09:02.710` | we are |
| `00:09:02.720 - 00:09:05.269` | initializing the stack pointer we are |
| `00:09:05.279 - 00:09:06.630` | initializing it |
| `00:09:06.640 - 00:09:07.350` | to |
| `00:09:07.360 - 00:09:09.670` | what's called the kernels |
| `00:09:09.680 - 00:09:11.110` | stack page |
| `00:09:11.120 - 00:09:14.389` | every process each of our 64 processes |
| `00:09:14.399 - 00:09:16.550` | will have a single page |
| `00:09:16.560 - 00:09:19.750` | set aside for the kernel stack and that |
| `00:09:19.760 - 00:09:22.389` | is what we're loading into sp here |
| `00:09:22.399 - 00:09:24.230` | or more precisely we're loading a |
| `00:09:24.240 - 00:09:25.670` | pointer to |
| `00:09:25.680 - 00:09:25.960` | the |
| `00:09:25.970 - 00:09:27.590` | [Music] |
| `00:09:27.600 - 00:09:29.509` | top of that page |
| `00:09:29.519 - 00:09:32.389` | remember that stacks grow downward so |
| `00:09:32.399 - 00:09:33.829` | we're essentially loading |
| `00:09:33.839 - 00:09:37.190` | sp with a pointer to a completely empty |
| `00:09:37.200 - 00:09:39.030` | stack |
| `00:09:39.040 - 00:09:41.110` | so that's what happens at this point |
| `00:09:41.120 - 00:09:45.190` | here |
| `00:09:45.200 - 00:09:47.190` | we've been executing and we are |
| `00:09:47.200 - 00:09:49.350` | currently executing on some particular |
| `00:09:49.360 - 00:09:51.590` | core but we don't know what core we're |
| `00:09:51.600 - 00:09:53.030` | executing on |
| `00:09:53.040 - 00:09:55.829` | however before we went into the user |
| `00:09:55.839 - 00:09:59.269` | mode code we would have saved |
| `00:09:59.279 - 00:10:00.790` | in this |
| `00:10:00.800 - 00:10:02.829` | field right here |
| `00:10:02.839 - 00:10:05.350` | the number of the core that we're |
| `00:10:05.360 - 00:10:07.430` | executing on |
| `00:10:07.440 - 00:10:10.550` | so the so-called heart id |
| `00:10:10.560 - 00:10:13.269` | and so at this point we are loading that |
| `00:10:13.279 - 00:10:16.230` | back into the so-called thread pointer |
| `00:10:16.240 - 00:10:17.430` | register |
| `00:10:17.440 - 00:10:18.949` | uh |
| `00:10:18.959 - 00:10:20.710` | by the way if you're watching i actually |
| `00:10:20.720 - 00:10:22.949` | reverse these so the offsets don't |
| `00:10:22.959 - 00:10:25.509` | exactly match up but uh the idea is |
| `00:10:25.519 - 00:10:26.630` | there |
| `00:10:26.640 - 00:10:27.990` | so we're loading |
| `00:10:28.000 - 00:10:30.550` | tp with the core number so now we can |
| `00:10:30.560 - 00:10:33.430` | make use of that |
| `00:10:33.440 - 00:10:34.470` | and |
| `00:10:34.480 - 00:10:35.910` | what we're going to do down here is |
| `00:10:35.920 - 00:10:37.350` | we're going to |
| `00:10:37.360 - 00:10:40.310` | jump into the user trap function |
| `00:10:40.320 - 00:10:42.550` | so we're going to do a jump via register |
| `00:10:42.560 - 00:10:46.470` | of t0 here we are loading t0 and we have |
| `00:10:46.480 - 00:10:49.509` | previously saved into this field the |
| `00:10:49.519 - 00:10:52.870` | address of the user trap function |
| `00:10:52.880 - 00:10:55.590` | so this instruction here is preparing |
| `00:10:55.600 - 00:10:59.430` | for this jump down here |
| `00:10:59.440 - 00:11:01.829` | at this point we're ready to switch to |
| `00:11:01.839 - 00:11:03.990` | the kernel's address space |
| `00:11:04.000 - 00:11:06.870` | uh we've uh set everything up so that we |
| `00:11:06.880 - 00:11:09.430` | can now execute kernel code in kernel's |
| `00:11:09.440 - 00:11:10.870` | address space |
| `00:11:10.880 - 00:11:11.750` | so |
| `00:11:11.760 - 00:11:13.269` | here we're |
| `00:11:13.279 - 00:11:14.870` | getting the |
| `00:11:14.880 - 00:11:17.269` | pointer to the kernel's page table |
| `00:11:17.279 - 00:11:19.269` | which we've previously saved |
| `00:11:19.279 - 00:11:22.069` | and we are storing it into the control |
| `00:11:22.079 - 00:11:24.870` | and status register called satp |
| `00:11:24.880 - 00:11:27.670` | so we retrieve it into register t1 and |
| `00:11:27.680 - 00:11:29.990` | then write it into this register and |
| `00:11:30.000 - 00:11:32.310` | then we do a fence instruction to make |
| `00:11:32.320 - 00:11:33.670` | sure that |
| `00:11:33.680 - 00:11:35.110` | everything that is supposed to be |
| `00:11:35.120 - 00:11:37.590` | executed before that is executed to |
| `00:11:37.600 - 00:11:39.190` | completion before that |
| `00:11:39.200 - 00:11:40.790` | and every instruction that's supposed to |
| `00:11:40.800 - 00:11:42.949` | be executed afterwards is |
| `00:11:42.959 - 00:11:44.550` | delayed until after the |
| `00:11:44.560 - 00:11:49.030` | satp register is loaded |
| `00:11:49.040 - 00:11:52.790` | the risk 5 processor |
| `00:11:52.800 - 00:11:54.949` | calls a function by jumping to the first |
| `00:11:54.959 - 00:11:58.310` | address after saving the return address |
| `00:11:58.320 - 00:12:02.310` | in register ra return address |
| `00:12:02.320 - 00:12:06.790` | so that's how a call is normally made |
| `00:12:06.800 - 00:12:08.389` | however in this case we're simply |
| `00:12:08.399 - 00:12:10.629` | jumping we're not saving the return |
| `00:12:10.639 - 00:12:13.190` | address and that's because user track |
| `00:12:13.200 - 00:12:15.590` | rep does not return |
| `00:12:15.600 - 00:12:17.910` | so now let's go take a look at user trap |
| `00:12:17.920 - 00:12:19.670` | return |
| `00:12:19.680 - 00:12:21.829` | sorry user trap |
| `00:12:21.839 - 00:12:23.110` | and |
| `00:12:23.120 - 00:12:29.910` | we have that code here |
| `00:12:29.920 - 00:12:32.150` | so this is coming from the trap |
| `00:12:32.160 - 00:12:35.269` | dot c file and there's some other things |
| `00:12:35.279 - 00:12:36.870` | there are some other functions in this |
| `00:12:36.880 - 00:12:38.389` | file which i will not cover in this |
| `00:12:38.399 - 00:12:42.150` | video but i will look at user trap |
| `00:12:42.160 - 00:12:44.230` | at this point so |
| `00:12:44.240 - 00:12:46.470` | this is expressed as a c function but |
| `00:12:46.480 - 00:12:50.069` | it's a c function that will not return |
| `00:12:50.079 - 00:12:52.550` | what will it do well at the end of this |
| `00:12:52.560 - 00:12:55.190` | function |
| `00:12:55.200 - 00:12:56.340` | it will |
| `00:12:56.350 - 00:12:59.670` | [Music] |
| `00:12:59.680 - 00:13:03.030` | it will |
| `00:13:03.040 - 00:13:04.230` | call |
| `00:13:04.240 - 00:13:05.670` | another function |
| `00:13:05.680 - 00:13:06.470` | and |
| `00:13:06.480 - 00:13:08.150` | that function will not return so we'll |
| `00:13:08.160 - 00:13:10.470` | get there |
| `00:13:10.480 - 00:13:13.350` | um |
| `00:13:13.360 - 00:13:16.389` | here we're doing a sanity check we're |
| `00:13:16.399 - 00:13:18.710` | looking at what's in the status register |
| `00:13:18.720 - 00:13:20.389` | and in particular we're looking at the |
| `00:13:20.399 - 00:13:22.949` | previous privilege level bit |
| `00:13:22.959 - 00:13:24.870` | and that tells whether the |
| `00:13:24.880 - 00:13:27.110` | the core was running in user mode or |
| `00:13:27.120 - 00:13:30.230` | supervisor mode when the trap occurred |
| `00:13:30.240 - 00:13:32.310` | and it had better have been running in |
| `00:13:32.320 - 00:13:33.750` | user mode or else something is |
| `00:13:33.760 - 00:13:37.430` | dreadfully the matter |
| `00:13:37.440 - 00:13:39.910` | so now we're running a kernel mode what |
| `00:13:39.920 - 00:13:42.150` | if we get another trap okay a device |
| `00:13:42.160 - 00:13:45.269` | might need service for example and might |
| `00:13:45.279 - 00:13:47.509` | cause an interrupt so here we write into |
| `00:13:47.519 - 00:13:50.310` | the st back a new address here we're |
| `00:13:50.320 - 00:13:52.629` | writing into it the address of a |
| `00:13:52.639 - 00:13:54.710` | function that will handle kernel |
| `00:13:54.720 - 00:13:56.949` | interrupts |
| `00:13:56.959 - 00:13:58.790` | and |
| `00:13:58.800 - 00:14:03.030` | then we grab a pointer to the proc |
| `00:14:03.040 - 00:14:04.150` | structure |
| `00:14:04.160 - 00:14:06.310` | i showed a picture of the proc uh |
| `00:14:06.320 - 00:14:08.389` | structure earlier and it was this |
| `00:14:08.399 - 00:14:11.189` | picture here okay so |
| `00:14:11.199 - 00:14:13.670` | we've got information such as the |
| `00:14:13.680 - 00:14:16.150` | pointer to the page table and and a |
| `00:14:16.160 - 00:14:18.310` | pointer to the trap frame and some other |
| `00:14:18.320 - 00:14:19.509` | things |
| `00:14:19.519 - 00:14:22.389` | and in particular we're interested in |
| `00:14:22.399 - 00:14:24.949` | this field trap frame which is a pointer |
| `00:14:24.959 - 00:14:25.750` | to |
| `00:14:25.760 - 00:14:29.189` | the physical address of the trap frame |
| `00:14:29.199 - 00:14:31.750` | page for this process |
| `00:14:31.760 - 00:14:32.870` | in memory |
| `00:14:32.880 - 00:14:34.470` | each of our |
| `00:14:34.480 - 00:14:36.310` | 64 processes |
| `00:14:36.320 - 00:14:38.550` | will have its own |
| `00:14:38.560 - 00:14:41.269` | page in physical memory for trap for the |
| `00:14:41.279 - 00:14:43.030` | trap frame |
| `00:14:43.040 - 00:14:45.350` | those will be mapped into the second to |
| `00:14:45.360 - 00:14:47.590` | the highest page in the user's address |
| `00:14:47.600 - 00:14:50.069` | space but we're no longer running in the |
| `00:14:50.079 - 00:14:52.150` | user's address space so we need the |
| `00:14:52.160 - 00:14:54.790` | physical address and this field it |
| `00:14:54.800 - 00:14:57.590` | contains a pointer to the trap frame |
| `00:14:57.600 - 00:14:59.590` | in physical memory |
| `00:14:59.600 - 00:15:02.470` | so we use we can use that now |
| `00:15:02.480 - 00:15:06.470` | uh previously we have not yet stored the |
| `00:15:06.480 - 00:15:08.710` | the program count uh sorry the program |
| `00:15:08.720 - 00:15:12.230` | counter from the user's address space so |
| `00:15:12.240 - 00:15:14.949` | here we're storing that okay so this is |
| `00:15:14.959 - 00:15:17.350` | the last bit of state of the user mode |
| `00:15:17.360 - 00:15:19.430` | process that we need to store |
| `00:15:19.440 - 00:15:20.949` | so |
| `00:15:20.959 - 00:15:23.030` | that's done at this point where we read |
| `00:15:23.040 - 00:15:24.790` | the control and status register and then |
| `00:15:24.800 - 00:15:27.910` | save its value |
| `00:15:27.920 - 00:15:30.790` | in my roadmap picture |
| `00:15:30.800 - 00:15:33.350` | i showed a big if statement |
| `00:15:33.360 - 00:15:35.430` | okay so now we're in user trap |
| `00:15:35.440 - 00:15:36.870` | and |
| `00:15:36.880 - 00:15:38.870` | we're going to do this if statement so |
| `00:15:38.880 - 00:15:41.749` | that's what's going on next |
| `00:15:41.759 - 00:15:42.870` | so |
| `00:15:42.880 - 00:15:45.110` | we look at the value of the s cause |
| `00:15:45.120 - 00:15:48.470` | register to determine what's going on |
| `00:15:48.480 - 00:15:51.829` | if it's eight that means the trap was as |
| `00:15:51.839 - 00:15:54.389` | a result of a system call |
| `00:15:54.399 - 00:15:55.430` | so |
| `00:15:55.440 - 00:15:56.230` | we |
| `00:15:56.240 - 00:15:58.710` | do this bit of code here |
| `00:15:58.720 - 00:16:01.110` | we'll ultimately call a function system |
| `00:16:01.120 - 00:16:03.110` | called to deal with it but |
| `00:16:03.120 - 00:16:06.470` | first of all we take a look at this trap |
| `00:16:06.480 - 00:16:08.710` | sorry this killed uh |
| `00:16:08.720 - 00:16:09.829` | field |
| `00:16:09.839 - 00:16:11.990` | that's a field in the proc |
| `00:16:12.000 - 00:16:14.150` | it's a boolean |
| `00:16:14.160 - 00:16:16.710` | if anybody wants this particular process |
| `00:16:16.720 - 00:16:19.350` | to die it can set that boolean field to |
| `00:16:19.360 - 00:16:21.430` | one |
| `00:16:21.440 - 00:16:24.230` | so we check it right now to see whether |
| `00:16:24.240 - 00:16:27.269` | this process needs to commit suicide and |
| `00:16:27.279 - 00:16:28.550` | if it does |
| `00:16:28.560 - 00:16:31.110` | we call a function exit exit will not |
| `00:16:31.120 - 00:16:34.230` | return so that's that but let's assume |
| `00:16:34.240 - 00:16:35.189` | that we |
| `00:16:35.199 - 00:16:37.189` | don't need to commit suicide and we keep |
| `00:16:37.199 - 00:16:39.749` | going |
| `00:16:39.759 - 00:16:41.590` | the |
| `00:16:41.600 - 00:16:44.790` | instruction that does the system call |
| `00:16:44.800 - 00:16:46.629` | in risk five is called the |
| `00:16:46.639 - 00:16:48.389` | environment call instruction e-call |
| `00:16:48.399 - 00:16:49.670` | instruction |
| `00:16:49.680 - 00:16:50.470` | and |
| `00:16:50.480 - 00:16:51.749` | our |
| `00:16:51.759 - 00:16:53.990` | risk five core will have saved the |
| `00:16:54.000 - 00:16:56.710` | pointer to that instruction in the epc |
| `00:16:56.720 - 00:16:57.829` | register |
| `00:16:57.839 - 00:16:59.749` | but when we return we don't want to |
| `00:16:59.759 - 00:17:01.990` | execute this instruction again we want |
| `00:17:02.000 - 00:17:04.470` | to increment the pc |
| `00:17:04.480 - 00:17:06.230` | to point to the instruction directly |
| `00:17:06.240 - 00:17:08.949` | after the e-call so |
| `00:17:08.959 - 00:17:11.029` | the instructions in the risc-5 processor |
| `00:17:11.039 - 00:17:13.669` | are four bytes long so we increment it |
| `00:17:13.679 - 00:17:16.949` | by four to adjust |
| `00:17:16.959 - 00:17:18.949` | and now we're ready to |
| `00:17:18.959 - 00:17:21.110` | now we're ready to turn interrupts on |
| `00:17:21.120 - 00:17:24.150` | so at this point the kernel code can be |
| `00:17:24.160 - 00:17:26.710` | interrupted by another trap |
| `00:17:26.720 - 00:17:28.470` | so for example if a device needs |
| `00:17:28.480 - 00:17:31.350` | handling it can happen while we're |
| `00:17:31.360 - 00:17:33.750` | handling the system call |
| `00:17:33.760 - 00:17:35.990` | okay we've saved the state of the user |
| `00:17:36.000 - 00:17:37.110` | thread |
| `00:17:37.120 - 00:17:39.430` | in here and if we get interrupted we're |
| `00:17:39.440 - 00:17:40.630` | going to have to save the state of the |
| `00:17:40.640 - 00:17:41.510` | kernel |
| `00:17:41.520 - 00:17:43.190` | thread somewhere else but we're not |
| `00:17:43.200 - 00:17:44.870` | going to worry too much about that right |
| `00:17:44.880 - 00:17:47.990` | now so we call this function and |
| `00:17:48.000 - 00:17:49.350` | depending on what kind of a system call |
| `00:17:49.360 - 00:17:53.510` | it is we deal with it and um if it's the |
| `00:17:53.520 - 00:17:55.909` | uh exit system call we |
| `00:17:55.919 - 00:17:57.830` | may not |
| `00:17:57.840 - 00:18:00.070` | in any case we return here and keep |
| `00:18:00.080 - 00:18:01.350` | going |
| `00:18:01.360 - 00:18:03.430` | then |
| `00:18:03.440 - 00:18:05.270` | if it's not a system call |
| `00:18:05.280 - 00:18:09.110` | we call this function device interrupt |
| `00:18:09.120 - 00:18:11.909` | and that then looks at the cause |
| `00:18:11.919 - 00:18:14.070` | register to determine which device |
| `00:18:14.080 - 00:18:15.270` | requires |
| `00:18:15.280 - 00:18:16.310` | handling |
| `00:18:16.320 - 00:18:19.190` | and it returns a code |
| `00:18:19.200 - 00:18:22.710` | if it is a normal device such as the |
| `00:18:22.720 - 00:18:26.310` | serial com device or the disk device it |
| `00:18:26.320 - 00:18:30.390` | returns one if it was a timer interrupt |
| `00:18:30.400 - 00:18:32.710` | or more precisely if it was a |
| `00:18:32.720 - 00:18:35.830` | software interrupt which is how machine |
| `00:18:35.840 - 00:18:37.750` | mode code will translate the timer |
| `00:18:37.760 - 00:18:40.710` | interrupt then it returns a two |
| `00:18:40.720 - 00:18:42.390` | and if it's anything else |
| `00:18:42.400 - 00:18:44.870` | it's an error and it returns zero so |
| `00:18:44.880 - 00:18:48.950` | here we're saying is it an error or not |
| `00:18:48.960 - 00:18:52.390` | if it is not an error and it is a device |
| `00:18:52.400 - 00:18:54.470` | then it will be handled here |
| `00:18:54.480 - 00:18:56.390` | if it is a timer interrupt |
| `00:18:56.400 - 00:18:58.789` | well we may update a counter to keep |
| `00:18:58.799 - 00:19:00.710` | track of the current time but basically |
| `00:19:00.720 - 00:19:03.110` | we just return two |
| `00:19:03.120 - 00:19:06.789` | and we save that in this switch device |
| `00:19:06.799 - 00:19:08.870` | variable which we'll then check right |
| `00:19:08.880 - 00:19:10.470` | down here |
| `00:19:10.480 - 00:19:12.630` | but if it is zero then we print an error |
| `00:19:12.640 - 00:19:13.590` | message |
| `00:19:13.600 - 00:19:16.870` | and set this killed boolean |
| `00:19:16.880 - 00:19:18.789` | to indicate that we need to kill |
| `00:19:18.799 - 00:19:21.990` | ourselves in the future down here |
| `00:19:22.000 - 00:19:23.350` | the error message is just going to print |
| `00:19:23.360 - 00:19:25.110` | out the process id |
| `00:19:25.120 - 00:19:27.909` | it's going to print out the location in |
| `00:19:27.919 - 00:19:31.270` | the user's address space at which the |
| `00:19:31.280 - 00:19:33.270` | error occurred |
| `00:19:33.280 - 00:19:41.350` | and |
| `00:19:41.360 - 00:19:45.430` | uh it's going to print out the |
| `00:19:45.440 - 00:19:47.110` | value of the st val which may contain |
| `00:19:47.120 - 00:19:48.950` | additional information |
| `00:19:48.960 - 00:19:50.390` | uh if you're not familiar with the |
| `00:19:50.400 - 00:19:52.630` | formatting code percent p it basically |
| `00:19:52.640 - 00:19:55.590` | prints out a |
| `00:19:55.600 - 00:20:00.390` | 64-bit value in hex |
| `00:20:00.400 - 00:20:02.470` | i paused for a moment because i saw that |
| `00:20:02.480 - 00:20:04.870` | we were reading from these control and |
| `00:20:04.880 - 00:20:08.789` | status registers and uh i momentarily |
| `00:20:08.799 - 00:20:12.149` | forgot that we turned interrupts on but |
| `00:20:12.159 - 00:20:15.990` | we only turned them on if we are in |
| `00:20:16.000 - 00:20:19.190` | a system call if we are in the device |
| `00:20:19.200 - 00:20:21.750` | interrupt the interrupts are not turned |
| `00:20:21.760 - 00:20:24.710` | on so the device handlers will execute |
| `00:20:24.720 - 00:20:27.669` | with interrupts disabled and likewise |
| `00:20:27.679 - 00:20:29.350` | this error message has interrupts |
| `00:20:29.360 - 00:20:31.190` | disabled so |
| `00:20:31.200 - 00:20:34.149` | these val these registers are still okay |
| `00:20:34.159 - 00:20:36.470` | to be read directly |
| `00:20:36.480 - 00:20:39.190` | okay if at any point in the system call |
| `00:20:39.200 - 00:20:40.549` | or or |
| `00:20:40.559 - 00:20:44.789` | here we've set killed to true then we |
| `00:20:44.799 - 00:20:47.270` | self-terminate |
| `00:20:47.280 - 00:20:49.270` | finally if we had a |
| `00:20:49.280 - 00:20:51.110` | timer interrupt |
| `00:20:51.120 - 00:20:55.430` | then we call the yield function |
| `00:20:55.440 - 00:20:57.990` | can be considered uh like |
| `00:20:58.000 - 00:20:59.110` | a no op |
| `00:20:59.120 - 00:21:00.310` | so |
| `00:21:00.320 - 00:21:02.149` | we call this function and then we return |
| `00:21:02.159 - 00:21:03.270` | from it |
| `00:21:03.280 - 00:21:04.470` | however |
| `00:21:04.480 - 00:21:06.789` | we may not return from it immediately |
| `00:21:06.799 - 00:21:09.029` | yield we'll call the scheduler and it |
| `00:21:09.039 - 00:21:10.870` | may schedule some other |
| `00:21:10.880 - 00:21:12.870` | threads to run and it may be quite a |
| `00:21:12.880 - 00:21:15.029` | while before this yield function |
| `00:21:15.039 - 00:21:18.390` | ultimately returns but at some point we |
| `00:21:18.400 - 00:21:21.190` | assume this scheduler will give the |
| `00:21:21.200 - 00:21:22.710` | processor back to |
| `00:21:22.720 - 00:21:24.710` | this particular thread and |
| `00:21:24.720 - 00:21:26.950` | while it will save all registers it will |
| `00:21:26.960 - 00:21:29.590` | restore them and so yield will simply |
| `00:21:29.600 - 00:21:31.590` | return |
| `00:21:31.600 - 00:21:33.590` | however it will not necessarily |
| `00:21:33.600 - 00:21:35.430` | return on the same core we may have |
| `00:21:35.440 - 00:21:38.470` | gotten switched to a different core |
| `00:21:38.480 - 00:21:41.029` | so let's continue with this uh function |
| `00:21:41.039 - 00:21:44.549` | and i'll show it like this |
| `00:21:44.559 - 00:21:45.669` | line them up |
| `00:21:45.679 - 00:21:47.669` | so |
| `00:21:47.679 - 00:21:49.510` | after this test |
| `00:21:49.520 - 00:21:52.149` | then we call user trap return |
| `00:21:52.159 - 00:21:54.630` | user trap return is right here so this |
| `00:21:54.640 - 00:21:56.149` | is effectively |
| `00:21:56.159 - 00:21:56.789` | a |
| `00:21:56.799 - 00:21:58.710` | go to if you will or or just falls |
| `00:21:58.720 - 00:22:00.230` | through |
| `00:22:00.240 - 00:22:01.110` | um |
| `00:22:01.120 - 00:22:03.990` | we also will invoke user trap return |
| `00:22:04.000 - 00:22:04.789` | from |
| `00:22:04.799 - 00:22:07.830` | um the fork process but i don't want to |
| `00:22:07.840 - 00:22:09.430` | go into that right now but that's why |
| `00:22:09.440 - 00:22:12.710` | this is set out as a separate function |
| `00:22:12.720 - 00:22:15.029` | rather than having it just simply fall |
| `00:22:15.039 - 00:22:18.310` | through so now |
| `00:22:18.320 - 00:22:19.909` | now we are |
| `00:22:19.919 - 00:22:22.549` | whoops we are |
| `00:22:22.559 - 00:22:23.830` | down here |
| `00:22:23.840 - 00:22:26.230` | and we have considered doing the yield |
| `00:22:26.240 - 00:22:30.470` | and we are now ready to return to the |
| `00:22:30.480 - 00:22:32.789` | user mode thread so we're now in user |
| `00:22:32.799 - 00:22:34.390` | trap return |
| `00:22:34.400 - 00:22:36.789` | and |
| `00:22:36.799 - 00:22:38.870` | we need to disable interrupts |
| `00:22:38.880 - 00:22:41.029` | and we need to |
| `00:22:41.039 - 00:22:42.630` | set up some stuff |
| `00:22:42.640 - 00:22:44.470` | and then go into the |
| `00:22:44.480 - 00:22:46.470` | assembly code in the trampoline page so |
| `00:22:46.480 - 00:22:47.350` | let's |
| `00:22:47.360 - 00:22:48.710` | walk through |
| `00:22:48.720 - 00:22:53.990` | what happens at that point |
| `00:22:54.000 - 00:22:56.390` | interrupts may have been turned on |
| `00:22:56.400 - 00:22:58.630` | if we had a system call for example |
| `00:22:58.640 - 00:23:01.830` | and so we need to turn interrupts off |
| `00:23:01.840 - 00:23:02.950` | we're going to be messing with some |
| `00:23:02.960 - 00:23:05.350` | control and status registers and we |
| `00:23:05.360 - 00:23:06.549` | cannot allow |
| `00:23:06.559 - 00:23:09.350` | the core to be interrupted while we are |
| `00:23:09.360 - 00:23:10.870` | dealing with |
| `00:23:10.880 - 00:23:14.470` | the control and status registers |
| `00:23:14.480 - 00:23:17.110` | we are grabbing a pointer to the proc |
| `00:23:17.120 - 00:23:23.029` | structure as well up here |
| `00:23:23.039 - 00:23:26.070` | so previously we had set the |
| `00:23:26.080 - 00:23:29.990` | st vac register to point to |
| `00:23:30.000 - 00:23:32.390` | kernel vac which will handle any |
| `00:23:32.400 - 00:23:34.310` | interrupts and traps that occur |
| `00:23:34.320 - 00:23:36.870` | if we're executing in kernel mode |
| `00:23:36.880 - 00:23:38.789` | now we're going to restore that by |
| `00:23:38.799 - 00:23:42.149` | writing into the sdvac the address of |
| `00:23:42.159 - 00:23:44.950` | the uservec code so any future traps |
| `00:23:44.960 - 00:23:47.190` | will jump to uservac |
| `00:23:47.200 - 00:23:49.430` | and what is that address well |
| `00:23:49.440 - 00:23:52.070` | it is the offset from trampoline which |
| `00:23:52.080 - 00:23:54.789` | as i said was zero |
| `00:23:54.799 - 00:23:58.070` | plus the address of the trampoline page |
| `00:23:58.080 - 00:24:01.510` | which is the highest page in memory |
| `00:24:01.520 - 00:24:02.870` | so |
| `00:24:02.880 - 00:24:04.789` | next we |
| `00:24:04.799 - 00:24:06.230` | uh |
| `00:24:06.240 - 00:24:08.789` | look at that trap frame in other words |
| `00:24:08.799 - 00:24:10.470` | we've got a pointer to |
| `00:24:10.480 - 00:24:12.630` | the proc structure |
| `00:24:12.640 - 00:24:15.510` | and we follow it to the physical address |
| `00:24:15.520 - 00:24:18.149` | of the trap of our trap frame the trap |
| `00:24:18.159 - 00:24:20.630` | frame page for this process |
| `00:24:20.640 - 00:24:22.549` | and we're going to |
| `00:24:22.559 - 00:24:23.430` | then |
| `00:24:23.440 - 00:24:26.149` | set the fields |
| `00:24:26.159 - 00:24:28.149` | these fields here |
| `00:24:28.159 - 00:24:30.630` | these four fields that we need |
| `00:24:30.640 - 00:24:32.789` | when the next trap occurs so that's what |
| `00:24:32.799 - 00:24:39.750` | we're doing at this point |
| `00:24:39.760 - 00:24:41.510` | we are currently executing in the |
| `00:24:41.520 - 00:24:43.350` | kernel's address space |
| `00:24:43.360 - 00:24:44.950` | so we get |
| `00:24:44.960 - 00:24:47.750` | the pointer to the kernels page table |
| `00:24:47.760 - 00:24:55.510` | and we store it here into the satp field |
| `00:24:55.520 - 00:24:57.830` | this is not actually a pointer but it's |
| `00:24:57.840 - 00:24:59.110` | basically you can think of it as a |
| `00:24:59.120 - 00:25:00.470` | pointer there are a couple of bits that |
| `00:25:00.480 - 00:25:02.149` | are set |
| `00:25:02.159 - 00:25:03.830` | but |
| `00:25:03.840 - 00:25:05.350` | we will restore it |
| `00:25:05.360 - 00:25:08.390` | as it is here |
| `00:25:08.400 - 00:25:09.190` | then |
| `00:25:09.200 - 00:25:12.789` | we set the sp field the stack pointer |
| `00:25:12.799 - 00:25:14.789` | every process has a stack |
| `00:25:14.799 - 00:25:16.310` | okay and |
| `00:25:16.320 - 00:25:19.269` | so that is shown here |
| `00:25:19.279 - 00:25:21.590` | i didn't actually draw a little uh |
| `00:25:21.600 - 00:25:23.990` | pointer to a page like i did down here |
| `00:25:24.000 - 00:25:25.750` | but it's the same idea we've got a |
| `00:25:25.760 - 00:25:28.070` | pointer to the stack page for this |
| `00:25:28.080 - 00:25:29.269` | kernel |
| `00:25:29.279 - 00:25:30.149` | and |
| `00:25:30.159 - 00:25:31.990` | here we are |
| `00:25:32.000 - 00:25:36.310` | initializing the register to point to |
| `00:25:36.320 - 00:25:38.149` | the address just beyond that page so |
| `00:25:38.159 - 00:25:40.830` | that is effectively an empty |
| `00:25:40.840 - 00:25:45.190` | stack and then we |
| `00:25:45.200 - 00:25:47.510` | initialize the |
| `00:25:47.520 - 00:25:51.029` | trap field to point to the |
| `00:25:51.039 - 00:25:53.430` | user trap |
| `00:25:53.440 - 00:25:54.870` | function |
| `00:25:54.880 - 00:25:58.149` | and that is the c code that we saw and |
| `00:25:58.159 - 00:25:59.909` | finally we are |
| `00:25:59.919 - 00:26:01.590` | getting the number of the current core |
| `00:26:01.600 - 00:26:03.430` | by reading the current |
| `00:26:03.440 - 00:26:05.669` | thread pointer register |
| `00:26:05.679 - 00:26:08.470` | remember we did a yield so it's possible |
| `00:26:08.480 - 00:26:10.070` | that we're not running on the same core |
| `00:26:10.080 - 00:26:11.110` | anymore |
| `00:26:11.120 - 00:26:13.430` | so we need to retrieve the core number |
| `00:26:13.440 - 00:26:14.470` | of the |
| `00:26:14.480 - 00:26:16.630` | of the core that we are running on and |
| `00:26:16.640 - 00:26:22.230` | we store it in the field called heart id |
| `00:26:22.240 - 00:26:25.669` | now we're going to go back to the user |
| `00:26:25.679 - 00:26:28.070` | code with the srett instruction |
| `00:26:28.080 - 00:26:29.350` | and |
| `00:26:29.360 - 00:26:31.510` | so we need to prepare for the srat |
| `00:26:31.520 - 00:26:32.710` | instruction |
| `00:26:32.720 - 00:26:34.950` | the sred instruction will use the status |
| `00:26:34.960 - 00:26:36.630` | register so |
| `00:26:36.640 - 00:26:39.110` | here what we're doing is we're we're |
| `00:26:39.120 - 00:26:40.230` | telling it |
| `00:26:40.240 - 00:26:42.390` | that the previous privilege mode was |
| `00:26:42.400 - 00:26:45.110` | user mode so we're clearing this bit |
| `00:26:45.120 - 00:26:47.430` | and so the srep instruction will then |
| `00:26:47.440 - 00:26:49.830` | return us to the previous privilege mode |
| `00:26:49.840 - 00:26:51.190` | that is it will |
| `00:26:51.200 - 00:26:53.110` | change us into user mode |
| `00:26:53.120 - 00:26:54.789` | we're also saying that the previous |
| `00:26:54.799 - 00:26:56.470` | interrupt enable bit |
| `00:26:56.480 - 00:26:59.110` | um was enabled okay so that when the |
| `00:26:59.120 - 00:27:02.390` | s-red instruction occurs it will enable |
| `00:27:02.400 - 00:27:04.710` | interrupts |
| `00:27:04.720 - 00:27:07.510` | and we then |
| `00:27:07.520 - 00:27:08.549` | write that |
| `00:27:08.559 - 00:27:11.110` | into the status register okay we we read |
| `00:27:11.120 - 00:27:13.350` | the status register here update it and |
| `00:27:13.360 - 00:27:15.190` | write it back |
| `00:27:15.200 - 00:27:17.510` | the srd instruction will also use the |
| `00:27:17.520 - 00:27:20.070` | scpc register to restore the program |
| `00:27:20.080 - 00:27:21.110` | counters |
| `00:27:21.120 - 00:27:22.470` | for the user mode |
| `00:27:22.480 - 00:27:25.350` | program so we are retrieving the value |
| `00:27:25.360 - 00:27:26.870` | that we saved |
| `00:27:26.880 - 00:27:28.470` | here |
| `00:27:28.480 - 00:27:30.470` | and we're moving it into |
| `00:27:30.480 - 00:27:32.070` | the |
| `00:27:32.080 - 00:27:35.350` | epc register |
| `00:27:35.360 - 00:27:36.549` | and then |
| `00:27:36.559 - 00:27:38.630` | we have a variable satp this is a |
| `00:27:38.640 - 00:27:40.549` | variable not a register |
| `00:27:40.559 - 00:27:41.590` | and |
| `00:27:41.600 - 00:27:43.590` | we are taking |
| `00:27:43.600 - 00:27:46.070` | the pointer to the current page table |
| `00:27:46.080 - 00:27:47.350` | for this user |
| `00:27:47.360 - 00:27:49.830` | to the page table for this user process |
| `00:27:49.840 - 00:27:51.669` | okay so we've got this field page table |
| `00:27:51.679 - 00:27:53.269` | in the proc structure that points to the |
| `00:27:53.279 - 00:27:57.430` | page table |
| `00:27:57.440 - 00:27:59.510` | and we are essentially storing the |
| `00:27:59.520 - 00:28:01.590` | address of the page table in this |
| `00:28:01.600 - 00:28:03.029` | variable |
| `00:28:03.039 - 00:28:04.070` | however |
| `00:28:04.080 - 00:28:06.389` | the satp register |
| `00:28:06.399 - 00:28:09.590` | has sort of a couple of fields in it and |
| `00:28:09.600 - 00:28:12.149` | make satp is a |
| `00:28:12.159 - 00:28:14.950` | preprocessor preprocessor macro that is |
| `00:28:14.960 - 00:28:17.110` | past the address of the page table |
| `00:28:17.120 - 00:28:19.909` | and it will shift out the offset bits |
| `00:28:19.919 - 00:28:21.029` | and it will |
| `00:28:21.039 - 00:28:23.110` | set some bits in the upper |
| `00:28:23.120 - 00:28:25.750` | field to indicate that we are using risk |
| `00:28:25.760 - 00:28:27.830` | 5 sv 39 mode |
| `00:28:27.840 - 00:28:29.350` | you know a few details that are related |
| `00:28:29.360 - 00:28:31.110` | to the hardware but you can think of it |
| `00:28:31.120 - 00:28:32.870` | as essentially grabbing a pointer to the |
| `00:28:32.880 - 00:28:36.310` | user's page table |
| `00:28:36.320 - 00:28:37.269` | and |
| `00:28:37.279 - 00:28:39.830` | finally we've got this thing down here |
| `00:28:39.840 - 00:28:41.590` | let me try to parse this for you a |
| `00:28:41.600 - 00:28:43.269` | little bit |
| `00:28:43.279 - 00:28:44.710` | what we're doing here |
| `00:28:44.720 - 00:28:47.590` | in the second line is calling a function |
| `00:28:47.600 - 00:28:51.110` | okay and we're passing it to arguments |
| `00:28:51.120 - 00:28:52.549` | so what is the function that we're |
| `00:28:52.559 - 00:28:53.750` | calling |
| `00:28:53.760 - 00:28:54.710` | well |
| `00:28:54.720 - 00:28:55.909` | we are |
| `00:28:55.919 - 00:28:57.669` | determining it right here |
| `00:28:57.679 - 00:28:58.789` | and |
| `00:28:58.799 - 00:29:01.190` | it is the user ret code or more |
| `00:29:01.200 - 00:29:02.470` | precisely |
| `00:29:02.480 - 00:29:04.389` | user rep minus trampoline is the offset |
| `00:29:04.399 - 00:29:06.710` | within the trampoline page of the user |
| `00:29:06.720 - 00:29:08.070` | rep code |
| `00:29:08.080 - 00:29:10.630` | and then we're adding it to the address |
| `00:29:10.640 - 00:29:12.789` | of the highest page in memory the |
| `00:29:12.799 - 00:29:15.510` | trampoline page and so this gives us our |
| `00:29:15.520 - 00:29:17.430` | address and remember that trampoline |
| `00:29:17.440 - 00:29:19.909` | page is mapped into all address spaces |
| `00:29:19.919 - 00:29:21.350` | um |
| `00:29:21.360 - 00:29:23.590` | we are executing in the kernel's address |
| `00:29:23.600 - 00:29:25.669` | space so that's fine it's also mapped |
| `00:29:25.679 - 00:29:28.310` | into the user's address space but so it |
| `00:29:28.320 - 00:29:30.630` | doesn't matter which what the address |
| `00:29:30.640 - 00:29:32.230` | it doesn't matter what value of essay of |
| `00:29:32.240 - 00:29:34.470` | the satp register we have |
| `00:29:34.480 - 00:29:36.230` | but in any case we get the pointer to |
| `00:29:36.240 - 00:29:39.269` | the user rep code and then we call it |
| `00:29:39.279 - 00:29:40.870` | and this |
| `00:29:40.880 - 00:29:42.389` | casting thing |
| `00:29:42.399 - 00:29:45.669` | here is basically uh the way of telling |
| `00:29:45.679 - 00:29:49.269` | the compiler that we that fn is a |
| `00:29:49.279 - 00:29:51.190` | uh pointer to a function |
| `00:29:51.200 - 00:29:52.870` | okay that returns void |
| `00:29:52.880 - 00:29:55.669` | and has two arguments okay the two |
| `00:29:55.679 - 00:29:59.269` | arguments are trap frame the pointer uh |
| `00:29:59.279 - 00:30:00.710` | the address of the second highest page |
| `00:30:00.720 - 00:30:03.190` | in memory and |
| `00:30:03.200 - 00:30:06.149` | the value that we will need to be |
| `00:30:06.159 - 00:30:09.190` | storing into the satp register |
| `00:30:09.200 - 00:30:11.750` | so now let's look at |
| `00:30:11.760 - 00:30:16.070` | user ret in |
| `00:30:16.080 - 00:30:17.750` | the |
| `00:30:17.760 - 00:30:21.110` | trampoline page |
| `00:30:21.120 - 00:30:23.190` | okay so here we go |
| `00:30:23.200 - 00:30:24.070` | uh |
| `00:30:24.080 - 00:30:25.750` | there we go |
| `00:30:25.760 - 00:30:28.149` | and |
| `00:30:28.159 - 00:30:29.909` | the comment here tells us that we are |
| `00:30:29.919 - 00:30:32.630` | calling it |
| `00:30:32.640 - 00:30:33.830` | with uh |
| `00:30:33.840 - 00:30:35.350` | two arguments |
| `00:30:35.360 - 00:30:37.190` | the risk five will |
| `00:30:37.200 - 00:30:38.870` | implement the call or the c compiler |
| `00:30:38.880 - 00:30:40.950` | will implement the call by moving |
| `00:30:40.960 - 00:30:44.310` | the two arguments into a0 and a1 |
| `00:30:44.320 - 00:30:46.149` | we'll also save the return address in |
| `00:30:46.159 - 00:30:49.510` | register r a the return address register |
| `00:30:49.520 - 00:30:51.830` | however we'll never be returning so we |
| `00:30:51.840 - 00:30:54.070` | don't care about that |
| `00:30:54.080 - 00:30:55.990` | and |
| `00:30:56.000 - 00:31:00.630` | at this point |
| `00:31:00.640 - 00:31:02.830` | a1 contains |
| `00:31:02.840 - 00:31:05.509` | the pointer to the page table for the |
| `00:31:05.519 - 00:31:08.870` | user address space |
| `00:31:08.880 - 00:31:11.830` | we are writing into the cs register |
| `00:31:11.840 - 00:31:13.350` | uh the control and status register |
| `00:31:13.360 - 00:31:16.630` | called satp that value here so now we |
| `00:31:16.640 - 00:31:19.590` | are switched into the user's address |
| `00:31:19.600 - 00:31:21.990` | space |
| `00:31:22.000 - 00:31:23.990` | we do the fence here |
| `00:31:24.000 - 00:31:24.950` | and |
| `00:31:24.960 - 00:31:26.789` | a0 points to |
| `00:31:26.799 - 00:31:28.549` | the trap frame page |
| `00:31:28.559 - 00:31:33.190` | okay so we can make use of a0 here so |
| `00:31:33.200 - 00:31:35.509` | the first thing we do is we grab |
| `00:31:35.519 - 00:31:37.190` | the |
| `00:31:37.200 - 00:31:39.909` | user's value for register a0 |
| `00:31:39.919 - 00:31:42.310` | that was stored at offset 112. and we |
| `00:31:42.320 - 00:31:44.870` | moved that into register t0 so we loaded |
| `00:31:44.880 - 00:31:46.549` | into register t0 |
| `00:31:46.559 - 00:31:49.269` | and then we write it into the scratch |
| `00:31:49.279 - 00:31:51.110` | register |
| `00:31:51.120 - 00:31:53.029` | so now the scratch register contains the |
| `00:31:53.039 - 00:31:55.909` | users a0 |
| `00:31:55.919 - 00:31:57.909` | whereas a0 contains a pointer to the |
| `00:31:57.919 - 00:31:59.190` | trap frame |
| `00:31:59.200 - 00:32:01.509` | and then we load all of the registers |
| `00:32:01.519 - 00:32:03.830` | that were previously saved |
| `00:32:03.840 - 00:32:04.789` | except |
| `00:32:04.799 - 00:32:07.830` | a0 here at offset 112. |
| `00:32:07.840 - 00:32:10.870` | okay we load all the registers |
| `00:32:10.880 - 00:32:14.710` | and then continuing on |
| `00:32:14.720 - 00:32:16.070` | at this point |
| `00:32:16.080 - 00:32:17.110` | we |
| `00:32:17.120 - 00:32:18.870` | swap the contents of the scratch |
| `00:32:18.880 - 00:32:20.549` | register and a0 |
| `00:32:20.559 - 00:32:23.029` | so now a0 |
| `00:32:23.039 - 00:32:25.190` | is the user's value which is what we |
| `00:32:25.200 - 00:32:27.669` | need and the scratch register |
| `00:32:27.679 - 00:32:29.509` | contains a pointer to the trap frame |
| `00:32:29.519 - 00:32:32.149` | which we will need to have which we |
| `00:32:32.159 - 00:32:36.070` | expect to have when the next trap occurs |
| `00:32:36.080 - 00:32:37.669` | and so finally we're ready to do the |
| `00:32:37.679 - 00:32:39.350` | esret instruction |
| `00:32:39.360 - 00:32:42.549` | so wrapping it up and going back to my |
| `00:32:42.559 - 00:32:47.269` | image i call the road map |
| `00:32:47.279 - 00:32:49.430` | so we're down here i just want to make a |
| `00:32:49.440 - 00:32:53.269` | note about this i i kind of um |
| `00:32:53.279 - 00:32:54.549` | did things |
| `00:32:54.559 - 00:32:56.310` | in the order that i thought made logical |
| `00:32:56.320 - 00:32:59.350` | sense the s status register was actually |
| `00:32:59.360 - 00:33:03.110` | pre-loaded up in user trap rat |
| `00:33:03.120 - 00:33:05.190` | but in any case you can see what we've |
| `00:33:05.200 - 00:33:08.230` | done we've disabled interrupts |
| `00:33:08.240 - 00:33:09.430` | we've |
| `00:33:09.440 - 00:33:12.149` | restored st back to point to |
| `00:33:12.159 - 00:33:14.549` | the uservac code namely |
| `00:33:14.559 - 00:33:18.950` | this code up here |
| `00:33:18.960 - 00:33:19.909` | um |
| `00:33:19.919 - 00:33:21.909` | we haven't really saved the uh we've |
| `00:33:21.919 - 00:33:25.590` | been we've set up sp and tp |
| `00:33:25.600 - 00:33:28.950` | so that they will be usable when we have |
| `00:33:28.960 - 00:33:30.230` | the next trap |
| `00:33:30.240 - 00:33:33.509` | and we loaded the scpc register and then |
| `00:33:33.519 - 00:33:35.990` | we |
| `00:33:36.000 - 00:33:38.389` | set the satp register to point to the |
| `00:33:38.399 - 00:33:40.549` | user's page table so now we're executing |
| `00:33:40.559 - 00:33:43.190` | in the user's virtual address space we |
| `00:33:43.200 - 00:33:46.310` | restored all the user registers we |
| `00:33:46.320 - 00:33:49.110` | loaded the status register or we |
| `00:33:49.120 - 00:33:51.350` | basically changed these bits with that |
| `00:33:51.360 - 00:33:52.389` | up here actually |
| `00:33:52.399 - 00:33:53.909` | and then finally we're ready to execute |
| `00:33:53.919 - 00:33:55.669` | the s red and so |
| `00:33:55.679 - 00:33:57.430` | that will then |
| `00:33:57.440 - 00:33:59.590` | change the program counter back to what |
| `00:33:59.600 - 00:34:02.630` | it was and we will continue |
| `00:34:02.640 - 00:34:04.950` | and it will change the mode to user mode |
| `00:34:04.960 - 00:34:07.350` | and enable interrupts so now we're right |
| `00:34:07.360 - 00:34:09.270` | back where we left off when the traffic |
| `00:34:09.280 - 00:34:11.109` | when the trap occurred up here and we're |
| `00:34:11.119 - 00:34:13.589` | ready for the next trap |
| `00:34:13.599 - 00:34:14.470` | okay |
| `00:34:14.480 - 00:34:16.869` | that's it for the trampoline page see |
| `00:34:16.879 - 00:34:20.520` | you in the next video |
