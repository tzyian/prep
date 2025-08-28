### How does DNS resolution work?

**DNS (Domain Name System) resolution** is the process of translating a human-readable domain name (e.g., `www.example.com`) into an IP address (e.g., `93.184.216.34`) that computers can use.

#### Steps in DNS Resolution:

1. **Check Local Cache**  
   - The browser first checks its own DNS cache.  
   - If found, resolution ends here.

2. **Check OS Resolver Cache**  
   - The operating system maintains a cache (e.g., `hosts` file, DNS cache service).  
   - If the domain is resolved locally, it returns immediately.

3. **Query Recursive Resolver (ISP / Custom DNS)**  
   - If not cached locally, the query is sent to a recursive DNS resolver (e.g., provided by your ISP, or public resolvers like Google DNS `8.8.8.8` or Cloudflare `1.1.1.1`).  
   - The resolver’s job is to find the IP by contacting other DNS servers.

4. **Root DNS Servers**  
   - The resolver queries a **root server** (there are 13 root server clusters worldwide).  
   - The root server doesn’t know the final IP but directs the resolver to the **TLD (Top-Level Domain) server** (e.g., `.com`, `.org`, `.net`).

5. **TLD DNS Servers**  
   - The TLD server responds with the address of the **authoritative DNS server** for the requested domain.

6. **Authoritative DNS Server**  
   - The authoritative server contains the actual DNS records (A, AAAA, CNAME, MX, etc.).  
   - It returns the IP address (e.g., A record → IPv4 address).

7. **Return to Client**  
   - The recursive resolver caches the result (for the duration of the TTL).  
   - It sends the IP address back to the client’s OS → browser.  
   - The browser can now open a TCP/UDP connection to the server’s IP.

---

#### Diagram:

Browser → OS Cache → Recursive Resolver  
↓  
Root Servers  
↓  
TLD Servers (.com, .org)  
↓  
Authoritative DNS Server  
↓
Final IP Returned


---

#### Example:
Resolving `www.example.com`:
1. Browser cache miss.  
2. OS cache miss.  
3. Recursive resolver queries root → TLD (`.com`) → authoritative server for `example.com`.  
4. Authoritative server returns `93.184.216.34`.  
5. Browser connects to `93.184.216.34`.

