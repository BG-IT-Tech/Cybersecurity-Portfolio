# Risk Assessment — Nexarion Solutions

**Project Type:** Risk Assessment  
**Scenario:** Annual information security risk assessment for Nexarion Solutions  
**Security+ Domains:** Governance, Risk, and Compliance (GRC)

---

## Scenario Background

Nexarion Solutions is a mid-sized IT services firm with approximately 200 employees. The company handles sensitive data for healthcare and legal clients, making it subject to HIPAA considerations and professional confidentiality requirements. This risk assessment was conducted as part of the company's annual security review cycle.

**Scope:** All information systems, personnel, and physical locations within Nexarion Solutions' primary office and cloud infrastructure  
**Assessment Date:** Q1 2025  
**Assessor:** IT Security Team

---

## Risk Scoring Methodology

Each risk is scored using the following matrix:

**Likelihood (L):**
| Score | Rating | Description |
|-------|--------|-------------|
| 1 | Low | Unlikely to occur within the next 12 months |
| 2 | Medium | Could occur within the next 12 months |
| 3 | High | Likely to occur or has occurred recently |

**Impact (I):**
| Score | Rating | Description |
|-------|--------|-------------|
| 1 | Low | Minor disruption; no data loss; easily recoverable |
| 2 | Medium | Moderate disruption; possible data breach; hours to recover |
| 3 | High | Major disruption; confirmed data breach; days/weeks to recover |

**Risk Score = Likelihood × Impact**

| Score | Risk Level |
|-------|-----------|
| 1–2 | Low |
| 3–4 | Medium |
| 6–9 | High |

---

## Risk Register

### Risk 1 — Phishing / Business Email Compromise

| Field | Detail |
|-------|--------|
| **Description** | Employees receive and act on phishing emails, resulting in credential theft or wire fraud |
| **Threat Actor** | External cybercriminal |
| **Vulnerability** | Low security awareness; no email sandboxing in place |
| **Likelihood** | 3 — High (simulated phishing campaign showed 31% click rate) |
| **Impact** | 3 — High (credentials or financial fraud could affect clients) |
| **Risk Score** | **9 — High** |
| **Current Controls** | Basic spam filter; annual awareness reminder |
| **Recommended Mitigations** | Deploy email sandboxing gateway; run quarterly phishing simulations; mandatory annual security awareness training |
| **Residual Risk after Mitigation** | Medium (3–4) |
| **Owner** | Head of IT Security |
| **Target Date** | Q2 2025 |

---

### Risk 2 — Ransomware Infection

| Field | Detail |
|-------|--------|
| **Description** | Ransomware encrypts files on workstations or network shares, disrupting operations |
| **Threat Actor** | External cybercriminal; ransomware-as-a-service operator |
| **Vulnerability** | Endpoints lack EDR; backup restoration has not been tested in 8 months |
| **Likelihood** | 2 — Medium |
| **Impact** | 3 — High (client data at risk; potential HIPAA notification required) |
| **Risk Score** | **6 — High** |
| **Current Controls** | Windows Defender; daily backups to NAS |
| **Recommended Mitigations** | Deploy EDR on all endpoints; test backup restoration monthly; implement network segmentation to limit blast radius; enable controlled folder access |
| **Residual Risk after Mitigation** | Medium (4) |
| **Owner** | IT Manager |
| **Target Date** | Q2 2025 |

---

### Risk 3 — Insider Threat (Data Exfiltration)

| Field | Detail |
|-------|--------|
| **Description** | A current or departing employee intentionally or accidentally exfiltrates client data |
| **Threat Actor** | Malicious or negligent insider |
| **Vulnerability** | No DLP (Data Loss Prevention) controls; USB ports unrestricted; excessive share permissions |
| **Likelihood** | 2 — Medium |
| **Impact** | 3 — High (confidential healthcare/legal client data; regulatory penalties) |
| **Risk Score** | **6 — High** |
| **Current Controls** | HR offboarding checklist (manual); shared drive access reviewed annually |
| **Recommended Mitigations** | Implement DLP solution; disable USB data transfer via Group Policy; conduct quarterly access reviews; enforce least privilege |
| **Residual Risk after Mitigation** | Low–Medium (2–3) |
| **Owner** | HR + IT Security (joint) |
| **Target Date** | Q3 2025 |

---

### Risk 4 — Unpatched Systems / Vulnerability Exploitation

| Field | Detail |
|-------|--------|
| **Description** | Attackers exploit known vulnerabilities in unpatched operating systems or applications |
| **Threat Actor** | External attacker; automated scanning bots |
| **Vulnerability** | Patch management is manual and inconsistent; some servers are 60+ days behind |
| **Likelihood** | 3 — High |
| **Impact** | 2 — Medium |
| **Risk Score** | **6 — High** |
| **Current Controls** | Manual patching process; Windows Update enabled on most workstations |
| **Recommended Mitigations** | Deploy centralised patch management (WSUS for Windows, Landscape for Ubuntu); establish SLA: critical patches within 48 hours, high within 7 days; conduct monthly vulnerability scans |
| **Residual Risk after Mitigation** | Low (2) |
| **Owner** | IT Manager |
| **Target Date** | Q2 2025 |

