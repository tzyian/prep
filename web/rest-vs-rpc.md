# REST vs RPC

## Question
Compare REST, gRPC, and GraphQL trade-offs for backend APIs.

## Short Answer
- REST: resource‑oriented, simple, cache‑friendly via HTTP; verbose for chatty calls.
- gRPC: contract‑first, HTTP/2 + protobuf, bi‑di streaming; great for service‑to‑service.
- GraphQL: client‑driven queries reduce over/under‑fetch; server complexity and caching harder.

## Deep Dive
- Latency: N+1 requests vs single RPC vs single GraphQL query.
- Evolution: versioning, backward compatibility, schema governance.
- Observability: tracing and error models.

## Follow‑ups
- Security considerations with [[web-security/oauth2]].

