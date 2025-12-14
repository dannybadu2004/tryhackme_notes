# Malware Analysis - Egg-xecutable

## Overview
In this room, we learned the **fundamentals of malware analysis** by analysing a malicious sample called
**HopHelper.exe**. We explored both **static** and **dynamic** analysis techniques using industry-standard tools
to understand malware behaviour, persistence, and network communication.

---

## What is Malware Analysis?

Malware analysis is the process of examining a malicious file to:
- Understand how it works
- Identify attacker techniques
- Extract indicators of compromise (IOCs)
- Create defensive measures

The goal is not fear, but **proactive defence**.

---

## Types of Malware Analysis

### Static Analysis
- Examining a file **without executing it**
- Fast and low-risk
- Useful for initial triage and intelligence gathering

### Dynamic Analysis
- Executing the malware in a **safe sandbox**
- Observing real behaviour on the system
- Higher risk, but more accurate

---

## Sandboxing

- Malware must **never** be executed on systems you care about
- Sandboxes are isolated environments (usually virtual machines)
- Allow safe execution and observation
- Snapshots enable easy rollback

---

## Static Analysis – HopHelper.exe

### Tool Used: PeStudio

Static analysis was performed using **PeStudio** to extract metadata and indicators.

#### Information Collected:
- File checksum (SHA256)
- Strings embedded in the executable
- Imports and resources

### SHA256 Checksum
```text
F29C270068F865EF4A747E2683BFA07667BF64E768B38FBB9A2750A3D879CA33
```

This checksum can be used as threat intelligence.

---

### Strings Analysis

Strings were analysed to identify readable data such as:
- URLs
- Commands
- Flags

Flag found in strings:
```text
THM{STRINGS_FOUND}
```

---

## Dynamic Analysis – HopHelper.exe

Dynamic analysis involved executing the malware inside the sandbox
and observing system changes.

---

## Registry Persistence (Regshot)

### Tool Used: Regshot

Regshot was used to:
1. Capture registry state **before execution**
2. Execute HopHelper.exe
3. Capture registry state **after execution**
4. Compare differences

### Persistence Mechanism Found
```text
HKU\S-1-5-21-1966530601-3185510712-10604624-1008\Software\Microsoft\Windows\CurrentVersion\Run\HopHelper
```

This confirms the malware establishes **persistence** via a Run key.

---

## Process Behaviour (ProcMon)

### Tool Used: Process Monitor (ProcMon)

ProcMon was used to observe:
- File creation
- Registry access
- Network connections

Filters were applied to:
- Focus only on **HopHelper.exe**
- Reduce background noise
- Inspect specific operations

---

### Network Activity

ProcMon was filtered for **TCP operations**.

Network protocol used:
```text
HTTP
```

This indicates the malware communicates with a remote web service.

---

## Answers Summary

| Question | Answer |
|--------|--------|
| SHA256 of HopHelper.exe | F29C270068F865EF4A747E2683BFA07667BF64E768B38FBB9A2750A3D879CA33 |
| Flag found in strings | THM{STRINGS_FOUND} |
| Registry persistence key | HKU\S-1-5-21-1966530601-3185510712-10604624-1008\Software\Microsoft\Windows\CurrentVersion\Run\HopHelper |
| Network protocol used | HTTP |

---

## Lessons Learned
- Never run malware on real systems
- Static analysis provides fast intelligence
- Dynamic analysis reveals real behaviour
- Registry Run keys are common persistence methods
- ProcMon is essential for behaviour analysis
- Combining tools gives full visibility
