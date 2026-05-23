# xv6 Kernel-2: General Features

| Field | Value |
| --- | --- |
| Video ID | `yfqxa-TYdFU` |
| URL | <https://www.youtube.com/watch?v=yfqxa-TYdFU> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:02.080 - 00:00:05.110` | hi welcome to another video in a series |
| `00:00:05.120 - 00:00:08.629` | on the xv6 operating system kernel |
| `00:00:08.639 - 00:00:12.470` | xp6 is an educational kernel and in this |
| `00:00:12.480 - 00:00:14.629` | video i am going to talk about some |
| `00:00:14.639 - 00:00:17.910` | general features of the kernel |
| `00:00:17.920 - 00:00:19.670` | it's meant to run on a shared memory |
| `00:00:19.680 - 00:00:22.230` | multiprocessor in other words it's meant |
| `00:00:22.240 - 00:00:25.189` | to run on a system with multiple cores |
| `00:00:25.199 - 00:00:27.429` | but which all share a single range of |
| `00:00:27.439 - 00:00:29.509` | main memory |
| `00:00:29.519 - 00:00:32.229` | in this video and in the code i'm going |
| `00:00:32.239 - 00:00:35.030` | to be using the terms |
| `00:00:35.040 - 00:00:38.869` | cpu core and heart synonymously so in |
| `00:00:38.879 - 00:00:39.510` | the |
| `00:00:39.520 - 00:00:41.270` | in the code sometimes you see cpu |
| `00:00:41.280 - 00:00:43.430` | sometimes you see heart |
| `00:00:43.440 - 00:00:46.389` | and i tend to say core but they all mean |
| `00:00:46.399 - 00:00:49.029` | a hardware processor capable of |
| `00:00:49.039 - 00:00:53.110` | executing a single thread of control |
| `00:00:53.120 - 00:00:55.510` | this term heart which stands for |
| `00:00:55.520 - 00:00:56.950` | hardware thread |
| `00:00:56.960 - 00:00:58.549` | is something that i first encountered |
| `00:00:58.559 - 00:01:02.470` | when reading the risk 5 documentation |
| `00:01:02.480 - 00:01:04.869` | the idea is normally a core will execute |
| `00:01:04.879 - 00:01:07.830` | a single hardware thread but for some |
| `00:01:07.840 - 00:01:09.670` | high performance cores |
| `00:01:09.680 - 00:01:11.030` | you might have |
| `00:01:11.040 - 00:01:13.270` | say two hardware threads |
| `00:01:13.280 - 00:01:15.270` | for example with the intel hyper |
| `00:01:15.280 - 00:01:17.270` | threading architectures |
| `00:01:17.280 - 00:01:18.870` | you might have two threads running on a |
| `00:01:18.880 - 00:01:20.310` | single core |
| `00:01:20.320 - 00:01:22.710` | the idea is that these two |
| `00:01:22.720 - 00:01:24.070` | threads are being |
| `00:01:24.080 - 00:01:25.670` | interwoven the instructions are being |
| `00:01:25.680 - 00:01:27.990` | executed |
| `00:01:28.000 - 00:01:30.630` | concurrently or possibly interwoven in |
| `00:01:30.640 - 00:01:32.469` | such a way as to share some of the |
| `00:01:32.479 - 00:01:34.310` | processing hardware |
| `00:01:34.320 - 00:01:36.870` | the idea is to increase the performance |
| `00:01:36.880 - 00:01:39.590` | per transistor |
| `00:01:39.600 - 00:01:40.870` | so |
| `00:01:40.880 - 00:01:43.510` | this term heart appears uh but it for |
| `00:01:43.520 - 00:01:44.950` | our purposes we're going to be running |
| `00:01:44.960 - 00:01:47.670` | it on a system with uh |
| `00:01:47.680 - 00:01:49.190` | multiple cores |
| `00:01:49.200 - 00:01:51.830` | and so these terms are all synonymous |
| `00:01:51.840 - 00:01:54.310` | there's just a single hardware thread |
| `00:01:54.320 - 00:01:55.910` | per core |
| `00:01:55.920 - 00:01:56.950` | okay |
| `00:01:56.960 - 00:01:59.429` | main memory is shared |
| `00:01:59.439 - 00:02:02.709` | sometimes we call it ram |
| `00:02:02.719 - 00:02:03.590` | in a |
| `00:02:03.600 - 00:02:06.389` | real operating system |
| `00:02:06.399 - 00:02:08.790` | the issues regarding the caching of main |
| `00:02:08.800 - 00:02:12.790` | memory l1 l2 and so on uh are important |
| `00:02:12.800 - 00:02:13.510` | and |
| `00:02:13.520 - 00:02:16.949` | the kernel needs to sort of work around |
| `00:02:16.959 - 00:02:18.070` | the |
| `00:02:18.080 - 00:02:20.630` | caching schemes and caching system in |
| `00:02:20.640 - 00:02:22.869` | order to improve performance |
| `00:02:22.879 - 00:02:24.229` | in this kernel |
| `00:02:24.239 - 00:02:25.270` | all |
| `00:02:25.280 - 00:02:26.949` | concerns about caching are completely |
| `00:02:26.959 - 00:02:28.869` | ignored |
| `00:02:28.879 - 00:02:32.630` | the main memory is 128 megabytes and |
| `00:02:32.640 - 00:02:34.710` | this is just hardwired into the kernel's |
| `00:02:34.720 - 00:02:39.430` | code it's fixed with a pound sign define |
| `00:02:39.440 - 00:02:42.150` | a typical real world operating system |
| `00:02:42.160 - 00:02:44.470` | kernel is going to look at the amount of |
| `00:02:44.480 - 00:02:46.550` | memory that's available |
| `00:02:46.560 - 00:02:47.910` | on the machine |
| `00:02:47.920 - 00:02:49.990` | when it starts up and it's going to |
| `00:02:50.000 - 00:02:53.030` | configure itself to use all the physical |
| `00:02:53.040 - 00:02:54.790` | memory |
| `00:02:54.800 - 00:02:56.070` | but with |
| `00:02:56.080 - 00:02:57.270` | xv6 |
| `00:02:57.280 - 00:02:59.670` | it's just hardwired in with the 128 |
| `00:02:59.680 - 00:03:01.910` | megabytes |
| `00:03:01.920 - 00:03:04.949` | the xv6 system can handle a couple of |
| `00:03:04.959 - 00:03:07.030` | different devices |
| `00:03:07.040 - 00:03:08.470` | the uart |
| `00:03:08.480 - 00:03:09.270` | is |
| `00:03:09.280 - 00:03:10.630` | the device that handles serial |
| `00:03:10.640 - 00:03:12.149` | communication |
| `00:03:12.159 - 00:03:14.229` | this stands for universal asynchronous |
| `00:03:14.239 - 00:03:16.869` | receive transmit unit in the old days it |
| `00:03:16.879 - 00:03:18.550` | was a separate chip but now it's |
| `00:03:18.560 - 00:03:20.869` | integrated on the processor chip along |
| `00:03:20.879 - 00:03:23.589` | with the cores and so on |
| `00:03:23.599 - 00:03:26.070` | this is the device that provides a |
| `00:03:26.080 - 00:03:28.550` | communication channel for |
| `00:03:28.560 - 00:03:29.910` | printing and reading input from a |
| `00:03:29.920 - 00:03:31.830` | keyboard typically |
| `00:03:31.840 - 00:03:34.070` | if you program with the arduino you're |
| `00:03:34.080 - 00:03:36.229` | probably going to be familiar with this |
| `00:03:36.239 - 00:03:39.110` | essentially it can send a byte stream |
| `00:03:39.120 - 00:03:41.670` | in one direction and receive a byte |
| `00:03:41.680 - 00:03:43.350` | string coming back in the other |
| `00:03:43.360 - 00:03:46.550` | direction |
| `00:03:46.560 - 00:03:49.270` | the xv6 system |
| `00:03:49.280 - 00:03:52.470` | uh has one disk drive and that will be |
| `00:03:52.480 - 00:03:54.470` | emulated of course with the file on your |
| `00:03:54.480 - 00:03:56.309` | on your host uh laptop |
| `00:03:56.319 - 00:03:58.229` | but there's a disk uh |
| `00:03:58.239 - 00:04:00.070` | device |
| `00:04:00.080 - 00:04:02.630` | there are also timer interrupts |
| `00:04:02.640 - 00:04:05.030` | uh the uart and the disk are shared |
| `00:04:05.040 - 00:04:08.070` | between all the cores uh but each core |
| `00:04:08.080 - 00:04:11.350` | has its own timer interrupt so these are |
| `00:04:11.360 - 00:04:14.390` | local to the core |
| `00:04:14.400 - 00:04:17.270` | the processors uh in the real world |
| `00:04:17.280 - 00:04:19.909` | contained something called a plick or |
| `00:04:19.919 - 00:04:22.550` | platform level interrupt controller |
| `00:04:22.560 - 00:04:25.030` | and what this does is a separate chip or |
| `00:04:25.040 - 00:04:26.950` | separate circuit that deals with |
| `00:04:26.960 - 00:04:28.469` | interrupts coming in from all the |
| `00:04:28.479 - 00:04:29.909` | different devices that might be on a |
| `00:04:29.919 - 00:04:32.390` | system and it's figuring out |
| `00:04:32.400 - 00:04:34.469` | which core should be interrupted |
| `00:04:34.479 - 00:04:36.230` | which core should be told about the |
| `00:04:36.240 - 00:04:39.030` | interrupt and allowed to uh you know |
| `00:04:39.040 - 00:04:42.150` | handle the interrupt and deal with it |
| `00:04:42.160 - 00:04:45.110` | the emulator will emulate the uh |
| `00:04:45.120 - 00:04:47.909` | platform level interrupt controller and |
| `00:04:47.919 - 00:04:49.909` | there's also a core local interrupt |
| `00:04:49.919 - 00:04:52.550` | controller which is also part of the |
| `00:04:52.560 - 00:04:55.909` | emulation and there is one of these core |
| `00:04:55.919 - 00:04:57.749` | local interrupt controllers for each |
| `00:04:57.759 - 00:04:59.990` | core so the operating system has to deal |
| `00:05:00.000 - 00:05:00.950` | with |
| `00:05:00.960 - 00:05:03.670` | these things as well |
| `00:05:03.680 - 00:05:06.310` | memory management is pretty simple |
| `00:05:06.320 - 00:05:08.629` | memory physical memory is divided into |
| `00:05:08.639 - 00:05:11.110` | pages and the page size is fixed with |
| `00:05:11.120 - 00:05:12.950` | the pound sign defined |
| `00:05:12.960 - 00:05:16.310` | at four kilobytes |
| `00:05:16.320 - 00:05:17.990` | memory is allocated at least for the |
| `00:05:18.000 - 00:05:19.350` | kernel |
| `00:05:19.360 - 00:05:20.230` | from |
| `00:05:20.240 - 00:05:22.629` | a free list there's a free list of |
| `00:05:22.639 - 00:05:24.550` | unused pages |
| `00:05:24.560 - 00:05:26.230` | and it's a simple linked list and |
| `00:05:26.240 - 00:05:28.230` | whenever the kernel needs more memory it |
| `00:05:28.240 - 00:05:30.790` | allocates the page from the free list |
| `00:05:30.800 - 00:05:33.510` | and when that page is no longer needed |
| `00:05:33.520 - 00:05:36.390` | it is returned by adding it to the front |
| `00:05:36.400 - 00:05:39.510` | of the free list very basic memory |
| `00:05:39.520 - 00:05:41.830` | allocation scheme |
| `00:05:41.840 - 00:05:42.870` | there are |
| `00:05:42.880 - 00:05:46.469` | no objects in the operating system |
| `00:05:46.479 - 00:05:48.469` | in xv6 there are several different |
| `00:05:48.479 - 00:05:50.310` | structures and of course they're |
| `00:05:50.320 - 00:05:53.029` | pointers but with an object-oriented |
| `00:05:53.039 - 00:05:54.790` | programming language typically you would |
| `00:05:54.800 - 00:05:58.309` | out be allocating variable-sized objects |
| `00:05:58.319 - 00:06:00.710` | none of that's going on in a real-world |
| `00:06:00.720 - 00:06:04.710` | kernel there are other aspects for |
| `00:06:04.720 - 00:06:05.990` | there are other techniques for |
| `00:06:06.000 - 00:06:07.909` | allocating memory to accommodate |
| `00:06:07.919 - 00:06:10.230` | variable size chunks of memory but that |
| `00:06:10.240 - 00:06:13.029` | doesn't happen in xv6 there's no malloc |
| `00:06:13.039 - 00:06:15.110` | for example |
| `00:06:15.120 - 00:06:17.110` | the virtual address spaces are handled |
| `00:06:17.120 - 00:06:19.270` | with page tables the page tables are |
| `00:06:19.280 - 00:06:21.189` | three levels i've |
| `00:06:21.199 - 00:06:23.189` | shown diagrammatically a three level |
| `00:06:23.199 - 00:06:25.110` | page table down at the bottom we have |
| `00:06:25.120 - 00:06:26.950` | the data pages |
| `00:06:26.960 - 00:06:30.150` | there's one table per process |
| `00:06:30.160 - 00:06:32.790` | and in addition there's one page table |
| `00:06:32.800 - 00:06:33.909` | for the kernel |
| `00:06:33.919 - 00:06:36.070` | itself which maps all of the physical |
| `00:06:36.080 - 00:06:37.990` | memory that |
| `00:06:38.000 - 00:06:40.550` | table for the kernel is shared by all |
| `00:06:40.560 - 00:06:43.270` | the cores |
| `00:06:43.280 - 00:06:45.510` | the page table hardware can accommodate |
| `00:06:45.520 - 00:06:47.749` | marking the data pages as either |
| `00:06:47.759 - 00:06:49.430` | readable writable |
| `00:06:49.440 - 00:06:51.430` | executable |
| `00:06:51.440 - 00:06:54.070` | and u stands for user mode uh |
| `00:06:54.080 - 00:06:57.270` | access and v stands for valid so a page |
| `00:06:57.280 - 00:06:59.990` | may or may not be writable |
| `00:07:00.000 - 00:07:03.270` | it may or may or may not be executable |
| `00:07:03.280 - 00:07:07.110` | it may or not may or may not be valid |
| `00:07:07.120 - 00:07:08.870` | it can also be marked |
| `00:07:08.880 - 00:07:11.909` | you or or not you and that determines |
| `00:07:11.919 - 00:07:13.909` | whether the page can be accessed when |
| `00:07:13.919 - 00:07:15.830` | the processor is running in |
| `00:07:15.840 - 00:07:17.749` | user mode |
| `00:07:17.759 - 00:07:19.990` | well i just use the word processor here |
| `00:07:20.000 - 00:07:22.710` | and probably i should have said core so |
| `00:07:22.720 - 00:07:24.309` | sometimes i guess i use the word |
| `00:07:24.319 - 00:07:27.029` | processor synonymously with with cpu and |
| `00:07:27.039 - 00:07:28.230` | core |
| `00:07:28.240 - 00:07:31.110` | it shows my age perhaps so |
| `00:07:31.120 - 00:07:32.790` | some pages |
| `00:07:32.800 - 00:07:34.629` | may be restricted so that they can only |
| `00:07:34.639 - 00:07:37.830` | be accessed when the core is running in |
| `00:07:37.840 - 00:07:41.589` | kernel mode and others other pages can |
| `00:07:41.599 - 00:07:46.150` | be accessed by user mode code |
| `00:07:46.160 - 00:07:49.430` | the scheduler for xv6 is quite simple |
| `00:07:49.440 - 00:07:53.589` | it's a basically a round robin scheduler |
| `00:07:53.599 - 00:07:55.430` | each process is given a time slice and |
| `00:07:55.440 - 00:08:00.070` | then it goes back it becomes |
| `00:08:00.080 - 00:08:02.469` | dormant sitting on the ready queue |
| `00:08:02.479 - 00:08:05.189` | the size of all time slices are fixed it |
| `00:08:05.199 - 00:08:06.390` | happens to be |
| `00:08:06.400 - 00:08:09.909` | fixed at one million cycles |
| `00:08:09.919 - 00:08:12.070` | all the cores share a single ready queue |
| `00:08:12.080 - 00:08:13.270` | sometimes a ready queue is called a |
| `00:08:13.280 - 00:08:16.550` | ready list or a run list or a run queue |
| `00:08:16.560 - 00:08:19.830` | i'll use all those terms probably |
| `00:08:19.840 - 00:08:21.189` | but in any case there's only one of |
| `00:08:21.199 - 00:08:24.070` | these things |
| `00:08:24.080 - 00:08:25.350` | now |
| `00:08:25.360 - 00:08:29.029` | the cores share the single ready queue |
| `00:08:29.039 - 00:08:32.630` | and so when a core is ready to run a |
| `00:08:32.640 - 00:08:33.829` | process |
| `00:08:33.839 - 00:08:36.230` | it will select a process the next |
| `00:08:36.240 - 00:08:38.709` | runnable process from the ready queue |
| `00:08:38.719 - 00:08:41.110` | and it will give it a time slice on that |
| `00:08:41.120 - 00:08:41.990` | core |
| `00:08:42.000 - 00:08:43.589` | and then it will |
| `00:08:43.599 - 00:08:45.269` | return it to the ridicue after the time |
| `00:08:45.279 - 00:08:46.870` | slice ends |
| `00:08:46.880 - 00:08:48.150` | and |
| `00:08:48.160 - 00:08:50.790` | so this ready queue is actually an array |
| `00:08:50.800 - 00:08:53.030` | and what's happening is each core |
| `00:08:53.040 - 00:08:54.790` | for scheduling is going through that |
| `00:08:54.800 - 00:08:57.110` | array linearly when it gets to the |
| `00:08:57.120 - 00:08:58.389` | bottom of the array it goes back up to |
| `00:08:58.399 - 00:09:00.470` | the top and what it's doing is it's |
| `00:09:00.480 - 00:09:03.110` | searching for a process |
| `00:09:03.120 - 00:09:05.910` | that is runnable that is it's ready to |
| `00:09:05.920 - 00:09:07.990` | go and it's ready for its next time |
| `00:09:08.000 - 00:09:09.190` | slice |
| `00:09:09.200 - 00:09:10.790` | and when it finds one it gives it a time |
| `00:09:10.800 - 00:09:13.269` | slice and then after the time slice ends |
| `00:09:13.279 - 00:09:15.590` | it uh returns it to the ready queue as a |
| `00:09:15.600 - 00:09:17.670` | runnable process and moves on to the |
| `00:09:17.680 - 00:09:18.790` | next |
| `00:09:18.800 - 00:09:19.829` | process |
| `00:09:19.839 - 00:09:21.430` | and so it just keeps going through each |
| `00:09:21.440 - 00:09:23.910` | core keeps going through that array |
| `00:09:23.920 - 00:09:25.430` | repeatedly |
| `00:09:25.440 - 00:09:28.630` | and so i said this is a round robin |
| `00:09:28.640 - 00:09:32.630` | scheduler it's not quite round robin |
| `00:09:32.640 - 00:09:35.030` | because what can happen is a particular |
| `00:09:35.040 - 00:09:37.910` | process might be given a time slice |
| `00:09:37.920 - 00:09:40.550` | say on core number one |
| `00:09:40.560 - 00:09:41.509` | and then |
| `00:09:41.519 - 00:09:44.470` | after the time slice completes the core |
| `00:09:44.480 - 00:09:47.670` | will put it back on the ready queue and |
| `00:09:47.680 - 00:09:50.550` | then move on to find another runnable |
| `00:09:50.560 - 00:09:51.910` | process |
| `00:09:51.920 - 00:09:55.190` | but immediately or very soon after some |
| `00:09:55.200 - 00:09:56.389` | other core |
| `00:09:56.399 - 00:09:59.030` | perhaps core number four |
| `00:09:59.040 - 00:10:01.110` | might be searching through the |
| `00:10:01.120 - 00:10:04.470` | array and it might happen to come to |
| `00:10:04.480 - 00:10:07.509` | that same process p |
| `00:10:07.519 - 00:10:08.710` | and so |
| `00:10:08.720 - 00:10:11.990` | it will give it a time slice and then |
| `00:10:12.000 - 00:10:13.670` | put it back on the ready q when the time |
| `00:10:13.680 - 00:10:16.150` | slice ends so what happens is this |
| `00:10:16.160 - 00:10:17.829` | single process p |
| `00:10:17.839 - 00:10:20.069` | might be given a time slice on core |
| `00:10:20.079 - 00:10:21.190` | number one |
| `00:10:21.200 - 00:10:22.150` | and then |
| `00:10:22.160 - 00:10:23.990` | immediately after given another time |
| `00:10:24.000 - 00:10:26.470` | slice on core number four |
| `00:10:26.480 - 00:10:28.230` | and so |
| `00:10:28.240 - 00:10:30.470` | normally with round robin |
| `00:10:30.480 - 00:10:33.590` | after process p is given a time slice it |
| `00:10:33.600 - 00:10:36.150` | needs to wait until all other runnable |
| `00:10:36.160 - 00:10:38.710` | processes have a chance to run |
| `00:10:38.720 - 00:10:39.590` | but |
| `00:10:39.600 - 00:10:40.389` | with |
| `00:10:40.399 - 00:10:43.750` | xv6 and the multiple cores it's sort of |
| `00:10:43.760 - 00:10:46.710` | round robin within each core |
| `00:10:46.720 - 00:10:48.470` | so it's not quite a |
| `00:10:48.480 - 00:10:50.470` | proper round-robin scheduling but it's |
| `00:10:50.480 - 00:10:52.870` | it's it's simple and it's probably just |
| `00:10:52.880 - 00:10:55.590` | about as effective equally |
| `00:10:55.600 - 00:10:59.430` | equally efficient |
| `00:10:59.440 - 00:11:02.630` | the boot sequence is uh pretty basic |
| `00:11:02.640 - 00:11:05.590` | the emulator which will be |
| `00:11:05.600 - 00:11:08.389` | emulating the risc-5 processor and |
| `00:11:08.399 - 00:11:10.310` | running our kernel |
| `00:11:10.320 - 00:11:13.350` | basically skips all the boot sequence |
| `00:11:13.360 - 00:11:15.509` | altogether what it's going to do is it's |
| `00:11:15.519 - 00:11:18.310` | going to load the kernel from some |
| `00:11:18.320 - 00:11:21.269` | executable file on your host laptop and |
| `00:11:21.279 - 00:11:23.030` | it's going to put it into |
| `00:11:23.040 - 00:11:25.269` | the memory or i should say the fixed |
| `00:11:25.279 - 00:11:26.870` | physical memory that the sorry the |
| `00:11:26.880 - 00:11:30.069` | emulated uh physical memory |
| `00:11:30.079 - 00:11:32.470` | of the risk processor it's emulating and |
| `00:11:32.480 - 00:11:34.790` | it's going to put that kernel code at a |
| `00:11:34.800 - 00:11:37.350` | fixed location in fact this is the |
| `00:11:37.360 - 00:11:38.949` | actual location that |
| `00:11:38.959 - 00:11:40.949` | it uses |
| `00:11:40.959 - 00:11:43.910` | there's no support in xv6 for |
| `00:11:43.920 - 00:11:46.790` | booting a bootloader or a master boot |
| `00:11:46.800 - 00:11:50.870` | record or bios or anything like that |
| `00:11:50.880 - 00:11:53.750` | uh this xb6 uses |
| `00:11:53.760 - 00:11:56.470` | a couple of techniques for locking and |
| `00:11:56.480 - 00:11:58.550` | concurrency control |
| `00:11:58.560 - 00:12:00.710` | spin locks are used |
| `00:12:00.720 - 00:12:01.829` | and |
| `00:12:01.839 - 00:12:03.509` | there are two functions sleep and wake |
| `00:12:03.519 - 00:12:04.790` | up |
| `00:12:04.800 - 00:12:07.110` | with spin locks uh you have two |
| `00:12:07.120 - 00:12:09.190` | functions they're called acquire and |
| `00:12:09.200 - 00:12:10.629` | release |
| `00:12:10.639 - 00:12:13.030` | and there's a single word in memory |
| `00:12:13.040 - 00:12:16.470` | that word is zero if the lock is free or |
| `00:12:16.480 - 00:12:20.710` | unheld and it is one if the lock is |
| `00:12:20.720 - 00:12:23.509` | busy or is locked is being held by some |
| `00:12:23.519 - 00:12:24.790` | process |
| `00:12:24.800 - 00:12:26.389` | and what a choir will do is just |
| `00:12:26.399 - 00:12:28.629` | basically in a tight loop wait for that |
| `00:12:28.639 - 00:12:29.509` | word |
| `00:12:29.519 - 00:12:31.910` | to become zero unlocked and then when it |
| `00:12:31.920 - 00:12:33.350` | finds that it's unlocked it will set it |
| `00:12:33.360 - 00:12:34.470` | to one |
| `00:12:34.480 - 00:12:37.030` | and release just simply sets the word |
| `00:12:37.040 - 00:12:39.430` | back to zero |
| `00:12:39.440 - 00:12:40.710` | uh |
| `00:12:40.720 - 00:12:44.069` | sleep and wake up well when a particular |
| `00:12:44.079 - 00:12:46.550` | thread executes the sleep function it |
| `00:12:46.560 - 00:12:49.590` | will end its time slice and it will be |
| `00:12:49.600 - 00:12:52.389` | placed back on the ready queue with a |
| `00:12:52.399 - 00:12:54.310` | status of runable i'm sorry with the |
| `00:12:54.320 - 00:12:56.150` | status of not runnable |
| `00:12:56.160 - 00:12:57.030` | and |
| `00:12:57.040 - 00:12:59.750` | it will then sleep until it's |
| `00:12:59.760 - 00:13:03.110` | been until it is woken up |
| `00:13:03.120 - 00:13:03.910` | so |
| `00:13:03.920 - 00:13:05.509` | when a process executes the sleep |
| `00:13:05.519 - 00:13:07.750` | function that process will no longer be |
| `00:13:07.760 - 00:13:09.990` | runnable it will go into a blocked or |
| `00:13:10.000 - 00:13:14.310` | sleep state and it will not be scheduled |
| `00:13:14.320 - 00:13:17.350` | later some other process will |
| `00:13:17.360 - 00:13:18.949` | execute the wake up |
| `00:13:18.959 - 00:13:20.629` | function and |
| `00:13:20.639 - 00:13:23.110` | wake up one or more processes or zero or |
| `00:13:23.120 - 00:13:24.470` | more processes |
| `00:13:24.480 - 00:13:27.190` | and if our process happens to get woken |
| `00:13:27.200 - 00:13:29.269` | up then it will be changed |
| `00:13:29.279 - 00:13:31.350` | back from a status of sleeping or |
| `00:13:31.360 - 00:13:33.030` | blocked to |
| `00:13:33.040 - 00:13:34.790` | runnable and then it will get time |
| `00:13:34.800 - 00:13:36.710` | slices after that |
| `00:13:36.720 - 00:13:38.310` | there's also a third technique that is |
| `00:13:38.320 - 00:13:41.430` | sometimes used and that is the selective |
| `00:13:41.440 - 00:13:44.470` | disabling of interrupts |
| `00:13:44.480 - 00:13:47.189` | so each core has a status control word |
| `00:13:47.199 - 00:13:49.269` | and there is a bit in that control word |
| `00:13:49.279 - 00:13:51.430` | that can be either set or cleared |
| `00:13:51.440 - 00:13:52.870` | which either |
| `00:13:52.880 - 00:13:55.269` | enables and allows interrupts or |
| `00:13:55.279 - 00:13:58.389` | disables and prevents interrupts |
| `00:13:58.399 - 00:14:01.269` | by disabling interrupts a thread running |
| `00:14:01.279 - 00:14:03.670` | on one core can prevent being |
| `00:14:03.680 - 00:14:04.790` | interrupted |
| `00:14:04.800 - 00:14:07.509` | when a timer interrupt goes off or when |
| `00:14:07.519 - 00:14:09.189` | an i o device |
| `00:14:09.199 - 00:14:10.790` | calls for an interrupt |
| `00:14:10.800 - 00:14:11.829` | so |
| `00:14:11.839 - 00:14:13.829` | we can use this technique on one core to |
| `00:14:13.839 - 00:14:15.509` | prevent being |
| `00:14:15.519 - 00:14:16.870` | interrupted by |
| `00:14:16.880 - 00:14:19.670` | another thread on that same core |
| `00:14:19.680 - 00:14:20.710` | however |
| `00:14:20.720 - 00:14:23.750` | xv6 is a multi-core system and disabling |
| `00:14:23.760 - 00:14:26.710` | interrupts on one core has no impact on |
| `00:14:26.720 - 00:14:28.069` | other cores |
| `00:14:28.079 - 00:14:30.790` | so a thread on another core can be |
| `00:14:30.800 - 00:14:34.870` | modifying memory simultaneously |
| `00:14:34.880 - 00:14:37.910` | xv6 has a number of fixed limits |
| `00:14:37.920 - 00:14:38.949` | these are |
| `00:14:38.959 - 00:14:41.990` | declared with pound sign defines |
| `00:14:42.000 - 00:14:43.990` | for example the number of processes is |
| `00:14:44.000 - 00:14:45.670` | just a fixed number |
| `00:14:45.680 - 00:14:47.829` | as i said the ready queue is stored in |
| `00:14:47.839 - 00:14:50.310` | an array and that array is allocated |
| `00:14:50.320 - 00:14:52.310` | with a fixed size |
| `00:14:52.320 - 00:14:54.230` | the number of files |
| `00:14:54.240 - 00:14:56.150` | open this the maximum number of files |
| `00:14:56.160 - 00:14:58.069` | that can be opened simultaneously as a |
| `00:14:58.079 - 00:15:00.710` | fixed number and so on |
| `00:15:00.720 - 00:15:01.509` | the |
| `00:15:01.519 - 00:15:04.230` | kernel tends to use arrays and not |
| `00:15:04.240 - 00:15:06.230` | linked lists so much |
| `00:15:06.240 - 00:15:09.350` | and so in several situations we are |
| `00:15:09.360 - 00:15:11.269` | running through these arrays with a |
| `00:15:11.279 - 00:15:12.870` | linear search |
| `00:15:12.880 - 00:15:15.590` | for example there's a function to kill a |
| `00:15:15.600 - 00:15:19.590` | process and it's passed the process id |
| `00:15:19.600 - 00:15:21.829` | and what it does is it does a linear |
| `00:15:21.839 - 00:15:25.430` | search of the array of processes |
| `00:15:25.440 - 00:15:27.670` | actually this this ready queue here is |
| `00:15:27.680 - 00:15:30.389` | is not a separate uh data structure |
| `00:15:30.399 - 00:15:33.430` | there's a there's a single array of |
| `00:15:33.440 - 00:15:35.910` | processes and some of them are marked |
| `00:15:35.920 - 00:15:38.470` | runnable and some of them are |
| `00:15:38.480 - 00:15:41.509` | not runnable and so when we want to find |
| `00:15:41.519 - 00:15:43.350` | a process that's runnable we basically |
| `00:15:43.360 - 00:15:45.030` | just go through the process array |
| `00:15:45.040 - 00:15:46.949` | looking for one that has a status of |
| `00:15:46.959 - 00:15:48.230` | runnable |
| `00:15:48.240 - 00:15:49.910` | and likewise when we want to kill a |
| `00:15:49.920 - 00:15:53.269` | process we just run through the array |
| `00:15:53.279 - 00:15:55.030` | looking for one that has a matching |
| `00:15:55.040 - 00:15:58.069` | process id |
| `00:15:58.079 - 00:16:00.629` | okay now let me talk about the user |
| `00:16:00.639 - 00:16:01.990` | address space |
| `00:16:02.000 - 00:16:03.990` | so this is the virtual address space |
| `00:16:04.000 - 00:16:08.230` | that a user mode program sees and here |
| `00:16:08.240 - 00:16:10.310` | i'm showing the address starting at |
| `00:16:10.320 - 00:16:12.790` | location zero going up to the maximum |
| `00:16:12.800 - 00:16:15.110` | virtual address |
| `00:16:15.120 - 00:16:17.670` | the kernel will allocate this address |
| `00:16:17.680 - 00:16:21.110` | space in units of pages each |
| `00:16:21.120 - 00:16:24.710` | one of these is a 4k page and so |
| `00:16:24.720 - 00:16:25.509` | when |
| `00:16:25.519 - 00:16:28.629` | the exact system call is used the kernel |
| `00:16:28.639 - 00:16:29.990` | will |
| `00:16:30.000 - 00:16:32.150` | go out to the file system and find the |
| `00:16:32.160 - 00:16:36.230` | executable file which is in elf format |
| `00:16:36.240 - 00:16:38.790` | and it will allocate |
| `00:16:38.800 - 00:16:40.310` | several pages |
| `00:16:40.320 - 00:16:42.949` | an integral number of pages here and it |
| `00:16:42.959 - 00:16:44.629` | will read in |
| `00:16:44.639 - 00:16:46.790` | the data and the code into this region |
| `00:16:46.800 - 00:16:49.189` | of memory |
| `00:16:49.199 - 00:16:51.829` | those pages will then be marked read |
| `00:16:51.839 - 00:16:56.150` | write and executable by the kernel |
| `00:16:56.160 - 00:16:57.430` | you also have |
| `00:16:57.440 - 00:17:00.230` | a page allocated for the stack |
| `00:17:00.240 - 00:17:02.629` | we see here that the stack only gets one |
| `00:17:02.639 - 00:17:03.670` | page |
| `00:17:03.680 - 00:17:05.829` | four kilobytes of memory |
| `00:17:05.839 - 00:17:09.270` | and so xv6 is somewhat limited in this |
| `00:17:09.280 - 00:17:11.029` | aspect a |
| `00:17:11.039 - 00:17:14.069` | process that wants to grow its stack |
| `00:17:14.079 - 00:17:17.189` | beyond this will not be able to |
| `00:17:17.199 - 00:17:20.710` | and so xv6 cannot support |
| `00:17:20.720 - 00:17:22.150` | that and |
| `00:17:22.160 - 00:17:23.990` | when a process tries to grow its stack |
| `00:17:24.000 - 00:17:26.069` | beyond that it will simply be |
| `00:17:26.079 - 00:17:29.510` | aborted the process will be terminated |
| `00:17:29.520 - 00:17:31.510` | and the way the kernel does that |
| `00:17:31.520 - 00:17:33.830` | is kind of clever it allocates what is |
| `00:17:33.840 - 00:17:36.549` | called a guard page right here this card |
| `00:17:36.559 - 00:17:39.190` | page is not readable not writable in |
| `00:17:39.200 - 00:17:40.549` | fact |
| `00:17:40.559 - 00:17:43.990` | it's not accessible in user mode so if |
| `00:17:44.000 - 00:17:46.630` | the user mode code tries to access this |
| `00:17:46.640 - 00:17:47.909` | page |
| `00:17:47.919 - 00:17:50.230` | for its virtual address space it will |
| `00:17:50.240 - 00:17:52.230` | immediately cause an exception and the |
| `00:17:52.240 - 00:17:54.070` | process will be thrown out and |
| `00:17:54.080 - 00:17:56.789` | terminated |
| `00:17:56.799 - 00:17:58.789` | the heap |
| `00:17:58.799 - 00:18:02.470` | starts after the stack and grows |
| `00:18:02.480 - 00:18:04.950` | in units of pages |
| `00:18:04.960 - 00:18:06.710` | this is called the break |
| `00:18:06.720 - 00:18:09.029` | if you program in linux or unix you may |
| `00:18:09.039 - 00:18:11.270` | be familiar with this concept |
| `00:18:11.280 - 00:18:13.590` | as the user mode |
| `00:18:13.600 - 00:18:14.390` | code |
| `00:18:14.400 - 00:18:16.870` | allocates things on its heap |
| `00:18:16.880 - 00:18:19.270` | this boundary here will be moved up |
| `00:18:19.280 - 00:18:22.070` | the kernel will be invoked to |
| `00:18:22.080 - 00:18:25.750` | allocate more pages and the break |
| `00:18:25.760 - 00:18:28.870` | point will be moved up and will consume |
| `00:18:28.880 - 00:18:31.110` | some of the unused space these pages |
| `00:18:31.120 - 00:18:35.350` | will be marked read and write |
| `00:18:35.360 - 00:18:38.390` | i should say that in a typical linux or |
| `00:18:38.400 - 00:18:40.630` | unix system the stack is usually put out |
| `00:18:40.640 - 00:18:43.430` | in high memory and it grows down |
| `00:18:43.440 - 00:18:46.310` | and it's capable of growing and memory |
| `00:18:46.320 - 00:18:47.909` | is only filled up when these two when |
| `00:18:47.919 - 00:18:49.669` | the heap and the stack run into each |
| `00:18:49.679 - 00:18:51.110` | other |
| `00:18:51.120 - 00:18:53.669` | that's not done with xv6 we have just a |
| `00:18:53.679 - 00:18:58.710` | single stack page down here |
| `00:18:58.720 - 00:18:59.909` | however we have something interesting |
| `00:18:59.919 - 00:19:02.470` | going on we have two pages up at the |
| `00:19:02.480 - 00:19:05.110` | very top of the virtual memory space |
| `00:19:05.120 - 00:19:07.350` | virtual memory happens to be 256 |
| `00:19:07.360 - 00:19:10.310` | gigabytes so this unused space here is |
| `00:19:10.320 - 00:19:12.549` | actually huge |
| `00:19:12.559 - 00:19:14.230` | and the heap |
| `00:19:14.240 - 00:19:15.510` | may |
| `00:19:15.520 - 00:19:17.510` | not take up very much of that and |
| `00:19:17.520 - 00:19:18.789` | certainly it's not going to take it all |
| `00:19:18.799 - 00:19:20.390` | up because |
| `00:19:20.400 - 00:19:21.789` | we only have |
| `00:19:21.799 - 00:19:25.510` | 128 megabytes of physical memory and |
| `00:19:25.520 - 00:19:26.549` | we're not going to be able to |
| `00:19:26.559 - 00:19:28.230` | accommodate |
| `00:19:28.240 - 00:19:29.750` | anything larger than that any virtual |
| `00:19:29.760 - 00:19:34.950` | address space that exceeds 128 megabytes |
| `00:19:34.960 - 00:19:39.029` | these pages up here are marked not |
| `00:19:39.039 - 00:19:42.150` | accessible in user mode so user code |
| `00:19:42.160 - 00:19:44.789` | cannot access these pages user mode |
| `00:19:44.799 - 00:19:46.950` | cannot read them or write them or |
| `00:19:46.960 - 00:19:49.110` | execute any code from them |
| `00:19:49.120 - 00:19:50.789` | these are used during |
| `00:19:50.799 - 00:19:53.990` | trap and exception processing |
| `00:19:54.000 - 00:19:56.789` | so the trampoline page contains code so |
| `00:19:56.799 - 00:19:59.110` | it's executable |
| `00:19:59.120 - 00:20:01.350` | and when an exception or interrupt |
| `00:20:01.360 - 00:20:03.590` | occurs we're going to be executing code |
| `00:20:03.600 - 00:20:05.190` | in the trampoline page |
| `00:20:05.200 - 00:20:07.669` | we'll discuss that later |
| `00:20:07.679 - 00:20:09.350` | the trap frame |
| `00:20:09.360 - 00:20:12.149` | is readable and writeable and that is |
| `00:20:12.159 - 00:20:13.110` | where |
| `00:20:13.120 - 00:20:14.310` | the |
| `00:20:14.320 - 00:20:16.070` | registers will be saved |
| `00:20:16.080 - 00:20:18.230` | when a trap that is when an |
| `00:20:18.240 - 00:20:20.549` | exception or an interrupt occurs |
| `00:20:20.559 - 00:20:22.549` | the registers the entire state of this |
| `00:20:22.559 - 00:20:24.230` | user process |
| `00:20:24.240 - 00:20:26.310` | will be saved and it will be saved in |
| `00:20:26.320 - 00:20:28.070` | the trap frame |
| `00:20:28.080 - 00:20:31.110` | by code in the trampoline page |
| `00:20:31.120 - 00:20:32.950` | there is |
| `00:20:32.960 - 00:20:35.590` | as each virtual address space has its |
| `00:20:35.600 - 00:20:39.190` | own trap frame page so it's they're all |
| `00:20:39.200 - 00:20:41.590` | mapped to the same location |
| `00:20:41.600 - 00:20:43.990` | but uh they're different physical memory |
| `00:20:44.000 - 00:20:45.510` | pages so each |
| `00:20:45.520 - 00:20:47.430` | process has its own |
| `00:20:47.440 - 00:20:50.710` | trap frame the trampoline page is shared |
| `00:20:50.720 - 00:20:52.549` | by all |
| `00:20:52.559 - 00:20:56.710` | processes so the exact same page of |
| `00:20:56.720 - 00:20:59.190` | physical memory is mapped into this into |
| `00:20:59.200 - 00:21:01.510` | the same spot in all of the virtual |
| `00:21:01.520 - 00:21:04.630` | address spaces |
| `00:21:04.640 - 00:21:06.470` | one thing i was going to mention was |
| `00:21:06.480 - 00:21:08.310` | that |
| `00:21:08.320 - 00:21:10.470` | i said that the kernel |
| `00:21:10.480 - 00:21:12.470` | doesn't have a very sophisticated |
| `00:21:12.480 - 00:21:15.430` | memory allocation system it just has |
| `00:21:15.440 - 00:21:17.430` | uh pages and they are kept in a free |
| `00:21:17.440 - 00:21:20.470` | list and so all allocations are in units |
| `00:21:20.480 - 00:21:23.190` | of one page or four hundred and four |
| `00:21:23.200 - 00:21:25.110` | thousand and ninety six bytes four k |
| `00:21:25.120 - 00:21:26.870` | bytes |
| `00:21:26.880 - 00:21:27.830` | however |
| `00:21:27.840 - 00:21:30.549` | we accommodate heaps for the user mode |
| `00:21:30.559 - 00:21:33.350` | programs so a user mode program is free |
| `00:21:33.360 - 00:21:36.310` | to implement malloc with and allocate |
| `00:21:36.320 - 00:21:38.230` | things on the heap in fact it's free to |
| `00:21:38.240 - 00:21:39.350` | implement |
| `00:21:39.360 - 00:21:41.029` | whatever complex garbage collection |
| `00:21:41.039 - 00:21:43.510` | algorithm it wants to |
| `00:21:43.520 - 00:21:45.669` | but it just has this uh memory that can |
| `00:21:45.679 - 00:21:47.669` | be grown here |
| `00:21:47.679 - 00:21:50.870` | to allocate its memory to to accommodate |
| `00:21:50.880 - 00:21:53.430` | its memory needs |
| `00:21:53.440 - 00:21:56.390` | so uh i mentioned that this uh |
| `00:21:56.400 - 00:21:59.430` | not you here means that uh the guard |
| `00:21:59.440 - 00:22:02.390` | page is not ex valid when it's accessed |
| `00:22:02.400 - 00:22:05.110` | in user mode likewise the trampoline and |
| `00:22:05.120 - 00:22:08.149` | trap frame pages are not |
| `00:22:08.159 - 00:22:13.110` | accessible in user mode |
| `00:22:13.120 - 00:22:15.270` | c programmers will be familiar with the |
| `00:22:15.280 - 00:22:17.669` | main function which takes a couple of |
| `00:22:17.679 - 00:22:20.390` | arguments arg c and r v |
| `00:22:20.400 - 00:22:21.750` | and these point to the arguments that |
| `00:22:21.760 - 00:22:23.830` | are passed into the program when it |
| `00:22:23.840 - 00:22:25.750` | begins executing |
| `00:22:25.760 - 00:22:27.669` | you may also be familiar with something |
| `00:22:27.679 - 00:22:31.029` | called arg e for environment but that is |
| `00:22:31.039 - 00:22:32.230` | not |
| `00:22:32.240 - 00:22:35.270` | accommodated with xv6 that doesn't xv6 |
| `00:22:35.280 - 00:22:36.470` | doesn't |
| `00:22:36.480 - 00:22:37.510` | use |
| `00:22:37.520 - 00:22:40.630` | any environment variables just rxe and |
| `00:22:40.640 - 00:22:43.909` | rxv and how does that happen well |
| `00:22:43.919 - 00:22:44.870` | when |
| `00:22:44.880 - 00:22:46.950` | an exact system call is made |
| `00:22:46.960 - 00:22:49.430` | the code in the kernel will |
| `00:22:49.440 - 00:22:51.830` | set up the virtual address space here |
| `00:22:51.840 - 00:22:52.789` | and |
| `00:22:52.799 - 00:22:54.230` | among other things it will allocate a |
| `00:22:54.240 - 00:22:56.390` | page for the stack and |
| `00:22:56.400 - 00:22:57.590` | it will |
| `00:22:57.600 - 00:22:59.590` | push onto the stack |
| `00:22:59.600 - 00:23:01.190` | the arguments |
| `00:23:01.200 - 00:23:03.029` | we'll get into the details of exactly |
| `00:23:03.039 - 00:23:05.110` | what's pushed later but basically it's |
| `00:23:05.120 - 00:23:07.350` | going to push all the arguments onto the |
| `00:23:07.360 - 00:23:10.070` | stack and then set the stack pointer to |
| `00:23:10.080 - 00:23:11.110` | be |
| `00:23:11.120 - 00:23:12.149` | something |
| `00:23:12.159 - 00:23:14.470` | besides right here |
| `00:23:14.480 - 00:23:15.510` | it will |
| `00:23:15.520 - 00:23:16.870` | allocate these things on the stack |
| `00:23:16.880 - 00:23:19.029` | essentially pushing them onto the stack |
| `00:23:19.039 - 00:23:21.350` | and moving them into the registers so |
| `00:23:21.360 - 00:23:24.549` | when the first instruction of the user |
| `00:23:24.559 - 00:23:28.390` | program is executed it will already find |
| `00:23:28.400 - 00:23:30.630` | several things on the stack it will find |
| `00:23:30.640 - 00:23:33.990` | its the arguments on the stack |
| `00:23:34.000 - 00:23:35.830` | now i mentioned that uh we could only |
| `00:23:35.840 - 00:23:39.270` | have 256 |
| `00:23:39.280 - 00:23:41.750` | gigabytes of virtual address space i |
| `00:23:41.760 - 00:23:45.830` | want to uh mention that the |
| `00:23:45.840 - 00:23:48.549` | risc-5 architecture |
| `00:23:48.559 - 00:23:50.549` | it's a pretty complex architecture and |
| `00:23:50.559 - 00:23:52.310` | there's several different options |
| `00:23:52.320 - 00:23:55.590` | uh for page tables there are three |
| `00:23:55.600 - 00:23:56.549` | options |
| `00:23:56.559 - 00:24:00.549` | called sv32 sv-39 sv 48 |
| `00:24:00.559 - 00:24:02.390` | the version of the architecture being |
| `00:24:02.400 - 00:24:04.549` | used for xv6 |
| `00:24:04.559 - 00:24:07.029` | is the sv 39 |
| `00:24:07.039 - 00:24:08.630` | version |
| `00:24:08.640 - 00:24:10.549` | the first one is a two level page table |
| `00:24:10.559 - 00:24:12.789` | scheme the second one is a three level |
| `00:24:12.799 - 00:24:16.549` | page table scheme and sp 48 is a four |
| `00:24:16.559 - 00:24:18.390` | level page table scheme |
| `00:24:18.400 - 00:24:20.870` | so xv6 uses |
| `00:24:20.880 - 00:24:23.190` | sv39 architecture which |
| `00:24:23.200 - 00:24:27.830` | provides for our three level page tables |
| `00:24:27.840 - 00:24:29.510` | okay |
| `00:24:29.520 - 00:24:31.909` | and with that particular scheme virtual |
| `00:24:31.919 - 00:24:34.549` | addresses are 39 bits |
| `00:24:34.559 - 00:24:38.390` | well 39 bits happens to be |
| `00:24:38.400 - 00:24:42.630` | 2 to the 39 happens to be 512 gigabytes |
| `00:24:42.640 - 00:24:45.029` | so with this scheme we could actually |
| `00:24:45.039 - 00:24:47.590` | access 512 gigabytes |
| `00:24:47.600 - 00:24:49.190` | um |
| `00:24:49.200 - 00:24:52.230` | two to the 39 expressed in hex is this |
| `00:24:52.240 - 00:24:53.590` | number here |
| `00:24:53.600 - 00:24:56.390` | and so you can see uh |
| `00:24:56.400 - 00:24:57.190` | that |
| `00:24:57.200 - 00:25:00.789` | expressed down here here are 39 bits and |
| `00:25:00.799 - 00:25:03.110` | the first bit beyond that would be |
| `00:25:03.120 - 00:25:03.990` | in |
| `00:25:04.000 - 00:25:08.149` | here right here a 8 is 1 0 0 0 |
| `00:25:08.159 - 00:25:11.350` | and then 0 is 0 0 0 0 and so on |
| `00:25:11.360 - 00:25:17.350` | so um |
| `00:25:17.360 - 00:25:19.990` | uh |
| `00:25:20.000 - 00:25:24.470` | it turns out that xv6 only uses 38 bits |
| `00:25:24.480 - 00:25:26.310` | okay it does not use the full 39 bits |
| `00:25:26.320 - 00:25:27.590` | it's one bit |
| `00:25:27.600 - 00:25:28.870` | shy |
| `00:25:28.880 - 00:25:31.990` | and so two to the 38 is actually 256 |
| `00:25:32.000 - 00:25:35.510` | gigabytes and that number is four zero |
| `00:25:35.520 - 00:25:37.909` | zero zero zero zero zero zero |
| `00:25:37.919 - 00:25:42.149` | that is equated to our maximum number so |
| `00:25:42.159 - 00:25:43.190` | this |
| `00:25:43.200 - 00:25:47.350` | our address range is from zero to |
| `00:25:47.360 - 00:25:49.110` | three f |
| `00:25:49.120 - 00:25:51.830` | that's three is one one and then f one |
| `00:25:51.840 - 00:25:54.870` | one one one so three f |
| `00:25:54.880 - 00:25:58.870` | one one one one one one and then fff |
| `00:25:58.880 - 00:26:01.750` | ffff and then fff |
| `00:26:01.760 - 00:26:03.350` | so our maximum |
| `00:26:03.360 - 00:26:05.110` | address is this |
| `00:26:05.120 - 00:26:06.230` | and |
| `00:26:06.240 - 00:26:07.190` | um |
| `00:26:07.200 - 00:26:08.710` | so maximum |
| `00:26:08.720 - 00:26:10.789` | virtual address here is actually |
| `00:26:10.799 - 00:26:12.470` | the address of |
| `00:26:12.480 - 00:26:17.750` | the byte just beyond our virtual address |
| `00:26:17.760 - 00:26:20.310` | okay uh that's it for this video i'll |
| `00:26:20.320 - 00:26:24.120` | see you on the next one |
