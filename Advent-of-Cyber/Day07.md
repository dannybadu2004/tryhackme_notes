# Network Discovery - Scan-ta Clause

## Overview
In this room, we performed **service discovery** on a compromised QA server to regain access.
The goal was to identify exposed services, interact with them, collect multiple keys, and finally
extract a flag from an internal database.

Target system:
- **Hostname:** tbfc-devqa01
- **IP address:** 10.81.137.10

---

## Objectives
- Discover open TCP and UDP ports
- Identify services running on those ports
- Interact with exposed services
- Collect three key parts
- Gain internal access and extract the final flag

---

## Initial Port Scan (TCP – Top 1000 Ports)

### Command:
```bash
nmap 10.81.137.10
```

### Result:
- Port 22 – SSH
- Port 80 – HTTP

This scan checks the most common TCP ports and gives a quick overview of exposed services.
Visiting the web server revealed a defaced page with the message:

**Pwned by HopSec**

---

## Full TCP Port Scan + Banner Grabbing

The initial scan only covered common ports. To ensure no hidden services were missed,
a full port scan was performed.

### Command:
```bash
nmap -p- --script=banner 10.81.137.10
```

### What this does:
- `-p-` → scans all 65,535 TCP ports
- `--script=banner` → retrieves service banners to identify software and versions

### New services discovered:
- Port **21212** – FTP (vsFTPd 3.0.5)
- Port **25251** – Custom TBFC service

Banner grabbing revealed exact services instead of just guessed names.

---

## FTP Enumeration (Key Part 1)

The FTP service was accessible using anonymous login.

### Command:
```bash
ftp 10.81.137.10 21212
```

### Login:
```text
Username: anonymous
```

### Files found:
```text
tbfc_qa_key1
```

### Retrieved key:
```
3aster_
```

Anonymous FTP access is a serious security misconfiguration.

---

## Custom Service Interaction with Netcat (Key Part 2)

The service on port 25251 was not a standard protocol.
Netcat was used to interact directly with it.

### Command:
```bash
nc -v 10.81.137.10 25251
```

### Available commands:
```
HELP
STATUS
GET KEY
QUIT
```

### Retrieved key:
```
15_th3_
```

The service lacked authentication and exposed sensitive data.

---

## UDP Port Discovery (DNS – Key Part 3)

Until now, only TCP ports were scanned. A UDP scan was performed to discover additional services.

### Command:
```bash
nmap -sU 10.81.137.10
```

### Result:
- Port **53/udp** – DNS

DNS TXT records were queried to check for hidden information.

### Command:
```bash
dig @10.81.137.10 TXT key3.tbfc.local +short
```

### Retrieved key:
```
n3w_xm45
```

---

## Gaining Internal Access

The three key parts were combined and submitted to the web application:

```
3aster_15_th3_n3w_xm45
```

This unlocked the **Secret Admin Console**, granting internal system access.

---

## On-Host Service Enumeration

Once inside the host, scanning was no longer necessary.
The operating system was queried directly.

### Command:
```bash
ss -tunlp
```

### Purpose:
- Lists all listening TCP and UDP services
- Reveals services bound to localhost (127.0.0.1)

### Important discovery:
- **MySQL database running on port 3306**

---

## Database Enumeration

Since the database was accessible locally, MySQL was queried directly.

### Commands:
```bash
mysql -D tbfcqa01 -e "show tables;"
mysql -D tbfcqa01 -e "select * from flags;"
```

### Final Flag:
```
THM{4ll_s3rvice5_d1sc0vered}
```

---

## Answers Summary

| Question | Answer |
|--------|-------|
| Website defacement message | Pwned by HopSec |
| First key (FTP) | 3aster_ |
| Second key (TBFC app) | 15_th3_ |
| Third key (DNS) | n3w_xm45 |
| MySQL port | 3306 |
| Final flag | THM{4ll_s3rvice5_d1sc0vered} |

---

## Lessons Learned
- Port scanning reveals exposed attack surfaces
- Banner grabbing exposes valuable service information
- Services can run on non-standard ports
- Anonymous FTP access is dangerous
- UDP services (DNS) should not be ignored
- Internal services may be hidden from external scans
- Service discovery is a critical step in both attack and defence
