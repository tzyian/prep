### Identity Provider (Keycloak/Cognito)
Configure:
1. redirect URI
2. logout URI
3. client ID
4. scopes
5. issuer
6. JWKS endpoint
7. (DO NOT USE A CLIENT SECRET)


## NextJs Backend for Frontend
The NextJS server-side handles the auth, but the backend is left to a separate service

Flow:
1. User logs in:
2. Login stores a `HTTPOnly`, HTTPS `Secure`, `SameSite` browser cookie
3. `proxy.ts` client-side checks if `session_id` exists cookie)
4. Client-side page calls server-side to check if session is valid
5. Server side reads `access_token`

Server-side implements
- `/api/login`: sets local cookies
- `/api/callback`: IdP redirect
- `/api/logout`: clears local cookies
- `/api/refresh`: refresh token flow
- `/api/session`: calls the backend /api/session

To validate a session:
```ts
// src/proxy.ts
import { NextRequest, NextResponse } from 'next/server';

export function proxy(request: NextRequest) {
  const session = request.cookies.get('session_id')?.value;
  if (!session) {
    const loginUrl = new URL('/api/login', request.url);
    loginUrl.searchParams.set('next', pathname);
    return NextResponse.redirect(loginUrl);
  }
  return NextResponse.next();
}

// getServerSideProps (server-side), or fetch()
// this is called by the page itself to ensure the cookie is valid
const response = await fetch('https://api.internal/me', {
  headers: {
    Cookie: `access_token=${cookies().get('access_token')}`,
  },
});
```


### FastAPI Backend

- Receives `Authorization: Bearer <access_token_jwt>`
- Validate JWT with IdP JWKS
- implements `/api/session` and `/api/me`

```python
# jwks_client.py

@lru_cache(maxsize=1)
def get_jwks() -> Dict[str, Any]:
    # e.g. https://idp/.well-known/jwks.json
    resp = httpx.get(str(settings.oidc_jwks_url), timeout=5.0)
    resp.raise_for_status()
    return resp.json()

def decode(token: str) -> Dict[str, Any]
	client = get_jwtks_client()
	try:
		signing_key = client.get_signing_key_from_jwt(token)
		claims = jwt.decode(token, ...)
		return claims
	except: ...

def get_current_user() -> dict[str, Any]:
	... # return user id
    
# routes.py
`@app.get("/orders") async def list_orders(current_user: Dict[str, Any] = Depends(get_current_user)):
	user_id = current_user["sub"]
	return True
```