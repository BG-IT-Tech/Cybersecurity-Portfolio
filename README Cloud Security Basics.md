# Cloud Security Basics — Nexarion Solutions

**Project Type:** Cloud Security  
**Scenario:** Reviewing and improving Nexarion Solutions' AWS environment security posture  
**Security+ Domains:** Architecture and Design | Implementation | Governance, Risk, and Compliance

---

## Scenario Background

Nexarion Solutions uses AWS as its cloud platform for hosting the client-facing web application (`NEXARION-WEB01`), storing client documents in S3, and running a small database in RDS. A recent external audit flagged several cloud security concerns. This project documents the current state, the issues found, and the improvements made.

**AWS Services in Use:**
- EC2 (web server)
- S3 (client document storage)
- RDS (PostgreSQL database)
- IAM (identity and access management)
- CloudTrail (audit logging)
- CloudWatch (monitoring and alerting)
- VPC (virtual private cloud / networking)

---

## Part 1 — Shared Responsibility Model

Before securing anything in AWS, it's important to understand what Nexarion is responsible for versus what AWS manages.

| Responsibility | AWS Manages | Nexarion Manages |
|---------------|-------------|-----------------|
| Physical data centre security | ✅ | |
| Hypervisor and hardware | ✅ | |
| Managed service patching (e.g., RDS) | ✅ | |
| EC2 operating system patching | | ✅ |
| Application security (web app) | | ✅ |
| IAM user and role configuration | | ✅ |
| S3 bucket permissions | | ✅ |
| Data encryption | Both | ✅ (Nexarion chooses to enable) |
| Network configuration (Security Groups, VPC) | | ✅ |
| CloudTrail / logging enablement | | ✅ |

**Key takeaway:** AWS secures the cloud infrastructure. Nexarion is responsible for securing everything it builds and configures inside the cloud.

---

## Part 2 — IAM Audit and Improvements

### Findings from Audit

| Finding | Severity | Detail |
|---------|----------|--------|
| Root account used for daily operations | 🔴 Critical | Root should never be used for day-to-day tasks |
| Root account has no MFA | 🔴 Critical | Root account access without MFA is extremely dangerous |
| 3 IAM users have `AdministratorAccess` policy attached | 🔴 High | Excessive privilege — only 1 admin needed |
| IAM user `deploy-bot` has console access enabled | 🟡 Medium | Service accounts should not have console access |
| IAM user `former-employee-KT` still active | 🔴 High | Departed employee account not deprovisioned |
| No IAM access key rotation in 12+ months | 🟡 Medium | Access keys should be rotated every 90 days |

### Remediation Actions

**1. Secure the root account**

```
Actions taken:
- Enabled MFA on root account (hardware token stored in secure safe)
- Removed all access keys from root account
- Created a break-glass procedure document for root access
- Root account is now only used if all admin IAM accounts are locked out
```

**2. Apply least privilege to IAM users**

Removed `AdministratorAccess` from 2 of the 3 users. Applied job-specific policies:

| User | Old Policy | New Policy |
|------|-----------|------------|
| `s.patel` (developer) | `AdministratorAccess` | `AmazonEC2FullAccess`, `AmazonS3ReadOnlyAccess` |
| `m.rodriguez` (DBA) | `AdministratorAccess` | `AmazonRDSFullAccess` |
| `it-admin` | `AdministratorAccess` | `AdministratorAccess` (retained — sole admin) |

**3. Fix the deploy-bot account**

```bash
# Disable console access for deploy-bot (service accounts should use access keys only)
aws iam delete-login-profile --user-name deploy-bot

# Rotate access keys
aws iam create-access-key --user-name deploy-bot
aws iam delete-access-key --user-name deploy-bot --access-key-id OLDKEYID
```

**4. Disable the former employee account**

```bash
# Disable login
aws iam update-login-profile --user-name former-employee-KT --password-reset-required
aws iam update-user --user-name former-employee-KT

# Remove from all groups and detach policies
aws iam remove-user-from-group --user-name former-employee-KT --group-name Developers

# Deactivate access keys
aws iam update-access-key --user-name former-employee-KT --access-key-id KEYID --status Inactive
```

---

## Part 3 — S3 Bucket Security

### Audit Findings

Nexarion has 3 S3 buckets:

| Bucket | Purpose | Issue Found |
|--------|---------|-------------|
| `nexarion-client-docs-prod` | Client document storage | ⚠️ Block Public Access not enabled |
| `nexarion-web-assets` | Static web assets (images, CSS) | ✅ Intentionally public — correct |
| `nexarion-logs-archive` | Log archive | ⚠️ No server-side encryption enabled |

### Remediation

**Block Public Access on the client documents bucket:**

```bash
aws s3api put-public-access-block \
  --bucket nexarion-client-docs-prod \
  --public-access-block-configuration \
  "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"
```

**Enable server-side encryption on the logs bucket:**

```bash
aws s3api put-bucket-encryption \
  --bucket nexarion-logs-archive \
  --server-side-encryption-configuration '{
    "Rules": [{
      "ApplyServerSideEncryptionByDefault": {
        "SSEAlgorithm": "aws:kms"
      }
    }]
  }'
```

**Enable S3 Versioning on the client docs bucket (protection against accidental deletion):**

```bash
aws s3api put-bucket-versioning \
  --bucket nexarion-client-docs-prod \
  --versioning-configuration Status=Enabled
```

