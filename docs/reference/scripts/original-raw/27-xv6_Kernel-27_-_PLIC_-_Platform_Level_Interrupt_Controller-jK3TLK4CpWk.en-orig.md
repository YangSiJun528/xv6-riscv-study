# xv6 Kernel-27: PLIC: Platform Level Interrupt Controller

| Field | Value |
| --- | --- |
| Video ID | `jK3TLK4CpWk` |
| URL | <https://www.youtube.com/watch?v=jK3TLK4CpWk> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.640 - 00:00:02.550` | what happens when an i o device wants to |
| `00:00:02.560 - 00:00:04.710` | interrupt a processor core |
| `00:00:04.720 - 00:00:06.470` | well if this question has been bothering |
| `00:00:06.480 - 00:00:09.030` | you then this is the video for you |
| `00:00:09.040 - 00:00:11.350` | in this video i will describe and |
| `00:00:11.360 - 00:00:13.830` | explain the platform level interrupt |
| `00:00:13.840 - 00:00:16.630` | controller or the click |
| `00:00:16.640 - 00:00:18.870` | this is a circuit that's particular to |
| `00:00:18.880 - 00:00:22.630` | the risc-5 processor ecosystem |
| `00:00:22.640 - 00:00:24.390` | although other computers have similar |
| `00:00:24.400 - 00:00:26.150` | kinds of circuits so |
| `00:00:26.160 - 00:00:27.750` | if you are interested in the subject |
| `00:00:27.760 - 00:00:30.950` | generally this will be a helpful video |
| `00:00:30.960 - 00:00:32.389` | this video is part of a series on the |
| `00:00:32.399 - 00:00:35.270` | xv6 operating system kernel but this |
| `00:00:35.280 - 00:00:37.510` | video is pretty much just about the |
| `00:00:37.520 - 00:00:39.990` | click and doesn't talk too much about |
| `00:00:40.000 - 00:00:42.229` | the kernel itself however at the very |
| `00:00:42.239 - 00:00:45.029` | end of this video i will go over the |
| `00:00:45.039 - 00:00:48.630` | code in the file click dot c |
| `00:00:48.640 - 00:00:50.549` | let's start with a high level overview |
| `00:00:50.559 - 00:00:52.229` | of the problem |
| `00:00:52.239 - 00:00:55.110` | here we see some i o devices we may have |
| `00:00:55.120 - 00:00:57.029` | some disks |
| `00:00:57.039 - 00:00:58.630` | here's a keyboard |
| `00:00:58.640 - 00:01:00.549` | we may have some other devices like a |
| `00:01:00.559 - 00:01:01.830` | uart |
| `00:01:01.840 - 00:01:05.350` | or a usb card or network card |
| `00:01:05.360 - 00:01:07.990` | and we have cores down here |
| `00:01:08.000 - 00:01:09.750` | and from time to time |
| `00:01:09.760 - 00:01:12.550` | an i o device will require attention |
| `00:01:12.560 - 00:01:14.630` | and it will need to interrupt one of the |
| `00:01:14.640 - 00:01:15.590` | cores |
| `00:01:15.600 - 00:01:18.070` | so for example when a human types a |
| `00:01:18.080 - 00:01:20.230` | character the keyboard |
| `00:01:20.240 - 00:01:22.230` | might send an interrupt and it needs to |
| `00:01:22.240 - 00:01:24.550` | be routed to some core and that core |
| `00:01:24.560 - 00:01:26.789` | will then uh talk to the keyboard |
| `00:01:26.799 - 00:01:28.230` | directly |
| `00:01:28.240 - 00:01:30.710` | it will run an interrupt handler so the |
| `00:01:30.720 - 00:01:32.950` | core will be executing along it will get |
| `00:01:32.960 - 00:01:35.510` | the interrupt a trap will occur and then |
| `00:01:35.520 - 00:01:38.149` | the interrupt handler code will run that |
| `00:01:38.159 - 00:01:40.310` | code will then ask the keyboard what |
| `00:01:40.320 - 00:01:42.149` | character was entered and |
| `00:01:42.159 - 00:01:44.230` | probably add it to some buffer somewhere |
| `00:01:44.240 - 00:01:46.149` | and then that interrupt handler will say |
| `00:01:46.159 - 00:01:48.550` | i'm done with the interrupt and go back |
| `00:01:48.560 - 00:01:49.350` | to |
| `00:01:49.360 - 00:01:50.870` | whatever it was doing before it was |
| `00:01:50.880 - 00:01:52.789` | interrupted |
| `00:01:52.799 - 00:01:55.510` | for disks typically they're |
| `00:01:55.520 - 00:01:58.069` | busy doing a read or write operation |
| `00:01:58.079 - 00:02:00.230` | which takes a relatively long time and |
| `00:02:00.240 - 00:02:02.950` | when the operation finishes uh the disk |
| `00:02:02.960 - 00:02:05.429` | will then send an interrupt and so some |
| `00:02:05.439 - 00:02:07.190` | core will need to be |
| `00:02:07.200 - 00:02:09.669` | interrupted and that will cause a trap |
| `00:02:09.679 - 00:02:12.309` | in the core and that core will then talk |
| `00:02:12.319 - 00:02:14.070` | to the disk directly |
| `00:02:14.080 - 00:02:16.790` | and perhaps wake up some waiting threads |
| `00:02:16.800 - 00:02:18.869` | that we're waiting on the operation to |
| `00:02:18.879 - 00:02:19.990` | complete |
| `00:02:20.000 - 00:02:23.270` | and it may also start that disk on |
| `00:02:23.280 - 00:02:24.949` | another operation |
| `00:02:24.959 - 00:02:26.550` | and then go back to doing whatever it |
| `00:02:26.560 - 00:02:28.229` | was doing so |
| `00:02:28.239 - 00:02:30.550` | again when a device needs attention it |
| `00:02:30.560 - 00:02:32.470` | sends an interrupt the interrupt is |
| `00:02:32.480 - 00:02:34.390` | routed to one of the cores |
| `00:02:34.400 - 00:02:37.270` | that causes a trap |
| `00:02:37.280 - 00:02:39.910` | whatever their core is doing will |
| `00:02:39.920 - 00:02:42.390` | be suspended and that core will then run |
| `00:02:42.400 - 00:02:44.309` | an interrupt handler which is some code |
| `00:02:44.319 - 00:02:45.430` | that will |
| `00:02:45.440 - 00:02:47.350` | talk directly to the device so it will |
| `00:02:47.360 - 00:02:49.589` | go around the plate |
| `00:02:49.599 - 00:02:51.350` | and so the question is how does the |
| `00:02:51.360 - 00:02:53.750` | click route the individual interrupts to |
| `00:02:53.760 - 00:02:56.550` | the various cores |
| `00:02:56.560 - 00:02:59.270` | we have to assume that not all cores can |
| `00:02:59.280 - 00:03:01.110` | deal with all devices |
| `00:03:01.120 - 00:03:02.630` | for example there might not be a direct |
| `00:03:02.640 - 00:03:05.190` | connection between some devices and some |
| `00:03:05.200 - 00:03:07.589` | cores in a large system so the first |
| `00:03:07.599 - 00:03:09.509` | thing we have inside the click shown |
| `00:03:09.519 - 00:03:10.710` | here in red |
| `00:03:10.720 - 00:03:15.030` | is a large enable matrix and this is a |
| `00:03:15.040 - 00:03:16.790` | matrix of bits |
| `00:03:16.800 - 00:03:19.589` | and it shows which devices can send |
| `00:03:19.599 - 00:03:22.309` | interrupts to which cores so for example |
| `00:03:22.319 - 00:03:24.390` | this keyboard here can send an interrupt |
| `00:03:24.400 - 00:03:26.630` | to either this core or this core but not |
| `00:03:26.640 - 00:03:29.030` | this core and so on |
| `00:03:29.040 - 00:03:29.830` | this |
| `00:03:29.840 - 00:03:32.390` | big enable matrix is generally set up |
| `00:03:32.400 - 00:03:34.630` | during the initialization phase and |
| `00:03:34.640 - 00:03:38.149` | won't change |
| `00:03:38.159 - 00:03:39.509` | now the click |
| `00:03:39.519 - 00:03:42.229` | is connected along with the io devices |
| `00:03:42.239 - 00:03:44.710` | and the cores and the main memory |
| `00:03:44.720 - 00:03:47.270` | to some other interconnects |
| `00:03:47.280 - 00:03:49.190` | here i've shown the cores and main |
| `00:03:49.200 - 00:03:51.430` | memory and typically there's a bus down |
| `00:03:51.440 - 00:03:54.550` | here so if a core wants to read or write |
| `00:03:54.560 - 00:03:57.110` | to main memory it sends a request on |
| `00:03:57.120 - 00:03:58.229` | this bus |
| `00:03:58.239 - 00:04:00.229` | and data is sent either to the main |
| `00:04:00.239 - 00:04:03.429` | memory or to the core |
| `00:04:03.439 - 00:04:04.710` | if the |
| `00:04:04.720 - 00:04:06.949` | core wants to |
| `00:04:06.959 - 00:04:09.830` | manipulate a particular i o device it |
| `00:04:09.840 - 00:04:11.830` | generally uses memory mapped i o |
| `00:04:11.840 - 00:04:15.110` | registers so these devices are each |
| `00:04:15.120 - 00:04:17.590` | mapped into some addresses into the |
| `00:04:17.600 - 00:04:20.229` | physical memory address space |
| `00:04:20.239 - 00:04:22.150` | and for this core to |
| `00:04:22.160 - 00:04:23.749` | communicate with this disk it will |
| `00:04:23.759 - 00:04:26.390` | either read or |
| `00:04:26.400 - 00:04:28.469` | i should show read this way or we'll |
| `00:04:28.479 - 00:04:30.070` | write data to the memory mapped |
| `00:04:30.080 - 00:04:32.150` | registers |
| `00:04:32.160 - 00:04:36.150` | up above i've shown the interrupt lines |
| `00:04:36.160 - 00:04:39.030` | and those are essentially wires that are |
| `00:04:39.040 - 00:04:41.830` | used to transmit the interrupt so when |
| `00:04:41.840 - 00:04:43.590` | an interrupt occurs the signal will go |
| `00:04:43.600 - 00:04:45.749` | over the wire to the click |
| `00:04:45.759 - 00:04:48.150` | and the click will then notify one or |
| `00:04:48.160 - 00:04:50.150` | more cores that some device is |
| `00:04:50.160 - 00:04:51.350` | requesting |
| `00:04:51.360 - 00:04:54.550` | attention |
| `00:04:54.560 - 00:04:58.070` | the click itself is also memory mapped |
| `00:04:58.080 - 00:05:00.070` | so for example to set it up during the |
| `00:05:00.080 - 00:05:02.629` | initialization phase to to write in that |
| `00:05:02.639 - 00:05:04.230` | enabled matrix |
| `00:05:04.240 - 00:05:06.870` | there are registers inside the click so |
| `00:05:06.880 - 00:05:08.710` | one of the cores during initialization |
| `00:05:08.720 - 00:05:11.029` | will do writes to these memory mapped |
| `00:05:11.039 - 00:05:12.790` | registers in the click |
| `00:05:12.800 - 00:05:13.909` | also |
| `00:05:13.919 - 00:05:16.230` | the communication to the click |
| `00:05:16.240 - 00:05:18.390` | to indicate which interrupts are |
| `00:05:18.400 - 00:05:20.790` | accepted and when the trap handler is |
| `00:05:20.800 - 00:05:21.749` | complete |
| `00:05:21.759 - 00:05:25.510` | are done using memory mapped references |
| `00:05:25.520 - 00:05:28.390` | now some devices will be integrated on |
| `00:05:28.400 - 00:05:30.310` | the same chip as the cores and others |
| `00:05:30.320 - 00:05:31.350` | will be |
| `00:05:31.360 - 00:05:33.270` | external devices |
| `00:05:33.280 - 00:05:35.189` | typically in a processor you'll have |
| `00:05:35.199 - 00:05:37.270` | several cores maybe four cores on one |
| `00:05:37.280 - 00:05:40.629` | ship and the click will likely be |
| `00:05:40.639 - 00:05:42.710` | integrated on the same |
| `00:05:42.720 - 00:05:46.070` | circuit chip as the cores |
| `00:05:46.080 - 00:05:47.430` | in the old days it might have been a |
| `00:05:47.440 - 00:05:50.070` | separate uh chip but then we didn't have |
| `00:05:50.080 - 00:05:52.790` | multiple cores on a single chip either |
| `00:05:52.800 - 00:05:54.310` | some of the devices |
| `00:05:54.320 - 00:05:56.390` | may be integrated on the same circuit |
| `00:05:56.400 - 00:05:58.550` | chip as the cores and the click |
| `00:05:58.560 - 00:06:00.710` | and others may be external |
| `00:06:00.720 - 00:06:03.909` | for example a uart device is almost |
| `00:06:03.919 - 00:06:05.350` | always going to be integrated on the |
| `00:06:05.360 - 00:06:08.309` | same chip as your cores |
| `00:06:08.319 - 00:06:10.629` | but other devices like disks and |
| `00:06:10.639 - 00:06:13.110` | obviously a keyboard are not going to be |
| `00:06:13.120 - 00:06:15.670` | on the same chip as the cores |
| `00:06:15.680 - 00:06:18.070` | the main memory uh may or may not be on |
| `00:06:18.080 - 00:06:21.029` | the same chip |
| `00:06:21.039 - 00:06:22.309` | here's another view of the platform |
| `00:06:22.319 - 00:06:23.990` | level interrupt controller |
| `00:06:24.000 - 00:06:25.990` | i've shown the cores over here and the |
| `00:06:26.000 - 00:06:27.749` | devices over here |
| `00:06:27.759 - 00:06:30.710` | these arrows represent wires whereby a |
| `00:06:30.720 - 00:06:33.430` | device can transmit a signal to the |
| `00:06:33.440 - 00:06:35.350` | click to indicate that it |
| `00:06:35.360 - 00:06:37.430` | needs attention in other words when an |
| `00:06:37.440 - 00:06:40.070` | interrupt is signaled by the device a |
| `00:06:40.080 - 00:06:41.830` | signal will go over the wire and the |
| `00:06:41.840 - 00:06:43.430` | first thing it encounters inside the |
| `00:06:43.440 - 00:06:46.070` | click is something called a gateway |
| `00:06:46.080 - 00:06:47.670` | and the gateway could just be thought of |
| `00:06:47.680 - 00:06:49.110` | as a single bit |
| `00:06:49.120 - 00:06:51.430` | when an interrupt arrives |
| `00:06:51.440 - 00:06:54.309` | uh a bit in the gateway is set to |
| `00:06:54.319 - 00:06:56.150` | remember that that interrupt has |
| `00:06:56.160 - 00:06:57.110` | happened |
| `00:06:57.120 - 00:06:59.189` | and that's called the source interrupt |
| `00:06:59.199 - 00:07:02.469` | pending bit and at some point that bit |
| `00:07:02.479 - 00:07:04.870` | will get cleared but |
| `00:07:04.880 - 00:07:06.550` | while the bit is set it means there is |
| `00:07:06.560 - 00:07:08.950` | an interrupt pending and it need and the |
| `00:07:08.960 - 00:07:10.629` | click will at that point |
| `00:07:10.639 - 00:07:12.309` | channel it to one of the core one or |
| `00:07:12.319 - 00:07:13.909` | more of the cores |
| `00:07:13.919 - 00:07:17.270` | now the signal coming in can either be |
| `00:07:17.280 - 00:07:19.510` | level sensitive or edge triggered |
| `00:07:19.520 - 00:07:21.189` | perhaps you already know these terms but |
| `00:07:21.199 - 00:07:23.430` | i'll discuss them in a second |
| `00:07:23.440 - 00:07:24.790` | but first i want to talk about what |
| `00:07:24.800 - 00:07:27.990` | happens uh when an interrupt occurs |
| `00:07:28.000 - 00:07:30.790` | okay so when an interrupt occurs the |
| `00:07:30.800 - 00:07:34.070` | source interrupt pending bit becomes one |
| `00:07:34.080 - 00:07:36.710` | and at that point the click will |
| `00:07:36.720 - 00:07:39.830` | notify all of the cores that are enabled |
| `00:07:39.840 - 00:07:42.070` | for that particular device |
| `00:07:42.080 - 00:07:43.110` | so |
| `00:07:43.120 - 00:07:47.589` | the matrix of all the enabled cores |
| `00:07:47.599 - 00:07:49.430` | will be consulted to determine which |
| `00:07:49.440 - 00:07:50.469` | cores |
| `00:07:50.479 - 00:07:53.270` | are going to be notified here's the |
| `00:07:53.280 - 00:07:55.110` | matrix so for example when an interrupt |
| `00:07:55.120 - 00:07:56.469` | comes in for |
| `00:07:56.479 - 00:07:57.830` | the keyboard |
| `00:07:57.840 - 00:07:59.990` | these two cores will be notified but not |
| `00:08:00.000 - 00:08:02.469` | this one |
| `00:08:02.479 - 00:08:04.390` | what does that notification mean well it |
| `00:08:04.400 - 00:08:06.790` | means an external interrupt will be |
| `00:08:06.800 - 00:08:08.790` | raised in that core |
| `00:08:08.800 - 00:08:11.029` | now remember with risk 5 |
| `00:08:11.039 - 00:08:12.950` | a core can deal with three kinds of |
| `00:08:12.960 - 00:08:15.350` | asynchronous interrupts one is a timer |
| `00:08:15.360 - 00:08:17.189` | interrupt one is what's called a |
| `00:08:17.199 - 00:08:19.430` | software interrupt and the third is an |
| `00:08:19.440 - 00:08:21.189` | external interrupt so the click will |
| `00:08:21.199 - 00:08:23.510` | cause an external interrupt to become |
| `00:08:23.520 - 00:08:25.510` | pending in that core |
| `00:08:25.520 - 00:08:27.830` | if that core has interrupts enabled then |
| `00:08:27.840 - 00:08:29.909` | a trap will occur immediately |
| `00:08:29.919 - 00:08:31.830` | but if interrupts are disabled then the |
| `00:08:31.840 - 00:08:34.070` | trap can't occur at that time |
| `00:08:34.080 - 00:08:36.630` | but when the trap occurs |
| `00:08:36.640 - 00:08:39.430` | the trap processing will invoke a trap |
| `00:08:39.440 - 00:08:42.149` | handler routine so the trap handler code |
| `00:08:42.159 - 00:08:44.310` | will start executing and the first thing |
| `00:08:44.320 - 00:08:47.509` | it will do presumably is to |
| `00:08:47.519 - 00:08:49.829` | interact with the click to |
| `00:08:49.839 - 00:08:51.910` | claim the interrupt and this claim |
| `00:08:51.920 - 00:08:55.030` | operation basically is the code telling |
| `00:08:55.040 - 00:08:55.829` | the |
| `00:08:55.839 - 00:08:58.310` | click that hey i want that particular |
| `00:08:58.320 - 00:09:00.310` | interrupt i accept it i've got it i'm |
| `00:09:00.320 - 00:09:01.509` | claiming it |
| `00:09:01.519 - 00:09:02.550` | uh |
| `00:09:02.560 - 00:09:04.630` | as far as the other course that you told |
| `00:09:04.640 - 00:09:06.550` | about this particular interrupt they |
| `00:09:06.560 - 00:09:08.710` | don't get it okay and this claim |
| `00:09:08.720 - 00:09:10.230` | operation is done |
| `00:09:10.240 - 00:09:12.230` | by reading a memory mapped register in |
| `00:09:12.240 - 00:09:15.190` | the click so the trap handler will do a |
| `00:09:15.200 - 00:09:16.949` | load from some |
| `00:09:16.959 - 00:09:20.630` | word in the plix address space |
| `00:09:20.640 - 00:09:23.110` | and that click will then return |
| `00:09:23.120 - 00:09:26.070` | a particular value for that memory load |
| `00:09:26.080 - 00:09:28.790` | okay the click will return the id of the |
| `00:09:28.800 - 00:09:31.110` | interrupting device as a result of this |
| `00:09:31.120 - 00:09:32.790` | claim operation |
| `00:09:32.800 - 00:09:35.350` | and we'll also at that point the clear |
| `00:09:35.360 - 00:09:36.949` | the source interrupt pending bit because |
| `00:09:36.959 - 00:09:39.269` | the interrupt is being taken care of |
| `00:09:39.279 - 00:09:41.190` | might clear it later but that's |
| `00:09:41.200 - 00:09:43.509` | details |
| `00:09:43.519 - 00:09:45.110` | what if there are multiple cores that |
| `00:09:45.120 - 00:09:47.670` | are enabled only one should get the |
| `00:09:47.680 - 00:09:50.630` | interrupt okay so only one claim |
| `00:09:50.640 - 00:09:52.389` | will return the id of the interrupting |
| `00:09:52.399 - 00:09:53.430` | device |
| `00:09:53.440 - 00:09:56.150` | the click will make sure that the others |
| `00:09:56.160 - 00:09:58.230` | get a zero when they read this memory |
| `00:09:58.240 - 00:09:59.910` | map register |
| `00:09:59.920 - 00:10:02.150` | so when they get a zero they'll say oh i |
| `00:10:02.160 - 00:10:03.750` | didn't get it okay i can go back to |
| `00:10:03.760 - 00:10:05.350` | doing whatever i was doing |
| `00:10:05.360 - 00:10:07.829` | whereas the handler code in the core |
| `00:10:07.839 - 00:10:08.949` | that actually |
| `00:10:08.959 - 00:10:10.870` | got the id |
| `00:10:10.880 - 00:10:11.670` | will |
| `00:10:11.680 - 00:10:13.670` | then go on to communicate with the |
| `00:10:13.680 - 00:10:15.670` | device and take care of whatever |
| `00:10:15.680 - 00:10:17.670` | operation is required |
| `00:10:17.680 - 00:10:19.910` | so at that point the handler code will |
| `00:10:19.920 - 00:10:22.870` | run and deal with the device and then at |
| `00:10:22.880 - 00:10:25.190` | some point at some future time the |
| `00:10:25.200 - 00:10:28.150` | handler code is done and it can signal |
| `00:10:28.160 - 00:10:29.990` | interrupt completion that's another |
| `00:10:30.000 - 00:10:31.190` | operation |
| `00:10:31.200 - 00:10:33.350` | that it does by communicating to the |
| `00:10:33.360 - 00:10:34.630` | click |
| `00:10:34.640 - 00:10:36.470` | and once |
| `00:10:36.480 - 00:10:38.949` | that interrupt is marked as completed |
| `00:10:38.959 - 00:10:40.710` | then the next interrupt from that device |
| `00:10:40.720 - 00:10:41.509` | can |
| `00:10:41.519 - 00:10:42.710` | occur |
| `00:10:42.720 - 00:10:45.350` | so the interrupt completion operation is |
| `00:10:45.360 - 00:10:47.110` | done by |
| `00:10:47.120 - 00:10:49.590` | the core writing to a memory mapped |
| `00:10:49.600 - 00:10:53.590` | register in the click |
| `00:10:53.600 - 00:10:55.670` | and then once the interrupt completion |
| `00:10:55.680 - 00:10:57.430` | is done the click will look at the |
| `00:10:57.440 - 00:10:59.269` | source interrupt pending bit and see |
| `00:10:59.279 - 00:11:01.750` | whether there is another pending uh |
| `00:11:01.760 - 00:11:04.389` | interrupt from that same device |
| `00:11:04.399 - 00:11:06.470` | and if that device is requesting another |
| `00:11:06.480 - 00:11:08.310` | interrupt or or when |
| `00:11:08.320 - 00:11:09.910` | that device |
| `00:11:09.920 - 00:11:11.829` | is requesting another interrupt and the |
| `00:11:11.839 - 00:11:14.150` | gateway bit is set again then the click |
| `00:11:14.160 - 00:11:16.630` | will once again signal another interrupt |
| `00:11:16.640 - 00:11:20.069` | and the process will repeat |
| `00:11:20.079 - 00:11:21.670` | now i want to describe what level |
| `00:11:21.680 - 00:11:24.389` | sensitive and edge triggered mean for |
| `00:11:24.399 - 00:11:26.069` | those of you who are not electrical |
| `00:11:26.079 - 00:11:27.269` | engineers |
| `00:11:27.279 - 00:11:28.550` | i'm talking about the signal that |
| `00:11:28.560 - 00:11:30.630` | travels along this wire from the device |
| `00:11:30.640 - 00:11:33.910` | to the gateway |
| `00:11:33.920 - 00:11:36.710` | in the case of a level sensitive |
| `00:11:36.720 - 00:11:40.389` | signal we see that over time the wire is |
| `00:11:40.399 - 00:11:42.470` | low carrying a zero and then it rises at |
| `00:11:42.480 - 00:11:44.550` | some point and has a one and then later |
| `00:11:44.560 - 00:11:46.069` | falls |
| `00:11:46.079 - 00:11:48.790` | what the click will do is check the |
| `00:11:48.800 - 00:11:50.629` | value of that |
| `00:11:50.639 - 00:11:53.350` | wire if it's high it will cause an |
| `00:11:53.360 - 00:11:55.670` | interrupt when it checks okay |
| `00:11:55.680 - 00:11:56.949` | so whenever |
| `00:11:56.959 - 00:12:00.150` | the handler completes its operation it |
| `00:12:00.160 - 00:12:01.990` | will check this wire and if it's high it |
| `00:12:02.000 - 00:12:04.150` | will cause an interrupt to be sent to |
| `00:12:04.160 - 00:12:05.590` | one of the cores |
| `00:12:05.600 - 00:12:06.389` | the |
| `00:12:06.399 - 00:12:08.629` | handler will execute and at some point |
| `00:12:08.639 - 00:12:11.269` | will complete the operation we'll |
| `00:12:11.279 - 00:12:12.710` | complete the processing of that |
| `00:12:12.720 - 00:12:15.350` | interrupt and at that point the click |
| `00:12:15.360 - 00:12:19.110` | will check again to see whether the |
| `00:12:19.120 - 00:12:22.310` | wire is high or not and if it's |
| `00:12:22.320 - 00:12:23.269` | high |
| `00:12:23.279 - 00:12:25.190` | again or still high doesn't matter what |
| `00:12:25.200 - 00:12:27.190` | happened in the middle here but if it's |
| `00:12:27.200 - 00:12:28.949` | high at that point then the click will |
| `00:12:28.959 - 00:12:30.710` | signal another interrupt and then the |
| `00:12:30.720 - 00:12:33.750` | handler code will run and complete and |
| `00:12:33.760 - 00:12:35.829` | then once again the |
| `00:12:35.839 - 00:12:38.230` | click will check and in the third case |
| `00:12:38.240 - 00:12:40.550` | it finds out that it's low so no |
| `00:12:40.560 - 00:12:43.269` | interrupt is signaled at this time |
| `00:12:43.279 - 00:12:45.269` | now in the case of an edge triggered |
| `00:12:45.279 - 00:12:46.230` | signal |
| `00:12:46.240 - 00:12:48.069` | we have the signal going from low to |
| `00:12:48.079 - 00:12:49.990` | high and what the click is watching for |
| `00:12:50.000 - 00:12:53.110` | is that transition when it goes from low |
| `00:12:53.120 - 00:12:55.269` | to high it doesn't really care too much |
| `00:12:55.279 - 00:12:58.150` | about when it goes from high back to low |
| `00:12:58.160 - 00:13:00.230` | and the rising edge is what signals the |
| `00:13:00.240 - 00:13:01.430` | interrupt |
| `00:13:01.440 - 00:13:03.670` | and there are really two options or two |
| `00:13:03.680 - 00:13:05.509` | variations on the gateway |
| `00:13:05.519 - 00:13:08.069` | in one case the gateway has a single bit |
| `00:13:08.079 - 00:13:10.949` | and the rising edge will set that bit to |
| `00:13:10.959 - 00:13:11.829` | one |
| `00:13:11.839 - 00:13:14.230` | and that will then cause the interrupt |
| `00:13:14.240 - 00:13:17.190` | and that bit will stay set at one until |
| `00:13:17.200 - 00:13:18.790` | the handler completes |
| `00:13:18.800 - 00:13:21.590` | and if there are additional rising edges |
| `00:13:21.600 - 00:13:25.190` | those will be ignored but at some point |
| `00:13:25.200 - 00:13:28.710` | the handler will signal that the |
| `00:13:28.720 - 00:13:31.110` | processing is complete and at that point |
| `00:13:31.120 - 00:13:33.350` | the click will |
| `00:13:33.360 - 00:13:35.590` | clear the bit |
| `00:13:35.600 - 00:13:37.829` | and then the next edge can set the bit |
| `00:13:37.839 - 00:13:40.230` | and cause another interrupt |
| `00:13:40.240 - 00:13:42.069` | in the second option the gateway |
| `00:13:42.079 - 00:13:44.790` | contains a counter and the idea with |
| `00:13:44.800 - 00:13:47.269` | this counter is that each rising edge |
| `00:13:47.279 - 00:13:49.430` | will increment the counter so we're |
| `00:13:49.440 - 00:13:51.670` | keeping a count of how many interrupt |
| `00:13:51.680 - 00:13:55.430` | signals have come in from the device |
| `00:13:55.440 - 00:13:58.310` | and then each time the handler |
| `00:13:58.320 - 00:13:59.829` | claims a |
| `00:13:59.839 - 00:14:02.629` | an interrupt then the click will |
| `00:14:02.639 - 00:14:04.629` | decrement the counter |
| `00:14:04.639 - 00:14:07.110` | and then once the handler indicates that |
| `00:14:07.120 - 00:14:09.189` | the handling of the interrupt is |
| `00:14:09.199 - 00:14:10.310` | complete |
| `00:14:10.320 - 00:14:12.150` | the click will then take a look at the |
| `00:14:12.160 - 00:14:14.310` | counter and if it's still positive the |
| `00:14:14.320 - 00:14:16.230` | click will immediately signal another |
| `00:14:16.240 - 00:14:18.310` | interrupt otherwise it will just wait |
| `00:14:18.320 - 00:14:20.949` | until a rising edge causes a counter to |
| `00:14:20.959 - 00:14:23.910` | increment from zero to one |
| `00:14:23.920 - 00:14:25.590` | next let's get into some of the details |
| `00:14:25.600 - 00:14:27.590` | and complications that arise with the |
| `00:14:27.600 - 00:14:28.710` | plick |
| `00:14:28.720 - 00:14:30.629` | first of all devices are numbered |
| `00:14:30.639 - 00:14:33.910` | starting at 1 and going up to 1023. |
| `00:14:33.920 - 00:14:36.710` | the number zero is used for no device at |
| `00:14:36.720 - 00:14:38.230` | all |
| `00:14:38.240 - 00:14:41.350` | next each core can contain more than one |
| `00:14:41.360 - 00:14:44.710` | heart or hardware thread |
| `00:14:44.720 - 00:14:47.990` | the core might be a super scalar core or |
| `00:14:48.000 - 00:14:49.670` | sometimes we say simultaneous |
| `00:14:49.680 - 00:14:51.110` | multi-threading |
| `00:14:51.120 - 00:14:54.069` | and the idea is the core is running two |
| `00:14:54.079 - 00:14:57.189` | threads simultaneously |
| `00:14:57.199 - 00:15:00.710` | another thing is the core in risk 5 can |
| `00:15:00.720 - 00:15:03.990` | be executing in machine mode supervisor |
| `00:15:04.000 - 00:15:06.150` | mode user mode |
| `00:15:06.160 - 00:15:07.430` | the |
| `00:15:07.440 - 00:15:09.590` | spec for the click also talks about a |
| `00:15:09.600 - 00:15:11.269` | hypervisor mode |
| `00:15:11.279 - 00:15:13.750` | but the interrupt will be sent to a |
| `00:15:13.760 - 00:15:17.430` | particular mode okay so it may be sent |
| `00:15:17.440 - 00:15:20.790` | to supervisor mode or it may be sent to |
| `00:15:20.800 - 00:15:22.230` | machine mode |
| `00:15:22.240 - 00:15:23.030` | okay |
| `00:15:23.040 - 00:15:25.990` | and we call these things targets so you |
| `00:15:26.000 - 00:15:26.710` | know |
| `00:15:26.720 - 00:15:29.749` | each heart is a separate target and each |
| `00:15:29.759 - 00:15:32.230` | mode is a separate target within that |
| `00:15:32.240 - 00:15:35.590` | and the specification can deal with up |
| `00:15:35.600 - 00:15:36.629` | to |
| `00:15:36.639 - 00:15:39.430` | 15 782 |
| `00:15:39.440 - 00:15:43.829` | different targets numbered with numbers |
| `00:15:43.839 - 00:15:44.949` | now |
| `00:15:44.959 - 00:15:47.350` | the specification goes up to these |
| `00:15:47.360 - 00:15:52.069` | numbers 1023 and 15 781 although a |
| `00:15:52.079 - 00:15:53.990` | particular implementation may not be |
| `00:15:54.000 - 00:15:57.430` | able to handle quite that many devices |
| `00:15:57.440 - 00:16:00.310` | or targets |
| `00:16:00.320 - 00:16:02.470` | okay next each |
| `00:16:02.480 - 00:16:04.230` | external device that can generate an |
| `00:16:04.240 - 00:16:07.509` | interrupt is assigned a priority some |
| `00:16:07.519 - 00:16:09.829` | are high priority devices and some are |
| `00:16:09.839 - 00:16:11.990` | low priority devices |
| `00:16:12.000 - 00:16:14.870` | we want to assign a high priority number |
| `00:16:14.880 - 00:16:17.590` | a large value to a device that we want |
| `00:16:17.600 - 00:16:18.790` | to have |
| `00:16:18.800 - 00:16:21.030` | serviced immediately |
| `00:16:21.040 - 00:16:23.509` | and others are very low priority |
| `00:16:23.519 - 00:16:25.990` | typically slow devices have low priority |
| `00:16:26.000 - 00:16:28.230` | you know a keyboard for example doesn't |
| `00:16:28.240 - 00:16:31.030` | need a super high priority because |
| `00:16:31.040 - 00:16:33.749` | people just can't type very fast but a |
| `00:16:33.759 - 00:16:35.430` | rotating disk |
| `00:16:35.440 - 00:16:37.749` | might need a very high priority if you |
| `00:16:37.759 - 00:16:40.629` | are going to try to start another read |
| `00:16:40.639 - 00:16:42.949` | operation before the disc spins all the |
| `00:16:42.959 - 00:16:45.269` | way around if you don't start the |
| `00:16:45.279 - 00:16:47.430` | operation soon enough |
| `00:16:47.440 - 00:16:49.110` | on the same track the disc may have to |
| `00:16:49.120 - 00:16:50.870` | do a full rotation to get back to the |
| `00:16:50.880 - 00:16:53.590` | right spot |
| `00:16:53.600 - 00:16:55.990` | each core well i really should say |
| `00:16:56.000 - 00:16:58.710` | target so each one of these targets |
| `00:16:58.720 - 00:17:01.829` | is assigned a threshold okay and here's |
| `00:17:01.839 - 00:17:03.030` | the key |
| `00:17:03.040 - 00:17:05.909` | a device will only interrupt a core |
| `00:17:05.919 - 00:17:08.309` | if the device priority exceeds the |
| `00:17:08.319 - 00:17:11.510` | course threshold |
| `00:17:11.520 - 00:17:13.510` | next let's talk about how a core |
| `00:17:13.520 - 00:17:15.590` | controls the platform level interrupt |
| `00:17:15.600 - 00:17:16.870` | controller |
| `00:17:16.880 - 00:17:18.470` | it does it by |
| `00:17:18.480 - 00:17:20.949` | reading and writing to memory mapped i o |
| `00:17:20.959 - 00:17:22.150` | registers |
| `00:17:22.160 - 00:17:24.949` | so the click will occupy a number of |
| `00:17:24.959 - 00:17:28.230` | bytes in the physical address space of |
| `00:17:28.240 - 00:17:32.150` | the processor and a core can do loads |
| `00:17:32.160 - 00:17:34.950` | and stores to addresses within that |
| `00:17:34.960 - 00:17:37.909` | range of of memory so-called memory |
| `00:17:37.919 - 00:17:40.710` | mapped registers to |
| `00:17:40.720 - 00:17:42.950` | send or receive data to and from the |
| `00:17:42.960 - 00:17:44.870` | click |
| `00:17:44.880 - 00:17:46.830` | first of all we have |
| `00:17:46.840 - 00:17:50.230` | 123 words of four bytes each that are |
| `00:17:50.240 - 00:17:54.150` | used in to store the device priorities |
| `00:17:54.160 - 00:17:56.870` | these are initialized at startup |
| `00:17:56.880 - 00:17:59.430` | and we can set the priorities of all the |
| `00:17:59.440 - 00:18:02.630` | devices by writing or doing a store into |
| `00:18:02.640 - 00:18:04.950` | these particular locations |
| `00:18:04.960 - 00:18:07.350` | we also have the priority thresholds for |
| `00:18:07.360 - 00:18:09.270` | each of the targets |
| `00:18:09.280 - 00:18:12.549` | so we have a bunch of words one for each |
| `00:18:12.559 - 00:18:15.669` | possible target and then we also have |
| `00:18:15.679 - 00:18:17.950` | that big bit matrix |
| `00:18:17.960 - 00:18:21.630` | 1023 devices times |
| `00:18:21.640 - 00:18:24.950` | 15782 targets that's a bunch of bits |
| `00:18:24.960 - 00:18:27.430` | so during initialization |
| `00:18:27.440 - 00:18:28.870` | these |
| `00:18:28.880 - 00:18:31.110` | memory locations will be written into to |
| `00:18:31.120 - 00:18:33.669` | basically configure we might even say |
| `00:18:33.679 - 00:18:35.990` | program the platform level interrupt |
| `00:18:36.000 - 00:18:38.070` | controller although i don't really like |
| `00:18:38.080 - 00:18:39.750` | to use the word program |
| `00:18:39.760 - 00:18:41.029` | unless there's actual code being |
| `00:18:41.039 - 00:18:43.350` | executed and the platform level |
| `00:18:43.360 - 00:18:45.029` | interrupt controller is not executing |
| `00:18:45.039 - 00:18:46.710` | any code by any stretch of the |
| `00:18:46.720 - 00:18:48.630` | imagination |
| `00:18:48.640 - 00:18:49.750` | then |
| `00:18:49.760 - 00:18:53.110` | we have the pending bits so this is a |
| `00:18:53.120 - 00:18:56.150` | allows a core to query to see which |
| `00:18:56.160 - 00:19:00.070` | devices currently have a pending bit |
| `00:19:00.080 - 00:19:01.990` | and then finally we have and this is the |
| `00:19:02.000 - 00:19:03.110` | probably the most |
| `00:19:03.120 - 00:19:04.390` | important one |
| `00:19:04.400 - 00:19:06.390` | these are the claim words |
| `00:19:06.400 - 00:19:10.230` | so for each target there is a word and |
| `00:19:10.240 - 00:19:12.470` | you can either load or store to this |
| `00:19:12.480 - 00:19:13.909` | location |
| `00:19:13.919 - 00:19:14.789` | and |
| `00:19:14.799 - 00:19:18.310` | what the core will do is a load to |
| `00:19:18.320 - 00:19:20.070` | the addresses associated with that |
| `00:19:20.080 - 00:19:23.430` | target to claim an interrupt so when an |
| `00:19:23.440 - 00:19:25.350` | external interrupt is signaled on a |
| `00:19:25.360 - 00:19:29.350` | particular core that core will load from |
| `00:19:29.360 - 00:19:30.150` | the |
| `00:19:30.160 - 00:19:33.029` | word that corresponds to it a 4 byte |
| `00:19:33.039 - 00:19:36.070` | value and that will be the id of the |
| `00:19:36.080 - 00:19:38.230` | device that is requesting an interrupt |
| `00:19:38.240 - 00:19:41.110` | or it might be zero if some other core |
| `00:19:41.120 - 00:19:44.390` | got to that interrupt first |
| `00:19:44.400 - 00:19:48.310` | and this same word is also used to |
| `00:19:48.320 - 00:19:50.870` | do the complete operation so that's done |
| `00:19:50.880 - 00:19:53.909` | by storing into the word and in that |
| `00:19:53.919 - 00:19:55.669` | case we store the |
| `00:19:55.679 - 00:19:57.830` | id of the device whose interrupt we have |
| `00:19:57.840 - 00:19:59.350` | just completed |
| `00:19:59.360 - 00:20:01.909` | a core might be uh handling several |
| `00:20:01.919 - 00:20:04.630` | interrupts simultaneously and it can |
| `00:20:04.640 - 00:20:06.230` | claim several and complete several |
| `00:20:06.240 - 00:20:08.230` | independently |
| `00:20:08.240 - 00:20:12.549` | okay so um all told these memory mapped |
| `00:20:12.559 - 00:20:13.750` | locations |
| `00:20:13.760 - 00:20:16.070` | uh are mapped out into |
| `00:20:16.080 - 00:20:18.630` | a range of 64 megabytes it's really |
| `00:20:18.640 - 00:20:20.950` | quite large of the physical address |
| `00:20:20.960 - 00:20:23.990` | space there are a lot of reserved bits |
| `00:20:24.000 - 00:20:26.549` | or unused bits and the actual mapping |
| `00:20:26.559 - 00:20:28.950` | i'm not going to go into too too much in |
| `00:20:28.960 - 00:20:31.270` | this video |
| `00:20:31.280 - 00:20:32.870` | next let's look at how the platform |
| `00:20:32.880 - 00:20:34.310` | level interrupt controller is |
| `00:20:34.320 - 00:20:36.710` | implemented in our system |
| `00:20:36.720 - 00:20:39.909` | we're talking about the xv6 kernel and |
| `00:20:39.919 - 00:20:42.310` | that will be run on kimu which is an |
| `00:20:42.320 - 00:20:43.510` | emulator |
| `00:20:43.520 - 00:20:46.230` | so the click is not actually implemented |
| `00:20:46.240 - 00:20:48.950` | physically it's simply virtually |
| `00:20:48.960 - 00:20:52.070` | emulated by chemo |
| `00:20:52.080 - 00:20:54.630` | the number of cores in xv6 is determined |
| `00:20:54.640 - 00:20:57.190` | by this constant in cpu which happens to |
| `00:20:57.200 - 00:21:00.549` | be eight so we have eight cores |
| `00:21:00.559 - 00:21:01.430` | and |
| `00:21:01.440 - 00:21:03.190` | kimu is only going to be able to send |
| `00:21:03.200 - 00:21:05.669` | the interrupts to machine mode or |
| `00:21:05.679 - 00:21:07.750` | supervisor mode |
| `00:21:07.760 - 00:21:09.430` | and so with two different modes and |
| `00:21:09.440 - 00:21:11.110` | eight cores we have |
| `00:21:11.120 - 00:21:13.830` | 16 different targets and they are |
| `00:21:13.840 - 00:21:16.870` | numbered as shown here |
| `00:21:16.880 - 00:21:20.390` | in our implementation of xv6 we are not |
| `00:21:20.400 - 00:21:22.070` | going to use machine |
| `00:21:22.080 - 00:21:24.230` | interrupts at all all the interrupts |
| `00:21:24.240 - 00:21:27.430` | will happen in supervisor mode |
| `00:21:27.440 - 00:21:29.830` | chemo in |
| `00:21:29.840 - 00:21:33.270` | two devices one is the virtual i o disk |
| `00:21:33.280 - 00:21:36.470` | device and the other is the uart device |
| `00:21:36.480 - 00:21:39.029` | and chemo will give these numbers one |
| `00:21:39.039 - 00:21:41.350` | and ten |
| `00:21:41.360 - 00:21:44.070` | so during the initialization phase the |
| `00:21:44.080 - 00:21:46.470` | code will set the priority of both these |
| `00:21:46.480 - 00:21:48.310` | devices to one |
| `00:21:48.320 - 00:21:51.029` | and then for each core it will set the |
| `00:21:51.039 - 00:21:53.350` | course threshold to zero so the |
| `00:21:53.360 - 00:21:55.750` | threshold won't stop any particular |
| `00:21:55.760 - 00:21:57.510` | interrupt from getting through |
| `00:21:57.520 - 00:22:00.390` | and we will als also enable the bit set |
| `00:22:00.400 - 00:22:03.029` | the enable bit for both the devices |
| `00:22:03.039 - 00:22:05.270` | so this means that any device can |
| `00:22:05.280 - 00:22:07.590` | interrupt any core |
| `00:22:07.600 - 00:22:10.470` | or more precisely when a device |
| `00:22:10.480 - 00:22:11.830` | is ready for an interrupt when it |
| `00:22:11.840 - 00:22:14.950` | signals an interrupt all cores will have |
| `00:22:14.960 - 00:22:16.870` | an interrupt signal |
| `00:22:16.880 - 00:22:19.510` | the benefit of signaling lots of cores |
| `00:22:19.520 - 00:22:22.149` | when you have an interrupt is that |
| `00:22:22.159 - 00:22:24.870` | the first core that is free will get to |
| `00:22:24.880 - 00:22:26.950` | it and take care of it and so it will |
| `00:22:26.960 - 00:22:29.350` | presumably get serviced more quickly if |
| `00:22:29.360 - 00:22:31.190` | other cores are busy and can't claim the |
| `00:22:31.200 - 00:22:33.669` | interrupt then they won't get it but the |
| `00:22:33.679 - 00:22:35.430` | one that can claim it first is the one |
| `00:22:35.440 - 00:22:38.789` | that we want to actually get it |
| `00:22:38.799 - 00:22:40.230` | okay now we're ready to take a look at |
| `00:22:40.240 - 00:22:43.110` | the code in xv6 and as you can see it's |
| `00:22:43.120 - 00:22:45.110` | pretty short we're looking at the code |
| `00:22:45.120 - 00:22:49.830` | in click dot c and we have four routines |
| `00:22:49.840 - 00:22:52.630` | we have two for initialization and then |
| `00:22:52.640 - 00:22:54.230` | one for the claim and one for the |
| `00:22:54.240 - 00:22:57.190` | complete operation |
| `00:22:57.200 - 00:23:00.230` | clickinet is executed only once by core |
| `00:23:00.240 - 00:23:03.430` | zero during startup and remember what we |
| `00:23:03.440 - 00:23:05.830` | need to do for initialization |
| `00:23:05.840 - 00:23:08.230` | we need to set the priority of both |
| `00:23:08.240 - 00:23:11.190` | devices to one so that's what's going on |
| `00:23:11.200 - 00:23:13.190` | here |
| `00:23:13.200 - 00:23:15.270` | click is the |
| `00:23:15.280 - 00:23:17.190` | address of the |
| `00:23:17.200 - 00:23:20.390` | plex memory map region and then we add |
| `00:23:20.400 - 00:23:23.510` | this to get to the particular |
| `00:23:23.520 - 00:23:26.390` | register for the priority for this |
| `00:23:26.400 - 00:23:28.230` | device and that device |
| `00:23:28.240 - 00:23:30.789` | and we just store a one into those |
| `00:23:30.799 - 00:23:32.230` | locations |
| `00:23:32.240 - 00:23:35.909` | and so that's done once by core zero and |
| `00:23:35.919 - 00:23:38.870` | clicking it heart is executed once for |
| `00:23:38.880 - 00:23:42.070` | each core so the first thing it does is |
| `00:23:42.080 - 00:23:44.549` | determine what core it's running on so |
| `00:23:44.559 - 00:23:46.789` | it gets the cpu id |
| `00:23:46.799 - 00:23:49.750` | and then we need to do two operations |
| `00:23:49.760 - 00:23:51.830` | we need to |
| `00:23:51.840 - 00:23:54.630` | and set the enable bit in the enable |
| `00:23:54.640 - 00:23:55.909` | matrix |
| `00:23:55.919 - 00:23:58.950` | to one for both of the devices for this |
| `00:23:58.960 - 00:24:01.029` | particular core and then we need to set |
| `00:24:01.039 - 00:24:03.830` | this course threshold to zero so that's |
| `00:24:03.840 - 00:24:06.549` | what's going on |
| `00:24:06.559 - 00:24:08.390` | here in the |
| `00:24:08.400 - 00:24:10.070` | we use this uh |
| `00:24:10.080 - 00:24:13.350` | macro click s enable |
| `00:24:13.360 - 00:24:15.990` | to get the address for the word we need |
| `00:24:16.000 - 00:24:16.789` | to |
| `00:24:16.799 - 00:24:20.549` | write to update the enable matrix so |
| `00:24:20.559 - 00:24:22.710` | what we're going to do is write into the |
| `00:24:22.720 - 00:24:26.470` | enable matrix for this particular core |
| `00:24:26.480 - 00:24:30.070` | and we're going to write into it |
| `00:24:30.080 - 00:24:32.789` | a word and |
| `00:24:32.799 - 00:24:35.510` | that word will contain two one bits |
| `00:24:35.520 - 00:24:37.510` | one in position ten |
| `00:24:37.520 - 00:24:41.350` | for the uart and one in position one for |
| `00:24:41.360 - 00:24:43.990` | the disk and so that will set the enable |
| `00:24:44.000 - 00:24:46.710` | bits so that these two devices are |
| `00:24:46.720 - 00:24:48.870` | allowed to |
| `00:24:48.880 - 00:24:50.149` | interrupt this |
| `00:24:50.159 - 00:24:52.870` | particular core so we write that |
| `00:24:52.880 - 00:24:53.909` | and then |
| `00:24:53.919 - 00:24:56.310` | we use this other macro here |
| `00:24:56.320 - 00:24:59.110` | to determine the memory mapped i o |
| `00:24:59.120 - 00:25:02.789` | address for the priority register and at |
| `00:25:02.799 - 00:25:05.590` | this point we store zero into that word |
| `00:25:05.600 - 00:25:07.990` | to set the priority threshold for this |
| `00:25:08.000 - 00:25:10.870` | particular core to be zero |
| `00:25:10.880 - 00:25:12.149` | so these two |
| `00:25:12.159 - 00:25:15.830` | macros click s enable and click |
| `00:25:15.840 - 00:25:18.390` | s priority are coming from |
| `00:25:18.400 - 00:25:20.870` | memory layout.h |
| `00:25:20.880 - 00:25:23.029` | and we can see them down here |
| `00:25:23.039 - 00:25:25.029` | click is the starting address for the |
| `00:25:25.039 - 00:25:28.789` | memory mapped region for the plick and |
| `00:25:28.799 - 00:25:29.750` | uh |
| `00:25:29.760 - 00:25:32.630` | here we have a number of macros that |
| `00:25:32.640 - 00:25:36.390` | aren't actually used in the code we only |
| `00:25:36.400 - 00:25:39.990` | use the s enable s priority and s claim |
| `00:25:40.000 - 00:25:42.549` | we don't use the machine mode |
| `00:25:42.559 - 00:25:45.990` | macros and so each one of these just |
| `00:25:46.000 - 00:25:48.950` | gives the address for this particular |
| `00:25:48.960 - 00:25:50.070` | course |
| `00:25:50.080 - 00:25:52.230` | register the enable register the |
| `00:25:52.240 - 00:25:57.190` | priority register and the claim register |
| `00:25:57.200 - 00:26:00.390` | by the way when i was looking at the |
| `00:26:00.400 - 00:26:02.630` | code that creates the virtual address |
| `00:26:02.640 - 00:26:05.909` | space for the kernel i noticed that we |
| `00:26:05.919 - 00:26:07.110` | only |
| `00:26:07.120 - 00:26:09.990` | memory map four megabytes bytes and not |
| `00:26:10.000 - 00:26:12.710` | 64 megabytes i think that's actually an |
| `00:26:12.720 - 00:26:14.390` | error in the code it doesn't cause any |
| `00:26:14.400 - 00:26:17.590` | harm but i wanted to point that out |
| `00:26:17.600 - 00:26:18.870` | okay so |
| `00:26:18.880 - 00:26:21.590` | we have these two functions |
| `00:26:21.600 - 00:26:23.350` | when a |
| `00:26:23.360 - 00:26:25.909` | handler for some interrupt |
| `00:26:25.919 - 00:26:27.269` | is activated |
| `00:26:27.279 - 00:26:30.789` | in the function device interrupt or dev |
| `00:26:30.799 - 00:26:31.669` | enter |
| `00:26:31.679 - 00:26:34.310` | will be called and it |
| `00:26:34.320 - 00:26:38.070` | will immediately invoke this click claim |
| `00:26:38.080 - 00:26:40.710` | and that basically asks the click which |
| `00:26:40.720 - 00:26:42.950` | device is interrupted and the click will |
| `00:26:42.960 - 00:26:46.470` | then return the number of that device |
| `00:26:46.480 - 00:26:48.870` | and so here we see that going on |
| `00:26:48.880 - 00:26:51.990` | click claim for this particular core is |
| `00:26:52.000 - 00:26:55.990` | the location of the claim register |
| `00:26:56.000 - 00:26:57.909` | and if the click |
| `00:26:57.919 - 00:26:58.870` | wants |
| `00:26:58.880 - 00:27:01.190` | the click will return the device id and |
| `00:27:01.200 - 00:27:03.430` | that will be saved here |
| `00:27:03.440 - 00:27:05.350` | if some other core |
| `00:27:05.360 - 00:27:08.950` | got to the click first and claimed the |
| `00:27:08.960 - 00:27:11.669` | interrupt before this particular core |
| `00:27:11.679 - 00:27:14.310` | then this particular load from that |
| `00:27:14.320 - 00:27:17.750` | memory mapped register will return zero |
| `00:27:17.760 - 00:27:21.110` | in the dev enter or the device interrupt |
| `00:27:21.120 - 00:27:23.029` | function if we get something that's |
| `00:27:23.039 - 00:27:25.350` | non-zero well we'll either get a 1 to |
| `00:27:25.360 - 00:27:28.549` | indicate it's the uart or 10 to indicate |
| `00:27:28.559 - 00:27:30.230` | it's the disk and then we'll call the |
| `00:27:30.240 - 00:27:31.830` | appropriate handler routine for that |
| `00:27:31.840 - 00:27:33.830` | device but if we get a zero we |
| `00:27:33.840 - 00:27:35.350` | immediately return |
| `00:27:35.360 - 00:27:39.190` | so one core will handle the uh interrupt |
| `00:27:39.200 - 00:27:41.110` | and other cores that get zero will |
| `00:27:41.120 - 00:27:42.630` | immediately return go back to doing |
| `00:27:42.640 - 00:27:45.510` | whatever they were doing |
| `00:27:45.520 - 00:27:48.549` | if we do service the device |
| `00:27:48.559 - 00:27:49.909` | that is if we get something that's |
| `00:27:49.919 - 00:27:53.190` | non-zero then the dev enter |
| `00:27:53.200 - 00:27:56.470` | the device interrupt function will then |
| `00:27:56.480 - 00:27:58.070` | end by calling the |
| `00:27:58.080 - 00:28:01.029` | click complete function click complete |
| `00:28:01.039 - 00:28:03.110` | will then store into the same location |
| `00:28:03.120 - 00:28:04.950` | right the same location |
| `00:28:04.960 - 00:28:07.590` | the code for the interrupt that it is |
| `00:28:07.600 - 00:28:09.510` | completing |
| `00:28:09.520 - 00:28:11.830` | okay that's it for the click um i hope |
| `00:28:11.840 - 00:28:13.669` | you found that interesting i will see |
| `00:28:13.679 - 00:28:17.159` | you in the next video |
