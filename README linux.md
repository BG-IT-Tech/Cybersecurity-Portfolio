# Linux Hardening Checklist — Nexarion Solutions

**Project Type:** Endpoint / Server Hardening  
**Scenario:** Hardening an Ubuntu 22.04 LTS server used as an internal file server (`NEXARION-FS02`) at Nexarion Solutions  
**Security+ Domains:** Implementation | Architecture and Design

---

## Scenario Background

Nexarion Solutions recently deployed a second Ubuntu 22.04 LTS server to act as a departmental file share for the Legal team. The server was stood up quickly with default settings. This checklist documents the hardening steps taken to bring it to an acceptable security baseline before going into production.

**Target System:** Ubuntu 22.04 LTS — `NEXARION-FS02`  
**IP Address:** `10.10.20.15` (Legal VLAN)  
**Applied By:** IT Security team

---

## 1. User Accounts and Authentication

### 1.1 Remove or lock unnecessary accounts

```bash
# List all users with login shells
cat /etc/passwd | grep -v nologin | grep -v false

# Lock the root account from direct login
sudo passwd -l root

# Verify no unauthorized accounts exist
awk -F: '($3 >= 1000) {print $1}' /etc/passwd
```

**Result:** Only one standard admin account (`nxadmin`) found. Root login locked. ✅

### 1.2 Enforce strong password policy

Install and configure `libpam-pwquality`:

```bash
sudo apt install libpam-pwquality -y
```

Edit `/etc/security/pwquality.conf`:

```
minlen = 14
dcredit = -1
ucredit = -1
lcredit = -1
ocredit = -1
maxrepeat = 3
```

**Result:** Passwords must be at least 14 characters with complexity requirements. ✅

### 1.3 Configure sudo access

```bash
# Ensure only authorized users are in the sudo group
getent group sudo

# Output:
sudo:x:27:nxadmin
```

**Result:** Only `nxadmin` has sudo access. ✅

### 1.4 Set account lockout policy

Edit `/etc/pam.d/common-auth` to add:

```
auth required pam_tally2.so onerr=fail audit silent deny=5 unlock_time=900
```

**Result:** Accounts lock after 5 failed attempts; unlock after 15 minutes. ✅

---

## 2. SSH Hardening

Edit `/etc/ssh/sshd_config`:

```bash
# Disable root login over SSH
PermitRootLogin no

# Disable password authentication — use SSH keys only
PasswordAuthentication no

# Only allow specific users
AllowUsers nxadmin

# Change SSH port (security through obscurity — minor benefit)
Port 2222

# Disable X11 forwarding (not needed on a server)
X11Forwarding no

# Set idle timeout — disconnect after 10 minutes of inactivity
ClientAliveInterval 600
ClientAliveCountMax 0

# Use only strong key exchange algorithms
KexAlgorithms curve25519-sha256,diffie-hellman-group14-sha256

# Restrict ciphers
Ciphers aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr
```

Restart SSH:

```bash
sudo systemctl restart sshd
```

**Result:** SSH locked to key-based auth, non-default port, root login disabled, idle timeout set. ✅

---

## 3. Firewall Configuration (UFW)

```bash
# Install UFW if not present
sudo apt install ufw -y

# Default: deny all incoming, allow all outgoing
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH on the custom port
sudo ufw allow 2222/tcp

# Allow Samba for Legal team file sharing (from Legal VLAN only)
sudo ufw allow from 10.10.20.0/24 to any port 445 proto tcp

# Enable the firewall
sudo ufw enable

# Verify rules
sudo ufw status verbose
```

**UFW Status Output:**

```
Status: active

To                         Action      From
--                         ------      ----
2222/tcp                   ALLOW IN    Anywhere
445/tcp                    ALLOW IN    10.10.20.0/24
```

**Result:** Only required ports open; all others blocked by default. ✅

---

## 4. System Updates and Patching

