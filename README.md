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

- `<<` to left-shift bits.
- Doubles a number.
- `5 << 2` --> 5 << 1 = 10. 10 << 1 = 20.

- `>>` to right-shift bits.
- Divides by 2, rounded down.
- `10 >> 2` --> 10 >> 1 = 5, 5 >> 1 = 2.

