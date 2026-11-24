# OWASP Juice Shop – Security Assessment Report

**Target:** OWASP Juice Shop (`http://localhost:3000`)  
**Environment:** Windows – Local Lab  
**Tools:** Burp Suite Community Edition, Nmap, Browser Developer Tools

## 1. SQL Injection – Authentication Bypass

**Endpoint:** `POST /rest/user/login`

**Test performed:**
- Intercepted the login request using Burp Suite.
- Sent the request to Repeater.
- Modified the email parameter with the payload:

```sql
' OR 1=1--
```

**Result:** The application returned an authentication token and identified the account as `admin@juice.shop`, despite the supplied credentials being invalid.

**Remediation:** Use parameterized SQL queries instead of dynamically concatenating user input into SQL statements.

## 2. IDOR – Unauthorized Basket Access

**Endpoint:** `GET /rest/basket/{id}`

**Test performed:**
- Accessed my own basket using basket ID `6`.
- Changed the ID to `1` in Burp Repeater.
- Submitted the modified request.

**Result:** The server returned another user's basket information without authorization.

An additional attempt to modify another user's basket was rejected with an invalid basket ID response.

**Remediation:** Implement server-side ownership and authorization checks for every basket request.

## 3. Cross-Site Scripting (XSS)

**Test location:** Juice Shop search interface

**Payload used:**

```html
<iframe src="javascript:alert('XSS')"></iframe>
```

**Result:** The browser successfully executed the injected JavaScript and displayed an alert containing `XSS`.

The exact XSS subtype was not established.

**Remediation:** Apply context-aware output encoding, sanitize untrusted HTML and implement Content Security Policy.

## 4. JWT and Cookie Security Analysis

**Tests performed:**
- Inspected the authentication response and extracted the JWT.
- Decoded the JWT header and identified the `RS256` signing algorithm.
- Observed the same token in the Authorization header and browser cookie.
- Tested authentication with the cookie and Authorization header separately.
- Inspected the cookie using Browser Developer Tools and `document.cookie`.

**Results:**
- The cookie alone was sufficient to identify the authenticated user.
- The Authorization header alone did not authenticate the inspected endpoint.
- The token was accessible through JavaScript, indicating that the HttpOnly attribute was absent.

**Remediation:** Use HttpOnly cookies and configure appropriate Secure and SameSite attributes.

## 5. HTTP Security Headers and CORS

Inspected HTTP responses through Burp Suite.

| Header | Observed result |
|---|---|
| Access-Control-Allow-Origin | `*` |
| X-Content-Type-Options | `nosniff` |
| X-Frame-Options | `SAMEORIGIN` |
| Content-Security-Policy | Not present in inspected response |
| Strict-Transport-Security | Not present in inspected HTTP response |

**Observation:** The wildcard CORS policy was identified but not demonstrated to be exploitable. Missing headers were recorded as security hardening observations.

## 6. Network Enumeration – Nmap

**Commands executed:**

```bash
nmap -sV -p 3000 localhost
```

```bash
nmap -p- localhost
```

**Results:**

- Confirmed that the local Juice Shop application was accessible on TCP port 3000.
- Identified open ports including 135, 445, 8080 and 8834.
- Identified services associated with Windows RPC, SMB, the Burp proxy and Nessus.
- Observed additional open ports that Nmap could not identify.

**Objective:** Understand local listening services, TCP port states and service identification.

## 7. Conclusion

This assessment provided practical experience in HTTP traffic interception, authentication testing, SQL Injection, IDOR, XSS, JWT and cookie inspection, HTTP security header analysis and TCP port enumeration.

The two most significant demonstrated vulnerabilities were SQL Injection authentication bypass and unauthorized access to another user's basket.

All testing was conducted against a locally hosted OWASP Juice Shop instance.