# CompTIA Security+ SY0-701 — 8-Week Study Roadmap
# For French-speaking study groups

Exam: 90 questions · 90 minutes · Pass score: 750/900
Resources: Professor Messer free videos (121 videos, 15h11min) + Official CompTIA PDF
Playlist: https://www.youtube.com/playlist?list=PLG49S3nxzAnl4QDVqK-hOnoqcSKEIDDuv
Course index: https://www.professormesser.com/sy0-701-certification-course/
Exam objectives PDF (free): https://www.professormesser.com/objectives

---

## HOW TO STUDY: DOMAIN-BY-DOMAIN (not chapter-by-chapter)

Follow Professor Messer's domain order. The official PDF lessons match exactly.
Daily formula every week:
  Mon/Tue  → Watch Prof. Messer videos for that domain
  Wed/Thu  → Read the matching official PDF lesson
  Fri      → Group activity or scenario exercise
  Sat      → Quiz together on the week's material
  Sun      → REST (no new material — memory consolidates during recovery)

---

## BEFORE YOU START (Day 1)

- Download the free exam objectives PDF from CompTIA
- Watch Section 0.1: "How to Pass Your SY0-701 Exam" (10 min)
- Show your group how the exam objectives map 1:1 to Prof. Messer's video index
- Tip: YouTube auto-translated French subtitles work well for technical content

---

## THE 5 EXAM DOMAINS

  Domain 1 — General Security Concepts         12%
  Domain 2 — Threats, Vulnerabilities & Mitigations  22%  ← 2 weeks
  Domain 3 — Security Architecture             18%
  Domain 4 — Security Operations               28%  ← 2 weeks (highest weight)
  Domain 5 — Security Program Management       20%

---

## WEEK 1 — Domain 1: General Security Concepts (12%)

Monday
  - Watch: Section 1.1 — Security Controls (12 min)
  - Watch: Section 1.2 — CIA Triad, Non-repudiation, AAA, Gap Analysis, Zero Trust (~38 min)

Tuesday
  - Read: Official PDF Lesson 1 — "Fundamental Security Concepts"
  - Focus on: control categories table and AAA explanations

Wednesday
  - Watch: Section 1.3 — Cryptography (hashing, symmetric/asymmetric, PKI, certificates) (~55 min)

Thursday
  - Read: Official PDF Lesson 1 — crypto sections
  - Activity: Draw a diagram of how PKI/certificate chain works

Friday
  - Watch: Section 1.4 — Authentication & Authorization (MFA, biometrics, access control models)

Saturday
  - Group quiz: 10 questions on all of Domain 1
  - Discuss wrong answers together
  - Make flashcards for crypto terms

Sunday — REST
  - Review personal notes only, no new material

Teacher tip: On Day 1 (Monday), show the exam objectives PDF next to the Prof. Messer
course index. Seeing they align 1:1 removes study anxiety for the whole group.

---

## WEEK 2 — Domain 2 Part 1: Threat Actors & Vulnerability Types (Domain 2 = 22%)

Monday
  - Watch: Section 2.1 — Threat actors, motivations, threat vectors
    (nation-state, insider threat, phishing, supply chain) (~40 min)

Tuesday
  - Read: Official PDF Lesson 2 — Threat actors
  - Activity: Make a comparison table: Actor type → Motivation → Typical vector

Wednesday
  - Watch: Section 2.2 — Vulnerability types
    (application, OS, zero-day, misconfiguration, CVSS scoring) (~45 min)

Thursday
  - Read: Official PDF Lesson 3 — Vulnerabilities
  - Focus on: CVSS base score components (AV, AC, PR, UI)

Friday
  - Group exercise: Given a scenario, identify the threat actor type and vulnerability class
  - Use real-world breach examples (SolarWinds, Colonial Pipeline, etc.)

Saturday
  - Quiz: 10 scenario-based questions on threat actors + vulnerabilities

Sunday — REST

Teacher tip: Domain 2 is the heaviest by exam weight (22%). Split it over 2 weeks
so it does not overwhelm beginners.

---

## WEEK 3 — Domain 2 Part 2: Malware, Attacks & Mitigations

Monday
  - Watch: Section 2.3 Part 1 — Malware types
    (ransomware, trojans, rootkits, worms, spyware, keyloggers, botnets) (~45 min)

Tuesday
  - Watch: Section 2.3 Part 2 — Attack techniques
    (SQL injection, XSS, buffer overflow, CSRF, pass-the-hash, privilege escalation) (~50 min)

