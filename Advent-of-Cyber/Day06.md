# Malware Analysis - Egg-xecutable

## Overview
In this room, we learned the **fundamentals of malware analysis** by analysing a malicious sample called
**HopHelper.exe**. We performed both **static** and **dynamic** analysis using real-world malware analysis tools
to understand persistence, system interaction, and network communication.

---

## What is Malware Analysis?

Malware analysis is the process of examining malicious files to:
- Understand how they operate
- Identify attacker techniques
- Extract indicators of compromise (IOCs)
- Improve defensive detection and response

---

## Types of Malware Analysis

### Static Analysis
- Examining a file **without executing it**
- Safe and fast
- Used for initial intelligence gathering

### Dynamic Analysis
- Executing malware in a **sandboxed environment**
- Observing real system behaviour
- Reveals persistence and network activity

---

## Sandboxing

- Malware must **never** be run on production systems
- Sandboxes are isolated virtual machines
- Snapshots allow safe rollback after execution
- All analysis was performed inside a controlled VM

---

## Tools Used in This Room

During this room, the following industry-standard tools were used:

- **PeStudio** – static analysis of Windows executables
- **Regshot** – registry comparison before and after execution
- **Process Monitor (ProcMon)** – monitoring file, registry, and network activity

---

## Static Analysis – HopHelper.exe

### Tool Used: PeStudio

PeStudio was used to analyse HopHelper.exe without execution and extract:
- File hashes
- Embedded strings
- Indicators of suspicious behaviour

### SHA256 Checksum
```text
F29C270068F865EF4A747E2683BFA07667BF64E768B38FBB9A2750A3D879CA33
```

This hash can be used as an IOC for detection and threat intelligence.

---

### Strings Analysis

Using PeStudio’s **Strings** view, readable data inside the executable was analysed.

Flag found:
```text
THM{STRINGS_FOUND}
```

Strings can reveal:
- URLs
- Commands
- Hardcoded values
- Debug artifacts

---

## Dynamic Analysis – HopHelper.exe

Dynamic analysis was performed by executing the malware inside the sandbox
and monitoring system changes.

---

## Registry Persistence Analysis

### Tool Used: Regshot

Regshot was used to:
1. Capture the registry **before execution**
2. Execute HopHelper.exe
3. Capture the registry **after execution**
4. Compare changes

### Persistence Mechanism Identified
```text
HKU\S-1-5-21-1966530601-3185510712-10604624-1008\Software\Microsoft\Windows\CurrentVersion\Run\HopHelper
```

This confirms the malware establishes **persistence via a Run key**.

---

## Process & Network Behaviour

### Tool Used: Process Monitor (ProcMon)

ProcMon was used to:
- Monitor file operations
- Track registry access
- Observe network connections

Filters were applied to:
- Isolate **HopHelper.exe**
- Reduce system noise
- Focus on relevant operations

---

### Network Communication

ProcMon was filtered for **TCP operations**.

Network protocol observed:
```text
HTTP
```

This indicates communication with a remote web service.

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
- Malware analysis must be done in isolated environments
- PeStudio is effective for quick static analysis
- Regshot is useful for detecting persistence mechanisms
- ProcMon provides deep visibility into malware behaviour
- Combining static and dynamic analysis gives full context
