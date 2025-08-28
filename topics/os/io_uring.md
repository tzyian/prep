### io_uring

#### What is it?
- **io_uring** is a Linux kernel interface (introduced in Linux 5.1) for **asynchronous I/O**.  
- Designed to replace older async interfaces (`aio`, `epoll`) with a **fast, efficient, flexible** mechanism.  
- Uses **ring buffers** shared between user space and kernel → minimizes syscalls and context switches.

---

#### How it Works
- Two circular queues (rings) are shared between user space and kernel:
  1. **Submission Queue (SQ)**: User places I/O requests (e.g., read, write).  
  2. **Completion Queue (CQ)**: Kernel places results of finished requests.  

- The app:
  1. Prepares an I/O request (`struct io_uring_sqe`).  
  2. Places it in the SQ.  
  3. Calls `io_uring_enter()` (or uses SQPOLL mode where kernel polls).  
  4. Kernel executes the I/O asynchronously.  
  5. App retrieves completion events from the CQ (`struct io_uring_cqe`).  

---

#### Key Features
- **Batch submission/completion**: Submit many requests at once, reduce syscalls.  
- **Async for everything**: Not just files → sockets, timeouts, accept, send/recv, etc.  
- **Zero-copy support**: Can register buffers with kernel (`IORING_REGISTER_BUFFERS`).  
- **Fixed file descriptors**: Avoids per-request FD lookups.  
- **Linked operations**: Chain requests (e.g., open → read → close).  
- **SQPOLL mode**: Dedicated kernel thread polls SQ to remove syscall overhead.  

---

#### Example (C)
Simple `io_uring` read from a file:

```c
#include <liburing.h>
#include <fcntl.h>
#include <string.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    struct io_uring ring;
    io_uring_queue_init(8, &ring, 0);

    int fd = open("test.txt", O_RDONLY);
    char buf[128];

    // Get a submission queue entry
    struct io_uring_sqe *sqe = io_uring_get_sqe(&ring);
    io_uring_prep_read(sqe, fd, buf, sizeof(buf), 0);

    // Submit request
    io_uring_submit(&ring);

    // Wait for completion
    struct io_uring_cqe *cqe;
    io_uring_wait_cqe(&ring, &cqe);

    printf("Read: %.*s\n", cqe->res, buf);

    io_uring_cqe_seen(&ring, cqe);
    io_uring_queue_exit(&ring);
    close(fd);
    return 0;
}
```


#### Why io_uring Matters

- **Performance**: Much lower syscall overhead than `epoll` or `aio`.
- **Flexibility**: Works with files, sockets, timers, etc.
- **Future-proof**: Expanding API (e.g., `sendmsg`, `recvmsg`, `statx`, splice).
- **Adoption**: Used in high-performance apps (databases, proxies, storage engines).

|API|Model|Limitations|
|---|---|---|
|`blocking`|Simple, synchronous|Wastes CPU waiting for I/O|
|`epoll`|Event-driven|Still needs separate syscalls per op|
|`aio`|POSIX async I/O|Limited support, clunky API|
|`io_uring`|Shared rings|Modern, high-perf, unified async API|