Wednesday
  - Read: Official PDF Lessons 4–5 — Malware and attacks
  - Activity: Build a flashcard deck — Attack name → What it does → Mitigation

Thursday
  - Watch: Section 2.4 — Mitigation techniques
    (patching, hardening, segmentation, monitoring, encryption) (~35 min)

Friday
  - Read: Official PDF Lesson 5 — Mitigations
  - Re-read any attack section that felt unclear

Saturday
  - Full Domain 2 quiz: 20 questions covering both weeks 2 and 3
  - Identify which sub-topic needs more review

Sunday — REST
  - Flashcard deck review only

Teacher tip: On Saturday, spend 15 minutes calling out attack vs mitigation pairs
out loud as a group. Speaking answers aloud improves retention more than re-reading.

---

## WEEK 4 — Domain 3: Security Architecture (18%)

Monday
  - Watch: Section 3.1 — Network infrastructure
    (on-prem vs cloud vs hybrid, VLANs, DMZ, microsegmentation, SD-WAN, SASE) (~45 min)

Tuesday
  - Watch: Section 3.2 — Security devices
    (firewalls: stateful/NGFW/WAF, IDS/IPS, proxies, VPN concentrators, DNS filtering) (~50 min)

Wednesday
  - Read: Official PDF Lessons 6–7
  - Activity: Draw a network diagram by hand:
    Internet → Firewall → DMZ → Internal network (label everything)

Thursday
  - Watch: Section 3.3 — Secure protocols
    (HTTPS/TLS, SSH, SFTP, DNSSEC, IPsec, S/MIME) + port numbers (~40 min)

Friday
  - Watch: Section 3.4 — Cloud & virtualization
    (IaaS/PaaS/SaaS, shared responsibility model, containers, CASB) (~40 min)

Saturday
  - Domain 3 quiz: include port number questions
  - Key ports to know:
      443  = HTTPS        22   = SSH          80   = HTTP
      3389 = RDP          53   = DNS          25   = SMTP
      110  = POP3         143  = IMAP         445  = SMB
      389  = LDAP         636  = LDAPS        21   = FTP

Sunday — REST
  - Review the hand-drawn network diagram and correct any mistakes

Teacher tip: Port numbers appear on almost every real Security+ exam. Drill them
as a group on Saturday — make it a rapid-fire game.

---

## WEEK 5 — Domain 4 Part 1: IAM & Endpoint Security (Domain 4 = 28%)

Monday
  - Watch: Section 4.1 — Identity & Access Management
    (SSO, federation, OAuth, SAML, OpenID Connect, least privilege, PAM) (~50 min)

Tuesday
  - Read: Official PDF Lesson 10 — IAM
  - Activity: Compare OAuth vs SAML vs OpenID Connect in a table (exam loves this)

Wednesday
  - Watch: Section 4.2 — Endpoint security
    (EDR/XDR, patch management, hardening, sandboxing, secure coding) (~45 min)

Thursday
  - Read: Official PDF Lessons 11–12
  - Focus on: EDR vs XDR vs traditional AV — a common exam trap

Friday
  - Group scenario: An employee leaves the company today.
    Walk through every deprovisioning step together.

Saturday
  - Quiz: 10 scenario-based questions on IAM + endpoint security
  - Example question type: "A user leaves — what should happen FIRST?"

Sunday — REST

---

## WEEK 6 — Domain 4 Part 2: SIEM, Monitoring & Incident Response

Monday
  - Watch: Section 4.3 — Monitoring & SIEM
    (log sources, correlation, SOAR, netflow, alerts, continuous monitoring) (~45 min)

Tuesday
  - Read: Official PDF Lessons 13–14
  - Focus on: SIEM vs SOAR — they are different and both tested

Wednesday
  - Watch: Section 4.4 — Incident Response
    (IR lifecycle, digital forensics, chain of custody, evidence handling) (~50 min)

Thursday
  - Read: Official PDF Lessons 15–17
  - MEMORIZE the 6 IR steps in exact order:
      1. Prepare
      2. Identify
      3. Contain
      4. Eradicate
      5. Recover
      6. Lessons Learned

Friday — TABLETOP EXERCISE (most important session of the course)
  - Scenario: You find ransomware on a workstation.
  - Walk through each IR step as a group and decide what action to take at each stage.

Saturday
  - Full Domain 4 quiz: 20 questions covering both weeks 5 and 6
  - IR step ordering questions are almost guaranteed on the real exam

Sunday — REST
  - Write the 6 IR steps from memory 3 times. That is all.

