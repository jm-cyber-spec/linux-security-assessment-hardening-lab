# Security Findings - Metasploitable2 Assessment

## 1. Assessment Overview

| Category | Details |
| -- | -- |
Project | Linux Security Assessment and Hardening Lab
Analyst Machine | Kali Linux (192.168.56.101)
Target Machine | Metasploitable2 (192.168.56.104)
Virtualization | Oracle VirtualBox
Network Configuration | Host-only Network
Assessment Type | Controlled Educational Security Assessment

## 2. Scope and Methodology

This assessment focuses on evaluating the security configuration of a Metasploitable2 Linux system within an isolated VirtualBox Lab.

The assessment covers the following areas:
- Network service exposure and enumeration
- Linux file permissions and access controls
- Authentication and password security
- Software versions and potential outdated components
- Service configurations and privilege separation
- Security hardening and monitoring

The assessment uses network scanning, Linux configuration inspection, service enumeration, and security configuration analysis.

All testing is limited to the authorized local lab.

Detailed commands and their purposes are documented in commands-used.md.

Recommended security improvements and remediation guidance are documented in hardening-recommendations.md.

## 3. Security  Findings

### Finding F-01 — Telnet Service Exposure

**Observation:** Nmap service-version detection identified `Linux telnetd` listening on TCP port 23 on the target machine.

**Risk:** Provisional — pending further assessment.

**Potential Impact:** 
- Credentials may be exposed to network interception.
- Remote administrative sessions may be vulnerable to eavesdropping.
- Sensitive information transmitted through Telnet may be disclosed.

**Recommendation:**
- Disable Telnet if it is not required.
- Use SSH for secure remote administration.
- Restrict remote access to authorized systems.

**Evidence Required:**
- Original Nmap scan output showing TCP port 23
- Service detection results confirming the Telnet service.

**Suggested Evidence:** 
- `scans/nmap-service-versions.txt`
- `screenshots/telnet-scan.png` (to be added)

**Verification Status:** Pending Evidence

---

### Finding F-02 — Weak or Default Credentials 

**Observation:** Weak or default credentials were identified as a potential security concern during the assessment.

However, the specific account, authentication configuration, and verification results have not yet been documented.

Further investigation is required before classifying this as a confirmed finding.

**Risk:** Unassessed pending verification

**Potential Impact:** 
- Unauthorized users may gain access through predictable credentials.
- Compromised accounts may expose sensitive system information.
- Privileged account compromise may result in broader system access.

**Recommendation:**
- Replace default credentials with strong, unique passwords.
- Enforce an appropriate password policy.
- Disable unnecessary or unused accounts.
- Restrict privileged account access.
- Implement multifactor authentication where supported.

**Evidence Required:**
- Verified authentication configuration.
- Authorized authentication test results, where applicable.

**Suggested Evidence:** `screenshots/authentication-review.png` (to be added)

**Verification Status:** Pending Evidence 

---

### Finding F-03 — Excessive Network Service Exposure

**Observation:** Nmap identified 22 open TCP ports on the Metasploitable2 target (`192.168.56.104`). 

The detected services include FTP, SSH, Telnet, SMTP, SMB, remote login services, database services and web services.

**Risk:** Provisional — pending further assessment.

**Potential Impact:**
- Unnecessary services increase the system's attack surface.
- Exposed services may introduce additional security weaknesses.
- Poorly configured services may provide unauthorized access opportunities.

**Recommendation:**
- Review all exposed services and determine whether they are required.
- Disable services that are not required.
- Restrict access to necessary services through firewall rules.

**Evidence Required:**
- Nmap port scanning results.
- Service enumeration output identifying exposed ports and services.

**Suggested Evidence:**
- `scans/nmap-service-versions.txt`
- `screenshots/service-enumeration.png` (to be added)

**Verification Status:** Pending Evidence

---

### Finding F-04 — Potentially Outdated Software 

**Observation:** Older software versions were reportedly identified during the initial service detection process.

However, the specific software products, installed versions, and corresponding security advisories have not yet been recorded.

Service version detection alone does not establish that a component contains an exploitable vulnerability.

