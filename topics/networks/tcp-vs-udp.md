### What’s the difference between TCP and UDP?
| Feature     | **TCP (Transmission Control Protocol)**                                          | **UDP (User Datagram Protocol)**                                        |
| ----------- | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Connection  | Connection-oriented (requires setup)                                             | Connectionless (no setup)                                               |
| Reliability | Reliable: guarantees delivery, retransmits lost packets, ensures correct order   | Unreliable: no guarantee of delivery, ordering, or duplicate protection |
| Overhead    | Higher (due to acknowledgments, headers ~20 bytes)                               | Lower (smaller header ~8 bytes)                                         |
| Speed       | Slower, due to connection management and reliability mechanisms                  | Faster, due to minimal overhead                                         |
| Use cases   | Web browsing (HTTP/HTTPS), email (SMTP), file transfer (FTP), remote login (SSH) | Streaming (video/audio), DNS queries, VoIP, online gaming               |

---

### Explain the 3-way handshake in TCP
The **TCP 3-way handshake** is used to establish a reliable connection between client and server:

1. **SYN** (synchronize):  
   - Client sends a TCP segment with the SYN flag set.  
   - Indicates a request to establish a connection and includes the client’s initial sequence number.

2. **SYN-ACK** (synchronize-acknowledge):  
   - Server responds with a segment that has both SYN and ACK flags set.  
   - Acknowledges client’s sequence number and sends its own initial sequence number.

3. **ACK** (acknowledge):  
   - Client sends back an ACK to confirm receipt of the server’s SYN.  
   - At this point, the connection is established, and data transfer can begin.

**Diagram:**

