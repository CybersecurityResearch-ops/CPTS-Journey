# SOC Analyst Simulation Lab

## Overview

In this lab I built a small **Security Operations Center (SOC) simulation environment** to practice monitoring and detecting suspicious activity using centralized logging.

The goal of this project is to simulate tasks that a SOC analyst might perform daily, such as:

- ingesting endpoint logs
- analyzing system activity
- detecting suspicious commands
- investigating authentication failures

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

- collect logs from many systems
- index and store those logs
- search and analyze security events
- detect suspicious behavior

After installing Splunk on Ubuntu, I started the service with:

```bash
sudo /opt/splunk/bin/splunk start --run-as-root
Once running, the Splunk web interface is available on:

http://<Ubuntu-IP>:8000
This interface allows analysts to search logs and build detections.

Windows Endpoint Monitoring
The Windows 10 machine represents a monitored endpoint in an organization.

Two important tools were installed on this system:

Splunk Universal Forwarder
The Splunk Universal Forwarder is a lightweight agent that runs on endpoints and sends logs to the Splunk server.

Its role is to:

collect logs from Windows Event Viewer

send those logs to the Splunk indexer

run continuously with minimal system impact

Without the forwarder, Splunk would have no way to collect logs from the Windows machine.

Sysmon
I also installed Sysmon (System Monitor), part of the Microsoft Sysinternals toolkit.

Sysmon enhances Windows logging by recording detailed system activity such as:

process creation

network connections

file creation

registry modifications

These events are extremely useful for detecting attacker behavior.

Example Sysmon Event IDs:

Event ID	Description
1	Process creation
3	Network connection
11	File creation
After installation, Sysmon logs can be found in:

Event Viewer
Applications and Services Logs
Microsoft
Windows
Sysmon
Operational
These logs are then forwarded to Splunk by the Splunk Universal Forwarder.

inputs.conf Configuration
To tell the forwarder which logs to collect, we configure the inputs.conf file.

Example configuration:

[WinEventLog://Application]
index = main
disabled = false

[WinEventLog://Security]
index = main
disabled = false

[WinEventLog://System]
index = main
disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
index = main
disabled = false

This configuration instructs the forwarder to collect logs from:

-Windows Application logs

-Windows Security logs

-Windows System logs

-Sysmon operational logs

The logs are then forwarded to the Splunk index named "main".

Forwarder Permissions
The SplunkForwarder service must run under the Local System Account.

This is necessary because:

the Security log requires elevated privileges

Sysmon logs require administrative access

Without these permissions, the forwarder cannot read the logs and they will not appear in Splunk.

Verifying Log Ingestion
To confirm that logging works correctly, I executed the following command:

notepad.exe
Because Sysmon logs process creation (Event ID 1), the event appears in Splunk.

Example search:

index=main EventCode=1 notepad.exe
This confirms that the logging pipeline is functioning correctly:

Windows → Sysmon → Splunk Forwarder → Splunk Server
Part 2 — Attack and Suspicious Activity Simulation
After verifying that logs are being ingested correctly, I simulated several common attacker behaviors.

Encoded PowerShell Execution
Attackers often encode PowerShell commands using Base64 encoding.

Example:

powershell -enc <encoded command>
Encoding PowerShell commands helps attackers:

bypass simple keyword-based detection

hide malicious commands from analysts

evade logging filters

obfuscate payloads delivered through scripts

However, encoded commands are still visible in Sysmon logs.

Search example in Splunk:

index=main EventCode=1 powershell
SOC analysts often specifically search for:

*-enc*
because encoded PowerShell commands are a common indicator of malicious activity.

System Reconnaissance Commands
After gaining access to a system, attackers typically perform reconnaissance to understand the environment.

I simulated this by running several common enumeration commands:

whoami
ipconfig
net user
net localgroup administrators
systeminfo

These commands allow an attacker to discover:

the current user account

network configuration

existing user accounts

administrator privileges

operating system information

Because Sysmon records process creation, each command generates an event.

Example Splunk search:

index=main EventCode=1
CommandLine="*ipconfig*" OR CommandLine="*net user*"
These logs allow analysts to detect suspicious reconnaissance activity.

Brute Force Attack Simulation
Finally, I simulated a brute force attack against the Windows SMB service using the Kali attacker machine.

The tool used was CrackMapExec, which is commonly used for penetration testing and network exploitation.

The attack repeatedly attempts to authenticate to SMB using different credentials.

This generates failed login events in Windows.

Windows logs these as:

Event ID 4625
Failed logon
These logs appear in Splunk and include useful information such as:

username attempted

source IP address

authentication type

logon failure reason

Example Splunk search:

index=main EventCode=4625
| stats count by Account_Name, Source_Network_Address
This allows analysts to quickly identify brute force attempts and determine the source of the attack.

Key Lessons Learned
Through this lab I gained practical experience with:

configuring a SIEM environment

forwarding endpoint logs

analyzing security events

detecting attacker techniques

This lab demonstrates and helped me understand how SOC analysts use centralized logging tools such as Splunk Enterprise to monitor systems and investigate suspicious activity.




