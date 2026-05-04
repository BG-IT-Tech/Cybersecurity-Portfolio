# Incident Response Playbook — Nexarion Solutions

**Project Type:** Incident Response  
**Scenario:** Ransomware infection on a workstation in the accounting department  
**Security+ Domains:** Threats, Attacks & Vulnerabilities | Incident Response

---

## Scenario Background

On a Tuesday morning, a help desk ticket is raised by an accounting employee who reports that files on their workstation are showing a `.nxrlocked` extension and a ransom note appeared on their desktop. The machine has shared drive access to the `\\NEXARION-FS01\Finance` network share.

**Affected Asset:** NXRWS-ACCT-07 (Windows 10 Pro, accounting department)  
**User:** A standard domain user account with read/write access to the Finance share  
**Time Reported:** 08:47 AM

---

## Phase 1 — Preparation

Before an incident occurs, Nexarion Solutions should have:

- An up-to-date asset inventory with criticality ratings
- Defined IR roles: Incident Commander, Analyst, Communications Lead
- Offline backups of all critical shares (tested monthly)
- EDR (Endpoint Detection and Response) software deployed on all endpoints
- An isolated "quarantine" VLAN ready to receive affected machines
- Contact list for legal counsel, cyber insurance provider, and affected clients

---

## Phase 2 — Identification

**Step 1 — Confirm the incident is real**

- Review the ransom note. Note the file extension (`.nxrlocked`) and any bitcoin wallet addresses or contact emails in the note (do NOT contact attackers yet).
- Check the EDR console for alerts on NXRWS-ACCT-07.
- Run a query in the EDR for any other machines with `.nxrlocked` files.

**Step 2 — Determine scope**

| Question | Finding |
|----------|---------|
| Is encryption still active? | Unknown — must isolate first |
| Is the Finance share affected? | Check `\\NEXARION-FS01\Finance` for `.nxrlocked` files |
| Is this one machine or multiple? | EDR query pending |
| What user account was active? | Domain user `jsmith` |

**Step 3 — Initial indicators**

- Ransom note file: `READ_ME_NXRLOCKED.txt` on the desktop
- File extensions changed to `.nxrlocked`
- High disk I/O on NXRWS-ACCT-07 observed at 08:31 AM (prior to user noticing)
- Suspicious process: `svchost32.exe` (note: not a legitimate Windows process — legitimate is `svchost.exe`)

---

## Phase 3 — Containment

### Short-Term Containment (first 15 minutes)

1. **Disconnect NXRWS-ACCT-07 from the network immediately** — unplug Ethernet cable or disable the switch port remotely via managed switch console. Do NOT power off the machine (volatile memory may contain encryption keys).
2. Disable the domain account `jsmith` in Active Directory to prevent lateral movement via those credentials.
3. Block the Finance share from new connections at the file server level temporarily.
4. Alert the IT manager and Incident Commander.

### Long-Term Containment

1. Move the affected machine to the quarantine VLAN for forensic imaging.
2. Scan all other accounting workstations for the `.nxrlocked` extension.
3. Review authentication logs in Active Directory for any lateral movement by `jsmith` in the past 72 hours.
4. Check firewall logs for any unusual outbound traffic from NXRWS-ACCT-07 — particularly to unknown external IPs on ports 443 or 80 (C2 beaconing).

---

## Phase 4 — Eradication

1. Acquire a forensic image of NXRWS-ACCT-07's hard drive before any changes (use a write blocker).
2. Identify the ransomware family if possible (check ransom note language and file extension against resources like ID Ransomware at nomoreransom.org).
3. Determine the initial access vector:
   - Review email logs for `jsmith` — was there a phishing email with a malicious attachment?
   - Review browser history (if accessible) for drive-by download sites.
   - Check if any USB devices were plugged in recently.
4. Once the attack vector is confirmed, remediate it across all endpoints:
   - Push email filtering rules to block the malicious sender/domain.
   - Deploy patches for any exploited vulnerabilities.
   - Rotate all credentials that `jsmith` had access to.
5. Wipe NXRWS-ACCT-07 and rebuild from a clean image.

---

## Phase 5 — Recovery

1. Restore Finance share files from the most recent clean backup (verify backup integrity before restore).
2. Confirm no `.nxrlocked` files remain on the share after restore.
3. Reconnect NXRWS-ACCT-07 to the network (on the production VLAN) only after a clean rebuild and AV/EDR scan.
4. Re-enable `jsmith`'s account with a forced password reset and MFA enrollment.
5. Monitor NXRWS-ACCT-07 closely for 72 hours post-recovery via EDR.

---

## Phase 6 — Lessons Learned

A post-incident review should be held within 5 business days and should answer:

- How did the ransomware get onto the machine?
- How long was the attacker present before encryption began?
- Did our EDR alert in time? If not, why not?
- Were backups clean and restorable? How long did recovery take?
- What policy or technical control would have prevented this?

**Recommended improvements from this incident:**

| Finding | Recommendation | Priority |
|---------|---------------|----------|
| `jsmith` had excessive share permissions | Implement least-privilege access reviews quarterly | High |
| No email attachment sandboxing | Deploy email security gateway with sandboxing | High |
| EDR alert came after user noticed issue | Tune EDR rules for high disk I/O and mass file renaming | High |
| Backups not tested recently | Schedule monthly backup restoration tests | Medium |

---

## Security+ Concepts Demonstrated

- **Incident response lifecycle** (PICERL: Preparation, Identification, Containment, Eradication, Recovery, Lessons Learned)
- **Threat classification** — ransomware as a type of malware
- **Least privilege principle**
- **Chain of custody** — forensic imaging before eradication
- **Defense in depth** — EDR, email filtering, network segmentation, backups

---

## Reflection

This project taught me how structured and time-sensitive incident response really is. Before this, I thought you just removed the virus and moved on. Working through each phase helped me understand why containment comes before eradication — if you wipe the machine first, you lose forensic evidence and might miss other infected systems. I also didn't realize how important the lessons-learned phase is for actually improving security over time rather than just fixing the immediate problem.
