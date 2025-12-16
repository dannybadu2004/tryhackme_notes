# XSS - Merry XSSMas

## Overview

In this lab, we explored **Cross-Site Scripting (XSS)** vulnerabilities in a web application.  
XSS allows attackers to inject malicious JavaScript into a website, which is then executed
in the browser of other users.

We focused on understanding and exploiting:
- Reflected XSS
- Stored XSS

We also learned the impact, detection, and prevention techniques from a defensive (SOC) perspective.

---

## Equipment Check

The vulnerable web application was accessed via:
```
http://10.81.137.200
```

The application was tested using the **AttackBox browser**.

---

## What is XSS?

**Cross-Site Scripting (XSS)** is a web vulnerability that occurs when:
- User input is not properly validated or sanitized
- The application renders that input as executable code instead of text

This allows attackers to inject **JavaScript**, which executes in the victim’s browser.

### Common impacts of XSS:
- Stealing session cookies
- Impersonating users
- Defacing pages
- Triggering fake login prompts

---

## Reflected XSS

### What is Reflected XSS?

Reflected XSS occurs when:
- Malicious input is immediately reflected in the HTTP response
- The payload is **not stored** on the server
- Victims are usually tricked into clicking a malicious link (phishing)

### Example

Normal request:
```
https://trygiftme.thm/search?term=gift
```

Malicious request:
```
https://trygiftme.thm/search?term=
<script>alert(atob("VEhNe0V2aWxfQnVubnl9"))</script>
```

If a victim clicks the malicious link, the injected script executes in their browser.

---

## Exploiting Reflected XSS

The following payload was injected into the search bar:

```html
<script>alert('Reflected Meow Meow')</script>
```
### What happened

- The message was stored on the server
- The payload executed automatically on every page reload
- Any user visiting the page triggered the alert

**Result:** Stored XSS vulnerability confirmed.

---

## Flags Recovered

### Reflected XSS Flag
```
THM{Evil_Bunny}
```

### Stored XSS Flag
```
THM{Evil_Stored_Egg}
```

---

## Detection & SOC Perspective

Indicators of XSS activity include:
- Input containing `<script>` tags or JavaScript keywords
- Suspicious parameters in URLs and POST requests
- Repeated execution of client-side alerts
- Stored payloads executing on page load

Logs and browser telemetry can help identify exploitation attempts.

---

## Protecting Against XSS

Effective defensive measures include:
- Avoid dangerous rendering functions
  - Use `textContent` instead of `innerHTML`
- Sanitize and encode all user input and output
- Restrict cookies using:
  - `HttpOnly`
  - `Secure`
  - `SameSite`
- Allowlist HTML when formatting is required
  - Permit only safe tags and attributes

---

## Key Takeaways

- XSS exploits improper handling of user input
- Reflected XSS is temporary and often phishing-based
- Stored XSS is persistent and affects all users
- Proper sanitization and encoding are critical defenses
- XSS is detectable with good logging and monitoring
