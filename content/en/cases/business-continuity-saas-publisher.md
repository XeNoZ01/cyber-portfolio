+++
title = "Business continuity and disaster recovery for a SaaS publisher"
date = 2026-11-24
draft = false
description = "Fictional case study: business impact analysis (BIA), disaster scenarios, recovery strategies, test plan and indicators for a fictional 40-person SaaS publisher."
case_study = "fictive"
translationKey = "continuite-saas"
+++

## Scope of the study

**Fictional organisation: Brumaille Logiciels.** Publisher of an online booking application (SaaS) for local shops, about 40 people (14 in development, 6 in operations and security, 8 in support and customer success, 12 in sales, finance and management). The application is hosted with a cloud provider (not named), in a single region, and clients pay a monthly subscription.

**Goal:** show how to build a business continuity and disaster recovery plan end to end: business impact analysis (BIA), scenarios, strategies, recovery plan, tests and indicators. The approach draws on the principles of ISO 22301 (business continuity management) and, on the IT side, ISO/IEC 27031 (ICT readiness for business continuity). Measures are linked to ISO/IEC 27002:2022.

**What is invented:** the company, its headcount, its architecture, all figures (durations, volumes, costs) and test results. **What is real:** the method, the concepts (MTPD, RTO, RPO) and the reference controls. This study is built purely as a demonstration; it is not an engagement carried out for an existing organisation.

**Convention:** all figures below are **study assumptions**, to be validated with management and the business in a real setting.

---

## 1. Key concepts

| Term | Plain definition |
|---|---|
| **MTPD** (maximum tolerable period of disruption) | Beyond it, the impact becomes unacceptable for the organisation (lost clients, sanctions, survival at stake) |
| **RTO** (recovery time objective) | Target time to restore the process; it must be **shorter** than the MTPD |
| **RPO** (recovery point objective) | Maximum amount of data that can be lost, expressed as time (for example 15 minutes of data) |

RTO and RPO are **objectives**; they only count if tests demonstrate them (section 6).

---

## 2. Business impact analysis (BIA)

### Processes and criticality

| Ref. | Process | Why it matters | Criticality |
|---|---|---|---|
| P1 | Booking application service (production) | It is the product sold; shops take their appointments through it | Critical |
| P2 | Client authentication and API | Without it, no access at all, including for integrations | Critical |
| P3 | Billing and subscription collection | Source of cash; depends on a payment provider | High |
| P4 | Customer support | Keeps clients informed during an incident | High |
| P5 | Development and deployment | Fixes and changes; needed to repair, not to serve | Medium |
| P6 | Payroll, accounting, HR | Legal obligations with fixed deadlines | Medium |
| P7 | Internal e-mail and collaboration tools | Coordinates crisis management | High |

### Objectives per process (assumptions)

| Ref. | MTPD | Target RTO | Target RPO | Main impact if exceeded (assumption) |
|---|---|---|---|---|
| P1 | 24 h | 4 h | 15 min | Cancellations, contractual availability penalties, reputation |
| P2 | 24 h | 2 h | 15 min | Same as P1; accounts unreachable |
| P3 | 5 days | 48 h | 24 h | Delayed collection, reminders to redo by hand |
| P4 | 8 h | 2 h | 1 h | Clients uninformed, alternative channels overloaded |
| P5 | 5 days | 48 h | 4 h | Fixes delayed |
| P6 | 10 days | 5 days | 24 h | Social and tax deadlines |
| P7 | 4 h | 1 h | 1 h | Poorly coordinated crisis |

**Reading:** P1 and P2 carry most of the stakes. P7 has the shortest MTPD: if the team cannot talk to each other, no other plan runs. Hence the decision to provide a crisis channel **independent** of the usual infrastructure (section 4).

### Dependencies

| Process | Depends on |
|---|---|
| P1, P2 | Cloud provider (compute, database, storage), DNS, certificates, deployment tool |
| P3 | Payment provider, P1 (for subscription status) |
| P4 | Online support tool, e-mail |
| P5 | Hosted code repository, automated deployment pipeline |

