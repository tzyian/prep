# Securing the OSI 7 Layers

## 1. Physical Layer
- **Risks**: Wiretapping, hardware theft, physical tampering.
- **Security Measures**:
  - Physical access controls (locks, guards, CCTV).
  - Environmental protections (fire suppression, cooling).
  - Shielded cables, secure wiring closets.

## 2. Data Link Layer
- **Risks**: MAC spoofing, ARP poisoning, VLAN hopping.
- **Security Measures**:
  - Port security on switches (limit MAC addresses per port).
  - VLAN segmentation & isolation.
  - ARP inspection, DHCP snooping.
  - Use WPA3/WPA2 for Wi-Fi encryption.

## 3. Network Layer
- **Risks**: IP spoofing, routing attacks, DoS/DDoS.
- **Security Measures**:
  - Firewalls, packet filtering, ACLs.
  - Intrusion detection/prevention (IDS/IPS).
  - Secure routing protocols (e.g., OSPF with authentication, BGP with RPKI).
  - Anti-spoofing filters.

## 4. Transport Layer
- **Risks**: Port scanning, session hijacking, SYN floods.
- **Security Measures**:
  - Use TLS/DTLS for encryption.
  - Rate limiting, SYN cookies for DoS protection.
  - Disable unused ports/services.
  - Strong authentication for sessions.

## 5. Session Layer
- **Risks**: Session hijacking, replay attacks, unauthorized access.
- **Security Measures**:
  - Strong session tokens (long, random, unique).
  - Timeout & reauthentication policies.
  - Mutual authentication mechanisms.
  - Protect sessions with encryption (TLS).

## 6. Presentation Layer
- **Risks**: Data interception, format manipulation, weak encryption.
- **Security Measures**:
  - Standardized encryption (AES, RSA).
  - Data integrity checks (hashing, HMAC).
  - Secure serialization/deserialization.
  - Avoid weak ciphers (e.g., DES, RC4).

## 7. Application Layer
- **Risks**: Malware, phishing, SQL injection, XSS, CSRF.
- **Security Measures**:
  - Input validation & sanitization.
  - Use secure coding practices (OWASP Top 10).
  - Multi-factor authentication.
  - Web application firewalls (WAFs).
  - Regular security patches & vulnerability scans.
