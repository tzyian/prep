# Question Card: HTTP Idempotency

- Question: What is idempotency in HTTP and why does it matter for APIs?
- Role: backend
- Difficulty: mid

## Short Answer
An operation is idempotent if performing it once or many times has the same effect on the server state. HTTP defines GET, PUT, DELETE, and HEAD as idempotent; POST is not. Idempotency simplifies retries, at‑least‑once delivery, and failure handling.

## Deep Dive
- Semantics: GET reads; PUT replaces a resource; DELETE removes a resource; multiple identical requests yield the same final state.
- Safety vs idempotency: safe methods don’t change state (GET), idempotent may change state but repeatable (PUT/DELETE).
- Retries: use idempotency keys for POST‑like semantics (e.g., create‑order with `Idempotency-Key`).
- Side effects: logging/metrics should be tolerant of repeats; make external calls idempotent or deduplicate.

## Example
```http
PUT /users/123
{"name":"A"}

PUT /users/123
{"name":"A"}  // same final state
```

## Follow‑ups
- How to design POST with idempotency keys? How to expire keys?
- Interaction with exactly‑once vs at‑least‑once processing.

## Pitfalls
- Treating POST as idempotent without keys or dedupe.
- Hidden side effects (emails, webhooks) that fire on retries.

## Checklist
- HTTP method semantics · safe vs idempotent · retry policy · idempotency keys storage (TTL, key scope)

## Sources
- RFC 9110 HTTP Semantics
- [[web/rest-vs-rpc]]

