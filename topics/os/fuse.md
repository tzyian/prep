### FUSE (Filesystem in Userspace)

#### What is FUSE?
- **FUSE** = *Filesystem in Userspace*.  
- A framework that allows developers to implement a fully functional filesystem in **user space**, rather than writing kernel code.  
- Kernel provides a FUSE module that forwards filesystem calls (open, read, write, etc.) from the VFS (Virtual File System) layer to a user-space process.

---

#### Why FUSE?
- Writing kernel modules is complex, risky (bugs crash the system), and requires privileged access.  
- FUSE makes it easy to:
  - Prototype filesystems quickly.  
  - Run without kernel-level coding.  
  - Safely isolate filesystem logic in user space.  

---

#### How it Works (Flow)
1. **Application** calls file operation (e.g., `open("/mnt/fuse/file.txt")`).  
2. The **Linux VFS layer** forwards the request to the **FUSE kernel module**.  
3. FUSE kernel module sends the request via `/dev/fuse` to a **user-space daemon** (filesystem implementation).  
4. The daemon processes the request (could fetch from remote server, DB, compressed archive, etc.).  
5. Response goes back to the kernel module → returned to the application.  

---

#### Architecture Diagram
```
+----------------------+  
| User Application | e.g. cat file.txt  
+----------------------+  
|  
v  
+----------------------+  
| VFS | Virtual File System layer  
+----------------------+  
|  
v  
+----------------------+  
| FUSE Kernel Module | <--> /dev/fuse  
+----------------------+  
|  
v  
+----------------------+  
| User-space Daemon | custom FS logic  
+----------------------+  
|  
v  
Backend (DB, S3, custom API, encrypted store, etc.)
```



---

#### Example Use Cases
- **EncFS** → Encrypted filesystem in user space.  
- **sshfs** → Mount remote directories over SSH as local folders.  
- **GlusterFS, CephFS clients** → Sometimes provide FUSE-based mounts.  
- **Custom filesystems** → Map DB records, cloud storage, or virtual objects into a filesystem interface.

---

#### Advantages
- No need to write kernel code.  
- Safer: bugs don’t crash the kernel.  
- Portable across Linux systems.  
- Easy prototyping and debugging.  

#### Disadvantages
- Extra context switches (kernel ↔ user space) → slower than in-kernel filesystems.  
- Higher overhead on metadata-heavy workloads.  
- Not suitable for very high-performance low-latency use cases.  

---

#### Example: Hello World FUSE (C)
```c
#define FUSE_USE_VERSION 31
#include <fuse3/fuse.h>
#include <string.h>

static int hello_read(const char *path, char *buf, size_t size,
                      off_t offset, struct fuse_file_info *fi) {
    const char *msg = "Hello from FUSE!\n";
    if (strcmp(path, "/hello") != 0)
        return -ENOENT;

    size_t len = strlen(msg);
    if (offset < len) {
        if (offset + size > len) size = len - offset;
        memcpy(buf, msg + offset, size);
    } else {
        size = 0;
    }
    return size;
}

static struct fuse_operations ops = {
    .read = hello_read,
};

int main(int argc, char *argv[]) {
    return fuse_main(argc, argv, &ops, NULL);
}
