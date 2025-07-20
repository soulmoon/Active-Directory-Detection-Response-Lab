# 🛡️ Active Directory Detection & Response Lab

This lab simulates a real-world enterprise environment to explore detection and response techniques using **Splunk**, **Sysmon**, **Windows Active Directory**, **Kali Linux**, and **Atomic Red Team**. The project focuses on event collection, monitoring, and adversary emulation through tools like Crowbar and Atomic Red Team.

---

## 🧠 Objective

- Set up an enterprise-like environment with Windows AD and Splunk logging.
- Forward logs from Windows hosts to Splunk.
- Simulate attacks (Brute Force, Atomic Red Team) using Kali Linux.
- Detect and analyze events (e.g., EventCodes 4624, 4625, T1136.001, T1059.001).

---

## 🖥️ Lab Environment

| Machine         | Role                        | IP Address         |
|----------------|-----------------------------|--------------------|
| Ubuntu Server  | Splunk Server               | 192.168.116.137    |
| Windows Server | Active Directory Domain     | 192.168.116.130    |
| Windows 10 PC  | Domain-Joined Target        | 192.168.116.132    |
| Kali Linux     | Attacker Machine            | 192.168.116.131    |

---

## 🔧 Tools & Services

- **Splunk Enterprise 9.4.3**
- **Splunk Universal Forwarder**
- **Sysmon with Olaf config**
- **Atomic Red Team (ART)**
- **Crowbar (RDP brute force tool)**
- **MITRE ATT&CK Framework**
- **Windows AD DS, DNS**
- **Kali Linux (Offensive toolset)**

---

## 📦 Splunk Server Setup (Ubuntu)

```bash
wget -O splunk-9.4.3.tgz https://download.splunk.com/products/splunk/releases/9.4.3/linux/splunk-9.4.3-237ebbd22314-linux-amd64.tgz
sudo tar -xvzf splunk-9.4.3.tgz -C /opt
sudo /opt/splunk/bin/splunk start --accept-license
sudo /opt/splunk/bin/splunk enable boot-start
Admin Login: splunk / Splunk@2025
Web Access: http://192.168.116.137:8000


Endpoint Configuration (Windows Server & Win 10)
Install Sysmon
Download Sysmon from Microsoft Sysinternals.

Use Olaf config to set rules.

Run:.\Sysmon.exe -i sysmonconfig.xml

Install Universal Forwarder
Install Splunk Universal Forwarder.

Create inputs.conf in:

C:\Program Files\SplunkUniversalForwarder\etc\system\local\

[WinEventLog://Application]
index = endpoint
disabled = false

[WinEventLog://Security]
index = endpoint
disabled = false

[WinEventLog://System]
index = endpoint
disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
index = endpoint
disabled = false
renderXml = true
Start the SplunkForwarder service using Local System Account.

🧑‍💻 Active Directory Configuration
Promote Windows Server to Domain Controller.
Create a domain (e.g., corp.local)
Create OU IT
Create Users:
dipak
dipesh

Join Win10 to Domain.
Update DNS to 192.168.116.130 (AD Server IP)


🧪 Simulated Attack: Brute Force via Crowbar (Kali Linux)
sudo apt update && sudo apt install -y crowbar freerdp3-x11
cd ~/Desktop/ad-project
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
cp /usr/share/wordlists/rockyou.txt .
head -n 30 rockyou.txt > passwords.txt
nano passwords.txt  # Add known test passwords

Run Attack: crowbar -b rdp -u dipak.pakhrin -C passwords.txt -s 192.168.116.132/32

🔍 Splunk Detection
Search Brute Force Attempts:
index=endpoint EventCode=4625 dipak.pakhrin

Search Successful Logins:
index=endpoint EventCode=4624
Look for Workstation Name: kali in logs to confirm attack source.

⚙️ Atomic Red Team (ART) Setup on Win10
Set-ExecutionPolicy Bypass -Scope CurrentUser
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)
Install-AtomicRedTeam -getAtomics

Run Tests:
Test 1: T1136.001 — Local Account Creation
Invoke-AtomicTest T1136.001

Test 2: T1059.001 — PowerShell Execution
Invoke-AtomicTest T1059.001

Search logs in Splunk after a few minutes:
index=endpoint EventCode=4720  # For account creation
index=endpoint EventCode=4104  # For PowerShell script block logging

🧠 Key Learnings
Real-time log forwarding with Sysmon and Universal Forwarder
Detection delay observed in ART-generated events
Importance of tuning Splunk to reduce detection latency
Simulated real-world RDP brute force and detection using Splunk

📁 Project Folder (on Kali)
ad-project/
├── rockyou.txt
├── passwords.txt
└── crowbar_output.txt

🧑 Author
Dipak Pakhrin

