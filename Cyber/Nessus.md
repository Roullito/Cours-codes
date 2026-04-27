# Nessus: Using Nessus for Security Vulnerability Scanning

**Article / Google Docs-ready course**  
**Module:** Vulnerability Management & Nessus  
**Audience:** cybersecurity students, junior pentesters, SOC analysts, system administrators  
**Language:** English  
**Scope:** authorized scanning only

---

## Table of Contents

1. Introduction
2. Legal and Ethical Notice
3. Vulnerability Management Fundamentals
4. Where Vulnerability Scanning Fits in Vulnerability Management
5. What Nessus Is
6. Nessus Editions and Licensing
7. Nessus Architecture and Core Concepts
8. Installing Nessus on Linux, Windows, and macOS
9. Initial Setup and Activation
10. Configuring Nessus for Performance, Stability, and Accuracy
11. Navigating the Nessus Interface
12. Scan Templates and Policies
13. Creating, Scheduling, Launching, and Managing Scans
14. Web Application Testing with Nessus
15. Credentialed vs Non-Credentialed Scans
16. Understanding Plugins and Vulnerability Detection
17. Reading and Interpreting Results
18. Vulnerability Severity: CVSS, Risk, Context, and Prioritization
19. Reporting in Nessus
20. Remediation Workflow
21. Verification and Rescanning
22. Common Mistakes and False Positives
23. Practical Exercise: Scan a Website with Nessus Essentials
24. Report Template for the Exercise
25. Screenshots Checklist
26. Cheat Sheet
27. Glossary
28. References

---

## 1. Introduction

Nessus is one of the most widely used vulnerability scanners in cybersecurity. It helps security teams identify weaknesses in systems, services, network devices, operating systems, applications, and web-facing assets.

A vulnerability scanner does not replace human analysis. It automates discovery and detection, but the analyst must still validate the findings, assess real business impact, remove false positives, and define realistic remediation actions.

In this course, you will learn how Nessus fits into vulnerability management, how to install and configure it, how to create and run scans, how to analyze results, and how to write a professional report after scanning a website.

By the end of this module, you should be able to explain Nessus clearly, operate it safely, and produce structured vulnerability reports suitable for a technical audience.

---

## 2. Legal and Ethical Notice

Vulnerability scanning can generate large amounts of traffic, trigger alerts, affect fragile services, or be interpreted as hostile activity. Only scan systems you own or systems for which you have explicit written authorization.

For public websites, even “simple” vulnerability scanning may violate terms of service or legal boundaries if you do not have permission. In an educational context, use intentionally vulnerable labs, school-provided targets, private test environments, or clearly authorized targets.

Before launching any scan, confirm:

- the exact target scope;
- whether scanning is allowed;
- the accepted scan intensity;
- the authorized time window;
- whether authentication is permitted;
- whether intrusive checks are allowed;
- who must be contacted if the scan causes issues.

A good security professional does not simply ask, “Can I find vulnerabilities?” They ask, “Can I test safely, legally, and in a way that helps the owner reduce risk?”

---

## 3. Vulnerability Management Fundamentals

### 3.1 What Is Vulnerability Management?

Vulnerability management is the continuous process of identifying, evaluating, prioritizing, remediating, and verifying security weaknesses across an environment.

It is not a one-time scan. It is a lifecycle.

A mature vulnerability management process usually includes:

1. **Asset inventory**  
   Knowing what systems, applications, cloud services, endpoints, databases, and network devices exist.

2. **Vulnerability discovery**  
   Detecting weaknesses through scanning, manual testing, code review, threat intelligence, configuration audits, and penetration testing.

3. **Risk assessment**  
   Understanding how severe each weakness is, how exposed the asset is, whether exploitation is likely, and what business impact exploitation would have.

4. **Prioritization**  
   Deciding what must be fixed first. Critical internet-facing vulnerabilities usually take priority over low-risk internal findings.

5. **Remediation**  
   Applying patches, changing configurations, disabling vulnerable services, adding compensating controls, or redesigning insecure components.

6. **Verification**  
   Confirming that the issue is actually fixed through rescanning, manual validation, or evidence review.

7. **Reporting and tracking**  
   Documenting risk, ownership, deadlines, exceptions, and remediation progress.

8. **Continuous improvement**  
   Reducing repeated findings by improving patch management, hardening baselines, secure development practices, monitoring, and asset governance.

### 3.2 Vulnerability vs Risk

A vulnerability is a weakness. Risk is the potential impact of that weakness being exploited in a specific context.

Example:

- A missing security header on an internal test page may be low risk.
- The same missing control on a high-value public banking portal may be more important.
- A critical remote code execution vulnerability on an exposed server is usually urgent.
- The same vulnerability on an isolated, decommissioned, unreachable lab VM may be less urgent.

Nessus helps detect vulnerabilities, but the analyst decides the real risk.

### 3.3 Vulnerability Management vs Penetration Testing

