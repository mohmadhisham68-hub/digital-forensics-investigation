[README (2).md](https://github.com/user-attachments/files/27705887/README.2.md)
# 🗂️ Digital Forensics Investigation

> End-to-end forensic investigation — disk imaging, evidence recovery, timeline reconstruction, and formal report writing.

---

## Overview

This project simulates a real-world digital forensics investigation across a multi-user environment. Following chain-of-custody procedures throughout, I acquired disk images, recovered deleted artefacts, identified IOCs, and reconstructed a full attacker timeline — culminating in a structured forensic report with remediation recommendations.

---

## 🛠️ Tools & Technologies

| Category | Tools |
|---|---|
| Disk Imaging | FTK Imager |
| Forensic Analysis | Autopsy |
| Database Artefacts | SQLite Browser |
| Evidence Types | File system, logs, browser history, timestamps |

---

## 📋 What Was Done

### Disk Imaging & Acquisition
- Acquired disk images using FTK Imager following proper forensic procedures
- Maintained **chain-of-custody** throughout to preserve evidence integrity
- Recovered deleted files from acquired images for further analysis

### IOC Identification
Identified indicators of unauthorised access across the environment, including:
- Suspicious file modifications
- Deleted or tampered system logs
- Anomalous timestamps inconsistent with normal user activity
- Evidence of access across multiple user accounts

### Timeline Reconstruction
- Used Autopsy to correlate artefacts across file system metadata, log files, and browser history
- Reconstructed a **full 6-stage attacker timeline** from initial access to post-compromise activity
- Cross-referenced file system timestamps with log entries to eliminate gaps

### Forensic Report
- Produced a structured forensic report including:
  - Full incident timeline
  - IOC list with supporting evidence
  - Chain-of-custody documentation
  - Remediation and hardening recommendations

---

## 🗂️ Investigation Stages

| Stage | Description |
|---|---|
| 1 | Initial access — entry point identified |
| 2 | Privilege escalation or lateral movement |
| 3 | Data access or exfiltration activity |
| 4 | Log tampering / anti-forensics |
| 5 | Persistence mechanisms |
| 6 | Post-compromise cleanup attempts |

---

## 📁 Repository Structure

```
digital-forensics-investigation/
├── acquisition/
│   └── imaging_notes.md          # FTK Imager process and hash verification
├── analysis/
│   ├── ioc_list.md               # All identified IOCs with evidence references
│   ├── timeline.md               # Full 6-stage attacker timeline
│   └── artefacts_notes.md        # File system, log, and browser history findings
├── reports/
│   └── forensic_report.md        # Final structured report with remediation steps
└── README.md
```

---

## 🔑 Key Takeaways

- Understood the importance of chain-of-custody and forensic integrity in evidence handling
- Learned how attackers attempt to cover their tracks (log deletion, timestamp manipulation)
- Developed the ability to tell a coherent story from fragmented digital evidence
- Practised writing professional forensic reports suitable for legal or management audiences

---

## ⚠️ Disclaimer

This investigation was conducted in a **simulated lab environment** using provided disk images for educational purposes only.

---

## 📬 Contact

**Mohamed Hishamudeen**
[LinkedIn](https://linkedin.com/in/mohamed-hishamudeen) · [mohmadhisham68@gmail.com](mailto:mohmadhisham68@gmail.com)
