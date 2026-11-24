+++
title = "GDPR compliance of a health centre"
date = 2026-11-24
draft = false
description = "Compliance walkthrough on a fictional 30-person organisation: record of processing, processors, data subject rights, impact assessment, security and handling of a personal data breach."
case_study = "fictive"
translationKey = "rgpd-centre-sante"
+++

## Scope of the study

**Fictional organisation: Les Alisiers health centre.** A neighbourhood health centre of about 30 people (6 doctors, 12 nurses and allied health staff, 6 medical secretaries and reception staff, 4 administration and management staff, 2 for logistics and technical work), following about 6,000 active patients (assumption). The name, headcount and figures are invented.

**Goal:** show the approach of a first compliance exercise under the General Data Protection Regulation (GDPR): map the processing activities, check legal bases and retention periods, frame processors, organise data subject rights, carry out an impact assessment on one high-risk processing, link security to ISO/IEC 27002:2022 and know how to react to a personal data breach.

**What is invented:** the centre, its tools, its suppliers, the figures and the incident. **What is real:** the GDPR articles cited and the ISO/IEC 27002:2022 reference controls.

**What it is not:** a real engagement, or legal advice. Health data is also subject to sector-specific rules (medical secrecy, patient records, health data hosting) that are only touched on here.

---

## 1. Map the processing activities (Art. 30)

Article 30 requires a **record of processing activities**. For each processing: purpose, categories of people and data, recipients, retention period and a general description of security measures. The first step is to ask each function (reception, care, billing, HR, IT) what it actually does with data.

The retention periods below are **study assumptions**: in a real case they are confirmed against sector rules and legal advice.

| Ref. | Processing | Purpose | Data | Legal basis | Retention (assumption) |
|---|---|---|---|---|---|
| T1 | Electronic patient record | Medical follow-up and care | Identity, history, reports, prescriptions (**health**) | Art. 6(1)(b) or (c) as applicable; Art. 9(2)(h) | 20 years after last contact |
| T2 | Online appointment booking | Schedule consultations | Identity, contact details, requested speciality (**reveals health**) | Art. 6(1)(b); Art. 9(2)(h) | Past appointments: 12 months |
| T3 | SMS appointment reminders | Reduce no-shows | Name, phone, appointment date | Art. 6(1)(a): consent | Until consent is withdrawn |
| T4 | Billing and third-party payment | Invoice, get paid | Identity, acts performed, insurance details | Art. 6(1)(b) and (c); Art. 9(2)(h) | 10 years (accounting records) |
| T5 | Staff management and payroll | Contracts, payroll, rota | Identity, bank details, absences | Art. 6(1)(b) and (c) | Contract duration, then legal archiving |
| T6 | Reception video surveillance | Safety of people and premises | Images | Art. 6(1)(f): legitimate interests | 30 days maximum |

### Critical reading

- **T1, T2 and T4 involve health data:** Article 9(1) prohibits it in principle, unless an exception applies. Here, the exception of Article 9(2)(h) (preventive medicine, diagnosis, care, by a professional bound by secrecy, Art. 9(3)) supports the processing. Consent is **not** the right basis for care.
- **T3 is the only processing based on consent:** it must be freely given, informed and withdrawable at any time (Art. 7). Refusing must not prevent someone from being treated. An SMS naming the speciality would also be excessive.
- **T6 relies on legitimate interests** (Art. 6(1)(f)): it requires a written balancing test against individuals' rights and clear information at the entrance (Art. 13).
- **Storage limitation** (Art. 5(1)(e)): at the start, the centre had no defined periods. The record forces a decision, then a purge process (ISO 27002 control 8.10, information deletion).

### Is a data protection officer (DPO) needed?

Article 37(1)(c) requires a DPO where the core activities involve **large-scale** processing of special categories of data. For a 6,000-patient centre this is debatable: it is a **question to settle and document**, not a given. Study assumption: the centre appoints an internal contact person and looks into a shared external DPO.

### Information asset inventory

The record says **what** is done with data; the inventory says **where it lives** (applications, machines, media, paper, suppliers). Without it, you cannot tell where to apply security measures or where to look for a rights request or a breach. Reference: ISO 27002:2022, 5.9 (inventory of information and other associated assets), 5.12 and 5.13 (classification and labelling).