The main dependency is a **single point of failure**: one region with one provider.

### Inventory of assets and dependencies

Each line has a named owner (a role) who keeps the inventory up to date. Criticality is that of the asset **during a disaster**, not in normal operation.

| Asset | Type | Owner | Criticality | Linked processes (BIA) |
|---|---|---|---|---|
| Booking web application and API | Application (containers) | Head of development | Critical | P1, P2, P3, P4 |
| Client authentication service | Application | Head of development | Critical | P2, P1 |
| Main database (relational, managed) | Data | Head of operations | Critical | P1, P2, P3 |
| Object storage (exports, attachments) | Data | Head of operations | High | P1, P3 |
| Network, load balancer, web application firewall | Cloud infrastructure | Head of operations | Critical | P1, P2 |
| DNS (public zone) and TLS certificates | Infrastructure | Head of operations | Critical | P1, P2, P4 |
| Staff identity and single sign-on (SSO), third-party service | Identity | Security lead | High | P4, P5, P6, P7 |
| Code repository, CI/CD pipeline, image registry | Delivery chain | Head of development | Medium in operation, **high in recovery** | P5; rebuilding P1 and P2 |
| Monitoring and alerting | Operations | Head of operations | High | P1, P2; plan activation |
| Secrets vault and encryption keys | Security | Security lead | Critical | P1, P2; recovery |
| Backups (dedicated account, off-provider copy) | Data | Head of operations | Critical | P1, P2, P3 |
| Payment provider | Supplier | Head of finance | High | P3 |
| Online support tool | Third-party service | Head of support | High | P4 |
| E-mail, collaboration and crisis channel | Third-party service and dedicated tool | Continuity lead | High | P7 |
| Key people: 2 operators, 1 database administrator, 1 security lead, technical management | People | Management | Critical | P1, P2, P5 |

**Reading:**
- DNS, identity, monitoring and the secrets vault are **cross-cutting dependencies** that are often forgotten: if they fail, they block the recovery of everything else.
- "Recovery" assets (CI/CD, vault, runbook) weigh little in normal times but are essential in a disaster; their criticality is therefore reassessed "in recovery".
- Four people concentrate the recovery skills: this is the risk handled by scenario S4.

---

## 3. Disaster scenarios

| Ref. | Scenario | Processes hit | Severity (assumption) |
|---|---|---|---|
| S1 | Major outage of the cloud provider in the hosting region (several hours to several days) | P1, P2, P3, P5 | Critical |
| S2 | Data loss or corruption (operating error, deletion, ransomware) | P1, P2, P3 | Critical |
| S3 | Main site unavailable: offices unreachable (fire, water damage, prolonged power cut) | P4, P6, P7 | Medium |
| S4 | Absence of key people (illness, departure, several operators unavailable at once) | P1, P2, P5 | High |

The scenarios are **deliberately cause-independent**: we plan for the loss of a resource (the provider, the data, the premises, the people), not for every possible incident. Scenario S2 overlaps with cybersecurity (ISO/IEC 27002:2022, 5.29: information security during disruption), but recovery is the same whatever the cause.

---

## 4. Recovery strategies

### S1: cloud provider outage

| Option | Principle | Cost (assumption) | Achievable RTO | Decision |
|---|---|---|---|---|
| Wait for restoration | No investment | 0 | Unknown, up to the provider | Rejected: RTO not under control |
| Backups in a second region, rebuild on demand | Infrastructure described as code, data copied continuously | About EUR 600/month | 6 to 10 h | Not enough for P1 (RTO 4 h) |
| **"Warm" standby environment in a second region** | Data replication, reduced resources, scale-up at failover | About EUR 2,500/month | 2 to 4 h | **Selected for P1 and P2** |
| Active-active across regions | Two regions running permanently | About EUR 7,000/month | Near zero | Rejected: disproportionate for 40 people (premature) |

Complement: a **degraded mode** for P1 in read-only (clients see their bookings, new bookings are suspended) if full failover takes longer than planned.

### S2: data loss

