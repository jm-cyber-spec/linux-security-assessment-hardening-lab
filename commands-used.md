## Commands Used — Linux Security Assessment and Hardening Lab

### 1. Purpose

#### Lab Environment and Command Execution

**Analyst VM:** Kali Linux (192.168.56.101)

**Target VM:** Metasploitable2 (192.168.56.104)

This document records the commands used during the Linux Security Assessment and Hardening Lab. 

Kali Linux was used for network discovery, connectivity testing, and Nmap scanning.

Metasploitable2 was used for local package  inspection, service configuration review, user and group inspection, file permission auditing, and process and network-listener verification.

This document distinguishes commands executed during the assessment from commands included as reference material.

All assessment activities were performed within the authorized local VirtualBox lab.

---

### 2. Network Discovery and Scanning

#### 2.1 Identify Network Interfaces

**Executed on:** Kali Linux (Analyst VM)

```
ip a
```

**Purpose:** Displays network interfaces and their assigned IP addresses.

**Usage:** Helps identify the Kali Linux Host-only network interface and verify its network configuration.

#### 2.2 Check Network Connectivity

**Executed on:** Kali Linux (Analyst VM)

```
ping -c 4 192.168.56.104
```

**Purpose:** Sends four ICMP echo requests to the Metasploitable2 target.

**Usage:** Checks basic network connectivity between Kali Linux and the target machine. 

#### 2.3 Scan Target Ports Using Nmap

**Executed on:** Kali Linux (Analyst VM)

```
nmap 192.168.56.104
```

**Purpose:** Performs a basic TCP port scan against the target.

**Usage:** Identifies accessible ports on the target machine.

**Evidence:** No separate output file was retained for this basic scan. The saved service-version scan is documented in Section 2.5.

#### 2.4 Detect Service Versions

**Executed on:** Kali Linux (Analyst VM)

```
nmap -sV 192.168.56.104
```

**Purpose:** Attempts to identify services and their versions on discovered ports.

**Usage:** Helps document exposed services and investigate potentially outdated software.

**Evidence:** [Nmap Service Version Scan](scans/nmap-service-versions.txt)

#### 2.5 Save Nmap Results

**Executed on:** Kali Linux (analyst VM)

```
nmap -sV 192.168.56.104 -oN ~/nmap-service-versions.txt
```

**Purpose:** Performs service-version detection against the Metasploitable2 target and saves the scan output in normal text format.

**Evidence Handling:** The scan was executed on Kali Linux and initially saved as `~/nmap-service-versions.txt`. The output was subsequently uploaded to the GitHub repository as `scans/nmap-service-versions.txt`.

**Verification Status:** Executed; scan output collected.

---

### 3. Linux System Inspection

#### 3.1 Identify Current User

**Executed on:** Metasploitable2 (Target VM)

```
whoami
```

**Purpose:** Displays the currently logged-in username.

#### 3.2 Display User and Group Information

**Executed on:** Metasploitable2 (Target VM)

```
id
```

**Purpose:** Displays the current user's UID, GID, and group memberships.

#### 3.3 Review File Permissions

**Executed on:** Metasploitable2 (Target VM)

```
ls -l
```

**Purpose:** Lists files in the current directory, including their permissions, ownership, and other file details.

#### 3.4 Directory Permission Inspection — Not Performed

No separate directory-specific permission inspection was documented during this assessment. File permission checks are recorded in Sections 3.3 and 4.9.

#### 3.5 Review Running Processes

**Executed on:** Metasploitable2 (Target VM)

```
ps aux
```

**Purpose:** Displays all running processes and their associated users.

**Usage:** Helps inspect process ownership and identify services requiring further review.

#### 3.6 Inspecting Listening Network Sockets

**Executed on:** Metasploitable2 (Target VM)

**Purpose:** Identify listening TCP and UDP sockets and verify network service exposure.

**Commands used:**

```
command -v ss
```
Confirmed that the utility is available at `/sbin/ss`

```
ss -tuln
```
Displayed TCP and UDP sockets and completed without errors.

```
ss -tuln | grep -E ':(21|22|23|80|3306|5432)[[:space:]]'
```
Displayed selected listening ports associated with FTP, SSH, Telnet, HTTP, MySQL, and PostgreSQL.

These commands were executed during the final documentation review, rather than during the original network assessment.

**Related Findings:**
- [F-01 — Telnet Service Enabled](findings.md)
- [F-03 — Excessive Service Exposure](findings.md)
- [F-04 — Potentially Outdated Software](findings.md)

**Evidence:**
- [Nmap Service Version Scan](scans/nmap-service-versions.txt)
- [Listening Network Sockets Verification](screenshots/ss-listening-ports-verification.png)

---

### 4. Service and Log Inspection


#### 4.1 — 4.2 — Reserved Sections (Legacy System Compatibility)

The original reference entries for `systemctl` and `journalctl` were removed because Metasploitable2 runs Ubuntu 8.04, which predates systemd. Service and configuration inspection performed in this lab is documented in Section 4.3. 


#### 4.3 Legacy Network Service and Configuration Inspection

