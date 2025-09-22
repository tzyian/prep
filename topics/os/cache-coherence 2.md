### Cache Coherence Scope

#### Within a single core
- Each CPU core typically has multiple levels of cache (L1, L2, maybe L3).
- Coherence between **L1 (per-core)** and **L2/L3 (shared)** is handled locally in the CPU pipeline/hierarchy.

---

#### Across cores in the same socket (node)
- Each core’s private caches may hold copies of the same memory line.
- Cache coherence protocols (MESI/MOESI, directory-based) ensure that:
  - Only one core has a **Modified** copy at a time.
  - Other cores see consistent data when they load from memory.
- Mechanism: **snoop bus** (older designs) or **directory in shared cache** (modern multi-core).

---

#### Across sockets (multi-socket NUMA system)
- Each CPU socket is a **node** with its own memory controller + local DRAM.
- Inter-socket links (Intel **QPI/UPI**, AMD **Infinity Fabric**, ARM CCIX/CXL) propagate coherence messages between nodes.
- Example: Core in Socket A writes to a cache line originally allocated in Socket B’s DRAM → coherence protocol ensures Socket B sees the updated data.

---

#### Across cluster/distributed systems
- Hardware cache coherence **does not extend across machines** (too expensive).
- Instead, distributed systems use **software-level consistency models** (e.g., release consistency, eventual consistency).

---

### So is cache coherence across the link and node?
✅ Yes:  
- Within a **node**: coherence is maintained among cores by hardware protocol.  
- Across **nodes (sockets)**: coherence traffic flows **over the interconnect link** (QPI/UPI/Infinity Fabric).  
- Beyond a physical system: **no hardware coherence**; software protocols (MPI, DSM, databases) handle consistency.

---

### Example
- 2-socket Intel Xeon system:
  - Each socket has multiple cores + local RAM.  
  - If Socket 0 modifies a line, the coherence protocol ensures Socket 1 invalidates or updates its copies.  
  - Traffic goes over the **UPI link** between sockets.  

---

### Diagram (simplified)
Core0 L1/L2 ──┐  
Core1 L1/L2 ──┤── Shared L3 ──┐  
Core2 L1/L2 ──┘ │  
▼  
Memory Controller  
│  
[ Interconnect Link ]  
│  
Another Socket (Node) with its own cores + memory