- **3-2-1 backup**: 3 copies, 2 different media, 1 off-site copy that is **immutable** (cannot be changed for 30 days), so a deletion or malicious encryption cannot reach it (ISO/IEC 27002:2022, 8.13).
- **Continuous database logging** to recover any moment of the last 7 days (point-in-time recovery to the minute); daily full backups kept 30 days.
- **Backup accounts separate** from production accounts: a compromised administrator account must not be able to erase the copies.
- **Tested restore**: a backup never restored is an assumption, not a guarantee.

### S3: main site unavailable

Tools are already online: **remote work** is the fallback. To prepare: spare laptops, a contact list kept outside the company directory, secure remote access (multi-factor authentication) and an **independent crisis channel** (third-party encrypted messaging, outside corporate e-mail) so that P7 survives an outage of the usual infrastructure.

### S4: absence of key people

- **Pairs** on each critical skill (operations, database, security) and an on-call rotation.
- **Up-to-date operations documentation**, stored outside the infrastructure it helps restore.
- **Secrets vault** with supervised emergency access ("break-glass" procedure, logged).
- Each key person in the roles matrix has a named deputy.

### Redundancy (ISO/IEC 27002:2022, 8.14)

The second region, duplicated core services (DNS, authentication) and database replication address the control on redundancy of information processing facilities. The chosen level is "warm", consistent with the BIA's RTOs.

### Target architecture (assumptions)

- **Region A (primary):** three availability zones. The application runs on at least two zones, and the main database replicates synchronously to a neighbouring zone. Losing one zone is absorbed **without failover** (outside the disaster scenarios, it is an ordinary incident).
- **Region B ("warm" standby):** asynchronous database replica (target lag under 60 seconds, alert beyond 5 minutes), copy of object storage, replicated container images, application capacity reduced to 50% but deployable on demand.
- **Cross-cutting services outside region A:** secondary DNS zone with a second provider, external monitoring, status page, crisis channel, mirror of the code repository.
- **Principle:** do not duplicate everything, but make sure that **nothing used to rebuild** depends on the region being rebuilt.

### Backup strategy

The frequency of each backup is chosen **to meet the RPO**. A daily full backup is not enough for a 15-minute RPO: continuous archiving of transaction logs is added.

| Data | Mechanism and frequency | Retention | RPO covered | Location |
|---|---|---|---|---|
| Database | Continuous archiving of transaction logs (at most every 5 min) | 7 days (point-in-time recovery) | 5 to 15 min | Region A, replica in region B |
| Database | Daily full backup | 30 days | 24 h | Dedicated backup account, region B, **immutable** for 30 days |
| Database | Weekly encrypted export | 90 days | 7 days | **Off-provider** (storage with a second supplier) |
| Object storage | Versioning and cross-region replication | 30 days of versions | 15 min | Regions A and B |
| Code and configuration (IaC) | Daily mirror | 90 days | 24 h | Mirror repository outside the provider |
| Secrets vault | Weekly encrypted export; sealed paper copies of root keys | 90 days | 7 days | Offline, two separate places |

**Encryption and key separation:**
- Backups are encrypted with keys **distinct** from production keys, held in a different account; a compromise of the production account gives neither access to the copies nor the ability to decrypt or erase them.
- The off-provider copy is encrypted **before sending**, with a key the second supplier does not hold.
- The root key is split into 3 shares, any 2 of which rebuild it (assumption); each share has a named holder and a deputy. Losing the key amounts to losing the backup: access to the key is therefore part of the tests.

### Database restore (point-in-time recovery) and its test

