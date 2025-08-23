# Question Card: WebSockets vs SSE

- Question: Compare WebSockets and Server-Sent Events (SSE). When to use each?
- Role: fullstack
- Difficulty: intro

## Short Answer
WebSockets provide full‑duplex, persistent connections for bi‑directional messaging. SSE is a unidirectional, server‑to‑client stream over HTTP for simple event streams. Use WebSockets for chat/collab; SSE for notifications, market data, or live updates where client→server is minimal.

## Deep Dive
- Transport: WebSocket upgrade vs SSE as `text/event-stream`.
- Reliability: SSE auto‑reconnect with `Last-Event-ID`; WebSockets need app‑level acks/heartbeats.
- Infra: SSE plays well with HTTP/2, proxies; WebSockets need proxy/load balancer support.

## Example
```http
GET /events
Accept: text/event-stream

data: {"price": 10}

```

## Follow‑ups
- Scaling (sticky sessions, sharding); backpressure.
- Security: auth on upgrade, token refresh.

## Pitfalls
- Using WebSockets for simple one‑way feeds; missing reconnect/backoff.

## Checklist
- Duplex vs unidirectional · reconnect · proxy support · backpressure

## Sources
- MDN Web Docs
- [[web/websockets-security]]