| Vulnerability Management | Penetration Testing |
|---|---|
| Continuous process | Point-in-time assessment |
| Broad coverage | Focused exploitation path |
| Often scanner-driven | Often manual and adversarial |
| Finds known vulnerabilities and misconfigurations | Demonstrates exploitability and impact |
| Supports patching and remediation programs | Tests defenses and business impact |

Both are useful. Nessus is mainly a vulnerability scanning and assessment tool, not a full penetration testing framework.

---

## 4. Where Vulnerability Scanning Fits in Vulnerability Management

Vulnerability scanning is the automated detection phase of vulnerability management.

It helps answer questions such as:

- Which hosts are alive?
- Which ports and services are exposed?
- Which software versions are running?
- Which known CVEs may apply?
- Which insecure configurations are present?
- Which SSL/TLS weaknesses exist?
- Which web application issues may be detectable automatically?
- Which patches appear to be missing?

However, scanners have limits.

They can miss vulnerabilities when:

- the scan is unauthenticated;
- services block scanning traffic;
- version detection is inaccurate;
- the vulnerability requires business logic understanding;
- the application is heavily JavaScript-based;
- custom code has vulnerabilities unknown to scanner plugins;
- a WAF or reverse proxy hides backend behavior.

They can also report false positives when:

- a banner exposes an old version but the software is backported and patched;
- a plugin detects a pattern that is not exploitable;
- the scanner lacks context;
- network middleboxes modify responses.

A vulnerability scan is therefore a strong starting point, not the final truth.

---

## 5. What Nessus Is

Nessus is a vulnerability assessment tool developed by Tenable. It uses a large plugin database to detect vulnerabilities, misconfigurations, missing patches, exposed services, weak protocols, insecure certificates, web application issues, and compliance-related weaknesses.

Nessus can scan many target types:

- servers;
- workstations;
- network devices;
- web servers;
- databases;
- cloud-facing assets;
- SSL/TLS services;
- operating systems;
- applications;
- local systems using credentials.

Nessus does not “hack” systems by default. It performs checks. Some checks are passive and banner-based. Others are active. Some may be more intrusive depending on policy settings.

---

## 6. Nessus Editions and Licensing

The main Nessus editions are:

### 6.1 Nessus Essentials

Nessus Essentials is the free edition commonly used by students and beginners. It is suitable for learning, home labs, and small authorized environments. Tenable states that Nessus Essentials is free and limited to a maximum number of IP addresses per scanner.

Important practical note: limits and licensing details can change. Always verify the current Tenable page before relying on a specific limit.

### 6.2 Nessus Professional

Nessus Professional is intended for consultants, security practitioners, and organizations that need broader vulnerability scanning, reporting, and professional use.

### 6.3 Nessus Expert

Nessus Expert extends Nessus capabilities toward external attack surface and web application security use cases.

### 6.4 Tenable Vulnerability Management / Tenable Security Center

These are enterprise platforms for managing scanners, assets, findings, dashboards, workflows, remediation, and reporting at scale.

---

## 7. Nessus Architecture and Core Concepts

### 7.1 Scanner

The scanner is the Nessus engine that performs checks against targets.

### 7.2 Web Interface

Nessus is managed through a browser-based interface, usually reachable locally at:

```text
https://localhost:8834/
```

or remotely if the service is exposed and allowed.

### 7.3 Plugins

Plugins are detection scripts used by Nessus. Each plugin checks for a specific vulnerability, configuration issue, service behavior, software version, or security condition.

A plugin usually includes:

- plugin ID;
- name;
- description;
- synopsis;
- solution;
- risk factor;
- CVSS score;
- affected port/service;
- output evidence;
- references such as CVE, CWE, vendor advisories, or exploit references.

### 7.4 Scan Template

A scan template is a predefined scan type designed for a purpose. Examples include host discovery, basic network scanning, web application tests, malware scanning, credentialed patch audit, and compliance checks.

### 7.5 Policy

A policy is a reusable scan configuration. You can create a policy once and reuse it across multiple scans.

### 7.6 Target

A target is the host, IP range, domain, URL, or asset to be scanned.

### 7.7 Credentials

Credentials allow Nessus to authenticate to a target and perform deeper checks. Credentialed scans are usually more accurate than unauthenticated scans.

### 7.8 Report

A report is the exported result of a scan. It may be used for remediation, audit evidence, management communication, or technical documentation.

---

## 8. Installing Nessus on Linux, Windows, and macOS

Tenable provides one Nessus package per operating system and processor architecture. The installed package is generally the same; the activation code determines which Nessus edition is enabled.

### 8.1 Installation Flow

The general installation flow is:

1. Create or obtain a Nessus activation code.
2. Download the correct package from Tenable.
3. Install the package.
4. Start the Nessus service.
5. Open the web interface.
6. Complete activation and account creation.
7. Wait for plugin download and compilation.
8. Create the first scan.

### 8.2 Installing on Debian / Ubuntu / Kali

Download the `.deb` package that matches your system architecture.

Example installation:

```bash
sudo dpkg -i Nessus-<version>-debian10_amd64.deb
```

If dependencies are missing:

