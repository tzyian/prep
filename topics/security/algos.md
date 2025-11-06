
## 4. RSA, SHA, AES
| Algorithm | Type                        | Purpose                                 | Where Used                        | Notes                                                          |
| --------- | --------------------------- | --------------------------------------- | --------------------------------- | -------------------------------------------------------------- |
| **RSA**   | Asymmetric (public/private) | Encryption, **Digital Signatures**      | JWT (RS256),<br><br>digital certs | Strong but slower at large key sizes; 2048-bit common baseline |
| **ECDHE** | Asymmetric (elliptic curve) | **Key Exchange** (derive shared secret) | TLS handshakes                    | Ephemeral = PFS (Perfect Forward Secrecy); not for signatures  |
| AES       | Symmetric                   | Encryption                              | Shared key after TLS              |                                                                |

# Hash vs Digest vs Signature vs Salt

|Term|What it is|Purpose|Example|
|---|---|---|---|
|**Hash**|One-way function: input → fixed-size output|Integrity check, data fingerprint|`SHA-256("hello") = 2cf24d...`|
|**Digest**|The **output** of a hash function|The actual hash value|`2cf24dba5...` is the digest of `"hello"`|
|**Signature**|Cryptographic proof created with a **private key** over a message (usually its hash)|Authenticity + integrity + non-repudiation|`RSA_sign( SHA256(payload), private_key )`|
|**Salt**|Random value added before hashing|Prevents pre-computed attacks (rainbow tables)|`hash(password + salt)` for password storage|

#### **ECDHE** = **Elliptic Curve Diffie–Hellman Ephemeral**. 

For **sharing** a secret  without sharing the secret

1. Client and server agree on DH parameters (prime `p`, generator `g` or curve).
2. Client picks random secret `a`, sends `A = g^a mod p`.
3. Server picks random secret `b`, sends `B = g^b mod p`.
4. Both compute shared
	- Client: `K = B^a mod p`.
	- Server: `K = A^b mod p`.
	- Both get the same `K`.

### Bcrypt

Generate random salt per password

`hash(password + salt)`

The salt is stored in cleartext

$2b \$12\$ N9qo8uLOickgx2ZMRZoMye IjZAg9x36PgojO6aG9a8pB9p46iXWZS
- `$2b$` → bcrypt version
- `12$` → cost factor (2^12 iterations)
- `N9qo8uLOickgx2ZMRZoMye` → salt in cleartext (22 chars base64)
- `IjZAg9x36PgojO6aG9a8pB9p46iXWZS` → hash(password + salt)