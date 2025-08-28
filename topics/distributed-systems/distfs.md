### How Distributed File Systems (DFS) Work

A **Distributed File System (DFS)** lets multiple machines share and access files as if they were on a single local disk.  
It hides the complexity of distribution (multiple nodes, replication, failures) behind a **global namespace**.

---

#### Key Goals
1. **Transparency**
   - **Location transparency**: Users see a single path (e.g., `/dfs/project/file.txt`) regardless of which machine stores it.  
   - **Replication transparency**: Users don’t know how many copies exist or where. 
   - **Concurrency transparency**: Multiple users can access files safely.
1. **Scalability**: Can store petabytes of data across thousands of machines.  
2. **Fault tolerance**: Survives hardware/network failures via replication and recovery.  
3. **Performance**: Balance between low-latency (small files) and high-throughput (large data sets).

---

#### Core Components
- **Metadata server (NameNode / master)**  
  - Stores *metadata*: file names, directory hierarchy, permissions, block locations.  
  - Does not store actual file content.  

- **Storage nodes (DataNodes / chunkservers / object servers)**  
  - Store actual file data in fixed-size **blocks/chunks** (e.g., 64MB or 128MB).  
  - Each block is usually **replicated** (e.g., 3 copies) across different machines/racks.

- **Client**  
  - Talks to the metadata server to resolve *where* data lives.  
  - Then communicates directly with storage nodes to read/write file contents.

---

#### How a File Read Works
1. Client requests `/dfs/data/file.txt` from the **metadata server**.  
2. Metadata server replies with block IDs + locations of replicas on storage nodes.  
3. Client connects directly to the chosen storage node(s) to read data.  
4. Data is streamed back to the client (often in parallel for different blocks).  

---

#### How a File Write Works
1. Client asks metadata server to create a new file.  
2. Metadata server allocates block IDs and selects target storage nodes (for replication).  
3. Client writes data → first storage node → pipelined to replicas.  
4. Acks flow back once all replicas confirm.  
5. Metadata server updates namespace and block mappings.

---

#### Fault Tolerance
- **Heartbeat messages**: Storage nodes periodically tell metadata server they’re alive.  
- If a node fails:
  - Metadata server detects missing heartbeat.  
  - Replicates the lost blocks to healthy nodes to maintain redundancy.  

---

#### Examples of Distributed File Systems
- **NFS** (classic networked FS, central server, less scalable).  
- **Google File System (GFS)** → inspired Hadoop HDFS.  
- **HDFS (Hadoop Distributed File System)** → big data ecosystem.  
- **CephFS** → POSIX-compliant, distributed metadata.  
- **GlusterFS** → scale-out, peer-to-peer architecture.  
- **Amazon S3** → object store, not POSIX but DFS-like semantics.  

---

#### Tradeoffs & Challenges
- **Consistency vs Availability** (CAP theorem):
  - Strong consistency (like NFS) vs eventual consistency (like S3).  
- **Small files problem**: Metadata bottleneck if millions of small files.  
- **Replication overhead**: More storage needed for durability.  
- **Network dependency**: Performance bound by bandwidth & latency.

---