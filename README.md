# GRC 205 Incident Response Planning and Ransomware Response Simulation

**Individual assignment (20% of final grade, marked out of 100): Northbridge Skills Institute, suspected ransomware incident (fictional training scenario)**

| Item | Detail |
| --- | --- |
| Programme | Governance, Compliance, and Risk Training Program, ICDFA |
| Course | GRC 205 Incident Response Planning and Ransomware Response Simulation (the assessment brief and teaching deck label the module GRC105, Week 20) |
| Student name | [YOUR FULL NAME] |
| Student ID | [YOUR STUDENT ID] |
| Official email | [YOUR OFFICIAL EMAIL] |
| Word count | approx. 8,020 words (excluding the reference list) |
| Role taken | Incident Response Lead, Northbridge Skills Institute |
| Time reference | All times are West Africa Time (WAT). Day 0 is a Monday; control of the incident is taken at 08:45. |

> **Data note.** All organisations, names, numbers and contact details in this document are fictional. Evidence collection is described as planned; no acquisitions, hashes or results are claimed unless explicitly labelled as simulation.

## Assumptions

The scenario leaves some facts open. These assumptions are labelled A1 to A8 and are referred to in the decisions below.

| ID | Assumption | Why it matters |
| --- | --- | --- |
| A1 | FS01 and LMS01 are virtual machines, so snapshots and isolated restores are possible. | Enables evidence capture and sandbox restore testing. |
| A2 | EDR on FIN-PC07 supports network containment and remote live response. | Allows isolation without powering the host off. |
| A3 | No incident response retainer exists, but an external forensic firm and legal counsel can be engaged within two hours through the cyber insurer's panel or a direct call. | Defines how a three-person IT team gets surge support. |
| A4 | The Friday 17:00 disconnected backup (Day minus 3) is physically offline and was not mounted on the network after it was taken. | It is the only backup route not exposed to svc_backup. |
| A5 | The master copy of the examination paper and learner enrolment list is held by the Academic Office outside FS01 (for example, on a controlled USB or an approved printed copy). | Needed for any alternative or rescheduled examination. |
| A6 | The Institute's jurisdiction is not stated in the scenario. The legal adviser will confirm it and the notification rules that apply. No legal deadline is asserted in this document. | Avoids guessing legal obligations. |
| A7 | The finance officer is available by phone and willing to be interviewed. | Needed to resolve E01 and E02. |
| A8 | Staff mobile numbers are held on personal phones and in HR paper files. | E08 says staff phones are available. |

## How the tasks map to the four teaching stages

| Teaching stage (ICDFA, n.d., slide 6) | Where it appears in this document |
| --- | --- |
| 1. Preparation | Task 2 (policy, CSIRT, RACI, preparation improvements); communication templates in Task 4 |
| 2. Detection and analysis | Task 1 (timeline, classification, severity, investigation questions) |
| 3. Containment, eradication and recovery | Task 3 (playbook, evidence, eradication, restoration order and acceptance criteria) |
| 4. Post-incident activity | Task 5 (tabletop, improvement plan, post-incident review) and Task 6 (case lessons) |

Communication (Task 4) runs through all four stages. Lessons from stage 4 feed back into preparation (ICDFA, n.d., slide 13).

---

# Task 1: Incident classification and initial assessment

## 1.1 Evidence-linked timeline

Classification of each entry: **Confirmed** (directly observed in a record or report), **Hypothesis** (working explanation to test), **Unresolved** (open question).

| ID | Time (WAT) | Confirmed observation | Working hypothesis | Unresolved question |
| --- | --- | --- | --- | --- |
| E01 | Day -1 16:40 | A finance officer reported opening an unexpected payroll attachment on FIN-PC07 (user report and mail record). | The attachment was a phishing payload and the likely initial access point. | Did malicious code actually run? What was the attachment hash and sender? |
| E02 | Day 0 01:15 | Microsoft 365 audit shows a sign-in to the finance officer's account from an unfamiliar IP and a new device. | Credentials or a session token were stolen from FIN-PC07 and reused by the attacker. | Was MFA satisfied, bypassed or absent? Was the user involved? |
| E03 | Day 0 01:35 | svc_backup made remote administrator logons to FS01 and BAK01 outside its normal schedule. | The attacker obtained svc_backup credentials and used them for lateral movement toward the file server and backups. | Who initiated the logons, from which source host, and what was done on arrival? |
| E04 | Day 0 01:50 | Proxy record shows FIN-PC07 uploaded 1.2 GB over HTTPS to unfamiliar cloud storage. | Data was staged and exfiltrated before encryption (double extortion pattern). | What was in the upload? Was it a legitimate sync? Did it include learner or finance data? |
| E05 | Day 0 02:00 | Backup console reports success for FS01 and LMS01 jobs. | Jobs ran, but the backup may contain encrypted, tampered or compromised data. | Are the backup sets restorable and clean? Were any restore points deleted or altered? |
| E06 | Day 0 08:30 | EDR and helpdesk report mass file changes on FIN-PC07. FS01 shares hold .locked files and a ransom note. | Ransomware is encrypting FIN-PC07 and FS01. | Malware family, whether encryption is still spreading, whether other hosts are affected. |
| E07 | Day 0 08:40 | The portal login page loads, but learners cannot open materials or submit assessments (helpdesk). | LMS01 is degraded because its dependent files on FS01 are encrypted or unavailable. | Is LMS01 itself compromised or encrypted, or only starved of FS01 content? |
| E08 | Day 0 08:45 | The incident contact list on FS01 is inaccessible; staff phones work; email trust is unverified. | The attacker's access may extend to Microsoft 365, so email may be monitored. | Has the attacker read mail or created forwarding rules? |

## 1.2 Alternative explanations for ambiguous events

| Event | Leading explanation | Alternative explanation 1 | Alternative explanation 2 | How to discriminate |
| --- | --- | --- | --- | --- |
| E02 unfamiliar sign-in | Attacker used stolen credentials or a stolen session token. | The officer used a personal phone or a travel network to check email at 01:15 (unlikely, but unverified). | A legitimate device enrolment or conditional-access change by IT. | Interview the officer (A7); check device ID, token type, MFA method and IP reputation in sign-in logs. |
| E03 svc_backup logons | Attacker lateral movement with stolen service credentials. | A scheduled maintenance or vendor task run outside its normal window. | A new or modified backup job configured by IT staff the previous evening. | Check change records, source host of the logon, logon type and commands run. |
| E04 1.2 GB upload | Data exfiltration before encryption. | Legitimate cloud sync or backup tool on FIN-PC07 uploading finance files. | A staged attacker transfer of tooling or archive data rather than stolen content. | Check destination reputation, process that made the connection (EDR), and files read before upload. |
| E05 backup success | Jobs ran, content unverified. | Job reports were tampered with, so success is false. | Jobs backed up already-encrypted data, so success is technically true but unusable. | Mount a restore point in an isolated sandbox and test integrity. |

