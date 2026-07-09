# Security Testing Report — Task 3

**Target Application:** DVWA (Damn Vulnerable Web Application)  
**Tester:** PRANESH M S  
**Date:** 09 July 2026  
**Offer Letter ID:** APSPL2633989  
**Internship:** ApexPlanet Cybersecurity & Ethical Hacking

---

## Executive Summary

| Metric | Value |
|--------|-------|
| Application | DVWA on localhost |
| Tests Performed | SQL Injection, XSS (Stored/Reflected), CSRF, File Inclusion |
| Tools Used | Burp Suite, Browser, DVWA |
| Critical Findings | 2 |
| High Findings | 3 |
| Medium Findings | 2 |
| Overall Risk | **Critical** |

---

## 1. SQL Injection

### Finding

| Field | Detail |
|-------|--------|
| Severity | Critical |
| Location | `/vulnerabilities/sqli/?id=` |
| Type | Union-based SQL Injection |
| Parameter | `id` (GET) |

### Attack

```
?id=0' UNION SELECT 1, user, password FROM users -- &Submit=Submit
```

### Result

Successfully extracted all user credentials from the database:

| Username | Password Hash |
|----------|--------------|
| admin | 21232f297a57a5a743894a0e4a801fc3 |
| gordonb | e99a18c428cb38d5f260853678922e03 |
| 1337 | 8d3533d75ae2c3966d7e0d4fcc69216b |
| pablo | 0d107d09f5bbe40cade3de5c71e9e9b7 |
| smithy | 5f4dcc3b5aa765d61d8327deb882cf99 |

### Mitigation

Use prepared statements with parameterized queries. See [scenarios/sqli-attack-mitigation.md](../scenarios/sqli-attack-mitigation.md).

---

## 2. Stored Cross-Site Scripting (XSS)

### Finding

| Field | Detail |
|-------|--------|
| Severity | High |
| Location | `/vulnerabilities/xss_s/` |
| Type | Stored XSS |
| Parameter | `txtName` (POST) |

### Attack

```html
<script>alert('Stored XSS')</script>
```

### Result

Script persisted in the guestbook and executed for all visitors.

### Mitigation

Input sanitization with `strip_tags()`, output encoding with `htmlspecialchars()`, and Content Security Policy. See [scenarios/xss-attack-mitigation.md](../scenarios/xss-attack-mitigation.md).

---

## 3. Reflected Cross-Site Scripting (XSS)

### Finding

| Field | Detail |
|-------|--------|
| Severity | High |
| Location | `/vulnerabilities/xss_r/` |
| Type | Reflected XSS |
| Parameter | `name` (GET) |

### Attack

```
?name=<script>alert('Reflected XSS')</script>
```

### Result

Script reflected in page response and executed in the browser.

### Mitigation

Input validation (whitelist), output encoding, and CSP header. See [scenarios/xss-attack-mitigation.md](../scenarios/xss-attack-mitigation.md).

---

## 4. Cross-Site Request Forgery (CSRF)

### Finding

| Field | Detail |
|-------|--------|
| Severity | High |
| Location | `/vulnerabilities/csrf/` |
| Type | CSRF — password change |
| Method | GET (no token required) |

### Attack

```html
<img src="http://localhost/DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change">
```

### Result

Admin password changed without consent when visiting attacker's page.

### Mitigation

CSRF tokens, SameSite cookies, and re-authentication for sensitive actions. See [scenarios/csrf-attack-mitigation.md](../scenarios/csrf-attack-mitigation.md).

---

## 5. File Inclusion

### Finding

| Field | Detail |
|-------|--------|
| Severity | Critical |
| Location | `/vulnerabilities/fi/` |
| Type | Local File Inclusion |
| Parameter | `page` (GET) |

### Attack

```
?page=../../../../etc/passwd
```

### Result

Contents of `/etc/passwd` displayed, revealing system user accounts.

### Mitigation

Whitelist allowed pages, disable `allow_url_include`, use absolute paths.

---

## 6. Security Headers Analysis

### securityheaders.com Results (Before)

| Header | Status |
|--------|--------|
| Strict-Transport-Security | Missing |
| Content-Security-Policy | Missing |
| X-Frame-Options | Missing |
| X-Content-Type-Options | Missing |
| Referrer-Policy | Missing |

**Grade: F**

### Recommended Apache Configuration

```apache
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-Content-Type-Options "nosniff"
Header always set Content-Security-Policy "default-src 'self'; script-src 'self'"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
```

---

## Findings Summary

| # | Vulnerability | Severity | Status |
|---|--------------|----------|--------|
| 1 | SQL Injection | Critical | Documented with fix |
| 2 | Stored XSS | High | Documented with fix |
| 3 | Reflected XSS | High | Documented with fix |
| 4 | CSRF | High | Documented with fix |
| 5 | Local File Inclusion | Critical | Documented with fix |
| 6 | Missing Security Headers | Medium | Configuration provided |

---

## Recommendations

1. **Immediate:** Implement prepared statements for all database queries
2. **High:** Add CSRF tokens to all state-changing forms
3. **High:** Enable Content Security Policy and output encoding
4. **Medium:** Configure security headers in Apache
5. **Ongoing:** Regular security testing with Burp Suite and automated scanners

---

## Conclusion

The DVWA application demonstrates critical web vulnerabilities that are common in real-world applications. Each vulnerability has a documented attack scenario and a corresponding mitigation strategy. Implementing the recommended fixes would significantly improve the application's security posture.

This report was prepared as part of Task 3 of the ApexPlanet Cybersecurity & Ethical Hacking Internship.
