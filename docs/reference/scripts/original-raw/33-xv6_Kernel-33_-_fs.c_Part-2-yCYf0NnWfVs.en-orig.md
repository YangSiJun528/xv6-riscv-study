# xv6 Kernel-33: fs.c Part-2

| Field | Value |
| --- | --- |
| Video ID | `yCYf0NnWfVs` |
| URL | <https://www.youtube.com/watch?v=yCYf0NnWfVs> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.960 - 00:00:02.750` | this video is part of a series on the |
| `00:00:02.760 - 00:00:05.030` | xb6 operating system kernel |
| `00:00:05.040 - 00:00:08.210` | in an earlier video I described the |
| `00:00:08.220 - 00:00:09.890` | inode system and talked about the |
| `00:00:09.900 - 00:00:12.890` | general organization of the file fs.c I |
| `00:00:12.900 - 00:00:14.690` | talked about the data structures and I |
| `00:00:14.700 - 00:00:16.609` | gave an overview of all of the functions |
| `00:00:16.619 - 00:00:18.290` | in that file |
| `00:00:18.300 - 00:00:22.250` | in the previous video to this video I |
| `00:00:22.260 - 00:00:25.250` | started walking through the code of the |
| `00:00:25.260 - 00:00:28.550` | functions that are in fs.c and in this |
| `00:00:28.560 - 00:00:30.529` | video I will finish walking through |
| `00:00:30.539 - 00:00:33.709` | those functions let me just put on the |
| `00:00:33.719 - 00:00:36.290` | screen for a visual reference what |
| `00:00:36.300 - 00:00:37.549` | functions were covered in the previous |
| `00:00:37.559 - 00:00:41.889` | video so we covered all these functions |
| `00:00:41.899 - 00:00:48.049` | and here are some more that we covered |
| `00:00:48.059 - 00:00:52.010` | I will start with bmap in this video but |
| `00:00:52.020 - 00:00:54.529` | in the previous video I covered eye |
| `00:00:54.539 - 00:00:56.630` | trunk and Stat I |
| `00:00:56.640 - 00:00:59.810` | after I talk about bmap I will go ahead |
| `00:00:59.820 - 00:01:02.029` | and talk about the remaining functions |
| `00:01:02.039 - 00:01:05.329` | starting with read I and so on |
| `00:01:05.339 - 00:01:09.710` | and as I said we are working toward the |
| `00:01:09.720 - 00:01:10.789` | name |
| `00:01:10.799 - 00:01:14.450` | I function and I will end by talking |
| `00:01:14.460 - 00:01:17.690` | about the name I function which is the |
| `00:01:17.700 - 00:01:20.030` | function that is past a pointer to a |
| `00:01:20.040 - 00:01:22.310` | string that contains a path and we'll |
| `00:01:22.320 - 00:01:25.910` | find that file on the disk and read an |
| `00:01:25.920 - 00:01:28.190` | inode into the in-memory cache and |
| `00:01:28.200 - 00:01:31.069` | return a pointer to that so let's get |
| `00:01:31.079 - 00:01:32.929` | started |
| `00:01:32.939 - 00:01:35.210` | from time to time we want to read data |
| `00:01:35.220 - 00:01:38.390` | from write data to a file and we need to |
| `00:01:38.400 - 00:01:41.210` | know where on disk that data is stored |
| `00:01:41.220 - 00:01:44.330` | so which block number on disk do we need |
| `00:01:44.340 - 00:01:47.030` | to read or write to get the data and |
| `00:01:47.040 - 00:01:49.010` | that's the function of this bmap |
| `00:01:49.020 - 00:01:51.590` | function it's passed a file or more |
| `00:01:51.600 - 00:01:53.270` | precisely a pointer to some inode |
| `00:01:53.280 - 00:01:55.429` | describing a file as well as the block |
| `00:01:55.439 - 00:01:57.289` | number relative to the beginning of the |
| `00:01:57.299 - 00:01:59.330` | file so let's say we want to write some |
| `00:01:59.340 - 00:02:01.429` | bytes in well let's say the seventh |
| `00:02:01.439 - 00:02:04.130` | Block in the file we need to convert |
| `00:02:04.140 - 00:02:06.950` | that seven block number into the actual |
| `00:02:06.960 - 00:02:09.830` | block number on disk return the disk |
| `00:02:09.840 - 00:02:11.809` | block address of the inth Block in the |
| `00:02:11.819 - 00:02:15.110` | file and if that block has not been |
| `00:02:15.120 - 00:02:18.050` | allocated to the file then this function |
| `00:02:18.060 - 00:02:22.190` | will allocate and zero a new block |
| `00:02:22.200 - 00:02:25.250` | so looking at the picture here uh what |
| `00:02:25.260 - 00:02:27.290` | we are going to do let's say we want to |
| `00:02:27.300 - 00:02:29.869` | get the seventh block we'll need to go |
| `00:02:29.879 - 00:02:32.690` | to the cache uh inode well let me use |
| `00:02:32.700 - 00:02:34.070` | this picture over here because it's got |
| `00:02:34.080 - 00:02:36.949` | the indirect block and if it's in one of |
| `00:02:36.959 - 00:02:38.930` | the first 12 blocks we just need to |
| `00:02:38.940 - 00:02:40.369` | access the inode and get the block |
| `00:02:40.379 - 00:02:41.390` | number |
| `00:02:41.400 - 00:02:44.570` | and find out where on disk that block is |
| `00:02:44.580 - 00:02:48.710` | stored if it's uh greater than uh 12 |
| `00:02:48.720 - 00:02:50.509` | here if it's 12 or greater we need to |
| `00:02:50.519 - 00:02:52.369` | access the indirect block and get the |
| `00:02:52.379 - 00:02:56.449` | block number from the indirect array |
| `00:02:56.459 - 00:02:59.930` | so that's what we are doing here |
| `00:02:59.940 - 00:03:01.910` | uh first we're asking whether the block |
| `00:03:01.920 - 00:03:04.910` | number that we're looking for is less |
| `00:03:04.920 - 00:03:08.149` | than well 12 that is if it's 0 through |
| `00:03:08.159 - 00:03:10.369` | 11. in that case we can use one of the |
| `00:03:10.379 - 00:03:12.830` | direct pointers so we are going to look |
| `00:03:12.840 - 00:03:16.729` | into the inode and the array and get the |
| `00:03:16.739 - 00:03:20.210` | element from that array and save it and |
| `00:03:20.220 - 00:03:23.570` | then return it okay if that array |
| `00:03:23.580 - 00:03:26.149` | contains a zero pointer then that block |
| `00:03:26.159 - 00:03:29.089` | hasn't been allocated so we need to call |
| `00:03:29.099 - 00:03:32.350` | B Alec we will |
| `00:03:32.360 - 00:03:36.649` | allocate or find a free block and set it |
| `00:03:36.659 - 00:03:39.530` | to all zeros and return the block number |
| `00:03:39.540 - 00:03:41.930` | on disk which we will then store into |
| `00:03:41.940 - 00:03:44.390` | the array so that it will be there in |
| `00:03:44.400 - 00:03:47.030` | the future and in either case we return |
| `00:03:47.040 - 00:03:48.770` | the block number |
| `00:03:48.780 - 00:03:51.289` | however if the block that we're looking |
| `00:03:51.299 - 00:03:54.530` | for is 12 or greater then we are going |
| `00:03:54.540 - 00:03:56.570` | to need to follow this pointer and look |
| `00:03:56.580 - 00:03:58.789` | in the indirect block so for example if |
| `00:03:58.799 - 00:04:02.570` | we're looking for a block number |
| `00:04:02.580 - 00:04:08.869` | 14 well this is uh 12 13 14 it's this |
| `00:04:08.879 - 00:04:11.570` | one we need to take the block number 14 |
| `00:04:11.580 - 00:04:14.750` | and subtract off these first 12 to get |
| `00:04:14.760 - 00:04:16.729` | an index into this array that is we need |
| `00:04:16.739 - 00:04:18.830` | the index of two so that's what's |
| `00:04:18.840 - 00:04:21.229` | happening in this line here we're |
| `00:04:21.239 - 00:04:23.150` | subtracting off 12 from the block number |
| `00:04:23.160 - 00:04:25.670` | and now we've got an index that's |
| `00:04:25.680 - 00:04:28.790` | between 0 and 255. or at least we hope |
| `00:04:28.800 - 00:04:30.469` | it is the first thing we do is check to |
| `00:04:30.479 - 00:04:33.170` | make sure that our index is not larger |
| `00:04:33.180 - 00:04:37.969` | than 255 and if it is well we've tried |
| `00:04:37.979 - 00:04:41.450` | to access a block that is beyond the |
| `00:04:41.460 - 00:04:43.670` | maximum file size that should have been |
| `00:04:43.680 - 00:04:45.650` | checked by whoever calls this function |
| `00:04:45.660 - 00:04:49.909` | and if not we uh issue a panic |
| `00:04:49.919 - 00:04:52.730` | um so then we need to load the indirect |
| `00:04:52.740 - 00:04:57.770` | block and we then go to the array and |
| `00:04:57.780 - 00:04:59.409` | this is the last element of the array |
| `00:04:59.419 - 00:05:03.650` | and ask whether it's zero or not if it |
| `00:05:03.660 - 00:05:07.370` | is 0 then we need to allocate the block |
| `00:05:07.380 - 00:05:09.950` | of indirect pointers so here we allocate |
| `00:05:09.960 - 00:05:12.469` | that indirect block and set it all to |
| `00:05:12.479 - 00:05:15.890` | zeros and we save the block number of |
| `00:05:15.900 - 00:05:18.770` | the indirect Block in the array so we're |
| `00:05:18.780 - 00:05:21.890` | allocating this block here and saving a |
| `00:05:21.900 - 00:05:24.529` | pointer in this element here |
| `00:05:24.539 - 00:05:26.170` | and that happens |
| `00:05:26.180 - 00:05:29.990` | here and we in either case we get the |
| `00:05:30.000 - 00:05:33.350` | address of the indirect block and here |
| `00:05:33.360 - 00:05:36.529` | we are reading the indirect block |
| `00:05:36.539 - 00:05:39.230` | into some buffer and setting a to point |
| `00:05:39.240 - 00:05:41.510` | to where that data is in that buffer so |
| `00:05:41.520 - 00:05:43.790` | that's the beginning of this array and |
| `00:05:43.800 - 00:05:46.490` | then we're using our value here to index |
| `00:05:46.500 - 00:05:50.210` | into that array and we are saving the |
| `00:05:50.220 - 00:05:52.129` | element so that's the block number that |
| `00:05:52.139 - 00:05:54.230` | we're interested in and if it's null |
| `00:05:54.240 - 00:05:56.689` | then that block hasn't been allocated so |
| `00:05:56.699 - 00:06:00.230` | we have to call bialic again to allocate |
| `00:06:00.240 - 00:06:03.290` | a block and set it to zero and we will |
| `00:06:03.300 - 00:06:06.650` | store the block address of the freshly |
| `00:06:06.660 - 00:06:10.370` | allocated block into the array and we |
| `00:06:10.380 - 00:06:13.670` | need to update the indirect block if we |
| `00:06:13.680 - 00:06:15.230` | have modified it so that's where we have |
| `00:06:15.240 - 00:06:18.890` | the log right here but otherwise we |
| `00:06:18.900 - 00:06:20.570` | don't need to update the indirect block |
| `00:06:20.580 - 00:06:22.309` | we just use the address that we |
| `00:06:22.319 - 00:06:25.490` | retrieved and return that so here we are |
| `00:06:25.500 - 00:06:29.390` | releasing the indirect block or more |
| `00:06:29.400 - 00:06:30.710` | precisely releasing the buffer that |
| `00:06:30.720 - 00:06:33.830` | contains the block of indirect pointers |
| `00:06:33.840 - 00:06:37.309` | now uh you can see that we are modifying |
| `00:06:37.319 - 00:06:40.070` | the I node here okay we could be |
| `00:06:40.080 - 00:06:43.010` | modifying the array directly either here |
| `00:06:43.020 - 00:06:44.930` | or |
| `00:06:44.940 - 00:06:48.950` | um here and so if we are modifying it uh |
| `00:06:48.960 - 00:06:51.770` | we need to update the I node on disk so |
| `00:06:51.780 - 00:06:54.290` | uh that's the responsibility of a caller |
| `00:06:54.300 - 00:06:58.730` | of bmap so let's keep that in mind |
| `00:06:58.740 - 00:07:00.830` | at some point we're going to want to |
| `00:07:00.840 - 00:07:03.770` | transfer bytes from the file to memory |
| `00:07:03.780 - 00:07:05.749` | and that's the purpose of the read I |
| `00:07:05.759 - 00:07:08.150` | function so it's going to read data from |
| `00:07:08.160 - 00:07:10.790` | the file that's described by the inode |
| `00:07:10.800 - 00:07:13.790` | pointed to by IP |
| `00:07:13.800 - 00:07:16.309` | we're going to get the data from this |
| `00:07:16.319 - 00:07:18.230` | offset within the file and we're going |
| `00:07:18.240 - 00:07:20.930` | to transfer in bytes we're going to move |
| `00:07:20.940 - 00:07:23.029` | those into memory and the address that |
| `00:07:23.039 - 00:07:25.010` | we're going to move them to is given by |
| `00:07:25.020 - 00:07:26.469` | the destination |
| `00:07:26.479 - 00:07:29.089` | argument here that could either be a |
| `00:07:29.099 - 00:07:31.010` | virtual address or a physical address |
| `00:07:31.020 - 00:07:33.589` | and that's determined by this argument |
| `00:07:33.599 - 00:07:37.430` | here if this argument is a 1 then the |
| `00:07:37.440 - 00:07:40.010` | destination address is an address in the |
| `00:07:40.020 - 00:07:41.570` | virtual address space of the currently |
| `00:07:41.580 - 00:07:43.249` | executing process |
| `00:07:43.259 - 00:07:46.730` | if this is zero then destination is a |
| `00:07:46.740 - 00:07:48.170` | physical address in the kernel address |
| `00:07:48.180 - 00:07:50.450` | space |
| `00:07:50.460 - 00:07:53.390` | we begin with a couple of checks first |
| `00:07:53.400 - 00:07:56.210` | if the offset is beyond the end of the |
| `00:07:56.220 - 00:07:59.210` | file then we return zero by the way this |
| `00:07:59.220 - 00:08:01.249` | function Returns the number of bytes |
| `00:08:01.259 - 00:08:03.350` | successfully transferred |
| `00:08:03.360 - 00:08:05.930` | this test here is written in an odd way |
| `00:08:05.940 - 00:08:08.270` | if you subtract offset from both sides |
| `00:08:08.280 - 00:08:10.550` | you see that it's really just checking |
| `00:08:10.560 - 00:08:12.050` | to see whether the number of bytes that |
| `00:08:12.060 - 00:08:14.930` | we want to transfer is less than zero |
| `00:08:14.940 - 00:08:19.430` | and if it is again we return 0. |
| `00:08:19.440 - 00:08:23.089` | offset may be within the file but the |
| `00:08:23.099 - 00:08:25.249` | extent of the amount of data that we're |
| `00:08:25.259 - 00:08:26.869` | transferring may go beyond the end of |
| `00:08:26.879 - 00:08:29.510` | the file and if that's the case then we |
| `00:08:29.520 - 00:08:32.149` | need to reduce in so that we only |
| `00:08:32.159 - 00:08:34.130` | transfer as many bytes as the file has |
| `00:08:34.140 - 00:08:37.370` | so here we compute the number of bytes |
| `00:08:37.380 - 00:08:40.009` | between the offset and the end of the |
| `00:08:40.019 - 00:08:44.630` | file and that is our new in |
| `00:08:44.640 - 00:08:46.910` | so the invites that we want to transfer |
| `00:08:46.920 - 00:08:49.490` | can be spread over several blocks in the |
| `00:08:49.500 - 00:08:51.470` | file and we need to read each block |
| `00:08:51.480 - 00:08:53.509` | individually and get the bytes out of |
| `00:08:53.519 - 00:08:56.150` | that block and we'll so we'll call B |
| `00:08:56.160 - 00:08:58.310` | read a number of times and for each one |
| `00:08:58.320 - 00:09:01.190` | we'll transfer using this either copy |
| `00:09:01.200 - 00:09:02.509` | out function |
| `00:09:02.519 - 00:09:05.329` | so this Loop will transfer a number of |
| `00:09:05.339 - 00:09:10.430` | bytes each time and then it will finally |
| `00:09:10.440 - 00:09:13.490` | terminate and return the total number of |
| `00:09:13.500 - 00:09:16.670` | bytes transferred so we start total at |
| `00:09:16.680 - 00:09:20.810` | zero and we end when we transfer uh n |
| `00:09:20.820 - 00:09:23.389` | bytes and then we return the number of |
| `00:09:23.399 - 00:09:27.470` | bytes that we've transferred |
| `00:09:27.480 - 00:09:29.090` | so the first thing we need to do is |
| `00:09:29.100 - 00:09:31.970` | figure out for this particular chunk for |
| `00:09:31.980 - 00:09:36.230` | the next chunk where it is in the file |
| `00:09:36.240 - 00:09:39.470` | and where it is on the disk so the |
| `00:09:39.480 - 00:09:41.090` | current offset from which we're getting |
| `00:09:41.100 - 00:09:44.750` | data is given by offset and as we go |
| `00:09:44.760 - 00:09:46.730` | through we'll Advance offset and we'll |
| `00:09:46.740 - 00:09:49.430` | Advance the destination pointer as we do |
| `00:09:49.440 - 00:09:51.829` | each chunk we'll compute the number of |
| `00:09:51.839 - 00:09:53.570` | bytes we're going to transfer here |
| `00:09:53.580 - 00:09:57.889` | that's M and increment the |
| `00:09:57.899 - 00:10:00.590` | Source the offset within the file as |
| `00:10:00.600 - 00:10:03.650` | well as the destination pointer and the |
| `00:10:03.660 - 00:10:06.290` | number of bytes transferred so we take |
| `00:10:06.300 - 00:10:08.750` | the offset where we're at right now and |
| `00:10:08.760 - 00:10:10.490` | figure out what Block it's in so we |
| `00:10:10.500 - 00:10:12.410` | compute the block number that contains |
| `00:10:12.420 - 00:10:16.610` | offset right here and call bmap bmap |
| `00:10:16.620 - 00:10:19.190` | Will map this block number which is |
| `00:10:19.200 - 00:10:20.630` | relative to the beginning of the file |
| `00:10:20.640 - 00:10:24.530` | into a block number on the disk and then |
| `00:10:24.540 - 00:10:26.449` | we call B read to read that into a |
| `00:10:26.459 - 00:10:28.190` | buffer which is pointed to by this |
| `00:10:28.200 - 00:10:30.170` | buffer pointer here |
| `00:10:30.180 - 00:10:33.110` | now we figure out how many bytes we want |
| `00:10:33.120 - 00:10:36.530` | to transfer in the next step and so |
| `00:10:36.540 - 00:10:38.150` | um we don't want to go over the end of |
| `00:10:38.160 - 00:10:39.769` | this block we don't want to go past the |
| `00:10:39.779 - 00:10:41.389` | end of this block so that's what we do |
| `00:10:41.399 - 00:10:44.389` | here we take the offset and see how much |
| `00:10:44.399 - 00:10:46.850` | is left in this particular block with |
| `00:10:46.860 - 00:10:48.769` | this expression here |
| `00:10:48.779 - 00:10:51.230` | and we don't want to transfer bytes |
| `00:10:51.240 - 00:10:53.329` | beyond what we're supposed to transfer |
| `00:10:53.339 - 00:10:56.449` | so we look at how many bytes we've |
| `00:10:56.459 - 00:10:58.970` | transferred so far and subtract that |
| `00:10:58.980 - 00:11:00.350` | from M and that's how many bytes we have |
| `00:11:00.360 - 00:11:02.930` | left to transfer total and we take the |
| `00:11:02.940 - 00:11:04.790` | minimum of those two and that will be |
| `00:11:04.800 - 00:11:05.990` | the number of bytes we're going to |
| `00:11:06.000 - 00:11:07.250` | transfer |
| `00:11:07.260 - 00:11:11.569` | so then we call either copy out and we |
| `00:11:11.579 - 00:11:13.910` | cover that earlier but basically it's |
| `00:11:13.920 - 00:11:18.410` | passed a source address here |
| `00:11:18.420 - 00:11:20.750` | a number of bytes to copy and a |
| `00:11:20.760 - 00:11:22.790` | destination address and then a flag to |
| `00:11:22.800 - 00:11:24.110` | tell whether those |
| `00:11:24.120 - 00:11:26.810` | bytes will be copied into a user's |
| `00:11:26.820 - 00:11:29.930` | virtual address space or into the kernel |
| `00:11:29.940 - 00:11:32.090` | space into a physical memory so that's |
| `00:11:32.100 - 00:11:34.310` | just the parameter that we had up here |
| `00:11:34.320 - 00:11:38.509` | okay and we are copying M bytes and |
| `00:11:38.519 - 00:11:41.030` | where are we copying them from well |
| `00:11:41.040 - 00:11:43.790` | we're copying here's the where the data |
| `00:11:43.800 - 00:11:47.449` | is in the buffer and we look at the |
| `00:11:47.459 - 00:11:49.790` | offset and we figure out where that is |
| `00:11:49.800 - 00:11:53.269` | so we offset into the Buffer's data and |
| `00:11:53.279 - 00:11:56.090` | that's where we begin copying our M |
| `00:11:56.100 - 00:11:58.730` | bytes and destinations where we copy |
| `00:11:58.740 - 00:12:01.790` | them to and if anything goes wrong this |
| `00:12:01.800 - 00:12:04.310` | function will return a minus one and we |
| `00:12:04.320 - 00:12:06.769` | immediately abort that could be if the |
| `00:12:06.779 - 00:12:09.470` | destination address is not actually in |
| `00:12:09.480 - 00:12:11.629` | the user's virtual address space it's |
| `00:12:11.639 - 00:12:13.449` | not an allocated page |
| `00:12:13.459 - 00:12:15.650` | for example that could cause a problem |
| `00:12:15.660 - 00:12:18.110` | and if that's the case then we |
| `00:12:18.120 - 00:12:20.090` | immediately release the buffer and |
| `00:12:20.100 - 00:12:23.449` | return a minus one and break out of here |
| `00:12:23.459 - 00:12:25.490` | so we set total to minus one and then |
| `00:12:25.500 - 00:12:28.250` | would turn out but if the copy is okay |
| `00:12:28.260 - 00:12:31.190` | then we just continue we release the |
| `00:12:31.200 - 00:12:33.170` | buffer that we allocated here and then |
| `00:12:33.180 - 00:12:35.509` | iterate and do the next Chunk we just |
| `00:12:35.519 - 00:12:40.190` | copied M bytes so we add m to the total |
| `00:12:40.200 - 00:12:42.710` | bytes we've copied so far as well as |
| `00:12:42.720 - 00:12:45.470` | from The Source address within the file |
| `00:12:45.480 - 00:12:47.569` | that is the offset and the destination |
| `00:12:47.579 - 00:12:49.610` | address for the bytes are being copied |
| `00:12:49.620 - 00:12:51.110` | too |
| `00:12:51.120 - 00:12:52.970` | now one thing I want to point out is |
| `00:12:52.980 - 00:12:55.550` | we're calling bmap here and as I |
| `00:12:55.560 - 00:12:58.730` | mentioned earlier bmap can modify the |
| `00:12:58.740 - 00:13:04.009` | inode in memory if the data is not |
| `00:13:04.019 - 00:13:06.530` | allocated in the file it will allocate |
| `00:13:06.540 - 00:13:09.769` | it so it may allocate new blocks and add |
| `00:13:09.779 - 00:13:11.870` | them to the file and that will modify |
| `00:13:11.880 - 00:13:14.629` | the inode and it is the responsibility |
| `00:13:14.639 - 00:13:18.350` | of the caller of bmap to write that |
| `00:13:18.360 - 00:13:22.550` | modified inode back to the disk and that |
| `00:13:22.560 - 00:13:24.949` | doesn't happen here and I think that is |
| `00:13:24.959 - 00:13:28.550` | a bona fide bug in this code but you |
| `00:13:28.560 - 00:13:30.230` | know maybe maybe there's something I |
| `00:13:30.240 - 00:13:31.550` | don't see |
| `00:13:31.560 - 00:13:33.769` | yes there definitely is something that I |
| `00:13:33.779 - 00:13:36.110` | didn't see at first after looking at |
| `00:13:36.120 - 00:13:38.090` | this code I've come to believe that it's |
| `00:13:38.100 - 00:13:40.550` | just fine as it is |
| `00:13:40.560 - 00:13:44.290` | in Unix we have the F seek system call |
| `00:13:44.300 - 00:13:47.449` | and if this is a file here you can use |
| `00:13:47.459 - 00:13:51.530` | SC to seek to a certain point beyond the |
| `00:13:51.540 - 00:13:53.329` | current end of the file and then you can |
| `00:13:53.339 - 00:13:55.009` | do a write |
| `00:13:55.019 - 00:13:58.790` | the posit specification says that the |
| `00:13:58.800 - 00:14:01.670` | bytes in between here that were never |
| `00:14:01.680 - 00:14:04.610` | actually written will be initialized or |
| `00:14:04.620 - 00:14:07.370` | assumed to be zero so that if later you |
| `00:14:07.380 - 00:14:10.009` | go back and you do a read in this region |
| `00:14:10.019 - 00:14:12.889` | here the read system call should return |
| `00:14:12.899 - 00:14:17.030` | the zero bytes that were supposed to be |
| `00:14:17.040 - 00:14:18.050` | there |
| `00:14:18.060 - 00:14:21.290` | I made the assumption that xv6 works the |
| `00:14:21.300 - 00:14:23.090` | same way and that you could have these |
| `00:14:23.100 - 00:14:26.509` | uh gaps like this but in fact that's not |
| `00:14:26.519 - 00:14:27.530` | the case |
| `00:14:27.540 - 00:14:31.610` | in xv6 there is no fseq system call so |
| `00:14:31.620 - 00:14:33.290` | it's impossible to do something like |
| `00:14:33.300 - 00:14:35.750` | this you cannot start writing beyond the |
| `00:14:35.760 - 00:14:39.230` | end of the file in fact with xv6 writing |
| `00:14:39.240 - 00:14:42.710` | will always start at position zero I |
| `00:14:42.720 - 00:14:44.449` | suppose that if you don't want to start |
| `00:14:44.459 - 00:14:47.389` | writing at position zero you will have |
| `00:14:47.399 - 00:14:48.829` | to do a bunch of reads to get to |
| `00:14:48.839 - 00:14:50.389` | whichever position you'd like to start |
| `00:14:50.399 - 00:14:52.370` | writing at |
| `00:14:52.380 - 00:14:55.009` | so with actually six |
| `00:14:55.019 - 00:14:57.650` | the blocks in the file are always |
| `00:14:57.660 - 00:15:00.230` | allocated so let's assume that we have a |
| `00:15:00.240 - 00:15:03.170` | file and it goes from this point up to |
| `00:15:03.180 - 00:15:06.710` | some size and so here's the file in blue |
| `00:15:06.720 - 00:15:08.750` | and what this means is that all the |
| `00:15:08.760 - 00:15:11.150` | blocks and each one of these is supposed |
| `00:15:11.160 - 00:15:14.150` | to be a block will be allocated and the |
| `00:15:14.160 - 00:15:16.670` | last block might not be fully used but |
| `00:15:16.680 - 00:15:19.189` | all the blocks in here will be allocated |
| `00:15:19.199 - 00:15:22.490` | and none of the blocks Beyond this point |
| `00:15:22.500 - 00:15:26.269` | will be allocated so that means that the |
| `00:15:26.279 - 00:15:29.030` | read-i system call when it calls bmap |
| `00:15:29.040 - 00:15:32.150` | will only be reading blocks that are |
| `00:15:32.160 - 00:15:34.670` | already in the file and that means there |
| `00:15:34.680 - 00:15:37.189` | will never be an update to the inode so |
| `00:15:37.199 - 00:15:39.590` | there's no reason or no necessity to |
| `00:15:39.600 - 00:15:42.050` | call the I update function |
| `00:15:42.060 - 00:15:45.170` | like there is in right as we will see in |
| `00:15:45.180 - 00:15:46.670` | a second |
| `00:15:46.680 - 00:15:49.189` | this uh points out that a lot of times |
| `00:15:49.199 - 00:15:51.530` | we make assumptions about the code that |
| `00:15:51.540 - 00:15:54.230` | we are either reading or writing and |
| `00:15:54.240 - 00:15:56.810` | these assumptions are often below the |
| `00:15:56.820 - 00:15:59.749` | level of Consciousness we have these |
| `00:15:59.759 - 00:16:02.090` | assumptions in the back of our mind but |
| `00:16:02.100 - 00:16:05.629` | they may not be clearly articulated |
| `00:16:05.639 - 00:16:08.930` | if we want to prove in a mathematically |
| `00:16:08.940 - 00:16:12.170` | strict way that the code is correct then |
| `00:16:12.180 - 00:16:14.689` | we may be required to make all of our |
| `00:16:14.699 - 00:16:19.189` | assumptions explicit and write them down |
| `00:16:19.199 - 00:16:22.550` | doing that proving a program is correct |
| `00:16:22.560 - 00:16:24.650` | and making all these assumptions |
| `00:16:24.660 - 00:16:27.050` | explicit is an excellent way to find |
| `00:16:27.060 - 00:16:30.650` | bugs but with the xv6 operating system |
| `00:16:30.660 - 00:16:33.230` | kernel we are not going to go into that |
| `00:16:33.240 - 00:16:35.090` | level of analysis |
| `00:16:35.100 - 00:16:37.850` | okay so let's move on to the right eye |
| `00:16:37.860 - 00:16:41.030` | function |
| `00:16:41.040 - 00:16:44.569` | to write data to a file we have the |
| `00:16:44.579 - 00:16:47.329` | right eye function so we will write data |
| `00:16:47.339 - 00:16:49.370` | to the file that's described by this |
| `00:16:49.380 - 00:16:52.610` | inode and the caller must hold a lock on |
| `00:16:52.620 - 00:16:54.170` | that I node |
| `00:16:54.180 - 00:16:57.170` | the data is coming from |
| `00:16:57.180 - 00:17:00.230` | memory at the location given by this |
| `00:17:00.240 - 00:17:04.549` | argument source and that can either be a |
| `00:17:04.559 - 00:17:06.770` | virtual address in the address space of |
| `00:17:06.780 - 00:17:09.169` | the currently running process or it can |
| `00:17:09.179 - 00:17:11.750` | be a physical address in kernel space as |
| `00:17:11.760 - 00:17:14.590` | determined by this argument here |
| `00:17:14.600 - 00:17:18.169` | we are placing the data in the file at |
| `00:17:18.179 - 00:17:20.630` | the location given by this offset here |
| `00:17:20.640 - 00:17:24.230` | and we'll be copying n bytes this |
| `00:17:24.240 - 00:17:26.390` | function will return the number of bytes |
| `00:17:26.400 - 00:17:28.610` | successfully written and it might be |
| `00:17:28.620 - 00:17:31.310` | less than the requested in if there's an |
| `00:17:31.320 - 00:17:33.289` | error of some kind |
| `00:17:33.299 - 00:17:35.450` | so it's very similar the code is very |
| `00:17:35.460 - 00:17:38.690` | similar to the read eye function we |
| `00:17:38.700 - 00:17:40.730` | start off by asking whether the offset |
| `00:17:40.740 - 00:17:43.130` | is beyond the end of the file and if so |
| `00:17:43.140 - 00:17:44.150` | we return |
| `00:17:44.160 - 00:17:45.830` | -1 in this case |
| `00:17:45.840 - 00:17:49.010` | and we also ask whether the number of |
| `00:17:49.020 - 00:17:50.990` | bytes to be transferred is less than |
| `00:17:51.000 - 00:17:53.029` | zero and again that's considered to be |
| `00:17:53.039 - 00:17:57.289` | an error so we return -1 and we also ask |
| `00:17:57.299 - 00:18:01.549` | whether we will be going beyond the |
| `00:18:01.559 - 00:18:04.610` | maximum file size so max file is the |
| `00:18:04.620 - 00:18:06.830` | maximum number of blocks in a file times |
| `00:18:06.840 - 00:18:09.830` | the block size and if offset plus n is |
| `00:18:09.840 - 00:18:12.590` | greater than that then we've got to |
| `00:18:12.600 - 00:18:15.169` | return an error indication |
| `00:18:15.179 - 00:18:18.470` | the loop here is roughly the same as we |
| `00:18:18.480 - 00:18:21.409` | saw in read I we're going to break the |
| `00:18:21.419 - 00:18:25.070` | transfer into a number of chunks of size |
| `00:18:25.080 - 00:18:26.630` | m |
| `00:18:26.640 - 00:18:29.090` | and so we start with the total number of |
| `00:18:29.100 - 00:18:32.029` | bytes transferred so far that's zero and |
| `00:18:32.039 - 00:18:34.310` | we'll stop when we transferred all n |
| `00:18:34.320 - 00:18:37.549` | bytes and we'll ultimately return this |
| `00:18:37.559 - 00:18:38.930` | total number |
| `00:18:38.940 - 00:18:40.610` | and we compute the number of bytes we're |
| `00:18:40.620 - 00:18:44.090` | going to transfer as M and in each |
| `00:18:44.100 - 00:18:47.510` | iteration of the loop we'll be adding m |
| `00:18:47.520 - 00:18:50.570` | to the offset in the file that we're |
| `00:18:50.580 - 00:18:52.970` | writing to as well as the source address |
| `00:18:52.980 - 00:18:55.909` | where we're getting the bytes from and |
| `00:18:55.919 - 00:18:57.950` | we'll increment the total number of |
| `00:18:57.960 - 00:19:01.070` | bytes that we've written so far by m and |
| `00:19:01.080 - 00:19:02.750` | again this is exactly the same as in |
| `00:19:02.760 - 00:19:05.029` | read I when you take the current offset |
| `00:19:05.039 - 00:19:06.650` | in the file figure out what Block it's |
| `00:19:06.660 - 00:19:09.350` | in Call bmap to determine the block |
| `00:19:09.360 - 00:19:12.230` | number on disk and then call B read to |
| `00:19:12.240 - 00:19:15.710` | get that block into a buffer 0.2 by BP |
| `00:19:15.720 - 00:19:18.070` | then we compute M |
| `00:19:18.080 - 00:19:22.490` | here we are limiting in if we are going |
| `00:19:22.500 - 00:19:24.169` | past the total number of bytes we need |
| `00:19:24.179 - 00:19:29.690` | to write then we stop there and uh so n |
| `00:19:29.700 - 00:19:32.090` | minus total is the number of bytes left |
| `00:19:32.100 - 00:19:33.830` | to right so we don't want to write any |
| `00:19:33.840 - 00:19:36.590` | more bytes than that and we also don't |
| `00:19:36.600 - 00:19:38.690` | want to go beyond the end of this block |
| `00:19:38.700 - 00:19:41.090` | so here we compute how many bytes are |
| `00:19:41.100 - 00:19:43.690` | left from offset |
| `00:19:43.700 - 00:19:46.970` | in this particular block and then we use |
| `00:19:46.980 - 00:19:50.390` | the either copy in function which will |
| `00:19:50.400 - 00:19:52.630` | copy |
| `00:19:52.640 - 00:19:55.970` | from this location in memory source |
| `00:19:55.980 - 00:19:58.310` | which is either in Virtual address space |
| `00:19:58.320 - 00:20:01.730` | or kernel outer space as given by this |
| `00:20:01.740 - 00:20:03.289` | parameter here |
| `00:20:03.299 - 00:20:07.970` | and we'll copy n bytes sorry M bytes and |
| `00:20:07.980 - 00:20:10.370` | where will we be copying them to well |
| `00:20:10.380 - 00:20:13.490` | we've read in the buffer that contains |
| `00:20:13.500 - 00:20:15.529` | the range of bytes that we are going to |
| `00:20:15.539 - 00:20:18.350` | be writing to and so here we compute |
| `00:20:18.360 - 00:20:21.350` | where that is so here's the the data in |
| `00:20:21.360 - 00:20:23.690` | the buffer and here's the offset within |
| `00:20:23.700 - 00:20:25.730` | that data and that's where we're moving |
| `00:20:25.740 - 00:20:30.049` | data to and after we copy the data from |
| `00:20:30.059 - 00:20:33.230` | memory to the buffer we need to write |
| `00:20:33.240 - 00:20:36.289` | this buffer back to memory sorry back to |
| `00:20:36.299 - 00:20:40.430` | the file so here we invoke the log right |
| `00:20:40.440 - 00:20:42.710` | function to do that and then we release |
| `00:20:42.720 - 00:20:47.149` | the buffer if the either copy in |
| `00:20:47.159 - 00:20:49.930` | function has a problem it will return -1 |
| `00:20:49.940 - 00:20:54.529` | and we then release the buffer and break |
| `00:20:54.539 - 00:20:59.270` | now we need to update the size field so |
| `00:20:59.280 - 00:21:01.370` | um here we are |
| `00:21:01.380 - 00:21:06.110` | updating the size attribute of this file |
| `00:21:06.120 - 00:21:10.490` | if the offset that we're at now after we |
| `00:21:10.500 - 00:21:12.950` | end this Loop exceeds the current size |
| `00:21:12.960 - 00:21:15.049` | of the file then we need to grow the |
| `00:21:15.059 - 00:21:17.630` | file or at least make a note that the |
| `00:21:17.640 - 00:21:21.230` | file has already grown bmap will be the |
| `00:21:21.240 - 00:21:23.570` | function that actually adds records or |
| `00:21:23.580 - 00:21:25.610` | adds blocks to the file |
| `00:21:25.620 - 00:21:27.830` | so uh |
| `00:21:27.840 - 00:21:31.430` | write the inode back to the disk even if |
| `00:21:31.440 - 00:21:33.529` | the size didn't change because the loop |
| `00:21:33.539 - 00:21:35.750` | above might have called bmap well it did |
| `00:21:35.760 - 00:21:39.049` | call bmap and that bmap might have added |
| `00:21:39.059 - 00:21:42.710` | a new block to the file and therefore |
| `00:21:42.720 - 00:21:46.370` | modified the array of block pointers so |
| `00:21:46.380 - 00:21:49.250` | we do call eye update here and finally |
| `00:21:49.260 - 00:21:51.890` | we return the total number of bytes |
| `00:21:51.900 - 00:21:54.529` | transferred |
| `00:21:54.539 - 00:21:57.049` | next let's take a look at the directory |
| `00:21:57.059 - 00:22:00.110` | lookup function it's passive directory |
| `00:22:00.120 - 00:22:03.409` | or more precisely a pointer to a cached |
| `00:22:03.419 - 00:22:05.990` | copy of the inode for that directory |
| `00:22:06.000 - 00:22:09.350` | file and a pointer to a string and it |
| `00:22:09.360 - 00:22:12.289` | looks that name up in the directory and |
| `00:22:12.299 - 00:22:15.409` | if it defines it it allocates a cached |
| `00:22:15.419 - 00:22:17.450` | version of the inode for that file and |
| `00:22:17.460 - 00:22:19.730` | returns a pointer to that |
| `00:22:19.740 - 00:22:21.830` | it also has the capability of returning |
| `00:22:21.840 - 00:22:24.590` | the offset within the directory at which |
| `00:22:24.600 - 00:22:27.350` | it found this name and that could be |
| `00:22:27.360 - 00:22:29.630` | useful if for example we want to delete |
| `00:22:29.640 - 00:22:32.210` | an entry from a directory |
| `00:22:32.220 - 00:22:34.070` | so let me remind you what the |
| `00:22:34.080 - 00:22:35.750` | directories look like |
| `00:22:35.760 - 00:22:38.029` | we have this directory entry structure |
| `00:22:38.039 - 00:22:41.090` | that's 16 bytes in length it has two |
| `00:22:41.100 - 00:22:44.210` | Fields the inum field is only two bytes |
| `00:22:44.220 - 00:22:47.870` | and it has the I number of the file and |
| `00:22:47.880 - 00:22:49.789` | then we have 14 bytes for the name of |
| `00:22:49.799 - 00:22:52.490` | the file if the name is shorter than 14 |
| `00:22:52.500 - 00:22:55.490` | bytes it will be followed by a null byte |
| `00:22:55.500 - 00:22:58.310` | and a directory is just a file that |
| `00:22:58.320 - 00:23:00.110` | contains an array of these |
| `00:23:00.120 - 00:23:03.350` | so here's an example directory some of |
| `00:23:03.360 - 00:23:05.750` | the entries will have an eye number of |
| `00:23:05.760 - 00:23:07.850` | zero and those are essentially unused |
| `00:23:07.860 - 00:23:10.370` | entries in the directory so this |
| `00:23:10.380 - 00:23:12.890` | directory seems to have three files in |
| `00:23:12.900 - 00:23:16.130` | it and so we have the file name if it's |
| `00:23:16.140 - 00:23:18.890` | shorter than 14 characters it is |
| `00:23:18.900 - 00:23:21.590` | followed by a null byte and if it is 14 |
| `00:23:21.600 - 00:23:23.750` | characters then there is no null byte |
| `00:23:23.760 - 00:23:25.850` | but in any case there's the I number |
| `00:23:25.860 - 00:23:28.070` | that's associated with these names |
| `00:23:28.080 - 00:23:29.690` | for the ones that are zero we don't |
| `00:23:29.700 - 00:23:31.490` | really care about what characters we |
| `00:23:31.500 - 00:23:33.289` | have for the name |
| `00:23:33.299 - 00:23:36.890` | Okay so what do we do in this function |
| `00:23:36.900 - 00:23:39.890` | first of all we check the file that were |
| `00:23:39.900 - 00:23:42.110` | passed the directory to make sure that |
| `00:23:42.120 - 00:23:44.930` | its type is in fact a directory and if |
| `00:23:44.940 - 00:23:47.149` | not there's a serious problem |
| `00:23:47.159 - 00:23:49.190` | then we're going to go through the file |
| `00:23:49.200 - 00:23:53.029` | so offset will start at zero and it will |
| `00:23:53.039 - 00:23:54.649` | be incremented by the size of these |
| `00:23:54.659 - 00:23:56.750` | directory entries so we have this local |
| `00:23:56.760 - 00:24:00.529` | variable called d e directory entry and |
| `00:24:00.539 - 00:24:04.490` | that's a 16 byte value and so we're |
| `00:24:04.500 - 00:24:07.310` | going to increment the offset in units |
| `00:24:07.320 - 00:24:09.890` | of 16. so here here here |
| `00:24:09.900 - 00:24:13.669` | and we're going to end when we get to |
| `00:24:13.679 - 00:24:15.649` | the end of this file the size of the |
| `00:24:15.659 - 00:24:17.630` | directory file itself |
| `00:24:17.640 - 00:24:19.789` | and for each iteration of the loop what |
| `00:24:19.799 - 00:24:21.769` | we're going to do is we're going to call |
| `00:24:21.779 - 00:24:24.230` | the read-eye function so we're going to |
| `00:24:24.240 - 00:24:27.470` | read from this directory file okay and |
| `00:24:27.480 - 00:24:28.789` | where are we going to read we're going |
| `00:24:28.799 - 00:24:31.669` | to read from this offset and we're going |
| `00:24:31.679 - 00:24:33.830` | to read well it's 16 the directory entry |
| `00:24:33.840 - 00:24:37.370` | is 16. we're going to read 16 bytes and |
| `00:24:37.380 - 00:24:39.710` | we're going to place those bytes in this |
| `00:24:39.720 - 00:24:42.110` | d e variable here |
| `00:24:42.120 - 00:24:45.110` | and we make sure that we've got 16 bytes |
| `00:24:45.120 - 00:24:47.090` | and if we didn't well something's really |
| `00:24:47.100 - 00:24:49.190` | wrong so we panic |
| `00:24:49.200 - 00:24:52.190` | if the eye number of this directory |
| `00:24:52.200 - 00:24:55.490` | entry is zero then we skip then go to |
| `00:24:55.500 - 00:24:58.250` | the next one with a continue otherwise |
| `00:24:58.260 - 00:25:00.710` | we compare the name that were passed |
| `00:25:00.720 - 00:25:04.250` | okay this string here with the name in |
| `00:25:04.260 - 00:25:06.409` | the directory entry and see whether they |
| `00:25:06.419 - 00:25:08.330` | are equal so at this point let me look |
| `00:25:08.340 - 00:25:11.149` | at this name compare okay that's a |
| `00:25:11.159 - 00:25:13.549` | little function here that is past two |
| `00:25:13.559 - 00:25:16.130` | strings and it uses string and compare |
| `00:25:16.140 - 00:25:19.490` | to compare up to 14 characters well it |
| `00:25:19.500 - 00:25:23.149` | Compares up to the first null byte and |
| `00:25:23.159 - 00:25:26.090` | in any case no more than 14 bytes |
| `00:25:26.100 - 00:25:31.789` | and so if the name is equal then we have |
| `00:25:31.799 - 00:25:33.769` | found it okay the two names are equal |
| `00:25:33.779 - 00:25:36.769` | then we have uh the entry that we're |
| `00:25:36.779 - 00:25:39.230` | looking for and so we grab the I number |
| `00:25:39.240 - 00:25:41.269` | that's associated with it |
| `00:25:41.279 - 00:25:44.269` | also if this P off |
| `00:25:44.279 - 00:25:47.570` | argument is non-null then we store the |
| `00:25:47.580 - 00:25:50.390` | current offset of this directory entry |
| `00:25:50.400 - 00:25:53.990` | in the variable that's pointed to here |
| `00:25:54.000 - 00:25:56.690` | and once we've gotten the I number for |
| `00:25:56.700 - 00:25:59.630` | this entry we then call iget |
| `00:25:59.640 - 00:26:03.890` | recall that I get will uh allocate an |
| `00:26:03.900 - 00:26:08.510` | inode in the cache of inodes and it will |
| `00:26:08.520 - 00:26:09.950` | initialize it with the appropriate |
| `00:26:09.960 - 00:26:13.490` | device and I number and from then on we |
| `00:26:13.500 - 00:26:15.350` | can lock it if we want to but at least |
| `00:26:15.360 - 00:26:17.810` | we've got the pointer to we've got a |
| `00:26:17.820 - 00:26:21.049` | pointer to the inode cached in memory |
| `00:26:21.059 - 00:26:23.210` | if this Loop goes all the way through |
| `00:26:23.220 - 00:26:26.090` | and ultimately reaches the size of the |
| `00:26:26.100 - 00:26:28.970` | directory file and still hasn't found a |
| `00:26:28.980 - 00:26:32.330` | match then we return 0 to indicate that |
| `00:26:32.340 - 00:26:35.110` | we haven't found anything |
| `00:26:35.120 - 00:26:37.789` | so next we have the directory link |
| `00:26:37.799 - 00:26:40.070` | function which is used to add an entry |
| `00:26:40.080 - 00:26:43.549` | to a directory it's passed DP which is a |
| `00:26:43.559 - 00:26:45.110` | pointer to the inode for the directory |
| `00:26:45.120 - 00:26:48.049` | as well as the name and I number to be |
| `00:26:48.059 - 00:26:51.649` | added to the directory |
| `00:26:51.659 - 00:26:54.169` | it will return 0 if everything's okay |
| `00:26:54.179 - 00:26:56.810` | and minus one if there's a problem |
| `00:26:56.820 - 00:26:59.090` | the first thing it does is it calls |
| `00:26:59.100 - 00:27:00.830` | directory lookup to determine whether |
| `00:27:00.840 - 00:27:03.769` | this name is already in the directory if |
| `00:27:03.779 - 00:27:06.110` | it finds something then it will set IP |
| `00:27:06.120 - 00:27:10.250` | to point to the inode for that file it |
| `00:27:10.260 - 00:27:12.590` | will call iget to do that within |
| `00:27:12.600 - 00:27:15.169` | directory lookup if that's the case then |
| `00:27:15.179 - 00:27:17.269` | well that's a problem we need to return |
| `00:27:17.279 - 00:27:19.789` | minus one but we are not interested in |
| `00:27:19.799 - 00:27:21.950` | the file that we've already found so we |
| `00:27:21.960 - 00:27:26.029` | call I put to undo the I get if you will |
| `00:27:26.039 - 00:27:27.769` | then we look for an empty directory |
| `00:27:27.779 - 00:27:31.850` | entry so we're going to look through the |
| `00:27:31.860 - 00:27:34.610` | directory file starting with offset 0 |
| `00:27:34.620 - 00:27:36.830` | and incrementing by the size of these |
| `00:27:36.840 - 00:27:39.529` | directory entries and we're going to |
| `00:27:39.539 - 00:27:42.350` | stop when we get to the size of the file |
| `00:27:42.360 - 00:27:44.330` | after we've looked at them all |
| `00:27:44.340 - 00:27:49.630` | and for each offset we will call reduy |
| `00:27:49.640 - 00:27:53.990` | to get from the directory file uh 16 |
| `00:27:54.000 - 00:27:56.389` | bytes and we'll transfer it from the |
| `00:27:56.399 - 00:27:58.850` | current offset within the file to this |
| `00:27:58.860 - 00:28:01.190` | de variable this directory entry |
| `00:28:01.200 - 00:28:03.350` | variable here is a local variable of 16 |
| `00:28:03.360 - 00:28:07.850` | bytes and that's in physical memory not |
| `00:28:07.860 - 00:28:09.950` | virtual address space so that's the flag |
| `00:28:09.960 - 00:28:13.430` | of zero here for that and if for some |
| `00:28:13.440 - 00:28:15.529` | reason we're unable to transfer the data |
| `00:28:15.539 - 00:28:19.070` | unable to transfer a full 16 bytes then |
| `00:28:19.080 - 00:28:20.570` | there's something dreadfully to matter |
| `00:28:20.580 - 00:28:23.090` | and we panic |
| `00:28:23.100 - 00:28:26.149` | um but otherwise we then check the |
| `00:28:26.159 - 00:28:28.850` | directory entries I number to determine |
| `00:28:28.860 - 00:28:31.610` | whether it's free if it's zero then we |
| `00:28:31.620 - 00:28:33.710` | found a spot in the directory that we |
| `00:28:33.720 - 00:28:36.370` | can use and we break with that offset |
| `00:28:36.380 - 00:28:38.630` | otherwise we go all the way up to the |
| `00:28:38.640 - 00:28:41.690` | end of the file and offset will be equal |
| `00:28:41.700 - 00:28:45.350` | to the size of the file because the uh |
| `00:28:45.360 - 00:28:47.870` | the size of the directories is a |
| `00:28:47.880 - 00:28:52.430` | multiple of 16. and uh in either case we |
| `00:28:52.440 - 00:28:54.769` | are now ready to create the direct |
| `00:28:54.779 - 00:28:58.010` | reentry and write it to the file so here |
| `00:28:58.020 - 00:29:00.409` | we are copying the name |
| `00:29:00.419 - 00:29:04.250` | into the directory entry variable and we |
| `00:29:04.260 - 00:29:08.630` | copy it up to 14 characters and we copy |
| `00:29:08.640 - 00:29:11.090` | the I number into the directory entry |
| `00:29:11.100 - 00:29:14.930` | and then we write that directory entry |
| `00:29:14.940 - 00:29:17.570` | so uh we're writing it |
| `00:29:17.580 - 00:29:19.909` | here's where we're writing |
| `00:29:19.919 - 00:29:24.769` | uh from and this is a address in kernel |
| `00:29:24.779 - 00:29:27.049` | space so the flag is zero and we're |
| `00:29:27.059 - 00:29:29.510` | writing to the offset this could be the |
| `00:29:29.520 - 00:29:33.409` | offset of an entry that is unused or it |
| `00:29:33.419 - 00:29:36.350` | could be an offset beyond the end of the |
| `00:29:36.360 - 00:29:39.710` | file and it doesn't matter which this |
| `00:29:39.720 - 00:29:42.950` | will grow the file if necessary and so |
| `00:29:42.960 - 00:29:45.110` | we write 16 bytes that's the size of the |
| `00:29:45.120 - 00:29:46.730` | directory entry if there's anything |
| `00:29:46.740 - 00:29:49.310` | wrong we panic but otherwise we're done |
| `00:29:49.320 - 00:29:54.230` | and we return 0 To indicate success |
| `00:29:54.240 - 00:29:56.570` | in order to open a file we need to be |
| `00:29:56.580 - 00:29:58.850` | able to deal with path names |
| `00:29:58.860 - 00:30:01.070` | the next function we want to look at is |
| `00:30:01.080 - 00:30:03.769` | called skip element this is purely a |
| `00:30:03.779 - 00:30:06.049` | string processing function and helps us |
| `00:30:06.059 - 00:30:08.570` | parse a path name it doesn't involve |
| `00:30:08.580 - 00:30:10.610` | inodes at all |
| `00:30:10.620 - 00:30:13.310` | it's passed a pointer to a string |
| `00:30:13.320 - 00:30:15.289` | containing a path name such as this one |
| `00:30:15.299 - 00:30:17.870` | right here and it will identify the |
| `00:30:17.880 - 00:30:21.529` | first file name in that path and Advance |
| `00:30:21.539 - 00:30:24.169` | the pointer to the second file name and |
| `00:30:24.179 - 00:30:26.269` | it will return a pointer to the second |
| `00:30:26.279 - 00:30:27.590` | file name |
| `00:30:27.600 - 00:30:30.649` | it will also save the first file name so |
| `00:30:30.659 - 00:30:33.289` | the second argument is a pointer to a 14 |
| `00:30:33.299 - 00:30:35.810` | byte area remember that file names can |
| `00:30:35.820 - 00:30:38.029` | be at most 14 characters and it will |
| `00:30:38.039 - 00:30:41.389` | save the first component in that area |
| `00:30:41.399 - 00:30:44.330` | if there is no file name in The Path |
| `00:30:44.340 - 00:30:47.570` | then it will return null |
| `00:30:47.580 - 00:30:49.549` | okay so let's take a look at the code |
| `00:30:49.559 - 00:30:52.370` | here |
| `00:30:52.380 - 00:30:54.710` | one thing I want to mention is that it |
| `00:30:54.720 - 00:30:59.269` | ignores leading slashes and if there are |
| `00:30:59.279 - 00:31:01.549` | multiple slashes separating the file |
| `00:31:01.559 - 00:31:04.130` | names then it ignores that and treats |
| `00:31:04.140 - 00:31:06.889` | them just as one |
| `00:31:06.899 - 00:31:09.649` | okay so here we're past a pointer to the |
| `00:31:09.659 - 00:31:12.710` | path and a pointer to the 14 byte area |
| `00:31:12.720 - 00:31:15.049` | to say the first file name |
| `00:31:15.059 - 00:31:18.169` | So It Begins by skipping past any |
| `00:31:18.179 - 00:31:20.509` | leading slashes and ignoring them |
| `00:31:20.519 - 00:31:23.330` | and then if there's nothing found that |
| `00:31:23.340 - 00:31:25.310` | is if we're positioned at the null byte |
| `00:31:25.320 - 00:31:27.110` | at the end of the string then there is |
| `00:31:27.120 - 00:31:29.389` | no file name in The Path so we just |
| `00:31:29.399 - 00:31:31.789` | returned zero |
| `00:31:31.799 - 00:31:34.070` | now we're positioned at the beginning of |
| `00:31:34.080 - 00:31:35.990` | the first file name so we save a pointer |
| `00:31:36.000 - 00:31:38.870` | to that and in this while loop here we |
| `00:31:38.880 - 00:31:40.370` | advance our pointer |
| `00:31:40.380 - 00:31:43.009` | to whatever comes after that file name |
| `00:31:43.019 - 00:31:47.149` | so we search for the next slash or the |
| `00:31:47.159 - 00:31:49.669` | next null character and stop when we hit |
| `00:31:49.679 - 00:31:51.950` | something either of those |
| `00:31:51.960 - 00:31:54.230` | and at that point we know where the |
| `00:31:54.240 - 00:31:56.269` | first file name begins and where it ends |
| `00:31:56.279 - 00:31:58.370` | so we can compute its length |
| `00:31:58.380 - 00:32:00.110` | now we're going to move it into this |
| `00:32:00.120 - 00:32:03.350` | name area okay that that's our second |
| `00:32:03.360 - 00:32:05.630` | argument here and we need to check to |
| `00:32:05.640 - 00:32:08.090` | see whether it's greater than 14 uh is |
| `00:32:08.100 - 00:32:11.750` | if it's 14 or greater than 14 |
| `00:32:11.760 - 00:32:13.909` | bytes in length then we don't need to |
| `00:32:13.919 - 00:32:15.889` | worry about the null we just move it |
| `00:32:15.899 - 00:32:20.090` | into the name field and uh directory |
| `00:32:20.100 - 00:32:22.430` | size is this 14 constant |
| `00:32:22.440 - 00:32:25.970` | uh if it is uh 13 or fewer characters we |
| `00:32:25.980 - 00:32:27.710` | need to add a null character after the |
| `00:32:27.720 - 00:32:31.009` | end of it so here we do it that way |
| `00:32:31.019 - 00:32:34.610` | and uh finally we advance the pointer to |
| `00:32:34.620 - 00:32:37.310` | the next thing beyond the slashes so if |
| `00:32:37.320 - 00:32:39.950` | we ended pointing to a slash then we |
| `00:32:39.960 - 00:32:43.070` | advance to the second file name or the |
| `00:32:43.080 - 00:32:46.549` | end of the string and uh return the |
| `00:32:46.559 - 00:32:49.730` | pointer the results |
| `00:32:49.740 - 00:32:52.009` | next I want to look at the name I |
| `00:32:52.019 - 00:32:54.169` | function this is sort of the culmination |
| `00:32:54.179 - 00:32:55.490` | of all the functions we've been looking |
| `00:32:55.500 - 00:32:58.549` | at name I is passed a pointer to a path |
| `00:32:58.559 - 00:33:01.850` | and it locates that file on disk and |
| `00:33:01.860 - 00:33:04.430` | returns a pointer to the inode for that |
| `00:33:04.440 - 00:33:05.630` | file |
| `00:33:05.640 - 00:33:07.970` | and if there's any problem such as the |
| `00:33:07.980 - 00:33:10.009` | file is not found or the path name is no |
| `00:33:10.019 - 00:33:13.009` | good then it returns no |
| `00:33:13.019 - 00:33:16.370` | the actual work is done by this name X |
| `00:33:16.380 - 00:33:19.070` | function there's another function name I |
| `00:33:19.080 - 00:33:21.350` | parent which is very similar to name I |
| `00:33:21.360 - 00:33:24.649` | except it stops one level earlier and |
| `00:33:24.659 - 00:33:26.990` | both the name I and name my parent call |
| `00:33:27.000 - 00:33:30.710` | namex to do the work so let's take a |
| `00:33:30.720 - 00:33:36.590` | look at name I and name I parent here |
| `00:33:36.600 - 00:33:40.850` | okay we're going so uh name I calls |
| `00:33:40.860 - 00:33:44.509` | namex and name my parent calls name X |
| `00:33:44.519 - 00:33:46.970` | and the only difference is they pass a |
| `00:33:46.980 - 00:33:50.210` | flag to say which is involved here |
| `00:33:50.220 - 00:33:54.889` | now name X requires a 14 byte field a 14 |
| `00:33:54.899 - 00:33:57.590` | byte memory area in which to save each |
| `00:33:57.600 - 00:34:01.610` | file name in The Path and so uh in the |
| `00:34:01.620 - 00:34:03.590` | case of name I parent we're trying to |
| `00:34:03.600 - 00:34:06.830` | extract that so it's passed in but for |
| `00:34:06.840 - 00:34:09.290` | name I we have a local variable that's |
| `00:34:09.300 - 00:34:11.690` | the 14 byte field and we're passing in |
| `00:34:11.700 - 00:34:14.930` | the address of that variable |
| `00:34:14.940 - 00:34:20.210` | okay so let's uh take a look at name X |
| `00:34:20.220 - 00:34:24.349` | it is past a pointer to the path and |
| `00:34:24.359 - 00:34:27.349` | then this flag name I parent if this |
| `00:34:27.359 - 00:34:29.569` | flag is zero then we will return the |
| `00:34:29.579 - 00:34:31.909` | inode for the parent |
| `00:34:31.919 - 00:34:34.310` | okay and |
| `00:34:34.320 - 00:34:39.069` | copy the final file name into the name |
| `00:34:39.079 - 00:34:44.690` | area okay otherwise we will open that |
| `00:34:44.700 - 00:34:46.430` | file directly |
| `00:34:46.440 - 00:34:49.609` | okay so this name X is only called from |
| `00:34:49.619 - 00:34:54.649` | name I and name my parent |
| `00:34:54.659 - 00:34:56.930` | so I think the way to look at this thing |
| `00:34:56.940 - 00:35:00.349` | is to imagine we're calling it from name |
| `00:35:00.359 - 00:35:03.470` | I first and I'll look at the second case |
| `00:35:03.480 - 00:35:06.170` | uh a bit later but so imagine that this |
| `00:35:06.180 - 00:35:10.550` | flag is zero right and we have a 14 byte |
| `00:35:10.560 - 00:35:14.270` | area that we can use within this name X |
| `00:35:14.280 - 00:35:16.670` | function |
| `00:35:16.680 - 00:35:19.130` | so here we start by seeing whether the |
| `00:35:19.140 - 00:35:21.829` | path begins with a slash if it does then |
| `00:35:21.839 - 00:35:24.109` | we're we need to start with the root |
| `00:35:24.119 - 00:35:28.430` | directory okay so here we uh have the |
| `00:35:28.440 - 00:35:30.230` | device on which the file system is kept |
| `00:35:30.240 - 00:35:33.530` | and the inode number which is one for |
| `00:35:33.540 - 00:35:36.349` | the root directory and we just get a |
| `00:35:36.359 - 00:35:39.650` | pointer to the inode for that here |
| `00:35:39.660 - 00:35:42.170` | and let's say we've got just a slash and |
| `00:35:42.180 - 00:35:45.109` | nothing else then uh this while okay so |
| `00:35:45.119 - 00:35:48.530` | here's a while loop that processes each |
| `00:35:48.540 - 00:35:52.069` | element in the path name but let's say |
| `00:35:52.079 - 00:35:54.790` | that there's nothing then we immediately |
| `00:35:54.800 - 00:35:58.609` | exit the loop and let's we're assuming |
| `00:35:58.619 - 00:36:02.870` | that this flag is zero so we skip this |
| `00:36:02.880 - 00:36:06.109` | and we would just return a pointer to |
| `00:36:06.119 - 00:36:08.710` | the I node for the root doesn't lock it |
| `00:36:08.720 - 00:36:12.650` | but it does add it to the cache |
| `00:36:12.660 - 00:36:14.930` | so this flag is called somewhat |
| `00:36:14.940 - 00:36:19.490` | confusingly name I parent and uh so the |
| `00:36:19.500 - 00:36:21.470` | flag is called name I parent and we're |
| `00:36:21.480 - 00:36:23.089` | going to assume that that is false when |
| `00:36:23.099 - 00:36:25.310` | we look at this code here |
| `00:36:25.320 - 00:36:28.490` | now if it doesn't start with a slash |
| `00:36:28.500 - 00:36:31.490` | then we need to begin processing the |
| `00:36:31.500 - 00:36:34.329` | path from the current working directory |
| `00:36:34.339 - 00:36:37.550` | so the structure for this particular |
| `00:36:37.560 - 00:36:40.490` | process that we're running in Has a |
| `00:36:40.500 - 00:36:41.690` | Field called |
| `00:36:41.700 - 00:36:44.510` | current working directory and that is a |
| `00:36:44.520 - 00:36:46.250` | pointer to an inode so that's the |
| `00:36:46.260 - 00:36:48.349` | current working directory and we need to |
| `00:36:48.359 - 00:36:51.770` | use that we haven't seen this idupe uh |
| `00:36:51.780 - 00:36:53.870` | function before but basically all this |
| `00:36:53.880 - 00:36:55.430` | does is just increment the reference |
| `00:36:55.440 - 00:36:58.670` | count for this I node in fact let me |
| `00:36:58.680 - 00:37:01.010` | just stop and show you the code for that |
| `00:37:01.020 - 00:37:04.250` | here here's the idupe function pass the |
| `00:37:04.260 - 00:37:08.990` | pointer to an inode and it acquires the |
| `00:37:09.000 - 00:37:12.410` | spin lock for the cache increments the |
| `00:37:12.420 - 00:37:13.670` | reference count and then releases the |
| `00:37:13.680 - 00:37:17.089` | lock okay so uh that's what idoop is |
| `00:37:17.099 - 00:37:22.569` | doing right here |
| `00:37:22.579 - 00:37:26.870` | and uh I get will increase the reference |
| `00:37:26.880 - 00:37:29.089` | count for the root directory so |
| `00:37:29.099 - 00:37:30.829` | regardless of whether we do this or we |
| `00:37:30.839 - 00:37:33.410` | do this we've got an I know that's been |
| `00:37:33.420 - 00:37:35.450` | added to the cache and we've incremented |
| `00:37:35.460 - 00:37:38.210` | the reference count for that so that's |
| `00:37:38.220 - 00:37:40.790` | what will be true when we return down |
| `00:37:40.800 - 00:37:42.410` | here |
| `00:37:42.420 - 00:37:46.370` | okay so whether we start with a slash or |
| `00:37:46.380 - 00:37:49.730` | not we are ready to start looking at the |
| `00:37:49.740 - 00:37:51.710` | path so we're going to call the skip |
| `00:37:51.720 - 00:37:53.630` | element function that we just talked |
| `00:37:53.640 - 00:37:54.410` | about |
| `00:37:54.420 - 00:37:56.990` | to locate the first file name in The |
| `00:37:57.000 - 00:37:59.270` | Path and to advance the path pointer to |
| `00:37:59.280 - 00:38:01.370` | the next element so that's what happens |
| `00:38:01.380 - 00:38:04.730` | here okay if there is no next element |
| `00:38:04.740 - 00:38:07.910` | then we are done okay and we exit the |
| `00:38:07.920 - 00:38:09.650` | loop |
| `00:38:09.660 - 00:38:13.430` | but uh otherwise we need to look in the |
| `00:38:13.440 - 00:38:16.190` | directory uh that we're pointed to at |
| `00:38:16.200 - 00:38:19.609` | the beginning of this for the file that |
| `00:38:19.619 - 00:38:22.190` | has this name so here we are identifying |
| `00:38:22.200 - 00:38:24.710` | the name and and and saving it in this |
| `00:38:24.720 - 00:38:28.370` | name variable and at that point the next |
| `00:38:28.380 - 00:38:29.270` | thing we're going to do is call |
| `00:38:29.280 - 00:38:31.370` | directory lookup but before we do that |
| `00:38:31.380 - 00:38:35.510` | we need to lock that inode okay so we |
| `00:38:35.520 - 00:38:38.030` | lock it here and then we call directory |
| `00:38:38.040 - 00:38:39.109` | lookup |
| `00:38:39.119 - 00:38:42.650` | before we do that we make sure that the |
| `00:38:42.660 - 00:38:45.170` | file that we are thinking as a directory |
| `00:38:45.180 - 00:38:47.450` | is in fact a directory so we check its |
| `00:38:47.460 - 00:38:49.190` | type and if it's not a directory |
| `00:38:49.200 - 00:38:53.030` | something is the matter and so we are |
| `00:38:53.040 - 00:38:55.010` | going to abort and return |
| `00:38:55.020 - 00:38:57.170` | a zero to indicate that we had problems |
| `00:38:57.180 - 00:39:01.490` | but remember that we've got the uh the |
| `00:39:01.500 - 00:39:04.490` | directory it has been had its reference |
| `00:39:04.500 - 00:39:06.530` | count increased and we've locked it so |
| `00:39:06.540 - 00:39:08.569` | we need to undo both of those things so |
| `00:39:08.579 - 00:39:10.609` | we unlock it and decrease the reference |
| `00:39:10.619 - 00:39:13.910` | count and before we return |
| `00:39:13.920 - 00:39:16.130` | now I said let's just take a look at the |
| `00:39:16.140 - 00:39:19.370` | case where this flag this name I parent |
| `00:39:19.380 - 00:39:22.970` | flag is false so let's skip that for now |
| `00:39:22.980 - 00:39:24.710` | and now we're ready to do the directory |
| `00:39:24.720 - 00:39:28.010` | lookup so we've got the first or the I |
| `00:39:28.020 - 00:39:30.349` | should say the next file name in the |
| `00:39:30.359 - 00:39:34.329` | path in this string here and this is our |
| `00:39:34.339 - 00:39:37.609` | inode of the directory and so we're |
| `00:39:37.619 - 00:39:42.890` | going to do a directory lookup |
| `00:39:42.900 - 00:39:45.410` | so remember that this is the offset into |
| `00:39:45.420 - 00:39:47.390` | the directory this is a pointer to a |
| `00:39:47.400 - 00:39:49.130` | place where we can save the offset if we |
| `00:39:49.140 - 00:39:50.750` | want to we're going to pass in null |
| `00:39:50.760 - 00:39:52.370` | because we don't care where in the |
| `00:39:52.380 - 00:39:54.589` | directory this name is found so we don't |
| `00:39:54.599 - 00:39:57.650` | need that information we pass in a null |
| `00:39:57.660 - 00:39:59.210` | rather than the address of an of a |
| `00:39:59.220 - 00:40:01.250` | variable in which to store it |
| `00:40:01.260 - 00:40:02.750` | so |
| `00:40:02.760 - 00:40:04.910` | um here is going to this is going to |
| `00:40:04.920 - 00:40:08.450` | return a pointer to the inode uh for |
| `00:40:08.460 - 00:40:10.730` | this file so that's what the next I node |
| `00:40:10.740 - 00:40:13.069` | is and we'll see here we're going to |
| `00:40:13.079 - 00:40:16.069` | advance to that okay so if everything |
| `00:40:16.079 - 00:40:18.890` | goes well then we return |
| `00:40:18.900 - 00:40:21.410` | a pointer to an i node but if there's a |
| `00:40:21.420 - 00:40:24.050` | problem we return null and again we've |
| `00:40:24.060 - 00:40:25.130` | got this |
| `00:40:25.140 - 00:40:28.370` | directory that we're looking into locked |
| `00:40:28.380 - 00:40:29.630` | and we've increased its reference count |
| `00:40:29.640 - 00:40:31.910` | so we need to undo those two things and |
| `00:40:31.920 - 00:40:33.890` | return a null indication to indicate |
| `00:40:33.900 - 00:40:35.990` | there was a problem okay we couldn't |
| `00:40:36.000 - 00:40:37.730` | find the file |
| `00:40:37.740 - 00:40:40.490` | so if there's a problem with a path name |
| `00:40:40.500 - 00:40:42.710` | and we can't find one of the files in |
| `00:40:42.720 - 00:40:45.890` | the path name we considered an error in |
| `00:40:45.900 - 00:40:48.109` | return null but assuming everything's |
| `00:40:48.119 - 00:40:50.930` | okay then we're done with this directory |
| `00:40:50.940 - 00:40:53.510` | we now have uh the next thing in the |
| `00:40:53.520 - 00:40:55.970` | path name could be the file that we're |
| `00:40:55.980 - 00:40:58.010` | looking for that is the thing at the end |
| `00:40:58.020 - 00:41:00.410` | of the path name or it could be another |
| `00:41:00.420 - 00:41:02.870` | directory in other words we could be |
| `00:41:02.880 - 00:41:05.510` | looking at the bin directory or we could |
| `00:41:05.520 - 00:41:07.490` | be down here looking at the file that we |
| `00:41:07.500 - 00:41:10.150` | want to get at but in any case |
| `00:41:10.160 - 00:41:13.670` | we are going to advance to that |
| `00:41:13.680 - 00:41:17.809` | to that so we uh unlock the directory |
| `00:41:17.819 - 00:41:20.870` | and then move to the next thing the next |
| `00:41:20.880 - 00:41:22.970` | file in the path and then we loop back |
| `00:41:22.980 - 00:41:26.030` | up okay if that was it okay if that was |
| `00:41:26.040 - 00:41:29.930` | the last thing in the past then this |
| `00:41:29.940 - 00:41:31.849` | thing is going to return zero and we'll |
| `00:41:31.859 - 00:41:33.710` | exit and |
| `00:41:33.720 - 00:41:36.950` | um we'll exit the loop here and then |
| `00:41:36.960 - 00:41:39.230` | return the pointer to the file that we |
| `00:41:39.240 - 00:41:43.250` | found otherwise the while loop |
| `00:41:43.260 - 00:41:45.349` | will continue and look at the next |
| `00:41:45.359 - 00:41:47.329` | element in the path name |
| `00:41:47.339 - 00:41:50.450` | okay so now let's take a look at the |
| `00:41:50.460 - 00:41:52.910` | name I parent function here we're |
| `00:41:52.920 - 00:41:55.609` | passing in one so in this case we just |
| `00:41:55.619 - 00:42:00.530` | want to stop prematurely |
| `00:42:00.540 - 00:42:04.010` | so again we start with something let's |
| `00:42:04.020 - 00:42:07.190` | say we start with the root directory |
| `00:42:07.200 - 00:42:11.750` | and we set IP 2.2 the the next directory |
| `00:42:11.760 - 00:42:14.750` | or the next thing in the path and let's |
| `00:42:14.760 - 00:42:16.069` | say there's nothing else okay this |
| `00:42:16.079 - 00:42:18.470` | returns zero well there is no second |
| `00:42:18.480 - 00:42:22.190` | thing and that's a problem so |
| `00:42:22.200 - 00:42:25.010` | um if this thing is true which it will |
| `00:42:25.020 - 00:42:28.430` | be for a name I parent situation then |
| `00:42:28.440 - 00:42:30.609` | we've got a problem we need to return |
| `00:42:30.619 - 00:42:33.829` | zero it was not at least one file name |
| `00:42:33.839 - 00:42:38.089` | in The Path so uh we uh |
| `00:42:38.099 - 00:42:40.370` | get we unlock the |
| `00:42:40.380 - 00:42:41.870` | um |
| `00:42:41.880 - 00:42:43.970` | sorry we haven't locked it because the |
| `00:42:43.980 - 00:42:47.150` | lock occurs in the loop okay |
| `00:42:47.160 - 00:42:48.530` | um |
| `00:42:48.540 - 00:42:52.490` | we uh do not have IP locked at this |
| `00:42:52.500 - 00:42:53.930` | point so we don't need to unlock it we |
| `00:42:53.940 - 00:42:55.670` | just need to decrement its reference |
| `00:42:55.680 - 00:42:57.530` | count before we return |
| `00:42:57.540 - 00:42:58.970` | now |
| `00:42:58.980 - 00:43:02.809` | okay in the loop what do we see |
| `00:43:02.819 - 00:43:04.309` | um it's the same thing we're going |
| `00:43:04.319 - 00:43:08.809` | through it and we get the next thing in |
| `00:43:08.819 - 00:43:12.470` | the path okay we look in the directory |
| `00:43:12.480 - 00:43:16.430` | it's that is pointed to by IP and |
| `00:43:16.440 - 00:43:20.450` | identify the next file and if we are |
| `00:43:20.460 - 00:43:23.210` | wanting to stop early and we've come to |
| `00:43:23.220 - 00:43:25.069` | the end of the path all right we've |
| `00:43:25.079 - 00:43:28.970` | Advanced path to a point to the thing |
| `00:43:28.980 - 00:43:31.670` | after name and if there is nothing after |
| `00:43:31.680 - 00:43:34.670` | name then we can stop okay we're done |
| `00:43:34.680 - 00:43:37.250` | okay we've identified the last thing in |
| `00:43:37.260 - 00:43:40.069` | the file and IP at this point points to |
| `00:43:40.079 - 00:43:43.130` | the directory so we unlock the directory |
| `00:43:43.140 - 00:43:44.510` | and return |
| `00:43:44.520 - 00:43:46.670` | it |
| `00:43:46.680 - 00:43:48.410` | okay I think that's it for all the |
| `00:43:48.420 - 00:43:51.349` | functions in the file fs.c I will see |
| `00:43:51.359 - 00:43:54.079` | you in the next video |
