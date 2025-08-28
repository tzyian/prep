# Alluxio — Overview

## What is Alluxio?
- **Alluxio** (formerly Tachyon) is an **open-source distributed virtual filesystem**.
- Acts as a **unified data access layer** sitting between **compute frameworks** (e.g., Spark, Presto, TensorFlow) and **storage systems** (e.g., S3, HDFS, GCS, NFS).
- Provides:
  - **Single namespace**: unify multiple backends into one view.
  - **Data locality**: cache frequently used data near compute.
  - **High throughput**: reduce repeated reads from remote object stores.
  - **Transparent access**: apps can read/write without knowing underlying storage.

---

## Architecture — Master & Worker Model

Alluxio follows a **master–worker architecture**.

### 1. Master Node
- **Role**: Manages **metadata** about files and directories (like a NameNode in HDFS).
- Responsibilities:
  - File system namespace (paths, directories).
  - Block metadata (which workers cache which blocks).
  - Coordinating client requests (where to read/write).
  - Enforcing policies (e.g., replication, eviction).
- **Stateless wrt data**: master does not store the actual file data, only metadata.

### 2. Worker Nodes
- **Role**: Manage **local storage resources** (RAM, SSD, HDD).
- Responsibilities:
  - Store and serve data blocks to clients.
  - Cache frequently accessed data blocks locally.
  - Report block metadata (what data they hold) back to the master.
- Workers **keep data close to compute**, reducing remote I/O.

---

## Why Master + Worker?
- **Metadata vs Data separation**:
  - Master → lightweight, central coordination point.
  - Workers → handle heavy I/O load and caching.
- **Scalability**:
  - Master tracks mappings, workers scale horizontally to handle throughput.
- **Fault tolerance**:
  - If a worker fails → only its cached data is lost (can be reloaded).
  - Master high-availability setups (with journal + standby masters).
- **Performance**:
  - Master only processes metadata; workers serve data directly to clients.

---

## How Clients Interact
1. Client contacts the **master** for metadata (where is file X?).
2. Master responds with worker locations holding the data blocks.
3. Client fetches data **directly from worker(s)**, bypassing the master for I/O.
4. On write, workers cache data locally, then asynchronously persist to underlying storage.

---

## Use Cases
- **Compute–storage disaggregation**: Cache remote object storage (S3/GCS) data close to compute clusters.
- **Multi-cloud & hybrid access**: Single namespace for multiple backends.
- **Data-intensive workloads**: Spark, Presto, TensorFlow training with faster data access.
- **Accelerating training pipelines**: Reduce repeated dataset downloads from S3/HDFS.

---

## Summary
- **Master** → Manages namespace and metadata (central brain).
- **Workers** → Cache and serve actual data (data muscle).
- Together: Alluxio improves **data locality, throughput, and unified access** across heterogeneous storage systems.
