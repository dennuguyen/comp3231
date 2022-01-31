# Operating Systems 

## What is an Operating System?

- OS is an abstract machine
- OS allocates resources to users and processes
- OS is the software that runs in privileged mode

> <sup>[<b>Privileged Mode</b>: OS has access to the whole architecture instruction set, all memory, all devices](#privileged_mode)</sup>

> <sup>[<b>User Mode</b>: Users have access to a subset of the architecture instruction set and their own memory space](#user_mode)</sup>

> <sup>[<b>Kernel</b>: OS running in privileged mode](#kernel)</sup>

## Structure of OS

- Usually monolothic: *Application&#8594; System Libraries &#8594; System Calls &#8594; OS*
- OS has control over applications through system calls and interrupts

> <sup>[<b>Application</b>: Software that runs in User mode](#application)</sup>

> <sup>[<b>System Libraries</b>: Libraries of support functions](#system_libraries)</sup>

## Processes

- Aka task or job
- Execution of an individual program
- "Owner" of resources allocated for program execution
- Encompasses one or more threads

A process can be created by any of the following events:
- System initialisation:
    - Foreground processes
    - Background processes (daemon, service)
- Execution of process creation system call by a running process
- User request to create new process
- Initiation of a batch job

A process can be terminated by any of the following conditions:
- Normal exit (voluntary)
- Error exit (voluntary)
- Fatal error (involuntary)
- Killed by another process (involuntary)

A process can be in 1 of 3 states:
- Running
- Blocked
- Ready

A process' information is stored in a process control block (PCB):
- Registers
- Process state
- Process ID
- Pointers to different memory segments
- Priority
- File descriptors

A process consists of at least 3 segments:
- Text
- Data
- Stack

## Threads

- Unit of execution
- Can be traced
- Belongs to a process

The thread model is a model that separates execution from the environment. Threads have:
- Stack
- State
- Local variables
- Program counter
- Registers

Threads can share items within their process:
- Global variables
- Open files
- Address space

The benefits of threads are:
- Simpler than a state machine
- Cheaper to create and destroy
- Can take advantage of parallelism on multiprocessor CPUs
- Better performance if threads waiting for I/O can be overlapped with computing threads

### User-Level Threads

User-level threads are implemented with a user-level thread control block (TCB), ready queue, blocked queue, and dispatcher.

The benefits of user-level threads are:
- management and user-level switching is fast
- dispatcher algorithm can be turned into an application
- can be implemented on any OS
- can easily support large number of threads

The disadvantages of user-level threads are:
- have to `yield()` threads manually
- does not take advantage of multiple CPUs
- if thread blocks then process blocks

### Kernel-Provided Threads

Kernel-provided threads are implemented with a kernel-level TCB. Thread management calls are implemented as system calls.

The benefits of kernel-provided threads are:
- Preemptive multithreading
- Parallelism

The disadvantages of kernel-provided threads are:
- More expensive to create, destroy, block, and unblock kernel-provided threads

## Context Switch

A context switch refers to a switch between processes/threads.

A context switch can occur anytime the operating system is invoked:
- on a system call
- on an exception
- on an interrupt

# Concurrency & Synchronisation

> <sup>[<b>Concurrency</b>: Ability for units of a program to execute out-of-order or in partial order without affecting the final outcome](#concurrency)</sup>

## Critical Region

A region of code where shared resources are accessed. Uncoordinated entry into critical regions can result in a race condition.

> <sup>[<b>Race Condition</b>: Condition where system behaviour is dependent on sequence or timing of uncontrollable events](#race_condition)</sup>

> <sup>[<b>Mutual Exclusion</b>: Property of concurrency to prevent race conditions](#mutual_exclusion)</sup>

Critical region solutions:
- Locks
    - Acquire lock before critical region
    - Release lock after critical region
- Disabling interrupts
    - Disable interrupts before critical region
    - Enable interrupts after critical region
- Hardware support
    - Test and set instruction
    - If `lock == 0`, set and acquire lock
    - If `lock == 1`, another process/thread has lock
- Semaphores
    - Process waits for a resource if it's not available (put in wait queue)
    - Process will signal when releasing resource, and any process waiting on that resource will be resumed
    - `P()`: test/wait/down
    - `V()`: increment/signal/up
- Monitors
    - Set of procedures, variables, datatypes grouped in a module
    - Only one process/thread can be in the monitor at any one time
    - Condition variable allows process to wait within monitor

> <sup>[<b>Spinlock</b>: A lock which causes a thread to wait in a loop to check if the lock is available - busy waiting](#spinlock)</sup>

> <sup>[<b>Condition Variables</b>: Container of threads waiting for a certain condition. They allow a process to wait within a monitor until some condition is met](#condition_variables)</sup>

> <sup>[<b>Synchronisation Primitives</b>: Simple software mechanisms for users to support thread or process synchronisation.](#synchronisation_primitives)</sup>

# Deadlock

## What is a Deadlock?

A set of processes is deadlocked if each process in the set is waiting for an event that only another process in the set can cause.

Deadlock occurs if all the following conditions are true:
1. Mutual exclusion condition
    - Each resource is assigned to a process or is available.
1. Hold and wait condition
    - Processes holding resources can request additional resources.
1. No preemption condition
    - Granted resources cannot be forcibly taken.
1. Circular wait condition
    - Must be a circular chain > 1 processes.

> <sup>[<b>Resource</b>: Can be anything e.g. devices, locks, table, etc.](#resource)</sup>

> <sup>[<b>Preemptable Resource</b>: Resources that can be taken away from a process/thread without ill effects](#preemptable_resource)</sup>

> <sup>[<b>Nonpreemptable Resource</b>: Resources that will cause process/thread to fail if taken away](#nonpreemptable_resource)</sup>

> <sup>[<b>Dining Philosophers Problem</b>](#dining_philosophers_problem)</sup>

> <sup>[<b>Reader Writer Problem</b>](#reader_writer_problem)</sup>

## Dealing with Deadlocks

Strategies for dealing with deadlocks:
1. Ostrich algorithm.
    - Ignore the problem.
    - Appropriate if problem is rare and cost of prevention is high.
1. Negate one of the four necessary conditions.
    - Attacking **mutual exclusion condition** is not feasible in general.
    - Attacking **hold and wait condition**.
        - Requires processes to request resources before starting.
        - Prone to livelock.
    - Attacking the **no preemption condition** is not viable.
    - Attacking the **circular wait condition**.
        - Numerically order resources.
        - Break circular chain.
1. Detection and recovery.
    - Detecting when deadlock occurs.

        Let:
        
        Existing resources be:
        ```math
        E = (e_{1}, e_{2}, ..., e_{m})
        ```

        Available resources be:
        ```math
        A = (a_{1}, a_{2}, ..., a_{m})
        ```

        Current allocation matrix (where row n is the current allocation to process n) be
        ```math
        C =
        \begin{bmatrix}
            c_{11} & c_{12} & c_{13} & \dots & c_{1m} \\
            c_{21} & c_{22} & c_{23} & \dots & c_{2m} \\
            \vdots & \vdots & \vdots & \ddots & \vdots \\
            c_{n1} & c_{n2} & c_{n3} & \dots & c_{nm}
        \end{bmatrix}
        ```

        Request matrix (where row n is what process n needs) be
        ```math
        R =
        \begin{bmatrix}
            r_{11} & r_{12} & r_{13} & \dots & r_{1m} \\
            r_{21} & r_{22} & r_{23} & \dots & r_{2m} \\
            \vdots & \vdots & \vdots & \ddots & \vdots \\
            r_{n1} & r_{n2} & r_{n3} & \dots & r_{nm}
        \end{bmatrix}
        ```

        1. Look for unmarked process Pi, for which the *i*-th row of R is less than or equal to A.
        1. If found, add the *i*-th row of C to A, and mark Pi. Go to step 1.
        1. If no such processes exists, terminate.

    - Recovering from deadlock through preemption.
        - Take a resouce from some other process.
    
    - Recovering from deadlock through rollback.
        - Save state periodically. Restart process if deadlocked.

    - Recovery through killing processes.
        - Simplest method.
        - Kill one of the processes in deadlock cycle.

1. Dynamic avoidance (careful resource allocation).
    - Only grant resource allocation that result in safe states.
    - [Safe](#safe_states) vs [unsafe](#unsafe_states) states.
    - Bankers Algorithm:
        - Banker has limited amount of money to loan clients.
        - Each client can borrow money up to client's credit limit.
        - Not common in practice because need to know number of processes and resources required.

> <sup>[<b>Safe States</b>: A state that guarantees eventual completion of processes i.e. there is no deadlock and there exists a scheduling order that results in every process running to completion](#safe_states)</sup>

> <sup>[<b>Unsafe States</b>: A state that cannot guarantee the eventual completion of processes](#unsafe_states)</sup>

## Livelock

Livelocked processes are not blocked, change state regularly, but never make progress.

## Starvation

A process that never receives the resource it is waiting for, despite resources (repeatedly) becoming free. The resource is always allocated to another waiting process.

# System Calls

Special function calls that provide controlled entry into the kernel and returns to the caller with a result.

Some examples:
- File operations (`read()`, `write()`, ...)
- Process management (`fork()`, `exit()`, ...)
- Directory management (`mkdir()`, `link()`, ...)

## MIPS

System calls are invoked via `syscall` which causes an exception and transfers control to the general exception handler.

## OS/161

`syscall` arguments are passed and returned via normal C function calling conventions.

Additionally:
- Register `v0` contains `syscall` number
- Register `a3` contains:
    - `0` if success with `v0` containing result
    - `errno` on error with `v0` containing `-1

# Computer Hardware, Memory Hierarchy, Caching

## Memory Hierarchy

<table>
    <tr>
        <th> Typical Access Time </th>
        <th> Memory Type </th>
        <th> Typical Capacity </th>
    </tr>
    <tr>
        <td> 1 ns </td>
        <td> Registers </td>
        <td> < 1 KB </td>
    </tr>
    <tr>
        <td> 2 ns </td>
        <td> Cache </td>
        <td> 1 MB </td>
    </tr>
    <tr>
        <td> 10 ns </td>
        <td> Main Memory </td>
        <td> 64 - 512 MB </td>
    </tr>
    <tr>
        <td> 10 ms </td>
        <td> Magnetic Disk </td>
        <td> 5 - 50 GB </td>
    </tr>
    <tr>
        <td> 100 s </td>
        <td> Magnetic Tape </td>
        <td> 20 - 100 GB </td>
    </tr>
</table>

Going down the hierarchy:
- Decreasing cost per bit
- Increasing capacity
- Increasing access time
- Decreasing frequency of access to memory by processor

## Caching

Caching allows for faster access to slower storage by using intermediate-speed storage as a cache.

### CPU Cache

CPU Cache is located between the CPU and main memory.
- 1 to a few cycles when accessed
- Hardware maintained
- Uses a hierarchy of caches (2-5 levels)

The effective access time is:
```math
T_{eff} = H \times T_{1} + (1 - H) \times T_{2}
```

Where:
- Teff is effective access time
- T1 is access time of memory 1 (e.g. cache memory)
- T2 is access time of memory 2 (e.g. main memory)
- H is hit rate in memory 1 (e.g. 95%)

## Disk Access

Disk access is very slow relative to main memory and is dominated by the time taken to move the head over data.

To partially fix this, the OS keeps a subset of the disk's data in main memory.

This principle is used for other slow access times, such as accessing media via the internet.

# File Management

- Files are a named repository for data.
- Files can potentially hold large amounts of data.
- A file's lifetime is independent of process lifetime and can be used to share data between processes.
- Files are convenient to input data into processes and save data from processes.
- Criteria for file organisation:
    - Rapid access.
    - Ease of update.
    - Economy of storage.

## File System (FS)

- Objectives:
    - Provide convenient file naming system.
    - Provide uniform I/O support for variety of storage device types.
    - Optimise performance.
    - Minimise or eliminate potential for lost or destroyed data.
    - Provide standardised set of I/O interface routines.
    - Ensure data in file is valid.
    - Provide I/O support and access control for multiple users.
    - Support system administration (e.g. backups).

## File Properties

### Names

- File names are for benefit of the user.
- Provide a level of abstraction from physical locations.
- File extensions are a tool used in maing files that gives users an understanding of what kind of data is contained in the file.

### Structure

- There exists three kinds of files:
    - Byte sequence:
        - Unstructured, simple management for OS.
    - Record sequence:
        - Series of bytes containing specific type of data.
        - File is made up of sequences of these.
        - OS optimises access to these records.
        - Used in old databases.
    - Key-based, tree structured:
        - Tree of record, each record has an associated key.
        - Data retrieval is performed by using the key.
        - Used in modern databases.

### Types

- Regular files:
    - Sequence of bits.
- Directories:
    - Files containing files.
    - Owned by OS.
    - Provides mapping between file names and files themselves.
- Device files:
    - Devices that can be interacted with as files.
    - Can be a bit stream.
- Executable files:
    - Files that contain application code.
    - OS must recognise files that contain executable code.

### File Access

- Sequential:
    - Read/write all data from beginning or from end.
    - No jumping around.
- Random:
    - Read/write data in any order at any location.
- Streams:
    - Read/write data sequentially or in blocks.

- Consider simultaneous file access:
    - OS provides mechanisms for simultaneous file access:
        - `flock()`, `lockf()`, system calls
    - Can lock entire file or records of file.

## File Operations

1. Create
1. Open
1. Close
1. Delete
1. Append
1. Seek
1. Get
1. Read
1. Write
1. Set Attributes
1. Rename

## Directory Operations

1. Create
1. Delete
1. Opendir
1. Closedir
1. Readdir
1. Rename
1. Link
1. Unlink

## Access Permissions

- None
    - Can't read, see, or write to file.
- Knowledge
    - Can see the file and who the owner is.
- Execution
    - Can load and execute a program but can't copy or write to it.
- Reading
    - Can read from a file.
- Appending
    - Can add data to the file but cannot modify or delete any of the file's contents.
- Updating
    - Can modify, delete, and add to the file's data.
- Changing protection
    - Can change access rights granted to other users.
- Deletion
    - Can delete a file.
- Owners
    - Has all rights previously listed.
    - May grant rights to others.

## UNIX Files

- UNIX file naming:
    - Simple, regular format.
    - Location independent.
- UNIX access permissions are:
    - `d` = is a directory
    - `r` = readable
    - `w` = writeable
    - `x` = executable
    - `-` = not
- UNIX permissions have 3 groups of RWX permissions: **user**, **group**, **other**.
    - `d rwx rwx rwx`

### UNIX Storage Stack

<table>
    <tr>
        <th> Item </th>
        <th> Description </th>
    </tr>
    <tr>
        <td> Application </td>
        <td> Syscall interface e.g. <code> open() </code> </td>
    </tr>
    <tr>
        <td> File descriptor table </td>
        <td> Keeps track of files opened by each process individually </td>
    </tr>
    <tr>
        <td> Open file table </td>
        <td> Keeps track of files opened by user level processes </td>
    </tr>
    <tr>
        <td> Virtual File System (VFS) </td>
        <td> Unified interface to multiple file systems </td>
    </tr>
    <tr>
        <td> File System (FS) </td>
        <td> Handles physical location of data on disk, exposes file/directories </td>
    </tr>
    <tr>
        <td> Buffer Cache </td>
        <td> Keeps recently accessed disk blocks in memory </td>
    </tr>
    <tr>
        <td> Disk Scheduler </td>
        <td> Schedule disk accesses from multiple processes </td>
    </tr>
    <tr>
        <td> Device Driver </td>
        <td> Handles device specific protocols </td>
    </tr>
    <tr>
        <td> Disk Controller </td>
        <td> Handles disk geometry, bad sectors </td>
    </tr>
    <tr>
        <td> HDD/SSD </td>
        <td> Primary storage device </td>
    </tr>
</table>

## File Allocation Methods

- A file is divided into "blocks".
    - Unit of transfer to storage.

> <sup>[<b>External Fragmentation</b>: Total memory space is enough to satisfy a request or to reside a process in it, but it is not contiguous so it cannot be used.](#external_fragmentation)</sup>

> <sup>[<b>Internal Fragmentation</b>: Memory block assigned to process is bigger. Some portion of memory is left unused as it cannot be used by another process.](#internal_fragmentation)</sup>

### Contiguous Allocation

<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Easy bookkeeping <br><br> Increases performance for sequential operations </td>
        <td> Needs max file size on creation <br><br> External fragmentation </td>
    </tr>
</table>

### Dynamic Allocation Strategies

- Disk space allocated in portions as needed.
- Allocation occurs in fixed-size blocks.

<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> No external fragmentation <br><br> Does not require pre-allocating disk space </td>
        <td> Internal fragmentation <br><br> File blocks are scattered across disk <br><br> Complex metadata management </td>
    </tr>
</table>

#### Linked List Allocation

- Each block contains a pointer to the next block in the chain.

<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Only a single metadata entry per file <br><br> Best for sequential files </td>
        <td> Poor random access <br><br> Blocks end up scattered across disk </td>
    </tr>
</table>

#### File Allocation Table (FAT)

- Keep a map of the entire FS in a separate table.
    - A table entry contains the number of the next block of the file.
    - The last block in a file and empty blocks are marked using reserved values.
- Table is stored on disk and is replicated in memory.

<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Fast random access </td>
        <td> Requires a lot of memory for large disks <br><br> Free block lookup is slow </td>
    </tr>
</table>

#### i-Node-Based FS Structure

- Separate table (i.e. index-node) for each file.
    - Only keep table for open files in memory.
- Popular FS structure today.

<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Fast random access </td>
        <td> Requires free-space management </td>
    </tr>
</table>

## Fixed-Size vs Variable=Size Directory Entries

- Fixed-size directory entries:
    - Either too small or waste too much space.
- Variable-size directory entries:
    - Freeing variable length entries can create external fragmentation in directory blocks.

## Searching Directory Listings

- Locating file in directory:
    - Linear scan
    - Hash lookup
    - B-tree (good for 100,000's of entries)

## Trade-Off in FS Block Size

- FS deal with 2 types of blocks:
    - Disk blocks or sectors (usually 512 bytes)
    - File system blocks (512 * 2^n bytes)
    - What is the optimal n?
- Larger blocks (larger n) require less FS metadata.
- Smaller blocks waste less disk space (less internal fragmentation).
- Sequential access:
    - The larger the block size, the fewer I/O operations required.
- Random access:
    - The larger the block size, the more unrelated data loaded.
    - Spatial locality improves situation.
- Choosing appropriate block size is a compromise.

## Virtual File System (VFS)

### Vnodes

- Represents a file (i-node) in the underlying FS.
- Points to the real i-node.
Contains pointers to functions to manipulate files/i-nodes.

### File Descriptor Table

### Open File Table

### Buffer Cache

### Cache

### File System Consistency

### Disk Scheduler

### Disk Arm Scheduling Algorithms

- First-Come First-Serve (FCFS)
- Shortest Seek Time First (SSTF)
- Elevator Algorithm (SCAN)
- Modified Elevator (C-SCAN)

## Ext2

## Ext3

# Memory Management

- Keeps track of what memory is in use and what memory is free.

> <sup>[<b>Compaction</b>: The shuffling of memory contents to place all free memory together in one large block.](#compaction)</sup>

> <sup>[<b>Swapping</b>: A process can be *swapped* temporarily out of memory then brought back into memory for continued execution.](#swapping)</sup>

## Fixed Partitioning

- Divides memory into fixed equal-sized partitions.
- Any process smaller or equal to partition size can be loaded into the partition.
- Partitions are free or busy.

<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Simple <br><br> Easy to implement </td>
        <td> Internal fragmentation <br><br> Processes smaller than main memory, but larger than a partition cannot run </td>
    </tr>
</table>

## Dynamic Partitioning

- Partitions are of variable length.
    - Allocated on-demand from ranges of free memory.
- Problems:
    - [External fragmentation](#external_fragmentation).

<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Processes are allocated the memory it needs </td>
        <td> External fragmentation </td>
    </tr>
</table>

## Dynamic Partitioning Allocation Algorithms

### Classic Approach

- Represent available memory as a linked list of available "holes".
    - Base, size.
    - Kept in order of increasing address.
    - List nodes be stored in the "holes" themselves.

## Dynamic Partitioning Placement Algorithms

- Algorithms to place partitions in memory in order of best to worst.

### First-Fit

- Scan the list for the first entry that fits.
- Aims to find a match quickly.
- Biases allocation to one end of memory.
- Tends to preserve larger blocks at the end of memory.

### Next-Fit

- Same as first-fit but begin search from point in list where last request succeeded.
- Tries (poorly) to spread allocation more uniformly over entire memory to reduce biased allocation.
- Performs worse than first-fit as it breaks up large free spaces at end of memory.

### Best-Fit

- Chooses the block that is closest in size to the request.
- Has to search complete list.
- Severe external fragmentation.

### Worst-Fit

- Chooses the block that is largest in size to request.
- Has to search complete list.
- Does not result in significantly less fragmentation.

# Virtual Memory

- A layer of abstraction for processes accessing memory locations.
    - Allows memory protection.
    - Better performance for having a known fixed address for global variables.
    - Can swap out chunks of memory if memory is limited.
- Paging and segmentation are two techniques to implement virtual memory.

## Segmentation

- Supports user's view of memory.
- A program is a collection of segments (functions, methods, stack, arrays, variables, etc.).
- Logical address consists of a two-tuple (segment number and offset).
- A *segment table* has entries containing a base address and limit.
- Allows sharing of segments.

## Paging

- Partition physical memory into small equal-sized chunks called *frames*.
- Divide each process' virtual address space into same-sized chunks called *pages*.
- No external fragmentation
- Small internal fragmentation.
- Allows sharing by mapping several pages to the same frame.
- Abstracts physical organisation.

### Page Faults

- Referencing an invalid page triggers a page fault.
    - Illegal address.
    - Page not resident.

### Page Table

- Page tables are translations from virtual addresses to physical addresses.
- Implemented as an array of page table entries. 
- Page table entries have a `frame` number and the following bits:
    - `valid` bit indicates valid mapping for page.
    - `dirty` bit indicates page is writeable.
    - `reference` bit indicates page has been accessed.
    - `protection` bit indicates RWX permissions.
    - `caching` bit indicates whether processor should bypass cache when accessing memory.
- Page tables can be "levelled" with indirection to prevent memory allocation of unused space.

### Inverted Page Table

- An array of page numbers stored by frame number.
- Grows with RAM size, not virtual address space.
- Need a separate data structure for non-resident pages.
- Saves a vast amount of space.

### Hashed Page Table

- Similar to inverted page table but more efficient with its size based on physical memory size and not virtual.

## Translation Lookaside Buffer (TLB)

- Given a virtual address, the processor examines the TLB.
- If matching page table entry is found (i.e. TLB hit), the address is translated.
- Otherwise (i.e. TLB miss), the page number is used to index into the process' page table.
- Properties:
    - Holds recently used subset of page table entries.
    - TLB may or may not be under OS control.
    - Typically has 64 - 128 entries.
    - Can have separate TLBs for instruction fetch and data access.
    - Is a shared piece of hardware.
    - Entries are process specific.
- On context switch, TLB needs to be flushed so that all of its entries are invalidated to ensure memory protection.

## MIPS R3000

### MIPS R3000 TLB

```
struct entry {
  /* entry hi */
  unsigned int vpn  : 20; /* virtual page number */
  unsigned int asid : 6;  /* asid */
  /* next 6 bits unused */
  
  /* entry lo */
  unsigned int pfn : 20; /* page frame number */
  unsigned int n   : 1;  /* not cacheable bit */
  unsigned int d   : 1;  /* dirty bit */
  unsigned int v   : 1;  /* valid bit */
  unsigned int g   : 1;  /* global bit */
  /* next 8 bits unused */
};
```

### MIPS R3000 Address Space Layout

- `kseg0`
    - 512 MB.
    - TLB not used.
    - Cacheable.
    - Only kernel mode accessible.
    - Usually where the kernel code is placed.
- `kuseg`
    - 2 GB.
    - TLB translated.
    - Cachable.
    - User mode and kernel mode accessible.
    - Page size is 4KB.
- `kseg1`
    - 512 MB.
    - Not cacheable.
    - Only kernel mode accessible.
    - Where devices are accessed.

## Demand Paging

- Load pages to meet demand of running processes when needed.
- Less memory used effectively.

## Pre-Paging

- Load more pages into memory than required, hoping they will be used in future.
- Improves I/O performance by reading large chunks by using idle I/O time.
    - Can waste I/O bandwidth if pages aren't used.

## Principle of Locality

- A program spends 90% of its time in 10% of its code.
    - Can reasonably predict what instructions and data a program will use in the near future based on its accesses in the recent past.
- Temporal Locality: Recently addressed items are likely to be reused in near future.
- Spatial Locality: Items whose addresses are near one another are likely to be used closely in time.

## Working Set

- Pages/segments required by an application in a time window is called a memory working set.
- Working set is an approximation of process' locality.

## Thrashing

- Thrashing occurs when (different ways of explaining):
    - A process or process' working set no longer fits in memory and must be continually swapped in and out of disk.
    - Virtual memory resources are overused, leading to a constant state of paging and page faults.
- To recover from thrashing, must suspend a few processes until more memory becomes available.

## Page Replacement Algorithms

- Policies for page replacement ranked for performance are:
    1. Optimal:
        - Remove the page that will not be used for the longest time.
        - Impossible to implement.
    1. LRU:
        - Remove the least recently used page.
        - Impossible to implement efficiently.
            - Implementation requires a time stamp for every page.
            - Requires exhaustive search of all pages' timestamps to choose victim.
    1. Clock:
        - Kick next page on list unless it has been referenced recently which then gets a second chance.
        - When a page is used, set `reference` bit to `1`.
        - When scanning for a victim, remove the first page with a `reference` bit of `0`, and set all `reference` bits to `0`.
        - Slower and harder to implement than FIFO.
        - Faster and easier to implement than LRU.
    1. FIFO:
        - Remove the page that was earliest-loaded.
        - Easy to implement.
        - Age of page is not necessarily related to its usage e.g. can remove heavily-used pages that have been around for a long time.
        - "Belady's Anomaly" i.e. more physical pages do not imply fewer page faults.

# I/O

## I/O Management

- Challenges of I/O management are:
    - efficiency because I/O devices are slow compared to main memory and CPU.
    - uniformity because there is a large diversity of I/O devices.

### Device Drivers

- Device drivers translate device requests into a sequence of commands.
- Different devices may have different I/O data rates.
- Originally compiled into the kernel. Nowadays drivers are dynamically loaded when needed.
- Device drivers are classified into classes so there is uniformity in interfaces.
- Uniform device interface for kernel code:
    - Allows different devices to be used the same way.
    - Allows internal changes of driver code without fear of breaking kernel code.
- Uniform kernel interface for device code.
    - Drivers use a defined interface to kernel services.
    - Allows kernels to evolve without breaking existing drivers.
- Combination of both interfaces avoids a lot of programming implementation for new interfaces.

## I/O Interaction

## Accessing I/O Controllers

- Can have:
    - Separate I/O and memory space.
    - Memory-mapped I/O.
    - Hybrid of above two.
- Devices are connected to an interrupt controller which is connected to and interrupts the CPU.

### Programmed I/O

- Aka polling or busy-waiting.
- Controller performs an action.
- Sets appropriate bits in I/O status register.
- No interrupts to the CPU.
- Processor checks status until operation is completed.
    - But this wastes CPU cycles.

### Interrupt-Driven I/O

- Processor is interrupted when I/O module (controller) is ready to exchange data.
- Processor is free to do other work.
- No needless waiting.
- Consumes a lot of processor time.

### Direct Memory Access (DMA)

- Transfers data directly between memory and device.
- CPU is not needed for copying.
- Interrupt is sent when task is completed.
- Processor is only involved at beginning and end of data transfer.
- Modern devices have an in-built DMA controller.
<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Reduces total number of interrupts and context switches </td>
        <td> Requires contiguous regions </td>
    </tr>
</table>

## Interrupt Handlers

- Can execute at (almost) any time in order to:
    - raise concurrency issues in kernel.
    - propagate signals to userspace.
- Interrupt handler steps:
    1. Save registers.
    1. Set up stack for interrupt service procedure.
    1. Mask interrupt controller.
    1. Run interrupt service procedure.
    1. May wake up higher priority blocked threading.
    1. Load new or original process' registers.
    1. Re-enable interrupts.

## Sleeping in Interrupts

- Interrupts generally has no context.
    - It is unfair to sleep in interrupted process.
- We want to:
    - Minimise time spent in interrupt context.
    - Prevent users from calling functions which are not safe to invoke in an interrupt context.

### Top/Bottom Half

- Do work in a safer context.
- Top Half:
    - Interrupt handler
- Bottom Half:
    - Is a routine that is scheduled by the top half to be executed later at a safer time.
    - Pre-emptable by top half.
    - Performs deferred work.
    - Is checked prior to every kernel exit.
    - Cannot block.
- Stack usage:
    1. Higher-level software.
    1. Interrupt processing (interrupts disabled).
    1. Deferred processing (interrupt re-enabled).
    1. Interrupt while in bottom half.
- Only gives us low interrupt latency.

### Deferring Work on In-Kernel Threads

- Defer work to a separate in-kernel thread stack.
- It is costly to defer work to a separate thread because of context switching.
- Gives us both low interrupt latency and blocking operations.

## Buffering

- Considering how OS handles I/O system calls with buffering i.e. how is data transferred to/from OS.

### No buffering

- Processes must read/write (system call) a device a byte/word at a time.
    - Each individual system call adds significant overhead.
    - Processes must wait until each I/O is complete.
- Very inefficient.

### User-Level buffering

- Read/write I/O straight into userspace buffer.
- Process specifies a memory buffer that incoming data is put in until it's filled.
    - Filling can be done by interrupt service routine.
    - Only a single system call, and block/wakeup per data buffer (low overhead).
- Efficient.
- Issues:
    - Could lose data while unavailable buffer is paged in.
    - Could lock buffer in memory.
    - Can cause deadlock as RAM is limited.
    - Dealing with writing:
        - Can block until device drains buffer.
        - Can send asynchronous signals.

### Single Buffer

- Read/write I/O into kernel buffer then into userspace buffer.
- OS assigns a buffer in kernel's memory for I/O request.
- Block-oriented:
    - Input transfers made to buffer.
    - Block is copied to userspace when needed.
    - Another block is written into the buffer.
- User process can process a block of data while next block is read in.
- Swapping can occur since input is taking place in system memory and not user memory.
- OS keeps track of assignment of system buffers to user processes.
- Speedup:
    ```math
    \frac{T + C}{max(T, C) + M}
    ```
    - T is transfer time of block from device.
    - C is computation time to process incoming block.
    - M is time to copy kernel buffer to user buffer.
- If kernel buffer is full, the user buffer is swapped out or the application is slowly processing the previous buffer.
    - Losing characters or dropping network packets.

### Double Buffer

- Use 2 system buffers instead of 1.
- A process can transfer data to or from one buffer while the operating system empties or fills the other buffer.
- Speedup:
    ```math
    \frac{T + C}{max(T, C + M)}
    ```
    - T is transfer time of block from device.
    - C is computation time to process incoming block.
    - M is time to copy kernel buffer to user buffer. Assuming M is much less than T.
- May be insufficient for bursty traffic.
    - Lots of application writes between long periods of computation.
    - Long periods of application computation while receiving data.
    - Might want to read-ahead more than a single block for disk.

### Circular buffer

- More than two buffers are used.
- Used when I/O operation must keep up with process.
- Recall bounded-buffer producer-consumer problem.

### Issues with Buffering

- Buffer-copying reduces performance (increases latency) when networks are almost as fast as memory speeds.
    - Super-fast networks solve this with zero-copy networking.

# Multiprocessors

- Amdahl's Law:
    - Given a proportion P of a program that can be made parallel, and the remaining serial portion (1 - P), speedup by using N processors is:
    ```math
    \frac{1}{(1-P)+\frac{P}{N}}
    ```
- Types of multiprocesors (MP):
    - UMA MP.
        - Uniform memory access.
        - Access to all memory occurs at the same speed for all processors.
    - NUMA MP
        - Non-uniform memory access.
        - Access to some parts of memory is faster for some processors than other parts of memory.
- MP increase computation power beyond a single CPU.
- MP share resources such as disk and memory.
- Issues:
    - Assumes parallisable workload to be effective.
    - Assumes not I/O bound.
    - Shared buses limits scalability.

## Bus-Based UMA

- Simplest MP is more than one processor on a single bus connected to memory.
- Bus bandwidth becomes a bottleneck with more than a few CPUs.
- Each processor has a cache to reduce its need for memory access.

## Each CPU has own OS

- Each CPU has its own independent OS and system calls; peripherals are shared.
<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Simpler to implement <br><br> Scalable <br><br> Avoids CPU-based concurrency issues by not sharing </td>
        <td> Each processor has its own scheduling queue and can lead to 1 overloaded processor <br><br> Each processor has its own memory partition and can lead to 1 processor thrashing </td>
    </tr>
</table>

### Cache Consistency

- When 2 CPUs have the same address in their caches to read/write, how do we ensure the address value is consistent?
    - Cache consistency is handled by hardware.
    - Writes to one cache and propagates the value or invalidates appropriate entries on other caches.
    - Cache transactions also consume bus bandwidth.
- Scalability is limited by bus bandwith.

## Symmetric Multiprocessors (SMP)

- OS kernel runs on all processors.
    - Load and resources are balanced between all processors including kernel execution.
- There is real concurrency in the kernel so there needs to be carefully applied synchronisation primitives.
    - A single mutex to make entire kernel a critical section.
        - "Big lock" creates bottleneck.
    - Identify largely independent parts of the kernel and make each of them their own critical section.
        - Better parallelism in the kernel.
        - Difficult to implement.

## Test-and-Set

- Test-and-set instruction writes a 1 to a memory location and returns its old value as a single atomic operation.
- Test-and-set is a busy-wait synchronisation primitive aka spinlock.
    - Lock contention leads to spinning on the lock.
- Hardware guarantees that the instruction executes atomically on a CPU.

### Test-and-Set on SMP

- Hardware blocks all other CPUs from accessing the bus during the TSL instruction to prevent memory accesses by other CPUs.
- Caching does not help reduce bus contention.
    - Either, TSL still blocks the bus.
    - Or, TSL requires exclusive access to local cache entry.

### Reducing Bus Contention

- Read before TSL.
    - Spin reading the lock variable.
- Allows lock to be shared read-only in all caches until its release.
- No race conditions as acquisition is within TSL.
```
start:
    while (lock == 1);
    r = TSL(lock);
    if (r == 1)
        goto start;
```

# Scheduling

- Scheduler (or dispatcher) chooses which process is ready to execute.
- **Non-pre-emptive Scheduling:** Processes only stops when completed, I/O blocks, or voluntarily yielding.
- **Preemptive Scheduling:** Processes can be interrupted by the OS.

## Scheduling Algorithms

- Goals of scheduling algorithms:
    - Fairness (share of CPU).
    - Policy enforcement.
    - Balance/Efficiency (keep all parts of system busy).

### Batch System

- No users directly waiting.
- Optimise overall machine performance.

### Interactive System

- Users directly waiting for results.
- Optimise "perceived" performance.
- Goals:
    - Minimise response time.
    - Improve proportionality i.e. an expected short job should finish shorter, an expected long job should finish longer.

#### Round-Robin Scheduling

- Processes get timesliced.
- When timeslices expires, move onto next timeslice regardless if process is finished.
- Implemented using regular queues and interrupt timer.
- Smaller timeslices result in:
    - More switching.
    - Better responsiveness.
<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Easy to implement </td>
        <td> Assumes everybody is equal </td>
    </tr>
</table>

#### Priority Scheduling

- Process with higher priority gets executed first.
<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Important processes get priority </td>
        <td> Can starve low priority processes </td>
    </tr>
</table>

- Starvation of low priority processes can be solved with adapting priorities periodically or over time.

#### Traditional UNIX Scheduler

- 2-level scheduler.
- Array of round-robin queues where priorities are numbered from -16 to 16.
    - Negative priorities = processes in kernel mode.
    - Zero priority = basic stuff.
    - Positive priority = processes in user mode.
- **priority = cpu use + nice + base**
    - cpu use = if process uses CPU more then give it higher priority, decays over time.
    - nice = set by user.
    - base = set of hardwired negative values to boost priority of I/O bound system activities.

#### Multiprocessor Scheduling

- Process with higher priority gets executed first.
<table>
    <tr>
        <th> Pros </th>
        <th> Cons </th>
    </tr>
    <tr>
        <td> Simple to implement <br><br> Automatic load balancing </td>
        <td> Locks can bottleneck <br><br> Not all CPUs are equal e.g. related entries in cache </td>
    </tr>
</table>

#### Affinity-Based Scheduling

- Type of multiprocessor scheduling.
- Each CPU has its own ready queue.
- Coarse-grained algorithm assigns process to CPUs.
- Bottom-level fine-grained schedule:
    - is per-CPU and selects its own ready queue to ensure affinity.
    - if nothing is ready, it steals work from another CPU.

### Real-Time System

- Completion of jobs at particular times is more important.
- Goals:
    - Must meet deadlines
    - Provide predictability i.e. occasionally missed deadlines is okay.