```bash
sudo apt --fix-broken install
```

Start the service:

```bash
sudo systemctl start nessusd
```

Enable it at boot:

```bash
sudo systemctl enable nessusd
```

Check service status:

```bash
sudo systemctl status nessusd
```

Open:

```text
https://localhost:8834/
```

### 8.3 Installing on Red Hat / Fedora / Rocky / AlmaLinux

Install the `.rpm` package:

```bash
sudo rpm -ivh Nessus-<version>.x86_64.rpm
```

Start and enable the service:

```bash
sudo systemctl start nessusd
sudo systemctl enable nessusd
```

Open:

```text
https://localhost:8834/
```

### 8.4 Installing on Windows

1. Download the Windows installer from Tenable.
2. Run the installer as Administrator.
3. Follow the setup wizard.
4. Allow the service to start.
5. Open the Nessus web interface in the browser.

Typical local URL:

```text
https://localhost:8834/
```

### 8.5 Installing on macOS

1. Download the macOS package from Tenable.
2. Open the installer.
3. Follow the installation steps.
4. Start Nessus if it does not start automatically.
5. Open:

```text
https://localhost:8834/
```

### 8.6 Browser Certificate Warning

The Nessus local interface usually uses HTTPS with a self-signed certificate. Your browser may show a warning. This is expected for a local administrative interface, but you should still verify that you are connecting to the correct local service.

---

## 9. Initial Setup and Activation

During first setup, Nessus asks for:

- product type;
- activation code;
- user account creation;
- plugin download;
- plugin compilation.

Plugin initialization can take time. Do not interrupt it unless it is clearly stuck.

After setup, confirm:

- the service is running;
- plugins are up to date;
- the UI is accessible;
- your license is active;
- your user account works.

---

## 10. Configuring Nessus for Performance, Stability, and Accuracy

Nessus scan configuration must balance speed, accuracy, and safety.

A fast scan is not always a good scan. A very aggressive scan can overload fragile targets, generate false positives, or trigger defensive systems. A very conservative scan may miss findings.

### 10.1 Performance Settings

Important performance settings include:

- maximum simultaneous hosts per scan;
- maximum simultaneous checks per host;
- network timeout;
- scan delay;
- port scan range;
- service detection intensity;
- safe checks;
- plugin families enabled or disabled.

Tenable documentation describes advanced scan settings such as maximum simultaneous checks per host and maximum simultaneous hosts per scan. These settings influence scan speed and target load.

### 10.2 Recommended Beginner Settings

For a student lab or authorized website scan:

- scan one target at a time;
- avoid scanning large ranges without permission;
- keep safe checks enabled;
- avoid denial-of-service plugin families unless explicitly authorized;
- use reasonable timeouts;
- avoid excessive concurrency;
- schedule scans outside peak usage hours;
- document every configuration choice.

### 10.3 Network Considerations

Before scanning, consider:

- target bandwidth;
- your own bandwidth;
- VPN latency;
- firewall or WAF behavior;
- IDS/IPS alerts;
- rate limits;
- whether the target is production or lab.

### 10.4 Accuracy Settings

To improve accuracy:

- use credentialed scans when authorized;
- keep plugins updated;
- scan from an appropriate network location;
- use the right template;
- define the target precisely;
- validate important findings manually;
- compare findings with service banners, vendor advisories, and patch status.

---

## 11. Navigating the Nessus Interface

The Nessus interface is organized around scans, policies, settings, and results.

### 11.1 Main Dashboard

The dashboard usually shows scan status, recent activity, and vulnerability summaries.

You can use it to quickly answer:

- Which scans have completed?
- Which scans failed?
- Which findings are critical or high?
- Which targets are most exposed?
- Which scans need attention?

### 11.2 Scans Menu

The Scans section is where you:

- create scans;
- launch scans;
- pause or stop scans;
- view scan history;
- open results;
- export reports;
- schedule recurring scans.

### 11.3 Policies Menu

The Policies section is where you create reusable scan configurations.

A policy is useful when you want consistent settings across repeated scans, for example:

- weekly internal scan;
- monthly external perimeter scan;
- web application scan baseline;
- authenticated Linux patch audit;
- SSL/TLS configuration review.

### 11.4 Plugin Rules and Settings

Advanced users may adjust plugin behavior, disable noisy checks, or customize scanning behavior. For beginners, keep default settings unless you understand the impact.

---

## 12. Scan Templates and Policies

### 12.1 What Is a Scan Template?

A scan template is a predefined scanning configuration designed for a particular purpose.

Common templates include:

- **Host Discovery**: finds live hosts.
- **Basic Network Scan**: general vulnerability scan for networked systems.
- **Advanced Scan**: customizable scan with detailed settings.
- **Web Application Tests**: checks common web application vulnerabilities.
- **Credentialed Patch Audit**: checks missing patches using credentials.
- **Malware Scan**: checks for signs of malware or compromise.
- **Policy Compliance Auditing**: evaluates systems against configuration standards.

### 12.2 Choosing the Correct Template

