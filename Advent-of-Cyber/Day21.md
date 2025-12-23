# Malware Analysis - Malhare.exe

## Overview
In this room, we investigated a phishing campaign targeting the elves of Wareville. The vector was a malicious **HTA (HTML Application)** file disguised as a "Developer Survey."

We performed static analysis on the file to understand its structure, identified how it abused the Windows `mshta.exe` process to execute code, and deobfuscated the payload to reveal the attacker's true intent.

---

## Key Concepts

### What is an HTA file?
**HTA (HTML Application)** is a legacy Windows file type that allows HTML and script code (VBScript or JScript) to run as a desktop application.
* **The Engine:** It runs via `mshta.exe`, not the browser.
* **The Danger:** Unlike a web browser which is sandboxed, an HTA has full access to the operating system (can read files, run programs, and modify the registry).


### Common Malicious Tactics
1.  **Living-off-the-Land (LotL):** Attackers use `mshta.exe` (a trusted Windows tool) to run malicious code, bypassing some antivirus checks.
2.  **Typosquatting:** Registering domains that look very similar to legitimate ones (e.g., `bestfestiivalcompany.com` vs `bestfestivalcompany.com`) to trick users.
3.  **Obfuscation:** Hiding the malicious code using encoding (Base64) or encryption (ROT13) to avoid detection by security scanners.


---

## Technical Investigation

### Step 1: Static Analysis (Metadata)
We opened `survey.hta` in a text editor to inspect the code without running it.
* **Title:** "Best Festival Company Developer Survey"
* **Social Engineering:** By analyzing the hidden HTML elements (specifically the `<div id="thanks">` block), we discovered the lure used by the attacker: a promise of a "chance to win a trip to the South Pole" displayed to the user only *after* they submit the data.

### Step 2: Analyzing VBScript Logic
We analyzed the `<script>` section to find the execution flow.
* **Function `getQuestions`:** This function simulated downloading survey questions but actually reached out to a malicious C2 server: `http://survey.bestfestiivalcompany.com`.
    * *Note the extra 'i' in the domain (Typosquatting).*
* **Reconnaissance:** The script collected the `ComputerName` and `UserName` and sent them to the attacker via a GET request to `/details`.

### Step 3: The Execution Trigger
We found the line of code responsible for executing the payload. It created a `WScript.Shell` object to run a PowerShell command:
```vbscript
runObject.Run "powershell.exe -nop -w hidden -c " & feedbackString, 0, False
```

This runs the downloaded content (`feedbackString`) silently in the background.

### Step 4: Deobfuscation (The Payload)
We retrieved the payload (which the malware would have downloaded) and analyzed it.
1.  **Layer 1 (Base64):** The script was encoded in Base64. We decoded it using CyberChef.
2.  **Layer 2 (ROT13):** The decoded text was still scrambled. We recognized the pattern as ROT13 (a simple Caesar cipher shifting letters by 13 places).
3.  **Result:** Applying ROT13 revealed the final flag.

---

## Answers Summary

| Question | Answer |
| :--- | :--- |
| What is the title of the HTA application? | `Best Festival Company Developer Survey` |
| What VBScript function is acting as if it is downloading the survey questions? | `getQuestions` |
| What URL domain is the "questions" being downloaded from? | `survey.bestfestiivalcompany.com` |
| What character in the domain gives away the typosquatting? | `i` |
| How many questions does the survey have? | `4` |
| The survey promises a chance to win a trip to where? | `South Pole` |
| What two pieces of information are being exfiltrated? | `ComputerName,UserName` |
| What endpoint is the enumerated data being exfiltrated to? | `/details` |
| What HTTP method is being used to exfiltrate the data? | `GET` |
| What line of code executes the contents of the download? | `runObject.Run "powershell.exe -nop -w hidden -c " & feedbackString, 0, False` |
| What popular encoding scheme was used to obfuscate the download? | `base64` |
| What encryption scheme was used in the script? | `rot13` |
| What is the flag value? | `THM{Malware.Analysed}` |

---

## Lessons Learned
* **Inspect, Don't Click:** An HTA file is essentially an executable program. Never double-click unknown `.hta` files.
* **Check the URL:** Typosquatting is a common trick. Always verify the domain spelling character by character (`bestfestiivalcompany` vs `bestfestivalcompany`).

* **Deobfuscation layers:** Attackers rarely use just one layer of hiding. If Base64 output looks like gibberish, look for XOR or ROT13.

---
> **Key Takeaway**: HTA files bridge the gap between web markup and desktop execution. This makes them versatile for developers but dangerous in the hands of attackers using them as "droppers" for malware.