**Enable S3 Object Lock on the logs bucket (prevent log tampering):**

```bash
# (Enabled at bucket creation — requires re-creating the bucket)
# Object Lock prevents log files from being modified or deleted for a set retention period
```

---

## Part 4 — CloudTrail (Audit Logging)

CloudTrail was found to be enabled for the `us-east-1` region only. It was not enabled globally.

### What CloudTrail Logs

CloudTrail records API calls made in your AWS account — essentially an audit log of everything that happens:

- Who made the call (IAM user, role, or root)
- What action was taken (e.g., `DeleteBucket`, `CreateUser`, `StopInstances`)
- When it happened (timestamp)
- Where from (IP address)

### Improvements Made

```bash
# Enable CloudTrail as a multi-region trail (covers all regions, not just us-east-1)
aws cloudtrail create-trail \
  --name nexarion-global-trail \
  --s3-bucket-name nexarion-logs-archive \
  --is-multi-region-trail \
  --enable-log-file-validation \
  --include-global-service-events

aws cloudtrail start-logging --name nexarion-global-trail
```

**Log file validation** ensures CloudTrail logs cannot be tampered with after the fact — each log file has a hash digest that can be verified.

---

## Part 5 — CloudWatch Alarms

The following CloudWatch alarms were created to alert the security team to suspicious activity:

| Alarm | Trigger | Alert Destination |
|-------|---------|------------------|
| Root account login | Any console login as root | security@nexarion.com + SMS |
| IAM policy change | Any policy attached/detached | security@nexarion.com |
| Security Group change | Any inbound rule added | security@nexarion.com |
| Failed console logins | 5 failures in 5 minutes | security@nexarion.com |
| S3 Public Access enabled | Any S3 public access block disabled | security@nexarion.com + PagerDuty |
| CloudTrail disabled | CloudTrail logging stopped | security@nexarion.com (critical) |

---

## Part 6 — VPC and Security Groups

### Findings

The EC2 instance running the web server had its security group misconfigured:

| Port | Protocol | Source | Finding |
|------|----------|--------|---------|
| 22 (SSH) | TCP | 0.0.0.0/0 | 🔴 SSH open to the entire internet |
| 443 (HTTPS) | TCP | 0.0.0.0/0 | ✅ Correct — web server should be public |
| 5432 (PostgreSQL) | TCP | 0.0.0.0/0 | 🔴 Database port exposed to internet |
| 3306 (MySQL) | TCP | 0.0.0.0/0 | 🔴 Old unused rule — MySQL not even in use |

### Fixed Security Group Rules

```
Inbound Rules (NEXARION-WEB01):
┌────────┬──────────┬─────────────────────┬──────────────────────────────┐
│ Port   │ Protocol │ Source              │ Purpose                      │
├────────┼──────────┼─────────────────────┼──────────────────────────────┤
│ 443    │ TCP      │ 0.0.0.0/0           │ HTTPS from internet          │
│ 22     │ TCP      │ 10.10.50.0/24 (VPN) │ SSH from management VLAN only│
└────────┴──────────┴─────────────────────┴──────────────────────────────┘

Inbound Rules (NEXARION-RDS01 — PostgreSQL):
┌────────┬──────────┬─────────────────────┬──────────────────────────────┐
│ Port   │ Protocol │ Source              │ Purpose                      │
├────────┼──────────┼─────────────────────┼──────────────────────────────┤
│ 5432   │ TCP      │ sg-0abc123 (WEB01)  │ DB access from web server SG │
└────────┴──────────┴─────────────────────┴──────────────────────────────┘
```

The RDS database is in a **private subnet** with no internet gateway route. It can only be reached from within the VPC.

---

## Part 7 — MFA Summary

| Account | MFA Status Before | MFA Status After |
|---------|------------------|-----------------|
| AWS Root | ❌ Not enabled | ✅ Hardware token |
| `it-admin` IAM | ❌ Not enabled | ✅ Virtual MFA (Authenticator app) |
| `s.patel` IAM | ❌ Not enabled | ✅ Virtual MFA |
| `m.rodriguez` IAM | ❌ Not enabled | ✅ Virtual MFA |
| `deploy-bot` | N/A (no console) | N/A |

**IAM Policy to enforce MFA:** An IAM policy was attached to all human users that denies all actions if MFA is not present, except for the actions needed to set up MFA.

---

## Security+ Concepts Demonstrated

- **Shared responsibility model** — knowing what Nexarion vs AWS is responsible for
- **Least privilege** — trimming IAM permissions to minimum required
- **MFA** — securing all console accounts
- **Auditing and logging** — CloudTrail for AWS API activity
- **Encryption at rest** — S3 server-side encryption with KMS
- **Network security** — Security Groups as virtual firewalls, private subnets for databases
- **Account lifecycle management** — deprovisioning the former employee account
- **Security alerts** — CloudWatch alarms for suspicious events

---

## Reflection

This project was eye-opening because I realised how easy it is to misconfigure cloud services with serious consequences. An S3 bucket being accidentally made public has caused real data breaches at major companies — and it can happen with a single misconfigured setting. I learned that cloud security is not just about the technology; it's about disciplined processes (like deprovisioning accounts when people leave) and visibility (CloudTrail telling you who did what). The shared responsibility model was a new concept for me — before this, I assumed cloud providers handled all the security. Understanding that IAM, S3 permissions, and security groups are entirely my responsibility was a significant shift in thinking.
