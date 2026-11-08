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

Local inspection of `/etc/inetd.conf` on Metasploitable2 identified an uncommented Telnet service entry invoking `/usr/sbin/in.telnetd` through `/usr/sbin/tcpd`.

The Nmap observation supports that Telnet is exposed on TCP port 23. No Telnet authentication attempt or credential interception was performed.

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
- `screenshots/legacy-services-configuration.png`
- `screenshots/telnet-scan.png` (to be added)

**Verification Status:** Nmap confirmed TCP port 23 was open and identified the Telnet service. Local configuration inspection also identified an enabled Telnet entry in `/etc/inetd.conf`. No exploitation or remediation was performed. 

---

### Finding F-02 — Authentication and Password Policy

**Observation:**

An authentication configuration review of the Metasploitable2 target ("192.168.56.104") identified the following:
- The "root" and "msfadmin" accounts have passwords set, with last recorded password changes on 18 May 2010 and 16 March 2010, respectively.
- Both accounts have a maximum password age of 99,999 days, effectively disabling routine password expiry through this setting.
- `/etc/login.defs` specifies `PASS_MAX_DAYS 99999`, `PASS_MIN_DAYS 0`, and `PASS_WARN_AGE 7`.
- The active `/etc/pam.d/common-password` configuration contains `pam_unix.so nullok obscure md5`, indicating legacy MD5-based password hashing is configured, basic password-strength checks are enabled, and empty passwords are permitted under applicable PAM conditions.
- `/etc/pam.d/common-auth` uses `pam_unix.so nullok_secure`. No `pam_tally` or `pam_faillock` rule was observed in this file.

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

Local service inspection confirmed that `xinetd` was running as root and listening on TCP ports 512, 513, and 514 at the time of inspection.

The `/etc/inetd.conf` configuration contained uncommented service entries for Telnet, TFTP, `rsh,rlogin,rexec`, and `ingreslock`.

The inspected `/etc/hosts.allow` and `/etc/hosts.deny` files contained no active access-control rules. Other access restrictions were not comprehensively assessed.

These observations provide additional evidence of legacy network service exposure. No exploitation or unauthorized access was attempted.

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
- `screenshots/legacy-services-configuration.png`
- `screenshots/service-enumeration.png` (to be added)

**Verification Status:** Nmap identified 22 open TCP ports. Local process and listener inspection confirmed `xinetd` running as root and listening on TCP ports 512-514. The legacy service entries in `/etc/inetd.conf` were also verified. No exploitation or remediation was performed.

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

Nmap service-version detection alone does not establish that a component contains an exploitable vulnerability.

---

#### F-04.1 — Apache HTTP Server: Security Update Assessment 

**Operating System:** Ubuntu 8.04 LTS (Hardy)

**Affected Component:** `apache2-mpm-prefork`

**Installed Version:** `2.2.8-1ubuntu0.15`

**Security Fix Version:** `2.2.8-1ubuntu0.21`

**CVE:** CVE-2011-3192

**Ubuntu Security Advisory:** USN-1199-1

**Assessment:**

Local package inspection confirmed that `apache2-mpm-prefork` version `2.2.8-1ubuntu0.15` is installed on the Metasploitable2 target.

Ubuntu Security Notice USN-1199-1 identifies `2.2.8-1ubuntu0.21` as the corrected package version for Ubuntu 8.04.

The installed package predates the documented security update.

`CVE-2011-3192` concerns Apache HTTP Server's handling of overlapping HTTP byte-range requests. A remote attacker may cause excessive resource consumption, potentially resulting in denial of service.

The package comparison supports a finding of a missing documented security update. Exploitability has not been tested, and manually applied patches have not been ruled out.

**Supporting Evidence:**
- `screenshots/ubuntu-version.png`
- `screenshots/apache-package-versions.png`</br>


#### F-04.2 — vsftpd 2.3.4: Binary Verification

**Service:** FTP (TCP port 21)

**Executable:** `/usr/sbin/vsftpd`

**Reported Version:** 2.3.4

**Package Manager Status:** `pn` — no installed vsftpd package recorded by dpkg.

**SHA-256:**

`01c115bd2b95eee4327f08066381121597891e5d9fba9475e1cdb2874aafa205`

