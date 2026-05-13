[ioc_list.md](https://github.com/user-attachments/files/27709513/ioc_list.md)
# IOC List — DFI-2025-001
**Lab:** Digital Forensics Investigation  
**Analyst:** Mohamed Hishamudeen  
**Date:** 2025-06-11  
**Tool:** Autopsy, FTK Imager, SQLite Browser

---

## Overview

The following indicators of compromise were identified during forensic analysis of DISK-001 and DISK-002. Each IOC is tagged with its source artefact, discovery method, and relevance to the attack timeline.

---

## Network IOCs

| Type | Value | Context | Confidence |
|---|---|---|---|
| IP Address | `185.220.101.47` | Found in browser history — attacker C2 server | HIGH |
| IP Address | `192.168.1.50` | Source of RDP logon — internal pivot point | HIGH |
| Domain | `update-service.net` | Resolved in DNS cache — malicious domain | HIGH |
| URL | `http://185.220.101.47/payload.exe` | Download URL found in PowerShell history | HIGH |
| URL | `http://update-service.net/c2` | Beaconing URL found in prefetch artefacts | MEDIUM |

---

## File IOCs

| File Name | Path | Hash (MD5) | Context |
|---|---|---|---|
| `payload.exe` | `C:\Users\jdoe\AppData\Temp\` | `d41d8cd98f00b204e9800998ecf8427e` | Malicious executable — downloaded from C2 |
| `clearlog.bat` | `C:\Windows\Temp\` | `b026324c6904b2a9cb4b88d6d61c81d1` | Batch script to clear Windows Event Logs |
| `exfil_data.zip` | `C:\Users\jdoe\Downloads\` | `26ab0db90d72e28ad0ba1e22ee510510` | Compressed archive — likely staged for exfiltration |
| `passwords.txt` | `C:\Users\jdoe\Desktop\` | `6d7fce9fee471194aa8b5b6e47267f03` | Plaintext credential store — accessed by attacker |
| `install.exe` | `C:\Users\jdoe\AppData\Temp\` | `48a24b70a0b376535542b996af517398` | Secondary dropper — partial recovery |

---

## Account IOCs

| Username | Context | Evidence Source |
|---|---|---|
| `jdoe` | Primary compromised account | Event logs, file metadata |
| `admin` | Privilege escalation target | Security event log 4672 |
| `svc_backup` | Service account abused for lateral movement | Event ID 4624 — logon type 3 |
| `hacker_tmp` | New local account created by attacker | Event ID 4720 |

---

## Persistence IOCs

| Type | Location | Value | Context |
|---|---|---|---|
| Registry Run Key | `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` | `payload.exe` | Persistence mechanism — executes on logon |
| Scheduled Task | `C:\Windows\System32\Tasks\` | `WindowsUpdateHelper` | Fake task — runs payload.exe every hour |
| Startup Folder | `C:\Users\jdoe\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\` | `update.lnk` | Shortcut pointing to payload.exe |

---

## Log Tampering IOCs

| Evidence | Details | Significance |
|---|---|---|
| `clearlog.bat` recovered | Batch script containing `wevtutil cl System` and `wevtutil cl Security` | Attacker attempted to clear Windows logs |
| Gap in Security Event Log | No events recorded between 08:45 – 09:30 on 2025-05-28 | Consistent with log clearing activity |
| Deleted VSS copies | Shadow copies deleted via `vssadmin delete shadows /all` found in PowerShell history | Prevented recovery via restore points |

---

## Timestamp Anomalies

| File | Created | Modified | Accessed | Anomaly |
|---|---|---|---|---|
| `payload.exe` | 2025-05-28 08:52 | 2025-05-20 14:00 | 2025-05-28 09:01 | Modified date predates created date — timestomping suspected |
| `clearlog.bat` | 2025-05-28 09:15 | 2025-05-15 11:00 | 2025-05-28 09:16 | Same pattern — modified before created |

> **Note:** Timestomping (T1070.006) is a common anti-forensics technique to mislead investigators about when files were placed on a system.

---

## Browser Artefacts (SQLite Browser)

Database: `C:\Users\jdoe\AppData\Local\Google\Chrome\User Data\Default\History`

| Type | Value | Timestamp |
|---|---|---|
| URL Visited | `http://185.220.101.47/payload.exe` | 2025-05-28 08:48 UTC |
| URL Visited | `http://update-service.net` | 2025-05-28 08:45 UTC |
| Download | `payload.exe` — saved to AppData\Temp | 2025-05-28 08:49 UTC |
| Search | `"how to clear windows event logs"` | 2025-05-28 09:10 UTC |
