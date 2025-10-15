# How many concurrent connections — sockets / ports / hostnames

Short answers up front:
- **One listening TCP socket (IP:port)** can have **many** concurrent connections.  
- Each TCP connection is uniquely identified by a **4-tuple**: `(client IP, client port, server IP, server port)`.  
- For a **single client IP → single server IP:port**, max simultaneous TCP connections ≈ **number of available client ephemeral ports** (typically tens of thousands).  
- Practical limits are **resource-bound** (file descriptors, memory, kernel limits, NAT), not the 4-tuple math alone.

---

## Concepts & formulas

1. **Ports per IP**  
   - Ports are 16-bit unsigned integers → `0..65535`.  
   - Usable ephemeral ports are typically `1024..65535` (so ≈ 64k total minus well-known ports).  
   - Formula: `max_ports = 65535` (theoretical). Practical ephemeral range reduces this.

2. **Unique TCP connection count to one server IP:port**  
   - Each connection must have a unique client port (or client IP+port).  
   - If you have `C` distinct client IPs and each client has `P` ephemeral ports available, the max unique connections to the same server IP:port is roughly:
     - `Max ≈ C * P`
   - Example: one client IP only → `Max ≈ P` (often ~28k–64k depending on ephemeral range).

3. **Server-side port reuse**  
   - The server listens on one server socket `(server IP, server port)`. The kernel creates an **established socket** per accepted connection, each bound to the same server port but different client-socket info. So *1 listening port → many established sockets*.

4. **Socket vs connection**  
   - `Socket` on server: the listening socket is a handle to accept new connections. Each accepted connection is a separate socket object (with its own file descriptor).

---

## Practical limits (what actually stops you)

- **File descriptor limit (per process)** — `ulimit -n`. Default often 1024; can be increased to 100k+. Each TCP conn uses an FD.
- **Kernel memory / TCP buffers** — many connections consume RAM (TCB, buffers).
- **Listen backlog / accept rate** — backlog queue and application ability to `accept()` quickly; overload leads to dropped connections.
- **Ephemeral port exhaustion on clients/NAT** — clients behind NAT share one public IP; NAT maps many internal connections to `{public IP, public port}`, so NAT port pool (~64k) can be exhausted.
- **Application/thread model / CPU** — context switching, threads, event loop scaling.
- **Network gear limits** — load balancers, firewalls, middleboxes may limit concurrent flows.
- **TIME_WAIT** — many short TCP connections leave sockets in TIME_WAIT and consume ephemeral ports temporarily.

---

## Special cases

- **UDP**: connectionless — a single UDP socket (listening on server IP:port) can receive datagrams from many peers. There’s no 4-tuple enforced by kernel as “connection”, but you can `connect()` a UDP socket to a peer; still one listening port handles many sources.
- **HTTP/1.1 (browser limits)**: clients historically limit concurrent TCP connections per hostname (e.g., 6). This is a browser policy, not a protocol limit.
- **HTTP/2 & HTTP/3 (QUIC)**: multiplexing multiple logical streams over a *single* transport connection (TCP for H2, QUIC/UDP for H3). So one TCP/QUIC connection can carry **many concurrent logical requests** — often hundreds or thousands (limited by implementation and settings).
- **Hostname → multiple IPs**: DNS can return several A/AAAA records; connections to the same hostname may go to different IPs (spreading load). So “per hostname” concurrency depends on DNS mapping and client resolver behavior.

---

## Quick practical examples

- **Single client machine to web server (one server IP:443)**  
  - If client ephemeral range is 32768–60999 (~28k ports), the client can open ~28k concurrent TCP connections to that server IP:443 (subject to OS limits and app FDs).

- **Many clients to one server process (one server IP:80)**  
  - Server can accept millions of connections *in theory* if:
    - it has enough file descriptors,
    - enough RAM,
    - kernel tuned (net.ipv4.tcp_mem, etc.),
    - and hardware/CPU to handle them.

- **Client behind NAT**  
  - NAT public IP has only ~64k possible source ports → if many internal clients try many outbound connections, NAT port pool can be exhausted.

---

## What to mention in interviews / notes
- Emphasize the **4-tuple uniqueness** for TCP.  
- Mention **ephemeral port limits per IP** for client-side constraints.  
- Call out **resource (FD/memory/CPU) & NAT** as the main real-world limits.  
- Note modern transports (HTTP/2, HTTP/3) reduce need for many TCP connections by multiplexing streams.

---

## One-line summary
A single server `IP:port` can handle **many** concurrent TCP connections (each distinguished by client IP+client port); the theoretical cap is huge (clients × ~65k ephemeral ports), but **real limits come from file descriptors, kernel/network resources, NAT port pools, and application/OS tuning**.
