# Phishing - Phishmas Greetings

## Overview
In this room, we analyzed the human element of security by investigating various email threats targeting The Best Festival Company. The goal was to triage incoming messages, identify **Indicators of Compromise (IoCs)**, and distinguish between harmless spam and malicious phishing attempts.

---

## Core Concepts

### Phishing vs. Spam
- **Phishing**: A precision strike designed to deceive specific users. Common goals include credential theft, malware delivery, and financial fraud.
- **Spam**: Digital noise focusing on quantity over precision. Usually consists of unsolicited marketing, low-quality promotions, or clickbait.

### Key Attack Techniques Identified
- **Impersonation**: Attackers pretend to be trusted individuals or services to gain credibility.
- **Social Engineering**: Manipulating emotions such as urgency, fear, or curiosity to pressure the victim into acting without thinking.
- **Typosquatting & Punycode**: Registering domains that look identical to legitimate ones by using misspellings or non-ASCII characters (e.g., using `ƒ` instead of `f`).
- **Email Spoofing**: Making an email appear to come from a trusted domain while the underlying headers (like `Return-Path`) reveal a different sender.
- **Malicious Attachments**: Using HTML/HTA files that can execute scripts outside of browser sandboxing, granting full access to the endpoint.

---

## Technical Investigation
We analyzed email headers to verify authenticity using three main security checks:
1. **SPF (Sender Policy Framework)**: Checks if the sending server is authorized by the domain.
2. **DKIM (DomainKeys Identified Mail)**: Digital signature to prove the message wasn't altered.
3. **DMARC**: Policy that uses SPF and DKIM to decide if a message is fake.

---

## Answers Summary

| Question | Classification | Flag |
| :--- | :--- | :--- |
| Classify the 1st email | Phishing | `THM{yougotnumber1-keep-it-going}` |
| Classify the 2nd email | Phishing | `THM{nnumber2-was-not-tha-thard!}` |
| Classify the 3rd email | Phishing | `THM{Impersonation-is-areal-thing-keepIt}` |
| Classify the 4th email | Phishing | `THM{Get-back-SOC-mas!!}` |
| Classify the 5th email | Spam | `THM{It-was-just-a-sp4m!!}` |
| Classify the 6th email | Phishing | `THM{number6-is-the-last-one!-DX!}` |

---

## Lessons Learned
- **The Intent Matters**: Not every unwanted email is a threat; determining if the intention is marketing or deception is key to effective triage.
- **Headers Never Lie**: While the "From" display name can be easily spoofed, the `Return-Path` and `Authentication-Results` provide the real story.
- **Credentials are the Main Goal**: Modern phishing often bypasses malware filters by using legitimate services (like OneDrive or Dropbox) to host fake login pages.
- **Vigilance over Convenience**: Attackers discourage using standard communication channels (Side Channel Communication) to avoid being caught by company controls.

---

> **Key Takeaway**: Technology alone cannot fix human vulnerabilities. Identifying social engineering cues and verifying domains via Punycode analysis are essential skills for a modern SOC Analyst.
