### Memory-Mapped I/O (MMIO)

#### Definition
**Memory-Mapped I/O** is a technique where device control registers and I/O buffers are mapped into the same address space as normal RAM.  
- The CPU uses **regular load/store instructions** to communicate with devices, instead of special I/O instructions.  
- A portion of the physical address space is reserved for devices (not backed by RAM).  

---

#### How it works
- Device registers (status, control, data buffers) are mapped to specific memory addresses.  
- When the CPU **reads/writes** to those addresses:
  - The memory management unit (MMU) routes the access to the device instead of DRAM.  
  - The device interprets the operation (e.g., send data to NIC, configure GPU).  

---

#### Example
Suppose a network card has:
- **Status register** at `0xFFFF0000`
- **Data register** at `0xFFFF0004`

```c
#define NET_STATUS  (*(volatile unsigned int*)0xFFFF0000)
#define NET_DATA    (*(volatile unsigned int*)0xFFFF0004)

void send_char(char c) {
    while ((NET_STATUS & 0x1) == 0) {
        // wait until ready (polling)
    }
    NET_DATA = c;  // write char to NIC data register
}
```
- `volatile` ensures compiler doesn’t optimize away repeated loads/stores.
- Writing to `NET_DATA` sends data to the NIC hardware.

## Memory-Mapped I/O vs Port-Mapped I/O

| Feature             | Memory-Mapped I/O (MMIO)                             | Port-Mapped I/O (PMIO)                    |
| ------------------- | ---------------------------------------------------- | ----------------------------------------- |
| Addressing          | Uses normal memory addresses                         | Separate I/O address space                |
| Instructions        | Regular load/store (`MOV`, etc.)                     | Special I/O instructions (`IN`, `OUT`)    |
| Performance         | Often faster (can use caching, burst transfers, DMA) | Typically slower, limited instruction set |
| Hardware complexity | Needs MMU/Bus decoding                               | Simpler, separate bus                     |
#### Advantages

- Simpler programming model (devices look like memory).
- Can use CPU caching/pipelining optimizations.
- Uniform instruction set for memory and devices.
    
#### Disadvantages
- Consumes part of the physical address space.
- Risk of accidental access (a bug can overwrite device state).
- Need to carefully control caching (devices often marked as _uncacheable_).
    
---

#### Real-world examples
- GPUs and NICs exposing control registers via MMIO.
- Linux drivers use `ioremap()` to map device memory into kernel virtual memory.
- PCIe devices often use MMIO to expose configuration and data registers.