Choose based on your objective:

| Objective | Recommended Template |
|---|---|
| Find live hosts | Host Discovery |
| General vulnerability scan | Basic Network Scan |
| Deep custom scan | Advanced Scan |
| Test a website | Web Application Tests |
| Check missing OS patches | Credentialed scan / patch audit |
| Review SSL/TLS | SSL/TLS-focused scan or relevant plugin families |
| Audit configuration | Compliance template |

### 12.3 What Is a Policy?

A policy is a saved scan configuration. Policies reduce mistakes and improve consistency.

For example, a company could create:

- `External_Web_Safe_Baseline`;
- `Internal_Linux_Credentialed_Patch_Audit`;
- `Windows_Server_Monthly_Scan`;
- `TLS_Configuration_Review`.

---

## 13. Creating, Scheduling, Launching, and Managing Scans

### 13.1 Creating a Scan

General process:

1. Go to **Scans**.
2. Click **New Scan**.
3. Select a template.
4. Enter scan name.
5. Enter target.
6. Configure discovery, assessment, credentials, plugins, and advanced settings.
7. Save the scan.
8. Launch immediately or schedule it.

### 13.2 Naming Convention

Use clear names:

```text
YYYY-MM-DD_Target_Template_Purpose
```

Example:

```text
2026-04-27_hacker-org_webapp-baseline_authorized-lab
```

### 13.3 Target Definition

Be precise:

Good:

```text
https://example.com
192.168.56.10
10.10.10.0/24
```

Risky or unclear:

```text
example.com/anything
all company network
random public website
```

### 13.4 Scheduling Scans

Scheduling is useful for recurring vulnerability management.

Examples:

- weekly scan of internal assets;
- monthly external perimeter scan;
- after patch Tuesday;
- after a major deployment;
- before a compliance review.

When scheduling scans, consider business impact. Avoid scanning production during peak traffic unless explicitly approved.

### 13.5 Monitoring Scan Progress

While a scan is running:

- watch scan status;
- check whether hosts are being discovered;
- monitor whether vulnerability counts increase;
- check for errors;
- watch system resource usage;
- ensure the target remains responsive;
- stop the scan if it causes instability.

### 13.6 Managing Scan History

Keep scan history organized. For coursework, export reports and keep screenshots. For professional work, track scan IDs, dates, scope, configuration, and remediation status.

---

## 14. Web Application Testing with Nessus

### 14.1 What the Web Application Tests Template Does

The Web Application Tests template is designed to detect common web-facing vulnerabilities and misconfigurations.

It may identify issues such as:

- outdated web server versions;
- insecure HTTP headers;
- weak SSL/TLS configuration;
- directory listing;
- exposed files;
- default pages;
- known vulnerable web technologies;
- some injection patterns;
- cross-site scripting indicators;
- authentication or session weaknesses detectable automatically.

### 14.2 Why Use It for This Exercise?

For the project task, the target is a website. The Web Application Tests template is appropriate because it focuses on HTTP/HTTPS services and web-specific checks rather than scanning an entire network like a general infrastructure scan.

It helps students learn how scanners detect web vulnerabilities, how findings are grouped by plugin, and how to interpret web-facing risk.

### 14.3 Limitations

Traditional Nessus web checks may not fully understand modern JavaScript-heavy applications, single-page applications, complex authentication flows, or business logic vulnerabilities. Tenable also has a dedicated Web App Scanning product for more advanced modern web application scanning.

For web security, combine Nessus with:

- manual testing;
- Burp Suite;
- OWASP Testing Guide methodology;
- source code review where available;
- authentication and authorization testing;
- business logic analysis.

---

## 15. Credentialed vs Non-Credentialed Scans

### 15.1 Non-Credentialed Scan

A non-credentialed scan sees the target like an external attacker or unauthenticated user.

It can detect:

- exposed services;
- banners;
- public vulnerabilities;
- SSL/TLS issues;
- some web vulnerabilities;
- default pages;
- network-level misconfigurations.

But it may miss:

- missing local patches;
- local configuration weaknesses;
- registry-level Windows issues;
- package-level Linux vulnerabilities;
- authenticated application vulnerabilities.

### 15.2 Credentialed Scan

A credentialed scan logs into the target using approved credentials.

It can detect:

- missing patches more accurately;
- vulnerable installed packages;
- insecure local configuration;
- weak permissions;
- local policy violations;
- compliance issues.

Credentialed scans are usually more accurate and less dependent on banners.

### 15.3 Credential Safety

Use dedicated low-privilege scanning accounts where possible. Protect credentials. Do not store shared administrator passwords casually. Rotate credentials after assessments when required.

---

## 16. Understanding Plugins and Vulnerability Detection

Nessus relies on plugins. A plugin is the detection logic for a specific issue.

A plugin result may contain:

- plugin ID;
- plugin name;
- risk factor;
- CVSS score;
- VPR score where available;
- exploit availability;
- affected port;
- evidence;
- description;
- solution;
- references;
- publication and modification dates.

### 16.1 Plugin Families

