# xv6 Kernel-28: Disk Buffer Cache

| Field | Value |
| --- | --- |
| Video ID | `1ajlGSLOqJE` |
| URL | <https://www.youtube.com/watch?v=1ajlGSLOqJE> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.280 - 00:00:02.790` | in this and the next couple of videos |
| `00:00:02.800 - 00:00:04.390` | i'll be describing how the unix file |
| `00:00:04.400 - 00:00:06.150` | system is implemented and i'll talk |
| `00:00:06.160 - 00:00:08.870` | about the system calls like open close |
| `00:00:08.880 - 00:00:11.749` | read and write |
| `00:00:11.759 - 00:00:14.070` | this video is part of a playlist on the |
| `00:00:14.080 - 00:00:17.029` | xv6 operating system kernel x36 is a |
| `00:00:17.039 - 00:00:19.750` | simplified version of unix |
| `00:00:19.760 - 00:00:22.070` | there's a lot of complexity here and in |
| `00:00:22.080 - 00:00:24.310` | a complex software system we can start |
| `00:00:24.320 - 00:00:26.630` | by describing the low level functions |
| `00:00:26.640 - 00:00:28.790` | first and then gradually work our way up |
| `00:00:28.800 - 00:00:31.269` | to the higher level functions |
| `00:00:31.279 - 00:00:32.709` | in another approach we can start with |
| `00:00:32.719 - 00:00:34.229` | the high level functions and describe |
| `00:00:34.239 - 00:00:36.790` | those first followed by successive |
| `00:00:36.800 - 00:00:38.310` | levels of greater |
| `00:00:38.320 - 00:00:39.350` | detail |
| `00:00:39.360 - 00:00:41.270` | i will go with the first approach and |
| `00:00:41.280 - 00:00:43.830` | talk about the low level functions first |
| `00:00:43.840 - 00:00:45.910` | in this video i'll be talking about the |
| `00:00:45.920 - 00:00:47.990` | disk system and how we interface to the |
| `00:00:48.000 - 00:00:48.950` | disk |
| `00:00:48.960 - 00:00:51.270` | and i'll talk about the buffer caching |
| `00:00:51.280 - 00:00:52.709` | system |
| `00:00:52.719 - 00:00:57.270` | i'll cover the code in files buff.h and |
| `00:00:57.280 - 00:01:01.270` | buffer io or bio dot c |
| `00:01:01.280 - 00:01:02.869` | so let's begin by |
| `00:01:02.879 - 00:01:05.189` | talking about the disk |
| `00:01:05.199 - 00:01:07.350` | whenever we transfer data to or from |
| `00:01:07.360 - 00:01:09.990` | main memory it's in units of bytes or |
| `00:01:10.000 - 00:01:11.350` | words |
| `00:01:11.360 - 00:01:13.990` | with a disc we're transferring units in |
| `00:01:14.000 - 00:01:16.870` | terms of a larger thing called a block |
| `00:01:16.880 - 00:01:19.590` | the block is a fixed size chunk of bytes |
| `00:01:19.600 - 00:01:22.789` | in the case of xv6 our block size |
| `00:01:22.799 - 00:01:24.870` | is 1024 |
| `00:01:24.880 - 00:01:27.830` | and that's fixed by this constant b size |
| `00:01:27.840 - 00:01:29.749` | other systems like units use a different |
| `00:01:29.759 - 00:01:32.630` | block size but in xv6 |
| `00:01:32.640 - 00:01:33.830` | or in any |
| `00:01:33.840 - 00:01:35.990` | any operating system the block size is |
| `00:01:36.000 - 00:01:38.069` | always fixed |
| `00:01:38.079 - 00:01:40.789` | now the disk is viewed as nothing more |
| `00:01:40.799 - 00:01:43.350` | than a numbered sequence of blocks so |
| `00:01:43.360 - 00:01:46.230` | the blocks are numbered from 0 1 2 on up |
| `00:01:46.240 - 00:01:49.030` | to some maximum |
| `00:01:49.040 - 00:01:51.270` | the second block that is the block |
| `00:01:51.280 - 00:01:52.789` | number 1 |
| `00:01:52.799 - 00:01:55.109` | is special and it contains something |
| `00:01:55.119 - 00:01:57.190` | called the super block the super block |
| `00:01:57.200 - 00:01:59.190` | is fixed and doesn't change and it |
| `00:01:59.200 - 00:02:01.510` | contains a number of parameters one of |
| `00:02:01.520 - 00:02:03.030` | the parameters contained in the super |
| `00:02:03.040 - 00:02:05.350` | block is the size or the number of |
| `00:02:05.360 - 00:02:07.590` | blocks on the disk |
| `00:02:07.600 - 00:02:09.270` | and so we can read that and know how |
| `00:02:09.280 - 00:02:12.150` | many blocks are being available on this |
| `00:02:12.160 - 00:02:18.309` | disk for the file system to be stored in |
| `00:02:18.319 - 00:02:20.710` | the disk actually may read or write |
| `00:02:20.720 - 00:02:23.990` | bytes in another unit called the sector |
| `00:02:24.000 - 00:02:26.630` | so block and sector are sometimes used |
| `00:02:26.640 - 00:02:28.229` | synonymously but they really are |
| `00:02:28.239 - 00:02:29.510` | different things |
| `00:02:29.520 - 00:02:31.190` | generally speaking the sector size is a |
| `00:02:31.200 - 00:02:33.350` | bit smaller than the block |
| `00:02:33.360 - 00:02:36.630` | a typical example is that in unix the |
| `00:02:36.640 - 00:02:40.150` | block size is 4k or 1096 |
| `00:02:40.160 - 00:02:42.150` | while individual disks may have a |
| `00:02:42.160 - 00:02:44.470` | smaller sector size |
| `00:02:44.480 - 00:02:46.229` | one particular model of disk might have |
| `00:02:46.239 - 00:02:49.270` | a sector size of 512 while some other |
| `00:02:49.280 - 00:02:50.790` | kind of disk might have a different |
| `00:02:50.800 - 00:02:52.309` | sector size |
| `00:02:52.319 - 00:02:54.790` | but by having the operating system work |
| `00:02:54.800 - 00:02:57.350` | in terms of blocks we can |
| `00:02:57.360 - 00:02:59.830` | ignore the different sector sizes of the |
| `00:02:59.840 - 00:03:00.949` | individual |
| `00:03:00.959 - 00:03:03.270` | models of disk drive |
| `00:03:03.280 - 00:03:04.470` | whenever the |
| `00:03:04.480 - 00:03:06.550` | kernel wants to read or write from the |
| `00:03:06.560 - 00:03:09.589` | disk it reads or writes an entire block |
| `00:03:09.599 - 00:03:11.830` | and that will cause the reading or |
| `00:03:11.840 - 00:03:14.710` | writing of a number of sectors by the |
| `00:03:14.720 - 00:03:16.309` | device drivers |
| `00:03:16.319 - 00:03:18.149` | for example if the sector size of one |
| `00:03:18.159 - 00:03:21.750` | particular disk drive is 512 then every |
| `00:03:21.760 - 00:03:24.229` | time the kernel does a read or write |
| `00:03:24.239 - 00:03:27.589` | it will transfer 4096 bytes and that |
| `00:03:27.599 - 00:03:30.309` | will cause the reading or the writing of |
| `00:03:30.319 - 00:03:33.030` | well eight sectors |
| `00:03:33.040 - 00:03:35.589` | on a disk which is a rotational device |
| `00:03:35.599 - 00:03:38.070` | we have a number of different delays |
| `00:03:38.080 - 00:03:39.910` | we may have to move the head the read |
| `00:03:39.920 - 00:03:43.190` | write head in or out to other tracks and |
| `00:03:43.200 - 00:03:44.949` | we may also have to wait for the disk to |
| `00:03:44.959 - 00:03:46.470` | rotate so that the |
| `00:03:46.480 - 00:03:49.350` | indicated data that the desired sector |
| `00:03:49.360 - 00:03:52.229` | is underneath the read write head |
| `00:03:52.239 - 00:03:55.750` | by grouping sectors into a block |
| `00:03:55.760 - 00:03:58.710` | we can make sure that whenever we read |
| `00:03:58.720 - 00:03:59.990` | most of the sectors are read |
| `00:04:00.000 - 00:04:02.789` | sequentially we have to do a seek to the |
| `00:04:02.799 - 00:04:04.630` | proper track and |
| `00:04:04.640 - 00:04:06.789` | proper spot on the track |
| `00:04:06.799 - 00:04:09.270` | to read the first sector but the |
| `00:04:09.280 - 00:04:11.190` | remaining seven sectors will be read |
| `00:04:11.200 - 00:04:13.670` | sequentially which improves performance |
| `00:04:13.680 - 00:04:15.110` | quite a bit |
| `00:04:15.120 - 00:04:17.110` | of course there's a penalty in many |
| `00:04:17.120 - 00:04:20.710` | cases the last block in a file will only |
| `00:04:20.720 - 00:04:23.430` | be partially filled and the minimum |
| `00:04:23.440 - 00:04:26.270` | minimum file size will be the block size |
| `00:04:26.280 - 00:04:29.189` | 4096. so we could have some wasted space |
| `00:04:29.199 - 00:04:30.629` | but we do have an increase in |
| `00:04:30.639 - 00:04:32.629` | performance |
| `00:04:32.639 - 00:04:35.670` | now the device that is implemented by |
| `00:04:35.680 - 00:04:40.310` | kimu is the virt io disk device |
| `00:04:40.320 - 00:04:43.030` | remember that xv6 will be emulated by |
| `00:04:43.040 - 00:04:45.670` | something called the kimu emulator and |
| `00:04:45.680 - 00:04:46.790` | the kimo |
| `00:04:46.800 - 00:04:48.550` | emulator will |
| `00:04:48.560 - 00:04:51.990` | uh interface to a vert i o disk |
| `00:04:52.000 - 00:04:53.430` | uh this |
| `00:04:53.440 - 00:04:55.830` | vert io stuff is an attempt to |
| `00:04:55.840 - 00:04:57.990` | standardize the interface between device |
| `00:04:58.000 - 00:05:01.189` | drivers and actual hardware and there |
| `00:05:01.199 - 00:05:02.790` | are a number of different interfaces |
| `00:05:02.800 - 00:05:06.710` | there is uh vert i o disk and usb and |
| `00:05:06.720 - 00:05:08.629` | uart and some other stuff that are not |
| `00:05:08.639 - 00:05:11.029` | used by the xv6 system |
| `00:05:11.039 - 00:05:12.390` | but uh |
| `00:05:12.400 - 00:05:15.510` | a kimo will provide an interface to |
| `00:05:15.520 - 00:05:17.990` | something called the vert i o disk i |
| `00:05:18.000 - 00:05:19.909` | won't go over it in this video but the |
| `00:05:19.919 - 00:05:22.230` | thing we need to be concerned about |
| `00:05:22.240 - 00:05:25.990` | is that the disk driver provides one |
| `00:05:26.000 - 00:05:28.070` | function that can be used for the read |
| `00:05:28.080 - 00:05:30.550` | and the write operations |
| `00:05:30.560 - 00:05:32.629` | so this function which is called vert i |
| `00:05:32.639 - 00:05:34.710` | o disk read write |
| `00:05:34.720 - 00:05:38.390` | can either perform a read or a write and |
| `00:05:38.400 - 00:05:40.070` | which of those it does is determined by |
| `00:05:40.080 - 00:05:42.629` | the second argument |
| `00:05:42.639 - 00:05:44.950` | and the first argument is a pointer to a |
| `00:05:44.960 - 00:05:46.310` | buffer |
| `00:05:46.320 - 00:05:48.310` | the buffer is an example or an instance |
| `00:05:48.320 - 00:05:51.029` | of a buff structure |
| `00:05:51.039 - 00:05:54.710` | and one of these buffers will contain |
| `00:05:54.720 - 00:05:58.309` | enough space for the block in our case |
| `00:05:58.319 - 00:06:02.309` | 1024 bytes as well as the particular |
| `00:06:02.319 - 00:06:05.110` | number of the block that we want to read |
| `00:06:05.120 - 00:06:06.790` | or write |
| `00:06:06.800 - 00:06:10.309` | it contains some other stuff as well |
| `00:06:10.319 - 00:06:13.749` | now with the virt i o desk and the xv6 |
| `00:06:13.759 - 00:06:16.309` | system there is no error reporting |
| `00:06:16.319 - 00:06:18.550` | this function does not return anything |
| `00:06:18.560 - 00:06:21.990` | and if any errors should occur then they |
| `00:06:22.000 - 00:06:25.029` | would be masked and taken care of in |
| `00:06:25.039 - 00:06:26.309` | these |
| `00:06:26.319 - 00:06:28.950` | in this function for example if there's |
| `00:06:28.960 - 00:06:31.430` | a reed failure a checksum failure or |
| `00:06:31.440 - 00:06:34.390` | something the sector or the entire block |
| `00:06:34.400 - 00:06:37.430` | would be re re-read until we finally can |
| `00:06:37.440 - 00:06:38.710` | get the data |
| `00:06:38.720 - 00:06:41.350` | uh in the emulator of course the disk is |
| `00:06:41.360 - 00:06:44.309` | emulated with a file on the host system |
| `00:06:44.319 - 00:06:45.909` | so there's probably not really going to |
| `00:06:45.919 - 00:06:48.550` | be any errors anyway but in any case |
| `00:06:48.560 - 00:06:52.629` | this function has no error reporting |
| `00:06:52.639 - 00:06:54.790` | the function may sleep for example if we |
| `00:06:54.800 - 00:06:57.350` | want to read a buffer this function will |
| `00:06:57.360 - 00:06:59.990` | start the read operation going and then |
| `00:07:00.000 - 00:07:02.710` | it will go to sleep somewhere else there |
| `00:07:02.720 - 00:07:06.150` | is a trap handler so when the operation |
| `00:07:06.160 - 00:07:08.950` | is complete the disc will cause a trap |
| `00:07:08.960 - 00:07:11.670` | and the handler for the disc will become |
| `00:07:11.680 - 00:07:13.909` | active and among other things it will |
| `00:07:13.919 - 00:07:15.670` | wake up this function |
| `00:07:15.680 - 00:07:17.670` | at that point the process that had |
| `00:07:17.680 - 00:07:19.110` | invoked the |
| `00:07:19.120 - 00:07:21.510` | read or the right operation will be |
| `00:07:21.520 - 00:07:25.270` | reawaken and will return |
| `00:07:25.280 - 00:07:28.790` | so in almost all cases uh this function |
| `00:07:28.800 - 00:07:30.390` | will sleep |
| `00:07:30.400 - 00:07:31.990` | another thing that's important to |
| `00:07:32.000 - 00:07:34.950` | remember or to know is that |
| `00:07:34.960 - 00:07:37.909` | this function will not reorder the |
| `00:07:37.919 - 00:07:39.430` | operations |
| `00:07:39.440 - 00:07:41.909` | it will do them in exactly the order in |
| `00:07:41.919 - 00:07:44.309` | which this function was called and we'll |
| `00:07:44.319 - 00:07:45.990` | see later that that's important when it |
| `00:07:46.000 - 00:07:47.350` | comes to |
| `00:07:47.360 - 00:07:49.749` | atomic transactions but for now just |
| `00:07:49.759 - 00:07:52.230` | remember that it does the operations |
| `00:07:52.240 - 00:07:54.230` | in the order in which the function is |
| `00:07:54.240 - 00:07:55.670` | involved |
| `00:07:55.680 - 00:07:57.830` | now this buffer that's pointed to here |
| `00:07:57.840 - 00:08:00.150` | is an instance of a structure called |
| `00:08:00.160 - 00:08:01.350` | buff |
| `00:08:01.360 - 00:08:02.710` | and |
| `00:08:02.720 - 00:08:05.430` | these structures are used as a cache for |
| `00:08:05.440 - 00:08:07.909` | blocks on the disk |
| `00:08:07.919 - 00:08:09.749` | there are a fixed number of buffers that |
| `00:08:09.759 - 00:08:12.629` | are pre-allocated at kernel startup time |
| `00:08:12.639 - 00:08:14.550` | and that's controlled by this constant |
| `00:08:14.560 - 00:08:17.270` | inbound turns out we have exactly 30 |
| `00:08:17.280 - 00:08:19.189` | buffers |
| `00:08:19.199 - 00:08:21.670` | that can contain data and each one has |
| `00:08:21.680 - 00:08:23.749` | enough space for exactly one block of |
| `00:08:23.759 - 00:08:27.029` | data in our case of course it's 1024 |
| `00:08:27.039 - 00:08:28.230` | bytes |
| `00:08:28.240 - 00:08:30.869` | in addition or we have the block number |
| `00:08:30.879 - 00:08:33.509` | for which this particular block of data |
| `00:08:33.519 - 00:08:36.389` | is a cache so which block on the disk is |
| `00:08:36.399 - 00:08:38.149` | held in the buffer |
| `00:08:38.159 - 00:08:39.589` | and there's some other |
| `00:08:39.599 - 00:08:40.949` | variables some other fields that are |
| `00:08:40.959 - 00:08:42.550` | used for synchronization we'll get to |
| `00:08:42.560 - 00:08:45.110` | those in a second |
| `00:08:45.120 - 00:08:47.350` | the buffers can either be free or in use |
| `00:08:47.360 - 00:08:49.590` | so we have a free list of buffers and |
| `00:08:49.600 - 00:08:54.230` | buffers that are not free are in use |
| `00:08:54.240 - 00:08:56.310` | in this diagram i'm showing how the |
| `00:08:56.320 - 00:08:59.670` | buffers are organized they are organized |
| `00:08:59.680 - 00:09:02.389` | in a circular linked list |
| `00:09:02.399 - 00:09:04.150` | circular linked lists are also called |
| `00:09:04.160 - 00:09:07.350` | doubly linked lists so here we have |
| `00:09:07.360 - 00:09:08.389` | individual |
| `00:09:08.399 - 00:09:09.910` | buffer structures |
| `00:09:09.920 - 00:09:11.110` | and one |
| `00:09:11.120 - 00:09:13.430` | is special and that is the head of the |
| `00:09:13.440 - 00:09:14.470` | list |
| `00:09:14.480 - 00:09:16.310` | it will not contain any other data it's |
| `00:09:16.320 - 00:09:18.150` | just used for its next and previous |
| `00:09:18.160 - 00:09:19.910` | pointers so i've indicated that with an |
| `00:09:19.920 - 00:09:21.509` | x |
| `00:09:21.519 - 00:09:24.389` | then we have the buffers in the cache we |
| `00:09:24.399 - 00:09:26.630` | have 30 buffers in the cache i've only |
| `00:09:26.640 - 00:09:29.190` | shown four here for simplicity |
| `00:09:29.200 - 00:09:32.070` | these are organized from most recently |
| `00:09:32.080 - 00:09:34.949` | used to least recently used so we can |
| `00:09:34.959 - 00:09:36.710` | locate the most recently used by |
| `00:09:36.720 - 00:09:39.509` | following the head pointer sorry the |
| `00:09:39.519 - 00:09:41.590` | next pointer of the head and we can |
| `00:09:41.600 - 00:09:43.269` | locate the least recently used by |
| `00:09:43.279 - 00:09:45.350` | following the previous pointer in the |
| `00:09:45.360 - 00:09:46.870` | head |
| `00:09:46.880 - 00:09:48.790` | with a circular linked list we can |
| `00:09:48.800 - 00:09:50.949` | easily remove an element |
| `00:09:50.959 - 00:09:52.870` | for example we can remove this element |
| `00:09:52.880 - 00:09:55.670` | and move it to the front of the list if |
| `00:09:55.680 - 00:09:58.150` | it is now to be the most recently used |
| `00:09:58.160 - 00:09:59.350` | element |
| `00:09:59.360 - 00:10:01.269` | so let's take a look at the detail of |
| `00:10:01.279 - 00:10:03.110` | what's in these buffers and i'm showing |
| `00:10:03.120 - 00:10:05.190` | that here |
| `00:10:05.200 - 00:10:07.430` | we see the next pointer and the previous |
| `00:10:07.440 - 00:10:08.470` | pointer |
| `00:10:08.480 - 00:10:10.870` | here we have a reference count |
| `00:10:10.880 - 00:10:12.790` | if the reference count is zero it means |
| `00:10:12.800 - 00:10:14.790` | this buffer is not currently in use and |
| `00:10:14.800 - 00:10:16.790` | it can be recycled if you will or used |
| `00:10:16.800 - 00:10:18.550` | for something else |
| `00:10:18.560 - 00:10:21.910` | but if it's greater than 0 then we can't |
| `00:10:21.920 - 00:10:24.470` | use it for anything else |
| `00:10:24.480 - 00:10:27.590` | the block number shows which block is |
| `00:10:27.600 - 00:10:29.670` | represented in the data portion down |
| `00:10:29.680 - 00:10:31.670` | here we have space for |
| `00:10:31.680 - 00:10:34.870` | the entire block of 1024 bytes |
| `00:10:34.880 - 00:10:35.670` | and |
| `00:10:35.680 - 00:10:37.430` | block number here is the number of which |
| `00:10:37.440 - 00:10:40.790` | block is being stored here |
| `00:10:40.800 - 00:10:42.470` | the device number |
| `00:10:42.480 - 00:10:43.269` | is |
| `00:10:43.279 - 00:10:46.230` | right here in xv6 we just have a single |
| `00:10:46.240 - 00:10:48.710` | disk device so we don't really see much |
| `00:10:48.720 - 00:10:50.790` | change in that it's more or less a |
| `00:10:50.800 - 00:10:52.150` | constant of one |
| `00:10:52.160 - 00:10:54.870` | but in a more complex unix system it |
| `00:10:54.880 - 00:10:57.430` | wouldn't be the case |
| `00:10:57.440 - 00:11:00.630` | we also have a ballot field and that |
| `00:11:00.640 - 00:11:02.470` | indicates whether the |
| `00:11:02.480 - 00:11:05.910` | data field contains the data from the |
| `00:11:05.920 - 00:11:08.470` | disk or whether it contains garbage |
| `00:11:08.480 - 00:11:10.630` | whether it doesn't contain that and if |
| `00:11:10.640 - 00:11:12.069` | it doesn't contain it well of course we |
| `00:11:12.079 - 00:11:13.910` | need to read it in from the desk if we |
| `00:11:13.920 - 00:11:15.829` | want to use that data |
| `00:11:15.839 - 00:11:17.590` | we also have a field called disk which |
| `00:11:17.600 - 00:11:20.949` | is used entirely within the disk driver |
| `00:11:20.959 - 00:11:22.870` | in other words it's only used in the |
| `00:11:22.880 - 00:11:24.069` | vert i o |
| `00:11:24.079 - 00:11:26.550` | disk read write function |
| `00:11:26.560 - 00:11:27.910` | so we don't really need to think too |
| `00:11:27.920 - 00:11:29.509` | much about it but basically it tells |
| `00:11:29.519 - 00:11:30.949` | whether a disk |
| `00:11:30.959 - 00:11:33.269` | operation is currently in progress or |
| `00:11:33.279 - 00:11:34.550` | not |
| `00:11:34.560 - 00:11:36.790` | and we have a sleep lock which will |
| `00:11:36.800 - 00:11:40.710` | protect the data as well as the |
| `00:11:40.720 - 00:11:43.509` | valid bit and the disc bit |
| `00:11:43.519 - 00:11:47.430` | now here is uh where we allocate the |
| `00:11:47.440 - 00:11:50.629` | buffers so we have a structure called b |
| `00:11:50.639 - 00:11:51.670` | cache |
| `00:11:51.680 - 00:11:53.269` | with three fields |
| `00:11:53.279 - 00:11:55.829` | uh the first field is a spin lock called |
| `00:11:55.839 - 00:11:56.790` | lock |
| `00:11:56.800 - 00:11:59.509` | and the second field is called head and |
| `00:11:59.519 - 00:12:01.509` | that contains an entire buffer structure |
| `00:12:01.519 - 00:12:03.350` | which i've shown here |
| `00:12:03.360 - 00:12:07.110` | so when we refer to the head structure |
| `00:12:07.120 - 00:12:09.030` | we can just refer to it as |
| `00:12:09.040 - 00:12:11.269` | be cash dot head |
| `00:12:11.279 - 00:12:13.509` | and we can also refer to the lock as be |
| `00:12:13.519 - 00:12:15.030` | cash dot lock |
| `00:12:15.040 - 00:12:15.829` | okay |
| `00:12:15.839 - 00:12:18.310` | and then the third field is an array in |
| `00:12:18.320 - 00:12:20.870` | our case we have 30 elements in this |
| `00:12:20.880 - 00:12:23.590` | array and these are the buffers |
| `00:12:23.600 - 00:12:26.470` | we do not access this array |
| `00:12:26.480 - 00:12:28.949` | directly instead we go through the |
| `00:12:28.959 - 00:12:30.389` | pointers here |
| `00:12:30.399 - 00:12:32.470` | from the head element |
| `00:12:32.480 - 00:12:34.470` | the array is there to allocate the |
| `00:12:34.480 - 00:12:36.829` | buffers and it's only accessed during |
| `00:12:36.839 - 00:12:39.829` | initialization when we build the initial |
| `00:12:39.839 - 00:12:41.750` | circular linked list |
| `00:12:41.760 - 00:12:44.790` | so all 30 buffers are always on this |
| `00:12:44.800 - 00:12:46.790` | list although from time to time we will |
| `00:12:46.800 - 00:12:49.110` | remove one and insert it at a different |
| `00:12:49.120 - 00:12:52.949` | location |
| `00:12:52.959 - 00:12:54.470` | the spin lock |
| `00:12:54.480 - 00:12:56.069` | is protecting |
| `00:12:56.079 - 00:12:58.710` | the |
| `00:12:58.720 - 00:13:01.190` | linked list essentially okay it's |
| `00:13:01.200 - 00:13:03.990` | protecting the fields next previous ref |
| `00:13:04.000 - 00:13:07.190` | count dev and block number so every time |
| `00:13:07.200 - 00:13:09.190` | we want to |
| `00:13:09.200 - 00:13:10.870` | allocate a buffer |
| `00:13:10.880 - 00:13:13.509` | or release a buffer we will need to |
| `00:13:13.519 - 00:13:15.990` | acquire this the spin lock which we can |
| `00:13:16.000 - 00:13:18.389` | refer to as |
| `00:13:18.399 - 00:13:22.150` | be cash dot lock |
| `00:13:22.160 - 00:13:24.069` | so now let's take a look at the file |
| `00:13:24.079 - 00:13:27.030` | buff.h which contains nothing more than |
| `00:13:27.040 - 00:13:31.030` | the definition of the buff structure and |
| `00:13:31.040 - 00:13:33.269` | i'll try to line these up here i showed |
| `00:13:33.279 - 00:13:35.430` | the fields in an order that makes sense |
| `00:13:35.440 - 00:13:36.310` | to me |
| `00:13:36.320 - 00:13:38.230` | but you can see it's the same set of |
| `00:13:38.240 - 00:13:41.509` | fields so we see the next and the |
| `00:13:41.519 - 00:13:44.150` | previous fields pointing to other buff |
| `00:13:44.160 - 00:13:45.509` | structures |
| `00:13:45.519 - 00:13:46.790` | we see the |
| `00:13:46.800 - 00:13:50.389` | reference count we see the device number |
| `00:13:50.399 - 00:13:52.710` | we see the block number |
| `00:13:52.720 - 00:13:55.990` | here we see the sleep lock and the valid |
| `00:13:56.000 - 00:13:57.189` | flag |
| `00:13:57.199 - 00:14:00.629` | and the disk flag and then here we see |
| `00:14:00.639 - 00:14:01.670` | the |
| `00:14:01.680 - 00:14:03.189` | block of data |
| `00:14:03.199 - 00:14:07.110` | the 1024 byte area that will contain the |
| `00:14:07.120 - 00:14:09.350` | block |
| `00:14:09.360 - 00:14:11.910` | now let's talk about the file bufferio |
| `00:14:11.920 - 00:14:13.030` | dot c |
| `00:14:13.040 - 00:14:14.949` | and we'll start with the definition of |
| `00:14:14.959 - 00:14:17.910` | this structure called be cache and i |
| `00:14:17.920 - 00:14:20.550` | showed that right here |
| `00:14:20.560 - 00:14:22.790` | so you can see it's exactly the same |
| `00:14:22.800 - 00:14:25.110` | we have the spin lock |
| `00:14:25.120 - 00:14:28.470` | we have the array of in our case 30 |
| `00:14:28.480 - 00:14:29.509` | buffers |
| `00:14:29.519 - 00:14:32.069` | shown right here and then we have the |
| `00:14:32.079 - 00:14:32.949` | head |
| `00:14:32.959 - 00:14:35.030` | buffer structure and these are not |
| `00:14:35.040 - 00:14:37.110` | pointers but these structures are just |
| `00:14:37.120 - 00:14:39.670` | included directly |
| `00:14:39.680 - 00:14:41.269` | i want to also |
| `00:14:41.279 - 00:14:44.389` | just read the comment that occurs at the |
| `00:14:44.399 - 00:14:45.990` | top of this file because i think it's |
| `00:14:46.000 - 00:14:47.350` | useful |
| `00:14:47.360 - 00:14:49.910` | the buffer cache is a link list of bus |
| `00:14:49.920 - 00:14:51.430` | structures holding the cached copies of |
| `00:14:51.440 - 00:14:53.750` | disk block contents |
| `00:14:53.760 - 00:14:55.990` | caching the disk blocks in memory will |
| `00:14:56.000 - 00:14:57.750` | reduce the number of disk reads and also |
| `00:14:57.760 - 00:15:00.310` | provides a synchronization point for |
| `00:15:00.320 - 00:15:01.670` | blocks that are used by multiple |
| `00:15:01.680 - 00:15:02.949` | processes |
| `00:15:02.959 - 00:15:04.790` | and here's the important point the |
| `00:15:04.800 - 00:15:06.150` | interface |
| `00:15:06.160 - 00:15:07.990` | in order to get a buffer for a |
| `00:15:08.000 - 00:15:09.990` | particular disk block |
| `00:15:10.000 - 00:15:13.990` | we should call the function b read |
| `00:15:14.000 - 00:15:15.750` | and after changing the |
| `00:15:15.760 - 00:15:17.750` | data in that buffer |
| `00:15:17.760 - 00:15:20.470` | we can call b write to write it back out |
| `00:15:20.480 - 00:15:21.750` | to disk |
| `00:15:21.760 - 00:15:23.509` | and we can continue to use it but when |
| `00:15:23.519 - 00:15:25.990` | we're done using the data in that buffer |
| `00:15:26.000 - 00:15:28.150` | when we're done using the buffer itself |
| `00:15:28.160 - 00:15:31.509` | we should call this function b release |
| `00:15:31.519 - 00:15:33.749` | and then we should not use the buffer |
| `00:15:33.759 - 00:15:35.749` | after we release it |
| `00:15:35.759 - 00:15:38.470` | and only one process at a time can use a |
| `00:15:38.480 - 00:15:40.870` | buffer and so we need to remember not to |
| `00:15:40.880 - 00:15:45.189` | hold them for longer than necessary |
| `00:15:45.199 - 00:15:48.069` | okay let's uh then move on to the |
| `00:15:48.079 - 00:15:51.670` | initialization function |
| `00:15:51.680 - 00:15:53.990` | so let's look at this uh function b inet |
| `00:15:54.000 - 00:15:57.509` | which is called during kernel startup |
| `00:15:57.519 - 00:15:59.509` | it initializes the |
| `00:15:59.519 - 00:16:03.749` | buffer cache lock that's the spin lock |
| `00:16:03.759 - 00:16:06.150` | and then it creates a linked list of the |
| `00:16:06.160 - 00:16:09.350` | buffers that is it uh establishes this |
| `00:16:09.360 - 00:16:11.269` | circular linked list |
| `00:16:11.279 - 00:16:12.949` | it starts by creating an empty linked |
| `00:16:12.959 - 00:16:15.910` | list where the head points the previous |
| `00:16:15.920 - 00:16:18.470` | pointer and the next pointer both point |
| `00:16:18.480 - 00:16:20.069` | to the head itself |
| `00:16:20.079 - 00:16:22.230` | and then it goes through the array so |
| `00:16:22.240 - 00:16:23.829` | here we're going through the |
| `00:16:23.839 - 00:16:26.949` | uh buff array from the zero element all |
| `00:16:26.959 - 00:16:29.430` | the way up to the 29th element |
| `00:16:29.440 - 00:16:31.990` | one by one and for each one of them we |
| `00:16:32.000 - 00:16:35.749` | are initializing the sleep block in that |
| `00:16:35.759 - 00:16:38.389` | buffer structure okay and we're also |
| `00:16:38.399 - 00:16:42.150` | adding it to the linked list so i can |
| `00:16:42.160 - 00:16:45.590` | just show this really quickly by |
| `00:16:45.600 - 00:16:47.030` | simulating it or |
| `00:16:47.040 - 00:16:49.430` | doing the instructions one by one so |
| `00:16:49.440 - 00:16:52.870` | we've got a a linked list here that uh |
| `00:16:52.880 - 00:16:55.030` | points to some |
| `00:16:55.040 - 00:16:57.430` | here's the head and we've got some lists |
| `00:16:57.440 - 00:16:59.350` | so far we've got a new structure b that |
| `00:16:59.360 - 00:17:01.110` | we want to add so we're going to make |
| `00:17:01.120 - 00:17:03.269` | the next pointer point to the head dot |
| `00:17:03.279 - 00:17:05.189` | next head dot next is this one so we |
| `00:17:05.199 - 00:17:06.870` | make our next pointer |
| `00:17:06.880 - 00:17:07.990` | point |
| `00:17:08.000 - 00:17:08.870` | here |
| `00:17:08.880 - 00:17:09.990` | like that |
| `00:17:10.000 - 00:17:11.829` | and then the next thing we do is we make |
| `00:17:11.839 - 00:17:14.710` | our next pointer point to the head |
| `00:17:14.720 - 00:17:16.069` | sorry whoops we make our previous |
| `00:17:16.079 - 00:17:18.949` | pointer point to the head so here we are |
| `00:17:18.959 - 00:17:19.829` | doing |
| `00:17:19.839 - 00:17:20.630` | that |
| `00:17:20.640 - 00:17:21.510` | okay |
| `00:17:21.520 - 00:17:22.309` | and |
| `00:17:22.319 - 00:17:24.390` | then in the next step we make the thing |
| `00:17:24.400 - 00:17:26.470` | that the head is pointing to next |
| `00:17:26.480 - 00:17:28.549` | okay which is this element here we make |
| `00:17:28.559 - 00:17:30.789` | its previous pointer point to b |
| `00:17:30.799 - 00:17:33.190` | okay so i'll do that by rerouting this |
| `00:17:33.200 - 00:17:35.190` | pointer like that |
| `00:17:35.200 - 00:17:38.070` | and then we make the head's next pointer |
| `00:17:38.080 - 00:17:39.270` | point to b |
| `00:17:39.280 - 00:17:41.190` | so um we |
| `00:17:41.200 - 00:17:42.710` | reroute that |
| `00:17:42.720 - 00:17:43.510` | to |
| `00:17:43.520 - 00:17:46.789` | here so at that point we've added this |
| `00:17:46.799 - 00:17:49.190` | into the linked list |
| `00:17:49.200 - 00:17:51.590` | by the way there are several fields in |
| `00:17:51.600 - 00:17:53.270` | the buff structure |
| `00:17:53.280 - 00:17:57.350` | that are used but not uh initialized the |
| `00:17:57.360 - 00:17:58.950` | reference count the device number and |
| `00:17:58.960 - 00:18:02.310` | the block number it turns out are |
| `00:18:02.320 - 00:18:04.470` | initialized implicitly to zero and we |
| `00:18:04.480 - 00:18:08.710` | will rely on that initialization |
| `00:18:08.720 - 00:18:10.310` | now let's take a look at the b read |
| `00:18:10.320 - 00:18:12.510` | function this is coming out of the |
| `00:18:12.520 - 00:18:14.870` | bufferio.c file |
| `00:18:14.880 - 00:18:16.950` | this will return a locked buffer with |
| `00:18:16.960 - 00:18:19.430` | the contents of the indicated disk block |
| `00:18:19.440 - 00:18:21.350` | in that buffer |
| `00:18:21.360 - 00:18:23.750` | okay it's going to search the cache and |
| `00:18:23.760 - 00:18:26.549` | if we find that we already have a buffer |
| `00:18:26.559 - 00:18:28.549` | containing that disc block then we'll |
| `00:18:28.559 - 00:18:30.950` | return that otherwise we're going to |
| `00:18:30.960 - 00:18:33.750` | allocate a new buffer and read in the |
| `00:18:33.760 - 00:18:35.909` | data from the disk into the block before |
| `00:18:35.919 - 00:18:37.110` | returning |
| `00:18:37.120 - 00:18:39.430` | and before we return we will grab the |
| `00:18:39.440 - 00:18:41.669` | sleep lock for this buffer and we'll |
| `00:18:41.679 - 00:18:44.150` | also increment the reference count for |
| `00:18:44.160 - 00:18:45.669` | that buffer |
| `00:18:45.679 - 00:18:47.350` | a reference count of zero indicates that |
| `00:18:47.360 - 00:18:49.190` | a particular buffer is free and |
| `00:18:49.200 - 00:18:52.310` | available for reuse but if the reference |
| `00:18:52.320 - 00:18:54.070` | count is greater than zero then that |
| `00:18:54.080 - 00:18:56.470` | buffer is in use |
| `00:18:56.480 - 00:18:58.710` | this function is past the device number |
| `00:18:58.720 - 00:19:00.150` | and block number |
| `00:19:00.160 - 00:19:03.430` | that we want to get so |
| `00:19:03.440 - 00:19:05.750` | the device number in xv6 doesn't change |
| `00:19:05.760 - 00:19:07.510` | it's always one but the block number |
| `00:19:07.520 - 00:19:09.669` | tells which block on the disk we want to |
| `00:19:09.679 - 00:19:10.870` | read in |
| `00:19:10.880 - 00:19:11.909` | so |
| `00:19:11.919 - 00:19:15.270` | this function will first call b get |
| `00:19:15.280 - 00:19:17.190` | beget will search the |
| `00:19:17.200 - 00:19:19.510` | circular linked list of buffers to see |
| `00:19:19.520 - 00:19:22.150` | whether we already have a cached copy of |
| `00:19:22.160 - 00:19:24.710` | this particular block and if so it will |
| `00:19:24.720 - 00:19:26.950` | return a pointer to that buffer but if |
| `00:19:26.960 - 00:19:29.190` | we don't already have one then b get |
| `00:19:29.200 - 00:19:31.669` | will return a pointer to a newly |
| `00:19:31.679 - 00:19:33.669` | allocated buffer in other words it'll |
| `00:19:33.679 - 00:19:36.070` | find a free buffer with reference count |
| `00:19:36.080 - 00:19:39.270` | of zero and return a pointer to that |
| `00:19:39.280 - 00:19:41.110` | in either case it will increment the |
| `00:19:41.120 - 00:19:43.909` | reference count and grab the lock so now |
| `00:19:43.919 - 00:19:45.590` | we have a buffer with the reference |
| `00:19:45.600 - 00:19:48.950` | count incremented and the |
| `00:19:48.960 - 00:19:51.110` | sleep lock set |
| `00:19:51.120 - 00:19:53.830` | then we look at the valid flag |
| `00:19:53.840 - 00:19:56.070` | if we found an existing copy in the |
| `00:19:56.080 - 00:19:56.950` | cache |
| `00:19:56.960 - 00:19:58.870` | of the write block then valid will be |
| `00:19:58.880 - 00:20:01.190` | set to true but if we had to allocate a |
| `00:20:01.200 - 00:20:04.149` | new buffer then valid will be false so |
| `00:20:04.159 - 00:20:06.390` | we need to read in the value we need to |
| `00:20:06.400 - 00:20:08.710` | read in the block from the disk so here |
| `00:20:08.720 - 00:20:11.430` | we call vert i o disk read write |
| `00:20:11.440 - 00:20:13.350` | this parameter 0 indicates that we want |
| `00:20:13.360 - 00:20:15.270` | to be doing a read and we pass it a |
| `00:20:15.280 - 00:20:17.669` | pointer to b which will |
| `00:20:17.679 - 00:20:19.510` | already have the device and block number |
| `00:20:19.520 - 00:20:22.149` | filled in by the b get function |
| `00:20:22.159 - 00:20:25.270` | and then we set the valid flag to one |
| `00:20:25.280 - 00:20:26.789` | after that and return a pointer to the |
| `00:20:26.799 - 00:20:28.149` | buffer |
| `00:20:28.159 - 00:20:30.710` | okay now let's take a look at the b get |
| `00:20:30.720 - 00:20:31.830` | function |
| `00:20:31.840 - 00:20:33.430` | this is going to first look through the |
| `00:20:33.440 - 00:20:35.350` | buffer cache to see whether we already |
| `00:20:35.360 - 00:20:38.390` | have a block with the right data in it |
| `00:20:38.400 - 00:20:40.390` | and if we do then we're going to return |
| `00:20:40.400 - 00:20:42.549` | a pointer to that buffer but if we don't |
| `00:20:42.559 - 00:20:44.710` | we're going to allocate a free buffer |
| `00:20:44.720 - 00:20:47.590` | so in either case in either case we are |
| `00:20:47.600 - 00:20:49.669` | going to return a pointer to a buffer |
| `00:20:49.679 - 00:20:51.750` | which will be locked and which will have |
| `00:20:51.760 - 00:20:54.310` | its reference count incremented |
| `00:20:54.320 - 00:20:56.310` | so here we are past the device and the |
| `00:20:56.320 - 00:20:58.230` | block number to indicate which block we |
| `00:20:58.240 - 00:20:59.590` | need to read in |
| `00:20:59.600 - 00:21:01.669` | and we return a pointer to the buffer |
| `00:21:01.679 - 00:21:04.710` | that we have read that data into or |
| `00:21:04.720 - 00:21:07.830` | maybe not read it might not be valid |
| `00:21:07.840 - 00:21:10.470` | um so we acquire the lock okay we're |
| `00:21:10.480 - 00:21:12.950` | going to be accessing this circular |
| `00:21:12.960 - 00:21:14.710` | linked list |
| `00:21:14.720 - 00:21:16.870` | here we're showing the next and previous |
| `00:21:16.880 - 00:21:19.029` | pointers and the reference count and |
| `00:21:19.039 - 00:21:21.750` | these are protected by the spin lock in |
| `00:21:21.760 - 00:21:23.270` | be cash |
| `00:21:23.280 - 00:21:25.350` | we're only going to be reading the next |
| `00:21:25.360 - 00:21:27.590` | and previous pointers but when fields |
| `00:21:27.600 - 00:21:30.390` | are protected by a lock we need to grab |
| `00:21:30.400 - 00:21:32.549` | that lock and acquire it even though |
| `00:21:32.559 - 00:21:34.390` | we're only reading it |
| `00:21:34.400 - 00:21:35.909` | the reference count we will be both |
| `00:21:35.919 - 00:21:38.310` | reading and modifying |
| `00:21:38.320 - 00:21:40.630` | so here we acquire the lock and you can |
| `00:21:40.640 - 00:21:43.430` | see before we return whether we release |
| `00:21:43.440 - 00:21:45.830` | the lock whether we return here okay we |
| `00:21:45.840 - 00:21:47.909` | release the lock or if we return down |
| `00:21:47.919 - 00:21:49.990` | here we release the lock if there's an |
| `00:21:50.000 - 00:21:51.350` | error we don't really worry too much |
| `00:21:51.360 - 00:21:53.270` | about it |
| `00:21:53.280 - 00:21:54.870` | okay in the first loop what we're doing |
| `00:21:54.880 - 00:21:57.590` | is we're going through the circular list |
| `00:21:57.600 - 00:21:59.510` | uh in the forward order so you can see |
| `00:21:59.520 - 00:22:01.590` | we're following the next pointer |
| `00:22:01.600 - 00:22:03.510` | we're starting with the first element in |
| `00:22:03.520 - 00:22:04.789` | the list in other words we look at the |
| `00:22:04.799 - 00:22:07.510` | head element and follow its next pointer |
| `00:22:07.520 - 00:22:09.029` | and we keep going |
| `00:22:09.039 - 00:22:11.669` | until we wrap around and get to the head |
| `00:22:11.679 - 00:22:12.789` | again |
| `00:22:12.799 - 00:22:15.270` | and what we're doing is we're asking is |
| `00:22:15.280 - 00:22:17.830` | this buffer a match in other words does |
| `00:22:17.840 - 00:22:20.710` | its device and block number match the |
| `00:22:20.720 - 00:22:21.830` | device |
| `00:22:21.840 - 00:22:24.549` | and block number that we are searching |
| `00:22:24.559 - 00:22:25.350` | for |
| `00:22:25.360 - 00:22:28.070` | and if so we found an existing copy of |
| `00:22:28.080 - 00:22:30.390` | that buffer and so we've got the data |
| `00:22:30.400 - 00:22:32.070` | already in the cache |
| `00:22:32.080 - 00:22:33.830` | we increment the reference count and of |
| `00:22:33.840 - 00:22:36.710` | course release the lock in b cache and |
| `00:22:36.720 - 00:22:38.870` | then finally we acquire the lock that's |
| `00:22:38.880 - 00:22:41.430` | in this particular buffer and return the |
| `00:22:41.440 - 00:22:43.350` | pointer to the buffer |
| `00:22:43.360 - 00:22:45.270` | now i should point out right here that i |
| `00:22:45.280 - 00:22:47.669` | mentioned earlier that the device block |
| `00:22:47.679 - 00:22:49.909` | number and reference count fields are |
| `00:22:49.919 - 00:22:52.149` | implicitly initialized to zero and we |
| `00:22:52.159 - 00:22:53.750` | don't actually do that explicitly when |
| `00:22:53.760 - 00:22:55.350` | we initialize things at the very |
| `00:22:55.360 - 00:22:58.310` | beginning and be init and this is why |
| `00:22:58.320 - 00:22:59.510` | because we're reading these things |
| `00:22:59.520 - 00:23:01.990` | before they ever get initialized |
| `00:23:02.000 - 00:23:03.669` | so we're assuming they're zero to start |
| `00:23:03.679 - 00:23:06.149` | with and we never look for block number |
| `00:23:06.159 - 00:23:09.669` | zero on device number zero |
| `00:23:09.679 - 00:23:11.590` | okay if we search the list and we don't |
| `00:23:11.600 - 00:23:14.870` | find a match then we need to grab a |
| `00:23:14.880 - 00:23:16.710` | buffer that's currently unused so we're |
| `00:23:16.720 - 00:23:18.149` | going to look through |
| `00:23:18.159 - 00:23:19.669` | the list for |
| `00:23:19.679 - 00:23:21.909` | a buffer with reference count zero |
| `00:23:21.919 - 00:23:23.350` | remember that a reference count of zero |
| `00:23:23.360 - 00:23:25.029` | means that it's free |
| `00:23:25.039 - 00:23:26.950` | however we are going to |
| `00:23:26.960 - 00:23:29.750` | use the uh list in the reverse order |
| `00:23:29.760 - 00:23:31.909` | we're going to search using the previous |
| `00:23:31.919 - 00:23:33.430` | field so we're searching it in the |
| `00:23:33.440 - 00:23:35.270` | reverse order we're starting with the |
| `00:23:35.280 - 00:23:37.750` | head's previous pointer and then we keep |
| `00:23:37.760 - 00:23:40.310` | going until we wrap around and reach the |
| `00:23:40.320 - 00:23:41.990` | head again |
| `00:23:42.000 - 00:23:44.070` | okay and if we find one with the |
| `00:23:44.080 - 00:23:45.830` | reference count of zero then we're going |
| `00:23:45.840 - 00:23:48.710` | to grab it we're going to |
| `00:23:48.720 - 00:23:50.630` | change its device and block number to |
| `00:23:50.640 - 00:23:51.269` | the |
| `00:23:51.279 - 00:23:52.390` | device and block number that we're |
| `00:23:52.400 - 00:23:53.750` | searching for |
| `00:23:53.760 - 00:23:54.549` | and |
| `00:23:54.559 - 00:23:56.149` | since it may contain data from some |
| `00:23:56.159 - 00:23:56.950` | other |
| `00:23:56.960 - 00:23:59.510` | disk block we will set it's valid to |
| `00:23:59.520 - 00:24:00.549` | zero |
| `00:24:00.559 - 00:24:02.230` | and we will |
| `00:24:02.240 - 00:24:04.549` | essentially initialize uh its reference |
| `00:24:04.559 - 00:24:06.549` | count to one which is nothing more than |
| `00:24:06.559 - 00:24:08.549` | actually incrementing it from zero |
| `00:24:08.559 - 00:24:10.710` | and finally we release the |
| `00:24:10.720 - 00:24:11.990` | lock on the |
| `00:24:12.000 - 00:24:13.110` | b cache |
| `00:24:13.120 - 00:24:16.070` | and then acquire the sleep lock for the |
| `00:24:16.080 - 00:24:18.470` | buffer itself before returning |
| `00:24:18.480 - 00:24:22.149` | if uh we can't find any free buffer |
| `00:24:22.159 - 00:24:23.909` | we've got a problem we have |
| `00:24:23.919 - 00:24:25.510` | pre-allocated enough buffers so this |
| `00:24:25.520 - 00:24:27.669` | should never happen but if it were to uh |
| `00:24:27.679 - 00:24:30.070` | we would have an error message here |
| `00:24:30.080 - 00:24:31.830` | now um i just want to point out when |
| `00:24:31.840 - 00:24:34.149` | you're maintaining a uh least recently |
| `00:24:34.159 - 00:24:36.310` | used list uh we can do it sort of a |
| `00:24:36.320 - 00:24:38.070` | couple of ways every time we use |
| `00:24:38.080 - 00:24:39.350` | something we can move it to the front of |
| `00:24:39.360 - 00:24:40.549` | the list |
| `00:24:40.559 - 00:24:42.710` | or every time we're done using it we |
| `00:24:42.720 - 00:24:45.190` | could move it to the rear of the list |
| `00:24:45.200 - 00:24:47.830` | and actually six uses the latter |
| `00:24:47.840 - 00:24:50.149` | approach every time we're done with a |
| `00:24:50.159 - 00:24:52.630` | buffer that is every time we release the |
| `00:24:52.640 - 00:24:55.190` | buffer we will uh remove it from |
| `00:24:55.200 - 00:24:57.510` | wherever it is on the list and we will |
| `00:24:57.520 - 00:24:58.630` | move it to |
| `00:24:58.640 - 00:25:01.269` | the tail of the list that's why we don't |
| `00:25:01.279 - 00:25:02.470` | see any |
| `00:25:02.480 - 00:25:04.470` | change in the position of the buffer on |
| `00:25:04.480 - 00:25:06.710` | the list with this but later we'll see |
| `00:25:06.720 - 00:25:09.269` | in the b release function that we move |
| `00:25:09.279 - 00:25:12.549` | it to the tail of the list |
| `00:25:12.559 - 00:25:13.990` | now let's take a look at the b write |
| `00:25:14.000 - 00:25:16.149` | function it's passed a pointer to a |
| `00:25:16.159 - 00:25:18.390` | buffer and it will write the contents of |
| `00:25:18.400 - 00:25:21.029` | the block in that buffer back to its |
| `00:25:21.039 - 00:25:23.990` | location on the disk |
| `00:25:24.000 - 00:25:26.710` | every buffer that we write to the disk |
| `00:25:26.720 - 00:25:29.430` | must first be acquired by a call to the |
| `00:25:29.440 - 00:25:31.590` | b read function |
| `00:25:31.600 - 00:25:34.470` | so at this point this buffer will have |
| `00:25:34.480 - 00:25:37.190` | its device and block number fields set |
| `00:25:37.200 - 00:25:40.830` | as well as the 1024 bytes of data in the |
| `00:25:40.840 - 00:25:43.430` | block and furthermore the b read |
| `00:25:43.440 - 00:25:46.230` | function will always return a buffer |
| `00:25:46.240 - 00:25:47.909` | that is locked so |
| `00:25:47.919 - 00:25:48.950` | we check |
| `00:25:48.960 - 00:25:50.710` | to make sure that we are actually |
| `00:25:50.720 - 00:25:53.430` | holding the lock on this buffer and if |
| `00:25:53.440 - 00:25:56.230` | not print an error message |
| `00:25:56.240 - 00:25:59.669` | then we call the vert io disk read write |
| `00:25:59.679 - 00:26:02.470` | function with an argument of 1 here to |
| `00:26:02.480 - 00:26:04.950` | perform the write operation this will |
| `00:26:04.960 - 00:26:06.710` | invoke the disk driver function that |
| `00:26:06.720 - 00:26:09.669` | will write the block in that buffer back |
| `00:26:09.679 - 00:26:12.549` | to disk and it won't return until after |
| `00:26:12.559 - 00:26:14.549` | that disk write is complete at which |
| `00:26:14.559 - 00:26:17.190` | time b write returns |
| `00:26:17.200 - 00:26:19.830` | now the way we use buffers is we first |
| `00:26:19.840 - 00:26:23.029` | always call b read and that will get the |
| `00:26:23.039 - 00:26:24.870` | data from the disk |
| `00:26:24.880 - 00:26:26.230` | into the buffer |
| `00:26:26.240 - 00:26:30.149` | so at this point uh the buffer will be |
| `00:26:30.159 - 00:26:31.830` | locked and |
| `00:26:31.840 - 00:26:33.909` | its device and block number will be set |
| `00:26:33.919 - 00:26:35.669` | and it will contain data |
| `00:26:35.679 - 00:26:37.669` | then we can modify some or all of the |
| `00:26:37.679 - 00:26:40.870` | bytes in that block and then invoke b |
| `00:26:40.880 - 00:26:43.269` | write to write the modified block back |
| `00:26:43.279 - 00:26:44.630` | to disk |
| `00:26:44.640 - 00:26:47.990` | we can then modify some more bytes |
| `00:26:48.000 - 00:26:51.190` | in that buffer and call b write another |
| `00:26:51.200 - 00:26:53.750` | time and we can keep doing this because |
| `00:26:53.760 - 00:26:55.669` | as you can see the b write function |
| `00:26:55.679 - 00:26:59.830` | doesn't release the buffer itself |
| `00:26:59.840 - 00:27:00.630` | so |
| `00:27:00.640 - 00:27:03.029` | when we get ready to release the |
| `00:27:03.039 - 00:27:06.789` | buffer we call this function b release |
| `00:27:06.799 - 00:27:08.470` | and |
| `00:27:08.480 - 00:27:09.909` | since that buffer |
| `00:27:09.919 - 00:27:12.549` | will have been acquired with a call to b |
| `00:27:12.559 - 00:27:14.789` | read we had better be holding the lock |
| `00:27:14.799 - 00:27:18.070` | on it okay so we first check to make |
| `00:27:18.080 - 00:27:20.389` | sure we are in fact holding the sleep |
| `00:27:20.399 - 00:27:22.310` | lock on that buffer |
| `00:27:22.320 - 00:27:25.430` | and then we release that lock |
| `00:27:25.440 - 00:27:27.269` | then we decrement the reference count |
| `00:27:27.279 - 00:27:30.070` | and check to see if it's gone to zero |
| `00:27:30.080 - 00:27:31.590` | now every time we |
| `00:27:31.600 - 00:27:33.430` | access the reference count |
| `00:27:33.440 - 00:27:36.149` | or the previous or next field as we will |
| `00:27:36.159 - 00:27:38.310` | do here we need to be holding the b |
| `00:27:38.320 - 00:27:39.510` | cache lock |
| `00:27:39.520 - 00:27:42.230` | remember that the b cache lock in my |
| `00:27:42.240 - 00:27:43.750` | picture here |
| `00:27:43.760 - 00:27:45.990` | protects these fields |
| `00:27:46.000 - 00:27:48.870` | so we need to grab that lock first and |
| `00:27:48.880 - 00:27:51.190` | we do that |
| `00:27:51.200 - 00:27:54.149` | at this point here and then subsequently |
| `00:27:54.159 - 00:27:58.470` | we will release the b cache lock here |
| `00:27:58.480 - 00:28:00.230` | at this point after getting the lock we |
| `00:28:00.240 - 00:28:02.389` | decrement the reference count if it's |
| `00:28:02.399 - 00:28:05.669` | now zero then this buffer is free it's |
| `00:28:05.679 - 00:28:08.549` | not in use by anybody it still contains |
| `00:28:08.559 - 00:28:11.669` | the data from that particular block and |
| `00:28:11.679 - 00:28:13.590` | the device and block number fields are |
| `00:28:13.600 - 00:28:16.389` | still accurate along with the valid flag |
| `00:28:16.399 - 00:28:18.230` | so it might be that a future call to be |
| `00:28:18.240 - 00:28:20.389` | read will be able to reuse it |
| `00:28:20.399 - 00:28:22.070` | but on the other hand a future call to |
| `00:28:22.080 - 00:28:25.110` | be read might actually need |
| `00:28:25.120 - 00:28:27.750` | this buffer for a different block and |
| `00:28:27.760 - 00:28:28.950` | will |
| `00:28:28.960 - 00:28:31.269` | not use the existing data |
| `00:28:31.279 - 00:28:33.430` | so if it's gone to zero then it's no |
| `00:28:33.440 - 00:28:36.070` | longer in use and it's now the least |
| `00:28:36.080 - 00:28:37.590` | recently used |
| `00:28:37.600 - 00:28:38.789` | buffer so |
| `00:28:38.799 - 00:28:40.549` | with these statements here |
| `00:28:40.559 - 00:28:43.350` | we modify the next and previous fields |
| `00:28:43.360 - 00:28:45.990` | of this |
| `00:28:46.000 - 00:28:47.590` | buffer as well as the |
| `00:28:47.600 - 00:28:51.029` | head buffer and i won't go over those in |
| `00:28:51.039 - 00:28:52.789` | detail but basically what we're doing is |
| `00:28:52.799 - 00:28:54.950` | we're taking the buffer de-linking it |
| `00:28:54.960 - 00:28:57.350` | from where it is and then moving it to |
| `00:28:57.360 - 00:29:00.870` | the end that is the least recently used |
| `00:29:00.880 - 00:29:04.950` | end of this circular linked list |
| `00:29:04.960 - 00:29:07.750` | i just want to comment here that the the |
| `00:29:07.760 - 00:29:10.310` | uh comment that you see here moved to |
| `00:29:10.320 - 00:29:12.310` | the head of the most recently used list |
| `00:29:12.320 - 00:29:14.070` | i feel is completely wrong and it should |
| `00:29:14.080 - 00:29:18.789` | say move it to the tail of that list |
| `00:29:18.799 - 00:29:21.110` | so at this point the buffer is released |
| `00:29:21.120 - 00:29:23.830` | and b release returns |
| `00:29:23.840 - 00:29:25.830` | now there are two other functions that |
| `00:29:25.840 - 00:29:27.350` | are of interest that we can cover right |
| `00:29:27.360 - 00:29:28.950` | now |
| `00:29:28.960 - 00:29:29.830` | the |
| `00:29:29.840 - 00:29:32.230` | pin and unpin functions |
| `00:29:32.240 - 00:29:34.710` | which will pin a buffer or unpin the |
| `00:29:34.720 - 00:29:37.110` | buffer by pinning it all we're doing is |
| `00:29:37.120 - 00:29:38.950` | we're incrementing the reference count |
| `00:29:38.960 - 00:29:40.710` | and unpinning it is decrementing the |
| `00:29:40.720 - 00:29:42.070` | reference count |
| `00:29:42.080 - 00:29:43.590` | whenever we have a buffer that we want |
| `00:29:43.600 - 00:29:45.750` | to make sure doesn't get released |
| `00:29:45.760 - 00:29:49.029` | prematurely we can call this b |
| `00:29:49.039 - 00:29:51.269` | pin function to increment the reference |
| `00:29:51.279 - 00:29:53.990` | count and then when we're done with that |
| `00:29:54.000 - 00:29:56.310` | buffer uh and we want to allow it to be |
| `00:29:56.320 - 00:29:59.669` | released uh by others then we can |
| `00:29:59.679 - 00:30:01.750` | decrement the reference count |
| `00:30:01.760 - 00:30:03.669` | in order to modify the reference count |
| `00:30:03.679 - 00:30:05.909` | we must be holding the b cache lock so |
| `00:30:05.919 - 00:30:07.590` | you can see we acquire and release it |
| `00:30:07.600 - 00:30:10.630` | here and we acquire and release it here |
| `00:30:10.640 - 00:30:12.470` | as well |
| `00:30:12.480 - 00:30:14.470` | okay that's it for the |
| `00:30:14.480 - 00:30:15.990` | buffer |
| `00:30:16.000 - 00:30:17.510` | functions and |
| `00:30:17.520 - 00:30:21.399` | i will see you in the next video |
