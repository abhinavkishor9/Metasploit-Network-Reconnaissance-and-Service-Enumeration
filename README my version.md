# Metasploit-Network-Reconnaissance-and-Service-Enumeration
## Overview
### 1. Introduction

Network reconnaissance is an important phase of cybersecurity assessments. It involves identifying reachable systems, discovering exposed network services, and collecting information about the software and configurations running on those systems.

An analyst uses reconnaissance findings to understand the attack surface and identify areas that require further investigation.

Metasploit Framework provides auxiliary modules that can automate several reconnaissance and enumeration activities.

### 2. Network Reconnaissance

Network reconnaissance is the process of gathering information about a target system and its network exposure.

Typical activities include:

- Identifying target IP addresses.
- Checking network reachability.
- Discovering accessible TCP ports.
- Identifying network services.
- Collecting service banners.
- Determining software versions.

Reconnaissance provides an initial understanding of a target before deeper security assessment.

### 3. IP Address and Network Communication

An IP address identifies a device on an IP network.

During this lab, Ubuntu WSL and Metasploitable 2 used different subnets.

| System | IP address |
|---|---|
| Ubuntu WSL | 172.31.162.144 |
| Metasploitable 2 | 192.168.174.129 |

Despite being on different subnets, the systems communicated successfully because a network route was available.

The `ip addr` command displays interface addresses, while `ip route` displays routing information.

The `ping` command can be used to test basic IP connectivity using ICMP.

### 4. TCP Ports

TCP ports are logical endpoints through which network applications communicate.

Common examples include:

| Port | Common service |
|---:|---|
| 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 139 | NetBIOS |
| 445 | SMB |
| 3306 | MySQL |
| 5432 | PostgreSQL |

An open TCP port indicates that a connection was accepted by a service or network endpoint.

A port scan does not, by itself, establish the exact service or prove a vulnerability.

### 5. TCP Port Scanning

TCP port scanning identifies ports that accept connection attempts.

Metasploit provides the following auxiliary module:

`auxiliary/scanner/portscan/tcp`

Important options include:

- `RHOSTS`: Target IP address or hosts.
- `PORTS`: Ports to scan.
- `THREADS`: Number of concurrent host-scanning threads.
- `CONCURRENCY`: Number of concurrent port checks.
- `TIMEOUT`: Connection timeout.
- `DELAY`: Delay between connection attempts.

The scanner can be configured to inspect selected ports rather than an entire port range.

### 6. Service Enumeration

Service enumeration attempts to identify the software and characteristics associated with an accessible port.

For example, a TCP scan may report port 22 as open. SSH enumeration can then identify the server as OpenSSH and disclose its version.

Service enumeration can reveal:

- Software products.
- Version strings.
- Operating-system identification.
- Protocol details.
- Cryptographic algorithms.
- Server banners.

These findings support subsequent vulnerability research and configuration assessment.

### 7. SSH Enumeration

Secure Shell (SSH) is used for secure remote administration.

The Metasploit module:

`auxiliary/scanner/ssh/ssh_version`

can identify the SSH server banner and report supported cryptographic algorithms.

SSH security depends on multiple factors, including:

- Software maintenance.
- Authentication configuration.
- Host-key security.
- Encryption algorithms.
- Message authentication algorithms.
- Key-exchange algorithms.

Deprecated algorithms indicate that the configuration requires review. Their advertisement does not prove that they were negotiated in a connection.

### 8. HTTP Enumeration

Hypertext Transfer Protocol (HTTP) is used for communication between web clients and servers.

The Metasploit module:

`auxiliary/scanner/http/http_version`

can identify server information disclosed in HTTP responses.

Examples of information include:

- Apache or Nginx identification.
- Server version.
- Operating-system information.
- Additional technologies such as PHP.
- HTTP response details.

Server banners can disclose information useful to an attacker, so unnecessary version disclosure should be evaluated.

### 9. WebDAV

Web Distributed Authoring and Versioning (WebDAV) extends HTTP to support remote content authoring and management.

Its security depends on configuration, authentication, authorization, and permitted HTTP methods.

The presence of a DAV identifier does not establish that unrestricted file upload or modification is possible.

### 10. SMB Enumeration

Server Message Block (SMB) is a network protocol commonly used for file and printer sharing.

Ports 139 and 445 are commonly associated with NetBIOS and SMB communication.

The Metasploit module:

`auxiliary/scanner/smb/smb_version`

can help identify SMB server implementation and version information.

SMB assessments may also consider:

- Protocol versions.
- Authentication controls.
- Guest access.
- Share permissions.
- Signing requirements.
- Exposure to untrusted networks.

### 11. Metasploit Auxiliary Modules

Auxiliary modules support activities such as scanning, service enumeration, and information collection.

Unlike exploit modules, reconnaissance scanners do not need to exploit a vulnerability to collect service information.

The modules used in this lab are:

