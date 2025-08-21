# CPU cache
RAM is mainly build up of a capacitor with a transistor per bit of storage. <br>
The capacitor makes RAM slow (along with the bus), but data can be stored in smaller area than with only transistors.

Cache is fast memory made with transistors and is placed between the core(s) of the CPU (in the chip) and RAM. <br>
When RAM is read from, the cache is loaded with more data than the CPU asks for, a so called cache line of data, it can be 64 or 128 bytes.
This takes time, but if data needed afterwards is in the cache, it will go faster than going to RAM each time. 

Usually a cache has several levels: L1, L2, L3. <br>
Example of storage capacity: L1 – 640KB, L2 – 8MB, L3 – 96MB <br>
1KB = 1024 bytes, 1 MB = ca 1 million bytes. <br>
L1 cache is divided into two, one for instructions the other for data.

A CPU have many cores (that is where operations is done), each core has its own L1 and L2 cache. <br>
L2 is connected to L1 cache, and L3 is connected to the L2’s.

When a program is made, data should be close together so the cache is utilized correctly. <br>
Several regions of memory is used, but as long as data often accessed is nearby in these regions, the program will execute faster.

### Cache line alignment
Cache line is the amount of data loaded into the cache after a read from RAM. <br>
Cache line alignment is that the addresses are split into equal parts according to the size of the cach line. <br>
If the cache line is 64 bytes (that is 64/8=8 reads), then if you where to read from say address 11111000b, <br>
then it will not load the cache line from that address, but from 11000000b. <br>
So read 64 bytes from a adress aligned to a multiple of 1000000b (40h). <br>
If the cache line is 128 bytes then its from address multiple of 80h.

### Cache line prefetching
When you read from memory, the cache will not only get a cache line of data, but also predict where the program will need data from.
So the cache will in the background load in more cache lines, so it will hopefully allready be there at a later point.

### Writeback
When cache is full, but it need data from another address, it has to write to RAM an cache line to free it up (evict it). <br>
The cache keep track of what data is modified by a single bit called the dirty bit, so if not modified it does not have to write into RAM.
Which cache line to evict can be determined by hardware, also by special software, it has what is called a writeback policy.

### Processes sharing address space - shared data
If two running programs, can be a process or a thread, share the same data / memory, and is running at the same time in different cores,
and a write occures in the shared memory, the data will be marked by the cache as invalid (in the L3 cache), as it holds a copy of a cores L2 cache.
So if the other process or thread want to read from there afterwards, the L1 and L2 cache of that core needs to be updated with what is in L3. <br>

I might have gotten some of the details here wrong.

Optmization related to the cach, as far as I know, is difficult to do, as caches are built different from each other.
