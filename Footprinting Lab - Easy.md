## HackTheBox Lab: InLaneFreight (CPTS Documentation)

**Author:** Your Name  
**Date:** 2025-09-12  
**Lab Type:** Retired/Practice HackTheBox machine  
**Objective:** Learn enumeration, FTP, and SSH exploitation techniques.

---

## 1. Lab Overview

This lab focuses on basic **network service enumeration** and **privileged access via SSH keys**. Key services observed:

| Port | Service | Notes |
|------|---------|-------|
| 21   | FTP     | Default FTP service, requires credentials |
| 2121 | FTP     | Alternate FTP port, same credentials work |
| 22   | SSH     | Key-based authentication only, password auth disabled |

The goal was to enumerate services, locate accessible files, and gain shell access via SSH using a discovered private key.

---

## 2. Enumeration

### 2.1 FTP Service

Both ports 21 and 2121 were open. The lab user `ceil` credentials were discovered via initial FTP enumeration:



