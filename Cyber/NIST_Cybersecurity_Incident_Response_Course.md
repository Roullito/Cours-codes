# NIST Cybersecurity & Incident Response — Complete Course

> **Focus:** NIST Cybersecurity Framework (CSF) 2.0, NIST SP 800-61 Rev. 2, Incident Response, NVD, CVE/CVSS/CWE/CPE, roles and responsibilities, post-incident review, continuous improvement, and anonymization.
>
> **Project objective:** Be able to explain the NIST incident response approach without relying on Google and build a professional, actionable incident response plan.

---

## Table of Contents

1. [Introduction to NIST](#1-introduction-to-nist)
2. [NIST Cybersecurity Framework — CSF 2.0](#2-nist-cybersecurity-framework--csf-20)
3. [The Six CSF 2.0 Functions](#3-the-six-csf-20-functions)
4. [CSF Core, Profiles and Tiers](#4-csf-core-profiles-and-tiers)
5. [Introduction to Incident Response](#5-introduction-to-incident-response)
6. [NIST SP 800-61 Incident Response Lifecycle](#6-nist-sp-800-61-incident-response-lifecycle)
7. [Phase 1 — Preparation](#7-phase-1--preparation)
8. [Phase 2 — Detection and Analysis](#8-phase-2--detection-and-analysis)
9. [Phase 3 — Containment](#9-phase-3--containment)
10. [Phase 4 — Eradication](#10-phase-4--eradication)
11. [Phase 5 — Recovery](#11-phase-5--recovery)
12. [Phase 6 — Post-Incident Activity](#12-phase-6--post-incident-activity)
13. [Incident Response Team Roles](#13-incident-response-team-roles)
14. [Communication During an Incident](#14-communication-during-an-incident)
15. [Evidence Collection and Chain of Custody](#15-evidence-collection-and-chain-of-custody)
16. [Incident Prioritization](#16-incident-prioritization)
17. [Building an Incident Response Plan](#17-building-an-incident-response-plan)
18. [Example Incident Response Playbook](#18-example-incident-response-playbook)
19. [Post-Incident Review and Lessons Learned](#19-post-incident-review-and-lessons-learned)
20. [Continuous Improvement](#20-continuous-improvement)
21. [National Vulnerability Database — NVD](#21-national-vulnerability-database--nvd)
22. [CVE, CVSS, CWE and CPE](#22-cve-cvss-cwe-and-cpe)
23. [Anonymization and Sensitive Information](#23-anonymization-and-sensitive-information)
24. [NIST CSF and Incident Response Mapping](#24-nist-csf-and-incident-response-mapping)
25. [Common Mistakes](#25-common-mistakes)
26. [Practical Incident Response Checklist](#26-practical-incident-response-checklist)
27. [Incident Report Template](#27-incident-report-template)
28. [Revision Sheet — What to Remember](#28-revision-sheet--what-to-remember)
29. [Practice Questions](#29-practice-questions)
30. [Conclusion](#30-conclusion)
31. [References](#31-references)

---

# 1. Introduction to NIST

## 1.1 What is NIST?

**NIST** stands for **National Institute of Standards and Technology**.

It is a United States government organization that publishes standards, frameworks, guidelines, and technical references used worldwide.

In cybersecurity, NIST publications are widely used to help organizations:

- manage cybersecurity risks;
- protect systems and information;
- create security policies;
- organize incident response;
- manage vulnerabilities;
- improve resilience;
- assess security controls;
- communicate cybersecurity risks to management.

NIST guidance is especially important because it provides a structured vocabulary and repeatable methodology.

---

## 1.2 Important NIST cybersecurity publications

Some major NIST resources include:

| Publication / Resource | Purpose |
|---|---|
| **NIST Cybersecurity Framework (CSF)** | Overall cybersecurity risk management framework |
| **NIST CSF 2.0** | Current version of the Cybersecurity Framework |
| **NIST SP 800-61** | Incident response guidance |
| **NIST SP 800-53** | Security and privacy controls |
| **NIST SP 800-30** | Risk assessment guidance |
| **NIST SP 800-37** | Risk Management Framework |
| **NIST SP 800-40** | Enterprise patch management guidance |
| **NIST SP 800-115** | Technical security testing guidance |
| **NIST NVD** | National Vulnerability Database |

This course focuses mainly on:

- **CSF 2.0**;
- **SP 800-61 Rev. 2 incident response lifecycle**;
- **National Vulnerability Database (NVD)**.

---

## 1.3 Important version note

The project requirements explicitly refer to **NIST SP 800-61 Rev. 2**, which describes the classic incident response lifecycle used throughout this course.

However:

- SP 800-61 Rev. 2 was published in **August 2012**;
- it was **withdrawn on April 3, 2025**;
- it was superseded by **NIST SP 800-61 Rev. 3**;
- Rev. 3 aligns incident response more closely with **NIST CSF 2.0**.

For this project, you should understand the Rev. 2 lifecycle because it is explicitly required, while also knowing that Rev. 3 is the current publication.

---

# 2. NIST Cybersecurity Framework — CSF 2.0

## 2.1 What is the NIST CSF?

The **NIST Cybersecurity Framework** is a framework designed to help organizations understand, assess, prioritize, communicate, and reduce cybersecurity risk.

It is not a list of mandatory technical configurations.

Instead, it defines **cybersecurity outcomes** an organization should aim to achieve.

The framework can be used by:

- companies;
- government agencies;
- small businesses;
- large enterprises;
- schools;
- healthcare organizations;
- cloud providers;
- industrial environments;
- IT, OT, IoT and mobile environments.

---

## 2.2 CSF 1.1 vs CSF 2.0

Older versions of the NIST CSF were commonly summarized using five functions:

```text
IDENTIFY → PROTECT → DETECT → RESPOND → RECOVER
```

CSF 2.0 added a sixth function:

```text
GOVERN
```

The six CSF 2.0 Functions are therefore:

```text
GOVERN
IDENTIFY
PROTECT
DETECT
RESPOND
RECOVER
```

The addition of **GOVERN** highlights that cybersecurity is not only a technical issue. It is also a matter of:

- leadership;
- policies;
- accountability;
- business risk;
- supply-chain risk;
- legal obligations;
- organizational strategy.

---

# 3. The Six CSF 2.0 Functions

A simple way to remember CSF 2.0 is:

```text
GOVERN  = Decide how cybersecurity risk will be managed
IDENTIFY = Understand what you have and what can go wrong
PROTECT  = Reduce the probability or impact of compromise
DETECT   = Discover suspicious or malicious activity
RESPOND  = Act when an incident occurs
RECOVER  = Restore operations and improve resilience
```

---

## 3.1 GOVERN

### Objective

Establish and monitor the organization's cybersecurity risk management strategy, expectations, roles, policies, and oversight.

### Typical activities

- define cybersecurity policies;
- define responsibilities;
- establish risk appetite and risk tolerance;
- ensure legal and regulatory compliance;
- manage third-party and supply-chain risks;
- monitor cybersecurity performance;
- ensure management involvement;
- define decision-making authority during incidents.

### Example

A company defines:

- who can disconnect a production server during an incident;
- who must notify customers;
- who can contact law enforcement;
- which incidents must be escalated to management.

That is **Govern**.

---

## 3.2 IDENTIFY

### Objective

Understand the organization's assets, systems, data, dependencies, risks, and vulnerabilities.

### Typical activities

- asset inventory;
- software inventory;
- data classification;
- business impact analysis;
- vulnerability identification;
- dependency mapping;
- threat identification;
- risk assessment.

### Example

Before protecting a database, the organization must know:

- that the database exists;
- what information it contains;
- who owns it;
- who can access it;
- what applications depend on it;
- what would happen if it became unavailable.

---

## 3.3 PROTECT

### Objective

Implement safeguards to reduce cybersecurity risk.

### Typical controls

- MFA;
- least privilege;
- encryption;
- secure configuration;
- backups;
- patch management;
- network segmentation;
- awareness training;
- endpoint protection;
- access control;
- data loss prevention.

### Example

A company protects administrator accounts using:

```text
Strong authentication + MFA + least privilege + privileged access monitoring
```

---

## 3.4 DETECT

### Objective

Identify cybersecurity events quickly.

### Typical mechanisms

- SIEM;
- IDS/IPS;
- EDR/XDR;
- antivirus alerts;
- authentication monitoring;
- network monitoring;
- file integrity monitoring;
- anomaly detection;
- cloud security monitoring;
- application logs.

### Example

A SIEM detects:

```text
50 failed logins
followed by
1 successful login
followed by
privilege escalation
```

This may indicate account compromise.

---

## 3.5 RESPOND

### Objective

Take action after an incident is detected.

Typical activities include:

- incident triage;
- containment;
- coordination;
- investigation;
- communication;
- mitigation;
- evidence preservation;
- incident reporting.

### Example

If ransomware is discovered:

```text
Detect ransomware
      ↓
Isolate affected hosts
      ↓
Disable compromised accounts
      ↓
Investigate entry point
      ↓
Remove persistence
```

---

## 3.6 RECOVER

### Objective

Restore affected services and improve resilience.

Typical activities include:

- restoring backups;
- rebuilding systems;
- validating integrity;
- returning systems to production;
- monitoring for recurrence;
- communicating restoration status;
- improving disaster recovery procedures.

Recovery does **not** simply mean turning systems back on.

A system should only return to production after the organization has reasonable confidence that:

- the threat has been removed;
- vulnerabilities have been corrected;
- credentials have been secured;
- restored data is trustworthy;
- monitoring is active.

---

# 4. CSF Core, Profiles and Tiers

CSF 2.0 is based on three main concepts:

1. **CSF Core**
2. **Organizational Profiles**
3. **Tiers**

---

## 4.1 CSF Core

The **Core** is a taxonomy of cybersecurity outcomes.

Its hierarchy is approximately:

```text
Function
   ↓
Category
   ↓
Subcategory
```

Example:

```text
DETECT
   ↓
Continuous Monitoring
   ↓
Specific monitoring outcomes
```

The CSF primarily defines **what outcome should exist**, not exactly which product or command must be used.

That makes it technology-neutral and flexible.

---

## 4.2 Organizational Profiles

A **Profile** describes an organization's cybersecurity posture using CSF outcomes.

Two useful concepts are:

### Current Profile

Where are we now?

### Target Profile

Where do we want to be?

The difference between them becomes a **gap analysis**.

Example:

```text
Current state:
No centralized logging

Target state:
Critical systems forward logs to SIEM with 24/7 alerting

Gap:
Deploy centralized logging + detection rules + response process
```

---

## 4.3 CSF Tiers

Tiers describe the rigor of cybersecurity governance and risk-management practices.

A simplified view is:

| Tier | Description |
|---|---|
| Tier 1 | Partial |
| Tier 2 | Risk-Informed |
| Tier 3 | Repeatable |
| Tier 4 | Adaptive |

They should not simply be interpreted as a score where every organization must reach Tier 4.

The appropriate level depends on business needs, threats, regulations, cost, and risk.

---

# 5. Introduction to Incident Response

## 5.1 What is a security event?

A **security event** is an observable occurrence relevant to security.

Examples:

- failed login;
- blocked malware;
- firewall denial;
- USB device connected;
- file modification;
- account creation.

An event is not automatically an incident.

---

## 5.2 What is a security incident?

A **security incident** is a security event or series of events that actually or potentially violates security policies or threatens the confidentiality, integrity, or availability of information systems.

Examples:

- ransomware infection;
- compromised administrator account;
- customer database exfiltration;
- web server compromise;
- successful phishing attack;
- unauthorized access;
- denial-of-service attack;
- malicious insider activity.

---

## 5.3 Event vs alert vs incident

```text
EVENT
Something happened

   ↓ analysis

ALERT
A tool or person considers the event suspicious

   ↓ validation

INCIDENT
The situation requires coordinated security response
```

Example:

```text
Event: 1 failed password attempt
Alert: 500 attempts from unusual IPs
Incident: attacker successfully authenticates and accesses sensitive files
```

---

## 5.4 Why incident response matters

Perfect prevention does not exist.

Even organizations with strong security controls can experience incidents.

Incident response aims to:

- identify incidents quickly;
- minimize damage;
- prevent spread;
- preserve evidence;
- restore services;
- communicate appropriately;
- determine root cause;
- learn from the incident;
- prevent recurrence.

---

# 6. NIST SP 800-61 Incident Response Lifecycle

NIST SP 800-61 Rev. 2 organizes incident handling into four major lifecycle groups:

```text
1. Preparation
        ↓
2. Detection & Analysis
        ↓
3. Containment, Eradication & Recovery
        ↓
4. Post-Incident Activity
        ↺
```

For learning purposes, it is often easier to split the third group into three separate operational phases:

```text
1. Preparation
2. Detection and Analysis
3. Containment
4. Eradication
5. Recovery
6. Post-Incident Activity
```

These are the six phases used throughout this course because they align with the project requirements.

---

# 7. Phase 1 — Preparation

## 7.1 Objective

Preparation ensures the organization is capable of responding **before** an incident occurs.

This is one of the most important phases.

A poor response plan cannot usually be fixed during a major ransomware attack.

---

## 7.2 Create an Incident Response Policy

The organization should define an official policy that explains:

- purpose;
- scope;
- authority;
- definitions;
- reporting obligations;
- roles;
- responsibilities;
- escalation process;
- communication rules;
- evidence handling expectations;
- links to legal or regulatory requirements.

---

## 7.3 Create an Incident Response Plan

A plan is more operational than a policy.

It should explain:

- mission and objectives;
- incident response approach;
- communication structure;
- team organization;
- management approval process;
- severity model;
- escalation procedure;
- relationships with external parties;
- metrics;
- improvement strategy.

---

## 7.4 Create procedures and playbooks

A **playbook** is a repeatable response procedure for a specific incident type.

Common playbooks include:

- phishing;
- ransomware;
- malware infection;
- lost device;
- compromised credentials;
- web application compromise;
- DDoS;
- insider threat;
- data leakage;
- cloud account compromise.

Example phishing playbook:

```text
1. Collect phishing email
2. Extract sender/domain/URLs/hashes
3. Search for other recipients
4. Block malicious indicators
5. Identify users who clicked
6. Reset compromised credentials
7. Revoke active sessions
8. Search endpoints for payloads
9. Monitor affected accounts
10. Document incident
```

---

## 7.5 Prepare tools

Possible tools include:

- SIEM;
- EDR;
- forensic workstation;
- packet capture tools;
- secure evidence storage;
- ticketing/case-management system;
- threat-intelligence platform;
- backup systems;
- password vault;
- communication platform;
- forensic disk imaging tools;
- hash utilities;
- log collection tools.

Useful command-line examples may include:

```bash
sha256sum evidence.img
journalctl
last
ss -tulpn
ps aux
ip addr
ip route
```

However, responders must follow approved procedures before executing commands on potentially compromised systems because every action can modify evidence.

---

## 7.6 Prepare logging

Incident response depends heavily on logs.

Important sources include:

- authentication logs;
- operating system logs;
- EDR telemetry;
- firewall logs;
- IDS/IPS logs;
- DNS logs;
- proxy logs;
- VPN logs;
- email logs;
- cloud audit logs;
- web server logs;
- database logs;
- application logs;
- DHCP logs.

### Logging best practices

- centralize logs;
- synchronize time using trusted time sources;
- protect logs against unauthorized modification;
- define retention periods;
- monitor critical events;
- restrict access;
- test that logging works before an incident.

---

## 7.7 Prepare backups

Backups are essential for recovery.

Good practices:

- maintain offline or isolated copies where appropriate;
- protect backup administrator accounts;
- encrypt sensitive backups;
- regularly test restoration;
- monitor backup failures;
- separate backup infrastructure from normal production credentials;
- document RPO and RTO requirements.

### RPO

**Recovery Point Objective** — acceptable amount of data loss measured in time.

Example:

```text
RPO = 4 hours
```

The company accepts losing at most four hours of data.

### RTO

**Recovery Time Objective** — target time to restore a service.

Example:

```text
RTO = 2 hours
```

The service should be restored within two hours.

---

## 7.8 Train the team

Preparation also includes:

- security awareness training;
- analyst training;
- tabletop exercises;
- simulated phishing;
- technical exercises;
- ransomware simulations;
- communication exercises;
- backup restoration testing.

---

# 8. Phase 2 — Detection and Analysis

## 8.1 Objective

Determine whether suspicious activity is actually an incident and understand:

- what happened;
- when it happened;
- how it happened;
- which systems are affected;
- which accounts are affected;
- whether data was accessed or stolen;
- how serious the incident is;
- whether the attacker is still active.

---

## 8.2 Precursors and indicators

NIST distinguishes useful concepts such as **precursors** and **indicators**.

### Precursor

A sign that an incident **may happen in the future**.

Examples:

- attacker scanning exposed services;
- threat intelligence warning of a campaign targeting your software;
- discovery of a critical vulnerability affecting your public server.

### Indicator

A sign that an incident **may have occurred or may be occurring now**.

Examples:

- antivirus detects malware;
- unusual outbound network traffic;
- unknown administrator account;
- suspicious PowerShell execution;
- impossible-travel login;
- unexpected encryption of files.

---

## 8.3 Detection sources

### Technical sources

- SIEM alerts;
- EDR detections;
- IDS/IPS alerts;
- firewall alerts;
- WAF alerts;
- cloud security alerts;
- antivirus;
- DLP;
- file integrity monitoring.

### Human sources

- employee report;
- customer complaint;
- administrator observation;
- external security researcher;
- vendor notification;
- law-enforcement notification.

---

## 8.4 Initial triage

An analyst should rapidly determine:

1. Is the alert legitimate?
2. Is it malicious or benign?
3. What is affected?
4. What is the potential business impact?
5. Does it require escalation?

Example:

```text
Alert: Successful admin login from unusual country

Questions:
- Is the admin traveling?
- Was MFA used?
- What device logged in?
- What actions happened after authentication?
- Were new accounts created?
- Was sensitive data downloaded?
```

---

## 8.5 Establish a timeline

A timeline is one of the most useful investigation tools.

Example:

```text
08:41 — phishing email delivered
08:46 — user clicks link
08:47 — credentials submitted to fake page
08:52 — attacker logs into Microsoft 365
08:55 — new inbox forwarding rule created
09:03 — attacker downloads customer files
09:12 — SIEM raises impossible-travel alert
09:18 — SOC begins investigation
09:23 — account disabled
```

Timelines help understand:

- attacker progression;
- response speed;
- control failures;
- potential exposure window.

---

## 8.6 Scope the incident

Scoping answers:

```text
How far did the incident spread?
```

Search for common indicators across the environment:

- IP addresses;
- domains;
- URLs;
- file hashes;
- filenames;
- registry keys;
- scheduled tasks;
- user agents;
- account names;
- command lines;
- processes;
- persistence mechanisms.

Do not assume that the first infected endpoint is the only affected endpoint.

---

## 8.7 Incident documentation

Record important facts as soon as possible:

- incident ID;
- detection time;
- reporter;
- systems involved;
- users involved;
- evidence collected;
- indicators of compromise;
- actions taken;
- decisions made;
- people approving decisions;
- timestamps;
- current status.

Documentation must be factual and clear.

Avoid unsupported conclusions.

Bad:

```text
The hacker stole everything.
```

Better:

```text
At 10:34 UTC, logs show the compromised account downloaded 2.4 GB from the customer-data repository. Investigation is ongoing to determine the exact files accessed.
```

---

# 9. Phase 3 — Containment

## 9.1 Objective

Containment limits damage and prevents the incident from spreading.

The key question is:

```text
How can we stop the attacker while preserving business operations and evidence?
```

---

## 9.2 Short-term containment

Immediate actions may include:

- isolate endpoint from network;
- disable compromised account;
- revoke active sessions;
- block malicious IP/domain/hash;
- remove malicious email from inboxes;
- restrict network segment;
- disable vulnerable service;
- temporarily block external access;
- place WAF rule;
- rotate exposed API keys.

Example:

```text
Compromised laptop
        ↓
EDR network isolation
        ↓
User account disabled
        ↓
Active sessions revoked
```

---

## 9.3 Long-term containment

Longer-term measures may include:

- temporary patched systems;
- clean replacement servers;
- network segmentation;
- temporary access-control restrictions;
- additional monitoring;
- alternate business process;
- temporary service migration.

---

## 9.4 Choosing a containment strategy

Containment decisions depend on factors such as:

- potential damage;
- evidence preservation;
- service criticality;
- time required;
- attacker behavior;
- legal requirements;
- business impact;
- available resources.

Example dilemma:

```text
Option A: Immediately disconnect server
Advantage: attacker loses access
Disadvantage: production outage and possible loss of volatile evidence

Option B: Monitor attacker temporarily
Advantage: gather intelligence
Disadvantage: attacker may cause more damage
```

Such decisions should follow pre-approved authority and risk criteria.

---

## 9.5 Containment is not eradication

This distinction is essential.

```text
Containment = stop the spread
Eradication = remove the cause
```

Example:

```text
Containment:
Disconnect infected workstation

Eradication:
Remove malware, persistence, compromised credentials and exploited vulnerability
```

---

# 10. Phase 4 — Eradication

## 10.1 Objective

Remove the root cause and attacker presence from the environment.

Possible actions:

- delete malware;
- remove persistence;
- patch exploited vulnerability;
- remove unauthorized accounts;
- reset passwords;
- rotate keys and tokens;
- rebuild compromised systems;
- remove malicious scheduled tasks;
- revoke malicious OAuth applications;
- fix insecure configuration;
- remove web shells;
- close exposed services.

---

## 10.2 Root cause vs symptom

A common mistake is removing only the visible symptom.

Example:

```text
Symptom:
Malware.exe on workstation

Root cause:
User credentials stolen through phishing, attacker deployed malware through remote management tool
```

Deleting `malware.exe` does not solve the incident if the attacker still has valid credentials.

---

## 10.3 Reimage vs clean

For heavily compromised systems, rebuilding from a trusted image may be safer than manually removing malware.

Rebuilding is often preferred when:

- attacker had administrator/root access;
- system integrity cannot be trusted;
- persistence is unknown;
- boot-level compromise is suspected;
- multiple malicious components exist.

---

# 11. Phase 5 — Recovery

## 11.1 Objective

Restore normal operations safely.

Recovery should be controlled, validated, and monitored.

---

## 11.2 Recovery actions

- restore from known-good backups;
- rebuild affected hosts;
- reinstall applications;
- restore data;
- reconnect systems gradually;
- reset credentials;
- verify patches;
- validate configuration;
- test applications;
- monitor for recurring indicators.

---

## 11.3 Validate before returning to production

Ask:

- Is the exploited vulnerability fixed?
- Are malicious accounts removed?
- Were passwords and tokens rotated?
- Are systems fully patched?
- Is EDR active?
- Are logs reaching the SIEM?
- Are restored files clean?
- Is unexpected persistence still present?
- Do integrity checks pass?

---

## 11.4 Increased monitoring after recovery

After an incident, affected systems should often receive enhanced monitoring.

Examples:

- watch for known malicious IPs;
- monitor old compromised usernames;
- detect repeated persistence mechanisms;
- track unusual process execution;
- monitor privileged actions;
- detect unusual outbound traffic.

Recovery is complete only when the organization has reasonable confidence that normal operations are stable and the threat has been addressed.

---

# 12. Phase 6 — Post-Incident Activity

## 12.1 Objective

Learn from the incident and improve the organization's future response.

This phase turns a security incident into an opportunity to strengthen controls.

---

## 12.2 Lessons learned meeting

A post-incident meeting should answer questions such as:

- What happened?
- When did it start?
- How was it detected?
- How long did detection take?
- What was the root cause?
- What worked well?
- What failed?
- Were escalation paths clear?
- Did communication work?
- Were logs sufficient?
- Were backups usable?
- Were responsibilities clear?
- Could containment have happened sooner?
- Did tools provide enough visibility?
- What controls should be improved?

The goal is **process improvement**, not blame.

---

## 12.3 Final incident report

A final report may include:

- executive summary;
- incident classification;
- affected systems;
- timeline;
- root cause;
- attack vector;
- technical indicators;
- containment actions;
- eradication actions;
- recovery actions;
- data exposure assessment;
- business impact;
- lessons learned;
- recommendations;
- owners and deadlines for corrective actions.

---

## 12.4 Metrics

Useful metrics include:

### MTTD — Mean Time To Detect

How long does it take to detect an incident?

### MTTR — Mean Time To Respond / Recover

Depending on organizational definition, how long does it take to respond to or recover from an incident?

### Other metrics

- time to containment;
- incidents by severity;
- repeated incidents;
- percentage detected internally;
- percentage of incidents caused by phishing;
- percentage of affected systems with complete logs;
- percentage of corrective actions completed on time.

Metrics should be used for improvement rather than blindly optimizing numbers.

---

# 13. Incident Response Team Roles

The project explicitly requires defined roles.

At minimum, understand these three:

1. **Incident Commander**
2. **Technical Specialist**
3. **Communications Lead**

---

## 13.1 Incident Commander

The **Incident Commander (IC)** coordinates the overall response.

Responsibilities:

- declare or confirm incident severity;
- coordinate teams;
- assign tasks;
- prioritize actions;
- approve or escalate major decisions;
- maintain situational awareness;
- coordinate with management;
- ensure documentation is maintained;
- determine when incident phases transition;
- confirm closure criteria.

The Incident Commander should not become lost in a single technical investigation.

Their role is to maintain the overall picture.

---

## 13.2 Technical Specialist

Technical specialists investigate and remediate the incident.

This may include SOC analysts, DFIR analysts, system administrators, network engineers, cloud engineers, application security specialists, and malware analysts.

Responsibilities:

- collect evidence;
- analyze logs;
- investigate endpoints;
- identify indicators of compromise;
- scope affected systems;
- determine root cause;
- contain malicious activity;
- eradicate malware/persistence;
- assist with restoration;
- validate technical remediation.

---

## 13.3 Communications Lead

The Communications Lead manages incident-related communications.

Responsibilities:

- coordinate internal communications;
- prepare management updates;
- work with legal and public relations;
- prepare customer notifications when required;
- ensure consistent messaging;
- prevent unauthorized disclosure;
- maintain communication records;
- coordinate with external stakeholders.

Technical responders should avoid independently speaking to the media or affected customers unless authorized.

---

## 13.4 Other important roles

### Legal / Privacy

- determine notification obligations;
- preserve legal privilege where applicable;
- coordinate regulators;
- advise on evidence and law enforcement.

### Management / Executive Sponsor

- authorize major business decisions;
- provide resources;
- accept business risk;
- support cross-department coordination.

### System Owner

- provide system context;
- explain business dependencies;
- validate service recovery.

### HR

Important when incidents involve:

- employees;
- insider threats;
- disciplinary actions;
- personnel records.

### Third-party providers

May assist with:

- cloud environments;
- managed security;
- forensic response;
- SaaS systems;
- cyber insurance.

---

## 13.5 RACI example

| Task | Incident Commander | Technical Specialist | Communications Lead | Legal | System Owner |
|---|---|---|---|---|---|
| Declare incident | A/R | C | I | C | C |
| Technical analysis | I | A/R | I | I | C |
| Isolate endpoint | A | R | I | I | C |
| Customer notification | C | I | R | A/C | I |
| Restore service | A | R | I | I | R/C |
| Final report | A | R/C | R/C | C | C |

Legend:

- **R** = Responsible
- **A** = Accountable
- **C** = Consulted
- **I** = Informed

---

# 14. Communication During an Incident

## 14.1 Why communication matters

A technically successful response can still become a business failure if communication is poor.

Potential stakeholders include:

- SOC;
- IT;
- management;
- legal;
- HR;
- public relations;
- customers;
- vendors;
- cloud providers;
- regulators;
- insurers;
- law enforcement.

---

## 14.2 Communication principles

Incident communication should be:

- accurate;
- timely;
- factual;
- consistent;
- need-to-know;
- appropriately confidential;
- documented.

Avoid speculation.

Bad:

```text
We think Russian hackers probably stole the database.
```

Better:

```text
We confirmed unauthorized access to the application server. Investigation of possible data access is ongoing. Attribution has not been established.
```

---

## 14.3 Out-of-band communication

If corporate email or chat may be compromised, the incident team should have a pre-approved alternative communication method.

Examples:

- dedicated emergency phone numbers;
- separate secure communication platform;
- offline contact list.

This must be prepared **before** an incident.

---

# 15. Evidence Collection and Chain of Custody

## 15.1 Why preserve evidence?

Evidence can help:

- understand root cause;
- determine attacker actions;
- support legal proceedings;
- support insurance claims;
- satisfy regulatory requirements;
- prove what did or did not happen.

---

## 15.2 Common evidence

- disk images;
- memory captures;
- logs;
- packet captures;
- email headers;
- malware samples;
- cloud audit records;
- authentication records;
- screenshots;
- configuration files;
- security alerts.

---

## 15.3 Chain of custody

The **chain of custody** documents who handled evidence and when.

Example fields:

```text
Evidence ID: EV-2026-017
Description: Disk image of workstation WS-42
Collected by: Analyst-01
Collection time: 2026-08-24 09:32 UTC
SHA-256: <hash>
Storage location: Evidence Vault A
Transfer history:
- 10:05 Analyst-01 → DFIR-02
- 11:40 DFIR-02 → Evidence Vault
```

---

## 15.4 Hashing evidence

Cryptographic hashes help verify integrity.

Example:

```bash
sha256sum workstation.img
```

If the hash changes unexpectedly, the evidence may have been modified or corrupted.

---

# 16. Incident Prioritization

Not every incident has the same impact.

Prioritization helps allocate resources.

Factors can include:

- functional impact;
- information impact;
- recoverability;
- number of systems;
- sensitivity of data;
- privileged access;
- attacker persistence;
- public exposure;
- regulatory impact.

---

## 16.1 Example severity model

### SEV-1 — Critical

Examples:

- active ransomware across production;
- confirmed theft of highly sensitive data;
- domain administrator compromise;
- major public service unavailable;
- active attacker with privileged access.

Response:

```text
Immediate full incident response activation
Executive notification
Continuous coordination
```

### SEV-2 — High

Examples:

- confirmed compromise of important server;
- malware spreading across multiple endpoints;
- privileged user account compromise with limited impact.

### SEV-3 — Medium

Examples:

- isolated malware infection;
- confirmed phishing with no privilege escalation;
- small-scale unauthorized access.

### SEV-4 — Low

Examples:

- low-impact policy violation;
- unsuccessful attack requiring limited investigation.

Severity models must be customized to the organization.

---

# 17. Building an Incident Response Plan

A professional incident response plan should include the following sections.

---

## 17.1 Purpose

Example:

```text
The purpose of this Incident Response Plan is to establish a coordinated process for detecting, analyzing, containing, eradicating, recovering from, and learning from cybersecurity incidents affecting the organization.
```

---

## 17.2 Scope

Define what is covered:

- employees;
- contractors;
- endpoints;
- servers;
- cloud systems;
- applications;
- networks;
- sensitive data;
- third-party systems where contractually applicable.

---

## 17.3 Incident definitions

Clearly define:

- event;
- alert;
- incident;
- major incident;
- data breach;
- security weakness.

---

## 17.4 Reporting methods

Example channels:

```text
security@example.invalid
Internal SOC hotline
Ticketing portal
Emergency phone number
```

Use placeholders in training material rather than real internal details.

---

## 17.5 Classification and severity

Define:

- incident type;
- severity;
- escalation level;
- response target.

---

## 17.6 Response phases

The plan should clearly explain:

```text
Preparation
Detection and Analysis
Containment
Eradication
Recovery
Post-Incident
```

---

## 17.7 Roles and responsibilities

At minimum:

- Incident Commander;
- Technical Specialist;
- Communications Lead.

Also define:

- legal;
- management;
- HR;
- system owners;
- external providers.

---

## 17.8 Escalation matrix

Example:

| Severity | Technical Lead | Incident Commander | Management | Legal | Communications |
|---|---|---|---|---|---|
| Low | Yes | Optional | No | No | No |
| Medium | Yes | Yes | Optional | If needed | If needed |
| High | Yes | Yes | Yes | Yes | Yes |
| Critical | Immediate | Immediate | Immediate | Immediate | Immediate |

---

## 17.9 Containment strategies

Predefine options for:

- compromised endpoint;
- compromised account;
- compromised web server;
- ransomware;
- data leak;
- DDoS;
- cloud compromise.

---

## 17.10 Recovery requirements

Define requirements such as:

- trusted backups;
- vulnerability fixed;
- credentials rotated;
- security monitoring active;
- system owner approval;
- change-management documentation.

---

## 17.11 Post-incident requirements

Define:

- report deadline;
- lessons-learned meeting;
- corrective action tracking;
- policy updates;
- training updates;
- metrics.

---

# 18. Example Incident Response Playbook

## Scenario: Compromised Employee Account

An employee enters credentials into a phishing website. The attacker then accesses the company's cloud environment.

---

## 18.1 Detection

Possible indicators:

- impossible-travel login;
- unfamiliar device;
- MFA reset attempt;
- suspicious inbox rule;
- abnormal download volume;
- threat-intelligence alert.

Actions:

1. Validate alert.
2. Identify affected user.
3. Review login history.
4. Review MFA events.
5. Review cloud audit logs.
6. Determine attacker actions.
7. Identify data accessed.

---

## 18.2 Containment

Actions:

1. Disable compromised account.
2. Revoke active sessions.
3. Revoke refresh tokens where applicable.
4. Reset password.
5. Remove unauthorized MFA methods.
6. Remove malicious inbox rules.
7. Block malicious domains and IPs if appropriate.
8. Identify other users who received the phishing message.

---

## 18.3 Eradication

Actions:

1. Remove attacker persistence.
2. Remove unauthorized applications/tokens.
3. Verify endpoint for malware.
4. Correct phishing-related security control gaps.
5. Rotate credentials if reuse is suspected.

---

## 18.4 Recovery

Actions:

1. Re-enable the account after verification.
2. Require secure credential reset.
3. Re-register MFA.
4. Confirm mailbox configuration.
5. Monitor account closely.
6. Validate access to business systems.

---

## 18.5 Post-incident

Questions:

- Why did the phishing email bypass controls?
- Was MFA phishing-resistant?
- Did the SIEM alert quickly enough?
- How long did the attacker have access?
- What data was accessed?
- Should conditional access be improved?
- Should user training be updated?

Possible corrective actions:

- deploy phishing-resistant MFA;
- improve email filtering;
- improve impossible-travel detection;
- restrict legacy authentication;
- shorten token lifetime for risky sessions;
- improve user awareness.

---

# 19. Post-Incident Review and Lessons Learned

## 19.1 Purpose

A post-incident review should convert observations into concrete improvements.

The process should avoid a blame culture.

The key question is:

```text
How can the organization make this incident less likely or less damaging next time?
```

---

## 19.2 Root Cause Analysis

A root cause is deeper than the immediate technical symptom.

Example:

```text
Incident:
Database exposed to Internet

Immediate cause:
Firewall allowed public access

Deeper cause:
Infrastructure-as-Code change had no security review

Organizational root cause:
No mandatory cloud security policy and no automated configuration validation
```

---

## 19.3 Five Whys example

```text
Why was customer data exposed?
Because the database was publicly reachable.

Why was it publicly reachable?
Because a firewall rule allowed 0.0.0.0/0.

Why was that rule deployed?
Because an engineer needed temporary access.

Why was temporary access not removed?
Because there was no expiration mechanism.

Why was there no expiration mechanism?
Because cloud firewall changes were not governed by an approved access workflow.
```

The final corrective action should therefore address the process, not just delete one firewall rule.

---

## 19.4 Corrective Action Plan

| Finding | Corrective Action | Owner | Priority | Due Date | Status |
|---|---|---|---|---|---|
| Weak MFA | Deploy phishing-resistant MFA | IAM Team | Critical | YYYY-MM-DD | Open |
| Incomplete logs | Enable cloud audit logs | Cloud Team | High | YYYY-MM-DD | Open |
| Slow escalation | Update on-call procedure | SOC Manager | High | YYYY-MM-DD | Open |

---

# 20. Continuous Improvement

Incident response must evolve.

Threats, infrastructure, staff, and business requirements change over time.

---

## 20.1 Update the plan regularly

Review the Incident Response Plan:

- on a defined schedule;
- after major infrastructure changes;
- after serious incidents;
- after exercises;
- after regulatory changes;
- after organizational restructuring.

---

## 20.2 Tabletop exercises

A **tabletop exercise** simulates an incident through discussion.

Example scenario:

```text
09:00 — SOC detects ransomware on finance workstation.
09:05 — Two file servers show mass encryption.
09:10 — Backup administrator account logs in from unknown IP.
09:15 — Customer portal becomes unavailable.
```

Participants must decide:

- who leads;
- who gets notified;
- whether systems are disconnected;
- whether backups are trustworthy;
- whether customers must be informed;
- whether law enforcement is contacted.

Tabletop exercises reveal process gaps without causing real damage.

---

## 20.3 Technical exercises

Examples:

- restore backups;
- investigate synthetic malware alert;
- isolate endpoint via EDR;
- recover a server;
- rotate compromised secrets;
- activate alternate communication channel.

---

## 20.4 Improve detection rules

After an incident, convert known attacker behaviors into detection logic.

Example:

```text
Incident discovered attacker creating hidden scheduled task.

Improvement:
Create SIEM alert for suspicious scheduled task creation on critical servers.
```

---

## 20.5 Update security awareness

If an incident succeeds through social engineering, training can be improved using anonymized lessons from the real incident.

Do not expose the affected employee unnecessarily.

---

# 21. National Vulnerability Database — NVD

## 21.1 What is the NVD?

The **National Vulnerability Database (NVD)** is a U.S. government repository of standards-based vulnerability management data maintained by NIST.

It enriches published CVE records with useful information for vulnerability management.

Typical information may include:

- CVE identifier;
- vulnerability description;
- CVSS metrics;
- CWE weakness classification;
- CPE product applicability;
- references;
- publication and modification dates.

---

## 21.2 NVD is not the same as CVE

This distinction is important.

### CVE

A standardized identifier for a publicly disclosed vulnerability.

Example:

```text
CVE-2025-12345
```

### NVD

A database that **enriches CVE information** with additional vulnerability-management data.

Simplified:

```text
CVE = identifier / vulnerability record ecosystem
NVD = NIST vulnerability database and enrichment
```

---

## 21.3 Using NVD during incident response

During an incident, NVD can help analysts understand whether:

- a vulnerable product version is known;
- an affected component has published vulnerabilities;
- a vulnerability has a high CVSS score;
- a CVE relates to the observed attack vector;
- vendor references or patches exist.

However, do not make response decisions from CVSS alone.

A vulnerability with a lower CVSS score may be extremely important if it affects a critical public-facing production system.

---

# 22. CVE, CVSS, CWE and CPE

These acronyms are frequently seen together.

---

## 22.1 CVE — Common Vulnerabilities and Exposures

CVE provides standardized identifiers for publicly disclosed cybersecurity vulnerabilities.

Format:

```text
CVE-YEAR-NUMBER
```

Example:

```text
CVE-2026-12345
```

The CVE ID allows vendors, scanners, databases, researchers, and organizations to refer to the same vulnerability consistently.

---

## 22.2 CVSS — Common Vulnerability Scoring System

CVSS provides standardized severity metrics.

A score commonly ranges from:

```text
0.0 → 10.0
```

Typical severity bands in CVSS v3.x are:

| Score | Severity |
|---:|---|
| 0.0 | None |
| 0.1–3.9 | Low |
| 4.0–6.9 | Medium |
| 7.0–8.9 | High |
| 9.0–10.0 | Critical |

NVD supports newer CVSS data as the ecosystem evolves, including CVSS v4.0.

### Important

CVSS is a **severity metric**, not a complete risk score for your organization.

Risk also depends on:

- asset criticality;
- exposure;
- exploit availability;
- active exploitation;
- data sensitivity;
- compensating controls;
- business impact.

---

## 22.3 CWE — Common Weakness Enumeration

CWE classifies **types of software weaknesses**.

Examples:

- SQL injection;
- buffer overflow;
- path traversal;
- improper authentication;
- use-after-free.

Simplified:

```text
CVE = specific vulnerability
CWE = category/type of weakness
```

---

## 22.4 CPE — Common Platform Enumeration

CPE provides standardized names for products and platforms.

It helps answer:

```text
Which exact software/product/version is affected?
```

CPE information is particularly useful for vulnerability scanners and automated vulnerability management.

---

## 22.5 Relationship

```text
Product
  ↓
CPE identifies the platform
  ↓
CVE identifies a specific vulnerability
  ↓
CWE describes the weakness type
  ↓
CVSS describes severity characteristics
  ↓
NVD aggregates/enriches the vulnerability-management information
```

---

# 23. Anonymization and Sensitive Information

The project explicitly requires **anonymization**.

Incident reports often contain sensitive information.

Examples:

- usernames;
- employee names;
- customer information;
- IP addresses;
- email addresses;
- API keys;
- access tokens;
- passwords;
- internal hostnames;
- proprietary system names;
- forensic evidence;
- personal data.

---

## 23.1 Why anonymize?

Anonymization reduces the risk that training documents, reports, screenshots, or examples accidentally disclose sensitive information.

---

## 23.2 Good anonymization examples

Instead of:

```text
jules.moleins@company.com
```

Use:

```text
user01@example.invalid
```

Instead of:

```text
192.0.2.14 belongs to FINANCE-PC-JULES
```

Use:

```text
Affected endpoint: WS-042
```

Instead of:

```text
API_KEY=sk_live_REALSECRET
```

Use:

```text
API_KEY=<REDACTED>
```

---

## 23.3 Preserve analytical value

Anonymization should not destroy useful context.

Bad:

```text
User did something on a computer.
```

Better:

```text
User-07 authenticated to APP-SRV-02 using a privileged account from an unusual external IP address.
```

The second version preserves the security relationship without exposing identity.

---

# 24. NIST CSF and Incident Response Mapping

CSF 2.0 and SP 800-61 can be understood together.

| Incident Response Activity | Related CSF Function |
|---|---|
| Define policies, responsibilities, authority | GOVERN |
| Inventory assets and understand risks | IDENTIFY |
| Implement MFA, backups, hardening | PROTECT |
| Monitor alerts and detect incidents | DETECT |
| Analyze, contain and communicate | RESPOND |
| Restore services and improve resilience | RECOVER |

However, the relationship is not strictly linear.

All CSF Functions support incident response.

Example:

```text
GOVERN tells us who has authority.
IDENTIFY tells us which assets matter.
PROTECT reduces attack opportunities.
DETECT discovers malicious activity.
RESPOND contains and mitigates it.
RECOVER restores operations.
```

---

# 25. Common Mistakes

## Mistake 1 — No preparation

```text
"We'll decide what to do if something happens."
```

Why it fails:

- unclear roles;
- lost time;
- communication chaos;
- no evidence process;
- no escalation path.

---

## Mistake 2 — Immediately wipe the compromised system

This may destroy useful evidence.

The correct action depends on risk and containment needs.

---

## Mistake 3 — Confusing containment with eradication

```text
Disconnecting an infected laptop ≠ removing the root cause.
```

---

## Mistake 4 — Restoring before fixing the vulnerability

If a vulnerable server is restored without remediation, it may be compromised again immediately.

---

## Mistake 5 — Resetting only one password

If an attacker stole tokens, API keys, service credentials, or domain credentials, one password reset may be insufficient.

---

## Mistake 6 — Focusing only on technical indicators

Incident response also involves:

- business impact;
- legal obligations;
- communication;
- customer impact;
- reputation;
- continuity.

---

## Mistake 7 — No lessons learned

Without post-incident review, the organization may suffer the same incident again.

---

## Mistake 8 — Using CVSS as the only risk factor

A CVSS 10 vulnerability on an isolated lab machine may be less urgent than a CVSS 7.5 vulnerability actively exploited on a public production server.

---

# 26. Practical Incident Response Checklist

## Preparation

- [ ] Incident Response Policy approved
- [ ] Incident Response Plan documented
- [ ] Incident Commander identified
- [ ] Technical responders identified
- [ ] Communications Lead identified
- [ ] Contact lists available offline
- [ ] Logging centralized
- [ ] Time synchronization configured
- [ ] EDR/SIEM alerting tested
- [ ] Backups tested
- [ ] Evidence storage available
- [ ] Playbooks created
- [ ] Tabletop exercises performed

## Detection and Analysis

- [ ] Validate alert
- [ ] Create incident record
- [ ] Record detection time
- [ ] Identify affected assets
- [ ] Identify affected accounts
- [ ] Collect relevant logs
- [ ] Build timeline
- [ ] Determine scope
- [ ] Identify indicators of compromise
- [ ] Assign severity
- [ ] Escalate appropriately

## Containment

- [ ] Isolate affected systems if appropriate
- [ ] Disable compromised accounts
- [ ] Revoke active sessions/tokens
- [ ] Block malicious indicators
- [ ] Preserve evidence
- [ ] Document every containment action
- [ ] Confirm that spread has stopped

## Eradication

- [ ] Remove malware/persistence
- [ ] Remove unauthorized accounts
- [ ] Patch exploited vulnerabilities
- [ ] Rotate compromised credentials and secrets
- [ ] Rebuild systems where integrity cannot be trusted
- [ ] Validate that root cause has been addressed

## Recovery

- [ ] Restore from trusted backup if necessary
- [ ] Validate system integrity
- [ ] Verify security controls
- [ ] Return systems gradually
- [ ] Monitor for recurrence
- [ ] Confirm business functionality

## Post-Incident

- [ ] Complete incident timeline
- [ ] Conduct lessons-learned meeting
- [ ] Identify root cause
- [ ] Document impact
- [ ] Create corrective actions
- [ ] Assign owners and deadlines
- [ ] Update detection rules
- [ ] Update playbooks
- [ ] Update training
- [ ] Track improvements to completion

---

# 27. Incident Report Template

The following template can be reused for a professional assignment.

---

## Incident Identification

```text
Incident ID: INC-YYYY-XXX
Date detected: YYYY-MM-DD HH:MM UTC
Severity: Low / Medium / High / Critical
Status: Open / Contained / Recovered / Closed
Incident Commander: <ROLE OR ANONYMIZED NAME>
```

---

## Executive Summary

Briefly explain:

- what happened;
- when it happened;
- systems affected;
- business impact;
- current status.

---

## Detection

Document:

- detection source;
- initial alert;
- first observed indicator;
- validation process.

---

## Incident Timeline

| Time | Event |
|---|---|
| HH:MM | Initial suspicious activity |
| HH:MM | Alert generated |
| HH:MM | Analyst begins investigation |
| HH:MM | Incident confirmed |
| HH:MM | Containment begins |
| HH:MM | Threat eradicated |
| HH:MM | Service restored |

---

## Scope

List:

- affected systems;
- affected accounts;
- data potentially affected;
- known indicators of compromise.

Use anonymized identifiers.

---

## Root Cause

Explain the technical and organizational causes.

---

## Containment Actions

Example:

```text
- Endpoint WS-042 isolated using EDR.
- Account USER-07 disabled.
- Active cloud sessions revoked.
- Malicious domain blocked at DNS layer.
```

---

## Eradication Actions

Example:

```text
- Malicious persistence removed.
- Vulnerable software patched.
- Compromised credentials rotated.
- Affected endpoint rebuilt from trusted image.
```

---

## Recovery Actions

Example:

```text
- Application restored from verified backup.
- EDR and logging validated.
- System reintroduced into production.
- Enhanced monitoring enabled for 72 hours.
```

---

## Impact Assessment

Consider:

- confidentiality;
- integrity;
- availability;
- financial impact;
- operational impact;
- legal impact;
- reputational impact.

---

## Lessons Learned

Document:

- what worked;
- what failed;
- process gaps;
- tool gaps;
- communication gaps.

---

## Corrective Actions

| Action | Owner | Priority | Deadline | Status |
|---|---|---|---|---|
| Example action | Security Team | High | YYYY-MM-DD | Open |

---

# 28. Revision Sheet — What to Remember

## NIST CSF 2.0

Remember the six Functions:

```text
GOVERN
IDENTIFY
PROTECT
DETECT
RESPOND
RECOVER
```

Mnemonic idea:

```text
Govern what you do,
Identify what matters,
Protect it,
Detect attacks,
Respond quickly,
Recover safely.
```

---

## NIST SP 800-61 Rev. 2 lifecycle

Remember:

```text
Preparation
Detection & Analysis
Containment
Eradication
Recovery
Post-Incident
```

NIST Rev. 2 officially groups the operational middle section as:

```text
Containment, Eradication & Recovery
```

---

## Three mandatory roles for this project

### Incident Commander

Coordinates the overall response and decisions.

### Technical Specialist

Investigates, contains, eradicates and supports recovery.

### Communications Lead

Coordinates accurate, controlled communications.

---

## Key differences

### Event vs Incident

```text
Event = observable occurrence
Incident = security event requiring coordinated response
```

### Containment vs Eradication

```text
Containment = stop spread / limit damage
Eradication = remove attacker/root cause
```

### Eradication vs Recovery

```text
Eradication = remove threat
Recovery = safely restore business operations
```

### CVE vs CWE vs CVSS vs CPE

```text
CVE  = specific vulnerability ID
CWE  = weakness category
CVSS = severity metrics
CPE  = standardized product/platform name
NVD  = NIST vulnerability database/enrichment
```

---

## Golden rule of incident response

```text
Detect → Understand → Contain → Remove → Restore → Learn
```

---

# 29. Practice Questions

## Question 1

What is the main purpose of the Preparation phase?

### Answer

To establish the people, processes, tools, communications, logging, backups, policies, and procedures required to respond effectively before an incident occurs.

---

## Question 2

What is the difference between containment and eradication?

### Answer

Containment limits damage and prevents spread. Eradication removes malware, persistence, compromised credentials, vulnerabilities, and other root causes.

---

## Question 3

Why should systems be monitored after recovery?

### Answer

To detect recurrence, remaining persistence, missed systems, attacker re-entry, or unexpected behavior after restoration.

---

## Question 4

Who coordinates the overall incident response?

### Answer

The **Incident Commander**.

---

## Question 5

What is the Technical Specialist responsible for?

### Answer

Technical investigation, evidence collection, scoping, containment, eradication, technical recovery support, and validation.

---

## Question 6

What is the Communications Lead responsible for?

### Answer

Coordinating accurate and authorized internal/external communication with management, employees, customers, legal, PR, and other stakeholders.

---

## Question 7

What is the purpose of a post-incident review?

### Answer

To identify root cause, determine what worked or failed, document lessons learned, assign corrective actions, and improve future incident response.

---

## Question 8

What are the six NIST CSF 2.0 Functions?

### Answer

```text
Govern
Identify
Protect
Detect
Respond
Recover
```

---

## Question 9

What is the NVD?

### Answer

The National Vulnerability Database is a NIST-maintained U.S. government repository of standards-based vulnerability-management data that enriches CVE information with data such as CVSS metrics, CWE classifications, CPE applicability information, and references.

---

## Question 10

Why should sensitive information be anonymized in incident reports used for coursework or broad distribution?

### Answer

To reduce the risk of exposing personal, confidential, security-sensitive, or operational information while keeping enough context for analysis.

---

## Question 11

Why is CVSS alone insufficient for vulnerability prioritization?

### Answer

Because organizational risk also depends on asset importance, exposure, exploitability, active exploitation, data sensitivity, business impact, and existing controls.

---

## Question 12

What does a CSF Current Profile represent?

### Answer

The organization's present cybersecurity posture relative to selected CSF outcomes.

---

## Question 13

What does a CSF Target Profile represent?

### Answer

The desired future cybersecurity posture the organization wants to achieve.

---

## Question 14

What is the purpose of a tabletop exercise?

### Answer

To test incident response roles, decisions, communication, procedures, and coordination through a simulated scenario without causing a real incident.

---

## Question 15

What should happen after lessons learned are identified?

### Answer

They should become tracked corrective actions with owners, priorities, deadlines, and validation that improvements were actually implemented.

---

# 30. Conclusion

NIST provides organizations with a structured approach to cybersecurity and incident response.

The **NIST Cybersecurity Framework 2.0** organizes cybersecurity risk management around six functions:

```text
GOVERN → IDENTIFY → PROTECT → DETECT → RESPOND → RECOVER
```

The classic **NIST SP 800-61 Rev. 2** incident response lifecycle teaches responders to manage incidents through:

```text
Preparation
      ↓
Detection & Analysis
      ↓
Containment
      ↓
Eradication
      ↓
Recovery
      ↓
Post-Incident Activity
      ↺ Continuous Improvement
```

A mature incident response capability depends on more than technical tools. It also requires:

- clear authority;
- trained people;
- defined roles;
- documented playbooks;
- reliable logging;
- tested backups;
- evidence preservation;
- controlled communication;
- post-incident learning;
- continuous improvement.

The most important concept is that incident response is a **continuous cycle**.

Every incident should make the organization better prepared for the next one.

---

# 31. References

## NIST Cybersecurity Framework 2.0

- NIST, **The NIST Cybersecurity Framework (CSF) 2.0**, February 26, 2024.  
  https://doi.org/10.6028/NIST.CSWP.29

- NIST Cybersecurity Framework website:  
  https://www.nist.gov/cyberframework

- NIST CSF 2.0 Resource & Overview Guide:  
  https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.1299.pdf

## NIST Incident Response

- NIST, **SP 800-61 Rev. 2 — Computer Security Incident Handling Guide**, August 2012.  
  https://doi.org/10.6028/NIST.SP.800-61r2

- NIST, **SP 800-61 Rev. 3 — Incident Response Recommendations and Considerations for Cybersecurity Risk Management: A CSF 2.0 Community Profile**, April 2025.  
  https://doi.org/10.6028/NIST.SP.800-61r3

- NIST Incident Response project:  
  https://csrc.nist.gov/projects/incident-response

## National Vulnerability Database

- NIST National Vulnerability Database:  
  https://nvd.nist.gov/

- NVD General Information:  
  https://nvd.nist.gov/general

---

## Final One-Minute Summary

```text
NIST CSF 2.0:
GOVERN — IDENTIFY — PROTECT — DETECT — RESPOND — RECOVER

NIST SP 800-61 Rev. 2:
PREPARATION
DETECTION & ANALYSIS
CONTAINMENT
ERADICATION
RECOVERY
POST-INCIDENT

Roles:
Incident Commander = coordinates
Technical Specialist = investigates/remediates
Communications Lead = communicates

Vulnerability terms:
CVE  = vulnerability ID
CWE  = weakness type
CVSS = severity metric
CPE  = product/platform naming
NVD  = NIST vulnerability database

Core philosophy:
Prepare before the incident.
Detect quickly.
Contain the damage.
Remove the cause.
Recover safely.
Learn and improve.
```
