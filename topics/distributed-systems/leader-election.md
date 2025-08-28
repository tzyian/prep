## Leader Election
- Process of **choosing a single coordinator node** in a distributed cluster.  
- Needed for tasks like log replication, lock management, scheduling.  
- Common algorithms:  
  - Bully algorithm (pick highest ID).  
  - Ring-based election.  
  - Consensus-based election (via Raft/Paxos).  