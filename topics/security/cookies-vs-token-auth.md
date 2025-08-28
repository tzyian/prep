# Question Card: Cookies vs Token Auth

- Question: Compare cookie-based session auth and token-based (JWT/OAuth2) auth for web apps.
- Role: fullstack
- Difficulty: mid

## Short Answer
Cookies carry session IDs (opaque) and rely on server state; JWTs are self-contained tokens signed by the issuer. Cookies are simple and secure with proper flags; JWTs enable stateless auth and federation (OAuth2/OIDC) but require careful key rotation and revocation strategies.

## Deep Dive
- Cookies: `HttpOnly`, `Secure`, `SameSite`; server session store; rotate on privilege change.
- JWT: signed (JWS), optionally encrypted (JWE); short TTL + refresh tokens; introspection/blacklists.
- OAuth2/OIDC: authorization code + PKCE for SPAs; ID Token vs Access Token.

## Example
```http
Set-Cookie: sid=abc; HttpOnly; Secure; SameSite=Lax
```

## Follow‑ups
- Where to store tokens in SPAs; XSS risk and mitigations.
- Rolling keys (JWKS) and token revocation.

## Pitfalls
- Long‑lived JWTs without rotation; storing tokens in localStorage.

## Checklist
- Cookie flags · token TTL/refresh · revocation · PKCE

## Sources
- OAuth2 RFCs, OIDC Core
- [[oauth2]] · [[oauth]]

