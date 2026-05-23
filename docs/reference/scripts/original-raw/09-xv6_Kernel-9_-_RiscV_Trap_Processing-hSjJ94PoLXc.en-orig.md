# xv6 Kernel-9: RISC-V Trap Processing

| Field | Value |
| --- | --- |
| Video ID | `hSjJ94PoLXc` |
| URL | <https://www.youtube.com/watch?v=hSjJ94PoLXc> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.439 - 00:00:03.189` | this video is part of a series on the |
| `00:00:03.199 - 00:00:06.389` | xv6 operating system kernel |
| `00:00:06.399 - 00:00:07.990` | in this video i'm going to talk about |
| `00:00:08.000 - 00:00:10.150` | the risc-5 architecture |
| `00:00:10.160 - 00:00:11.990` | and i'm going to focus on the status |
| `00:00:12.000 - 00:00:13.110` | register |
| `00:00:13.120 - 00:00:15.749` | and how traps are handled by the |
| `00:00:15.759 - 00:00:20.310` | hardware |
| `00:00:20.320 - 00:00:23.189` | so recall that we use this terminology |
| `00:00:23.199 - 00:00:25.269` | there are two kinds of traps |
| `00:00:25.279 - 00:00:27.189` | there are exceptions and there are |
| `00:00:27.199 - 00:00:29.189` | interrupts |
| `00:00:29.199 - 00:00:31.750` | a system call is a type of exception and |
| `00:00:31.760 - 00:00:33.990` | any kind of a program error is an |
| `00:00:34.000 - 00:00:36.310` | exception as well |
| `00:00:36.320 - 00:00:39.270` | the system call instruction in risk five |
| `00:00:39.280 - 00:00:41.110` | is e-call |
| `00:00:41.120 - 00:00:42.389` | and |
| `00:00:42.399 - 00:00:43.990` | program errors can include things like |
| `00:00:44.000 - 00:00:46.709` | illegal instructions or alignment errors |
| `00:00:46.719 - 00:00:48.229` | and so on |
| `00:00:48.239 - 00:00:51.189` | we also have devices that can interrupt |
| `00:00:51.199 - 00:00:52.709` | the interrupt can occur when we're |
| `00:00:52.719 - 00:00:55.189` | executing in user mode or supervisor |
| `00:00:55.199 - 00:00:56.150` | mode |
| `00:00:56.160 - 00:00:59.110` | and regardless of what mode we're in |
| `00:00:59.120 - 00:01:02.630` | when it happens we begin executing some |
| `00:01:02.640 - 00:01:07.750` | handler code running in supervisor mode |
| `00:01:07.760 - 00:01:10.710` | so st vec is a control and status |
| `00:01:10.720 - 00:01:12.230` | register |
| `00:01:12.240 - 00:01:15.030` | it contains a pointer to the handler |
| `00:01:15.040 - 00:01:16.870` | code in particular it contains the |
| `00:01:16.880 - 00:01:19.670` | address of the first instruction of some |
| `00:01:19.680 - 00:01:22.469` | code that will handle whatever trap has |
| `00:01:22.479 - 00:01:23.749` | occurred |
| `00:01:23.759 - 00:01:26.950` | there are two pieces of code in xv6 that |
| `00:01:26.960 - 00:01:27.990` | handle |
| `00:01:28.000 - 00:01:28.950` | traps |
| `00:01:28.960 - 00:01:31.350` | kernelvec handles traps that occur while |
| `00:01:31.360 - 00:01:33.510` | executing in supervisor mode |
| `00:01:33.520 - 00:01:35.830` | and uservac handles traps |
| `00:01:35.840 - 00:01:37.030` | that occur |
| `00:01:37.040 - 00:01:41.190` | when running user mode code |
| `00:01:41.200 - 00:01:43.670` | so let's begin with the status word and |
| `00:01:43.680 - 00:01:45.429` | i'm going to focus on |
| `00:01:45.439 - 00:01:47.510` | the status word that |
| `00:01:47.520 - 00:01:49.350` | code that's running in supervisor mode |
| `00:01:49.360 - 00:01:52.870` | would see the s status word |
| `00:01:52.880 - 00:01:56.230` | the xv6 kernel runs almost entirely in |
| `00:01:56.240 - 00:01:58.310` | supervisor mode so this is our status |
| `00:01:58.320 - 00:02:01.030` | word that we're concerned with |
| `00:02:01.040 - 00:02:02.709` | things are a little bit more complicated |
| `00:02:02.719 - 00:02:05.109` | than i'm going to go into but i'm only |
| `00:02:05.119 - 00:02:07.030` | going to focus on three bits and i think |
| `00:02:07.040 - 00:02:09.029` | that's all you need to understand the |
| `00:02:09.039 - 00:02:11.430` | xv6 kernel |
| `00:02:11.440 - 00:02:12.790` | there's a bit that controls whether |
| `00:02:12.800 - 00:02:15.270` | interrupts are enabled or not |
| `00:02:15.280 - 00:02:18.150` | so that's called s i e for interrupts |
| `00:02:18.160 - 00:02:19.910` | enabled |
| `00:02:19.920 - 00:02:22.150` | when an interrupt occurs we need to save |
| `00:02:22.160 - 00:02:25.190` | the value of this bit and it's saved in |
| `00:02:25.200 - 00:02:27.390` | another bit in the status word called |
| `00:02:27.400 - 00:02:30.630` | s-p-i-e previous interrupts-enabled bit |
| `00:02:30.640 - 00:02:32.470` | we also need to remember what mode we |
| `00:02:32.480 - 00:02:35.350` | were executing in when the trap occurred |
| `00:02:35.360 - 00:02:37.350` | it could be we were executing in user |
| `00:02:37.360 - 00:02:39.910` | mode or it could be that this was an |
| `00:02:39.920 - 00:02:41.430` | exception that occurred in supervisor |
| `00:02:41.440 - 00:02:45.190` | mode |
| `00:02:45.200 - 00:02:47.509` | the trap can occur as a result of an |
| `00:02:47.519 - 00:02:50.630` | exception or an interrupt obviously if |
| `00:02:50.640 - 00:02:52.630` | it's an interrupt then |
| `00:02:52.640 - 00:02:54.869` | the previous value was enabled otherwise |
| `00:02:54.879 - 00:02:56.949` | we wouldn't be handling it |
| `00:02:56.959 - 00:02:58.550` | but it might be that we have an |
| `00:02:58.560 - 00:03:00.869` | exception that's occurring while |
| `00:03:00.879 - 00:03:03.270` | interrupts are disabled so in any case |
| `00:03:03.280 - 00:03:06.070` | we need to save the previous value of |
| `00:03:06.080 - 00:03:07.350` | this bit |
| `00:03:07.360 - 00:03:09.270` | so that when we return from the |
| `00:03:09.280 - 00:03:11.509` | interrupted code we can restore the bit |
| `00:03:11.519 - 00:03:16.309` | to whatever it was before |
| `00:03:16.319 - 00:03:17.990` | one technique in kernels is to |
| `00:03:18.000 - 00:03:20.550` | temporarily change the interrupt enabled |
| `00:03:20.560 - 00:03:21.350` | bit |
| `00:03:21.360 - 00:03:23.750` | to disable all interrupts |
| `00:03:23.760 - 00:03:26.470` | and that can prevent other threads from |
| `00:03:26.480 - 00:03:28.949` | interfering with one particular critical |
| `00:03:28.959 - 00:03:30.550` | section |
| `00:03:30.560 - 00:03:32.869` | however xv6 is a multi-core operating |
| `00:03:32.879 - 00:03:34.229` | system so |
| `00:03:34.239 - 00:03:36.390` | it doesn't have any impact on what the |
| `00:03:36.400 - 00:03:38.390` | other cores are doing |
| `00:03:38.400 - 00:03:41.110` | they may be changing memory uh |
| `00:03:41.120 - 00:03:43.270` | simultaneously so |
| `00:03:43.280 - 00:03:46.149` | we have locks to handle those situations |
| `00:03:46.159 - 00:03:48.149` | however interrupt disabling does occur |
| `00:03:48.159 - 00:03:50.470` | in the kernel |
| `00:03:50.480 - 00:03:53.589` | okay what happens when a trap occurs |
| `00:03:53.599 - 00:03:55.030` | well the first question to ask is |
| `00:03:55.040 - 00:03:56.869` | whether interrupts are enabled or |
| `00:03:56.879 - 00:03:57.990` | disabled |
| `00:03:58.000 - 00:04:00.470` | if interrupts are disabled then the trap |
| `00:04:00.480 - 00:04:02.070` | will remain pending |
| `00:04:02.080 - 00:04:03.830` | that is it will not be immediately |
| `00:04:03.840 - 00:04:05.509` | processed by the hardware and the |
| `00:04:05.519 - 00:04:07.910` | handler code will not run until at some |
| `00:04:07.920 - 00:04:10.149` | later time the kernel re-enables |
| `00:04:10.159 - 00:04:11.990` | interrupts and and |
| `00:04:12.000 - 00:04:14.949` | then at that point the trap crossing |
| `00:04:14.959 - 00:04:16.310` | will occur |
| `00:04:16.320 - 00:04:18.150` | now if it's an exception it's going to |
| `00:04:18.160 - 00:04:20.310` | be handled immediately regardless of |
| `00:04:20.320 - 00:04:22.390` | whether interrupts are disabled or not |
| `00:04:22.400 - 00:04:25.909` | but in any case when the trap is handled |
| `00:04:25.919 - 00:04:27.830` | the hardware will do this it will do |
| `00:04:27.840 - 00:04:30.310` | these things and then it will resume |
| `00:04:30.320 - 00:04:33.030` | executing so the previous instruction is |
| `00:04:33.040 - 00:04:35.350` | completed and then the hardware will |
| `00:04:35.360 - 00:04:37.510` | save a copy of the program counter |
| `00:04:37.520 - 00:04:38.710` | in |
| `00:04:38.720 - 00:04:40.710` | a control and status register called |
| `00:04:40.720 - 00:04:42.550` | sepc |
| `00:04:42.560 - 00:04:44.870` | then it will branch to the first |
| `00:04:44.880 - 00:04:47.110` | instruction the handler code |
| `00:04:47.120 - 00:04:48.469` | by |
| `00:04:48.479 - 00:04:51.270` | copying the value in s-t back to the |
| `00:04:51.280 - 00:04:53.590` | program counter |
| `00:04:53.600 - 00:04:56.150` | then it will save in the s cause |
| `00:04:56.160 - 00:04:58.790` | register information about what exactly |
| `00:04:58.800 - 00:05:01.430` | happened what caused the trap handler to |
| `00:05:01.440 - 00:05:02.950` | be invoked |
| `00:05:02.960 - 00:05:05.029` | and it may save additional information |
| `00:05:05.039 - 00:05:08.629` | in a csr called st val |
| `00:05:08.639 - 00:05:10.550` | there are several causes that we will be |
| `00:05:10.560 - 00:05:11.909` | concerned with |
| `00:05:11.919 - 00:05:14.469` | in the xv6 kernel |
| `00:05:14.479 - 00:05:16.550` | if it's a system call the cause will be |
| `00:05:16.560 - 00:05:17.670` | eight |
| `00:05:17.680 - 00:05:19.590` | uh if there's an interrupt from an |
| `00:05:19.600 - 00:05:22.870` | external device will be nine |
| `00:05:22.880 - 00:05:25.110` | if there's a software interrupt and i'll |
| `00:05:25.120 - 00:05:26.790` | discuss those in a second but they are |
| `00:05:26.800 - 00:05:28.950` | related to timer interrupts then it will |
| `00:05:28.960 - 00:05:30.550` | be a one |
| `00:05:30.560 - 00:05:32.790` | anything else is a program exception and |
| `00:05:32.800 - 00:05:38.070` | some sort of an error in the code |
| `00:05:38.080 - 00:05:40.629` | the hardware will immediately save the |
| `00:05:40.639 - 00:05:42.550` | previous mode whether we were executing |
| `00:05:42.560 - 00:05:45.110` | in user mode or supervisor mode when the |
| `00:05:45.120 - 00:05:48.070` | exception or interrupt occurred |
| `00:05:48.080 - 00:05:50.310` | that will be saved in this bit here of |
| `00:05:50.320 - 00:05:52.710` | the status word and we will also save |
| `00:05:52.720 - 00:05:54.070` | the previous value of the interrupt |
| `00:05:54.080 - 00:05:55.430` | enabled bit |
| `00:05:55.440 - 00:05:57.990` | in spie |
| `00:05:58.000 - 00:06:01.110` | finally we will disable interrupts and |
| `00:06:01.120 - 00:06:03.110` | change the mode to supervisor if it was |
| `00:06:03.120 - 00:06:04.870` | not already supervisor |
| `00:06:04.880 - 00:06:08.309` | at that point the hardware phase of the |
| `00:06:08.319 - 00:06:10.629` | trap processing is done and we begin |
| `00:06:10.639 - 00:06:12.230` | executing the first instruction of the |
| `00:06:12.240 - 00:06:13.990` | handler code |
| `00:06:14.000 - 00:06:15.909` | the handler code will do a lot of things |
| `00:06:15.919 - 00:06:17.590` | who knows what exactly we'll see that |
| `00:06:17.600 - 00:06:19.590` | with the kernel but at some point in the |
| `00:06:19.600 - 00:06:21.189` | future we'll be ready to return to the |
| `00:06:21.199 - 00:06:22.550` | interrupted code |
| `00:06:22.560 - 00:06:24.870` | for that we will use the risk |
| `00:06:24.880 - 00:06:27.510` | instruction called s ret |
| `00:06:27.520 - 00:06:29.350` | for return from |
| `00:06:29.360 - 00:06:30.870` | supervisor mode |
| `00:06:30.880 - 00:06:32.710` | what this will do is restore the |
| `00:06:32.720 - 00:06:34.550` | interrupt enabled bit from the saved |
| `00:06:34.560 - 00:06:35.909` | copy |
| `00:06:35.919 - 00:06:37.590` | return to the whatever mode we were in |
| `00:06:37.600 - 00:06:38.870` | previously |
| `00:06:38.880 - 00:06:41.270` | and restore the program counter to |
| `00:06:41.280 - 00:06:43.909` | whatever we whatever it was before and |
| `00:06:43.919 - 00:06:45.990` | then we will resume executing the code |
| `00:06:46.000 - 00:06:49.510` | that was interrupted |
| `00:06:49.520 - 00:06:51.189` | now that's what happens in supervisor |
| `00:06:51.199 - 00:06:52.550` | mode and most of the kernel runs in |
| `00:06:52.560 - 00:06:54.550` | supervisor mode but there's a little bit |
| `00:06:54.560 - 00:06:57.589` | of the kernel that runs in machine mode |
| `00:06:57.599 - 00:06:59.270` | and so now i'm going to talk about trap |
| `00:06:59.280 - 00:07:01.830` | processing in machine mode it's |
| `00:07:01.840 - 00:07:04.230` | essentially the same idea |
| `00:07:04.240 - 00:07:07.110` | there's another register called m status |
| `00:07:07.120 - 00:07:09.749` | actually it might be |
| `00:07:09.759 - 00:07:12.150` | a mirror copy of the same register so |
| `00:07:12.160 - 00:07:13.270` | there might be |
| `00:07:13.280 - 00:07:14.629` | it might actually be implemented as one |
| `00:07:14.639 - 00:07:17.029` | register but for all intents and |
| `00:07:17.039 - 00:07:18.790` | purposes you can think of it as a |
| `00:07:18.800 - 00:07:20.629` | separate register |
| `00:07:20.639 - 00:07:21.990` | and like the |
| `00:07:22.000 - 00:07:25.749` | s status register it has bits for |
| `00:07:25.759 - 00:07:28.550` | disabling or enabling interrupts and for |
| `00:07:28.560 - 00:07:30.550` | saving the previous value of the |
| `00:07:30.560 - 00:07:31.909` | interrupts enabled |
| `00:07:31.919 - 00:07:33.830` | bit and finally |
| `00:07:33.840 - 00:07:36.469` | it has a field for saving the previous |
| `00:07:36.479 - 00:07:38.150` | mode level |
| `00:07:38.160 - 00:07:39.830` | we could have been interrupted in user |
| `00:07:39.840 - 00:07:43.029` | mode supervisor mode or machine mode |
| `00:07:43.039 - 00:07:45.749` | so we have two bits to save that |
| `00:07:45.759 - 00:07:47.830` | if we're executing in machine mode then |
| `00:07:47.840 - 00:07:49.909` | we will not be having any interrupt to |
| `00:07:49.919 - 00:07:52.150` | supervisor mode so |
| `00:07:52.160 - 00:07:54.629` | interrupts and traps only go upward |
| `00:07:54.639 - 00:07:55.830` | so |
| `00:07:55.840 - 00:07:57.189` | there is no |
| `00:07:57.199 - 00:07:59.029` | we need two bits for machine mode |
| `00:07:59.039 - 00:08:00.869` | interrupts but for supervisor mode |
| `00:08:00.879 - 00:08:02.869` | interrupts we only need one bit because |
| `00:08:02.879 - 00:08:05.430` | we could not be coming from machine mode |
| `00:08:05.440 - 00:08:08.790` | to supervisor mode |
| `00:08:08.800 - 00:08:10.629` | the only interrupt that we deal with at |
| `00:08:10.639 - 00:08:12.790` | machine mode level is the timer |
| `00:08:12.800 - 00:08:13.990` | interrupt |
| `00:08:14.000 - 00:08:16.550` | and we have a mechanism for delegating |
| `00:08:16.560 - 00:08:17.990` | all other |
| `00:08:18.000 - 00:08:19.909` | traps all other interrupts and |
| `00:08:19.919 - 00:08:22.469` | exceptions to be handled at supervisor |
| `00:08:22.479 - 00:08:23.670` | mode |
| `00:08:23.680 - 00:08:25.830` | for some reason we cannot |
| `00:08:25.840 - 00:08:27.990` | delegate the timer interrupts so |
| `00:08:28.000 - 00:08:29.749` | we're going to see that it gets sort of |
| `00:08:29.759 - 00:08:32.149` | mutated into something called a software |
| `00:08:32.159 - 00:08:33.750` | interrupt |
| `00:08:33.760 - 00:08:36.149` | so interrupts are always enabled at the |
| `00:08:36.159 - 00:08:40.310` | machine mode level so whenever a trap |
| `00:08:40.320 - 00:08:42.469` | sorry whenever a timer interrupt occurs |
| `00:08:42.479 - 00:08:44.310` | it will be handled |
| `00:08:44.320 - 00:08:45.990` | whenever any |
| `00:08:46.000 - 00:08:48.550` | trap of any other kind happens it will |
| `00:08:48.560 - 00:08:50.550` | be automatically delegated to supervisor |
| `00:08:50.560 - 00:08:52.630` | mode and whether or not it's handled |
| `00:08:52.640 - 00:08:54.310` | immediately or whether it becomes |
| `00:08:54.320 - 00:08:56.310` | pending will be determined by whether |
| `00:08:56.320 - 00:08:59.030` | interrupts are enabled or disabled at |
| `00:08:59.040 - 00:09:02.470` | the super supervisor mode level |
| `00:09:02.480 - 00:09:04.630` | when a timer interrupt occurs |
| `00:09:04.640 - 00:09:08.150` | a machine mode handler will run and that |
| `00:09:08.160 - 00:09:09.670` | code will |
| `00:09:09.680 - 00:09:13.030` | create or force or synthesize |
| `00:09:13.040 - 00:09:15.509` | something called a software interrupt |
| `00:09:15.519 - 00:09:17.829` | and that will interrupt the supervisor |
| `00:09:17.839 - 00:09:19.269` | mode code |
| `00:09:19.279 - 00:09:20.310` | and |
| `00:09:20.320 - 00:09:21.030` | the |
| `00:09:21.040 - 00:09:23.190` | handler at machine mode |
| `00:09:23.200 - 00:09:25.350` | will continue running and it will |
| `00:09:25.360 - 00:09:26.870` | re-enable interrupts and return to the |
| `00:09:26.880 - 00:09:28.230` | interrupted code |
| `00:09:28.240 - 00:09:30.230` | if the interrupted code was supervisor |
| `00:09:30.240 - 00:09:33.030` | code it will return to supervisor mode |
| `00:09:33.040 - 00:09:34.630` | if the interrupted code was a user |
| `00:09:34.640 - 00:09:37.269` | program it will return to that |
| `00:09:37.279 - 00:09:39.670` | if interrupts are enabled at supervisor |
| `00:09:39.680 - 00:09:41.590` | level then |
| `00:09:41.600 - 00:09:43.910` | an interrupt will occur immediately at |
| `00:09:43.920 - 00:09:46.710` | that level otherwise if the colonel has |
| `00:09:46.720 - 00:09:49.350` | momentarily disabled interrupts at |
| `00:09:49.360 - 00:09:52.389` | supervisor mode level then the interrupt |
| `00:09:52.399 - 00:09:55.030` | will remain pending until the kernel is |
| `00:09:55.040 - 00:09:57.750` | ready to deal with it |
| `00:09:57.760 - 00:09:59.509` | the trap processing at machine mode |
| `00:09:59.519 - 00:10:00.790` | level happens |
| `00:10:00.800 - 00:10:02.790` | pretty much the same way it's exactly |
| `00:10:02.800 - 00:10:06.150` | the same idea as i said the only cause |
| `00:10:06.160 - 00:10:08.630` | of interrupts are timer interrupts |
| `00:10:08.640 - 00:10:10.310` | every other sort of interrupt is |
| `00:10:10.320 - 00:10:12.150` | delegated at the hardware level |
| `00:10:12.160 - 00:10:14.310` | immediately to supervisor mode and so we |
| `00:10:14.320 - 00:10:16.150` | don't do this processing for anything |
| `00:10:16.160 - 00:10:20.069` | but timer interrupts |
| `00:10:20.079 - 00:10:21.750` | like the st vac |
| `00:10:21.760 - 00:10:25.110` | register there's also an mt vac register |
| `00:10:25.120 - 00:10:27.030` | which contains the address of the |
| `00:10:27.040 - 00:10:30.389` | machine mode trap handler |
| `00:10:30.399 - 00:10:33.430` | in xv6 that code is a function called |
| `00:10:33.440 - 00:10:35.829` | timer vac and that will handle the timer |
| `00:10:35.839 - 00:10:38.230` | interrupts |
| `00:10:38.240 - 00:10:39.990` | the hardware will take the following |
| `00:10:40.000 - 00:10:41.509` | actions after completing the previous |
| `00:10:41.519 - 00:10:43.590` | instruction it will save the program |
| `00:10:43.600 - 00:10:47.670` | counter in a register called mepc |
| `00:10:47.680 - 00:10:50.230` | it will load the program counter with |
| `00:10:50.240 - 00:10:53.509` | mtvec which will force a jump to the |
| `00:10:53.519 - 00:10:55.590` | handler |
| `00:10:55.600 - 00:10:59.430` | the m cause and mt val registers are |
| `00:10:59.440 - 00:11:00.550` | ignored |
| `00:11:00.560 - 00:11:02.310` | it's a timer interrupt and there's no |
| `00:11:02.320 - 00:11:03.829` | further information about it that we're |
| `00:11:03.839 - 00:11:05.509` | interested in |
| `00:11:05.519 - 00:11:07.829` | the hardware will save the previous mode |
| `00:11:07.839 - 00:11:09.670` | whether we were executing in user mode |
| `00:11:09.680 - 00:11:12.550` | supervisor mode or machine mode and |
| `00:11:12.560 - 00:11:15.110` | we'll save that in these two bits |
| `00:11:15.120 - 00:11:17.910` | and then it will save the previous value |
| `00:11:17.920 - 00:11:19.750` | of interrupts enabled |
| `00:11:19.760 - 00:11:21.030` | in this bit |
| `00:11:21.040 - 00:11:24.150` | and it will then disable interrupts |
| `00:11:24.160 - 00:11:26.230` | and switch to machine mode |
| `00:11:26.240 - 00:11:30.470` | we'll then execute the timervec code |
| `00:11:30.480 - 00:11:33.269` | and what will it do well it will |
| `00:11:33.279 - 00:11:35.750` | synthesize or cause an interrupt at the |
| `00:11:35.760 - 00:11:38.470` | supervisor level so it's going to do |
| `00:11:38.480 - 00:11:41.509` | something that will create an interrupt |
| `00:11:41.519 - 00:11:43.990` | that at that level that interrupt is not |
| `00:11:44.000 - 00:11:45.910` | a timer interrupt but it's a so-called |
| `00:11:45.920 - 00:11:47.750` | software interrupt |
| `00:11:47.760 - 00:11:49.030` | and then |
| `00:11:49.040 - 00:11:52.230` | this code which is fairly short will |
| `00:11:52.240 - 00:11:55.350` | execute an instruction called m return |
| `00:11:55.360 - 00:11:58.069` | return for machine mode |
| `00:11:58.079 - 00:12:00.069` | and it will restore the interrupts |
| `00:12:00.079 - 00:12:01.590` | enabled bit |
| `00:12:01.600 - 00:12:03.430` | it will restore the mode to whatever it |
| `00:12:03.440 - 00:12:06.389` | was previously and it will |
| `00:12:06.399 - 00:12:08.310` | restore the program counter thereby |
| `00:12:08.320 - 00:12:09.430` | returning to the code that was |
| `00:12:09.440 - 00:12:10.710` | interrupted |
| `00:12:10.720 - 00:12:12.629` | what's going to happen after that well |
| `00:12:12.639 - 00:12:15.509` | we've raised this software interrupt |
| `00:12:15.519 - 00:12:16.389` | and |
| `00:12:16.399 - 00:12:18.550` | what happens will depend on whether |
| `00:12:18.560 - 00:12:20.790` | interrupts are enabled or disabled at |
| `00:12:20.800 - 00:12:23.590` | the soft at the supervisor mode level |
| `00:12:23.600 - 00:12:26.389` | if the interrupts are disabled then the |
| `00:12:26.399 - 00:12:28.790` | software interrupt will become pending |
| `00:12:28.800 - 00:12:29.670` | and |
| `00:12:29.680 - 00:12:30.870` | it will just have to wait until the |
| `00:12:30.880 - 00:12:33.910` | kernel is ready to enable interrupts |
| `00:12:33.920 - 00:12:35.910` | if interrupts are enabled then the trap |
| `00:12:35.920 - 00:12:37.990` | will occur at supervisor level |
| `00:12:38.000 - 00:12:42.230` | immediately |
| `00:12:42.240 - 00:12:43.030` | so |
| `00:12:43.040 - 00:12:44.389` | we have this |
| `00:12:44.399 - 00:12:46.629` | mie bit |
| `00:12:46.639 - 00:12:49.509` | that enables interrupts at machine mode |
| `00:12:49.519 - 00:12:52.629` | and we have this s s-i-e bit which |
| `00:12:52.639 - 00:12:55.590` | enables or disables interrupts at |
| `00:12:55.600 - 00:12:57.990` | supervisor mode level and these are |
| `00:12:58.000 - 00:13:00.069` | global enabling bits |
| `00:13:00.079 - 00:13:01.750` | there's also so |
| `00:13:01.760 - 00:13:03.590` | the interrupts can be all interrupts can |
| `00:13:03.600 - 00:13:05.990` | be enabled or disabled by changing these |
| `00:13:06.000 - 00:13:08.470` | bits in the status registers |
| `00:13:08.480 - 00:13:10.870` | we also have a facility at |
| `00:13:10.880 - 00:13:12.230` | for doing more |
| `00:13:12.240 - 00:13:14.550` | selective control in the risk |
| `00:13:14.560 - 00:13:17.829` | architecture it's not really needed |
| `00:13:17.839 - 00:13:19.829` | but i want to cover it because we have |
| `00:13:19.839 - 00:13:21.509` | these registers and they are |
| `00:13:21.519 - 00:13:23.110` | touched during |
| `00:13:23.120 - 00:13:24.949` | initialization |
| `00:13:24.959 - 00:13:27.190` | so the mie |
| `00:13:27.200 - 00:13:30.710` | for uh machine mode interrupts enabled |
| `00:13:30.720 - 00:13:32.550` | is a register |
| `00:13:32.560 - 00:13:34.949` | it's rather confusing because there is a |
| `00:13:34.959 - 00:13:39.110` | bit called m-i-e in the status word |
| `00:13:39.120 - 00:13:41.350` | but this is a separate register likewise |
| `00:13:41.360 - 00:13:42.389` | there's a |
| `00:13:42.399 - 00:13:45.110` | bit called s-i-e |
| `00:13:45.120 - 00:13:48.310` | in the status register s-i-e is here and |
| `00:13:48.320 - 00:13:51.110` | we have a register called s i e as well |
| `00:13:51.120 - 00:13:52.790` | but these are separate things |
| `00:13:52.800 - 00:13:55.509` | for the selective control |
| `00:13:55.519 - 00:13:57.030` | we have a bunch of different bits going |
| `00:13:57.040 - 00:13:58.470` | on here but we're only interested in |
| `00:13:58.480 - 00:14:00.949` | this one bit |
| `00:14:00.959 - 00:14:02.949` | this stands for machine mode timer |
| `00:14:02.959 - 00:14:05.670` | interrupt enable and we will set it to |
| `00:14:05.680 - 00:14:08.710` | one during the initialization phase this |
| `00:14:08.720 - 00:14:11.189` | happens in the start function which is |
| `00:14:11.199 - 00:14:13.430` | executed in machine mode |
| `00:14:13.440 - 00:14:14.629` | we also have |
| `00:14:14.639 - 00:14:16.870` | a way to selectively |
| `00:14:16.880 - 00:14:19.030` | enable and disable interrupts at the |
| `00:14:19.040 - 00:14:21.189` | supervisor mode level |
| `00:14:21.199 - 00:14:23.670` | that's taken care of in this sie |
| `00:14:23.680 - 00:14:25.110` | register |
| `00:14:25.120 - 00:14:27.110` | and the bits that we're interested in |
| `00:14:27.120 - 00:14:29.430` | are these three bits |
| `00:14:29.440 - 00:14:32.230` | we have one to enable or disable device |
| `00:14:32.240 - 00:14:33.990` | interrupts that is interrupts from |
| `00:14:34.000 - 00:14:35.670` | hardware devices |
| `00:14:35.680 - 00:14:38.550` | we have one to interrupt sorry to enable |
| `00:14:38.560 - 00:14:39.990` | or disable |
| `00:14:40.000 - 00:14:41.670` | software interrupts |
| `00:14:41.680 - 00:14:43.110` | and we have something for timer |
| `00:14:43.120 - 00:14:45.670` | interrupts but uh i'm not sure |
| `00:14:45.680 - 00:14:48.710` | why that's not usable here we can't seem |
| `00:14:48.720 - 00:14:50.710` | to delegate timer interrupts down here |
| `00:14:50.720 - 00:14:51.509` | but |
| `00:14:51.519 - 00:14:53.430` | so but this bit is uh just want to |
| `00:14:53.440 - 00:14:55.189` | mention it anyway |
| `00:14:55.199 - 00:14:56.310` | and so |
| `00:14:56.320 - 00:14:59.269` | what we'll do in the initialization is |
| `00:14:59.279 - 00:15:02.310` | to set all these bits to one |
| `00:15:02.320 - 00:15:05.670` | we also have a register called sip for |
| `00:15:05.680 - 00:15:08.550` | interrupts pending |
| `00:15:08.560 - 00:15:10.710` | this has the same format so |
| `00:15:10.720 - 00:15:13.509` | when a device is interrupting that bit |
| `00:15:13.519 - 00:15:15.350` | will be changed to one |
| `00:15:15.360 - 00:15:17.509` | when there's an interrupt pending and if |
| `00:15:17.519 - 00:15:19.750` | there are no interrupts it will be zero |
| `00:15:19.760 - 00:15:21.430` | so we have |
| `00:15:21.440 - 00:15:23.590` | the ability to |
| `00:15:23.600 - 00:15:26.550` | force an interrupt to become pending or |
| `00:15:26.560 - 00:15:27.670` | to happen |
| `00:15:27.680 - 00:15:29.829` | by setting this bit to one and that's |
| `00:15:29.839 - 00:15:33.110` | what the timer vec code which is |
| `00:15:33.120 - 00:15:34.710` | executing at machine mode will do it |
| `00:15:34.720 - 00:15:38.230` | will set this bit here to one |
| `00:15:38.240 - 00:15:40.389` | in the sip |
| `00:15:40.399 - 00:15:43.990` | register thereby forcing the |
| `00:15:44.000 - 00:15:46.870` | software interrupt to occur |
| `00:15:46.880 - 00:15:48.470` | so |
| `00:15:48.480 - 00:15:50.069` | i'm saying that here the timer interrupt |
| `00:15:50.079 - 00:15:52.310` | handler which is running in machine mode |
| `00:15:52.320 - 00:15:53.430` | will set the |
| `00:15:53.440 - 00:15:55.430` | ssie bit |
| `00:15:55.440 - 00:15:56.150` | to |
| `00:15:56.160 - 00:15:57.990` | 1 and that will |
| `00:15:58.000 - 00:16:00.790` | force a software interrupt to happen |
| `00:16:00.800 - 00:16:02.389` | at the kernel level |
| `00:16:02.399 - 00:16:03.189` | in |
| `00:16:03.199 - 00:16:08.949` | supervisor mode |
| `00:16:08.959 - 00:16:12.710` | so now we have the ability to delegate |
| `00:16:12.720 - 00:16:15.350` | traps a trap being either an interrupt |
| `00:16:15.360 - 00:16:17.030` | or an exception |
| `00:16:17.040 - 00:16:18.069` | so |
| `00:16:18.079 - 00:16:20.069` | all traps |
| `00:16:20.079 - 00:16:21.829` | in the risk architecture will go to |
| `00:16:21.839 - 00:16:23.910` | machine mode handlers |
| `00:16:23.920 - 00:16:25.749` | but there's also a facility to sort of |
| `00:16:25.759 - 00:16:27.990` | bypass the machine mode handler and go |
| `00:16:28.000 - 00:16:30.069` | straight to a handler that's executing |
| `00:16:30.079 - 00:16:31.990` | at supervisor mode |
| `00:16:32.000 - 00:16:35.030` | level and that's what we do |
| `00:16:35.040 - 00:16:37.829` | xv6 delegates all traps |
| `00:16:37.839 - 00:16:38.629` | to |
| `00:16:38.639 - 00:16:40.230` | supervisor mode |
| `00:16:40.240 - 00:16:41.110` | so |
| `00:16:41.120 - 00:16:42.790` | normally it would be handled by a |
| `00:16:42.800 - 00:16:44.870` | handler running in machine mode but |
| `00:16:44.880 - 00:16:46.230` | we're going to delegate things to |
| `00:16:46.240 - 00:16:47.990` | supervisor mode |
| `00:16:48.000 - 00:16:51.189` | and there are two registers m e |
| `00:16:51.199 - 00:16:54.310` | delegation and m i delegation |
| `00:16:54.320 - 00:16:56.470` | which are used to delegate exceptions |
| `00:16:56.480 - 00:16:58.230` | and interrupts |
| `00:16:58.240 - 00:17:00.069` | so |
| `00:17:00.079 - 00:17:02.710` | xv6 will set these |
| `00:17:02.720 - 00:17:05.669` | bits in these two registers to delegate |
| `00:17:05.679 - 00:17:08.150` | traps so that when |
| `00:17:08.160 - 00:17:10.470` | an interrupt or an exception occurs it |
| `00:17:10.480 - 00:17:11.590` | will be |
| `00:17:11.600 - 00:17:13.750` | immediately handled |
| `00:17:13.760 - 00:17:15.590` | in supervisor mode |
| `00:17:15.600 - 00:17:17.590` | by the kernel code |
| `00:17:17.600 - 00:17:19.510` | for some reason the timer interrupts |
| `00:17:19.520 - 00:17:21.990` | can't be delegated so as i said it's |
| `00:17:22.000 - 00:17:24.069` | handled a bit differently by doing this |
| `00:17:24.079 - 00:17:26.470` | software interrupt thing |
| `00:17:26.480 - 00:17:29.909` | so these two registers me deleg and mi |
| `00:17:29.919 - 00:17:34.710` | deleg are initialized during startup in |
| `00:17:34.720 - 00:17:35.830` | which |
| `00:17:35.840 - 00:17:37.830` | it happens in the start function which |
| `00:17:37.840 - 00:17:39.669` | executes in machine mode |
| `00:17:39.679 - 00:17:42.070` | they are set to one and they never |
| `00:17:42.080 - 00:17:44.470` | change |
| `00:17:44.480 - 00:17:46.630` | when a trap occurs |
| `00:17:46.640 - 00:17:48.470` | because these interrupts and exceptions |
| `00:17:48.480 - 00:17:50.710` | are delegated nothing happens in machine |
| `00:17:50.720 - 00:17:52.310` | mode instead |
| `00:17:52.320 - 00:17:55.029` | it immediately becomes a supervisor |
| `00:17:55.039 - 00:17:58.390` | level interrupt or exception |
| `00:17:58.400 - 00:17:59.830` | so let me just |
| `00:17:59.840 - 00:18:01.270` | point out these |
| `00:18:01.280 - 00:18:03.350` | registers so here is the |
| `00:18:03.360 - 00:18:06.230` | register for delegating exceptions and |
| `00:18:06.240 - 00:18:08.549` | on the next slide we have delegating |
| `00:18:08.559 - 00:18:09.830` | interrupts |
| `00:18:09.840 - 00:18:11.270` | and we have a bunch of different kinds |
| `00:18:11.280 - 00:18:13.590` | of exceptions that might occur |
| `00:18:13.600 - 00:18:15.190` | we have |
| `00:18:15.200 - 00:18:18.789` | page faults for stores and loads |
| `00:18:18.799 - 00:18:21.669` | and instruction fetches we have |
| `00:18:21.679 - 00:18:25.270` | environmental calls the system call |
| `00:18:25.280 - 00:18:28.630` | we have different access faults we have |
| `00:18:28.640 - 00:18:31.190` | faults related to misalignment |
| `00:18:31.200 - 00:18:34.470` | uh if an instruction is illegal and we |
| `00:18:34.480 - 00:18:35.909` | attempt to execute an illegal |
| `00:18:35.919 - 00:18:37.430` | instruction we would have |
| `00:18:37.440 - 00:18:39.990` | an exception for that and |
| `00:18:40.000 - 00:18:41.110` | so on |
| `00:18:41.120 - 00:18:43.590` | so there is an instruction uh that is |
| `00:18:43.600 - 00:18:47.990` | executed to set the me deleg register |
| `00:18:48.000 - 00:18:49.430` | and basically we're just setting all the |
| `00:18:49.440 - 00:18:50.950` | bits to one |
| `00:18:50.960 - 00:18:53.750` | this is not exactly legal assembly code |
| `00:18:53.760 - 00:18:55.830` | but i think it gives you the idea it |
| `00:18:55.840 - 00:18:57.350` | writes into the |
| `00:18:57.360 - 00:18:59.750` | control and status register called me |
| `00:18:59.760 - 00:19:03.350` | deleg a value of all ones |
| `00:19:03.360 - 00:19:05.190` | for delegating the |
| `00:19:05.200 - 00:19:08.310` | interrupts we have a register called mi |
| `00:19:08.320 - 00:19:12.950` | delegation and it contains some bits and |
| `00:19:12.960 - 00:19:14.150` | we have |
| `00:19:14.160 - 00:19:16.390` | the ability to delegate device |
| `00:19:16.400 - 00:19:19.110` | interrupts we have the ability to |
| `00:19:19.120 - 00:19:20.310` | delegate |
| `00:19:20.320 - 00:19:23.029` | timer interrupts which apparently |
| `00:19:23.039 - 00:19:24.630` | can't happen for some reason we don't |
| `00:19:24.640 - 00:19:26.549` | have that ability but in any case |
| `00:19:26.559 - 00:19:27.990` | they're set to one |
| `00:19:28.000 - 00:19:30.789` | and for software interrupts we uh could |
| `00:19:30.799 - 00:19:32.230` | delegate those as well the |
| `00:19:32.240 - 00:19:33.669` | initialization is very simple it |
| `00:19:33.679 - 00:19:35.190` | basically just sets all these bits to |
| `00:19:35.200 - 00:19:35.990` | one |
| `00:19:36.000 - 00:19:38.870` | so we have this going on okay that's it |
| `00:19:38.880 - 00:19:43.880` | for this video see you in the next one |
