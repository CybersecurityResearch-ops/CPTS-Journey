### HackTheBox Lab: InLaneFreight (CPTS Documentation)

**Author:** Arathorna 
**Date:** 2025-09-11   
**Objective:** Learn enumeration, FTP, and SSH exploitation techniques.

---

## 1. Lab Overview

This lab focuses on basic **network service enumeration** and **privileged access via SSH keys**. Key services observed:

| Port | Service | Notes |
|------|---------|-------|
| 21   | FTP     | Default FTP service, requires credentials |
| 2121 | FTP     | Alternate FTP port, same credentials work |
| 22   | SSH     | Key-based authentication only, password auth disabled |
| 53   | DNS     | Default DNS server

The goal was to enumerate services, locate accessible files, and gain shell access via SSH using a discovered private key.

---

## Enumeration

We start by doing an Nmap scan 

<img width="502" height="363" alt="Screenshot from 2025-09-12 16-21-05" src="https://github.com/user-attachments/assets/471f7a3b-2ec9-4bef-b0b0-83a911a277ed" />


## Gaining a Foothold


# Tools Used

ftp, ssh, nmap, dig
