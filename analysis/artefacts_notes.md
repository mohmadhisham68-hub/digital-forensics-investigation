
# Forensic Artefacts — Analysis Notes
**Lab:** Digital Forensics Investigation  
**Analyst:** Mohamed Hishamudeen  
**Tools:** Autopsy, FTK Imager, SQLite Browser

---

## 1. File System Artefacts (Autopsy)

### Deleted Files Recovered

| File | Path | Recovery Method | Readable |
|---|---|---|---|
| `passwords.txt` | `C:\Users\jdoe\Desktop\` | Unallocated space carving | Yes |
| `exfil_data.zip` | `C:\Users\jdoe\Downloads\` | MFT entry recovery | Yes |
| `clearlog.bat` | `C:\Windows\Temp\` | MFT entry + unallocated | Yes |
| `payload.exe` | `C:\Users\jdoe\AppData\Temp\` | MFT entry recovery | Yes |

### MFT Analysis
- Master File Table parsed to recover deleted entries
- $I30 index entries in directory nodes revealed files deleted from Desktop
- Timestamps extracted from MFT records and cross-referenced with log data

---

## 2. Windows Event Log Artefacts

### Key Events Identified

| Event ID | Meaning | Count Found | Significance |
|---|---|---|---|
| 4625 | Failed Logon | 5 | Brute-force pattern |
| 4624 | Successful Logon | 2 | `jdoe` and `admin` sessions |
| 4688 | Process Creation | 8 | Malicious processes tracked |
| 4672 | Special Privileges | 1 | Privilege escalation confirmed |
| 4720 | Account Created | 1 | `hacker_tmp` account created |
| 4698 | Scheduled Task Created | 1 | `WindowsUpdateHelper` persistence |
| 4634 | Logoff | 1 | Session end |

### Log Gap
- Security event log shows a gap from **09:30 – end of session**
- Consistent with `wevtutil cl Security` execution at 09:31
- System log similarly cleared

---

## 3. Prefetch Files

Prefetch files provide evidence of program execution even after deletion.

| Prefetch File | Executable | Last Run | Run Count |
|---|---|---|---|
| `PAYLOAD.EXE-XXXXXXXX.pf` | payload.exe | 2025-05-28 08:52 | 3 |
| `CLEARLOG.BAT-XXXXXXXX.pf` | clearlog.bat | 2025-05-28 09:31 | 1 |
| `INSTALL.EXE-XXXXXXXX.pf` | install.exe | 2025-05-28 09:05 | 1 |
| `POWERSHELL.EXE-XXXXXXXX.pf` | powershell.exe | 2025-05-28 09:00 | 4 |

> Prefetch files survived the log clearing and provided critical corroboration of execution timeline.

---

## 4. Browser History (SQLite Browser)

**Database:** `C:\Users\jdoe\AppData\Local\Google\Chrome\User Data\Default\History`  
**Table:** `urls`, `downloads`, `keyword_search_terms`

### URLs Table (Key Entries)

```sql
SELECT url, title, visit_count, last_visit_time FROM urls ORDER BY last_visit_time DESC;
```

| URL | Last Visited | Visit Count |
|---|---|---|
| `http://update-service.net` | 2025-05-28 08:45 | 1 |
| `http://185.220.101.47/payload.exe` | 2025-05-28 08:48 | 1 |
| `https://www.google.com/search?q=how+to+clear+windows+event+logs` | 2025-05-28 09:10 | 1 |

### Downloads Table

```sql
SELECT target_path, tab_url, start_time, received_bytes FROM downloads;
```

| File | Source URL | Time | Size |
|---|---|---|---|
| `payload.exe` | `http://185.220.101.47/payload.exe` | 08:48 | 2.4MB |

---

## 5. Registry Artefacts

### Run Keys (Persistence)

**Hive:** `NTUSER.DAT` — `jdoe` profile  
**Key:** `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`

```
Value Name: WindowsHelper
Value Data: C:\Users\jdoe\AppData\Temp\payload.exe
```

### UserAssist (Program Execution)

UserAssist keys track GUI program execution, ROT13 encoded:

| ROT13 Entry (decoded) | Last Run | Run Count |
|---|---|---|
| `payload.exe` | 2025-05-28 08:52 | 3 |
| `install.exe` | 2025-05-28 09:05 | 1 |

### Recently Accessed Files (RecentDocs)

```
HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs
```

- `passwords.txt` — last accessed 2025-05-28 09:20
- `exfil_data.zip` — last accessed 2025-05-28 09:22

---

## 6. PowerShell History

**Path:** `C:\Users\jdoe\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`

```powershell
Set-ExecutionPolicy Bypass -Scope Process
.\payload.ps1
net user hacker_tmp P@ssw0rd! /add
net localgroup administrators hacker_tmp /add
vssadmin delete shadows /all /quiet
wevtutil cl Security
wevtutil cl System
```

> PowerShell history file survived the cleanup — provided a near-complete record of attacker commands.
