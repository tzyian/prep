## Authentication vs Authorization
Authentication: guarantee access scope
Authorization: guarantee identity

Authentication guarantees authorization
Authorization does not lead to authentication
## OAuth Roles
1. Resource Owner (owner grants permission to access resources)
	1. you own your Google photos
2. Client (web app)
	1. Your printing shop app wants to access resources on your behalf
3. Authorization Server (keycloak authenticates)
	1. Google issues an access token to the printing shop app
4. Resource server (where resources stored, accepts access tokens)
	1. Google photos API stores the images

## OpenID Connect (OIDC)
- adds authentication to OAuth authorization
- Adds ID Token to the JWT (sub, email, name)

## JWT
(assymmetric)
- `header.payload.signature`
- JWT signature created by auth server
	- `signature = RS256_sign( SHA256( header + "." + payload ), private_key )
- `PK = (n, e)`
- Auth server computes digest and signature
	- `h = SHA256(header || "." || payload)`
	- `s = m^d mod n`
- Resource server takes digest `h` and signature `s`. If `PK = (n, e)`
	- `m' = s^e mod n`

## JSON Web Key Set (JWKS)
- public keys published by **authorization server**

- signed by authorization server's private key


- resource servers verify JWT with authorization server JWKS public key
```json
{ // JWT header
  "alg": "RS256",
  "typ": "JWT", 
  "kid": "abc123"  // key id
}
```
API takes JWKS public key and runs JWT `algo` to see if the JWT signature is authentic (validity that it is issued by the auth provider)

```json
{ // JWKS
  "keys": [
    {
      "kty": "RSA", 
      "kid": "abc123",
      "use": "sig",
      "n": "...",   // modulus
      "e": "AQAB"   // exponent
    }
  ]
}

```

## Tokens
- **Authorization Code** → One-time credential for exchange.
- **Access Token** → Grants temporary access to resources (often JWT).
- **Refresh Token** → Used by client to silently obtain new access tokens without asking the user to log in again.

### Authorisation Request

## Authorization Code Flow
1. **User → Client**  
    You click “Login with X”.
2. **Client → Auth Server**  (in query params)
		- `response_type=code` → ask for authorization code
		- `client_id=...` → app’s registered ID
		- `redirect_uri=...` → where code should be sent back
		- `scope=openid profile email api.read` → requested access scopes
		- `state=xyz123` → random string to prevent CSRF
		- `code_challenge=...` → `BASE64(SHA256(PKCE_verifier))`
		- `code_challenge_method=S256` → PKCE method
3. **Auth Server → User**  
    Prompts login + consent.    
4. **Auth Server → Client**  (in query params)
		- `code=abc123` → short-lived authorization code
		- `state=xyz123` → must match original
5. **Client → Auth Server**  (Token exchange in POST body)
	    - `grant_type=authorization_code`
		- `code=abc123` → authorization code received
		- `redirect_uri=...` → must match original
		- `client_id=...` (and client_secret if confidential client)
		- `code_verifier=...` → original PKCE random string
6. **Auth server --> Client** (token returned in JSON)
		- `token_type=Bearer`
		- `expires_in=3600` → lifetime in seconds
		- `refresh_token=def456...` → (if allowed) for silent renewal
		- `id_token=eyJhbGciOiJSUzI1Ni...` → (if OIDC scope) identity claims (who the user is)
7. **Client → Resource Server**  
	    - Uses **Access Token** in `Authorization: Bearer` header.
8. **Resource Server → Client**  
	- Verifies token (via **[[#JSON Web Key Set (JWKS))]]**) and serves resource.
9. **Client → Auth Server**  (Token exchange in POST body)
	Sent (POST body):
		- `grant_type=refresh_token`
		- `refresh_token=def456...`
	Returned (JSON):
		- `access_token=newJWT...`
		- (possibly new `refresh_token`)
    
## Authorization Code Flow With PKCE
https://auth0.com/docs/get-started/authentication-and-authorization-flow/authorization-code-flow-with-pkce

Public clients (SPAs, mobiles) cannot store CLIENT_SECRET securely
PKCE Hardens authorization code exchange

PKCE protects public clients (SPAs, mobile) by proving possession of a secret derived at runtime.

- Client sends `code_challenge=BASE64URL(SHA256(code_verifier))` in auth request.
- Exchanges auth code with `code_verifier` to redeem; server matches challenge.
- Prevents code interception from being reused.

![[auth-sequence-auth-code-pkce.png]]

See also: [[oauth2]] · [[questions/fullstack/cookies-vs-token-auth]]

|Step|Exchange|Contains|
|---|---|---|
|1. Auth Request|Query params|client_id, redirect_uri, scope, state, code_challenge|
|2. Auth Response|Redirect params|code, state|
|3. Token Request|POST body|code, redirect_uri, client_id, code_verifier|
|4. Token Response|JSON|access_token, refresh_token, id_token, expires_in|
|5. Resource Request|HTTP header|Authorization: Bearer <access_token>|
|6. JWKS Fetch|JSON|Public keys (n, e, kid, alg)|
|7. Refresh Request|POST body|grant_type=refresh_token, refresh_token|