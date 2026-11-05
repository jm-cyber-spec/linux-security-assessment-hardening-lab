# Linux Security Assessment and Hardening Lab

## Project Overview

This project documents a hands-on Linux security assessment using Kali Linux and Metasploitable2 in a controlled virtual lab environment.

The objective is to develop practical cybersecurity skills in network service discovery, Linux security configuration review, identification of potential security weaknesses, and defensive hardening recommendations.

## Objectives
* Identify exposed network services using Nmap
* Understand Linux users, groups, file permissions, and privilege separation.
* Identify potential security risks associated with insecure configurations.
* Develop practical Linux hardening recommendations.
* Document findings through evidence-based security reporting.

## Lab Environment
 * **Virtualization:** Oracle VirtualBox
 * **Analyst system:** Kali Linux (`192.168.56.101`)
   - Adapter 1: NAT
   - Adapter 2: Host-Only
 * **Target system:** Metasploitable2 (`192.168.56.104`)
   - Host-Only network
 * **Target Operating System:** Ubuntu 8.04 LTS (Hardy Heron)

The Metasploitable2 target was isolated from external networks. Kali Linux was used for network scanning and security assessment activities.

See [Lab Setup](lab-setup.md) for setup instructions and network configuration.

## Tools
* Nmap — Network and service discovery.
* Linux command-line utilities — System inspection and permission checks.
* Oracle VirtualBox — Virtual lab environment.

Additional tools will be documented when their use in this project is confirmed.

## Project Structure
* [Lab Setup](lab-setup.md) — Virtual Lab setup and network configuration.
* [Security Findings](findings.md) — Security observations, supporting evidence, impact, and verification limitations.
* [Hardening Recommendations](hardening-recommendations.md) — Recommended defensive improvements and remediation priorities.
* [Commands Used](commands-used.md) — Commands executed during the assessment and their purposes.
* [Scan results](scans) — Saved Nmap scan outputs
* [Screenshots](screenshots) — Supporting visual evidence.

## Methodology
* Establish the lab environment and define the assessment scope.
* Identify the target and enumerate exposed network services.
* Review relevant Linux security settings and permissions where access permits.
* Document observations with supporting evidence.
* Evaluate potential impact and recommend defensive controls.
* Plan future remediation validation and documentation.

## Scope and Safety

All assessment activities are intended for an authorized local virtual lab.

Metasploitable2 is intentionally vulnerable and must be isolated from untrusted networks. Assessment activities must remain within the defined lab scope.

## Findings and Results

The assessment documented security observations relating to:
- Telnet service exposure.
- Authentication and password policy configuration.
- Excessive network service exposure.
- Potentially outdated software components.
- User privileges and privilege separation.
- Linux security hardening baseline.

Network scanning, local configuration inspection, package-version verification, and security-advisory comparisons were performed within the controlled lab.

Findings include supporting evidence, security implications, recommended defensive improvements, and verification limitations.

See [Security Findings](findings.md) and [Hardening Recommendations](hardening-recommendations.md).

No exploitation or remediation was performed as part of the documented assessment.

## Current Status

**Status:** Security assessment and documentation in progress.

**Completed:**
- Network discovery and service-version scanning.
- Inspection of legacy services and authentication.
- Initial privilege and file-permission assessment.
- Selected software package and security-advisory comparisons.
- Documentation of findings and supporting evidence.

**Pending:**
- Remaining software verification, including OpenSSH and PostgreSQL.
- Further validation of selected authentication and privilege-related observations.
- Repository documentation consistency review.
- Controlled hardening and remediation validation, if undertaken.

This repository is being developed as a cybersecurity learning and portfolio project.

## Learning Outcomes

This project aims to demonstrate a structured approach to security assessment, risk communication, Linux security fundamentals, defensive hardening, and evidence-based technical documentation.