Plugin families group checks by topic, such as:

- web servers;
- databases;
- Windows;
- Linux;
- firewalls;
- SSL;
- CGI abuses;
- policy compliance;
- denial of service;
- default credentials.

### 16.2 Safe Checks

Safe checks try to detect vulnerabilities without performing dangerous actions. Keep safe checks enabled unless you have explicit authorization and understand the consequences.

### 16.3 Intrusive Checks

Some checks can be more aggressive. They may attempt behavior that could affect fragile services. Do not enable intrusive or denial-of-service checks against production systems unless explicitly permitted.

---

## 17. Reading and Interpreting Results

After a scan completes, analyze results carefully.

### 17.1 Main Views

Nessus results are commonly organized by:

- vulnerabilities;
- hosts/assets;
- severity;
- plugin family;
- port/protocol;
- scan history.

### 17.2 What to Look at First

Start with:

1. Critical findings.
2. High findings.
3. Internet-facing exposure.
4. Exploitability.
5. Authentication requirements.
6. Known exploited vulnerabilities.
7. Business-critical assets.
8. Repeated issues across many hosts.

### 17.3 How to Analyze a Finding

For each important vulnerability, answer:

- What is the vulnerability?
- Which asset is affected?
- Which port/service is affected?
- What evidence did Nessus provide?
- Is the finding likely true?
- Is the asset exposed publicly?
- Is exploitation easy or known?
- What is the potential impact?
- What remediation is recommended?
- Who owns the asset?
- What priority should it receive?

### 17.4 Example Finding Analysis Format

```text
Finding: TLS 1.0 Supported
Severity: Medium
Affected asset: https://example.com:443
Evidence: Nessus detected that the server accepts TLS 1.0 connections.
Impact: Attackers may exploit outdated protocol weaknesses or downgrade risks depending on client/server configuration.
Remediation: Disable TLS 1.0 and TLS 1.1. Enforce TLS 1.2 or TLS 1.3. Test compatibility before production rollout.
Priority: Medium, higher if the asset handles sensitive data or must comply with strict standards.
Validation: Confirm with openssl, testssl.sh, or browser/server configuration review.
```

---

## 18. Vulnerability Severity: CVSS, Risk, Context, and Prioritization

### 18.1 Severity Categories

Tenable commonly presents severity using categories:

| Severity | Typical CVSS v3 Range | Meaning |
|---|---:|---|
| Info | 0.0 | Informational finding |
| Low | 0.1–3.9 | Limited impact or difficult exploitation |
| Medium | 4.0–6.9 | Meaningful weakness but not immediately critical |
| High | 7.0–8.9 | Serious vulnerability likely requiring prompt remediation |
| Critical | 9.0–10.0 | Severe issue requiring urgent attention |

### 18.2 CVSS

CVSS stands for Common Vulnerability Scoring System. It provides a standardized way to score technical severity based on factors such as:

- attack vector;
- attack complexity;
- privileges required;
- user interaction;
- scope;
- confidentiality impact;
- integrity impact;
- availability impact.

CVSS is useful, but it is not the full risk picture.

### 18.3 Risk Context

Prioritization should also consider:

- asset criticality;
- internet exposure;
- exploit availability;
- active exploitation in the wild;
- sensitive data exposure;
- compensating controls;
- business function;
- patch complexity;
- regulatory requirements.

### 18.4 Prioritization Example

A medium vulnerability on a public login portal may be more urgent than a high vulnerability on an isolated lab system.

A critical vulnerability with public exploit code on an internet-facing server should usually be treated as urgent.

---

## 19. Reporting in Nessus

### 19.1 Why Reports Matter

A scan is only useful if the results are communicated clearly and lead to action.

A good report should:

- define scope;
- explain methodology;
- summarize risk;
- show evidence;
- prioritize remediation;
- provide clear recommendations;
- be understandable by both technical and non-technical readers.

### 19.2 Common Export Formats

Depending on edition and configuration, Nessus may support exports such as:

- PDF;
- HTML;
- CSV;
- Nessus XML;
- Nessus DB.

PDF or HTML is useful for human review. CSV is useful for tracking, filtering, and importing into spreadsheets or ticketing systems. Nessus XML is useful for tool integrations.

### 19.3 Report Structure

A professional vulnerability report usually contains:

1. Title page
2. Executive summary
3. Scope
4. Rules of engagement
5. Methodology
6. Scan configuration
7. Summary of findings
8. Severity distribution
9. Detailed findings
10. Remediation plan
11. Evidence and screenshots
12. Limitations
13. Appendix

---

## 20. Remediation Workflow

### 20.1 Remediation Actions

Common remediation actions include:

- patching software;
- upgrading unsupported versions;
- disabling vulnerable protocols;
- changing default credentials;
- removing exposed files;
- fixing insecure headers;
- restricting access with firewall rules;
- correcting permissions;
- improving TLS configuration;
- disabling unnecessary services;
- applying vendor hardening guides.

### 20.2 Assigning Ownership

Every finding should have an owner.

Examples:

