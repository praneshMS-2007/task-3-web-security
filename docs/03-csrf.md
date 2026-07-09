# Cross-Site Request Forgery (CSRF)

CSRF tricks an authenticated user into performing unwanted actions on a web application they are logged into.

---

## How CSRF Works

```
1. Victim is logged into bank.com (session cookie active)
2. Victim visits attacker.com
3. Attacker's page contains:
   <img src="http://bank.com/transfer?to=attacker&amount=10000">
4. Browser automatically sends session cookie with the request
5. Bank processes the transfer as if the victim initiated it
```

The browser includes cookies automatically — the server cannot distinguish a legitimate request from a forged one.

---

## CSRF Attack on DVWA

### Scenario: Change Admin Password

**Vulnerable form (no CSRF protection):**
```html
<form action="/DVWA/vulnerabilities/csrf/" method="GET">
  <input name="password_new" value="hacked">
  <input name="password_conf" value="hacked">
  <input name="Change" value="Change">
</form>
```

**Attacker's malicious page:**
```html
<html>
<body>
  <h1>Click here for a free prize!</h1>
  <img src="http://localhost/DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change" style="display:none">
</body>
</html>
```

When the logged-in admin visits this page, their password is changed without their knowledge.

---

## CSRF Attack Vectors

| Vector | Method | Example |
|--------|--------|---------|
| GET request | Image/iframe tag | `<img src="http://site/action?param=value">` |
| POST request | Auto-submit form | Hidden form with JavaScript submit |
| JSON API | Fetch with credentials | `fetch(url, {credentials: 'include'})` |
| Link | Anchor tag | `<a href="http://site/delete?id=1">Click me</a>` |

---

## Conditions for CSRF

1. Victim must be **authenticated** (active session)
2. Target action must be **predictable** (known URL/parameters)
3. No **CSRF protection** on the form/request
4. Victim must **visit** the attacker's page

---

## Prevention: CSRF Tokens

### How Tokens Work

```
1. Server generates a unique random token per session/form
2. Token is embedded in the form as a hidden field
3. Server validates the token on submission
4. Attacker cannot guess or obtain the token
```

### Implementation (PHP)

```php
// Generate token
session_start();
$_SESSION['csrf_token'] = bin2hex(random_bytes(32));

// In form
echo '<input type="hidden" name="csrf_token" value="' . $_SESSION['csrf_token'] . '">';

// Validate on submit
if ($_POST['csrf_token'] !== $_SESSION['csrf_token']) {
    die('CSRF token validation failed');
}
```

### SameSite Cookie Attribute

```php
setcookie("session", $value, [
    "samesite" => "Strict",
    "httponly" => true,
    "secure" => true
]);
```

| SameSite Value | Behavior |
|----------------|----------|
| Strict | Cookie never sent on cross-site requests |
| Lax | Cookie sent on top-level navigation only |
| None | Cookie sent on all requests (requires Secure) |

### Additional Defenses

- **Referer/Origin header validation** — check request origin
- **Re-authentication** — require password for sensitive actions
- **Custom headers** — require `X-Requested-With` header for AJAX
- **Double submit cookie** — token in both cookie and form field

See [scenarios/csrf-attack-mitigation.md](../scenarios/csrf-attack-mitigation.md) for a complete attack and fix scenario.
