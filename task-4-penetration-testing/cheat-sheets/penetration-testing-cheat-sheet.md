# Penetration Testing Cheat Sheet

## Reconnaissance

```bash
nmap -sS target-ip
nmap -sV -A target-ip
netdiscover -r target-network
```

## Scanning

```bash
nmap -p- target-ip
nmap --script vuln target-ip
```

## Exploitation

```bash
msfconsole
nc -lvnp 4444
```

## Privilege Escalation

```bash
sudo -l
id
find / -perm -4000 2>/dev/null
```

## Reporting

- Capture screenshots
- Record commands and outputs
- Document severity and impact
- Provide remediation steps
