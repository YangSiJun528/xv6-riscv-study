# xv6 Kernel-37: File-Related System Calls-Part 2

| Field | Value |
| --- | --- |
| Video ID | `TYmHsRS_JWI` |
| URL | <https://www.youtube.com/watch?v=TYmHsRS_JWI> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.199 - 00:00:02.790` | this video is part of a series on the |
| `00:00:02.800 - 00:00:05.510` | xp6 operating system kernel |
| `00:00:05.520 - 00:00:07.510` | there are a number of functions that |
| `00:00:07.520 - 00:00:10.070` | implement the file related system calls |
| `00:00:10.080 - 00:00:12.390` | and these are coming out of the file |
| `00:00:12.400 - 00:00:14.549` | sysfile dot c |
| `00:00:14.559 - 00:00:16.470` | there are quite a few functions and in |
| `00:00:16.480 - 00:00:18.950` | the previous video that is part one i |
| `00:00:18.960 - 00:00:21.590` | covered some of them and in this video i |
| `00:00:21.600 - 00:00:23.830` | will continue and in particular i will |
| `00:00:23.840 - 00:00:26.710` | be covering the following system calls |
| `00:00:26.720 - 00:00:28.150` | open |
| `00:00:28.160 - 00:00:30.710` | make node make directory |
| `00:00:30.720 - 00:00:32.389` | and pipe |
| `00:00:32.399 - 00:00:34.870` | the exact system call is a bit |
| `00:00:34.880 - 00:00:37.110` | complicated and i will handle that in a |
| `00:00:37.120 - 00:00:38.790` | subsequent video |
| `00:00:38.800 - 00:00:42.709` | okay let's continue with these functions |
| `00:00:42.719 - 00:00:45.190` | now let's look at the create function |
| `00:00:45.200 - 00:00:47.830` | this function is used to create a file |
| `00:00:47.840 - 00:00:49.910` | and add it to some directory in the file |
| `00:00:49.920 - 00:00:51.189` | system |
| `00:00:51.199 - 00:00:53.270` | it's passed a type code that indicates |
| `00:00:53.280 - 00:00:55.350` | what kind of file we want to create |
| `00:00:55.360 - 00:00:58.069` | either a directory file a regular file |
| `00:00:58.079 - 00:01:00.069` | or a device file |
| `00:01:00.079 - 00:01:02.470` | if we're creating a device file we need |
| `00:01:02.480 - 00:01:05.030` | the major and the minor numbers so these |
| `00:01:05.040 - 00:01:06.390` | will be something |
| `00:01:06.400 - 00:01:08.390` | for other kinds of files for directory |
| `00:01:08.400 - 00:01:09.990` | files and regular files |
| `00:01:10.000 - 00:01:13.429` | these will be passed in as zero and not |
| `00:01:13.439 - 00:01:14.950` | used |
| `00:01:14.960 - 00:01:16.789` | we're given the path name here as an |
| `00:01:16.799 - 00:01:20.469` | argument and that tells not only uh |
| `00:01:20.479 - 00:01:22.149` | what directory we're adding it to but |
| `00:01:22.159 - 00:01:24.070` | the name as well so for example with |
| `00:01:24.080 - 00:01:25.350` | this file name |
| `00:01:25.360 - 00:01:26.870` | we can see that we're adding a file |
| `00:01:26.880 - 00:01:30.230` | called xxx to some directory in this |
| `00:01:30.240 - 00:01:34.230` | example user bin |
| `00:01:34.240 - 00:01:36.230` | on success we will return a pointer to |
| `00:01:36.240 - 00:01:38.870` | the newly created file or more precisely |
| `00:01:38.880 - 00:01:41.510` | to an inode for the newly created file |
| `00:01:41.520 - 00:01:45.510` | and that inode will be locked as well |
| `00:01:45.520 - 00:01:49.830` | if there's a problem we'll return null |
| `00:01:49.840 - 00:01:51.590` | create is called from exactly three |
| `00:01:51.600 - 00:01:53.429` | places it's called from the open system |
| `00:01:53.439 - 00:01:55.749` | call it's called from the make node |
| `00:01:55.759 - 00:01:58.230` | system call and it's called from make |
| `00:01:58.240 - 00:01:59.670` | directory |
| `00:01:59.680 - 00:02:00.789` | and |
| `00:02:00.799 - 00:02:02.469` | for the |
| `00:02:02.479 - 00:02:06.149` | uh make node system call it's for adding |
| `00:02:06.159 - 00:02:09.350` | a device file and so type will be device |
| `00:02:09.360 - 00:02:10.949` | for make directory we're creating a |
| `00:02:10.959 - 00:02:14.070` | directory so type will be directory and |
| `00:02:14.080 - 00:02:16.550` | finally the open system call has the |
| `00:02:16.560 - 00:02:19.270` | option to open a file and create it if |
| `00:02:19.280 - 00:02:20.630` | it doesn't exist |
| `00:02:20.640 - 00:02:22.949` | so when called from open |
| `00:02:22.959 - 00:02:25.910` | the type will be file |
| `00:02:25.920 - 00:02:26.949` | so |
| `00:02:26.959 - 00:02:28.710` | we passed in this path |
| `00:02:28.720 - 00:02:31.430` | and the first thing we do is call name i |
| `00:02:31.440 - 00:02:34.229` | parent to parse this path name |
| `00:02:34.239 - 00:02:36.869` | so it will identify the final thing in |
| `00:02:36.879 - 00:02:39.030` | the path name and store it in this |
| `00:02:39.040 - 00:02:41.350` | variable name so this local variable |
| `00:02:41.360 - 00:02:44.470` | name will contain xxx |
| `00:02:44.480 - 00:02:47.110` | and then the prefix of the path name |
| `00:02:47.120 - 00:02:49.589` | will be a directory and that directory |
| `00:02:49.599 - 00:02:52.309` | uh will be open and the reference count |
| `00:02:52.319 - 00:02:54.790` | will for that inode will be incremented |
| `00:02:54.800 - 00:02:57.350` | and dp will be set to a pointer to the |
| `00:02:57.360 - 00:02:58.949` | inode |
| `00:02:58.959 - 00:03:00.869` | if everything's okay then we'll keep |
| `00:03:00.879 - 00:03:03.990` | going but if we had a problem |
| `00:03:04.000 - 00:03:06.229` | then this will return null and will |
| `00:03:06.239 - 00:03:09.350` | immediately return null |
| `00:03:09.360 - 00:03:10.949` | so we're going to be modifying this |
| `00:03:10.959 - 00:03:14.470` | directory we begin by locking it |
| `00:03:14.480 - 00:03:17.589` | the next thing we do is we look up xxx |
| `00:03:17.599 - 00:03:19.350` | in this directory to see if it's already |
| `00:03:19.360 - 00:03:20.390` | there |
| `00:03:20.400 - 00:03:22.309` | okay if it's already there |
| `00:03:22.319 - 00:03:24.710` | this will return and |
| `00:03:24.720 - 00:03:27.110` | a pointer to the inode for that file and |
| `00:03:27.120 - 00:03:29.190` | we'll set ip and it will be something |
| `00:03:29.200 - 00:03:31.670` | that's non-zero so we do this code if |
| `00:03:31.680 - 00:03:33.910` | the file already exists |
| `00:03:33.920 - 00:03:34.949` | and |
| `00:03:34.959 - 00:03:38.630` | if this thing returns no then we've uh |
| `00:03:38.640 - 00:03:41.589` | we don't have a file so we'll go on here |
| `00:03:41.599 - 00:03:43.350` | to create the file |
| `00:03:43.360 - 00:03:45.670` | but it might be okay for us to have the |
| `00:03:45.680 - 00:03:47.110` | file |
| `00:03:47.120 - 00:03:50.070` | in particular |
| `00:03:50.080 - 00:03:52.390` | if we are called from open |
| `00:03:52.400 - 00:03:54.309` | then it would be the case that we've had |
| `00:03:54.319 - 00:03:55.830` | the flag to create the file if it |
| `00:03:55.840 - 00:03:57.429` | doesn't exist |
| `00:03:57.439 - 00:03:59.910` | and if that's the case we might have an |
| `00:03:59.920 - 00:04:02.229` | existing regular file okay so we check |
| `00:04:02.239 - 00:04:04.949` | the type field of the file that does |
| `00:04:04.959 - 00:04:07.270` | exist and if it's a regular file well |
| `00:04:07.280 - 00:04:09.110` | that's okay it already exists we'll go |
| `00:04:09.120 - 00:04:10.229` | with that |
| `00:04:10.239 - 00:04:12.710` | or if it's a device file that too is |
| `00:04:12.720 - 00:04:14.470` | okay so we'll go with that |
| `00:04:14.480 - 00:04:17.590` | and in that case we'll return ip |
| `00:04:17.600 - 00:04:19.990` | uh but if we were called to make a |
| `00:04:20.000 - 00:04:23.670` | device file or make a directory then |
| `00:04:23.680 - 00:04:24.950` | this is going to be false and we'll |
| `00:04:24.960 - 00:04:28.390` | return null but if we return if we find |
| `00:04:28.400 - 00:04:29.749` | that it's okay |
| `00:04:29.759 - 00:04:31.749` | we will return ip |
| `00:04:31.759 - 00:04:33.670` | first we have to lock it before we check |
| `00:04:33.680 - 00:04:36.070` | the type field and then we will return |
| `00:04:36.080 - 00:04:37.510` | with it locked |
| `00:04:37.520 - 00:04:40.070` | and um |
| `00:04:40.080 - 00:04:41.990` | before we return we will unlock the |
| `00:04:42.000 - 00:04:45.030` | directory okay and we'll return either |
| `00:04:45.040 - 00:04:46.390` | way here |
| `00:04:46.400 - 00:04:49.350` | and so uh if it's not okay that is if |
| `00:04:49.360 - 00:04:50.390` | we're called |
| `00:04:50.400 - 00:04:52.629` | from the make node system call or the |
| `00:04:52.639 - 00:04:54.550` | make directory system call and we've |
| `00:04:54.560 - 00:04:57.350` | already got something then um |
| `00:04:57.360 - 00:04:58.150` | uh |
| `00:04:58.160 - 00:05:01.270` | we need to return no so we're going to |
| `00:05:01.280 - 00:05:03.830` | both unlock the directory and unlock the |
| `00:05:03.840 - 00:05:05.670` | file that already exists and just return |
| `00:05:05.680 - 00:05:07.110` | null |
| `00:05:07.120 - 00:05:08.310` | okay so we've |
| `00:05:08.320 - 00:05:10.469` | got the directory file locked at this |
| `00:05:10.479 - 00:05:12.390` | point and we've determined that the file |
| `00:05:12.400 - 00:05:15.270` | doesn't exist so at this point we call i |
| `00:05:15.280 - 00:05:16.230` | alec |
| `00:05:16.240 - 00:05:18.550` | i alec will create we'll allocate an |
| `00:05:18.560 - 00:05:21.110` | inode and we'll increment the reference |
| `00:05:21.120 - 00:05:23.590` | count and we'll set its type to whatever |
| `00:05:23.600 - 00:05:25.749` | were passed in here |
| `00:05:25.759 - 00:05:27.749` | and so now we've got the inode in |
| `00:05:27.759 - 00:05:30.629` | existence and assuming that that works |
| `00:05:30.639 - 00:05:32.710` | successfully and doesn't return no we |
| `00:05:32.720 - 00:05:34.150` | keep on going |
| `00:05:34.160 - 00:05:35.670` | now we lock the file that we've just |
| `00:05:35.680 - 00:05:37.270` | created because we're going to access |
| `00:05:37.280 - 00:05:38.710` | some fields |
| `00:05:38.720 - 00:05:41.029` | first of all we've uh we're going to set |
| `00:05:41.039 - 00:05:43.990` | its hard link count to one and we're |
| `00:05:44.000 - 00:05:45.350` | going to fill in the major and minor |
| `00:05:45.360 - 00:05:47.909` | numbers which will be important if it |
| `00:05:47.919 - 00:05:50.830` | happens to be a device file that we just |
| `00:05:50.840 - 00:05:54.950` | created then we're going to see whether |
| `00:05:54.960 - 00:05:56.390` | the thing that we're creating is a |
| `00:05:56.400 - 00:05:57.830` | directory file |
| `00:05:57.840 - 00:06:00.150` | if we have just created a directory file |
| `00:06:00.160 - 00:06:02.230` | we need to add entries into the |
| `00:06:02.240 - 00:06:05.270` | directory for the dot and dot dot |
| `00:06:05.280 - 00:06:07.270` | files |
| `00:06:07.280 - 00:06:09.189` | so that's what we're going to do down |
| `00:06:09.199 - 00:06:12.390` | here but remember that the hard link |
| `00:06:12.400 - 00:06:13.189` | count |
| `00:06:13.199 - 00:06:16.710` | in a directory situation will |
| `00:06:16.720 - 00:06:17.749` | include |
| `00:06:17.759 - 00:06:18.710` | the |
| `00:06:18.720 - 00:06:21.670` | hard link from a child to its parent so |
| `00:06:21.680 - 00:06:23.909` | here we are increasing the count of hard |
| `00:06:23.919 - 00:06:28.550` | links to the directory that contains xxx |
| `00:06:28.560 - 00:06:30.150` | and that's what happens here and that's |
| `00:06:30.160 - 00:06:31.590` | why |
| `00:06:31.600 - 00:06:34.550` | we do that and then since we've modified |
| `00:06:34.560 - 00:06:38.309` | the directory that we're adding to we |
| `00:06:38.319 - 00:06:39.749` | update that |
| `00:06:39.759 - 00:06:42.710` | and then we are going to add |
| `00:06:42.720 - 00:06:45.590` | entries for dot and dot dot so we call |
| `00:06:45.600 - 00:06:47.430` | directory link |
| `00:06:47.440 - 00:06:49.510` | first for dot providing the i number of |
| `00:06:49.520 - 00:06:51.430` | the file that we just created |
| `00:06:51.440 - 00:06:54.390` | and then for dot dot providing the i |
| `00:06:54.400 - 00:06:56.790` | number for the directory itself |
| `00:06:56.800 - 00:07:00.309` | and if something goes wrong there and we |
| `00:07:00.319 - 00:07:04.070` | panic but otherwise we keep going |
| `00:07:04.080 - 00:07:07.430` | now at this point we need to add xxx to |
| `00:07:07.440 - 00:07:09.189` | the directory itself |
| `00:07:09.199 - 00:07:12.390` | so here we're calling directory link |
| `00:07:12.400 - 00:07:15.990` | yet again passing in a pointer or to the |
| `00:07:16.000 - 00:07:18.070` | inode for the directory that we're |
| `00:07:18.080 - 00:07:20.150` | adding xxx2 |
| `00:07:20.160 - 00:07:22.790` | as well as the name xxx |
| `00:07:22.800 - 00:07:24.469` | and the i number of the file that we |
| `00:07:24.479 - 00:07:25.749` | just created |
| `00:07:25.759 - 00:07:27.430` | so |
| `00:07:27.440 - 00:07:30.950` | presumably that will return |
| `00:07:30.960 - 00:07:33.270` | something and |
| `00:07:33.280 - 00:07:35.990` | if there is a problem then it will |
| `00:07:36.000 - 00:07:39.830` | return negative one and uh we can |
| `00:07:39.840 - 00:07:42.390` | panic but otherwise uh we're pretty much |
| `00:07:42.400 - 00:07:43.189` | done |
| `00:07:43.199 - 00:07:46.550` | we still got the directory itself locked |
| `00:07:46.560 - 00:07:50.230` | so we need to unlock it and we also |
| `00:07:50.240 - 00:07:52.070` | incremented the |
| `00:07:52.080 - 00:07:54.070` | reference count when we called name i |
| `00:07:54.080 - 00:07:57.110` | parent so we need to undo that as well |
| `00:07:57.120 - 00:07:59.990` | with a put so we do that here and then |
| `00:08:00.000 - 00:08:02.070` | we return a pointer to the newly created |
| `00:08:02.080 - 00:08:03.350` | file |
| `00:08:03.360 - 00:08:07.670` | and it will be locked at this point |
| `00:08:07.680 - 00:08:10.070` | now let's look at the make directory |
| `00:08:10.080 - 00:08:12.309` | system call it's passed a path name and |
| `00:08:12.319 - 00:08:13.749` | it's just going to create a directory |
| `00:08:13.759 - 00:08:15.749` | file |
| `00:08:15.759 - 00:08:17.670` | so here's the code for sys make |
| `00:08:17.680 - 00:08:18.710` | directory |
| `00:08:18.720 - 00:08:20.150` | and basically it's just going to call |
| `00:08:20.160 - 00:08:21.990` | create which we looked at so it's pretty |
| `00:08:22.000 - 00:08:24.309` | straightforward |
| `00:08:24.319 - 00:08:25.350` | we begin |
| `00:08:25.360 - 00:08:27.830` | by putting everything in a transaction |
| `00:08:27.840 - 00:08:30.469` | so we have a begin up and an end up |
| `00:08:30.479 - 00:08:31.589` | and |
| `00:08:31.599 - 00:08:33.509` | if there's a problem we'll return minus |
| `00:08:33.519 - 00:08:35.430` | one otherwise we'll return zero to |
| `00:08:35.440 - 00:08:38.310` | indicate that everything's okay |
| `00:08:38.320 - 00:08:41.190` | we begin by looking at the first |
| `00:08:41.200 - 00:08:42.469` | argument |
| `00:08:42.479 - 00:08:45.030` | and it is the path name so we're going |
| `00:08:45.040 - 00:08:48.870` | to call argument string to copy from the |
| `00:08:48.880 - 00:08:50.949` | user's virtual address space into this |
| `00:08:50.959 - 00:08:53.269` | local variable path |
| `00:08:53.279 - 00:08:56.150` | the path name and then we're going to |
| `00:08:56.160 - 00:08:58.949` | call create providing it that path name |
| `00:08:58.959 - 00:09:00.790` | and the type code |
| `00:09:00.800 - 00:09:02.230` | will indicate that we want to create a |
| `00:09:02.240 - 00:09:03.829` | directory file |
| `00:09:03.839 - 00:09:05.590` | and we pass in the major and minor |
| `00:09:05.600 - 00:09:07.430` | numbers as zero |
| `00:09:07.440 - 00:09:10.630` | and if that works it will set ip |
| `00:09:10.640 - 00:09:13.110` | to be a pointer to the inode for the |
| `00:09:13.120 - 00:09:15.829` | newly created directory file |
| `00:09:15.839 - 00:09:18.150` | if there's a problem it will return null |
| `00:09:18.160 - 00:09:20.310` | and we immediately end the transaction |
| `00:09:20.320 - 00:09:23.190` | and return one but if it's exceeded well |
| `00:09:23.200 - 00:09:25.350` | we don't really need to do anything more |
| `00:09:25.360 - 00:09:26.150` | so |
| `00:09:26.160 - 00:09:29.670` | we have locked this uh inode so we need |
| `00:09:29.680 - 00:09:32.150` | to unlock it and then decrement the |
| `00:09:32.160 - 00:09:34.710` | reference count for the inode and so we |
| `00:09:34.720 - 00:09:37.590` | do that here in our transaction and then |
| `00:09:37.600 - 00:09:39.990` | return 0. |
| `00:09:40.000 - 00:09:42.790` | now let's take a look at the make node |
| `00:09:42.800 - 00:09:45.110` | system call it's very similar it's |
| `00:09:45.120 - 00:09:47.269` | passed a path name and we'll create a |
| `00:09:47.279 - 00:09:49.430` | file but it will create a device file |
| `00:09:49.440 - 00:09:52.150` | with the given major and minor numbers |
| `00:09:52.160 - 00:09:53.990` | returning zero if everything's okay and |
| `00:09:54.000 - 00:09:55.990` | minus one otherwise |
| `00:09:56.000 - 00:09:58.389` | so here is the code for |
| `00:09:58.399 - 00:09:59.670` | make node |
| `00:09:59.680 - 00:10:02.310` | and it's really quite similar |
| `00:10:02.320 - 00:10:03.990` | we have a transaction and if |
| `00:10:04.000 - 00:10:05.990` | everything's okay we return |
| `00:10:06.000 - 00:10:08.949` | zero otherwise we return minus one |
| `00:10:08.959 - 00:10:11.990` | we have three arguments uh the first one |
| `00:10:12.000 - 00:10:14.310` | is the path name so we call argument |
| `00:10:14.320 - 00:10:16.550` | string to grab that |
| `00:10:16.560 - 00:10:18.710` | and put that string |
| `00:10:18.720 - 00:10:21.110` | move it from the user's virtual address |
| `00:10:21.120 - 00:10:24.150` | space into this local variable path |
| `00:10:24.160 - 00:10:26.389` | and then we get the next argument and |
| `00:10:26.399 - 00:10:29.110` | move that into the local variable major |
| `00:10:29.120 - 00:10:32.470` | and then we call argument argent to get |
| `00:10:32.480 - 00:10:36.150` | the last argument as a number an integer |
| `00:10:36.160 - 00:10:39.030` | and move it into the minor variable |
| `00:10:39.040 - 00:10:40.470` | now that we've got those we can just |
| `00:10:40.480 - 00:10:43.670` | call create and provided the path |
| `00:10:43.680 - 00:10:46.710` | and this time the type is device and we |
| `00:10:46.720 - 00:10:48.550` | provided the given major and minor |
| `00:10:48.560 - 00:10:49.670` | numbers |
| `00:10:49.680 - 00:10:52.790` | and again if there's a problem uh it |
| `00:10:52.800 - 00:10:54.069` | will return |
| `00:10:54.079 - 00:10:56.150` | null in which case we terminate our |
| `00:10:56.160 - 00:10:58.470` | transaction and return -1 but if |
| `00:10:58.480 - 00:11:00.870` | everything's okay it will |
| `00:11:00.880 - 00:11:02.630` | return a pointer to the inode for the |
| `00:11:02.640 - 00:11:04.550` | newly created file |
| `00:11:04.560 - 00:11:06.949` | and that file is locked so we need to |
| `00:11:06.959 - 00:11:08.710` | unlock it and decrease the reference |
| `00:11:08.720 - 00:11:11.509` | count and in the transaction and return |
| `00:11:11.519 - 00:11:14.710` | 0 for success |
| `00:11:14.720 - 00:11:16.710` | we are now in a position to look at the |
| `00:11:16.720 - 00:11:19.110` | code for the open system call |
| `00:11:19.120 - 00:11:22.710` | remember that in unix open is passed a |
| `00:11:22.720 - 00:11:24.949` | path name that's the name of the file to |
| `00:11:24.959 - 00:11:27.509` | be opened as well as an integer it's |
| `00:11:27.519 - 00:11:30.230` | called the flags value and that value |
| `00:11:30.240 - 00:11:32.389` | has some bits defined in it that tell |
| `00:11:32.399 - 00:11:34.870` | what to do in certain situations |
| `00:11:34.880 - 00:11:35.750` | and |
| `00:11:35.760 - 00:11:37.509` | the system call returns the file |
| `00:11:37.519 - 00:11:39.910` | descriptor that's the small integer that |
| `00:11:39.920 - 00:11:42.310` | we'll be using in system calls like read |
| `00:11:42.320 - 00:11:45.269` | write close and so on |
| `00:11:45.279 - 00:11:46.470` | now this |
| `00:11:46.480 - 00:11:48.949` | flags value |
| `00:11:48.959 - 00:11:52.710` | has this definition here for xv6 |
| `00:11:52.720 - 00:11:54.949` | in real unix |
| `00:11:54.959 - 00:11:57.190` | this is a little bit more complicated so |
| `00:11:57.200 - 00:11:58.949` | you can look at the documentation for |
| `00:11:58.959 - 00:12:00.310` | that if you're concerned about what's |
| `00:12:00.320 - 00:12:03.590` | going on in production operating systems |
| `00:12:03.600 - 00:12:06.790` | but here's what xv6 does |
| `00:12:06.800 - 00:12:09.670` | it has the flag word |
| `00:12:09.680 - 00:12:13.190` | defined with several bits if this bit is |
| `00:12:13.200 - 00:12:15.829` | one then the file should be opened right |
| `00:12:15.839 - 00:12:17.509` | only |
| `00:12:17.519 - 00:12:19.829` | so write only is that bit |
| `00:12:19.839 - 00:12:21.990` | if this bit is one then the file should |
| `00:12:22.000 - 00:12:25.269` | be open for reading and writing and |
| `00:12:25.279 - 00:12:27.350` | that's this bit and if this bit is set |
| `00:12:27.360 - 00:12:29.430` | we should create the file if it doesn't |
| `00:12:29.440 - 00:12:31.829` | exist and if this bit is set we should |
| `00:12:31.839 - 00:12:33.910` | truncate the file to |
| `00:12:33.920 - 00:12:36.230` | size 0 if it exists |
| `00:12:36.240 - 00:12:38.069` | and finally we have this read only which |
| `00:12:38.079 - 00:12:41.509` | is no bit set at all |
| `00:12:41.519 - 00:12:43.030` | okay now let's try to walk through the |
| `00:12:43.040 - 00:12:44.310` | code for |
| `00:12:44.320 - 00:12:47.590` | the sys open function which implements |
| `00:12:47.600 - 00:12:49.829` | the open system call |
| `00:12:49.839 - 00:12:51.990` | it will be returning the file descriptor |
| `00:12:52.000 - 00:12:54.069` | integer |
| `00:12:54.079 - 00:12:57.110` | it begins by looking at its arguments |
| `00:12:57.120 - 00:12:57.829` | the |
| `00:12:57.839 - 00:13:01.030` | function calls arg string to move the |
| `00:13:01.040 - 00:13:04.949` | path name which is the first argument to |
| `00:13:04.959 - 00:13:05.750` | the |
| `00:13:05.760 - 00:13:08.069` | variable path here which is a local |
| `00:13:08.079 - 00:13:09.110` | variable |
| `00:13:09.120 - 00:13:11.509` | from the user's virtual address space |
| `00:13:11.519 - 00:13:15.269` | and then it calls argent for the second |
| `00:13:15.279 - 00:13:18.389` | argument and it stores the flag integer |
| `00:13:18.399 - 00:13:20.790` | in this local variable called |
| `00:13:20.800 - 00:13:22.949` | o mode or open mode |
| `00:13:22.959 - 00:13:24.870` | if there's anything wrong we immediately |
| `00:13:24.880 - 00:13:26.550` | return minus one |
| `00:13:26.560 - 00:13:28.710` | otherwise we start a transaction we're |
| `00:13:28.720 - 00:13:31.430` | going to possibly be creating a file so |
| `00:13:31.440 - 00:13:33.829` | we will be modifying the disk so we need |
| `00:13:33.839 - 00:13:37.110` | to have a transaction going |
| `00:13:37.120 - 00:13:38.710` | now the next thing we have is an if |
| `00:13:38.720 - 00:13:41.590` | statement we look at the create bit in |
| `00:13:41.600 - 00:13:44.710` | the flags value and if that is set we do |
| `00:13:44.720 - 00:13:46.470` | this we call create |
| `00:13:46.480 - 00:13:50.470` | and if it's not set then we call name i |
| `00:13:50.480 - 00:13:53.269` | create will be past the path name |
| `00:13:53.279 - 00:13:54.629` | and it will |
| `00:13:54.639 - 00:13:56.870` | try to open the file if it already |
| `00:13:56.880 - 00:13:57.910` | exists |
| `00:13:57.920 - 00:13:59.910` | and if it doesn't exist it will create |
| `00:13:59.920 - 00:14:02.550` | it and it will create it as a regular |
| `00:14:02.560 - 00:14:05.829` | file |
| `00:14:05.839 - 00:14:08.550` | if it creates it or opens it |
| `00:14:08.560 - 00:14:11.269` | successfully then it will set ip to |
| `00:14:11.279 - 00:14:12.310` | point to |
| `00:14:12.320 - 00:14:14.629` | an inode representing that file and it |
| `00:14:14.639 - 00:14:16.150` | will not only increment the reference |
| `00:14:16.160 - 00:14:18.230` | account for that inode but it will also |
| `00:14:18.240 - 00:14:20.470` | lock that inode so i'm drawing two lines |
| `00:14:20.480 - 00:14:21.670` | here |
| `00:14:21.680 - 00:14:23.110` | if there's a problem though it will |
| `00:14:23.120 - 00:14:25.269` | return null and we immediately in the |
| `00:14:25.279 - 00:14:28.870` | transaction and return minus one |
| `00:14:28.880 - 00:14:30.790` | otherwise if the create bit is not set |
| `00:14:30.800 - 00:14:33.110` | then the file had better exist so we |
| `00:14:33.120 - 00:14:35.829` | called name i and provided the path name |
| `00:14:35.839 - 00:14:39.509` | and it will find that file and allocate |
| `00:14:39.519 - 00:14:42.550` | an inode in memory and return a pointer |
| `00:14:42.560 - 00:14:45.110` | to that inode with the reference count |
| `00:14:45.120 - 00:14:46.949` | incremented so that's why i draw one |
| `00:14:46.959 - 00:14:48.310` | line here |
| `00:14:48.320 - 00:14:50.069` | if there's any problem that like the |
| `00:14:50.079 - 00:14:52.550` | file doesn't exist then it will return |
| `00:14:52.560 - 00:14:55.030` | no and we in the transaction will return |
| `00:14:55.040 - 00:14:57.030` | minus one |
| `00:14:57.040 - 00:15:00.230` | next we lock that inode so in either |
| `00:15:00.240 - 00:15:02.870` | case we've got the file locked |
| `00:15:02.880 - 00:15:05.590` | if the file already existed then we |
| `00:15:05.600 - 00:15:08.230` | check to see if it's a directory file |
| `00:15:08.240 - 00:15:10.550` | we're allowed to open directory files |
| `00:15:10.560 - 00:15:13.430` | but only in read own only mode so here |
| `00:15:13.440 - 00:15:14.790` | we're checking to make sure that the |
| `00:15:14.800 - 00:15:18.069` | flags bit is the flag's word is all |
| `00:15:18.079 - 00:15:21.030` | zeros that is the um |
| `00:15:21.040 - 00:15:23.670` | right only or the read write flag uh |
| `00:15:23.680 - 00:15:25.910` | flags are not set we're not trying to |
| `00:15:25.920 - 00:15:28.069` | create it and it's uh we're not |
| `00:15:28.079 - 00:15:30.389` | truncating it so we're only allowed to |
| `00:15:30.399 - 00:15:31.829` | open it |
| `00:15:31.839 - 00:15:33.350` | in read-only mode |
| `00:15:33.360 - 00:15:35.030` | and if anything's wrong there we |
| `00:15:35.040 - 00:15:36.629` | immediately |
| `00:15:36.639 - 00:15:38.069` | unlock it |
| `00:15:38.079 - 00:15:40.470` | and we decrement the reference count in |
| `00:15:40.480 - 00:15:44.310` | the transaction and return minus one |
| `00:15:44.320 - 00:15:47.430` | if the file was opened and it |
| `00:15:47.440 - 00:15:50.389` | was a device type oh well that's okay we |
| `00:15:50.399 - 00:15:52.150` | do a quick check to make sure that the |
| `00:15:52.160 - 00:15:55.749` | major number is in the legal range here |
| `00:15:55.759 - 00:15:58.230` | and if there's a problem there we would |
| `00:15:58.240 - 00:16:00.230` | uh unlock it decrease the reference |
| `00:16:00.240 - 00:16:02.389` | count in the transaction and return |
| `00:16:02.399 - 00:16:05.350` | minus one |
| `00:16:05.360 - 00:16:07.110` | now we're going to |
| `00:16:07.120 - 00:16:08.629` | we've got an inode |
| `00:16:08.639 - 00:16:10.150` | either for |
| `00:16:10.160 - 00:16:12.389` | a directory or regular file |
| `00:16:12.399 - 00:16:14.550` | or a device file |
| `00:16:14.560 - 00:16:17.110` | and what we need to do is find a slot in |
| `00:16:17.120 - 00:16:19.430` | the open file array |
| `00:16:19.440 - 00:16:22.310` | for the proc structure for this process |
| `00:16:22.320 - 00:16:24.790` | and we need to fill in that value with a |
| `00:16:24.800 - 00:16:25.990` | pointer |
| `00:16:26.000 - 00:16:27.749` | to a file structure so we need to |
| `00:16:27.759 - 00:16:30.470` | allocate a file structure as well and |
| `00:16:30.480 - 00:16:33.189` | set its ip pointer to point to this |
| `00:16:33.199 - 00:16:35.269` | inode whether it's a |
| `00:16:35.279 - 00:16:37.590` | directory file a regular file or in this |
| `00:16:37.600 - 00:16:39.430` | case a device file |
| `00:16:39.440 - 00:16:41.430` | so that's what we're doing |
| `00:16:41.440 - 00:16:44.629` | here we call file alloc |
| `00:16:44.639 - 00:16:46.710` | to create the file structure |
| `00:16:46.720 - 00:16:48.829` | and we set f to point to that file |
| `00:16:48.839 - 00:16:52.069` | structure and then we call fdabloc |
| `00:16:52.079 - 00:16:53.749` | and fdalock will |
| `00:16:53.759 - 00:16:55.509` | look through the array and find an empty |
| `00:16:55.519 - 00:16:57.430` | slot and set |
| `00:16:57.440 - 00:17:00.069` | that to point to the file structure and |
| `00:17:00.079 - 00:17:02.069` | increase the reference count for the |
| `00:17:02.079 - 00:17:03.430` | file structure |
| `00:17:03.440 - 00:17:05.110` | so |
| `00:17:05.120 - 00:17:07.429` | if there is any problem with either of |
| `00:17:07.439 - 00:17:08.390` | these |
| `00:17:08.400 - 00:17:10.309` | that is we can't allocate the file |
| `00:17:10.319 - 00:17:13.590` | structure and it returns null or |
| `00:17:13.600 - 00:17:16.069` | there were no available slots in the |
| `00:17:16.079 - 00:17:17.590` | open file array |
| `00:17:17.600 - 00:17:18.470` | then |
| `00:17:18.480 - 00:17:21.029` | we will get rid of the file structure if |
| `00:17:21.039 - 00:17:24.549` | we allocated it by calling file close |
| `00:17:24.559 - 00:17:27.909` | and in either case we unlock the |
| `00:17:27.919 - 00:17:30.870` | inode and decrement the reference count |
| `00:17:30.880 - 00:17:34.230` | in the transaction and return -1 |
| `00:17:34.240 - 00:17:36.150` | okay but assuming everything's okay we |
| `00:17:36.160 - 00:17:38.710` | continue |
| `00:17:38.720 - 00:17:39.909` | if the |
| `00:17:39.919 - 00:17:41.909` | type of the inode that we've just opened |
| `00:17:41.919 - 00:17:43.350` | is device |
| `00:17:43.360 - 00:17:45.990` | then we've got sort of this situation |
| `00:17:46.000 - 00:17:48.390` | down here where the |
| `00:17:48.400 - 00:17:50.390` | inode points to a device |
| `00:17:50.400 - 00:17:52.870` | what we need to do is set the type of |
| `00:17:52.880 - 00:17:55.029` | the file structure that we've allocated |
| `00:17:55.039 - 00:17:58.789` | to device and copy the major number to |
| `00:17:58.799 - 00:18:00.470` | the file structure |
| `00:18:00.480 - 00:18:02.630` | so that's what we're doing here |
| `00:18:02.640 - 00:18:04.630` | we are setting the type to device and |
| `00:18:04.640 - 00:18:06.710` | copying the major number from the inode |
| `00:18:06.720 - 00:18:08.470` | to the file structure |
| `00:18:08.480 - 00:18:11.270` | otherwise we've got a directory file or |
| `00:18:11.280 - 00:18:12.710` | a regular file |
| `00:18:12.720 - 00:18:15.110` | and so in that case we need to set the |
| `00:18:15.120 - 00:18:17.190` | type of the file structure to |
| `00:18:17.200 - 00:18:19.990` | inode and we need to initialize the |
| `00:18:20.000 - 00:18:21.669` | offset to zero |
| `00:18:21.679 - 00:18:23.590` | and that's what we do |
| `00:18:23.600 - 00:18:26.150` | right here we set the type to inode and |
| `00:18:26.160 - 00:18:29.990` | we initialize the offset to zero |
| `00:18:30.000 - 00:18:32.789` | finally we need to fill in the ipointer |
| `00:18:32.799 - 00:18:34.630` | in the file structure to point to the |
| `00:18:34.640 - 00:18:36.789` | inode whether it's a directory or |
| `00:18:36.799 - 00:18:39.350` | regular file or whether it's a device |
| `00:18:39.360 - 00:18:42.710` | file we need to set the ip |
| `00:18:42.720 - 00:18:45.190` | field to point to the inode structure |
| `00:18:45.200 - 00:18:47.510` | and we do that here |
| `00:18:47.520 - 00:18:49.110` | then we need to set the readable and |
| `00:18:49.120 - 00:18:52.950` | writable flags in the file structure |
| `00:18:52.960 - 00:18:54.710` | so |
| `00:18:54.720 - 00:18:58.150` | here we are looking to see whether um |
| `00:18:58.160 - 00:19:00.710` | we have right only uh we make sure that |
| `00:19:00.720 - 00:19:04.230` | that bit is zero if it's zero then |
| `00:19:04.240 - 00:19:05.430` | either we have a |
| `00:19:05.440 - 00:19:08.470` | read only file or a read write file |
| `00:19:08.480 - 00:19:11.430` | and in that case um in either of those |
| `00:19:11.440 - 00:19:13.750` | cases the readable flag should be set to |
| `00:19:13.760 - 00:19:16.230` | true so that's what we're doing here |
| `00:19:16.240 - 00:19:17.830` | and then we look at the |
| `00:19:17.840 - 00:19:19.350` | write only bit |
| `00:19:19.360 - 00:19:20.710` | if it's set |
| `00:19:20.720 - 00:19:23.590` | or if the read write bit is set then we |
| `00:19:23.600 - 00:19:26.230` | are allowed to write to this file so we |
| `00:19:26.240 - 00:19:27.430` | can set the |
| `00:19:27.440 - 00:19:30.549` | writable flag to true |
| `00:19:30.559 - 00:19:33.510` | and finally we look at the truncate bit |
| `00:19:33.520 - 00:19:36.549` | if the truncate bit is set in the flags |
| `00:19:36.559 - 00:19:37.669` | value |
| `00:19:37.679 - 00:19:40.789` | and we're looking at a regular file then |
| `00:19:40.799 - 00:19:43.510` | we can call i truncate and that will |
| `00:19:43.520 - 00:19:46.150` | truncate the size of the file to zero |
| `00:19:46.160 - 00:19:49.350` | and release any space it's occupying |
| `00:19:49.360 - 00:19:52.070` | now at this point we've got the file the |
| `00:19:52.080 - 00:19:53.990` | ip the inode |
| `00:19:54.000 - 00:19:56.549` | locked and we've got the reference count |
| `00:19:56.559 - 00:19:59.830` | uh incremented so we need to unlock the |
| `00:19:59.840 - 00:20:02.390` | inode but we will keep the reference |
| `00:20:02.400 - 00:20:04.630` | count incremented okay and return the |
| `00:20:04.640 - 00:20:06.390` | file descriptor |
| `00:20:06.400 - 00:20:07.830` | uh we keep the reference count |
| `00:20:07.840 - 00:20:09.750` | incremented because we now have a |
| `00:20:09.760 - 00:20:12.310` | pointer we stored a pointer to the file |
| `00:20:12.320 - 00:20:13.510` | structure |
| `00:20:13.520 - 00:20:14.630` | in |
| `00:20:14.640 - 00:20:18.149` | the uh inode and we stored a pointer to |
| `00:20:18.159 - 00:20:21.909` | the uh sorry we stored a pointer to the |
| `00:20:21.919 - 00:20:25.350` | file structure in this open file array |
| `00:20:25.360 - 00:20:28.149` | and we stored a pointer to the inode in |
| `00:20:28.159 - 00:20:30.870` | the ip field so we have we do have a |
| `00:20:30.880 - 00:20:33.190` | pointer to the inode so we need to keep |
| `00:20:33.200 - 00:20:36.310` | the inode's reference count |
| `00:20:36.320 - 00:20:39.110` | incremented to indicate this pointer has |
| `00:20:39.120 - 00:20:41.669` | been added to the inode |
| `00:20:41.679 - 00:20:44.470` | and so i believe that ends the |
| `00:20:44.480 - 00:20:47.270` | code for the open system file |
| `00:20:47.280 - 00:20:49.590` | for the open |
| `00:20:49.600 - 00:20:51.909` | so i believe that ends the code for the |
| `00:20:51.919 - 00:20:55.190` | open system call |
| `00:20:55.200 - 00:20:57.190` | next let's take a look at the pipe |
| `00:20:57.200 - 00:20:58.789` | system call |
| `00:20:58.799 - 00:21:02.549` | in unix systems including xv6 the pipe |
| `00:21:02.559 - 00:21:04.870` | system call is passed a pointer to an |
| `00:21:04.880 - 00:21:07.669` | array and that array has two elements |
| `00:21:07.679 - 00:21:10.549` | it will create the pipe and it will |
| `00:21:10.559 - 00:21:13.029` | return the file descriptors for the read |
| `00:21:13.039 - 00:21:16.549` | and the right end in this array |
| `00:21:16.559 - 00:21:18.230` | it will return zero if everything's okay |
| `00:21:18.240 - 00:21:20.470` | at minus one if there's a problem so |
| `00:21:20.480 - 00:21:23.510` | let's uh look at what's going on here |
| `00:21:23.520 - 00:21:25.669` | here i'm showing a proc structure for |
| `00:21:25.679 - 00:21:28.230` | some process and it's got an open file |
| `00:21:28.240 - 00:21:30.549` | array that has some elements already |
| `00:21:30.559 - 00:21:31.669` | filled in |
| `00:21:31.679 - 00:21:34.630` | and the system call will allocate a pipe |
| `00:21:34.640 - 00:21:37.190` | structure as well as two file structures |
| `00:21:37.200 - 00:21:39.350` | shown here and here |
| `00:21:39.360 - 00:21:41.909` | and then it will fill in |
| `00:21:41.919 - 00:21:44.630` | pointers from the open file array to |
| `00:21:44.640 - 00:21:46.630` | these file structures |
| `00:21:46.640 - 00:21:49.510` | so here we've created a pipe structure |
| `00:21:49.520 - 00:21:51.750` | and this is the read end this file |
| `00:21:51.760 - 00:21:54.070` | structure represents the read end and |
| `00:21:54.080 - 00:21:56.390` | the readable flag is set to true and the |
| `00:21:56.400 - 00:21:58.549` | writable flag is set to false |
| `00:21:58.559 - 00:21:59.990` | and you can tell that this is the right |
| `00:22:00.000 - 00:22:02.070` | end because the writable flag is true |
| `00:22:02.080 - 00:22:04.630` | and the readable flag is set to false |
| `00:22:04.640 - 00:22:06.950` | but both contain pointers to the pipe |
| `00:22:06.960 - 00:22:08.789` | structure and the reference count for |
| `00:22:08.799 - 00:22:11.669` | both is one and then what this system |
| `00:22:11.679 - 00:22:14.789` | call will do is it will search the open |
| `00:22:14.799 - 00:22:17.830` | file array and find the first unused |
| `00:22:17.840 - 00:22:20.310` | slot and make that point to |
| `00:22:20.320 - 00:22:21.669` | the |
| `00:22:21.679 - 00:22:22.950` | read end |
| `00:22:22.960 - 00:22:25.909` | and it will find the next unused slot |
| `00:22:25.919 - 00:22:27.909` | and make that point to |
| `00:22:27.919 - 00:22:31.190` | the right end and then it will save in |
| `00:22:31.200 - 00:22:32.710` | this array |
| `00:22:32.720 - 00:22:34.870` | the file descriptor for the read end |
| `00:22:34.880 - 00:22:37.990` | well that was two so we'll save two here |
| `00:22:38.000 - 00:22:40.789` | and then it will save in the second slot |
| `00:22:40.799 - 00:22:42.789` | of this array the file descriptor it |
| `00:22:42.799 - 00:22:47.029` | used for the right end which was five |
| `00:22:47.039 - 00:22:48.950` | this array is a |
| `00:22:48.960 - 00:22:51.430` | fixed size array of two elements each |
| `00:22:51.440 - 00:22:53.270` | one is big enough to hold |
| `00:22:53.280 - 00:22:55.430` | an integer so the total size of this |
| `00:22:55.440 - 00:23:00.870` | array is eight bytes at least in xv6 |
| `00:23:00.880 - 00:23:03.430` | next let's look at the code for cis pipe |
| `00:23:03.440 - 00:23:06.310` | which implements the pipe system call |
| `00:23:06.320 - 00:23:08.310` | there's no activity on the disk so we |
| `00:23:08.320 - 00:23:10.310` | don't see any transactions in this |
| `00:23:10.320 - 00:23:12.710` | particular function |
| `00:23:12.720 - 00:23:14.789` | we're going to begin by |
| `00:23:14.799 - 00:23:17.669` | calling arg adder to get our first |
| `00:23:17.679 - 00:23:19.669` | argument that's the |
| `00:23:19.679 - 00:23:22.710` | virtual address of this fd array |
| `00:23:22.720 - 00:23:23.430` | so |
| `00:23:23.440 - 00:23:25.669` | we are going to store that virtual |
| `00:23:25.679 - 00:23:28.789` | address into this local variable fb |
| `00:23:28.799 - 00:23:30.070` | array |
| `00:23:30.080 - 00:23:31.909` | and if there's any problem we return |
| `00:23:31.919 - 00:23:33.590` | immediately |
| `00:23:33.600 - 00:23:36.549` | next we call pipealoc and what pipe alec |
| `00:23:36.559 - 00:23:39.029` | is going to do is allocate this pipe |
| `00:23:39.039 - 00:23:41.350` | structure as well as these two file |
| `00:23:41.360 - 00:23:45.909` | structures and set this thing up here |
| `00:23:45.919 - 00:23:46.950` | and |
| `00:23:46.960 - 00:23:50.789` | it will return pointers to the read end |
| `00:23:50.799 - 00:23:52.870` | and the right end that is the pointers |
| `00:23:52.880 - 00:23:54.549` | to the uh |
| `00:23:54.559 - 00:23:56.950` | file structures for the read end and the |
| `00:23:56.960 - 00:23:59.269` | right end by setting these variables |
| `00:23:59.279 - 00:24:00.070` | here |
| `00:24:00.080 - 00:24:02.549` | there's any problem that returns -1 and |
| `00:24:02.559 - 00:24:04.470` | we return immediately |
| `00:24:04.480 - 00:24:07.029` | now we have got these two these three |
| `00:24:07.039 - 00:24:10.789` | structures allocated and we're ready to |
| `00:24:10.799 - 00:24:13.909` | fill in these pointers here |
| `00:24:13.919 - 00:24:17.029` | so we're going to call fd alec to find a |
| `00:24:17.039 - 00:24:19.750` | slot in the open file array and set it |
| `00:24:19.760 - 00:24:22.390` | to point to the read |
| `00:24:22.400 - 00:24:24.630` | file structure and then we're going to |
| `00:24:24.640 - 00:24:28.390` | call fdalic again to find another slot |
| `00:24:28.400 - 00:24:31.350` | in the open file array and |
| `00:24:31.360 - 00:24:33.190` | fill it in to point to the file |
| `00:24:33.200 - 00:24:35.830` | structure that represents the right end |
| `00:24:35.840 - 00:24:38.070` | and then we're going to save these two |
| `00:24:38.080 - 00:24:42.310` | file descriptors in ft 0 and fd1 |
| `00:24:42.320 - 00:24:44.710` | now i have no idea why we're setting fd |
| `00:24:44.720 - 00:24:48.230` | 0 to -1 here because whatever happens |
| `00:24:48.240 - 00:24:50.070` | this the first thing we do is call |
| `00:24:50.080 - 00:24:51.510` | fbalik and it's going to return |
| `00:24:51.520 - 00:24:54.870` | something and modify fd0 but |
| `00:24:54.880 - 00:24:56.390` | in any case |
| `00:24:56.400 - 00:24:59.029` | here if anything goes wrong that is |
| `00:24:59.039 - 00:25:01.909` | either of these returns a negative one |
| `00:25:01.919 - 00:25:05.350` | then we've got a problem and we need to |
| `00:25:05.360 - 00:25:07.190` | deal with it so |
| `00:25:07.200 - 00:25:09.909` | if we've got a slot for |
| `00:25:09.919 - 00:25:11.510` | the read end |
| `00:25:11.520 - 00:25:14.310` | but failed on this on the right end then |
| `00:25:14.320 - 00:25:16.630` | fd 0 will be something |
| `00:25:16.640 - 00:25:19.590` | greater than or equal to 0 and so we |
| `00:25:19.600 - 00:25:21.750` | need to |
| `00:25:21.760 - 00:25:24.470` | eliminate that we need to restore it to |
| `00:25:24.480 - 00:25:27.350` | be no so that's what we do here but in |
| `00:25:27.360 - 00:25:29.990` | either case we close both file |
| `00:25:30.000 - 00:25:31.190` | structures |
| `00:25:31.200 - 00:25:33.750` | and remember that with the pipes when we |
| `00:25:33.760 - 00:25:34.710` | close |
| `00:25:34.720 - 00:25:37.190` | a an end of the pipe it will eliminate |
| `00:25:37.200 - 00:25:39.590` | that file structure and if it's the last |
| `00:25:39.600 - 00:25:43.110` | pointer to that pipe structure it will |
| `00:25:43.120 - 00:25:45.990` | free up the pipe structure itself so on |
| `00:25:46.000 - 00:25:48.630` | the second call to file close we close |
| `00:25:48.640 - 00:25:49.510` | the |
| `00:25:49.520 - 00:25:52.630` | last end of the pipe and that will free |
| `00:25:52.640 - 00:25:54.390` | the pipe structure as well then we |
| `00:25:54.400 - 00:25:56.230` | return minus -1 |
| `00:25:56.240 - 00:25:59.269` | but assuming that we were able to store |
| `00:25:59.279 - 00:26:00.549` | these pointers |
| `00:26:00.559 - 00:26:01.510` | into |
| `00:26:01.520 - 00:26:02.390` | the |
| `00:26:02.400 - 00:26:07.269` | open file array um we now have fd0 being |
| `00:26:07.279 - 00:26:10.549` | 2 and fd 1 being five |
| `00:26:10.559 - 00:26:12.950` | so we need to store those into |
| `00:26:12.960 - 00:26:16.070` | the fd array in the user space |
| `00:26:16.080 - 00:26:17.190` | so |
| `00:26:17.200 - 00:26:19.430` | this value right here is the virtual |
| `00:26:19.440 - 00:26:20.549` | address |
| `00:26:20.559 - 00:26:22.870` | of this fd array |
| `00:26:22.880 - 00:26:26.390` | and page table indicates the virtual |
| `00:26:26.400 - 00:26:29.190` | address space so we're using copy out to |
| `00:26:29.200 - 00:26:30.470` | copy |
| `00:26:30.480 - 00:26:31.510` | from |
| `00:26:31.520 - 00:26:33.590` | the kernel space namely the value that's |
| `00:26:33.600 - 00:26:35.590` | stored at fd0 |
| `00:26:35.600 - 00:26:37.909` | and the size of fd0 well that's an |
| `00:26:37.919 - 00:26:40.470` | integer so that would be four bytes |
| `00:26:40.480 - 00:26:42.470` | and so we're going to copy four bytes |
| `00:26:42.480 - 00:26:45.669` | into the fd array at offset zero |
| `00:26:45.679 - 00:26:48.070` | okay and then we're going to copy |
| `00:26:48.080 - 00:26:49.750` | the value of |
| `00:26:49.760 - 00:26:51.430` | fd1 |
| `00:26:51.440 - 00:26:52.470` | okay |
| `00:26:52.480 - 00:26:56.310` | and uh we're gonna copy again four bytes |
| `00:26:56.320 - 00:26:58.630` | and we're gonna copy it to |
| `00:26:58.640 - 00:27:01.029` | the virtual address space and this time |
| `00:27:01.039 - 00:27:03.590` | we're going to copy it into offset four |
| `00:27:03.600 - 00:27:05.909` | that is the beginning of fd array plus |
| `00:27:05.919 - 00:27:08.870` | the size of the first element so |
| `00:27:08.880 - 00:27:10.870` | the beginning of fd array plus the size |
| `00:27:10.880 - 00:27:13.830` | of the first element will be the second |
| `00:27:13.840 - 00:27:15.350` | the address of the second element in the |
| `00:27:15.360 - 00:27:18.389` | array so here we're storing |
| `00:27:18.399 - 00:27:19.269` | the |
| `00:27:19.279 - 00:27:21.430` | two and the five |
| `00:27:21.440 - 00:27:23.909` | the two file descriptors that is into |
| `00:27:23.919 - 00:27:26.710` | this fd array in user space |
| `00:27:26.720 - 00:27:29.830` | and uh if there's anything wrong here |
| `00:27:29.840 - 00:27:32.549` | well we need to |
| `00:27:32.559 - 00:27:35.990` | return those file descriptors to null so |
| `00:27:36.000 - 00:27:39.750` | we store a 0 in this element here and a |
| `00:27:39.760 - 00:27:42.070` | 0 in this element there |
| `00:27:42.080 - 00:27:44.149` | and then we close the |
| `00:27:44.159 - 00:27:46.549` | read file descriptor and the write file |
| `00:27:46.559 - 00:27:48.389` | descriptor as before |
| `00:27:48.399 - 00:27:50.549` | and return minus one but if these two |
| `00:27:50.559 - 00:27:52.630` | copy outs worked fine |
| `00:27:52.640 - 00:27:54.070` | then |
| `00:27:54.080 - 00:27:56.549` | we skip that and return zero to indicate |
| `00:27:56.559 - 00:27:57.990` | success |
| `00:27:58.000 - 00:28:00.389` | okay the final |
| `00:28:00.399 - 00:28:02.870` | file uh the final system call that we |
| `00:28:02.880 - 00:28:05.269` | haven't talked about is the exec and |
| `00:28:05.279 - 00:28:07.110` | that's pretty involved so i'm gonna do |
| `00:28:07.120 - 00:28:09.350` | that in the next video that's all for |
| `00:28:09.360 - 00:28:11.190` | this video i'll see you in the next |
| `00:28:11.200 - 00:28:14.200` | video |
