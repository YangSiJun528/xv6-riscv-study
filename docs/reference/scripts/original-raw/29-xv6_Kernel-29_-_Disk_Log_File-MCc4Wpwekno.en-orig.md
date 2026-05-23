# xv6 Kernel-29: Disk Log File

| Field | Value |
| --- | --- |
| Video ID | `MCc4Wpwekno` |
| URL | <https://www.youtube.com/watch?v=MCc4Wpwekno> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.799 - 00:00:03.189` | welcome to another video in a video |
| `00:00:03.199 - 00:00:06.230` | playlist describing the xv6 operating |
| `00:00:06.240 - 00:00:07.829` | system kernel |
| `00:00:07.839 - 00:00:10.070` | in this video i'm going to describe the |
| `00:00:10.080 - 00:00:12.230` | disk log file system |
| `00:00:12.240 - 00:00:13.910` | and i'll be covering the code in the |
| `00:00:13.920 - 00:00:15.030` | file |
| `00:00:15.040 - 00:00:16.630` | dot c |
| `00:00:16.640 - 00:00:18.470` | this code is pretty complicated so i'm |
| `00:00:18.480 - 00:00:21.830` | going to start with a motivating example |
| `00:00:21.840 - 00:00:23.509` | the problem is that we |
| `00:00:23.519 - 00:00:26.070` | may have complex data structures and |
| `00:00:26.080 - 00:00:28.150` | crashes can occur |
| `00:00:28.160 - 00:00:29.109` | and |
| `00:00:29.119 - 00:00:32.150` | updates require several different |
| `00:00:32.160 - 00:00:34.630` | operations in order to perform the |
| `00:00:34.640 - 00:00:37.350` | entire update consistently |
| `00:00:37.360 - 00:00:40.470` | so for example here i have a singly |
| `00:00:40.480 - 00:00:41.750` | linked list |
| `00:00:41.760 - 00:00:44.709` | okay it's linked like this and i'd like |
| `00:00:44.719 - 00:00:48.389` | to swap the order of these two nodes and |
| `00:00:48.399 - 00:00:50.470` | so i need to update one |
| `00:00:50.480 - 00:00:53.189` | two three pointers in order to do this |
| `00:00:53.199 - 00:00:55.510` | particular update so i need three |
| `00:00:55.520 - 00:00:57.029` | different rights |
| `00:00:57.039 - 00:00:59.029` | to make the change |
| `00:00:59.039 - 00:01:00.950` | and if a crash occurs in the middle of |
| `00:01:00.960 - 00:01:02.709` | this operation |
| `00:01:02.719 - 00:01:04.950` | then the data structure can be left in |
| `00:01:04.960 - 00:01:07.590` | what we call an inconsistent state |
| `00:01:07.600 - 00:01:09.750` | in other words the linked list is all |
| `00:01:09.760 - 00:01:11.670` | messed up and |
| `00:01:11.680 - 00:01:14.149` | while this example is probably in memory |
| `00:01:14.159 - 00:01:15.590` | and the whole thing just goes away at |
| `00:01:15.600 - 00:01:17.749` | the time of the crash what we're really |
| `00:01:17.759 - 00:01:21.030` | getting at here is a problem with the |
| `00:01:21.040 - 00:01:22.710` | file system |
| `00:01:22.720 - 00:01:25.270` | in that case the data structure |
| `00:01:25.280 - 00:01:27.670` | is kept on disk and that data structure |
| `00:01:27.680 - 00:01:29.830` | is used to represent files and |
| `00:01:29.840 - 00:01:32.469` | directories as well as inodes and free |
| `00:01:32.479 - 00:01:34.230` | maps and so on |
| `00:01:34.240 - 00:01:37.109` | in my example each update was a |
| `00:01:37.119 - 00:01:39.830` | modification of a single pointer but |
| `00:01:39.840 - 00:01:41.670` | with our file system |
| `00:01:41.680 - 00:01:44.950` | each right will be a right of a single |
| `00:01:44.960 - 00:01:47.830` | of an entire block to the disk |
| `00:01:47.840 - 00:01:50.550` | and a particular update may involve |
| `00:01:50.560 - 00:01:52.550` | several blocks in other words it may |
| `00:01:52.560 - 00:01:54.550` | require several blocks to be written to |
| `00:01:54.560 - 00:01:56.069` | the disk |
| `00:01:56.079 - 00:01:58.389` | and a crash of the system either a |
| `00:01:58.399 - 00:01:59.510` | hardware |
| `00:01:59.520 - 00:02:01.910` | crash or a software crash |
| `00:02:01.920 - 00:02:04.310` | must not be allowed to |
| `00:02:04.320 - 00:02:06.469` | mess up the file system we need to keep |
| `00:02:06.479 - 00:02:09.109` | the file system in a consistent state |
| `00:02:09.119 - 00:02:11.430` | so in this particular example |
| `00:02:11.440 - 00:02:13.030` | if we are going to swap these two |
| `00:02:13.040 - 00:02:15.350` | elements we either need to |
| `00:02:15.360 - 00:02:18.150` | do none of the changes in which case the |
| `00:02:18.160 - 00:02:20.949` | update hasn't occurred or we need to do |
| `00:02:20.959 - 00:02:23.270` | all of the changes in which case the |
| `00:02:23.280 - 00:02:25.430` | update has occurred |
| `00:02:25.440 - 00:02:27.190` | let's look at a more realistic example |
| `00:02:27.200 - 00:02:28.790` | from the file system |
| `00:02:28.800 - 00:02:30.309` | let's assume we want to increase the |
| `00:02:30.319 - 00:02:32.710` | size of some file |
| `00:02:32.720 - 00:02:35.830` | we will have to allocate and remove a |
| `00:02:35.840 - 00:02:37.350` | block from some |
| `00:02:37.360 - 00:02:39.670` | pool of free blocks and then we'll have |
| `00:02:39.680 - 00:02:42.550` | to add that block to the file that we |
| `00:02:42.560 - 00:02:43.830` | are growing |
| `00:02:43.840 - 00:02:46.070` | and each one of these will involve an |
| `00:02:46.080 - 00:02:47.670` | update to the data structure so we |
| `00:02:47.680 - 00:02:50.150` | assume that we have to have at least two |
| `00:02:50.160 - 00:02:52.150` | disk rights |
| `00:02:52.160 - 00:02:55.350` | now assume that the computer crashes |
| `00:02:55.360 - 00:02:56.150` | and |
| `00:02:56.160 - 00:02:58.550` | that one of these rights has completed |
| `00:02:58.560 - 00:03:02.070` | but the other one has not completed so |
| `00:03:02.080 - 00:03:04.790` | we might have a data structure that is |
| `00:03:04.800 - 00:03:07.910` | corrupted in an inconsistent state |
| `00:03:07.920 - 00:03:09.910` | so how are we going to guard against |
| `00:03:09.920 - 00:03:10.869` | this |
| `00:03:10.879 - 00:03:11.750` | well |
| `00:03:11.760 - 00:03:13.589` | we might |
| `00:03:13.599 - 00:03:17.110` | try removing from the free pool first |
| `00:03:17.120 - 00:03:20.229` | before we add the block to the file but |
| `00:03:20.239 - 00:03:22.070` | if the crash occurs after the first |
| `00:03:22.080 - 00:03:22.949` | write |
| `00:03:22.959 - 00:03:24.229` | then |
| `00:03:24.239 - 00:03:26.869` | we've got one block that has been |
| `00:03:26.879 - 00:03:29.110` | removed from the pre-pool but not added |
| `00:03:29.120 - 00:03:32.149` | to any file so essentially we've lost a |
| `00:03:32.159 - 00:03:34.789` | block it's become unaccounted for so |
| `00:03:34.799 - 00:03:36.149` | that's not going to work |
| `00:03:36.159 - 00:03:36.949` | so |
| `00:03:36.959 - 00:03:39.509` | maybe we can reverse the order and |
| `00:03:39.519 - 00:03:42.630` | add the block to the file first that is |
| `00:03:42.640 - 00:03:44.710` | perform the write that will update the |
| `00:03:44.720 - 00:03:46.949` | file data structure |
| `00:03:46.959 - 00:03:49.750` | and then update the free pool data |
| `00:03:49.760 - 00:03:51.670` | structure to indicate that we've |
| `00:03:51.680 - 00:03:54.309` | allocated one of the blocks but then if |
| `00:03:54.319 - 00:03:57.750` | the crash occurs after the first write |
| `00:03:57.760 - 00:04:00.070` | then we've got a different problem we've |
| `00:04:00.080 - 00:04:02.869` | now got a block that's still in the free |
| `00:04:02.879 - 00:04:05.190` | pool yet has already been added to a |
| `00:04:05.200 - 00:04:08.470` | file and that's a disaster as well one |
| `00:04:08.480 - 00:04:11.190` | block is both in a file and in the free |
| `00:04:11.200 - 00:04:12.789` | pool |
| `00:04:12.799 - 00:04:15.830` | so neither of these two approaches works |
| `00:04:15.840 - 00:04:18.069` | this problem is more general |
| `00:04:18.079 - 00:04:19.430` | you know imagine a |
| `00:04:19.440 - 00:04:21.909` | bank computer a system that's uh |
| `00:04:21.919 - 00:04:24.469` | involved with bank accounts and money |
| `00:04:24.479 - 00:04:27.030` | and imagine that the operation of the |
| `00:04:27.040 - 00:04:28.469` | transaction |
| `00:04:28.479 - 00:04:29.990` | is to remove |
| `00:04:30.000 - 00:04:32.390` | say a million dollars from one account |
| `00:04:32.400 - 00:04:33.990` | and put it into |
| `00:04:34.000 - 00:04:35.670` | a second account |
| `00:04:35.680 - 00:04:36.629` | and this |
| `00:04:36.639 - 00:04:38.629` | operation is going to involve two |
| `00:04:38.639 - 00:04:41.189` | different updates one removes the |
| `00:04:41.199 - 00:04:43.189` | million dollars from one account and the |
| `00:04:43.199 - 00:04:45.030` | other update uh |
| `00:04:45.040 - 00:04:47.189` | adds the million dollars to the second |
| `00:04:47.199 - 00:04:48.310` | account |
| `00:04:48.320 - 00:04:49.590` | well |
| `00:04:49.600 - 00:04:53.270` | you can't tolerate a crash i mean if you |
| `00:04:53.280 - 00:04:55.590` | do them in one order you may have lost a |
| `00:04:55.600 - 00:04:56.950` | million dollars |
| `00:04:56.960 - 00:04:58.950` | and if you do them in the other order |
| `00:04:58.960 - 00:05:00.070` | you may have |
| `00:05:00.080 - 00:05:01.990` | fabricated a million dollars out of thin |
| `00:05:02.000 - 00:05:04.469` | air and neither of those solutions is is |
| `00:05:04.479 - 00:05:05.670` | acceptable |
| `00:05:05.680 - 00:05:08.230` | so this problem of transactions and |
| `00:05:08.240 - 00:05:10.790` | is a general problem and we're going to |
| `00:05:10.800 - 00:05:14.469` | see how it's handled here in the xv6 |
| `00:05:14.479 - 00:05:16.710` | operating system |
| `00:05:16.720 - 00:05:19.110` | with an operation involving two writes |
| `00:05:19.120 - 00:05:22.150` | our goal is that either both rights are |
| `00:05:22.160 - 00:05:24.469` | completed and the disk is updated |
| `00:05:24.479 - 00:05:26.629` | or if a crash happens |
| `00:05:26.639 - 00:05:28.550` | neither of the rights is actually |
| `00:05:28.560 - 00:05:31.430` | performed so it's either all or nothing |
| `00:05:31.440 - 00:05:33.430` | we don't have a situation where one |
| `00:05:33.440 - 00:05:35.909` | right is done to the disk but the other |
| `00:05:35.919 - 00:05:39.590` | right is not done more generally we can |
| `00:05:39.600 - 00:05:42.390` | talk about transactions and we will |
| `00:05:42.400 - 00:05:44.390` | group several updates and in this case |
| `00:05:44.400 - 00:05:46.790` | we're talking about disk rights |
| `00:05:46.800 - 00:05:48.550` | and we will group several disk rights |
| `00:05:48.560 - 00:05:51.990` | into a single transaction and either the |
| `00:05:52.000 - 00:05:54.150` | transaction occurs |
| `00:05:54.160 - 00:05:56.629` | and the disk is updated or the |
| `00:05:56.639 - 00:05:58.870` | transaction does not occur before the |
| `00:05:58.880 - 00:06:02.309` | crash and none of the rights occur |
| `00:06:02.319 - 00:06:03.670` | all or nothing |
| `00:06:03.680 - 00:06:06.870` | what this looks like in xv6 involves |
| `00:06:06.880 - 00:06:09.110` | these functions begin op |
| `00:06:09.120 - 00:06:10.469` | b read |
| `00:06:10.479 - 00:06:12.950` | log write and end up |
| `00:06:12.960 - 00:06:13.830` | and |
| `00:06:13.840 - 00:06:15.990` | the begin op function will be called |
| `00:06:16.000 - 00:06:18.830` | first and that will start what we call a |
| `00:06:18.840 - 00:06:21.029` | transaction and then |
| `00:06:21.039 - 00:06:24.790` | the end up will end the transaction and |
| `00:06:24.800 - 00:06:26.950` | between these two we will have a number |
| `00:06:26.960 - 00:06:28.550` | of rights |
| `00:06:28.560 - 00:06:31.430` | we will also have some reads as well but |
| `00:06:31.440 - 00:06:32.790` | it's the rights that we're sort of |
| `00:06:32.800 - 00:06:35.990` | focusing on and when the end op function |
| `00:06:36.000 - 00:06:38.070` | is called then the transaction is |
| `00:06:38.080 - 00:06:41.590` | complete and it signals to the system |
| `00:06:41.600 - 00:06:44.629` | where the transaction is and it will |
| `00:06:44.639 - 00:06:49.189` | either all happen or not happen at all |
| `00:06:49.199 - 00:06:51.189` | now let's take a look at this example |
| `00:06:51.199 - 00:06:53.909` | i've got two transactions here's one and |
| `00:06:53.919 - 00:06:55.510` | here's the other and they're being |
| `00:06:55.520 - 00:06:57.830` | executed in two separate threads |
| `00:06:57.840 - 00:06:59.189` | concurrently |
| `00:06:59.199 - 00:07:01.990` | so each begins with a begin operation |
| `00:07:02.000 - 00:07:04.870` | and ends with an end operation |
| `00:07:04.880 - 00:07:07.350` | and there are a number of reads |
| `00:07:07.360 - 00:07:09.830` | and writes in the transaction |
| `00:07:09.840 - 00:07:11.990` | and so i'm showing in this transaction |
| `00:07:12.000 - 00:07:15.110` | we are reading block number 103 |
| `00:07:15.120 - 00:07:17.270` | and writing it back to the disk and then |
| `00:07:17.280 - 00:07:20.629` | here we're reading block 108 and writing |
| `00:07:20.639 - 00:07:21.830` | 108 |
| `00:07:21.840 - 00:07:23.830` | and in this transaction we're reading |
| `00:07:23.840 - 00:07:25.749` | block 103 |
| `00:07:25.759 - 00:07:27.830` | but we're not writing it and then we're |
| `00:07:27.840 - 00:07:31.350` | reading 106 and writing 106. so whatever |
| `00:07:31.360 - 00:07:33.990` | we write into block 106 could be |
| `00:07:34.000 - 00:07:36.150` | dependent on 103 |
| `00:07:36.160 - 00:07:38.469` | and i'm showing by the positioning here |
| `00:07:38.479 - 00:07:41.909` | that this read occurs after this write |
| `00:07:41.919 - 00:07:42.950` | so |
| `00:07:42.960 - 00:07:45.830` | here with this read we must be getting |
| `00:07:45.840 - 00:07:47.589` | the version of the block that was |
| `00:07:47.599 - 00:07:50.790` | written by this write operation here |
| `00:07:50.800 - 00:07:54.710` | however we can't actually write this |
| `00:07:54.720 - 00:07:57.909` | block to the disk until after the end |
| `00:07:57.919 - 00:08:00.230` | operation so we have to keep it in a |
| `00:08:00.240 - 00:08:02.230` | buffer and delay the actual write to the |
| `00:08:02.240 - 00:08:03.110` | disk |
| `00:08:03.120 - 00:08:04.710` | while at the same time delivering to |
| `00:08:04.720 - 00:08:07.830` | this read operation the data that was |
| `00:08:07.840 - 00:08:09.749` | written here so we have to get the new |
| `00:08:09.759 - 00:08:10.790` | version |
| `00:08:10.800 - 00:08:12.710` | and the other thing is when this |
| `00:08:12.720 - 00:08:15.909` | transaction ends we can't just simply |
| `00:08:15.919 - 00:08:19.029` | write block 106 to the disk because it's |
| `00:08:19.039 - 00:08:22.230` | dependent on 103 as well so we can't |
| `00:08:22.240 - 00:08:25.029` | actually perform the disk update |
| `00:08:25.039 - 00:08:28.790` | until we're ready to uh perform this end |
| `00:08:28.800 - 00:08:31.189` | operation we've got to wait we can't |
| `00:08:31.199 - 00:08:35.029` | write block 106 unless we're we are also |
| `00:08:35.039 - 00:08:37.909` | writing blocks 103 and 108. so in a |
| `00:08:37.919 - 00:08:39.430` | sense these things are all linked |
| `00:08:39.440 - 00:08:40.550` | together |
| `00:08:40.560 - 00:08:41.670` | and |
| `00:08:41.680 - 00:08:44.389` | have to be either committed together or |
| `00:08:44.399 - 00:08:46.389` | not committed |
| `00:08:46.399 - 00:08:48.630` | so what we're going to do for the right |
| `00:08:48.640 - 00:08:50.550` | operation is |
| `00:08:50.560 - 00:08:53.190` | keep the block in the buffer cache in |
| `00:08:53.200 - 00:08:54.389` | other words we're not going to write it |
| `00:08:54.399 - 00:08:56.230` | to the disk immediately we're just going |
| `00:08:56.240 - 00:08:58.389` | to keep it in memory |
| `00:08:58.399 - 00:09:01.750` | and that solves this problem here |
| `00:09:01.760 - 00:09:05.190` | for the read we will use the version of |
| `00:09:05.200 - 00:09:07.750` | the block that is in the buffer cache if |
| `00:09:07.760 - 00:09:10.630` | there is a block sitting there and |
| `00:09:10.640 - 00:09:12.870` | otherwise we will read the data from the |
| `00:09:12.880 - 00:09:13.990` | disk |
| `00:09:14.000 - 00:09:15.509` | okay at the |
| `00:09:15.519 - 00:09:17.110` | end operation |
| `00:09:17.120 - 00:09:20.630` | we will not perform the rights until all |
| `00:09:20.640 - 00:09:22.389` | of the transactions are ended |
| `00:09:22.399 - 00:09:24.070` | okay here we have two separate |
| `00:09:24.080 - 00:09:25.430` | transactions |
| `00:09:25.440 - 00:09:27.269` | and at this one we're not going to do |
| `00:09:27.279 - 00:09:29.829` | anything really it's only at this point |
| `00:09:29.839 - 00:09:33.509` | here where we will be doing the rights |
| `00:09:33.519 - 00:09:34.790` | okay |
| `00:09:34.800 - 00:09:36.949` | we also have a requirement that we want |
| `00:09:36.959 - 00:09:38.790` | to |
| `00:09:38.800 - 00:09:41.590` | avoid the exhaustion of the free buffers |
| `00:09:41.600 - 00:09:42.630` | and |
| `00:09:42.640 - 00:09:45.269` | log records okay so we can't |
| `00:09:45.279 - 00:09:46.870` | start |
| `00:09:46.880 - 00:09:49.350` | operations can't start transactions if |
| `00:09:49.360 - 00:09:50.870` | we don't have enough |
| `00:09:50.880 - 00:09:53.750` | memory or resources to |
| `00:09:53.760 - 00:09:56.389` | complete the transaction |
| `00:09:56.399 - 00:09:58.070` | we also don't want to delay writing |
| `00:09:58.080 - 00:10:01.190` | forever that would be starvation if we |
| `00:10:01.200 - 00:10:02.790` | for example have a sequence of |
| `00:10:02.800 - 00:10:04.550` | transactions that are trying to be |
| `00:10:04.560 - 00:10:05.670` | started |
| `00:10:05.680 - 00:10:08.470` | we have a bunch of transactions and |
| `00:10:08.480 - 00:10:11.030` | there's always one in progress it might |
| `00:10:11.040 - 00:10:12.790` | be the situation where |
| `00:10:12.800 - 00:10:14.710` | we never have a situation where we can |
| `00:10:14.720 - 00:10:16.870` | actually do the commit |
| `00:10:16.880 - 00:10:19.590` | okay so we can't allow that |
| `00:10:19.600 - 00:10:22.389` | so we will have to delay some begin |
| `00:10:22.399 - 00:10:24.470` | operations if we have inadequate |
| `00:10:24.480 - 00:10:27.910` | resources or starvation is a possibility |
| `00:10:27.920 - 00:10:29.910` | and finally we have this idea of a |
| `00:10:29.920 - 00:10:32.710` | commit and at the time of the commit |
| `00:10:32.720 - 00:10:35.110` | that is when we perform all of the right |
| `00:10:35.120 - 00:10:37.910` | operations that we've accumulated so far |
| `00:10:37.920 - 00:10:40.470` | so here at this end |
| `00:10:40.480 - 00:10:42.710` | we won't actually do the commit |
| `00:10:42.720 - 00:10:45.350` | okay this transaction is over but it's |
| `00:10:45.360 - 00:10:47.350` | not yet committed it's only at this |
| `00:10:47.360 - 00:10:49.030` | point here |
| `00:10:49.040 - 00:10:51.269` | when all transactions are completed that |
| `00:10:51.279 - 00:10:56.389` | we can perform the commit operation |
| `00:10:56.399 - 00:10:59.430` | here i'm showing in schematic form the |
| `00:10:59.440 - 00:11:02.069` | disk each one of these little squares |
| `00:11:02.079 - 00:11:05.269` | represents a block on the disk and the |
| `00:11:05.279 - 00:11:07.750` | disk is nothing more than a sequential |
| `00:11:07.760 - 00:11:10.230` | set of blocks starting with block 0 and |
| `00:11:10.240 - 00:11:13.030` | going up to some maximum |
| `00:11:13.040 - 00:11:15.110` | we'll be talking about the log which |
| `00:11:15.120 - 00:11:18.150` | consists of a header and some log blocks |
| `00:11:18.160 - 00:11:19.269` | and |
| `00:11:19.279 - 00:11:21.269` | we won't be too concerned with the first |
| `00:11:21.279 - 00:11:23.990` | two blocks the first block is the boot |
| `00:11:24.000 - 00:11:26.230` | record or the master boot record |
| `00:11:26.240 - 00:11:29.430` | and the second block is the superblock |
| `00:11:29.440 - 00:11:31.190` | the superblock contains a number of |
| `00:11:31.200 - 00:11:34.310` | fixed parameters it's read-only and at |
| `00:11:34.320 - 00:11:36.870` | startup time the kernel will read in the |
| `00:11:36.880 - 00:11:39.990` | superblock and this superblock contains |
| `00:11:40.000 - 00:11:42.790` | parameters such as where the log is |
| `00:11:42.800 - 00:11:45.590` | located how big the log is where the |
| `00:11:45.600 - 00:11:48.150` | main block area begins and how many |
| `00:11:48.160 - 00:11:51.110` | blocks there are on the disk |
| `00:11:51.120 - 00:11:52.550` | by the way |
| `00:11:52.560 - 00:11:55.269` | xv6 talks about a disk |
| `00:11:55.279 - 00:11:57.910` | but file systems can be stored on not |
| `00:11:57.920 - 00:12:00.470` | only rotating disks but solid-state |
| `00:12:00.480 - 00:12:02.550` | storage devices as well |
| `00:12:02.560 - 00:12:05.190` | and the organization that doesn't really |
| `00:12:05.200 - 00:12:08.550` | uh matter i mean it's it's similar for |
| `00:12:08.560 - 00:12:11.350` | solid state as it is for disks the solid |
| `00:12:11.360 - 00:12:13.829` | state uh device is broken up into a |
| `00:12:13.839 - 00:12:16.069` | number of blocks and so on but we'll |
| `00:12:16.079 - 00:12:21.110` | just stay focused uh on the disk here |
| `00:12:21.120 - 00:12:22.710` | the |
| `00:12:22.720 - 00:12:27.030` | log consists of a header and a small uh |
| `00:12:27.040 - 00:12:30.230` | fixed sized area which contains a number |
| `00:12:30.240 - 00:12:33.190` | of log blocks in our case it happens to |
| `00:12:33.200 - 00:12:35.190` | be 30. |
| `00:12:35.200 - 00:12:37.269` | and that's followed by the main block |
| `00:12:37.279 - 00:12:39.430` | area the main block area is where the |
| `00:12:39.440 - 00:12:41.509` | file system is stored and the file |
| `00:12:41.519 - 00:12:43.590` | system contains all kinds of stuff |
| `00:12:43.600 - 00:12:46.710` | the directories the files the inodes |
| `00:12:46.720 - 00:12:48.790` | the log system is not going to be |
| `00:12:48.800 - 00:12:51.030` | concerned at all with the organization |
| `00:12:51.040 - 00:12:53.590` | of the file system so from our point of |
| `00:12:53.600 - 00:12:56.470` | view in this video the main block area |
| `00:12:56.480 - 00:12:59.509` | is just a large collection of blocks |
| `00:12:59.519 - 00:13:04.790` | we don't really know how it's organized |
| `00:13:04.800 - 00:13:06.870` | the header block |
| `00:13:06.880 - 00:13:09.829` | is uh read in at startup time into |
| `00:13:09.839 - 00:13:12.710` | memory and stored in a structure so |
| `00:13:12.720 - 00:13:15.110` | here's the structure that's kept in |
| `00:13:15.120 - 00:13:17.990` | memory and from time to time |
| `00:13:18.000 - 00:13:20.069` | this structure will be written back to |
| `00:13:20.079 - 00:13:23.110` | the header block on disk |
| `00:13:23.120 - 00:13:25.910` | the header contains a counter n |
| `00:13:25.920 - 00:13:28.550` | which tells how many of these log blocks |
| `00:13:28.560 - 00:13:31.030` | are in use and for every one of these |
| `00:13:31.040 - 00:13:35.269` | log blocks there's an entry in an array |
| `00:13:35.279 - 00:13:37.590` | so this number n tells how many of the |
| `00:13:37.600 - 00:13:40.389` | array elements are in use so for example |
| `00:13:40.399 - 00:13:42.790` | at any one point in time we might have |
| `00:13:42.800 - 00:13:45.990` | say three log blocks in use and the |
| `00:13:46.000 - 00:13:48.710` | remaining are yet to be used so in that |
| `00:13:48.720 - 00:13:50.949` | case n would be three and the first |
| `00:13:50.959 - 00:13:53.269` | three elements here would contain |
| `00:13:53.279 - 00:13:56.230` | information about which block is stored |
| `00:13:56.240 - 00:13:59.910` | in these positions on the disk |
| `00:13:59.920 - 00:14:03.430` | i'm also showing here the buffer cache |
| `00:14:03.440 - 00:14:06.389` | remember that the buffer cache is a |
| `00:14:06.399 - 00:14:09.269` | circularly linked list a doubly linked |
| `00:14:09.279 - 00:14:10.069` | list |
| `00:14:10.079 - 00:14:13.189` | with a header buffer and a number of |
| `00:14:13.199 - 00:14:14.629` | buffers |
| `00:14:14.639 - 00:14:16.629` | i'm also showing uh |
| `00:14:16.639 - 00:14:20.069` | individual blocks in the main block area |
| `00:14:20.079 - 00:14:21.910` | these are from the previous example i'm |
| `00:14:21.920 - 00:14:23.670` | going to try to work through it and show |
| `00:14:23.680 - 00:14:25.990` | how this thing gets updated but blocks |
| `00:14:26.000 - 00:14:29.750` | 103 106 and 108 contain some data |
| `00:14:29.760 - 00:14:31.509` | initially |
| `00:14:31.519 - 00:14:33.189` | here is the example that we're going to |
| `00:14:33.199 - 00:14:34.710` | be walking through |
| `00:14:34.720 - 00:14:37.030` | every transaction begins with a begin |
| `00:14:37.040 - 00:14:40.069` | operation and ends with end and can |
| `00:14:40.079 - 00:14:43.110` | contain reads and writes |
| `00:14:43.120 - 00:14:45.269` | so let's take a look at what all these |
| `00:14:45.279 - 00:14:52.069` | operations do read write begin and end |
| `00:14:52.079 - 00:14:55.430` | the read operation is really just b read |
| `00:14:55.440 - 00:14:58.470` | which we discussed in the previous video |
| `00:14:58.480 - 00:15:00.629` | what we'll do is we'll search the buffer |
| `00:15:00.639 - 00:15:02.629` | cache to see whether the block that we |
| `00:15:02.639 - 00:15:06.710` | want is in memory and if so we'll just |
| `00:15:06.720 - 00:15:09.430` | use that otherwise we'll allocate a |
| `00:15:09.440 - 00:15:11.829` | buffer from the buffer cache and read in |
| `00:15:11.839 - 00:15:14.790` | the data from the disk |
| `00:15:14.800 - 00:15:16.790` | the write operation is something new and |
| `00:15:16.800 - 00:15:18.710` | it's implemented with a function called |
| `00:15:18.720 - 00:15:21.269` | log right |
| `00:15:21.279 - 00:15:23.750` | the first thing it will do is it will |
| `00:15:23.760 - 00:15:25.670` | pin the buffer that we're trying to |
| `00:15:25.680 - 00:15:27.590` | write remember that we only do a write |
| `00:15:27.600 - 00:15:28.949` | after we've read |
| `00:15:28.959 - 00:15:30.949` | a particular block |
| `00:15:30.959 - 00:15:33.350` | so we have a buffer and we want to write |
| `00:15:33.360 - 00:15:35.509` | the buffer out so we won't actually |
| `00:15:35.519 - 00:15:37.430` | write it at this time |
| `00:15:37.440 - 00:15:39.350` | instead we'll just do something we call |
| `00:15:39.360 - 00:15:42.069` | pinning it which basically sets a flag |
| `00:15:42.079 - 00:15:43.990` | to indicate that that buffer should not |
| `00:15:44.000 - 00:15:45.749` | be freed |
| `00:15:45.759 - 00:15:47.910` | and then we will find the next available |
| `00:15:47.920 - 00:15:50.550` | slot in the log okay if there are three |
| `00:15:50.560 - 00:15:53.189` | elements in the log then n will be three |
| `00:15:53.199 - 00:15:56.470` | and we increment to 4 and we are able to |
| `00:15:56.480 - 00:16:00.470` | use the next entry in the array |
| `00:16:00.480 - 00:16:01.509` | so |
| `00:16:01.519 - 00:16:03.590` | we add an entry to the array and |
| `00:16:03.600 - 00:16:05.670` | increment the counter |
| `00:16:05.680 - 00:16:07.509` | but first it's possible that the block |
| `00:16:07.519 - 00:16:08.949` | that we're writing has already been |
| `00:16:08.959 - 00:16:11.590` | written and therefore is already located |
| `00:16:11.600 - 00:16:12.870` | in the log |
| `00:16:12.880 - 00:16:16.150` | so we search the log and if we find it |
| `00:16:16.160 - 00:16:18.629` | we just reuse that log slot and we do |
| `00:16:18.639 - 00:16:20.829` | not increment |
| `00:16:20.839 - 00:16:22.550` | n |
| `00:16:22.560 - 00:16:25.430` | every transaction must begin with this |
| `00:16:25.440 - 00:16:27.350` | operation and it's actually done in the |
| `00:16:27.360 - 00:16:30.069` | function called begin op |
| `00:16:30.079 - 00:16:32.150` | if there is another thread in the middle |
| `00:16:32.160 - 00:16:35.030` | of a commit operation then the begin |
| `00:16:35.040 - 00:16:37.269` | operation will just wait okay we'll go |
| `00:16:37.279 - 00:16:38.629` | to sleep |
| `00:16:38.639 - 00:16:41.030` | and wait until the commit is done and |
| `00:16:41.040 - 00:16:42.629` | then it will proceed |
| `00:16:42.639 - 00:16:44.230` | it will also check how many slots are |
| `00:16:44.240 - 00:16:46.069` | available on the log if we don't have |
| `00:16:46.079 - 00:16:48.150` | enough resources to continue this to |
| `00:16:48.160 - 00:16:49.910` | complete the biggest possible |
| `00:16:49.920 - 00:16:51.189` | transaction |
| `00:16:51.199 - 00:16:53.990` | then there could be a problem so |
| `00:16:54.000 - 00:16:56.790` | the begin operation will just sleep and |
| `00:16:56.800 - 00:16:57.910` | once |
| `00:16:57.920 - 00:16:59.350` | other things have finished it will be |
| `00:16:59.360 - 00:17:01.030` | reawakened |
| `00:17:01.040 - 00:17:03.509` | finally when everything's okay it will |
| `00:17:03.519 - 00:17:05.029` | increment a counter and that counter |
| `00:17:05.039 - 00:17:06.789` | just tells how many transactions are |
| `00:17:06.799 - 00:17:09.510` | currently in progress |
| `00:17:09.520 - 00:17:10.710` | the end |
| `00:17:10.720 - 00:17:11.990` | operation |
| `00:17:12.000 - 00:17:14.390` | function end up begins by decrementing |
| `00:17:14.400 - 00:17:15.669` | this counter |
| `00:17:15.679 - 00:17:17.510` | if it goes to zero that means this is |
| `00:17:17.520 - 00:17:20.150` | the last transaction and we can go ahead |
| `00:17:20.160 - 00:17:21.829` | and do the commit |
| `00:17:21.839 - 00:17:24.309` | but if there are other transactions |
| `00:17:24.319 - 00:17:26.870` | currently in progress then the counter |
| `00:17:26.880 - 00:17:29.430` | will not go to zero so this end will |
| `00:17:29.440 - 00:17:31.190` | just return immediately without doing |
| `00:17:31.200 - 00:17:33.430` | anything further |
| `00:17:33.440 - 00:17:36.390` | the commit operation will then perform |
| `00:17:36.400 - 00:17:37.909` | all the writes to the disk it will |
| `00:17:37.919 - 00:17:40.150` | actually do writing of the blocks to the |
| `00:17:40.160 - 00:17:41.110` | disk |
| `00:17:41.120 - 00:17:43.430` | in the write operation there is no |
| `00:17:43.440 - 00:17:46.070` | actual write to the disk okay we just |
| `00:17:46.080 - 00:17:49.430` | save everything in memory |
| `00:17:49.440 - 00:17:50.390` | and |
| `00:17:50.400 - 00:17:53.350` | then we will empty the log and finally |
| `00:17:53.360 - 00:17:56.390` | since this transaction is committed and |
| `00:17:56.400 - 00:17:58.390` | we've released the log |
| `00:17:58.400 - 00:18:01.990` | blocks we can then wake up any sleeping |
| `00:18:02.000 - 00:18:05.350` | operations that are stuck on begin |
| `00:18:05.360 - 00:18:07.990` | i want to mention one thing by uh |
| `00:18:08.000 - 00:18:10.470` | saving the rights until the uh commit |
| `00:18:10.480 - 00:18:11.669` | operation |
| `00:18:11.679 - 00:18:13.750` | we are bunching them up so it's very |
| `00:18:13.760 - 00:18:17.430` | typical for a program to |
| `00:18:17.440 - 00:18:20.310` | write sequential bytes to a particular |
| `00:18:20.320 - 00:18:21.430` | disk block |
| `00:18:21.440 - 00:18:22.310` | and |
| `00:18:22.320 - 00:18:23.909` | we don't want to do a write to the disk |
| `00:18:23.919 - 00:18:26.070` | for each time a program writes a single |
| `00:18:26.080 - 00:18:29.029` | byte instead we just use the same buffer |
| `00:18:29.039 - 00:18:31.990` | in memory and they get saved up and then |
| `00:18:32.000 - 00:18:33.990` | at the time of the commit we do |
| `00:18:34.000 - 00:18:37.029` | a write that writes the entire block |
| `00:18:37.039 - 00:18:38.870` | okay now i'm going to walk through this |
| `00:18:38.880 - 00:18:41.990` | example here and i'm going to execute it |
| `00:18:42.000 - 00:18:44.150` | by updating the disk on this piece of |
| `00:18:44.160 - 00:18:45.669` | paper here |
| `00:18:45.679 - 00:18:47.029` | to make it more interesting i'm going to |
| `00:18:47.039 - 00:18:55.110` | change this from block 108 to block 106. |
| `00:18:55.120 - 00:18:58.390` | and we begin with the read of block 103 |
| `00:18:58.400 - 00:18:59.590` | well we |
| `00:18:59.600 - 00:19:00.549` | start |
| `00:19:00.559 - 00:19:03.430` | by searching the beak the buffer cache |
| `00:19:03.440 - 00:19:05.190` | for that block we find out it's not |
| `00:19:05.200 - 00:19:08.230` | there so we perform a disk read and |
| `00:19:08.240 - 00:19:10.630` | allocate a buffer and |
| `00:19:10.640 - 00:19:13.750` | read block 103 into that buffer so i'll |
| `00:19:13.760 - 00:19:15.669` | put an x right there |
| `00:19:15.679 - 00:19:18.630` | next we write that same |
| `00:19:18.640 - 00:19:19.590` | block |
| `00:19:19.600 - 00:19:20.630` | so |
| `00:19:20.640 - 00:19:23.590` | we will allocate an element in the log |
| `00:19:23.600 - 00:19:27.190` | i'll change this n to 1 |
| `00:19:27.200 - 00:19:31.430` | and this first element will set to 103. |
| `00:19:31.440 - 00:19:34.549` | this particular block will go into the |
| `00:19:34.559 - 00:19:36.710` | log right here but we don't write it |
| `00:19:36.720 - 00:19:39.669` | just yet the next thing is a read of |
| `00:19:39.679 - 00:19:41.350` | block 106 |
| `00:19:41.360 - 00:19:44.230` | so again the b read function will search |
| `00:19:44.240 - 00:19:45.669` | the buffer cache |
| `00:19:45.679 - 00:19:48.630` | find it's not there grab a free buffer |
| `00:19:48.640 - 00:19:50.549` | and |
| `00:19:50.559 - 00:19:54.070` | read in block 106 to this point |
| `00:19:54.080 - 00:19:57.110` | okay we'll put it right there |
| `00:19:57.120 - 00:19:59.029` | i forgot to mention that with this right |
| `00:19:59.039 - 00:20:02.470` | here we'll also pin this buffer so i'll |
| `00:20:02.480 - 00:20:04.950` | indicate that a buffer is pinned by |
| `00:20:04.960 - 00:20:06.549` | putting a little check mark underneath |
| `00:20:06.559 - 00:20:08.070` | it |
| `00:20:08.080 - 00:20:10.549` | okay now that next transaction |
| `00:20:10.559 - 00:20:13.510` | uh begins by reading 103 and that call |
| `00:20:13.520 - 00:20:16.310` | to be read we'll search the buffer cache |
| `00:20:16.320 - 00:20:18.789` | find that we already have it so a disk |
| `00:20:18.799 - 00:20:20.549` | read will not be necessary |
| `00:20:20.559 - 00:20:22.789` | it'll return a pointer to this buffer |
| `00:20:22.799 - 00:20:24.149` | right here |
| `00:20:24.159 - 00:20:26.230` | and the next thing we do is read block |
| `00:20:26.240 - 00:20:28.870` | 106 and again we have it in the buffer |
| `00:20:28.880 - 00:20:30.789` | cache so we return a pointer to this |
| `00:20:30.799 - 00:20:32.630` | buffer here |
| `00:20:32.640 - 00:20:35.350` | and then we do a write of |
| `00:20:35.360 - 00:20:36.630` | 106. |
| `00:20:36.640 - 00:20:38.470` | at that point uh |
| `00:20:38.480 - 00:20:39.750` | we will |
| `00:20:39.760 - 00:20:42.549` | search to see whether 106 is already in |
| `00:20:42.559 - 00:20:44.950` | the log we find out it's not so we |
| `00:20:44.960 - 00:20:47.110` | allocate another element in the log and |
| `00:20:47.120 - 00:20:48.950` | so we increment in |
| `00:20:48.960 - 00:20:53.029` | to 2 and we write 106 here |
| `00:20:53.039 - 00:20:58.390` | okay and we also pin that buffer |
| `00:20:58.400 - 00:21:00.470` | and finally we end |
| `00:21:00.480 - 00:21:02.390` | well i haven't shown the incrementing or |
| `00:21:02.400 - 00:21:04.789` | the decrementing of the counters but |
| `00:21:04.799 - 00:21:07.750` | this is not the last transaction so |
| `00:21:07.760 - 00:21:09.669` | we really don't do anything |
| `00:21:09.679 - 00:21:11.669` | and then the other thread continues and |
| `00:21:11.679 - 00:21:14.630` | it tries to write block 106. |
| `00:21:14.640 - 00:21:17.590` | so again what it will do is it will |
| `00:21:17.600 - 00:21:18.950` | search the |
| `00:21:18.960 - 00:21:20.710` | log and this time it finds that it is |
| `00:21:20.720 - 00:21:22.470` | there so it doesn't really have to do |
| `00:21:22.480 - 00:21:25.190` | anything in is not incremented |
| `00:21:25.200 - 00:21:27.750` | and this this thing is already pinned so |
| `00:21:27.760 - 00:21:29.669` | it really doesn't do anything |
| `00:21:29.679 - 00:21:30.390` | okay |
| `00:21:30.400 - 00:21:33.350` | and finally we come to the end operation |
| `00:21:33.360 - 00:21:35.350` | here and at that point we find it's the |
| `00:21:35.360 - 00:21:38.149` | last transaction and now we're ready to |
| `00:21:38.159 - 00:21:41.590` | commit so next let's take a look at what |
| `00:21:41.600 - 00:21:46.070` | happens in the commit |
| `00:21:46.080 - 00:21:49.110` | has two phases in the first |
| `00:21:49.120 - 00:21:51.270` | it's going to move the updated versions |
| `00:21:51.280 - 00:21:53.750` | of the blocks into the log area on the |
| `00:21:53.760 - 00:21:56.070` | disk and then in the second it's going |
| `00:21:56.080 - 00:21:56.870` | to |
| `00:21:56.880 - 00:22:00.549` | write to the main block area of the disk |
| `00:22:00.559 - 00:22:03.510` | so the commit operation will |
| `00:22:03.520 - 00:22:06.870` | begin by going through the array |
| `00:22:06.880 - 00:22:09.350` | in the log header |
| `00:22:09.360 - 00:22:11.750` | and for each block okay in our case |
| `00:22:11.760 - 00:22:13.909` | there are only two blocks block 103 and |
| `00:22:13.919 - 00:22:15.110` | 106 |
| `00:22:15.120 - 00:22:17.909` | we will copy that block from |
| `00:22:17.919 - 00:22:19.750` | the cache to |
| `00:22:19.760 - 00:22:21.510` | the log area |
| `00:22:21.520 - 00:22:23.270` | on the disk |
| `00:22:23.280 - 00:22:25.430` | okay and then at that point after |
| `00:22:25.440 - 00:22:28.149` | they've all been copied we will write |
| `00:22:28.159 - 00:22:31.669` | the header to the disk so i can indicate |
| `00:22:31.679 - 00:22:33.270` | the uh first |
| `00:22:33.280 - 00:22:34.789` | we're going through this loop here we've |
| `00:22:34.799 - 00:22:36.390` | got two elements in it |
| `00:22:36.400 - 00:22:39.510` | and we're going to write the |
| `00:22:39.520 - 00:22:42.390` | new version of this block and that block |
| `00:22:42.400 - 00:22:43.590` | maybe i should put a prime here to |
| `00:22:43.600 - 00:22:45.590` | indicate that it's been updated so we |
| `00:22:45.600 - 00:22:47.350` | write x prime |
| `00:22:47.360 - 00:22:48.230` | here |
| `00:22:48.240 - 00:22:50.630` | and we write y prime |
| `00:22:50.640 - 00:22:51.590` | there |
| `00:22:51.600 - 00:22:53.350` | okay and then |
| `00:22:53.360 - 00:22:56.390` | finally we write the header back to |
| `00:22:56.400 - 00:23:00.549` | this block on disk so at that point we |
| `00:23:00.559 - 00:23:03.270` | are committing okay if a crash occurs |
| `00:23:03.280 - 00:23:05.590` | before this right of the header to the |
| `00:23:05.600 - 00:23:06.470` | disk |
| `00:23:06.480 - 00:23:08.230` | then it will be as if none of those |
| `00:23:08.240 - 00:23:10.789` | transactions ever happened when we boot |
| `00:23:10.799 - 00:23:13.110` | back up and do the recovery on the other |
| `00:23:13.120 - 00:23:15.669` | hand if the crash happens after this |
| `00:23:15.679 - 00:23:17.350` | right then |
| `00:23:17.360 - 00:23:19.350` | these transactions will be |
| `00:23:19.360 - 00:23:22.149` | carried out and they will succeed okay |
| `00:23:22.159 - 00:23:24.630` | so now after the header has been updated |
| `00:23:24.640 - 00:23:26.789` | on the disk we're going to go through |
| `00:23:26.799 - 00:23:29.750` | the array and we're going to write the |
| `00:23:29.760 - 00:23:32.390` | block to the main block area so we're |
| `00:23:32.400 - 00:23:34.149` | going to go through the |
| `00:23:34.159 - 00:23:36.230` | header array here again there are two |
| `00:23:36.240 - 00:23:39.029` | elements and we're going to write x |
| `00:23:39.039 - 00:23:41.350` | prime and y prime back to the disk so at |
| `00:23:41.360 - 00:23:42.870` | that point we actually write it to the |
| `00:23:42.880 - 00:23:44.950` | main area here so i'm going to update it |
| `00:23:44.960 - 00:23:45.830` | by |
| `00:23:45.840 - 00:23:48.149` | indicating a prime here and a prime |
| `00:23:48.159 - 00:23:50.630` | there so now the main block area has the |
| `00:23:50.640 - 00:23:54.310` | updated versions of both x and y |
| `00:23:54.320 - 00:23:57.190` | and then we update the counter to zero |
| `00:23:57.200 - 00:24:02.230` | and write the header to disk |
| `00:24:02.240 - 00:24:03.750` | so i suppose i could show that here as |
| `00:24:03.760 - 00:24:04.630` | well |
| `00:24:04.640 - 00:24:06.630` | we change that to zero |
| `00:24:06.640 - 00:24:08.230` | so these are effectively |
| `00:24:08.240 - 00:24:09.430` | uh |
| `00:24:09.440 - 00:24:11.510` | erased or not important anymore and then |
| `00:24:11.520 - 00:24:14.149` | we write that block back here |
| `00:24:14.159 - 00:24:16.149` | the data is still here in the log but of |
| `00:24:16.159 - 00:24:18.710` | course it's no longer needed okay what |
| `00:24:18.720 - 00:24:20.549` | happens if we |
| `00:24:20.559 - 00:24:23.750` | have a crash and then we reboot |
| `00:24:23.760 - 00:24:24.950` | well |
| `00:24:24.960 - 00:24:25.909` | the |
| `00:24:25.919 - 00:24:28.149` | system begins by reading the log header |
| `00:24:28.159 - 00:24:30.070` | it doesn't know whether the previous |
| `00:24:30.080 - 00:24:33.430` | session uh ended with a crash or not |
| `00:24:33.440 - 00:24:35.190` | so regardless of whether there was a |
| `00:24:35.200 - 00:24:37.909` | crash in the past or not whenever we |
| `00:24:37.919 - 00:24:40.470` | boot the system we begin by reading in |
| `00:24:40.480 - 00:24:42.149` | the log header |
| `00:24:42.159 - 00:24:44.950` | if the counter is zero then we do |
| `00:24:44.960 - 00:24:47.029` | nothing any |
| `00:24:47.039 - 00:24:48.950` | in progress transaction |
| `00:24:48.960 - 00:24:51.590` | if there was one that was lost because |
| `00:24:51.600 - 00:24:53.029` | of a crash |
| `00:24:53.039 - 00:24:54.870` | will just be ignored so |
| `00:24:54.880 - 00:24:56.710` | that transaction |
| `00:24:56.720 - 00:24:58.789` | or that commit will not happen and all |
| `00:24:58.799 - 00:25:00.630` | the transactions that are involved in it |
| `00:25:00.640 - 00:25:02.710` | will be essentially lost |
| `00:25:02.720 - 00:25:06.149` | on the other hand if n is non-zero then |
| `00:25:06.159 - 00:25:09.029` | we have managed to |
| `00:25:09.039 - 00:25:11.430` | do this we have managed to write a |
| `00:25:11.440 - 00:25:13.990` | header to the disk at this point so we |
| `00:25:14.000 - 00:25:15.909` | need to replay the log and make sure |
| `00:25:15.919 - 00:25:18.630` | that everything got written to the main |
| `00:25:18.640 - 00:25:21.269` | block area and that's what we do here we |
| `00:25:21.279 - 00:25:23.590` | go through the array and for each |
| `00:25:23.600 - 00:25:25.510` | element in it we write |
| `00:25:25.520 - 00:25:28.630` | from the log area back to the main block |
| `00:25:28.640 - 00:25:30.230` | area so |
| `00:25:30.240 - 00:25:32.549` | uh i've already erased let's go back |
| `00:25:32.559 - 00:25:35.029` | here and say that we have a crash |
| `00:25:35.039 - 00:25:36.149` | okay so |
| `00:25:36.159 - 00:25:38.789` | the count is still 2 and we still have |
| `00:25:38.799 - 00:25:41.830` | 103 and 106. |
| `00:25:41.840 - 00:25:44.070` | in this header so we read in the header |
| `00:25:44.080 - 00:25:45.990` | we find out that it is not |
| `00:25:46.000 - 00:25:48.710` | zero so at that point we need to replay |
| `00:25:48.720 - 00:25:49.830` | the log |
| `00:25:49.840 - 00:25:51.590` | so we'll go through |
| `00:25:51.600 - 00:25:54.230` | and for each one of these we'll read in |
| `00:25:54.240 - 00:25:56.789` | this block here and write it to the |
| `00:25:56.799 - 00:26:00.950` | appropriate place in the main block area |
| `00:26:00.960 - 00:26:03.669` | and then once we've done that |
| `00:26:03.679 - 00:26:06.310` | we can set into zero and write the |
| `00:26:06.320 - 00:26:11.110` | header back out to disk with the zero |
| `00:26:11.120 - 00:26:13.350` | the commit operation is done in several |
| `00:26:13.360 - 00:26:14.789` | different functions i'll just mention |
| `00:26:14.799 - 00:26:16.549` | them commit |
| `00:26:16.559 - 00:26:18.950` | write log there's a right head |
| `00:26:18.960 - 00:26:20.870` | and then there's an install trans or |
| `00:26:20.880 - 00:26:23.029` | install transaction |
| `00:26:23.039 - 00:26:25.990` | the stuff that happens on the reboot |
| `00:26:26.000 - 00:26:27.269` | well um |
| `00:26:27.279 - 00:26:28.950` | we initially |
| `00:26:28.960 - 00:26:29.990` | uh |
| `00:26:30.000 - 00:26:32.230` | execute this init log function that will |
| `00:26:32.240 - 00:26:34.789` | initialize the variables of the |
| `00:26:34.799 - 00:26:35.990` | log |
| `00:26:36.000 - 00:26:38.950` | and that's called from fsnet file system |
| `00:26:38.960 - 00:26:39.909` | inet |
| `00:26:39.919 - 00:26:42.310` | that's called in the init process as one |
| `00:26:42.320 - 00:26:43.590` | of the first things it does the first |
| `00:26:43.600 - 00:26:45.750` | time it has a time slice |
| `00:26:45.760 - 00:26:49.669` | and it will invoke recover from log |
| `00:26:49.679 - 00:26:52.950` | install trends and it also calls read |
| `00:26:52.960 - 00:26:56.630` | head and right head |
| `00:26:56.640 - 00:26:57.990` | okay i think we're ready to take a look |
| `00:26:58.000 - 00:27:00.070` | at the code next |
| `00:27:00.080 - 00:27:03.029` | the file log.c begins with a comment and |
| `00:27:03.039 - 00:27:05.909` | then it gets into the data definitions |
| `00:27:05.919 - 00:27:08.549` | this structure called log header is what |
| `00:27:08.559 - 00:27:11.430` | i showed right here within plus the |
| `00:27:11.440 - 00:27:14.390` | array so here we see a field n and we |
| `00:27:14.400 - 00:27:17.269` | see the array log size is a fixed |
| `00:27:17.279 - 00:27:19.430` | constant parameter it tells how big to |
| `00:27:19.440 - 00:27:21.110` | make the log |
| `00:27:21.120 - 00:27:23.430` | all of the variables associated with the |
| `00:27:23.440 - 00:27:26.070` | logging are contained in this structure |
| `00:27:26.080 - 00:27:28.710` | called log and there's exactly one |
| `00:27:28.720 - 00:27:31.909` | instance of this structure also called |
| `00:27:31.919 - 00:27:32.950` | log |
| `00:27:32.960 - 00:27:35.909` | and we see a number of different things |
| `00:27:35.919 - 00:27:38.230` | we see a spin lock that protects these |
| `00:27:38.240 - 00:27:40.470` | two fields outstanding and committing |
| `00:27:40.480 - 00:27:41.590` | and then we see |
| `00:27:41.600 - 00:27:44.630` | start and size which tell where on the |
| `00:27:44.640 - 00:27:47.110` | disk the log will occur |
| `00:27:47.120 - 00:27:49.190` | okay these are constants and they don't |
| `00:27:49.200 - 00:27:50.470` | change |
| `00:27:50.480 - 00:27:53.190` | and we see a field called device |
| `00:27:53.200 - 00:27:55.669` | uh typically we'll have several devices |
| `00:27:55.679 - 00:27:58.789` | and each will have a separate log |
| `00:27:58.799 - 00:28:02.070` | in xv6 we only have one device so yet we |
| `00:28:02.080 - 00:28:05.190` | still have this field here |
| `00:28:05.200 - 00:28:06.549` | the |
| `00:28:06.559 - 00:28:07.990` | outstanding |
| `00:28:08.000 - 00:28:10.230` | field is a counter every time we do a |
| `00:28:10.240 - 00:28:11.909` | begin operation that counter is |
| `00:28:11.919 - 00:28:14.310` | incremented and every time we do an end |
| `00:28:14.320 - 00:28:16.789` | operation that counters decremented and |
| `00:28:16.799 - 00:28:19.029` | if it's zero we do a commit |
| `00:28:19.039 - 00:28:21.029` | if we are in the middle of committing |
| `00:28:21.039 - 00:28:23.430` | the committing flag will be set to true |
| `00:28:23.440 - 00:28:25.669` | and it will cause other begin operations |
| `00:28:25.679 - 00:28:27.750` | to wait |
| `00:28:27.760 - 00:28:30.070` | and finally we have an instance of the |
| `00:28:30.080 - 00:28:32.549` | log header structure itself |
| `00:28:32.559 - 00:28:33.750` | right here |
| `00:28:33.760 - 00:28:35.909` | okay now let's take a look at the init |
| `00:28:35.919 - 00:28:38.789` | log function this is called on kernel |
| `00:28:38.799 - 00:28:40.070` | startup |
| `00:28:40.080 - 00:28:41.350` | in particular it's called from this |
| `00:28:41.360 - 00:28:44.549` | function fs init which itself is called |
| `00:28:44.559 - 00:28:47.110` | from uh forecret the first time that |
| `00:28:47.120 - 00:28:49.350` | fork rep returns |
| `00:28:49.360 - 00:28:51.269` | we're going to be accessing the disk |
| `00:28:51.279 - 00:28:53.750` | possibly if we do some recovery and that |
| `00:28:53.760 - 00:28:56.310` | might involve sleeping so we need to be |
| `00:28:56.320 - 00:28:59.669` | running in a user process |
| `00:28:59.679 - 00:29:01.590` | so that's why we are |
| `00:29:01.600 - 00:29:02.870` | doing this |
| `00:29:02.880 - 00:29:06.870` | from within you poor grant |
| `00:29:06.880 - 00:29:09.029` | the first thing we do is initialize the |
| `00:29:09.039 - 00:29:11.430` | spin lock called block and then we |
| `00:29:11.440 - 00:29:14.630` | initialize the fixed fields the start |
| `00:29:14.640 - 00:29:17.029` | size and device fields |
| `00:29:17.039 - 00:29:20.710` | we're past the device number of which we |
| `00:29:20.720 - 00:29:22.789` | are initializing as well as a pointer to |
| `00:29:22.799 - 00:29:24.870` | the super block previously the super |
| `00:29:24.880 - 00:29:26.389` | block has been read in |
| `00:29:26.399 - 00:29:29.350` | and here we just pick up values from the |
| `00:29:29.360 - 00:29:32.149` | super block to initialize the start and |
| `00:29:32.159 - 00:29:35.269` | size fields and we initialize the device |
| `00:29:35.279 - 00:29:36.230` | field |
| `00:29:36.240 - 00:29:38.310` | and then finally we call recover from |
| `00:29:38.320 - 00:29:39.269` | log |
| `00:29:39.279 - 00:29:41.510` | this will read the header block in and |
| `00:29:41.520 - 00:29:44.630` | if n is non-zero it will do the recovery |
| `00:29:44.640 - 00:29:46.310` | stuff |
| `00:29:46.320 - 00:29:48.149` | okay let's take a look at the begin |
| `00:29:48.159 - 00:29:49.669` | operation function |
| `00:29:49.679 - 00:29:51.110` | this is called at the beginning of every |
| `00:29:51.120 - 00:29:52.310` | transaction |
| `00:29:52.320 - 00:29:53.909` | this says called at the start of every |
| `00:29:53.919 - 00:29:56.789` | file system system call well every file |
| `00:29:56.799 - 00:29:58.870` | system system call is a single |
| `00:29:58.880 - 00:30:02.549` | transaction so that's why that says that |
| `00:30:02.559 - 00:30:03.909` | essentially what it does is it |
| `00:30:03.919 - 00:30:06.230` | increments this outstanding counter to |
| `00:30:06.240 - 00:30:09.029` | indicate that yet another transaction is |
| `00:30:09.039 - 00:30:10.950` | in progress |
| `00:30:10.960 - 00:30:14.230` | this counter is protected by a spin lock |
| `00:30:14.240 - 00:30:17.830` | and here we acquire that spin lock |
| `00:30:17.840 - 00:30:20.470` | and after we do the increment we release |
| `00:30:20.480 - 00:30:23.190` | the spin lock and we return we break out |
| `00:30:23.200 - 00:30:26.389` | of this loop and then return |
| `00:30:26.399 - 00:30:28.630` | but before we can increment the counter |
| `00:30:28.640 - 00:30:31.029` | we need to perform a couple of checks |
| `00:30:31.039 - 00:30:32.710` | first we check to see whether some other |
| `00:30:32.720 - 00:30:34.710` | thread is in the middle of committing |
| `00:30:34.720 - 00:30:37.669` | and if so we go to sleep |
| `00:30:37.679 - 00:30:40.789` | the first argument to the sleep function |
| `00:30:40.799 - 00:30:43.590` | is the channel and we use as a channel |
| `00:30:43.600 - 00:30:45.990` | the address of the log structure itself |
| `00:30:46.000 - 00:30:48.230` | and of course the second argument is the |
| `00:30:48.240 - 00:30:49.830` | spin lock that will be temporarily |
| `00:30:49.840 - 00:30:52.230` | released while we are asleep and |
| `00:30:52.240 - 00:30:55.430` | reacquired once we are reawakened |
| `00:30:55.440 - 00:30:57.830` | so if nobody's committing then we make a |
| `00:30:57.840 - 00:30:59.590` | check to make sure that there is enough |
| `00:30:59.600 - 00:31:02.230` | space in the log for |
| `00:31:02.240 - 00:31:04.310` | the blocks that will be involved in this |
| `00:31:04.320 - 00:31:06.310` | transaction so we look at the current |
| `00:31:06.320 - 00:31:08.070` | value of n |
| `00:31:08.080 - 00:31:09.990` | and we |
| `00:31:10.000 - 00:31:12.870` | have this parameter max op blocks which |
| `00:31:12.880 - 00:31:15.110` | happens to be 10 and that's the maximum |
| `00:31:15.120 - 00:31:16.870` | number of blocks that any transaction is |
| `00:31:16.880 - 00:31:18.950` | allowed to update |
| `00:31:18.960 - 00:31:19.909` | so |
| `00:31:19.919 - 00:31:22.070` | we look at how many |
| `00:31:22.080 - 00:31:24.549` | transactions are currently in progress |
| `00:31:24.559 - 00:31:27.029` | plus this current transaction that we're |
| `00:31:27.039 - 00:31:28.310` | trying to start |
| `00:31:28.320 - 00:31:29.509` | and we |
| `00:31:29.519 - 00:31:31.190` | figure that they might all use the |
| `00:31:31.200 - 00:31:33.350` | maximum number and we're asking whether |
| `00:31:33.360 - 00:31:35.590` | that would exceed the size of the log |
| `00:31:35.600 - 00:31:38.950` | and if so again we go to sleep and we |
| `00:31:38.960 - 00:31:41.190` | use the address of the log structure as |
| `00:31:41.200 - 00:31:41.909` | the |
| `00:31:41.919 - 00:31:43.029` | channel |
| `00:31:43.039 - 00:31:44.789` | and after somebody commits after some |
| `00:31:44.799 - 00:31:47.990` | other thread commits will get reawakened |
| `00:31:48.000 - 00:31:49.909` | and if both those are okay in other |
| `00:31:49.919 - 00:31:51.909` | words if both those tests are false then |
| `00:31:51.919 - 00:31:53.269` | we can go ahead and increment the |
| `00:31:53.279 - 00:31:56.389` | outstanding counter and return |
| `00:31:56.399 - 00:31:58.549` | after we wake up we will basically need |
| `00:31:58.559 - 00:32:00.230` | to check and make sure that both these |
| `00:32:00.240 - 00:32:02.149` | conditions are true just because we've |
| `00:32:02.159 - 00:32:04.389` | been reawakened we don't know exactly |
| `00:32:04.399 - 00:32:06.630` | what's true and what's not true so once |
| `00:32:06.640 - 00:32:08.230` | we wake up from either of these two |
| `00:32:08.240 - 00:32:11.190` | sleeps this while loop will repeat and |
| `00:32:11.200 - 00:32:12.549` | we won't be |
| `00:32:12.559 - 00:32:15.269` | incrementing the outstanding unless we |
| `00:32:15.279 - 00:32:17.750` | pass this test and this test while |
| `00:32:17.760 - 00:32:20.149` | holding the lock and if we pass both of |
| `00:32:20.159 - 00:32:22.070` | these then we can go ahead and increment |
| `00:32:22.080 - 00:32:24.470` | the outstanding counter and release the |
| `00:32:24.480 - 00:32:26.950` | lock and return |
| `00:32:26.960 - 00:32:28.950` | now let's take a look at this function |
| `00:32:28.960 - 00:32:31.350` | log right which is passed a pointer to a |
| `00:32:31.360 - 00:32:32.310` | buffer |
| `00:32:32.320 - 00:32:35.110` | in the comment here we have an example |
| `00:32:35.120 - 00:32:37.430` | we would first call b read |
| `00:32:37.440 - 00:32:39.909` | with a block number to read in the data |
| `00:32:39.919 - 00:32:41.590` | from the disk and that gives us a |
| `00:32:41.600 - 00:32:44.710` | pointer to a buffer then we modify the |
| `00:32:44.720 - 00:32:47.509` | data in that buffer and after that we |
| `00:32:47.519 - 00:32:50.710` | call this function log right to |
| `00:32:50.720 - 00:32:52.470` | write it out to the disk it doesn't |
| `00:32:52.480 - 00:32:54.230` | actually do the write but it schedules |
| `00:32:54.240 - 00:32:56.389` | it we might do some more modification |
| `00:32:56.399 - 00:32:58.950` | and call log write again but ultimately |
| `00:32:58.960 - 00:33:01.029` | we need to release this buffer and we |
| `00:33:01.039 - 00:33:02.870` | call b release |
| `00:33:02.880 - 00:33:05.110` | so the caller has modified the data in |
| `00:33:05.120 - 00:33:07.190` | the buffer and is done |
| `00:33:07.200 - 00:33:08.950` | we record the |
| `00:33:08.960 - 00:33:11.990` | block number in the log we also pin this |
| `00:33:12.000 - 00:33:13.110` | buffer |
| `00:33:13.120 - 00:33:14.310` | we do that |
| `00:33:14.320 - 00:33:16.230` | by increasing the reference count for |
| `00:33:16.240 - 00:33:18.389` | this buffer we saw that in the previous |
| `00:33:18.399 - 00:33:19.269` | video |
| `00:33:19.279 - 00:33:21.590` | and then the commit operation later will |
| `00:33:21.600 - 00:33:25.110` | actually perform the right to the disk |
| `00:33:25.120 - 00:33:26.149` | so |
| `00:33:26.159 - 00:33:28.070` | here's the code we're going to be |
| `00:33:28.080 - 00:33:30.230` | modifying the log so we need to acquire |
| `00:33:30.240 - 00:33:31.669` | the spin lock |
| `00:33:31.679 - 00:33:34.310` | and we acquire that here and then before |
| `00:33:34.320 - 00:33:37.990` | we return we release the spin lock |
| `00:33:38.000 - 00:33:40.149` | we do a couple of checks first |
| `00:33:40.159 - 00:33:42.149` | we make sure that we have enough room in |
| `00:33:42.159 - 00:33:44.549` | the log we've already checked that with |
| `00:33:44.559 - 00:33:47.029` | the begin operation but we do another |
| `00:33:47.039 - 00:33:48.789` | check right here to make sure that we |
| `00:33:48.799 - 00:33:50.630` | have enough room in the log |
| `00:33:50.640 - 00:33:52.470` | and we also make sure that we are in the |
| `00:33:52.480 - 00:33:55.190` | middle of some transaction |
| `00:33:55.200 - 00:33:56.789` | the begin operation will have |
| `00:33:56.799 - 00:33:59.029` | incremented this counter and we make |
| `00:33:59.039 - 00:34:01.990` | sure that uh it's not zero |
| `00:34:02.000 - 00:34:05.430` | so if everything's okay then we scan |
| `00:34:05.440 - 00:34:07.830` | through the array looking to see whether |
| `00:34:07.840 - 00:34:09.190` | we've got |
| `00:34:09.200 - 00:34:11.030` | an entry in the array that happens to |
| `00:34:11.040 - 00:34:13.349` | match this particular block number |
| `00:34:13.359 - 00:34:15.030` | so from 0 to |
| `00:34:15.040 - 00:34:18.310` | n minus 1 we ask is the array does the |
| `00:34:18.320 - 00:34:19.750` | array contain |
| `00:34:19.760 - 00:34:22.550` | an entry that has the block that we are |
| `00:34:22.560 - 00:34:25.829` | currently looking at and if so we set i |
| `00:34:25.839 - 00:34:29.030` | to that otherwise we set i to n that is |
| `00:34:29.040 - 00:34:31.669` | we exit when i becomes equal to n that's |
| `00:34:31.679 - 00:34:34.950` | the next available entry |
| `00:34:34.960 - 00:34:37.430` | then we update the array |
| `00:34:37.440 - 00:34:39.510` | okay if i comes out if we didn't find it |
| `00:34:39.520 - 00:34:42.869` | then i is equal to n so we uh update the |
| `00:34:42.879 - 00:34:44.629` | next available entry by storing the |
| `00:34:44.639 - 00:34:46.710` | block number if we already found it then |
| `00:34:46.720 - 00:34:48.629` | we just overwrite it so that doesn't |
| `00:34:48.639 - 00:34:50.790` | really do anything |
| `00:34:50.800 - 00:34:53.030` | if i is equal to n |
| `00:34:53.040 - 00:34:55.829` | then that means we did not find it and |
| `00:34:55.839 - 00:34:57.589` | we're adding a new entry at the element |
| `00:34:57.599 - 00:35:00.230` | at the end of the array so we need to |
| `00:35:00.240 - 00:35:01.670` | increment in |
| `00:35:01.680 - 00:35:03.349` | and we also |
| `00:35:03.359 - 00:35:04.230` | invoke |
| `00:35:04.240 - 00:35:06.550` | buffer pin on this particular buffer to |
| `00:35:06.560 - 00:35:09.190` | make sure that we will not |
| `00:35:09.200 - 00:35:12.230` | reuse this buffer for some other block |
| `00:35:12.240 - 00:35:13.670` | until after it's been written to the |
| `00:35:13.680 - 00:35:14.630` | disk |
| `00:35:14.640 - 00:35:17.030` | and then again we release the spin lock |
| `00:35:17.040 - 00:35:18.870` | and return |
| `00:35:18.880 - 00:35:21.190` | here's the function end operation |
| `00:35:21.200 - 00:35:22.870` | it's called at the end of each file |
| `00:35:22.880 - 00:35:25.190` | system system call that is it's called |
| `00:35:25.200 - 00:35:27.430` | at the end of every transaction and if |
| `00:35:27.440 - 00:35:29.430` | this is the last transaction and there |
| `00:35:29.440 - 00:35:31.750` | are no other outstanding transactions |
| `00:35:31.760 - 00:35:33.430` | then it will perform the commit |
| `00:35:33.440 - 00:35:36.310` | operation |
| `00:35:36.320 - 00:35:38.630` | so we have this counter outstanding |
| `00:35:38.640 - 00:35:40.230` | which tells how many transactions are |
| `00:35:40.240 - 00:35:42.470` | currently in progress and we are |
| `00:35:42.480 - 00:35:45.270` | decrementing it right here this counter |
| `00:35:45.280 - 00:35:47.030` | and the committing flag are both |
| `00:35:47.040 - 00:35:49.589` | protected by the lock so we acquire the |
| `00:35:49.599 - 00:35:51.109` | spin lock here |
| `00:35:51.119 - 00:35:55.109` | and then we will release it down here |
| `00:35:55.119 - 00:35:57.750` | if the outstanding counter goes to zero |
| `00:35:57.760 - 00:35:59.829` | after we decrement it then we need to go |
| `00:35:59.839 - 00:36:01.510` | ahead and do the commit |
| `00:36:01.520 - 00:36:03.670` | okay but we better not already be doing |
| `00:36:03.680 - 00:36:05.829` | a commit because this transaction was in |
| `00:36:05.839 - 00:36:07.910` | progress up until this point so we do a |
| `00:36:07.920 - 00:36:09.670` | little error check here to make sure we |
| `00:36:09.680 - 00:36:13.030` | aren't committing already but if the |
| `00:36:13.040 - 00:36:14.630` | outstanding counter is decremented to |
| `00:36:14.640 - 00:36:17.910` | zero then we set this do commit flag |
| `00:36:17.920 - 00:36:19.589` | right that's a local variable here do |
| `00:36:19.599 - 00:36:22.150` | commit and we check it down here and if |
| `00:36:22.160 - 00:36:24.630` | it's true we'll execute this code |
| `00:36:24.640 - 00:36:27.430` | and we also set the committing flag so |
| `00:36:27.440 - 00:36:29.910` | that we know so that every other threads |
| `00:36:29.920 - 00:36:32.230` | will know that we are committing |
| `00:36:32.240 - 00:36:34.550` | however if we did not go to zero |
| `00:36:34.560 - 00:36:35.990` | then it means there are other |
| `00:36:36.000 - 00:36:38.950` | transactions still running and there may |
| `00:36:38.960 - 00:36:40.790` | also be transactions that are waiting |
| `00:36:40.800 - 00:36:41.589` | for |
| `00:36:41.599 - 00:36:44.150` | uh enough log space to begin so there |
| `00:36:44.160 - 00:36:45.030` | could be |
| `00:36:45.040 - 00:36:47.910` | threads that are in the begin operation |
| `00:36:47.920 - 00:36:51.030` | that are sleeping so we do a wake-up |
| `00:36:51.040 - 00:36:53.750` | here to wake up any sleeping begin |
| `00:36:53.760 - 00:36:56.550` | operations |
| `00:36:56.560 - 00:36:57.750` | we check to make sure that there's |
| `00:36:57.760 - 00:37:00.150` | enough reserved log space based on how |
| `00:37:00.160 - 00:37:03.270` | many transactions are in action |
| `00:37:03.280 - 00:37:05.349` | and since we are now finished with this |
| `00:37:05.359 - 00:37:06.470` | transaction |
| `00:37:06.480 - 00:37:07.990` | it means that we no longer have to |
| `00:37:08.000 - 00:37:09.990` | reserve uh additional log space and it |
| `00:37:10.000 - 00:37:12.230` | may be that uh there's enough log space |
| `00:37:12.240 - 00:37:14.870` | for one of these transactions to begin |
| `00:37:14.880 - 00:37:17.670` | so that's why we're calling wake up here |
| `00:37:17.680 - 00:37:20.150` | and then we release the lock |
| `00:37:20.160 - 00:37:22.390` | now if we need to do a commit |
| `00:37:22.400 - 00:37:24.390` | then we are going to call this commit |
| `00:37:24.400 - 00:37:25.990` | function which i'll talk about in just |
| `00:37:26.000 - 00:37:27.109` | one second |
| `00:37:27.119 - 00:37:28.870` | and then we're going to clear the |
| `00:37:28.880 - 00:37:31.430` | committing flag after it completes |
| `00:37:31.440 - 00:37:33.990` | we have to reacquire the lock and then |
| `00:37:34.000 - 00:37:36.150` | release it |
| `00:37:36.160 - 00:37:38.710` | in order to modify this flag and again |
| `00:37:38.720 - 00:37:41.349` | we also call the wake up function to |
| `00:37:41.359 - 00:37:42.710` | wake up any |
| `00:37:42.720 - 00:37:44.630` | begin operations that happen to be |
| `00:37:44.640 - 00:37:46.069` | sleeping |
| `00:37:46.079 - 00:37:49.030` | okay here is the commit function it's |
| `00:37:49.040 - 00:37:51.589` | called from exactly one place |
| `00:37:51.599 - 00:37:55.109` | and it first checks the counter in |
| `00:37:55.119 - 00:37:57.270` | now presumably there were some rights in |
| `00:37:57.280 - 00:37:59.829` | this transaction and it will be greater |
| `00:37:59.839 - 00:38:02.230` | than zero but if it happens to be the |
| `00:38:02.240 - 00:38:04.630` | case that there were no |
| `00:38:04.640 - 00:38:06.069` | calls to |
| `00:38:06.079 - 00:38:07.990` | the right log |
| `00:38:08.000 - 00:38:10.470` | the log write function then now this |
| `00:38:10.480 - 00:38:11.910` | would be 0 and we don't need to do |
| `00:38:11.920 - 00:38:13.030` | anything |
| `00:38:13.040 - 00:38:15.670` | so here we're going to call the right |
| `00:38:15.680 - 00:38:17.430` | log function |
| `00:38:17.440 - 00:38:19.910` | okay previously i talked about the log |
| `00:38:19.920 - 00:38:21.190` | write function it's a little bit |
| `00:38:21.200 - 00:38:23.349` | confusing but the right log function |
| `00:38:23.359 - 00:38:26.150` | which is right here is only called from |
| `00:38:26.160 - 00:38:29.349` | this one place |
| `00:38:29.359 - 00:38:30.710` | that will |
| `00:38:30.720 - 00:38:33.109` | write all the blocks from the buffer |
| `00:38:33.119 - 00:38:34.069` | cache |
| `00:38:34.079 - 00:38:36.710` | to the log area on the disk |
| `00:38:36.720 - 00:38:38.230` | and then we call this function right |
| `00:38:38.240 - 00:38:40.390` | head which will copy that header |
| `00:38:40.400 - 00:38:43.670` | structure to the |
| `00:38:43.680 - 00:38:46.470` | disk block where it's in the log file on |
| `00:38:46.480 - 00:38:49.190` | the disk and that performs the commit |
| `00:38:49.200 - 00:38:51.589` | after this completes then this |
| `00:38:51.599 - 00:38:54.470` | transaction or all of these transactions |
| `00:38:54.480 - 00:38:56.390` | are committed and even if we crash |
| `00:38:56.400 - 00:38:57.910` | directly after that |
| `00:38:57.920 - 00:39:00.630` | the log will be replayed and we will do |
| `00:39:00.640 - 00:39:03.430` | the rights to the disk if we crash |
| `00:39:03.440 - 00:39:07.270` | before this right head completes then |
| `00:39:07.280 - 00:39:09.510` | it will be as if no |
| `00:39:09.520 - 00:39:12.790` | transactions have occurred |
| `00:39:12.800 - 00:39:14.390` | then we call |
| `00:39:14.400 - 00:39:17.670` | install transaction and that will run |
| `00:39:17.680 - 00:39:20.790` | through the log and it will write each |
| `00:39:20.800 - 00:39:21.829` | block |
| `00:39:21.839 - 00:39:24.630` | to the actual location in the main block |
| `00:39:24.640 - 00:39:26.790` | area of the disk |
| `00:39:26.800 - 00:39:28.950` | and then we set in to zero |
| `00:39:28.960 - 00:39:30.950` | in the in memory copy of the |
| `00:39:30.960 - 00:39:33.270` | log header structure and then write that |
| `00:39:33.280 - 00:39:35.670` | log header structure back to the disk |
| `00:39:35.680 - 00:39:37.990` | and that essentially sits in on the disk |
| `00:39:38.000 - 00:39:39.270` | back to zero |
| `00:39:39.280 - 00:39:41.990` | and at that point the commit is done but |
| `00:39:42.000 - 00:39:44.230` | now let's look at this write log |
| `00:39:44.240 - 00:39:46.790` | function and as i said it's called only |
| `00:39:46.800 - 00:39:47.829` | from |
| `00:39:47.839 - 00:39:51.430` | here in commit |
| `00:39:51.440 - 00:39:55.109` | what it's going to do is go through the |
| `00:39:55.119 - 00:39:57.990` | array in the header okay so we're going |
| `00:39:58.000 - 00:39:58.630` | to |
| `00:39:58.640 - 00:39:59.750` | go through |
| `00:39:59.760 - 00:40:00.550` | uh |
| `00:40:00.560 - 00:40:02.470` | this array here |
| `00:40:02.480 - 00:40:05.910` | from 0 on up to n minus 1 and for each |
| `00:40:05.920 - 00:40:08.710` | one of those we are going to |
| `00:40:08.720 - 00:40:10.710` | uh |
| `00:40:10.720 - 00:40:14.470` | write it to the log area of the disk |
| `00:40:14.480 - 00:40:17.990` | okay so we're going to |
| `00:40:18.000 - 00:40:19.349` | write |
| `00:40:19.359 - 00:40:22.150` | from the buffer cache okay |
| `00:40:22.160 - 00:40:24.630` | so for example 103 is the first one we |
| `00:40:24.640 - 00:40:25.589` | look at |
| `00:40:25.599 - 00:40:28.230` | we're going we've got that it's pinned |
| `00:40:28.240 - 00:40:30.710` | okay so it will be in the cache |
| `00:40:30.720 - 00:40:32.710` | and we're going to write it to this |
| `00:40:32.720 - 00:40:35.270` | location here and that's what's going on |
| `00:40:35.280 - 00:40:38.069` | here so the first call uh well let's |
| `00:40:38.079 - 00:40:39.589` | look at this call |
| `00:40:39.599 - 00:40:41.910` | from that's the from location |
| `00:40:41.920 - 00:40:45.829` | and we are |
| `00:40:45.839 - 00:40:48.950` | looking at the log okay here's the array |
| `00:40:48.960 - 00:40:52.630` | and tail is our index and |
| `00:40:52.640 - 00:40:54.710` | we will find it in the cache because it |
| `00:40:54.720 - 00:40:56.710` | was pinned okay |
| `00:40:56.720 - 00:40:58.790` | and so this just gives us a pointer to |
| `00:40:58.800 - 00:41:00.150` | the buffer |
| `00:41:00.160 - 00:41:02.309` | okay it doesn't actually read from the |
| `00:41:02.319 - 00:41:04.309` | disk |
| `00:41:04.319 - 00:41:06.390` | this read function right here |
| `00:41:06.400 - 00:41:09.190` | is needed uh because we are going to do |
| `00:41:09.200 - 00:41:12.630` | a write every call to b write needs to |
| `00:41:12.640 - 00:41:15.990` | be preceded by a b read to |
| `00:41:16.000 - 00:41:17.270` | get the buffer |
| `00:41:17.280 - 00:41:18.069` | so |
| `00:41:18.079 - 00:41:20.710` | here we are asking uh |
| `00:41:20.720 - 00:41:23.190` | using the index tail here |
| `00:41:23.200 - 00:41:26.950` | for the block number in the log area so |
| `00:41:26.960 - 00:41:29.030` | that will give us the block number in |
| `00:41:29.040 - 00:41:31.829` | this case 0 1 2 3 for this particular |
| `00:41:31.839 - 00:41:32.790` | block |
| `00:41:32.800 - 00:41:35.430` | and we will read it in although that's |
| `00:41:35.440 - 00:41:37.349` | not really |
| `00:41:37.359 - 00:41:39.990` | uh i suppose could be optimized in some |
| `00:41:40.000 - 00:41:41.349` | other kernel |
| `00:41:41.359 - 00:41:44.390` | and then we're going to be writing it uh |
| `00:41:44.400 - 00:41:47.349` | here so before we write it we move all |
| `00:41:47.359 - 00:41:48.550` | of the data |
| `00:41:48.560 - 00:41:50.630` | from the |
| `00:41:50.640 - 00:41:54.150` | buffer that came out of the cache okay |
| `00:41:54.160 - 00:41:57.670` | to this new buffer that we just got okay |
| `00:41:57.680 - 00:42:00.870` | and so we move an entire block into the |
| `00:42:00.880 - 00:42:03.670` | two buffer and then we write it to the |
| `00:42:03.680 - 00:42:04.870` | log area |
| `00:42:04.880 - 00:42:07.430` | and then we release both buffers both |
| `00:42:07.440 - 00:42:08.550` | the |
| `00:42:08.560 - 00:42:10.950` | cache buffer and the cache and we |
| `00:42:10.960 - 00:42:12.710` | created a new buffer in the cache for |
| `00:42:12.720 - 00:42:14.390` | this particular |
| `00:42:14.400 - 00:42:16.790` | writing operation and we release that as |
| `00:42:16.800 - 00:42:19.829` | well |
| `00:42:19.839 - 00:42:22.309` | so to review the commit function |
| `00:42:22.319 - 00:42:23.910` | we are going to |
| `00:42:23.920 - 00:42:26.710` | write the log that is we are going to |
| `00:42:26.720 - 00:42:30.870` | take every block that is indicated by |
| `00:42:30.880 - 00:42:32.870` | the array in this case we have two of |
| `00:42:32.880 - 00:42:34.790` | them and then we're going to copy that |
| `00:42:34.800 - 00:42:37.910` | data two blocks on the disk in the log |
| `00:42:37.920 - 00:42:39.270` | area |
| `00:42:39.280 - 00:42:41.030` | and then in the second step we are going |
| `00:42:41.040 - 00:42:44.069` | to write the header so we copy this data |
| `00:42:44.079 - 00:42:46.150` | structure here to |
| `00:42:46.160 - 00:42:48.630` | this block here |
| `00:42:48.640 - 00:42:51.030` | then we do the install transaction |
| `00:42:51.040 - 00:42:52.069` | function |
| `00:42:52.079 - 00:42:53.990` | which will go ahead and perform the |
| `00:42:54.000 - 00:42:57.670` | rights to the main block area |
| `00:42:57.680 - 00:43:00.309` | then we set into 0 and write the header |
| `00:43:00.319 - 00:43:02.470` | a second time |
| `00:43:02.480 - 00:43:05.030` | so let's take a look at the right head |
| `00:43:05.040 - 00:43:07.190` | function |
| `00:43:07.200 - 00:43:08.790` | here it is |
| `00:43:08.800 - 00:43:10.870` | and essentially what it's going to do |
| `00:43:10.880 - 00:43:14.150` | is write the in memory log header to the |
| `00:43:14.160 - 00:43:15.109` | disk |
| `00:43:15.119 - 00:43:17.109` | the comment says this is the true point |
| `00:43:17.119 - 00:43:19.349` | at which the current transaction commits |
| `00:43:19.359 - 00:43:22.470` | well that applies to the first use of |
| `00:43:22.480 - 00:43:24.390` | right head here but i don't think it |
| `00:43:24.400 - 00:43:26.790` | really applies to the second use |
| `00:43:26.800 - 00:43:28.230` | but in any case |
| `00:43:28.240 - 00:43:30.710` | what it's going to do is get a buffer |
| `00:43:30.720 - 00:43:32.230` | for the |
| `00:43:32.240 - 00:43:34.510` | appropriate block on the disk okay |
| `00:43:34.520 - 00:43:36.870` | log.start that's |
| `00:43:36.880 - 00:43:39.510` | this block number here it would be 0 1 2 |
| `00:43:39.520 - 00:43:40.950` | it would be 2 |
| `00:43:40.960 - 00:43:43.990` | and once we have a buffer pointed to by |
| `00:43:44.000 - 00:43:46.870` | buff we're going to copy |
| `00:43:46.880 - 00:43:48.230` | the |
| `00:43:48.240 - 00:43:51.430` | n and copy the array into |
| `00:43:51.440 - 00:43:53.670` | that buffer and then we're going to |
| `00:43:53.680 - 00:43:57.430` | write it and release the buffer okay |
| `00:43:57.440 - 00:43:58.470` | so |
| `00:43:58.480 - 00:44:02.150` | more precisely we're calling b read to |
| `00:44:02.160 - 00:44:03.270` | read in |
| `00:44:03.280 - 00:44:06.150` | this disk block into a buffer so it's |
| `00:44:06.160 - 00:44:07.829` | going to actually read this |
| `00:44:07.839 - 00:44:09.190` | into some |
| `00:44:09.200 - 00:44:11.109` | buffer in the buffer cache |
| `00:44:11.119 - 00:44:13.349` | we don't really need to perform that |
| `00:44:13.359 - 00:44:14.870` | read and perhaps some other kernels |
| `00:44:14.880 - 00:44:18.790` | could optimize that away but in any case |
| `00:44:18.800 - 00:44:20.870` | then we get a pointer |
| `00:44:20.880 - 00:44:23.589` | into the data of that buffer okay into |
| `00:44:23.599 - 00:44:24.550` | the |
| `00:44:24.560 - 00:44:27.270` | block part of that buffer and we treat |
| `00:44:27.280 - 00:44:29.270` | that as a pointer to one of these log |
| `00:44:29.280 - 00:44:31.270` | header structures and then we're going |
| `00:44:31.280 - 00:44:34.550` | to copy first we copy in from the in |
| `00:44:34.560 - 00:44:37.990` | memory log header to this area in the |
| `00:44:38.000 - 00:44:40.630` | buffer and then we go through the array |
| `00:44:40.640 - 00:44:44.069` | up to n minus one and copy from the log |
| `00:44:44.079 - 00:44:47.349` | header structure here to the |
| `00:44:47.359 - 00:44:50.470` | uh buffer that we have just allocated |
| `00:44:50.480 - 00:44:52.150` | and then we write the buffer and release |
| `00:44:52.160 - 00:44:54.309` | the buffer |
| `00:44:54.319 - 00:44:55.910` | now let's take a look at this install |
| `00:44:55.920 - 00:44:57.990` | transaction function |
| `00:44:58.000 - 00:44:59.990` | it says copy committed blocks from the |
| `00:45:00.000 - 00:45:02.470` | log to their home location so what it's |
| `00:45:02.480 - 00:45:04.870` | going to do it's going to go through the |
| `00:45:04.880 - 00:45:08.230` | array the header right here and for each |
| `00:45:08.240 - 00:45:09.750` | block in the log |
| `00:45:09.760 - 00:45:12.069` | like this one right here it will copy it |
| `00:45:12.079 - 00:45:15.030` | to its home location in the main block |
| `00:45:15.040 - 00:45:17.589` | area and then it will do the next block |
| `00:45:17.599 - 00:45:20.309` | and so on |
| `00:45:20.319 - 00:45:22.790` | this function is called in two places |
| `00:45:22.800 - 00:45:25.670` | it's called when the commit happens |
| `00:45:25.680 - 00:45:29.190` | and it's called at startup okay so if |
| `00:45:29.200 - 00:45:31.349` | we're calling it during the commit |
| `00:45:31.359 - 00:45:32.470` | operation |
| `00:45:32.480 - 00:45:34.390` | from the commit function recovering will |
| `00:45:34.400 - 00:45:35.670` | be false |
| `00:45:35.680 - 00:45:37.190` | if we're calling it on startup |
| `00:45:37.200 - 00:45:39.829` | recovering will be true |
| `00:45:39.839 - 00:45:41.910` | okay so here's what it's going to do we |
| `00:45:41.920 - 00:45:44.150` | assume we've already read in the log |
| `00:45:44.160 - 00:45:45.829` | header structure that it's in memory at |
| `00:45:45.839 - 00:45:47.190` | this point |
| `00:45:47.200 - 00:45:49.829` | and we go through the array from zero to |
| `00:45:49.839 - 00:45:52.710` | n minus one with the index tail |
| `00:45:52.720 - 00:45:54.710` | and we're going to |
| `00:45:54.720 - 00:45:58.630` | read a block from the log okay so here |
| `00:45:58.640 - 00:46:01.109` | we're using the start of the log |
| `00:46:01.119 - 00:46:01.990` | plus |
| `00:46:02.000 - 00:46:02.870` | the |
| `00:46:02.880 - 00:46:06.390` | index plus one for the header block okay |
| `00:46:06.400 - 00:46:08.230` | so that's where we're computing the |
| `00:46:08.240 - 00:46:10.550` | block number here so the first one would |
| `00:46:10.560 - 00:46:13.270` | be this block number here |
| `00:46:13.280 - 00:46:16.470` | and we are reading it in from the disk |
| `00:46:16.480 - 00:46:18.150` | into a buffer |
| `00:46:18.160 - 00:46:21.829` | and then we are going to be writing |
| `00:46:21.839 - 00:46:24.790` | to a location on disk so we need to have |
| `00:46:24.800 - 00:46:27.190` | a b read for that and that's what the |
| `00:46:27.200 - 00:46:29.589` | second b read does |
| `00:46:29.599 - 00:46:32.630` | it is uh reading from the disk and where |
| `00:46:32.640 - 00:46:34.630` | is it reading from well we go to the log |
| `00:46:34.640 - 00:46:37.430` | header and we use tail as an index into |
| `00:46:37.440 - 00:46:40.230` | the array okay so we use a tail into an |
| `00:46:40.240 - 00:46:42.790` | as an index we find out the block number |
| `00:46:42.800 - 00:46:45.510` | 103 in this case and so we're going to |
| `00:46:45.520 - 00:46:49.510` | get a buffer for this particular block |
| `00:46:49.520 - 00:46:50.470` | and |
| `00:46:50.480 - 00:46:52.230` | then we will |
| `00:46:52.240 - 00:46:53.349` | move |
| `00:46:53.359 - 00:46:55.790` | a block of data that in our case it's |
| `00:46:55.800 - 00:46:57.829` | 1024 bytes |
| `00:46:57.839 - 00:46:58.790` | from |
| `00:46:58.800 - 00:46:59.589` | the |
| `00:46:59.599 - 00:47:03.510` | log block okay l buff to the buffer |
| `00:47:03.520 - 00:47:05.270` | that's going to be written out the |
| `00:47:05.280 - 00:47:06.470` | debuff |
| `00:47:06.480 - 00:47:09.430` | okay and then we write the debuff |
| `00:47:09.440 - 00:47:13.670` | and uh if we are called from commit |
| `00:47:13.680 - 00:47:16.069` | then we know that this |
| `00:47:16.079 - 00:47:17.910` | particular buffer |
| `00:47:17.920 - 00:47:20.470` | okay has been pinned so we need to unpin |
| `00:47:20.480 - 00:47:21.430` | it |
| `00:47:21.440 - 00:47:24.549` | okay if we are recovering after a crash |
| `00:47:24.559 - 00:47:27.109` | then it won't have been pinned so we do |
| `00:47:27.119 - 00:47:29.589` | not call the unpin function |
| `00:47:29.599 - 00:47:32.230` | but in either case we release both these |
| `00:47:32.240 - 00:47:34.150` | two buffers |
| `00:47:34.160 - 00:47:35.910` | now i should add that |
| `00:47:35.920 - 00:47:39.109` | when we perform this first read here |
| `00:47:39.119 - 00:47:42.710` | we are reading from this here |
| `00:47:42.720 - 00:47:47.349` | that uh may actually still be in the |
| `00:47:47.359 - 00:47:49.829` | buffer cache okay it's not this one |
| `00:47:49.839 - 00:47:51.430` | right here because this one |
| `00:47:51.440 - 00:47:54.309` | points to this this block number |
| `00:47:54.319 - 00:47:55.270` | okay |
| `00:47:55.280 - 00:47:57.349` | this buffer points to this block number |
| `00:47:57.359 - 00:47:58.150` | but |
| `00:47:58.160 - 00:48:01.109` | in order to write this we had another |
| `00:48:01.119 - 00:48:03.510` | buffer here in the buffer cache for this |
| `00:48:03.520 - 00:48:05.910` | particular block so it may be that this |
| `00:48:05.920 - 00:48:09.349` | b read here doesn't actually do a disc |
| `00:48:09.359 - 00:48:13.190` | read |
| `00:48:13.200 - 00:48:16.470` | here in this case we have to call b read |
| `00:48:16.480 - 00:48:19.670` | before we can call b write and so this |
| `00:48:19.680 - 00:48:22.790` | will read in an entire block |
| `00:48:22.800 - 00:48:25.510` | okay so when we are preparing to write |
| `00:48:25.520 - 00:48:28.470` | to this block here we first call b read |
| `00:48:28.480 - 00:48:30.950` | for 103 and that's what's what's |
| `00:48:30.960 - 00:48:41.430` | happening here |
| `00:48:41.440 - 00:48:42.870` | now let's take a look at what happens on |
| `00:48:42.880 - 00:48:44.150` | startup |
| `00:48:44.160 - 00:48:47.030` | the function init log is called as part |
| `00:48:47.040 - 00:48:49.430` | of the very first time slice and it runs |
| `00:48:49.440 - 00:48:52.309` | as part of a user mode process |
| `00:48:52.319 - 00:48:54.230` | it begins by initializing the spin lock |
| `00:48:54.240 - 00:48:56.390` | that protects our log variables and |
| `00:48:56.400 - 00:48:58.390` | initializing several constants that |
| `00:48:58.400 - 00:49:00.630` | won't change after this and then it |
| `00:49:00.640 - 00:49:03.670` | calls this function recover from log |
| `00:49:03.680 - 00:49:07.589` | so what's recover from log going to do |
| `00:49:07.599 - 00:49:10.150` | it's going to begin by reading the log |
| `00:49:10.160 - 00:49:13.190` | header from disk and moving it to the in |
| `00:49:13.200 - 00:49:14.870` | memory copy |
| `00:49:14.880 - 00:49:18.150` | and then it's going to check in |
| `00:49:18.160 - 00:49:20.150` | if it's 0 then it indicates there's |
| `00:49:20.160 - 00:49:22.710` | nothing in the log area and we can |
| `00:49:22.720 - 00:49:25.190` | proceed immediately but if it is greater |
| `00:49:25.200 - 00:49:26.549` | than 0 |
| `00:49:26.559 - 00:49:29.030` | as in this example it's 2 it indicates |
| `00:49:29.040 - 00:49:32.470` | that the log area contains a |
| `00:49:32.480 - 00:49:34.710` | transaction that hasn't been completely |
| `00:49:34.720 - 00:49:37.670` | finished so it will go through the array |
| `00:49:37.680 - 00:49:39.750` | and for each element it will read a |
| `00:49:39.760 - 00:49:42.630` | block from the log area and write it to |
| `00:49:42.640 - 00:49:45.109` | the appropriate location in the main |
| `00:49:45.119 - 00:49:47.510` | block area and so it will do that for |
| `00:49:47.520 - 00:49:48.870` | each of the blocks |
| `00:49:48.880 - 00:49:50.549` | and update |
| `00:49:50.559 - 00:49:52.870` | the main block area |
| `00:49:52.880 - 00:49:57.190` | and finally it will set into 0 and then |
| `00:49:57.200 - 00:49:59.829` | write the log header back to disk |
| `00:49:59.839 - 00:50:03.349` | thereby essentially clearing it |
| `00:50:03.359 - 00:50:05.430` | now it may be that the crash occurred in |
| `00:50:05.440 - 00:50:07.910` | the middle of committing a transaction |
| `00:50:07.920 - 00:50:09.030` | and |
| `00:50:09.040 - 00:50:10.549` | some of these blocks have already been |
| `00:50:10.559 - 00:50:12.790` | written to the main block area |
| `00:50:12.800 - 00:50:13.670` | but |
| `00:50:13.680 - 00:50:15.030` | this |
| `00:50:15.040 - 00:50:17.670` | recover from log will repeat that work |
| `00:50:17.680 - 00:50:19.829` | it won't matter that we |
| `00:50:19.839 - 00:50:22.470` | read this block and write it a second |
| `00:50:22.480 - 00:50:23.510` | time |
| `00:50:23.520 - 00:50:26.390` | to the main block area so let's take a |
| `00:50:26.400 - 00:50:31.430` | look at recover from log and here it is |
| `00:50:31.440 - 00:50:33.990` | it begins by calling read head and then |
| `00:50:34.000 - 00:50:37.670` | it calls install trans with the |
| `00:50:37.680 - 00:50:40.309` | recovering argument set to 1 to indicate |
| `00:50:40.319 - 00:50:41.910` | that we're being called as part of the |
| `00:50:41.920 - 00:50:44.630` | startup and not as part of a commit then |
| `00:50:44.640 - 00:50:47.990` | it sets into 0 and writes the log header |
| `00:50:48.000 - 00:50:50.150` | structure back to disk |
| `00:50:50.160 - 00:50:52.309` | let's take a look at read head that's |
| `00:50:52.319 - 00:50:55.190` | right here and |
| `00:50:55.200 - 00:50:56.710` | it's going to read the log header from |
| `00:50:56.720 - 00:50:58.710` | disk into the in memory log header |
| `00:50:58.720 - 00:50:59.990` | structure |
| `00:51:00.000 - 00:51:01.670` | and so it has a |
| `00:51:01.680 - 00:51:04.790` | read here and it's providing the address |
| `00:51:04.800 - 00:51:07.190` | or the block number i should say of the |
| `00:51:07.200 - 00:51:08.390` | log header |
| `00:51:08.400 - 00:51:11.030` | that's the start of the log which is uh |
| `00:51:11.040 - 00:51:13.750` | in our example it's two so it reads from |
| `00:51:13.760 - 00:51:14.950` | this block |
| `00:51:14.960 - 00:51:18.230` | into some buffer and that is a pointer |
| `00:51:18.240 - 00:51:19.430` | to the buffer |
| `00:51:19.440 - 00:51:20.549` | and then |
| `00:51:20.559 - 00:51:23.270` | um we get a pointer to the data area in |
| `00:51:23.280 - 00:51:24.470` | that buffer |
| `00:51:24.480 - 00:51:27.030` | lh's pointer |
| `00:51:27.040 - 00:51:30.150` | with c data is an array so |
| `00:51:30.160 - 00:51:31.990` | this expression here is just equivalent |
| `00:51:32.000 - 00:51:34.069` | to the address of the first element of |
| `00:51:34.079 - 00:51:35.510` | the array |
| `00:51:35.520 - 00:51:37.270` | and so that's where we're going to be |
| `00:51:37.280 - 00:51:40.870` | copying from and we're copying into the |
| `00:51:40.880 - 00:51:43.030` | log header the in-memory copy of the log |
| `00:51:43.040 - 00:51:45.510` | header so we first copy in |
| `00:51:45.520 - 00:51:46.390` | from |
| `00:51:46.400 - 00:51:47.510` | the buffer |
| `00:51:47.520 - 00:51:50.790` | lh into the log header and then we go |
| `00:51:50.800 - 00:51:52.069` | through the array |
| `00:51:52.079 - 00:51:55.030` | and copy each of the array elements from |
| `00:51:55.040 - 00:51:57.910` | the buffer to the log header that's in |
| `00:51:57.920 - 00:51:59.270` | memory |
| `00:51:59.280 - 00:52:01.990` | if n is zero we don't do any work so we |
| `00:52:02.000 - 00:52:04.790` | only copy as many as is necessary |
| `00:52:04.800 - 00:52:09.349` | and finally we release this buffer |
| `00:52:09.359 - 00:52:11.910` | now we could consider just using a |
| `00:52:11.920 - 00:52:13.430` | memory move |
| `00:52:13.440 - 00:52:16.230` | function call to do this copying and in |
| `00:52:16.240 - 00:52:18.309` | that case we would be copying from the |
| `00:52:18.319 - 00:52:21.670` | buffer to the in-memory log header area |
| `00:52:21.680 - 00:52:23.750` | and we're copying the entire structure |
| `00:52:23.760 - 00:52:26.390` | that is we would copy the entire array |
| `00:52:26.400 - 00:52:28.470` | but normally the previous session will |
| `00:52:28.480 - 00:52:31.109` | not have ended with a crash so in will |
| `00:52:31.119 - 00:52:33.589` | normally be zero so we really only need |
| `00:52:33.599 - 00:52:36.710` | to copy in in most cases and can skip |
| `00:52:36.720 - 00:52:39.190` | copying the array |
| `00:52:39.200 - 00:52:41.030` | okay |
| `00:52:41.040 - 00:52:44.150` | so now let's take a look at |
| `00:52:44.160 - 00:52:45.270` | in |
| `00:52:45.280 - 00:52:47.910` | recover from log we read the header and |
| `00:52:47.920 - 00:52:51.030` | then we call install trans so we can |
| `00:52:51.040 - 00:52:53.109` | take a look at that again |
| `00:52:53.119 - 00:52:55.109` | and we have this recovering argument |
| `00:52:55.119 - 00:52:57.589` | that will be set to true if we are being |
| `00:52:57.599 - 00:52:59.349` | called on startup |
| `00:52:59.359 - 00:53:02.950` | and again what it does is it reads from |
| `00:53:02.960 - 00:53:06.470` | the log area on desk and it writes to |
| `00:53:06.480 - 00:53:09.349` | okay here's the right debuff it writes |
| `00:53:09.359 - 00:53:11.109` | to |
| `00:53:11.119 - 00:53:12.710` | whatever address |
| `00:53:12.720 - 00:53:15.270` | the array tells us to write 2. so here |
| `00:53:15.280 - 00:53:17.349` | we're going into the log header and into |
| `00:53:17.359 - 00:53:18.710` | the array |
| `00:53:18.720 - 00:53:19.829` | this is our |
| `00:53:19.839 - 00:53:22.870` | tail as our index from 0 to n minus 1 in |
| `00:53:22.880 - 00:53:25.910` | this array and we are |
| `00:53:25.920 - 00:53:28.549` | looking at the array to find the address |
| `00:53:28.559 - 00:53:31.030` | okay so first we would find 103 and that |
| `00:53:31.040 - 00:53:33.589` | tells us we want to write to 103. |
| `00:53:33.599 - 00:53:34.549` | so we |
| `00:53:34.559 - 00:53:36.309` | get a buffer for |
| `00:53:36.319 - 00:53:40.230` | block number 103 and then we write to |
| `00:53:40.240 - 00:53:43.990` | that block uh first we move the entire |
| `00:53:44.000 - 00:53:45.030` | data |
| `00:53:45.040 - 00:53:48.069` | block 1024 bytes from |
| `00:53:48.079 - 00:53:51.190` | the buffer that we just read in l buff |
| `00:53:51.200 - 00:53:53.589` | okay to the destination buffer and then |
| `00:53:53.599 - 00:53:54.630` | we write |
| `00:53:54.640 - 00:53:56.549` | now the recovering here |
| `00:53:56.559 - 00:53:57.990` | will be |
| `00:53:58.000 - 00:54:01.270` | true on startup so we will not need to |
| `00:54:01.280 - 00:54:05.349` | unpin the buffer recall that |
| `00:54:05.359 - 00:54:07.750` | these buffers in the buffer cache |
| `00:54:07.760 - 00:54:10.390` | when we first read in the old value of |
| `00:54:10.400 - 00:54:13.190` | block 103 into the buffer |
| `00:54:13.200 - 00:54:15.190` | we pinned it okay |
| `00:54:15.200 - 00:54:16.630` | and so if we are in the middle of a |
| `00:54:16.640 - 00:54:19.190` | commit we need to do the unpin but on |
| `00:54:19.200 - 00:54:20.950` | recovery |
| `00:54:20.960 - 00:54:22.790` | these blocks in the buffer are not |
| `00:54:22.800 - 00:54:26.390` | pinned so we don't do the unpin and then |
| `00:54:26.400 - 00:54:29.270` | finally we release both of these buffers |
| `00:54:29.280 - 00:54:31.910` | so that they could be reused |
| `00:54:31.920 - 00:54:33.990` | in some way |
| `00:54:34.000 - 00:54:35.270` | now |
| `00:54:35.280 - 00:54:36.630` | so i think we've looked at everything |
| `00:54:36.640 - 00:54:39.910` | but i wanted to make one comment |
| `00:54:39.920 - 00:54:42.150` | this knit log function calls recover |
| `00:54:42.160 - 00:54:43.750` | from log |
| `00:54:43.760 - 00:54:45.990` | recover from log is only called from |
| `00:54:46.000 - 00:54:48.230` | that one place and it consists of four |
| `00:54:48.240 - 00:54:50.230` | lines it's pretty short |
| `00:54:50.240 - 00:54:52.710` | and it calls read head read head is only |
| `00:54:52.720 - 00:54:54.950` | called from this particular location and |
| `00:54:54.960 - 00:54:57.910` | again it is quite short so we could |
| `00:54:57.920 - 00:55:00.230` | consider doing some inlining we could |
| `00:55:00.240 - 00:55:02.630` | take all of this code here |
| `00:55:02.640 - 00:55:06.309` | and move it into the recover from log |
| `00:55:06.319 - 00:55:07.750` | function |
| `00:55:07.760 - 00:55:09.829` | and or we could take the recover from |
| `00:55:09.839 - 00:55:12.710` | log function and move it directly into |
| `00:55:12.720 - 00:55:15.510` | the init log function |
| `00:55:15.520 - 00:55:17.829` | recover from log is pretty short and |
| `00:55:17.839 - 00:55:20.710` | it's only four lines so i would prefer |
| `00:55:20.720 - 00:55:22.710` | to in my if i were writing this code i |
| `00:55:22.720 - 00:55:26.789` | would do the in lighting i think that uh |
| `00:55:26.799 - 00:55:29.589` | i might also consider inlining read head |
| `00:55:29.599 - 00:55:30.950` | as well |
| `00:55:30.960 - 00:55:32.789` | the resulting function |
| `00:55:32.799 - 00:55:34.870` | init log would then be |
| `00:55:34.880 - 00:55:38.390` | still well less than a page |
| `00:55:38.400 - 00:55:40.069` | it's an aesthetic question and it kind |
| `00:55:40.079 - 00:55:41.349` | of depends on |
| `00:55:41.359 - 00:55:43.109` | how you feel about things |
| `00:55:43.119 - 00:55:44.950` | you could make the argument that recover |
| `00:55:44.960 - 00:55:47.589` | from log is a separate enough function |
| `00:55:47.599 - 00:55:49.589` | separate enough operation it's a |
| `00:55:49.599 - 00:55:51.510` | discrete and different uh |
| `00:55:51.520 - 00:55:53.829` | operation so it makes sense to pull it |
| `00:55:53.839 - 00:55:56.150` | out and put it in its own |
| `00:55:56.160 - 00:56:00.150` | function and document it separately |
| `00:56:00.160 - 00:56:01.910` | the argument that i would make is that |
| `00:56:01.920 - 00:56:03.430` | every time you have a separate function |
| `00:56:03.440 - 00:56:04.549` | introduced |
| `00:56:04.559 - 00:56:07.349` | it requires a little extra effort a |
| `00:56:07.359 - 00:56:10.549` | little extra mental effort not much but |
| `00:56:10.559 - 00:56:11.910` | a little bit |
| `00:56:11.920 - 00:56:13.990` | if you were to approach this code |
| `00:56:14.000 - 00:56:16.789` | without having seen it before and are |
| `00:56:16.799 - 00:56:18.870` | trying to read it and you encounter some |
| `00:56:18.880 - 00:56:21.109` | function like this um |
| `00:56:21.119 - 00:56:22.630` | you have to say well where is this thing |
| `00:56:22.640 - 00:56:24.390` | called from |
| `00:56:24.400 - 00:56:26.309` | and oh it calls this function here where |
| `00:56:26.319 - 00:56:27.510` | is that so |
| `00:56:27.520 - 00:56:29.510` | you have a little bit of extra mental |
| `00:56:29.520 - 00:56:31.190` | effort not much but a little bit you've |
| `00:56:31.200 - 00:56:35.030` | got to go find where read head is so you |
| `00:56:35.040 - 00:56:36.710` | got to shuffle through some pages or |
| `00:56:36.720 - 00:56:38.789` | whatever and locate it |
| `00:56:38.799 - 00:56:40.230` | but more importantly |
| `00:56:40.240 - 00:56:41.589` | when you're looking at a function you |
| `00:56:41.599 - 00:56:43.750` | have to ask where is it called from so |
| `00:56:43.760 - 00:56:45.829` | if i see recover from log |
| `00:56:45.839 - 00:56:47.750` | is it called where is it called from so |
| `00:56:47.760 - 00:56:49.910` | that requires a little bit of a search |
| `00:56:49.920 - 00:56:51.270` | okay and then you might find out that |
| `00:56:51.280 - 00:56:54.309` | it's only called from one place |
| `00:56:54.319 - 00:56:56.150` | read head it's not clear at all that |
| `00:56:56.160 - 00:56:58.630` | it's a call from only one place |
| `00:56:58.640 - 00:57:00.870` | so you might uh spend some time trying |
| `00:57:00.880 - 00:57:01.910` | to search |
| `00:57:01.920 - 00:57:05.510` | for where else it is being used |
| `00:57:05.520 - 00:57:07.030` | these are minor things and again it's an |
| `00:57:07.040 - 00:57:10.069` | aesthetic question so um |
| `00:57:10.079 - 00:57:13.349` | the code in log.c is fairly complex and |
| `00:57:13.359 - 00:57:15.910` | what it does is fairly complex but |
| `00:57:15.920 - 00:57:17.589` | i think we've gone through all of this |
| `00:57:17.599 - 00:57:20.069` | file and so i can say thanks for |
| `00:57:20.079 - 00:57:24.280` | watching i'll see you in the next video |
