## SOC Analyst Simulation Lab 

In this lab I will run an SOC Lab and attempt to simulate real world tasks in which a SOC analyst might be doing. In this lab I will be running 3 virtual machines consisting of; Ubuntu server 22.04 (splunk host), Windows 10 (Splunk forwarder and Sysmon log ingester), and finally, a Kali box (attacker).

I will go through the entire setup process for this lab and at the end I will create some common event logs to simulate attacks and search for said attacks through splunk queries. 

I set up the Ubuntu VM first by installing splunk enterprise and initilaizing the splunk server with the following command: sudo /opt/splunk/bin/splunk start --run-as-root 



