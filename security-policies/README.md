# Security Policies — Nexarion Solutions

**Project Type:** Governance / Policy Writing  
**Scenario:** Drafting three foundational security policies for Nexarion Solutions  
**Security+ Domains:** Governance, Risk, and Compliance

---

## Overview

Good security is not just about technology — it's about people knowing what is expected of them. Policies set the rules. Standards define how to meet them. Procedures explain the steps to follow. This project documents three policies written for Nexarion Solutions:

1. Acceptable Use Policy (AUP)
2. Password Policy
3. Incident Reporting Policy

---

---

# Policy 1 — Acceptable Use Policy (AUP)

**Policy Number:** NX-POL-001  
**Version:** 1.2  
**Effective Date:** January 1, 2025  
**Review Date:** January 1, 2026  
**Owner:** IT Security Manager  
**Approved By:** Chief Operating Officer

---

## 1. Purpose

This Acceptable Use Policy defines the rules governing use of Nexarion Solutions' information technology resources, including computers, networks, email, internet access, and data. The purpose is to protect the company, its clients, and its employees from legal liability, data breaches, and reputational harm.

## 2. Scope

This policy applies to all employees, contractors, consultants, temporary staff, and any other person granted access to Nexarion Solutions' IT systems, regardless of location.

## 3. Acceptable Use

Nexarion Solutions IT resources are provided for business purposes. Incidental personal use is permitted provided it does not:

- Interfere with work responsibilities
- Consume excessive bandwidth or storage
- Violate any provision of this policy

**Permitted uses include:**

- Performing job duties and communicating with clients and colleagues
- Accessing work-related websites and cloud services
- Using company-approved software and applications
- Limited personal web browsing during break times

## 4. Prohibited Use

The following activities are strictly prohibited on Nexarion IT systems:

| Category | Prohibited Activity |
|----------|-------------------|
| **Security** | Attempting to bypass security controls, installing unauthorised software, sharing login credentials |
| **Legal** | Downloading pirated software, accessing or distributing copyrighted material without authorisation |
| **Data** | Transferring client or company data to personal cloud storage (e.g., personal Dropbox, Google Drive) |
| **Content** | Accessing or distributing offensive, discriminatory, or sexually explicit content |
| **Network** | Using company network for cryptocurrency mining, running personal servers, or setting up unauthorised network devices |
| **Communication** | Impersonating another employee or using company email for personal business |

## 5. Monitoring

Nexarion Solutions reserves the right to monitor all activity on company-owned systems and networks. Employees should have no expectation of privacy when using company IT resources. Monitoring may include:

- Email content and metadata
- Web browsing history
- File access and transfer logs
- Network traffic

## 6. Consequences of Violation

Violations of this policy may result in:

- Verbal or written warning
- Suspension of IT access
- Disciplinary action up to and including termination
- Legal action where criminal activity is involved

## 7. Bring Your Own Device (BYOD)

Personal devices used to access Nexarion systems (e.g., personal phones for email) must:

- Have a device passcode or biometric lock enabled
- Have the Nexarion MDM (Mobile Device Management) profile installed
- Not be used on public Wi-Fi without a VPN

---

---

# Policy 2 — Password Policy

**Policy Number:** NX-POL-002  
**Version:** 2.0  
**Effective Date:** January 1, 2025  
**Review Date:** January 1, 2026  
**Owner:** IT Security Manager  
**Approved By:** Chief Operating Officer

---

## 1. Purpose

This policy defines the minimum standards for creating, managing, and protecting passwords used to access Nexarion Solutions' systems and data. Weak passwords are one of the most common causes of security incidents.

## 2. Scope

This policy applies to all accounts used to access Nexarion systems, including domain accounts, application accounts, service accounts, and cloud platform accounts.

## 3. Password Requirements

### Standard User Accounts

| Requirement | Standard |
|-------------|----------|
| Minimum length | 14 characters |
| Complexity | Must include uppercase, lowercase, number, and symbol |
| Maximum age | 90 days (prompted to change) |
| History | Cannot reuse last 10 passwords |
| Account lockout | 5 failed attempts; 15-minute lockout |

### Privileged / Admin Accounts

| Requirement | Standard |
|-------------|----------|
| Minimum length | 20 characters |
| Complexity | Must include uppercase, lowercase, number, and symbol |
| Maximum age | 60 days |
| MFA | Required — Microsoft Authenticator |
| Shared accounts | Prohibited — each admin must have a named account |

### Service Accounts

| Requirement | Standard |
|-------------|----------|
| Minimum length | 25 characters |
| Rotation | Every 90 days, managed by IT |
| Storage | Must be stored in the company password vault (Bitwarden Teams) |
| Interactive login | Disabled where technically possible |

