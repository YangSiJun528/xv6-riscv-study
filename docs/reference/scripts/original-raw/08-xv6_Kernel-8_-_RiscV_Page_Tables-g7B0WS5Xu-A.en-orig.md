# xv6 Kernel-8: RISC-V Page Tables

| Field | Value |
| --- | --- |
| Video ID | `g7B0WS5Xu-A` |
| URL | <https://www.youtube.com/watch?v=g7B0WS5Xu-A> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.520 - 00:00:03.189` | this video is part of a series on the |
| `00:00:03.199 - 00:00:06.230` | xv6 operating system kernel |
| `00:00:06.240 - 00:00:08.310` | in this video i'm going to talk about |
| `00:00:08.320 - 00:00:11.990` | address translation and describe the |
| `00:00:12.000 - 00:00:14.150` | page table architecture that's used in |
| `00:00:14.160 - 00:00:16.950` | the risc-5 processor at least as far as |
| `00:00:16.960 - 00:00:19.990` | we need to know to understand the xv6 |
| `00:00:20.000 - 00:00:21.750` | kernel |
| `00:00:21.760 - 00:00:23.590` | the register that we need to know about |
| `00:00:23.600 - 00:00:24.470` | here |
| `00:00:24.480 - 00:00:26.710` | is satp |
| `00:00:26.720 - 00:00:29.109` | the supervisor register for address |
| `00:00:29.119 - 00:00:31.269` | translation pointer |
| `00:00:31.279 - 00:00:34.630` | it is a control and status register and |
| `00:00:34.640 - 00:00:37.190` | it will be set to point to |
| `00:00:37.200 - 00:00:38.790` | the page table |
| `00:00:38.800 - 00:00:41.830` | page tables are kept in main memory and |
| `00:00:41.840 - 00:00:44.790` | at any one time there is one page table |
| `00:00:44.800 - 00:00:46.790` | that is in use and that is the one |
| `00:00:46.800 - 00:00:49.270` | that's pointed to by the satp |
| `00:00:49.280 - 00:00:51.110` | register |
| `00:00:51.120 - 00:00:52.790` | virtual address translation in the |
| `00:00:52.800 - 00:00:56.470` | risc-5 core is always turned on |
| `00:00:56.480 - 00:00:59.750` | that is after initialization is complete |
| `00:00:59.760 - 00:01:00.790` | so |
| `00:01:00.800 - 00:01:04.229` | initially satp is zero and when it's |
| `00:01:04.239 - 00:01:06.550` | zero then there is no translation |
| `00:01:06.560 - 00:01:07.750` | occurring |
| `00:01:07.760 - 00:01:09.030` | but |
| `00:01:09.040 - 00:01:11.429` | in the initialization phase we will set |
| `00:01:11.439 - 00:01:14.149` | satp to point to the page table that we |
| `00:01:14.159 - 00:01:16.230` | want to use |
| `00:01:16.240 - 00:01:18.070` | it's always turned on when we're running |
| `00:01:18.080 - 00:01:21.030` | in supervisor and user mode it's not |
| `00:01:21.040 - 00:01:22.710` | turned on in |
| `00:01:22.720 - 00:01:24.950` | machine mode |
| `00:01:24.960 - 00:01:27.749` | there are several page tables there is |
| `00:01:27.759 - 00:01:30.069` | one page table for the kernel |
| `00:01:30.079 - 00:01:33.590` | all cores will share that page table |
| `00:01:33.600 - 00:01:36.469` | and it provides a one-to-one mapping uh |
| `00:01:36.479 - 00:01:38.069` | pretty much one-to-one there are a |
| `00:01:38.079 - 00:01:40.390` | couple of exceptions |
| `00:01:40.400 - 00:01:43.350` | but it will map all of physical memory |
| `00:01:43.360 - 00:01:45.429` | so that the kernel code can access any |
| `00:01:45.439 - 00:01:47.749` | location in memory without having to do |
| `00:01:47.759 - 00:01:49.350` | any sort of |
| `00:01:49.360 - 00:01:52.310` | computation about the address |
| `00:01:52.320 - 00:01:54.069` | there are also a number of memory mapped |
| `00:01:54.079 - 00:01:56.950` | i o devices and the kernels page table |
| `00:01:56.960 - 00:01:59.749` | will map those directly |
| `00:01:59.759 - 00:02:01.910` | in addition to this one page table there |
| `00:02:01.920 - 00:02:04.230` | is also a single page table for every |
| `00:02:04.240 - 00:02:07.350` | user mode process |
| `00:02:07.360 - 00:02:09.350` | now with the risk five there are a |
| `00:02:09.360 - 00:02:11.350` | number of options for |
| `00:02:11.360 - 00:02:14.949` | how page tables are implemented uh these |
| `00:02:14.959 - 00:02:16.229` | options are called |
| `00:02:16.239 - 00:02:20.630` | sv32 sv39 sv48 |
| `00:02:20.640 - 00:02:22.630` | one of them is a two-level |
| `00:02:22.640 - 00:02:24.390` | implementation scheme |
| `00:02:24.400 - 00:02:26.470` | uh another is the three-level |
| `00:02:26.480 - 00:02:28.150` | architecture and |
| `00:02:28.160 - 00:02:32.790` | the sv-48 is the four-level architecture |
| `00:02:32.800 - 00:02:36.309` | sv 32 is available for 32-bit processors |
| `00:02:36.319 - 00:02:38.229` | we are only concerned with a 64-bit |
| `00:02:38.239 - 00:02:41.990` | processor and xb6 will use the sv-39 |
| `00:02:42.000 - 00:02:43.830` | scheme so the processors processor |
| `00:02:43.840 - 00:02:45.270` | implements the sv |
| `00:02:45.280 - 00:02:46.470` | 39 |
| `00:02:46.480 - 00:02:49.190` | scheme so we have three level page |
| `00:02:49.200 - 00:02:51.430` | tables there's some other options that i |
| `00:02:51.440 - 00:02:52.949` | won't go into |
| `00:02:52.959 - 00:02:53.990` | every |
| `00:02:54.000 - 00:02:55.030` | load |
| `00:02:55.040 - 00:02:56.150` | store |
| `00:02:56.160 - 00:02:57.430` | or fetch |
| `00:02:57.440 - 00:02:59.270` | to or from main memory |
| `00:02:59.280 - 00:03:00.790` | will |
| `00:03:00.800 - 00:03:02.790` | walk the page table table at least |
| `00:03:02.800 - 00:03:04.309` | conceptually so we'll go through the |
| `00:03:04.319 - 00:03:06.390` | page table to find |
| `00:03:06.400 - 00:03:09.270` | the address translation to use that's |
| `00:03:09.280 - 00:03:11.110` | conceptual |
| `00:03:11.120 - 00:03:12.630` | walking the page table would involve |
| `00:03:12.640 - 00:03:13.509` | several |
| `00:03:13.519 - 00:03:15.910` | memory accesses and that's going to lead |
| `00:03:15.920 - 00:03:18.070` | to incredible inefficiency so for |
| `00:03:18.080 - 00:03:19.830` | performance reasons |
| `00:03:19.840 - 00:03:21.990` | a real processor is going to have |
| `00:03:22.000 - 00:03:23.830` | something called translation look aside |
| `00:03:23.840 - 00:03:25.430` | buffers |
| `00:03:25.440 - 00:03:28.470` | these are registers that are in the core |
| `00:03:28.480 - 00:03:29.430` | and |
| `00:03:29.440 - 00:03:31.589` | generally they are transparent or i |
| `00:03:31.599 - 00:03:35.110` | should say invisible to the programmers |
| `00:03:35.120 - 00:03:36.789` | and basically what these registers will |
| `00:03:36.799 - 00:03:41.110` | do is is act as caches for recent page |
| `00:03:41.120 - 00:03:43.750` | table entries |
| `00:03:43.760 - 00:03:46.229` | the only thing we need to know about as |
| `00:03:46.239 - 00:03:48.710` | kernel programmers is that whenever we |
| `00:03:48.720 - 00:03:51.190` | change the page table that is whenever |
| `00:03:51.200 - 00:03:54.949` | we update the satp register we need to |
| `00:03:54.959 - 00:03:56.470` | somehow flush |
| `00:03:56.480 - 00:03:59.270` | the cache we need to empty out all the |
| `00:03:59.280 - 00:04:01.190` | tlbs |
| `00:04:01.200 - 00:04:03.190` | for this there is an instruction in the |
| `00:04:03.200 - 00:04:06.710` | risk 5 instruction set called s fence |
| `00:04:06.720 - 00:04:08.630` | vma |
| `00:04:08.640 - 00:04:11.110` | in the kernel there is a function with |
| `00:04:11.120 - 00:04:14.149` | the name s fence vma that does really |
| `00:04:14.159 - 00:04:16.069` | nothing more than just execute this |
| `00:04:16.079 - 00:04:17.909` | instruction |
| `00:04:17.919 - 00:04:20.150` | okay let's look at virtual addresses for |
| `00:04:20.160 - 00:04:22.790` | the 30 the sv 39 |
| `00:04:22.800 - 00:04:24.710` | scheme |
| `00:04:24.720 - 00:04:28.710` | a virtual address is 39 bits okay and |
| `00:04:28.720 - 00:04:30.950` | that address can be divided into an |
| `00:04:30.960 - 00:04:32.150` | offset |
| `00:04:32.160 - 00:04:35.590` | all pages will be 4 kilobytes in length |
| `00:04:35.600 - 00:04:38.710` | and since 2 to the 12th is 4k |
| `00:04:38.720 - 00:04:41.189` | we need exactly 12 bits to offset into |
| `00:04:41.199 - 00:04:42.550` | the page |
| `00:04:42.560 - 00:04:45.830` | the remaining 27 bits are divided into |
| `00:04:45.840 - 00:04:46.950` | three |
| `00:04:46.960 - 00:04:48.070` | fields |
| `00:04:48.080 - 00:04:50.310` | which will access the level 1 level |
| `00:04:50.320 - 00:04:52.870` | sorry level 2 level 1 and level 0 of the |
| `00:04:52.880 - 00:04:54.070` | page table |
| `00:04:54.080 - 00:04:57.590` | the bits above this are ignored |
| `00:04:57.600 - 00:05:00.070` | and when we access the page table we |
| `00:05:00.080 - 00:05:02.710` | will get a page table entry |
| `00:05:02.720 - 00:05:06.790` | and that page table entry will contain a |
| `00:05:06.800 - 00:05:09.670` | number of bits that will control whether |
| `00:05:09.680 - 00:05:11.909` | the access should be allowed or not it |
| `00:05:11.919 - 00:05:13.670` | can be marked |
| `00:05:13.680 - 00:05:14.950` | read |
| `00:05:14.960 - 00:05:17.189` | in which case reading is okay |
| `00:05:17.199 - 00:05:18.390` | writable |
| `00:05:18.400 - 00:05:20.870` | if we're writing to the page we need to |
| `00:05:20.880 - 00:05:22.390` | have this bit set |
| `00:05:22.400 - 00:05:24.950` | and x stands for executable there's also |
| `00:05:24.960 - 00:05:28.150` | a u-bit to indicate whether the page is |
| `00:05:28.160 - 00:05:30.550` | accessible in user mode or only in |
| `00:05:30.560 - 00:05:32.230` | supervisor mode |
| `00:05:32.240 - 00:05:34.070` | and there's a final bit |
| `00:05:34.080 - 00:05:36.150` | v which stands for valid to indicate |
| `00:05:36.160 - 00:05:38.070` | whether this page table entry is valid |
| `00:05:38.080 - 00:05:39.270` | or not |
| `00:05:39.280 - 00:05:40.790` | there's some other bits here but we |
| `00:05:40.800 - 00:05:43.029` | don't care about those and finally there |
| `00:05:43.039 - 00:05:45.510` | is the physical page number |
| `00:05:45.520 - 00:05:48.390` | and the address translation hardware |
| `00:05:48.400 - 00:05:49.350` | will |
| `00:05:49.360 - 00:05:51.110` | take the virtual address |
| `00:05:51.120 - 00:05:53.909` | use these indexes to look up the right |
| `00:05:53.919 - 00:05:57.270` | entry and retrieve the page table entry |
| `00:05:57.280 - 00:05:59.270` | and then it will take the 44 bits of the |
| `00:05:59.280 - 00:06:01.670` | page number and the 12 bits of the |
| `00:06:01.680 - 00:06:02.710` | offset |
| `00:06:02.720 - 00:06:05.270` | and put them together |
| `00:06:05.280 - 00:06:07.029` | to form the physical address which will |
| `00:06:07.039 - 00:06:09.990` | be 56 bits so with this scheme we can |
| `00:06:10.000 - 00:06:13.189` | support up to two to the 56 |
| `00:06:13.199 - 00:06:16.150` | bytes of main physical memory |
| `00:06:16.160 - 00:06:18.870` | okay let's look at the page table itself |
| `00:06:18.880 - 00:06:21.670` | remember we have these three fields and |
| `00:06:21.680 - 00:06:23.510` | each is nine bits |
| `00:06:23.520 - 00:06:24.469` | so |
| `00:06:24.479 - 00:06:26.309` | the page table looks like this |
| `00:06:26.319 - 00:06:29.430` | here's our satp register it points to a |
| `00:06:29.440 - 00:06:30.550` | tree |
| `00:06:30.560 - 00:06:33.189` | each node in the tree is a four kilobyte |
| `00:06:33.199 - 00:06:37.029` | page in main memory and the tree has |
| `00:06:37.039 - 00:06:39.510` | three levels |
| `00:06:39.520 - 00:06:41.749` | and the leaf level at the leaf we have |
| `00:06:41.759 - 00:06:43.670` | the actual data pages |
| `00:06:43.680 - 00:06:45.189` | so all of these |
| `00:06:45.199 - 00:06:48.390` | nodes and the leaves are four k byte |
| `00:06:48.400 - 00:06:49.830` | pages |
| `00:06:49.840 - 00:06:52.390` | and each one of the |
| `00:06:52.400 - 00:06:54.230` | interior nodes of the tree that is each |
| `00:06:54.240 - 00:06:55.749` | one of the three levels in the page |
| `00:06:55.759 - 00:06:57.270` | table |
| `00:06:57.280 - 00:06:58.430` | will contain |
| `00:06:58.440 - 00:07:03.510` | 512 entries each entry is 64 bits and of |
| `00:07:03.520 - 00:07:06.629` | course uh 64 bits uh |
| `00:07:06.639 - 00:07:08.070` | is eight |
| `00:07:08.080 - 00:07:09.830` | bytes and eight times five hundred and |
| `00:07:09.840 - 00:07:12.550` | twelve is four thousand ninety six |
| `00:07:12.560 - 00:07:16.469` | so we have room for 512 entries in each |
| `00:07:16.479 - 00:07:18.469` | node and each |
| `00:07:18.479 - 00:07:20.390` | entry is a page table entry which will |
| `00:07:20.400 - 00:07:24.309` | have some bits and a pointer to the next |
| `00:07:24.319 - 00:07:25.990` | level node |
| `00:07:26.000 - 00:07:27.510` | and so |
| `00:07:27.520 - 00:07:30.950` | the way it works is |
| `00:07:30.960 - 00:07:33.270` | the hardware we'll look at the first |
| `00:07:33.280 - 00:07:35.270` | nine bit field here |
| `00:07:35.280 - 00:07:38.150` | and use that as an ax as an index into |
| `00:07:38.160 - 00:07:38.870` | this |
| `00:07:38.880 - 00:07:40.390` | root node |
| `00:07:40.400 - 00:07:42.150` | recall that uh |
| `00:07:42.160 - 00:07:44.790` | two to the ninth is 512 so nine bits is |
| `00:07:44.800 - 00:07:46.629` | exactly what we need |
| `00:07:46.639 - 00:07:48.230` | of course 12 bits |
| `00:07:48.240 - 00:07:50.790` | uh will allow us any |
| `00:07:50.800 - 00:07:53.270` | access to access to any byte in the data |
| `00:07:53.280 - 00:07:56.629` | page since 2 to the 12th is 4096. |
| `00:07:56.639 - 00:08:00.150` | so we use the first index here the level |
| `00:08:00.160 - 00:08:01.270` | two index |
| `00:08:01.280 - 00:08:02.950` | the index here |
| `00:08:02.960 - 00:08:05.029` | we get a pointer |
| `00:08:05.039 - 00:08:07.990` | we need we use the next index |
| `00:08:08.000 - 00:08:10.309` | to index into that |
| `00:08:10.319 - 00:08:12.309` | we use the and that gives us a pointer |
| `00:08:12.319 - 00:08:15.110` | to the next page we use that |
| `00:08:15.120 - 00:08:17.430` | index to access into |
| `00:08:17.440 - 00:08:18.390` | this |
| `00:08:18.400 - 00:08:20.550` | page and that gives us the final page |
| `00:08:20.560 - 00:08:23.510` | table entry which is shown here and |
| `00:08:23.520 - 00:08:26.150` | which will be used to check the access |
| `00:08:26.160 - 00:08:27.670` | of the data page |
| `00:08:27.680 - 00:08:31.270` | and to build the physical address |
| `00:08:31.280 - 00:08:33.350` | okay that's it for the page table scheme |
| `00:08:33.360 - 00:08:37.000` | see you in the next video |
