# OWASP Juice Shop – Web Application Security Assessment

## Project Overview

Conducted a hands-on security assessment of OWASP Juice Shop, an intentionally vulnerable web application, to identify and validate common web application security vulnerabilities.

**Environment:** Windows (local lab)  
**Tools:** Burp Suite Community Edition, Nmap, Browser Developer Tools  
**Methodology:** Manual HTTP analysis, vulnerability testing and network enumeration

## Scope of Assessment

- Authentication and session management
- SQL Injection
- Broken Access Control (IDOR)
- Cross-Site Scripting (XSS)
- JWT and cookie security
- HTTP security headers and CORS
- TCP port enumeration

## Key Findings

### 1. SQL Injection – Authentication Bypass
**Endpoint:** `POST /rest/user/login`

- Manipulated the email parameter using `' OR 1=1--`.
- Successfully bypassed authentication and obtained an administrator identity.
- Demonstrated the risk of dynamically constructed SQL queries.

**Remediation:** Use parameterized queries, secure database access and least-privilege permissions.

### 2. Insecure Direct Object Reference (IDOR)
**Endpoint:** `GET /rest/basket/{id}`

- Modified the basket ID in an authenticated request.
- Successfully accessed another user's basket without authorization.
- Confirmed unauthorized data access; modification was not demonstrated.

**Remediation:** Enforce server-side object ownership and authorization checks.

### 3. Cross-Site Scripting (XSS)
**Test location:** Search input

- Injected an iframe-based JavaScript payload.
- Successfully triggered JavaScript execution in the browser.
- Exact XSS subtype was not conclusively established.

**Remediation:** Apply context-aware output encoding, input sanitization and Content Security Policy.

### 4. JWT and Session Security
- Inspected JWT structure and identified the RS256 signing algorithm.
- Examined authentication headers and cookie-based session behaviour.
- Confirmed that the authentication token was accessible through JavaScript, indicating the absence of the HttpOnly attribute on the observed cookie.

**Remediation:** Configure HttpOnly, Secure and appropriate SameSite cookie attributes, alongside proper token lifecycle management.

### 5. HTTP Security Headers and CORS
Inspected application responses and identified:

- `X-Content-Type-Options: nosniff` – Present
- `X-Frame-Options: SAMEORIGIN` – Present
- Content-Security-Policy – Absent in the inspected response
- Strict-Transport-Security – Absent in the inspected HTTP response
- `Access-Control-Allow-Origin: *` – Observed

**Remediation:** Implement appropriate security headers and restrict CORS origins where required.

*Note: The observed wildcard CORS policy was not independently validated as an exploitable vulnerability.*

### 6. Network Enumeration
**Tool:** Nmap

- Performed a full TCP port scan of the local Windows machine.
- Identified open services, including SMB, MSRPC, the local Juice Shop application and Burp proxy.
- Examined service identification and the distinction between listening ports and outgoing connections.

## Key Learning Outcomes

- Practical HTTP request interception and manipulation using Burp Suite.
- Understanding and reproducing SQL injection and access control vulnerabilities.
- Authentication, JWT and cookie security analysis.
- Identifying browser security configuration weaknesses.
- TCP port enumeration and service identification using Nmap.
- Documenting security findings with evidence, impact and remediation.

## Ethical Scope

All testing was conducted in an isolated local lab against OWASP Juice Shop. No third-party systems or real user accounts were targeted.