**Relevant CVE:** CVE-2011-2523 (applicability unverified)

**Assessment:**

Nmap identified `vsftpd 2.3.4` listening on TCP port 21. Local inspection independently confirmed that `/usr/sbin/vsftpd` reports version 2.3.4.

The executable exists on the target system, but the Debian package manager does not report an installed vsftpd package. File inspection identified it as a stripped, 32-bit ELF executable.

The SHA-256 hash was collected to identify the exact executable examined.

`CVE-2011-2523` concerns a maliciously modified distribution of vsftpd 2.3.4. If this modification is present, unauthorized remote command execution may be possible.

The reported version alone does not establish that the backdoor is present. The executable has not been matched against a trusted reference binary, and no exploitation was performed.

**Supporting Evidence:**
- `screenshots/vsftpd-binary-verification.png`</br>


#### F-04.3 — ProFTPD 1.3.1: Service and Configuration Verification

**Service:** FTP (TCP port 2121)

**Installed Package:** proftpd 1.3.1-6ubuntu1

**Executable:** `/usr/sbin/proftpd`

**Executable Version:** 1.3.1

**Relevant CVE for Further Investigation:** `CVE-2010-4652` (applicability unconfirmed)

**Assessment:**

Nmap identified ProFTPD 1.3.1 on TCP port 2121. Local package inspection confirmed that proftpd version 1.3.1-6ubuntu1 is installed.

Executable inspection independently confirmed ProFTPD version 1.3.1. Process and network-listener inspection confirmed that the ProFTPD daemon (`PID 4568` at the time of inspection) was accepting connections on TCP port 2121.

The main configuration includes `/etc/proftpd/modules.conf`, which contains uncommented `LoadModule` directives for `mod_sql.c`, `mod_sql_mysql.c`, and `mod_sql_postgres.c`.

A configuration syntax test completed successfully when executed with privileges.

A search of `/etc/proftpd/` did not identify uncommented SQL authentication or database-connection directives matching the patterns tested. The direct inclusion of `/etc/proftpd/sql.conf` in the main configuration was commented out.

`CVE-2010-4652` is associated with ProFTPD's SQL module. The module loading configuration warrants further investigation, but the available evidence does not establish whether the vulnerability is applicable to this installed package or its active configuration.

**Potential Impact:**

If an applicable vulnerability is present and its prerequisites are satisfied, the FTP service could expose the system to unauthorized access, service disruption, or other security consequences depending on the specific vulnerability. 

**Supporting Evidence:**
- `scans/nmap-service-versions.txt`
- `screenshots/proftpd-verification.png`
- Additional configuration inspection results (Verified in terminal; screenshots not yet collected).

**Verification Status:**
- Installed package, executable version, configuration syntax, and listening service (verified).
- SQL module runtime state, security-patch applicability, and CVE exploitability remain unconfirmed.
- No exploitation or remediation was performed.</br>


#### F-04.4 — MySQL 5.0: Security Update Assessment

**Service:** MySQL (TCP port 3306)

**Operating System:** Ubuntu 8.04 LTS (Hardy)

**Installed Package:** mysql-server-5.0

**Installed Version:** 5.0.51a-3ubuntu5

**Server Executable Version:** 5.0.51a-3ubuntu5

**Security Fix Version:** 5.0.51a-3ubuntu5.4

**Ubuntu Security Advisory:** USN-671-1

**Relevant CVEs:**
- CVE-2008-2079
- CVE-2008-3963
- CVE-2008-4097
- CVE-2008-4098

**Assessment:**

An Nmap service-version scan identified MySQL 5.0.51a on TCP port 3306.

Local package inspection confirmed that `mysql-server-5.0` version `5.0.51a-3ubuntu5` is installed on the Metasploitable2 target.

The MySQL server executable independently reported version `5.0.51a-3ubuntu5`.

Network-listener inspection confirmed that the MySQL daemon (PID 4253 at the time of inspection) was listening on `0.0.0.0:3306`, indicating that the service was bound to all available IPv4 interfaces.

Ubuntu Security Notice USN-671-1 identifies `5.0.51a-3ubuntu5.4` as the corrected MySQL server package version for Ubuntu 8.04.

The installed package predates this documented security update.

