**INTERNATIONAL CYBERSECURITY AND DIGITAL FORENSICS ACADEMY**

# Business Continuity and Disaster Recovery Assignment

**GRC205 Incident Management and Business Continuity, Week 21 \| Northbridge Skills Institute (fictional)**

| **Item**       | **Detail**                                                                                             |
|----------------|--------------------------------------------------------------------------------------------------------|
| Programme      | Governance, Compliance, and Risk Training Program, ICDFA                                               |
| Course         | GRC205 Incident Management and Business Continuity, Week 21                                            |
| Student name   | PONSEL ILIYA                                                                                           |
| Student ID     | 2025/GCR/12779                                                                                         |
| Official email | gcr2512779@students.icdfa.edu.ng                                                                       |
| Word count     | approx. 3,290 words, excluding the cover, tables, reference list and appendices (limit 3,000 to 3,500) |
| Role taken     | Business Continuity and Disaster Recovery (BC/DR) Coordinator, Northbridge Skills Institute            |
| Time reference | All times are WAT. Day 0 is a Monday. The disruption begins at 08:00.                                  |

> **Data note.** All organisations, people, figures and contact details are fictional. Cost figures are exercise estimates, not observed losses. Recovery results are modelled baseline results, not live tests.

## Assumptions and evidence status

Each statement in this report is one of: **Confirmed** (supplied in the brief), **Assumption** (my planning choice), **Unverified** (needs checking before it is relied on), or **Modelled** (calculated from supplied timings).

| **ID** | **Statement**                                                                                                                                          | **Status** | **How it will be checked**           |
|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------|------------|--------------------------------------|
| A1     | DB01 incrementals run hourly, so the 07:00 chain is B01 plus eight increments (00:00 to 07:00). The brief gives only the chain's end point.            | Assumption | Read the backup catalogue            |
| A2     | The baseline drill passes all integrity checks (stated in the brief). Later injects remove this assumption.                                            | Confirmed  | Tabletop injects                     |
| A3     | The master examination paper and the learner access-arrangement list can be obtained outside LMS01.                                                    | Unverified | Academic Office confirms by 08:45    |
| A4     | Microsoft 365 services beyond identity and email (Forms, Teams, SharePoint) are usable on mobile internet. The brief confirms only identity and email. | Unverified | IT tests by 08:45                    |
| A5     | The 80 own-device learners sit in an invigilated room at the alternate facility, and the recovery network is reachable from there.                     | Assumption | Facilities confirms room use         |
| A6     | The warm site supplier can start work at 08:00 and the drill timings hold.                                                                             | Modelled   | Supplier confirmation and timed test |
| A7     | Staff mobile numbers are available through personal phones and a copy of the contact list in Microsoft 365.                                            | Assumption | Phone tree test                      |
| A8     | The payment provider can supply a settlement report on request.                                                                                        | Unverified | Provider contract and test           |
| A9     | Up to 10 managed laptops can be loaned from staff stock.                                                                                               | Unverified | Inventory check                      |
| A10    | Business acceptance takes 30 minutes per function after restoration.                                                                                   | Assumption | Measured in tests                    |

# Task 1: Business Impact Analysis

## 1.1 Five-function BIA

The BIA follows the six phases in the teaching deck (ICDFA, n.d., slide 5). Table 1.1 records ownership, dependencies and resources. Table 1.2 records impact over time, the minimum acceptable service and the recovery priority. Volumes not supplied in the brief are marked.

**Table 1.1: Owners, users, dependencies and resources**

| **Function**                   | **Owner role**         | **Users affected**                                            | **Dependencies**             | **People**                               | **Premises**                                    | **Suppliers**                                          | **Information**                                          |
|--------------------------------|------------------------|---------------------------------------------------------------|------------------------------|------------------------------------------|-------------------------------------------------|--------------------------------------------------------|----------------------------------------------------------|
| F1 Teaching and assessment     | Academic Director      | 1,800 learners (120 sit the 11:00 exam); teaching staff       | Network, AUTH01, DB01, LMS01 | Exam officer, invigilators, ICT support  | Alternate facility (25 workstations from 10:00) | Alternate facility provider, mobile ISP, Microsoft 365 | Exam paper, enrolment list, access arrangements, results |
| F2 Fees and finance            | Finance Manager        | 1,800 learners as payers; finance staff                       | Network, AUTH01, DB01, FIN01 | Two finance officers (maker and checker) | Alternate desks                                 | Payment provider, bank                                 | Receipts, wallet records, provider settlement report     |
| F3 Admissions and registration | Registrar              | Applicants (number not supplied); learners requesting changes | Network, AUTH01, DB01, REG01 | Registry staff                           | Alternate desks                                 | Microsoft 365 for temporary forms                      | Applications, registration changes, supporting documents |
| F4 Staff payroll               | HR and Payroll Manager | 90 staff                                                      | Network, AUTH01, DB01, HR01  | Payroll officer and approver             | Alternate desk                                  | Bank                                                   | Payroll master, change log, bank file                    |
| F5 Records and certificates    | Records Officer        | Learners and alumni requesting records (volume not supplied)  | Network, AUTH01, FS01        | Records officer                          | Alternate desk                                  | None beyond the recovery supplier                      | Record files, certificate templates, access permissions  |

**Table 1.2: Impact over time, minimum service and priority**

| **Function** | **Rate (NGN/h)** | **1 h** | **8 h** | **24 h**  | **Time-critical effect**                                 | **Minimum acceptable service and controls**                                                                                                                                                  | **Priority** |
|--------------|------------------|---------|---------|-----------|----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| F1           | 60,000           | 60,000  | 480,000 | 1,440,000 | 11:00 exam; NGN 120,000 one-off if no acceptable service | Exam delivered in controlled sittings on a temporary service. Approval: Academic Director. Controls: identity check, invigilation, parallel papers, results logged in a controlled register. | 1            |
| F2           | 40,000           | 40,000  | 320,000 | 960,000   | Fee confirmation due 16:00                               | Confirm fees from the payment provider's settlement report. Approval: Finance Manager with a second checker. Controls: numbered manual log, no wallet edits until reconciliation.            | 2            |
| F3           | 20,000           | 20,000  | 160,000 | 480,000   | Admissions close 18:00                                   | Accept applications through a restricted online form with timestamps. Approval: Registrar. Controls: submission log, key-in under dual control after REG01.                                  | 3            |
| F4           | 10,000           | 10,000  | 80,000  | 240,000   | Bank file due 12:00 Day 1                                | Hold changes; verify against the last approved run; dual authorisation; one bank submission with a unique batch ID.                                                                          | 4            |
| F5           | 5,000            | 5,000   | 40,000  | 120,000   | 48-hour service commitment                               | Log requests; release only verified urgent records with Records Officer approval and access checks.                                                                                          | 5            |

## 1.2 Worked impact calculations

The hourly amounts are separate, non-overlapping incremental costs, applied for the first 24 hours. With no recovery or continuity measures, no acceptable assessment service exists at 11:00, so the NGN 120,000 rescheduling cost is added once to both totals. The combined rate is 60,000 + 40,000 + 20,000 + 10,000 + 5,000 = NGN 135,000 per hour.

