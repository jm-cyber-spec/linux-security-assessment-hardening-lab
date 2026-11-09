# Lab Setup Documentation

## Objective

To build a controlled virtual environment for learning Linux security assessment, network service discovery, authentication and permission reviews, log analysis and defensive hardening.

This lab is intended for educational purposes and authorized testing only.

---

## Environment Overview

### Host System
- Virtualization platform: Oracle VirtualBox
- Host operating system: Personal computer

### Virtual Machines

#### 1. Kali Linux — Analyst Machine
Purpose:
- Perform network and service discovery
- Inspect security configurations and permissions.
- Analyze available logs and security-related information.
- Document findings and defensive recommendations.

Network configuration:
- Adapter 1: NAT, for internet access and updates.
- Adapter 2: Host-only, for communication with the isolated lab target.

#### 2. Metasploitable2 — Target Machine
Purpose:
- Provide an intentionally vulnerable Linux system for security assessment.
- Demonstrate exposed services and insecure configurations.
- Support learning about security weaknesses and defensive mitigations.

Network configuration:
- Adapter 1: Host-only, connected to the same host only network as Kali Linux.
- Additional adapters: Disabled for this isolated assessment.
- Internet access: Not intentionally provided.

Metasploitable2 must not be exposed to untrusted networks.

---

## Network Design

The lab uses a VirtualBox **Host-only network** to ensure:
- The vulnerable machine is not exposed to the internet.
- All testing remains inside the local system.
- Safe experimentation without external risk.

This design allows Kali Linux to access the lab target through the host-only interface while retaining a separate NAT interface for internet connectivity, if configured. 

### Network Addressing
- Kali Linux Host-only IP: 192.168.56.101
- Metasploitable2 IP: 192.168.56.104

Kali Linux uses a separate NAT adapter for internet access and updates. Metasploitable2 is configured with a Host-only adapter to keep the vulnerable target isolated from direct internet access.

---

## Why this Architecture Matters

This architecture demonstrates several important security principles:
- Separates the lab target from the normal network environment.
- Restricts assessment activities to an authorized environment.
- Uses one VM for assessment and another as the target.
- Minimizes the risk of exposing an intentionally vulnerable system.

---

### Tools and Utilities

#### Virtualization
- Oracle VirtualBox — Virtual machine management and network isolation.

#### Kali Linux (Analyst VM)
- Nmap — Network discovery and service-version scanning.
- Linux command-line utilities — Network connectivity testing and inspection.

#### Metasploitable2 (Target VM)
- Linux command-line utilities — User, group, and file-permission inspection.
- `dpkg` and `dpkg-query` — Installed package verification.
- `ps` and `netstat` — Process and network-listener inspection.
- Configuration file inspection — Authentication, legacy services, and application configuration reviews.

Commands such as `ss`, `systemctl`, and `journalctl` are documented as reference commands where execution was not confirmed.

See [Commands Used](commands-used.md) for the commands documented during this assessment.

### Safety Considerations
- Keep Metasploitable2 connected only to the intended isolated lab network.
- Verify VirtualBox adapter assignments before conducting assessments.
- Do not expose the vulnerable target through bridged networking or public port forwarding.
- Do not publish passwords, private keys, or other sensitive information.

### Project Status

The virtual lab environment has been configured, and network discovery, service inspection, authentication review, privilege inspection, and selected software-version assessments have been documented.

The initial software package and security-advisory assessments have been documented, including Apache, vsftpd, ProFTPD, MySQL, PostgreSQL, and OpenSSH. Some vulnerability applicability questions, authentication and privilege-related observations, and supporting evidence collection remain unresolved. These limitations are recorded in the security findings.

No exploitation or remediation has been performed as part of the documented assessment.

See [Security Findings](findings.md) for current observations and verification limitations.
