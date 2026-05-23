# xv6 Kernel-20: VM Functions: Code Walkthru

| Field | Value |
| --- | --- |
| Video ID | `BB0Tbyu3XMQ` |
| URL | <https://www.youtube.com/watch?v=BB0Tbyu3XMQ> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.000 - 00:00:02.510` | This video is part of a series on the |
| `00:00:02.520 - 00:00:05.310` | XV6 operating system kernel. |
| `00:00:05.320 - 00:00:07.070` | In this video, I'm going to talk about |
| `00:00:07.080 - 00:00:09.750` | the virtual memory helper functions that |
| `00:00:09.760 - 00:00:13.230` | are found in the file vm.c. |
| `00:00:13.240 - 00:00:16.030` | In the previous video, I looked at the |
| `00:00:16.040 - 00:00:19.110` | same file and I did an overview talking |
| `00:00:19.120 - 00:00:21.550` | about each function individually and |
| `00:00:21.560 - 00:00:24.470` | describing what it did. |
| `00:00:24.480 - 00:00:27.310` | In this video, I'm going to look at the |
| `00:00:27.320 - 00:00:29.750` | functions again a second time, but this |
| `00:00:29.760 - 00:00:31.910` | time I'm going to actually do a code |
| `00:00:31.920 - 00:00:35.190` | walk-through and see how they achieve |
| `00:00:35.200 - 00:00:37.510` | their functionality. |
| `00:00:37.520 - 00:00:40.430` | You can watch both videos if you want |
| `00:00:40.440 - 00:00:42.110` | the greatest understanding of this |
| `00:00:42.120 - 00:00:44.550` | material, or if you've already watched |
| `00:00:44.560 - 00:00:46.950` | the previous video, then it's quite |
| `00:00:46.960 - 00:00:48.430` | reasonable to consider skipping this |
| `00:00:48.440 - 00:00:50.910` | video. This video is quite long, over an |
| `00:00:50.920 - 00:00:53.390` | hour long, so you may want to just skip |
| `00:00:53.400 - 00:00:54.950` | this video. But for greater |
| `00:00:54.960 - 00:00:57.710` | understanding, watch them both. Or if |
| `00:00:57.720 - 00:00:59.790` | you have not yet watched the overview of |
| `00:00:59.800 - 00:01:01.910` | function, then you might want to just |
| `00:01:01.920 - 00:01:04.310` | watch this function this video alone, |
| `00:01:04.320 - 00:01:05.950` | and that's also |
| `00:01:05.960 - 00:01:07.750` | reasonable and acceptable. |
| `00:01:07.760 - 00:01:11.350` | Okay, let's get going. |
| `00:01:11.360 - 00:01:13.110` | Okay, let's take a look at the walk |
| `00:01:13.120 - 00:01:15.990` | function. It's passed a pointer to the |
| `00:01:16.000 - 00:01:18.870` | root of the page table tree, a virtual |
| `00:01:18.880 - 00:01:21.910` | address, and a boolean called alloc. |
| `00:01:21.920 - 00:01:25.230` | And it walks the tree and returns the |
| `00:01:25.240 - 00:01:29.030` | address of the page table entry. |
| `00:01:29.040 - 00:01:32.670` | So, here is a picture of a page table |
| `00:01:32.680 - 00:01:36.390` | and our page table consists of three |
| `00:01:36.400 - 00:01:40.110` | levels of index pages and the data page. |
| `00:01:40.120 - 00:01:41.470` | We're passed a pointer to the root of |
| `00:01:41.480 - 00:01:44.390` | this tree and we're going to walk this |
| `00:01:44.400 - 00:01:47.590` | edge here to this index page, and then |
| `00:01:47.600 - 00:01:49.030` | we're going to walk this edge to this |
| `00:01:49.040 - 00:01:50.870` | index page, and finally we're going to |
| `00:01:50.880 - 00:01:53.510` | return a pointer to the page table entry |
| `00:01:53.520 - 00:01:56.630` | in the level zero index page. We do not |
| `00:01:56.640 - 00:01:58.590` | follow this pointer down here and don't |
| `00:01:58.600 - 00:02:01.910` | do anything with a data page. |
| `00:02:01.920 - 00:02:04.670` | If in the course of walking that tree, |
| `00:02:04.680 - 00:02:05.790` | either |
| `00:02:05.800 - 00:02:08.109` | the index page at this level or the |
| `00:02:08.119 - 00:02:10.430` | index page at this level is missing, we |
| `00:02:10.440 - 00:02:12.590` | have the option to allocate them and |
| `00:02:12.600 - 00:02:15.230` | fill them in. So, that's what the |
| `00:02:15.240 - 00:02:18.030` | last parameter alloc is all about. And |
| `00:02:18.040 - 00:02:20.270` | if it's true and we need to, then we |
| `00:02:20.280 - 00:02:23.270` | will allocate and initialize index pages |
| `00:02:23.280 - 00:02:24.630` | in the tree. |
| `00:02:24.640 - 00:02:28.030` | So, here is the uh code for this |
| `00:02:28.040 - 00:02:29.390` | function. |
| `00:02:29.400 - 00:02:32.670` | And we see that we are passed a pointer |
| `00:02:32.680 - 00:02:33.790` | to the root of the page table, the |
| `00:02:33.800 - 00:02:36.990` | virtual address, and this boolean alloc. |
| `00:02:37.000 - 00:02:38.830` | First, we check to make sure that the |
| `00:02:38.840 - 00:02:41.990` | virtual address is not exceeding the |
| `00:02:42.000 - 00:02:44.190` | maximum allowable number and if so, we |
| `00:02:44.200 - 00:02:46.110` | print an error message. |
| `00:02:46.120 - 00:02:48.190` | Okay, so this is organized as a a loop |
| `00:02:48.200 - 00:02:50.230` | here, a for loop, and you can see that |
| `00:02:50.240 - 00:02:52.430` | level starts at two and it's |
| `00:02:52.440 - 00:02:55.310` | decremented. And |
| `00:02:55.320 - 00:02:58.270` | So, it only iterates twice. The first |
| `00:02:58.280 - 00:03:00.710` | time level will be equal to two, and the |
| `00:03:00.720 - 00:03:02.750` | second time level will be equal to one, |
| `00:03:02.760 - 00:03:06.030` | and then it exits the loop and uh does |
| `00:03:06.040 - 00:03:07.949` | this last thing. So, what the loop is |
| `00:03:07.959 - 00:03:10.229` | going to do, it it's pa- it starts with |
| `00:03:10.239 - 00:03:12.390` | page table pointing to one of these |
| `00:03:12.400 - 00:03:17.310` | index nodes, and the loop body will go |
| `00:03:17.320 - 00:03:20.190` | down, grab this pointer, and chase it |
| `00:03:20.200 - 00:03:22.910` | down here, and at the end of the loop, |
| `00:03:22.920 - 00:03:24.270` | at the beginning of the iteration of the |
| `00:03:24.280 - 00:03:27.390` | next iteration, page table will point to |
| `00:03:27.400 - 00:03:28.830` | this node. |
| `00:03:28.840 - 00:03:31.590` | Okay, so in the first iteration, it |
| `00:03:31.600 - 00:03:34.870` | moves page table from here to here, and |
| `00:03:34.880 - 00:03:36.030` | in the second iteration, it moves it |
| `00:03:36.040 - 00:03:38.750` | from from here to here, and then |
| `00:03:38.760 - 00:03:41.229` | finally, uh we go down and we get the |
| `00:03:41.239 - 00:03:44.270` | pointer to this page table entry. |
| `00:03:44.280 - 00:03:45.910` | Okay, so let's take a look at that in a |
| `00:03:45.920 - 00:03:48.430` | little bit more detail. |
| `00:03:48.440 - 00:03:51.350` | We have another variable called PTE. So, |
| `00:03:51.360 - 00:03:54.230` | you can see the first thing we do is we |
| `00:03:54.240 - 00:03:54.790` | um |
| `00:03:54.800 - 00:03:59.110` | ex- extract the uh index field. Now, uh |
| `00:03:59.120 - 00:04:02.190` | remember that a uh virtual address uh |
| `00:04:02.200 - 00:04:05.430` | looks like this. Okay? It has |
| `00:04:05.440 - 00:04:06.830` | three fields |
| `00:04:06.840 - 00:04:08.550` | for the level two, level one, and level |
| `00:04:08.560 - 00:04:11.710` | zero index, as well as the um 12-bit |
| `00:04:11.720 - 00:04:15.710` | offset. And we uh uh |
| `00:04:15.720 - 00:04:18.750` | extract We start by extracting the level |
| `00:04:18.760 - 00:04:20.349` | two index here. |
| `00:04:20.359 - 00:04:22.670` | And uh then we |
| `00:04:22.680 - 00:04:25.230` | um |
| `00:04:25.240 - 00:04:28.670` | index into the page table. And uh |
| `00:04:28.680 - 00:04:30.590` | we use that to index in here and get |
| `00:04:30.600 - 00:04:32.350` | this pointer. And that's what we do |
| `00:04:32.360 - 00:04:34.710` | here. PTE now points to this this page |
| `00:04:34.720 - 00:04:37.430` | table entry. And then um |
| `00:04:37.440 - 00:04:40.270` | here what we do is we look at the the |
| `00:04:40.280 - 00:04:41.909` | page table entry. We fetch that page |
| `00:04:41.919 - 00:04:44.350` | table entry and get a pointer to the |
| `00:04:44.360 - 00:04:45.830` | next one. So, that's what's going on |
| `00:04:45.840 - 00:04:47.270` | here. We fetch |
| `00:04:47.280 - 00:04:50.150` | the the page table entry and that that |
| `00:04:50.160 - 00:04:53.390` | gives us a pointer to the next one. And |
| `00:04:53.400 - 00:04:55.470` | we set page table to that. Then we |
| `00:04:55.480 - 00:04:57.670` | iterate. Okay, let's look at that in a a |
| `00:04:57.680 - 00:05:00.230` | little bit more detail. So, |
| `00:05:00.240 - 00:05:02.150` | here we see |
| `00:05:02.160 - 00:05:06.630` | a call to this macro function called PX. |
| `00:05:06.640 - 00:05:08.990` | And what does PX do? Well, here's |
| `00:05:09.000 - 00:05:10.950` | another picture of the virtual address |
| `00:05:10.960 - 00:05:14.190` | with the nine-bit fields. And PX is |
| `00:05:14.200 - 00:05:17.750` | passed uh this virtual address plus a a |
| `00:05:17.760 - 00:05:19.950` | level number, two, zero, or one. And |
| `00:05:19.960 - 00:05:21.350` | then it extracts those nine bits and |
| `00:05:21.360 - 00:05:23.670` | moves them down uh here. So, it returns |
| `00:05:23.680 - 00:05:26.510` | a number between zero and 511, which we |
| `00:05:26.520 - 00:05:30.750` | can use to index into the uh index page. |
| `00:05:30.760 - 00:05:34.750` | So, PX first we extract level uh uh the |
| `00:05:34.760 - 00:05:37.670` | field for level two. And we use that as |
| `00:05:37.680 - 00:05:41.830` | an index to index into uh this page, and |
| `00:05:41.840 - 00:05:43.550` | that gives us a pointer to this page |
| `00:05:43.560 - 00:05:45.310` | table entry here. |
| `00:05:45.320 - 00:05:46.790` | And we uh |
| `00:05:46.800 - 00:05:49.750` | fetch it, and um |
| `00:05:49.760 - 00:05:52.590` | then we use uh |
| `00:05:52.600 - 00:05:54.950` | the page table entry, we fetch the page |
| `00:05:54.960 - 00:05:57.590` | table entry itself right here, and call |
| `00:05:57.600 - 00:06:01.790` | this function PTE to PA. And I have a |
| `00:06:01.800 - 00:06:05.110` | picture of that uh |
| `00:06:05.120 - 00:06:07.230` | right |
| `00:06:07.240 - 00:06:08.870` | here. |
| `00:06:08.880 - 00:06:12.910` | Here's PTE to physical address. And the |
| `00:06:12.920 - 00:06:15.110` | page table entry consists of 44 bits, |
| `00:06:15.120 - 00:06:17.870` | which is the pointer, plus 10 |
| `00:06:17.880 - 00:06:19.750` | bits, the valid bit, read, write, |
| `00:06:19.760 - 00:06:22.350` | execute, and accessible in user mode. |
| `00:06:22.360 - 00:06:24.710` | So, it's got 10 10 bits here, of which |
| `00:06:24.720 - 00:06:27.070` | uh a few of them are unused. It extracts |
| `00:06:27.080 - 00:06:29.350` | this 44 bits, shifts it a little bit, |
| `00:06:29.360 - 00:06:31.990` | and adds in zeros here to give you a |
| `00:06:32.000 - 00:06:35.230` | physical address. So, PTE to physical |
| `00:06:35.240 - 00:06:36.350` | address. |
| `00:06:36.360 - 00:06:37.470` | There's another function, which we'll |
| `00:06:37.480 - 00:06:39.670` | come to in a second, called physical |
| `00:06:39.680 - 00:06:42.110` | address to PTE, which basically takes |
| `00:06:42.120 - 00:06:44.270` | the physical address and creates a page |
| `00:06:44.280 - 00:06:45.550` | table entry. |
| `00:06:45.560 - 00:06:48.750` | But, here we are taking the page table |
| `00:06:48.760 - 00:06:52.230` | entry and turning it into an address, |
| `00:06:52.240 - 00:06:54.510` | and setting page table to point to it. |
| `00:06:54.520 - 00:06:57.270` | So, in the first iteration, we um go |
| `00:06:57.280 - 00:07:00.150` | down here, and we fetch this this this |
| `00:07:00.160 - 00:07:02.510` | PTE This is the PTE, and we build a |
| `00:07:02.520 - 00:07:04.950` | pointer, and now page table points to |
| `00:07:04.960 - 00:07:07.350` | the next one. And then |
| `00:07:07.360 - 00:07:09.230` | uh we check it. Uh |
| `00:07:09.240 - 00:07:12.630` | here, uh before we do that, we check the |
| `00:07:12.640 - 00:07:14.630` | uh page table entry, |
| `00:07:14.640 - 00:07:17.670` | and we check the valid bit, okay? So, |
| `00:07:17.680 - 00:07:19.750` | that's what's going on here. If the |
| `00:07:19.760 - 00:07:21.830` | valid bit is set, then we can go ahead |
| `00:07:21.840 - 00:07:24.550` | and access it and use it as a pointer. |
| `00:07:24.560 - 00:07:26.150` | If it's not set, then we've got a |
| `00:07:26.160 - 00:07:28.390` | problem. We may have the this page table |
| `00:07:28.400 - 00:07:30.909` | entry may not be valid, and we may have |
| `00:07:30.919 - 00:07:33.510` | to allocate this |
| `00:07:33.520 - 00:07:36.550` | index page right here. So, |
| `00:07:36.560 - 00:07:38.950` | when we fetch the page table entry right |
| `00:07:38.960 - 00:07:41.190` | here, we check it, and if the valid bit |
| `00:07:41.200 - 00:07:44.230` | is one, then we can go ahead and get the |
| `00:07:44.240 - 00:07:46.470` | pointer to the next index page. But if |
| `00:07:46.480 - 00:07:49.270` | it's not one, then we may have to |
| `00:07:49.280 - 00:07:52.790` | allocate. So, um we first check our |
| `00:07:52.800 - 00:07:54.590` | allocate |
| `00:07:54.600 - 00:07:56.030` | parameter here. |
| `00:07:56.040 - 00:07:59.790` | And if it is false, then it's this not |
| `00:07:59.800 - 00:08:02.590` | alloc will be true and we immediately |
| `00:08:02.600 - 00:08:05.430` | abort by returning a null. |
| `00:08:05.440 - 00:08:08.350` | Um but if it is true, we keep going and |
| `00:08:08.360 - 00:08:12.310` | here we see the call to K alloc. So, |
| `00:08:12.320 - 00:08:15.150` | uh we are Let's assume that we on the |
| `00:08:15.160 - 00:08:16.510` | first iteration of the loop had a |
| `00:08:16.520 - 00:08:18.870` | problem. So, that means this was not a |
| `00:08:18.880 - 00:08:20.950` | valid page table entry. So, we're |
| `00:08:20.960 - 00:08:23.350` | allocating the index the level one index |
| `00:08:23.360 - 00:08:25.830` | page with this K alloc. |
| `00:08:25.840 - 00:08:27.030` | And |
| `00:08:27.040 - 00:08:27.990` | uh |
| `00:08:28.000 - 00:08:30.510` | So, we do that and we now have a pointer |
| `00:08:30.520 - 00:08:32.390` | to the next one. So, we set page table |
| `00:08:32.400 - 00:08:33.909` | to point to |
| `00:08:33.919 - 00:08:36.310` | the level one index page that we just |
| `00:08:36.320 - 00:08:38.469` | allocated. If we had a problem and it |
| `00:08:38.479 - 00:08:41.430` | returns null, then again we return and |
| `00:08:41.440 - 00:08:43.430` | abort uh here by returning zero |
| `00:08:43.440 - 00:08:46.630` | immediately. But if the K alloc worked, |
| `00:08:46.640 - 00:08:50.310` | then we initialize the new index page to |
| `00:08:50.320 - 00:08:51.829` | all zeros. |
| `00:08:51.839 - 00:08:55.350` | The call to memset here will |
| `00:08:55.360 - 00:08:58.190` | initialize the new level one index page |
| `00:08:58.200 - 00:09:00.390` | to all zeros. So, that means all the |
| `00:09:00.400 - 00:09:01.790` | valid bits for all the page table |
| `00:09:01.800 - 00:09:04.470` | entries in this would be zero. |
| `00:09:04.480 - 00:09:05.710` | And |
| `00:09:05.720 - 00:09:09.470` | um then we build the page table entry. |
| `00:09:09.480 - 00:09:12.190` | We build this page table entry here |
| `00:09:12.200 - 00:09:14.670` | and store it. Okay, remember it was not |
| `00:09:14.680 - 00:09:15.990` | valid before and it contained no |
| `00:09:16.000 - 00:09:17.910` | pointer. But now what we're going going |
| `00:09:17.920 - 00:09:20.230` | to do is we're going to take the pointer |
| `00:09:20.240 - 00:09:22.270` | to the new page we just allocated. |
| `00:09:22.280 - 00:09:24.110` | That's page table. |
| `00:09:24.120 - 00:09:25.910` | And we're going to convert it physical |
| `00:09:25.920 - 00:09:29.670` | address to page table entry. So, that's |
| `00:09:29.680 - 00:09:30.710` | um |
| `00:09:30.720 - 00:09:32.110` | Whoops. |
| `00:09:32.120 - 00:09:33.829` | That's this page here |
| `00:09:33.839 - 00:09:35.870` | uh showing that we have a physical |
| `00:09:35.880 - 00:09:38.070` | address and physical address to page |
| `00:09:38.080 - 00:09:40.310` | table entry will convert it to a page |
| `00:09:40.320 - 00:09:42.470` | table entry by shifting it and setting |
| `00:09:42.480 - 00:09:44.870` | all these bits to zero. |
| `00:09:44.880 - 00:09:46.230` | Uh but we don't want them all to be |
| `00:09:46.240 - 00:09:48.350` | zero. We want the valid bit to be set. |
| `00:09:48.360 - 00:09:51.870` | So, here we are oring in the valid bit, |
| `00:09:51.880 - 00:09:54.750` | and we store that. Okay? Uh we store it |
| `00:09:54.760 - 00:09:57.270` | right here. Okay? And now we have page |
| `00:09:57.280 - 00:10:00.110` | table pointing to this new index page, |
| `00:10:00.120 - 00:10:01.630` | and we've uh |
| `00:10:01.640 - 00:10:03.750` | we've we've uh fixed the uh pointer |
| `00:10:03.760 - 00:10:04.630` | there. |
| `00:10:04.640 - 00:10:07.590` | So, then we iterate, and we may have to |
| `00:10:07.600 - 00:10:10.150` | uh allocate another page. Um if we've |
| `00:10:10.160 - 00:10:11.750` | allocated this one, then obviously the |
| `00:10:11.760 - 00:10:14.310` | next one will also need to be allocated. |
| `00:10:14.320 - 00:10:16.750` | But in any case, we iterate, |
| `00:10:16.760 - 00:10:20.350` | um and in the second iteration, we index |
| `00:10:20.360 - 00:10:24.190` | down here, follow the pointer, and end |
| `00:10:24.200 - 00:10:25.950` | the loop with a pointer to the level |
| `00:10:25.960 - 00:10:28.790` | zero index page. So, when we end the |
| `00:10:28.800 - 00:10:31.310` | loop, we've got a pointer to the |
| `00:10:31.320 - 00:10:34.830` | level zero index page. And now what we |
| `00:10:34.840 - 00:10:38.870` | do is we extract with our PX function |
| `00:10:38.880 - 00:10:41.070` | the level zero field. So, here's the |
| `00:10:41.080 - 00:10:43.270` | level zero field. We extract those nine |
| `00:10:43.280 - 00:10:46.950` | bits, and that gives us uh the index |
| `00:10:46.960 - 00:10:49.550` | into Okay, here's the level zero page. |
| `00:10:49.560 - 00:10:51.670` | We we use that |
| `00:10:51.680 - 00:10:54.470` | uh last field to index in there, and |
| `00:10:54.480 - 00:10:56.910` | that gives us a pointer to the page |
| `00:10:56.920 - 00:10:59.310` | table entry. And that's what's going on |
| `00:10:59.320 - 00:11:01.430` | right here, and we return |
| `00:11:01.440 - 00:11:06.030` | uh the address of that page table entry. |
| `00:11:06.040 - 00:11:08.230` | We just looked at the function walk, |
| `00:11:08.240 - 00:11:10.670` | which is used to walk the |
| `00:11:10.680 - 00:11:13.710` | uh the page table and allocate index |
| `00:11:13.720 - 00:11:16.430` | pages as necessary, and find a pointer |
| `00:11:16.440 - 00:11:18.950` | to the page table entry in the level |
| `00:11:18.960 - 00:11:21.430` | zero index page. |
| `00:11:21.440 - 00:11:22.910` | Now we're going to look at the function |
| `00:11:22.920 - 00:11:25.750` | map pages, which is used to fill in and |
| `00:11:25.760 - 00:11:29.230` | create this page table entry. |
| `00:11:29.240 - 00:11:32.350` | So, map pages is passed a pointer to the |
| `00:11:32.360 - 00:11:34.270` | root of the page table, a virtual |
| `00:11:34.280 - 00:11:35.390` | address, |
| `00:11:35.400 - 00:11:39.990` | the size, and a physical address, as |
| `00:11:40.000 - 00:11:43.070` | well as a collection of permissions. |
| `00:11:43.080 - 00:11:46.110` | And the virtual address, along with the |
| `00:11:46.120 - 00:11:48.870` | size, will determine how many virtual |
| `00:11:48.880 - 00:11:51.190` | pages are to be added to the virtual |
| `00:11:51.200 - 00:11:53.030` | address space. |
| `00:11:53.040 - 00:11:55.670` | And we assume that PA points to a |
| `00:11:55.680 - 00:11:58.390` | physical address where there are |
| `00:11:58.400 - 00:12:00.590` | the the same number of blocks of |
| `00:12:00.600 - 00:12:02.910` | physical memory uh |
| `00:12:02.920 - 00:12:06.230` | pre-allocated and and to be used. So, |
| `00:12:06.240 - 00:12:08.830` | what this function will do is it will |
| `00:12:08.840 - 00:12:11.830` | add page table entries so that each one |
| `00:12:11.840 - 00:12:14.670` | of these virtual uh pages points to the |
| `00:12:14.680 - 00:12:17.550` | corresponding physical page in memory. |
| `00:12:17.560 - 00:12:19.710` | And each page will have its permission |
| `00:12:19.720 - 00:12:22.830` | set identically according to this last |
| `00:12:22.840 - 00:12:25.310` | parameter, which can set the pages to |
| `00:12:25.320 - 00:12:27.430` | readable, writable, executable, or |
| `00:12:27.440 - 00:12:30.310` | accessible in user mode. |
| `00:12:30.320 - 00:12:33.910` | Okay, so here is the uh function for |
| `00:12:33.920 - 00:12:35.910` | uh map pages. |
| `00:12:35.920 - 00:12:37.829` | I want to start out by saying uh it |
| `00:12:37.839 - 00:12:39.030` | creates page table entries for the |
| `00:12:39.040 - 00:12:40.630` | virtual addresses starting at the |
| `00:12:40.640 - 00:12:42.030` | virtual address |
| `00:12:42.040 - 00:12:44.390` | um that refer to the physical addresses |
| `00:12:44.400 - 00:12:47.350` | starting at PA. |
| `00:12:47.360 - 00:12:50.430` | It says VA might not be page aligned. It |
| `00:12:50.440 - 00:12:53.270` | turns out the way that map pages is used |
| `00:12:53.280 - 00:12:54.630` | in XV6 |
| `00:12:54.640 - 00:12:57.270` | is such that VA is always exactly page |
| `00:12:57.280 - 00:12:58.430` | aligned. |
| `00:12:58.440 - 00:13:01.390` | Size is in bytes, uh and it returns zero |
| `00:13:01.400 - 00:13:02.670` | on success, minus one if there's a |
| `00:13:02.680 - 00:13:03.910` | problem. |
| `00:13:03.920 - 00:13:07.390` | Okay, so uh here we have the arguments, |
| `00:13:07.400 - 00:13:09.070` | pointer to the page table, a virtual |
| `00:13:09.080 - 00:13:11.230` | address, size in bytes, physical |
| `00:13:11.240 - 00:13:13.710` | address, and the permissions that these |
| `00:13:13.720 - 00:13:16.430` | pages should have. |
| `00:13:16.440 - 00:13:18.230` | We start by making sure that size is not |
| `00:13:18.240 - 00:13:21.190` | zero, so a quick error message there. |
| `00:13:21.200 - 00:13:23.230` | Um as I said, |
| `00:13:23.240 - 00:13:25.070` | the virtual address will always be page |
| `00:13:25.080 - 00:13:29.110` | aligned, so this call to page round down |
| `00:13:29.120 - 00:13:30.990` | will not actually do anything. |
| `00:13:31.000 - 00:13:33.510` | It will just copy the eight into the |
| `00:13:33.520 - 00:13:35.230` | local variable A. |
| `00:13:35.240 - 00:13:38.630` | And A will then increment through the |
| `00:13:38.640 - 00:13:41.870` | virtual pages one by one. |
| `00:13:41.880 - 00:13:45.870` | And PA will increment through the |
| `00:13:45.880 - 00:13:49.070` | physical pages one by one. So, |
| `00:13:49.080 - 00:13:52.070` | here's the loop executes once for each |
| `00:13:52.080 - 00:13:55.150` | page and we see at the bottom of it it's |
| `00:13:55.160 - 00:13:57.990` | incrementing A by page size and physical |
| `00:13:58.000 - 00:14:01.390` | address also by page size. |
| `00:14:01.400 - 00:14:04.590` | Before we start the loop, we compute the |
| `00:14:04.600 - 00:14:06.750` | address of the last page. So, we take |
| `00:14:06.760 - 00:14:08.710` | the virtual address plus size minus one. |
| `00:14:08.720 - 00:14:10.070` | So, that's the |
| `00:14:10.080 - 00:14:11.910` | last byte in the range of virtual |
| `00:14:11.920 - 00:14:14.470` | memory. And by rounding it down, we |
| `00:14:14.480 - 00:14:16.590` | determine which page that last byte is |
| `00:14:16.600 - 00:14:20.750` | on. So, here we check to see whether the |
| `00:14:20.760 - 00:14:22.830` | page that we just added was the last |
| `00:14:22.840 - 00:14:24.590` | page and if so, we terminate the loop |
| `00:14:24.600 - 00:14:27.670` | and then return zero. |
| `00:14:27.680 - 00:14:29.270` | So, what do we do for each one of these |
| `00:14:29.280 - 00:14:31.310` | virtual addresses? |
| `00:14:31.320 - 00:14:35.030` | We call walk to walk the page table |
| `00:14:35.040 - 00:14:37.550` | allocating any index pages as necessary. |
| `00:14:37.560 - 00:14:39.990` | So, we see that the alloc parameter to |
| `00:14:40.000 - 00:14:41.950` | walk is set to true. |
| `00:14:41.960 - 00:14:44.150` | And if there's a problem and that |
| `00:14:44.160 - 00:14:46.310` | returns null, then we immediately return |
| `00:14:46.320 - 00:14:48.270` | minus one. But otherwise, we get a |
| `00:14:48.280 - 00:14:51.190` | pointer to the page table entry. PTE |
| `00:14:51.200 - 00:14:53.510` | here is a pointer to the page table |
| `00:14:53.520 - 00:14:54.590` | entry. |
| `00:14:54.600 - 00:14:56.790` | That page table entry had better not be |
| `00:14:56.800 - 00:14:58.470` | valid. So, here what we're doing is |
| `00:14:58.480 - 00:15:01.070` | we're testing the valid bit. |
| `00:15:01.080 - 00:15:04.350` | And if that is set to true, then we call |
| `00:15:04.360 - 00:15:06.310` | panic and there's a problem. |
| `00:15:06.320 - 00:15:08.350` | But otherwise, we |
| `00:15:08.360 - 00:15:10.070` | call this |
| `00:15:10.080 - 00:15:14.790` | function PA to PTE. And that's a macro |
| `00:15:14.800 - 00:15:16.510` | preprocessor function. |
| `00:15:16.520 - 00:15:18.230` | And that takes the physical address and |
| `00:15:18.240 - 00:15:18.990` | it |
| `00:15:19.000 - 00:15:21.390` | builds the page table entry. |
| `00:15:21.400 - 00:15:22.630` | So, uh |
| `00:15:22.640 - 00:15:25.470` | here is a picture of the uh physical |
| `00:15:25.480 - 00:15:26.670` | address |
| `00:15:26.680 - 00:15:29.310` | and if it's a page-aligned address, |
| `00:15:29.320 - 00:15:31.550` | which PA will be, then the offset will |
| `00:15:31.560 - 00:15:33.230` | be all zeros. |
| `00:15:33.240 - 00:15:35.590` | At PA physical address to page table |
| `00:15:35.600 - 00:15:38.070` | entry will ignore these bits anyway, but |
| `00:15:38.080 - 00:15:39.950` | what it'll do is it'll extract the 44 |
| `00:15:39.960 - 00:15:42.310` | bits here, shift them, and it'll set |
| `00:15:42.320 - 00:15:45.510` | these bits to zero. So, these are the um |
| `00:15:45.520 - 00:15:47.470` | read, write, executable, valid, and the |
| `00:15:47.480 - 00:15:50.230` | user bit. And those are all set to zero |
| `00:15:50.240 - 00:15:53.750` | uh by PA to PTE. |
| `00:15:53.760 - 00:15:56.310` | Uh and then we OR in |
| `00:15:56.320 - 00:15:57.750` | There we go. Then we OR in the |
| `00:15:57.760 - 00:15:59.150` | permissions that we are supposed to |
| `00:15:59.160 - 00:16:01.350` | have, that is from the last parameter |
| `00:16:01.360 - 00:16:02.750` | perm here, |
| `00:16:02.760 - 00:16:06.350` | as well as setting the valid bit. And |
| `00:16:06.360 - 00:16:07.990` | then we store |
| `00:16:08.000 - 00:16:11.870` | this newly-built page table entry in the |
| `00:16:11.880 - 00:16:14.390` | uh level zero index page where uh it's |
| `00:16:14.400 - 00:16:15.750` | supposed to go. |
| `00:16:15.760 - 00:16:17.110` | And then we just repeat that for each |
| `00:16:17.120 - 00:16:19.870` | one of the virtual pages, and finally uh |
| `00:16:19.880 - 00:16:23.470` | exit and return zero. |
| `00:16:23.480 - 00:16:24.310` | Next, we're going to look at the |
| `00:16:24.320 - 00:16:26.310` | functions that build the kernel's page |
| `00:16:26.320 - 00:16:30.470` | table, and they call map pages, but they |
| `00:16:30.480 - 00:16:32.870` | use this little wrapper function KVM |
| `00:16:32.880 - 00:16:36.230` | map. All it does is call map pages, and |
| `00:16:36.240 - 00:16:37.750` | you can see that the parameters are the |
| `00:16:37.760 - 00:16:39.990` | same, the page table, the virtual |
| `00:16:40.000 - 00:16:43.030` | address, the physical address, the size. |
| `00:16:43.040 - 00:16:45.190` | Okay, they're not in the same order, and |
| `00:16:45.200 - 00:16:46.510` | the permissions. |
| `00:16:46.520 - 00:16:48.190` | And if there is a problem with map |
| `00:16:48.200 - 00:16:50.390` | pages, this thing immediately invokes |
| `00:16:50.400 - 00:16:53.190` | panic. |
| `00:16:53.200 - 00:16:54.670` | Next, let's take a look at the function |
| `00:16:54.680 - 00:16:57.190` | KVM make, which is going to create and |
| `00:16:57.200 - 00:17:00.070` | initialize the kernel's page table. |
| `00:17:00.080 - 00:17:04.150` | It will call KVM map to do the work, and |
| `00:17:04.160 - 00:17:05.350` | that's a little wrapper function that we |
| `00:17:05.360 - 00:17:07.150` | just looked at that does nothing more |
| `00:17:07.160 - 00:17:09.710` | than call map pages. |
| `00:17:09.720 - 00:17:12.510` | This function will create direct |
| `00:17:12.520 - 00:17:14.710` | mappings for the physical memory, the IO |
| `00:17:14.720 - 00:17:17.470` | devices, and it will also map the |
| `00:17:17.480 - 00:17:20.949` | trampoline page, and it will create uh |
| `00:17:20.959 - 00:17:23.710` | mappings for pages for the |
| `00:17:23.720 - 00:17:25.870` | uh stacks for each of the different |
| `00:17:25.880 - 00:17:28.110` | processes. I'll I'll talk more about |
| `00:17:28.120 - 00:17:30.590` | that in a second. But, basically, uh if |
| `00:17:30.600 - 00:17:32.350` | you remember this picture here, it's |
| `00:17:32.360 - 00:17:35.870` | going to create the mappings that are |
| `00:17:35.880 - 00:17:38.310` | described here. This is |
| `00:17:38.320 - 00:17:40.470` | physical main memory here, and this is |
| `00:17:40.480 - 00:17:43.030` | the kernel's virtual address space. |
| `00:17:43.040 - 00:17:46.510` | We see the uh IO devices here. |
| `00:17:46.520 - 00:17:50.150` | We see the kernel's uh code and data, |
| `00:17:50.160 - 00:17:53.350` | and we see the physical RAM here, and |
| `00:17:53.360 - 00:17:55.670` | those are direct mapped, and uh we see |
| `00:17:55.680 - 00:17:57.910` | some other stuff. So, let's get uh into |
| `00:17:57.920 - 00:17:59.470` | the code here. |
| `00:17:59.480 - 00:18:03.190` | So, here is the code for KVM make. |
| `00:18:03.200 - 00:18:04.950` | It's going to |
| `00:18:04.960 - 00:18:06.910` | uh |
| `00:18:06.920 - 00:18:08.030` | re- |
| `00:18:08.040 - 00:18:09.670` | create the page table. So, here it |
| `00:18:09.680 - 00:18:12.710` | allocates the top-most index page, the |
| `00:18:12.720 - 00:18:14.710` | level two index page, |
| `00:18:14.720 - 00:18:17.870` | and with memset, it zeros it out, so it |
| `00:18:17.880 - 00:18:22.350` | has no entries, and uh it sets uh that |
| `00:18:22.360 - 00:18:25.150` | local variable uh kernel's page table, |
| `00:18:25.160 - 00:18:29.230` | and that will be used in all the |
| `00:18:29.240 - 00:18:31.990` | calls to KVM map. |
| `00:18:32.000 - 00:18:34.270` | the uh final bit of this function, it |
| `00:18:34.280 - 00:18:36.550` | will return a pointer to that. |
| `00:18:36.560 - 00:18:38.470` | So, let's um |
| `00:18:38.480 - 00:18:40.870` | see here, we've created the uh top-level |
| `00:18:40.880 - 00:18:44.110` | index page and zeroed it out, and then |
| `00:18:44.120 - 00:18:46.990` | we start calling KVM map to add the |
| `00:18:47.000 - 00:18:49.150` | mappings. The first one is for the |
| `00:18:49.160 - 00:18:51.470` | serial device, the uh universal |
| `00:18:51.480 - 00:18:54.710` | asynchronous receive transmit unit, and |
| `00:18:54.720 - 00:18:56.550` | it's direct mapped, so the virtual |
| `00:18:56.560 - 00:18:58.830` | address is here, the physical address is |
| `00:18:58.840 - 00:19:02.630` | there, and it's only one page in size, |
| `00:19:02.640 - 00:19:04.950` | so the page size is just the 4K page |
| `00:19:04.960 - 00:19:05.910` | size, |
| `00:19:05.920 - 00:19:07.350` | and it will be marked readable and |
| `00:19:07.360 - 00:19:09.310` | writable. These are the permissions. |
| `00:19:09.320 - 00:19:13.230` | And uh so, that creates this mapping, |
| `00:19:13.240 - 00:19:15.230` | this mapping here. |
| `00:19:15.240 - 00:19:18.710` | And then, in the next line, we, uh, |
| `00:19:18.720 - 00:19:21.150` | create the mapping for the disk device. |
| `00:19:21.160 - 00:19:23.670` | Again, one page, readable and writable. |
| `00:19:23.680 - 00:19:27.070` | And so, that's the disk page here, uh, |
| `00:19:27.080 - 00:19:29.550` | single page over in physical, uh, the |
| `00:19:29.560 - 00:19:31.950` | physical address space. And then next, |
| `00:19:31.960 - 00:19:33.830` | we have the, uh, platform level |
| `00:19:33.840 - 00:19:36.710` | interrupt controller. Um, and it's |
| `00:19:36.720 - 00:19:39.830` | mapped direct. Its size is a little bit |
| `00:19:39.840 - 00:19:42.710` | larger. It's it's in fact 4 MB. |
| `00:19:42.720 - 00:19:45.390` | It's direct mapped into its addresses. |
| `00:19:45.400 - 00:19:47.670` | Again, readable and writable. |
| `00:19:47.680 - 00:19:49.390` | Um, now, let's take a look at the |
| `00:19:49.400 - 00:19:51.310` | kernel. Uh, the kernel has been loaded |
| `00:19:51.320 - 00:19:54.190` | into physical memory, and |
| `00:19:54.200 - 00:19:55.870` | um, the address at which it's loaded is |
| `00:19:55.880 - 00:19:59.190` | called, uh, kernel base. And there's |
| `00:19:59.200 - 00:20:01.550` | another address called e-text, which is |
| `00:20:01.560 - 00:20:05.470` | where the data begins. And so, uh, first |
| `00:20:05.480 - 00:20:07.470` | of all, we create a mapping for the |
| `00:20:07.480 - 00:20:10.910` | code, uh, portion, which is readable and |
| `00:20:10.920 - 00:20:13.190` | executable, but not writable. |
| `00:20:13.200 - 00:20:15.590` | And that goes from kernel base to, |
| `00:20:15.600 - 00:20:18.270` | uh, this address here, e-text. So, |
| `00:20:18.280 - 00:20:20.270` | that's what's going on here. Uh, the |
| `00:20:20.280 - 00:20:22.990` | virtual address and the physical address |
| `00:20:23.000 - 00:20:24.470` | are one in the same. It's a direct |
| `00:20:24.480 - 00:20:27.390` | mapping, and the size is e-text minus |
| `00:20:27.400 - 00:20:29.830` | the kernel base. And again, it's |
| `00:20:29.840 - 00:20:31.750` | readable and executable. |
| `00:20:31.760 - 00:20:34.270` | Uh, then, the remaining the remainder of |
| `00:20:34.280 - 00:20:38.390` | physical memory, uh, from here on up to |
| `00:20:38.400 - 00:20:41.830` | the top of physical memory, versus top, |
| `00:20:41.840 - 00:20:44.510` | um, will be mapped readable and |
| `00:20:44.520 - 00:20:45.830` | writable. And that will include the |
| `00:20:45.840 - 00:20:48.670` | kernel's data, as well as, um, the |
| `00:20:48.680 - 00:20:50.830` | remaining memory, which can be used for |
| `00:20:50.840 - 00:20:53.310` | the, which will be used by the kalloc |
| `00:20:53.320 - 00:20:56.630` | and kfree routines. So, um, |
| `00:20:56.640 - 00:20:58.470` | here we're mapping, |
| `00:20:58.480 - 00:21:01.390` | uh, that. We're giving the |
| `00:21:01.400 - 00:21:03.710` | virtual address as e-text. The physical |
| `00:21:03.720 - 00:21:07.110` | address is the same, and the size is, |
| `00:21:07.120 - 00:21:09.430` | uh, the from the top of memory down to |
| `00:21:09.440 - 00:21:10.670` | e-text. |
| `00:21:10.680 - 00:21:13.030` | And it's readable and writable. |
| `00:21:13.040 - 00:21:15.790` | Next, we're mapping the trampoline page. |
| `00:21:15.800 - 00:21:19.070` | And this is not direct map. Instead, um |
| `00:21:19.080 - 00:21:20.630` | the virtual address will be trampoline, |
| `00:21:20.640 - 00:21:24.670` | which is the address of the highest page |
| `00:21:24.680 - 00:21:27.150` | in the virtual address space. So, it's |
| `00:21:27.160 - 00:21:28.990` | this this address |
| `00:21:29.000 - 00:21:30.230` | right here. |
| `00:21:30.240 - 00:21:33.310` | And it will be mapped to the physical |
| `00:21:33.320 - 00:21:36.750` | address of where the trampoline code is. |
| `00:21:36.760 - 00:21:40.790` | Um and uh it's a single page, readable |
| `00:21:40.800 - 00:21:43.190` | and executable. |
| `00:21:43.200 - 00:21:45.870` | Okay, uh next uh going to the next page, |
| `00:21:45.880 - 00:21:49.030` | uh we see uh the last little bit of this |
| `00:21:49.040 - 00:21:53.110` | function we call proc_map_stacks. |
| `00:21:53.120 - 00:21:55.190` | Now, let me go back to this page here, |
| `00:21:55.200 - 00:22:00.070` | or this diagram shows over here |
| `00:22:00.080 - 00:22:02.590` | that this is the virtual address space |
| `00:22:02.600 - 00:22:06.430` | of the kernel's address space. And we |
| `00:22:06.440 - 00:22:08.670` | see in the topmost page, the trampoline |
| `00:22:08.680 - 00:22:09.910` | page. |
| `00:22:09.920 - 00:22:12.830` | And then we have a number of pages, one |
| `00:22:12.840 - 00:22:15.990` | for each of the 64 processes, and these |
| `00:22:16.000 - 00:22:18.430` | will hold the stacks for each one of the |
| `00:22:18.440 - 00:22:19.950` | processes. |
| `00:22:19.960 - 00:22:22.110` | They are each a page in length, and |
| `00:22:22.120 - 00:22:24.230` | they're each separated by a single guard |
| `00:22:24.240 - 00:22:26.030` | page. |
| `00:22:26.040 - 00:22:27.750` | We have a preprocessor function called |
| `00:22:27.760 - 00:22:29.390` | kstack. |
| `00:22:29.400 - 00:22:32.310` | And it's passed a number 0 through 63, |
| `00:22:32.320 - 00:22:34.910` | and it will return the address |
| `00:22:34.920 - 00:22:36.310` | that is the virtual address of this |
| `00:22:36.320 - 00:22:38.070` | page. So, for example, if we call it |
| `00:22:38.080 - 00:22:40.710` | with two, it would return this address |
| `00:22:40.720 - 00:22:42.310` | this virtual address right here. If we |
| `00:22:42.320 - 00:22:43.910` | call it with one, it will return this |
| `00:22:43.920 - 00:22:46.550` | address here. So, it tells us where |
| `00:22:46.560 - 00:22:49.430` | these pages go in the virtual address |
| `00:22:49.440 - 00:22:50.670` | space. |
| `00:22:50.680 - 00:22:53.350` | So, now we're ready to So, we In this |
| `00:22:53.360 - 00:22:57.030` | function, we call proc_map_stacks. |
| `00:22:57.040 - 00:22:58.510` | Up until now, all this code has been |
| `00:22:58.520 - 00:23:00.590` | coming from vm.c. |
| `00:23:00.600 - 00:23:03.550` | But, proc uh sorry, proc_map_stacks |
| `00:23:03.560 - 00:23:05.870` | is in the uh |
| `00:23:05.880 - 00:23:08.230` | file proc.c. |
| `00:23:08.240 - 00:23:11.790` | So, here it is. Okay, so it's passed a |
| `00:23:11.800 - 00:23:14.270` | pointer to the kernel's page table. It's |
| `00:23:14.280 - 00:23:15.790` | not going to return anything. It's just |
| `00:23:15.800 - 00:23:18.230` | going to update that page table. |
| `00:23:18.240 - 00:23:20.470` | So, it's going to |
| `00:23:20.480 - 00:23:23.510` | iterate uh the number of processes |
| `00:23:23.520 - 00:23:26.630` | happens to be 64. And we've got this |
| `00:23:26.640 - 00:23:28.470` | proc array with a |
| `00:23:28.480 - 00:23:30.590` | pointer which is a number of proc |
| `00:23:30.600 - 00:23:32.550` | structures. And we're basically just |
| `00:23:32.560 - 00:23:33.870` | going to alloc |
| `00:23:33.880 - 00:23:36.230` | to iterate through that array. So, |
| `00:23:36.240 - 00:23:37.750` | that's what's going on with this loop |
| `00:23:37.760 - 00:23:40.110` | here. We're iterating through each of |
| `00:23:40.120 - 00:23:43.150` | the 64 proc structures. And for each one |
| `00:23:43.160 - 00:23:45.710` | of them, we're going to allocate a page, |
| `00:23:45.720 - 00:23:47.710` | okay, in physical memory. That is, we're |
| `00:23:47.720 - 00:23:50.590` | going to allocate a page in the Kalloc |
| `00:23:50.600 - 00:23:52.790` | region somewhere, a physical page, for |
| `00:23:52.800 - 00:23:54.150` | the stack. |
| `00:23:54.160 - 00:23:55.630` | But then we're going to map it somewhere |
| `00:23:55.640 - 00:23:57.670` | up here. And here we see that same |
| `00:23:57.680 - 00:24:00.430` | picture with the trampoline page and the |
| `00:24:00.440 - 00:24:01.350` | uh |
| `00:24:01.360 - 00:24:04.230` | stack pages separated by guard pages. |
| `00:24:04.240 - 00:24:06.950` | Okay, so what we're going to grab a |
| `00:24:06.960 - 00:24:09.510` | physical page, okay, from the free |
| `00:24:09.520 - 00:24:11.710` | memory, so up here somewhere. |
| `00:24:11.720 - 00:24:13.590` | And then we're going to create a mapping |
| `00:24:13.600 - 00:24:16.550` | from that to there. Now, these arrows |
| `00:24:16.560 - 00:24:18.510` | here, um I don't like these arrows |
| `00:24:18.520 - 00:24:20.950` | because they're pointing someplace else. |
| `00:24:20.960 - 00:24:22.510` | They actually should be pointing into |
| `00:24:22.520 - 00:24:25.550` | the uh free memory region here. |
| `00:24:25.560 - 00:24:29.990` | Okay, so uh here we are allocating for |
| `00:24:30.000 - 00:24:33.750` | each proc for each uh process |
| `00:24:33.760 - 00:24:37.950` | uh a a page. And then uh if there's a |
| `00:24:37.960 - 00:24:43.110` | problem, we panic. But if not, we um |
| `00:24:43.120 - 00:24:45.430` | This This actually computes the number |
| `00:24:45.440 - 00:24:47.830` | um here. It's kind of a funny way C |
| `00:24:47.840 - 00:24:49.790` | works, but uh we're computing the number |
| `00:24:49.800 - 00:24:52.030` | 0 to 63 and we're calling Kstack to get |
| `00:24:52.040 - 00:24:54.030` | the address, that is, the virtual |
| `00:24:54.040 - 00:24:56.230` | address where we want to map it. And |
| `00:24:56.240 - 00:24:57.550` | then we call |
| `00:24:57.560 - 00:24:58.910` | KVM map |
| `00:24:58.920 - 00:25:00.390` | giving it the virtual address that we |
| `00:25:00.400 - 00:25:02.510` | just computed, as well as the physical |
| `00:25:02.520 - 00:25:05.310` | address of the page itself. Uh it's one |
| `00:25:05.320 - 00:25:07.110` | page in length, and it will be readable |
| `00:25:07.120 - 00:25:08.550` | and writable. |
| `00:25:08.560 - 00:25:13.110` | Okay, that wraps it up for KVM make. |
| `00:25:13.120 - 00:25:14.550` | Next, let's take a look at two short |
| `00:25:14.560 - 00:25:18.710` | functions, KVM init and KVM init heart. |
| `00:25:18.720 - 00:25:19.870` | I'll just go straight to the code on |
| `00:25:19.880 - 00:25:24.910` | these. |
| `00:25:24.920 - 00:25:28.390` | KVM init is called once by core zero |
| `00:25:28.400 - 00:25:30.070` | during initialization. So, it's only |
| `00:25:30.080 - 00:25:32.990` | called by one core, and it calls KVM |
| `00:25:33.000 - 00:25:36.230` | make to create the kernel's page table |
| `00:25:36.240 - 00:25:39.150` | and saves a pointer to it in this global |
| `00:25:39.160 - 00:25:41.550` | variable kernel page table. |
| `00:25:41.560 - 00:25:43.510` | Then, when during as part of the |
| `00:25:43.520 - 00:25:46.590` | initialization process, each core will |
| `00:25:46.600 - 00:25:50.870` | call KVM init heart. And what that will |
| `00:25:50.880 - 00:25:54.750` | do is it will set the control and status |
| `00:25:54.760 - 00:25:57.030` | register called SATP. So, this is |
| `00:25:57.040 - 00:26:00.230` | writing to that SATP register |
| `00:26:00.240 - 00:26:03.550` | the address of the kernel's page table. |
| `00:26:03.560 - 00:26:06.910` | That will effectively turn on paging and |
| `00:26:06.920 - 00:26:09.990` | install the page table, if you will. |
| `00:26:10.000 - 00:26:13.470` | Make SATP um is just a little thing that |
| `00:26:13.480 - 00:26:15.430` | mangles this pointer a little bit into |
| `00:26:15.440 - 00:26:18.430` | the format that the uh RISC-V processor |
| `00:26:18.440 - 00:26:19.750` | requires. |
| `00:26:19.760 - 00:26:22.590` | And we also see uh fence instruction to |
| `00:26:22.600 - 00:26:25.830` | make sure that uh anything that we do |
| `00:26:25.840 - 00:26:28.230` | that we should do before we uh change |
| `00:26:28.240 - 00:26:29.870` | this control status register is actually |
| `00:26:29.880 - 00:26:31.990` | done before that, and anything that must |
| `00:26:32.000 - 00:26:33.870` | be done after that is is actually done |
| `00:26:33.880 - 00:26:36.550` | after that. |
| `00:26:36.560 - 00:26:38.550` | Next, let's look at this walk address |
| `00:26:38.560 - 00:26:39.790` | function. |
| `00:26:39.800 - 00:26:42.350` | It's passed a page table, and it uses |
| `00:26:42.360 - 00:26:45.390` | that to map a virtual address into a |
| `00:26:45.400 - 00:26:47.030` | physical address. |
| `00:26:47.040 - 00:26:50.550` | Uh the virtual address must be valid, |
| `00:26:50.560 - 00:26:54.590` | and it must have user permission set. |
| `00:26:54.600 - 00:26:56.470` | Uh this virtual address has to be a |
| `00:26:56.480 - 00:26:59.350` | page-aligned address, and it returns the |
| `00:26:59.360 - 00:27:01.230` | address of the physical page where |
| `00:27:01.240 - 00:27:03.350` | that's located in memory. And if there |
| `00:27:03.360 - 00:27:06.070` | is any problem, it returns zero. So, now |
| `00:27:06.080 - 00:27:08.310` | let's look at the code for uh walk |
| `00:27:08.320 - 00:27:09.910` | address here. |
| `00:27:09.920 - 00:27:12.390` | Um and you see we we have an error |
| `00:27:12.400 - 00:27:14.510` | check. If the virtual address is too |
| `00:27:14.520 - 00:27:16.830` | large, we just return zero. |
| `00:27:16.840 - 00:27:19.150` | Then we call the walk function with the |
| `00:27:19.160 - 00:27:22.590` | virtual address to get a pointer to the |
| `00:27:22.600 - 00:27:26.150` | page table entry. Uh we do not allocate |
| `00:27:26.160 - 00:27:29.230` | uh index pages if they don't exist. |
| `00:27:29.240 - 00:27:32.470` | Um if it returns something valid, then |
| `00:27:32.480 - 00:27:34.230` | okay, we proceed. But if it returns |
| `00:27:34.240 - 00:27:37.590` | null, we immediately return zero. |
| `00:27:37.600 - 00:27:39.630` | Then we check the valid bit to make sure |
| `00:27:39.640 - 00:27:41.910` | that it is set. If it's not set, we |
| `00:27:41.920 - 00:27:46.430` | return zero. And we check the um bit to |
| `00:27:46.440 - 00:27:48.710` | see if it's accessible if that page is |
| `00:27:48.720 - 00:27:51.790` | accessible in user mode. And if that is |
| `00:27:51.800 - 00:27:54.070` | not set, again we return zero. And |
| `00:27:54.080 - 00:27:57.510` | finally, we call this uh little PTE to |
| `00:27:57.520 - 00:28:00.190` | physical address function. Uh we uh |
| `00:28:00.200 - 00:28:03.510` | retrieve the page table entry here and |
| `00:28:03.520 - 00:28:05.430` | uh extract the |
| `00:28:05.440 - 00:28:08.510` | pointer to the physical page and return |
| `00:28:08.520 - 00:28:11.870` | the physical address. |
| `00:28:11.880 - 00:28:14.230` | The function UVM create is used to |
| `00:28:14.240 - 00:28:17.670` | create and return an empty virtual |
| `00:28:17.680 - 00:28:19.070` | address space. So, it creates a page |
| `00:28:19.080 - 00:28:21.750` | table with only the root, that is the |
| `00:28:21.760 - 00:28:24.670` | level two index page. |
| `00:28:24.680 - 00:28:28.630` | So, here's the code. |
| `00:28:28.640 - 00:28:30.830` | UVM create returns a pointer to a page |
| `00:28:30.840 - 00:28:31.870` | table. |
| `00:28:31.880 - 00:28:34.350` | Uh it's only used to create virtual |
| `00:28:34.360 - 00:28:37.310` | address spaces for uh user processes, so |
| `00:28:37.320 - 00:28:39.670` | that's why it's called UVM create. And |
| `00:28:39.680 - 00:28:42.470` | you can see it allocates a page here, |
| `00:28:42.480 - 00:28:44.830` | and that will be the level two index |
| `00:28:44.840 - 00:28:47.110` | page, the root page. If there's a |
| `00:28:47.120 - 00:28:49.790` | problem, it returns null. |
| `00:28:49.800 - 00:28:53.150` | Otherwise, it calls memset to initialize |
| `00:28:53.160 - 00:28:55.310` | all the page table entries in the root |
| `00:28:55.320 - 00:28:57.470` | index page to zero |
| `00:28:57.480 - 00:29:02.630` | and returns a pointer to that page. |
| `00:29:02.640 - 00:29:05.430` | The UVM init function is used to create |
| `00:29:05.440 - 00:29:07.750` | the virtual address space for the first |
| `00:29:07.760 - 00:29:10.030` | process. |
| `00:29:10.040 - 00:29:13.590` | So, the first process will contain code |
| `00:29:13.600 - 00:29:16.390` | that comes from init code.s. It's |
| `00:29:16.400 - 00:29:17.670` | assembly code. |
| `00:29:17.680 - 00:29:20.710` | But basically, it's a very short program |
| `00:29:20.720 - 00:29:24.110` | that does an exact system call and |
| `00:29:24.120 - 00:29:26.870` | invokes another program and gets things |
| `00:29:26.880 - 00:29:27.990` | rolling. |
| `00:29:28.000 - 00:29:30.790` | Uh this assembly language program is |
| `00:29:30.800 - 00:29:34.110` | assembled separately and we extract all |
| `00:29:34.120 - 00:29:37.750` | the bytes. There are actually 52 bytes. |
| `00:29:37.760 - 00:29:41.550` | And in hex, they are specified directly |
| `00:29:41.560 - 00:29:44.710` | in the kernel code in this array of |
| `00:29:44.720 - 00:29:48.230` | bytes called init code. So, what this |
| `00:29:48.240 - 00:29:50.630` | function is going to do is allocate a |
| `00:29:50.640 - 00:29:52.310` | single page |
| `00:29:52.320 - 00:29:54.750` | and it's going to then copy these bytes |
| `00:29:54.760 - 00:29:56.510` | into that page. |
| `00:29:56.520 - 00:29:59.630` | Uh so, it takes a parameter, source and |
| `00:29:59.640 - 00:30:03.030` | size, which points to these bytes and |
| `00:30:03.040 - 00:30:06.070` | the size is the 52, the number of them. |
| `00:30:06.080 - 00:30:09.070` | And page table. And it will allocate one |
| `00:30:09.080 - 00:30:12.110` | page, map it into virtual address zero |
| `00:30:12.120 - 00:30:14.790` | with read, write, executable, and user |
| `00:30:14.800 - 00:30:16.310` | mode |
| `00:30:16.320 - 00:30:20.110` | permissions. And that then that |
| `00:30:20.120 - 00:30:23.070` | program has then been loaded into the |
| `00:30:23.080 - 00:30:25.630` | first page of the virtual address space. |
| `00:30:25.640 - 00:30:27.110` | So, here let's take a look at the code |
| `00:30:27.120 - 00:30:31.310` | for UVM init. |
| `00:30:31.320 - 00:30:33.030` | Uh we see it's passed a pointer to a |
| `00:30:33.040 - 00:30:36.670` | page table and a pointer to the bytes, |
| `00:30:36.680 - 00:30:39.670` | the 52 bytes, as long along with their |
| `00:30:39.680 - 00:30:42.990` | size. And what does it do? It makes sure |
| `00:30:43.000 - 00:30:44.990` | that they're going to fit into a single |
| `00:30:45.000 - 00:30:48.430` | page here, and if not, it panics. And |
| `00:30:48.440 - 00:30:50.630` | then it allocates a page of physical |
| `00:30:50.640 - 00:30:52.710` | memory by calling KAlloc. |
| `00:30:52.720 - 00:30:54.110` | And |
| `00:30:54.120 - 00:30:58.150` | it sets all of that page to zeros. |
| `00:30:58.160 - 00:31:02.310` | And then it maps that page. So here is |
| `00:31:02.320 - 00:31:04.870` | the pointer, the physical address of the |
| `00:31:04.880 - 00:31:07.310` | page that we just allocated here. |
| `00:31:07.320 - 00:31:10.790` | And it's size is zero It is page size |
| `00:31:10.800 - 00:31:12.790` | here, and we're mapping it into virtual |
| `00:31:12.800 - 00:31:16.790` | address zero with the call to map pages. |
| `00:31:16.800 - 00:31:19.150` | And we're passing it the permissions of |
| `00:31:19.160 - 00:31:23.110` | write, read, executable, and accessible |
| `00:31:23.120 - 00:31:26.270` | in user mode. And finally, |
| `00:31:26.280 - 00:31:29.070` | we are moving |
| `00:31:29.080 - 00:31:31.550` | this number this these 52 bytes from |
| `00:31:31.560 - 00:31:33.830` | wherever they are in this array, this |
| `00:31:33.840 - 00:31:35.630` | initcode array. |
| `00:31:35.640 - 00:31:37.990` | We're moving them into the page that we |
| `00:31:38.000 - 00:31:40.190` | just allocated. So here we're moving |
| `00:31:40.200 - 00:31:42.270` | them from source to |
| `00:31:42.280 - 00:31:44.790` | that page. |
| `00:31:44.800 - 00:31:46.870` | By the way, the virtual address space |
| `00:31:46.880 - 00:31:49.430` | for the initial process is quite |
| `00:31:49.440 - 00:31:51.270` | different from the virtual address |
| `00:31:51.280 - 00:31:54.150` | spaces for all the other processes. We |
| `00:31:54.160 - 00:31:56.710` | sort of cheat a little bit. Recall that |
| `00:31:56.720 - 00:31:58.990` | a virtual address space normally looks |
| `00:31:59.000 - 00:32:00.590` | like this with the |
| `00:32:00.600 - 00:32:03.990` | stack down here and code and heap and |
| `00:32:04.000 - 00:32:06.750` | and so on. Well, with the initial |
| `00:32:06.760 - 00:32:10.870` | process, we just have a single page |
| `00:32:10.880 - 00:32:13.110` | allocated at location zero in the |
| `00:32:13.120 - 00:32:14.670` | virtual address space. So we only have |
| `00:32:14.680 - 00:32:18.910` | one page, and we load into that page the |
| `00:32:18.920 - 00:32:21.510` | code for the initial process. |
| `00:32:21.520 - 00:32:24.270` | That's those 52 bytes from the |
| `00:32:24.280 - 00:32:27.150` | initcode.s assembly code. |
| `00:32:27.160 - 00:32:29.950` | And we also use that same page for the |
| `00:32:29.960 - 00:32:32.030` | stack. Of course, every process will |
| `00:32:32.040 - 00:32:34.350` | need a stack, and we will position the |
| `00:32:34.360 - 00:32:36.630` | stack pointer at the very top of that |
| `00:32:36.640 - 00:32:39.750` | page. So, the stack doesn't have any |
| `00:32:39.760 - 00:32:41.750` | more than one page to grow downward. Of |
| `00:32:41.760 - 00:32:44.390` | course, it really won't need that. But, |
| `00:32:44.400 - 00:32:46.310` | in any case, that's how we set up this |
| `00:32:46.320 - 00:32:47.830` | initial |
| `00:32:47.840 - 00:32:50.470` | process's virtual address space. |
| `00:32:50.480 - 00:32:52.110` | It's got a single page marked read, |
| `00:32:52.120 - 00:32:53.750` | write, executable, and accessible in |
| `00:32:53.760 - 00:32:55.750` | user mode. And the vast majority of that |
| `00:32:55.760 - 00:32:58.030` | space is just unallocated. Of course, |
| `00:32:58.040 - 00:33:00.070` | every virtual address space for user |
| `00:33:00.080 - 00:33:01.590` | process will need a trampoline and a |
| `00:33:01.600 - 00:33:04.990` | trap frame page. |
| `00:33:05.000 - 00:33:07.350` | Now, let's take a look at UVM alloc, |
| `00:33:07.360 - 00:33:09.510` | which is used to grow a virtual address |
| `00:33:09.520 - 00:33:10.830` | space. |
| `00:33:10.840 - 00:33:12.030` | Here's a picture of a virtual address |
| `00:33:12.040 - 00:33:15.750` | space. And as the process executes, it |
| `00:33:15.760 - 00:33:18.430` | may want to enlarge its heap area. We |
| `00:33:18.440 - 00:33:21.390` | have this point called a break point. |
| `00:33:21.400 - 00:33:23.110` | More precisely, it's the address of the |
| `00:33:23.120 - 00:33:27.270` | first byte beyond the heap. So, since |
| `00:33:27.280 - 00:33:28.630` | the virtual address space starts at |
| `00:33:28.640 - 00:33:29.710` | zero, |
| `00:33:29.720 - 00:33:31.910` | the break is actually nothing more than |
| `00:33:31.920 - 00:33:35.110` | the size of bytes that we've allocated. |
| `00:33:35.120 - 00:33:37.590` | And what this function is going to do is |
| `00:33:37.600 - 00:33:38.830` | going to |
| `00:33:38.840 - 00:33:42.550` | allow us to grow the size of the virtual |
| `00:33:42.560 - 00:33:44.110` | address space. So, we're going to go |
| `00:33:44.120 - 00:33:46.590` | from some old size to some larger new |
| `00:33:46.600 - 00:33:48.390` | size. |
| `00:33:48.400 - 00:33:49.790` | The |
| `00:33:49.800 - 00:33:52.110` | break point need not be page aligned. |
| `00:33:52.120 - 00:33:54.870` | And so, here I'm showing an old size |
| `00:33:54.880 - 00:33:56.350` | that happens to fall in the middle of a |
| `00:33:56.360 - 00:33:59.910` | page. And we might want to grow it. The |
| `00:33:59.920 - 00:34:02.710` | user might want to grow it to some new |
| `00:34:02.720 - 00:34:05.870` | location, to some new size. It's also |
| `00:34:05.880 - 00:34:07.910` | not page aligned. |
| `00:34:07.920 - 00:34:10.869` | So, what we're going to have to do is |
| `00:34:10.879 - 00:34:13.629` | we're going to have to increase this old |
| `00:34:13.639 - 00:34:16.070` | size to the next page boundary. And then |
| `00:34:16.080 - 00:34:17.590` | we're going to say, "Is it less than the |
| `00:34:17.600 - 00:34:19.310` | new size?" No. So, we're going to have |
| `00:34:19.320 - 00:34:21.629` | to allocate this page here, this page |
| `00:34:21.639 - 00:34:22.869` | right here. |
| `00:34:22.879 - 00:34:25.070` | And then is it |
| `00:34:25.080 - 00:34:27.350` | increment it to the next page boundary? |
| `00:34:27.360 - 00:34:29.190` | And we're going to say, "Is that |
| `00:34:29.200 - 00:34:31.470` | Have we exceeded new yet?" No, we |
| `00:34:31.480 - 00:34:32.590` | haven't. So, we're going to have to |
| `00:34:32.600 - 00:34:34.590` | allocate this page. And so, we're going |
| `00:34:34.600 - 00:34:37.350` | to allocate two pages here. The new size |
| `00:34:37.360 - 00:34:40.149` | will still be uh set to here, but we |
| `00:34:40.159 - 00:34:42.629` | will go ahead and allocate two pages. |
| `00:34:42.639 - 00:34:45.350` | So, that's what UVM alloc does. |
| `00:34:45.360 - 00:34:47.430` | Pass it a pointer to a page table, the |
| `00:34:47.440 - 00:34:50.149` | old size, that is what it was before, |
| `00:34:50.159 - 00:34:52.869` | and the new size. And it returns the new |
| `00:34:52.879 - 00:34:54.190` | size. |
| `00:34:54.200 - 00:34:56.950` | Uh the uh pages are added to the virtual |
| `00:34:56.960 - 00:35:00.350` | address space at it calls K alloc to get |
| `00:35:00.360 - 00:35:03.030` | new physical pages, and it zeros those |
| `00:35:03.040 - 00:35:06.030` | out, and it call calls map pages to add |
| `00:35:06.040 - 00:35:07.550` | them to the |
| `00:35:07.560 - 00:35:10.470` | page table. Uh the new pages will be |
| `00:35:10.480 - 00:35:12.950` | marked as read, write, executable, and |
| `00:35:12.960 - 00:35:16.190` | have user permission set. And as I said, |
| `00:35:16.200 - 00:35:17.910` | the old and the new need not be page |
| `00:35:17.920 - 00:35:18.910` | aligned. |
| `00:35:18.920 - 00:35:21.750` | Um if there are any problems, uh it will |
| `00:35:21.760 - 00:35:23.910` | basically undo everything it did. So, it |
| `00:35:23.920 - 00:35:26.310` | will call K free to uh |
| `00:35:26.320 - 00:35:29.230` | deallocate um what it's what it's |
| `00:35:29.240 - 00:35:31.430` | allocated, and it will also use UVM |
| `00:35:31.440 - 00:35:34.910` | dealloc, um which I'll talk about |
| `00:35:34.920 - 00:35:37.710` | in a second. But, uh it will return zero |
| `00:35:37.720 - 00:35:40.510` | if it fails to actually enlarge the uh |
| `00:35:40.520 - 00:35:43.190` | address space and instead leaves it just |
| `00:35:43.200 - 00:35:44.550` | the way it is. |
| `00:35:44.560 - 00:35:46.190` | So, let's take a look at the code for |
| `00:35:46.200 - 00:35:48.150` | UVM alloc here. |
| `00:35:48.160 - 00:35:49.750` | We're passed a pointer to the page |
| `00:35:49.760 - 00:35:52.030` | table, uh the old size, and the new |
| `00:35:52.040 - 00:35:54.830` | size, and we're going to return the new |
| `00:35:54.840 - 00:35:56.390` | size. |
| `00:35:56.400 - 00:35:58.390` | First thing we do is we do a check to |
| `00:35:58.400 - 00:36:01.230` | make sure that we are trying to grow the |
| `00:36:01.240 - 00:36:02.670` | uh address space. |
| `00:36:02.680 - 00:36:04.470` | And if we are not trying to grow it, we |
| `00:36:04.480 - 00:36:06.990` | just return the old size. |
| `00:36:07.000 - 00:36:10.550` | Then, uh we round the old size up to the |
| `00:36:10.560 - 00:36:12.110` | next page boundary. So, that's what I |
| `00:36:12.120 - 00:36:14.630` | talked about here, rounding it up to |
| `00:36:14.640 - 00:36:17.270` | uh the next page boundary. So, page |
| `00:36:17.280 - 00:36:18.470` | round up. |
| `00:36:18.480 - 00:36:20.110` | And then we're going to walk through the |
| `00:36:20.120 - 00:36:22.310` | pages. In my example, I did |
| `00:36:22.320 - 00:36:24.150` | this page and this page, so I did two |
| `00:36:24.160 - 00:36:25.270` | pages. |
| `00:36:25.280 - 00:36:28.670` | So, A is going to start out at old size |
| `00:36:28.680 - 00:36:30.190` | and we're going to increase it by page |
| `00:36:30.200 - 00:36:34.310` | size until it exceeds new size. |
| `00:36:34.320 - 00:36:35.470` | And so, |
| `00:36:35.480 - 00:36:36.790` | that's what's going on with the loop |
| `00:36:36.800 - 00:36:38.670` | here. So, each iteration of the loop |
| `00:36:38.680 - 00:36:42.230` | will allocate one new page and add it to |
| `00:36:42.240 - 00:36:44.350` | the virtual address space. |
| `00:36:44.360 - 00:36:46.230` | So, the first thing we do for each page |
| `00:36:46.240 - 00:36:48.190` | is we allocate a page of physical memory |
| `00:36:48.200 - 00:36:49.870` | by calling kalloc. |
| `00:36:49.880 - 00:36:52.470` | And if there are problems, if kalloc has |
| `00:36:52.480 - 00:36:54.790` | no more memory, it'll it will return |
| `00:36:54.800 - 00:36:56.750` | null and we will then have an error |
| `00:36:56.760 - 00:36:59.710` | situation. So, I'll describe UVM |
| `00:36:59.720 - 00:37:02.710` | thealloc later, but basically it undoes |
| `00:37:02.720 - 00:37:04.310` | everything and and |
| `00:37:04.320 - 00:37:04.870` | um |
| `00:37:04.880 - 00:37:06.710` | this is the new size here. So, we revert |
| `00:37:06.720 - 00:37:08.950` | to whatever the old size was. |
| `00:37:08.960 - 00:37:12.710` | Uh so, this is the the current size and |
| `00:37:12.720 - 00:37:15.070` | for the call to UVM thealloc is the the |
| `00:37:15.080 - 00:37:17.070` | current size and this is the new size. |
| `00:37:17.080 - 00:37:20.110` | And so, what we've done is we've uh |
| `00:37:20.120 - 00:37:22.630` | managed to get this far as A here and we |
| `00:37:22.640 - 00:37:24.710` | then retract everything or undo |
| `00:37:24.720 - 00:37:27.710` | everything and return zero. But, if |
| `00:37:27.720 - 00:37:30.910` | kalloc succeeds, uh then mem will not be |
| `00:37:30.920 - 00:37:34.350` | null and so, we've got a page. So, we |
| `00:37:34.360 - 00:37:34.990` | uh |
| `00:37:35.000 - 00:37:37.590` | zero it out by calling memset. You know, |
| `00:37:37.600 - 00:37:40.110` | we don't want any data from some |
| `00:37:40.120 - 00:37:42.430` | previous page which is which is which |
| `00:37:42.440 - 00:37:43.710` | was in some |
| `00:37:43.720 - 00:37:46.270` | other processes virtual address space |
| `00:37:46.280 - 00:37:49.590` | perhaps to uh be We don't want the data |
| `00:37:49.600 - 00:37:51.550` | on that page to become visible to the |
| `00:37:51.560 - 00:37:53.710` | new process because that would mean data |
| `00:37:53.720 - 00:37:55.910` | leak leakage and it would be a security |
| `00:37:55.920 - 00:37:58.470` | violation. So, we zero out that page. |
| `00:37:58.480 - 00:38:01.830` | And then we add an entry to the page |
| `00:38:01.840 - 00:38:05.510` | table for that virtual address. So, A is |
| `00:38:05.520 - 00:38:07.990` | the virtual address here that we're |
| `00:38:08.000 - 00:38:09.270` | adding. So, |
| `00:38:09.280 - 00:38:10.950` | A is the uh |
| `00:38:10.960 - 00:38:12.710` | virtual address first here and then on |
| `00:38:12.720 - 00:38:14.510` | the next iteration here. |
| `00:38:14.520 - 00:38:19.270` | And we call map_pages to add uh that and |
| `00:38:19.280 - 00:38:22.070` | the physical address is the |
| `00:38:22.080 - 00:38:24.110` | uh address of the page we got from K |
| `00:38:24.120 - 00:38:26.270` | Alloc. So, that's the physical address. |
| `00:38:26.280 - 00:38:30.150` | Of course, it's 4K bytes. And we pass in |
| `00:38:30.160 - 00:38:33.750` | permissions to make this page readable, |
| `00:38:33.760 - 00:38:35.910` | writable, executable, and accessible in |
| `00:38:35.920 - 00:38:38.910` | user mode. And if everything's okay, |
| `00:38:38.920 - 00:38:41.630` | then we go on to the next page. However, |
| `00:38:41.640 - 00:38:45.830` | if there's a problem, then we free that |
| `00:38:45.840 - 00:38:49.110` | page that we allocated here, okay? With |
| `00:38:49.120 - 00:38:51.150` | a call to K Free. And then we also |
| `00:38:51.160 - 00:38:52.910` | deallocate all the other pages. So, |
| `00:38:52.920 - 00:38:55.910` | again, we call UVM Dealloc to undo |
| `00:38:55.920 - 00:38:58.310` | everything we've done so far and return |
| `00:38:58.320 - 00:38:59.390` | zero. |
| `00:38:59.400 - 00:39:00.550` | Um |
| `00:39:00.560 - 00:39:02.030` | when the loop finishes, everything's |
| `00:39:02.040 - 00:39:04.550` | okay, we return the new size that we |
| `00:39:04.560 - 00:39:06.950` | were passed. |
| `00:39:06.960 - 00:39:09.790` | Before we look at the UVM Dealloc, let's |
| `00:39:09.800 - 00:39:12.510` | look at UVM Unmap. |
| `00:39:12.520 - 00:39:14.750` | It's passed a pointer to a page table, |
| `00:39:14.760 - 00:39:16.950` | as well as a number of virtual address |
| `00:39:16.960 - 00:39:20.190` | pages. So, virtual address here is a |
| `00:39:20.200 - 00:39:22.230` | page-aligned address, and we have the |
| `00:39:22.240 - 00:39:23.990` | number of pages that need to be |
| `00:39:24.000 - 00:39:25.510` | unmapped. |
| `00:39:25.520 - 00:39:27.910` | And for each page, it will locate its |
| `00:39:27.920 - 00:39:31.550` | page table entry and clear the valid |
| `00:39:31.560 - 00:39:32.710` | bit. |
| `00:39:32.720 - 00:39:34.470` | In addition, there's another parameter, |
| `00:39:34.480 - 00:39:37.430` | do free, and if that's true, then this |
| `00:39:37.440 - 00:39:39.830` | function will call K Free on each of the |
| `00:39:39.840 - 00:39:42.470` | data pages and return them to the data |
| `00:39:42.480 - 00:39:44.190` | the free data pool. |
| `00:39:44.200 - 00:39:45.950` | Okay, here's the code. |
| `00:39:45.960 - 00:39:49.470` | Remove in pages of mapping starting from |
| `00:39:49.480 - 00:39:51.590` | the virtual address VA, which must be |
| `00:39:51.600 - 00:39:52.790` | page-aligned. |
| `00:39:52.800 - 00:39:55.270` | And the mappings must exist. |
| `00:39:55.280 - 00:39:56.830` | And optionally free the physical memory. |
| `00:39:56.840 - 00:39:58.710` | So, here's a pointer to the page table, |
| `00:39:58.720 - 00:40:00.270` | the virtual address, the number of |
| `00:40:00.280 - 00:40:02.830` | pages, and the boolean. |
| `00:40:02.840 - 00:40:05.190` | And the first thing we do is make sure |
| `00:40:05.200 - 00:40:08.190` | that VA is page-aligned, and if it's |
| `00:40:08.200 - 00:40:12.590` | not, we call panic. |
| `00:40:12.600 - 00:40:14.310` | Then, what we're going going to do is |
| `00:40:14.320 - 00:40:15.950` | we're going to |
| `00:40:15.960 - 00:40:17.750` | iterate through all of the virtual |
| `00:40:17.760 - 00:40:20.030` | pages. So, we start with the virtual |
| `00:40:20.040 - 00:40:23.390` | address VA and we successfully increment |
| `00:40:23.400 - 00:40:28.350` | it by 4K by the page size until we |
| `00:40:28.360 - 00:40:30.710` | uh reach the limit. In other words, VA |
| `00:40:30.720 - 00:40:33.670` | plus the number of pages times the page |
| `00:40:33.680 - 00:40:35.310` | size. |
| `00:40:35.320 - 00:40:37.190` | So, for each |
| `00:40:37.200 - 00:40:38.710` | virtual page, which has a virtual |
| `00:40:38.720 - 00:40:41.270` | address A, what are we going to do? |
| `00:40:41.280 - 00:40:44.190` | Well, we start by calling walk and |
| `00:40:44.200 - 00:40:45.910` | here's the pointer to the page table in |
| `00:40:45.920 - 00:40:48.790` | the virtual address and we |
| `00:40:48.800 - 00:40:51.630` | and walk will return a pointer to the |
| `00:40:51.640 - 00:40:54.630` | page table entry. PTE is a pointer to |
| `00:40:54.640 - 00:40:56.350` | the page table entry. |
| `00:40:56.360 - 00:40:58.230` | Uh if there's any problem with that, |
| `00:40:58.240 - 00:41:01.830` | it'll return null and we panic. The |
| `00:41:01.840 - 00:41:04.750` | pages that we are unmapping had better |
| `00:41:04.760 - 00:41:06.710` | be mapped. |
| `00:41:06.720 - 00:41:09.310` | Uh so, the next thing we do is we |
| `00:41:09.320 - 00:41:11.710` | retrieve the page table entry and we |
| `00:41:11.720 - 00:41:15.190` | check the valid bit. And if it is zero, |
| `00:41:15.200 - 00:41:17.270` | uh then that page is not mapped. That's |
| `00:41:17.280 - 00:41:20.510` | a problem. It had better be one. |
| `00:41:20.520 - 00:41:22.750` | Next, we look at the page table entry |
| `00:41:22.760 - 00:41:25.950` | and this function here PTE flags will |
| `00:41:25.960 - 00:41:28.910` | isolate the the flag bits. |
| `00:41:28.920 - 00:41:30.750` | And uh the flag bits, you know, the |
| `00:41:30.760 - 00:41:32.510` | readable, writable, executable, user |
| `00:41:32.520 - 00:41:34.270` | mode, and valid bits. |
| `00:41:34.280 - 00:41:36.910` | Uh we are we check to make sure that the |
| `00:41:36.920 - 00:41:39.270` | valid bit is one. And here we're |
| `00:41:39.280 - 00:41:41.550` | checking to make sure that something |
| `00:41:41.560 - 00:41:44.390` | else is one besides. It better not just |
| `00:41:44.400 - 00:41:47.670` | be one. And this says the message is uh |
| `00:41:47.680 - 00:41:50.670` | that it's not a leaf. So, exactly what's |
| `00:41:50.680 - 00:41:52.990` | going on there? Well, let's uh look at |
| `00:41:53.000 - 00:41:55.030` | this uh pay |
| `00:41:55.040 - 00:41:56.430` | picture of the |
| `00:41:56.440 - 00:41:58.830` | page table. And here we have a page |
| `00:41:58.840 - 00:42:00.470` | table entry pointing to another index |
| `00:42:00.480 - 00:42:01.910` | page and here we have a page table entry |
| `00:42:01.920 - 00:42:03.990` | pointing to another index page. And here |
| `00:42:04.000 - 00:42:05.270` | we have a page table entry pointing to a |
| `00:42:05.280 - 00:42:06.990` | data page. |
| `00:42:07.000 - 00:42:07.630` | Uh |
| `00:42:07.640 - 00:42:10.590` | in the case of these page table entries, |
| `00:42:10.600 - 00:42:12.710` | they had better be valid if there is in |
| `00:42:12.720 - 00:42:15.430` | fact a pointer. And the other bits, the |
| `00:42:15.440 - 00:42:17.470` | readable, writable, executable, and user |
| `00:42:17.480 - 00:42:20.470` | mode bits had better be zero. |
| `00:42:20.480 - 00:42:22.750` | Down here, if the page table entry is |
| `00:42:22.760 - 00:42:25.550` | valid, then it had better point to a |
| `00:42:25.560 - 00:42:27.910` | data page, and that data page needs to |
| `00:42:27.920 - 00:42:30.070` | be at least readable or writable or |
| `00:42:30.080 - 00:42:32.990` | executable and have the user mode bit |
| `00:42:33.000 - 00:42:35.790` | possibly set. So, the other bits besides |
| `00:42:35.800 - 00:42:38.990` | the valid bit can't all be zero. So, |
| `00:42:39.000 - 00:42:41.110` | that's what we're checking for here. We |
| `00:42:41.120 - 00:42:42.030` | are |
| `00:42:42.040 - 00:42:45.270` | making sure that there's something some |
| `00:42:45.280 - 00:42:48.310` | bit besides just the valid bit that is |
| `00:42:48.320 - 00:42:49.110` | uh |
| `00:42:49.120 - 00:42:50.350` | set. |
| `00:42:50.360 - 00:42:52.310` | And so, |
| `00:42:52.320 - 00:42:54.030` | what we're going to do with this page |
| `00:42:54.040 - 00:42:56.550` | table entry is just set it to zero, |
| `00:42:56.560 - 00:42:58.910` | which will clear the valid bit |
| `00:42:58.920 - 00:43:00.390` | among other things. |
| `00:43:00.400 - 00:43:01.950` | And so, that's what we do here. But |
| `00:43:01.960 - 00:43:04.230` | before we do that, we ask whether we are |
| `00:43:04.240 - 00:43:07.870` | required to free the data page, and if |
| `00:43:07.880 - 00:43:10.390` | so, we use this |
| `00:43:10.400 - 00:43:13.630` | page table entry to physical address to |
| `00:43:13.640 - 00:43:16.870` | extract the pointer to the |
| `00:43:16.880 - 00:43:18.750` | actual page in memory. |
| `00:43:18.760 - 00:43:19.430` | Uh |
| `00:43:19.440 - 00:43:22.070` | here, remember this picture. This is the |
| `00:43:22.080 - 00:43:23.470` | page table |
| `00:43:23.480 - 00:43:26.110` | entry, and we're using the PTE to |
| `00:43:26.120 - 00:43:27.630` | physical address, |
| `00:43:27.640 - 00:43:30.070` | and it's going to ignore the |
| `00:43:30.080 - 00:43:33.470` | bits and do a little shifting and |
| `00:43:33.480 - 00:43:36.070` | replace that offset with zero to give us |
| `00:43:36.080 - 00:43:37.550` | an address |
| `00:43:37.560 - 00:43:40.830` | that we can use in |
| `00:43:40.840 - 00:43:43.310` | the call to K free. So, here we are |
| `00:43:43.320 - 00:43:45.350` | using that physical address |
| `00:43:45.360 - 00:43:47.070` | that was so obtained |
| `00:43:47.080 - 00:43:49.590` | and returning that page to the free |
| `00:43:49.600 - 00:43:51.790` | pool. |
| `00:43:51.800 - 00:43:54.870` | Next, I want to talk about UVM dealloc. |
| `00:43:54.880 - 00:43:58.110` | Uh previously, I talked about UVM alloc, |
| `00:43:58.120 - 00:44:00.430` | and remember it's passed an old size and |
| `00:44:00.440 - 00:44:03.310` | a new size, and it's essentially used |
| `00:44:03.320 - 00:44:06.790` | when the user process needs to grow the |
| `00:44:06.800 - 00:44:09.310` | virtual address space. So, it needs to |
| `00:44:09.320 - 00:44:11.790` | increase the break point. |
| `00:44:11.800 - 00:44:14.110` | And so, |
| `00:44:14.120 - 00:44:16.590` | UVM alloc adds pages to the virtual |
| `00:44:16.600 - 00:44:18.390` | address space. And if anything goes |
| `00:44:18.400 - 00:44:20.990` | wrong, it calls UVM dealloc to remove |
| `00:44:21.000 - 00:44:24.150` | pages. So, it has it can have a problem |
| `00:44:24.160 - 00:44:25.950` | and returns zero. |
| `00:44:25.960 - 00:44:28.870` | UVM dealloc will not fail. |
| `00:44:28.880 - 00:44:29.830` | So, |
| `00:44:29.840 - 00:44:32.230` | it's passed the old size and the new |
| `00:44:32.240 - 00:44:34.550` | size. And so, here's an example there |
| `00:44:34.560 - 00:44:37.270` | where we are taking |
| `00:44:37.280 - 00:44:39.030` | the virtual address space and shrinking |
| `00:44:39.040 - 00:44:41.750` | it. So, in this particular example, we |
| `00:44:41.760 - 00:44:44.470` | are removing two pages. Old size and new |
| `00:44:44.480 - 00:44:46.910` | size need not be page aligned. |
| `00:44:46.920 - 00:44:50.390` | And we will also call K free to get rid |
| `00:44:50.400 - 00:44:51.990` | of the data pages. |
| `00:44:52.000 - 00:44:54.350` | So, when we call UVM unmap, we'll pass |
| `00:44:54.360 - 00:44:58.110` | it the do free parameter being true. |
| `00:44:58.120 - 00:44:59.310` | So, the way it's going to work is it's |
| `00:44:59.320 - 00:45:01.430` | going to take the old size and round it |
| `00:45:01.440 - 00:45:05.710` | up to the next page boundary here, and |
| `00:45:05.720 - 00:45:07.550` | round new size up to the next page |
| `00:45:07.560 - 00:45:09.510` | boundary here. And then it's going to |
| `00:45:09.520 - 00:45:11.670` | subtract them to determine the number of |
| `00:45:11.680 - 00:45:14.190` | pages that have to be freed. |
| `00:45:14.200 - 00:45:16.150` | So, let's take a look at the code for |
| `00:45:16.160 - 00:45:17.350` | UVM |
| `00:45:17.360 - 00:45:21.230` | dealloc. |
| `00:45:21.240 - 00:45:22.350` | It's passed the pointer to the page |
| `00:45:22.360 - 00:45:26.070` | table, the old size, and the new size. |
| `00:45:26.080 - 00:45:28.990` | First, it checks to make sure that we |
| `00:45:29.000 - 00:45:30.390` | are |
| `00:45:30.400 - 00:45:32.670` | decreasing the |
| `00:45:32.680 - 00:45:35.590` | size of the space. So, if new size is |
| `00:45:35.600 - 00:45:37.350` | less than old size, then we are |
| `00:45:37.360 - 00:45:38.870` | shrinking the address space and we can |
| `00:45:38.880 - 00:45:41.750` | keep on going. But otherwise, |
| `00:45:41.760 - 00:45:44.070` | we're not asking to shrink the size, so |
| `00:45:44.080 - 00:45:46.950` | we just return the old size. |
| `00:45:46.960 - 00:45:50.630` | Um and we don't shrink anything. |
| `00:45:50.640 - 00:45:52.310` | Next, I think the best way to understand |
| `00:45:52.320 - 00:45:55.310` | this is to look at the computation of in |
| `00:45:55.320 - 00:45:56.630` | pages. |
| `00:45:56.640 - 00:45:59.550` | So, the number of pages, we round up the |
| `00:45:59.560 - 00:46:02.710` | old size and we round up the new size. |
| `00:46:02.720 - 00:46:04.910` | Okay, so we round these numbers up here. |
| `00:46:04.920 - 00:46:06.950` | So, we have a pointer here for old size |
| `00:46:06.960 - 00:46:09.350` | and a pointer here. We find the |
| `00:46:09.360 - 00:46:11.310` | difference, that's the number of bytes, |
| `00:46:11.320 - 00:46:13.070` | and then divide by the page size to get |
| `00:46:13.080 - 00:46:16.590` | the number of pages. |
| `00:46:16.600 - 00:46:20.310` | Here we are checking to make sure we get |
| `00:46:20.320 - 00:46:21.590` | something. |
| `00:46:21.600 - 00:46:24.630` | Um if these happen to be equal, |
| `00:46:24.640 - 00:46:27.270` | uh then we won't do this um because that |
| `00:46:27.280 - 00:46:29.590` | would be a zero here. So, we make sure |
| `00:46:29.600 - 00:46:31.310` | that we have a positive number of pages |
| `00:46:31.320 - 00:46:32.430` | here. |
| `00:46:32.440 - 00:46:37.310` | And then we call UVM unmap. Again, we uh |
| `00:46:37.320 - 00:46:39.150` | round up the new size. So, we're going |
| `00:46:39.160 - 00:46:41.510` | to be freeing these two pages. So, we |
| `00:46:41.520 - 00:46:44.150` | round new size up to this point and then |
| `00:46:44.160 - 00:46:46.670` | we have the number of pages is two. So, |
| `00:46:46.680 - 00:46:48.790` | we pass it number of pages and this is |
| `00:46:48.800 - 00:46:53.190` | the do free argument to UVM unmap to |
| `00:46:53.200 - 00:46:55.230` | free the data pages. And finally, we |
| `00:46:55.240 - 00:46:58.590` | return the new size. |
| `00:46:58.600 - 00:47:01.110` | Next, I want to talk about UVM free. |
| `00:47:01.120 - 00:47:03.230` | This is used to basically get rid of a |
| `00:47:03.240 - 00:47:05.830` | virtual address space. So, here's a |
| `00:47:05.840 - 00:47:07.910` | virtual address space. Uh it has some |
| `00:47:07.920 - 00:47:10.070` | size which shows how many pages have |
| `00:47:10.080 - 00:47:12.110` | been allocated down in this region. |
| `00:47:12.120 - 00:47:13.910` | And of course, it has the trampoline and |
| `00:47:13.920 - 00:47:16.990` | trap frame pages uh mapped in the upper |
| `00:47:17.000 - 00:47:19.910` | two pages of the virtual address space. |
| `00:47:19.920 - 00:47:22.830` | What it's going to do is call UVM unmap |
| `00:47:22.840 - 00:47:25.110` | with the parameter of size, and that's |
| `00:47:25.120 - 00:47:28.870` | going to unmap and free all of the pages |
| `00:47:28.880 - 00:47:31.030` | that are here. So, all of the data pages |
| `00:47:31.040 - 00:47:33.470` | will be returned to the free pool and |
| `00:47:33.480 - 00:47:35.430` | all of the page table entries that point |
| `00:47:35.440 - 00:47:37.630` | to those uh data pages will be uh |
| `00:47:37.640 - 00:47:40.430` | cleared out and made invalid. |
| `00:47:40.440 - 00:47:43.550` | Uh it will leave the trampoline uh |
| `00:47:43.560 - 00:47:46.270` | and trap frame pages uh in physical |
| `00:47:46.280 - 00:47:47.990` | memory. It will not attempt to return |
| `00:47:48.000 - 00:47:49.750` | those to the free pool. Of course, we |
| `00:47:49.760 - 00:47:51.630` | don't want to do that because they are |
| `00:47:51.640 - 00:47:54.070` | uh in use. Um |
| `00:47:54.080 - 00:47:56.430` | and the they will the trap frame page |
| `00:47:56.440 - 00:47:58.630` | has to continue to exist for this |
| `00:47:58.640 - 00:48:01.150` | process for each process and the trap |
| `00:48:01.160 - 00:48:03.110` | frame page is of course shared by every |
| `00:48:03.120 - 00:48:05.310` | virtual address space so we can't |
| `00:48:05.320 - 00:48:07.430` | free that page either. |
| `00:48:07.440 - 00:48:09.510` | Um then it will call this function free |
| `00:48:09.520 - 00:48:12.230` | walk which will eliminate the entire |
| `00:48:12.240 - 00:48:13.470` | page table. |
| `00:48:13.480 - 00:48:16.190` | Um so let's take a look at the code for |
| `00:48:16.200 - 00:48:18.070` | UVM free. |
| `00:48:18.080 - 00:48:20.270` | Uh here we see it. We're passed a |
| `00:48:20.280 - 00:48:22.830` | pointer to a page table as well as the |
| `00:48:22.840 - 00:48:26.150` | size and what we're going to do is call |
| `00:48:26.160 - 00:48:29.910` | UVM unmap assuming there are more than |
| `00:48:29.920 - 00:48:31.390` | zero bytes. |
| `00:48:31.400 - 00:48:34.190` | Um and we'll pass it the virtual address |
| `00:48:34.200 - 00:48:36.790` | of zero and uh |
| `00:48:36.800 - 00:48:38.790` | take the size and divide it by the page |
| `00:48:38.800 - 00:48:40.990` | size to get the number of pages. |
| `00:48:41.000 - 00:48:43.470` | This is the do free parameter that will |
| `00:48:43.480 - 00:48:45.070` | out that will |
| `00:48:45.080 - 00:48:48.550` | call K free for all of the data pages. |
| `00:48:48.560 - 00:48:51.430` | And then uh we call the free walk |
| `00:48:51.440 - 00:48:53.870` | function. |
| `00:48:53.880 - 00:48:55.790` | Next, let's talk about the free walk |
| `00:48:55.800 - 00:48:58.430` | function. It will free all the pages in |
| `00:48:58.440 - 00:49:02.070` | the page table or more precisely it will |
| `00:49:02.080 - 00:49:05.110` | go through the page table tree and for |
| `00:49:05.120 - 00:49:08.270` | each index page it will call K free to |
| `00:49:08.280 - 00:49:10.310` | return it to the free pool. |
| `00:49:10.320 - 00:49:13.510` | It does not free the data pages. It uh |
| `00:49:13.520 - 00:49:15.830` | assumes that all the data pages have |
| `00:49:15.840 - 00:49:19.110` | been previously freed and all uh the |
| `00:49:19.120 - 00:49:20.750` | entries the page table entries that |
| `00:49:20.760 - 00:49:24.070` | point to data pages have been uh |
| `00:49:24.080 - 00:49:25.470` | removed. |
| `00:49:25.480 - 00:49:28.510` | Uh so um I wanted to uh |
| `00:49:28.520 - 00:49:31.750` | mention for the UVM free |
| `00:49:31.760 - 00:49:35.150` | uh this uh frees all the data pages and |
| `00:49:35.160 - 00:49:37.350` | clears out the page table entries for |
| `00:49:37.360 - 00:49:40.230` | all the pages below the size. It doesn't |
| `00:49:40.240 - 00:49:43.110` | do anything for the uh two pages at the |
| `00:49:43.120 - 00:49:46.550` | very top. These pages are not to be |
| `00:49:46.560 - 00:49:49.790` | freed because they need to be used. |
| `00:49:49.800 - 00:49:54.270` | And before we call UVM free, we will um |
| `00:49:54.280 - 00:49:57.190` | separately call UVMunmap |
| `00:49:57.200 - 00:49:59.910` | to remove the entries for the trampoline |
| `00:49:59.920 - 00:50:02.030` | and the trap frame pages without |
| `00:50:02.040 - 00:50:04.990` | actually freeing them. And so, when we |
| `00:50:05.000 - 00:50:07.390` | get ready to call free walk, we can |
| `00:50:07.400 - 00:50:10.790` | assume that there are no data pages in |
| `00:50:10.800 - 00:50:13.070` | the page table. |
| `00:50:13.080 - 00:50:15.590` | The way this thing works is recursively. |
| `00:50:15.600 - 00:50:17.950` | It's a past initially a pointer to the |
| `00:50:17.960 - 00:50:19.670` | root of the page table. |
| `00:50:19.680 - 00:50:21.630` | And for |
| `00:50:21.640 - 00:50:24.390` | uh that for each page it's called on, it |
| `00:50:24.400 - 00:50:26.230` | goes through and looks at all of the |
| `00:50:26.240 - 00:50:29.190` | children and calls itself recursively. |
| `00:50:29.200 - 00:50:31.550` | Each recursive call will completely free |
| `00:50:31.560 - 00:50:35.470` | the entire subtree. And so, it it frees |
| `00:50:35.480 - 00:50:38.030` | all of the children and then finally it |
| `00:50:38.040 - 00:50:39.910` | frees the top page. |
| `00:50:39.920 - 00:50:41.590` | Okay? So, now let's take a look at the |
| `00:50:41.600 - 00:50:47.990` | code for free walk. |
| `00:50:48.000 - 00:50:51.710` | It's past a pointer to an index page. |
| `00:50:51.720 - 00:50:53.550` | And uh |
| `00:50:53.560 - 00:50:55.830` | it goes through that index page. In the |
| `00:50:55.840 - 00:50:57.150` | initially it's past a pointer to the |
| `00:50:57.160 - 00:50:59.710` | root, but in the recursive calls down |
| `00:50:59.720 - 00:51:02.430` | here it's past pointers to it's past a |
| `00:51:02.440 - 00:51:05.670` | pointer to the child index page. |
| `00:51:05.680 - 00:51:08.990` | So, it's got a loop. It's going to |
| `00:51:09.000 - 00:51:12.550` | uh iterate through and look at all 512 |
| `00:51:12.560 - 00:51:14.630` | entries in the |
| `00:51:14.640 - 00:51:19.270` | index page. So, here it grabs the |
| `00:51:19.280 - 00:51:23.550` | index uh the ith uh page table entry and |
| `00:51:23.560 - 00:51:26.150` | looks at it and calls itself recursively |
| `00:51:26.160 - 00:51:28.110` | on that child. |
| `00:51:28.120 - 00:51:30.030` | And then finally after it's uh called |
| `00:51:30.040 - 00:51:31.350` | itself recursively on all of its |
| `00:51:31.360 - 00:51:35.630` | children, it frees that node itself. |
| `00:51:35.640 - 00:51:37.030` | Now, let me um |
| `00:51:37.040 - 00:51:41.070` | mention here that uh remember that in |
| `00:51:41.080 - 00:51:43.910` | the index pages uh in the upper pages |
| `00:51:43.920 - 00:51:46.870` | here, we we the valid bit being set. So, |
| `00:51:46.880 - 00:51:49.710` | in uh level one and level two, the valid |
| `00:51:49.720 - 00:51:52.190` | bit is set, but none of the other flag |
| `00:51:52.200 - 00:51:53.630` | bits, the readable, writable, or |
| `00:51:53.640 - 00:51:57.070` | executable are set. They're all zero. |
| `00:51:57.080 - 00:52:00.070` | Whereas, down here, we should if we have |
| `00:52:00.080 - 00:52:01.790` | the valid bit set, then we have a |
| `00:52:01.800 - 00:52:04.110` | pointer to a data page, and it must be |
| `00:52:04.120 - 00:52:05.430` | either readable, writable, or |
| `00:52:05.440 - 00:52:07.950` | executable, or or some combination. |
| `00:52:07.960 - 00:52:11.070` | So, we're assuming that all the data |
| `00:52:11.080 - 00:52:14.150` | pages have been freed, and all of the |
| `00:52:14.160 - 00:52:16.550` | page table entries for them have been |
| `00:52:16.560 - 00:52:19.190` | set to zero. So, they'll have a zero |
| `00:52:19.200 - 00:52:20.590` | valid bit. |
| `00:52:20.600 - 00:52:21.910` | Okay, so you |
| `00:52:21.920 - 00:52:22.910` | That's |
| `00:52:22.920 - 00:52:24.630` | what's going on here. |
| `00:52:24.640 - 00:52:26.350` | We uh |
| `00:52:26.360 - 00:52:29.270` | grab the next page table entry, and then |
| `00:52:29.280 - 00:52:30.590` | we look at it. |
| `00:52:30.600 - 00:52:34.310` | Is the valid bit set? Okay, if the valid |
| `00:52:34.320 - 00:52:35.710` | bit is set, |
| `00:52:35.720 - 00:52:37.950` | then we're assuming that we point down |
| `00:52:37.960 - 00:52:40.070` | to another index page. |
| `00:52:40.080 - 00:52:41.270` | Okay, so |
| `00:52:41.280 - 00:52:44.270` | we check here to make sure that the |
| `00:52:44.280 - 00:52:46.910` | readable, writable, and executable bits |
| `00:52:46.920 - 00:52:51.310` | are zero. So, if it is valid, and uh |
| `00:52:51.320 - 00:52:54.630` | the bits are all zero, then we've got |
| `00:52:54.640 - 00:52:57.230` | a valid page table entry pointing down |
| `00:52:57.240 - 00:52:58.310` | to |
| `00:52:58.320 - 00:53:00.710` | a lower level. So, we need to chase that |
| `00:53:00.720 - 00:53:05.390` | pointer. And so, here we call the |
| `00:53:05.400 - 00:53:07.750` | page table entry to physical address, |
| `00:53:07.760 - 00:53:11.150` | which turns the 44 bits, that shifts |
| `00:53:11.160 - 00:53:12.750` | them, and |
| `00:53:12.760 - 00:53:15.390` | gives us a pointer to the child index |
| `00:53:15.400 - 00:53:17.030` | page. And then we call ourselves |
| `00:53:17.040 - 00:53:20.110` | recursively on that child. And then |
| `00:53:20.120 - 00:53:23.310` | finally, we set that page table entry to |
| `00:53:23.320 - 00:53:24.510` | zero. |
| `00:53:24.520 - 00:53:26.630` | Um |
| `00:53:26.640 - 00:53:28.710` | So, |
| `00:53:28.720 - 00:53:30.990` | we have here a second check. We're |
| `00:53:31.000 - 00:53:33.870` | asking whether we've got a page table |
| `00:53:33.880 - 00:53:36.230` | entry that is |
| `00:53:36.240 - 00:53:38.350` | non-zero. Well, we're looking at the |
| `00:53:38.360 - 00:53:40.910` | valid bit. If the valid bit is set, |
| `00:53:40.920 - 00:53:44.990` | but one of these bits is not is one, |
| `00:53:45.000 - 00:53:47.350` | then this won't have been true. So, |
| `00:53:47.360 - 00:53:49.310` | we're saying that if we have a valid |
| `00:53:49.320 - 00:53:50.230` | bit, |
| `00:53:50.240 - 00:53:51.790` | but we've got readable, writable, or |
| `00:53:51.800 - 00:53:54.910` | executable, then something's wrong. We |
| `00:53:54.920 - 00:53:57.470` | should We We might have that situation |
| `00:53:57.480 - 00:53:59.110` | where we've got a valid bit and a |
| `00:53:59.120 - 00:54:00.910` | pointer to a data page with readable, |
| `00:54:00.920 - 00:54:03.990` | writable, or executable set, but we |
| `00:54:04.000 - 00:54:05.350` | assume that we've removed all the data |
| `00:54:05.360 - 00:54:07.830` | pages, so we better not have that |
| `00:54:07.840 - 00:54:10.550` | situation. So, that's what's going on |
| `00:54:10.560 - 00:54:12.830` | here. We |
| `00:54:12.840 - 00:54:14.150` | are |
| `00:54:14.160 - 00:54:16.030` | We've detected page table entry that |
| `00:54:16.040 - 00:54:19.710` | points to a data page here. |
| `00:54:19.720 - 00:54:21.790` | And the other thing I want to ask is |
| `00:54:21.800 - 00:54:23.230` | I'm not sure it's really necessary that |
| `00:54:23.240 - 00:54:25.830` | we zero out each page table entry. We're |
| `00:54:25.840 - 00:54:28.670` | going to be calling |
| `00:54:28.680 - 00:54:32.390` | kfree on this page anyway, so there's |
| `00:54:32.400 - 00:54:34.350` | not really any need to zero out the |
| `00:54:34.360 - 00:54:36.870` | entries as we process them, but I don't |
| `00:54:36.880 - 00:54:39.670` | think that hurts anything. |
| `00:54:39.680 - 00:54:41.870` | Next, let's take a look at the function |
| `00:54:41.880 - 00:54:44.990` | UVM copy. This is used in the fork |
| `00:54:45.000 - 00:54:46.910` | system call. |
| `00:54:46.920 - 00:54:49.270` | Remember that when we have a fork, we |
| `00:54:49.280 - 00:54:50.870` | need to take the |
| `00:54:50.880 - 00:54:53.790` | virtual address space of the process |
| `00:54:53.800 - 00:54:56.230` | that's doing the fork and make an exact |
| `00:54:56.240 - 00:54:59.910` | copy of that virtual address space, and |
| `00:54:59.920 - 00:55:03.350` | UVM copy is what we'll use to do that. |
| `00:55:03.360 - 00:55:06.150` | So, in this diagram, I'm trying to show |
| `00:55:06.160 - 00:55:09.110` | what's going on. Here's the existing |
| `00:55:09.120 - 00:55:11.270` | virtual address space, and it's got a |
| `00:55:11.280 - 00:55:15.310` | bunch of stuff in it up to size, as well |
| `00:55:15.320 - 00:55:18.310` | as a pointer to the trampoline page and |
| `00:55:18.320 - 00:55:22.550` | to the trap frame page for this process. |
| `00:55:22.560 - 00:55:23.670` | And |
| `00:55:23.680 - 00:55:26.870` | UVM copy assumes that we've got a second |
| `00:55:26.880 - 00:55:28.670` | address space that's |
| `00:55:28.680 - 00:55:31.350` | already created, and it assumes that it |
| `00:55:31.360 - 00:55:32.830` | has |
| `00:55:32.840 - 00:55:35.270` | the top two pages mapped into the |
| `00:55:35.280 - 00:55:38.070` | trampoline page, which is shared by all |
| `00:55:38.080 - 00:55:41.070` | virtual address spaces, and the trap |
| `00:55:41.080 - 00:55:44.470` | frame page is allocated and somewhere |
| `00:55:44.480 - 00:55:46.750` | else. And so that is mapped into this |
| `00:55:46.760 - 00:55:49.070` | virtual address space as well. |
| `00:55:49.080 - 00:55:50.910` | And what this |
| `00:55:50.920 - 00:55:53.790` | function UVM copy is going to do is copy |
| `00:55:53.800 - 00:55:58.470` | all pages up from zero up to size, okay, |
| `00:55:58.480 - 00:56:00.550` | into the new address space. So here I'm |
| `00:56:00.560 - 00:56:01.870` | showing |
| `00:56:01.880 - 00:56:04.430` | the new address space before this |
| `00:56:04.440 - 00:56:06.510` | function, and here I'm showing it after |
| `00:56:06.520 - 00:56:09.430` | the function. So what it's going to do |
| `00:56:09.440 - 00:56:11.590` | is it's going to make copies of all of |
| `00:56:11.600 - 00:56:14.270` | these data pages. So here it's going to |
| `00:56:14.280 - 00:56:17.590` | make a copy of the page, and then it's |
| `00:56:17.600 - 00:56:19.790` | going to add mappings for each one of |
| `00:56:19.800 - 00:56:24.910` | those pages to the page table. |
| `00:56:24.920 - 00:56:28.950` | And also the permissions that will be in |
| `00:56:28.960 - 00:56:32.270` | the new page table will be identical to |
| `00:56:32.280 - 00:56:33.190` | the |
| `00:56:33.200 - 00:56:34.670` | uh |
| `00:56:34.680 - 00:56:36.030` | permissions that were in the old page |
| `00:56:36.040 - 00:56:37.110` | table. |
| `00:56:37.120 - 00:56:40.430` | And if anything goes wrong, it's going |
| `00:56:40.440 - 00:56:42.870` | to sort of free up all the pages that |
| `00:56:42.880 - 00:56:45.590` | allocates and return an error code. |
| `00:56:45.600 - 00:56:47.550` | Okay, it'll return minus one if there's |
| `00:56:47.560 - 00:56:48.830` | a problem. |
| `00:56:48.840 - 00:56:50.910` | So So it's passed the old page table, |
| `00:56:50.920 - 00:56:54.670` | the new page table, and the size of the |
| `00:56:54.680 - 00:56:57.950` | old page old virtual address space. |
| `00:56:57.960 - 00:57:00.350` | Here's the code. So let's take a look at |
| `00:57:00.360 - 00:57:03.190` | this. Uh |
| `00:57:03.200 - 00:57:04.510` | It's |
| `00:57:04.520 - 00:57:06.750` | passed pointer to the old page table and |
| `00:57:06.760 - 00:57:09.670` | the new page table, as well as the size, |
| `00:57:09.680 - 00:57:12.070` | and it returns zero if everything's okay |
| `00:57:12.080 - 00:57:15.310` | or minus one if there is an error. |
| `00:57:15.320 - 00:57:19.470` | So it's going to loop here |
| `00:57:19.480 - 00:57:20.990` | through |
| `00:57:21.000 - 00:57:23.510` | all of the pages from virtual address |
| `00:57:23.520 - 00:57:27.550` | zero all the way up to size, and it's |
| `00:57:27.560 - 00:57:30.670` | going to go in increments of pages. So |
| `00:57:30.680 - 00:57:33.950` | it's going to increment by 4K here until |
| `00:57:33.960 - 00:57:37.310` | it reaches or exceeds size. |
| `00:57:37.320 - 00:57:40.830` | And for each one of the virtual pages in |
| `00:57:40.840 - 00:57:43.310` | the old space, |
| `00:57:43.320 - 00:57:46.150` | um it's going to obtain the page table |
| `00:57:46.160 - 00:57:49.350` | entry. So, it calls walk. Oh, I forgot |
| `00:57:49.360 - 00:57:52.390` | to bold it. Anyway, it calls walk on the |
| `00:57:52.400 - 00:57:55.070` | um old address space with the virtual |
| `00:57:55.080 - 00:57:56.910` | address I. |
| `00:57:56.920 - 00:57:59.070` | And uh |
| `00:57:59.080 - 00:57:59.750` | uh |
| `00:57:59.760 - 00:58:02.110` | it gets a pointer to that |
| `00:58:02.120 - 00:58:02.670` | um |
| `00:58:02.680 - 00:58:05.670` | and PTE in this case is a pointer. |
| `00:58:05.680 - 00:58:08.310` | And if there is any problem, |
| `00:58:08.320 - 00:58:09.070` | um |
| `00:58:09.080 - 00:58:11.510` | it uh just calls panic. The page table |
| `00:58:11.520 - 00:58:15.070` | entry should exist. And furthermore, |
| `00:58:15.080 - 00:58:17.310` | uh it should be marked valid. So, here |
| `00:58:17.320 - 00:58:19.550` | we fetch the page table entry and check |
| `00:58:19.560 - 00:58:21.390` | the valid bit to make sure that it is |
| `00:58:21.400 - 00:58:22.830` | not zero. |
| `00:58:22.840 - 00:58:24.070` | And |
| `00:58:24.080 - 00:58:27.190` | then we proceed to look at the page |
| `00:58:27.200 - 00:58:30.710` | table entry and extract the address. So, |
| `00:58:30.720 - 00:58:32.470` | this is page table entry to physical |
| `00:58:32.480 - 00:58:35.670` | address. So, we extract the uh page |
| `00:58:35.680 - 00:58:37.990` | number and turn it into a the a pointer |
| `00:58:38.000 - 00:58:41.790` | to the uh page to the physical page the |
| `00:58:41.800 - 00:58:43.790` | physical data page. |
| `00:58:43.800 - 00:58:46.710` | And uh we also |
| `00:58:46.720 - 00:58:49.390` | take the page table entry and extract |
| `00:58:49.400 - 00:58:51.750` | the permissions. So, this extracts the |
| `00:58:51.760 - 00:58:53.590` | uh bits, the readable, writable, |
| `00:58:53.600 - 00:58:56.870` | executable, user mode bits and puts them |
| `00:58:56.880 - 00:58:58.910` | in flags. |
| `00:58:58.920 - 00:59:01.150` | Now, we're ready to copy the data page. |
| `00:59:01.160 - 00:59:03.430` | So, the first thing we do is call K |
| `00:59:03.440 - 00:59:07.510` | alloc to get a new data page. So, for |
| `00:59:07.520 - 00:59:09.230` | the first time we're looking at this |
| `00:59:09.240 - 00:59:11.910` | data page down here and we call K alloc |
| `00:59:11.920 - 00:59:14.190` | to allocate a page here and then we're |
| `00:59:14.200 - 00:59:16.550` | going to copy the data. Okay? |
| `00:59:16.560 - 00:59:19.990` | So, if um let me just zoom out here. If |
| `00:59:20.000 - 00:59:23.150` | we have any problem here, |
| `00:59:23.160 - 00:59:25.270` | we're going to do a go to |
| `00:59:25.280 - 00:59:27.710` | to this label down here. |
| `00:59:27.720 - 00:59:30.750` | You don't see go-tos in C programs very |
| `00:59:30.760 - 00:59:33.230` | often, but you know, here you have one. |
| `00:59:33.240 - 00:59:35.750` | Uh no comment about that. But in any |
| `00:59:35.760 - 00:59:37.910` | case, uh we call KAlloc, and if |
| `00:59:37.920 - 00:59:41.150` | everything's okay, then we copy |
| `00:59:41.160 - 00:59:45.070` | from the old page, right? We got the |
| `00:59:45.080 - 00:59:47.110` | address of the old data page here, or |
| `00:59:47.120 - 00:59:49.470` | the data page from the old virtual |
| `00:59:49.480 - 00:59:51.590` | address space, and we're going to copy |
| `00:59:51.600 - 00:59:53.790` | it into the page that we just allocated |
| `00:59:53.800 - 00:59:55.750` | with the KAlloc here. |
| `00:59:55.760 - 00:59:58.990` | And it's a 4K page size. |
| `00:59:59.000 - 01:00:01.830` | And then we're going to add the mapping, |
| `01:00:01.840 - 01:00:04.350` | so we have a pointer to the new page |
| `01:00:04.360 - 01:00:06.910` | table and the virtual address, |
| `01:00:06.920 - 01:00:08.670` | uh the page size uh |
| `01:00:08.680 - 01:00:10.270` | is what |
| `01:00:10.280 - 01:00:12.270` | we've got, and we've got a pointer to |
| `01:00:12.280 - 01:00:14.390` | the um |
| `01:00:14.400 - 01:00:16.630` | the data page that we've just created, |
| `01:00:16.640 - 01:00:18.830` | as well as the flags. So, this tells us |
| `01:00:18.840 - 01:00:23.030` | to add a mapping to the new virtual |
| `01:00:23.040 - 01:00:25.790` | address space for this page. |
| `01:00:25.800 - 01:00:27.870` | And if there's no problem, we keep |
| `01:00:27.880 - 01:00:29.910` | going, but if there is a problem, we |
| `01:00:29.920 - 01:00:31.790` | immediately free the page that we |
| `01:00:31.800 - 01:00:35.310` | allocated with KAlloc, and we go to air |
| `01:00:35.320 - 01:00:38.310` | again. So, um |
| `01:00:38.320 - 01:00:40.110` | then we keep looping |
| `01:00:40.120 - 01:00:43.150` | like this through all of the pages in |
| `01:00:43.160 - 01:00:45.950` | the old address space. So, we loop |
| `01:00:45.960 - 01:00:47.670` | through all of these pages, |
| `01:00:47.680 - 01:00:49.470` | copying each one of them and adding an |
| `01:00:49.480 - 01:00:51.390` | entry to the page table. |
| `01:00:51.400 - 01:00:54.390` | And then finally, if we make it through |
| `01:00:54.400 - 01:00:57.950` | all that without any problems, we return |
| `01:00:57.960 - 01:00:59.350` | zero. |
| `01:00:59.360 - 01:01:02.630` | But if we have a problem, then we need |
| `01:01:02.640 - 01:01:05.310` | to unmap everything and return minus |
| `01:01:05.320 - 01:01:08.390` | one. So, this is the new address space, |
| `01:01:08.400 - 01:01:10.190` | and from |
| `01:01:10.200 - 01:01:11.830` | virtual address zero, that is from the |
| `01:01:11.840 - 01:01:14.070` | beginning, um |
| `01:01:14.080 - 01:01:17.990` | through I, okay? I we need the |
| `01:01:18.000 - 01:01:21.190` | I is incremented by page size here, so |
| `01:01:21.200 - 01:01:23.950` | it goes in units of pages, and so we |
| `01:01:23.960 - 01:01:25.790` | divide by page size to determine the |
| `01:01:25.800 - 01:01:29.350` | number of pages to unmap, and we remove |
| `01:01:29.360 - 01:01:31.830` | them from the virtual |
| `01:01:31.840 - 01:01:35.190` | we remove them from the page table, and |
| `01:01:35.200 - 01:01:37.950` | the do free argument here indicates that |
| `01:01:37.960 - 01:01:41.190` | we return them to the |
| `01:01:41.200 - 01:01:43.550` | free pool by calling K free. |
| `01:01:43.560 - 01:01:46.270` | So, if anything goes wrong, we basically |
| `01:01:46.280 - 01:01:48.670` | undo what we've done and return minus |
| `01:01:48.680 - 01:01:51.550` | one. |
| `01:01:51.560 - 01:01:53.630` | Next, let's look at the UVM clear |
| `01:01:53.640 - 01:01:55.310` | function. |
| `01:01:55.320 - 01:01:57.510` | It's passed a virtual address, and it |
| `01:01:57.520 - 01:01:59.870` | will mark the corresponding page in the |
| `01:01:59.880 - 01:02:02.590` | page table as not accessible in user |
| `01:02:02.600 - 01:02:04.070` | mode. |
| `01:02:04.080 - 01:02:05.750` | It's used in only one spot, and that's |
| `01:02:05.760 - 01:02:08.950` | for marking the guard page in a user |
| `01:02:08.960 - 01:02:12.390` | address space as not accessible. So, |
| `01:02:12.400 - 01:02:15.110` | here's a picture of an address space. |
| `01:02:15.120 - 01:02:17.430` | Got the code and data. We've got one |
| `01:02:17.440 - 01:02:20.590` | page for the stack, the user stack, and |
| `01:02:20.600 - 01:02:22.630` | then we've got this one page called a |
| `01:02:22.640 - 01:02:25.670` | guard page here, and so UVM clear is |
| `01:02:25.680 - 01:02:29.590` | used to mark this particular page as not |
| `01:02:29.600 - 01:02:31.510` | accessible in user mode. So, if the user |
| `01:02:31.520 - 01:02:33.510` | program tries to |
| `01:02:33.520 - 01:02:35.390` | access this page, |
| `01:02:35.400 - 01:02:39.030` | then it would the the XV6 kernel will |
| `01:02:39.040 - 01:02:41.190` | catch that error and and throw that |
| `01:02:41.200 - 01:02:43.230` | process out. And that would happen if |
| `01:02:43.240 - 01:02:45.870` | the stack, which is growing downward |
| `01:02:45.880 - 01:02:50.470` | from this point, gets too big and |
| `01:02:50.480 - 01:02:52.590` | runs into the guard page. |
| `01:02:52.600 - 01:02:56.630` | And so, the code for UVM clear is |
| `01:02:56.640 - 01:02:58.870` | pretty straightforward. You might say |
| `01:02:58.880 - 01:03:00.150` | it's clear. |
| `01:03:00.160 - 01:03:01.670` | Here's a pointer to the page table and |
| `01:03:01.680 - 01:03:03.270` | the virtual address. |
| `01:03:03.280 - 01:03:05.630` | We walk the page table |
| `01:03:05.640 - 01:03:08.390` | to find a pointer to the page table |
| `01:03:08.400 - 01:03:10.750` | entry. This is a pointer. |
| `01:03:10.760 - 01:03:14.030` | And if there's a problem, |
| `01:03:14.040 - 01:03:16.950` | we immediately panic because we are only |
| `01:03:16.960 - 01:03:18.430` | calling this on the guard page that |
| `01:03:18.440 - 01:03:20.190` | should already be there. |
| `01:03:20.200 - 01:03:22.070` | And then we are |
| `01:03:22.080 - 01:03:25.390` | updating the page table entry. So, what |
| `01:03:25.400 - 01:03:28.230` | we're doing is we're ending it with um |
| `01:03:28.240 - 01:03:30.150` | a word that has all ones except for |
| `01:03:30.160 - 01:03:32.670` | where the U bit is uh which has a zero |
| `01:03:32.680 - 01:03:36.270` | there. So, that's a way to clear uh this |
| `01:03:36.280 - 01:03:40.710` | particular bit in the page table entry. |
| `01:03:40.720 - 01:03:43.310` | Next, let's take a look at the copy in |
| `01:03:43.320 - 01:03:45.150` | function. |
| `01:03:45.160 - 01:03:47.270` | It's passed a page table that describes |
| `01:03:47.280 - 01:03:49.110` | a virtual address space |
| `01:03:49.120 - 01:03:53.830` | and a virtual address within that space. |
| `01:03:53.840 - 01:03:57.830` | And it's passed as well a destination |
| `01:03:57.840 - 01:03:59.070` | location |
| `01:03:59.080 - 01:04:01.390` | and a length which is the number of |
| `01:04:01.400 - 01:04:03.470` | bytes that needs to be copied. |
| `01:04:03.480 - 01:04:06.150` | So, here in this picture is a virtual |
| `01:04:06.160 - 01:04:09.550` | address space and the source virtual |
| `01:04:09.560 - 01:04:11.110` | address is at the beginning of the red |
| `01:04:11.120 - 01:04:15.030` | zone here and the length is the number |
| `01:04:15.040 - 01:04:16.710` | of bytes. And you can see in this |
| `01:04:16.720 - 01:04:19.950` | example, we are trying to grab a number |
| `01:04:19.960 - 01:04:23.350` | of bytes and they overlap several pages. |
| `01:04:23.360 - 01:04:24.470` | And |
| `01:04:24.480 - 01:04:26.190` | when we actually look at where these |
| `01:04:26.200 - 01:04:29.790` | bytes are in memory, there's a virtual |
| `01:04:29.800 - 01:04:31.470` | uh there's a page table that maps each |
| `01:04:31.480 - 01:04:32.950` | of these virtual pages into some |
| `01:04:32.960 - 01:04:35.310` | physical page. So, for example, the |
| `01:04:35.320 - 01:04:37.470` | first page is is actually located right |
| `01:04:37.480 - 01:04:38.550` | here. |
| `01:04:38.560 - 01:04:39.830` | And what we want to do is we want to |
| `01:04:39.840 - 01:04:41.590` | copy these bytes which are actually |
| `01:04:41.600 - 01:04:44.070` | located in physical memory at you know, |
| `01:04:44.080 - 01:04:47.270` | here, here, here, and here. And we want |
| `01:04:47.280 - 01:04:50.750` | to copy them into some contiguous place |
| `01:04:50.760 - 01:04:53.910` | in the kernel's address space. |
| `01:04:53.920 - 01:04:56.750` | So, that's the destination location. |
| `01:04:56.760 - 01:04:58.470` | Okay, so we're going to copy bytes from |
| `01:04:58.480 - 01:05:01.230` | the user space to the kernel area and |
| `01:05:01.240 - 01:05:02.990` | we'll return minus one if there's any |
| `01:05:03.000 - 01:05:05.390` | kind of a problem. It may be that the |
| `01:05:05.400 - 01:05:07.750` | user process gave us an invalid virtual |
| `01:05:07.760 - 01:05:11.230` | address um or so. |
| `01:05:11.240 - 01:05:13.430` | Let's take a look at the code now. This |
| `01:05:13.440 - 01:05:14.630` | is uh |
| `01:05:14.640 - 01:05:17.390` | I think kind of tricky code to to |
| `01:05:17.400 - 01:05:20.950` | look at. So, um let's see if we can work |
| `01:05:20.960 - 01:05:23.270` | through this. Here's the page table. |
| `01:05:23.280 - 01:05:25.310` | And here's the destination pointer. Uh |
| `01:05:25.320 - 01:05:28.230` | here's the length in bytes and the |
| `01:05:28.240 - 01:05:30.270` | virtual address, the source uh virtual |
| `01:05:30.280 - 01:05:33.230` | address from where it's coming from. |
| `01:05:33.240 - 01:05:35.510` | So, first of all, we're going to copy in |
| `01:05:35.520 - 01:05:39.070` | chunks and uh length is the number of |
| `01:05:39.080 - 01:05:41.590` | bytes we have left to go. So, while we |
| `01:05:41.600 - 01:05:44.310` | have more bytes left to copy, uh we'll |
| `01:05:44.320 - 01:05:46.510` | keep doing this loop. And so, each |
| `01:05:46.520 - 01:05:48.710` | iteration of the loop is going to copy a |
| `01:05:48.720 - 01:05:50.910` | piece from one page. So, this piece, |
| `01:05:50.920 - 01:05:53.230` | then this piece, then this piece, and |
| `01:05:53.240 - 01:05:55.270` | then finally this piece. |
| `01:05:55.280 - 01:05:58.990` | So, we will be decrementing length, |
| `01:05:59.000 - 01:06:02.550` | okay? We will determine how many bytes |
| `01:06:02.560 - 01:06:03.790` | to copy, |
| `01:06:03.800 - 01:06:05.950` | and then we will copy that and decrement |
| `01:06:05.960 - 01:06:07.990` | length, and we'll increment the |
| `01:06:08.000 - 01:06:10.070` | destination pointer uh by the same |
| `01:06:10.080 - 01:06:11.310` | amount. |
| `01:06:11.320 - 01:06:13.870` | Um so, here you see the actual uh call |
| `01:06:13.880 - 01:06:17.350` | to memmove, which does the movement from |
| `01:06:17.360 - 01:06:20.910` | destination, and it moves n bytes. So, I |
| `01:06:20.920 - 01:06:21.990` | think we've |
| `01:06:22.000 - 01:06:25.550` | taken care of destination and length. |
| `01:06:25.560 - 01:06:27.830` | And if we manage to copy all the pieces |
| `01:06:27.840 - 01:06:29.790` | and exit this loop, |
| `01:06:29.800 - 01:06:32.150` | uh we have no more bytes to copy, we've |
| `01:06:32.160 - 01:06:34.510` | decremented length all the way to zero, |
| `01:06:34.520 - 01:06:38.190` | then we return uh zero. If there's any |
| `01:06:38.200 - 01:06:41.510` | problem, we're going to return a minus |
| `01:06:41.520 - 01:06:45.830` | So, uh the tricky part is the source, uh |
| `01:06:45.840 - 01:06:49.270` | and we have two um variables here, the |
| `01:06:49.280 - 01:06:53.750` | virtual address zero, VA0, and and PA0. |
| `01:06:53.760 - 01:06:55.070` | So, |
| `01:06:55.080 - 01:06:56.950` | let's see how this works. We're going to |
| `01:06:56.960 - 01:07:00.670` | start by taking the source location. |
| `01:07:00.680 - 01:07:03.830` | Let's go source uh VA here. |
| `01:07:03.840 - 01:07:04.990` | Um |
| `01:07:05.000 - 01:07:06.750` | source virtual address, we're going to |
| `01:07:06.760 - 01:07:09.670` | round it down to the nearest uh page |
| `01:07:09.680 - 01:07:11.830` | boundary. So, that gives us this virtual |
| `01:07:11.840 - 01:07:14.550` | address. And then we're going to call |
| `01:07:14.560 - 01:07:18.350` | walk address on that to turn it into the |
| `01:07:18.360 - 01:07:22.790` | physical page. So, that gives us the |
| `01:07:22.800 - 01:07:24.990` | pointer to the |
| `01:07:25.000 - 01:07:27.630` | page where it's kept in physical memory. |
| `01:07:27.640 - 01:07:30.190` | Uh we still don't know this address, but |
| `01:07:30.200 - 01:07:32.190` | we know this address here. |
| `01:07:32.200 - 01:07:35.190` | And we want to copy this bit here first. |
| `01:07:35.200 - 01:07:37.070` | That's the first thing we copy. |
| `01:07:37.080 - 01:07:39.070` | So, um |
| `01:07:39.080 - 01:07:41.710` | we check here to see whether we had any |
| `01:07:41.720 - 01:07:43.390` | problem. For example, if that the |
| `01:07:43.400 - 01:07:46.310` | virtual address is not mapped or um it's |
| `01:07:46.320 - 01:07:48.430` | not accessible in user mode, then we |
| `01:07:48.440 - 01:07:50.830` | would have a problem here and we just uh |
| `01:07:50.840 - 01:07:53.190` | return minus one. |
| `01:07:53.200 - 01:07:55.110` | So, |
| `01:07:55.120 - 01:07:58.350` | source VA minus VA0, what that's doing |
| `01:07:58.360 - 01:08:01.030` | is determining the offset. Source VA is |
| `01:08:01.040 - 01:08:04.510` | this this address here and VA0 is this |
| `01:08:04.520 - 01:08:06.350` | address. So, we're returning we're |
| `01:08:06.360 - 01:08:09.150` | computing the offset here. So, that's |
| `01:08:09.160 - 01:08:11.870` | what what what's what's happening here. |
| `01:08:11.880 - 01:08:14.270` | And um |
| `01:08:14.280 - 01:08:17.110` | page size uh |
| `01:08:17.120 - 01:08:20.710` | is the amount remaining. So, that's the |
| `01:08:20.720 - 01:08:23.430` | amount we have to copy, okay? |
| `01:08:23.440 - 01:08:26.390` | So, we've determined N, the amount we |
| `01:08:26.400 - 01:08:28.789` | have to copy. Now, |
| `01:08:28.799 - 01:08:30.349` | uh we check to make sure we haven't |
| `01:08:30.359 - 01:08:32.990` | exceeded the length, okay? So, in this |
| `01:08:33.000 - 01:08:35.470` | case we haven't for the first uh chunk |
| `01:08:35.480 - 01:08:37.990` | that we have to copy. Um but if we do, |
| `01:08:38.000 - 01:08:41.230` | we we shorten N to the length if it So, |
| `01:08:41.240 - 01:08:42.950` | we don't want to copy more bytes than we |
| `01:08:42.960 - 01:08:44.910` | are required to copy. |
| `01:08:44.920 - 01:08:47.269` | Now, we have to compute where to copy |
| `01:08:47.279 - 01:08:49.590` | from. So, we take the physical address, |
| `01:08:49.600 - 01:08:52.269` | that is the address of the |
| `01:08:52.279 - 01:08:54.390` | uh physical page where the memory is |
| `01:08:54.400 - 01:08:59.309` | located, and then we add to it |
| `01:08:59.319 - 01:09:02.710` | source VA minus VA0. That's this offset |
| `01:09:02.720 - 01:09:04.670` | right here. So, we're we're taking this |
| `01:09:04.680 - 01:09:06.910` | address here and adding this offset and |
| `01:09:06.920 - 01:09:08.950` | that gives us the location here. |
| `01:09:08.960 - 01:09:11.470` | So, now we're ready to do the copy and |
| `01:09:11.480 - 01:09:12.990` | we do the copy. |
| `01:09:13.000 - 01:09:14.910` | And then um |
| `01:09:14.920 - 01:09:18.789` | here we are incrementing source VA |
| `01:09:18.799 - 01:09:21.030` | to um |
| `01:09:21.040 - 01:09:25.349` | the next unit. So, VA zero is here |
| `01:09:25.359 - 01:09:28.349` | and we're adding 1K to it. So, now we |
| `01:09:28.359 - 01:09:29.190` | are |
| `01:09:29.200 - 01:09:31.390` | are pointed here. Okay, we've copied |
| `01:09:31.400 - 01:09:34.349` | this much and we're now pointed to this |
| `01:09:34.359 - 01:09:35.829` | place and we're ready to iterate the |
| `01:09:35.839 - 01:09:37.030` | loop. |
| `01:09:37.040 - 01:09:40.349` | And I think that uh covers it. Uh that |
| `01:09:40.359 - 01:09:43.910` | is copy in. |
| `01:09:43.920 - 01:09:46.590` | Next, let's look at copy out. We just |
| `01:09:46.600 - 01:09:49.590` | looked at copy in and copy out does uh |
| `01:09:49.600 - 01:09:51.510` | the same thing in reverse. |
| `01:09:51.520 - 01:09:55.550` | Okay, so uh copy in takes uh string |
| `01:09:55.560 - 01:09:57.070` | that's spread possibly spread across |
| `01:09:57.080 - 01:09:58.990` | several pages in the virtual address |
| `01:09:59.000 - 01:10:02.030` | space and copies it to some uh |
| `01:10:02.040 - 01:10:05.230` | contiguous locations in uh the kernel's |
| `01:10:05.240 - 01:10:07.270` | address space and copy out does the |
| `01:10:07.280 - 01:10:09.470` | exact reverse. It takes uh |
| `01:10:09.480 - 01:10:12.510` | something in uh a buffer in in the |
| `01:10:12.520 - 01:10:15.270` | kernel's address space and moves it into |
| `01:10:15.280 - 01:10:17.030` | the virtual address space spreading it |
| `01:10:17.040 - 01:10:20.750` | across uh several pages as necessary. |
| `01:10:20.760 - 01:10:24.230` | So, you see that it uh is passed a |
| `01:10:24.240 - 01:10:28.630` | source location and a length in bytes |
| `01:10:28.640 - 01:10:32.870` | and it uh copies that many bytes to the |
| `01:10:32.880 - 01:10:34.550` | destination virtual address in the |
| `01:10:34.560 - 01:10:36.750` | virtual address space. |
| `01:10:36.760 - 01:10:38.390` | Copies bytes from the kernel area to the |
| `01:10:38.400 - 01:10:40.950` | user space returning minus one if there |
| `01:10:40.960 - 01:10:42.630` | is any problem. |
| `01:10:42.640 - 01:10:45.470` | And we went over the code for |
| `01:10:45.480 - 01:10:49.110` | uh copy in and the code for copy out is |
| `01:10:49.120 - 01:10:51.870` | uh really uh almost identical. |
| `01:10:51.880 - 01:10:54.510` | Um so, here's copy in. |
| `01:10:54.520 - 01:10:56.510` | I I just want to like compare these two. |
| `01:10:56.520 - 01:10:59.870` | I just line them up. You have the exact |
| `01:10:59.880 - 01:11:04.990` | same number of lines. Um the |
| `01:11:05.000 - 01:11:06.590` | we can see copy in and we've got the |
| `01:11:06.600 - 01:11:08.870` | while loop and we've got the page round |
| `01:11:08.880 - 01:11:11.710` | down and walk address and |
| `01:11:11.720 - 01:11:15.350` | check to make sure PA0 is not zero and |
| `01:11:15.360 - 01:11:18.270` | computation of in and adjustment for |
| `01:11:18.280 - 01:11:21.270` | length and then the memory move and then |
| `01:11:21.280 - 01:11:23.750` | uh updating of the variables and then |
| `01:11:23.760 - 01:11:27.270` | returning zero. So, in copy in we see |
| `01:11:27.280 - 01:11:30.110` | destination. Here it's changed to |
| `01:11:30.120 - 01:11:32.470` | source. And here we see the source |
| `01:11:32.480 - 01:11:34.990` | virtual address and here it's changed to |
| `01:11:35.000 - 01:11:37.070` | destination virtual address. |
| `01:11:37.080 - 01:11:38.190` | And |
| `01:11:38.200 - 01:11:40.150` | we go when we go through this |
| `01:11:40.160 - 01:11:41.630` | we see that that's really all that's |
| `01:11:41.640 - 01:11:43.310` | happening. |
| `01:11:43.320 - 01:11:45.710` | Source virtual address changed to |
| `01:11:45.720 - 01:11:47.910` | destination virtual address. |
| `01:11:47.920 - 01:11:51.830` | Um and then everything's the same. |
| `01:11:51.840 - 01:11:54.190` | Here the computation of |
| `01:11:54.200 - 01:11:57.030` | how big the chunk we want to copy is. |
| `01:11:57.040 - 01:11:59.870` | Okay, this is the offset and this is |
| `01:11:59.880 - 01:12:01.590` | subtracting from the page size as a |
| `01:12:01.600 - 01:12:04.750` | whole and that's just uh |
| `01:12:04.760 - 01:12:06.550` | source virtual address is changed to |
| `01:12:06.560 - 01:12:08.630` | destination virtual address. |
| `01:12:08.640 - 01:12:09.750` | And then |
| `01:12:09.760 - 01:12:11.030` | in the move |
| `01:12:11.040 - 01:12:13.430` | here we were copying it from the virtual |
| `01:12:13.440 - 01:12:16.270` | address space to the destination. |
| `01:12:16.280 - 01:12:19.110` | Um and here we're copying it from the |
| `01:12:19.120 - 01:12:21.870` | source from the kernel's address space |
| `01:12:21.880 - 01:12:24.910` | and the destination is then this chunk |
| `01:12:24.920 - 01:12:28.830` | of the virtual address space. And again |
| `01:12:28.840 - 01:12:30.550` | we see destination changed to source |
| `01:12:30.560 - 01:12:31.550` | here. |
| `01:12:31.560 - 01:12:33.310` | And source |
| `01:12:33.320 - 01:12:34.750` | virtual address changed to destination |
| `01:12:34.760 - 01:12:36.990` | virtual address. So, there's a a nice |
| `01:12:37.000 - 01:12:39.550` | symmetry there. |
| `01:12:39.560 - 01:12:41.070` | The last function I want to look at is |
| `01:12:41.080 - 01:12:43.590` | copy in string. |
| `01:12:43.600 - 01:12:45.950` | So, to motivate it imagine that you've |
| `01:12:45.960 - 01:12:49.630` | got system call such as open that |
| `01:12:49.640 - 01:12:51.950` | provides a file name. That file name |
| `01:12:51.960 - 01:12:54.150` | will be located in the user's virtual |
| `01:12:54.160 - 01:12:56.230` | address space, and presumably it's a |
| `01:12:56.240 - 01:12:58.270` | string with a variable number of |
| `01:12:58.280 - 01:13:00.870` | characters terminated with a null |
| `01:13:00.880 - 01:13:02.070` | character. |
| `01:13:02.080 - 01:13:04.110` | And the kernel needs to get that file |
| `01:13:04.120 - 01:13:06.990` | name, and it's wants to it'll need to |
| `01:13:07.000 - 01:13:08.590` | copy that |
| `01:13:08.600 - 01:13:11.830` | string into some buffer. Well, the |
| `01:13:11.840 - 01:13:14.190` | kernel will have a fixed-size buffer, |
| `01:13:14.200 - 01:13:16.390` | and we don't know exactly how many |
| `01:13:16.400 - 01:13:18.630` | characters will be in that string before |
| `01:13:18.640 - 01:13:22.310` | we hit the final null. We don't want to |
| `01:13:22.320 - 01:13:24.110` | allow the kernel to overrun the buffer. |
| `01:13:24.120 - 01:13:27.630` | That would be disastrous, and so |
| `01:13:27.640 - 01:13:30.510` | we need a slight variation on this |
| `01:13:30.520 - 01:13:33.430` | copy in. So, we want to have a version |
| `01:13:33.440 - 01:13:37.710` | of copy in that will copy up until the |
| `01:13:37.720 - 01:13:40.750` | null character, but won't overflow the |
| `01:13:40.760 - 01:13:43.550` | buffer. So, |
| `01:13:43.560 - 01:13:46.510` | copy in string is passed a page table, |
| `01:13:46.520 - 01:13:49.350` | and the pointer to a pointer to the the |
| `01:13:49.360 - 01:13:52.070` | destination area, as well as the max |
| `01:13:52.080 - 01:13:54.710` | length. So, how many bytes we have in |
| `01:13:54.720 - 01:13:56.950` | the buffer to copy to. |
| `01:13:56.960 - 01:13:59.950` | And the source from where in the virtual |
| `01:13:59.960 - 01:14:02.150` | address space the characters will be |
| `01:14:02.160 - 01:14:03.230` | coming. |
| `01:14:03.240 - 01:14:05.350` | And what it does is it it's like copy |
| `01:14:05.360 - 01:14:08.230` | in. It copies bytes from the user space |
| `01:14:08.240 - 01:14:10.870` | to the kernel area, but it stops copying |
| `01:14:10.880 - 01:14:15.470` | after the null character. And if the |
| `01:14:15.480 - 01:14:18.270` | null byte is not reached before we copy |
| `01:14:18.280 - 01:14:20.470` | max length characters, |
| `01:14:20.480 - 01:14:22.310` | it stops, so it doesn't overrun the |
| `01:14:22.320 - 01:14:24.710` | buffer. And it also returns minus one in |
| `01:14:24.720 - 01:14:26.630` | that case to let us know there was an |
| `01:14:26.640 - 01:14:27.790` | error. |
| `01:14:27.800 - 01:14:30.550` | So, now let's look at the code. And |
| `01:14:30.560 - 01:14:32.150` | first I want to |
| `01:14:32.160 - 01:14:34.670` | look review copy in because it's really |
| `01:14:34.680 - 01:14:35.990` | quite similar. |
| `01:14:36.000 - 01:14:37.710` | So, |
| `01:14:37.720 - 01:14:39.710` | we've got |
| `01:14:39.720 - 01:14:41.390` | you know, this stuff here is is pretty |
| `01:14:41.400 - 01:14:43.550` | much the same. It's the memory move |
| `01:14:43.560 - 01:14:46.390` | where there's a difference. Okay, so |
| `01:14:46.400 - 01:14:49.910` | uh take a look at copy in string. |
| `01:14:49.920 - 01:14:52.390` | Again, it's a pointer to page uh pointer |
| `01:14:52.400 - 01:14:54.470` | to the page table, uh the destination |
| `01:14:54.480 - 01:14:55.550` | area, |
| `01:14:55.560 - 01:14:57.830` | uh the source virtual address, and the |
| `01:14:57.840 - 01:15:00.790` | maximum number of characters to copy. |
| `01:15:00.800 - 01:15:02.030` | So, |
| `01:15:02.040 - 01:15:04.830` | we've got a while loop here, and uh |
| `01:15:04.840 - 01:15:08.030` | instead of length, previously we had |
| `01:15:08.040 - 01:15:10.590` | length and the while loop uh |
| `01:15:10.600 - 01:15:12.590` | copied until we had length characters |
| `01:15:12.600 - 01:15:13.830` | copied. |
| `01:15:13.840 - 01:15:16.590` | Here we have max Here we have max, |
| `01:15:16.600 - 01:15:19.390` | and so the while loop copies until we |
| `01:15:19.400 - 01:15:22.230` | exceed max. And so, we will be |
| `01:15:22.240 - 01:15:24.190` | decrementing uh |
| `01:15:24.200 - 01:15:28.430` | max uh as we go, and uh as long as it's |
| `01:15:28.440 - 01:15:30.190` | uh greater than zero, we've still got |
| `01:15:30.200 - 01:15:32.910` | more characters to copy. But, uh we've |
| `01:15:32.920 - 01:15:35.910` | also got a flag here called got null. |
| `01:15:35.920 - 01:15:37.950` | Initially, we haven't seen the null byte |
| `01:15:37.960 - 01:15:39.910` | at the end of the string, so it it's uh |
| `01:15:39.920 - 01:15:41.710` | initialized to false. |
| `01:15:41.720 - 01:15:44.790` | And the minute we do see the null byte, |
| `01:15:44.800 - 01:15:47.270` | we'll set that flag to true, and that |
| `01:15:47.280 - 01:15:49.550` | will cause this loop to terminate. |
| `01:15:49.560 - 01:15:51.150` | And um |
| `01:15:51.160 - 01:15:53.190` | then uh if everything went well, and |
| `01:15:53.200 - 01:15:54.990` | we've we saw the null character, we |
| `01:15:55.000 - 01:15:56.630` | return zero, but if we didn't see it, we |
| `01:15:56.640 - 01:15:58.630` | return minus one. |
| `01:15:58.640 - 01:16:00.430` | Um |
| `01:16:00.440 - 01:16:03.070` | So, uh |
| `01:16:03.080 - 01:16:07.310` | this part here is pretty similar to uh |
| `01:16:07.320 - 01:16:09.350` | copy in string. So, let's just take a |
| `01:16:09.360 - 01:16:12.830` | quick look at that. Uh we are |
| `01:16:12.840 - 01:16:14.070` | try to line these up a little bit |
| `01:16:14.080 - 01:16:15.630` | better. Uh |
| `01:16:15.640 - 01:16:19.150` | we are uh copying we are rounding the |
| `01:16:19.160 - 01:16:21.390` | source location, the source virtual |
| `01:16:21.400 - 01:16:24.110` | address down to the nearest page, and |
| `01:16:24.120 - 01:16:26.310` | we're figuring out uh what is the |
| `01:16:26.320 - 01:16:28.590` | physical address of that page in in |
| `01:16:28.600 - 01:16:30.830` | memory, and uh we're checking for an |
| `01:16:30.840 - 01:16:33.790` | error here, and then we're figuring out |
| `01:16:33.800 - 01:16:36.390` | how many bytes to copy in that first |
| `01:16:36.400 - 01:16:39.430` | chunk, and possibly uh making sure that |
| `01:16:39.440 - 01:16:42.790` | we don't over copy here. Um but, then we |
| `01:16:42.800 - 01:16:45.550` | hit the mem move and that's replaced by |
| `01:16:45.560 - 01:16:47.430` | a loop that copies individual |
| `01:16:47.440 - 01:16:48.870` | characters. |
| `01:16:48.880 - 01:16:50.550` | So, um |
| `01:16:50.560 - 01:16:53.070` | in in copy n, |
| `01:16:53.080 - 01:16:56.550` | we are copying from this location, |
| `01:16:56.560 - 01:16:59.230` | physical address plus the offset here. |
| `01:16:59.240 - 01:17:00.310` | And so that's what we're doing here, |
| `01:17:00.320 - 01:17:02.590` | physical address plus the offset. And |
| `01:17:02.600 - 01:17:06.230` | that's where we copy from. And so P is |
| `01:17:06.240 - 01:17:08.110` | going to be our our pointer. |
| `01:17:08.120 - 01:17:10.590` | And instead of just uh copying n bytes |
| `01:17:10.600 - 01:17:13.310` | here into destination, what we're going |
| `01:17:13.320 - 01:17:16.270` | to do is a a while loop and we're going |
| `01:17:16.280 - 01:17:18.950` | to copy we're going to try to copy n |
| `01:17:18.960 - 01:17:23.430` | bytes in this while loop. And we um |
| `01:17:23.440 - 01:17:24.510` | are |
| `01:17:24.520 - 01:17:27.310` | um |
| `01:17:27.320 - 01:17:30.110` | looking at the the uh characters are |
| `01:17:30.120 - 01:17:34.070` | coming from the pointer P. So, we see if |
| `01:17:34.080 - 01:17:36.870` | we've got uh the null character and if |
| `01:17:36.880 - 01:17:40.310` | so, uh we copy it uh to the destination |
| `01:17:40.320 - 01:17:42.470` | and we set our flag and we are done with |
| `01:17:42.480 - 01:17:44.870` | this loop. Um |
| `01:17:44.880 - 01:17:46.990` | but uh if uh |
| `01:17:47.000 - 01:17:49.390` | uh |
| `01:17:49.400 - 01:17:52.110` | we uh don't have the null, then we just |
| `01:17:52.120 - 01:17:56.030` | copy the byte and then we uh decrement |
| `01:17:56.040 - 01:17:59.630` | our counter n, okay? And we also |
| `01:17:59.640 - 01:18:01.350` | decrement max, which is the number of |
| `01:18:01.360 - 01:18:03.910` | characters we have to go, incrementing P |
| `01:18:03.920 - 01:18:05.710` | and incrementing the destination |
| `01:18:05.720 - 01:18:08.310` | pointer. So, we're doing these um |
| `01:18:08.320 - 01:18:10.630` | by one here. Uh of course, we do the |
| `01:18:10.640 - 01:18:14.350` | loop uh a bunch of times n times. Here, |
| `01:18:14.360 - 01:18:16.710` | uh when we adjust the pointers, we do it |
| `01:18:16.720 - 01:18:19.590` | uh all at once. Uh we did n uh |
| `01:18:19.600 - 01:18:21.990` | iterations here, we copied n bytes, so |
| `01:18:22.000 - 01:18:24.470` | we can just adjust the the length in the |
| `01:18:24.480 - 01:18:28.190` | destination uh by n all at once. Uh so, |
| `01:18:28.200 - 01:18:31.070` | here we're uh adjusting the destination |
| `01:18:31.080 - 01:18:33.910` | one by one and the length uh |
| `01:18:33.920 - 01:18:35.870` | decrementing it one by one. |
| `01:18:35.880 - 01:18:38.470` | And uh but then uh we sort of continue |
| `01:18:38.480 - 01:18:43.110` | the same way. So here in copy in we are |
| `01:18:43.120 - 01:18:44.950` | figuring out the virtual address of the |
| `01:18:44.960 - 01:18:47.750` | next chunk we need to copy and we do |
| `01:18:47.760 - 01:18:51.150` | that the same way here. And so I think |
| `01:18:51.160 - 01:18:52.190` | that |
| `01:18:52.200 - 01:18:56.230` | finishes it and that is all that we have |
| `01:18:56.240 - 01:19:00.760` | for the file vm.c. |
