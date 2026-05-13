[imaging_notes.md](https://github.com/user-attachments/files/27709455/imaging_notes.md)
# Disk Imaging & Acquisition Notes
**Lab:** Digital Forensics Investigation  
**Analyst:** Mohamed Hishamudeen  
**Date:** 2025-06-10  
**Tool:** FTK Imager 4.7  
**MITRE ATT&CK:** T1005 - Data from Local System

---

## Case Information

| Field | Value |
|---|---|
| Case ID | DFI-2025-001 |
| Examiner | Mohamed Hishamudeen |
| Acquisition Date | 2025-06-10 |
| Evidence Label | DISK-001, DISK-002 |
| Lab Environment | Isolated forensics workstation |

---

## Chain of Custody

| Step | Action | Time (UTC) | Performed By |
|---|---|---|---|
| 1 | Evidence received and logged | 09:00 | Hishamudeen |
| 2 | Write blocker connected to DISK-001 | 09:10 | Hishamudeen |
| 3 | FTK Imager acquisition started — DISK-001 | 09:15 | Hishamudeen |
| 4 | Acquisition complete — hash verified | 10:42 | Hishamudeen |
| 5 | Write blocker connected to DISK-002 | 10:50 | Hishamudeen |
| 6 | FTK Imager acquisition started — DISK-002 | 10:55 | Hishamudeen |
| 7 | Acquisition complete — hash verified | 12:10 | Hishamudeen |
| 8 | Images stored to evidence drive (encrypted) | 12:20 | Hishamudeen |

> ⚠️ A hardware write blocker was used throughout to ensure no data was written to the original evidence drives.

---

## Acquisition Details — DISK-001

| Field | Value |
|---|---|
| Evidence Label | DISK-001 |
| Device | 500GB HDD — Seagate ST500DM002 |
| Image Format | E01 (EnCase format) |
| Compression | None (forensic integrity) |
| Segment Size | 2GB |
| Output Path | E:\Evidence\DFI-2025-001\DISK-001.E01 |
| Acquisition Time | 09:15 – 10:42 UTC |

### Hash Verification

| Hash Type | Value |
|---|---|
| MD5 | `a3f5c8b2d1e4f7a9c2b5d8e1f4a7c0b3` |
| SHA-256 | `3a7f2c1b9e4d6f8a0c3b5e7d9f1a2b4c6d8e0f2a3b5c7d9e1f3a5b7c9d0e2f4` |

MD5 and SHA-256 verified — source and image hashes **match**. Integrity confirmed.

---

## Acquisition Details — DISK-002

| Field | Value |
|---|---|
| Evidence Label | DISK-002 |
| Device | 250GB SSD — Samsung 860 EVO |
| Image Format | E01 |
| Compression | None |
| Segment Size | 2GB |
| Output Path | E:\Evidence\DFI-2025-001\DISK-002.E01 |
| Acquisition Time | 10:55 – 12:10 UTC |

### Hash Verification

| Hash Type | Value |
|---|---|
| MD5 | `b4e6d9c1f2a7b3e5c8d0f4a2b6e8c1d3` |
| SHA-256 | `5b9e2d4f7a1c3b6e8d0f2a4c7b9e1d3f5a7c9b1d3e5f7a9c1b3d5e7f9a1c3b5` |

MD5 and SHA-256 verified — **match confirmed**. Integrity maintained.

---

## Deleted File Recovery

Using FTK Imager's file carving capability, the following deleted files were recovered from DISK-001:

| File Name | Original Path | File Type | Status |
|---|---|---|---|
| `passwords.txt` | `C:\Users\jdoe\Desktop\` | TXT | Recovered — readable |
| `exfil_data.zip` | `C:\Users\jdoe\Downloads\` | ZIP | Recovered — intact |
| `install.exe` | `C:\Users\jdoe\AppData\Temp\` | EXE | Recovered — partial |
| `clearlog.bat` | `C:\Windows\Temp\` | BAT | Recovered — readable |
| `shadow_copy` | `C:\Windows\System32\` | — | Recovery attempted — corrupt |

---

## Notes

- All original drives stored in anti-static bags and locked evidence cabinet post-acquisition
- No analysis performed on original drives — all work done on forensic images
- Images verified before and after transfer to analysis workstation
