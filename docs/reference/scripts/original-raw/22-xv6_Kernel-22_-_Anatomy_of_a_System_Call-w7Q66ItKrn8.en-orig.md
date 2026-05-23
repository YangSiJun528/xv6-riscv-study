# xv6 Kernel-22: Anatomy of a System Call

| Field | Value |
| --- | --- |
| Video ID | `w7Q66ItKrn8` |
| URL | <https://www.youtube.com/watch?v=w7Q66ItKrn8> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.880 - 00:00:02.790` | what exactly happens during a system |
| `00:00:02.800 - 00:00:04.710` | call |
| `00:00:04.720 - 00:00:06.789` | this video is titled anatomy of a system |
| `00:00:06.799 - 00:00:09.110` | call and in it i'll walk through all the |
| `00:00:09.120 - 00:00:11.270` | actions that occur whenever system call |
| `00:00:11.280 - 00:00:12.629` | is invoked |
| `00:00:12.639 - 00:00:15.669` | i'll look at the s break system call as |
| `00:00:15.679 - 00:00:17.109` | an example |
| `00:00:17.119 - 00:00:19.349` | s-break is found in unix and linux |
| `00:00:19.359 - 00:00:20.390` | systems |
| `00:00:20.400 - 00:00:21.349` | and |
| `00:00:21.359 - 00:00:23.509` | we're talking about the xv6 operating |
| `00:00:23.519 - 00:00:24.710` | system kernel |
| `00:00:24.720 - 00:00:27.509` | and that is a simplified version of unix |
| `00:00:27.519 - 00:00:29.589` | and linux |
| `00:00:29.599 - 00:00:30.870` | here are the functions and files that |
| `00:00:30.880 - 00:00:34.389` | we'll be discussing in the in this video |
| `00:00:34.399 - 00:00:36.069` | every user mode program that wants to |
| `00:00:36.079 - 00:00:38.709` | make a system call will need to include |
| `00:00:38.719 - 00:00:41.750` | this user.h file |
| `00:00:41.760 - 00:00:43.910` | and we have a number of assembly code |
| `00:00:43.920 - 00:00:45.150` | functions in |
| `00:00:45.160 - 00:00:49.510` | usys.s that are run in user mode |
| `00:00:49.520 - 00:00:52.310` | the definitions in syscall.h |
| `00:00:52.320 - 00:00:54.790` | are used both by the user mode code and |
| `00:00:54.800 - 00:00:56.630` | by the kernel code so this file is |
| `00:00:56.640 - 00:00:59.430` | shared between both user mode and kernel |
| `00:00:59.440 - 00:01:00.549` | code |
| `00:01:00.559 - 00:01:03.189` | once the trap occurs we will be thrown |
| `00:01:03.199 - 00:01:06.310` | into the code in the trampoline page |
| `00:01:06.320 - 00:01:08.469` | this is assembly language code |
| `00:01:08.479 - 00:01:11.429` | and the uservec code will then invoke |
| `00:01:11.439 - 00:01:13.830` | the user trap function |
| `00:01:13.840 - 00:01:15.670` | user trap function will determine that |
| `00:01:15.680 - 00:01:17.910` | we've got a system call and invoke the |
| `00:01:17.920 - 00:01:19.910` | function syscall |
| `00:01:19.920 - 00:01:21.910` | syscall will determine which system call |
| `00:01:21.920 - 00:01:24.630` | we want and it will in our case invoke |
| `00:01:24.640 - 00:01:26.789` | s-break to do the system call that we're |
| `00:01:26.799 - 00:01:28.550` | going to be looking at |
| `00:01:28.560 - 00:01:31.670` | s-break will then invoke grow proc which |
| `00:01:31.680 - 00:01:33.190` | will actually increase the size of the |
| `00:01:33.200 - 00:01:34.710` | address space |
| `00:01:34.720 - 00:01:37.429` | and then when everything returns syscall |
| `00:01:37.439 - 00:01:39.429` | will then |
| `00:01:39.439 - 00:01:41.590` | end uh by |
| `00:01:41.600 - 00:01:43.190` | going back to |
| `00:01:43.200 - 00:01:44.950` | usertrap rec |
| `00:01:44.960 - 00:01:47.510` | and usertrapret will then jump to |
| `00:01:47.520 - 00:01:49.749` | userret the assembly language code which |
| `00:01:49.759 - 00:01:52.710` | will execute the srep instruction and |
| `00:01:52.720 - 00:01:55.270` | bring us back into executing user mode |
| `00:01:55.280 - 00:01:56.709` | code |
| `00:01:56.719 - 00:01:58.389` | so let's begin with |
| `00:01:58.399 - 00:02:02.870` | user.h |
| `00:02:02.880 - 00:02:05.429` | any user mode program that wants to |
| `00:02:05.439 - 00:02:08.070` | invoke a system call needs to include |
| `00:02:08.080 - 00:02:09.990` | this file and it contains a number of |
| `00:02:10.000 - 00:02:13.030` | function prototypes so it contains |
| `00:02:13.040 - 00:02:15.589` | one prototype for each of the 21 system |
| `00:02:15.599 - 00:02:17.990` | calls and in particular we see |
| `00:02:18.000 - 00:02:21.670` | the prototype for s break here |
| `00:02:21.680 - 00:02:24.630` | s break is passed a single argument |
| `00:02:24.640 - 00:02:26.630` | and that argument is the amount to grow |
| `00:02:26.640 - 00:02:29.030` | the heap by it can be positive or it |
| `00:02:29.040 - 00:02:30.630` | could be negative if you want to shrink |
| `00:02:30.640 - 00:02:32.070` | the heap |
| `00:02:32.080 - 00:02:34.229` | it can also be zero |
| `00:02:34.239 - 00:02:36.229` | it returns the previous size of the heap |
| `00:02:36.239 - 00:02:37.670` | so if you want to know how big the heap |
| `00:02:37.680 - 00:02:40.949` | is you can just invoke it with zero |
| `00:02:40.959 - 00:02:43.750` | in the risk by risk five system |
| `00:02:43.760 - 00:02:46.550` | arguments are passed in registers a0 a1 |
| `00:02:46.560 - 00:02:50.390` | and a2 and so on so this function has |
| `00:02:50.400 - 00:02:53.990` | three arguments ours only has one |
| `00:02:54.000 - 00:02:56.790` | so only a zero would be used if if there |
| `00:02:56.800 - 00:02:59.750` | is a return value and all of the system |
| `00:02:59.760 - 00:03:01.990` | calls have a return value that would be |
| `00:03:02.000 - 00:03:04.790` | placed in a zero this function also |
| `00:03:04.800 - 00:03:07.030` | includes sorry this file |
| `00:03:07.040 - 00:03:08.550` | user.h |
| `00:03:08.560 - 00:03:10.309` | also includes a number of other |
| `00:03:10.319 - 00:03:13.509` | definitions for helper functions like |
| `00:03:13.519 - 00:03:16.470` | printf and stringlength and so on but we |
| `00:03:16.480 - 00:03:19.270` | won't talk about those here |
| `00:03:19.280 - 00:03:22.470` | so next let's uh ask which function gets |
| `00:03:22.480 - 00:03:26.550` | invoked when a user mode program calls |
| `00:03:26.560 - 00:03:28.550` | something like s-break |
| `00:03:28.560 - 00:03:31.509` | or fork or read or write or |
| `00:03:31.519 - 00:03:33.110` | any other system calls |
| `00:03:33.120 - 00:03:35.350` | well the answer is a short assembly |
| `00:03:35.360 - 00:03:38.309` | language program that runs in user mode |
| `00:03:38.319 - 00:03:39.430` | is invoked |
| `00:03:39.440 - 00:03:41.990` | those uh functions are also often called |
| `00:03:42.000 - 00:03:45.190` | stub routines and they're all collected |
| `00:03:45.200 - 00:03:49.110` | and all found in this file usys.s |
| `00:03:49.120 - 00:03:51.110` | this is an assembly language file that |
| `00:03:51.120 - 00:03:53.670` | contains a short stub routine for each |
| `00:03:53.680 - 00:03:56.710` | of the 21 system calls this file is |
| `00:03:56.720 - 00:03:59.110` | actually automatically generated by a |
| `00:03:59.120 - 00:04:01.910` | perl script usasis.pl |
| `00:04:01.920 - 00:04:03.670` | but i've shown here |
| `00:04:03.680 - 00:04:04.630` | what |
| `00:04:04.640 - 00:04:06.229` | goes into that file |
| `00:04:06.239 - 00:04:08.229` | it begins with an include which is |
| `00:04:08.239 - 00:04:09.830` | syscall.h |
| `00:04:09.840 - 00:04:11.589` | and let me just stop here because this |
| `00:04:11.599 - 00:04:13.670` | is called syscall.h is pretty |
| `00:04:13.680 - 00:04:15.509` | straightforward and i can dispense with |
| `00:04:15.519 - 00:04:17.349` | it quickly |
| `00:04:17.359 - 00:04:19.189` | syscall.h |
| `00:04:19.199 - 00:04:20.550` | associates a number with each of the |
| `00:04:20.560 - 00:04:22.230` | system calls |
| `00:04:22.240 - 00:04:24.550` | this number is how the user mode code |
| `00:04:24.560 - 00:04:26.790` | communicates with the |
| `00:04:26.800 - 00:04:29.430` | kernel as to which system call is |
| `00:04:29.440 - 00:04:31.590` | desired in our case |
| `00:04:31.600 - 00:04:33.189` | s break is |
| `00:04:33.199 - 00:04:35.430` | 12. okay and so |
| `00:04:35.440 - 00:04:38.710` | we include those definitions here |
| `00:04:38.720 - 00:04:40.870` | and then for each one of the 21 system |
| `00:04:40.880 - 00:04:42.710` | calls we have these five lines in the |
| `00:04:42.720 - 00:04:45.590` | file i've only shown the code the stub |
| `00:04:45.600 - 00:04:48.390` | routine for s-break but it's repeated |
| `00:04:48.400 - 00:04:50.550` | once for each of the other 20 system |
| `00:04:50.560 - 00:04:52.150` | calls |
| `00:04:52.160 - 00:04:54.390` | and what does this dub function look |
| `00:04:54.400 - 00:04:56.790` | like well it's got a label and a return |
| `00:04:56.800 - 00:04:58.790` | so it's like any function |
| `00:04:58.800 - 00:05:01.510` | it's also got a global which exports |
| `00:05:01.520 - 00:05:03.110` | that label so it's visible in the |
| `00:05:03.120 - 00:05:04.790` | linking phase |
| `00:05:04.800 - 00:05:06.629` | but primarily it has these three |
| `00:05:06.639 - 00:05:09.029` | instructions load immediate |
| `00:05:09.039 - 00:05:10.950` | e call in red |
| `00:05:10.960 - 00:05:13.830` | load immediate will move into register |
| `00:05:13.840 - 00:05:15.189` | a7 |
| `00:05:15.199 - 00:05:19.350` | some value in this case we remember that |
| `00:05:19.360 - 00:05:21.590` | the s break constant |
| `00:05:21.600 - 00:05:25.510` | was 12. so this just moves 12 into a7 |
| `00:05:25.520 - 00:05:27.749` | and then we execute the environment call |
| `00:05:27.759 - 00:05:29.830` | or e-call instruction which performs the |
| `00:05:29.840 - 00:05:31.590` | system call |
| `00:05:31.600 - 00:05:34.310` | at the point which any of these global |
| `00:05:34.320 - 00:05:37.270` | these uh stub functions is called we can |
| `00:05:37.280 - 00:05:39.830` | expect the arguments to be in registers |
| `00:05:39.840 - 00:05:41.670` | a0 through a5 |
| `00:05:41.680 - 00:05:43.670` | we can accommodate up to six arguments |
| `00:05:43.680 - 00:05:45.430` | to any system call |
| `00:05:45.440 - 00:05:46.870` | and |
| `00:05:46.880 - 00:05:49.029` | those registers will be unchanged by |
| `00:05:49.039 - 00:05:50.710` | this load immediate and so they'll still |
| `00:05:50.720 - 00:05:53.430` | be valid and the kernel will fetch the |
| `00:05:53.440 - 00:05:55.510` | values that it saved for those registers |
| `00:05:55.520 - 00:05:58.629` | and that's how it accesses the arguments |
| `00:05:58.639 - 00:06:01.189` | the kernel code will also place or |
| `00:06:01.199 - 00:06:03.430` | overwrite the a0 register with whatever |
| `00:06:03.440 - 00:06:05.270` | the return value should be |
| `00:06:05.280 - 00:06:07.590` | so after the kernel comes back that is |
| `00:06:07.600 - 00:06:10.629` | after we resume executing in user mode |
| `00:06:10.639 - 00:06:13.590` | at this point a0 will contain our result |
| `00:06:13.600 - 00:06:15.350` | and we don't touch it we just do the |
| `00:06:15.360 - 00:06:17.670` | return instruction |
| `00:06:17.680 - 00:06:18.629` | so |
| `00:06:18.639 - 00:06:20.390` | in risk five |
| `00:06:20.400 - 00:06:22.309` | whenever a function is called the return |
| `00:06:22.319 - 00:06:25.270` | address is placed in register r a return |
| `00:06:25.280 - 00:06:26.230` | address |
| `00:06:26.240 - 00:06:27.670` | and then the return instruction just |
| `00:06:27.680 - 00:06:29.990` | simply copies the value in r a to the |
| `00:06:30.000 - 00:06:31.990` | program counter |
| `00:06:32.000 - 00:06:33.350` | the |
| `00:06:33.360 - 00:06:35.990` | kernel will preserve all registers |
| `00:06:36.000 - 00:06:39.350` | except for a0 and so we can be sure that |
| `00:06:39.360 - 00:06:41.430` | at this point the value that was in our |
| `00:06:41.440 - 00:06:44.790` | a here will still be there and so this |
| `00:06:44.800 - 00:06:48.390` | can return properly |
| `00:06:48.400 - 00:06:51.110` | okay so we're executing in user mode and |
| `00:06:51.120 - 00:06:54.390` | at some point an e-call instruction is |
| `00:06:54.400 - 00:06:56.950` | executed and that causes a trap after |
| `00:06:56.960 - 00:07:00.230` | that we execute in kernel mode and then |
| `00:07:00.240 - 00:07:01.510` | finally |
| `00:07:01.520 - 00:07:03.830` | we execute the sred instruction in |
| `00:07:03.840 - 00:07:06.150` | kernel mode and that returns us to user |
| `00:07:06.160 - 00:07:08.790` | mode and we resume execution directly |
| `00:07:08.800 - 00:07:11.510` | after the ecall instruction which is the |
| `00:07:11.520 - 00:07:13.990` | return instruction in this dub routine |
| `00:07:14.000 - 00:07:15.270` | so let's look at that in a little bit |
| `00:07:15.280 - 00:07:16.870` | more detail |
| `00:07:16.880 - 00:07:18.790` | here is the process from the trap all |
| `00:07:18.800 - 00:07:22.390` | the way down to the s red instruction |
| `00:07:22.400 - 00:07:25.189` | a trap will change the mode into kernel |
| `00:07:25.199 - 00:07:27.350` | mode disable interrupts |
| `00:07:27.360 - 00:07:30.950` | save the program counter and jump to |
| `00:07:30.960 - 00:07:31.830` | the |
| `00:07:31.840 - 00:07:33.909` | uservec function |
| `00:07:33.919 - 00:07:38.469` | user func uservec is located in the |
| `00:07:38.479 - 00:07:40.710` | trampoline page and it will save the |
| `00:07:40.720 - 00:07:42.469` | user registers |
| `00:07:42.479 - 00:07:44.550` | load the kernel registers |
| `00:07:44.560 - 00:07:45.510` | and |
| `00:07:45.520 - 00:07:47.589` | change to the kernel's virtual address |
| `00:07:47.599 - 00:07:48.469` | space |
| `00:07:48.479 - 00:07:51.350` | and finally it will jump to user trap |
| `00:07:51.360 - 00:07:53.110` | we'll look at user trap in more detail |
| `00:07:53.120 - 00:07:54.550` | but it will figure out that it's a |
| `00:07:54.560 - 00:07:57.749` | system call and so on but eventually |
| `00:07:57.759 - 00:08:00.710` | user trap will call user trap ret |
| `00:08:00.720 - 00:08:03.029` | and that will then |
| `00:08:03.039 - 00:08:06.150` | invoke user rep which is assembly code |
| `00:08:06.160 - 00:08:07.430` | and that will |
| `00:08:07.440 - 00:08:10.469` | finish the process and execute the srad |
| `00:08:10.479 - 00:08:12.469` | instruction in particular we'll go back |
| `00:08:12.479 - 00:08:14.710` | to the user's address space restore the |
| `00:08:14.720 - 00:08:16.309` | user's registers |
| `00:08:16.319 - 00:08:18.469` | and then the s-red instruction itself |
| `00:08:18.479 - 00:08:19.350` | will |
| `00:08:19.360 - 00:08:20.710` | change the |
| `00:08:20.720 - 00:08:23.430` | mode to user mode and restore the |
| `00:08:23.440 - 00:08:25.670` | program counter to whatever was saved |
| `00:08:25.680 - 00:08:29.270` | and re-enable interrupts |
| `00:08:29.280 - 00:08:31.189` | here's a diagram i used in an earlier |
| `00:08:31.199 - 00:08:33.269` | video showing the same thing in a little |
| `00:08:33.279 - 00:08:35.029` | bit more detail |
| `00:08:35.039 - 00:08:36.870` | from the trap |
| `00:08:36.880 - 00:08:38.829` | to the esret |
| `00:08:38.839 - 00:08:41.909` | instruction again you see |
| `00:08:41.919 - 00:08:43.190` | the |
| `00:08:43.200 - 00:08:45.990` | assembly code in the trampoline page |
| `00:08:46.000 - 00:08:48.790` | at uservec being invoked to save the |
| `00:08:48.800 - 00:08:51.509` | state of the process |
| `00:08:51.519 - 00:08:54.949` | it jumps to the user trap function |
| `00:08:54.959 - 00:08:57.509` | which then does some stuff and |
| `00:08:57.519 - 00:09:00.230` | eventually calls user trap ret |
| `00:09:00.240 - 00:09:02.630` | and that restores |
| `00:09:02.640 - 00:09:05.269` | the state of the user process |
| `00:09:05.279 - 00:09:07.430` | along with user rep and ultimately |
| `00:09:07.440 - 00:09:10.790` | executes the esret instruction |
| `00:09:10.800 - 00:09:13.430` | so what happens in user trap |
| `00:09:13.440 - 00:09:14.389` | well |
| `00:09:14.399 - 00:09:16.470` | we have a big if statement that |
| `00:09:16.480 - 00:09:18.949` | determines the cause of the trap is it |
| `00:09:18.959 - 00:09:21.750` | an exception or is a device requiring |
| `00:09:21.760 - 00:09:24.710` | attention or is it a timer interrupt or |
| `00:09:24.720 - 00:09:26.389` | is it a system call |
| `00:09:26.399 - 00:09:28.710` | if it's a system call we enable |
| `00:09:28.720 - 00:09:31.430` | interrupts and then invoke the syscall |
| `00:09:31.440 - 00:09:34.150` | function and after that returns |
| `00:09:34.160 - 00:09:36.630` | it invokes the user trap ret to begin |
| `00:09:36.640 - 00:09:38.310` | the process of going back to the user |
| `00:09:38.320 - 00:09:40.630` | mode code |
| `00:09:40.640 - 00:09:43.910` | we also check the killed flag |
| `00:09:43.920 - 00:09:46.470` | there's a boolean value that is set |
| `00:09:46.480 - 00:09:49.509` | if somebody wants this process to die |
| `00:09:49.519 - 00:09:52.470` | and if it was set then we invoke exit |
| `00:09:52.480 - 00:09:55.190` | i've also hinted at here that there |
| `00:09:55.200 - 00:09:58.310` | could be some process switching going on |
| `00:09:58.320 - 00:10:01.750` | the moment we re-enable interrupts |
| `00:10:01.760 - 00:10:03.430` | it's possible that a timer interrupt |
| `00:10:03.440 - 00:10:05.670` | could occur or a device could require |
| `00:10:05.680 - 00:10:07.910` | attention so the scheduler could get |
| `00:10:07.920 - 00:10:10.470` | involved and i tried to indicate that |
| `00:10:10.480 - 00:10:13.030` | over here to say that any time between |
| `00:10:13.040 - 00:10:15.110` | the interrupts being enabled here |
| `00:10:15.120 - 00:10:17.350` | and the interrupts being disabled at the |
| `00:10:17.360 - 00:10:19.829` | beginning of user trap ret it's possible |
| `00:10:19.839 - 00:10:22.630` | that we could switch to another process |
| `00:10:22.640 - 00:10:24.310` | but eventually we'll come back and |
| `00:10:24.320 - 00:10:26.310` | finish executing whatever code is |
| `00:10:26.320 - 00:10:29.110` | involved with the system call |
| `00:10:29.120 - 00:10:32.150` | so now let's take a look at the |
| `00:10:32.160 - 00:10:35.269` | user trap function in more detail |
| `00:10:35.279 - 00:10:37.110` | so i'm not going to go over everything |
| `00:10:37.120 - 00:10:40.550` | here but |
| `00:10:40.560 - 00:10:42.470` | we |
| `00:10:42.480 - 00:10:44.230` | basically have an if statement that |
| `00:10:44.240 - 00:10:47.269` | looks at the status and control register |
| `00:10:47.279 - 00:10:49.750` | called s-cause to determine whether it's |
| `00:10:49.760 - 00:10:51.110` | a system call |
| `00:10:51.120 - 00:10:53.110` | or something else and so we have some |
| `00:10:53.120 - 00:10:54.310` | other stuff down here in the if |
| `00:10:54.320 - 00:10:55.269` | statement |
| `00:10:55.279 - 00:10:58.069` | but with risk five the s-cos will have a |
| `00:10:58.079 - 00:11:01.829` | value of eight if it's a system call so |
| `00:11:01.839 - 00:11:03.750` | that's what happens here we |
| `00:11:03.760 - 00:11:05.670` | basically execute |
| `00:11:05.680 - 00:11:08.230` | the system call function |
| `00:11:08.240 - 00:11:10.630` | first we check the killed flag to see |
| `00:11:10.640 - 00:11:13.910` | whether someone has set it and if so we |
| `00:11:13.920 - 00:11:16.310` | invoke the exit function |
| `00:11:16.320 - 00:11:18.550` | exit will never return so if that |
| `00:11:18.560 - 00:11:20.470` | happens this process will stop right |
| `00:11:20.480 - 00:11:24.230` | here but assuming it continues going |
| `00:11:24.240 - 00:11:27.910` | we then look at the epc field in the |
| `00:11:27.920 - 00:11:29.990` | trap frame |
| `00:11:30.000 - 00:11:32.470` | when the trap occurred the |
| `00:11:32.480 - 00:11:34.389` | code and userback will save all the |
| `00:11:34.399 - 00:11:35.509` | registers |
| `00:11:35.519 - 00:11:38.069` | and the program counter will have gotten |
| `00:11:38.079 - 00:11:41.110` | saved in the epc field |
| `00:11:41.120 - 00:11:42.949` | the program counter will point to the |
| `00:11:42.959 - 00:11:45.509` | e-call instruction itself and we need to |
| `00:11:45.519 - 00:11:47.990` | advance it past the e-call instruction |
| `00:11:48.000 - 00:11:49.750` | which is four bytes |
| `00:11:49.760 - 00:11:51.829` | to the next instruction that's |
| `00:11:51.839 - 00:11:54.230` | presumably the return instruction in the |
| `00:11:54.240 - 00:11:57.829` | stub routine the |
| `00:11:57.839 - 00:12:00.470` | e-call instruction is four bytes so we |
| `00:12:00.480 - 00:12:02.069` | advance it to point to the next |
| `00:12:02.079 - 00:12:03.910` | instruction so when we return we'll |
| `00:12:03.920 - 00:12:05.269` | execute that |
| `00:12:05.279 - 00:12:07.670` | return instruction |
| `00:12:07.680 - 00:12:10.150` | here we turn interrupts on and then we |
| `00:12:10.160 - 00:12:11.990` | invoke this call |
| `00:12:12.000 - 00:12:15.110` | after syscall returns down here we check |
| `00:12:15.120 - 00:12:17.829` | the killed flag again and perhaps exit |
| `00:12:17.839 - 00:12:19.430` | right there |
| `00:12:19.440 - 00:12:22.870` | the uh which device would be set |
| `00:12:22.880 - 00:12:24.949` | if there's a device interrupt |
| `00:12:24.959 - 00:12:27.590` | but we initialize it up here to zero and |
| `00:12:27.600 - 00:12:29.990` | it's not changed so we will not execute |
| `00:12:30.000 - 00:12:32.470` | this yield function here |
| `00:12:32.480 - 00:12:34.389` | and then continuing uh with this |
| `00:12:34.399 - 00:12:38.389` | function uh after the yield we call user |
| `00:12:38.399 - 00:12:41.350` | trap rep and then that |
| `00:12:41.360 - 00:12:43.110` | which is shown right here basically |
| `00:12:43.120 - 00:12:44.710` | restores the state |
| `00:12:44.720 - 00:12:47.190` | and ends up going back to |
| `00:12:47.200 - 00:12:48.550` | the |
| `00:12:48.560 - 00:12:50.230` | user |
| `00:12:50.240 - 00:12:52.389` | rep function down here |
| `00:12:52.399 - 00:12:55.590` | and returning to the user mode code |
| `00:12:55.600 - 00:12:58.230` | after the function user trap figures out |
| `00:12:58.240 - 00:13:00.069` | that the cause of the trap was a system |
| `00:13:00.079 - 00:13:03.430` | call it will invoke the syscall function |
| `00:13:03.440 - 00:13:05.069` | which is located in the syscall |
| `00:13:05.079 - 00:13:07.269` | syscall.c file |
| `00:13:07.279 - 00:13:10.069` | and here's what happens |
| `00:13:10.079 - 00:13:11.829` | remember at the time of the trap |
| `00:13:11.839 - 00:13:14.069` | the code will save all of the registers |
| `00:13:14.079 - 00:13:15.910` | of the user mode process |
| `00:13:15.920 - 00:13:16.870` | and |
| `00:13:16.880 - 00:13:19.030` | the stub function has preloaded into |
| `00:13:19.040 - 00:13:20.949` | register a7 |
| `00:13:20.959 - 00:13:23.670` | a number in the case of s break it would |
| `00:13:23.680 - 00:13:25.190` | be 12 |
| `00:13:25.200 - 00:13:26.949` | a number which indicates which system |
| `00:13:26.959 - 00:13:28.470` | call is required |
| `00:13:28.480 - 00:13:30.949` | so the first thing we do is we get a |
| `00:13:30.959 - 00:13:33.990` | pointer to the proc structure and then |
| `00:13:34.000 - 00:13:35.430` | we chase it to |
| `00:13:35.440 - 00:13:37.269` | the trap frame |
| `00:13:37.279 - 00:13:41.590` | and look at the a7 the register a7 field |
| `00:13:41.600 - 00:13:43.110` | and retrieve that |
| `00:13:43.120 - 00:13:44.949` | okay then what we're going to do is |
| `00:13:44.959 - 00:13:47.430` | we're going to use that as an index into |
| `00:13:47.440 - 00:13:48.550` | an array |
| `00:13:48.560 - 00:13:49.590` | so |
| `00:13:49.600 - 00:13:51.350` | let me take a look at what occurs |
| `00:13:51.360 - 00:13:53.829` | earlier in this file |
| `00:13:53.839 - 00:13:56.069` | this is a collection of function |
| `00:13:56.079 - 00:13:59.350` | prototypes and here we see s break |
| `00:13:59.360 - 00:14:00.949` | all lowercase this is the function we're |
| `00:14:00.959 - 00:14:04.550` | going to be calling to get the work done |
| `00:14:04.560 - 00:14:07.189` | and then we have an array this array is |
| `00:14:07.199 - 00:14:09.430` | called syscalls and it's a bunch of |
| `00:14:09.440 - 00:14:12.069` | pointers to functions |
| `00:14:12.079 - 00:14:13.269` | and |
| `00:14:13.279 - 00:14:14.150` | so |
| `00:14:14.160 - 00:14:16.389` | for number 12 |
| `00:14:16.399 - 00:14:19.189` | where is it here it's uh right here uh |
| `00:14:19.199 - 00:14:22.629` | we have the value of a pointer to the |
| `00:14:22.639 - 00:14:23.910` | function |
| `00:14:23.920 - 00:14:26.310` | cis s break okay and |
| `00:14:26.320 - 00:14:27.750` | uh even though these aren't in quite the |
| `00:14:27.760 - 00:14:30.470` | same order all 21 |
| `00:14:30.480 - 00:14:32.150` | system calls are represented with |
| `00:14:32.160 - 00:14:34.949` | functions to execute the system call |
| `00:14:34.959 - 00:14:36.150` | and |
| `00:14:36.160 - 00:14:39.590` | uh the array has all 12 entries for each |
| `00:14:39.600 - 00:14:42.150` | one of those so that's what's going on |
| `00:14:42.160 - 00:14:43.110` | here |
| `00:14:43.120 - 00:14:47.350` | in syscall we look up the |
| `00:14:47.360 - 00:14:49.030` | and get the pointer to the function and |
| `00:14:49.040 - 00:14:51.030` | then we invoke that function |
| `00:14:51.040 - 00:14:53.509` | okay it's past nothing and it's going to |
| `00:14:53.519 - 00:14:55.030` | return |
| `00:14:55.040 - 00:14:56.550` | a value |
| `00:14:56.560 - 00:14:58.550` | okay and that value will be stored in |
| `00:14:58.560 - 00:14:59.910` | the trap frame |
| `00:14:59.920 - 00:15:03.590` | in a0 overwriting whatever was in a zero |
| `00:15:03.600 - 00:15:05.189` | okay so that's where we actually call |
| `00:15:05.199 - 00:15:07.110` | the s break function |
| `00:15:07.120 - 00:15:09.829` | before we do the call we just check to |
| `00:15:09.839 - 00:15:11.590` | make sure that the user mode code is |
| `00:15:11.600 - 00:15:13.750` | providing a legal number so we check to |
| `00:15:13.760 - 00:15:15.590` | make sure it's greater than zero |
| `00:15:15.600 - 00:15:16.949` | and that |
| `00:15:16.959 - 00:15:20.550` | it's an entry in the syscalls |
| `00:15:20.560 - 00:15:21.670` | array |
| `00:15:21.680 - 00:15:23.110` | and |
| `00:15:23.120 - 00:15:24.230` | that that |
| `00:15:24.240 - 00:15:26.310` | pointer is uh has been initialized to |
| `00:15:26.320 - 00:15:27.910` | something and is not null |
| `00:15:27.920 - 00:15:31.269` | so if it's all okay we uh invoke it but |
| `00:15:31.279 - 00:15:33.910` | if the user has provided a bad system |
| `00:15:33.920 - 00:15:35.030` | call number |
| `00:15:35.040 - 00:15:37.189` | then we print a message |
| `00:15:37.199 - 00:15:38.790` | normally the kernel wouldn't just |
| `00:15:38.800 - 00:15:40.470` | directly print a message for this sort |
| `00:15:40.480 - 00:15:42.150` | of thing but it would just return an |
| `00:15:42.160 - 00:15:45.430` | error code so we do see the error code |
| `00:15:45.440 - 00:15:47.670` | being returned we're writing a minus one |
| `00:15:47.680 - 00:15:51.670` | into a0 but we're also printing uh some |
| `00:15:51.680 - 00:15:53.990` | information here so we can debug both |
| `00:15:54.000 - 00:15:56.470` | the kernel and the user mode program the |
| `00:15:56.480 - 00:15:59.189` | process id the processes name |
| `00:15:59.199 - 00:16:00.710` | and the number that was actually |
| `00:16:00.720 - 00:16:02.550` | provided |
| `00:16:02.560 - 00:16:05.189` | okay so in this file we have a couple of |
| `00:16:05.199 - 00:16:06.629` | other functions |
| `00:16:06.639 - 00:16:09.110` | so i want to look at |
| `00:16:09.120 - 00:16:10.949` | this first one |
| `00:16:10.959 - 00:16:12.629` | these functions are helper functions |
| `00:16:12.639 - 00:16:14.550` | that will be used in |
| `00:16:14.560 - 00:16:16.710` | functions like sysbreak that implement |
| `00:16:16.720 - 00:16:18.389` | the actual system call |
| `00:16:18.399 - 00:16:21.670` | so we need a way to fetch the arguments |
| `00:16:21.680 - 00:16:24.310` | so our raw |
| `00:16:24.320 - 00:16:26.389` | will be passed a number that's the |
| `00:16:26.399 - 00:16:28.629` | number of the argument you want |
| `00:16:28.639 - 00:16:31.110` | zero through five zero so we're |
| `00:16:31.120 - 00:16:32.710` | switching on zero |
| `00:16:32.720 - 00:16:35.509` | through five here and all it does is it |
| `00:16:35.519 - 00:16:38.629` | goes into the trap frame and returns the |
| `00:16:38.639 - 00:16:39.670` | value |
| `00:16:39.680 - 00:16:44.389` | of the users register a0 a1 a2 3 4 or 5. |
| `00:16:44.399 - 00:16:46.069` | and |
| `00:16:46.079 - 00:16:49.350` | then it returns that value |
| `00:16:49.360 - 00:16:52.389` | uh that that is used by this next |
| `00:16:52.399 - 00:16:56.470` | function argent and argent is uh just |
| `00:16:56.480 - 00:16:57.749` | going to call |
| `00:16:57.759 - 00:17:00.150` | our raw but it's also going to store |
| `00:17:00.160 - 00:17:01.990` | that value it's passed a pointer to an |
| `00:17:02.000 - 00:17:03.030` | integer |
| `00:17:03.040 - 00:17:05.590` | and so it stores it in that |
| `00:17:05.600 - 00:17:07.909` | integer variable |
| `00:17:07.919 - 00:17:09.590` | and we have a couple of others there's |
| `00:17:09.600 - 00:17:10.549` | uh |
| `00:17:10.559 - 00:17:12.789` | our address |
| `00:17:12.799 - 00:17:13.990` | it's |
| `00:17:14.000 - 00:17:15.990` | exactly the same |
| `00:17:16.000 - 00:17:17.909` | code |
| `00:17:17.919 - 00:17:21.590` | so the only difference is that uh the |
| `00:17:21.600 - 00:17:23.510` | variable that is being stored into it |
| `00:17:23.520 - 00:17:27.270` | has a type uh uen64 as opposed to an |
| `00:17:27.280 - 00:17:28.870` | integer here |
| `00:17:28.880 - 00:17:30.070` | there's also |
| `00:17:30.080 - 00:17:33.350` | uh one for arg |
| `00:17:33.360 - 00:17:34.390` | string |
| `00:17:34.400 - 00:17:35.830` | okay so |
| `00:17:35.840 - 00:17:38.230` | fetch the nth word size system call |
| `00:17:38.240 - 00:17:40.310` | argument okay so that's the number |
| `00:17:40.320 - 00:17:42.150` | between zero and five |
| `00:17:42.160 - 00:17:45.029` | as a null terminated string |
| `00:17:45.039 - 00:17:46.870` | copy it into buffer |
| `00:17:46.880 - 00:17:47.750` | and |
| `00:17:47.760 - 00:17:49.909` | at most max characters so |
| `00:17:49.919 - 00:17:51.750` | uh in addition to the number of the |
| `00:17:51.760 - 00:17:53.350` | argument we want we passed a pointer to |
| `00:17:53.360 - 00:17:55.669` | some buffer and its size |
| `00:17:55.679 - 00:17:57.270` | and it returns the string length of |
| `00:17:57.280 - 00:17:58.549` | bouquet |
| `00:17:58.559 - 00:18:02.150` | and -1 if there's a problem so here we |
| `00:18:02.160 - 00:18:03.830` | are |
| `00:18:03.840 - 00:18:05.669` | fetching |
| `00:18:05.679 - 00:18:08.150` | the address here |
| `00:18:08.160 - 00:18:09.909` | and |
| `00:18:09.919 - 00:18:11.830` | putting in a local variable address and |
| `00:18:11.840 - 00:18:15.350` | then we have this function fetch string |
| `00:18:15.360 - 00:18:17.590` | to do the work and fetch string is right |
| `00:18:17.600 - 00:18:19.110` | here |
| `00:18:19.120 - 00:18:21.430` | fetch the null terminated string at |
| `00:18:21.440 - 00:18:24.789` | address from the current process |
| `00:18:24.799 - 00:18:28.150` | and it returns the length of the string |
| `00:18:28.160 - 00:18:30.789` | and -1 for an error |
| `00:18:30.799 - 00:18:32.950` | so here we see |
| `00:18:32.960 - 00:18:36.230` | that it's calling copy in string we |
| `00:18:36.240 - 00:18:38.310` | discussed that in a previous video when |
| `00:18:38.320 - 00:18:40.710` | i discussed the virtual memory functions |
| `00:18:40.720 - 00:18:43.190` | helper functions so basically it's going |
| `00:18:43.200 - 00:18:45.029` | to use the |
| `00:18:45.039 - 00:18:47.270` | current procedures page table |
| `00:18:47.280 - 00:18:50.070` | and here's the address in kernel memory |
| `00:18:50.080 - 00:18:52.390` | and here's the virtual address |
| `00:18:52.400 - 00:18:54.070` | and the maximum number of characters to |
| `00:18:54.080 - 00:18:55.909` | copy and |
| `00:18:55.919 - 00:18:57.510` | then it's going to copy that return the |
| `00:18:57.520 - 00:18:59.270` | string links |
| `00:18:59.280 - 00:19:00.789` | there's also another helper function |
| `00:19:00.799 - 00:19:03.190` | called fetch address |
| `00:19:03.200 - 00:19:05.110` | and the idea is the argument to the |
| `00:19:05.120 - 00:19:08.870` | system call will be an address and we |
| `00:19:08.880 - 00:19:10.470` | want to go into the virtual address |
| `00:19:10.480 - 00:19:14.710` | space and fetch a 64-bit value from the |
| `00:19:14.720 - 00:19:17.190` | virtual address space so this is past |
| `00:19:17.200 - 00:19:19.029` | the virtual address |
| `00:19:19.039 - 00:19:20.070` | and |
| `00:19:20.080 - 00:19:23.029` | the value that's fetched will be stored |
| `00:19:23.039 - 00:19:25.830` | and it will be stored at the location |
| `00:19:25.840 - 00:19:28.549` | pointed to by this pointer here |
| `00:19:28.559 - 00:19:31.909` | so to do this we see that the copy in |
| `00:19:31.919 - 00:19:34.390` | function is used |
| `00:19:34.400 - 00:19:36.630` | we passed a page table to |
| `00:19:36.640 - 00:19:38.230` | indicate which virtual address space it |
| `00:19:38.240 - 00:19:39.029` | is |
| `00:19:39.039 - 00:19:42.390` | and here is ip that's the place where |
| `00:19:42.400 - 00:19:44.630` | we're supposed to store the uh eight |
| `00:19:44.640 - 00:19:47.110` | bytes right this thing will be 64 bits |
| `00:19:47.120 - 00:19:50.070` | or or eight bytes and here's the virtual |
| `00:19:50.080 - 00:19:52.789` | address and here is the number of bytes |
| `00:19:52.799 - 00:19:54.630` | that we need to copy |
| `00:19:54.640 - 00:19:55.750` | and |
| `00:19:55.760 - 00:19:58.150` | well ip is a pointer to |
| `00:19:58.160 - 00:20:00.310` | 64-bit value so the size of that is |
| `00:20:00.320 - 00:20:03.270` | eight bytes so this is eight here |
| `00:20:03.280 - 00:20:05.270` | we see it expressed slightly differently |
| `00:20:05.280 - 00:20:08.470` | here so this is also 8 bytes |
| `00:20:08.480 - 00:20:10.390` | first i want to say that if there's a |
| `00:20:10.400 - 00:20:13.510` | problem we just return -1 |
| `00:20:13.520 - 00:20:15.909` | otherwise we return 0. |
| `00:20:15.919 - 00:20:17.430` | here we're checking to make sure that |
| `00:20:17.440 - 00:20:19.110` | the address that the |
| `00:20:19.120 - 00:20:21.190` | user mode program has provided is |
| `00:20:21.200 - 00:20:23.669` | correct and we see that we're checking |
| `00:20:23.679 - 00:20:25.510` | to make sure that the address well it |
| `00:20:25.520 - 00:20:27.909` | better be somewhere between zero and the |
| `00:20:27.919 - 00:20:29.909` | size of the address space right the top |
| `00:20:29.919 - 00:20:32.390` | of the heap the break uh point so we're |
| `00:20:32.400 - 00:20:34.950` | checking to make sure that it is less |
| `00:20:34.960 - 00:20:38.070` | than the break address |
| `00:20:38.080 - 00:20:40.789` | okay and then uh we are adding well |
| `00:20:40.799 - 00:20:44.070` | eight bytes to that and again we are uh |
| `00:20:44.080 - 00:20:47.830` | looking to see whether uh it is not too |
| `00:20:47.840 - 00:20:50.070` | large okay |
| `00:20:50.080 - 00:20:51.669` | and if there's a problem with it we |
| `00:20:51.679 - 00:20:53.590` | return minus 1. |
| `00:20:53.600 - 00:20:54.950` | now you might say |
| `00:20:54.960 - 00:20:57.430` | why in the world would we perform this |
| `00:20:57.440 - 00:20:59.590` | check because clearly if this is true |
| `00:20:59.600 - 00:21:02.870` | and then you add 8 to address this is |
| `00:21:02.880 - 00:21:05.270` | going to definitely be true |
| `00:21:05.280 - 00:21:07.830` | but here is the thing |
| `00:21:07.840 - 00:21:09.510` | what about wrap around we're using |
| `00:21:09.520 - 00:21:12.070` | unsigned integers what if address is way |
| `00:21:12.080 - 00:21:14.710` | up at the very top of the |
| `00:21:14.720 - 00:21:17.430` | 64-bit values and when you add 8 to it |
| `00:21:17.440 - 00:21:19.590` | it wraps around so this one could be |
| `00:21:19.600 - 00:21:20.630` | okay |
| `00:21:20.640 - 00:21:22.950` | while this one is not but if this one is |
| `00:21:22.960 - 00:21:24.549` | below the size of the virtual address |
| `00:21:24.559 - 00:21:27.190` | space which is limited to |
| `00:21:27.200 - 00:21:28.870` | substantially limited |
| `00:21:28.880 - 00:21:31.669` | then we won't have a wrap around here so |
| `00:21:31.679 - 00:21:33.590` | this is the kind of thing where you have |
| `00:21:33.600 - 00:21:35.750` | security flaws in kernels so you need to |
| `00:21:35.760 - 00:21:37.750` | be really really careful with all |
| `00:21:37.760 - 00:21:40.870` | possible values that the user code could |
| `00:21:40.880 - 00:21:43.430` | provide and so this is an example why |
| `00:21:43.440 - 00:21:48.950` | this second check is actually necessary |
| `00:21:48.960 - 00:21:51.029` | now we're ready to look at the s-break |
| `00:21:51.039 - 00:21:53.669` | function that actually does the work |
| `00:21:53.679 - 00:21:56.950` | it's located in the file sysproc.c |
| `00:21:56.960 - 00:21:58.789` | along with a bunch of other functions |
| `00:21:58.799 - 00:22:00.630` | for other system calls |
| `00:22:00.640 - 00:22:02.549` | some are pretty simple like here's the |
| `00:22:02.559 - 00:22:03.830` | one for |
| `00:22:03.840 - 00:22:05.669` | the get pid |
| `00:22:05.679 - 00:22:08.710` | and all it does is simply go to the proc |
| `00:22:08.720 - 00:22:11.750` | structure and fetch the process id and |
| `00:22:11.760 - 00:22:13.270` | return that |
| `00:22:13.280 - 00:22:14.549` | each of these |
| `00:22:14.559 - 00:22:17.350` | functions is passed no arguments and |
| `00:22:17.360 - 00:22:19.750` | returns a return value that will go into |
| `00:22:19.760 - 00:22:21.909` | a0 |
| `00:22:21.919 - 00:22:25.110` | other functions or are more complicated |
| `00:22:25.120 - 00:22:28.230` | here we see the fork system call and |
| `00:22:28.240 - 00:22:29.990` | it's just calling this function fork |
| `00:22:30.000 - 00:22:32.230` | which is located elsewhere to do all the |
| `00:22:32.240 - 00:22:33.909` | work |
| `00:22:33.919 - 00:22:36.549` | let's go to let's go down here |
| `00:22:36.559 - 00:22:41.510` | to where we see uh |
| `00:22:41.520 - 00:22:43.110` | this is |
| `00:22:43.120 - 00:22:45.270` | so let's go down to where we see uh |
| `00:22:45.280 - 00:22:49.990` | sysbreak and we see that it will call |
| `00:22:50.000 - 00:22:52.789` | the function grow proc to do the work |
| `00:22:52.799 - 00:22:55.669` | but first it has a single argument |
| `00:22:55.679 - 00:22:57.029` | which is |
| `00:22:57.039 - 00:22:59.669` | number zero in register a0 |
| `00:22:59.679 - 00:23:02.549` | so local variable n |
| `00:23:02.559 - 00:23:05.669` | is here the call to argent is just going |
| `00:23:05.679 - 00:23:09.110` | to fetch the value from register a0 |
| `00:23:09.120 - 00:23:11.350` | and place it in the |
| `00:23:11.360 - 00:23:12.870` | variable in |
| `00:23:12.880 - 00:23:13.990` | and |
| `00:23:14.000 - 00:23:16.230` | we are |
| `00:23:16.240 - 00:23:17.830` | then going to |
| `00:23:17.840 - 00:23:20.230` | capture the previous size of this |
| `00:23:20.240 - 00:23:22.870` | process so we are just getting the size |
| `00:23:22.880 - 00:23:25.430` | field and saving it here and that's what |
| `00:23:25.440 - 00:23:27.590` | we will be returning and then we talk |
| `00:23:27.600 - 00:23:31.190` | call grow proc to do the work |
| `00:23:31.200 - 00:23:33.350` | finally let's look at the grow proc |
| `00:23:33.360 - 00:23:35.990` | function which comes from the file proc |
| `00:23:36.000 - 00:23:37.990` | dot c |
| `00:23:38.000 - 00:23:38.789` | so |
| `00:23:38.799 - 00:23:41.830` | this will be passed a number of bytes |
| `00:23:41.840 - 00:23:43.590` | and it will grow or shrink the user |
| `00:23:43.600 - 00:23:46.230` | memory returning minus one if there's a |
| `00:23:46.240 - 00:23:48.950` | problem |
| `00:23:48.960 - 00:23:50.630` | we check the |
| `00:23:50.640 - 00:23:52.710` | number of bytes to see if it's positive |
| `00:23:52.720 - 00:23:55.669` | in which case we call uvm alec or if |
| `00:23:55.679 - 00:23:58.710` | it's negative we call uvm dialic or if |
| `00:23:58.720 - 00:24:01.350` | it's zero we do nothing |
| `00:24:01.360 - 00:24:05.350` | and uvm alec is past the old size and |
| `00:24:05.360 - 00:24:06.870` | the new size |
| `00:24:06.880 - 00:24:09.110` | so here we're capturing the old size |
| `00:24:09.120 - 00:24:12.390` | from the proc structure p |
| `00:24:12.400 - 00:24:14.950` | and adding a positive number to it |
| `00:24:14.960 - 00:24:17.350` | and if there is any problem for example |
| `00:24:17.360 - 00:24:19.750` | if the pages cannot be allocated we |
| `00:24:19.760 - 00:24:22.230` | return -1 |
| `00:24:22.240 - 00:24:25.110` | otherwise we will be returning 0. |
| `00:24:25.120 - 00:24:27.750` | if we are shrinking the address space |
| `00:24:27.760 - 00:24:30.310` | then n is a negative number and here the |
| `00:24:30.320 - 00:24:34.070` | old size is larger than the new size |
| `00:24:34.080 - 00:24:36.950` | and uvm dialect will always succeed so |
| `00:24:36.960 - 00:24:38.950` | we don't need to check |
| `00:24:38.960 - 00:24:41.909` | but in any case both of these return the |
| `00:24:41.919 - 00:24:45.350` | size of the address space afterwards so |
| `00:24:45.360 - 00:24:47.269` | we save that in the size field of the |
| `00:24:47.279 - 00:24:49.830` | proc structure |
| `00:24:49.840 - 00:24:51.990` | okay that's it for the anatomy of a |
| `00:24:52.000 - 00:24:54.390` | system call i will see you in the next |
| `00:24:54.400 - 00:24:57.400` | video |
