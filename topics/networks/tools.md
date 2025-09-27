# 🟢 Application Layer
### 1. `curl` / `wget`
- **Use**: Test HTTP(S) endpoints, APIs.
- **Freq**: Very common.
- **Scenario**: If web app is down, first check if the endpoint responds. Confirms if service itself is reachable.
- **Bad**: `Timeout`, `Connection refused`, `5xx` errors → service crash or backend issue.

```
> curl google.com

HTTP/2 200  
server: nginx  
content-type: text/html; charset=UTF-8  
content-length: 1256  
```
**Interpretation**:  
- 200 OK = success.  
- Server = nginx.  
- Content-Type = HTML.  

### 2. `dig` / `nslookup`
- **Use**: DNS resolution.
- **Freq**: Common when domain names fail.
- **Scenario**: If app resolves via hostname but not IP. Confirms DNS path.
- **Good**: IP address returned quickly.
- **Bad**: `NXDOMAIN`, `SERVFAIL`, slow resolution → DNS misconfig or outage.

```
> dig google.com

;; ANSWER SECTION:
google.com.             15      IN      A       142.251.12.100
```
**Interpretation**:  
- A record = IPv4 address.  
- TTL = 215 seconds cache validity.  

### 3. `nmap`
- **Use**: Check open ports, services.
- **Freq**: Medium (troubleshooting or security).
- **Scenario**: If app can’t connect, confirm server is listening on expected port.
- **Good**: Only expected ports open.
- **Bad**: Target port closed/filtered.

```
> nmap google.com


Nmap scan report for google.com (74.125.68.100)
Host is up (0.0082s latency).
Other addresses for google.com (not scanned): 74.125.68.102 
rDNS record for 74.125.68.100: ab-in-g101.1e200.net
Not shown: 998 filtered ports

PORT    STATE SERVICE
80/tcp  open  http
443/tcp open  https 
```


---

# 🟡 Transport Layer
### 4. `ss` (modern) / `netstat` (legacy)
- **Use**: View sockets, TCP states.
- **Freq**: Common.
- **Scenario**: If service is running but clients fail, check if it listens on correct port/IP.
- **Good**: `LISTEN` on right port, healthy established connections.
- **Bad**: Many sockets in `SYN_RECV` (handshake stuck) or `TIME_WAIT` (exhaustion).
```
> # Show all TCP sockets listening on the local 8080 port:
> ss -lt src :{{8080}}

State   Recv-Q Send-Q Local Address:Port    Peer Address:Port  
LISTEN  0      128    0.0.0.0:22           0.0.0.0:*  
ESTAB   0      0      192.168.1.10:50512   142.250.190.14:443  
```
**Interpretation**:  
- LISTEN = SSH service.  
- ESTAB = active HTTPS connection.  


Can also use to see processes using :8080
#### `lsof -i :8080`

### 5. `iperf3`
- **Use**: Measure throughput, jitter, loss.
- **Freq**: Less common, but important for performance issues.
- **Scenario**: If app is slow, test raw bandwidth and latency between client and server.
- **Good**: Close to line speed, low loss.
- **Bad**: Throughput far below expected → congestion or throttling.
    
---

# 🔵 Network Layer
### 6. `ping`
- **Use**: Connectivity check, RTT.
- **Freq**: Very common.
- **Scenario**: If server unreachable, test basic ICMP path.
- **Good**: Replies with stable RTT.
- **Bad**: `100% packet loss` → host down, firewall blocking ICMP, or network issue.

- **Purpose**: Checks reachability and measures round-trip latency.
- **How it works**: Sends ICMP Echo Request, waits for Echo Reply.
- **Example**
```
> ping google.com

PING google.com (142.250.190.14): 56 data bytes  
64 bytes from 142.250.190.14: icmp_seq=0 ttl=118 time=12.3 ms  
64 bytes from 142.250.190.14: icmp_seq=1 ttl=118 time=11.9 ms  
64 bytes from 142.250.190.14: icmp_seq=2 ttl=118 time=12.1 ms  

--- google.com ping statistics ---  
3 packets transmitted, 3 received, 0% packet loss  
round-trip min/avg/max/stddev = 11.9/12.1/12.3/0.2 ms  
```
**Interpretation**:  
- Avg latency ~12 ms → good.  
- 0% loss → stable connection.  

### 7. `traceroute` / `tracepath`
- **Use**: Show route to host.
- **Freq**: Common when connectivity partially fails.
- **Scenario**: If `ping` fails or latency spikes, trace path to find failing hop.
- **Good**: Gradual RTT increase.
- **Bad**: Sudden loss or large latency jump → routing/firewall issue.

```
 > traceroute google.com

 1  192.168.1.1  1.1 ms  0.9 ms  0.8 ms  
 2  10.10.0.1    8.3 ms  8.5 ms  8.7 ms  
 3  * * *  
 4  72.14.219.1 15.5 ms 15.7 ms 15.8 ms  
 5  142.250.190.14 16.1 ms 16.3 ms 16.2 ms  
```
**Interpretation**:  
- Hop 1 = local router.  
- Hop 3 = no ICMP reply.  
- Gradual latency increase = normal.  

Note:
- **Many networks stop responding to traceroute probes.**
- Routers may be configured to drop or rate-limit ICMP responses.
- Some backbone routers don’t reply at all to avoid being overloaded.
- **Your traffic may still reach the destination.** That’s why a normal `ping google.com` works even if traceroute seems to “stop.”
### 8. `mtr`
- **Use**: Continuous `ping`+`traceroute`.
- **Freq**: Very common for diagnosing intermittent issues.
- **Scenario**: If app intermittently fails, check live packet loss/latency.
- **Good**: Stable RTT, 0% loss.
- **Bad**: High loss at a hop → network congestion or failure.

