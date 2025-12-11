#  Splunk Basics - Did you SIEM?

## Overview
In this room, we investigated a compromised web server using Splunk.  
We analyzed logs, identified the attacker, followed the attack chain,  
and confirmed outbound C2 communication and data exfiltration.

---

#  Data Sources

### web_traffic
- HTTP requests to the server  
- Key fields: client_ip, user_agent, path, status

### firewall_logs
- Traffic allowed/blocked  
- Key fields: src_ip, dest_ip, bytes_transferred, action

---

#  Starting the Investigation

### Show all logs
```spl
index=main
```

### Only web logs
```spl
index=main sourcetype=web_traffic
```

---

#  Finding the Attack Day

### Count logs per day
```spl
index=main sourcetype=web_traffic 
| timechart span=1d count
```

### Sort by busiest day
```spl
index=main sourcetype=web_traffic 
| timechart span=1d count 
| sort by count 
| reverse
```

 Peak traffic day: **2025-10-12**

---

#  Identifying the Attacker

### Remove normal browsers (keep suspicious traffic)
```spl
index=main sourcetype=web_traffic 
user_agent!=*Mozilla* 
user_agent!=*Chrome* 
user_agent!=*Safari* 
user_agent!=*Firefox*
```

### Most suspicious IPs
```spl
sourcetype=web_traffic 
user_agent!=*Mozilla* 
user_agent!=*Chrome* 
user_agent!=*Safari* 
user_agent!=*Firefox* 
| stats count by client_ip 
| sort -count 
| head 5
```

 Attacker IP: **198.51.100.55**

---

#  Attack Chain

## 1️ Recon
```spl
sourcetype=web_traffic 
client_ip="198.51.100.55" 
AND path IN ("/.env","/*phpinfo*","/.git*") 
| table _time, path, user_agent, status
```

---

## 2️ Path Traversal Attempts
```spl
sourcetype=web_traffic 
client_ip="198.51.100.55" 
AND path="*..\/..\/*" OR path="*redirect*" 
| stats count by path
```
 Attempts: **658**

---

## 3️ SQL Injection
```spl
sourcetype=web_traffic 
client_ip="198.51.100.55" 
AND user_agent IN ("*sqlmap*","*Havij*") 
| table _time, path, status
```
 Havij events: **993**

---

## 4️ Exfiltration Attempts
```spl
sourcetype=web_traffic 
client_ip="198.51.100.55" 
AND path IN ("*backup.zip*","*logs.tar.gz*") 
| table _time, path, user_agent
```

---

## 5️ Webshell & Ransomware Execution
```spl
sourcetype=web_traffic 
client_ip="198.51.100.55" 
AND path IN ("*bunnylock.bin*","*shell.php?cmd=*") 
| table _time, path, user_agent, status
```

---

#  C2 Communication (Firewall Logs)
```spl
sourcetype=firewall_logs 
src_ip="10.10.1.5" 
AND dest_ip="198.51.100.55" 
AND action="ALLOWED" 
| table _time, action, protocol, src_ip, dest_ip, dest_port, reason
```

---

#  Data Exfiltration Total
```spl
sourcetype=firewall_logs 
src_ip="10.10.1.5" 
AND dest_ip="198.51.100.55" 
AND action="ALLOWED" 
| stats sum(bytes_transferred) by src_ip
```

 Bytes exfiltrated: **126167**

---

#  Answers Summary

| Question | Answer |
|---------|--------|
| Attacker IP | 198.51.100.55 |
| Peak traffic day | 2025-10-12 |
| Havij events | 993 |
| Path traversal attempts | 658 |
| Data exfiltrated | 126167 |

---

#  Lessons Learned
- Filtering logs reveals attacker behavior  
- Recon → traversal → SQLi → webshell → ransomware → exfiltration  
- Outbound firewall logs confirm C2 channels  
- SQLi tools are visible in user_agent  
- Webshell = full remote access  
