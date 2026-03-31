Idempotency
- sending the same request should result in the same effect on the server state 
- simplifies retries, at‑least‑once delivery, and failure handling.

- GET, PUT, DELETE, HEAD is idempotent
- POST is not (because you create)

An idempotency key (e.g. UUID) can be used for _at-most-once_ semantics where required



## Pitfalls
- Treating POST as idempotent without keys or dedupe.
- Hidden side effects (emails, webhooks) that fire on retries.

## Checklist
- HTTP method semantics · safe vs idempotent · retry policy · idempotency keys storage (TTL, key scope)

## Sources
- RFC 9110 HTTP Semantics
- [rest-vs-graphql](<./rest-vs-graphql.md>)