| Module | Purpose |
|---|---|
| `auxiliary/scanner/portscan/tcp` | TCP port discovery |
| `auxiliary/scanner/ssh/ssh_version` | SSH version identification |
| `auxiliary/scanner/http/http_version` | HTTP server identification |
| `auxiliary/scanner/smb/smb_version` | SMB version identification |

### 12. Metasploit Database and Workspaces

Metasploit integrates with PostgreSQL to store assessment information.

The database can organize information about discovered hosts, services, and vulnerabilities.

A workspace provides a logical separation between different assessments.

Useful commands include:

- `db_status`: Check database connectivity.
- `workspace`: Display available workspaces.
- `hosts`: Review recorded hosts.
- `services`: Review recorded services.
- `vulns`: Review recorded vulnerability findings.

Database records depend on module reporting behavior and should be checked rather than assumed.

### 13. Reconnaissance Versus Vulnerability Validation

An open port or an old software version is not automatically proof of a vulnerability.

A reliable security assessment separates:

- Confirmed observations.
- Potential security concerns.
- Validated vulnerabilities.
- Confirmed exploitation.

For example, identifying an older SSH version is a confirmed observation. Determining whether a specific vulnerability applies requires additional evidence.

### 14. Conclusion

Network reconnaissance and service enumeration help analysts build an initial understanding of a target's exposed attack surface.

Metasploit auxiliary scanners automate discovery and provide useful service identification information.

The most important skill is not simply running scanners, but interpreting their results accurately and distinguishing technical evidence from assumptions.

This lab focuses on network reconnaissance and service enumeration using the Metasploit Framework against an intentionally vulnerable Metasploitable 2 virtual machine.

The objective is to identify accessible TCP ports, determine exposed services, collect software version information, and understand how reconnaissance findings contribute to security assessments.

The lab uses Metasploit auxiliary scanner modules to perform controlled discovery and enumeration. No exploitation, payload execution, or post-exploitation activity is performed.

## Lab Environment

| Component | Details |
|---|---|
| Attacker machine | Ubuntu 24.04.5 LTS (WSL) |
| Attacker IP | 172.31.162.144 |
| Target machine | Metasploitable 2 |
| Target platform | VMware virtual machine |
| Target IP | 192.168.174.129 |
| Target interface | eth0 |
| Additional interface | eth1 (no IPv4 address observed) |
| Metasploit version | 6.5.12-dev |
| Database | PostgreSQL |
| Workspace | Metasploit-Labs |

## Lab Objectives

### Primary Objective

To develop practical skills in identifying exposed network services and collecting service-level information from an intentionally vulnerable Linux virtual machine using Metasploit Framework.

### Technical Objectives

1. Establish and verify communication between an Ubuntu WSL attacker environment and a Metasploitable 2 target.
2. Inspect network interfaces and routing information to understand the connectivity between different IP subnets.
3. Use Metasploit auxiliary modules to identify accessible TCP ports.
4. Interpret common port numbers and their associated network services.
5. Perform SSH version enumeration and examine the server's advertised cryptographic algorithms.
6. Perform HTTP version enumeration and identify server technologies disclosed through HTTP responses.
7. Perform SMB version enumeration and examine server identification information.
8. Understand how Metasploit workspaces and PostgreSQL integration support assessment organization.
9. Review discovered hosts and services through Metasploit database commands.

### Analytical Objectives

1. Distinguish between an accessible TCP port and a confirmed running service.
2. Understand the difference between port scanning and service enumeration.
3. Identify legacy software and cryptographic configuration indicators.
4. Recognize the security implications of unnecessary service exposure and information disclosure.
5. Separate confirmed observations from potential security concerns.
6. Avoid treating software version identification as automatic proof of exploitability.

### Investigation and Documentation Objectives

1. Maintain an evidence-based record of commands and scanner results.
2. Record target and attacker network details.
3. Document the modules and options used during reconnaissance.
4. Preserve service banners and relevant identification information.
5. Identify incomplete investigation activities without inventing results.
6. Produce clear investigation notes, troubleshooting records, and an execution timeline.

### Scope Objectives

The lab is limited to controlled reconnaissance and service enumeration.

The exercise does not include vulnerability exploitation, password attacks, payload execution, session creation, or post-exploitation.

### Expected Learning Outcome

By completing this exercise, the learner should be able to conduct a basic network reconnaissance assessment using Metasploit, interpret service identification results, recognize potential security concerns, and produce a structured technical investigation report supported by observed evidence.

## Lab Scenario

### Scenario

A security analyst has been assigned to conduct an initial reconnaissance assessment of an intentionally vulnerable Linux server deployed in an isolated virtual lab environment.

The organization wants to understand which network services are accessible, what software is exposed, and whether the server reveals information that could help identify potential security weaknesses.

The analyst uses Ubuntu WSL as the assessment machine and Metasploitable 2 as the authorized target. Metasploit Framework is already installed, its PostgreSQL database is operational, and a dedicated workspace is available for organizing the investigation.

### Environment

