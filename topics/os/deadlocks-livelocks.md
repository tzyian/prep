
#### Deadlock
A **deadlock** occurs when two or more processes are waiting indefinitely for resources held by each other, so none of them can proceed.

**Necessary conditions (Coffman’s conditions):**
1. **Mutual Exclusion** – At least one resource cannot be shared.  
2. **Hold and Wait** – A process holding one resource can request additional resources. 
3. **No Preemption** – Resources cannot be forcibly taken away.  
4. **Circular Wait** – A circular chain of processes exists, where each process waits for a resource held by the next.  

**Example (Dining Philosophers Problem):**
- Each philosopher picks up one fork and waits for the other.  
- All are stuck waiting forever.  

---

#### Livelock
A **livelock** occurs when processes are not blocked (as in deadlock), but they keep changing state in response to each other and never make progress.  
- The system is active but not moving forward.  

**Example:**
- Two people trying to pass in a hallway: each steps aside repeatedly to let the other pass, but they keep mirroring each other’s moves indefinitely.  

---

#### Key Difference
| Feature         | Deadlock | Livelock |
|-----------------|----------|-----------|
| State           | Processes are blocked, doing nothing. | Processes are active but still not progressing. |
| Cause           | Circular wait for resources. | Over-coordination or repeated state changes without progress. |
| CPU Usage       | Low (waiting). | High (busy but useless work). |

---

#### Solutions
- **Deadlock prevention/avoidance:** Break one of Coffman’s conditions (e.g., allow preemption, impose ordering of resource requests, use Banker’s Algorithm).  
- **Livelock resolution:** Introduce randomness or back-off strategies so processes don’t endlessly mirror each other.  
