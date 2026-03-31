# Question Card: Caching Strategies

## Types

- Cache aside  (lazy load)
- Read through (cache reads db)
- Write through (write to both cache and db)
- Write behind (cache async write to db)
- Write around (write to db, only fetch cache)


- Core headers: `Cache-Control`, `ETag`/`If-None-Match`, `Last-Modified`/`If-Modified-Since`.
- Layers: browser cache, CDN/edge, reverse proxy, application cache.
- Strategies: freshness (max-age), revalidation, surrogate keys, cache busting.


## Eviction Strategies
- Least Recently Used (LRU)
- Least Frequently Used (LFU)
- Time to live (TTL)


## Sharding

## Invalidation Path
- 

## CDN Caching
- Consistency: invalidate on change, version keys, or publish/subscribe.

## Thundering Herd Problem
- Thundering herd on popular expirations.
- stampede protection (locks/jittered TTL).