Teacher tip: The Friday tabletop exercise is the most valuable session of the entire
course. Performance-based questions on the exam test exactly this kind of thinking.

---

## WEEK 7 — Domain 5: GRC, Compliance, Privacy & Awareness (20%)

Monday
  - Watch: Section 5.1 — Governance & Risk
    (policies, standards, procedures, risk register, qualitative vs quantitative analysis) (~45 min)

Tuesday
  - Watch: Section 5.2 — Compliance & regulations
    (GDPR, HIPAA, PCI-DSS, SOC 2, ISO 27001, audits, attestation) (~40 min)

Wednesday
  - Read: Official PDF Lessons 20–22
  - Activity: Make a regulation comparison table:

      Regulation  | Applies to          | Protects
      ----------- | ------------------- | ------------------
      GDPR        | Any org with EU data| Personal data (EU)
      HIPAA       | US healthcare       | Medical records
      PCI-DSS     | Card payments       | Cardholder data
      SOC 2       | Service providers   | Customer data
      ISO 27001   | Any organization    | Information systems

Thursday
  - Watch: Sections 5.3–5.4 — Data classification, privacy, security awareness,
    phishing simulations (~40 min)

Friday
  - Read: Official PDF Lessons 23–28
  - Focus on: GDPR right to be forgotten, data controller vs processor distinction

Saturday
  - Domain 5 quiz: 10 questions
  - These are reading-comprehension style — context understanding matters more
    than memorizing acronyms

Sunday — REST
  - Review regulation comparison table from Wednesday

---

## WEEK 8 — Full Review, Practice Exams & Exam Day

Monday
  - Watch: Latest Prof. Messer Study Group replay
    (https://www.professormesser.com/security-plus/sy0-701/sy0-701-study-group/)
  - He covers the most common exam traps from real student feedback

Tuesday — MOCK EXAM 1
  - Full timed exam: 90 questions, 90 minutes, no breaks
  - Score it and list which domains had the most wrong answers

Wednesday — Targeted review
  - Go back to the weakest domain from Tuesday only
  - Re-watch 2–3 key videos + re-read that PDF lesson
  - Do NOT try to re-study everything

Thursday — MOCK EXAM 2
  - Second full timed exam
  - Compare score with Tuesday
  - 80%+ means you are ready

Friday — Light review only
  - Flashcards: port numbers, IR steps, malware types, regulation names
  - No new videos, no full chapters

Saturday — COMPLETE REST
  - Sleep early, eat well
  - Memory consolidates during sleep — studying tonight hurts more than it helps

Sunday — EXAM DAY
  - Arrive early
  - Read each question twice before answering
  - For scenario questions: eliminate 2 obviously wrong answers first,
    then choose between the remaining two
  - You have 60 seconds per question on average — do not panic

---

## KEY RESOURCES

  Free videos (121 total):
  https://www.professormesser.com/security-plus/sy0-701/sy0-701-video/sy0-701-comptia-security-plus-course/

  YouTube playlist:
  https://www.youtube.com/playlist?list=PLG49S3nxzAnl4QDVqK-hOnoqcSKEIDDuv

  Exam objectives (free PDF):
  https://www.professormesser.com/objectives

  Study Group replays (free):
  https://www.professormesser.com/security-plus/sy0-701/sy0-701-study-group/sy0-701-security-study-group-replays/

  Course Notes PDF (~$15, highly recommended):
  https://www.professormesser.com/sy0-701-success-bundle/

  Official CompTIA Student Guide (paid):
  https://www.comptia.org/training/books/security-sy0-701-study-guide

---

## QUICK REFERENCE — THINGS THE EXAM ALWAYS TESTS

Port numbers (memorize these):
  443=HTTPS  22=SSH  80=HTTP  3389=RDP  53=DNS  25=SMTP
  110=POP3  143=IMAP  445=SMB  389=LDAP  636=LDAPS  21=FTP

Incident Response steps (in exact order):
  1-Prepare  2-Identify  3-Contain  4-Eradicate  5-Recover  6-Lessons Learned

Security control categories:
  Technical / Managerial / Operational / Physical

Security control types:
  Preventive / Detective / Corrective / Deterrent / Compensating / Directive

Authentication protocols:
  OAuth = authorization  |  SAML = SSO federation  |  OpenID Connect = identity layer on OAuth

Key regulation scope:
  GDPR=EU personal data  |  HIPAA=US medical  |  PCI-DSS=payment cards

---

Good luck to your group!
Estimated total study time: 8 weeks / ~6–8 hours per week
