Perfect question — this shows you’re thinking about **where trust and security actually live** in the OAuth2 / OIDC model.  
Let’s unpack it carefully.

---

## 🧩 1️⃣ Who “owns” the login page

- The **login page** is hosted by the **Authorization Server (IdP)** — e.g., Keycloak, Google, Okta.
    
- When the browser is redirected there (`https://auth.example.com/login`),  
    you’re now directly communicating with the **auth server**, _not_ the client app.
    
- The login form, cookies, and credentials belong entirely to that domain.
    

So your password is entered into **auth.example.com**, not **app.example.com**.

---

## 🔒 2️⃣ Why That’s Secure

Because all communication between your browser and the Authorization Server is done over **HTTPS** (TLS).

TLS ensures:

1. **Encryption in transit** — nobody can read your credentials in the network.
    
2. **Server authenticity** — the auth server presents a valid certificate signed by a trusted CA.
    
    - The browser checks that the certificate’s domain matches `auth.example.com`.
        
    - The certificate’s signature is verified using public keys from your OS/browser’s trusted root store.
        
3. **Integrity** — the content of the login page cannot be modified in transit.
    

This is what makes the **auth server’s login page in your browser secure**.

---

## 🧱 3️⃣ Why It’s Better Than Client-Hosted Login

If the **client app** hosted the login page:

- The app would need to handle passwords directly → risk of compromise, phishing, or misconfiguration.
    
- Every third-party app would have to store and verify credentials.
    

OAuth centralizes trust:

- Only the **Authorization Server** handles credentials.
    
- The **client app** never sees the password — it only gets tokens _after_ login succeeds.
    

---

## 🧠 4️⃣ How Browser Knows It’s the “Real” Auth Server

Through **PKI (Public Key Infrastructure):**

- The auth server’s TLS certificate (from a trusted CA like DigiCert or Let’s Encrypt) includes:
    
    - The domain name (`auth.example.com`)
        
    - The public key
        
    - A signature from the CA
        

When your browser connects:

1. The server sends its certificate.
    
2. The browser checks:
    
    - CA signature chain → trusted?
        
    - Domain → matches URL?
        
    - Expiry → still valid?
        
3. If all pass, browser establishes an encrypted TLS session.
    

That’s why even though the page is in your browser, it’s secure — your browser only trusts it because the certificate checks out.

---

## 🧭 5️⃣ Extra Protection Layers

Modern IdPs like Keycloak also use:

- **SameSite cookies** → prevent cross-site request forgery.
    
- **Secure cookies** → only sent over HTTPS.
    
- **Content Security Policy** → block injection attacks.
    
- **Login forms served from fixed domains** → you can verify the URL before typing credentials.
    

If an attacker tried to spoof the login page from another domain, your browser would:

- Show a different URL,
    
- Fail TLS validation,
    
- Or display “Not Secure”.

|Step|Why Secure|
|---|---|
|Browser → Auth Server|HTTPS/TLS encrypts credentials and verifies domain identity|
|Login Form Ownership|Hosted by the Authorization Server (not the client app)|
|Browser Validation|PKI chain ensures real `auth.example.com` certificate|
|Client Separation|Client app never touches username/password|
|Additional Controls|Secure cookies, CSP, anti-CSRF, SameSite, etc.|

So in short:

> The login page is secure in your browser **because of TLS + PKI**,  
> and it’s trusted because the **browser verifies the Authorization Server’s certificate**,  
> not because of anything the client app does.