The advisory addresses multiple security issues involving MySQL table-file handling and improper processing of empty bit-string literals.

**Potential Impact:**

The documented vulnerabilities include:
- Possible bypass of certain database privilege checks by authenticated users through affected table-creation functionality.
- Potential denial of service resulting from improper handling of specially crafted SQL statements.

Actual exploitability depends on the affected functionality, user privileges, configuration, and applicable security fixes.

**Supporting Evidence:**
- `scans/nmap-service-versions.txt`
- `screenshots/mysql-version-verification.png`

**Verification Status:**
- The installed MySQL package version, server executable version, and listening service (verified).
- The installed package predates the security fix documented in USN-671-1. Manually applied patches and exploitability were not independently verified.
- No exploitation or remediation was performed.</br>


#### F-04.5 — Remaining Software Components Under Investigation

**OpenSSH:** Security Update Assessment.

**Service:** OpenSSH (TCP port 22)

**Installed Package:** openssh-server

**Installed Version:** 1:4.7p1-8ubuntu1

**Executable Version:** OpenSSH_4.7p1 Debian-8ubuntu1

**Installed OpenSSL Library:** libssl0.9.8 version 0.9.8g-4ubuntu3.18

**OpenSSH Security Fix Version:** 1:4.7p1-8ubuntu1.1

**Relevant Security Advisories:**
- USN-612-1 — OpenSSL Security Update
- USN-612-2 — OpenSSH Security Update

**Relevant CVE:** CVE-2008-0166

**Assessment:**

Local package inspection confirmed that the installed OpenSSH server package version 1:4.7p1-8ubuntu1 predates the documented Ubuntu 8.04 security update 1:4.7p1-8ubuntu1.1.

The installed OpenSSL library package version 0.9.8g-4ubuntu3.18 is later than the initial security-fix version 0.9.8g-4ubuntu3.1.

CVE-2008-0166 concerns predictable cryptographic keys generated by affected OpenSSL implementations. Although the OpenSSL package is at a later revision, previously generated SSH keys may still require verification.

The `ssh-vulnkey` utility was not found in the command search path. Package inspection also returned no matching `openssh-blacklist` or `openssh-blacklist-extra` packages.

Consequently, the strength of existing SSH host and authentication keys could not be determined.

**Potential Impact:** If affected weak SSH keys are present, an attacker may be able to compromise SSH authentication or host identity verification.

**Supporting Evidence:**
- `scans/nmap-service-versions.txt`
- Local OpenSSH/OpenSSL package and executable version outputs.
- Local checks for the weak-key verification utility and blacklist packages.

**Verification Status:** Package versions and advisory comparisons were completed. Weak-key presence, manually applied patches, and exploitability were not independently verified. No exploitation or remediation was performed. 


**PostgreSQL 8.3:** Security Update Assessment

**Service:** PostgreSQL (TCP port 5432)

**Operating System:** Ubuntu 8.04 LTS (Hardy)

**Installed Package:** postgresql-8.3

**Installed Version:** 8.3.1-1

**Client Version:** 8.3.1

**Running Server Version:** 8.3.1

**Security Fix Version:** 8.3.7-0ubuntu8.04.1

**Ubuntu Security Advisory:** USN-753-1

**Relevant CVE:** CVE-2009-0922

**Assessment:**

Local package inspection confirmed PostgreSQL package version `8.3.1-1`. The `psql` executable reported version `8.3.1`, and a read-only SQL query independently confirmed that the running server reported `PostgreSQL 8.3.1`.

Network-listener inspection identified the PostgreSQL process listening on `0.0.0.0:5432` and `[::]:5432`, indicating wildcard IPv4 and IPv6 bindings.

Ubuntu Security Notice USN-753-1 identifies `8.3.7-0ubuntu8.04.1` as the security-fix package version for Ubuntu 8.04. The installed package predates that update.

CVE-2009-0922 concerns a potential denial of service condition involving PostgreSQL encoding conversion errors. The advisory describes an attack requiring authenticated access.

**Potential Impact:** An affected PostgreSQL service could experience denial of service if the vulnerability's prerequisites are satisfied.

**Supporting Evidence:**
- `scans/nmap-service-versions.txt`
- Local package, executable, SQL-query and listener outputs inspected in the terminal; screenshots not yet collected. 

