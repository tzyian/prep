# Gossip Protocols

## Question
What are gossip protocols and where are they used?

## Short Answer
- Lightweight, epidemic-style communication among nodes.  
- Each node periodically exchanges state with a random peer.  
- Properties:  
  - **Scalable** (low overhead, works in large clusters).  
  - **Robust** (tolerates failures, partitions).  
  - **Eventually consistent** (information spreads like a virus).  
- Uses:  
  - Cluster membership & failure detection (e.g., Cassandra, Consul, SWIM protocol).  
  - Disseminating configuration or metadata.  
  - Load balancing / health monitoring.  

## Deep Dive
- Epidemic anti‑entropy (push/pull), rumor mongering, TTL.
- Failure detection: SWIM‑style suspicion + indirect probes.
- Convergence speed vs bandwidth trade‑offs.

## Follow‑ups
- Membership in service meshes and databases.