```bash
# Update package list and upgrade all packages
sudo apt update && sudo apt upgrade -y

# Enable automatic security updates
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

Edit `/etc/apt/apt.conf.d/50unattended-upgrades` to enable:

```
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
};
Unattended-Upgrade::Automatic-Reboot "false";
Unattended-Upgrade::Mail "security-team@nexarion.local";
```

**Result:** Security updates apply automatically; team is emailed on updates. ✅

---

## 5. Filesystem and Permissions

```bash
# Find world-writable files (potential security risk)
find / -xdev -type f -perm -0002 -ls 2>/dev/null

# Find SUID/SGID binaries (should be reviewed)
find / -xdev \( -perm -4000 -o -perm -2000 \) -type f -ls 2>/dev/null

# Secure /tmp with noexec, nosuid, nodev
# Add to /etc/fstab:
tmpfs /tmp tmpfs defaults,noexec,nosuid,nodev 0 0
```

**Result:** No unexpected world-writable files. SUID binaries reviewed — all legitimate. `/tmp` mounted with restrictions. ✅

---

## 6. Logging and Auditing

### 6.1 Enable auditd

```bash
sudo apt install auditd -y
sudo systemctl enable auditd
sudo systemctl start auditd
```

Add audit rules to `/etc/audit/rules.d/nexarion.rules`:

```
# Monitor authentication events
-w /etc/passwd -p wa -k passwd_changes
-w /etc/shadow -p wa -k shadow_changes
-w /etc/sudoers -p wa -k sudoers_changes

# Monitor SSH configuration
-w /etc/ssh/sshd_config -p wa -k sshd_config

# Monitor privileged commands
-a always,exit -F arch=b64 -S execve -F euid=0 -k root_commands

# Monitor login/logout
-w /var/log/lastlog -p wa -k logins
```

### 6.2 Configure log forwarding

Logs forwarded to Nexarion's central Splunk SIEM via `rsyslog`:

```bash
sudo apt install rsyslog -y
```

Add to `/etc/rsyslog.conf`:

```
*.* @10.10.10.50:514   # Splunk syslog receiver
```

**Result:** All privileged activity, auth events, and config changes logged and forwarded. ✅

---

## 7. Remove Unnecessary Services

```bash
# List running services
systemctl list-units --type=service --state=running

# Disable services not required on a file server
sudo systemctl disable --now cups       # Printing
sudo systemctl disable --now avahi-daemon  # mDNS discovery
sudo systemctl disable --now bluetooth  # Bluetooth
```

**Services disabled:** `cups`, `avahi-daemon`, `bluetooth` ✅

---

## 8. Fail2Ban (Brute Force Protection)

```bash
sudo apt install fail2ban -y
```

Create `/etc/fail2ban/jail.local`:

```ini
[DEFAULT]
bantime  = 3600
findtime = 600
maxretry = 5

[sshd]
enabled = true
port    = 2222
logpath = %(sshd_log)s
backend = %(syslog_backend)s
```

```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

**Result:** SSH brute-force attempts will result in a 1-hour IP ban after 5 failures. ✅

---

## Security+ Concepts Demonstrated

- **Hardening** — removing unnecessary services, locking down SSH
- **Least privilege** — restricted sudo access, no root login
- **Authentication controls** — key-based SSH, lockout policies
- **Network security** — UFW firewall with default-deny
- **Logging and auditing** — auditd with rules, centralized SIEM forwarding
- **Patch management** — automated security updates
- **Defense in depth** — fail2ban + firewall + key auth layered together

---

## Reflection

This project taught me that Linux servers are not "secure by default" — they need the same careful hardening as Windows systems. The SSH hardening section was especially interesting; I learned that disabling password authentication entirely and using only SSH keys dramatically reduces brute-force risk. I also didn't know about auditd before this project — it's a powerful tool for tracking exactly who ran what command and when, which would be essential during a forensic investigation.