---

### Risk 5 — Weak or Reused Passwords

| Field | Detail |
|-------|--------|
| **Description** | Employees use weak or reused passwords, making credential-based attacks effective |
| **Threat Actor** | External attacker; credential stuffing bots |
| **Vulnerability** | No MFA enforced on all systems; password policy minimum is only 8 characters |
| **Likelihood** | 3 — High |
| **Impact** | 2 — Medium |
| **Risk Score** | **6 — High** |
| **Current Controls** | Password policy in Active Directory; some MFA for VPN |
| **Recommended Mitigations** | Enforce MFA on all systems (Microsoft 365, VPN, internal apps); increase minimum password length to 14 characters; deploy password manager for staff |
| **Residual Risk after Mitigation** | Low (2) |
| **Owner** | IT Security |
| **Target Date** | Q1 2025 |

---

### Risk 6 — Physical Security — Unauthorised Access

| Field | Detail |
|-------|--------|
| **Description** | An unauthorised person enters the server room or office and accesses systems or hardware |
| **Threat Actor** | Physical intruder; tailgater |
| **Vulnerability** | Server room uses a standard key lock; no CCTV coverage of server room entrance |
| **Likelihood** | 1 — Low |
| **Impact** | 3 — High |
| **Risk Score** | **3 — Medium** |
| **Current Controls** | Locked server room; visitor sign-in log at reception |
| **Recommended Mitigations** | Replace key lock with badge reader + PIN; install CCTV at server room entrance; implement a clean desk policy |
| **Residual Risk after Mitigation** | Low (1–2) |
| **Owner** | Facilities + IT |
| **Target Date** | Q3 2025 |

---

### Risk 7 — Cloud Misconfiguration (AWS S3)

| Field | Detail |
|-------|--------|
| **Description** | An S3 bucket containing client documents is accidentally made public due to misconfiguration |
| **Threat Actor** | External opportunist; automated cloud scanner |
| **Vulnerability** | No automated cloud security posture scanning; manual bucket creation process |
| **Likelihood** | 2 — Medium |
| **Impact** | 3 — High (client data exposure; HIPAA breach) |
| **Risk Score** | **6 — High** |
| **Current Controls** | AWS account has MFA on root; basic IAM roles in place |
| **Recommended Mitigations** | Enable AWS Config with S3 public access rules; enable S3 Block Public Access at account level; deploy AWS Security Hub; conduct quarterly IAM access reviews |
| **Residual Risk after Mitigation** | Low (2) |
| **Owner** | Cloud/DevOps Lead |
| **Target Date** | Q2 2025 |

---

### Risk 8 — Third-Party Vendor Risk

| Field | Detail |
|-------|--------|
| **Description** | A third-party vendor with access to Nexarion systems is compromised, providing attackers a supply-chain entry point |
| **Threat Actor** | Nation-state or criminal group targeting the supply chain |
| **Vulnerability** | No formal vendor risk assessment process; some vendors have broad VPN access |
| **Likelihood** | 1 — Low |
| **Impact** | 3 — High |
| **Risk Score** | **3 — Medium** |
| **Current Controls** | NDAs signed with vendors; basic contractual security requirements |
| **Recommended Mitigations** | Implement vendor risk assessment questionnaires; review and restrict vendor VPN access to minimum required; monitor vendor sessions |
| **Residual Risk after Mitigation** | Low (1–2) |
| **Owner** | Procurement + IT Security |
| **Target Date** | Q3 2025 |

---

## Risk Summary Dashboard

| Risk | Score | Level | Priority |
|------|-------|-------|----------|
| Phishing / BEC | 9 | 🔴 High | 1 |
| Ransomware | 6 | 🔴 High | 2 |
| Insider Threat | 6 | 🔴 High | 3 |
| Unpatched Systems | 6 | 🔴 High | 4 |
| Weak Passwords | 6 | 🔴 High | 5 |
| Cloud Misconfiguration | 6 | 🔴 High | 6 |
| Physical Security | 3 | 🟡 Medium | 7 |
| Third-Party Vendor | 3 | 🟡 Medium | 8 |

---

## Security+ Concepts Demonstrated

- **Risk = Likelihood × Impact** — qualitative risk scoring
- **Risk register** — formal documentation of identified risks
- **Risk response strategies** — mitigate (reduce likelihood/impact), accept, transfer (insurance), avoid
- **Residual risk** — risk remaining after controls are applied
- **Threat actors** — external attackers, insiders, nation-states
- **Vulnerabilities** — technical gaps (no EDR), process gaps (manual patching), human gaps (phishing susceptibility)
- **Compliance considerations** — HIPAA implications for healthcare client data

---

## Reflection

Completing this risk assessment changed how I think about security. Before this project, I thought about security in terms of technical tools — firewalls, antivirus, passwords. This project taught me to think systematically about what could go wrong, how likely it is, and how bad it would be. I found the scoring methodology really useful because it forces you to prioritise — you can't fix everything at once, so you have to focus on the highest-risk items first. I was surprised by how highly phishing scored; it's not the most technically sophisticated attack, but because it's so likely and the impact is so high, it becomes the top priority.