The assessment environment consists of:

- Ubuntu 24.04.5 LTS running under WSL.
- Metasploit Framework 6.5.12.
- Metasploitable 2 running in VMware.
- Ubuntu WSL address: `172.31.162.144`.
- Metasploitable 2 address: `192.168.174.129`.

The target's `eth0` interface has an active IPv4 configuration. A second interface, `eth1`, is also present but does not show an IPv4 address in the collected output.

Although the attacker and target use different IP subnets, connectivity has been successfully verified.

### Investigation Tasks

The analyst begins by reviewing the target's network configuration and confirming that the target is reachable from Ubuntu WSL.

After establishing connectivity, the analyst opens Metasploit, verifies database connectivity, and selects the dedicated assessment workspace.

The first technical activity is TCP port discovery. A selected set of ports is scanned to determine which services accept incoming connections.

The analyst then performs service-specific enumeration to obtain more information about SSH and HTTP. The results include an older OpenSSH identification string, advertised legacy cryptographic algorithms, Apache server information, PHP version disclosure, and WebDAV identification.

The initial scan also identifies SMB-related ports. These are selected for further service enumeration.

### Investigation Expectations

The analyst is expected to:

- Record the accessible ports and associated service hypotheses.
- Identify service banners and software versions.
- Review cryptographic information disclosed by SSH.
- Examine HTTP server information and technology disclosure.
- Investigate SMB implementation and version details.
- Maintain a clear distinction between confirmed observations and potential security concerns.
- Preserve command outputs and document the investigation timeline.

### Scope and Restrictions

The assessment is restricted to the designated Metasploitable 2 virtual machine.

Only reconnaissance and service enumeration are included in this exercise. Exploitation, password attacks, payload execution, remote session establishment, and post-exploitation activities are outside the scope.

No testing against public or third-party systems is permitted.

### Expected Outcome

The analyst should produce an initial attack-surface overview of the target, supported by observed network and service information.

The final report should explain what was discovered, what remains unverified, and which services or configurations warrant additional assessment.

The emphasis is on evidence-based analysis rather than assuming that an exposed service or legacy version automatically represents a confirmed exploitable vulnerability.

## Activities Performed

### 1. Target and Network Verification

- Started the Metasploitable 2 virtual machine in VMware.
- Verified the target network interfaces using `ip addr`.
- Identified eth0 as the interface with IPv4 address 192.168.174.129.
- Verified the target routing table using `ip route`.
- Tested connectivity from Ubuntu WSL using ICMP ping.
- Confirmed successful communication with zero packet loss.

### 2. Metasploit Preparation

- Started the Metasploit console.
- Verified PostgreSQL connectivity using `db_status`.
- Selected the `Metasploit-Labs` workspace.

### 3. TCP Port Discovery

Used the auxiliary module:

`auxiliary/scanner/portscan/tcp`

Scanned 24 selected TCP ports on the target.

All 24 selected ports were reported as TCP OPEN.

### 4. SSH Enumeration

Used:

`auxiliary/scanner/ssh/ssh_version`

Identified the SSH service banner:

`SSH-2.0-OpenSSH_4.7p1 Debian-8ubuntu1`

The scanner also reported legacy cryptographic algorithms and operating-system identification information.

### 5. HTTP Enumeration

Used:

`auxiliary/scanner/http/http_version`

Identified the HTTP server response:

`Apache/2.2.8 (Ubuntu) DAV/2`

The response also disclosed:

`PHP/5.2.4-2ubuntu5.10`

### 6. SMB Enumeration

The initial TCP scan identified ports 139 and 445 as open.

SMB version enumeration is the next activity in this lab and must be documented after the scanner output is collected.

## Confirmed Findings

| Area | Observation |
|---|---|
| Network connectivity | Ubuntu WSL successfully reached the target |
| Target IP | 192.168.174.129 |
| TCP port discovery | 24 selected ports reported open |
| SSH | OpenSSH 4.7p1 advertised |
| SSH cryptography | Multiple deprecated algorithms advertised |
| HTTP | Apache 2.2.8 identified |
| PHP | PHP 5.2.4 identified |
| WebDAV | DAV/2 advertised |
| SMB | TCP ports 139 and 445 reported open |

## Security Observations

- The target exposes multiple network services.
- SSH advertises legacy cryptographic algorithms.
- The HTTP response discloses older Apache and PHP versions.
- WebDAV functionality is advertised.
- SMB and NetBIOS ports are accessible.

These observations identify areas requiring further security assessment. They do not independently establish that a specific vulnerability is exploitable.

## Scope and Limitations

This lab is restricted to reconnaissance and service enumeration.

The following activities were not performed:

- Exploitation of vulnerabilities.
- Password attacks or brute-force attempts.
- Payload generation or execution.
- Establishment of remote sessions.
- Post-exploitation activities.
- Confirmation of unauthorized access.

The TCP scan covered only 24 explicitly selected ports, not the complete TCP port range.