**Risk:** Unassessed pending verification

**Potential Impact:**
- Unsupported software may no longer receive security updates.
- Unpatched components may contain publicly documented vulnerabilities.
- Vulnerable services may increase the likelihood of system compromise.

**Recommendation:**
- Identify installed software and service versions.
- Compare versions against vendor security advisories.
- Review relevant CVEs using trusted vulnerability databases.
- Apply available security updates.

**Evidence Required:**
- Nmap service version detection results.
- Installed software or package inventory.

**Suggested Evidence:**
- `scans/nmap-service-versions.txt`
-  `screenshots/software-versions.png` (to be added)

**Verification Status:** Pending Evidence

---

### Finding F-05 — Privilege Separation 

**Observation:** Service privilege separation and Linux permission configurations were identified as areas requiring further investigation.

However, no specific service running with excessive privileges or incorrectly configured permission has yet been confirmed.

System-level inspection is required before reporting this as verified security weakness.

**Risk:** Unassessed pending verification

**Potential Impact:** 
- Compromised services may provide excessive system access.
- Incorrect file permissions may expose sensitive system resources.
- Excessive administrative privileges may increase the impact of unauthorized access.

**Recommendation:**
- Review service process ownership and execution privileges.
- Use dedicated least-privileged service accounts where supported.
- Audit sudo permissions and privileged groups.
- Review sensitive file and directory permissions.
- Apply the principle of least privilege across the system.

**Evidence Required:**
- Service process ownership information.
- Linux file and directory permission inspection.
- Sudo configuration and privileged group review.

**Suggested Evidence:** `screenshots/permissions-audit.png` (to be added)

**Verification Status:** Pending Evidence

---

### Finding F-06 — Security Hardening Baseline 

**Observation:** The assessment identified several areas requiring security configuration review, including:
- Legacy network services
- Authentication security
- Network service exposure
- Software maintenance
- Linux permissions
- Access restrictions and logging

**Risk:** Overall posture not yet formally assessed

**Potential Impact:** 
- Missing or inconsistent security controls may increase the likelihood and impact of unauthorized access.
- Insufficient monitoring may also delay the detection of suspicious activity.

**Recommendation:**
- Establish a documented Linux security baseline.
- Review authentication and access control policies.
- Review file permissions and privileged accounts.
- Apply security updates where supported.
- Refer to the CIS Linux Benchmarks where applicable.

**Evidence Required:**
- System configuration review.
- Linux security hardening checklist.
- Documentation of implemented security controls.

**Suggested Evidence:** `screenshots/hardening-checklist.png` (to be added)

**Verification Status:** Pending Review

---

## 4. Assessment Summary

The assessment identified six security areas requiring investigation and potential remediation.


These include:
- Legacy network protocols
- Authentication weaknesses
- Network service exposure
- Software maintenance
- Privilege separation
- Linux security hardening and monitoring

Several observations remain provisional or unverified pending collection of supporting technical evidence. 

Final risk classifications will be assigned after the relevant findings have been verified.

Overall Assessment Status: In Progress

## 5. Assessment Limitations

The following limitations apply to this assessment:

- The target is an intentionally vulnerable educational training system.
- Testing is restricted to the authorized local VirtualBox lab.
- Findings are limited to the checks actually performed.
- Risk ratings may change following further investigation.
- Recommendations are not considered implemented until remediation has been performed and validated.

## 6. Conclusion

This Linux Security Assessment and Hardening Lab demonstrates an evidence-based approach to identifying potential security weaknesses in a Linux environment.

The assessment focuses on network service exposure, authentication security, software maintenance, Linux permissions, privilege management, and system hardening.

The findings provide a structured starting point for investigating security risks and developing appropriate defensive recommendations.

Further verification, supporting evidence collection, and remediation testing are required before all findings can be considered confirmed and resolved.

The overall objective is to demonstrate practical cybersecurity skills through systematic assessment, risk identification, defensive configuration, and security validation.

## 7. References

- Nmap Documentation
- CIS Benchmarks
- National Vulnerability Database (NVD)
- MITRE Common Vulnerabilities and Exposure (CVE)
- OpenSSH Documentation