## 1.3 Incident type and severity

**Incident type:** Suspected human-operated ransomware with probable data exfiltration, privileged service-account compromise and possible identity-platform compromise.

### Classification record

The teaching deck requires every validated incident to be classified by incident type, attack vector, affected systems and scope of impact, then given a priority tier (ICDFA, n.d., slide 10). Ransomware and data breaches are both listed as Critical examples (ICDFA, n.d., slide 4).

| Field | Classification |
| --- | --- |
| Incident type | Ransomware with suspected data exfiltration and privileged account compromise |
| Attack vector | Phishing email with a malicious attachment (suspected, E01), followed by stolen credentials or session token (E02) |
| Affected systems | FIN-PC07, FS01 (confirmed); LMS01, BAK01, M365 account (potential) |
| Scope of impact | Learner services and examination for 100 learners, finance and admissions data, backup recoverability |
| Priority tier | Critical |

### Severity criteria

| Level | Criteria |
| --- | --- |
| Critical | Core service for a time-critical activity is down or at imminent risk, and either sensitive data exposure is confirmed or probable at scale, or recovery routes (backups) are in doubt, and no adequate workaround exists. Immediate full-team response, executive notification and external escalation required (matches ICDFA, n.d., slide 10). |
| High | A major service is disrupted or encrypted, or sensitive data exposure is possible, with a workaround that is partial. Rapid response within a defined service level; business owner and senior management informed within 30 minutes. |
| Medium | A limited set of non-critical systems is affected, no confirmed sensitive data exposure, and backups and workarounds are available. Standard workflow, documented and tracked in the ticketing system. The teaching deck groups Medium and Low in one tier; they are separated here as the brief asks for four levels. |
| Low | A contained event, such as a blocked malware execution on one endpoint with no spread or data loss. Logged and handled in routine operations. |

### Selected level: Critical (initial)

Justification, using operational impact, affected data and uncertainty:

- **Operational impact:** FS01 shares are encrypted (E06) and the portal is degraded (E07) about two hours fifteen minutes before an examination for 100 learners. Staff cannot reach the contact list (E08).
- **Affected data:** FS01 holds admissions documents, assessment records and finance files. A 1.2 GB upload to unfamiliar storage (E04) makes data theft plausible, though not proven.
- **Uncertainty:** Backup recoverability and cleanliness are unproven (E05), BAK01 may have been accessed by svc_backup (E03), and no restoration test has been run in 90 days. The attacker's reach into Microsoft 365 is unknown (E02, E08).

I choose Critical because the combination of active encryption, a hard deadline, and doubt over the recovery route means a wrong decision could cause lasting loss. I will review the rating at 10:15 and downgrade to High only if the findings in 1.6 show that BAK01 and LMS01 are clean and the upload did not contain personal data.

## 1.4 Affected and potentially affected assets

| Asset | Status | Basis |
| --- | --- | --- |
| FIN-PC07 | Confirmed affected | E01, E04, E06 |
| FS01 | Confirmed affected | E06 |
| Finance officer M365 account | Probably compromised | E02 |
| svc_backup account | Probably compromised | E03 |
| BAK01 | Potentially affected | E03 |
| LMS01 | Potentially affected; availability confirmed degraded | E05, E07 |
| Microsoft 365 tenant and email | Potentially affected | E02, E08 |
| Learner, admissions, assessment and finance data | Potentially exposed or encrypted | E04, E06 |

## 1.5 Confidentiality, integrity and availability

| Property | Assessment | Evidence |
| --- | --- | --- |
| Confidentiality | **Potentially high impact, not confirmed.** The upload and ransom note suggest theft, but contents are unknown. | E04, E06 |
| Integrity | **Confirmed loss on FS01 files** (encrypted). Unknown for LMS01, BAK01 and the M365 mailbox. | E05, E06, E07 |
| Availability | **Confirmed loss** of FS01 shares, FIN-PC07 and normal portal function. The contact list is unavailable. | E06, E07, E08 |

## 1.6 Response priorities

1. **Stop spread and protect recovery routes** (0 to 30 minutes): isolate FIN-PC07 and FS01, suspend svc_backup, and disconnect BAK01. This addresses E03, E05 and E06.
2. **Secure identity and stop exfiltration:** revoke the finance officer's sessions, block the cloud-storage destination, and review M365 for persistence (E02, E04).
3. **Preserve high-value evidence** that would be lost by action: volatile memory, EDR telemetry and logs.
4. **Make the examination decision by 10:15** with a safe default of postponement, since the portal depends on FS01 and backup safety is uncertain (E07).
5. **Establish a trusted communication channel** because email trust is unverified and the contact list is lost (E08).
6. **Scope the compromise** before any restoration begins.

## 1.7 Priority investigation questions

| # | Question | Resolving check | Findings that would change severity or scope |
| --- | --- | --- | --- |
| Q1 | Did the payroll attachment execute on FIN-PC07, and how did the attacker reach the M365 account (E01, E02)? | EDR process tree and file hash; message trace; M365 sign-in logs with MFA and token details; interview with the officer. | If a token was stolen and mailbox access occurred, scope widens to the whole tenant and notification risk rises. If MFA was satisfied from the officer's own device, a user-assisted compromise is likely. |
| Q2 | What was in the 1.2 GB upload (E04)? | Proxy and DNS logs; destination reputation; EDR file-read events before 01:50; FS01 file audit logs; provider abuse contact. | If it contained learner, admissions or finance data, confidentiality becomes confirmed and legal assessment is triggered. If it was a legitimate sync, severity may fall. |
| Q3 | Is any backup clean and restorable, and did the attacker touch BAK01 (E03, E05)? | BAK01 security logs for svc_backup sessions; deletion or modification of restore points; sandbox restore test of Friday 17:00 backup and the latest network backup. | If BAK01 was altered, only the Friday disconnected backup is trusted and recovery time and data loss increase. If clean, recovery is faster. |
| Q4 | Is LMS01 compromised, or only affected by missing FS01 files (E07)? | LMS01 logs, file integrity check, snapshot review, web shell and scheduled task search. | If LMS01 is compromised, it must be rebuilt, delaying the portal. If not, restoration of FS01 content may be enough. |
| Q5 | Has the attacker established persistence beyond FIN-PC07 and FS01? | EDR and IOC sweep, new admin accounts, scheduled tasks, M365 forwarding rules and OAuth app consents. | Any additional host or persistence mechanism widens the scope and extends eradication. |

