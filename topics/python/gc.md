`gc.set_threshold()`.


1. refcounting
2. for cyclic objects, use cyclic gc





Generations for how many times the gc sees it
If a group is unreachable, but still reference inside the cycle, 


The `gc` module detects cycles between unreachable objects and breaks them.

Algorithm:

- Keeps _generations_ (0, 1, 2) for young→old objects.
    
- Tracks container types (`list`, `dict`, `class`, etc.) that can form cycles.
    
- Periodically scans generation 0, then 1, then 2 for unreachable groups.
    
- Unreachable but still referenced inside the cycle are collected.


### Mark and Sweep


When a generation’s threshold is exceeded:

1. **Mark phase**
    
    - Start with all tracked objects in that generation and younger ones.
        
    - Each object has an internal refcount `_gc_ref` copied from its `ob_refcnt`.
        
    - For every reference an object holds to another tracked object, decrement that target’s `_gc_ref`.
        
    - After this, any object whose `_gc_ref` > 0 is reachable from outside the generation (still referenced).
        
    - Those with `_gc_ref` == 0 are _unreachable candidates_.
        
2. **Sweep phase**
    
    - For each candidate with `_gc_ref == 0`, unlink it from containers and free it.
        
    - Survivors move to the next (older) generation.
        
    - The oldest generation (2) is scanned least frequently.
3. **Finalizers (`__del__`)**
    - If any unreachable objects have `__del__`, they are not destroyed automatically because destruction order is uncertain.
    - They are moved into `gc.garbage` for user inspection.




### 2. Why you don’t need to check _everything_

Each object’s _refcount_ already proves most reachability.  
The cyclic collector only targets _tracked containers_ whose refcounts never hit 0 because they reference each other.

The algorithm works like this:

1. **Snapshot phase**  
    For every object in the target generations, copy its `ob_refcnt` into a private counter `gc_ref`.
    
2. **Internal-edge subtraction**  
    For every object A → B reference inside those generations, decrement `B.gc_ref`.  
    After this, `gc_ref` ≈ number of references from _outside_ the set.
    
3. **Isolation test**  
    Any object with `gc_ref == 0` has **no external references**, meaning the only things keeping it alive are other unreachable objects in the same subgraph.
    
4. **Sweep**  
    Unlink and free all such objects.  
    Surviving objects (those with `gc_ref > 0`) are still reachable from active objects and are promoted to an older generation.