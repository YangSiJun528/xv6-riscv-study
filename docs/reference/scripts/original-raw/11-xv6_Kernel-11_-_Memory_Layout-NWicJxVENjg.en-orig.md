# xv6 Kernel-11: Memory Layout

| Field | Value |
| --- | --- |
| Video ID | `NWicJxVENjg` |
| URL | <https://www.youtube.com/watch?v=NWicJxVENjg> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.199 - 00:00:02.710` | this video is part of a series on the |
| `00:00:02.720 - 00:00:05.349` | xv6 operating system kernel |
| `00:00:05.359 - 00:00:06.789` | in this video i'm going to talk about |
| `00:00:06.799 - 00:00:09.030` | how memory is laid out and i'm going to |
| `00:00:09.040 - 00:00:12.150` | walk through the file memlayout.h |
| `00:00:12.160 - 00:00:14.150` | i'll talk about the general organization |
| `00:00:14.160 - 00:00:16.230` | of physical memory i'll talk about the |
| `00:00:16.240 - 00:00:18.310` | kernels virtual address space and the |
| `00:00:18.320 - 00:00:20.310` | kernel's page table |
| `00:00:20.320 - 00:00:22.630` | and i will describe and motivate the |
| `00:00:22.640 - 00:00:26.150` | need for the trampoline and trap frame |
| `00:00:26.160 - 00:00:28.550` | pages |
| `00:00:28.560 - 00:00:31.029` | let's begin with this uh picture of a |
| `00:00:31.039 - 00:00:33.110` | user thread which is executing |
| `00:00:33.120 - 00:00:34.549` | instructions |
| `00:00:34.559 - 00:00:37.270` | at some point a trap occurs and we start |
| `00:00:37.280 - 00:00:39.910` | executing instructions in the kernel |
| `00:00:39.920 - 00:00:41.990` | and then at some other point |
| `00:00:42.000 - 00:00:44.709` | the kernel code will execute the cis rep |
| `00:00:44.719 - 00:00:47.590` | or system return instruction and go back |
| `00:00:47.600 - 00:00:49.910` | into user code |
| `00:00:49.920 - 00:00:52.470` | the user code executes in user mode and |
| `00:00:52.480 - 00:00:54.869` | the kernel code executes in |
| `00:00:54.879 - 00:00:57.430` | supervisor mode risk 5 calls it uh |
| `00:00:57.440 - 00:00:59.510` | supervisor mode sometimes i use the term |
| `00:00:59.520 - 00:01:01.990` | kernel mode same idea |
| `00:01:02.000 - 00:01:04.070` | so let's look more closely at what |
| `00:01:04.080 - 00:01:05.109` | happens |
| `00:01:05.119 - 00:01:07.990` | at the time of the trap and the sret |
| `00:01:08.000 - 00:01:11.109` | instruction |
| `00:01:11.119 - 00:01:13.670` | so here we're executing in user mode and |
| `00:01:13.680 - 00:01:15.270` | we have a trap and then we have the |
| `00:01:15.280 - 00:01:17.030` | context switch |
| `00:01:17.040 - 00:01:18.870` | so that's when we go into executing |
| `00:01:18.880 - 00:01:21.109` | kernel code and |
| `00:01:21.119 - 00:01:23.670` | it's running in what risk five calls the |
| `00:01:23.680 - 00:01:25.109` | supervisor mode |
| `00:01:25.119 - 00:01:26.550` | or kernel mode |
| `00:01:26.560 - 00:01:28.550` | and then at some later time we have the |
| `00:01:28.560 - 00:01:30.870` | sysret instruction and we return to |
| `00:01:30.880 - 00:01:34.310` | executing the user code in user mode in |
| `00:01:34.320 - 00:01:37.030` | the user's virtual address space |
| `00:01:37.040 - 00:01:38.710` | so here's the trap instruction this is |
| `00:01:38.720 - 00:01:41.830` | what happens in the hardware |
| `00:01:41.840 - 00:01:44.149` | and then down here we have the s rep |
| `00:01:44.159 - 00:01:45.350` | instruction |
| `00:01:45.360 - 00:01:46.310` | and |
| `00:01:46.320 - 00:01:48.469` | so the trap |
| `00:01:48.479 - 00:01:50.789` | will be handled in hardware and what |
| `00:01:50.799 - 00:01:52.870` | will it do well the core will switch |
| `00:01:52.880 - 00:01:55.350` | into kernel mode or supervisor mode if |
| `00:01:55.360 - 00:01:56.389` | you will |
| `00:01:56.399 - 00:01:58.950` | it will disable interrupts |
| `00:01:58.960 - 00:02:01.350` | it will save the program counter in a |
| `00:02:01.360 - 00:02:04.870` | control and status register called sepc |
| `00:02:04.880 - 00:02:05.990` | and then it will load the program |
| `00:02:06.000 - 00:02:10.070` | counter from another csr called |
| `00:02:10.080 - 00:02:13.750` | s t vec this register will contain the |
| `00:02:13.760 - 00:02:16.229` | address of the first instruction |
| `00:02:16.239 - 00:02:19.350` | in this uservec routine and so |
| `00:02:19.360 - 00:02:21.830` | effectively this is a jump to the |
| `00:02:21.840 - 00:02:24.229` | uservec routine |
| `00:02:24.239 - 00:02:26.390` | the uservec routine is written in |
| `00:02:26.400 - 00:02:27.990` | assembly language |
| `00:02:28.000 - 00:02:30.309` | and it will begin |
| `00:02:30.319 - 00:02:33.589` | by saving the user registers |
| `00:02:33.599 - 00:02:34.470` | the |
| `00:02:34.480 - 00:02:36.229` | general purpose registers and the |
| `00:02:36.239 - 00:02:37.830` | program counter are |
| `00:02:37.840 - 00:02:40.070` | very important to the user program and |
| `00:02:40.080 - 00:02:41.750` | they need to be saved immediately so |
| `00:02:41.760 - 00:02:45.350` | later we can restore them |
| `00:02:45.360 - 00:02:46.949` | we can't really do anything without |
| `00:02:46.959 - 00:02:49.430` | using general purpose registers so this |
| `00:02:49.440 - 00:02:51.190` | has to be more or less the first thing |
| `00:02:51.200 - 00:02:54.070` | we do saving the user registers |
| `00:02:54.080 - 00:02:55.990` | and then we load a few of the critical |
| `00:02:56.000 - 00:02:57.670` | kernel registers |
| `00:02:57.680 - 00:03:00.070` | we need to load the kernel stack pointer |
| `00:03:00.080 - 00:03:01.110` | register |
| `00:03:01.120 - 00:03:03.589` | and we also need to load the tp register |
| `00:03:03.599 - 00:03:05.110` | which contains the |
| `00:03:05.120 - 00:03:07.190` | core number |
| `00:03:07.200 - 00:03:09.350` | and finally we need to switch into the |
| `00:03:09.360 - 00:03:12.309` | kernel's address space so there's |
| `00:03:12.319 - 00:03:14.630` | another control and status register |
| `00:03:14.640 - 00:03:16.710` | called satp |
| `00:03:16.720 - 00:03:18.550` | and we load that with the address of the |
| `00:03:18.560 - 00:03:21.509` | kernels page table thereby switching all |
| `00:03:21.519 - 00:03:23.910` | execution into a different virtual |
| `00:03:23.920 - 00:03:25.270` | address space |
| `00:03:25.280 - 00:03:27.030` | and finally we jump to |
| `00:03:27.040 - 00:03:29.110` | this user trap function which is written |
| `00:03:29.120 - 00:03:31.190` | in c |
| `00:03:31.200 - 00:03:33.270` | uh later we get ready to when we get |
| `00:03:33.280 - 00:03:35.750` | ready to return to the user code we |
| `00:03:35.760 - 00:03:38.869` | uh call this user trap rat instruction |
| `00:03:38.879 - 00:03:42.229` | uh sorry routine and this uh user trap |
| `00:03:42.239 - 00:03:45.430` | rep function will call a |
| `00:03:45.440 - 00:03:48.550` | routine called user rat |
| `00:03:48.560 - 00:03:51.430` | user rep is coded in assembly language |
| `00:03:51.440 - 00:03:53.509` | and it will |
| `00:03:53.519 - 00:03:56.789` | restore the satp register which will |
| `00:03:56.799 - 00:03:59.030` | switch us back into the user's virtual |
| `00:03:59.040 - 00:04:00.550` | address space |
| `00:04:00.560 - 00:04:01.509` | and then |
| `00:04:01.519 - 00:04:03.589` | directly before executing the sret |
| `00:04:03.599 - 00:04:06.070` | instruction it will restore the user's |
| `00:04:06.080 - 00:04:07.750` | registers |
| `00:04:07.760 - 00:04:09.750` | and finally the s red instruction will |
| `00:04:09.760 - 00:04:12.550` | switch the mode into user mode and it |
| `00:04:12.560 - 00:04:14.390` | will restore the program counter from |
| `00:04:14.400 - 00:04:18.069` | whatever is in the s epc register |
| `00:04:18.079 - 00:04:20.310` | and finally enable interrupts and from |
| `00:04:20.320 - 00:04:24.150` | then on we execute in user mode |
| `00:04:24.160 - 00:04:26.710` | now i said these things were functions |
| `00:04:26.720 - 00:04:27.510` | but |
| `00:04:27.520 - 00:04:29.110` | not really |
| `00:04:29.120 - 00:04:31.990` | a function is called and then it returns |
| `00:04:32.000 - 00:04:33.430` | whereas with these things there's not |
| `00:04:33.440 - 00:04:34.950` | really a return |
| `00:04:34.960 - 00:04:36.790` | uservec |
| `00:04:36.800 - 00:04:39.670` | ends with a jump to |
| `00:04:39.680 - 00:04:42.710` | user trap user trap is coded as a c |
| `00:04:42.720 - 00:04:45.110` | function but there's never any return |
| `00:04:45.120 - 00:04:46.469` | from it |
| `00:04:46.479 - 00:04:48.230` | it calls this and it calls that and |
| `00:04:48.240 - 00:04:50.310` | eventually it calls a function |
| `00:04:50.320 - 00:04:51.990` | user trap ret |
| `00:04:52.000 - 00:04:54.310` | and user trap will |
| `00:04:54.320 - 00:04:57.590` | call user rep |
| `00:04:57.600 - 00:05:00.550` | user read will never return to user trap |
| `00:05:00.560 - 00:05:03.029` | rat so in that sense it's not really a |
| `00:05:03.039 - 00:05:07.110` | function but it's just a block of code |
| `00:05:07.120 - 00:05:11.350` | likewise user ret is not going to have a |
| `00:05:11.360 - 00:05:14.230` | normal kind of return instead instead |
| `00:05:14.240 - 00:05:20.070` | it's executing this esret instruction |
| `00:05:20.080 - 00:05:22.310` | okay let's look at the |
| `00:05:22.320 - 00:05:24.710` | what so the problem is |
| `00:05:24.720 - 00:05:26.629` | that |
| `00:05:26.639 - 00:05:29.029` | the code that saves the user registers |
| `00:05:29.039 - 00:05:31.670` | and loads the kernel registers |
| `00:05:31.680 - 00:05:34.469` | is running in the user's address space |
| `00:05:34.479 - 00:05:36.950` | so these instructions and the memory |
| `00:05:36.960 - 00:05:39.189` | locations that they refer to have to be |
| `00:05:39.199 - 00:05:42.070` | in the user's virtual address space |
| `00:05:42.080 - 00:05:45.749` | likewise down here after we restore satp |
| `00:05:45.759 - 00:05:47.270` | we're running in the user's address |
| `00:05:47.280 - 00:05:50.150` | space so the instructions |
| `00:05:50.160 - 00:05:53.350` | have to be in that address space and the |
| `00:05:53.360 - 00:05:56.070` | memory locations from which we |
| `00:05:56.080 - 00:05:59.029` | get the old register contents also needs |
| `00:05:59.039 - 00:06:01.830` | to be in the user's address space |
| `00:06:01.840 - 00:06:03.590` | so let's look at the user's address |
| `00:06:03.600 - 00:06:04.950` | space |
| `00:06:04.960 - 00:06:07.430` | we showed this picture earlier but the |
| `00:06:07.440 - 00:06:09.270` | the user's virtual address space |
| `00:06:09.280 - 00:06:11.029` | contains the |
| `00:06:11.039 - 00:06:13.350` | code and the data of the program as well |
| `00:06:13.360 - 00:06:16.150` | as a page for stack and maybe some pages |
| `00:06:16.160 - 00:06:17.430` | for a heap |
| `00:06:17.440 - 00:06:19.670` | and then up at the very top we have this |
| `00:06:19.680 - 00:06:22.230` | thing called the trampoline page |
| `00:06:22.240 - 00:06:26.629` | and we also have a trap frame page |
| `00:06:26.639 - 00:06:29.430` | these pages are in every |
| `00:06:29.440 - 00:06:31.029` | virtual address space |
| `00:06:31.039 - 00:06:33.270` | and they're at the exact same location |
| `00:06:33.280 - 00:06:36.710` | namely the top two pages |
| `00:06:36.720 - 00:06:38.950` | what goes on below that is you know |
| `00:06:38.960 - 00:06:40.790` | different between you know depends on |
| `00:06:40.800 - 00:06:42.629` | the user program so |
| `00:06:42.639 - 00:06:46.390` | where exactly the stack page is can vary |
| `00:06:46.400 - 00:06:48.550` | but these two pages are always at the |
| `00:06:48.560 - 00:06:51.029` | same location |
| `00:06:51.039 - 00:06:51.909` | the |
| `00:06:51.919 - 00:06:54.309` | users page table will mark these two |
| `00:06:54.319 - 00:06:55.510` | pages as |
| `00:06:55.520 - 00:06:58.950` | not accessible in user mode |
| `00:06:58.960 - 00:07:01.189` | so if the user code tries to access them |
| `00:07:01.199 - 00:07:03.350` | it will get an error so they're sort of |
| `00:07:03.360 - 00:07:04.790` | invisible |
| `00:07:04.800 - 00:07:07.670` | to the user code |
| `00:07:07.680 - 00:07:09.589` | the trampoline page will contain code so |
| `00:07:09.599 - 00:07:11.589` | it's marked it's marked readable and |
| `00:07:11.599 - 00:07:13.990` | executable and the trap frame page will |
| `00:07:14.000 - 00:07:16.390` | contain data so it's marked readable and |
| `00:07:16.400 - 00:07:19.189` | writable |
| `00:07:19.199 - 00:07:23.110` | okay so here is another picture showing |
| `00:07:23.120 - 00:07:25.189` | what's going on and i'll come back to |
| `00:07:25.199 - 00:07:26.870` | this in a second but we've got the |
| `00:07:26.880 - 00:07:29.350` | virtual address space for all of the |
| `00:07:29.360 - 00:07:31.350` | user processes |
| `00:07:31.360 - 00:07:34.390` | there are up to 64 user processes and |
| `00:07:34.400 - 00:07:36.870` | each one will have a trap frame page |
| `00:07:36.880 - 00:07:40.150` | and a trampoline page the trampoline in |
| `00:07:40.160 - 00:07:42.870` | blue the trap frame in red |
| `00:07:42.880 - 00:07:45.110` | there is exactly one virtual address |
| `00:07:45.120 - 00:07:48.150` | space for the kernel and all the kernel |
| `00:07:48.160 - 00:07:51.430` | uh code will use the same address space |
| `00:07:51.440 - 00:07:54.070` | all the cores uh will share this one |
| `00:07:54.080 - 00:07:56.150` | virtual address space |
| `00:07:56.160 - 00:07:58.550` | and it contains a number of things but |
| `00:07:58.560 - 00:07:59.909` | in particular i want to point out it |
| `00:07:59.919 - 00:08:02.230` | contains the trampoline page at the very |
| `00:08:02.240 - 00:08:06.390` | top of the uh address space |
| `00:08:06.400 - 00:08:08.950` | so before i talk more about that let's |
| `00:08:08.960 - 00:08:10.070` | uh |
| `00:08:10.080 - 00:08:12.469` | oops let's uh look at the trampoline |
| `00:08:12.479 - 00:08:13.350` | page |
| `00:08:13.360 - 00:08:16.309` | and the trap frame page |
| `00:08:16.319 - 00:08:19.510` | as i said there is one trampoline page |
| `00:08:19.520 - 00:08:20.550` | and |
| `00:08:20.560 - 00:08:22.469` | it contains code |
| `00:08:22.479 - 00:08:24.830` | and in particular it contains this |
| `00:08:24.840 - 00:08:27.749` | uservac and this user rep |
| `00:08:27.759 - 00:08:30.309` | functions so it contains uservac |
| `00:08:30.319 - 00:08:31.749` | and user wreck these two assembly |
| `00:08:31.759 - 00:08:33.829` | language routines and it contains |
| `00:08:33.839 - 00:08:36.230` | nothing else |
| `00:08:36.240 - 00:08:38.790` | it is mapped into the very same address |
| `00:08:38.800 - 00:08:41.029` | namely the top page of all virtual |
| `00:08:41.039 - 00:08:44.070` | address spaces and by that i mean the 64 |
| `00:08:44.080 - 00:08:46.150` | virtual address spaces for one for each |
| `00:08:46.160 - 00:08:48.310` | of the user processes |
| `00:08:48.320 - 00:08:50.870` | and the kernel address space and as i |
| `00:08:50.880 - 00:08:53.030` | said it's marked readable and executable |
| `00:08:53.040 - 00:08:54.870` | but it cannot be accessed when running |
| `00:08:54.880 - 00:08:56.949` | in user mode |
| `00:08:56.959 - 00:08:59.670` | there is a track frame page |
| `00:08:59.680 - 00:09:02.070` | and each process has its own |
| `00:09:02.080 - 00:09:04.310` | page so each page is different |
| `00:09:04.320 - 00:09:07.110` | there are 64 processes |
| `00:09:07.120 - 00:09:08.470` | some of them may be dormant and not |
| `00:09:08.480 - 00:09:10.870` | executing but there can be at most 64 |
| `00:09:10.880 - 00:09:12.310` | active processes |
| `00:09:12.320 - 00:09:14.790` | and each virtual address space will have |
| `00:09:14.800 - 00:09:16.949` | its own trap frame |
| `00:09:16.959 - 00:09:18.710` | and that will contain the data and in |
| `00:09:18.720 - 00:09:20.230` | particular it will contain the area |
| `00:09:20.240 - 00:09:24.150` | where we will save the users registers |
| `00:09:24.160 - 00:09:27.110` | so marked read writable not accessible |
| `00:09:27.120 - 00:09:29.110` | in user mode so in this picture down |
| `00:09:29.120 - 00:09:31.670` | here i'm showing the top of all the 64 |
| `00:09:31.680 - 00:09:34.070` | virtual address spaces |
| `00:09:34.080 - 00:09:37.509` | for the user processes and the top page |
| `00:09:37.519 - 00:09:40.310` | is the trampoline page and the second to |
| `00:09:40.320 - 00:09:44.070` | the top page is the trap frame page |
| `00:09:44.080 - 00:09:46.070` | and the idea here is that the page table |
| `00:09:46.080 - 00:09:49.030` | maps all of these trap frame pages into |
| `00:09:49.040 - 00:09:51.430` | the same physical page |
| `00:09:51.440 - 00:09:53.190` | sorry these trampoline pages are all |
| `00:09:53.200 - 00:09:55.509` | mapped into the same physical |
| `00:09:55.519 - 00:09:57.030` | address |
| `00:09:57.040 - 00:09:59.750` | but the trap frame pages |
| `00:09:59.760 - 00:10:02.389` | down here in blue are each mapped to a |
| `00:10:02.399 - 00:10:04.949` | different physical page |
| `00:10:04.959 - 00:10:07.670` | so they are not shared |
| `00:10:07.680 - 00:10:09.350` | it's okay to share code because it |
| `00:10:09.360 - 00:10:10.870` | doesn't change and it can be shared |
| `00:10:10.880 - 00:10:12.870` | without any problem but each user |
| `00:10:12.880 - 00:10:15.190` | process will need its own area in which |
| `00:10:15.200 - 00:10:19.350` | to save the registers |
| `00:10:19.360 - 00:10:22.150` | so now let's take a look at this picture |
| `00:10:22.160 - 00:10:23.590` | which is the |
| `00:10:23.600 - 00:10:25.750` | uh |
| `00:10:25.760 - 00:10:28.949` | physical memory on the right hand side |
| `00:10:28.959 - 00:10:31.590` | and the kernel's virtual address space |
| `00:10:31.600 - 00:10:33.190` | on the left hand side |
| `00:10:33.200 - 00:10:34.470` | so let's start out with the physical |
| `00:10:34.480 - 00:10:36.550` | memory it starts at zero and goes all |
| `00:10:36.560 - 00:10:38.870` | the way up to this enormous number of |
| `00:10:38.880 - 00:10:42.550` | 256 exabytes the vast majority of it |
| `00:10:42.560 - 00:10:45.269` | majority of it is unused and not |
| `00:10:45.279 - 00:10:48.710` | allocated nothing is up there |
| `00:10:48.720 - 00:10:51.350` | the computer that xv6 is intended to run |
| `00:10:51.360 - 00:10:55.030` | on will have 128 megabytes of installed |
| `00:10:55.040 - 00:10:55.990` | physical |
| `00:10:56.000 - 00:10:57.269` | main memory |
| `00:10:57.279 - 00:11:01.350` | the ram and that's in this region here |
| `00:11:01.360 - 00:11:04.069` | it will be located at this particular |
| `00:11:04.079 - 00:11:05.030` | address |
| `00:11:05.040 - 00:11:07.350` | which happens to be the two gigabyte |
| `00:11:07.360 - 00:11:08.630` | boundary |
| `00:11:08.640 - 00:11:11.829` | and below that we have space for the |
| `00:11:11.839 - 00:11:16.150` | memory mapped devices so we see the |
| `00:11:16.160 - 00:11:18.310` | serial communication device it gets a |
| `00:11:18.320 - 00:11:21.350` | page we see the disk device that gets a |
| `00:11:21.360 - 00:11:22.550` | page |
| `00:11:22.560 - 00:11:24.630` | we see the platform level interrupt |
| `00:11:24.640 - 00:11:27.590` | controller that gets four megabytes |
| `00:11:27.600 - 00:11:29.190` | and we also see uh |
| `00:11:29.200 - 00:11:32.310` | core local interrupt controller |
| `00:11:32.320 - 00:11:34.310` | down here is the boot rom after the |
| `00:11:34.320 - 00:11:36.230` | kernel is executing the |
| `00:11:36.240 - 00:11:38.949` | the booting process is over so this is |
| `00:11:38.959 - 00:11:42.630` | not accessed at all by the kernel |
| `00:11:42.640 - 00:11:44.310` | now over on the left hand side we see |
| `00:11:44.320 - 00:11:46.949` | the virtual address space for the kernel |
| `00:11:46.959 - 00:11:48.069` | and |
| `00:11:48.079 - 00:11:50.310` | the first thing to note is that |
| `00:11:50.320 - 00:11:53.509` | all the physical memory is direct mapped |
| `00:11:53.519 - 00:11:54.629` | into |
| `00:11:54.639 - 00:11:56.629` | the virtual address space |
| `00:11:56.639 - 00:11:58.470` | that means the kernel can provide an |
| `00:11:58.480 - 00:12:00.550` | address and it doesn't need to really |
| `00:12:00.560 - 00:12:01.910` | make a distinction between whether it's |
| `00:12:01.920 - 00:12:03.910` | a virtual address or physical memory |
| `00:12:03.920 - 00:12:05.110` | address |
| `00:12:05.120 - 00:12:07.590` | the same number can be used |
| `00:12:07.600 - 00:12:10.310` | for physical locations even though |
| `00:12:10.320 - 00:12:12.470` | virtual addressing is turned on it will |
| `00:12:12.480 - 00:12:14.150` | go to the same spot |
| `00:12:14.160 - 00:12:17.030` | and likewise all the stuff down here |
| `00:12:17.040 - 00:12:18.870` | is direct mapped |
| `00:12:18.880 - 00:12:21.190` | so to access the virtual |
| `00:12:21.200 - 00:12:22.790` | disk and to access the serial |
| `00:12:22.800 - 00:12:25.030` | communication device and the platform |
| `00:12:25.040 - 00:12:26.949` | level interrupt controller |
| `00:12:26.959 - 00:12:28.710` | the kernel can just use physical |
| `00:12:28.720 - 00:12:30.470` | addresses and since they're direct |
| `00:12:30.480 - 00:12:32.470` | mapped |
| `00:12:32.480 - 00:12:34.949` | there won't be a problem |
| `00:12:34.959 - 00:12:38.550` | the core local interrupt controller is |
| `00:12:38.560 - 00:12:40.870` | only accessed in machine mode |
| `00:12:40.880 - 00:12:43.030` | remember that when we're in machine mode |
| `00:12:43.040 - 00:12:45.190` | there is no virtual addressing page the |
| `00:12:45.200 - 00:12:48.310` | page table is not active and we only use |
| `00:12:48.320 - 00:12:50.550` | physical addresses |
| `00:12:50.560 - 00:12:52.150` | the core local interrupt controller is |
| `00:12:52.160 - 00:12:53.990` | only accessed uh |
| `00:12:54.000 - 00:12:56.790` | in machine mode so there's not actually |
| `00:12:56.800 - 00:12:58.310` | a need for it to be mapped into the |
| `00:12:58.320 - 00:13:00.710` | virtual address space |
| `00:13:00.720 - 00:13:02.389` | now the other thing we want to talk |
| `00:13:02.399 - 00:13:04.470` | about is what's going on up here |
| `00:13:04.480 - 00:13:06.629` | at the top of the virtual address space |
| `00:13:06.639 - 00:13:09.269` | at 256 gigabytes |
| `00:13:09.279 - 00:13:10.710` | minus one page |
| `00:13:10.720 - 00:13:13.509` | we have the trampoline page |
| `00:13:13.519 - 00:13:15.670` | and then we have a number of |
| `00:13:15.680 - 00:13:17.990` | pages called stack pages kernel stack |
| `00:13:18.000 - 00:13:18.949` | pages |
| `00:13:18.959 - 00:13:20.870` | there is one for each user process so |
| `00:13:20.880 - 00:13:23.590` | there are 64 of these things |
| `00:13:23.600 - 00:13:25.110` | all of these pages the trampoline and |
| `00:13:25.120 - 00:13:26.870` | the in the stack pages are separated by |
| `00:13:26.880 - 00:13:28.550` | a guard page |
| `00:13:28.560 - 00:13:30.150` | that guard page is |
| `00:13:30.160 - 00:13:31.670` | not mapped |
| `00:13:31.680 - 00:13:33.750` | it has it's not readable writable or |
| `00:13:33.760 - 00:13:34.949` | executable |
| `00:13:34.959 - 00:13:37.269` | so any attempt to access it will cause |
| `00:13:37.279 - 00:13:40.310` | an error and that just catches any stack |
| `00:13:40.320 - 00:13:46.470` | overflow from the kernel stack areas |
| `00:13:46.480 - 00:13:49.030` | within the kernel uh in the physical |
| `00:13:49.040 - 00:13:51.430` | main memory we're going to see the |
| `00:13:51.440 - 00:13:54.790` | kernel code here and the read-only data |
| `00:13:54.800 - 00:13:57.509` | followed by the read-write data for the |
| `00:13:57.519 - 00:13:59.670` | kernel this would be the variables that |
| `00:13:59.680 - 00:14:02.389` | are used in the kernel and then above |
| `00:14:02.399 - 00:14:04.470` | that we have the rest of uh physical |
| `00:14:04.480 - 00:14:05.829` | memory |
| `00:14:05.839 - 00:14:08.389` | and that will be |
| `00:14:08.399 - 00:14:11.030` | used for the page allocator |
| `00:14:11.040 - 00:14:13.350` | we have these two functions k alec |
| `00:14:13.360 - 00:14:15.110` | and k free |
| `00:14:15.120 - 00:14:17.269` | and these |
| `00:14:17.279 - 00:14:19.269` | this memory here is initially divided |
| `00:14:19.279 - 00:14:21.829` | into pages and those pages are all kept |
| `00:14:21.839 - 00:14:23.430` | on a free list |
| `00:14:23.440 - 00:14:26.069` | every time we call the k alec function |
| `00:14:26.079 - 00:14:28.870` | it will allocate a page of free memory |
| `00:14:28.880 - 00:14:30.870` | from that free list so one of these |
| `00:14:30.880 - 00:14:33.350` | pages will be allocated for use by |
| `00:14:33.360 - 00:14:34.470` | something |
| `00:14:34.480 - 00:14:36.310` | and then when we're done using that page |
| `00:14:36.320 - 00:14:38.870` | we can call the k-free routine |
| `00:14:38.880 - 00:14:40.550` | and it will return it |
| `00:14:40.560 - 00:14:43.030` | to the free area here and it will be put |
| `00:14:43.040 - 00:14:46.310` | back on the free list and used uh the |
| `00:14:46.320 - 00:14:48.389` | next time we need a page |
| `00:14:48.399 - 00:14:50.389` | so the most of the |
| `00:14:50.399 - 00:14:53.189` | physical memory will be |
| `00:14:53.199 - 00:14:55.509` | occupied with this uh in this |
| `00:14:55.519 - 00:14:58.230` | page region that's used by k alec and k |
| `00:14:58.240 - 00:15:01.990` | free |
| `00:15:02.000 - 00:15:05.910` | so now let's take a look at |
| `00:15:05.920 - 00:15:07.430` | the |
| `00:15:07.440 - 00:15:09.910` | this picture here which attempts to show |
| `00:15:09.920 - 00:15:12.069` | things a little bit differently again |
| `00:15:12.079 - 00:15:15.030` | we've got physical memory here somewhere |
| `00:15:15.040 - 00:15:17.189` | and we've got over here the virtual |
| `00:15:17.199 - 00:15:20.150` | address spaces we have 64 user processes |
| `00:15:20.160 - 00:15:21.750` | so i'm showing only three of them here |
| `00:15:21.760 - 00:15:23.350` | but there's a virtual address space for |
| `00:15:23.360 - 00:15:24.150` | each |
| `00:15:24.160 - 00:15:26.870` | of the user processes there's exactly |
| `00:15:26.880 - 00:15:29.829` | one virtual address space for the kernel |
| `00:15:29.839 - 00:15:31.910` | so all the cores will share this one |
| `00:15:31.920 - 00:15:34.949` | virtual address space and this one page |
| `00:15:34.959 - 00:15:37.350` | table for the kernel |
| `00:15:37.360 - 00:15:40.629` | we see the trampoline pages in blue here |
| `00:15:40.639 - 00:15:42.470` | at the top of all of the virtual outer |
| `00:15:42.480 - 00:15:43.509` | spaces |
| `00:15:43.519 - 00:15:45.509` | and at the top of each |
| `00:15:45.519 - 00:15:46.949` | of the user mode |
| `00:15:46.959 - 00:15:49.590` | virtual outer spaces there is a trap |
| `00:15:49.600 - 00:15:51.430` | frame shown in red |
| `00:15:51.440 - 00:15:53.030` | now these pages are mapped somewhere |
| `00:15:53.040 - 00:15:55.430` | into physical memory |
| `00:15:55.440 - 00:15:57.189` | the trampoline page |
| `00:15:57.199 - 00:15:59.269` | will be |
| `00:15:59.279 - 00:16:00.389` | mapped into |
| `00:16:00.399 - 00:16:03.509` | code so it's a page that's actually part |
| `00:16:03.519 - 00:16:04.470` | of the |
| `00:16:04.480 - 00:16:07.590` | uh kernel's text area so |
| `00:16:07.600 - 00:16:10.629` | the kernel code is in this area here |
| `00:16:10.639 - 00:16:12.870` | in physical memory here so the |
| `00:16:12.880 - 00:16:14.710` | trampoline page will be mapped somewhere |
| `00:16:14.720 - 00:16:18.069` | into the text area |
| `00:16:18.079 - 00:16:20.389` | the trap frame pages |
| `00:16:20.399 - 00:16:22.389` | will be allocated |
| `00:16:22.399 - 00:16:24.470` | when the kernel starts up and they will |
| `00:16:24.480 - 00:16:26.870` | be allocated somewhere in the free |
| `00:16:26.880 - 00:16:29.110` | region so they will be allocated |
| `00:16:29.120 - 00:16:31.910` | somewhere in this region here |
| `00:16:31.920 - 00:16:33.749` | where the pages that are used by k alec |
| `00:16:33.759 - 00:16:36.389` | and k free are kept |
| `00:16:36.399 - 00:16:38.389` | and so i'm showing them maps somewhere |
| `00:16:38.399 - 00:16:40.790` | in that region of course all the |
| `00:16:40.800 - 00:16:43.189` | physical memory is direct map so the |
| `00:16:43.199 - 00:16:44.949` | kernel can access them directly if it |
| `00:16:44.959 - 00:16:46.470` | needs to |
| `00:16:46.480 - 00:16:48.870` | but they the page tables for each one of |
| `00:16:48.880 - 00:16:51.670` | the user processes will map those trap |
| `00:16:51.680 - 00:16:53.189` | frame pages |
| `00:16:53.199 - 00:16:54.310` | into |
| `00:16:54.320 - 00:16:57.030` | one of the pages that was obtained with |
| `00:16:57.040 - 00:17:00.710` | a call to k alec |
| `00:17:00.720 - 00:17:02.389` | we've also got these |
| `00:17:02.399 - 00:17:04.630` | kernel stack pages up here |
| `00:17:04.640 - 00:17:05.510` | okay |
| `00:17:05.520 - 00:17:08.549` | for each of the 64 processes we need a |
| `00:17:08.559 - 00:17:10.630` | kernel stack |
| `00:17:10.640 - 00:17:11.909` | and |
| `00:17:11.919 - 00:17:15.189` | that is because here |
| `00:17:15.199 - 00:17:16.829` | when we |
| `00:17:16.839 - 00:17:19.510` | uh first go into |
| `00:17:19.520 - 00:17:22.870` | kernel mode the trap occurs and we begin |
| `00:17:22.880 - 00:17:24.309` | executing |
| `00:17:24.319 - 00:17:27.029` | we save the user registers and we load |
| `00:17:27.039 - 00:17:29.190` | the kernel registers |
| `00:17:29.200 - 00:17:30.789` | each process |
| `00:17:30.799 - 00:17:33.350` | will need a separate stack obviously two |
| `00:17:33.360 - 00:17:35.590` | separate threads cannot share the same |
| `00:17:35.600 - 00:17:38.789` | stack they each need an individual |
| `00:17:38.799 - 00:17:39.750` | stack |
| `00:17:39.760 - 00:17:42.230` | so each one of the 64 |
| `00:17:42.240 - 00:17:44.390` | threads that are associated with the 64 |
| `00:17:44.400 - 00:17:47.669` | user processes will have its own stack |
| `00:17:47.679 - 00:17:49.190` | so when the |
| `00:17:49.200 - 00:17:51.750` | user mode code switches over |
| `00:17:51.760 - 00:17:52.710` | into |
| `00:17:52.720 - 00:17:53.909` | uh |
| `00:17:53.919 - 00:17:56.310` | executing in kernel mode |
| `00:17:56.320 - 00:17:59.510` | it will need to access its kernel stack |
| `00:17:59.520 - 00:18:02.390` | and so here we have one stack page for |
| `00:18:02.400 - 00:18:04.230` | each of the 64 |
| `00:18:04.240 - 00:18:06.230` | user processes |
| `00:18:06.240 - 00:18:07.750` | at startup |
| `00:18:07.760 - 00:18:09.590` | there are 64 pages that are allocated |
| `00:18:09.600 - 00:18:10.950` | from the free pool |
| `00:18:10.960 - 00:18:12.870` | by calling k alex |
| `00:18:12.880 - 00:18:15.510` | and each time chaolic is called |
| `00:18:15.520 - 00:18:17.909` | that page is mapped into one of these |
| `00:18:17.919 - 00:18:19.270` | regions up here |
| `00:18:19.280 - 00:18:21.669` | and here i'm showing the guard pages |
| `00:18:21.679 - 00:18:24.870` | separating those pages |
| `00:18:24.880 - 00:18:26.390` | okay now i think we're ready to actually |
| `00:18:26.400 - 00:18:29.190` | look at the code for |
| `00:18:29.200 - 00:18:32.470` | mem layout dot h |
| `00:18:32.480 - 00:18:35.029` | so uh we've got just two pages here |
| `00:18:35.039 - 00:18:36.549` | let's start with this one we've got some |
| `00:18:36.559 - 00:18:37.990` | comments |
| `00:18:38.000 - 00:18:40.950` | and we begin with the address of |
| `00:18:40.960 - 00:18:44.390` | the serial device so that's this address |
| `00:18:44.400 - 00:18:45.270` | here |
| `00:18:45.280 - 00:18:46.789` | and so you see that address and that's |
| `00:18:46.799 - 00:18:50.070` | just defined with a define constant here |
| `00:18:50.080 - 00:18:52.950` | and then we have the page for the disk |
| `00:18:52.960 - 00:18:56.070` | device and that is shown right here so |
| `00:18:56.080 - 00:18:58.789` | that's this address here that we're uh |
| `00:18:58.799 - 00:19:00.870` | defining |
| `00:19:00.880 - 00:19:03.669` | this is the core local interrupter |
| `00:19:03.679 - 00:19:06.150` | and it's mapped into some particular |
| `00:19:06.160 - 00:19:08.710` | address and that's uh |
| `00:19:08.720 - 00:19:12.710` | shown here okay it's uh not used when |
| `00:19:12.720 - 00:19:14.950` | we're executing in kernel mode but it is |
| `00:19:14.960 - 00:19:17.190` | used in machine mode so we give it an |
| `00:19:17.200 - 00:19:19.270` | address here |
| `00:19:19.280 - 00:19:22.390` | that will be used for timers and timer |
| `00:19:22.400 - 00:19:24.789` | interrupts those are those are |
| `00:19:24.799 - 00:19:26.470` | handled the the timer interrupt is |
| `00:19:26.480 - 00:19:28.150` | handled with code that runs in machine |
| `00:19:28.160 - 00:19:29.190` | mode |
| `00:19:29.200 - 00:19:30.310` | and |
| `00:19:30.320 - 00:19:32.230` | the |
| `00:19:32.240 - 00:19:34.390` | device that's creating those interrupts |
| `00:19:34.400 - 00:19:35.909` | is is |
| `00:19:35.919 - 00:19:38.310` | accessed here now these memory map |
| `00:19:38.320 - 00:19:40.470` | devices have what are called |
| `00:19:40.480 - 00:19:42.549` | register sometimes hardware registers to |
| `00:19:42.559 - 00:19:44.230` | be not to be confused with the general |
| `00:19:44.240 - 00:19:45.750` | purpose registers |
| `00:19:45.760 - 00:19:47.190` | in the core |
| `00:19:47.200 - 00:19:49.590` | so |
| `00:19:49.600 - 00:19:51.830` | that's what's going on here there is one |
| `00:19:51.840 - 00:19:53.750` | register called m-time |
| `00:19:53.760 - 00:19:56.150` | okay and where is that located well it's |
| `00:19:56.160 - 00:19:58.549` | at this starting location plus some |
| `00:19:58.559 - 00:20:00.470` | constant and that |
| `00:20:00.480 - 00:20:03.270` | hardware register contains uh the number |
| `00:20:03.280 - 00:20:06.390` | of cycles since uh the boot occurred |
| `00:20:06.400 - 00:20:08.870` | so we the kernel code can read |
| `00:20:08.880 - 00:20:09.909` | from that |
| `00:20:09.919 - 00:20:11.110` | address |
| `00:20:11.120 - 00:20:13.590` | and effectively goes to this device and |
| `00:20:13.600 - 00:20:16.630` | gets uh the current value so that device |
| `00:20:16.640 - 00:20:18.710` | is constantly updating the value that's |
| `00:20:18.720 - 00:20:20.470` | stored at this |
| `00:20:20.480 - 00:20:22.950` | so-called memory location we can just |
| `00:20:22.960 - 00:20:24.070` | read it |
| `00:20:24.080 - 00:20:28.149` | when we need the current time |
| `00:20:28.159 - 00:20:32.390` | this one here in time compare |
| `00:20:32.400 - 00:20:35.510` | that is a function recall that with the |
| `00:20:35.520 - 00:20:37.750` | c preprocessor it matters whether you've |
| `00:20:37.760 - 00:20:40.310` | got a space here or not |
| `00:20:40.320 - 00:20:42.070` | if we have a space then this is just |
| `00:20:42.080 - 00:20:44.470` | what gets substituted if you do not have |
| `00:20:44.480 - 00:20:46.789` | a space then this is a parameter or an |
| `00:20:46.799 - 00:20:49.110` | argument if you will and |
| `00:20:49.120 - 00:20:51.350` | this is a defining a function |
| `00:20:51.360 - 00:20:52.549` | so given |
| `00:20:52.559 - 00:20:55.029` | a particular |
| `00:20:55.039 - 00:20:56.789` | core number |
| `00:20:56.799 - 00:21:00.070` | we will have the expression here |
| `00:21:00.080 - 00:21:00.950` | that |
| `00:21:00.960 - 00:21:03.190` | computes the address of |
| `00:21:03.200 - 00:21:04.230` | one of |
| `00:21:04.240 - 00:21:07.510` | the registers there are eight cores |
| `00:21:07.520 - 00:21:09.270` | and so there are eight hardware |
| `00:21:09.280 - 00:21:10.390` | registers |
| `00:21:10.400 - 00:21:12.390` | and where are they located well the |
| `00:21:12.400 - 00:21:15.350` | starting address plus some value and |
| `00:21:15.360 - 00:21:17.909` | each register is 8 bytes so this |
| `00:21:17.919 - 00:21:20.390` | expression here computes the address of |
| `00:21:20.400 - 00:21:23.110` | one of these hardware registers |
| `00:21:23.120 - 00:21:25.830` | this register will be loaded by the |
| `00:21:25.840 - 00:21:27.510` | kernel |
| `00:21:27.520 - 00:21:29.990` | and it will tell when to give the next |
| `00:21:30.000 - 00:21:32.149` | interrupt so the kernel writes to this |
| `00:21:32.159 - 00:21:34.470` | location and then when an interrupt |
| `00:21:34.480 - 00:21:36.070` | sorry when that time |
| `00:21:36.080 - 00:21:37.750` | is reached an interrupt will be |
| `00:21:37.760 - 00:21:40.870` | generated automatically by this device |
| `00:21:40.880 - 00:21:41.750` | and |
| `00:21:41.760 - 00:21:43.270` | for the platform level interrupt |
| `00:21:43.280 - 00:21:44.950` | controller we've got a similar kind of |
| `00:21:44.960 - 00:21:46.310` | thing going on we've got a starting |
| `00:21:46.320 - 00:21:47.350` | address |
| `00:21:47.360 - 00:21:49.590` | and then we've got some various uh |
| `00:21:49.600 - 00:21:51.270` | different functions and you can see |
| `00:21:51.280 - 00:21:52.630` | we've got some different hardware |
| `00:21:52.640 - 00:21:54.070` | registers here |
| `00:21:54.080 - 00:21:57.430` | defined as a function of the core number |
| `00:21:57.440 - 00:22:00.070` | okay heart id is just the number zero |
| `00:22:00.080 - 00:22:01.350` | through seven |
| `00:22:01.360 - 00:22:03.830` | of the core and these |
| `00:22:03.840 - 00:22:06.390` | expressions compute various addresses |
| `00:22:06.400 - 00:22:08.230` | within that device |
| `00:22:08.240 - 00:22:10.630` | okay moving on to the second page of mim |
| `00:22:10.640 - 00:22:11.830` | layout |
| `00:22:11.840 - 00:22:14.390` | we have |
| `00:22:14.400 - 00:22:17.590` | a definition of where the kernel |
| `00:22:17.600 - 00:22:19.510` | is to be loaded as i said this number |
| `00:22:19.520 - 00:22:20.549` | here is |
| `00:22:20.559 - 00:22:22.789` | two gigabytes that's |
| `00:22:22.799 - 00:22:25.110` | the kernel base right here where the |
| `00:22:25.120 - 00:22:27.990` | kernel is loaded |
| `00:22:28.000 - 00:22:30.710` | we also have the top of physical |
| `00:22:30.720 - 00:22:34.789` | main memory 128 megabytes of main memory |
| `00:22:34.799 - 00:22:35.909` | past |
| `00:22:35.919 - 00:22:38.549` | the starting location is the top of |
| `00:22:38.559 - 00:22:40.710` | physical main memory fizz top |
| `00:22:40.720 - 00:22:43.029` | is so that's giving this address right |
| `00:22:43.039 - 00:22:46.470` | here |
| `00:22:46.480 - 00:22:48.390` | this constant trampoline gives the |
| `00:22:48.400 - 00:22:51.510` | address of the trampoline page which is |
| `00:22:51.520 - 00:22:54.590` | the maximum virtual address |
| `00:22:54.600 - 00:22:58.789` | 256 gigabytes less one page so that's |
| `00:22:58.799 - 00:23:01.110` | this this point right here |
| `00:23:01.120 - 00:23:02.789` | and then finally we have the address of |
| `00:23:02.799 - 00:23:04.549` | the stack pages |
| `00:23:04.559 - 00:23:06.470` | okay that's a function |
| `00:23:06.480 - 00:23:08.630` | given a process number |
| `00:23:08.640 - 00:23:09.590` | 0 |
| `00:23:09.600 - 00:23:11.110` | through 63 |
| `00:23:11.120 - 00:23:14.310` | we compute an address here and you can |
| `00:23:14.320 - 00:23:15.750` | work out this uh |
| `00:23:15.760 - 00:23:17.430` | algebra here but basically we're doing |
| `00:23:17.440 - 00:23:19.190` | it in units of two pages because of the |
| `00:23:19.200 - 00:23:20.710` | card pages |
| `00:23:20.720 - 00:23:23.430` | so every two pages we have the address |
| `00:23:23.440 - 00:23:26.310` | of a stack page |
| `00:23:26.320 - 00:23:28.390` | and finally we have |
| `00:23:28.400 - 00:23:30.310` | a constant trap frame |
| `00:23:30.320 - 00:23:34.310` | which is just the address of the trap |
| `00:23:34.320 - 00:23:37.350` | frame page so it's just the |
| `00:23:37.360 - 00:23:40.070` | address of the |
| `00:23:40.080 - 00:23:41.830` | trampoline page |
| `00:23:41.840 - 00:23:45.350` | minus 4096 which is this value right |
| `00:23:45.360 - 00:23:47.590` | here so that's what's going on with this |
| `00:23:47.600 - 00:23:49.669` | definition here |
| `00:23:49.679 - 00:23:51.909` | okay that's it for memory layout |
| `00:23:51.919 - 00:23:55.640` | see you in the next video |
