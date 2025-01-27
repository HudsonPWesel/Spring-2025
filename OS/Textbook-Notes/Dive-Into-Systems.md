# Section 11 Storage & The Memory Hierarchy 

## 11.2. Storage Devices
Storage types:
primary storage -> data access directly by CPU (registers & memory)
secondary storage -> CPU asks device to load its data into RAM 

Three attributes of storage
- Capacity
- Latency
	- Physical distance from CPU
	- Underlying Technology
		- Electrical circuits vs Capacitors vs Magnetic disks
- Transfer Rate -> propagation 
### 11.2.1. Primary Storage

RAM is Random Access Memory because it doesn't matter where the data is and we don't have to rewind the tapes, etc.

**Two Types of RAM**
- SRAM -> Static Ram (small electrical circuit) (Registers & Cache)
- DRAM -> Dynamic Ram (Capacitor that need to be refreshed) (RAM)
	- To retrieve a file the CPU sends a read on the memory bust (control wire) and the address to read (address wire) of the memory bus
 **Von Neuman Bottleneck:** Distance between CPU and memory

**Cache**
- We don't manually put data into cache
- Cache is the intermediary between Registers and RAM both physically and performance-wise
- typically stores a few kilobytes to megabytes
- Multiples Levels of cache
### 11.2.2. Secondary Storage
**Older Storage Devices**
- Punchcards : Punch holes into index cards to store data
- Tapes : data stored on spools that must be spooled to the correct location, high density 
- Floppy Disks : similar to HDDs magnetic recording media that rotates over a disk head
**Modern Secondary Storage**
{SATA,USB,IDE} Controller --> I/O Controller --> Memory Bus
![[Pasted image 20250123104045.png]]

**Hard Disk Drives**
- Consists of several spinning magnetic platters, the platters rotate quickly, small mechanical arm with a disk head at the tip moves across the platter to read or write data on concentric tracks
**Latency with HDDs**
 In order to read or write data the arm of the HDD must be aligned to the specific track by either extending or retracting the arm, this is called **seeking**  and the time it takes for the arm to find the specific track is called **seeking _latency_**
 Now that the arm is on the correct track, the platters has to spin to the 
**SSDs (Solid State Drives)**
They're "solid" in that there's no mechanical movement in the drive which reduces latency and use **flash memory**

## 11.3. Locality
**Locality** is a memory access pattern, where data that is actively being used by the system gets closer to the CPU (register or cache) and unused data remains farther away from the CPU

