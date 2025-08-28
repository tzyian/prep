# Question Card: TLS Handshake

- Question: Walk through the modern TLS 1.3 handshake and how HTTPS secures traffic.
- Role: fullstack
- Difficulty: mid

## Short Answer
TLS 1.3 performs an authenticated key exchange (ECDHE) in 1‑RTT, deriving symmetric keys for encryption and integrity. The server proves identity via its X.509 certificate; clients validate chain, hostname, and revocation as configured. Forward secrecy comes from ephemeral keys.

## Deep Dive
- Steps: ClientHello (cipher suites, SNI) -> ServerHello (+ certificate, key share) -> Finished; then encrypted app data.
- PKI: CA‑signed certificates; hostname verification (SAN); OCSP/CRL for revocation.
- HTTP: HTTPS = HTTP over TLS; HSTS and ALPN (HTTP/2, HTTP/3).

## Example
```text
ClientHello → ServerHello/Cert → [Finished] ↔ [Encrypted traffic]
```

## Follow‑ups
- TLS 1.2 vs 1.3 differences; 0‑RTT replay risks.
- mTLS for service‑to‑service auth.

## Pitfalls
- Skipping hostname verification; weak cipher suites; missing HSTS.

## Checklist
- ECDHE · certificate validation · forward secrecy · ALPN · HSTS

## Sources
- RFC 8446 TLS 1.3
- [[websockets-security]]

