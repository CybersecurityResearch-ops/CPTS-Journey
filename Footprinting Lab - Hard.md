# Footprinting Lab - Hard

**Author:** Arathorna  

**Date:** 2025-09-17  

**Objective:** Learn enumeration, service exploitation, credential discovery, and MySQL enumeration techniques.

---

## 1. Lab Overview

The goal of this lab was to enumerate services, discover credentials through accessible services, gain shell access via SSH, and explore MySQL databases to locate hidden user information.

---

## Enumeration

We started by performing an Nmap scan with the SYN scan parameter `-sS` to identify open TCP ports. The scan revealed the following services:

 <img width="443" height="397" alt="Screenshot from 2025-09-17 13-17-05" src="https://github.com/user-attachments/assets/014b8ba4-1874-4300-9b8b-a43a7475d959" />
<img width="443" height="413" alt="Screenshot from 2025-09-17 13-17-37" src="https://github.com/user-attachments/assets/cc22691a-4d32-4df6-8a7d-39ad89e916c2" />

Next, we conducted a UDP scan and discovered that the SNMP service was running.

<img width="512" height="348" alt="Screenshot from 2025-09-17 16-21-08" src="https://github.com/user-attachments/assets/6b9b1dac-907d-4070-b758-7d81b13822c3" />


Further enumeration using a wordlist against SNMP shares revealed a share called **`backup`**. By connecting to this share and using a tool called snmpwalk, we discovered the username `tom` and an associated password.

<img width="503" height="113" alt="Screenshot from 2025-09-17 13-25-43" src="https://github.com/user-attachments/assets/a05de625-9871-420e-9a74-7e452b5fe227" />

<img width="503" height="414" alt="Screenshot from 2025-09-17 13-26-48" src="https://github.com/user-attachments/assets/e7ec14a7-e077-419a-a9da-4155ab843ad0" />
<img width="503" height="427" alt="Screenshot from 2025-09-17 13-27-10" src="https://github.com/user-attachments/assets/4e892cec-b1cc-4d3e-802b-1d270387d60e" />


---

## Credential Discovery and IMAP Exploitation

Using the credentials obtained from SNMP, we accessed the IMAP service:

1. Logged in with user `tom` and the discovered password.  
2. Selected the inbox and found **1 available message**.  
3. The message contained a **private SSH key** for the `tom` user.


<img width="510" height="158" alt="Screenshot from 2025-09-17 13-34-28" src="https://github.com/user-attachments/assets/2abbcb31-6ebf-4361-b152-bbce44732a04" />

<img width="510" height="425" alt="Screenshot from 2025-09-17 13-38-26" src="https://github.com/user-attachments/assets/895dbdb2-6a92-4eeb-8e62-8c56382ade4f" />



This allowed us to prepare for SSH access to the target machine.

---

## Gaining a Foothold

After downloading the private key, we set proper file permissions so that the SSH service would accept the key:

chmod +x sshprivkey.txt

We then proceed to SSH into the target machine using the credentials and private key obtained earlier.

<img width="502" height="388" alt="Screenshot from 2025-09-17 13-43-50" src="https://github.com/user-attachments/assets/18d3b092-b207-480a-ab79-f91ce423a11b" />

While exploring the file system, we notice a couple of entries related to MySQL. The first is .mysql_history file, and the second is a MySQL entry in /etc/passwd. These indicators suggest that MySQL may contain valuable information worth investigating.




We then attempt to log in to MySQL using the credentials discovered earlier.

<img width="503" height="303" alt="Screenshot from 2025-09-17 15-32-29" src="https://github.com/user-attachments/assets/9c9382eb-4b78-4121-bc3e-58cd906561b6" />


After successfully logging in, we find a database named users.

<img width="503" height="324" alt="Screenshot from 2025-09-17 15-36-14" src="https://github.com/user-attachments/assets/e69440b8-78b8-4cfa-ba10-26d71c2c8b54" />

<img width="517" height="382" alt="Screenshot from 2025-09-17 15-44-48" src="https://github.com/user-attachments/assets/c88a9789-31dc-447d-b74f-8410ad3094ef" />


By running the following query, we successfully retrieve the user HTB along with the corresponding password.

<img width="416" height="157" alt="Screenshot from 2025-09-17 15-45-09" src="https://github.com/user-attachments/assets/a0193d5d-9e5b-4fc1-8ee3-d37d8461505f" />





