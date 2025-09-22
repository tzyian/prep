
# Networking Q&A: UDP, TCP, HTTPS

---

## 1. Does UDP handshake?
- **No.**
- UDP (User Datagram Protocol) is **connectionless**:
  - Sender just sends datagrams.
  - No setup, no teardown.
- Pros: Low overhead, fast.
- Cons: No reliability, ordering, or congestion control.
- Use cases: DNS, VoIP, video streaming, gaming.

---

## 2. How many TCP back-and-forths (handshakes)?
### Connection Setup (3-way handshake)
1. SYN → Client → Server
2. SYN-ACK → Server → Client
3. ACK → Client → Server  
**= 3 messages, 1.5 round-trips.**

### Connection Teardown (4-way handshake)
1. FIN → Client → Server
2. ACK → Server → Client
3. FIN → Server → Client
4. ACK → Client → Server  
**= 4 messages, 2 round-trips.**

---

## 3. What’s in the TCP header?
TCP header has **mandatory 20 bytes + optional fields**:

- **Source Port (16 bits)**
- **Destination Port (16 bits)**
- **Sequence Number (32 bits)**
- **Acknowledgment Number (32 bits)**
- **Data Offset (4 bits)** – length of TCP header
- **Flags (9 bits total)**:
  - URG, ACK, PSH, RST, SYN, FIN
- **Window Size (16 bits)** – flow control
- **Checksum (16 bits)** – error detection
- **Urgent Pointer (16 bits)**
- **Options** (variable, e.g., MSS, window scaling)

---

## 4. What’s in the HTTPS header?
### Important distinction:
- **HTTPS** = HTTP over TLS.
- After TLS handshake, the HTTP request/response is **encrypted**.
- So: Wireshark shows TLS records, not raw HTTP headers.

### Inside (if decrypted):
Typical **HTTP request headers**:
- Host: `example.com`
- User-Agent: `Mozilla/...`
- Accept: `text/html`
- Content-Type: `application/json`
- Authorization: `Bearer <token>`
- Cookie: `<cookie-data>`

Typical **HTTP response headers**:
- Status: `200 OK`
- Content-Type: `text/html; charset=UTF-8`
- Content-Length: `1234`
- Set-Cookie: `sessionid=...; HttpOnly; Secure`
- Server: `nginx/1.23`

TLS adds its own record layer:
- **TLS Record Header**:
  - Content type (handshake, alert, application data)
  - Version
  - Length
  - Encrypted payload (which contains HTTP headers + body)

So: in HTTPS, the **HTTP headers are still there**, but they’re **encrypted inside TLS records**.

---