---

# Task 2: Incident response plan and team responsibilities

## 2.1 Policy and plan outline

| Element | Content |
| --- | --- |
| Mission | Protect learners, staff and data; restore safe delivery of teaching and assessment; preserve evidence and trust. |
| Objectives | Detect and declare quickly; contain with minimal loss; preserve evidence; recover from verified clean sources; communicate accurately; learn and improve. |
| Structure and reporting hierarchy | The Incident Manager leads day-to-day response and reports to the Executive Sponsor. CSIRT composition is in 2.2. Authority levels are set out under authority to isolate and escalation below. The plan integrates with IT operations and the business units (Academic Office, Finance) through the Business Owner. |
| Scope | All Institute systems (including FIN-PC07, FS01, LMS01, BAK01), Microsoft 365, staff, contractors and service providers handling Institute data. |
| Incident declaration | Any staff member can report. The Security Analyst triages. The Incident Manager declares a Security Incident when two or more indicators are corroborated, or when encryption, confirmed data loss, privileged account misuse or service loss is observed. Severity is set using the Task 1 criteria. |
| Activation and escalation | Medium: Incident Manager and Security Analyst. High: add Business Owner and IT Operations within 30 minutes. Critical: Executive Sponsor within 15 minutes, legal adviser and external forensic support activated within 60 minutes, and the CSIRT convened by phone. Escalate a level if scope grows, backups are in doubt, personal data exposure becomes probable, or a hard business deadline is at risk. |
| Authority to isolate | The Incident Manager, Security Analyst and IT Operations Lead are pre-authorised to isolate any endpoint, server account or network segment where there is active encryption, credential misuse or exfiltration. Isolation of a system critical to a live examination is reported to the Business Owner immediately, not before. |
| Documentation | Record detection time, initial indicators, affected systems, actions taken and all evidence collected (ICDFA, n.d., slide 10). Maintain a decision log with time, decision, owner and rationale; evidence register with chain of custody; action tracker; communication log with timestamps of notifications sent; and an impact assessment (operational, financial, reputational) (ICDFA, n.d., slide 13). All times in WAT. Stored outside FS01. |
| Plan review | Annually, after every Critical or High incident, after every tabletop exercise, and after major infrastructure change. The Incident Manager owns the review and the Executive Sponsor approves. |
| Resource requirements | Tools: EDR on all servers, central logging, ticketing, forensic tools and a jump kit with secure communication (ICDFA, n.d., slide 7). Personnel: the CSIRT in 2.2 with a 24/7 on-call rotation. Budget: a pre-approved emergency limit held by the Executive Sponsor. Training: role-specific training and exercises (2.4). External contracts: forensic firm, legal counsel, insurer-panel responder. |
| Link to business continuity | The plan uses the business impact analysis: examinations and learner records are critical. Declaration of a Critical incident also invokes business continuity measures (alternative assessment arrangements, manual learner communication, and use of the recovery objectives in Task 3). Recovery priorities follow business criticality, not technical convenience. |

The outline covers the four core plan components in the teaching deck: mission, objectives and structure; roles, responsibilities and escalation; communication protocols; and resource requirements (ICDFA, n.d., slide 14). The document follows the four teaching stages (preparation; detection and analysis; containment, eradication and recovery; post-incident activity) (ICDFA, n.d., slide 6). Where I use NIST SP 800-61 Revision 3, I follow its CSF 2.0 alignment: Detect, Respond and Recover form incident response, and Govern, Identify and Protect support preparation (NIST, 2025).

## 2.2 CSIRT structure

| Role | Assigned to (fictional) | Main responsibilities |
| --- | --- | --- |
| Incident Manager (IM) | IT Manager (A. Okoye) | Runs the response, keeps the decision log, sets cadence, recommends severity. |
| Security Analyst (SA) | Security Analyst (B. Danjuma) | Triage, log analysis, IOC hunting, scope assessment. |
| Forensics Lead (FL) | Security Analyst, supported by external forensic firm | Evidence acquisition and custody, root cause analysis. |
| IT Operations Lead (ITO) | Senior systems administrator (C. Bello) | Isolation, account resets, rebuild, restoration. |
| Business Owner (BO) | Academic Director (D. Eze) | Decides on examination delivery, accepts service return. |
| Legal and Privacy Adviser (LPA) | External counsel and Data Protection Officer | Reporting obligations, privilege, regulator and law-enforcement contact, contracts and insurer. |
| Communications Lead (CL) | Communications Manager (E. Musa) | Messages to learners, staff and media, holding statements. |
| Executive Sponsor (ES) | Principal (F. Adamu) | Resourcing, approval of major or external communications, closure. |
| Scribe and Evidence Custodian | Administrative officer, trained | Decision log, evidence register entries, custody forms. |

**Obtaining additional support:** With three IT staff and one analyst, the Institute will use (per A3) an external forensic firm, a managed detection provider or insurer-panel responder, external legal counsel, and the cloud and backup vendors' support lines. Requests are authorised by the Executive Sponsor, who holds a pre-approved emergency spending limit. A mutual-aid arrangement with a partner institute can supply temporary staff and equipment. Staff relief is planned so that no responder works more than 12 hours without a handover. A 24/7 on-call rotation and escalation procedure is part of preparation (ICDFA, n.d., slide 7). External stakeholders include legal counsel, law enforcement, and information-sharing groups (ISACs) and external incident response firms (ICDFA, n.d., slide 15); contact with law enforcement and any sector sharing goes through the Legal and Privacy Adviser.

## 2.3 RACI matrix

Key: **R** Responsible, **A** Accountable (exactly one per activity), **C** Consulted, **I** Informed, **A/R** combined, blank = not involved. Role codes as in 2.2.

| Activity | IM | SA | FL | ITO | BO | LPA | CL | ES |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1. Detection and triage | A | R |  | C | I |  |  | I |
| 2. Analysis and scoping | A | R | R | C | I | C |  | I |
| 3. Containment and isolation | A | R | C | R | C | I | I | I |
| 4. Evidence handling and custody | I | R | A/R | C |  | C |  |  |
| 5. Eradication and credential reset | A | R | C | R | I |  |  | I |
| 6. Stakeholder communication | C | C |  | I | C | C | R | A |
| 7. Assessment of reporting obligations | C | C | C |  | C | A/R | I | I |
| 8. Examination continuity decision | C | I |  | C | A/R | I | I | I |
| 9. Restoration approval | R | C | C | R | A | I | I | I |
| 10. Closure and post-incident review | R | C | C | C | C | C | C | A |

