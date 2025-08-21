# Nibbles - Knowledge Check
This is the first box in the the CPTS path where there is not a guided walkthrough to assist us. 

## Report

**Date:** 2025-08-19  
**Author:** Arathorna
**Objective:** Gain a foothold of the system and escalate privileges

---

## 🖥️ Reconnaissance

### Nmap Scan
bash
nmap --open -sV -oA HTB <target_ip>

<img width="635" height="233" alt="HTBLAB-nmap" src="https://github.com/user-attachments/assets/655b75a3-7951-4d08-ac1f-995fb1e6ec8c" />
Findings: 
22/tcp → OpenSSH 8.2p1 (Ubuntu Linux)
80/tcp → Apache httpd 2.4.41 (Ubuntu)
Host is Ubuntu with a http web server running on port 80.

## 🌐 Web Enumeration

### whatweb
whatweb <target_ip>
Findings: Apache 2.4.41, GetSimple CMS detected 

### gobuster
gobuster dir -u http://<target_ip -w /usr/share/wordlists/dirb/common.txt

<img width="640" height="387" alt="HTBLAB-gobuster" src="https://github.com/user-attachments/assets/2353bb13-052a-49f5-8cc0-e2fb1d6f7d29" />

Findings: /admin → (admin login page), /data/users/admin.xml → Username: admin, hashed password, email: admin@gettingstarted.com

<img width="954" height="356" alt="HTBLAB-credentials" src="https://github.com/user-attachments/assets/70c4f2a9-e55d-4543-bad5-dc023057ef38" />





