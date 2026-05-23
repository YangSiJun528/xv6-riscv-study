# xv6 Kernel-18: uart.c and console.c

| Field | Value |
| --- | --- |
| Video ID | `ec8A0u6SjDg` |
| URL | <https://www.youtube.com/watch?v=ec8A0u6SjDg> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.199 - 00:00:02.710` | this video is part of a series on the |
| `00:00:02.720 - 00:00:05.110` | xv6 operating system kernel |
| `00:00:05.120 - 00:00:07.990` | in this video i'll talk about the serial |
| `00:00:08.000 - 00:00:10.629` | input output system and i will cover the |
| `00:00:10.639 - 00:00:15.110` | files uart.c and console.c |
| `00:00:15.120 - 00:00:17.750` | let's begin with an overview of uh the |
| `00:00:17.760 - 00:00:20.550` | system uh |
| `00:00:20.560 - 00:00:21.590` | the |
| `00:00:21.600 - 00:00:23.750` | 16550a |
| `00:00:23.760 - 00:00:25.189` | chip is something that comes from the |
| `00:00:25.199 - 00:00:27.029` | early days of computing |
| `00:00:27.039 - 00:00:29.669` | but the interface that it provides and |
| `00:00:29.679 - 00:00:32.229` | the protocol that it uses is still in |
| `00:00:32.239 - 00:00:36.470` | use today and the risk emulator that xv6 |
| `00:00:36.480 - 00:00:39.830` | runs on uh uses this particular chip so |
| `00:00:39.840 - 00:00:42.069` | the kernel is interfacing to |
| `00:00:42.079 - 00:00:45.750` | something that emulates a 16 550a chip |
| `00:00:45.760 - 00:00:46.950` | and |
| `00:00:46.960 - 00:00:49.029` | this is the hardware in this dark box |
| `00:00:49.039 - 00:00:50.869` | here and it interfaces with the display |
| `00:00:50.879 - 00:00:52.709` | and keyboard somehow |
| `00:00:52.719 - 00:00:53.590` | and |
| `00:00:53.600 - 00:00:56.869` | to send a character to the display |
| `00:00:56.879 - 00:00:59.430` | there's a register and you write the |
| `00:00:59.440 - 00:01:01.349` | kernel code will write to some |
| `00:01:01.359 - 00:01:04.310` | particular location a byte and that will |
| `00:01:04.320 - 00:01:06.310` | ultimately appear on the display and |
| `00:01:06.320 - 00:01:08.630` | likewise to get a character from the |
| `00:01:08.640 - 00:01:09.670` | keyboard |
| `00:01:09.680 - 00:01:11.510` | the kernel will read from some |
| `00:01:11.520 - 00:01:12.870` | particular bite |
| `00:01:12.880 - 00:01:14.630` | and this is what goes on inside the |
| `00:01:14.640 - 00:01:16.149` | kernel |
| `00:01:16.159 - 00:01:17.830` | the chip itself |
| `00:01:17.840 - 00:01:21.109` | may or may not contain some fifo cues to |
| `00:01:21.119 - 00:01:22.870` | make the communication go a little bit |
| `00:01:22.880 - 00:01:24.230` | more smoothly |
| `00:01:24.240 - 00:01:26.710` | but these internal fifo cues can be |
| `00:01:26.720 - 00:01:28.469` | ignored |
| `00:01:28.479 - 00:01:30.149` | the kernel provides |
| `00:01:30.159 - 00:01:31.670` | an output queue |
| `00:01:31.680 - 00:01:34.469` | called the uart transmit buffer |
| `00:01:34.479 - 00:01:37.109` | and an input queue called a console |
| `00:01:37.119 - 00:01:38.069` | buffer |
| `00:01:38.079 - 00:01:40.069` | each of these cues is protected by its |
| `00:01:40.079 - 00:01:41.190` | own lock |
| `00:01:41.200 - 00:01:45.190` | so to do output uh the kernel code will |
| `00:01:45.200 - 00:01:48.630` | call a function uart put c which will |
| `00:01:48.640 - 00:01:51.190` | add a character to the |
| `00:01:51.200 - 00:01:53.350` | output fifoq |
| `00:01:53.360 - 00:01:55.670` | and to read |
| `00:01:55.680 - 00:01:57.749` | data from the |
| `00:01:57.759 - 00:02:00.550` | serial input we call a function console |
| `00:02:00.560 - 00:02:03.429` | read which will get characters from the |
| `00:02:03.439 - 00:02:05.429` | input buffer |
| `00:02:05.439 - 00:02:06.709` | we also |
| `00:02:06.719 - 00:02:09.029` | have some other pathways here |
| `00:02:09.039 - 00:02:10.309` | in particular |
| `00:02:10.319 - 00:02:12.630` | whenever a character is read from the |
| `00:02:12.640 - 00:02:15.110` | input it will automatically be echoed by |
| `00:02:15.120 - 00:02:17.830` | the kernel software and immediately |
| `00:02:17.840 - 00:02:20.710` | displayed and that echoing will bypass |
| `00:02:20.720 - 00:02:22.869` | the output buffer in case the output |
| `00:02:22.879 - 00:02:25.510` | buffer is full or we would you know if |
| `00:02:25.520 - 00:02:27.030` | it were full we would have to grab the |
| `00:02:27.040 - 00:02:28.550` | lock and acquire the lock and that might |
| `00:02:28.560 - 00:02:30.949` | delay the echoing so this basically goes |
| `00:02:30.959 - 00:02:32.869` | to the front of the line shall we say |
| `00:02:32.879 - 00:02:36.309` | and outputs the character immediately |
| `00:02:36.319 - 00:02:38.550` | so the interface to the whole system |
| `00:02:38.560 - 00:02:40.309` | basically is in these three functions |
| `00:02:40.319 - 00:02:41.670` | here |
| `00:02:41.680 - 00:02:43.509` | printf is used by the kernel to print |
| `00:02:43.519 - 00:02:45.750` | error messages and those error messages |
| `00:02:45.760 - 00:02:47.830` | are considered sort of critical and we |
| `00:02:47.840 - 00:02:49.030` | want those to go to the front of the |
| `00:02:49.040 - 00:02:51.910` | line too and be output immediately |
| `00:02:51.920 - 00:02:54.390` | but the console read and console write |
| `00:02:54.400 - 00:02:56.790` | functions are used by the user mode |
| `00:02:56.800 - 00:02:57.910` | programs |
| `00:02:57.920 - 00:03:00.869` | to get data from the input and write |
| `00:03:00.879 - 00:03:03.910` | data to the output and so those will use |
| `00:03:03.920 - 00:03:05.830` | the buffering here |
| `00:03:05.840 - 00:03:08.710` | so let me start by talking about |
| `00:03:08.720 - 00:03:10.790` | the output buffer |
| `00:03:10.800 - 00:03:14.149` | and here is a picture of it um it's |
| `00:03:14.159 - 00:03:16.949` | called the uart transmit buffer and it's |
| `00:03:16.959 - 00:03:18.630` | a fifo buffer |
| `00:03:18.640 - 00:03:21.430` | it has a limited size it's actually 32 |
| `00:03:21.440 - 00:03:23.430` | not eight but you get the idea |
| `00:03:23.440 - 00:03:25.270` | and there are two indexes into this |
| `00:03:25.280 - 00:03:28.149` | buffer a read index and a write index so |
| `00:03:28.159 - 00:03:30.789` | to add to this buffer we store a byte |
| `00:03:30.799 - 00:03:33.910` | there and increment w and to get a |
| `00:03:33.920 - 00:03:36.470` | character out of this buffer we read at |
| `00:03:36.480 - 00:03:39.750` | the r index and then increment r |
| `00:03:39.760 - 00:03:41.030` | and if there's nothing in the buffer |
| `00:03:41.040 - 00:03:44.070` | it's indicated when r and w catch up to |
| `00:03:44.080 - 00:03:45.830` | each other and so when they're equal |
| `00:03:45.840 - 00:03:49.910` | that's an empty buffer situation |
| `00:03:49.920 - 00:03:52.229` | now this is a |
| `00:03:52.239 - 00:03:55.190` | wrap around buffer and so here's the |
| `00:03:55.200 - 00:03:57.030` | code we actually use to |
| `00:03:57.040 - 00:03:59.030` | store something into the buffer |
| `00:03:59.040 - 00:04:00.789` | we use the w |
| `00:04:00.799 - 00:04:03.270` | and we store something in into the |
| `00:04:03.280 - 00:04:05.270` | buffer using that array but and then we |
| `00:04:05.280 - 00:04:07.910` | increment after that but we do it modulo |
| `00:04:07.920 - 00:04:10.070` | the size of the buffer and likewise to |
| `00:04:10.080 - 00:04:11.990` | read something we |
| `00:04:12.000 - 00:04:14.470` | use the r index incrementing it after |
| `00:04:14.480 - 00:04:16.629` | we've fetched the thing but we take the |
| `00:04:16.639 - 00:04:18.870` | index modulo the size |
| `00:04:18.880 - 00:04:22.950` | so r and w just keep increasing okay |
| `00:04:22.960 - 00:04:24.790` | and so we could have a situation like |
| `00:04:24.800 - 00:04:27.350` | this our buffer here is eight characters |
| `00:04:27.360 - 00:04:29.830` | long and it stores a b c |
| `00:04:29.840 - 00:04:31.189` | d e f |
| `00:04:31.199 - 00:04:34.070` | and so w uh is continuing to advance |
| `00:04:34.080 - 00:04:36.870` | here but whenever we use w we're doing |
| `00:04:36.880 - 00:04:39.590` | it mod modulo the size of the buffer so |
| `00:04:39.600 - 00:04:41.510` | we're really effectively looking at w |
| `00:04:41.520 - 00:04:42.710` | right here |
| `00:04:42.720 - 00:04:46.070` | and likewise with the read index |
| `00:04:46.080 - 00:04:49.590` | and using this technique where w and r |
| `00:04:49.600 - 00:04:51.510` | just keep incrementing |
| `00:04:51.520 - 00:04:53.590` | is helpful because we can distinguish a |
| `00:04:53.600 - 00:04:55.270` | full buffer situation from an empty |
| `00:04:55.280 - 00:04:56.950` | buffer situation |
| `00:04:56.960 - 00:04:58.790` | so here's what a full buffer situation |
| `00:04:58.800 - 00:04:59.909` | looks like |
| `00:04:59.919 - 00:05:04.390` | uh when w exceeds r by exactly the size |
| `00:05:04.400 - 00:05:06.469` | of the buffer then we have a full |
| `00:05:06.479 - 00:05:09.350` | condition and here the buffer is full of |
| `00:05:09.360 - 00:05:11.830` | a b c d e f g h |
| `00:05:11.840 - 00:05:15.270` | okay and if we take w modulo uh the size |
| `00:05:15.280 - 00:05:16.950` | of the buffer it would come back to five |
| `00:05:16.960 - 00:05:19.909` | but uh here we can check for a full |
| `00:05:19.919 - 00:05:23.510` | buffer and uh you can ask yourself what |
| `00:05:23.520 - 00:05:26.150` | would happen when these uh indexes r and |
| `00:05:26.160 - 00:05:27.029` | w |
| `00:05:27.039 - 00:05:29.510` | uh ultimately wrap around you know after |
| `00:05:29.520 - 00:05:31.350` | lots of input and output they're gonna |
| `00:05:31.360 - 00:05:35.189` | wrap around and you can um think about |
| `00:05:35.199 - 00:05:35.909` | when |
| `00:05:35.919 - 00:05:38.150` | under what situations this might be uh |
| `00:05:38.160 - 00:05:40.710` | safe and might not cause a bug |
| `00:05:40.720 - 00:05:42.469` | but uh one other thing we need to think |
| `00:05:42.479 - 00:05:44.870` | about is the fact that this buffer will |
| `00:05:44.880 - 00:05:47.430` | be accessed by multiple processes and |
| `00:05:47.440 - 00:05:48.790` | multiple cores |
| `00:05:48.800 - 00:05:51.029` | so it needs to be protected by a lock |
| `00:05:51.039 - 00:05:52.950` | and it is the name of the lock is the |
| `00:05:52.960 - 00:05:55.029` | uart transmit lock |
| `00:05:55.039 - 00:05:59.029` | and uh now let's take a look at the |
| `00:05:59.039 - 00:06:01.270` | reed |
| `00:06:01.280 - 00:06:04.950` | buffer it's a little bit different |
| `00:06:04.960 - 00:06:07.590` | we have three indexes into this buffer |
| `00:06:07.600 - 00:06:11.430` | so here's the buffer some some array and |
| `00:06:11.440 - 00:06:12.150` | uh |
| `00:06:12.160 - 00:06:13.029` | called |
| `00:06:13.039 - 00:06:15.909` | console buffer and we've got indexes for |
| `00:06:15.919 - 00:06:19.670` | read write and the end i guess |
| `00:06:19.680 - 00:06:21.830` | as well as a lock that uh |
| `00:06:21.840 - 00:06:24.390` | guards this particular buffer and again |
| `00:06:24.400 - 00:06:27.029` | it's a fifo buffer so we're reading when |
| `00:06:27.039 - 00:06:29.350` | we take stuff out of the buffer from the |
| `00:06:29.360 - 00:06:31.189` | index r |
| `00:06:31.199 - 00:06:32.870` | uh however when we add stuff we're |
| `00:06:32.880 - 00:06:37.670` | adding it at the e index so we add here |
| `00:06:37.680 - 00:06:39.749` | but we also have the capability when |
| `00:06:39.759 - 00:06:42.629` | we're typing input to hit a control u |
| `00:06:42.639 - 00:06:44.790` | and that will erase everything that's |
| `00:06:44.800 - 00:06:46.469` | the last line of input that's in the |
| `00:06:46.479 - 00:06:48.070` | buffer |
| `00:06:48.080 - 00:06:50.469` | so that's why we have this uh third |
| `00:06:50.479 - 00:06:52.070` | index so |
| `00:06:52.080 - 00:06:55.189` | here we've typed in w-o-r-l-d |
| `00:06:55.199 - 00:06:57.430` | but we haven't hit the new line yet so |
| `00:06:57.440 - 00:07:00.070` | if we hit a control u at this point what |
| `00:07:00.080 - 00:07:02.150` | is going to happen is that e will be |
| `00:07:02.160 - 00:07:05.749` | backed up okay to this point here |
| `00:07:05.759 - 00:07:07.749` | if we get a control sorry if we get a |
| `00:07:07.759 - 00:07:10.950` | new line we'll instead of ctrl u we'll |
| `00:07:10.960 - 00:07:13.110` | store it here and |
| `00:07:13.120 - 00:07:16.390` | we'll advance w to this point and we'll |
| `00:07:16.400 - 00:07:19.510` | advance both w and e to this point here |
| `00:07:19.520 - 00:07:22.150` | and so we will have in the buffer |
| `00:07:22.160 - 00:07:24.230` | between the read and index and the right |
| `00:07:24.240 - 00:07:27.350` | index a number of lines each one being |
| `00:07:27.360 - 00:07:30.070` | terminated by a newline character when |
| `00:07:30.080 - 00:07:32.150` | we read from the buffer we'll read a |
| `00:07:32.160 - 00:07:34.230` | whole line and then stop |
| `00:07:34.240 - 00:07:35.749` | okay so |
| `00:07:35.759 - 00:07:39.029` | that's how the three indexes are used |
| `00:07:39.039 - 00:07:39.749` | so |
| `00:07:39.759 - 00:07:42.830` | now let's look a little bit at the um |
| `00:07:42.840 - 00:07:45.430` | the hardware |
| `00:07:45.440 - 00:07:46.390` | um |
| `00:07:46.400 - 00:07:49.350` | here i mentioned that the hardware uh |
| `00:07:49.360 - 00:07:51.909` | contains a couple of fifo cues that are |
| `00:07:51.919 - 00:07:53.270` | essentially hidden and we won't worry |
| `00:07:53.280 - 00:07:54.790` | too much about these |
| `00:07:54.800 - 00:07:57.589` | and to write a character we we ride a |
| `00:07:57.599 - 00:07:59.589` | bike to some location |
| `00:07:59.599 - 00:08:03.510` | and to read from the device we read a |
| `00:08:03.520 - 00:08:05.350` | byte from some location |
| `00:08:05.360 - 00:08:07.510` | well those locations those are memory |
| `00:08:07.520 - 00:08:11.110` | mapped locations and the way it works is |
| `00:08:11.120 - 00:08:12.550` | we have |
| `00:08:12.560 - 00:08:15.350` | this device this uart device being |
| `00:08:15.360 - 00:08:17.749` | memory mapped into some location some |
| `00:08:17.759 - 00:08:18.869` | address |
| `00:08:18.879 - 00:08:22.309` | in the physical memory address space and |
| `00:08:22.319 - 00:08:24.869` | in particular the address is |
| `00:08:24.879 - 00:08:28.070` | indicated by this constant uart 0 |
| `00:08:28.080 - 00:08:29.909` | and it happens to be equated to some |
| `00:08:29.919 - 00:08:31.830` | particular address |
| `00:08:31.840 - 00:08:33.990` | and at that address there are several |
| `00:08:34.000 - 00:08:35.029` | bytes |
| `00:08:35.039 - 00:08:37.269` | and these bytes are considered to be |
| `00:08:37.279 - 00:08:39.750` | i o registers if you will |
| `00:08:39.760 - 00:08:41.589` | and the key register is the one that's |
| `00:08:41.599 - 00:08:44.550` | offset zero and if we store into that |
| `00:08:44.560 - 00:08:47.430` | particular location that's the transmit |
| `00:08:47.440 - 00:08:49.030` | hold register |
| `00:08:49.040 - 00:08:51.990` | we will be sending a byte to the output |
| `00:08:52.000 - 00:08:55.910` | and if we read from that same location |
| `00:08:55.920 - 00:08:58.150` | here i'm saying store into this location |
| `00:08:58.160 - 00:09:00.790` | at offset zero and here i'm saying |
| `00:09:00.800 - 00:09:03.670` | load from the location at offset zero |
| `00:09:03.680 - 00:09:05.750` | that register has a different name the |
| `00:09:05.760 - 00:09:06.870` | read |
| `00:09:06.880 - 00:09:09.670` | register read hold register and we are |
| `00:09:09.680 - 00:09:13.030` | getting a byte from the keyboard |
| `00:09:13.040 - 00:09:15.190` | so those two are our main registers |
| `00:09:15.200 - 00:09:17.750` | there are some other registers that we |
| `00:09:17.760 - 00:09:20.630` | uh need when we set things up |
| `00:09:20.640 - 00:09:22.949` | and in particular uh |
| `00:09:22.959 - 00:09:24.870` | we want to enable interrupts so we have |
| `00:09:24.880 - 00:09:26.949` | to write some special stuff to this |
| `00:09:26.959 - 00:09:29.350` | register and if we want to use the |
| `00:09:29.360 - 00:09:31.750` | on-chip fifos we need to write some |
| `00:09:31.760 - 00:09:33.430` | stuff to that register |
| `00:09:33.440 - 00:09:36.230` | and we the line control register is used |
| `00:09:36.240 - 00:09:38.230` | for setting the baud rate that's the |
| `00:09:38.240 - 00:09:40.389` | transmission rate |
| `00:09:40.399 - 00:09:41.190` | up |
| `00:09:41.200 - 00:09:43.030` | which would be relevant for real |
| `00:09:43.040 - 00:09:44.550` | hardware |
| `00:09:44.560 - 00:09:47.350` | um and we also have another important |
| `00:09:47.360 - 00:09:49.990` | one is the line status register this has |
| `00:09:50.000 - 00:09:52.550` | two bits well that are important to us |
| `00:09:52.560 - 00:09:54.550` | one bit tells us whether there is any |
| `00:09:54.560 - 00:09:58.389` | input in the read hold register so |
| `00:09:58.399 - 00:10:00.550` | we can read this register all we want |
| `00:10:00.560 - 00:10:02.230` | but is are we getting new data or are we |
| `00:10:02.240 - 00:10:04.069` | getting the previous character over and |
| `00:10:04.079 - 00:10:06.630` | over so we might want to check this bit |
| `00:10:06.640 - 00:10:09.030` | here and likewise that |
| `00:10:09.040 - 00:10:11.030` | the the other bit tells us whether the |
| `00:10:11.040 - 00:10:13.590` | transmit hold register is ready to |
| `00:10:13.600 - 00:10:16.630` | receive the next output byte so if we |
| `00:10:16.640 - 00:10:17.509` | write |
| `00:10:17.519 - 00:10:20.310` | to this register prematurely that by |
| `00:10:20.320 - 00:10:21.990` | might not actually make it to the output |
| `00:10:22.000 - 00:10:25.350` | it might might get lost |
| `00:10:25.360 - 00:10:27.269` | next let's look at the functions that |
| `00:10:27.279 - 00:10:29.509` | are in the uart c file and there are |
| `00:10:29.519 - 00:10:31.829` | five of them that i want to look at |
| `00:10:31.839 - 00:10:35.190` | we'll start with uart put c sync and |
| `00:10:35.200 - 00:10:36.949` | this is used to send a character to the |
| `00:10:36.959 - 00:10:40.710` | hardware output as soon as possible and |
| `00:10:40.720 - 00:10:42.870` | it's used by printf |
| `00:10:42.880 - 00:10:44.949` | to print error messages we want to print |
| `00:10:44.959 - 00:10:46.550` | them to the display as quickly as |
| `00:10:46.560 - 00:10:49.509` | possible bypassing the output queue |
| `00:10:49.519 - 00:10:51.430` | and for echoing |
| `00:10:51.440 - 00:10:53.350` | the input characters again we want to |
| `00:10:53.360 - 00:10:56.550` | bypass the fifo queue here |
| `00:10:56.560 - 00:10:59.430` | next we have uart put c |
| `00:10:59.440 - 00:11:02.069` | this will be called by console.write and |
| `00:11:02.079 - 00:11:05.670` | it will add a character to the output q |
| `00:11:05.680 - 00:11:06.870` | and |
| `00:11:06.880 - 00:11:08.310` | in order to do that it will first have |
| `00:11:08.320 - 00:11:10.389` | to acquire the lock and it will have to |
| `00:11:10.399 - 00:11:12.630` | test for the condition |
| `00:11:12.640 - 00:11:15.110` | of the buffer having some extra space in |
| `00:11:15.120 - 00:11:18.150` | it so it may wait |
| `00:11:18.160 - 00:11:20.069` | but after it does that after it puts a |
| `00:11:20.079 - 00:11:21.990` | character in it will call |
| `00:11:22.000 - 00:11:25.030` | you art start and what uart start does |
| `00:11:25.040 - 00:11:27.509` | is handle this link here |
| `00:11:27.519 - 00:11:29.590` | it sends the next character to the |
| `00:11:29.600 - 00:11:30.870` | hardware |
| `00:11:30.880 - 00:11:32.790` | if there is a character in the buffer |
| `00:11:32.800 - 00:11:35.670` | and if the hardware is ready to receive |
| `00:11:35.680 - 00:11:37.670` | another character |
| `00:11:37.680 - 00:11:40.470` | on the input side we have uart get c |
| `00:11:40.480 - 00:11:42.949` | which is a little function that will |
| `00:11:42.959 - 00:11:47.670` | read the next byte from the hardware |
| `00:11:47.680 - 00:11:50.870` | if it is ready and just return it it |
| `00:11:50.880 - 00:11:52.710` | will not wait |
| `00:11:52.720 - 00:11:55.350` | instead if there is no data ready from |
| `00:11:55.360 - 00:11:57.430` | the keyboard it will just return a minus |
| `00:11:57.440 - 00:11:59.110` | one code |
| `00:11:59.120 - 00:12:00.550` | and |
| `00:12:00.560 - 00:12:02.949` | finally we have this uart interrupt |
| `00:12:02.959 - 00:12:04.230` | function |
| `00:12:04.240 - 00:12:07.110` | whenever an input character is available |
| `00:12:07.120 - 00:12:10.790` | to be fetched from the hardware or |
| `00:12:10.800 - 00:12:12.870` | whenever the hardware is ready to |
| `00:12:12.880 - 00:12:14.790` | receive the next output character the |
| `00:12:14.800 - 00:12:17.110` | hardware will generate an interrupt and |
| `00:12:17.120 - 00:12:19.750` | that interrupt will then invoke the uart |
| `00:12:19.760 - 00:12:22.069` | interrupt function so it's called when |
| `00:12:22.079 - 00:12:24.629` | the hardware interrupts and what this |
| `00:12:24.639 - 00:12:27.190` | function will do is it will retrieve as |
| `00:12:27.200 - 00:12:29.590` | many input characters from the hardware |
| `00:12:29.600 - 00:12:32.230` | as are available and for each one of |
| `00:12:32.240 - 00:12:34.949` | these it will call console interrupt |
| `00:12:34.959 - 00:12:36.870` | which is another function function |
| `00:12:36.880 - 00:12:39.269` | passing it one character for each call |
| `00:12:39.279 - 00:12:42.150` | and it will use that function to add |
| `00:12:42.160 - 00:12:45.509` | those characters to the buffer |
| `00:12:45.519 - 00:12:48.710` | and it will also call uart start |
| `00:12:48.720 - 00:12:53.110` | to send the next output character from |
| `00:12:53.120 - 00:12:56.069` | the output buffer to the hardware |
| `00:12:56.079 - 00:12:57.990` | so every time |
| `00:12:58.000 - 00:12:59.030` | the |
| `00:12:59.040 - 00:13:01.110` | output is able to display a character |
| `00:13:01.120 - 00:13:02.710` | and is ready for the next character an |
| `00:13:02.720 - 00:13:04.069` | interrupt will occur |
| `00:13:04.079 - 00:13:06.710` | and the uart interrupt function will |
| `00:13:06.720 - 00:13:08.470` | move the next character from the fifo |
| `00:13:08.480 - 00:13:11.350` | queue into the hardware |
| `00:13:11.360 - 00:13:14.230` | okay next let's move on to the functions |
| `00:13:14.240 - 00:13:15.110` | in |
| `00:13:15.120 - 00:13:17.350` | console.c |
| `00:13:17.360 - 00:13:18.949` | so the functions we want to talk about |
| `00:13:18.959 - 00:13:20.069` | are these |
| `00:13:20.079 - 00:13:23.750` | four functions here |
| `00:13:23.760 - 00:13:26.550` | the console.write function |
| `00:13:26.560 - 00:13:27.350` | will |
| `00:13:27.360 - 00:13:29.110` | be used to |
| `00:13:29.120 - 00:13:30.150` | call |
| `00:13:30.160 - 00:13:32.069` | uart put c |
| `00:13:32.079 - 00:13:33.590` | character by character and add |
| `00:13:33.600 - 00:13:36.710` | characters to the output buffer so |
| `00:13:36.720 - 00:13:38.470` | that's what happens |
| `00:13:38.480 - 00:13:39.750` | and since |
| `00:13:39.760 - 00:13:41.990` | you are put c may sleep this function |
| `00:13:42.000 - 00:13:44.629` | may sleep as well |
| `00:13:44.639 - 00:13:46.069` | console read |
| `00:13:46.079 - 00:13:47.189` | will |
| `00:13:47.199 - 00:13:49.829` | wait for input to be ready in the |
| `00:13:49.839 - 00:13:51.269` | console input |
| `00:13:51.279 - 00:13:54.150` | in particular it uh waits until the |
| `00:13:54.160 - 00:13:55.030` | right |
| `00:13:55.040 - 00:13:56.870` | index has been moved |
| `00:13:56.880 - 00:13:59.590` | to some place beyond the next new line |
| `00:13:59.600 - 00:14:00.470` | or |
| `00:14:00.480 - 00:14:03.269` | control d for into file and so that |
| `00:14:03.279 - 00:14:05.590` | means there's some input ready to be |
| `00:14:05.600 - 00:14:09.829` | retrieved and what it will do is it will |
| `00:14:09.839 - 00:14:12.470` | copy characters from the input |
| `00:14:12.480 - 00:14:13.910` | buffer |
| `00:14:13.920 - 00:14:15.910` | and move them into the user space |
| `00:14:15.920 - 00:14:19.110` | somewhere until it encounters |
| `00:14:19.120 - 00:14:21.990` | a new line character or the into file |
| `00:14:22.000 - 00:14:24.389` | control d character |
| `00:14:24.399 - 00:14:25.189` | and |
| `00:14:25.199 - 00:14:27.269` | as i mentioned uh in the |
| `00:14:27.279 - 00:14:28.550` | uart |
| `00:14:28.560 - 00:14:31.350` | interrupt function uh we call |
| `00:14:31.360 - 00:14:34.069` | this function console interrupt for each |
| `00:14:34.079 - 00:14:36.470` | input character and what a console |
| `00:14:36.480 - 00:14:39.189` | interrupt is going to do it is add a |
| `00:14:39.199 - 00:14:40.870` | character to |
| `00:14:40.880 - 00:14:44.150` | the input buffer so it's going to |
| `00:14:44.160 - 00:14:46.629` | add the character here and advance e |
| `00:14:46.639 - 00:14:49.030` | it's also going to process uh characters |
| `00:14:49.040 - 00:14:52.230` | like backspace and control u as well as |
| `00:14:52.240 - 00:14:55.990` | echoing the character to the output |
| `00:14:56.000 - 00:14:58.470` | if it hits a |
| `00:14:58.480 - 00:15:01.750` | new line or the into file character then |
| `00:15:01.760 - 00:15:03.910` | it's going to say okay we've got enough |
| `00:15:03.920 - 00:15:07.110` | so that we can enable the uh input |
| `00:15:07.120 - 00:15:08.949` | reader to get something and so it's |
| `00:15:08.959 - 00:15:10.790` | going to advance w |
| `00:15:10.800 - 00:15:14.230` | to e and then it's also going to wake up |
| `00:15:14.240 - 00:15:16.230` | any sleeping functions that are waiting |
| `00:15:16.240 - 00:15:18.230` | for input so it's it might |
| `00:15:18.240 - 00:15:20.790` | wake up a console read function right |
| `00:15:20.800 - 00:15:23.750` | the console read function sleeps until |
| `00:15:23.760 - 00:15:26.629` | there is something available that is r |
| `00:15:26.639 - 00:15:27.590` | and w |
| `00:15:27.600 - 00:15:29.430` | are not the same |
| `00:15:29.440 - 00:15:30.150` | and |
| `00:15:30.160 - 00:15:30.949` | so |
| `00:15:30.959 - 00:15:32.790` | a console read function |
| `00:15:32.800 - 00:15:36.550` | may be sleeping so once we read all the |
| `00:15:36.560 - 00:15:39.189` | way up to a new line or the end of file |
| `00:15:39.199 - 00:15:41.269` | we call wake up |
| `00:15:41.279 - 00:15:43.509` | to wake up any anybody that's |
| `00:15:43.519 - 00:15:45.110` | to wake up all the functions that are |
| `00:15:45.120 - 00:15:46.949` | waiting |
| `00:15:46.959 - 00:15:49.430` | and finally we've got a function to |
| `00:15:49.440 - 00:15:51.350` | initialize things |
| `00:15:51.360 - 00:15:53.030` | okay if uh you just want an idea of |
| `00:15:53.040 - 00:15:55.189` | what's going on you can probably stop |
| `00:15:55.199 - 00:15:56.150` | here |
| `00:15:56.160 - 00:15:58.790` | next i'm going to look at the code |
| `00:15:58.800 - 00:16:00.870` | in more detail |
| `00:16:00.880 - 00:16:02.550` | okay now that i've introduced the main |
| `00:16:02.560 - 00:16:04.310` | organization of these two files i'm |
| `00:16:04.320 - 00:16:05.990` | going to go over the code that's in the |
| `00:16:06.000 - 00:16:09.269` | uart.c and the console.c files |
| `00:16:09.279 - 00:16:11.670` | let's start with uart.c |
| `00:16:11.680 - 00:16:13.910` | and we see the |
| `00:16:13.920 - 00:16:16.550` | offsets for the registers remember in |
| `00:16:16.560 - 00:16:19.509` | this diagram here i showed the offsets |
| `00:16:19.519 - 00:16:22.230` | for the register such as lsrs at offset |
| `00:16:22.240 - 00:16:24.470` | 5 and we see |
| `00:16:24.480 - 00:16:25.670` | lsr |
| `00:16:25.680 - 00:16:28.710` | at offset 5. here we see the transmit |
| `00:16:28.720 - 00:16:31.990` | hold register and receive hold register |
| `00:16:32.000 - 00:16:33.910` | and we have a little macro function here |
| `00:16:33.920 - 00:16:37.110` | that is used to add the base address of |
| `00:16:37.120 - 00:16:38.150` | the |
| `00:16:38.160 - 00:16:41.670` | uart memory map device to the offset |
| `00:16:41.680 - 00:16:43.990` | and we also have a couple functions here |
| `00:16:44.000 - 00:16:45.269` | one is to |
| `00:16:45.279 - 00:16:48.230` | go out and retrieve the value from this |
| `00:16:48.240 - 00:16:50.870` | memory mapped register and a second |
| `00:16:50.880 - 00:16:51.990` | function |
| `00:16:52.000 - 00:16:55.030` | write register which uh writes value v |
| `00:16:55.040 - 00:16:57.829` | into memory mapped register |
| `00:16:57.839 - 00:17:01.430` | i had the uh transmit buffer okay that's |
| `00:17:01.440 - 00:17:03.430` | this buffer here with its read and write |
| `00:17:03.440 - 00:17:04.949` | indices |
| `00:17:04.959 - 00:17:07.669` | and that's defined right here the uart |
| `00:17:07.679 - 00:17:10.630` | transmit buffer and the size is |
| `00:17:10.640 - 00:17:12.470` | 32 here |
| `00:17:12.480 - 00:17:15.029` | here is the read and the right here are |
| `00:17:15.039 - 00:17:17.909` | the read and write indices and here is |
| `00:17:17.919 - 00:17:20.630` | the lock that protects this buffer |
| `00:17:20.640 - 00:17:21.669` | we've also |
| `00:17:21.679 - 00:17:23.029` | going to be referencing a variable |
| `00:17:23.039 - 00:17:25.590` | called panicked and we'll see that in a |
| `00:17:25.600 - 00:17:27.590` | second but let's first move to the |
| `00:17:27.600 - 00:17:29.830` | second page which is |
| `00:17:29.840 - 00:17:30.710` | the |
| `00:17:30.720 - 00:17:33.190` | initial initialize function |
| `00:17:33.200 - 00:17:34.549` | so |
| `00:17:34.559 - 00:17:36.710` | here we are writing to what's called the |
| `00:17:36.720 - 00:17:39.110` | interrupt enable register to disable |
| `00:17:39.120 - 00:17:41.510` | interrupts from this device so it won't |
| `00:17:41.520 - 00:17:43.110` | be interrupting at least for the |
| `00:17:43.120 - 00:17:44.870` | duration of this code |
| `00:17:44.880 - 00:17:47.909` | and uh then we are doing something with |
| `00:17:47.919 - 00:17:51.190` | these four writes that are setting the |
| `00:17:51.200 - 00:17:54.950` | baud rate to 38.4 k |
| `00:17:54.960 - 00:17:57.830` | and um so we do that that gets this |
| `00:17:57.840 - 00:18:01.270` | device initialized properly and we also |
| `00:18:01.280 - 00:18:06.390` | uh reset the internal fifos |
| `00:18:06.400 - 00:18:09.029` | in this device in this picture here |
| `00:18:09.039 - 00:18:09.830` | i |
| `00:18:09.840 - 00:18:11.510` | indicated that the device might have |
| `00:18:11.520 - 00:18:12.549` | some |
| `00:18:12.559 - 00:18:15.350` | internal uh buffering going on and so |
| `00:18:15.360 - 00:18:19.270` | here we are uh initializing and uh |
| `00:18:19.280 - 00:18:23.029` | enabling that uh internal buffering |
| `00:18:23.039 - 00:18:25.510` | and finally we are writing to the |
| `00:18:25.520 - 00:18:30.710` | interrupt enable register to cause it to |
| `00:18:30.720 - 00:18:32.390` | interrupt whenever we get |
| `00:18:32.400 - 00:18:34.070` | a |
| `00:18:34.080 - 00:18:36.549` | character received or whenever the |
| `00:18:36.559 - 00:18:38.390` | transmit channel is ready to receive |
| `00:18:38.400 - 00:18:40.390` | another output character |
| `00:18:40.400 - 00:18:43.430` | and we are also initializing the lock |
| `00:18:43.440 - 00:18:46.310` | all right moving on to the next page |
| `00:18:46.320 - 00:18:48.870` | we have um |
| `00:18:48.880 - 00:18:50.630` | actually i think i want to do this one |
| `00:18:50.640 - 00:18:52.310` | first the |
| `00:18:52.320 - 00:18:53.990` | uart put c |
| `00:18:54.000 - 00:18:55.430` | sync |
| `00:18:55.440 - 00:18:58.310` | because i think that's simpler |
| `00:18:58.320 - 00:18:59.909` | you can see that what it's doing it's |
| `00:18:59.919 - 00:19:02.070` | got a while loop here and it's just |
| `00:19:02.080 - 00:19:05.029` | reading the line status register and |
| `00:19:05.039 - 00:19:08.549` | checking the bit to see whether it's set |
| `00:19:08.559 - 00:19:10.950` | and as long as it's clear it it does a |
| `00:19:10.960 - 00:19:13.669` | busy loop and the minute this bit is set |
| `00:19:13.679 - 00:19:16.150` | that is the hardware's way of saying |
| `00:19:16.160 - 00:19:17.909` | it's ready to receive another byte to |
| `00:19:17.919 - 00:19:21.830` | transmit and so at that point we write |
| `00:19:21.840 - 00:19:24.789` | the character that it's passed into the |
| `00:19:24.799 - 00:19:27.350` | output register we also see a couple of |
| `00:19:27.360 - 00:19:29.270` | other things we see it uh disabling |
| `00:19:29.280 - 00:19:31.430` | interrupts and re-enabling interrupts |
| `00:19:31.440 - 00:19:33.990` | and we also see this code here |
| `00:19:34.000 - 00:19:36.789` | panicked is a global variable that gets |
| `00:19:36.799 - 00:19:38.549` | set to true |
| `00:19:38.559 - 00:19:41.830` | after any hardware error or sorry any |
| `00:19:41.840 - 00:19:43.909` | kernel error so whenever we call the |
| `00:19:43.919 - 00:19:45.830` | panic function after printing the |
| `00:19:45.840 - 00:19:48.789` | message we set the panicked flag |
| `00:19:48.799 - 00:19:50.950` | and what this does is frees up all |
| `00:19:50.960 - 00:19:53.510` | future output so if that flag has |
| `00:19:53.520 - 00:19:55.830` | already been set from a previous error |
| `00:19:55.840 - 00:19:58.070` | we have an infinite loop here this core |
| `00:19:58.080 - 00:19:59.909` | when it hits this it's disabled the |
| `00:19:59.919 - 00:20:01.590` | interrupts it's just going to go into an |
| `00:20:01.600 - 00:20:04.310` | infinite loop and lock up this core and |
| `00:20:04.320 - 00:20:07.510` | the purpose of that is to |
| `00:20:07.520 - 00:20:08.870` | once you get an error message you don't |
| `00:20:08.880 - 00:20:10.310` | want to see anything else you don't see |
| `00:20:10.320 - 00:20:11.990` | any other output |
| `00:20:12.000 - 00:20:13.510` | if the thing is looping or something you |
| `00:20:13.520 - 00:20:15.430` | don't want to see a bunch of output you |
| `00:20:15.440 - 00:20:16.870` | want to freeze up the other cores and |
| `00:20:16.880 - 00:20:19.350` | prevent them from doing any output so |
| `00:20:19.360 - 00:20:21.669` | now let's take a look at |
| `00:20:21.679 - 00:20:24.390` | user r uart put c |
| `00:20:24.400 - 00:20:25.350` | and |
| `00:20:25.360 - 00:20:28.470` | that is the function here i described it |
| `00:20:28.480 - 00:20:29.190` | with this uh |
| `00:20:29.200 - 00:20:30.390` | [Music] |
| `00:20:30.400 - 00:20:32.789` | picture here that's going to grab the |
| `00:20:32.799 - 00:20:37.029` | lock on the output buffer and add |
| `00:20:37.039 - 00:20:38.630` | a byte to that |
| `00:20:38.640 - 00:20:40.070` | so |
| `00:20:40.080 - 00:20:42.070` | we see that we are acquiring the lock |
| `00:20:42.080 - 00:20:44.549` | first and we'll release it down below |
| `00:20:44.559 - 00:20:46.870` | and again we have this panicked stuff if |
| `00:20:46.880 - 00:20:48.870` | if we've had an error message already |
| `00:20:48.880 - 00:20:50.630` | this will just basically freeze up the |
| `00:20:50.640 - 00:20:51.590` | core |
| `00:20:51.600 - 00:20:54.549` | but assuming we haven't had that then |
| `00:20:54.559 - 00:20:57.669` | what we've got here is a while loop and |
| `00:20:57.679 - 00:20:59.590` | we check right here |
| `00:20:59.600 - 00:21:00.549` | and |
| `00:21:00.559 - 00:21:02.870` | this is to check to see whether the |
| `00:21:02.880 - 00:21:04.630` | buffer is full |
| `00:21:04.640 - 00:21:05.350` | uh |
| `00:21:05.360 - 00:21:07.270` | remember in my |
| `00:21:07.280 - 00:21:09.590` | diagram here i said that if the |
| `00:21:09.600 - 00:21:11.909` | difference between r and w is the size |
| `00:21:11.919 - 00:21:13.830` | of the buffer then we have a full |
| `00:21:13.840 - 00:21:15.510` | condition and that's what's being tested |
| `00:21:15.520 - 00:21:16.789` | here |
| `00:21:16.799 - 00:21:18.549` | if read plus the size of the buffer is |
| `00:21:18.559 - 00:21:19.909` | equal to the right then the buffer is |
| `00:21:19.919 - 00:21:22.950` | full and we don't have an ability to add |
| `00:21:22.960 - 00:21:25.430` | another character to the buffer we don't |
| `00:21:25.440 - 00:21:27.110` | want to lose that character we want to |
| `00:21:27.120 - 00:21:30.070` | wait so here we go to sleep and the |
| `00:21:30.080 - 00:21:32.070` | thing that we're using for the channel |
| `00:21:32.080 - 00:21:33.590` | to sleep on |
| `00:21:33.600 - 00:21:36.230` | is the address of the read index |
| `00:21:36.240 - 00:21:38.310` | variable so this is just a |
| `00:21:38.320 - 00:21:40.870` | number that we use to coordinate the |
| `00:21:40.880 - 00:21:44.549` | sleep here with the wake up that happens |
| `00:21:44.559 - 00:21:45.510` | in the |
| `00:21:45.520 - 00:21:48.149` | uh uart start function |
| `00:21:48.159 - 00:21:51.190` | uh so uh that's the channel right there |
| `00:21:51.200 - 00:21:53.430` | and we're passing at the lock as we do |
| `00:21:53.440 - 00:21:55.510` | with uh the sleep function |
| `00:21:55.520 - 00:21:58.549` | and once we are able to uh |
| `00:21:58.559 - 00:22:00.710` | once the buffer is not full we come down |
| `00:22:00.720 - 00:22:03.669` | and execute this code here |
| `00:22:03.679 - 00:22:05.830` | which will um |
| `00:22:05.840 - 00:22:07.590` | add |
| `00:22:07.600 - 00:22:09.029` | the character c |
| `00:22:09.039 - 00:22:09.830` | to |
| `00:22:09.840 - 00:22:11.510` | the transmit buffer |
| `00:22:11.520 - 00:22:13.590` | at location w |
| `00:22:13.600 - 00:22:16.470` | okay and it will do it mod the buffer |
| `00:22:16.480 - 00:22:19.029` | size and then increment |
| `00:22:19.039 - 00:22:20.149` | w |
| `00:22:20.159 - 00:22:24.070` | and we also call uart start in case the |
| `00:22:24.080 - 00:22:25.669` | hardware is ready to receive the next |
| `00:22:25.679 - 00:22:27.350` | output byte we want to get it going on |
| `00:22:27.360 - 00:22:30.390` | that so we call your start here |
| `00:22:30.400 - 00:22:32.710` | and then we release the lock and exit |
| `00:22:32.720 - 00:22:34.470` | the loop and return |
| `00:22:34.480 - 00:22:37.430` | so next let's look at um |
| `00:22:37.440 - 00:22:39.350` | uart start |
| `00:22:39.360 - 00:22:45.350` | and that is here |
| `00:22:45.360 - 00:22:47.830` | so we have a while loop here |
| `00:22:47.840 - 00:22:50.230` | and what we're doing |
| `00:22:50.240 - 00:22:51.350` | is |
| `00:22:51.360 - 00:22:53.270` | checking to see whether |
| `00:22:53.280 - 00:22:56.070` | the buffer is empty okay if the buffer |
| `00:22:56.080 - 00:22:57.430` | is empty there's no |
| `00:22:57.440 - 00:23:00.149` | nothing to be sent to the output so if |
| `00:23:00.159 - 00:23:03.110` | the read and write index are equal then |
| `00:23:03.120 - 00:23:06.870` | we immediately just give up and return |
| `00:23:06.880 - 00:23:08.789` | but assuming there is something in the |
| `00:23:08.799 - 00:23:11.350` | buffer we need to keep going so here we |
| `00:23:11.360 - 00:23:13.990` | read the status register the line status |
| `00:23:14.000 - 00:23:16.390` | register and we ask whether |
| `00:23:16.400 - 00:23:19.430` | the bit that indicates that the hardware |
| `00:23:19.440 - 00:23:21.909` | is ready to receive a byte is is clear |
| `00:23:21.919 - 00:23:25.110` | or not and so if the hardware is ready |
| `00:23:25.120 - 00:23:27.029` | to receive the next byte |
| `00:23:27.039 - 00:23:29.350` | um then we can proceed but if it's not |
| `00:23:29.360 - 00:23:32.630` | we just return immediately if it's not |
| `00:23:32.640 - 00:23:34.710` | ready then they will interrupt us later |
| `00:23:34.720 - 00:23:37.430` | when it is ready to receive a byte but |
| `00:23:37.440 - 00:23:39.590` | um if it's not ready we we there's |
| `00:23:39.600 - 00:23:41.750` | nothing we can do so we just return |
| `00:23:41.760 - 00:23:43.669` | okay so the hardware is ready at this |
| `00:23:43.679 - 00:23:44.470` | point |
| `00:23:44.480 - 00:23:47.590` | uh when we get to this point so we get a |
| `00:23:47.600 - 00:23:51.510` | character from the buffer okay so we are |
| `00:23:51.520 - 00:23:54.549` | going to advance so we're going to grab |
| `00:23:54.559 - 00:23:56.390` | the the read index |
| `00:23:56.400 - 00:23:57.430` | and |
| `00:23:57.440 - 00:24:00.230` | mod buffer size grab that character and |
| `00:24:00.240 - 00:24:01.510` | then increment |
| `00:24:01.520 - 00:24:03.269` | the read index |
| `00:24:03.279 - 00:24:04.390` | and |
| `00:24:04.400 - 00:24:05.990` | we have now |
| `00:24:06.000 - 00:24:08.070` | started up the |
| `00:24:08.080 - 00:24:11.669` | output and we have freed a position in |
| `00:24:11.679 - 00:24:13.830` | the output buffer so we might want to |
| `00:24:13.840 - 00:24:16.630` | wake up a waiting uart put c remember we |
| `00:24:16.640 - 00:24:19.669` | just did a sleep on that on that so |
| `00:24:19.679 - 00:24:22.070` | here's where we do a wake up and again |
| `00:24:22.080 - 00:24:25.269` | we're using the address of the |
| `00:24:25.279 - 00:24:28.390` | receive or the read index as our channel |
| `00:24:28.400 - 00:24:29.510` | key |
| `00:24:29.520 - 00:24:30.470` | and |
| `00:24:30.480 - 00:24:32.470` | we also need to send that character |
| `00:24:32.480 - 00:24:35.029` | actually to the hardware so that's done |
| `00:24:35.039 - 00:24:41.350` | at this point here |
| `00:24:41.360 - 00:24:44.149` | next we have the um go through the get c |
| `00:24:44.159 - 00:24:47.750` | the uart getc function and that is |
| `00:24:47.760 - 00:24:49.430` | called to get |
| `00:24:49.440 - 00:24:51.909` | an input character get a |
| `00:24:51.919 - 00:24:53.590` | character from |
| `00:24:53.600 - 00:24:56.070` | the keyboard if one is ready |
| `00:24:56.080 - 00:24:59.269` | but not wait so here what we do is we |
| `00:24:59.279 - 00:25:00.950` | read an input character from the |
| `00:25:00.960 - 00:25:03.430` | hardware but if none is wait |
| `00:25:03.440 - 00:25:05.269` | if none is ready then we return minus |
| `00:25:05.279 - 00:25:07.590` | one and we just read the status register |
| `00:25:07.600 - 00:25:09.990` | and check the bit and if it says there's |
| `00:25:10.000 - 00:25:12.070` | something ready in the read |
| `00:25:12.080 - 00:25:13.909` | holding register then we grab it |
| `00:25:13.919 - 00:25:16.310` | otherwise we return minus one |
| `00:25:16.320 - 00:25:18.230` | and finally we have this |
| `00:25:18.240 - 00:25:21.669` | uart interrupt and whenever the |
| `00:25:21.679 - 00:25:23.590` | the hardware is uh |
| `00:25:23.600 - 00:25:24.710` | ready to |
| `00:25:24.720 - 00:25:25.590` | uh |
| `00:25:25.600 - 00:25:28.789` | receive the next output character or |
| `00:25:28.799 - 00:25:30.789` | because some input has arrived and is |
| `00:25:30.799 - 00:25:33.029` | ready to be fetched uh there's an |
| `00:25:33.039 - 00:25:35.110` | interrupt and so |
| `00:25:35.120 - 00:25:36.870` | this is going to be called |
| `00:25:36.880 - 00:25:40.390` | from the the function device interrupt |
| `00:25:40.400 - 00:25:42.549` | which is in trap.c |
| `00:25:42.559 - 00:25:44.070` | and so |
| `00:25:44.080 - 00:25:46.870` | here we read the incoming characters as |
| `00:25:46.880 - 00:25:48.549` | many as are there |
| `00:25:48.559 - 00:25:51.430` | and here we send uh |
| `00:25:51.440 - 00:25:53.190` | characters to the next character to the |
| `00:25:53.200 - 00:25:54.149` | output |
| `00:25:54.159 - 00:25:55.110` | so |
| `00:25:55.120 - 00:25:57.110` | here we um |
| `00:25:57.120 - 00:26:01.190` | call the uart get c to read a character |
| `00:26:01.200 - 00:26:04.549` | and um if there's nothing there then |
| `00:26:04.559 - 00:26:05.830` | we're done reading all the input |
| `00:26:05.840 - 00:26:08.470` | characters otherwise we call console |
| `00:26:08.480 - 00:26:10.549` | interrupt console we're we're in the |
| `00:26:10.559 - 00:26:13.350` | interrupt uh handler so console |
| `00:26:13.360 - 00:26:16.070` | interrupt will not sleep if there are |
| `00:26:16.080 - 00:26:17.590` | too many characters and no room in the |
| `00:26:17.600 - 00:26:19.669` | input buffer they just get dropped we |
| `00:26:19.679 - 00:26:22.070` | don't wait uh we just drop them and that |
| `00:26:22.080 - 00:26:25.190` | will happen in the console interrupt |
| `00:26:25.200 - 00:26:25.990` | okay |
| `00:26:26.000 - 00:26:30.230` | then uh we call uart start |
| `00:26:30.240 - 00:26:34.789` | to send characters to the output buffer |
| `00:26:34.799 - 00:26:37.190` | next i want to walk through the code in |
| `00:26:37.200 - 00:26:39.029` | console.c |
| `00:26:39.039 - 00:26:41.430` | and i'm going to start with this macro |
| `00:26:41.440 - 00:26:43.510` | function c here |
| `00:26:43.520 - 00:26:45.909` | from time to time we need to |
| `00:26:45.919 - 00:26:48.230` | deal with control characters so for |
| `00:26:48.240 - 00:26:50.710` | example right here we're asking whether |
| `00:26:50.720 - 00:26:53.909` | the character is equal to a control d |
| `00:26:53.919 - 00:26:56.149` | and we also are concerned with things |
| `00:26:56.159 - 00:27:00.390` | like uh control p and control u and uh |
| `00:27:00.400 - 00:27:01.990` | control h |
| `00:27:02.000 - 00:27:02.870` | so |
| `00:27:02.880 - 00:27:04.710` | remember what these control characters |
| `00:27:04.720 - 00:27:06.310` | are |
| `00:27:06.320 - 00:27:08.230` | these are ascii characters it's a way of |
| `00:27:08.240 - 00:27:13.110` | specifying uh any ascii value |
| `00:27:13.120 - 00:27:15.590` | that has a very low number so they start |
| `00:27:15.600 - 00:27:18.230` | with the ampersand and ctrl a |
| `00:27:18.240 - 00:27:21.029` | corresponds to number one control b is |
| `00:27:21.039 - 00:27:24.149` | two and so on control d corresponds to |
| `00:27:24.159 - 00:27:25.669` | number four |
| `00:27:25.679 - 00:27:26.870` | and |
| `00:27:26.880 - 00:27:29.110` | normally we're used to uh using things |
| `00:27:29.120 - 00:27:31.430` | like backslash escapes to deal with |
| `00:27:31.440 - 00:27:33.830` | control characters for example the new |
| `00:27:33.840 - 00:27:35.990` | line characters backslash n |
| `00:27:36.000 - 00:27:38.549` | well that's equivalent to uh decimal 10 |
| `00:27:38.559 - 00:27:41.909` | or hex a okay you can also type control |
| `00:27:41.919 - 00:27:44.950` | j uh to to achieve the same thing so you |
| `00:27:44.960 - 00:27:46.789` | can try that on your keyboard if you |
| `00:27:46.799 - 00:27:48.789` | type ctrl j it's probably equivalent to |
| `00:27:48.799 - 00:27:49.830` | hitting the |
| `00:27:49.840 - 00:27:52.470` | enter or return key |
| `00:27:52.480 - 00:27:54.389` | we also have control d which is an |
| `00:27:54.399 - 00:27:57.269` | important one and that is equivalent to |
| `00:27:57.279 - 00:28:00.230` | number four so control d is number four |
| `00:28:00.240 - 00:28:01.110` | here |
| `00:28:01.120 - 00:28:03.190` | and so what this macro is doing it's |
| `00:28:03.200 - 00:28:05.029` | just allowing us to specify with a |
| `00:28:05.039 - 00:28:07.029` | letter and it's just subtracting the |
| `00:28:07.039 - 00:28:09.110` | ampersand which is the control for |
| `00:28:09.120 - 00:28:10.549` | control 0 |
| `00:28:10.559 - 00:28:14.149` | here and so that's what's going on there |
| `00:28:14.159 - 00:28:15.110` | this |
| `00:28:15.120 - 00:28:17.669` | defined constant backspace |
| `00:28:17.679 - 00:28:19.269` | is a number that's greater than any |
| `00:28:19.279 - 00:28:22.789` | ascii uh number uh the ascii codes go |
| `00:28:22.799 - 00:28:27.029` | all the way up to hex 7f or 127. and |
| `00:28:27.039 - 00:28:29.990` | this is 2 decimal 256 so it's not a |
| `00:28:30.000 - 00:28:31.510` | nasty code |
| `00:28:31.520 - 00:28:32.870` | um |
| `00:28:32.880 - 00:28:34.230` | now let's move into |
| `00:28:34.240 - 00:28:36.310` | cons put c |
| `00:28:36.320 - 00:28:40.389` | uh constant c is basically called to um |
| `00:28:40.399 - 00:28:42.830` | send a character directly to |
| `00:28:42.840 - 00:28:47.430` | the output so it actually sits between |
| `00:28:47.440 - 00:28:49.269` | printf and |
| `00:28:49.279 - 00:28:50.870` | uart put c |
| `00:28:50.880 - 00:28:52.230` | sync |
| `00:28:52.240 - 00:28:53.830` | printf actually doesn't call this |
| `00:28:53.840 - 00:28:57.590` | directly but it calls console put c and |
| `00:28:57.600 - 00:29:00.389` | console.c just calls you output c so |
| `00:29:00.399 - 00:29:02.549` | it's past the character and then it |
| `00:29:02.559 - 00:29:04.870` | passes it on through where it is then |
| `00:29:04.880 - 00:29:06.389` | sent directly to |
| `00:29:06.399 - 00:29:08.789` | the output hardware |
| `00:29:08.799 - 00:29:11.190` | however it's also used for echoing and |
| `00:29:11.200 - 00:29:13.750` | for that purpose it checks to see |
| `00:29:13.760 - 00:29:15.510` | whether we have a special case of the |
| `00:29:15.520 - 00:29:17.110` | backspace |
| `00:29:17.120 - 00:29:20.310` | when the user types the backspace key or |
| `00:29:20.320 - 00:29:22.389` | the delete key |
| `00:29:22.399 - 00:29:23.750` | the old |
| `00:29:23.760 - 00:29:27.510` | serialio terminals required the |
| `00:29:27.520 - 00:29:29.590` | terminal to |
| `00:29:29.600 - 00:29:31.350` | back up and |
| `00:29:31.360 - 00:29:33.909` | print a blank to overwrite the character |
| `00:29:33.919 - 00:29:35.909` | so for example if the user types a w and |
| `00:29:35.919 - 00:29:37.350` | then a backspace |
| `00:29:37.360 - 00:29:40.389` | it displays a w and then the terminal |
| `00:29:40.399 - 00:29:42.710` | needs to back up one character display a |
| `00:29:42.720 - 00:29:45.110` | bank blank to get rid of the w and then |
| `00:29:45.120 - 00:29:47.669` | back up one more more time so that the |
| `00:29:47.679 - 00:29:50.149` | next character will go where the w |
| `00:29:50.159 - 00:29:52.310` | had gone before so that's what's going |
| `00:29:52.320 - 00:29:53.830` | on here |
| `00:29:53.840 - 00:29:56.070` | if we have that situation |
| `00:29:56.080 - 00:29:59.190` | we send to the output terminal to the |
| `00:29:59.200 - 00:30:01.830` | display a backspace character followed |
| `00:30:01.840 - 00:30:03.830` | by a blank character followed by another |
| `00:30:03.840 - 00:30:06.630` | backspace character |
| `00:30:06.640 - 00:30:09.510` | okay next let's move on to um |
| `00:30:09.520 - 00:30:12.470` | this the second page and remember that |
| `00:30:12.480 - 00:30:13.350` | our |
| `00:30:13.360 - 00:30:15.190` | output buffer |
| `00:30:15.200 - 00:30:16.470` | um |
| `00:30:16.480 - 00:30:19.190` | was defined i i pictured it here like |
| `00:30:19.200 - 00:30:20.389` | this uh |
| `00:30:20.399 - 00:30:23.110` | and it has these three indices and we |
| `00:30:23.120 - 00:30:26.789` | see that going on here here is the |
| `00:30:26.799 - 00:30:28.070` | cons |
| `00:30:28.080 - 00:30:29.750` | uh buffer |
| `00:30:29.760 - 00:30:31.990` | of 128 bytes |
| `00:30:32.000 - 00:30:35.269` | and here are our three indexes |
| `00:30:35.279 - 00:30:37.590` | and we've also got a |
| `00:30:37.600 - 00:30:40.549` | cons lock a lock associated with this |
| `00:30:40.559 - 00:30:46.310` | that protects the buffer and the indexes |
| `00:30:46.320 - 00:30:47.590` | so |
| `00:30:47.600 - 00:30:50.870` | next let's look at the write function |
| `00:30:50.880 - 00:30:52.230` | and |
| `00:30:52.240 - 00:30:53.430` | it's |
| `00:30:53.440 - 00:30:55.269` | called to |
| `00:30:55.279 - 00:30:57.269` | send characters to |
| `00:30:57.279 - 00:31:00.789` | the output buffer so |
| `00:31:00.799 - 00:31:03.029` | it's called here whenever we have a |
| `00:31:03.039 - 00:31:04.710` | bunch of characters and we want to send |
| `00:31:04.720 - 00:31:06.789` | them to the output it's got to add them |
| `00:31:06.799 - 00:31:09.750` | to the buffer one by one and so it will |
| `00:31:09.760 - 00:31:12.470` | call uart put c for each individual |
| `00:31:12.480 - 00:31:14.230` | character to add it to the buffer and |
| `00:31:14.240 - 00:31:16.070` | that's what's going on here |
| `00:31:16.080 - 00:31:19.029` | it's passed a pointer source and the |
| `00:31:19.039 - 00:31:22.149` | count of characters to send the output |
| `00:31:22.159 - 00:31:24.230` | and it's got a loop that does uh that |
| `00:31:24.240 - 00:31:25.590` | many times |
| `00:31:25.600 - 00:31:29.750` | and uh for each character it calls uart |
| `00:31:29.760 - 00:31:31.430` | put c |
| `00:31:31.440 - 00:31:32.470` | now |
| `00:31:32.480 - 00:31:34.470` | source points to |
| `00:31:34.480 - 00:31:36.389` | the place where the characters are to be |
| `00:31:36.399 - 00:31:38.710` | found and n is the number of characters |
| `00:31:38.720 - 00:31:41.110` | we want to send to the output |
| `00:31:41.120 - 00:31:43.590` | these characters can either be located |
| `00:31:43.600 - 00:31:45.750` | in the kernel's address space |
| `00:31:45.760 - 00:31:47.990` | or they can be located in |
| `00:31:48.000 - 00:31:50.230` | user virtual memory and this boolean |
| `00:31:50.240 - 00:31:53.190` | flag right here tells which |
| `00:31:53.200 - 00:31:54.070` | so |
| `00:31:54.080 - 00:31:55.990` | this determines whether those characters |
| `00:31:56.000 - 00:31:57.509` | will be |
| `00:31:57.519 - 00:31:59.750` | gotten from the virtual address space |
| `00:31:59.760 - 00:32:02.789` | which requires mapping or from a direct |
| `00:32:02.799 - 00:32:05.269` | mapped address and so that's what this |
| `00:32:05.279 - 00:32:06.789` | function here is doing |
| `00:32:06.799 - 00:32:09.990` | it is past a place to put the characters |
| `00:32:10.000 - 00:32:12.149` | and the number of characters to get and |
| `00:32:12.159 - 00:32:14.389` | it's getting one character at a time |
| `00:32:14.399 - 00:32:17.190` | and it's getting them from this address |
| `00:32:17.200 - 00:32:19.029` | okay so we're incrementing through the |
| `00:32:19.039 - 00:32:20.549` | source buffer |
| `00:32:20.559 - 00:32:22.710` | one by one and we're getting one |
| `00:32:22.720 - 00:32:24.710` | character at a time and we're passing it |
| `00:32:24.720 - 00:32:26.389` | this flag to tell whether they're coming |
| `00:32:26.399 - 00:32:27.269` | from |
| `00:32:27.279 - 00:32:30.470` | a user address space or not and |
| `00:32:30.480 - 00:32:31.350` | the |
| `00:32:31.360 - 00:32:33.190` | local variable c is just a place where |
| `00:32:33.200 - 00:32:34.950` | we put them |
| `00:32:34.960 - 00:32:37.750` | before we send them to the output |
| `00:32:37.760 - 00:32:39.190` | and then we return the number of |
| `00:32:39.200 - 00:32:42.549` | characters that we successfully wrote |
| `00:32:42.559 - 00:32:44.310` | and if anything goes wrong for example |
| `00:32:44.320 - 00:32:45.509` | the user |
| `00:32:45.519 - 00:32:47.350` | provides a virtual address that's no |
| `00:32:47.360 - 00:32:49.190` | good or that |
| `00:32:49.200 - 00:32:53.669` | then we return -1 from this and |
| `00:32:53.679 - 00:32:55.750` | that will cause us to break and so we'll |
| `00:32:55.760 - 00:32:57.590` | stop sending characters to the output if |
| `00:32:57.600 - 00:33:00.310` | we run into a bad virtual address |
| `00:33:00.320 - 00:33:01.990` | okay let's move on to |
| `00:33:02.000 - 00:33:03.509` | the next page |
| `00:33:03.519 - 00:33:04.710` | and |
| `00:33:04.720 - 00:33:06.470` | this is the |
| `00:33:06.480 - 00:33:09.269` | console read function |
| `00:33:09.279 - 00:33:12.310` | and it too is passed a buffer here's the |
| `00:33:12.320 - 00:33:14.149` | address of the buffer and here's the |
| `00:33:14.159 - 00:33:15.830` | number of characters in the buffer we |
| `00:33:15.840 - 00:33:18.310` | want to get all the characters from this |
| `00:33:18.320 - 00:33:20.549` | buffer and |
| `00:33:20.559 - 00:33:22.950` | sorry we want to read from the keyboard |
| `00:33:22.960 - 00:33:25.029` | and send the |
| `00:33:25.039 - 00:33:27.269` | data to the user by placing those |
| `00:33:27.279 - 00:33:30.310` | characters in this buffer |
| `00:33:30.320 - 00:33:32.630` | so uh |
| `00:33:32.640 - 00:33:35.509` | we will be getting characters out of the |
| `00:33:35.519 - 00:33:38.149` | buffer here and so |
| `00:33:38.159 - 00:33:41.430` | we need to |
| `00:33:41.440 - 00:33:44.149` | acquire the lock on that so we've got a |
| `00:33:44.159 - 00:33:46.149` | while loop here and what we're going to |
| `00:33:46.159 - 00:33:49.830` | do is we're going to |
| `00:33:49.840 - 00:33:51.430` | get characters out of the buffer and |
| `00:33:51.440 - 00:33:53.430` | that's going on right here you can see |
| `00:33:53.440 - 00:33:55.350` | that we're grabbing a character from the |
| `00:33:55.360 - 00:33:56.549` | buffer |
| `00:33:56.559 - 00:33:59.190` | using the our index and then we're |
| `00:33:59.200 - 00:34:01.350` | incrementing it and let me skip this |
| `00:34:01.360 - 00:34:03.830` | part right here but going down further |
| `00:34:03.840 - 00:34:07.029` | we decrement our count and |
| `00:34:07.039 - 00:34:08.710` | when we've gotten as many characters as |
| `00:34:08.720 - 00:34:10.710` | we want we then |
| `00:34:10.720 - 00:34:13.190` | terminate the loop release the lock and |
| `00:34:13.200 - 00:34:14.389` | we return |
| `00:34:14.399 - 00:34:16.310` | and we need to return how many |
| `00:34:16.320 - 00:34:18.629` | characters we actually successfully read |
| `00:34:18.639 - 00:34:20.950` | so that's what's going on with target |
| `00:34:20.960 - 00:34:23.909` | okay this target is saved here local |
| `00:34:23.919 - 00:34:26.149` | variable which if we want for example |
| `00:34:26.159 - 00:34:28.310` | 100 characters |
| `00:34:28.320 - 00:34:30.470` | we remember that we want 100 characters |
| `00:34:30.480 - 00:34:32.389` | and then we decrement down and if |
| `00:34:32.399 - 00:34:34.470` | something goes wrong we execute this we |
| `00:34:34.480 - 00:34:37.030` | exit the loop early uh we won't have |
| `00:34:37.040 - 00:34:38.629` | gone all the way to zero but if we get |
| `00:34:38.639 - 00:34:40.069` | all the characters |
| `00:34:40.079 - 00:34:42.310` | uh then n will be decremented all the |
| `00:34:42.320 - 00:34:44.710` | way to zero and we can just return |
| `00:34:44.720 - 00:34:46.829` | target but if we exit the loop |
| `00:34:46.839 - 00:34:49.990` | prematurely then we return something |
| `00:34:50.000 - 00:34:53.750` | less than target |
| `00:34:53.760 - 00:34:55.909` | so as we are getting the characters |
| `00:34:55.919 - 00:34:57.589` | let's go back to this point where we get |
| `00:34:57.599 - 00:35:01.510` | the characters um we get a character c |
| `00:35:01.520 - 00:35:02.550` | and |
| `00:35:02.560 - 00:35:04.310` | then |
| `00:35:04.320 - 00:35:06.550` | here what we're going to do is |
| `00:35:06.560 - 00:35:09.430` | use this function either copy out and |
| `00:35:09.440 - 00:35:11.430` | it's going to move well we're moving one |
| `00:35:11.440 - 00:35:14.390` | character at a time and where are we |
| `00:35:14.400 - 00:35:17.109` | putting them well this is the |
| `00:35:17.119 - 00:35:19.349` | um |
| `00:35:19.359 - 00:35:21.910` | the place where we're getting them from |
| `00:35:21.920 - 00:35:24.550` | okay that's this local variable c buff |
| `00:35:24.560 - 00:35:27.589` | here and destination is the |
| `00:35:27.599 - 00:35:30.069` | address that was provided for us that's |
| `00:35:30.079 - 00:35:32.150` | where we are to place them and user |
| `00:35:32.160 - 00:35:33.910` | destination is a boolean that tells |
| `00:35:33.920 - 00:35:35.190` | whether this is a |
| `00:35:35.200 - 00:35:37.349` | the user's virtual address space or not |
| `00:35:37.359 - 00:35:39.589` | so we're passing that and |
| `00:35:39.599 - 00:35:42.630` | copy out will then move one in this case |
| `00:35:42.640 - 00:35:45.030` | one character from here to the |
| `00:35:45.040 - 00:35:47.910` | destination and if that worked okay then |
| `00:35:47.920 - 00:35:49.910` | it won't return a minus one but if we |
| `00:35:49.920 - 00:35:52.069` | had any problems we'll break |
| `00:35:52.079 - 00:35:53.589` | and uh |
| `00:35:53.599 - 00:35:54.710` | we'll just |
| `00:35:54.720 - 00:35:58.630` | we won't have succeeded in uh decker in |
| `00:35:58.640 - 00:35:59.910` | moving that character so we don't |
| `00:35:59.920 - 00:36:01.190` | decrement in |
| `00:36:01.200 - 00:36:03.829` | instead we return uh some number short |
| `00:36:03.839 - 00:36:05.670` | of our target |
| `00:36:05.680 - 00:36:06.470` | and |
| `00:36:06.480 - 00:36:08.630` | if we do succeed uh in moving the |
| `00:36:08.640 - 00:36:11.190` | character into the user's address space |
| `00:36:11.200 - 00:36:12.550` | then we increment |
| `00:36:12.560 - 00:36:14.630` | uh the pointer uh for the next time |
| `00:36:14.640 - 00:36:16.230` | around the loop |
| `00:36:16.240 - 00:36:18.870` | uh if we had just moved the new line |
| `00:36:18.880 - 00:36:20.150` | we've then we've |
| `00:36:20.160 - 00:36:23.430` | properly read a full complete line and |
| `00:36:23.440 - 00:36:26.550` | we need to return at that point so after |
| `00:36:26.560 - 00:36:28.790` | moving the new line into the user's |
| `00:36:28.800 - 00:36:31.750` | buffer we break out of the loop here |
| `00:36:31.760 - 00:36:33.190` | now there's one other |
| `00:36:33.200 - 00:36:35.750` | thing moving back up to |
| `00:36:35.760 - 00:36:37.910` | this point in the loop |
| `00:36:37.920 - 00:36:38.870` | we are |
| `00:36:38.880 - 00:36:40.790` | checking to see whether we got a control |
| `00:36:40.800 - 00:36:42.069` | d |
| `00:36:42.079 - 00:36:43.109` | now |
| `00:36:43.119 - 00:36:47.190` | if this is the um |
| `00:36:47.200 - 00:36:48.950` | first uh |
| `00:36:48.960 - 00:36:52.710` | character that uh we've gotten okay in |
| `00:36:52.720 - 00:36:54.550` | other words uh this is the first time |
| `00:36:54.560 - 00:36:57.589` | through the loop n and target are equal |
| `00:36:57.599 - 00:37:00.150` | then um |
| `00:37:00.160 - 00:37:01.349` | this will |
| `00:37:01.359 - 00:37:02.710` | not be true |
| `00:37:02.720 - 00:37:05.670` | um and we will simply break uh we will |
| `00:37:05.680 - 00:37:09.190` | have gotten no characters so um |
| `00:37:09.200 - 00:37:12.790` | the uh target here will be returned as |
| `00:37:12.800 - 00:37:14.069` | zero |
| `00:37:14.079 - 00:37:18.150` | and uh |
| `00:37:18.160 - 00:37:20.310` | then the next time around |
| `00:37:20.320 - 00:37:22.069` | we'll read some more characters so |
| `00:37:22.079 - 00:37:24.710` | that's the way unix works uh when we get |
| `00:37:24.720 - 00:37:28.230` | a control d we have to return an empty |
| `00:37:28.240 - 00:37:30.710` | string a string of length zero uh but |
| `00:37:30.720 - 00:37:33.190` | then after that we can keep going |
| `00:37:33.200 - 00:37:35.270` | on the other hand if we've already |
| `00:37:35.280 - 00:37:37.589` | moved some characters into |
| `00:37:37.599 - 00:37:39.910` | the user's buffer |
| `00:37:39.920 - 00:37:41.190` | then we |
| `00:37:41.200 - 00:37:42.950` | basically need to |
| `00:37:42.960 - 00:37:44.950` | not do the control d here we need to |
| `00:37:44.960 - 00:37:46.790` | stop okay |
| `00:37:46.800 - 00:37:49.510` | and that's so we break but we also need |
| `00:37:49.520 - 00:37:52.710` | to remember that we have hit a control d |
| `00:37:52.720 - 00:37:54.150` | and so the next time around we're going |
| `00:37:54.160 - 00:37:56.870` | to need to return a line of zero |
| `00:37:56.880 - 00:37:58.630` | characters so that's why we decrement |
| `00:37:58.640 - 00:38:01.030` | this r here so that the next time we're |
| `00:38:01.040 - 00:38:03.270` | called in this in this function we can |
| `00:38:03.280 - 00:38:04.550` | return |
| `00:38:04.560 - 00:38:07.190` | zero characters |
| `00:38:07.200 - 00:38:09.349` | okay moving on to the next function in |
| `00:38:09.359 - 00:38:13.270` | this file we have the console interrupt |
| `00:38:13.280 - 00:38:15.190` | function and this is past a single |
| `00:38:15.200 - 00:38:17.030` | character |
| `00:38:17.040 - 00:38:20.069` | remember how this is used uh whenever |
| `00:38:20.079 - 00:38:20.870` | the |
| `00:38:20.880 - 00:38:24.230` | uart interrupt function is um |
| `00:38:24.240 - 00:38:25.829` | getting a character from the keyboard |
| `00:38:25.839 - 00:38:28.150` | it's going to call this function for |
| `00:38:28.160 - 00:38:32.950` | each character so uh going back to that |
| `00:38:32.960 - 00:38:34.950` | uart interrupt function |
| `00:38:34.960 - 00:38:36.710` | uh it's basically looking to see whether |
| `00:38:36.720 - 00:38:38.310` | there are any input characters ready |
| `00:38:38.320 - 00:38:41.190` | from the hardware and if there are for |
| `00:38:41.200 - 00:38:43.190` | each one it's going to call this |
| `00:38:43.200 - 00:38:45.430` | function |
| `00:38:45.440 - 00:38:47.349` | so we've got a character |
| `00:38:47.359 - 00:38:49.910` | and uh we need to do something with it |
| `00:38:49.920 - 00:38:51.910` | okay the character is c |
| `00:38:51.920 - 00:38:55.030` | and we are going to be putting it into |
| `00:38:55.040 - 00:38:57.510` | the input buffer so we need to acquire |
| `00:38:57.520 - 00:39:01.589` | the lock that protects the input buffer |
| `00:39:01.599 - 00:39:04.550` | the console buffer and uh we acquire it |
| `00:39:04.560 - 00:39:06.390` | then we have a switch statement and we |
| `00:39:06.400 - 00:39:08.310` | look at what kind of character it is it |
| `00:39:08.320 - 00:39:09.109` | is |
| `00:39:09.119 - 00:39:11.190` | and um |
| `00:39:11.200 - 00:39:13.829` | then following down here after the |
| `00:39:13.839 - 00:39:15.829` | switch statement we release the lock on |
| `00:39:15.839 - 00:39:19.589` | the buffer and return |
| `00:39:19.599 - 00:39:23.910` | so we do a couple things if we |
| `00:39:23.920 - 00:39:25.030` | see that the |
| `00:39:25.040 - 00:39:27.190` | input character is a control p |
| `00:39:27.200 - 00:39:29.109` | we handle it especially |
| `00:39:29.119 - 00:39:30.150` | by |
| `00:39:30.160 - 00:39:32.310` | printing the process list we call proc |
| `00:39:32.320 - 00:39:34.150` | dump so that could be useful for |
| `00:39:34.160 - 00:39:35.750` | debugging the kernel and seeing what's |
| `00:39:35.760 - 00:39:37.589` | going on it's not |
| `00:39:37.599 - 00:39:38.790` | uh |
| `00:39:38.800 - 00:39:42.630` | if it is a let's do control h |
| `00:39:42.640 - 00:39:43.349` | the |
| `00:39:43.359 - 00:39:45.109` | backspace character |
| `00:39:45.119 - 00:39:49.030` | will send a control h that is an ascii |
| `00:39:49.040 - 00:39:51.910` | 08 character to |
| `00:39:51.920 - 00:39:55.670` | the hardware and the delete key |
| `00:39:55.680 - 00:39:58.550` | generally will send the ascii character |
| `00:39:58.560 - 00:40:00.470` | hex 7f |
| `00:40:00.480 - 00:40:01.990` | so |
| `00:40:02.000 - 00:40:03.589` | regardless of which of these two |
| `00:40:03.599 - 00:40:05.910` | characters is with two keys is pressed |
| `00:40:05.920 - 00:40:08.069` | or which character we receive we want to |
| `00:40:08.079 - 00:40:10.710` | treat them by backing up |
| `00:40:10.720 - 00:40:11.990` | and so |
| `00:40:12.000 - 00:40:16.069` | we ask here do we have anything uh |
| `00:40:16.079 - 00:40:18.550` | in the input in other words |
| `00:40:18.560 - 00:40:20.790` | have we gotten anything |
| `00:40:20.800 - 00:40:24.390` | uh is e greater than w |
| `00:40:24.400 - 00:40:25.349` | if |
| `00:40:25.359 - 00:40:26.950` | so |
| `00:40:26.960 - 00:40:28.470` | then we want to |
| `00:40:28.480 - 00:40:32.870` | back up e one byte and we want to |
| `00:40:32.880 - 00:40:35.430` | call cons put c with this special |
| `00:40:35.440 - 00:40:37.030` | backspace |
| `00:40:37.040 - 00:40:40.390` | code which will obliterate the character |
| `00:40:40.400 - 00:40:43.190` | by doing a backspace and then planck and |
| `00:40:43.200 - 00:40:45.510` | then printing another backspace |
| `00:40:45.520 - 00:40:47.190` | so that's how we deal with a backspace |
| `00:40:47.200 - 00:40:48.710` | or delete key |
| `00:40:48.720 - 00:40:50.870` | and if we get a control u we do |
| `00:40:50.880 - 00:40:52.790` | something similar |
| `00:40:52.800 - 00:40:55.109` | we ask |
| `00:40:55.119 - 00:40:57.030` | we basically are going to do the same |
| `00:40:57.040 - 00:41:00.390` | thing repeatedly until we back up e |
| `00:41:00.400 - 00:41:03.589` | uh to the next control uh n that is |
| `00:41:03.599 - 00:41:04.550` | sorry a |
| `00:41:04.560 - 00:41:07.510` | new line character or until we hit w so |
| `00:41:07.520 - 00:41:09.430` | that's what's going on with this loop |
| `00:41:09.440 - 00:41:12.710` | as long as e we can back up some that is |
| `00:41:12.720 - 00:41:14.309` | e and w are different |
| `00:41:14.319 - 00:41:16.950` | and we haven't yet hit the new line |
| `00:41:16.960 - 00:41:19.510` | character then we do the same thing we |
| `00:41:19.520 - 00:41:21.109` | decrement a and |
| `00:41:21.119 - 00:41:25.270` | call cons put c with this backspace code |
| `00:41:25.280 - 00:41:27.510` | now let's look at what happens |
| `00:41:27.520 - 00:41:32.069` | for everything else |
| `00:41:32.079 - 00:41:35.510` | if the character is a null character |
| `00:41:35.520 - 00:41:37.030` | we ignore it |
| `00:41:37.040 - 00:41:40.230` | and if the buffer is completely full we |
| `00:41:40.240 - 00:41:41.349` | ignore it |
| `00:41:41.359 - 00:41:43.670` | okay so we just drop the character if |
| `00:41:43.680 - 00:41:46.950` | the buffer is full here we're asking um |
| `00:41:46.960 - 00:41:49.430` | doing the buffer full test |
| `00:41:49.440 - 00:41:52.710` | with this right here |
| `00:41:52.720 - 00:41:54.870` | if you hit uh the enter key or the |
| `00:41:54.880 - 00:41:57.589` | return key uh on different terminals can |
| `00:41:57.599 - 00:41:59.829` | either return |
| `00:41:59.839 - 00:42:02.390` | the so-called ascii return code |
| `00:42:02.400 - 00:42:06.309` | or the ascii newline code |
| `00:42:06.319 - 00:42:08.870` | this is decimal 10 and this is decimal |
| `00:42:08.880 - 00:42:10.230` | 13. |
| `00:42:10.240 - 00:42:13.430` | also 0x0d |
| `00:42:13.440 - 00:42:18.470` | or 0x 0a |
| `00:42:18.480 - 00:42:20.870` | some of us have memorized these |
| `00:42:20.880 - 00:42:23.109` | and |
| `00:42:23.119 - 00:42:25.510` | if it is return |
| `00:42:25.520 - 00:42:27.750` | we convert it to the newline character |
| `00:42:27.760 - 00:42:29.430` | with this otherwise we keep it whatever |
| `00:42:29.440 - 00:42:30.870` | it is |
| `00:42:30.880 - 00:42:35.589` | then we echo it uh back to the um output |
| `00:42:35.599 - 00:42:39.270` | and then we store it into the buffer so |
| `00:42:39.280 - 00:42:42.870` | here we're using e so we add it here |
| `00:42:42.880 - 00:42:44.630` | whether it's a new line or something |
| `00:42:44.640 - 00:42:47.670` | else we add it here and increment e and |
| `00:42:47.680 - 00:42:50.710` | so that's what's going on at this point |
| `00:42:50.720 - 00:42:51.750` | and |
| `00:42:51.760 - 00:42:52.710` | then |
| `00:42:52.720 - 00:42:55.510` | we determine whether we've got something |
| `00:42:55.520 - 00:42:58.550` | that we need to wake up the reader okay |
| `00:42:58.560 - 00:42:59.430` | so |
| `00:42:59.440 - 00:43:00.790` | if it was a |
| `00:43:00.800 - 00:43:03.109` | control a new line |
| `00:43:03.119 - 00:43:05.990` | or it was a control d |
| `00:43:06.000 - 00:43:07.030` | or the |
| `00:43:07.040 - 00:43:09.990` | buffer was full then we need to wake up |
| `00:43:10.000 - 00:43:10.790` | any |
| `00:43:10.800 - 00:43:13.589` | sleeping console read function |
| `00:43:13.599 - 00:43:16.710` | okay so that's what we do here |
| `00:43:16.720 - 00:43:19.829` | first of all we update w so |
| `00:43:19.839 - 00:43:22.230` | that moves w all the way up to here |
| `00:43:22.240 - 00:43:24.309` | which means that the reader can read as |
| `00:43:24.319 - 00:43:25.430` | much as |
| `00:43:25.440 - 00:43:27.349` | as is available |
| `00:43:27.359 - 00:43:30.550` | and then we do a wake up so this is |
| `00:43:30.560 - 00:43:31.589` | using |
| `00:43:31.599 - 00:43:33.589` | uh as a channel |
| `00:43:33.599 - 00:43:34.390` | the |
| `00:43:34.400 - 00:43:37.349` | address of the read index variable |
| `00:43:37.359 - 00:43:41.030` | and so that uh |
| `00:43:41.040 - 00:43:43.109` | concludes this function |
| `00:43:43.119 - 00:43:44.950` | uh we've got |
| `00:43:44.960 - 00:43:47.109` | uh so we just we've got one more |
| `00:43:47.119 - 00:43:49.030` | function after that here's the end of |
| `00:43:49.040 - 00:43:51.109` | that switch statement and the release |
| `00:43:51.119 - 00:43:52.470` | and then we've got |
| `00:43:52.480 - 00:43:54.870` | an initialize function that is going to |
| `00:43:54.880 - 00:43:57.349` | initialize the console lock |
| `00:43:57.359 - 00:43:59.270` | and it's also going to call the uart |
| `00:43:59.280 - 00:44:00.790` | initialize function |
| `00:44:00.800 - 00:44:02.390` | um |
| `00:44:02.400 - 00:44:03.829` | don't want to really get into that this |
| `00:44:03.839 - 00:44:05.910` | here but we're saving pointers to the |
| `00:44:05.920 - 00:44:10.710` | console read and console.write functions |
| `00:44:10.720 - 00:44:15.079` | okay that's it see you in the next video |
