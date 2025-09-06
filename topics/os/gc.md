| Aspect            | Rust                                                                                                                        | Go                                                                                     | Python (CPython)                                                                          |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Memory management | Ownership + borrowing at compile time. `Drop` runs deterministically. Optional `Rc/Arc` (refcount), `Weak` to break cycles. | Concurrent tri-color mark-and-sweep, non-generational, non-compacting. Write barriers. | Reference counting + cyclic GC (3 generations).                                           |
| Determinism       | Deterministic frees at end of scope.                                                                                        | Non-deterministic; objects reclaimed when GC runs.                                     | Mostly deterministic via refcount; non-deterministic for cycles.                          |
| Pause behavior    | None from GC.                                                                                                               | Very short stop-the-world pauses for phase transitions; marking runs concurrently.     | Short stop-the-world pauses when cycle collector runs; refcount decrements are immediate. |
| Fragmentation     | Low by design; allocator-dependent.                                                                                         | Possible since heap is non-compacting; mitigations exist.                              | Possible; many small objects and arenas.                                                  |
| Concurrency       | Zero global runtime; thread-safe via `Send/Sync`.                                                                           | GC is concurrent; scales with `GOMAXPROCS`.                                            | GIL limits true CPU parallelism of Python code; GC still runs.                            |
| Cycles            | Not collected unless you use `Weak` or custom logic.                                                                        | Collected by tracer.                                                                   | Collected by cycle GC; `__del__` on cyclic objects is tricky.                             |
| Finalizers        | `Drop` deterministic.                                                                                                       | `runtime.SetFinalizer` non-deterministic; avoid for critical frees.                    | `__del__` non-deterministic for cycles; prefer context managers.                          |
| Tuning knobs      | Choose allocators, use `Box`, `Vec`, arenas; avoid cycles with `Weak`.                                                      | `GOGC`, `debug.SetGCPercent`, `SetMemoryLimit`; reduce pointers; reuse buffers.        | `gc.set_threshold`, `gc.disable/enable`; reduce object churn; use arrays/NumPy.           |
| Typical overhead  | Minimal runtime overhead; more code complexity.                                                                             | Low pauses, extra RAM to keep GC fast; latency usually sub-ms to few ms at scale.      | Overhead per object is high; frequent small allocations hurt.                             |
## ## Tri-Color Mark-and-Sweep

### The three sets
- **White**: not yet discovered. If still white at sweep → freed.  
- **Gray**: discovered but not fully scanned.  
- **Black**: discovered and fully scanned.  

### Invariant
> No black object may point to a white object.  
This ensures all reachable objects eventually move out of white.

### Algorithm
1. **Initialization**  
   - All heap objects are white.  
   - Roots become gray.  
2. **Mark loop**  
   - While gray set is not empty:  
     - Pop a gray object.  
     - Scan its children.  
     - White children → paint gray.  
     - Current object → paint black.  
3. **Sweep**  
   - White objects → reclaimed.  
   - Black objects → kept (and reset to white for next cycle).  

### Conceptual flow
Roots → Gray → Black  
↘ discovers → White → Gray → Black


### Benefits
- Enables incremental and concurrent GC.  
- Shorter stop-the-world pauses.  
- Used in Go’s runtime (with write barriers).  
