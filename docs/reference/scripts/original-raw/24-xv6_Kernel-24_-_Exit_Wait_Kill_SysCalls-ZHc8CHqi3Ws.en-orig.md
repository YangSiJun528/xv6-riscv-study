# xv6 Kernel-24: Exit Wait Kill SysCalls

| Field | Value |
| --- | --- |
| Video ID | `ZHc8CHqi3Ws` |
| URL | <https://www.youtube.com/watch?v=ZHc8CHqi3Ws> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.120 - 00:00:02.710` | this video is part of a series on the |
| `00:00:02.720 - 00:00:05.510` | xv6 operating system kernel |
| `00:00:05.520 - 00:00:08.150` | in this video i will talk about the exit |
| `00:00:08.160 - 00:00:10.790` | wait and kill system calls |
| `00:00:10.800 - 00:00:12.709` | i will also describe the reparent |
| `00:00:12.719 - 00:00:15.509` | function and review sleep and wake up as |
| `00:00:15.519 - 00:00:18.710` | they relate to these system calls |
| `00:00:18.720 - 00:00:20.790` | let's begin by reviewing the exit system |
| `00:00:20.800 - 00:00:21.910` | call |
| `00:00:21.920 - 00:00:24.390` | as i am sure you remember there is no |
| `00:00:24.400 - 00:00:25.349` | return |
| `00:00:25.359 - 00:00:28.870` | exit is used to terminate a process |
| `00:00:28.880 - 00:00:31.669` | and we provide the exit status as a |
| `00:00:31.679 - 00:00:34.229` | value to the exit and that is returned |
| `00:00:34.239 - 00:00:37.270` | to some other process |
| `00:00:37.280 - 00:00:39.910` | on unix and linux the exit status is an |
| `00:00:39.920 - 00:00:42.470` | 8-bit positive number |
| `00:00:42.480 - 00:00:45.430` | and on xv6 it's a 32-bit integer but |
| `00:00:45.440 - 00:00:47.270` | that's not really relevant |
| `00:00:47.280 - 00:00:50.069` | on unix and linux the upper bits are |
| `00:00:50.079 - 00:00:51.670` | used to communicate additional |
| `00:00:51.680 - 00:00:54.069` | information to the other process |
| `00:00:54.079 - 00:00:56.229` | such as uh what signals cause the |
| `00:00:56.239 - 00:00:58.790` | process to terminate and so on |
| `00:00:58.800 - 00:01:01.750` | but in xv6 there are no signals so we |
| `00:01:01.760 - 00:01:03.750` | can just use 32 bits |
| `00:01:03.760 - 00:01:05.189` | but in both the |
| `00:01:05.199 - 00:01:07.270` | unix linux and xv6 |
| `00:01:07.280 - 00:01:09.670` | 0 is used to indicate that the process |
| `00:01:09.680 - 00:01:11.910` | terminated with success whatever that |
| `00:01:11.920 - 00:01:14.230` | might mean for the process and any other |
| `00:01:14.240 - 00:01:17.270` | positive value is used to |
| `00:01:17.280 - 00:01:18.870` | indicate |
| `00:01:18.880 - 00:01:24.070` | that there was an error in the process |
| `00:01:24.080 - 00:01:25.109` | so |
| `00:01:25.119 - 00:01:27.590` | what happens with exit well if there's a |
| `00:01:27.600 - 00:01:30.390` | parent waiting for a child to terminate |
| `00:01:30.400 - 00:01:33.109` | then that parent is first awakened and |
| `00:01:33.119 - 00:01:35.670` | then the exit status is somehow copied |
| `00:01:35.680 - 00:01:36.789` | from |
| `00:01:36.799 - 00:01:40.469` | here to the parent code and the parent |
| `00:01:40.479 - 00:01:42.870` | then will be responsible for cleaning up |
| `00:01:42.880 - 00:01:44.710` | the process |
| `00:01:44.720 - 00:01:47.030` | after a process terminates it stops |
| `00:01:47.040 - 00:01:48.789` | executing instructions |
| `00:01:48.799 - 00:01:51.270` | we need to free up its data structures |
| `00:01:51.280 - 00:01:53.350` | and in particular we need to return its |
| `00:01:53.360 - 00:01:55.270` | proc struct |
| `00:01:55.280 - 00:01:58.630` | to an unused state but we can't do that |
| `00:01:58.640 - 00:02:00.870` | in the child process itself because it's |
| `00:02:00.880 - 00:02:03.510` | no longer executing instructions so it's |
| `00:02:03.520 - 00:02:05.190` | the responsibility of the parent to |
| `00:02:05.200 - 00:02:06.830` | perform that cleanup |
| `00:02:06.840 - 00:02:09.109` | operation but if there is no parent |
| `00:02:09.119 - 00:02:11.910` | waiting then the terminating process |
| `00:02:11.920 - 00:02:14.710` | must freeze and wait |
| `00:02:14.720 - 00:02:18.070` | so at that point it will become what we |
| `00:02:18.080 - 00:02:20.390` | say is a zombie in other words it |
| `00:02:20.400 - 00:02:22.470` | stopped living it stopped executing |
| `00:02:22.480 - 00:02:25.190` | instructions but it's not yet fully dead |
| `00:02:25.200 - 00:02:27.190` | its data structures are still |
| `00:02:27.200 - 00:02:28.390` | persisting |
| `00:02:28.400 - 00:02:29.750` | because we need some place to store the |
| `00:02:29.760 - 00:02:31.589` | exit status |
| `00:02:31.599 - 00:02:33.509` | at some point in the future its parent |
| `00:02:33.519 - 00:02:36.309` | will finally issue a call to the weight |
| `00:02:36.319 - 00:02:38.869` | system call and at that point the |
| `00:02:38.879 - 00:02:41.750` | operation can be completed |
| `00:02:41.760 - 00:02:44.229` | now there could be a problem if a |
| `00:02:44.239 - 00:02:46.470` | process exits without waiting for any of |
| `00:02:46.480 - 00:02:47.750` | its children |
| `00:02:47.760 - 00:02:49.350` | and what happens then |
| `00:02:49.360 - 00:02:52.630` | well all of the children of a and |
| `00:02:52.640 - 00:02:55.030` | terminate of a terminated process |
| `00:02:55.040 - 00:02:57.509` | are moved okay we say they are |
| `00:02:57.519 - 00:02:59.030` | re-parented |
| `00:02:59.040 - 00:03:01.509` | and they're moved to the init process so |
| `00:03:01.519 - 00:03:02.869` | they're no longer children of the |
| `00:03:02.879 - 00:03:05.270` | process that's terminating but instead |
| `00:03:05.280 - 00:03:07.750` | they become children of this init |
| `00:03:07.760 - 00:03:09.430` | process |
| `00:03:09.440 - 00:03:10.869` | what does a knit do |
| `00:03:10.879 - 00:03:12.390` | well it does some initialization and |
| `00:03:12.400 - 00:03:14.229` | then it goes into an infinite loop |
| `00:03:14.239 - 00:03:17.030` | waiting for children to exit |
| `00:03:17.040 - 00:03:18.949` | and so whenever one of these i guess we |
| `00:03:18.959 - 00:03:22.229` | can call them adopted children uh issues |
| `00:03:22.239 - 00:03:24.789` | an exit system call and it will then |
| `00:03:24.799 - 00:03:27.990` | collect that exit status and that will |
| `00:03:28.000 - 00:03:29.750` | allow the uh |
| `00:03:29.760 - 00:03:32.070` | zombies to finish dying and it will |
| `00:03:32.080 - 00:03:33.030` | allow |
| `00:03:33.040 - 00:03:35.670` | some process to clean up after |
| `00:03:35.680 - 00:03:39.030` | a process has died |
| `00:03:39.040 - 00:03:40.390` | now let's look at the |
| `00:03:40.400 - 00:03:43.430` | weight system call |
| `00:03:43.440 - 00:03:45.589` | weight which is available both in xv6 |
| `00:03:45.599 - 00:03:48.710` | and all unix and linux systems |
| `00:03:48.720 - 00:03:49.910` | is |
| `00:03:49.920 - 00:03:52.390` | passed the address of some location |
| `00:03:52.400 - 00:03:53.429` | and |
| `00:03:53.439 - 00:03:56.390` | it returns the process id of some child |
| `00:03:56.400 - 00:03:58.229` | that has exited |
| `00:03:58.239 - 00:04:00.470` | okay if there are no children |
| `00:04:00.480 - 00:04:01.990` | of the parent |
| `00:04:02.000 - 00:04:03.910` | uh then it returns minus one but |
| `00:04:03.920 - 00:04:06.550` | assuming there are uh children then it |
| `00:04:06.560 - 00:04:09.270` | will wait for one to terminate if |
| `00:04:09.280 - 00:04:11.110` | waiting is required |
| `00:04:11.120 - 00:04:13.670` | and it will grab the exit status from |
| `00:04:13.680 - 00:04:14.470` | that |
| `00:04:14.480 - 00:04:17.349` | child and store it so the argument here |
| `00:04:17.359 - 00:04:19.270` | is a pointer to some place to store that |
| `00:04:19.280 - 00:04:22.069` | exit status |
| `00:04:22.079 - 00:04:23.510` | unix and linux systems have an |
| `00:04:23.520 - 00:04:25.830` | additional system call that's not |
| `00:04:25.840 - 00:04:28.950` | available in xv6 called weight pid that |
| `00:04:28.960 - 00:04:30.870` | gives you more control |
| `00:04:30.880 - 00:04:32.950` | for example you can wait for a specific |
| `00:04:32.960 - 00:04:34.550` | process to terminate |
| `00:04:34.560 - 00:04:36.870` | and also you can avoid sleeping |
| `00:04:36.880 - 00:04:38.629` | so there's a parameter that allows you |
| `00:04:38.639 - 00:04:39.510` | to |
| `00:04:39.520 - 00:04:42.230` | return immediately if there are no |
| `00:04:42.240 - 00:04:44.870` | zombie children waiting |
| `00:04:44.880 - 00:04:47.110` | okay so how does weight work what does |
| `00:04:47.120 - 00:04:48.070` | it do |
| `00:04:48.080 - 00:04:48.950` | well |
| `00:04:48.960 - 00:04:51.510` | first it looks for a child which has |
| `00:04:51.520 - 00:04:53.270` | already exited |
| `00:04:53.280 - 00:04:55.270` | in other words it looks for a zombie |
| `00:04:55.280 - 00:04:57.830` | child and if it finds one it grabs the |
| `00:04:57.840 - 00:05:01.029` | exit status from that child and then it |
| `00:05:01.039 - 00:05:03.350` | cleans up after that child |
| `00:05:03.360 - 00:05:05.749` | in particular it gets |
| `00:05:05.759 - 00:05:07.590` | rid of the proc structure calls free |
| `00:05:07.600 - 00:05:09.590` | proc which changes it back to an unused |
| `00:05:09.600 - 00:05:11.909` | proc so it can be recycled and then of |
| `00:05:11.919 - 00:05:14.070` | course it returns the process id of the |
| `00:05:14.080 - 00:05:16.070` | terminated child |
| `00:05:16.080 - 00:05:18.629` | but if there are no zombie children |
| `00:05:18.639 - 00:05:19.909` | okay then |
| `00:05:19.919 - 00:05:22.390` | the weight system call will need to |
| `00:05:22.400 - 00:05:26.070` | sleep so it calls the sleep function and |
| `00:05:26.080 - 00:05:29.270` | when some child finally terminates |
| `00:05:29.280 - 00:05:30.390` | within |
| `00:05:30.400 - 00:05:33.590` | the exit system calls for for that child |
| `00:05:33.600 - 00:05:35.670` | the wake up function will be invoked and |
| `00:05:35.680 - 00:05:37.430` | this will wake the parent back up and |
| `00:05:37.440 - 00:05:39.909` | the parent will then loop back up and |
| `00:05:39.919 - 00:05:40.950` | look for |
| `00:05:40.960 - 00:05:42.710` | the zombie child |
| `00:05:42.720 - 00:05:44.790` | if the parent is awoken for some other |
| `00:05:44.800 - 00:05:46.150` | reason |
| `00:05:46.160 - 00:05:48.070` | then there won't be a zombie child and |
| `00:05:48.080 - 00:05:50.950` | it would just go back to sleep |
| `00:05:50.960 - 00:05:52.790` | remember that the sleep and the wake up |
| `00:05:52.800 - 00:05:54.790` | functions communicate with this channel |
| `00:05:54.800 - 00:05:57.029` | which is an uninterpreted value that the |
| `00:05:57.039 - 00:05:58.469` | sleep and the wake up don't really look |
| `00:05:58.479 - 00:05:59.430` | at |
| `00:05:59.440 - 00:06:02.070` | but it's used to coordinate |
| `00:06:02.080 - 00:06:03.670` | so which |
| `00:06:03.680 - 00:06:07.029` | processes should a wake-up call |
| `00:06:07.039 - 00:06:09.430` | awaken well that's determined by |
| `00:06:09.440 - 00:06:10.950` | whichever channel is passed to the |
| `00:06:10.960 - 00:06:13.830` | wake-up call and for |
| `00:06:13.840 - 00:06:16.710` | this application we use the address of |
| `00:06:16.720 - 00:06:19.189` | the parents proc structure |
| `00:06:19.199 - 00:06:21.990` | as the code or the number if you will |
| `00:06:22.000 - 00:06:25.029` | that that is passed to the sleep so |
| `00:06:25.039 - 00:06:27.749` | this wake up will only wake up processes |
| `00:06:27.759 - 00:06:29.510` | the the parent process it won't wake up |
| `00:06:29.520 - 00:06:32.309` | any other processes |
| `00:06:32.319 - 00:06:34.230` | okay next let's see how this looks in |
| `00:06:34.240 - 00:06:35.670` | code |
| `00:06:35.680 - 00:06:37.990` | whenever the exit system call is invoked |
| `00:06:38.000 - 00:06:40.150` | the kernel will go into this function |
| `00:06:40.160 - 00:06:43.670` | sysexit which we'll just call exit to do |
| `00:06:43.680 - 00:06:44.710` | the work |
| `00:06:44.720 - 00:06:46.950` | this function exit will never return so |
| `00:06:46.960 - 00:06:48.710` | we won't get to this point |
| `00:06:48.720 - 00:06:51.430` | there's a single argument so we invoke |
| `00:06:51.440 - 00:06:53.909` | argent to retrieve |
| `00:06:53.919 - 00:06:56.950` | a register a0 from the user's registers |
| `00:06:56.960 - 00:06:59.670` | and place that in a local variable n |
| `00:06:59.680 - 00:07:01.670` | that value is then passed to the exit |
| `00:07:01.680 - 00:07:03.189` | function |
| `00:07:03.199 - 00:07:05.110` | so here's the exit function |
| `00:07:05.120 - 00:07:08.390` | and all of these functions exit kill |
| `00:07:08.400 - 00:07:11.350` | weight and re-parent are in the file |
| `00:07:11.360 - 00:07:13.909` | proc dot c |
| `00:07:13.919 - 00:07:17.189` | so exit is past the status value and |
| `00:07:17.199 - 00:07:18.710` | what does it do |
| `00:07:18.720 - 00:07:21.270` | remember that the global variable init |
| `00:07:21.280 - 00:07:24.150` | proc points to the proc structure for |
| `00:07:24.160 - 00:07:26.309` | the initial process and here we're just |
| `00:07:26.319 - 00:07:28.230` | making sure that that initial process is |
| `00:07:28.240 - 00:07:30.870` | not trying to exit |
| `00:07:30.880 - 00:07:32.870` | i haven't talked about the file system |
| `00:07:32.880 - 00:07:34.710` | yet but you can see here that we're |
| `00:07:34.720 - 00:07:37.830` | closing the open files for this process |
| `00:07:37.840 - 00:07:41.350` | we go through the open file array okay |
| `00:07:41.360 - 00:07:43.189` | and for each one that's open we are |
| `00:07:43.199 - 00:07:45.670` | closing it and clearing it out |
| `00:07:45.680 - 00:07:47.670` | we're also closing out the current |
| `00:07:47.680 - 00:07:50.230` | working directory with this code here |
| `00:07:50.240 - 00:07:53.029` | now let's get to the good stuff |
| `00:07:53.039 - 00:07:54.869` | in the rest of this function |
| `00:07:54.879 - 00:07:56.629` | we can notice that there are no if |
| `00:07:56.639 - 00:07:59.510` | statements or loops so it goes straight |
| `00:07:59.520 - 00:08:01.270` | through and among other things we can |
| `00:08:01.280 - 00:08:03.510` | see that the state of the process is |
| `00:08:03.520 - 00:08:05.670` | turned into a zombie state |
| `00:08:05.680 - 00:08:08.550` | so all terminating processes will become |
| `00:08:08.560 - 00:08:10.550` | a zombie and |
| `00:08:10.560 - 00:08:14.469` | the parent will ultimately clean up and |
| `00:08:14.479 - 00:08:15.830` | take care of |
| `00:08:15.840 - 00:08:17.749` | returning the proc structure to the |
| `00:08:17.759 - 00:08:21.350` | unused status so it could be recycled |
| `00:08:21.360 - 00:08:24.950` | we also save the status that's the |
| `00:08:24.960 - 00:08:29.589` | argument right here in a field in the |
| `00:08:29.599 - 00:08:32.149` | proc structure called x exit state or x |
| `00:08:32.159 - 00:08:33.190` | state |
| `00:08:33.200 - 00:08:35.269` | and whenever we access these fields we |
| `00:08:35.279 - 00:08:37.829` | need to be holding the lock on the proc |
| `00:08:37.839 - 00:08:40.310` | structure so we've acquired it before |
| `00:08:40.320 - 00:08:42.149` | that point |
| `00:08:42.159 - 00:08:44.310` | before this we see that we're invoking |
| `00:08:44.320 - 00:08:46.949` | re-parent so if this process has any |
| `00:08:46.959 - 00:08:49.190` | children they have to be moved they have |
| `00:08:49.200 - 00:08:51.990` | to be orphaned if you will and adopted |
| `00:08:52.000 - 00:08:54.870` | by the initial process so that happens |
| `00:08:54.880 - 00:08:57.269` | in the re-parent function |
| `00:08:57.279 - 00:08:59.670` | and then we might have a parent that is |
| `00:08:59.680 - 00:09:01.110` | waiting for us |
| `00:09:01.120 - 00:09:03.030` | maybe there's a parent sleeping and |
| `00:09:03.040 - 00:09:04.790` | maybe not but if there is one sleeping |
| `00:09:04.800 - 00:09:06.550` | we need to wake it up |
| `00:09:06.560 - 00:09:08.949` | okay so here we are calling wake up and |
| `00:09:08.959 - 00:09:10.790` | as the channel we're passing a pointer |
| `00:09:10.800 - 00:09:14.230` | to our parents prop structure |
| `00:09:14.240 - 00:09:18.070` | so before we access the parent field we |
| `00:09:18.080 - 00:09:19.910` | need to be holding this lock called |
| `00:09:19.920 - 00:09:22.230` | weight lock and so we've acquired it |
| `00:09:22.240 - 00:09:23.910` | here |
| `00:09:23.920 - 00:09:26.310` | the reparent function will also be |
| `00:09:26.320 - 00:09:28.949` | accessing the parent field and the |
| `00:09:28.959 - 00:09:31.430` | re-parent function requires us to |
| `00:09:31.440 - 00:09:33.030` | already be holding weight lock when it's |
| `00:09:33.040 - 00:09:35.590` | called so we acquire the weight lock |
| `00:09:35.600 - 00:09:38.949` | here and then call re-parent and wake up |
| `00:09:38.959 - 00:09:40.630` | the parent process |
| `00:09:40.640 - 00:09:43.750` | the parent process might get awoken at |
| `00:09:43.760 - 00:09:45.829` | this point but the first thing it does |
| `00:09:45.839 - 00:09:48.630` | we'll see is acquire the weight lock or |
| `00:09:48.640 - 00:09:50.550` | try to acquire the weight lock so it |
| `00:09:50.560 - 00:09:52.070` | won't really make any progress until |
| `00:09:52.080 - 00:09:54.790` | after we release the weight lock so |
| `00:09:54.800 - 00:09:56.389` | we don't release it until after we've |
| `00:09:56.399 - 00:09:58.790` | become a zombie so that the parent |
| `00:09:58.800 - 00:10:00.949` | process when it does wake up will find |
| `00:10:00.959 - 00:10:03.430` | us being a zombie find this process |
| `00:10:03.440 - 00:10:05.990` | as having a state of zombie and the |
| `00:10:06.000 - 00:10:08.710` | status will already have been set |
| `00:10:08.720 - 00:10:11.030` | and so finally we jump into scad and |
| `00:10:11.040 - 00:10:13.590` | skid is the scheduler and we set our |
| `00:10:13.600 - 00:10:16.389` | status to zombie first and the scheduler |
| `00:10:16.399 - 00:10:18.790` | will never schedule anything unless it's |
| `00:10:18.800 - 00:10:21.430` | runnable so this process will never be |
| `00:10:21.440 - 00:10:23.509` | scheduled again so we'll never execute |
| `00:10:23.519 - 00:10:26.150` | this last instruction here |
| `00:10:26.160 - 00:10:30.069` | in sked we are required to be holding |
| `00:10:30.079 - 00:10:32.630` | the lock on the process and no other |
| `00:10:32.640 - 00:10:35.430` | locks so that acquire is uh done right |
| `00:10:35.440 - 00:10:37.750` | here and satisfies the requirement for |
| `00:10:37.760 - 00:10:39.269` | skid |
| `00:10:39.279 - 00:10:42.230` | okay next let's take a look at uh the |
| `00:10:42.240 - 00:10:44.310` | re-parent function |
| `00:10:44.320 - 00:10:48.790` | and uh we see that the collar must hold |
| `00:10:48.800 - 00:10:50.069` | weight lock |
| `00:10:50.079 - 00:10:52.630` | and all it's going to do it's passed a |
| `00:10:52.640 - 00:10:55.670` | pointer to the uh process that is |
| `00:10:55.680 - 00:10:56.710` | exiting |
| `00:10:56.720 - 00:10:57.509` | okay |
| `00:10:57.519 - 00:10:59.750` | that we can call that the parent process |
| `00:10:59.760 - 00:11:02.230` | and it runs through the proc array |
| `00:11:02.240 - 00:11:04.870` | looking for children so basically it uh |
| `00:11:04.880 - 00:11:08.310` | for every potential child it looks at |
| `00:11:08.320 - 00:11:10.230` | its parent pointer and asks whether that |
| `00:11:10.240 - 00:11:13.190` | points to p and if so we've got a child |
| `00:11:13.200 - 00:11:16.069` | and so we modify its parent pointer to |
| `00:11:16.079 - 00:11:18.870` | point to the proc structure for the |
| `00:11:18.880 - 00:11:20.389` | initial process |
| `00:11:20.399 - 00:11:23.350` | so that's where the re-parenting occurs |
| `00:11:23.360 - 00:11:25.190` | and then since we've |
| `00:11:25.200 - 00:11:27.590` | added a new child to the knit proc |
| `00:11:27.600 - 00:11:30.470` | to the initial process we need to |
| `00:11:30.480 - 00:11:32.710` | wake that process up in case it's |
| `00:11:32.720 - 00:11:35.670` | sleeping because it will be in a tight |
| `00:11:35.680 - 00:11:38.470` | loop waiting on children to terminate so |
| `00:11:38.480 - 00:11:41.670` | that it can get rid of them so we issue |
| `00:11:41.680 - 00:11:44.949` | the wake up call for the init process in |
| `00:11:44.959 - 00:11:46.870` | case it's sleeping and that's all |
| `00:11:46.880 - 00:11:49.350` | reparent does |
| `00:11:49.360 - 00:11:50.550` | next let's take a look at the weight |
| `00:11:50.560 - 00:11:51.829` | system call |
| `00:11:51.839 - 00:11:54.550` | the kernel invokes cis weight which will |
| `00:11:54.560 - 00:11:57.990` | then call weight to do the work |
| `00:11:58.000 - 00:11:59.590` | the weight system call has a single |
| `00:11:59.600 - 00:12:02.790` | argument so here we invoke arg adder to |
| `00:12:02.800 - 00:12:06.790` | fetch the value of the user register a0 |
| `00:12:06.800 - 00:12:09.350` | and we place it in a local variable |
| `00:12:09.360 - 00:12:12.230` | previously in sys exit which is right |
| `00:12:12.240 - 00:12:15.350` | here we called argent the only |
| `00:12:15.360 - 00:12:17.750` | difference is that argent retrieves 32 |
| `00:12:17.760 - 00:12:21.670` | bits and arg adder retrieves a 64-bit |
| `00:12:21.680 - 00:12:22.710` | value |
| `00:12:22.720 - 00:12:24.550` | and that is the virtual address into |
| `00:12:24.560 - 00:12:28.069` | which we will store the exit status |
| `00:12:28.079 - 00:12:30.389` | weight will return the process id of the |
| `00:12:30.399 - 00:12:32.710` | child that is terminated and then we |
| `00:12:32.720 - 00:12:35.509` | return that to the user mode code |
| `00:12:35.519 - 00:12:38.310` | so here's the code for weight and let's |
| `00:12:38.320 - 00:12:39.750` | start by looking at the general |
| `00:12:39.760 - 00:12:40.870` | structure |
| `00:12:40.880 - 00:12:42.629` | we see that it's passed the virtual |
| `00:12:42.639 - 00:12:44.870` | address into which we store the exits |
| `00:12:44.880 - 00:12:46.150` | code |
| `00:12:46.160 - 00:12:48.069` | we've got an infinite loop here |
| `00:12:48.079 - 00:12:50.949` | okay an infinite loop and |
| `00:12:50.959 - 00:12:53.829` | it uh initializes a local variable have |
| `00:12:53.839 - 00:12:55.910` | kids to zero and then it looks through |
| `00:12:55.920 - 00:12:57.829` | all of the children for one that's a |
| `00:12:57.839 - 00:12:59.829` | zombie okay and if it finds a zombie it |
| `00:12:59.839 - 00:13:01.110` | does this stuff |
| `00:13:01.120 - 00:13:02.870` | and so um |
| `00:13:02.880 - 00:13:03.990` | here's where it looks through all the |
| `00:13:04.000 - 00:13:05.030` | children |
| `00:13:05.040 - 00:13:07.829` | and if it finds no children |
| `00:13:07.839 - 00:13:10.389` | after it finds no zombie children |
| `00:13:10.399 - 00:13:12.389` | then it asks |
| `00:13:12.399 - 00:13:15.030` | have we found any children at all okay |
| `00:13:15.040 - 00:13:17.350` | if ever we find a |
| `00:13:17.360 - 00:13:20.470` | child we set half kids to one here but |
| `00:13:20.480 - 00:13:23.030` | if we get down here and we find that we |
| `00:13:23.040 - 00:13:25.509` | have no children then we immediately |
| `00:13:25.519 - 00:13:27.910` | return minus one |
| `00:13:27.920 - 00:13:31.030` | this function at the beginning acquires |
| `00:13:31.040 - 00:13:32.230` | weight loss because it's going to be |
| `00:13:32.240 - 00:13:35.829` | looking at the parent pointers and so we |
| `00:13:35.839 - 00:13:38.550` | release a weight lock before returning |
| `00:13:38.560 - 00:13:40.310` | that minus one |
| `00:13:40.320 - 00:13:44.389` | also if ever any other nasty processes |
| `00:13:44.399 - 00:13:47.269` | have set our killed flag then this |
| `00:13:47.279 - 00:13:50.389` | process needs to terminate itself |
| `00:13:50.399 - 00:13:53.030` | and so if killed has been set again we |
| `00:13:53.040 - 00:13:56.790` | release the lock and we return minus one |
| `00:13:56.800 - 00:14:00.069` | the kernel code that invoked uh this was |
| `00:14:00.079 - 00:14:02.949` | originally called from uservac and as we |
| `00:14:02.959 - 00:14:07.350` | return through cis weight to sys call |
| `00:14:07.360 - 00:14:09.269` | to the userback function before we |
| `00:14:09.279 - 00:14:11.509` | return to user mode code we'll check the |
| `00:14:11.519 - 00:14:14.389` | killed flag and if it has been set we |
| `00:14:14.399 - 00:14:17.509` | will exit then and so we will never |
| `00:14:17.519 - 00:14:19.509` | actually return a minus one to the to |
| `00:14:19.519 - 00:14:21.750` | the process that invoked the weight |
| `00:14:21.760 - 00:14:23.110` | system call |
| `00:14:23.120 - 00:14:24.550` | the weight system call simply will not |
| `00:14:24.560 - 00:14:26.710` | return at all |
| `00:14:26.720 - 00:14:30.389` | okay so um if we have not found a zombie |
| `00:14:30.399 - 00:14:34.150` | but we do have children then we sleep |
| `00:14:34.160 - 00:14:38.710` | so here we are sleeping um on |
| `00:14:38.720 - 00:14:39.590` | uh |
| `00:14:39.600 - 00:14:41.269` | the channel and for the channel we're |
| `00:14:41.279 - 00:14:43.189` | using the |
| `00:14:43.199 - 00:14:45.030` | address of the proc structure of this |
| `00:14:45.040 - 00:14:46.949` | very process okay that was obtained |
| `00:14:46.959 - 00:14:48.949` | right up here at the very beginning we |
| `00:14:48.959 - 00:14:50.550` | grab our own |
| `00:14:50.560 - 00:14:53.590` | uh an address of our own proc structure |
| `00:14:53.600 - 00:14:56.069` | and uh then we |
| `00:14:56.079 - 00:14:58.230` | sleep using that of the channel the |
| `00:14:58.240 - 00:15:01.750` | child any child that exits will then |
| `00:15:01.760 - 00:15:04.150` | do a wake up using the address of its |
| `00:15:04.160 - 00:15:06.550` | parent that is that same address and |
| `00:15:06.560 - 00:15:08.470` | wake us back up and then we'll repeat |
| `00:15:08.480 - 00:15:10.949` | the infinite loop the for loop the outer |
| `00:15:10.959 - 00:15:14.310` | loop and again look for a zombie child |
| `00:15:14.320 - 00:15:15.829` | um |
| `00:15:15.839 - 00:15:16.790` | we're |
| `00:15:16.800 - 00:15:17.910` | passing |
| `00:15:17.920 - 00:15:20.629` | uh we're as the second argument the |
| `00:15:20.639 - 00:15:23.430` | weight lock and remember that with sleep |
| `00:15:23.440 - 00:15:26.790` | and wake up they work in such a way that |
| `00:15:26.800 - 00:15:29.990` | the lock that is passed should be held |
| `00:15:30.000 - 00:15:32.150` | and it will be released but it will be |
| `00:15:32.160 - 00:15:34.550` | released and the process will be put to |
| `00:15:34.560 - 00:15:35.430` | sleep |
| `00:15:35.440 - 00:15:38.550` | as an atomic operation so we won't miss |
| `00:15:38.560 - 00:15:40.389` | any |
| `00:15:40.399 - 00:15:42.389` | signals and the weight lock will be held |
| `00:15:42.399 - 00:15:44.310` | until we are certainly all the way |
| `00:15:44.320 - 00:15:46.310` | asleep |
| `00:15:46.320 - 00:15:48.550` | okay now let's assume that a child has |
| `00:15:48.560 - 00:15:51.990` | exited and so this process is woken up |
| `00:15:52.000 - 00:15:53.350` | from the sleep |
| `00:15:53.360 - 00:15:56.629` | the outer loop will be repeated okay |
| `00:15:56.639 - 00:15:58.710` | and what happens |
| `00:15:58.720 - 00:16:01.749` | well we initialize half kids to zero |
| `00:16:01.759 - 00:16:02.710` | again |
| `00:16:02.720 - 00:16:04.790` | and we are going to search through the |
| `00:16:04.800 - 00:16:08.310` | proc array for any children so we go |
| `00:16:08.320 - 00:16:11.110` | through the proc array and for each one |
| `00:16:11.120 - 00:16:13.110` | call it uh np |
| `00:16:13.120 - 00:16:15.430` | we check to see whether it is a child of |
| `00:16:15.440 - 00:16:17.509` | ours in other words does its parent |
| `00:16:17.519 - 00:16:20.310` | pointer point to this process |
| `00:16:20.320 - 00:16:22.710` | and if so we found a child so we'll set |
| `00:16:22.720 - 00:16:25.030` | have kids to one and then check to see |
| `00:16:25.040 - 00:16:27.590` | whether it is a zombie |
| `00:16:27.600 - 00:16:29.749` | anytime we access the state field of |
| `00:16:29.759 - 00:16:32.069` | course we need to acquire the lock so we |
| `00:16:32.079 - 00:16:34.230` | acquire the lock on np and then look at |
| `00:16:34.240 - 00:16:35.509` | the state field |
| `00:16:35.519 - 00:16:38.230` | and if we found a zombie well this is |
| `00:16:38.240 - 00:16:40.069` | the one we are going to take care of so |
| `00:16:40.079 - 00:16:42.150` | we grab its process id |
| `00:16:42.160 - 00:16:43.590` | and that's what we'll be returning down |
| `00:16:43.600 - 00:16:44.470` | here |
| `00:16:44.480 - 00:16:45.350` | and then |
| `00:16:45.360 - 00:16:48.230` | we grab its exit status here so here |
| `00:16:48.240 - 00:16:50.710` | we're grabbing its exit status |
| `00:16:50.720 - 00:16:54.150` | if adder is null we can |
| `00:16:54.160 - 00:16:58.550` | skip the copy so we check for that if we |
| `00:16:58.560 - 00:17:00.710` | actually have been provided with a |
| `00:17:00.720 - 00:17:02.710` | virtual address in which to store the |
| `00:17:02.720 - 00:17:06.069` | exit state then we will do the copy out |
| `00:17:06.079 - 00:17:07.029` | and |
| `00:17:07.039 - 00:17:08.150` | so |
| `00:17:08.160 - 00:17:09.110` | here |
| `00:17:09.120 - 00:17:11.350` | we're using the function copy out which |
| `00:17:11.360 - 00:17:12.390` | copies |
| `00:17:12.400 - 00:17:13.590` | from |
| `00:17:13.600 - 00:17:16.630` | the kernel's memory into the virtual |
| `00:17:16.640 - 00:17:19.909` | address of some process that is the |
| `00:17:19.919 - 00:17:21.990` | parents process is the one we're copying |
| `00:17:22.000 - 00:17:25.110` | into at the virtual address in this |
| `00:17:25.120 - 00:17:26.710` | variable adder |
| `00:17:26.720 - 00:17:29.110` | and so if there's any problem with that |
| `00:17:29.120 - 00:17:31.430` | we will uh release the two locks we're |
| `00:17:31.440 - 00:17:33.590` | holding the lock on the child and the |
| `00:17:33.600 - 00:17:35.750` | weight lock and return minus one but |
| `00:17:35.760 - 00:17:39.029` | assuming the copy out works okay then we |
| `00:17:39.039 - 00:17:40.710` | go ahead and |
| `00:17:40.720 - 00:17:43.190` | clean up after the child process |
| `00:17:43.200 - 00:17:47.029` | the zombie child is now ready to be laid |
| `00:17:47.039 - 00:17:48.870` | to rest we can say |
| `00:17:48.880 - 00:17:50.950` | so we take care of the final action |
| `00:17:50.960 - 00:17:52.549` | which is to free up the data structure |
| `00:17:52.559 - 00:17:54.789` | that's associated with a child we call |
| `00:17:54.799 - 00:17:56.549` | free proc to do that |
| `00:17:56.559 - 00:17:58.789` | and then we release the lock on the |
| `00:17:58.799 - 00:18:00.950` | child at this point the child proc |
| `00:18:00.960 - 00:18:03.110` | structure is now unused and can be |
| `00:18:03.120 - 00:18:05.510` | recycled and then we release the weight |
| `00:18:05.520 - 00:18:07.669` | lock which we're still holding and |
| `00:18:07.679 - 00:18:09.350` | return |
| `00:18:09.360 - 00:18:10.950` | next let's take a look at the kill |
| `00:18:10.960 - 00:18:12.310` | system call |
| `00:18:12.320 - 00:18:15.190` | first we get into the cis kill function |
| `00:18:15.200 - 00:18:18.549` | which we'll call kill to do the work |
| `00:18:18.559 - 00:18:20.230` | kill takes a single argument which is |
| `00:18:20.240 - 00:18:23.430` | the process id of the target process |
| `00:18:23.440 - 00:18:26.789` | that we want to uh terminate |
| `00:18:26.799 - 00:18:29.909` | we retrieve the value of user register |
| `00:18:29.919 - 00:18:32.630` | a0 by calling arg and |
| `00:18:32.640 - 00:18:35.110` | place that in a local variable and pass |
| `00:18:35.120 - 00:18:37.909` | that to kill |
| `00:18:37.919 - 00:18:40.070` | kill will return zero if everything's |
| `00:18:40.080 - 00:18:42.549` | okay and minus one if we've been passed |
| `00:18:42.559 - 00:18:46.390` | a process id that doesn't exist |
| `00:18:46.400 - 00:18:48.630` | okay so here's the code for |
| `00:18:48.640 - 00:18:49.510` | kill |
| `00:18:49.520 - 00:18:52.549` | it's uh pretty straightforward uh where |
| `00:18:52.559 - 00:18:54.950` | we've got a single loop here that will |
| `00:18:54.960 - 00:18:58.310` | search for a matching process id so |
| `00:18:58.320 - 00:18:59.590` | let's look at that in a little bit more |
| `00:18:59.600 - 00:19:01.350` | detail |
| `00:19:01.360 - 00:19:03.350` | here we're going through the proc array |
| `00:19:03.360 - 00:19:06.150` | one by one and for each proc |
| `00:19:06.160 - 00:19:07.990` | p for each process p |
| `00:19:08.000 - 00:19:10.230` | we are looking at its pid field to see |
| `00:19:10.240 - 00:19:13.190` | if it is the one we are searching for |
| `00:19:13.200 - 00:19:15.990` | and if so we are going to set its killed |
| `00:19:16.000 - 00:19:18.150` | field to true |
| `00:19:18.160 - 00:19:19.750` | we are also going to look at its state |
| `00:19:19.760 - 00:19:21.029` | field |
| `00:19:21.039 - 00:19:23.990` | now these three fields pid killed at |
| `00:19:24.000 - 00:19:26.470` | state are among the fields in the proc |
| `00:19:26.480 - 00:19:28.789` | structure which are protected by the |
| `00:19:28.799 - 00:19:32.070` | lock so if we are going to read them or |
| `00:19:32.080 - 00:19:34.070` | modify them then we need to first |
| `00:19:34.080 - 00:19:36.390` | acquire the lock so here we are |
| `00:19:36.400 - 00:19:38.230` | acquiring the lock on this proc |
| `00:19:38.240 - 00:19:39.350` | structure |
| `00:19:39.360 - 00:19:40.950` | and |
| `00:19:40.960 - 00:19:43.110` | then we are releasing the lock if we do |
| `00:19:43.120 - 00:19:45.510` | the if statement we release it here and |
| `00:19:45.520 - 00:19:48.310` | everything's okay we return zero |
| `00:19:48.320 - 00:19:50.950` | if we find that it doesn't match it's |
| `00:19:50.960 - 00:19:52.470` | not the right process |
| `00:19:52.480 - 00:19:54.789` | then we release the lock and |
| `00:19:54.799 - 00:19:57.270` | repeat the loop okay and if we go all |
| `00:19:57.280 - 00:19:59.110` | the way through the loop without finding |
| `00:19:59.120 - 00:20:03.190` | a matching process we return minus one |
| `00:20:03.200 - 00:20:04.470` | okay uh |
| `00:20:04.480 - 00:20:06.870` | we set the killed flag here to true and |
| `00:20:06.880 - 00:20:08.710` | then we check its uh |
| `00:20:08.720 - 00:20:09.750` | state |
| `00:20:09.760 - 00:20:12.630` | if this process happens to be sleeping |
| `00:20:12.640 - 00:20:16.070` | okay it's not running or runnable then |
| `00:20:16.080 - 00:20:19.110` | uh it is waiting on something and we |
| `00:20:19.120 - 00:20:20.470` | don't know what it's waiting on but we |
| `00:20:20.480 - 00:20:22.710` | don't really care so we need to just |
| `00:20:22.720 - 00:20:24.549` | wake that process |
| `00:20:24.559 - 00:20:26.870` | so we can wake it by changing its state |
| `00:20:26.880 - 00:20:29.430` | to runnable and now if it's sleeping |
| `00:20:29.440 - 00:20:31.830` | that means it was in the kernel thread |
| `00:20:31.840 - 00:20:33.350` | for that process |
| `00:20:33.360 - 00:20:35.510` | and so whatever's gonna |
| `00:20:35.520 - 00:20:37.029` | whatever system call it was in the |
| `00:20:37.039 - 00:20:39.590` | middle of doing or why it was sleeping |
| `00:20:39.600 - 00:20:41.830` | it's going to wake back up and finish |
| `00:20:41.840 - 00:20:45.350` | that system call okay it might be |
| `00:20:45.360 - 00:20:47.510` | you know might end up returning an error |
| `00:20:47.520 - 00:20:49.110` | or something because it just got woken |
| `00:20:49.120 - 00:20:50.870` | up arbitrarily |
| `00:20:50.880 - 00:20:53.029` | but with any process with any system |
| `00:20:53.039 - 00:20:53.909` | called |
| `00:20:53.919 - 00:20:56.549` | before we return to executing a user |
| `00:20:56.559 - 00:21:00.390` | mode we always check the killed flag so |
| `00:21:00.400 - 00:21:02.549` | whatever this process was doing whatever |
| `00:21:02.559 - 00:21:03.909` | system call it was in the middle of |
| `00:21:03.919 - 00:21:05.350` | executing |
| `00:21:05.360 - 00:21:07.590` | yeah it finished but and maybe it didn't |
| `00:21:07.600 - 00:21:10.070` | do the right thing but nonetheless uh |
| `00:21:10.080 - 00:21:13.510` | before it returns it will uh |
| `00:21:13.520 - 00:21:15.029` | not return it will see that its kill |
| `00:21:15.039 - 00:21:18.070` | flag has been set and it will then call |
| `00:21:18.080 - 00:21:21.270` | and exit so that process will never |
| `00:21:21.280 - 00:21:23.510` | return to user mode so here we wake it |
| `00:21:23.520 - 00:21:25.350` | from sleeping otherwise it might just |
| `00:21:25.360 - 00:21:27.830` | persist and stay sleeping forever |
| `00:21:27.840 - 00:21:30.070` | if the condition never arises we want to |
| `00:21:30.080 - 00:21:31.750` | kill it immediately so that's what we do |
| `00:21:31.760 - 00:21:33.190` | here |
| `00:21:33.200 - 00:21:35.110` | okay that's it for |
| `00:21:35.120 - 00:21:37.590` | these system calls i will see you in the |
| `00:21:37.600 - 00:21:40.840` | next video |
