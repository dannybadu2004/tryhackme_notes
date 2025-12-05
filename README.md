# Linux & Cybersecurity Notes (TryHackMe)

## Linux CLI Essentials

### Navigation
- `pwd` – show current directory
- `ls` – list files
- `cd` – change directory
- `cat` – view file contents

### Hidden Files
- `ls -la` – list everything (including hidden)
- `.bash_history` – command history file

### Log Analysis
- `/var/log` – system logs directory
- `grep "Failed password" auth.log` – search for failed logins

### File Searching
- `find /home -name "*egg*"` – find suspicious files

### Special Symbols
- `|`  – pipe output
- `>`  – overwrite file
- `>>` – append
- `&&` – run if previous succeeds

### Privileges
- `sudo su` – switch to root user
- `whoami` – show active user

### Example of Malware Script
```bash
cat wishlist.txt | sort | uniq > /tmp/dump.txt
rm wishlist.txt
mv eastmas.txt wishlist.txt
