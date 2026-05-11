## Web Application Basics — Deep Dive Guide

### 1. HTTP Fundamentals

> #### [ x ] HTTP Status Codes

HTTP status codes are three-digit numbers returned by a server to indicate the result of a client’s request. They are grouped into five categories:
1. informational (1xx)
2. success (2xx)
3. redirection (3xx) 
4. client error (4xx)
5. server error (5xx)

Each code helps developers and penetration testers quickly understand the outcome of a request.

As a penetration tester, understanding status codes is critical:
- 2xx codes confirm successful exploitation (e.g., SQL injection returning data).
- 3xx status codes can reveal redirection logic if they are not properly configured, which can lead to Host Header Injection or Open Redirection vulnerabilities.
- 4xx codes help identify authentication/authorization flaws (e.g., 401 vs 403).
- 5xx codes often indicate server misconfigurations or unhandled exceptions exploitable for information disclosure.

> #### [ x ] HTTP Methods


| **[Method](ca://s?q=Explain_HTTP_methods)** | **Purpose** | **Idempotent** | **Cacheable** | **Example Use Case** |
| --- | --- | --- | --- | --- | 
| **[GET](ca://s?q=Explain_GET_HTTP_method)** | Retrieve data from server | ✅ | ✅ | Fetch a webpage or API data |
| **[POST](ca://s?q=Explain_POST_HTTP_method)** | Submit data to server, create resource | ❌ | Sometimes | Submit a form, upload file |
| **[PUT](ca://s?q=Explain_PUT_HTTP_method)** | Replace an existing resource entirely (or create if not exists) | ✅ | ❌ | Updating a user profile with all fields |
| **[PATCH](ca://s?q=Explain_PATCH_HTTP_method)** | Partially update an existing resource | ❌ | Sometimes | Update only email field in profile |
| **[OPTIONS](ca://s?q=Explain_OPTIONS_HTTP_method)** | Query supported methods | ✅ | ❌ |  Browsers automatically send an OPTIONS request before certain cross-origin requests (like POST, PUT, or DELETE). |

**#Idempotent**
- An operation that produces the same result no matter how many times it’s executed.
- Example: PUT /users/123 {name: "Alice"} → whether you send it once or ten times, user 123’s name will be “Alice.”

**#Non-Idempotent**
- Each execution may produce a different result.
- Example: POST /orders → each request creates a new order, so repeating it generates multiple orders.

**Idempotent methods (like PUT)** → attackers may exploit them for overwriting data, but repeated requests don’t multiply the damage.
**Non-idempotent methods (like POST)** → attackers can abuse them for replay attacks, creating duplicate transactions or orders.

**#Cacheable Methods**
- GET and HEAD are inherently cacheable.
  > Most commonly cached by proxies, CDNs, and browsers.
  > Vulnerabilities like Web Cache Poisoning and Web Cache Deception usually exploit GET requests because caches store their responses.
- POST and PATCH are not cacheable by default, but can be if explicitly configured with headers.
  > Typically not cached because POST is meant for state-changing operations.
  > However, misconfigured caches or CDNs may mistakenly cache POST responses, creating rare but dangerous poisoning vectors.
- PUT and DELETE are generally not cacheable.

Because caches store responses to cacheable methods, these are the ones most relevant to cache-related vulnerabilities.
<details>
<summary>Click to expand</summary>

**⚠️ Web Cache Poisoning**
**Definition:** An attacker tricks the cache into storing a malicious response.

**Example:**
- Attacker sends GET /home?foo=bar with a malicious header.
- Server reflects the header in the response.
- Cache stores this poisoned response.
- Other users visiting /home get the attacker’s injected payload.

👉 Cacheability is the direct enabler here — if the method wasn’t cacheable, the poisoned response wouldn’t persist.

**⚠️ Web Cache Deception**
**Definition:** Attacker tricks the cache into storing sensitive content that should never be cached.

**Example:**
- Victim visits GET /profile.php/fake.css.
- Server still returns sensitive profile data.
- Cache mistakenly stores it as a static resource.
- Attacker later requests /profile.php/fake.css and gets cached private data.

👉 Again, cacheability of GET responses is the root cause.

</details>

**#Mitigations**
- Set proper headers: Use Cache-Control, Vary, and Content-Type correctly.

> #### [ x ] HTTP Versions

HTTP/1.1
**Features:**
- Persistent Connections: Multiple requests/responses can be sent over a single TCP connection, reducing overhead `(keep-alive)`.
- Chunked Transfer Encoding: Allows dynamic content delivery without knowing the full size beforehand.
- Pipelining: Clients can send multiple requests without waiting for responses, improving efficiency.
- Caching Improvements: Introduced cache-control headers for better performance.

**Vulnerabilities:** Request smuggling, cache poisoning, and slowloris DoS attacks.

HTTP/2

HTTP/3


> #### [ x ] Vulnerabiliites

<details>
<summary>Click to expand</summary>
    
1. Application Running On Insecure Channel HTTP

    E.g.: http://example.com/login

2. Data Transfer in Plan text format ( Sensitive Data in GET Parameters )

    E.g.: https://example.com/login?password=secret123

3. Server Version Disclosure

    E.g.: Server: Apache/2.4.29 (Ubuntu)

6. HTTP Downgrade / Mixed Content

    E.g.: A site is served over HTTPS, but an attacker forces it to run over HTTP.

7. Vulnerable and Outdated TLS/SSL Version

   E.g: 1.0 TLS Version
</details>



