
## JWT
(asymmetric)
- `header.payload.signature`
- JWT signature created by auth server
	- `signature = RS256_sign( SHA256( header + "." + payload ), private_key )
- `PK = (n, e)`
- Auth server computes digest and signature
	- `h = SHA256(header || "." || payload)`
	- `s = m^d mod n`
- Resource server takes digest `h` and signature `s`. If `PK = (n, e)`
	- `m' = s^e mod n`


The JWT signature checks the integrity of the header + payload
The JWKS ensures signature is indeed issued by IdP
## JSON Web Key Set (JWKS)
- public keys published by **authorization server**
- signed by authorization server's private key
- resource servers verify JWT with authorization server JWKS public key

```json
{ // JWT header
  "alg": "RS256",  // HS256 for HMAC, RS256 for RSA, ES256 for ECDSA
  "typ": "JWT",    // type
  "kid": "abc123"  // key id
}
```
API takes JWKS public key and runs JWT `algo` to see if the JWT signature is authentic (validity that it is issued by the auth provider)

```json
{ // JWKS
  "keys": [
    {
	  "kty": "RSA",     // key type: RSA, EC, oct
      "kid": "abc123",  // key ID *must* match header `kid`
      "use": "sig",     // sig (signature) or enc (encryption)
      "n": "...",       // RSA modulus
      "e": "AQAB"       // RSA public exponent
      // other key types have other fields
    }
  ]
}
```

