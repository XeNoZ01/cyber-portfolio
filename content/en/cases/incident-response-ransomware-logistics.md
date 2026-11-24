+++
title = "Incident response: compromised admin account and ransomware"
date = 2026-11-24
draft = true
description = "Fictional scenario in a 60-person logistics SME: hour-by-hour timeline, decisions, notification duties and lessons learned, following the NIST and ISO/IEC 27035 incident response lifecycle."
case_study = "fictive"
translationKey = "incident-response-ransomware"
+++

## Scope of the study

**Fictional organisation: Fictilog.** A logistics and warehousing SME of about 60 people on two sites: a head office (offices and main warehouse) and a secondary site ("site B": warehouse and loading dock). It manages stock for industrial clients and employs drivers and order pickers.

**Goal:** show how an incident response unfolds, from detection to lessons learned, with reasoned decisions and their consequences. The study follows the NIST incident response lifecycle (SP 800-61, whose revision 2 describes four phases) and ISO/IEC 27035, and links each step to the ISO/IEC 27002:2022 controls.

**What is invented:** the company, the people, the systems, the timeline, the technical indicators, the durations and the figures. **What is real:** the frameworks, the logic of the response lifecycle and the reference controls cited. This is not a real incident and not an engagement the author worked on.

---

## 1. Preparation: the starting point (assumptions)

A response is largely decided before the incident. Here is what the study assumes is in place at Fictilog on the day of the attack.

| Item | Assumed situation | ISO 27002:2022 control |
|---|---|---|
| Incident response plan | A two-page document exists, never tested | 5.24 |
| On-call | An external IT provider, reachable in the daytime; informal night cover | 5.24, 6.8 |
| Logging | Logs kept for 30 days, not centralised; no night-time monitoring | 8.15, 8.16 |
| Privileged accounts | One domain administrator account shared by two technicians, old password | 5.15, 8.2 |
| Authentication | No multi-factor authentication on VPN access | 8.5 |
| Backup | Daily online copy, plus a weekly offline copy; restore tested once in two years | 8.13 |
| Useful contacts | Incomplete list (insurer, legal counsel, data protection contact) | 5.5, 5.6 |

**Reading:** these weaknesses are common in an SME. They explain the rest of the study: the incident is nothing exceptional, and preparation is what decides how long it lasts.

### Inventory of the affected assets (fictional)

Criticality on 4 levels (1: low, 4: vital to the business). Values are study assumptions.

| Asset | Role | Owner | Criticality | State during the incident |
|---|---|---|---|---|
| Domain controllers (2, head office) | Directory and authentication | IT manager | 4 | Privileged account compromised; machines not encrypted |
| File server, site B | Working documents, site HR files | Site B manager | 3 | Encrypted, data copy likely |
| WMS server, site B | Warehouse management (stock, picking) | Operations manager | 4 | Encrypted |
| Application and print server, site B | Ancillary services | IT manager | 2 | Encrypted |
| ERP, head office | Invoicing, client orders | Finance and administration director | 4 | Not affected |
| VPN access | Remote access for technicians and the provider | IT manager | 3 | Entry point |
| Online backup | Daily copy | IT manager | 4 | Partly encrypted |
| Offline backup | Weekly disconnected copy | IT manager | 4 | Intact |
| 22 workstations, site B | Staff workstations | Site B manager | 2 | Encrypted |
| Scanning terminals, site B | Label reading | Operations manager | 2 | Unusable (depend on the WMS) |

### Reconstructed attack path (fictional, conceptual level)

| Step | What happened (assumption) | Weakness used |
|---|---|---|
| 1. Initial access | VPN login with the shared admin account, whose password was old and reused elsewhere | No MFA; shared account (8.5, 5.15) |
| 2. Reconnaissance | Browsing the directory and network shares over several nights | No monitoring, logs not centralised (8.15, 8.16) |
| 3. Persistence | Creation of the `svc-backup2` account and a scheduled task | Over-broad admin rights (8.2) |
| 4. Lateral movement | Hop from head office to site B with the privileged account | Weak network segregation (8.22) |
| 5. Collection | Night-time archiving and outbound transfer of file server folders | No detection of abnormal volumes (8.16) |
| 6. Impact | Encryption launched on site B, then ransom note dropped | Online backup reachable with a domain account (8.13) |

