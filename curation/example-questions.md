## 1. Programming Language Internals
- What is a virtual method table (vtable)?
- What’s the difference between `==` and `.equals()` in Java?
- Explain the difference between stack and heap memory.
- How does garbage collection work in Java / .NET?
- What’s a memory leak, and how can it occur in managed languages?
- What is tail call optimisation?
- How are default arguments implemented in C++?
- What are covariant and contravariant return types?
- What is type erasure?

## 2. Time & Space Complexity
- What is the time complexity of binary search?
- How do hash tables achieve average constant-time lookup?
- What is the Big-O of accessing an element in a linked list?
- What's the best/worst case for quicksort?
- Why is merge sort stable?
- Compare time and space complexity of quicksort vs heapsort.
- What is amortised analysis?
- What's the Master Theorem?

## 3. Data Structures
- What data structure would you use for an LRU cache?
- Explain how a trie works and its space-time trade-offs.
- Why is a B-tree preferred over a binary search tree in databases?
- What’s the difference between a min-heap and a binary search tree?
- What’s a skip list and when is it useful?
- Describe a disjoint set (union-find) and its applications.
- How do you implement a circular buffer?
- What is a Fenwick tree?

## 4. Algorithms & Properties
- How does Dijkstra’s algorithm work?
- Explain the difference between DFS and BFS.
- What’s the optimal time complexity of comparison-based sorting algorithms? Why?
- How does KMP string matching work?
- What is a greedy algorithm? When does it fail?
- Explain dynamic programming and memoisation.
- How does backtracking differ from recursion?
- What are the invariants of quicksort?
- Describe a topological sort and its applications.
- What is a strongly connected component?

## 5. Operating Systems
- What is a race condition?
- What’s the difference between a process and a thread?
- What is virtual memory?
- Explain context switching.
- What are system calls and how are they invoked?
- What is demand paging?
- What’s the difference between preemptive and cooperative multitasking?
- What is a kernel mode trap?

## 6. Networking
- What’s the difference between TCP and UDP?
- Explain the 3-way handshake in TCP.
- What is ARP and why is it needed?
- What is the purpose of a subnet mask?
- How does DNS resolution work?
- What is NAT?
- What’s the difference between HTTP/1.1 and HTTP/2?
- What is a SYN flood attack?

## 7. Databases
- What is the difference between clustered and non-clustered indexes?
- What does ACID mean in the context of transactions?
- What’s the difference between an inner join and a left join?
- What are normal forms in relational databases?
- What is MVCC?
- How are transactions isolated in PostgreSQL?
- What is a materialised view?
- What’s the difference between OLTP and OLAP?

## 8. Systems Design (Lightweight Trivia)
- What is a load balancer?
- What is eventual consistency?
- What is a CDN and how does it improve performance?
- What’s the CAP theorem?
- What is sharding?
- What is replication lag?
- What is the difference between horizontal and vertical scaling?
- What are idempotent APIs?

## 9. Compilation & Interpreters
- What’s the difference between a compiler and an interpreter?
- Explain the steps of lexical analysis.
- What is just-in-time compilation?
- What is an abstract syntax tree?
- What is register allocation?
- What is loop unrolling?
- What is a peephole optimisation?

## 10. Security
- What is SQL injection?
- What’s the difference between symmetric and asymmetric encryption?
- What is a CSRF attack, and how is it mitigated?
- How does HTTPS ensure secure communication?
- What is XSS?
- What is HSTS?
- How does a digital signature work?
- What is a hash collision?

## 11. Concurrency
- What is a deadlock? How can it be prevented?
- What are the four conditions for deadlock?
- What is the difference between mutex and semaphore?
- Explain optimistic vs pessimistic locking.
- - What is the ABA problem?
- What is a memory barrier?
- What is the difference between atomicity and isolation?

## 12. Memory & Computer Architecture
- What is endianness?
- What’s the difference between instruction-level parallelism and thread-level parallelism?
- Explain the function of CPU caches.
- What is a page fault?
- What is cache coherence?
- What are TLBs?
- What is memory-mapped I/O?







