# xv6 Kernel-35: File Descriptors and Open Files

| Field | Value |
| --- | --- |
| Video ID | `oWuVGDese4k` |
| URL | <https://www.youtube.com/watch?v=oWuVGDese4k> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.199 - 00:00:02.869` | this video is part of a series on the |
| `00:00:02.879 - 00:00:05.510` | xv6 operating system kernel |
| `00:00:05.520 - 00:00:07.670` | in this video i'll be talking about file |
| `00:00:07.680 - 00:00:09.669` | descriptors and |
| `00:00:09.679 - 00:00:11.190` | open files |
| `00:00:11.200 - 00:00:13.509` | and i'll be going over the code in the |
| `00:00:13.519 - 00:00:17.269` | file called file.c |
| `00:00:17.279 - 00:00:19.189` | let's begin by talking about the word |
| `00:00:19.199 - 00:00:22.790` | file this term is ambiguous and fuzzy |
| `00:00:22.800 - 00:00:24.550` | and can be confusing because it has a |
| `00:00:24.560 - 00:00:26.870` | couple of related meanings |
| `00:00:26.880 - 00:00:28.470` | the most common use is when we talk |
| `00:00:28.480 - 00:00:30.950` | about a regular file on the disk |
| `00:00:30.960 - 00:00:32.549` | so for example we might say that a |
| `00:00:32.559 - 00:00:35.430` | directory contains several files as well |
| `00:00:35.440 - 00:00:37.430` | as directories |
| `00:00:37.440 - 00:00:38.709` | as if the directory is something |
| `00:00:38.719 - 00:00:41.110` | different but in fact a directory is a |
| `00:00:41.120 - 00:00:43.270` | kind of file as well |
| `00:00:43.280 - 00:00:46.069` | and in fact within the file system |
| `00:00:46.079 - 00:00:48.069` | a file can either be a directory file a |
| `00:00:48.079 - 00:00:51.189` | regular file or a device file |
| `00:00:51.199 - 00:00:53.510` | these things each have an inode that |
| `00:00:53.520 - 00:00:54.709` | refers to them |
| `00:00:54.719 - 00:00:57.029` | and they can be referenced using path |
| `00:00:57.039 - 00:00:59.910` | names |
| `00:00:59.920 - 00:01:01.750` | c programmers will be familiar with a |
| `00:01:01.760 - 00:01:05.350` | structure called file in capital letters |
| `00:01:05.360 - 00:01:08.149` | this is a user mode only structure in |
| `00:01:08.159 - 00:01:10.070` | other words it lives only in the user's |
| `00:01:10.080 - 00:01:11.990` | virtual address space and the kernel |
| `00:01:12.000 - 00:01:14.550` | doesn't know about it at all |
| `00:01:14.560 - 00:01:16.070` | there are a number of library functions |
| `00:01:16.080 - 00:01:19.429` | like f open f read f write and f close |
| `00:01:19.439 - 00:01:22.789` | that use this file structure but these |
| `00:01:22.799 - 00:01:25.270` | are not system calls instead to get |
| `00:01:25.280 - 00:01:26.469` | their work done |
| `00:01:26.479 - 00:01:29.350` | they use system calls and like open |
| `00:01:29.360 - 00:01:31.670` | close read and write which are system |
| `00:01:31.680 - 00:01:32.950` | calls |
| `00:01:32.960 - 00:01:33.749` | so |
| `00:01:33.759 - 00:01:35.350` | these communicate with the kernel by |
| `00:01:35.360 - 00:01:37.270` | referring to files using a file |
| `00:01:37.280 - 00:01:38.550` | descriptor |
| `00:01:38.560 - 00:01:40.469` | the file descriptor is a small integer |
| `00:01:40.479 - 00:01:42.630` | and each small integer refers to a |
| `00:01:42.640 - 00:01:44.069` | different file |
| `00:01:44.079 - 00:01:46.950` | but in this sense a file descriptor can |
| `00:01:46.960 - 00:01:49.910` | also refer to a pipe as well as a |
| `00:01:49.920 - 00:01:52.550` | directory regular or device or device |
| `00:01:52.560 - 00:01:55.350` | file so that's yet another meaning of |
| `00:01:55.360 - 00:01:57.830` | file |
| `00:01:57.840 - 00:01:59.510` | in this video we'll be looking at a |
| `00:01:59.520 - 00:02:02.630` | structure whose name is file |
| `00:02:02.640 - 00:02:05.109` | and to make things even more confusing |
| `00:02:05.119 - 00:02:07.510` | we have a couple of files in the kernel |
| `00:02:07.520 - 00:02:10.550` | which are called file.h and file.c and |
| `00:02:10.560 - 00:02:13.030` | we'll be looking at the code in those |
| `00:02:13.040 - 00:02:15.830` | files |
| `00:02:15.840 - 00:02:17.589` | to show how the file structure is used |
| `00:02:17.599 - 00:02:19.670` | i've created this diagram |
| `00:02:19.680 - 00:02:22.309` | we've got two processes and each process |
| `00:02:22.319 - 00:02:25.750` | is represented by a proc structure |
| `00:02:25.760 - 00:02:29.750` | and we've got four file structures here |
| `00:02:29.760 - 00:02:31.110` | and then we've got a couple of inode |
| `00:02:31.120 - 00:02:34.229` | structures and a pipe structure |
| `00:02:34.239 - 00:02:37.589` | so let's start with the proc structure |
| `00:02:37.599 - 00:02:40.390` | it has a field called o file which is an |
| `00:02:40.400 - 00:02:43.190` | array and this array contains well it |
| `00:02:43.200 - 00:02:45.430` | contains 15 elements as determined by |
| `00:02:45.440 - 00:02:48.390` | this constant number of open files |
| `00:02:48.400 - 00:02:50.949` | and these are the file descriptors so |
| `00:02:50.959 - 00:02:53.350` | this array is indexed by the file |
| `00:02:53.360 - 00:02:54.710` | descriptor |
| `00:02:54.720 - 00:02:56.550` | when a system call like read or write |
| `00:02:56.560 - 00:02:58.949` | makes a system call to the kernel will |
| `00:02:58.959 - 00:03:01.990` | be passed a file descriptor and it will |
| `00:03:02.000 - 00:03:04.790` | use that as an index into this array and |
| `00:03:04.800 - 00:03:07.589` | then it will follow this pointer to the |
| `00:03:07.599 - 00:03:11.509` | file structure that is being referred to |
| `00:03:11.519 - 00:03:13.910` | so the kernel will |
| `00:03:13.920 - 00:03:15.750` | allocate these pointers and allocate |
| `00:03:15.760 - 00:03:17.509` | slots in this array |
| `00:03:17.519 - 00:03:20.790` | whenever an open system call is made and |
| `00:03:20.800 - 00:03:23.030` | so the user code doesn't get to choose |
| `00:03:23.040 - 00:03:25.110` | which elements are used it is just |
| `00:03:25.120 - 00:03:27.190` | returned to the file descriptor and then |
| `00:03:27.200 - 00:03:29.830` | it must provide the same file descriptor |
| `00:03:29.840 - 00:03:34.470` | in the read write and close system calls |
| `00:03:34.480 - 00:03:35.430` | so |
| `00:03:35.440 - 00:03:38.309` | the file structure begins with a type |
| `00:03:38.319 - 00:03:39.270` | field |
| `00:03:39.280 - 00:03:41.190` | and let's go through these fields and |
| `00:03:41.200 - 00:03:43.270` | see what it's got it's got a reference |
| `00:03:43.280 - 00:03:44.710` | count which is just the number of |
| `00:03:44.720 - 00:03:46.070` | pointers that are pointing to this |
| `00:03:46.080 - 00:03:47.509` | structure |
| `00:03:47.519 - 00:03:49.270` | and then it's got two flags to tell |
| `00:03:49.280 - 00:03:51.350` | whether this file is either readable or |
| `00:03:51.360 - 00:03:53.270` | writable or perhaps both |
| `00:03:53.280 - 00:03:55.589` | and then it's got two pointers the pipe |
| `00:03:55.599 - 00:03:57.830` | pointer will point to a pipe structure |
| `00:03:57.840 - 00:04:00.949` | and the ip pointer if used will point to |
| `00:04:00.959 - 00:04:02.949` | an inode structure |
| `00:04:02.959 - 00:04:05.830` | and then there's an offset and a major |
| `00:04:05.840 - 00:04:09.030` | so let's quickly take a look at the |
| `00:04:09.040 - 00:04:11.670` | structure as defined in the file |
| `00:04:11.680 - 00:04:14.630` | file.h and we see exactly that |
| `00:04:14.640 - 00:04:17.509` | we see the type field |
| `00:04:17.519 - 00:04:18.949` | which can either be |
| `00:04:18.959 - 00:04:19.830` | pipe |
| `00:04:19.840 - 00:04:24.070` | inode or device and it can also be none |
| `00:04:24.080 - 00:04:26.390` | for unused structures |
| `00:04:26.400 - 00:04:28.070` | and then we've got the reference count |
| `00:04:28.080 - 00:04:30.230` | we've got the two readable and writable |
| `00:04:30.240 - 00:04:32.629` | flags and then we've got a pointer to a |
| `00:04:32.639 - 00:04:34.790` | pipe structure and then we've got a |
| `00:04:34.800 - 00:04:37.030` | pointer to an inode structure and the |
| `00:04:37.040 - 00:04:43.590` | offset and a device major number |
| `00:04:43.600 - 00:04:45.990` | so a |
| `00:04:46.000 - 00:04:49.189` | file that's open in some process |
| `00:04:49.199 - 00:04:51.830` | can either be a pipe in which case the |
| `00:04:51.840 - 00:04:53.990` | type field will be pipe |
| `00:04:54.000 - 00:04:56.150` | or it can be a directory or a regular |
| `00:04:56.160 - 00:04:58.469` | file in which case the type field will |
| `00:04:58.479 - 00:04:59.590` | be |
| `00:04:59.600 - 00:05:03.110` | fd underscore inode it'll be inode |
| `00:05:03.120 - 00:05:06.150` | or it can be a device in which case the |
| `00:05:06.160 - 00:05:09.029` | type field is device |
| `00:05:09.039 - 00:05:12.150` | if it is a pipe structure well each pipe |
| `00:05:12.160 - 00:05:14.550` | has a read end and a right end as |
| `00:05:14.560 - 00:05:16.310` | determined by the settings of the |
| `00:05:16.320 - 00:05:18.150` | readable and writable flag so this is |
| `00:05:18.160 - 00:05:20.629` | pointing to the read end of this pipe |
| `00:05:20.639 - 00:05:22.230` | and the pipe field will point to the |
| `00:05:22.240 - 00:05:24.550` | structure and the remaining fields will |
| `00:05:24.560 - 00:05:26.629` | not be used |
| `00:05:26.639 - 00:05:29.830` | if it is a directory or a regular file |
| `00:05:29.840 - 00:05:31.830` | then the pipe field will not be used but |
| `00:05:31.840 - 00:05:34.790` | the ip field will point to the inode |
| `00:05:34.800 - 00:05:38.070` | and the offset field will contain an |
| `00:05:38.080 - 00:05:40.790` | integer that tells where in that file |
| `00:05:40.800 - 00:05:43.590` | the next read or write will occur and |
| `00:05:43.600 - 00:05:46.230` | the major field is unused now this file |
| `00:05:46.240 - 00:05:49.189` | is open for writing only so the next |
| `00:05:49.199 - 00:05:51.670` | write operation will happen at offset |
| `00:05:51.680 - 00:05:53.590` | 47. |
| `00:05:53.600 - 00:05:57.110` | and finally if it's a device then the ip |
| `00:05:57.120 - 00:05:59.990` | pointer will point to an inode |
| `00:06:00.000 - 00:06:02.390` | but the offset will be unused and |
| `00:06:02.400 - 00:06:04.710` | instead the major number which is in the |
| `00:06:04.720 - 00:06:06.950` | inode will be copied into the major |
| `00:06:06.960 - 00:06:11.029` | field of the file structure |
| `00:06:11.039 - 00:06:13.909` | now i want to remind you that in unix |
| `00:06:13.919 - 00:06:16.150` | file descriptor 0 was always used for |
| `00:06:16.160 - 00:06:17.510` | standard n |
| `00:06:17.520 - 00:06:19.029` | and file descriptor 1 is used for |
| `00:06:19.039 - 00:06:21.029` | standard out and file descriptor 2 is |
| `00:06:21.039 - 00:06:22.950` | used for standard error |
| `00:06:22.960 - 00:06:25.990` | so the parent process here apparently |
| `00:06:26.000 - 00:06:27.670` | has standard in |
| `00:06:27.680 - 00:06:30.230` | being open to a pipe that's the read end |
| `00:06:30.240 - 00:06:32.790` | which is what we would expect |
| `00:06:32.800 - 00:06:34.950` | and for standard out the standard out |
| `00:06:34.960 - 00:06:36.070` | will go to |
| `00:06:36.080 - 00:06:37.909` | a file |
| `00:06:37.919 - 00:06:39.670` | and |
| `00:06:39.680 - 00:06:42.309` | that file is described by this inode it |
| `00:06:42.319 - 00:06:44.629` | happens to have a size of 900 but we're |
| `00:06:44.639 - 00:06:45.909` | writing starting at the beginning of |
| `00:06:45.919 - 00:06:47.510` | this file |
| `00:06:47.520 - 00:06:49.830` | and we've made it up to location 47 at |
| `00:06:49.840 - 00:06:51.589` | this point |
| `00:06:51.599 - 00:06:53.670` | and for some reason standard error is |
| `00:06:53.680 - 00:06:57.110` | closed which would not really be normal |
| `00:06:57.120 - 00:06:59.990` | the kernel will allocate entries into |
| `00:07:00.000 - 00:07:02.950` | the o file array on each |
| `00:07:02.960 - 00:07:06.070` | calls to the open system call and it |
| `00:07:06.080 - 00:07:09.189` | will use the next available unused slot |
| `00:07:09.199 - 00:07:09.990` | so |
| `00:07:10.000 - 00:07:12.790` | apparently standard error was open but |
| `00:07:12.800 - 00:07:16.309` | it has been closed |
| `00:07:16.319 - 00:07:18.309` | now let's take a look at what happens |
| `00:07:18.319 - 00:07:19.189` | when |
| `00:07:19.199 - 00:07:21.990` | this parent forks a child |
| `00:07:22.000 - 00:07:22.870` | so |
| `00:07:22.880 - 00:07:24.790` | the fork process will copy the proc |
| `00:07:24.800 - 00:07:27.189` | structure into a new proc structure and |
| `00:07:27.199 - 00:07:30.550` | in particular it will copy the open file |
| `00:07:30.560 - 00:07:34.070` | array into the child's proc structure |
| `00:07:34.080 - 00:07:35.589` | okay so |
| `00:07:35.599 - 00:07:37.510` | that means that every file that is open |
| `00:07:37.520 - 00:07:40.070` | in the parent such as this pipe right |
| `00:07:40.080 - 00:07:43.350` | here or this file right here will be |
| `00:07:43.360 - 00:07:45.990` | open in the child so the red lines |
| `00:07:46.000 - 00:07:47.749` | indicate that these pointers have just |
| `00:07:47.759 - 00:07:50.309` | been copied so this pointer here is |
| `00:07:50.319 - 00:07:52.230` | copied as well |
| `00:07:52.240 - 00:07:55.029` | and initially uh file descriptor number |
| `00:07:55.039 - 00:07:55.830` | two |
| `00:07:55.840 - 00:07:59.029` | is copied it was closed so it's a zero |
| `00:07:59.039 - 00:08:01.110` | here |
| `00:08:01.120 - 00:08:03.189` | now let's say that the child decides to |
| `00:08:03.199 - 00:08:05.270` | open a file |
| `00:08:05.280 - 00:08:07.510` | the open system call will find the next |
| `00:08:07.520 - 00:08:09.749` | available unused file descriptor which |
| `00:08:09.759 - 00:08:11.749` | happens to be two so it will use this |
| `00:08:11.759 - 00:08:14.950` | slot right here and it will |
| `00:08:14.960 - 00:08:16.309` | open the file |
| `00:08:16.319 - 00:08:17.990` | we've provided a path name to the open |
| `00:08:18.000 - 00:08:20.150` | system call and it turns out that that |
| `00:08:20.160 - 00:08:22.309` | path name happens to refer to the exact |
| `00:08:22.319 - 00:08:23.510` | same file |
| `00:08:23.520 - 00:08:26.950` | that file descriptor 1 refers to |
| `00:08:26.960 - 00:08:29.350` | file descriptor 1 had this file open for |
| `00:08:29.360 - 00:08:31.589` | writing and was positioned on |
| `00:08:31.599 - 00:08:34.870` | offset 47 but when we open it here |
| `00:08:34.880 - 00:08:37.269` | apparently we're opening it for read on |
| `00:08:37.279 - 00:08:40.469` | reading and read only and the offset is |
| `00:08:40.479 - 00:08:42.550` | initialized to zero so |
| `00:08:42.560 - 00:08:45.590` | any read using file descriptor two will |
| `00:08:45.600 - 00:08:47.430` | come from the file starting at offset |
| `00:08:47.440 - 00:08:48.550` | zero |
| `00:08:48.560 - 00:08:50.550` | and if we write to the file |
| `00:08:50.560 - 00:08:53.750` | using offset one it will write |
| `00:08:53.760 - 00:08:56.230` | characters starting at offset 47 and |
| `00:08:56.240 - 00:08:57.430` | increment them |
| `00:08:57.440 - 00:08:59.269` | so |
| `00:08:59.279 - 00:09:01.590` | this the file structure shows that |
| `00:09:01.600 - 00:09:03.590` | there's a sort of an intermediate layer |
| `00:09:03.600 - 00:09:06.070` | between the proc structure and the |
| `00:09:06.080 - 00:09:08.870` | inodes or pipes and that intermediate |
| `00:09:08.880 - 00:09:10.949` | structure is used for a number of things |
| `00:09:10.959 - 00:09:13.269` | and in particular it's used for |
| `00:09:13.279 - 00:09:14.150` | the |
| `00:09:14.160 - 00:09:16.150` | readable and writable flags so you can |
| `00:09:16.160 - 00:09:18.790` | have one file that's open |
| `00:09:18.800 - 00:09:22.710` | for writing by one process and |
| `00:09:22.720 - 00:09:25.430` | for a different access protection in |
| `00:09:25.440 - 00:09:28.790` | this case reading by another process |
| `00:09:28.800 - 00:09:30.949` | and it also contains a field for the |
| `00:09:30.959 - 00:09:31.990` | offset |
| `00:09:32.000 - 00:09:34.630` | so one file could be what sorry one |
| `00:09:34.640 - 00:09:36.949` | process could be at one point in the |
| `00:09:36.959 - 00:09:37.910` | file |
| `00:09:37.920 - 00:09:39.430` | using its |
| `00:09:39.440 - 00:09:40.790` | file structure |
| `00:09:40.800 - 00:09:42.870` | and another process could be at another |
| `00:09:42.880 - 00:09:47.750` | point in that file using its offset |
| `00:09:47.760 - 00:09:49.590` | file structures are a critical kernel |
| `00:09:49.600 - 00:09:50.470` | resource |
| `00:09:50.480 - 00:09:51.829` | so next we need to look at how this |
| `00:09:51.839 - 00:09:54.630` | resource is managed by the kernel |
| `00:09:54.640 - 00:09:56.150` | there are a fixed number of file |
| `00:09:56.160 - 00:09:58.150` | structures that are allocated startup |
| `00:09:58.160 - 00:10:01.190` | time and they are kept in an array shown |
| `00:10:01.200 - 00:10:02.949` | here |
| `00:10:02.959 - 00:10:05.670` | this array has size 100 the exact size |
| `00:10:05.680 - 00:10:09.190` | is determined by this constant infile |
| `00:10:09.200 - 00:10:11.670` | every structure in this array is either |
| `00:10:11.680 - 00:10:14.710` | in use or is free and available for |
| `00:10:14.720 - 00:10:17.190` | future use |
| `00:10:17.200 - 00:10:19.269` | there's a variable called f table that |
| `00:10:19.279 - 00:10:21.430` | contains a structure and that structure |
| `00:10:21.440 - 00:10:24.389` | has two fields lock and file |
| `00:10:24.399 - 00:10:26.310` | lock is a spin lock |
| `00:10:26.320 - 00:10:31.990` | and file is the array of 100 structures |
| `00:10:32.000 - 00:10:33.670` | the file structures |
| `00:10:33.680 - 00:10:35.990` | uh have these fields we saw these before |
| `00:10:36.000 - 00:10:38.470` | we have a type field a reference count |
| `00:10:38.480 - 00:10:41.829` | the readable and writable flags the pipe |
| `00:10:41.839 - 00:10:45.110` | pointer the ip pointer the offset and |
| `00:10:45.120 - 00:10:47.190` | the major number |
| `00:10:47.200 - 00:10:49.430` | the reference count tells whether the |
| `00:10:49.440 - 00:10:51.990` | structure is in use or not |
| `00:10:52.000 - 00:10:54.949` | if a structure is free and unused and |
| `00:10:54.959 - 00:10:56.790` | available for future use then the |
| `00:10:56.800 - 00:10:59.030` | reference count will be zero on the |
| `00:10:59.040 - 00:11:00.710` | other hand if it's in use then the |
| `00:11:00.720 - 00:11:02.470` | reference count will be |
| `00:11:02.480 - 00:11:03.990` | one or greater |
| `00:11:04.000 - 00:11:05.430` | so the reference count keeps track of |
| `00:11:05.440 - 00:11:07.670` | how many pointers are pointing to this |
| `00:11:07.680 - 00:11:09.350` | file structure |
| `00:11:09.360 - 00:11:11.750` | the where are these pointers coming from |
| `00:11:11.760 - 00:11:13.670` | well remember that a process will have a |
| `00:11:13.680 - 00:11:15.750` | number of open files |
| `00:11:15.760 - 00:11:18.389` | and each open file is given a file |
| `00:11:18.399 - 00:11:21.910` | descriptor and that's a slot in this |
| `00:11:21.920 - 00:11:26.069` | array called o-file so the o-file arrays |
| `00:11:26.079 - 00:11:28.630` | contain pointers to the file structures |
| `00:11:28.640 - 00:11:30.230` | so that's where they're coming from and |
| `00:11:30.240 - 00:11:31.829` | reference count counts the number of |
| `00:11:31.839 - 00:11:35.110` | pointers to this particular object |
| `00:11:35.120 - 00:11:38.790` | the type field can either be fd none fbi |
| `00:11:38.800 - 00:11:42.470` | node fd device or fd pipe |
| `00:11:42.480 - 00:11:43.910` | if the |
| `00:11:43.920 - 00:11:46.470` | structure is unused then its type field |
| `00:11:46.480 - 00:11:48.069` | will be none |
| `00:11:48.079 - 00:11:50.230` | but really the reference count is what |
| `00:11:50.240 - 00:11:52.310` | determines whether the structure is |
| `00:11:52.320 - 00:11:56.310` | unused or whether it's in use |
| `00:11:56.320 - 00:11:58.550` | the pipe field will contain a pointer to |
| `00:11:58.560 - 00:12:01.190` | a pipe structure if and only if the type |
| `00:12:01.200 - 00:12:03.590` | is fb pipe |
| `00:12:03.600 - 00:12:06.069` | the ip pointer will contain a pointer to |
| `00:12:06.079 - 00:12:08.470` | an inode and that will be used if and |
| `00:12:08.480 - 00:12:11.110` | only if the type is either |
| `00:12:11.120 - 00:12:14.550` | fdi node or fd device |
| `00:12:14.560 - 00:12:18.470` | if it is inode then that means that this |
| `00:12:18.480 - 00:12:20.949` | is pointing to a device |
| `00:12:20.959 - 00:12:23.430` | file or a regular file and so the offset |
| `00:12:23.440 - 00:12:25.350` | will be valid |
| `00:12:25.360 - 00:12:27.829` | on the other hand if the inode is a |
| `00:12:27.839 - 00:12:30.629` | device type then the major number will |
| `00:12:30.639 - 00:12:33.350` | be used |
| `00:12:33.360 - 00:12:35.509` | okay so this pin lock protects the |
| `00:12:35.519 - 00:12:37.269` | reference count we need to acquire the |
| `00:12:37.279 - 00:12:39.910` | spin lock before allocating structures |
| `00:12:39.920 - 00:12:41.910` | and every time we increment or decrement |
| `00:12:41.920 - 00:12:44.389` | the reference count we better first |
| `00:12:44.399 - 00:12:48.790` | acquire the spin lock |
| `00:12:48.800 - 00:12:50.790` | offset will be changed |
| `00:12:50.800 - 00:12:51.829` | and |
| `00:12:51.839 - 00:12:53.750` | since there can be multiple processes |
| `00:12:53.760 - 00:12:55.750` | referring to this object it needs to be |
| `00:12:55.760 - 00:12:57.509` | protected by a lock |
| `00:12:57.519 - 00:12:59.190` | however there is no lock in this |
| `00:12:59.200 - 00:13:00.389` | structure |
| `00:13:00.399 - 00:13:03.110` | instead the offset field is protected by |
| `00:13:03.120 - 00:13:06.230` | the lock that is in the inode structure |
| `00:13:06.240 - 00:13:08.629` | so if this thing is a directory file or |
| `00:13:08.639 - 00:13:11.030` | a regular file then ip will contain a |
| `00:13:11.040 - 00:13:13.030` | pointer to an inode and remember that |
| `00:13:13.040 - 00:13:14.790` | inodes contain |
| `00:13:14.800 - 00:13:17.030` | a sleep block so that's the lock that |
| `00:13:17.040 - 00:13:19.509` | has to be acquired before the offset is |
| `00:13:19.519 - 00:13:21.670` | read or modified so that's sort of |
| `00:13:21.680 - 00:13:23.269` | unusual |
| `00:13:23.279 - 00:13:25.350` | and uh the other fields in this |
| `00:13:25.360 - 00:13:26.470` | structure |
| `00:13:26.480 - 00:13:29.110` | don't change they're initialized when |
| `00:13:29.120 - 00:13:30.790` | the structure is allocated and they |
| `00:13:30.800 - 00:13:32.389` | don't change so therefore they don't |
| `00:13:32.399 - 00:13:34.870` | need a lock so the other fields do not |
| `00:13:34.880 - 00:13:37.430` | change once the file structure is |
| `00:13:37.440 - 00:13:39.269` | allocated and the reference count goes |
| `00:13:39.279 - 00:13:42.230` | from zero to one |
| `00:13:42.240 - 00:13:43.829` | okay let's begin by taking a look at the |
| `00:13:43.839 - 00:13:45.670` | code that initializes this data |
| `00:13:45.680 - 00:13:47.430` | structure and this is coming from the |
| `00:13:47.440 - 00:13:51.670` | file file dot c |
| `00:13:51.680 - 00:13:54.069` | so we see the definition of the variable |
| `00:13:54.079 - 00:13:57.990` | f table that's got two fields lock and |
| `00:13:58.000 - 00:13:58.949` | file |
| `00:13:58.959 - 00:14:02.470` | file is the array of these 100 file |
| `00:14:02.480 - 00:14:03.910` | structures |
| `00:14:03.920 - 00:14:06.069` | remember that within with c when you |
| `00:14:06.079 - 00:14:07.750` | define a variable the name of the |
| `00:14:07.760 - 00:14:10.150` | variable comes after the type and before |
| `00:14:10.160 - 00:14:11.829` | the semicolon |
| `00:14:11.839 - 00:14:14.310` | sometimes we see a name here and an |
| `00:14:14.320 - 00:14:17.110` | example of that would be the file |
| `00:14:17.120 - 00:14:19.189` | structure in that case we're defining a |
| `00:14:19.199 - 00:14:21.189` | new name which we can use in other |
| `00:14:21.199 - 00:14:23.509` | places to refer to this particular |
| `00:14:23.519 - 00:14:24.710` | structure |
| `00:14:24.720 - 00:14:27.030` | but in this example we see no variable |
| `00:14:27.040 - 00:14:28.470` | being created |
| `00:14:28.480 - 00:14:31.430` | in fact that name file is used right |
| `00:14:31.440 - 00:14:33.350` | here and this is where we are actually |
| `00:14:33.360 - 00:14:35.350` | creating some instances of the file |
| `00:14:35.360 - 00:14:36.470` | structure |
| `00:14:36.480 - 00:14:38.629` | but for this example the |
| `00:14:38.639 - 00:14:40.470` | structure will be anonymous we don't |
| `00:14:40.480 - 00:14:42.389` | ever need to refer to this type |
| `00:14:42.399 - 00:14:45.110` | anywhere else |
| `00:14:45.120 - 00:14:47.350` | this function filenet is called from the |
| `00:14:47.360 - 00:14:48.710` | main function |
| `00:14:48.720 - 00:14:50.870` | as it's running on core zero only so |
| `00:14:50.880 - 00:14:53.350` | it'll get executed and it simply |
| `00:14:53.360 - 00:14:56.550` | initializes the spin lock that goes |
| `00:14:56.560 - 00:14:59.590` | along with this f table structure so |
| `00:14:59.600 - 00:15:01.829` | that's the spin lock right here that |
| `00:15:01.839 - 00:15:03.670` | protects all the reference fields in the |
| `00:15:03.680 - 00:15:05.350` | file structures |
| `00:15:05.360 - 00:15:06.550` | these things are assumed to be |
| `00:15:06.560 - 00:15:08.550` | initialized to zero so we don't see code |
| `00:15:08.560 - 00:15:12.790` | to actually initialize those |
| `00:15:12.800 - 00:15:15.110` | whenever we are ready to open a file we |
| `00:15:15.120 - 00:15:17.910` | need to locate an unused file structure |
| `00:15:17.920 - 00:15:20.710` | in the array of 100 file structures and |
| `00:15:20.720 - 00:15:22.389` | allocate it and that's the purpose of |
| `00:15:22.399 - 00:15:25.269` | the file alec function |
| `00:15:25.279 - 00:15:28.470` | so it will acquire the spin lock here |
| `00:15:28.480 - 00:15:30.629` | and then before returning whether we |
| `00:15:30.639 - 00:15:33.430` | return here or here it will release the |
| `00:15:33.440 - 00:15:35.829` | spin lock and what is it going to do |
| `00:15:35.839 - 00:15:38.550` | well it's going to go through the file |
| `00:15:38.560 - 00:15:40.870` | array in the f table function in the f |
| `00:15:40.880 - 00:15:42.230` | table variable |
| `00:15:42.240 - 00:15:43.269` | and |
| `00:15:43.279 - 00:15:45.829` | find one where the reference count is |
| `00:15:45.839 - 00:15:47.910` | zero and then it's going to increment |
| `00:15:47.920 - 00:15:50.310` | the reference count to one and return a |
| `00:15:50.320 - 00:15:53.269` | pointer to that file structure and if |
| `00:15:53.279 - 00:15:54.949` | for some reason there are no more |
| `00:15:54.959 - 00:15:56.790` | available it will return the null |
| `00:15:56.800 - 00:15:59.590` | pointer |
| `00:15:59.600 - 00:16:01.829` | now let's take a look at the file dupe |
| `00:16:01.839 - 00:16:03.509` | function |
| `00:16:03.519 - 00:16:06.069` | from time to time we will have a process |
| `00:16:06.079 - 00:16:09.749` | and we will fork it so here's the parent |
| `00:16:09.759 - 00:16:13.030` | process and we will need to create a |
| `00:16:13.040 - 00:16:15.590` | child process remember that |
| `00:16:15.600 - 00:16:16.550` | the |
| `00:16:16.560 - 00:16:19.030` | proc structure contains this array |
| `00:16:19.040 - 00:16:21.749` | of open files and pointing to various |
| `00:16:21.759 - 00:16:23.269` | file structures |
| `00:16:23.279 - 00:16:26.230` | and we are copying this array and |
| `00:16:26.240 - 00:16:28.870` | therefore we are adding pointers uh to |
| `00:16:28.880 - 00:16:31.189` | each of the file objects that were |
| `00:16:31.199 - 00:16:33.670` | pointed to by the parent and so we need |
| `00:16:33.680 - 00:16:35.350` | to go through these file structures and |
| `00:16:35.360 - 00:16:37.670` | increment the reference counts and |
| `00:16:37.680 - 00:16:39.430` | that's the purpose of the |
| `00:16:39.440 - 00:16:41.670` | file dupe function |
| `00:16:41.680 - 00:16:45.030` | it begins by acquiring the lock on the f |
| `00:16:45.040 - 00:16:45.990` | table |
| `00:16:46.000 - 00:16:47.590` | and then it increments the reference |
| `00:16:47.600 - 00:16:50.230` | count releases the lock and returns it |
| `00:16:50.240 - 00:16:51.590` | also does a quick check to make sure |
| `00:16:51.600 - 00:16:54.790` | that we are already holding a pointer to |
| `00:16:54.800 - 00:16:57.990` | that particular file object |
| `00:16:58.000 - 00:16:59.030` | next |
| `00:16:59.040 - 00:17:01.030` | at some point we are going to close |
| `00:17:01.040 - 00:17:03.030` | files and we need to return these file |
| `00:17:03.040 - 00:17:05.189` | structures to the free pool because they |
| `00:17:05.199 - 00:17:07.429` | are no longer in use |
| `00:17:07.439 - 00:17:09.510` | so we have this file close function |
| `00:17:09.520 - 00:17:11.110` | that's passed the pointer to some file |
| `00:17:11.120 - 00:17:12.710` | structure |
| `00:17:12.720 - 00:17:14.789` | the first thing it's going to do is |
| `00:17:14.799 - 00:17:17.270` | decrement the reference count so here we |
| `00:17:17.280 - 00:17:18.390` | acquire the |
| `00:17:18.400 - 00:17:21.429` | lock on the table of 100 file structures |
| `00:17:21.439 - 00:17:24.789` | uh and we decrement the reference count |
| `00:17:24.799 - 00:17:26.069` | right here |
| `00:17:26.079 - 00:17:27.429` | and uh |
| `00:17:27.439 - 00:17:30.630` | then if it has not gone to zero that is |
| `00:17:30.640 - 00:17:33.430` | er we're pre-decrementing it here and if |
| `00:17:33.440 - 00:17:35.750` | it's still not zero then there's nothing |
| `00:17:35.760 - 00:17:36.950` | to be done we don't need to free |
| `00:17:36.960 - 00:17:39.350` | anything there is another pointer still |
| `00:17:39.360 - 00:17:42.549` | in existence so uh we can just release |
| `00:17:42.559 - 00:17:45.190` | the lock and return immediately |
| `00:17:45.200 - 00:17:47.029` | we've also got a check here to make sure |
| `00:17:47.039 - 00:17:48.710` | that the reference count is not already |
| `00:17:48.720 - 00:17:49.830` | zero |
| `00:17:49.840 - 00:17:52.310` | but if the reference count has gone to |
| `00:17:52.320 - 00:17:54.950` | zero then we need to do additional work |
| `00:17:54.960 - 00:17:57.590` | and that's shown here |
| `00:17:57.600 - 00:18:01.190` | so this ff variable is a local variable |
| `00:18:01.200 - 00:18:02.630` | in this function |
| `00:18:02.640 - 00:18:04.950` | that is a file structure so we're going |
| `00:18:04.960 - 00:18:07.029` | to copy the entire file structure right |
| `00:18:07.039 - 00:18:08.310` | here |
| `00:18:08.320 - 00:18:10.230` | and |
| `00:18:10.240 - 00:18:12.870` | then we set its reference count to zero |
| `00:18:12.880 - 00:18:15.990` | thereby making it a free and unused file |
| `00:18:16.000 - 00:18:18.710` | structure in the table of a hundred file |
| `00:18:18.720 - 00:18:20.870` | structures and we're also setting its |
| `00:18:20.880 - 00:18:23.190` | type to fd none |
| `00:18:23.200 - 00:18:24.549` | and then at that point we can release |
| `00:18:24.559 - 00:18:26.549` | the lock |
| `00:18:26.559 - 00:18:27.590` | however |
| `00:18:27.600 - 00:18:29.350` | this file structure |
| `00:18:29.360 - 00:18:31.270` | may be pointing to some other structures |
| `00:18:31.280 - 00:18:32.870` | that we need to take care of |
| `00:18:32.880 - 00:18:35.190` | it may be pointing to a pipe |
| `00:18:35.200 - 00:18:38.710` | or it may be pointing to an inode |
| `00:18:38.720 - 00:18:41.029` | so that's why we made a copy of it so |
| `00:18:41.039 - 00:18:44.390` | that we can access the pipe pointer here |
| `00:18:44.400 - 00:18:47.669` | or the ip pointer here |
| `00:18:47.679 - 00:18:49.669` | so if the type of the structure that we |
| `00:18:49.679 - 00:18:51.350` | just released |
| `00:18:51.360 - 00:18:52.230` | was |
| `00:18:52.240 - 00:18:55.270` | fd pipe then it will |
| `00:18:55.280 - 00:18:58.710` | contain a pointer to a pipe structure |
| `00:18:58.720 - 00:19:01.190` | right there will be exactly two pointers |
| `00:19:01.200 - 00:19:02.950` | to a pipe structure one from the read |
| `00:19:02.960 - 00:19:03.830` | end |
| `00:19:03.840 - 00:19:06.870` | and one from the right end and whichever |
| `00:19:06.880 - 00:19:09.590` | one we are we need to |
| `00:19:09.600 - 00:19:11.029` | execute the |
| `00:19:11.039 - 00:19:13.909` | pipe close function to |
| `00:19:13.919 - 00:19:16.070` | possibly free that structure |
| `00:19:16.080 - 00:19:17.669` | okay |
| `00:19:17.679 - 00:19:19.270` | so here we're calling pipe close which |
| `00:19:19.280 - 00:19:21.430` | we looked at before and is this the read |
| `00:19:21.440 - 00:19:23.350` | end or the right end well we're passing |
| `00:19:23.360 - 00:19:25.669` | it the value of the writable flag to |
| `00:19:25.679 - 00:19:27.350` | determine that |
| `00:19:27.360 - 00:19:29.750` | on the other hand if the type was |
| `00:19:29.760 - 00:19:32.630` | fdi node or fd device |
| `00:19:32.640 - 00:19:33.909` | then |
| `00:19:33.919 - 00:19:35.590` | we are |
| `00:19:35.600 - 00:19:37.669` | going to need to deal with the inode |
| `00:19:37.679 - 00:19:39.190` | that's pointed to |
| `00:19:39.200 - 00:19:39.990` | so |
| `00:19:40.000 - 00:19:42.230` | for that we will use the i put function |
| `00:19:42.240 - 00:19:44.230` | which we covered earlier but essentially |
| `00:19:44.240 - 00:19:45.830` | it's going to decrement the reference |
| `00:19:45.840 - 00:19:48.870` | count for this inode and if there are no |
| `00:19:48.880 - 00:19:51.510` | other pointers to it it will free the |
| `00:19:51.520 - 00:19:52.630` | inode |
| `00:19:52.640 - 00:19:55.430` | structure in memory and furthermore if |
| `00:19:55.440 - 00:19:57.669` | they're uh if we're freeing the inode |
| `00:19:57.679 - 00:19:59.830` | structure and there are no hard links |
| `00:19:59.840 - 00:20:03.430` | left that is the in link field is zero |
| `00:20:03.440 - 00:20:04.470` | then |
| `00:20:04.480 - 00:20:06.630` | this will also remove the file from the |
| `00:20:06.640 - 00:20:07.750` | disk |
| `00:20:07.760 - 00:20:10.870` | so disk operations uh are involved here |
| `00:20:10.880 - 00:20:12.870` | so we need to have these inside of a |
| `00:20:12.880 - 00:20:15.830` | transaction so here we have the begin op |
| `00:20:15.840 - 00:20:18.830` | and the end up that uh define where the |
| `00:20:18.840 - 00:20:22.149` | transaction occurs |
| `00:20:22.159 - 00:20:24.070` | there are three more functions in the |
| `00:20:24.080 - 00:20:27.590` | file file.c there's file staff |
| `00:20:27.600 - 00:20:29.590` | there's file read and there's a file |
| `00:20:29.600 - 00:20:30.870` | write function |
| `00:20:30.880 - 00:20:32.230` | in the remainder of this video i'll just |
| `00:20:32.240 - 00:20:34.710` | talk about file stat and i'll cover the |
| `00:20:34.720 - 00:20:37.350` | others in a future video |
| `00:20:37.360 - 00:20:39.830` | file stat is used for the f-stat system |
| `00:20:39.840 - 00:20:42.149` | call so let's quickly review what the |
| `00:20:42.159 - 00:20:45.430` | f-stat system call does |
| `00:20:45.440 - 00:20:46.710` | this uh |
| `00:20:46.720 - 00:20:48.630` | system call is available in both unix |
| `00:20:48.640 - 00:20:51.510` | and xv6 and has more or less the same |
| `00:20:51.520 - 00:20:52.950` | definition |
| `00:20:52.960 - 00:20:54.870` | it's passed a small integer which |
| `00:20:54.880 - 00:20:57.110` | indicates which file we're interested in |
| `00:20:57.120 - 00:20:58.870` | and a pointer to some place in the |
| `00:20:58.880 - 00:21:00.870` | user's virtual address space |
| `00:21:00.880 - 00:21:04.470` | and what it will do is it will move into |
| `00:21:04.480 - 00:21:06.470` | the user's virtual address space some |
| `00:21:06.480 - 00:21:08.710` | information about this file |
| `00:21:08.720 - 00:21:09.750` | the |
| `00:21:09.760 - 00:21:12.070` | memory area is laid out according to |
| `00:21:12.080 - 00:21:14.870` | this stat structure and contains the |
| `00:21:14.880 - 00:21:16.710` | device number the i number the type of |
| `00:21:16.720 - 00:21:18.310` | the file the number of hard links and |
| `00:21:18.320 - 00:21:20.149` | the size of the file |
| `00:21:20.159 - 00:21:21.110` | and |
| `00:21:21.120 - 00:21:24.070` | then it will return zero if everything |
| `00:21:24.080 - 00:21:26.549` | was okay or minus one if there was an |
| `00:21:26.559 - 00:21:27.669` | error |
| `00:21:27.679 - 00:21:30.390` | in unix we have the error number |
| `00:21:30.400 - 00:21:33.029` | variable and the kernel will also set |
| `00:21:33.039 - 00:21:36.070` | that to indicate what caused the error |
| `00:21:36.080 - 00:21:38.549` | if there was an error but in xv6 we |
| `00:21:38.559 - 00:21:40.390` | don't mess with the error we don't have |
| `00:21:40.400 - 00:21:41.990` | the error number |
| `00:21:42.000 - 00:21:45.190` | system going on we don't use that at all |
| `00:21:45.200 - 00:21:47.430` | okay so |
| `00:21:47.440 - 00:21:48.470` | when a |
| `00:21:48.480 - 00:21:50.310` | system call occurs |
| `00:21:50.320 - 00:21:52.310` | the kernel will get control from the |
| `00:21:52.320 - 00:21:55.510` | user's program and it will look into a |
| `00:21:55.520 - 00:21:58.230` | register to determine which system call |
| `00:21:58.240 - 00:21:59.110` | it is |
| `00:21:59.120 - 00:22:01.909` | and then it will invoke one of these cis |
| `00:22:01.919 - 00:22:04.870` | functions to actually do the work so in |
| `00:22:04.880 - 00:22:07.350` | the case of the f-stat system call the |
| `00:22:07.360 - 00:22:10.950` | kernel will then invoke sys f-stat which |
| `00:22:10.960 - 00:22:13.190` | is coming from file |
| `00:22:13.200 - 00:22:16.149` | sysfile.c |
| `00:22:16.159 - 00:22:18.789` | these functions are passed no arguments |
| `00:22:18.799 - 00:22:20.230` | and they return |
| `00:22:20.240 - 00:22:21.270` | the |
| `00:22:21.280 - 00:22:22.870` | value that should be returned to the |
| `00:22:22.880 - 00:22:24.149` | user's |
| `00:22:24.159 - 00:22:26.470` | code so they return either zero or minus |
| `00:22:26.480 - 00:22:29.190` | one in this case |
| `00:22:29.200 - 00:22:31.110` | so what does this thing do |
| `00:22:31.120 - 00:22:33.990` | well it needs to first obtain the |
| `00:22:34.000 - 00:22:36.149` | arguments that the user code was trying |
| `00:22:36.159 - 00:22:37.510` | to pass in |
| `00:22:37.520 - 00:22:40.710` | arguments are passed in registers |
| `00:22:40.720 - 00:22:43.270` | and those registers got saved so we have |
| `00:22:43.280 - 00:22:45.110` | to call a function |
| `00:22:45.120 - 00:22:45.909` | and |
| `00:22:45.919 - 00:22:49.110` | functions like arg int and arg address |
| `00:22:49.120 - 00:22:52.230` | were reviewed in a video a long time ago |
| `00:22:52.240 - 00:22:53.590` | by me |
| `00:22:53.600 - 00:22:56.950` | and what they do is they're past the |
| `00:22:56.960 - 00:22:58.470` | number of the argument you want to |
| `00:22:58.480 - 00:23:00.549` | retrieve so here we are retrieving the |
| `00:23:00.559 - 00:23:02.710` | first argument zero and the second |
| `00:23:02.720 - 00:23:05.430` | argument number one and in the case of |
| `00:23:05.440 - 00:23:08.230` | arg address that will be a virtual |
| `00:23:08.240 - 00:23:11.029` | address so we're passed where to store |
| `00:23:11.039 - 00:23:11.830` | that |
| `00:23:11.840 - 00:23:14.789` | okay so this is a local variable and |
| `00:23:14.799 - 00:23:18.149` | this arg address function will |
| `00:23:18.159 - 00:23:21.430` | find the saved register and move the |
| `00:23:21.440 - 00:23:22.870` | value into |
| `00:23:22.880 - 00:23:26.950` | the location st here and if there is any |
| `00:23:26.960 - 00:23:30.230` | problem it will return -1 and |
| `00:23:30.240 - 00:23:33.430` | we'll immediately return -1 |
| `00:23:33.440 - 00:23:36.470` | the arg fd function i have not looked at |
| `00:23:36.480 - 00:23:38.549` | but it's a similar kind of thing it's |
| `00:23:38.559 - 00:23:41.269` | passed the register number |
| `00:23:41.279 - 00:23:43.350` | that is the argument number that we want |
| `00:23:43.360 - 00:23:46.230` | to get and a place where we want to |
| `00:23:46.240 - 00:23:47.350` | store |
| `00:23:47.360 - 00:23:49.510` | it now this is a file descriptor so |
| `00:23:49.520 - 00:23:50.710` | we're past |
| `00:23:50.720 - 00:23:53.190` | and the address of f and so |
| `00:23:53.200 - 00:23:56.630` | this function here will obtain |
| `00:23:56.640 - 00:23:58.070` | a pointer to |
| `00:23:58.080 - 00:23:58.789` | the |
| `00:23:58.799 - 00:24:01.909` | file structure for this file |
| `00:24:01.919 - 00:24:03.190` | for this |
| `00:24:03.200 - 00:24:08.710` | and so let's take a quick look at arg fd |
| `00:24:08.720 - 00:24:12.070` | here's the code fetch the nth |
| `00:24:12.080 - 00:24:13.669` | system call argument |
| `00:24:13.679 - 00:24:15.510` | as a file descriptor |
| `00:24:15.520 - 00:24:18.230` | return both the file descriptor and the |
| `00:24:18.240 - 00:24:20.149` | pointer to the corresponding file |
| `00:24:20.159 - 00:24:21.909` | structure |
| `00:24:21.919 - 00:24:23.430` | okay so |
| `00:24:23.440 - 00:24:24.149` | it |
| `00:24:24.159 - 00:24:26.789` | is passed the number of the argument and |
| `00:24:26.799 - 00:24:28.789` | it's passed two pointers |
| `00:24:28.799 - 00:24:30.630` | okay that's where to store the file |
| `00:24:30.640 - 00:24:33.110` | descriptor that's a small number and |
| `00:24:33.120 - 00:24:35.110` | this is where to store a pointer to the |
| `00:24:35.120 - 00:24:38.149` | file structure |
| `00:24:38.159 - 00:24:41.269` | so it's going to start by calling argent |
| `00:24:41.279 - 00:24:45.110` | and passing it in so that gets the first |
| `00:24:45.120 - 00:24:47.110` | argument or or the nth argument i should |
| `00:24:47.120 - 00:24:49.110` | say and stores it |
| `00:24:49.120 - 00:24:51.430` | in this local variable fd and if there's |
| `00:24:51.440 - 00:24:53.830` | a problem with that it returns -1 |
| `00:24:53.840 - 00:24:56.310` | then it checks this file descriptor that |
| `00:24:56.320 - 00:24:58.630` | we just retrieved okay to make sure it's |
| `00:24:58.640 - 00:25:01.510` | valid is it zero or greater but |
| `00:25:01.520 - 00:25:03.990` | not greater than uh the number of files |
| `00:25:04.000 - 00:25:06.390` | that we have uh the greatest possible |
| `00:25:06.400 - 00:25:08.390` | number of open files |
| `00:25:08.400 - 00:25:12.070` | and then we look at the o file array and |
| `00:25:12.080 - 00:25:15.110` | see whether it's null or not okay so |
| `00:25:15.120 - 00:25:16.549` | here we're going to |
| `00:25:16.559 - 00:25:19.750` | the current procedures uh proc structure |
| `00:25:19.760 - 00:25:22.390` | and we're looking at the open file array |
| `00:25:22.400 - 00:25:24.149` | in my picture |
| `00:25:24.159 - 00:25:26.630` | here is the proc structure for a |
| `00:25:26.640 - 00:25:28.870` | procedure and we are looking at its o |
| `00:25:28.880 - 00:25:30.950` | file structure we make sure that the |
| `00:25:30.960 - 00:25:33.269` | index is not |
| `00:25:33.279 - 00:25:35.669` | less than zero or greater than the |
| `00:25:35.679 - 00:25:38.710` | number of entries that we have here |
| `00:25:38.720 - 00:25:42.630` | determined by the number in o file and |
| `00:25:42.640 - 00:25:44.070` | then we check to make sure that it's not |
| `00:25:44.080 - 00:25:46.549` | a null pointer and if so we're going to |
| `00:25:46.559 - 00:25:48.310` | trace it and return a pointer to the |
| `00:25:48.320 - 00:25:50.549` | file structure so that's what's going on |
| `00:25:50.559 - 00:25:52.950` | here |
| `00:25:52.960 - 00:25:54.470` | we check it to make sure it's within |
| `00:25:54.480 - 00:25:57.110` | range and not null |
| `00:25:57.120 - 00:25:59.590` | and then |
| `00:25:59.600 - 00:26:02.230` | we are grabbing it this pointer and |
| `00:26:02.240 - 00:26:04.950` | storing it in f |
| `00:26:04.960 - 00:26:05.909` | if |
| `00:26:05.919 - 00:26:08.310` | we want to capture the file descriptor |
| `00:26:08.320 - 00:26:10.149` | itself then this will be a pointer to |
| `00:26:10.159 - 00:26:12.710` | where to store that file descriptor a |
| `00:26:12.720 - 00:26:14.870` | small integer but otherwise it will be |
| `00:26:14.880 - 00:26:16.390` | null so if it's null we don't do |
| `00:26:16.400 - 00:26:18.310` | anything but if it is a pointer then we |
| `00:26:18.320 - 00:26:21.590` | store the file descriptor there and |
| `00:26:21.600 - 00:26:23.110` | this argument will be appointed to where |
| `00:26:23.120 - 00:26:25.830` | does where to store the pointed to the |
| `00:26:25.840 - 00:26:26.870` | file this |
| `00:26:26.880 - 00:26:28.950` | structure |
| `00:26:28.960 - 00:26:31.750` | and so if this is null we don't do |
| `00:26:31.760 - 00:26:34.310` | anything but if it is a pointer then we |
| `00:26:34.320 - 00:26:35.430` | store |
| `00:26:35.440 - 00:26:39.029` | the pointer to the file structure there |
| `00:26:39.039 - 00:26:42.630` | and then we return 0. |
| `00:26:42.640 - 00:26:44.710` | so now we're ready to go back to |
| `00:26:44.720 - 00:26:47.269` | system |
| `00:26:47.279 - 00:26:48.230` | we |
| `00:26:48.240 - 00:26:50.630` | have a pointer to the file structure |
| `00:26:50.640 - 00:26:51.510` | okay |
| `00:26:51.520 - 00:26:53.190` | we were not interested in the actual |
| `00:26:53.200 - 00:26:54.789` | file descriptor number so we didn't |
| `00:26:54.799 - 00:26:56.310` | bother saving that |
| `00:26:56.320 - 00:26:58.549` | there was a problem we returned -1 and |
| `00:26:58.559 - 00:27:01.430` | so now we call file stat to do the work |
| `00:27:01.440 - 00:27:03.750` | we're passing in a pointer to |
| `00:27:03.760 - 00:27:05.750` | the file structure as well as a virtual |
| `00:27:05.760 - 00:27:07.510` | address st |
| `00:27:07.520 - 00:27:10.070` | of the stat structure |
| `00:27:10.080 - 00:27:11.269` | so |
| `00:27:11.279 - 00:27:15.350` | now we're back in file.c and here is the |
| `00:27:15.360 - 00:27:16.789` | file stat |
| `00:27:16.799 - 00:27:18.549` | function |
| `00:27:18.559 - 00:27:20.549` | so we're uh |
| `00:27:20.559 - 00:27:22.230` | getting a pointer to the current |
| `00:27:22.240 - 00:27:23.350` | procedure |
| `00:27:23.360 - 00:27:26.630` | and then we are looking at this file |
| `00:27:26.640 - 00:27:28.070` | structure f |
| `00:27:28.080 - 00:27:30.710` | is it an inode |
| `00:27:30.720 - 00:27:33.110` | or is it a device |
| `00:27:33.120 - 00:27:36.950` | well if so then it's i p pointer will be |
| `00:27:36.960 - 00:27:39.830` | valid and so we can return something but |
| `00:27:39.840 - 00:27:43.430` | if it's a pipe then we have a problem |
| `00:27:43.440 - 00:27:45.909` | so we return -1 |
| `00:27:45.919 - 00:27:47.190` | so if it |
| `00:27:47.200 - 00:27:50.230` | is pointing to an i node that is if the |
| `00:27:50.240 - 00:27:52.389` | type is inode or device it's either a |
| `00:27:52.399 - 00:27:54.870` | regular file or a directory file |
| `00:27:54.880 - 00:27:56.950` | or it's a device |
| `00:27:56.960 - 00:27:59.029` | in either case the ip pointer will point |
| `00:27:59.039 - 00:28:01.510` | to something so |
| `00:28:01.520 - 00:28:03.909` | we're going to lock the inode that's |
| `00:28:03.919 - 00:28:05.669` | pointed to |
| `00:28:05.679 - 00:28:08.549` | and then we're going to use this stat i |
| `00:28:08.559 - 00:28:10.230` | function which i |
| `00:28:10.240 - 00:28:12.310` | talked about earlier and that is going |
| `00:28:12.320 - 00:28:15.029` | to copy the data from |
| `00:28:15.039 - 00:28:17.669` | the inode into the virtual address space |
| `00:28:17.679 - 00:28:19.510` | at this virtual address |
| `00:28:19.520 - 00:28:21.350` | and then we are |
| `00:28:21.360 - 00:28:23.029` | sorry it's going to copy it into the |
| `00:28:23.039 - 00:28:25.269` | kernel space and so here we have a |
| `00:28:25.279 - 00:28:27.190` | variable in the kernel space where we |
| `00:28:27.200 - 00:28:29.669` | are going to be storing the data and |
| `00:28:29.679 - 00:28:32.310` | then we unlock the inode and here we |
| `00:28:32.320 - 00:28:34.389` | copy it into the virtual address space |
| `00:28:34.399 - 00:28:36.710` | and one thing that xv6 does is it does a |
| `00:28:36.720 - 00:28:38.710` | lot of copying from here |
| `00:28:38.720 - 00:28:40.070` | you know copies that maybe you could |
| `00:28:40.080 - 00:28:41.909` | eliminate so maybe we could copy |
| `00:28:41.919 - 00:28:43.510` | directly into the virtual address space |
| `00:28:43.520 - 00:28:45.990` | with a different design but in any case |
| `00:28:46.000 - 00:28:48.230` | we're using stat i to copy it first to |
| `00:28:48.240 - 00:28:50.549` | this stat structure here and then we're |
| `00:28:50.559 - 00:28:52.870` | using copy out to copy it into the |
| `00:28:52.880 - 00:28:54.710` | virtual address space |
| `00:28:54.720 - 00:28:57.110` | so we are copying |
| `00:28:57.120 - 00:28:58.710` | to the virtual address space described |
| `00:28:58.720 - 00:29:00.310` | by this page table |
| `00:29:00.320 - 00:29:02.549` | and here is the virtual address that |
| `00:29:02.559 - 00:29:03.510` | we're |
| `00:29:03.520 - 00:29:05.350` | copying it to |
| `00:29:05.360 - 00:29:07.110` | and then here's where we're copying it |
| `00:29:07.120 - 00:29:10.230` | from namely this local st variable and |
| `00:29:10.240 - 00:29:12.230` | we're copying however many bytes that |
| `00:29:12.240 - 00:29:13.669` | structure takes |
| `00:29:13.679 - 00:29:14.549` | and if |
| `00:29:14.559 - 00:29:16.470` | there's a problem we return minus one |
| `00:29:16.480 - 00:29:18.549` | otherwise return zero and everything's |
| `00:29:18.559 - 00:29:19.750` | okay |
| `00:29:19.760 - 00:29:21.510` | so i'm going to talk about the read and |
| `00:29:21.520 - 00:29:25.350` | write functions in the next video so i |
| `00:29:25.360 - 00:29:29.559` | will see you in the next video |