- system administrator;
- web developer;
- DevOps engineer;
- network administrator;
- database administrator;
- product owner;
- third-party vendor.

### 20.3 Remediation Timeline Example

| Severity | Suggested Timeline |
|---|---|
| Critical | 24–72 hours depending on exposure |
| High | 7–14 days |
| Medium | 30–60 days |
| Low | 60–90 days or next maintenance cycle |
| Info | Best effort / documentation |

These are examples. Real deadlines depend on business context, exposure, and policy.

---

## 21. Verification and Rescanning

After remediation, verify the fix.

Verification methods:

- rerun the same Nessus scan;
- run a targeted remediation scan;
- manually test the affected service;
- confirm patch version;
- review configuration;
- confirm exposure is removed;
- check logs for errors.

A vulnerability is not truly closed until the fix has been verified.

---

## 22. Common Mistakes and False Positives

### 22.1 Common Beginner Mistakes

- scanning targets without authorization;
- scanning too aggressively;
- ignoring scope;
- relying only on scanner severity;
- not validating critical findings;
- exporting reports without analysis;
- forgetting screenshots for coursework;
- scanning production during peak hours;
- ignoring credentials when authorized;
- treating informational findings as urgent vulnerabilities.

### 22.2 False Positives

A false positive is a reported issue that is not actually exploitable or applicable.

Reasons include:

- inaccurate banner grabbing;
- patched backported packages;
- proxy/WAF behavior;
- plugin limitations;
- incomplete scan evidence;
- unusual service behavior.

### 22.3 False Negatives

A false negative is a real issue missed by the scanner.

Reasons include:

- missing authentication;
- blocked scanner traffic;
- modern JavaScript-heavy app;
- business logic flaw;
- custom vulnerability;
- plugin not available yet;
- scan template not adapted.

---

## 23. Practical Exercise: Scan a Website with Nessus Essentials

### 23.1 Objective

The objective is to use Nessus Essentials to configure and run a vulnerability scan against an authorized website, then analyze the results and produce a professional report.

### 23.2 Target Selection

The project suggests examples such as:

- hellboundhackers
- hackaday
- catb.org
- hacklang.org
- hacker.org

Important: before scanning a public website, verify that scanning is authorized by your school, instructor, the site owner, or the site policy. If you cannot confirm authorization, use a local vulnerable lab instead, such as OWASP Juice Shop, DVWA, Metasploitable, or a school-provided environment.

### 23.3 Recommended Safe Lab Alternative

For a controlled local web scan, you can use OWASP Juice Shop in Docker:

```bash
docker run --rm -p 3000:3000 bkimminich/juice-shop
```

Then scan:

```text
http://localhost:3000
```

This is safer than scanning a third-party public website without explicit authorization.

### 23.4 Scan Configuration

Use the **Web Application Tests** template.

#### Why choose Web Application Tests?

Because the target is a website and the objective is to identify web-facing vulnerabilities. This template focuses on HTTP/HTTPS checks and web-specific issues rather than broad infrastructure scanning.

#### Configuration Considerations

When configuring the scan, consider:

- authorization and scope;
- exact target URL;
- HTTP vs HTTPS;
- scan intensity;
- rate limits;
- production safety;
- time window;
- whether authentication is needed;
- whether intrusive checks are allowed;
- plugin families enabled;
- report requirements;
- screenshots required for the submission.

Recommended beginner settings:

- one target only;
- safe checks enabled;
- conservative performance;
- no denial-of-service checks;
- no brute-force checks unless explicitly authorized;
- clear scan name;
- detailed notes in the report.

### 23.5 Scan Setup Steps

1. Log in to Nessus.
2. Go to **Scans**.
3. Click **New Scan**.
4. Choose **Web Application Tests**.
5. Name the scan clearly.
6. Enter the target website URL.
7. Review discovery settings.
8. Review assessment settings.
9. Disable dangerous or intrusive checks if needed.
10. Save the scan.
11. Take a screenshot of the scan configuration.
12. Launch the scan.

### 23.6 Scan Execution

To launch:

1. Open the saved scan.
2. Click **Launch**.
3. Monitor progress from the scan list or scan details page.
4. Confirm the scan is running and not failing immediately.
5. Watch for errors, timeouts, or target instability.
6. Take screenshots showing the scan in progress.

### 23.7 Monitoring the Scan

You should monitor:

- scan status;
- elapsed time;
- hosts tested;
- vulnerabilities appearing;
- errors;
- target availability;
- local CPU/RAM usage;
- Nessus logs if needed.

If the scan creates instability, stop it and document what happened.

### 23.8 Results Analysis

After completion:

1. Open the scan results.
2. Check total vulnerabilities.
3. Capture a screenshot of the severity summary.
4. List critical and high findings.
5. Open each critical/high finding.
6. Review description, evidence, affected URL/port, impact, and remediation.
7. Choose one vulnerability for detailed analysis.
8. Export the Nessus report.

### 23.9 Example Answer: Number of Vulnerabilities

Use your actual result.

Example format:

