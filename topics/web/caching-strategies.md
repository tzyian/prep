# Question Card: Caching Strategies

- Question: Compare caching strategies (write-through, write-back, cache-aside). When to use which?
- Role: backend
- Difficulty: mid

## Short Answer
Cache-aside keeps source of truth in DB; app loads into cache on miss and invalidates/updates on write. Write-through writes to cache and DB synchronously. Write-back buffers writes in cache and flushes later. Use cache-aside for general app caching, write-through for simplicity/consistency, and write-back for high write throughput with acceptable risk.

## Deep Dive
- Eviction: LRU/LFU/TTL; stampede protection (locks/jittered TTL).
- Consistency: invalidate on change, version keys, or publish/subscribe.
- Topology: client‑side vs service‑side cache; CDNs for edge.

## Example
```text
Cache-aside read: GET k -> miss -> DB -> set k (TTL)
```

## Follow‑ups
- Negative caching and TTL selection.
- Hot keys, sharding, and replication.

## Pitfalls
- Thundering herd on popular expirations.
- Inconsistent invalidations leading to stale reads.

## Checklist
- Strategy choice · TTL/eviction · stampede protection · invalidation path

## Sources
- [[http-caching]] · [[consistency-models]]

