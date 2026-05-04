# Phishing Awareness Pack — Nexarion Solutions

**Project Type:** Security Awareness Training  
**Scenario:** Creating a phishing awareness pack for non-technical staff at Nexarion Solutions following a simulated phishing campaign  
**Security+ Domains:** Threats, Attacks & Vulnerabilities | Governance, Risk, and Compliance

---

## Scenario Background

Nexarion Solutions ran a simulated phishing campaign using GoPhish in February. The results were concerning:

| Metric | Result |
|--------|--------|
| Emails sent | 200 |
| Emails opened | 147 (73.5%) |
| Links clicked | 62 (31%) |
| Credentials submitted on fake login page | 29 (14.5%) |
| Reported to IT as suspicious | 11 (5.5%) |

The worst-performing departments were Customer Support (48% click rate) and Accounting (39% click rate). This awareness pack was developed to train all staff, with extra sessions for those departments.

---

## Part 1 — What Is Phishing?

Phishing is a type of social engineering attack where an attacker sends a fraudulent message — usually an email — designed to trick you into revealing sensitive information, clicking a malicious link, or downloading malware.

**Why attackers use phishing:**
- It's easier to trick a person than to hack a system
- It doesn't require advanced technical skill
- One successful click can give an attacker a foothold into the entire company

---

## Part 2 — Types of Phishing

| Type | Description | Example |
|------|-------------|---------|
| **Phishing** | Mass emails sent to many people | Fake "IT department" password reset email |
| **Spear Phishing** | Targeted attack using your name, role, or company details | Email appearing to be from your manager |
| **Whaling** | Spear phishing targeting executives | Fake legal notice sent to the CEO |
| **Smishing** | Phishing via SMS text message | "Your package is delayed — click here" |
| **Vishing** | Phishing via voice call | Fake bank calling to "verify your account" |
| **Business Email Compromise (BEC)** | Attacker impersonates a supplier or executive to request a wire transfer | Fake invoice from a trusted vendor |

---

## Part 3 — Real Example Analysis

Below is an analysis of the simulated phishing email used in Nexarion's February campaign.

---

**Simulated Email (Fictional — For Training Only):**

> **From:** it-support@nexarion-helpdesk.net  
> **To:** [employee]@nexarion.com  
> **Subject:** ⚠️ ACTION REQUIRED: Your account will be suspended in 24 hours  
>
> Dear Nexarion Employee,
>
> We have detected unusual activity on your account. To avoid suspension, please verify your credentials immediately by clicking the link below.
>
> [Verify My Account Now]
>
> If you do not verify within 24 hours, your account will be locked and you will lose access to all company systems.
>
> — Nexarion IT Support Team

---

**Red Flags in This Email:**

| # | Red Flag | Explanation |
|---|----------|-------------|
| 1 | Sender domain is `nexarion-helpdesk.net` not `nexarion.com` | Attackers register similar-looking domains |
| 2 | Creates urgency ("24 hours", "suspended") | Pressure tactics stop you thinking clearly |
| 3 | Generic greeting — no name used | Mass phishing emails often don't know your name |
| 4 | Vague threat ("unusual activity") with no specifics | Legitimate IT teams give specific details |
| 5 | Link text doesn't match actual URL | Hover over links before clicking — check the real destination |
| 6 | No digital signature or IT ticket reference | Real IT communications at Nexarion include a ticket number |

---

## Part 4 — How to Spot a Phishing Email

Use the **SLICE** method when checking any suspicious email:

**S — Sender**  
Does the email address match the organisation it claims to be from? Check the full domain, not just the display name.

**L — Links**  
Hover over any link before clicking. Does the URL look legitimate? Watch for typosquatting (e.g., `nexarlon.com` instead of `nexarion.com`).

**I — Information Requested**  
Legitimate companies will almost never ask for your password, PIN, or full card number via email.

