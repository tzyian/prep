# Paging in Operating Systems

## 1. What is Paging?
- **Paging** is a memory management technique where:
  - Virtual memory (process address space) is divided into fixed-size **pages**.
  - Physical memory (RAM) is divided into fixed-size **frames** (same size as a page).
- The OS uses a **page table** to map each virtual page to a physical frame.

---

## 2. Key Terms
- **Page** → block of virtual memory (e.g., 4 KB).
- **Frame** → block of physical memory (same size).
- **Page Table** → per-process mapping: virtual page number → physical frame number.
- **MMU (Memory Management Unit)** → hardware that translates virtual addresses to physical addresses.
- **TLB (Translation Lookaside Buffer)** → CPU cache that speeds up page table lookups.

---

## 3. Address Translation
A virtual address is split into:
- **Virtual Page Number (VPN)** → index into page table.
- **Page Offset** → location within the page.

**Process:**
1. CPU generates virtual address: `VPN | offset`.
2. MMU uses VPN to look up PFN (physical frame number) in page table (or TLB).
3. Physical address = `PFN | offset`.
4. Access RAM at that physical address.

---

## 4. Example
- Page size = 4 KB (offset = 12 bits).
- Virtual address = `0x12345`:
  - VPN = upper bits → `0x12`.
  - Offset = lower 12 bits → `0x345`.
- Page table says VPN `0x12` → PFN `0x9A`.
- Physical address = `0x9A345`.

---

## 5. Demand Paging
- Pages are loaded into RAM **only when accessed**.
- If page is missing → **page fault** occurs:
  1. OS pauses process.
  2. Loads page from disk (swap area).
  3. Updates page table.
  4. Resumes process.

---

## 6. Multi-level Paging
- Large address spaces need huge page tables → wasteful.
- Solution: **hierarchical page tables** (2-level, 3-level, 4-level):
  - Virtual address split into multiple parts: e.g., `Dir → Table → Page → Offset`.
  - Each level narrows down the mapping.

---

## 7. Advantages
- Eliminates **external fragmentation** (since frames are fixed size).
- Provides **isolation** between processes.
- Enables **virtual memory** larger than physical RAM.

## 8. Disadvantages
- Page table overhead (can be very large).
- Page faults are expensive (disk I/O).
- TLB misses add latency.

---

## 9. Diagram

Virtual Address: [ VPN | Offset ]  
|  
v  
+-----------------+  
| Page Table |  
+-----------------+  
|  
v  
Physical Address: [ PFN | Offset ]