# Networking Questions & Answers (Interview Prep)

---

## Multiplexing

Two contexts:

1. **Transport-layer multiplexing**
    
    - Many connections share the same IP address + NIC.
        
    - Each TCP connection is identified by a 4-tuple `(src IP, src port, dst IP, dst port)`.
        
    - This lets one host talk to thousands of different servers, and one server accept thousands of clients on the same port.
        
2. **I/O multiplexing**
    
    - One process can manage many sockets without blocking on one.
        
    - Syscalls: `select()`, `poll()`, `epoll_wait()` (Linux), `kqueue()` (BSD).
        
    - Basis of event-driven servers (e.g., Nginx, Node.js)

## Basics

### 1. What is a network?
A network is a collection of devices connected together to share resources and exchange data.

### 2. What is the difference between LAN, WAN, and MAN?
- **LAN**: Local Area Network, small area (e.g., office, home).  
- **MAN**: Metropolitan Area Network, covers a city.  
- **WAN**: Wide Area Network, spans countries (e.g., Internet).  

### 3. What is the difference between TCP and UDP?
- **TCP**: Reliable, ordered, connection-oriented.  
- **UDP**: Fast, unreliable, connectionless.  

### 4. What is an IP address?
A unique identifier for a device in a network. Can be **IPv4 (32-bit)** or **IPv6 (128-bit)**.

### 5. What is a MAC address?
A unique hardware identifier (48-bit) assigned to a network interface card (NIC).

---

## OSI & Models

### 6. What are the OSI 7 layers?
1. Physical  
2. Data Link  
3. Network  
4. Transport  
5. Session  
6. Presentation  
7. Application  

### 7. OSI vs TCP/IP model?
- **OSI**: 7 layers, theoretical.  
- **TCP/IP**: 4 layers (Link, Internet, Transport, Application), practical.  

### 8. At which layer does IP work?
- **Network layer** (Layer 3).

### 9. At which layer does TCP work?
- **Transport layer** (Layer 4).

### 10. At which layer does HTTP work?
- **Application layer** (Layer 7).

---

## Protocols

### 11. What is DNS?
Domain Name System, resolves domain names (example.com) to IP addresses.

### 12. What is DHCP?
Dynamic Host Configuration Protocol, automatically assigns IP addresses and configuration to hosts.

### 13. What is ARP?
Address Resolution Protocol, maps IP addresses to MAC addresses.

### 14. What is ICMP?
Internet Control Message Protocol, used for diagnostics (e.g., `ping`, `traceroute`).

### 15. What is NAT?
Network Address Translation, maps private IP addresses to a public IP address.

---

## Devices

### 16. Difference between hub, switch, router?
- **Hub**: Broadcasts data to all devices.  
- **Switch**: Forwards data to correct MAC address.  
- **Router**: Forwards data between networks (based on IP).  

### 17. What is a firewall?
A network security system that filters traffic based on rules.

### 18. What is a load balancer?
Distributes network traffic across multiple servers to improve performance and availability.

### 19. What is a proxy server?
An intermediary that forwards client requests to servers, often used for caching and anonymity.

### 20. What is a CDN?
Content Delivery Network, caches content at edge servers closer to users.

---

## TCP/IP Details

### 21. What is the TCP 3-way handshake?
1. SYN → Client to server  
2. SYN-ACK → Server to client  
3. ACK → Client to server  

### 22. What is the difference between HTTP and HTTPS?
- **HTTP**: Insecure, plaintext.  
- **HTTPS**: HTTP + TLS/SSL encryption, secure.

### 23. What is SSL/TLS?
Protocols for encrypting data and providing authentication between client and server.

### 24. What is a socket?
An endpoint for sending/receiving data between applications over a network.

### 25. What is port forwarding?
Redirecting traffic from one port/address to another, often used in NAT and firewalls.

---

## IP & Subnetting

### 26. What is subnetting?
Dividing a large network into smaller sub-networks for efficiency and security.

### 27. What is a subnet mask?
Defines the network vs host part of an IP address. Example: `255.255.255.0`.

### 28. What is CIDR notation?
Compact representation of subnet mask, e.g. `192.168.1.0/24`.

### 29. What are private IP ranges?
- **Class A**: 10.0.0.0 – 10.255.255.255  
- **Class B**: 172.16.0.0 – 172.31.255.255  
- **Class C**: 192.168.0.0 – 192.168.255.255  

### 30. What is the difference between public and private IP?
- **Public IP**: Globally routable.  
- **Private IP**: Used in LANs, not routable on Internet.

---

## Advanced Topics

### 31. What is latency, bandwidth, and throughput?
- **Latency**: Delay before transfer starts.  
- **Bandwidth**: Max data capacity.  
- **Throughput**: Actual data transfer rate.  

### 32. What is QoS (Quality of Service)?
Mechanism to prioritize certain traffic (e.g., VoIP over bulk downloads).

### 33. What is multicast vs broadcast vs unicast?
- **Unicast**: One-to-one.  
- **Broadcast**: One-to-all.  
- **Multicast**: One-to-many (only interested receivers).  

### 34. What is VPN?
Virtual Private Network, encrypts traffic and tunnels it securely to a remote server.

### 35. What is VLAN?
Virtual LAN, logically separates networks on the same physical switch.

---

## Security & Troubleshooting

### 36. What is DDoS?
Distributed Denial of Service, flooding a target with traffic to make it unavailable.

### 37. What is traceroute used for?
To trace the path packets take to reach a destination.

### 38. What is packet sniffing?
Capturing and analyzing network packets using tools like Wireshark.

### 39. What is zero trust security?
A model where no device/user is trusted by default, even inside the network.

### 40. How do you secure a network?
- Firewalls, IDS/IPS, VPNs  
- Strong authentication & encryption  
- Patch management  
- Network segmentation  
- Monitoring and logging
