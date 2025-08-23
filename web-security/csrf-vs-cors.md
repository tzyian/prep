# Question Card: CSRF vs CORS

- Question: Explain CSRF and CORS. How are they different and how do you mitigate CSRF?
- Role: fullstack
- Difficulty: mid

## Short Answer
CSRF is a cross‑site request forgery where a browser reuses credentials to send unwanted requests; mitigate with same‑site cookies, anti‑CSRF tokens, and checking `Origin`/`Referer`. CORS is a browser enforcement for cross‑origin requests; it controls which origins can read responses via preflight/headers. CORS doesn’t mitigate CSRF by itself.

## Deep Dive
- CSRF: requires ambient credentials (cookies); tokens bind intent to user session; SameSite=Lax/Strict helps.
- CORS: `Access-Control-Allow-Origin`, credentials mode, preflight with `OPTIONS`.
- SPA auth: prefer tokens in memory + `SameSite` cookies for refresh.

## Example
```http
Set-Cookie: sid=...; SameSite=Lax; Secure
```

## Follow‑ups
- Double submit cookie vs synchronizer token.
- Handling `SameSite=None; Secure` for third‑party.

## Pitfalls
- Assuming CORS blocks CSRF; misusing wildcard ACAO with credentials.

## Checklist
- SameSite · CSRF tokens · Origin checks · CORS headers

## Sources
- MDN Web Docs on CSRF/CORS
- [[web/rest-vs-rpc]]

