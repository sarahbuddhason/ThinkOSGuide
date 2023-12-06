## Compilation

- **Compiled Language:** Programs are translated into machine language, executed by hardware (C).
- **Interpreted Language:** Programs are read and executed by a software interpreter (Python).
- **Hybrid:** Java compiles into Java bytecode, then interpreted by JVM.
- **Statically-Typed Language:** Variables are known at compile time (C).
- **Dynamically-Typed Language:** Variables are known only at runtime (Python).

### Compilation Process
1. **Preprocessing:** `#include` directive causes the source code from `.h` file to be inserted.
2. **Parsing:** Compiler reads source code and builts an **abstract syntax tree**. Detects syntax errors.
3. **Static Checking:** Compiler checks whether variables, values have the right type, etc. Detects static semantic errors.
4. **Code Generation:** Compiler reads program and generates machine code.
5. **Linking:** If the program uses libraries, compiler finds the appropriate libraries and includes the required code.
6. **Optimization:** At several points in the process, the compiler generates code that runs faster or uses less space.

### Object Code
- `-c` flag tells `gcc` to compile program and generate machine code.
- Result is `name.o` file, which is not yet executable.
- `nm hello.o` reads object file and generates information about defined functions.

### Assembly Code
- `-S` flag tells `gcc` to compile program and generate assembly code (human-readable).
- Shows low-level code representation.

### Preprocessing
- `-E` flag tells `gcc` to run the preprocessor only.
- Shows included code in `name.h` and all the files included from `name.h`, etc.

### Error Messages
1. **Preprocessor Errors:** Wrong `#include` directive.
2. **Syntax Errors:** Expected `;`.
3. **Linker Errors:** Use a function not defined in any library.
4. **Runtime Errors:** Segmentation faults (read/write to invalid memory), floating point (illegal operation), division by 0.

---

## Processes

- **Abstraction:** Simplified representation of complex systems.
- **Virtualization:** Type of abstrtaction. Process of creating an illusion of a larger, more capable system.

### Isolation
- Goal of designing an OS is to isolate system components to prevent unwanted interactions.
- Software object that provides this isolation is a **process**.

### Processes
- Represent a running program, containing:
  1. Program text (machine language)
  2. Data (static and dynamic)
  3. I/O operations status (waiting to read from disk)
  4. Hardware state (data in registers, program counter indicating which instruction is currently executing)
 
### OS Capabilities
1. **Multitasking:** Can interrupt running process at any time, save its hardware state, and resume process later.
2. **Virtual Memory:** Create illusion that each process has its own chunk of memory.
3. **Device Abstraction:** Coordinates which process interacts with each components and when.

### UNIX Processes
- Examples are LaTex, make, terminal windows, text editors.
- Many more running in the background, performing OS operations.
- `ps` UNIX command prints information about running processes.
  1. PID: Unique process ID.
  2. TTY: Terminal that crated the process.
  3. TIME: Total processor time used by the process.
  4. CMD: Name of the running program.
 
### Kernel and Daemon Processes
- `init` created when OS starts.
- `kthreadd` is used by OS to created new threads.
- `k` stands for **kernel**, which is the part of the OS responsible for core capabilities like creating threads.
- `d` stands for **daemon**, which is the name for processes that run in the background and provide OS services.
- `ksoftirqd` is a kernel daemon that handles software interrupt requests.
- `kworker` is one of many kernel processes.

---

## Virtual Memory

- **Bit:** Binary digit, one unit of information, 0 or 1.
- **Byte:** 8 bits, holding 1 of 256 (2^8) values.
- Relationship between number of bits and number of values they can encode: `N = 2^b`.

### Main Memory (RAM)
- Most data held in random access memory.
- Is volatile, meaning that when the computer shuts down, the contents are lost.
- Typically 2 to 8 GB of memory.

### Storage (HDD/SSD)
- When process reads and writes files, files are stored in hard drives or solid state drives.
- Are non-volatile, used for long-term storage.
- Typically 500 GB to 2 TB.

### Address Spaces
- Each byte in RAM is specified by an integer **physical address**.
- Set of valid physical address is called **physical address space**.
- Runs from 0 to N-1, where N = size of RAM.

### Virtual Address Spaces
- Most OS provide virtual memory, so programs never need to know how much physical memory is available.
- Programs work with **virtual addresses**, numbered 0 to M-1, where M = number of valid virtual addresses.
- M determined by OS and hardware. 32-bit system: 2^32 bytes, or 4 GB.

### Virtual Addresses
- When reading and writing values in memory, program generates virtual addresses.
- Hardware translates to physical addresses before accessing RAM.
- Translation is done per-process, so even if two processes generate same virtual address, they map to different physical memory locations.

### Memory Segments
- Segments of a process, organized starting at addresses near 0 -> M:
  1. **Code Segment:** Contains machine langauge instructions.
  2. **Static Segment:** Contains immutable values.
  3. **Global Segment:** Contains global variables, local variables declared `static`.
  4. **Heap Segment:** Contains chunks of memory allocated at runtime, often by calling C function `malloc`.
  5. **Stack Segment:** Contains call stack with a sequence of stack frames, each containing function's parameters and local variables.
 
```cpp
#include <stdio.h>
#include <stdlib.h>
int global;                  // Third address.

int main ()                  // Lowest address.
{
int local = 5;               // Highest address.
void *p = malloc(128);       // Fourth address.
char *s = "Hello, World";    // Second address.
}
```

- `0x7ffe6085443c` has 12 hexadecimal digits. Each hex digits correspond to 4 bits, so it is a 48-bit address. Virtual address space is 2^48.

### Static Local Variables
- **Automatic:** Allocated when function is called, freed when function returns.
- **Static:** Allocated in the global segment, initialized when program starts and keeps its value from one function call to next.

### Address Translation
- A **Memory Management Unit (MMU)** sits between CPU and RAM.
- Performs fast translations between VAs and PAs.

### Translation Process
1. CPU generates VA.
2. MMU splits VA into two parts: **page number** and **offset**.
   - **Page** is a chunk of memory, ranging between 1-4 KB.
3. MMU looks up page number in **Translation Lookaside Buffer (TLB)** and retrieves corresponding PA page number.
4. Combines PA page number with offset to produce PA.
5. PA is passed to RAM, which reads or writes to given location.