**Executed On:** Metasploitable2 (Target VM)

**Purpose:** Inspect legacy network services, their configuration, and the processes responsible for listening on network ports.

**Commands used:**

```
sudo netstat -tulnp | grep -E ':(512|513|514)\b'
``` 
Identifies listening services associated with TCP ports 512, 513, and 514, including process information.

```
ps aux | grep '[x]inetd'
```
Identifies the running `xinetd` process and its process owner.

```
cat /etc/inetd.conf
```
Displays legacy service definitions configured through the inetd-compatible configuration.

```
cat /etc/hosts.allow
cat /etc/hosts.deny
```
Displays TCP Wrappers access control configuration files.

**Related Findings:** F-01 — Telnet Service Exposure, F-03 — Excessive Network Service Exposure.

**Evidence:**
- [Nmap Service Version Scan](scans/nmap-service-versions.txt)
- [Legacy Services Configuration Screenshot](screenshots/legacy-services-configuration.png)


#### 4.4 Apache HTTP Server Version Inspection 

**Executed on:** Metasploitable2 (Target VM)

**Purpose:** Identify the operating system release and installed Apache package versions for comparison against Ubuntu security advisories.

**Commands used:**

```
cat /etc/lsb-release
```
Displays the Ubuntu distribution and release information.

```
dpkg -l 'apache2*'
```
Lists Apache-related packages and their package installation states and versions.

**Related Findings:** F-04.1 — Apache HTTP Server.

**Evidence:**
- [Ubuntu Version Screenshot](screenshots/ubuntu-version.png)
- [Apache Package Versions Screenshot](screenshots/apache-package-versions.png)

  
#### 4.5 vsftpd Version and Binary Inspection

**Executed on:** Metasploitable2 (Target VM)

**Purpose:** Identify the vsftpd executable version, package installation status, executable type, and SHA-256 fingerprint.

**Commands used:**

```
/usr/sbin/vsftpd -v
```
Displays the version reported by the vsftpd executable.

```
dpkg -l vsftpd
```
Checks whether vsftpd is registered as an installed Debian package.

```
file /usr/sbin/vsftpd
```
Identifies the executable's file format.

```
sha256sum /usr/sbin/vsftpd
```
Calculates the executable's SHA-256 fingerprint for potential comparison against a trusted reference.

**Related Findings:** F-04.2 — vsftpd

**Evidence:**
- [vsftpd Binary Verification Screenshot](screenshots/vsftpd-binary-verification.png)
- [Nmap Service Version Scan](scans/nmap-service-versions.txt)


#### 4.6 ProFTPD Service and Configuration Inspection

**Executed on:** Metasploitable2 (Target VM)

**Purpose:** Identify the installed ProFTPD version, listening service and relevant SQL module configuration.

**Commands used:**

```
dpkg -l proftpd
/usr/sbin/proftpd -v
```
Checks the installed package and executable versions.

```
dpkg-query -W -f='${Package} ${Version}\n' proftpd
```
Displays the installed ProFTPD package name and version using a custom output format. 

```
sudo netstat -tlnp | grep proftpd
```
Identifies the ProFTPD listening socket and process.

```
cat /etc/proftpd/proftpd.conf
cat /etc/proftpd/modules.conf
```
Displays the main configuration and module loading directives.

```
grep -R -E 'SQLEngine|SQLAuthenticate|SQLConnectInfo|SQLUserInfo|SQLGroupInfo' /etc/proftpd/
```
Searches configuration files for SQL authentication and connection directives.

```
sudo /usr/sbin/proftpd -t
```
Checks ProFTPD configuration syntax without starting a new service.

**Related Findings:** F-04.3 — ProFTPD

**Evidence:** [ProFTPD Verification Screenshot](screenshots/proftpd-verification.png)


#### 4.7 MySQL Server Version and Listener Inspection

**Executed on:** Metasploitable2 (Target VM)

**Purpose:** Verify installed MySQL package versions, executable versions and network-listener configuration.

**Commands used:**

```
dpkg -l 'mysql-server*' | grep '^ii'
```
Lists installed MySQL server packages.

```
mysql --version
mysqld --version
```
Displays the MySQL client and server executable versions.

```
sudo netstat -tlnp | grep ':3306'
```
Identifies the process listening on MySQL's TCP port 3306.

**Related Findings:** F-04.4 — MySQL

**Evidence:** [MySQL Version Verification Screenshot](screenshots/mysql-version-verification.png)



#### 4.8 Authentication and Password Policy Inspection

**Executed on:** Metasploitable2 (Target VM)

**Purpose:** Inspect local account password status, password-aging settings and PAM authentication configuration.

**Commands used:**

```
sudo passwd -S msfadmin
```
Displays password status and aging information for the `msfadmin` account.

```
cat /etc/login.defs
```
Displays system-wide login and password aging configuration defaults.

```
cat /etc/pam.d/common-auth
cat /etc/pam.d/common-password
```
Displays PAM authentication and password policy configuration.

**Related Findings:** F-02 — Authentication and Password Policy.