This map is useful both ways: it bounds what to search (each step leaves traces to look for) and it ties each weakness to a control to strengthen.

---

## 2. Detection and analysis

### Fictional timeline

Times are fictional. D is the day the incident is discovered (a Friday).

| When | Event (fictional) | Decision or action |
|---|---|---|
| D-9, night | Successful VPN login with the shared admin account from an unusual address (203.0.113.45, a range reserved for documentation) | Not detected: no alert, no review of such logins |
| D-9 to D-2 | The intruder explores the network, creates an extra service account, copies folders from site B's file server | Not detected |
| D, 01:10 | Encryption of site B's servers and workstations begins | |
| D, 05:30 | When warehouse B opens, the scanning terminals cannot connect; the team leader calls the IT manager | Alert raised by a person, not a tool |
| D, 05:50 | A ransom note is found on a server, files carry an unknown extension | **Qualification**: major security incident (assessment and decision, control 5.25) |
| D, 06:10 | The external provider confirms remotely that encryption is under way | |

### The 06:15 decision: contain before analysing

Two options existed: power machines off (stops encryption but wipes volatile memory and complicates analysis) or isolate the network without powering off (keeps traces, demands more care). **Choice: isolate site B** by cutting its link to head office and disabling switch ports, **without shutting machines down**. Machines still actively encrypting are stopped case by case. The decision and its time are logged.

### Indicators of compromise (fictional and generic)

| Type | Fictional example | Use |
|---|---|---|
| IP address | 203.0.113.45 (documentation range) | The intruder's VPN login |
| Account | `svc-backup2` (created by the intruder) | Persistence; to look for in the directory |
| File extension | `.fx-locked` | Extent of encryption |
| File | `READ_ME.txt` (ransom note) | Confirmation and scope tracking |
| Tool | An archiving utility launched at night on the file server | Probable sign of data copying |
| Domain | `example-collect.invalid` | Suspicious destination of a transfer |

These values are made up. Their purpose is to show what an indicator is for: looking for the same sign on other machines and in the logs.

### Log sources used (fictional)

| Source | What we look for | Retention (assumption) |
|---|---|---|
| VPN logs | Logins per account, source address, times, duration | 30 days |
| Domain controller security log | Logons (events 4624 and 4625), account creation (4720), group membership changes (4728, 4732, 4756), Kerberos tickets (4768, 4769) | 30 days |
| Server logs | Scheduled task creation (4698), service installation (7045) | 30 days |
| Firewall | Large outbound flows, unusual destinations | 30 days |
| File server | Mass reads, serial renames | 14 days (assumption) |
| Backup console | Job stops or deletions, console access | 30 days |
| Endpoint protection (head office only) | Alerts, unusual processes | 30 days |

### Detection rules (fictional, defensive pseudo-code)

These rules serve both to find the intrusion after the fact and to detect the next one. Thresholds are assumptions to be calibrated on real activity.

```text
RULE 1 - Unusual privileged login
  IF event = VPN login
  AND account belongs to the "administrators" group
  AND (time outside 07:00-19:00 OR source address never seen for this account)
  THEN alert (high priority)

RULE 2 - New privileged account
  IF event = account creation (4720)
  AND no matching change request
  OR added to a privileged group within the next hour (4728/4732)
  THEN alert (high priority)

RULE 3 - Remote scheduled execution
  IF event = scheduled task created (4698) OR service installed (7045)
  ON a server
  AND author account != usual operations account
  THEN alert (medium priority)

RULE 4 - Mass encryption
  IF more than N files renamed with the same new extension
  WITHIN 5 minutes ON one share
  THEN alert (critical priority) AND automatically isolate the source machine

RULE 5 - Abnormal outbound volume
  IF outbound volume from an internal server > S per hour
  TO a never-seen destination
  THEN alert (high priority)

RULE 6 - Backup tampering
  IF a backup job is stopped or deleted
  OR the backup console is accessed outside the usual window
  THEN alert (critical priority)
```