```text
The scan identified 18 findings in total:
- Critical: 0
- High: 1
- Medium: 5
- Low: 7
- Informational: 5
```

Add screenshot evidence below this section in your final report.

### 23.10 Example Detailed Vulnerability Write-Up

```text
Vulnerability Name: SSL Certificate Cannot Be Trusted
Severity: Medium
Affected Asset: https://example.com:443
Plugin ID: [Insert Nessus Plugin ID]
Evidence: Nessus reported that the certificate chain could not be validated.
Potential Impact: Users may be unable to verify the identity of the server. In some scenarios, this may increase the risk of man-in-the-middle attacks or user trust warnings.
Remediation: Install a valid certificate issued by a trusted CA, ensure the full certificate chain is configured, and verify renewal automation.
Validation: Re-run the scan and confirm the finding is no longer present. Also validate manually with browser certificate details or openssl.
```

---

## 24. Report Template for the Exercise

Copy this section into your final submission and replace placeholders with your real results.

# Nessus Web Application Vulnerability Scan Report

## 1. Executive Summary

This report presents the results of a Nessus Essentials vulnerability scan performed against:

```text
Target: [Insert target]
Date: [Insert date]
Scanner: Nessus Essentials
Template: Web Application Tests
Authorization: [Explain why the scan was authorized]
```

The goal was to identify web-facing vulnerabilities and understand how Nessus can support vulnerability management.

The scan identified:

```text
Critical: [number]
High: [number]
Medium: [number]
Low: [number]
Informational: [number]
Total: [number]
```

Overall risk assessment:

```text
[Write 5–8 lines summarizing the most important risks.]
```

## 2. Scope

| Item | Description |
|---|---|
| Target | [URL / IP] |
| Scan type | Web Application Tests |
| Authentication | None / authenticated / not applicable |
| Time window | [Date and time] |
| Exclusions | [If any] |
| Limitations | [If any] |

## 3. Methodology

The assessment followed these steps:

1. Installed and configured Nessus Essentials.
2. Created a new scan using the Web Application Tests template.
3. Configured target, safe scan settings, and relevant options.
4. Launched the scan.
5. Monitored progress and confirmed completion.
6. Reviewed findings by severity.
7. Selected one vulnerability for detailed analysis.
8. Exported the Nessus report.
9. Prepared remediation recommendations.

## 4. Scan Configuration

### 4.1 Template Choice

The Web Application Tests template was chosen because the target was a website. This template is designed to focus on web-specific checks such as HTTP/HTTPS service issues, web server weaknesses, SSL/TLS problems, exposed files, and common web application vulnerabilities.

### 4.2 Configuration Considerations

The following considerations were taken into account:

- scope and authorization;
- target URL accuracy;
- scan safety;
- avoiding denial-of-service checks;
- scan timing;
- target availability;
- result quality;
- screenshot evidence for documentation.

### 4.3 Screenshots

Insert screenshots here:

- Nessus dashboard
- New scan template selection
- Target configuration
- Advanced settings
- Saved scan page

## 5. Scan Execution

The scan was launched from the Nessus Scans page by clicking **Launch** on the saved scan.

During execution, progress was monitored through the scan status view. The scan was checked for errors, timeouts, and unexpected target instability.

### Screenshots

Insert screenshots here:

- scan running
- scan progress
- scan completed

## 6. Results Summary

| Severity | Count |
|---|---:|
| Critical | [number] |
| High | [number] |
| Medium | [number] |
| Low | [number] |
| Informational | [number] |
| Total | [number] |

### Screenshot

Insert screenshot of the Nessus severity summary.

## 7. Critical and High Findings Summary

| Severity | Vulnerability | Affected Asset | Potential Impact | Recommended Action |
|---|---|---|---|---|
| Critical/High | [Name] | [Asset/port] | [Impact] | [Fix] |
| Critical/High | [Name] | [Asset/port] | [Impact] | [Fix] |

If no critical or high vulnerabilities were found, write:

```text
No critical or high-severity vulnerabilities were identified during this scan. The most important findings were medium-severity issues related to [insert topic].
```

## 8. Detailed Vulnerability Analysis

### 8.1 Vulnerability Name

```text
[Insert vulnerability name]
```

### 8.2 Severity

```text
[Critical / High / Medium / Low / Info]
```

### 8.3 Affected Asset

```text
[URL, host, port, protocol]
```

### 8.4 Nessus Evidence

```text
[Paste relevant Nessus plugin output or summarize the evidence]
```

### 8.5 Technical Explanation

Explain what the vulnerability means in your own words.

### 8.6 Potential Impact

Explain what an attacker could do if the vulnerability is exploitable.

### 8.7 Remediation Steps

Provide clear, actionable steps.

Example:

1. Update the affected software.
2. Disable vulnerable protocol or feature.
3. Apply vendor-recommended configuration.
4. Restart the service if required.
5. Re-run the scan to verify remediation.

### 8.8 Verification

Explain how you would confirm the vulnerability is fixed.

## 9. Prioritization and Remediation Plan

