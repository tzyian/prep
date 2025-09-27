
# Ch1 Introduction


![[Pasted image 20250926024621.png]]
Frequency Division Multiplexing
Time Division Multiplexing

Internet Exchange Point
![[Pasted image 20250926024747.png]]


Packet Delay:
![[Pasted image 20250926024842.png]]
Nodal delay = 
- Processing Delay: checking bit errors
- Queuing Delay: waiting for output link
- Transmission Delay: L / R
- Propagation Delay
Packet delay = arrival rate * pk length / link bandwidth

### Throughput:
- Rate bits are sent to sender to receiver
### Security
- packet sniffing: (Wireshark)
- IP spoofing: inject packet with false source address
- DOS

- authN
- confidentiality (encryption)
- integrity (signatures)
- access restrictions (passwords)
- firewalls



### OSI Layers
1. **application**: supporting network applications
	- HTTP, IMAP, SMTP, DNS
2. **transport**: process-process data transfer
	- TCP, UDP
3. **network**: routing of datagrams from source to destination
	- IP, routing protocols
4. **link**: data transfer between neighboring  network elements
	- Ethernet, 802.11 (WiFi), PPP
5. **physical**: bits “on the wire”
![[Pasted image 20250926025736.png]]

# Ch2 Application Layer
Requirements
- data integrity
- timing
- throughput
- security
Sockets:
- a process ends/receives to/from socket
Addressing:
- identifier to IP address and port


| TCP                                          | UDP        |
| -------------------------------------------- | ---------- |
| reliable                                     | unreliable |
| flow control                                 |            |
| congestion control                           |            |
| connection oriented                          |            |
| no timing, guarantee, security               |            |
| secured using Transport Layer Security (TLS) |            |

### Persistent HTTP:
1. Can send multiple objects in a single connection
2. No need TCP handshake
3. As little as one RTT per object rather than min 2
Non-persistent HTTP Workflow
4. Client initiates TCP to `hostname:port`
5. Host accepts TCP connection
6. Client sends request with URL into TCP connection socket for `hostname/path.index`
7. Server receives request, sends response

![[Pasted image 20250926042323.png]]
![[Pasted image 20250926042605.png]]

### State
Cookies stored on user's browser
![[Pasted image 20250926044156.png]]
### Caches
browser caches with HTTP 304

### HTTP1.1/2
| HTTP/1.1                          | HTTP/2                                      | HTTP/3                               |
| --------------------------------- | ------------------------------------------- | ------------------------------------ |
| FCFS causes Head of Line blocking | client specified order priority             | per boject error, congestion control |
| loss recovery stalls transmission | *push* unrequested objects to client        |                                      |
|                                   | divide objects into frames, schedule frames | security                             |

### QUIC
application protocol on top of UDP
- reliability, congestion control, authentication, encryption, state 
- established in one RTT
![[Pasted image 20250926044649.png]]
![[Pasted image 20250926044712.png]]

### Email
SMTP Handshaking
![[Pasted image 20250926045810.png]]

| SMTP                              | HTTP                                  |
| --------------------------------- | ------------------------------------- |
| Client push                       | client pull                           |
| Persistent connections            | Persistent(2+) / non-persistent (1.1) |
| multiple objects in multipart msg | Each object in a msg                  |
| TCP                               |                                       |


### DNS (Domain Name System)
- hostname-to-IP-address translation
- client queries root DNS server, then TLD, then authoritative server
- ![[Pasted image 20250926113711.png]]

### DNS Records
distributed database storing resource records (RR)
RR format: `(name, value, type, ttl)`

| type  | name                                          | value                                     |
| ----- | --------------------------------------------- | ----------------------------------------- |
| A     | hostname                                      | IP addr                                   |
| NS    | domain e.g. foo.com                           | hostname of auth name server for domain   |
| CNAME | alias name for canonical name<br>e.g. ibm.com | canonical name<br>e.g. servereast.ibm.com |
| MX    | name                                          | SMTP mail server associated with name     |

![[Pasted image 20250926114535.png]]

- to register a DNS, 
	- register at DNS registrar
	- create an authoritative server

### Streaming
HTTPS Live Streaming (HLS)
Change bitrate based on bandwidth

#### Dynamic Adaptive Streaming over HTTP (DASH)
Server:
- divide video file into chunks
- encode each chunk at different rates
- replicate files in CDN
- **manifest file** provide URLs for different chunks
Client:
- periodically estimates server to client bandwidth
- consults manifest file to request one chunk at maximum coding rate at a fixed time (varies coding rate and server based on bandwidth)

# Ch3 Transport Layer

- **IP datagram** → network layer unit.
- **UDP datagram** → transport layer unit
- **TCP** does not use the word _datagram_. Its unit is called a **TCP segment**.