**Table 1.3: Disruption cost with no recovery or continuity measures**

| **Function**                    | **Rate (NGN/h)** | **8-hour calculation** | **8-hour cost (NGN)** | **24-hour calculation** | **24-hour cost (NGN)** |
|---------------------------------|------------------|------------------------|-----------------------|-------------------------|------------------------|
| F1                              | 60,000           | 60,000 x 8             | 480,000               | 60,000 x 24             | 1,440,000              |
| F2                              | 40,000           | 40,000 x 8             | 320,000               | 40,000 x 24             | 960,000                |
| F3                              | 20,000           | 20,000 x 8             | 160,000               | 20,000 x 24             | 480,000                |
| F4                              | 10,000           | 10,000 x 8             | 80,000                | 10,000 x 24             | 240,000                |
| F5                              | 5,000            | 5,000 x 8              | 40,000                | 5,000 x 24              | 120,000                |
| Subtotal                        | 135,000          | 135,000 x 8            | 1,080,000             | 135,000 x 24            | 3,240,000              |
| Examination rescheduling (once) | n/a              | Trigger met at 11:00   | 120,000               | Trigger met at 11:00    | 120,000                |
| Total                           |                  |                        | 1,200,000             |                         | 3,360,000              |

**Two limitations.** First, a flat hourly rate hides the timing of harm. Real losses depend on deadlines: little may be lost until 16:00 for fees or 12:00 on Day 1 for payroll, then a lot at once. The figures also assume no recovery at all, so they overstate expected loss and are not a forecast. Second, adding the rates assumes functions do not affect each other and ignores recovery costs, overtime, supplier charges and penalties. A payroll error, for example, creates correction work that grows with delay. All rates are exercise estimates, not observed data.

**Financial and non-financial impact.** The totals measure money only. Learner welfare (120 candidates facing disruption on the day), reputation, and data integrity cannot be reduced to an hourly rate. F5 has the lowest rate, yet a certificate issued from corrupted records or released without access controls could cause lasting harm. Exam integrity matters more than the NGN 120,000 trigger, because a compromised assessment cannot be repaired by payment.

## 1.3 Priorities and dependency risks

The 11:00 examination makes F1 the first priority. It has the highest rate, a deadline three hours after the disruption, and learners who will attend. The later deadlines then set the order: fees at 16:00 (8 hours), admissions at 18:00 (10 hours), payroll at 12:00 on Day 1 (28 hours), and records within 48 hours. Business priority is therefore F1, F2, F3, F4, F5.

The technical order is different, because shared infrastructure sets it. Network, AUTH01, and security and backup checks must come first. DB01 must follow before any of F1 to F4 can be restored. FS01 does not need DB01, so F5 can be restored early on the second technician without delaying anything more urgent. Restoring the lowest priority function second is therefore a use of idle capacity, not a change of priority. Where both technicians are free after DB01, F1 starts first, F2 second, F3 third and F4 fourth.

**Table 1.4: Priority sequence**

| **Business rank** | **Function** | **Driver**                             | **Technical position**                                            |
|-------------------|--------------|----------------------------------------|-------------------------------------------------------------------|
| 1                 | F1           | 11:00 exam, highest rate, integrity    | After the DB01 path (LMS01 is the last step on the critical path) |
| 2                 | F2           | 16:00 fee confirmation, reconciliation | After DB01 (FIN01)                                                |
| 3                 | F3           | 18:00 admissions close                 | After DB01 (REG01)                                                |
| 4                 | F4           | Bank deadline 12:00 Day 1              | After DB01 (HR01)                                                 |
| 5                 | F5           | 48-hour commitment                     | Earliest, in parallel (needs only AUTH01 and checks)              |

**Table 1.5: Dependency risks**

| **\#** | **Dependency risk**                                                                                        | **Effect**                                                                          | **Mitigation**                                                                                 |
|--------|------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| DR1    | DB01 is a single point for F1 to F4 and takes 90 minutes.                                                  | One failed chain delays four functions.                                             | Verify the chain first; keep a differential and a fallback backup; temporary procedures ready. |
| DR2    | AUTH01 is a separate local directory, and its snapshot is 8 hours old.                                     | Accounts and permissions may differ from Microsoft 365, blocking sign-in.           | Reconcile the directory with HR and Microsoft 365 lists; take snapshots every 4 hours.         |
| DR3    | Only two technicians are available, and the main path is serial.                                           | No time can be saved by adding parallel work; fatigue risk if closure lasts longer. | Third technician on call; 12-hour rota; supplier support.                                      |
| DR4    | Production and network backup administration share one identity, and offsite independence is undocumented. | One compromised account could affect production and backups.                        | Separate identities, MFA, immutable copy, supplier evidence.                                   |
| DR5    | Temporary services need Microsoft 365, mobile internet and 25 workstations from 10:00.                     | Capacity or connectivity loss limits examination and finance work.                  | Test at 08:45; keep 10 loaned laptops and a paper fallback.                                    |

# Task 2: Recovery objectives and backup design

## 2.1 Definitions and targets

The teaching deck defines **RTO** as the maximum acceptable downtime until restoration, **RPO** as the maximum acceptable data loss measured in time, and **MTD** as the absolute maximum outage before irreparable harm, which includes RTO plus extra recovery and validation time (ICDFA, n.d., slide 6). Tiers follow the deck: T1 mission critical, T2 important, T3 non-critical.

**Table 2.1: Objective matrix (elapsed from 08:00)**

| **Function** | **Tier** | **RTO** | **RPO** | **MTD** | **Buffer (MTD minus RTO)** | **Justification**                                                                                          |
|--------------|----------|---------|---------|---------|----------------------------|------------------------------------------------------------------------------------------------------------|
| F1           | T1       | 4 h     | 1 h     | 8 h     | 4 h                        | Exam and teaching; MTD set at the same-day end of the working window so a later sitting is still possible. |
| F2           | T1       | 4 h     | 1 h     | 8 h     | 4 h                        | Fee confirmation at 16:00; reconciliation needs about 3.5 hours after restoration.                         |
| F3           | T2       | 6 h     | 4 h     | 10 h    | 4 h                        | Admissions close at 18:00; applications can be re-keyed from the form log.                                 |
| F4           | T2       | 8 h     | 4 h     | 24 h    | 16 h                       | Bank file due at 12:00 on Day 1; MTD leaves 4 hours for checks and submission.                             |
| F5           | T3       | 12 h    | 24 h    | 36 h    | 24 h                       | 48-hour commitment; MTD leaves 12 hours of margin.                                                         |

I declare a function **recovered** only when its technical restoration is complete, the owner has signed the acceptance checklist (sample transactions, totals and access controls checked), and reconciliation of temporary records has started with no blocking discrepancy. Restoring technology is not the same as resuming an acceptable business service (see 4.4). Each buffer is at least 4 hours, which absorbs the 30-minute acceptance step (A10) and one failed step without reaching the MTD.