**Evidence:**
- [Password Aging Policy](screenshots/password-aging-policy.png)
- [Password Policy Configuration](screenshots/password-policy-configuration.png)
- [Authentication Configuration](screenshots/authentication-configuration.png)


#### 4.9 User Privileges and File Permission Inspection

**Executed on:** Metasploitable2 (Target VM)

**Purpose:** Inspect user identity, administrative privileges, group memberships, and permissions on security-sensitive Linux files.

**Commands used:**

```
whoami
id
```
Displays the current user identity, user ID, group ID, and group memberships.

```
sudo -l
```
Lists the commands the current user is authorized to execute through `sudo`.

```
getent group sudo
getent group admin
```
Checks membership information for the `sudo` and `admin` groups.

```
ls -l /etc/passwd /etc/shadow /etc/sudoers
```
Displays permissions and ownership of security-sensitive account and privilege configuration files.

**Related Findings:** F-05 — Privilege Separation

**Evidence:** [Sudo Privileges Screenshot](screenshots/sudo-privileges.png)

Shows the output of `sudo -l`, confirming the inspected user's sudo privileges.


#### 4.10 PostgreSQL Package, Server and Listener Inspection

**Executed on:** Metasploitable2 (Target VM)

**Purpose:** Verify installed PostgreSQL packages, the client and running server versions, and the network listener.

**Commands used:**

```
dpkg -l 'postgresql*'
```
Lists PostgreSQL related packages and their installation states.

```
psql --version
```
Displays the PostgreSQL client version.

```
sudo netstat -tlnp | grep ':5432'
```
Identifies the process listening on TCP port 5432.

```
sudo -u postgres psql -c "SELECT version();"
```
Queries the running PostgreSQL server to retrieve its reported version.

**Related Findings:** F-04.5 — PostgreSQL Security Update Assessment.

**Evidence:**
- [Nmap Service Version Scan](scans/nmap-service-versions.txt)
- [PostgreSQL Package and Server Version Verification](screenshots/postgresql-version-verification.png)
- [PostgreSQL Listener Verification](screenshots/postgresql-listener-verification.png)


#### 4.11 OpenSSH Package and Security Verification

**Executed on:** Metasploitable2 (Target VM)

**Purpose:** Verify the installed OpenSSH and OpenSSL package versions, inspect the OpenSSH executable version, and determine whether local SSH weak-key assessment utilities are available.

**Commands used:**

```
dpkg-query -W -f='${Package} ${Version}\n' openssh-server
```
Confirms the installed OpenSSH server package and its version.

```
/usr/sbin/sshd -V
```
Attempts to display the OpenSSH server version. The installed version does not support -V, but the resulting error output includes the OpenSSH and OpenSSL version information.

```
dpkg-query -W -f='${Package} ${Version}\n' libssl0.9.8
```
Confirms the installed OpenSSL library package version for comparison against the Ubuntu security advisory.

```
command -v ssh-vulnkey
```
Checks whether `ssh-vulnkey` is available in the shell's command search path. No path was returned.

```
dpkg -l openssh-blacklist openssh-blacklist-extra
```
Checks the local package database for the specified blacklist packages. No matching packages were found.

**Related Findings:** F-04.5 — OpenSSH Security Update Assessment

**Evidence:**
- [Nmap Service Version Scan](scans/nmap-service-versions.txt)
- [OpenSSH and OpenSSL Package Verification](screenshots/openssh-openssl-package-verification.png)
- [OpenSSH Executable Version Verification](screenshots/openssh-executable-verification.png)
- [SSH Weak-Key Tool Availability Verification](screenshots/openssh-weak-key-tool-availability.png)

**Security References:**
- Ubuntu Security Notice USN-612-1
- Ubuntu Security Notice USN-612-2 

---

### 5. Command Execution Notes
- Commands must be executed only within the authorized local lab.
- Replace generic placeholder paths with actual paths from your environment.
- Some commands may require elevated privileges (`sudo`).

---

### 6. Evidence and Results

The following scan output and screenshots were collected during the Linux Security Assessment and Hardening Lab.

**Network Scanning**

- [Nmap Service Version Scan](scans/nmap-service-versions.txt)

**Service and Configuration Inspection**

- [Legacy Services Configuration](screenshots/legacy-services-configuration.png)
- [Ubuntu Version](screenshots/ubuntu-version.png)
- [Apache Package Versions](screenshots/apache-package-versions.png)
- [vsftpd Binary Verification](screenshots/vsftpd-binary-verification.png)
- [ProFTPD Verification](screenshots/proftpd-verification.png)
- [MySQL Version Verification](screenshots/mysql-version-verification.png)

**Authentication and Privilege Inspection**

- [Sudo Privileges](screenshots/sudo-privileges.png)
- [Password Aging Policy](screenshots/password-aging-policy.png)
- [Password Policy Configuration](screenshots/password-policy-configuration.png)
- [Authentication Configuration](screenshots/authentication-configuration.png)

Detailed observations, security implications, and verification limitations are documented in `findings.md`.

Hardening recommendations are documented in `hardening-recommendations.md`.

---

### 7. References

- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Linux Manual Pages (`man`)](https://man7.org/linux/man-pages/)