## 2.4 Preparation improvements and readiness checks

| # | Improvement | Owner | Resources needed | Readiness check |
| --- | --- | --- | --- | --- |
| P1 | **Backup readiness:** immutable or offline backup copy of FS01, LMS01 and Microsoft 365 data; separate backup credentials with MFA; documented restore runbook. | IT Operations Lead | Immutable storage or object-lock service; isolated restore environment; one test day each quarter. | Quarterly restore test with measured recovery time and integrity check, recorded and reviewed by the Business Owner. |
| P2 | **Accessible incident contact list:** printed and offline copies, a mobile phone tree, and an out-of-band messaging group, independent of FS01 and email. | Incident Manager | Printed cards, secure mobile messaging group, HR data feed. | Twice-yearly call-tree test: at least 90% of CSIRT roles reached within 15 minutes. |
| P3 | **Server EDR and logging coverage:** EDR on all servers, central log retention of at least 90 days, alerts for service-account anomalies. | Security Analyst | EDR licences, log platform, tuning time. | Monthly coverage report showing 100% of servers; alert test for out-of-hours svc_backup logon. |
| P4 | **Privileged account hardening:** replace broad svc_backup rights with least privilege, vaulted secrets, no interactive logon, conditional access and phishing-resistant MFA for admins. | IT Operations Lead | Privileged access tooling, MFA tokens, change window. | Quarterly privileged account review; automated test that service accounts cannot log on interactively. |
| P5 | **Secure communication and training:** pre-approved holding statements, out-of-band channel, annual role-specific training and phishing awareness for finance staff. | Communications Lead and Incident Manager | Template library, training platform, two exercise days per year. | Completion rate of 95% of staff; tabletop exercise held at least twice yearly; phishing simulation click rate tracked. |

---

# Task 3: Response playbook and evidence preservation

## 3.1 Playbook: first 60 minutes and first 24 hours

Day 0 times start at 08:45. Pre-authorisation under the plan means isolation steps do not wait for further approval.

| Time or stage | Action | Owner | Required approval | Evidence to retain | Completion check |
| --- | --- | --- | --- | --- | --- |
| 08:45 to 08:50 (declare) | Declare a Critical incident, open the decision log, start a phone bridge, and appoint a scribe. | Incident Manager | Executive Sponsor informed by phone | Decision log start entry; call time | Roles confirmed and log open |
| 08:50 to 09:00 (short-term containment) | Network-isolate FIN-PC07 and FS01 using EDR containment or switch/VLAN quarantine. Do not power off. | IT Operations Lead | Pre-authorised (Incident Manager informed) | EDR containment record; screenshot of ransom note; firewall logs | No traffic from either host in firewall logs |
| 09:00 to 09:10 (protect backups) | Disable svc_backup, pause backup jobs, disconnect BAK01 from the network, and take a configuration snapshot. | IT Operations Lead | Incident Manager | BAK01 session and job logs; account audit record | No active sessions on BAK01; jobs paused |
| 09:05 to 09:20 (identity) | Revoke M365 sessions for the finance officer, block the unfamiliar IP, remove the new device, review MFA methods and mailbox rules. Reset passwords after logs are exported. | Security Analyst and IT Operations Lead | Incident Manager | Exported sign-in and audit logs; mailbox rule export | Sessions revoked; no active unknown device |
| 09:10 to 09:20 (exfiltration) | Block the unfamiliar cloud-storage destination at proxy and firewall, and preserve proxy logs. | IT Operations Lead | Incident Manager | Proxy log export; block rule record | Rule hit count visible; no further uploads |
| 09:15 to 09:30 (communications) | Treat corporate email as untrusted for incident traffic. Rebuild the contact list from phones and HR files and move the team to the out-of-band channel. | Incident Manager | None | Rebuilt contact list; channel membership record | All CSIRT roles confirmed on the bridge |
| 09:20 to 09:45 (investigation) | Take an LMS01 snapshot, then restrict it from the network. Triage for encryption, web shells and new scheduled tasks. Start volatile capture on FIN-PC07 and FS01 if EDR live response allows. | Forensics Lead and Security Analyst | Incident Manager | LMS01 snapshot reference; memory capture log; triage notes | Snapshot recorded; triage note issued |
| 09:45 (communication) | Issue the executive briefing and the first learner holding notice by SMS and website banner. | Communications Lead | Executive Sponsor | Message text and send log | Both sent and logged |
| 09:45 to 10:15 (scope hunt) | Run an IOC and behaviour sweep across endpoints and servers. Search for new admin accounts, scheduled tasks and M365 forwarding rules. | Security Analyst | Incident Manager | IOC list; query outputs; host list with hits | Sweep complete; hosts with hits reported |
| 10:15 (decision) | Hold the examination go or no-go decision using the criteria in 3.2. | Business Owner | Executive Sponsor informed | Decision log entry with rationale | Decision communicated to learners and staff |
| 10:30 to 12:00 (engage support) | Engage the external forensic firm, legal counsel and insurer. Do not communicate with the attacker. | Incident Manager | Executive Sponsor | Engagement records | Support confirmed on the bridge |
| 12:00 to 18:00 (backup validation) | Identify the candidate backup. Scan and mount the Friday 17:00 disconnected backup in an isolated environment and run a test restore of FS01 and LMS01 data. | IT Operations Lead and Security Analyst | Incident Manager | Restore test log; scan results; integrity check | Test restore completes with integrity confirmed |
| Day 0 evening (eradication) | Rebuild FIN-PC07 and FS01 from clean images. Remove persistence found in the sweep. Reset all privileged, service and finance accounts. Close the entry point. | IT Operations Lead and Security Analyst | Incident Manager | Before and after configuration; account reset log | IOC rescan clean; no active old credentials |
| Day 1 morning (recovery) | Restore services in the agreed order (3.5), under enhanced monitoring. | IT Operations Lead | Business Owner | Restore logs; validation results | Acceptance criteria in 3.6 met |
| By Day 1 08:45 (closure steps) | Confirm status, update notification assessment, schedule the post-incident review, and plan staged recovery of remaining services. | Incident Manager | Executive Sponsor | Status report; action list | Review date set; open actions assigned |

**Checking for broader compromise:** I will look at five things: endpoints (EDR sweep for the same file hash, process or persistence), privileged and cloud accounts (new admins, OAuth consents, MFA changes, forwarding rules), the shared server and backup repository (logons by svc_backup and file changes), network (connections to the upload destination and lateral movement), and any second staging host. The sweep relies on IOCs from FIN-PC07 and FS01, and I treat any host contacting the same destination as suspect (CISA, n.d.).

