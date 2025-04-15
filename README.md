# Venus VulnHub CTF Walkthrough 🌌

## 🎯 Objective
Perform a full penetration test on the "Venus" VulnHub virtual machine using Kali Linux. The goal is to gain user access, escalate to root, and capture both flags.

## 🛠️ Tools & Techniques
- Kali Linux
- arp-scan
- nmap
- gobuster
- hydra
- Burp Suite
- CyberChef
- Base64 + ROT13 decoding
- Python3 HTTP Server
- wget
- linpeas
- CVE-2021-4034 (PwnKit) Exploit

## 🔍 Summary of Steps

### 1. Network Discovery
Used `sudo arp-scan -l` to find active IPs. Target was identified at `10.0.2.15`.

### 2. Port Scanning
Nmap revealed ports 22 (SSH) and 8080 (HTTP) open.

### 3. Web Enumeration
Visited `http://10.0.2.15:8080/`, logged in with `guest:guest`, and found no sensitive data.

### 4. Directory Brute-Force
Used Gobuster and discovered the `/admin` path.

### 5. Hydra Brute Force
Tested many usernames with static password `pass`. Hydra returned multiple hits:
- guest:pass
- venus:pass
- magellan:pass

### 6. Login Failure
Login still failed via web. Suspected additional validation or CSRF protection.

### 7. Intercepted with Burp Suite
Captured the login request and noticed an `auth` cookie.

### 8. Cookie Decoding
Decoded cookie with CyberChef (Base64): guest:thrfg → ROT13 → guest:guest

### 9. Manual Auth Bypass
Replaced guest with other usernames → saw valid cookie returned for:
- venus:venusiangeology1989
- magellan:venusiangeology1989

### 10. Verified Working Credentials
Successfully logged in using both username/password combos.
- venus:venus
- magellan:venusiangeology1989

### 11. SSH Access as magellan
Used SSH to access the target:
```
ssh magellan@10.0.2.15
```
Captured user flag:
```
cat /home/magellan/user_flag.txt
# user_flag_e799a60032068b27b8ff212b57c200b0
```

### 12. LinPEAS Enumeration
Hosted linpeas.sh on Kali via Python server. Used wget to download it on Venus.
```
chmod +x linpeas.sh
./linpeas.sh | tee linpeas_output.txt
```

### 13. Found PwnKit Vulnerability
LinPEAS output showed vulnerability to CVE-2021-4034 (pkexec/PwnKit).

### 14. Downloaded Exploit
Transferred exploit zip (CVEmain.zip) via HTTP, unzipped on Venus.

### 15. Compiled Exploit with make
```
make
./cve-2021-4034
```

### 16. Root Access Gained
Verified root:
```
whoami
```

### 17. Captured Root Flag
```
cat /root/root_flag.txt
# root_flag_2ffb8a7239d90b3d3db02a258d009c79
```

## 🚩 Flags Captured
- **User flag:** e799a60032068b27b8ff212b57c200b0
- **Root flag:** 2ffb8a7239d90b3d3db02a258d009c79

## 🔗 References
- https://download.vulnhub.com/theplanets/Venus.ova
- https://github.com/berdav/CVE-2021-4034
- https://gchq.github.io/CyberChef/

## 📁 Full Walkthrough
See `docs/full-walkthrough.md` for detailed steps and commands.
