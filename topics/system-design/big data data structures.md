https://highscalability.com/big-data-counting-how-to-count-a-billion-distinct-objects-us/

HelloInterview:
https://www.youtube.com/watch?v=IgyU0iFIoqM
https://link.excalidraw.com/l/56zGeHiLyKZ/7KgpbORPEdl
https://www.hellointerview.com/learn/system-design/deep-dives/data-structures-for-big-data

## Bloom filter
- If all hash functions get positive, then result is positive
- Probabilistic data structure
- Params: 
	- number of hash functions
	- number of bitsets to check
- No false negatives
- Elements cannot be deleted in the base form

Uses:
- used to check SSTables in nosql LSM Trees to see if an element exists
- in-memory cache
- malicious URL detection (fast checking) before network checking

Variants:
- Counting Bloom Filters

Implementation
- Redis

## Count Min Sketch

## Linear Probabilistic Counter

## HyperLogLog