### Translation Lookaside Buffer
- Contains cached copies of data from page table (kernel memory).
- **Page table** contains mapping from virtual page numbers to physical page numbers.
- Each process has its own page table.

### TLB Example
- Suppose VA is 32 bits. Physical memory is 1 GB, divided into 1 KB pages.
- 1 GB = 2^30 bytes. 1 KB = 2^10 bytes.
- So, there are 2^20 physical pages, or "frames".
- VA space size = 2^23 bytes. Page size = 2^10 bytes.
- So, there are 2^22 virtual pages.
- Size of offset is determined by page size. Here, it takes 10 bits to specify a byte on a page.
- VA = 32 bits. Offset = 10 bits.
- So, 22 bits make up the virtual page number.
- Number of physical pages = 2^20. Physical page number = 20 bits.
- So, adding in 10 bit offset, 30 bits make up the physical addresses.

![image](https://github.com/sarahbuddhason/ThinkOSGuide/assets/55853717/9ead8692-468e-4818-9e5c-c99f6030258f)

### Sparse Page Tables
- Due to impractical size of complete page tables, sparse implementations are used.
- **Multilevel Page Table:** Used by Linux, etc.
- **Associative Table:** Each entry includes both virtual PN and physical PN.

### Context Switch
- OS interrupting a running process, saving its state, and then running another process.
- Since each process has its own page table, OS works with MMU to make sure each process gets the right one.
- Each page table entry gets a process ID, so page tables from multiple processes can be in the MMU at once.

---

## Files and File Systems

### Persistence
- Data stored in HDD or SSD is persistent, surviving after the process completes.
- HDD: Data is stored in blocks, laid out in sectors, which make up tracks.
- SSD: Data blocks are numbered sequentially, but each block has a limited write-to life.
- We want an abstraction of persistent storage hardware: **a file system**.

### File Systems
- A mapping from each file's name to its contents.
- Names are `keys` and contents are `values`: key-value database.
- A `file` is a sequence of bytes.
- File names are **hierarchical**, specifying a path from a top-level directory to the file.

### Key Difference
- Files are byte-based.
- Persistent storage is block-based.
- OS translates byte-based file operations into block-based operations on storage devices.

### File Reading

```cpp
FILE *fp = fopen("/home/name/file.txt", "r");
char c = fgetc(fp);
fclose(fp);
```

1. `fopen` uses filename to find the top-level directory ("/"), subdirectory ("home"), and sub-subdirectory ("name").
2. Finds `file.txt` and opens it for reading. Creates data structure that represents the file being read and the **file position**.
3. `fgetc` checks whether the next character is already in memory. If yes, then read the next character, advance the file position, and return result.
4. If no, OS issues an I/O request to get the next block.
5. New data block is stored in memory. Resumes reading the first character and storing it as a local variable.
6. When file is closed, OS completes any pending operations, removes stored data, and frees the data structure.

### File Writing

```cpp
FILE *fp = fopen("/home/downey/file.txt", "w");
fputc('b', fp);
fclose(fp);
```

1. `fopen` uses filename to find the file. If it does not exist, creates a new file and adds an entry in parent directory.
2. Create data structure that indicates file is open for writing. Sets file position to 0.
3. `fputc` attempts to write first byte of file. If file already exists, OS loads the first block into memory. Otherwise, allocates a new block in memory and requests a new block on disk.
4. After data block is modified, it is not copied back to disk right away. Data is **buffered**, stored in memory and only written to disk when there is at least one block to write.
5. When file is closed, any **buffered data** is written to disk and data structure is freed.

### Disk Performance
- If the CPU completes one instruction per clock cycle, it would complete 200 instructions while waiting for a byte from memory.
- OS generally switches to another process while waiting for data from disk.
- Significant speed difference between main memory and persistent storage.

### Speed Improvement Strategies
- **Block Transfers:** Generally try to read large blocks each time disk is accessed.
- **Pre-Fetching:** Sometimes OS can predict that a process will read a block, start loading it before it is requested. If reading first block, likely that you will read second block.
- **Buffering:** When writing, OS stores data block in memory and only writes it to disk later. Can modify it several times before writing to disk once.
- **Caching:** If a processed has used a block recently, likely to use it again. OS keeps a copy of the block in memory to handle future requests at memory speed.

### Index Nodes (Inodes)
- Data structure that contains information about files, location of their blocks, and other **metadata**.
- Contains a pointer to an **indirection block** which contains only pointers to other blocks.
- Number of pointers = 1024. Block size = 8 KB. So, an indirection block can address 8 MB.
- With a **double indirection block**, we can address 8 MB.
- With a **triple indirection block**, we can address 8 TB.

### File Allocation Table (FAT)
- Alterative to inodes.
- Contains one entry per block, called a **cluster**.
- A **root directory** contains a pointer to the first cluster in each file.
- The FAT entry for each cluster points to the next cluster in the file.

### Block Allocation
- File systems also have to keep track of available blocks.
- When file is created, FS allocates an available block.
- When file is deleted, FS makes the block available for re-allocation.
- Goals are:
  1. **Speed:** Allocating and freeing should be fast.
  2. **Minimal space overhead:** Data structures in allocator should be small.
  3. **Minimal fragmentation:** No blocks are ideally left unused or partially used.
  4. **Maximum contiguity:** Data that is likely to use used at the same time should be physically contiguous.

---

## Integer Representation
- In C, all integer types are signed, unless declared unsigned.
- Operations on unsigned ints do not use sign extension: copying the sign bit into the new bits.

## Bitwise Operators
- `&` to clear bits using a **mask**.
```cpp
  xxxx
& 0011
  ----
  00xx
```

- `|` to set bits.
```cpp
  xxxx
| 0011
  ----
  xx11
```

- `^` to toggle bits.
```cpp
  xxxx
^ 0011
  ----
  xx!x!x
```

- `<<` to left-shift bits. Doubles a number.
```cpp
5 << 2 = 20, since:
  5 << 1 = 10, so
  10 << 1 = 20.
```

- `>>` to right-shift bits. Divides by 2, rounded down.
```cpp
10 >> 2 = 2, since:
  10 >> 1 = 5, so
  5 >> 1 = 2.
```

## Float Representation
- Most computers use IEEE standard for floating-point arithmetic.
- `float` corresponds to 32-bit.
- `double` corresponds to 64-bit.
- Consists of a sign bit, 8 bits for exponent, and 23 bits for coefficient. `(-1)^s * c * 2^q`.

### Normalized Floats
- Most floats are **normalized** so that there is one digit before the point.
- In base 2, the number always has the digit 1 before the point (eg. 1.1 * 10^2). Can save space by leaving it out of the representation.

## Unions and Memory Errors
- Two cases for C unions:
  1. Access the binary representation of data.
  2. Store heterogeneous data.
- Reading and writing incorrect types can lead to errors. Must keep track of data type in the union.

## String Representation
- **ASCII Encoding:** Numeric representation of characters.
- C-strings are null-terminated.

---

## Memory Management

### Functions for Dynamic Memory Allocation
1. `malloc`: Takes integer size (in bytes) and returns pointer to new memory allocation with at least the given size. If impossible, returns NULL.
2. `calloc`: Takes integer size (in bytes) and clears newly allocated memory, setting all bytes to 0.
3. `free`: Takes a pointer to previously allocated memory, deallocates it.
4. `realloc`: Takes a pointer to previously allocated memory and a new integer size. Allocates memory with size, copies data from old chunk, frees old chunk, and returns pointer to new chunk.

### Memory Errors
1. Access unallocated memory.
2. Free unallocated memory.
3. Double freeing.

### Memory Leaks
- Program runs for a long time and allocated memory is not freed, total memory use will increase indefinitely.
- System runs out of physical memory, returning `NULL` on the next `malloc`.

```cpp
void *p = malloc(size);
if (p == NULL) {
perror("malloc failed");  // Prints error message.
exit(-1);                 // Terminate process with status code.
}
```

### Memory Implementation
1. When process starts, OS allocates space for:
  - Text segment, static data.
  - Stack.
  - Heap, which contains dynamic data. Initially only contains one free chunk.
2. When `malloc` called, checks whether it can find chunk big enough.
3. If not, requests more memory from the system:
  - `sbrk` sets a pointer to end of the heap.
  - OS allocates new pages of physical memory, updates page table, sets the program break.
4. Most Linux systems use `ptmalloc` to implement this API.

### Memory Details
- `malloc`: Runtime depends on how many free chunks there are.
- `free`: Usually fast, regardless of number of free chunks.
- `calloc`: Runtime depends on chunk size (and number of free chunks) as it clears every byte.
- `realloc`: Fast if new size is smaller or current size can be expanded. Otherwise, runtime depends on size of old chunk.

### `malloc` Internal Structure
- When `malloc` allocates chunk, adds space to beginning and end for **boundary tags**.
- Stores information, including size and state (allocated, free).
- Using BTs, `malloc` can get from any chunk to the previous one or the next one in memory.
- **Free List:** Free chunks are chained as a doubly-linked list.
- Minimum chunk size is 16 bytes, so `malloc` is not space-efficient for small structures due to BT and FL overhead.

### Heap Fragmentation
- If you allocate and free chunks of varied sizes, heap becomes fragmented.
- Free space broken into many small pieces, wasting space and making memory caches less effective.

### Binning and Caching
- Free list sorted by size into bins.
- `malloc` searches for a chunk with a particular size by searching in that bin.
- `malloc` is fast for freeing a chunk and immediately allocating a chunk with the same size.

---

## Caching

### Program Execution Model

- Program starts:
1. Code is stored on HDD or SSD.
2. OS creates a new process to run the program.
3. Loader copies the text from storage into main memory.
4. Starts the program by calling main.

- While it runs:
1. Most data is stored in main memory, some stored in **registers**.
2. Registers include:
   - **Program Counter:** Contains the address of the next instruction.
   - **Instruction Register:** Contains machine code instruction in execution.
   - **Stack Pointer:** Contains address of the current function's stack frame.
   - **Status Register:** Contains information about the current computation. Usually contains a bit that is set if result of previous operation = 0.
   - General-purpose ones that hold the data the program is working with.
3. CPU executes **instruction cycle**:
   - **Fetch:** Next instruction is fetched from memory, stored in IR.
   - **Decode:** Control unit that decodes the instruction and sends signals to other parts.
   - **Execute:** Signals from CU cause appropriate computation to occur.
  
### Instruction Set
- **Load:** Transfer value from memory to register.
- **Arithmetic:** Loads operands from registers, performs operation, stores result in register.
- **Store:** Transfer value from register to memory.
- **Jump:** Changes program counter, causing flow of execution to jump to another location in the program.

### Cache Performance
- **Memory Bottleneck:** CPU speed is significally faster than memory access speed.
- Solution is **caching**, a small, fast memory near the CPU.
- Reduces access time for frequently used data.

### Cache Levels
1. **Level 1 Cache:** Smallest, fastest, 1-2 MB with access time = 1 ns.
2. **Level 2 Cache:** Access time = 4 ns.
3. **Level 3 Cache:** Access time = 16 ns.

### Cache Lines
- Units where data are stored in caches.
- Each line corresponds to a block of addresses in memory.
- When data is loaded into cache, corresponding addresses also stored.

### Cache Speed
- **Hit Rate:** Fraction of memory accesses that find data in cache.
- **Miss Rate:** Fraction of memory accesses that go to memory.
- **Miss Penalty:** Extra time to process a cache miss.
- **Average Access Time:** `Th - m*Tp`, where `Tp = Tm - Th`.

### Locality
1. **Temporal Locality:** Tendency to reuse data within short time frame.
   - Instructions inside loops.
   - Result of one instruction used as an operand in next instruction.
2. **Spatial Locality:** Tendency to use data in nearly locations.
   - Blocks of code with no jumps, instructions run sequentially.
   - Parameters and local variables of a function stored on the stack.
   - Process elements of an array sequentially.

### Measuring Cache Performance

```cpp
i1 = 0;
do {
  sec_0 = get_seconds();

  for (int n = 0; n < limit; n += stride) // Max index, and elements skipped over.
    array[n]++;                           // Access cache.

  i1++;
  sec += (get_seconds() - sec_0)          // Total CPU time in inner loop.
} while (sec < 0.1);                      // Runs for 0.1 seconds.
```

```cpp
i2 = 0;
do {
  sec_0 = get_seconds();

  for (int n = 0; n < limit; n += stride)
    temp += n;

  i2++;
  sec - (get_seconds() - sec_0)
} while (i2 < i1);
```

- `sec` = **total miss penalty incurred by all accesses**.
- Average miss penalty per access is `sec * 1e9 / i1 / limit * stride` (ns)
- Maximum miss penalty if `stride >= block size`, since only one element is accessed per block.
- Good cache performance if `array.size() < cache.size()` or `array.size() > cache.size()` AND `stride` is large.
- **Multi-Level Caches:** Processor has small, fast cache and bigger, slower cache.

### Programming for Performance
- For large array, traverse array once rather than several times.
- For 2D array (array of rows), faster to traverse through elements by row.
- `merge_sort` breaks big arrays into smaller pieces for better performance.

### Memory Hierarchy

| Device  | Access Time | Typical Size | Cost          | Details |
|---------|-------------|--------------|---------------|---------|
| Register| 0.5 ns      | 256 B        | ?             |         |
| Cache   | 1 ns        | 2 MiB        | ?             | Small, close to CPU              |
| DRAM    | 100 ns      | 4 GiB        | $10 / GiB     | Dynamic RAM, packaged as dual in-line memory module (DIMM) with 16+ chips      |
| SSD     | 10 µs       | 100 GiB      | $1 / GiB      | Faster than HDD, more expensive  |
| HDD     | 5 ms        | 500 GiB      | $0.25 / GiB   |         |
| Tape    | minutes     | 1–2 TiB      | $0.02 / GiB   | Store large amounts cheaply      |

- Each level grows bigger and becomes slower.
- Each level acts as a cache for the one below it.

### Caching Policy

1. Who moves data where?
   - Register allocation done by Compiler.
   - Memory cache done by CPU.
   - Data from storage to memory by User or OS.
   - Data from tape to disk done by Administrator.
  
2. What data is moved?
   - Block sizes smallest at top of hierarchy.

3. When is data moved?
   - Data gets moved into cache when used for first time.
   - With **pre-fetching**, data is loaded before explicitly requested (pre-load entire block, etc.)

4. Where does data go in cache?
   - More time to make decisions at bottom of hierarchy.
   - Cache at top needs to be fast.

### Cache Replacement Policies

1. **Least-Recently-Used (LRU)**:
   - Discard LRU items first when cache is full.
   - Keep track of what was used and when.
   - Every time an item is accessed, its position is updated as it becomes the most recently used.
   - Implemented in linked lists and hash tables for effective tracking and retrieval.

## Paging / Swapping

1. Process A calls `malloc` to allocate a chunk.
2. If no free space in heap with the requested size, `malloc` calls `sbrk` (program break) to ask the OS for more memory (**swap space**).
3. If there is a free page in physical memory, OS adds it to Process A's page table with a new range of virtual addresses.
4. If no free pages, then the paging system chooses a **victim page** from Process B.
5. Copies the contents of the victim page from memory to disk.
6. Modifies Process B's page table to indicate the page has been "swapped out".
7. Page is reallocated to Process A after it is cleared.
8. `sbrk` call returns, and `malloc` gets additional space in the heap.
9. `malloc` allocated the requested chunk and returns.
10. When Process B accesses a swapped out page, MMU causes an interrupt.
11. OS handles the interrupt and transfers the page back from disk to memory.
12. Page is swapped in, and Process B resumes.

### Paging Advantages

- Some pages are only used once and can be swapped out.
- Memory leaks can occur when allocated space is never accessed again. Swapping plugs the leak.
- Daemons often sit idle and "wake up" to respond to events, can be swapped out.
- Users may have many windows open, inactive ones swapped out.
- Many processes running the same program, can share same text and static segments.
- Total memory allocated often greatly exceeds size of physical memory.

### Thrashing

- Process A runs and evicts pages Process B needs, and vice versa.
- Both processes slow down and system becomes unresponsive (thrashing).
- Avoid by detecting an increase in paging and blocking processes until system responsive again.

### What happens when you `malloc` 8GB on a 4GB RAM system?

- When `malloc` is called to allocate 8GB, the OS' memory manager handles the request, allocating **virtual memory**.
- Virtual memory is moreso the promise of memory, so no physical memory or disk space is used yet at this point.
- If at some point there is not enough physical memory available for all the running processes, as in this case, the OS intervenes with a process called **paging**.
- **Paging** moves data between RAM and a part of the disk called **swap space**.
- Swap space is used dynamically, moving data there as needed.
- When the program later tries to access memory that is not in RAM, which may be because it has yet to load or it has been swapped out, a **page fault** is triggered.
- The OS handles the page fault by transferring data from the swap space to RAM in chunks as needed.
- There is, however, a significant performance bottleneck with swapping in and out this data, since disk accesses are significantly slower than RAM accesses.
- This can lead to a process called **thrashing**, where the system spends more time moving data in and out of memory instead of executing actual tasks.
- Sometimes, the CPU does not find the data it needs in cache.
- It would then attempt to retrieve it from RAM, which is slower, or in the case of a page fault, from an even slower disk.
- Repeated loading of new data into RAM due to page faults disrupts the cache's ability to speed up data access.
- Overall, significant resources will be needed to handle page faults and swapping, straining the stability of the system.

### How could you fetch data from RAM faster?

- One solution is built-in caches which store frequently accessed data from RAM.
- These caches operate at different levels, with L1 being the fastest and closes to the CPU core.

### Latency Comparison Numbers

| Operation                            | Latency       | Comparative Latency           |
|--------------------------------------|---------------|-------------------------------|
| L1 cache reference                   | 0.5 ns        |                               |
| Branch mispredict                    | 5 ns          |                               |
| L2 cache reference                   | 7 ns          | 14x L1 cache                  |
| Mutex lock/unlock                    | 25 ns         |                               |
| Main memory reference                | 100 ns        | 20x L2 cache, 200x L1 cache   |
| Compress 1K bytes with Zippy         | 3,000 ns      | 3 us                          |
| Send 1K bytes over 1 Gbps network    | 10,000 ns     | 10 us                         |
| Read 4K randomly from SSD*           | 150,000 ns    | 150 us ~1GB/sec SSD           |
| Read 1 MB sequentially from memory   | 250,000 ns    | 250 us                        |
| Round trip within same datacenter    | 500,000 ns    | 500 us                        |
| Read 1 MB sequentially from SSD*     | 1,000,000 ns  | 1 ms ~1GB/sec SSD, 4X memory  |
| Disk seek                            | 10,000,000 ns | 10 ms 20x datacenter roundtrip|
| Read 1 MB sequentially from disk     | 20,000,000 ns | 20 ms 80x memory, 20X SSD     |
| Send packet CA->Netherlands->CA      | 150,000,000 ns| 150 ms                        |

### If virtual memory is the promise of memory, when is actual physical memory used?

- When a process calls `malloc` to allocate space, the OS reserves a portion of the virtual address space for it.
- Virtual memory provides the process with the illusion of a large block of contiguous memory, regardless of how much physical memory is actually available.
- Physical memory is actually used when the process starts to access (that is, read or write) the memory it has been allocated.
- The first access to a page of memory that has not been mapped to physical memory triggers a page fault.
- This interrupt tells the OS that we need to access part of its virtual memory.
- The OS then allocates physical memory for the page, and updates the page table that maps between virtual and physical memory.
- As the program continues running, it may access different parts of its allocated memory.
- In this case, space is limited, and so the OS may also swap some of the less frequently used data to disk to make room for new data in physical memory.

### Is there a limit to virtual memory?

- Yes, there is a limit which is determined by a few factors.
- The most fundamental one is the OS' architecture.
- A 32-bit OS has a virtual memory limit of 2^32 bytes, which is 4GB.
- A portion is often reserved for the OS itself, so in reality, there is actually less than 4GB for other processes.
- A 64-bit OS can theoretically address up to 2^64 bytes (18 exabytes) but practical limits are much lower.
- Virtual memory also relies on physical storage like HDD and SSD for the swap space.
- The size of those devices limit the extent of the swap space, and thus the amount of virtual memory vaialble.

### What happens if you call `calloc` 8GB on a 4GB RAM system?

- Calling `calloc` to allocate 8GB on a 4B RAM system follows a generally similar process to using `malloc`, with some differences in memory initialization.
- Like `malloc`, `calloc` doesn't immediately allocate physical memory but reserves a portion of the virtual address space.
- The key difference is that `calloc` initializes each byte in the allocated memory to zero.
- Since we only have 4GB of physical RAM, the OS would use virtual memmory to fulfill the 8GB request.
- It would use RAM and swap space on the HDD or SSD to provide the necessary space.
- Again, the OS would allocate physical memory only when the pages in virtual memory are actually accessed.
- Similarly to `malloc`, the first access results in a page fault, which the OS responds to by allocating a physical memory page.
- If the program access all 8GB, the OS would start using swap space, significantly slowing down the system.
- The access speeds of swap space on the disk compared to RAM is significant.
- Like with `malloc`, if there is heavy reliance on swap space due to physical RAM limitations, it may lead to thrashing.
- This occurs when the system spends more time swapping data between RAM and disk than performing actual tasks.

### If you've allocated 3GB total on a 4GB RAM system, and you see that you're getting many page faults, what might be the source?



### Most OS have daemons that will kill processes using a lot of memory. If I allocate 8GB on a 4GB system, will my allocating process get open-killed?



---

## Multitasking

- CPU contains **multiple cores**, running several processes at once.

### Kernel
- Lowest level of OS, implements multitasking and interrupts.
- **Interrupts:** Event that stops normal instruction cycle.
- **Interrupt Handler:** Flow of execution jumps to special handler.

### Interrupt Types
1. **Hardware:** Caused when a device sends a signal to CPU.
   - Network interface may cause interrupt when packet of data arrives.
   - Disk drive may cause interrupt when data transfer completes.
   - Timers that cause interrupts at intervals.
2. **Software:** Caused by a running program.
   - If an instruction cannot complete.
   - Floating-point errors, division by 0.
  
### System Calls
- When a program needs to access a hardware device.
- System call executes an instruction that triggers interrupt, causing flow of execution to jump to the kernel.
- Kernel reads system call, performs request, then resumes process.

### Interrupts and Hardware States
1. Interrupt occurs.
2. Hardware saves PC in a special register for the kernel, and jumps to interrupt handler.
3. Interrupt handler stores PC, status registers, stack pointer, and relevant data registers in memory.
4. Interrupt handler runs its code.
5. Hardware restores the contents of the saved registers.
6. Hardware restores the PC, jumping back to the interrupted instruction.

### Context Switching
- Kernel switches CPU from one process to another: **context switch**.
- During switch, has to clear data stored in MMU, and wait for new process to load data into cache.
- Context switches also occur after a process' **time slice** elapses.
- In a multitasking system, the **scheduler** handles time slice context switches.

### Process Life Cycle

- **Process Control Block:** Data structure that keeps track of the process information and state:
  1. Running, on a core.
  2. Ready, for future running.
  3. Blocked, waiting for a future event (network, disk read).
  4. Done, but has unsaved exit information.

- **State Transitions:**
  1. **Created -> Ready:** `fork` system call creates a process. When `fork` is done, new process (`child`) is ready.
  2. **Ready -> Running:** Scheduler resumes `parent` process or starts new `child` process.
  3. **Running -> Ready:** Process is interrupted. Scheduler chooses to not let it resume.
  4. **Running -> Blocked:** Process executes system call that cannot complete immediately. Scheduler chooses another process.
  5. **Blocked -> Ready:** Interrupt handler determines which process was waiting. Scheduler may choose to resume unblocked process.
  6. **Running -> Done:** Calls `exit`. Interrupt handler stores `exit` code in PCB.

### Scheduling
- Aims to optimize response time and throughput (number of requests completed per unit of time).
- **Decision Heuristics:**
  1. Limitations of resources. Computation-heavy = CPU-bound. Read from disk = I/O-bound. User action = Interaction-bound.
  2. If process runs for short time, then makes block request, it should run immediately after.

### Priority-Based Scheduling
1. Explicit user prioritization through `nice` system call. Decreases priority.
2. Process requests block before time slice is complete. Likely to be interaction- or I/O-bound. Priority up.
3. Process runs for entire time slice. Likely to be CPU-bound. Priority down.
4. Process blocked for a long time. Should respond to what it was waiting for. Priority up.
5. Process A is blocked, waiting for Process B. Priority of Process B up.

### Real-Time Scheduling
- Applications with tasks that must meet strict deadlines (motors, sensors).
- **Real-Time OS (RTOS):** Specialized OS to ensure highest priority processes run within fixed time frame.

---

## Threads

- Create a process, OS also makes:
  1. New address space, including text segment, static segment, heap.
  2. **Thread of execution**, including PC, hardware state, call stack.

- **Single-Threaded:** Only one thread of execution runs in each address space.
- **Multi-Threaded:** Multiple threads running in the same address space.

### Shared Resources
- Share the text segment, but often running different parts of code.
- Share the static segment, so if one thread changes a global variable, other threads see the change.
- Share the heap, so threads share dynamically-allocated chunks.

### Individual Stacks
- Each thread has its own stack.
- Can call functions without interfering with each other.
- Often do not or cannot access each other's local variables.

### Creating Threads
- `POSIX Threads`: Pthreads is the UNIX standard.

```cpp
#include <pthread.h>
#include <semaphore.h>

pthread_t makeThread(void *(*entry)(void *), Shared *shared) {    // Wrapper to provide error-checking. `pthread_t` is the handle / ID.
  int n;
  pthread_t thread;

  n = pthread_create(&thread, NULL, entry, (void *)shared);       // Success = 0.

  if (n != 0) {
    perror("pthread_create failed.");
    exit(-1);
  }

  return thread;
}

typedef struct {
  int counter;
} Shared;                                              // Data structure to contain values shared between threads.

Shared *makeShared() {
  Shared *shared = check_malloc(sizeof (Shared));      // Allocate space.
  shared->counter = 0;                                 // Initialize.
  return shared;
}
```

```cpp
gcc -g -02 -o arr arr.c -lpthread`    // Compiles source file named `arr.c`, links with `Pthread` library, generates executable named `arr`.
```

### `pthread_create`

```cpp
pthread_create(&thread, NULL, entry, (void *)shared);
```

- Specify the function where the execution of the new thread will begin.
- Function is named `entry`.
  
```cpp
void *entry(void *arg) {
  Shared *shared = (Shared *) arg;
  child_code(shared);
  pthread_exit(NULL);
}
```

- `entry` parameter declared as a `void` pointer, but is really a pointer to a `Shared` structure.
- Can typecast it accordingly and pass to `child_code` to do work.

```cpp
void child_code(Shared *shared) {
  cout << shared->counter << endl;
  shared->counter++;
}
```

- When `child_code` returns, `entry` calls `pthread_exit` used to pass a value to the thread that joins with the current thread.
- If nothing to pass, then pass `NULL`.

```cpp
pthread_t child[NUM_CHILDREN];                  // NUM_CHILDREN = compile-time constant.
Shared *shared = make_shared(NUM_THREADS);

for (int i = 0; i < NUM_CHILDREN; i++) {
  child[i] = make_thread(entry, shared);        // `child` = array of thread handles.
}
```

### Joining Threads

- When one thread wants to wait for another one to complete, invokes `pthread_join`.

```cpp
void joinThread(pthread_t thread) {
  int n = pthread_join(thread, NULL);        // Handle of the thread you want to wait for.
  if (n == -1) {
    perror("pthread_join fail.);
    exit(-1);
  }
}
```

- Most commonly, the parent thread creates and joins all child threads.
- Loop below waits for one child at a time in their creation order.
- If one of the children is late, other children may complete in the meantime.
- Loop exits only when children are done.

```cpp
for (int i = 0; i < NUM_CHILDREN; i++) {
  joinThread(child[i]);
}
```

### Synchronization with Mutex

- Without synchronization and order, threads can read and write to shared variables unpredictably (`int counter`).
- **Mutex:** Object that guarantees mutual exclusion for a block of code. Only one thread can execute at a time.

```cpp
void child_code(Shared *shared) {
  mutex_lock(shared->mutex);
  cout << shared->counter << endl;
  shared->counter++;
  mutex_unlock(shared->mutex);
}

typedef struct {
  int counter;
  Mutex *mutex;
} Shared;

Shared *makeShared(int end) {
  Shared *shared = check_malloc(sizeof(Shared));
  shared->counter = 0;
  shared->mutex = makeMutex();
  return shared;
}
```

- Before thread can access `counter`, it locks the mutex, barring all other threads.
- When thread is done, it unlocks, allowing blocked threads to proceed.
- In effect, threads line up to execute `child_code` one at a time.

### Mutex Initialization

- `Mutex` is a wrapper over POSIX's `pthread_mutex_t`.
- If using POSIX, must allocate space for `pthread_mutex_t` and call `pthread_mutex_init`.
- Must pass by address, so that it does not create a copy.

```cpp
#include <pthread.h>

typedef pthread_mutex_t Mutex;

Mutex makeMutex() {
  Mutex *mutex = check_malloc(sizeof(Mutex));      // Allocate space.
  int n = pthread_mutex_init(mutex, NULL);

  if (n != 0) {
    perror("makeMutex failed.");
    exit(-1);
  }
  
  return mutex;                                    // A pointer, can be passed around without unwanted copying.
}
```

### Mutex Locking

```cpp
void mutexLock(Mutex *mutex) {
  int n = pthread_mutex_lock(mutex);
  if (n != 0) perror_exit("mutexLock failed.");
}
```

### Mutex Unlocking

```cpp
void mutexUnblock(Mutex *mutex) {
  int n = pthread_mutex_unlock(mutex);
  if (n != 0) perror_exit("mutexUnlock failed.");
}
```

---

## Condition Variables

### Work Queue
- Threads communicate with each other using a **work queue**.
- **Producers:** Put data into the queue.
- **Consumers:** Take data out of the queue.

### Work Queue for GUI
- One thread runs GUI, responds to user events.
- Another thread processes user requests.
- Puts those requests into a queue.
- "Back-end" thread takes requests out of queue and processes them.

### Thread Safety
- Need queue implementation so that multiple can access it at once.
- Need to handle cases where queue is empty, when queue is full.

### Circular Buffer
- Single, fixed-sized buffer connected end-to-end.
- Does not need to have elements moved around when one is consumed. Follows FIFO.

### Thread-Safe Queue Implementation

```cpp
typedef struct {
  int *array;
  int length;
  int next_in;
  int next_out;
} Queue;

Queue *make_queue(int length) {
  Queue *q = (Queue *) malloc(sizeof(Queue));        // Allocate space.
  q->length = length + 1;                            // Length of array + 1 = queue length.
  q->array = (int *) malloc(length * sizeof(int));   // Allocate space.
  q->next_in = 0;
  q->next_out = 0;                                   
  return q;
}

int queue_empty(Queue *q) {
  return (q->next_in == q->next_out);                // next_in == next_out is a special case where the queue is empty. next_out = 0 is invalid, technically.
}

void queue_push(Queue *q, int item) {
  if (queue_full(q)) perror_exit("Queue is full.");

  q->array[q->next_in] = item;                       // Insert the new element.
  q->next_in = queue_incr(q, q->next_in);            // Increment `next_in`.
}

int queue_incr(Queue *q, int i) {
  return (i + 1) % q->length;                        // When index `i` reaches end of `array` it wraps around to 0.
}

int queue_full(Queue *q) {
  return (queue_incr(q, q->next_in) == q->next_out); // Special case.
}
```

- If only `queue_incr` exists, eventually `next_in` catches up with `next_out`.
- Would incorrectly conclude that queue was empty.
- In `queue_full`, if incrementing `next_in` leads to `next_out`, we cannot add another element without making it appear empty.
- So, we stop one element before the `end`, and then indicate that it is full.

```cpp
int queue_pop(Queue *q) {
  if (queue_empty(q)) perror_exit("Queue is empty.");

  int popped = q->array[q->next_out];
  q->next_out = queue_incr(q, q->next_out);
  return popped;
}
```

### Producers and Consumers Implementation

```cpp
typedef struct {
  Queue *queue;
} Shared;

Share *make_shared() {
  Shared *shared = check_malloc(sizeof(Shared));    // Allocate space.
  shared->queue = make_queue(QUEUE_LENGTH);
  return shared;
}
```

- Entry point for producer thread.

```cpp
void *producer_entry(void *arg) {
  Shared *shared = (Shared *) arg;

  for (int i = 0; i < QUEUE_LENGTH - 1; i++) {
    cout << "Adding item " << i << endl;
    queue_push(shared->queue, i);
  }

  pthread_exit(NULL);
}
```

- Entry point for consumer thread.

```cpp
void *consumer_entry(void *arg) {
  int item;
  Shared *shared = (Shared *) arg;

  for (int i = 0; i < QUEUE_LENGTH - 1; i++) {
    item = queue_pop(shared->queue);
    cout << "Consuming item " << item << endl;
  }

  pthread_exit(NULL);
}
```

- Parent code that starts producer and consumer threads and wait for them.

```cpp
  pthread_t child[NUM_CHILDREN];
  Shared *shared = make_shared();

  child[0] = make_thread(producer_entry, shared);
  child[1] = make_thread(consumer_entry, shared);

  for (int i = 0; i < NUM_CHILDREN; i++) {
    join_thread(child[i]);
  }
```

- Current implementation is not thread-safe.
- Different threads can access `array`, `next_in`, `next_out` simultaneously.
- Above can be solved with **mutex**.

- Consumer should block until queue is not empty.
- Producer should block if the queue is full.
- Above can be solved with **condition variables**.

### Thread-Safety with Mutex

```cpp
typedef struct {
  int *array;
  int length;
  int next_in;
  int next_out;
  Mutex *mutex;                             // Add mutex.
} Queue;

Queue *make_queue(int length) {
  Queue *q = (Queue *) malloc(sizeof(Queue));
  q->length = length;
  q->array = (int *) malloc(length * sizeof(int));
  q->next_in = 0;
  q->next_out = 0;
  q->mutex = make_mutex();                  // Initialize the mutex.
}

void queue_push(Queue *q, int item) {
  mutex_lock(q->mutex);                     // Lock the queue.

  if (queue_full(q)) {
    mutex_unlock(q->mutex);                 // If queue is full, unlock then exit.
    perror_exit("Queue is full.");
  }

  q->array[q->next_in] = item;
  q->next_in = queue_incr(q, q->next_in);
  mutex_unlock(q->mutex);                  // Unlock the queue after producing.
}

int queue_pop(Queue *q) {
  mutex_lock(q->mutex);                     // Lock the queue.

  if (queue_empty(q)) {
    mutex_unlock(q->mutex);                 // If queue is empty, unlock then exit.
    perror_exit("Queue is empty.");
  }

  int item = q->array[q->next_out];
  q->next_out = queue_incr(q, q->next_out);

  mutex_unlock(q->mutex);                  // Unlock the queue after consuming.
  return item;
}
```

### Thread-Safety with Condition Variables

- Data structure associated with a condition.
- Allows threads to block until the condition becomes true.
- `thread_pop`: Check whether queue is empty. If so, then wait until queue is not empty.
- `thread_push`: Check whether queue is full. If so, then wait until queue is not full.


```cpp
typedef struct {
  int *array;
  int length;
  int next_in;
  int next_out;
  Mutex *mutex;
  Cond *nonempty;                           // Condition variable.
  Cond *nonfull;                            // Condition variable.
} Queue;

Queue *make_queue(int length) {
  Queue *q = (Queue *) malloc(sizeof(Queue));
  q->length = length;
  q->array = (int *) malloc(length * sizeof(int));
  q->next_in = 0;
  q->next_out = 0;
  q->mutex = make_mutex();
  q->nonempty = make_cond();                // Initialize the condition variable.
  q->nonfull = make_cond();                 // Initialize the condition variable.
}

int queue_pop(Queue *q) {
  mutex_lock(q->mutex);

  while (queue_empty(q)) {                  // P1: Condition we are waiting for = a `nonempty` queue.
    cond_wait(q->nonempty, q->mutex);       // P2: Mutex that protects the queue.
  }

  int item = q->array[q->next_out];
  q->next_out = queue_incr(q, q->next_out);

  mutex_unlock(q->mutex);
  cond_signal(q->nonfull);                  // Signal that the queue is `nonfull`, since we just popped a thread.
  return item;
}
```

- When thread that locked the mutex calls `cond_wait`, it unlocks the mutex then blocks.
- If `cond_wait` did not unlock before blocking, no other thread would be able to able to access the queue.
- While the consumer is blocked on `nonempty`, producer can run.

```cpp
void queue_push(Queue *q, int item) {
  mutex_lock(q->mutex);

  if (queue_full(q)) {
    mutex_unlock(q->mutex);
    perror_exit("Queue is full.");
  }

  q->array[q->next_in] = item;
  q->next_in = queue_incr(q, q->next_in);
  mutex_unlock(q->mutex);
  cond_signal(q->nonempty);                // Signals that the queue is `nonempty`, since we just pushed a thread.
}
```

- Signalling a condition variable indicates that it is true.
- If no threads are waiting on the condition variable, no effect.
- If threads are waiting, one of them gets unblocked and resumes execution of `cond_wait`.
- Before it can return from `cond_wait`, must wait for and then lock the mutex, again.

- When consumer thread exits `while` loop in `queue_pop`, we know:
  1. There is at least one item in queue.
  2. The mutex is locked, so it is safe to access the queue.
- Consumer then pops an item, unlocks mutex, and returns.

### `cond_wait`

1. `while` loop instead of an `if` statement because:
   - Must be re-checked in the chance of an intercepted signal.
   - Thread A is waiting on `nonempty`.
   - Thread B adds item, signals `nonempty`.
   - Thread A tries to lock mutex, but before it can, Evil Thread C locks it, pops an item, and unlocks.
   - Queue is now empty, but Thread A is no longer blocked.
   - Thread A could lock mutex and attempt to pop from empty queue, leading to error.
  
2. Naming convention:
   - Condition is false when you call `cond_wait`, true when you call `cond_signal`.
   - "Wait, queue is not `nonempty`."
   - "Now signalling that queue is `nonempty`."
  
### Condition Variable Implementation

```cpp
typedef pthread_cond_t Cond;

Cond *make_cond() {
  Cond *cond = check_malloc(sizeof(Cond));      // Allocate space.
  int n = pthread_cond_init(cond, NULL);        // Initialize condition variable.
  if (n != 0) perror_exit("make_cond failed");
  return cond;                                  // Returns pointer to `cond`.
}

void cond_wait(Cond *cond, Mutex *mutex) {
  int n = pthread_cond_wait(cond, mutex);
  if (n != 0) perror_exit("cond_wait failed.");
}

void cond_signal(Cond *cond, Mutex *mutex) {
  int n = pthread_cond_signal(cond);
  if (n != 0) perror_exit("cond_signal failed.");
}
```

1. `cond_wait`:
  - When thread calls `cond_wait`, thread is blocked, and `mutex` is atomically released.
  - Thread remains blocked until another thread signals `cond`.
  - After `cond` is signalled, `cond_wait` requires mutex, returns to the calling thread.

2. `cond_signal`:
   - When thread calls `cond_signal`, thread unblocks one of the threads waiting on `cond`.
  
---

## Semaphores

- Data structure to help threads work together without interference.
- POSIX semaphores: `sem_t`.

```cpp
typedef sem_t Semaphore;

Semaphore *make_semaphore(int value);
void semaphore_wait(Semaphore *sem);
void semaphore_signal(Semaphore *sem);
```

```cpp
Semaphore *make_semaphore(int value) {
  Semaphore *sem = check_malloc(sizeof(Semaphore));    // Allocate space.
  int n = sem_init(sem, 0, value);                     // Initialize semaphore.
  if (n != 0) perror_exit("sem_init failed.");
  return sem;
}
```

```cpp
void *semaphore_wait(Semaphore *sem) {
  int n = sem_wait(sem);
  if (n != 0) perror_exit("sem_wait failed.");
}
```

```cpp
void *semaphore_signal(Semaphore *sem) {
  int n = sem_post(sem);
  if (n != 0) perror_exit("sem_post failed.");
}
```

### Using Semaphores as a Mutex

```cpp
Semaphore *mutex = make_semaphore(1);

semaphore_wait(mutex);
// ...
// Protected code blocks ...
// ...
semaphore_signal(mutex);
```

- Initialize to 1 to indicate `mutex` is unlocked.
- The first thread can pass the semaphore without blocking.

### Using Semaphores with Producer-Consumer

```cpp
typedef struct {
  int *array;
  int length;
  int next_in;
  int next_out;
  Semaphore *mutex;            // Mutex as a semaphore.
  Semaphore *items;            // # items in the queue = # consumers that can execute `queue_pop` without blocking. Initially 0.
  Semaphore *spaces;           // # empty spaces in the queue = # producers that can execute `queue_push` without blocking. Initially length - 1.
} Queue;

Queue *make_queue(int length)
{
  Queue *q = (Queue *) malloc(sizeof(Queue));
  q->length = length;
  q->array = (int *) malloc(length * sizeof(int));
  q->next_in = 0;
  q->next_out = 0;
  q->mutex = make_semaphore(1);
  q->items = make_semaphore(0);
  q->spaces = make_semaphore(length - 1);
  return q;
}

void queue_push(Queue *q, int item) {
  semaphore_wait(q->spaces);                    // Keeps track of how many SPACES are available.
  semaphore_wait(q->mutex);                     // Blocks producers if queue is full.

  q->array[q->next_in] = item;
  q->next_in = queue_incr(q, q->next_in);

  semaphore_signal(queue->mutex);
  semaphore_signal(queue->items);               // Keeps track of how many ITEMS are **NOW** available.
}

int queue_pop(Queue *q) {
  semaphore_wait(q->items);                    // Keeps track of how many ITEMS are available.
  semaphore_wait(q->mutex);                    // Blocks consumers if queue is full.

  int item = q->array[q->next_out];
  q->next_out = queue_incr(q, q->next_out);

  semaphore_signal(q->mutex);
  semaphore_signal(q->spaces);                 // Keeps track of how many SPACES are **NOW** available.
  return item;
}
```