| Priority | Finding | Reason | Owner | Deadline |
|---|---|---|---|---|
| P1 | [Critical finding] | Public exposure / exploitability | [Owner] | [Date] |
| P2 | [High finding] | Significant impact | [Owner] | [Date] |
| P3 | [Medium finding] | Lower risk but should be fixed | [Owner] | [Date] |

## 10. Limitations

Examples:

- The scan was unauthenticated.
- The scan was limited to one target.
- Business logic vulnerabilities were not manually tested.
- Modern JavaScript flows may not be fully covered.
- Results depend on Nessus plugins available at scan time.

## 11. Conclusion

The scan demonstrated how Nessus can identify and classify vulnerabilities in a web application context. The results should be used as part of a broader vulnerability management process, including manual validation, remediation tracking, and rescanning.

## 12. Appendix

Attach:

- Nessus exported report;
- screenshots;
- scan configuration evidence;
- remediation notes;
- validation screenshots after rescanning.

---

## 25. Screenshots Checklist

For your submission, capture screenshots of:

1. Nessus login/dashboard.
2. New scan creation page.
3. Web Application Tests template selection.
4. Target configuration.
5. Scan settings.
6. Saved scan page.
7. Scan running.
8. Scan completed.
9. Vulnerability summary by severity.
10. Critical/high finding list.
11. Detailed view of one vulnerability.
12. Export/report generation.

Name screenshots clearly:

```text
01-dashboard.png
02-template-selection.png
03-target-configuration.png
04-scan-running.png
05-scan-completed.png
06-severity-summary.png
07-vulnerability-detail.png
08-report-export.png
```

---

## 26. Cheat Sheet

### Start Nessus on Linux

```bash
sudo systemctl start nessusd
```

### Stop Nessus on Linux

```bash
sudo systemctl stop nessusd
```

### Restart Nessus on Linux

```bash
sudo systemctl restart nessusd
```

### Check Nessus Status

```bash
sudo systemctl status nessusd
```

### Open Nessus

```text
https://localhost:8834/
```

### Beginner Scan Flow

```text
Scans → New Scan → Web Application Tests → Configure target → Save → Launch → Monitor → Analyze → Export report
```

### Good Scan Naming

```text
YYYY-MM-DD_target_template_purpose
```

### Severity Priority

```text
Critical > High > Medium > Low > Info
```

### Analysis Questions

```text
What is affected?
What evidence exists?
Is it exploitable?
Is it exposed?
What is the impact?
How do we fix it?
How do we verify the fix?
```

---

## 27. Glossary

**Asset**  
A system, website, application, server, or device that may contain vulnerabilities.

**CVSS**  
Common Vulnerability Scoring System, a standardized technical severity scoring system.

**False positive**  
A reported vulnerability that is not actually present or exploitable.

**False negative**  
A real vulnerability that the scanner failed to detect.

**Plugin**  
A Nessus detection script that checks for a specific vulnerability or condition.

**Policy**  
A reusable Nessus scan configuration.

**Remediation**  
The process of fixing or mitigating a vulnerability.

**Scan template**  
A predefined scan configuration for a specific purpose.

**Vulnerability**  
A weakness that could be exploited to harm confidentiality, integrity, or availability.

**Vulnerability management**  
The continuous process of finding, prioritizing, fixing, and verifying vulnerabilities.

---

## 28. References

- Tenable Nessus Documentation: https://docs.tenable.com/nessus/
- Tenable Nessus Installation Guide: https://docs.tenable.com/nessus/Content/InstallNessus.htm
- Tenable Nessus Scan Templates: https://docs.tenable.com/nessus/Content/ScanAndPolicyTemplates.htm
- Tenable Nessus Get Started Guide: https://docs.tenable.com/nessus/Content/GetStarted.htm
- Tenable Nessus Advanced Scan Settings: https://docs.tenable.com/nessus/Content/AdvancedScanSettings.htm
- Tenable Nessus Risk Metrics: https://docs.tenable.com/nessus/Content/RiskMetrics.htm
- Tenable Vulnerability Severity Indicators: https://docs.tenable.com/vulnerability-management/Content/Explore/Findings/VulnerabilitySeverityIndicators.htm
- Tenable Web App Scanning Documentation: https://docs.tenable.com/web-app-scanning/
- Tenable Nessus Essentials: https://www.tenable.com/products/nessus/nessus-essentials
- Tenable Nessus FAQ: https://www.tenable.com/products/nessus/nessus-faq
- FIRST CVSS Specification: https://www.first.org/cvss/
- OWASP Web Security Testing Guide: https://owasp.org/www-project-web-security-testing-guide/
- OWASP Juice Shop: https://owasp.org/www-project-juice-shop/

---

## Final Reminder

Nessus is powerful because it automates vulnerability discovery, but professional security work requires more than clicking “Scan.” You must define scope, choose safe settings, validate results, understand impact, prioritize intelligently, and communicate remediation clearly.

The goal is not to produce the biggest vulnerability list. The goal is to reduce real risk.