1. Set the **target moment**: the last known healthy state, slightly before the incident.
2. Restore into an **isolated instance**, never over production.
3. Replay the transaction logs up to the target moment.
4. Check integrity: row count per table against a reference, checksum on critical tables, foreign-key consistency, presence of the latest expected bookings.
5. Test the application read-only on the restored copy.
6. Decide on promoting the copy to production (decision by the crisis lead, on operations' advice).

**Test:** each month, restore a sample and count rows; each quarter (exercise Q2 in the calendar), a full timed restore on real volumes (assumption: 400 GB), recording the time of each phase.

### Infrastructure as code to rebuild

- Network, compute, database, DNS, monitoring and access rights are described as **versioned code**. No manual change in production; a weekly check detects drift between the code and what exists.
- The infrastructure state is stored **outside region A**.
- Modules are parameterised by region: rebuilding in region B means changing a parameter, not rewriting.
- The standby deployment can be run **from an operations workstation**, without the CI/CD pipeline (which may itself be down).
- A full rebuild from scratch is tested once a year (target duration: 2 h, assumption).

### Secrets management and emergency access (break-glass)

- Secrets injected at startup from the vault, never in code or images; vault replicated to region B.
- **Two emergency accounts**, outside SSO (which may be unavailable), protected by a hardware multi-factor key. Passwords are kept in sealed envelopes in two separate physical safes.
- Any use triggers an **immediate alert** to the crisis cell and a log entry; the secrets involved are changed and the use is reviewed within 24 h.
- Six-monthly test, measuring access time (target: 20 minutes or less).

### Monitoring and activation alerts

External probes, located outside region A, replay a **synthetic booking journey** every minute. Monitoring and its alerts are themselves hosted outside region A, with a "heartbeat": if monitoring goes silent, someone is alerted.

| Level | Condition (assumptions) | Response |
|---|---|---|
| SEV3 | Error rate above 2% over 5 min, or replication lag above 1 min | On-call, during working hours |
| SEV2 | Synthetic journey failing for 3 min from at least 2 probes; or log-archiving lag above 10 min; or daily backup failure | Immediate on-call, operational cell opened |
| SEV1 | P1 or P2 unavailable for more than 10 min, or suspected data loss | Crisis lead alerted, plan activation assessed |

### Degraded mode

| Level | Description | Entry condition |
|---|---|---|
| N0 | Normal service | n/a |
| N1: read-only | Bookings and calendars can be viewed; creation and changes suspended; information banner | Database being restored, or replication lag unknown |
| N2: minimal | Static information page; sending, **the day before**, an export of the next day's appointments to each shop (generated every night and stored outside region A) | Failover impossible, or RTO exceeded |

**Leaving degraded mode:** integrity check passed and written validation from support. N2 does not restore the service; it keeps shops from losing sight of their appointments.

---

## 5. Sequenced recovery plan (scenario S1)

Example run for a major outage of the hosting region. Durations are **study targets**.

| Step | Target time (from the decision) | Action | Owner |
|---|---|---|---|
| 0 | T0 | Detection (external monitoring, client alerts); first diagnosis | On-call operations |
| 1 | T0 + 15 min | Qualification: provider outage confirmed on its status page? Announced duration? | Head of operations |
| 2 | T0 + 30 min | **Failover decision** (criterion: no restoration announced within 1 h, or unknown duration) | Crisis lead |
| 3 | T0 + 35 min | Open the crisis channel; message to all staff | Crisis lead |
| 4 | T0 + 45 min | Message to clients (status page hosted outside the affected region) | Support / communications |
| 5 | T0 + 1 h | Switch access: promote the database replica in the standby region | Operations |
| 6 | T0 + 2 h | Scale up application services; **P2 (authentication) first**, then P1 | Operations |
| 7 | T0 + 3 h | DNS switch to the standby region; health check (test booking journey) | Operations, development |
| 8 | T0 + 3 h 30 | Business validation: support tests with two fictional client accounts | Support |
| 9 | T0 + 4 h | Announce service restored; move to heightened monitoring | Crisis lead |
| 10 | After the disaster | Planned return to the original region, outside the emergency; lessons learned within 5 days | Technical management |

**Critical point:** the failover decision (step 2) is the hardest. Waiting for a restoration that never comes eats into the MTPD; failing over too early risks losing recent data and a second risky operation. Hence a **written decision criterion** and a named decision-maker, with a deputy.

**For scenario S2 (data loss):** identify the last healthy state, isolate the cause, restore in a separate environment, check integrity, then switch. The target RTO is 4 h for P1, longer if the extent of corruption is poorly known.

### Detailed runbook (scenario S1)

The previous table gives target milestones; this one details the actions, starting from the failover decision, with an estimated duration and the check that allows the next step. Durations: study assumptions.

| No. | Action | Estimated duration | Check (pass criterion) |
|---|---|---|---|
| 1 | Open the operational cell, record the decision time in the crisis log | 5 min | Log open, roles confirmed |
| 2 | Freeze deployments and scheduled jobs that write | 5 min | No deployment or job running |
| 3 | Check the replica in region B: replication lag and last point received | 5 min | Estimated loss known; if above 15 min, the crisis lead is informed before continuing |
| 4 | Promote the replica to primary database | 10 min | Database writable, write test passed |
| 5 | Deploy the application infrastructure in region B from code, using the last validated state | 25 min | Execution plan without errors, resources created |
| 6 | Retrieve secrets from the vault replica | 10 min | Services start with their credentials |
| 7 | Start the authentication service (P2) | 15 min | Test account login succeeds |
| 8 | Start the application and API (P1) at 50% capacity | 20 min | Health checks green on all nodes |
| 9 | Run the automated test journey (login, create and cancel a booking) | 10 min | End-to-end journey succeeds |
| 10 | Switch DNS (procedure below) | 15 min | Resolution to region B from 3 external points |
| 11 | Scale to 100% capacity and enable monitoring on region B | 20 min | Latency and error rate within thresholds |
| 12 | Support validation (fictional accounts), announce service restored | 15 min | Written validation from the head of support |

**Estimated total: 2 h 35**, about 3 h 05 from detection when the 30 minutes to the decision are included: a margin of about 55 minutes on the 4 h RTO, deliberately kept to absorb the unexpected.

### DNS failover procedure

- **Permanent preparation:** time to live (TTL) of 60 seconds on access records; secondary zone with a second provider; records described as code.
- **Switch:** 1) confirm region B is healthy; 2) apply the change through code (otherwise through the console with the emergency account); 3) check from 3 external points and by querying the authoritative name servers directly; 4) watch residual traffic to region A (some resolvers ignore the TTL).
- **Avoid running both sides:** put the region A database in read-only or isolate it as soon as possible, so that no writes are accepted on both sides.
- **Rollback:** if region B's health check fails, restore the old record, which remains possible as long as region A has not been isolated.

