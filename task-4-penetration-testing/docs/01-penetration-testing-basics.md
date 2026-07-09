# 01 — Penetration Testing Basics

## What is Penetration Testing?

Penetration testing is a controlled security assessment in which authorized testers simulate attacks against systems, applications, or networks to identify weaknesses before real attackers can exploit them.

## Phases of Penetration Testing

1. Planning and scoping
2. Reconnaissance and information gathering
3. Vulnerability discovery
4. Exploitation
5. Privilege escalation and post-exploitation
6. Reporting and remediation

## Common Objectives

- Identify exposed services and misconfigurations
- Validate the impact of vulnerabilities
- Provide actionable remediation steps
- Support security hardening efforts

## Tools Commonly Used

- Nmap — host and port discovery
- Burp Suite — web app testing
- Metasploit — exploitation framework
- Netcat — shell testing and simple backdoors
- Wireshark — traffic inspection

## Important Rules

- Only test systems you own or are authorized to inspect
- Document every step clearly
- Avoid destructive actions unless explicitly allowed
- Keep evidence and screenshots for reporting

## Example Lab Flow

- Start with discovery
- Identify open ports and running services
- Analyze the service versions
- Test for known vulnerabilities
- Record findings and recommend fixes
