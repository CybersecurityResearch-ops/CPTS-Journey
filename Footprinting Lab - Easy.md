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

## 1. Enumeration



