# Passwords - A Cracking Christmas

## Overview
In this lab, we learned how attackers recover weak passwords from encrypted files without breaking the encryption itself.
Instead of attacking cryptographic algorithms directly, attackers target **human weaknesses** by guessing passwords
using automated tools and common password patterns.

We practiced cracking **password-protected PDF and ZIP files** using dictionary-based attacks and learned how this
activity can be detected from a SOC perspective.

---

## How Attackers Recover Weak Passwords

Modern encryption algorithms are strong, so attackers rarely try to break the encryption directly.
Instead, they focus on guessing the **password protecting the file**.

The two main approaches are:
- Dictionary attacks
- Brute-force / mask attacks

---

## Dictionary Attacks

A dictionary attack uses a predefined list of common passwords (wordlists) and tests them automatically.
These wordlists usually contain:
- Passwords leaked from previous breaches
- Common patterns like `password123`, `admin123`, `qwerty`
- Names, dates, and simple substitutions

Because many users reuse weak passwords, dictionary attacks are:
- Fast
- Highly effective
- Low cost for attackers

Common wordlists used:
- `rockyou.txt`
- SecLists collections

---

## Brute-Force & Mask Attacks

Brute-force attacks try **every possible character combination** until the correct password is found.
While guaranteed to succeed eventually, they become slow as password length increases.

Mask attacks optimize brute-force by limiting guesses to a specific pattern.
Example mask:
- `?l?l?l?d?d` → three lowercase letters followed by two digits

Mask attacks are effective when the attacker has an idea of the password structure.

---

## Practical Exercise

The goal of this exercise was to crack passwords protecting encrypted files found on the system.

### Step 1: Identify the File Type

Before selecting the cracking tool, the file type must be confirmed.

```bash
file flag.pdf
file flag.zip
```
This step determines which cracking tools should be used for each file.

---

## Tools Used

Depending on the file type, different tools were used for password cracking:

- **PDF files**
  - `pdfcrack` – performs dictionary attacks directly against encrypted PDF files
  - `john` with `pdf2john.pl` – converts a PDF into a hash format crackable by John the Ripper

- **ZIP files**
  - `john` with `zip2john` – extracts the ZIP password hash and cracks it using a wordlist

- **Wordlist**
  - `/usr/share/wordlists/rockyou.txt` – commonly leaked passwords used for fast dictionary attacks

---

## Cracking the PDF File

A dictionary attack was executed against the encrypted PDF file using `pdfcrack`.

```bash
pdfcrack -f flag.pdf -w /usr/share/wordlists/rockyou.txt
# dupa ce parola este gasita, deschizi PDF-ul si citesti flag-ul
# THM{Cr4ck1ng_PDFs_1s_34$y}

# extragem hash-ul din arhiva ZIP
zip2john flag.zip > ziphash.txt

# rulam John the Ripper cu wordlist
john --wordlist=/usr/share/wordlists/rockyou.txt ziphash.txt

# afisam parola gasita de John
john --show ziphash.txt

# deschidem arhiva ZIP cu parola gasita si citim flag-ul
# THM{Cr4ck1n6_z1p$_1s_34$yyyy}
```

## SOC Perspective: Detection & Monitoring

From a SOC perspective, password-cracking activity against encrypted files can be identified by abnormal endpoint behavior rather than network traffic.

Common indicators include repeated access to protected files, high CPU usage over short periods of time, and the execution of known password-cracking tools such as `pdfcrack` or `John the Ripper`. The use of large wordlists like `rockyou.txt` is also a strong indicator of unauthorized activity.

Endpoint Detection and Response (EDR) solutions can detect this behavior by monitoring suspicious process execution, command-line arguments, and unusual resource consumption. These signals are critical for detecting offline attacks that do not generate network alerts.

---

## Mitigation & Best Practices

To reduce the risk of password-cracking attacks against encrypted files, organizations should enforce strong password policies and proper access controls.

Recommended best practices include:
- Using long, complex, and unique passwords
- Avoiding common words and predictable patterns
- Enforcing least-privilege file access
- Using password managers
- Monitoring endpoints for cracking tools and abnormal behavior

Strong encryption must always be combined with strong passwords to be effective.

---

## Key Takeaways

This lab highlights that encryption alone does not guarantee data security if weak passwords are used.

Attackers typically exploit human weaknesses through dictionary and brute-force attacks rather than attempting to break cryptographic algorithms. From a defensive perspective, endpoint monitoring and strong password hygiene are essential to preventing and detecting these attacks.

