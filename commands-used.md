## Commands Used — Linux Security Assessment and Hardening Lab

### 1. Purpose

This document records the commands used during the Linux Security Assessment and Hardening Lab.

The commands support network discovery, service enumeration, Linux permission inspection, process inspection, and security configuration review.

This document distinguishes commands executed during the assessment from commands included as reference material.

---

### 2. Network Discovery and Scanning

**2.1 Identify Network Interfaces**

`ip a`

**Purpose:** Displays network interfaces and their assigned IP addresses.

**Usage:** Helps identify the Kali Linux Host-only network interface and verify its network configuration.

**2.2 Check Network Connectivity**

`ping -c 4 192.168.56.104`

**Purpose:** Sends four ICMP echo requests to the Metasploitable2 target.

**Usage:** Checks basic network connectivity between Kali Linux and the target machine. 

**2.3 Scan Target Ports Using Nmap**

`nmap 192.168.56.104`

**Purpose:** Performs a basic TCP port scan against the target.

**Usage:** Identifies accessible ports on the target machine.

**2.4 Detect Service Versions**

`nmap -sV 192.168.56.104`

**Purpose:** Attempts to identify services and their versions on discovered ports.

**Usage:** Helps document exposed services and investigate potentially outdated software.

**2.5 Save Nmap Results**

`nmap -sV 192.168.56.104 -oN scans/nmap-results.txt`

**Purpose:** Saves the scan output in normal text format.

**Usage:** Provides supporting evidence for documenting network service exposure and detected software versions.

---

### 3. Linux System Inspection

**3.1 Identify Current User**

`whoami`

**Purpose:** Displays the currently logged-in username.

**3.2 Display User and Group Information**

`id`

**Purpose:** Displays the current user's UID, GID, and group memberships.

**3.3 Review File Permissions**

`ls -l`

**Purpose:** Lists file permissions, ownership, and details.

**3.4 Review Directory Permissions**

`ls -ld /path/to/directory`

**Purpose:** Displays permissions and ownership of a specified directory.

**Note:** **/path/to/directory** is a placeholder. Replace it with the actual directory path when performing the assessment.

**3.5 Review Running Processes**

`ps aux`

**Purpose:** Displays all running processes and their associated users.

**Usage:** Helps inspect process ownership and identify services requiring further review.

**3.6 Review Listening Network Services**

`ss -tuln`

**Purpose:** Displays listening TCP and UDP sockets.

**Usage:** Helps identify services listening on network ports.

---

### 4. Service and Log Inspection

**4.1 Review System Services**

`systemctl --type=service --state=running`

**Purpose:** Lists running systemd services.

**4.2 Review System Logs**

`journalctl`

**Purpose:** Displays logs collected by the systemd journal.

**Note:** These commands depend on systemd and may not be available on older Linux distributions. Their availability will be checked during the practical assessment. 

---

### 5. Command Execution Notes
- Commands must be executed only within the authorized local lab.
- Replace generic placeholder paths with actual paths from your environment.
- Some commands may require elevated privileges (`sudo`).

---

### 6. Evidence and Results

Supporting scan outputs and screenshots will be added to the repository as the assessment progresses.

Recommended evidence locations:
- `scans/nmap-results.txt`
- `evidence/network-configuration.png`
- `evidence/service-enumeration.png`
- `evidence/permissions-review.png`

Actual results should be documented alongside the relevant findings in `findings.md`

Security improvements and remediation validation should be documented in `hardening-recommendations.md`

---

### 7. References

- [Nmap Reference Guide] (https://nmap.org/book/man.html)
- [Linux Manual Pages (`man`)] (https://man7.org/linux/man-pages/)
