# 📌 Penetration Testing Frameworks

## Introduction

A **penetration testing framework** is a structured methodology that guides security professionals through every stage of an engagement, from initial planning and scoping to exploitation, reporting, and remediation validation. Consider the analogy of a building inspector following a code-compliance checklist: the inspector does not wander through the building, hoping to notice problems. Instead, they follow a systematic process that ensures every structural element, electrical system, and fire safety measure is evaluated against a known standard. Penetration testing frameworks serve the same purpose for security assessments.

There are many penetration testing frameworks in active use today, and each has its own philosophy, strengths, and ideal use cases. In this room, we will explore the following in depth:

1. [Open Source Security Testing Methodology Manual (OSSTMM)](https://www.isecom.org/OSSTMM.3.pdf), a scientific, metrics-driven approach to security testing
2. [OWASP Web Security Testing Guide (WSTG)](https://owasp.org/www-project-web-security-testing-guide/), the go-to framework for web application assessments
3. [NIST Special Publication 800-115](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-115.pdf), the U.S. government's technical guide to security testing and assessment
4. [Penetration Testing Execution Standard (PTES)](http://www.pentest-standard.org/index.php/Main_Page), a practical, phase-driven standard that mirrors how real engagements are conducted
5. [Information Systems Security Assessment Framework (ISSAF)](https://untrustednetwork.net/files/issaf0.2.1.pdf), a historically influential methodology with a detailed nine-step assessment model

## OSSTMM

OSSTMM organizes testing around five security channels, reflecting its philosophy that security is not just a network problem:

- Human Security (HUMSEC): Social engineering and human-factor vulnerabilities.
- Physical Security (PHYSSEC): Physical access controls, from badge readers to tailgating.
- Wireless Communications (SPECSEC): Wi-Fi, Bluetooth, RFID, and other electromagnetic signals.
- Telecommunications (COMSEC): Phone systems, VoIP, fax, and modem infrastructure.
- Data Networks (DATASEC): Network services, firewalls, and application-layer protocols.

## OWASP WSTG

### Phases: Security Across the SDLC
What sets the WSTG apart from many penetration testing frameworks is that it does not treat security as a single event. Instead, it aligns testing across five phases of the Software Development Life Cycle (SDLC), embedding security from initial planning through post-launch maintenance.

Let's see how this works for our online retailer, ShopSecure Inc., which is building a new customer portal.

Phase 1: Before development begins. Security requirements and regulatory obligations are established upfront. For ShopSecure, this means defining that the portal must comply with PCI DSS (since it processes payments) and establishing measurable criteria, such as the maximum acceptable time for patching vulnerabilities.

Phase 2: During definition and design. The application architecture is reviewed for security flaws before any code is written. ShopSecure's team creates threat models for the payment flow, identifying that the checkout API will be a high-value target and designing rate-limiting and input validation controls from the start.

Phase 3: During development. Code is vetted through walkthroughs and reviews. ShopSecure's developers review the authentication module against WSTG test cases for credential handling (WSTG-ATHN), identifying a flaw in which password reset tokens do not expire.

Phase 4: During deployment. Security controls are verified in the production environment. ShopSecure's team runs a penetration test against the staged application, verifying that default credentials have been changed, that TLS is properly configured, and that no debug endpoints are exposed.

Phase 5: During maintenance and operations. Security is maintained post-launch through periodic health checks, especially after updates. When ShopSecure pushes a new product recommendation feature three months later, the relevant WSTG test cases are re-executed to ensure the update has not introduced new vulnerabilities.

## NIST SP 800-115

- usually for governments

## PTES

You have now seen frameworks that emphasize scientific metrics (OSSTMM), web application coverage (OWASP WSTG), and government-aligned assessment techniques (NIST SP 800-115). But here is a question worth considering: if you were hired tomorrow for a standard penetration testing engagement against a corporate network, which framework would most closely mirror the actual workflow you would follow from the first client call to the final report delivery?

For many working pentesters, the answer is the [Penetration Testing Execution Standard (PTES)](http://www.pentest-standard.org/index.php/Main_Page). Available at pentest-standard.org(opens in new tab), PTES was developed by a group of experienced security practitioners with a specific goal: to define what a real penetration test looks like, end-to-end. Where other frameworks focus on what to test or how to measure, PTES focuses on how the engagement flows from start to finish.

PTES is organized into seven phases that map directly to the lifecycle of a penetration testing engagement. This approach makes it exceptionally practical for junior testers because it answers the question that many frameworks leave implicit: "I have a signed contract; now what do I do on day one, day two, and every day after?"


Phase 1: Pre-Engagement Interactions. This is everything that happens before testing begins. You define the scope with MedGuard's IT director: the corporate LAN (10.10.0.0/16), the patient portal at records.medguard-health.thm, and wireless networks at the headquarters building. You document the rules of engagement, including testing windows (weeknights only to avoid disrupting clinical operations), emergency contacts, and a "get out of jail free" letter authorizing the test. PTES is notably detailed here because unclear scoping is the number one source of legal and professional disputes in penetration testing.

Phase 2: Intelligence Gathering. You collect information about MedGuard using both passive and active techniques. Passive reconnaissance includes harvesting employee email addresses from LinkedIn, discovering subdomains through certificate transparency logs, and reviewing job postings that reveal technology stacks ("seeking a DBA with Oracle 19c experience"). Active reconnaissance involves DNS enumeration and network scanning within the agreed scope. PTES distinguishes between these levels because the depth of intelligence gathering directly shapes the quality of the subsequent phases.

Phase 3: Threat Modeling. Using the intelligence gathered, you identify the most valuable targets and the most likely attack paths. At MedGuard, the patient records database is the highest-value asset. Your threat model identifies two primary attack paths: compromising the patient portal directly through a web vulnerability, or pivoting through the corporate LAN after compromising an employee workstation. This phase ensures your testing effort is directed by adversarial logic rather than random scanning.

Phase 4: Vulnerability Analysis. You systematically identify weaknesses that could enable the attack paths from your threat model. At MedGuard, vulnerability scanning reveals that the patient portal is running an outdated version of Apache Tomcat, which contains a known deserialization vulnerability. On the internal network, several workstations are missing critical patches. PTES emphasizes that vulnerability analysis includes both automated scanning and manual verification to eliminate false positives.

Phase 5: Exploitation. You attempt to exploit the confirmed vulnerabilities. At MedGuard, you exploit the Tomcat deserialization flaw to gain a shell on the portal server. On the internal side, you use a phishing pretext (authorized in the scope) to deliver a payload to an employee workstation. PTES stresses that exploitation should be purposeful: the goal is to demonstrate business impact, not to "pop boxes" for the sake of it.

Phase 6: Post-Exploitation. After gaining access, you determine the real-world impact. From the compromised portal server, you pivot into the backend database and confirm read access to patient records. From the employee workstation, you extract cached domain credentials and demonstrate lateral movement to a file server containing financial data. PTES treats post-exploitation as the phase where technical findings are translated into business risk: "we accessed 50,000 patient records" carries far more weight than "we got a shell."

Phase 7: Reporting. You deliver the findings in a structured report with two audiences in mind. The executive summary communicates business risk in plain language for MedGuard's leadership: patient data was accessible, regulatory exposure under HIPAA is significant, and remediation is urgent. The technical report provides the details that MedGuard's IT team needs to reproduce and fix each finding: exact exploitation steps, affected hosts, evidence screenshots, and prioritized remediation guidance.


## ISSAF

 Information Systems Security Assessment Framework (ISSAF)

Phases: A Walkthrough
ISSAF divides an assessment into three phases. Let's walk through them with a scenario: your team is assessing the security of TechBridge Solutions, a software development company with 200 employees, an internal Git server, and a client-facing project management portal.

Phase 1: Planning and Preparation

This phase sets the engagement boundaries. You meet with TechBridge's CTO to define the scope (corporate network, Git server, and the project management portal), establish escalation protocols and emergency contacts, identify constraints (the production Git server must not be disrupted during business hours), and agree on the toolset appropriate for the assessment.

Phase 2: Assessment

This is the core of ISSAF and where its nine-step model lives. Each step builds on the previous one, simulating how a real adversary would progress through the environment.

- Information gathering: Collect publicly available data about TechBridge. DNS records, WHOIS data, employee profiles on LinkedIn, and technology references in job postings ("experience with Jenkins and GitLab CI required") all feed your understanding of the target.
- Network mapping: Map the live network topology. You discover TechBridge's external IP range hosts the project portal, a VPN gateway, and a mail server. Internal scanning (once in scope) reveals the Git server, a Jenkins build server, and several developer workstations.
- Vulnerability identification: Scan the mapped assets for weaknesses. The project portal runs an outdated CMS with a known authentication bypass. The Jenkins server has its administrative console exposed without authentication.
- Penetration: Attempt initial exploitation. You exploit the unauthenticated Jenkins console to execute system commands on the build server.
- Gaining access and privilege escalation: Escalate from initial access to higher privileges. From the Jenkins server, you recover stored credentials for the service account that deploys code to production, which has administrative rights on the Git server.
- Enumerating further: With elevated access, enumerate what is now reachable. From the Git server, you discover repositories containing API keys, database connection strings, and client project source code.
- Compromise remote users/sites (lateral movement): Move laterally to other systems. Using the harvested credentials, you access several developer workstations and the internal mail server.
- Maintaining access: Establish persistent access to demonstrate that a real attacker could retain their foothold. You document (without actually deploying) how a backdoor could be planted in the CI/CD pipeline, persisting across system reboots and deployments.
- Covering tracks: Demonstrate how an attacker would erase evidence. You document which logs captured your activity and identify gaps in TechBridge's logging that would allow a real adversary to operate undetected.
Notice the progression: each step deepens the attacker's position in the environment. Steps 1 through 3 are reconnaissance and analysis, steps 4 through 7 are active compromise, and steps 8 through 9 address persistence and stealth.

Phase 3: Reporting and Cleanup

You compile findings into a structured report, prioritized by business impact. The unauthenticated Jenkins console is flagged as critical because it provided the initial foothold that led to source code access. Cleanup involves removing any test artifacts, revoking any temporary accounts created during testing, and confirming with TechBridge's team that no testing residue remains in their environment.

## MITRE ATT&CK


Here are the clean, simple documentation notes dedicated exclusively to the MITRE ATT&CK Matrix:

🛡️ MITRE ATT&CK: Quick Reference Notes
What is it?
Not a Framework: It is not a penetration testing framework. It won't tell you how to run a test, manage clients, or write reports.

An Encyclopedia/Dictionary: It is a massive, real-world knowledge base tracking observed adversary behaviors in the wild.

The Analogy: If a pentest framework (like PTES) is the diagnostic procedure a doctor follows, MITRE ATT&CK is the medical dictionary used to give standardized names to the symptoms found.

The Matrix Structure: Tactics vs. Techniques
The knowledge base is organized like a massive table or matrix. It breaks down attacker actions into three levels:

1. Tactics (The Columns) ➡️ The "Why"
These represent the adversary's high-level operational objectives.

The Enterprise matrix currently contains 14 tactics tracking an attack from start to finish.

Examples: Initial Access, Credential Access, Lateral Movement, Exfiltration.

2. Techniques (The Rows) ➡️ The "How"
The specific technical methods an adversary uses to achieve a tactic.

Each technique has a unique ID number.

Examples under the "Initial Access" Tactic:

T1190 - Exploit Public-Facing Application

T1566 - Phishing

3. Sub-Techniques ➡️ The Specific Variation
Granular breakdowns of a technique for even more specific actions.

Examples under T1566 (Phishing):

T1566.001 - Spearphishing Attachment

T1566.002 - Spearphishing Link

Inside a Technique Page (e.g., T1003)
When you look up an ID on their website, every entry acts as a living reference that includes:

Description: What the technique is and how it works.

Procedure Examples: Real-world examples of malware (like Mimikatz) or threat groups (like APT29) that have used it.

Mitigations: Structural defense concepts to completely block or secure systems against it.

Detection: Specific logging, telemetry, and data sources the Blue Team should monitor to catch the behavior.

Why Use It in Security Reports?
Universal Language: It allows penetration testers (Red Team), defenders (Blue Team), and threat hunters to speak the exact same language.

Upgrading the Conversation: Instead of telling a client "Fix this single software bug," mapping findings to ATT&CK IDs allows the client to ask, "Can our system detect this entire class of adversary behavior?"

Official Website: attack.mitre.org

Direct URL Shortcut: attack.mitre.org/techniques/[ID]


## Other Notable Frameworks

### Specialized & Domain-Specific Cyber Security Frameworks: Quick Reference

1. WASC Threat Classification
- Domain: Web Applications.
- Type: Threat Taxonomy (A classification system for vulnerabilities).
- Active Status: No (Largely obsolete and superseded by OWASP Top Ten and WSTG).
- When to use it: You will generally only see this in legacy documentation, historical context, or older compliance frameworks.
2. CSA Cloud Controls Matrix (CCM)
- Domain: Cloud Environments (AWS, Azure, GCP, etc.).
- Type: Governance and Compliance Controls Framework.
- Active Status: Yes.
- Key Concept: It is not a hacking or penetration testing methodology. It is a checklist of security controls across 17 domains to evaluate cloud providers and configurations.
- When to use it: Use this when a client needs a Cloud Security Posture Assessment or wants to evaluate if their cloud architecture aligns with major standards like ISO 27001 or NIST.
3. OWASP MASTG (Mobile Application Security Testing Guide)
- Domain: Mobile Applications (Android and iOS).
- Type: Technical Technical Testing Guide.
- Active Status: Yes.
- Key Concept: It is the mobile equivalent of the WSTG. It provides highly detailed, hands-on test cases covering mobile storage, cryptography, network communication, and reverse engineering. It works alongside the MASVS (Mobile Application Security Verification Standard).
- When to use it: This is your mandatory go-to reference whenever you are tasked with a mobile app penetration test (e.g., mobile banking or mobile healthcare apps).
4. PCI DSS Penetration Testing Guidelines
- Domain: Payment Card Environments.
- Type: Regulatory Mandate (Enforced via Requirement 11.4 of PCI DSS v4.0).
- Active Status: Yes.
- Key Concept: This is not optional—it is a legal/contractual requirement. It mandates that any organization handling credit card data must conduct internal and external penetration tests at least annually and after any major infrastructure changes. It also requires validation of network segmentation.
- When to use it: Use this during any engagement where the client is a retailer, bank, payment gateway, or any business operating within the payment card ecosystem.
5. CBEST Framework
- Domain: Financial Sector (United Kingdom).
- Type: Threat-Intelligence-Led Penetration Testing Framework.
- Active Status: Yes (Maintained by the Bank of England).
- Key Concept: This framework strictly bridges threat intelligence with hands-on testing. Before hacking begins, a dedicated intelligence phase discovers the exact threat actors currently targeting that specific institution. The penetration testers then mimic those exact real-world adversaries.
- When to use it: Use this when dealing with financial institutions based in the UK (banks, core insurers, and financial market infrastructure providers).

📊 Cheat Sheet Matrix
| Framework | Target Domain | Primary Purpose | Real-World Use Case |
|--|--|--|--|
| WASC | Web Apps | Threat terminology | Looking at older legacy compliance reports. |
| CSA CCM | Cloud Settings | Governance & Auditing | Auditing AWS/Azure security configurations. |
| OWASP MASTG | Mobile (iOS/Android) | Technical Hacking Guide |Pentesting an Android/iOS app. |
|PCI DSS Guidelines | Credit Card Networks | Mandatory Testing Rules | Scoping an annual test for an e-commerce platform.CBESTUK Financial SectorAdversary EmulationSimulating real threats against a UK bank. |