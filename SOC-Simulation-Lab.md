# SOC Analyst Simulation Lab

## Overview

In this lab I built a small **Security Operations Center (SOC) simulation environment** to practice monitoring and detecting suspicious activity using centralized logging.

The goal of this project is to simulate tasks that a SOC analyst might perform daily, such as:

- Ingesting endpoint logs
- Analyzing system activity
- Detecting suspicious commands
- Investigating authentication failures

The lab environment consists of three virtual machines:

| Machine | Role |
|--------|------|
| Ubuntu Server 22.04 | Splunk SIEM server |
| Windows 10 | Endpoint being monitored |
| Kali Linux | Attacker machine used to simulate malicious activity |

The Windows system generates logs which are forwarded to the Splunk server for analysis. After configuring the environment, I simulate several common attacker behaviors and detect them using Splunk searches.



# Part 1 — Environment Setup

## Splunk Server (Ubuntu)

The Ubuntu virtual machine acts as the **central logging server** running **Splunk Enterprise**.

Splunk is a **Security Information and Event Management (SIEM)** platform that allows organizations to:

- Collect logs from many systems
- Index and store those logs
- Search and analyze security events
- Detect suspicious behavior

After installing Splunk on Ubuntu, I start

!(Ubuntu_server_splunk.png)
*image of service initialization*

Once running, the Splunk web interface is available on:

```
http://<Ubuntu-IP>:8000
```

*image of splunk interface*

This interface allows analysts to search logs and build detections.

### Windows Endpoint Monitoring
The Windows 10 machine represents a monitored endpoint in an organization.

Two important tools were installed on this system:

#### Splunk Universal Forwarder
The Splunk Universal Forwarder is a lightweight agent that runs on endpoints and sends logs to the Splunk server.

Its role is to:

- Collect logs from Windows Event Viewer

- Send those logs to the Splunk indexer

- Run continuously with minimal system impact

Without the forwarder, Splunk would have no way to collect logs from the Windows machine.

#### Sysmon Installation
I also installed Sysmon (System Monitor), part of the Microsoft Sysinternals toolkit.

*Sysmon install image*

Sysmon enhances Windows logging by recording detailed system activity such as:

- process creation
- network connections
- file creation
- registry modifications

These events are extremely useful for detecting attacker behavior.

#### Example Sysmon Event IDs:

Event ID	Description
1	Process creation
3	Network connection
11	File creation
After installation, Sysmon logs can be found in:

- Event Viewer
- Applications and Services Logs
- Microsoft
- Windows
- Sysmon
- Operational

*image of location*

These logs are then forwarded to Splunk by the Splunk Universal Forwarder.

### inputs.conf Configuration
To tell the forwarder which logs to collect, we configure the inputs.conf file.

Example configuration:

*inputs.conf image*

This configuration instructs the forwarder to collect logs from:

- Windows Application logs

- Windows Security logs

- Windows System logs

- Sysmon operational logs

The logs are then forwarded to the Splunk index named "main".

### Forwarder Permissions
The SplunkForwarder service must run under the **Local System Account**.

This is necessary because:

- The Security log requires elevated privileges

- Sysmon logs require administrative access

Without these permissions, the forwarder cannot read the logs and they will not appear in Splunk.

*image of permission*

### Verifying Log Ingestion
To confirm that logging works correctly, I executed the following command:
```
notepad.exe
```
Because Sysmon logs process creation (Event ID 1), the event appears in Splunk.

#### Example search:
```
index=main EventCode=1 notepad.exe
```
*image of command execution + log of command*

This confirms that the logging pipeline is functioning correctly:

Windows → Sysmon → Splunk Forwarder → Splunk Server

## Part 2 — Attack and Suspicious Activity Simulation

After verifying that logs are being ingested correctly, I simulated several common attacker behaviors.

### Encoded PowerShell Execution
Attackers often encode PowerShell commands using Base64 encoding.

Example:

*PS encoded command image*

Encoding PowerShell commands helps attackers:

- Bypass simple keyword-based detection

- Hide malicious commands from analysts

- Evade logging filters

- Obfuscate payloads delivered through scripts

However, encoded commands are still visible in Sysmon logs.

*image of encoded powershell command log*

#### Search example in Splunk:
```
index=main EventCode=1 powershell
```
Or we can specifically search for: 
```
*-enc*
```
because encoded PowerShell commands are a common indicator of malicious activity.

### System Reconnaissance Commands
After gaining access to a system, attackers typically perform reconnaissance to understand the environment.

I simulated this by running several common enumeration commands:
```
whoami
ipconfig
net user
net localgroup administrators
systeminfo
```

*image of commands run*

These commands allow an attacker to discover:

- The current user account

- Network configuration

- Existing user accounts

- Administrator privileges

- Operating system information

Because Sysmon records process creation, each command generates an event.

*image of command execution logs*

#### Example Splunk search:
```
index=main EventCode=1
CommandLine="*ipconfig*" OR CommandLine="*net user*"
```
These logs allow analysts to detect suspicious reconnaissance activity.


### Brute Force Attack Simulation

Finally, I simulated a brute force attack against the Windows SMB service using the Kali attacker machine.

The tool used was CrackMapExec, which is commonly used for penetration testing and network exploitation.

This attack repeatedly attempts to authenticate to SMB using different credentials.

*image of CME command*

This generates failed login events in Windows.

Windows logs these as:

- Event ID 4625
- Failed logon
  
These logs appear in Splunk and include useful information such as:

- Username attempted

- Source IP address

- Authentication type

- Logon failure reason

*image of CME logs*

#### Example Splunk search:

```
index=main EventCode=4625
| stats count by Account_Name, Source_Network_Address
```
This allows us to quickly identify brute force attempts and determine the source of the attack.


#### Key Lessons Learned

Through this lab I gained practical experience with:

- Configuring a SIEM environment

- Forwarding endpoint logs

- Analyzing security events

- Detecting attacker techniques

This lab demonstrates and helped me understand how SOC analysts use centralized logging tools such as Splunk Enterprise to monitor systems and investigate suspicious activity.




