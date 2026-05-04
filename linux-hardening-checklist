# Windows Hardening Checklist — Nexarion Solutions

**Project Type:** Endpoint Hardening  
**Scenario:** Hardening a standard Windows 10 Pro workstation issued to new employees at Nexarion Solutions  
**Security+ Domains:** Implementation | Architecture and Design

---

## Scenario Background

Nexarion Solutions issues Windows 10 Pro workstations to all employees. A recent internal audit found that default Windows settings were not changed on several machines, leaving them exposed to common attacks. This checklist documents the hardening steps applied to bring workstations into compliance with Nexarion's internal security baseline.

**Target System:** Windows 10 Pro (22H2), domain-joined to `NEXARION.LOCAL`  
**Applied By:** IT Security team during device provisioning

---

## Account and Authentication

| # | Control | Status | Notes |
|---|---------|--------|-------|
| 1 | Rename the built-in Administrator account | ✅ Done | Renamed to `NX-LocalAdmin` |
| 2 | Disable the built-in Guest account | ✅ Done | Disabled via Local Security Policy |
| 3 | Require passwords of at least 14 characters | ✅ Done | Set via Group Policy |
| 4 | Enable password complexity requirements | ✅ Done | GPO: `Password must meet complexity requirements` |
| 5 | Set account lockout after 5 failed attempts | ✅ Done | Lockout duration: 15 minutes |
| 6 | Enforce MFA for all domain accounts | ✅ Done | Microsoft Authenticator enrolled |
| 7 | Disable NTLM authentication where possible | ✅ Done | Force Kerberos via GPO |
| 8 | Remove unused local user accounts | ✅ Done | Audited — no extra accounts found |

---

## Windows Defender and Antivirus

| # | Control | Status | Notes |
|---|---------|--------|-------|
| 9 | Confirm Windows Defender is enabled and up to date | ✅ Done | Definitions updated; last scan: clean |
| 10 | Enable real-time protection | ✅ Done | |
| 11 | Enable cloud-delivered protection | ✅ Done | |
| 12 | Enable controlled folder access (ransomware protection) | ✅ Done | Protected folders: Documents, Desktop, Finance |
| 13 | Schedule weekly full scans | ✅ Done | Scheduled via Task Scheduler — Sundays 02:00 |
| 14 | Enable tamper protection | ✅ Done | Prevents malware from disabling Defender |

---

## Windows Firewall

| # | Control | Status | Notes |
|---|---------|--------|-------|
| 15 | Enable Windows Firewall on all profiles (Domain, Private, Public) | ✅ Done | |
| 16 | Block inbound connections by default | ✅ Done | Only allow established/related traffic |
| 17 | Disable Remote Desktop Protocol (RDP) if not required | ✅ Done | Not needed on standard workstations |
| 18 | Block outbound SMB (port 445) to the internet | ✅ Done | Firewall outbound rule added |
| 19 | Log dropped packets | ✅ Done | Log path: `C:\Windows\System32\LogFiles\Firewall\pfirewall.log` |

---

## Windows Update and Patching

| # | Control | Status | Notes |
|---|---------|--------|-------|
| 20 | Enable automatic updates | ✅ Done | Managed via WSUS server on `NEXARION.LOCAL` |
| 21 | Apply all critical and security patches | ✅ Done | Last patched: current month |
| 22 | Enable Microsoft Update (to include Office/other MS products) | ✅ Done | |
| 23 | Configure update installation outside business hours | ✅ Done | Install window: 22:00–04:00 |

---

## User Privileges and Application Control

| # | Control | Status | Notes |
|---|---------|--------|-------|
| 24 | Ensure standard users do not have local admin rights | ✅ Done | Standard domain users only |
| 25 | Enable User Account Control (UAC) at highest setting | ✅ Done | |
| 26 | Enable AppLocker to restrict unauthorized software execution | ✅ Done | Whitelist-based policy applied |
| 27 | Disable PowerShell v2 (older, no logging support) | ✅ Done | `Disable-WindowsOptionalFeature -Online -FeatureName MicrosoftWindowsPowerShellV2` |
| 28 | Enable PowerShell Script Block Logging | ✅ Done | GPO: `Turn on PowerShell Script Block Logging` |
| 29 | Enable Constrained Language Mode for PowerShell | ✅ Done | Limits dangerous cmdlets for standard users |

---

## Audit Logging and Monitoring

| # | Control | Status | Notes |
|---|---------|--------|-------|
| 30 | Enable audit logging for logon/logoff events | ✅ Done | Both success and failure |
| 31 | Enable audit logging for account management | ✅ Done | Captures account creation, deletion, password changes |
| 32 | Enable audit logging for object access (file/folder) | ✅ Done | Focused on sensitive folders |
| 33 | Enable audit logging for policy changes | ✅ Done | |
| 34 | Forward event logs to SIEM (Splunk) | ✅ Done | Logs forwarded via Windows Event Forwarding |
| 35 | Set Security event log size minimum 1 GB | ✅ Done | Prevents log overwrite before collection |

---

## Encryption

| # | Control | Status | Notes |
|---|---------|--------|-------|
| 36 | Enable BitLocker on the system drive (C:) | ✅ Done | TPM + PIN required at boot |
| 37 | Store BitLocker recovery keys in Active Directory | ✅ Done | Keys escrowed to `NEXARION.LOCAL` domain |
| 38 | Enable BitLocker on removable drives | ✅ Done | Users prompted to encrypt USB drives on insertion |

---

## Unnecessary Services and Features

| # | Control | Status | Notes |
|---|---------|--------|-------|
| 39 | Disable Print Spooler if not needed | ✅ Done | Standard workstations use network printing; local spooler disabled |
| 40 | Disable Windows Remote Management (WinRM) | ✅ Done | Not required on standard workstations |
| 41 | Disable Bluetooth if not required | ✅ Done | Device Manager — Bluetooth adapter disabled |
| 42 | Disable AutoPlay/AutoRun for all drives | ✅ Done | Prevents malicious USB attacks |
| 43 | Uninstall Internet Explorer | ✅ Done | Legacy browser; removed via Windows Features |
| 44 | Disable SMBv1 | ✅ Done | `Set-SmbServerConfiguration -EnableSMB1Protocol $false` |

---

## Browser Security (Microsoft Edge)

| # | Control | Status | Notes |
|---|---------|--------|-------|
| 45 | Enable SmartScreen for Edge | ✅ Done | |
| 46 | Block password saving in the browser | ✅ Done | Enforced via Edge GPO |
| 47 | Disable extension installs from unmanaged sources | ✅ Done | Only allow extensions from the managed store |
| 48 | Enable Enhanced Security Mode | ✅ Done | |

---

## Security+ Concepts Demonstrated

- **Least privilege** — standard users without admin rights, AppLocker
- **Defense in depth** — multiple overlapping controls (AV, firewall, encryption, logging)
- **Hardening** — disabling unnecessary services and legacy protocols (SMBv1, IE)
- **Auditing and accountability** — event logging forwarded to SIEM
- **Encryption at rest** — BitLocker on system and removable drives
- **Authentication controls** — MFA, lockout policy, complexity requirements

---

## Reflection

This project showed me how many attack surfaces exist on a default Windows installation. SMBv1, for example, was used in the WannaCry ransomware attack in 2017 — and it's still enabled by default on some Windows versions. Going through each category (accounts, firewall, patching, logging) made me realize that hardening is never just one thing; it's a series of layered controls that work together. I also learned why logging is so important — without it, you can't detect or investigate incidents later.
