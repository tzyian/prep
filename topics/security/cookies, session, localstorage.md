
Adapted from
https://www.greatfrontend.com/questions/quiz/describe-the-difference-between-a-cookie-sessionstorage-and-localstorage


- **Cookies**: Suitable for server-client communication, small storage capacity, can be persistent or session-based, domain-specific. Sent to the server on every request.
- **`localStorage`**: Suitable for long-term storage, data persists even after the browser is closed, accessible across all tabs and windows of the same origin, highest storage capacity among the three.
- **`sessionStorage`**: Suitable for temporary data within a single page session, data is cleared when the tab or window is closed, has a higher storage capacity compared to cookies.


| Property                               | Cookie                                               | `localStorage`      | `sessionStorage`    |
| -------------------------------------- | ---------------------------------------------------- | ------------------- | ------------------- |
| Initiator                              | Client or server. Server can use `Set-Cookie` header | Client              | Client              |
| Lifespan                               | As specified                                         | Until deleted       | Until tab is closed |
| Persistent across browser sessions     | If a future expiry date is set                       | Yes                 | No                  |
| Sent to server with every HTTP request | Yes, sent via `Cookie` header                        | No                  | No                  |
| Total capacity (per domain)            | 4kb                                                  | 5MB                 | 5MB                 |
| Access                                 | Across windows/tabs                                  | Across windows/tabs | Same tab            |
| Security                               | JavaScript cannot access `HttpOnly` cookies          | None                | None                |

## Cookie Attributes

Here are the main cookie flags and what they do:

- `HttpOnly`: prevents JS from reading the cookie through browser APIs, which helps reduce token leakage during XSS incidents.
- `Secure`: send cookie only over HTTPS
- `SameSite`: 
	- `SameSite=strict`: sends the cookie only for same-site requests, giving the strongest CSRF protection but sometimes breaking login or cross-site flows.
	- `SameSite=Lax`: (default) allows the cookie on top-level navigations like clicking a link, but not on most other cross-site subresource requests
	- `SameSite=None`: allows cross-site sending, which is needed for some third-party cookies, embedded content, or SSO flows, but it must be paired with `Secure`.
- `Expires` / `Max-Age`: self-explanatory
- `Domain`: limits which hostnames can receive the cookie, such as a parent domain or specific subdomain
- `Path`: limits which URL paths on the site receive the cookie.
    

A typical secure auth cookie often looks like: `Set-Cookie: sid=abc123; HttpOnly; Secure; SameSite=Lax`. Lax because login forms can redirect to another domain. For third-party or cross-site SSO behavior, you may need `SameSite=None; Secure` instead.