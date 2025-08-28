
[https://www.cl.cam.ac.uk/teaching/2021/ConcDisSys/dist-sys-notes.pdf](https://www.cl.cam.ac.uk/teaching/2021/ConcDisSys/dist-sys-notes.pdf)

[https://www.youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB](https://www.youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB)

  

Two Generals Problem: communication is not guaranteed

Byzantine Generals Problem: given >= 1 malicious entity

  

Network Behaviour

Reliable Links (reordered)

↑ deduping, resending

Fair Loss Links (can be lost, duped, reordered)

↑ (TLS)

Arbitrary (active adversary)

  

Synchrony Assumptions

- Sync (message latency known, finite, nodes are finite)
    
- Partially sync (asynchronous for finite but unknown time)
    
- Async (arbitrary msg delay or node pause, no timing guarantees)
    

Logical Time

Every event increments a node’s logical time

- Lamport Time: every node sends/receives time
    
- Vector Time: every node has a vector of known times for each node, update on sending msg. Merge if no conflicts, else decide which to take
    

  

Broadcast Order (in increasing strictness)

Using → to mean relational order i.e.

- FIFO
    

- If m1, m2 broadcast by same node, and b(m1) → b(m2), delivery is FIFO
    

- Causal
    

- If b(m1) → b(m2), then m1 delivered before m2, regardless of who sent m1 or m2
    
- Concurrent messages are any order
    

- Total order (atomic broadcast)
    

- All nodes deliver msgs in the same order
    

- FIFO total-order
    

- FIFO + total order
    

  

Broadcast Algorithms

- Gossip protocol
    

  

Replication

- Quorum
    

  

Consensus

- Raft
    
- Paxos
    

  

Replica Consistency

- 2 Phase Commit
    
- Linearizability
    
- Eventual Consistency