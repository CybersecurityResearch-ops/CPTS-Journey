# Shells & Payloads - The Live Engagement

**Author:** Arathorna  

**Date:** 2025-10-2  

**Objective:** Exploit and gain a RCE session on each of the 3 hosts on the same network. 

---

## 1. Lab Overview

This lab focused on full-cycle attack flow: host enumeration, service discovery, vulnerability identification, weaponization (file uploads / exploit modules), exploitation to obtain remote shells, and basic post-exploitation (file-system exploration and flag discovery). Targets were exploited using a mix of a custom WAR upload, a local Metasploit exploit module, and an EternalBlue-based module.

---

## 1. Host 1 

---

First things fist, we RDP into our foothold machine provided. Once we establish an RDP session we will explore the file system for any valuable information or credentials lying around. 

<img width="548" height="44" alt="Screenshot from 2025-10-01 15-23-45" src="https://github.com/user-attachments/assets/85aa429c-a2e8-4835-85fc-73d0d6db1173" />


We manage to discover a set of admin credentials for an Apache server. 

<img width="607" height="295" alt="Screenshot from 2025-10-02 13-42-35" src="https://github.com/user-attachments/assets/e6af126a-ffb9-40a1-9502-cafd58c97da2" />

Now we will start enumerating our first host with nmap. We also discover the host name shells-winsvr

<img width="548" height="52" alt="Screenshot from 2025-10-01 15-36-45" src="https://github.com/user-attachments/assets/27d90970-25cf-4776-a09e-9012d3443258" />

<img width="776" height="98" alt="Screenshot from 2025-10-01 15-37-16" src="https://github.com/user-attachments/assets/4879bf16-b0a5-4c0d-8878-7de283ad53d2" />


Navigating to 172.16.1.11:8080 in our browser reveals to us an Apache Tomcat web application. 

<img width="972" height="296" alt="Screenshot from 2025-10-02 13-44-50" src="https://github.com/user-attachments/assets/de132077-7260-4f0e-b7a3-46b21c230bce" />

The Tomcat Manager was present and prompted for credentials. I authenticated using the credentials recovered from the foothold VM. Scrolling down the web page, we can also see some information on the host machine.

<img width="972" height="85" alt="Screenshot from 2025-10-02 13-44-01" src="https://github.com/user-attachments/assets/def9d1a0-7ac0-4315-abce-992944761a66" />

The Manager UI allows deploying WAR files. We will try to take advantage of this, by creating a custom payload with msfvenom. 

<img width="1005" height="305" alt="Screenshot from 2025-10-03 11-13-53" src="https://github.com/user-attachments/assets/1a84e42d-9bb0-43d0-aed6-e2775de3a673" />

We will use a custom java jsp payload since the web application is Apache Tomcat and runs on java. Listing the options, we see that we only need to list an LHOST and LPORT. 

<img width="806" height="113" alt="Screenshot from 2025-10-02 13-36-32" src="https://github.com/user-attachments/assets/44d37ab4-5733-4714-8a00-7bd00d1623d1" />

<img width="601" height="77" alt="Screenshot from 2025-10-03 11-32-53" src="https://github.com/user-attachments/assets/dd3d3e50-3a6c-49fe-94df-a7b5010e0802" />

We generate the WAR file with msfvenom, specifying payload, LHOST, LPORT, format (-f), and the output file name (saved as shell.war).

<img width="805" height="77" alt="Screenshot from 2025-10-03 11-30-33" src="https://github.com/user-attachments/assets/fc153c22-071a-4132-aeab-69343d7021bb" />

Now we head back in the Tomcat Manager UI and upload the shell.war file. 

<img width="974" height="124" alt="Screenshot from 2025-10-02 13-37-18" src="https://github.com/user-attachments/assets/f0ac195b-200d-49e0-8375-3b6ec9c280ea" />

It was uploaded successfully. We will now start a Listener on metasploit, only the LPORT and LHOST need to be set. Also, we need to make sure our LPORT matches the one we configured for our shell.war payload (in our case we chose 4444) so that once the payload is executed it can communicate with our listener. Once everything is configured we enter the "run" command which starts the TCP listener on port 4444. 

<img width="820" height="400" alt="Screenshot from 2025-10-02 13-35-17" src="https://github.com/user-attachments/assets/89ddf7dc-66a8-435d-9460-29975b2da3a0" />

