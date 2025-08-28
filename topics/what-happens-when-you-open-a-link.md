**![[media_FmXEahEXEAEBd_Z.jpg]]**
## 1. **Parsing the URL**
- The browser breaks down the URL into components:
    - **Scheme/protocol** → e.g., `http://` or `https://`
    - **Domain name / Hostname** → e.g., `www.example.com`
    - **Port** (if specified, otherwise defaults: `80` for HTTP, `443` for HTTPS)
    - **Path** → e.g., `/about/index.html`
    - **Query parameters** → e.g., `?id=123`
    - **Fragment/anchor** → e.g., `#section2` (handled locally by the browser, not sent to the server).

---
## 2. **Browser Checks Local Data Before Network**
- **Browser cache** → Has the browser already stored the resource (HTML, CSS, JS, images)?
- **OS-level DNS cache** → Has the system recently resolved this domain?
- **Hosts file** → OS may map domains to IPs here (manual overrides).
- If still unresolved → it moves on to DNS resolution.

---
## 3. **DNS Resolution**
If the IP address is not cached, the browser performs a **recursive lookup**:
1. Ask the **local DNS resolver** (usually provided by ISP, router, or set to a custom DNS like Google 8.8.8.8).
2. If not found, queries go up the hierarchy:
    - **Root DNS servers** → direct query to the right TLD (e.g., `.com`).
    - **TLD DNS servers** → direct query to the authoritative server for `example.com`.
    - **Authoritative DNS server** → returns the IP address of the web server.
3. IP address returned → stored in DNS cache for subsequent requests.

---

## 4. **Establishing a TCP Connection**
- Using the resolved IP, the browser initiates a **TCP 3-way handshake**:
    - SYN → client to server
    - SYN-ACK → server to client
    - ACK → client to server
- At this point, a reliable channel is established over TCP (usually on port 80 or 443).

---
## 5. **TLS/SSL Handshake (HTTPS only)**
- If HTTPS is used:
    1. **Client Hello** → browser proposes encryption protocols.
    2. **Server Hello** → server chooses and sends SSL certificate.
    3. **Certificate Validation** → browser verifies authenticity with Certificate Authority (CA).
    4. **Key Exchange** → secure session key generated.
    5. Both sides confirm, and all subsequent communication is encrypted.

---
## 6. **Sending the HTTP Request**
- The browser formats and sends a request (usually HTTP/1.1, HTTP/2, or HTTP/3):
- Includes:
    - **Method** (GET, POST, etc.)
    - **Headers** (cookies, accepted formats, compression support, etc.)
- **Body** (for POST/PUT requests).

---
## 7. **Server Processing**
- The web server (e.g., Nginx, Apache, Node.js) receives the request.
- Possible steps:
    - Passes request to **application server** (e.g., Django, Spring Boot, Express).
    - Executes **business logic** and possibly queries a **database**.
    - Generates the response (HTML, JSON, etc.).

---
## 8. **Server Response**
- The server returns an **HTTP response**:k
```
HTTP/1.1 200 OK
Content-Length: 3056
```
Response contains:
Status line (e.g., 200 OK, 404 Not Found, 301 Redirect).
Headers (content type, cache control, cookies, etc.).
Body (HTML, JSON, CSS, JS, images, etc.).

---
## 9. **Browser Rendering Pipeline**
Once the response is received, the browser begins **rendering the page**:
1. **HTML Parsing** → Builds the **DOM tree**.
2. **CSS Parsing** → Builds the **CSSOM tree**.
3. **DOM + CSSOM → Render Tree**.
4. **Layout** → Calculates positions and sizes of elements.
5. **Painting** → Fills in colors, borders, text, etc.
6. **Compositing** → Layers are combined and displayed on the screen.

---

## 10. **Additional Requests**
- As the HTML is parsed:
    - If `<img src="...">`, `<link rel="stylesheet" href="...">`, or `<script src="...">` are found → browser issues additional requests.
    - These may go through the same **DNS + TCP + TLS** steps (unless cached or same connection can be reused).
- **Critical Rendering Path optimization**: Browsers often prioritize CSS and JS needed to render visible content quickly.

---
## 11. **JavaScript Execution**
- Browser’s JS engine (e.g., V8 in Chrome, SpiderMonkey in Firefox):
    - Executes scripts.
    - Modifies DOM (e.g., via `document.getElementById`).
    - May make **AJAX/fetch calls** to load more data without reloading.
    - May delay rendering if scripts are blocking.

---

## 12. **Caching and Reuse**
- Browser may store:
    - **DNS results** → future lookups faster.
    - **Static assets** (CSS, JS, images) in **HTTP cache** based on headers (`Cache-Control`, `ETag`, `Last-Modified`).
    - **Persistent data** in cookies, localStorage, IndexedDB.
- On reload or revisit, the browser may reuse cached data instead of re-downloading.
