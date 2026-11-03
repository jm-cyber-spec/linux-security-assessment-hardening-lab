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

### Finding F-02 — Authentication and Password Policy

**Observation:**

An authentication configuration review of the Metasploitable2 target ("192.168.56.104") identified the following:
- The "root" and "msfadmin" accounts have passwords set, with last recorded password changes on 18 May 2010 and 16 March 2010, respectively.
- Both accounts have a maximum password age of 99,999 days, effectively disabling routine password expiry through this setting.
- `/etc/login.defs` specifies `PASS_MAX_DAYS 99999`, `PASS_MIN_DAYS 0`, and `PASS_WARN_AGE 7`.
- The active `/etc/pam.d/common-password` configuration contains `pam_unix.so nullok obscure md5`, indicating legacy MD5-based password hashing is configured, basic password-strength checks are enabled, and empty passwords are permitted under applicable PAM conditions.
- `/etc/pam.d/common-auth` uses `pam_unix.so nullock_secure`. No `pam_tally` or `pam_faillock` rule was observed in this file.

**Risk:** Provisional — further assessment required.

**Potential Impact:**
- Weak password-management controls can increase the likelihood of unauthorized access.
- Legacy password hashing and permissive authentication settings may increase risk if credentials are exposed or accounts are misconfigured.

**Recommendation:**
- Review password-aging requirements and account security policies.
- Use modern, supported password-hashing algorithms.
- Review whether empty-password authentication should be permitted.
- Assess account-lockout or login rate-limiting protections.
- Enforce appropriate password-strength requirements.
- Restrict privileged account access.

**Suggested Evidence:**
- `screenshots/password-aging-policy.png`
- `screenshots/password-policy-configuration.png`
- `screenshots/authentication-configuration.png`

**Verification Status:**
Password status, password-aging settings, and selected PAM configuration files were inspected. Actual password strength, default credential usage, account-lockout behavior across all authentication services, and existing password hash formats were not verified. No password guessing or exploitation was performed.

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
|3306/tcp | MySQL | MySQL 5.0.51a-3ubuntu5|
|5432/tcp | PostgreSQL | PostgreSQL 8.3.0-8.3.7(version range reported by Nmap)|

These results indicate potentially outdated components that require further investigation.

Nmap Service version detection alone does not establish that a component contains an exploitable vulnerability.

#### F-04.1 — Apache HTTP Server: Security Update Assessment 

**Operating System:** Ubuntu 8.04 LTS (Hardy)

**Affected Component:** `apache2-mpm-prefork`

**Installed Version:** `2.2.8-1ubuntu0.15`

**Security Fix Version:** `2.2.8-1ubuntu0.21`

**CVE:** CVE-2011-3192

**Ubuntu Security Advisory:** USN-1199-1

**Assessment:**

Local Package inspection confirmed that `apache2-mpm-prefork` version `2.2.8-1ubuntu0.15` is installed on the Metasploitable2 target.

Ubuntu Security Notice USN-1199-1 identifies `2.2.8-1ubuntu0.21` as the corrected package version for Ubuntu 8.04.

The installed package predates the documented security update.

`CVE-2011-3192` concerns Apache HTTP Server's handling of overlapping HTTP byte-range requests. A remote attacker may cause excessive resource consumption, potentially resulting in denial of service.

The package comparison supports a finding of a missing documented security update. Exploitability has not been tested, and manually applied patches have not been ruled out.

**Supporting Evidence:**
- `screenshots/ubuntu-version.png`
- `screenshots/apache-package-versions.png`

#### F-04.2 — vsftpd 2.3.4: Binary Verification

**Service:** FTP (TCP port 21)

**Executable:** `/usr/sbin/vsftpd`

**Reported Version:** 2.3.4

**Package Manager Status:** `pn` — no installed vsftpd package recorded by dpkg.

**SHA-256:**

01c115bd2b95eee4327f08066381121597891e5d9fba9475e1cdb2874aafa205

**Relevant CVE:** CVE-2011-2523 (applicability unverified)

**Assessment:**

Nmap identified `vsftpd 2.3.4` listening on TCP port 21. Local inspection independently confirmed that `/usr/sbin/vsftpd` reports version 2.3.4.

The executable exists on the target system, but the Debian package manager does not report an installed vsftpd package. File inspection identified it as a stripped, 32-bit ELF executable.

The SHA-256 hash was collected to identify the exact executable examined.

`CVE-2011-2523` concerns a maliciously modified distribution of vsftpd 2.3.4. If this modification is present, unauthorized remote command execution may be possible.

The reported version alone does not establish whether this particular executable contains the backdoor.

The reported version alone does not establish that the backdoor is present. The executable has not been matched against a trusted reference binary, and no exploitation was performed.

**Supporting Evidence:**
- `screenshots/vsftpd-bianry-verification.png`

#### F-04.3 — Remaining Software Components

The following services require additional package-version and security-advisory verification:

- OpenSSH — installed package version 1:4.7p1-8ubuntu1 previously confirmed; advisory applicability requires further review.
- ProFTPD 1.3.1 — pending local version verification.
- MySQL 5.0.51a — pending local package verification.
- PostgreSQL 8.3.x — pending local package verification.

**Overall Risk:** Provisional — additional components under review. 

**Overall Potential Impact:**

- Unsupported or unpatched network-facing software may expose the system to known vulnerabilities, potentially resulting in denial of service, unauthorized access, or system compromise.
- The actual impact depends on the affected component, configuration, applicable security fixes.

**Recommendation:**
- Verify installed software versions and security patch levels.
- Compare package versions against vendor security advisories and relevant CVEs.
- Replace unnecessary legacy FTP service with supported alternatives, using SFTP where appropriate.
- Replace unsupported operating systems and services with supported alternatives.
- Apply available security updates in an appropriately maintained environment.
- Disable unnecessary network-facing services.

**Evidence Required:**
- Nmap service version detection results identifying exposed services and their reported versions (collected).
- Ubuntu operating system identification (verified).
- Apache package inventory and advisory comparison (verified).
- vsftpd executable version and SHA-256 fingerprint (verified).
- Remaining package and security-advisory checks (pending).

**Evidence:**
- `scans/nmap-service-versions.txt`
- `screenshots/ubuntu-version.png`
- `screenshots/apache-package-versions.png`
- `screenshots/vsftp-binary-verification.png`
-  `screenshots/software-versions.png` (to be added)

**References:**
- `https://ubuntu.com/security/notices/USN-1199-1`
- `https://ubuntu.com/security/CVE-2011-3192`
- `https://nvd.nist.gov/vuln/detail/CVE-2011-2523`

**Overall Verification Status:** 

The Ubuntu 8.04 operating system release and installed Apache package versions were verified locally. The installed `apache2-mpm-prefork` package predates the documented security fix for `CVE-2011-3192`.

The vsftpd executable version and SHA-256 fingerprint were verified. Applicapability of CVE-2011-2523 remains unconfirmed.

OpenSSH package identification has been completed, while further advisory verification and assessment of the remaining services are pending.

No exploitation or remediation was performed.

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

