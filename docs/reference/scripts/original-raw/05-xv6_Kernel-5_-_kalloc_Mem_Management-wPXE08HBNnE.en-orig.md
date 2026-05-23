# xv6 Kernel-5: kalloc Mem Management

| Field | Value |
| --- | --- |
| Video ID | `wPXE08HBNnE` |
| URL | <https://www.youtube.com/watch?v=wPXE08HBNnE> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:00.880 - 00:00:02.470` | this video is part of a series on the |
| `00:00:02.480 - 00:00:05.349` | xv6 operating system kernel |
| `00:00:05.359 - 00:00:06.789` | in this video i'm going to look at |
| `00:00:06.799 - 00:00:10.549` | memory management and how the xv6 kernel |
| `00:00:10.559 - 00:00:11.669` | manages |
| `00:00:11.679 - 00:00:13.830` | free memory |
| `00:00:13.840 - 00:00:16.150` | it's pretty straightforward and simple |
| `00:00:16.160 - 00:00:20.390` | all memory management is in terms of 4k |
| `00:00:20.400 - 00:00:23.830` | blocks or we can also call them pages so |
| `00:00:23.840 - 00:00:25.910` | four kilobyte pages |
| `00:00:25.920 - 00:00:29.429` | and they're maintained on a free list |
| `00:00:29.439 - 00:00:32.870` | we have two important functions kealic |
| `00:00:32.880 - 00:00:34.870` | and kfree |
| `00:00:34.880 - 00:00:36.549` | which just |
| `00:00:36.559 - 00:00:39.190` | take things off of the free list in the |
| `00:00:39.200 - 00:00:42.950` | case of k alec or add a free block back |
| `00:00:42.960 - 00:00:45.190` | to the free list in the case of |
| `00:00:45.200 - 00:00:46.790` | k-free |
| `00:00:46.800 - 00:00:48.790` | and that's about all there is to it but |
| `00:00:48.800 - 00:00:50.229` | let's go into this in a little bit more |
| `00:00:50.239 - 00:00:51.430` | detail |
| `00:00:51.440 - 00:00:53.670` | uh first of all uh the |
| `00:00:53.680 - 00:00:56.310` | all this is coming from the file k alec |
| `00:00:56.320 - 00:00:59.830` | dot c and uh here is the file we've got |
| `00:00:59.840 - 00:01:01.830` | some includes |
| `00:01:01.840 - 00:01:05.429` | uh here is our pointer to the free list |
| `00:01:05.439 - 00:01:07.910` | and we've got this little structure here |
| `00:01:07.920 - 00:01:10.550` | called run which is just a pointer to |
| `00:01:10.560 - 00:01:13.590` | the next block so free list is a pointer |
| `00:01:13.600 - 00:01:15.429` | to |
| `00:01:15.439 - 00:01:17.830` | a linked list of |
| `00:01:17.840 - 00:01:20.550` | four kilobyte pages |
| `00:01:20.560 - 00:01:23.350` | this structure here groups the free list |
| `00:01:23.360 - 00:01:24.950` | with a spin lock |
| `00:01:24.960 - 00:01:27.510` | called simply lock |
| `00:01:27.520 - 00:01:29.910` | this is used to associate the lock with |
| `00:01:29.920 - 00:01:31.749` | the free list |
| `00:01:31.759 - 00:01:33.910` | whenever we manipulate the free list we |
| `00:01:33.920 - 00:01:35.670` | need to grab |
| `00:01:35.680 - 00:01:38.469` | the lock we need to acquire or set if |
| `00:01:38.479 - 00:01:40.550` | you will the lock and then when we're |
| `00:01:40.560 - 00:01:43.109` | done looking at the free list we need to |
| `00:01:43.119 - 00:01:45.429` | release that lock |
| `00:01:45.439 - 00:01:47.749` | we've also got a variable here this is |
| `00:01:47.759 - 00:01:50.069` | set by the linker |
| `00:01:50.079 - 00:01:51.190` | when |
| `00:01:51.200 - 00:01:53.670` | the program is being built and it's set |
| `00:01:53.680 - 00:01:56.310` | to point to the first address after the |
| `00:01:56.320 - 00:01:57.350` | kernel |
| `00:01:57.360 - 00:01:58.630` | the kernel's |
| `00:01:58.640 - 00:02:00.149` | executable code |
| `00:02:00.159 - 00:02:03.190` | its text section and its data section |
| `00:02:03.200 - 00:02:06.630` | and this is the first usable memory |
| `00:02:06.640 - 00:02:10.150` | and we'll use that in initialization |
| `00:02:10.160 - 00:02:12.869` | so |
| `00:02:12.879 - 00:02:14.070` | let's just |
| `00:02:14.080 - 00:02:17.110` | look at the initialization here |
| `00:02:17.120 - 00:02:17.910` | we |
| `00:02:17.920 - 00:02:19.430` | initialize the |
| `00:02:19.440 - 00:02:21.190` | lock and then we call |
| `00:02:21.200 - 00:02:24.390` | free range to free everything between |
| `00:02:24.400 - 00:02:25.510` | the |
| `00:02:25.520 - 00:02:28.949` | first available byte and the top of |
| `00:02:28.959 - 00:02:31.910` | physical memory which is a constant our |
| `00:02:31.920 - 00:02:34.869` | happens to be 128 megabytes |
| `00:02:34.879 - 00:02:37.990` | okay let's uh look at |
| `00:02:38.000 - 00:02:40.630` | the allocation routine |
| `00:02:40.640 - 00:02:43.110` | what it does is it acquires |
| `00:02:43.120 - 00:02:44.949` | the spin lock here |
| `00:02:44.959 - 00:02:46.470` | manipulates the free list and then |
| `00:02:46.480 - 00:02:48.869` | releases the lock and finally it returns |
| `00:02:48.879 - 00:02:53.670` | the pointer okay so in more detail it |
| `00:02:53.680 - 00:02:55.110` | grabs the |
| `00:02:55.120 - 00:02:58.229` | first thing off the free list |
| `00:02:58.239 - 00:03:01.270` | if it got something that is if memory is |
| `00:03:01.280 - 00:03:02.869` | not exhausted |
| `00:03:02.879 - 00:03:04.390` | then |
| `00:03:04.400 - 00:03:07.670` | it can keep going and what it'll do is |
| `00:03:07.680 - 00:03:09.830` | modify the free list to point to the |
| `00:03:09.840 - 00:03:10.949` | next item |
| `00:03:10.959 - 00:03:13.830` | so if the free list points to this item |
| `00:03:13.840 - 00:03:15.270` | we grab it |
| `00:03:15.280 - 00:03:16.550` | and then |
| `00:03:16.560 - 00:03:18.949` | redirect free list to point to the the |
| `00:03:18.959 - 00:03:20.790` | second what was the second item on the |
| `00:03:20.800 - 00:03:24.149` | list |
| `00:03:24.159 - 00:03:25.830` | finally we return |
| `00:03:25.840 - 00:03:27.830` | after releasing the spin lock we return |
| `00:03:27.840 - 00:03:28.949` | the pointer |
| `00:03:28.959 - 00:03:32.390` | before we return it if we got something |
| `00:03:32.400 - 00:03:35.750` | then we set every byte in the page to |
| `00:03:35.760 - 00:03:37.509` | this value of five |
| `00:03:37.519 - 00:03:41.990` | just filling it with junk |
| `00:03:42.000 - 00:03:43.830` | here is the |
| `00:03:43.840 - 00:03:47.350` | routine k free and it's passed a pointer |
| `00:03:47.360 - 00:03:48.789` | to a page |
| `00:03:48.799 - 00:03:51.430` | and what it does well it starts off with |
| `00:03:51.440 - 00:03:53.270` | a little bit of error checking make sure |
| `00:03:53.280 - 00:03:55.750` | that this pa physical address |
| `00:03:55.760 - 00:03:59.110` | is aligned on a page boundary |
| `00:03:59.120 - 00:04:01.670` | it makes sure that it's not |
| `00:04:01.680 - 00:04:05.030` | too small and not too large |
| `00:04:05.040 - 00:04:07.190` | okay so it's not below the starting |
| `00:04:07.200 - 00:04:09.190` | address and it's not |
| `00:04:09.200 - 00:04:10.309` | above |
| `00:04:10.319 - 00:04:12.470` | uh the ending address |
| `00:04:12.480 - 00:04:15.429` | and if that's okay then it fills the |
| `00:04:15.439 - 00:04:18.390` | page it starts by filling the page with |
| `00:04:18.400 - 00:04:21.670` | junk in this case junk happens to be one |
| `00:04:21.680 - 00:04:24.950` | and the purpose of that is to hopefully |
| `00:04:24.960 - 00:04:28.710` | bring out or or trigger any bugs in the |
| `00:04:28.720 - 00:04:30.790` | uh rest of the kernel code |
| `00:04:30.800 - 00:04:32.710` | we don't want to use a page after we |
| `00:04:32.720 - 00:04:35.110` | return it to the free list so by setting |
| `00:04:35.120 - 00:04:38.070` | every byte in it to some value we can |
| `00:04:38.080 - 00:04:40.870` | hopefully confuse any program code that |
| `00:04:40.880 - 00:04:48.150` | tries to use data from that page |
| `00:04:48.160 - 00:04:49.270` | then we |
| `00:04:49.280 - 00:04:51.909` | acquire the we're going to use r as our |
| `00:04:51.919 - 00:04:54.550` | pointer we acquire the |
| `00:04:54.560 - 00:04:56.070` | um |
| `00:04:56.080 - 00:04:57.270` | spin lock |
| `00:04:57.280 - 00:04:58.950` | we manipulate and we add it to the free |
| `00:04:58.960 - 00:05:01.749` | list and then we release our spin lock |
| `00:05:01.759 - 00:05:04.790` | so adding it to the free list if our is |
| `00:05:04.800 - 00:05:07.270` | if this is our free block here |
| `00:05:07.280 - 00:05:09.590` | then we simply set the next pointer of |
| `00:05:09.600 - 00:05:10.950` | it to point to |
| `00:05:10.960 - 00:05:12.390` | the first element |
| `00:05:12.400 - 00:05:14.390` | of the free list and then we redirect |
| `00:05:14.400 - 00:05:16.790` | the free list to point to this |
| `00:05:16.800 - 00:05:19.189` | now let's go back and look at the |
| `00:05:19.199 - 00:05:21.510` | initialization again |
| `00:05:21.520 - 00:05:24.070` | we are initializing all of memory from |
| `00:05:24.080 - 00:05:25.189` | this |
| `00:05:25.199 - 00:05:26.629` | starting point |
| `00:05:26.639 - 00:05:29.189` | to the physical top the top of physical |
| `00:05:29.199 - 00:05:32.230` | memory and we have a function here that |
| `00:05:32.240 - 00:05:34.390` | will do that |
| `00:05:34.400 - 00:05:37.270` | it starts by |
| `00:05:37.280 - 00:05:39.830` | it's past this starting address it |
| `00:05:39.840 - 00:05:41.590` | rounds it up to the nearest page |
| `00:05:41.600 - 00:05:44.230` | boundary because it will not necessarily |
| `00:05:44.240 - 00:05:46.070` | be page aligned |
| `00:05:46.080 - 00:05:49.510` | and then it does a loop and what it's |
| `00:05:49.520 - 00:05:53.350` | doing here is it's adding each page to |
| `00:05:53.360 - 00:05:55.110` | the free list |
| `00:05:55.120 - 00:05:57.189` | by calling k free |
| `00:05:57.199 - 00:06:00.469` | and it's just a loop here incrementing p |
| `00:06:00.479 - 00:06:01.909` | by |
| `00:06:01.919 - 00:06:04.870` | 4 096 bytes until |
| `00:06:04.880 - 00:06:06.469` | um |
| `00:06:06.479 - 00:06:07.430` | p |
| `00:06:07.440 - 00:06:11.639` | finally exceeds the end point |