---

## 6. Test and exercise plan

A continuity plan that is not tested is a document, not a capability. ISO/IEC 27002:2022 (5.30) asks for ICT readiness for business continuity to be ensured, and that requires testing.

### Annual calendar (assumption)

| Period | Exercise | Type | Scope |
|---|---|---|---|
| Every month | Restore a backup sample, check integrity | Technical | P1, P2 |
| Q1 | Table-top exercise: cloud provider outage (S1) | Table-top | Crisis cell, operations, support |
| Q2 | Full database restore in an isolated environment, timed (S2) | Technical | P1, P2, P3 |
| Q3 | Table-top exercise: absence of key people during an incident (S4) | Table-top | Operations, management |
| Q4 | **Real failover** to the standby region, outside peak hours, with prior notice to clients (S1) | Technical | P1, P2 |
| Yearly | Test of the crisis channel and call tree | Communication | Everyone |
| After major change | Partial replay of the plan | Mixed | Depends on the change |

### Success criteria (assumptions)

| Exercise | Criterion |
|---|---|
| Monthly restore | 100% of samples restored, integrity checked, duration recorded |
| Full restore | Measured RTO ≤ 4 h; measured data loss (RPO) ≤ 15 min |
| Real failover | Service restored in the standby region in ≤ 4 h; no security incident linked to the failover |
| Table-top | Failover decision taken in ≤ 30 min; all roles filled; at least 3 gaps recorded |
| Call tree | 90% of people reached within 30 min |

### Follow-up

Each exercise produces a short report: gaps found, actions, owner, deadline. An exercise that fails is not a problem: that is the point. An exercise never followed by actions is.

### Lessons learned from a fictional exercise

Example report (**invented** results to illustrate the method): table-top exercise on scenario S1 (Q1) followed, the next quarter, by the database restore test (Q2).