## 3.2 Two difficult decisions

### Decision A: Containment while preserving useful evidence

The teaching deck lists isolating affected systems and preserving forensic evidence before changes are made as short-term containment steps (ICDFA, n.d., slide 11). These can conflict, so I set an order.

Evidence of active encryption (E06) means **immediate isolation is necessary** when files are changing now, when svc_backup could reach BAK01 (E03), or when exfiltration continues. In that case the first step is network isolation, not evidence collection. Network containment stops spread yet keeps the host powered, so volatile data (memory, running processes, network connections) is retained. I will not power off FIN-PC07 or FS01 unless the EDR containment fails or the host is still spreading, because shutting down destroys memory and encryption keys that may support recovery. If memory capture would delay isolation of a spreading host, isolation wins. I will record every case of evidence loss in the decision log with time, reason and affected artifact, and I will note the operational impact (for example, finance staff cannot work, and FS01 shares are unavailable). The forensic lead will confirm what can still be recovered from disk, logs and EDR (NIST, 2025).

### Decision B: Restoring examination access while backup safety is uncertain

Management may want the portal back before 11:00. I will not reopen the portal on an unverified backup. My reasons: the portal depends on FS01 (E07), backup integrity is unproven (E05), BAK01 may have been touched (E03), and no restore test has run in 90 days. Reopening on a compromised service could expose learners and corrupt assessment records. The go criteria for the 10:15 decision are: (1) a clean restore of FS01 and LMS01 content from a verified source tested in isolation; (2) LMS01 triage shows no compromise; (3) all privileged credentials reset; (4) monitoring in place. If these are not met, the default is **postponement** with a new date. Where the Academic Office holds the master paper (A5), an alternative controlled assessment can be offered later without the compromised portal. The Business Owner decides, with the Incident Manager's recommendation recorded in the log.

## 3.3 Evidence register (planned collection)

Nothing below has been collected. Dates and times are planned and use WAT. Hashes will be computed at acquisition; none are claimed here.

| ID | Source | Proposed collection method | Collector role | Planned date and time (WAT) | Integrity check | Secure storage | Custody transfers |
| --- | --- | --- | --- | --- | --- | --- | --- |
| EV-01 | FIN-PC07 volatile memory | EDR live response or memory acquisition tool run from trusted media | Forensics Lead | Not yet collected; planned Day 0 09:30 | SHA-256 recorded at acquisition (planned) | Encrypted evidence store, access restricted | To be recorded on custody form |
| EV-02 | FIN-PC07 disk image and EDR telemetry | Forensic disk image after volatile capture; EDR export | Forensics Lead | Not yet collected; planned Day 0 10:30 | SHA-256 of image (planned); write-blocker | Encrypted store; second offline copy | To be recorded |
| EV-03 | FS01 event logs, ransom note, sample .locked files | Log export and file copy using read-only methods | Security Analyst | Not yet collected; planned Day 0 09:45 | SHA-256 of each exported file (planned) | Encrypted store | To be recorded |
| EV-04 | Microsoft 365 audit and sign-in logs, message trace | Admin export through an authorised account | Security Analyst | Not yet collected; planned Day 0 09:15 | SHA-256 of export files (planned) | Encrypted store | To be recorded |
| EV-05 | Proxy logs covering E04 | Export from proxy console | IT Operations Lead | Not yet collected; planned Day 0 09:20 | SHA-256 of export (planned) | Encrypted store | To be recorded |
| EV-06 | BAK01 security logs and backup job history | Log export before any rebuild | Security Analyst | Not yet collected; planned Day 0 09:10 | SHA-256 of export (planned) | Encrypted store | To be recorded |
| EV-07 | LMS01 snapshot | Hypervisor snapshot, retained read-only | IT Operations Lead | Not yet collected; planned Day 0 09:30 | Snapshot ID and checksum if supported (planned) | Hypervisor store with restricted access | To be recorded |

**Access restrictions and custody arrangements:** Evidence is stored on an encrypted location separate from FS01 and from the affected domain, with access limited to the Forensics Lead, Security Analyst, Incident Manager and the external forensic firm. Every access is logged. A custody form records each transfer with name, role, date, time (WAT), purpose and integrity check result. Working copies are used for analysis and originals remain untouched. Evidence is not shared by email while email trust is unverified; transfers use the secure out-of-band method.

## 3.4 Eradication and security of entry point and credentials

- **Persistence:** Remove malicious scheduled tasks, services, startup items and web shells found in the sweep. Rebuild FIN-PC07 and FS01 from clean images rather than cleaning them in place, and verify removal with more than one scanning tool (ICDFA, n.d., slide 11).
- **Likely entry point (E01):** Block the sender and attachment type, add mail filtering for the observed pattern, and brief finance staff. Confirm the entry point through forensic analysis before closure.
- **Credentials:** After evidence is captured, reset the finance officer's password and re-register MFA with phishing-resistant methods. Rotate svc_backup and all privileged and service accounts. Review M365 for malicious app consents, mail rules and added authentication methods. Treat every credential used on FIN-PC07 as exposed.

## 3.5 Recovery order, targets and acceptable data loss

**Restoration order**

1. Identity and access baseline: privileged accounts reset, conditional access, MFA enforced.
2. Clean isolated restore environment and verified backup.
3. FS01 (file server), because the portal depends on it.
4. LMS01 (learning portal), after content validation.
5. Finance workstations including rebuilt FIN-PC07 and finance data.
6. BAK01 rebuilt with immutable copy before normal backups resume.

**Targets:** Critical systems (identity, FS01, LMS01) restored within 72 hours. Full restoration of all remaining systems within 7 days. These are recovery targets I set for the scenario, supported by risk-based prioritisation (NIST, 2025).

**Acceptable data loss:** If recovery uses the Friday 17:00 disconnected backup (A4), the loss window is about 63 hours 45 minutes (Friday 17:00 to Monday 08:45). The Business Owner accepts this loss for FS01 and LMS01 content. Any later data will be reconstructed from clean sources (for example, learner emails, paper records, or re-submission) where possible.

**Selecting and validating a backup:** Prefer the Friday disconnected backup because it was not exposed to svc_backup (A4). Verify provenance and checksums, scan the restore point in isolation, mount it in a sandbox, and compare file counts and sample files with expected content. Do not trust the Day 0 02:00 job status (E05). Check data integrity by opening sample assessment records, validating database consistency, and confirming no ransomware artifacts or unexpected scheduled tasks. Restore in a controlled environment before any production connection.

## 3.6 Recovery acceptance criteria (before the Business Owner approves service return)

