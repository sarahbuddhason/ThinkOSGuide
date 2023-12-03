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
 
## Kernel and Daemon Processes
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

