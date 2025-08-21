# Nibbles - Knowledge Check
This is the first CPTS box without a guided walkthrough. 

## Report

**Date:** 2025-08-19  
**Author:** Arathorna
**Objective:** Gain a foothold of the system and escalate privileges

---

## 🖥️ Reconnaissance

### Nmap Scan
nmap --open -sV -oA HTB <target_ip>

<img width="635" height="233" alt="HTBLAB-nmap" src="https://github.com/user-attachments/assets/655b75a3-7951-4d08-ac1f-995fb1e6ec8c" />
Findings: 
22/tcp → OpenSSH 8.2p1 (Ubuntu Linux)
80/tcp → Apache httpd 2.4.41 (Ubuntu)
Host is Ubuntu with a http web server running on port 80.

## 🌐 Web Enumeration

### whatweb
whatweb <target_ip>
Findings: Web app is running HTTP Server Apache v2.4.41, GetSimple CMS detected 

### gobuster
gobuster dir -u http://<target_ip -w /usr/share/wordlists/dirb/common.txt

<img width="640" height="387" alt="HTBLAB-gobuster" src="https://github.com/user-attachments/assets/2353bb13-052a-49f5-8cc0-e2fb1d6f7d29" />

Findings: /admin → (admin login page), /data/users/admin.xml → Username: admin, hashed password, email: admin@gettingstarted.com

<img width="954" height="356" alt="HTBLAB-credentials" src="https://github.com/user-attachments/assets/70c4f2a9-e55d-4543-bad5-dc023057ef38" />

### 🔑 Credential Discovery
Cracked password hash with johntheripper → Admin Password: admin
Logged into /admin panel successfully.

<img width="664" height="422" alt="HTBLAB-john" src="https://github.com/user-attachments/assets/320fc758-d1dd-45bb-8ea1-f0a3c537e6f4" />


### 💥 Exploitation
GetSimple v3.3.15 vulnerability found in metasploit

<img width="611" height="66" alt="HTBLAB-GetSimple" src="https://github.com/user-attachments/assets/4ecee1d4-d39f-41e5-8224-0de185c8eee3" />


<img width="663" height="323" alt="HTBLAB-metasploit" src="https://github.com/user-attachments/assets/76e9b161-98d5-4794-906c-b90fce636f6c" />

<img width="555" height="486" alt="HTBLAB-exploit_options" src="https://github.com/user-attachments/assets/dc2c02c2-3d83-4334-b851-273fb096a4c7" />


Exploit completed successfully and spawned a shell

<img width="630" height="128" alt="HTBLAB-exploit" src="https://github.com/user-attachments/assets/dcab40ce-4746-4a66-bcd7-f6ff5a3a599e" />

Upgrade to Linux shell with "shell" then python script: python3 -c 'import pty; pty.spawn("/bin/bash")'


## 🚀 Privilege Escalation

Sudo -l gives us the following output: 

User www-data may run the following commands on gettingstarted:
    (ALL : ALL) NOPASSWD: /usr/bin/php

This indicates we can run PHP commands as root.

Privilege escalation via GTFObins:

sudo php -r "system('/bin/sh');"

<img width="668" height="105" alt="HTBLAB-root" src="https://github.com/user-attachments/assets/0a94d5bb-d3fa-4511-86db-ee0991bc41c7" />


We gained a root shell. 

## 📝 Notes / Lessons Learned


Thorough directory enumeration can reveal sensitive files containing credentials or configuration details

Weak admin password + outdated CMS led to full compromise

Misconfigured sudo privileges (NOPASSWD) allowed root escalation

## Tools used 

nmap
whatweb
gobuster
johntheripper
metasploit
GTFObins



