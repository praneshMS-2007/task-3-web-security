# Burp Suite Advanced

Burp Suite is the industry-standard web application security testing proxy.

---

## Setup

1. Launch Burp Suite on Kali Linux
2. Configure browser proxy: `127.0.0.1:8080`
3. Install Burp CA certificate for HTTPS interception
4. Browse to target application through the proxy

---

## Proxy — Intercept

### Intercepting Login Requests

1. Enable **Intercept** in Proxy tab
2. Navigate to DVWA login page
3. Enter credentials and submit
4. Request appears in Intercept tab:

```http
POST /DVWA/login.php HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

username=admin&password=password&Login=Login
```

5. Modify parameters before forwarding:
   - Change username to `admin' --`
   - Change password to anything
6. Forward the modified request
7. Observe if SQL injection bypasses authentication

### Modifying Headers

```http
X-Forwarded-For: 127.0.0.1
User-Agent: <script>alert('XSS')</script>
Referer: http://evil.com
```

---

## Repeater

Send individual requests and modify them repeatedly.

1. Right-click intercepted request → **Send to Repeater**
2. Modify parameters in Repeater tab
3. Click **Send** to see response
4. Iterate with different payloads

**Use cases:**
- Test SQL injection payloads one by one
- Test XSS payloads in different parameters
- Modify cookies and headers

---

## Intruder — Fuzzing

Automated payload injection for brute force and fuzzing.

### Setup

1. Intercept a request (e.g., login form)
2. Right-click → **Send to Intruder**
3. Go to **Positions** tab → **Clear §**
4. Highlight parameter values → **Add §**

```http
POST /DVWA/login.php HTTP/1.1

username=§admin§&password=§password§&Login=Login
```

### Payload Configuration

1. **Payloads** tab → **Payload type:** Simple list
2. Add payloads:

```
admin
administrator
root
test
guest
' OR '1'='1
admin' --
```

3. **Start attack**

### Analyzing Results

| Column | What to Check |
|--------|---------------|
| Status | 200 vs 302 (redirect = success) |
| Length | Different response length = different result |
| Time | Longer response = possible time-based SQLi |

### Attack Types

| Type | Use Case |
|------|----------|
| Sniper | One payload position at a time |
| Battering ram | Same payload in all positions |
| Pitchfork | Paired payloads (username:password lists) |
| Cluster bomb | All combinations of payloads |

---

## HTTP History

Review all proxied traffic:
- Filter by status code, MIME type, parameter
- Search for sensitive data in responses
- Identify hidden parameters and endpoints

---

## Scanner (Pro Version)

Automated vulnerability scanning:
- Active scanning sends payloads to detect vulnerabilities
- Passive scanning analyzes traffic without sending attacks
- Reports vulnerabilities with severity ratings

---

## Workflow Summary

```
1. Configure proxy and browse target
2. Map the application (Spider/Crawler)
3. Intercept interesting requests
4. Send to Repeater for manual testing
5. Send to Intruder for automated fuzzing
6. Document findings
7. Verify and report vulnerabilities
```

---

## Key Shortcuts

| Action | Shortcut |
|--------|----------|
| Toggle intercept | Ctrl + I |
| Forward request | Ctrl + F |
| Send to Repeater | Ctrl + R |
| Send to Intruder | Ctrl + I |
| HTTP History | Ctrl + H |
