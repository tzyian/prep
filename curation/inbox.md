Distributed Systems (Kleppmann)
https://www.cl.cam.ac.uk/teaching/2021/ConcDisSys/dist-sys-notes.pdf
https://www.youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB

Two Generals Problem: communication is not guaranteed
Byzantine Generals Problem: given >= 1 malicious entity

Network Behaviour
Reliable Links (reordered)
↑ deduping, resending
Fair Loss Links (can be lost, duped, reordered)
↑ (TLS)
Arbitrary (active adversary)

Synchrony Assumptions
Sync (message latency known, finite, nodes are finite)
Partially sync (asynchronous for finite but unknown time)
Async (arbitrary msg delay or node pause, no timing guarantees)
Logical Time
Every event increments a node’s logical time
Lamport Time: every node sends/receives time
Vector Time: every node has a vector of known times for each node, update on sending msg. Merge if no conflicts, else decide which to take

Broadcast Order (in increasing strictness)
Using → to mean relational order i.e.
FIFO
If m1, m2 broadcast by same node, and b(m1) → b(m2), delivery is FIFO
Causal
If b(m1) → b(m2), then m1 delivered before m2, regardless of who sent m1 or m2
Concurrent messages are any order
Total order (atomic broadcast)
All nodes deliver msgs in the same order
FIFO total-order
FIFO + total order

Broadcast Algorithms
Gossip protocol

Replication
Quorum

Consensus
Raft
Paxos

Replica Consistency
2 Phase Commit
Linearizability
Eventual Consistency

Web Security
What is Oauth2?

How browser encrypts traffic?

TLS Handshake










At least Once
2 Generals/ Byzantine Generals Problem
Lamport Time, Vector Time
RAFT algorithm, Paxos algorithm
Cache coherence models
Consistency model
ACID
CAP
Gossip Protocols
Broadcast Protocol
2 Phase Commit
2 Phase Locking
How cdn work



Web

caching, content distribution, processes & threads & cores, etc

Consistency models
Cache coherence

https://github.com/bregman-arie/devops-exercises/tree/master?tab=readme-ov-file

False sharing





CAP consistency vs ACID consistency



https://www.ardanlabs.com/blog/2018/12/scheduling-in-go-part3.html


cs2100 computer organisation
iee754 decimal
storage architectures
endianness
page addressing, byte addressing
pipelining
pipeline hazards (structural, data, forwarding, control hazards, branch resolution, branch prediction)
cache locality
block size
direct mapped cache
cache misses (compulsory, conflict miss)
cache type: (write-through, write-back)

cs2102 database
ACID

make a sql cheatsheet with the whole create, alter table, column, type, default, drop, numeric, drop constraint,, alter constraint, delete table, drop cascade, update values
sql cheatsheet with common commands, include CTE, views, triggers, deferred triggers, functions, select distinct, conditionals, nullif group by, aggregate, coalesce, check, deferred constraints, 
inner, outer join, 
common types of nested sql questions 
FK, PK, include syntax
BCNF, 3NF


cs2109 classical AI
hill climbing, A*, heuristics, minimax

cs2103 swe
how to ensure exhaustive tests eg equivalence partitions, boundary value, 

cs2106 operating systems
VM, microkernel, container, processes, pages, deadlocks, syscalls, traps, exceptions, interrupts, scheduling algorithms (round robin, fcfs, priority scheduling, MLFQ)
multithreading, user threads, system threads, SIMD, shared memory, Send and receive
mutex, semaphore, condvars
producer-consumer, reader-writer, barriers
virtual memory, paging schemes (demand paging, direct paging, 2 level paging, inverted page tables)
file descriptors, vtable, file ttable, vnodetable

