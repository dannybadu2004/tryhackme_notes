# IDOR - Santa’s Little IDOR

## Overview
In this room, we learned about **IDOR (Insecure Direct Object Reference)**, a common access control vulnerability.
We explored why IDOR happens, how attackers exploit it, different forms it can take, and how to properly fix it.

---

## What is IDOR?

**IDOR (Insecure Direct Object Reference)** is a vulnerability where a web application:
- Uses direct references (IDs) to access objects
- Fails to verify if the user is authorized to access those objects

Example:
```
https://awesome.website.thm/TrackPackage?packageID=1001
```

If changing `packageID=1001` to `1002` reveals another user's data, the application is vulnerable to IDOR.

---

## Why IDOR Happens

Web applications often retrieve data using simple queries like:
```sql
SELECT person, address, status FROM Packages WHERE packageID = value;
```

Problems occur when:
- IDs are sequential and predictable
- The server does not check ownership
- Authentication or authorization is missing

This allows attackers to access data belonging to other users.

---

## Authentication vs Authorization

### Authentication
- Verifies **who you are**
- Example: username + password
- Happens on **every request** using session cookies or tokens

### Authorization
- Verifies **what you are allowed to do**
- Example: can you view this account or data?

Authorization cannot exist without authentication.

---

## Privilege Escalation Types

### Vertical Privilege Escalation
- Gaining higher permissions
- Example: user → admin

### Horizontal Privilege Escalation
- Accessing other users’ data at the same permission level
- Example: viewing another user’s account

**Most IDOR vulnerabilities are horizontal privilege escalation**

---

## Practical IDOR Exploitation

### Login Credentials
- Username: `niels`
- Password: `TryHackMe#2025`
- URL: `http://10.81.184.94`

Browser **Developer Tools** and **Burp Suite** were used to inspect and manipulate requests.

---

## Simple IDOR Example

The application sends requests using:
```
user_id=10
```

By changing the `user_id` value in **Local Storage** from `10` to `11` and refreshing the page,
data belonging to another user becomes visible.

This confirms a basic IDOR vulnerability.

---

## IDOR in Disguise

### Base64 Encoded IDs
Example:
```
Mg==
```

This is Base64 for the number `2`.  
Encoding does **not** prevent IDOR — it only hides the reference.

---

### Hashed IDs (MD5 / SHA1)
Some endpoints use hashed identifiers.
If the hashing logic is known, attackers can reproduce hashes
and access unauthorized objects.

---

### Deterministic UUIDs
UUID version 1 is time-based.
If the generation time window is known, attackers can generate
valid UUIDs through brute force.

---

## Automated Exploitation with Burp’s Intruder

**Burp Suite Intruder** was used to speed up IDOR exploitation by:
- Automating ID iteration
- Brute-forcing encoded, hashed, or UUID-based references
- Quickly identifying valid object IDs
- Reducing manual testing time

This is especially effective when:
- IDs are sequential
- Base64 or hashes are used
- Large keyspaces must be tested

---

## How to Fix IDOR Properly

✔️ Always verify permissions on the **server**
✔️ Check ownership on every request
✔️ Do not rely on Base64, hashing, or obfuscation
✔️ Use random IDs, but still enforce authorization
✔️ Log and monitor failed access attempts
✔️ Test by attempting to access other users’ data

---

## Answers Summary

| Question | Answer |
|--------|--------|
| What does IDOR stand for? | Insecure Direct Object Reference |
| IDOR privilege escalation type | Horizontal |
| Parent user_id with 10 children | 15 |

---

## Lessons Learned
- IDOR is an authorization failure, not an ID problem
- Hiding or encoding IDs does not secure applications
- Horizontal privilege escalation is extremely common
- Burp’s Intruder greatly speeds up IDOR discovery
- Server-side authorization checks are mandatory