```
HOST: mypc.local    Loss%   Snt   Last   Avg  Best  Wrst StDev  
  1. 192.168.1.1      0.0%    10   1.1   1.2   1.0   1.4   0.1  
  2. 10.10.0.1        0.0%    10   8.2   8.4   8.1   8.6   0.2  
  3. 72.14.219.1      0.0%    10  15.4  15.6  15.2  15.9   0.2  
  4. 142.250.190.14   0.0%    10  16.2  16.3  16.0  16.5   0.2  
```
**Interpretation**:  
- No packet loss.  
- Latency stable.  


---

# 🟣 Link Layer
### 9. `ip` / `ifconfig`
- **Use**: Show interface IP, routes.
- **Freq**: Very common.
- **Scenario**: If app can’t bind or connect, check interface IP and default route.
- **Good**: Correct IP, default route present.
- **Bad**: Interface `DOWN`, wrong subnet/gateway.

```
inet 192.168.1.10/24 brd 192.168.1.255  
```
**Interpretation**:  
- Device IP = 192.168.1.10.  
- Subnet mask /24 = 255.255.255.0.  

### 10. `ethtool`
- **Use**: Check NIC speed/duplex/link.
- **Freq**: Less frequent (hardware-level issues).
- **Scenario**: If random packet drops, check NIC negotiation.
- **Good**: Full-duplex, expected speed.
- **Bad**: Half-duplex mismatch → collisions, retransmissions.

---

# ⚫ Physical Layer
### 11. `tcpdump`
- **Use**: Capture packets.
- **Freq**: Common for deep debugging.
- **Scenario**: If app handshake fails (e.g., no TCP ACK), capture traffic to confirm packets leave and return.
- **Good**: Expected SYN → SYN-ACK → ACK.
- **Bad**: Only SYN seen, no reply → server down, firewall drop, or blackhole route.

```
192.168.1.10.50512 > 142.250.190.14.443: Flags [S], seq 12345  
```

**Interpretation**:  
- [S] = SYN packet (handshake start).  
- Shows TCP sequence numbers, ports.  

### 12. `wireshark` / `tshark`
- **Use**: Protocol analysis (TLS, HTTP, DNS, etc).
- **Freq**: Medium (heavier tool).
- **Scenario**: If app errors aren’t clear, capture and decode traffic.
- **Good**: Proper protocol exchanges.
- **Bad**: Malformed packets, failed TLS handshake, retransmits.

---

# 🔎 If an App Goes Down: Debugging Order
1. **Application check** (`curl`/`wget`) → is the service responding?
2. **DNS check** (`dig`) → does the hostname resolve?
3. **Socket check** (`ss`) → is server listening on correct port?
4. **Connectivity check** (`ping`) → is host reachable?
5. **Path check** (`traceroute`/`mtr`) → is the route stable?
6. **Interface check** (`ip addr`, `ip route`) → is local config correct?
7. **NIC check** (`ethtool`) → is link healthy?
8. **Packet capture** (`tcpdump`) → are packets being sent/dropped?
9. **Performance test** (`iperf3`) → is network throughput/jitter acceptable?
10. **Deep protocol inspection** (`wireshark`) → only if all else fails.


`sudo iptables -L -v -n` or `sudo ufw status`
See if firwarell is blocking anything


# Summary Table of Linux Network Tools

# Extended Summary Table of Linux Network Tools

| Layer        | Command                  | Frequency of Use | Scenario (when app down)                  | What It Is Used For                                |
|--------------|--------------------------|-----------------|-------------------------------------------|---------------------------------------------------|
| Application  | curl / wget              | Very common     | Check if web app/API responds              | Test HTTP(S) connectivity, headers, body          |
| Application  | dig / nslookup           | Common          | Name resolves but IP works / DNS errors    | DNS resolution, query A/AAAA records              |
| Application  | nmap                     | Medium          | Suspect port closed or wrong service       | Port scanning, service detection                  |
| Transport    | ss / netstat             | Common          | Service running but clients can’t connect  | Check listening sockets and connection states     |
| Transport    | iperf3                   | Less common     | App is slow (performance issue)            | Measure bandwidth, jitter, packet loss            |
| Network      | ping                     | Very common     | App unreachable, check host liveness       | Test ICMP connectivity and round-trip time        |
| Network      | traceroute / tracepath   | Common          | Path issues, routing suspected             | Show packet path and latency per hop              |
| Network      | mtr                      | Very common     | Intermittent issues, packet loss suspected | Continuous ping + traceroute with live stats      |
| Link         | ip / ifconfig            | Very common     | Interface misconfig suspected              | Show IP addresses, routes, interface state        |
| Link         | ethtool                  | Less frequent   | Suspect NIC or cabling issue               | Check NIC speed, duplex, link status              |
| Link↔Network | iptables                 | Medium          | Traffic blocked by firewall suspected      | Packet filtering, NAT, firewall rules at kernel   |
| Link↔Network | ufw                      | Medium          | Easier firewall management, app blocked    | User-friendly frontend for iptables               |
| Physical↔Net | tcpdump                  | Common          | Handshake fails, packets not received      | Capture and inspect raw packets                   |
| Physical↔App | wireshark / tshark       | Medium          | Protocol-level errors (TLS, HTTP, DNS)     | Deep packet inspection and protocol analysis      |