Now we head back to the Tomcat Manager UI and click on one shell.war file to execute the payload. We check in on our listener and we have succesfully secured a reverse shell. We navigate to the Shares directory and discover a Share "dev-share". 

<img width="820" height="392" alt="Screenshot from 2025-10-02 13-34-36" src="https://github.com/user-attachments/assets/d0b6a4e2-92bf-42fc-8d56-7c9393b23306" />

---

## 2. Host 2 

---

Now off to host 2. First we start or enumeration phase with an nmap command and we discover port 80 is open, hosting an Apache webserver, our scan also reveals to us the Linux distribution the machine is running on which is Ubuntu. 

<img width="802" height="428" alt="Screenshot from 2025-10-02 15-43-23" src="https://github.com/user-attachments/assets/51771fbd-bc02-4e11-b231-6a686306c78b" />

Exploring the site revealed a developer’s post hinting that the blog engine was vulnerable to a PHP exploit that allows RCE.

<img width="506" height="419" alt="Screenshot from 2025-10-02 15-45-40" src="https://github.com/user-attachments/assets/2153d0d6-c439-4c67-aaf4-13e36d436835" />

While using searchsploit we found a matching exploit; the same exploit logic was present locally as 50064.rb, although the module wasn’t initially visible in the Metasploit console.

<img width="693" height="30" alt="Screenshot from 2025-10-03 12-04-26" src="https://github.com/user-attachments/assets/d19a91ee-60a5-4dd4-8928-5238de5d137a" />

While navigating our metasploit-framework/modules/exploits directory we do find that we have a 50064.rb file, and inspecting the contents matches the exploit that the web server is vulnerable to. We can also see that the shell language is written in PHP.

<img width="809" height="180" alt="Screenshot from 2025-10-03 12-11-16" src="https://github.com/user-attachments/assets/c191f545-0c5b-40b6-96a7-cdac0341277c" />

<img width="715" height="207" alt="Screenshot from 2025-10-02 17-55-04" src="https://github.com/user-attachments/assets/7edc12de-f8d9-4084-a3ad-d78ed8bc24a7" />

Since the file is already written in ruby we can directly copy the file to metasploits multi/php directory so that we may see and use the exploit in msfconsole. 

<img width="804" height="98" alt="Screenshot from 2025-10-02 16-19-03" src="https://github.com/user-attachments/assets/0a8d8f82-0e44-4c18-9779-3c7c72c1d554" />

Now since we added a new exploit, we must reload the modules so that it can register in the database. 

<img width="467" height="51" alt="Screenshot from 2025-10-02 16-19-28" src="https://github.com/user-attachments/assets/d942fae2-517d-4222-ae62-2bdf4c828873" />

We can now see our exploit added and we will start configuring the settings and run the exploit.

<img width="802" height="241" alt="Screenshot from 2025-10-02 16-19-50" src="https://github.com/user-attachments/assets/52bc12a8-f3b1-4b40-aa0b-1d7332f03b0c" />

<img width="804" height="226" alt="Screenshot from 2025-10-02 16-58-16" src="https://github.com/user-attachments/assets/843e8d49-df01-4584-a0da-0a12eda727be" />

We manage to catch a shell session and we navigate to "customscripts" to find the flag.txt file. 

<img width="171" height="96" alt="Screenshot from 2025-10-02 17-03-13" src="https://github.com/user-attachments/assets/d48c1689-b94f-463d-accb-349c492f60a2" />

---

## 3. Host 3

---

We conduct an nmap scan on our 3rd and final target and we find the host name shells-winblue. We can also see that it has open ports responsible for SMB. We can try to see if our target is vulnerable to the etnernalblue exploit. We can test this with an auxiliary module in metasploit. 

<img width="802" height="212" alt="Screenshot from 2025-10-02 17-32-33" src="https://github.com/user-attachments/assets/27186a26-b6de-486b-8508-b08e2e367960" />

As we can see, there is a good chance our target is vulnerable to this exploit. We will now use the eternalblue exploit module. After configuring the options and running the exploit we catch a shell. 

<img width="806" height="480" alt="Screenshot from 2025-10-02 17-39-18" src="https://github.com/user-attachments/assets/80a55d34-0b63-4a60-8fa7-6901226a3052" />

Navigating to the \Administrator\Desktop directory, we find our Skills-flag.txt file. 

<img width="730" height="222" alt="Screenshot from 2025-10-02 17-58-35" src="https://github.com/user-attachments/assets/9ee0b055-f7a2-47a0-927b-bdfd34d726d4" />










