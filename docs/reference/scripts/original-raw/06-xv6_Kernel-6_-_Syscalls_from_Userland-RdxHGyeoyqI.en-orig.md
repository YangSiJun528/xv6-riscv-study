# xv6 Kernel-6: Syscalls from Userland

| Field | Value |
| --- | --- |
| Video ID | `RdxHGyeoyqI` |
| URL | <https://www.youtube.com/watch?v=RdxHGyeoyqI> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.280 - 00:00:03.030` | this video is part of a series on the |
| `00:00:03.040 - 00:00:06.150` | xv6 operating system kernel |
| `00:00:06.160 - 00:00:07.829` | in this video i'm going to talk about |
| `00:00:07.839 - 00:00:09.110` | system calls |
| `00:00:09.120 - 00:00:11.430` | and how they can be made from user mode |
| `00:00:11.440 - 00:00:12.709` | code |
| `00:00:12.719 - 00:00:15.350` | i'll start by looking at a small c |
| `00:00:15.360 - 00:00:17.750` | program that makes a couple of system |
| `00:00:17.760 - 00:00:18.710` | calls |
| `00:00:18.720 - 00:00:20.470` | and then i'll look at |
| `00:00:20.480 - 00:00:23.189` | the program called init code which is |
| `00:00:23.199 - 00:00:26.390` | the very first code that is executed in |
| `00:00:26.400 - 00:00:28.950` | user mode this program is written in |
| `00:00:28.960 - 00:00:30.470` | assembly code so |
| `00:00:30.480 - 00:00:32.470` | be prepared we'll see a little assembly |
| `00:00:32.480 - 00:00:34.470` | code in this video |
| `00:00:34.480 - 00:00:37.350` | but let's start with the c program |
| `00:00:37.360 - 00:00:39.350` | here is the code for |
| `00:00:39.360 - 00:00:40.630` | kill |
| `00:00:40.640 - 00:00:43.430` | it is a command that i'm sure you're |
| `00:00:43.440 - 00:00:45.510` | familiar with |
| `00:00:45.520 - 00:00:47.350` | we see that it makes |
| `00:00:47.360 - 00:00:49.029` | calls to some |
| `00:00:49.039 - 00:00:51.670` | library functions fprintf |
| `00:00:51.680 - 00:00:54.709` | and a2i are used here |
| `00:00:54.719 - 00:00:57.029` | it also makes some calls to |
| `00:00:57.039 - 00:01:00.470` | exit and kill which are system calls |
| `00:01:00.480 - 00:01:02.549` | it passes arguments uh |
| `00:01:02.559 - 00:01:04.710` | in this case it passes a one two this is |
| `00:01:04.720 - 00:01:06.710` | to call exit |
| `00:01:06.720 - 00:01:08.950` | system calls can return values but we |
| `00:01:08.960 - 00:01:10.830` | don't see that happening |
| `00:01:10.840 - 00:01:13.350` | here this program |
| `00:01:13.360 - 00:01:14.789` | includes |
| `00:01:14.799 - 00:01:16.710` | user.h |
| `00:01:16.720 - 00:01:18.390` | these two are not so interesting but |
| `00:01:18.400 - 00:01:21.190` | user.h is relevant here because it |
| `00:01:21.200 - 00:01:22.630` | contains the |
| `00:01:22.640 - 00:01:25.109` | function prototypes for both the library |
| `00:01:25.119 - 00:01:26.390` | functions |
| `00:01:26.400 - 00:01:29.350` | and the system calls so here's a code |
| `00:01:29.360 - 00:01:33.270` | for user.h |
| `00:01:33.280 - 00:01:35.830` | okay and it does a pretty short program |
| `00:01:35.840 - 00:01:39.109` | a short file it contains |
| `00:01:39.119 - 00:01:40.789` | function prototypes for the various |
| `00:01:40.799 - 00:01:43.510` | system calls |
| `00:01:43.520 - 00:01:45.510` | and it contains function prototypes for |
| `00:01:45.520 - 00:01:47.429` | the library functions |
| `00:01:47.439 - 00:01:50.950` | so you can see each one of the 21 system |
| `00:01:50.960 - 00:01:53.190` | calls that xv6 supports |
| `00:01:53.200 - 00:01:54.469` | is listed here |
| `00:01:54.479 - 00:01:57.190` | so for example we see exit |
| `00:01:57.200 - 00:01:59.030` | and we see kill |
| `00:01:59.040 - 00:02:02.389` | each of these takes a single parameter |
| `00:02:02.399 - 00:02:03.590` | and |
| `00:02:03.600 - 00:02:06.789` | we also see that the system calls return |
| `00:02:06.799 - 00:02:08.869` | values |
| `00:02:08.879 - 00:02:09.830` | this |
| `00:02:09.840 - 00:02:11.350` | attribute stuff here is a bit of |
| `00:02:11.360 - 00:02:13.910` | compiler magic that tells the compiler |
| `00:02:13.920 - 00:02:15.190` | that this particular function is |
| `00:02:15.200 - 00:02:17.350` | guaranteed never to return |
| `00:02:17.360 - 00:02:19.350` | and so this might allow the compiler to |
| `00:02:19.360 - 00:02:21.830` | do some optimizations |
| `00:02:21.840 - 00:02:23.670` | also see |
| `00:02:23.680 - 00:02:25.990` | function prototypes for the library |
| `00:02:26.000 - 00:02:27.270` | functions |
| `00:02:27.280 - 00:02:30.070` | the xv6 system doesn't include a whole |
| `00:02:30.080 - 00:02:33.509` | lot of library functions a normal uh |
| `00:02:33.519 - 00:02:36.390` | operating system would have hundreds of |
| `00:02:36.400 - 00:02:39.110` | functions but we do see |
| `00:02:39.120 - 00:02:41.670` | prototypes for |
| `00:02:41.680 - 00:02:42.949` | fprintf |
| `00:02:42.959 - 00:02:44.550` | printf |
| `00:02:44.560 - 00:02:46.630` | some string functions |
| `00:02:46.640 - 00:02:48.150` | like string length |
| `00:02:48.160 - 00:02:52.070` | and we nee we see uh a to i and some |
| `00:02:52.080 - 00:02:53.589` | other things |
| `00:02:53.599 - 00:02:54.869` | we're not going to look at the code for |
| `00:02:54.879 - 00:02:58.390` | these right now but these are in a file |
| `00:02:58.400 - 00:03:01.350` | called ulib.c |
| `00:03:01.360 - 00:03:03.589` | we're more interested in the |
| `00:03:03.599 - 00:03:06.869` | function code functions for these |
| `00:03:06.879 - 00:03:09.350` | system calls |
| `00:03:09.360 - 00:03:11.110` | there is a small |
| `00:03:11.120 - 00:03:12.070` | function |
| `00:03:12.080 - 00:03:13.910` | for each one of these and these are all |
| `00:03:13.920 - 00:03:17.589` | collected into one file called usys.s |
| `00:03:17.599 - 00:03:23.830` | which is an assembly language program |
| `00:03:23.840 - 00:03:25.270` | the |
| `00:03:25.280 - 00:03:26.949` | function |
| `00:03:26.959 - 00:03:29.750` | codes for each one of these functions |
| `00:03:29.760 - 00:03:31.750` | that is in the assembly language is |
| `00:03:31.760 - 00:03:33.670` | actually generated automatically by a |
| `00:03:33.680 - 00:03:36.470` | perl script so let's take a look at that |
| `00:03:36.480 - 00:03:37.350` | so |
| `00:03:37.360 - 00:03:39.110` | here we've got |
| `00:03:39.120 - 00:03:41.430` | uses.pl |
| `00:03:41.440 - 00:03:43.990` | and this script is going to generate |
| `00:03:44.000 - 00:03:45.589` | some assembly code and here i'm showing |
| `00:03:45.599 - 00:03:47.589` | what the assembly code is generated and |
| `00:03:47.599 - 00:03:50.630` | it goes into this file usys.s which will |
| `00:03:50.640 - 00:03:53.589` | be assembled and linked with |
| `00:03:53.599 - 00:03:55.190` | the program |
| `00:03:55.200 - 00:03:58.830` | like that we're compiling like kill dot |
| `00:03:58.840 - 00:04:01.830` | c so what do we see here |
| `00:04:01.840 - 00:04:02.869` | well |
| `00:04:02.879 - 00:04:05.429` | we uh start with a couple print |
| `00:04:05.439 - 00:04:08.949` | statements uh a comment here and then a |
| `00:04:08.959 - 00:04:11.910` | pound sign include and here i'm showing |
| `00:04:11.920 - 00:04:13.429` | what gets produced |
| `00:04:13.439 - 00:04:14.470` | our comment |
| `00:04:14.480 - 00:04:16.390` | and the pound sign include we're |
| `00:04:16.400 - 00:04:20.069` | including something called syscall.h |
| `00:04:20.079 - 00:04:21.670` | and then |
| `00:04:21.680 - 00:04:24.870` | for each of the 21 system calls |
| `00:04:24.880 - 00:04:27.430` | we're going to generate |
| `00:04:27.440 - 00:04:29.590` | some code here |
| `00:04:29.600 - 00:04:31.430` | and what do we do |
| `00:04:31.440 - 00:04:34.870` | well for each one of them we create a |
| `00:04:34.880 - 00:04:37.110` | dot global statement |
| `00:04:37.120 - 00:04:38.950` | here's the dot global |
| `00:04:38.960 - 00:04:41.270` | with the particular name in this example |
| `00:04:41.280 - 00:04:42.790` | it's it's open |
| `00:04:42.800 - 00:04:44.950` | so the name that we're dealing with is |
| `00:04:44.960 - 00:04:45.830` | open |
| `00:04:45.840 - 00:04:47.430` | then we have a label |
| `00:04:47.440 - 00:04:49.749` | open colon which is right |
| `00:04:49.759 - 00:04:51.030` | here |
| `00:04:51.040 - 00:04:52.390` | and then |
| `00:04:52.400 - 00:04:53.990` | we have three |
| `00:04:54.000 - 00:04:55.270` | instructions |
| `00:04:55.280 - 00:04:57.830` | li is load immediate e call is |
| `00:04:57.840 - 00:04:59.270` | environment call |
| `00:04:59.280 - 00:05:02.390` | and rep is returned so we see those li |
| `00:05:02.400 - 00:05:04.230` | ecal and rep |
| `00:05:04.240 - 00:05:05.350` | li |
| `00:05:05.360 - 00:05:08.629` | a7 and then again cis underscore and |
| `00:05:08.639 - 00:05:09.749` | then our name |
| `00:05:09.759 - 00:05:12.629` | sys underscore open |
| `00:05:12.639 - 00:05:14.710` | so for each one of these we'll have a |
| `00:05:14.720 - 00:05:17.430` | very small function |
| `00:05:17.440 - 00:05:19.510` | this function will |
| `00:05:19.520 - 00:05:21.430` | execute a load immediate instruction |
| `00:05:21.440 - 00:05:25.510` | which will move some constant into a7 |
| `00:05:25.520 - 00:05:26.710` | sysopen |
| `00:05:26.720 - 00:05:27.590` | is |
| `00:05:27.600 - 00:05:29.670` | just a number and |
| `00:05:29.680 - 00:05:32.029` | we see that we are including |
| `00:05:32.039 - 00:05:33.590` | syscall.h |
| `00:05:33.600 - 00:05:36.790` | well here is this call.h |
| `00:05:36.800 - 00:05:37.830` | all it |
| `00:05:37.840 - 00:05:39.749` | has is |
| `00:05:39.759 - 00:05:42.150` | a collection of defined statements and |
| `00:05:42.160 - 00:05:44.629` | for each of the 21 system calls it |
| `00:05:44.639 - 00:05:46.150` | associates a number |
| `00:05:46.160 - 00:05:48.950` | in our example we're looking at open so |
| `00:05:48.960 - 00:05:50.870` | we see that |
| `00:05:50.880 - 00:05:54.469` | open has a system has a number of 15. |
| `00:05:54.479 - 00:05:56.309` | so |
| `00:05:56.319 - 00:05:58.790` | all this does is move a 15 into register |
| `00:05:58.800 - 00:06:00.070` | a7 |
| `00:06:00.080 - 00:06:02.550` | and then then execute the equal |
| `00:06:02.560 - 00:06:03.670` | instruction |
| `00:06:03.680 - 00:06:06.469` | ecall will |
| `00:06:06.479 - 00:06:07.270` | end |
| `00:06:07.280 - 00:06:10.309` | execution in user mode switch to kernel |
| `00:06:10.319 - 00:06:12.629` | mode and we'll |
| `00:06:12.639 - 00:06:15.270` | uh exec the kernel will execute some |
| `00:06:15.280 - 00:06:17.749` | code that will do whatever this is |
| `00:06:17.759 - 00:06:20.469` | involved for opening a file and |
| `00:06:20.479 - 00:06:23.189` | ultimately it will return to user mode |
| `00:06:23.199 - 00:06:25.270` | and execute the next instruction which |
| `00:06:25.280 - 00:06:26.950` | is a return |
| `00:06:26.960 - 00:06:29.110` | now we are passing |
| `00:06:29.120 - 00:06:31.110` | arguments in the open |
| `00:06:31.120 - 00:06:34.870` | system call so if we look at open here |
| `00:06:34.880 - 00:06:37.350` | we see that we pass two arguments |
| `00:06:37.360 - 00:06:39.430` | pointing to the file name and uh the |
| `00:06:39.440 - 00:06:40.629` | mode |
| `00:06:40.639 - 00:06:42.950` | that we want to open that file in and we |
| `00:06:42.960 - 00:06:45.029` | return a value |
| `00:06:45.039 - 00:06:48.230` | arguments are passed in the a registers |
| `00:06:48.240 - 00:06:50.309` | the first argument in a0 the second |
| `00:06:50.319 - 00:06:52.870` | argument in a1 and if there are |
| `00:06:52.880 - 00:06:56.790` | additional arguments in a2 a3 and so on |
| `00:06:56.800 - 00:07:00.150` | a return value will appear in a0 |
| `00:07:00.160 - 00:07:03.270` | and the kernel |
| `00:07:03.280 - 00:07:05.830` | will do that it will expect arguments in |
| `00:07:05.840 - 00:07:08.150` | a0 1 2 and so on |
| `00:07:08.160 - 00:07:10.950` | and upon return from the kernel the |
| `00:07:10.960 - 00:07:12.950` | result will be an a0 |
| `00:07:12.960 - 00:07:15.990` | and that's why this little bit of code |
| `00:07:16.000 - 00:07:18.710` | here doesn't mess with a0 |
| `00:07:18.720 - 00:07:20.790` | a1 a2 and so on |
| `00:07:20.800 - 00:07:22.710` | they are assumed to already be in the |
| `00:07:22.720 - 00:07:26.070` | registers when this is invoked and the |
| `00:07:26.080 - 00:07:29.270` | result from the kernel in a0 will stay |
| `00:07:29.280 - 00:07:32.790` | in a0 and it will be returned to |
| `00:07:32.800 - 00:07:37.990` | whoever invoked the open system call |
| `00:07:38.000 - 00:07:40.710` | okay next let's take a look at |
| `00:07:40.720 - 00:07:42.870` | initcode.s |
| `00:07:42.880 - 00:07:45.670` | this is a user mode program in fact it's |
| `00:07:45.680 - 00:07:47.589` | somewhat special because it is the very |
| `00:07:47.599 - 00:07:48.710` | first |
| `00:07:48.720 - 00:07:50.790` | code that's executed in user mode by the |
| `00:07:50.800 - 00:07:52.629` | kernel |
| `00:07:52.639 - 00:07:55.189` | and here we see |
| `00:07:55.199 - 00:07:57.270` | the code for |
| `00:07:57.280 - 00:08:00.550` | this program it's pretty short uh it |
| `00:08:00.560 - 00:08:04.150` | includes systemcall.h so in fact we make |
| `00:08:04.160 - 00:08:06.629` | a couple system calls here we call the |
| `00:08:06.639 - 00:08:08.150` | exact system call |
| `00:08:08.160 - 00:08:11.430` | and the exit system call |
| `00:08:11.440 - 00:08:13.510` | now what does this little program do |
| `00:08:13.520 - 00:08:15.830` | i've tried to show it in |
| `00:08:15.840 - 00:08:17.350` | this form here |
| `00:08:17.360 - 00:08:18.710` | it simply |
| `00:08:18.720 - 00:08:19.749` | calls |
| `00:08:19.759 - 00:08:22.629` | the exact system call passing a pointer |
| `00:08:22.639 - 00:08:25.430` | to the file name and a pointer to an |
| `00:08:25.440 - 00:08:27.510` | array and the array contains two |
| `00:08:27.520 - 00:08:30.469` | elements |
| `00:08:30.479 - 00:08:31.749` | this |
| `00:08:31.759 - 00:08:33.589` | first argument is a pointer to a |
| `00:08:33.599 - 00:08:35.589` | sequence of bytes |
| `00:08:35.599 - 00:08:37.990` | backslash i n i t |
| `00:08:38.000 - 00:08:39.509` | nullbyte |
| `00:08:39.519 - 00:08:42.149` | and our v is a pointer to an array of |
| `00:08:42.159 - 00:08:45.030` | two elements the first is a pointer to |
| `00:08:45.040 - 00:08:46.470` | back flash in it |
| `00:08:46.480 - 00:08:48.550` | and the second thing is the null the |
| `00:08:48.560 - 00:08:52.630` | terminating null |
| `00:08:52.640 - 00:08:53.750` | these are each |
| `00:08:53.760 - 00:08:56.470` | one byte and this is actually uh eight |
| `00:08:56.480 - 00:08:58.470` | bytes a double word it's not shown very |
| `00:08:58.480 - 00:08:59.750` | clearly because they look like the same |
| `00:08:59.760 - 00:09:01.750` | size here |
| `00:09:01.760 - 00:09:03.430` | if exec returns |
| `00:09:03.440 - 00:09:05.829` | which normally it would not but if for |
| `00:09:05.839 - 00:09:07.910` | some reason this file doesn't exist then |
| `00:09:07.920 - 00:09:10.310` | it would return and what do we do then |
| `00:09:10.320 - 00:09:13.350` | well we invoke exit we're not even |
| `00:09:13.360 - 00:09:16.150` | and then if exit should return which it |
| `00:09:16.160 - 00:09:17.910` | definitely will not do |
| `00:09:17.920 - 00:09:21.030` | this program contains a go to statement |
| `00:09:21.040 - 00:09:23.430` | which we'll call it again |
| `00:09:23.440 - 00:09:25.829` | so we don't expect that to happen |
| `00:09:25.839 - 00:09:29.269` | so now let's look at the actual code |
| `00:09:29.279 - 00:09:32.070` | we're including syscall.h |
| `00:09:32.080 - 00:09:34.470` | that is our |
| `00:09:34.480 - 00:09:36.230` | file that includes numbers that |
| `00:09:36.240 - 00:09:40.070` | associates a value with each one of the |
| `00:09:40.080 - 00:09:41.269` | cis |
| `00:09:41.279 - 00:09:44.630` | constants so here we're using cis exec |
| `00:09:44.640 - 00:09:46.310` | that's number seven |
| `00:09:46.320 - 00:09:48.470` | we're also using cis exit |
| `00:09:48.480 - 00:09:52.150` | that's number two okay |
| `00:09:52.160 - 00:09:54.230` | and what do we have well |
| `00:09:54.240 - 00:09:57.750` | we are executing |
| `00:09:57.760 - 00:09:59.590` | three instructions and then making the |
| `00:09:59.600 - 00:10:00.710` | system call |
| `00:10:00.720 - 00:10:04.230` | la loads the address into a0 of variable |
| `00:10:04.240 - 00:10:07.829` | init which is down here |
| `00:10:07.839 - 00:10:10.790` | this second instruction loads a 1 with |
| `00:10:10.800 - 00:10:13.750` | rgv which is the address of |
| `00:10:13.760 - 00:10:14.630` | this |
| `00:10:14.640 - 00:10:15.829` | down here |
| `00:10:15.839 - 00:10:18.389` | here we have exactly what's going on |
| `00:10:18.399 - 00:10:19.590` | right here |
| `00:10:19.600 - 00:10:20.790` | init |
| `00:10:20.800 - 00:10:23.590` | and these bytes are in our v and these |
| `00:10:23.600 - 00:10:24.870` | two words |
| `00:10:24.880 - 00:10:27.590` | so here we have init |
| `00:10:27.600 - 00:10:29.430` | backslash init |
| `00:10:29.440 - 00:10:30.630` | null byte |
| `00:10:30.640 - 00:10:32.550` | and here we have our v |
| `00:10:32.560 - 00:10:35.430` | which is a contains the address uh of |
| `00:10:35.440 - 00:10:38.310` | init and it contains a zero |
| `00:10:38.320 - 00:10:41.590` | with an alignment statement here |
| `00:10:41.600 - 00:10:43.110` | so we load in |
| `00:10:43.120 - 00:10:45.990` | the two arguments and we set a seven to |
| `00:10:46.000 - 00:10:47.990` | the code number for |
| `00:10:48.000 - 00:10:51.190` | the exact system call and we make the |
| `00:10:51.200 - 00:10:52.230` | e call |
| `00:10:52.240 - 00:10:55.350` | e stands for environment call and then |
| `00:10:55.360 - 00:10:57.509` | if that should return |
| `00:10:57.519 - 00:10:59.030` | we then have |
| `00:10:59.040 - 00:11:03.829` | the code to call the exit system call |
| `00:11:03.839 - 00:11:06.069` | we load into |
| `00:11:06.079 - 00:11:07.590` | a7 |
| `00:11:07.600 - 00:11:11.269` | the code number which was two for the |
| `00:11:11.279 - 00:11:14.069` | exit system call and then we perform the |
| `00:11:14.079 - 00:11:16.389` | system call and if that should return we |
| `00:11:16.399 - 00:11:20.150` | just jump here and keep repeating it |
| `00:11:20.160 - 00:11:22.630` | exit happens to take a parameter it |
| `00:11:22.640 - 00:11:24.710` | takes a single argument which is the |
| `00:11:24.720 - 00:11:27.350` | exit code or return code |
| `00:11:27.360 - 00:11:29.910` | we don't even bother to load a0 with |
| `00:11:29.920 - 00:11:32.550` | anything so who knows what it would |
| `00:11:32.560 - 00:11:33.910` | actually be |
| `00:11:33.920 - 00:11:37.350` | but hopefully exit sorry exec will never |
| `00:11:37.360 - 00:11:40.230` | return |
| `00:11:40.240 - 00:11:41.670` | okay that's it i'll see you in the next |
| `00:11:41.680 - 00:11:43.839` | video |
