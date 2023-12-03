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

