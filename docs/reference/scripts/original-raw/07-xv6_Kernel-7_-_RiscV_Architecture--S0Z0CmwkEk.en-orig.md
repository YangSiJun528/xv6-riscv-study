# xv6 Kernel-7: RISC-V Architecture

| Field | Value |
| --- | --- |
| Video ID | `-S0Z0CmwkEk` |
| URL | <https://www.youtube.com/watch?v=-S0Z0CmwkEk> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.520 - 00:00:03.830` | this video is part of a series on the |
| `00:00:03.840 - 00:00:06.869` | xv6 operating system kernel |
| `00:00:06.879 - 00:00:09.509` | in this and the next couple of videos |
| `00:00:09.519 - 00:00:11.509` | i'll be talking about the risc-5 |
| `00:00:11.519 - 00:00:13.669` | processor architecture |
| `00:00:13.679 - 00:00:15.829` | i'll be going into it in enough detail |
| `00:00:15.839 - 00:00:17.910` | so that you can understand the xv6 |
| `00:00:17.920 - 00:00:20.070` | kernel but i won't be going into great |
| `00:00:20.080 - 00:00:21.429` | detail |
| `00:00:21.439 - 00:00:23.429` | i won't assume that you have any prior |
| `00:00:23.439 - 00:00:26.870` | knowledge about the risc-5 architecture |
| `00:00:26.880 - 00:00:28.390` | but i'm not going to go into it in |
| `00:00:28.400 - 00:00:30.790` | enough enough depth for you to become an |
| `00:00:30.800 - 00:00:34.470` | assembly language programmer |
| `00:00:34.480 - 00:00:37.670` | let's start with the registers |
| `00:00:37.680 - 00:00:40.229` | the risk 5 instruction set architecture |
| `00:00:40.239 - 00:00:43.590` | has 32 general purpose registers |
| `00:00:43.600 - 00:00:46.470` | and a program counter all of which are |
| `00:00:46.480 - 00:00:48.549` | 64 bits |
| `00:00:48.559 - 00:00:50.869` | so let me go through these registers |
| `00:00:50.879 - 00:00:53.110` | here are the register names |
| `00:00:53.120 - 00:00:55.590` | and we start with the first one which is |
| `00:00:55.600 - 00:00:58.069` | hardwired to be zero so we won't need to |
| `00:00:58.079 - 00:00:59.750` | save it when we do |
| `00:00:59.760 - 00:01:03.349` | context switches between processes |
| `00:01:03.359 - 00:01:05.509` | ra is the return address |
| `00:01:05.519 - 00:01:08.469` | the risk 5 uses kind of a clever system |
| `00:01:08.479 - 00:01:11.750` | for function calls and returns |
| `00:01:11.760 - 00:01:14.070` | when a call is made the return address |
| `00:01:14.080 - 00:01:16.149` | is saved in this register rather than |
| `00:01:16.159 - 00:01:18.149` | being pushed onto the stack |
| `00:01:18.159 - 00:01:20.710` | and then the return instruction simply |
| `00:01:20.720 - 00:01:22.870` | copies the value from the return address |
| `00:01:22.880 - 00:01:25.749` | register back into the pc |
| `00:01:25.759 - 00:01:27.350` | so for many |
| `00:01:27.360 - 00:01:29.910` | functions we don't need to access main |
| `00:01:29.920 - 00:01:31.990` | memory at all |
| `00:01:32.000 - 00:01:34.550` | sp is the stack pointer the stack grows |
| `00:01:34.560 - 00:01:36.149` | downward |
| `00:01:36.159 - 00:01:38.950` | tp is the so-called thread pointer |
| `00:01:38.960 - 00:01:42.149` | in the xv6 kernel it will contain the |
| `00:01:42.159 - 00:01:46.230` | core number which is the heart id the |
| `00:01:46.240 - 00:01:49.109` | hardware thread so thread here i guess |
| `00:01:49.119 - 00:01:52.630` | might stand for hardware thread |
| `00:01:52.640 - 00:01:55.749` | gp is a global pointer it's used by the |
| `00:01:55.759 - 00:02:00.230` | compiler and will be set and not changed |
| `00:02:00.240 - 00:02:01.830` | basically it just is used to make |
| `00:02:01.840 - 00:02:02.950` | accessing |
| `00:02:02.960 - 00:02:05.350` | global and shared variables |
| `00:02:05.360 - 00:02:07.590` | quick and efficient |
| `00:02:07.600 - 00:02:10.229` | then we have the a registers which are |
| `00:02:10.239 - 00:02:13.589` | used to pass arguments to functions |
| `00:02:13.599 - 00:02:16.710` | a0 is used for a return value if the |
| `00:02:16.720 - 00:02:19.350` | function returns something |
| `00:02:19.360 - 00:02:22.710` | the a registers and these t registers |
| `00:02:22.720 - 00:02:26.150` | can be used freely within a function to |
| `00:02:26.160 - 00:02:28.949` | do whatever the function needs to do |
| `00:02:28.959 - 00:02:31.750` | in addition we have 12 so-called callee |
| `00:02:31.760 - 00:02:34.390` | saved registers |
| `00:02:34.400 - 00:02:37.589` | the caller will assume that any function |
| `00:02:37.599 - 00:02:40.309` | it calls will not modify these |
| `00:02:40.319 - 00:02:43.430` | so if a function wants to use any of |
| `00:02:43.440 - 00:02:45.030` | these registers |
| `00:02:45.040 - 00:02:48.470` | then that function must save them before |
| `00:02:48.480 - 00:02:51.270` | using them so typically they would |
| `00:02:51.280 - 00:02:53.110` | be pushed onto the stack |
| `00:02:53.120 - 00:02:55.350` | and that function must restore these |
| `00:02:55.360 - 00:02:58.630` | registers before returning |
| `00:02:58.640 - 00:03:01.589` | so this is the entire state these uh 31 |
| `00:03:01.599 - 00:03:04.390` | registers here and the program counter |
| `00:03:04.400 - 00:03:06.309` | it's the entire state of a user mode |
| `00:03:06.319 - 00:03:08.070` | thread |
| `00:03:08.080 - 00:03:10.550` | user code has no access to the status |
| `00:03:10.560 - 00:03:13.030` | register so the status register is |
| `00:03:13.040 - 00:03:17.030` | invisible in user mode code |
| `00:03:17.040 - 00:03:18.869` | at each context switch |
| `00:03:18.879 - 00:03:19.910` | that is |
| `00:03:19.920 - 00:03:22.630` | when we are ending one process's time |
| `00:03:22.640 - 00:03:25.910` | slice and about to begin the time slice |
| `00:03:25.920 - 00:03:27.670` | of another process |
| `00:03:27.680 - 00:03:29.670` | the kernel will need to save |
| `00:03:29.680 - 00:03:32.390` | the state of the previous thread okay |
| `00:03:32.400 - 00:03:34.949` | the previous processes registers will be |
| `00:03:34.959 - 00:03:37.270` | saved somewhere by the kernel |
| `00:03:37.280 - 00:03:39.350` | and then before the next time slice |
| `00:03:39.360 - 00:03:40.789` | begins |
| `00:03:40.799 - 00:03:43.110` | the colonel will need to load the |
| `00:03:43.120 - 00:03:45.910` | registers to constitute the state of the |
| `00:03:45.920 - 00:03:49.670` | next process |
| `00:03:49.680 - 00:03:51.509` | at any time the |
| `00:03:51.519 - 00:03:52.470` | risk |
| `00:03:52.480 - 00:03:56.309` | processor is in one of three modes |
| `00:03:56.319 - 00:03:57.990` | there's machine mode |
| `00:03:58.000 - 00:03:58.869` | m |
| `00:03:58.879 - 00:04:01.270` | there's supervisor mode |
| `00:04:01.280 - 00:04:03.350` | s and there's user mode |
| `00:04:03.360 - 00:04:04.470` | u |
| `00:04:04.480 - 00:04:05.830` | so each core |
| `00:04:05.840 - 00:04:08.229` | has its own set of registers and each |
| `00:04:08.239 - 00:04:11.990` | core is running in exactly one mode at |
| `00:04:12.000 - 00:04:15.350` | any one time |
| `00:04:15.360 - 00:04:17.030` | machine mode is the |
| `00:04:17.040 - 00:04:19.909` | highest and most powerful mode with the |
| `00:04:19.919 - 00:04:21.830` | greatest privileges |
| `00:04:21.840 - 00:04:23.430` | and after the |
| `00:04:23.440 - 00:04:26.310` | core starts up or or is reset |
| `00:04:26.320 - 00:04:29.350` | it will go into machine mode |
| `00:04:29.360 - 00:04:32.230` | in the xv6 kernel machine mode is not |
| `00:04:32.240 - 00:04:34.550` | used very much |
| `00:04:34.560 - 00:04:37.350` | there is some code that is executing in |
| `00:04:37.360 - 00:04:40.150` | machine mode on startup that does some |
| `00:04:40.160 - 00:04:43.430` | initialization stuff the other thing |
| `00:04:43.440 - 00:04:44.550` | that |
| `00:04:44.560 - 00:04:46.870` | we need machine mode for is for dealing |
| `00:04:46.880 - 00:04:49.270` | with timer interrupts |
| `00:04:49.280 - 00:04:52.550` | timer interrupts require the handler to |
| `00:04:52.560 - 00:04:55.030` | run in machine mode so |
| `00:04:55.040 - 00:04:57.110` | there is a little bit of code that will |
| `00:04:57.120 - 00:04:59.030` | run in machine mode |
| `00:04:59.040 - 00:05:01.749` | uh that code immediately converts the |
| `00:05:01.759 - 00:05:04.070` | interrupt into an interrupt to the |
| `00:05:04.080 - 00:05:06.150` | supervisor mode code |
| `00:05:06.160 - 00:05:08.710` | and then it basically returns quickly so |
| `00:05:08.720 - 00:05:10.390` | this handler |
| `00:05:10.400 - 00:05:12.870` | is short and quick and it's just |
| `00:05:12.880 - 00:05:13.990` | basically |
| `00:05:14.000 - 00:05:16.310` | converting the interrupt the timer |
| `00:05:16.320 - 00:05:17.749` | interrupt into |
| `00:05:17.759 - 00:05:19.350` | an interrupt to code running in |
| `00:05:19.360 - 00:05:21.029` | supervisor mode |
| `00:05:21.039 - 00:05:22.629` | so supervisor mode is where all the |
| `00:05:22.639 - 00:05:25.029` | action happens all the kernel code runs |
| `00:05:25.039 - 00:05:27.909` | in this mode |
| `00:05:27.919 - 00:05:29.510` | of course some instructions are |
| `00:05:29.520 - 00:05:31.670` | privileged and they cannot be executed |
| `00:05:31.680 - 00:05:33.830` | in user mode so privileged instructions |
| `00:05:33.840 - 00:05:36.150` | can only be executed in machine and |
| `00:05:36.160 - 00:05:39.110` | supervisor mode and finally of course we |
| `00:05:39.120 - 00:05:40.790` | have user mode |
| `00:05:40.800 - 00:05:43.110` | all user code runs in this mode in fact |
| `00:05:43.120 - 00:05:45.909` | we can say that if code runs in |
| `00:05:45.919 - 00:05:49.029` | supervisor mode it is kernel code and |
| `00:05:49.039 - 00:05:51.990` | vice versa likewise any and all code |
| `00:05:52.000 - 00:05:54.390` | that runs in user mode is |
| `00:05:54.400 - 00:05:56.230` | a user code it's part of a user |
| `00:05:56.240 - 00:05:58.870` | application |
| `00:05:58.880 - 00:06:00.790` | so privilege instructions are of course |
| `00:06:00.800 - 00:06:04.550` | not allowed in user mode and if a user |
| `00:06:04.560 - 00:06:06.550` | program tries to do something that's |
| `00:06:06.560 - 00:06:09.590` | privileged it will cause a trap and the |
| `00:06:09.600 - 00:06:13.029` | kernel will abort that process |
| `00:06:13.039 - 00:06:14.790` | okay now let's move on to supervisor |
| `00:06:14.800 - 00:06:17.590` | mode and machine mode |
| `00:06:17.600 - 00:06:18.710` | we'll |
| `00:06:18.720 - 00:06:20.469` | not be going over the individual |
| `00:06:20.479 - 00:06:22.710` | instructions of the |
| `00:06:22.720 - 00:06:25.270` | risc-5 processor of course we have the |
| `00:06:25.280 - 00:06:27.430` | usual instructions |
| `00:06:27.440 - 00:06:29.029` | add and |
| `00:06:29.039 - 00:06:30.469` | move and |
| `00:06:30.479 - 00:06:32.950` | load data from memory and store data to |
| `00:06:32.960 - 00:06:36.950` | memory and test and branch and so on but |
| `00:06:36.960 - 00:06:41.110` | i won't be going over those |
| `00:06:41.120 - 00:06:42.390` | in addition to the general purpose |
| `00:06:42.400 - 00:06:43.990` | registers there are |
| `00:06:44.000 - 00:06:46.830` | a number of control and status registers |
| `00:06:46.840 - 00:06:48.390` | csrs |
| `00:06:48.400 - 00:06:50.710` | in fact the architecture accommodates up |
| `00:06:50.720 - 00:06:53.589` | to 4k of these registers that's a lot of |
| `00:06:53.599 - 00:06:55.909` | registers there's a lot of complexity |
| `00:06:55.919 - 00:06:56.790` | here |
| `00:06:56.800 - 00:06:59.830` | and i'm going to sort of present a |
| `00:06:59.840 - 00:07:02.230` | simplified model hopefully everything i |
| `00:07:02.240 - 00:07:04.469` | say is correct and true but i'm going to |
| `00:07:04.479 - 00:07:06.710` | leave out some details that i think are |
| `00:07:06.720 - 00:07:08.950` | not necessary for understanding the xy6 |
| `00:07:08.960 - 00:07:10.070` | kernel |
| `00:07:10.080 - 00:07:14.830` | i'm only going to discuss 19 of these |
| `00:07:14.840 - 00:07:17.510` | csrs we have a couple of privileged |
| `00:07:17.520 - 00:07:18.870` | instructions we have three privileged |
| `00:07:18.880 - 00:07:20.629` | instructions that are important |
| `00:07:20.639 - 00:07:23.510` | we have one to read a csr |
| `00:07:23.520 - 00:07:27.270` | okay so this example will take the value |
| `00:07:27.280 - 00:07:28.150` | in |
| `00:07:28.160 - 00:07:30.070` | a cs register which happens to be called |
| `00:07:30.080 - 00:07:32.550` | s status and move it into the a0 |
| `00:07:32.560 - 00:07:33.990` | register |
| `00:07:34.000 - 00:07:36.950` | we have a write instruction |
| `00:07:36.960 - 00:07:39.350` | takes the value in some register and |
| `00:07:39.360 - 00:07:41.990` | moves it into a some |
| `00:07:42.000 - 00:07:43.909` | control and status register in this |
| `00:07:43.919 - 00:07:46.629` | example we're writing into the s status |
| `00:07:46.639 - 00:07:47.909` | register |
| `00:07:47.919 - 00:07:51.670` | and finally we have a swap instruction |
| `00:07:51.680 - 00:07:53.430` | so here you see |
| `00:07:53.440 - 00:07:55.909` | register a0 it happens to be the same so |
| `00:07:55.919 - 00:07:58.629` | this is going to simultaneously |
| `00:07:58.639 - 00:08:01.270` | write from or copy the value from m |
| `00:08:01.280 - 00:08:04.070` | scratch to a0 and copy from |
| `00:08:04.080 - 00:08:06.869` | this register here which is also a 0 |
| `00:08:06.879 - 00:08:07.749` | into |
| `00:08:07.759 - 00:08:09.430` | the csr |
| `00:08:09.440 - 00:08:12.629` | this is done atomically |
| `00:08:12.639 - 00:08:14.950` | so either both of these copies happen or |
| `00:08:14.960 - 00:08:17.270` | neither of them happen |
| `00:08:17.280 - 00:08:18.950` | well i don't think there's any way that |
| `00:08:18.960 - 00:08:20.469` | they would not happen they both happen |
| `00:08:20.479 - 00:08:22.950` | simultaneously without any interleaving |
| `00:08:22.960 - 00:08:25.110` | of other instructions |
| `00:08:25.120 - 00:08:27.830` | okay so here are the 19 registers and |
| `00:08:27.840 - 00:08:29.189` | i'll just uh |
| `00:08:29.199 - 00:08:31.189` | mention that some of them |
| `00:08:31.199 - 00:08:33.750` | are used and accessible only in machine |
| `00:08:33.760 - 00:08:34.870` | mode |
| `00:08:34.880 - 00:08:35.829` | and |
| `00:08:35.839 - 00:08:37.350` | other others |
| `00:08:37.360 - 00:08:38.870` | the ones that start with s are |
| `00:08:38.880 - 00:08:40.630` | accessible in both machine and |
| `00:08:40.640 - 00:08:46.949` | supervisor mode |
| `00:08:46.959 - 00:08:50.710` | one register contains the core number |
| `00:08:50.720 - 00:08:53.509` | another is the status register |
| `00:08:53.519 - 00:08:54.630` | we have |
| `00:08:54.640 - 00:08:56.630` | the trap vector which is basically the |
| `00:08:56.640 - 00:08:58.470` | address of the handler that will be |
| `00:08:58.480 - 00:09:02.470` | invoked when a trap occurs |
| `00:09:02.480 - 00:09:05.110` | when a trap occurs the previous program |
| `00:09:05.120 - 00:09:08.710` | counter will be saved in the epc |
| `00:09:08.720 - 00:09:10.389` | exception previous |
| `00:09:10.399 - 00:09:12.630` | uh exception program counter |
| `00:09:12.640 - 00:09:13.829` | register |
| `00:09:13.839 - 00:09:16.470` | also the cause of the trap will be saved |
| `00:09:16.480 - 00:09:18.949` | in an s-cause register and if there's |
| `00:09:18.959 - 00:09:21.750` | any additional information there's an st |
| `00:09:21.760 - 00:09:24.550` | val register which might be updated as |
| `00:09:24.560 - 00:09:26.710` | well |
| `00:09:26.720 - 00:09:28.550` | there's also an m cause register but we |
| `00:09:28.560 - 00:09:30.470` | don't care about that one |
| `00:09:30.480 - 00:09:32.230` | there's a work register that can be used |
| `00:09:32.240 - 00:09:34.070` | by our |
| `00:09:34.080 - 00:09:38.630` | trap handlers satp is fairly important |
| `00:09:38.640 - 00:09:41.350` | it will contain a pointer to the page |
| `00:09:41.360 - 00:09:44.150` | table so atp is address translation |
| `00:09:44.160 - 00:09:46.150` | pointer |
| `00:09:46.160 - 00:09:48.470` | and then there are a number of registers |
| `00:09:48.480 - 00:09:51.670` | that allow us to |
| `00:09:51.680 - 00:09:54.070` | enable interrupts selectively both in |
| `00:09:54.080 - 00:09:56.949` | machine mode and in supervisor mode |
| `00:09:56.959 - 00:09:59.190` | we can also find out which registers are |
| `00:09:59.200 - 00:10:01.350` | sorry which interrupts are pending at |
| `00:10:01.360 - 00:10:02.710` | any one point |
| `00:10:02.720 - 00:10:05.269` | and we'll see that later |
| `00:10:05.279 - 00:10:06.630` | finally |
| `00:10:06.640 - 00:10:08.230` | exceptions and interrupts can be |
| `00:10:08.240 - 00:10:10.550` | delegated from machine mode to |
| `00:10:10.560 - 00:10:12.550` | supervisor mode i'll talk about that |
| `00:10:12.560 - 00:10:13.590` | later |
| `00:10:13.600 - 00:10:15.110` | and then we've got something called |
| `00:10:15.120 - 00:10:17.030` | physical memory protection |
| `00:10:17.040 - 00:10:21.269` | and we'll talk about that |
| `00:10:21.279 - 00:10:23.590` | so before i get going any further i want |
| `00:10:23.600 - 00:10:24.389` | to |
| `00:10:24.399 - 00:10:27.590` | discuss some terminology |
| `00:10:27.600 - 00:10:29.269` | we have exceptions and interrupts and |
| `00:10:29.279 - 00:10:31.910` | these are both kinds of traps so trap is |
| `00:10:31.920 - 00:10:33.910` | a more general |
| `00:10:33.920 - 00:10:36.150` | term and we talk about having a trap |
| `00:10:36.160 - 00:10:39.110` | handler to either handle an exception or |
| `00:10:39.120 - 00:10:41.829` | to handle an interrupt |
| `00:10:41.839 - 00:10:43.750` | exceptions are synchronous which means |
| `00:10:43.760 - 00:10:45.590` | they are caused by some |
| `00:10:45.600 - 00:10:47.509` | instruction you know some instruction is |
| `00:10:47.519 - 00:10:50.150` | executed and that instruction has an |
| `00:10:50.160 - 00:10:52.230` | exception |
| `00:10:52.240 - 00:10:54.630` | the system call instruction |
| `00:10:54.640 - 00:10:57.350` | happens to be named e-call in risk five |
| `00:10:57.360 - 00:10:59.670` | and that would cause an exception |
| `00:10:59.680 - 00:11:01.750` | also any instruction that causes some |
| `00:11:01.760 - 00:11:03.670` | kind of an error |
| `00:11:03.680 - 00:11:05.509` | will cause an exception |
| `00:11:05.519 - 00:11:07.670` | this could be an illegal instruction or |
| `00:11:07.680 - 00:11:09.990` | an alignment error or some kind of page |
| `00:11:10.000 - 00:11:11.590` | fault or memory access something like |
| `00:11:11.600 - 00:11:14.470` | that |
| `00:11:14.480 - 00:11:17.030` | on the other hand are asynchronous they |
| `00:11:17.040 - 00:11:18.870` | come from someplace besides the current |
| `00:11:18.880 - 00:11:20.310` | instruction |
| `00:11:20.320 - 00:11:21.350` | so |
| `00:11:21.360 - 00:11:22.870` | one example of that is the timer |
| `00:11:22.880 - 00:11:23.990` | interrupt |
| `00:11:24.000 - 00:11:26.069` | another example is |
| `00:11:26.079 - 00:11:28.389` | when a device interrupts so we have two |
| `00:11:28.399 - 00:11:30.470` | two devices we have the |
| `00:11:30.480 - 00:11:31.829` | asynchronous |
| `00:11:31.839 - 00:11:33.590` | received transmit unit |
| `00:11:33.600 - 00:11:36.550` | uh which is the serial communication |
| `00:11:36.560 - 00:11:38.710` | device and we have the disk and those |
| `00:11:38.720 - 00:11:41.509` | can interrupt at any time |
| `00:11:41.519 - 00:11:43.910` | the timer interrupt will only interrupt |
| `00:11:43.920 - 00:11:45.829` | code running in machine mode or i should |
| `00:11:45.839 - 00:11:46.630` | say |
| `00:11:46.640 - 00:11:49.509` | the handler has to run in machine mode |
| `00:11:49.519 - 00:11:51.829` | it can happen at any time of course but |
| `00:11:51.839 - 00:11:53.509` | the handler happens to run in machine |
| `00:11:53.519 - 00:11:55.269` | mode and we'll talk about how that's |
| `00:11:55.279 - 00:11:56.629` | dealt with |
| `00:11:56.639 - 00:11:58.629` | uh later |
| `00:11:58.639 - 00:11:59.670` | finally we have something called a |
| `00:11:59.680 - 00:12:02.230` | software interrupt |
| `00:12:02.240 - 00:12:03.269` | uh |
| `00:12:03.279 - 00:12:05.910` | when a timer interrupt occurs if the |
| `00:12:05.920 - 00:12:07.670` | handler is running in machine mode it |
| `00:12:07.680 - 00:12:10.629` | needs to notify the supervisor |
| `00:12:10.639 - 00:12:13.030` | code it needs to notify the kernel so it |
| `00:12:13.040 - 00:12:15.030` | causes what's known as a software |
| `00:12:15.040 - 00:12:17.110` | interrupt at the supervisor level |
| `00:12:17.120 - 00:12:19.190` | so a software interrupt will be caused |
| `00:12:19.200 - 00:12:21.990` | by code running in machine mode whenever |
| `00:12:22.000 - 00:12:24.790` | a timer interrupt occurs and then |
| `00:12:24.800 - 00:12:27.269` | that software interrupt will be handled |
| `00:12:27.279 - 00:12:28.710` | by |
| `00:12:28.720 - 00:12:31.430` | code that runs in supervisor mode |
| `00:12:31.440 - 00:12:34.870` | that is the kernel has a |
| `00:12:34.880 - 00:12:37.110` | interrupt handler for the software |
| `00:12:37.120 - 00:12:38.870` | interrupt that will |
| `00:12:38.880 - 00:12:40.949` | do what it needs to do for a timer |
| `00:12:40.959 - 00:12:42.710` | interrupt |
| `00:12:42.720 - 00:12:44.550` | finally in this video let me talk about |
| `00:12:44.560 - 00:12:46.710` | two registers uh three registers that |
| `00:12:46.720 - 00:12:49.910` | are fairly easy to discuss |
| `00:12:49.920 - 00:12:51.509` | the heart is |
| `00:12:51.519 - 00:12:53.829` | synonymous in these videos at least with |
| `00:12:53.839 - 00:12:57.030` | the core or the processor number so this |
| `00:12:57.040 - 00:13:00.230` | register m heart id contains the core |
| `00:13:00.240 - 00:13:02.150` | number each core has its own separate |
| `00:13:02.160 - 00:13:04.829` | set of registers |
| `00:13:04.839 - 00:13:09.269` | and the heart id register is hardwired |
| `00:13:09.279 - 00:13:11.670` | it cannot be modified and it can be |
| `00:13:11.680 - 00:13:13.750` | queried to find out what core the code |
| `00:13:13.760 - 00:13:15.750` | is running on |
| `00:13:15.760 - 00:13:17.030` | the kernel will |
| `00:13:17.040 - 00:13:18.470` | immediately move |
| `00:13:18.480 - 00:13:21.750` | this value into the tp register during |
| `00:13:21.760 - 00:13:25.030` | startup and it will never change at that |
| `00:13:25.040 - 00:13:27.430` | point it will stay constant so there's a |
| `00:13:27.440 - 00:13:29.269` | function cpu id |
| `00:13:29.279 - 00:13:31.350` | which is used all over the place and all |
| `00:13:31.360 - 00:13:33.509` | it does is return the value that's in |
| `00:13:33.519 - 00:13:36.629` | this tp register |
| `00:13:36.639 - 00:13:38.949` | now i say tp never changes it never |
| `00:13:38.959 - 00:13:41.670` | changes in the kernel however |
| `00:13:41.680 - 00:13:44.150` | all registers are accessible in user |
| `00:13:44.160 - 00:13:47.590` | mode code and the user code could easily |
| `00:13:47.600 - 00:13:51.189` | overwrite tp with some other value |
| `00:13:51.199 - 00:13:54.150` | whenever the kernel goes from kernel |
| `00:13:54.160 - 00:13:57.990` | code into user code it first saves all |
| `00:13:58.000 - 00:14:00.629` | the kernels registers including tp |
| `00:14:00.639 - 00:14:03.269` | then the user code has a time slice and |
| `00:14:03.279 - 00:14:05.110` | runs until something happens some kind |
| `00:14:05.120 - 00:14:06.470` | of trap occurs |
| `00:14:06.480 - 00:14:08.629` | and at that point the kernel becomes |
| `00:14:08.639 - 00:14:10.150` | active and the first thing it will do is |
| `00:14:10.160 - 00:14:13.110` | restore its own registers including tp |
| `00:14:13.120 - 00:14:17.189` | so tp never changes within the kernel |
| `00:14:17.199 - 00:14:19.350` | we've also got these two registers |
| `00:14:19.360 - 00:14:21.509` | there's a system for |
| `00:14:21.519 - 00:14:23.509` | physical memory protection |
| `00:14:23.519 - 00:14:28.069` | it is not used in the xv6 kernel |
| `00:14:28.079 - 00:14:29.910` | but we need to deal with it anyway since |
| `00:14:29.920 - 00:14:32.710` | it's there basically it's going to |
| `00:14:32.720 - 00:14:35.030` | be able to limit access to physical |
| `00:14:35.040 - 00:14:37.269` | memory for any code running in |
| `00:14:37.279 - 00:14:39.829` | supervisor or user mode so machine mode |
| `00:14:39.839 - 00:14:41.509` | can basically |
| `00:14:41.519 - 00:14:44.870` | uh partition memory into |
| `00:14:44.880 - 00:14:48.069` | areas and keep the code that's in those |
| `00:14:48.079 - 00:14:50.310` | areas out of you know keep it from |
| `00:14:50.320 - 00:14:52.790` | interfering with other code |
| `00:14:52.800 - 00:14:55.189` | the intended usage for this system is to |
| `00:14:55.199 - 00:14:57.430` | support secure bootstrapping |
| `00:14:57.440 - 00:14:58.710` | and |
| `00:14:58.720 - 00:15:03.509` | hypervisor code hypervisor is some |
| `00:15:03.519 - 00:15:06.230` | code like an operating system where each |
| `00:15:06.240 - 00:15:07.990` | of the user |
| `00:15:08.000 - 00:15:10.389` | threads the users are |
| `00:15:10.399 - 00:15:14.069` | host os's so the idea with a hypervisor |
| `00:15:14.079 - 00:15:16.389` | is it's going to use this physical |
| `00:15:16.399 - 00:15:18.069` | memory protection scheme |
| `00:15:18.079 - 00:15:21.110` | to isolate the memory of one |
| `00:15:21.120 - 00:15:24.629` | operating system's uh use from |
| `00:15:24.639 - 00:15:26.550` | another operating system |
| `00:15:26.560 - 00:15:29.430` | in our case with xv6 this is loaded |
| `00:15:29.440 - 00:15:30.870` | during startup |
| `00:15:30.880 - 00:15:33.189` | while we're running in machine mode and |
| `00:15:33.199 - 00:15:35.590` | basically it marks all of physical main |
| `00:15:35.600 - 00:15:37.910` | memory as fully accessible |
| `00:15:37.920 - 00:15:40.230` | read write and execute and then these |
| `00:15:40.240 - 00:15:43.430` | registers are not changed after that |
| `00:15:43.440 - 00:15:45.430` | okay in the next video i'm going to |
| `00:15:45.440 - 00:15:47.670` | going to talk about the status registers |
| `00:15:47.680 - 00:15:50.629` | and page tables so i'll see you in the |
| `00:15:50.639 - 00:15:53.880` | next video |