## 4. Password Management

- All employees must use the company-approved password manager: **Bitwarden Teams**
- Passwords must never be:
  - Written on paper or sticky notes
  - Stored in plain text files, spreadsheets, or emails
  - Shared with colleagues, IT staff, or third parties
  - Reused across personal and work accounts

## 5. Multi-Factor Authentication (MFA)

MFA is mandatory for:

- All Microsoft 365 accounts
- VPN access
- Remote Desktop Gateway
- All cloud platform consoles (AWS, Azure)
- Any application containing client data

MFA is strongly recommended for all other accounts where available.

## 6. Password Resets

- Users may reset their own passwords via the self-service portal at `https://password.nexarion.local`
- IT Help Desk will never ask for your current password during a reset
- If you suspect your password has been compromised, reset it immediately and notify IT Security

---

---

# Policy 3 — Incident Reporting Policy

**Policy Number:** NX-POL-003  
**Version:** 1.1  
**Effective Date:** January 1, 2025  
**Review Date:** January 1, 2026  
**Owner:** IT Security Manager  
**Approved By:** Chief Operating Officer

---

## 1. Purpose

This policy defines how employees must report security incidents and suspected security events at Nexarion Solutions. Early reporting is critical to containing the impact of any security incident.

## 2. Scope

This policy applies to all employees, contractors, and third parties with access to Nexarion systems or data. All personnel are responsible for reporting — security is everyone's responsibility.

## 3. What Is a Security Incident?

A security incident is any event that threatens the confidentiality, integrity, or availability of Nexarion's information or systems.

**Examples of incidents that must be reported:**

- Receiving a suspected phishing email
- Clicking a link or opening an attachment you now suspect was malicious
- A lost or stolen device (laptop, phone, USB drive) containing company data
- Discovering that you have access to data you should not have
- Observing unusual system behaviour (unexpected pop-ups, locked files, slow performance)
- Unauthorised physical access to a secure area
- A colleague asking you to share your login credentials

**When in doubt — report it. It is always better to report and be wrong than not to report at all.**

## 4. How to Report an Incident

| Method | Contact | Use When |
|--------|---------|----------|
| **Phone** | IT Help Desk — Ext. 2100 | Urgent / active incident |
| **Email** | security@nexarion.com | Non-urgent; suspected phishing |
| **Phishing button** | Use the "Report Phishing" button in Outlook | Suspected phishing email only |
| **In person** | IT Security team — Room 204 | If systems may be compromised |

**For a suspected active breach, always call — do not use email or instant message on the potentially compromised device.**

## 5. What Happens After You Report

1. The IT Help Desk logs the incident and assigns a ticket number
2. Incidents are triaged by severity within 1 hour during business hours
3. The reporting employee will be contacted and may be asked questions — please cooperate fully
4. IT Security handles investigation and containment
5. You will be notified of the outcome and any actions required from you

## 6. Reporting Timeline

| Incident Severity | Detection-to-Report Target |
|-------------------|--------------------------|
| Critical (active breach, ransomware) | Within 15 minutes |
| High (compromised account, lost device with data) | Within 1 hour |
| Medium (phishing email, suspected malware) | Within 4 hours |
| Low (policy violation, suspicious but unconfirmed) | Within 24 hours |

## 7. Non-Retaliation

Nexarion Solutions has a strict non-retaliation policy. No employee will face disciplinary action for reporting a security incident in good faith — even if they caused it by mistake. Retaliation against someone who reports a security concern is a disciplinary offence.

## 8. Regulatory Notification

Some incidents may require notification to regulators or affected clients. The IT Security Manager, in coordination with Legal, will determine regulatory obligations. Employees should not independently contact regulators or notify clients without authorisation.

---

## Security+ Concepts Demonstrated

- **Policies, standards, and procedures** — understanding the governance framework
- **Acceptable use** — defining permitted and prohibited activities
- **Password controls** — length, complexity, MFA, vaulting
- **Least privilege** — service accounts, named admin accounts
- **Incident response — reporting** — the human side of detecting incidents
- **Non-repudiation** — named accounts (no shared admin logins)
- **Compliance** — regulatory notification obligations

---

## Reflection

Writing these policies taught me that policies need to be realistic to be effective. If a password policy demands 32-character passwords changed every 30 days, employees will write them on sticky notes — which is worse than a shorter, less frequently changed password. I also learned that policies must be written for a human audience, not just IT professionals. The incident reporting policy, for example, had to be clear enough that someone in customer support with no technical background could understand what to do if they clicked a bad link. Good security policies balance security requirements with usability.
