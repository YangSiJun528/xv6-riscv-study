# xv6 Kernel-17: sleep and wakeup

| Field | Value |
| --- | --- |
| Video ID | `rUSEYGBF3PY` |
| URL | <https://www.youtube.com/watch?v=rUSEYGBF3PY> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.040 - 00:00:02.550` | this video is part of a series on the |
| `00:00:02.560 - 00:00:05.030` | xv6 operating system kernel |
| `00:00:05.040 - 00:00:06.950` | in this video i'm going to talk about |
| `00:00:06.960 - 00:00:09.910` | the sleep and wake up functions |
| `00:00:09.920 - 00:00:12.390` | i will use as an example the system |
| `00:00:12.400 - 00:00:14.789` | called sleep which is dealt with in this |
| `00:00:14.799 - 00:00:17.510` | function here |
| `00:00:17.520 - 00:00:21.269` | any process can call sleep to put itself |
| `00:00:21.279 - 00:00:23.670` | to sleep and that will in the current |
| `00:00:23.680 - 00:00:25.029` | time slice |
| `00:00:25.039 - 00:00:27.429` | and change its status from running or |
| `00:00:27.439 - 00:00:30.870` | runnable to a status of sleeping and the |
| `00:00:30.880 - 00:00:33.350` | process will then remain sleeping until |
| `00:00:33.360 - 00:00:36.389` | some other process calls wake up |
| `00:00:36.399 - 00:00:38.790` | so we have this problem though of |
| `00:00:38.800 - 00:00:43.110` | exactly which processes to wake up when |
| `00:00:43.120 - 00:00:45.029` | some process calls wake up |
| `00:00:45.039 - 00:00:45.990` | so |
| `00:00:46.000 - 00:00:47.750` | when we go to sleep we need to provide |
| `00:00:47.760 - 00:00:50.709` | some kind of information about when we |
| `00:00:50.719 - 00:00:52.709` | want to be woken up |
| `00:00:52.719 - 00:00:53.990` | and then |
| `00:00:54.000 - 00:00:55.910` | wake up should not wake up every single |
| `00:00:55.920 - 00:00:58.069` | sleeping process but it should only wake |
| `00:00:58.079 - 00:01:01.270` | up a certain subset of those processes |
| `00:01:01.280 - 00:01:03.590` | and the question is how do we identify |
| `00:01:03.600 - 00:01:05.910` | which processes to wake up |
| `00:01:05.920 - 00:01:08.630` | and with xv6 we have something called a |
| `00:01:08.640 - 00:01:09.830` | channel |
| `00:01:09.840 - 00:01:13.109` | the channel is just a simple number um |
| `00:01:13.119 - 00:01:14.630` | it could be anything really it's |
| `00:01:14.640 - 00:01:16.390` | uninterpreted by the sleep and the wake |
| `00:01:16.400 - 00:01:18.149` | up functions |
| `00:01:18.159 - 00:01:19.030` | and |
| `00:01:19.040 - 00:01:22.230` | for example it might be the number 37 so |
| `00:01:22.240 - 00:01:25.590` | a process could sleep on the number 37 |
| `00:01:25.600 - 00:01:27.830` | and then when wake up is called it's |
| `00:01:27.840 - 00:01:31.190` | past a channel that is some number |
| `00:01:31.200 - 00:01:34.390` | if it's uh waking up on 37 it will wake |
| `00:01:34.400 - 00:01:37.350` | up that process but if wake up is called |
| `00:01:37.360 - 00:01:40.469` | with some other number like uh 54 |
| `00:01:40.479 - 00:01:42.630` | it will only wake up the processes that |
| `00:01:42.640 - 00:01:45.830` | are sleeping on 54. |
| `00:01:45.840 - 00:01:48.950` | so that's how the channel works |
| `00:01:48.960 - 00:01:50.389` | there are other approaches used in |
| `00:01:50.399 - 00:01:53.030` | different systems |
| `00:01:53.040 - 00:01:54.389` | sometimes you provide |
| `00:01:54.399 - 00:01:57.190` | a list of threads here so you sleep on a |
| `00:01:57.200 - 00:01:58.389` | particular |
| `00:01:58.399 - 00:02:01.590` | set or list of threads and then wake up |
| `00:02:01.600 - 00:02:04.230` | is applied to a particular list of |
| `00:02:04.240 - 00:02:05.670` | sleeping threads and it will wake up |
| `00:02:05.680 - 00:02:09.430` | everything on that list some systems |
| `00:02:09.440 - 00:02:11.670` | also have condition variables and a |
| `00:02:11.680 - 00:02:13.190` | condition variable is really |
| `00:02:13.200 - 00:02:15.110` | fundamentally nothing more than a list |
| `00:02:15.120 - 00:02:18.150` | of sleeping threads |
| `00:02:18.160 - 00:02:22.390` | but this is how xv6 works |
| `00:02:22.400 - 00:02:24.229` | the channel is |
| `00:02:24.239 - 00:02:26.630` | a field and it's just kept in the proc |
| `00:02:26.640 - 00:02:29.270` | structure so every process has a field |
| `00:02:29.280 - 00:02:31.270` | that will store the number that it's |
| `00:02:31.280 - 00:02:35.030` | sleeping on if its status is sleeping |
| `00:02:35.040 - 00:02:37.030` | and then the way wake up works is it |
| `00:02:37.040 - 00:02:39.589` | goes through all the processes in other |
| `00:02:39.599 - 00:02:41.270` | words it iterates through the entire |
| `00:02:41.280 - 00:02:44.869` | proc array and it will wake up any |
| `00:02:44.879 - 00:02:46.790` | process that is sleeping with the |
| `00:02:46.800 - 00:02:49.670` | matching channel number |
| `00:02:49.680 - 00:02:53.509` | so next let's uh show how |
| `00:02:53.519 - 00:02:55.670` | these functions are used so i'm going to |
| `00:02:55.680 - 00:02:57.990` | look at a typical usage |
| `00:02:58.000 - 00:02:59.110` | so |
| `00:02:59.120 - 00:03:00.149` | in |
| `00:03:00.159 - 00:03:02.790` | code we would like to |
| `00:03:02.800 - 00:03:04.630` | check conditions and only proceed if the |
| `00:03:04.640 - 00:03:06.630` | condition is true otherwise we want to |
| `00:03:06.640 - 00:03:08.869` | wait for that condition to become true |
| `00:03:08.879 - 00:03:11.670` | and so here's the typical pattern we see |
| `00:03:11.680 - 00:03:13.350` | in this while loop we are checking to |
| `00:03:13.360 - 00:03:15.030` | see whether the condition is true and if |
| `00:03:15.040 - 00:03:18.309` | it's not yet true then we call sleep and |
| `00:03:18.319 - 00:03:20.390` | we're going to wait until |
| `00:03:20.400 - 00:03:22.550` | the condition is updated |
| `00:03:22.560 - 00:03:25.110` | it may be that some process will some |
| `00:03:25.120 - 00:03:27.430` | other process will update |
| `00:03:27.440 - 00:03:29.030` | variables and |
| `00:03:29.040 - 00:03:30.949` | those variables are somehow involved |
| `00:03:30.959 - 00:03:32.869` | with that condition the other process |
| `00:03:32.879 - 00:03:35.110` | may not really know what condition |
| `00:03:35.120 - 00:03:37.110` | we're waiting on but |
| `00:03:37.120 - 00:03:39.030` | we just basically wake up the other |
| `00:03:39.040 - 00:03:41.830` | process will wake up any process that's |
| `00:03:41.840 - 00:03:43.190` | waiting on those |
| `00:03:43.200 - 00:03:44.390` | variables that are involved in that |
| `00:03:44.400 - 00:03:47.589` | condition so it may wake up a little too |
| `00:03:47.599 - 00:03:51.110` | many processes uh it may wake up several |
| `00:03:51.120 - 00:03:52.550` | processes that are waiting for some |
| `00:03:52.560 - 00:03:54.390` | changes in the shared data |
| `00:03:54.400 - 00:03:55.990` | it may be that the condition is true or |
| `00:03:56.000 - 00:03:59.030` | not true it may be that one of them will |
| `00:03:59.040 - 00:04:01.830` | grab the condition and find that it's |
| `00:04:01.840 - 00:04:03.589` | true while others will |
| `00:04:03.599 - 00:04:05.190` | find that it's false and go back to |
| `00:04:05.200 - 00:04:06.309` | sleep so |
| `00:04:06.319 - 00:04:08.550` | we have to recheck the condition upon |
| `00:04:08.560 - 00:04:10.309` | waking up |
| `00:04:10.319 - 00:04:12.070` | however there's a problem with this |
| `00:04:12.080 - 00:04:14.630` | particular pattern here |
| `00:04:14.640 - 00:04:15.750` | and that is |
| `00:04:15.760 - 00:04:16.870` | we're |
| `00:04:16.880 - 00:04:18.390` | concerned about what would happen if the |
| `00:04:18.400 - 00:04:20.949` | condition might become true sometime |
| `00:04:20.959 - 00:04:23.030` | between being checked and the sleep |
| `00:04:23.040 - 00:04:24.629` | being executed |
| `00:04:24.639 - 00:04:26.950` | we've got other processes running both |
| `00:04:26.960 - 00:04:29.110` | on this core another course and it could |
| `00:04:29.120 - 00:04:31.670` | be that our time swipe ends right here |
| `00:04:31.680 - 00:04:36.150` | and other processes run and um they may |
| `00:04:36.160 - 00:04:37.830` | make the condition true |
| `00:04:37.840 - 00:04:40.870` | and furthermore they may issue a wake up |
| `00:04:40.880 - 00:04:43.270` | and it |
| `00:04:43.280 - 00:04:44.790` | we would miss it because we haven't yet |
| `00:04:44.800 - 00:04:47.189` | executed our sleep so we don't want to |
| `00:04:47.199 - 00:04:49.990` | miss any wake ups after checking this |
| `00:04:50.000 - 00:04:51.749` | condition |
| `00:04:51.759 - 00:04:53.990` | it could really be bad if there are no |
| `00:04:54.000 - 00:04:55.909` | further wake ups in which case we go to |
| `00:04:55.919 - 00:04:58.710` | sleep and never wake up |
| `00:04:58.720 - 00:04:59.590` | so |
| `00:04:59.600 - 00:05:01.990` | here's an example from the code that we |
| `00:05:02.000 - 00:05:04.550` | will look at in a second |
| `00:05:04.560 - 00:05:06.790` | the sleep system call |
| `00:05:06.800 - 00:05:09.590` | is handled in this function and here's |
| `00:05:09.600 - 00:05:12.310` | an outline of what the code does |
| `00:05:12.320 - 00:05:14.629` | first of all it grabs the argument to |
| `00:05:14.639 - 00:05:15.990` | the system call |
| `00:05:16.000 - 00:05:18.070` | which is how long we want to sleep |
| `00:05:18.080 - 00:05:20.070` | and that's measured in a unit called |
| `00:05:20.080 - 00:05:22.950` | ticks every time the timer interrupt |
| `00:05:22.960 - 00:05:25.749` | goes off it's said to be a tick |
| `00:05:25.759 - 00:05:27.990` | and there's some global variable |
| `00:05:28.000 - 00:05:30.710` | that is shared amongst all cores |
| `00:05:30.720 - 00:05:33.749` | and it contains the current |
| `00:05:33.759 - 00:05:35.909` | tick count that's the the time that |
| `00:05:35.919 - 00:05:38.390` | represents the current time it's updated |
| `00:05:38.400 - 00:05:40.790` | by core zero whenever core zero gets a |
| `00:05:40.800 - 00:05:43.270` | timer interrupt so it's a global shared |
| `00:05:43.280 - 00:05:44.870` | data |
| `00:05:44.880 - 00:05:45.830` | and |
| `00:05:45.840 - 00:05:47.430` | we're grabbing the the time that we |
| `00:05:47.440 - 00:05:50.550` | start the weight and then we here's our |
| `00:05:50.560 - 00:05:52.469` | condition we're checking to see whether |
| `00:05:52.479 - 00:05:55.189` | n ticks have gone by |
| `00:05:55.199 - 00:05:58.070` | and if not then we go to sleep and we |
| `00:05:58.080 - 00:06:01.189` | sleep until this shared variable is |
| `00:06:01.199 - 00:06:02.710` | incremented again |
| `00:06:02.720 - 00:06:04.390` | we also have a little check in here to |
| `00:06:04.400 - 00:06:06.710` | see whether the process has been killed |
| `00:06:06.720 - 00:06:09.029` | and if so we abort our weighting but |
| `00:06:09.039 - 00:06:11.350` | that's beside the point |
| `00:06:11.360 - 00:06:12.629` | so |
| `00:06:12.639 - 00:06:13.990` | ticks |
| `00:06:14.000 - 00:06:15.029` | is |
| `00:06:15.039 - 00:06:17.670` | a global shared variable and it is |
| `00:06:17.680 - 00:06:20.870` | protected or we could say guarded by |
| `00:06:20.880 - 00:06:22.629` | a lock |
| `00:06:22.639 - 00:06:25.590` | if you want to look at or modify ticks |
| `00:06:25.600 - 00:06:27.430` | you need to first acquire |
| `00:06:27.440 - 00:06:29.029` | ticks lock |
| `00:06:29.039 - 00:06:31.350` | and so we have these requirements that |
| `00:06:31.360 - 00:06:33.510` | we've got to hold ticks lock while we're |
| `00:06:33.520 - 00:06:35.029` | looking at ticks |
| `00:06:35.039 - 00:06:36.550` | so for example right here when we check |
| `00:06:36.560 - 00:06:37.990` | the condition |
| `00:06:38.000 - 00:06:39.990` | and we don't certainly don't want to |
| `00:06:40.000 - 00:06:42.469` | miss a wake up but we've also got this |
| `00:06:42.479 - 00:06:44.390` | other condition that we cannot hold |
| `00:06:44.400 - 00:06:46.230` | locks while sleeping |
| `00:06:46.240 - 00:06:48.070` | remember that spin locks must be |
| `00:06:48.080 - 00:06:50.230` | released quickly and in particular we |
| `00:06:50.240 - 00:06:52.469` | cannot hold a spin lock while we're |
| `00:06:52.479 - 00:06:53.510` | sleeping |
| `00:06:53.520 - 00:06:55.110` | so we've got |
| `00:06:55.120 - 00:06:57.430` | a bit of a problem here and the solution |
| `00:06:57.440 - 00:06:59.909` | is this |
| `00:06:59.919 - 00:07:02.230` | our code is going to look uh like this |
| `00:07:02.240 - 00:07:05.350` | we're going to uh |
| `00:07:05.360 - 00:07:06.950` | acquire the lock in this case it's the |
| `00:07:06.960 - 00:07:09.270` | tix lock whatever lock is required in |
| `00:07:09.280 - 00:07:11.189` | order to check the condition must be |
| `00:07:11.199 - 00:07:14.309` | acquired before we look at the condition |
| `00:07:14.319 - 00:07:16.870` | so we acquire the lock and then we check |
| `00:07:16.880 - 00:07:19.110` | the condition again we go to sleep and |
| `00:07:19.120 - 00:07:22.150` | then uh wake up and check it again but |
| `00:07:22.160 - 00:07:24.150` | the difference is that we are now |
| `00:07:24.160 - 00:07:26.309` | passing a pointer to the lock that is |
| `00:07:26.319 - 00:07:27.990` | being held |
| `00:07:28.000 - 00:07:30.469` | to the sleep function and what's the |
| `00:07:30.479 - 00:07:32.309` | sleep function going to do |
| `00:07:32.319 - 00:07:33.189` | well |
| `00:07:33.199 - 00:07:35.749` | it must release that lock before we go |
| `00:07:35.759 - 00:07:38.390` | to sleep but it's going to do that in an |
| `00:07:38.400 - 00:07:41.430` | atomic way so the lock will be released |
| `00:07:41.440 - 00:07:43.830` | and we will go to sleep in such a way |
| `00:07:43.840 - 00:07:46.070` | that we can be guaranteed |
| `00:07:46.080 - 00:07:48.950` | that we won't miss any wake ups and then |
| `00:07:48.960 - 00:07:51.430` | at some later point the uh |
| `00:07:51.440 - 00:07:53.670` | some other process will |
| `00:07:53.680 - 00:07:55.510` | change our status from sleeping back to |
| `00:07:55.520 - 00:07:57.510` | runnable and the scheduler will select |
| `00:07:57.520 - 00:07:58.390` | us and |
| `00:07:58.400 - 00:07:59.430` | we'll be |
| `00:07:59.440 - 00:08:01.510` | changed to running and at that point |
| `00:08:01.520 - 00:08:03.749` | this process will wake up |
| `00:08:03.759 - 00:08:05.830` | still in the sleep function |
| `00:08:05.840 - 00:08:07.589` | and the sleep function will then |
| `00:08:07.599 - 00:08:11.270` | reacquire this lock before returning so |
| `00:08:11.280 - 00:08:13.909` | once we loop back and we are holding the |
| `00:08:13.919 - 00:08:15.990` | lock when we check the condition |
| `00:08:16.000 - 00:08:16.869` | again |
| `00:08:16.879 - 00:08:18.869` | and we keep loop looping until finally |
| `00:08:18.879 - 00:08:20.869` | the condition is found to be true |
| `00:08:20.879 - 00:08:23.270` | in which case we exit the loop and then |
| `00:08:23.280 - 00:08:25.270` | we do something with our shared data and |
| `00:08:25.280 - 00:08:28.469` | finally we must release the lock so the |
| `00:08:28.479 - 00:08:30.950` | acquire and the release are paired here |
| `00:08:30.960 - 00:08:34.230` | and in a sense they're also paired here |
| `00:08:34.240 - 00:08:37.509` | okay so now let's look at the function |
| `00:08:37.519 - 00:08:39.269` | cis sleep and see what that actually |
| `00:08:39.279 - 00:08:41.670` | looks like in code |
| `00:08:41.680 - 00:08:42.949` | and |
| `00:08:42.959 - 00:08:44.949` | here you see |
| `00:08:44.959 - 00:08:47.350` | a call to this function that will |
| `00:08:47.360 - 00:08:50.230` | obtain our first argument and store it |
| `00:08:50.240 - 00:08:52.389` | in this variable in |
| `00:08:52.399 - 00:08:55.590` | and then we're acquiring ticks lock |
| `00:08:55.600 - 00:08:57.430` | here we're accessing the shared variable |
| `00:08:57.440 - 00:08:59.269` | ticks and grabbing the starting time |
| `00:08:59.279 - 00:09:01.350` | which is a local variable |
| `00:09:01.360 - 00:09:03.430` | here's the condition uh |
| `00:09:03.440 - 00:09:04.870` | that's being checked |
| `00:09:04.880 - 00:09:07.269` | and then here we see a call to sleep and |
| `00:09:07.279 - 00:09:09.110` | we're providing a pointer to the ticks |
| `00:09:09.120 - 00:09:10.310` | lock |
| `00:09:10.320 - 00:09:12.550` | and finally after the loop exits we |
| `00:09:12.560 - 00:09:15.269` | release the ticks lock and return |
| `00:09:15.279 - 00:09:17.430` | we also see a check to see whether the |
| `00:09:17.440 - 00:09:20.870` | killed flag has been set and if so we |
| `00:09:20.880 - 00:09:24.630` | immediately release the lock and return |
| `00:09:24.640 - 00:09:27.030` | and uh the code that calls us will then |
| `00:09:27.040 - 00:09:29.509` | notice that kill killed has been set and |
| `00:09:29.519 - 00:09:31.590` | terminate the process but that happens |
| `00:09:31.600 - 00:09:33.750` | elsewhere |
| `00:09:33.760 - 00:09:34.550` | um |
| `00:09:34.560 - 00:09:37.590` | so what about the channel here |
| `00:09:37.600 - 00:09:39.190` | we are using |
| `00:09:39.200 - 00:09:41.829` | the address of the shared variable here |
| `00:09:41.839 - 00:09:43.590` | as our channel so |
| `00:09:43.600 - 00:09:44.710` | when we |
| `00:09:44.720 - 00:09:48.389` | sleep we sleep and we pass a |
| `00:09:48.399 - 00:09:51.030` | the address of the tix global variable |
| `00:09:51.040 - 00:09:55.190` | and that's okay uh it is a number |
| `00:09:55.200 - 00:09:57.110` | before we look at how sleep is |
| `00:09:57.120 - 00:09:59.670` | implemented let's uh review |
| `00:09:59.680 - 00:10:01.230` | yield because they're really quite |
| `00:10:01.240 - 00:10:02.829` | similar |
| `00:10:02.839 - 00:10:05.509` | um both will |
| `00:10:05.519 - 00:10:07.829` | stop the process from running and put it |
| `00:10:07.839 - 00:10:09.750` | back on the uh you know in a dormant |
| `00:10:09.760 - 00:10:10.710` | state |
| `00:10:10.720 - 00:10:11.990` | um |
| `00:10:12.000 - 00:10:12.949` | and |
| `00:10:12.959 - 00:10:15.910` | uh so yield will change the state to |
| `00:10:15.920 - 00:10:18.550` | runnable okay but first it acquires a |
| `00:10:18.560 - 00:10:20.630` | lock on the process |
| `00:10:20.640 - 00:10:23.190` | so when we call sched |
| `00:10:23.200 - 00:10:25.590` | here we have to be holding a lock on |
| `00:10:25.600 - 00:10:28.069` | this process sched will release that |
| `00:10:28.079 - 00:10:29.910` | lock and then when the process is |
| `00:10:29.920 - 00:10:31.590` | scheduled again it will reacquire the |
| `00:10:31.600 - 00:10:32.470` | lock |
| `00:10:32.480 - 00:10:34.389` | and then we come back here and release |
| `00:10:34.399 - 00:10:37.190` | the lock and keep going but |
| `00:10:37.200 - 00:10:39.670` | that's what yield does and sleep um is |
| `00:10:39.680 - 00:10:41.910` | is really quite similar |
| `00:10:41.920 - 00:10:44.829` | so here's the code for sleep |
| `00:10:44.839 - 00:10:48.389` | so we see uh the acquire |
| `00:10:48.399 - 00:10:50.710` | of the lock |
| `00:10:50.720 - 00:10:53.269` | this acquires the processes lock and |
| `00:10:53.279 - 00:10:55.670` | here we uh change its state we don't |
| `00:10:55.680 - 00:10:57.269` | change it to runnable we change it to |
| `00:10:57.279 - 00:10:59.910` | sleeping so when the scheduler |
| `00:10:59.920 - 00:11:03.590` | encounters this process uh from now on |
| `00:11:03.600 - 00:11:04.790` | it will |
| `00:11:04.800 - 00:11:06.790` | see that it is not runnable and it will |
| `00:11:06.800 - 00:11:09.350` | ignore it so it will remain sleeping |
| `00:11:09.360 - 00:11:11.829` | until some other process changes it back |
| `00:11:11.839 - 00:11:15.269` | to runnable so we change our state |
| `00:11:15.279 - 00:11:18.949` | to sleeping we call skid here and then |
| `00:11:18.959 - 00:11:22.310` | when sched returns we release the lock |
| `00:11:22.320 - 00:11:24.389` | and return |
| `00:11:24.399 - 00:11:25.829` | however we see a couple of other things |
| `00:11:25.839 - 00:11:27.110` | going on |
| `00:11:27.120 - 00:11:29.269` | we see that we are past |
| `00:11:29.279 - 00:11:30.710` | a |
| `00:11:30.720 - 00:11:31.670` | pointer |
| `00:11:31.680 - 00:11:33.750` | to some spin lock |
| `00:11:33.760 - 00:11:37.670` | and here we are releasing that spin lock |
| `00:11:37.680 - 00:11:40.470` | so we acquire the lock to the process |
| `00:11:40.480 - 00:11:42.470` | and the order here is critical we |
| `00:11:42.480 - 00:11:44.310` | acquire the lock to the process first |
| `00:11:44.320 - 00:11:46.710` | and then we release the spin lock |
| `00:11:46.720 - 00:11:48.389` | and so we're not holding the spin lock |
| `00:11:48.399 - 00:11:49.990` | while we are sleeping |
| `00:11:50.000 - 00:11:52.629` | but then after we come back |
| `00:11:52.639 - 00:11:53.750` | we |
| `00:11:53.760 - 00:11:57.190` | reacquire the spin lock and it doesn't |
| `00:11:57.200 - 00:11:59.509` | really matter this order here is is not |
| `00:11:59.519 - 00:12:00.949` | critical |
| `00:12:00.959 - 00:12:01.990` | but |
| `00:12:02.000 - 00:12:04.389` | so we release the lock first |
| `00:12:04.399 - 00:12:07.110` | when in doubt we prefer to release locks |
| `00:12:07.120 - 00:12:09.430` | as soon as possible before we do |
| `00:12:09.440 - 00:12:13.269` | acquiring so let's see the code oh we |
| `00:12:13.279 - 00:12:15.670` | also have the channel that's passed and |
| `00:12:15.680 - 00:12:18.069` | here it's just a pointer to some address |
| `00:12:18.079 - 00:12:19.670` | and we see that we're storing the |
| `00:12:19.680 - 00:12:21.269` | channel in the |
| `00:12:21.279 - 00:12:23.990` | field of the proc structure and then |
| `00:12:24.000 - 00:12:26.069` | after we are |
| `00:12:26.079 - 00:12:29.430` | woken back up we clear that field |
| `00:12:29.440 - 00:12:31.910` | just as a matter of tidying up |
| `00:12:31.920 - 00:12:33.269` | next let's take a look at the wake up |
| `00:12:33.279 - 00:12:34.310` | function |
| `00:12:34.320 - 00:12:36.310` | the wake up function is passed a channel |
| `00:12:36.320 - 00:12:38.230` | and it goes through all the processes |
| `00:12:38.240 - 00:12:39.910` | and wakes up any and all that are |
| `00:12:39.920 - 00:12:42.470` | sleeping on that channel |
| `00:12:42.480 - 00:12:44.150` | it has a loop and it goes through the |
| `00:12:44.160 - 00:12:46.790` | proc array one by one and for each |
| `00:12:46.800 - 00:12:49.350` | process it asks is the state of that |
| `00:12:49.360 - 00:12:51.509` | process sleeping and is the channel |
| `00:12:51.519 - 00:12:53.829` | field of that process equal to the |
| `00:12:53.839 - 00:12:57.269` | channel that we are using as an argument |
| `00:12:57.279 - 00:12:58.870` | and if so it changes the state to |
| `00:12:58.880 - 00:13:00.949` | runnable so that the next time the |
| `00:13:00.959 - 00:13:02.790` | scheduler encounters that process it |
| `00:13:02.800 - 00:13:05.750` | will be given a time slice |
| `00:13:05.760 - 00:13:07.750` | the field state and channel and some |
| `00:13:07.760 - 00:13:09.990` | others are protected by the lock on the |
| `00:13:10.000 - 00:13:12.389` | process which means we need to acquire |
| `00:13:12.399 - 00:13:15.590` | the lock before we access those fields |
| `00:13:15.600 - 00:13:17.269` | and we see that |
| `00:13:17.279 - 00:13:18.949` | acquire occurring here and the |
| `00:13:18.959 - 00:13:22.710` | corresponding release right here |
| `00:13:22.720 - 00:13:24.949` | we also see this test right here which |
| `00:13:24.959 - 00:13:27.110` | is asking is the current process that |
| `00:13:27.120 - 00:13:29.590` | we're looking at equal to the current |
| `00:13:29.600 - 00:13:33.110` | process that's running this code itself |
| `00:13:33.120 - 00:13:34.710` | the comment says that this function must |
| `00:13:34.720 - 00:13:37.110` | be called without holding a lock on any |
| `00:13:37.120 - 00:13:38.150` | process |
| `00:13:38.160 - 00:13:41.030` | and so if that's true then um this test |
| `00:13:41.040 - 00:13:43.430` | is really uh superfluous |
| `00:13:43.440 - 00:13:45.590` | uh we are looking at the state of the |
| `00:13:45.600 - 00:13:47.590` | process and obviously the current |
| `00:13:47.600 - 00:13:49.110` | process the one that is running will |
| `00:13:49.120 - 00:13:52.069` | have a state of running and not sleeping |
| `00:13:52.079 - 00:13:53.509` | so it would seem that this test is |
| `00:13:53.519 - 00:13:55.509` | really unnecessary |
| `00:13:55.519 - 00:13:57.430` | it may be that this is an artifact of |
| `00:13:57.440 - 00:13:59.509` | the history of this code or it may be |
| `00:13:59.519 - 00:14:01.509` | that somewhere in the xv6 |
| `00:14:01.519 - 00:14:03.590` | code that i'm not aware of |
| `00:14:03.600 - 00:14:05.990` | this function is actually called while |
| `00:14:06.000 - 00:14:09.189` | holding a lock on the current process in |
| `00:14:09.199 - 00:14:12.230` | any case this test is here and it |
| `00:14:12.240 - 00:14:13.829` | certainly doesn't hurt anything other |
| `00:14:13.839 - 00:14:15.350` | than maybe |
| `00:14:15.360 - 00:14:18.389` | taking a little bit of extra time |
| `00:14:18.399 - 00:14:20.310` | now i want to go back to the |
| `00:14:20.320 - 00:14:21.350` | uh |
| `00:14:21.360 - 00:14:23.269` | pseudocode that we looked at for how |
| `00:14:23.279 - 00:14:26.310` | showing how sleep is used |
| `00:14:26.320 - 00:14:29.030` | uh remember that we are typically using |
| `00:14:29.040 - 00:14:31.990` | sleep in a loop where we're looking |
| `00:14:32.000 - 00:14:33.509` | for some condition |
| `00:14:33.519 - 00:14:35.590` | and this condition is |
| `00:14:35.600 - 00:14:37.110` | checked by looking at some shared |
| `00:14:37.120 - 00:14:39.829` | variables and we acquire a lock before |
| `00:14:39.839 - 00:14:41.910` | looking at the shared variables |
| `00:14:41.920 - 00:14:44.870` | and we need to release the lock before |
| `00:14:44.880 - 00:14:47.350` | we go to sleep before we actually begin |
| `00:14:47.360 - 00:14:48.949` | the sleeping state |
| `00:14:48.959 - 00:14:51.350` | because these are spin locks and we |
| `00:14:51.360 - 00:14:53.990` | cannot uh allow them to be held while we |
| `00:14:54.000 - 00:14:56.389` | are sleeping we need to release them |
| `00:14:56.399 - 00:15:00.230` | quickly uh without any uh delays like a |
| `00:15:00.240 - 00:15:02.230` | sleeping process would have |
| `00:15:02.240 - 00:15:04.629` | so we need to release the lock before we |
| `00:15:04.639 - 00:15:06.470` | actually go to sleep and that has to |
| `00:15:06.480 - 00:15:09.350` | happen in the sleep function and it does |
| `00:15:09.360 - 00:15:11.670` | happen in the sleep function |
| `00:15:11.680 - 00:15:13.829` | and we said that this has to be atomic |
| `00:15:13.839 - 00:15:16.550` | so i wanted to just comment on |
| `00:15:16.560 - 00:15:18.389` | why it is atomic |
| `00:15:18.399 - 00:15:20.550` | in other words if a wake up comes along |
| `00:15:20.560 - 00:15:23.430` | that matches our channel here |
| `00:15:23.440 - 00:15:26.069` | it will either execute |
| `00:15:26.079 - 00:15:28.870` | before this group of instructions |
| `00:15:28.880 - 00:15:30.790` | or after this group |
| `00:15:30.800 - 00:15:33.990` | of two instructions it won't execute uh |
| `00:15:34.000 - 00:15:36.790` | in the middle so we will either |
| `00:15:36.800 - 00:15:38.629` | still be holding the lock in which case |
| `00:15:38.639 - 00:15:40.310` | we can be certain that our condition is |
| `00:15:40.320 - 00:15:42.389` | still false and so we don't need to be |
| `00:15:42.399 - 00:15:43.509` | awoken |
| `00:15:43.519 - 00:15:44.949` | or it happens |
| `00:15:44.959 - 00:15:47.749` | after here in which case we will be |
| `00:15:47.759 - 00:15:50.470` | properly awakened and then reacquire the |
| `00:15:50.480 - 00:15:53.670` | lock and go to check again the condition |
| `00:15:53.680 - 00:15:54.550` | so |
| `00:15:54.560 - 00:15:57.350` | the way that works is is that |
| `00:15:57.360 - 00:15:58.470` | the |
| `00:15:58.480 - 00:15:59.910` | within the |
| `00:15:59.920 - 00:16:01.430` | sleep function |
| `00:16:01.440 - 00:16:04.470` | recall that we are acquiring a lock on |
| `00:16:04.480 - 00:16:05.829` | the process |
| `00:16:05.839 - 00:16:08.629` | uh before we're releasing the spin lock |
| `00:16:08.639 - 00:16:09.430` | okay |
| `00:16:09.440 - 00:16:11.509` | and likewise |
| `00:16:11.519 - 00:16:14.150` | notice that in the wake up function we |
| `00:16:14.160 - 00:16:15.910` | are acquiring |
| `00:16:15.920 - 00:16:18.310` | a lock on the process before we |
| `00:16:18.320 - 00:16:20.870` | consider possibly waking it up |
| `00:16:20.880 - 00:16:25.110` | so it's that acquire that the spin lock |
| `00:16:25.120 - 00:16:27.430` | sorry this uh this lock on the process |
| `00:16:27.440 - 00:16:29.110` | can only be held by |
| `00:16:29.120 - 00:16:32.150` | uh one thread at a time so either the |
| `00:16:32.160 - 00:16:34.230` | wake up gets there first and acquires a |
| `00:16:34.240 - 00:16:37.189` | lock on the process in which case |
| `00:16:37.199 - 00:16:38.870` | the um |
| `00:16:38.880 - 00:16:41.590` | the sleep code is is still somewhere |
| `00:16:41.600 - 00:16:44.790` | above this acquire and still holding |
| `00:16:44.800 - 00:16:47.430` | the spinlock lk |
| `00:16:47.440 - 00:16:48.550` | or |
| `00:16:48.560 - 00:16:51.670` | the acquire in the um |
| `00:16:51.680 - 00:16:53.829` | sleep function gets there first and |
| `00:16:53.839 - 00:16:56.310` | acquires the lock in which case it |
| `00:16:56.320 - 00:16:58.710` | releases the um |
| `00:16:58.720 - 00:17:01.509` | spin lock lk but also sets the channel |
| `00:17:01.519 - 00:17:03.509` | and the state fields before actually |
| `00:17:03.519 - 00:17:06.150` | going to sleep and so |
| `00:17:06.160 - 00:17:07.429` | later |
| `00:17:07.439 - 00:17:10.309` | uh well this this |
| `00:17:10.319 - 00:17:12.150` | lock on the process will be immediately |
| `00:17:12.160 - 00:17:14.390` | released once we get into the sched |
| `00:17:14.400 - 00:17:15.510` | function |
| `00:17:15.520 - 00:17:17.909` | and at that point wake up can then |
| `00:17:17.919 - 00:17:19.990` | acquire the lock for |
| `00:17:20.000 - 00:17:22.789` | that process and can be sure that it |
| `00:17:22.799 - 00:17:23.829` | will be |
| `00:17:23.839 - 00:17:26.069` | found to be sleeping |
| `00:17:26.079 - 00:17:28.870` | and the channel to have been set so then |
| `00:17:28.880 - 00:17:31.110` | it will wake it up so that's how we |
| `00:17:31.120 - 00:17:34.070` | guarantee the atomicity |
| `00:17:34.080 - 00:17:36.310` | atomicity of |
| `00:17:36.320 - 00:17:39.029` | these two operations here |
| `00:17:39.039 - 00:17:40.950` | okay that's it for this video see you in |
| `00:17:40.960 - 00:17:44.280` | the next video |