## Concurrency and Parallelism
- What is the difference between concurrency and parallelism?
- What are the invariants of concurrent queue algorithms?
- What is the difference between a lock and a spinlock?
- What is a barrier?
- What is a thread pool?
- What are lock-free and wait-free algorithms?
- What is false sharing?
- What is Amdahl’s Law?
## Concurrency and Parallelism (Advanced)
- What is a compare-and-swap (CAS) operation?
- What is the role of memory fences?
- Explain the Dining Philosophers problem and its solutions.
- How do lock-free stacks work?
- What is a thread-safe data structure?
- How is starvation different from deadlock?
- What are the advantages of actor-based concurrency models?
- What are atomic operations and how are they implemented?
## Operating Systems (Advanced)
- What is a scheduler and how does it select processes?
- What is a syscall and how is it invoked in x86_64?
- What is the difference between user space and kernel space?
- What is memory-mapped file I/O?
- What is a zombie process?
- What is NUMA and how does it affect memory allocation?
- What are loadable kernel modules?
- What is a watchdog timer?
## Operating Systems (More Advanced)
- How do Linux control groups (cgroups) work?
- What is the role of namespaces in containerisation?
- What is IO scheduling?
- What are real-time operating systems and how are they different?
- How does process priority affect scheduling?
- What is the role of init and systemd?
- What is ASLR and how does it protect processes?
- How does a modern OS handle syscall dispatching?
## Networking (Advanced)
- What is a TCP sliding window?
- What is MTU and how does fragmentation occur?
- What is the difference between HTTP/2 and HTTP/3 (QUIC)?
- How does TLS perform a handshake?
- What is BGP and how does it work?
- What is ECN in TCP?
- What are the differences between IPv4 and IPv6 at the protocol level?
- What is packet sniffing and how is it prevented?
## Networking (Even Deeper)
- What is a three-way handshake timeout and retransmission policy?
- What is the difference between flow control and congestion control?
- What is TCP slow start and how does it prevent congestion?
- What are SYN cookies and when are they used?
- What is TCP fast open?
- What is a CDN edge node?
- How do proxies modify HTTP headers?
- What is connection reuse in HTTP/1.1?

## Distributed Systems
- What is the difference between consensus and coordination?
- What is the FLP impossibility result?
- What is Raft and how does it compare to Paxos?
- What is leader election?
- What is quorum in distributed databases?
- What is a vector clock?
- What is the purpose of gossip protocols?
- What are consistency levels in Cassandra?

## Distributed Systems (Even Deeper)
- What is Byzantine fault tolerance?
- What are the trade-offs in eventual consistency?
- What is a split-brain scenario?
- How does Zookeeper ensure consistency?
- What is a WAL (Write-Ahead Log)?
- What is log compaction in Kafka?
- What is two-phase commit and what are its failure scenarios?
- What is anti-entropy in distributed databases?

## Web Internals
- What happens when you type a URL and press enter?
- What is the purpose of the browser rendering engine?
- What is the difference between DOMContentLoaded and `load` events?
- What is CORS and how does it work?
- What is the Same-Origin Policy?
- What is HTTP pipelining?
- What is the critical rendering path?
- How do service workers enable offline access?

## Web Internals (Advanced)
- How does prefetching improve performance?
- What is a shadow DOM?
- How does HTTP caching work (ETag, Last-Modified)?
- What are web sockets and how do they maintain stateful communication?
- What is the difference between synchronous and asynchronous scripts?
- What is a CSP (Content Security Policy)?
- How do browser event loops differ across JS engines?
- How does lazy loading of images work?

## Cryptography & Security (Beyond Web)
- What is the difference between symmetric and asymmetric encryption?
- What is perfect forward secrecy?
- How does the Diffie-Hellman key exchange work?
- What is elliptic curve cryptography and why is it used?
- What are digital certificates and how are they validated?
- What is a MAC and how does it differ from a digital signature?
- What is a cryptographic nonce?
- How do hash collisions compromise integrity?

## Storage Systems & Filesystems
- What is journaling in file systems?
- How does ext4 ensure data integrity?
- What are the trade-offs between RAID 0, 1, 5, 6, 10?
- What is the difference between block storage and object storage?
- How do SSDs manage write amplification?
- What is wear leveling in NAND flash?
- How does the TRIM command improve SSD performance?

## Performance & Optimisation
- What is false sharing and how does it affect multithreading performance?
- How does cache locality improve performance?
- What is tail latency and why is it important?
- What are flame graphs and how are they used in profiling?
- When is a program CPU-bound vs I/O-bound?
- What is branch prediction and how does it affect performance?
- How does vectorisation improve loop performance?


## Database Internals
- What is a B+ tree and why is it preferred in databases?
- How does a database implement a transaction log (WAL)?
- What is MVCC (Multi-Version Concurrency Control)?
- What are heap-organised tables vs index-organised tables?
- What is the role of a buffer pool?
- What is page splitting and how does it affect performance?
- How does query planning and optimisation work?
- What are bitmap indexes and when are they effective?
- How do LSM trees work and where are they used?
- What is a bloom filter and how is it used in databases?
- How does PostgreSQL implement vacuuming?
- What are undo logs and redo logs?
- What is checkpointing in a database?
- What is write amplification in LSM-based storage engines?
- What is tuple visibility in PostgreSQL?

## Backend Engineering
- What is the N+1 query problem?
- How do you ensure idempotency in APIs?
- What is eventual consistency vs strong consistency?
- What is connection pooling?
- How does rate limiting work?
- What are the pros/cons of REST vs gRPC?
- How does dependency injection work in backend frameworks?
- What’s the difference between monoliths and microservices?

