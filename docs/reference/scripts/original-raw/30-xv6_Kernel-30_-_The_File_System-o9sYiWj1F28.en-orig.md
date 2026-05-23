# xv6 Kernel-30: The File System

| Field | Value |
| --- | --- |
| Video ID | `o9sYiWj1F28` |
| URL | <https://www.youtube.com/watch?v=o9sYiWj1F28> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.880 - 00:00:02.550` | this video is part of a video course on |
| `00:00:02.560 - 00:00:05.349` | the xv6 operating system kernel |
| `00:00:05.359 - 00:00:07.110` | there's a lot of code involved with the |
| `00:00:07.120 - 00:00:10.230` | file system and i've broken it up into a |
| `00:00:10.240 - 00:00:12.390` | number of different videos so far i've |
| `00:00:12.400 - 00:00:14.950` | already talked about the buffer cache |
| `00:00:14.960 - 00:00:18.550` | system and the disk logging system |
| `00:00:18.560 - 00:00:20.150` | in future videos i'll talk about the |
| `00:00:20.160 - 00:00:21.109` | code |
| `00:00:21.119 - 00:00:23.269` | but in this video i'm going to focus on |
| `00:00:23.279 - 00:00:25.109` | how the file system is represented on |
| `00:00:25.119 - 00:00:25.990` | disk |
| `00:00:26.000 - 00:00:27.509` | so in this video i'll talk about the |
| `00:00:27.519 - 00:00:30.550` | disk organization and layout for |
| `00:00:30.560 - 00:00:33.670` | directories and files |
| `00:00:33.680 - 00:00:35.510` | previously when i talked about the |
| `00:00:35.520 - 00:00:36.549` | logging |
| `00:00:36.559 - 00:00:39.190` | system i showed this schematic image of |
| `00:00:39.200 - 00:00:41.990` | the disk where each one of these squares |
| `00:00:42.000 - 00:00:45.110` | is a block on disk and we had |
| `00:00:45.120 - 00:00:47.270` | some stuff including the log area as |
| `00:00:47.280 - 00:00:50.950` | well as a main block area |
| `00:00:50.960 - 00:00:53.270` | let's uh simplify that picture and just |
| `00:00:53.280 - 00:00:54.549` | look at the |
| `00:00:54.559 - 00:00:56.950` | disk block area so we just have a |
| `00:00:56.960 - 00:00:58.470` | sequential uh |
| `00:00:58.480 - 00:01:00.069` | set of |
| `00:01:00.079 - 00:01:03.110` | disk blocks on the disk and the code |
| `00:01:03.120 - 00:01:04.869` | that we've looked at already provides a |
| `00:01:04.879 - 00:01:07.030` | number of functions that we can use to |
| `00:01:07.040 - 00:01:10.469` | access these blocks and here i'm showing |
| `00:01:10.479 - 00:01:13.350` | what it would look like in the code to |
| `00:01:13.360 - 00:01:16.070` | access the blocks on the disk |
| `00:01:16.080 - 00:01:18.390` | in particular we have the function b |
| `00:01:18.400 - 00:01:19.350` | read |
| `00:01:19.360 - 00:01:21.030` | which can be used |
| `00:01:21.040 - 00:01:23.590` | to read a block in from the disk and we |
| `00:01:23.600 - 00:01:25.910` | provide a block number and it allocates |
| `00:01:25.920 - 00:01:28.630` | a buffer from the cache and reads the |
| `00:01:28.640 - 00:01:31.109` | disk block into memory and returns a |
| `00:01:31.119 - 00:01:33.350` | buffer pointer a pointer to that buffer |
| `00:01:33.360 - 00:01:34.469` | in memory |
| `00:01:34.479 - 00:01:36.789` | and then we have the log write function |
| `00:01:36.799 - 00:01:37.590` | where |
| `00:01:37.600 - 00:01:40.310` | we provide a pointer to a buffer and it |
| `00:01:40.320 - 00:01:42.950` | will write that buffer data back out |
| `00:01:42.960 - 00:01:44.469` | onto the disk |
| `00:01:44.479 - 00:01:46.550` | we can |
| `00:01:46.560 - 00:01:49.749` | read or update the data in that buffer |
| `00:01:49.759 - 00:01:51.350` | any time after we |
| `00:01:51.360 - 00:01:52.789` | call b read |
| `00:01:52.799 - 00:01:55.270` | and we can call log right multiple times |
| `00:01:55.280 - 00:01:57.030` | so we may write it and then modify it |
| `00:01:57.040 - 00:01:59.350` | some more and write it again |
| `00:01:59.360 - 00:02:01.429` | when we're done with that particular |
| `00:02:01.439 - 00:02:03.510` | buffer we need to call this function b |
| `00:02:03.520 - 00:02:04.550` | release |
| `00:02:04.560 - 00:02:05.670` | and |
| `00:02:05.680 - 00:02:09.430` | between the b read call and the release |
| `00:02:09.440 - 00:02:12.309` | the buffer will be locked which enforces |
| `00:02:12.319 - 00:02:14.150` | the fact that we have exclusive use of |
| `00:02:14.160 - 00:02:17.510` | that buffer only this thread can access |
| `00:02:17.520 - 00:02:20.309` | that particular buffer |
| `00:02:20.319 - 00:02:23.030` | now a collection of writes are |
| `00:02:23.040 - 00:02:26.390` | uh included in a transaction the call to |
| `00:02:26.400 - 00:02:28.550` | begin op signals where the transaction |
| `00:02:28.560 - 00:02:31.509` | begins and at the end of the transaction |
| `00:02:31.519 - 00:02:34.630` | we should invoke this function in op |
| `00:02:34.640 - 00:02:37.030` | there can be a number of calls to log |
| `00:02:37.040 - 00:02:39.350` | right within the transaction either to |
| `00:02:39.360 - 00:02:41.990` | the same buffer or to different buffers |
| `00:02:42.000 - 00:02:44.390` | and those are all grouped together and |
| `00:02:44.400 - 00:02:45.670` | the |
| `00:02:45.680 - 00:02:47.830` | logging system will enforce the fact |
| `00:02:47.840 - 00:02:51.430` | that either all of the rights within the |
| `00:02:51.440 - 00:02:54.550` | transaction are performed or none of |
| `00:02:54.560 - 00:02:57.030` | them are so it's an all or nothing |
| `00:02:57.040 - 00:02:59.670` | situation |
| `00:02:59.680 - 00:03:02.790` | a file system contains a single tree of |
| `00:03:02.800 - 00:03:04.869` | directories as well as a bunch of files |
| `00:03:04.879 - 00:03:06.790` | that are located in those directories |
| `00:03:06.800 - 00:03:09.030` | and all of these are on one single |
| `00:03:09.040 - 00:03:12.390` | device like a disk or a usb drive |
| `00:03:12.400 - 00:03:15.190` | the directories are organized as a tree |
| `00:03:15.200 - 00:03:17.350` | that is there are no cycles and it's not |
| `00:03:17.360 - 00:03:21.589` | a dag a dag is a directed acyclic graph |
| `00:03:21.599 - 00:03:24.309` | so here's an example of a file system |
| `00:03:24.319 - 00:03:27.430` | and i've shown the directories in red |
| `00:03:27.440 - 00:03:28.309` | and |
| `00:03:28.319 - 00:03:30.630` | the files that are not directories in |
| `00:03:30.640 - 00:03:32.630` | green and you can see that if you just |
| `00:03:32.640 - 00:03:34.710` | look at the red it is a tree in other |
| `00:03:34.720 - 00:03:36.789` | words every director directory has a |
| `00:03:36.799 - 00:03:39.910` | single parent except for the root |
| `00:03:39.920 - 00:03:42.869` | the other files that are not directories |
| `00:03:42.879 - 00:03:45.670` | are shown in green and in some cases you |
| `00:03:45.680 - 00:03:48.869` | can see that some of these files have |
| `00:03:48.879 - 00:03:50.470` | multiple parents |
| `00:03:50.480 - 00:03:51.990` | and of course you remember that we refer |
| `00:03:52.000 - 00:03:55.670` | to files by their path names the file |
| `00:03:55.680 - 00:03:58.309` | itself does not have a name instead we |
| `00:03:58.319 - 00:04:01.110` | refer to it or access it based on its |
| `00:04:01.120 - 00:04:03.830` | path name so for example this file here |
| `00:04:03.840 - 00:04:06.149` | could be referred to as slash a slash f |
| `00:04:06.159 - 00:04:07.429` | slash k |
| `00:04:07.439 - 00:04:09.589` | or we could also refer to the same file |
| `00:04:09.599 - 00:04:12.710` | using another path name |
| `00:04:12.720 - 00:04:15.429` | every directory is itself a file |
| `00:04:15.439 - 00:04:17.430` | um and so we have several different |
| `00:04:17.440 - 00:04:19.430` | types of files both directories and |
| `00:04:19.440 - 00:04:21.349` | regular files |
| `00:04:21.359 - 00:04:23.749` | in xv6 we also have |
| `00:04:23.759 - 00:04:26.230` | a file type called device |
| `00:04:26.240 - 00:04:28.469` | in fact there's only one device that is |
| `00:04:28.479 - 00:04:30.469` | located in the directory tree and that |
| `00:04:30.479 - 00:04:33.030` | is the console but in general we could |
| `00:04:33.040 - 00:04:35.030` | have other devices as well |
| `00:04:35.040 - 00:04:36.870` | those are the only three types of files |
| `00:04:36.880 - 00:04:38.950` | that xv6 implements |
| `00:04:38.960 - 00:04:39.990` | but |
| `00:04:40.000 - 00:04:42.390` | unix systems and linux systems implement |
| `00:04:42.400 - 00:04:44.150` | other types as well |
| `00:04:44.160 - 00:04:47.510` | posix is the standard for unix and linux |
| `00:04:47.520 - 00:04:49.909` | systems and it specifies other kinds of |
| `00:04:49.919 - 00:04:52.950` | files so we typically see |
| `00:04:52.960 - 00:04:54.870` | devices we have two different kinds of |
| `00:04:54.880 - 00:04:56.710` | devices actually we have a character |
| `00:04:56.720 - 00:04:59.030` | device and a block device |
| `00:04:59.040 - 00:05:00.550` | we also have symbolic links which are |
| `00:05:00.560 - 00:05:03.189` | sometimes called soft links we have |
| `00:05:03.199 - 00:05:05.990` | named pipes and sockets but none of |
| `00:05:06.000 - 00:05:09.189` | those are available in xv6 and as i said |
| `00:05:09.199 - 00:05:11.510` | files don't have names we use path names |
| `00:05:11.520 - 00:05:15.590` | to refer to an individual file |
| `00:05:15.600 - 00:05:17.110` | i want to take a moment to talk about |
| `00:05:17.120 - 00:05:19.430` | the difference between hard links and |
| `00:05:19.440 - 00:05:20.870` | symbolic links |
| `00:05:20.880 - 00:05:23.430` | symbolic links are sometimes called soft |
| `00:05:23.440 - 00:05:24.390` | links |
| `00:05:24.400 - 00:05:27.830` | just to be clear xv6 and every unix and |
| `00:05:27.840 - 00:05:30.469` | linux system implements hard links but |
| `00:05:30.479 - 00:05:34.469` | xv6 does not implement symbolic links |
| `00:05:34.479 - 00:05:36.629` | so a regular file can have multiple hard |
| `00:05:36.639 - 00:05:38.469` | links pointing to it that is multiple |
| `00:05:38.479 - 00:05:42.150` | parents and in my example here this file |
| `00:05:42.160 - 00:05:45.270` | has multiple hard links that refer to it |
| `00:05:45.280 - 00:05:47.350` | in other words this file |
| `00:05:47.360 - 00:05:49.350` | can be located as an entry in both this |
| `00:05:49.360 - 00:05:53.670` | directory and this directory here |
| `00:05:53.680 - 00:05:55.830` | every directory on the other hand has |
| `00:05:55.840 - 00:05:57.590` | exactly one parent except of course the |
| `00:05:57.600 - 00:06:00.550` | root node so each directory can only |
| `00:06:00.560 - 00:06:03.029` | have one hard link pointing to it that |
| `00:06:03.039 - 00:06:06.390` | is the directory tree has to be a tree |
| `00:06:06.400 - 00:06:09.510` | whereas if we include the other files we |
| `00:06:09.520 - 00:06:13.830` | have a directed acyclic graph |
| `00:06:13.840 - 00:06:16.629` | so in xv6 we have regular files |
| `00:06:16.639 - 00:06:19.350` | directories and devices |
| `00:06:19.360 - 00:06:21.749` | symbolic link is another kind of file |
| `00:06:21.759 - 00:06:23.029` | and |
| `00:06:23.039 - 00:06:25.270` | that's not included in xv6 but it is a |
| `00:06:25.280 - 00:06:27.909` | type of file so what's going on with the |
| `00:06:27.919 - 00:06:29.189` | symbolic link |
| `00:06:29.199 - 00:06:32.150` | well we have a file okay but the file |
| `00:06:32.160 - 00:06:34.550` | doesn't contain data it contains another |
| `00:06:34.560 - 00:06:36.629` | path name and when encountered the |
| `00:06:36.639 - 00:06:39.110` | kernel will automatically use that path |
| `00:06:39.120 - 00:06:41.590` | name to locate what we can call the |
| `00:06:41.600 - 00:06:44.550` | target file so for example |
| `00:06:44.560 - 00:06:47.510` | this file here might be a symbolic link |
| `00:06:47.520 - 00:06:49.110` | okay it doesn't contain any data |
| `00:06:49.120 - 00:06:51.270` | directly but instead what it contains |
| `00:06:51.280 - 00:06:54.629` | here is a path name so if we refer to |
| `00:06:54.639 - 00:06:55.510` | this |
| `00:06:55.520 - 00:06:58.870` | file okay with the path name c h |
| `00:06:58.880 - 00:07:00.550` | what the kernel will do is it will go |
| `00:07:00.560 - 00:07:03.909` | down and see that it's a symbolic file |
| `00:07:03.919 - 00:07:06.150` | a symbolic link and it will then use |
| `00:07:06.160 - 00:07:08.469` | that second path name which might refer |
| `00:07:08.479 - 00:07:11.189` | to let's say this file over here |
| `00:07:11.199 - 00:07:13.510` | and then instead of |
| `00:07:13.520 - 00:07:15.510` | accessing the data here when you do a |
| `00:07:15.520 - 00:07:17.670` | read or write for example you'll be |
| `00:07:17.680 - 00:07:19.909` | reading and writing data from this file |
| `00:07:19.919 - 00:07:22.070` | here |
| `00:07:22.080 - 00:07:24.629` | now a path name is with hard length is |
| `00:07:24.639 - 00:07:27.749` | either valid or invalid okay if we say c |
| `00:07:27.759 - 00:07:30.870` | g we get to this file it was a c |
| `00:07:30.880 - 00:07:32.870` | b there is no b entry in this directory |
| `00:07:32.880 - 00:07:35.189` | so that's a bad path name |
| `00:07:35.199 - 00:07:37.670` | we have another situation with symbolic |
| `00:07:37.680 - 00:07:41.350` | links a symbolic link can become broken |
| `00:07:41.360 - 00:07:42.550` | okay so |
| `00:07:42.560 - 00:07:45.029` | we might have a perfectly valid path |
| `00:07:45.039 - 00:07:47.110` | name to get down here but the path name |
| `00:07:47.120 - 00:07:49.110` | here might indicate a file |
| `00:07:49.120 - 00:07:50.950` | that doesn't exist it might be a bad |
| `00:07:50.960 - 00:07:53.830` | path name |
| `00:07:53.840 - 00:07:57.990` | with hard links the files must be on the |
| `00:07:58.000 - 00:08:00.309` | same file system or within the same file |
| `00:08:00.319 - 00:08:02.390` | system as the directory that points to |
| `00:08:02.400 - 00:08:04.070` | them so everything |
| `00:08:04.080 - 00:08:05.270` | is in |
| `00:08:05.280 - 00:08:09.350` | or on one device with the hard links |
| `00:08:09.360 - 00:08:10.869` | one advantage of |
| `00:08:10.879 - 00:08:12.869` | symbolic links is that the |
| `00:08:12.879 - 00:08:14.629` | path name can point to |
| `00:08:14.639 - 00:08:16.790` | a file that's on another device in |
| `00:08:16.800 - 00:08:18.950` | another file system |
| `00:08:18.960 - 00:08:19.749` | and |
| `00:08:19.759 - 00:08:22.309` | we can run into problems if we try to |
| `00:08:22.319 - 00:08:24.550` | trace down or locate |
| `00:08:24.560 - 00:08:27.670` | and follow a symbolic path name |
| `00:08:27.680 - 00:08:30.070` | that is located on a file system that's |
| `00:08:30.080 - 00:08:31.830` | not even available |
| `00:08:31.840 - 00:08:33.029` | so that's another thing that can go |
| `00:08:33.039 - 00:08:35.269` | wrong with symbolic |
| `00:08:35.279 - 00:08:38.070` | links |
| `00:08:38.080 - 00:08:40.550` | next i want to define the terms i number |
| `00:08:40.560 - 00:08:42.230` | and inode |
| `00:08:42.240 - 00:08:44.630` | as you know user mode programs refer to |
| `00:08:44.640 - 00:08:46.949` | files with path names |
| `00:08:46.959 - 00:08:48.550` | but the path names aren't necessarily |
| `00:08:48.560 - 00:08:50.790` | unique and they can change |
| `00:08:50.800 - 00:08:53.910` | instead the kernel uses a number to |
| `00:08:53.920 - 00:08:55.910` | identify and work with files and that's |
| `00:08:55.920 - 00:08:57.509` | called the i number |
| `00:08:57.519 - 00:08:59.829` | it's a small integer and it's assigned |
| `00:08:59.839 - 00:09:02.470` | to the file when that file is created |
| `00:09:02.480 - 00:09:04.389` | and throughout the life of the file |
| `00:09:04.399 - 00:09:08.070` | its inem i number will never change |
| `00:09:08.080 - 00:09:09.910` | after the file is deleted the i number |
| `00:09:09.920 - 00:09:12.870` | is no longer needed and can be recycled |
| `00:09:12.880 - 00:09:15.990` | or reused for a new file and that also |
| `00:09:16.000 - 00:09:17.269` | happens |
| `00:09:17.279 - 00:09:19.190` | and it's also the case that the root |
| `00:09:19.200 - 00:09:21.910` | directory has a fixed and known i number |
| `00:09:21.920 - 00:09:25.430` | and that number is one this is a mistake |
| `00:09:25.440 - 00:09:27.910` | the i number of the root is one so that |
| `00:09:27.920 - 00:09:31.190` | the kernel can locate the root directory |
| `00:09:31.200 - 00:09:34.949` | and files are numbered from one not from |
| `00:09:34.959 - 00:09:38.310` | zero the i number of zero is reserved to |
| `00:09:38.320 - 00:09:42.790` | indicate an unused directory entry |
| `00:09:42.800 - 00:09:45.030` | associated with every file are a number |
| `00:09:45.040 - 00:09:46.630` | of attributes |
| `00:09:46.640 - 00:09:48.550` | the most important one is the i number |
| `00:09:48.560 - 00:09:51.350` | which is used to uniquely identify each |
| `00:09:51.360 - 00:09:53.190` | file |
| `00:09:53.200 - 00:09:55.509` | the type tells which kind of file we're |
| `00:09:55.519 - 00:09:57.750` | dealing with in xv6 we can have a |
| `00:09:57.760 - 00:09:59.990` | directory file a regular file or a |
| `00:10:00.000 - 00:10:02.790` | device in other unix and linux systems |
| `00:10:02.800 - 00:10:05.269` | we have other types as well |
| `00:10:05.279 - 00:10:07.509` | directories and regular files contain |
| `00:10:07.519 - 00:10:09.670` | data and so if we have a directory or a |
| `00:10:09.680 - 00:10:11.750` | regular file we need to know how much |
| `00:10:11.760 - 00:10:14.310` | data we have and so we have the size in |
| `00:10:14.320 - 00:10:17.190` | bytes of the data |
| `00:10:17.200 - 00:10:18.790` | we also need to keep track of how many |
| `00:10:18.800 - 00:10:21.110` | hard links point to this file okay |
| `00:10:21.120 - 00:10:23.110` | that's the number of parents in the tree |
| `00:10:23.120 - 00:10:25.750` | for example this file has two hard links |
| `00:10:25.760 - 00:10:27.190` | pointing to it |
| `00:10:27.200 - 00:10:29.110` | if there were no hard links pointing to |
| `00:10:29.120 - 00:10:31.269` | it then there is no path name by which |
| `00:10:31.279 - 00:10:33.190` | we could refer to a file |
| `00:10:33.200 - 00:10:34.630` | and so |
| `00:10:34.640 - 00:10:36.310` | we can think of the number of hard links |
| `00:10:36.320 - 00:10:38.710` | as a reference count when the number of |
| `00:10:38.720 - 00:10:41.350` | pointers to an object goes to zero |
| `00:10:41.360 - 00:10:43.190` | in an object-oriented system |
| `00:10:43.200 - 00:10:45.190` | the object space is reclaimed by the |
| `00:10:45.200 - 00:10:46.550` | garbage collector |
| `00:10:46.560 - 00:10:48.310` | well a similar thing is going on with |
| `00:10:48.320 - 00:10:49.509` | the |
| `00:10:49.519 - 00:10:51.910` | xv6 kernel when the number of hard |
| `00:10:51.920 - 00:10:54.069` | lengths goes to zero the kernel will |
| `00:10:54.079 - 00:10:56.949` | automatically delete the file |
| `00:10:56.959 - 00:10:58.630` | in the case of directories and regular |
| `00:10:58.640 - 00:11:00.710` | files there is additional data |
| `00:11:00.720 - 00:11:02.949` | and so we need to know where on disk |
| `00:11:02.959 - 00:11:05.350` | that data is stored so we basically have |
| `00:11:05.360 - 00:11:07.430` | a list of the block numbers that contain |
| `00:11:07.440 - 00:11:09.430` | the files data |
| `00:11:09.440 - 00:11:12.630` | in other systems not in xv6 but in other |
| `00:11:12.640 - 00:11:15.990` | unix linux systems we have additional |
| `00:11:16.000 - 00:11:18.550` | information including the timestamp of |
| `00:11:18.560 - 00:11:20.550` | when the file was created |
| `00:11:20.560 - 00:11:22.949` | and when it was last modified and so on |
| `00:11:22.959 - 00:11:25.269` | we also have information about who owns |
| `00:11:25.279 - 00:11:26.389` | the file |
| `00:11:26.399 - 00:11:28.310` | and some bits that indicate the |
| `00:11:28.320 - 00:11:29.590` | permissions and |
| `00:11:29.600 - 00:11:31.590` | to tell how other |
| `00:11:31.600 - 00:11:35.110` | users can access this file |
| `00:11:35.120 - 00:11:37.509` | the attributes are kept on disk in a |
| `00:11:37.519 - 00:11:39.430` | small fixed size structure called the |
| `00:11:39.440 - 00:11:41.190` | inode |
| `00:11:41.200 - 00:11:44.550` | okay the inode is kept on disk it's not |
| `00:11:44.560 - 00:11:47.350` | kept in any file but these are kept in a |
| `00:11:47.360 - 00:11:49.509` | separate area of the disk so somewhere |
| `00:11:49.519 - 00:11:51.030` | on the disk there is essentially an |
| `00:11:51.040 - 00:11:54.629` | array indexed by i number of these inode |
| `00:11:54.639 - 00:11:56.310` | structures |
| `00:11:56.320 - 00:11:58.310` | whenever a file is in use by the kernel |
| `00:11:58.320 - 00:12:00.629` | the kernel needs to read in the inode |
| `00:12:00.639 - 00:12:02.550` | structure from disk and keep it in |
| `00:12:02.560 - 00:12:05.190` | memory so the kernel maintains a cache |
| `00:12:05.200 - 00:12:08.949` | of inodes in memory |
| `00:12:08.959 - 00:12:10.790` | so let's take a look at how the |
| `00:12:10.800 - 00:12:14.069` | inode structure on disk is defined |
| `00:12:14.079 - 00:12:17.910` | so here we have a structure that shows |
| `00:12:17.920 - 00:12:20.550` | the on disk inode |
| `00:12:20.560 - 00:12:22.550` | we don't see the i number as i said |
| `00:12:22.560 - 00:12:24.710` | these things are kept in an array that's |
| `00:12:24.720 - 00:12:26.949` | indexed by the i number so the i number |
| `00:12:26.959 - 00:12:28.949` | is implicit |
| `00:12:28.959 - 00:12:31.269` | the type field tells what kind of file |
| `00:12:31.279 - 00:12:32.310` | this is |
| `00:12:32.320 - 00:12:34.069` | and we have several constants to |
| `00:12:34.079 - 00:12:36.150` | indicate whether it's a directory |
| `00:12:36.160 - 00:12:38.949` | a regular file or a device |
| `00:12:38.959 - 00:12:41.670` | we also use the type code to indicate |
| `00:12:41.680 - 00:12:42.949` | whether the |
| `00:12:42.959 - 00:12:46.550` | inode is free so some of the inodes on |
| `00:12:46.560 - 00:12:49.910` | disk in this inode array are in use and |
| `00:12:49.920 - 00:12:50.949` | have a |
| `00:12:50.959 - 00:12:53.670` | type of directory file or device but |
| `00:12:53.680 - 00:12:55.910` | others are free and unused and available |
| `00:12:55.920 - 00:12:58.790` | for future files and we use a type of |
| `00:12:58.800 - 00:13:01.430` | xero for those |
| `00:13:01.440 - 00:13:04.310` | if the file is a device |
| `00:13:04.320 - 00:13:06.389` | then we also have the major and minor |
| `00:13:06.399 - 00:13:08.470` | numbers being valid |
| `00:13:08.480 - 00:13:10.150` | and what are the major and minor numbers |
| `00:13:10.160 - 00:13:12.710` | this is essentially the device number |
| `00:13:12.720 - 00:13:15.430` | so the device number is a 32-bit number |
| `00:13:15.440 - 00:13:17.430` | and it's broken up into two 16-bit |
| `00:13:17.440 - 00:13:21.269` | fields the major and the minor number |
| `00:13:21.279 - 00:13:25.030` | the major number is uh an identifier to |
| `00:13:25.040 - 00:13:27.430` | tell what kind of device it is |
| `00:13:27.440 - 00:13:29.750` | and the minor number tells which device |
| `00:13:29.760 - 00:13:31.910` | among several it is for example if we |
| `00:13:31.920 - 00:13:34.310` | have three hewlett packard printers the |
| `00:13:34.320 - 00:13:35.750` | major number would indicate that this is |
| `00:13:35.760 - 00:13:37.990` | a hewlett-packard printer and that will |
| `00:13:38.000 - 00:13:40.310` | be used to indicate what kind of what |
| `00:13:40.320 - 00:13:42.310` | routines we should use to access the |
| `00:13:42.320 - 00:13:44.230` | printer the minor number would tell |
| `00:13:44.240 - 00:13:46.550` | whether this is printer number |
| `00:13:46.560 - 00:13:50.230` | one two or three or whatever |
| `00:13:50.240 - 00:13:52.629` | then we have the number of hard links |
| `00:13:52.639 - 00:13:54.550` | and then we have the size and bytes if |
| `00:13:54.560 - 00:13:57.670` | it is a directory or a regular file and |
| `00:13:57.680 - 00:14:01.509` | finally if it is we have the blocks on |
| `00:14:01.519 - 00:14:03.750` | disk where those |
| `00:14:03.760 - 00:14:05.829` | where that data will be kept so this is |
| `00:14:05.839 - 00:14:10.310` | an array of block numbers here |
| `00:14:10.320 - 00:14:12.150` | previously when i talked about disk |
| `00:14:12.160 - 00:14:14.150` | logging i presented this schematic |
| `00:14:14.160 - 00:14:16.790` | diagram for what the disk looks like |
| `00:14:16.800 - 00:14:19.430` | and i just had the main block area here |
| `00:14:19.440 - 00:14:20.949` | but now i want to present another view |
| `00:14:20.959 - 00:14:23.509` | of the disk that is more accurate and |
| `00:14:23.519 - 00:14:25.189` | more detailed |
| `00:14:25.199 - 00:14:28.069` | let's go through this thing |
| `00:14:28.079 - 00:14:30.150` | the boot record is a block that is |
| `00:14:30.160 - 00:14:32.470` | neither read nor modified by the kernel |
| `00:14:32.480 - 00:14:34.310` | it's used just during the boot and |
| `00:14:34.320 - 00:14:35.910` | startup process |
| `00:14:35.920 - 00:14:39.590` | in order to load the kernel into memory |
| `00:14:39.600 - 00:14:41.189` | the super block contains a number of |
| `00:14:41.199 - 00:14:44.150` | parameters it is read by the kernel but |
| `00:14:44.160 - 00:14:46.310` | not modified among other things that |
| `00:14:46.320 - 00:14:49.030` | tells where the other items on the disk |
| `00:14:49.040 - 00:14:50.710` | are located |
| `00:14:50.720 - 00:14:52.710` | then we have the log which i talked |
| `00:14:52.720 - 00:14:54.710` | about elsewhere and won't go into any |
| `00:14:54.720 - 00:14:56.870` | more detail on here |
| `00:14:56.880 - 00:14:59.110` | then we have an area where the inode |
| `00:14:59.120 - 00:15:02.069` | structures are stored we have the bitmap |
| `00:15:02.079 - 00:15:04.310` | and we have the data blocks |
| `00:15:04.320 - 00:15:06.550` | remember that each inode is stored in a |
| `00:15:06.560 - 00:15:08.949` | fixed size structure and these are |
| `00:15:08.959 - 00:15:10.389` | packed into |
| `00:15:10.399 - 00:15:12.470` | an array and this array is kept in these |
| `00:15:12.480 - 00:15:13.750` | blocks |
| `00:15:13.760 - 00:15:15.590` | a small number of |
| `00:15:15.600 - 00:15:18.470` | inodes will fit into each block so |
| `00:15:18.480 - 00:15:21.910` | depending on how many inodes we have |
| `00:15:21.920 - 00:15:23.750` | including both the ones that are in use |
| `00:15:23.760 - 00:15:25.269` | and the ones that are free |
| `00:15:25.279 - 00:15:26.629` | we have a number of blocks that are |
| `00:15:26.639 - 00:15:28.470` | allocated just |
| `00:15:28.480 - 00:15:31.110` | to the storage of inodes |
| `00:15:31.120 - 00:15:35.110` | and then we have a bitmap the bitmap is |
| `00:15:35.120 - 00:15:37.749` | contains one bit for every block on the |
| `00:15:37.759 - 00:15:38.790` | disk |
| `00:15:38.800 - 00:15:40.470` | and that bit indicates whether that |
| `00:15:40.480 - 00:15:43.509` | block is in use or free it's really |
| `00:15:43.519 - 00:15:45.189` | useful |
| `00:15:45.199 - 00:15:47.350` | for the data blocks okay |
| `00:15:47.360 - 00:15:50.069` | we consider all of these blocks |
| `00:15:50.079 - 00:15:53.509` | up to this point here to be in use so |
| `00:15:53.519 - 00:15:55.509` | the bitmap will indicate that all of |
| `00:15:55.519 - 00:15:58.550` | these blocks up to this point are in use |
| `00:15:58.560 - 00:16:00.389` | and all of these blocks here |
| `00:16:00.399 - 00:16:03.269` | are either in use or free indicated by a |
| `00:16:03.279 - 00:16:05.350` | bit in this bitmap |
| `00:16:05.360 - 00:16:07.749` | okay so there's one bit for all of the |
| `00:16:07.759 - 00:16:10.389` | blocks in this particular file system we |
| `00:16:10.399 - 00:16:13.030` | only have a thousand blocks so this |
| `00:16:13.040 - 00:16:14.629` | bitmap would only be |
| `00:16:14.639 - 00:16:17.509` | as big as required to contain a thousand |
| `00:16:17.519 - 00:16:18.389` | bits |
| `00:16:18.399 - 00:16:21.269` | that'll all fit into one block of course |
| `00:16:21.279 - 00:16:23.030` | but i've shown three blocks here for |
| `00:16:23.040 - 00:16:24.949` | illustrative purposes |
| `00:16:24.959 - 00:16:26.949` | and the bit is either one to indicate |
| `00:16:26.959 - 00:16:29.030` | the block is in use or zero to indicate |
| `00:16:29.040 - 00:16:30.710` | that it's free |
| `00:16:30.720 - 00:16:32.870` | and finally we have the data blocks this |
| `00:16:32.880 - 00:16:35.189` | is where the files and directories are |
| `00:16:35.199 - 00:16:37.030` | actually stored |
| `00:16:37.040 - 00:16:39.030` | now let's look at the superblock |
| `00:16:39.040 - 00:16:41.990` | uh the superblock is |
| `00:16:42.000 - 00:16:43.749` | essentially a block that contains this |
| `00:16:43.759 - 00:16:46.310` | structure here and the structure has a |
| `00:16:46.320 - 00:16:48.470` | number of fields okay so let's see |
| `00:16:48.480 - 00:16:52.470` | what's stored in this block on disk |
| `00:16:52.480 - 00:16:54.470` | first of all we have a magic number this |
| `00:16:54.480 - 00:16:57.110` | is just a constant and the operating |
| `00:16:57.120 - 00:16:59.030` | system when it reads in the super block |
| `00:16:59.040 - 00:17:01.030` | will check this number to make sure it |
| `00:17:01.040 - 00:17:03.189` | has the right value and if it doesn't |
| `00:17:03.199 - 00:17:05.029` | that might indicate that wow this is not |
| `00:17:05.039 - 00:17:07.270` | the kind of file system we expect or |
| `00:17:07.280 - 00:17:09.909` | something is dreadfully wrong this disk |
| `00:17:09.919 - 00:17:12.549` | doesn't contain a file system at all or |
| `00:17:12.559 - 00:17:13.510` | something |
| `00:17:13.520 - 00:17:15.829` | else is really the matter |
| `00:17:15.839 - 00:17:17.669` | the size field |
| `00:17:17.679 - 00:17:20.309` | in our example will be a thousand |
| `00:17:20.319 - 00:17:22.470` | it tells how many blocks are available |
| `00:17:22.480 - 00:17:23.909` | on the disk |
| `00:17:23.919 - 00:17:25.909` | okay the |
| `00:17:25.919 - 00:17:28.069` | number of data blocks |
| `00:17:28.079 - 00:17:28.990` | is |
| `00:17:29.000 - 00:17:31.669` | 959 in our case so we have that many |
| `00:17:31.679 - 00:17:32.950` | blocks here |
| `00:17:32.960 - 00:17:34.070` | and |
| `00:17:34.080 - 00:17:35.510` | we have |
| `00:17:35.520 - 00:17:38.230` | 200 inodes so |
| `00:17:38.240 - 00:17:40.630` | how many blocks is that well the |
| `00:17:40.640 - 00:17:42.470` | kernel will compute that and figure that |
| `00:17:42.480 - 00:17:43.750` | out |
| `00:17:43.760 - 00:17:46.789` | but in any case we have enough space to |
| `00:17:46.799 - 00:17:50.070` | store 200 inodes and that means that |
| `00:17:50.080 - 00:17:52.390` | this file system can accommodate up to |
| `00:17:52.400 - 00:17:55.750` | 200 files we cannot have more than 200 |
| `00:17:55.760 - 00:17:57.510` | files |
| `00:17:57.520 - 00:17:59.110` | in log is the number of blocks that are |
| `00:17:59.120 - 00:18:00.549` | allocated to the |
| `00:18:00.559 - 00:18:01.990` | uh log |
| `00:18:02.000 - 00:18:04.070` | and then we have an indication of where |
| `00:18:04.080 - 00:18:05.830` | that starts it starts at block number |
| `00:18:05.840 - 00:18:06.789` | two |
| `00:18:06.799 - 00:18:09.029` | and then where the i nodes start |
| `00:18:09.039 - 00:18:11.430` | block number 32 and then where the |
| `00:18:11.440 - 00:18:14.630` | bitmap starts block number 38. |
| `00:18:14.640 - 00:18:16.150` | uh the size of the bid |
| `00:18:16.160 - 00:18:18.230` | bitmap will be determined by the number |
| `00:18:18.240 - 00:18:20.549` | of uh blocks and as i said with a |
| `00:18:20.559 - 00:18:22.710` | thousand blocks we can actually fit the |
| `00:18:22.720 - 00:18:25.590` | bitmap into a single block but um i've |
| `00:18:25.600 - 00:18:27.990` | shown three here even though that |
| `00:18:28.000 - 00:18:30.070` | wouldn't be the case |
| `00:18:30.080 - 00:18:33.510` | so uh this superblock is read in |
| `00:18:33.520 - 00:18:36.070` | from disk at startup by this function |
| `00:18:36.080 - 00:18:37.510` | read sb |
| `00:18:37.520 - 00:18:38.470` | and then it |
| `00:18:38.480 - 00:18:41.430` | never changes and so here i have the |
| `00:18:41.440 - 00:18:43.110` | structure |
| `00:18:43.120 - 00:18:45.750` | that contains these and you see this is |
| `00:18:45.760 - 00:18:47.029` | exactly |
| `00:18:47.039 - 00:18:49.750` | what we saw before |
| `00:18:49.760 - 00:18:50.710` | we have |
| `00:18:50.720 - 00:18:52.150` | the magic number |
| `00:18:52.160 - 00:18:53.750` | we have the size |
| `00:18:53.760 - 00:18:56.310` | in blocks the number of inodes this |
| `00:18:56.320 - 00:18:59.270` | number of blocks in the log where the |
| `00:18:59.280 - 00:19:00.549` | log starts |
| `00:19:00.559 - 00:19:02.470` | where the inode |
| `00:19:02.480 - 00:19:06.789` | blocks start and where the bitmap blocks |
| `00:19:06.799 - 00:19:09.029` | start and so that's read in at startup |
| `00:19:09.039 - 00:19:11.430` | time |
| `00:19:11.440 - 00:19:14.390` | in my example file system i showed the |
| `00:19:14.400 - 00:19:16.630` | edges as being labeled and that is |
| `00:19:16.640 - 00:19:18.710` | important because it emphasizes path |
| `00:19:18.720 - 00:19:19.750` | names |
| `00:19:19.760 - 00:19:21.110` | but that might not be the way you think |
| `00:19:21.120 - 00:19:24.950` | of directories |
| `00:19:24.960 - 00:19:27.110` | here i'm showing the edges labeled but |
| `00:19:27.120 - 00:19:28.950` | you might think of it as a directory |
| `00:19:28.960 - 00:19:31.190` | containing a bunch of names and those |
| `00:19:31.200 - 00:19:33.909` | names each refer to a directory or file |
| `00:19:33.919 - 00:19:35.669` | somehow |
| `00:19:35.679 - 00:19:38.549` | well directories are stored in files so |
| `00:19:38.559 - 00:19:41.350` | for every directory there is a file and |
| `00:19:41.360 - 00:19:43.510` | that file contains a number of blocks |
| `00:19:43.520 - 00:19:46.310` | and the directory information is stored |
| `00:19:46.320 - 00:19:48.549` | in those blocks |
| `00:19:48.559 - 00:19:52.230` | here's what we have in xv6 in x36 a |
| `00:19:52.240 - 00:19:55.110` | directory is represented as an array |
| `00:19:55.120 - 00:19:58.390` | and each array entry has both a name and |
| `00:19:58.400 - 00:20:00.230` | a reference to the file |
| `00:20:00.240 - 00:20:01.270` | the |
| `00:20:01.280 - 00:20:03.830` | name has space for up to 14 characters |
| `00:20:03.840 - 00:20:06.149` | and the reference to the file is an i |
| `00:20:06.159 - 00:20:08.070` | number |
| `00:20:08.080 - 00:20:11.190` | the original unix system had this 14 |
| `00:20:11.200 - 00:20:13.990` | character limit for file names in xv6 we |
| `00:20:14.000 - 00:20:16.870` | can have up to 14 characters |
| `00:20:16.880 - 00:20:19.190` | for the file name and if it's shorter we |
| `00:20:19.200 - 00:20:21.029` | would terminate the file name with a |
| `00:20:21.039 - 00:20:22.950` | null character |
| `00:20:22.960 - 00:20:24.710` | when we want to look up a file we have |
| `00:20:24.720 - 00:20:26.950` | to go through this array |
| `00:20:26.960 - 00:20:28.070` | linearly |
| `00:20:28.080 - 00:20:30.549` | and that's a bit time consuming |
| `00:20:30.559 - 00:20:33.669` | in modern unix and linux systems we have |
| `00:20:33.679 - 00:20:36.310` | a different organization for directories |
| `00:20:36.320 - 00:20:39.270` | that will accommodate longer file names |
| `00:20:39.280 - 00:20:41.110` | and as you know some directories contain |
| `00:20:41.120 - 00:20:43.350` | hundreds of files so we have different |
| `00:20:43.360 - 00:20:45.270` | data structures that allow for faster |
| `00:20:45.280 - 00:20:47.830` | lookups but essentially it's still the |
| `00:20:47.840 - 00:20:50.230` | same idea you have a name and for each |
| `00:20:50.240 - 00:20:53.350` | name you have an i number |
| `00:20:53.360 - 00:20:55.590` | so that pretty much completes the |
| `00:20:55.600 - 00:20:57.830` | information about how the file system is |
| `00:20:57.840 - 00:21:00.710` | stored on disk |
| `00:21:00.720 - 00:21:03.510` | so next let's take a look at how the |
| `00:21:03.520 - 00:21:05.750` | mkfs |
| `00:21:05.760 - 00:21:09.190` | function or program which can be used to |
| `00:21:09.200 - 00:21:11.909` | create or build a file system from |
| `00:21:11.919 - 00:21:14.510` | scratch |
| `00:21:14.520 - 00:21:18.070` | mkfs or makefile system is a standalone |
| `00:21:18.080 - 00:21:21.270` | c program it's not part of the xv6 |
| `00:21:21.280 - 00:21:23.350` | kernel and it's not a user mode program |
| `00:21:23.360 - 00:21:26.310` | that runs under the xv6 kernel |
| `00:21:26.320 - 00:21:28.710` | instead it's a separate c program it's |
| `00:21:28.720 - 00:21:30.870` | compiled on your host computer and it |
| `00:21:30.880 - 00:21:33.590` | runs on that computer just as the chemo |
| `00:21:33.600 - 00:21:36.950` | emulator runs on that host computer |
| `00:21:36.960 - 00:21:39.590` | when you run the make file it compiles |
| `00:21:39.600 - 00:21:42.549` | the xv6 kernel and it compiles all of |
| `00:21:42.559 - 00:21:45.190` | the user mode programs and it also |
| `00:21:45.200 - 00:21:47.909` | compiles this mkfs |
| `00:21:47.919 - 00:21:51.510` | program and runs this program as well |
| `00:21:51.520 - 00:21:53.350` | and what does this make file system |
| `00:21:53.360 - 00:21:54.549` | program do |
| `00:21:54.559 - 00:21:57.590` | well it's going to create the disk image |
| `00:21:57.600 - 00:22:00.310` | file the disk image file is named |
| `00:22:00.320 - 00:22:02.390` | fs.image |
| `00:22:02.400 - 00:22:05.270` | and this is a file on the host computer |
| `00:22:05.280 - 00:22:07.510` | and it will be used by kimu to emulate |
| `00:22:07.520 - 00:22:08.630` | the disk |
| `00:22:08.640 - 00:22:09.430` | so |
| `00:22:09.440 - 00:22:11.750` | all of the contents of the disk are kept |
| `00:22:11.760 - 00:22:15.510` | in this file system image file and |
| `00:22:15.520 - 00:22:16.630` | whenever |
| `00:22:16.640 - 00:22:18.789` | a read or write command is issued to the |
| `00:22:18.799 - 00:22:22.070` | disk kimo will actually go to this |
| `00:22:22.080 - 00:22:25.029` | file on the host system to |
| `00:22:25.039 - 00:22:27.510` | get or put data |
| `00:22:27.520 - 00:22:31.669` | so our disk in xv6 is initialized to |
| `00:22:31.679 - 00:22:34.430` | have a thousand blocks and each block is |
| `00:22:34.440 - 00:22:38.470` | 1024 bytes so that means there is a |
| `00:22:38.480 - 00:22:40.549` | thousand k bytes |
| `00:22:40.559 - 00:22:43.350` | in this file one byte in the file for |
| `00:22:43.360 - 00:22:47.029` | every byte on the emulated disk |
| `00:22:47.039 - 00:22:49.510` | make file system will initialize the |
| `00:22:49.520 - 00:22:52.230` | disk okay we'll create the entire file |
| `00:22:52.240 - 00:22:55.029` | system it will create the directory tree |
| `00:22:55.039 - 00:22:57.110` | and it will |
| `00:22:57.120 - 00:22:59.990` | put a number of files into the directory |
| `00:23:00.000 - 00:23:03.350` | tree so that when the kernel begins |
| `00:23:03.360 - 00:23:05.590` | that is at startup time |
| `00:23:05.600 - 00:23:07.669` | the kernel will find the file system |
| `00:23:07.679 - 00:23:09.270` | already exists |
| `00:23:09.280 - 00:23:10.950` | so |
| `00:23:10.960 - 00:23:12.950` | what does it do well |
| `00:23:12.960 - 00:23:15.510` | make file system will write the super |
| `00:23:15.520 - 00:23:16.470` | block |
| `00:23:16.480 - 00:23:18.789` | to the file or that is to the image file |
| `00:23:18.799 - 00:23:21.110` | actually it doesn't write anything for |
| `00:23:21.120 - 00:23:22.390` | the boot record because that's not |
| `00:23:22.400 - 00:23:23.590` | really needed |
| `00:23:23.600 - 00:23:25.590` | and it will also initialize all the |
| `00:23:25.600 - 00:23:26.870` | inodes |
| `00:23:26.880 - 00:23:29.430` | it will initialize the bitmap area of |
| `00:23:29.440 - 00:23:31.510` | the emulated disk |
| `00:23:31.520 - 00:23:33.909` | it will create a directory structure and |
| `00:23:33.919 - 00:23:36.789` | initialize some directories and for each |
| `00:23:36.799 - 00:23:38.789` | of the user files |
| `00:23:38.799 - 00:23:41.190` | that is the user mode executables like |
| `00:23:41.200 - 00:23:43.590` | cat echo the initial program and the |
| `00:23:43.600 - 00:23:45.029` | shell and so on |
| `00:23:45.039 - 00:23:48.230` | it will create a file and add that to |
| `00:23:48.240 - 00:23:50.470` | the directory of the file system |
| `00:23:50.480 - 00:23:53.110` | so that when make file system is done |
| `00:23:53.120 - 00:23:53.909` | this |
| `00:23:53.919 - 00:23:56.470` | image file will contain an entire |
| `00:23:56.480 - 00:23:59.029` | complete file system ready to go so that |
| `00:23:59.039 - 00:24:01.830` | the kernel can boot up and find the file |
| `00:24:01.840 - 00:24:04.470` | system |
| `00:24:04.480 - 00:24:07.510` | real unix and linux systems also have a |
| `00:24:07.520 - 00:24:10.549` | similar program called make file system |
| `00:24:10.559 - 00:24:13.510` | and these will initialize not an image |
| `00:24:13.520 - 00:24:16.950` | file but a device and they will write |
| `00:24:16.960 - 00:24:19.990` | whatever is necessary to initialize the |
| `00:24:20.000 - 00:24:21.909` | organization for the file structure on |
| `00:24:21.919 - 00:24:23.430` | that device |
| `00:24:23.440 - 00:24:24.149` | the |
| `00:24:24.159 - 00:24:25.510` | file system organization that we've |
| `00:24:25.520 - 00:24:27.590` | talked about for xv6 is fairly |
| `00:24:27.600 - 00:24:28.789` | simplified |
| `00:24:28.799 - 00:24:30.310` | and these |
| `00:24:30.320 - 00:24:32.310` | make file system programs for other |
| `00:24:32.320 - 00:24:35.269` | systems will create file systems uh with |
| `00:24:35.279 - 00:24:37.510` | very different and more complex uh file |
| `00:24:37.520 - 00:24:38.549` | systems |
| `00:24:38.559 - 00:24:39.669` | uh |
| `00:24:39.679 - 00:24:41.669` | but so these are complicated programs to |
| `00:24:41.679 - 00:24:44.830` | create the initial file system on a |
| `00:24:44.840 - 00:24:47.830` | device but in any case uh the make file |
| `00:24:47.840 - 00:24:50.310` | system program that we are looking at |
| `00:24:50.320 - 00:24:52.630` | here in xv6 we'll make use of some |
| `00:24:52.640 - 00:24:56.230` | parameters i will go to the file param.h |
| `00:24:56.240 - 00:24:59.110` | and get things like the file system size |
| `00:24:59.120 - 00:25:01.269` | that's the number of blocks to |
| `00:25:01.279 - 00:25:03.830` | create in the file system as well as the |
| `00:25:03.840 - 00:25:06.149` | number of blocks to add to the log |
| `00:25:06.159 - 00:25:08.230` | and we also have a number of |
| `00:25:08.240 - 00:25:11.909` | predefined parameters or macros |
| `00:25:11.919 - 00:25:14.950` | and constants for example the number of |
| `00:25:14.960 - 00:25:16.789` | bits per block |
| `00:25:16.799 - 00:25:19.750` | that uh we can store for the bitmap |
| `00:25:19.760 - 00:25:21.750` | or the number of inodes that we can pack |
| `00:25:21.760 - 00:25:23.269` | into a block |
| `00:25:23.279 - 00:25:25.190` | in the inode array that will be |
| `00:25:25.200 - 00:25:27.269` | represented on disk these constants are |
| `00:25:27.279 - 00:25:29.190` | needed by |
| `00:25:29.200 - 00:25:30.909` | the |
| `00:25:30.919 - 00:25:34.310` | makefilesystem.c program to create the |
| `00:25:34.320 - 00:25:36.630` | image file |
| `00:25:36.640 - 00:25:38.070` | okay that wraps it up i'll see you in |
| `00:25:38.080 - 00:25:41.320` | the next video |
