# AWS-Cloud-Forensics-Investigation---Operation-Overcast

---

## 📋 Executive Summary

A forensic investigation was conducted on AWS CloudTrail logs following a suspected cloud compromise. An attacker successfully compromised an IAM access key, escalated privileges to full administrator, exfiltrated sensitive data, and established persistence within the environment using advanced techniques including credential abuse, privilege escalation, data exfiltration, backdoor creation, and log evasion tactics.

---

## 🎯 Key Findings

| # | Finding | Detail |
|---|---------|--------|
| 1 | Stolen Access Key | `AKIA_DEV_PROD_9982` |
| 2 | Initial Attacker IP | `190.45.112.3` |
| 3 | Persistence IP | `45.33.10.11` — first seen at `03:35:45Z` |
| 4 | Privilege Escalation | `iam:PutUserPolicy` at `02:50:33Z` — Action:* Resource:* |
| 5 | Exfiltrated File | `ceo_personal_taxes_2025.pdf` from `top-secret-financials-2026` |
| 6 | Stolen Credential | `db_pass: P@ssw0rd_Cloud_2026!` from Secrets Manager |
| 7 | Backdoor User | `support_service_backup` with AdministratorAccess |
| 8 | Final Evasion | Original key deactivated at `04:50:33Z` to lock out defenders |

---

## ⚔️ Attack Chain Timeline

### Phase 1 — Reconnaissance
| Timestamp | Event | Meaning |
|-----------|-------|---------|
| 01:15:33Z | `sts:GetCallerIdentity` | Verifies stolen key is valid |
| 01:25:44Z | `iam:GetAccountSummary` | Maps account structure |
| 01:40:00Z | `s3:ListBuckets` | Enumerates all storage buckets |
| 02:05:45Z | `iam:ListUserPolicies` | Profiles dev_user_musa |
| 02:40:00Z | `cloudtrail:DescribeTrails` | Checks if anyone is watching |

### Phase 2 — Escalation & Exfiltration
| Timestamp | Event | Meaning |
|-----------|-------|---------|
| 02:50:33Z | `iam:PutUserPolicy` — FullAdmin | **Full admin access granted** |
| 03:02:11Z | `secretsmanager:GetSecretValue` | Database password stolen |
| 03:10:44Z | `s3:GetObject` — ceo_personal_taxes_2025.pdf | **Data exfiltrated** |

### Phase 3 — Persistence
| Timestamp | Event | Meaning |
|-----------|-------|---------|
| 03:15:22Z | `iam:CreateUser` — support_service_backup | Backdoor user created |
| 03:25:33Z | `iam:AttachUserPolicy` — AdministratorAccess | Backdoor given full access |
| 03:35:45Z | `iam:CreateAccessKey` | Backdoor key generated |
| 03:40:00Z | `cloudtrail:StopLogging` — **AccessDenied** | Logging evasion failed |

### Phase 4 — Cover Tracks
| Timestamp | Event | Meaning |
|-----------|-------|---------|
| 04:05:44Z | `sts:GetCallerIdentity` | Backdoor key verified |
| 04:50:33Z | `iam:UpdateAccessKey` → Inactive | Original stolen key disabled |

---

## 🔍 Base64 Obfuscation Analysis

| | String 1 | String 2 |
|--|----------|----------|
| **Encoded** | `c2V0dGluZzogYmFja3VwX2VuYWJsZWQ9dHJ1ZQ==` | `ZGJfcGFzczogUEBzc3cwcmRfQ2xvdWRfMjAyNiE=` |
| **Decoded** | `setting: backup_enabled=true` | `db_pass: P@ssw0rd_Cloud_2026!` |
| **Verdict** | ✅ Legitimate — routine DevOps config | 🚨 Malicious — stolen database password |

---

## 🛡️ Indicators of Compromise (IOCs)

| Type | Value |
|------|-------|
| Attacker IP 1 | `190.45.112.3` |
| Attacker IP 2 | `45.33.10.11` |
| Stolen Key | `AKIA_DEV_PROD_9982` |
| Backdoor Key | `AKIA_FAKE_BACKDOOR_KEY_7721` |
| Backdoor User | `support_service_backup` |
| Exfiltrated File | `ceo_personal_taxes_2025.pdf` |
| Stolen Secret | `db_pass: P@ssw0rd_Cloud_2026!` |

---

## 🧰 Tools & Skills Applied
- AWS CloudTrail log analysis
- IAM privilege escalation detection
- Base64 decoding (CyberChef)
- Attack timeline reconstruction
- DFIR — Digital Forensics & Incident Response

---

> 📄 Full investigation report available in the repository files above.
