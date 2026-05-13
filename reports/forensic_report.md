[forensic_report.md](https://github.com/user-attachments/files/27709912/forensic_report.md)
# Forensic Investigation Report — DFI-2025-001
**Classification:** Confidential  
**Case ID:** DFI-2025-001  
**Analyst:** Mohamed Hishamudeen  
**Date:** 2025-06-12  
**Status:** Final

---

## Executive Summary

A forensic investigation was conducted on two disk images (DISK-001, DISK-002) seized from a suspected compromised workstation. Analysis revealed a structured, multi-stage attack beginning with an RDP brute-force at 08:30 UTC on 2025-05-28, culminating in data exfiltration and anti-forensic cleanup by 09:40 UTC.

The attacker achieved full system access, escalated privileges, installed multiple persistence mechanisms, accessed and exfiltrated sensitive data, and attempted to erase evidence. Despite this, forensic artefacts including PowerShell history, prefetch files, and browser SQLite databases survived and provided a near-complete reconstruction of the attack.

---

## Scope & Methodology

| Item | Detail |
|---|---|
| Evidence | DISK-001 (500GB HDD), DISK-002 (250GB SSD) |
| Tools Used | FTK Imager, Autopsy, SQLite Browser |
| Methodology | ACPO Guidelines, chain-of-custody maintained |
| Analysis Period | 2025-06-10 to 2025-06-12 |

All work performed on forensic images — original evidence untouched.

---

## Attack Timeline

| Stage | Time (UTC) | MITRE Technique | Summary |
|---|---|---|---|
| 1 — Initial Access | 08:30 – 08:42 | T1110.001, T1078 | RDP brute-force → successful logon as `jdoe` |
| 2 — Execution | 08:45 – 09:02 | T1059.001, T1204.002 | Payload downloaded from C2 and executed |
| 3 — Privilege Escalation | 09:05 | T1068, T1134 | Local exploit → admin-level access |
| 4 — Persistence | 09:12 | T1547.001, T1053.005 | Registry key, scheduled task, startup shortcut |
| 5 — Data Exfiltration | 09:20 – 09:25 | T1005, T1560.001, T1041 | Files staged and 52MB exfiltrated to C2 |
| 6 — Anti-Forensics | 09:30 – 09:40 | T1070.001, T1070.006 | Logs cleared, VSS deleted, timestamps altered |

---

## Key Findings

### 1. Compromised Account
User account `jdoe` was compromised via RDP brute-force. No MFA or account lockout policy was in place.

### 2. Malicious Payload
`payload.exe` was downloaded from `http://185.220.101.47/payload.exe` and executed at 08:52 UTC. The file was recovered from unallocated disk space despite attempted deletion.

### 3. Privilege Escalation
A local privilege escalation exploit was used to gain `SeDebugPrivilege` and `SeImpersonatePrivilege`, followed by access to an `admin`-level session.

### 4. Persistence (3 Methods)
Three separate persistence mechanisms were installed to survive reboots:
- Registry Run key
- Scheduled task (`WindowsUpdateHelper`)
- Startup folder shortcut

### 5. Data Exfiltration
`passwords.txt` and HR directory files were compressed into `exfil_data.zip` and transferred to C2 IP `185.220.101.47` (~52MB).

### 6. Anti-Forensics
- Windows Security and System logs cleared via `wevtutil`
- VSS shadow copies deleted via `vssadmin`
- File timestamps manipulated (timestomping) on `payload.exe` and `clearlog.bat`

Despite these efforts, the following artefacts survived and were instrumental in the investigation:
- PowerShell `ConsoleHost_history.txt`
- Prefetch files
- Chrome browser SQLite databases
- MFT entries for deleted files

---

## IOC Summary

| Type | Value |
|---|---|
| IP (C2) | `185.220.101.47` |
| IP (Pivot) | `192.168.1.50` |
| Domain | `update-service.net` |
| File | `payload.exe` — MD5: `d41d8cd98f00b204e9800998ecf8427e` |
| File | `clearlog.bat` — MD5: `b026324c6904b2a9cb4b88d6d61c81d1` |
| Account | `jdoe` (compromised), `hacker_tmp` (created by attacker) |

---

## Recommendations

| Priority | Recommendation |
|---|---|
| CRITICAL | Enable MFA on all accounts — especially RDP-accessible ones |
| CRITICAL | Implement account lockout policy — 5 attempts, 30-minute lockout |
| HIGH | Block RDP (3389) at perimeter — use VPN + jump host instead |
| HIGH | Deploy centralised SIEM — forward logs off-host to prevent clearing |
| HIGH | Block outbound connections to unknown IPs — allowlist-based egress |
| MEDIUM | Enable PowerShell ScriptBlock Logging and forward to SIEM |
| MEDIUM | Enforce application allowlisting to prevent unsigned binary execution |
| LOW | Enable Tamper Protection on Windows Defender |
| LOW | Regular shadow copy / backup verification |

---

## Conclusion

The attack was well-structured and deliberate, consistent with a threat actor with intermediate-to-advanced capability. The anti-forensic techniques used suggest awareness of investigation methods. However, the attacker failed to account for PowerShell history and browser artefacts, which enabled full reconstruction of the attack chain.

All evidence has been preserved in accordance with chain-of-custody procedures and is available for legal or management review upon request.

---

*Report prepared by Mohamed Hishamudeen | DFI-2025-001 | 2025-06-12*
