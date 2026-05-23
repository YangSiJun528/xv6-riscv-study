# xv6 Kernel-26: Traps in Kernel Mode

| Field | Value |
| --- | --- |
| Video ID | `Aj3DLErovD8` |
| URL | <https://www.youtube.com/watch?v=Aj3DLErovD8> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.960 - 00:00:02.550` | this video is part of a series on the |
| `00:00:02.560 - 00:00:04.870` | xv6 operating system kernel |
| `00:00:04.880 - 00:00:06.389` | in this video i'll talk about what |
| `00:00:06.399 - 00:00:08.470` | happens when a trap occurs while we're |
| `00:00:08.480 - 00:00:10.390` | executing in kernel mode |
| `00:00:10.400 - 00:00:12.230` | kernel mode is sometimes also called |
| `00:00:12.240 - 00:00:14.470` | supervisor mode |
| `00:00:14.480 - 00:00:15.829` | i'll look at the assembly code in the |
| `00:00:15.839 - 00:00:17.830` | file kernelvec.s |
| `00:00:17.840 - 00:00:19.910` | and i will look at the code in the file |
| `00:00:19.920 - 00:00:23.109` | trap.c in a particular kernel trap and |
| `00:00:23.119 - 00:00:25.109` | device interrupt |
| `00:00:25.119 - 00:00:26.710` | so let's uh review this picture that |
| `00:00:26.720 - 00:00:28.710` | i've shown earlier where |
| `00:00:28.720 - 00:00:30.550` | we have code executing in user mode and |
| `00:00:30.560 - 00:00:32.950` | then a trap occurs and we go down here |
| `00:00:32.960 - 00:00:35.350` | and ultimately execute an sred and |
| `00:00:35.360 - 00:00:38.790` | return to user mode |
| `00:00:38.800 - 00:00:40.470` | when the trap occurs |
| `00:00:40.480 - 00:00:42.150` | we will take an immediate jump the |
| `00:00:42.160 - 00:00:45.590` | hardware will take a jump to uservac |
| `00:00:45.600 - 00:00:48.950` | there's a special register called stvac |
| `00:00:48.960 - 00:00:50.950` | and as part of the hardware processing |
| `00:00:50.960 - 00:00:52.549` | the value in that register will be moved |
| `00:00:52.559 - 00:00:54.549` | into the program counter |
| `00:00:54.559 - 00:00:56.709` | this register will contain the address |
| `00:00:56.719 - 00:00:58.389` | of the first instruction of userback so |
| `00:00:58.399 - 00:01:01.510` | this just does a jump to userback |
| `00:01:01.520 - 00:01:03.670` | userback is assembly code it immediately |
| `00:01:03.680 - 00:01:06.310` | saves all the registers and basically |
| `00:01:06.320 - 00:01:07.670` | just jumps to |
| `00:01:07.680 - 00:01:10.390` | the function user trap which then looks |
| `00:01:10.400 - 00:01:11.990` | at the situation and determines whether |
| `00:01:12.000 - 00:01:14.230` | we have a program exception a device or |
| `00:01:14.240 - 00:01:17.510` | timer interrupt or it's a system call |
| `00:01:17.520 - 00:01:20.870` | ultimately we come down to user trap rat |
| `00:01:20.880 - 00:01:24.390` | which then invokes user ret and does the |
| `00:01:24.400 - 00:01:26.870` | sret instruction first |
| `00:01:26.880 - 00:01:28.630` | they will save the registers and get |
| `00:01:28.640 - 00:01:30.789` | everything restored and then go back to |
| `00:01:30.799 - 00:01:34.069` | executing in user mode |
| `00:01:34.079 - 00:01:36.149` | at this point when the trap occurs |
| `00:01:36.159 - 00:01:38.469` | interrupts are disabled so if a device |
| `00:01:38.479 - 00:01:40.149` | interrupts |
| `00:01:40.159 - 00:01:42.789` | anywhere along this pathway until |
| `00:01:42.799 - 00:01:44.630` | interrupts are re-enabled |
| `00:01:44.640 - 00:01:45.429` | that |
| `00:01:45.439 - 00:01:47.109` | interrupt will remain pending and will |
| `00:01:47.119 - 00:01:48.950` | not be dealt with until the moment that |
| `00:01:48.960 - 00:01:51.510` | the interrupts are re-enabled |
| `00:01:51.520 - 00:01:53.590` | and you see that interrupts are enabled |
| `00:01:53.600 - 00:01:55.510` | along the pathway that deals with the |
| `00:01:55.520 - 00:01:56.709` | system call |
| `00:01:56.719 - 00:01:58.789` | so while the process is executing the |
| `00:01:58.799 - 00:02:01.109` | code that implements the system call |
| `00:02:01.119 - 00:02:02.789` | a lot of things can happen but |
| `00:02:02.799 - 00:02:05.270` | interrupts are enabled so it may be that |
| `00:02:05.280 - 00:02:07.429` | we get an interrupt |
| `00:02:07.439 - 00:02:10.949` | and uh if we do then uh we will go off |
| `00:02:10.959 - 00:02:11.830` | and |
| `00:02:11.840 - 00:02:13.589` | deal with the interrupt by executing the |
| `00:02:13.599 - 00:02:15.589` | device handler and then we will come |
| `00:02:15.599 - 00:02:18.070` | back to this process and complete the |
| `00:02:18.080 - 00:02:20.309` | system call and then finally come down |
| `00:02:20.319 - 00:02:22.710` | here to user trap red |
| `00:02:22.720 - 00:02:24.309` | the first thing we do in user trap rat |
| `00:02:24.319 - 00:02:28.229` | is disable the interrupts and so |
| `00:02:28.239 - 00:02:30.710` | the next thing we do is we restore this |
| `00:02:30.720 - 00:02:33.270` | register sdvac to point to uservac so |
| `00:02:33.280 - 00:02:34.790` | that when we are |
| `00:02:34.800 - 00:02:37.430` | executing in user mode any the next trap |
| `00:02:37.440 - 00:02:42.790` | will go to user back as it should |
| `00:02:42.800 - 00:02:44.150` | so |
| `00:02:44.160 - 00:02:46.309` | we also see that interrupts remain |
| `00:02:46.319 - 00:02:49.270` | disabled along the device path so all |
| `00:02:49.280 - 00:02:51.110` | the device handlers will execute with |
| `00:02:51.120 - 00:02:53.509` | interrupts disabled |
| `00:02:53.519 - 00:02:55.670` | we also see that interrupts are disabled |
| `00:02:55.680 - 00:02:58.949` | along the timer interrupt pathway |
| `00:02:58.959 - 00:03:00.630` | so |
| `00:03:00.640 - 00:03:03.270` | we will more or less complete this yield |
| `00:03:03.280 - 00:03:05.670` | and then go straight back here now what |
| `00:03:05.680 - 00:03:08.630` | will yield do yield may switch to |
| `00:03:08.640 - 00:03:10.869` | another process and what happens in that |
| `00:03:10.879 - 00:03:12.869` | process well we don't know but most |
| `00:03:12.879 - 00:03:14.390` | likely interrupts will be enabled at |
| `00:03:14.400 - 00:03:15.910` | some point so |
| `00:03:15.920 - 00:03:18.470` | an interrupt may occur but when we come |
| `00:03:18.480 - 00:03:20.309` | back from the yield things will be just |
| `00:03:20.319 - 00:03:21.990` | as they were when we left and in |
| `00:03:22.000 - 00:03:23.750` | particular the interrupts will be |
| `00:03:23.760 - 00:03:27.509` | disabled and we immediately |
| `00:03:27.519 - 00:03:31.589` | uh disable them again |
| `00:03:31.599 - 00:03:34.149` | if a device |
| `00:03:34.159 - 00:03:36.309` | interrupts during this phase right here |
| `00:03:36.319 - 00:03:38.390` | it will remain pending that interrupt |
| `00:03:38.400 - 00:03:41.670` | will not be executed but it will be uh |
| `00:03:41.680 - 00:03:44.229` | it will be pending and the minute we |
| `00:03:44.239 - 00:03:47.270` | enable interrupts right here it will |
| `00:03:47.280 - 00:03:48.470` | occur |
| `00:03:48.480 - 00:03:51.030` | if we don't take that pathway then we |
| `00:03:51.040 - 00:03:53.589` | don't enable interrupts until we go back |
| `00:03:53.599 - 00:03:56.710` | to the user mode code so if the device |
| `00:03:56.720 - 00:03:58.949` | interrupt occurs anywhere else |
| `00:03:58.959 - 00:04:00.550` | in particular |
| `00:04:00.560 - 00:04:03.110` | happens down here then that interrupt |
| `00:04:03.120 - 00:04:05.350` | will remain pending until we return to |
| `00:04:05.360 - 00:04:07.350` | user mode code and then the first thing |
| `00:04:07.360 - 00:04:09.350` | that the user mode code will do is have |
| `00:04:09.360 - 00:04:10.869` | another interrupt and |
| `00:04:10.879 - 00:04:13.750` | deal with the device |
| `00:04:13.760 - 00:04:16.469` | i went over the function user trap in an |
| `00:04:16.479 - 00:04:18.229` | earlier video in this series but i |
| `00:04:18.239 - 00:04:19.509` | wanted to show that right at the |
| `00:04:19.519 - 00:04:21.110` | beginning of this function |
| `00:04:21.120 - 00:04:23.990` | we are writing to the control and status |
| `00:04:24.000 - 00:04:26.150` | register called st vac |
| `00:04:26.160 - 00:04:29.030` | and we are storing in it the address of |
| `00:04:29.040 - 00:04:30.710` | the first instruction of the assembly |
| `00:04:30.720 - 00:04:33.350` | code called kernel vac |
| `00:04:33.360 - 00:04:36.790` | okay so uh that's what i showed right |
| `00:04:36.800 - 00:04:38.710` | here in the diagram that we're setting |
| `00:04:38.720 - 00:04:40.150` | steven |
| `00:04:40.160 - 00:04:42.870` | and i also want to look at um what |
| `00:04:42.880 - 00:04:45.030` | happens at |
| `00:04:45.040 - 00:04:46.469` | the bottom here |
| `00:04:46.479 - 00:04:47.990` | when we |
| `00:04:48.000 - 00:04:50.950` | restore st vac after we disable the |
| `00:04:50.960 - 00:04:54.950` | interrupts so in user trap red which is |
| `00:04:54.960 - 00:04:58.629` | right here this is user trap rep |
| `00:04:58.639 - 00:05:00.550` | the first thing we do is |
| `00:05:00.560 - 00:05:02.230` | turn interrupts off |
| `00:05:02.240 - 00:05:05.270` | and then again we write into the st vac |
| `00:05:05.280 - 00:05:07.270` | and we're writing the address of the |
| `00:05:07.280 - 00:05:09.350` | user vac so there's where we are |
| `00:05:09.360 - 00:05:11.909` | restoring it |
| `00:05:11.919 - 00:05:13.430` | when we get a trap while executing in |
| `00:05:13.440 - 00:05:15.350` | user mode the first thing we do is save |
| `00:05:15.360 - 00:05:18.150` | all of the registers in the processes |
| `00:05:18.160 - 00:05:19.430` | trap frame |
| `00:05:19.440 - 00:05:21.270` | i discussed that at length in an earlier |
| `00:05:21.280 - 00:05:23.670` | video in this series |
| `00:05:23.680 - 00:05:25.350` | when we get a trap while running in |
| `00:05:25.360 - 00:05:26.790` | kernel mode we do things a bit |
| `00:05:26.800 - 00:05:27.990` | differently |
| `00:05:28.000 - 00:05:29.670` | we don't have any fixed place to store |
| `00:05:29.680 - 00:05:31.990` | them instead we save the registers on |
| `00:05:32.000 - 00:05:34.550` | the kernel's stack okay so now let's |
| `00:05:34.560 - 00:05:36.670` | take a look at the code in |
| `00:05:36.680 - 00:05:38.950` | kernelvec kernelvec.s |
| `00:05:38.960 - 00:05:40.790` | and it is the first code that is |
| `00:05:40.800 - 00:05:45.029` | executed after a trap in kernel mode |
| `00:05:45.039 - 00:05:46.790` | we make it known to the assembler that |
| `00:05:46.800 - 00:05:48.629` | the label kernelvec |
| `00:05:48.639 - 00:05:50.550` | and the function kernel trap which we |
| `00:05:50.560 - 00:05:53.749` | will be calling are externally known |
| `00:05:53.759 - 00:05:55.749` | labels |
| `00:05:55.759 - 00:05:58.309` | so the first thing we do is we |
| `00:05:58.319 - 00:06:01.189` | subtract 256 from the stack pointer this |
| `00:06:01.199 - 00:06:05.350` | is an ad immediate and we subtract 256 |
| `00:06:05.360 - 00:06:06.469` | uh and |
| `00:06:06.479 - 00:06:07.990` | from the stack pointer and that |
| `00:06:08.000 - 00:06:10.390` | allocates bytes on the stack |
| `00:06:10.400 - 00:06:12.870` | then we say each of the general purpose |
| `00:06:12.880 - 00:06:13.990` | registers |
| `00:06:14.000 - 00:06:16.629` | ra sp gptp |
| `00:06:16.639 - 00:06:18.550` | and so on |
| `00:06:18.560 - 00:06:20.870` | now we don't have to save the zero |
| `00:06:20.880 - 00:06:22.950` | register the one that contains zero at |
| `00:06:22.960 - 00:06:25.830` | all times so actually we only need 248 |
| `00:06:25.840 - 00:06:28.390` | bytes but nonetheless this code pushes |
| `00:06:28.400 - 00:06:30.790` | 256 bytes |
| `00:06:30.800 - 00:06:31.670` | then |
| `00:06:31.680 - 00:06:32.870` | we |
| `00:06:32.880 - 00:06:35.510` | call the kernel trap function |
| `00:06:35.520 - 00:06:37.510` | and that will go off and execute the |
| `00:06:37.520 - 00:06:39.350` | device handler |
| `00:06:39.360 - 00:06:41.990` | if it was a timer interrupt it will do a |
| `00:06:42.000 - 00:06:45.189` | yield and at some point the yield will |
| `00:06:45.199 - 00:06:48.150` | come back and eventually we'll return |
| `00:06:48.160 - 00:06:49.110` | from |
| `00:06:49.120 - 00:06:51.670` | kernel trap and then we will restore the |
| `00:06:51.680 - 00:06:52.790` | registers |
| `00:06:52.800 - 00:06:55.350` | so previously we saw an sd which was a |
| `00:06:55.360 - 00:06:57.749` | store double word here's a load double |
| `00:06:57.759 - 00:06:59.350` | word so we're loading all of the |
| `00:06:59.360 - 00:07:02.230` | registers registers back into the |
| `00:07:02.240 - 00:07:03.830` | registers |
| `00:07:03.840 - 00:07:05.990` | and you see all the way down here and |
| `00:07:06.000 - 00:07:07.909` | then we pop |
| `00:07:07.919 - 00:07:10.790` | the 256 bytes off the stack by adding |
| `00:07:10.800 - 00:07:13.270` | and then we do an sret to return to |
| `00:07:13.280 - 00:07:15.830` | the executing code that was interrupted |
| `00:07:15.840 - 00:07:17.749` | with the trap |
| `00:07:17.759 - 00:07:20.070` | one thing you will notice is that we do |
| `00:07:20.080 - 00:07:23.029` | not restore the tp register remember |
| `00:07:23.039 - 00:07:25.110` | that the tp register is used to hold the |
| `00:07:25.120 - 00:07:26.629` | number of the core that we're executing |
| `00:07:26.639 - 00:07:28.309` | on |
| `00:07:28.319 - 00:07:29.830` | the trap could have been caused by a |
| `00:07:29.840 - 00:07:32.309` | timer interrupt in which case we will be |
| `00:07:32.319 - 00:07:33.909` | executing a yield |
| `00:07:33.919 - 00:07:36.469` | yield will go off and make this process |
| `00:07:36.479 - 00:07:37.749` | uh |
| `00:07:37.759 - 00:07:40.309` | runnable instead of running and it will |
| `00:07:40.319 - 00:07:42.230` | then sit on the ready queue for a while |
| `00:07:42.240 - 00:07:43.670` | and ultimately |
| `00:07:43.680 - 00:07:46.469` | some core will select this process to |
| `00:07:46.479 - 00:07:49.270` | run and then it will be rescheduled and |
| `00:07:49.280 - 00:07:51.510` | the yield will return however it may not |
| `00:07:51.520 - 00:07:53.670` | return on the same core |
| `00:07:53.680 - 00:07:56.070` | the tp is supposed to hold the number of |
| `00:07:56.080 - 00:07:58.629` | the core we're executing on and that |
| `00:07:58.639 - 00:08:00.710` | will be set appropriately uh by the |
| `00:08:00.720 - 00:08:02.629` | scheduler i mean so when we come back to |
| `00:08:02.639 - 00:08:04.710` | it we'll have a tp register for that |
| `00:08:04.720 - 00:08:05.589` | core |
| `00:08:05.599 - 00:08:07.350` | so we don't want to overwrite that with |
| `00:08:07.360 - 00:08:08.390` | the |
| `00:08:08.400 - 00:08:10.869` | core number that we were executing on |
| `00:08:10.879 - 00:08:12.790` | at the time the trap occurred because we |
| `00:08:12.800 - 00:08:15.029` | may resume this process on a different |
| `00:08:15.039 - 00:08:17.749` | core i think that's kind of cool |
| `00:08:17.759 - 00:08:20.469` | okay so the code in kernelvec has just |
| `00:08:20.479 - 00:08:22.469` | saved all the general purpose registers |
| `00:08:22.479 - 00:08:25.029` | and invoked kernel trap which is shown |
| `00:08:25.039 - 00:08:26.550` | here |
| `00:08:26.560 - 00:08:28.150` | the first thing the kernel trap function |
| `00:08:28.160 - 00:08:29.830` | does is |
| `00:08:29.840 - 00:08:32.310` | save the value in several of the control |
| `00:08:32.320 - 00:08:34.230` | and status registers |
| `00:08:34.240 - 00:08:37.110` | the program counter the status register |
| `00:08:37.120 - 00:08:39.509` | and the cause register are fetched and |
| `00:08:39.519 - 00:08:41.110` | saved here |
| `00:08:41.120 - 00:08:42.550` | we will need the program counter in the |
| `00:08:42.560 - 00:08:43.750` | status |
| `00:08:43.760 - 00:08:45.509` | in order to return to the interrupted |
| `00:08:45.519 - 00:08:46.870` | process |
| `00:08:46.880 - 00:08:48.870` | the cause register will contain a number |
| `00:08:48.880 - 00:08:52.389` | that tells exactly what caused the trap |
| `00:08:52.399 - 00:08:54.150` | so the first thing we do or after that |
| `00:08:54.160 - 00:08:56.470` | is do some sanity checking |
| `00:08:56.480 - 00:08:57.990` | we make sure that we were really |
| `00:08:58.000 - 00:09:00.630` | executing in kernel mode when we were |
| `00:09:00.640 - 00:09:02.470` | interrupted by looking at the status |
| `00:09:02.480 - 00:09:04.870` | register and looking at the previous |
| `00:09:04.880 - 00:09:06.150` | privilege mode |
| `00:09:06.160 - 00:09:08.310` | field and making sure that it is |
| `00:09:08.320 - 00:09:10.710` | supervisor or kernel mode |
| `00:09:10.720 - 00:09:12.550` | then we make sure that interrupts are in |
| `00:09:12.560 - 00:09:15.350` | fact disabled and assuming everything is |
| `00:09:15.360 - 00:09:17.750` | good there we then call this function |
| `00:09:17.760 - 00:09:20.310` | device interrupt and device interrupt |
| `00:09:20.320 - 00:09:21.269` | will |
| `00:09:21.279 - 00:09:23.670` | look at the cause register and determine |
| `00:09:23.680 - 00:09:26.470` | whether it's the the uart or the disc uh |
| `00:09:26.480 - 00:09:29.190` | that is causing the interrupt and if so |
| `00:09:29.200 - 00:09:32.310` | it will execute the appropriate handler |
| `00:09:32.320 - 00:09:35.350` | routine for that device and return |
| `00:09:35.360 - 00:09:37.750` | one if it is a timer interrupt it won't |
| `00:09:37.760 - 00:09:40.389` | do anything but it will return a two and |
| `00:09:40.399 - 00:09:41.990` | if it's anything else it will return a |
| `00:09:42.000 - 00:09:44.310` | zero that is a problem |
| `00:09:44.320 - 00:09:47.110` | so here we are saving the value returns |
| `00:09:47.120 - 00:09:49.750` | and if it returns zero we print out the |
| `00:09:49.760 - 00:09:51.190` | cause register |
| `00:09:51.200 - 00:09:52.630` | and we also print out the program |
| `00:09:52.640 - 00:09:55.190` | counter and the value of this st val |
| `00:09:55.200 - 00:09:57.829` | register at the time of the interrupt oh |
| `00:09:57.839 - 00:09:59.670` | i should have highlighted bold faced |
| `00:09:59.680 - 00:10:02.150` | these function calls but in any case |
| `00:10:02.160 - 00:10:05.509` | that we then panic now if it was a timer |
| `00:10:05.519 - 00:10:06.870` | interrupt then |
| `00:10:06.880 - 00:10:10.150` | it will have returned two so we call the |
| `00:10:10.160 - 00:10:11.829` | yield function |
| `00:10:11.839 - 00:10:14.069` | yield will go away uh it will invoke the |
| `00:10:14.079 - 00:10:15.670` | scheduler and our process will be |
| `00:10:15.680 - 00:10:17.509` | changed from running to runnable and |
| `00:10:17.519 - 00:10:19.509` | then at some later time |
| `00:10:19.519 - 00:10:23.030` | we will get a chance to run again and |
| `00:10:23.040 - 00:10:25.750` | we will change back from runnable to |
| `00:10:25.760 - 00:10:26.790` | running |
| `00:10:26.800 - 00:10:28.630` | and this might actually even occur on a |
| `00:10:28.640 - 00:10:29.829` | different core |
| `00:10:29.839 - 00:10:31.910` | and so we might resume executing on some |
| `00:10:31.920 - 00:10:33.750` | other core |
| `00:10:33.760 - 00:10:36.310` | so before we return |
| `00:10:36.320 - 00:10:38.870` | we need to restore the value of this |
| `00:10:38.880 - 00:10:41.350` | register for the program counter and the |
| `00:10:41.360 - 00:10:43.509` | status these will be used as part of the |
| `00:10:43.519 - 00:10:44.470` | srep |
| `00:10:44.480 - 00:10:47.030` | process the sred instruction will copy |
| `00:10:47.040 - 00:10:50.069` | the value of scpc into the program |
| `00:10:50.079 - 00:10:53.829` | counter and the copy s status into the |
| `00:10:53.839 - 00:10:55.590` | status register so we need to have those |
| `00:10:55.600 - 00:10:58.949` | set up before we return to |
| `00:10:58.959 - 00:11:00.790` | kernel vec |
| `00:11:00.800 - 00:11:03.030` | now the other thing i want to discuss is |
| `00:11:03.040 - 00:11:04.870` | why do we do this test here |
| `00:11:04.880 - 00:11:06.870` | if we had a time interrupt we're also |
| `00:11:06.880 - 00:11:09.190` | making before we call yield we're making |
| `00:11:09.200 - 00:11:11.269` | sure that the |
| `00:11:11.279 - 00:11:14.630` | state is running so here we are asking |
| `00:11:14.640 - 00:11:16.710` | what is the current process and if it's |
| `00:11:16.720 - 00:11:17.990` | not no |
| `00:11:18.000 - 00:11:21.430` | and then we follow that to the state and |
| `00:11:21.440 - 00:11:22.870` | make sure it's running |
| `00:11:22.880 - 00:11:25.430` | and so you might ask well how could we |
| `00:11:25.440 - 00:11:28.150` | have been interrupted from any process |
| `00:11:28.160 - 00:11:30.150` | that wasn't running |
| `00:11:30.160 - 00:11:32.230` | and to see the answer to that |
| `00:11:32.240 - 00:11:34.150` | let's go back to look at the scheduler |
| `00:11:34.160 - 00:11:35.110` | code |
| `00:11:35.120 - 00:11:37.430` | here's the scheduler code |
| `00:11:37.440 - 00:11:38.230` | so |
| `00:11:38.240 - 00:11:40.949` | remember that this will be |
| `00:11:40.959 - 00:11:43.269` | executing a for loop here where it goes |
| `00:11:43.279 - 00:11:45.350` | through all the proc structures in the |
| `00:11:45.360 - 00:11:47.110` | proc array looking for one that's |
| `00:11:47.120 - 00:11:48.790` | runnable looking for one that's runnable |
| `00:11:48.800 - 00:11:50.389` | and if it finds one it changes it to |
| `00:11:50.399 - 00:11:53.110` | running and switches into it |
| `00:11:53.120 - 00:11:55.190` | and it just keeps looking |
| `00:11:55.200 - 00:11:56.790` | and if when it gets to the end of that |
| `00:11:56.800 - 00:11:58.550` | array or if it doesn't find anything in |
| `00:11:58.560 - 00:12:00.550` | the array it will then loop back to this |
| `00:12:00.560 - 00:12:02.310` | infinite loop |
| `00:12:02.320 - 00:12:04.230` | we turn interrupts on here |
| `00:12:04.240 - 00:12:06.949` | okay i discussed that earlier why that's |
| `00:12:06.959 - 00:12:09.430` | necessary but at this point when |
| `00:12:09.440 - 00:12:12.389` | interrupts are turned on it may be that |
| `00:12:12.399 - 00:12:14.710` | we will have a trap it could be that a |
| `00:12:14.720 - 00:12:16.790` | device tried to interrupt earlier and |
| `00:12:16.800 - 00:12:19.269` | that interrupt has been remain remaining |
| `00:12:19.279 - 00:12:22.790` | pending so the moment we turn interrupts |
| `00:12:22.800 - 00:12:26.230` | on here boom we get a trap okay we're |
| `00:12:26.240 - 00:12:29.670` | not actually in any process we're in the |
| `00:12:29.680 - 00:12:30.790` | scheduler |
| `00:12:30.800 - 00:12:31.910` | okay so |
| `00:12:31.920 - 00:12:35.509` | no process will have a state of running |
| `00:12:35.519 - 00:12:36.629` | in fact |
| `00:12:36.639 - 00:12:38.230` | the last thing we do before calling |
| `00:12:38.240 - 00:12:40.629` | switch is change our our state to |
| `00:12:40.639 - 00:12:42.790` | runnable or maybe sleeping |
| `00:12:42.800 - 00:12:43.590` | so |
| `00:12:43.600 - 00:12:46.310` | we don't want to have |
| `00:12:46.320 - 00:12:48.310` | the |
| `00:12:48.320 - 00:12:51.030` | yield function executed while we're in |
| `00:12:51.040 - 00:12:53.030` | the scheduler that would really mess |
| `00:12:53.040 - 00:12:55.590` | things up so that's why we we do that |
| `00:12:55.600 - 00:12:56.550` | here |
| `00:12:56.560 - 00:12:57.910` | but notice that |
| `00:12:57.920 - 00:13:01.430` | from this acquire on through the switch |
| `00:13:01.440 - 00:13:02.870` | interrupts will be disabled remember |
| `00:13:02.880 - 00:13:04.629` | that when we acquire a spin lock we |
| `00:13:04.639 - 00:13:07.910` | disable interrupts until they are uh |
| `00:13:07.920 - 00:13:10.310` | released till the spin lock is released |
| `00:13:10.320 - 00:13:12.870` | and likewise uh in the code that call |
| `00:13:12.880 - 00:13:15.829` | switch that comes back we will also |
| `00:13:15.839 - 00:13:18.790` | acquire the lock which will disable |
| `00:13:18.800 - 00:13:21.110` | interrupts until this point |
| `00:13:21.120 - 00:13:22.790` | so it's possible that |
| `00:13:22.800 - 00:13:24.550` | interrupts will be re-enabled at this |
| `00:13:24.560 - 00:13:26.710` | point and we might have |
| `00:13:26.720 - 00:13:30.550` | that trap occurring here as well |
| `00:13:30.560 - 00:13:32.550` | next let's talk about the device |
| `00:13:32.560 - 00:13:35.269` | interrupt function it begins by reading |
| `00:13:35.279 - 00:13:38.389` | the s cause register to see |
| `00:13:38.399 - 00:13:40.629` | what exactly caused the trap |
| `00:13:40.639 - 00:13:42.550` | this function will return two if it was |
| `00:13:42.560 - 00:13:43.910` | a timer interrupt |
| `00:13:43.920 - 00:13:46.550` | one if it was the uart or the disk or |
| `00:13:46.560 - 00:13:48.550` | zero if it was something else that was |
| `00:13:48.560 - 00:13:50.230` | unrecognized |
| `00:13:50.240 - 00:13:52.310` | so here we are looking at the s cause |
| `00:13:52.320 - 00:13:55.750` | register and if it's this value then |
| `00:13:55.760 - 00:13:56.870` | we've got |
| `00:13:56.880 - 00:13:59.350` | an external interrupt |
| `00:13:59.360 - 00:14:01.990` | the platform level interrupt controller |
| `00:14:02.000 - 00:14:05.430` | makes its need to be taken care of known |
| `00:14:05.440 - 00:14:08.870` | by causing an external interrupt so if |
| `00:14:08.880 - 00:14:11.030` | we have an external interrupt uh it must |
| `00:14:11.040 - 00:14:12.949` | be the click so |
| `00:14:12.959 - 00:14:15.189` | then we handle it here and otherwise we |
| `00:14:15.199 - 00:14:17.990` | handle it here |
| `00:14:18.000 - 00:14:20.470` | we call this function click claim which |
| `00:14:20.480 - 00:14:22.790` | will return a code to indicate which |
| `00:14:22.800 - 00:14:25.350` | device is demanding attention |
| `00:14:25.360 - 00:14:27.269` | and then we check that number |
| `00:14:27.279 - 00:14:29.430` | if it has this value it must have been |
| `00:14:29.440 - 00:14:32.790` | the uart so we call the function here |
| `00:14:32.800 - 00:14:34.949` | which is the interrupt handler for the |
| `00:14:34.959 - 00:14:36.389` | uart device |
| `00:14:36.399 - 00:14:38.790` | we've had this value then we call this |
| `00:14:38.800 - 00:14:40.949` | function which is the interrupt handler |
| `00:14:40.959 - 00:14:42.870` | for the disk device |
| `00:14:42.880 - 00:14:45.430` | and if it's zero we ignore it but if |
| `00:14:45.440 - 00:14:47.189` | it's anything else we print some sort of |
| `00:14:47.199 - 00:14:48.790` | an error message |
| `00:14:48.800 - 00:14:49.509` | so |
| `00:14:49.519 - 00:14:51.189` | uh |
| `00:14:51.199 - 00:14:53.670` | then we need to call click complete the |
| `00:14:53.680 - 00:14:55.110` | click allows each device to raise at |
| `00:14:55.120 - 00:14:57.269` | most one interrupt at a time tell the |
| `00:14:57.279 - 00:14:58.710` | click the device is now allowed to |
| `00:14:58.720 - 00:15:00.710` | interrupt again |
| `00:15:00.720 - 00:15:04.150` | and we return the code of 1. |
| `00:15:04.160 - 00:15:05.829` | otherwise we check the s calls register |
| `00:15:05.839 - 00:15:07.910` | and if it has this value then we must |
| `00:15:07.920 - 00:15:10.629` | have a software interrupt and remember |
| `00:15:10.639 - 00:15:12.470` | that when we have a timer interrupt the |
| `00:15:12.480 - 00:15:14.389` | machine mode code gets uh |
| `00:15:14.399 - 00:15:15.590` | gets that |
| `00:15:15.600 - 00:15:17.990` | and it then simulates this software |
| `00:15:18.000 - 00:15:20.949` | interrupt at supervisor level so that's |
| `00:15:20.959 - 00:15:22.389` | what's happening here |
| `00:15:22.399 - 00:15:23.910` | and uh |
| `00:15:23.920 - 00:15:25.750` | then what are we going to do if it was a |
| `00:15:25.760 - 00:15:28.150` | timer interrupt we are going to check |
| `00:15:28.160 - 00:15:30.310` | whether we are running on core 0 and if |
| `00:15:30.320 - 00:15:32.389` | so we call this function |
| `00:15:32.399 - 00:15:33.430` | clock |
| `00:15:33.440 - 00:15:35.110` | interrupt and |
| `00:15:35.120 - 00:15:37.829` | i'll look at that in just a second |
| `00:15:37.839 - 00:15:40.470` | but regardless we acknowledge the |
| `00:15:40.480 - 00:15:41.829` | software interrupt |
| `00:15:41.839 - 00:15:43.509` | by clearing the |
| `00:15:43.519 - 00:15:45.509` | bit in the |
| `00:15:45.519 - 00:15:48.069` | interrupt uh pending |
| `00:15:48.079 - 00:15:50.069` | word in that register so we read the |
| `00:15:50.079 - 00:15:52.629` | register clear the bit and write it back |
| `00:15:52.639 - 00:15:55.030` | and then we return the code of two and |
| `00:15:55.040 - 00:15:56.790` | if it was anything else then we'd return |
| `00:15:56.800 - 00:15:59.189` | zero so what does this clock interrupt |
| `00:15:59.199 - 00:16:00.470` | function do |
| `00:16:00.480 - 00:16:02.790` | well all it does is |
| `00:16:02.800 - 00:16:05.509` | increment this global variable ticks |
| `00:16:05.519 - 00:16:07.910` | that's essentially our clock x36 doesn't |
| `00:16:07.920 - 00:16:09.749` | have any kind of a real-time clock we're |
| `00:16:09.759 - 00:16:11.670` | just counting the number of ticks on |
| `00:16:11.680 - 00:16:12.870` | core zero |
| `00:16:12.880 - 00:16:13.910` | and so |
| `00:16:13.920 - 00:16:16.389` | that global variable is protected by a |
| `00:16:16.399 - 00:16:18.790` | spin lock called pixlock |
| `00:16:18.800 - 00:16:20.389` | so we acquire that |
| `00:16:20.399 - 00:16:22.310` | and then we |
| `00:16:22.320 - 00:16:24.310` | increment it and if anybody has been |
| `00:16:24.320 - 00:16:26.150` | waiting if any other processes are |
| `00:16:26.160 - 00:16:29.110` | waiting on a particular time coming |
| `00:16:29.120 - 00:16:29.990` | to be |
| `00:16:30.000 - 00:16:32.470` | then we wake that |
| `00:16:32.480 - 00:16:35.189` | process up or all of the processes up |
| `00:16:35.199 - 00:16:38.710` | and then we release the spin lock |
| `00:16:38.720 - 00:16:41.509` | okay that's it for kernel vac and what |
| `00:16:41.519 - 00:16:42.949` | happens when a |
| `00:16:42.959 - 00:16:45.110` | trap occurs in kernel mode i'll see you |
| `00:16:45.120 - 00:16:48.399` | in the next video |
