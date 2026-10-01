# Windows SOC Investigation

## Overview

This project documents a hands-on Windows Security Operations Center (SOC) investigation conducted in a controlled Windows lab environment.

The investigation focused on two related areas of endpoint security monitoring:

1. Windows authentication activity
2. Suspicious-looking PowerShell execution and endpoint telemetry

The objective was to collect security evidence, analyze Windows events, correlate activity across telemetry sources, and document findings in a format similar to a real SOC investigation.

---

## Investigation Objectives

- Investigate failed Windows authentication events
- Analyze authentication-related Event IDs
- Identify patterns in local authentication activity
- Investigate PowerShell process execution
- Analyze Sysmon endpoint telemetry
- Examine encoded PowerShell command execution
- Correlate timestamps, users, processes, and events
- Document evidence and investigative findings

---

## Environment

**Operating System:** Windows  
**Environment:** Controlled security lab / virtual machine  
**Primary tools:**
- Windows Event Viewer
- Windows Security Event Logs
- PowerShell
- Sysmon
- PowerShell Operational Logs

---

# Investigation 1 — Windows Authentication Activity

## Event ID 4625

Windows Security Event ID **4625** was analyzed to investigate failed logon activity.

The investigation identified:

- **50 failed authentication events**
- Target account: `USER`
- **Logon Type:** 2 (Interactive)
- **Status:** `0xC000006E`
- Activity occurred across multiple days
- No remote source IP address was observed in the investigated events

Process-level analysis showed that the authentication failures were associated primarily with:

- `msedge.exe` — 45 events
- Microsoft Edge WebView2 — 5 events

The observed activity was therefore predominantly associated with local interactive authentication attempts rather than a clearly identified remote source.

---

## Event ID 4648 Correlation

An Event ID **4648** was also identified involving explicit credential use.

Observed activity included:

- `svchost.exe`
- Target: `localhost`
- Time: **11:10:18**

Four Event ID 4625 failures followed shortly afterward:

- **11:10:35**
- **11:10:39**
- **11:10:44**
- **11:10:47**

The timing was investigated as a possible correlation.

However, the events involved different processes, so the relationship was treated as a **temporal correlation rather than confirmed process-level causation**.

---

## Authentication Investigation Finding

The investigation established recurring local authentication activity.

However, the available evidence did **not** by itself establish:

- A remote brute-force attack
- Malicious process activity
- Malicious activity by Microsoft Edge
- An external attacker
- System compromise

The findings were therefore documented based on the available telemetry rather than assuming malicious intent.

---

# Investigation 2 — PowerShell Endpoint Activity

## Sysmon Event ID 1 — Process Creation

Sysmon Event ID **1 (Process Creation)** was used to investigate PowerShell execution.

Observed process information included:

| Field | Observed Value |
|---|---|
| Date/Time | September 28, 2026 — 02:49:29 AM |
| User | `DESKTOP-OPSIGGP\USER` |
| Process | `powershell.exe` |
| Integrity Level | High |
| Command Line | `-NoProfile -EncodedCommand ...` |
| Parent Process | `powershell.exe` |
| Parent PID | 14496 |
| Process ID | 15656 |
| SHA-256 | `8BB6FA8C283B4D92120B1EF249A9B311B0F804D4CABBE9981159976C8BE76A5E` |

The use of `-EncodedCommand` was treated as an investigation indicator because encoded PowerShell commands can conceal the readable contents of a command line.

---

## PowerShell Command Analysis

The encoded PowerShell command was decoded during the investigation.

The decoded command performed a harmless test action:

```powershell
Write-Output "SOC Project 2 - Suspicious PowerShell Test"
```

A subsequent `Get-Date` command recorded:

September 28, 2026 — 02:50:08 AM

This confirmed that the activity was part of a controlled security testing exercise rather than an attempt to execute a malicious payload.

## PowerShell Operational Logging

PowerShell Operational logging was also examined.

Events observed around 02:49:29 AM included:

Event ID 40962
Event ID 53504
Event ID 40961

ScriptBlock logging was enabled, with the following ScriptBlock ID observed:

2d726514-cb3f-427e-8504-3bb4daff5689

The investigation demonstrated the importance of examining multiple telemetry sources when investigating PowerShell activity.

Timeline
Time	Event	Significance
11:10:18	Event ID 4648	Explicit credential use involving svchost.exe and localhost
11:10:35	Event ID 4625	Failed authentication
11:10:39	Event ID 4625	Failed authentication
11:10:44	Event ID 4625	Failed authentication
11:10:47	Event ID 4625	Failed authentication
02:49:29	Sysmon Event ID 1	PowerShell process creation
02:49:29	PowerShell events	PowerShell Operational telemetry
02:50:08	Get-Date	Controlled test execution timestamp
Key Findings
Finding 1 — Repeated Authentication Failures

Multiple Event ID 4625 records showed recurring failed authentication attempts against the USER account.

Finding 2 — Local Authentication Context

The investigated authentication events used Logon Type 2 and did not provide evidence of a remote source IP in the observed records.

Finding 3 — PowerShell Encoded Command

Sysmon identified PowerShell execution using:

-NoProfile -EncodedCommand

Encoded command execution warranted investigation because the readable command was not immediately visible in the process command line.

Finding 4 — Controlled PowerShell Test

Decoding the command showed that the PowerShell activity executed a harmless test command:

Write-Output "SOC Project 2 - Suspicious PowerShell Test"
Finding 5 — Multi-Source Investigation

The investigation demonstrated the value of correlating:

Windows Security Logs
Sysmon
PowerShell Operational Logs
Process information
Timestamps
User accounts
Command-line data
Investigation Methodology

The investigation followed a basic SOC workflow:

Collect relevant Windows security telemetry.
Identify unusual or repeated events.
Examine event details and process information.
Correlate related events by timestamp, user, process, and activity.
Investigate PowerShell command-line activity.
Decode the encoded command.
Validate the observed behavior against the controlled test.
Document findings and limitations.
Lessons Learned

This investigation provided hands-on experience with:

Windows authentication event analysis
Event ID 4625 investigation
Event ID 4648 investigation
Sysmon Event ID 1 analysis
PowerShell process investigation
Encoded PowerShell command analysis
Event correlation
Security evidence documentation
Distinguishing suspicious indicators from confirmed malicious activity

A key lesson was that a suspicious indicator should not automatically be treated as proof of compromise. Investigation requires additional evidence and correlation before drawing conclusions.

Tools Used
Windows Event Viewer
Windows Security Event Logs
PowerShell
Sysmon
PowerShell Operational Logs
Project Status

Completed: Initial Windows authentication and PowerShell endpoint investigation.

This project forms part of my cybersecurity portfolio as I develop practical skills in SOC operations, defensive security, and ethical hacking.

Author

Gaby Wanjiku

Aspiring SOC Analyst | Ethical Hacking | Cybersecurity

GitHub: https://github.com/Gaby-cybersecurity
LinkedIn: https://www.linkedin.com/in/gaby-wanjiiku-/
