i.e. Avoids at least one extra copy through user space
- kernel and hardware may still copy between buffers 
- reduces memcpy CPU cycles
- saves memory bandwidth
- data can stay within kernel space and is accessed 

| Aspect    | Normal I/O                          | Zero-Copy I/O                            |
| --------- | ----------------------------------- | ---------------------------------------- |
| Data path | Disk → kernel → user → kernel → NIC | Disk → kernel → NIC                      |
| CPU usage | High (memcpy)                       | Low                                      |
| Latency   | Higher                              | Lower                                    |
| APIs      | `read/write`                        | `sendfile`, `splice`, `mmap`, `io_uring` |


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

**Traditional read/write:**
```c
char buf[4096];
int n = read(in_fd, buf, 4096);     // copy: kernel → user
write(out_fd, buf, n);              // copy: user → kernel
```



