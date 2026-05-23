# xv6 Kernel-23: Fork System Call

| Field | Value |
| --- | --- |
| Video ID | `MnTQi1IZTUM` |
| URL | <https://www.youtube.com/watch?v=MnTQi1IZTUM> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.040 - 00:00:02.629` | this video is part of a series on the |
| `00:00:02.639 - 00:00:04.950` | xv6 operating system kernel |
| `00:00:04.960 - 00:00:07.269` | in this video i'll describe how the fork |
| `00:00:07.279 - 00:00:10.150` | system call works |
| `00:00:10.160 - 00:00:12.230` | whenever a process wants to create |
| `00:00:12.240 - 00:00:15.270` | another process it uses the fork system |
| `00:00:15.280 - 00:00:17.430` | call and this is the only way that |
| `00:00:17.440 - 00:00:19.429` | processes are created |
| `00:00:19.439 - 00:00:21.429` | with the exception of the creation of |
| `00:00:21.439 - 00:00:23.670` | the initial process |
| `00:00:23.680 - 00:00:25.910` | the process that does the creating is |
| `00:00:25.920 - 00:00:28.630` | said to be the parent process and it |
| `00:00:28.640 - 00:00:31.750` | creates a child process |
| `00:00:31.760 - 00:00:34.150` | the child process is a clone of the |
| `00:00:34.160 - 00:00:36.870` | parent process and almost exactly |
| `00:00:36.880 - 00:00:40.709` | identical to the parent process |
| `00:00:40.719 - 00:00:42.630` | when the parent makes the fork system |
| `00:00:42.640 - 00:00:45.590` | call the kernel becomes active and then |
| `00:00:45.600 - 00:00:46.869` | at |
| `00:00:46.879 - 00:00:49.029` | some point the fork system call returns |
| `00:00:49.039 - 00:00:50.630` | and when it returns |
| `00:00:50.640 - 00:00:53.590` | both processes now exist |
| `00:00:53.600 - 00:00:56.950` | and so in both processes there will be a |
| `00:00:56.960 - 00:00:59.270` | return from the system call |
| `00:00:59.280 - 00:01:02.310` | one major difference is that the fork |
| `00:01:02.320 - 00:01:05.189` | system call returns the child's process |
| `00:01:05.199 - 00:01:06.230` | id |
| `00:01:06.240 - 00:01:10.070` | while the fork system call returns zero |
| `00:01:10.080 - 00:01:12.469` | in the child process |
| `00:01:12.479 - 00:01:15.030` | the code in the |
| `00:01:15.040 - 00:01:17.510` | both processes the parent and the child |
| `00:01:17.520 - 00:01:20.230` | will typically take a look at the return |
| `00:01:20.240 - 00:01:22.310` | value and that code can then determine |
| `00:01:22.320 - 00:01:24.070` | whether it is running in the child or |
| `00:01:24.080 - 00:01:27.190` | the parent process and can then take |
| `00:01:27.200 - 00:01:30.149` | actions differently depending on whether |
| `00:01:30.159 - 00:01:30.789` | it |
| `00:01:30.799 - 00:01:34.630` | needs to to behave as a child or parent |
| `00:01:34.640 - 00:01:37.030` | if there is any difficulty |
| `00:01:37.040 - 00:01:38.789` | such as we run out of memory and we |
| `00:01:38.799 - 00:01:40.870` | can't allocate the pages for the child |
| `00:01:40.880 - 00:01:43.510` | the fork system call will return |
| `00:01:43.520 - 00:01:45.830` | -1 in the parent |
| `00:01:45.840 - 00:01:48.469` | so what's involved in creating the child |
| `00:01:48.479 - 00:01:49.670` | process |
| `00:01:49.680 - 00:01:51.830` | well we do several things and then we |
| `00:01:51.840 - 00:01:55.910` | return the process id of the child |
| `00:01:55.920 - 00:01:59.190` | the first thing we do is call the alec |
| `00:01:59.200 - 00:02:00.950` | proc function |
| `00:02:00.960 - 00:02:03.510` | this will search for an unused proc |
| `00:02:03.520 - 00:02:06.709` | structure and grab it and begin the |
| `00:02:06.719 - 00:02:08.790` | initialization process |
| `00:02:08.800 - 00:02:12.470` | it starts by assigning a new process id |
| `00:02:12.480 - 00:02:14.869` | to that proc structure |
| `00:02:14.879 - 00:02:17.670` | it also creates an empty address space |
| `00:02:17.680 - 00:02:19.110` | and |
| `00:02:19.120 - 00:02:20.869` | adds mappings for the trampoline and |
| `00:02:20.879 - 00:02:23.110` | trap frame pages |
| `00:02:23.120 - 00:02:25.030` | then after alec proc |
| `00:02:25.040 - 00:02:26.150` | returns |
| `00:02:26.160 - 00:02:28.309` | we need to copy the virtual address |
| `00:02:28.319 - 00:02:29.270` | space |
| `00:02:29.280 - 00:02:32.790` | so every data page from the parent |
| `00:02:32.800 - 00:02:36.550` | process is copied and added to the |
| `00:02:36.560 - 00:02:39.509` | child's virtual address space |
| `00:02:39.519 - 00:02:41.990` | the permissions on each data page in the |
| `00:02:42.000 - 00:02:44.790` | child will be set to be exactly the same |
| `00:02:44.800 - 00:02:46.869` | as the permissions on the corresponding |
| `00:02:46.879 - 00:02:47.910` | page |
| `00:02:47.920 - 00:02:50.869` | in the parents virtual address space |
| `00:02:50.879 - 00:02:53.750` | and then we initialize and set a few of |
| `00:02:53.760 - 00:02:56.390` | the fields in the proc structure |
| `00:02:56.400 - 00:02:59.670` | the size field is the number of bytes in |
| `00:02:59.680 - 00:03:02.390` | the virtual address space that is |
| `00:03:02.400 - 00:03:04.070` | since the virtual address space starts |
| `00:03:04.080 - 00:03:06.149` | at zero that's just the same as the |
| `00:03:06.159 - 00:03:08.390` | break address at the top of the heap so |
| `00:03:08.400 - 00:03:10.229` | that's copied |
| `00:03:10.239 - 00:03:13.589` | next we copy the trap frame |
| `00:03:13.599 - 00:03:15.430` | when the system call was made in the |
| `00:03:15.440 - 00:03:17.750` | parent process all the |
| `00:03:17.760 - 00:03:20.470` | registers of the user process were saved |
| `00:03:20.480 - 00:03:22.949` | as well as the program counter |
| `00:03:22.959 - 00:03:25.270` | and those were saved in the trap frame |
| `00:03:25.280 - 00:03:27.430` | and by copying the trap frame it means |
| `00:03:27.440 - 00:03:29.030` | that the child process when it's |
| `00:03:29.040 - 00:03:30.550` | scheduled again and |
| `00:03:30.560 - 00:03:33.350` | runs in user mode will have exactly the |
| `00:03:33.360 - 00:03:36.470` | same values in all of its registers and |
| `00:03:36.480 - 00:03:39.030` | it will begin executing at the same spot |
| `00:03:39.040 - 00:03:41.830` | namely directly after the e-call |
| `00:03:41.840 - 00:03:44.309` | instruction that did the fork system |
| `00:03:44.319 - 00:03:45.190` | call |
| `00:03:45.200 - 00:03:48.229` | and then we modify register a0 to set it |
| `00:03:48.239 - 00:03:49.910` | to 0 so that |
| `00:03:49.920 - 00:03:50.789` | the |
| `00:03:50.799 - 00:03:51.750` | child |
| `00:03:51.760 - 00:03:55.990` | process will see a return value of 0. |
| `00:03:56.000 - 00:03:59.350` | each process has a name and we copy |
| `00:03:59.360 - 00:04:01.670` | the name field so the child process will |
| `00:04:01.680 - 00:04:03.750` | be uh given the same name as the parent |
| `00:04:03.760 - 00:04:06.470` | process |
| `00:04:06.480 - 00:04:09.910` | each process has a number of open files |
| `00:04:09.920 - 00:04:12.630` | we copy all of the file descriptors for |
| `00:04:12.640 - 00:04:15.830` | open files in the parent process into |
| `00:04:15.840 - 00:04:18.469` | the child process so that any file that |
| `00:04:18.479 - 00:04:20.550` | was open in the parent process will then |
| `00:04:20.560 - 00:04:23.830` | be open in the child process |
| `00:04:23.840 - 00:04:25.510` | we also copy the current working |
| `00:04:25.520 - 00:04:27.909` | directory so the child will be in the |
| `00:04:27.919 - 00:04:30.150` | same working directory |
| `00:04:30.160 - 00:04:31.030` | and |
| `00:04:31.040 - 00:04:34.950` | we then set the parent pointer in the |
| `00:04:34.960 - 00:04:37.110` | ch in the child's proc structure to |
| `00:04:37.120 - 00:04:39.909` | point to the parents proc structure |
| `00:04:39.919 - 00:04:42.150` | this is the other difference between the |
| `00:04:42.160 - 00:04:45.510` | child process and the parent process |
| `00:04:45.520 - 00:04:47.670` | they each have a different location in |
| `00:04:47.680 - 00:04:52.629` | the parent-child hierarchy of processes |
| `00:04:52.639 - 00:04:55.670` | and finally we set the state to runnable |
| `00:04:55.680 - 00:04:57.670` | and at that point we're done creating |
| `00:04:57.680 - 00:05:00.310` | the child process it's runnable and it |
| `00:05:00.320 - 00:05:02.310` | will be scheduled when it's given a |
| `00:05:02.320 - 00:05:04.710` | chance so at this point |
| `00:05:04.720 - 00:05:07.350` | the system call is able to just simply |
| `00:05:07.360 - 00:05:09.350` | return the process id of the child that |
| `00:05:09.360 - 00:05:11.110` | has been created |
| `00:05:11.120 - 00:05:14.469` | next let's look at that in code |
| `00:05:14.479 - 00:05:16.070` | so |
| `00:05:16.080 - 00:05:18.150` | here is the fork process |
| `00:05:18.160 - 00:05:19.350` | in the |
| `00:05:19.360 - 00:05:22.390` | file proc dot c |
| `00:05:22.400 - 00:05:25.029` | the system call initially invoke invokes |
| `00:05:25.039 - 00:05:29.590` | cis fork cis fork simply calls fork and |
| `00:05:29.600 - 00:05:32.550` | whatever fork returns cis fork will |
| `00:05:32.560 - 00:05:34.230` | return |
| `00:05:34.240 - 00:05:36.390` | so we begin by getting a pointer to the |
| `00:05:36.400 - 00:05:37.590` | parents |
| `00:05:37.600 - 00:05:40.230` | proc structure and then we call the alec |
| `00:05:40.240 - 00:05:42.230` | proc function |
| `00:05:42.240 - 00:05:44.950` | if there are any problems then we return |
| `00:05:44.960 - 00:05:47.510` | -1 and we're done but otherwise we save |
| `00:05:47.520 - 00:05:48.870` | a pointer |
| `00:05:48.880 - 00:05:50.150` | np |
| `00:05:50.160 - 00:05:53.510` | is a new process i guess so we have a |
| `00:05:53.520 - 00:05:55.830` | pointer to that process |
| `00:05:55.840 - 00:05:57.749` | alec proc will also |
| `00:05:57.759 - 00:06:00.309` | acquire the lock on the newly created |
| `00:06:00.319 - 00:06:02.870` | proc structure |
| `00:06:02.880 - 00:06:04.629` | and it will initialize the virtual |
| `00:06:04.639 - 00:06:08.710` | address space and in this step we invoke |
| `00:06:08.720 - 00:06:11.749` | uvm copy to copy all of the data pages |
| `00:06:11.759 - 00:06:13.189` | from the parent |
| `00:06:13.199 - 00:06:16.230` | into the child's virtual address space |
| `00:06:16.240 - 00:06:18.790` | so these are pointers to the page tables |
| `00:06:18.800 - 00:06:21.749` | and that's the size of the address space |
| `00:06:21.759 - 00:06:23.510` | which indicates the number of pages that |
| `00:06:23.520 - 00:06:25.430` | need to be copied |
| `00:06:25.440 - 00:06:28.309` | if there are any problems then |
| `00:06:28.319 - 00:06:29.270` | we |
| `00:06:29.280 - 00:06:30.710` | free this |
| `00:06:30.720 - 00:06:33.189` | proc structure this zeros out all the |
| `00:06:33.199 - 00:06:35.029` | fields and returns everything to the |
| `00:06:35.039 - 00:06:37.590` | free memory pool and then we release the |
| `00:06:37.600 - 00:06:39.590` | lock on that structure and return minus |
| `00:06:39.600 - 00:06:40.390` | one |
| `00:06:40.400 - 00:06:41.990` | but assuming everything's good we keep |
| `00:06:42.000 - 00:06:44.469` | going uh here we make a copy of the size |
| `00:06:44.479 - 00:06:45.830` | field |
| `00:06:45.840 - 00:06:48.469` | and here we copy the trap frame so this |
| `00:06:48.479 - 00:06:50.550` | is where we are copying all of the |
| `00:06:50.560 - 00:06:52.790` | registers that were valid in user mode |
| `00:06:52.800 - 00:06:54.790` | as well as the program counter from the |
| `00:06:54.800 - 00:06:57.110` | parent to the child so the child can |
| `00:06:57.120 - 00:06:59.110` | resume executing it exactly the same |
| `00:06:59.120 - 00:07:00.469` | place |
| `00:07:00.479 - 00:07:04.870` | and here we modify a0 to clear it to |
| `00:07:04.880 - 00:07:07.350` | zero so that the return value |
| `00:07:07.360 - 00:07:10.790` | will be zero |
| `00:07:10.800 - 00:07:11.909` | in this uh |
| `00:07:11.919 - 00:07:14.150` | code here we are running through the |
| `00:07:14.160 - 00:07:17.510` | open files of the parent and for each |
| `00:07:17.520 - 00:07:20.710` | file that is open we make a duplicate of |
| `00:07:20.720 - 00:07:25.189` | that and add it to the child here |
| `00:07:25.199 - 00:07:27.110` | and here we're making a duplicate of the |
| `00:07:27.120 - 00:07:28.870` | current working directory |
| `00:07:28.880 - 00:07:30.710` | and copying that so the child will have |
| `00:07:30.720 - 00:07:33.830` | the same current working directory |
| `00:07:33.840 - 00:07:35.589` | here we are copying the parent's name |
| `00:07:35.599 - 00:07:38.230` | into the name field of the child |
| `00:07:38.240 - 00:07:41.110` | and here we grab the process idea of the |
| `00:07:41.120 - 00:07:43.510` | child and we're going to be returning it |
| `00:07:43.520 - 00:07:46.790` | right down here |
| `00:07:46.800 - 00:07:49.830` | then we see that we are updating the |
| `00:07:49.840 - 00:07:51.670` | parent pointer |
| `00:07:51.680 - 00:07:53.909` | of the child to point to the parents |
| `00:07:53.919 - 00:07:55.510` | proc structure |
| `00:07:55.520 - 00:07:58.309` | whenever the parent pointer is accessed |
| `00:07:58.319 - 00:08:00.550` | either read or modified we must be |
| `00:08:00.560 - 00:08:03.589` | holding a lock called weight lock so we |
| `00:08:03.599 - 00:08:05.270` | acquire that lock here and then we |
| `00:08:05.280 - 00:08:07.110` | release it here |
| `00:08:07.120 - 00:08:08.230` | and |
| `00:08:08.240 - 00:08:09.189` | finally |
| `00:08:09.199 - 00:08:12.869` | we set the state of this |
| `00:08:12.879 - 00:08:15.830` | process to runnable and release the lock |
| `00:08:15.840 - 00:08:19.430` | that was acquired up in alec proc |
| `00:08:19.440 - 00:08:22.469` | and then return at that point |
| `00:08:22.479 - 00:08:23.510` | the |
| `00:08:23.520 - 00:08:25.189` | child process has been created and will |
| `00:08:25.199 - 00:08:27.430` | get scheduled whenever uh the scheduler |
| `00:08:27.440 - 00:08:29.589` | gets around to it |
| `00:08:29.599 - 00:08:31.350` | we also see something else going on here |
| `00:08:31.360 - 00:08:33.190` | we see uh |
| `00:08:33.200 - 00:08:35.350` | during this code right here we first |
| `00:08:35.360 - 00:08:37.670` | release the lock on the child and then |
| `00:08:37.680 - 00:08:39.269` | reacquire it |
| `00:08:39.279 - 00:08:40.469` | and |
| `00:08:40.479 - 00:08:42.550` | we also notice that |
| `00:08:42.560 - 00:08:44.550` | we don't just return |
| `00:08:44.560 - 00:08:47.110` | the pid field here we copy it to a |
| `00:08:47.120 - 00:08:49.670` | variable and then return that |
| `00:08:49.680 - 00:08:51.350` | this is because we need to acquire this |
| `00:08:51.360 - 00:08:54.790` | field while we are holding a lock on the |
| `00:08:54.800 - 00:08:56.150` | process |
| `00:08:56.160 - 00:08:58.070` | it's possible that |
| `00:08:58.080 - 00:09:00.470` | you know the minute we release that lock |
| `00:09:00.480 - 00:09:01.430` | this |
| `00:09:01.440 - 00:09:03.430` | proc structure will get scheduled it |
| `00:09:03.440 - 00:09:06.829` | will run it will exit and uh |
| `00:09:06.839 - 00:09:09.509` | another uh fort call will grab this |
| `00:09:09.519 - 00:09:12.630` | process and a new pid will be assigned |
| `00:09:12.640 - 00:09:14.550` | highly unlikely that all the secret |
| `00:09:14.560 - 00:09:16.710` | sequence of events could occur but in an |
| `00:09:16.720 - 00:09:18.710` | operating system we have to assume that |
| `00:09:18.720 - 00:09:21.190` | anything that can occur will occur |
| `00:09:21.200 - 00:09:24.389` | and so if we just simply grab the pid |
| `00:09:24.399 - 00:09:26.550` | field from the proc structure after |
| `00:09:26.560 - 00:09:29.750` | releasing the lock we could in fact get |
| `00:09:29.760 - 00:09:32.870` | the wrong number so that's why we grab |
| `00:09:32.880 - 00:09:35.590` | the process id while we're still holding |
| `00:09:35.600 - 00:09:36.389` | the lock |
| `00:09:36.399 - 00:09:38.150` | but still we haven't re uh figured out |
| `00:09:38.160 - 00:09:40.470` | why we are releasing this lock and then |
| `00:09:40.480 - 00:09:43.350` | reacquiring it so for that i want to |
| `00:09:43.360 - 00:09:46.389` | go back and remind you about deadlock |
| `00:09:46.399 - 00:09:47.430` | so |
| `00:09:47.440 - 00:09:49.269` | here's a simple deadlock situation we |
| `00:09:49.279 - 00:09:53.030` | have two processes and we have two locks |
| `00:09:53.040 - 00:09:55.750` | and i'm going to show that lock a is |
| `00:09:55.760 - 00:09:58.070` | currently held by process one with a |
| `00:09:58.080 - 00:09:59.509` | solid arrow |
| `00:09:59.519 - 00:10:01.190` | and i'm going to show that lock b is |
| `00:10:01.200 - 00:10:03.110` | currently held by process two with a |
| `00:10:03.120 - 00:10:04.550` | solid arrow |
| `00:10:04.560 - 00:10:06.630` | now let's assume that |
| `00:10:06.640 - 00:10:07.990` | process 1 |
| `00:10:08.000 - 00:10:10.069` | wants to acquire |
| `00:10:10.079 - 00:10:11.110` | lock b |
| `00:10:11.120 - 00:10:13.750` | that is it has called the acquire |
| `00:10:13.760 - 00:10:15.910` | function and it's trying to obtain a |
| `00:10:15.920 - 00:10:17.670` | lock on |
| `00:10:17.680 - 00:10:18.389` | the |
| `00:10:18.399 - 00:10:20.389` | lock b which is |
| `00:10:20.399 - 00:10:22.870` | impossible because it's held by process |
| `00:10:22.880 - 00:10:25.190` | 2. |
| `00:10:25.200 - 00:10:27.750` | so the acquire function will be spinning |
| `00:10:27.760 - 00:10:31.590` | here meanwhile process 2 is wanting lock |
| `00:10:31.600 - 00:10:34.949` | a so it calls acquire and it is spinning |
| `00:10:34.959 - 00:10:37.509` | tied up in the acquire function waiting |
| `00:10:37.519 - 00:10:39.269` | on lock a |
| `00:10:39.279 - 00:10:41.430` | this is a deadlock deadlock in general |
| `00:10:41.440 - 00:10:43.829` | occurs whenever we have a cycle |
| `00:10:43.839 - 00:10:44.710` | in |
| `00:10:44.720 - 00:10:46.550` | one of these graphs that involves |
| `00:10:46.560 - 00:10:48.949` | processes and locks here i'm showing a |
| `00:10:48.959 - 00:10:51.110` | very simple cycle but we can have more |
| `00:10:51.120 - 00:10:52.870` | complex cycles but this gives the idea |
| `00:10:52.880 - 00:10:54.389` | of what deadlock is |
| `00:10:54.399 - 00:10:56.630` | there's a cyclic weight situation |
| `00:10:56.640 - 00:10:58.630` | process one can't proceed because it |
| `00:10:58.640 - 00:11:01.750` | can't get lock b process 2 can't proceed |
| `00:11:01.760 - 00:11:03.509` | because it can't get lock a and so |
| `00:11:03.519 - 00:11:04.949` | they're both frozen |
| `00:11:04.959 - 00:11:08.790` | now in our situation |
| `00:11:08.800 - 00:11:09.590` | we |
| `00:11:09.600 - 00:11:11.509` | i haven't yet talked about the code and |
| `00:11:11.519 - 00:11:13.829` | exit and wake up but we do have this |
| `00:11:13.839 - 00:11:15.269` | situation |
| `00:11:15.279 - 00:11:17.750` | the weight lock is concerned whenever um |
| `00:11:17.760 - 00:11:19.910` | we are basically looking at the parent |
| `00:11:19.920 - 00:11:22.630` | child hierarchy and when a |
| `00:11:22.640 - 00:11:25.590` | process exits we need to notify uh its |
| `00:11:25.600 - 00:11:27.670` | parent so the weight lock will be |
| `00:11:27.680 - 00:11:30.389` | acquired in the exit um |
| `00:11:30.399 - 00:11:31.910` | function and we also have a function |
| `00:11:31.920 - 00:11:34.790` | wake up and wake up goes through all of |
| `00:11:34.800 - 00:11:37.350` | the potential parent processes |
| `00:11:37.360 - 00:11:39.430` | it goes through all processes in fact |
| `00:11:39.440 - 00:11:41.350` | and grabs all of the locks on those |
| `00:11:41.360 - 00:11:42.630` | processes |
| `00:11:42.640 - 00:11:43.350` | so |
| `00:11:43.360 - 00:11:46.150` | um we have this situation where |
| `00:11:46.160 - 00:11:48.550` | at any at some point in |
| `00:11:48.560 - 00:11:51.269` | this these two functions we might have a |
| `00:11:51.279 - 00:11:53.430` | situation where the weight lock is held |
| `00:11:53.440 - 00:11:55.910` | and we are trying to acquire the lock on |
| `00:11:55.920 - 00:11:57.750` | a particular process |
| `00:11:57.760 - 00:12:00.790` | now in fork we are |
| `00:12:00.800 - 00:12:03.190` | needing to grab the |
| `00:12:03.200 - 00:12:06.629` | weight lock to modify the parent |
| `00:12:06.639 - 00:12:09.350` | the parent field so we see that |
| `00:12:09.360 - 00:12:11.670` | uh right here we are calling acquire to |
| `00:12:11.680 - 00:12:14.470` | grab the weight lock okay now what if we |
| `00:12:14.480 - 00:12:15.990` | were holding |
| `00:12:16.000 - 00:12:17.990` | a lock on some |
| `00:12:18.000 - 00:12:19.829` | process okay |
| `00:12:19.839 - 00:12:23.269` | then we would have the arrow going here |
| `00:12:23.279 - 00:12:26.629` | and that would be our deadlock situation |
| `00:12:26.639 - 00:12:29.110` | and we can't allow that so this is |
| `00:12:29.120 - 00:12:31.829` | uh an explanation for why we must |
| `00:12:31.839 - 00:12:35.030` | release the lock on the process before |
| `00:12:35.040 - 00:12:37.190` | we attempt to acquire |
| `00:12:37.200 - 00:12:41.269` | the lock called weight lock |
| `00:12:41.279 - 00:12:42.710` | next let's take a look at what happens |
| `00:12:42.720 - 00:12:44.790` | in the child process |
| `00:12:44.800 - 00:12:46.550` | at some point the scheduler will select |
| `00:12:46.560 - 00:12:48.069` | the child process |
| `00:12:48.079 - 00:12:51.110` | and will invoke switch |
| `00:12:51.120 - 00:12:53.910` | switch will load the registers from |
| `00:12:53.920 - 00:12:55.110` | context |
| `00:12:55.120 - 00:12:57.590` | these are the registers that were |
| `00:12:57.600 - 00:13:00.310` | saved previously in most cases but in |
| `00:13:00.320 - 00:13:01.509` | this case they have been just |
| `00:13:01.519 - 00:13:03.030` | initialized |
| `00:13:03.040 - 00:13:05.990` | and then switch will do a return well |
| `00:13:06.000 - 00:13:08.629` | register r a was loaded from context and |
| `00:13:08.639 - 00:13:11.190` | this return will effectively just jump |
| `00:13:11.200 - 00:13:12.069` | to |
| `00:13:12.079 - 00:13:15.990` | the instruction that's pointed to by ra |
| `00:13:16.000 - 00:13:17.910` | when the fork system call created the |
| `00:13:17.920 - 00:13:20.550` | child it called alec proc |
| `00:13:20.560 - 00:13:22.949` | and among other things that function |
| `00:13:22.959 - 00:13:26.629` | initialized the context for this process |
| `00:13:26.639 - 00:13:29.030` | it zeroed out all the registers |
| `00:13:29.040 - 00:13:31.670` | and it saved in r a |
| `00:13:31.680 - 00:13:33.590` | the address of this function called |
| `00:13:33.600 - 00:13:34.790` | forkrat |
| `00:13:34.800 - 00:13:38.069` | and it's saved in the sp register the |
| `00:13:38.079 - 00:13:39.509` | stack pointer |
| `00:13:39.519 - 00:13:41.990` | that is in fact |
| `00:13:42.000 - 00:13:44.150` | the page that's pointed to |
| `00:13:44.160 - 00:13:47.430` | by the k-stack field in the proc |
| `00:13:47.440 - 00:13:49.829` | structure and since stacks grow downward |
| `00:13:49.839 - 00:13:52.310` | we save the pointer to the top of that |
| `00:13:52.320 - 00:13:54.949` | page so we have a fresh stack and a |
| `00:13:54.959 - 00:13:57.430` | starting address in this function called |
| `00:13:57.440 - 00:13:59.030` | for crept which we'll look at in just a |
| `00:13:59.040 - 00:14:02.069` | second but before that let's |
| `00:14:02.079 - 00:14:04.870` | review what happens in a trap so |
| `00:14:04.880 - 00:14:06.389` | remember this picture we're showing a |
| `00:14:06.399 - 00:14:08.629` | trap and we save the registers in |
| `00:14:08.639 - 00:14:11.269` | uservec usertrap figures out what's |
| `00:14:11.279 - 00:14:13.030` | going on let's say it's a timer |
| `00:14:13.040 - 00:14:14.550` | interrupt and we call yield because i |
| `00:14:14.560 - 00:14:17.030` | want to look at yield a little bit more |
| `00:14:17.040 - 00:14:19.750` | and yield does some stuff |
| `00:14:19.760 - 00:14:22.069` | as we saw it called switch eventually |
| `00:14:22.079 - 00:14:25.030` | but ultimately yield returns and we come |
| `00:14:25.040 - 00:14:25.829` | back |
| `00:14:25.839 - 00:14:28.870` | and then user trap rep is invoked |
| `00:14:28.880 - 00:14:31.910` | user ret is invoked and these restore |
| `00:14:31.920 - 00:14:33.829` | the registers and set things back up and |
| `00:14:33.839 - 00:14:36.710` | then return to the user mode with the |
| `00:14:36.720 - 00:14:39.110` | esret instruction so let's look at this |
| `00:14:39.120 - 00:14:42.389` | pathway in a different way |
| `00:14:42.399 - 00:14:44.470` | let's assume we've got a timer interrupt |
| `00:14:44.480 - 00:14:46.470` | so we've got a trap that goes into |
| `00:14:46.480 - 00:14:49.110` | uservac and usertrap saves the user mode |
| `00:14:49.120 - 00:14:50.310` | registers |
| `00:14:50.320 - 00:14:53.750` | and then calls the yield function |
| `00:14:53.760 - 00:14:56.710` | well what does yield do |
| `00:14:56.720 - 00:14:59.430` | yield basically just calls |
| `00:14:59.440 - 00:15:01.509` | skid after acquiring a lock on the |
| `00:15:01.519 - 00:15:04.470` | process here here's the code for yield |
| `00:15:04.480 - 00:15:07.110` | to review it so you see yield it |
| `00:15:07.120 - 00:15:09.110` | acquires the lock well in the case of |
| `00:15:09.120 - 00:15:11.189` | yield |
| `00:15:11.199 - 00:15:14.389` | it changes the state to runnable uh in |
| `00:15:14.399 - 00:15:16.150` | the case of a child process it's already |
| `00:15:16.160 - 00:15:18.629` | runnable so uh keep that in mind but |
| `00:15:18.639 - 00:15:21.030` | then it calls sked and then after sched |
| `00:15:21.040 - 00:15:23.030` | comes back it releases the lock and then |
| `00:15:23.040 - 00:15:24.150` | returns |
| `00:15:24.160 - 00:15:24.949` | so |
| `00:15:24.959 - 00:15:28.310` | uh we call scan here and what does skid |
| `00:15:28.320 - 00:15:31.590` | do well skid does some error checking uh |
| `00:15:31.600 - 00:15:34.629` | here's the sched function it does some |
| `00:15:34.639 - 00:15:36.870` | error checking primarily and |
| `00:15:36.880 - 00:15:39.030` | essentially just calls switch |
| `00:15:39.040 - 00:15:40.790` | and then returns |
| `00:15:40.800 - 00:15:41.910` | and so |
| `00:15:41.920 - 00:15:43.269` | uh |
| `00:15:43.279 - 00:15:46.230` | when switch returns uh we are back in |
| `00:15:46.240 - 00:15:48.310` | sched sched returns |
| `00:15:48.320 - 00:15:49.829` | and then uh |
| `00:15:49.839 - 00:15:52.550` | yield uh becomes active uh does its last |
| `00:15:52.560 - 00:15:55.590` | statement which is to unlock the process |
| `00:15:55.600 - 00:15:57.509` | and then it returns to user trap rep |
| `00:15:57.519 - 00:15:58.230` | which |
| `00:15:58.240 - 00:16:01.509` | immediately calls user trap ret |
| `00:16:01.519 - 00:16:03.990` | and then that calls user rep and then |
| `00:16:04.000 - 00:16:05.430` | that calls |
| `00:16:05.440 - 00:16:07.350` | that executes the srett instruction |
| `00:16:07.360 - 00:16:11.509` | which pushes back in user mode |
| `00:16:11.519 - 00:16:13.430` | switch does uh |
| `00:16:13.440 - 00:16:15.269` | some things basically it unlocks the |
| `00:16:15.279 - 00:16:17.110` | process and goes off and |
| `00:16:17.120 - 00:16:19.350` | invokes a scheduler and may execute some |
| `00:16:19.360 - 00:16:21.509` | other processes but eventually it |
| `00:16:21.519 - 00:16:23.110` | requires the lock |
| `00:16:23.120 - 00:16:26.230` | and at that point switch will load all |
| `00:16:26.240 - 00:16:28.870` | the registers and |
| `00:16:28.880 - 00:16:30.710` | return to ra |
| `00:16:30.720 - 00:16:33.030` | okay and normally that would be a return |
| `00:16:33.040 - 00:16:34.870` | address in the sked |
| `00:16:34.880 - 00:16:37.910` | function but in the case of |
| `00:16:37.920 - 00:16:39.910` | a child process |
| `00:16:39.920 - 00:16:43.269` | we've pre-loaded ra with forecret so |
| `00:16:43.279 - 00:16:45.670` | when switch |
| `00:16:45.680 - 00:16:47.990` | returns it will be returning not to some |
| `00:16:48.000 - 00:16:50.389` | address in scad where it can finish this |
| `00:16:50.399 - 00:16:53.110` | process here but it will be essentially |
| `00:16:53.120 - 00:16:55.269` | jumping to fork red |
| `00:16:55.279 - 00:16:57.110` | well what does forecret do |
| `00:16:57.120 - 00:16:58.389` | it |
| `00:16:58.399 - 00:17:01.030` | unlocks the lock on the process |
| `00:17:01.040 - 00:17:03.990` | and calls user trap rent so essentially |
| `00:17:04.000 - 00:17:07.189` | it does this right here it takes us |
| `00:17:07.199 - 00:17:09.270` | into this point so we can complete the |
| `00:17:09.280 - 00:17:12.470` | return process |
| `00:17:12.480 - 00:17:14.470` | user trap rep will load the user |
| `00:17:14.480 - 00:17:16.549` | registers |
| `00:17:16.559 - 00:17:18.470` | remember we've saved a copy of the |
| `00:17:18.480 - 00:17:21.029` | parents user registers |
| `00:17:21.039 - 00:17:22.069` | we've also |
| `00:17:22.079 - 00:17:25.110` | modified a0 to b0 |
| `00:17:25.120 - 00:17:27.270` | but this is how the user registers were |
| `00:17:27.280 - 00:17:29.669` | saved and they will be loaded by user |
| `00:17:29.679 - 00:17:33.430` | trapret and it will call user rep and |
| `00:17:33.440 - 00:17:34.950` | ultimately the s-red instruction will |
| `00:17:34.960 - 00:17:37.590` | happen and we will resume execution |
| `00:17:37.600 - 00:17:38.950` | after the call |
| `00:17:38.960 - 00:17:41.909` | to fork or more precisely after the |
| `00:17:41.919 - 00:17:43.909` | e-call instruction |
| `00:17:43.919 - 00:17:46.070` | so just to complete this let's take a a |
| `00:17:46.080 - 00:17:48.789` | look at fork reps code |
| `00:17:48.799 - 00:17:51.590` | here is the four code for four gret and |
| `00:17:51.600 - 00:17:54.549` | you can see it does exactly what we said |
| `00:17:54.559 - 00:17:55.350` | um |
| `00:17:55.360 - 00:17:58.830` | it is uh going to |
| `00:17:58.840 - 00:18:00.549` | um |
| `00:18:00.559 - 00:18:03.669` | release the lock on the process |
| `00:18:03.679 - 00:18:04.950` | and |
| `00:18:04.960 - 00:18:07.830` | invoke user trap ret |
| `00:18:07.840 - 00:18:09.830` | this code here is only executed the |
| `00:18:09.840 - 00:18:12.630` | first time for the first time slice and |
| `00:18:12.640 - 00:18:14.390` | that would be for the init process when |
| `00:18:14.400 - 00:18:16.070` | uh it gets going and this just does a |
| `00:18:16.080 - 00:18:18.549` | little initialization it'll set first to |
| `00:18:18.559 - 00:18:21.190` | zero and from then on this code will |
| `00:18:21.200 - 00:18:23.590` | never get executed so we can ignore that |
| `00:18:23.600 - 00:18:26.390` | it won't be involved for any child |
| `00:18:26.400 - 00:18:28.150` | process |
| `00:18:28.160 - 00:18:30.310` | okay that's how the fork system call |
| `00:18:30.320 - 00:18:35.080` | works i'll see you in the next video |
