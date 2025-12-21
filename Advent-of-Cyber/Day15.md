# Web Attack Forensics - Drone Alone

## Overview
In this room, we stepped into the shoes of a Blue Teamer (SOC Analyst) to investigate a compromised Windows machine running an Apache web server. The goal was to use **Splunk**, a powerful SIEM (Security Information and Event Management) tool, to trace the attacker's path.

We investigated a **Command Injection** attack where the attacker exploited a vulnerable CGI script (`hello.bat`) to execute system commands, perform reconnaissance, and attempt to run PowerShell payloads.

---

## Key Concepts

### What is Splunk?
Splunk is a platform used to search, monitor, and analyze machine-generated data (logs). It acts as the "brain" of the SOC, collecting logs from Web Servers, Operating Systems, and Firewalls to allow analysts to correlate events and detect attacks.

### Sysmon (System Monitor)
While standard Windows logs show *what* happened, Sysmon shows *how* it happened. It provides detailed information about process creations, network connections, and file changes.
* **Critical Indicator:** Analyzing **Parent-Child Process relationships**. For example, a web server process (`httpd.exe`) should rarely, if ever, spawn a command line interface (`cmd.exe`).

### Command Injection
An attack where a hacker inputs malicious system commands into a vulnerable web application form or parameter. If the application doesn't sanitize the input, the server executes the command (e.g., passing `; whoami` to a script).

---

## Technical Investigation (Splunk)

### Step 1: Detecting Suspicious HTTP Requests
We started by searching the web access logs for common command execution keywords.
**Query:**
```splunk
index=windows_apache_access (cmd.exe OR powershell OR "powershell.exe" OR "Invoke-Expression")
| table _time host clientip uri_path uri_query status
```
**Finding:** We found a Base64 encoded PowerShell string. Decoding it revealed the message: *"This is now Mine! MUAHAAAA"*.

### Step 2: Analyzing Server Errors
We checked the error logs to see if the server struggled to process the malicious input.

**Query:**
```splunk
index=windows_apache_error ("cmd.exe" OR "powershell" OR "Internal Server Error")
```
**Finding:** Confirmed that requests targeting `/cgi-bin/hello.bat` were causing errors, a sign of exploitation attempts.

### Step 3: Tracing Process Creation (The "Smoking Gun")
This was the most critical step. We looked for instances where the Apache web server (`httpd.exe`) spawned a system shell.

**Query:**
```splunk
index=windows_sysmon ParentImage="*httpd.exe"
```
**Finding:** The logs showed `httpd.exe` as the Parent Process for `cmd.exe`. This confirms the Command Injection was successful and the web attack penetrated the operating system.

### Step 4: Confirming Enumeration
We looked for what commands the attacker ran once they had a shell.

**Query:**
```splunk
index=windows_sysmon *cmd.exe* *whoami*
```
**Finding:** The attacker executed `whoami.exe` to see which user account they had compromised.

---

### Answers Summary

| Question | Answer |
| :--- | :--- |
| What is the reconnaissance executable file name? | `whoami.exe` |
| What executable did the attacker attempt to run through the command injection? | `powershell.exe` |

---

### Lessons Learned
* **Know your Baseline:** A web server process (`httpd.exe`, `w3wp.exe`, `nginx`) spawning a shell (`cmd.exe`, `powershell.exe`) is almost always malicious.
* **Log Everything:** Without `windows_sysmon` logs, we wouldn't have seen the process execution, only the web requests.
* **Decode Payloads:** Attackers use Base64 to hide their intent. Always decode suspicious strings found in logs.
* **Input Validation:** This entire attack could have been prevented if the web application sanitized user input before passing it to the system.
