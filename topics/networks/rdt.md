# Reliable Data Transfer (RDT)

## What is RDT?
Reliable Data Transfer (RDT) refers to protocols that ensure **data is delivered accurately and in order** across unreliable or lossy channels (like the Internet).  
It is often taught using a sequence of incremental protocols: `rdt1.0 → rdt2.0 → rdt2.1 → rdt2.2 → rdt3.0`.

---

## 1. rdt1.0 – Perfect Channel
- Assumes an **error-free channel**.  
- Sender simply sends packets, receiver delivers them.  
- No need for acknowledgments (ACKs) or retransmissions.  

---

## 2. rdt2.0 – Channel with Bit Errors
- Now the channel may corrupt bits.  
- Uses **ACKs** and **NAKs (negative acknowledgments)**:
  - ACK = packet received correctly.  
  - NAK = packet corrupted → sender retransmits.  
- **Problem**: What if the ACK/NAK itself is corrupted?

---

## 3. rdt2.1 – Handling Corrupted ACK/NAK
- Adds **sequence numbers** (e.g., 0/1) to detect duplicates.  
- Sender can distinguish whether ACK/NAK is for the current or previous packet.  
- Receiver discards duplicate packets.  

---

## 4. rdt2.2 – NAK-free Protocol
- Instead of NAKs, receiver just sends ACKs with the **expected sequence number**.  
- If sender receives duplicate ACK → it knows retransmission is needed.  
- Similar to how **TCP handles ACKs**.

---

## 5. rdt3.0 – Channels with Bit Errors + Packet Loss
- Adds **timeouts** to recover from packet loss.  
- Sender:
  - Starts a timer when sending a packet.  
  - If no ACK arrives → retransmits.  
- This is essentially the **Stop-and-Wait ARQ protocol**.

---

## Summary
- **rdt1.0**: Perfect channel (no errors).  
- **rdt2.0**: Errors possible → ACK/NAK.  
- **rdt2.1**: Errors in ACK/NAK → sequence numbers.  
- **rdt2.2**: NAK-free (duplicate ACKs used).  
- **rdt3.0**: Adds timeout for packet loss → Stop-and-Wait.  

---

## Relation to Real Protocols
- **Stop-and-Wait ARQ** = rdt3.0  
- **Go-Back-N (GBN)** = pipeline with window, cumulative ACKs.  
- **Selective Repeat (SR)** = pipeline with selective ACKs/retransmission.  
- **TCP** = practical reliable transport using cumulative ACKs + sliding window + retransmissions.
