### Zero-Copy — TL;DR


#### Key idea

> “Zero-copy” doesn’t mean _literally zero_ copies —  
> it means **no redundant user↔kernel copies**; kernel and hardware may still copy between buffers they control.


#### What it means
Normally, sending data between disk, kernel, and user space requires **copying buffers** several times:

```
[Disk] → [Kernel buffer] → [User buffer] → [Socket buffer] → [NIC]
```


Each copy = CPU + memory overhead.

**Zero-copy** avoids those redundant memory copies — data stays in kernel space and is passed between subsystems by reference.

---

#### Why it matters
- Saves **CPU cycles** (no memcpy between user and kernel).  
- Saves **memory bandwidth**.  
- Critical for **high-throughput servers** (file servers, proxies, Kafka, etc.).

---

#### How it’s implemented

| Mechanism / API | What it does | Notes |
|------------------|--------------|-------|
| **`sendfile()`** | Copies data directly from a file descriptor to a socket | File → NIC, no user-space copy |
| **`splice()` / `tee()`** | Moves data between pipes or fds inside the kernel | Used in proxies, stream relays |
| **`mmap()`** | Maps file pages directly into a process’s address space | App can access file without `read()` copy |
| **`io_uring` with `IORING_OP_SEND_ZC`** | Explicit kernel zero-copy send support | Linux ≥ 5.19 |
| **DMA (Direct Memory Access)** | Devices transfer data directly between memory and hardware | Hardware-level zero-copy |

---

#### Example — Traditional vs Zero-Copy

**Traditional read/write:**
```c
char buf[4096];
int n = read(in_fd, buf, 4096);     // copy: kernel → user
write(out_fd, buf, n);              // copy: user → kernel
```


|Aspect|Normal I/O|Zero-Copy I/O|
|---|---|---|
|Data path|Disk → kernel → user → kernel → NIC|Disk → kernel → NIC|
|CPU usage|High (memcpy)|Low|
|Latency|Higher|Lower|
|APIs|`read/write`|`sendfile`, `splice`, `mmap`, `io_uring`|
