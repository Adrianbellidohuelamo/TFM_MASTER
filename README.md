# 🛡️ Red Team / Blue Team Lab — Attack Detection with Wazuh SIEM

> **Master's Thesis** | Cybersecurity, Ethical Hacking & Offensive Security  
> EIP International Business School · 2025/2026  
> **Authors:** Christian Antony Miranda Toliano & Adrián Bellido Huélamo

---

## 📌 Overview

This project presents the **design, deployment and evaluation of a fully functional Red Team / Blue Team cybersecurity laboratory**, built from scratch using open-source technologies.

The lab simulates a realistic corporate security incident — from initial phishing access to lateral movement and domain compromise — while the Blue Team monitors, detects and responds using a SIEM platform.

---

## 🎯 Objectives

- Deploy a segmented virtual corporate network from scratch (Proxmox + OPNsense)
- Simulate a realistic attack chain: phishing → reverse shell → credential theft → lateral movement
- Detect and respond to the attack using Wazuh SIEM + Sysmon telemetry
- Map all techniques to the **MITRE ATT&CK** framework
- Measure detection and response effectiveness (MTTD / MTTR)

---

## 🏗️ Lab Architecture

```
Internet
   │
[Proxmox Hypervisor]
   │
[OPNsense Firewall]
   ├── Blue Team LAN (10.0.0.0/24)
   │     ├── Windows Server AD/DC     (10.0.0.10)
   │     ├── Windows Client (victim)  (10.0.0.20)
   │     └── Ubuntu + Wazuh SIEM      (10.0.0.40)
   │
   └── Red Team LAN (10.20.0.0/24)
         └── Kali Linux (attacker)    (10.20.0.50)
```

Both networks are isolated by default. The Red Team can only interact with the Blue Team through controlled attack scenarios.

---

## 🔧 Technologies Used

| Category | Tools |
|---|---|
| Hypervisor | Proxmox VE |
| Firewall / Segmentation | OPNsense |
| SIEM / HIDS | Wazuh (Manager + Indexer + Dashboard) |
| Endpoint Telemetry | Sysmon (EventID 1, 3, 11, 22) |
| Active Directory | Windows Server 2022 |
| Attack Platform | Kali Linux |
| Phishing | Gophish |
| Payload Generation | msfvenom / Metasploit Framework |
| Credential Access | KeePass (simulated corporate vault) |
| Lateral Movement | PowerShell Remoting (WinRM) |

---

## ⚔️ Attack Scenario

The simulated attack follows a realistic kill chain:

1. **Initial Access** — Spearphishing campaign via Gophish targeting corporate users
2. **Execution** — User downloads and executes malicious payload (`msfvenom` reverse shell)
3. **Post-Exploitation** — Internal reconnaissance, shared drive enumeration
4. **Credential Access** — KeePass corporate vault exfiltrated via Netcat
5. **Lateral Movement** — PowerShell Remoting (WinRM) to Domain Controller using stolen credentials
6. **Detection & Response** — Wazuh alerts triggered, attacker IP identified via Sysmon Event ID 3, WinRM blocked at firewall level

---

## 🔍 MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| Initial Access | Phishing: Spearphishing Link | T1566.002 |
| Execution | User Execution: Malicious File | T1204.002 |
| Execution | PowerShell | T1059.001 |
| Execution | Windows Command Shell | T1059.003 |
| Credential Access | Credentials from Password Managers | T1555.005 |
| Lateral Movement | Remote Services: WinRM | T1021.006 |
| Command & Control | Web Protocol | T1071.001 |

---

## 📊 Detection Results

| Wazuh Rule | Description | Severity |
|---|---|---|
| 92052 | Windows command prompt started by abnormal process | 4 |
| 92110 | WinRM activity detected between endpoints | 4 |
| 92213 | Executable dropped in folder commonly used by malware | 15 |
| 92217 | Executable dropped in Windows root folder | 6 |

- **MTTD** (Mean Time To Detect): measured from payload execution to first Wazuh alert
- **MTTR** (Mean Time To Respond): measured from detection to containment (firewall block)

---

## 🛡️ Blue Team Response

1. Identified compromised host via Wazuh alerts (rules 92052, 92110, 92213)
2. Located attacker IP via Sysmon Event ID 3 (NetworkConnect) forensic analysis
3. Blocked WinRM ports (TCP 5985/5986) from compromised host in OPNsense
4. Blocked all traffic from attacker IP (10.20.0.50) at firewall level
5. Revoked and reset exposed credentials (svc_backup account)
6. Cleaned malicious artefacts from compromised endpoint
7. Re-admitted endpoint to network after integrity validation

---

## 📁 Repository Structure

```
📦 tfm-blueteam-wazuh
 ┣ 📄 TFMEIPAYC2025-28.pdf       # Full thesis document (ES)
 ┗ 📄 README.md                  # This file
```

---

## ⚠️ Disclaimer

This lab was designed and executed exclusively for **academic and educational purposes** in a fully isolated virtual environment. No real systems or third-party infrastructure were affected. All attack techniques were performed under controlled conditions and documented for defensive analysis only.

---

## 👤 Author

**Adrián Bellido Huélamo**  
Infrastructure & Systems Technician | Cybersecurity Specialist  
🔗 [LinkedIn](https://www.linkedin.com/in/adrianbellidohuelamo/)  
🌐 [Portfolio](https://soloist.ai/adrianbellidohuelamo)