**C — Content and Tone**  
Is the email creating urgency or fear? Poor grammar and spelling are warning signs, but sophisticated attacks may have none of these.

**E — Expected?**  
Were you expecting this email? Did you request a password reset? If not, treat it with suspicion.

---

## Part 5 — What to Do If You Receive a Suspicious Email

```
┌─────────────────────────────────────────────────────┐
│         RECEIVED A SUSPICIOUS EMAIL?                │
│                                                     │
│  1. DO NOT click any links                          │
│  2. DO NOT download any attachments                 │
│  3. DO NOT reply to the sender                      │
│  4. DO report it — forward to:                      │
│        phishing@nexarion.com                        │
│     or call the IT Help Desk: ext. 2100             │
│  5. If you already clicked — call IT immediately    │
│        Time matters. Don't wait.                    │
└─────────────────────────────────────────────────────┘
```

**If you already clicked a link or entered credentials:**
- Call the IT Help Desk immediately (ext. 2100)
- Do not try to fix it yourself
- Do not power off your computer — IT may need to examine it
- Change your password from a different, unaffected device

---

## Part 6 — Quick Reference Card

*(This card should be printed and kept at workstations)*

```
╔══════════════════════════════════════╗
║    NEXARION PHISHING QUICK GUIDE     ║
╠══════════════════════════════════════╣
║  ✅ Hover links before clicking      ║
║  ✅ Check the sender's full domain   ║
║  ✅ Report suspicious emails         ║
║     → phishing@nexarion.com          ║
╠══════════════════════════════════════╣
║  ❌ Never enter your password        ║
║     from an email link               ║
║  ❌ Never open unexpected            ║
║     attachments                      ║
║  ❌ Never be rushed — slow down      ║
╠══════════════════════════════════════╣
║  SPOTTED SOMETHING? CALL IT:         ║
║         Ext. 2100                    ║
╚══════════════════════════════════════╝
```

---

## Part 7 — Quiz (Post-Training Check)

**Q1.** You receive an email from `hr@nexarion-portal.net` asking you to confirm your payroll details. What should you do?

- A) Reply with your bank details since it looks official  
- B) Click the link to see what it says  
- **C) Report it to phishing@nexarion.com — the domain is not nexarion.com** ✅  
- D) Ignore it and delete it without reporting

**Q2.** What is spear phishing?

- A) Phishing via text message  
- **B) A targeted phishing attack using personal details about the victim** ✅  
- C) A phishing attack against executives only  
- D) Phishing via phone call

**Q3.** You hover over a link in an email and see the URL is `http://nexarlon.com/login`. The email claims to be from Nexarion IT. What do you notice?

- A) Nothing suspicious — it looks fine  
- B) It should use HTTPS but it's HTTP  
- **C) The domain is misspelled (`nexarlon` not `nexarion`) — this is typosquatting** ✅  
- D) Both B and C are correct

**Q4.** What should you do if you accidentally clicked a phishing link?

- A) Clear your browser history and say nothing  
- B) Power off your computer immediately  
- **C) Call IT immediately and do not power off the machine** ✅  
- D) Run antivirus and hope for the best

---

## Security+ Concepts Demonstrated

- **Social engineering** — phishing, spear phishing, vishing, smishing, whaling, BEC
- **User awareness training** — a key administrative control
- **Defence in depth** — technical controls (email filters) must be paired with human controls
- **Indicators of compromise** — recognising suspicious sender domains, urgency tactics

---

## Reflection

This project taught me that the human element is often the weakest link in cybersecurity — not the technology. No firewall can stop an employee from willingly typing their password into a fake login page. Building this awareness pack made me think from both sides: as a defender creating training, and as an attacker designing a convincing phishing email. Understanding what makes phishing effective (urgency, authority, familiarity) helped me write better red-flag guidance. I also learned how important it is to make reporting easy — if people feel embarrassed about falling for phishing, they won't report it, and that makes the damage worse.
