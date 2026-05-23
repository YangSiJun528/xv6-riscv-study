# xv6 Kernel-36: File-Related System Calls-Part 1

| Field | Value |
| --- | --- |
| Video ID | `Mq-3HA3FimI` |
| URL | <https://www.youtube.com/watch?v=Mq-3HA3FimI> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.199 - 00:00:02.790` | this video is part of a series on the |
| `00:00:02.800 - 00:00:05.349` | xv6 operating system kernel |
| `00:00:05.359 - 00:00:07.990` | in this and the next video i'll be going |
| `00:00:08.000 - 00:00:10.470` | over the code in the functions that |
| `00:00:10.480 - 00:00:13.749` | handle the file related system calls |
| `00:00:13.759 - 00:00:15.270` | these functions are coming from |
| `00:00:15.280 - 00:00:17.109` | sysfile.c |
| `00:00:17.119 - 00:00:19.910` | and i will also cover file read and file |
| `00:00:19.920 - 00:00:23.109` | write which come from file.c |
| `00:00:23.119 - 00:00:24.870` | there's quite a bit of code here so i'm |
| `00:00:24.880 - 00:00:27.750` | breaking it into two different videos |
| `00:00:27.760 - 00:00:30.390` | this is part one and in this video i'll |
| `00:00:30.400 - 00:00:34.310` | be covering the following system calls |
| `00:00:34.320 - 00:00:39.030` | read write close link unlink f-stat |
| `00:00:39.040 - 00:00:41.350` | change directory and dupe |
| `00:00:41.360 - 00:00:43.990` | in part two i will cover the open system |
| `00:00:44.000 - 00:00:44.869` | call |
| `00:00:44.879 - 00:00:48.389` | make node make directory and pipe |
| `00:00:48.399 - 00:00:50.630` | the exec system call is a little bit |
| `00:00:50.640 - 00:00:53.110` | complicated so i'll be covering that in |
| `00:00:53.120 - 00:00:54.790` | a subsequent video |
| `00:00:54.800 - 00:00:58.310` | ok let's get started |
| `00:00:58.320 - 00:01:00.310` | so let's take a look at the read and the |
| `00:01:00.320 - 00:01:01.830` | write system call |
| `00:01:01.840 - 00:01:04.390` | each of these takes three arguments |
| `00:01:04.400 - 00:01:06.469` | and the user mode code will begin by |
| `00:01:06.479 - 00:01:08.950` | placing these arguments into registers |
| `00:01:08.960 - 00:01:12.390` | a0 a1 a2 and so on |
| `00:01:12.400 - 00:01:14.469` | it will also place a code number in |
| `00:01:14.479 - 00:01:18.230` | register a7 and that code number is used |
| `00:01:18.240 - 00:01:20.310` | by the kernel to determine which system |
| `00:01:20.320 - 00:01:23.429` | call is wanted |
| `00:01:23.439 - 00:01:25.670` | then the user mode code will execute the |
| `00:01:25.680 - 00:01:27.590` | system call instruction |
| `00:01:27.600 - 00:01:30.230` | in risk 5 that instruction is named |
| `00:01:30.240 - 00:01:32.469` | e-call and at that point the kernel will |
| `00:01:32.479 - 00:01:36.149` | get control and it will begin by saving |
| `00:01:36.159 - 00:01:39.429` | the user registers someplace |
| `00:01:39.439 - 00:01:40.950` | then it will look at |
| `00:01:40.960 - 00:01:43.990` | the value in a7 and determine which |
| `00:01:44.000 - 00:01:46.149` | system call is involved |
| `00:01:46.159 - 00:01:48.789` | and if it's a read system call the |
| `00:01:48.799 - 00:01:50.870` | kernel will then invoke |
| `00:01:50.880 - 00:01:53.429` | the sys read function there's one of |
| `00:01:53.439 - 00:01:55.670` | these cis functions |
| `00:01:55.680 - 00:01:59.270` | for every system call |
| `00:01:59.280 - 00:02:01.510` | the cis read will begin by |
| `00:02:01.520 - 00:02:04.149` | locating the arguments and grabbing them |
| `00:02:04.159 - 00:02:06.870` | from wherever they were stored initially |
| `00:02:06.880 - 00:02:09.910` | so we have these functions rgfd argant |
| `00:02:09.920 - 00:02:11.589` | and arg address |
| `00:02:11.599 - 00:02:14.150` | and each is passed a an argument the |
| `00:02:14.160 - 00:02:16.869` | first argument tells which argument |
| `00:02:16.879 - 00:02:18.150` | we're interested in |
| `00:02:18.160 - 00:02:18.949` | so |
| `00:02:18.959 - 00:02:20.470` | a0 |
| `00:02:20.480 - 00:02:23.430` | a1 and a2 these things aren't done in |
| `00:02:23.440 - 00:02:24.630` | order really |
| `00:02:24.640 - 00:02:26.390` | uh so this is the first argument the |
| `00:02:26.400 - 00:02:27.990` | second argument and the third third |
| `00:02:28.000 - 00:02:29.190` | argument |
| `00:02:29.200 - 00:02:32.150` | the argant function will go to register |
| `00:02:32.160 - 00:02:34.070` | a2 in this case |
| `00:02:34.080 - 00:02:37.190` | grab a 32-bit value and store it well |
| `00:02:37.200 - 00:02:38.710` | here's the address of where to store it |
| `00:02:38.720 - 00:02:40.790` | and that's the local variable in |
| `00:02:40.800 - 00:02:42.630` | number of bytes |
| `00:02:42.640 - 00:02:45.270` | the arg address function will go to |
| `00:02:45.280 - 00:02:48.470` | register where register a1 is stored and |
| `00:02:48.480 - 00:02:51.589` | grab a 64-bit value and store it in |
| `00:02:51.599 - 00:02:53.589` | local variable p |
| `00:02:53.599 - 00:02:55.990` | and finally this arg fd |
| `00:02:56.000 - 00:02:57.030` | will |
| `00:02:57.040 - 00:02:59.750` | obtain the value that was in a0 at the |
| `00:02:59.760 - 00:03:01.670` | time of the system call |
| `00:03:01.680 - 00:03:04.790` | and interpret it as a file descriptor it |
| `00:03:04.800 - 00:03:06.229` | will check to make sure it's a legal |
| `00:03:06.239 - 00:03:07.990` | file descriptor and return minus one if |
| `00:03:08.000 - 00:03:09.270` | there's a problem |
| `00:03:09.280 - 00:03:11.589` | and then it will get a pointer to the |
| `00:03:11.599 - 00:03:14.070` | file structure and it will store that |
| `00:03:14.080 - 00:03:16.550` | pointer in f |
| `00:03:16.560 - 00:03:18.470` | it can also uh obtain the file |
| `00:03:18.480 - 00:03:20.790` | descriptor number itself uh but that's |
| `00:03:20.800 - 00:03:22.710` | not needed so this address here is just |
| `00:03:22.720 - 00:03:23.750` | null |
| `00:03:23.760 - 00:03:24.949` | if there's a problem with the arguments |
| `00:03:24.959 - 00:03:27.110` | we return minus one but otherwise we're |
| `00:03:27.120 - 00:03:30.070` | going to call this function file read to |
| `00:03:30.080 - 00:03:31.509` | actually do the work passing it a |
| `00:03:31.519 - 00:03:33.430` | pointer to the file structure |
| `00:03:33.440 - 00:03:35.910` | as well as the virtual address and the |
| `00:03:35.920 - 00:03:37.350` | number of bytes |
| `00:03:37.360 - 00:03:38.470` | and file |
| `00:03:38.480 - 00:03:41.190` | file read will return the number of |
| `00:03:41.200 - 00:03:43.670` | bytes transferred or minus one if |
| `00:03:43.680 - 00:03:45.350` | there's a problem |
| `00:03:45.360 - 00:03:47.509` | so before i look at file read i want to |
| `00:03:47.519 - 00:03:49.910` | just look at the |
| `00:03:49.920 - 00:03:52.229` | cis write function which is exactly |
| `00:03:52.239 - 00:03:53.270` | identical |
| `00:03:53.280 - 00:03:55.589` | right it gets the arguments |
| `00:03:55.599 - 00:03:56.789` | here |
| `00:03:56.799 - 00:03:59.750` | except it calls file write instead of |
| `00:03:59.760 - 00:04:01.110` | file read |
| `00:04:01.120 - 00:04:05.030` | okay so let's take a look at file read |
| `00:04:05.040 - 00:04:08.309` | the function file read is coming from |
| `00:04:08.319 - 00:04:10.550` | file.c |
| `00:04:10.560 - 00:04:11.429` | and |
| `00:04:11.439 - 00:04:12.949` | as i said it's passed a pointer to the |
| `00:04:12.959 - 00:04:14.710` | file structure as well as the virtual |
| `00:04:14.720 - 00:04:16.870` | address and the number of bytes |
| `00:04:16.880 - 00:04:19.590` | so it checks that file structure and |
| `00:04:19.600 - 00:04:21.749` | looks at the readable flag to make sure |
| `00:04:21.759 - 00:04:23.350` | that this |
| `00:04:23.360 - 00:04:25.350` | file descriptor describes a file that is |
| `00:04:25.360 - 00:04:27.510` | actually readable and if not there's a |
| `00:04:27.520 - 00:04:29.909` | problem it returns -1 |
| `00:04:29.919 - 00:04:32.710` | and that would then get returned to the |
| `00:04:32.720 - 00:04:35.430` | user mode code |
| `00:04:35.440 - 00:04:38.070` | otherwise it looks at the type if it's a |
| `00:04:38.080 - 00:04:40.790` | pipe device or inode |
| `00:04:40.800 - 00:04:42.550` | if it's a pipe it's going to call the |
| `00:04:42.560 - 00:04:45.030` | pipe read function which i covered in an |
| `00:04:45.040 - 00:04:46.310` | earlier video |
| `00:04:46.320 - 00:04:47.909` | and it's passed a pointer to the pipe |
| `00:04:47.919 - 00:04:49.510` | structure |
| `00:04:49.520 - 00:04:52.629` | and it will perform the read and store |
| `00:04:52.639 - 00:04:55.510` | the bytes in this virtual address |
| `00:04:55.520 - 00:04:57.749` | and return the number of bytes |
| `00:04:57.759 - 00:04:59.430` | successfully read from the pipe or |
| `00:04:59.440 - 00:05:01.110` | possibly minus one |
| `00:05:01.120 - 00:05:03.909` | and then that will get returned |
| `00:05:03.919 - 00:05:04.870` | if the |
| `00:05:04.880 - 00:05:08.469` | file structure is a device then we look |
| `00:05:08.479 - 00:05:10.550` | at the major number in the file |
| `00:05:10.560 - 00:05:12.950` | structure that's the device number and |
| `00:05:12.960 - 00:05:15.350` | we make sure it's uh legal |
| `00:05:15.360 - 00:05:17.510` | and we also look into this device switch |
| `00:05:17.520 - 00:05:18.550` | array |
| `00:05:18.560 - 00:05:20.870` | we use it as an index into that array |
| `00:05:20.880 - 00:05:23.430` | and remember that that array contains a |
| `00:05:23.440 - 00:05:25.590` | structure that has two fields both of |
| `00:05:25.600 - 00:05:27.749` | those point two functions |
| `00:05:27.759 - 00:05:29.270` | so we're gonna be using the read |
| `00:05:29.280 - 00:05:31.029` | function so we look at that field make |
| `00:05:31.039 - 00:05:33.029` | sure it's not null here |
| `00:05:33.039 - 00:05:35.430` | if there are any problems we return -1 |
| `00:05:35.440 - 00:05:37.670` | but otherwise |
| `00:05:37.680 - 00:05:40.150` | we use the major number as an index into |
| `00:05:40.160 - 00:05:41.189` | the array |
| `00:05:41.199 - 00:05:42.790` | get the read |
| `00:05:42.800 - 00:05:44.390` | value which is a pointer to some |
| `00:05:44.400 - 00:05:46.469` | function and then we invoke that |
| `00:05:46.479 - 00:05:48.790` | function so |
| `00:05:48.800 - 00:05:51.990` | that will be the console read operation |
| `00:05:52.000 - 00:05:53.830` | we passed a virtual address and a 1 to |
| `00:05:53.840 - 00:05:55.670` | indicate that this is a virtual and not |
| `00:05:55.680 - 00:05:57.510` | physical address as well with the number |
| `00:05:57.520 - 00:05:59.189` | of bytes |
| `00:05:59.199 - 00:06:02.309` | finally if it's an inode then we will |
| `00:06:02.319 - 00:06:04.710` | use the read i function which i've also |
| `00:06:04.720 - 00:06:05.670` | covered |
| `00:06:05.680 - 00:06:08.469` | that's passed a pointer to the inode as |
| `00:06:08.479 - 00:06:11.029` | well as a address and a flag which |
| `00:06:11.039 - 00:06:12.230` | indicates that this happens to be a |
| `00:06:12.240 - 00:06:13.749` | virtual address |
| `00:06:13.759 - 00:06:16.230` | as well as where in the file we want to |
| `00:06:16.240 - 00:06:18.309` | begin the reading and so we go to the |
| `00:06:18.319 - 00:06:20.870` | file sorry we go to the i |
| `00:06:20.880 - 00:06:23.430` | sorry we go to the file structure |
| `00:06:23.440 - 00:06:25.830` | and we get the current offset and the |
| `00:06:25.840 - 00:06:28.550` | number of bytes if anything goes wrong |
| `00:06:28.560 - 00:06:30.710` | we return a minus one but otherwise we |
| `00:06:30.720 - 00:06:33.029` | return the number of bytes red and if we |
| `00:06:33.039 - 00:06:35.510` | read any bytes this was positive then we |
| `00:06:35.520 - 00:06:37.990` | need to increment the offset that's |
| `00:06:38.000 - 00:06:39.430` | stored in the file structure and we do |
| `00:06:39.440 - 00:06:40.790` | that here |
| `00:06:40.800 - 00:06:43.830` | now read i expects the |
| `00:06:43.840 - 00:06:46.230` | inode to be locked so we need to lock it |
| `00:06:46.240 - 00:06:47.909` | first and then unlock it after we're |
| `00:06:47.919 - 00:06:49.430` | done with it |
| `00:06:49.440 - 00:06:52.629` | if it's anything else then we panic |
| `00:06:52.639 - 00:06:54.070` | but in any case |
| `00:06:54.080 - 00:06:56.469` | we return our number of bytes that we've |
| `00:06:56.479 - 00:06:59.270` | read |
| `00:06:59.280 - 00:07:01.189` | now i want to take a look at the file |
| `00:07:01.199 - 00:07:02.790` | write function |
| `00:07:02.800 - 00:07:05.189` | it's used for the write system call and |
| `00:07:05.199 - 00:07:06.550` | this function is very similar to the |
| `00:07:06.560 - 00:07:08.629` | file read function that we just looked |
| `00:07:08.639 - 00:07:10.790` | at it's passed a pointer to a file |
| `00:07:10.800 - 00:07:12.469` | structure as well as the virtual address |
| `00:07:12.479 - 00:07:14.070` | and the number of bytes |
| `00:07:14.080 - 00:07:15.749` | begins by checking the file structure to |
| `00:07:15.759 - 00:07:17.909` | make sure that this file can be written |
| `00:07:17.919 - 00:07:20.950` | to and returns -1 if there's a problem |
| `00:07:20.960 - 00:07:22.390` | and then it looks at the type to |
| `00:07:22.400 - 00:07:24.950` | determine whether this file is a pipe a |
| `00:07:24.960 - 00:07:27.510` | device or |
| `00:07:27.520 - 00:07:30.550` | a directory or regular file |
| `00:07:30.560 - 00:07:33.510` | and if it's a pipe it calls pipe right |
| `00:07:33.520 - 00:07:36.309` | previously we called pipe read here and |
| `00:07:36.319 - 00:07:37.990` | that will return the number of bytes |
| `00:07:38.000 - 00:07:39.909` | transferred or minus one if there's a |
| `00:07:39.919 - 00:07:41.510` | problem and then at the bottom of the |
| `00:07:41.520 - 00:07:45.670` | state but we return that value |
| `00:07:45.680 - 00:07:47.350` | if it's a device |
| `00:07:47.360 - 00:07:50.790` | similarly to file read we check the |
| `00:07:50.800 - 00:07:52.950` | major number to make sure it's legal we |
| `00:07:52.960 - 00:07:55.990` | look into this device switch array |
| `00:07:56.000 - 00:07:58.390` | using that as an index and that gives us |
| `00:07:58.400 - 00:08:00.550` | a structure that has these two fields |
| `00:08:00.560 - 00:08:03.110` | read and write we make sure the right |
| `00:08:03.120 - 00:08:04.710` | field is not null |
| `00:08:04.720 - 00:08:06.950` | and if everything's okay then we're |
| `00:08:06.960 - 00:08:09.670` | going to use that value okay so we index |
| `00:08:09.680 - 00:08:12.390` | into the array and get the right field |
| `00:08:12.400 - 00:08:14.230` | that's a pointer to a function we're |
| `00:08:14.240 - 00:08:15.029` | going to |
| `00:08:15.039 - 00:08:17.430` | call that function so we're passing it |
| `00:08:17.440 - 00:08:19.510` | an address an indication that that's a |
| `00:08:19.520 - 00:08:21.990` | virtual address and the number of bytes |
| `00:08:22.000 - 00:08:23.189` | and again |
| `00:08:23.199 - 00:08:25.990` | returns the number of bytes transferred |
| `00:08:26.000 - 00:08:28.150` | and we return that |
| `00:08:28.160 - 00:08:30.629` | now in the third case |
| `00:08:30.639 - 00:08:33.430` | it's an fdi node for the file type which |
| `00:08:33.440 - 00:08:35.589` | means it's a directory file or regular |
| `00:08:35.599 - 00:08:38.230` | file |
| `00:08:38.240 - 00:08:40.790` | we might have a very large right and |
| `00:08:40.800 - 00:08:41.750` | these rights are going to have to go |
| `00:08:41.760 - 00:08:44.550` | into transactions previously the read |
| `00:08:44.560 - 00:08:46.790` | did not go into a transaction because if |
| `00:08:46.800 - 00:08:48.870` | it's uh interrupted by a system crash |
| `00:08:48.880 - 00:08:51.910` | for example it's no no problem the disk |
| `00:08:51.920 - 00:08:53.590` | is still consistent but here we are |
| `00:08:53.600 - 00:08:56.150` | doing a write operation so we need to |
| `00:08:56.160 - 00:08:58.870` | include all of the writing in a |
| `00:08:58.880 - 00:09:00.870` | transaction so we'll see a begin up and |
| `00:09:00.880 - 00:09:02.949` | an end down here |
| `00:09:02.959 - 00:09:05.590` | that transaction is limited it must be |
| `00:09:05.600 - 00:09:06.630` | limited |
| `00:09:06.640 - 00:09:08.710` | to a certain number of blocks we can't |
| `00:09:08.720 - 00:09:10.790` | write too much otherwise we might fill |
| `00:09:10.800 - 00:09:12.070` | up the |
| `00:09:12.080 - 00:09:15.829` | log file and that could cause problems |
| `00:09:15.839 - 00:09:17.910` | so we need to break the right into a |
| `00:09:17.920 - 00:09:19.829` | series of chunks |
| `00:09:19.839 - 00:09:22.550` | and the first thing we do is we compute |
| `00:09:22.560 - 00:09:25.750` | the size the maximum size of any chunk |
| `00:09:25.760 - 00:09:28.790` | so that computation happens here |
| `00:09:28.800 - 00:09:30.230` | so let's just read this write a few |
| `00:09:30.240 - 00:09:31.829` | blocks at a time to avoid exceeding the |
| `00:09:31.839 - 00:09:34.630` | maximum log transaction size include and |
| `00:09:34.640 - 00:09:36.550` | we're going to be writing the inode |
| `00:09:36.560 - 00:09:39.350` | possibly up writing an indirect block |
| `00:09:39.360 - 00:09:41.750` | and the actual blocks that are involved |
| `00:09:41.760 - 00:09:44.230` | and the right uh the blocks the series |
| `00:09:44.240 - 00:09:46.389` | of bytes that we're writing may extend |
| `00:09:46.399 - 00:09:48.710` | over a block boundary so we're going to |
| `00:09:48.720 - 00:09:50.630` | add two for that as well |
| `00:09:50.640 - 00:09:51.910` | and |
| `00:09:51.920 - 00:09:54.310` | i'm not quite a 100 comfortable with |
| `00:09:54.320 - 00:09:56.230` | this computation here |
| `00:09:56.240 - 00:09:59.190` | but it is what it is and uh don't want |
| `00:09:59.200 - 00:10:01.350` | to bog down on that i do want to say |
| `00:10:01.360 - 00:10:03.350` | this really the comment says this really |
| `00:10:03.360 - 00:10:05.829` | belongs lower down since right eye might |
| `00:10:05.839 - 00:10:08.630` | be writing to a device like the console |
| `00:10:08.640 - 00:10:10.790` | but that's not really what right eye |
| `00:10:10.800 - 00:10:13.350` | does right eye only writes to |
| `00:10:13.360 - 00:10:15.829` | files on the disk |
| `00:10:15.839 - 00:10:19.509` | so we compute the maximum chunk size and |
| `00:10:19.519 - 00:10:21.590` | in this while loop here |
| `00:10:21.600 - 00:10:24.550` | okay uh what we're going to do is uh |
| `00:10:24.560 - 00:10:28.230` | break the entire |
| `00:10:28.240 - 00:10:30.069` | sequence of bytes that we need to write |
| `00:10:30.079 - 00:10:33.190` | into a number of chunks so n is the |
| `00:10:33.200 - 00:10:35.590` | number of bytes to be written and n one |
| `00:10:35.600 - 00:10:37.190` | will be the size of this particular |
| `00:10:37.200 - 00:10:39.670` | chunk and i will be the number of bytes |
| `00:10:39.680 - 00:10:41.430` | that we've written so far so we're |
| `00:10:41.440 - 00:10:43.509` | starting i out at zero and we just keep |
| `00:10:43.519 - 00:10:46.550` | looping until we have written a full n |
| `00:10:46.560 - 00:10:47.910` | bytes |
| `00:10:47.920 - 00:10:50.389` | and here we see how many bytes we have |
| `00:10:50.399 - 00:10:53.269` | left to write so we compute n1 but we |
| `00:10:53.279 - 00:10:54.949` | don't want to write any more than |
| `00:10:54.959 - 00:10:58.069` | maximum number of bytes so we |
| `00:10:58.079 - 00:11:01.350` | cut in one off at max if it exceeds |
| `00:11:01.360 - 00:11:02.470` | maximum |
| `00:11:02.480 - 00:11:05.030` | and then we're going to write that chunk |
| `00:11:05.040 - 00:11:06.870` | that will go into a transaction so |
| `00:11:06.880 - 00:11:09.110` | here's the begin up and the end up and |
| `00:11:09.120 - 00:11:11.350` | we begin by locking the inode here and |
| `00:11:11.360 - 00:11:13.990` | then unlocking it here and then we call |
| `00:11:14.000 - 00:11:16.630` | right eye to perform this particular |
| `00:11:16.640 - 00:11:19.030` | chunk to write this particular chunk |
| `00:11:19.040 - 00:11:20.710` | here's the inode that we're writing it |
| `00:11:20.720 - 00:11:21.430` | to |
| `00:11:21.440 - 00:11:23.910` | here's the virtual address where we are |
| `00:11:23.920 - 00:11:25.509` | getting it |
| `00:11:25.519 - 00:11:26.949` | and here's a one to indicate that that's |
| `00:11:26.959 - 00:11:28.550` | a virtual address |
| `00:11:28.560 - 00:11:31.030` | and here is the offset within the file |
| `00:11:31.040 - 00:11:33.190` | that we are writing to and the number of |
| `00:11:33.200 - 00:11:35.750` | bytes that this particular write |
| `00:11:35.760 - 00:11:36.710` | will be |
| `00:11:36.720 - 00:11:38.630` | if everything's okay it returns the |
| `00:11:38.640 - 00:11:40.630` | number of bytes written and if that's |
| `00:11:40.640 - 00:11:42.310` | greater than zero we need to increment |
| `00:11:42.320 - 00:11:43.990` | the offset |
| `00:11:44.000 - 00:11:44.949` | and |
| `00:11:44.959 - 00:11:46.710` | then |
| `00:11:46.720 - 00:11:48.949` | we see whether we've got a problem if we |
| `00:11:48.959 - 00:11:53.190` | didn't write all n one bytes then um |
| `00:11:53.200 - 00:11:55.350` | the r the number that we did write will |
| `00:11:55.360 - 00:11:56.470` | be |
| `00:11:56.480 - 00:11:59.509` | less and so we are |
| `00:11:59.519 - 00:12:01.030` | done we need to break out of this loop |
| `00:12:01.040 - 00:12:03.750` | something went wrong um and so we break |
| `00:12:03.760 - 00:12:05.990` | out on the loop otherwise we increment i |
| `00:12:06.000 - 00:12:08.550` | and keep going and then |
| `00:12:08.560 - 00:12:10.389` | we determine whether we wrote all of the |
| `00:12:10.399 - 00:12:13.190` | bytes did we write all n bytes if so we |
| `00:12:13.200 - 00:12:15.269` | return that number otherwise we return |
| `00:12:15.279 - 00:12:21.110` | to minus one |
| `00:12:21.120 - 00:12:22.550` | next let's take a look at the closed |
| `00:12:22.560 - 00:12:24.550` | system call which is passed a file |
| `00:12:24.560 - 00:12:25.990` | descriptor |
| `00:12:26.000 - 00:12:28.389` | so here's the sys function |
| `00:12:28.399 - 00:12:30.870` | for the closed system call and it begins |
| `00:12:30.880 - 00:12:34.150` | by obtaining its argument |
| `00:12:34.160 - 00:12:37.350` | arg fd is used to get the argument in a |
| `00:12:37.360 - 00:12:39.829` | register a0 which is the first argument |
| `00:12:39.839 - 00:12:42.069` | that's expected to be a file descriptor |
| `00:12:42.079 - 00:12:44.069` | and it will store the file descriptor |
| `00:12:44.079 - 00:12:47.110` | integer itself in local variable fd and |
| `00:12:47.120 - 00:12:50.069` | a pointer to the file structure in f |
| `00:12:50.079 - 00:12:51.590` | if everything's okay |
| `00:12:51.600 - 00:12:54.629` | then it will go into the proc structure |
| `00:12:54.639 - 00:12:58.629` | and go into the o file array and no out |
| `00:12:58.639 - 00:13:01.590` | the entry that's indicated by fd and |
| `00:13:01.600 - 00:13:04.150` | then we'll call file close |
| `00:13:04.160 - 00:13:06.870` | to do the work i discussed file close in |
| `00:13:06.880 - 00:13:09.670` | a previous video but let's take a look |
| `00:13:09.680 - 00:13:12.470` | at what's going on here so |
| `00:13:12.480 - 00:13:13.990` | here's a process |
| `00:13:14.000 - 00:13:16.069` | and here's the proc structure for that |
| `00:13:16.079 - 00:13:17.190` | process |
| `00:13:17.200 - 00:13:18.710` | so the first thing sys |
| `00:13:18.720 - 00:13:21.350` | close will do is go to the |
| `00:13:21.360 - 00:13:23.110` | particular entry |
| `00:13:23.120 - 00:13:25.910` | and it will change it to null and then |
| `00:13:25.920 - 00:13:29.110` | it will call file close and file close |
| `00:13:29.120 - 00:13:30.069` | will |
| `00:13:30.079 - 00:13:31.910` | decrement the reference count |
| `00:13:31.920 - 00:13:34.389` | and if the reference count goes to zero |
| `00:13:34.399 - 00:13:36.310` | then it will |
| `00:13:36.320 - 00:13:39.829` | look at the ip and the pipe pointers if |
| `00:13:39.839 - 00:13:41.590` | it points to an inode |
| `00:13:41.600 - 00:13:43.750` | uh then it will go into that inode |
| `00:13:43.760 - 00:13:45.590` | decrement the reference count and if |
| `00:13:45.600 - 00:13:47.350` | that goes to zero then it will close out |
| `00:13:47.360 - 00:13:49.189` | the i node |
| `00:13:49.199 - 00:13:50.949` | if it contains a pipe pointer then it |
| `00:13:50.959 - 00:13:53.750` | will follow that and it will look to see |
| `00:13:53.760 - 00:13:56.389` | whether this is the last pointer if the |
| `00:13:56.399 - 00:13:58.870` | other pointer has been shut down already |
| `00:13:58.880 - 00:14:02.069` | and it will free the pipe structure |
| `00:14:02.079 - 00:14:03.910` | but if the reference count |
| `00:14:03.920 - 00:14:05.910` | doesn't go all the way down to |
| `00:14:05.920 - 00:14:08.629` | zero then files will not do anything |
| `00:14:08.639 - 00:14:09.670` | else |
| `00:14:09.680 - 00:14:11.670` | okay |
| `00:14:11.680 - 00:14:13.910` | now let's take a look at the dupe system |
| `00:14:13.920 - 00:14:14.949` | call |
| `00:14:14.959 - 00:14:17.189` | it's past the file descriptor of a file |
| `00:14:17.199 - 00:14:19.189` | that's already open and it will allocate |
| `00:14:19.199 - 00:14:21.430` | a new file descriptor for this file and |
| `00:14:21.440 - 00:14:23.590` | return that new file descriptor or minus |
| `00:14:23.600 - 00:14:26.870` | one if there are no more slots left |
| `00:14:26.880 - 00:14:29.350` | so uh before we look at the sysdup |
| `00:14:29.360 - 00:14:33.910` | function let's look at fb alec |
| `00:14:33.920 - 00:14:35.590` | if the alec has passed a pointer to a |
| `00:14:35.600 - 00:14:37.509` | file structure that's the file that's |
| `00:14:37.519 - 00:14:38.710` | already open |
| `00:14:38.720 - 00:14:41.350` | and it goes to the proc structure and |
| `00:14:41.360 - 00:14:43.910` | then in this loop it runs through the o |
| `00:14:43.920 - 00:14:46.629` | file array from zero to the end looking |
| `00:14:46.639 - 00:14:49.350` | for an entry that is currently not in |
| `00:14:49.360 - 00:14:51.829` | use and if it finds one then it will |
| `00:14:51.839 - 00:14:52.949` | store |
| `00:14:52.959 - 00:14:55.110` | a pointer to this file structure in that |
| `00:14:55.120 - 00:14:57.990` | entry and return the file descriptor |
| `00:14:58.000 - 00:15:00.069` | that is the number the small integer |
| `00:15:00.079 - 00:15:02.790` | that indicates uh which entry was used |
| `00:15:02.800 - 00:15:05.670` | and if there are no more slots then it |
| `00:15:05.680 - 00:15:08.310` | returns minus one so looking at the |
| `00:15:08.320 - 00:15:09.189` | picture |
| `00:15:09.199 - 00:15:11.829` | uh we see that it's going to go through |
| `00:15:11.839 - 00:15:14.310` | the array until it finds a null and then |
| `00:15:14.320 - 00:15:16.550` | it's going to store a pointer to some |
| `00:15:16.560 - 00:15:18.470` | file structure |
| `00:15:18.480 - 00:15:20.550` | and return whichever index it stored it |
| `00:15:20.560 - 00:15:21.910` | into |
| `00:15:21.920 - 00:15:24.870` | now we can look at cis dupe |
| `00:15:24.880 - 00:15:27.189` | it's passed a single argument and argh |
| `00:15:27.199 - 00:15:29.990` | fd goes and gets the first argument |
| `00:15:30.000 - 00:15:32.389` | and that should be a file descriptor |
| `00:15:32.399 - 00:15:35.590` | to an open file so it returns or saves |
| `00:15:35.600 - 00:15:39.189` | into f a pointer to the file structure |
| `00:15:39.199 - 00:15:42.310` | and if that's okay then it calls fd alec |
| `00:15:42.320 - 00:15:44.550` | and that will find an empty slot in the |
| `00:15:44.560 - 00:15:46.710` | array and return the index of that empty |
| `00:15:46.720 - 00:15:48.629` | slot as well as filling it in |
| `00:15:48.639 - 00:15:51.749` | and at this point we call file dupe file |
| `00:15:51.759 - 00:15:54.550` | duke was covered in an earlier video but |
| `00:15:54.560 - 00:15:56.310` | essentially what it's going to do |
| `00:15:56.320 - 00:15:58.150` | it's passed a pointer to a file |
| `00:15:58.160 - 00:15:59.189` | structure |
| `00:15:59.199 - 00:16:00.550` | and it will |
| `00:16:00.560 - 00:16:02.150` | lock that structure |
| `00:16:02.160 - 00:16:04.629` | increment the reference count and then |
| `00:16:04.639 - 00:16:06.230` | unlock it in return |
| `00:16:06.240 - 00:16:07.590` | and then finally |
| `00:16:07.600 - 00:16:10.150` | sysdup will return the file descriptor |
| `00:16:10.160 - 00:16:11.749` | that was allocated |
| `00:16:11.759 - 00:16:13.269` | now one thing about unix that's very |
| `00:16:13.279 - 00:16:14.629` | important |
| `00:16:14.639 - 00:16:18.069` | and that is that uh the user has this |
| `00:16:18.079 - 00:16:19.269` | idea |
| `00:16:19.279 - 00:16:21.110` | that these file descriptors are |
| `00:16:21.120 - 00:16:23.030` | allocated in some order |
| `00:16:23.040 - 00:16:26.230` | and zero one zero is used for standard n |
| `00:16:26.240 - 00:16:28.870` | one for standard out two for uh standard |
| `00:16:28.880 - 00:16:29.749` | error |
| `00:16:29.759 - 00:16:33.269` | and that we rely on the fact that dupe |
| `00:16:33.279 - 00:16:36.629` | will fill in uh the first empty slot in |
| `00:16:36.639 - 00:16:40.150` | this ordered array here and that's used |
| `00:16:40.160 - 00:16:42.069` | by the shell program |
| `00:16:42.079 - 00:16:46.629` | that is relied on by the shell program |
| `00:16:46.639 - 00:16:49.670` | next let's look at the f-stat function |
| `00:16:49.680 - 00:16:51.509` | it's passed the file descriptor of an |
| `00:16:51.519 - 00:16:53.670` | open file and a virtual address and it |
| `00:16:53.680 - 00:16:55.749` | saves some information about that file |
| `00:16:55.759 - 00:16:57.829` | at that address in the user's address |
| `00:16:57.839 - 00:16:59.269` | space |
| `00:16:59.279 - 00:17:01.509` | i already covered the |
| `00:17:01.519 - 00:17:02.790` | sys |
| `00:17:02.800 - 00:17:05.029` | stat function but i'll just quickly |
| `00:17:05.039 - 00:17:07.990` | review it here takes two arguments |
| `00:17:08.000 - 00:17:10.870` | it uses arg fd to get a pointer to the |
| `00:17:10.880 - 00:17:12.870` | file structure for the open file |
| `00:17:12.880 - 00:17:16.069` | and it obtains the virtual address here |
| `00:17:16.079 - 00:17:18.630` | as the second argument and then it calls |
| `00:17:18.640 - 00:17:21.590` | the file stat function which will copy |
| `00:17:21.600 - 00:17:24.710` | information about this particular file |
| `00:17:24.720 - 00:17:29.909` | to the virtual address given here |
| `00:17:29.919 - 00:17:31.669` | next let's take a look at the change |
| `00:17:31.679 - 00:17:33.750` | directory system call |
| `00:17:33.760 - 00:17:36.710` | every process has this idea of a current |
| `00:17:36.720 - 00:17:38.950` | working directory and from time to time |
| `00:17:38.960 - 00:17:40.710` | we can use the system call to change |
| `00:17:40.720 - 00:17:42.470` | that directory |
| `00:17:42.480 - 00:17:45.830` | it's passed a string which is a path |
| `00:17:45.840 - 00:17:48.549` | name that describes a file which better |
| `00:17:48.559 - 00:17:50.789` | exist and better be a directory |
| `00:17:50.799 - 00:17:52.549` | and if so |
| `00:17:52.559 - 00:17:54.230` | it will change the current working |
| `00:17:54.240 - 00:17:56.230` | directory to that |
| `00:17:56.240 - 00:17:57.590` | file |
| `00:17:57.600 - 00:17:59.110` | so in this picture |
| `00:17:59.120 - 00:18:02.230` | i showed the o file array i did not show |
| `00:18:02.240 - 00:18:04.230` | the current working directory field but |
| `00:18:04.240 - 00:18:05.430` | it's there |
| `00:18:05.440 - 00:18:06.950` | and it points |
| `00:18:06.960 - 00:18:09.029` | not to a file structure but directly to |
| `00:18:09.039 - 00:18:11.669` | an inode structure |
| `00:18:11.679 - 00:18:15.029` | so now let's take a look at the function |
| `00:18:15.039 - 00:18:17.909` | sys change directory that |
| `00:18:17.919 - 00:18:22.150` | does this system call |
| `00:18:22.160 - 00:18:25.669` | first it gets the argument the argument |
| `00:18:25.679 - 00:18:28.630` | is a pointer to a string in the user's |
| `00:18:28.640 - 00:18:30.310` | virtual address space |
| `00:18:30.320 - 00:18:32.630` | and the arg string function will |
| `00:18:32.640 - 00:18:36.630` | retrieve that string from the user space |
| `00:18:36.640 - 00:18:38.549` | 0 indicates that it's the first argument |
| `00:18:38.559 - 00:18:40.230` | that we're interested in there is only |
| `00:18:40.240 - 00:18:42.630` | one argument path is a local variable |
| `00:18:42.640 - 00:18:45.430` | here and we're going to move care the |
| `00:18:45.440 - 00:18:47.590` | string up to the null byte |
| `00:18:47.600 - 00:18:50.470` | uh into this array here |
| `00:18:50.480 - 00:18:53.830` | and we'll stop at the maximum length if |
| `00:18:53.840 - 00:18:55.590` | we reach that |
| `00:18:55.600 - 00:18:57.510` | so the second thing we do is we invoke |
| `00:18:57.520 - 00:18:59.990` | this name i function |
| `00:19:00.000 - 00:19:01.590` | we covered that earlier but basically |
| `00:19:01.600 - 00:19:03.590` | it's passed a string which is a path |
| `00:19:03.600 - 00:19:07.110` | name and it will open that file and |
| `00:19:07.120 - 00:19:09.669` | allocate an inode and return a pointer |
| `00:19:09.679 - 00:19:12.070` | to that inode so ip is a pointer to an |
| `00:19:12.080 - 00:19:14.070` | inode |
| `00:19:14.080 - 00:19:15.270` | and |
| `00:19:15.280 - 00:19:17.350` | if that's successful then we keep going |
| `00:19:17.360 - 00:19:18.390` | but |
| `00:19:18.400 - 00:19:19.750` | if there's either a problem with a |
| `00:19:19.760 - 00:19:22.870` | string or a problem finding that |
| `00:19:22.880 - 00:19:24.710` | file then |
| `00:19:24.720 - 00:19:28.230` | we will immediately return minus one |
| `00:19:28.240 - 00:19:30.310` | now you can see that i've drawn a line |
| `00:19:30.320 - 00:19:32.950` | between this begin op and this end off |
| `00:19:32.960 - 00:19:36.150` | and also between this lock and unlock |
| `00:19:36.160 - 00:19:38.630` | sometimes when we have a complex series |
| `00:19:38.640 - 00:19:41.110` | of nested loops or if statements |
| `00:19:41.120 - 00:19:44.789` | uh using lines like this can help match |
| `00:19:44.799 - 00:19:46.549` | the braces |
| `00:19:46.559 - 00:19:49.029` | and so here i'm doing it to make sure or |
| `00:19:49.039 - 00:19:50.870` | to show how these things are matched up |
| `00:19:50.880 - 00:19:53.110` | and to make sure they are in fact |
| `00:19:53.120 - 00:19:56.230` | matched |
| `00:19:56.240 - 00:19:57.669` | so |
| `00:19:57.679 - 00:19:59.590` | the name i function |
| `00:19:59.600 - 00:20:00.390` | will |
| `00:20:00.400 - 00:20:02.549` | increment the reference count for the i |
| `00:20:02.559 - 00:20:03.510` | node |
| `00:20:03.520 - 00:20:06.789` | okay so that was done in the i get |
| `00:20:06.799 - 00:20:10.870` | function and that's part of name i so if |
| `00:20:10.880 - 00:20:13.990` | this thing is successful then the |
| `00:20:14.000 - 00:20:15.830` | reference count for the inode is |
| `00:20:15.840 - 00:20:17.830` | incremented and we need to ultimately do |
| `00:20:17.840 - 00:20:21.190` | an i put such as this down here to match |
| `00:20:21.200 - 00:20:23.990` | that but if there's a problem then it |
| `00:20:24.000 - 00:20:26.070` | won't be incremented so then when we get |
| `00:20:26.080 - 00:20:27.669` | ready to return here we're going to jump |
| `00:20:27.679 - 00:20:28.549` | out |
| `00:20:28.559 - 00:20:30.630` | and we can see that we're crossing this |
| `00:20:30.640 - 00:20:32.950` | single line here so we need to take care |
| `00:20:32.960 - 00:20:34.950` | of that so there's the end up that |
| `00:20:34.960 - 00:20:37.190` | matches with that begin up |
| `00:20:37.200 - 00:20:39.350` | but assuming everything's okay then we |
| `00:20:39.360 - 00:20:41.270` | need to check that file |
| `00:20:41.280 - 00:20:43.029` | and make sure that it is a directory |
| `00:20:43.039 - 00:20:44.149` | file |
| `00:20:44.159 - 00:20:46.310` | before we look at any field in that |
| `00:20:46.320 - 00:20:49.029` | inode we need to lock the inode so here |
| `00:20:49.039 - 00:20:51.510` | we're locking it and checking the type |
| `00:20:51.520 - 00:20:53.510` | field to make sure that it's okay |
| `00:20:53.520 - 00:20:55.430` | and if it's not okay |
| `00:20:55.440 - 00:20:58.789` | then well we have done the get |
| `00:20:58.799 - 00:21:01.110` | within the name i function and we've |
| `00:21:01.120 - 00:21:03.510` | done the lock and we've also got the |
| `00:21:03.520 - 00:21:05.270` | begin up sort of |
| `00:21:05.280 - 00:21:08.789` | open so we need to uh undo all of that |
| `00:21:08.799 - 00:21:11.190` | so we unlock put and in the op and |
| `00:21:11.200 - 00:21:13.669` | return to minus one but assuming that it |
| `00:21:13.679 - 00:21:16.630` | is a directory then everything's okay |
| `00:21:16.640 - 00:21:18.549` | and we're going to proceed and return |
| `00:21:18.559 - 00:21:20.390` | zero here |
| `00:21:20.400 - 00:21:22.230` | now we've got an existing |
| `00:21:22.240 - 00:21:24.870` | current working directory and |
| `00:21:24.880 - 00:21:25.669` | that |
| `00:21:25.679 - 00:21:28.230` | field will point to an inode for some |
| `00:21:28.240 - 00:21:31.350` | other directory and we have incremented |
| `00:21:31.360 - 00:21:33.909` | the reference count for that inode to |
| `00:21:33.919 - 00:21:37.110` | keep it from being deleted and so we're |
| `00:21:37.120 - 00:21:39.909` | done with that now we no longer are |
| `00:21:39.919 - 00:21:42.310` | going to point to it so we need to do |
| `00:21:42.320 - 00:21:45.270` | the put function for that so here we're |
| `00:21:45.280 - 00:21:47.430` | sort of releasing our pointer |
| `00:21:47.440 - 00:21:50.149` | and uh this one we've incremented the |
| `00:21:50.159 - 00:21:51.990` | pointer so in some sense we've |
| `00:21:52.000 - 00:21:53.830` | incremented one pointer and decremented |
| `00:21:53.840 - 00:21:55.990` | the other uh reference count |
| `00:21:56.000 - 00:21:57.430` | we've incremented one reference count |
| `00:21:57.440 - 00:21:59.190` | and decremented the other reference |
| `00:21:59.200 - 00:22:00.310` | count so |
| `00:22:00.320 - 00:22:02.390` | in some ways these are sort of matched |
| `00:22:02.400 - 00:22:05.430` | but in any case um we are now done with |
| `00:22:05.440 - 00:22:08.310` | our transaction and the final thing we |
| `00:22:08.320 - 00:22:10.789` | do is store a pointer to the inode in |
| `00:22:10.799 - 00:22:13.510` | the current working directory |
| `00:22:13.520 - 00:22:15.990` | field of the proc structure and then we |
| `00:22:16.000 - 00:22:19.029` | return zero |
| `00:22:19.039 - 00:22:20.470` | next let's take a look at the link |
| `00:22:20.480 - 00:22:22.950` | system call it's passed pointers to two |
| `00:22:22.960 - 00:22:24.310` | strings |
| `00:22:24.320 - 00:22:26.870` | the first is the path name of an |
| `00:22:26.880 - 00:22:29.029` | existing file and what this function is |
| `00:22:29.039 - 00:22:31.669` | going to do is it's going to add an |
| `00:22:31.679 - 00:22:34.710` | entry into a directory so that the new |
| `00:22:34.720 - 00:22:37.110` | path name will be valid and will refer |
| `00:22:37.120 - 00:22:39.110` | to the same file |
| `00:22:39.120 - 00:22:41.590` | if everything's okay it will return 0 |
| `00:22:41.600 - 00:22:44.470` | and it will return -1 if there are any |
| `00:22:44.480 - 00:22:46.390` | problems |
| `00:22:46.400 - 00:22:49.669` | so here is the syslink function |
| `00:22:49.679 - 00:22:50.789` | and |
| `00:22:50.799 - 00:22:54.070` | it begins by dealing with the arguments |
| `00:22:54.080 - 00:22:57.750` | we have two arrays here new and old in |
| `00:22:57.760 - 00:23:00.390` | which we store these path name strings |
| `00:23:00.400 - 00:23:02.390` | we need to copy them from the user's |
| `00:23:02.400 - 00:23:04.390` | virtual address space into these arrays |
| `00:23:04.400 - 00:23:05.270` | first |
| `00:23:05.280 - 00:23:07.350` | and we do that with the call to the arg |
| `00:23:07.360 - 00:23:09.110` | string function |
| `00:23:09.120 - 00:23:11.110` | so here we're calling it for the first |
| `00:23:11.120 - 00:23:12.710` | argument and here for the second |
| `00:23:12.720 - 00:23:16.149` | argument and we copy the first path name |
| `00:23:16.159 - 00:23:19.750` | into the old array up to the null byte |
| `00:23:19.760 - 00:23:21.750` | or up to max path |
| `00:23:21.760 - 00:23:23.029` | characters |
| `00:23:23.039 - 00:23:25.990` | and here we are copying the second |
| `00:23:26.000 - 00:23:29.110` | argument string into the new array up to |
| `00:23:29.120 - 00:23:30.549` | the null byte |
| `00:23:30.559 - 00:23:32.710` | uh with no more than max path bytes |
| `00:23:32.720 - 00:23:34.789` | copied if there's any problem here we |
| `00:23:34.799 - 00:23:37.350` | immediately return -1 |
| `00:23:37.360 - 00:23:38.870` | now we're going to be updating the file |
| `00:23:38.880 - 00:23:41.830` | system so we need to put this into a |
| `00:23:41.840 - 00:23:43.350` | transaction |
| `00:23:43.360 - 00:23:45.990` | this begin op is where the transaction |
| `00:23:46.000 - 00:23:49.190` | begins and down here is the matching end |
| `00:23:49.200 - 00:23:51.669` | up and we return zero to indicate that |
| `00:23:51.679 - 00:23:53.269` | everything's okay |
| `00:23:53.279 - 00:23:55.269` | so you can see that i'm using this line |
| `00:23:55.279 - 00:23:58.870` | system where i match i get with i put i |
| `00:23:58.880 - 00:24:01.990` | lock with i unlock and i begin with |
| `00:24:02.000 - 00:24:06.070` | sorry begin off with endop |
| `00:24:06.080 - 00:24:08.070` | the next thing we do is we take the old |
| `00:24:08.080 - 00:24:10.549` | path name and we invoke this name i |
| `00:24:10.559 - 00:24:12.549` | function that i talked about earlier |
| `00:24:12.559 - 00:24:13.830` | this is going to go out to the file |
| `00:24:13.840 - 00:24:16.549` | system and locate this file |
| `00:24:16.559 - 00:24:19.510` | and then it's going to allocate an inode |
| `00:24:19.520 - 00:24:21.669` | structure in the inode cache |
| `00:24:21.679 - 00:24:24.390` | and if successful it will return a |
| `00:24:24.400 - 00:24:26.789` | pointer to that inode structure and |
| `00:24:26.799 - 00:24:28.549` | increment the reference count |
| `00:24:28.559 - 00:24:31.269` | but if there's a problem it will return |
| `00:24:31.279 - 00:24:33.990` | minus one so we're inside this begin up |
| `00:24:34.000 - 00:24:34.870` | we mean |
| `00:24:34.880 - 00:24:36.470` | we need to immediately end the |
| `00:24:36.480 - 00:24:39.350` | transaction and return -1 |
| `00:24:39.360 - 00:24:41.750` | but if it is successful then we will |
| `00:24:41.760 - 00:24:43.590` | have increment of the reference count |
| `00:24:43.600 - 00:24:46.789` | for that inode which is what iget does |
| `00:24:46.799 - 00:24:48.630` | and so that's why this line |
| `00:24:48.640 - 00:24:50.310` | is here |
| `00:24:50.320 - 00:24:51.990` | the next thing we're going to do is |
| `00:24:52.000 - 00:24:54.070` | check the type of the old file the |
| `00:24:54.080 - 00:24:56.310` | existing file to make sure that it's not |
| `00:24:56.320 - 00:24:57.909` | a directory |
| `00:24:57.919 - 00:25:00.070` | the directories must be organized in a |
| `00:25:00.080 - 00:25:02.470` | tree so we can't add additional links to |
| `00:25:02.480 - 00:25:03.669` | a directory |
| `00:25:03.679 - 00:25:05.750` | and that's why we're checking here |
| `00:25:05.760 - 00:25:08.390` | if it is a directory then we're going to |
| `00:25:08.400 - 00:25:11.430` | abort but before we check the type field |
| `00:25:11.440 - 00:25:13.909` | we need to lock that inode so we lock it |
| `00:25:13.919 - 00:25:14.710` | here |
| `00:25:14.720 - 00:25:16.710` | check it if there's a problem we |
| `00:25:16.720 - 00:25:19.669` | immediately unlock it and then |
| `00:25:19.679 - 00:25:22.789` | call i put to undo the increment of the |
| `00:25:22.799 - 00:25:24.310` | reference count so we decrement the |
| `00:25:24.320 - 00:25:25.510` | reference count |
| `00:25:25.520 - 00:25:28.149` | and then we end the transaction so we're |
| `00:25:28.159 - 00:25:29.750` | essentially breaking out going through |
| `00:25:29.760 - 00:25:31.750` | all three of these lines here |
| `00:25:31.760 - 00:25:34.470` | and then we return -1 |
| `00:25:34.480 - 00:25:36.470` | but assuming that it's not a directory |
| `00:25:36.480 - 00:25:39.110` | then things are looking good so we need |
| `00:25:39.120 - 00:25:42.070` | to increment the n-link field in the |
| `00:25:42.080 - 00:25:44.549` | inode that's the number of hard links |
| `00:25:44.559 - 00:25:46.470` | that point to this file |
| `00:25:46.480 - 00:25:48.549` | and since we just updated the inode we |
| `00:25:48.559 - 00:25:50.710` | need to write that back to the disk so |
| `00:25:50.720 - 00:25:53.830` | here we're calling i update and then we |
| `00:25:53.840 - 00:25:56.950` | unlock this inode this won't actually |
| `00:25:56.960 - 00:25:58.470` | get written to the disk of course until |
| `00:25:58.480 - 00:26:02.230` | we end the transaction down here but |
| `00:26:02.240 - 00:26:04.789` | for now we're done with it so we update |
| `00:26:04.799 - 00:26:08.630` | the disk and unlock this inode |
| `00:26:08.640 - 00:26:10.710` | next we need to process the new path |
| `00:26:10.720 - 00:26:13.590` | name so here's an example new path name |
| `00:26:13.600 - 00:26:16.149` | it consists of a directory |
| `00:26:16.159 - 00:26:17.590` | followed by |
| `00:26:17.600 - 00:26:19.990` | a name at the very end and we talked |
| `00:26:20.000 - 00:26:23.029` | about name i parent before but it's past |
| `00:26:23.039 - 00:26:26.230` | this path name what it's going to do is |
| `00:26:26.240 - 00:26:28.549` | locate this directory and allocate an |
| `00:26:28.559 - 00:26:31.750` | inode for it and return the inode or |
| `00:26:31.760 - 00:26:34.789` | return a pointer to the inode as dp |
| `00:26:34.799 - 00:26:36.230` | and so it's incremented the reference |
| `00:26:36.240 - 00:26:38.470` | count for the inode that describes the |
| `00:26:38.480 - 00:26:40.149` | directory |
| `00:26:40.159 - 00:26:43.430` | and it's also going to isolate the final |
| `00:26:43.440 - 00:26:45.990` | name in this path name and it's going to |
| `00:26:46.000 - 00:26:48.870` | store it in this variable here |
| `00:26:48.880 - 00:26:52.070` | name is an array here that's limited to |
| `00:26:52.080 - 00:26:54.630` | well directory size is 14 bytes so it |
| `00:26:54.640 - 00:26:56.870` | will store the final |
| `00:26:56.880 - 00:26:59.750` | item in this path name into this name |
| `00:26:59.760 - 00:27:01.909` | variable |
| `00:27:01.919 - 00:27:05.269` | if there's a problem like the string is |
| `00:27:05.279 - 00:27:07.269` | all messed up and empty or this |
| `00:27:07.279 - 00:27:09.909` | directory doesn't exist then this thing |
| `00:27:09.919 - 00:27:13.350` | will return -1 and we will jump out and |
| `00:27:13.360 - 00:27:15.830` | go to a label called bad |
| `00:27:15.840 - 00:27:17.669` | if there is a problem we will not |
| `00:27:17.679 - 00:27:20.389` | increment the reference count for |
| `00:27:20.399 - 00:27:23.750` | this dp inode so that's why i'm skipping |
| `00:27:23.760 - 00:27:25.350` | this line here |
| `00:27:25.360 - 00:27:28.630` | but we still have the |
| `00:27:28.640 - 00:27:31.269` | i the reference count for the old file |
| `00:27:31.279 - 00:27:33.750` | and the begin op sort of pending so |
| `00:27:33.760 - 00:27:35.269` | that's why they're i'm crossing two |
| `00:27:35.279 - 00:27:37.269` | lines here now let's look at this bad |
| `00:27:37.279 - 00:27:39.830` | label right now oops |
| `00:27:39.840 - 00:27:41.590` | here it is |
| `00:27:41.600 - 00:27:43.990` | and so i'm showing the two lines here |
| `00:27:44.000 - 00:27:45.510` | that we have to cross to come into this |
| `00:27:45.520 - 00:27:46.710` | label |
| `00:27:46.720 - 00:27:49.750` | and we need to decrement the |
| `00:27:49.760 - 00:27:51.269` | number of hard lengths |
| `00:27:51.279 - 00:27:53.590` | we previously incremented it on the |
| `00:27:53.600 - 00:27:54.710` | assumption that everything was going to |
| `00:27:54.720 - 00:27:57.269` | work out but we've got a problem now so |
| `00:27:57.279 - 00:28:00.710` | we need to decrement the end link field |
| `00:28:00.720 - 00:28:02.389` | and in order to do that we need to hold |
| `00:28:02.399 - 00:28:05.750` | a lock on that inode so here we lock the |
| `00:28:05.760 - 00:28:08.870` | inode and here we uh decrement it and |
| `00:28:08.880 - 00:28:10.630` | here we update it |
| `00:28:10.640 - 00:28:11.830` | on disk |
| `00:28:11.840 - 00:28:14.630` | and then at this point we unlock it and |
| `00:28:14.640 - 00:28:17.029` | we also decrement the reference count |
| `00:28:17.039 - 00:28:19.750` | for this existing file and finally we in |
| `00:28:19.760 - 00:28:24.149` | the transaction and return -1 |
| `00:28:24.159 - 00:28:25.830` | but assuming that we did not take that |
| `00:28:25.840 - 00:28:28.789` | jump and everything's still looking good |
| `00:28:28.799 - 00:28:32.149` | we now need to modify this directory to |
| `00:28:32.159 - 00:28:34.789` | add this name so we're going to lock the |
| `00:28:34.799 - 00:28:36.870` | directory dp |
| `00:28:36.880 - 00:28:39.909` | and then we are going to check to make |
| `00:28:39.919 - 00:28:42.630` | sure that this directory is located on |
| `00:28:42.640 - 00:28:44.789` | the same device that the existing file |
| `00:28:44.799 - 00:28:46.549` | is located on |
| `00:28:46.559 - 00:28:48.870` | remember that hard links cannot cross |
| `00:28:48.880 - 00:28:50.710` | from one file system to another they |
| `00:28:50.720 - 00:28:52.710` | have to be on the same device and so |
| `00:28:52.720 - 00:28:54.950` | that's this check right here and if |
| `00:28:54.960 - 00:28:56.950` | there's a problem we're going to go to |
| `00:28:56.960 - 00:28:59.350` | bad |
| `00:28:59.360 - 00:29:00.710` | but if |
| `00:29:00.720 - 00:29:01.830` | that's okay |
| `00:29:01.840 - 00:29:04.310` | then we call the directory link function |
| `00:29:04.320 - 00:29:06.630` | which i discussed before it's passed a |
| `00:29:06.640 - 00:29:08.950` | pointer to an inode which describes a |
| `00:29:08.960 - 00:29:11.430` | directory like dp here |
| `00:29:11.440 - 00:29:15.190` | and it's passed a name array as well as |
| `00:29:15.200 - 00:29:18.149` | an inode number an i number |
| `00:29:18.159 - 00:29:21.110` | and so this will add a new entry to this |
| `00:29:21.120 - 00:29:22.230` | directory |
| `00:29:22.240 - 00:29:24.149` | okay and if everything's okay it'll |
| `00:29:24.159 - 00:29:26.950` | return zero otherwise it will return |
| `00:29:26.960 - 00:29:29.269` | minus one if anything went wrong with |
| `00:29:29.279 - 00:29:30.149` | that |
| `00:29:30.159 - 00:29:31.029` | then |
| `00:29:31.039 - 00:29:32.149` | we need to |
| `00:29:32.159 - 00:29:34.470` | uh go to bad okay |
| `00:29:34.480 - 00:29:36.149` | we uh |
| `00:29:36.159 - 00:29:36.950` | will |
| `00:29:36.960 - 00:29:38.070` | need |
| `00:29:38.080 - 00:29:40.389` | we |
| `00:29:40.399 - 00:29:43.029` | will need to deal with the lock that we |
| `00:29:43.039 - 00:29:45.190` | have on dp as well as the fact that |
| `00:29:45.200 - 00:29:46.870` | we've incremented the reference count |
| `00:29:46.880 - 00:29:50.870` | for this inode so we unlock and put here |
| `00:29:50.880 - 00:29:53.669` | and then we go to bad and so we still |
| `00:29:53.679 - 00:29:54.470` | have |
| `00:29:54.480 - 00:29:57.269` | these two things to do we need to |
| `00:29:57.279 - 00:29:59.510` | uh do the matching |
| `00:29:59.520 - 00:30:03.110` | i put and end up which we saw |
| `00:30:03.120 - 00:30:05.110` | happens in |
| `00:30:05.120 - 00:30:09.269` | bad we do the put here and the end up |
| `00:30:09.279 - 00:30:11.510` | okay but if that didn't happen |
| `00:30:11.520 - 00:30:14.789` | and the directory link returned zero |
| `00:30:14.799 - 00:30:16.389` | then everything was okay |
| `00:30:16.399 - 00:30:19.590` | well we're done now we can |
| `00:30:19.600 - 00:30:20.870` | unlock |
| `00:30:20.880 - 00:30:22.389` | the directory |
| `00:30:22.399 - 00:30:23.909` | and here we incremented the reference |
| `00:30:23.919 - 00:30:25.909` | count for that inode so we |
| `00:30:25.919 - 00:30:27.830` | do the corresponding put |
| `00:30:27.840 - 00:30:29.830` | and then we |
| `00:30:29.840 - 00:30:32.310` | do the corresponding put for the old |
| `00:30:32.320 - 00:30:35.430` | file and end this transaction and |
| `00:30:35.440 - 00:30:38.789` | finally we return 0. |
| `00:30:38.799 - 00:30:41.350` | next let's look at the unlink system |
| `00:30:41.360 - 00:30:42.149` | call |
| `00:30:42.159 - 00:30:43.750` | it's passed a pointer to a string which |
| `00:30:43.760 - 00:30:46.470` | is a path name and it will remove a hard |
| `00:30:46.480 - 00:30:49.269` | link to that file |
| `00:30:49.279 - 00:30:51.990` | return 0 if everything's okay and -1 if |
| `00:30:52.000 - 00:30:53.430` | there's a problem |
| `00:30:53.440 - 00:30:56.789` | before looking at the code of sys unlink |
| `00:30:56.799 - 00:30:59.269` | let's look at the code for |
| `00:30:59.279 - 00:31:02.149` | is directory empty it's a little helper |
| `00:31:02.159 - 00:31:03.110` | function |
| `00:31:03.120 - 00:31:05.750` | and it's going to be past the inode for |
| `00:31:05.760 - 00:31:07.269` | a directory and it's going to go through |
| `00:31:07.279 - 00:31:09.110` | that directory and ask whether it |
| `00:31:09.120 - 00:31:11.190` | contains anything |
| `00:31:11.200 - 00:31:13.590` | so you see it loops through this |
| `00:31:13.600 - 00:31:16.789` | directory in units of |
| `00:31:16.799 - 00:31:19.110` | well d e is the |
| `00:31:19.120 - 00:31:21.590` | is a directory entry and remember that a |
| `00:31:21.600 - 00:31:23.750` | directory entry contains a 14 |
| `00:31:23.760 - 00:31:26.789` | byte file name and a two byte inode |
| `00:31:26.799 - 00:31:29.430` | number so the size of this thing is 16. |
| `00:31:29.440 - 00:31:31.590` | so it's in going to increment in units |
| `00:31:31.600 - 00:31:32.950` | of 16 |
| `00:31:32.960 - 00:31:35.509` | but it's not starting at zero instead |
| `00:31:35.519 - 00:31:38.070` | we're going to skip the entry for dot |
| `00:31:38.080 - 00:31:39.750` | and dot dot |
| `00:31:39.760 - 00:31:42.230` | every directory must contain these two |
| `00:31:42.240 - 00:31:43.909` | so instead of starting |
| `00:31:43.919 - 00:31:46.389` | at and going 0 |
| `00:31:46.399 - 00:31:47.909` | 16 |
| `00:31:47.919 - 00:31:49.110` | 32 |
| `00:31:49.120 - 00:31:53.269` | 48 and so on we're going to start at 32 |
| `00:31:53.279 - 00:31:56.470` | and increment in units of 16. so that's |
| `00:31:56.480 - 00:31:58.950` | what's going on here |
| `00:31:58.960 - 00:32:00.630` | and we end when we get to the size of |
| `00:32:00.640 - 00:32:02.870` | the directory and for each of these |
| `00:32:02.880 - 00:32:05.590` | offsets we're going to read from |
| `00:32:05.600 - 00:32:07.669` | the directory so we're providing the |
| `00:32:07.679 - 00:32:10.149` | pointer to the inode for the directory |
| `00:32:10.159 - 00:32:13.350` | and we are going to move from |
| `00:32:13.360 - 00:32:16.070` | this offset in the file |
| `00:32:16.080 - 00:32:19.029` | 16 bytes and we're going to place them |
| `00:32:19.039 - 00:32:22.389` | into this local variable d e |
| `00:32:22.399 - 00:32:25.269` | and this is a variable in kernel space |
| `00:32:25.279 - 00:32:27.750` | and this flag here indicates that it is |
| `00:32:27.760 - 00:32:30.310` | a physical address and not an address in |
| `00:32:30.320 - 00:32:32.070` | virtual memory |
| `00:32:32.080 - 00:32:35.190` | and we should return the number of bytes |
| `00:32:35.200 - 00:32:37.350` | that we've asked for so we're reading 16 |
| `00:32:37.360 - 00:32:38.630` | bytes and we should |
| `00:32:38.640 - 00:32:40.710` | return 16 and if that's not the case |
| `00:32:40.720 - 00:32:44.149` | then is the matter and we panic |
| `00:32:44.159 - 00:32:46.870` | but otherwise we look into the directory |
| `00:32:46.880 - 00:32:49.350` | entry that we just read here and ask is |
| `00:32:49.360 - 00:32:51.990` | the i number zero unused entries will |
| `00:32:52.000 - 00:32:54.389` | have an i number of zero |
| `00:32:54.399 - 00:32:57.029` | and um if it's not zero then there is |
| `00:32:57.039 - 00:32:58.950` | something in the directory besides the |
| `00:32:58.960 - 00:33:01.830` | dot and dot dot entries so we return |
| `00:33:01.840 - 00:33:04.230` | false is directly empty |
| `00:33:04.240 - 00:33:06.230` | well it's false that's not true if we |
| `00:33:06.240 - 00:33:08.070` | find something but if we go all the way |
| `00:33:08.080 - 00:33:09.269` | through |
| `00:33:09.279 - 00:33:13.590` | and find nothing we return true |
| `00:33:13.600 - 00:33:16.630` | here is the cis unlink function it's |
| `00:33:16.640 - 00:33:17.830` | kind of long |
| `00:33:17.840 - 00:33:19.990` | you can see that we begin a transaction |
| `00:33:20.000 - 00:33:23.269` | here and we go down here and continuing |
| `00:33:23.279 - 00:33:24.470` | on to the next page |
| `00:33:24.480 - 00:33:26.630` | we ultimately in the transaction and |
| `00:33:26.640 - 00:33:28.950` | return zero if everything's okay |
| `00:33:28.960 - 00:33:30.710` | we've also got a bad label where we |
| `00:33:30.720 - 00:33:35.029` | return minus one |
| `00:33:35.039 - 00:33:35.350` | so |
| `00:33:35.360 - 00:33:36.470` | [Music] |
| `00:33:36.480 - 00:33:38.389` | let's begin with the argument |
| `00:33:38.399 - 00:33:40.070` | we're passed a pointer to a string which |
| `00:33:40.080 - 00:33:43.110` | is the path name and we call arg string |
| `00:33:43.120 - 00:33:45.269` | to copy from the user's virtual address |
| `00:33:45.279 - 00:33:47.990` | space into a local variable path |
| `00:33:48.000 - 00:33:49.269` | so that's what happens here and if |
| `00:33:49.279 - 00:33:50.870` | there's any problem with that we |
| `00:33:50.880 - 00:33:53.990` | immediately return -1 and then we begin |
| `00:33:54.000 - 00:33:55.350` | the transaction |
| `00:33:55.360 - 00:33:57.430` | we need to be running a transaction |
| `00:33:57.440 - 00:33:58.950` | because we're going to be updating the |
| `00:33:58.960 - 00:34:00.310` | disk |
| `00:34:00.320 - 00:34:04.389` | when we remove the file from a directory |
| `00:34:04.399 - 00:34:07.669` | then we begin by calling name i parent |
| `00:34:07.679 - 00:34:09.909` | name i parent which i covered earlier |
| `00:34:09.919 - 00:34:11.829` | has passed a path name |
| `00:34:11.839 - 00:34:14.310` | such as this right here and it's going |
| `00:34:14.320 - 00:34:16.869` | to identify the final component in the |
| `00:34:16.879 - 00:34:18.829` | path name uh |
| `00:34:18.839 - 00:34:21.750` | xxx the file name that we're removing |
| `00:34:21.760 - 00:34:23.909` | and then it's going to identify the |
| `00:34:23.919 - 00:34:26.550` | directory that it's located in |
| `00:34:26.560 - 00:34:28.470` | and it's going to return dp the |
| `00:34:28.480 - 00:34:31.270` | directory pointer which is a pointer to |
| `00:34:31.280 - 00:34:34.869` | an inode structure for this directory |
| `00:34:34.879 - 00:34:37.030` | if it's in this form then it will return |
| `00:34:37.040 - 00:34:38.310` | a pointer to |
| `00:34:38.320 - 00:34:40.869` | the root directory and in this form |
| `00:34:40.879 - 00:34:42.550` | it'll return a pointer to the current |
| `00:34:42.560 - 00:34:46.550` | working directory so now dp points to |
| `00:34:46.560 - 00:34:50.470` | an inode for the directory and the name |
| `00:34:50.480 - 00:34:51.750` | field |
| `00:34:51.760 - 00:34:54.310` | is a local variable here and we are |
| `00:34:54.320 - 00:34:55.510` | copying |
| `00:34:55.520 - 00:34:57.829` | the final component in this path name |
| `00:34:57.839 - 00:35:00.950` | into this name array here |
| `00:35:00.960 - 00:35:02.630` | if something goes wrong |
| `00:35:02.640 - 00:35:03.510` | then |
| `00:35:03.520 - 00:35:06.310` | it will return dp equal to null and we |
| `00:35:06.320 - 00:35:08.870` | immediately end this transaction and |
| `00:35:08.880 - 00:35:11.829` | return minus one but assuming uh that |
| `00:35:11.839 - 00:35:14.310` | this directory exists then we can go |
| `00:35:14.320 - 00:35:17.349` | ahead and lock it |
| `00:35:17.359 - 00:35:19.030` | the next thing we do is we check to see |
| `00:35:19.040 - 00:35:20.870` | whether we're trying to remove the dot |
| `00:35:20.880 - 00:35:23.990` | or dot dot entry these cannot be removed |
| `00:35:24.000 - 00:35:26.630` | from a directory with this function so |
| `00:35:26.640 - 00:35:28.390` | we do a compare to see whether name |
| `00:35:28.400 - 00:35:31.990` | happens to be dot or dot dot and if |
| `00:35:32.000 - 00:35:33.910` | either of these is true then we go to |
| `00:35:33.920 - 00:35:35.670` | bad so |
| `00:35:35.680 - 00:35:36.550` | we've |
| `00:35:36.560 - 00:35:38.310` | started a transaction |
| `00:35:38.320 - 00:35:40.790` | we've done the i get function to |
| `00:35:40.800 - 00:35:42.069` | increment the reference count for the |
| `00:35:42.079 - 00:35:44.870` | inode and we've locked the inode so |
| `00:35:44.880 - 00:35:46.790` | let's go to bat and see what happens |
| `00:35:46.800 - 00:35:47.990` | there |
| `00:35:48.000 - 00:35:49.670` | so here's bad |
| `00:35:49.680 - 00:35:50.470` | we |
| `00:35:50.480 - 00:35:53.750` | are going to unlock the directory |
| `00:35:53.760 - 00:35:56.150` | do an i put which will |
| `00:35:56.160 - 00:35:57.990` | decrement the reference count and then |
| `00:35:58.000 - 00:35:59.190` | we're going to end the transaction and |
| `00:35:59.200 - 00:36:05.349` | return minus one |
| `00:36:05.359 - 00:36:07.510` | so assuming that it's not dot or dot dot |
| `00:36:07.520 - 00:36:09.829` | we can proceed and now we're going to |
| `00:36:09.839 - 00:36:12.710` | call directory lookup directory lookup |
| `00:36:12.720 - 00:36:14.950` | is passed a pointer to a vi node for |
| `00:36:14.960 - 00:36:17.349` | some directory such as |
| `00:36:17.359 - 00:36:20.470` | here and uh a name |
| `00:36:20.480 - 00:36:21.990` | and we're going to look this name up in |
| `00:36:22.000 - 00:36:24.470` | the directory and if we find it we're |
| `00:36:24.480 - 00:36:28.470` | going to allocate an inode for that file |
| `00:36:28.480 - 00:36:31.349` | and return a pointer to that inode |
| `00:36:31.359 - 00:36:33.829` | we're also going to store the offset |
| `00:36:33.839 - 00:36:36.230` | into this directory file of where that |
| `00:36:36.240 - 00:36:38.310` | directory entry occurred so we have a |
| `00:36:38.320 - 00:36:40.390` | local variable offset |
| `00:36:40.400 - 00:36:42.470` | and we're going to save the offset |
| `00:36:42.480 - 00:36:44.230` | within the directory file |
| `00:36:44.240 - 00:36:48.230` | of this entry for this particular name |
| `00:36:48.240 - 00:36:49.990` | if there's a problem and we couldn't |
| `00:36:50.000 - 00:36:53.190` | find the name in the directory then we |
| `00:36:53.200 - 00:36:56.069` | will go to bad okay we will not |
| `00:36:56.079 - 00:36:58.390` | increment the reference count for ip |
| `00:36:58.400 - 00:37:00.870` | uh but still we have to |
| `00:37:00.880 - 00:37:03.190` | unlock the directory and decrement the |
| `00:37:03.200 - 00:37:04.870` | reference account for the directory and |
| `00:37:04.880 - 00:37:05.670` | in the |
| `00:37:05.680 - 00:37:09.109` | transaction so that's what happens there |
| `00:37:09.119 - 00:37:10.790` | but assuming that the name is in the |
| `00:37:10.800 - 00:37:13.190` | directory we have incremented the |
| `00:37:13.200 - 00:37:15.910` | reference count for this inode now we |
| `00:37:15.920 - 00:37:18.310` | need to lock it we're going to be uh |
| `00:37:18.320 - 00:37:20.390` | modifying its end link first of all |
| `00:37:20.400 - 00:37:21.510` | we're going to be looking at its end |
| `00:37:21.520 - 00:37:24.470` | link so we need to lock it before that |
| `00:37:24.480 - 00:37:25.910` | we do a quick check to make sure that |
| `00:37:25.920 - 00:37:27.750` | the end link |
| `00:37:27.760 - 00:37:30.150` | is at least one i mean we can't uh we |
| `00:37:30.160 - 00:37:31.589` | would have some kind of a consistency |
| `00:37:31.599 - 00:37:34.390` | error otherwise and so we panic if it's |
| `00:37:34.400 - 00:37:37.190` | zero or smaller |
| `00:37:37.200 - 00:37:38.069` | then |
| `00:37:38.079 - 00:37:40.630` | we look at the type of the file that |
| `00:37:40.640 - 00:37:42.470` | we're removing |
| `00:37:42.480 - 00:37:45.990` | if we are removing a file you know xxx |
| `00:37:46.000 - 00:37:47.670` | and that is a actually a directory |
| `00:37:47.680 - 00:37:48.790` | itself |
| `00:37:48.800 - 00:37:50.710` | then it better be empty we can only |
| `00:37:50.720 - 00:37:53.109` | remove it if it's an empty file |
| `00:37:53.119 - 00:37:55.270` | uh an empty directory so here we're |
| `00:37:55.280 - 00:37:57.670` | calling the is directory empty function |
| `00:37:57.680 - 00:37:59.670` | so if it is a directory |
| `00:37:59.680 - 00:38:01.750` | and it is not empty |
| `00:38:01.760 - 00:38:03.829` | then we've got a problem so at this |
| `00:38:03.839 - 00:38:06.230` | point we've locked ip an increment of |
| `00:38:06.240 - 00:38:07.829` | the reference count so we need to unlock |
| `00:38:07.839 - 00:38:10.630` | and put the ip and then we can go to bad |
| `00:38:10.640 - 00:38:13.190` | to take care of the other stuff |
| `00:38:13.200 - 00:38:15.270` | okay so that's assuming that error |
| `00:38:15.280 - 00:38:16.790` | hasn't happened |
| `00:38:16.800 - 00:38:18.790` | we don't have any more error conditions |
| `00:38:18.800 - 00:38:20.630` | and we can keep on going |
| `00:38:20.640 - 00:38:23.270` | so we have a local variable d e which is |
| `00:38:23.280 - 00:38:26.550` | a directory entry right 16 bytes 14 |
| `00:38:26.560 - 00:38:27.750` | bytes for the name and two bytes for the |
| `00:38:27.760 - 00:38:29.589` | i number what we're going to do is set |
| `00:38:29.599 - 00:38:31.589` | it all to zero including the i number in |
| `00:38:31.599 - 00:38:32.790` | particular |
| `00:38:32.800 - 00:38:34.310` | and so we've got the directory entry |
| `00:38:34.320 - 00:38:36.710` | being set to zero and now we're going to |
| `00:38:36.720 - 00:38:39.109` | write that directory entry into the |
| `00:38:39.119 - 00:38:40.950` | directory so here's the right eye |
| `00:38:40.960 - 00:38:43.430` | function we're passing it the inode of |
| `00:38:43.440 - 00:38:45.190` | the directory file |
| `00:38:45.200 - 00:38:49.270` | and we are writing from this directory |
| `00:38:49.280 - 00:38:51.109` | entry that's all zeros |
| `00:38:51.119 - 00:38:53.589` | that's in kernel space so this flag says |
| `00:38:53.599 - 00:38:55.430` | it's kernel space and not a virtual |
| `00:38:55.440 - 00:38:56.790` | address |
| `00:38:56.800 - 00:38:59.510` | and we're writing to the offset that's |
| `00:38:59.520 - 00:39:03.030` | the offset we saved with the call to |
| `00:39:03.040 - 00:39:04.550` | directory lookup |
| `00:39:04.560 - 00:39:06.630` | and we are writing well directory |
| `00:39:06.640 - 00:39:08.630` | entries are 16 bytes so we're writing 16 |
| `00:39:08.640 - 00:39:09.510` | bytes |
| `00:39:09.520 - 00:39:11.190` | and this thing will return the number of |
| `00:39:11.200 - 00:39:14.230` | bytes that we wrote it better be 16 i |
| `00:39:14.240 - 00:39:15.910` | mean the directory entry |
| `00:39:15.920 - 00:39:18.790` | is there we found it earlier and we've |
| `00:39:18.800 - 00:39:22.310` | had the thing locked since then so um it |
| `00:39:22.320 - 00:39:23.910` | better still be there |
| `00:39:23.920 - 00:39:26.470` | so we panic if there's a if it's not |
| `00:39:26.480 - 00:39:28.470` | and then uh let me just skip this for a |
| `00:39:28.480 - 00:39:30.630` | second now we're done with the directory |
| `00:39:30.640 - 00:39:32.870` | we're going to unlock it and decrement |
| `00:39:32.880 - 00:39:34.870` | the reference count and then we're going |
| `00:39:34.880 - 00:39:37.910` | to go to the inode for the file itself |
| `00:39:37.920 - 00:39:39.430` | and decrement the number of hard links |
| `00:39:39.440 - 00:39:40.550` | to it |
| `00:39:40.560 - 00:39:42.710` | that will involve an update to the disk |
| `00:39:42.720 - 00:39:47.829` | so we need to um do an unl an eye update |
| `00:39:47.839 - 00:39:50.470` | for this particular inode here |
| `00:39:50.480 - 00:39:53.030` | and then finally we can unlock this file |
| `00:39:53.040 - 00:39:54.710` | and decrement the reference count and |
| `00:39:54.720 - 00:39:56.710` | we're done we can end the operation in |
| `00:39:56.720 - 00:39:59.349` | the transaction and return zero |
| `00:39:59.359 - 00:40:01.670` | but i skipped over this part |
| `00:40:01.680 - 00:40:04.470` | and that i want to try to describe here |
| `00:40:04.480 - 00:40:06.230` | so |
| `00:40:06.240 - 00:40:07.670` | let's imagine |
| `00:40:07.680 - 00:40:10.550` | that uh we're trying to |
| `00:40:10.560 - 00:40:11.829` | remove |
| `00:40:11.839 - 00:40:12.950` | the |
| `00:40:12.960 - 00:40:15.430` | bin entry from this directory so here's |
| `00:40:15.440 - 00:40:18.390` | a directory slash user let's say |
| `00:40:18.400 - 00:40:20.790` | and it's got only one file in it and |
| `00:40:20.800 - 00:40:22.309` | that file is bin |
| `00:40:22.319 - 00:40:24.390` | and so we have a directory entry 14 |
| `00:40:24.400 - 00:40:27.190` | bytes plus two byte inode number which |
| `00:40:27.200 - 00:40:29.670` | points to some other file and that other |
| `00:40:29.680 - 00:40:32.150` | file is a directory file okay it happens |
| `00:40:32.160 - 00:40:34.550` | to be a directory um |
| `00:40:34.560 - 00:40:35.510` | and |
| `00:40:35.520 - 00:40:38.630` | it's pointed to by ip or the inode for |
| `00:40:38.640 - 00:40:40.950` | this file is pointed to by ip |
| `00:40:40.960 - 00:40:43.510` | so uh with directories we have the dot |
| `00:40:43.520 - 00:40:45.430` | and the dot dot going on |
| `00:40:45.440 - 00:40:47.349` | so |
| `00:40:47.359 - 00:40:50.950` | the dot entry always contains the inode |
| `00:40:50.960 - 00:40:53.349` | uh the i number of the file itself okay |
| `00:40:53.359 - 00:40:56.710` | so that's that's the dot entry |
| `00:40:56.720 - 00:40:59.750` | and the dot dot contains the inode of |
| `00:40:59.760 - 00:41:02.470` | the parent so this is a tree we have |
| `00:41:02.480 - 00:41:04.150` | only a single parent |
| `00:41:04.160 - 00:41:06.710` | for directories so we can have |
| `00:41:06.720 - 00:41:09.190` | uh a parent pointer and that's the dot |
| `00:41:09.200 - 00:41:10.790` | dot pointer |
| `00:41:10.800 - 00:41:13.430` | now the question is how many |
| `00:41:13.440 - 00:41:15.270` | links are there to this particular |
| `00:41:15.280 - 00:41:17.829` | directory well of course we have the one |
| `00:41:17.839 - 00:41:19.589` | link that |
| `00:41:19.599 - 00:41:22.470` | is for the directory in which it lives |
| `00:41:22.480 - 00:41:23.430` | okay |
| `00:41:23.440 - 00:41:26.069` | uh we don't count this pointer here but |
| `00:41:26.079 - 00:41:29.109` | we do count this pointer so we've got |
| `00:41:29.119 - 00:41:31.030` | one directory we could have you know |
| `00:41:31.040 - 00:41:32.870` | other directories as well |
| `00:41:32.880 - 00:41:36.150` | in this directory but it looks like we |
| `00:41:36.160 - 00:41:38.950` | just got this one bin and so we've got a |
| `00:41:38.960 - 00:41:41.190` | back pointer from |
| `00:41:41.200 - 00:41:41.990` | bin |
| `00:41:42.000 - 00:41:44.309` | and so that's the second hard link to |
| `00:41:44.319 - 00:41:47.109` | this directory so in link will be two if |
| `00:41:47.119 - 00:41:49.109` | we had something besides bin |
| `00:41:49.119 - 00:41:50.550` | we might have actually |
| `00:41:50.560 - 00:41:54.150` | additional pointers from others but |
| `00:41:54.160 - 00:41:56.069` | what this is showing is that the number |
| `00:41:56.079 - 00:41:59.990` | of hard lengths for a directory includes |
| `00:42:00.000 - 00:42:01.430` | the hard link from |
| `00:42:01.440 - 00:42:03.910` | the parent in the directory tree as well |
| `00:42:03.920 - 00:42:05.910` | as one hard link from each of the |
| `00:42:05.920 - 00:42:08.550` | children directories in the directory |
| `00:42:08.560 - 00:42:11.990` | tree so we've got to decrement |
| `00:42:12.000 - 00:42:13.270` | okay we're |
| `00:42:13.280 - 00:42:15.589` | eliminating this file so we're removing |
| `00:42:15.599 - 00:42:17.349` | bin from this directory |
| `00:42:17.359 - 00:42:19.430` | i already showed where we |
| `00:42:19.440 - 00:42:21.990` | decrement the end link for |
| `00:42:22.000 - 00:42:24.870` | ip but we also have to decrement the |
| `00:42:24.880 - 00:42:27.109` | n-link for dp |
| `00:42:27.119 - 00:42:29.030` | so we decrement the |
| `00:42:29.040 - 00:42:31.589` | in link for ip here |
| `00:42:31.599 - 00:42:34.069` | and we increment or we decrement the in |
| `00:42:34.079 - 00:42:35.109` | link |
| `00:42:35.119 - 00:42:36.950` | for dp here |
| `00:42:36.960 - 00:42:39.510` | and here we update the directory if if |
| `00:42:39.520 - 00:42:42.069` | that's the case we have to update this |
| `00:42:42.079 - 00:42:44.390` | inode on disk as well so we have to call |
| `00:42:44.400 - 00:42:46.790` | i update here |
| `00:42:46.800 - 00:42:48.790` | so after we're done with all that |
| `00:42:48.800 - 00:42:49.990` | we have |
| `00:42:50.000 - 00:42:52.470` | eliminated both this hard link and this |
| `00:42:52.480 - 00:42:55.670` | hard link and adjusted this |
| `00:42:55.680 - 00:42:57.750` | in link pointer and this one because |
| `00:42:57.760 - 00:43:00.950` | we've also eliminated that one |
| `00:43:00.960 - 00:43:02.950` | and then we can return zero that |
| `00:43:02.960 - 00:43:05.670` | everything worked out okay |
| `00:43:05.680 - 00:43:07.430` | there's a subtle point that i wanted to |
| `00:43:07.440 - 00:43:09.670` | mention about this code |
| `00:43:09.680 - 00:43:11.990` | whenever you're locking multiple things |
| `00:43:12.000 - 00:43:14.150` | it can be very critical what order you |
| `00:43:14.160 - 00:43:15.910` | lock them in |
| `00:43:15.920 - 00:43:17.430` | if you lock them in the wrong order |
| `00:43:17.440 - 00:43:19.910` | there might be a possibility of deadlock |
| `00:43:19.920 - 00:43:21.829` | and i've drawn my lines to match the |
| `00:43:21.839 - 00:43:23.430` | locks with the unlocks to make sure that |
| `00:43:23.440 - 00:43:25.990` | everything we lock gets unlocked but it |
| `00:43:26.000 - 00:43:27.750` | doesn't really matter which order you |
| `00:43:27.760 - 00:43:30.150` | unlock things in generally speaking as |
| `00:43:30.160 - 00:43:31.750` | long as you get around to unlocking |
| `00:43:31.760 - 00:43:33.910` | everything that you've logged so here |
| `00:43:33.920 - 00:43:36.710` | i'm showing the unlocks reversed in |
| `00:43:36.720 - 00:43:37.670` | order |
| `00:43:37.680 - 00:43:42.950` | okay and the code that i showed before |
| `00:43:42.960 - 00:43:46.150` | i wanted to i kind of messed up my lines |
| `00:43:46.160 - 00:43:48.550` | we're locking dp here |
| `00:43:48.560 - 00:43:51.589` | and then within that we're locking ip |
| `00:43:51.599 - 00:43:53.349` | but and then at the bottom of this |
| `00:43:53.359 - 00:43:54.550` | function |
| `00:43:54.560 - 00:43:56.150` | we are unlocking |
| `00:43:56.160 - 00:43:57.990` | dp |
| `00:43:58.000 - 00:44:00.790` | and then we're unlocking ip so actually |
| `00:44:00.800 - 00:44:04.069` | we have uh this situation here and not |
| `00:44:04.079 - 00:44:06.870` | this situation so |
| `00:44:06.880 - 00:44:08.230` | really i suppose i ought to draw these |
| `00:44:08.240 - 00:44:10.069` | lines crossing or something |
| `00:44:10.079 - 00:44:12.630` | but i did want to mention that |
| `00:44:12.640 - 00:44:14.550` | okay that's enough code to put in one |
| `00:44:14.560 - 00:44:15.510` | video |
| `00:44:15.520 - 00:44:17.349` | in the next video i will continue going |
| `00:44:17.359 - 00:44:21.430` | over the functions in sysfile.c i will |
| `00:44:21.440 - 00:44:25.560` | see you in part two |
