# WebSockets Security

- Authenticate on upgrade (e.g., Bearer token) and re‑auth on reconnect.
- Authorize per channel/room; validate messages server‑side.
- Protect against CSRF (no cookies or use tokens); rate limit.
- Use `wss://` and verify TLS just like HTTPS.

Related: [[questions/fullstack/websockets-vs-sse]] · [[questions/fullstack/tls-handshake]]

