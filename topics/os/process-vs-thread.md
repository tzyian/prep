### User Threads vs Kernel Threads vs Hybrid Threads

#### 1. User Threads
- **Definition**: Threads implemented entirely in **user space** (managed by a runtime library, not the OS kernel).
- **Scheduling**: Done by the user-level thread library (e.g., `pthreads` in pure user mode).
- **OS visibility**: Kernel sees only the **process**, not its user threads.
- **Context switch**: Fast (no kernel mode switch).
- **Blocking issue**: If one thread performs a blocking syscall, the **whole process blocks**.
- **Example systems**: Early Java Green Threads, GNU Portable Threads.

**Pros**:
- Very fast thread creation/switching (no kernel involvement).
- Portable across OSes.

**Cons**:
- Cannot leverage multiple CPUs (kernel only sees one process).
- One blocking syscall blocks all threads.

---

#### 2. Kernel Threads
- **Definition**: Threads managed directly by the **OS kernel**.
- **Scheduling**: Kernel schedules each thread individually.
- **OS visibility**: Kernel is aware of every thread.
- **Context switch**: More expensive (requires kernel mode).
- **Blocking**: If one thread blocks, others can still run (on same or different CPUs).
- **Example systems**: Linux (NPTL), Windows threads.

**Pros**:
- True parallelism across CPUs.
- Blocking syscalls only affect one thread.

**Cons**:
- Higher overhead (creation, context switching).
- Kernel involvement makes them heavier.

---

#### 3. Hybrid Threads (M:N Model)
- **Definition**: Combines user and kernel threads.
  - **M user threads** are mapped onto **N kernel threads**.
- **Scheduling**:
  - User-level scheduler maps threads to kernel threads.
  - Kernel schedules kernel threads on CPUs.
- **Goal**: Best of both worlds (fast user switching + kernel-level parallelism).
- **Example systems**: Solaris threads, some versions of Windows Fibers, GNU Scheduler Activations (research).

**Pros**:
- More efficient than pure kernel threads (lighter weight).
- Can exploit multiple CPUs.
- Flexible (user library decides mapping).

**Cons**:
- Complex implementation.
- Synchronization between user scheduler and kernel scheduler can be tricky.

---

#### Comparison Table

| Feature              | User Threads                     | Kernel Threads                    | Hybrid Threads (M:N)              |
|----------------------|----------------------------------|------------------------------------|-----------------------------------|
| Managed by           | User-level library               | OS Kernel                          | Both (library + kernel)           |
| Visible to OS        | No (only process is visible)     | Yes (each thread is visible)       | Yes (kernel sees N threads)       |
| Scheduling           | User library                     | Kernel scheduler                   | User + Kernel                     |
| Parallelism          | No (single CPU only)             | Yes (multi-core supported)         | Yes (multi-core supported)        |
| Blocking syscalls    | Block entire process             | Block only the calling thread      | User library can schedule others   |
| Context switch cost  | Very low                         | Higher (kernel mode switch)        | Medium                            |
| Complexity           | Simple                           | Moderate                           | High                              |



| Aspect        | Process                       | Thread (within process)         |
| ------------- | ----------------------------- | ------------------------------- |
| **Heap**      | Private (per process)         | Shared (all threads in process) |
| **Stack**     | One per process (main thread) | One per thread (private)        |
| **Isolation** | Heap/stack isolated by OS     | Heap shared, stacks private     |
| **Access**    | Other processes need IPC      | Threads can access same heap    |

---
- **Leveraging Multiple Cores**: Modern computers have multiple CPU cores. By running CPU-bound tasks in separate processes, each process can be assigned to a different core, achieving true parallel execution. If an application is single-threaded, it can only utilize one CPU core at a time, regardless of how many are available.[](https://stackoverflow.com/questions/12707924/multiple-process-vs-multiple-threads-for-cpu-intensive-and-io-intensive-processe)
    

**Memory Isolation**: Each process has its own independent memory space. This isolation is a major advantage for CPU-bound work, as it prevents parallel tasks from interfering with each other's data, which can lead to complex bugs like race conditions.