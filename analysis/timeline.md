[timeline.md](https://github.com/user-attachments/files/27709598/timeline.md)
# Attacker Timeline — DFI-2025-001
**Lab:** Digital Forensics Investigation  
**Analyst:** Mohamed Hishamudeen  
**Reconstructed using:** Autopsy, Windows Event Logs, Browser History, Prefetch, Registry

---

## Timeline Overview

| Stage | Name | Time (UTC) | Summary |
|---|---|---|---|
| 1 | Initial Access | 08:30 | Attacker gains foothold via RDP brute-force |
| 2 | Execution | 08:48 | Malicious payload downloaded and executed |
| 3 | Privilege Escalation | 09:05 | Attacker escalates to admin-level access |
| 4 | Persistence | 09:12 | Multiple persistence mechanisms installed |
| 5 | Data Access & Exfiltration | 09:20 | Sensitive files accessed and staged for exfiltration |
| 6 | Anti-Forensics & Cleanup | 09:30 | Logs cleared, shadow copies deleted, timestamps manipulated |

---

## Stage 1 — Initial Access
**Time:** 2025-05-28 08:30 – 08:42 UTC  
**MITRE:** T1110.001 (Brute Force), T1078 (Valid Accounts)

| Time | Event ID | Details |
|---|---|---|
| 08:30:01 | 4625 | Failed RDP logon — `jdoe` from `192.168.1.50` |
| 08:30:14 | 4625 | Failed RDP logon — attempt 2 |
| 08:30:28 | 4625 | Failed RDP logon — attempt 3 |
| 08:31:05 | 4625 | Failed RDP logon — attempt 4 |
| 08:31:22 | 4625 | Failed RDP logon — attempt 5 |
| 08:42:10 | 4624 | **Successful RDP logon** — `jdoe` from `192.168.1.50` (Logon Type 10) |

**Evidence Sources:** Windows Security Event Log (DISK-001)  
**Analysis:** After 5 failed attempts, attacker successfully authenticated using correct credentials for `jdoe`. The 10-minute gap between last failure and success suggests a brief pause — possibly switching to a credential list or using a different tool.

---

## Stage 2 — Execution
**Time:** 2025-05-28 08:45 – 09:02 UTC  
**MITRE:** T1059.001 (PowerShell), T1204.002 (Malicious File)

| Time | Artefact | Details |
|---|---|---|
| 08:45:00 | Browser History | `jdoe` visited `http://update-service.net` |
| 08:48:00 | Browser History | Downloaded `payload.exe` from `http://185.220.101.47/payload.exe` |
| 08:49:00 | File System | `payload.exe` created in `C:\Users\jdoe\AppData\Temp\` |
| 08:52:00 | Prefetch | `payload.exe` executed — first run recorded in prefetch |
| 09:00:00 | Event ID 4688 | `powershell.exe -ExecutionPolicy Bypass -File payload.ps1` launched by `jdoe` |

**Evidence Sources:** Chrome History (SQLite), File System (Autopsy), Prefetch files, Event ID 4688  
**Analysis:** Attacker downloaded payload via browser, executed it, and used PowerShell to run a secondary script — bypassing execution policy restrictions.

---

## Stage 3 — Privilege Escalation
**Time:** 2025-05-28 09:05 UTC  
**MITRE:** T1068 (Exploitation for Privilege Escalation), T1134 (Access Token Manipulation)

| Time | Artefact | Details |
|---|---|---|
| 09:05:12 | Event ID 4672 | Special privileges assigned to `jdoe` — SeDebugPrivilege, SeImpersonatePrivilege |
| 09:05:30 | Event ID 4688 | `install.exe` executed — dropper for privilege escalation tool |
| 09:06:00 | Event ID 4624 | New logon session for `admin` account initiated |

**Evidence Sources:** Windows Security Event Log, Prefetch  
**Analysis:** Payload triggered a local privilege escalation, granting `jdoe` debug and impersonation privileges, then opening an admin-level session.

---

## Stage 4 — Persistence
**Time:** 2025-05-28 09:12 UTC  
**MITRE:** T1547.001 (Registry Run Keys), T1053.005 (Scheduled Tasks)

| Time | Artefact | Details |
|---|---|---|
| 09:12:00 | Registry | `payload.exe` added to `HKCU\...\Run` key |
| 09:13:00 | Task Scheduler | `WindowsUpdateHelper` task created — runs `payload.exe` hourly |
| 09:14:00 | Startup Folder | `update.lnk` shortcut created pointing to `payload.exe` |

**Evidence Sources:** Registry hive (Autopsy), Task Scheduler XML files, Startup folder  
**Analysis:** Three separate persistence mechanisms installed — registry run key, scheduled task, and startup shortcut. Demonstrates attacker's intention to maintain access across reboots.

---

## Stage 5 — Data Access & Exfiltration
**Time:** 2025-05-28 09:20 UTC  
**MITRE:** T1005 (Data from Local System), T1560.001 (Archive Collected Data), T1041 (Exfiltration Over C2 Channel)

| Time | Artefact | Details |
|---|---|---|
| 09:20:00 | File Access | `passwords.txt` accessed from `C:\Users\jdoe\Desktop\` |
| 09:21:00 | File Access | HR documents accessed from `C:\Shared\HR\` |
| 09:22:00 | File Creation | `exfil_data.zip` created in Downloads — contains passwords.txt and HR files |
| 09:25:00 | Network | Outbound connection to `185.220.101.47` — ~52MB transferred |

**Evidence Sources:** File system timestamps (Autopsy), Network connections (Event Log), Zip archive recovered (FTK Imager)  
**Analysis:** Attacker accessed sensitive files, compressed them into an archive, and transferred to the C2 IP. The 52MB transfer is consistent with the recovered archive size.

---

## Stage 6 — Anti-Forensics & Cleanup
**Time:** 2025-05-28 09:30 UTC  
**MITRE:** T1070.001 (Clear Windows Event Logs), T1070.004 (File Deletion), T1070.006 (Timestomping)

| Time | Artefact | Details |
|---|---|---|
| 09:30:00 | PowerShell History | `vssadmin delete shadows /all` — deleted all VSS shadow copies |
| 09:31:00 | File Recovery | `clearlog.bat` executed — `wevtutil cl Security` and `wevtutil cl System` |
| 09:32:00 | Event Log Gap | Security log gap confirmed 09:30 – end of session |
| 09:33:00 | File System | Timestamps on `payload.exe` and `clearlog.bat` manipulated (timestomping) |
| 09:40:00 | Event ID 4634 | `jdoe` session logged off — RDP session terminated |

**Evidence Sources:** PowerShell history, Recovered batch file, File system metadata inconsistencies, VSS status  
**Analysis:** Attacker attempted to cover tracks via log clearing and timestamp manipulation. However, PowerShell history and prefetch files survived and provided strong evidence of the full attack chain.

---

## Full Timeline Summary

```
08:30 ──► RDP brute-force begins
08:42 ──► Successful logon (jdoe)
08:45 ──► Browser used to reach C2 domain
08:48 ──► payload.exe downloaded
08:52 ──► payload.exe executed
09:00 ──► PowerShell execution
09:05 ──► Privilege escalation
09:12 ──► Persistence installed (3 methods)
09:20 ──► Sensitive files accessed
09:22 ──► Data compressed and staged
09:25 ──► 52MB exfiltrated to 185.220.101.47
09:30 ──► Shadow copies deleted
09:31 ──► Event logs cleared
09:33 ──► Timestamps manipulated
09:40 ──► Attacker disconnects
```