## Backend Engineering (Advanced)
- What is the Circuit Breaker pattern?
- What is the Saga pattern in microservices?
- How does backpressure work in event-driven systems?
- What is eventual vs strong vs causal consistency?
- How do you detect and resolve race conditions in web servers?
- What are the trade-offs of synchronous vs asynchronous communication between services?
- What is connection multiplexing in HTTP/2?
## DevOps
- What is Infrastructure as Code?
- How does Docker achieve isolation?
- What is the difference between Docker and a virtual machine?
- What is Kubernetes and how does it manage containers?
- What is a service mesh?
- What is the difference between blue-green and canary deployment?
- What is CI/CD?
- What is configuration drift?

## DevOps (Advanced)
- What are sidecars in Kubernetes?
- What is the role of etcd in Kubernetes?
- What is an init container?
- How does GitOps differ from traditional CI/CD?
- What is a rolling update in Kubernetes?
- What is the difference between StatefulSet and Deployment?
- How does Helm work?
- What is a reverse proxy and where is it used?


## System Design
- What is the difference between horizontal and vertical scaling?
- What is a load balancer and what algorithms does it use (e.g., round-robin, least connections)?
- What is a service registry and discovery mechanism?
- What is eventual consistency and how is it achieved?
- What is the CAP theorem and what are its implications?
- What are the trade-offs between monolithic and microservice architectures?
- What is sharding and how is it implemented?
- What is replication and what are the different types (master-slave, multi-master)?
- How does a message queue ensure at-least-once or exactly-once delivery?
- What are the different caching strategies (write-through, write-behind, cache-aside)?
- What is a circuit breaker pattern and why is it useful?
- What is backpressure and how do systems handle it?
- What is the role of API gateways?
- What are idempotent operations and why are they important?
- What is fan-out and fan-in in distributed task processing?
- How do you ensure data consistency in distributed transactions?
- What are distributed locks and how can they be implemented (e.g., using Redis, Zookeeper)?
- How do you monitor and observe a large-scale distributed system (logs, metrics, tracing)?
- What is a bulkhead pattern in service isolation?

## Observability & Monitoring
- What are the pillars of observability?
- What is the difference between metrics, logs, and traces?
- What is structured logging?
- How does distributed tracing work (e.g., OpenTelemetry)?
- What is a histogram vs summary metric in Prometheus?
- What is an SLO and how is it related to SLAs?

## Containerisation & Orchestration
- What is the difference between Docker image layers?
- How does a Kubernetes pod differ from a container?
- What is a DaemonSet?
- What is a sidecar container?
- How does Kubernetes handle health checks?
- What is an operator in Kubernetes?

## Infrastructure
- What is a VPC and how does it isolate traffic?
- What is a subnet and how does routing work within it?
- What is a NAT gateway and when is it used?
- How does DNS failover work?
- What is a bastion host?
- What is IaC (Infrastructure as Code) and how does Terraform implement it?

## Caching
- What is cache invalidation and why is it hard?
- What is a distributed cache and what are its challenges?
- What is a write-through cache?
- What is a cache stampede and how is it mitigated?
- How does Redis handle eviction policies?

## CI/CD & Build Systems
- What is a build pipeline and what are its stages?
- What is the difference between static and dynamic analysis in CI?
- What is the purpose of a staging environment?
- What is a canary release?
- How does rollback work in deployment strategies?


## Data Engineering

### Data Modeling & Warehousing
- What is a star schema vs a snowflake schema?
- What is a slowly changing dimension (SCD)?
- What is denormalisation and when is it used in data warehouses?
- What are fact and dimension tables?
- What are surrogate keys and why are they used?
- What is a grain of a fact table?

### ETL / ELT
- What is the difference between ETL and ELT?
- What is an idempotent ETL job?
- What is data lineage and why is it important?
- How do you ensure data quality in pipelines?
- What are common failure modes in ETL pipelines?
- How do you handle schema evolution?

### Data Pipelines & Orchestration
- What is DAG scheduling in Apache Airflow?
- What are task dependencies and retries in Airflow?
- How do event-driven pipelines differ from batch pipelines?
- What is backfilling and how is it done safely?
- What is a sensor in Airflow?

### Big Data Systems
- What is the difference between Hadoop and Spark?
- How does Spark manage fault tolerance?
- What is a shuffle operation in Spark?
- What is a partition and how does it affect performance?
- What is data skew and how do you mitigate it?

### Data Lakes & Lakehouses
- What is a data lake vs data warehouse?
- What is a Delta Lake / Hudi / Iceberg table?
- What is ACID compliance in modern lakehouses?
- What is schema-on-read vs schema-on-write?

### Query Engines & Performance
- How do columnar formats like Parquet improve performance?
- What is predicate pushdown?
- How do materialised views help performance?
- What is a query execution plan?
- How does indexing work in columnar stores?

### Streaming Systems
- What is the difference between stream and microbatch processing?
- How does checkpointing work in Spark Streaming or Flink?
- What are watermarks and event-time vs processing-time?
- What is exactly-once semantics?
- How does Kafka ensure message ordering?
