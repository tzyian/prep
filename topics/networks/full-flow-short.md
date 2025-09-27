# Full Request Walkthrough (App, Transport, Network Layers)

## Application Layer — Browser Actions, DNS, Auth
1. User types `https://sub.example.tld/path` in browser.  
   - Browser checks its **HTTP cache** and **OS / browser DNS cache**.  
2. If no cached IP, browser asks OS resolver.  
   - OS resolver calls a **recursive DNS resolver** (from DHCP/OS config).  
3. Recursive resolver flow:  
   - Root server → TLD server → authoritative name server for `example.tld` → authoritative answer for `sub.example.tld`.  
   - May include CNAMEs, which the resolver follows.  
4. DNS transport:  
   - Typically **UDP datagram** to **port 53**.  
   - If answer too big or DNSSEC/TC bit set, uses TCP.  
   - Resolver returns IP address to OS and populates cache.

## Transport Layer — UDP and TCP
5. DNS query itself:  
   - Application data → UDP header (src port, dst port=53, length, checksum) → IP.  
   - UDP is connectionless; each query/response is independent.  
6. Browser opens **TCP socket** to server (dstPort=443).  
   - TCP **3-way handshake**:  
     - Client → SYN(seq=x)  
     - Server → SYN+ACK(seq=y, ack=x+1)  
     - Client → ACK(ack=y+1)  
   - Socket identified by 4-tuple (srcIP, srcPort, dstIP, dstPort).  
7. TCP segment structure:  
   - Seq, Ack, Flags, Window, Checksum, Payload.  
   - Each TCP segment is wrapped inside an IP datagram.

## Network Layer — IP Datagrams, Forwarding, Routing
8. Encapsulation order:  
   Application data → TCP/UDP segment → IP datagram → link-layer frame → physical bits.  
9. IP datagram fields:  
   - Source IP, Destination IP, TTL, Protocol, Header checksum, Payload.  
   - Used by routers for forwarding.  
10. Router forwarding:  
    - Input port receives frame.  
    - Extract IP datagram.  
    - Consult **forwarding table**.  
    - Decide outgoing interface.  
    - Enqueue packet into **output buffer**.  
    - If buffer full → packet drop.  
    - Fabric + buffers = internal router switch fabric + per-port queues.  
11. Routing decision rule:  
    - **Longest-prefix match** on destination IP.  
    - Control plane (OSPF, BGP) populates routing/forwarding tables.

## Path Through Networks
12. Each hop:  
    - Receive frame → input queue → switching fabric → output port queue.  
    - Router actions: decrement TTL, recompute checksum, longest-prefix match, forward/drop.  
13. Across subnets/AS:  
    - Inter-AS routing: BGP advertises reachability.  
    - Intra-AS routing: OSPF, RIP, IS-IS compute paths.  
    - Forwarding tables direct next hop.  
14. Destination host:  
    - NIC receives frame.  
    - Driver hands IP datagram to OS.  
    - OS matches destination IP.  
    - Payload delivered to transport layer.  
    - Transport demultiplexing by port (UDP) or 4-tuple (TCP).  
    - Application reads from socket.

## Failure Modes and Checks
- DNS over UDP may be lost → retry or fallback to TCP.  
- DNS answers cached at browser, OS, and resolver → often avoids root/TLD lookup.  
- TCP includes retransmission, ACKs, congestion and flow control.  
- Router buffer overflow → packet drops.  
- Queuing → increased delay.
