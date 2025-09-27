# DNS Resolution: All 5 Layers and Wrapping/Unwrapping

https://mermaid.ink/img/pako:eNp1Vdtu2zgQ_ZUBn1zUN_mSOMI2gO0AadCsY9gugl3ohbZoi4hEqiQVJxvkdT9gP7Ff0hlKdhyl6wdLnHM4t8OhXthGx4KFzIofhVAbcSX5zvAsUrxwWhXZWphI5dw4uZE5Vw4mwC1MjN7bOnK3JAj_F8Lq9LEOL65mnrAQm8JY-SgADTWK1s5T6Nlof3qPrm6vCKRHo-3SuAaPC5cQTk9tpOOOYtRDXN8T53Y8g2vuxJ4_1zIIyviFEwaCGqZOMPUeW5pHApfCfKh7qTcPb5hfCqxuMOhjAZGaaSdAEzIJ4Ts2FdxzLiwkzuU27HRssW6LJ57lqaCiOzl3Ce3jqYMp3yQCEukiBTBpXV6WLuDrajXvULs3RIiUSNFYkjNp7YF9twxJA0DlzTPUAhHpboks0i08Ua1kN75fzSG27suQygCvLpFRuRDG9gG22sDBD1lbJ662whiegtNeTJLo6AANb_tr6SD2ey9e-nduyPLm5zelEeHN2VjZPXZ-3Bnj7-imalF1nGO4mVdNKTu9EK4wCq3Nss9AOggV11Rt4tkIYTWdQ7-F5w0SrmKb8AfUhUS4vg9h-dcsUtf3lE4QUsZ7buLSugjIqupWhVbvNxap9MeKzGhpVXRcfx5Pv3nqR78VFLSqBD5AmExZ5In_EpkcIuMKGo-Sg_ETYT_9b93COr5OpU2wh41ByxWoA6wltgGsn4aPgzDO81RucIS1AiuISWd6CYauKetO0ljdLmGaSqHcV5GmGjqeeSBCgxKwYpchASSpBTF3_o6rRaUWVcNdtUOq3R9r07m8UXnh6NwXAn7--x9s-drIjX9F_hEjd5UCkzKvcuRredlcKyvex_aVzG6m3iemiA3DS4MWlH4ssuKp9I1FY8dwbCtZ8PDb6kLBYZxr46C8WIjWOkqV5xiYYxep9tMsF1U6ZRqWZ8JH3KZ6HynWZDsjYxY6U4gmy4TJOC3ZC81BxFwiMhGxEF9TuUtcxCL1ipvw2vtb6-ywD8_HLmHhluMt1GRFjjkcPjNHCkoszFQXyrFwMDzzPlj4wp5Y2OtftC-C7vkoGJ11-8OA0GcWtoLuqN097w2CUS_ojS66Z73XJvvHxw3a3VGvNxoEw97wfNC76J83mYil0-bP8mvnP3qvvwAmIFWk?type=png

<img src="network_flow.png" style="background-color:white;">






Let's walk through **every step** of DNS resolution, showing how the application, transport, network, link, and physical layers interact, and **who does the wrapping/unwrapping at each stage**.

***

## 1. Application Layer

- **Who:** DNS client process (e.g., browser, OS resolver)
- **Action:** User types a URL (e.g., `www.example.com`). The browser extracts the hostname and checks the local DNS cache.
- **If not cached:** The DNS client creates a DNS query message ("What is the IP of www.example.com?").
- **Wrapping:** The DNS client hands the query to the OS socket API for transmission.

***

## 2. Transport Layer (UDP)

- **Who:** OS transport layer (UDP implementation)
- **Action:** The DNS query message is wrapped in a UDP segment.
    - **UDP header**: Source port (ephemeral), destination port 53 (DNS).
- **Wrapping:** OS kernel adds UDP header, computes checksum, and passes the segment to the network layer.

***

## 3. Network Layer (IP)

- **Who:** OS network layer (IP stack)
- **Action:** The UDP segment is wrapped in an IP datagram.
    - **IP header**: Source IP (client), destination IP (DNS server), protocol field set to UDP, TTL, checksum.
- **Wrapping:** OS kernel adds IP header, passes datagram to the link layer.

***

## 4. Link Layer (Ethernet/Wi-Fi)

