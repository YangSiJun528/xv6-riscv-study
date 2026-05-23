# xv6 Kernel-3: Startup + Organization

| Field | Value |
| --- | --- |
| Video ID | `6hhJ6JN95As` |
| URL | <https://www.youtube.com/watch?v=6hhJ6JN95As> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.280 - 00:00:03.030` | this video is part of a series on the |
| `00:00:03.040 - 00:00:05.910` | xv6 operating system kernel |
| `00:00:05.920 - 00:00:08.390` | in this video i'm going to introduce the |
| `00:00:08.400 - 00:00:10.310` | main function and talk a little bit |
| `00:00:10.320 - 00:00:12.549` | about the startup procedure |
| `00:00:12.559 - 00:00:14.629` | before that i'm going to go over the |
| `00:00:14.639 - 00:00:17.510` | file organization and |
| `00:00:17.520 - 00:00:20.310` | i'll end by going over a couple of small |
| `00:00:20.320 - 00:00:23.670` | files that we can take care of quickly |
| `00:00:23.680 - 00:00:26.230` | let's begin with the files that are |
| `00:00:26.240 - 00:00:28.870` | included with the xv6 system |
| `00:00:28.880 - 00:00:32.790` | uh here i've listed out all of the files |
| `00:00:32.800 - 00:00:34.470` | so you can see they're not too many of |
| `00:00:34.480 - 00:00:35.910` | them |
| `00:00:35.920 - 00:00:37.190` | the organization is pretty |
| `00:00:37.200 - 00:00:39.350` | straightforward you've got |
| `00:00:39.360 - 00:00:40.630` | two directories |
| `00:00:40.640 - 00:00:42.790` | kernel and user |
| `00:00:42.800 - 00:00:45.110` | these are the files that are in the |
| `00:00:45.120 - 00:00:47.029` | kernel directory |
| `00:00:47.039 - 00:00:49.350` | basically it's just a bunch of c code |
| `00:00:49.360 - 00:00:51.990` | files some header files |
| `00:00:52.000 - 00:00:53.910` | there are several assembly language |
| `00:00:53.920 - 00:00:55.189` | files |
| `00:00:55.199 - 00:00:58.470` | there's entry.s which i'll discuss base |
| `00:00:58.480 - 00:00:59.590` | a little bit |
| `00:00:59.600 - 00:01:01.270` | colonelvac |
| `00:01:01.280 - 00:01:02.590` | there is |
| `00:01:02.600 - 00:01:05.670` | switch.s very interesting one |
| `00:01:05.680 - 00:01:07.830` | trampoline.s |
| `00:01:07.840 - 00:01:11.830` | there's also initcode.s |
| `00:01:11.840 - 00:01:14.230` | there's also a |
| `00:01:14.240 - 00:01:18.469` | file here that's used by the linker |
| `00:01:18.479 - 00:01:20.870` | as well |
| `00:01:20.880 - 00:01:24.230` | in the user directory you have |
| `00:01:24.240 - 00:01:27.190` | the code for the initial process |
| `00:01:27.200 - 00:01:29.990` | as well as the code for the user |
| `00:01:30.000 - 00:01:32.550` | application programs the shell |
| `00:01:32.560 - 00:01:35.350` | and user mode programs like cat echo and |
| `00:01:35.360 - 00:01:36.390` | so on |
| `00:01:36.400 - 00:01:37.830` | so |
| `00:01:37.840 - 00:01:40.310` | in addition uh you've got the make file |
| `00:01:40.320 - 00:01:42.950` | to build all these files |
| `00:01:42.960 - 00:01:44.950` | and you've got a readme in a license |
| `00:01:44.960 - 00:01:46.870` | file so it's pretty straightforward |
| `00:01:46.880 - 00:01:51.030` | organization |
| `00:01:51.040 - 00:01:53.670` | the x36 system runs on a multi-core |
| `00:01:53.680 - 00:01:55.109` | computer |
| `00:01:55.119 - 00:01:56.709` | and |
| `00:01:56.719 - 00:01:58.310` | when it begins |
| `00:01:58.320 - 00:02:01.030` | each core will start executing all at |
| `00:02:01.040 - 00:02:03.429` | once they all begin at the same time |
| `00:02:03.439 - 00:02:06.069` | it's a shared memory system so all cores |
| `00:02:06.079 - 00:02:09.350` | will share the exact same memory |
| `00:02:09.360 - 00:02:11.270` | and they will all begin executing the |
| `00:02:11.280 - 00:02:14.550` | same exact code and that code is in a |
| `00:02:14.560 - 00:02:17.910` | file called entry dot s |
| `00:02:17.920 - 00:02:19.110` | it's not a very |
| `00:02:19.120 - 00:02:21.430` | long file just a few lines |
| `00:02:21.440 - 00:02:24.630` | and that code will then transfer control |
| `00:02:24.640 - 00:02:28.229` | to a c function called start in the file |
| `00:02:28.239 - 00:02:29.990` | start.c |
| `00:02:30.000 - 00:02:32.309` | and that will then transfer |
| `00:02:32.319 - 00:02:38.070` | control to the main function |
| `00:02:38.080 - 00:02:38.949` | the |
| `00:02:38.959 - 00:02:42.869` | assembly code that's in the entry file |
| `00:02:42.879 - 00:02:44.150` | basically |
| `00:02:44.160 - 00:02:45.830` | gets things set up so that we can |
| `00:02:45.840 - 00:02:49.110` | execute c programs |
| `00:02:49.120 - 00:02:50.470` | it will |
| `00:02:50.480 - 00:02:52.949` | initialize the stack pointer register |
| `00:02:52.959 - 00:02:55.750` | the sp register |
| `00:02:55.760 - 00:03:00.070` | and it will initialize the tp register |
| `00:03:00.080 - 00:03:02.229` | each core will |
| `00:03:02.239 - 00:03:04.949` | share main memory so they will be |
| `00:03:04.959 - 00:03:06.550` | accessing the same set of global |
| `00:03:06.560 - 00:03:07.750` | variables |
| `00:03:07.760 - 00:03:08.630` | but |
| `00:03:08.640 - 00:03:11.830` | each core will need its own stack they |
| `00:03:11.840 - 00:03:14.550` | can't overlap that wouldn't work at all |
| `00:03:14.560 - 00:03:15.430` | so |
| `00:03:15.440 - 00:03:17.670` | there's a separate stack space for each |
| `00:03:17.680 - 00:03:19.030` | of the course |
| `00:03:19.040 - 00:03:22.229` | and the code and entry.s will |
| `00:03:22.239 - 00:03:24.789` | initialize the stack pointer register |
| `00:03:24.799 - 00:03:28.390` | for the core appropriately |
| `00:03:28.400 - 00:03:29.509` | also |
| `00:03:29.519 - 00:03:31.750` | there's a tp register that stands for |
| `00:03:31.760 - 00:03:34.390` | thread pointer but the tp register |
| `00:03:34.400 - 00:03:36.309` | actually will contain |
| `00:03:36.319 - 00:03:38.869` | the core number the number of the core 0 |
| `00:03:38.879 - 00:03:40.630` | 1 2 or so on |
| `00:03:40.640 - 00:03:43.190` | instead of some sort of a thread pointer |
| `00:03:43.200 - 00:03:44.309` | and |
| `00:03:44.319 - 00:03:46.949` | that register will stay constant on that |
| `00:03:46.959 - 00:03:50.470` | core throughout so that allows the code |
| `00:03:50.480 - 00:03:53.270` | to ask at any time what core am i |
| `00:03:53.280 - 00:03:54.630` | running on |
| `00:03:54.640 - 00:03:56.789` | so once those are set up |
| `00:03:56.799 - 00:03:59.670` | we transfer control to the start |
| `00:03:59.680 - 00:04:01.429` | function |
| `00:04:01.439 - 00:04:02.789` | in another video i talk about the |
| `00:04:02.799 - 00:04:04.550` | different modes that the risc-5 |
| `00:04:04.560 - 00:04:06.470` | processor can execute in |
| `00:04:06.480 - 00:04:08.630` | it can execute in machine mode |
| `00:04:08.640 - 00:04:11.190` | supervisor mode and user mode |
| `00:04:11.200 - 00:04:13.589` | the all of the kernel runs in supervisor |
| `00:04:13.599 - 00:04:16.229` | mode except for a tiny bit of code which |
| `00:04:16.239 - 00:04:18.710` | is in this start.c file |
| `00:04:18.720 - 00:04:20.789` | and that so initially |
| `00:04:20.799 - 00:04:22.230` | when the |
| `00:04:22.240 - 00:04:23.350` | system |
| `00:04:23.360 - 00:04:26.469` | begins execution |
| `00:04:26.479 - 00:04:29.909` | it begins executing in machine mode and |
| `00:04:29.919 - 00:04:32.950` | the code here will take care of a few |
| `00:04:32.960 - 00:04:35.189` | bookkeeping things and then switch to |
| `00:04:35.199 - 00:04:36.710` | supervisor mode |
| `00:04:36.720 - 00:04:38.150` | and the |
| `00:04:38.160 - 00:04:40.550` | cores will remain in supervisor mode |
| `00:04:40.560 - 00:04:43.909` | after that okay now let's take a look at |
| `00:04:43.919 - 00:04:46.310` | the main function |
| `00:04:46.320 - 00:04:50.070` | here is the code for main.c |
| `00:04:50.080 - 00:04:51.350` | it's not |
| `00:04:51.360 - 00:04:53.510` | very long and it contains nothing more |
| `00:04:53.520 - 00:04:56.710` | than the main function |
| `00:04:56.720 - 00:04:57.749` | so let's |
| `00:04:57.759 - 00:04:59.670` | see what happens |
| `00:04:59.680 - 00:05:01.830` | remember that each core will begin |
| `00:05:01.840 - 00:05:04.710` | executing this code in parallel so there |
| `00:05:04.720 - 00:05:06.950` | all cores will begin |
| `00:05:06.960 - 00:05:09.029` | with this if statement |
| `00:05:09.039 - 00:05:11.830` | cpu id is a short function that |
| `00:05:11.840 - 00:05:13.430` | basically looks at |
| `00:05:13.440 - 00:05:16.790` | and returns the value of the tp register |
| `00:05:16.800 - 00:05:18.230` | so |
| `00:05:18.240 - 00:05:21.029` | on core 0 this function will return 0 |
| `00:05:21.039 - 00:05:23.590` | and core 0 will then execute this code |
| `00:05:23.600 - 00:05:24.870` | here |
| `00:05:24.880 - 00:05:26.390` | all other cores |
| `00:05:26.400 - 00:05:28.070` | will |
| `00:05:28.080 - 00:05:31.670` | execute this code here instead |
| `00:05:31.680 - 00:05:33.110` | so what does the |
| `00:05:33.120 - 00:05:36.310` | code do well core 0 is tasked with |
| `00:05:36.320 - 00:05:38.629` | initializing things so you see a lot of |
| `00:05:38.639 - 00:05:41.510` | calls to init and it annette this and |
| `00:05:41.520 - 00:05:44.150` | that that and then knit some other stuff |
| `00:05:44.160 - 00:05:47.029` | it also prints out this message that uh |
| `00:05:47.039 - 00:05:51.990` | the kernel is booting |
| `00:05:52.000 - 00:05:54.230` | there is a global variable or a shared |
| `00:05:54.240 - 00:05:56.150` | variable here it's in the memory space |
| `00:05:56.160 - 00:05:58.070` | so of course all cores will have access |
| `00:05:58.080 - 00:05:59.029` | to it |
| `00:05:59.039 - 00:06:01.350` | and it's used for synchronization |
| `00:06:01.360 - 00:06:02.950` | this keyword here |
| `00:06:02.960 - 00:06:06.950` | volatile is a little bit of c magic that |
| `00:06:06.960 - 00:06:10.550` | says that this variable is used for |
| `00:06:10.560 - 00:06:12.390` | synchronization possibly by multiple |
| `00:06:12.400 - 00:06:14.790` | cores or concurrent threads |
| `00:06:14.800 - 00:06:18.469` | and it is in fact used to control |
| `00:06:18.479 - 00:06:19.510` | the |
| `00:06:19.520 - 00:06:21.830` | begin beginning of the other |
| `00:06:21.840 - 00:06:23.909` | course so |
| `00:06:23.919 - 00:06:25.990` | it is initialized to |
| `00:06:26.000 - 00:06:29.270` | zero or false if you will and |
| `00:06:29.280 - 00:06:31.670` | once core zero is done initializing it |
| `00:06:31.680 - 00:06:33.270` | will change it to |
| `00:06:33.280 - 00:06:34.870` | true |
| `00:06:34.880 - 00:06:37.189` | all the other cores go into this tight |
| `00:06:37.199 - 00:06:39.110` | loop where they're testing it |
| `00:06:39.120 - 00:06:41.590` | and they keep testing until it is found |
| `00:06:41.600 - 00:06:43.909` | to be true and then they execute this |
| `00:06:43.919 - 00:06:46.870` | code here and they start out by printing |
| `00:06:46.880 - 00:06:48.469` | heart |
| `00:06:48.479 - 00:06:50.790` | something is starting |
| `00:06:50.800 - 00:06:53.270` | the term heart is synonymous with core |
| `00:06:53.280 - 00:06:54.950` | at least for these videos |
| `00:06:54.960 - 00:06:57.350` | and so basically saying core one is |
| `00:06:57.360 - 00:06:59.589` | starting core two is starting and so on |
| `00:06:59.599 - 00:07:02.390` | and they are pulling up their own core |
| `00:07:02.400 - 00:07:06.790` | or cpu id right here |
| `00:07:06.800 - 00:07:09.909` | the last thing that core zero does is it |
| `00:07:09.919 - 00:07:11.830` | starts the |
| `00:07:11.840 - 00:07:13.270` | code for |
| `00:07:13.280 - 00:07:15.430` | the init process so that's what's going |
| `00:07:15.440 - 00:07:16.950` | on here |
| `00:07:16.960 - 00:07:17.990` | and |
| `00:07:18.000 - 00:07:21.749` | then it sets started to one and then the |
| `00:07:21.759 - 00:07:23.430` | other |
| `00:07:23.440 - 00:07:24.950` | product the other course will do some |
| `00:07:24.960 - 00:07:27.029` | initialization here |
| `00:07:27.039 - 00:07:28.790` | km in it |
| `00:07:28.800 - 00:07:32.390` | trapping it click init these are per |
| `00:07:32.400 - 00:07:34.150` | core initialization things and they |
| `00:07:34.160 - 00:07:37.990` | happen in core zero as well k a k v m uh |
| `00:07:38.000 - 00:07:39.830` | trapping it |
| `00:07:39.840 - 00:07:42.150` | and clicking it happen here for core |
| `00:07:42.160 - 00:07:43.670` | zero |
| `00:07:43.680 - 00:07:44.629` | once |
| `00:07:44.639 - 00:07:47.589` | all that happens on all of the cores |
| `00:07:47.599 - 00:07:50.390` | each core will then call the scheduler |
| `00:07:50.400 - 00:07:51.430` | function |
| `00:07:51.440 - 00:07:53.990` | and what scheduler will do is we'll look |
| `00:07:54.000 - 00:07:56.790` | for a process to execute so at that |
| `00:07:56.800 - 00:07:59.670` | point all the cores will start executing |
| `00:07:59.680 - 00:08:03.430` | processes |
| `00:08:03.440 - 00:08:07.589` | we also see this sync synchronize |
| `00:08:07.599 - 00:08:09.670` | function here this is again a little bit |
| `00:08:09.680 - 00:08:10.469` | of |
| `00:08:10.479 - 00:08:13.110` | compiler magic compilers will try to |
| `00:08:13.120 - 00:08:16.230` | optimize things and what this is |
| `00:08:16.240 - 00:08:19.029` | doing is telling the compiler to chill |
| `00:08:19.039 - 00:08:21.350` | out and not do that optimization |
| `00:08:21.360 - 00:08:22.950` | the compiler |
| `00:08:22.960 - 00:08:23.749` | might |
| `00:08:23.759 - 00:08:26.390` | rearrange code in order to |
| `00:08:26.400 - 00:08:28.390` | try to achieve greater performance and |
| `00:08:28.400 - 00:08:29.510` | efficiency |
| `00:08:29.520 - 00:08:31.990` | what the synchronize does is tell the |
| `00:08:32.000 - 00:08:35.029` | compiler make sure you finish everything |
| `00:08:35.039 - 00:08:36.070` | above it |
| `00:08:36.080 - 00:08:38.870` | first before you start anything after it |
| `00:08:38.880 - 00:08:40.630` | so it's saying |
| `00:08:40.640 - 00:08:42.870` | please finish all of this initialization |
| `00:08:42.880 - 00:08:46.070` | before you change this variable to one |
| `00:08:46.080 - 00:08:47.269` | you may not |
| `00:08:47.279 - 00:08:48.710` | be able to understand what i'm doing |
| `00:08:48.720 - 00:08:51.269` | here you're saying to the compiler but |
| `00:08:51.279 - 00:08:53.990` | i'm telling you you need to finish doing |
| `00:08:54.000 - 00:08:56.550` | every single thing above this before |
| `00:08:56.560 - 00:08:58.230` | you think about changing that variable |
| `00:08:58.240 - 00:08:59.750` | to one |
| `00:08:59.760 - 00:09:02.150` | likewise down here |
| `00:09:02.160 - 00:09:05.350` | it's telling the compiler the same thing |
| `00:09:05.360 - 00:09:07.990` | don't start executing this stuff until |
| `00:09:08.000 - 00:09:12.470` | you have completed this while loop here |
| `00:09:12.480 - 00:09:14.070` | okay there are |
| `00:09:14.080 - 00:09:16.630` | several small files that |
| `00:09:16.640 - 00:09:18.389` | i want to take a quick look at and get |
| `00:09:18.399 - 00:09:21.269` | these out of the way |
| `00:09:21.279 - 00:09:23.590` | types.h |
| `00:09:23.600 - 00:09:26.870` | contains just these type defs here |
| `00:09:26.880 - 00:09:29.030` | you're probably familiar with uh these |
| `00:09:29.040 - 00:09:31.110` | abbreviations for familiar |
| `00:09:31.120 - 00:09:32.230` | types |
| `00:09:32.240 - 00:09:33.030` | so |
| `00:09:33.040 - 00:09:35.350` | on the architecture we're using the risk |
| `00:09:35.360 - 00:09:37.670` | 5 architecture with the tool chain it's |
| `00:09:37.680 - 00:09:40.389` | a 64-bit machine integers will be 32 |
| `00:09:40.399 - 00:09:43.430` | bits |
| `00:09:43.440 - 00:09:45.190` | so we define |
| `00:09:45.200 - 00:09:48.630` | unsigned 8-bit values unsigned 16-bit |
| `00:09:48.640 - 00:09:50.949` | values unsigned 32-bit values and |
| `00:09:50.959 - 00:09:53.350` | unsigned 64-bit values |
| `00:09:53.360 - 00:09:57.590` | we use this type uint 64 quite a bit for |
| `00:09:57.600 - 00:10:00.470` | addresses and pointers |
| `00:10:00.480 - 00:10:02.870` | okay next file i want to look at is |
| `00:10:02.880 - 00:10:04.949` | param dot h |
| `00:10:04.959 - 00:10:06.790` | there are a number of things that are |
| `00:10:06.800 - 00:10:10.069` | hard coded into the kernel so let me |
| `00:10:10.079 - 00:10:12.870` | just go through these quickly |
| `00:10:12.880 - 00:10:15.590` | in proc the maximum number of processes |
| `00:10:15.600 - 00:10:19.990` | is just set to 64. the number of cores |
| `00:10:20.000 - 00:10:21.509` | is eight |
| `00:10:21.519 - 00:10:23.590` | then you have other things |
| `00:10:23.600 - 00:10:26.150` | the number of open files the number of |
| `00:10:26.160 - 00:10:28.470` | open files per system |
| `00:10:28.480 - 00:10:30.310` | number of inodes |
| `00:10:30.320 - 00:10:32.389` | number of devices |
| `00:10:32.399 - 00:10:34.710` | the device number root dev |
| `00:10:34.720 - 00:10:37.750` | max argument uh this is the |
| `00:10:37.760 - 00:10:39.269` | maximum number of arguments that you |
| `00:10:39.279 - 00:10:40.710` | could have on a |
| `00:10:40.720 - 00:10:43.509` | on an exact system call |
| `00:10:43.519 - 00:10:48.069` | max op blocks log size in buff fs size |
| `00:10:48.079 - 00:10:50.150` | max path that's the |
| `00:10:50.160 - 00:10:52.389` | maximum number of characters in a |
| `00:10:52.399 - 00:10:55.509` | files path name |
| `00:10:55.519 - 00:10:57.990` | finally i want to look at a file called |
| `00:10:58.000 - 00:11:00.230` | desk.h |
| `00:11:00.240 - 00:11:02.470` | and this uh |
| `00:11:02.480 - 00:11:04.069` | is |
| `00:11:04.079 - 00:11:06.470` | listed out here on these four pages so |
| `00:11:06.480 - 00:11:08.550` | you can see what's going on but |
| `00:11:08.560 - 00:11:10.630` | basically this is just for the compiler |
| `00:11:10.640 - 00:11:13.269` | contains a bunch of uh |
| `00:11:13.279 - 00:11:15.829` | function prototypes so for example |
| `00:11:15.839 - 00:11:19.750` | uh the file console.c |
| `00:11:19.760 - 00:11:22.470` | contains uh at least these three |
| `00:11:22.480 - 00:11:24.790` | functions which might be used in in |
| `00:11:24.800 - 00:11:26.790` | other files as well so |
| `00:11:26.800 - 00:11:29.269` | this is basically uh |
| `00:11:29.279 - 00:11:31.269` | just the function prototypes there's |
| `00:11:31.279 - 00:11:32.150` | nothing |
| `00:11:32.160 - 00:11:34.069` | much of interest here we'll encounter |
| `00:11:34.079 - 00:11:35.430` | all these files |
| `00:11:35.440 - 00:11:36.870` | later so let's go through all these |
| `00:11:36.880 - 00:11:40.790` | pages on the last page there is |
| `00:11:40.800 - 00:11:43.269` | a preprocessor macro |
| `00:11:43.279 - 00:11:44.790` | perhaps you've seen |
| `00:11:44.800 - 00:11:47.670` | this in other contexts but this is the |
| `00:11:47.680 - 00:11:50.629` | number of elements and you would have |
| `00:11:50.639 - 00:11:52.790` | a variable here and |
| `00:11:52.800 - 00:11:55.590` | an array variable and what it does is it |
| `00:11:55.600 - 00:11:58.870` | just asks how big is the entire array |
| `00:11:58.880 - 00:12:00.150` | and |
| `00:12:00.160 - 00:12:03.269` | how big is a single element and it just |
| `00:12:03.279 - 00:12:05.030` | gives you the number of elements |
| `00:12:05.040 - 00:12:07.030` | okay that's it for this video i'll see |
| `00:12:07.040 - 00:12:10.519` | you in the next video |
