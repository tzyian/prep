# RSA (Rivest–Shamir–Adleman)

RSA is an **asymmetric cryptosystem**:
- Uses a **public key** for encryption (or signature verification).
- Uses a **private key** for decryption (or signing).

---

## 1. Key Generation
1. Choose two large prime numbers: p, q.
2. Compute modulus:  
   n = p * q
3. Compute Euler’s totient:  
   φ(n) = (p - 1) * (q - 1)
4. Choose public exponent e such that:  
   1 < e < φ(n) and gcd(e, φ(n)) = 1  
   (Common choice: e = 65537).
5. Compute private exponent d:  
   d ≡ e⁻¹ (mod φ(n))  
   (i.e., modular multiplicative inverse).

- Public key = (e, n)  
- Private key = (d, n)

---

## 2. Encryption (Confidentiality)
To send a message M (integer, M < n):

Ciphertext C = M^e mod n  

Receiver decrypts with private key:

Plaintext M = C^d mod n

---

## 3. Digital Signatures (Integrity & Authentication)
- Sender signs message M with private key:  
  Signature S = H(M)^d mod n  
  (where H(M) is a hash of the message).
- Receiver verifies with public key:  
  H(M) == S^e mod n

---

## 4. Why RSA Works
- Based on the **mathematical property**:  
  (M^e)^d ≡ M (mod n)  
  because ed ≡ 1 (mod φ(n)).
- Security relies on the **difficulty of factoring large n** into p and q.

---

## 5. Example (Small Numbers)
1. p = 61, q = 53 → n = 3233  
2. φ(n) = (61-1)(53-1) = 3120  
3. e = 17 (valid since gcd(17,3120)=1)  
4. d = 2753 (since 17*2753 ≡ 1 mod 3120)  

- Public key = (17, 3233)  
- Private key = (2753, 3233)  

Encrypt M=65:  
C = 65^17 mod 3233 = 2790  

Decrypt:  
M = 2790^2753 mod 3233 = 65

---

## 6. Limitations
- Slow for large data → usually used only for **key exchange** or **signatures**.
- Vulnerable if:
  - n is too small,
  - p and q are poorly chosen,
  - padding not used (e.g., textbook RSA).

Modern usage: **RSA-OAEP** (encryption) and **RSA-PSS** (signatures).
