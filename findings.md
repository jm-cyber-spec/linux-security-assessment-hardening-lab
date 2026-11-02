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

**Verification Status:** Service exposure was confirmed by Nmap scan, while configuration and security impact remain under review.

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

**Verification Status:** Service exposure was confirmed by Nmap scan, while configuration and security impact remain under review.

---

### Finding F-04 — Potentially Outdated Software 

**Observation:**

An Nmap service-version scan of the Metasploitable2 target(`192.168.56.104`) identified several older software versions.

Port | Service | Detected Version
|--|--|--|
|21/tcp | FTP | vsftpd 2.3.4|
|22/tcp | SSH | OpenSSH 4.7p1 Debian 8ubuntu1|
|80/tcp | HTTP | Apache HTTP Server 2.2.8|
|2121/tcp |FTP | ProFTPD 1.3.1|
|3306/tcp | MySQL | MySQL 5.0.5.1a-3ubuntu5|
|5432/tcp | PostgreSQL | PostgreSQL 8.3.0-8.3.7(version range reported by Nmap)|

These results indicate potentially outdated components that require further investigation.

Nmap Service version detection alone does not establish that a component contains an exploitable vulnerability.

**Risk:** Unassessed pending verification

**Potential Impact:**
- Unsupported software may no longer receive security updates.
- Unpatched components may contain publicly documented vulnerabilities.
- Vulnerable services may increase the likelihood of system compromise.

**Recommendation:**
- Verify installed software and service versions.
- Compare identified versions against vendor security advisories.
- Review relevant CVEs using trusted vulnerability databases.
- Apply available security updates or replace unsupported components where appropriate.

**Evidence Required:**
- Nmap service version detection results.
- Installed software or package inventory.

**Suggested Evidence:**
- `scans/nmap-service-versions.txt`
-  `screenshots/software-versions.png` (to be added)

**Verification Status:** 

Service versions were identified through Nmap scanning. Installed package versions, security patch status, and applicable CVEs has not yet been verified.

---

### Finding F-05 — Privilege Separation 

**Observation:** 

The assessment identified several privilege-separation concerns on the Metasploitable2 target:
- The `msfadmin` account has unrestricted sudo privileges, as confirmed by the output of  `sudo -l`, which displays (`(ALL)ALL`). This allows the account to execute commands with root privileges.
- The `/etc/inetd.conf` configuration contains legacy remote access services (`rsh`,`rlogin`, and `rexec`) configured to run as `root`.
- The `ingreslock` service is configured to launch `/bin/bash` with `root`.
- The `root` and `msfadmin` accounts have password maximum age settings of 99,999 days, effectively allowing passwords to remain unchanged for an extended period.

**Risk:** Provisional — further assessment required

**Potential Impact:** 
- If one of these exposed services is compromised, its root-level execution context could allow an attacker to execute commands with administrative privileges.
- Additionally, compromise of the `msfadmin` account could provide administrative access through its unrestricted `sudo` permissions. The presence of legacy remote-access services may also increase the attack surface, particularly where insecure authentication or communication mechanisms are involved. 

**Recommendation:**
- Review service process ownership and execution privileges.
- Avoid configuring network-facing services to execute with root privileges unless strictly necessary.
- Disable legacy remote access services where they are not required.
- Prefer SSH for secure remote communication. 
- Apply the principle of least privilege to all user accounts and services.

**Evidence Required:**
- Service process ownership information.
- Linux file and directory permission inspection.
- Sudo configuration and privileged group review.

**Suggested Evidence:** 
- `screenshots/sudo-privileges.png`
- `screenshots/legacy-services-configuration.png`
- `screenshots/permissions-audit.png` (to be added)
- `screenshots/sudo-and-groups-review.png` (to be added)

**Verification Status:** 

The sudo privileges and service configuration were inspected. No successful remote root access or privilege-escalation was tested. No remediation was performed 

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

**Suggested Evidence:** 
- `screenshots/hardening-checklist.png` (to be added)
- `screenshots/hardening-validation.png` (to be added)

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

