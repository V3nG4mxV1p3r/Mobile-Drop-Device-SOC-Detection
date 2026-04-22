# 🛡️ Project Phantom: Mobile "Drop Device" SOC Detection Simulation

This project demonstrates an end-to-end SOC laboratory workshop simulating how an unauthorized, root-less Android device (Casper Via S), used as a "Drop Device" within an internal network, is detected via Wazuh SIEM when launching critical Layer 7 attacks (Web Scanning, RCE).

This repository showcases the fusion of Mobile Penetration Testing (Red Team), SIEM Log Integration (Blue Team), and Custom Rule Creation (Detection Engineering) to mitigate the "Shadow Device" threat.

## 🎯 Project Objectives (Executive Summary)
1. **Attack Vector:** Transformation of a standard, root-less mobile device into a functional penetration testing station (drop device) using PRoot Debian without hardware modification (rooting).
2. **Execution:** Launching "Unprivileged" reconnaissance (Nmap) and aggressive application-layer attacks (Nikto) against a target web server (Windows 10 / Apache) on non-standard Port 8080.
3. **Detection & Response:** Configuring Wazuh SIEM to ingest target web server logs and automatically identify critical exploit patterns, specifically the **Shellshock (CVE-2014-6271)** RCE payload, mapped to MITRE ATT&CK tactics.

## 🏗️ Laboratory Architecture & Topology
* **Attacker Unit (Drop Device):** Casper Via S (Android) -> PRoot Debian Container -> Termux via SSH tunnel.
* **Target System:** Windows 10 Host (IP: 192.168.x.x) running XAMPP Apache Web Server (Port: 8080).
* **Security Monitoring (SOC):** Wazuh Server on Ubuntu 22.04 (IP: 192.168.x.x) with Wazuh Agent deployed on the Target.

## ⚔️ Operational Steps

### 1. Reconnaissance (Red Team)
Performed using `nmap --unprivileged` from the mobile device to discover open services. Standard TCP Connect (-sT) scans were utilized due to SELinux restrictions preventing raw socket creation on non-rooted Android devices. The target Apache server was identified on Port 8080.

### 2. Exploitation/Scanning (Red Team)
Initiated an aggressive vulnerability scan using Nikto against the target's internal IP and Port 8080. Nikto attempted to obfuscate its identity while injecting numerous well-known exploit payloads.

### 3. Detection & Incident Response (Blue Team/SOC)
Ingested Apache `access.log` from the Windows 10 target into Wazuh Server via the deployed agent. 

**Critical Log Detail Captured:**
Although Nikto attempted to hide its scanner identity, Wazuh's deep log analysis (Rule 31166) successfully detected the notorious **Shellshock** RCE payload within the request header.

* **Alert Level:** 6 (Elevated)
* **Rule ID:** 31166 (Shellshock attack attempt)
* **CVE:** CVE-2014-6271
* **MITRE ATT&CK Tactic:** T1190 (Exploit Public-Facing Application)
* **Source IP:** 192.168.x.x (The Casper Drop Device)

## 📸 Evidence & Visualization

### A. SOC Monitoring Overview 
This image perfectly captures the project: The mobile "Drop Device" active in hand, and the SOC Dashboard behind it, showing the real-time detection alerts.

<img width="620" height="522" alt="phonelog2" src="https://github.com/user-attachments/assets/0d2c069e-cf8f-4179-9876-f349ff60bf15" />

### B. Technical Proof of Detection
These images show the specific log detail captured by Wazuh SIEM, highlighting the Shellshock payload, the CVE ID, and the MITRE ATT&CK techniques.

<img width="1493" height="607" alt="Wazuh_Shellshock_CVE_Detection" src="https://github.com/user-attachments/assets/2d634e3b-40e9-4f77-b7cb-5b4e6f070494" />

### C. Detection Engineering (Bonus)
In addition to built-in rules, a custom Level 12 rule was created to identify generalized web scanner patterns from unauthorized devices, although in this instance, the specific Shellshock alert (Rule 31166) was prioritized.

<img width="916" height="168" alt="Custom_Wazuh_Rule_Creation" src="https://github.com/user-attachments/assets/2a565e62-a951-4461-b295-b74e9d1514e6" />
