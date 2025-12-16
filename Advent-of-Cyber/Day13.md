# YARA Rules - YARA mean one!

## YARA Overview

In this stage, McSkidy, the lead defender, assigned a mission involving **YARA rules**.  
Before taking action, we explored what YARA is, how it works, and why it is a powerful tool
in TBFC’s effort to protect SOC-mas.

**YARA** is a malware identification and classification tool that searches for unique patterns,
also known as *digital fingerprints*, left behind by attackers.

Think of YARA as a detective’s notebook for cyber defenders:
- Instead of fingerprints, it scans files, code, and memory
- It looks for strings, byte patterns, and logic
- It helps identify malware families and suspicious behavior

Within TBFC, YARA acts as a silent guardian, uncovering threats that traditional tools may miss.

---

## Why YARA Matters and When to Use It

Not all threats are obvious. Some malware hides inside legitimate-looking files, scripts, or memory.
YARA allows defenders to detect malware based on **patterns and behavior**, not just file names.

### Common Use Cases for YARA

- **Post-incident analysis**  
  Check if malware traces exist elsewhere in the environment

- **Threat hunting**  
  Search endpoints for known or related malware families

- **Intelligence-based scans**  
  Apply shared YARA rules from other defenders

- **Memory analysis**  
  Scan memory dumps for malicious code fragments

YARA helps defenders move from reactive defense to **active threat hunting**.

---

## YARA Values

YARA gives defenders clarity in chaos by offering:

- **Speed** – scans large datasets quickly
- **Flexibility** – detects text, binary patterns, and complex logic
- **Control** – analysts define what “malicious” means
- **Shareability** – rules can be reused and improved
- **Visibility** – connects scattered clues into one picture

YARA empowers defenders to act before attackers can escalate.

---

## YARA Rules Structure

A YARA rule is built from three main components:

### 1. Metadata
Information about the rule:
- Author
- Description
- Creation date

### 2. Strings
Indicators YARA searches for:
- Text strings
- Hexadecimal bytes
- Regular expressions

### 3. Conditions
Logical expressions deciding when a rule triggers

---

## Example YARA Rule

```yara
rule TBFC_KingMalhare_Trace
{
    meta:
        author = "Defender of SOC-mas"
        description = "Detects traces of King Malhare’s malware"
        date = "2025-10-10"

    strings:
        $s1 = "rundll32.exe" fullword ascii
        $s2 = "msvcrt.dll" fullword wide
        $url1 = /http:\/\/.*malhare.*/ nocase

    condition:
        any of them
}
```
## Strings in YARA

Strings represent malware signatures. They are the indicators YARA searches for when scanning
files, memory, or other data sources. YARA supports multiple string types, each useful in
different detection scenarios.

---

## Text Strings

Text strings are simple ASCII or Unicode text fragments commonly found in malware.

```yara
strings:
    $TBFC_string = "Christmas"
condition:
    $TBFC_string
```
## Useful Modifiers

Modifiers extend string-matching capabilities and help bypass obfuscation techniques.

- `nocase` – ignores letter casing
- `wide / ascii` – supports Unicode and ASCII formats
- `xor` – detects XOR-obfuscated strings
- `base64 / base64wide` – decodes Base64-encoded content

```yara
$xmas   = "Christmas" wide ascii
$hidden = "Malhare" xor
$b64    = "SOC-mas" base64
```
## Hexadecimal Strings

Hex strings are used when malware hides in raw bytes instead of readable text. They are useful
for detecting file headers, shellcode, or binary fragments.

```yara
rule TBFC_Malhare_HexDetect
{
    strings:
        $mz = { 4D 5A 90 00 }   // MZ header of a Windows executable
        $hex_string = { E3 41 ?? C8 }

    condition:
        $mz and $hex_string
}
```
## Regular Expression Strings

Regular expressions allow flexible matching when malware patterns vary slightly, such as
URLs, commands, or encoded payloads.

```yara
rule TBFC_Malhare_RegexDetect
{
    strings:
        $url = /http:\/\/.*malhare.*/ nocase
        $cmd = /powershell.*-enc\s+[A-Za-z0-9+/=]+/ nocase

    condition:
        $url and $cmd
}
```
Regex rules should be written carefully, as overly broad expressions can slow down scans.
## Conditions

Conditions determine when a YARA rule triggers. They combine string matches and file properties
using logical expressions.

### Match a Single String
```
condition:
    $xmas
```
### Match Any String
```
condition:
    any of them
```
### Match All Strings
```
condition:
    all of them
```
### Logical Operators
```
condition:
    ($s1 or $s2) and not $benign
This triggers when either $s1 or $s2 is found, but not $benign.
```
### File Property Checks
```
condition:
    any of them and filesize < 700KB
This reduces false positives by limiting matches based on file size.
```
---

## YARA Study Use Case – IcedID Detection

TBFC analysts identified small malware loaders used by the IcedID trojan. These samples
shared a common PE header and binary patterns.
```
rule TBFC_Simple_MZ_Detect
{
    meta:
        author = "TBFC SOC L2"
        description = "IcedID Rule"
        date = "2025-10-10"
        confidence = "low"

    strings:
        $mz   = { 4D 5A }              // PE header
        $hex1 = { 48 8B ?? ?? 48 89 }
        $s1   = "malhare" nocase

    condition:
        all of them and filesize < 10485760
}
```

---

## Running the Rule
yara -r icedid_starter.yar C:\

### Detected File
C:\Users\WarevilleElf\AppData\Roaming\TBFC_Presents\malhare_gift_loader.exe

---

## Useful YARA Flags

- -r – scans directories recursively
- -s – displays matched strings inside files

---

## YARA Practical Task

The blue team searched the /home/ubuntu/Downloads/easter directory for strings starting with
TBFC: followed by ASCII alphanumeric characters.

### Regex Used
/TBFC:[A-Za-z0-9]+/

---

## Results

- Images containing TBFC: 5
- Decoded message: Find me in HopSec Island

---

## Key Takeaways

- YARA detects malware by behavior and patterns, not filenames
- Custom rules give defenders full control
- Strings and conditions together form powerful detection logic
- Ideal for threat hunting and incident response
- YARA turns threat intelligence into actionable defense


