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
## JSON Web Key Set (JWKS)
- public keys published by **authorization server**
- signed by authorization server's private key
- resource servers verify JWT with authorization server JWKS public key
## Tokens
- **Authorization Code** → One-time credential for exchange.
- **Access Token** → Grants temporary access to resources (often JWT).
- **Refresh Token** → Used by client to silently obtain new access tokens without asking the user to log in again.
## Authorization Code Flow
1. **User → Client**  
    You click “Login with X”.
2. **Client → Authorization Server**  
    Redirect with parameters (`client_id`, `redirect_uri`, `scope`, `state`).
3. **Authorization Server → User**  
    Prompts login + consent.    
4. **Authorization Server → Client**  
    Sends an **Authorization Code** (short-lived, single-use).
5. **Client → Authorization Server**  
    Exchanges **Authorization Code** (and client_secret if applicable) for:  
    - **Access Token** (short-lived, bearer token for API access)
    - **Refresh Token** (long-lived, can request new access tokens)
6. **Client → Resource Server**  
    Uses **Access Token** in `Authorization: Bearer` header.
7. **Resource Server → Client**  
    Verifies token (via **JWKS**) and serves resource.
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