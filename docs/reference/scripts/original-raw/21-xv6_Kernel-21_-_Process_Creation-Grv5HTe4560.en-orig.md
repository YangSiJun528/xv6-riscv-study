# xv6 Kernel-21: Process Creation

| Field | Value |
| --- | --- |
| Video ID | `Grv5HTe4560` |
| URL | <https://www.youtube.com/watch?v=Grv5HTe4560> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.880 - 00:00:02.389` | this video is part of a series on the |
| `00:00:02.399 - 00:00:05.110` | xv6 operating system kernel |
| `00:00:05.120 - 00:00:07.349` | in this video i'm going to talk about |
| `00:00:07.359 - 00:00:09.830` | processes and in particular the data |
| `00:00:09.840 - 00:00:12.470` | structure that is used to represent a |
| `00:00:12.480 - 00:00:15.430` | process i'll talk about how the array of |
| `00:00:15.440 - 00:00:18.790` | proc structures is initialized and how |
| `00:00:18.800 - 00:00:19.830` | we set up |
| `00:00:19.840 - 00:00:20.710` | the |
| `00:00:20.720 - 00:00:22.630` | data for the initial |
| `00:00:22.640 - 00:00:24.470` | first process |
| `00:00:24.480 - 00:00:28.070` | this stuff comes from the file proc.c |
| `00:00:28.080 - 00:00:29.589` | which also contains a bunch of other |
| `00:00:29.599 - 00:00:32.150` | stuff so in addition to this material |
| `00:00:32.160 - 00:00:33.750` | that i'll be talking about |
| `00:00:33.760 - 00:00:35.590` | it also contains the code for the |
| `00:00:35.600 - 00:00:37.990` | scheduler for yield and so on |
| `00:00:38.000 - 00:00:40.229` | as well as the code for the sleep and |
| `00:00:40.239 - 00:00:42.310` | wake up functions i've covered these |
| `00:00:42.320 - 00:00:44.389` | things in previous videos |
| `00:00:44.399 - 00:00:47.190` | the file also contains code for |
| `00:00:47.200 - 00:00:49.830` | functions like fork and weight and kill |
| `00:00:49.840 - 00:00:52.630` | and i'll cover those in a future video |
| `00:00:52.640 - 00:00:54.630` | so let's begin with |
| `00:00:54.640 - 00:00:56.229` | the file itself |
| `00:00:56.239 - 00:01:00.069` | and um i want to start with these two |
| `00:01:00.079 - 00:01:02.549` | fields uh there's a |
| `00:01:02.559 - 00:01:05.429` | process id counter that's initialized to |
| `00:01:05.439 - 00:01:08.550` | one and there's a spin lock called pid |
| `00:01:08.560 - 00:01:09.429` | lock |
| `00:01:09.439 - 00:01:11.429` | so right off the bat we can take a look |
| `00:01:11.439 - 00:01:13.670` | at this function |
| `00:01:13.680 - 00:01:15.749` | aloc pid |
| `00:01:15.759 - 00:01:18.149` | so whenever we need a new |
| `00:01:18.159 - 00:01:21.109` | process id we can call this function and |
| `00:01:21.119 - 00:01:22.789` | it's pretty straightforward |
| `00:01:22.799 - 00:01:25.590` | it returns the new process id |
| `00:01:25.600 - 00:01:28.710` | and in order to access to read and |
| `00:01:28.720 - 00:01:31.429` | update the next pid |
| `00:01:31.439 - 00:01:33.670` | global variable we need to acquire the |
| `00:01:33.680 - 00:01:36.950` | lock first so this acquires the lock and |
| `00:01:36.960 - 00:01:40.149` | then it reads the current value into a |
| `00:01:40.159 - 00:01:41.429` | local variable |
| `00:01:41.439 - 00:01:42.469` | and |
| `00:01:42.479 - 00:01:44.789` | increments the global variable and then |
| `00:01:44.799 - 00:01:46.710` | releases the lock so this is pretty |
| `00:01:46.720 - 00:01:48.789` | straightforward use of acquire and |
| `00:01:48.799 - 00:01:50.230` | release |
| `00:01:50.240 - 00:01:51.910` | okay now that that's out of the way we |
| `00:01:51.920 - 00:01:53.350` | can |
| `00:01:53.360 - 00:01:55.749` | go into the |
| `00:01:55.759 - 00:01:57.429` | first function that i want to cover |
| `00:01:57.439 - 00:02:00.789` | which is proc net before i do that let |
| `00:02:00.799 - 00:02:02.149` | me |
| `00:02:02.159 - 00:02:04.630` | remind you of the structures that are |
| `00:02:04.640 - 00:02:07.270` | relevant for this video |
| `00:02:07.280 - 00:02:09.430` | we've got |
| `00:02:09.440 - 00:02:10.630` | an array |
| `00:02:10.640 - 00:02:13.750` | of proc structures so here i show one of |
| `00:02:13.760 - 00:02:16.630` | them and we have 64 of them in total one |
| `00:02:16.640 - 00:02:18.550` | for each process |
| `00:02:18.560 - 00:02:20.790` | and remember that they have a spin lock |
| `00:02:20.800 - 00:02:23.110` | and some fields such as the state |
| `00:02:23.120 - 00:02:24.949` | whether it's running or runnable or so |
| `00:02:24.959 - 00:02:25.830` | on |
| `00:02:25.840 - 00:02:28.949` | uh channel if it's sleeping a bullion |
| `00:02:28.959 - 00:02:31.509` | for whether the process has been killed |
| `00:02:31.519 - 00:02:33.830` | and some other things |
| `00:02:33.840 - 00:02:37.990` | we've also got a pointer to |
| `00:02:38.000 - 00:02:42.150` | the area in virtual memory where the |
| `00:02:42.160 - 00:02:45.190` | stack is located so |
| `00:02:45.200 - 00:02:47.589` | i'll come to that in a second |
| `00:02:47.599 - 00:02:49.990` | the size of the address space that is |
| `00:02:50.000 - 00:02:52.630` | the break point for where the top of the |
| `00:02:52.640 - 00:02:55.190` | heap is with this particular process |
| `00:02:55.200 - 00:02:57.589` | point of the page table a pointer to the |
| `00:02:57.599 - 00:02:59.030` | trap frame |
| `00:02:59.040 - 00:03:01.990` | a context for saving it and a few other |
| `00:03:02.000 - 00:03:05.670` | things such as the name field |
| `00:03:05.680 - 00:03:08.149` | so i'm not going to go over that just |
| `00:03:08.159 - 00:03:10.790` | now but i do want to talk about this |
| `00:03:10.800 - 00:03:13.589` | function proc knit which initially |
| `00:03:13.599 - 00:03:16.869` | initializes the proc array |
| `00:03:16.879 - 00:03:18.869` | this is called once |
| `00:03:18.879 - 00:03:21.270` | by core zero only |
| `00:03:21.280 - 00:03:23.589` | during the initialization of the kernel |
| `00:03:23.599 - 00:03:27.030` | and it initializes that lock for the |
| `00:03:27.040 - 00:03:29.509` | process id that we just saw |
| `00:03:29.519 - 00:03:30.869` | and |
| `00:03:30.879 - 00:03:34.470` | it also initializes another lock |
| `00:03:34.480 - 00:03:37.270` | called weight pid |
| `00:03:37.280 - 00:03:38.869` | sorry weight lock |
| `00:03:38.879 - 00:03:40.149` | and then |
| `00:03:40.159 - 00:03:43.190` | it goes through this proc array okay so |
| `00:03:43.200 - 00:03:45.350` | it's going through the proc array and |
| `00:03:45.360 - 00:03:48.229` | for each one of them it initializes the |
| `00:03:48.239 - 00:03:49.430` | spin lock |
| `00:03:49.440 - 00:03:50.630` | okay so |
| `00:03:50.640 - 00:03:53.030` | it goes through |
| `00:03:53.040 - 00:03:54.470` | oops |
| `00:03:54.480 - 00:03:56.550` | it goes through this array and for each |
| `00:03:56.560 - 00:03:59.190` | one it initializes the spin lock |
| `00:03:59.200 - 00:04:00.470` | and |
| `00:04:00.480 - 00:04:02.630` | it also initializes |
| `00:04:02.640 - 00:04:05.030` | this k stack okay |
| `00:04:05.040 - 00:04:07.030` | so at this point i'll talk about what |
| `00:04:07.040 - 00:04:09.350` | the virtual address space looks like |
| `00:04:09.360 - 00:04:11.270` | for the kernel remember that the |
| `00:04:11.280 - 00:04:13.350` | kernel's virtual address space like all |
| `00:04:13.360 - 00:04:15.190` | outer spaces will have a trampoline page |
| `00:04:15.200 - 00:04:17.830` | mapped into the uppermost page |
| `00:04:17.840 - 00:04:21.030` | and then it has a series of guard pages |
| `00:04:21.040 - 00:04:23.430` | and stack pages |
| `00:04:23.440 - 00:04:27.030` | so for each of the 64 processes there is |
| `00:04:27.040 - 00:04:30.070` | one page in the virtual address space |
| `00:04:30.080 - 00:04:31.030` | for |
| `00:04:31.040 - 00:04:32.870` | the stack that will be used when that |
| `00:04:32.880 - 00:04:35.749` | process is running in kernel mode |
| `00:04:35.759 - 00:04:36.469` | so |
| `00:04:36.479 - 00:04:37.350` | uh |
| `00:04:37.360 - 00:04:38.230` | at |
| `00:04:38.240 - 00:04:40.310` | this point here |
| `00:04:40.320 - 00:04:41.510` | uh in |
| `00:04:41.520 - 00:04:42.710` | the |
| `00:04:42.720 - 00:04:44.710` | proconet function |
| `00:04:44.720 - 00:04:47.030` | we are |
| `00:04:47.040 - 00:04:50.230` | determining the virtual address |
| `00:04:50.240 - 00:04:52.469` | this is a preprocessor macro function |
| `00:04:52.479 - 00:04:55.510` | given a number between 0 and 63. it |
| `00:04:55.520 - 00:04:57.909` | determines the virtual address for that |
| `00:04:57.919 - 00:05:01.510` | stack page and it simply saves it in the |
| `00:05:01.520 - 00:05:05.189` | k stack field of this proc structure so |
| `00:05:05.199 - 00:05:07.510` | here's the proc structure again here's |
| `00:05:07.520 - 00:05:10.230` | the case stack field and it just says |
| `00:05:10.240 - 00:05:12.550` | the virtual address and i i added this |
| `00:05:12.560 - 00:05:14.070` | dashed line to indicate this is a |
| `00:05:14.080 - 00:05:15.590` | virtual address |
| `00:05:15.600 - 00:05:17.510` | uh as opposed to |
| `00:05:17.520 - 00:05:19.029` | these links here |
| `00:05:19.039 - 00:05:21.189` | the page table points to a physical |
| `00:05:21.199 - 00:05:23.430` | address somewhere somewhere in physical |
| `00:05:23.440 - 00:05:26.629` | memory as well as and the trap frame |
| `00:05:26.639 - 00:05:28.550` | pointer which points to the actual |
| `00:05:28.560 - 00:05:30.790` | physical address of the trap frame |
| `00:05:30.800 - 00:05:32.950` | the trap frame of course will be mapped |
| `00:05:32.960 - 00:05:34.710` | into the second highest page of the |
| `00:05:34.720 - 00:05:36.629` | virtual address space but this pointer |
| `00:05:36.639 - 00:05:38.230` | right here is to the actual physical |
| `00:05:38.240 - 00:05:41.430` | page that was returned from k alec we'll |
| `00:05:41.440 - 00:05:43.510` | see that happening in a second |
| `00:05:43.520 - 00:05:44.950` | uh there are a couple of other functions |
| `00:05:44.960 - 00:05:46.710` | in this that i've already mentioned but |
| `00:05:46.720 - 00:05:49.830` | i'll just uh review them |
| `00:05:49.840 - 00:05:52.310` | cpu id returns the number of the core |
| `00:05:52.320 - 00:05:53.990` | that's currently executing this function |
| `00:05:54.000 - 00:05:56.150` | by just returning the value of the tp |
| `00:05:56.160 - 00:05:57.590` | register |
| `00:05:57.600 - 00:06:00.070` | the my cpu |
| `00:06:00.080 - 00:06:01.510` | uses that |
| `00:06:01.520 - 00:06:03.430` | current core number and returns a |
| `00:06:03.440 - 00:06:06.790` | pointer to the cpu struct |
| `00:06:06.800 - 00:06:11.029` | okay so here is the array of cpu structs |
| `00:06:11.039 - 00:06:13.510` | and here i'm showing one there are up to |
| `00:06:13.520 - 00:06:16.790` | eight cores uh that's a fixed constant |
| `00:06:16.800 - 00:06:20.309` | and each one of them has a cpu structure |
| `00:06:20.319 - 00:06:21.909` | and uh |
| `00:06:21.919 - 00:06:24.230` | so we're uh |
| `00:06:24.240 - 00:06:25.909` | returning a pointer to the one for the |
| `00:06:25.919 - 00:06:27.749` | current core |
| `00:06:27.759 - 00:06:30.870` | and uh finally we have the my proc |
| `00:06:30.880 - 00:06:32.469` | uh routine |
| `00:06:32.479 - 00:06:35.990` | which uh gets the |
| `00:06:36.000 - 00:06:38.870` | pointer to the current cpu struct and |
| `00:06:38.880 - 00:06:41.189` | then follows the proc field to get a |
| `00:06:41.199 - 00:06:43.189` | pointer to the |
| `00:06:43.199 - 00:06:44.870` | structure that represents the procedure |
| `00:06:44.880 - 00:06:46.629` | that's currently executing |
| `00:06:46.639 - 00:06:47.430` | okay |
| `00:06:47.440 - 00:06:50.629` | each cpu struct |
| `00:06:50.639 - 00:06:52.950` | each cpu struct has a proc field that |
| `00:06:52.960 - 00:06:56.550` | points to a proc structure |
| `00:06:56.560 - 00:06:58.550` | each core at any one point in time is |
| `00:06:58.560 - 00:07:00.070` | either executing |
| `00:07:00.080 - 00:07:03.430` | the scheduler code or it's executing one |
| `00:07:03.440 - 00:07:05.670` | of the processes if it's executing a |
| `00:07:05.680 - 00:07:07.430` | process then this pointer is valid it |
| `00:07:07.440 - 00:07:09.589` | points to a proc struct |
| `00:07:09.599 - 00:07:13.110` | if the cpu is executing a the scheduler |
| `00:07:13.120 - 00:07:14.830` | code then this will be |
| `00:07:14.840 - 00:07:18.790` | null okay so those functions are |
| `00:07:18.800 - 00:07:20.469` | pretty straightforward |
| `00:07:20.479 - 00:07:23.589` | next let's take a look at |
| `00:07:23.599 - 00:07:25.110` | proc dump |
| `00:07:25.120 - 00:07:28.309` | i think proc dump is |
| `00:07:28.319 - 00:07:31.350` | simple basically this is for debugging |
| `00:07:31.360 - 00:07:34.550` | and it's just going to go through |
| `00:07:34.560 - 00:07:35.430` | the |
| `00:07:35.440 - 00:07:38.070` | proc array and print it out print out |
| `00:07:38.080 - 00:07:39.350` | information |
| `00:07:39.360 - 00:07:41.670` | so um |
| `00:07:41.680 - 00:07:44.629` | it's past nothing and it returns nothing |
| `00:07:44.639 - 00:07:47.110` | and what does it do well as i said it's |
| `00:07:47.120 - 00:07:49.189` | a for loop that goes through the entire |
| `00:07:49.199 - 00:07:53.430` | array of all 64 proc structures here |
| `00:07:53.440 - 00:07:55.350` | and |
| `00:07:55.360 - 00:07:57.110` | if that |
| `00:07:57.120 - 00:08:00.070` | struct is unused okay in other words if |
| `00:08:00.080 - 00:08:02.070` | the state here is unused and we just |
| `00:08:02.080 - 00:08:04.309` | don't print out anything |
| `00:08:04.319 - 00:08:08.550` | so we continue and repeat the loop body |
| `00:08:08.560 - 00:08:11.510` | otherwise we look up the state |
| `00:08:11.520 - 00:08:13.029` | in the |
| `00:08:13.039 - 00:08:14.629` | array that we have here we have a little |
| `00:08:14.639 - 00:08:15.990` | array that |
| `00:08:16.000 - 00:08:17.909` | maps the |
| `00:08:17.919 - 00:08:19.909` | constants unused sleeping runnable |
| `00:08:19.919 - 00:08:22.309` | running and zombie into |
| `00:08:22.319 - 00:08:25.110` | short strings and so we grab the string |
| `00:08:25.120 - 00:08:26.710` | here and |
| `00:08:26.720 - 00:08:28.390` | called state and we're here we're just |
| `00:08:28.400 - 00:08:30.550` | checking to make sure it's a valid |
| `00:08:30.560 - 00:08:31.990` | a valid number |
| `00:08:32.000 - 00:08:33.509` | otherwise we use |
| `00:08:33.519 - 00:08:36.230` | that and then we print a single line so |
| `00:08:36.240 - 00:08:38.870` | this is going to print a line for the |
| `00:08:38.880 - 00:08:42.070` | process with the process id its current |
| `00:08:42.080 - 00:08:45.350` | state and the name okay the name field |
| `00:08:45.360 - 00:08:46.870` | is a fixed |
| `00:08:46.880 - 00:08:50.710` | length uh field and we can store a short |
| `00:08:50.720 - 00:08:52.310` | name |
| `00:08:52.320 - 00:08:54.710` | in that field and so here we we print |
| `00:08:54.720 - 00:08:55.590` | the name |
| `00:08:55.600 - 00:08:57.590` | okay so that's uh pretty straightforward |
| `00:08:57.600 - 00:08:59.509` | that's proc dub |
| `00:08:59.519 - 00:09:01.910` | now we're going to get into |
| `00:09:01.920 - 00:09:04.150` | the alec |
| `00:09:04.160 - 00:09:05.030` | uh |
| `00:09:05.040 - 00:09:06.150` | function |
| `00:09:06.160 - 00:09:08.870` | um so |
| `00:09:08.880 - 00:09:10.949` | let's look at what this function does |
| `00:09:10.959 - 00:09:12.790` | okay first of all it's past nothing when |
| `00:09:12.800 - 00:09:15.269` | we need a procedure when we need a proc |
| `00:09:15.279 - 00:09:16.389` | struct |
| `00:09:16.399 - 00:09:18.230` | uh to uh |
| `00:09:18.240 - 00:09:21.110` | create a new process we call alec proc |
| `00:09:21.120 - 00:09:23.030` | and it's going to find one and |
| `00:09:23.040 - 00:09:25.509` | initialize it and return a pointer to it |
| `00:09:25.519 - 00:09:27.590` | okay look in the process table for an |
| `00:09:27.600 - 00:09:31.269` | unused proc if found initialize it and |
| `00:09:31.279 - 00:09:32.310` | return |
| `00:09:32.320 - 00:09:34.470` | with the lock held okay |
| `00:09:34.480 - 00:09:37.350` | and if there's a problem it returns no |
| `00:09:37.360 - 00:09:39.110` | so here's what it's going to do this is |
| `00:09:39.120 - 00:09:41.269` | an outline of what it does what the code |
| `00:09:41.279 - 00:09:42.230` | does |
| `00:09:42.240 - 00:09:44.949` | it first searches for an unused proc |
| `00:09:44.959 - 00:09:46.310` | structure |
| `00:09:46.320 - 00:09:49.910` | then it calls k alec to allocate a trap |
| `00:09:49.920 - 00:09:51.750` | frame page |
| `00:09:51.760 - 00:09:54.230` | for that process |
| `00:09:54.240 - 00:09:57.030` | then it creates a new page table |
| `00:09:57.040 - 00:10:00.070` | adding mappings for the trampoline page |
| `00:10:00.080 - 00:10:02.069` | which of course is shared by all virtual |
| `00:10:02.079 - 00:10:03.430` | address spaces |
| `00:10:03.440 - 00:10:06.710` | and a mapping for the trap frame page |
| `00:10:06.720 - 00:10:08.630` | that just got allocated |
| `00:10:08.640 - 00:10:11.910` | and then it sets up a the context or |
| `00:10:11.920 - 00:10:14.150` | maybe i should say it initializes the |
| `00:10:14.160 - 00:10:17.190` | context in preparation for the first |
| `00:10:17.200 - 00:10:18.550` | time slice |
| `00:10:18.560 - 00:10:23.110` | of this process recall that in the |
| `00:10:23.120 - 00:10:25.509` | proc structure we have this |
| `00:10:25.519 - 00:10:28.069` | context area and this is the register |
| `00:10:28.079 - 00:10:30.710` | save area so we do some initialization |
| `00:10:30.720 - 00:10:33.110` | there and |
| `00:10:33.120 - 00:10:34.630` | uh |
| `00:10:34.640 - 00:10:37.509` | the address space that got created |
| `00:10:37.519 - 00:10:39.110` | we you know we've created a page table |
| `00:10:39.120 - 00:10:40.550` | here but we didn't put anything in it so |
| `00:10:40.560 - 00:10:42.389` | there's no code or data yet |
| `00:10:42.399 - 00:10:44.470` | uh alec proc |
| `00:10:44.480 - 00:10:46.630` | this alec proc function is not |
| `00:10:46.640 - 00:10:48.310` | responsible for that |
| `00:10:48.320 - 00:10:50.870` | but uh it'll be added later |
| `00:10:50.880 - 00:10:52.550` | and if there are any problems |
| `00:10:52.560 - 00:10:54.150` | this function has to undo everything |
| `00:10:54.160 - 00:10:55.190` | return all |
| `00:10:55.200 - 00:10:57.430` | allocated pages back to the free pool by |
| `00:10:57.440 - 00:10:59.110` | calling kfree |
| `00:10:59.120 - 00:11:01.829` | and then it returns no |
| `00:11:01.839 - 00:11:03.590` | okay so let's take a look at the code |
| `00:11:03.600 - 00:11:04.790` | here |
| `00:11:04.800 - 00:11:05.670` | um |
| `00:11:05.680 - 00:11:08.069` | here we are looking for |
| `00:11:08.079 - 00:11:11.030` | a proc structure that has a status or |
| `00:11:11.040 - 00:11:13.750` | state of unused so we're just |
| `00:11:13.760 - 00:11:15.190` | looping in this for loop through the |
| `00:11:15.200 - 00:11:18.069` | entire array of all 64 elements and for |
| `00:11:18.079 - 00:11:20.710` | each we acquire the lock if it's uh |
| `00:11:20.720 - 00:11:23.910` | unused then we have found something |
| `00:11:23.920 - 00:11:26.389` | uh another go-to here to this label down |
| `00:11:26.399 - 00:11:28.389` | here |
| `00:11:28.399 - 00:11:30.790` | but if it's in use |
| `00:11:30.800 - 00:11:32.230` | then we release the lock and keep |
| `00:11:32.240 - 00:11:34.470` | looking okay and if we can't find |
| `00:11:34.480 - 00:11:36.389` | anything we return our null |
| `00:11:36.399 - 00:11:38.389` | okay let's say we find something well |
| `00:11:38.399 - 00:11:39.430` | that's good |
| `00:11:39.440 - 00:11:42.230` | uh so here we fill in the process id |
| `00:11:42.240 - 00:11:46.389` | each process will get a new identifier |
| `00:11:46.399 - 00:11:47.990` | no telling what happens when this |
| `00:11:48.000 - 00:11:50.230` | variable rolls over we don't worry about |
| `00:11:50.240 - 00:11:51.990` | that sort of thing in xv6 because it's |
| `00:11:52.000 - 00:11:53.509` | not going to ever happen |
| `00:11:53.519 - 00:11:54.870` | in our lifetime |
| `00:11:54.880 - 00:11:57.590` | and we set the state to |
| `00:11:57.600 - 00:12:00.069` | used so that's so one of the states that |
| `00:12:00.079 - 00:12:01.110` | we |
| `00:12:01.120 - 00:12:02.870` | can have in |
| `00:12:02.880 - 00:12:06.629` | the um |
| `00:12:06.639 - 00:12:08.389` | i just remember that |
| `00:12:08.399 - 00:12:10.389` | the used state is |
| `00:12:10.399 - 00:12:12.389` | not listed here |
| `00:12:12.399 - 00:12:13.590` | okay |
| `00:12:13.600 - 00:12:17.030` | um well in any case uh it goes into this |
| `00:12:17.040 - 00:12:19.509` | state of being used |
| `00:12:19.519 - 00:12:20.070` | and |
| `00:12:20.080 - 00:12:21.910` | [Music] |
| `00:12:21.920 - 00:12:23.430` | will |
| `00:12:23.440 - 00:12:25.190` | the caller of this will be responsible |
| `00:12:25.200 - 00:12:28.310` | for making it running or sorry runnable |
| `00:12:28.320 - 00:12:29.990` | at some future time |
| `00:12:30.000 - 00:12:31.269` | okay so |
| `00:12:31.279 - 00:12:33.590` | then we allocate the trap frame page so |
| `00:12:33.600 - 00:12:35.590` | here we're calling k alec and if |
| `00:12:35.600 - 00:12:37.509` | anything goes wrong |
| `00:12:37.519 - 00:12:39.190` | well we're going to release the lock and |
| `00:12:39.200 - 00:12:40.470` | return no |
| `00:12:40.480 - 00:12:42.550` | and we're also going to call this free |
| `00:12:42.560 - 00:12:43.430` | proc |
| `00:12:43.440 - 00:12:45.190` | function i'm going to cover that one |
| `00:12:45.200 - 00:12:47.430` | next but basically just it's called |
| `00:12:47.440 - 00:12:48.710` | anytime there's a problem we see it |
| `00:12:48.720 - 00:12:50.389` | being called down here as well it's |
| `00:12:50.399 - 00:12:52.629` | basically going to |
| `00:12:52.639 - 00:12:56.230` | make the proc structure unused again and |
| `00:12:56.240 - 00:12:58.550` | zero out all the fields so that it could |
| `00:12:58.560 - 00:13:00.230` | be recycled |
| `00:13:00.240 - 00:13:02.310` | okay so |
| `00:13:02.320 - 00:13:05.430` | now we create the page table so |
| `00:13:05.440 - 00:13:08.230` | here we call a proc page table and i'll |
| `00:13:08.240 - 00:13:10.790` | cover that uh in a moment here but |
| `00:13:10.800 - 00:13:12.150` | that's going to |
| `00:13:12.160 - 00:13:14.389` | create the page table |
| `00:13:14.399 - 00:13:16.710` | and um |
| `00:13:16.720 - 00:13:19.430` | add a couple mappings but if that's all |
| `00:13:19.440 - 00:13:21.990` | okay then we keep going but otherwise we |
| `00:13:22.000 - 00:13:24.069` | call this free proc to undo what we've |
| `00:13:24.079 - 00:13:26.790` | done release the lock and return null |
| `00:13:26.800 - 00:13:28.790` | just as we did up here |
| `00:13:28.800 - 00:13:32.629` | alec proc is called in two places one is |
| `00:13:32.639 - 00:13:35.990` | user init to create the first |
| `00:13:36.000 - 00:13:37.190` | process |
| `00:13:37.200 - 00:13:40.470` | the init process and the other place is |
| `00:13:40.480 - 00:13:43.110` | the fork function which is used for the |
| `00:13:43.120 - 00:13:44.790` | fork system call |
| `00:13:44.800 - 00:13:45.750` | and |
| `00:13:45.760 - 00:13:47.910` | in any case what's going to happen after |
| `00:13:47.920 - 00:13:50.629` | the process is created is it's going to |
| `00:13:50.639 - 00:13:53.030` | be scheduled to be run |
| `00:13:53.040 - 00:13:54.870` | so |
| `00:13:54.880 - 00:13:56.790` | we need to kind of set things up for |
| `00:13:56.800 - 00:13:58.629` | that so |
| `00:13:58.639 - 00:14:02.230` | remember that in the proc |
| `00:14:02.240 - 00:14:04.230` | structure we have |
| `00:14:04.240 - 00:14:06.230` | the save area for the registers this |
| `00:14:06.240 - 00:14:07.350` | would be the |
| `00:14:07.360 - 00:14:09.430` | registers that the kernels |
| `00:14:09.440 - 00:14:11.910` | the the process is going to use when it |
| `00:14:11.920 - 00:14:13.189` | starts running and of course it will |
| `00:14:13.199 - 00:14:16.230` | start running when it gets scheduled in |
| `00:14:16.240 - 00:14:19.269` | kernel mode so you've got the registers |
| `00:14:19.279 - 00:14:20.710` | that will be |
| `00:14:20.720 - 00:14:21.910` | used |
| `00:14:21.920 - 00:14:25.750` | or restored i should say and the ra |
| `00:14:25.760 - 00:14:27.910` | and sp registers the return address and |
| `00:14:27.920 - 00:14:29.509` | the stack pointer |
| `00:14:29.519 - 00:14:32.790` | okay so this is a new process |
| `00:14:32.800 - 00:14:33.910` | it's not |
| `00:14:33.920 - 00:14:36.150` | getting rescheduled it's getting |
| `00:14:36.160 - 00:14:38.069` | scheduled for the first time |
| `00:14:38.079 - 00:14:40.790` | so we need to have a return address |
| `00:14:40.800 - 00:14:43.590` | and a stack pointer for its initial |
| `00:14:43.600 - 00:14:47.509` | scheduling so we set the context well |
| `00:14:47.519 - 00:14:49.269` | first of all we we clear out all the |
| `00:14:49.279 - 00:14:52.230` | registers so mimset just writes a zero |
| `00:14:52.240 - 00:14:54.310` | to all of the registers in the register |
| `00:14:54.320 - 00:14:56.949` | save area and then we initialize |
| `00:14:56.959 - 00:15:00.629` | the ra register to point to this fork |
| `00:15:00.639 - 00:15:02.470` | ret function |
| `00:15:02.480 - 00:15:04.389` | i'll look at that in just one second and |
| `00:15:04.399 - 00:15:07.269` | we initialize the sp register to point |
| `00:15:07.279 - 00:15:08.389` | to the |
| `00:15:08.399 - 00:15:09.590` | stack |
| `00:15:09.600 - 00:15:11.829` | page |
| `00:15:11.839 - 00:15:14.230` | remember that |
| `00:15:14.240 - 00:15:17.189` | each process has a page in virtual |
| `00:15:17.199 - 00:15:18.310` | memory |
| `00:15:18.320 - 00:15:19.509` | and |
| `00:15:19.519 - 00:15:22.389` | for example process two has this page at |
| `00:15:22.399 - 00:15:24.790` | this address in virtual memory and |
| `00:15:24.800 - 00:15:26.870` | stacks grow downward so we want to |
| `00:15:26.880 - 00:15:28.710` | initialize the stack pointer to point to |
| `00:15:28.720 - 00:15:30.710` | the top of this so that it can begin to |
| `00:15:30.720 - 00:15:33.910` | grow downward from this zone so we |
| `00:15:33.920 - 00:15:36.949` | initialize the stack pointer so |
| `00:15:36.959 - 00:15:39.269` | when this process is scheduled that is |
| `00:15:39.279 - 00:15:41.509` | when it has its first time slice |
| `00:15:41.519 - 00:15:45.990` | it will begin executing at the address |
| `00:15:46.000 - 00:15:50.069` | given here for correct with a stack |
| `00:15:50.079 - 00:15:51.829` | what we're going to do after we call |
| `00:15:51.839 - 00:15:55.509` | alec proc is basically fill in the code |
| `00:15:55.519 - 00:15:56.710` | and the data |
| `00:15:56.720 - 00:15:59.990` | and we also need to release the lock |
| `00:16:00.000 - 00:16:02.150` | remember that we have acquired a lock up |
| `00:16:02.160 - 00:16:04.710` | here we need to release the lock and |
| `00:16:04.720 - 00:16:07.030` | then this thread can go on and do |
| `00:16:07.040 - 00:16:08.710` | whatever it wants to do but at some |
| `00:16:08.720 - 00:16:11.110` | point the scheduler will select this |
| `00:16:11.120 - 00:16:12.310` | process |
| `00:16:12.320 - 00:16:14.069` | and it will |
| `00:16:14.079 - 00:16:16.069` | acquire the lock and it will |
| `00:16:16.079 - 00:16:18.150` | for that process and it will schedule it |
| `00:16:18.160 - 00:16:19.749` | and then it will |
| `00:16:19.759 - 00:16:22.550` | load the registers from the context |
| `00:16:22.560 - 00:16:24.949` | and that will include the return address |
| `00:16:24.959 - 00:16:25.829` | and so |
| `00:16:25.839 - 00:16:28.629` | this process will begin every process |
| `00:16:28.639 - 00:16:31.110` | including the inet process we'll begin |
| `00:16:31.120 - 00:16:34.790` | by executing the fork rep function so |
| `00:16:34.800 - 00:16:36.710` | here's the fork rep function |
| `00:16:36.720 - 00:16:40.629` | and we can see what it does |
| `00:16:40.639 - 00:16:42.230` | it |
| `00:16:42.240 - 00:16:43.509` | well remember when we first get |
| `00:16:43.519 - 00:16:45.670` | scheduled we are holding the process |
| `00:16:45.680 - 00:16:48.310` | lock so we have to release that lock |
| `00:16:48.320 - 00:16:51.430` | and uh we have nothing more to do except |
| `00:16:51.440 - 00:16:54.550` | the um return from the trap well we |
| `00:16:54.560 - 00:16:57.670` | haven't really had a trap but we begin |
| `00:16:57.680 - 00:16:59.590` | by returning from the trap in the case |
| `00:16:59.600 - 00:17:01.910` | of a fork we have had the trap in the |
| `00:17:01.920 - 00:17:04.949` | case of the initial process we are going |
| `00:17:04.959 - 00:17:07.189` | to fake the trap |
| `00:17:07.199 - 00:17:10.710` | but in any case we do a trap return here |
| `00:17:10.720 - 00:17:13.829` | this other other stuff in fork gret |
| `00:17:13.839 - 00:17:15.110` | you can see that you've got a global |
| `00:17:15.120 - 00:17:17.350` | variable first or static variable i |
| `00:17:17.360 - 00:17:20.069` | should say that's initialized to true so |
| `00:17:20.079 - 00:17:24.150` | this will be done one time the first |
| `00:17:24.160 - 00:17:26.789` | time that any process is scheduled |
| `00:17:26.799 - 00:17:27.909` | it will |
| `00:17:27.919 - 00:17:29.350` | execute this code |
| `00:17:29.360 - 00:17:31.590` | then set first to false |
| `00:17:31.600 - 00:17:32.870` | and it says the file system |
| `00:17:32.880 - 00:17:34.470` | initialization must be run in the |
| `00:17:34.480 - 00:17:36.950` | context of a regular process |
| `00:17:36.960 - 00:17:39.190` | because it calls sleep |
| `00:17:39.200 - 00:17:42.710` | and thus it cannot be run from the main |
| `00:17:42.720 - 00:17:45.669` | function so that's what's going on here |
| `00:17:45.679 - 00:17:47.990` | when we are just about to schedule the |
| `00:17:48.000 - 00:17:50.150` | initial process we will |
| `00:17:50.160 - 00:17:52.310` | see that first is one |
| `00:17:52.320 - 00:17:55.909` | and we will invoke this file system init |
| `00:17:55.919 - 00:17:56.870` | function |
| `00:17:56.880 - 00:17:59.430` | and take care of that before we do the |
| `00:17:59.440 - 00:18:01.320` | user trap rat to the |
| `00:18:01.330 - 00:18:03.430` | [Music] |
| `00:18:03.440 - 00:18:07.190` | first bite of the initial process |
| `00:18:07.200 - 00:18:10.310` | okay next let's take a look at |
| `00:18:10.320 - 00:18:11.669` | um |
| `00:18:11.679 - 00:18:13.270` | free proc |
| `00:18:13.280 - 00:18:15.750` | so if anything goes wrong |
| `00:18:15.760 - 00:18:17.029` | uh and |
| `00:18:17.039 - 00:18:18.870` | we'll call free proc and when we're done |
| `00:18:18.880 - 00:18:21.909` | with a process we'll also call free proc |
| `00:18:21.919 - 00:18:24.710` | so what's it gonna do um well it's |
| `00:18:24.720 - 00:18:26.549` | passed a pointer to the procedure that |
| `00:18:26.559 - 00:18:29.110` | you know we're abandoning |
| `00:18:29.120 - 00:18:33.029` | and it looks at the trap frame pointer |
| `00:18:33.039 - 00:18:34.070` | okay |
| `00:18:34.080 - 00:18:36.470` | looks at the trapper and pointer |
| `00:18:36.480 - 00:18:39.029` | oops here we go and it asks if it's not |
| `00:18:39.039 - 00:18:41.190` | null then it points to something so |
| `00:18:41.200 - 00:18:43.909` | return that to the free pool so |
| `00:18:43.919 - 00:18:45.990` | that happens here |
| `00:18:46.000 - 00:18:48.950` | and sets the track frame to null and |
| `00:18:48.960 - 00:18:50.789` | then it looks at the page table pointer |
| `00:18:50.799 - 00:18:54.310` | okay it looks at this pointer here |
| `00:18:54.320 - 00:18:56.390` | to the page table and it needs to |
| `00:18:56.400 - 00:18:57.430` | um |
| `00:18:57.440 - 00:19:00.630` | basically destroy that page table and so |
| `00:19:00.640 - 00:19:03.750` | we'll uh discuss page table and proc |
| `00:19:03.760 - 00:19:05.830` | page table |
| `00:19:05.840 - 00:19:08.789` | and proc free page table in a second |
| `00:19:08.799 - 00:19:11.190` | here but basically it's going to call |
| `00:19:11.200 - 00:19:14.310` | proc free page table to undo that page |
| `00:19:14.320 - 00:19:17.110` | table return everything to the free pool |
| `00:19:17.120 - 00:19:19.669` | and then it uh zeroes out uh uh some of |
| `00:19:19.679 - 00:19:22.870` | the remaining fields um |
| `00:19:22.880 - 00:19:25.270` | you know it this is not strictly |
| `00:19:25.280 - 00:19:27.270` | necessary but it does all of this it's a |
| `00:19:27.280 - 00:19:29.190` | null in the name field |
| `00:19:29.200 - 00:19:32.150` | and so on but in most important it sets |
| `00:19:32.160 - 00:19:35.350` | the state to unused so at this point |
| `00:19:35.360 - 00:19:37.270` | this |
| `00:19:37.280 - 00:19:38.630` | process |
| `00:19:38.640 - 00:19:41.750` | is the proc structure is unused |
| `00:19:41.760 - 00:19:43.430` | okay so |
| `00:19:43.440 - 00:19:44.950` | in |
| `00:19:44.960 - 00:19:46.470` | uh alec |
| `00:19:46.480 - 00:19:49.190` | proc so returning to the function alec |
| `00:19:49.200 - 00:19:51.430` | brock |
| `00:19:51.440 - 00:19:52.390` | we |
| `00:19:52.400 - 00:19:53.430` | found |
| `00:19:53.440 - 00:19:54.310` | a |
| `00:19:54.320 - 00:19:56.789` | proc structure |
| `00:19:56.799 - 00:19:57.590` | and |
| `00:19:57.600 - 00:20:00.950` | um set up the trap frame |
| `00:20:00.960 - 00:20:04.710` | and then we call proc page table okay |
| `00:20:04.720 - 00:20:05.510` | to |
| `00:20:05.520 - 00:20:08.149` | create an empty user page table so now |
| `00:20:08.159 - 00:20:14.710` | let's look at proc page table |
| `00:20:14.720 - 00:20:15.830` | so |
| `00:20:15.840 - 00:20:18.149` | create a huge a user page table for a |
| `00:20:18.159 - 00:20:19.909` | given process so it's passed a pointer |
| `00:20:19.919 - 00:20:21.830` | to the proc structure |
| `00:20:21.840 - 00:20:23.110` | and |
| `00:20:23.120 - 00:20:25.909` | it's going to |
| `00:20:25.919 - 00:20:29.270` | set up the page table and return a |
| `00:20:29.280 - 00:20:31.750` | pointer to that page table |
| `00:20:31.760 - 00:20:32.950` | and |
| `00:20:32.960 - 00:20:36.230` | as we saw in alec proc it'll just store |
| `00:20:36.240 - 00:20:38.549` | that in the page table field |
| `00:20:38.559 - 00:20:40.630` | okay so |
| `00:20:40.640 - 00:20:43.110` | what does it do well we've covered uvm |
| `00:20:43.120 - 00:20:45.669` | create to create an empty page table |
| `00:20:45.679 - 00:20:48.630` | okay so that just uh sort of creates the |
| `00:20:48.640 - 00:20:50.710` | user page table and if there's any |
| `00:20:50.720 - 00:20:53.350` | problem we return zero |
| `00:20:53.360 - 00:20:54.470` | next we |
| `00:20:54.480 - 00:20:55.590` | map |
| `00:20:55.600 - 00:20:57.270` | we create a mapping for the trampoline |
| `00:20:57.280 - 00:20:58.230` | page |
| `00:20:58.240 - 00:20:59.990` | okay so the trampoline |
| `00:21:00.000 - 00:21:01.669` | address this is the address of the |
| `00:21:01.679 - 00:21:03.350` | highest page in the virtual address |
| `00:21:03.360 - 00:21:04.630` | space |
| `00:21:04.640 - 00:21:08.549` | it's one page in length and we map it to |
| `00:21:08.559 - 00:21:11.590` | the trampoline page this is the page in |
| `00:21:11.600 - 00:21:14.549` | the kernel's code area marking it read |
| `00:21:14.559 - 00:21:17.350` | and executable and so we've discussed |
| `00:21:17.360 - 00:21:19.830` | map pages so that adds a mapping to the |
| `00:21:19.840 - 00:21:22.549` | page table for the trampoline page |
| `00:21:22.559 - 00:21:24.950` | and if there is a problem with that then |
| `00:21:24.960 - 00:21:25.990` | we |
| `00:21:26.000 - 00:21:29.270` | free the page table that we've created |
| `00:21:29.280 - 00:21:32.950` | and just return okay next we map the |
| `00:21:32.960 - 00:21:35.350` | trap frame to the page the second |
| `00:21:35.360 - 00:21:36.870` | highest page |
| `00:21:36.880 - 00:21:41.669` | and |
| `00:21:41.679 - 00:21:45.110` | we call map pages again uh the trap |
| `00:21:45.120 - 00:21:47.430` | frame address is the second highest page |
| `00:21:47.440 - 00:21:50.230` | again it's one page in length and where |
| `00:21:50.240 - 00:21:53.909` | is that page well we've allocated a |
| `00:21:53.919 - 00:21:56.870` | page by calling k alec earlier and set |
| `00:21:56.880 - 00:21:58.950` | trap frame to point to the physical page |
| `00:21:58.960 - 00:22:01.270` | in physical memory and so |
| `00:22:01.280 - 00:22:03.750` | that's what we're using right here the |
| `00:22:03.760 - 00:22:06.149` | trap frame and we're making it readable |
| `00:22:06.159 - 00:22:07.669` | and writable |
| `00:22:07.679 - 00:22:11.270` | and if there is any problem well we |
| `00:22:11.280 - 00:22:13.430` | unmap the trampoline page |
| `00:22:13.440 - 00:22:15.990` | and then again free the page table and |
| `00:22:16.000 - 00:22:17.510` | return zero |
| `00:22:17.520 - 00:22:19.110` | okay |
| `00:22:19.120 - 00:22:22.789` | now what about uh the converse the free |
| `00:22:22.799 - 00:22:25.270` | proc free page table |
| `00:22:25.280 - 00:22:26.789` | well |
| `00:22:26.799 - 00:22:28.390` | we've discussed |
| `00:22:28.400 - 00:22:30.470` | these functions in the |
| `00:22:30.480 - 00:22:31.590` | videos |
| `00:22:31.600 - 00:22:34.310` | on the virtual memory functions |
| `00:22:34.320 - 00:22:36.390` | so we basically remove the mapping for |
| `00:22:36.400 - 00:22:38.310` | the trampoline page without freeing the |
| `00:22:38.320 - 00:22:41.350` | data page itself and we remove the |
| `00:22:41.360 - 00:22:43.909` | mapping for the trap frame page without |
| `00:22:43.919 - 00:22:46.470` | freeing the trap frame page itself |
| `00:22:46.480 - 00:22:50.950` | and then we um call uvm free to |
| `00:22:50.960 - 00:22:52.830` | completely obliterate |
| `00:22:52.840 - 00:22:55.990` | the uh page table to return all the data |
| `00:22:56.000 - 00:22:58.230` | pages to the free pool and to return all |
| `00:22:58.240 - 00:23:00.310` | the index pages to the page to the free |
| `00:23:00.320 - 00:23:01.110` | pool |
| `00:23:01.120 - 00:23:01.909` | so |
| `00:23:01.919 - 00:23:03.909` | where we call |
| `00:23:03.919 - 00:23:05.669` | um |
| `00:23:05.679 - 00:23:08.390` | where we call this |
| `00:23:08.400 - 00:23:11.350` | is in |
| `00:23:11.360 - 00:23:13.510` | free proc so here's our function free |
| `00:23:13.520 - 00:23:14.470` | proc |
| `00:23:14.480 - 00:23:15.750` | and |
| `00:23:15.760 - 00:23:17.750` | you see we we're already freeing the |
| `00:23:17.760 - 00:23:20.070` | trap frame page here so that's why we |
| `00:23:20.080 - 00:23:23.510` | don't uh ask for it to be freed at this |
| `00:23:23.520 - 00:23:25.430` | point right here |
| `00:23:25.440 - 00:23:27.990` | because we've already freed it |
| `00:23:28.000 - 00:23:31.270` | and we call a free page table here and |
| `00:23:31.280 - 00:23:33.830` | set page table to null so |
| `00:23:33.840 - 00:23:36.310` | this is where we use proc free page |
| `00:23:36.320 - 00:23:37.350` | table |
| `00:23:37.360 - 00:23:38.789` | okay now let's |
| `00:23:38.799 - 00:23:41.510` | talk about the |
| `00:23:41.520 - 00:23:43.430` | this function here |
| `00:23:43.440 - 00:23:48.950` | which is user init |
| `00:23:48.960 - 00:23:51.430` | is called to set up the first |
| `00:23:51.440 - 00:23:53.029` | user process |
| `00:23:53.039 - 00:23:55.029` | so let's walk through the code it's not |
| `00:23:55.039 - 00:23:56.630` | too long so |
| `00:23:56.640 - 00:23:59.430` | the first thing it does is it calls alec |
| `00:23:59.440 - 00:24:02.149` | proc to find a proc structure and |
| `00:24:02.159 - 00:24:04.230` | sort of set it up so now we've got a |
| `00:24:04.240 - 00:24:05.430` | pointer to |
| `00:24:05.440 - 00:24:07.350` | the proc structure |
| `00:24:07.360 - 00:24:08.310` | and |
| `00:24:08.320 - 00:24:10.549` | this is called the init proc so we're |
| `00:24:10.559 - 00:24:13.909` | going to save a pointer to that |
| `00:24:13.919 - 00:24:16.549` | next we're going to allocate a single |
| `00:24:16.559 - 00:24:20.390` | page and copy in the en code for the |
| `00:24:20.400 - 00:24:22.149` | initial |
| `00:24:22.159 - 00:24:24.310` | uh |
| `00:24:24.320 - 00:24:25.510` | process |
| `00:24:25.520 - 00:24:26.390` | so |
| `00:24:26.400 - 00:24:29.669` | uh here we are calling uvm init which |
| `00:24:29.679 - 00:24:31.430` | we've discussed |
| `00:24:31.440 - 00:24:33.590` | earlier but it's passed a pointer to the |
| `00:24:33.600 - 00:24:36.549` | page table okay so alec proc would have |
| `00:24:36.559 - 00:24:38.950` | set up this page table but not filled in |
| `00:24:38.960 - 00:24:40.789` | any code or data |
| `00:24:40.799 - 00:24:43.110` | and uvm init has passed a pointer to |
| `00:24:43.120 - 00:24:45.990` | some bytes and the number of bytes the |
| `00:24:46.000 - 00:24:46.950` | count |
| `00:24:46.960 - 00:24:47.990` | uh |
| `00:24:48.000 - 00:24:50.310` | and what is that well this is a knit |
| `00:24:50.320 - 00:24:53.590` | code right here okay this is this array |
| `00:24:53.600 - 00:24:56.789` | and it contains the bytes and uh i think |
| `00:24:56.799 - 00:25:00.630` | they counted out 52 of these things um |
| `00:25:00.640 - 00:25:03.350` | you can check me on that let's uh eight |
| `00:25:03.360 - 00:25:04.870` | times six forty eight four nine fifty |
| `00:25:04.880 - 00:25:07.669` | two one two fifty two page uh bytes |
| `00:25:07.679 - 00:25:10.230` | so here we are um |
| `00:25:10.240 - 00:25:12.789` | going to whoops going to copy in those |
| `00:25:12.799 - 00:25:15.750` | 52 bytes into page 0 |
| `00:25:15.760 - 00:25:18.630` | of the |
| `00:25:18.640 - 00:25:20.390` | virtual address space that is being |
| `00:25:20.400 - 00:25:22.470` | pointed to by this page table |
| `00:25:22.480 - 00:25:24.789` | we're also going to set up the size |
| `00:25:24.799 - 00:25:27.430` | field that's one of the fields in the |
| `00:25:27.440 - 00:25:29.510` | proc structure |
| `00:25:29.520 - 00:25:34.070` | right here this tells how big the |
| `00:25:34.080 - 00:25:35.510` | the |
| `00:25:35.520 - 00:25:37.350` | virtual address space is |
| `00:25:37.360 - 00:25:39.590` | from zero to |
| `00:25:39.600 - 00:25:42.149` | the break point and here we just have a |
| `00:25:42.159 - 00:25:45.029` | single page so that's not very much |
| `00:25:45.039 - 00:25:48.230` | okay prepare for the very first return |
| `00:25:48.240 - 00:25:50.710` | from the kernel to the user here's the |
| `00:25:50.720 - 00:25:52.470` | trap frame |
| `00:25:52.480 - 00:25:56.070` | the epc was where we saved |
| `00:25:56.080 - 00:25:56.950` | the |
| `00:25:56.960 - 00:25:59.110` | program counter |
| `00:25:59.120 - 00:26:01.430` | when a trap occurs in a running user |
| `00:26:01.440 - 00:26:03.350` | process we save the program counter and |
| `00:26:03.360 - 00:26:05.350` | we save it here we save all the |
| `00:26:05.360 - 00:26:07.750` | registers as well okay |
| `00:26:07.760 - 00:26:09.909` | we save all the general purpose |
| `00:26:09.919 - 00:26:12.470` | registers including the stack pointer |
| `00:26:12.480 - 00:26:13.269` | okay |
| `00:26:13.279 - 00:26:15.190` | and so when we get ready to return to |
| `00:26:15.200 - 00:26:16.549` | the |
| `00:26:16.559 - 00:26:19.669` | users process we return to executing at |
| `00:26:19.679 - 00:26:21.909` | this program counter here |
| `00:26:21.919 - 00:26:24.789` | after restoring all the registers so |
| `00:26:24.799 - 00:26:26.630` | what we're doing here |
| `00:26:26.640 - 00:26:28.070` | is we are |
| `00:26:28.080 - 00:26:30.630` | setting the program counter to zero so |
| `00:26:30.640 - 00:26:32.630` | for the for this process |
| `00:26:32.640 - 00:26:34.710` | and only for this process we'll start |
| `00:26:34.720 - 00:26:37.510` | executing it location zero for all other |
| `00:26:37.520 - 00:26:39.110` | processes when they first begin |
| `00:26:39.120 - 00:26:40.390` | executing |
| `00:26:40.400 - 00:26:41.590` | well they |
| `00:26:41.600 - 00:26:43.110` | were created as a result of before |
| `00:26:43.120 - 00:26:44.870` | construction and they will begin |
| `00:26:44.880 - 00:26:48.310` | executing directly after the the fork uh |
| `00:26:48.320 - 00:26:50.310` | called to the fork system call |
| `00:26:50.320 - 00:26:51.830` | and so |
| `00:26:51.840 - 00:26:53.029` | we don't need to do that for the other |
| `00:26:53.039 - 00:26:55.029` | processes but for the first process the |
| `00:26:55.039 - 00:26:57.830` | init process we set its program counter |
| `00:26:57.840 - 00:27:00.470` | to zero and we also set its stack |
| `00:27:00.480 - 00:27:03.269` | pointer to |
| `00:27:03.279 - 00:27:05.510` | the page size okay we're going to |
| `00:27:05.520 - 00:27:07.269` | remember that the initial process will |
| `00:27:07.279 - 00:27:09.750` | have exactly one page which will contain |
| `00:27:09.760 - 00:27:12.470` | both the code and the stack so by |
| `00:27:12.480 - 00:27:13.830` | setting sp |
| `00:27:13.840 - 00:27:16.789` | to the page size we are setting it to |
| `00:27:16.799 - 00:27:19.269` | the top of that initial page and so |
| `00:27:19.279 - 00:27:21.350` | hopefully the initial process |
| `00:27:21.360 - 00:27:25.190` | won't grow its stack very much in fact |
| `00:27:25.200 - 00:27:28.230` | it won't grow it at all but we do set it |
| `00:27:28.240 - 00:27:31.110` | up anyway and then finally we copy into |
| `00:27:31.120 - 00:27:33.269` | the name field |
| `00:27:33.279 - 00:27:35.669` | of the proc structure so here's the proc |
| `00:27:35.679 - 00:27:37.190` | structure again and we have this name |
| `00:27:37.200 - 00:27:38.549` | field down here |
| `00:27:38.559 - 00:27:40.230` | it's a fixed number of bytes a small |
| `00:27:40.240 - 00:27:42.149` | number of bytes |
| `00:27:42.159 - 00:27:43.510` | and |
| `00:27:43.520 - 00:27:47.909` | we are copying in these characters |
| `00:27:47.919 - 00:27:49.909` | save string copy is something that will |
| `00:27:49.919 - 00:27:52.389` | copy a string from one place to another |
| `00:27:52.399 - 00:27:53.750` | place |
| `00:27:53.760 - 00:27:57.669` | and it won't overrun the target area so |
| `00:27:57.679 - 00:28:00.389` | we're passing in the maximum number of |
| `00:28:00.399 - 00:28:02.630` | characters to copy with this size of |
| `00:28:02.640 - 00:28:05.510` | parameter here so that way we don't over |
| `00:28:05.520 - 00:28:08.870` | accidentally overrun this uh array here |
| `00:28:08.880 - 00:28:11.269` | and we've got a couple of other fields |
| `00:28:11.279 - 00:28:13.430` | uh the current working directory down |
| `00:28:13.440 - 00:28:14.549` | here |
| `00:28:14.559 - 00:28:18.149` | of the proc structure is initialized uh |
| `00:28:18.159 - 00:28:19.909` | to point to |
| `00:28:19.919 - 00:28:20.870` | um |
| `00:28:20.880 - 00:28:21.750` | this |
| `00:28:21.760 - 00:28:24.710` | this uh a slash so that's what that is |
| `00:28:24.720 - 00:28:27.430` | and then finally we set the state |
| `00:28:27.440 - 00:28:28.549` | whoops |
| `00:28:28.559 - 00:28:30.470` | set the state to runnable |
| `00:28:30.480 - 00:28:33.830` | and release the lock remember alec proc |
| `00:28:33.840 - 00:28:35.669` | returns with the lock set |
| `00:28:35.679 - 00:28:38.230` | and so now we've allocated a |
| `00:28:38.240 - 00:28:40.789` | a function sorry allocated a proc |
| `00:28:40.799 - 00:28:41.909` | structure |
| `00:28:41.919 - 00:28:43.750` | and |
| `00:28:43.760 - 00:28:45.990` | changed its uh initialized its page |
| `00:28:46.000 - 00:28:47.590` | table and everything got it all ready to |
| `00:28:47.600 - 00:28:51.110` | go we set it state to runnable and we're |
| `00:28:51.120 - 00:28:52.950` | done with it we can ignore it from here |
| `00:28:52.960 - 00:28:54.789` | on out uh this this thread that's |
| `00:28:54.799 - 00:28:56.950` | running this can ignore it and so we |
| `00:28:56.960 - 00:28:59.430` | just release the lock and the scheduler |
| `00:28:59.440 - 00:29:01.190` | will then find this process when it's |
| `00:29:01.200 - 00:29:03.029` | looking for something to run and it will |
| `00:29:03.039 - 00:29:04.789` | see that it's runnable and it will just |
| `00:29:04.799 - 00:29:07.669` | schedule it and the initial code will |
| `00:29:07.679 - 00:29:10.149` | then start executing and of course what |
| `00:29:10.159 - 00:29:13.510` | it does is not much |
| `00:29:13.520 - 00:29:15.750` | just looking up to this code here here's |
| `00:29:15.760 - 00:29:17.029` | the code |
| `00:29:17.039 - 00:29:18.870` | of course you can't read this because |
| `00:29:18.880 - 00:29:20.630` | it's the machine code for the risk 5 |
| `00:29:20.640 - 00:29:22.789` | processor but in any case what it does |
| `00:29:22.799 - 00:29:24.870` | is calls |
| `00:29:24.880 - 00:29:26.630` | performs a system call |
| `00:29:26.640 - 00:29:29.190` | for the exec system call passing it a |
| `00:29:29.200 - 00:29:31.669` | string of slash init and that's all it |
| `00:29:31.679 - 00:29:35.590` | does and um we are basically uh getting |
| `00:29:35.600 - 00:29:38.230` | this code this is the unix command octal |
| `00:29:38.240 - 00:29:39.430` | dump |
| `00:29:39.440 - 00:29:41.510` | basically we're |
| `00:29:41.520 - 00:29:43.590` | octal dumping it and putting it into a |
| `00:29:43.600 - 00:29:45.909` | file and then copying it into here |
| `00:29:45.919 - 00:29:48.710` | so there you have it and i'll cover the |
| `00:29:48.720 - 00:29:52.470` | rest of this uh file the proc.c file in |
| `00:29:52.480 - 00:29:55.840` | a future video |
