# Obfuscation - The Egg Shell File

## Overview
In this room, we explored the concept of **Obfuscation** (hiding the meaning of code/data) and **Deobfuscation** (reversing that process). Attackers use obfuscation to evade antivirus detection and hide their tracks.

Our goal was to analyze a PowerShell script (`SantaStealer.ps1`), identify the obfuscation techniques used (Base64, XOR), and use **CyberChef** to reveal the hidden C2 (Command & Control) URL and obfuscate an API key.

---

## Key Concepts

### What is Obfuscation?
The practice of making data or code difficult for humans and security tools to read, without changing its functionality.
* **Simple Ciphers (ROT13)**: Shifting letters in the alphabet.
* **Base64**: Encoding binary data into ASCII characters (ends often with `=`).
* **XOR**: A bitwise operation that changes data using a key. It's reversible if you know the key.

### Identifying Patterns
| Technique | Visual Clues |
| :--- | :--- |
| **ROT13** | Text looks like gibberish but keeps spaces and length. `the` -> `gur`. |
| **Base64** | Long strings of alphanumeric characters (`A-Z`, `a-z`, `0-9`), often ending in `=` or `==`. |
| **XOR** | Random symbols, non-readable characters, same length as original. |

### CyberChef
The "Cyber Swiss Army Knife". A web-based tool for encryption, encoding, compression, and data analysis.
* **Recipe**: A chain of operations (e.g., `From Base64` -> `XOR` -> `Gunzip`).
* **Magic**: An operation that attempts to guess the encoding used.

---

## Technical Investigation

### Challenge 1: Deobfuscating the C2 URL
We opened the `SantaStealer.ps1` script and found a Base64 encoded string representing the C2 URL.
1.  **Input:** The Base64 string from the script.
2.  **Recipe:** `From Base64`.
3.  **Result:** We found the readable URL.
4.  **Action:** We updated the `$URL` variable in the script with the decoded value and ran it to get the first flag.

### Challenge 2: Obfuscating the API Key (XOR)
The script required us to obfuscate an API key (`CANDY-CANE-API-KEY`) using XOR with the key `0x37` (Hex 37) to mimic the attacker's behavior.
1.  **Input:** `CANDY-CANE-API-KEY`
2.  **Recipe:**
    * `XOR` (Key: `37`, Type: `Hex`)
    * `To Hex` (Delimiter: `None`)
3.  **Result:** A long hex string (`747679...`).
4.  **Action:** We pasted this hex string into the `$ObfAPIKEY` variable in the script and ran it again to get the second flag.

---

## Answers Summary

| Question | Answer |
| :--- | :--- |
| What is the first flag you get after deobfuscating the C2 URL? | `THM{C2_De0bfuscation_29838}` |
| What is the second flag you get after obfuscating the API key? | `THM{API_Obfusc4tion_ftw_0283}` |

---

## Lessons Learned
* **Layered Obfuscation**: Attackers often combine techniques (e.g., Base64 + XOR + Gzip) to make analysis harder. You must reverse them in the correct order (LIFO - Last In, First Out).
* **Script Analysis**: Never run a suspicious script blindly. Always open it in a text editor first to understand what it does.
* **CyberChef Skills**: Knowing how to chain operations (Input -> Operation -> Output) is essential for any SOC analyst.

---

> **Key Takeaway**: Malware is rarely plain text. If you see random strings of characters, it's likely obfuscated code waiting to be unraveled.
