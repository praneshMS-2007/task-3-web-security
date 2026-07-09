# Task 3 Final Submission

**Program:** ApexPlanet Cybersecurity & Ethical Hacking Internship  
**Task:** Task 3 - Web Application Security  
**Intern Name:** PRANESH M S  
**Offer Letter ID:** APSPL2633989  
**Date:** 09 July 2026

## Objective

Identify and understand OWASP Top 10 web vulnerabilities in a controlled lab environment through documentation, attack scenarios, and mitigation strategies.

## Work Completed

This submission includes documentation and scenarios covering all Task 3 areas:

1. SQL Injection (authentication bypass, data extraction)
2. Cross-Site Scripting — Stored and Reflected XSS
3. Cross-Site Request Forgery (CSRF)
4. File Inclusion attacks (LFI and RFI)
5. Burp Suite advanced usage (Intercept, Intruder)
6. Web security headers analysis and configuration

## Included Files

| File | Purpose |
|------|---------|
| `README.md` | Project overview and repository structure |
| `docs/01-sql-injection.md` | SQLi types, payloads, and prevention |
| `docs/02-cross-site-scripting.md` | Stored and reflected XSS |
| `docs/03-csrf.md` | CSRF attacks and token protection |
| `docs/04-file-inclusion.md` | LFI and RFI attacks |
| `docs/05-burp-suite.md` | Intercept, modify, and Intruder fuzzing |
| `docs/06-security-headers.md` | HTTP security headers and Apache config |
| `scenarios/sqli-attack-mitigation.md` | SQLi attack scenario with fix |
| `scenarios/xss-attack-mitigation.md` | XSS attack scenarios with fix |
| `scenarios/csrf-attack-mitigation.md` | CSRF attack scenario with fix |
| `reports/security-testing-report.md` | Consolidated security testing report |
| `cheat-sheets/owasp-top10-cheat-sheet.md` | OWASP Top 10 quick reference |

## Summary of Learning

### SQL Injection
- Authentication bypass using `' OR '1'='1`
- UNION-based extraction of usernames and passwords
- Prevention using prepared statements and parameterized queries

### Cross-Site Scripting
- Stored XSS via persistent input fields
- Reflected XSS via URL query parameters
- Mitigation: input validation, output encoding, Content Security Policy

### CSRF
- Forging requests to change user passwords
- Token-based protection (CSRF tokens, SameSite cookies)

### File Inclusion
- Local File Inclusion to read sensitive files (`/etc/passwd`)
- Remote File Inclusion to execute malicious code

### Burp Suite
- Intercepting and modifying login requests
- Fuzzing with the Intruder tool

### Security Headers
- Analyzing sites with securityheaders.com
- Configuring Apache with proper HTTP security headers

## Deliverables

- Security testing report covering SQLi, XSS, and CSRF
- Attack scenarios with mitigation notes
- GitHub repository with complete documentation

## Submission Links

| Item | Link |
|------|------|
| GitHub Repository | https://github.com/praneshMS-2007/task-3-web-security |
| LinkedIn Video | [Paste your LinkedIn video link] |

## Declaration

This repository has been prepared as the Task 3 submission for the ApexPlanet Cybersecurity & Ethical Hacking Internship.
