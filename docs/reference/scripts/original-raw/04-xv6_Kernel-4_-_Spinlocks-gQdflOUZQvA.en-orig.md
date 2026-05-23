# xv6 Kernel-4: Spinlocks

| Field | Value |
| --- | --- |
| Video ID | `gQdflOUZQvA` |
| URL | <https://www.youtube.com/watch?v=gQdflOUZQvA> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.680 - 00:00:03.429` | this video is part of a series on the |
| `00:00:03.439 - 00:00:06.070` | xv6 operating system kernel |
| `00:00:06.080 - 00:00:07.909` | in this video i'm going to describe spin |
| `00:00:07.919 - 00:00:09.589` | locks and tell you how they're |
| `00:00:09.599 - 00:00:12.629` | implemented in the xv6 kernel |
| `00:00:12.639 - 00:00:14.390` | first let's start off with remembering |
| `00:00:14.400 - 00:00:18.630` | what spin locks are the idea is that |
| `00:00:18.640 - 00:00:20.150` | the spin lock is represented with a |
| `00:00:20.160 - 00:00:22.630` | single word and i've called that word |
| `00:00:22.640 - 00:00:24.470` | locked here |
| `00:00:24.480 - 00:00:25.990` | and that word has |
| `00:00:26.000 - 00:00:28.830` | one of two values it's either zero or |
| `00:00:28.840 - 00:00:31.910` | one if it's zero that means the lock is |
| `00:00:31.920 - 00:00:34.150` | free or sometimes we say it's unlocked |
| `00:00:34.160 - 00:00:36.229` | or it has been released |
| `00:00:36.239 - 00:00:38.549` | and if the value is one then the lock is |
| `00:00:38.559 - 00:00:40.790` | said to be held or |
| `00:00:40.800 - 00:00:43.350` | acquired or locked |
| `00:00:43.360 - 00:00:45.110` | these are the common values that are |
| `00:00:45.120 - 00:00:47.270` | typically used and xv6 |
| `00:00:47.280 - 00:00:49.990` | does that as well |
| `00:00:50.000 - 00:00:51.750` | okay so |
| `00:00:51.760 - 00:00:55.510` | here is the structure that xv6 uses |
| `00:00:55.520 - 00:00:57.670` | for a spin lock |
| `00:00:57.680 - 00:01:01.110` | there are two other fields here name and |
| `00:01:01.120 - 00:01:03.510` | cpu which you can see are just used for |
| `00:01:03.520 - 00:01:05.910` | debugging so here's what a spin lock |
| `00:01:05.920 - 00:01:08.950` | looks like in addition to the key field |
| `00:01:08.960 - 00:01:11.750` | that tells the state of the lock we have |
| `00:01:11.760 - 00:01:14.469` | a pointer to a string which could be |
| `00:01:14.479 - 00:01:16.230` | used for debugging |
| `00:01:16.240 - 00:01:18.469` | and we have this field called cpu which |
| `00:01:18.479 - 00:01:20.710` | points to some other structure |
| `00:01:20.720 - 00:01:22.230` | every |
| `00:01:22.240 - 00:01:24.390` | core has a |
| `00:01:24.400 - 00:01:26.550` | structure associated with it called a |
| `00:01:26.560 - 00:01:29.590` | cpu structure and this field contains a |
| `00:01:29.600 - 00:01:31.190` | pointer to the |
| `00:01:31.200 - 00:01:34.069` | structure for the cpu that's currently |
| `00:01:34.079 - 00:01:36.469` | holding the lock |
| `00:01:36.479 - 00:01:38.950` | okay so the important functions on a |
| `00:01:38.960 - 00:01:42.069` | spin lock or for or any lock really are |
| `00:01:42.079 - 00:01:44.069` | acquire and release |
| `00:01:44.079 - 00:01:46.789` | in addition we have a function that is |
| `00:01:46.799 - 00:01:48.630` | uh to be called when the lock is first |
| `00:01:48.640 - 00:01:51.510` | created that initializes the lock |
| `00:01:51.520 - 00:01:53.670` | uh basically it's past |
| `00:01:53.680 - 00:01:56.069` | the name that we associate with the lock |
| `00:01:56.079 - 00:01:58.630` | once the name is set it does not change |
| `00:01:58.640 - 00:02:00.550` | we've also got a function that's used |
| `00:02:00.560 - 00:02:02.709` | for error checking to determine whether |
| `00:02:02.719 - 00:02:05.109` | the current core is holding the lock |
| `00:02:05.119 - 00:02:07.030` | each of these functions is passed a |
| `00:02:07.040 - 00:02:10.070` | pointer to one of these spin lock |
| `00:02:10.080 - 00:02:13.190` | structs so it's past a pointer like this |
| `00:02:13.200 - 00:02:15.670` | pointer here |
| `00:02:15.680 - 00:02:18.790` | okay uh to acquire the lock typically |
| `00:02:18.800 - 00:02:21.350` | what we do is we just set we want to set |
| `00:02:21.360 - 00:02:24.229` | the field to one |
| `00:02:24.239 - 00:02:25.830` | but before we do that we need to check |
| `00:02:25.840 - 00:02:27.190` | to make sure that it's not currently |
| `00:02:27.200 - 00:02:30.869` | being held so our first pass at coding |
| `00:02:30.879 - 00:02:32.630` | the acquire function might look like |
| `00:02:32.640 - 00:02:33.509` | this |
| `00:02:33.519 - 00:02:35.750` | check to see whether the lock is free |
| `00:02:35.760 - 00:02:38.390` | and if it is free then set it to one |
| `00:02:38.400 - 00:02:41.270` | and if it's not free then loop back and |
| `00:02:41.280 - 00:02:44.550` | keep trying so these are spin locks they |
| `00:02:44.560 - 00:02:46.390` | spin it's a tight loop that keeps |
| `00:02:46.400 - 00:02:49.030` | checking until it finds that the |
| `00:02:49.040 - 00:02:52.150` | field is zero it sets it to one and then |
| `00:02:52.160 - 00:02:55.030` | at that point the lock is acquired |
| `00:02:55.040 - 00:02:57.270` | well of course this has a problem if |
| `00:02:57.280 - 00:02:59.910` | there are other threads that are |
| `00:02:59.920 - 00:03:01.990` | executing concurrently in the case of |
| `00:03:02.000 - 00:03:05.430` | xc6 we've got multiple cores so this one |
| `00:03:05.440 - 00:03:07.830` | particular field in memory may be |
| `00:03:07.840 - 00:03:11.190` | accessed simultaneously by other cores |
| `00:03:11.200 - 00:03:12.869` | or perhaps if there's |
| `00:03:12.879 - 00:03:14.790` | uh thread switching going on within the |
| `00:03:14.800 - 00:03:16.949` | single core perhaps some other thread |
| `00:03:16.959 - 00:03:18.869` | within the same core is trying to access |
| `00:03:18.879 - 00:03:20.869` | the same memory location |
| `00:03:20.879 - 00:03:22.869` | and we could have both uh we could have |
| `00:03:22.879 - 00:03:25.030` | two threads simultaneously check this |
| `00:03:25.040 - 00:03:26.070` | field |
| `00:03:26.080 - 00:03:28.630` | and just happen to find that they are |
| `00:03:28.640 - 00:03:29.589` | both |
| `00:03:29.599 - 00:03:32.550` | uh that if the lock is unlocked and then |
| `00:03:32.560 - 00:03:35.030` | simultaneously both set the lock to one |
| `00:03:35.040 - 00:03:37.110` | and and so that's going to cause a |
| `00:03:37.120 - 00:03:39.830` | problem because they both now think they |
| `00:03:39.840 - 00:03:41.830` | are holding the lock |
| `00:03:41.840 - 00:03:44.309` | so we have to uh |
| `00:03:44.319 - 00:03:46.149` | find another way to do it this code will |
| `00:03:46.159 - 00:03:47.589` | not work |
| `00:03:47.599 - 00:03:49.190` | and for that |
| `00:03:49.200 - 00:03:51.190` | the risk 5 architecture has an |
| `00:03:51.200 - 00:03:54.149` | instruction called amo swap atomic |
| `00:03:54.159 - 00:03:56.630` | memory operation swap |
| `00:03:56.640 - 00:03:59.110` | and this single instruction will do two |
| `00:03:59.120 - 00:04:02.070` | things it will copy a value into |
| `00:04:02.080 - 00:04:04.309` | a word of memory and at the same time it |
| `00:04:04.319 - 00:04:06.869` | will retrieve the previous value of that |
| `00:04:06.879 - 00:04:08.949` | word and it will do it without |
| `00:04:08.959 - 00:04:10.550` | interruption so |
| `00:04:10.560 - 00:04:12.550` | it will |
| `00:04:12.560 - 00:04:14.949` | not allow any other threads or any other |
| `00:04:14.959 - 00:04:16.550` | instructions whether from this core or |
| `00:04:16.560 - 00:04:17.749` | another core |
| `00:04:17.759 - 00:04:18.550` | to |
| `00:04:18.560 - 00:04:20.390` | do anything between |
| `00:04:20.400 - 00:04:23.350` | the retrieval of the value |
| `00:04:23.360 - 00:04:26.469` | and the setting of the value |
| `00:04:26.479 - 00:04:28.390` | so here's how we're going to use that |
| `00:04:28.400 - 00:04:31.110` | atomic memory swap operation to |
| `00:04:31.120 - 00:04:34.150` | correct the problem that this code has |
| `00:04:34.160 - 00:04:36.550` | we're going to execute the atomic swap |
| `00:04:36.560 - 00:04:39.189` | operation which i'm indicating |
| `00:04:39.199 - 00:04:41.189` | schematically here we're going to write |
| `00:04:41.199 - 00:04:43.350` | a 1 into the location and we're also |
| `00:04:43.360 - 00:04:45.510` | going to retrieve the old value the |
| `00:04:45.520 - 00:04:46.950` | previous value |
| `00:04:46.960 - 00:04:49.670` | if the previous value was 0 then we're |
| `00:04:49.680 - 00:04:51.749` | done we have acquired the lock but if |
| `00:04:51.759 - 00:04:54.310` | the previous value was not 0 well it |
| `00:04:54.320 - 00:04:56.310` | must have been one because there are no |
| `00:04:56.320 - 00:04:57.510` | other options |
| `00:04:57.520 - 00:04:58.550` | then |
| `00:04:58.560 - 00:05:00.790` | somebody else was previously holding the |
| `00:05:00.800 - 00:05:02.950` | lock so we have to loop back and try |
| `00:05:02.960 - 00:05:06.310` | again so then we spin until finally we |
| `00:05:06.320 - 00:05:08.870` | find the previous value was zero |
| `00:05:08.880 - 00:05:11.350` | and we then have acquired the lock |
| `00:05:11.360 - 00:05:12.790` | the release operation is pretty |
| `00:05:12.800 - 00:05:15.189` | straightforward we just copy |
| `00:05:15.199 - 00:05:16.870` | the unlocked |
| `00:05:16.880 - 00:05:20.390` | value of zero into the word |
| `00:05:20.400 - 00:05:21.909` | uh this doesn't |
| `00:05:21.919 - 00:05:24.469` | it's hard to imagine how copying value |
| `00:05:24.479 - 00:05:26.469` | into a single location of memory can be |
| `00:05:26.479 - 00:05:29.749` | anything but atomic so in some systems |
| `00:05:29.759 - 00:05:32.469` | this is uh implemented as just a single |
| `00:05:32.479 - 00:05:36.230` | memory store operation |
| `00:05:36.240 - 00:05:39.510` | okay now let's look at the code for |
| `00:05:39.520 - 00:05:42.310` | the acquire operation |
| `00:05:42.320 - 00:05:44.310` | uh first of all we can take care of the |
| `00:05:44.320 - 00:05:45.909` | init operation this is code that's |
| `00:05:45.919 - 00:05:48.790` | coming from the spinlock.c |
| `00:05:48.800 - 00:05:51.990` | file |
| `00:05:52.000 - 00:05:53.510` | initialize the spin lock we're passed a |
| `00:05:53.520 - 00:05:55.510` | pointer to the structure |
| `00:05:55.520 - 00:05:58.230` | and the name we set the name field and |
| `00:05:58.240 - 00:06:00.070` | it never changes after that |
| `00:06:00.080 - 00:06:02.710` | we also set the cpu field to null |
| `00:06:02.720 - 00:06:04.950` | because the lock is not held and we set |
| `00:06:04.960 - 00:06:06.390` | its initial value to |
| `00:06:06.400 - 00:06:07.909` | zero |
| `00:06:07.919 - 00:06:09.590` | here is the code for the acquire |
| `00:06:09.600 - 00:06:10.629` | function |
| `00:06:10.639 - 00:06:12.870` | and here right here we see the while |
| `00:06:12.880 - 00:06:15.029` | loop that does exactly what i described |
| `00:06:15.039 - 00:06:16.710` | previously |
| `00:06:16.720 - 00:06:20.230` | this magic sync lock test and set is |
| `00:06:20.240 - 00:06:21.590` | going to be |
| `00:06:21.600 - 00:06:24.629` | turned into some assembly code and the |
| `00:06:24.639 - 00:06:27.350` | comment here is indicating that the amo |
| `00:06:27.360 - 00:06:28.950` | swap |
| `00:06:28.960 - 00:06:30.230` | instruction |
| `00:06:30.240 - 00:06:32.150` | is going to be happening and it talks |
| `00:06:32.160 - 00:06:34.150` | about some registers but essentially |
| `00:06:34.160 - 00:06:36.870` | we're passing it a pointer to the word |
| `00:06:36.880 - 00:06:39.029` | that we want to update |
| `00:06:39.039 - 00:06:40.950` | and we're passing it the new value which |
| `00:06:40.960 - 00:06:43.430` | is one and this function is going to |
| `00:06:43.440 - 00:06:45.590` | return the old value and we're checking |
| `00:06:45.600 - 00:06:48.629` | to see whether it is zero or not |
| `00:06:48.639 - 00:06:51.909` | and if it was not zero uh then we |
| `00:06:51.919 - 00:06:53.589` | repeat this while loop |
| `00:06:53.599 - 00:06:55.430` | and if it was zero then we've acquired |
| `00:06:55.440 - 00:06:57.670` | the lock and we're done |
| `00:06:57.680 - 00:06:58.790` | now there are a couple of other things i |
| `00:06:58.800 - 00:07:00.710` | want to mention about this function |
| `00:07:00.720 - 00:07:01.749` | first |
| `00:07:01.759 - 00:07:05.430` | once we acquire the lock we are storing |
| `00:07:05.440 - 00:07:07.670` | into the cpu field |
| `00:07:07.680 - 00:07:10.150` | every core has a structure associated |
| `00:07:10.160 - 00:07:11.110` | with it |
| `00:07:11.120 - 00:07:11.830` | so |
| `00:07:11.840 - 00:07:13.830` | there are in fact eight cores so there |
| `00:07:13.840 - 00:07:16.150` | are eight structures and this function |
| `00:07:16.160 - 00:07:18.629` | here will retrieve a pointer or return a |
| `00:07:18.639 - 00:07:20.950` | pointer to the structure for the core |
| `00:07:20.960 - 00:07:23.510` | that's currently executing so we're just |
| `00:07:23.520 - 00:07:25.830` | saving that here |
| `00:07:25.840 - 00:07:27.830` | up here we are |
| `00:07:27.840 - 00:07:30.710` | uh checking to see whether before we |
| `00:07:30.720 - 00:07:32.950` | acquire the lock whether we already |
| `00:07:32.960 - 00:07:35.110` | whether it's already um |
| `00:07:35.120 - 00:07:37.110` | being held by us so we'll see the |
| `00:07:37.120 - 00:07:39.189` | holding function in just a second and if |
| `00:07:39.199 - 00:07:41.670` | it is then something's drastically the |
| `00:07:41.680 - 00:07:45.589` | matter so we um cause an error message |
| `00:07:45.599 - 00:07:48.070` | then we've got this uh synchronized |
| `00:07:48.080 - 00:07:49.830` | thing going on here |
| `00:07:49.840 - 00:07:51.749` | so tell the c compiler and the processor |
| `00:07:51.759 - 00:07:53.589` | not to move loads or stores past this |
| `00:07:53.599 - 00:07:55.029` | point to ensure that the critical |
| `00:07:55.039 - 00:07:57.189` | sections memory references happen |
| `00:07:57.199 - 00:08:00.070` | strictly after the lock is acquired |
| `00:08:00.080 - 00:08:02.550` | this is a fence instruction |
| `00:08:02.560 - 00:08:05.430` | so we want to prevent the compiler for |
| `00:08:05.440 - 00:08:07.909` | doing up from doing optimizations |
| `00:08:07.919 - 00:08:10.550` | it might in fact reorder things |
| `00:08:10.560 - 00:08:14.150` | and that is not acceptable the anything |
| `00:08:14.160 - 00:08:15.990` | that's supposed to be done after we |
| `00:08:16.000 - 00:08:18.469` | acquire this lock after this while loop |
| `00:08:18.479 - 00:08:21.909` | completes must be done after that |
| `00:08:21.919 - 00:08:22.629` | so |
| `00:08:22.639 - 00:08:25.270` | that's what this sync synchronize does |
| `00:08:25.280 - 00:08:27.110` | it forces |
| `00:08:27.120 - 00:08:29.430` | the compiler to emit code and the |
| `00:08:29.440 - 00:08:30.710` | processor to |
| `00:08:30.720 - 00:08:32.310` | execute that code in such a way that |
| `00:08:32.320 - 00:08:33.750` | everything that is supposed to happen |
| `00:08:33.760 - 00:08:35.509` | before this point |
| `00:08:35.519 - 00:08:37.829` | is completed and everything that is |
| `00:08:37.839 - 00:08:40.070` | supposed to happen after this point is |
| `00:08:40.080 - 00:08:43.670` | not started until after this point |
| `00:08:43.680 - 00:08:45.190` | we see one other thing here that's kind |
| `00:08:45.200 - 00:08:47.910` | of unusual and that is push off |
| `00:08:47.920 - 00:08:49.750` | the comment says disable interrupts to |
| `00:08:49.760 - 00:08:51.190` | avoid deadlock |
| `00:08:51.200 - 00:08:54.070` | holy smoke what's that i mean uh isn't |
| `00:08:54.080 - 00:08:56.710` | this a lock situation here why why are |
| `00:08:56.720 - 00:08:58.949` | interrupts involved at all |
| `00:08:58.959 - 00:09:01.670` | i'll come back to that in a minute so |
| `00:09:01.680 - 00:09:04.710` | i'm gonna just uh leave you in suspense |
| `00:09:04.720 - 00:09:06.790` | but first uh let's take a look at the |
| `00:09:06.800 - 00:09:09.350` | release operation |
| `00:09:09.360 - 00:09:11.910` | we do a quick check to make sure that we |
| `00:09:11.920 - 00:09:15.110` | are in fact holding this lock |
| `00:09:15.120 - 00:09:17.829` | and if not we print an error message and |
| `00:09:17.839 - 00:09:20.150` | then we're going to be releasing it down |
| `00:09:20.160 - 00:09:21.190` | here |
| `00:09:21.200 - 00:09:23.110` | and so we might as well go ahead and set |
| `00:09:23.120 - 00:09:26.389` | the cpu field to null at this point |
| `00:09:26.399 - 00:09:27.670` | and |
| `00:09:27.680 - 00:09:29.750` | here we are copying |
| `00:09:29.760 - 00:09:31.829` | uh the zero into |
| `00:09:31.839 - 00:09:34.389` | this field they're using an amo swap |
| `00:09:34.399 - 00:09:36.310` | instruction to do it with this sync |
| `00:09:36.320 - 00:09:37.750` | glock release |
| `00:09:37.760 - 00:09:40.870` | but like i said it's hard to imagine how |
| `00:09:40.880 - 00:09:44.949` | a and a normal stored of memory could be |
| `00:09:44.959 - 00:09:47.269` | anything less than atomic |
| `00:09:47.279 - 00:09:49.190` | and we've got |
| `00:09:49.200 - 00:09:50.790` | pop off |
| `00:09:50.800 - 00:09:52.389` | well we've got the synchronize here up |
| `00:09:52.399 - 00:09:54.310` | here |
| `00:09:54.320 - 00:09:55.910` | which makes sure that anything that's |
| `00:09:55.920 - 00:09:57.829` | supposed to happen before we release the |
| `00:09:57.839 - 00:10:00.150` | lock actually finishes |
| `00:10:00.160 - 00:10:01.990` | and that anything that's |
| `00:10:02.000 - 00:10:04.230` | and only after we finish that do we |
| `00:10:04.240 - 00:10:08.310` | actually set the locked field to zero |
| `00:10:08.320 - 00:10:09.990` | so pop off is |
| `00:10:10.000 - 00:10:14.949` | a partner a dual of uh push off and so |
| `00:10:14.959 - 00:10:17.670` | what it does is it enables interrupts |
| `00:10:17.680 - 00:10:20.230` | and i'll come back to that in a second |
| `00:10:20.240 - 00:10:22.949` | but um first let's look at this holding |
| `00:10:22.959 - 00:10:26.870` | function just to complete this uh code |
| `00:10:26.880 - 00:10:29.190` | this just checks to see whether the |
| `00:10:29.200 - 00:10:31.910` | current core that's executing this is |
| `00:10:31.920 - 00:10:33.829` | holding the lock |
| `00:10:33.839 - 00:10:35.990` | okay it |
| `00:10:36.000 - 00:10:37.110` | looks at the |
| `00:10:37.120 - 00:10:39.990` | value of the field and if it's one and |
| `00:10:40.000 - 00:10:43.030` | the cpu is the same as this core then it |
| `00:10:43.040 - 00:10:45.350` | returns true and otherwise it returns |
| `00:10:45.360 - 00:10:49.269` | false |
| `00:10:49.279 - 00:10:51.190` | a spin lock should never be held for a |
| `00:10:51.200 - 00:10:53.269` | long period of time |
| `00:10:53.279 - 00:10:55.110` | imagine a situation where one core is |
| `00:10:55.120 - 00:10:57.190` | holding a lock and another core wants to |
| `00:10:57.200 - 00:10:58.630` | acquire that lock |
| `00:10:58.640 - 00:11:00.389` | well the acquire function is going to be |
| `00:11:00.399 - 00:11:02.790` | spinning in a tight loop waiting for the |
| `00:11:02.800 - 00:11:05.430` | lock to become free so as a general rule |
| `00:11:05.440 - 00:11:08.069` | of thumb you should always uh plan to |
| `00:11:08.079 - 00:11:10.310` | release the lock soon after it's |
| `00:11:10.320 - 00:11:11.350` | acquired |
| `00:11:11.360 - 00:11:13.350` | in particular we don't want the thread |
| `00:11:13.360 - 00:11:15.030` | that's holding the lock to go to sleep |
| `00:11:15.040 - 00:11:17.670` | or to get time sliced |
| `00:11:17.680 - 00:11:19.750` | there are other techniques for locking |
| `00:11:19.760 - 00:11:22.550` | in xv6 we have the sleep and the wake up |
| `00:11:22.560 - 00:11:24.150` | function and these can be used in |
| `00:11:24.160 - 00:11:25.829` | situations where the lock needs to be |
| `00:11:25.839 - 00:11:28.949` | held for a long period of time |
| `00:11:28.959 - 00:11:31.910` | locks are used to protect shared data |
| `00:11:31.920 - 00:11:34.949` | actually the xv6 documentation has some |
| `00:11:34.959 - 00:11:37.030` | interesting wisdom |
| `00:11:37.040 - 00:11:39.670` | they point out that locks really protect |
| `00:11:39.680 - 00:11:41.590` | constraints and i think that's a nice |
| `00:11:41.600 - 00:11:43.590` | way to put it but |
| `00:11:43.600 - 00:11:45.430` | in other in any case |
| `00:11:45.440 - 00:11:47.670` | we usually or almost always see this |
| `00:11:47.680 - 00:11:49.990` | particular pattern here we acquire the |
| `00:11:50.000 - 00:11:52.710` | lock then we access the shared data and |
| `00:11:52.720 - 00:11:55.430` | then we release the lock |
| `00:11:55.440 - 00:11:56.710` | the |
| `00:11:56.720 - 00:11:58.550` | code here is often called a critical |
| `00:11:58.560 - 00:12:01.110` | section in other words it's critical it |
| `00:12:01.120 - 00:12:03.670` | can only be executed by one thread at a |
| `00:12:03.680 - 00:12:06.629` | time so therefore this code is protected |
| `00:12:06.639 - 00:12:07.670` | by |
| `00:12:07.680 - 00:12:09.509` | a lock |
| `00:12:09.519 - 00:12:11.829` | let's look at a quick example and i'm |
| `00:12:11.839 - 00:12:13.350` | going to imagine |
| `00:12:13.360 - 00:12:15.590` | input a character input that's coming |
| `00:12:15.600 - 00:12:17.110` | from somewhere and going to someplace |
| `00:12:17.120 - 00:12:20.629` | else so perhaps we have a keyboard and |
| `00:12:20.639 - 00:12:22.069` | every time the user types on the |
| `00:12:22.079 - 00:12:23.350` | keyboard |
| `00:12:23.360 - 00:12:26.790` | an interrupt handler wakes up and adds a |
| `00:12:26.800 - 00:12:28.150` | character to |
| `00:12:28.160 - 00:12:30.310` | a shared buffer so the buffer is the |
| `00:12:30.320 - 00:12:33.430` | shared memory item and it's going to be |
| `00:12:33.440 - 00:12:36.790` | a fifo queue so the data goes in one end |
| `00:12:36.800 - 00:12:38.870` | and there's some other thread that is |
| `00:12:38.880 - 00:12:40.710` | reading from the buffer |
| `00:12:40.720 - 00:12:43.350` | when it needs a character it just |
| `00:12:43.360 - 00:12:45.829` | needs to access this shared data |
| `00:12:45.839 - 00:12:46.870` | structure |
| `00:12:46.880 - 00:12:49.269` | which we will need to protect and in |
| `00:12:49.279 - 00:12:51.269` | this example we will protect it with a |
| `00:12:51.279 - 00:12:53.269` | spin lock so |
| `00:12:53.279 - 00:12:55.030` | here's the code that we might see in the |
| `00:12:55.040 - 00:12:56.230` | handler |
| `00:12:56.240 - 00:12:58.230` | it acquires the spin lock |
| `00:12:58.240 - 00:12:59.910` | adds a character to the buffer and then |
| `00:12:59.920 - 00:13:02.069` | releases the lock the thread that wants |
| `00:13:02.079 - 00:13:02.790` | to |
| `00:13:02.800 - 00:13:05.670` | get input is going to acquire the lock |
| `00:13:05.680 - 00:13:07.670` | so it can access the shared buffer |
| `00:13:07.680 - 00:13:10.389` | remove a character and release the lock |
| `00:13:10.399 - 00:13:11.590` | i'm not going to be concerned with what |
| `00:13:11.600 - 00:13:13.350` | happens if the buffer is completely full |
| `00:13:13.360 - 00:13:17.350` | or completely empty |
| `00:13:17.360 - 00:13:18.310` | the |
| `00:13:18.320 - 00:13:20.790` | handler is called when someone types a |
| `00:13:20.800 - 00:13:23.430` | character on the keyboard and interrupt |
| `00:13:23.440 - 00:13:24.629` | handlers |
| `00:13:24.639 - 00:13:27.190` | begin with the trap processing which |
| `00:13:27.200 - 00:13:29.750` | will disable interrupts and then they'll |
| `00:13:29.760 - 00:13:32.310` | do some stuff basically this stuff here |
| `00:13:32.320 - 00:13:34.389` | in the case of our example |
| `00:13:34.399 - 00:13:36.389` | and then they will return to the |
| `00:13:36.399 - 00:13:38.629` | interrupted code with the |
| `00:13:38.639 - 00:13:41.670` | s rep instruction which will re-enable |
| `00:13:41.680 - 00:13:44.069` | interrupts so we might imagine a thread |
| `00:13:44.079 - 00:13:46.550` | t that's running like this |
| `00:13:46.560 - 00:13:48.550` | an interrupt happens as a result of a |
| `00:13:48.560 - 00:13:51.509` | key being pressed the handler runs and |
| `00:13:51.519 - 00:13:52.949` | then when it's done adding the character |
| `00:13:52.959 - 00:13:54.629` | to the buffer it returns to the |
| `00:13:54.639 - 00:13:56.949` | interrupted thread whatever it was and |
| `00:13:56.959 - 00:13:58.629` | continues |
| `00:13:58.639 - 00:14:01.670` | well so you can imagine this situation |
| `00:14:01.680 - 00:14:03.110` | if t |
| `00:14:03.120 - 00:14:04.550` | happens to be this thread here that |
| `00:14:04.560 - 00:14:06.710` | acquires a lock |
| `00:14:06.720 - 00:14:08.870` | and then at just the wrong moment the |
| `00:14:08.880 - 00:14:11.990` | interrupt comes in from the keyboard and |
| `00:14:12.000 - 00:14:14.870` | the handler is invoked and the first |
| `00:14:14.880 - 00:14:16.870` | thing the handler does is try to acquire |
| `00:14:16.880 - 00:14:18.150` | that lock |
| `00:14:18.160 - 00:14:19.350` | well |
| `00:14:19.360 - 00:14:21.269` | it will wait until the lock is released |
| `00:14:21.279 - 00:14:22.870` | but unfortunately the thread that is |
| `00:14:22.880 - 00:14:24.150` | holding the lock |
| `00:14:24.160 - 00:14:26.389` | is waiting for the handler to complete |
| `00:14:26.399 - 00:14:30.550` | so we've got a deadlock situation |
| `00:14:30.560 - 00:14:32.790` | operating systems we need to remember |
| `00:14:32.800 - 00:14:35.269` | that if anything if any combination of |
| `00:14:35.279 - 00:14:38.870` | events can possibly theoretically happen |
| `00:14:38.880 - 00:14:40.790` | it's best to assume that it will happen |
| `00:14:40.800 - 00:14:43.590` | no matter how unlikely or how rarely you |
| `00:14:43.600 - 00:14:47.189` | think the event is |
| `00:14:47.199 - 00:14:48.870` | our first approach to solving the |
| `00:14:48.880 - 00:14:50.870` | problem of deadlock is |
| `00:14:50.880 - 00:14:53.110` | to disable interrupts in the acquire |
| `00:14:53.120 - 00:14:54.150` | function |
| `00:14:54.160 - 00:14:56.389` | and then to re-enable them |
| `00:14:56.399 - 00:14:58.629` | in the release function |
| `00:14:58.639 - 00:15:00.790` | so we don't have a situation like this |
| `00:15:00.800 - 00:15:03.189` | if t acquires a lock it will |
| `00:15:03.199 - 00:15:05.430` | disable interrupts so it uh cannot have |
| `00:15:05.440 - 00:15:08.230` | a situation where some other thread on |
| `00:15:08.240 - 00:15:09.829` | that same |
| `00:15:09.839 - 00:15:13.670` | core is trying to acquire the lock |
| `00:15:13.680 - 00:15:15.910` | this also has an additional benefit |
| `00:15:15.920 - 00:15:17.910` | we don't want to hold spin locks for |
| `00:15:17.920 - 00:15:19.269` | very long |
| `00:15:19.279 - 00:15:21.829` | so by disabling interrupts we are |
| `00:15:21.839 - 00:15:23.829` | preventing time slicing from happening |
| `00:15:23.839 - 00:15:26.230` | while the lock is held and we are |
| `00:15:26.240 - 00:15:28.069` | preventing interrupt processing from |
| `00:15:28.079 - 00:15:29.269` | occurring |
| `00:15:29.279 - 00:15:31.509` | basically we're allowing the core to |
| `00:15:31.519 - 00:15:33.910` | focus on the thread that is holding the |
| `00:15:33.920 - 00:15:35.509` | lock and to |
| `00:15:35.519 - 00:15:37.269` | complete its critical section without |
| `00:15:37.279 - 00:15:40.389` | interruption quickly and then release |
| `00:15:40.399 - 00:15:42.550` | the lock |
| `00:15:42.560 - 00:15:44.470` | but now we have another problem |
| `00:15:44.480 - 00:15:47.509` | what if interrupts are already disabled |
| `00:15:47.519 - 00:15:50.230` | for example in our handler code |
| `00:15:50.240 - 00:15:52.150` | we have interrupts disabled and for a |
| `00:15:52.160 - 00:15:53.910` | number of reasons we don't want to |
| `00:15:53.920 - 00:15:56.710` | re-enable interrupts prematurely before |
| `00:15:56.720 - 00:16:00.790` | we get to the system return instruction |
| `00:16:00.800 - 00:16:03.590` | we might also have |
| `00:16:03.600 - 00:16:05.110` | several spin locks that we need to |
| `00:16:05.120 - 00:16:06.150` | acquire |
| `00:16:06.160 - 00:16:09.269` | and so we have perhaps three acquire |
| `00:16:09.279 - 00:16:11.189` | functions being called in a row and for |
| `00:16:11.199 - 00:16:12.470` | each one of those we'll have three |
| `00:16:12.480 - 00:16:14.230` | release functions |
| `00:16:14.240 - 00:16:16.629` | but the first release will |
| `00:16:16.639 - 00:16:18.870` | uh disable sorry we'll re-enable |
| `00:16:18.880 - 00:16:20.710` | interrupts and that might not be quite |
| `00:16:20.720 - 00:16:23.590` | what we want so essentially we want this |
| `00:16:23.600 - 00:16:24.629` | release |
| `00:16:24.639 - 00:16:26.470` | to return the interrupt status to |
| `00:16:26.480 - 00:16:29.030` | whatever it was before the acquire |
| `00:16:29.040 - 00:16:31.509` | happened and we want to accommodate |
| `00:16:31.519 - 00:16:33.670` | nested calls that is we want to |
| `00:16:33.680 - 00:16:36.150` | accommodate several acquire function |
| `00:16:36.160 - 00:16:37.670` | calls |
| `00:16:37.680 - 00:16:40.069` | followed by several release function |
| `00:16:40.079 - 00:16:41.030` | calls |
| `00:16:41.040 - 00:16:43.350` | and our solution to this problem is |
| `00:16:43.360 - 00:16:45.509` | simple we're going to use a counter |
| `00:16:45.519 - 00:16:49.189` | and that counter happens to be called in |
| `00:16:49.199 - 00:16:50.389` | off |
| `00:16:50.399 - 00:16:53.430` | and it's specific to a core |
| `00:16:53.440 - 00:16:56.069` | this variable will be held in the cpu |
| `00:16:56.079 - 00:16:58.629` | structure each core has its own |
| `00:16:58.639 - 00:17:01.910` | cpu structure and each one will contain |
| `00:17:01.920 - 00:17:05.750` | its own counter so the acquire function |
| `00:17:05.760 - 00:17:08.309` | will increment the counter and then |
| `00:17:08.319 - 00:17:09.990` | disable the interrupts |
| `00:17:10.000 - 00:17:11.590` | perhaps they were already disabled but |
| `00:17:11.600 - 00:17:14.390` | they will be after the acquire for sure |
| `00:17:14.400 - 00:17:16.309` | and what's release going to do |
| `00:17:16.319 - 00:17:18.150` | well release is going to decrement the |
| `00:17:18.160 - 00:17:19.110` | count |
| `00:17:19.120 - 00:17:19.990` | and |
| `00:17:20.000 - 00:17:21.750` | to handle the nested case if it goes |
| `00:17:21.760 - 00:17:24.789` | back to zero then it's time to consider |
| `00:17:24.799 - 00:17:27.669` | re-enabling them more precisely we need |
| `00:17:27.679 - 00:17:29.510` | to ask whether they were previously |
| `00:17:29.520 - 00:17:31.190` | enabled before |
| `00:17:31.200 - 00:17:33.270` | the first |
| `00:17:33.280 - 00:17:34.789` | call to acquire |
| `00:17:34.799 - 00:17:37.110` | so if the count is zero and they were |
| `00:17:37.120 - 00:17:39.669` | previously enabled |
| `00:17:39.679 - 00:17:41.990` | then we will re-enable them |
| `00:17:42.000 - 00:17:44.710` | that previously enabled status is kept |
| `00:17:44.720 - 00:17:47.430` | in a variable called interrupt enable |
| `00:17:47.440 - 00:17:49.430` | int ena i guess |
| `00:17:49.440 - 00:17:51.990` | and so we all we will remember the |
| `00:17:52.000 - 00:17:54.150` | previous interrupt status before the |
| `00:17:54.160 - 00:17:56.870` | first call to acquire in this variable |
| `00:17:56.880 - 00:18:01.669` | which is a part of the cpu structure |
| `00:18:01.679 - 00:18:04.630` | okay now we can take a look at the code |
| `00:18:04.640 - 00:18:05.510` | for |
| `00:18:05.520 - 00:18:08.549` | push off and pop off |
| `00:18:08.559 - 00:18:11.430` | remember that in the acquire function |
| `00:18:11.440 - 00:18:13.110` | shown here |
| `00:18:13.120 - 00:18:15.270` | the first thing we did was |
| `00:18:15.280 - 00:18:17.669` | call push off to disable interrupts to |
| `00:18:17.679 - 00:18:20.070` | avoid deadlock so now we know what that |
| `00:18:20.080 - 00:18:20.950` | means |
| `00:18:20.960 - 00:18:22.710` | and in the release |
| `00:18:22.720 - 00:18:26.070` | we also uh end up calling |
| `00:18:26.080 - 00:18:28.390` | pop off |
| `00:18:28.400 - 00:18:31.750` | here's the code for release and right |
| `00:18:31.760 - 00:18:35.029` | before we return we re-enable interrupts |
| `00:18:35.039 - 00:18:37.350` | if appropriate so let's take a look at |
| `00:18:37.360 - 00:18:38.549` | push off |
| `00:18:38.559 - 00:18:43.270` | and pop off |
| `00:18:43.280 - 00:18:45.270` | push-off is called by acquire we |
| `00:18:45.280 - 00:18:47.029` | immediately turn interrupts off we |
| `00:18:47.039 - 00:18:49.990` | disable interrupts well first we |
| `00:18:50.000 - 00:18:51.830` | find out what the previous status of the |
| `00:18:51.840 - 00:18:54.789` | interrupts was that's called old |
| `00:18:54.799 - 00:18:56.789` | and then here we're accessing the cpu |
| `00:18:56.799 - 00:18:57.750` | structure |
| `00:18:57.760 - 00:18:59.029` | and |
| `00:18:59.039 - 00:19:01.590` | if this is the first call to acquire |
| `00:19:01.600 - 00:19:03.510` | that is if the counter is already is |
| `00:19:03.520 - 00:19:05.669` | previously zero then we're going to save |
| `00:19:05.679 - 00:19:07.510` | the old status |
| `00:19:07.520 - 00:19:09.110` | but in any case we're going to increment |
| `00:19:09.120 - 00:19:10.710` | the counter |
| `00:19:10.720 - 00:19:13.510` | pop off is as you'd expect |
| `00:19:13.520 - 00:19:14.710` | pretty similar |
| `00:19:14.720 - 00:19:15.669` | we are |
| `00:19:15.679 - 00:19:18.870` | accessing the counter and the |
| `00:19:18.880 - 00:19:19.830` | int |
| `00:19:19.840 - 00:19:21.990` | enabled variable here |
| `00:19:22.000 - 00:19:23.909` | so we start by getting a pointer to the |
| `00:19:23.919 - 00:19:25.909` | cpu structure |
| `00:19:25.919 - 00:19:26.870` | and |
| `00:19:26.880 - 00:19:28.549` | then we decrement the counter at this |
| `00:19:28.559 - 00:19:31.830` | point here and if it went to zero |
| `00:19:31.840 - 00:19:34.950` | and interrupts were previously enabled |
| `00:19:34.960 - 00:19:38.470` | then we turn them back on at this point |
| `00:19:38.480 - 00:19:41.350` | we also have some uh error checking here |
| `00:19:41.360 - 00:19:43.669` | if for some reason we call pop off and |
| `00:19:43.679 - 00:19:45.830` | interrupts are are already enabled |
| `00:19:45.840 - 00:19:48.230` | something's drastically the matter so we |
| `00:19:48.240 - 00:19:49.830` | print an error message |
| `00:19:49.840 - 00:19:52.549` | and we also make sure that the counter |
| `00:19:52.559 - 00:19:55.350` | is not zero or smaller |
| `00:19:55.360 - 00:19:57.510` | that would also be some sort of an error |
| `00:19:57.520 - 00:19:58.549` | condition |
| `00:19:58.559 - 00:20:01.909` | okay that's it for spin locks uh see you |
| `00:20:01.919 - 00:20:05.320` | in the next video |