## 2.2 Checkpoint ages and data-loss windows

The reference point is the 08:00 disruption. DB01's latest recoverable point is the 07:00 incremental chain (B02), which depends on B01 and every increment through 07:00.

**Table 2.2: Checkpoint calculations at 08:00 on Day 0**

| **System**               | **Checkpoint**    | **Calculation**                       | **Age (data-loss window)** | **Functions affected** | **Proposed RPO**           | **Gap**                           |
|--------------------------|-------------------|---------------------------------------|----------------------------|------------------------|----------------------------|-----------------------------------|
| DB01                     | B02, 07:00 Day 0  | 08:00 minus 07:00                     | 1 hour                     | F1 to F4               | 1 h (F1, F2); 4 h (F3, F4) | None, but no margin for F1 and F2 |
| AUTH01                   | B03, 00:00 Day 0  | 08:00 minus 00:00                     | 8 hours                    | F1 to F5               | 4 h                        | 4 hours over                      |
| FS01                     | B04, 23:00 Day -1 | 08:00 minus 23:00 (9 hours)           | 9 hours                    | F5                     | 24 h                       | None (15 hours margin)            |
| Disconnected full backup | 17:00 Day -3      | 7 h (to midnight) + 24 h + 24 h + 8 h | 63 hours                   | Fallback only          | n/a                        | Exceeds every RPO                 |
| DB01 base (B01)          | 23:00 Day -1      | 08:00 minus 23:00                     | 9 hours                    | F1 to F4               | n/a                        | Base only; latest point is B02    |

