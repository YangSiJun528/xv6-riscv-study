# xv6 Kernel-10: Context Switching

| Field | Value |
| --- | --- |
| Video ID | `1sSanF_y8FY` |
| URL | <https://www.youtube.com/watch?v=1sSanF_y8FY> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.280 - 00:00:02.790` | this video is part of a series on the |
| `00:00:02.800 - 00:00:05.510` | xv6 operating system kernel |
| `00:00:05.520 - 00:00:07.749` | in this video i will talk about context |
| `00:00:07.759 - 00:00:08.870` | switching |
| `00:00:08.880 - 00:00:10.390` | i'll talk about how |
| `00:00:10.400 - 00:00:12.629` | traps and system return instructions are |
| `00:00:12.639 - 00:00:15.350` | used to implement time slices |
| `00:00:15.360 - 00:00:17.670` | and i'll talk about how the system |
| `00:00:17.680 - 00:00:20.630` | switches from one thread to another |
| `00:00:20.640 - 00:00:23.590` | let's start with this picture here |
| `00:00:23.600 - 00:00:26.470` | and i'm showing time flowing down the |
| `00:00:26.480 - 00:00:27.990` | page |
| `00:00:28.000 - 00:00:30.390` | here's a user thread and |
| `00:00:30.400 - 00:00:32.069` | this indicates instructions are being |
| `00:00:32.079 - 00:00:34.790` | executed until at some point a trap |
| `00:00:34.800 - 00:00:35.830` | occurs |
| `00:00:35.840 - 00:00:36.790` | and then |
| `00:00:36.800 - 00:00:39.510` | we switch into the kernel and execute |
| `00:00:39.520 - 00:00:42.310` | instructions in kernel mode |
| `00:00:42.320 - 00:00:44.229` | user thread instructions are executed in |
| `00:00:44.239 - 00:00:46.869` | user mode and anything executed in |
| `00:00:46.879 - 00:00:49.350` | kernel mode is by definition part of the |
| `00:00:49.360 - 00:00:51.670` | kernel |
| `00:00:51.680 - 00:00:55.590` | the switch from user mode to kernel mode |
| `00:00:55.600 - 00:00:58.549` | occurs as the result of a trap this can |
| `00:00:58.559 - 00:01:01.910` | be an interrupt such as from a device |
| `00:01:01.920 - 00:01:04.229` | that is requesting attention |
| `00:01:04.239 - 00:01:06.469` | or it could be requested by the user |
| `00:01:06.479 - 00:01:08.070` | thread itself |
| `00:01:08.080 - 00:01:11.109` | in the form of a system call or perhaps |
| `00:01:11.119 - 00:01:13.350` | the user thread has a program exception |
| `00:01:13.360 - 00:01:15.830` | some sort of an error and that results |
| `00:01:15.840 - 00:01:18.230` | in a trap as well |
| `00:01:18.240 - 00:01:20.310` | after the kernel executes and is ready |
| `00:01:20.320 - 00:01:24.469` | to return to the user thread the s-ret |
| `00:01:24.479 - 00:01:27.510` | or system return instruction is executed |
| `00:01:27.520 - 00:01:31.190` | and that takes us back into user mode |
| `00:01:31.200 - 00:01:33.510` | this could be a device requesting |
| `00:01:33.520 - 00:01:36.469` | attention so if it's an interrupt |
| `00:01:36.479 - 00:01:39.030` | the user thread will be unaware that |
| `00:01:39.040 - 00:01:42.389` | this trap has occurred and every and the |
| `00:01:42.399 - 00:01:44.310` | user thread will just uh be executing |
| `00:01:44.320 - 00:01:46.630` | instructions and really have no clue |
| `00:01:46.640 - 00:01:49.990` | that the trap and return happened |
| `00:01:50.000 - 00:01:51.350` | at the point of the trap all the |
| `00:01:51.360 - 00:01:53.670` | registers of the user thread should be |
| `00:01:53.680 - 00:01:55.910` | saved and will be saved |
| `00:01:55.920 - 00:01:58.950` | and at the system return those registers |
| `00:01:58.960 - 00:02:01.910` | will be just restored or reloaded if you |
| `00:02:01.920 - 00:02:02.709` | will |
| `00:02:02.719 - 00:02:05.510` | and so the user thread just picks up |
| `00:02:05.520 - 00:02:09.270` | where it left off |
| `00:02:09.280 - 00:02:12.869` | so the user thread experiences over time |
| `00:02:12.879 - 00:02:15.430` | a series of time slices it happened |
| `00:02:15.440 - 00:02:16.550` | here's one |
| `00:02:16.560 - 00:02:19.190` | and here's another and here's another |
| `00:02:19.200 - 00:02:21.430` | it this trap could be the result of a |
| `00:02:21.440 - 00:02:24.070` | timer interrupt in which case uh the |
| `00:02:24.080 - 00:02:26.470` | kernel will go off in this region and do |
| `00:02:26.480 - 00:02:28.949` | something else with some other threads |
| `00:02:28.959 - 00:02:30.790` | but eventually we'll come back |
| `00:02:30.800 - 00:02:31.509` | to |
| `00:02:31.519 - 00:02:33.350` | user thread x |
| `00:02:33.360 - 00:02:36.550` | so if the trap is a timer interrupt we |
| `00:02:36.560 - 00:02:38.869` | have the end of the time slice and at |
| `00:02:38.879 - 00:02:40.390` | some later time |
| `00:02:40.400 - 00:02:42.949` | the kernel will decide to give the |
| `00:02:42.959 - 00:02:45.270` | user thread another time slice and then |
| `00:02:45.280 - 00:02:48.390` | we'll return to the user thread |
| `00:02:48.400 - 00:02:51.589` | so uh what here's a different view of it |
| `00:02:51.599 - 00:02:53.670` | and here i'm showing the scheduler |
| `00:02:53.680 - 00:02:56.869` | thread in red on the left and we've got |
| `00:02:56.879 - 00:02:58.229` | several threads |
| `00:02:58.239 - 00:03:01.830` | okay x is executing down here and thread |
| `00:03:01.840 - 00:03:04.949` | y is executing over here |
| `00:03:04.959 - 00:03:07.270` | and from the point of view of thread x |
| `00:03:07.280 - 00:03:10.390` | you say it executes along it gets a trap |
| `00:03:10.400 - 00:03:12.390` | and then at some later time a return |
| `00:03:12.400 - 00:03:15.110` | happens and it continues executing and |
| `00:03:15.120 - 00:03:16.949` | then it traps and then at some other |
| `00:03:16.959 - 00:03:21.270` | point a return happens and it resumes |
| `00:03:21.280 - 00:03:23.830` | while the kernel is active it may choose |
| `00:03:23.840 - 00:03:27.110` | to run a time slice for thread y so the |
| `00:03:27.120 - 00:03:29.750` | system return goes back to thread y and |
| `00:03:29.760 - 00:03:33.030` | that executes until thread y gets a trap |
| `00:03:33.040 - 00:03:35.190` | so one interesting thing to note in this |
| `00:03:35.200 - 00:03:36.789` | picture the trap is followed by the |
| `00:03:36.799 - 00:03:39.190` | return sort of like a call is followed |
| `00:03:39.200 - 00:03:40.789` | by a return |
| `00:03:40.799 - 00:03:42.869` | so the user thread can imagine that it |
| `00:03:42.879 - 00:03:44.550` | is calling the kernel particularly with |
| `00:03:44.560 - 00:03:46.390` | a system call and that the kernel |
| `00:03:46.400 - 00:03:48.550` | eventually returns to it |
| `00:03:48.560 - 00:03:50.630` | here it's sort of different because the |
| `00:03:50.640 - 00:03:52.070` | return |
| `00:03:52.080 - 00:03:54.550` | and trap are sort of reversed so this |
| `00:03:54.560 - 00:03:56.949` | trap is not followed immediately by |
| `00:03:56.959 - 00:03:59.830` | return but it's followed by some other |
| `00:03:59.840 - 00:04:00.869` | stuff |
| `00:04:00.879 - 00:04:01.670` | so |
| `00:04:01.680 - 00:04:03.350` | from the scheduler's point of view we go |
| `00:04:03.360 - 00:04:06.149` | into thread x and begin its time slice |
| `00:04:06.159 - 00:04:08.949` | with the system return instruction |
| `00:04:08.959 - 00:04:11.509` | and the time slice ends with a trap |
| `00:04:11.519 - 00:04:13.750` | instruction |
| `00:04:13.760 - 00:04:15.190` | i want to look at a little bit what |
| `00:04:15.200 - 00:04:16.629` | happens um |
| `00:04:16.639 - 00:04:18.310` | when the trap occurs let's go back to |
| `00:04:18.320 - 00:04:19.990` | this diagram when the trap occurs the |
| `00:04:20.000 - 00:04:21.909` | kernel executes and then there's a |
| `00:04:21.919 - 00:04:23.670` | system return |
| `00:04:23.680 - 00:04:26.469` | this is uh this diagram is |
| `00:04:26.479 - 00:04:29.430` | what i call the road map my road map and |
| `00:04:29.440 - 00:04:31.830` | that's pretty complicated but basically |
| `00:04:31.840 - 00:04:34.870` | we have user code executing it executes |
| `00:04:34.880 - 00:04:37.510` | and there's a trap and then down at the |
| `00:04:37.520 - 00:04:38.950` | bottom |
| `00:04:38.960 - 00:04:40.469` | there's a return |
| `00:04:40.479 - 00:04:42.710` | and then the user code resumes so a lot |
| `00:04:42.720 - 00:04:44.390` | of stuff is going on here and i'll come |
| `00:04:44.400 - 00:04:45.990` | back to this in detail |
| `00:04:46.000 - 00:04:47.670` | but among other things |
| `00:04:47.680 - 00:04:50.469` | the trap happens it could be as a result |
| `00:04:50.479 - 00:04:52.550` | of a timer interrupt or a device |
| `00:04:52.560 - 00:04:55.189` | interrupt those are both asynchronous |
| `00:04:55.199 - 00:04:56.950` | the in the sense that they |
| `00:04:56.960 - 00:04:57.830` | happen |
| `00:04:57.840 - 00:05:00.629` | uh from outside the user code and the |
| `00:05:00.639 - 00:05:02.390` | user code will be unaware that they've |
| `00:05:02.400 - 00:05:03.670` | occurred |
| `00:05:03.680 - 00:05:05.590` | or it could be a system call the user |
| `00:05:05.600 - 00:05:07.510` | code might have asked for something from |
| `00:05:07.520 - 00:05:08.710` | the kernel |
| `00:05:08.720 - 00:05:10.550` | or it could be that the user code has |
| `00:05:10.560 - 00:05:13.029` | some sort of an error condition |
| `00:05:13.039 - 00:05:15.270` | so we see you know saving the registers |
| `00:05:15.280 - 00:05:17.270` | and the program counter happens really |
| `00:05:17.280 - 00:05:19.189` | early and then way down here at the |
| `00:05:19.199 - 00:05:21.909` | bottom before we do the system return we |
| `00:05:21.919 - 00:05:24.790` | see restoring user registers and then |
| `00:05:24.800 - 00:05:27.270` | the sret instruction |
| `00:05:27.280 - 00:05:30.629` | and what goes on here well we have a big |
| `00:05:30.639 - 00:05:33.110` | if statement and we |
| `00:05:33.120 - 00:05:35.270` | might have a device requesting attention |
| `00:05:35.280 - 00:05:38.629` | so here we have to run the the handler |
| `00:05:38.639 - 00:05:41.110` | for this particular device |
| `00:05:41.120 - 00:05:43.350` | maybe the user code asks for a system |
| `00:05:43.360 - 00:05:45.670` | call so we have to deal with that |
| `00:05:45.680 - 00:05:47.590` | maybe it was a timer interrupt or maybe |
| `00:05:47.600 - 00:05:49.350` | it was a program exception |
| `00:05:49.360 - 00:05:51.110` | but at least in these three cases we all |
| `00:05:51.120 - 00:05:53.990` | come down here and ultimately go back to |
| `00:05:54.000 - 00:06:00.629` | the user code |
| `00:06:00.639 - 00:06:01.909` | now |
| `00:06:01.919 - 00:06:03.270` | one thing i want to |
| `00:06:03.280 - 00:06:04.309` | talk about |
| `00:06:04.319 - 00:06:08.070` | next is the um |
| `00:06:08.080 - 00:06:10.150` | fact that we could have uh things going |
| `00:06:10.160 - 00:06:14.230` | on on a multi-core system so here i just |
| `00:06:14.240 - 00:06:17.270` | showed the scheduler uh executing thread |
| `00:06:17.280 - 00:06:20.070` | x and then thread y and then thread x |
| `00:06:20.080 - 00:06:23.029` | that's uh for a single core system |
| `00:06:23.039 - 00:06:24.230` | in this picture |
| `00:06:24.240 - 00:06:26.790` | i'm showing more or less the same thing |
| `00:06:26.800 - 00:06:28.390` | but for two cores |
| `00:06:28.400 - 00:06:29.749` | so here you see |
| `00:06:29.759 - 00:06:31.270` | the scheduler for |
| `00:06:31.280 - 00:06:33.590` | one core in blue |
| `00:06:33.600 - 00:06:34.390` | okay |
| `00:06:34.400 - 00:06:36.390` | and over here we have another core shown |
| `00:06:36.400 - 00:06:37.430` | in red |
| `00:06:37.440 - 00:06:40.070` | and here we have some processes x y z |
| `00:06:40.080 - 00:06:41.270` | and w |
| `00:06:41.280 - 00:06:43.430` | and you can see the scheduler |
| `00:06:43.440 - 00:06:45.909` | decides to give process z a time slice |
| `00:06:45.919 - 00:06:47.990` | at this point and so it does a system |
| `00:06:48.000 - 00:06:50.469` | return into process z |
| `00:06:50.479 - 00:06:52.790` | until process c finally has a trap and |
| `00:06:52.800 - 00:06:54.950` | we go back to the scheduler for this |
| `00:06:54.960 - 00:06:57.270` | core and then maybe it selects some |
| `00:06:57.280 - 00:06:59.430` | other cores and does some other stuff |
| `00:06:59.440 - 00:07:01.830` | meanwhile this core is executing along |
| `00:07:01.840 - 00:07:04.550` | and and perhaps at this point it decides |
| `00:07:04.560 - 00:07:07.589` | to give process c a time slice so at |
| `00:07:07.599 - 00:07:10.230` | that point it does a system return into |
| `00:07:10.240 - 00:07:13.270` | process z and process z gets another |
| `00:07:13.280 - 00:07:15.189` | time slice |
| `00:07:15.199 - 00:07:17.350` | from the point of view of process z we |
| `00:07:17.360 - 00:07:18.230` | see that |
| `00:07:18.240 - 00:07:20.710` | it got a time slice on this core |
| `00:07:20.720 - 00:07:22.710` | and then it had to wait for a while and |
| `00:07:22.720 - 00:07:24.710` | then it got a time slice on the other |
| `00:07:24.720 - 00:07:25.909` | core |
| `00:07:25.919 - 00:07:27.909` | there could be additional cores as well |
| `00:07:27.919 - 00:07:28.950` | so |
| `00:07:28.960 - 00:07:31.990` | the time slices for any process can |
| `00:07:32.000 - 00:07:35.430` | happen on any core it's just a matter of |
| `00:07:35.440 - 00:07:37.430` | well more or less chance in the case of |
| `00:07:37.440 - 00:07:40.070` | xv6 |
| `00:07:40.080 - 00:07:42.790` | so what happens at this trap this is the |
| `00:07:42.800 - 00:07:45.670` | trap where we go back into the kernel |
| `00:07:45.680 - 00:07:47.350` | and at this point |
| `00:07:47.360 - 00:07:49.510` | the registers for process z will be |
| `00:07:49.520 - 00:07:51.749` | saved at this context switch |
| `00:07:51.759 - 00:07:54.070` | all of z's |
| `00:07:54.080 - 00:07:56.710` | state on the core on the core is saved |
| `00:07:56.720 - 00:07:58.469` | namely its general purpose registers and |
| `00:07:58.479 - 00:08:00.390` | its program counter |
| `00:08:00.400 - 00:08:02.790` | and then at some later time core this |
| `00:08:02.800 - 00:08:05.670` | red core over here decides to give z a |
| `00:08:05.680 - 00:08:08.309` | time slice so it does another context |
| `00:08:08.319 - 00:08:11.670` | switch into process z |
| `00:08:11.680 - 00:08:14.629` | and it will at that point load registers |
| `00:08:14.639 - 00:08:17.430` | into the core zero |
| `00:08:17.440 - 00:08:21.270` | that will the registers that process z |
| `00:08:21.280 - 00:08:23.430` | needs to execute |
| `00:08:23.440 - 00:08:25.670` | now at this point |
| `00:08:25.680 - 00:08:28.309` | when the state of process z is saved it |
| `00:08:28.319 - 00:08:30.070` | will be saved into |
| `00:08:30.080 - 00:08:32.070` | memory so it's shared memory shared |
| `00:08:32.080 - 00:08:34.389` | amongst all the cores and at this |
| `00:08:34.399 - 00:08:37.350` | context switch that shared memory is |
| `00:08:37.360 - 00:08:39.750` | accessed and the state is loaded from |
| `00:08:39.760 - 00:08:41.750` | the shared memory back into the |
| `00:08:41.760 - 00:08:44.949` | registers of a core |
| `00:08:44.959 - 00:08:47.509` | that shared memory where we are storing |
| `00:08:47.519 - 00:08:49.990` | the state of process c is of course |
| `00:08:50.000 - 00:08:52.630` | critical and needs to be protected with |
| `00:08:52.640 - 00:08:55.750` | locks and will be protected with locks |
| `00:08:55.760 - 00:08:57.269` | so |
| `00:08:57.279 - 00:08:59.910` | while that state is being saved |
| `00:08:59.920 - 00:09:01.750` | at this contact switch |
| `00:09:01.760 - 00:09:04.550` | a lock will be held and only after that |
| `00:09:04.560 - 00:09:05.829` | it's done |
| `00:09:05.839 - 00:09:08.710` | is that lock released and freed up |
| `00:09:08.720 - 00:09:10.949` | and so that prevents core |
| `00:09:10.959 - 00:09:13.350` | this red core over here from trying to |
| `00:09:13.360 - 00:09:15.110` | start the |
| `00:09:15.120 - 00:09:17.030` | time slice of process c |
| `00:09:17.040 - 00:09:18.310` | too soon |
| `00:09:18.320 - 00:09:20.150` | so only after |
| `00:09:20.160 - 00:09:22.389` | the red core is able to acquire the lock |
| `00:09:22.399 - 00:09:25.430` | can it then begin to |
| `00:09:25.440 - 00:09:28.710` | load process these registers and do the |
| `00:09:28.720 - 00:09:30.710` | context switch into |
| `00:09:30.720 - 00:09:40.310` | process z |
| `00:09:40.320 - 00:09:42.870` | now i showed the uh |
| `00:09:42.880 - 00:09:44.870` | time is flowing down the page here we do |
| `00:09:44.880 - 00:09:46.550` | a trap into the kernel and then we do a |
| `00:09:46.560 - 00:09:48.790` | return back from the point of view of |
| `00:09:48.800 - 00:09:50.550` | the user's thread |
| `00:09:50.560 - 00:09:52.949` | sometimes we see it |
| `00:09:52.959 - 00:09:54.310` | drawn |
| `00:09:54.320 - 00:09:55.509` | differently |
| `00:09:55.519 - 00:09:58.070` | here in this diagram up here time is |
| `00:09:58.080 - 00:10:00.630` | flowing to the right so we see the trap |
| `00:10:00.640 - 00:10:03.829` | occurring from user mode into kernel |
| `00:10:03.839 - 00:10:06.389` | mode and then at the system return we |
| `00:10:06.399 - 00:10:08.870` | have a context switch from kernel mode |
| `00:10:08.880 - 00:10:11.829` | back into user mode |
| `00:10:11.839 - 00:10:15.750` | if it's a simple uh operation then we |
| `00:10:15.760 - 00:10:18.150` | sort of have this picture for example if |
| `00:10:18.160 - 00:10:20.949` | it's a device it's requesting service |
| `00:10:20.959 - 00:10:23.190` | the trap is a an interrupt |
| `00:10:23.200 - 00:10:25.509` | we're able to handle the |
| `00:10:25.519 - 00:10:28.790` | device and deal with the uh device |
| `00:10:28.800 - 00:10:30.389` | without doing any further contact |
| `00:10:30.399 - 00:10:32.150` | switches and then we go straight back |
| `00:10:32.160 - 00:10:35.269` | into the interrupted user mode |
| `00:10:35.279 - 00:10:37.430` | also in the case of some system calls it |
| `00:10:37.440 - 00:10:39.110` | may be that we can |
| `00:10:39.120 - 00:10:41.030` | service the system call |
| `00:10:41.040 - 00:10:42.550` | without doing any further context |
| `00:10:42.560 - 00:10:44.550` | switches and go straight back to the |
| `00:10:44.560 - 00:10:47.030` | user mode code and that's pretty |
| `00:10:47.040 - 00:10:50.230` | efficient but in other situations |
| `00:10:50.240 - 00:10:51.030` | we |
| `00:10:51.040 - 00:10:55.269` | can't so here i've drawn a slightly more |
| `00:10:55.279 - 00:10:57.750` | a different situation |
| `00:10:57.760 - 00:11:01.110` | here we are executing along in user mode |
| `00:11:01.120 - 00:11:01.990` | and |
| `00:11:02.000 - 00:11:04.710` | we do our trap and now we're executing |
| `00:11:04.720 - 00:11:06.949` | in the in the kernel |
| `00:11:06.959 - 00:11:08.949` | but it in some sense it's the same |
| `00:11:08.959 - 00:11:10.150` | process |
| `00:11:10.160 - 00:11:11.990` | okay it's not we're not really in the |
| `00:11:12.000 - 00:11:14.630` | scheduler thread yet the scheduler |
| `00:11:14.640 - 00:11:16.470` | thread is indicated in brown as a |
| `00:11:16.480 - 00:11:17.509` | separate |
| `00:11:17.519 - 00:11:19.190` | thread down here |
| `00:11:19.200 - 00:11:22.470` | so here we're in the |
| `00:11:22.480 - 00:11:25.910` | thread the kernel thread for |
| `00:11:25.920 - 00:11:29.030` | this user process and if we decide that |
| `00:11:29.040 - 00:11:30.710` | we want to schedule |
| `00:11:30.720 - 00:11:33.430` | another thread then we we will |
| `00:11:33.440 - 00:11:36.150` | do a second context switch |
| `00:11:36.160 - 00:11:38.389` | so we will then go into this scheduler |
| `00:11:38.399 - 00:11:42.230` | thread select another another process |
| `00:11:42.240 - 00:11:45.990` | and then go to that processes kernel |
| `00:11:46.000 - 00:11:46.949` | thread |
| `00:11:46.959 - 00:11:48.630` | so each process |
| `00:11:48.640 - 00:11:51.990` | has a user portion and a kernel portion |
| `00:11:52.000 - 00:11:53.509` | i'm showing |
| `00:11:53.519 - 00:11:55.430` | one thread in red here |
| `00:11:55.440 - 00:11:57.110` | okay this is the user portion that |
| `00:11:57.120 - 00:11:59.269` | executes in user mode and this is the |
| `00:11:59.279 - 00:12:01.110` | kernel portion that executes in kernel |
| `00:12:01.120 - 00:12:04.470` | mode but it's all part of one |
| `00:12:04.480 - 00:12:05.990` | thread if you will |
| `00:12:06.000 - 00:12:08.710` | and i've also shown a second thread in |
| `00:12:08.720 - 00:12:10.470` | blue here |
| `00:12:10.480 - 00:12:13.110` | so the scheduler thread is a different |
| `00:12:13.120 - 00:12:15.590` | thread and there's a context switch |
| `00:12:15.600 - 00:12:18.470` | from the process into the scheduler's |
| `00:12:18.480 - 00:12:20.870` | thread and then another context switch |
| `00:12:20.880 - 00:12:21.910` | back |
| `00:12:21.920 - 00:12:24.310` | and this is a little bit different than |
| `00:12:24.320 - 00:12:26.790` | the ones that are going on here |
| `00:12:26.800 - 00:12:29.269` | when we switch from user mode into |
| `00:12:29.279 - 00:12:31.430` | kernel mode we need to save all of the |
| `00:12:31.440 - 00:12:33.190` | general purpose registers in the program |
| `00:12:33.200 - 00:12:35.829` | counter we need to save everything |
| `00:12:35.839 - 00:12:39.509` | okay down here when we're switching |
| `00:12:39.519 - 00:12:41.910` | to the scheduler thread we're already in |
| `00:12:41.920 - 00:12:43.110` | the kernel and we can make some |
| `00:12:43.120 - 00:12:45.030` | assumptions we don't need to save quite |
| `00:12:45.040 - 00:12:46.949` | all of the registers and it's a little |
| `00:12:46.959 - 00:12:49.110` | bit different |
| `00:12:49.120 - 00:12:51.030` | this context switch |
| `00:12:51.040 - 00:12:53.269` | and this contact switch are both handled |
| `00:12:53.279 - 00:12:55.829` | in a an assembly language function |
| `00:12:55.839 - 00:12:56.670` | called |
| `00:12:56.680 - 00:13:00.949` | swtch or switch i guess we can say |
| `00:13:00.959 - 00:13:02.550` | that's assembly code |
| `00:13:02.560 - 00:13:05.030` | it's called in these two functions when |
| `00:13:05.040 - 00:13:07.509` | the scheduler chooses which uh process |
| `00:13:07.519 - 00:13:10.310` | to go to it will cause it that's done in |
| `00:13:10.320 - 00:13:12.310` | this function called scheduler and it |
| `00:13:12.320 - 00:13:15.910` | will call switch to to to make that |
| `00:13:15.920 - 00:13:17.590` | context switch |
| `00:13:17.600 - 00:13:19.110` | there's another function which i guess |
| `00:13:19.120 - 00:13:21.470` | we can call skedge |
| `00:13:21.480 - 00:13:23.030` | s-c-h-e-d |
| `00:13:23.040 - 00:13:25.750` | and that is invoked by |
| `00:13:25.760 - 00:13:28.710` | the kernel portion of a processes thread |
| `00:13:28.720 - 00:13:30.710` | to go into the scheduler |
| `00:13:30.720 - 00:13:34.550` | so it too will call this switch function |
| `00:13:34.560 - 00:13:36.310` | so the switch function |
| `00:13:36.320 - 00:13:38.710` | basically will save registers from one |
| `00:13:38.720 - 00:13:41.110` | thread and load the registers for the |
| `00:13:41.120 - 00:13:43.110` | next thread |
| `00:13:43.120 - 00:13:44.550` | and as i said it's written in assembly |
| `00:13:44.560 - 00:13:45.590` | code |
| `00:13:45.600 - 00:13:47.750` | okay that's it for context switching and |
| `00:13:47.760 - 00:13:49.430` | the trap and system |
| `00:13:49.440 - 00:13:52.710` | returns we'll come back to the |
| `00:13:52.720 - 00:13:56.150` | gigantic road map later |
| `00:13:56.160 - 00:13:59.800` | in future videos |
