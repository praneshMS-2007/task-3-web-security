# Web Security Headers

HTTP security headers protect web applications from common attacks by instructing browsers how to behave.

---

## Analyzing with securityheaders.com

1. Visit https://securityheaders.com
2. Enter target URL
3. Review the security grade and missing headers
4. Document findings for the report

### Example Analysis Result

| Header | Status | Grade Impact |
|--------|--------|-------------|
| Strict-Transport-Security | Missing | F |
| Content-Security-Policy | Missing | F |
| X-Frame-Options | Missing | F |
| X-Content-Type-Options | Missing | F |
| Referrer-Policy | Missing | F |
| Permissions-Policy | Missing | F |

---

## Essential Security Headers

### Strict-Transport-Security (HSTS)

Forces browsers to use HTTPS only.

```apache
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
```

### Content-Security-Policy (CSP)

Controls which resources the browser can load — primary XSS defense.

```apache
Header set Content-Security-Policy "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; frame-ancestors 'none'"
```

### X-Frame-Options

Prevents clickjacking by blocking iframe embedding.

```apache
Header always set X-Frame-Options "SAMEORIGIN"
```

### X-Content-Type-Options

Prevents MIME type sniffing.

```apache
Header always set X-Content-Type-Options "nosniff"
```

### Referrer-Policy

Controls how much referrer information is sent.

```apache
Header always set Referrer-Policy "strict-origin-when-cross-origin"
```

### Permissions-Policy

Restricts browser features (camera, microphone, geolocation).

```apache
Header always set Permissions-Policy "camera=(), microphone=(), geolocation=()"
```

---

## Apache Configuration

### Enable mod_headers

```bash
sudo a2enmod headers
sudo systemctl restart apache2
```

### Full Security Headers Config

Create or edit `/etc/apache2/conf-available/security-headers.conf`:

```apache
<IfModule mod_headers.c>
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"
    Header always set Referrer-Policy "strict-origin-when-cross-origin"
    Header always set Permissions-Policy "camera=(), microphone=(), geolocation=()"
    Header always set Content-Security-Policy "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'"
    Header always unset X-Powered-By
    Header always unset Server
</IfModule>
```

Enable the configuration:

```bash
sudo a2enconf security-headers
sudo systemctl restart apache2
```

### Verify Headers

```bash
curl -I http://localhost/DVWA/
```

---

## Header Purpose Summary

| Header | Protects Against |
|--------|-----------------|
| HSTS | Protocol downgrade, MITM |
| CSP | XSS, data injection |
| X-Frame-Options | Clickjacking |
| X-Content-Type-Options | MIME sniffing attacks |
| Referrer-Policy | Information leakage |
| Permissions-Policy | Unauthorized feature access |
| X-XSS-Protection | Reflected XSS (legacy) |

---

## Before vs After

| Check | Before Headers | After Headers |
|-------|---------------|---------------|
| securityheaders.com grade | F | A or B |
| XSS via inline script | Possible | Blocked by CSP |
| Clickjacking | Possible | Blocked by X-Frame-Options |
| MIME sniffing | Possible | Blocked by nosniff |
| Server info leak | Visible | Hidden |

Implementing proper security headers significantly improves the security posture of any web application.