**Reconciliation.** What can be reconciled from temporary records: payments made after 07:00 (the provider's settlement report is independent of local systems), applications and requests received by email, and staff and learner changes recorded in emails or forms. What cannot be assumed recoverable: local transactions between 07:00 and 08:00 held only on DB01; directory changes after 00:00 on AUTH01 (password resets, role changes, disabled leavers); and file changes on FS01 after 23:00 on Day -1. The AUTH01 gap is the most serious because every function depends on it and it can silently re-enable old accounts.

## 2.3 Backup and restoration design

The design closes the AUTH01 gap and strengthens the DB01 margin. It uses full, differential and incremental backups in line with the deck's comparison: full backups restore fastest and incrementals slowest, with differentials in between (ICDFA, n.d., slide 8).

**Table 2.3: Backup schedule**

| **System** | **Schedule**                                                               | **Retention**                                                              | **Placement**                                                              | **Restore chain**                                                                                                          |
|------------|----------------------------------------------------------------------------|----------------------------------------------------------------------------|----------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| DB01       | Full nightly at 23:00; differential at 03:00; incremental every 30 minutes | Full: 14 daily, 4 weekly, 12 monthly. Differential and increments: 14 days | Copy 2 offsite within 15 minutes of each job; Copy 3 immutable weekly full | Latest full (23:00) + 03:00 differential + every increment after 03:00 up to the chosen point (11 items for a 07:30 point) |
| AUTH01     | Snapshot every 4 hours, plus before any privileged change                  | 14 days                                                                    | Copy 2 offsite; Copy 3 weekly                                              | Latest snapshot and configuration                                                                                          |
| FS01       | Full nightly at 23:00                                                      | 14 daily, 12 monthly                                                       | Copy 2 offsite; Copy 3 weekly                                              | Latest full                                                                                                                |

**3-2-1 and protection.** Three copies exist: production, an encrypted offsite repository in a different flood zone, and an independent immutable or offline copy held under separate credentials. Two media types are used (disk and cloud object storage, plus the offline or immutable copy), and one copy is offsite (ICDFA, n.d., slide 8). Backup administration is separated from the production administrator identity, uses MFA, requires two-person approval to delete, and has retention locks. Data is encrypted in transit and at rest with keys held by Northbridge, not only by the supplier. Restoration is tested monthly for files, quarterly for the full DB01 chain in isolation, and annually at the warm site with timings recorded. The differential reduces dependence on early increments: if a night increment fails, the 03:00 differential still gives a later valid base.

**Why offsite storage or a live replica does not prove recoverability.** An offsite copy proves only that data was copied. It may be incomplete, corrupted, undecryptable, or unreachable without credentials that the disaster has removed. A live replica copies mistakes and malicious changes within seconds, so it can propagate corruption. The shared administrator identity and the supplier's undocumented access controls mean an attacker or error could affect production and backup together.

**Checks.** Integrity: verify hashes and chain order, confirm every increment is present, and scan for malware before use. Access independence: show that production administrator credentials cannot read, change or delete backups, and obtain the supplier's evidence (unverified until received). Usability: restore to an isolated environment, run database consistency checks, and have the function owner check sample records and totals. Unverified assumptions are A1, A3, A4 and the supplier's independence controls.

# Task 3: Recovery strategy and budget

## 3.1 Option comparison

The quoted recovery windows are planning estimates and are checked against my targets (RTO F1 and F2 4 hours, RPO 1 hour). Deck guidance shows warm sites suit hours to a day or two of downtime, hot sites suit minutes to two hours, and cold sites days to weeks (ICDFA, n.d., slide 7).

**Table 3.1: Option matrix**

| **Criterion**                | **Cold**                               | **Warm**                                                    | **Hot**                                                                        |
|------------------------------|----------------------------------------|-------------------------------------------------------------|--------------------------------------------------------------------------------|
| First critical service       | 24 to 48 h                             | 3 to 5 h (drill: 3 h 45 m for LMS01)                        | About 1 h                                                                      |
| Meets F1 and F2 RTO of 4 h?  | No                                     | Yes in the drill; the upper estimate (5 h) would miss       | Yes                                                                            |
| Data-loss window             | Up to 24 h                             | 1 to 6 h                                                    | Up to 15 min                                                                   |
| Meets 1 h RPO for F1 and F2? | No                                     | Only if contracted at 1 h and supported by my backup design | Yes                                                                            |
| Staffing                     | Two technicians cannot rebuild in time | Two technicians suffice per drill; third on call            | Needs round-the-clock monitoring, beyond two technicians                       |
| Geography                    | Any site                               | Must be outside the main site's flood zone                  | Replica region must be outside the flood zone                                  |
| Supplier dependencies        | Low                                    | Moderate: site, restore support                             | High: replication provider, lock-in                                            |
| Corruption propagation       | Low                                    | Low: restoration from selectable points                     | High: real-time replication copies bad changes; immutable backups still needed |

## 3.2 Itemised budget

Each package needs NGN 400,000 for independent protected backups and NGN 200,000 for training and tests.

**Table 3.2: Annual cost against the NGN 3,000,000 limit**

| **Package** | **Base (NGN)** | **+ Independent backups** | **+ Training and tests** | **Total (NGN)** | **Fits limit?**           | **Headroom (NGN)** |
|-------------|----------------|---------------------------|--------------------------|-----------------|---------------------------|--------------------|
| Cold        | 600,000        | 400,000                   | 200,000                  | 1,200,000       | Yes                       | 1,800,000          |
| Warm        | 1,500,000      | 400,000                   | 200,000                  | 2,100,000       | Yes                       | 900,000            |
| Hot         | 2,400,000      | 400,000                   | 200,000                  | 3,000,000       | Yes, exactly at the limit | 0                  |

**Recommendation: Warm, at NGN 2,100,000.** Cold fails every RTO and the 1-hour RPO, so it cannot meet business needs. Hot meets all objectives on paper but uses the whole budget with no contingency, needs staffing the Institute does not have, and still requires the immutable backups. Warm meets the RTOs in the drill, leaves NGN 900,000 for unexpected costs and test overruns, and the drill already shows how recovery would run.

**Objectives Warm cannot meet.** It cannot restore LMS01 before the 11:00 exam (11:45 modelled), so a continuity workaround is required. It meets the 1-hour RPO for F1 and F2 only if the contract and my backup design deliver it; the quoted 6-hour end of the window would not. Its 3 to 5 hour estimate means F1 and F2 could miss a 4-hour RTO.

**Table 3.3: Residual risks and acceptance**

| **Residual risk**                                                      | **Accepted by**                                                |
|------------------------------------------------------------------------|----------------------------------------------------------------|
| 11:00 exam cannot be met by restoration; depends on temporary delivery | Academic Director, endorsed by the Executive Sponsor           |
| Supplier timings or data-loss window worse than estimated              | Executive Steering Committee (until validated in a timed test) |
| Reconciliation risk on fees and payroll                                | Finance Manager and HR and Payroll Manager                     |
| Two technicians as a single point of failure                           | Executive Sponsor                                              |
| Residual flood exposure of the alternate site                          | Executive Sponsor, after a flood assessment                    |

## 3.3 Comparison with disruption costs and investment evidence

The 8-hour estimate is NGN 1,200,000 and the 24-hour estimate NGN 3,360,000 (Table 1.3). The Warm annual cost of NGN 2,100,000 is 1.75 times the 8-hour estimate and 62.5% of the 24-hour estimate. Cold (NGN 1,200,000) equals the 8-hour estimate; Hot (NGN 3,000,000) is 2.5 times the 8-hour estimate and 89% of the 24-hour estimate.

**Table 3.4: Modelled single-outage cost under each package (illustrative)**

| **Package**  | **Calculation**                                                                                                                                | **Modelled cost (NGN)**                              |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------|
| Cold         | Whole first 24 h at 135,000 plus exam trigger (first service 24 to 48 h)                                                                       | At least 3,360,000                                   |
| Warm (drill) | F1 3.75 h x 60,000 = 225,000; F2 3.5 h x 40,000 = 140,000; F3 4.25 h x 20,000 = 85,000; F4 4.25 h x 10,000 = 42,500; F5 2.5 h x 5,000 = 12,500 | 505,000 (625,000 if the exam trigger is not avoided) |
| Hot          | All functions about 1 h x 135,000 (supplier claim, unvalidated)                                                                                | 135,000                                              |

A single outage cannot establish annual return on investment. Return depends on how often outages happen, which is unknown; the rates are exercise estimates; the modelled savings assume the suppliers' timings; and non-financial harm is excluded. As an illustration only, Hot costs NGN 900,000 a year more than Warm and saves about NGN 370,000 per similar event in the model (505,000 minus 135,000), so it would pay back only if roughly 2.4 such events occurred each year (900,000 divided by 370,000). That frequency is far from established.

Before approving spending or accepting supplier claims I would require: the contract's recovery and data-loss commitments with remedies; a timed restore of Northbridge's own data at the supplier; proof of workstation capacity and flood-zone location; the supplier's independence evidence; local flood history and outage frequency; and measured downtime costs to replace the exercise rates.

# Task 4: Business continuity and disaster recovery plan

## 4.1 Scope, activation and coordination

**Scope.** The plan covers F1 to F5, the shared infrastructure (network, AUTH01, DB01, FS01), the alternate facility, and all staff and learners. It follows the plan components in the deck (ICDFA, n.d., slide 10).

**Activation thresholds.** The plan is activated when any of the following occurs: the main site is inaccessible for more than 4 hours; critical systems are unavailable for more than 60 minutes; or any function is at risk of missing a deadline within its RTO. Today meets all three (72-hour closure, 11:00 exam). The Executive Sponsor (Principal) approves activation. The authorised decision maker during the response is the Incident Commander (BC/DR Coordinator). The deputy is the Operations Manager, and the deputy to the Executive Sponsor is the Academic Director.

**Escalation, contacts and reporting.** The Incident Commander escalates to the Executive Sponsor immediately, and to the Executive Steering Committee for spending above the delegated limit or any decision that changes an exam date. Contact roles are: Incident Commander, deputy, DR Technical Lead, technicians, Incident Response Lead, function owners, Facilities Manager and Communications Lead. Because the site and its systems are unavailable, alternative communication uses Microsoft 365 mail and chat on mobile devices, a phone tree and SMS (A7). Situation reports go out at 08:30, then hourly until 14:00, then every three hours on Day 0, then twice daily. Each report gives status by function, estimated times, risks and decisions needed.

**Working together.** The continuity team (function owners) runs temporary procedures. The technical recovery team (two technicians) restores systems. Facilities manages the site, the alternate facility and clearance. The incident response team stands by and takes over any suspected compromise. A single Incident Commander directs all four, with shared reporting and joint review, as the deck's integration model describes (ICDFA, n.d., slide 14).

## 4.2 RACI matrix

Key: **R** responsible, **A** accountable (exactly one per activity), **C** consulted, **I** informed, **A/R** combined. ES Executive Sponsor; IC Incident Commander; DRL DR Technical Lead; IRL Incident Response Lead; FO function owner; FAC Facilities Manager; COM Communications Lead.

| **Activity**                                  | **ES** | **IC** | **DRL** | **IRL** | **FO** | **FAC** | **COM** |
|-----------------------------------------------|--------|--------|---------|---------|--------|---------|---------|
| 1\. Plan activation                           | A      | R      | C       | I       | I      | C       | I       |
| 2\. Temporary assessment delivery             | I      | C      | C       | C       | A/R    | C       | C       |
| 3\. Backup validation                         | I      | A      | R       | R       | I      |         |         |
| 4\. Restoration                               | I      | C      | A/R     | C       | I      |         | I       |
| 5\. Business acceptance of restored service   | I      | C      | R       | I       | A/R    |         |         |
| 6\. Stakeholder communication                 | A      | C      | I       | C       | C      | I       | R       |
| 7\. Reconciliation of temporary records       | I      | I      | C       |         | A/R    |         |         |
| 8\. Decision on incident response involvement | I      | C      | C       | A/R     | I      |         | I       |
| 9\. Return to the primary site                | A      | R      | R       | C       | C      | R       | I       |

## 4.3 Temporary procedures for F1 to F5 (72-hour outage)

The alternate facility has 25 workstations from 10:00 and 40 learners need a centre workstation. The plan therefore holds the exam in sittings. Sitting 1 starts at 11:00 for 105 learners: the 80 own-device learners and 25 workstation learners. Sitting 2 starts at 13:15 for the remaining 15 on the restored LMS01 if it has passed acceptance, or on the temporary service if not. Learners with access arrangements are placed in sitting 1 so that adjustments are met first.

| **Function** | **Temporary procedure and approvals**                                                                                                                                            | **Controls (integrity, accessibility, privacy, records)**                                                                                                                                                       | **Later reconciliation**                                                               |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| F1           | Academic Director approves sittings. Exam delivered through a controlled Microsoft 365 service from a master paper held outside LMS01 (A3, A4). Sitting 2 uses a parallel paper. | Identity checked against the enrolment list; invigilation of every room; randomised order; extra time and assistive tools per the access list; no copies on personal devices; results in a restricted register. | Results imported into LMS01 and checked against the register.                          |
| F2           | Finance Manager approves confirmations from the provider's settlement report; a second officer checks.                                                                           | Numbered manual log; no wallet edits; minimum data in logs; restricted folder.                                                                                                                                  | Match provider report, restored FIN01 and manual log before 16:00; resolve duplicates. |
| F3           | Registrar approves a restricted online form for applications and change requests.                                                                                                | Time-stamped submissions; access limited to registry staff; data deleted from the form after migration.                                                                                                         | Key into REG01 under dual control; compare counts.                                     |
| F4           | HR and Payroll Manager freezes changes except approved ones; two approvers.                                                                                                      | Compare against the last approved run; one submission with a unique batch ID.                                                                                                                                   | Compare restored HR01 with the change log before the 12:00 Day 1 bank file.            |
| F5           | Records Officer logs requests; urgent items released only after verification.                                                                                                    | Access list checked per request; no bulk file copying.                                                                                                                                                          | Process the log once FS01 passes integrity checks.                                     |

| **Category**     | **Minimum requirement**                                                                                                                                                                                                                      |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Staff            | Incident Commander and deputy; two technicians plus a third on call; Communications Lead; Facilities Manager; an owner and delegate per function; five invigilators, one exam officer and one ICT support for the exam; two finance officers |
| Equipment        | 25 workstations (15 in the stress case), up to 10 loaned laptops (A9), mobile hotspots, chargers, printed forms, secure storage for paper                                                                                                    |
| Supplier support | Alternate facility provider, mobile ISP, Microsoft 365, warm site supplier, offsite backup provider, payment provider, bank                                                                                                                  |

## 4.4 DR runbook

Restoring technology means the application or file action has finished and passed its local validation. Resuming business service means the owner has accepted it, temporary records are reconciled, and users can work under the normal controls. The runbook keeps these apart. Times are modelled (Table 5.1).

| **Step** | **Action**                                                                                              | **Owner**             | **Prerequisite** | **Completion check**                                           | **If the step fails**                                        |
|----------|---------------------------------------------------------------------------------------------------------|-----------------------|------------------|----------------------------------------------------------------|--------------------------------------------------------------|
| R1       | Activate plan; freeze changes                                                                           | IC (ES approves)      | Thresholds met   | Activation logged; roles confirmed                             | Deputy approves                                              |
| R2       | Select backups: B01 and chain to 07:00, B03, B04; record the fallback (17:00 Day -3)                    | DRL                   | R1               | Catalogue chain complete                                       | Choose last continuous point; escalate                       |
| R3       | Recovery network, 08:00 to 08:30                                                                        | Technician 1          | Site access      | Connectivity and access tests pass                             | Call supplier; use hotspots for Microsoft 365 services only  |
| R4       | AUTH01 from B03, 08:30 to 09:00                                                                         | Technician 1          | R3               | Admin sign-in; accounts match HR list                          | Earlier snapshot; rebuild minimal directory                  |
| R5       | Security and backup checks, 09:00 to 09:30                                                              | Technician 1 with IRL | R4               | Malware scan clean; chain verified; backup admin logs reviewed | Stop; invoke incident response; last valid point or fallback |
| R6       | DB01 restore, 09:30 to 11:00                                                                            | Technician 1          | R5               | Consistency check passes; last transaction 07:00               | Restore to last valid point; use fallback and reconcile      |
| R7       | FS01 restore, 09:30 to 10:30                                                                            | Technician 2          | R4, R5           | File counts, permissions and sample files correct              | Earlier full; read-only access                               |
| R8       | LMS01, 11:00 to 11:45                                                                                   | Technician 1          | R6               | Login, exam content and learner count correct                  | Continue temporary delivery                                  |
| R9       | FIN01, 11:00 to 11:30                                                                                   | Technician 2          | R6               | Ledger totals and sample receipts correct                      | Manual log continues                                         |
| R10      | HR01, 11:30 to 12:15                                                                                    | Technician 2          | R6               | 90 staff records; totals match last run                        | Last approved payroll plus changes                           |
| R11      | REG01, 11:45 to 12:15                                                                                   | Technician 1          | R6               | Application counts and samples correct                         | Form capture continues                                       |
| R12      | Business acceptance, 30 minutes each                                                                    | FO                    | R8 to R11        | Signed checklist                                               | Rollback (R14)                                               |
| R13      | Declare each function recovered                                                                         | IC                    | R12              | Recovery criteria in 2.1 met                                   | Hold declaration                                             |
| R14      | Rollback: revert to pre-acceptance snapshot or temporary procedure; keep failed image for investigation | DRL                   | Failed R12       | Service stable                                                 | Escalate to ES                                               |
| R15      | Reconcile temporary records                                                                             | FO                    | R13              | Counts and totals agree                                        | Hold dependent actions                                       |

## 4.5 Communications

**Table 4.1: Stakeholder communication matrix**

| **Stakeholder**                           | **Message and trigger**                         | **Owner**        | **Channel**                             | **Approver** | **Frequency**                      |
|-------------------------------------------|-------------------------------------------------|------------------|-----------------------------------------|--------------|------------------------------------|
| Learners                                  | Closure, exam arrangements, payment reassurance | COM              | SMS, Microsoft 365 mail, website notice | ES           | By 09:00, then every 3 h on Day 0  |
| Staff                                     | Activation, roles, locations                    | IC               | Phone tree, chat                        | IC           | Twice on Day 0, then daily         |
| Executive Steering Committee              | Status, decisions, spending                     | IC               | Phone, secure chat                      | IC           | Hourly until 14:00, then every 3 h |
| Alternate facility provider               | Workstation count and access                    | FAC              | Phone                                   | IC           | At activation and daily            |
| Payment provider and bank                 | Settlement report, payroll file                 | FO (Finance, HR) | Phone, portal                           | FO           | Daily                              |
| Offsite backup provider                   | Access and restore support                      | DRL              | Phone, portal                           | IC           | At need                            |
| Insurer and, where applicable, regulators | Notification of loss                            | ES               | Phone, email                            | ES           | At activation                      |

**Initial learner notice (09:00).**

> **Important notice for learners: Northbridge Skills Institute**
>
> Our main campus is closed because of flooding and a power failure, and we expect it to stay closed for up to 72 hours. Please do not travel to the main campus.
>
> Today's 11:00 examination will still take place. If you need a computer, report to the alternate facility, where places are limited. Otherwise bring your own device, your learner ID and a charger. We will send your sitting time by SMS and email before 10:00. If you need a reasonable adjustment, reply to this email or call the helpline on +000 000 0000 (fictional).
>
> Fee, admissions and certificate services are running through temporary procedures. Your payments are safe and will be reconciled, so please do not pay again.
>
> We will update you by 12:00 and every three hours today through SMS, email and the website notice page. Please ignore messages from unofficial senders. Thank you for your patience.

**When possible compromise involves incident response.** No attack is confirmed. The Incident Response Lead is engaged immediately if any of these appear: an unexpected backup administrator logon, a failed integrity check with no clear cause, unexplained changes in AUTH01, ransom notes or unexpected encryption, or unusual outbound data. In that case both teams run together under one Incident Commander, as in the deck's shared-activation model (ICDFA, n.d., slide 14). Restoration pauses at the failing step until the Incident Response Lead clears the backup source.

## 4.6 Safe return to the primary site

Return is allowed only when facilities clearance and business validation are both complete. Facilities clearance means a qualified inspector confirms structural, electrical and fire safety, water damage is dealt with, power is stable for 24 hours, and flood-damaged equipment is replaced or tested before it is powered on. Business validation means each function owner signs that the restored environment at the primary site works under normal controls.

New records created during the outage move back in a controlled transfer. Temporary processing is frozen at an agreed time. Records created at the alternate facility are exported, counted, and loaded under dual control. Counts in and out must match, and exceptions are resolved before closure. The alternate facility stays available for 48 hours as a fallback. The Executive Sponsor gives final sign-off after reading both the facilities clearance and the business validation reports.

# Task 5: Recovery schedule and exercise

## 5.1 Baseline recovery schedule (modelled, not a live test)

Using the drill timings and two technicians, FS01 does not depend on DB01, so Technician 2 restores it while Technician 1 restores DB01. After DB01, the four applications are shared across both technicians in business-priority order.

**Table 5.1: Baseline schedule (Day 0, WAT)**

| **Action**                 | **Technician** | **Start** | **Finish** | **Prerequisite**           |
|----------------------------|----------------|-----------|------------|----------------------------|
| Recovery network           | T1             | 08:00     | 08:30      | None                       |
| AUTH01 restoration         | T1             | 08:30     | 09:00      | Recovery network           |
| Security and backup checks | T1             | 09:00     | 09:30      | AUTH01 restoration         |
| DB01 restoration           | T1             | 09:30     | 11:00      | Security and backup checks |
| FS01 restoration           | T2             | 09:30     | 10:30      | AUTH01 and checks          |
| LMS01 restoration          | T1             | 11:00     | 11:45      | DB01 restoration           |
| FIN01 restoration          | T2             | 11:00     | 11:30      | DB01 restoration           |
| HR01 restoration           | T2             | 11:30     | 12:15      | DB01 restoration           |
| REG01 restoration          | T1             | 11:45     | 12:15      | DB01 restoration           |

The **earliest restoration of assessment service is 11:45**. The controlling dependency path is Recovery network (30) then AUTH01 (30) then checks (30) then DB01 (90) then LMS01 (45), a total of 225 minutes. No other action lies on it, and a second technician cannot shorten it because each step needs the one before. All five functions are restored by **12:15 (255 minutes)**. This is the earliest possible: after DB01 at 11:00, 150 minutes of work remain across two technicians, so at least 75 minutes are needed, and the split LMS01 plus REG01 against FIN01 plus HR01 achieves it exactly.

## 5.2 Elapsed time against objectives (modelled baseline)

**Table 5.2: Elapsed recovery against RTO, MTD and RPO**

| **Function**        | **Finish** | **Elapsed** | **RTO** | **RTO met?**   | **MTD** | **Margin to MTD** | **Checkpoint age** | **RPO** | **RPO met?**   |
|---------------------|------------|-------------|---------|----------------|---------|-------------------|--------------------|---------|----------------|
| **F1**              | 11:45      | 3 h 45 m    | 4 h     | Yes (15 min)   | 8 h     | 4 h 15 m          | 1 h (DB01)         | 1 h     | Yes, no margin |
| **F2**              | 11:30      | 3 h 30 m    | 4 h     | Yes (30 min)   | 8 h     | 4 h 30 m          | 1 h (DB01)         | 1 h     | Yes, no margin |
| **F3**              | 12:15      | 4 h 15 m    | 6 h     | Yes (1 h 45 m) | 10 h    | 5 h 45 m          | 1 h (DB01)         | 4 h     | Yes            |
| **F4**              | 12:15      | 4 h 15 m    | 8 h     | Yes (3 h 45 m) | 24 h    | 19 h 45 m         | 1 h (DB01)         | 4 h     | Yes            |
| **F5**              | 10:30      | 2 h 30 m    | 12 h    | Yes (9 h 30 m) | 36 h    | 33 h 30 m         | 9 h (FS01)         | 24 h    | Yes            |
| **AUTH01 (shared)** | 09:00      | 1 h         | n/a     | n/a            | n/a     | n/a               | 8 h                | 4 h     | No (4 h gap)   |

The **11:00 exam** needs a continuity workaround even though restoration succeeds, because LMS01 finishes at 11:45 and acceptance follows. F2 and F3 meet their deadlines (16:00 and 18:00), but each needs reconciliation of work done after the last checkpoint. F4 and F5 meet theirs. The AUTH01 gap affects every function and is the main baseline weakness.

The margins are thin where they matter most. F1 has 15 minutes of spare time against its RTO and F2 has 30, and the DB01 data-loss window equals the F1 and F2 RPO exactly, so any delay or missing increment breaks an objective. These results show that the targets are achievable only in the baseline. They are modelled commitments to be tested, not evidence of live performance, and the stress case below shows how quickly the margin disappears.

## 5.3 Tabletop exercise (simulation)

> **SIMULATION.** This 45-minute walkthrough was done on paper with one person playing each role in turn. Results are simulated. The injects are applied to the plan as what-if events and are not tied to clock times in the baseline schedule.

**Table 5.3: Exercise design**

| **Element**         | **Design**                                                                                                                                                                                                                                     |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Duration and format | 45 minutes, discussion-based tabletop (ICDFA, n.d., slide 11)                                                                                                                                                                                  |
| Objectives          | Test the exam plan, the recovery path and the reconciliation process under stress                                                                                                                                                              |
| Roles simulated     | Incident Commander, Executive Sponsor, DR Technical Lead, Incident Response Lead, Academic Director, Finance Manager, Facilities Manager, Communications Lead                                                                                  |
| Agenda              | Minutes 0 to 5 briefing; 5 to 10 baseline actions; injects at 10, 20, 30 and 40; 40 to 45 debrief                                                                                                                                              |
| Success criteria    | A decision, owner and revised action logged for every inject within 4 minutes; all 120 candidates placed in a sitting; effect on RTO and RPO quantified for each inject; incident response engaged within 5 minutes of the administrator logon |

**Table 5.4: Exercise log (stress scenario, separate from the baseline)**

| **Minute** | **Inject**                                                                          | **Decision and revised action**                                                                                                                                                                                                                                                     | **Owner**                                      | **Rationale**                                                                                    | **Effect on service or recovery**                                                                                                                       |
|------------|-------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| 10         | Closure extends from 72 hours to five days                                          | Extend the alternate facility booking to five days; ask the supplier for a third technician and set a 12-hour rota; set an end date for temporary procedures; update learners                                                                                                       | Incident Commander with Facilities Manager     | Longer manual operation raises error and fatigue risk                                            | Recovery objectives unchanged; continuity duration rises from 72 to 120 hours; return to the primary site moves to Day 5 or later                       |
| 20         | The alternate facility has only 15 working workstations                             | Sitting 1: 80 own-device plus 15 workstation learners (95). Sitting 2 at 13:15: 15 learners. Sitting 3 at 15:30: remaining 10, or loaned laptops to bring them forward. Access-arrangement learners go first.                                                                       | Academic Director                              | Keeps all 120 in a controlled sitting plan on the same day                                       | Exam finishes later; partial rescheduling cost possible for the last 10 if they slip to the next day; more invigilation needed                          |
| 30         | A DB01 incremental fails its integrity check; unexpected backup administrator logon | Stop restoring from the failed chain. Engage the Incident Response Lead; suspend and rotate the shared administrator credential; restore DB01 only to the last valid point; use the 17:00 Day -3 backup as fallback if the source cannot be cleared. Record the data-loss increase. | Incident Commander with Incident Response Lead | Possible compromise; restoring unverified data could spread it                                   | Adds 30 minutes of re-verification; LMS01 finishes at 12:15, breaching the 4-hour RTO by 15 minutes; RPO worsens by the gap to the last valid increment |
| 40         | Provider reports successful payments absent from restored finance records           | Treat the provider's settlement report as authoritative; do not re-run payments; hold wallet updates; create a dual-control reconciliation batch; check whether any record was altered; send the 16:00 confirmation from the verified list                                          | Finance Manager                                | Payments after 07:00 are expected to be missing, but unexplained gaps may signal changed records | F2 acceptance later; 16:00 deadline still met if reconciliation ends by about 15:00; incident response consulted                                        |

The exercise shows four lessons. First, the examination plan must be built in sittings, because neither 25 nor 15 workstations can seat 40 learners at once. Second, a failed increment is both a recovery problem and a possible security event, so incident response must join the decision at once. Third, the provider's records, not the restored local records, are the reliable reference for fees. Fourth, a longer closure strains people before it strains systems, so a third technician matters. The stress case is kept apart from the baseline schedule, and Table 5.5 shows its modelled effect.

**Table 5.5: Stress schedule for the minute-30 inject (modelled)**

| **Action**                                                     | **Technician** | **Start** | **Finish** | **Note**                                   |
|----------------------------------------------------------------|----------------|-----------|------------|--------------------------------------------|
| Recovery network, AUTH01, checks                               | T1             | 08:00     | 09:30      | Check fails at 09:30                       |
| Chain re-verification and clearance (added 30 min, assumption) | T1             | 09:30     | 10:00      | Incident response involved                 |
| DB01 to last valid point                                       | T1             | 10:00     | 11:30      | Delayed by 30 min                          |
| FS01                                                           | T2             | 10:00     | 11:00      | Held until the repository is cleared       |
| LMS01                                                          | T1             | 11:30     | 12:15      | 4 h 15 m: RTO missed by 15 min, within MTD |
| FIN01                                                          | T2             | 11:30     | 12:00      | 4 h: RTO met with no margin                |
| REG01                                                          | T1             | 12:15     | 12:45      | 4 h 45 m                                   |
| HR01                                                           | T2             | 12:00     | 12:45      | 4 h 45 m                                   |

## 5.4 Corrective actions

| **\#** | **Corrective action**                                                                        | **Priority** | **Owner**          | **Target date**         | **Measurable completion evidence**                                     |
|--------|----------------------------------------------------------------------------------------------|--------------|--------------------|-------------------------|------------------------------------------------------------------------|
| CA1    | Separate backup and production administrator identities, enforce MFA and two-person deletion | High         | DR Technical Lead  | Plan approval + 14 days | Access report; test shows production credentials cannot delete backups |
| CA2    | Obtain and review the offsite supplier's access-independence evidence; add contract clauses  | High         | Operations Manager | \+ 30 days              | Signed evidence and contract change                                    |
| CA3    | Move AUTH01 to 4-hourly snapshots; DB01 to 30-minute incrementals with a 03:00 differential  | High         | DR Technical Lead  | \+ 21 days              | Catalogue shows at least 95% of jobs on schedule for 14 days           |
| CA4    | Test the sitting-based exam plan, including parallel papers and the 15-workstation case      | High         | Academic Director  | \+ 45 days              | Mock exam with at least 25 learners; integrity checklist signed        |
| CA5    | Write and test a payment reconciliation procedure using the provider's report                | Medium       | Finance Manager    | \+ 30 days              | Test reconciles 100% of sample transactions                            |
| CA6    | Arrange a third technician or on-call supplier and a 12-hour rota                            | Medium       | Operations Manager | \+ 60 days              | Signed agreement; rota published                                       |
| CA7    | Contract the alternate facility for 25 workstations with a 5-day option and a pre-check      | Medium       | Facilities Manager | \+ 30 days              | Contract clause; a count test passed                                   |
| CA8    | Run a full DB01 chain restore test in isolation                                              | High         | DR Technical Lead  | \+ 45 days              | Test log with timing and integrity results                             |

# Task 6: Governance and performance measures

## 6.1 Ownership, testing and maintenance

The Executive Sponsor (Principal) approves the plan, and the BC/DR Coordinator owns it and reports to the Executive Steering Committee, which meets quarterly (ICDFA, n.d., slide 12). The plan is reviewed at least annually, as the deck and NIST require (NIST, 2010; ICDFA, n.d., slide 4). It is also reviewed after any test failure, major incident, supplier change, system change, or change in flood risk. Version control uses a numbered version, change log and named approver in a controlled Microsoft 365 library. Distribution goes to a named list, with an offline copy on role holders' phones and at the alternate facility. Training covers every role holder on appointment and yearly.

**Table 6.1: Risk-based testing calendar (year one)**

| **Quarter** | **Test**                                                                   | **Frequency basis** | **Focus**                                    |
|-------------|----------------------------------------------------------------------------|---------------------|----------------------------------------------|
| Q1          | Tabletop and file restore test                                             | Quarterly           | Roles, communication, the exam sittings      |
| Q2          | Walkthrough and parallel restore at the warm site, before the rainy season | Semi-annual; annual | Runbook, timings, DB01 chain                 |
| Q3          | Tabletop and file restore                                                  | Quarterly           | Compromise and incident response involvement |
| Q4          | Simulation exercise and walkthrough                                        | Annual; semi-annual | Time pressure; reconciliation                |

A full interruption test is not planned in year one because of its risk (ICDFA, n.d., slide 11); I would review it for year three.

**Flood and climate consideration.** The scenario is a flood, so the plan treats flooding as a recurring hazard. I would assess the flood exposure of the main site, the alternate facility and the backup region, so that none shares the same flood plain, raise critical equipment off the floor, check drainage and backup power, and test before the rainy season. ISO 22301:2019 Amendment 1 (2024) adds climate action changes to the standard, which supports treating climate as part of the organisation's context (ISO, 2024). If flood frequency rises, I would re-examine the 72-hour estimate and the annual cost case.

## 6.2 Framework mapping (educational alignment, no certification claimed)

| **NIST SP 800-34 step (slide 4)**   | **Output in this report**                                  |
|-------------------------------------|------------------------------------------------------------|
| 1\. Policy statement                | Ownership and approval in 6.1; scope and activation in 4.1 |
| 2\. Business impact analysis        | Task 1 BIA and calculations                                |
| 3\. Preventive controls             | Backup separation, immutable copy, 3-2-1 design (2.3)      |
| 4\. Contingency strategies          | Option comparison and budget (Task 3)                      |
| 5\. Contingency plan                | BC/DR plan, RACI, runbook (Task 4)                         |
| 6\. Testing, training and exercises | Tabletop, testing calendar (5.3, 6.1)                      |
| 7\. Plan maintenance                | Review triggers, version control (6.1)                     |

| **ISO 22301 theme (slide 9)** | **Output in this report**                     |
|-------------------------------|-----------------------------------------------|
| Clause 4 Context              | Scope, flood and climate consideration (6.1)  |
| Clause 5 Leadership           | Executive Sponsor approval, roles (4.1, 6.1)  |
| Clause 6 Planning             | RTO, RPO and MTD matrix (2.1)                 |
| Clause 7 Support              | Resources, training, communication (4.3, 4.5) |
| Clause 8 Operation            | BIA, strategy, plans, runbook (Tasks 1 to 4)  |
| Clause 9 Performance          | KPIs and dashboard (6.3)                      |
| Clause 10 Improvement         | Corrective actions (5.4)                      |

## 6.3 KPIs, dashboard and maturity

**Table 6.2: Key performance indicators**

| **KPI**                      | **Formula or method**                                                                                | **Data source**             | **Target**                        | **Frequency**               | **Owner**              |
|------------------------------|------------------------------------------------------------------------------------------------------|-----------------------------|-----------------------------------|-----------------------------|------------------------|
| K1 Recovery within RTO       | Functions restored within RTO divided by functions tested, x 100                                     | Test and drill logs         | 100% for T1; at least 90% overall | Each test; quarterly report | DR Technical Lead      |
| K2 Recovery within RPO       | Systems whose restored checkpoint age is within RPO divided by systems tested, x 100                 | Backup catalogue, test logs | 100%                              | Each test; quarterly        | DR Technical Lead      |
| K3 Restoration-test coverage | Critical systems with a successful full restore test in 12 months divided by critical systems, x 100 | Test register               | 100% within 12 months             | Quarterly                   | BC/DR Coordinator      |
| K4 Corrective-action closure | Actions closed by due date divided by actions due, x 100                                             | Action tracker              | At least 90%                      | Monthly                     | BC/DR Coordinator      |
| K5 Training                  | Role holders trained in the last 12 months divided by role holders, x 100                            | Training records            | At least 95%                      | Quarterly                   | HR and Payroll Manager |

**Table 6.3: Performance dashboard (baseline)**

| **KPI** | **Current value**                                        | **Basis**                                        | **Target** | **Status**  |
|---------|----------------------------------------------------------|--------------------------------------------------|------------|-------------|
| K1      | 5 of 5 functions (100%); 4 of 5 (80%) in the stress case | Modelled; F1 breaches RTO by 15 min under stress | 100% T1    | Amber       |
| K2      | 2 of 3 systems (67%)                                     | DB01 and FS01 meet RPO; AUTH01 misses by 4 h     | 100%       | Red         |
| K3      | 0% verified                                              | No live restore evidence supplied                | 100%       | Red         |
| K4      | 0 of 8 closed                                            | Actions just raised, none due                    | 90%        | Not yet due |
| K5      | 0%                                                       | Plan not yet issued                              | 95%        | Red         |

**Maturity.** Using the five-level model in the deck (ICDFA, n.d., slide 13), I assess Northbridge at **Level 2: Developing (Repeatable)**. Evidence: backups exist and a recovery drill has been timed, which suggests basic plans for critical systems, but restoration is not shown to be tested regularly, administration is shared, the supplier's controls are undocumented, and there is no governance structure yet. A realistic 12-month target is **Level 3: Defined (Standardized)**: a documented plan, regular testing on the calendar above, clear governance and consistent recovery. Level 4 would need measured data over several test cycles, which one year cannot supply.

# References

- ICDFA. (n.d.). *Business Continuity and Disaster Recovery*. GRC205, Week 21. Teaching deck, slides 1 to 15. International Cybersecurity and Digital Forensics Academy.

- NIST. (2010). *SP 800-34 Revision 1: Contingency Planning Guide for Federal Information Systems* (updated 11 November 2010). National Institute of Standards and Technology. [https://csrc.nist.gov/pubs/sp/800/34/r1/upd1/final](https://csrc.nist.gov/pubs/sp/800/34/r1/upd1/final)

- ISO. (2019). *ISO 22301:2019 Security and resilience: Business continuity management systems: Requirements*. International Organization for Standardization. [https://www.iso.org/standard/75106.html](https://www.iso.org/standard/75106.html)

- ISO. (2024). *ISO 22301:2019/Amd 1:2024 Climate action changes*. International Organization for Standardization. [https://www.iso.org/standard/88412.html](https://www.iso.org/standard/88412.html)

| **Source**        | **Decisions it supports**                                                                                                                                                                                        |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ICDFA deck (n.d.) | BIA phases, tiers and objective definitions (slides 5 and 6); site options and backups (7 and 8); plan components, testing and governance (10 to 12); maturity and KPIs (13); incident response integration (14) |
| NIST (2010)       | Seven-step structure used for the plan, testing and maintenance mapping (6.2)                                                                                                                                    |
| ISO (2019, 2024)  | Educational mapping of outputs to clauses 4 to 10 and the climate consideration (6.1, 6.2)                                                                                                                       |

# Appendix: AI declaration

- **Tool used:** Claude (Anthropic), a generative AI assistant.

- **How it was used:** To help structure the report, draft calculations, tables and text from the assessment brief and teaching slides, and format the document.

- **Responsibility:** I have reviewed and written this document, I take responsibility for its content, and I can explain any decision in it if asked.

- **Data:** All organisations, people, figures and contact details are fictional.