**Table-top (S1):** 9 participants, 2 hours, injection of a regional outage announced without a duration by the provider.

| Measure | Target | Result | Verdict |
|---|---|---|---|
| Failover decision | 30 min | 52 min | Not met |
| Roles filled | All | Crisis lead's deputy had no access to the crisis channel | Partial |
| First client message | 45 min | 40 min | Met |

**Restore test (S2):** 400 GB database, restored to a target moment 20 minutes before a simulated incident.

| Measure | Target | Result | Verdict |
|---|---|---|---|
| Restore and integrity check time (nominal copy) | 4 h | 3 h 15 | Met |
| Measured data loss (RPO) | 15 min | 22 min | Not met |
| Restore from the off-provider copy | 12 h (last resort) | 4 h 50, including 40 min to find the decryption key | Met, with a gap |

| Ref. | Gap found | Cause | Action | Owner | Deadline | Status |
|---|---|---|---|---|---|---|
| E1 | Failover decision too slow | Criterion "no restoration announced" ambiguous when the provider gives no duration | Rewrite the criterion (numeric threshold) and have it approved | Continuity lead | 1 month | Closed |
| E2 | Runbook not found by on-call | Stored in a wiki hosted in the affected region | Offline copy and copy on the operations workstation, quarterly update | Head of operations | 1 month | Closed |
| E3 | Deputy without access to the crisis channel | Accounts created for titular holders only | Access created and tested for all deputies | Continuity lead | 2 weeks | Closed |
| E4 | No template message for data loss | Case not foreseen initially | Write it and have legal approve it | Head of support | 2 months | In progress |
| E5 | RPO measured at 22 min | Log archiving set to every 15 min, plus copy delay | Move to 5 min; alert on archiving lag (SEV2) | Head of operations | 1 month | Closed |
| E6 | Off-provider copy key found in 40 min | Access procedure for the sealed envelope not documented | Document it, test with a second holder | Security lead | 2 months | In progress |

Gaps E1 to E3 are organisational, E5 and E6 technical: only an exercise would have exposed them before a real disaster.

---

## 7. Roles and communication

### Roles

| Role | Mission during a crisis | Deputy |
|---|---|---|
| Crisis lead (general management) | Decides on failover, arbitrates | Technical management |
| Head of operations | Leads technical recovery | Senior operations engineer |
| Security lead (part-time CISO) | Assesses the security dimension, preserves evidence | Head of operations |
| Head of support | Informs and handles clients | A senior support agent |
| Legal and compliance lead (external, part-time) | Contractual and regulatory duties, notification | General management |

### Communication

- **Internal:** independent crisis channel, status update every 30 minutes during the incident.
- **To clients:** status page hosted **outside** the affected infrastructure; prepared template messages (first notice, progress update, back to normal); factual tone, no promise of a deadline that cannot be kept.
- **To authorities and partners:** if personal data is involved, assess the notification duty (GDPR) with legal counsel; inform the payment provider if P3 is affected.
- **Cloud provider:** higher-tier support contact, case number recorded as soon as it is opened.

### Governance and levels of responsibility

| Level | Who | Responsibilities | Rhythm |
|---|---|---|---|
| Management (sponsor) | General management | Approves the continuity policy, objectives and budget; accepts residual risks; chairs the management review | Yearly review and after any major incident |
| Continuity lead | Named person (about 20% of their time, assumption) | Maintains the BIA, the plan, the exercise calendar and action follow-up; does not decide on activation | Continuous |
| Strategic crisis cell | General management, technical management, legal, head of support | High-stakes decisions: failover, notification, external communication, exceptional spending | When activated |
| Operational cell | Head of operations, lead developer, database administrator, security lead, support | Runs the runbook, diagnoses, reports to the strategic cell every 30 min | When activated |
| Process owners | Owners of P1 to P7 | Validate their MTPD, RTO and RPO; take part in tests; validate business recovery | Yearly and exercises |
| Technical teams | Operations, development | Maintain runbooks, infrastructure code, backups, monitoring; open actions | Continuous |
| Suppliers | Cloud provider, secondary DNS, payment, support | Service commitments and escalation contacts in the contract; take part in an exercise when the contract allows | Yearly contract review |