**Application to Fictilog:** rules 1, 2 and 5 would have surfaced the intrusion several days before encryption, provided logs were centralised and watched. That is exactly what was missing.

---

## 3. Containment, eradication and recovery

### Timeline of day D

| Time | Action | Reason or trade-off |
|---|---|---|
| 06:40 | **Crisis cell** assembled: management, IT manager, provider, finance, data protection contact, one person in charge of communication | One voice for decisions and a log kept continuously |
| 07:30 | Shared admin account disabled, sessions revoked, privileged passwords changed out of band | Cut the intruder's access; the VPN is closed during the analysis |
| 08:30 | **Scope** estimated: 3 servers and 22 workstations at site B; head office seems untouched. The online backup is partly encrypted; Sunday's offline copy is intact | That copy conditions the whole recovery |
| 09:30 | Disks and logs of affected machines copied to separate media, with a handling register | Preserve evidence (control 5.28) |
| 10:00 | **Degraded mode**: paper picking lists, site B shipments moved to head office | Minimum continuity (5.29, 5.30) |
| 11:00 | **Ransom: no payment and no negotiation** for now. Management decides with its legal counsel, considering that recovery of data is not guaranteed and that paying may fund further attacks | A management decision, not a technical one |
| 14:00 | Cyber insurer informed (assumption: a policy exists); **police report to be filed** | Policy conditions to be checked |
| 16:00 | Persistence hunt across the estate: the `svc-backup2` account found, one scheduled task removed | Eradicate before recovery, or the intruder returns |
| 18:00 | **Crisis communication** (see below) | |

### Following days (assumptions)

- **D+1:** site B servers rebuilt from clean images, directory checked, all account passwords changed, multi-factor authentication required on the VPN.
- **D+2:** data restored from the offline copy. It dates from Sunday, so four days of entries must be re-keyed from paper slips and client exchanges.
- **D+3:** partial resumption of site B activity under closer monitoring.
- **D+5:** assumed return to normal; heightened monitoring kept for several weeks.

**Order to remember:** contain, understand, eradicate, then restore. Restoring too early on a network the intruder can still reach restarts the incident.

### Technical detail of the response (fictional)

**Containment**
1. **Network isolation:** blocking site B's link at the firewall, then disabling switch ports of the affected machines. Machines are not powered off, except those still encrypting.
2. **Access revocation:** disabling the shared account and `svc-backup2`, closing open sessions, blocking the provider's VPN accounts until they are revalidated.
3. **Invalidating Kerberos tickets:** since the privileged account may have been used to obtain tickets, the `krbtgt` account password is changed **twice**, with a delay between the two to let replication complete (coordinated with the provider, as it disconnects services).
4. **Secret rotation:** passwords of service accounts, local administrators, the backup console and network devices; shared keys and VPN certificates renewed. All done from a clean workstation, out of band.

**Evidence preservation** (control 5.28)
- Collection order: what disappears fast first (live connections and processes, logs), then disks.
- Read-only disk copies, a hash of each copy (SHA-256), numbered media.
- Handling register: who, when, what, where the media is. VPN and domain controller logs are exported before any automatic rotation.

**Eradication**
- Removing the accounts, scheduled tasks and services created by the intruder, found using the indicators of compromise.
- Checking group policies, startup items and privileged group memberships.
- Each removal is logged to be compared against the asset inventory.

**Rebuilding from a clean backup**
- **Point of attention:** Sunday's offline copy dates from D-5, which is **after** the intrusion began (D-9). It may contain persistence items.
- Decision: **systems are rebuilt from clean images and only data is restored**, after the copy has been analysed in an isolated environment (searching for indicators, checking the accounts and tasks present).
- Recovery order: directory, then WMS, then file server, then workstations.

