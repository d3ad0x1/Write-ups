---
title: Internal AD Assessment
tags:
  - AD
  - Windows
  - PrivEsc
difficulty: Medium
---

# Internal AD Assessment

## Recon

Initial reconnaissance inside the internal network.

```bash
nmap -sV 10.10.10.15
```

## Enumeration

Enumerating SMB shares and users.

```bash
enum4linux-ng 10.10.10.15
```

## Credentials

Discovered credentials in configuration files.

```txt
admin:Password123
```

## Privilege Escalation

Privilege escalation through vulnerable service permissions.

```powershell
whoami /priv
```

## Notes

- SMB signing disabled
- WinRM enabled
- Kerberos active