| # | Criterion | Evidence of completion |
| --- | --- | --- |
| 1 | Backup source verified and scanned clean in isolation. | Scan report; restore test log |
| 2 | Restored data passes integrity checks (file counts, sample records, database consistency). | Integrity checklist signed by IT Operations Lead |
| 3 | All privileged, service and finance credentials reset; MFA enforced. | Account reset log; MFA report |
| 4 | Entry point addressed and persistence removed; IOC rescan shows no hits. | Scan results; change record |
| 5 | EDR active on restored servers and workstations. | Coverage report |
| 6 | Enhanced monitoring in place for at least 72 hours with alert thresholds set. | Monitoring plan; alert test |
| 7 | Business Owner signs off after a user acceptance test of the portal and a mock assessment. | Signed acceptance record |
| 8 | Rollback plan documented. | Rollback procedure |

---

# Task 4: Communication and escalation

## 4.1 Communication matrix

Corporate email is untrusted for incident traffic (E08). The alternative channels are phone calls, SMS broadcast, an out-of-band messaging group on managed devices, a public website notice hosted outside FS01, and printed notices on campus.

| Stakeholder | Trigger | Message owner | Channel | Approver | Update frequency |
| --- | --- | --- | --- | --- | --- |
| Learners | Declaration of Critical incident and any exam decision | Communications Lead | SMS, website banner, social account (alternative to email) | Executive Sponsor | At decision points and at least every 3 hours on Day 0 |
| All staff | Declaration and each major status change | Communications Lead | Phone cascade and out-of-band group | Incident Manager | Twice on Day 0, then daily |
| Executive management | Declaration, severity change, any approval needed | Incident Manager | Phone call and secure group | Incident Manager | Every 2 hours on Day 0, then daily |
| IT and security staff | Continuous | Incident Manager | Bridge call and out-of-band group | Incident Manager | Continuous, with hourly summary |
| Service providers (cloud, backup, ISP, forensic firm) | Need for support, log access or blocking | IT Operations Lead | Phone and vendor portal | Incident Manager | As needed, at least every 4 hours while support is active |
| Legal and privacy advisers | Declaration, and any indication of personal data involvement | Incident Manager | Phone, then secure document share | Executive Sponsor | Immediate; then every 4 hours on Day 0 |
| Insurer | Declaration of Critical incident | Legal and Privacy Adviser | Phone and insurer portal | Executive Sponsor | At declaration and on material change |
| Law enforcement and sector sharing group (ISAC) | Legal adviser advises that a report or intelligence sharing is appropriate | Legal and Privacy Adviser | Phone and secure share | Executive Sponsor | On decision; sharing after the incident (ICDFA, n.d., slide 16) |
| Media and public | Any inquiry or social post | Communications Lead | Approved holding statement | Executive Sponsor with legal review | On request only; scripted |

## 4.2 Executive briefing (Day 0, 09:45 WAT, simulated timing)

> **Executive briefing: Northbridge Skills Institute**
>
> **Confirmed:** Ransomware-type encryption is active on the finance workstation FIN-PC07 and the file server FS01, and a ransom note is present (E06). The learning portal loads, but learners cannot open materials or submit assessments (E07). The incident contact list is inaccessible (E08).
>
> **Uncertain:** We have not confirmed the malware family, the entry point, or whether learner data was stolen. A 1.2 GB upload to unfamiliar cloud storage (E04) is under investigation. Backup integrity and corporate email safety are unverified (E05, E08).
>
> **Actions taken:** Affected systems are isolated, backup access is suspended, and the finance officer's account sessions have been revoked.
>
> **Decisions needed:** (1) Approve postponing or relocating the 11:00 examination unless a safe service is verified by 10:15; (2) approve engagement of external forensic and legal support; (3) approve recovery from the offline Friday backup after testing.
>
> **Next update:** 11:00 WAT by phone, then every two hours.

## 4.3 Learner notice

> **Important notice for learners**
>
> We are investigating a technical incident that affects the learning portal. Some course materials and assessment submissions are unavailable.
>
> **Examination:** Today's 11:00 online examination will not take place at the scheduled time. Your place is secure and you do not need to re-register. We will confirm a new date by 14:00 today on the website notice page and by SMS.
>
> **What you should do:** Do not repeatedly try to log in. Do not open unexpected emails or attachments that claim to come from the Institute, and never share your password. If you already submitted work, keep your own copy. For help, call the helpline on +000 000 0000 (fictional).
>
> We apologise for the disruption and will update you as soon as we can.

## 4.4 Escalation rules and notification assessment

**Escalation rules:**

- Escalate to the Executive Sponsor within 15 minutes of any Critical declaration, or if personal data exposure becomes probable.
- Escalate to the legal adviser immediately when exfiltration is suspected, when an attacker claims to hold data, or when media contact occurs.
- Escalate to external forensic support within 60 minutes for any Critical incident, or when backups are in doubt.
- Escalate to law enforcement or national incident response bodies on the legal adviser's advice.
- Any message that names a data breach, an attacker, or a ransom requires legal review and Executive Sponsor approval.

**Assessing notification obligations:** The Legal and Privacy Adviser, supported by the Incident Manager and Forensics Lead, will (1) confirm the jurisdiction and applicable data protection and sector rules (A6); (2) establish what personal data may be involved, whose, and in what form; (3) assess risk of harm to affected people; (4) confirm the recipients (regulator, individuals, partners, insurer) and the legally required content and deadline from the primary legal source; and (5) record the assessment and its timing in the decision log. No legal deadline is asserted in this document because the jurisdiction is not given in the scenario.

**Operational versus formal notices:** An *operational notice* (such as the learner notice) explains service status and safe behaviour and does not state conclusions about data breach. A *formal breach notification* goes to a regulator or affected individuals only after the legal adviser confirms the requirement, with verified facts, and is approved by the Executive Sponsor.

---

# Task 5: Tabletop exercise and improvement plan

> **SIMULATION.** Everything in this task describes a simulated exercise carried out on paper as a walkthrough by one person playing each role. Results are simulated and are not real measurements.

## 5.1 Design

**Duration:** 60 minutes. **Exercise clock:** Exercise minute 0 corresponds to 09:30 WAT on Day 0, after initial containment has been ordered.

**Objectives:** (1) Test containment and backup protection decisions. (2) Test the examination decision under management pressure. (3) Test communication, including the out-of-band channel. (4) Test recovery planning when a backup fails.

**Participant roles (played by the author in turn):** Incident Manager, Security Analyst, IT Operations Lead, Business Owner (Academic Director), Legal and Privacy Adviser, Communications Lead, with the Executive Sponsor as a decision approver.

**Agenda**