**Checks before resumption** (checklist)
- No unknown account or task in the directory and on servers.
- All privileged passwords renewed, MFA active on the VPN.
- Centralised logs and minimal alerts active before the site is reconnected.
- Restore tested on a data sample, consistency checked with operations.
- Written sign-off from the IT manager and the site B manager before the network is reconnected.

---

## 4. Notification duties and communication

### What has to be examined

| Topic | Framework | Application to Fictilog (fictional) |
|---|---|---|
| Personal data breach | GDPR, Article 33: notify the supervisory authority (in France, the CNIL) within 72 hours of becoming aware, unless the breach is unlikely to result in a risk to individuals | Employee records and driver data were on the affected server and a copy is likely: **the risk is judged non-negligible**, initial notification on D+1 |
| Informing individuals | GDPR, Article 34: if the risk is high, communication to the people concerned | Assessed on D+2; employees informed, as identity and bank details are involved (assumption) |
| Breach register | GDPR, Article 33(5): every breach is documented, even if not notified | The crisis cell's log is the basis |
| Notification in stages | GDPR, Article 33(4): information may be provided in phases | First notification incomplete, completed afterwards |
| NIS2 | **To be checked**: applicability depends on sector, size and the entity's status, as well as national transposition | No conclusion here: a warehouse is not necessarily in scope. A status check should come before any incident |
| Contract clauses | Client contracts, insurance | Information duties to be re-read (assumption: a 48-hour deadline at one client) |

### Crisis communication

- **Internally:** a short message to staff from 08:00 (what happened, what to do and not do, who to contact). Instruction: switch nothing on, plug nothing in, do not pay.
- **To clients and carriers:** a holding statement at 18:00, factual: IT incident, site B slowed, measures under way, a single point of contact. No speculation on the cause or on data.
- **To authorities and the insurer:** by the designated person, with the same facts as those given internally.
- **Principle:** state nothing unverified, update at fixed intervals, keep a single version of the facts.

---

## 5. Lessons learned

A review is held about three weeks after the incident (assumption), without looking for someone to blame: the question is "what do we change?".

### Findings and actions

| Finding | Action | ISO 27002:2022 control |
|---|---|---|
| A shared admin account without MFA opened the door | Named accounts, separate admin accounts, MFA on the VPN and privileged access | 5.15, 8.2, 8.5 |
| Nine days of intruder presence undetected | Centralised logs, longer retention, alerts on out-of-hours VPN logins | 8.15, 8.16 |
| The alert came from a team leader, not a tool | Endpoint and server protection with detection, including site B; 24/7 alert procedure | 8.7, 6.8 |
| The offline backup saved the recovery; the online copy was encrypted | Immutable or more frequent offline backups, scheduled restore test | 8.13 |
| Response plan never tested, contacts incomplete | Yearly tabletop exercise, up-to-date contact list (insurer, legal, authorities) | 5.24, 5.5, 5.6 |
| Personal data on a shared site B server | Network segmentation and data minimisation | 8.22 |
| Lessons learned | Incident report, update of the risk analysis and the plan | 5.27 |

### Follow-up indicators

Time from first intruder activity to detection, time from detection to containment, share of privileged accounts with MFA, date and result of the last restore test, number of crisis exercises per year.

### Fictional figures (labelled assumptions)

In this study, the partial shutdown of site B lasted five working days and four days of data were re-keyed. These durations are demonstration assumptions and represent no statistic.

---

## 6. Roles, responsibilities and decision rights

### Crisis cell levels (fictional)

| Level | Role | Members |
|---|---|---|
| Decision | Arbitrates binding decisions (shutting a site, ransom, notification) | Managing director, finance and administration director, legal counsel |
| Operational | Runs containment, analysis and recovery | IT manager, external provider, site B manager |
| Communication | Prepares and issues messages, keeps the log | Communication lead, data protection contact |

### RACI matrix by phase

R: does the work. A: ultimately accountable (one per row). C: consulted. I: informed.

