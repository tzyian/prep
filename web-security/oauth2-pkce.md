# OAuth2 PKCE

PKCE protects public clients (SPAs, mobile) by proving possession of a secret derived at runtime.

- Client sends `code_challenge=BASE64URL(SHA256(code_verifier))` in auth request.
- Exchanges auth code with `code_verifier` to redeem; server matches challenge.
- Prevents code interception from being reused.

See also: [[web-security/oauth2]] · [[questions/fullstack/cookies-vs-token-auth]]

