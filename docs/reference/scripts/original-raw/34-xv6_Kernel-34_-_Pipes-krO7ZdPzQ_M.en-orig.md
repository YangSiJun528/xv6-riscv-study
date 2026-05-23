# xv6 Kernel-34: Pipes

| Field | Value |
| --- | --- |
| Video ID | `krO7ZdPzQ_M` |
| URL | <https://www.youtube.com/watch?v=krO7ZdPzQ_M> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.079 - 00:00:03.530` | welcome to another video in a series on |
| `00:00:03.540 - 00:00:06.590` | the xv6 operating system kernel in this |
| `00:00:06.600 - 00:00:08.690` | video I'll be talking about pipes and |
| `00:00:08.700 - 00:00:10.610` | I'll do a Code walkthrough of the file |
| `00:00:10.620 - 00:00:13.009` | pipe.c |
| `00:00:13.019 - 00:00:15.589` | remember that in Unix a pipe is a little |
| `00:00:15.599 - 00:00:18.710` | bit like a file you can write data to it |
| `00:00:18.720 - 00:00:21.410` | and you can read data from it however a |
| `00:00:21.420 - 00:00:23.929` | file is kept on disk while a pipe is |
| `00:00:23.939 - 00:00:25.849` | entirely within memory |
| `00:00:25.859 - 00:00:27.890` | so here we have a process let's call it |
| `00:00:27.900 - 00:00:30.650` | w writing data to the pipe and some |
| `00:00:30.660 - 00:00:33.590` | other process R can be reading data from |
| `00:00:33.600 - 00:00:37.190` | that pipe the data is kept in some |
| `00:00:37.200 - 00:00:39.709` | buffer inside the Kernel's memory and |
| `00:00:39.719 - 00:00:42.350` | that buffer has a limited size so if |
| `00:00:42.360 - 00:00:45.350` | process W writes too many bytes the |
| `00:00:45.360 - 00:00:47.990` | kernel will need to suspend it until the |
| `00:00:48.000 - 00:00:51.290` | buffer is no longer full and likewise if |
| `00:00:51.300 - 00:00:53.990` | process R tries to read bytes but there |
| `00:00:54.000 - 00:00:56.090` | are no bytes yet sitting in the buffer |
| `00:00:56.100 - 00:00:59.029` | then the kernel will need to suspend |
| `00:00:59.039 - 00:01:02.029` | process R until process W writes the |
| `00:01:02.039 - 00:01:05.690` | bytes into the pipe |
| `00:01:05.700 - 00:01:08.630` | for every open pipe the kernel will |
| `00:01:08.640 - 00:01:11.450` | allocate and use exactly one structure |
| `00:01:11.460 - 00:01:15.109` | of type pipe which I'm showing here |
| `00:01:15.119 - 00:01:17.749` | so this structure has a spin lock and |
| `00:01:17.759 - 00:01:19.190` | that spin lock protects all the |
| `00:01:19.200 - 00:01:21.289` | remaining fields in the structure |
| `00:01:21.299 - 00:01:25.550` | it has a buffer and that buffer is 512 |
| `00:01:25.560 - 00:01:27.950` | bytes in our case the actual size is |
| `00:01:27.960 - 00:01:29.510` | determined by a constant which is |
| `00:01:29.520 - 00:01:32.330` | defined to be 512. |
| `00:01:32.340 - 00:01:36.590` | and it has two indexes read and write |
| `00:01:36.600 - 00:01:39.230` | which tell where in the buffer we are |
| `00:01:39.240 - 00:01:42.230` | and a couple of Boolean Flags called |
| `00:01:42.240 - 00:01:44.870` | read open and right open which I will |
| `00:01:44.880 - 00:01:47.330` | discuss a bit later |
| `00:01:47.340 - 00:01:51.170` | Now with uh inodes the kernel maintains |
| `00:01:51.180 - 00:01:55.490` | a cache of inodes some of which are free |
| `00:01:55.500 - 00:01:58.190` | and unused and whenever we want to |
| `00:01:58.200 - 00:02:01.130` | allocate a new inode we search through |
| `00:02:01.140 - 00:02:04.429` | that array and find an unused inode and |
| `00:02:04.439 - 00:02:07.069` | then we allocate it and use that one |
| `00:02:07.079 - 00:02:09.469` | with pipes things are done a little bit |
| `00:02:09.479 - 00:02:11.690` | differently instead we don't have a |
| `00:02:11.700 - 00:02:14.150` | cache or an array of unused pipe |
| `00:02:14.160 - 00:02:15.350` | structures |
| `00:02:15.360 - 00:02:18.710` | and whenever we need a new pipe |
| `00:02:18.720 - 00:02:21.229` | structure the kernel will call K Alec |
| `00:02:21.239 - 00:02:23.869` | and allocate an entire page and then |
| `00:02:23.879 - 00:02:25.250` | when it's done with that structure it |
| `00:02:25.260 - 00:02:27.650` | will return that page to the free pool |
| `00:02:27.660 - 00:02:31.369` | remember that our pages in xv6 are 4K |
| `00:02:31.379 - 00:02:35.030` | bytes in length so the structure itself |
| `00:02:35.040 - 00:02:37.369` | will occupy just a little bit more than |
| `00:02:37.379 - 00:02:39.410` | half a k |
| `00:02:39.420 - 00:02:41.449` | the remaining through almost three and a |
| `00:02:41.459 - 00:02:45.170` | half K of the page will simply be unused |
| `00:02:45.180 - 00:02:46.970` | and wasted |
| `00:02:46.980 - 00:02:50.270` | for a production operating system you |
| `00:02:50.280 - 00:02:51.589` | might want to think about a slightly |
| `00:02:51.599 - 00:02:54.530` | different organization particularly if |
| `00:02:54.540 - 00:02:57.589` | you think that we are going to have a |
| `00:02:57.599 - 00:03:01.130` | lot of pipes but for xv6 we're not going |
| `00:03:01.140 - 00:03:03.470` | to have so many pipes and this is quite |
| `00:03:03.480 - 00:03:05.030` | practical |
| `00:03:05.040 - 00:03:08.869` | now next I want to talk about the code |
| `00:03:08.879 - 00:03:12.050` | here and show that the code corresponds |
| `00:03:12.060 - 00:03:15.050` | to this exactly here we have the spin |
| `00:03:15.060 - 00:03:16.850` | lock here's the definition for the |
| `00:03:16.860 - 00:03:20.089` | structure pipe coming from file pipe.c |
| `00:03:20.099 - 00:03:23.330` | and we have the spin lock we have the |
| `00:03:23.340 - 00:03:27.550` | buffer of size pipe size we have the two |
| `00:03:27.560 - 00:03:30.410` | indexes and we have the two Boolean |
| `00:03:30.420 - 00:03:31.910` | Flags here |
| `00:03:31.920 - 00:03:34.130` | okay |
| `00:03:34.140 - 00:03:37.970` | how are these indexes used well the in |
| `00:03:37.980 - 00:03:40.910` | read tells where in the buffer the next |
| `00:03:40.920 - 00:03:44.809` | byte to be fetched for the reader is and |
| `00:03:44.819 - 00:03:46.970` | the inrite index tells where in the |
| `00:03:46.980 - 00:03:50.390` | buffer we should put the next byte that |
| `00:03:50.400 - 00:03:52.250` | is written to the pipe |
| `00:03:52.260 - 00:03:54.710` | and so here in red I'm showing that this |
| `00:03:54.720 - 00:03:58.970` | buffer contains uh several bytes and we |
| `00:03:58.980 - 00:04:02.089` | will add to the buffer at this point and |
| `00:04:02.099 - 00:04:04.369` | we will retrieve from the buffer at this |
| `00:04:04.379 - 00:04:07.070` | point advancing the pointers |
| `00:04:07.080 - 00:04:10.970` | buffer is a circular buffer so before we |
| `00:04:10.980 - 00:04:13.729` | access the buffer we are going to take |
| `00:04:13.739 - 00:04:16.969` | the value modulo 512 so we have a |
| `00:04:16.979 - 00:04:18.949` | wraparound situation here's another |
| `00:04:18.959 - 00:04:21.050` | situation where we have one two three |
| `00:04:21.060 - 00:04:24.230` | four five six seven eight nine bytes in |
| `00:04:24.240 - 00:04:28.310` | the buffer and they wrap around here you |
| `00:04:28.320 - 00:04:31.249` | can think of the buffer as an infinite |
| `00:04:31.259 - 00:04:35.030` | sequence of bytes so in read and in |
| `00:04:35.040 - 00:04:38.570` | right can be Advanced well Beyond 512. |
| `00:04:38.580 - 00:04:40.790` | in other words we can write thousands |
| `00:04:40.800 - 00:04:42.590` | and thousands of bytes into the buffer |
| `00:04:42.600 - 00:04:44.930` | as long as we keep up with the reading |
| `00:04:44.940 - 00:04:48.710` | and never have more than 512 bytes in |
| `00:04:48.720 - 00:04:51.350` | the buffer so these two situations are |
| `00:04:51.360 - 00:04:56.450` | equivalent views of the same situation |
| `00:04:56.460 - 00:04:59.749` | an empty buffer situation will look like |
| `00:04:59.759 - 00:05:03.170` | this whenever the read index catches up |
| `00:05:03.180 - 00:05:07.189` | to the right index we have an empty |
| `00:05:07.199 - 00:05:10.010` | buffer okay the next character or the |
| `00:05:10.020 - 00:05:11.270` | next byte that we're going to write into |
| `00:05:11.280 - 00:05:12.730` | the buffer will go into this position |
| `00:05:12.740 - 00:05:15.650` | and in retails where to read it and we |
| `00:05:15.660 - 00:05:18.350` | haven't written that byte yet |
| `00:05:18.360 - 00:05:21.110` | when the buffer is full we have a full |
| `00:05:21.120 - 00:05:24.050` | 512 bytes in the buffer so the right |
| `00:05:24.060 - 00:05:27.110` | pointer will exceed the read index by |
| `00:05:27.120 - 00:05:31.070` | exactly 512. and we can think of it this |
| `00:05:31.080 - 00:05:34.430` | way or we can think of it this way |
| `00:05:34.440 - 00:05:38.090` | now in the past I wrote some code like |
| `00:05:38.100 - 00:05:40.610` | this and I stored I performed the module |
| `00:05:40.620 - 00:05:43.189` | operator before storing the indexes so |
| `00:05:43.199 - 00:05:45.230` | these indexes would only range from 0 to |
| `00:05:45.240 - 00:05:49.129` | 511 and uh that's really not a very good |
| `00:05:49.139 - 00:05:51.650` | way to do things because it makes the |
| `00:05:51.660 - 00:05:54.650` | empty situation uh identical or appear |
| `00:05:54.660 - 00:05:57.529` | to be identical to the full situation so |
| `00:05:57.539 - 00:06:00.070` | it would require some other |
| `00:06:00.080 - 00:06:04.790` | data when reading the xb6 code I realize |
| `00:06:04.800 - 00:06:06.350` | that this is really a much better way of |
| `00:06:06.360 - 00:06:08.990` | doing things so we allow in read and |
| `00:06:09.000 - 00:06:12.469` | inrite to exceed 512 perhaps by quite a |
| `00:06:12.479 - 00:06:14.510` | lot and we only take the modular |
| `00:06:14.520 - 00:06:17.150` | operator before accessing the data in |
| `00:06:17.160 - 00:06:19.430` | the buffer and this is a superior way of |
| `00:06:19.440 - 00:06:22.550` | doing things that I learned by reading |
| `00:06:22.560 - 00:06:25.189` | the xv6 code and that points out that |
| `00:06:25.199 - 00:06:27.710` | you really can learn new tricks by |
| `00:06:27.720 - 00:06:29.930` | looking at the way someone else does |
| `00:06:29.940 - 00:06:33.290` | something that is by reading other code |
| `00:06:33.300 - 00:06:35.510` | okay next let's get into talking about |
| `00:06:35.520 - 00:06:40.730` | how this pipe structure is used |
| `00:06:40.740 - 00:06:42.710` | we'll Begin by assuming that we've got a |
| `00:06:42.720 - 00:06:44.570` | process called p |
| `00:06:44.580 - 00:06:47.210` | every process is represented by a proc |
| `00:06:47.220 - 00:06:49.610` | structure and this object right here is |
| `00:06:49.620 - 00:06:52.730` | used to represent process p |
| `00:06:52.740 - 00:06:55.189` | a process can have a number of files |
| `00:06:55.199 - 00:06:57.650` | open at any one time |
| `00:06:57.660 - 00:07:00.409` | the user code refers to the individual |
| `00:07:00.419 - 00:07:03.050` | files using a file descriptor the file |
| `00:07:03.060 - 00:07:05.809` | descriptor is a small number 0 1 2 and |
| `00:07:05.819 - 00:07:08.330` | so on and it's used as an index into |
| `00:07:08.340 - 00:07:11.390` | this array by the kernel So within the |
| `00:07:11.400 - 00:07:14.050` | proc structure we've got this o file |
| `00:07:14.060 - 00:07:18.890` | array and it contains pointers to file |
| `00:07:18.900 - 00:07:22.189` | objects so for every open file there is |
| `00:07:22.199 - 00:07:23.809` | a pointer in this array |
| `00:07:23.819 - 00:07:27.170` | and it points to this file object and of |
| `00:07:27.180 - 00:07:29.390` | course the user process doesn't get to |
| `00:07:29.400 - 00:07:31.969` | use pointers in the kernel space instead |
| `00:07:31.979 - 00:07:34.370` | it passes in a file descriptor so for |
| `00:07:34.380 - 00:07:36.469` | example if it wants to read from file |
| `00:07:36.479 - 00:07:38.210` | number two it passes in a two and the |
| `00:07:38.220 - 00:07:39.710` | kernel will then follow it to this file |
| `00:07:39.720 - 00:07:42.170` | structure |
| `00:07:42.180 - 00:07:44.330` | here it looks like we've got all the |
| `00:07:44.340 - 00:07:46.969` | file descriptors in use but if some of |
| `00:07:46.979 - 00:07:48.650` | the files are closed and these entries |
| `00:07:48.660 - 00:07:50.629` | are unused then there would be a null |
| `00:07:50.639 - 00:07:53.869` | stored in that particular intrigue |
| `00:07:53.879 - 00:07:56.749` | the file object starts with a type field |
| `00:07:56.759 - 00:07:59.029` | and I'm going to be talking about pipes |
| `00:07:59.039 - 00:08:01.969` | so the type for this example is pipe but |
| `00:08:01.979 - 00:08:04.610` | in most cases the file structure is |
| `00:08:04.620 - 00:08:07.129` | going to point to an inode so in any |
| `00:08:07.139 - 00:08:09.290` | case the file structures contain a |
| `00:08:09.300 - 00:08:11.390` | pointer and that pointer will point to |
| `00:08:11.400 - 00:08:13.249` | some object it could be a pipe or it |
| `00:08:13.259 - 00:08:15.710` | could be an inode normally it would be |
| `00:08:15.720 - 00:08:19.010` | an inode for a regular file or directory |
| `00:08:19.020 - 00:08:21.170` | there are also a couple of flags the |
| `00:08:21.180 - 00:08:22.670` | readable flag and the writable flag |
| `00:08:22.680 - 00:08:24.890` | within the file structure to tell |
| `00:08:24.900 - 00:08:28.670` | whether the file can be read or written |
| `00:08:28.680 - 00:08:31.070` | or perhaps both |
| `00:08:31.080 - 00:08:34.969` | in the case of a pipe we have two file |
| `00:08:34.979 - 00:08:37.430` | structures okay we have one for the read |
| `00:08:37.440 - 00:08:40.610` | end and one for the right end and so the |
| `00:08:40.620 - 00:08:43.310` | file structure for the read end well it |
| `00:08:43.320 - 00:08:45.889` | would have a type of FDA pipe and the |
| `00:08:45.899 - 00:08:47.690` | readable flag would be true but it would |
| `00:08:47.700 - 00:08:49.910` | not be writable and for the right end of |
| `00:08:49.920 - 00:08:52.310` | the file of the pipe we would have a |
| `00:08:52.320 - 00:08:55.070` | file structure again with type FD pipe |
| `00:08:55.080 - 00:08:57.710` | and it would be marked writable but not |
| `00:08:57.720 - 00:08:59.329` | readable |
| `00:08:59.339 - 00:09:02.090` | and so we could have two pointers or we |
| `00:09:02.100 - 00:09:03.650` | will have two pointers pointing to the |
| `00:09:03.660 - 00:09:06.410` | pipe and two file structures one for |
| `00:09:06.420 - 00:09:08.690` | each end of the pipe |
| `00:09:08.700 - 00:09:10.850` | now these file structures contain a |
| `00:09:10.860 - 00:09:12.290` | reference count too I didn't show it |
| `00:09:12.300 - 00:09:15.350` | here but the file structure can be |
| `00:09:15.360 - 00:09:16.610` | pointed to by a number of different |
| `00:09:16.620 - 00:09:19.490` | processes right this one particular file |
| `00:09:19.500 - 00:09:21.590` | could be opened by lots of different |
| `00:09:21.600 - 00:09:24.230` | processes and so we have a number of |
| `00:09:24.240 - 00:09:26.210` | different pointers here and the |
| `00:09:26.220 - 00:09:28.190` | reference count keeps track of those and |
| `00:09:28.200 - 00:09:30.050` | when it goes to zero we can reclaim the |
| `00:09:30.060 - 00:09:32.870` | space used by the file structure and add |
| `00:09:32.880 - 00:09:35.449` | it back to the free pool |
| `00:09:35.459 - 00:09:37.490` | the pipe structures on the other hand |
| `00:09:37.500 - 00:09:40.190` | will have exactly two pointers pointing |
| `00:09:40.200 - 00:09:43.070` | to them so in the pipe structure we have |
| `00:09:43.080 - 00:09:45.590` | these two fields that I showed earlier |
| `00:09:45.600 - 00:09:48.430` | the read open and the right open flag |
| `00:09:48.440 - 00:09:50.150` | and so |
| `00:09:50.160 - 00:09:52.970` | the read open slide will will be true if |
| `00:09:52.980 - 00:09:55.070` | and only if there is a file object |
| `00:09:55.080 - 00:09:57.290` | pointing to this pipe structure and |
| `00:09:57.300 - 00:09:59.990` | likewise the right open flag will be |
| `00:10:00.000 - 00:10:02.509` | true if and only if there's a file for |
| `00:10:02.519 - 00:10:04.910` | the Right End pointing to this pipe |
| `00:10:04.920 - 00:10:07.910` | structure and when both the read open |
| `00:10:07.920 - 00:10:10.730` | and the right open are false we can say |
| `00:10:10.740 - 00:10:13.009` | there's no pointer pointing to this pipe |
| `00:10:13.019 - 00:10:16.069` | object and then it can be returned in |
| `00:10:16.079 - 00:10:18.590` | that case we would go ahead and free the |
| `00:10:18.600 - 00:10:20.990` | entire page that it sits on by calling |
| `00:10:21.000 - 00:10:23.750` | the K free function |
| `00:10:23.760 - 00:10:26.990` | okay so what I want to do is assume that |
| `00:10:27.000 - 00:10:29.750` | this process P has called the pipe |
| `00:10:29.760 - 00:10:32.870` | system call to create the pipe and to |
| `00:10:32.880 - 00:10:35.150` | allocate two file objects and set up |
| `00:10:35.160 - 00:10:38.090` | these pointers so after the pipe system |
| `00:10:38.100 - 00:10:40.910` | called This is the situation we have |
| `00:10:40.920 - 00:10:43.430` | the the system call will find some empty |
| `00:10:43.440 - 00:10:46.730` | slots in the file descriptor array and |
| `00:10:46.740 - 00:10:48.769` | it will use those and it will return |
| `00:10:48.779 - 00:10:52.310` | those numbers in this case 2 and 4 to |
| `00:10:52.320 - 00:10:54.110` | the user mode process so it can refer to |
| `00:10:54.120 - 00:10:57.050` | either the read-in or the right end |
| `00:10:57.060 - 00:10:59.690` | now let's assume that this process Forks |
| `00:10:59.700 - 00:11:01.130` | a couple of children |
| `00:11:01.140 - 00:11:04.490` | one child will be a reader process and |
| `00:11:04.500 - 00:11:07.310` | the other child will be a writer process |
| `00:11:07.320 - 00:11:11.509` | so whenever we Fork a process we make a |
| `00:11:11.519 - 00:11:14.030` | full copy of this array |
| `00:11:14.040 - 00:11:17.930` | okay so we copy all of the open files so |
| `00:11:17.940 - 00:11:19.970` | if a file was open in the parent it will |
| `00:11:19.980 - 00:11:22.970` | be open in the child and so initially |
| `00:11:22.980 - 00:11:27.350` | both pointer two and pointer 4 here will |
| `00:11:27.360 - 00:11:29.210` | be set to point to these two file |
| `00:11:29.220 - 00:11:31.430` | objects but we're going to assume that |
| `00:11:31.440 - 00:11:34.069` | the reader process immediately closes |
| `00:11:34.079 - 00:11:36.310` | the right end so |
| `00:11:36.320 - 00:11:39.110` | we decrement the count to this the |
| `00:11:39.120 - 00:11:41.090` | reference count for this object and set |
| `00:11:41.100 - 00:11:43.130` | this pointer to zero because that |
| `00:11:43.140 - 00:11:45.949` | pointer no longer exists and process p |
| `00:11:45.959 - 00:11:48.290` | is also going to Fork the right process |
| `00:11:48.300 - 00:11:51.949` | and again the entire o file array will |
| `00:11:51.959 - 00:11:54.470` | be copied and the reference counts |
| `00:11:54.480 - 00:11:57.110` | increased for all the file objects and |
| `00:11:57.120 - 00:11:58.850` | then we're going to assume that process |
| `00:11:58.860 - 00:12:01.970` | W will immediately close the read end so |
| `00:12:01.980 - 00:12:04.569` | we'll end up following this pointer |
| `00:12:04.579 - 00:12:06.889` | decrementing the reference count and |
| `00:12:06.899 - 00:12:09.050` | then setting that to null so now we have |
| `00:12:09.060 - 00:12:11.930` | this situation by the way my indexes |
| `00:12:11.940 - 00:12:13.370` | don't quite line up here but I think you |
| `00:12:13.380 - 00:12:15.650` | get the idea so at this point we have |
| `00:12:15.660 - 00:12:18.470` | this situation where |
| `00:12:18.480 - 00:12:21.110` | process R Points to a file object for |
| `00:12:21.120 - 00:12:23.750` | the read end of the pipe and process W |
| `00:12:23.760 - 00:12:26.150` | points to a file object for the right |
| `00:12:26.160 - 00:12:28.310` | end of the pipe the parent process still |
| `00:12:28.320 - 00:12:30.350` | has these pointers and perhaps it will |
| `00:12:30.360 - 00:12:32.210` | go ahead and close them or not but I |
| `00:12:32.220 - 00:12:34.910` | don't know but in any case at this point |
| `00:12:34.920 - 00:12:39.050` | process W can send data to process R by |
| `00:12:39.060 - 00:12:41.750` | issuing a system called a write system |
| `00:12:41.760 - 00:12:46.910` | call on the file for file descriptor 4 |
| `00:12:46.920 - 00:12:50.810` | while the reader can read data from the |
| `00:12:50.820 - 00:12:53.810` | pipe by issuing a read system call using |
| `00:12:53.820 - 00:12:57.470` | file descriptor 2. |
| `00:12:57.480 - 00:13:00.470` | so here is the pipe allocate function |
| `00:13:00.480 - 00:13:03.650` | and what it's going to do is allocate |
| `00:13:03.660 - 00:13:06.590` | the pipe structure and these two file |
| `00:13:06.600 - 00:13:08.810` | objects and return pointers to the two |
| `00:13:08.820 - 00:13:13.310` | file objects that were allocated |
| `00:13:13.320 - 00:13:14.509` | so |
| `00:13:14.519 - 00:13:17.569` | it has to return two pointers so that's |
| `00:13:17.579 - 00:13:20.690` | done as follows the first argument at |
| `00:13:20.700 - 00:13:23.030` | zero is a pointer to a pointer and |
| `00:13:23.040 - 00:13:24.650` | that's for the read end |
| `00:13:24.660 - 00:13:26.569` | and the second argument is another |
| `00:13:26.579 - 00:13:28.490` | pointer to a pointer for the right end |
| `00:13:28.500 - 00:13:30.470` | and you can think of it this way if |
| `00:13:30.480 - 00:13:32.269` | you're not familiar with this pointer to |
| `00:13:32.279 - 00:13:35.690` | pointer stuff F0 points to an area where |
| `00:13:35.700 - 00:13:38.870` | we will store a pointer to the read end |
| `00:13:38.880 - 00:13:42.110` | file object and F1 points to an area |
| `00:13:42.120 - 00:13:43.970` | where we will store the pointer to the |
| `00:13:43.980 - 00:13:46.670` | other file object that we allocate and |
| `00:13:46.680 - 00:13:48.170` | this function will return zero if |
| `00:13:48.180 - 00:13:50.090` | everything's okay and minus one if |
| `00:13:50.100 - 00:13:51.410` | there's a problem |
| `00:13:51.420 - 00:13:54.590` | so we start by initializing these two |
| `00:13:54.600 - 00:13:57.769` | variables to zero okay so that they |
| `00:13:57.779 - 00:14:00.110` | don't point to anything and then we call |
| `00:14:00.120 - 00:14:02.569` | file Outlook to allocate the read end |
| `00:14:02.579 - 00:14:06.350` | and save that in this variable here and |
| `00:14:06.360 - 00:14:08.930` | we call file Outlook again and save the |
| `00:14:08.940 - 00:14:11.090` | pointer to this object in this location |
| `00:14:11.100 - 00:14:13.610` | here and if there was a problem with |
| `00:14:13.620 - 00:14:16.009` | either of these and we get null back |
| `00:14:16.019 - 00:14:18.710` | then we're going to go to this bad label |
| `00:14:18.720 - 00:14:20.930` | down below where we clean up |
| `00:14:20.940 - 00:14:23.150` | and the next thing we do is we call kl |
| `00:14:23.160 - 00:14:26.030` | to allocate a page in memory and if |
| `00:14:26.040 - 00:14:28.730` | there's a problem with that again we get |
| `00:14:28.740 - 00:14:32.030` | a null back and we go to bad now if we |
| `00:14:32.040 - 00:14:33.949` | go to bad let's take a look at the bad |
| `00:14:33.959 - 00:14:36.230` | label down here we basically need to |
| `00:14:36.240 - 00:14:37.910` | clean up so we check to see whether |
| `00:14:37.920 - 00:14:39.410` | we've allocated either of these file |
| `00:14:39.420 - 00:14:41.509` | objects and if we've allocated the read |
| `00:14:41.519 - 00:14:43.850` | end well we call file close to get rid |
| `00:14:43.860 - 00:14:45.949` | of it and if we've allocated the right |
| `00:14:45.959 - 00:14:48.310` | end we call file close to get rid of it |
| `00:14:48.320 - 00:14:51.110` | we have this code here that checks Pi |
| `00:14:51.120 - 00:14:52.850` | that's the pointer to the page that |
| `00:14:52.860 - 00:14:54.889` | we've allocated but if you look at this |
| `00:14:54.899 - 00:14:57.189` | code I don't think you can ever actually |
| `00:14:57.199 - 00:15:00.790` | call K free here because |
| `00:15:00.800 - 00:15:04.009` | Pi is set to zero here and if we go to |
| `00:15:04.019 - 00:15:06.050` | bad here Pi will be zero it won't get |
| `00:15:06.060 - 00:15:08.090` | executed and likewise the only way we |
| `00:15:08.100 - 00:15:11.269` | can take this jump is if K Alec returned |
| `00:15:11.279 - 00:15:15.590` | no in which case Pi will be zero so I |
| `00:15:15.600 - 00:15:17.329` | think this test is actually |
| `00:15:17.339 - 00:15:18.250` | um |
| `00:15:18.260 - 00:15:21.470` | Superfluous and cannot actually be used |
| `00:15:21.480 - 00:15:24.290` | but in any case moving on |
| `00:15:24.300 - 00:15:25.970` | the first the next thing we do is we |
| `00:15:25.980 - 00:15:28.910` | allocate the pipe structure so remember |
| `00:15:28.920 - 00:15:30.590` | that we have a number of fields in the |
| `00:15:30.600 - 00:15:32.030` | pipe structure that we need to |
| `00:15:32.040 - 00:15:34.610` | initialize we need to initialize the |
| `00:15:34.620 - 00:15:37.249` | spin lock as well as the two indexes |
| `00:15:37.259 - 00:15:40.430` | into the buffer area and these two flags |
| `00:15:40.440 - 00:15:43.129` | and we're doing that right here here we |
| `00:15:43.139 - 00:15:45.350` | are initializing the two indexes here |
| `00:15:45.360 - 00:15:47.629` | we're initializing the two flags and |
| `00:15:47.639 - 00:15:49.490` | here we are initializing a lock |
| `00:15:49.500 - 00:15:51.350` | associated with this pipe |
| `00:15:51.360 - 00:15:54.290` | then we need to initialize the two file |
| `00:15:54.300 - 00:15:57.290` | objects so for the read end we set its |
| `00:15:57.300 - 00:16:00.050` | type 2 FD pipe and we make it readable |
| `00:16:00.060 - 00:16:02.870` | but not writable and we set its pointer |
| `00:16:02.880 - 00:16:06.889` | to point to the pipe object that we just |
| `00:16:06.899 - 00:16:10.189` | allocated then we allocate then we |
| `00:16:10.199 - 00:16:13.310` | initialize the right end so we set the |
| `00:16:13.320 - 00:16:16.069` | type to fdpipe and we set it to writable |
| `00:16:16.079 - 00:16:18.470` | but not readable and again we set its |
| `00:16:18.480 - 00:16:21.410` | pointer to point to the pipe object and |
| `00:16:21.420 - 00:16:23.930` | finally we return 0 because everything's |
| `00:16:23.940 - 00:16:26.150` | okay |
| `00:16:26.160 - 00:16:29.030` | here is the pipe read function in |
| `00:16:29.040 - 00:16:30.650` | addition to the pipe allocate function |
| `00:16:30.660 - 00:16:33.650` | there's also a read a write and a closed |
| `00:16:33.660 - 00:16:35.030` | function so we only have those four |
| `00:16:35.040 - 00:16:36.889` | functions to deal with |
| `00:16:36.899 - 00:16:38.990` | the pipe read function is past a pointer |
| `00:16:39.000 - 00:16:41.870` | to one of these pipe objects as well as |
| `00:16:41.880 - 00:16:44.210` | an address and a byte count |
| `00:16:44.220 - 00:16:46.970` | this address is a virtual address and |
| `00:16:46.980 - 00:16:49.009` | the byte count tells us how many bytes |
| `00:16:49.019 - 00:16:51.590` | we want to get out of the pipe at most |
| `00:16:51.600 - 00:16:54.170` | and place into the user's virtual |
| `00:16:54.180 - 00:16:55.610` | address space |
| `00:16:55.620 - 00:16:57.769` | this function will return the number of |
| `00:16:57.779 - 00:17:00.829` | bytes that we actually read and this |
| `00:17:00.839 - 00:17:02.509` | could be zero if the pipe has been |
| `00:17:02.519 - 00:17:04.069` | closed down |
| `00:17:04.079 - 00:17:06.949` | and if there's a problem uh it might |
| `00:17:06.959 - 00:17:09.470` | return minus one |
| `00:17:09.480 - 00:17:11.150` | okay since we're going to be accessing |
| `00:17:11.160 - 00:17:13.730` | the counters the in read and the in |
| `00:17:13.740 - 00:17:16.429` | right counters we need to First acquire |
| `00:17:16.439 - 00:17:19.069` | the spin lock that protects this pipe |
| `00:17:19.079 - 00:17:22.010` | structure so we acquire that lock right |
| `00:17:22.020 - 00:17:23.929` | here |
| `00:17:23.939 - 00:17:27.409` | and then in this Loop here we are going |
| `00:17:27.419 - 00:17:29.150` | to be checking to see whether the pipe |
| `00:17:29.160 - 00:17:31.430` | is empty right we have two situations |
| `00:17:31.440 - 00:17:33.710` | that we need to watch for one is whether |
| `00:17:33.720 - 00:17:36.169` | the pipe is empty and the other is |
| `00:17:36.179 - 00:17:39.350` | whether the pipe is full if the pipe is |
| `00:17:39.360 - 00:17:43.010` | empty then the reader will have to go to |
| `00:17:43.020 - 00:17:46.130` | sleep and wait for a rider and if the |
| `00:17:46.140 - 00:17:48.289` | pipe is full then a writer would have to |
| `00:17:48.299 - 00:17:50.450` | go to sleep and wait for a reader to |
| `00:17:50.460 - 00:17:52.909` | take some of the bites out of the pipe |
| `00:17:52.919 - 00:17:54.590` | so here we're checking to see whether |
| `00:17:54.600 - 00:17:56.750` | these two things are equal and if the |
| `00:17:56.760 - 00:17:58.909` | two indexes are the same we have a |
| `00:17:58.919 - 00:18:00.590` | situation like this where we have an |
| `00:18:00.600 - 00:18:02.630` | empty pipe |
| `00:18:02.640 - 00:18:06.350` | so if we still have a writer okay then |
| `00:18:06.360 - 00:18:08.750` | we will go to sleep and wait for that |
| `00:18:08.760 - 00:18:10.789` | Rider to put something in |
| `00:18:10.799 - 00:18:12.710` | if we don't have a writer then we've got |
| `00:18:12.720 - 00:18:13.850` | a problem |
| `00:18:13.860 - 00:18:16.669` | um there's no point in going to sleep if |
| `00:18:16.679 - 00:18:19.370` | the right open is false it means there |
| `00:18:19.380 - 00:18:21.110` | is no file object that is pointing to |
| `00:18:21.120 - 00:18:23.570` | this pipe and so no bytes will ever be |
| `00:18:23.580 - 00:18:26.390` | written and we will never be awakened |
| `00:18:26.400 - 00:18:28.070` | from any sleep we would do so we better |
| `00:18:28.080 - 00:18:30.350` | not go to sleep instead we'll just |
| `00:18:30.360 - 00:18:32.090` | return zero down here |
| `00:18:32.100 - 00:18:34.909` | so um let's say that the pipe is empty |
| `00:18:34.919 - 00:18:37.549` | and we do have a rider then we will go |
| `00:18:37.559 - 00:18:43.549` | to sleep okay so the sleep is needs a a |
| `00:18:43.559 - 00:18:45.890` | channel and the channel number that |
| `00:18:45.900 - 00:18:47.570` | we're using that is the code or |
| `00:18:47.580 - 00:18:49.549` | identifier that we're using to the sleep |
| `00:18:49.559 - 00:18:50.690` | function |
| `00:18:50.700 - 00:18:55.850` | is uh what is the address of the read |
| `00:18:55.860 - 00:18:58.430` | index so we're passing it the address of |
| `00:18:58.440 - 00:18:59.450` | the read |
| `00:18:59.460 - 00:19:02.510` | field within the pipe structure that is |
| `00:19:02.520 - 00:19:05.750` | currently empty and that will be the |
| `00:19:05.760 - 00:19:08.270` | channel That the writer will wake up on |
| `00:19:08.280 - 00:19:10.430` | it will issue a wake-up call using this |
| `00:19:10.440 - 00:19:13.190` | channel to wake up this process |
| `00:19:13.200 - 00:19:15.110` | when we call sleep we need to be holding |
| `00:19:15.120 - 00:19:17.029` | a spin lock and we are holding the spin |
| `00:19:17.039 - 00:19:18.890` | lock and that will be momentarily |
| `00:19:18.900 - 00:19:21.169` | released but then when we are reawakened |
| `00:19:21.179 - 00:19:23.510` | it will be reacquired and we'll loop |
| `00:19:23.520 - 00:19:25.490` | back and again check to see whether the |
| `00:19:25.500 - 00:19:27.830` | pipe is empty it could be that some |
| `00:19:27.840 - 00:19:30.710` | other process is another reader and |
| `00:19:30.720 - 00:19:33.350` | grabbed those bytes and then the pipe is |
| `00:19:33.360 - 00:19:35.330` | still empty so we make these checks |
| `00:19:35.340 - 00:19:39.169` | again and assuming that there are bytes |
| `00:19:39.179 - 00:19:42.110` | in the pipe then we can proceed down |
| `00:19:42.120 - 00:19:43.430` | here |
| `00:19:43.440 - 00:19:46.549` | before we go to sleep we also check to |
| `00:19:46.559 - 00:19:48.590` | see whether this process has been killed |
| `00:19:48.600 - 00:19:50.990` | by some other process so we're grabbing |
| `00:19:51.000 - 00:19:53.390` | a pointer to our proc structure and |
| `00:19:53.400 - 00:19:56.029` | we're checking the killed field here and |
| `00:19:56.039 - 00:19:57.590` | if that's been set to true then we need |
| `00:19:57.600 - 00:20:00.289` | to you know terminate after the system |
| `00:20:00.299 - 00:20:02.690` | call that's involved with this pipe |
| `00:20:02.700 - 00:20:05.330` | reading and so we immediately release |
| `00:20:05.340 - 00:20:08.029` | the lock and return -1 to indicate that |
| `00:20:08.039 - 00:20:10.130` | something is wrong |
| `00:20:10.140 - 00:20:12.649` | okay so assuming that's not the case we |
| `00:20:12.659 - 00:20:15.289` | go to sleep eventually we wake up and we |
| `00:20:15.299 - 00:20:19.370` | find that the either the file is no |
| `00:20:19.380 - 00:20:22.909` | longer empty or there is no writer |
| `00:20:22.919 - 00:20:24.830` | anymore in which case we need to go |
| `00:20:24.840 - 00:20:26.690` | ahead and return something |
| `00:20:26.700 - 00:20:28.310` | so what we're going to do is we're going |
| `00:20:28.320 - 00:20:31.130` | to Loop through each byte we need to |
| `00:20:31.140 - 00:20:34.909` | find up to n bytes and we're going to |
| `00:20:34.919 - 00:20:36.590` | ultimately return the number of bytes |
| `00:20:36.600 - 00:20:38.270` | that we have transferred and we're going |
| `00:20:38.280 - 00:20:41.330` | to transfer each one byte at a time |
| `00:20:41.340 - 00:20:44.630` | so we check here to see whether the pipe |
| `00:20:44.640 - 00:20:46.730` | is empty we could get to this situation |
| `00:20:46.740 - 00:20:49.070` | after looping through this a couple of |
| `00:20:49.080 - 00:20:51.110` | times in which case we've emptied the |
| `00:20:51.120 - 00:20:53.990` | pipe or it could be that on the first |
| `00:20:54.000 - 00:20:56.029` | iteration we got into here because |
| `00:20:56.039 - 00:20:58.490` | there's no writer and so the pipe is |
| `00:20:58.500 - 00:21:01.010` | empty and furthermore there's no Rider |
| `00:21:01.020 - 00:21:03.470` | so in that case we need to return zero |
| `00:21:03.480 - 00:21:04.669` | immediately |
| `00:21:04.679 - 00:21:06.529` | but normally we would get at least a |
| `00:21:06.539 - 00:21:08.690` | couple of bytes so |
| `00:21:08.700 - 00:21:12.169` | um I will not be zero |
| `00:21:12.179 - 00:21:13.490` | so |
| `00:21:13.500 - 00:21:16.430` | um if the pipe has something in it then |
| `00:21:16.440 - 00:21:17.990` | what we're going to do here is we're |
| `00:21:18.000 - 00:21:21.110` | going to go to the buffer area in the |
| `00:21:21.120 - 00:21:24.289` | pipe structure using in read as the |
| `00:21:24.299 - 00:21:27.110` | index and remember it's a circular pipe |
| `00:21:27.120 - 00:21:29.690` | and so we need to do mod the pipe size |
| `00:21:29.700 - 00:21:33.649` | which happens to be 512. and then we use |
| `00:21:33.659 - 00:21:36.890` | that as an index to grab a byte of data |
| `00:21:36.900 - 00:21:40.549` | advancing the index and we save the byte |
| `00:21:40.559 - 00:21:43.970` | in ch and then we're going to transfer |
| `00:21:43.980 - 00:21:46.250` | that one character to the virtual |
| `00:21:46.260 - 00:21:48.890` | address space so that's the copy out |
| `00:21:48.900 - 00:21:51.350` | function right here it's best to pointer |
| `00:21:51.360 - 00:21:53.090` | to the page table that describes the |
| `00:21:53.100 - 00:21:55.730` | user's virtual address space as well as |
| `00:21:55.740 - 00:21:58.789` | where we want to place that byte so this |
| `00:21:58.799 - 00:22:00.830` | is the virtual address that was our |
| `00:22:00.840 - 00:22:03.830` | argument here offset by how many bytes |
| `00:22:03.840 - 00:22:06.950` | we've read and we are passing a pointer |
| `00:22:06.960 - 00:22:09.710` | to this byte that we just retrieved as |
| `00:22:09.720 - 00:22:12.529` | well as a byte count of one and if that |
| `00:22:12.539 - 00:22:14.990` | works okay then we loop back but |
| `00:22:15.000 - 00:22:16.610` | otherwise we break out of this Loop and |
| `00:22:16.620 - 00:22:18.789` | we've had we're done |
| `00:22:18.799 - 00:22:21.789` | in any case we have |
| `00:22:21.799 - 00:22:24.890` | red bites well maybe we haven't actually |
| `00:22:24.900 - 00:22:26.810` | retrieved any bytes and incremented the |
| `00:22:26.820 - 00:22:28.070` | in read |
| `00:22:28.080 - 00:22:30.289` | um we can't it can't hurt to wake up a |
| `00:22:30.299 - 00:22:33.230` | process but in any case we must be sure |
| `00:22:33.240 - 00:22:35.990` | to wake up any writers because we have |
| `00:22:36.000 - 00:22:39.350` | probably removed some bites from the |
| `00:22:39.360 - 00:22:41.810` | pipe and so the pipe is no longer empty |
| `00:22:41.820 - 00:22:46.390` | so we use the address of the right index |
| `00:22:46.400 - 00:22:50.570` | for the writer so while this while |
| `00:22:50.580 - 00:22:53.810` | readers will sleep on the read index and |
| `00:22:53.820 - 00:22:56.630` | wake up on the right index Riders will |
| `00:22:56.640 - 00:22:59.810` | sleep on the right index and wake up on |
| `00:22:59.820 - 00:23:01.850` | the read index |
| `00:23:01.860 - 00:23:04.850` | well after waking up any waiting waking |
| `00:23:04.860 - 00:23:07.669` | up all waiting writers we will release |
| `00:23:07.679 - 00:23:09.470` | the spin lock and return the number of |
| `00:23:09.480 - 00:23:13.549` | bytes that we have moved into the user's |
| `00:23:13.559 - 00:23:16.909` | virtual address space uh at this line |
| `00:23:16.919 - 00:23:20.029` | okay next let's move on to the right |
| `00:23:20.039 - 00:23:23.510` | function |
| `00:23:23.520 - 00:23:26.750` | here is the pipe right function like the |
| `00:23:26.760 - 00:23:28.730` | pipe read function it's past a pointer |
| `00:23:28.740 - 00:23:31.010` | to the pipe structure as well as an |
| `00:23:31.020 - 00:23:33.590` | address and a byte count the address is |
| `00:23:33.600 - 00:23:35.450` | a virtual address and the user's address |
| `00:23:35.460 - 00:23:38.690` | space and N is the number of bytes to |
| `00:23:38.700 - 00:23:40.610` | transfer from that virtual address base |
| `00:23:40.620 - 00:23:44.149` | to the pipe it Returns the number of |
| `00:23:44.159 - 00:23:47.870` | bytes that have been moved into the pipe |
| `00:23:47.880 - 00:23:50.690` | in the case of pipe Reed the number |
| `00:23:50.700 - 00:23:53.450` | returned may be less than the desired |
| `00:23:53.460 - 00:23:54.610` | number |
| `00:23:54.620 - 00:23:57.890` | the pipe read function will try to read |
| `00:23:57.900 - 00:24:01.490` | at least one thing but will return after |
| `00:24:01.500 - 00:24:04.010` | getting at least one byte if there is |
| `00:24:04.020 - 00:24:07.610` | nothing else waiting in the pipe |
| `00:24:07.620 - 00:24:10.310` | on the other hand pipe right will try to |
| `00:24:10.320 - 00:24:12.409` | write all n bytes and will normally |
| `00:24:12.419 - 00:24:14.870` | return in if there's some sort of an |
| `00:24:14.880 - 00:24:16.909` | error it may return |
| `00:24:16.919 - 00:24:19.190` | -1 and if there's a problem with the |
| `00:24:19.200 - 00:24:22.970` | page table it might return a value less |
| `00:24:22.980 - 00:24:26.149` | than n but normally it will return all |
| `00:24:26.159 - 00:24:28.850` | in bytes so the structure of this |
| `00:24:28.860 - 00:24:31.190` | function is a loop and each iteration of |
| `00:24:31.200 - 00:24:34.850` | the loop will try to move exactly one |
| `00:24:34.860 - 00:24:37.190` | byte and that happens right here and we |
| `00:24:37.200 - 00:24:40.970` | increment I so I starts out as the count |
| `00:24:40.980 - 00:24:43.130` | I is the count of bytes that we have |
| `00:24:43.140 - 00:24:46.370` | moved and it will keep being incremented |
| `00:24:46.380 - 00:24:48.649` | and ultimately when we exit this loop I |
| `00:24:48.659 - 00:24:51.470` | will be equal to n and we will return in |
| `00:24:51.480 - 00:24:54.890` | the number of bytes moved uh it may be |
| `00:24:54.900 - 00:24:57.289` | that we execute exit prematurely if |
| `00:24:57.299 - 00:24:58.490` | there's a problem with the page table |
| `00:24:58.500 - 00:25:02.930` | and we return a smaller in a smaller |
| `00:25:02.940 - 00:25:05.090` | value than n |
| `00:25:05.100 - 00:25:06.950` | so let's take a look at this |
| `00:25:06.960 - 00:25:09.830` | first of all we check in the body of the |
| `00:25:09.840 - 00:25:11.870` | loop to see whether we have any readers |
| `00:25:11.880 - 00:25:14.630` | if the read open flag of the pipe |
| `00:25:14.640 - 00:25:17.870` | structure is false then there are no |
| `00:25:17.880 - 00:25:20.690` | readers so we immediately return minus |
| `00:25:20.700 - 00:25:21.610` | one |
| `00:25:21.620 - 00:25:25.070` | likewise if we have been killed then the |
| `00:25:25.080 - 00:25:27.710` | killed field of this process will have |
| `00:25:27.720 - 00:25:29.750` | been set and we need to abort |
| `00:25:29.760 - 00:25:32.750` | immediately and return -1 but in either |
| `00:25:32.760 - 00:25:34.970` | case we release the spin lock first |
| `00:25:34.980 - 00:25:37.130` | before returning |
| `00:25:37.140 - 00:25:39.049` | so assuming everything's good we next |
| `00:25:39.059 - 00:25:41.029` | need to check to see whether the pipe is |
| `00:25:41.039 - 00:25:44.990` | full and here we have that check so in |
| `00:25:45.000 - 00:25:47.990` | read plus the pipe size |
| `00:25:48.000 - 00:25:51.769` | which is 512 if that equals the right |
| `00:25:51.779 - 00:25:54.169` | index then we've got a pipe full |
| `00:25:54.179 - 00:25:57.350` | situation that is shown in this picture |
| `00:25:57.360 - 00:26:01.850` | here where in read plus 512 is equal to |
| `00:26:01.860 - 00:26:04.610` | in right |
| `00:26:04.620 - 00:26:07.549` | if we've got a pipe full situation then |
| `00:26:07.559 - 00:26:10.789` | we need to go to sleep but and we will |
| `00:26:10.799 - 00:26:14.029` | use as the channel to the sleep function |
| `00:26:14.039 - 00:26:18.470` | the address of the right index in the |
| `00:26:18.480 - 00:26:20.149` | pipe structure |
| `00:26:20.159 - 00:26:23.750` | okay and readers will |
| `00:26:23.760 - 00:26:27.289` | wake up using that channel remember that |
| `00:26:27.299 - 00:26:31.010` | the here's a pipe read function and |
| `00:26:31.020 - 00:26:32.690` | remember that when we do a wake up we |
| `00:26:32.700 - 00:26:36.409` | wake up on the right index for the pipe |
| `00:26:36.419 - 00:26:40.669` | that we want to wake up |
| `00:26:40.679 - 00:26:43.490` | before we go to sleep though it may be |
| `00:26:43.500 - 00:26:46.850` | that we have some readers that are |
| `00:26:46.860 - 00:26:49.310` | waiting and so we need to wake up any |
| `00:26:49.320 - 00:26:51.830` | sleeping readers it may be that this |
| `00:26:51.840 - 00:26:53.870` | Loop has looped through we started we |
| `00:26:53.880 - 00:26:56.090` | might have started with an empty pipe |
| `00:26:56.100 - 00:26:58.669` | um and the readers were waiting to be |
| `00:26:58.679 - 00:27:02.149` | awakened and in maybe a very large |
| `00:27:02.159 - 00:27:05.450` | number greater than 512 and we may have |
| `00:27:05.460 - 00:27:07.610` | executed this Loop enough times to fill |
| `00:27:07.620 - 00:27:11.330` | up the pipe in which case uh we need to |
| `00:27:11.340 - 00:27:13.490` | go to sleep because the pipe is full but |
| `00:27:13.500 - 00:27:14.769` | we've got |
| `00:27:14.779 - 00:27:16.909` | readers that went to sleep when the pipe |
| `00:27:16.919 - 00:27:19.010` | was empty so we need to wake up any |
| `00:27:19.020 - 00:27:21.049` | readers that are out there first before |
| `00:27:21.059 - 00:27:23.149` | we go to sleep |
| `00:27:23.159 - 00:27:26.330` | okay assuming that the pipe is not full |
| `00:27:26.340 - 00:27:29.389` | then we can go ahead and move a bite to |
| `00:27:29.399 - 00:27:31.870` | the pipe so that's what's Happening Here |
| `00:27:31.880 - 00:27:35.510` | we use the copy in function to copy |
| `00:27:35.520 - 00:27:37.250` | bytes from |
| `00:27:37.260 - 00:27:40.250` | a virtual address space as described by |
| `00:27:40.260 - 00:27:43.190` | this page table here to some place |
| `00:27:43.200 - 00:27:46.070` | that's local variable CH and where are |
| `00:27:46.080 - 00:27:48.289` | we copying from well it's the virtual |
| `00:27:48.299 - 00:27:49.970` | address plus the number of bytes already |
| `00:27:49.980 - 00:27:53.090` | copied and we're moving one byte at a |
| `00:27:53.100 - 00:27:53.810` | time |
| `00:27:53.820 - 00:27:56.690` | and if anything goes wrong then we abort |
| `00:27:56.700 - 00:27:59.870` | the pipe right function with however |
| `00:27:59.880 - 00:28:02.090` | many bytes we've successfully copied we |
| `00:28:02.100 - 00:28:04.730` | break out of this Loop and return the |
| `00:28:04.740 - 00:28:06.710` | number of bytes we've copied so far but |
| `00:28:06.720 - 00:28:09.169` | assuming everything's okay then we've |
| `00:28:09.179 - 00:28:11.630` | got a bite from the user's virtual |
| `00:28:11.640 - 00:28:14.450` | address space and in this next line we |
| `00:28:14.460 - 00:28:18.110` | move it into the buffer for this pipe so |
| `00:28:18.120 - 00:28:22.430` | we are using the right index and we are |
| `00:28:22.440 - 00:28:26.450` | taking it mod 512 the pipe size and |
| `00:28:26.460 - 00:28:28.549` | we're moving the byte into that location |
| `00:28:28.559 - 00:28:32.330` | in the in the buffer and we are then |
| `00:28:32.340 - 00:28:35.389` | incrementing the right index as well as |
| `00:28:35.399 - 00:28:38.570` | incrementing I and then we loop back |
| `00:28:38.580 - 00:28:41.149` | now after we're done filling the pipe |
| `00:28:41.159 - 00:28:42.710` | there may have been some readers that |
| `00:28:42.720 - 00:28:45.350` | were sleeping and so we need to issue a |
| `00:28:45.360 - 00:28:48.110` | wake-up call and again we're issuing the |
| `00:28:48.120 - 00:28:51.049` | wake-up call using as the channel the |
| `00:28:51.059 - 00:28:54.950` | address of the in read index which is |
| `00:28:54.960 - 00:28:56.990` | what any Sleepers for this pipe will |
| `00:28:57.000 - 00:28:58.430` | have gone to sleep on |
| `00:28:58.440 - 00:29:01.610` | and finally we release the spin lock for |
| `00:29:01.620 - 00:29:03.710` | this pipe and return the number of bytes |
| `00:29:03.720 - 00:29:06.289` | that we have transferred |
| `00:29:06.299 - 00:29:08.570` | finally let's look at the pipe close |
| `00:29:08.580 - 00:29:11.630` | function remember that from time to time |
| `00:29:11.640 - 00:29:14.210` | a user process will call the close |
| `00:29:14.220 - 00:29:17.210` | system call providing a file descriptor |
| `00:29:17.220 - 00:29:19.250` | for example fault descriptor number two |
| `00:29:19.260 - 00:29:22.070` | and what that means is the user wants to |
| `00:29:22.080 - 00:29:24.649` | remove this pointer and eliminate this |
| `00:29:24.659 - 00:29:27.889` | file from this process |
| `00:29:27.899 - 00:29:29.690` | there is a reference count associated |
| `00:29:29.700 - 00:29:33.049` | with each of these file objects and if |
| `00:29:33.059 - 00:29:35.690` | it does not go to zero when we eliminate |
| `00:29:35.700 - 00:29:37.549` | this pointer then we don't do anything |
| `00:29:37.559 - 00:29:40.250` | but eventually it will go to zero when |
| `00:29:40.260 - 00:29:41.930` | there are no more pointers to the file |
| `00:29:41.940 - 00:29:44.750` | object and at that point we need to |
| `00:29:44.760 - 00:29:47.529` | return the file object to the free pool |
| `00:29:47.539 - 00:29:50.810` | and eliminate this pointer here and so |
| `00:29:50.820 - 00:29:52.970` | at that point we will call the pipe |
| `00:29:52.980 - 00:29:56.090` | close function on this object here |
| `00:29:56.100 - 00:29:57.889` | so whenever we |
| `00:29:57.899 - 00:30:00.590` | close the read end or close the right |
| `00:30:00.600 - 00:30:03.169` | end we will call this pipe close |
| `00:30:03.179 - 00:30:06.950` | function so let's take a look at it it |
| `00:30:06.960 - 00:30:08.930` | is passed a pointer to the pipe |
| `00:30:08.940 - 00:30:11.870` | structure and a flag to indicate whether |
| `00:30:11.880 - 00:30:14.510` | we are closing the right end or the read |
| `00:30:14.520 - 00:30:17.750` | end and so that's this argument here |
| `00:30:17.760 - 00:30:21.649` | We Begin by acquiring the lock for the |
| `00:30:21.659 - 00:30:25.430` | pipe object and then if we are closing |
| `00:30:25.440 - 00:30:30.169` | the right end then we will set the right |
| `00:30:30.179 - 00:30:34.549` | open flag in the pipe structure to zero |
| `00:30:34.559 - 00:30:38.570` | okay again that's this one right here to |
| `00:30:38.580 - 00:30:40.669` | indicate that we have lost the pointer |
| `00:30:40.679 - 00:30:43.370` | from the file object that's associated |
| `00:30:43.380 - 00:30:44.990` | with the right end |
| `00:30:45.000 - 00:30:48.350` | now there could be readers or processes |
| `00:30:48.360 - 00:30:51.350` | that are reading and they are waiting |
| `00:30:51.360 - 00:30:52.970` | because they found that the pipe was |
| `00:30:52.980 - 00:30:55.130` | empty and so they're just waiting for |
| `00:30:55.140 - 00:30:57.470` | something to go into the pipe so we need |
| `00:30:57.480 - 00:31:00.230` | to wake those up remember that a reader |
| `00:31:00.240 - 00:31:03.230` | will be sleeping if it finds that the |
| `00:31:03.240 - 00:31:05.509` | pipe is empty so going to the pipe Reed |
| `00:31:05.519 - 00:31:09.310` | again we see that if the pipe is empty |
| `00:31:09.320 - 00:31:11.889` | then we will be sleeping |
| `00:31:11.899 - 00:31:14.870` | but we also check to make sure that we |
| `00:31:14.880 - 00:31:17.810` | do have a writer and if we eliminate the |
| `00:31:17.820 - 00:31:19.490` | right end if we close down the right end |
| `00:31:19.500 - 00:31:22.669` | we need to wake this uh sleeping reader |
| `00:31:22.679 - 00:31:24.590` | back up and it will check and then it |
| `00:31:24.600 - 00:31:27.529` | will find that there is no uh right and |
| `00:31:27.539 - 00:31:29.750` | open and it will go ahead and return |
| `00:31:29.760 - 00:31:33.769` | zero so that's what's going on here if |
| `00:31:33.779 - 00:31:35.450` | we're closing the right end we set the |
| `00:31:35.460 - 00:31:37.789` | flag to zero to indicate that we do not |
| `00:31:37.799 - 00:31:40.850` | have any right end and we wake up any |
| `00:31:40.860 - 00:31:43.009` | waiting readers on the other hand if |
| `00:31:43.019 - 00:31:46.730` | we're closing the read end then we set |
| `00:31:46.740 - 00:31:49.549` | the read open flag to zero and wake up |
| `00:31:49.559 - 00:31:54.169` | any sleeping writers for the same reason |
| `00:31:54.179 - 00:31:57.110` | finally if we have closed both the read |
| `00:31:57.120 - 00:31:59.389` | end and the right end in other words if |
| `00:31:59.399 - 00:32:01.909` | now both of these flags are zero then we |
| `00:32:01.919 - 00:32:03.769` | are done with a pipe object all together |
| `00:32:03.779 - 00:32:07.070` | so at that point we release the lock and |
| `00:32:07.080 - 00:32:10.009` | call K free to return the page that |
| `00:32:10.019 - 00:32:12.350` | contained the pipe object back to the |
| `00:32:12.360 - 00:32:16.850` | free pool of pages if the uh if there's |
| `00:32:16.860 - 00:32:20.810` | still one of the ends open then we will |
| `00:32:20.820 - 00:32:23.750` | just release the spin lock for this pipe |
| `00:32:23.760 - 00:32:26.590` | object and return okay that's it for |
| `00:32:26.600 - 00:32:31.700` | pipe.c I will see you in the next video |