**Verification Status:** Installed package version, client version, running server version and listening service were verified. The package predates the documented Ubuntu security update. Manually applied patches and exploitability were not independently verified. No exploitation or remediation was performed.

**OpenSSH Verification Status:** Security-advisory assessment remains pending.</br>

**Overall Risk:** Provisional — additional components under review.</br>

**Overall Potential Impact:**

- Unsupported or unpatched network-facing software may expose the system to known vulnerabilities, potentially resulting in denial of service, unauthorized access, or system compromise.
- The actual impact depends on the affected component, configuration, applicable security fixes.</br>

**Recommendation:**
- Verify installed software versions and security patch levels.
- Compare package versions against vendor security advisories and relevant CVEs.
- Replace unnecessary legacy FTP service with supported alternatives, using SFTP where appropriate.
- Replace unsupported operating systems and services with supported alternatives.
- Apply available security updates in an appropriately maintained environment.
- Disable unnecessary network-facing services.</br>

**Evidence Required:**
- Nmap service version detection results identifying exposed services and their reported versions (collected).
- Ubuntu operating system identification (verified).
- Apache package inventory and advisory comparison (verified).
- vsftpd executable version and SHA-256 fingerprint (verified).
- ProFTPD package, executable, configuration syntax, and listening-port verification (verified; configuration evidence screenshots pending).
- MySQL package version, server executable version, listening-port verification, and security-advisory comparison (verified).
- OpenSSH security advisory and patch applicability assessment (pending).
- PostgreSQL package, client and running-server version, network listener inspection, and security-advisory comparison (verified; screenshots pending).</br>

**Evidence:**
- `scans/nmap-service-versions.txt`
- `screenshots/ubuntu-version.png`
- `screenshots/apache-package-versions.png`
- `screenshots/vsftpd-binary-verification.png`
- `screenshots/proftpd-verification.png`
- `screenshots/mysql-version-verification.png`
- `screenshots/software-versions.png` (to be added)</br>

**References:**
- `https://ubuntu.com/security/notices/USN-1199-1`
- `https://ubuntu.com/security/CVE-2011-3192`
- `https://nvd.nist.gov/vuln/detail/CVE-2011-2523`
- `https://nvd.nist.gov/vuln/detail/CVE-2010-4652`
- `https://ubuntu.com/security/notices/USN-671-1`</br>

**Overall Verification Status:** 

The Ubuntu 8.04 operating system release and installed Apache package versions were verified locally.

**Apache HTTP Server:** The installed `apache2-mpm-prefork` package version `2.2.8-1ubuntu0.15` predates the documented security fix `2.2.8-1ubuntu0.21` for CVE-2011-3192 in Ubuntu Security Notice USN-1199-1.

**vsftpd:** The executable reported version `2.3.4` and its SHA-256 fingerprint were verified. Applicability of CVE-2011-2523 remains unconfirmed because the executable has not been validated against a trusted reference binary.

**ProFTPD:** The installed package version `1.3.1-6ubuntu1` and executable version `1.3.1` and running service on TCP port 2121 were verified. SQL module-loading directives were identified, but CVE-2010-4652 applicability remains unconfirmed.

**MySQL:** The installed server package version `5.0.51a-3ubuntu5` predates the documented security fix `5.0.51a-3ubuntu5.4` in Ubuntu Security Notice USN-671-1. The running MySQL service was confirmed listening on TCP port 3306.

**PostgreSQL:** The package version `8.3.1`, TCP port 5432 listeners, and comparison against Ubuntu Security Notice USN-753-1 were verified. The installed package predates the documented security update.

**Remaining Components:**  OpenSSH package version 1:4.7p1-8ubuntu1 and OpenSSL library version 0.9.8g-4ubuntu3.18 were also verified and compared against Ubuntu Security Notice USN-612-1 and USN-612-2. The OpenSSH package predates the documented security update, while the OpenSSL package is later than the initial security fix. Existing SSH keys were not assessed for weakness. Exploitability and manually applied patches remain unverified.

**Assessment Limitations:** Exploitability was not independently tested and the possibility of manually applied security patches has not been ruled out. No exploitation or remediation was performed.

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

