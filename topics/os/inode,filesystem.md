

![[Pasted image 20251002124641.png]]

![[Pasted image 20251002124522.png]]

# FD Table, Open File Table, Vnode/Inode Table

In Unix-like kernels (Linux, BSD, Solaris, macOS), file access is layered.  
Three key structures:

---

## 1. File Descriptor Table (FD Table)
- **Scope**: Per-process.
- **Type**: Array (or hash) of file descriptors (`int` indices).
- **Entry**: Each `fd` entry points to an **open file description** (struct file in Linux).
- **FD Flags**: Stored here (`FD_CLOEXEC` etc.).

**Lifetime**:
- Created when process calls `open()`, `socket()`, etc.
- Inherited on `fork()`.
- Destroyed on `close(fd)` (decrements refcount on underlying open file).

**Key point**: The **FD table is per-process**, so fd=3 in process A is unrelated to fd=3 in process B.

---

## 2. Open File Table (a.k.a. File Table / Open File Description Table)
- **Scope**: System-wide (shared).
- **Type**: Table of "open file descriptions" (POSIX term).
- **Entry fields**:
  - File status flags (`O_APPEND`, `O_NONBLOCK`, `O_SYNC`…).
  - Current **file offset**.
  - Reference count (# of FDs pointing here).
  - Pointer to **vnode/inode**.
  - Pointer to file ops (vtable: read, write, mmap, …).

**Sharing**:
- If you `dup()` or `fork()`, multiple FDs may point to the same open file entry.
- Thus they share file offset and status flags.

**Lifetime**:
- Created on `open()`.
- Freed when last FD pointing to it is closed.

---

## 3. Vnode/Inode Table
- **Scope**: System-wide cache of filesystem objects.
- **Vnode** = abstraction in VFS (Virtual File System layer).
- **Inode** = actual on-disk metadata structure (ext4, XFS, etc.).
- **Entry fields**:
  - File type (regular, directory, char device…).
  - Permissions, owner, timestamps.
  - Size, block pointers, etc.
  - Refcount (# of open file entries pointing here).
  - Ops vector: filesystem-specific functions (ext4_read, nfs_read, etc.).

**Sharing**:
- Multiple open file entries can point to the same vnode/inode (e.g., two processes opening the same path separately).

**Lifetime**:
- Cached in kernel memory.
- Evicted under memory pressure (like page cache).
- Flushed to disk on `fsync` or regular writeback.

---

## Putting it all together (flow of `open()`)

```
Process A (per-process FD table)  
┌───────────┐  
│ fd=3 ────────────────┐  
│ fd=4 ──┐ │  
└────────┘ │  
           ▼  
Open File Table (system-wide)  
┌───────────────────────────────┐  
│ struct file                   │  
│ - flags: O_RDWR, O_APPEND     │  
│ - offset: 1024                │  
│ - refcount: 2                 │  
│ - f_ops: vtable ptr           │  
│ - vnode*: 0xabcd              │  
└───────────────────────────────┘  
                │  
                ▼  
Vnode / Inode Table (system-wide)  
┌──────────────────────────────┐  
│ struct inode                 │  
│ - mode: regular file         │  
│ - uid/gid, perms             │  
│ - size: 4 KB                 │  
│ - block pointers             │  
│ - refcount: 1                │  
│ - fs-specific ops            │  
└──────────────────────────────┘
```


- FD table entry = **handle per process**.
- Open file entry = **shared open description** (offset, flags).
- Vnode/inode = **filesystem object metadata**.

---

## Key Subtleties

1. **Duplication and Fork**
   - `dup(fd)` → new FD points to the **same open file entry**.
   - `fork()` → child’s FD table copies parent’s pointers, still pointing to same open file entries.

2. **Offset Sharing**
   - If two FDs share the same open file entry (via dup/fork), `lseek()` in one changes offset for both.

3. **Independent Opens**
   - Two separate `open(path)` calls create **different open file entries**, even if they reference the same inode. Offsets are independent.

4. **Device and Socket FDs**
   - Same structure: inodes point to device drivers or socket implementations via vtables.

---

## Quick Analogy
- **Inode/Vnode** = the book in the library (metadata + content).
- **Open File Table entry** = a borrower’s reading session (bookmark, reading mode).
- **FD table entry** = your personal library card slot pointing to your borrowed book.