### RACI matrix by scenario

R: responsible (does it); A: accountable (decides); C: consulted; I: informed; "-": not involved. Each row has a single A, sometimes combined with R.

| Scenario and activity | General mgmt | Continuity lead | Head of operations | Security lead | Head of support | Legal |
|---|---|---|---|---|---|---|
| S1: failover decision | A | C | R | C | I | I |
| S1: client communication | A | C | C | I | R | C |
| S2: choice of restore point | I | I | A/R | C | I | I |
| S2: personal data notification | A | I | C | C | I | R |
| S3: activating remote-work fallback | I | A | R | C | C | - |
| S3: internal communication | A | R | I | I | I | - |
| S4: calling deputies | I | A/R | C | C | I | - |
| S4: bringing in external support | A | C | R | C | I | C |

### Activation chain and criteria

| Step | Who decides | Threshold (assumptions) | Deputy |
|---|---|---|---|
| Qualifying a major incident | On-call operations | SEV1, or SEV2 unresolved after 15 min | Secondary on-call |
| Opening the operational cell | Head of operations | Major incident qualified | Database administrator |
| Activating the recovery plan (failover or restore) | Crisis lead (general management), on the head of operations' recommendation | P1 or P2 unavailable for 30 min with no cause fixable within 1 h; or provider with no restoration announced within 1 h; or confirmed data loss | Technical management |
| Last resort | Head of operations | General management and deputy unreachable for 20 min, and P1 or P2 unavailable for more than 1 h | Security lead |

**Written delegations (assumptions):** the head of operations may commit up to EUR 10,000 of recovery spending without prior approval; the head of support publishes template messages without further approval; only general management or legal notifies an authority.

---

## 8. Monitoring indicators

| Indicator | Target (assumption) |
|---|---|
| Age of the last successfully restored backup | ≤ 31 days |
| RTO measured at the last exercise / target RTO | ≤ 100% |
| RPO measured at the last exercise / target RPO | ≤ 100% |
| Share of yearly exercises carried out | 100% |
| Actions from exercises closed on time | ≥ 80% |
| Deputy coverage on key roles | 100% |
| Recovery documentation reviewed less than 6 months ago | 100% |
| Age of the last BIA review | ≤ 12 months |

**Review:** the BIA and the plan are reviewed every 12 months, and after any major change (new supplier, new product, notable growth in clients, major incident).

---

## 9. Order of magnitude of costs (assumptions)

| Item | Estimated monthly cost |
|---|---|
| "Warm" standby environment | About EUR 2,500 |
| Off-site, immutable backup storage | About EUR 300 |
| Status page and crisis channel outside the infrastructure | About EUR 100 |
| Exercise time (about 6 days a year per person involved in recovery) | Internal cost, not invoiced |

In total, about EUR 2,900 a month as an assumption, to be set against the cost of a 24-hour interruption (cancellations, penalties, image), which a real study should quantify with management.

---

## 10. Internal audit of the arrangements

Exercises check that the plan works; the internal audit checks that **the arrangements** (policy, objectives, evidence, follow-up) are applied and effective. The findings below are **fictional**.

### Programme and scope

- **Frequency:** one audit a year, on a three-year cycle to cover every process; this year: P1, P2, P3, P4, P7 and the backup, restore and failover mechanisms.
- **Independence:** an auditor who did not take part in building the plan (a peer from another team, or an external provider).
- **Method:** the conduct follows the ISO 19011 guidelines for auditing management systems, adapted to a small organisation.

### Criteria

- The organisation's continuity policy and the RTO/RPO objectives in the BIA.
- The plan, the exercise calendar and the defined roles.
- Controls 5.29, 5.30, 8.13 and 8.14 of ISO/IEC 27002:2022.
- The principles of ISO 22301 (impact analysis, proportionate strategies, exercises, continual improvement), without auditing its requirements one by one.

### Method

