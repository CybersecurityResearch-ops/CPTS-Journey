# Footprinting Lab - Easy

**Author:** Arathorna 

**Date:** 2025-09-11   

**Objective:** Learn enumeration, FTP, and SSH exploitation techniques.

---

## 1. Lab Overview

The goal was to enumerate services, locate accessible files, and gain shell access via SSH using a discovered private key.

---

## Enumeration

We start by doing an Nmap scan 


<img width="502" height="363" alt="Screenshot from 2025-09-12 16-21-05" src="https://github.com/user-attachments/assets/471f7a3b-2ec9-4bef-b0b0-83a911a277ed" />


We can see we have another ftp service running on port 2121. We will attempt to login with the credentials our teammate has found. 


<img width="398" height="169" alt="Screenshot from 2025-09-12 15-39-22" src="https://github.com/user-attachments/assets/344f4f61-d2d4-455d-92bf-8d9ca83a27d8" />


We logged in successfully. By using ls- a we can see there is a .ssh directory listed. 


<img width="512" height="280" alt="Screenshot from 2025-09-12 15-39-45" src="https://github.com/user-attachments/assets/74482224-734d-4c25-ad48-cae27afc320c" />


Further investigation lead us to the private and public SSH keys.


<img width="509" height="216" alt="Screenshot from 2025-09-12 15-40-20" src="https://github.com/user-attachments/assets/3cd52e89-be6e-4e15-b33a-08457e94f7c7" />


Downloading the key


<img width="509" height="138" alt="Screenshot from 2025-09-12 15-40-47" src="https://github.com/user-attachments/assets/0c53ca8a-3d12-4151-a46b-31c6215d2be1" />


Once the file is downloaded on our local machine, we must change the file permissions so that SSH service can accept the key. The file must be readable **only** by the owner. 


<img width="181" height="38" alt="Screenshot from 2025-09-12 15-48-07" src="https://github.com/user-attachments/assets/8645784c-9ab2-4fdb-880e-3fea907e7432" />


---

## Gaining a Foothold

Now we are ready to SSH to the target with the private key we have discovered. 


<img width="506" height="305" alt="Screenshot from 2025-09-12 15-51-25" src="https://github.com/user-attachments/assets/b5a99b4e-50bd-41bd-a5a1-14d1bbf3c68c" />


Once we are successfully logged in, we navigate through the file system and find the flag.txt file. 


<img width="409" height="251" alt="Screenshot from 2025-09-12 15-53-56" src="https://github.com/user-attachments/assets/206a2b02-b215-43c3-bbc3-ac2baa405a24" />


---

### Tools Used

ftp, ssh, nmap
