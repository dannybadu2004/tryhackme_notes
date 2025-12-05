# Pre Security – Important Linux Commands

This file contains the most important Linux commands learned during the TryHackMe Pre Security path.

---

## File & Directory Management
touch file.txt – create file  
mkdir folder – create folder  
cd folder – change directory  
ls -la – show permissions and hidden files  
file file.txt – show file type  

---

## File Viewing
cat file.txt – read file  
less file.txt – read file page by page  
wc file.txt – count lines, words, and characters  

---

## Searching
grep "text" file.txt – search inside file  
find / -name filename – locate file  

---

## User Management
su – switch user  
su - – login as another user  
whoami – current user  

---

## Network & Transfers
ssh user@ip – connect to server  
scp file user@ip:/path – upload file  
scp user@ip:/path file – download file  
wget http://site/file – HTTP download  
python3 -m http.server – local web server  

---

## Help & Documentation
command --help / -h – basic help  
man command – full manual  

---

## Important Directories
/etc – system config files  
/var – logs & service files  
/root – home of root  
/tmp – temporary files  

---

## Process Management
ps aux – process list  
top – task manager  
htop – advanced process viewer  
kill PID – stop process  

---

## Signals
SIGTERM – safe kill  
SIGKILL – force kill  
SIGSTOP – pause process  

---

## Background & Logic
command & – run in background  
fg – bring to foreground  
&& – run next if successful  

---

## Output Redirection
echo "text" > file.txt – overwrite file  
echo "text" >> file.txt – append file  

---

## Services & Scheduling
systemctl start/stop/enable/disable service  
crontab -l – list jobs  
crontab -e – edit jobs  

---

## Package Management
apt update  
apt upgrade  
apt install package  
add-apt-repository ppa:name  

---

## Notes
Commands practiced in TryHackMe and Linux labs.
