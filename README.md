# Linux Security Assessment and Hardening Lab

**Project Overview**
This project documents a hands-on Linux security assessment using Kali Linux and Metasploitable2 in a controlled virtual lab environment.

The objective is to develop practical cybersecurity skills in network service discovery, Linux security configuration review, identification of potential security weaknesses, and defensive hardening recommendations.

**Objectives**
* Identify exposed network services using Nmap
* Understand Linux users, groups, file permissions, and priviledge separation.
* Identify potential security risks associated with insecure configurations.
* Develop practical Linux hardening recommendations.
* Document findings through evidence-based security reporting.

 **Lab Environment**
 * Analyst system: Kali Linux
 * Target system: Metasploitable2
 * Virtualization: Oracle VirtualBox
 * Networking: Host-only network, configured to isolate the assessment environment.

See lab-setup.md for setup instructions and network configuration.

**Tools**
* Nmap -- Network and service discovery.
* Linux command-line utilities -- System inspection and permission checks.
* Oracle VirtualBox -- Virtual lab environment.

Additional tools will be documented when their use in this project is confirmed.

**Project Structure**
* lab-setup.md -- Lab setup and network configuration.
* findings.md -- Security observations, evidence, impact, and validation status.
* hardening-recommendations.md -- Recommended defensive improvements and remediation priorities.
* commands-used.md -- Commands used and explanations.
* screenshots/ -- Supporting screenshots and sanitized evidence.

**Methodology**
* Establish the lab environment and define the assessment scope.
* Identify the target and enumerate exposed network services.
* Review relevant Linux security settings and permissions where access permits.
* Document observations with supporting evidence.
* Evaluate potential impact and recommend defensive controls.
* Validate implemented remediation and document the results.

**Scope and Safety**

All assessment activities are intended for an authorized local virtual lab.

Metasploitable2 is intentionally vulnerable and must be isolated from untrusted networks. Assessment activities must remain within the defined lab scope.

**Findings and Results**

Verified findings, remediation recommendations, and validation results will be documented in findings.md and hardening-recommendations.md.
Results will be added as the assessment progresses. No remediation or validation will be claimed until it has been performed and verified.

**Current Status**

**Status:** In progress

This repository is being developed as a cybersecurity learning and portfolio project. Documentation and results will be updated as the assessment and hardening activities are completed.

**Learning Outcomes**

This project aims to demonstrate a structured approach to security assessment, risk communication, Linux security fundamentals, defensive hardening, and evidence-based technical documentation.
