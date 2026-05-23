# xv6 Kernel-25: Sleeplocks

| Field | Value |
| --- | --- |
| Video ID | `nc03pTD53Ro` |
| URL | <https://www.youtube.com/watch?v=nc03pTD53Ro> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.799 - 00:00:02.550` | in this video i'm going to describe |
| `00:00:02.560 - 00:00:05.030` | sleep locks and contrast them with spin |
| `00:00:05.040 - 00:00:05.990` | locks |
| `00:00:06.000 - 00:00:07.430` | this video is part of a series on the |
| `00:00:07.440 - 00:00:11.270` | xv6 operating system kernel |
| `00:00:11.280 - 00:00:13.669` | with any locking system there are two |
| `00:00:13.679 - 00:00:16.390` | primary functions acquire and release |
| `00:00:16.400 - 00:00:18.070` | sometimes these are called lock and |
| `00:00:18.080 - 00:00:21.189` | unlock but for spin locks the |
| `00:00:21.199 - 00:00:23.429` | functions are called acquire and release |
| `00:00:23.439 - 00:00:25.349` | and they're each passed a pointer to a |
| `00:00:25.359 - 00:00:28.390` | structure representing the spin lock |
| `00:00:28.400 - 00:00:30.550` | the key with the spin lock is that |
| `00:00:30.560 - 00:00:33.190` | it must not be held for very long and in |
| `00:00:33.200 - 00:00:36.229` | particular between the acquire |
| `00:00:36.239 - 00:00:38.229` | function and executing the release |
| `00:00:38.239 - 00:00:39.350` | function |
| `00:00:39.360 - 00:00:41.590` | you are not allowed to go to sleep |
| `00:00:41.600 - 00:00:45.110` | so there is no time slicing and what the |
| `00:00:45.120 - 00:00:47.590` | acquire function will do is loop in a |
| `00:00:47.600 - 00:00:50.069` | tight loop looking for the lock or |
| `00:00:50.079 - 00:00:52.150` | waiting for the lock to be released and |
| `00:00:52.160 - 00:00:55.029` | this is very practical on multi-core |
| `00:00:55.039 - 00:00:57.270` | systems because the |
| `00:00:57.280 - 00:01:00.229` | lock can generally be held by a core |
| `00:01:00.239 - 00:01:03.270` | another core and it will release the |
| `00:01:03.280 - 00:01:05.509` | lock quickly because it must not hold |
| `00:01:05.519 - 00:01:07.109` | the lock for very long |
| `00:01:07.119 - 00:01:09.910` | so the busy loop or the spin |
| `00:01:09.920 - 00:01:12.390` | loop inside the acquire function |
| `00:01:12.400 - 00:01:14.230` | won't have to wait for very long before |
| `00:01:14.240 - 00:01:17.510` | it finds that the lock has been released |
| `00:01:17.520 - 00:01:19.510` | so the acquire will never wait for very |
| `00:01:19.520 - 00:01:22.149` | long and this also happens to imply that |
| `00:01:22.159 - 00:01:23.190` | the |
| `00:01:23.200 - 00:01:25.830` | release will always be performed on the |
| `00:01:25.840 - 00:01:28.390` | same core that did the corresponding |
| `00:01:28.400 - 00:01:30.069` | acquire |
| `00:01:30.079 - 00:01:32.149` | but the problem is what if you need to |
| `00:01:32.159 - 00:01:34.469` | wait for a long time between acquiring a |
| `00:01:34.479 - 00:01:37.670` | lock and releasing it and in particular |
| `00:01:37.680 - 00:01:39.990` | what if you need to execute the sleep |
| `00:01:40.000 - 00:01:42.069` | function and suspend the execution of |
| `00:01:42.079 - 00:01:44.550` | the process between the acquire and the |
| `00:01:44.560 - 00:01:47.270` | corresponding release |
| `00:01:47.280 - 00:01:49.350` | well if you were to hold the lock for a |
| `00:01:49.360 - 00:01:52.630` | very long time then with a spin lock the |
| `00:01:52.640 - 00:01:54.550` | acquire would have to |
| `00:01:54.560 - 00:01:56.950` | loop in its tight loop waiting for that |
| `00:01:56.960 - 00:01:58.149` | lock to be |
| `00:01:58.159 - 00:02:00.149` | released and that would essentially tie |
| `00:02:00.159 - 00:02:02.469` | up the core and so this is just not |
| `00:02:02.479 - 00:02:04.149` | going to work |
| `00:02:04.159 - 00:02:07.109` | instead what we have are sleep locks and |
| `00:02:07.119 - 00:02:09.510` | sleep locks are very similar to spin |
| `00:02:09.520 - 00:02:10.389` | locks |
| `00:02:10.399 - 00:02:12.150` | in that they have an acquire and release |
| `00:02:12.160 - 00:02:14.150` | function however you get to hold the |
| `00:02:14.160 - 00:02:17.270` | lock while you go to sleep |
| `00:02:17.280 - 00:02:19.510` | in the case of the xv6 implementation |
| `00:02:19.520 - 00:02:21.430` | the acquire functions called acquire |
| `00:02:21.440 - 00:02:23.350` | sleep and there's a corresponding |
| `00:02:23.360 - 00:02:25.830` | release sleep both of these are passed a |
| `00:02:25.840 - 00:02:28.229` | pointer to a structure that represents |
| `00:02:28.239 - 00:02:30.229` | the sleep lock |
| `00:02:30.239 - 00:02:33.270` | there's also a function called a knit |
| `00:02:33.280 - 00:02:35.670` | sleep lock which is used to initialize |
| `00:02:35.680 - 00:02:37.110` | the structure |
| `00:02:37.120 - 00:02:39.830` | each structure also carries a name field |
| `00:02:39.840 - 00:02:42.309` | which is initialized and never changed |
| `00:02:42.319 - 00:02:44.150` | and that can be used for debugging it's |
| `00:02:44.160 - 00:02:45.910` | not really relevant to the functionality |
| `00:02:45.920 - 00:02:47.110` | of it |
| `00:02:47.120 - 00:02:49.350` | and there's also a function to check to |
| `00:02:49.360 - 00:02:51.670` | see whether the current process is |
| `00:02:51.680 - 00:02:54.070` | holding a given lock |
| `00:02:54.080 - 00:02:56.150` | and that's called holding sleep and it |
| `00:02:56.160 - 00:02:58.790` | returns true if the current process is |
| `00:02:58.800 - 00:03:01.190` | holding the lock the way this is used is |
| `00:03:01.200 - 00:03:03.430` | just for error checking and if it ever |
| `00:03:03.440 - 00:03:05.589` | returns true the kernel immediately |
| `00:03:05.599 - 00:03:07.350` | panics |
| `00:03:07.360 - 00:03:09.030` | okay here's the representation of a |
| `00:03:09.040 - 00:03:10.229` | sleep lock |
| `00:03:10.239 - 00:03:13.110` | the structure contains four fields |
| `00:03:13.120 - 00:03:15.350` | and the first field is a spin lock which |
| `00:03:15.360 - 00:03:17.750` | is just used to protect the remaining |
| `00:03:17.760 - 00:03:19.270` | fields |
| `00:03:19.280 - 00:03:21.110` | as i said the name is initialized and |
| `00:03:21.120 - 00:03:22.390` | never changed so it's not really |
| `00:03:22.400 - 00:03:23.990` | protecting the name field but it's |
| `00:03:24.000 - 00:03:26.710` | protecting the key field which is this |
| `00:03:26.720 - 00:03:28.949` | boolean called locked |
| `00:03:28.959 - 00:03:32.149` | if true the sleep lock is being held and |
| `00:03:32.159 - 00:03:34.229` | if it's true then the pid will contain |
| `00:03:34.239 - 00:03:37.110` | the process id of the process that's |
| `00:03:37.120 - 00:03:38.869` | actually holding the lock |
| `00:03:38.879 - 00:03:41.430` | and if it's false the lock is free and |
| `00:03:41.440 - 00:03:42.789` | unheld |
| `00:03:42.799 - 00:03:45.270` | okay now let's take a look at the code |
| `00:03:45.280 - 00:03:47.670` | the sleeplock.h file contains the |
| `00:03:47.680 - 00:03:49.990` | structure that is used to represent a |
| `00:03:50.000 - 00:03:52.070` | sweep block and nothing more |
| `00:03:52.080 - 00:03:55.110` | that structure contains the fields lk |
| `00:03:55.120 - 00:03:57.110` | locked name and pid |
| `00:03:57.120 - 00:04:00.550` | and here we see that lk is a spin lock |
| `00:04:00.560 - 00:04:02.789` | the locked is the boolean that's true if |
| `00:04:02.799 - 00:04:04.789` | and only if the lock is held |
| `00:04:04.799 - 00:04:07.830` | and then we have the name string and |
| `00:04:07.840 - 00:04:10.470` | the pid uh the process id that is |
| `00:04:10.480 - 00:04:12.710` | currently holding the lock if any that |
| `00:04:12.720 - 00:04:14.789` | are used for debugging |
| `00:04:14.799 - 00:04:17.430` | the file sleep lock dot c |
| `00:04:17.440 - 00:04:19.590` | contains the functions |
| `00:04:19.600 - 00:04:21.110` | the initialize function is passed a |
| `00:04:21.120 - 00:04:23.110` | pointer to one of these structures as |
| `00:04:23.120 - 00:04:26.790` | well as a name string and it initializes |
| `00:04:26.800 - 00:04:28.710` | all four fields lk |
| `00:04:28.720 - 00:04:31.270` | lock name and pid |
| `00:04:31.280 - 00:04:33.590` | the lk field is a spin lock so we have |
| `00:04:33.600 - 00:04:35.350` | to call this function to initialize all |
| `00:04:35.360 - 00:04:39.430` | spin locks we give it an arbitrary name |
| `00:04:39.440 - 00:04:42.550` | we save the name field and we set the |
| `00:04:42.560 - 00:04:44.950` | locked to indicate that this sleep lock |
| `00:04:44.960 - 00:04:47.670` | is currently not held it's in the |
| `00:04:47.680 - 00:04:49.110` | unlocked state |
| `00:04:49.120 - 00:04:52.390` | and we also clear out the process id |
| `00:04:52.400 - 00:04:54.150` | the acquire function has passed a |
| `00:04:54.160 - 00:04:56.469` | pointer to a sleep lock and essentially |
| `00:04:56.479 - 00:04:57.909` | what it's going to do is it's going to |
| `00:04:57.919 - 00:05:02.230` | acquire the lk spin lock |
| `00:05:02.240 - 00:05:04.230` | set the locked field to true to indicate |
| `00:05:04.240 - 00:05:06.710` | that it's held and also save the process |
| `00:05:06.720 - 00:05:09.110` | id of the process that invoked this |
| `00:05:09.120 - 00:05:12.550` | function and then release the spin lock |
| `00:05:12.560 - 00:05:15.029` | but we also need to check the lock field |
| `00:05:15.039 - 00:05:17.029` | because it's possible this sleep lock is |
| `00:05:17.039 - 00:05:18.790` | currently being held by some other |
| `00:05:18.800 - 00:05:20.070` | process |
| `00:05:20.080 - 00:05:21.670` | if it's false we immediately skip the |
| `00:05:21.680 - 00:05:23.909` | while loop but if it's true we go to |
| `00:05:23.919 - 00:05:27.189` | sleep and then when we wake back up we |
| `00:05:27.199 - 00:05:30.070` | check the lock field again and if it's |
| `00:05:30.080 - 00:05:33.110` | unlocked this time we then can proceed |
| `00:05:33.120 - 00:05:34.950` | otherwise we keep sleeping until |
| `00:05:34.960 - 00:05:36.950` | eventually we can find that it's |
| `00:05:36.960 - 00:05:39.590` | unlocked and we can proceed |
| `00:05:39.600 - 00:05:41.909` | now remember how the sleep works it's |
| `00:05:41.919 - 00:05:44.230` | passed a channel and |
| `00:05:44.240 - 00:05:46.070` | a spin lock |
| `00:05:46.080 - 00:05:48.790` | and the spin lock must be held upon call |
| `00:05:48.800 - 00:05:51.189` | to the sleep function and what sleep |
| `00:05:51.199 - 00:05:53.110` | will do is it will |
| `00:05:53.120 - 00:05:56.469` | release the spin lock and go to sleep as |
| `00:05:56.479 - 00:05:59.110` | one atomic operation |
| `00:05:59.120 - 00:06:01.590` | and it does this so that no wake ups uh |
| `00:06:01.600 - 00:06:03.990` | will be missed uh so |
| `00:06:04.000 - 00:06:06.950` | uh it won't uh release the spin lock |
| `00:06:06.960 - 00:06:10.390` | until it's certain that it is asleep |
| `00:06:10.400 - 00:06:12.790` | uh the channel is an arbitrary |
| `00:06:12.800 - 00:06:14.950` | number uninterpreted by the sleep and |
| `00:06:14.960 - 00:06:17.029` | wake-up functions that's just used to |
| `00:06:17.039 - 00:06:19.590` | coordinate the wake up and sleep so |
| `00:06:19.600 - 00:06:21.350` | we're using the address of the sleep |
| `00:06:21.360 - 00:06:22.150` | lock |
| `00:06:22.160 - 00:06:25.350` | and when any process releases a sleep |
| `00:06:25.360 - 00:06:28.950` | lock it will notify all processes that |
| `00:06:28.960 - 00:06:31.749` | are sleeping using that same |
| `00:06:31.759 - 00:06:33.830` | channel number that is it will notify |
| `00:06:33.840 - 00:06:34.469` | all |
| `00:06:34.479 - 00:06:37.430` | processes that are waiting for that lock |
| `00:06:37.440 - 00:06:38.950` | to be released |
| `00:06:38.960 - 00:06:42.710` | and so when we are reawakened the sweep |
| `00:06:42.720 - 00:06:45.270` | function will reacquire the lock and |
| `00:06:45.280 - 00:06:48.070` | then we can check the lock field again |
| `00:06:48.080 - 00:06:50.550` | now as several processes are reawakened |
| `00:06:50.560 - 00:06:52.469` | that is if several processes are trying |
| `00:06:52.479 - 00:06:54.790` | to acquire this sleep lock only one of |
| `00:06:54.800 - 00:06:56.469` | them will be |
| `00:06:56.479 - 00:06:58.469` | given the spin lock the others will have |
| `00:06:58.479 - 00:07:00.790` | to spin waiting for it |
| `00:07:00.800 - 00:07:02.629` | but whoever gets it will then be able to |
| `00:07:02.639 - 00:07:05.589` | check the locked field and possibly |
| `00:07:05.599 - 00:07:07.749` | proceed if it's unlocked the others when |
| `00:07:07.759 - 00:07:09.589` | they get the spin lock we'll find that |
| `00:07:09.599 - 00:07:11.589` | someone else got there first |
| `00:07:11.599 - 00:07:14.390` | okay here's the release function |
| `00:07:14.400 - 00:07:16.790` | it acquires the spin lock |
| `00:07:16.800 - 00:07:19.029` | and then sets the status to unlocked |
| `00:07:19.039 - 00:07:21.589` | also clears out the process id field |
| `00:07:21.599 - 00:07:23.670` | and releases the lock but before it |
| `00:07:23.680 - 00:07:24.790` | releases it |
| `00:07:24.800 - 00:07:27.350` | it also wakes up all the other |
| `00:07:27.360 - 00:07:29.990` | processes or any other process that is |
| `00:07:30.000 - 00:07:34.309` | waiting for this particular sleep lock |
| `00:07:34.319 - 00:07:36.469` | and finally we have the holding sleep |
| `00:07:36.479 - 00:07:38.150` | function which is just used for |
| `00:07:38.160 - 00:07:40.629` | debugging this thing returns a boolean |
| `00:07:40.639 - 00:07:42.870` | and if it ever returns uh |
| `00:07:42.880 - 00:07:45.350` | false we will be calling panic but |
| `00:07:45.360 - 00:07:46.869` | basically it just takes a look at the |
| `00:07:46.879 - 00:07:49.830` | lock field and make sure that it is set |
| `00:07:49.840 - 00:07:51.670` | but before we look at the lock |
| `00:07:51.680 - 00:07:53.430` | lock field or the pid field we need to |
| `00:07:53.440 - 00:07:55.990` | obtain this sleep lock so it obtains the |
| `00:07:56.000 - 00:07:59.350` | lk sleep lock sorry we need to obtain |
| `00:07:59.360 - 00:08:01.749` | the spin lock so before it looks at |
| `00:08:01.759 - 00:08:05.830` | these two fields it obtains the lk spin |
| `00:08:05.840 - 00:08:06.710` | lock |
| `00:08:06.720 - 00:08:08.950` | and then it checks these fields and if |
| `00:08:08.960 - 00:08:10.390` | there's any uh |
| `00:08:10.400 - 00:08:13.189` | and then gets that and returns that uh |
| `00:08:13.199 - 00:08:15.110` | true if it's locked by the current |
| `00:08:15.120 - 00:08:17.029` | process and then of course it releases |
| `00:08:17.039 - 00:08:19.830` | the spin lock before returning |
| `00:08:19.840 - 00:08:21.990` | okay that's it for sleep locks see you |
| `00:08:22.000 - 00:08:25.360` | in the next video |
