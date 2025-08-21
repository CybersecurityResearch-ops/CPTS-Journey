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
* 22/tcp → OpenSSH 8.2p1 (Ubuntu Linux)
* 80/tcp → Apache httpd 2.4.41 (Ubuntu)

### whatweb
