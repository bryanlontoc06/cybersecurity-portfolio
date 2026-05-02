# 📌 Incident Response Fundamentals


## What are Incidents?
Summary of the Incident Response Pipeline

- Event Generation: Computing devices run both interactive processes (user-driven) and non-interactive processes (background) that generate logs for every action they perform.

- Log Ingestion: Due to the massive volume of data, these events are ingested into security solutions which scan them for patterns of harmful activity.

- Alert Triggering: When the security solution identifies a potential threat, it triggers an alert for the security team to investigate.

- Alert Classification:

    -  False Positive: An alert that appears dangerous but is actually harmless, such as a large data transfer that is actually a scheduled backup.

    - True Positive: An alert that points to a legitimate threat, such as a confirmed phishing attempt.

- Incident Categorization: True positive alerts are referred to as incidents.

- Prioritization via Severity: Incidents are assigned a severity level—Low, Medium, High, or Critical—based on their impact. Critical Severity incidents receive the highest priority for immediate response.

## Types of Incidents

Summary of Cyber Security Incident Types

While "hacking" is a generic term, security incidents are categorized based on their specific nature and behavior.

- Malware Infections: These involve malicious programs like text files, documents, or executables designed to damage systems, networks, or applications. This is the most common type of incident.

- Security Breaches: These occur when an unauthorized individual successfully gains access to confidential data that they are not permitted to see.

- Data Leaks: This is the exposure of confidential information to unauthorized entities. Unlike breaches, these can be caused by intentional attacks, human error, or system misconfigurations.

- Insider Attacks: These are intentional attacks initiated by someone within the organization, such as a disgruntled employee. These are particularly hazardous because the attacker already has high-level access to internal resources.

- Denial of Service (DoS) Attacks: These incidents target the "Availability" of a system by flooding it with false requests. This exhausts the system's resources, making it unavailable to legitimate users.

The Impact of Severity
The severity of these incidents is subjective and depends on the specific organization. For example, a company that relies heavily on its website would view a DoS attack as disastrous, whereas a company with non-critical public data might view a data leak as a minor issue.

## Incident Response Process

Phase	Explanation	Example

Preparation
This is the first phase. The preparation phase includes building the necessary resources to handle an incident. These resources include developing incident response teams, having a proper incident response plan in place, and deploying necessary security solutions to combat the incidents.	Conducting awareness training for employees on phishing emails. Phishing emails are fraudulent emails sent by malicious attackers that can trick you into performing actions that can lead you to an incident.

Identification
The identification phase refers to looking for any abnormal behavior that may indicate an incident. This involves using various security solutions and techniques to monitor abnormal events.	The security team notices a huge amount of data being sent out from one of the hosts. Upon analysis, it was found to be compromised after a malicious file was downloaded from a phishing email attachment.

Containment
Once an incident has been identified, the next step should be to contain it. This means minimizing the impact of the attack. This is usually done by isolating the victim machine, disabling the compromised user accounts, etc.	The Security team isolates the host from the network to minimize the impact and not allow the attacker to jump to other systems, leveraging the compromised host.

Eradication
This phase, as its name suggests, involves removing the threat from the attacked environment. The threat may be of any kind. The eradication phase will ensure the subject environment is clean, and now we can move to the recovery phase.	A deep malware scan was executed on the system to remove the malicious software from the host.

Recovery
The recovery phase is very important in this chain. It involves recovering the affected systems from backup or rebuilding them. The recovered systems are then tested and are ready to use.	The compromised host was re-configured, and the exfiltrated data was restored from the backup.

Lessons
Learned	This is also an important part of the incident response lifecycle. Gaps in the detection and analysis of the incident are identified and documented, helping to improve the overall process in future incidents. Conducting a post-incident review meeting to analyze the incident's root cause and improve the security to prevent future attacks.

## Incident Response Technique

Security Solutions for Identification
Since identifying threats manually is extremely difficult, security teams rely on various automated solutions to detect abnormal behavior:

- SIEM (Security Information and Event Management): This tool collects important logs into one centralized location and correlates them to identify potential incidents.

- AV (Antivirus): This solution focuses on detecting and regularly scanning a system for known malicious programs.

- EDR (Endpoint Detection and Response): Deployed on individual systems, it protects against advanced-level threats and has the capability to contain and eradicate them.

Response Guidelines: Playbooks vs. Runbooks
Once an incident is identified, specific procedures must be followed to investigate the extent of the attack and prevent further damage. To save time, teams use predefined instructions:

- Playbooks: These are high-level guidelines for comprehensive incident response.

    - Example (Phishing Email Playbook): Notify stakeholders, analyze the email header and body, inspect attachments, determine if anyone opened them, isolate infected systems, and block the sender.

- Runbooks: These provide a much more detailed, step-by-step execution of specific actions during different incidents. These steps may vary depending on the resources available for the investigation.