- **Who:** Network Interface Card (NIC) hardware/driver
- **Action:** The IP datagram is wrapped in a link-layer frame (e.g., Ethernet frame).
    - **Link header**: Source MAC, destination MAC (usually router's MAC for first hop), error detection bits (CRC).
- **Wrapping:** NIC hardware/driver adds link header/trailer, sends frame over the physical medium.

***

## 5. Physical Layer

- **Who:** NIC hardware, cables, wireless transceivers
- **Action:** Frame is transmitted as bits/signals over the physical medium (copper, fiber, radio).

***

## 6. At Each Router Hop

- **Who:** Router hardware/software
- **Unwrapping:**
    - **Input port:** Router receives link-layer frame, NIC strips link header/trailer to expose IP datagram.
    - **Network layer:** Router checks IP header (destination IP, TTL, checksum), decrements TTL, recalculates checksum.
    - **Forwarding:** Router uses **longest prefix match** on destination IP to look up next hop in forwarding table.
    - **Fabric/buffer:** IP datagram is switched through router's internal fabric (crossbar, bus, etc.), buffered if output port is busy.
    - **Wrapping:** Router wraps IP datagram in a new link-layer frame for the next hop (updates MAC addresses).

***

## 7. At the Destination DNS Server

- **Who:** DNS server NIC, OS kernel, DNS server process
- **Unwrapping:**
    - **NIC:** Strips link-layer header, passes IP datagram to OS.
    - **Network layer:** OS strips IP header, passes UDP segment to transport layer.
    - **Transport layer:** OS strips UDP header, delivers DNS query to DNS server process via socket.
    - **Application layer:** DNS server process receives query, processes it, and prepares a response.

***

## 8. DNS Response (Reverse Path)

- **Who:** DNS server process, OS kernel, NIC
- **Wrapping:**
    - **Application:** DNS server creates response message.
    - **Transport:** OS wraps response in UDP segment (source port 53, dest port ephemeral).
    - **Network:** OS wraps UDP segment in IP datagram (source IP: DNS server, dest IP: client).
    - **Link:** NIC wraps IP datagram in link-layer frame (MAC addresses).
    - **Physical:** Frame sent over medium.
- **At each router:** Same unwrapping/wrapping as above, until it reaches the client.
- **At client:** NIC, OS, and DNS client process unwrap each layer in turn, finally delivering the IP address to the browser.

***

## 9. Layer-by-Layer Wrapping/Unwrapping Table

| Layer | Sender Wrapping | Router Unwrapping/Wrapping | Receiver Unwrapping |
| :-- | :-- | :-- | :-- |
| Application | DNS client creates query | N/A | DNS server receives query |
| Transport | OS adds UDP header | N/A | OS strips UDP header |
| Network | OS adds IP header | Router strips/adds IP header | OS strips IP header |
| Link | NIC adds link header/trailer | Router strips/adds link header | NIC strips link header/trailer |
| Physical | NIC transmits bits/signals | Router receives/transmits signals | NIC receives bits/signals |


***

## 10. Key Details and Interactions

- **Application layer:** Only runs on end hosts (not routers). DNS client/server processes communicate via sockets.
- **Transport layer:** UDP is connectionless, unreliable, no handshake. Each DNS query/response is a separate UDP segment.
- **Network layer:** IP datagrams are forwarded hop-by-hop. Routers use forwarding tables and longest prefix match.
- **Link layer:** Frames are only valid for a single link. Each hop re-wraps the IP datagram in a new frame with new MAC addresses.
- **Physical layer:** Handles transmission of bits/signals; not involved in protocol logic.
- **Routers:** Only unwrap link and network layers; never touch transport or application data.
- **NIC:** Handles link and physical layers; OS handles network and transport; application handles its own data.

***

## 11. Example: DNS Query from Client to Server

1. **Application:** DNS client creates query.
2. **Transport:** OS wraps in UDP segment.
3. **Network:** OS wraps in IP datagram.
4. **Link:** NIC wraps in Ethernet frame.
5. **Physical:** Frame sent as bits.
6. **Router:**
    - Unwraps Ethernet frame, checks IP datagram, forwards using routing table, re-wraps in new Ethernet frame.
7. **Destination DNS server:**
    - NIC unwraps Ethernet frame, OS unwraps IP datagram and UDP segment, DNS server process receives query.
8. **Response:** Reverse process for DNS reply.

***

This covers **all five layers**, showing exactly **who wraps and unwraps** at each stage, and how routers, NICs, OS, and applications interact for DNS resolution.[^1][^2][^3]