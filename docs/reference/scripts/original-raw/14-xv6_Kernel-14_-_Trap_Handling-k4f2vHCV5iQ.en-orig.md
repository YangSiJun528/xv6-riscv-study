# xv6 Kernel-14: Trap Handling

| Field | Value |
| --- | --- |
| Video ID | `k4f2vHCV5iQ` |
| URL | <https://www.youtube.com/watch?v=k4f2vHCV5iQ> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.520 - 00:00:01.829` | this video is part of a series on the |
| `00:00:01.839 - 00:00:04.070` | xv6 operating system |
| `00:00:04.080 - 00:00:06.630` | kernel in this image we're executing in |
| `00:00:06.640 - 00:00:09.790` | user code we get a trap and we execute |
| `00:00:09.800 - 00:00:11.749` | something in the kernel and then we |
| `00:00:11.759 - 00:00:14.030` | execute a system return instruction and |
| `00:00:14.040 - 00:00:15.990` | go back to user mode in this video I'm |
| `00:00:16.000 - 00:00:17.269` | going to talk about what happens when |
| `00:00:17.279 - 00:00:19.910` | we're in kernel mode I'll talk about the |
| `00:00:19.920 - 00:00:21.870` | data structures in particular and I'll |
| `00:00:21.880 - 00:00:26.310` | do a code walk through of the proc |
| `00:00:26.320 - 00:00:29.269` | file so let's uh begin with this uh |
| `00:00:29.279 - 00:00:31.349` | thing that I call the road map and it |
| `00:00:31.359 - 00:00:33.430` | shows everything that happens between |
| `00:00:33.440 - 00:00:37.110` | the Trap and the SR |
| `00:00:37.120 - 00:00:39.229` | instruction and let's go through this |
| `00:00:39.239 - 00:00:42.270` | more carefully here uh the Trap could be |
| `00:00:42.280 - 00:00:44.709` | caused as a result of an asynchronous |
| `00:00:44.719 - 00:00:47.310` | interrupt either the timer is requiring |
| `00:00:47.320 - 00:00:49.830` | attention or an IO device is requiring |
| `00:00:49.840 - 00:00:52.069` | attention or it could be a synchronous |
| `00:00:52.079 - 00:00:54.310` | exception due to a system call by the |
| `00:00:54.320 - 00:00:58.310` | user mode code or some sort of a program |
| `00:00:58.320 - 00:01:00.750` | error the hardware will do several |
| `00:01:00.760 - 00:01:02.670` | things it will disable interrupts it |
| `00:01:02.680 - 00:01:04.710` | will switch to supervisor mode it will |
| `00:01:04.720 - 00:01:06.789` | save the program counter in |
| `00:01:06.799 - 00:01:10.190` | scpc it will save some information about |
| `00:01:10.200 - 00:01:13.109` | what exactly caused the trap in this s |
| `00:01:13.119 - 00:01:15.870` | cause register and it will load the PC |
| `00:01:15.880 - 00:01:19.230` | with the address of the |
| `00:01:19.240 - 00:01:23.990` | userve function uh there is a control |
| `00:01:24.000 - 00:01:27.990` | and Status register um called stvc and |
| `00:01:28.000 - 00:01:31.870` | that contains the address uh of the code |
| `00:01:31.880 - 00:01:33.950` | that's supposed to handle any kind of a |
| `00:01:33.960 - 00:01:38.109` | trap and so uh that's how we Branch to |
| `00:01:38.119 - 00:01:40.910` | this assembly code that is located in |
| `00:01:40.920 - 00:01:43.789` | the trampoline page so this trampoline |
| `00:01:43.799 - 00:01:47.350` | page recall is mapped into all address |
| `00:01:47.360 - 00:01:49.749` | spaces both the user address space and |
| `00:01:49.759 - 00:01:52.149` | the Kernel's address space and it does |
| `00:01:52.159 - 00:01:56.310` | the initial saving of the user's state |
| `00:01:56.320 - 00:01:58.789` | so what does it do in particular well it |
| `00:01:58.799 - 00:02:01.190` | saves all the general purpose registers |
| `00:02:01.200 - 00:02:04.149` | and the program counter in the Trap |
| `00:02:04.159 - 00:02:06.830` | frame each process has its own unique |
| `00:02:06.840 - 00:02:08.990` | trap frame but they're mapped into the |
| `00:02:09.000 - 00:02:10.790` | second highest page in the user's |
| `00:02:10.800 - 00:02:13.510` | address space this code is executed in |
| `00:02:13.520 - 00:02:17.030` | the user's address space so then we get |
| `00:02:17.040 - 00:02:20.390` | ready to start executing kernel code and |
| `00:02:20.400 - 00:02:23.470` | every thread needs its own stack uh so |
| `00:02:23.480 - 00:02:26.550` | we uh have to initialize the stack |
| `00:02:26.560 - 00:02:29.350` | pointer register uh and we also |
| `00:02:29.360 - 00:02:31.670` | initialize the TP register which |
| `00:02:31.680 - 00:02:33.229` | contains the core |
| `00:02:33.239 - 00:02:35.670` | number these two registers would have |
| `00:02:35.680 - 00:02:37.430` | other values in the user code we can't |
| `00:02:37.440 - 00:02:39.830` | trust what values they might have had so |
| `00:02:39.840 - 00:02:41.589` | we need to load those but we don't |
| `00:02:41.599 - 00:02:45.030` | really need to load anything else uh we |
| `00:02:45.040 - 00:02:47.670` | then load thep control and Status |
| `00:02:47.680 - 00:02:49.350` | register with the pointer to the |
| `00:02:49.360 - 00:02:51.670` | Kernel's page table so that will switch |
| `00:02:51.680 - 00:02:54.149` | us effectively into the Kernel's virtual |
| `00:02:54.159 - 00:02:57.390` | address bace and at that point we then |
| `00:02:57.400 - 00:03:01.149` | jump into this function called user trap |
| `00:03:01.159 - 00:03:04.190` | and this is C code and what does it do |
| `00:03:04.200 - 00:03:06.149` | well it's got a n statement and it |
| `00:03:06.159 - 00:03:08.869` | figures out what exactly is going on but |
| `00:03:08.879 - 00:03:12.509` | first it stores a pointer to the another |
| `00:03:12.519 - 00:03:14.309` | trap Vector in |
| `00:03:14.319 - 00:03:17.070` | stvc previously stvc contained a pointer |
| `00:03:17.080 - 00:03:20.110` | to this function here but if a trap |
| `00:03:20.120 - 00:03:21.710` | occurs while we're executing in kernel |
| `00:03:21.720 - 00:03:23.350` | mode we want to handle it a different |
| `00:03:23.360 - 00:03:27.350` | way so we update St back here and then |
| `00:03:27.360 - 00:03:30.070` | we look at the cause register the s |
| `00:03:30.080 - 00:03:32.949` | cause register and determine whether it |
| `00:03:32.959 - 00:03:36.270` | was some sort of uh program exception if |
| `00:03:36.280 - 00:03:38.190` | there's an error we print a message and |
| `00:03:38.200 - 00:03:39.789` | call a function exit which will |
| `00:03:39.799 - 00:03:42.149` | terminate this uh process Al |
| `00:03:42.159 - 00:03:45.149` | together if a device needs uh handling |
| `00:03:45.159 - 00:03:47.990` | well we call this device interrupt uh |
| `00:03:48.000 - 00:03:50.350` | function here to take care of |
| `00:03:50.360 - 00:03:53.830` | that uh there's a Boolean called killed |
| `00:03:53.840 - 00:03:56.509` | that's associated with each process and |
| `00:03:56.519 - 00:03:58.390` | if it gets set we need to terminate that |
| `00:03:58.400 - 00:04:00.789` | process so if for some reason this |
| `00:04:00.799 - 00:04:03.470` | process has been killed some somebody |
| `00:04:03.480 - 00:04:06.149` | else wanted this process to die well |
| `00:04:06.159 - 00:04:08.550` | they would have set that Boolean to true |
| `00:04:08.560 - 00:04:10.390` | and at this point we check it and if |
| `00:04:10.400 - 00:04:13.069` | it's uh the case that this process |
| `00:04:13.079 - 00:04:15.149` | should die after handling the device |
| `00:04:15.159 - 00:04:18.390` | interrupt we then call exit but probably |
| `00:04:18.400 - 00:04:21.469` | we just continue down here if uh the |
| `00:04:21.479 - 00:04:23.990` | timer interrupted then it's time to have |
| `00:04:24.000 - 00:04:27.110` | the uh end of this time slice and give |
| `00:04:27.120 - 00:04:30.550` | some other processes uh a chance to run |
| `00:04:30.560 - 00:04:33.150` | so we check the killed thing and if so |
| `00:04:33.160 - 00:04:37.550` | we just exit uh but otherwise we call |
| `00:04:37.560 - 00:04:39.950` | yield yield |
| `00:04:39.960 - 00:04:42.749` | will eventually return but what it will |
| `00:04:42.759 - 00:04:45.909` | do is it will um cause the execution or |
| `00:04:45.919 - 00:04:48.469` | allow the execution of other processes |
| `00:04:48.479 - 00:04:50.510` | it will invoke the scheduler and some |
| `00:04:50.520 - 00:04:52.310` | other processes which I've hinted at |
| `00:04:52.320 - 00:04:55.469` | here will execute but eventually uh |
| `00:04:55.479 - 00:04:58.590` | we'll get another time slice and return |
| `00:04:58.600 - 00:05:00.790` | to yield and then yield will return and |
| `00:05:00.800 - 00:05:02.310` | then we'll continue and go back into the |
| `00:05:02.320 - 00:05:04.590` | user mode uh |
| `00:05:04.600 - 00:05:08.029` | process if the cause of the interrupt |
| `00:05:08.039 - 00:05:10.310` | was or because of the Trap was a system |
| `00:05:10.320 - 00:05:14.189` | call then we enable the interrupts okay |
| `00:05:14.199 - 00:05:17.510` | so we This Thread itself may be |
| `00:05:17.520 - 00:05:19.629` | interrupted uh for example there could |
| `00:05:19.639 - 00:05:23.070` | be a device requiring attention while |
| `00:05:23.080 - 00:05:25.510` | we're dealing with this system call |
| `00:05:25.520 - 00:05:28.990` | interrupts are enabled uh and so that |
| `00:05:29.000 - 00:05:31.150` | might actually occur we might uh uh go |
| `00:05:31.160 - 00:05:33.550` | off and do some other stuff but uh in |
| `00:05:33.560 - 00:05:35.670` | any case we handle the system call and |
| `00:05:35.680 - 00:05:38.270` | then we come back and if that point |
| `00:05:38.280 - 00:05:41.270` | somebody has asked for us this process |
| `00:05:41.280 - 00:05:43.550` | to die then we call exit but otherwise |
| `00:05:43.560 - 00:05:46.990` | we continue and then finally whatever |
| `00:05:47.000 - 00:05:49.830` | the case is we call a c function user |
| `00:05:49.840 - 00:05:53.909` | trap r or user trap return and it will |
| `00:05:53.919 - 00:05:55.510` | begin the process of returning to the |
| `00:05:55.520 - 00:05:58.790` | user mode code it will disable the |
| `00:05:58.800 - 00:06:00.990` | interrupts in in case they got enabled |
| `00:06:01.000 - 00:06:05.390` | here and it will set stve to point to |
| `00:06:05.400 - 00:06:07.870` | user VC so now uh this is in preparation |
| `00:06:07.880 - 00:06:09.710` | for the next trap so we need to have it |
| `00:06:09.720 - 00:06:11.629` | point to |
| `00:06:11.639 - 00:06:15.309` | user Fin and then we need to um save |
| `00:06:15.319 - 00:06:18.670` | into our trap frame values for the stack |
| `00:06:18.680 - 00:06:20.510` | pointer and we need to save the core |
| `00:06:20.520 - 00:06:22.670` | number that we're executing on remember |
| `00:06:22.680 - 00:06:25.670` | we we loaded the these here well here |
| `00:06:25.680 - 00:06:27.430` | we're we're setting them up and getting |
| `00:06:27.440 - 00:06:30.909` | them ready to go for when the next trap |
| `00:06:30.919 - 00:06:33.790` | occurs the SR instruction will rely on |
| `00:06:33.800 - 00:06:37.830` | the scpc register so we uh retrieve the |
| `00:06:37.840 - 00:06:41.270` | saved program counter and we put it into |
| `00:06:41.280 - 00:06:44.990` | the scpc register and then we jump into |
| `00:06:45.000 - 00:06:47.430` | some assembly code and this assembly |
| `00:06:47.440 - 00:06:50.029` | code is located in the trampoline page |
| `00:06:50.039 - 00:06:52.110` | it's called user rep and this will |
| `00:06:52.120 - 00:06:54.990` | complete the process and execute the SR |
| `00:06:55.000 - 00:06:57.550` | instruction it will now that we're |
| `00:06:57.560 - 00:07:00.270` | executing the trampoline page uh we are |
| `00:07:00.280 - 00:07:02.430` | in that top page that lives in every |
| `00:07:02.440 - 00:07:04.990` | user add every address space and so we |
| `00:07:05.000 - 00:07:06.749` | can switch from the Colonel's address |
| `00:07:06.759 - 00:07:09.990` | space to the users address space and we |
| `00:07:10.000 - 00:07:12.110` | do that by loading the satp register |
| `00:07:12.120 - 00:07:15.790` | with a pointer to the users page table |
| `00:07:15.800 - 00:07:18.430` | and then finally we restore the users |
| `00:07:18.440 - 00:07:21.909` | registers and we also um set a couple of |
| `00:07:21.919 - 00:07:25.909` | bits in the status register uh the SR |
| `00:07:25.919 - 00:07:28.150` | instruction will use the status register |
| `00:07:28.160 - 00:07:30.710` | and we set bits to to tell the SR |
| `00:07:30.720 - 00:07:32.990` | instruction we want to go into user mode |
| `00:07:33.000 - 00:07:36.029` | we want to the previous privilege level |
| `00:07:36.039 - 00:07:37.909` | uh will be user mode so we will be |
| `00:07:37.919 - 00:07:40.869` | returning to user mode and the previous |
| `00:07:40.879 - 00:07:43.749` | interrupts enable flag we set to enabled |
| `00:07:43.759 - 00:07:45.710` | so this will be copied into the current |
| `00:07:45.720 - 00:07:48.070` | interrupts enabled flag so when we |
| `00:07:48.080 - 00:07:50.430` | execute the S interrupts will then |
| `00:07:50.440 - 00:07:52.350` | become enabled and we will be in user |
| `00:07:52.360 - 00:07:56.270` | mode you running with uh the users |
| `00:07:56.280 - 00:07:59.070` | registers and uh the users page table |
| `00:07:59.080 - 00:08:00.430` | and so |
| `00:08:00.440 - 00:08:03.189` | one okay so let's now take a look at |
| `00:08:03.199 - 00:08:04.629` | some of the data structures that are |
| `00:08:04.639 - 00:08:06.550` | involved uh first of all let's take a |
| `00:08:06.560 - 00:08:07.790` | look at this trap |
| `00:08:07.800 - 00:08:11.670` | frame while we're executing in user mode |
| `00:08:11.680 - 00:08:13.230` | there's a control and Status register |
| `00:08:13.240 - 00:08:16.909` | called sratch and it will point to this |
| `00:08:16.919 - 00:08:21.029` | trap frame and that will be |
| `00:08:21.039 - 00:08:24.350` | useful this contains the place where we |
| `00:08:24.360 - 00:08:27.189` | will save the user mode registers so up |
| `00:08:27.199 - 00:08:29.990` | here when we save the uh registers in |
| `00:08:30.000 - 00:08:32.230` | the PC and the trap frame that's going |
| `00:08:32.240 - 00:08:33.430` | to |
| `00:08:33.440 - 00:08:36.550` | happen and that uh information will be |
| `00:08:36.560 - 00:08:39.350` | saved here the 31 general purpose |
| `00:08:39.360 - 00:08:42.190` | registers um will be saved here |
| `00:08:42.200 - 00:08:44.470` | remembering that the 302nd register that |
| `00:08:44.480 - 00:08:46.509` | is register zero is is always uh |
| `00:08:46.519 - 00:08:48.269` | hardwire to zero so we only have to save |
| `00:08:48.279 - 00:08:50.790` | 31 of them and the program counter the |
| `00:08:50.800 - 00:08:52.910` | previous program counter will be saved |
| `00:08:52.920 - 00:08:54.990` | here so that's the state of the user |
| `00:08:55.000 - 00:08:59.750` | mode thread and then uh we load up the |
| `00:08:59.760 - 00:09:03.069` | state for the kernel thread so uh we |
| `00:09:03.079 - 00:09:04.670` | have a pointer to the Colonel's page |
| `00:09:04.680 - 00:09:06.829` | table that's nice and handy to have and |
| `00:09:06.839 - 00:09:09.750` | that's right there we have a pointer to |
| `00:09:09.760 - 00:09:12.630` | the stack that this particular process |
| `00:09:12.640 - 00:09:15.230` | will be using uh and then we have the |
| `00:09:15.240 - 00:09:18.110` | address of uh user trap that's actually |
| `00:09:18.120 - 00:09:19.230` | not really part of the state of the |
| `00:09:19.240 - 00:09:21.550` | supervisor thread but we have that in |
| `00:09:21.560 - 00:09:24.069` | this trap frame as well and finally we |
| `00:09:24.079 - 00:09:26.829` | have the heart ID or or the core number |
| `00:09:26.839 - 00:09:29.030` | if you will which we will need to load |
| `00:09:29.040 - 00:09:31.190` | into the TP register when the Trap |
| `00:09:31.200 - 00:09:33.829` | occurs so that's what's going on on the |
| `00:09:33.839 - 00:09:36.829` | Trap frame we have some other crucial |
| `00:09:36.839 - 00:09:39.310` | and important data structures we have a |
| `00:09:39.320 - 00:09:41.670` | data structure called CPU there is an |
| `00:09:41.680 - 00:09:45.590` | array called CPUs and there are eight of |
| `00:09:45.600 - 00:09:50.030` | these one per core and each one of these |
| `00:09:50.040 - 00:09:52.910` | elements in this array is a structure |
| `00:09:52.920 - 00:09:55.030` | and that structure contains these |
| `00:09:55.040 - 00:09:58.550` | fields um if a user mode process is |
| `00:09:58.560 - 00:10:01.150` | running on that c or then we will have a |
| `00:10:01.160 - 00:10:03.150` | pointer to the proc structure that |
| `00:10:03.160 - 00:10:06.949` | describes it okay if nothing is running |
| `00:10:06.959 - 00:10:08.829` | on that CPU or I should say if the |
| `00:10:08.839 - 00:10:11.670` | schedu or thread is running this proc |
| `00:10:11.680 - 00:10:15.829` | field will be zero or null and uh I |
| `00:10:15.839 - 00:10:18.110` | talked earlier about uh interrupts and |
| `00:10:18.120 - 00:10:20.710` | how we can sort of stack them uh we |
| `00:10:20.720 - 00:10:22.470` | remember whether initially they were |
| `00:10:22.480 - 00:10:24.630` | enabled or not and then if we want to |
| `00:10:24.640 - 00:10:26.790` | disable them then we uh remember the |
| `00:10:26.800 - 00:10:30.069` | initial value and uh increment a counter |
| `00:10:30.079 - 00:10:31.949` | and then when we uh back off we can |
| `00:10:31.959 - 00:10:33.630` | decrement the counter and ultimately |
| `00:10:33.640 - 00:10:35.949` | return to the uh original state of the |
| `00:10:35.959 - 00:10:40.949` | interrupts enabled uh bit and finally we |
| `00:10:40.959 - 00:10:44.590` | have another register save area um |
| `00:10:44.600 - 00:10:45.670` | called |
| `00:10:45.680 - 00:10:48.590` | context now one thing I didn't really |
| `00:10:48.600 - 00:10:51.509` | make clear was that |
| `00:10:51.519 - 00:10:54.389` | um when we're executing a process we're |
| `00:10:54.399 - 00:10:55.990` | executing either in user mode or in |
| `00:10:56.000 - 00:10:58.910` | kernel mode but we may switch and I |
| `00:10:58.920 - 00:11:00.509` | don't show this here but we may switch |
| `00:11:00.519 - 00:11:03.470` | over to the supervisor thread okay and |
| `00:11:03.480 - 00:11:06.230` | we leave that process behind so we're |
| `00:11:06.240 - 00:11:09.470` | really uh in all of this uh what that |
| `00:11:09.480 - 00:11:11.150` | we're talking about here we are |
| `00:11:11.160 - 00:11:16.509` | executing as part of a process okay but |
| `00:11:16.519 - 00:11:18.790` | in here perhaps in here or or in the in |
| `00:11:18.800 - 00:11:22.110` | the yield we will maybe go into another |
| `00:11:22.120 - 00:11:26.509` | processes uh uh time slice so uh we will |
| `00:11:26.519 - 00:11:28.190` | have other contact switches that are |
| `00:11:28.200 - 00:11:30.949` | different a context switch occurring |
| `00:11:30.959 - 00:11:33.350` | here is different than a context switch |
| `00:11:33.360 - 00:11:36.310` | occurring here and so if we leave this |
| `00:11:36.320 - 00:11:38.949` | process and go into the schedu thread we |
| `00:11:38.959 - 00:11:41.509` | will need to save the registers and so |
| `00:11:41.519 - 00:11:43.590` | that's what this register save area is |
| `00:11:43.600 - 00:11:46.550` | all about here so |
| `00:11:46.560 - 00:11:49.949` | uh then let me point out we don't need |
| `00:11:49.959 - 00:11:52.430` | to save all of the registers um we only |
| `00:11:52.440 - 00:11:55.509` | need to save uh some of the registers um |
| `00:11:55.519 - 00:11:59.790` | and we'll talk about that later if um |
| `00:11:59.800 - 00:12:03.350` | we are executing if this core is |
| `00:12:03.360 - 00:12:06.030` | executing a processor thread then we'll |
| `00:12:06.040 - 00:12:07.710` | have a pointer to a structure that |
| `00:12:07.720 - 00:12:10.310` | describes that process and it's got a |
| `00:12:10.320 - 00:12:11.710` | number of fields so let me go through |
| `00:12:11.720 - 00:12:14.629` | these quickly we've got a lock field we |
| `00:12:14.639 - 00:12:16.629` | have the current state of the process it |
| `00:12:16.639 - 00:12:19.230` | could be unused used it could be |
| `00:12:19.240 - 00:12:21.189` | sleeping it could be runnable which |
| `00:12:21.199 - 00:12:23.189` | means it's ready to go and and it could |
| `00:12:23.199 - 00:12:24.470` | be scheduled but it's just waiting for a |
| `00:12:24.480 - 00:12:26.990` | Time slice it could be actually running |
| `00:12:27.000 - 00:12:30.030` | or after the uh process terminates we |
| `00:12:30.040 - 00:12:32.350` | call exit it becomes a zombie for a |
| `00:12:32.360 - 00:12:35.189` | little while before the uh structure is |
| `00:12:35.199 - 00:12:37.150` | then recycled and becomes |
| `00:12:37.160 - 00:12:40.110` | unused um if we are waiting on some lock |
| `00:12:40.120 - 00:12:42.910` | if this if this thread is sleeping if it |
| `00:12:42.920 - 00:12:45.949` | has the status of sleeping then um this |
| `00:12:45.959 - 00:12:48.550` | channel uh describes exactly what it is |
| `00:12:48.560 - 00:12:50.990` | we're waiting on here's that killed |
| `00:12:51.000 - 00:12:53.389` | Boolean that I mentioned after the |
| `00:12:53.399 - 00:12:55.550` | process terminates it has an exit status |
| `00:12:55.560 - 00:12:58.590` | and that's what this U field is and |
| `00:12:58.600 - 00:13:01.069` | every process has an ID number uh that's |
| `00:13:01.079 - 00:13:03.509` | sequentially given an ID number this |
| `00:13:03.519 - 00:13:06.750` | lock protects these fields so let me |
| `00:13:06.760 - 00:13:09.670` | zoom in a bit uh this lock this spin |
| `00:13:09.680 - 00:13:11.189` | lock will protect these fields in other |
| `00:13:11.199 - 00:13:12.710` | words if any of these fields are going |
| `00:13:12.720 - 00:13:14.990` | to be modified or accessed we need to uh |
| `00:13:15.000 - 00:13:18.710` | grab the spin lock we also have a |
| `00:13:18.720 - 00:13:20.430` | pointer to our parents so this is a |
| `00:13:20.440 - 00:13:22.470` | pointer to one of these other proc |
| `00:13:22.480 - 00:13:26.110` | structures uh there are up to 64 |
| `00:13:26.120 - 00:13:28.350` | processes well there are 64 processes |
| `00:13:28.360 - 00:13:31.110` | some of them May be unused but we could |
| `00:13:31.120 - 00:13:33.710` | have up to 64 processes uh running and |
| `00:13:33.720 - 00:13:36.189` | runnable at once and for each one of |
| `00:13:36.199 - 00:13:38.110` | them there is a structure and this is |
| `00:13:38.120 - 00:13:40.350` | the structure and so this I'm showing |
| `00:13:40.360 - 00:13:44.150` | just the structure for one process and |
| `00:13:44.160 - 00:13:47.150` | parent points to whichever structure is |
| `00:13:47.160 - 00:13:49.430` | the parent process represents the parent |
| `00:13:49.440 - 00:13:53.550` | process for this parent for this |
| `00:13:53.560 - 00:13:56.749` | process uh there is a stack for the |
| `00:13:56.759 - 00:13:58.710` | kernel aspect of this process so when |
| `00:13:58.720 - 00:14:01.430` | we're are executing here okay when we're |
| `00:14:01.440 - 00:14:04.230` | executing here we need to have a stack |
| `00:14:04.240 - 00:14:07.470` | for the kernel to use each of the 64 |
| `00:14:07.480 - 00:14:10.030` | processes needs a separate stack so |
| `00:14:10.040 - 00:14:13.389` | that's uh given here by this this |
| `00:14:13.399 - 00:14:15.910` | there's a pointer to that stack here |
| `00:14:15.920 - 00:14:18.030` | this is the size of the virtual address |
| `00:14:18.040 - 00:14:19.629` | space and here's a pointer to the page |
| `00:14:19.639 - 00:14:22.509` | table for the user's address space and |
| `00:14:22.519 - 00:14:25.749` | then we have a pointer to our trap frame |
| `00:14:25.759 - 00:14:29.230` | page each process has a unique page in |
| `00:14:29.240 - 00:14:31.470` | physical memory and so this points to |
| `00:14:31.480 - 00:14:33.990` | that page it will be mapped in they will |
| `00:14:34.000 - 00:14:37.269` | all be mapped into the second highest |
| `00:14:37.279 - 00:14:40.629` | page in in uh the address bases but this |
| `00:14:40.639 - 00:14:43.710` | is a pointer to the physical page and |
| `00:14:43.720 - 00:14:44.790` | then we have another one of these |
| `00:14:44.800 - 00:14:47.430` | context things so this is another save |
| `00:14:47.440 - 00:14:49.030` | area so when we're switching back and |
| `00:14:49.040 - 00:14:52.509` | forth between the the scheduler thread |
| `00:14:52.519 - 00:14:54.670` | and the process we can use these two |
| `00:14:54.680 - 00:14:55.710` | save |
| `00:14:55.720 - 00:14:58.790` | areas and uh we have an array here for |
| `00:14:58.800 - 00:15:00.430` | open open files and the current working |
| `00:15:00.440 - 00:15:03.269` | directory we even have a space for the |
| `00:15:03.279 - 00:15:04.910` | name of this process so we can give |
| `00:15:04.920 - 00:15:05.990` | processes |
| `00:15:06.000 - 00:15:08.829` | names okay now uh that we've seen |
| `00:15:08.839 - 00:15:10.189` | pictures of those let's go ahead and |
| `00:15:10.199 - 00:15:15.230` | look at the definitions in this uh file |
| `00:15:15.240 - 00:15:20.310` | proc so this is a context um that was |
| `00:15:20.320 - 00:15:24.990` | used both here and here and you can see |
| `00:15:25.000 - 00:15:28.389` | it contains the ra that's the return |
| `00:15:28.399 - 00:15:30.949` | address Reg register and that tells you |
| `00:15:30.959 - 00:15:32.990` | know that's the PC if you will where we |
| `00:15:33.000 - 00:15:36.309` | go back to and then the stack pointer |
| `00:15:36.319 - 00:15:38.670` | and then we only need to worry about the |
| `00:15:38.680 - 00:15:42.309` | S registers there are 12 s registers and |
| `00:15:42.319 - 00:15:43.990` | and we only need to worry about those so |
| `00:15:44.000 - 00:15:47.030` | we only need to have an area to save |
| `00:15:47.040 - 00:15:49.470` | those next let's look at the CPU |
| `00:15:49.480 - 00:15:52.710` | structure this CPU structure here and we |
| `00:15:52.720 - 00:15:56.790` | see that uh it contains a pointer to the |
| `00:15:56.800 - 00:16:00.590` | pro the process if it's it's running or |
| `00:16:00.600 - 00:16:04.309` | null if not and we have an area for the |
| `00:16:04.319 - 00:16:08.269` | context the save area that I showed here |
| `00:16:08.279 - 00:16:10.670` | and then we have uh these two fields |
| `00:16:10.680 - 00:16:13.629` | that are the depth of the push off every |
| `00:16:13.639 - 00:16:15.749` | time we call push off it increments that |
| `00:16:15.759 - 00:16:19.389` | that counter and uh this remembers uh |
| `00:16:19.399 - 00:16:21.150` | whether interrupts were enabled before |
| `00:16:21.160 - 00:16:23.509` | the initial push off and then here we |
| `00:16:23.519 - 00:16:26.590` | have our uh array of well we have eight |
| `00:16:26.600 - 00:16:29.389` | CPUs so it's an array of eight elements |
| `00:16:29.399 - 00:16:30.829` | of these of these |
| `00:16:30.839 - 00:16:32.990` | structures uh the next thing we want to |
| `00:16:33.000 - 00:16:38.030` | look at is the the um trap frame so I'm |
| `00:16:38.040 - 00:16:39.269` | not going to read through this |
| `00:16:39.279 - 00:16:41.309` | information here but here's what's going |
| `00:16:41.319 - 00:16:42.990` | on in the Trap frame we're going to be |
| `00:16:43.000 - 00:16:44.990` | accessing this in assembly code so we |
| `00:16:45.000 - 00:16:47.230` | actually care about these offsets the |
| `00:16:47.240 - 00:16:49.509` | Trap frame is a page and this will be |
| `00:16:49.519 - 00:16:52.030` | page aligned uh and these are the |
| `00:16:52.040 - 00:16:54.910` | offsets uh given in comments here of |
| `00:16:54.920 - 00:16:56.350` | each of these fields you can see that |
| `00:16:56.360 - 00:17:00.030` | each of these fields is a 64 bit number |
| `00:17:00.040 - 00:17:03.470` | right and here are the fields that I I I |
| `00:17:03.480 - 00:17:06.949` | mentioned uh let me get my trap frame |
| `00:17:06.959 - 00:17:09.510` | picture here and so you can kind of we |
| `00:17:09.520 - 00:17:13.110` | can go along um we've got the |
| `00:17:13.120 - 00:17:17.029` | satp uh the stack pointer this trap I |
| `00:17:17.039 - 00:17:18.630` | did these in a slightly different order |
| `00:17:18.640 - 00:17:21.990` | the saved PC and then the core |
| `00:17:22.000 - 00:17:24.429` | number uh and then we have uh you can |
| `00:17:24.439 - 00:17:27.150` | count them we have all uh 31 registers |
| `00:17:27.160 - 00:17:32.190` | here um and uh okay so we have all 31 |
| `00:17:32.200 - 00:17:35.870` | registers there um just in case you're |
| `00:17:35.880 - 00:17:38.110` | curious uh they listed in sort of a a |
| `00:17:38.120 - 00:17:39.750` | strange order for example we have some |
| `00:17:39.760 - 00:17:42.310` | T's here we have some s's here we have |
| `00:17:42.320 - 00:17:44.310` | some A's here we have more S's and we |
| `00:17:44.320 - 00:17:46.950` | have uh more T's down down |
| `00:17:46.960 - 00:17:49.430` | here that's the actual way they're |
| `00:17:49.440 - 00:17:52.470` | organized in the risk 5 processor but uh |
| `00:17:52.480 - 00:17:54.070` | we just care about we we just access |
| `00:17:54.080 - 00:17:55.549` | them by name and we don't really care |
| `00:17:55.559 - 00:17:58.909` | which this is register X1 X2 X3 and and |
| `00:17:58.919 - 00:18:03.870` | so on um so next let's take a look at |
| `00:18:03.880 - 00:18:08.470` | the proc structure okay so that's this |
| `00:18:08.480 - 00:18:11.029` | structure here uh we're going to look at |
| `00:18:11.039 - 00:18:15.789` | this structure here and um it's uh shown |
| `00:18:15.799 - 00:18:18.350` | uh whoops let's see if I can zoom in |
| `00:18:18.360 - 00:18:19.990` | there |
| `00:18:20.000 - 00:18:24.789` | uh the uh State down here um is a proc |
| `00:18:24.799 - 00:18:28.110` | State and as I said here are the states |
| `00:18:28.120 - 00:18:31.590` | uh unused used used |
| `00:18:31.600 - 00:18:34.990` | sleeping runnable running and zombie so |
| `00:18:35.000 - 00:18:37.430` | when the process is actually running on |
| `00:18:37.440 - 00:18:40.590` | the core it has a status of or a state |
| `00:18:40.600 - 00:18:43.230` | if you will of running when it's ready |
| `00:18:43.240 - 00:18:45.430` | to run and uh but unfortunately there's |
| `00:18:45.440 - 00:18:46.990` | not an available core for it just |
| `00:18:47.000 - 00:18:49.110` | sitting around waiting for a Time slice |
| `00:18:49.120 - 00:18:53.310` | it it's runnable um if it has waited on |
| `00:18:53.320 - 00:18:56.630` | something uh it will go into a state of |
| `00:18:56.640 - 00:18:59.990` | sleeping and uh if it's terminated it |
| `00:19:00.000 - 00:19:02.270` | will become a zombie until it can be |
| `00:19:02.280 - 00:19:04.630` | recycled these structures are recycled |
| `00:19:04.640 - 00:19:07.310` | we only have 64 of them so we need to uh |
| `00:19:07.320 - 00:19:09.830` | reuse them after that process is killed |
| `00:19:09.840 - 00:19:13.510` | we need to reuse that structure it |
| `00:19:13.520 - 00:19:14.990` | remains in the zombie state for a little |
| `00:19:15.000 - 00:19:18.350` | while until the exit status this exit |
| `00:19:18.360 - 00:19:21.029` | State uh can be retrieved by um the |
| `00:19:21.039 - 00:19:23.350` | parent or whoever Waits on it um and |
| `00:19:23.360 - 00:19:25.549` | then at that point it can be recycled |
| `00:19:25.559 - 00:19:27.909` | and it becomes unused and then used as |
| `00:19:27.919 - 00:19:30.390` | sort of intermed mediate between unused |
| `00:19:30.400 - 00:19:34.950` | and something else so um here we have |
| `00:19:34.960 - 00:19:37.789` | this the lock that's very important we |
| `00:19:37.799 - 00:19:40.909` | need to lock the process and in |
| `00:19:40.919 - 00:19:42.310` | particular we need to lock it whenever |
| `00:19:42.320 - 00:19:45.590` | we're going to be using uh these fields |
| `00:19:45.600 - 00:19:49.590` | here's our State field listed up above |
| `00:19:49.600 - 00:19:52.149` | uh then we have this channel field it |
| `00:19:52.159 - 00:19:54.470` | could be uh zero but I mean it's it's |
| `00:19:54.480 - 00:19:56.710` | only relevant when we're uh asleep and |
| `00:19:56.720 - 00:19:58.830` | it tells what we're sleeping on we'll |
| `00:19:58.840 - 00:20:00.750` | get into how that works later but |
| `00:20:00.760 - 00:20:02.070` | basically it's just an |
| `00:20:02.080 - 00:20:05.909` | identifier um and uh uh that identifies |
| `00:20:05.919 - 00:20:07.149` | some different things that we might be |
| `00:20:07.159 - 00:20:11.029` | waiting on and uh here's the Boolean |
| `00:20:11.039 - 00:20:13.070` | that indicates whether someone has asked |
| `00:20:13.080 - 00:20:15.630` | for this process to be terminated here |
| `00:20:15.640 - 00:20:19.870` | is an integer that uh is used to uh |
| `00:20:19.880 - 00:20:23.029` | transfer an exit status uh from the |
| `00:20:23.039 - 00:20:26.950` | process itself to whoever Waits on it um |
| `00:20:26.960 - 00:20:29.149` | and then this is an integer that's just |
| `00:20:29.159 - 00:20:32.310` | the process ID number here is a pointer |
| `00:20:32.320 - 00:20:35.070` | to another proc structure that is the |
| `00:20:35.080 - 00:20:36.470` | parents |
| `00:20:36.480 - 00:20:40.630` | process weight lock must be held when |
| `00:20:40.640 - 00:20:43.510` | dealing with this uh field and then we |
| `00:20:43.520 - 00:20:46.909` | have some so called private Fields uh |
| `00:20:46.919 - 00:20:48.750` | the lock does not need to be held when |
| `00:20:48.760 - 00:20:51.990` | we are dealing with these this is the |
| `00:20:52.000 - 00:20:55.510` | address of the colonel stack uh here is |
| `00:20:55.520 - 00:20:56.990` | the |
| `00:20:57.000 - 00:21:01.190` | um size of the the uh uh virtual of the |
| `00:21:01.200 - 00:21:04.669` | uh virtual address space um here is a |
| `00:21:04.679 - 00:21:07.510` | pointer to the page table that describes |
| `00:21:07.520 - 00:21:10.830` | the virtual OS space here is a pointer |
| `00:21:10.840 - 00:21:14.870` | to the U uh data page that the |
| `00:21:14.880 - 00:21:17.310` | trampoline is going to use this will get |
| `00:21:17.320 - 00:21:19.430` | mapped into the second highest page but |
| `00:21:19.440 - 00:21:22.669` | here here's a pointer to that um here is |
| `00:21:22.679 - 00:21:27.310` | the context area so when we switch um |
| `00:21:27.320 - 00:21:29.470` | this process will will be using that and |
| `00:21:29.480 - 00:21:31.990` | finally an array array that is uh used |
| `00:21:32.000 - 00:21:33.510` | for open |
| `00:21:33.520 - 00:21:36.430` | files number of files is the number of |
| `00:21:36.440 - 00:21:38.710` | files that could be open simultaneously |
| `00:21:38.720 - 00:21:40.549` | and current working directory and then |
| `00:21:40.559 - 00:21:42.630` | we have a field of 16 bytes for a |
| `00:21:42.640 - 00:21:45.029` | process name that we can can |
| `00:21:45.039 - 00:21:48.669` | use okay I think that covers uh our road |
| `00:21:48.679 - 00:21:50.990` | map and hopefully uh this image uh will |
| `00:21:51.000 - 00:21:54.310` | help you understand what's going on uh |
| `00:21:54.320 - 00:21:57.110` | in the next video I plan to uh discuss |
| `00:21:57.120 - 00:22:00.149` | uh the user and user trap functions as |
| `00:22:00.159 - 00:22:03.950` | well as user trap R and user R see you |
| `00:22:03.960 - 00:22:07.400` | in the next video |