| Minute | Activity |
| --- | --- |
| 0 to 5 | Briefing: scenario, rules, roles, evidence pack E01 to E08. |
| 5 to 10 | Initial actions: confirm containment, channels and roles. |
| 10 to 20 | Inject 1 and discussion. |
| 20 to 35 | Inject 2 and discussion; investigation priorities. |
| 35 to 45 | Inject 3 and discussion. |
| 45 to 52 | Inject 4 and discussion. |
| 52 to 56 | Own inject (Inject 5) and discussion. |
| 56 to 60 | Hot wash: record decisions, successes and gaps. |

**Measurable success criteria**

| # | Criterion | Target |
| --- | --- | --- |
| SC1 | Time from declaration to isolation of FIN-PC07 and FS01 | 15 minutes or less |
| SC2 | CSIRT roles reached by the out-of-band channel | At least 90% within 15 minutes |
| SC3 | Time from the minute-10 inject to a documented examination decision | 10 minutes or less |
| SC4 | Share of decisions logged with owner and rationale | 100% |
| SC5 | Time from the media inject to an approved holding statement | 15 minutes or less |

## 5.2 Injects

| Minute | Inject |
| --- | --- |
| 10 | Management asks to reopen the portal before the 11:00 examination even if the latest backup is unverified. |
| 20 | An attacker claims to hold all 1,800 learner records and threatens publication in 12 hours. A screenshot of unknown authenticity is supplied. |
| 35 | A test restoration of the latest network backup fails with an unreadable snapshot error. |
| 45 | A social media post claims the examination is cancelled, and a journalist asks whether learner information has been exposed. |
| 52 (own inject) | The EDR sweep finds a scheduled task on LMS01 created at 01:40 that connects to an unknown external IP address. |

## 5.3 Simulated role-by-role walkthrough

| Inject | Incident Manager | Security Analyst | IT Operations | Business Owner | Legal and Privacy | Communications |
| --- | --- | --- | --- | --- | --- | --- |
| 1 (reopen portal) | Refuses reopening on unverified backup; sets go criteria and 10:15 deadline. | Confirms LMS01 triage is incomplete. | States FS01 content is not restored. | Accepts postponement as the safe default and asks for an alternative plan. | Advises that reopening a compromised service raises exposure risk. | Prepares a postponement notice for approval. |
| 2 (attacker claim) | Treats the claim as unverified; no contact with attacker. | Preserves screenshot; compares visible fields with known records; checks the E04 upload. | Increases monitoring of egress. | Asks about learner impact. | Starts notification assessment; advises on law enforcement and insurer contact. | Prepares a holding statement; no confirmation or denial. |
| 3 (backup fails) | Switches to the Friday offline backup and records the changed recovery estimate. | Inspects BAK01 logs for tampering. | Tests the offline backup in isolation. | Accepts a longer recovery and larger data loss. | Notes possible impact on data availability obligations. | Updates the planned message with a revised timeline. |
| 4 (social post, journalist) | Calls a short bridge; confirms facts. | Confirms no evidence yet of public posting of data. | Checks website notice page is up. | Confirms the examination is postponed, not cancelled. | Reviews the media statement. | Posts a correction and issues the media holding statement. |
| 5 (LMS01 task) | Escalates scope to include LMS01 compromise; extends the portal outage. | Pivots IOC search to the new destination and task. | Rebuilds LMS01 from a clean image. | Agrees to reschedule the examination. | Informs counsel of broader scope. | Prepares an updated learner notice. |

## 5.4 Simulated decision log

| # | Inject | Decision | Decision owner | Rationale | Communication | Effect on response or recovery plan |
| --- | --- | --- | --- | --- | --- | --- |
| D1 | Min 10 | Do not reopen the portal on an unverified backup. Hold the examination go or no-go at 10:15 with postponement as the default. | Business Owner (on Incident Manager recommendation) | Backup unverified (E05), BAK01 may be touched (E03), portal depends on FS01 (E07). | Learner holding notice; staff update | Keeps recovery on the verified route and avoids exposing learners to a compromised service. |
| D2 | Min 20 | Treat the attacker's claim as unverified. Do not respond or pay. Preserve the screenshot and start a legal and law-enforcement assessment. | Incident Manager with Executive Sponsor | Authenticity unknown; E04 supports plausibility but not proof; engaging could escalate risk. | Legal adviser, insurer and executive team informed. No public confirmation. | Notification assessment starts early; monitoring of publication sites requested through the provider. |
| D3 | Min 35 | Move to the Friday 17:00 disconnected backup as the primary recovery source, test it in isolation, and revise data loss to about 63 hours 45 minutes. | Incident Manager; Business Owner accepts data loss | The network backup route is unreadable and untested; Friday backup is offline (A4). | Executive update with revised estimate | Recovery time and data-loss expectations change; eradication proceeds in parallel. |
| D4 | Min 45 | Issue a holding statement that neither confirms nor denies data exposure, and correct the false post: the examination is postponed, not cancelled. | Executive Sponsor (on Communications Lead draft and legal review) | Facts unconfirmed; false statements spread quickly. | Social account, website notice and SMS | Communication load increases; a spokesperson is nominated. |
| D5 | Min 52 | Treat LMS01 as compromised. Rebuild from a clean image and restore only validated content. Reschedule the examination. | Incident Manager; Business Owner for reschedule | Unknown outbound task created at 01:40 suggests persistence. | Updated learner notice; executive update | Recovery order unchanged but LMS01 now needs a rebuild, adding time to the 72-hour target. |
| D6 | Min 56 | Fix a hot-wash action list and schedule the post-incident review. | Incident Manager | Capture gaps while fresh. | Participants | Feeds the improvement plan in 5.6. |

## 5.5 Simulated evaluation against success criteria

| Criterion | Simulated result | Met? | Notes |
| --- | --- | --- | --- |
| SC1 isolation within 15 min | Simulated at 12 minutes because EDR containment was pre-authorised. | Yes | Depends on EDR containment working on FS01; server EDR coverage is incomplete. |
| SC2 CSIRT reached within 15 min | Simulated 6 of 9 roles reached; the contact list had to be rebuilt from personal phones. | No | Confirms the need for an accessible contact list (P2). |
| SC3 exam decision within 10 min | Decision documented in 8 minutes. | Yes | Clear go criteria helped. |
| SC4 decisions logged | 6 of 6 decisions logged. | Yes | Scribe role was valuable. |
| SC5 holding statement within 15 min | Simulated 22 minutes. | No | No pre-approved media statement existed. |

The exercise simulation showed that containment and decision discipline were strong, and that contact reachability, media statements and backup readiness were weak.

## 5.6 Improvement plan

