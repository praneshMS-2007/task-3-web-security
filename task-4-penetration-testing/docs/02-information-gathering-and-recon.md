# 02 — Information Gathering & Reconnaissance

## Purpose

Reconnaissance is the first stage of penetration testing. It helps collect information about hosts, services, operating systems, network layout, and possible entry points.

## Types of Reconnaissance

### Passive Recon

- Search engine dorking
- Social media and public profiles
- Domain WHOIS and DNS records
- Public code repositories

### Active Recon

- Host discovery with ping or ICMP sweeps
- Port scanning with Nmap
- Service fingerprinting
- Banner grabbing

## Example Commands

```bash
nmap -sS 192.168.56.0/24
nmap -sV -A 192.168.56.101
netdiscover -r 192.168.56.0/24
```

## What to Collect

- IP addresses
- Open ports
- Service versions
- Operating system details
- Domain names and subdomains
- Web technologies in use

## Reconnaissance Notes

- Keep notes organized by target
- Save scan results for later comparison
- Do not exceed the agreed scope
- Use caution with DNS and email enumeration
