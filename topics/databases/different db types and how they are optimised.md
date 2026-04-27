| DB Type         | Schema Style             | Read Optimisation                         | Write Optimisation                    | Typical Use     |
| --------------- | ------------------------ | ----------------------------------------- | ------------------------------------- | --------------- |
| **OLTP**        | Normalised, row          | Indexes for point reads, ACID consistency | Fast single inserts/updates           | Banking, orders |
| **OLAP**        | Star/snowflake, columnar | Columnar scans, compression, pre-agg      | Bulk batch loads, partitioned inserts | BI dashboards   |
| **Analytical**  | Columnar + MPP           | Query pruning, vectorised execution       | Append-only batch ingest              | Data warehouse  |
| **Time-Series** | Time-bucketed            | Time-range scans, downsampling, tags      | Sequential appends, compression       | Monitoring, IoT |

Here’s a structured comparison:

---

## Types of Databases

### 1. OLTP (Online Transaction Processing)

- **Workload:** High volume of small read/write transactions. Examples: banking, e-commerce.

- **Optimised for:**
    - **Writes:** Fast inserts/updates with ACID guarantees. Normalised schema to avoid anomalies. Use of indexes for point lookups.
    - **Reads:** Point queries (`WHERE id=…`). Optimised by B-trees, clustered indexes (physical row order), row-oriented storage.

- **Tradeoff:** Not efficient for large scans/aggregations.

---

### 2. OLAP (Online Analytical Processing)

- **Workload:** Complex queries, aggregations, reporting. Examples: BI dashboards, analytics.

- **Optimised for:**
    - **Reads:** Columnar storage (Parquet, ORC). Compression, vectorised execution, bitmap indexes, star schemas for efficient scans.
    - **Writes:** Bulk loads are efficient; random updates are costly. Data is usually ingested in batches.

- **Tradeoff:** High-throughput reads at the expense of update latency.

---

### 3. Analytical Databases / Data Warehouses (Snowflake, Redshift, BigQuery)

- **Workload:** Large-scale analytics, often SQL over TB–PB of data.

- **Optimised for:**
    - **Reads:** MPP query execution, pruning, caching hot aggregates, columnar compressed storage.
    - **Writes:** Append-only batch loads; updates/deletes are slower and often rewritten as new partitions.

- **Tradeoff:** Great for BI/ML workloads, poor for OLTP-style workloads.

---

### 4. Time-Series Databases (InfluxDB, TimescaleDB, Prometheus)

- **Workload:** Data indexed by timestamp (IoT sensors, monitoring). Heavy sequential writes and time-range queries.

- **Optimised for:**
    - **Writes:** Fast sequential appends, compression (delta encoding, Gorilla), partitioning by time buckets.
    - **Reads:** Optimised for time-range scans, downsampling, retention policies, and aggregates (`avg` over intervals). Indexing on time + tags.

- **Tradeoff:** Limited support for complex joins or arbitrary queries.