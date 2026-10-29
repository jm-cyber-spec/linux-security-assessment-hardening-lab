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

#### 1. Kali Linux --- Analyst Machine
Purpose:
- Perform network and service discovery
- Inspect security configurations and permissions.
- Analyze available logs and security-related information.
- Document findings and defensive recommendations.

Network configuration:
- Adapter 1: NAT, for internet access and updates.
- Adapter 2: Host-only, for communication with the isolated lab target.

#### 2. Metasplotable2 -- Target MAchine
Purpose:
- Provide an intentionally vulnerable Linux system for security assessment.
- Demonstrate exposed services and insecure configurations.
- Support learning about security weaknesses and defensive mitigations.

Network configuration:
- Adapter 1: Host-only, connected to the same host only network as Kali Linux.
- Addititonal adapters: Disabled for this isolated assessment.
- Internet access: Not intentionally provided.

Metasplotitable2 must not be exposed to untrusted networks.

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
- Uses one VM for assesment and another as the target.
- Minimizes the risk of exposing an intentionally vulnerable system.

---

### Tools Used

#### Virtualization
- Oracle VirtualBox

#### Kali Linux
- Nmap -- Network and service discovery.
- ss -- Socket and listening-port inspection.
- journalctl -- Systemd journal inspection, where applicable.
- Linux core utilities -- Basic system and permission checks.

#### Metasploitable2
- ifconfig or ip -- Network interface inspection, depending on availability.
- Available system utilities -- Target configuration and security observations.

Tools and commands will be documented according to their actual use in the lab.

### Safety Considerations
- Keep Metasploitable2 connected only to the intended isolated lab network.
- Verify VirtualBox adapter assignments before conducting assessments.
- Do not expose the vulnerable target through bridged networking or public port forwarding.
- Do not publish passwords, private keys, or other sensitive information.

### Project Status

This lab environment is being documented as part of a Linux security assessment and hardening portfolio project. Network configuration, assessment findings, and remediation results will be updated as they are verified.
