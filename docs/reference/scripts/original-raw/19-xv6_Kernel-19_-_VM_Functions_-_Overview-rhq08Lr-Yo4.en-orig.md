# xv6 Kernel-19: VM Functions: Overview

| Field | Value |
| --- | --- |
| Video ID | `rhq08Lr-Yo4` |
| URL | <https://www.youtube.com/watch?v=rhq08Lr-Yo4> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.120 - 00:00:02.550` | this video is part of a series on the |
| `00:00:02.560 - 00:00:05.110` | xv6 operating system kernel |
| `00:00:05.120 - 00:00:06.869` | in this video i'm going to go over a |
| `00:00:06.879 - 00:00:08.710` | number of helper functions for virtual |
| `00:00:08.720 - 00:00:10.150` | memory management |
| `00:00:10.160 - 00:00:12.709` | these come from the file vm.c and |
| `00:00:12.719 - 00:00:14.549` | they're used to manipulate the page |
| `00:00:14.559 - 00:00:16.870` | table structures they're fairly |
| `00:00:16.880 - 00:00:18.390` | straightforward and can be understood in |
| `00:00:18.400 - 00:00:20.470` | isolation for example they don't do any |
| `00:00:20.480 - 00:00:22.710` | locking or sleeping |
| `00:00:22.720 - 00:00:24.230` | however there |
| `00:00:24.240 - 00:00:26.550` | there's quite a bit of material and i'm |
| `00:00:26.560 - 00:00:28.630` | going to break it into two videos |
| `00:00:28.640 - 00:00:30.710` | in this video i'll do a general overview |
| `00:00:30.720 - 00:00:33.110` | of the functions and in the next video |
| `00:00:33.120 - 00:00:36.549` | i'll do a walkthrough of the actual code |
| `00:00:36.559 - 00:00:38.310` | so let's begin with remembering what |
| `00:00:38.320 - 00:00:39.990` | page tables look like |
| `00:00:40.000 - 00:00:42.790` | a page table is represented as a tree |
| `00:00:42.800 - 00:00:46.549` | and in the case of xv6 they are |
| `00:00:46.559 - 00:00:48.229` | three level trees |
| `00:00:48.239 - 00:00:52.470` | and we can refer to the root node as the |
| `00:00:52.480 - 00:00:53.910` | root page |
| `00:00:53.920 - 00:00:57.110` | and all the pages that comprise the page |
| `00:00:57.120 - 00:00:59.990` | table we can call index pages |
| `00:01:00.000 - 00:01:02.150` | at the bottom level the index pages |
| `00:01:02.160 - 00:01:05.350` | point to data pages so |
| `00:01:05.360 - 00:01:07.270` | so let's look in more detail at what |
| `00:01:07.280 - 00:01:10.230` | these look like and here we see a |
| `00:01:10.240 - 00:01:13.670` | picture of the page table one branch in |
| `00:01:13.680 - 00:01:14.789` | the tree |
| `00:01:14.799 - 00:01:16.070` | and |
| `00:01:16.080 - 00:01:17.749` | here's the root node |
| `00:01:17.759 - 00:01:19.429` | and we have a number of page table |
| `00:01:19.439 - 00:01:21.429` | entries each pointing to the next level |
| `00:01:21.439 - 00:01:23.030` | and at the lowest level we have the data |
| `00:01:23.040 - 00:01:24.630` | pages |
| `00:01:24.640 - 00:01:27.109` | so remember that a virtual address looks |
| `00:01:27.119 - 00:01:28.390` | like this |
| `00:01:28.400 - 00:01:30.550` | we can use the field in the upper part |
| `00:01:30.560 - 00:01:33.429` | of the address to index into the pages |
| `00:01:33.439 - 00:01:34.230` | one |
| `00:01:34.240 - 00:01:35.109` | two |
| `00:01:35.119 - 00:01:36.149` | and three |
| `00:01:36.159 - 00:01:38.630` | and the offset is used to index into the |
| `00:01:38.640 - 00:01:40.469` | data page itself |
| `00:01:40.479 - 00:01:43.030` | the page table entries |
| `00:01:43.040 - 00:01:44.550` | have this format |
| `00:01:44.560 - 00:01:46.950` | namely they contain a pointer that's a |
| `00:01:46.960 - 00:01:49.590` | page align and this is used to go down |
| `00:01:49.600 - 00:01:51.590` | one level in the tree |
| `00:01:51.600 - 00:01:55.030` | they also have a number of bits the read |
| `00:01:55.040 - 00:01:57.510` | write and execute bits as well as a |
| `00:01:57.520 - 00:02:00.069` | valid bit and a bit to indicate whether |
| `00:02:00.079 - 00:02:04.149` | the page is accessible in user mode |
| `00:02:04.159 - 00:02:08.309` | these bits are valid in the last page |
| `00:02:08.319 - 00:02:10.469` | table entry the one at the lowest level |
| `00:02:10.479 - 00:02:11.830` | here |
| `00:02:11.840 - 00:02:14.229` | in the upper index pages at these two |
| `00:02:14.239 - 00:02:16.790` | levels only the valid bit matters and |
| `00:02:16.800 - 00:02:20.150` | the other bits are zero |
| `00:02:20.160 - 00:02:22.309` | okay now let's go through the various |
| `00:02:22.319 - 00:02:24.309` | functions and we'll start with the walk |
| `00:02:24.319 - 00:02:25.830` | function |
| `00:02:25.840 - 00:02:27.990` | walk is past a page table or more |
| `00:02:28.000 - 00:02:31.830` | precisely a pointer to the root node of |
| `00:02:31.840 - 00:02:33.190` | a page table |
| `00:02:33.200 - 00:02:34.949` | and a virtual address |
| `00:02:34.959 - 00:02:36.949` | and it will walk the tree and it will |
| `00:02:36.959 - 00:02:39.110` | return the address of the page table |
| `00:02:39.120 - 00:02:41.270` | entry that points to the data page |
| `00:02:41.280 - 00:02:43.110` | in other words it's going to be passed a |
| `00:02:43.120 - 00:02:45.270` | pointer to the root and it's going to |
| `00:02:45.280 - 00:02:47.670` | walk down the tree and return a pointer |
| `00:02:47.680 - 00:02:50.390` | to the page table entry that |
| `00:02:50.400 - 00:02:53.750` | points to the data page |
| `00:02:53.760 - 00:02:56.309` | if there are missing index pages in the |
| `00:02:56.319 - 00:02:58.309` | page table then we have the option to |
| `00:02:58.319 - 00:03:01.750` | create them this third parameter alec |
| `00:03:01.760 - 00:03:02.790` | is |
| `00:03:02.800 - 00:03:04.710` | indicating whether we should create |
| `00:03:04.720 - 00:03:07.430` | index pages if they are needed |
| `00:03:07.440 - 00:03:09.830` | if there's any problem for example we |
| `00:03:09.840 - 00:03:11.910` | can't allocate a page |
| `00:03:11.920 - 00:03:13.750` | this function will return the null |
| `00:03:13.760 - 00:03:15.350` | pointer |
| `00:03:15.360 - 00:03:17.350` | the next function to look at is map |
| `00:03:17.360 - 00:03:20.470` | pages and it's used to add page table |
| `00:03:20.480 - 00:03:22.790` | entries to a page table |
| `00:03:22.800 - 00:03:24.949` | so it's past a page table |
| `00:03:24.959 - 00:03:26.070` | and |
| `00:03:26.080 - 00:03:28.550` | a virtual address and that tells where |
| `00:03:28.560 - 00:03:30.390` | the pages are going to be added in the |
| `00:03:30.400 - 00:03:32.550` | virtual address space |
| `00:03:32.560 - 00:03:35.350` | it has also passed a physical address |
| `00:03:35.360 - 00:03:38.149` | which points to a number of pages so |
| `00:03:38.159 - 00:03:40.309` | size gives the number of pages that are |
| `00:03:40.319 - 00:03:41.670` | being pointed to |
| `00:03:41.680 - 00:03:43.990` | and this function will add |
| `00:03:44.000 - 00:03:46.550` | a mapping or multiple mappings in this |
| `00:03:46.560 - 00:03:49.509` | case three mappings to the page table |
| `00:03:49.519 - 00:03:51.110` | so that these virtual pages are mapped |
| `00:03:51.120 - 00:03:53.350` | to these physical pages |
| `00:03:53.360 - 00:03:55.589` | the mappings will be given the |
| `00:03:55.599 - 00:03:58.149` | permissions that are given by the last |
| `00:03:58.159 - 00:04:00.550` | argument read write executable |
| `00:04:00.560 - 00:04:02.309` | accessible in user mode |
| `00:04:02.319 - 00:04:04.070` | if there's a problem this function will |
| `00:04:04.080 - 00:04:07.030` | return minus one otherwise zero |
| `00:04:07.040 - 00:04:09.190` | it's often times the case with the |
| `00:04:09.200 - 00:04:11.750` | xv6 kernel functions that if there's a |
| `00:04:11.760 - 00:04:14.789` | problem the function will return -1 and |
| `00:04:14.799 - 00:04:21.349` | if everything's okay it will return 0. |
| `00:04:21.359 - 00:04:24.150` | for the kernel there is exactly one |
| `00:04:24.160 - 00:04:25.430` | page table |
| `00:04:25.440 - 00:04:28.710` | and all cores will share that page table |
| `00:04:28.720 - 00:04:31.749` | and when we create that page table we |
| `00:04:31.759 - 00:04:34.950` | use this function kvm map kernel virtual |
| `00:04:34.960 - 00:04:37.189` | memory map and it's basically the same |
| `00:04:37.199 - 00:04:39.670` | as map pages except that we do not |
| `00:04:39.680 - 00:04:41.510` | expect errors when we're creating the |
| `00:04:41.520 - 00:04:43.990` | kernels page table so this function just |
| `00:04:44.000 - 00:04:45.189` | adds a call |
| `00:04:45.199 - 00:04:47.830` | to panic in case map pages returns a |
| `00:04:47.840 - 00:04:49.909` | minus one error code |
| `00:04:49.919 - 00:04:51.510` | and as i said it's used to build the |
| `00:04:51.520 - 00:04:53.830` | kernels page table |
| `00:04:53.840 - 00:04:56.710` | so the function kb make |
| `00:04:56.720 - 00:04:57.590` | is |
| `00:04:57.600 - 00:05:00.629` | the one that calls kb map to create and |
| `00:05:00.639 - 00:05:03.749` | initialize the kernel's page table |
| `00:05:03.759 - 00:05:05.909` | it adds all of physical memory to the |
| `00:05:05.919 - 00:05:08.550` | page table with a direct mapping |
| `00:05:08.560 - 00:05:10.629` | and it adds all the memory mapped io |
| `00:05:10.639 - 00:05:12.550` | devices to the page table again with a |
| `00:05:12.560 - 00:05:14.230` | direct mapping |
| `00:05:14.240 - 00:05:17.029` | and it creates a mapping for |
| `00:05:17.039 - 00:05:19.830` | the trampoline page the trampoline page |
| `00:05:19.840 - 00:05:22.150` | exists somewhere in the physical memory |
| `00:05:22.160 - 00:05:24.469` | but in addition there's a second mapping |
| `00:05:24.479 - 00:05:26.150` | for that from the highest page in the |
| `00:05:26.160 - 00:05:28.629` | virtual address space to that physical |
| `00:05:28.639 - 00:05:29.990` | page |
| `00:05:30.000 - 00:05:33.590` | and finally it calls prop max that map |
| `00:05:33.600 - 00:05:35.350` | stacks |
| `00:05:35.360 - 00:05:38.310` | uh here's a picture of the virtual |
| `00:05:38.320 - 00:05:40.150` | address space for the kernel |
| `00:05:40.160 - 00:05:42.629` | and up at the top we see the trampoline |
| `00:05:42.639 - 00:05:45.029` | page and we also see |
| `00:05:45.039 - 00:05:47.749` | one page for each process |
| `00:05:47.759 - 00:05:48.870` | and these |
| `00:05:48.880 - 00:05:51.189` | pages are mapped into some virtual sorry |
| `00:05:51.199 - 00:05:53.270` | some physical pages that are obtained |
| `00:05:53.280 - 00:05:55.749` | from k alec |
| `00:05:55.759 - 00:05:58.629` | each process will need a page for its |
| `00:05:58.639 - 00:05:59.749` | stack |
| `00:05:59.759 - 00:06:01.590` | to be used when that process is |
| `00:06:01.600 - 00:06:03.350` | executing in kernel mode |
| `00:06:03.360 - 00:06:07.990` | and so we allocate 64 pages and we add |
| `00:06:08.000 - 00:06:10.309` | those mappings to the |
| `00:06:10.319 - 00:06:12.469` | page table for the kernel |
| `00:06:12.479 - 00:06:14.150` | recall that we've got this |
| `00:06:14.160 - 00:06:16.230` | preprocessor macro function called k |
| `00:06:16.240 - 00:06:19.189` | stack which is passed a number between 0 |
| `00:06:19.199 - 00:06:22.390` | and 63 and returns a pointer to returns |
| `00:06:22.400 - 00:06:25.110` | the address of the corresponding kernel |
| `00:06:25.120 - 00:06:29.350` | page table page |
| `00:06:29.360 - 00:06:31.749` | the function kb annette |
| `00:06:31.759 - 00:06:33.270` | will call kb |
| `00:06:33.280 - 00:06:35.590` | kvm make to create the page table for |
| `00:06:35.600 - 00:06:37.909` | the kernel and it will assign it to some |
| `00:06:37.919 - 00:06:42.150` | global variable kernel page table |
| `00:06:42.160 - 00:06:43.510` | this function is called during |
| `00:06:43.520 - 00:06:45.990` | initialization by core 0 to create the |
| `00:06:46.000 - 00:06:48.230` | page table for the kernel and then |
| `00:06:48.240 - 00:06:50.710` | subsequently each and every core we'll |
| `00:06:50.720 - 00:06:53.830` | call init heart which will use that page |
| `00:06:53.840 - 00:06:56.950` | table and write the page table address |
| `00:06:56.960 - 00:06:59.270` | into the satp register |
| `00:06:59.280 - 00:07:01.990` | this is the control and status register |
| `00:07:02.000 - 00:07:05.749` | and when the satp register is set to |
| `00:07:05.759 - 00:07:07.670` | point to a page table that effectively |
| `00:07:07.680 - 00:07:11.589` | turns on paging for that core |
| `00:07:11.599 - 00:07:13.110` | next we have the |
| `00:07:13.120 - 00:07:14.950` | walk address |
| `00:07:14.960 - 00:07:17.189` | function which is used to take a virtual |
| `00:07:17.199 - 00:07:19.589` | app virtual address and convert it into |
| `00:07:19.599 - 00:07:21.270` | a physical address |
| `00:07:21.280 - 00:07:23.350` | so this will use the page table |
| `00:07:23.360 - 00:07:24.550` | and |
| `00:07:24.560 - 00:07:27.350` | walk it to find which page that virtual |
| `00:07:27.360 - 00:07:29.350` | address is mapped into and then it will |
| `00:07:29.360 - 00:07:31.189` | translate the virtual address into a |
| `00:07:31.199 - 00:07:33.270` | physical address |
| `00:07:33.280 - 00:07:35.029` | the page on which this virtual address |
| `00:07:35.039 - 00:07:36.150` | occurs |
| `00:07:36.160 - 00:07:39.270` | must be marked valid and it must have |
| `00:07:39.280 - 00:07:41.830` | you permission set and if there's any |
| `00:07:41.840 - 00:07:44.710` | problem it this function will return |
| `00:07:44.720 - 00:07:47.589` | no or zero |
| `00:07:47.599 - 00:07:49.189` | since uh the |
| `00:07:49.199 - 00:07:51.589` | no pages in virtual any virtual address |
| `00:07:51.599 - 00:07:54.390` | space are mapped to the very first |
| `00:07:54.400 - 00:07:57.189` | page in the physical memory |
| `00:07:57.199 - 00:07:59.350` | no virtual address can have a physical |
| `00:07:59.360 - 00:08:02.309` | address of zero so it's safe to use zero |
| `00:08:02.319 - 00:08:07.110` | as the result of this call |
| `00:08:07.120 - 00:08:09.430` | next let's look at the uvm create |
| `00:08:09.440 - 00:08:11.270` | function this is |
| `00:08:11.280 - 00:08:12.629` | a function that will create an empty |
| `00:08:12.639 - 00:08:14.150` | virtual address space |
| `00:08:14.160 - 00:08:15.589` | essentially it's just going to allocate |
| `00:08:15.599 - 00:08:16.469` | one |
| `00:08:16.479 - 00:08:19.110` | physical page and clear it out to zero |
| `00:08:19.120 - 00:08:22.070` | and return that as the root |
| `00:08:22.080 - 00:08:24.869` | index page for the page table |
| `00:08:24.879 - 00:08:27.909` | uvm is user virtual memory and we've |
| `00:08:27.919 - 00:08:29.270` | also got |
| `00:08:29.280 - 00:08:31.670` | user virtual memory init |
| `00:08:31.680 - 00:08:34.550` | which is used to create the first user |
| `00:08:34.560 - 00:08:36.870` | virtual address space this is a little |
| `00:08:36.880 - 00:08:38.949` | bit special |
| `00:08:38.959 - 00:08:41.589` | um it's going to allocate a single page |
| `00:08:41.599 - 00:08:43.829` | map it into virtual address zero and |
| `00:08:43.839 - 00:08:46.070` | mark that page as readable writable |
| `00:08:46.080 - 00:08:47.670` | executable and |
| `00:08:47.680 - 00:08:49.430` | accessible in user mode |
| `00:08:49.440 - 00:08:51.430` | and then it's going to fill it with some |
| `00:08:51.440 - 00:08:52.710` | code |
| `00:08:52.720 - 00:08:54.710` | okay it's going to fill it with the code |
| `00:08:54.720 - 00:08:57.269` | for this function init code or should |
| `00:08:57.279 - 00:09:00.230` | say this process init code |
| `00:09:00.240 - 00:09:03.910` | a knit code is represented in the kernel |
| `00:09:03.920 - 00:09:06.710` | as an array of a bunch of bytes 52 bytes |
| `00:09:06.720 - 00:09:08.550` | spelled out in hex |
| `00:09:08.560 - 00:09:10.389` | and what |
| `00:09:10.399 - 00:09:12.710` | this function is going to do is simply |
| `00:09:12.720 - 00:09:14.630` | going to copy |
| `00:09:14.640 - 00:09:16.070` | that |
| `00:09:16.080 - 00:09:18.470` | sequence of bytes which is pointed to by |
| `00:09:18.480 - 00:09:21.430` | source with a length of size into that |
| `00:09:21.440 - 00:09:23.269` | first page |
| `00:09:23.279 - 00:09:25.350` | now what are these bytes well they are |
| `00:09:25.360 - 00:09:28.150` | instructions and where do they come from |
| `00:09:28.160 - 00:09:29.990` | originally they come from a file called |
| `00:09:30.000 - 00:09:31.670` | initcode.s |
| `00:09:31.680 - 00:09:33.590` | which is the very first user mode |
| `00:09:33.600 - 00:09:36.310` | process that ever executes |
| `00:09:36.320 - 00:09:38.070` | and it's written in assembly code but |
| `00:09:38.080 - 00:09:41.190` | basically what it does is it calls the |
| `00:09:41.200 - 00:09:45.190` | exact system call passing it a file name |
| `00:09:45.200 - 00:09:48.870` | which is the name of the init process |
| `00:09:48.880 - 00:09:51.509` | as well as the zero argument |
| `00:09:51.519 - 00:09:52.389` | that |
| `00:09:52.399 - 00:09:55.190` | should not uh ever return this init |
| `00:09:55.200 - 00:09:58.070` | function uh this init file should exist |
| `00:09:58.080 - 00:10:00.150` | and so there should be no problem but if |
| `00:10:00.160 - 00:10:02.870` | for some reason it does return this code |
| `00:10:02.880 - 00:10:05.269` | we'll call the exit system call |
| `00:10:05.279 - 00:10:08.150` | which itself should never return but uh |
| `00:10:08.160 - 00:10:10.230` | that is followed by a go to |
| `00:10:10.240 - 00:10:12.069` | to this loop label |
| `00:10:12.079 - 00:10:14.630` | and that is all assembled and the bytes |
| `00:10:14.640 - 00:10:16.790` | then are extracted from the executable |
| `00:10:16.800 - 00:10:19.030` | and placed in this array where they can |
| `00:10:19.040 - 00:10:22.389` | be copied into this single page virtual |
| `00:10:22.399 - 00:10:25.829` | address space |
| `00:10:25.839 - 00:10:28.710` | the next function to look at is uvm aluc |
| `00:10:28.720 - 00:10:31.030` | which will add pages to an existing |
| `00:10:31.040 - 00:10:33.030` | virtual address space |
| `00:10:33.040 - 00:10:34.710` | it's passed a pointer to the existing |
| `00:10:34.720 - 00:10:37.590` | address space and the size of the old |
| `00:10:37.600 - 00:10:39.509` | virtual address space as well as the |
| `00:10:39.519 - 00:10:41.990` | size we'd like to bring it up to |
| `00:10:42.000 - 00:10:44.949` | so it calls k alec to allocate physical |
| `00:10:44.959 - 00:10:48.870` | pages and it calls map pages to add |
| `00:10:48.880 - 00:10:51.750` | page table entries to the page table |
| `00:10:51.760 - 00:10:53.670` | the new pages will be marked readable |
| `00:10:53.680 - 00:10:56.550` | writable executable and accessible in |
| `00:10:56.560 - 00:10:58.230` | user mode |
| `00:10:58.240 - 00:11:00.870` | and so here's uh an attempt to show this |
| `00:11:00.880 - 00:11:01.990` | with pictures |
| `00:11:02.000 - 00:11:04.790` | here's the old virtual address space and |
| `00:11:04.800 - 00:11:07.590` | uh we want to add as many pages as |
| `00:11:07.600 - 00:11:10.470` | necessary to bring it up to new |
| `00:11:10.480 - 00:11:13.350` | the old and the new pointers on the old |
| `00:11:13.360 - 00:11:15.190` | and the new sizes need not be page |
| `00:11:15.200 - 00:11:18.069` | aligned and we will add as many pages as |
| `00:11:18.079 - 00:11:20.069` | necessary to make sure that the |
| `00:11:20.079 - 00:11:22.389` | virtual address space is at least as big |
| `00:11:22.399 - 00:11:25.590` | as the new size and we will return the |
| `00:11:25.600 - 00:11:27.350` | new size |
| `00:11:27.360 - 00:11:29.590` | if there are any problems |
| `00:11:29.600 - 00:11:32.949` | this function will call k free and uvm |
| `00:11:32.959 - 00:11:35.190` | the alec which i'll discuss in a second |
| `00:11:35.200 - 00:11:36.949` | to basically |
| `00:11:36.959 - 00:11:39.269` | return all the pages to the free pool |
| `00:11:39.279 - 00:11:41.030` | and restore the page table to what it |
| `00:11:41.040 - 00:11:44.230` | was before as well as returning a zero |
| `00:11:44.240 - 00:11:45.990` | to indicate that |
| `00:11:46.000 - 00:11:48.230` | it has failed |
| `00:11:48.240 - 00:11:51.350` | so here is an example use of it here's a |
| `00:11:51.360 - 00:11:53.750` | user's virtual address space we've got |
| `00:11:53.760 - 00:11:55.430` | the code and the data at the lowest |
| `00:11:55.440 - 00:11:56.389` | address |
| `00:11:56.399 - 00:11:58.470` | we've got the stack page and a guard |
| `00:11:58.480 - 00:12:01.030` | page to catch stack overflow and then |
| `00:12:01.040 - 00:12:03.430` | zero or more heap pages |
| `00:12:03.440 - 00:12:05.590` | this is the so-called break point that's |
| `00:12:05.600 - 00:12:07.590` | how big the user thinks the address |
| `00:12:07.600 - 00:12:09.750` | space is but of course we also have the |
| `00:12:09.760 - 00:12:12.230` | trampoline page and the trap frame page |
| `00:12:12.240 - 00:12:13.829` | up in high memory |
| `00:12:13.839 - 00:12:14.629` | so |
| `00:12:14.639 - 00:12:17.670` | when the user program wants to grow the |
| `00:12:17.680 - 00:12:18.550` | heap |
| `00:12:18.560 - 00:12:21.030` | we will ultimately end up calling uvm |
| `00:12:21.040 - 00:12:25.190` | alec uh using this point as the old and |
| `00:12:25.200 - 00:12:27.990` | enlarge the heap to whatever the user |
| `00:12:28.000 - 00:12:29.750` | needs it to be |
| `00:12:29.760 - 00:12:31.350` | we've also got |
| `00:12:31.360 - 00:12:33.350` | uvm unmap |
| `00:12:33.360 - 00:12:35.190` | and this is going to go through each |
| `00:12:35.200 - 00:12:36.230` | page |
| `00:12:36.240 - 00:12:40.470` | and set its page table entry to zero |
| `00:12:40.480 - 00:12:42.790` | which includes the valid bit |
| `00:12:42.800 - 00:12:44.470` | and so it will |
| `00:12:44.480 - 00:12:45.269` | by |
| `00:12:45.279 - 00:12:47.350` | definition change this page table entry |
| `00:12:47.360 - 00:12:49.509` | to an invalid entry |
| `00:12:49.519 - 00:12:50.470` | and so |
| `00:12:50.480 - 00:12:52.470` | we have the pointer to the page table we |
| `00:12:52.480 - 00:12:55.110` | want to modify and the virtual address |
| `00:12:55.120 - 00:12:56.629` | as well as the number of pages we'd like |
| `00:12:56.639 - 00:12:59.430` | to remove from the virtual address space |
| `00:12:59.440 - 00:13:02.069` | and we also have a fourth argument do |
| `00:13:02.079 - 00:13:04.150` | free and this boolean tells whether or |
| `00:13:04.160 - 00:13:06.389` | not to free the data pages so if it's |
| `00:13:06.399 - 00:13:09.590` | true then this function will also call |
| `00:13:09.600 - 00:13:12.389` | kfree to return the data pages that are |
| `00:13:12.399 - 00:13:14.790` | being unmapped to the |
| `00:13:14.800 - 00:13:17.750` | free pool |
| `00:13:17.760 - 00:13:20.310` | next let's look at the function uvm |
| `00:13:20.320 - 00:13:22.069` | dialog which is very similar to the |
| `00:13:22.079 - 00:13:23.590` | previous function |
| `00:13:23.600 - 00:13:25.910` | the previous function i talked about uvm |
| `00:13:25.920 - 00:13:27.030` | unmap |
| `00:13:27.040 - 00:13:29.829` | worked in terms of the number of pages |
| `00:13:29.839 - 00:13:31.910` | whereas this one works in terms of the |
| `00:13:31.920 - 00:13:34.790` | old size and the new size and it's used |
| `00:13:34.800 - 00:13:36.470` | to shrink back |
| `00:13:36.480 - 00:13:37.990` | an address space |
| `00:13:38.000 - 00:13:39.829` | and all it does really is called uvm |
| `00:13:39.839 - 00:13:42.310` | unmapped to remove the pages and to free |
| `00:13:42.320 - 00:13:43.670` | the data pages |
| `00:13:43.680 - 00:13:46.550` | so it calls uvm unmap |
| `00:13:46.560 - 00:13:50.069` | with the do free argument set to true |
| `00:13:50.079 - 00:13:52.949` | the old size and the new size do not |
| `00:13:52.959 - 00:13:55.269` | need to be page aligned as this |
| `00:13:55.279 - 00:13:57.430` | particular example shows |
| `00:13:57.440 - 00:13:59.829` | and also the new size can be greater |
| `00:13:59.839 - 00:14:02.230` | than the old size in which case we don't |
| `00:14:02.240 - 00:14:03.670` | do anything |
| `00:14:03.680 - 00:14:05.350` | it's okay |
| `00:14:05.360 - 00:14:07.269` | next i want to talk about free walk |
| `00:14:07.279 - 00:14:10.069` | which is used to remove a page table to |
| `00:14:10.079 - 00:14:12.230` | free up all the index pages that are |
| `00:14:12.240 - 00:14:14.230` | involved in a page table |
| `00:14:14.240 - 00:14:17.189` | it's passed a pointer to the root of the |
| `00:14:17.199 - 00:14:18.790` | page table tree |
| `00:14:18.800 - 00:14:21.509` | and it will walk the entire tree and |
| `00:14:21.519 - 00:14:23.350` | free the index pages but it will not |
| `00:14:23.360 - 00:14:25.750` | free the data pages |
| `00:14:25.760 - 00:14:27.990` | this is actually a recursive function |
| `00:14:28.000 - 00:14:30.389` | and it's passed a pointer to any index |
| `00:14:30.399 - 00:14:33.030` | node and what it does is it calls itself |
| `00:14:33.040 - 00:14:35.670` | on the children of that node to delete |
| `00:14:35.680 - 00:14:38.150` | the subtrees and then it |
| `00:14:38.160 - 00:14:42.069` | frees the root page the top page |
| `00:14:42.079 - 00:14:44.629` | now in kernel coding we need to be |
| `00:14:44.639 - 00:14:46.949` | careful with recursive functions |
| `00:14:46.959 - 00:14:49.350` | the kernel doesn't have a very large |
| `00:14:49.360 - 00:14:51.750` | stack and typically they're a fixed size |
| `00:14:51.760 - 00:14:53.990` | and rather small stack |
| `00:14:54.000 - 00:14:56.150` | so um we need to make sure that this |
| `00:14:56.160 - 00:14:58.550` | thing doesn't recurse too deeply |
| `00:14:58.560 - 00:15:01.590` | in fact the tree is only three levels |
| `00:15:01.600 - 00:15:03.350` | deep so we can be certain that it won't |
| `00:15:03.360 - 00:15:06.389` | recurse very far and we can be sure that |
| `00:15:06.399 - 00:15:08.790` | the stack usage is bounded and we won't |
| `00:15:08.800 - 00:15:12.710` | get into any trouble there |
| `00:15:12.720 - 00:15:15.990` | next let's look at the uvm free function |
| `00:15:16.000 - 00:15:17.670` | which will |
| `00:15:17.680 - 00:15:20.230` | basically free the uh virtual address |
| `00:15:20.240 - 00:15:23.189` | space and uh it's passed a pointer to |
| `00:15:23.199 - 00:15:25.910` | the page table and the current size of |
| `00:15:25.920 - 00:15:27.829` | the virtual address space |
| `00:15:27.839 - 00:15:30.150` | so here i'm showing |
| `00:15:30.160 - 00:15:32.949` | the user's virtual address space |
| `00:15:32.959 - 00:15:35.030` | and we see the code in the data |
| `00:15:35.040 - 00:15:37.990` | we see a stack page and a guard page to |
| `00:15:38.000 - 00:15:39.430` | catch overflow |
| `00:15:39.440 - 00:15:41.590` | and finally zero or more pages that are |
| `00:15:41.600 - 00:15:43.189` | allocated to the heap |
| `00:15:43.199 - 00:15:44.710` | and what this thing is going to do is |
| `00:15:44.720 - 00:15:47.670` | free all the pages below size |
| `00:15:47.680 - 00:15:50.069` | and it's going to walk the page table |
| `00:15:50.079 - 00:15:53.189` | and free all the index pages |
| `00:15:53.199 - 00:15:54.629` | a user address space also has a |
| `00:15:54.639 - 00:15:57.189` | trampoline page and a trap page |
| `00:15:57.199 - 00:15:59.269` | the tripling page is shared by all |
| `00:15:59.279 - 00:16:00.710` | virtual address spaces so we don't want |
| `00:16:00.720 - 00:16:03.110` | to free that and the trap page is also |
| `00:16:03.120 - 00:16:05.030` | pre-allocated there's one for each |
| `00:16:05.040 - 00:16:09.749` | process so that's not free either |
| `00:16:09.759 - 00:16:12.870` | the next function is uvm copy |
| `00:16:12.880 - 00:16:14.949` | remember that when we have a fork system |
| `00:16:14.959 - 00:16:17.670` | call we need to copy the entire virtual |
| `00:16:17.680 - 00:16:20.150` | address space of the process that |
| `00:16:20.160 - 00:16:23.030` | invokes the fork and we need to create a |
| `00:16:23.040 - 00:16:25.590` | second address space for the child |
| `00:16:25.600 - 00:16:26.629` | process |
| `00:16:26.639 - 00:16:29.110` | and uvm copy is going to take care of |
| `00:16:29.120 - 00:16:33.110` | that for us it will copy all pages up to |
| `00:16:33.120 - 00:16:36.710` | size from the old address space and put |
| `00:16:36.720 - 00:16:39.110` | them into a new address space so it's |
| `00:16:39.120 - 00:16:41.110` | past the pointer to the old page table |
| `00:16:41.120 - 00:16:43.509` | and to the new page table as well as the |
| `00:16:43.519 - 00:16:44.949` | size |
| `00:16:44.959 - 00:16:45.910` | so |
| `00:16:45.920 - 00:16:48.710` | here is a picture of the old address |
| `00:16:48.720 - 00:16:50.790` | space we've got the code the data the |
| `00:16:50.800 - 00:16:53.189` | guard the stack the heap pages as well |
| `00:16:53.199 - 00:16:55.189` | as the trampoline and trap frame |
| `00:16:55.199 - 00:16:57.509` | and here is the new address space before |
| `00:16:57.519 - 00:16:59.590` | we call this function |
| `00:16:59.600 - 00:17:02.710` | and it will already be set up with the |
| `00:17:02.720 - 00:17:04.949` | topmost page pointing to the trampoline |
| `00:17:04.959 - 00:17:07.669` | page and it will have its own trap frame |
| `00:17:07.679 - 00:17:09.189` | page |
| `00:17:09.199 - 00:17:11.669` | and what this will do is |
| `00:17:11.679 - 00:17:13.750` | copy all of the data pages so here i've |
| `00:17:13.760 - 00:17:16.710` | indicated the data page is being copied |
| `00:17:16.720 - 00:17:20.150` | and it will create mappings for each one |
| `00:17:20.160 - 00:17:21.829` | of these |
| `00:17:21.839 - 00:17:24.470` | the permissions that were valid here |
| `00:17:24.480 - 00:17:25.429` | will be |
| `00:17:25.439 - 00:17:27.189` | applied to the new |
| `00:17:27.199 - 00:17:28.950` | page table so these will have the same |
| `00:17:28.960 - 00:17:31.590` | exact permissions |
| `00:17:31.600 - 00:17:33.029` | thing i wanted to mention is that the |
| `00:17:33.039 - 00:17:35.669` | guard page is actually mapped to a |
| `00:17:35.679 - 00:17:37.590` | physical page in memory |
| `00:17:37.600 - 00:17:40.549` | it will be marked as not accessible in |
| `00:17:40.559 - 00:17:43.669` | user mode so over here it will also be |
| `00:17:43.679 - 00:17:46.390` | marked as not accessible in user mode |
| `00:17:46.400 - 00:17:47.990` | but to keep things simple |
| `00:17:48.000 - 00:17:50.070` | we copy the page even though this data |
| `00:17:50.080 - 00:17:53.110` | page will never be accessed |
| `00:17:53.120 - 00:17:55.190` | so in order to do this the function |
| `00:17:55.200 - 00:17:56.470` | calls |
| `00:17:56.480 - 00:17:57.750` | walk |
| `00:17:57.760 - 00:18:00.070` | to walk the page table chaotic to get |
| `00:18:00.080 - 00:18:02.549` | new pages new physical memory pages |
| `00:18:02.559 - 00:18:05.430` | memory move to copy the data and map |
| `00:18:05.440 - 00:18:08.549` | pages to add mappings to the new page |
| `00:18:08.559 - 00:18:09.750` | table |
| `00:18:09.760 - 00:18:11.990` | if anything goes wrong for example if we |
| `00:18:12.000 - 00:18:13.590` | run out of memory |
| `00:18:13.600 - 00:18:16.789` | then it basically undoes everything it |
| `00:18:16.799 - 00:18:21.830` | calls kfree and uv m unmap to |
| `00:18:21.840 - 00:18:22.549` | uh |
| `00:18:22.559 - 00:18:24.710` | remove every uh entry from the page |
| `00:18:24.720 - 00:18:29.190` | table and uh return all the pages |
| `00:18:29.200 - 00:18:32.070` | next let's look at uvm clear |
| `00:18:32.080 - 00:18:35.430` | and this is uh used only to mark the |
| `00:18:35.440 - 00:18:38.070` | guard page as not accessible in user |
| `00:18:38.080 - 00:18:40.470` | mode that's the only place this is used |
| `00:18:40.480 - 00:18:43.350` | so it's past the pointer to the page |
| `00:18:43.360 - 00:18:45.750` | table and the virtual address of the |
| `00:18:45.760 - 00:18:49.029` | guard page and it will mark that page by |
| `00:18:49.039 - 00:18:51.110` | clearing the u-bit in the page table |
| `00:18:51.120 - 00:18:53.430` | entry and as i said it's used for |
| `00:18:53.440 - 00:18:55.590` | marking the stack |
| `00:18:55.600 - 00:18:58.070` | the stax guard page in a user address |
| `00:18:58.080 - 00:19:00.310` | space |
| `00:19:00.320 - 00:19:02.070` | from time to time the kernel needs to |
| `00:19:02.080 - 00:19:05.110` | move bytes into or out of the virtual |
| `00:19:05.120 - 00:19:08.630` | address space of the user processes |
| `00:19:08.640 - 00:19:11.669` | for example when the system call open |
| `00:19:11.679 - 00:19:13.190` | happens it's past |
| `00:19:13.200 - 00:19:16.710` | a pointer to the string that is the path |
| `00:19:16.720 - 00:19:18.470` | name or the file name that we want to be |
| `00:19:18.480 - 00:19:21.990` | opening and that string is located in |
| `00:19:22.000 - 00:19:24.789` | the user's virtual address space |
| `00:19:24.799 - 00:19:27.190` | the kernel needs to get it |
| `00:19:27.200 - 00:19:28.950` | the problem is that that string may |
| `00:19:28.960 - 00:19:32.070` | cross multiple pages it may lie on more |
| `00:19:32.080 - 00:19:33.590` | than one page |
| `00:19:33.600 - 00:19:36.710` | so in this example i'm showing the block |
| `00:19:36.720 - 00:19:39.669` | of bytes that we want to access in red |
| `00:19:39.679 - 00:19:42.230` | and you can see that this block of bytes |
| `00:19:42.240 - 00:19:44.549` | spans uh four pages |
| `00:19:44.559 - 00:19:45.590` | well that's |
| `00:19:45.600 - 00:19:46.710` | what it looks like in the virtual |
| `00:19:46.720 - 00:19:49.029` | address space it's all contiguous |
| `00:19:49.039 - 00:19:51.350` | at least the user sees it that way but |
| `00:19:51.360 - 00:19:53.990` | the actual physical pages in memory can |
| `00:19:54.000 - 00:19:57.750` | be spread all over and so this data is |
| `00:19:57.760 - 00:19:59.909` | not contiguous in |
| `00:19:59.919 - 00:20:02.230` | physical memory and so the functions |
| `00:20:02.240 - 00:20:04.149` | that we're going to look at copy in and |
| `00:20:04.159 - 00:20:05.270` | copy out |
| `00:20:05.280 - 00:20:08.549` | basically are used to copy the data into |
| `00:20:08.559 - 00:20:10.870` | a buffer in the kernel or from the |
| `00:20:10.880 - 00:20:13.110` | buffer back in such a way that it will |
| `00:20:13.120 - 00:20:15.510` | be contiguous in the virtual address |
| `00:20:15.520 - 00:20:16.710` | space |
| `00:20:16.720 - 00:20:19.590` | so let's start with copy in |
| `00:20:19.600 - 00:20:22.710` | it's past a page table and |
| `00:20:22.720 - 00:20:25.430` | the virtual address from which to get |
| `00:20:25.440 - 00:20:26.789` | some bytes |
| `00:20:26.799 - 00:20:28.549` | as well as the number of bytes we want |
| `00:20:28.559 - 00:20:29.990` | to obtain |
| `00:20:30.000 - 00:20:32.549` | and it's passed a place like the address |
| `00:20:32.559 - 00:20:34.950` | of this buffer here where it wants to |
| `00:20:34.960 - 00:20:36.070` | store |
| `00:20:36.080 - 00:20:38.390` | the bytes and what it will do is it will |
| `00:20:38.400 - 00:20:40.870` | copy bytes from the user's virtual |
| `00:20:40.880 - 00:20:42.230` | address space |
| `00:20:42.240 - 00:20:43.190` | to |
| `00:20:43.200 - 00:20:44.950` | this kernel area pointed to by |
| `00:20:44.960 - 00:20:46.310` | destination |
| `00:20:46.320 - 00:20:48.710` | so it's going to copy the individual |
| `00:20:48.720 - 00:20:51.350` | pieces i mean it would start by copying |
| `00:20:51.360 - 00:20:52.470` | um |
| `00:20:52.480 - 00:20:54.470` | well let's say memory goes up this way |
| `00:20:54.480 - 00:20:56.710` | it starts by copying this piece |
| `00:20:56.720 - 00:20:58.149` | and then |
| `00:20:58.159 - 00:20:59.350` | this piece |
| `00:20:59.360 - 00:21:01.350` | and finally the last |
| `00:21:01.360 - 00:21:03.510` | the last piece |
| `00:21:03.520 - 00:21:05.350` | copy out is the reverse it's used to |
| `00:21:05.360 - 00:21:08.390` | move data from this buffer back into the |
| `00:21:08.400 - 00:21:11.029` | virtual address space it's past a page |
| `00:21:11.039 - 00:21:13.750` | table and the virtual address where the |
| `00:21:13.760 - 00:21:15.909` | data is supposed to be placed as well as |
| `00:21:15.919 - 00:21:17.590` | a pointer to the source that is the |
| `00:21:17.600 - 00:21:20.070` | buffer and the number of bytes |
| `00:21:20.080 - 00:21:21.750` | if anything goes and it copies bytes |
| `00:21:21.760 - 00:21:24.070` | from the kernel area back to the user's |
| `00:21:24.080 - 00:21:25.990` | virtual address space |
| `00:21:26.000 - 00:21:27.909` | if anything goes wrong for example the |
| `00:21:27.919 - 00:21:30.470` | virtual address is not valid |
| `00:21:30.480 - 00:21:32.710` | then this function will return a minus |
| `00:21:32.720 - 00:21:33.590` | one |
| `00:21:33.600 - 00:21:36.549` | if there's an error or zero if it's all |
| `00:21:36.559 - 00:21:39.270` | okay |
| `00:21:39.280 - 00:21:41.590` | in the case of the open system call we |
| `00:21:41.600 - 00:21:43.830` | are looking for a string that will be |
| `00:21:43.840 - 00:21:45.669` | null terminated so we only want to copy |
| `00:21:45.679 - 00:21:48.310` | bytes up to the null byte and so there's |
| `00:21:48.320 - 00:21:50.310` | a variation on copy in |
| `00:21:50.320 - 00:21:52.789` | called copy and string |
| `00:21:52.799 - 00:21:56.310` | and it's similar it's passed a source |
| `00:21:56.320 - 00:21:59.750` | virtual address and the destination uh |
| `00:21:59.760 - 00:22:01.270` | that is the address of a buffer into |
| `00:22:01.280 - 00:22:03.830` | which it's going to be placed as well as |
| `00:22:03.840 - 00:22:05.990` | the maximum number of bytes to copy this |
| `00:22:06.000 - 00:22:07.590` | is probably going to be the buffer |
| `00:22:07.600 - 00:22:08.630` | length |
| `00:22:08.640 - 00:22:11.669` | and so it will start copying |
| `00:22:11.679 - 00:22:13.190` | and it will go up to max length but it |
| `00:22:13.200 - 00:22:15.909` | will stop after it copies the null |
| `00:22:15.919 - 00:22:17.750` | terminating byte at the end of the |
| `00:22:17.760 - 00:22:19.110` | string |
| `00:22:19.120 - 00:22:20.950` | if it goes all the way to the |
| `00:22:20.960 - 00:22:23.510` | maximum length without encountering the |
| `00:22:23.520 - 00:22:25.350` | terminating null byte |
| `00:22:25.360 - 00:22:28.230` | then it will stop copying and it will |
| `00:22:28.240 - 00:22:31.190` | turn a minus one to indicate that the |
| `00:22:31.200 - 00:22:33.430` | there was some sort of an error |
| `00:22:33.440 - 00:22:35.669` | okay as i mentioned uh |
| `00:22:35.679 - 00:22:37.510` | this this is an overview of these |
| `00:22:37.520 - 00:22:41.510` | functions and in the um next video i'll |
| `00:22:41.520 - 00:22:43.350` | be walking through the code |
| `00:22:43.360 - 00:22:46.070` | and it's safe if you want to just skip |
| `00:22:46.080 - 00:22:48.070` | that if you feel like you understand |
| `00:22:48.080 - 00:22:49.669` | these functions well enough |
| `00:22:49.679 - 00:22:52.149` | uh you can skip the next video which |
| `00:22:52.159 - 00:22:55.029` | walks through the code in detail and |
| `00:22:55.039 - 00:22:56.789` | proceed with just this overview of what |
| `00:22:56.799 - 00:22:58.470` | these functions do |
| `00:22:58.480 - 00:23:00.470` | okay that's it thank you and see you in |
| `00:23:00.480 - 00:23:04.919` | the next video or the one after that |
