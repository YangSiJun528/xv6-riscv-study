# xv6 Kernel-32: fs.c Part-1

| Field | Value |
| --- | --- |
| Video ID | `LTAOiYPemZM` |
| URL | <https://www.youtube.com/watch?v=LTAOiYPemZM> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.520 - 00:00:03.030` | this video is part of a series on the |
| `00:00:03.040 - 00:00:05.670` | xv6 operating system kernel |
| `00:00:05.680 - 00:00:07.909` | in this video i will cover the functions |
| `00:00:07.919 - 00:00:10.390` | and walk through the code in the file fs |
| `00:00:10.400 - 00:00:11.749` | dot c |
| `00:00:11.759 - 00:00:13.830` | there are a large number of functions |
| `00:00:13.840 - 00:00:15.749` | and i've broken this into two separate |
| `00:00:15.759 - 00:00:18.070` | videos each is about 45 minutes long and |
| `00:00:18.080 - 00:00:21.590` | this is the first video in that series |
| `00:00:21.600 - 00:00:24.070` | in the previous video i introduced the |
| `00:00:24.080 - 00:00:26.710` | data structures and gave a summary |
| `00:00:26.720 - 00:00:29.109` | outline of the functions and in this |
| `00:00:29.119 - 00:00:31.589` | video i'll go over the code in the |
| `00:00:31.599 - 00:00:33.590` | functions but here let me just review |
| `00:00:33.600 - 00:00:35.190` | this list |
| `00:00:35.200 - 00:00:36.549` | these are the functions that i will be |
| `00:00:36.559 - 00:00:39.910` | covering in this video and i just will |
| `00:00:39.920 - 00:00:42.470` | present this material here on the screen |
| `00:00:42.480 - 00:00:44.389` | so you can have a reference as to what |
| `00:00:44.399 - 00:00:46.470` | is covered in this video |
| `00:00:46.480 - 00:00:49.110` | these functions are all covered |
| `00:00:49.120 - 00:00:54.069` | b map i will cover in part two |
| `00:00:54.079 - 00:00:56.790` | however in this video i will cover eye |
| `00:00:56.800 - 00:00:59.110` | trunk and stat i |
| `00:00:59.120 - 00:01:00.069` | and then |
| `00:01:00.079 - 00:01:02.630` | the other functions read i and so on |
| `00:01:02.640 - 00:01:04.070` | will be covered in |
| `00:01:04.080 - 00:01:06.469` | the next video |
| `00:01:06.479 - 00:01:08.550` | and in the next video |
| `00:01:08.560 - 00:01:09.590` | we will |
| `00:01:09.600 - 00:01:10.789` | end up |
| `00:01:10.799 - 00:01:13.350` | talking about the main function name i |
| `00:01:13.360 - 00:01:14.630` | which is sort of what we're working |
| `00:01:14.640 - 00:01:17.030` | toward it's passed a pointer to a path |
| `00:01:17.040 - 00:01:19.910` | name string and it figures out which |
| `00:01:19.920 - 00:01:21.990` | file on the disk that is and returns a |
| `00:01:22.000 - 00:01:23.670` | pointer to an inode representing that |
| `00:01:23.680 - 00:01:24.870` | file |
| `00:01:24.880 - 00:01:27.910` | okay let's get going |
| `00:01:27.920 - 00:01:29.270` | let's start with the functions that are |
| `00:01:29.280 - 00:01:31.670` | involved with initialization so here's |
| `00:01:31.680 - 00:01:34.310` | the main function uh this is from file |
| `00:01:34.320 - 00:01:35.590` | main.c |
| `00:01:35.600 - 00:01:39.510` | and recall that it tests the core and |
| `00:01:39.520 - 00:01:41.270` | all of these functions are executed on |
| `00:01:41.280 - 00:01:43.590` | core zero that is they're executed only |
| `00:01:43.600 - 00:01:45.830` | once and these functions are executed by |
| `00:01:45.840 - 00:01:47.270` | the other cores |
| `00:01:47.280 - 00:01:51.109` | and after all this we start scheduling |
| `00:01:51.119 - 00:01:52.789` | right here we see b init which |
| `00:01:52.799 - 00:01:54.550` | initializes the data that's associated |
| `00:01:54.560 - 00:01:56.230` | with the buffers |
| `00:01:56.240 - 00:01:58.709` | and then we call i init |
| `00:01:58.719 - 00:02:00.069` | remember that the |
| `00:02:00.079 - 00:02:02.950` | inode cache consists of an array it |
| `00:02:02.960 - 00:02:05.590` | happens to have 50 structures in it |
| `00:02:05.600 - 00:02:07.590` | and the structures are |
| `00:02:07.600 - 00:02:08.790` | inodes |
| `00:02:08.800 - 00:02:10.869` | and there is one spin lock that protects |
| `00:02:10.879 - 00:02:13.670` | the entire array and each structure has |
| `00:02:13.680 - 00:02:16.150` | its own sleep block called lock |
| `00:02:16.160 - 00:02:17.510` | so |
| `00:02:17.520 - 00:02:20.869` | here is the code for i annette |
| `00:02:20.879 - 00:02:22.710` | and you see we're initializing the one |
| `00:02:22.720 - 00:02:25.110` | lock that's associated with the i table |
| `00:02:25.120 - 00:02:26.869` | and then we're going through the entire |
| `00:02:26.879 - 00:02:29.270` | array right here and initializing each |
| `00:02:29.280 - 00:02:31.830` | of the sleep blocks |
| `00:02:31.840 - 00:02:34.229` | these structures also have a reference |
| `00:02:34.239 - 00:02:36.710` | count and if this is zero that indicates |
| `00:02:36.720 - 00:02:38.390` | that this element is unused and |
| `00:02:38.400 - 00:02:42.309` | available for uh use in the future and |
| `00:02:42.319 - 00:02:44.229` | the reference field is initialized to |
| `00:02:44.239 - 00:02:46.869` | zero by default |
| `00:02:46.879 - 00:02:49.589` | okay going back to main |
| `00:02:49.599 - 00:02:52.550` | we see a call to file in it this uh i |
| `00:02:52.560 - 00:02:53.910` | haven't talked about this yet but |
| `00:02:53.920 - 00:02:56.229` | essentially this initializes a lock as |
| `00:02:56.239 - 00:02:57.190` | well |
| `00:02:57.200 - 00:02:59.509` | and we do some initialization related to |
| `00:02:59.519 - 00:03:00.790` | the device |
| `00:03:00.800 - 00:03:02.869` | and then we create the |
| `00:03:02.879 - 00:03:05.430` | init process so here we create the first |
| `00:03:05.440 - 00:03:07.030` | user process |
| `00:03:07.040 - 00:03:08.949` | and at some point we will start |
| `00:03:08.959 - 00:03:11.350` | scheduling okay and one of the cores |
| `00:03:11.360 - 00:03:14.390` | will pick up this process and we'll |
| `00:03:14.400 - 00:03:16.949` | begin the very first time slice |
| `00:03:16.959 - 00:03:19.270` | we can't do any disk i o until we are |
| `00:03:19.280 - 00:03:20.869` | actually running in a user process |
| `00:03:20.879 - 00:03:23.830` | because the disk io stuff needs to sleep |
| `00:03:23.840 - 00:03:24.550` | so |
| `00:03:24.560 - 00:03:26.229` | let's take a look at what happens when |
| `00:03:26.239 - 00:03:29.270` | uh init is first executed |
| `00:03:29.280 - 00:03:31.589` | well remember that four cred from the |
| `00:03:31.599 - 00:03:33.910` | file proc dot c |
| `00:03:33.920 - 00:03:36.070` | will be called to |
| `00:03:36.080 - 00:03:39.110` | schedule the first time slice |
| `00:03:39.120 - 00:03:42.869` | and it contains this code here that asks |
| `00:03:42.879 - 00:03:45.030` | whether this is the first time it's ever |
| `00:03:45.040 - 00:03:46.470` | been called right there's this local |
| `00:03:46.480 - 00:03:49.030` | variable sorry this is a static variable |
| `00:03:49.040 - 00:03:51.430` | it's initialized to true and so the |
| `00:03:51.440 - 00:03:53.270` | first time this function is called by |
| `00:03:53.280 - 00:03:55.830` | any process it will execute this code |
| `00:03:55.840 - 00:03:59.030` | here and then set first to zero |
| `00:03:59.040 - 00:04:01.589` | and what does that do well it calls fs |
| `00:04:01.599 - 00:04:04.949` | annette so let's take a look at fsnet |
| `00:04:04.959 - 00:04:07.509` | now we're running inside of a process |
| `00:04:07.519 - 00:04:10.470` | and here is fsnet |
| `00:04:10.480 - 00:04:13.910` | oh uh shows read sb2 the first thing it |
| `00:04:13.920 - 00:04:16.949` | does is it calls read sb which will read |
| `00:04:16.959 - 00:04:19.509` | in the super block from the disk |
| `00:04:19.519 - 00:04:20.949` | so here we see |
| `00:04:20.959 - 00:04:23.670` | a call to be read and this will be the |
| `00:04:23.680 - 00:04:25.510` | first read we do |
| `00:04:25.520 - 00:04:28.870` | and sets bp to point to the buffer and |
| `00:04:28.880 - 00:04:31.030` | then it moves data |
| `00:04:31.040 - 00:04:32.870` | from the buffer and how much does it |
| `00:04:32.880 - 00:04:34.790` | move well the size of the super block |
| `00:04:34.800 - 00:04:37.510` | structure and it moves it into this |
| `00:04:37.520 - 00:04:39.830` | super block structure but we're passing |
| `00:04:39.840 - 00:04:41.430` | it the address of that super block |
| `00:04:41.440 - 00:04:42.469` | structure |
| `00:04:42.479 - 00:04:43.350` | and |
| `00:04:43.360 - 00:04:46.070` | then we release that buffer so after |
| `00:04:46.080 - 00:04:47.990` | this point here |
| `00:04:48.000 - 00:04:51.030` | we have the super block variables uh |
| `00:04:51.040 - 00:04:53.110` | stored in that super block structure and |
| `00:04:53.120 - 00:04:55.030` | we can access things like the magic |
| `00:04:55.040 - 00:04:56.710` | field so we immediately check it to make |
| `00:04:56.720 - 00:04:59.510` | sure that the device contains a file |
| `00:04:59.520 - 00:05:01.830` | system of the type that we are expecting |
| `00:05:01.840 - 00:05:03.830` | that the file system has been properly |
| `00:05:03.840 - 00:05:05.670` | initialized and so on |
| `00:05:05.680 - 00:05:08.070` | and then we call a net |
| `00:05:08.080 - 00:05:10.390` | log and i discussed the logging system |
| `00:05:10.400 - 00:05:13.270` | uh in a previous video but this is where |
| `00:05:13.280 - 00:05:17.430` | that gets called so after this we can |
| `00:05:17.440 - 00:05:19.749` | create transactions that is we can call |
| `00:05:19.759 - 00:05:25.350` | begin op and end up and log right |
| `00:05:25.360 - 00:05:27.270` | next let's take a look at the function b |
| `00:05:27.280 - 00:05:28.150` | alec |
| `00:05:28.160 - 00:05:31.110` | every time we create or grow a regular |
| `00:05:31.120 - 00:05:34.390` | file or a disk file we need to find an |
| `00:05:34.400 - 00:05:38.230` | unused block on the desk and zero it out |
| `00:05:38.240 - 00:05:40.070` | and then add it to the file |
| `00:05:40.080 - 00:05:44.230` | so b alec is called to allocate and zero |
| `00:05:44.240 - 00:05:47.029` | out a disk block and it will return the |
| `00:05:47.039 - 00:05:49.270` | number of the block that it found so |
| `00:05:49.280 - 00:05:51.270` | basically it's going to search through |
| `00:05:51.280 - 00:05:54.870` | the bitmap for an unused block |
| `00:05:54.880 - 00:05:57.590` | remember that our bitmap here can in |
| `00:05:57.600 - 00:05:59.430` | general consist of several |
| `00:05:59.440 - 00:06:01.670` | blocks okay |
| `00:06:01.680 - 00:06:04.870` | with only a tiny disk with a thousand |
| `00:06:04.880 - 00:06:07.350` | blocks in it it'll only have |
| `00:06:07.360 - 00:06:09.990` | one block but in general this code will |
| `00:06:10.000 - 00:06:12.390` | search a number of blocks |
| `00:06:12.400 - 00:06:14.469` | okay so this code can accommodate a much |
| `00:06:14.479 - 00:06:15.749` | larger disk |
| `00:06:15.759 - 00:06:17.350` | so |
| `00:06:17.360 - 00:06:18.710` | it's got let's look at the structure of |
| `00:06:18.720 - 00:06:22.070` | this thing it's got two nested for loops |
| `00:06:22.080 - 00:06:23.670` | okay and |
| `00:06:23.680 - 00:06:25.350` | the first one will go through the blocks |
| `00:06:25.360 - 00:06:28.390` | and for each block in the bitmap it will |
| `00:06:28.400 - 00:06:30.950` | read that block and then the next loop |
| `00:06:30.960 - 00:06:32.469` | will go through all the bits in the |
| `00:06:32.479 - 00:06:36.309` | block looking for one that is free |
| `00:06:36.319 - 00:06:37.110` | okay |
| `00:06:37.120 - 00:06:40.790` | so um here this outer loop has an index |
| `00:06:40.800 - 00:06:42.070` | variable of b |
| `00:06:42.080 - 00:06:43.830` | and it's being incremented by the number |
| `00:06:43.840 - 00:06:46.390` | of bits in a block |
| `00:06:46.400 - 00:06:48.469` | well they're 1020 |
| `00:06:48.479 - 00:06:51.589` | bytes with eight bits each so it's |
| `00:06:51.599 - 00:06:55.110` | b goes from zero and then eight k and |
| `00:06:55.120 - 00:06:58.950` | then 16k and so on okay so each one of |
| `00:06:58.960 - 00:07:01.430` | these will um |
| `00:07:01.440 - 00:07:02.950` | b |
| `00:07:02.960 - 00:07:05.670` | block and the first thing it does is it |
| `00:07:05.680 - 00:07:09.589` | reads that block in from the device okay |
| `00:07:09.599 - 00:07:12.790` | this function b block is passed a |
| `00:07:12.800 - 00:07:14.550` | block number as well as a pointer to the |
| `00:07:14.560 - 00:07:16.790` | super block and it will figure out which |
| `00:07:16.800 - 00:07:19.510` | block on disk contains that bit so we |
| `00:07:19.520 - 00:07:21.830` | read the first block and then the second |
| `00:07:21.840 - 00:07:23.189` | block using the |
| `00:07:23.199 - 00:07:25.589` | bit at the beginning of the block as the |
| `00:07:25.599 - 00:07:27.029` | index here |
| `00:07:27.039 - 00:07:28.790` | this inner loop |
| `00:07:28.800 - 00:07:31.270` | runs through the bits within that block |
| `00:07:31.280 - 00:07:34.309` | so it goes from zero up to |
| `00:07:34.319 - 00:07:35.670` | eight thousand |
| `00:07:35.680 - 00:07:37.909` | one hundred and ninety one eight one |
| `00:07:37.919 - 00:07:41.029` | nine one so it uh that's this loop index |
| `00:07:41.039 - 00:07:44.070` | here it's incremented by one however uh |
| `00:07:44.080 - 00:07:46.070` | so the actual bit that we're looking at |
| `00:07:46.080 - 00:07:48.469` | is this number plus the index within to |
| `00:07:48.479 - 00:07:50.230` | the within the block |
| `00:07:50.240 - 00:07:52.950` | okay and that's b plus b i |
| `00:07:52.960 - 00:07:55.909` | and of course we also stop if we |
| `00:07:55.919 - 00:07:58.950` | hit the size of the disk if we search |
| `00:07:58.960 - 00:08:00.950` | through all of the bits in the bitmap up |
| `00:08:00.960 - 00:08:01.670` | to |
| `00:08:01.680 - 00:08:03.749` | well in this case we have |
| `00:08:03.759 - 00:08:06.309` | only uh 1 000 blocks so if we search up |
| `00:08:06.319 - 00:08:08.950` | to this point without finding anything |
| `00:08:08.960 - 00:08:10.390` | then we immediately |
| `00:08:10.400 - 00:08:14.150` | abort um and um |
| `00:08:14.160 - 00:08:16.230` | that causes us to panic and say that |
| `00:08:16.240 - 00:08:18.629` | we're out of blocks that the disc is |
| `00:08:18.639 - 00:08:21.830` | full there are no free blocks |
| `00:08:21.840 - 00:08:23.990` | so once we have |
| `00:08:24.000 - 00:08:27.510` | a bit number within the block that's bi |
| `00:08:27.520 - 00:08:28.390` | we |
| `00:08:28.400 - 00:08:29.990` | need to figure out which bit within the |
| `00:08:30.000 - 00:08:32.709` | word or within the byte i should say |
| `00:08:32.719 - 00:08:34.469` | so we are using |
| `00:08:34.479 - 00:08:36.870` | mod 8 to figure out which bit within the |
| `00:08:36.880 - 00:08:38.389` | byte and |
| `00:08:38.399 - 00:08:40.709` | divide by eight to figure out which byte |
| `00:08:40.719 - 00:08:42.149` | within the block |
| `00:08:42.159 - 00:08:44.470` | so here we create a mask all zeros |
| `00:08:44.480 - 00:08:46.550` | except for one one bit in the |
| `00:08:46.560 - 00:08:48.150` | appropriate position |
| `00:08:48.160 - 00:08:50.389` | and so that's our mask |
| `00:08:50.399 - 00:08:51.829` | and |
| `00:08:51.839 - 00:08:54.150` | it's a byte although it's declared as a |
| `00:08:54.160 - 00:08:56.230` | 32-bit uh value |
| `00:08:56.240 - 00:08:57.829` | we're only concerned about the eight |
| `00:08:57.839 - 00:08:58.949` | bits in it |
| `00:08:58.959 - 00:09:01.910` | and then we here we are looking at |
| `00:09:01.920 - 00:09:04.870` | uh one individual byte and |
| `00:09:04.880 - 00:09:06.630` | anding it with the mask to look at one |
| `00:09:06.640 - 00:09:09.910` | particular bit and if that is zero then |
| `00:09:09.920 - 00:09:12.070` | this indicates it's a free block okay |
| `00:09:12.080 - 00:09:14.150` | zero is used to indicate free and the |
| `00:09:14.160 - 00:09:17.030` | one bit is just to indicate in use |
| `00:09:17.040 - 00:09:18.790` | so if we find a free block then the |
| `00:09:18.800 - 00:09:20.389` | first thing we do is we set the bet so |
| `00:09:20.399 - 00:09:21.430` | here we're |
| `00:09:21.440 - 00:09:24.230` | taking the same word and we're ordering |
| `00:09:24.240 - 00:09:27.030` | it with the mask to uh set that one bit |
| `00:09:27.040 - 00:09:28.389` | to a one |
| `00:09:28.399 - 00:09:30.230` | and then we are |
| `00:09:30.240 - 00:09:33.269` | writing the block back we've changed |
| `00:09:33.279 - 00:09:35.430` | this one block in the bitmap so we need |
| `00:09:35.440 - 00:09:37.509` | to write it back to the disk |
| `00:09:37.519 - 00:09:39.910` | and we do that here |
| `00:09:39.920 - 00:09:42.070` | and then we release that block and |
| `00:09:42.080 - 00:09:43.590` | finally we zero |
| `00:09:43.600 - 00:09:44.550` | that |
| `00:09:44.560 - 00:09:46.790` | block so |
| `00:09:46.800 - 00:09:48.150` | um |
| `00:09:48.160 - 00:09:50.870` | here we are |
| `00:09:50.880 - 00:09:53.590` | using b plus bi that's the actual block |
| `00:09:53.600 - 00:09:56.790` | number that we found that is free so |
| `00:09:56.800 - 00:09:59.190` | we're passing this block number to the |
| `00:09:59.200 - 00:10:01.110` | b0 function |
| `00:10:01.120 - 00:10:03.750` | and uh then we return the block number |
| `00:10:03.760 - 00:10:04.710` | here |
| `00:10:04.720 - 00:10:07.110` | and i just want to say that after we've |
| `00:10:07.120 - 00:10:08.949` | searched this entire block and not found |
| `00:10:08.959 - 00:10:10.069` | any free |
| `00:10:10.079 - 00:10:12.790` | bits then we release that block and we |
| `00:10:12.800 - 00:10:14.470` | move on to the next |
| `00:10:14.480 - 00:10:16.310` | block in the bitmap |
| `00:10:16.320 - 00:10:19.190` | so this b0 function is right here it's |
| `00:10:19.200 - 00:10:21.829` | only called from uh b alec where i just |
| `00:10:21.839 - 00:10:24.230` | showed you and so you can more or less |
| `00:10:24.240 - 00:10:25.990` | forget about it after this |
| `00:10:26.000 - 00:10:28.710` | um but what it does is it is past the |
| `00:10:28.720 - 00:10:29.990` | block number |
| `00:10:30.000 - 00:10:30.949` | okay |
| `00:10:30.959 - 00:10:33.990` | and it uh reads that block getting a |
| `00:10:34.000 - 00:10:36.150` | buffer pointer to the buffer that |
| `00:10:36.160 - 00:10:38.310` | contains that data and here we are |
| `00:10:38.320 - 00:10:40.069` | updating the data and in fact we're |
| `00:10:40.079 - 00:10:42.150` | using this function memset to move a |
| `00:10:42.160 - 00:10:44.630` | bunch of zeros how many zeros well uh |
| `00:10:44.640 - 00:10:47.269` | the block size is 1024 so we're moving |
| `00:10:47.279 - 00:10:50.550` | that many zero bytes into this data area |
| `00:10:50.560 - 00:10:53.350` | and then we're writing that block back |
| `00:10:53.360 - 00:10:54.710` | to the disk |
| `00:10:54.720 - 00:10:57.750` | and finally we're releasing that buffer |
| `00:10:57.760 - 00:10:59.269` | now |
| `00:10:59.279 - 00:11:01.670` | so these functions here are calling b |
| `00:11:01.680 - 00:11:02.630` | read |
| `00:11:02.640 - 00:11:05.750` | log write and b release okay so that |
| `00:11:05.760 - 00:11:07.269` | means they must be |
| `00:11:07.279 - 00:11:10.230` | within a transaction so we'll see that |
| `00:11:10.240 - 00:11:13.910` | all of the functions in this file here |
| `00:11:13.920 - 00:11:15.269` | are |
| `00:11:15.279 - 00:11:18.710` | supposed to be in a transaction |
| `00:11:18.720 - 00:11:24.150` | they will call be read and log right and |
| `00:11:24.160 - 00:11:25.750` | we assume that all of this is happening |
| `00:11:25.760 - 00:11:28.069` | within some transaction |
| `00:11:28.079 - 00:11:32.150` | so this code outside of the file fs.c |
| `00:11:32.160 - 00:11:33.030` | will |
| `00:11:33.040 - 00:11:34.630` | begin the transaction and then call |
| `00:11:34.640 - 00:11:36.230` | these functions or some of these |
| `00:11:36.240 - 00:11:38.069` | functions and then |
| `00:11:38.079 - 00:11:40.470` | ultimately call the |
| `00:11:40.480 - 00:11:42.470` | end op function to terminate the |
| `00:11:42.480 - 00:11:45.269` | transaction |
| `00:11:45.279 - 00:11:47.509` | as a professor i can't help but insert a |
| `00:11:47.519 - 00:11:50.790` | little commentary at this point |
| `00:11:50.800 - 00:11:53.190` | this algorithm is pretty straightforward |
| `00:11:53.200 - 00:11:55.430` | and easy to understand and for our |
| `00:11:55.440 - 00:11:59.829` | purposes of xv6 it's adequate but i have |
| `00:11:59.839 - 00:12:02.230` | some questions about efficiency |
| `00:12:02.240 - 00:12:03.910` | for one thing it's |
| `00:12:03.920 - 00:12:07.430` | searching every bit in the block one by |
| `00:12:07.440 - 00:12:10.710` | one so this inner loop is executing 8k |
| `00:12:10.720 - 00:12:13.670` | times and for each execution it's |
| `00:12:13.680 - 00:12:15.910` | building this mask and then |
| `00:12:15.920 - 00:12:18.710` | doing a little bit of computation |
| `00:12:18.720 - 00:12:20.790` | now when we allocate a block we need to |
| `00:12:20.800 - 00:12:23.030` | search through all the bits until we |
| `00:12:23.040 - 00:12:26.310` | find a bit that is zero and so we might |
| `00:12:26.320 - 00:12:27.910` | have a bunch of blocks that are in use |
| `00:12:27.920 - 00:12:29.910` | so we go through a bunch of bits that |
| `00:12:29.920 - 00:12:31.590` | are one |
| `00:12:31.600 - 00:12:34.470` | one approach would be to look at entire |
| `00:12:34.480 - 00:12:36.629` | words at a time rather than individual |
| `00:12:36.639 - 00:12:39.269` | bits at a time so the idea is that we |
| `00:12:39.279 - 00:12:40.389` | would have a loop that would loop |
| `00:12:40.399 - 00:12:42.470` | through all the words in the block and |
| `00:12:42.480 - 00:12:44.629` | it would look at each word and ask is |
| `00:12:44.639 - 00:12:47.670` | this word equal to a word with all one |
| `00:12:47.680 - 00:12:48.629` | bits |
| `00:12:48.639 - 00:12:50.550` | and if it is then it doesn't contain any |
| `00:12:50.560 - 00:12:53.829` | zero bits so there are no free blocks |
| `00:12:53.839 - 00:12:55.910` | represented by that word so we can move |
| `00:12:55.920 - 00:12:58.470` | on to the next word so it searches word |
| `00:12:58.480 - 00:13:00.790` | by word instead of bit by bit and then |
| `00:13:00.800 - 00:13:03.670` | when we finally find a word that is not |
| `00:13:03.680 - 00:13:06.310` | all ones it must contain a zero bit so |
| `00:13:06.320 - 00:13:07.750` | at that point we search the word and |
| `00:13:07.760 - 00:13:11.030` | find out which block is actually free |
| `00:13:11.040 - 00:13:13.190` | but still there's a problem this is |
| `00:13:13.200 - 00:13:15.269` | doing a linear search |
| `00:13:15.279 - 00:13:18.710` | if the disk is almost entirely full we |
| `00:13:18.720 - 00:13:20.629` | have to search through the entire |
| `00:13:20.639 - 00:13:23.990` | bitmap and for large devices that could |
| `00:13:24.000 - 00:13:26.550` | be time consuming so we don't really |
| `00:13:26.560 - 00:13:28.710` | like to do linear searches in the first |
| `00:13:28.720 - 00:13:29.910` | place |
| `00:13:29.920 - 00:13:31.670` | i want to just mention another approach |
| `00:13:31.680 - 00:13:32.550` | to |
| `00:13:32.560 - 00:13:34.389` | managing free lists |
| `00:13:34.399 - 00:13:36.470` | so the idea is that um |
| `00:13:36.480 - 00:13:40.470` | we keep a linked list of pointers and so |
| `00:13:40.480 - 00:13:42.150` | each one of these structures represents |
| `00:13:42.160 - 00:13:45.189` | a block on disk okay and each block |
| `00:13:45.199 - 00:13:48.949` | contains 256 words shall we say the |
| `00:13:48.959 - 00:13:51.509` | first word points to the next block in |
| `00:13:51.519 - 00:13:54.310` | the linked list and the remaining words |
| `00:13:54.320 - 00:13:57.110` | in the block point to free blocks |
| `00:13:57.120 - 00:13:58.949` | okay so um |
| `00:13:58.959 - 00:14:00.230` | in order to |
| `00:14:00.240 - 00:14:02.230` | get a free block we go to this linked |
| `00:14:02.240 - 00:14:04.069` | list and |
| `00:14:04.079 - 00:14:05.750` | maybe these have been allocated but here |
| `00:14:05.760 - 00:14:07.750` | is the first block so we can allocate |
| `00:14:07.760 - 00:14:08.870` | that one |
| `00:14:08.880 - 00:14:11.269` | and that's pretty quick because we just |
| `00:14:11.279 - 00:14:13.750` | keep a pointer it's a stack basically |
| `00:14:13.760 - 00:14:16.230` | when we return blocks to the free pool |
| `00:14:16.240 - 00:14:18.389` | we push onto this stack and then when we |
| `00:14:18.399 - 00:14:20.790` | allocate blocks we pop off of the stack |
| `00:14:20.800 - 00:14:22.629` | and then when we get to the end okay |
| `00:14:22.639 - 00:14:24.550` | after we allocate this block |
| `00:14:24.560 - 00:14:27.030` | the next allocate well we just allocate |
| `00:14:27.040 - 00:14:29.590` | this block itself the entire block and |
| `00:14:29.600 - 00:14:31.430` | now this block becomes the head of the |
| `00:14:31.440 - 00:14:32.310` | list |
| `00:14:32.320 - 00:14:35.829` | and we begin with our stack pointer here |
| `00:14:35.839 - 00:14:37.670` | and when we want to allocate a block we |
| `00:14:37.680 - 00:14:39.590` | allocate this one and then this one and |
| `00:14:39.600 - 00:14:42.069` | then this one |
| `00:14:42.079 - 00:14:45.750` | these blocks will be kept on disk and um |
| `00:14:45.760 - 00:14:48.069` | you can see that uh the these are |
| `00:14:48.079 - 00:14:50.069` | uh each one of these out it requires a |
| `00:14:50.079 - 00:14:51.670` | block but we have a bunch of free blocks |
| `00:14:51.680 - 00:14:54.310` | anyway so it's not really a cost to uh |
| `00:14:54.320 - 00:14:59.509` | store this uh linked list on disk as we |
| `00:14:59.519 - 00:15:01.189` | consume |
| `00:15:01.199 - 00:15:03.269` | blocks as blocks are allocated we |
| `00:15:03.279 - 00:15:05.030` | consume the blocks that are used for the |
| `00:15:05.040 - 00:15:07.110` | linked list as well |
| `00:15:07.120 - 00:15:08.790` | and |
| `00:15:08.800 - 00:15:10.629` | we would also like to kick |
| `00:15:10.639 - 00:15:13.189` | one or two of these blocks in memory to |
| `00:15:13.199 - 00:15:15.990` | make this process faster so we can keep |
| `00:15:16.000 - 00:15:18.150` | allocating here we can allocate this |
| `00:15:18.160 - 00:15:19.430` | block and then we can allocate this |
| `00:15:19.440 - 00:15:21.509` | block and we can keep going but at some |
| `00:15:21.519 - 00:15:22.790` | point |
| `00:15:22.800 - 00:15:25.910` | we have to read in the next block from |
| `00:15:25.920 - 00:15:27.750` | disk and |
| `00:15:27.760 - 00:15:29.749` | likewise when we're turning blocks we |
| `00:15:29.759 - 00:15:31.910` | can fill up this block and after this |
| `00:15:31.920 - 00:15:33.670` | block is completely full |
| `00:15:33.680 - 00:15:35.269` | well we need to write this one back to |
| `00:15:35.279 - 00:15:37.670` | disk and we just have we're turning a |
| `00:15:37.680 - 00:15:39.350` | block so we actually have a place to |
| `00:15:39.360 - 00:15:40.949` | write it |
| `00:15:40.959 - 00:15:43.590` | so we have a slightly complicated |
| `00:15:43.600 - 00:15:45.189` | algorithm |
| `00:15:45.199 - 00:15:45.990` | but |
| `00:15:46.000 - 00:15:47.509` | it's going to be much more efficient |
| `00:15:47.519 - 00:15:50.470` | because returning a block |
| `00:15:50.480 - 00:15:52.550` | will normally involve just writing a |
| `00:15:52.560 - 00:15:54.550` | number into this and advancing the stack |
| `00:15:54.560 - 00:15:55.509` | pointer |
| `00:15:55.519 - 00:15:57.189` | and allocating a block will be just |
| `00:15:57.199 - 00:16:00.069` | grabbing the next element on this and |
| `00:16:00.079 - 00:16:02.150` | decrementing or increasing the stack |
| `00:16:02.160 - 00:16:04.710` | pointer |
| `00:16:04.720 - 00:16:07.350` | the one aspect of the free |
| `00:16:07.360 - 00:16:09.670` | block being kept as a bid map is from |
| `00:16:09.680 - 00:16:11.670` | time to time you want to allocate |
| `00:16:11.680 - 00:16:13.269` | a number of blocks and you want them to |
| `00:16:13.279 - 00:16:15.350` | be close together on the disk for |
| `00:16:15.360 - 00:16:17.030` | example when we allocate the blocks for |
| `00:16:17.040 - 00:16:18.230` | a file |
| `00:16:18.240 - 00:16:19.670` | we would like all those blocks to be |
| `00:16:19.680 - 00:16:21.509` | close together because if one is red |
| `00:16:21.519 - 00:16:22.790` | it's quite likely that the others will |
| `00:16:22.800 - 00:16:24.870` | be needed as well |
| `00:16:24.880 - 00:16:26.949` | this approach here doesn't really deal |
| `00:16:26.959 - 00:16:29.030` | with that whereas with the bitmap if |
| `00:16:29.040 - 00:16:31.189` | you're trying to allocate let's say |
| `00:16:31.199 - 00:16:34.310` | 10 blocks altogether then you can search |
| `00:16:34.320 - 00:16:38.389` | the bitmap for a place where you find 10 |
| `00:16:38.399 - 00:16:42.710` | 0 bits that is 10 free blocks altogether |
| `00:16:42.720 - 00:16:45.430` | and so there you know we can go down a |
| `00:16:45.440 - 00:16:47.269` | rabbit hole here but i just want to |
| `00:16:47.279 - 00:16:48.629` | point out that |
| `00:16:48.639 - 00:16:51.110` | this code is simple |
| `00:16:51.120 - 00:16:53.189` | and easy to understand and adequate for |
| `00:16:53.199 - 00:16:54.230` | the |
| `00:16:54.240 - 00:16:57.749` | xv6 teaching kernel but in reality |
| `00:16:57.759 - 00:16:59.269` | things are going to get quite a bit more |
| `00:16:59.279 - 00:17:01.030` | complicated |
| `00:17:01.040 - 00:17:04.230` | so now let's turn to looking at the |
| `00:17:04.240 - 00:17:06.390` | corresponding free |
| `00:17:06.400 - 00:17:09.189` | routine |
| `00:17:09.199 - 00:17:11.750` | from time to time files are deleted or |
| `00:17:11.760 - 00:17:14.069` | their size is reduced and we need to |
| `00:17:14.079 - 00:17:16.390` | return the blocks in the file to the |
| `00:17:16.400 - 00:17:18.710` | free pool and for that we have the |
| `00:17:18.720 - 00:17:21.189` | befree function |
| `00:17:21.199 - 00:17:22.789` | when we allocate a block from the free |
| `00:17:22.799 - 00:17:24.549` | pool we have to search the bitmap to |
| `00:17:24.559 - 00:17:27.029` | find an unused block but when we free a |
| `00:17:27.039 - 00:17:29.430` | block we just need to find the |
| `00:17:29.440 - 00:17:31.590` | corresponding bit in the bitmap and |
| `00:17:31.600 - 00:17:33.029` | change it |
| `00:17:33.039 - 00:17:36.630` | so b3 is passed the block number |
| `00:17:36.640 - 00:17:38.630` | and it calls b block |
| `00:17:38.640 - 00:17:40.310` | with that block number to determine |
| `00:17:40.320 - 00:17:42.549` | which block in the bitmap will contain |
| `00:17:42.559 - 00:17:45.430` | the corresponding bit for that block |
| `00:17:45.440 - 00:17:47.110` | and then we read that block from the |
| `00:17:47.120 - 00:17:50.390` | bitmap area in to a buffer |
| `00:17:50.400 - 00:17:52.630` | and here we compute |
| `00:17:52.640 - 00:17:53.750` | the |
| `00:17:53.760 - 00:17:56.390` | bit within that block so this is |
| `00:17:56.400 - 00:17:58.950` | bits per block and we use the mod |
| `00:17:58.960 - 00:18:00.390` | operator to figure out |
| `00:18:00.400 - 00:18:04.150` | a number between 0 and 8191 within that |
| `00:18:04.160 - 00:18:05.669` | block |
| `00:18:05.679 - 00:18:08.150` | then we use the mod and the divide |
| `00:18:08.160 - 00:18:10.150` | operator to figure out uh |
| `00:18:10.160 - 00:18:11.270` | which |
| `00:18:11.280 - 00:18:14.150` | bit within a byte and which byte within |
| `00:18:14.160 - 00:18:17.430` | the block so here we are making a mask |
| `00:18:17.440 - 00:18:20.150` | all zeros except for a single one bit in |
| `00:18:20.160 - 00:18:20.950` | the |
| `00:18:20.960 - 00:18:23.830` | corresponding position using the mod |
| `00:18:23.840 - 00:18:26.310` | operator and here we are |
| `00:18:26.320 - 00:18:28.789` | figuring out which byte within the data |
| `00:18:28.799 - 00:18:31.190` | block will contain that bit |
| `00:18:31.200 - 00:18:33.270` | and we're looking at that so we're |
| `00:18:33.280 - 00:18:36.150` | adding it with the mask to determine the |
| `00:18:36.160 - 00:18:38.230` | value of the bit |
| `00:18:38.240 - 00:18:39.909` | zero is used to indicate tree and if |
| `00:18:39.919 - 00:18:41.830` | it's already zero we've got a problem so |
| `00:18:41.840 - 00:18:42.950` | we panic |
| `00:18:42.960 - 00:18:45.830` | but otherwise we set the same |
| `00:18:45.840 - 00:18:48.070` | bite |
| `00:18:48.080 - 00:18:48.870` | by |
| `00:18:48.880 - 00:18:50.230` | anding it |
| `00:18:50.240 - 00:18:51.830` | with the |
| `00:18:51.840 - 00:18:54.390` | knot of the mask right so we turn all |
| `00:18:54.400 - 00:18:56.630` | the zero bits into one and so that |
| `00:18:56.640 - 00:18:58.710` | preserves them but the bit that we're |
| `00:18:58.720 - 00:19:01.110` | interested in here turns into a zero and |
| `00:19:01.120 - 00:19:03.350` | by ending it we clear it so this sets |
| `00:19:03.360 - 00:19:04.070` | the |
| `00:19:04.080 - 00:19:05.990` | or i should say clears the |
| `00:19:06.000 - 00:19:08.070` | bit that we're interested in to zero and |
| `00:19:08.080 - 00:19:10.870` | then we write that buffer |
| `00:19:10.880 - 00:19:14.950` | back to disk and release the buffer |
| `00:19:14.960 - 00:19:17.110` | next i want to talk about the i get and |
| `00:19:17.120 - 00:19:19.990` | the i put functions as well as the i |
| `00:19:20.000 - 00:19:23.430` | lock and i unlock functions |
| `00:19:23.440 - 00:19:25.270` | whenever we want to access an existing |
| `00:19:25.280 - 00:19:28.789` | file we'll call i get and i lock and |
| `00:19:28.799 - 00:19:31.029` | then when we're done with it we'll call |
| `00:19:31.039 - 00:19:34.390` | unlock and i put |
| `00:19:34.400 - 00:19:39.430` | so i get is past an inode number as well |
| `00:19:39.440 - 00:19:41.430` | as the device in which we want to find |
| `00:19:41.440 - 00:19:43.909` | this file and it returns a pointer to |
| `00:19:43.919 - 00:19:47.430` | that inode in the cache so it will |
| `00:19:47.440 - 00:19:49.430` | look in the cache to see if we already |
| `00:19:49.440 - 00:19:50.390` | have |
| `00:19:50.400 - 00:19:53.830` | that inode open if you will and if we do |
| `00:19:53.840 - 00:19:55.990` | we'll use that one otherwise it will |
| `00:19:56.000 - 00:19:59.110` | allocate a new entry in the cache and |
| `00:19:59.120 - 00:20:01.590` | devote it to storing that particular |
| `00:20:01.600 - 00:20:04.470` | inode but in any case it will increment |
| `00:20:04.480 - 00:20:06.070` | the reference count to indicate that |
| `00:20:06.080 - 00:20:09.909` | that structure in the cache is used for |
| `00:20:09.919 - 00:20:12.310` | that particular inode |
| `00:20:12.320 - 00:20:14.710` | it won't read in the inode from disk and |
| `00:20:14.720 - 00:20:16.950` | it won't lock the inode to do that we |
| `00:20:16.960 - 00:20:19.750` | call i lock which will block the node |
| `00:20:19.760 - 00:20:22.070` | and then if it has not yet been read in |
| `00:20:22.080 - 00:20:23.270` | from the desk |
| `00:20:23.280 - 00:20:25.430` | it will go ahead and read it in |
| `00:20:25.440 - 00:20:27.190` | so then when we're done using this |
| `00:20:27.200 - 00:20:30.070` | particular inode we call unlock and it |
| `00:20:30.080 - 00:20:33.590` | will unlock the node and we can call i |
| `00:20:33.600 - 00:20:34.470` | put |
| `00:20:34.480 - 00:20:36.710` | okay i put will decrement the reference |
| `00:20:36.720 - 00:20:37.590` | count |
| `00:20:37.600 - 00:20:39.510` | so if it was already there when we |
| `00:20:39.520 - 00:20:41.350` | called i get we incremented the |
| `00:20:41.360 - 00:20:43.909` | reference count and then when we're done |
| `00:20:43.919 - 00:20:46.870` | with it we call i put and it decrements |
| `00:20:46.880 - 00:20:48.950` | the reference count and it checks to see |
| `00:20:48.960 - 00:20:51.350` | whether we have no more hard links to it |
| `00:20:51.360 - 00:20:53.830` | and if so it gets rid of the file |
| `00:20:53.840 - 00:20:57.029` | uh but otherwise um the file will stay |
| `00:20:57.039 - 00:21:00.149` | in the cache if it uh decremented the |
| `00:21:00.159 - 00:21:02.149` | reference count to zero then that node |
| `00:21:02.159 - 00:21:04.390` | in the or that structure in the cache is |
| `00:21:04.400 - 00:21:06.549` | now available for reuse |
| `00:21:06.559 - 00:21:08.870` | but if there are other users of the |
| `00:21:08.880 - 00:21:10.470` | inode in the cache then the reference |
| `00:21:10.480 - 00:21:13.669` | count will not yet be zero and it will |
| `00:21:13.679 - 00:21:16.470` | uh persist for the other users |
| `00:21:16.480 - 00:21:18.870` | so let's first take a look at the |
| `00:21:18.880 - 00:21:20.549` | get function |
| `00:21:20.559 - 00:21:24.230` | which is here i get |
| `00:21:24.240 - 00:21:26.549` | find the inode with the number inum on |
| `00:21:26.559 - 00:21:28.789` | this particular device |
| `00:21:28.799 - 00:21:30.390` | and return |
| `00:21:30.400 - 00:21:33.510` | the in-memory copy okay but it will not |
| `00:21:33.520 - 00:21:35.990` | lock that and it will not necessarily |
| `00:21:36.000 - 00:21:37.590` | read it in from disk |
| `00:21:37.600 - 00:21:40.310` | okay so |
| `00:21:40.320 - 00:21:43.270` | in order to access the inode cache we |
| `00:21:43.280 - 00:21:46.630` | need to lock the itable lock |
| `00:21:46.640 - 00:21:48.549` | and then we have a loop that's going to |
| `00:21:48.559 - 00:21:50.630` | search the |
| `00:21:50.640 - 00:21:52.630` | cached inodes |
| `00:21:52.640 - 00:21:54.870` | so here we start with 0 and we go all |
| `00:21:54.880 - 00:21:57.270` | the way up through all 50 of them |
| `00:21:57.280 - 00:21:59.430` | and we look at each one of them so what |
| `00:21:59.440 - 00:22:01.029` | we're doing here |
| `00:22:01.039 - 00:22:03.510` | is in this picture we're acquiring this |
| `00:22:03.520 - 00:22:05.350` | spin lock and then we're going through |
| `00:22:05.360 - 00:22:07.830` | all 50 of these structures and we're |
| `00:22:07.840 - 00:22:10.789` | looking for one that |
| `00:22:10.799 - 00:22:13.830` | matches the device and i number |
| `00:22:13.840 - 00:22:16.310` | okay so we're looking for one that |
| `00:22:16.320 - 00:22:18.710` | in this loop |
| `00:22:18.720 - 00:22:21.190` | we are looking for one |
| `00:22:21.200 - 00:22:23.909` | whose device matches the device we're on |
| `00:22:23.919 - 00:22:26.310` | and whose i number matches and we're |
| `00:22:26.320 - 00:22:28.789` | looking for something that is not free |
| `00:22:28.799 - 00:22:30.630` | so if it's uh the reference count is |
| `00:22:30.640 - 00:22:33.350` | greater than zero then we've got one and |
| `00:22:33.360 - 00:22:35.990` | we increment the reference count and we |
| `00:22:36.000 - 00:22:38.230` | release the lock and return a pointer to |
| `00:22:38.240 - 00:22:40.950` | that inode okay but we might not find |
| `00:22:40.960 - 00:22:42.950` | one so |
| `00:22:42.960 - 00:22:45.029` | as we go through all 50 of these |
| `00:22:45.039 - 00:22:47.510` | structures we look for one that has a |
| `00:22:47.520 - 00:22:50.950` | reference count of zero and if so we |
| `00:22:50.960 - 00:22:53.190` | save a pointer to that one in this |
| `00:22:53.200 - 00:22:54.630` | variable empty |
| `00:22:54.640 - 00:22:56.549` | and here we're checking to see whether |
| `00:22:56.559 - 00:22:59.350` | we've already found one and we only save |
| `00:22:59.360 - 00:23:01.750` | the first one |
| `00:23:01.760 - 00:23:03.510` | and |
| `00:23:03.520 - 00:23:05.669` | then |
| `00:23:05.679 - 00:23:07.029` | we |
| `00:23:07.039 - 00:23:09.350` | exit this loop if we haven't found |
| `00:23:09.360 - 00:23:10.310` | one |
| `00:23:10.320 - 00:23:13.669` | and we are recycling or allocating a new |
| `00:23:13.679 - 00:23:15.430` | entry in the cache for this particular |
| `00:23:15.440 - 00:23:16.630` | inode |
| `00:23:16.640 - 00:23:19.590` | okay so we make sure we have enough |
| `00:23:19.600 - 00:23:21.270` | we actually found one otherwise we've |
| `00:23:21.280 - 00:23:24.470` | run out of inodes in our cache and |
| `00:23:24.480 - 00:23:25.750` | that's |
| `00:23:25.760 - 00:23:27.270` | calls for panic |
| `00:23:27.280 - 00:23:30.470` | but if we uh find one then |
| `00:23:30.480 - 00:23:31.750` | we are |
| `00:23:31.760 - 00:23:33.029` | pointing to it |
| `00:23:33.039 - 00:23:35.510` | and then we set its device and i number |
| `00:23:35.520 - 00:23:37.190` | to indicate that it's in use and we set |
| `00:23:37.200 - 00:23:39.029` | its reference count also to indicate |
| `00:23:39.039 - 00:23:42.149` | it's in use so now we have allocated it |
| `00:23:42.159 - 00:23:44.870` | and we said it's valid to zero now |
| `00:23:44.880 - 00:23:46.789` | previously i said that the valid field |
| `00:23:46.799 - 00:23:49.350` | was protected by the sleep lock of the |
| `00:23:49.360 - 00:23:50.950` | structure itself |
| `00:23:50.960 - 00:23:53.830` | so this sleep lock cult lock here |
| `00:23:53.840 - 00:23:55.669` | protects everything below it including |
| `00:23:55.679 - 00:23:58.230` | the valid field |
| `00:23:58.240 - 00:24:01.110` | so is this is this cool that we're |
| `00:24:01.120 - 00:24:02.870` | modifying the valid without holding the |
| `00:24:02.880 - 00:24:05.190` | sleep lock on this |
| `00:24:05.200 - 00:24:06.230` | well |
| `00:24:06.240 - 00:24:08.230` | we are the only process that is |
| `00:24:08.240 - 00:24:10.630` | accessing this inode okay because the |
| `00:24:10.640 - 00:24:12.470` | reference count is one here we just set |
| `00:24:12.480 - 00:24:14.390` | it to one |
| `00:24:14.400 - 00:24:16.870` | and we are holding the i table lock so |
| `00:24:16.880 - 00:24:19.750` | no other process could conceivably get a |
| `00:24:19.760 - 00:24:22.470` | pointer to this particular cache because |
| `00:24:22.480 - 00:24:25.590` | the reference count is protected by the |
| `00:24:25.600 - 00:24:27.110` | lock so |
| `00:24:27.120 - 00:24:30.870` | setting valid to zero here is safe |
| `00:24:30.880 - 00:24:33.830` | okay next let's look at the lock |
| `00:24:33.840 - 00:24:39.590` | function the i lock function |
| `00:24:39.600 - 00:24:41.830` | so it's passed a pointer to |
| `00:24:41.840 - 00:24:44.950` | one of these cached inode structures |
| `00:24:44.960 - 00:24:46.070` | and |
| `00:24:46.080 - 00:24:47.750` | it is making sure |
| `00:24:47.760 - 00:24:50.870` | that the reference count is really one |
| `00:24:50.880 - 00:24:54.549` | or greater and if not it panics |
| `00:24:54.559 - 00:24:56.950` | okay now we are going to modify the |
| `00:24:56.960 - 00:24:59.990` | fields of it so we need to begin by |
| `00:25:00.000 - 00:25:02.470` | acquiring the sleep lock |
| `00:25:02.480 - 00:25:06.549` | and this will |
| `00:25:06.559 - 00:25:07.669` | lock it |
| `00:25:07.679 - 00:25:08.950` | and |
| `00:25:08.960 - 00:25:11.029` | once we've gotten the sleep lock |
| `00:25:11.039 - 00:25:13.830` | we check the valid bit if the valid flag |
| `00:25:13.840 - 00:25:15.350` | indicates that we have not yet read the |
| `00:25:15.360 - 00:25:17.669` | data in from disk then we need to do |
| `00:25:17.679 - 00:25:21.029` | that so here we call b read |
| `00:25:21.039 - 00:25:24.070` | and we're reading from the device |
| `00:25:24.080 - 00:25:24.950` | and |
| `00:25:24.960 - 00:25:26.310` | the |
| `00:25:26.320 - 00:25:27.830` | call to i block which i should have |
| `00:25:27.840 - 00:25:29.510` | bolded here |
| `00:25:29.520 - 00:25:30.950` | is uh |
| `00:25:30.960 - 00:25:32.470` | called to |
| `00:25:32.480 - 00:25:35.590` | compute which block on the disk |
| `00:25:35.600 - 00:25:37.190` | this i number |
| `00:25:37.200 - 00:25:40.070` | uh the inode is kept in and then we read |
| `00:25:40.080 - 00:25:41.590` | that block |
| `00:25:41.600 - 00:25:43.269` | and here what we're doing is we're |
| `00:25:43.279 - 00:25:45.029` | moving everything |
| `00:25:45.039 - 00:25:48.630` | from the inode structure in that block |
| `00:25:48.640 - 00:25:49.830` | into |
| `00:25:49.840 - 00:25:51.830` | uh the |
| `00:25:51.840 - 00:25:55.029` | cached version pointed to by ip |
| `00:25:55.039 - 00:25:59.029` | so we're getting a pointer to the inode |
| `00:25:59.039 - 00:25:59.990` | in |
| `00:26:00.000 - 00:26:01.590` | the block so here we're accessing the |
| `00:26:01.600 - 00:26:03.990` | data of the block and we're figuring out |
| `00:26:04.000 - 00:26:06.630` | exactly where in that block this |
| `00:26:06.640 - 00:26:08.630` | particular inode is stored this is |
| `00:26:08.640 - 00:26:11.190` | inodes per block so we're figuring out |
| `00:26:11.200 - 00:26:12.070` | that |
| `00:26:12.080 - 00:26:12.950` | so |
| `00:26:12.960 - 00:26:13.909` | back in this picture we're just |
| `00:26:13.919 - 00:26:15.510` | basically computing |
| `00:26:15.520 - 00:26:20.549` | where in the block we need to read from |
| `00:26:20.559 - 00:26:21.909` | and that's |
| `00:26:21.919 - 00:26:22.870` | the |
| `00:26:22.880 - 00:26:25.350` | pointer for the source and then we're |
| `00:26:25.360 - 00:26:27.830` | copying the type major minor |
| `00:26:27.840 - 00:26:30.549` | in link size field as well as the |
| `00:26:30.559 - 00:26:33.510` | address array here okay so here we're |
| `00:26:33.520 - 00:26:37.269` | copying the address uh field from dip to |
| `00:26:37.279 - 00:26:39.510` | the cached copy and finally we're |
| `00:26:39.520 - 00:26:41.510` | releasing the buffer that we use to read |
| `00:26:41.520 - 00:26:44.149` | in the inode from disk and setting the |
| `00:26:44.159 - 00:26:46.789` | valid flag to true |
| `00:26:46.799 - 00:26:49.269` | and we're also making sure that the type |
| `00:26:49.279 - 00:26:50.789` | that we just read in |
| `00:26:50.799 - 00:26:52.870` | is not zero |
| `00:26:52.880 - 00:26:55.510` | we shouldn't be reading in a |
| `00:26:55.520 - 00:26:58.310` | i know that's not allocated |
| `00:26:58.320 - 00:27:00.950` | okay so unlock is pretty straightforward |
| `00:27:00.960 - 00:27:02.549` | the i unlock function has passed a |
| `00:27:02.559 - 00:27:05.029` | pointer to one of these cache inodes |
| `00:27:05.039 - 00:27:07.830` | and it just releases the sleep lock |
| `00:27:07.840 - 00:27:09.430` | okay it does a little checking here to |
| `00:27:09.440 - 00:27:10.789` | make sure that |
| `00:27:10.799 - 00:27:12.470` | we have a pointer and we're actually |
| `00:27:12.480 - 00:27:14.149` | holding the sleep block |
| `00:27:14.159 - 00:27:17.110` | and the reference count can't be zero we |
| `00:27:17.120 - 00:27:20.389` | better be referring to it and so then we |
| `00:27:20.399 - 00:27:22.149` | release the sleep log |
| `00:27:22.159 - 00:27:24.549` | so next let's take a look at the i put |
| `00:27:24.559 - 00:27:27.029` | function |
| `00:27:27.039 - 00:27:28.710` | okay remember what the i put function is |
| `00:27:28.720 - 00:27:29.750` | going to do |
| `00:27:29.760 - 00:27:31.110` | it's going to decrement the reference |
| `00:27:31.120 - 00:27:34.310` | count and if that reference count |
| `00:27:34.320 - 00:27:36.310` | is now zero that means no other |
| `00:27:36.320 - 00:27:37.750` | processes are |
| `00:27:37.760 - 00:27:40.310` | using the cached copy and the number of |
| `00:27:40.320 - 00:27:42.789` | hard links to this file is zero then |
| `00:27:42.799 - 00:27:44.870` | we're going to eliminate the file we're |
| `00:27:44.880 - 00:27:46.870` | going to truncate it to link zero set |
| `00:27:46.880 - 00:27:49.029` | its type to 0 and update everything on |
| `00:27:49.039 - 00:27:51.350` | the desk and finally unlock the inode |
| `00:27:51.360 - 00:27:53.269` | with a reference count of 0. |
| `00:27:53.279 - 00:27:55.350` | the cached structure will now be free to |
| `00:27:55.360 - 00:27:57.750` | be reused for some other inode |
| `00:27:57.760 - 00:27:59.830` | so let's take a look at the i put |
| `00:27:59.840 - 00:28:00.870` | function |
| `00:28:00.880 - 00:28:04.149` | when a process is done with a file it's |
| `00:28:04.159 - 00:28:06.549` | done with the cached copy the inode and |
| `00:28:06.559 - 00:28:09.590` | we need to allow other processes to use |
| `00:28:09.600 - 00:28:10.310` | the |
| `00:28:10.320 - 00:28:12.870` | memory and the cache for other inodes |
| `00:28:12.880 - 00:28:15.029` | so let me read this what we're going to |
| `00:28:15.039 - 00:28:17.669` | do is drop a reference to an in-memory |
| `00:28:17.679 - 00:28:19.669` | cached copy of an inode |
| `00:28:19.679 - 00:28:22.470` | if that was the last reference um |
| `00:28:22.480 - 00:28:23.430` | then |
| `00:28:23.440 - 00:28:24.230` | the |
| `00:28:24.240 - 00:28:26.789` | cached space the space in the cache can |
| `00:28:26.799 - 00:28:28.389` | be recycled |
| `00:28:28.399 - 00:28:29.909` | and furthermore if that was the last |
| `00:28:29.919 - 00:28:31.909` | hard link to the file |
| `00:28:31.919 - 00:28:35.510` | then this inode is not reachable and so |
| `00:28:35.520 - 00:28:38.789` | we need to free both the inode and all |
| `00:28:38.799 - 00:28:41.590` | of its data and its block of indirection |
| `00:28:41.600 - 00:28:43.350` | as well if it has one |
| `00:28:43.360 - 00:28:44.710` | so |
| `00:28:44.720 - 00:28:47.190` | as is true of all the functions in this |
| `00:28:47.200 - 00:28:49.269` | fs.c file |
| `00:28:49.279 - 00:28:51.110` | this function must be inside a |
| `00:28:51.120 - 00:28:52.310` | transaction |
| `00:28:52.320 - 00:28:54.149` | okay so |
| `00:28:54.159 - 00:28:57.029` | it's passed a pointer to the cached copy |
| `00:28:57.039 - 00:28:58.470` | of the inode |
| `00:28:58.480 - 00:29:01.909` | and it's going to acquire a lock on the |
| `00:29:01.919 - 00:29:03.590` | cache because we're going to be |
| `00:29:03.600 - 00:29:05.990` | modifying the reference count so the way |
| `00:29:06.000 - 00:29:07.669` | this is actually organized is a bit |
| `00:29:07.679 - 00:29:10.149` | different than i showed in my out in my |
| `00:29:10.159 - 00:29:11.990` | handwritten stuff we have the if |
| `00:29:12.000 - 00:29:13.990` | statement first followed by the |
| `00:29:14.000 - 00:29:16.310` | decrement of the reference count and |
| `00:29:16.320 - 00:29:20.710` | then we release the lock on the cache |
| `00:29:20.720 - 00:29:22.789` | uh the reason we want to decrement it |
| `00:29:22.799 - 00:29:24.710` | last is because if it turns out we're |
| `00:29:24.720 - 00:29:26.710` | going to be deleting the file we want to |
| `00:29:26.720 - 00:29:29.029` | maintain a reference count of one so |
| `00:29:29.039 - 00:29:31.190` | here we're looking to see whether |
| `00:29:31.200 - 00:29:33.029` | we have a reference count of one that is |
| `00:29:33.039 - 00:29:35.350` | we are the last process that is |
| `00:29:35.360 - 00:29:37.830` | accessing this |
| `00:29:37.840 - 00:29:40.549` | cached copy of that inode and if that's |
| `00:29:40.559 - 00:29:42.389` | the case and the number of hard lengths |
| `00:29:42.399 - 00:29:43.990` | is zero |
| `00:29:44.000 - 00:29:46.230` | uh then we can go ahead and delete the |
| `00:29:46.240 - 00:29:48.070` | file okay so |
| `00:29:48.080 - 00:29:50.630` | we will be uh truncating it to link zero |
| `00:29:50.640 - 00:29:52.870` | and setting its type to zero |
| `00:29:52.880 - 00:29:55.750` | in order to modify the type we need to |
| `00:29:55.760 - 00:29:56.950` | be holding |
| `00:29:56.960 - 00:29:59.990` | this inode's sleep lock so here we |
| `00:30:00.000 - 00:30:02.789` | acquire the sweep block and here we |
| `00:30:02.799 - 00:30:04.630` | release the sleep lock |
| `00:30:04.640 - 00:30:07.909` | and then we call i trunk which will look |
| `00:30:07.919 - 00:30:10.389` | at this inode and it will look at all |
| `00:30:10.399 - 00:30:12.549` | the data blocks it points to either |
| `00:30:12.559 - 00:30:14.950` | directly or indirectly and it will |
| `00:30:14.960 - 00:30:17.430` | eliminate all of those or i should say |
| `00:30:17.440 - 00:30:19.909` | it will return those to the free pool |
| `00:30:19.919 - 00:30:22.470` | and update the bitmap and so on |
| `00:30:22.480 - 00:30:26.470` | and uh then we set its type to zero so |
| `00:30:26.480 - 00:30:29.350` | that makes it an unused inode previously |
| `00:30:29.360 - 00:30:30.789` | its type was either regular file |
| `00:30:30.799 - 00:30:33.350` | directory or device and by setting its |
| `00:30:33.360 - 00:30:35.830` | type to zero we indicate that it is free |
| `00:30:35.840 - 00:30:38.630` | but this is the in memory cached version |
| `00:30:38.640 - 00:30:41.269` | so here we write that to |
| `00:30:41.279 - 00:30:43.269` | the disk so at this point we are |
| `00:30:43.279 - 00:30:45.830` | updating the disk and now the inode |
| `00:30:45.840 - 00:30:47.830` | that's stored on the disk will also have |
| `00:30:47.840 - 00:30:52.070` | a type code of zero and um we then we |
| `00:30:52.080 - 00:30:54.870` | set the valid bit to zero so |
| `00:30:54.880 - 00:30:56.070` | um |
| `00:30:56.080 - 00:30:57.750` | now |
| `00:30:57.760 - 00:31:00.950` | here we are releasing the uh lock for |
| `00:31:00.960 - 00:31:02.070` | the cache |
| `00:31:02.080 - 00:31:04.789` | and then reacquiring it right here |
| `00:31:04.799 - 00:31:07.350` | uh we need to be holding the lock for |
| `00:31:07.360 - 00:31:09.350` | the cash before we access the reference |
| `00:31:09.360 - 00:31:11.269` | count so we need to reacquire it if we |
| `00:31:11.279 - 00:31:12.389` | release it |
| `00:31:12.399 - 00:31:14.710` | um why do we release it |
| `00:31:14.720 - 00:31:17.909` | well as far as i can tell there's not a |
| `00:31:17.919 - 00:31:19.830` | deadlock concern we're not releasing it |
| `00:31:19.840 - 00:31:22.149` | for that reason um and i think we could |
| `00:31:22.159 - 00:31:24.870` | it would be legal or valid or correct to |
| `00:31:24.880 - 00:31:26.630` | just hold on to this lock |
| `00:31:26.640 - 00:31:28.149` | you know after all we've acquired this |
| `00:31:28.159 - 00:31:29.509` | lock in this lock |
| `00:31:29.519 - 00:31:32.710` | and we're not acquiring any more locks |
| `00:31:32.720 - 00:31:35.990` | we can release this lock here or |
| `00:31:36.000 - 00:31:38.549` | not but there's no problem with holding |
| `00:31:38.559 - 00:31:40.549` | on to it as far as i can tell |
| `00:31:40.559 - 00:31:42.630` | but it's uh if we do release it we need |
| `00:31:42.640 - 00:31:44.470` | to reacquire it of course |
| `00:31:44.480 - 00:31:46.870` | but it's just a question of politeness |
| `00:31:46.880 - 00:31:49.029` | okay we're going to be calling eye trunk |
| `00:31:49.039 - 00:31:51.029` | and that's going to be doing some disk |
| `00:31:51.039 - 00:31:53.190` | writes or more precisely it's going to |
| `00:31:53.200 - 00:31:56.549` | be calling log right and b read |
| `00:31:56.559 - 00:31:58.070` | which will |
| `00:31:58.080 - 00:32:01.110` | need to allocate buffers and uh |
| `00:32:01.120 - 00:32:04.070` | modify the log and so on could end the |
| `00:32:04.080 - 00:32:05.669` | update uh it's the same thing we're |
| `00:32:05.679 - 00:32:07.350` | going to be um |
| `00:32:07.360 - 00:32:10.310` | calling logwrite to update the log file |
| `00:32:10.320 - 00:32:11.750` | all of this stuff could take a little |
| `00:32:11.760 - 00:32:12.710` | while |
| `00:32:12.720 - 00:32:15.509` | the cache of inodes is somewhat of a |
| `00:32:15.519 - 00:32:16.710` | bottleneck |
| `00:32:16.720 - 00:32:18.470` | it could be that the cache holds the |
| `00:32:18.480 - 00:32:20.149` | inodes for some |
| `00:32:20.159 - 00:32:23.110` | very important and popular files and so |
| `00:32:23.120 - 00:32:25.509` | we'd like to release this spin lock as |
| `00:32:25.519 - 00:32:27.830` | soon as we can i think that's why it's |
| `00:32:27.840 - 00:32:29.830` | released here but then of course it's |
| `00:32:29.840 - 00:32:31.830` | reacquired |
| `00:32:31.840 - 00:32:32.950` | now |
| `00:32:32.960 - 00:32:34.070` | then we have the question of why is |
| `00:32:34.080 - 00:32:36.310` | valid being set to zero here we've |
| `00:32:36.320 - 00:32:38.070` | already set type to zero which indicates |
| `00:32:38.080 - 00:32:40.230` | that this inode is unused |
| `00:32:40.240 - 00:32:42.149` | but if you look right here there's a |
| `00:32:42.159 - 00:32:44.230` | moment of vulnerability okay we've |
| `00:32:44.240 - 00:32:45.909` | released the spin lock and we've |
| `00:32:45.919 - 00:32:47.990` | released the sleep lock so at this point |
| `00:32:48.000 - 00:32:50.389` | right here we're not holding any locks |
| `00:32:50.399 - 00:32:53.509` | and we haven't looked at i alec yet |
| `00:32:53.519 - 00:32:56.710` | but what i alec is going to do is look |
| `00:32:56.720 - 00:32:59.350` | on the disk and find an inode whose type |
| `00:32:59.360 - 00:33:01.750` | is zero and so it might find |
| `00:33:01.760 - 00:33:04.549` | this very inode the item with the same |
| `00:33:04.559 - 00:33:07.110` | number that we just uh wrote out here |
| `00:33:07.120 - 00:33:08.470` | and it might say okay i'm going to |
| `00:33:08.480 - 00:33:11.029` | recycle that for some other file |
| `00:33:11.039 - 00:33:12.149` | and then what |
| `00:33:12.159 - 00:33:14.789` | it will do is um call iget to see if |
| `00:33:14.799 - 00:33:16.549` | there's already a cached version in |
| `00:33:16.559 - 00:33:17.590` | memory |
| `00:33:17.600 - 00:33:20.710` | okay and so um there is a cached version |
| `00:33:20.720 - 00:33:21.990` | in memory so |
| `00:33:22.000 - 00:33:23.430` | we need to make sure that it doesn't get |
| `00:33:23.440 - 00:33:24.710` | used so |
| `00:33:24.720 - 00:33:26.710` | that is |
| `00:33:26.720 - 00:33:29.830` | why we need to set valid to zero right |
| `00:33:29.840 - 00:33:30.710` | here |
| `00:33:30.720 - 00:33:32.630` | okay |
| `00:33:32.640 - 00:33:35.430` | the reference count is still one so when |
| `00:33:35.440 - 00:33:37.990` | we look for it in the cache in the cache |
| `00:33:38.000 - 00:33:41.350` | i alec will find it or it might find it |
| `00:33:41.360 - 00:33:44.470` | and try to reuse the cached version |
| `00:33:44.480 - 00:33:47.830` | well the cached version um is |
| `00:33:47.840 - 00:33:50.310` | not correct |
| `00:33:50.320 - 00:33:52.870` | i alec will have modified the type on |
| `00:33:52.880 - 00:33:55.590` | the disk and it needs to read that back |
| `00:33:55.600 - 00:33:58.549` | in so the type in memory is also |
| `00:33:58.559 - 00:33:59.830` | adjusted |
| `00:33:59.840 - 00:34:01.269` | and we can't do that here because we |
| `00:34:01.279 - 00:34:02.950` | don't know what the type is |
| `00:34:02.960 - 00:34:05.909` | so um i think that's why we are setting |
| `00:34:05.919 - 00:34:08.069` | valid to false right here |
| `00:34:08.079 - 00:34:12.550` | so i hope that explains uh i put |
| `00:34:12.560 - 00:34:14.790` | the other function we have down here is |
| `00:34:14.800 - 00:34:18.389` | called i unlock put and all it does is |
| `00:34:18.399 - 00:34:21.829` | unlock an inode and call put so it's |
| `00:34:21.839 - 00:34:23.750` | very common that after we've modified or |
| `00:34:23.760 - 00:34:27.349` | used a file we are ready to both un |
| `00:34:27.359 - 00:34:29.909` | unlock its inode and |
| `00:34:29.919 - 00:34:31.109` | uh |
| `00:34:31.119 - 00:34:32.790` | get rid of our reference to it we're all |
| `00:34:32.800 - 00:34:37.669` | done with it so this is a common idiom |
| `00:34:37.679 - 00:34:39.990` | next let's take a look at the ialoc |
| `00:34:40.000 - 00:34:42.149` | function this is called whenever we want |
| `00:34:42.159 - 00:34:44.710` | to create a file we pass it the device |
| `00:34:44.720 - 00:34:45.990` | it indicates |
| `00:34:46.000 - 00:34:48.230` | where we want to create the file and the |
| `00:34:48.240 - 00:34:50.629` | type which will either be a regular file |
| `00:34:50.639 - 00:34:53.909` | a directory file or a device type |
| `00:34:53.919 - 00:34:54.950` | and |
| `00:34:54.960 - 00:34:57.030` | what this will do is find an unused |
| `00:34:57.040 - 00:34:59.430` | inode on the device and mark it as |
| `00:34:59.440 - 00:35:01.990` | allocated by setting its type field to |
| `00:35:02.000 - 00:35:04.390` | whatever we were passed here |
| `00:35:04.400 - 00:35:07.670` | and then it will call i get here to |
| `00:35:07.680 - 00:35:09.829` | return |
| `00:35:09.839 - 00:35:13.670` | a cached copy of that inode |
| `00:35:13.680 - 00:35:16.790` | okay so let's go through the code here |
| `00:35:16.800 - 00:35:20.069` | we are going to uh loop through all |
| `00:35:20.079 - 00:35:22.150` | possible i numbers |
| `00:35:22.160 - 00:35:24.630` | we skip 0 because 0 is not a valid i |
| `00:35:24.640 - 00:35:26.870` | number and so we go from 1 all the way |
| `00:35:26.880 - 00:35:29.990` | up to our maximum number in our example |
| `00:35:30.000 - 00:35:31.990` | we have 199 |
| `00:35:32.000 - 00:35:37.910` | possible inodes so we loop from 1 to 199 |
| `00:35:37.920 - 00:35:39.990` | and for each one of those we need to |
| `00:35:40.000 - 00:35:41.589` | read it from disk |
| `00:35:41.599 - 00:35:43.270` | okay so we'll call |
| `00:35:43.280 - 00:35:45.109` | b read |
| `00:35:45.119 - 00:35:48.470` | i block is past the i number and it will |
| `00:35:48.480 - 00:35:51.510` | determine which block this inode is |
| `00:35:51.520 - 00:35:52.790` | stored on |
| `00:35:52.800 - 00:35:53.589` | so |
| `00:35:53.599 - 00:35:55.750` | the first one number one will be stored |
| `00:35:55.760 - 00:35:57.589` | on this block which happens to be block |
| `00:35:57.599 - 00:35:58.630` | 32 |
| `00:35:58.640 - 00:36:01.589` | and so we'll use block 32 for several |
| `00:36:01.599 - 00:36:06.950` | and then we'll go to block 3 3 and so on |
| `00:36:06.960 - 00:36:09.990` | so then we compute once the b read |
| `00:36:10.000 - 00:36:12.550` | returns it has a pointer to the buffer |
| `00:36:12.560 - 00:36:14.150` | that we've read in |
| `00:36:14.160 - 00:36:16.230` | and we then figure out where in that |
| `00:36:16.240 - 00:36:19.109` | buffer we can find this particular inode |
| `00:36:19.119 - 00:36:20.069` | so |
| `00:36:20.079 - 00:36:21.829` | here's the pointer to the data of the |
| `00:36:21.839 - 00:36:25.190` | buffer and we take the i number |
| `00:36:25.200 - 00:36:28.069` | modulo the inodes per |
| `00:36:28.079 - 00:36:31.589` | block and that tells where in the |
| `00:36:31.599 - 00:36:34.310` | buffer we could find the on this data |
| `00:36:34.320 - 00:36:36.470` | for this inode and then we look at its |
| `00:36:36.480 - 00:36:38.790` | type field and if that's zero |
| `00:36:38.800 - 00:36:41.270` | okay then we found one that's free |
| `00:36:41.280 - 00:36:42.710` | okay |
| `00:36:42.720 - 00:36:44.790` | if it's not 0 then we release this |
| `00:36:44.800 - 00:36:46.310` | buffer and |
| `00:36:46.320 - 00:36:49.030` | loop back to the next i number and call |
| `00:36:49.040 - 00:36:51.990` | b read again now most likely |
| `00:36:52.000 - 00:36:55.270` | when we call b read the next time uh the |
| `00:36:55.280 - 00:36:57.670` | block that we need is just sitting in |
| `00:36:57.680 - 00:36:59.910` | the buffer cache okay we just read it |
| `00:36:59.920 - 00:37:02.950` | previously for the the inode before this |
| `00:37:02.960 - 00:37:05.109` | and this new inode is probably on the |
| `00:37:05.119 - 00:37:07.030` | same block so we don't actually do a |
| `00:37:07.040 - 00:37:09.349` | disc read for most of them |
| `00:37:09.359 - 00:37:11.430` | so we just loop through |
| `00:37:11.440 - 00:37:12.390` | the |
| `00:37:12.400 - 00:37:14.630` | inodes in one block |
| `00:37:14.640 - 00:37:17.270` | ultimate eventually we hope to find one |
| `00:37:17.280 - 00:37:19.750` | that has a type of zero if we |
| `00:37:19.760 - 00:37:22.790` | don't and we get all the way up to 199 |
| `00:37:22.800 - 00:37:26.150` | then we'll exit this loop and panic |
| `00:37:26.160 - 00:37:29.190` | that we have already allocated as many |
| `00:37:29.200 - 00:37:31.589` | files as we possibly can i think earlier |
| `00:37:31.599 - 00:37:34.550` | i said we have a maximum of 200 files |
| `00:37:34.560 - 00:37:36.390` | but since we don't use zero we have a |
| `00:37:36.400 - 00:37:40.150` | maximum of 199 files |
| `00:37:40.160 - 00:37:42.550` | um okay so let's say we find an inode |
| `00:37:42.560 - 00:37:44.390` | with type equal to zero |
| `00:37:44.400 - 00:37:46.630` | then what we're going to do here is zero |
| `00:37:46.640 - 00:37:49.030` | out all the fields in |
| `00:37:49.040 - 00:37:51.670` | the inode on disk so |
| `00:37:51.680 - 00:37:53.910` | we copied zeros into |
| `00:37:53.920 - 00:37:56.310` | this buffer |
| `00:37:56.320 - 00:37:58.310` | and here we're doing that this is the |
| `00:37:58.320 - 00:38:01.109` | size of |
| `00:38:01.119 - 00:38:04.150` | the inode on disk |
| `00:38:04.160 - 00:38:05.030` | and |
| `00:38:05.040 - 00:38:06.710` | then we set the type |
| `00:38:06.720 - 00:38:08.470` | field to whatever argument we were |
| `00:38:08.480 - 00:38:09.349` | passed |
| `00:38:09.359 - 00:38:11.670` | and we write that buffer back to disk |
| `00:38:11.680 - 00:38:14.630` | and release it and now that we've got |
| `00:38:14.640 - 00:38:18.310` | the inode updated on disk we call i get |
| `00:38:18.320 - 00:38:21.589` | and it i get will look through the cache |
| `00:38:21.599 - 00:38:24.870` | and find an empty slot for this inode |
| `00:38:24.880 - 00:38:27.030` | and then it will fill in the device and |
| `00:38:27.040 - 00:38:28.870` | i number as well as setting the |
| `00:38:28.880 - 00:38:31.510` | reference count to 1 and it will return |
| `00:38:31.520 - 00:38:34.230` | a pointer to this um in |
| `00:38:34.240 - 00:38:37.270` | memory cached version of the inode the |
| `00:38:37.280 - 00:38:39.430` | valid flag will still be zero because we |
| `00:38:39.440 - 00:38:43.589` | set it to zero earlier and um so unused |
| `00:38:43.599 - 00:38:46.470` | entries in the cache we'll have a valid |
| `00:38:46.480 - 00:38:49.670` | flag being equal to zero and |
| `00:38:49.680 - 00:38:53.109` | in uh that when we call lock when we |
| `00:38:53.119 - 00:38:55.190` | call ilock we will go ahead and read the |
| `00:38:55.200 - 00:38:56.710` | data in |
| `00:38:56.720 - 00:39:01.030` | from disk and copy it into this area |
| `00:39:01.040 - 00:39:03.829` | i alec is only called from the create |
| `00:39:03.839 - 00:39:06.230` | system call and |
| `00:39:06.240 - 00:39:08.069` | that system call will immediately call |
| `00:39:08.079 - 00:39:10.950` | ilock so it will |
| `00:39:10.960 - 00:39:14.790` | read the data from the disk okay and it |
| `00:39:14.800 - 00:39:16.470` | will copy it into |
| `00:39:16.480 - 00:39:17.190` | the |
| `00:39:17.200 - 00:39:19.270` | cached structure |
| `00:39:19.280 - 00:39:21.829` | and since the block is probably still |
| `00:39:21.839 - 00:39:23.829` | sitting in the buffer it won't involve a |
| `00:39:23.839 - 00:39:28.550` | second read of the disk |
| `00:39:28.560 - 00:39:30.950` | whenever we update the inode associated |
| `00:39:30.960 - 00:39:32.630` | with any file we need to write those |
| `00:39:32.640 - 00:39:35.510` | changes back to the disk and that's the |
| `00:39:35.520 - 00:39:38.310` | function of the i update function |
| `00:39:38.320 - 00:39:40.470` | so it's passed a pointer to some |
| `00:39:40.480 - 00:39:43.109` | cached inode and it will write it to |
| `00:39:43.119 - 00:39:46.390` | disk so copy a modified in-memory cache |
| `00:39:46.400 - 00:39:49.190` | version of an inode back to the disk |
| `00:39:49.200 - 00:39:51.270` | it must be called after every change to |
| `00:39:51.280 - 00:39:54.069` | a field in the inode that is stored on |
| `00:39:54.079 - 00:39:55.670` | the desk |
| `00:39:55.680 - 00:39:57.030` | and |
| `00:39:57.040 - 00:39:58.630` | the caller of this function had better |
| `00:39:58.640 - 00:40:00.390` | be holding the sleep lock associated |
| `00:40:00.400 - 00:40:04.470` | with this inode |
| `00:40:04.480 - 00:40:06.390` | so we saw that |
| `00:40:06.400 - 00:40:09.109` | this was called in the |
| `00:40:09.119 - 00:40:10.950` | i put routine so here's the i put |
| `00:40:10.960 - 00:40:13.589` | routine remember we uh if we're going to |
| `00:40:13.599 - 00:40:16.230` | delete the file we set its type to zero |
| `00:40:16.240 - 00:40:18.550` | and then immediately write that to disk |
| `00:40:18.560 - 00:40:20.790` | so here's an example where we call i |
| `00:40:20.800 - 00:40:23.670` | update |
| `00:40:23.680 - 00:40:24.790` | this is the code is pretty |
| `00:40:24.800 - 00:40:26.150` | straightforward |
| `00:40:26.160 - 00:40:28.630` | first we invoke iblock |
| `00:40:28.640 - 00:40:30.309` | giving it the |
| `00:40:30.319 - 00:40:33.270` | i number of this particular inode and it |
| `00:40:33.280 - 00:40:36.470` | computes which block contains that inode |
| `00:40:36.480 - 00:40:37.670` | and then we |
| `00:40:37.680 - 00:40:41.030` | read that block from disk into a buffer |
| `00:40:41.040 - 00:40:43.990` | okay then we compute where in that |
| `00:40:44.000 - 00:40:46.870` | buffer we can find this particular inode |
| `00:40:46.880 - 00:40:49.990` | so we take the i number modulo the |
| `00:40:50.000 - 00:40:51.589` | inodes per block |
| `00:40:51.599 - 00:40:54.390` | and then add that as an offset to |
| `00:40:54.400 - 00:40:57.750` | where the data is for that block |
| `00:40:57.760 - 00:40:59.109` | and uh |
| `00:40:59.119 - 00:41:01.510` | that gives us a pointer to into the |
| `00:41:01.520 - 00:41:04.710` | buffer and so then we set the type the |
| `00:41:04.720 - 00:41:07.589` | major the minor the end link and the |
| `00:41:07.599 - 00:41:09.990` | size right we set the |
| `00:41:10.000 - 00:41:12.790` | type major minor in link and size and |
| `00:41:12.800 - 00:41:14.309` | then we set the |
| `00:41:14.319 - 00:41:15.750` | uh |
| `00:41:15.760 - 00:41:17.190` | address array |
| `00:41:17.200 - 00:41:21.190` | so we copy it from the cached copy in |
| `00:41:21.200 - 00:41:21.990` | the |
| `00:41:22.000 - 00:41:24.550` | itable cache okay so we're copying it |
| `00:41:24.560 - 00:41:26.470` | from this area here |
| `00:41:26.480 - 00:41:28.630` | into the buffer |
| `00:41:28.640 - 00:41:31.190` | and then we'll write the buffer back to |
| `00:41:31.200 - 00:41:32.150` | disk |
| `00:41:32.160 - 00:41:33.510` | so |
| `00:41:33.520 - 00:41:34.710` | that's what we're doing with these |
| `00:41:34.720 - 00:41:37.109` | copies and here we're copying the |
| `00:41:37.119 - 00:41:39.589` | address array the size of this array is |
| `00:41:39.599 - 00:41:42.790` | uh 13 that is the number of direct |
| `00:41:42.800 - 00:41:44.950` | pointers plus one more |
| `00:41:44.960 - 00:41:48.550` | and then we write that buffer back to |
| `00:41:48.560 - 00:41:51.270` | memory |
| `00:41:51.280 - 00:41:52.470` | and |
| `00:41:52.480 - 00:41:55.510` | release the buffer |
| `00:41:55.520 - 00:41:58.069` | from time to time we need to truncate a |
| `00:41:58.079 - 00:42:00.309` | file to link zero that is we need to get |
| `00:42:00.319 - 00:42:02.470` | rid of all of its data |
| `00:42:02.480 - 00:42:05.589` | one place we do this is in the i put |
| `00:42:05.599 - 00:42:06.710` | function |
| `00:42:06.720 - 00:42:09.589` | so here's the i put function and if |
| `00:42:09.599 - 00:42:11.030` | we've determined that the number of hard |
| `00:42:11.040 - 00:42:13.670` | links to this file is 0 we're going to |
| `00:42:13.680 - 00:42:15.829` | go ahead and delete the file so we call |
| `00:42:15.839 - 00:42:18.790` | i trunk or i truncate here and then we |
| `00:42:18.800 - 00:42:20.470` | go ahead and set the type to zero and so |
| `00:42:20.480 - 00:42:21.589` | on |
| `00:42:21.599 - 00:42:24.069` | another place that i trunk is called is |
| `00:42:24.079 - 00:42:27.030` | in the open system call code we have the |
| `00:42:27.040 - 00:42:28.950` | option there of truncating the file to |
| `00:42:28.960 - 00:42:30.390` | length 0. |
| `00:42:30.400 - 00:42:32.630` | so let's take a look at what this file |
| `00:42:32.640 - 00:42:33.829` | does |
| `00:42:33.839 - 00:42:36.710` | the caller must hold the sleep lock on |
| `00:42:36.720 - 00:42:39.670` | the cached version of the inode so it's |
| `00:42:39.680 - 00:42:41.190` | passed a pointer to the |
| `00:42:41.200 - 00:42:44.150` | inode it's cached and it's going to go |
| `00:42:44.160 - 00:42:47.190` | through that inode and look at all the |
| `00:42:47.200 - 00:42:49.030` | data blocks it points to |
| `00:42:49.040 - 00:42:50.870` | so here's the picture |
| `00:42:50.880 - 00:42:52.309` | it's passed a pointer to one of the |
| `00:42:52.319 - 00:42:54.230` | cached copies and it's going to go |
| `00:42:54.240 - 00:42:56.150` | through this array here and let me use |
| `00:42:56.160 - 00:42:57.910` | this array up here because it has the |
| `00:42:57.920 - 00:43:00.390` | blocks so we're going to go through the |
| `00:43:00.400 - 00:43:02.550` | direct pointers and if they are non-zero |
| `00:43:02.560 - 00:43:05.990` | we're going to call b3 to return this |
| `00:43:06.000 - 00:43:08.069` | block to the free pool |
| `00:43:08.079 - 00:43:09.510` | and then we're going to go through the |
| `00:43:09.520 - 00:43:12.790` | block of indirect pointers |
| `00:43:12.800 - 00:43:15.190` | so that's what's happening here |
| `00:43:15.200 - 00:43:18.950` | we go through the direct pointers all in |
| `00:43:18.960 - 00:43:21.190` | our case we have 12 of them and so it's |
| `00:43:21.200 - 00:43:25.030` | from 0 to 11 and we look at each entry |
| `00:43:25.040 - 00:43:27.990` | in the array in this inode and if it is |
| `00:43:28.000 - 00:43:31.190` | non-zero then it has a pointer and so we |
| `00:43:31.200 - 00:43:32.470` | need to |
| `00:43:32.480 - 00:43:36.069` | call b3 using that block number and what |
| `00:43:36.079 - 00:43:38.790` | this will do is update the bitmap to |
| `00:43:38.800 - 00:43:41.109` | change the bit for this block |
| `00:43:41.119 - 00:43:42.950` | from one |
| `00:43:42.960 - 00:43:46.069` | meaning in use to zero meaning free |
| `00:43:46.079 - 00:43:48.550` | and then we zero out the |
| `00:43:48.560 - 00:43:50.230` | element in the array |
| `00:43:50.240 - 00:43:52.069` | and so after we've done all the direct |
| `00:43:52.079 - 00:43:54.630` | pointers we need to ask whether there is |
| `00:43:54.640 - 00:43:55.670` | a |
| `00:43:55.680 - 00:43:57.829` | block of indirect pointers |
| `00:43:57.839 - 00:44:00.230` | so we ask whether this pointer here is |
| `00:44:00.240 - 00:44:04.069` | zero or not and then if it is not zero |
| `00:44:04.079 - 00:44:06.150` | we need to read this block and then go |
| `00:44:06.160 - 00:44:08.230` | through all of these pointers for each |
| `00:44:08.240 - 00:44:09.750` | one of these and then free the block |
| `00:44:09.760 - 00:44:10.870` | itself |
| `00:44:10.880 - 00:44:13.430` | so that's what's happening here |
| `00:44:13.440 - 00:44:15.190` | we ask |
| `00:44:15.200 - 00:44:17.510` | uh is the last element okay that's the |
| `00:44:17.520 - 00:44:19.990` | 13th element or we have 12 direct |
| `00:44:20.000 - 00:44:21.990` | pointers so |
| `00:44:22.000 - 00:44:23.829` | element number 12 which is the 13th |
| `00:44:23.839 - 00:44:26.950` | element of the array if it is non-zero |
| `00:44:26.960 - 00:44:29.750` | then we have an indirect block so here |
| `00:44:29.760 - 00:44:33.589` | we call b read to read that indirect |
| `00:44:33.599 - 00:44:38.150` | block into some buffer and here we find |
| `00:44:38.160 - 00:44:41.349` | where in that buffer uh the data is |
| `00:44:41.359 - 00:44:44.550` | actually stored okay and that we will |
| `00:44:44.560 - 00:44:46.870` | consider to be an array okay it's |
| `00:44:46.880 - 00:44:48.309` | considered to be |
| `00:44:48.319 - 00:44:50.230` | this array here and we're going to index |
| `00:44:50.240 - 00:44:53.510` | through it so in this loop here we are |
| `00:44:53.520 - 00:44:55.990` | indexing from 0 up to well in our case |
| `00:44:56.000 - 00:44:59.430` | we have we go from 0 to 255 |
| `00:44:59.440 - 00:45:02.150` | and we look at each element in the array |
| `00:45:02.160 - 00:45:05.750` | and ask if it's non-zero or not and then |
| `00:45:05.760 - 00:45:08.309` | if there is a pointer there then we call |
| `00:45:08.319 - 00:45:11.829` | b3 providing that block number |
| `00:45:11.839 - 00:45:14.230` | and that will free the block |
| `00:45:14.240 - 00:45:17.109` | so we go through this array |
| `00:45:17.119 - 00:45:19.990` | one by one and free these blocks |
| `00:45:20.000 - 00:45:21.510` | and then |
| `00:45:21.520 - 00:45:22.790` | finally we |
| `00:45:22.800 - 00:45:25.030` | release the buffer that contains the |
| `00:45:25.040 - 00:45:28.550` | indirect block okay that's this buffer |
| `00:45:28.560 - 00:45:31.430` | and then we need to free that |
| `00:45:31.440 - 00:45:34.630` | block itself so again here is the |
| `00:45:34.640 - 00:45:37.430` | address or the block number of the |
| `00:45:37.440 - 00:45:38.710` | indirect block |
| `00:45:38.720 - 00:45:41.829` | and we just call b free with that block |
| `00:45:41.839 - 00:45:43.829` | number to free the indirect block |
| `00:45:43.839 - 00:45:45.829` | and we set the final element of the |
| `00:45:45.839 - 00:45:48.550` | array to zero |
| `00:45:48.560 - 00:45:51.030` | then we set the size in the |
| `00:45:51.040 - 00:45:54.550` | inode to zero and we update the cache |
| `00:45:54.560 - 00:45:56.950` | version of the inode by copying it back |
| `00:45:56.960 - 00:45:58.950` | to disk |
| `00:45:58.960 - 00:46:01.349` | okay we've also got this function here |
| `00:46:01.359 - 00:46:02.630` | stat i |
| `00:46:02.640 - 00:46:05.270` | uh which is simply going to copy some |
| `00:46:05.280 - 00:46:06.550` | information |
| `00:46:06.560 - 00:46:07.589` | from |
| `00:46:07.599 - 00:46:11.349` | a cached inode to someplace else okay so |
| `00:46:11.359 - 00:46:13.589` | we've got a stat structure |
| `00:46:13.599 - 00:46:15.190` | and we assume |
| `00:46:15.200 - 00:46:17.270` | that we are holding the lock on the |
| `00:46:17.280 - 00:46:20.550` | cached inode because we are reading the |
| `00:46:20.560 - 00:46:23.510` | fields in that cached inode and we're |
| `00:46:23.520 - 00:46:24.870` | just copying them |
| `00:46:24.880 - 00:46:29.030` | uh into this stat structure |
| `00:46:29.040 - 00:46:30.790` | okay we've just covered about half of |
| `00:46:30.800 - 00:46:33.670` | the functions in the file fs.c and the |
| `00:46:33.680 - 00:46:36.309` | remaining functions will be covered in |
| `00:46:36.319 - 00:46:40.920` | the next video so i'll see you there |
