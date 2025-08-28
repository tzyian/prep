### Lock-Free vs Wait-Free

#### Lock-Free
- **Definition**: A concurrent algorithm is **lock-free** if, in the presence of multiple threads, **at least one thread** makes progress in a finite number of steps (system as a whole moves forward).
- Individual threads may **starve** (get delayed indefinitely), but global progress is guaranteed.
- Typically built with atomic primitives like **CAS (compare-and-swap)** or **FAA (fetch-and-add)**.

**Example**:
- Lock-free stack using CAS to push/pop nodes.
- If two threads collide, one succeeds (makes progress), the other retries.

---

#### Wait-Free
- **Definition**: A concurrent algorithm is **wait-free** if **every thread** completes its operation in a finite number of steps, regardless of scheduling or interference.
- Guarantees **per-thread progress**, not just system-wide.
- Stronger guarantee than lock-free, harder to implement.

**Example**:
- A wait-free queue ensures that *all* enqueue/dequeue calls finish in bounded time, even under contention.

---

#### Comparison

| Aspect          | **Lock-Free**                              | **Wait-Free**                          |
|-----------------|---------------------------------------------|-----------------------------------------|
| Progress        | At least one thread always progresses       | Every thread always progresses          |
| Starvation      | Possible (one thread may retry forever)     | Impossible (all threads finish)         |
| Complexity      | Easier to design (e.g., CAS retry loops)    | Harder, often needs advanced techniques |
| Use cases       | Common in practice (e.g., concurrent stacks, queues) | Rare, mostly in real-time systems where bounded latency is critical |

---

#### Analogy
- **Lock-Free**: At least someone gets through the doorway each time (others may be pushed back and try again).  
- **Wait-Free**: Everyone gets through the doorway in a guaranteed number of steps, no matter what.  