1. **Document review:** policy, BIA, plan, runbooks, exercise reports.
2. **Interviews:** management, continuity lead, operations, support.
3. **Observing an exercise:** attending a table-top exercise.
4. **Checking evidence:** restore logs, backup reports, alert history, emergency accounts.

**Classification:** *major nonconformity* (critical objective not demonstrated, or a requirement of the arrangements missing); *minor nonconformity* (partial application, no immediate effect on an objective); *point of attention* (improvement lead, not a nonconformity).

### Findings

| Ref. | Class | Finding | Evidence | Risk | Corrective action | Owner | Deadline |
|---|---|---|---|---|---|---|---|
| A1 | Major | 4 h RTO for P1 not demonstrated end to end: only the database was timed, no real failover | No failover report | Surprise in a real event | Run exercise Q4 (real failover) and publish the report | Head of operations | 3 months |
| A2 | Major | No management review of continuity this year | No minutes | Objectives, budget and residual risks not arbitrated | Hold and formalise the review, schedule it yearly | General management | 1 month |
| A3 | Minor | RPO measured at 22 min against 15 min targeted; fix applied but not rechecked | Q2 test report, configuration history | Data loss above the objective | Measure again at the next test and keep the evidence | Head of operations | 3 months |
| A4 | Minor | Runbook updated 8 months ago, while the rule is 3 months | Last modification date | Outdated procedure | Automatic quarterly review reminder | Continuity lead | 1 month |
| A5 | Minor | Deputy coverage at 85%: the security lead's deputy absent from the last exercise | Attendance list | Role not filled in a crisis | Train and include the deputy in the next exercise | Security lead | 2 months |
| A6 | Minor | BIA not reviewed for 14 months (target: 12 months) | Date of last version | Objectives out of step with the business | Review the BIA and have process owners validate it | Continuity lead | 2 months |
| A7 | Point of attention | No documented alternative if the payment provider is unavailable (P3) | Plan and interview | Prolonged collection delay | Study a second provider or a manual collection mode | Head of finance | 6 months |
| A8 | Point of attention | Emergency accounts: only one of the two is tested | Test log | Second account failing at the critical moment | Test both accounts every cycle | Security lead | 2 months |
| A9 | Point of attention | Standby environment cost never compared with a quantified cost of interruption | Interview with management | Sizing not justified | Estimate the cost of a 24 h interruption and review the "warm" level | General management | 4 months |

### Follow-up and management review

- **Follow-up:** actions are logged in a single plan, reviewed monthly by the continuity lead; the closure of a major nonconformity is **verified by the auditor**, not just declared.
- **Management review (yearly):** inputs: exercise results, indicators, audit findings, changes in activity or suppliers, incidents; outputs: decisions on RTO/RPO objectives, resources, the level of the recovery strategy, accepted residual risks.
- **Improvement:** a gap repeated from one year to the next is escalated to management as a flaw in the arrangements, not as a simple oversight.

---

## What this study shows, and its limits

**Skills used:** business impact analysis, consistent MTPD, RTO and RPO, disaster scenarios, cost and risk trade-offs between strategies, a sequenced recovery plan with a decision criterion, a test plan with success criteria, roles, crisis communication, indicators, and links to controls 5.29, 5.30, 8.13 and 8.14 of ISO/IEC 27002:2022.

**Stated limits:**
- The organisation, architecture, durations, costs and expected results are **study assumptions**, with no audit or real measurement.
- In a real setting, RTO and RPO are negotiated with the business and management; they are proven by tests, which this study can only plan.
- The study draws on the principles of ISO 22301 and ISO/IEC 27031 without claiming to demonstrate compliance with these standards or detailing their requirements.
- The plan does not go deep into contractual aspects (provider availability clauses, insurance) or dependence on the payment provider.
- The exercise reports and audit findings are **fictional**, written to illustrate the method; no exercise or audit actually took place, and the success criteria are targets.
- The technical details (volumes, durations, alert thresholds, architecture) are mutually consistent assumptions, not proven on a real system.
- The plan is deliberately proportionate to a 40-person organisation; for a larger one, governance and exercise levels would be heavier.