```
+-------------------------+
| Application Data        |  ← HTTP, DNS, etc.
+-------------------------+
| TCP Segment             |  ← transport layer (reliable, stream)
| UDP Datagram            |  ← transport layer (message-oriented)
+-------------------------+
| IP Datagram (Packet)    |  ← network layer (IPv4/IPv6)
+-------------------------+
| Frame                   |  ← data link layer (Ethernet, Wi-Fi, etc.)
+-------------------------+
| Bits on the Wire        |  ← physical layer
+-------------------------+
```


| TCP                               | UDP                                                                  |
| --------------------------------- | -------------------------------------------------------------------- |
| Reliable                          | Unreliable<br><br>(HTTP/3 reliability added over application layer)  |
| In order                          | Unordered                                                            |
| Congestion control                | None<br><br>(HTTP/3 congestion control added over application layer) |
| Flow control                      | None                                                                 |
| connection setup<br>- handshaking | None except QUIC (HTTP/3)<br>Thus no RTT delay                       |
| No delay guarantees               | No delay guarantees                                                  |
| No bandwidth guarantees           | No bandwidth guarantees                                              |
|                                   | No connection state                                                  |
| larger header                     | Small header size                                                    |
| full duplex data flow             |                                                                      |

## UDP Datagram Structure
![[Pasted image 20250926160731.png]]
UDP uses weak 1's complement addition checksum

#### TCP Segment Structure
![[Pasted image 20250926161125.png]]

1. Sender gets an application layer message
2. Determines segment header fields
3. Creates segment and passes to IP
4. Receiver receives segment
5. Checks header
6. Extracts application layer msg
7. Demultiplexes message up to application

### IP Datagrams
- Each network layer datagram has source IP, dest IP
- Each datagram carries 1 transport layer segment
- Each transport layer segment has source port, dest port
- IP/UDP datagrams with same dest port # but diff source IP/port are redirected to same receiving socket
### Demuxing
- **UDP multiplexing**: simple, port-based, no connection state.
- **TCP multiplexing**: connection-based, 4-tuple, stateful.
#### Connectionless multiplexing
- **Protocol**: UDP
- **Mechanism**: Source port + destination port identify the flow.
- **No state**: Each UDP datagram is independent. The transport layer does not track sequence, reliability, or session.
- **Example**:
    - Client sends DNS query from `(IP_A, port_53000)` to `(IP_B, port_53)`.
    - Response comes back to `(IP_A, port_53000)`.
- **Multiplexing role**: Ports allow many applications on the same host to share a single IP address without maintaining a connection.
    
#### Connection-oriented multiplexing
- **Protocol**: TCP
- **Mechanism**: A connection is identified by the 4-tuple `(source IP, source port, destination IP, destination port)`.
- **Stateful**: TCP maintains sequence numbers, acknowledgments, retransmissions, and flow control.
- **Example**:
    - Multiple simultaneous HTTP requests:
        - `(10.0.0.5, 51001) ↔ (142.250.72.14, 443)`
        - `(10.0.0.5, 51002) ↔ (142.250.72.14, 443)`
    - OS keeps each connection’s state separate.
- **Multiplexing role**: The OS can manage many parallel connections reliably, ensuring each stream stays isolated and ordered.

### RDT
1. checksum
2. seq no
3. ACK with seq no the last correct pkt
4. Retransmit if no ACK received within time
5. Because required **stop and wait** for packets to be ACKed, **pipeline** in a sliding window
6. Go-Back-N ACKs the *k* uncorrupted sequential packets received
7. Selective Repeat ACKs all received packets, maintains state for 
8. Typically TCP will ACK multiple packets in a row to reduce ACKs needed

### TCP RTT timeout
- exponential weighted moving average (EWMA)
	- the last packet matters exponentially more
- EstimatedRTT = (1-a)\*EstimatedRTT + a\*SampleRTT
- a = 0.125
- Timeout Interval = EstimatedRTT + 4 \* DevRTT

### TCP Fast retransmit
- if 3 duplicate ACKs, resend unACKed seg w smallest seg
- no need wait for timeout

### TCP Flow Control
- TCP Receiver advertises free buffer space in rwnd field in TCP header

### TCP 3 Way Handshake
![[Pasted image 20250926164246.png]]

Closing a TCP connection:
- client, server each close their side of connection
- send TCP segment with FIN bit = 1
- respond to received FIN with ACK
- on receiving FIN, ACK can be combined with own FIN
- simultaneous FIN exchanges can be handled

### Network Assisted Congestion Control
- routers feedback to senders/receivers with congestion level
- congestion window (CWND).
- ![[Pasted image 20250926164654.png]]
- **Algorithms**
- increase TCP send rate until packet loss occurs at **bottleneck link**
- TCP Rate = cwnd/RTT bytes/sec\
	- Additive increase Multiplicative Decrease (sawtooth probing)
	- TCP Cubic
	- TCP Reno
	- ![[Pasted image 20250926164847.png]]
- 

