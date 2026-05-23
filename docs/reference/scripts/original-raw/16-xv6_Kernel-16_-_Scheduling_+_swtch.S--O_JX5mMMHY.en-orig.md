# xv6 Kernel-16: Scheduling + swtch.S

| Field | Value |
| --- | --- |
| Video ID | `-O_JX5mMMHY` |
| URL | <https://www.youtube.com/watch?v=-O_JX5mMMHY> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.280 - 00:00:02.790` | this video is part of a series on the |
| `00:00:02.800 - 00:00:05.269` | xv6 operating system kernel |
| `00:00:05.279 - 00:00:06.869` | scheduling is probably the most |
| `00:00:06.879 - 00:00:08.390` | interesting part of the kernel in my |
| `00:00:08.400 - 00:00:10.390` | opinion and in this video i'm going to |
| `00:00:10.400 - 00:00:11.669` | take a look at it |
| `00:00:11.679 - 00:00:14.390` | i will look at these three functions |
| `00:00:14.400 - 00:00:16.790` | i'll pronounce this one skid so i'll |
| `00:00:16.800 - 00:00:18.950` | look at functions yield skid and |
| `00:00:18.960 - 00:00:20.230` | scheduler |
| `00:00:20.240 - 00:00:21.670` | and i'll also look at some helper |
| `00:00:21.680 - 00:00:24.470` | functions i'll look at cpu id my cpu and |
| `00:00:24.480 - 00:00:25.990` | my proc |
| `00:00:26.000 - 00:00:27.910` | and i will go over the assembly code |
| `00:00:27.920 - 00:00:30.870` | that's in the switch.s file |
| `00:00:30.880 - 00:00:32.229` | so let's begin with these helper |
| `00:00:32.239 - 00:00:33.990` | functions since they're pretty |
| `00:00:34.000 - 00:00:35.350` | straightforward and easy to get out of |
| `00:00:35.360 - 00:00:37.830` | the way first |
| `00:00:37.840 - 00:00:39.990` | the cpuid function |
| `00:00:40.000 - 00:00:42.830` | will simply return the value in the tp |
| `00:00:42.840 - 00:00:44.869` | register that register will always |
| `00:00:44.879 - 00:00:46.549` | contain the number of the core that |
| `00:00:46.559 - 00:00:49.590` | we're currently executing on |
| `00:00:49.600 - 00:00:51.270` | since we could have interrupts and |
| `00:00:51.280 - 00:00:53.110` | things could change at any moment we |
| `00:00:53.120 - 00:00:54.790` | really need to be called with interrupts |
| `00:00:54.800 - 00:00:58.470` | disabled to make sure that the value is |
| `00:00:58.480 - 00:00:59.990` | not |
| `00:01:00.000 - 00:01:03.270` | out of date the minute we return it |
| `00:01:03.280 - 00:01:05.750` | the my cpu function |
| `00:01:05.760 - 00:01:06.950` | will |
| `00:01:06.960 - 00:01:10.070` | get the identifier for the number for |
| `00:01:10.080 - 00:01:12.789` | the current core and then it will look |
| `00:01:12.799 - 00:01:13.990` | up the |
| `00:01:14.000 - 00:01:17.109` | cpu structure in the array so remember |
| `00:01:17.119 - 00:01:18.789` | this picture we had |
| `00:01:18.799 - 00:01:21.670` | showing that there's one cpu struct for |
| `00:01:21.680 - 00:01:24.789` | each of the eight cores and so all we're |
| `00:01:24.799 - 00:01:26.950` | doing here is returning a pointer to the |
| `00:01:26.960 - 00:01:29.510` | structure that describes the data for |
| `00:01:29.520 - 00:01:34.390` | this particular core |
| `00:01:34.400 - 00:01:35.350` | and |
| `00:01:35.360 - 00:01:36.310` | the |
| `00:01:36.320 - 00:01:38.469` | myproc function |
| `00:01:38.479 - 00:01:39.510` | will |
| `00:01:39.520 - 00:01:41.109` | go to that structure we'll get that |
| `00:01:41.119 - 00:01:43.830` | structure so it'll call my cpu and it |
| `00:01:43.840 - 00:01:47.109` | will go to the proc field and follow it |
| `00:01:47.119 - 00:01:48.550` | and return that |
| `00:01:48.560 - 00:01:49.350` | so |
| `00:01:49.360 - 00:01:51.749` | the cpu structure will contain a pointer |
| `00:01:51.759 - 00:01:54.149` | to the currently executing procedure |
| `00:01:54.159 - 00:01:55.510` | sorry process |
| `00:01:55.520 - 00:01:57.910` | and we'll return a pointer to the proc |
| `00:01:57.920 - 00:01:59.510` | structure |
| `00:01:59.520 - 00:02:02.469` | it could very well be null if we are not |
| `00:02:02.479 - 00:02:03.749` | executing any |
| `00:02:03.759 - 00:02:07.749` | process at the moment but we return that |
| `00:02:07.759 - 00:02:09.669` | now my cpu has to be called with |
| `00:02:09.679 - 00:02:12.949` | interrupts disabled and myproc is called |
| `00:02:12.959 - 00:02:14.630` | from all over the place |
| `00:02:14.640 - 00:02:17.110` | so one thing we do is call push off to |
| `00:02:17.120 - 00:02:19.350` | disable interrupts here |
| `00:02:19.360 - 00:02:21.270` | that will |
| `00:02:21.280 - 00:02:23.589` | also increment the counter then we call |
| `00:02:23.599 - 00:02:26.630` | papa to restore the interrupts |
| `00:02:26.640 - 00:02:28.550` | to decrement the counter and if it goes |
| `00:02:28.560 - 00:02:30.869` | back to zero to restore the interrupt |
| `00:02:30.879 - 00:02:35.190` | status to whatever it was before |
| `00:02:35.200 - 00:02:38.630` | okay now let's take a look at this road |
| `00:02:38.640 - 00:02:41.190` | map that we showed earlier |
| `00:02:41.200 - 00:02:43.670` | and this shows everything that happens |
| `00:02:43.680 - 00:02:45.030` | between the trap |
| `00:02:45.040 - 00:02:47.670` | and the srep instruction |
| `00:02:47.680 - 00:02:51.190` | user code is being executed and we get a |
| `00:02:51.200 - 00:02:53.350` | trap and we see what's going on and in |
| `00:02:53.360 - 00:02:55.270` | the case of a timer interrupt |
| `00:02:55.280 - 00:02:58.070` | we will call the yield function so i'm |
| `00:02:58.080 - 00:02:59.990` | going to be focusing on the yield |
| `00:03:00.000 - 00:03:02.149` | function i kind of hinted at some stuff |
| `00:03:02.159 - 00:03:03.270` | here |
| `00:03:03.280 - 00:03:07.430` | but i will now show this area here in |
| `00:03:07.440 - 00:03:08.869` | more detail |
| `00:03:08.879 - 00:03:11.750` | after yield returns we go back to the |
| `00:03:11.760 - 00:03:13.190` | user mode function and continue |
| `00:03:13.200 - 00:03:14.710` | executing |
| `00:03:14.720 - 00:03:16.710` | one thing i will notice that |
| `00:03:16.720 - 00:03:18.470` | when the trap occurs interrupts are |
| `00:03:18.480 - 00:03:20.710` | disabled and along this path where we |
| `00:03:20.720 - 00:03:21.990` | call yield |
| `00:03:22.000 - 00:03:24.710` | interrupts remain disabled and so |
| `00:03:24.720 - 00:03:26.630` | interrupts are disabled for the yield |
| `00:03:26.640 - 00:03:28.550` | function |
| `00:03:28.560 - 00:03:32.390` | so uh looking at that area more closely |
| `00:03:32.400 - 00:03:35.190` | we see that the trap happens |
| `00:03:35.200 - 00:03:37.190` | and we have a lot of stuff going on and |
| `00:03:37.200 - 00:03:39.350` | we determine that it's not a system call |
| `00:03:39.360 - 00:03:41.670` | and not a device that needs interrupt |
| `00:03:41.680 - 00:03:42.949` | handling |
| `00:03:42.959 - 00:03:45.430` | and we end up calling the yield function |
| `00:03:45.440 - 00:03:47.509` | and so this is what happens and |
| `00:03:47.519 - 00:03:50.630` | ultimately we return from yield and |
| `00:03:50.640 - 00:03:52.229` | basically do some other stuff and |
| `00:03:52.239 - 00:03:53.750` | execute the s red and go back to the |
| `00:03:53.760 - 00:03:56.789` | user mode code |
| `00:03:56.799 - 00:03:58.390` | yield |
| `00:03:58.400 - 00:04:01.190` | calls skid so here i'm kind of |
| `00:04:01.200 - 00:04:03.830` | indicating that yield will call skid not |
| `00:04:03.840 - 00:04:07.270` | much happens in yield and sched just a |
| `00:04:07.280 - 00:04:09.910` | few statements are executed really |
| `00:04:09.920 - 00:04:10.789` | um |
| `00:04:10.799 - 00:04:14.229` | and we call a function switch |
| `00:04:14.239 - 00:04:16.710` | so |
| `00:04:16.720 - 00:04:19.270` | this is the processor thread so we are |
| `00:04:19.280 - 00:04:21.590` | executing in user mode for the |
| `00:04:21.600 - 00:04:23.270` | particular process p |
| `00:04:23.280 - 00:04:25.990` | and then we are executing here in kernel |
| `00:04:26.000 - 00:04:27.749` | mode for process p |
| `00:04:27.759 - 00:04:30.390` | and at this point when the switch occurs |
| `00:04:30.400 - 00:04:31.830` | we switch from |
| `00:04:31.840 - 00:04:34.390` | process p to the scheduler thread and |
| `00:04:34.400 - 00:04:36.790` | then we execute the scheduler thread |
| `00:04:36.800 - 00:04:38.390` | the scheduler thread may do some other |
| `00:04:38.400 - 00:04:40.710` | stuff it may |
| `00:04:40.720 - 00:04:43.110` | invoke some other it may do time slices |
| `00:04:43.120 - 00:04:45.350` | for other processes but eventually |
| `00:04:45.360 - 00:04:47.430` | it will come back |
| `00:04:47.440 - 00:04:48.469` | and |
| `00:04:48.479 - 00:04:52.230` | the call to switch here will return and |
| `00:04:52.240 - 00:04:54.550` | we'll continue executing in the sched |
| `00:04:54.560 - 00:04:56.790` | function and then the yield function and |
| `00:04:56.800 - 00:04:57.909` | then we'll |
| `00:04:57.919 - 00:04:59.909` | do some restoring of the user state and |
| `00:04:59.919 - 00:05:02.230` | return |
| `00:05:02.240 - 00:05:03.990` | so let's take a look at what happens |
| `00:05:04.000 - 00:05:05.830` | here |
| `00:05:05.840 - 00:05:07.990` | in the yield and scad functions before |
| `00:05:08.000 - 00:05:11.510` | we call switch |
| `00:05:11.520 - 00:05:14.390` | we're going to examine and modify |
| `00:05:14.400 - 00:05:17.909` | the state of this process p |
| `00:05:17.919 - 00:05:20.790` | while we are running the state is |
| `00:05:20.800 - 00:05:22.550` | running it is |
| `00:05:22.560 - 00:05:24.230` | it's running but we're going to change |
| `00:05:24.240 - 00:05:25.590` | it to runnable |
| `00:05:25.600 - 00:05:27.270` | uh which means it's no longer running |
| `00:05:27.280 - 00:05:29.510` | and waiting for another time slice |
| `00:05:29.520 - 00:05:32.390` | so we acquire the lock on process p |
| `00:05:32.400 - 00:05:36.070` | itself and change its state to runnable |
| `00:05:36.080 - 00:05:39.350` | and we also save this uh field |
| `00:05:39.360 - 00:05:41.430` | interrupts enabled |
| `00:05:41.440 - 00:05:44.070` | of the cpu structure |
| `00:05:44.080 - 00:05:46.390` | okay let me just uh review the cpu |
| `00:05:46.400 - 00:05:48.070` | structure not only do we have the |
| `00:05:48.080 - 00:05:50.230` | pointer to the proc that we're executing |
| `00:05:50.240 - 00:05:53.029` | but we have two fields that are used by |
| `00:05:53.039 - 00:05:55.110` | push-offs and pop-off |
| `00:05:55.120 - 00:05:57.270` | in-off is a counter and interrupts |
| `00:05:57.280 - 00:05:59.830` | enabled is the previous status and then |
| `00:05:59.840 - 00:06:02.070` | we also have this context area where we |
| `00:06:02.080 - 00:06:05.990` | will save registers |
| `00:06:06.000 - 00:06:07.749` | so we're going to save that |
| `00:06:07.759 - 00:06:10.309` | and then later we will restore that |
| `00:06:10.319 - 00:06:11.670` | field |
| `00:06:11.680 - 00:06:15.350` | and release the lock on p and return to |
| `00:06:15.360 - 00:06:16.309` | uh |
| `00:06:16.319 - 00:06:20.390` | the user mode code so after we call |
| `00:06:20.400 - 00:06:22.469` | switch some other stuff happens and |
| `00:06:22.479 - 00:06:24.629` | switch ultimately will return |
| `00:06:24.639 - 00:06:26.950` | and i'm indicating it right here and |
| `00:06:26.960 - 00:06:29.110` | then we come back and finish the |
| `00:06:29.120 - 00:06:32.070` | execution of the sked function |
| `00:06:32.080 - 00:06:33.189` | and |
| `00:06:33.199 - 00:06:35.670` | the execution the yield function and so |
| `00:06:35.680 - 00:06:38.150` | on now what happens in the scheduler |
| `00:06:38.160 - 00:06:39.350` | thread |
| `00:06:39.360 - 00:06:40.309` | well |
| `00:06:40.319 - 00:06:42.629` | once we resume executing the scheduler |
| `00:06:42.639 - 00:06:44.950` | thread we will |
| `00:06:44.960 - 00:06:47.830` | change the cpu's proc field to null |
| `00:06:47.840 - 00:06:49.670` | because we're no longer executing this |
| `00:06:49.680 - 00:06:51.749` | particular process and we'll release |
| `00:06:51.759 - 00:06:53.189` | that lock |
| `00:06:53.199 - 00:06:54.629` | and then we'll do some other stuff and |
| `00:06:54.639 - 00:06:56.309` | eventually uh |
| `00:06:56.319 - 00:06:59.670` | we will choose to run p again |
| `00:06:59.680 - 00:07:01.350` | in the interim we may get some other |
| `00:07:01.360 - 00:07:03.189` | processes time slices but eventually |
| `00:07:03.199 - 00:07:04.629` | we'll come around and |
| `00:07:04.639 - 00:07:06.629` | this core or perhaps some other core |
| `00:07:06.639 - 00:07:09.749` | will choose to give p a time slice and |
| `00:07:09.759 - 00:07:11.830` | at that point we will acquire the lock |
| `00:07:11.840 - 00:07:14.070` | on p again |
| `00:07:14.080 - 00:07:17.029` | and change its state to running |
| `00:07:17.039 - 00:07:19.110` | and we will then |
| `00:07:19.120 - 00:07:22.230` | set the proc field for the current cpu |
| `00:07:22.240 - 00:07:23.510` | remember it could be a different core |
| `00:07:23.520 - 00:07:25.270` | that we're executing on we'll set the |
| `00:07:25.280 - 00:07:26.469` | current |
| `00:07:26.479 - 00:07:29.510` | cpu structs proc field to point to the |
| `00:07:29.520 - 00:07:31.990` | process that we are now starting which |
| `00:07:32.000 - 00:07:33.110` | is p |
| `00:07:33.120 - 00:07:34.550` | and then we'll |
| `00:07:34.560 - 00:07:36.710` | use switch to go back |
| `00:07:36.720 - 00:07:37.670` | into |
| `00:07:37.680 - 00:07:38.469` | the |
| `00:07:38.479 - 00:07:40.070` | scad function and then the yield |
| `00:07:40.080 - 00:07:41.270` | function |
| `00:07:41.280 - 00:07:43.270` | okay switches is where |
| `00:07:43.280 - 00:07:45.830` | the interesting stuff happens so i want |
| `00:07:45.840 - 00:07:49.749` | to talk about that next |
| `00:07:49.759 - 00:07:52.710` | in the cpu structure we have a register |
| `00:07:52.720 - 00:07:55.029` | straight save area where we can save |
| `00:07:55.039 - 00:07:58.950` | registers r a s p and the s registers |
| `00:07:58.960 - 00:08:00.550` | and also in the proc |
| `00:08:00.560 - 00:08:02.790` | structure we have a register save area |
| `00:08:02.800 - 00:08:09.110` | where we can save those same registers |
| `00:08:09.120 - 00:08:10.830` | when |
| `00:08:10.840 - 00:08:13.830` | we switch from |
| `00:08:13.840 - 00:08:15.110` | the |
| `00:08:15.120 - 00:08:16.950` | process p |
| `00:08:16.960 - 00:08:20.150` | will save the current registers of |
| `00:08:20.160 - 00:08:22.150` | process p in |
| `00:08:22.160 - 00:08:25.110` | the proc structure in that register save |
| `00:08:25.120 - 00:08:28.150` | area and we'll load the registers for |
| `00:08:28.160 - 00:08:32.230` | from the cpu field from the cpu structs |
| `00:08:32.240 - 00:08:34.469` | register save area |
| `00:08:34.479 - 00:08:37.269` | and when we go back from the scheduler |
| `00:08:37.279 - 00:08:38.949` | thread to |
| `00:08:38.959 - 00:08:39.670` | the |
| `00:08:39.680 - 00:08:41.269` | thread of process p |
| `00:08:41.279 - 00:08:42.709` | we will save |
| `00:08:42.719 - 00:08:46.949` | the schedulers uh registers in |
| `00:08:46.959 - 00:08:49.670` | the cpu struct for that particular core |
| `00:08:49.680 - 00:08:52.550` | and we will load the registers |
| `00:08:52.560 - 00:08:56.790` | from process p's register save area |
| `00:08:56.800 - 00:09:02.310` | okay next let's take a look at |
| `00:09:02.320 - 00:09:03.430` | switch |
| `00:09:03.440 - 00:09:06.550` | switch is written in assembly code |
| `00:09:06.560 - 00:09:09.030` | and we see here that it's past two |
| `00:09:09.040 - 00:09:10.630` | pointers |
| `00:09:10.640 - 00:09:13.110` | both pointers are pointers to the |
| `00:09:13.120 - 00:09:15.030` | context so |
| `00:09:15.040 - 00:09:17.670` | essentially we're passing a pointer to |
| `00:09:17.680 - 00:09:19.509` | this context here |
| `00:09:19.519 - 00:09:20.310` | and |
| `00:09:20.320 - 00:09:28.470` | that context there in the proc structure |
| `00:09:28.480 - 00:09:32.389` | in the risk 5 system whenever a call is |
| `00:09:32.399 - 00:09:34.070` | made to a function |
| `00:09:34.080 - 00:09:36.150` | we will or the i should say the |
| `00:09:36.160 - 00:09:38.070` | instruction that does the call will save |
| `00:09:38.080 - 00:09:39.269` | the current |
| `00:09:39.279 - 00:09:42.070` | program counter in the return address |
| `00:09:42.080 - 00:09:44.550` | register the ra register and it will |
| `00:09:44.560 - 00:09:47.110` | jump to the first instruction of the |
| `00:09:47.120 - 00:09:48.550` | proce of the function that we want to |
| `00:09:48.560 - 00:09:49.430` | call |
| `00:09:49.440 - 00:09:51.269` | the return instruction and there's one |
| `00:09:51.279 - 00:09:52.470` | right here |
| `00:09:52.480 - 00:09:53.350` | will |
| `00:09:53.360 - 00:09:55.030` | take the value that's in the |
| `00:09:55.040 - 00:09:57.430` | ra register and move it into the pc |
| `00:09:57.440 - 00:10:02.470` | thereby going back to the caller |
| `00:10:02.480 - 00:10:05.190` | if a function doesn't call any other |
| `00:10:05.200 - 00:10:07.110` | functions then we can leave the return |
| `00:10:07.120 - 00:10:09.190` | address in that register and we don't |
| `00:10:09.200 - 00:10:11.590` | need to save it on the stack |
| `00:10:11.600 - 00:10:13.670` | and in this particular function we see |
| `00:10:13.680 - 00:10:15.590` | nothing is pushed or popped onto the |
| `00:10:15.600 - 00:10:17.269` | stack so |
| `00:10:17.279 - 00:10:20.870` | that's what's happening here |
| `00:10:20.880 - 00:10:22.949` | in the risk five uh calling conventions |
| `00:10:22.959 - 00:10:25.750` | we assume that any function can update |
| `00:10:25.760 - 00:10:27.910` | and use and modify |
| `00:10:27.920 - 00:10:30.790` | and overwrite the a registers and the t |
| `00:10:30.800 - 00:10:33.110` | registers so |
| `00:10:33.120 - 00:10:36.230` | uh anybody who calls switch |
| `00:10:36.240 - 00:10:38.150` | will not be able to make any assumptions |
| `00:10:38.160 - 00:10:39.910` | about the values of the |
| `00:10:39.920 - 00:10:40.949` | s |
| `00:10:40.959 - 00:10:43.030` | sorry the a and the t registers after it |
| `00:10:43.040 - 00:10:44.150` | returns |
| `00:10:44.160 - 00:10:46.470` | so that's why we don't bother saving |
| `00:10:46.480 - 00:10:49.509` | them but what do we do we save all the |
| `00:10:49.519 - 00:10:51.990` | other registers we save the ra and the |
| `00:10:52.000 - 00:10:54.230` | stack pointer and we save all the s |
| `00:10:54.240 - 00:10:56.790` | registers and where do we save them |
| `00:10:56.800 - 00:10:58.870` | well the first parameter the pointer to |
| `00:10:58.880 - 00:11:01.990` | the old context where we save the state |
| `00:11:02.000 - 00:11:04.310` | of the previously executing thread |
| `00:11:04.320 - 00:11:06.310` | is in a0 |
| `00:11:06.320 - 00:11:08.550` | the pointer to the context for the |
| `00:11:08.560 - 00:11:10.790` | thread we're going to be executing next |
| `00:11:10.800 - 00:11:12.069` | is in the |
| `00:11:12.079 - 00:11:14.630` | context pointed to by the new |
| `00:11:14.640 - 00:11:18.310` | argument which is in a1 so a0 is where |
| `00:11:18.320 - 00:11:21.269` | we save everything here we see a store |
| `00:11:21.279 - 00:11:24.710` | double word and we store registers r a s |
| `00:11:24.720 - 00:11:28.150` | p and the s registers |
| `00:11:28.160 - 00:11:32.069` | the gp register is not used in the xv6 |
| `00:11:32.079 - 00:11:34.230` | kernel so we don't bother with that |
| `00:11:34.240 - 00:11:36.550` | the tp register contains the number of |
| `00:11:36.560 - 00:11:38.630` | the core we're currently executing on |
| `00:11:38.640 - 00:11:41.829` | and that won't change we are executing |
| `00:11:41.839 - 00:11:44.150` | on a certain core when we |
| `00:11:44.160 - 00:11:45.670` | do this saving and we are still |
| `00:11:45.680 - 00:11:48.069` | executing on that core so we don't |
| `00:11:48.079 - 00:11:50.790` | change the tp register |
| `00:11:50.800 - 00:11:51.750` | so |
| `00:11:51.760 - 00:11:53.590` | after we save the state of the |
| `00:11:53.600 - 00:11:57.509` | previously executing thread we then load |
| `00:11:57.519 - 00:12:00.069` | the state of the next thread using the |
| `00:12:00.079 - 00:12:02.230` | pointer to the new context and we load |
| `00:12:02.240 - 00:12:04.790` | those exact same registers |
| `00:12:04.800 - 00:12:07.670` | so here we're saving the return address |
| `00:12:07.680 - 00:12:10.550` | from the function that called us |
| `00:12:10.560 - 00:12:12.310` | and here we're loading the return |
| `00:12:12.320 - 00:12:15.829` | address from some earlier thing we saved |
| `00:12:15.839 - 00:12:19.030` | and so this return will not return |
| `00:12:19.040 - 00:12:20.790` | directly instead it will return to |
| `00:12:20.800 - 00:12:22.389` | something else |
| `00:12:22.399 - 00:12:25.829` | so that's it's kind of kind of weird so |
| `00:12:25.839 - 00:12:29.509` | in this picture here that i showed |
| `00:12:29.519 - 00:12:31.750` | here we're calling switch |
| `00:12:31.760 - 00:12:34.069` | and here we're returning from switch |
| `00:12:34.079 - 00:12:36.470` | but this call that we are executing here |
| `00:12:36.480 - 00:12:37.590` | to switch |
| `00:12:37.600 - 00:12:39.590` | doesn't return immediately |
| `00:12:39.600 - 00:12:41.750` | to the same thread |
| `00:12:41.760 - 00:12:43.829` | it returns to some other thread |
| `00:12:43.839 - 00:12:46.389` | likewise this call here returns over to |
| `00:12:46.399 - 00:12:47.829` | this thread |
| `00:12:47.839 - 00:12:49.190` | okay so |
| `00:12:49.200 - 00:12:51.269` | that's switch |
| `00:12:51.279 - 00:12:53.430` | now let's take a look at |
| `00:12:53.440 - 00:12:56.710` | the yield function and see how and walk |
| `00:12:56.720 - 00:13:01.670` | through that code |
| `00:13:01.680 - 00:13:04.150` | yield give up the cpu for one scheduling |
| `00:13:04.160 - 00:13:07.350` | round so the primary thing it does is it |
| `00:13:07.360 - 00:13:09.430` | calls skid |
| `00:13:09.440 - 00:13:11.670` | okay but before it does that does it |
| `00:13:11.680 - 00:13:13.590` | gets a pointer to the current process |
| `00:13:13.600 - 00:13:15.829` | we're executing the process p gets a |
| `00:13:15.839 - 00:13:16.949` | pointer to |
| `00:13:16.959 - 00:13:19.829` | the the proc and then it acquires the |
| `00:13:19.839 - 00:13:22.389` | lock remember that the proc structure |
| `00:13:22.399 - 00:13:25.030` | has a lock field that protects things |
| `00:13:25.040 - 00:13:26.310` | like the state we're going to be |
| `00:13:26.320 - 00:13:28.069` | changing the state from |
| `00:13:28.079 - 00:13:29.670` | running to runnable |
| `00:13:29.680 - 00:13:32.710` | so that's what we do we acquire the lock |
| `00:13:32.720 - 00:13:35.430` | and then we change the state to runnable |
| `00:13:35.440 - 00:13:37.590` | then we call sked |
| `00:13:37.600 - 00:13:40.470` | and we go away and maybe quite a while |
| `00:13:40.480 - 00:13:42.389` | later after some other processes have |
| `00:13:42.399 - 00:13:45.430` | had time slices ultimately |
| `00:13:45.440 - 00:13:47.829` | the scheduler function we'll |
| `00:13:47.839 - 00:13:48.710` | call |
| `00:13:48.720 - 00:13:50.710` | switch again and we'll return back to |
| `00:13:50.720 - 00:13:52.550` | this |
| `00:13:52.560 - 00:13:54.629` | thread so at some point sched will |
| `00:13:54.639 - 00:13:55.829` | return |
| `00:13:55.839 - 00:13:58.389` | and at that point we release the lock |
| `00:13:58.399 - 00:14:01.670` | that we acquired here |
| `00:14:01.680 - 00:14:03.750` | yield is called in exactly two places |
| `00:14:03.760 - 00:14:05.910` | it's called from user trap |
| `00:14:05.920 - 00:14:08.310` | and kernel trap so whenever a trap |
| `00:14:08.320 - 00:14:10.389` | occurs when we're running in user mode |
| `00:14:10.399 - 00:14:12.790` | we uh execute user trap and if we've had |
| `00:14:12.800 - 00:14:15.430` | a timer interrupt we will call yield and |
| `00:14:15.440 - 00:14:17.110` | likewise if we're executing in |
| `00:14:17.120 - 00:14:18.629` | supervisor mode we |
| `00:14:18.639 - 00:14:20.710` | have a function called kernel trap and |
| `00:14:20.720 - 00:14:23.590` | if the timer has gone off we will call |
| `00:14:23.600 - 00:14:25.829` | yield but in any case uh we will be |
| `00:14:25.839 - 00:14:27.910` | calling it from a trap handler |
| `00:14:27.920 - 00:14:31.030` | interrupts will be disabled and at that |
| `00:14:31.040 - 00:14:33.430` | time no locks will be held |
| `00:14:33.440 - 00:14:35.350` | now here we do an acquire |
| `00:14:35.360 - 00:14:37.750` | and then a release |
| `00:14:37.760 - 00:14:39.910` | as you remember the acquire is a spin |
| `00:14:39.920 - 00:14:41.910` | lock and what it will do is it will |
| `00:14:41.920 - 00:14:45.269` | disable interrupts and then it will |
| `00:14:45.279 - 00:14:47.350` | remember whether they were enabled |
| `00:14:47.360 - 00:14:50.310` | previously or not well |
| `00:14:50.320 - 00:14:51.910` | they are not so |
| `00:14:51.920 - 00:14:53.670` | it will save that status in this |
| `00:14:53.680 - 00:14:56.310` | interrupts enabled previously field |
| `00:14:56.320 - 00:14:59.509` | will always be false and since no locks |
| `00:14:59.519 - 00:15:00.629` | are held |
| `00:15:00.639 - 00:15:03.030` | uh it will increment the counter for the |
| `00:15:03.040 - 00:15:06.470` | locks the in off counter and it will |
| `00:15:06.480 - 00:15:08.870` | increment it from zero and so it will be |
| `00:15:08.880 - 00:15:11.829` | one so at this point |
| `00:15:11.839 - 00:15:13.750` | when we call sked |
| `00:15:13.760 - 00:15:16.949` | the in off field will be one and |
| `00:15:16.959 - 00:15:19.430` | interrupts will be disabled |
| `00:15:19.440 - 00:15:21.590` | okay so now let's take a look at what |
| `00:15:21.600 - 00:15:24.829` | happens in skid |
| `00:15:24.839 - 00:15:26.790` | so |
| `00:15:26.800 - 00:15:27.829` | we are |
| `00:15:27.839 - 00:15:30.870` | grabbing a pointer to the my proc field |
| `00:15:30.880 - 00:15:34.710` | and we are asking uh uh doing some error |
| `00:15:34.720 - 00:15:36.389` | checking here |
| `00:15:36.399 - 00:15:37.990` | we make sure that we are holding the |
| `00:15:38.000 - 00:15:38.870` | lock |
| `00:15:38.880 - 00:15:41.430` | okay well we acquired it right |
| `00:15:41.440 - 00:15:42.710` | here |
| `00:15:42.720 - 00:15:44.629` | sked can be called sked can be called |
| `00:15:44.639 - 00:15:47.030` | from some other places as well |
| `00:15:47.040 - 00:15:49.670` | but let's just focus on this call from |
| `00:15:49.680 - 00:15:50.949` | yield |
| `00:15:50.959 - 00:15:52.230` | so we make sure that we're holding the |
| `00:15:52.240 - 00:15:53.110` | lock |
| `00:15:53.120 - 00:15:56.150` | we make sure that n off is one |
| `00:15:56.160 - 00:15:58.710` | and we make sure that |
| `00:15:58.720 - 00:16:03.509` | the state is |
| `00:16:03.519 - 00:16:05.430` | not running |
| `00:16:05.440 - 00:16:08.069` | and then we also make sure that |
| `00:16:08.079 - 00:16:11.430` | interrupts are disabled so |
| `00:16:11.440 - 00:16:12.949` | if interrupts are enabled this would |
| `00:16:12.959 - 00:16:14.710` | return true and we would print an error |
| `00:16:14.720 - 00:16:16.629` | message |
| `00:16:16.639 - 00:16:17.590` | so |
| `00:16:17.600 - 00:16:20.629` | now we're going to save the value of |
| `00:16:20.639 - 00:16:24.150` | int ena interrupts enabled so in this |
| `00:16:24.160 - 00:16:25.910` | particular case when we're calling it |
| `00:16:25.920 - 00:16:29.350` | from yield it will always be false |
| `00:16:29.360 - 00:16:31.749` | so we're going to save that state but if |
| `00:16:31.759 - 00:16:34.470` | we call schedule sked from someplace |
| `00:16:34.480 - 00:16:37.110` | else it might be true so in any case we |
| `00:16:37.120 - 00:16:39.350` | save the previous value of |
| `00:16:39.360 - 00:16:42.389` | int ena interrupts enabled |
| `00:16:42.399 - 00:16:44.150` | and we save it in a local variable and |
| `00:16:44.160 - 00:16:45.990` | then we restore it later after the call |
| `00:16:46.000 - 00:16:47.110` | to switch |
| `00:16:47.120 - 00:16:49.670` | from that same location |
| `00:16:49.680 - 00:16:51.509` | is this local variable kept in a |
| `00:16:51.519 - 00:16:53.829` | register or is it kept on us on the |
| `00:16:53.839 - 00:16:55.030` | stack |
| `00:16:55.040 - 00:16:56.710` | we don't know exactly what the compiler |
| `00:16:56.720 - 00:16:59.590` | will do but it doesn't really matter all |
| `00:16:59.600 - 00:17:01.910` | we know is that the state of this thread |
| `00:17:01.920 - 00:17:04.710` | will be entirely saved and whether |
| `00:17:04.720 - 00:17:07.350` | this local variable is on the stack or |
| `00:17:07.360 - 00:17:09.590` | in a register it doesn't really matter |
| `00:17:09.600 - 00:17:10.949` | the point is |
| `00:17:10.959 - 00:17:12.069` | that |
| `00:17:12.079 - 00:17:15.110` | is saved in this in in some data that |
| `00:17:15.120 - 00:17:16.949` | this thread |
| `00:17:16.959 - 00:17:19.270` | has and it will be restored so then we |
| `00:17:19.280 - 00:17:21.510` | restore the value of it and then we |
| `00:17:21.520 - 00:17:26.150` | return and release the lock |
| `00:17:26.160 - 00:17:28.390` | now let's take a look at the scheduler |
| `00:17:28.400 - 00:17:29.430` | function |
| `00:17:29.440 - 00:17:33.669` | and uh see what it looks like |
| `00:17:33.679 - 00:17:35.350` | so it's uh pretty short it just goes |
| `00:17:35.360 - 00:17:37.430` | from here down to here |
| `00:17:37.440 - 00:17:39.510` | and it contains an infinite loop |
| `00:17:39.520 - 00:17:41.190` | okay |
| `00:17:41.200 - 00:17:43.430` | throughout this function |
| `00:17:43.440 - 00:17:44.549` | c is |
| `00:17:44.559 - 00:17:46.950` | set to the |
| `00:17:46.960 - 00:17:49.669` | um |
| `00:17:49.679 - 00:17:51.510` | pointer to the cpu structure for this |
| `00:17:51.520 - 00:17:53.510` | particular core |
| `00:17:53.520 - 00:17:55.669` | each core will execute its |
| `00:17:55.679 - 00:17:57.590` | a single thread for the scheduler |
| `00:17:57.600 - 00:17:59.510` | function and this function will you know |
| `00:17:59.520 - 00:18:02.310` | be executed on one core of course it'll |
| `00:18:02.320 - 00:18:03.750` | be executed on the other course |
| `00:18:03.760 - 00:18:05.990` | simultaneously but let's just focus on |
| `00:18:06.000 - 00:18:08.470` | this one particular core so we get a |
| `00:18:08.480 - 00:18:11.909` | pointer to the cpu struct that describes |
| `00:18:11.919 - 00:18:13.669` | this core |
| `00:18:13.679 - 00:18:15.909` | and then we've got an infinite loop here |
| `00:18:15.919 - 00:18:17.830` | and uh inside of that we've got another |
| `00:18:17.840 - 00:18:20.070` | loop so let's look at this loop first |
| `00:18:20.080 - 00:18:21.909` | what is this doing well it's going |
| `00:18:21.919 - 00:18:24.870` | through this proc array one by one |
| `00:18:24.880 - 00:18:28.549` | okay so it's uh here's our array |
| `00:18:28.559 - 00:18:30.230` | we have 64 |
| `00:18:30.240 - 00:18:31.990` | processes and it's going through each |
| `00:18:32.000 - 00:18:33.909` | one of them one by one and looking at |
| `00:18:33.919 - 00:18:35.669` | the proc structures |
| `00:18:35.679 - 00:18:36.549` | and |
| `00:18:36.559 - 00:18:39.029` | we see that's going here |
| `00:18:39.039 - 00:18:42.789` | and for each one |
| `00:18:42.799 - 00:18:44.150` | it |
| `00:18:44.160 - 00:18:46.310` | asks whether it's runnable or not and if |
| `00:18:46.320 - 00:18:47.990` | so it calls switch |
| `00:18:48.000 - 00:18:51.350` | so we acquire the lock for that process |
| `00:18:51.360 - 00:18:54.549` | right we're going to be looking at the |
| `00:18:54.559 - 00:18:57.909` | state variable and changing the state |
| `00:18:57.919 - 00:18:59.750` | variable so we need to hold the lock |
| `00:18:59.760 - 00:19:01.350` | while we're doing that so we acquire the |
| `00:19:01.360 - 00:19:02.870` | lock |
| `00:19:02.880 - 00:19:04.870` | on this particular |
| `00:19:04.880 - 00:19:05.990` | process |
| `00:19:06.000 - 00:19:08.230` | and if it is runnable |
| `00:19:08.240 - 00:19:09.830` | we do this if it's not runnable we |
| `00:19:09.840 - 00:19:11.350` | release the lock and move on and look at |
| `00:19:11.360 - 00:19:13.190` | the next one and when we've gone through |
| `00:19:13.200 - 00:19:15.909` | them all we then repeat the outer loop |
| `00:19:15.919 - 00:19:17.830` | and uh start from the beginning |
| `00:19:17.840 - 00:19:19.990` | beginning again |
| `00:19:20.000 - 00:19:22.630` | if the process is runnable then we're |
| `00:19:22.640 - 00:19:26.070` | going to change its state to running |
| `00:19:26.080 - 00:19:27.909` | okay we've selected it we're also going |
| `00:19:27.919 - 00:19:28.630` | to |
| `00:19:28.640 - 00:19:29.750` | store |
| `00:19:29.760 - 00:19:31.430` | in the cpu |
| `00:19:31.440 - 00:19:32.390` | struct |
| `00:19:32.400 - 00:19:33.669` | a pointer to |
| `00:19:33.679 - 00:19:36.710` | the relevant proc structure so that we |
| `00:19:36.720 - 00:19:39.029` | know which process we're running |
| `00:19:39.039 - 00:19:41.029` | and then we're going to call switch |
| `00:19:41.039 - 00:19:44.150` | and here we see the call to switch we're |
| `00:19:44.160 - 00:19:47.190` | providing it a pointer to |
| `00:19:47.200 - 00:19:48.950` | the context |
| `00:19:48.960 - 00:19:50.150` | for |
| `00:19:50.160 - 00:19:52.710` | the current core and a pointer to the |
| `00:19:52.720 - 00:19:54.710` | context for the new process that we're |
| `00:19:54.720 - 00:19:57.669` | going to be executing so we're passing |
| `00:19:57.679 - 00:20:00.390` | switch |
| `00:20:00.400 - 00:20:01.990` | for the old |
| `00:20:02.000 - 00:20:04.149` | argument a pointer to |
| `00:20:04.159 - 00:20:07.110` | the area in which to save the registers |
| `00:20:07.120 - 00:20:09.590` | for the scheduler thread |
| `00:20:09.600 - 00:20:11.270` | and in the new |
| `00:20:11.280 - 00:20:12.870` | argument we're passing it a pointer to |
| `00:20:12.880 - 00:20:15.909` | the context area for the proc structure |
| `00:20:15.919 - 00:20:18.789` | and that's where to load the registers |
| `00:20:18.799 - 00:20:20.549` | for the |
| `00:20:20.559 - 00:20:22.149` | process that we're about to |
| `00:20:22.159 - 00:20:24.070` | start executing |
| `00:20:24.080 - 00:20:26.870` | so we call switch and |
| `00:20:26.880 - 00:20:29.590` | then after switch comes back we're no |
| `00:20:29.600 - 00:20:32.710` | longer executing that process so finally |
| `00:20:32.720 - 00:20:36.470` | we set the proc field to null |
| `00:20:36.480 - 00:20:38.789` | and so let me |
| `00:20:38.799 - 00:20:43.029` | just show how that's working here |
| `00:20:43.039 - 00:20:47.110` | we saw all of this going on |
| `00:20:47.120 - 00:20:49.510` | now let me look at it this way here |
| `00:20:49.520 - 00:20:50.310` | so |
| `00:20:50.320 - 00:20:52.789` | here i'm showing it from the |
| `00:20:52.799 - 00:20:54.789` | user's point of view we have a trap we |
| `00:20:54.799 - 00:20:56.310` | go into switch |
| `00:20:56.320 - 00:20:58.149` | the scheduler executes give some others |
| `00:20:58.159 - 00:20:59.909` | some time slots time slices and |
| `00:20:59.919 - 00:21:01.909` | eventually we come back execute the s |
| `00:21:01.919 - 00:21:04.070` | return back in the user |
| `00:21:04.080 - 00:21:06.230` | the user's code but now let's look at it |
| `00:21:06.240 - 00:21:09.669` | from the scheduler's point of view |
| `00:21:09.679 - 00:21:10.789` | we are |
| `00:21:10.799 - 00:21:13.270` | choosing some process p to run acquiring |
| `00:21:13.280 - 00:21:15.590` | the lock on p and changing its state to |
| `00:21:15.600 - 00:21:16.870` | running |
| `00:21:16.880 - 00:21:19.350` | and we are also updating the proc field |
| `00:21:19.360 - 00:21:20.870` | and then we're switching |
| `00:21:20.880 - 00:21:21.909` | into that |
| `00:21:21.919 - 00:21:23.110` | process |
| `00:21:23.120 - 00:21:25.590` | and then at some point later |
| `00:21:25.600 - 00:21:29.110` | that process will give up the cpu okay |
| `00:21:29.120 - 00:21:30.950` | timer if nothing else a timer interrupt |
| `00:21:30.960 - 00:21:33.350` | will happen and cause a trap or it may |
| `00:21:33.360 - 00:21:35.430` | be something else but in any case the |
| `00:21:35.440 - 00:21:37.750` | timer interrupt causes a trap and we uh |
| `00:21:37.760 - 00:21:40.549` | come back into yield and then |
| `00:21:40.559 - 00:21:42.149` | we call switch again |
| `00:21:42.159 - 00:21:44.870` | only when we call switch in yield |
| `00:21:44.880 - 00:21:46.870` | we will provide the context in the |
| `00:21:46.880 - 00:21:49.270` | reverse order |
| `00:21:49.280 - 00:21:50.070` | so |
| `00:21:50.080 - 00:21:52.710` | just let me go in there |
| `00:21:52.720 - 00:21:55.270` | uh we call yield we call sked |
| `00:21:55.280 - 00:21:58.390` | and in sked uh calls |
| `00:21:58.400 - 00:22:01.190` | switch and this time the old is the |
| `00:22:01.200 - 00:22:02.390` | processes |
| `00:22:02.400 - 00:22:04.230` | context so that's where we're going to |
| `00:22:04.240 - 00:22:08.230` | save the registers from the processes |
| `00:22:08.240 - 00:22:09.750` | registers we're going to save the |
| `00:22:09.760 - 00:22:13.909` | processes state and we're going to load |
| `00:22:13.919 - 00:22:16.789` | the registers for the scheduler thread |
| `00:22:16.799 - 00:22:18.549` | so we're going to go to the my cpu |
| `00:22:18.559 - 00:22:22.230` | struct and use its context |
| `00:22:22.240 - 00:22:27.750` | so |
| `00:22:27.760 - 00:22:28.630` | uh |
| `00:22:28.640 - 00:22:32.070` | so then switch comes back so |
| `00:22:32.080 - 00:22:33.909` | we set proc to p |
| `00:22:33.919 - 00:22:37.190` | and invoke switch then uh some something |
| `00:22:37.200 - 00:22:38.549` | goes on eventually get the timer |
| `00:22:38.559 - 00:22:40.870` | interrupt yield happens and we have |
| `00:22:40.880 - 00:22:43.029` | switch and then we're back in the |
| `00:22:43.039 - 00:22:45.909` | scheduler thread so we then |
| `00:22:45.919 - 00:22:48.070` | finish off our loop by setting the proc |
| `00:22:48.080 - 00:22:50.710` | field to null and we release the lock on |
| `00:22:50.720 - 00:22:52.630` | p |
| `00:22:52.640 - 00:22:53.590` | so |
| `00:22:53.600 - 00:22:55.830` | we see exactly that going on here we |
| `00:22:55.840 - 00:22:57.190` | call switch |
| `00:22:57.200 - 00:22:59.510` | we come back we set proc to null and |
| `00:22:59.520 - 00:23:01.990` | then we release the uh |
| `00:23:02.000 - 00:23:04.789` | lock for p and then we iterate now one |
| `00:23:04.799 - 00:23:07.270` | thing uh i |
| `00:23:07.280 - 00:23:09.430` | do say something that we say choose p i |
| `00:23:09.440 - 00:23:11.029` | say here choose p and then acquire the |
| `00:23:11.039 - 00:23:12.230` | lock on p |
| `00:23:12.240 - 00:23:13.350` | well |
| `00:23:13.360 - 00:23:15.190` | in order to choose p we have to find one |
| `00:23:15.200 - 00:23:17.270` | that's runnable and we can't look at the |
| `00:23:17.280 - 00:23:19.350` | state without locking it first so |
| `00:23:19.360 - 00:23:20.549` | actually we do things in a slightly |
| `00:23:20.559 - 00:23:23.430` | different order we acquire the lock then |
| `00:23:23.440 - 00:23:25.190` | we look at the state to see if we found |
| `00:23:25.200 - 00:23:28.070` | one that's runnable |
| `00:23:28.080 - 00:23:29.830` | but that's just a |
| `00:23:29.840 - 00:23:31.350` | minor detail |
| `00:23:31.360 - 00:23:33.909` | the switch function is called in exactly |
| `00:23:33.919 - 00:23:35.270` | two places |
| `00:23:35.280 - 00:23:38.630` | it's called in the sked function |
| `00:23:38.640 - 00:23:41.190` | to go into the scheduler function |
| `00:23:41.200 - 00:23:42.710` | and it's called in the scheduler |
| `00:23:42.720 - 00:23:46.310` | function to go back into some process |
| `00:23:46.320 - 00:23:48.789` | okay so whenever it's called in scad it |
| `00:23:48.799 - 00:23:52.870` | is always switching from a process |
| `00:23:52.880 - 00:23:55.029` | to the scheduler thread |
| `00:23:55.039 - 00:23:56.630` | and when it's called in the scheduler |
| `00:23:56.640 - 00:23:57.750` | thread |
| `00:23:57.760 - 00:23:59.750` | it's always going from the schedule |
| `00:23:59.760 - 00:24:02.149` | thread into a process |
| `00:24:02.159 - 00:24:04.789` | so if we look at the code for |
| `00:24:04.799 - 00:24:07.190` | sked |
| `00:24:07.200 - 00:24:08.230` | here |
| `00:24:08.240 - 00:24:10.950` | we see that switch is being called |
| `00:24:10.960 - 00:24:14.149` | and it's being passed a pointer to the |
| `00:24:14.159 - 00:24:16.230` | old context where it saves the state of |
| `00:24:16.240 - 00:24:18.390` | the previously running thread and a |
| `00:24:18.400 - 00:24:21.029` | pointer to the new context |
| `00:24:21.039 - 00:24:22.710` | so it will save |
| `00:24:22.720 - 00:24:26.630` | the current registers in the context for |
| `00:24:26.640 - 00:24:28.470` | the process that is |
| `00:24:28.480 - 00:24:29.990` | that it is leaving |
| `00:24:30.000 - 00:24:31.990` | and it will load the registers from the |
| `00:24:32.000 - 00:24:33.510` | context area |
| `00:24:33.520 - 00:24:37.029` | for the cpu that is the scheduler thread |
| `00:24:37.039 - 00:24:38.390` | okay that's |
| `00:24:38.400 - 00:24:40.070` | going in |
| `00:24:40.080 - 00:24:43.029` | this direction okay in this direction |
| `00:24:43.039 - 00:24:45.510` | it's called from the scheduler function |
| `00:24:45.520 - 00:24:47.110` | okay i'm showing that right here here's |
| `00:24:47.120 - 00:24:48.950` | the scheduler function |
| `00:24:48.960 - 00:24:50.549` | and we |
| `00:24:50.559 - 00:24:53.510` | call switch here and it's going |
| `00:24:53.520 - 00:24:55.110` | from |
| `00:24:55.120 - 00:24:55.909` | the |
| `00:24:55.919 - 00:24:57.909` | scheduler function or the scheduler |
| `00:24:57.919 - 00:25:01.110` | thread to the process to some process |
| `00:25:01.120 - 00:25:03.110` | that's now going to be running |
| `00:25:03.120 - 00:25:05.430` | so it will save the scheduler's |
| `00:25:05.440 - 00:25:07.830` | registers in the context associated with |
| `00:25:07.840 - 00:25:11.190` | the current cpu the current core and it |
| `00:25:11.200 - 00:25:13.110` | will load the registers |
| `00:25:13.120 - 00:25:14.789` | for the |
| `00:25:14.799 - 00:25:17.110` | process that's going to be executing |
| `00:25:17.120 - 00:25:19.190` | next |
| `00:25:19.200 - 00:25:21.190` | another thing i want to mention |
| `00:25:21.200 - 00:25:23.510` | is that we always |
| `00:25:23.520 - 00:25:26.789` | have acquires and releases of locks |
| `00:25:26.799 - 00:25:29.190` | being paired so |
| `00:25:29.200 - 00:25:31.430` | if we look in the yield function for |
| `00:25:31.440 - 00:25:33.669` | example we see an acquire |
| `00:25:33.679 - 00:25:35.350` | and a release |
| `00:25:35.360 - 00:25:37.990` | so here's the yield function and we see |
| `00:25:38.000 - 00:25:40.549` | that this locked is being acquired here |
| `00:25:40.559 - 00:25:42.789` | and released here |
| `00:25:42.799 - 00:25:46.950` | and if we look in the scheduler function |
| `00:25:46.960 - 00:25:48.710` | we see a similar kind of thing going on |
| `00:25:48.720 - 00:25:50.310` | so here's the scheduler function and |
| `00:25:50.320 - 00:25:52.310` | we're acquiring the lock and we're |
| `00:25:52.320 - 00:25:55.110` | releasing it and it would seem that this |
| `00:25:55.120 - 00:25:57.430` | acquire in this release are paired up |
| `00:25:57.440 - 00:26:00.789` | but that's not really quite right |
| `00:26:00.799 - 00:26:03.750` | so here we have the lock being acquired |
| `00:26:03.760 - 00:26:06.310` | in the yield function |
| `00:26:06.320 - 00:26:07.269` | and |
| `00:26:07.279 - 00:26:09.029` | it's a spin lock we shouldn't hold spin |
| `00:26:09.039 - 00:26:11.029` | locks for very long and we see it being |
| `00:26:11.039 - 00:26:13.510` | released very soon after in the |
| `00:26:13.520 - 00:26:15.990` | scheduler function |
| `00:26:16.000 - 00:26:16.950` | and |
| `00:26:16.960 - 00:26:19.190` | here we see the lock being acquired in |
| `00:26:19.200 - 00:26:20.789` | the scheduler function |
| `00:26:20.799 - 00:26:23.350` | and then after just a few instructions |
| `00:26:23.360 - 00:26:24.630` | being released |
| `00:26:24.640 - 00:26:25.510` | in |
| `00:26:25.520 - 00:26:28.310` | the yield function so they are paired up |
| `00:26:28.320 - 00:26:29.750` | but they're paired in sort of a funny |
| `00:26:29.760 - 00:26:31.110` | way |
| `00:26:31.120 - 00:26:32.870` | for example we might be acquiring this |
| `00:26:32.880 - 00:26:34.950` | lock when running on one core and |
| `00:26:34.960 - 00:26:36.630` | release it |
| `00:26:36.640 - 00:26:39.830` | pretty soon after and then later it may |
| `00:26:39.840 - 00:26:41.110` | be that some |
| `00:26:41.120 - 00:26:42.310` | other core |
| `00:26:42.320 - 00:26:45.430` | decides to choose p and to run it so it |
| `00:26:45.440 - 00:26:47.990` | will acquire that lock and |
| `00:26:48.000 - 00:26:48.789` | then |
| `00:26:48.799 - 00:26:52.310` | begin that process running and then the |
| `00:26:52.320 - 00:26:55.110` | yield function will release that lock so |
| `00:26:55.120 - 00:26:56.630` | in fact the lock could be acquired on |
| `00:26:56.640 - 00:27:01.510` | one core and released on another core |
| `00:27:01.520 - 00:27:02.950` | another thing i want to point out is |
| `00:27:02.960 - 00:27:03.909` | that |
| `00:27:03.919 - 00:27:05.590` | whenever we call |
| `00:27:05.600 - 00:27:08.630` | the switch function and we switch from |
| `00:27:08.640 - 00:27:09.990` | a process to |
| `00:27:10.000 - 00:27:11.350` | the |
| `00:27:11.360 - 00:27:13.669` | scheduler thread we will always have |
| `00:27:13.679 - 00:27:16.470` | interrupts disabled and we know that for |
| `00:27:16.480 - 00:27:18.389` | sure because well |
| `00:27:18.399 - 00:27:21.110` | here's the sched function where we call |
| `00:27:21.120 - 00:27:23.190` | switch and right before that we check to |
| `00:27:23.200 - 00:27:25.909` | make sure that interrupts are disabled |
| `00:27:25.919 - 00:27:28.070` | so interrupts are always disabled when |
| `00:27:28.080 - 00:27:29.269` | we come |
| `00:27:29.279 - 00:27:32.549` | back into the scheduler function |
| `00:27:32.559 - 00:27:35.110` | so if we look at the scheduler function |
| `00:27:35.120 - 00:27:38.870` | we see that we are going um |
| `00:27:38.880 - 00:27:40.710` | here we call switch but then at some |
| `00:27:40.720 - 00:27:43.190` | point we come back okay so we return |
| `00:27:43.200 - 00:27:46.149` | from switch from after executing a |
| `00:27:46.159 - 00:27:49.750` | processor's time slice and at that point |
| `00:27:49.760 - 00:27:51.750` | or this point here interrupts will be |
| `00:27:51.760 - 00:27:53.029` | disabled |
| `00:27:53.039 - 00:27:55.830` | and we release the lock |
| `00:27:55.840 - 00:27:57.269` | but that |
| `00:27:57.279 - 00:27:59.909` | interrupts may remain disabled and we |
| `00:27:59.919 - 00:28:00.830` | loop |
| `00:28:00.840 - 00:28:04.630` | back now uh if we |
| `00:28:04.640 - 00:28:06.710` | go into another process |
| `00:28:06.720 - 00:28:08.470` | right if we go into another process we |
| `00:28:08.480 - 00:28:10.549` | choose another runnable process and we |
| `00:28:10.559 - 00:28:12.070` | switch into it |
| `00:28:12.080 - 00:28:13.830` | then |
| `00:28:13.840 - 00:28:16.549` | we're going to do the switch |
| `00:28:16.559 - 00:28:17.669` | and |
| `00:28:17.679 - 00:28:20.310` | we come back here now this yield was |
| `00:28:20.320 - 00:28:22.710` | being executed as part of the |
| `00:28:22.720 - 00:28:26.149` | user trap function and |
| `00:28:26.159 - 00:28:28.230` | interrupts are disabled at the time the |
| `00:28:28.240 - 00:28:30.149` | trap occurs and they will remain |
| `00:28:30.159 - 00:28:33.669` | disabled for the yield function so |
| `00:28:33.679 - 00:28:35.909` | this acquire here |
| `00:28:35.919 - 00:28:37.510` | will remember the previous state of the |
| `00:28:37.520 - 00:28:40.230` | interrupts and this release will then |
| `00:28:40.240 - 00:28:41.350` | not |
| `00:28:41.360 - 00:28:43.750` | re-enable the interrupts at this point |
| `00:28:43.760 - 00:28:45.510` | but |
| `00:28:45.520 - 00:28:47.350` | we restore the state of the user |
| `00:28:47.360 - 00:28:49.190` | registers and so on and then we execute |
| `00:28:49.200 - 00:28:51.590` | the srat instruction and at that point |
| `00:28:51.600 - 00:28:53.990` | interrupts will be enabled and so they |
| `00:28:54.000 - 00:28:56.870` | will become enabled when we |
| `00:28:56.880 - 00:28:58.630` | go into |
| `00:28:58.640 - 00:29:01.110` | the next process |
| `00:29:01.120 - 00:29:02.630` | but it's possible that there won't be a |
| `00:29:02.640 - 00:29:04.389` | next process i mean it's it's possible |
| `00:29:04.399 - 00:29:07.350` | that all of the processes on the |
| `00:29:07.360 - 00:29:09.909` | in the array are not |
| `00:29:09.919 - 00:29:11.590` | runnable so we we might not be able to |
| `00:29:11.600 - 00:29:13.510` | find one that's runnable and what what |
| `00:29:13.520 - 00:29:16.389` | would we do we would just um if we go |
| `00:29:16.399 - 00:29:17.909` | through the entire array and don't find |
| `00:29:17.919 - 00:29:19.909` | anything that's runnable then we uh come |
| `00:29:19.919 - 00:29:22.549` | out of this this for loop here and we go |
| `00:29:22.559 - 00:29:25.350` | back to iterate this while loop here and |
| `00:29:25.360 - 00:29:27.909` | we see that we're turning interrupts on |
| `00:29:27.919 - 00:29:30.310` | so we could get into a situation where |
| `00:29:30.320 - 00:29:32.389` | there are no runnable processes it could |
| `00:29:32.399 - 00:29:34.389` | be that we're waiting for |
| `00:29:34.399 - 00:29:36.310` | um |
| `00:29:36.320 - 00:29:39.110` | that all the processes are are sleeping |
| `00:29:39.120 - 00:29:40.630` | or waiting on something it could we |
| `00:29:40.640 - 00:29:42.230` | could be waiting on user input for |
| `00:29:42.240 - 00:29:45.590` | example and so there may be no runnable |
| `00:29:45.600 - 00:29:47.430` | processes and we could get into a |
| `00:29:47.440 - 00:29:50.310` | situation then well if they're disabled |
| `00:29:50.320 - 00:29:52.310` | when we come out of this switch and we |
| `00:29:52.320 - 00:29:55.029` | don't re-enable them here um then they |
| `00:29:55.039 - 00:29:57.669` | stay disabled because we we're never |
| `00:29:57.679 - 00:29:59.590` | finding a runnable process |
| `00:29:59.600 - 00:30:01.510` | so that's what's going on here so you |
| `00:30:01.520 - 00:30:03.190` | see this comment avoid deadlock by |
| `00:30:03.200 - 00:30:05.269` | ensuring the devices |
| `00:30:05.279 - 00:30:07.110` | can interrupt so we do turn the |
| `00:30:07.120 - 00:30:08.630` | interrupts on |
| `00:30:08.640 - 00:30:10.789` | if we go through the entire array and uh |
| `00:30:10.799 - 00:30:12.230` | every time we go through the entire |
| `00:30:12.240 - 00:30:15.110` | array once we turn them on at least for |
| `00:30:15.120 - 00:30:16.789` | a tiny amount of time but it's long |
| `00:30:16.799 - 00:30:17.830` | enough for |
| `00:30:17.840 - 00:30:19.110` | the |
| `00:30:19.120 - 00:30:22.630` | interrupt to occur and the trap to be uh |
| `00:30:22.640 - 00:30:25.350` | handled and that will possibly wake up |
| `00:30:25.360 - 00:30:26.149` | some |
| `00:30:26.159 - 00:30:28.950` | other processes so that's what's going |
| `00:30:28.960 - 00:30:31.190` | on right here |
| `00:30:31.200 - 00:30:33.110` | all right this this uh |
| `00:30:33.120 - 00:30:34.870` | stuff with the process switching i i |
| `00:30:34.880 - 00:30:36.389` | just find it the most interesting part |
| `00:30:36.399 - 00:30:38.950` | of the kernel and i hope you've enjoyed |
| `00:30:38.960 - 00:30:40.789` | this video as much as i've enjoyed |
| `00:30:40.799 - 00:30:41.909` | making it |
| `00:30:41.919 - 00:30:44.070` | all right i will |
| `00:30:44.080 - 00:30:45.830` | plan to continue making videos although |
| `00:30:45.840 - 00:30:48.149` | there may be a slight delay uh look |
| `00:30:48.159 - 00:30:51.840` | forward to seeing you in the next video |
