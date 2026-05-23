# xv6 Kernel-31: Inodes

| Field | Value |
| --- | --- |
| Video ID | `yzyCoL9SA2M` |
| URL | <https://www.youtube.com/watch?v=yzyCoL9SA2M> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.960 - 00:00:02.470` | this video is part of a series on the |
| `00:00:02.480 - 00:00:04.870` | xv6 operating system kernel |
| `00:00:04.880 - 00:00:06.789` | in this video i'll be talking about |
| `00:00:06.799 - 00:00:07.990` | inodes |
| `00:00:08.000 - 00:00:10.310` | i'll talk about how they are represented |
| `00:00:10.320 - 00:00:13.030` | and how they are stored in an array on |
| `00:00:13.040 - 00:00:15.190` | the disk and i'll be talking about how |
| `00:00:15.200 - 00:00:17.349` | they are represented in memory we have a |
| `00:00:17.359 - 00:00:19.750` | cache in memory of the inodes that we're |
| `00:00:19.760 - 00:00:21.429` | currently using |
| `00:00:21.439 - 00:00:23.509` | i'll go over some definitions from the |
| `00:00:23.519 - 00:00:27.509` | files fs.h and file.h and then i'll look |
| `00:00:27.519 - 00:00:31.109` | at the code in fs fs.c |
| `00:00:31.119 - 00:00:33.590` | there are a lot of functions 25 |
| `00:00:33.600 - 00:00:36.470` | functions in this file and in this video |
| `00:00:36.480 - 00:00:38.630` | i will just do an overview of these |
| `00:00:38.640 - 00:00:39.670` | functions |
| `00:00:39.680 - 00:00:41.590` | i will actually walk through the code of |
| `00:00:41.600 - 00:00:45.190` | these functions in the next video |
| `00:00:45.200 - 00:00:47.990` | okay let's get started |
| `00:00:48.000 - 00:00:50.150` | previously i presented this picture |
| `00:00:50.160 - 00:00:52.630` | which is a schematic showing the layout |
| `00:00:52.640 - 00:00:53.990` | of the disk |
| `00:00:54.000 - 00:00:55.750` | in this picture each one of these small |
| `00:00:55.760 - 00:00:58.709` | squares represents a disk block |
| `00:00:58.719 - 00:01:00.869` | and we see the area where the log is |
| `00:01:00.879 - 00:01:02.549` | kept that's here |
| `00:01:02.559 - 00:01:05.350` | we see the area where the inode array is |
| `00:01:05.360 - 00:01:07.350` | kept and that's here we also have a |
| `00:01:07.360 - 00:01:10.390` | bitmap which is kept here and we have |
| `00:01:10.400 - 00:01:13.510` | the data block area the data block area |
| `00:01:13.520 - 00:01:16.550` | contains the blocks that store the data |
| `00:01:16.560 - 00:01:20.390` | of the regular files and the directories |
| `00:01:20.400 - 00:01:22.230` | in the bitmap there's a single bit for |
| `00:01:22.240 - 00:01:26.070` | each one of these blocks and that bit |
| `00:01:26.080 - 00:01:28.469` | tells whether the block is free or in |
| `00:01:28.479 - 00:01:30.069` | use |
| `00:01:30.079 - 00:01:32.469` | the blocks from zero all the way up to |
| `00:01:32.479 - 00:01:34.870` | here are considered to be in use so |
| `00:01:34.880 - 00:01:37.270` | within the bitmap the bits corresponding |
| `00:01:37.280 - 00:01:41.190` | to these blocks will be a 1 to indicate |
| `00:01:41.200 - 00:01:43.350` | that those blocks are in use |
| `00:01:43.360 - 00:01:45.350` | whenever we want to get a free block to |
| `00:01:45.360 - 00:01:47.749` | add to a directory or file |
| `00:01:47.759 - 00:01:50.950` | we will look in the bitmap and it will |
| `00:01:50.960 - 00:01:53.910` | only find a free block somewhere in this |
| `00:01:53.920 - 00:01:55.590` | area |
| `00:01:55.600 - 00:01:57.030` | okay next what i want to do is take a |
| `00:01:57.040 - 00:01:58.630` | closer look at what's going on with the |
| `00:01:58.640 - 00:01:59.830` | inodes |
| `00:01:59.840 - 00:02:02.069` | so here i am showing |
| `00:02:02.079 - 00:02:05.109` | uh the disk again here's the log here's |
| `00:02:05.119 - 00:02:07.670` | the bitmap here the data blocks |
| `00:02:07.680 - 00:02:10.070` | this area is where the inode array is |
| `00:02:10.080 - 00:02:12.790` | kept and it starts at some particular |
| `00:02:12.800 - 00:02:15.190` | block number and that's |
| `00:02:15.200 - 00:02:17.830` | value is in the super block so there's a |
| `00:02:17.840 - 00:02:19.350` | field in the super block structure |
| `00:02:19.360 - 00:02:21.670` | called inode start that allows us to |
| `00:02:21.680 - 00:02:24.550` | find where this array begins this array |
| `00:02:24.560 - 00:02:26.710` | contains a number of structures each |
| `00:02:26.720 - 00:02:29.270` | element in the array is an inode |
| `00:02:29.280 - 00:02:31.350` | structure and i'll talk about those in a |
| `00:02:31.360 - 00:02:32.390` | second |
| `00:02:32.400 - 00:02:33.910` | they are packed |
| `00:02:33.920 - 00:02:36.470` | together as tightly as possible |
| `00:02:36.480 - 00:02:37.910` | i'm indicating that we can get four of |
| `00:02:37.920 - 00:02:39.910` | these structures per block |
| `00:02:39.920 - 00:02:42.550` | and uh here's a block right here and |
| `00:02:42.560 - 00:02:44.550` | here's a block and each one of these is |
| `00:02:44.560 - 00:02:46.470` | a block containing four |
| `00:02:46.480 - 00:02:48.390` | inode structures with a little bit of |
| `00:02:48.400 - 00:02:50.630` | wasted space at the end |
| `00:02:50.640 - 00:02:52.550` | there will be wasted space if we cannot |
| `00:02:52.560 - 00:02:55.670` | get an even number of inodes packed into |
| `00:02:55.680 - 00:02:58.470` | a block and in reality these inodes are |
| `00:02:58.480 - 00:03:00.630` | not quite that big so we can actually |
| `00:03:00.640 - 00:03:03.030` | get more than just four but here i'm |
| `00:03:03.040 - 00:03:04.630` | showing you that they are packed |
| `00:03:04.640 - 00:03:07.430` | together like this with uh some unused |
| `00:03:07.440 - 00:03:08.630` | space |
| `00:03:08.640 - 00:03:10.229` | now let's take a look at one particular |
| `00:03:10.239 - 00:03:13.110` | inode and see what that structure looks |
| `00:03:13.120 - 00:03:13.910` | like |
| `00:03:13.920 - 00:03:16.229` | so i covered this before but let me |
| `00:03:16.239 - 00:03:18.949` | remind you that it has a type field that |
| `00:03:18.959 - 00:03:20.949` | tells whether it's a regular file a |
| `00:03:20.959 - 00:03:23.910` | directory or a device if it's device |
| `00:03:23.920 - 00:03:26.070` | it's got a major number and a minor |
| `00:03:26.080 - 00:03:27.750` | number |
| `00:03:27.760 - 00:03:29.990` | and in any case regardless of what kind |
| `00:03:30.000 - 00:03:32.390` | of file it is we have in link which is |
| `00:03:32.400 - 00:03:34.070` | the number of hard links that point to |
| `00:03:34.080 - 00:03:34.869` | it |
| `00:03:34.879 - 00:03:37.270` | so if this particular file |
| `00:03:37.280 - 00:03:40.149` | is it is in say let's say four |
| `00:03:40.159 - 00:03:42.070` | directories then the end link will be |
| `00:03:42.080 - 00:03:43.910` | four this tells how many directories |
| `00:03:43.920 - 00:03:46.869` | point to this particular file |
| `00:03:46.879 - 00:03:50.070` | if it is a regular file |
| `00:03:50.080 - 00:03:52.470` | or a directory then it will have a size |
| `00:03:52.480 - 00:03:53.830` | the device |
| `00:03:53.840 - 00:03:56.309` | files would ignore the size and this |
| `00:03:56.319 - 00:03:57.990` | array down here |
| `00:03:58.000 - 00:03:59.589` | and finally for regular files and |
| `00:03:59.599 - 00:04:01.270` | directories we have to have the actual |
| `00:04:01.280 - 00:04:02.229` | data |
| `00:04:02.239 - 00:04:04.789` | and so we have an array here of block |
| `00:04:04.799 - 00:04:07.190` | numbers each block number can be thought |
| `00:04:07.200 - 00:04:09.589` | of as a pointer to a block up in this |
| `00:04:09.599 - 00:04:11.350` | data area here |
| `00:04:11.360 - 00:04:13.270` | and that array has a fixed size which |
| `00:04:13.280 - 00:04:15.270` | happens to be 12. so we have 12 blocks |
| `00:04:15.280 - 00:04:17.749` | here from 0 to 11 |
| `00:04:17.759 - 00:04:20.629` | and beyond that if the file grows beyond |
| `00:04:20.639 - 00:04:23.110` | a size of 12 blocks |
| `00:04:23.120 - 00:04:25.670` | what we do is we use an indirect block |
| `00:04:25.680 - 00:04:28.790` | so the next pointer okay the 13th |
| `00:04:28.800 - 00:04:31.510` | pointer points to another block |
| `00:04:31.520 - 00:04:34.629` | on disk somewhere in here and that block |
| `00:04:34.639 - 00:04:36.310` | is filled with |
| `00:04:36.320 - 00:04:38.710` | block numbers or pointers to other |
| `00:04:38.720 - 00:04:39.990` | blocks |
| `00:04:40.000 - 00:04:42.870` | and since the block number is four bytes |
| `00:04:42.880 - 00:04:45.990` | and the block size is uh 1024 we can |
| `00:04:46.000 - 00:04:48.070` | pack in 256 |
| `00:04:48.080 - 00:04:51.030` | pointers into the indirect block |
| `00:04:51.040 - 00:04:51.909` | so |
| `00:04:51.919 - 00:04:54.469` | with the 256 blocks that are pointed to |
| `00:04:54.479 - 00:04:56.629` | by the indirect block |
| `00:04:56.639 - 00:04:59.830` | and these 12 blocks that imposes the |
| `00:04:59.840 - 00:05:03.430` | maximum size on any file it's uh rather |
| `00:05:03.440 - 00:05:04.870` | limited in |
| `00:05:04.880 - 00:05:07.670` | xv6 here it's given by these two |
| `00:05:07.680 - 00:05:10.070` | constants the number of direct pointers |
| `00:05:10.080 - 00:05:12.790` | is 12 and the number of pointers we can |
| `00:05:12.800 - 00:05:14.629` | squeeze into a |
| `00:05:14.639 - 00:05:17.749` | an indirect block is 256. |
| `00:05:17.759 - 00:05:20.150` | and production unix and linux systems we |
| `00:05:20.160 - 00:05:21.909` | have other ways of |
| `00:05:21.919 - 00:05:24.550` | accommodating bigger files for example |
| `00:05:24.560 - 00:05:26.390` | we might have a pointer that points to a |
| `00:05:26.400 - 00:05:28.469` | double indirect block |
| `00:05:28.479 - 00:05:30.710` | so each of these pointers points to an |
| `00:05:30.720 - 00:05:32.950` | indirect block it points to the block |
| `00:05:32.960 - 00:05:35.110` | itself we can also have triple in |
| `00:05:35.120 - 00:05:37.909` | direction and with that we have a |
| `00:05:37.919 - 00:05:40.150` | potential maximum file size that's |
| `00:05:40.160 - 00:05:41.909` | absolutely huge |
| `00:05:41.919 - 00:05:44.550` | but xv6 just has this single |
| `00:05:44.560 - 00:05:46.950` | block of indirection and that's good |
| `00:05:46.960 - 00:05:51.670` | enough for us |
| `00:05:51.680 - 00:05:54.469` | so we don't actually store the inode |
| `00:05:54.479 - 00:05:57.029` | number that's implicit so this is the |
| `00:05:57.039 - 00:05:59.350` | inode for |
| `00:05:59.360 - 00:06:01.189` | file number six |
| `00:06:01.199 - 00:06:03.670` | and uh we will then need to read it into |
| `00:06:03.680 - 00:06:05.590` | memory where we can work on it and for |
| `00:06:05.600 - 00:06:07.909` | that we have a cache of inodes that are |
| `00:06:07.919 - 00:06:10.469` | stored in memory so there's an array or |
| `00:06:10.479 - 00:06:13.189` | i should say a variable called i table |
| `00:06:13.199 - 00:06:15.029` | that's the structure and it contains two |
| `00:06:15.039 - 00:06:18.550` | fields a lock and an array called inode |
| `00:06:18.560 - 00:06:21.830` | and that array contains a number of uh |
| `00:06:21.840 - 00:06:23.590` | structures that look like this |
| `00:06:23.600 - 00:06:26.469` | and it's uh fixed uh to have a size of |
| `00:06:26.479 - 00:06:29.830` | 50. so we have up to 50 inodes out of |
| `00:06:29.840 - 00:06:32.469` | the 200 that we can be actively working |
| `00:06:32.479 - 00:06:34.710` | on so if we are |
| `00:06:34.720 - 00:06:36.469` | working on a file that is it's open and |
| `00:06:36.479 - 00:06:38.070` | we're accessing it |
| `00:06:38.080 - 00:06:40.710` | what the kernel will do is it will read |
| `00:06:40.720 - 00:06:41.990` | the structure |
| `00:06:42.000 - 00:06:45.029` | from the disk into one of these cached |
| `00:06:45.039 - 00:06:46.629` | structures |
| `00:06:46.639 - 00:06:48.870` | the cache inode contains all the fields |
| `00:06:48.880 - 00:06:51.749` | that are in the inode on disk type major |
| `00:06:51.759 - 00:06:55.029` | minor in link size in the address array |
| `00:06:55.039 - 00:06:57.990` | type major minor in link size and the |
| `00:06:58.000 - 00:06:59.350` | address array |
| `00:06:59.360 - 00:07:02.070` | as well as some additional fields for |
| `00:07:02.080 - 00:07:04.390` | one thing we need the i number as well |
| `00:07:04.400 - 00:07:06.070` | as the device number |
| `00:07:06.080 - 00:07:09.189` | in xv6 we only have one device that can |
| `00:07:09.199 - 00:07:11.589` | contain file systems but |
| `00:07:11.599 - 00:07:13.990` | in other unix systems we will have |
| `00:07:14.000 - 00:07:16.790` | several devices that are currently being |
| `00:07:16.800 - 00:07:18.790` | used so in order to |
| `00:07:18.800 - 00:07:20.710` | identify the file we need not only the |
| `00:07:20.720 - 00:07:24.469` | device number but the i number |
| `00:07:24.479 - 00:07:25.749` | and |
| `00:07:25.759 - 00:07:28.550` | the i numbers are unique on each file |
| `00:07:28.560 - 00:07:30.950` | system that if we have several file |
| `00:07:30.960 - 00:07:33.430` | systems uh the i numbers we could have |
| `00:07:33.440 - 00:07:36.550` | some confusion because i number six uh |
| `00:07:36.560 - 00:07:38.150` | just says it's uh i number six it |
| `00:07:38.160 - 00:07:40.710` | doesn't say which file system that's in |
| `00:07:40.720 - 00:07:42.469` | so it could be either this file system |
| `00:07:42.479 - 00:07:44.150` | or some other file system so that's why |
| `00:07:44.160 - 00:07:46.629` | we have the device number here |
| `00:07:46.639 - 00:07:49.430` | the reference count is the number of |
| `00:07:49.440 - 00:07:51.749` | pointers to this structure or you can |
| `00:07:51.759 - 00:07:52.950` | think of it as the number of threads |
| `00:07:52.960 - 00:07:55.430` | that are currently using this cached |
| `00:07:55.440 - 00:07:57.270` | inode |
| `00:07:57.280 - 00:07:59.350` | within this cache of 50 |
| `00:07:59.360 - 00:08:01.430` | uh inodes |
| `00:08:01.440 - 00:08:02.629` | the |
| `00:08:02.639 - 00:08:03.990` | reference number will tell which ones |
| `00:08:04.000 - 00:08:06.390` | are free and which ones are in use if |
| `00:08:06.400 - 00:08:08.469` | the reference number is zero then it's |
| `00:08:08.479 - 00:08:10.869` | free and it can be used to cache a |
| `00:08:10.879 - 00:08:13.670` | different inode but if it's in use the |
| `00:08:13.680 - 00:08:15.589` | reference number will be something |
| `00:08:15.599 - 00:08:17.749` | greater than zero |
| `00:08:17.759 - 00:08:20.309` | the structure also contains a lock oh |
| `00:08:20.319 - 00:08:22.710` | i've labeled these wrong the lock field |
| `00:08:22.720 - 00:08:25.350` | is a sleep lock okay and the valid field |
| `00:08:25.360 - 00:08:26.950` | is a flag |
| `00:08:26.960 - 00:08:29.830` | and the sleep lock protects this so |
| `00:08:29.840 - 00:08:32.550` | whenever the kernel wants to read or |
| `00:08:32.560 - 00:08:35.190` | update any of the fields in the inode it |
| `00:08:35.200 - 00:08:37.509` | must be holding the sleep lock called |
| `00:08:37.519 - 00:08:39.269` | lock |
| `00:08:39.279 - 00:08:41.990` | at some point uh the |
| `00:08:42.000 - 00:08:43.589` | particular element in the cache is |
| `00:08:43.599 - 00:08:46.070` | allocated to some inode |
| `00:08:46.080 - 00:08:48.310` | and the i number is filled in along with |
| `00:08:48.320 - 00:08:50.630` | the device and the reference count |
| `00:08:50.640 - 00:08:52.949` | but the data may not be read in |
| `00:08:52.959 - 00:08:55.269` | immediately so the valid flag tells |
| `00:08:55.279 - 00:08:57.350` | whether the data has been read in from |
| `00:08:57.360 - 00:08:59.990` | disk or not and if it's zero then we |
| `00:09:00.000 - 00:09:01.670` | need to go ahead and read in from disk |
| `00:09:01.680 - 00:09:03.990` | if we're going to look at it |
| `00:09:04.000 - 00:09:06.870` | the uh fields up here the device number |
| `00:09:06.880 - 00:09:09.910` | i inode number and reference count are |
| `00:09:09.920 - 00:09:11.110` | protected by |
| `00:09:11.120 - 00:09:14.470` | a spin lock in this eye table structure |
| `00:09:14.480 - 00:09:16.550` | and that spin lock protects all of these |
| `00:09:16.560 - 00:09:17.750` | so when we |
| `00:09:17.760 - 00:09:20.230` | allocate an element from the cache |
| `00:09:20.240 - 00:09:23.350` | to be used for a particular i node |
| `00:09:23.360 - 00:09:25.910` | we will need to first grab the spin lock |
| `00:09:25.920 - 00:09:28.630` | and then set the i i number and the |
| `00:09:28.640 - 00:09:30.470` | device as well as incrementing the |
| `00:09:30.480 - 00:09:31.670` | reference count |
| `00:09:31.680 - 00:09:37.829` | to 1. |
| `00:09:37.839 - 00:09:40.870` | there's another variable called device |
| `00:09:40.880 - 00:09:43.030` | switch and that's an array |
| `00:09:43.040 - 00:09:45.269` | it happens to be |
| `00:09:45.279 - 00:09:47.910` | containing 10 elements as given by this |
| `00:09:47.920 - 00:09:50.389` | constant number of devices |
| `00:09:50.399 - 00:09:52.790` | each element in the array is a structure |
| `00:09:52.800 - 00:09:56.389` | and i've shown one of them enlarged here |
| `00:09:56.399 - 00:09:59.190` | and for each device there is an element |
| `00:09:59.200 - 00:10:02.069` | in this array and what does the array |
| `00:10:02.079 - 00:10:05.190` | contain well it contains pointers two |
| `00:10:05.200 - 00:10:06.630` | functions |
| `00:10:06.640 - 00:10:07.350` | so |
| `00:10:07.360 - 00:10:09.990` | for example element number one is the |
| `00:10:10.000 - 00:10:12.310` | console you know whenever we do a reader |
| `00:10:12.320 - 00:10:14.790` | write to the serial output we need to |
| `00:10:14.800 - 00:10:16.550` | access this device |
| `00:10:16.560 - 00:10:20.150` | and so for the console device we use |
| `00:10:20.160 - 00:10:21.750` | these two particular functions to |
| `00:10:21.760 - 00:10:24.389` | perform the read and the right for other |
| `00:10:24.399 - 00:10:25.750` | devices we might have different |
| `00:10:25.760 - 00:10:27.509` | functions so these are essentially the |
| `00:10:27.519 - 00:10:29.829` | device drivers that are specific to |
| `00:10:29.839 - 00:10:33.509` | device number one |
| `00:10:33.519 - 00:10:34.230` | so |
| `00:10:34.240 - 00:10:37.269` | for the console device the read function |
| `00:10:37.279 - 00:10:39.829` | uh takes a destination address this is a |
| `00:10:39.839 - 00:10:42.310` | buffer in kernel memory where well it's |
| `00:10:42.320 - 00:10:44.389` | a buffer where we're going to be storing |
| `00:10:44.399 - 00:10:46.470` | the characters that we read here we have |
| `00:10:46.480 - 00:10:48.230` | a byte count that's how many characters |
| `00:10:48.240 - 00:10:49.590` | we want to read |
| `00:10:49.600 - 00:10:51.190` | and here we have a flag the first |
| `00:10:51.200 - 00:10:53.750` | argument is either 0 or 1 to indicate |
| `00:10:53.760 - 00:10:56.389` | whether this destination address is a |
| `00:10:56.399 - 00:10:58.550` | physical address in kernel address space |
| `00:10:58.560 - 00:11:00.389` | or whether it's a virtual address in |
| `00:11:00.399 - 00:11:03.350` | some user's virtual address |
| `00:11:03.360 - 00:11:04.949` | and it returns the number of bytes that |
| `00:11:04.959 - 00:11:06.790` | we have successfully read |
| `00:11:06.800 - 00:11:08.389` | likewise there's a console write |
| `00:11:08.399 - 00:11:10.550` | function it takes the address of where |
| `00:11:10.560 - 00:11:12.710` | the characters are and how many bytes |
| `00:11:12.720 - 00:11:14.550` | there are that we want to write out to |
| `00:11:14.560 - 00:11:16.870` | the console as well as a flag to tell |
| `00:11:16.880 - 00:11:18.389` | whether this address is in the kernel |
| `00:11:18.399 - 00:11:19.990` | space or the user's virtual address |
| `00:11:20.000 - 00:11:22.069` | space and it returns the number of bytes |
| `00:11:22.079 - 00:11:23.350` | written |
| `00:11:23.360 - 00:11:26.310` | in xv6 this array is not really used |
| `00:11:26.320 - 00:11:27.750` | very much |
| `00:11:27.760 - 00:11:29.190` | in fact the only element that has |
| `00:11:29.200 - 00:11:31.590` | anything in it is device number one and |
| `00:11:31.600 - 00:11:34.470` | so that's the console device it's a bit |
| `00:11:34.480 - 00:11:37.190` | confusing because the um file system is |
| `00:11:37.200 - 00:11:40.230` | also on device number one |
| `00:11:40.240 - 00:11:42.790` | but that's just the way it is so we're |
| `00:11:42.800 - 00:11:45.110` | only using this uh device array for the |
| `00:11:45.120 - 00:11:46.230` | console |
| `00:11:46.240 - 00:11:48.069` | but here you get the idea that for |
| `00:11:48.079 - 00:11:50.710` | different kinds of devices we could have |
| `00:11:50.720 - 00:11:52.710` | different routines to be used for them |
| `00:11:52.720 - 00:11:54.949` | so the major number would be used to |
| `00:11:54.959 - 00:11:57.030` | index into this device to tell us which |
| `00:11:57.040 - 00:11:58.550` | functions to use |
| `00:11:58.560 - 00:12:00.230` | and the minor number would probably be |
| `00:12:00.240 - 00:12:02.470` | passed to the driver to indicate which |
| `00:12:02.480 - 00:12:06.470` | device to send it to |
| `00:12:06.480 - 00:12:10.470` | now let's take a look at the file fs.h |
| `00:12:10.480 - 00:12:11.990` | in the real world there are several |
| `00:12:12.000 - 00:12:13.670` | kinds of file systems that are in common |
| `00:12:13.680 - 00:12:16.470` | use and here's some examples ext4 the |
| `00:12:16.480 - 00:12:19.190` | file system used by minix xfs are some |
| `00:12:19.200 - 00:12:20.389` | common ones |
| `00:12:20.399 - 00:12:22.310` | we might call the file system format |
| `00:12:22.320 - 00:12:26.870` | used by xv6 x36 fs |
| `00:12:26.880 - 00:12:28.790` | a real operating system is going to need |
| `00:12:28.800 - 00:12:30.310` | to be able to handle several different |
| `00:12:30.320 - 00:12:32.470` | file system formats |
| `00:12:32.480 - 00:12:34.629` | xp6 can only handle one and the |
| `00:12:34.639 - 00:12:36.870` | definitions that are related to the on |
| `00:12:36.880 - 00:12:38.949` | disk file system formatting are |
| `00:12:38.959 - 00:12:42.550` | contained in this file fs.h |
| `00:12:42.560 - 00:12:43.750` | so let's go through and see what it's |
| `00:12:43.760 - 00:12:45.190` | got |
| `00:12:45.200 - 00:12:46.389` | first of all we need to be able to |
| `00:12:46.399 - 00:12:48.710` | locate the root directory and that is |
| `00:12:48.720 - 00:12:50.870` | inode number one |
| `00:12:50.880 - 00:12:53.190` | the block size for the blocks on this |
| `00:12:53.200 - 00:12:54.069` | disc |
| `00:12:54.079 - 00:12:56.310` | is 1024. |
| `00:12:56.320 - 00:12:58.949` | the second block that is block numbers |
| `00:12:58.959 - 00:12:59.910` | one |
| `00:12:59.920 - 00:13:02.310` | is uh the superblock it's a block that's |
| `00:13:02.320 - 00:13:04.389` | read in at startup and it contains a |
| `00:13:04.399 - 00:13:07.670` | number of values and these values never |
| `00:13:07.680 - 00:13:09.509` | change and this block is never updated |
| `00:13:09.519 - 00:13:11.430` | this just basically tells us where the |
| `00:13:11.440 - 00:13:13.030` | log and the |
| `00:13:13.040 - 00:13:15.509` | bitmap and the inode array |
| `00:13:15.519 - 00:13:16.790` | are on the |
| `00:13:16.800 - 00:13:17.670` | disk |
| `00:13:17.680 - 00:13:19.269` | here's the magic number that's related |
| `00:13:19.279 - 00:13:21.190` | to this field |
| `00:13:21.200 - 00:13:24.310` | inodes contain 12 direct pointers okay |
| `00:13:24.320 - 00:13:26.470` | that's this number here |
| `00:13:26.480 - 00:13:29.350` | and the indirect block will contain |
| `00:13:29.360 - 00:13:31.430` | a number of pointers given by this |
| `00:13:31.440 - 00:13:33.430` | constant here we take the block size and |
| `00:13:33.440 - 00:13:35.670` | divide it by the size of a block number |
| `00:13:35.680 - 00:13:38.310` | and that's the 256 number i |
| `00:13:38.320 - 00:13:40.470` | referred to earlier |
| `00:13:40.480 - 00:13:42.870` | uh the files have a maximum size |
| `00:13:42.880 - 00:13:44.150` | determined by the number of direct |
| `00:13:44.160 - 00:13:45.910` | pointers plus the number of indirect |
| `00:13:45.920 - 00:13:49.189` | pointers so that's 12 plus 256 and given |
| `00:13:49.199 - 00:13:52.550` | that the block size is 200 is is 1024 |
| `00:13:52.560 - 00:13:55.509` | bytes that imposes a maximum size on |
| `00:13:55.519 - 00:13:57.189` | every block |
| `00:13:57.199 - 00:14:00.790` | the inode as it's kept on disk has this |
| `00:14:00.800 - 00:14:02.470` | uh structure here |
| `00:14:02.480 - 00:14:04.870` | uh the type the major and minor numbers |
| `00:14:04.880 - 00:14:06.629` | the number of hard lengths the size and |
| `00:14:06.639 - 00:14:09.110` | bytes and this array with |
| `00:14:09.120 - 00:14:12.790` | 12 direct pointers and one indirect |
| `00:14:12.800 - 00:14:14.790` | block which is pointed to by the 13th |
| `00:14:14.800 - 00:14:17.110` | element in this array |
| `00:14:17.120 - 00:14:21.110` | let's go to the next page which is here |
| `00:14:21.120 - 00:14:21.910` | this |
| `00:14:21.920 - 00:14:23.910` | constant here is the number of inodes we |
| `00:14:23.920 - 00:14:27.509` | can squeeze into one block okay here i |
| `00:14:27.519 - 00:14:29.509` | indicated it was four |
| `00:14:29.519 - 00:14:32.629` | and maybe uh the block size is evenly |
| `00:14:32.639 - 00:14:34.069` | divisible by |
| `00:14:34.079 - 00:14:37.509` | the size of the inode structure uh or |
| `00:14:37.519 - 00:14:39.030` | maybe it's not in which case we have a |
| `00:14:39.040 - 00:14:41.030` | little bit of extra space but in any |
| `00:14:41.040 - 00:14:43.189` | case it's not really four it's given by |
| `00:14:43.199 - 00:14:46.870` | this inodes per block value here that is |
| `00:14:46.880 - 00:14:48.790` | determined by the size of the block and |
| `00:14:48.800 - 00:14:53.509` | the size of the inode structure |
| `00:14:53.519 - 00:14:56.069` | okay which block on disk will contain a |
| `00:14:56.079 - 00:14:58.389` | particular inode so |
| `00:14:58.399 - 00:15:00.710` | this function this macro preprocessor |
| `00:15:00.720 - 00:15:03.189` | function i block is passed an inode |
| `00:15:03.199 - 00:15:04.230` | number |
| `00:15:04.240 - 00:15:06.710` | and a pointer to the superblock which is |
| `00:15:06.720 - 00:15:08.629` | a structure in memory that has things |
| `00:15:08.639 - 00:15:11.350` | like where the inode array starts |
| `00:15:11.360 - 00:15:13.829` | in my example i started it at |
| `00:15:13.839 - 00:15:15.269` | 32 |
| `00:15:15.279 - 00:15:17.590` | and so we just do a little bit of |
| `00:15:17.600 - 00:15:20.230` | division here by the inodes per block |
| `00:15:20.240 - 00:15:22.550` | and add 32. |
| `00:15:22.560 - 00:15:25.030` | the bitmap will contain a number of bits |
| `00:15:25.040 - 00:15:26.870` | in each block and the number of bits in |
| `00:15:26.880 - 00:15:29.910` | each block is just 1024 bytes times 8 |
| `00:15:29.920 - 00:15:32.870` | bits per byte |
| `00:15:32.880 - 00:15:36.470` | so given a particular |
| `00:15:36.480 - 00:15:39.269` | bit number or block number |
| `00:15:39.279 - 00:15:41.590` | where which block in the bitmap will |
| `00:15:41.600 - 00:15:43.749` | contain that bit |
| `00:15:43.759 - 00:15:47.030` | okay so b is the block number and this |
| `00:15:47.040 - 00:15:49.030` | uh b block will |
| `00:15:49.040 - 00:15:50.790` | compute |
| `00:15:50.800 - 00:15:53.350` | b divided by the number of uh |
| `00:15:53.360 - 00:15:56.550` | bits per block and where the bitmap |
| `00:15:56.560 - 00:15:58.150` | starts |
| `00:15:58.160 - 00:15:59.269` | the |
| `00:15:59.279 - 00:16:01.350` | directory contains a bunch of files and |
| `00:16:01.360 - 00:16:03.910` | each file can have up to 14 characters |
| `00:16:03.920 - 00:16:06.470` | in its name and so that's directory size |
| `00:16:06.480 - 00:16:07.430` | here |
| `00:16:07.440 - 00:16:11.269` | and the directories will contain |
| `00:16:11.279 - 00:16:13.910` | a number of these directory entries |
| `00:16:13.920 - 00:16:15.110` | which has |
| `00:16:15.120 - 00:16:17.110` | each one of these structures has |
| `00:16:17.120 - 00:16:19.509` | an inode number and |
| `00:16:19.519 - 00:16:20.550` | 14 |
| `00:16:20.560 - 00:16:23.670` | characters so it can accommodate a file |
| `00:16:23.680 - 00:16:27.430` | name size up to 14. |
| `00:16:27.440 - 00:16:30.629` | okay now i also want to take a look in |
| `00:16:30.639 - 00:16:31.749` | this file |
| `00:16:31.759 - 00:16:33.670` | file.h which |
| `00:16:33.680 - 00:16:35.910` | contains a number of different things |
| `00:16:35.920 - 00:16:38.310` | but in particular it contains |
| `00:16:38.320 - 00:16:40.389` | the in memory |
| `00:16:40.399 - 00:16:41.590` | inode |
| `00:16:41.600 - 00:16:42.310` | so |
| `00:16:42.320 - 00:16:45.430` | remember that our cache is made up of 50 |
| `00:16:45.440 - 00:16:47.670` | it turns out to be 50 of these inode |
| `00:16:47.680 - 00:16:48.949` | structures |
| `00:16:48.959 - 00:16:50.470` | and |
| `00:16:50.480 - 00:16:52.629` | we have uh the fields that i talked |
| `00:16:52.639 - 00:16:56.470` | about in this picture here so |
| `00:16:56.480 - 00:16:58.629` | here is the structure as i showed it |
| `00:16:58.639 - 00:16:59.670` | before |
| `00:16:59.680 - 00:17:02.629` | device inode number and the reference |
| `00:17:02.639 - 00:17:06.789` | count so device i know inode number and |
| `00:17:06.799 - 00:17:08.949` | the reference count and then we have a |
| `00:17:08.959 - 00:17:10.309` | sleep lock |
| `00:17:10.319 - 00:17:12.949` | and here's the sleep lock called lock |
| `00:17:12.959 - 00:17:15.590` | and this sleep lock protects everything |
| `00:17:15.600 - 00:17:18.630` | below that okay so we have the type |
| `00:17:18.640 - 00:17:20.870` | major minor in like the number of hard |
| `00:17:20.880 - 00:17:24.069` | lengths size and the uh pointers |
| `00:17:24.079 - 00:17:27.029` | uh it also protects the valid field uh i |
| `00:17:27.039 - 00:17:29.510` | didn't really draw this quite correctly |
| `00:17:29.520 - 00:17:31.590` | but the sleep lock corrects the valid |
| `00:17:31.600 - 00:17:33.270` | flag as well |
| `00:17:33.280 - 00:17:34.950` | and then i mentioned the |
| `00:17:34.960 - 00:17:35.990` | device |
| `00:17:36.000 - 00:17:37.350` | switch |
| `00:17:37.360 - 00:17:38.390` | array |
| `00:17:38.400 - 00:17:40.470` | and that's given |
| `00:17:40.480 - 00:17:43.990` | right here these are the structures and |
| `00:17:44.000 - 00:17:46.470` | each one is a uh |
| `00:17:46.480 - 00:17:48.470` | structure that contains two pointers a |
| `00:17:48.480 - 00:17:51.430` | reed and a right field so that's uh was |
| `00:17:51.440 - 00:17:53.190` | shown in this picture here |
| `00:17:53.200 - 00:17:55.909` | this structure here contains two fields |
| `00:17:55.919 - 00:17:58.710` | called read and write and each of these |
| `00:17:58.720 - 00:18:00.870` | is a pointer to a function |
| `00:18:00.880 - 00:18:04.150` | okay the function takes three arguments |
| `00:18:04.160 - 00:18:06.470` | and returns uh an |
| `00:18:06.480 - 00:18:08.070` | integer value |
| `00:18:08.080 - 00:18:09.750` | and the array itself is declared |
| `00:18:09.760 - 00:18:11.350` | elsewhere |
| `00:18:11.360 - 00:18:13.350` | okay that's it for oh oh wait we also |
| `00:18:13.360 - 00:18:16.310` | have this console which is |
| `00:18:16.320 - 00:18:17.669` | the |
| `00:18:17.679 - 00:18:19.990` | number in the array of the console |
| `00:18:20.000 - 00:18:22.230` | device |
| `00:18:22.240 - 00:18:23.909` | there are a couple of other definitions |
| `00:18:23.919 - 00:18:25.430` | that i wanted to cover and these are |
| `00:18:25.440 - 00:18:30.070` | coming out of the file fs.c not dot h |
| `00:18:30.080 - 00:18:31.270` | here is the |
| `00:18:31.280 - 00:18:34.150` | super block variable sb where the super |
| `00:18:34.160 - 00:18:37.430` | block is read into at startup time |
| `00:18:37.440 - 00:18:39.029` | then we have a macro function called |
| `00:18:39.039 - 00:18:41.430` | minimum which is defined pretty much as |
| `00:18:41.440 - 00:18:43.110` | you would expect it to be |
| `00:18:43.120 - 00:18:45.590` | remember that as a kernel programmer you |
| `00:18:45.600 - 00:18:47.270` | have to define everything you're going |
| `00:18:47.280 - 00:18:49.830` | to use you can't rely on library |
| `00:18:49.840 - 00:18:51.350` | functions that you would normally rely |
| `00:18:51.360 - 00:18:53.990` | on as a regular programmer |
| `00:18:54.000 - 00:18:55.350` | there's something else that i wanted to |
| `00:18:55.360 - 00:18:57.510` | mention and that's the eye table |
| `00:18:57.520 - 00:18:58.630` | structure |
| `00:18:58.640 - 00:19:01.430` | here is the picture of it that i used |
| `00:19:01.440 - 00:19:03.750` | this is where we have the cache of |
| `00:19:03.760 - 00:19:05.190` | inodes |
| `00:19:05.200 - 00:19:07.750` | and it has a spin lock |
| `00:19:07.760 - 00:19:10.150` | and it has this array of inode |
| `00:19:10.160 - 00:19:13.190` | structures that we will use as our cache |
| `00:19:13.200 - 00:19:14.950` | and i also wanted to mention something |
| `00:19:14.960 - 00:19:17.029` | from the file |
| `00:19:17.039 - 00:19:20.390` | file.c and that is this array i |
| `00:19:20.400 - 00:19:22.310` | discussed previously called device |
| `00:19:22.320 - 00:19:23.270` | switch |
| `00:19:23.280 - 00:19:24.470` | which has |
| `00:19:24.480 - 00:19:26.950` | 10 as it turns out elements |
| `00:19:26.960 - 00:19:28.150` | i won't talk about the rest of this |
| `00:19:28.160 - 00:19:30.070` | stuff right now but i'll cover this in a |
| `00:19:30.080 - 00:19:32.390` | later video |
| `00:19:32.400 - 00:19:34.870` | there are a lot of functions in fs.c in |
| `00:19:34.880 - 00:19:37.190` | fact they're about 25 functions |
| `00:19:37.200 - 00:19:38.630` | the code in some of these functions is |
| `00:19:38.640 - 00:19:40.950` | pretty complicated and before i get into |
| `00:19:40.960 - 00:19:43.350` | the code itself i want to just introduce |
| `00:19:43.360 - 00:19:45.430` | these functions by name and talk a |
| `00:19:45.440 - 00:19:47.830` | little bit about each one of them |
| `00:19:47.840 - 00:19:49.270` | the first three functions are concerned |
| `00:19:49.280 - 00:19:51.029` | with initialization |
| `00:19:51.039 - 00:19:54.310` | i init is for initializing the cache of |
| `00:19:54.320 - 00:19:55.669` | inodes |
| `00:19:55.679 - 00:19:58.310` | uh file system init is uh past the |
| `00:19:58.320 - 00:20:01.270` | device number and it initializes things |
| `00:20:01.280 - 00:20:04.549` | and among other things it calls read sb |
| `00:20:04.559 - 00:20:07.430` | which will read in the super block from |
| `00:20:07.440 - 00:20:09.669` | the disk and store it in this super |
| `00:20:09.679 - 00:20:12.390` | block structure |
| `00:20:12.400 - 00:20:15.669` | the function b0 is past a block number |
| `00:20:15.679 - 00:20:18.390` | and it will go out to disk and |
| `00:20:18.400 - 00:20:23.190` | write all zeros to that block |
| `00:20:23.200 - 00:20:25.750` | b alec is used when we need to allocate |
| `00:20:25.760 - 00:20:27.909` | a block for example when we're growing a |
| `00:20:27.919 - 00:20:28.789` | file |
| `00:20:28.799 - 00:20:29.830` | and so |
| `00:20:29.840 - 00:20:31.830` | it will search the |
| `00:20:31.840 - 00:20:34.149` | bitmap to find a block that is currently |
| `00:20:34.159 - 00:20:36.230` | free and it will change the bitmap to |
| `00:20:36.240 - 00:20:38.470` | indicate that it's now in use and return |
| `00:20:38.480 - 00:20:40.870` | the number of that block |
| `00:20:40.880 - 00:20:42.950` | to free the block after we're done with |
| `00:20:42.960 - 00:20:46.390` | it we can call b free which is past a |
| `00:20:46.400 - 00:20:48.950` | block number and it will change the bit |
| `00:20:48.960 - 00:20:51.350` | in the bitmap to indicate that the block |
| `00:20:51.360 - 00:20:52.310` | is now |
| `00:20:52.320 - 00:20:53.909` | free |
| `00:20:53.919 - 00:20:56.710` | the i alloc function is used when we're |
| `00:20:56.720 - 00:20:58.950` | creating a new file |
| `00:20:58.960 - 00:21:01.669` | and we say which device we want to |
| `00:21:01.679 - 00:21:04.630` | create it on and the type of the new |
| `00:21:04.640 - 00:21:06.710` | file this could be a regular file a |
| `00:21:06.720 - 00:21:09.430` | directory or a device file |
| `00:21:09.440 - 00:21:12.549` | and it returns a pointer to the inode |
| `00:21:12.559 - 00:21:15.270` | that is a to a structure in the inode |
| `00:21:15.280 - 00:21:16.630` | cache |
| `00:21:16.640 - 00:21:17.830` | what it will do |
| `00:21:17.840 - 00:21:20.310` | is it will read the disk to locate an |
| `00:21:20.320 - 00:21:23.669` | unused inode in the inode array and so |
| `00:21:23.679 - 00:21:25.990` | at that point it determines what the i |
| `00:21:26.000 - 00:21:28.789` | number of the new file will be |
| `00:21:28.799 - 00:21:31.590` | remember that the i notes in this array |
| `00:21:31.600 - 00:21:34.149` | will have a type value of 0 to indicate |
| `00:21:34.159 - 00:21:36.230` | that they're free and so |
| `00:21:36.240 - 00:21:39.270` | this function will set the type |
| `00:21:39.280 - 00:21:42.549` | on the disk to indicate that that i node |
| `00:21:42.559 - 00:21:44.549` | is now used |
| `00:21:44.559 - 00:21:47.190` | and then it will call i get and return |
| `00:21:47.200 - 00:21:49.990` | uh a pointer to the inode and so what |
| `00:21:50.000 - 00:21:51.990` | does i get do |
| `00:21:52.000 - 00:21:53.350` | well at this point |
| `00:21:53.360 - 00:21:56.630` | we've got the i number of the new file |
| `00:21:56.640 - 00:21:59.669` | and we allocate a |
| `00:21:59.679 - 00:22:03.110` | structure in the inode cache okay if we |
| `00:22:03.120 - 00:22:05.190` | already have a structure for that |
| `00:22:05.200 - 00:22:07.990` | particular i number then we |
| `00:22:08.000 - 00:22:10.789` | go ahead and use that one but in either |
| `00:22:10.799 - 00:22:13.430` | case we increment the reference count to |
| `00:22:13.440 - 00:22:15.750` | indicate that this particular inode in |
| `00:22:15.760 - 00:22:16.870` | the cache |
| `00:22:16.880 - 00:22:18.630` | is in use |
| `00:22:18.640 - 00:22:21.029` | we will not actually read in the inode |
| `00:22:21.039 - 00:22:23.750` | data from the disk okay remember there's |
| `00:22:23.760 - 00:22:26.390` | a valid flag in the inode structure |
| `00:22:26.400 - 00:22:28.470` | and so that may remain |
| `00:22:28.480 - 00:22:30.070` | zero to indicate that there's no valid |
| `00:22:30.080 - 00:22:31.669` | data and |
| `00:22:31.679 - 00:22:34.390` | each inode in the cache has a lock |
| `00:22:34.400 - 00:22:37.510` | associated with it and i get will not |
| `00:22:37.520 - 00:22:39.029` | set the lock |
| `00:22:39.039 - 00:22:40.630` | okay if we want to set the lock we can |
| `00:22:40.640 - 00:22:42.390` | call i lock |
| `00:22:42.400 - 00:22:44.789` | which is pass the pointer to the inode |
| `00:22:44.799 - 00:22:46.390` | and it will |
| `00:22:46.400 - 00:22:47.750` | set the |
| `00:22:47.760 - 00:22:50.950` | sleep lock in the inode |
| `00:22:50.960 - 00:22:54.789` | furthermore if the data in that node is |
| `00:22:54.799 - 00:22:57.029` | has not yet been read in from disk then |
| `00:22:57.039 - 00:23:00.390` | the valid flag will be false or zero and |
| `00:23:00.400 - 00:23:03.750` | this function will also read in the data |
| `00:23:03.760 - 00:23:05.990` | from the inode structure on disk and |
| `00:23:06.000 - 00:23:09.029` | store it in the cache version |
| `00:23:09.039 - 00:23:12.390` | and there is an i unlock function which |
| `00:23:12.400 - 00:23:22.390` | will simply unlock the inode |
| `00:23:22.400 - 00:23:25.510` | next we look at the i update function |
| `00:23:25.520 - 00:23:28.149` | and this is passed a pointer to an inode |
| `00:23:28.159 - 00:23:30.549` | in the inode cache and after we've made |
| `00:23:30.559 - 00:23:32.710` | changes to the inode we need to write |
| `00:23:32.720 - 00:23:34.789` | that inode back to disk and that's what |
| `00:23:34.799 - 00:23:37.350` | this function will do |
| `00:23:37.360 - 00:23:39.029` | from time to time we need to increment |
| `00:23:39.039 - 00:23:42.310` | the reference count and so we can call i |
| `00:23:42.320 - 00:23:43.669` | dupe |
| `00:23:43.679 - 00:23:45.990` | to increment that reference count |
| `00:23:46.000 - 00:23:47.190` | remember that the reference count is |
| `00:23:47.200 - 00:23:49.669` | protected by the lock for the entire |
| `00:23:49.679 - 00:23:50.470` | cache |
| `00:23:50.480 - 00:23:53.110` | the itable.lock field |
| `00:23:53.120 - 00:23:55.110` | so it'll have to set that in order to |
| `00:23:55.120 - 00:23:57.510` | increment the reference count |
| `00:23:57.520 - 00:23:58.630` | and |
| `00:23:58.640 - 00:24:01.269` | the i put function is passed pointer to |
| `00:24:01.279 - 00:24:03.750` | an inode and it will decrement the |
| `00:24:03.760 - 00:24:05.269` | reference count |
| `00:24:05.279 - 00:24:07.669` | now if uh this particular thread is the |
| `00:24:07.679 - 00:24:10.310` | last thread that's using that inode in |
| `00:24:10.320 - 00:24:12.870` | the cache then the reference count will |
| `00:24:12.880 - 00:24:14.470` | have gone to zero |
| `00:24:14.480 - 00:24:17.269` | and at that point we can also ask or |
| `00:24:17.279 - 00:24:18.950` | this function will also ask whether |
| `00:24:18.960 - 00:24:21.830` | there are any hard links to this file |
| `00:24:21.840 - 00:24:24.070` | and in link is zero it means there are |
| `00:24:24.080 - 00:24:26.549` | no references to this file from any |
| `00:24:26.559 - 00:24:29.750` | directory so at that point this file is |
| `00:24:29.760 - 00:24:31.669` | not accessible there is no path name |
| `00:24:31.679 - 00:24:35.110` | which can get to it so this file needs |
| `00:24:35.120 - 00:24:35.990` | to be |
| `00:24:36.000 - 00:24:38.149` | released and freed the space associated |
| `00:24:38.159 - 00:24:39.750` | with it has to be freed |
| `00:24:39.760 - 00:24:42.630` | so what it will do in that case is |
| `00:24:42.640 - 00:24:44.149` | lock the inode |
| `00:24:44.159 - 00:24:46.789` | truncate the file to a length of zero |
| `00:24:46.799 - 00:24:49.110` | which will free all the data blocks and |
| `00:24:49.120 - 00:24:51.590` | then it will call this i update function |
| `00:24:51.600 - 00:24:55.110` | here to write the modified inode back to |
| `00:24:55.120 - 00:24:56.149` | disk |
| `00:24:56.159 - 00:24:58.470` | and at that point the uh |
| `00:24:58.480 - 00:24:59.269` | the |
| `00:24:59.279 - 00:25:03.110` | file is freed |
| `00:25:03.120 - 00:25:05.750` | the function i unlock put |
| `00:25:05.760 - 00:25:09.830` | is just calling i unlock followed by i |
| `00:25:09.840 - 00:25:12.390` | put |
| `00:25:12.400 - 00:25:15.669` | and then we have this bmap function |
| `00:25:15.679 - 00:25:18.149` | uh it's passed a pointer to an inode so |
| `00:25:18.159 - 00:25:20.070` | we've got some file that we're concerned |
| `00:25:20.080 - 00:25:22.230` | about and we've got the inode cached in |
| `00:25:22.240 - 00:25:24.070` | the inode cache |
| `00:25:24.080 - 00:25:26.630` | and we are trying to get some data out |
| `00:25:26.640 - 00:25:28.630` | of the file or perhaps write data into |
| `00:25:28.640 - 00:25:29.830` | the file |
| `00:25:29.840 - 00:25:33.350` | so we figure out which block in the file |
| `00:25:33.360 - 00:25:35.990` | with the first byte of the file being in |
| `00:25:36.000 - 00:25:38.149` | block 0 of that file so we have the |
| `00:25:38.159 - 00:25:39.350` | block number |
| `00:25:39.360 - 00:25:41.430` | relative to the beginning of the file |
| `00:25:41.440 - 00:25:43.909` | and we need to translate it to a block |
| `00:25:43.919 - 00:25:46.789` | on the disk and that's what bmap does so |
| `00:25:46.799 - 00:25:49.830` | it will consult the |
| `00:25:49.840 - 00:25:52.870` | the array of block pointers of direct |
| `00:25:52.880 - 00:25:55.990` | block pointers and if it is a block |
| `00:25:56.000 - 00:25:58.390` | beyond the 12th block it will look at |
| `00:25:58.400 - 00:26:02.070` | the block of in direction and it will uh |
| `00:26:02.080 - 00:26:05.350` | find a pointer to locate the |
| `00:26:05.360 - 00:26:06.470` | block |
| `00:26:06.480 - 00:26:08.549` | on the disk so it will figure out the |
| `00:26:08.559 - 00:26:11.350` | block number on the desk |
| `00:26:11.360 - 00:26:13.909` | if that block is not actually allocated |
| `00:26:13.919 - 00:26:17.110` | in the file okay it's not in the file |
| `00:26:17.120 - 00:26:19.590` | then this function will also allocate a |
| `00:26:19.600 - 00:26:21.909` | free block and add it to the file |
| `00:26:21.919 - 00:26:24.950` | and it will also update the inode |
| `00:26:24.960 - 00:26:26.789` | by adding it to |
| `00:26:26.799 - 00:26:29.590` | either the array of direct pointers or |
| `00:26:29.600 - 00:26:33.510` | the block of indirect pointers |
| `00:26:33.520 - 00:26:35.190` | next let's look at the function i |
| `00:26:35.200 - 00:26:37.750` | truncate which is passed a pointer to an |
| `00:26:37.760 - 00:26:40.630` | inode which is in the inode cache and it |
| `00:26:40.640 - 00:26:43.830` | will truncate this file to size 0. |
| `00:26:43.840 - 00:26:46.390` | it will free all the data blocks in this |
| `00:26:46.400 - 00:26:49.350` | file and it will free the indirection |
| `00:26:49.360 - 00:26:51.750` | block too if there is one and set the |
| `00:26:51.760 - 00:26:52.549` | size |
| `00:26:52.559 - 00:26:55.750` | attribute to zero and call i update to |
| `00:26:55.760 - 00:26:58.390` | update the inode on the disk this is |
| `00:26:58.400 - 00:27:01.830` | called among other places from the i put |
| `00:27:01.840 - 00:27:04.070` | function remember with i put we |
| `00:27:04.080 - 00:27:05.669` | basically call this when we're done with |
| `00:27:05.679 - 00:27:07.990` | a particular file and it will decrement |
| `00:27:08.000 - 00:27:10.390` | the reference count and if there are no |
| `00:27:10.400 - 00:27:12.870` | hard links to it then it will truncate |
| `00:27:12.880 - 00:27:15.669` | the file to link zero |
| `00:27:15.679 - 00:27:20.070` | it calls i update as well as i truncate |
| `00:27:20.080 - 00:27:23.029` | and you might think well gee that is a |
| `00:27:23.039 - 00:27:24.630` | duplication of effort it's writing to |
| `00:27:24.640 - 00:27:26.470` | the disk twice but remember we're using |
| `00:27:26.480 - 00:27:28.310` | the disk logging system |
| `00:27:28.320 - 00:27:30.310` | and um |
| `00:27:30.320 - 00:27:32.710` | so there will only actually be one right |
| `00:27:32.720 - 00:27:34.549` | to the disk |
| `00:27:34.559 - 00:27:37.029` | the reason we call i update here |
| `00:27:37.039 - 00:27:40.310` | a second time is because we are also |
| `00:27:40.320 - 00:27:42.549` | setting the type field to zero which i |
| `00:27:42.559 - 00:27:45.029` | did not include in this |
| `00:27:45.039 - 00:27:47.430` | so let's add that there |
| `00:27:47.440 - 00:27:49.750` | and we need to set the type field to |
| `00:27:49.760 - 00:27:52.070` | zero and then write that to the disk so |
| `00:27:52.080 - 00:27:54.870` | that the on disk inode structure |
| `00:27:54.880 - 00:27:56.310` | will have a type of zero which will |
| `00:27:56.320 - 00:27:59.029` | indicate that this particular inode is |
| `00:27:59.039 - 00:28:01.990` | free and can be recycled |
| `00:28:02.000 - 00:28:06.070` | okay the next function is stat i |
| `00:28:06.080 - 00:28:08.789` | and this is passed a pointer to an inode |
| `00:28:08.799 - 00:28:11.269` | and to a structure called a stat |
| `00:28:11.279 - 00:28:12.870` | structure and all it's going to do is |
| `00:28:12.880 - 00:28:15.269` | copy data from the inode to someplace |
| `00:28:15.279 - 00:28:17.350` | else |
| `00:28:17.360 - 00:28:20.389` | now we want to look at the read eye and |
| `00:28:20.399 - 00:28:22.549` | right eye functions these are used to |
| `00:28:22.559 - 00:28:26.549` | actually move data into or out of a file |
| `00:28:26.559 - 00:28:29.029` | so read eye is called with a pointer to |
| `00:28:29.039 - 00:28:31.190` | at inode that's in the cache |
| `00:28:31.200 - 00:28:34.630` | and a destination address of where to |
| `00:28:34.640 - 00:28:36.789` | put the data in memory |
| `00:28:36.799 - 00:28:38.710` | as well as an offset |
| `00:28:38.720 - 00:28:42.070` | in the file of where to get the data and |
| `00:28:42.080 - 00:28:44.470` | the number of bytes to transfer this |
| `00:28:44.480 - 00:28:47.110` | destination address is either a physical |
| `00:28:47.120 - 00:28:50.549` | address or it is a virtual address in |
| `00:28:50.559 - 00:28:51.350` | some |
| `00:28:51.360 - 00:28:53.669` | user's virtual address space and that's |
| `00:28:53.679 - 00:28:56.230` | determined by this flag zero if |
| `00:28:56.240 - 00:28:57.669` | destination address is a physical |
| `00:28:57.679 - 00:29:00.310` | address one if it's a virtual address |
| `00:29:00.320 - 00:29:02.630` | and it'll return the number of bytes |
| `00:29:02.640 - 00:29:05.510` | read |
| `00:29:05.520 - 00:29:07.750` | the right eye is just the |
| `00:29:07.760 - 00:29:09.990` | other direction and here we have the |
| `00:29:10.000 - 00:29:12.710` | same arguments a pointer to an inode the |
| `00:29:12.720 - 00:29:13.830` | flag |
| `00:29:13.840 - 00:29:15.909` | the source from where we're getting the |
| `00:29:15.919 - 00:29:16.870` | data |
| `00:29:16.880 - 00:29:19.190` | and the number of bytes and where in the |
| `00:29:19.200 - 00:29:21.510` | file we will be writing it to and then |
| `00:29:21.520 - 00:29:23.350` | it returns the number of bytes correctly |
| `00:29:23.360 - 00:29:25.430` | written |
| `00:29:25.440 - 00:29:28.389` | and then we have the function directory |
| `00:29:28.399 - 00:29:29.510` | lookup |
| `00:29:29.520 - 00:29:33.029` | and this is passed a pointer to an inode |
| `00:29:33.039 - 00:29:35.190` | okay that's a directory file or at least |
| `00:29:35.200 - 00:29:37.350` | it should be a directory file |
| `00:29:37.360 - 00:29:38.870` | and |
| `00:29:38.880 - 00:29:41.750` | a name okay this is uh something that's |
| `00:29:41.760 - 00:29:43.590` | uh 14 characters or |
| `00:29:43.600 - 00:29:44.630` | fewer |
| `00:29:44.640 - 00:29:45.590` | and |
| `00:29:45.600 - 00:29:47.750` | we also have an offset variable and |
| `00:29:47.760 - 00:29:49.830` | we'll pass the address of that |
| `00:29:49.840 - 00:29:51.750` | so what it's going to do is look this |
| `00:29:51.760 - 00:29:53.669` | name up in |
| `00:29:53.679 - 00:29:56.149` | the file that's pointed to by this inode |
| `00:29:56.159 - 00:29:57.590` | here |
| `00:29:57.600 - 00:30:00.389` | and if we find this name in the |
| `00:30:00.399 - 00:30:04.389` | directory then we're going to call i get |
| `00:30:04.399 - 00:30:05.510` | which will |
| `00:30:05.520 - 00:30:09.430` | add an inode for this file |
| `00:30:09.440 - 00:30:12.870` | into the inode cache it will not lock it |
| `00:30:12.880 - 00:30:14.950` | okay |
| `00:30:14.960 - 00:30:17.750` | and it will then return the inode of the |
| `00:30:17.760 - 00:30:20.310` | file that we just looked up |
| `00:30:20.320 - 00:30:22.389` | in addition we've got this offset |
| `00:30:22.399 - 00:30:24.789` | variable and so |
| `00:30:24.799 - 00:30:27.350` | this function will also save |
| `00:30:27.360 - 00:30:30.230` | the offset in the directory of the |
| `00:30:30.240 - 00:30:33.190` | directory entry for this particular file |
| `00:30:33.200 - 00:30:35.830` | so it will save the location of this |
| `00:30:35.840 - 00:30:38.149` | directory entry within the directory |
| `00:30:38.159 - 00:30:39.110` | array |
| `00:30:39.120 - 00:30:41.909` | the directory file in this variable |
| `00:30:41.919 - 00:30:43.269` | offset |
| `00:30:43.279 - 00:30:45.350` | and if the file is not found it will |
| `00:30:45.360 - 00:30:48.470` | return zero |
| `00:30:48.480 - 00:30:49.830` | the next function to look at is |
| `00:30:49.840 - 00:30:52.870` | directory link which is used to add an |
| `00:30:52.880 - 00:30:54.950` | entry to a directory so it's passed a |
| `00:30:54.960 - 00:30:56.549` | pointer to |
| `00:30:56.559 - 00:30:58.789` | an inode that represents the directory |
| `00:30:58.799 - 00:31:02.470` | as well as a pointer to a name and the i |
| `00:31:02.480 - 00:31:05.110` | number and it will add a name number |
| `00:31:05.120 - 00:31:07.990` | pair to this file |
| `00:31:08.000 - 00:31:09.909` | it will return zero if everything went |
| `00:31:09.919 - 00:31:12.789` | okay and minus one if there's a failure |
| `00:31:12.799 - 00:31:15.350` | and the failure can happen if there is |
| `00:31:15.360 - 00:31:17.750` | already an entry for that name in the |
| `00:31:17.760 - 00:31:19.029` | directory |
| `00:31:19.039 - 00:31:21.830` | and this function will do reads from the |
| `00:31:21.840 - 00:31:25.350` | disk as necessary to check and after it |
| `00:31:25.360 - 00:31:28.549` | adds the entry it will write that |
| `00:31:28.559 - 00:31:30.389` | back to the disk |
| `00:31:30.399 - 00:31:31.990` | by the way all of these functions that |
| `00:31:32.000 - 00:31:34.389` | we've talked about so far are reading |
| `00:31:34.399 - 00:31:37.029` | from the disk and many of them are |
| `00:31:37.039 - 00:31:39.590` | writing as well with a log write |
| `00:31:39.600 - 00:31:41.029` | operation |
| `00:31:41.039 - 00:31:44.149` | these reads and the corresponding writes |
| `00:31:44.159 - 00:31:47.190` | and the releases the b release function |
| `00:31:47.200 - 00:31:48.389` | calls |
| `00:31:48.399 - 00:31:49.509` | are all |
| `00:31:49.519 - 00:31:52.230` | part of transactions but none of these |
| `00:31:52.240 - 00:31:54.789` | functions include the transaction itself |
| `00:31:54.799 - 00:31:56.710` | remember the transaction begins with a |
| `00:31:56.720 - 00:32:00.389` | call to the begin op function and ends |
| `00:32:00.399 - 00:32:03.269` | with a call to the end up function none |
| `00:32:03.279 - 00:32:06.870` | of these functions in fs.c in the file |
| `00:32:06.880 - 00:32:08.789` | fs.c |
| `00:32:08.799 - 00:32:11.590` | contain calls to begin up or end up but |
| `00:32:11.600 - 00:32:14.389` | they are all used within code that is |
| `00:32:14.399 - 00:32:17.509` | part of a transaction |
| `00:32:17.519 - 00:32:18.870` | the next function |
| `00:32:18.880 - 00:32:20.549` | skip element |
| `00:32:20.559 - 00:32:22.470` | is not a function that |
| `00:32:22.480 - 00:32:24.549` | accesses the disk it doesn't do any |
| `00:32:24.559 - 00:32:26.789` | reading or writing it's simply a string |
| `00:32:26.799 - 00:32:28.870` | processing function |
| `00:32:28.880 - 00:32:31.029` | it's passed a pointer to a path name |
| `00:32:31.039 - 00:32:33.430` | string somewhere in memory such as this |
| `00:32:33.440 - 00:32:35.750` | uh string right here |
| `00:32:35.760 - 00:32:37.110` | and so it's passed a pointer to the |
| `00:32:37.120 - 00:32:38.630` | first character |
| `00:32:38.640 - 00:32:42.149` | and it will parse it essentially it will |
| `00:32:42.159 - 00:32:46.470` | find uh where the first element is |
| `00:32:46.480 - 00:32:49.909` | and it will store that in a variable |
| `00:32:49.919 - 00:32:51.909` | called name so it's passed an address of |
| `00:32:51.919 - 00:32:55.430` | some 14 byte area and it will store |
| `00:32:55.440 - 00:32:57.110` | whatever the first element in this path |
| `00:32:57.120 - 00:32:58.310` | name is |
| `00:32:58.320 - 00:33:01.269` | and in this case it's the characters usr |
| `00:33:01.279 - 00:33:03.430` | in the name variable |
| `00:33:03.440 - 00:33:06.549` | and it will also advance the pointer to |
| `00:33:06.559 - 00:33:08.870` | the next element in the |
| `00:33:08.880 - 00:33:12.549` | path name and return a pointer to the |
| `00:33:12.559 - 00:33:14.070` | next element so it'll return a pointer |
| `00:33:14.080 - 00:33:15.190` | to |
| `00:33:15.200 - 00:33:16.549` | this |
| `00:33:16.559 - 00:33:18.310` | if the path name is empty and there are |
| `00:33:18.320 - 00:33:23.590` | no more elements it will return zero |
| `00:33:23.600 - 00:33:25.909` | okay finally we have this name i |
| `00:33:25.919 - 00:33:27.269` | function and this is what we've sort of |
| `00:33:27.279 - 00:33:29.029` | been working toward we're passed a |
| `00:33:29.039 - 00:33:31.909` | pointer to a path name and it's going to |
| `00:33:31.919 - 00:33:34.549` | find that file on disk |
| `00:33:34.559 - 00:33:36.630` | move the inode into the inode cache and |
| `00:33:36.640 - 00:33:39.269` | return a pointer to the cached inode |
| `00:33:39.279 - 00:33:41.990` | so the inode will be cached and the |
| `00:33:42.000 - 00:33:44.870` | reference count of that inode will be |
| `00:33:44.880 - 00:33:47.909` | incremented it will not be locked |
| `00:33:47.919 - 00:33:48.870` | and if |
| `00:33:48.880 - 00:33:50.789` | the file doesn't exist if this path name |
| `00:33:50.799 - 00:33:53.029` | is no good then this function will |
| `00:33:53.039 - 00:33:54.310` | return |
| `00:33:54.320 - 00:33:56.789` | null or zero |
| `00:33:56.799 - 00:33:59.190` | there's also a variation of this that |
| `00:33:59.200 - 00:34:01.110` | will do exactly the same thing it's |
| `00:34:01.120 - 00:34:03.269` | passed a pointer to a path name |
| `00:34:03.279 - 00:34:06.149` | and it returns a pointer to the inode in |
| `00:34:06.159 - 00:34:08.869` | the cache but it will stop one level |
| `00:34:08.879 - 00:34:11.750` | earlier so basically if it's past this |
| `00:34:11.760 - 00:34:14.310` | path name here it would return a pointer |
| `00:34:14.320 - 00:34:17.669` | to you the directory user slash bin |
| `00:34:17.679 - 00:34:20.470` | okay and it will save cat in |
| `00:34:20.480 - 00:34:22.950` | this name variable so we'll stop one |
| `00:34:22.960 - 00:34:25.669` | level earlier in the path name save the |
| `00:34:25.679 - 00:34:28.149` | last file name that is cap |
| `00:34:28.159 - 00:34:30.869` | in this name variable and it will return |
| `00:34:30.879 - 00:34:34.389` | the inode of the parent directory |
| `00:34:34.399 - 00:34:37.190` | and both of these are quite similar and |
| `00:34:37.200 - 00:34:39.829` | all they do is call the function name x |
| `00:34:39.839 - 00:34:42.950` | which actually does the work of both the |
| `00:34:42.960 - 00:34:46.389` | name i and the name i parent function |
| `00:34:46.399 - 00:34:48.470` | so name x is passed a pointer to a path |
| `00:34:48.480 - 00:34:51.349` | name and a flag to tell whether it's uh |
| `00:34:51.359 - 00:34:53.909` | doing the name i function or the name i |
| `00:34:53.919 - 00:34:55.270` | parent function |
| `00:34:55.280 - 00:34:56.710` | and uh |
| `00:34:56.720 - 00:34:58.790` | the name variable |
| `00:34:58.800 - 00:35:01.270` | and does the work of both name i and |
| `00:35:01.280 - 00:35:04.630` | name my parent and name x is not called |
| `00:35:04.640 - 00:35:06.790` | from anywhere else |
| `00:35:06.800 - 00:35:08.390` | okay that's it for this video in the |
| `00:35:08.400 - 00:35:10.230` | next video i will start looking at the |
| `00:35:10.240 - 00:35:12.390` | code for these functions |
| `00:35:12.400 - 00:35:15.720` | see you there |
