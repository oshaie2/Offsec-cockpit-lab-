# 🧠 Cockpit – Penetration Test Walkthrough

---

## 📌 Overview

- **Target:** Linux Machine (OffSec Proving Grounds)  
- **Objective:** Gain root access  
- **Methodology:** Enumeration → Exploitation → Privilege Escalation  

---

## 🔍 1. Nmap Scan

Performed a full port scan to identify open services:

![Nmap](nmap.png)

---

## 💉 2. SQL Injection

While testing the login form, an SQL error was returned.

This indicates that user input is being directly inserted into a query without proper sanitization, confirming a potential SQL injection vulnerability.

![SQL Error](MYSQL-error.png)

---

## 🔓 3. Login Bypass

Using SQL injection, authentication was bypassed with:

```
'or 1=1--
```

This forces the query to always evaluate as true, allowing login without valid credentials.

![Login Bypass](login-bypass.jpeg)

---

## 🔐 4. Base64 Credentials

Discovered credentials were encoded using Base64.

After decoding:

```
james:canttouchthis@455152
cameron:thiscantbetouched@455152
```

![Base64](base64-decoded.png)

---

## 🔑 5. SSH Access

Used the discovered credentials to gain SSH access as user **james**:

![SSH](ssh-access.png)

---

## 🚀 6. Privilege Escalation

Checked sudo permissions:

```
sudo -l
```

User **james** can run `tar` as root without a password:

```
sudo /usr/bin/tar -czvf /tmp/backup.tar.gz *
```

The `tar` binary supports checkpoint actions that allow command execution.

By abusing wildcard injection and checkpoint execution, we forced `tar` to execute a shell script as root, resulting in privilege escalation.

![Sudo](sudo-1.jpeg)  
![Tar Exploit](tar-exploit.png)

---

## 👑 7. Root Access

Spawned a root shell using the tar exploit.

Verified access:

```
whoami
root
```

![Root Shell](root-shell.png)

---

## 🧠 Key Takeaways

- SQL errors can reveal injection points  
- Authentication mechanisms should always be tested for SQL injection  
- Base64 encoding is often used to hide credentials  
- Misconfigured sudo permissions can lead to privilege escalation  
- GTFOBins is essential for exploiting allowed binaries  

---

## 🔥 Attack Chain Summary

```
Web App → SQL Injection → Auth Bypass → Credentials → SSH → sudo tar → Root
```

---
