# xv6 Kernel-1: Intro and Overview

| Field | Value |
| --- | --- |
| Video ID | `fWUJKH0RNFE` |
| URL | <https://www.youtube.com/watch?v=fWUJKH0RNFE> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.960 - 00:00:03.830` | this is the first in a series of videos |
| `00:00:03.840 - 00:00:07.510` | on the xv6 operating system kernel |
| `00:00:07.520 - 00:00:09.830` | this is a very short but very sweet |
| `00:00:09.840 - 00:00:12.470` | unix-like operating system that's used |
| `00:00:12.480 - 00:00:14.709` | for educational purposes |
| `00:00:14.719 - 00:00:16.630` | it was developed at mit and used it to |
| `00:00:16.640 - 00:00:19.670` | other places as well |
| `00:00:19.680 - 00:00:20.790` | this is |
| `00:00:20.800 - 00:00:22.390` | an operating system that's used by |
| `00:00:22.400 - 00:00:24.470` | students primarily in an operating |
| `00:00:24.480 - 00:00:25.990` | system course |
| `00:00:26.000 - 00:00:27.750` | and there are |
| `00:00:27.760 - 00:00:30.950` | two implementations of this kernel |
| `00:00:30.960 - 00:00:35.110` | one for the x86 architecture and one for |
| `00:00:35.120 - 00:00:37.910` | the risc-5 architecture |
| `00:00:37.920 - 00:00:39.590` | in this series of videos i'm going to be |
| `00:00:39.600 - 00:00:42.630` | talking about the risc-5 version |
| `00:00:42.640 - 00:00:45.110` | the risc-5 version that is used is a |
| `00:00:45.120 - 00:00:47.590` | 64-bit processor |
| `00:00:47.600 - 00:00:50.630` | and whether using the x86 version or the |
| `00:00:50.640 - 00:00:52.630` | risc-5 version |
| `00:00:52.640 - 00:00:55.510` | you're probably going to be using it in |
| `00:00:55.520 - 00:00:58.630` | an emulated fashion using an emulator |
| `00:00:58.640 - 00:01:00.709` | like chemo |
| `00:01:00.719 - 00:01:02.389` | most likely you don't have a spare |
| `00:01:02.399 - 00:01:04.469` | computer sitting around |
| `00:01:04.479 - 00:01:06.070` | probably a spare |
| `00:01:06.080 - 00:01:07.510` | risk 5 |
| `00:01:07.520 - 00:01:10.550` | processor is even less likely |
| `00:01:10.560 - 00:01:12.550` | so instead you'll be running the |
| `00:01:12.560 - 00:01:14.789` | operating system if you choose to run it |
| `00:01:14.799 - 00:01:16.789` | uh under an emulator |
| `00:01:16.799 - 00:01:19.030` | but in any case it's meant to run on a |
| `00:01:19.040 - 00:01:20.469` | bear machine |
| `00:01:20.479 - 00:01:23.109` | and in fact it's a multi-core operating |
| `00:01:23.119 - 00:01:25.190` | system so |
| `00:01:25.200 - 00:01:28.710` | kimu is capable of emulating multi-core |
| `00:01:28.720 - 00:01:30.149` | systems |
| `00:01:30.159 - 00:01:33.510` | as i said it's short and very sweet |
| `00:01:33.520 - 00:01:36.789` | it's only about 6000 lines of code |
| `00:01:36.799 - 00:01:39.109` | most of it is written in the c |
| `00:01:39.119 - 00:01:40.630` | programming language |
| `00:01:40.640 - 00:01:43.749` | with maybe about 300 lines in assembly |
| `00:01:43.759 - 00:01:45.590` | language |
| `00:01:45.600 - 00:01:47.670` | in this video series what i'm going to |
| `00:01:47.680 - 00:01:48.550` | do |
| `00:01:48.560 - 00:01:49.749` | is |
| `00:01:49.759 - 00:01:52.389` | do a walkthrough of more or less all of |
| `00:01:52.399 - 00:01:54.310` | the code to give you an idea of what's |
| `00:01:54.320 - 00:01:56.230` | going on with it |
| `00:01:56.240 - 00:01:58.870` | the code is very simple and |
| `00:01:58.880 - 00:02:00.950` | well-written and clean code |
| `00:02:00.960 - 00:02:02.709` | and i've |
| `00:02:02.719 - 00:02:04.389` | read a lot of c code and written a lot |
| `00:02:04.399 - 00:02:07.270` | of c code and i'm still learning new |
| `00:02:07.280 - 00:02:08.949` | coding techniques and i think this is an |
| `00:02:08.959 - 00:02:10.389` | example |
| `00:02:10.399 - 00:02:13.110` | that is worth studying |
| `00:02:13.120 - 00:02:15.910` | in addition of course as an operating |
| `00:02:15.920 - 00:02:16.869` | system |
| `00:02:16.879 - 00:02:19.110` | kernel it illustrates some of the basic |
| `00:02:19.120 - 00:02:21.589` | concepts that you'd be learning in an |
| `00:02:21.599 - 00:02:23.430` | operating systems course so that's |
| `00:02:23.440 - 00:02:25.430` | another good reason to study this code |
| `00:02:25.440 - 00:02:27.430` | in detail |
| `00:02:27.440 - 00:02:29.030` | for this video series |
| `00:02:29.040 - 00:02:30.949` | i'm not going to assume that you have |
| `00:02:30.959 - 00:02:32.070` | any |
| `00:02:32.080 - 00:02:34.790` | knowledge of the risk 5 |
| `00:02:34.800 - 00:02:37.110` | instruction set architecture |
| `00:02:37.120 - 00:02:39.750` | but i will assume that you have had some |
| `00:02:39.760 - 00:02:41.430` | assembly language coding |
| `00:02:41.440 - 00:02:43.750` | i intend to walk through the assembly |
| `00:02:43.760 - 00:02:44.949` | language |
| `00:02:44.959 - 00:02:46.470` | instructions |
| `00:02:46.480 - 00:02:49.350` | line by line so i will hold your hand |
| `00:02:49.360 - 00:02:51.910` | there so don't worry |
| `00:02:51.920 - 00:02:54.390` | have you had an operating system class |
| `00:02:54.400 - 00:02:56.309` | maybe maybe you're in an operating |
| `00:02:56.319 - 00:02:58.070` | system class right now that is using the |
| `00:02:58.080 - 00:02:59.990` | xv6 system |
| `00:03:00.000 - 00:03:01.750` | in any case uh i've taught a few |
| `00:03:01.760 - 00:03:03.910` | operating system classes and i'll |
| `00:03:03.920 - 00:03:05.110` | probably |
| `00:03:05.120 - 00:03:06.869` | go over some of the concepts as we |
| `00:03:06.879 - 00:03:08.630` | encounter them |
| `00:03:08.640 - 00:03:10.630` | this is going to be a long series so |
| `00:03:10.640 - 00:03:13.030` | buckle your seat belts it's not for the |
| `00:03:13.040 - 00:03:15.350` | faint of heart |
| `00:03:15.360 - 00:03:17.910` | i'm assuming that you've got some brains |
| `00:03:17.920 - 00:03:20.149` | and furthermore i'm assuming that you're |
| `00:03:20.159 - 00:03:22.229` | interested in this particular system and |
| `00:03:22.239 - 00:03:23.750` | can focus for |
| `00:03:23.760 - 00:03:26.309` | the amount of time that it's going to be |
| `00:03:26.319 - 00:03:27.990` | for this video series |
| `00:03:28.000 - 00:03:30.710` | so let's uh look at some of the features |
| `00:03:30.720 - 00:03:33.990` | uh that the kernel has |
| `00:03:34.000 - 00:03:35.270` | it's got |
| `00:03:35.280 - 00:03:37.190` | processes |
| `00:03:37.200 - 00:03:39.350` | these processes run in their own virtual |
| `00:03:39.360 - 00:03:40.949` | address spaces |
| `00:03:40.959 - 00:03:42.149` | so |
| `00:03:42.159 - 00:03:44.869` | there are page tables for a page table |
| `00:03:44.879 - 00:03:47.830` | for each address space to support the |
| `00:03:47.840 - 00:03:50.550` | virtual address spaces |
| `00:03:50.560 - 00:03:52.869` | the operating system supports |
| `00:03:52.879 - 00:03:56.149` | files unix-like files and the directory |
| `00:03:56.159 - 00:03:57.750` | hierarchy |
| `00:03:57.760 - 00:04:00.390` | you can pipe data from |
| `00:04:00.400 - 00:04:03.030` | one program to another program |
| `00:04:03.040 - 00:04:06.309` | of course there's a timer interrupt so |
| `00:04:06.319 - 00:04:10.070` | there is multitasking the the various |
| `00:04:10.080 - 00:04:12.550` | processes are running in parallel |
| `00:04:12.560 - 00:04:14.949` | with time slicing |
| `00:04:14.959 - 00:04:16.229` | there are |
| `00:04:16.239 - 00:04:18.629` | 21 system calls that are implemented in |
| `00:04:18.639 - 00:04:20.069` | xv6 |
| `00:04:20.079 - 00:04:23.430` | this is not a lot the |
| `00:04:23.440 - 00:04:26.230` | production unix systems have more like |
| `00:04:26.240 - 00:04:28.550` | 300 system calls |
| `00:04:28.560 - 00:04:31.749` | maybe 500 system calls but this is |
| `00:04:31.759 - 00:04:32.870` | enough |
| `00:04:32.880 - 00:04:37.990` | to give you the core ideas of unix |
| `00:04:38.000 - 00:04:39.909` | there are a number of user programs that |
| `00:04:39.919 - 00:04:42.550` | are supplied with this kernel |
| `00:04:42.560 - 00:04:43.430` | and |
| `00:04:43.440 - 00:04:46.230` | these can illustrate |
| `00:04:46.240 - 00:04:48.150` | the capabilities of this operating |
| `00:04:48.160 - 00:04:49.830` | system |
| `00:04:49.840 - 00:04:51.430` | the operating system can run a simple |
| `00:04:51.440 - 00:04:53.110` | shell program |
| `00:04:53.120 - 00:04:54.710` | in fact i made a video that talks about |
| `00:04:54.720 - 00:04:56.469` | the shell program in detail |
| `00:04:56.479 - 00:04:58.790` | you can look for that if you want |
| `00:04:58.800 - 00:05:03.270` | other common unix programs cat echo |
| `00:05:03.280 - 00:05:04.790` | grep |
| `00:05:04.800 - 00:05:06.469` | kill which is used to terminate a |
| `00:05:06.479 - 00:05:07.670` | process |
| `00:05:07.680 - 00:05:09.909` | ln which is used to create a hard link |
| `00:05:09.919 - 00:05:12.710` | from one file to another |
| `00:05:12.720 - 00:05:13.510` | and |
| `00:05:13.520 - 00:05:16.230` | ls which is used to list out the |
| `00:05:16.240 - 00:05:18.070` | contents of a directory |
| `00:05:18.080 - 00:05:19.909` | you can create directories |
| `00:05:19.919 - 00:05:21.830` | you can remove files |
| `00:05:21.840 - 00:05:22.629` | and |
| `00:05:22.639 - 00:05:26.710` | wc is for counting the words in a file |
| `00:05:26.720 - 00:05:29.110` | as well as the characters |
| `00:05:29.120 - 00:05:30.870` | so all in all |
| `00:05:30.880 - 00:05:32.629` | i think that this can really be |
| `00:05:32.639 - 00:05:35.670` | considered a true unix system although |
| `00:05:35.680 - 00:05:38.629` | it's pretty short and simple |
| `00:05:38.639 - 00:05:41.189` | there's a lot that's missing okay |
| `00:05:41.199 - 00:05:42.469` | definitely |
| `00:05:42.479 - 00:05:44.230` | all the complexity of a real operating |
| `00:05:44.240 - 00:05:46.070` | system is is not there |
| `00:05:46.080 - 00:05:48.550` | a real operating system like linux may |
| `00:05:48.560 - 00:05:50.710` | have you know as much as a hundred times |
| `00:05:50.720 - 00:05:52.550` | as much code |
| `00:05:52.560 - 00:05:54.629` | and you know we're talking a million |
| `00:05:54.639 - 00:05:56.309` | lines of the kernel and you know when |
| `00:05:56.319 - 00:05:58.230` | you add the device drivers in |
| `00:05:58.240 - 00:05:59.749` | you can go |
| `00:05:59.759 - 00:06:03.270` | up to many millions of lines of code so |
| `00:06:03.280 - 00:06:04.790` | this is just too much |
| `00:06:04.800 - 00:06:06.390` | for any human |
| `00:06:06.400 - 00:06:08.790` | to study really and if you want to find |
| `00:06:08.800 - 00:06:11.110` | out how kernels work this is really the |
| `00:06:11.120 - 00:06:13.270` | operating system for you |
| `00:06:13.280 - 00:06:14.790` | but there there are some things that are |
| `00:06:14.800 - 00:06:17.830` | missing from your typical unix or linux |
| `00:06:17.840 - 00:06:19.189` | system |
| `00:06:19.199 - 00:06:20.390` | there |
| `00:06:20.400 - 00:06:24.230` | are no user ids and no login sequence no |
| `00:06:24.240 - 00:06:26.629` | verification |
| `00:06:26.639 - 00:06:29.110` | there are no protection bits associated |
| `00:06:29.120 - 00:06:30.390` | with files |
| `00:06:30.400 - 00:06:31.909` | you know the read write execute |
| `00:06:31.919 - 00:06:35.110` | protections that's not here |
| `00:06:35.120 - 00:06:37.749` | the mount command is just not available |
| `00:06:37.759 - 00:06:38.469` | so |
| `00:06:38.479 - 00:06:41.590` | you just have one file system |
| `00:06:41.600 - 00:06:43.270` | in a real system |
| `00:06:43.280 - 00:06:45.510` | the virtual address spaces can be paged |
| `00:06:45.520 - 00:06:48.469` | out to disk so that you can run more |
| `00:06:48.479 - 00:06:52.070` | processes than will fit in physical |
| `00:06:52.080 - 00:06:53.830` | main memory |
| `00:06:53.840 - 00:06:55.270` | that's not present |
| `00:06:55.280 - 00:06:57.830` | in xv6 |
| `00:06:57.840 - 00:06:59.110` | there's no |
| `00:06:59.120 - 00:07:01.990` | support for networks no sockets or |
| `00:07:02.000 - 00:07:03.990` | anything like that |
| `00:07:04.000 - 00:07:06.550` | in fact there's no way for processes to |
| `00:07:06.560 - 00:07:07.589` | communicate |
| `00:07:07.599 - 00:07:11.270` | or synchronize amongst themselves |
| `00:07:11.280 - 00:07:12.710` | there are |
| `00:07:12.720 - 00:07:16.309` | two device drivers but a real operating |
| `00:07:16.319 - 00:07:18.070` | system a real world operating system is |
| `00:07:18.080 - 00:07:20.629` | going to have many more device drivers |
| `00:07:20.639 - 00:07:22.629` | to support all kinds of different bits |
| `00:07:22.639 - 00:07:25.110` | of hardware that you might find |
| `00:07:25.120 - 00:07:28.629` | and lastly there is only a limited |
| `00:07:28.639 - 00:07:30.469` | amount of user code |
| `00:07:30.479 - 00:07:32.710` | i listed the |
| `00:07:32.720 - 00:07:34.469` | approximately 10 |
| `00:07:34.479 - 00:07:35.990` | programs that are |
| `00:07:36.000 - 00:07:38.870` | distributed with it but a real |
| `00:07:38.880 - 00:07:41.270` | usable linux or using unix system is |
| `00:07:41.280 - 00:07:44.070` | going to have lots and lots of apps |
| `00:07:44.080 - 00:07:47.350` | so let's uh go over some of the |
| `00:07:47.360 - 00:07:49.510` | system calls that are present i'll go |
| `00:07:49.520 - 00:07:51.189` | over these in more detail later when we |
| `00:07:51.199 - 00:07:53.270` | encounter them but i just wanted to kind |
| `00:07:53.280 - 00:07:55.350` | of list them out here so you could see |
| `00:07:55.360 - 00:07:57.430` | what we've got |
| `00:07:57.440 - 00:07:58.629` | so |
| `00:07:58.639 - 00:08:00.070` | fork |
| `00:08:00.080 - 00:08:02.469` | this these are familiar from any unix or |
| `00:08:02.479 - 00:08:03.749` | linux system |
| `00:08:03.759 - 00:08:06.950` | the parameters are slightly different um |
| `00:08:06.960 - 00:08:08.950` | in some cases but |
| `00:08:08.960 - 00:08:11.589` | the idea is is there |
| `00:08:11.599 - 00:08:13.029` | in |
| `00:08:13.039 - 00:08:14.790` | concept anyway |
| `00:08:14.800 - 00:08:17.830` | fork is used to create a new process |
| `00:08:17.840 - 00:08:19.749` | weight is used to |
| `00:08:19.759 - 00:08:22.869` | wait for a child process to terminate |
| `00:08:22.879 - 00:08:26.390` | exit is for terminating a process pipe |
| `00:08:26.400 - 00:08:28.070` | is for creating pipes |
| `00:08:28.080 - 00:08:30.150` | and then we've got open |
| `00:08:30.160 - 00:08:31.589` | close |
| `00:08:31.599 - 00:08:36.709` | read and write for dealing with files |
| `00:08:36.719 - 00:08:39.750` | we've got kill to terminate a process |
| `00:08:39.760 - 00:08:41.509` | we've got exec |
| `00:08:41.519 - 00:08:44.550` | which is passed a file name and we'll |
| `00:08:44.560 - 00:08:45.750` | read in that |
| `00:08:45.760 - 00:08:47.910` | file presumably it's an executable file |
| `00:08:47.920 - 00:08:49.829` | and we'll load it into |
| `00:08:49.839 - 00:08:51.590` | memory creating a new virtual address |
| `00:08:51.600 - 00:08:55.190` | space and execute it we can |
| `00:08:55.200 - 00:08:56.389` | make |
| `00:08:56.399 - 00:08:58.710` | inodes we can |
| `00:08:58.720 - 00:09:00.389` | create links |
| `00:09:00.399 - 00:09:03.350` | hard links and we can remove hard links |
| `00:09:03.360 - 00:09:04.470` | and |
| `00:09:04.480 - 00:09:07.430` | unlink files thereby possibly removing |
| `00:09:07.440 - 00:09:09.670` | them if it's the last link |
| `00:09:09.680 - 00:09:12.310` | we can get information about files |
| `00:09:12.320 - 00:09:14.870` | uh we can change directory so we do have |
| `00:09:14.880 - 00:09:16.310` | a notion of the current working |
| `00:09:16.320 - 00:09:17.750` | directory |
| `00:09:17.760 - 00:09:19.910` | dupe is used for copying file |
| `00:09:19.920 - 00:09:21.269` | descriptors |
| `00:09:21.279 - 00:09:22.389` | now we can |
| `00:09:22.399 - 00:09:26.550` | get program id sorry the process id for |
| `00:09:26.560 - 00:09:28.389` | the current process |
| `00:09:28.399 - 00:09:29.509` | we can |
| `00:09:29.519 - 00:09:30.870` | grow the heap |
| `00:09:30.880 - 00:09:33.430` | so that's this function here this system |
| `00:09:33.440 - 00:09:34.550` | call here |
| `00:09:34.560 - 00:09:36.870` | and we can put a process to sleep for a |
| `00:09:36.880 - 00:09:39.430` | while we can also see how long the |
| `00:09:39.440 - 00:09:41.030` | kernel has been running |
| `00:09:41.040 - 00:09:43.430` | so in the next videos and in this series |
| `00:09:43.440 - 00:09:45.110` | i'll be going through the code in quite |
| `00:09:45.120 - 00:09:47.269` | a bit more detail but i just wanted to |
| `00:09:47.279 - 00:09:49.590` | start with giving you an idea of what |
| `00:09:49.600 - 00:09:52.550` | this operating system kernel has |
| `00:09:52.560 - 00:09:55.350` | and what its capabilities are |
| `00:09:55.360 - 00:09:57.670` | so you can determine whether you want to |
| `00:09:57.680 - 00:09:59.590` | make the commitment for watching these |
| `00:09:59.600 - 00:10:01.590` | videos as i said |
| `00:10:01.600 - 00:10:03.509` | this series is not for the faint of |
| `00:10:03.519 - 00:10:05.990` | heart it's not for the amateurs it's for |
| `00:10:06.000 - 00:10:07.590` | people who really want to |
| `00:10:07.600 - 00:10:09.269` | look at an operating system kernel in |
| `00:10:09.279 - 00:10:12.069` | detail and understand |
| `00:10:12.079 - 00:10:14.389` | a rather large but not |
| `00:10:14.399 - 00:10:16.550` | too large body of code |
| `00:10:16.560 - 00:10:18.550` | okay let's get started with the next |
| `00:10:18.560 - 00:10:21.560` | video |