CS3223 Database Implementation, MVCC, ARIES, 2PL, query optimisation
ACID thm
DB tuning
B+ trees, static hashing, linear hashing, extendible hasihing, page replacement policies
external merge sort
selection (table scan, index scan, selectivity)
indexes
sorting with blocked IO
projection (sort-based)
join (hash join, nested loop, block,index nested loops,tuple,page loops)
grace hash join
query evaluation, aggregation, group-by, materialisation, pipelining, partial materialisation,
query plans
query optimisation
Transactions (view equiv, view serialisaible, conflict equiv, conflict serialisable), anomalies (dirty read, unrepeateable read, lost update, phantom read)
concurrency control (2PL, deadlocks, deadlock prevention )
intention shared locks, intention exclusive locks
MVCC, mV view equiv, MV view serialisable
snapshot isolation
serialisable snapshot isolation

CS4248
HMM
POMDP
tokenisation
Viterbi
Attention
perplexity
complexity
MHA, cross attention, self attention, various types of attention
attention mathematics and theory (QKV)
PEFT, LSTM, ROPE, LORA
encoder decoder
CFG, different types of grammars
bayes
softmax
confusion matrix
encoder-decoder models
llama
MOE
MCP
COT
superposition
diffusion
VLLMs
open world

CS4243
YOLO, ViT, Dino
Gabor filters
tracking
optical flow


CS4225 Big Data
spark internals
hadoop
flink
alluxio internals

CS3210 Parallel and Concurrent Programming, coherence, consistency, cuda, openmp,mpi, performance, monitoring, architecture, distributed systems
pipelining, superscalar processing, task mapping and scheduling
IPC
exceptions, interrupts, threads (uyser, kernel-level)
time slicing, context switching
synchronisation
- atomic operations, locks, mutexes, sempahores, test and set, condvars, barriers
deadlocks (mutex, hold and wait, preemption, circular wait), what they are, how caused, how prevented 
livelocks, what they are, how they are caused, how to be prevented
starvation
spinlocks
Instruction level parallelism, data parallelism, single program multiple data, task parallelism
pipelining
superscalar pipelining
SIMD, SMT, MISD, SISD, SIMD, MISD, MIMD
distributed memory systems, shared memory systems
cache coherence (uma, numa, multicore numa, cache coherent numa)
patterns (fork-join, parbegin parend, spmd, mimd, master-worker, task-pool ,producer consumer pipelining)
amdahl's law

cache locality, contention, spatial locality, false sharing. prefetching

GPU architecture, streaming multiprocessors, types of caches, mapping, memory access patterns, strided access, memory banks, 

write through, write back policies
cache tracking (snooping, directory-based)
memory-consistency (sequential, TSO, weak ordering)


CS3219 
microservices, event storming, CQRS, event sourcing, sagas
K8s
NGINX, ingress, egress, reverse proxy, API gateway, service registry, partitioning, sharding
kafka internals 
pubsub
rabbitmq




CS2105  Networking

CS2107 Security



atomic vars, test-and-set, compare-and-swap
epoll (i know its sth about io but i cant rmb if i even learnt this)
pipes and redirects
mutexes semaphores condvars (i cant rmb jack abt condvars)

Distributed
CAP thm
Paxos, zookeeper, etcd, raft, 

AI
devops, grafana, kubernetes, docker, loki, prometheus, argo, flux, helm, kustomize,

CS2107 Security
How oauth2, oauth2 pkce works


Goroutines sockets websockets
He asked abt security and symm/asymm cryptography



2 phase locking (i cant rmb shit)
database indexes
write ahead logs
2 phase commits (it turns out i never learnt this)
cache coherence
false sharing
C++ Memory model (idk what this is)
paging
race conditions
message queues
and of course it ends in implement lru cache

Write a few paragraph on lang choice, mq choice, architecture choice, on everything i have esp 3219

Oauth oidc pkce


Fast io


Io uring



Websocket security

Pk encryption


also only tangentially related but one really interesting thing to look up is structured concurrency https://vorpus.org/blog/notes-on-structured-concurrency-or-go-statement-considered-harmful/, which is implemented in Python most famously in trio (but sadly it has limited traction overall so I just use asyncio for convenience)



Acid properties matter
https://interviewprep.org/computer-science-interview-questions/



Go recap how db pk and cascade work

Websockets do run thru https, so they can be secured in the same way i think

Debouncing