**Classification used (assumption):** C1 Public, C2 Internal, C3 Confidential, C4 Health (health data and data revealing it). **A/I/C needs** rated 1 to 4: **A**vailability (4 = outage tolerated less than 4 h), **I**ntegrity (4 = an alteration can harm the patient's health), **C**onfidentiality (4 = disclosure causes serious harm).

| Ref. | Asset | Owner | Location | Data | Class. | A/I/C | Processing |
|---|---|---|---|---|---|---|---|
| A1 | Patient-record software (online service) | Coordinating doctor | Vendor's data centre (location to confirm) | Records, prescriptions | C4 | 4/4/4 | T1, T4 |
| A2 | Online booking platform | Reception manager | Supplier's cloud (to confirm) | Identity, requested speciality | C4 | 3/3/3 | T2, T3 |
| A3 | Business e-mail | IT manager | Online service | Exchanges, attachments that may hold health data | C4 | 3/3/4 | T1, T2, T4, T5 |
| A4 | Local file server | IT manager | Technical room | Administrative documents, exports, HR | C3 (may hold C4) | 3/3/3 | T4, T5 |
| A5 | Fixed workstations (18, assumption) | IT manager | Consulting rooms, reception | Access to A1 to A3, temporary files | C4 | 2/3/4 | T1 to T4 |
| A6 | Laptops (8, assumption) | IT manager | Mobile (home visits, home) | Same + possible local copies | C4 | 2/3/4 | T1, T2 |
| A7 | Backups (local NAS + off-site copy) | IT manager | Technical room + backup host | Copies of A4, and of A1 depending on the vendor | C4 | 3/4/4 | T1, T4, T5 |
| A8 | Paper records and archives | Medical secretariat | Locked archive room | Old records, letters, accounting documents | C4 | 2/3/4 | T1, T4, T5 |
| A9 | Multifunction copier-scanner | IT manager | Secretariat | Scanned documents, internal disk | C4 | 1/2/3 | T1, T4, T5 |
| A10 | Video recorder | Management | Technical room | Images | C3 | 2/2/3 | T6 |
| A11 | Payroll software (external firm) | HR manager | At the supplier | HR and bank data | C3 | 2/4/3 | T5 |
| A12 | Removable media (USB sticks) | IT manager | Scattered | Occasional copies | C4 | 1/2/4 | T1, T4 |

**Critical reading:**
- The **copier-scanner (A9)** and **USB sticks (A12)** are the assets that get forgotten: the first keeps copies on its disk, the second leave the centre uncontrolled. Measures: disk encryption or wiping at end of lease or life (ISO 27002: 7.14, secure disposal), ban on unencrypted media (7.10, storage media).
- Every processing in the record must point to at least one asset, and every C4 asset to a named owner: the owner validates access rights and the annual review.
- **A1 and A7 combine the highest needs:** they drive the DPIA (section 4) and the backup measures (section 5).

---

## 2. Frame the processors (Art. 28)

A processor handles data **on behalf of** the centre, which remains the controller. Article 28 requires a **contract** (or other legal act) and sufficient guarantees from the supplier.

| Supplier (fictional) | Role | Processing concerned | Point of attention |
|---|---|---|---|
| Patient-record software vendor | Hosting and maintenance | T1, T4 | Health data hosting: a requirement of French health law, to be checked |
| Online booking platform | Collection and storage | T2 | Data location, sub-processors |
| SMS provider | Sends reminders | T3 | No medical content in the message |
| IT maintenance company | Remote access to workstations | All | Privileged access: logging and limited access |
| External payroll firm | Produces payslips | T5 | Contract and confidentiality |

### Minimum contract content (Art. 28(3))

Subject matter, duration, nature and purpose of processing, types of data and of people; the centre's **documented instructions**; confidentiality commitment of the supplier's staff; security measures (Art. 32); conditions for using a **sub-processor**; help with data subject requests and breaches; **deletion or return** of data at the end of the contract; making available the information needed for audits.

### Findings (study assumptions)

Of the 5 suppliers, 2 have a contract that mentions data protection and 3 have nothing. Action: request the processing addendum or, failing that, consider changing supplier. ISO 27002 reference: 5.19 to 5.23 (supplier relationships, agreements, cloud services).

---

## 3. Organise data subject rights (Art. 12 to 22)

Patients have, among others, the rights of **access** (Art. 15), **rectification** (Art. 16), **erasure** (Art. 17), **restriction** (Art. 18), **objection** (Art. 21) and, depending on the legal basis, **portability** (Art. 20). Prior information falls under Articles 13 and 14.

### Response procedure adopted

1. **Receipt:** a single entry point (dedicated address and reception desk), no formality required. Date of receipt logged in a requests register.
2. **Identity check:** proportionate, so that health data is not handed to a third party.
3. **Qualification:** which right, which processing, which exceptions (for example, erasure is not absolute: Article 17(3) provides exceptions, including a legal obligation to retain).
4. **Handling** with the doctor concerned for anything touching the medical record: access to the record also follows sector-specific health rules, to be checked with legal counsel.
5. **Reply within one month** of receipt (Art. 12(3)). This can be extended by two months if the request is complex or numerous, provided the person is told **within the first month** and given the reasons.
6. **Written, free reply** (in principle), and a trace of the decision, including a reasoned refusal.

**Indicator:** average response time, number of late requests.

---

## 4. Impact assessment (DPIA, Art. 35) on a high-risk processing

### Why a DPIA, and why on T1

Article 35 requires a DPIA when a processing is **likely to result in a high risk** to rights and freedoms. Article 35(3)(b) cites large-scale processing of special categories of data. The European guidelines on DPIAs (WP248, endorsed by the European Data Protection Board) also list combinable criteria: sensitive data, vulnerable people, volume, and so on.

**Processing T1 (patient record)** combines health data, vulnerable people, access by many professionals and a **planned migration to an online hosted software** (a significant change). It is selected for the DPIA.

### Simple method used

Risk is assessed **for the data subjects**, not for the centre. Minimum content under Article 35(7): description of the processing, necessity and proportionality, risks, measures.

**Scales** (4 levels, same model as the other case study): severity for the person from 1 (negligible) to 4 (maximum, lasting harm to privacy or health); likelihood from 1 (unlikely) to 4 (almost certain). **Level = severity × likelihood.**

### Necessity and proportionality

| Question | Answer (study assumption) |
|---|---|
| Specified and legitimate purpose? | Yes: care and medical follow-up |
| Data limited to what is necessary? | To review: free-text fields in the software, history not needed by reception |
| Access rights by profile? | To put in place: reception does not see reports |
| Patients informed? | Posters and welcome booklet to complete (Art. 13) |

### Feared events

| Ref. | Feared event | Severity | Likelihood (before) | Level before | Measures | Level after |
|---|---|---|---|---|---|---|
| E1 | **Illegitimate access:** a compromised account or a curious colleague reads records | 4 | 3 | 12 | MFA, access profiles, logging and access review | 4 (4 × 1) |
| E2 | **Unwanted modification:** an error or attack alters a record (allergy erased) | 4 | 2 | 8 | Limited write rights, change history, backups | 4 (4 × 1) |
| E3 | **Disappearance:** record unavailable or lost during a consultation | 3 | 2 | 6 | Tested backups, paper fallback mode, availability commitment from the supplier | 3 (3 × 1) |

### DPIA conclusion (fictional)

The **residual** risk is brought to an acceptable level **if** the measures are really in place before the migration. Article 36 provides for prior consultation of the supervisory authority if a high risk remains despite the measures: this is not the case under this assumption. The DPIA is a living document: it is reviewed after any significant change.

---

## 5. Security of processing (Art. 32) and ISO/IEC 27002:2022

Article 32 gives no closed list: it requires measures **appropriate to the risk**, citing for example pseudonymisation and encryption, the ability to ensure confidentiality, integrity, availability and resilience, the ability to restore access to data, and **regular testing**. ISO/IEC 27002:2022 provides a catalogue of controls to structure the response.

| Need (Art. 32) | Measure | ISO 27002:2022 ref. | Priority |
|---|---|---|---|
| Confidentiality: access | Profiles by role, MFA on the patient record and e-mail | 5.15, 5.18, 8.5 | High |
| Confidentiality: devices | Laptop disk encryption, session lock | 8.24, 7.7 | High |
| Availability, restoration | Off-site backup, regular restore test | 8.13 | High |
| Integrity, detection | Logging of access to the patient record, periodic review | 8.15 | Medium |
| Staff awareness | Short annual training, reminders on sending data | 6.3 | High |
| Vulnerabilities, malware | Automatic updates, endpoint protection | 8.7, 8.8 | Medium |
| Suppliers | Contracts and follow-up of processors | 5.19 to 5.23 | High |
| Data leakage | Sending rules, secure sharing instead of attachments | 8.12, 5.14 | Medium |
| Incident management | Data breach procedure, register | 5.24 to 5.27 | High |
| Privacy protection | Policy and roles for personal data | 5.34 | Medium |

**Note:** ISO 27002 is a set of information security good practices. Applying its controls is not the same as complying with the GDPR, which has other requirements (legal bases, information, rights). The two complement each other.

### Retention and purge, processing by processing

Setting a period is not enough (Art. 5(1)(e)): a **mechanism** must apply it and there must be **evidence** that the purge took place. Distinction used: **active** base (available to teams), restricted **archive** (exceptional, traced access), then deletion. Periods are study assumptions.

| Processing | Active base | Archive | Purge mechanism | Evidence / check |
|---|---|---|---|---|
| T1 Patient record | 5 years after last contact | Up to 20 years | Yearly list of expired records, approved by a doctor, then deleted in the software | Purge log (date, scope, approver) |
| T2 Appointments | 12 months | None | Automatic monthly purge configured at the supplier | Quarterly check export |
| T3 SMS | Until consent is withdrawn | None | Number removed on request, then purged | Withdrawal register |
| T4 Billing | Current financial year | 10 years | Accounting archive, yearly purge of expired documents | Purge log |
| T5 HR | Contract duration | Legal obligations (to confirm) | Yearly HR review | Review minutes |
| T6 Video | 30 days | None | Automatic overwrite by the recorder | Quarterly test of the oldest image |
| Paper (A8) | As per processing | As per processing | Shredding or destruction provider | Destruction slip |

**Technical point:** data purged from the database **remains** in backups until their retention cycle expires (assumption: 35 days). Backup retention must be short, documented, and explained if someone requests erasure (ISO 27002: 8.10, information deletion).

### Access management and least privilege

Principle: each person only accesses what their role requires (ISO 27002: 5.15, 5.18, 8.2 privileged access rights, 8.3 information access restriction). **Named** accounts only, no shared accounts, MFA for the patient record and e-mail.

| Profile | Access to the patient record (T1) | Remark |
|---|---|---|
| Doctor | Read and write on own patients and care-team patients | |
| Nurse / allied health | Read and write on care acts | Cannot edit medical reports |
| Medical secretary | Identity, schedule, billing | **No access to reports** |
| Reception | Schedule and identity | No access to history |
| Billing | Acts and insurance | No access to history |
| Technical administrator | Account management, no reading of medical content | Exceptional access, logged |
| Management | Aggregated statistics | No named access |

**Break-glass:** in an emergency a professional may open a record outside their scope; the access is justified, **logged and reviewed** by the contact person within 48 h.

**Six-monthly rights review:** the IT manager extracts the list of accounts and profiles; each business manager confirms or removes; unconfirmed accounts are suspended within 5 days; the signed extract is kept as evidence.

### Joiners, movers and leavers

| Event | Actions | ISO 27002 ref. |
|---|---|---|
| Joiner | Written request from the business manager; named account with a standard profile; **signed confidentiality undertaking**; awareness training before access is opened | 6.1, 6.2, 6.6 |
| Mover | Profile reviewed, rights no longer needed removed | 5.18 |
| Leaver | Accounts suspended **on the leaving day** (patient record, e-mail, VPN, badge); equipment returned; deletion within 30 days; reminder of confidentiality after leaving | 5.11, 6.5 |
| Locums, trainees | Time-limited accounts, restricted profile | 5.18 |

### Encryption: at rest and in transit

- **At rest:** full-disk encryption on laptops and mobile workstations; removable media encrypted (or banned); backups encrypted, keys kept separately. For the online service (A1) the centre **does not control** encryption: it asks the vendor, by questionnaire and contract, to describe what is encrypted and how.
- **In transit:** access to online services only over encrypted connections (TLS); remote access through a VPN with MFA.
- **Limits not to hide:** encryption reduces risk in case of **theft or loss**, but does not protect against a compromised account or an unlocked workstation. Ordinary e-mail is not end-to-end encrypted: health data goes through a secure health messaging service or an access-controlled link, not as an attachment. Key management (who, where, what recovery procedure) is a control in its own right (ISO 27002: 8.24).

### Logging and detection

- **To log:** successful and failed log-ins, viewing, editing and export of a record, administration actions, break-glass.
- **Protecting logs:** restricted access, proportionate retention (assumption: 12 months), staff informed, since logs are also personal data.
- **Review:** simple automatic alerts if the software allows (access to many records in a short time, out-of-hours access, access to a relative's or colleague's record); otherwise a quarterly sample of 10 accessed records. Reference: 8.15 (logging), 8.16 (monitoring activities).
- **Stated limit:** a centre this size has no security operations centre; detection relies on sample checks and staff reporting.

### Backups and recovery

- 3-2-1 rule, **encrypted** backups, at least one copy **offline or protected against deletion**.
- Objectives (assumptions): maximum data loss of 24 h, patient record restored in under 8 h; a **paper fallback mode** is planned for consultations.
- **Restore test:** quarterly on a sample, full once a year; the result is dated and kept.
- For the online service, the vendor's backup does not relieve the centre from obtaining an **export option** and a reversibility clause (ISO 27002: 8.13, 5.30).

### Pseudonymisation and minimisation

- **Minimisation (Art. 5(1)(c)):** collect only what is needed; remove or control free-text fields; booking form without a detailed medical reason.
- **Pseudonymisation (Art. 25(1), Art. 32(1)(a)):** for management statistics and audit extracts, identity is replaced by an internal identifier, with the mapping table kept separately. Pseudonymised data is still personal data; the measure reduces risk, it does not remove it.
- **Testing:** no real data in the software's test or training environments (ISO 27002: 8.33, 8.11).

### Security of exchanges with patients

| Channel | Rule |
|---|---|
| Software portal or secure messaging | Default channel for reports and documents |
| SMS | Appointment date and time only, never speciality or reason |
| Ordinary e-mail | No health data; a temporary link is sent, with the code passed through another channel if needed |
| Phone | Identity checked on two items before any medical information |
| Post | Address checked, sealed envelope, "confidential" mention |
| Access request | Delivered through a secure channel or in person against ID |

References: ISO 27002: 5.14 (information transfer), 8.12 (data leakage prevention).

---

## 6. Personal data breach: assess and notify (Art. 33 and 34)

### Reminder

A **personal data breach** (Art. 4(12)) is a breach of security leading to the accidental or unlawful destruction, loss, alteration, disclosure of, or access to, personal data. Three main families: confidentiality, integrity, availability.

- **Art. 33:** the controller notifies the supervisory authority (in France, the CNIL) **within 72 hours** of becoming aware of it, **unless** the breach is unlikely to result in a risk to individuals. Beyond 72 hours, the notification includes the reasons for the delay.
- **Art. 34:** if the risk is **high**, the individuals concerned are also informed, without undue delay.
- **Art. 33(5):** **every** breach is documented internally, even if not notified.
- A processor informs the controller **without undue delay** after becoming aware of a breach (Art. 33(2)).

### Full procedure adopted

| Step | Content | Owner |
|---|---|---|
| 1. Detect and report | Any staff member reports an incident **without delay** through a single channel (dedicated address and the contact person's phone), with no fear of blame | Everyone |
| 2. Qualify | Is it a breach of confidentiality, integrity or availability of personal data (Art. 4(12))? Incident form opened; **the 72 h clock starts at awareness** | Contact person |
| 3. Contain | Recall the message, suspend the account, isolate the workstation, request written deletion | IT manager |
| 4. Assess risk to individuals | Nature and sensitivity of data, volume, ease of identification, severity of consequences, vulnerable people, nature of the recipient, mitigating measures (encryption, confirmed deletion) | Contact person, with the business manager |
| 5. Decide | No risk: internal documentation only. Risk: notify the authority. **High** risk: notify **and** inform individuals | Management |
| 6. Notify the authority | Content of Art. 33(3). If not all information is available, it may be provided **in phases** without undue further delay (Art. 33(4)) | Contact person |
| 7. Inform individuals (if high risk) | Clear and plain language, nature of the breach, contact, likely consequences, measures (Art. 34(2)). Not required in three cases (Art. 34(3)): data rendered unintelligible (encryption), subsequent measures removing the high risk, disproportionate effort (public communication instead) | Management, contact person |
| 8. Correct and learn | Root cause, corrective measures, procedure update, feedback to staff | Contact person, IT manager |
| 9. Document | Entry in the breach register, **even if no notification is made** (Art. 33(5)) | Contact person |

If the centre learns of the breach from a **processor**, the latter must inform it without undue delay (Art. 33(2)): the contract (section 2) sets the channel and the deadline. A **yearly exercise** (simulated misdirected sending or lost laptop) checks that the procedure works.

### Fictional incident

**Monday 9:10 am:** a secretary mistakenly sends an export of next week's appointments, meant for management, to an external address. The file holds **38 patients**: surname, first name, date of birth, speciality consulted and reason for visit. The recipient, a person with no link to the centre, replies the same day, points out the mistake and **confirms in writing that the message was deleted**. The error is reported to the contact person at **11:30 am**: this is when the centre "becomes aware" of the breach (T0).

### Assessment

| Question | Analysis (study assumption) |
|---|---|
| Is it a breach? | Yes: unauthorised disclosure of data (confidentiality) |
| Which data? | Identity, date of birth and reasons for visit: **health data** |
| How many people? | 38 patients, including vulnerable people |
| Who had access? | A single identified recipient, acting in good faith, who confirms deletion |
| Risk to individuals? | **Present:** health data disclosed, even briefly; mitigated by the small circle and the confirmed deletion |
| High risk? | **Not retained** under this assumption, but a reasoned decision to be revisited if something changes (for example, no confirmation of deletion) |

### Decision and timeline (fictional)

| When | Action |
|---|---|
| T0 (Monday 11:30 am) | Awareness, incident form opened, containment: message recall and written request for deletion |
| T0 + 4 h | Assessment by the contact person and management: confidentiality breach of health data |
| T0 + 24 h | **Decision: notify the CNIL** (risk cannot be ruled out); **no individual communication** (Art. 34), the decision is justified in writing |
| Before T0 + 72 h | Notification with the content required by Art. 33(3): nature of the breach, categories and approximate number of people and records, contact details, likely consequences, measures taken or proposed |
| Afterwards | Lessons learned, a sending rule put in place, entry in the breach register (Art. 33(5)) |

**Corrective measures:** recipient check before sending, secure sharing instead of e-mail, awareness. ISO 27002 references: 5.24 to 5.27 (incident management), 8.12 (data leakage prevention), 6.3.

**Honest discussion point:** another person could conclude there is no risk, or on the contrary a high risk. What matters is not the verdict but the **approach**: qualify, assess the risk to individuals, decide, justify and document within the deadlines.

### Breach register: fields kept

Form number; date and time of the breach; date and time of **awareness**; description and cause; nature (confidentiality, integrity, availability); categories and approximate number of people; categories of data; processor involved; likely consequences; measures taken; **risk level retained and reason** (none, risk, high); decision to notify the authority (date, reference); decision to inform individuals (yes or no, reason); corrective actions, owner and deadline; status. For the fictional incident above: level "risk", authority notified yes, individuals informed no (justified).

---

## 7. Prioritised action plan

| Priority | Action | Ref. | Deadline (assumption) |
|---|---|---|---|
| **P1** | Data breach procedure and breach register | Art. 33, 34; ISO 5.24 to 5.27 | 1 month |
| **P1** | MFA and access profiles on the patient record and e-mail | Art. 32; ISO 5.15, 8.5 | 1 month |
| **P1** | Fix the missing processor contracts | Art. 28; ISO 5.19 to 5.23 | 2 months |
| **P1** | Procedure for answering rights requests | Art. 12, 15 to 22 | 2 months |
| **P2** | Finalise the record of processing and retention periods | Art. 30, 5(1)(e) | 3 months |
| **P2** | DPIA on T1, before the software migration | Art. 35 | Before migration |
| **P2** | Laptop encryption, tested off-site backup | Art. 32; ISO 8.24, 8.13 | 3 months |
| **P2** | Update patient information (posters, booklet, website) | Art. 13 | 3 months |
| **P3** | Annual staff training | ISO 6.3 | 6 months |
| **P3** | DPO decision, access logging, annual review of the record | Art. 37; ISO 8.15 | 6 to 12 months |

---

## 8. Governance and responsibilities

### Roles

- **Management = controller.** The centre (in practice its management) determines purposes and means; it is responsible for compliance and must be able to **demonstrate** it (Art. 5(2), Art. 24(1)): appropriate technical and organisational measures, reviewed and updated. Data protection by design and by default (Art. 25) applies to every new project, such as the patient-record migration.
- **Compliance contact person or DPO.** The DPO informs, advises, monitors, cooperates with the authority and acts as contact point (Art. 39). If appointed, the DPO acts **without instructions** on their tasks and reports to the highest level (Art. 38(3)), their contact details are published and communicated to the authority (Art. 37(7)), and they must have no **conflict of interests** (Art. 38(6)): the director or the IT manager cannot monitor their own decisions. The DPO may be internal or external (Art. 37(6)).
- **IT / security manager (CISO or part-time equivalent).** Implements and runs the Article 32 measures; provides evidence (extracts, logs). ISO 27002: 5.2 (roles and responsibilities), 5.3 (segregation of duties).
- **Business managers** (coordinating doctor, reception, HR, billing): owners of the assets and processing in their scope; they approve access rights and retention periods.
- **Processors:** act on documented instructions (Art. 28); their contractual responsibility does not relieve the centre from choosing and monitoring them.

**DPO appointment: a decision to document.** Study assumption: the centre does not formally appoint a DPO at this stage ("large scale" is debatable, section 1) but names a **compliance contact person** (the quality manager) with a mission letter. It avoids calling them "DPO", a title that carries specific independence guarantees. The decision, its reasons and its review date are written down. If a DPO becomes mandatory or desirable, a shared external DPO is the most realistic option for 30 people.

### RACI matrix on key activities

R = does the work; A = decides and is accountable (legal responsibility stays with the controller); C = consulted; I = informed.

| Activity | Management | Contact person / DPO | IT manager | Business managers | Suppliers | Independent auditor |
|---|---|---|---|---|---|---|
| Keep the record of processing | A | R | C | C | I | |
| Answer data subject rights | I | A / R | C | R | C | |
| Handle a data breach | A | R | R | C | C | |
| Carry out a DPIA | A | R | C | C | C | |
| Frame processors | A | R | C | I | C | |
| Train staff | A | R | C | I | | |
| Audit compliance | A | C | C | C | I | R |

### The three lines of defence

| Line | Who | Role |
|---|---|---|
| 1st | Business managers, teams, IT manager | Apply the rules and check their own day-to-day activity |
| 2nd | Compliance contact person or DPO, security manager | Frame, advise, follow indicators, check application |
| 3rd | Independent internal audit | Assesses the whole and reports to management |

**Limit:** in a 30-person centre, independence of the third line is hard to get. Solutions: an auditor from another establishment or an external provider, with the rule that **nobody audits what they designed or run**. Remaining limits are stated in the report.

---

## 9. Internal compliance audit

### Audit programme

- **Frequency:** one full audit in the first year (the baseline), then thematic audits (access and authorisations, processors, rights and breaches), with an extra audit after a significant breach or a major change.
- **Scope (audit 1):** the 6 processing activities of the record, assets A1 to A12, the 5 processors.
- **Criteria:** applicable GDPR articles (5, 12 to 22, 25, 28, 30, 32 to 35), the centre's internal policies and procedures (data protection policy, rights procedure, breach procedure, retention policy, access rules) and contract clauses.
- **Reference framework:** the approach is inspired by the internal audit and management review of ISO/IEC 27001:2022 (clauses 9.2 and 9.3) and by the auditing guidelines of ISO 19011. The centre is not certified: this is an organisational model, not a claim of conformity to these standards.

### Method

1. **Preparation:** audit plan, scope, criteria, samples, schedule.
2. **Interviews** with management, the contact person, the IT manager and a sample from each function.
3. **Document review:** record, contracts, procedures, requests and breach registers.
4. **Sample tests (assumptions):** 100% of active patient-record accounts compared with the staff list; 5 processor contracts; 10 accessed records checked in the logs; all rights requests of the year; access test with a secretary account.
5. **Report and closing meeting:** findings with evidence, factual validation by the auditees, then action plan.

### Finding classification grid

| Class | Definition |
|---|---|
| Compliant | The requirement is met and evidence exists |
| Minor nonconformity | The requirement is partly met; no immediate exposure of individuals |
| Major nonconformity | The requirement is not met, or evidence is missing on an essential point, with exposure of individuals or breach of a legal obligation |
| Point of concern | Not a nonconformity today, but a risk or an improvement to watch |

### Fictional findings of audit 1

| Ref. | Finding and evidence | Criterion | Class | Risk to individuals | Corrective action | Owner | Deadline |
|---|---|---|---|---|---|---|---|
| C1 | The record lists 5 processing activities; video surveillance (T6) is missing (comparison with the inventory) | Art. 30 | Minor | Low: processing not framed or disclosed | Add T6, legitimate-interest assessment, information sign | Contact person | 1 month |
| C2 | 3 suppliers out of 5 have no processing clause (review of 5 contracts) | Art. 28 | **Major** | High: data transferred without guarantees | Addenda or change of supplier | Management | 2 months |
| C3 | Of 30 active patient-record accounts, 4 belong to people who left, one 7 months ago (match against the staff list) | Art. 32; ISO 5.18, 6.5 | **Major** | High: possible illegitimate access | Immediate suspension, leaver procedure, six-monthly review | IT manager | 1 week (suspension), 2 months (procedure) |
| C4 | The "secretary" profile can open reports (test with a secretary account) | Art. 5(1)(f), 25, 32 | **Major** | High: unnecessary access to health data | Reconfigure profiles per the matrix | IT manager, coordinating doctor | 1 month |
| C5 | Appointment purge is not enabled: about 3,400 appointments older than 12 months (assumption) remain on the platform | Art. 5(1)(e) | Minor | Medium: excessive retention | Enable purge, traced initial purge | Reception manager | 1 month |
| C6 | An access request was handled in 26 days, but with no register and no trace of identity verification | Art. 12(3), 5(2) | Minor | Medium: deadline met, evidence weak | Requests register, reply template | Contact person | 2 months |
| C7 | No breach procedure; 2 misdirected-sending incidents of the year were not documented (interviews) | Art. 33(5), 33 | **Major** | High: possibly missed notifications | Procedure and register, after-the-fact qualification of the 2 incidents | Management, contact person | 1 month |
| C8 | The backup restore has never been tested (no trace) | Art. 32(1)(c) and (d); ISO 8.13 | Minor | Medium: prolonged unavailability of the record | Quarterly restore test | IT manager | 3 months |
| C9 | 30 of 30 staff signed a confidentiality undertaking (review of HR files) | Art. 32(4); ISO 6.6 | Compliant | | Maintain for joiners | HR manager | Ongoing |
| C10 | The copier-scanner is leased; its disk keeps scans and nothing is planned for the end of the lease | ISO 7.14, 8.10 | Point of concern | Medium if returned without wiping | Wiping or disk destruction clause | IT manager | At end of lease |

### Action follow-up and management review

- **Follow-up:** each nonconformity gets an owner, a deadline, a status (open, in progress, closed, late) and **closing evidence** (copy of the addendum, account extract, purge log). Major nonconformities are reviewed monthly by the contact person; closure is verified, not declared.
- **Effectiveness check:** for major nonconformities, a new sample is checked after 3 months.
- **Management review (yearly, modelled on clause 9.3 of ISO 27001):** inputs: audit results, indicators, breaches, rights requests, planned changes, state of risks; outputs: decisions, resources, priorities for the next year, revision of the record and the DPIA. Minutes are kept.

---

## 10. Compliance steering indicators

Starting values and targets: study assumptions, drawn from the fictional audit.

| Indicator | Definition | Start | Target | Frequency | Source |
|---|---|---|---|---|---|
| Record up to date | Share of processing activities reviewed in the last 12 months | 50% | 100% | Six-monthly | Record |
| Processor contracts | Suppliers with an Art. 28-compliant contract | 2 of 5 | 5 of 5 | Quarterly | Contracts file |
| Data subject rights | Share of requests answered within one month; average time | 100%; 26 days | 100%; under 20 days | Quarterly | Requests register |
| Breaches | Number detected, notified, average notification time | Not tracked | 100% tracked; under 72 h | Monthly | Breach register |
| Training | Share of staff trained in the last 12 months | 0% | 100% | Six-monthly | Attendance sheet |
| Up-to-date accounts | Share of active accounts confirmed at the last review | Not measured | 100% | Six-monthly | Signed extract |
| MFA | Share of critical accounts with MFA | 0% | 100% | Quarterly | Admin console |
| Backups | Date and result of the last restore test | Never tested | Under 3 months, successful | Quarterly | Test report |
| Purge | Share of planned purges done on time | 0% | 100% | Quarterly | Purge log |
| DPIA | DPIAs carried out out of DPIAs required | 0 of 1 | 1 of 1 | Each project | DPIA file |
| Audit | Open major nonconformities; late items | 4; 0 | 0; 0 | Monthly | Audit follow-up |

These indicators feed the management review. An indicator with no threshold or owner is not a steering indicator: each one here has an owner (the contact person unless stated otherwise) and a target.

---

## What this study shows, and its limits

**Skills used:** processing mapping and record, choice of legal bases, framing processors, rights procedure, impact assessment, link to ISO 27002, qualification of a data breach, action plan.

**Stated limits:**
- **This is not legal advice.** The qualifications (legal bases, deadlines, retention, need for a DPO) are study assumptions to be confirmed by a lawyer or the competent contact person.
- Health-sector rules (medical secrecy, patient records, health data hosting) are not covered in detail.
- Figures, periods and scores are assumptions to illustrate the method.
- The study does not cost the measures, which would be needed to arbitrate in practice.
- The audit findings, samples, indicator values and classification levels are invented to illustrate the method; the ISO standards are cited as organisational models, with no claim of certification.
- The centre, the suppliers and the incident are entirely fictional.
