### User Space vs Kernel Space

#### User Space
- **Definition**: Portion of system memory where **user applications** run.  
- **Access rights**: Restricted — cannot directly access hardware or critical OS structures.  
- **Isolation**: Each process has its own virtual address space → protects processes from crashing each other.  
- **Needs system calls**: To request OS services (file I/O, network, memory allocation).  

**Examples**:
- Programs like browsers, editors, databases.
- Standard libraries (`libc`, etc.) that wrap syscalls.

---

#### Kernel Space
- **Definition**: Portion of system memory reserved for the **operating system kernel** and drivers.  
- **Access rights**: Full privileged access — can execute any CPU instruction and directly interact with hardware.  
- **Shared**: One global kernel space is shared by all processes.  
- **Responsibilities**:
  - Process scheduling
  - Memory management
  - Filesystems
  - Device drivers
  - Networking stack

---

#### Key Differences

| Feature              | **User Space**                        | **Kernel Space**                |
|----------------------|----------------------------------------|----------------------------------|
| Who runs here        | User processes (apps)                 | OS kernel + drivers             |
| Privileges           | Limited (cannot access hardware directly) | Full, privileged access         |
| Memory isolation     | Each process has private address space | Single shared space             |
| Crash impact         | App crash affects only that process    | Kernel crash = entire system crash |
| System calls         | Required to cross into kernel space    | Executes requests directly      |

---

#### Interaction Flow (Example: `read()` system call)
1. App (user space) calls `read(fd, buf, n)`.  
2. CPU executes a **trap instruction** → switch to **kernel mode**.  
3. Kernel (kernel space) checks permissions, fetches data from disk.  
4. Kernel copies data into app’s buffer.  
5. Control switches back to user space.  

