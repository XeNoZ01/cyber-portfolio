+++
title = "ISO 27005 risk analysis of an accounting firm"
date = 2026-11-24
draft = false
description = "End-to-end walkthrough on a fictional 14-person organisation: context, identification, evaluation, treatment and monitoring of risks."
case_study = "fictive"
translationKey = "iso27005-cabinet"
+++

## Scope of the study

**Fictional organisation: Aubépine firm.** A 14-person accounting firm (2 partners, 9 accountants, 3 assistants) that keeps the books and payroll of about 180 clients, mostly very small businesses.

**Goal:** show a risk analysis from start to finish, following the ISO/IEC 27005 approach (information security risk management) and linked to the ISO/IEC 27002:2022 controls. It is the first building block of an ISO 27001 ISMS, but the study stops at the analysis and the treatment plan.

**What is invented:** the firm, its headcount, its tools and the scores given. **What is real:** the method, the criteria and the reference controls used.

---

## 1. Establish the context

### Scope

The whole firm: a single site, occasional remote work, workstations, the file server, e-mail, the online accounting and payroll software, and backups.

### What is at stake

- **Confidentiality:** clients' financial and payroll data, including personal data (GDPR); professional secrecy.
- **Availability:** tax and social deadlines do not move; an outage during year-end closing is costly.
- **Integrity:** a falsified accounting entry or payroll transfer has direct consequences for clients.

### Study criteria

Four-level scales, deliberately simple for a small firm.

| Level | Severity (impact) | Likelihood (over 12 months) |
|---|---|---|
| 1 | Negligible: nuisance with no client impact | Unlikely: less than once in 5 years |
| 2 | Limited: one or two days' delay | Possible: about once in 2 to 5 years |
| 3 | Significant: clients affected, notification duty | Likely: about once a year |
| 4 | Critical: lost clients, sanction, long shutdown | Almost certain: several times a year |

**Risk level = severity × likelihood.**

| Score | Level | Decision |
|---|---|---|
| 1 to 3 | Low | Accepted, monitored |
| 4 to 6 | Medium | Treated within 12 months |
| 8 to 9 | High | Treated as a priority (6 months) |
| 12 to 16 | Critical | Treated immediately |

---

## 2. Identify the risks

### Main assets

| Asset | Value to the firm |
|---|---|
| Client files (accounting, payroll, ID documents) | Core business, personal data |
| Online accounting and payroll software | Daily production, supplier dependency |
| File server | Working documents and archives |
| 14 workstations, 6 of them laptops | Access to files, mobility |
| E-mail | Client exchanges, common attack vector |
| Backups | Last resort in a disaster |

### Risk scenarios

The chosen approach is **scenario-based**: a threat exploits a vulnerability, hits an asset and produces an impact.

| Ref. | Scenario | Vulnerability (study assumption) |
|---|---|---|
| R1 | Phishing: an employee's mailbox account is compromised | No multi-factor authentication, little awareness training |
| R2 | Ransomware on the file server and workstations | Basic antivirus, irregular updates |
| R3 | Loss or theft of a laptop | Unencrypted disks |
| R4 | Payroll data sent to the wrong recipient | No checking rule, attachments in clear |
| R5 | Online software unavailable during closing period | No fallback planned |
| R6 | Access kept after an employee leaves | No access rights review |
| R7 | Server failure with no usable backup | Backup never tested by restoring |
| R8 | Intrusion through an exposed remote access | Remote desktop open to the Internet |

---

## 3. Analyse and evaluate

| Ref. | Severity | Likelihood | Score | Level |
|---|---|---|---|---|
| R1 Mailbox compromise | 4 | 3 | 12 | **Critical** |
| R2 Ransomware | 4 | 3 | 12 | **Critical** |
| R3 Lost laptop | 3 | 3 | 9 | **High** |
| R4 Sending error | 3 | 3 | 9 | **High** |
| R7 Unusable backup | 4 | 2 | 8 | **High** |
| R8 Exposed remote access | 4 | 2 | 8 | **High** |
| R5 Software unavailable | 3 | 2 | 6 | Medium |
| R6 Access not removed | 3 | 2 | 6 | Medium |

**Reading:** two critical risks, both tied to people and e-mail. They often lead to one another (a successful phishing opens the door to ransomware), hence a shared response on authentication and awareness.

---

## 4. Treat the risks

Four options are available: **reduce**, **avoid**, **transfer**, **accept**. Controls are linked to ISO/IEC 27002:2022.

| Ref. | Option | Selected measures | ISO 27002:2022 ref. | Deadline |
|---|---|---|---|---|
| R1 | Reduce | MFA on e-mail and business software; awareness and phishing exercises | 8.5, 6.3 | 1 month |
| R2 | Reduce + transfer | Endpoint protection (EDR or managed antivirus), automatic updates, offline backup; look into cyber insurance | 8.7, 8.8, 8.13 | 3 months |
| R3 | Reduce | Laptop disk encryption, automatic screen lock | 8.1, 8.24 | 2 months |
| R4 | Reduce | Double-check rule for recipients; secure link sharing instead of attachments | 5.14, 6.3 | 2 months |
| R7 | Reduce | 3-2-1 backup (3 copies, 2 media, 1 off-site) and quarterly restore test | 8.13, 5.30 | 3 months |
| R8 | Avoid | Close the exposed remote desktop; access through VPN with MFA | 8.20, 8.5 | 1 month |
| R5 | Accept, with fallback | Paper/spreadsheet procedure for critical deadlines; check the availability clause with the supplier | 5.23, 5.30 | 6 months |
| R6 | Reduce | Joiner/leaver procedure removing access; six-monthly rights review | 5.18, 6.5 | 4 months |

### Expected residual risk

| Ref. | Before | After (estimate) |
|---|---|---|
| R1 | 12 | 4 (severity 4 × likelihood 1) |
| R2 | 12 | 4 |
| R3 | 9 | 3 (theft is still possible, impact falls to 1) |
| R4 | 9 | 4 |
| R7 | 8 | 2 (severity lowered to 2 by a tested restore, likelihood 1) |
| R8 | 8 | 0 (exposure removed) |
| R5 | 6 | 6 (accepted) |
| R6 | 6 | 3 |

**R5 (6)** stays accepted: management approves it explicitly, because removing it would cost more than the expected impact. **R1, R2 and R4 (4)** remain at the treatment threshold and are to be reviewed at the deadline.

---

## 5. Communication and monitoring

- **Acceptance:** the two partners approve the plan and the residual risk R5 in writing.
- **Communication:** a one-page summary for staff; a 30-minute phishing awareness session.
- **Indicators:** MFA activation rate, click rate in phishing exercises, date and result of the last restore test, number of up-to-date workstations.
- **Review:** every 12 months, and after any major change (new tool, incident, new supplier).

---

## What this study shows, and its limits

**Skills used:** scoping, evaluation criteria, risk scenarios, prioritisation, a treatment plan linked to ISO 27002, monitoring.

**Stated limits:**
- The vulnerabilities are study assumptions, not the result of a real audit.
- The scores demonstrate the method; in a real engagement they are discussed with management and the business.
- The study does not cost the measures, which would be needed to arbitrate between them.
