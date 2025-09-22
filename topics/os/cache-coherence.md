### Cache-Coherence Models

Across the interconnects and sockets

#### Background
In multiprocessor systems, each CPU core may have its own cache.  
**Cache coherence** ensures that all processors see a **consistent view of memory** when multiple caches store the same data.

---

### 1. Strict Consistency
- **Definition**: Any read to a memory location returns the most recent write, instantly visible to all processors.
- **Unrealistic** in real systems (would require infinite-speed propagation).

---

### 2. Sequential Consistency (SC)
- **Definition**: The result of execution is the same as if:
  - All operations of all processors were executed in a single sequential order.
  - Operations of each processor appear in program order.
- Easier to reason about, but may limit performance.
- Example: Many CPUs provide *sequential consistency* by default if memory fences are used.

---

### 3. Processor Consistency (PC)
- **Definition**: Writes from a single processor are seen by all in program order,  
  but writes from different processors may be observed in different orders by others.
- Weaker than sequential consistency but higher performance.

---

### 4. Weak Consistency
- **Definition**: Memory may appear inconsistent until a **synchronization point (e.g., lock/unlock, fence)** is reached.
- Synchronization acts as a barrier: all writes before must be visible before proceeding.
- Example: Many modern CPUs (ARM, POWER) use weak consistency to optimize performance.

---

### 5. Release Consistency (RC)
- **Definition**: A refinement of weak consistency:
  - **Acquire**: Before entering a critical section, ensures all following reads/writes see up-to-date values.
  - **Release**: Before leaving a critical section, ensures all previous writes are visible.
- Common in systems with explicit lock/unlock primitives.

---

### 6. Eventual Consistency (in distributed systems)
- In some distributed shared-memory or key-value stores, updates eventually propagate to all nodes.  
- Not typically used in hardware caches but relevant for **distributed cache coherence**.

---

### Protocols for Hardware Cache Coherence
- **MSI / MESI / MOESI protocols**: Hardware state machines used to keep caches coherent.
  - **M**odified – cache line is dirty and owned exclusively.
  - **E**xclusive – cache line matches memory, but no other cache has it.
  - **S**hared – cache line can be in multiple caches, clean.
  - **I**nvalid – cache line not valid.
  - (MOESI adds **O**wned state for optimization.)

---

### Comparison Table

| Model                 | Guarantees                           | Performance |
|------------------------|--------------------------------------|-------------|
| Strict Consistency     | Reads see latest write instantly     | Impractical |
| Sequential Consistency | All ops appear in total order        | Lower perf  |
| Processor Consistency  | Per-CPU order preserved              | Better perf |
| Weak Consistency       | Requires sync ops (fences)           | High perf   |
| Release Consistency    | Sync split into acquire/release      | Even better |
| Eventual Consistency   | Updates visible eventually           | Very high perf (distributed systems) |

