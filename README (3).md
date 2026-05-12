# 🌐 Network Penetration Testing Lab

> Controlled lab environment for vulnerability enumeration, exploitation, and traffic analysis using industry-standard tools.

---

## Overview

This project covers a structured penetration testing workflow performed in an isolated lab environment. The focus is on reconnaissance, vulnerability identification, validation, and network traffic analysis — following an ethical hacking methodology throughout.

---

## 🛠️ Tools & Technologies

| Category | Tools |
|---|---|
| Enumeration | Nmap |
| Vulnerability Scanning | OpenVAS |
| Exploitation | Metasploit |
| Traffic Analysis | Wireshark |
| Platform | Kali Linux |
| Framework | CVSS (severity scoring) |

---

## 📋 What Was Done

### Enumeration & Service Fingerprinting
- Identified **15+ open ports and services** using Nmap
- Performed banner grabbing and service version detection to build a full attack surface picture
- Mapped findings to known CVEs for prioritisation

### Vulnerability Assessment
- Ran OpenVAS scans and prioritised findings by **CVSS severity score**
- Filtered out false positives and focused remediation effort on critical/high findings

### Exploitation & Validation
- Validated identified vulnerabilities in controlled lab environments using Metasploit
- Confirmed exploitability without causing damage — focused on proof-of-concept only

### Network Traffic Analysis
- Captured live PCAP traffic using Wireshark
- Identified the following across TCP/IP, DNS, and HTTP:
  - **ARP spoofing attempts**
  - **Cleartext credential transmission**
  - **Anomalous traffic patterns** indicative of scanning or C2 activity

---

## 🗂️ MITRE ATT&CK Techniques Covered

| Technique ID | Name |
|---|---|
| T1046 | Network Service Scanning |
| T1190 | Exploit Public-Facing Application |
| T1040 | Network Sniffing |
| T1557 | Adversary-in-the-Middle (ARP Spoofing) |
| T1552.001 | Credentials in Files (Cleartext) |

---

## 📁 Repository Structure

```
network-pentest-lab/
├── enumeration/
│   └── nmap_scan_notes.md
├── vulnerability-assessment/
│   └── openvas_findings.md
├── exploitation/
│   └── metasploit_notes.md
├── traffic-analysis/
│   └── wireshark_findings.md
└── README.md
```

---

## ⚠️ Disclaimer

All activities were performed in a **controlled, isolated lab environment** for educational purposes only. No systems were harmed and no unauthorised access was performed.

---

## 🔑 Key Takeaways

- Learned to approach a target systematically: enumerate → assess → validate → document
- Understood how attackers move from open ports to exploitable vulnerabilities
- Recognised dangerous network patterns (ARP spoofing, cleartext creds) that defenders should monitor for
- Gained experience translating raw scan output into prioritised, actionable findings

---

## 📬 Contact

**Mohamed Hishamudeen**
[LinkedIn](https://linkedin.com/in/mohamed-hishamudeen) · [mohmadhisham68@gmail.com](mailto:mohmadhisham68@gmail.com)
