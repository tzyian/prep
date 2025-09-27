![[Pasted image 20250926025736.png]]
1. Application payload, wrapped by TCP segment by OS, wrapped by IP datagram in OS
2. Wrapped to Link frame by NIC
3. Link layer WIFI/Ethernet switches via MAC
4. Within the subnet to Network Layer router
5. Router unwraps Link frame to IP datagram
6. In-router switching fabric move packet from input port to output port
7. Router buffer if necessary, drop packets if necessary, use scheduling algos (RR, PQ, weighted RR)
8. Router finds next destination in routing table
9. Router wraps to Link Frame
10. Go through multiple subnet/router until dest
11. Reverse



```mermaid
sequenceDiagram
autonumber
actor App as App
participant Stub as OS Stub Resolver
participant Cache as OS DNS Cache
participant Rec as Recursive Resolver
participant Root as Root
participant TLD as TLD (.com)
participant Auth as Authoritative NS

App->>Stub: Query A www.example.com
Stub->>Cache: Lookup
alt Cache hit
  Cache-->>Stub: Answer
  Stub-->>App: Answer
else Cache miss
  Stub->>Rec: Forward query (UDP/53)
  Rec->>Rec: Check cache
  alt Rec cache hit
    Rec-->>Stub: Answer
    Stub-->>App: Answer
  else Rec cache miss
    Rec->>Root: Iterative query
    Root-->>Rec: Referral to TLD
    Rec->>TLD: Query NS for example.com
    TLD-->>Rec: Referral to Authoritative
    Rec->>Auth: Query A www.example.com
    Auth-->>Rec: Final answer + TTL
    Rec-->>Stub: Answer
    Stub-->>App: Answer
  end
end

note over Rec,Auth: May switch to TCP/53 if response is truncated.\nDNSSEC adds RRSIG/DS validation steps.

opt DoH/DoT path
  Stub->>Rec: DoH (HTTPS/443) or DoT (TLS/853)
end
```