Single-threaded service

### Cache
- TTL
- 

cluster replication

### Top-k (leaderboard)
ZSET

### Tumbling Window rate limiter
1. EXPIRE ipaddr 60 (seconds)
2. every request, INCR ipaddr
3. If GET ipaddr = 60, then refuse the request
4. at 60 seconds, the key will expire and reset
(though this has problems with distributed o)
do also consider sliding window,, different kinds of 
 

### distributed locks
- REDLOCK algorithm for distributed locking
- also note: https://www.hellointerview.com/learn/system-design/deep-dives/redis#comment-cm6vzbqen0108kuuzr32cix5n
- https://redis.io/docs/latest/develop/clients/patterns/distributed-locks/


### bloom filter
### pub sub
SET


ZSET (sorted set)
- top-k leaderboards