| Phase | Management | IT manager | Provider | Data protection contact | Finance | Site B manager | Communication |
|---|---|---|---|---|---|---|---|
| Preparation | A | R | C | C | C | C | I |
| Detection and qualification | I | A/R | R | I | I | C | I |
| Containment | I | A | R | I | I | R | I |
| Eradication and recovery | I | A | R | C | I | R | I |
| Notification to authorities | A | C | I | R | C | I | C |
| Crisis communication | A | C | I | C | C | C | R |
| Lessons learned | A | R | C | C | C | C | C |

### Who may decide what

| Decision | Who decides | Condition |
|---|---|---|
| Isolate a segment or machines | IT manager, or the provider by delegation | Management informed immediately |
| Shut down a whole site | Managing director | If encryption is active and management cannot be reached, the IT manager may act, with approval after the fact within 30 minutes (assumption) |
| Contact the insurer | Finance and administration director | As soon as the incident is qualified; policy deadlines to be checked |
| Notify the data protection authority | Managing director, on the data protection contact's proposal | The contact prepares the file |
| Inform clients | Managing director, drafted by communication | Verified facts only |
| Pay or negotiate a ransom | Managing director alone, with legal counsel | Not delegable |

---

## 7. Post-incident internal audit

An internal audit measures the gaps between what was planned and what existed. It feeds the review under ISO/IEC 27001:2022 (clause 9.2, internal audit; clause 10.2, nonconformity and corrective action). The findings below are fictional.

| Ref. | Finding | Risk | Corrective action | Owner | Deadline (assumption) | Follow-up |
|---|---|---|---|---|---|---|
| F1 | Shared admin account, old password (5.15, 8.2) | Critical | Named accounts, separate admin accounts, password vault | IT manager | 1 month | Done; quarterly rights review |
| F2 | No MFA on the VPN or privileged access (8.5) | Critical | MFA everywhere | IT manager | 1 month | Done; activation rate tracked |
| F3 | Logs not centralised, 30-day retention, no night monitoring (8.15, 8.16) | High | Centralisation, longer retention, rules 1 to 6, outsourced monitoring | Provider, approved by management | 3 months | In progress |
| F4 | Weak network segregation between head office and site B (8.22) | High | Segmentation, filtering of administration flows | IT manager | 4 months | To be planned |
| F5 | Online backup reachable with a domain account (8.13) | High | Dedicated account, immutable or more frequent offline copy | IT manager | 2 months | In progress |
| F6 | Response plan untested, contacts incomplete (5.24, 5.5, 5.6) | Medium | Yearly simulation exercise, up-to-date contact list | Managing director | 6 months | To be planned |
| F7 | No detection-capable protection on site B servers (8.7) | High | Deployment on all servers and workstations | Provider | 3 months | In progress |

**Follow-up:** a monthly item on the management committee agenda until each finding is closed, with proof of closure (configuration capture, exercise report). A finding is closed only after verification, not when the action is announced.

---

## What this study shows, and its limits

**Skills used:** structuring an incident response along NIST SP 800-61 and ISO/IEC 27035, keeping a timeline, containment trade-offs, evidence preservation, qualifying notification duties, crisis communication, lessons learned linked to ISO/IEC 27002:2022.

**Stated limits:**
- Everything is fictional: the company, the systems, the indicators, the durations and the decisions. No real incident is described, and this text does not recount an engagement.
- The choices (isolate without powering off, do not pay) are reasonable decisions within this scenario, not universal rules: in reality they depend on the facts, legal advice and the insurer.
- The legal analysis is deliberately partial. GDPR Article 33 is cited as an example; whether NIS2 applies, and any sector-specific duties, must be checked with a lawyer or the competent authority.
- The study does not cost the real impact (lost trading, ransom, rebuild) or the proposed measures.
- The technical side is simplified: no detailed forensic analysis.
- The detection rules are illustrative pseudo-code, tied to no product or query language, and their thresholds (N, S) need calibrating. Windows event numbers are cited as examples and depend on the audit configuration.
- Deadlines, owners, criticality levels and audit timings are demonstration assumptions.
