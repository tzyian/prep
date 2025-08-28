# Content Delivery Network (CDN)

## What is a CDN?
A **Content Delivery Network (CDN)** is a geographically distributed network of proxy servers and data centers that deliver content (e.g., web pages, videos, images, scripts) to users with high availability and performance.  
It reduces latency by serving content from a location closer to the user instead of always from the origin server.

---

## How it Works
1. **User requests content** (e.g., `image.jpg` from `example.com`).
2. DNS routing sends the request to the nearest CDN edge server.
3. If the edge server has the content cached:
   - It serves it directly (fast, local response).
4. If not cached:
   - It fetches from the origin server, caches it, and then serves it to the user.

---

## Benefits
- **Reduced Latency**: Content served from nearby edge servers.
- **Improved Availability**: Load is distributed across multiple servers.
- **Scalability**: Handles large spikes in traffic (e.g., product launches, live events).
- **DDoS Protection**: Absorbs and mitigates traffic surges.
- **Bandwidth Savings**: Offloads traffic from the origin server.
- **Content Optimization**: Compression, image optimization, TLS termination.

---

## Examples of CDN Providers
- **Commercial**: Cloudflare, Akamai, AWS CloudFront, Google Cloud CDN, Fastly.
- **Open Source / Self-Hosted**: Varnish, Nginx-based caching, Apache Traffic Server.

---

## Typical Use Cases
- **Websites & Web Apps**: Faster page load times.
- **Video Streaming**: Deliver smooth playback globally.
- **Software Distribution**: Large file downloads (games, updates).
- **APIs**: Reduce latency for global users.