| # | Improvement | Category (slide 13) | Priority | Owner | Target completion | Measurable evidence of completion |
| --- | --- | --- | --- | --- | --- | --- |
| I1 | Create immutable offline backups and run a documented restore test of FS01 and LMS01. | Security controls | High | IT Operations Lead | 30 days | Test report showing restore time and successful integrity check. |
| I2 | Publish an offline contact list and phone tree independent of FS01 and email. | Policies and procedures | High | Incident Manager | 14 days | Call-tree test with at least 90% reached in 15 minutes. |
| I3 | Pre-approve media and learner holding statements with legal review. | Policies and procedures | High | Communications Lead | 14 days | Approved template library stored outside FS01. |
| I4 | Extend EDR to all servers and alert on out-of-hours service-account logons. | Tools and technology | High | Security Analyst | 30 days | Coverage report showing 100% of servers; alert test passed. |
| I5 | Reduce svc_backup privileges, vault its secrets and block interactive logon. | Security controls | High | IT Operations Lead | 21 days | Privilege review record; test showing denied interactive logon. |
| I6 | Establish an alternative assessment plan using the offline master paper (A5). | Policies and procedures | Medium | Business Owner | 45 days | Documented continuity procedure tested in a mock session. |
| I7 | Run a follow-up tabletop with a different scenario. | Training and awareness | Medium | Incident Manager | 90 days | Exercise report with success criteria measured. |

**Post-incident review:** Held within two weeks of incident closure (ICDFA, n.d., slide 12), in a blame-free format. The Incident Manager chairs it. The agenda is what happened and when, what worked well, and what could be improved. Root cause analysis uses a timeline reconstruction, the Five Whys, and attack chain mapping with MITRE ATT&CK (ICDFA, n.d., slide 12). The review output covers the required documentation elements (ICDFA, n.d., slide 13). The Executive Sponsor closes the actions after the Incident Manager confirms the evidence of completion in the table above, and open actions are tracked weekly. Lessons are shared with sector partners where the legal adviser agrees.

---

# Task 6: Case study analysis and critical reflection

**Strengths.** First, verified offline backups let the healthcare organisation recover without paying the ransom, so backup isolation, not tooling alone, decided the outcome (ICDFA, n.d., slide 16). Second, EDR alerts triggered immediate team activation, and isolating network segments limited spread. Third, a pre-established team and playbooks supported on-time regulatory notification and a Day 14 review that fed sector information sharing.

**Limitations.** The malware moved laterally for 72 hours before encryption, and EDR alerted only then; the case does not explain why earlier activity went unseen. Also, "no data exfiltration confirmed" is stated without the evidence behind it, and the restoration gaps found by backup testing are not shown as closed.

**Transfer to Northbridge.** (1) Tested offline backups: Northbridge has not tested restoration in 90 days and cannot trust E05, so I made the Friday disconnected backup the primary route and added quarterly restore tests (P1, I1). (2) Segmentation with earlier detection: svc_backup reached FS01 and BAK01 at 01:35 (E03) without an alert, so I added server EDR, out-of-hours service-account alerts and reduced privileges (I4, I5).

**72 hours versus seven days.** Restoring critical systems in 72 hours returns the minimum services the mission needs, from validated backups, with reduced functionality and heavy monitoring. Full restoration over seven days adds remaining systems, data reconciliation, rebuilt workstations and hardening. The first is driven by business impact; compressing the second into it risks reintroducing compromise (NIST, 2025).

**Evidence before claiming no data was stolen.** Absence of a ransom claim is not enough. Northbridge would need full egress and DNS logs across the attack window, endpoint and cloud audit logs showing no unauthorised access or archive creation, the contents of the 1.2 GB upload (E04), no leak-site listing, and forensic confirmation of attacker activity dates. If logs are missing, the correct statement is that no evidence of theft was found.

**Decision to revise.** After the tabletop I would revise reliance on the Friday backup alone: its loss window of about 63 hours 45 minutes strains the 72-hour target (D3), so I would add immutable intermediate backups and a pre-agreed data reconstruction plan.

---

# References and source-to-decision map

All sources should be opened and checked by the student before submission, and an access date added.

- ICDFA. (n.d.). *Incident Response Planning*. GRC105, Week 20. Teaching deck, slides 1 to 18. International Cybersecurity and Digital Forensics Academy.
- NIST. (2025). *SP 800-61 Revision 3: Incident Response Recommendations and Considerations for Cybersecurity Risk Management*. National Institute of Standards and Technology. https://csrc.nist.gov/pubs/sp/800/61/r3/final
- NIST. (n.d.). *Incident Response project: Framework and life cycle overview*. https://csrc.nist.gov/projects/incident-response
- CISA and partners. (n.d.). *#StopRansomware Guide*. Cybersecurity and Infrastructure Security Agency. https://www.cisa.gov/resources-tools/resources/stopransomware-guide

| Source | Decisions it supports |
| --- | --- |
| ICDFA Week 20 deck (n.d.) | Slide 4 and 10: classification and priority tiers (Task 1). Slide 6: four stages. Slide 7 and 8: preparation, on-call, contact lists and pre-approved statements (2.4, Task 4). Slide 11: containment, eradication and recovery steps (Task 3). Slide 12 and 13: post-incident review, documentation and improvement areas (Task 5). Slide 14 and 15: plan components, roles and RACI (Task 2). Slide 16: healthcare case (Task 6). |
| NIST (2025) SP 800-61r3 | CSF 2.0 alignment of incident response and preparation (2.1); evidence handling and containment trade-offs (3.2); recovery prioritisation (3.5, 3.6); 72-hour versus full restoration (Task 6). |
| NIST (n.d.) Incident Response project | Overview of life cycle and improvement feeding all functions (5.6, post-incident review). |
| CISA (n.d.) #StopRansomware Guide | Isolation of affected systems and scope checks (3.1); backup readiness, offline copies and restore testing (2.4, 3.5). |

## Authorship and AI declaration

[Edit this section so it matches the course's required AI declaration wording and form.]

- **Tool used:** Claude (Anthropic), a generative AI assistant.
- **How it was used:** To help structure the assignment, draft tables and text for all six tasks from the assessment brief and teaching slides, and format the document.
- **What I did myself:** [State what you did: for example, reviewed every task against the brief and the slides, checked the sources, changed wording and decisions to reflect my own judgement, and added my personal details.]
- **Responsibility:** I have reviewed and edited this work, I take responsibility for its content and accuracy, and I can explain any decision in it if asked.
- **Data:** All organisations, people, numbers and contact details in the scenario and this document are fictional.
- **Sources:** The teaching slides and the NIST and CISA sources in the reference list.