**Two Forms of Locality**
- Temporal Locality --> Programs access the same data within a time frame (say a program's execution time)
- Spatial Locality -->  Programs access data located near previously access data
*The program exhibits **Temporal Locality** (variables **len, i, sum, and array*** are accessed frequently in time, thus after the first time they're access, the CPU cache's these variables) 
The program exhibits **Spatial Locality** (When the program access data at `array[i]` the system  doesn't just load one int, but several ints into the cache based on the cache's *block size* — the amount of data transferred into the cache at once)
	- For example, with a 16-byte block size, a system copies four integers from memory to the cache at a time

```C
/* Sum up the elements in an integer array of length len. */
int sum_array(int *array, int len) {
    int i;
    int sum = 0;

    for (i = 0; i < len; i++) {
        sum += array[i];
    }

    return sum;
}
```

### 11.3.3. Temporal Locality
If the cache is full the system will evict the *least* recently used data 
### 11.3.4. Spatial Locality
When the program retrieves data, not only at the specified address but also the data surrounding it based on the **cache block size**
## 11.4 CPU Caches
We must consider three questions of cache (which is a fast SRAM storage that holds limited data )
- Which subsets of the program should we sotre
- When should we store it 
- How does the system know if the data can store 

To make the request the hardware sends the request with the desired address to both cache and main memory, since cache responds quicker, if we get a **cache hit** the pending request to RAM will be canceled 

On a **cache miss** the data (wether reading or writing) still gets put into cache

If there is not enough space in the cache to store the new data, the hardware must first write the data to memory before the data can be **evicted** from cache

Cache designers employ one of three designs to provide the above functionality.
### 11.4.1. Direct-Mapped Caches
The most basic implementation of cache is **direct-mapped cache** Which is comprised of dozens, hundreds, or even thousands of **cache lines** depending on the chip. In this implementation, each **cache line** is independent of each other. Each cache line is comprised of two parts (*metadata* and the *cache data block*)

*Cache Block*
- Actual bytes of data from program memory. Leveraged by spatial locality so the data transfered into this cache line is equal to the size of the cache block 
- Tradeoff between Small and Large *Cache Blocks*
	- Larger Cache Block -->  Using larger blocks is better for spatial locality performance (Larger cache blocks to facilitate larger data transfer from spatial locality)
	- Smaller Cache Block --> More diverse subsets of memory (More data transfers occur due to smaller cache block size)
- a typical CPU cache today uses middle-of-the-road block sizes ranging from 16 to 64 bytes.
*Metadata*
- Data about data (in this case it's data about the cache block), such as  which subset of memory the cache line’s data block holds).

The system must know where to look to determine if the address in memory (which corresponds to data) is in cache. In direct-mapped cache, each address is mapped to **one** cache line *this doesn't mean that cache lines aren't shared (they are, so data will compete with eachother)*

*How does the mapping of Address --> Cache Line Work?* 
Caches use bits taken from the _middle_ of the memory address, known as the **index** portion of the address, to determine which line the address maps to. Of course the number of bits in the index determines the number the possible cache lines. 
- By using the middle parts of the address, programs with good spatial locality will map to different cache lines (as opposed to the higher-order bits) which are the same, so data spreads more evenly
#### Identifying Cache Contents
Because multiple memory ranges map to the same cache line, the cache examines the line’s metadata to answer two important questions: 
_Does this cache line hold a valid subset of memory?_ _If so, which of the many subsets of memory that map to this cache line does it currently hold?_
To answer these questions, each cache line’s metadata includes a valid bit and a tag
- **Valid Bit:** Does that cache line store some data (subset of program memory)
- **Tag** Stores the higher order bits of the address whose data is currently stored (since the request already has the index, it already has selected the correct cache line, all it needs to determine if the currently requested address data matches the address data  stored in that cache line (since multiple addresses can map to the same cache line))
- ![[Pasted image 20250123125515.png]]
#### Retrieving Cached Data
After finding the proper cache line and verifying the data  the cache sends the requested data to the CPU’s components that need it. Because a cache line’s data block size (for example, 64 bytes) is typically much larger than the amount of data that programs request (for example, 4 bytes), caches use the low-order bits of the requested address as an **offset** into the  cached data block.![[Pasted image 20250123130031.png]]
#### Memory Address Division
The _dimensions_ of a cache dictate how many bits to interpret as the offset, index, and tag portions of a memory address.
- **Offset** (Rightmost Bits) Its length depends on a cache’s block size dimension (so the offset portion must contain enough bits must be able to specify any nth byte in the cache block)
	- Say a cache block stores 32bytes the offset must have 5 bits for it (2^5 = 32)
- **Index** (Immediately after **Offset** bits) Its length depends on a number of **cache lines** . (so the **Index** needs enough bits to uniquely identify every cache line)
- **Tag** (remaining address bits) Since it uniquely identifies the subset of memory contained within a cache line, it must use **all remaining bits** 
	- For example, if a machine uses 32-bit addresses, a cache with 5 offset bits and 10 index bits uses the remaining 32 - 15 = 17 bits of the address to represent the tag.
#### Writing to Cached Data
Caches must also allow programs to store values, and they support store operations with one of two strategies.
- **write-through cache**, a memory write operation modifies the value in the cache and simultaneously updates the contents of main memory
- **write-back cache**, a memory write operation modifies the value stored in the cache’s data block, but it does _not_ update main memory, until that cache block is evicted.
	- To identify cache blocks whose contents differ from their main memory counterparts, each line in a write-back cache stores an additional bit of metadata, known as a **dirty bit**.
	- reduce the cost of repeated writes to the same location in memory.
![[Pasted image 20250123132001.png]]
### 11.4.2. Cache Misses and Associative Designs
real caches can’t expect to hit on every access for a variety of reasons
- Cold-starts : First time access data
- Capacity misses :  Ideally each cache line holds exactly what is being used in from program memory, but if the memory data size exceeds the cache block size, this isn't possible for all data
- Conflict Misses : Data gets evicted before it's used again

Associative caches allow an address to be stored in one of **many** locations (not just one). This reduces the liklihood of a conflict, but means thats the hardware has most locations to check rather than one

A **fully associative** cache allows any memory region to occupy any cache location.

**Set associative** caches occupy the middle ground between direct-mapped and fully associative design . Every memory address maps to exactly one **cache set**, but each set stores multiple cache lines. The number of cache lines per set varies but it's usually 2-8 **cache lines** (not cache blocks)
### 11.4.3. Set Associative Caches

- **Index :** Maps to a single **cache set** rather than a single cache line and when performing an address lookup, the cache simultaneously checks every line in the set.
- **Tag** : If any of a set’s valid lines contains a tag that matches the address’s tag portion, the matching line completes the lookup.
- **Offset** : the cache uses the address’s _offset_ to send the desired bytes from the line’s cache block to the CPU
- ![[Pasted image 20250123144105.png]]
**LRU (Least Recently Used) Bit:**  The cache uses the LRU bit to determine which cache line gets evicted 
![[Pasted image 20250123145142.png]]


## 11.5. Cache Analysis and Valgrind
Most systems provide profiling tools to measure a program’s use of the cache. One such tool is Valgrind’s `cachegrind` mod
#### 11.5.2. Cache Analysis in the Real World: Cachegrind
Cachegrind enables a programmer to study how a program or particular function affects the cache.

Cachegrind simulates how a program interacts with the computer’s cache hierarchy

In many cases, Cachegrind can autodetect the cache organization of a machine. In the cases that it cannot, Cachegrind still simulates the first level (L1) cache and the last level (LL) cache
Cachegrind collects and outputs the following information:
- Instruction cache reads (`Ir`)
- L1 instruction cache read misses (`I1mr`) and LL cache instruction read misses (`ILmr`)
- Data cache reads (`Dr`)
- D1 cache read misses (`D1mr`) and LL cache data misses (`DLmr`)
- Data cache writes (`Dw`)
- D1 cache write misses (`D1mw`) and LL cache data write misses (`DLmw`)
D1 total access is computed by `D1 = D1mr + D1mw` and LL total access is given by `ILmr + DLmr + DLmw`.

**Run with Valgrind's Cachegrind Tool**
 Cachegrind outputs information about the number of cache hits and misses in the overall program:
```bash
valgrind --tool=cachegrind --cache-sim=yes ./cachex 1000
```

*To further tailor our analysis between cache hits and misses  use the Cachegrind tool `cg_annotate`*
```bash
cg_annotate cachegrind.out.28657
```

## 11.6. Looking Ahead: Caching on Multicore Processors
the L2 cache is a cache of RAM contents, and each core’s L1 cache is a cache of the L2 cache contents.
Since each core executes different program instructions each core’s L1 cache stores a copy of only those blocks of memory that are from its execution stream as opposed to competing for space in a single L1 cache shared by all cores.
![[Pasted image 20250123150657.png]]

#### **11.6.1. Cache Coherency**
multiple L1 caches can result in **cache coherency** problems.
Although each core is running their own program (processes) those individual programs might used the same resource (kernel library). What's more, the that data might be cached in each core's respective L1 Cache. The problem occurs where one core modifies its cache (on the shared resource) and the other one needs *the most recent version* of that data

Imagine a shared variable `counter` that tracks the number of completed tasks in a multicore system:
- Initial value: 5
- Core 1 increments it to 6
- Core 2 still has the old value of 5 in its cache
If Core 2 were to use the outdated value of 5:
- It would be operating on incorrect information
- This could lead to serious logical errors in program execution

Multicore processors implement a **cache-coherence protocol** to ensure a coherent view of memory that can be cached and accessed by multiple cores. Any core accessing a memory location sees the most recently modified value of that memory location rather than seeing an older (stale) copy of the value that may be stored in its L1 cache.

#### 11.6.2. The MSI (Cache Coherency) Protocol
The **MSI protocol** (Modified, Shared, Invalid) adds three flags (or bits) to each cache line, and encodes the state of its data block with respect to cache coherency with other cached copies of the block

- The **M** flag that, if set, indicates the block has been modified, meaning that this core has written to its copy of the cached value.
- The **S** flag that, if set, indicates that the block is unmodified and can be safely shared, meaning that multiple L1 caches may safely store a copy of the block and read from their copy.
- The **I** flag that, if set, indicates if the cached block is invalid or contains stale data (is an older copy of the data that does not reflect the current value of the block of memory).

*Read : If in S or M we're good | I get the new value from L2 Cache (**Cache miss**)*
*Write : If in M no changes to MSI bits | S or I   notify other cores that the block is being written to*
Updates occur through L2 cache

#### 11.6.3. Implementing Cache Coherency Protocols
To implement a cache coherency protocol, a processor needs some mechanism to identify when accesses to one core’s L1 cache contents require coherency state changes involving the other cores' L1 cache contents. One way this mechanism is implemented is through **snooping** on a bus that is shared by all L1 caches. . A snooping cache controller listens (or snoops) on the bus for reads or writes to blocks that it caches.  For example, it can set the I flag on a cache line when it snoops a write to the same address by another L1 cache. This example is how a **write-invalidate protocol** would be implemented with snooping.

# Section 13 The Operating System
## 13.1. How the OS Works and How It Runs
Skipped for breviy
## 13.2. Processes

Process states 

Fork : Create process
Exec : Execute program
Exit : SIGENV, SIGKILL
Wait : Parent calls this to clean up child 

There are three way we can have two processes communicate with eachother
**signals** : A notification of some event that is sent from process A --> OS --> process B (very limited)
**message passing**  Establishes a message communication channel this is used by the two processes to exchange messages (pipes & sockets)
**shared memory**  A process shares all or part of it's VA space contents with another  for both **read** **and** **write**

## 13.3. Virtual Memory
Page structure and mapping
## 13.4. Interprocess Communication
### 13.4.1. Signals
When a process receives a signal its execution will stop and have the OS execute the signal handler code. A signal is asynchrnous, unlike a trap where it's explicitly triggered, or a hardware interrupt that comes from the hardware

`man 7 signals`

A signal can either be ignored, terminate the process, or block/unblock the process  and programmers can define their own signal handler code to change the default action of a signal via `sigaction`(thread friendly) `signal` , though some can't such as `SIGKILL, SIGSTOP, SIGCONT`

(communicating through shared memory or signals is not an option for processes running on different systems).


### 13.4.3. Shared Memory

When two processes are running on the same machine, they can take advantage of shared system resources to communicate more efficiently than by using message passing. One way that the OS can implement partial address space sharing is by setting entries in the page tables of two or more processes to map to the same physical frames.
In Unix systems, the system call `shmget` creates or attaches to a shared memory segment.

Threads can easily share execution state by reading and writing to shared locations in their common address space.

Processes get their own VA space, divided into page tables. The lower order bites are for the offset within the page/frame to specify the specific address and the higher order bits are used to specify the specific page number. 

There's a MMU
#### 14.2.1. Creating and Joining Threads

# Section 14 Parallel Computing
### 14.1 - 14.4 (POSIX Threading)
pthread
### 14.5.1 Caches on Multicore Systems

The `index` directories contain files with numerous details about each logical core’s caches. The contents of a sample `index0` directory are shown here (`index0` typically corresponds to a Linux system’s L1 cache):
```bash
ls /sys/devices/system/cpu/cpu0/cache/index0

```
To discover the cache line size of the L1 cache, use this command:
```bash
cat /sys/devices/system/cpu/cpu0/cache/coherency_line_size
```

Every time a program updates a shared variable, the _entire cache line in other caches storing the variable is invalidated_.
- The end result is that, despite updating _different_ memory locations in `counts`, any cache line containing `counts` is _invalidated_ with _every update_ to `counts`!
- The invalidation forces all L1 caches to update the line with a "valid" version from L2. The repeated invalidation and overwriting of lines from the L1 cache is an example of **thrashing**, where repeated conflicts in the cache cause a series of misses.
- The addition of more cores makes the problem worse, given that now more L1 caches are invalidating the line
#### 14.5.3. Fixing False Sharing
A solution is to have threads write to _local storage_ whenever possible. Local storage in this context refers to memory that is _local_ to a thread. Since `local_counts` is not shared among the different threads, a write to it will not invalidate its associated cache line.
### 14.6.1. Fixing Issues of Thread Safety
Some library functions are not 'thread safe'
The takeaway from this section is that one should always check [the list of thread-unsafe functions in C](http://pubs.opengroup.org/onlinepubs/009695399/functions/xsh_chap02_09.html) when writing multithreaded applications
### 14.7. Implicit Threading with OpenMP (TODO)

# Section 15  GPU Computing & CUDA (TODO)

