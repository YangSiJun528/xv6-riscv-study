# xv6 Kernel-38: Exec System Call

| Field | Value |
| --- | --- |
| Video ID | `bp0qo4-ozEg` |
| URL | <https://www.youtube.com/watch?v=bp0qo4-ozEg> |
| Source | local YouTube `en-orig` WebVTT captions |
| Status | converted to readable Markdown; recognition errors preserved |

---

## Raw Caption Text

WebVTT timing tags and repeated rolling-caption display text were removed. Caption recognition errors were not corrected.

| Time | Text |
| --- | --- |
| `00:00:01.199 - 00:00:02.790` | this video is part of a series on the |
| `00:00:02.800 - 00:00:05.829` | xv6 operating system kernel and is in |
| `00:00:05.839 - 00:00:09.669` | fact the last video in that series |
| `00:00:09.679 - 00:00:11.990` | in this video i'm going to describe how |
| `00:00:12.000 - 00:00:14.870` | the exec system call works this is |
| `00:00:14.880 - 00:00:16.790` | essentially a culmination of all you've |
| `00:00:16.800 - 00:00:19.670` | learned so far it's a pretty long video |
| `00:00:19.680 - 00:00:22.230` | but uh i think you'll see that it's |
| `00:00:22.240 - 00:00:24.070` | really quite interesting what happens in |
| `00:00:24.080 - 00:00:26.230` | the exec system call |
| `00:00:26.240 - 00:00:29.429` | i will go over the elf file format at |
| `00:00:29.439 - 00:00:32.790` | least in vague outline and then i will |
| `00:00:32.800 - 00:00:35.750` | cover the sys exec function |
| `00:00:35.760 - 00:00:38.790` | and the exec function from file |
| `00:00:38.800 - 00:00:42.229` | sysfile.c and exec.c |
| `00:00:42.239 - 00:00:45.910` | okay let's get started |
| `00:00:45.920 - 00:00:47.830` | in order to understand the exact system |
| `00:00:47.840 - 00:00:50.069` | call we need to understand how the |
| `00:00:50.079 - 00:00:53.029` | executable file is laid out |
| `00:00:53.039 - 00:00:56.069` | the executable file will be in the elf |
| `00:00:56.079 - 00:00:57.670` | file format |
| `00:00:57.680 - 00:00:59.590` | there are a number of different file |
| `00:00:59.600 - 00:01:00.950` | formats that are used on different |
| `00:01:00.960 - 00:01:04.869` | systems the elf format is used in the |
| `00:01:04.879 - 00:01:06.630` | unix world |
| `00:01:06.640 - 00:01:09.750` | in the apple world we have a file format |
| `00:01:09.760 - 00:01:12.950` | called mock o and for windows we have |
| `00:01:12.960 - 00:01:15.510` | the portable executable |
| `00:01:15.520 - 00:01:18.310` | file format these are all sort of the |
| `00:01:18.320 - 00:01:20.630` | same but the details different for each |
| `00:01:20.640 - 00:01:23.270` | one |
| `00:01:23.280 - 00:01:26.230` | the elf file will begin with a fixed |
| `00:01:26.240 - 00:01:27.350` | format |
| `00:01:27.360 - 00:01:29.350` | file header and that tells where the |
| `00:01:29.360 - 00:01:32.789` | other parts of the file are located |
| `00:01:32.799 - 00:01:35.510` | the header is followed by an array of |
| `00:01:35.520 - 00:01:36.950` | program headers |
| `00:01:36.960 - 00:01:39.749` | each program header is exactly the same |
| `00:01:39.759 - 00:01:42.789` | they have the same size and so on |
| `00:01:42.799 - 00:01:45.510` | and that will be followed by a number of |
| `00:01:45.520 - 00:01:46.710` | segments |
| `00:01:46.720 - 00:01:49.910` | the actual code that's to be loaded into |
| `00:01:49.920 - 00:01:53.830` | memory will be located in these segments |
| `00:01:53.840 - 00:01:55.590` | and these segments can have different |
| `00:01:55.600 - 00:01:58.069` | sizes as you can see here |
| `00:01:58.079 - 00:02:00.310` | each segment will be pointed to by |
| `00:02:00.320 - 00:02:02.870` | exactly one program header and that will |
| `00:02:02.880 - 00:02:05.590` | contain information about where in |
| `00:02:05.600 - 00:02:07.830` | the executable file the segment is |
| `00:02:07.840 - 00:02:09.589` | located |
| `00:02:09.599 - 00:02:11.190` | the segments can be followed by some |
| `00:02:11.200 - 00:02:12.790` | other stuff |
| `00:02:12.800 - 00:02:14.150` | for example |
| `00:02:14.160 - 00:02:16.229` | we can have something called the section |
| `00:02:16.239 - 00:02:17.430` | headers |
| `00:02:17.440 - 00:02:20.229` | in the case of relocatable files this |
| `00:02:20.239 - 00:02:22.470` | would contain information about how to |
| `00:02:22.480 - 00:02:25.030` | relocate the machine code that's in the |
| `00:02:25.040 - 00:02:26.070` | segments |
| `00:02:26.080 - 00:02:27.510` | we could also have some information |
| `00:02:27.520 - 00:02:30.150` | about debugging and so on |
| `00:02:30.160 - 00:02:32.710` | uh i won't be looking at this section |
| `00:02:32.720 - 00:02:34.869` | header stuff at all |
| `00:02:34.879 - 00:02:37.430` | now let's take a look at the file header |
| `00:02:37.440 - 00:02:38.470` | here |
| `00:02:38.480 - 00:02:43.430` | and i'm showing it in more detail here |
| `00:02:43.440 - 00:02:46.630` | there are a number of fields |
| `00:02:46.640 - 00:02:49.509` | and let's go through some of these |
| `00:02:49.519 - 00:02:52.390` | the first is the magic number and this |
| `00:02:52.400 - 00:02:56.470` | should contain exactly these four bytes |
| `00:02:56.480 - 00:02:58.550` | these can be checked to make sure we're |
| `00:02:58.560 - 00:03:01.589` | dealing with an l file if there's a |
| `00:03:01.599 - 00:03:04.630` | mismatch here then clearly this file is |
| `00:03:04.640 - 00:03:08.149` | not an elf format file |
| `00:03:08.159 - 00:03:10.229` | of course if the magic number is correct |
| `00:03:10.239 - 00:03:12.470` | the file could still contain errors |
| `00:03:12.480 - 00:03:16.070` | either due to bugs or perhaps malicious |
| `00:03:16.080 - 00:03:18.070` | code |
| `00:03:18.080 - 00:03:20.630` | the next field here indicates whether |
| `00:03:20.640 - 00:03:23.830` | this is a 32-bit file or whether the |
| `00:03:23.840 - 00:03:27.110` | data in this file is 64 bits |
| `00:03:27.120 - 00:03:30.229` | and the next field indicates whether the |
| `00:03:30.239 - 00:03:33.509` | data will be in little indian format or |
| `00:03:33.519 - 00:03:36.309` | big indian format |
| `00:03:36.319 - 00:03:38.789` | i'm not showing the exact sizes of these |
| `00:03:38.799 - 00:03:40.390` | fields here |
| `00:03:40.400 - 00:03:42.390` | and i'm not showing the offset within |
| `00:03:42.400 - 00:03:43.750` | the file header |
| `00:03:43.760 - 00:03:45.830` | these will actually change there are |
| `00:03:45.840 - 00:03:48.789` | different versions of the elf file |
| `00:03:48.799 - 00:03:51.430` | depending on whether it's 32 bits or 64 |
| `00:03:51.440 - 00:03:53.190` | bits |
| `00:03:53.200 - 00:03:56.149` | but let's just move on |
| `00:03:56.159 - 00:03:58.630` | the executable code could |
| `00:03:58.640 - 00:04:01.670` | be for one kind of a processor or for |
| `00:04:01.680 - 00:04:04.550` | another kind of processor and this field |
| `00:04:04.560 - 00:04:05.830` | called machine |
| `00:04:05.840 - 00:04:08.869` | indicates whether it's for uh for |
| `00:04:08.879 - 00:04:12.390` | example the x86 architecture or the x86 |
| `00:04:12.400 - 00:04:15.350` | 64 architecture there's also a code for |
| `00:04:15.360 - 00:04:17.909` | the risc-5 architecture so we would |
| `00:04:17.919 - 00:04:22.710` | expect this field to contain hex f3 |
| `00:04:22.720 - 00:04:24.310` | the |
| `00:04:24.320 - 00:04:27.350` | executable code might be intended to run |
| `00:04:27.360 - 00:04:29.510` | on a different kind of operating system |
| `00:04:29.520 - 00:04:30.230` | so |
| `00:04:30.240 - 00:04:33.270` | this field called os |
| `00:04:33.280 - 00:04:35.430` | application binary interface |
| `00:04:35.440 - 00:04:37.749` | contains a code to indicate which |
| `00:04:37.759 - 00:04:40.390` | operating system that this file is meant |
| `00:04:40.400 - 00:04:43.909` | to be run on there's not actually a code |
| `00:04:43.919 - 00:04:46.710` | for the x86 but we don't even look at |
| `00:04:46.720 - 00:04:48.469` | this field anyway so |
| `00:04:48.479 - 00:04:50.950` | that's that's okay |
| `00:04:50.960 - 00:04:53.350` | the elf file format is normally used for |
| `00:04:53.360 - 00:04:56.870` | executable files and it's also used for |
| `00:04:56.880 - 00:04:59.189` | relocatable files |
| `00:04:59.199 - 00:05:01.749` | when the compiler runs it will compile |
| `00:05:01.759 - 00:05:04.390` | the source code and produce an object |
| `00:05:04.400 - 00:05:05.270` | file |
| `00:05:05.280 - 00:05:08.469` | that is a dot o file and that file will |
| `00:05:08.479 - 00:05:10.469` | be a relocatable file |
| `00:05:10.479 - 00:05:12.550` | and in the unix world the relocatable |
| `00:05:12.560 - 00:05:15.590` | file will also be an elf file format |
| `00:05:15.600 - 00:05:18.150` | file so this indicates whether we've got |
| `00:05:18.160 - 00:05:20.150` | a relocatable file |
| `00:05:20.160 - 00:05:22.550` | or an executable file |
| `00:05:22.560 - 00:05:24.950` | the relocatable files the object files |
| `00:05:24.960 - 00:05:26.870` | will be processed by the linker which |
| `00:05:26.880 - 00:05:29.430` | will then produce a file in the |
| `00:05:29.440 - 00:05:31.430` | executable format |
| `00:05:31.440 - 00:05:33.749` | the l file format is also used for other |
| `00:05:33.759 - 00:05:37.430` | things for example for core files |
| `00:05:37.440 - 00:05:40.550` | when a program crashes or has some kind |
| `00:05:40.560 - 00:05:41.749` | of an error |
| `00:05:41.759 - 00:05:44.150` | sometimes we want to capture the |
| `00:05:44.160 - 00:05:46.710` | contents of main memory for the purposes |
| `00:05:46.720 - 00:05:48.390` | of debugging |
| `00:05:48.400 - 00:05:50.070` | in that case we can create what's called |
| `00:05:50.080 - 00:05:52.790` | a core file and that core file can also |
| `00:05:52.800 - 00:05:56.070` | be stored using the else file format |
| `00:05:56.080 - 00:05:58.230` | but we will only be looking at |
| `00:05:58.240 - 00:06:01.590` | executable files |
| `00:06:01.600 - 00:06:04.309` | the program header offset and program |
| `00:06:04.319 - 00:06:07.909` | header number indicate where in the file |
| `00:06:07.919 - 00:06:11.270` | we can find the array of program headers |
| `00:06:11.280 - 00:06:13.670` | so the offset says where in the file |
| `00:06:13.680 - 00:06:15.909` | this array begins that's this location |
| `00:06:15.919 - 00:06:16.710` | here |
| `00:06:16.720 - 00:06:19.510` | and the number indicates how many |
| `00:06:19.520 - 00:06:22.790` | program headers we can expect to find in |
| `00:06:22.800 - 00:06:24.870` | the elf file |
| `00:06:24.880 - 00:06:27.749` | and finally we have the entry point |
| `00:06:27.759 - 00:06:29.990` | this contains the address at which |
| `00:06:30.000 - 00:06:32.629` | execution should begin |
| `00:06:32.639 - 00:06:35.510` | this might be the first byte of the main |
| `00:06:35.520 - 00:06:36.710` | function |
| `00:06:36.720 - 00:06:39.189` | but typically there will also be some |
| `00:06:39.199 - 00:06:42.390` | prolog code in the executable file |
| `00:06:42.400 - 00:06:45.270` | the prolog code will be the code that |
| `00:06:45.280 - 00:06:47.670` | actually calls the main function |
| `00:06:47.680 - 00:06:48.950` | and so |
| `00:06:48.960 - 00:06:51.590` | in that case the entry point would |
| `00:06:51.600 - 00:06:54.469` | be the address of the first byte of the |
| `00:06:54.479 - 00:06:56.550` | prolog code |
| `00:06:56.560 - 00:06:59.189` | so after the kernel loads the executable |
| `00:06:59.199 - 00:07:02.390` | into memory it will then jump to the |
| `00:07:02.400 - 00:07:04.790` | entry point and execution will then |
| `00:07:04.800 - 00:07:07.029` | begin |
| `00:07:07.039 - 00:07:09.029` | in the xv6 system |
| `00:07:09.039 - 00:07:11.909` | we ignore a lot of these fields and in |
| `00:07:11.919 - 00:07:13.990` | fact we will only be looking at the |
| `00:07:14.000 - 00:07:15.670` | magic number |
| `00:07:15.680 - 00:07:19.029` | the entry point and the program header |
| `00:07:19.039 - 00:07:21.110` | offset and number to find the program |
| `00:07:21.120 - 00:07:22.150` | headers |
| `00:07:22.160 - 00:07:25.189` | the other fields uh will be completely |
| `00:07:25.199 - 00:07:26.550` | ignored |
| `00:07:26.560 - 00:07:29.110` | so now let's take a look at the program |
| `00:07:29.120 - 00:07:31.749` | headers so we have in this example four |
| `00:07:31.759 - 00:07:34.150` | of them and each one of these things has |
| `00:07:34.160 - 00:07:37.430` | a fixed format and here i'm showing the |
| `00:07:37.440 - 00:07:41.110` | program header structure |
| `00:07:41.120 - 00:07:42.710` | it has a number of |
| `00:07:42.720 - 00:07:46.150` | fields and as i said it's a fixed size |
| `00:07:46.160 - 00:07:48.309` | the first field is the |
| `00:07:48.319 - 00:07:50.070` | is the type field |
| `00:07:50.080 - 00:07:51.270` | and there are a number of different |
| `00:07:51.280 - 00:07:53.589` | types but we will only be concerned with |
| `00:07:53.599 - 00:07:54.629` | uh |
| `00:07:54.639 - 00:07:57.830` | loadable segments uh that's indicated by |
| `00:07:57.840 - 00:08:01.029` | a code number of one |
| `00:08:01.039 - 00:08:04.070` | the flags field indicates whether this |
| `00:08:04.080 - 00:08:05.110` | segment |
| `00:08:05.120 - 00:08:07.189` | should be marked as executable writable |
| `00:08:07.199 - 00:08:09.990` | or readable so these particular bits in |
| `00:08:10.000 - 00:08:13.110` | this flags word indicates whether the |
| `00:08:13.120 - 00:08:15.270` | segment when it's loaded into memory |
| `00:08:15.280 - 00:08:18.309` | should be marked as executable and or |
| `00:08:18.319 - 00:08:21.670` | writable and or readable |
| `00:08:21.680 - 00:08:22.469` | the |
| `00:08:22.479 - 00:08:26.550` | offset field tells where in the l file |
| `00:08:26.560 - 00:08:29.110` | we can find the data so this points to |
| `00:08:29.120 - 00:08:30.309` | this segment |
| `00:08:30.319 - 00:08:34.149` | and the file size field indicates how |
| `00:08:34.159 - 00:08:37.350` | much data is in that segment that is how |
| `00:08:37.360 - 00:08:38.829` | big the segment |
| `00:08:38.839 - 00:08:40.630` | is |
| `00:08:40.640 - 00:08:42.709` | the data in this segment will then be |
| `00:08:42.719 - 00:08:45.509` | moved into main memory by the kernel |
| `00:08:45.519 - 00:08:48.630` | when the executable file is loaded |
| `00:08:48.640 - 00:08:50.389` | so the |
| `00:08:50.399 - 00:08:52.310` | virtual address field tells where in |
| `00:08:52.320 - 00:08:54.389` | memory to place this |
| `00:08:54.399 - 00:08:56.790` | there's also a physical address field |
| `00:08:56.800 - 00:08:57.990` | in |
| `00:08:58.000 - 00:08:59.829` | most unix systems we are dealing with |
| `00:08:59.839 - 00:09:01.430` | virtual memory so |
| `00:09:01.440 - 00:09:03.590` | certainly in xv6 we are only concerned |
| `00:09:03.600 - 00:09:06.310` | with the virtual address so that's where |
| `00:09:06.320 - 00:09:08.710` | in the virtual address space we're going |
| `00:09:08.720 - 00:09:13.509` | to load or move this data segment |
| `00:09:13.519 - 00:09:17.110` | the physical address will be ignored in |
| `00:09:17.120 - 00:09:19.509` | xv6 |
| `00:09:19.519 - 00:09:21.190` | as i said the file size tells how many |
| `00:09:21.200 - 00:09:23.910` | bytes are in the file for the segment |
| `00:09:23.920 - 00:09:27.430` | the memory size tells how many bytes in |
| `00:09:27.440 - 00:09:30.150` | the virtual memory address space are to |
| `00:09:30.160 - 00:09:32.389` | be occupied by the segment |
| `00:09:32.399 - 00:09:35.670` | the memory size can actually be larger |
| `00:09:35.680 - 00:09:37.509` | than the file size |
| `00:09:37.519 - 00:09:39.750` | it cannot be smaller but it can be equal |
| `00:09:39.760 - 00:09:43.030` | to or larger than the file size |
| `00:09:43.040 - 00:09:44.710` | if it is larger |
| `00:09:44.720 - 00:09:46.389` | then we are |
| `00:09:46.399 - 00:09:48.230` | the kernel is supposed to fill in the |
| `00:09:48.240 - 00:09:51.030` | remaining bytes with zeros |
| `00:09:51.040 - 00:09:54.389` | so if we actually have zero bytes in the |
| `00:09:54.399 - 00:09:57.350` | file we can use the program header to |
| `00:09:57.360 - 00:09:59.990` | indicate a region of memory region of |
| `00:10:00.000 - 00:10:01.670` | virtual outer space that should be |
| `00:10:01.680 - 00:10:03.750` | entirely filled with |
| `00:10:03.760 - 00:10:05.829` | zeros so in that case the file size |
| `00:10:05.839 - 00:10:08.949` | would be zero and the entire segment in |
| `00:10:08.959 - 00:10:13.269` | the virtual memory would be zero filled |
| `00:10:13.279 - 00:10:15.590` | finally we have an alignment field |
| `00:10:15.600 - 00:10:17.990` | that's actually ignored |
| `00:10:18.000 - 00:10:21.750` | in fact we assume that the segment will |
| `00:10:21.760 - 00:10:24.630` | be loaded into a |
| `00:10:24.640 - 00:10:27.990` | an address that is page aligned |
| `00:10:28.000 - 00:10:31.030` | but in other systems we might have |
| `00:10:31.040 - 00:10:33.110` | different alignment requirements for the |
| `00:10:33.120 - 00:10:34.230` | segments |
| `00:10:34.240 - 00:10:36.630` | in xv6 all of our segments will be |
| `00:10:36.640 - 00:10:41.269` | aligned beginning on a page boundary |
| `00:10:41.279 - 00:10:43.509` | the file elf.h contains a number of |
| `00:10:43.519 - 00:10:45.509` | definitions that are related to the elf |
| `00:10:45.519 - 00:10:48.389` | file format so let's take a look at that |
| `00:10:48.399 - 00:10:51.110` | we begin with a structure that defines |
| `00:10:51.120 - 00:10:55.110` | the layout of the file header in the elf |
| `00:10:55.120 - 00:10:57.990` | file and so here we see a number of |
| `00:10:58.000 - 00:11:00.389` | fields that i showed |
| `00:11:00.399 - 00:11:03.030` | there's a loose correspondence here we |
| `00:11:03.040 - 00:11:05.990` | see the magic number we skip over a |
| `00:11:06.000 - 00:11:08.150` | bunch of stuff here we have the type |
| `00:11:08.160 - 00:11:11.430` | field the machine field |
| `00:11:11.440 - 00:11:13.269` | here's the |
| `00:11:13.279 - 00:11:15.750` | skip some stuff we have the entry |
| `00:11:15.760 - 00:11:18.710` | uh the program header all set and down |
| `00:11:18.720 - 00:11:21.990` | here we have the programs header count |
| `00:11:22.000 - 00:11:23.430` | we have some additional stuff for the |
| `00:11:23.440 - 00:11:25.910` | section headers and some other things |
| `00:11:25.920 - 00:11:26.710` | that |
| `00:11:26.720 - 00:11:29.670` | we will ignore as i said before we only |
| `00:11:29.680 - 00:11:32.069` | are concerned with the magic field the |
| `00:11:32.079 - 00:11:34.470` | entry field and the program header |
| `00:11:34.480 - 00:11:37.509` | offset and number |
| `00:11:37.519 - 00:11:39.030` | we have a |
| `00:11:39.040 - 00:11:40.949` | constant up here which is the magic |
| `00:11:40.959 - 00:11:44.870` | number i showed it here but we're giving |
| `00:11:44.880 - 00:11:47.430` | it in little indian format so we have |
| `00:11:47.440 - 00:11:51.590` | seven f four five four c and four six |
| `00:11:51.600 - 00:11:54.389` | okay given right here |
| `00:11:54.399 - 00:11:55.190` | and |
| `00:11:55.200 - 00:11:58.230` | uh then we have the |
| `00:11:58.240 - 00:12:01.590` | program header okay so let me |
| `00:12:01.600 - 00:12:04.949` | show the program header i described it |
| `00:12:04.959 - 00:12:05.990` | here |
| `00:12:06.000 - 00:12:07.350` | and |
| `00:12:07.360 - 00:12:10.310` | the correspondence is straightforward we |
| `00:12:10.320 - 00:12:11.509` | have the type |
| `00:12:11.519 - 00:12:12.870` | the flags |
| `00:12:12.880 - 00:12:15.829` | the offset the virtual address the |
| `00:12:15.839 - 00:12:18.790` | physical address which we don't use |
| `00:12:18.800 - 00:12:20.389` | the file size |
| `00:12:20.399 - 00:12:23.030` | and the memory size and finally the |
| `00:12:23.040 - 00:12:27.269` | alignment value which we also ignore |
| `00:12:27.279 - 00:12:30.470` | uh we have some other constants |
| `00:12:30.480 - 00:12:31.670` | in the |
| `00:12:31.680 - 00:12:34.230` | program header we have a type field and |
| `00:12:34.240 - 00:12:37.509` | here is the code number for an |
| `00:12:37.519 - 00:12:40.310` | executable segment a program |
| `00:12:40.320 - 00:12:42.870` | loadable executable segment |
| `00:12:42.880 - 00:12:46.310` | and here we have the codes for the bits |
| `00:12:46.320 - 00:12:47.350` | here |
| `00:12:47.360 - 00:12:50.710` | in the flags word or we have the |
| `00:12:50.720 - 00:12:53.509` | executable writable and readable bits |
| `00:12:53.519 - 00:12:55.190` | and those are just given |
| `00:12:55.200 - 00:13:01.190` | with these constants here |
| `00:13:01.200 - 00:13:03.750` | so here is the exact system call |
| `00:13:03.760 - 00:13:06.470` | it's passed two arguments the first is a |
| `00:13:06.480 - 00:13:08.470` | pointer to a path name that describes |
| `00:13:08.480 - 00:13:10.949` | the executable file that we like to load |
| `00:13:10.959 - 00:13:13.910` | and execute and the second argument is a |
| `00:13:13.920 - 00:13:16.389` | pointer to an array and these are the |
| `00:13:16.399 - 00:13:18.710` | arguments that will be passed to the |
| `00:13:18.720 - 00:13:21.430` | executable file this array |
| `00:13:21.440 - 00:13:23.670` | contains pointers to strings that are |
| `00:13:23.680 - 00:13:25.110` | null terminated |
| `00:13:25.120 - 00:13:28.629` | and it ends with an entry that is a null |
| `00:13:28.639 - 00:13:30.790` | pointer and |
| `00:13:30.800 - 00:13:35.110` | these are in the virtual address space |
| `00:13:35.120 - 00:13:37.910` | in unix systems there are several other |
| `00:13:37.920 - 00:13:40.790` | variants of the exact system call |
| `00:13:40.800 - 00:13:41.590` | but |
| `00:13:41.600 - 00:13:44.150` | xv6 has just this one and this is the |
| `00:13:44.160 - 00:13:45.750` | main one that gives you the idea of |
| `00:13:45.760 - 00:13:47.110` | what's going on |
| `00:13:47.120 - 00:13:49.750` | so when this system call happens the |
| `00:13:49.760 - 00:13:51.990` | kernel will go into |
| `00:13:52.000 - 00:13:54.870` | this sysex function this is coming out |
| `00:13:54.880 - 00:13:57.590` | of the file sysfile.c |
| `00:13:57.600 - 00:14:00.949` | and what it needs to do is get these |
| `00:14:00.959 - 00:14:03.350` | strings this argument stuff |
| `00:14:03.360 - 00:14:06.069` | from the virtual address space of the |
| `00:14:06.079 - 00:14:09.430` | process that invoke the exec system call |
| `00:14:09.440 - 00:14:11.990` | and then it will call |
| `00:14:12.000 - 00:14:14.550` | the function exec to actually do the |
| `00:14:14.560 - 00:14:15.350` | work |
| `00:14:15.360 - 00:14:19.990` | so let's step through the sysex function |
| `00:14:20.000 - 00:14:23.269` | the first thing it's going to do is copy |
| `00:14:23.279 - 00:14:26.230` | the path argument and we saw the |
| `00:14:26.240 - 00:14:28.870` | argument string function earlier |
| `00:14:28.880 - 00:14:30.710` | and it's going to copy that into a local |
| `00:14:30.720 - 00:14:32.870` | variable called path |
| `00:14:32.880 - 00:14:34.710` | the second thing it's going to do is get |
| `00:14:34.720 - 00:14:37.509` | the second argument this is an a0 this |
| `00:14:37.519 - 00:14:41.030` | is an a1 and it's going to copy the |
| `00:14:41.040 - 00:14:43.189` | address of the array that's a virtual |
| `00:14:43.199 - 00:14:46.629` | address into this local variable uart |
| `00:14:46.639 - 00:14:47.509` | so |
| `00:14:47.519 - 00:14:50.629` | at this point i can just |
| `00:14:50.639 - 00:14:52.470` | draw this line here to indicate that |
| `00:14:52.480 - 00:14:55.590` | that gets stored |
| `00:14:55.600 - 00:14:57.590` | the next thing it's going to do well |
| `00:14:57.600 - 00:15:01.110` | we've got this rgv array and |
| `00:15:01.120 - 00:15:04.310` | it's fixed size it can accommodate up to |
| `00:15:04.320 - 00:15:05.590` | 32 |
| `00:15:05.600 - 00:15:07.030` | uh elements |
| `00:15:07.040 - 00:15:08.870` | actually that would be 31 arguments |
| `00:15:08.880 - 00:15:10.230` | because the last argument has to be |
| `00:15:10.240 - 00:15:12.710` | followed with a null pointer and that's |
| `00:15:12.720 - 00:15:16.150` | given by this max arg constant so the |
| `00:15:16.160 - 00:15:19.189` | next thing we do here is we set all |
| `00:15:19.199 - 00:15:22.310` | entries in that array to zero |
| `00:15:22.320 - 00:15:24.629` | so here i'm showing the array so let me |
| `00:15:24.639 - 00:15:26.389` | just draw it like that |
| `00:15:26.399 - 00:15:29.110` | and we're setting all the elements to |
| `00:15:29.120 - 00:15:31.430` | null okay later we'll go ahead and |
| `00:15:31.440 - 00:15:33.750` | change them |
| `00:15:33.760 - 00:15:35.189` | then we have |
| `00:15:35.199 - 00:15:37.350` | a loop here which is going to walk |
| `00:15:37.360 - 00:15:39.189` | through all of the arguments so it |
| `00:15:39.199 - 00:15:42.230` | starts at i zero and just increments |
| `00:15:42.240 - 00:15:44.470` | okay and the first thing we do is check |
| `00:15:44.480 - 00:15:46.710` | to make sure we haven't gotten more |
| `00:15:46.720 - 00:15:49.350` | arguments than we can accommodate so we |
| `00:15:49.360 - 00:15:52.870` | have to have room for um uh we have room |
| `00:15:52.880 - 00:15:55.189` | for up to 31 arguments plus the final |
| `00:15:55.199 - 00:15:56.949` | null so here we're checking to see |
| `00:15:56.959 - 00:15:59.110` | whether we have gone past |
| `00:15:59.120 - 00:16:01.910` | uh the last element and if so we go to |
| `00:16:01.920 - 00:16:04.710` | this label bad okay i'll come to that in |
| `00:16:04.720 - 00:16:06.389` | just a second but basically we're going |
| `00:16:06.399 - 00:16:11.829` | to return -1 at the label bad |
| `00:16:11.839 - 00:16:14.870` | next what we do is we |
| `00:16:14.880 - 00:16:16.470` | go into |
| `00:16:16.480 - 00:16:17.509` | the |
| `00:16:17.519 - 00:16:19.509` | virtual address space |
| `00:16:19.519 - 00:16:21.269` | okay |
| `00:16:21.279 - 00:16:23.269` | and we fetch |
| `00:16:23.279 - 00:16:24.710` | the |
| `00:16:24.720 - 00:16:26.150` | value |
| `00:16:26.160 - 00:16:29.269` | that is stored at some particular |
| `00:16:29.279 - 00:16:31.189` | location and what location are we |
| `00:16:31.199 - 00:16:33.350` | interested in well we're interested in |
| `00:16:33.360 - 00:16:34.389` | the |
| `00:16:34.399 - 00:16:36.710` | address of the user's |
| `00:16:36.720 - 00:16:40.389` | array plus i times the size of the uh |
| `00:16:40.399 - 00:16:42.389` | entries okay so |
| `00:16:42.399 - 00:16:44.230` | this is the user's |
| `00:16:44.240 - 00:16:47.269` | array and we add i and so we go in and |
| `00:16:47.279 - 00:16:50.629` | we get a pointer okay to one of these |
| `00:16:50.639 - 00:16:53.430` | strings and then we store it in a local |
| `00:16:53.440 - 00:16:56.710` | variable called u arg okay so urg is a |
| `00:16:56.720 - 00:16:58.870` | local variable here |
| `00:16:58.880 - 00:17:02.790` | uh let me just uh illustrate this um for |
| `00:17:02.800 - 00:17:04.470` | one of the |
| `00:17:04.480 - 00:17:06.390` | examples let's assume we've gone through |
| `00:17:06.400 - 00:17:09.189` | a couple of times and now we're on uh i |
| `00:17:09.199 - 00:17:11.669` | equals uh two here so we've already |
| `00:17:11.679 - 00:17:14.549` | filled in a couple of elements but um |
| `00:17:14.559 - 00:17:15.510` | on |
| `00:17:15.520 - 00:17:18.069` | the iteration of this loop where i is |
| `00:17:18.079 - 00:17:19.350` | now two |
| `00:17:19.360 - 00:17:21.110` | we uh fetch |
| `00:17:21.120 - 00:17:24.230` | this pointer and store it in uarg okay |
| `00:17:24.240 - 00:17:27.110` | so uh we go to the |
| `00:17:27.120 - 00:17:29.830` | this element here and we fetch that so |
| `00:17:29.840 - 00:17:32.630` | now you are points to this string that's |
| `00:17:32.640 - 00:17:33.590` | it |
| `00:17:33.600 - 00:17:35.990` | sorry you are points to this string here |
| `00:17:36.000 - 00:17:39.830` | the second element |
| `00:17:39.840 - 00:17:42.310` | uh if there's a problem then we go to |
| `00:17:42.320 - 00:17:43.190` | bad |
| `00:17:43.200 - 00:17:46.549` | but um then we check to see whether um |
| `00:17:46.559 - 00:17:49.029` | we got something okay |
| `00:17:49.039 - 00:17:51.590` | if we got the last element here if we |
| `00:17:51.600 - 00:17:53.029` | got a null |
| `00:17:53.039 - 00:17:57.190` | then we are done we need to store a null |
| `00:17:57.200 - 00:17:58.070` | in the |
| `00:17:58.080 - 00:18:01.510` | arg v array and we do that and then we |
| `00:18:01.520 - 00:18:03.909` | break out of the loop |
| `00:18:03.919 - 00:18:05.990` | otherwise what we're going to do is copy |
| `00:18:06.000 - 00:18:08.390` | the string remember that these strings |
| `00:18:08.400 - 00:18:09.350` | are in |
| `00:18:09.360 - 00:18:11.990` | the virtual address space and we need to |
| `00:18:12.000 - 00:18:14.789` | move them into kernel's memory somewhere |
| `00:18:14.799 - 00:18:16.630` | so the rgv array |
| `00:18:16.640 - 00:18:18.710` | is a local memory for this particular |
| `00:18:18.720 - 00:18:20.870` | function and what we're going to do is |
| `00:18:20.880 - 00:18:23.510` | allocate a page for each and every |
| `00:18:23.520 - 00:18:24.630` | string |
| `00:18:24.640 - 00:18:26.390` | so for |
| `00:18:26.400 - 00:18:29.110` | this string we're going to allocate an |
| `00:18:29.120 - 00:18:32.470` | entire page and copy it so here we're uh |
| `00:18:32.480 - 00:18:34.549` | here's the page we allocate and we're |
| `00:18:34.559 - 00:18:36.150` | going to copy all the characters up to |
| `00:18:36.160 - 00:18:39.909` | the terminating null into this page |
| `00:18:39.919 - 00:18:42.710` | so that's what's going on um |
| `00:18:42.720 - 00:18:45.190` | here we allocate the page |
| `00:18:45.200 - 00:18:46.150` | okay |
| `00:18:46.160 - 00:18:48.470` | and uh if we uh failed with the |
| `00:18:48.480 - 00:18:52.070` | allocation then we go to this bad label |
| `00:18:52.080 - 00:18:55.270` | but otherwise we call fetch string |
| `00:18:55.280 - 00:18:58.070` | and fetch string is past the virtual |
| `00:18:58.080 - 00:18:59.110` | address |
| `00:18:59.120 - 00:19:01.909` | okay and it's past the um place where |
| `00:19:01.919 - 00:19:03.110` | we're going to |
| `00:19:03.120 - 00:19:06.470` | copy it to okay that's uh |
| `00:19:06.480 - 00:19:07.990` | the pointer |
| `00:19:08.000 - 00:19:08.950` | to |
| `00:19:08.960 - 00:19:11.190` | the uh page |
| `00:19:11.200 - 00:19:13.270` | okay up here uh after we allocated the |
| `00:19:13.280 - 00:19:15.590` | page we saved it uh so i forgot to draw |
| `00:19:15.600 - 00:19:17.430` | that line so let's go ahead and save the |
| `00:19:17.440 - 00:19:18.470` | pointer |
| `00:19:18.480 - 00:19:21.750` | to this page this newly allocated page |
| `00:19:21.760 - 00:19:24.470` | and then we call fetch string which will |
| `00:19:24.480 - 00:19:25.430` | copy |
| `00:19:25.440 - 00:19:26.710` | up to |
| `00:19:26.720 - 00:19:30.150` | a full page worth of bytes into |
| `00:19:30.160 - 00:19:31.990` | the |
| `00:19:32.000 - 00:19:34.470` | page that we just allocated in kernel |
| `00:19:34.480 - 00:19:35.590` | space |
| `00:19:35.600 - 00:19:39.029` | this particular function will copy the |
| `00:19:39.039 - 00:19:42.310` | final null byte and it will return -1 if |
| `00:19:42.320 - 00:19:44.310` | there wasn't enough room to copy the |
| `00:19:44.320 - 00:19:47.029` | entire string plus the null byte and if |
| `00:19:47.039 - 00:19:49.669` | there's a problem with that we go to bad |
| `00:19:49.679 - 00:19:52.630` | okay so now we can take a look at bad |
| `00:19:52.640 - 00:19:55.270` | that is just going to loop through |
| `00:19:55.280 - 00:19:58.390` | this array loop through the rv array and |
| `00:19:58.400 - 00:20:01.029` | for every element that is not null |
| `00:20:01.039 - 00:20:04.070` | we will just call kfree to get rid of |
| `00:20:04.080 - 00:20:06.470` | these pages return them to the free pool |
| `00:20:06.480 - 00:20:09.190` | and then we'll return minus one |
| `00:20:09.200 - 00:20:11.510` | but assuming that uh we go through all |
| `00:20:11.520 - 00:20:14.470` | the strings and finally exit when we hit |
| `00:20:14.480 - 00:20:15.590` | the |
| `00:20:15.600 - 00:20:16.390` | null |
| `00:20:16.400 - 00:20:18.070` | pointer |
| `00:20:18.080 - 00:20:20.870` | we will then call the exact function |
| `00:20:20.880 - 00:20:21.990` | passing it |
| `00:20:22.000 - 00:20:24.549` | the local variable path |
| `00:20:24.559 - 00:20:26.230` | okay that's here |
| `00:20:26.240 - 00:20:27.270` | and |
| `00:20:27.280 - 00:20:29.430` | the rv array |
| `00:20:29.440 - 00:20:31.110` | the pointer to that array |
| `00:20:31.120 - 00:20:33.029` | and we call |
| `00:20:33.039 - 00:20:34.630` | exec exec |
| `00:20:34.640 - 00:20:37.510` | ideally will not return it will result |
| `00:20:37.520 - 00:20:40.710` | in the executable file being loaded the |
| `00:20:40.720 - 00:20:42.470` | previous process |
| `00:20:42.480 - 00:20:44.549` | will have its memory returned and we |
| `00:20:44.559 - 00:20:46.470` | will never come back here but if there's |
| `00:20:46.480 - 00:20:48.470` | any problem this function will return a |
| `00:20:48.480 - 00:20:52.149` | minus one and then we can return -1 to |
| `00:20:52.159 - 00:20:53.190` | the |
| `00:20:53.200 - 00:20:55.029` | user process that invoked the exact |
| `00:20:55.039 - 00:20:57.590` | system call but before we do that again |
| `00:20:57.600 - 00:21:00.230` | we need to go through this array and |
| `00:21:00.240 - 00:21:02.789` | free all of the elements that have been |
| `00:21:02.799 - 00:21:05.750` | allocated |
| `00:21:05.760 - 00:21:07.270` | next let's take a look at the exact |
| `00:21:07.280 - 00:21:10.470` | function from the file exec.c |
| `00:21:10.480 - 00:21:12.149` | this function will complete the work of |
| `00:21:12.159 - 00:21:14.149` | the exact system call |
| `00:21:14.159 - 00:21:17.110` | it's passed a string which is the path |
| `00:21:17.120 - 00:21:19.350` | name of the executable file and a |
| `00:21:19.360 - 00:21:20.950` | pointer to an array |
| `00:21:20.960 - 00:21:23.190` | that array contains pointers to strings |
| `00:21:23.200 - 00:21:25.270` | and those are the arguments to pass to |
| `00:21:25.280 - 00:21:28.789` | the new executable program |
| `00:21:28.799 - 00:21:32.470` | it will return -1 if there is a problem |
| `00:21:32.480 - 00:21:34.549` | but if everything goes okay then it will |
| `00:21:34.559 - 00:21:37.990` | begin executing the new program in the |
| `00:21:38.000 - 00:21:41.510` | new virtual address space |
| `00:21:41.520 - 00:21:44.390` | so it begins by starting a transaction |
| `00:21:44.400 - 00:21:47.590` | and calling the name i function |
| `00:21:47.600 - 00:21:50.710` | name i is passed the path name of a file |
| `00:21:50.720 - 00:21:52.310` | and it will go out and make sure that |
| `00:21:52.320 - 00:21:56.230` | that file exists and load an inode for |
| `00:21:56.240 - 00:21:59.909` | that file into memory return a pointer |
| `00:21:59.919 - 00:22:01.430` | to that inode |
| `00:22:01.440 - 00:22:03.830` | if anything goes wrong then the |
| `00:22:03.840 - 00:22:06.230` | transaction will be ended and we'll just |
| `00:22:06.240 - 00:22:08.710` | return minus one but if everything's |
| `00:22:08.720 - 00:22:10.470` | okay then the |
| `00:22:10.480 - 00:22:12.390` | inode will have its reference count |
| `00:22:12.400 - 00:22:14.070` | incremented and i've shown that with |
| `00:22:14.080 - 00:22:15.669` | this line here |
| `00:22:15.679 - 00:22:17.830` | and then the next thing we do is we lock |
| `00:22:17.840 - 00:22:20.549` | that inode |
| `00:22:20.559 - 00:22:24.390` | after that we begin by reading in the |
| `00:22:24.400 - 00:22:26.549` | header from the elf file |
| `00:22:26.559 - 00:22:28.789` | so we've got this local variable here |
| `00:22:28.799 - 00:22:31.029` | called elf which will contain a header |
| `00:22:31.039 - 00:22:32.070` | structure |
| `00:22:32.080 - 00:22:33.190` | and |
| `00:22:33.200 - 00:22:36.310` | we read in from offset 0 |
| `00:22:36.320 - 00:22:38.870` | the structure and we place it into this |
| `00:22:38.880 - 00:22:41.270` | local variable and this flag indicates |
| `00:22:41.280 - 00:22:43.430` | that that variable is in kernel space |
| `00:22:43.440 - 00:22:45.750` | and this is the inode pointer and if |
| `00:22:45.760 - 00:22:48.549` | there's anything wrong we will jump to |
| `00:22:48.559 - 00:22:50.710` | this bad label which i'll discuss in a |
| `00:22:50.720 - 00:22:51.750` | moment |
| `00:22:51.760 - 00:22:53.510` | but if everything's okay |
| `00:22:53.520 - 00:22:55.669` | we next check the |
| `00:22:55.679 - 00:23:00.070` | magic number for the uh elf file and the |
| `00:23:00.080 - 00:23:02.549` | magic number is this uh |
| `00:23:02.559 - 00:23:04.710` | constant of seven f four five four c |
| `00:23:04.720 - 00:23:05.750` | four six |
| `00:23:05.760 - 00:23:08.390` | and uh assuming it's okay then we keep |
| `00:23:08.400 - 00:23:09.590` | on going |
| `00:23:09.600 - 00:23:10.870` | and |
| `00:23:10.880 - 00:23:13.350` | the next thing we do is we call proc |
| `00:23:13.360 - 00:23:15.270` | page table |
| `00:23:15.280 - 00:23:17.190` | now this code is executing within a |
| `00:23:17.200 - 00:23:19.669` | process and that process has a virtual |
| `00:23:19.679 - 00:23:21.990` | address space and if anything goes wrong |
| `00:23:22.000 - 00:23:23.990` | we will have to |
| `00:23:24.000 - 00:23:26.870` | return -1 and return to executing the |
| `00:23:26.880 - 00:23:28.470` | code that's in that virtual address |
| `00:23:28.480 - 00:23:29.350` | space |
| `00:23:29.360 - 00:23:31.590` | but if things go okay then we're going |
| `00:23:31.600 - 00:23:32.310` | to |
| `00:23:32.320 - 00:23:34.950` | create a new virtual address space |
| `00:23:34.960 - 00:23:37.430` | and load the executable program into |
| `00:23:37.440 - 00:23:40.230` | that virtual address space so here we're |
| `00:23:40.240 - 00:23:42.789` | creating the second or the new virtual |
| `00:23:42.799 - 00:23:44.950` | address space and this local variable |
| `00:23:44.960 - 00:23:47.270` | page table will point to the newly |
| `00:23:47.280 - 00:23:49.190` | created page table |
| `00:23:49.200 - 00:23:50.470` | this function here |
| `00:23:50.480 - 00:23:51.590` | needs to |
| `00:23:51.600 - 00:23:53.669` | have a pointer to the current proc |
| `00:23:53.679 - 00:23:55.029` | structure |
| `00:23:55.039 - 00:23:57.110` | because it needs to add a trap frame to |
| `00:23:57.120 - 00:23:59.270` | the top of this virtual address space |
| `00:23:59.280 - 00:24:01.590` | and it needs to know which physical page |
| `00:24:01.600 - 00:24:04.630` | to use for that particular uh page in |
| `00:24:04.640 - 00:24:06.310` | the virtual address space |
| `00:24:06.320 - 00:24:09.110` | if there is any problem in allocating |
| `00:24:09.120 - 00:24:12.230` | this page table then again we will jump |
| `00:24:12.240 - 00:24:14.630` | to this bad label but otherwise we keep |
| `00:24:14.640 - 00:24:17.269` | going so let's continue here |
| `00:24:17.279 - 00:24:20.470` | and the next thing we see is a for loop |
| `00:24:20.480 - 00:24:22.470` | and what this for loop is going to do is |
| `00:24:22.480 - 00:24:23.750` | go through |
| `00:24:23.760 - 00:24:24.710` | the |
| `00:24:24.720 - 00:24:27.590` | program headers remember that the elf |
| `00:24:27.600 - 00:24:29.909` | file contains an array of program |
| `00:24:29.919 - 00:24:30.870` | headers |
| `00:24:30.880 - 00:24:33.269` | so that's what this loop is going to go |
| `00:24:33.279 - 00:24:35.430` | through |
| `00:24:35.440 - 00:24:36.630` | here we see |
| `00:24:36.640 - 00:24:39.669` | offset and that's the offset of the |
| `00:24:39.679 - 00:24:42.310` | first program header and we are |
| `00:24:42.320 - 00:24:44.390` | incrementing by the size of the program |
| `00:24:44.400 - 00:24:47.590` | headers here we've also got this index i |
| `00:24:47.600 - 00:24:50.230` | which is being incremented and that |
| `00:24:50.240 - 00:24:52.390` | allows us to stop after we've done each |
| `00:24:52.400 - 00:24:55.430` | of the program headers |
| `00:24:55.440 - 00:24:56.950` | we call redi |
| `00:24:56.960 - 00:25:00.070` | here to read in the first or the next |
| `00:25:00.080 - 00:25:02.710` | program header into this local variable |
| `00:25:02.720 - 00:25:03.830` | ph |
| `00:25:03.840 - 00:25:06.149` | so going back to the top of the exact |
| `00:25:06.159 - 00:25:08.710` | function we see the local variable ph |
| `00:25:08.720 - 00:25:10.470` | here which is big enough to hold a |
| `00:25:10.480 - 00:25:13.350` | structure for the program header |
| `00:25:13.360 - 00:25:16.310` | and this is the inode and this address |
| `00:25:16.320 - 00:25:19.269` | here is in kernel memory this is the |
| `00:25:19.279 - 00:25:21.269` | offset the current offset for the |
| `00:25:21.279 - 00:25:23.110` | program header and if there's any |
| `00:25:23.120 - 00:25:27.110` | problem we go to the bad label |
| `00:25:27.120 - 00:25:29.269` | then we check the type field of the |
| `00:25:29.279 - 00:25:31.669` | program header |
| `00:25:31.679 - 00:25:32.549` | the |
| `00:25:32.559 - 00:25:34.710` | type field had better be |
| `00:25:34.720 - 00:25:37.750` | a 1 to indicate that it is a loadable |
| `00:25:37.760 - 00:25:39.750` | segment |
| `00:25:39.760 - 00:25:40.870` | there |
| `00:25:40.880 - 00:25:42.789` | might be some other kinds of segments |
| `00:25:42.799 - 00:25:44.390` | although i don't know what other kind of |
| `00:25:44.400 - 00:25:46.470` | segment we might find in an executable |
| `00:25:46.480 - 00:25:48.710` | file but if we find something besides |
| `00:25:48.720 - 00:25:50.789` | that we would just continue and repeat |
| `00:25:50.799 - 00:25:51.990` | this loop |
| `00:25:52.000 - 00:25:53.909` | that is we would skip anything that is |
| `00:25:53.919 - 00:25:56.390` | not a loadable segment |
| `00:25:56.400 - 00:25:57.909` | but if it is a loadable segment we |
| `00:25:57.919 - 00:25:59.669` | continue here and the next thing we |
| `00:25:59.679 - 00:26:02.149` | check is memsize and we make sure that |
| `00:26:02.159 - 00:26:05.669` | it is not less than file size |
| `00:26:05.679 - 00:26:07.190` | okay remember that |
| `00:26:07.200 - 00:26:09.669` | in the |
| `00:26:09.679 - 00:26:10.549` | file |
| `00:26:10.559 - 00:26:13.669` | we have a segment and the size of that |
| `00:26:13.679 - 00:26:16.149` | segment is file size and we're going to |
| `00:26:16.159 - 00:26:18.310` | initialize a chunk of memory in the |
| `00:26:18.320 - 00:26:20.390` | virtual address space and the size of |
| `00:26:20.400 - 00:26:23.110` | that chunk is mem size and it better be |
| `00:26:23.120 - 00:26:25.510` | at least as large as file size so it |
| `00:26:25.520 - 00:26:27.990` | ought to be able to contain all of these |
| `00:26:28.000 - 00:26:30.390` | these bytes here so we're checking that |
| `00:26:30.400 - 00:26:32.549` | to make sure that we don't have a |
| `00:26:32.559 - 00:26:34.950` | problem there |
| `00:26:34.960 - 00:26:37.269` | next we're adding v adder plus min size |
| `00:26:37.279 - 00:26:39.029` | and checking it and what's going on |
| `00:26:39.039 - 00:26:39.990` | there |
| `00:26:40.000 - 00:26:42.789` | well remember uh that v adder and |
| `00:26:42.799 - 00:26:46.230` | memsize are unsigned integers |
| `00:26:46.240 - 00:26:47.190` | and |
| `00:26:47.200 - 00:26:49.430` | just to refresh your memory whenever we |
| `00:26:49.440 - 00:26:52.310` | have unsigned integers and we add them |
| `00:26:52.320 - 00:26:55.110` | there's a possibility of overflow |
| `00:26:55.120 - 00:26:56.230` | and |
| `00:26:56.240 - 00:26:57.990` | if overflow occurs |
| `00:26:58.000 - 00:26:59.269` | then |
| `00:26:59.279 - 00:27:01.909` | the answer will be incorrect and if |
| `00:27:01.919 - 00:27:04.070` | overflow does not occur then the answer |
| `00:27:04.080 - 00:27:06.149` | will be correct and if the answer is |
| `00:27:06.159 - 00:27:08.950` | correct and there's no overflow then the |
| `00:27:08.960 - 00:27:11.110` | sum will be greater than or equal to a |
| `00:27:11.120 - 00:27:13.029` | and will be greater than or equal to b |
| `00:27:13.039 - 00:27:15.750` | because these are zero or larger |
| `00:27:15.760 - 00:27:18.549` | however if and only if there is overflow |
| `00:27:18.559 - 00:27:21.269` | then a plus b will be less than a and |
| `00:27:21.279 - 00:27:22.390` | less than b |
| `00:27:22.400 - 00:27:25.029` | so what we're doing here is checking to |
| `00:27:25.039 - 00:27:27.190` | see whether we've got overflow |
| `00:27:27.200 - 00:27:29.669` | so that's what's going on here |
| `00:27:29.679 - 00:27:32.789` | remember that the kernel can't trust the |
| `00:27:32.799 - 00:27:35.430` | code that's running in a process a user |
| `00:27:35.440 - 00:27:37.909` | process and it can't trust what's going |
| `00:27:37.919 - 00:27:41.269` | on inside an executable file so we need |
| `00:27:41.279 - 00:27:43.110` | to make sure that we don't have overflow |
| `00:27:43.120 - 00:27:44.950` | when we add these two things together |
| `00:27:44.960 - 00:27:46.149` | down here |
| `00:27:46.159 - 00:27:48.710` | otherwise the kernel might be uh get get |
| `00:27:48.720 - 00:27:51.190` | confused and we might have a problem so |
| `00:27:51.200 - 00:27:52.950` | that's why we're checking for overflow |
| `00:27:52.960 - 00:27:54.710` | here |
| `00:27:54.720 - 00:27:56.389` | the address where we're going to be |
| `00:27:56.399 - 00:27:58.470` | loading the segment in virtual address |
| `00:27:58.480 - 00:28:01.110` | space had better be a multiple of page |
| `00:28:01.120 - 00:28:03.350` | size that is it had better be page |
| `00:28:03.360 - 00:28:07.269` | aligned and we're checking that here |
| `00:28:07.279 - 00:28:09.909` | then we call uvm alec |
| `00:28:09.919 - 00:28:13.190` | uvm alec is used to grow a virtual |
| `00:28:13.200 - 00:28:15.430` | address space so here's the pointer to |
| `00:28:15.440 - 00:28:16.950` | the page table for this new address |
| `00:28:16.960 - 00:28:19.750` | space that we're creating and here's the |
| `00:28:19.760 - 00:28:22.870` | old size and here's the new size so |
| `00:28:22.880 - 00:28:23.909` | we're |
| `00:28:23.919 - 00:28:25.029` | providing |
| `00:28:25.039 - 00:28:27.669` | a size here that it is at least large |
| `00:28:27.679 - 00:28:29.269` | enough to contain |
| `00:28:29.279 - 00:28:30.389` | the |
| `00:28:30.399 - 00:28:31.909` | segment that we're going to be loading |
| `00:28:31.919 - 00:28:33.990` | so this is the virtual address right |
| `00:28:34.000 - 00:28:36.470` | plus the mem size so we're making sure |
| `00:28:36.480 - 00:28:37.830` | it's at least |
| `00:28:37.840 - 00:28:39.029` | this big |
| `00:28:39.039 - 00:28:42.389` | okay and uh this will return the new |
| `00:28:42.399 - 00:28:44.389` | size if everything is okay |
| `00:28:44.399 - 00:28:47.110` | if there's a problem it will return zero |
| `00:28:47.120 - 00:28:48.549` | so that's why we're using this local |
| `00:28:48.559 - 00:28:52.630` | variable here cz one we uh if there's a |
| `00:28:52.640 - 00:28:55.590` | problem and it returns zero okay then |
| `00:28:55.600 - 00:28:58.630` | we're going to jump to bad and bad will |
| `00:28:58.640 - 00:29:00.870` | need the size variable the old value so |
| `00:29:00.880 - 00:29:02.710` | that's why we're using a temporary here |
| `00:29:02.720 - 00:29:04.789` | but if everything's okay we can go ahead |
| `00:29:04.799 - 00:29:09.269` | and update the size variable |
| `00:29:09.279 - 00:29:12.549` | now this xv6 code happens to be changing |
| `00:29:12.559 - 00:29:14.710` | and since i made the video where i |
| `00:29:14.720 - 00:29:17.669` | discussed the uvm alec function |
| `00:29:17.679 - 00:29:20.549` | the code has actually been modified |
| `00:29:20.559 - 00:29:23.990` | and now the uvm alec function |
| `00:29:24.000 - 00:29:26.710` | takes an additional parameter |
| `00:29:26.720 - 00:29:28.070` | in the video where i covered this |
| `00:29:28.080 - 00:29:29.029` | function |
| `00:29:29.039 - 00:29:31.750` | i did not have this additional parameter |
| `00:29:31.760 - 00:29:35.510` | but now it has this additional parameter |
| `00:29:35.520 - 00:29:38.470` | the way i describe uvmalik it allocates |
| `00:29:38.480 - 00:29:40.470` | the pages and for each page it will mark |
| `00:29:40.480 - 00:29:41.669` | them as |
| `00:29:41.679 - 00:29:44.070` | accessible in user mode readable |
| `00:29:44.080 - 00:29:47.029` | writeable and executable however that is |
| `00:29:47.039 - 00:29:49.750` | changed and now the current version of |
| `00:29:49.760 - 00:29:53.590` | uvm alec will mark each page as |
| `00:29:53.600 - 00:29:56.230` | accessible in user mode and readable and |
| `00:29:56.240 - 00:29:58.470` | it will take this additional argument |
| `00:29:58.480 - 00:30:00.230` | and this additional argument will tell |
| `00:30:00.240 - 00:30:02.950` | whether to mark it as executable and or |
| `00:30:02.960 - 00:30:04.230` | writeable |
| `00:30:04.240 - 00:30:07.909` | so this little helper function flags to |
| `00:30:07.919 - 00:30:08.870` | perm |
| `00:30:08.880 - 00:30:10.789` | is |
| `00:30:10.799 - 00:30:12.710` | given right here so let's take a quick |
| `00:30:12.720 - 00:30:15.430` | look at that |
| `00:30:15.440 - 00:30:17.750` | remember that in the |
| `00:30:17.760 - 00:30:20.549` | program header we have a flags word |
| `00:30:20.559 - 00:30:23.269` | and that flags word contains |
| `00:30:23.279 - 00:30:25.669` | some bits and |
| `00:30:25.679 - 00:30:26.630` | the |
| `00:30:26.640 - 00:30:27.830` | first bit |
| `00:30:27.840 - 00:30:30.389` | is the executable and the second bit is |
| `00:30:30.399 - 00:30:31.990` | writable okay |
| `00:30:32.000 - 00:30:34.070` | so |
| `00:30:34.080 - 00:30:37.029` | flags two perm is going to take such a |
| `00:30:37.039 - 00:30:39.430` | word and it's going to look at the |
| `00:30:39.440 - 00:30:42.470` | rightmost bit and the second bit and if |
| `00:30:42.480 - 00:30:45.510` | the executable bit is set |
| `00:30:45.520 - 00:30:48.950` | then it will build a permissions where |
| `00:30:48.960 - 00:30:50.549` | the page table |
| `00:30:50.559 - 00:30:51.669` | entry |
| `00:30:51.679 - 00:30:55.190` | bit for executable is set |
| `00:30:55.200 - 00:30:57.990` | and then it looks at the second bit and |
| `00:30:58.000 - 00:31:01.029` | if that is set it also will set the page |
| `00:31:01.039 - 00:31:02.230` | table entry |
| `00:31:02.240 - 00:31:03.750` | writeable bit |
| `00:31:03.760 - 00:31:05.430` | and then it will return |
| `00:31:05.440 - 00:31:08.149` | that new permissions and that can then |
| `00:31:08.159 - 00:31:09.830` | be passed |
| `00:31:09.840 - 00:31:10.630` | to |
| `00:31:10.640 - 00:31:13.669` | the uh uvm alec function so that's |
| `00:31:13.679 - 00:31:16.070` | what's happening with this last argument |
| `00:31:16.080 - 00:31:17.669` | here |
| `00:31:17.679 - 00:31:19.590` | so um |
| `00:31:19.600 - 00:31:21.430` | let's at this point take a look at bad |
| `00:31:21.440 - 00:31:24.149` | if anything goes wrong here we jump to |
| `00:31:24.159 - 00:31:27.110` | this bad label which i have |
| `00:31:27.120 - 00:31:28.230` | right here |
| `00:31:28.240 - 00:31:31.590` | so here's the end of this function |
| `00:31:31.600 - 00:31:34.630` | we may have a |
| `00:31:34.640 - 00:31:37.190` | transaction in progress |
| `00:31:37.200 - 00:31:39.350` | so here's what happens we first look at |
| `00:31:39.360 - 00:31:41.669` | the page table and if we've managed to |
| `00:31:41.679 - 00:31:43.990` | get far enough to allocate a page table |
| `00:31:44.000 - 00:31:45.029` | then |
| `00:31:45.039 - 00:31:46.389` | we need to |
| `00:31:46.399 - 00:31:49.350` | return all the pages that are involved |
| `00:31:49.360 - 00:31:51.590` | back to the free pool and so we call |
| `00:31:51.600 - 00:31:54.310` | proc free page table with this page |
| `00:31:54.320 - 00:31:57.350` | table and the current size and so that's |
| `00:31:57.360 - 00:32:00.630` | why we had to use this cz1 temporary |
| `00:32:00.640 - 00:32:02.470` | variable because we we need size right |
| `00:32:02.480 - 00:32:03.430` | here |
| `00:32:03.440 - 00:32:05.750` | and this will free the page table and |
| `00:32:05.760 - 00:32:08.870` | return everything to the free pool |
| `00:32:08.880 - 00:32:11.350` | likewise if we've got the inode for the |
| `00:32:11.360 - 00:32:15.350` | executable file still open then we need |
| `00:32:15.360 - 00:32:16.149` | to |
| `00:32:16.159 - 00:32:19.029` | call put and unlock and we need to end |
| `00:32:19.039 - 00:32:23.830` | the transaction and then return -1 |
| `00:32:23.840 - 00:32:26.710` | we will see okay going back to this loop |
| `00:32:26.720 - 00:32:27.509` | here |
| `00:32:27.519 - 00:32:30.389` | after the loop ends we unlock we call |
| `00:32:30.399 - 00:32:32.470` | the put and unlock an endop but there |
| `00:32:32.480 - 00:32:34.710` | are other places where we call bad like |
| `00:32:34.720 - 00:32:36.630` | down here where we will be outside of |
| `00:32:36.640 - 00:32:39.190` | this transaction so that's why we check |
| `00:32:39.200 - 00:32:40.870` | it that way |
| `00:32:40.880 - 00:32:43.430` | so um after we |
| `00:32:43.440 - 00:32:46.070` | allocate space in the virtual address |
| `00:32:46.080 - 00:32:47.830` | space that we're creating |
| `00:32:47.840 - 00:32:50.870` | we need to actually load the bytes or |
| `00:32:50.880 - 00:32:52.710` | move the bytes from |
| `00:32:52.720 - 00:32:54.389` | the file to |
| `00:32:54.399 - 00:32:56.389` | the virtual address space so we have |
| `00:32:56.399 - 00:32:58.789` | this helper function load segment and |
| `00:32:58.799 - 00:33:00.230` | that's what it does |
| `00:33:00.240 - 00:33:01.110` | so |
| `00:33:01.120 - 00:33:04.149` | the file size field tells how many bytes |
| `00:33:04.159 - 00:33:06.230` | are in the file |
| `00:33:06.240 - 00:33:07.830` | and |
| `00:33:07.840 - 00:33:08.870` | ph |
| `00:33:08.880 - 00:33:12.149` | offset tells um |
| `00:33:12.159 - 00:33:15.269` | where in the file those bytes begin so |
| `00:33:15.279 - 00:33:18.070` | we're moving it the bytes from the file |
| `00:33:18.080 - 00:33:19.990` | and we're moving them to |
| `00:33:20.000 - 00:33:21.990` | here's the pointer to the inode for the |
| `00:33:22.000 - 00:33:25.029` | file and where are we moving them to in |
| `00:33:25.039 - 00:33:27.430` | this new virtual outer space well the v |
| `00:33:27.440 - 00:33:29.190` | adder field tells where we're moving |
| `00:33:29.200 - 00:33:30.230` | them to |
| `00:33:30.240 - 00:33:32.710` | if anything goes wrong this load segment |
| `00:33:32.720 - 00:33:34.950` | function will return to -1 and we can |
| `00:33:34.960 - 00:33:36.710` | exit to the bad label |
| `00:33:36.720 - 00:33:39.509` | but if it succeeds then we are done with |
| `00:33:39.519 - 00:33:41.590` | this loop and we can go on to the next |
| `00:33:41.600 - 00:33:43.269` | program header |
| `00:33:43.279 - 00:33:44.710` | and after we've done all the program |
| `00:33:44.720 - 00:33:46.710` | headers we're done with the executable |
| `00:33:46.720 - 00:33:48.710` | file we can then |
| `00:33:48.720 - 00:33:52.310` | uh put and unlock it and in the |
| `00:33:52.320 - 00:33:53.590` | transaction |
| `00:33:53.600 - 00:33:56.710` | and we then clear out the inode |
| `00:33:56.720 - 00:33:59.269` | pointer so that in the bad label we |
| `00:33:59.279 - 00:34:00.310` | won't |
| `00:34:00.320 - 00:34:02.470` | redo these two actions |
| `00:34:02.480 - 00:34:04.710` | okay now let's take a look at this load |
| `00:34:04.720 - 00:34:07.669` | segment function |
| `00:34:07.679 - 00:34:10.149` | here is the load segment function |
| `00:34:10.159 - 00:34:12.470` | it's passed page table which is a |
| `00:34:12.480 - 00:34:14.790` | pointer to the page table describing the |
| `00:34:14.800 - 00:34:16.470` | virtual address space that is being |
| `00:34:16.480 - 00:34:19.669` | created for this executable program and |
| `00:34:19.679 - 00:34:22.470` | it will load a program segment |
| `00:34:22.480 - 00:34:27.349` | into that virtual address at location va |
| `00:34:27.359 - 00:34:30.629` | and that va address must be page aligned |
| `00:34:30.639 - 00:34:33.030` | and furthermore the pages in the virtual |
| `00:34:33.040 - 00:34:34.950` | address space that are going to be |
| `00:34:34.960 - 00:34:37.669` | written to must already be present in |
| `00:34:37.679 - 00:34:39.829` | that virtual address space |
| `00:34:39.839 - 00:34:42.790` | if there is any problem it returns -1 |
| `00:34:42.800 - 00:34:45.030` | and otherwise it returns 0. |
| `00:34:45.040 - 00:34:47.589` | so in addition to a pointer to the page |
| `00:34:47.599 - 00:34:49.270` | table describing the new virtual address |
| `00:34:49.280 - 00:34:52.230` | space it takes the address at which we |
| `00:34:52.240 - 00:34:54.550` | will be loading the data |
| `00:34:54.560 - 00:34:57.109` | ip points to the inode for the |
| `00:34:57.119 - 00:34:58.710` | executable file |
| `00:34:58.720 - 00:35:01.349` | offset is the place in that file where |
| `00:35:01.359 - 00:35:03.829` | we are going to get the bytes and size |
| `00:35:03.839 - 00:35:05.030` | is the number of bytes that we're going |
| `00:35:05.040 - 00:35:06.710` | to be moving into the virtual address |
| `00:35:06.720 - 00:35:08.310` | space |
| `00:35:08.320 - 00:35:12.310` | so this function contains a loop and |
| `00:35:12.320 - 00:35:13.430` | each |
| `00:35:13.440 - 00:35:16.069` | iteration of this loop will move one |
| `00:35:16.079 - 00:35:17.910` | page of data |
| `00:35:17.920 - 00:35:20.550` | from the file into the virtual address |
| `00:35:20.560 - 00:35:21.910` | space |
| `00:35:21.920 - 00:35:23.589` | i will be the number of bytes we've |
| `00:35:23.599 - 00:35:27.589` | moved so far so we're just going to |
| `00:35:27.599 - 00:35:30.790` | loop in units of page size here until |
| `00:35:30.800 - 00:35:31.670` | we've |
| `00:35:31.680 - 00:35:34.950` | moved as many as size bytes |
| `00:35:34.960 - 00:35:36.630` | the first thing we do is we take a look |
| `00:35:36.640 - 00:35:38.870` | at the virtual address space and we |
| `00:35:38.880 - 00:35:40.790` | determine where |
| `00:35:40.800 - 00:35:42.870` | we are going to move the next chunk of |
| `00:35:42.880 - 00:35:46.870` | data so we call walk address and we pass |
| `00:35:46.880 - 00:35:48.790` | it the virtual address |
| `00:35:48.800 - 00:35:50.710` | plus i that's the number we've already |
| `00:35:50.720 - 00:35:52.310` | moved up to now |
| `00:35:52.320 - 00:35:54.550` | and that will give us the |
| `00:35:54.560 - 00:35:57.270` | physical address of the page in uh |
| `00:35:57.280 - 00:35:59.109` | kernel memory where |
| `00:35:59.119 - 00:36:02.150` | that page is mapped to so |
| `00:36:02.160 - 00:36:04.310` | uh once we have the physical address we |
| `00:36:04.320 - 00:36:06.630` | check to make sure that we really have |
| `00:36:06.640 - 00:36:07.910` | something |
| `00:36:07.920 - 00:36:12.069` | we have already called uvm aluc so that |
| `00:36:12.079 - 00:36:14.069` | page ought to be in the virtual address |
| `00:36:14.079 - 00:36:15.910` | space and if it's not we |
| `00:36:15.920 - 00:36:18.390` | print an error message here |
| `00:36:18.400 - 00:36:20.790` | then we look at how many bytes we have |
| `00:36:20.800 - 00:36:24.069` | left to go so i is the number that we've |
| `00:36:24.079 - 00:36:26.470` | moved so far up till now |
| `00:36:26.480 - 00:36:29.430` | and if the remaining amount of bytes is |
| `00:36:29.440 - 00:36:33.990` | less than one page worth of data then we |
| `00:36:34.000 - 00:36:35.910` | don't want to move a full page worth of |
| `00:36:35.920 - 00:36:36.950` | data |
| `00:36:36.960 - 00:36:38.710` | instead we compute the number of bytes |
| `00:36:38.720 - 00:36:41.270` | to move as size minus i otherwise we're |
| `00:36:41.280 - 00:36:44.230` | going to move a full page of data |
| `00:36:44.240 - 00:36:48.310` | and finally we call read i and read i is |
| `00:36:48.320 - 00:36:50.550` | past the inode |
| `00:36:50.560 - 00:36:52.870` | from the file from which we're going to |
| `00:36:52.880 - 00:36:56.710` | read as well as the offset in that file |
| `00:36:56.720 - 00:36:58.950` | so this is our offset parameter plus the |
| `00:36:58.960 - 00:37:01.589` | number we've moved already and n is the |
| `00:37:01.599 - 00:37:03.670` | number of bytes we're going to be moving |
| `00:37:03.680 - 00:37:06.550` | on this particular move and pa is the |
| `00:37:06.560 - 00:37:08.790` | physical address this flag indicates |
| `00:37:08.800 - 00:37:10.310` | that that's a physical address and not a |
| `00:37:10.320 - 00:37:12.470` | virtual address if there's any problem |
| `00:37:12.480 - 00:37:15.670` | we return -1 otherwise we loop back |
| `00:37:15.680 - 00:37:17.670` | and move the next |
| `00:37:17.680 - 00:37:20.710` | chunk of data from the file into the |
| `00:37:20.720 - 00:37:22.550` | virtual address space and when we're |
| `00:37:22.560 - 00:37:25.750` | done we return 0. |
| `00:37:25.760 - 00:37:28.230` | so let's return to the exact function |
| `00:37:28.240 - 00:37:29.589` | here's the loop where we're going |
| `00:37:29.599 - 00:37:31.910` | through all of the program headers and |
| `00:37:31.920 - 00:37:34.150` | for each one we are loading a segment |
| `00:37:34.160 - 00:37:36.310` | and after we're done with this loop |
| `00:37:36.320 - 00:37:37.829` | we're done with the |
| `00:37:37.839 - 00:37:40.630` | executable file so we call unlock put |
| `00:37:40.640 - 00:37:42.710` | and then we in the transaction |
| `00:37:42.720 - 00:37:45.589` | and then we move down here |
| `00:37:45.599 - 00:37:47.349` | here we are getting a pointer to the |
| `00:37:47.359 - 00:37:49.349` | proc structure we actually did that |
| `00:37:49.359 - 00:37:51.109` | earlier in this function so this |
| `00:37:51.119 - 00:37:53.030` | statement is unnecessary |
| `00:37:53.040 - 00:37:55.190` | and then we're capturing the size of the |
| `00:37:55.200 - 00:37:57.589` | existing virtual address space |
| `00:37:57.599 - 00:38:00.390` | and we will be using that later on |
| `00:38:00.400 - 00:38:01.670` | and then let's see what's going on with |
| `00:38:01.680 - 00:38:04.470` | this so here is a picture of the virtual |
| `00:38:04.480 - 00:38:05.829` | address space |
| `00:38:05.839 - 00:38:08.870` | when we created it it had two frames uh |
| `00:38:08.880 - 00:38:10.550` | two pages at the very top of the virtual |
| `00:38:10.560 - 00:38:11.990` | address space for the trampoline and |
| `00:38:12.000 - 00:38:13.349` | trap frame |
| `00:38:13.359 - 00:38:15.430` | and then we loaded a bunch of executable |
| `00:38:15.440 - 00:38:17.349` | segments down here somewhere and |
| `00:38:17.359 - 00:38:19.430` | apparently in this example we require |
| `00:38:19.440 - 00:38:22.790` | four pages so we have a number of pages |
| `00:38:22.800 - 00:38:24.790` | and size might not be page aligned at |
| `00:38:24.800 - 00:38:27.030` | this point so what we're going to do is |
| `00:38:27.040 - 00:38:29.190` | round it up to the nearest page boundary |
| `00:38:29.200 - 00:38:30.950` | and then we're going to allocate two |
| `00:38:30.960 - 00:38:33.670` | pages one for the guard page and one for |
| `00:38:33.680 - 00:38:36.230` | the user stack page |
| `00:38:36.240 - 00:38:38.390` | so that's what's going on next |
| `00:38:38.400 - 00:38:40.470` | here we see that we are |
| `00:38:40.480 - 00:38:42.790` | rounding size up to the next page |
| `00:38:42.800 - 00:38:46.230` | boundary and here we're calling uvm alec |
| `00:38:46.240 - 00:38:49.430` | and the old size is size at this point |
| `00:38:49.440 - 00:38:51.109` | and then we're adding two additional |
| `00:38:51.119 - 00:38:52.870` | pages and growing the virtual address |
| `00:38:52.880 - 00:38:55.510` | space by two pages |
| `00:38:55.520 - 00:38:58.470` | and if there's a problem there we go to |
| `00:38:58.480 - 00:39:01.510` | bad but otherwise we have enlarged the |
| `00:39:01.520 - 00:39:04.069` | size of the virtual address space |
| `00:39:04.079 - 00:39:06.790` | these pages pages will be marked as |
| `00:39:06.800 - 00:39:09.510` | readable and accessible in user mode by |
| `00:39:09.520 - 00:39:12.870` | the uvm alec and this uh new argument to |
| `00:39:12.880 - 00:39:14.390` | uvm alec |
| `00:39:14.400 - 00:39:16.470` | in this case we'll mark the pages as |
| `00:39:16.480 - 00:39:19.430` | writable as well so we can go ahead and |
| `00:39:19.440 - 00:39:22.230` | mark these pages as |
| `00:39:22.240 - 00:39:24.950` | accessible in user mode readable and |
| `00:39:24.960 - 00:39:27.109` | writeable accessible in user mode |
| `00:39:27.119 - 00:39:29.990` | readable and writeable |
| `00:39:30.000 - 00:39:33.109` | the next thing we do here is call uvm |
| `00:39:33.119 - 00:39:36.390` | clear and we're passing it the size |
| `00:39:36.400 - 00:39:39.750` | pointer minus two pages so at this point |
| `00:39:39.760 - 00:39:42.310` | the size pointer has been incremented by |
| `00:39:42.320 - 00:39:44.870` | two pages and decrementing two pages |
| `00:39:44.880 - 00:39:47.030` | we're pointing at the guard page |
| `00:39:47.040 - 00:39:47.750` | so |
| `00:39:47.760 - 00:39:49.990` | we call uvm clear which is going to mark |
| `00:39:50.000 - 00:39:51.190` | this thing as |
| `00:39:51.200 - 00:39:54.390` | not accessible in user mode |
| `00:39:54.400 - 00:39:57.750` | and if the user mode program tries to |
| `00:39:57.760 - 00:39:59.510` | grow the stack |
| `00:39:59.520 - 00:40:02.950` | it grows downward and grow it beyond one |
| `00:40:02.960 - 00:40:05.430` | page it will try to access this guard |
| `00:40:05.440 - 00:40:07.589` | page and that will abort the user |
| `00:40:07.599 - 00:40:09.109` | program |
| `00:40:09.119 - 00:40:11.829` | next we need to capture the pointer the |
| `00:40:11.839 - 00:40:13.109` | stack pointer |
| `00:40:13.119 - 00:40:14.470` | the stack is empty and it will be |
| `00:40:14.480 - 00:40:17.349` | growing downward and we also capture a |
| `00:40:17.359 - 00:40:19.430` | variable called stack base |
| `00:40:19.440 - 00:40:21.430` | which will allow us to detect whether we |
| `00:40:21.440 - 00:40:23.670` | are going to push |
| `00:40:23.680 - 00:40:25.750` | stuff on the stack and overflow it |
| `00:40:25.760 - 00:40:27.589` | within this function |
| `00:40:27.599 - 00:40:28.550` | so |
| `00:40:28.560 - 00:40:30.069` | here we |
| `00:40:30.079 - 00:40:31.589` | clear the |
| `00:40:31.599 - 00:40:34.150` | user mode bit and here we capture the |
| `00:40:34.160 - 00:40:39.750` | stack pointer and the stack base pointer |
| `00:40:39.760 - 00:40:41.990` | so continuing with the exec function the |
| `00:40:42.000 - 00:40:43.430` | next thing we're going to do is deal |
| `00:40:43.440 - 00:40:46.150` | with the argument strings so remember |
| `00:40:46.160 - 00:40:48.870` | that the exec function is called with a |
| `00:40:48.880 - 00:40:51.990` | pointer arc v to an array and that array |
| `00:40:52.000 - 00:40:54.630` | contains pointers to strings and each |
| `00:40:54.640 - 00:40:57.109` | string has been placed in a separate |
| `00:40:57.119 - 00:41:00.150` | page in the kernel space |
| `00:41:00.160 - 00:41:00.950` | so |
| `00:41:00.960 - 00:41:03.030` | here's what we'd like to get to okay |
| `00:41:03.040 - 00:41:05.190` | here is a picture of the stack |
| `00:41:05.200 - 00:41:07.589` | and we start with an empty stack and the |
| `00:41:07.599 - 00:41:09.750` | stack will grow downward in this |
| `00:41:09.760 - 00:41:11.030` | direction |
| `00:41:11.040 - 00:41:13.430` | and we want to push each one of the four |
| `00:41:13.440 - 00:41:15.829` | argument strings onto the stack and we |
| `00:41:15.839 - 00:41:18.390` | want to push an array with pointers to |
| `00:41:18.400 - 00:41:21.910` | those strings and then we will pass to |
| `00:41:21.920 - 00:41:25.190` | the main function of the new program |
| `00:41:25.200 - 00:41:26.870` | both arg c |
| `00:41:26.880 - 00:41:30.069` | which will be 4 in this case as well as |
| `00:41:30.079 - 00:41:32.630` | rgb which is a pointer to an array of |
| `00:41:32.640 - 00:41:36.309` | pointers to the strings |
| `00:41:36.319 - 00:41:40.150` | so let's walk through this code |
| `00:41:40.160 - 00:41:42.630` | we are going to count the number of |
| `00:41:42.640 - 00:41:45.589` | arguments with the variable argc starts |
| `00:41:45.599 - 00:41:46.710` | at zero |
| `00:41:46.720 - 00:41:50.069` | and we're going to go through the r v |
| `00:41:50.079 - 00:41:53.109` | array and for each one of the strings |
| `00:41:53.119 - 00:41:55.190` | we're going to push that string onto the |
| `00:41:55.200 - 00:41:57.670` | stack |
| `00:41:57.680 - 00:42:00.069` | so first of all we make sure that we |
| `00:42:00.079 - 00:42:01.750` | don't have too many arguments and if so |
| `00:42:01.760 - 00:42:04.470` | we go to this bad label |
| `00:42:04.480 - 00:42:06.790` | then we look at the string |
| `00:42:06.800 - 00:42:08.950` | and we figure out how long it is and |
| `00:42:08.960 - 00:42:11.270` | then we add one for the terminating null |
| `00:42:11.280 - 00:42:12.150` | byte |
| `00:42:12.160 - 00:42:13.510` | and |
| `00:42:13.520 - 00:42:16.390` | then we subtract the stack pointer and |
| `00:42:16.400 - 00:42:19.030` | we also want the stack pointer to remain |
| `00:42:19.040 - 00:42:22.390` | aligned on 16 byte boundaries so this |
| `00:42:22.400 - 00:42:24.710` | line here will lower the stack pointer |
| `00:42:24.720 - 00:42:26.630` | if necessary to the next |
| `00:42:26.640 - 00:42:29.589` | 16 byte boundary and then we check to |
| `00:42:29.599 - 00:42:31.670` | make sure that we haven't overflowed the |
| `00:42:31.680 - 00:42:35.109` | stack by looking at the stack base value |
| `00:42:35.119 - 00:42:38.790` | and go to bad if that's the case |
| `00:42:38.800 - 00:42:41.109` | and then we are ready to copy |
| `00:42:41.119 - 00:42:42.550` | from |
| `00:42:42.560 - 00:42:43.430` | the |
| `00:42:43.440 - 00:42:46.069` | string okay here is a pointer to the |
| `00:42:46.079 - 00:42:48.950` | string in sitting in its page by itself |
| `00:42:48.960 - 00:42:51.750` | here's the length of the string plus the |
| `00:42:51.760 - 00:42:54.630` | null byte and we're going to copy it to |
| `00:42:54.640 - 00:42:56.829` | where the stack pointer now |
| `00:42:56.839 - 00:42:59.510` | is and so |
| `00:42:59.520 - 00:43:01.430` | here |
| `00:43:01.440 - 00:43:03.990` | in this diagram these tick marks here |
| `00:43:04.000 - 00:43:06.950` | show 16 byte boundaries |
| `00:43:06.960 - 00:43:08.710` | and so we figure out how long this |
| `00:43:08.720 - 00:43:11.670` | string is and then we |
| `00:43:11.680 - 00:43:14.550` | drop that down to the next 16 byte |
| `00:43:14.560 - 00:43:16.950` | boundary and we copy the string in there |
| `00:43:16.960 - 00:43:19.270` | possibly leaving some unused bytes |
| `00:43:19.280 - 00:43:21.510` | um after it |
| `00:43:21.520 - 00:43:23.990` | now we've got this other variable called |
| `00:43:24.000 - 00:43:25.349` | u stack |
| `00:43:25.359 - 00:43:27.510` | that's a local variable in the exact |
| `00:43:27.520 - 00:43:28.470` | function |
| `00:43:28.480 - 00:43:32.950` | and um we want to save a pointer to the |
| `00:43:32.960 - 00:43:34.309` | string that we just pushed onto the |
| `00:43:34.319 - 00:43:36.630` | stack in that array |
| `00:43:36.640 - 00:43:39.109` | so that's what's going on here |
| `00:43:39.119 - 00:43:41.910` | i'm showing the u stack array here uh |
| `00:43:41.920 - 00:43:44.870` | normally i in the past i've shown arrays |
| `00:43:44.880 - 00:43:46.550` | uh with the |
| `00:43:46.560 - 00:43:48.550` | first elements at the top and then |
| `00:43:48.560 - 00:43:50.309` | indexes increasing as we go down the |
| `00:43:50.319 - 00:43:53.349` | page like this but for this case i want |
| `00:43:53.359 - 00:43:56.150` | to show it in the reverse way so this is |
| `00:43:56.160 - 00:43:58.309` | the first element here to the first |
| `00:43:58.319 - 00:44:01.030` | string i'm doing it this way because uh |
| `00:44:01.040 - 00:44:03.510` | i'm showing the stack growing downward |
| `00:44:03.520 - 00:44:05.589` | with low addresses down here and higher |
| `00:44:05.599 - 00:44:09.109` | addresses up higher on the page and so |
| `00:44:09.119 - 00:44:12.790` | we add a pointer to this u-stack array |
| `00:44:12.800 - 00:44:15.190` | for the first string and for the other |
| `00:44:15.200 - 00:44:17.510` | strings as well so we go through this |
| `00:44:17.520 - 00:44:19.430` | loop repeating for each one of the |
| `00:44:19.440 - 00:44:20.470` | arguments |
| `00:44:20.480 - 00:44:23.030` | saving a pushing it onto the stack |
| `00:44:23.040 - 00:44:24.950` | and then saving a pointer to it in this |
| `00:44:24.960 - 00:44:27.190` | u-stack array and finally when we're all |
| `00:44:27.200 - 00:44:28.630` | done we |
| `00:44:28.640 - 00:44:30.069` | save a null |
| `00:44:30.079 - 00:44:33.910` | pointer after the last pointer so that's |
| `00:44:33.920 - 00:44:37.109` | this null pointer here |
| `00:44:37.119 - 00:44:39.750` | now we need to push that u stack array |
| `00:44:39.760 - 00:44:41.349` | onto the stack |
| `00:44:41.359 - 00:44:43.030` | so here we are |
| `00:44:43.040 - 00:44:44.790` | calculating the size of it this is the |
| `00:44:44.800 - 00:44:47.190` | number of elements plus the terminating |
| `00:44:47.200 - 00:44:48.829` | null |
| `00:44:48.839 - 00:44:50.710` | so 4 |
| `00:44:50.720 - 00:44:52.230` | plus 1 |
| `00:44:52.240 - 00:44:54.230` | times the size of these pointers and so |
| `00:44:54.240 - 00:44:56.870` | we subtract that from the stack and then |
| `00:44:56.880 - 00:44:59.910` | with this line just like before we round |
| `00:44:59.920 - 00:45:02.630` | it down to the next 16 byte boundary and |
| `00:45:02.640 - 00:45:04.150` | check to make sure that we haven't |
| `00:45:04.160 - 00:45:06.390` | overflowed the stack |
| `00:45:06.400 - 00:45:08.150` | if everything's okay then we can now |
| `00:45:08.160 - 00:45:10.790` | copy the entire array so the size of |
| `00:45:10.800 - 00:45:14.230` | this array is just rxc plus one for the |
| `00:45:14.240 - 00:45:15.510` | null pointer |
| `00:45:15.520 - 00:45:19.829` | and we copy that to sp and here's the |
| `00:45:19.839 - 00:45:21.910` | where the the array is located so we |
| `00:45:21.920 - 00:45:23.109` | copy it to |
| `00:45:23.119 - 00:45:26.069` | the current value of the stack pointer |
| `00:45:26.079 - 00:45:28.390` | and if there are problems with that we |
| `00:45:28.400 - 00:45:30.829` | jump too bad |
| `00:45:30.839 - 00:45:33.510` | so that's what's happening here we have |
| `00:45:33.520 - 00:45:35.829` | figured out how big this array is uh |
| `00:45:35.839 - 00:45:37.750` | dropped down to the next 16 byte |
| `00:45:37.760 - 00:45:40.470` | boundary and then copied |
| `00:45:40.480 - 00:45:42.390` | the elements up to and including the |
| `00:45:42.400 - 00:45:44.069` | first null byte |
| `00:45:44.079 - 00:45:46.950` | uh the first null pointer into |
| `00:45:46.960 - 00:45:49.270` | uh this area here in the stack so again |
| `00:45:49.280 - 00:45:52.950` | we may have some space after that array |
| `00:45:52.960 - 00:45:55.109` | now the stack pointer is pointing here |
| `00:45:55.119 - 00:45:59.270` | and we when we call the main program for |
| `00:45:59.280 - 00:46:00.550` | the new |
| `00:46:00.560 - 00:46:01.589` | program |
| `00:46:01.599 - 00:46:04.150` | we will end up passing it argc that's |
| `00:46:04.160 - 00:46:06.069` | the count of the arguments |
| `00:46:06.079 - 00:46:10.230` | in a0 as well as a pointer to |
| `00:46:10.240 - 00:46:12.950` | this array and that will be in |
| `00:46:12.960 - 00:46:16.790` | register a1 |
| `00:46:16.800 - 00:46:18.870` | okay so we've just copied the usdac |
| `00:46:18.880 - 00:46:22.150` | array into the new virtual address space |
| `00:46:22.160 - 00:46:24.710` | at location sp |
| `00:46:24.720 - 00:46:27.829` | if anything goes wrong then we jump to |
| `00:46:27.839 - 00:46:30.630` | our bad label but otherwise nothing else |
| `00:46:30.640 - 00:46:33.190` | can go wrong and we will proceed down to |
| `00:46:33.200 - 00:46:36.069` | the return here so we are committed at |
| `00:46:36.079 - 00:46:37.990` | this point to |
| `00:46:38.000 - 00:46:39.990` | executing the new |
| `00:46:40.000 - 00:46:42.230` | program |
| `00:46:42.240 - 00:46:45.349` | sp at this point will point to |
| `00:46:45.359 - 00:46:48.150` | the rgv array that we've pushed onto the |
| `00:46:48.160 - 00:46:51.270` | stack in the new virtual address space |
| `00:46:51.280 - 00:46:53.430` | and that needs to be saved |
| `00:46:53.440 - 00:46:56.630` | uh in register a1 so that the new |
| `00:46:56.640 - 00:47:00.950` | program will get it a0 and a1 will be |
| `00:47:00.960 - 00:47:03.349` | the arguments to the function the first |
| `00:47:03.359 - 00:47:05.430` | function that executes in the new |
| `00:47:05.440 - 00:47:10.150` | program so we need to save sp that is |
| `00:47:10.160 - 00:47:12.309` | the pointer to the |
| `00:47:12.319 - 00:47:14.630` | rv array on the stack |
| `00:47:14.640 - 00:47:16.390` | in the trap frame |
| `00:47:16.400 - 00:47:19.030` | for register a1 |
| `00:47:19.040 - 00:47:21.670` | we have a field called name in the proc |
| `00:47:21.680 - 00:47:23.510` | structure and |
| `00:47:23.520 - 00:47:25.270` | what this does right here is save the |
| `00:47:25.280 - 00:47:27.349` | program name for debugging |
| `00:47:27.359 - 00:47:28.470` | so |
| `00:47:28.480 - 00:47:30.150` | the path name |
| `00:47:30.160 - 00:47:32.870` | might look like this and we run through |
| `00:47:32.880 - 00:47:34.950` | the path name with variable s |
| `00:47:34.960 - 00:47:37.430` | up to the null byte |
| `00:47:37.440 - 00:47:40.630` | and we increment through it and for each |
| `00:47:40.640 - 00:47:43.829` | uh character we ask is it a slash and if |
| `00:47:43.839 - 00:47:46.710` | it is then we're going to save a pointer |
| `00:47:46.720 - 00:47:49.190` | to the character after the slash |
| `00:47:49.200 - 00:47:51.510` | so for example when we hit this slash we |
| `00:47:51.520 - 00:47:53.990` | set last to point to this |
| `00:47:54.000 - 00:47:55.910` | position here |
| `00:47:55.920 - 00:47:58.550` | and after this last slash is encountered |
| `00:47:58.560 - 00:48:01.750` | we have a pointer to the final file name |
| `00:48:01.760 - 00:48:03.990` | in the entire path name |
| `00:48:04.000 - 00:48:06.069` | if there are no slashes well we |
| `00:48:06.079 - 00:48:07.510` | initialized last to point to the |
| `00:48:07.520 - 00:48:09.270` | beginning of the path name |
| `00:48:09.280 - 00:48:11.109` | so now we will copy |
| `00:48:11.119 - 00:48:13.910` | from last through the null byte into |
| `00:48:13.920 - 00:48:17.349` | this name field in the proc structure |
| `00:48:17.359 - 00:48:20.470` | and we use save string copy to copy up |
| `00:48:20.480 - 00:48:21.510` | to |
| `00:48:21.520 - 00:48:24.870` | as many characters as there are in this |
| `00:48:24.880 - 00:48:27.670` | field |
| `00:48:27.680 - 00:48:31.109` | now we are ready to switch out the old |
| `00:48:31.119 - 00:48:34.230` | virtual address space and switch to the |
| `00:48:34.240 - 00:48:36.630` | new virtual address space and make sure |
| `00:48:36.640 - 00:48:39.349` | we start executing at the beginning of |
| `00:48:39.359 - 00:48:41.670` | the new program |
| `00:48:41.680 - 00:48:45.670` | so we have previously saved the size of |
| `00:48:45.680 - 00:48:46.470` | the |
| `00:48:46.480 - 00:48:48.549` | old virtual address space |
| `00:48:48.559 - 00:48:50.390` | and here we're saving a pointer to the |
| `00:48:50.400 - 00:48:52.549` | old page table for the previous virtual |
| `00:48:52.559 - 00:48:55.349` | address space and here we are going to |
| `00:48:55.359 - 00:48:58.150` | call proc free page table with the |
| `00:48:58.160 - 00:49:00.309` | pointer to the old page table and its |
| `00:49:00.319 - 00:49:02.950` | size to free all of the pages that are |
| `00:49:02.960 - 00:49:04.950` | associated with the previous address |
| `00:49:04.960 - 00:49:06.390` | space |
| `00:49:06.400 - 00:49:09.430` | and here we are going to switch over to |
| `00:49:09.440 - 00:49:11.190` | the new virtual address space by |
| `00:49:11.200 - 00:49:13.670` | replacing the page table with the new |
| `00:49:13.680 - 00:49:15.910` | page table that we just created which |
| `00:49:15.920 - 00:49:17.829` | has size |
| `00:49:17.839 - 00:49:20.549` | indicated right here |
| `00:49:20.559 - 00:49:23.589` | we will also update the program counter |
| `00:49:23.599 - 00:49:26.150` | we saved the executing program counter |
| `00:49:26.160 - 00:49:29.510` | of the previous program in the trap |
| `00:49:29.520 - 00:49:31.430` | frame at this location but here we're |
| `00:49:31.440 - 00:49:33.430` | going to overwrite it with the entry |
| `00:49:33.440 - 00:49:35.750` | address for the new program |
| `00:49:35.760 - 00:49:37.750` | now previously we had gotten into the |
| `00:49:37.760 - 00:49:39.349` | kernel with an |
| `00:49:39.359 - 00:49:41.030` | environmental call |
| `00:49:41.040 - 00:49:43.829` | instruction so the program counter was |
| `00:49:43.839 - 00:49:45.349` | pointed to the place where we should |
| `00:49:45.359 - 00:49:47.430` | return from that |
| `00:49:47.440 - 00:49:50.069` | but the entry point of the new program |
| `00:49:50.079 - 00:49:52.710` | is the beginning of some function so |
| `00:49:52.720 - 00:49:55.670` | previously we would be returning a0 as a |
| `00:49:55.680 - 00:49:59.430` | return value for from the ecall function |
| `00:49:59.440 - 00:50:04.150` | but now we are going to return a0 and a1 |
| `00:50:04.160 - 00:50:05.270` | as well |
| `00:50:05.280 - 00:50:08.630` | and those will be arguments to the code |
| `00:50:08.640 - 00:50:10.470` | presumably a function that begins |
| `00:50:10.480 - 00:50:13.670` | executing at this location here |
| `00:50:13.680 - 00:50:16.069` | we also need to update the stack pointer |
| `00:50:16.079 - 00:50:18.230` | so here is the stack pointer that we |
| `00:50:18.240 - 00:50:20.790` | have computed for our new stack in the |
| `00:50:20.800 - 00:50:23.589` | new virtual address space and we are |
| `00:50:23.599 - 00:50:25.829` | saving that in the track frame so that |
| `00:50:25.839 - 00:50:29.190` | when we return to the a program |
| `00:50:29.200 - 00:50:31.190` | uh it will have a new stack pointer as |
| `00:50:31.200 - 00:50:33.349` | well as a new program counter then we |
| `00:50:33.359 - 00:50:35.589` | free the page tables uh the old page |
| `00:50:35.599 - 00:50:39.349` | table and return now remember that this |
| `00:50:39.359 - 00:50:42.390` | exact function was called from the cis |
| `00:50:42.400 - 00:50:43.430` | exec |
| `00:50:43.440 - 00:50:44.549` | function |
| `00:50:44.559 - 00:50:47.349` | and uh previously i told you that the |
| `00:50:47.359 - 00:50:49.670` | exact function here would not return |
| `00:50:49.680 - 00:50:51.349` | unless there was an error and if there |
| `00:50:51.359 - 00:50:53.030` | was an error it would free all those |
| `00:50:53.040 - 00:50:55.030` | pages that we allocated |
| `00:50:55.040 - 00:50:57.510` | but i kind of lied it will return even |
| `00:50:57.520 - 00:51:00.950` | if there's no error so if it does return |
| `00:51:00.960 - 00:51:03.109` | without an error then it will return a |
| `00:51:03.119 - 00:51:07.190` | value that is the r count the rxc value |
| `00:51:07.200 - 00:51:09.349` | and that will then get returned to the |
| `00:51:09.359 - 00:51:12.390` | program in register a0 but whether |
| `00:51:12.400 - 00:51:14.870` | there's an error or not we will do this |
| `00:51:14.880 - 00:51:17.990` | loop which we'll call k3 for each one of |
| `00:51:18.000 - 00:51:19.829` | the pages that we allocated for the arg |
| `00:51:19.839 - 00:51:21.589` | for temporary storage of the argument |
| `00:51:21.599 - 00:51:22.870` | strings |
| `00:51:22.880 - 00:51:24.150` | and at this point |
| `00:51:24.160 - 00:51:26.870` | we return i i'm not sure we should call |
| `00:51:26.880 - 00:51:30.230` | it return but we uh go back |
| `00:51:30.240 - 00:51:32.230` | as if we are returning from an |
| `00:51:32.240 - 00:51:35.270` | environment call and we resume executing |
| `00:51:35.280 - 00:51:37.510` | in the user |
| `00:51:37.520 - 00:51:40.710` | users process at location |
| `00:51:40.720 - 00:51:41.910` | epc |
| `00:51:41.920 - 00:51:43.829` | with the new stack pointer and the new |
| `00:51:43.839 - 00:51:45.829` | page table |
| `00:51:45.839 - 00:51:47.750` | the proc structure also contains some |
| `00:51:47.760 - 00:51:49.910` | other fields for example it contains the |
| `00:51:49.920 - 00:51:52.549` | o file which is the array of pointers to |
| `00:51:52.559 - 00:51:55.430` | the open files and it contains the field |
| `00:51:55.440 - 00:51:57.270` | cwd which is the current working |
| `00:51:57.280 - 00:52:00.150` | directory those are not changed here so |
| `00:52:00.160 - 00:52:03.589` | the newly executing program will begin |
| `00:52:03.599 - 00:52:06.390` | execution with all files that were open |
| `00:52:06.400 - 00:52:09.349` | in the previous program still being open |
| `00:52:09.359 - 00:52:11.510` | in the new program and with the same |
| `00:52:11.520 - 00:52:16.069` | current working directory |
| `00:52:16.079 - 00:52:18.390` | okay that wraps it up for this video and |
| `00:52:18.400 - 00:52:20.150` | that also wraps it up for the entire |
| `00:52:20.160 - 00:52:23.109` | playlist so if you have watched all the |
| `00:52:23.119 - 00:52:25.430` | videos in this series then um kudos to |
| `00:52:25.440 - 00:52:27.750` | you uh by all means leave me a comment |
| `00:52:27.760 - 00:52:30.630` | uh on this video so i will know that and |
| `00:52:30.640 - 00:52:33.430` | um well thanks for watching and i hope |
| `00:52:33.440 - 00:52:34.470` | you've learned something about the |
| `00:52:34.480 - 00:52:36.710` | kernel and uh |
| `00:52:36.720 - 00:52:40.200` | thank you very much |
