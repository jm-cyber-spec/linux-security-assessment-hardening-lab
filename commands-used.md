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

`ip a`

**Purpose:** Displays network interfaces and their assigned IP addresses.

**Usage:** Helps identify the Kali Linux Host-only network interface and verify its network configuration.

#### 2.2 Check Network Connectivity

`ping -c 4 192.168.56.104`

**Purpose:** Sends four ICMP echo requests to the Metasploitable2 target.

**Usage:** Checks basic network connectivity between Kali Linux and the target machine. 

#### 2.3 Scan Target Ports Using Nmap

`nmap 192.168.56.104`

**Purpose:** Performs a basic TCP port scan against the target.

**Usage:** Identifies accessible ports on the target machine.

#### 2.4 Detect Service Versions

`nmap -sV 192.168.56.104`

**Purpose:** Attempts to identify services and their versions on discovered ports.

**Usage:** Helps document exposed services and investigate potentially outdated software.

#### 2.5 Save Nmap Results

**Executed on:** Kali Linux (analyst VM)

`nmap -sV 192.168.56.104 -oN ~/nmap-service-versions.txt`

**Purpose:** Performs service-version detection against the Metasploitable2 target and saves the scan output in normal text format.

**Evidence Handling:** The scan was executed on Kali Linux and initially saved as `~/nmap-service-versions.txt`. The output was subsequently uploaded to the GitHub repository as `scans/nmap-service-versions.txt`.

**Verification Status:** Executed; scan output collected.

---

### 3. Linux System Inspection

#### 3.1 Identify Current User

`whoami`

**Purpose:** Displays the currently logged-in username.

#### 3.2 Display User and Group Information

`id`

**Purpose:** Displays the current user's UID, GID, and group memberships.

#### 3.3 Review File Permissions

`ls -l`

**Purpose:** Lists files in the current directory, including their permissions, ownership, and other file details.

#### 3.4 Review Directory Permissions

`ls -ld /path/to/directory`

**Purpose:** Displays permissions and ownership of a specified directory.

**Note:** **/path/to/directory** is a placeholder (reference command). Replace it with the actual directory path when performing the assessment.

#### 3.5 Review Running Processes

`ps aux`

**Purpose:** Displays all running processes and their associated users.

**Usage:** Helps inspect process ownership and identify services requiring further review.

#### 3.6 Review Listening Network Services

`ss -tuln`

**Purpose:** Displays listening TCP and UDP sockets.

**Usage:** Helps identify services listening on network ports.

---

### 4. Service and Log Inspection

#### 4.1 Review System Services

`systemctl --type=service --state=running`

**Purpose:** Lists running systemd services.

#### 4.2 Review System Logs

`journalctl`

**Purpose:** Displays logs collected by the systemd journal.

**Note:** These commands depend on systemd and may not be available on older Linux distributions. Their availability will be checked during the practical assessment. 

#### 4.3 Legacy Network Service and Configuration Inspection

**Executed On:** Metasploitable2 (Target VM)

**Purpose:** Inspect legacy network services, their configuration, and the processes responsible for listening on netwrok ports.

**Commands Executed:**

`sudo netstat -tulpn | grep -E ':(512|513|514)\b'` Identifies listening services associated with TCP ports 512, 513, and 514, including process information.

`ps aux | grep '[x]inetd'` Identifies the running `xinetd` process and its process owner.

`cat /etc/hosts.allow` `cat /etc/hosts.deny` Displays TCP Wrappers access control configuration files.

---

### 5. Command Execution Notes
- Commands must be executed only within the authorized local lab.
- Replace generic placeholder paths with actual paths from your environment.
- Some commands may require elevated privileges (`sudo`).

---

### 6. Evidence and Results

Supporting scan outputs and screenshots will be added to the repository as the assessment progresses.

Recommended evidence locations:
- `scans/nmap-service-versions.txt`
- `screenshots/network-configuration.png`(Upload pending)
- `screenshots/service-enumeration.png` (Upload pending)
- `screenshots/permissions-review.png` (Upload pending)

Actual results should be documented alongside the relevant findings in `findings.md`

Security improvements and remediation validation should be documented in `hardening-recommendations.md`

---

### 7. References

- [Nmap Reference Guide] (https://nmap.org/book/man.html)
- [Linux Manual Pages (`man`)] (https://man7.org/linux/man-pages/)
