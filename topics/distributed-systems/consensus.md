
## Consensus vs Coordination
- **Consensus**:  
  - Agreement among distributed nodes on a single value (e.g., "which log entry is committed").  
  - Requires handling faults, message delays, and partitions.  
  - Used in leader election, state machine replication, transaction commits.  

- **Coordination**:  
  - Broader: ensuring nodes cooperate correctly (scheduling, locking, resource allocation).  
  - Consensus is one *form* of coordination, but coordination also includes barriers, synchronization, etc.  
