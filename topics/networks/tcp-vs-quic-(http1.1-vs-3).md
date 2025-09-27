QUIC reduces the handshake cost by collapsing transport and crypto setup into a single exchange, unlike TCP+TLS which needs multiple.

- **TCP + TLS 1.2 (traditional):**
    1. TCP 3-way handshake (SYN, SYN-ACK, ACK).
	2. TLS handshake (2+ RTTs: exchange randoms, certificates, Diffie-Hellman).  
        ⇒ Total: ~2 RTTs before app data.
        
- **QUIC (over UDP):**
    1. QUIC integrates TLS 1.3 inside its transport.
    2. First client packet contains both the transport connection request _and_ the TLS ClientHello.
    3. Server responds with transport state + TLS ServerHello in one packet.  
        ⇒ Secure channel and transport are ready after **1 RTT**.      
- **0-RTT in QUIC:** if client has previously connected, it can send app data in its very first packet (encrypted with resumption keys). Server can accept or reject it.

Why possible
- QUIC runs in user space over UDP, so it avoids kernel TCP stack constraints.
- TLS 1.3 reduced handshake messages vs TLS 1.2