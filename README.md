# Lab 02 – Network Reconnaissance and Service Enumeration Using Metasploit

## Overview

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

- Verify network connectivity between Ubuntu WSL and Metasploitable 2.
- Understand TCP port discovery and service enumeration.
- Identify accessible TCP ports using Metasploit.
- Enumerate SSH, HTTP, and SMB services.
- Identify software versions and service banners.
- Examine SSH cryptographic algorithms advertised by the server.
- Understand the role of Metasploit workspaces and database integration.
- Distinguish confirmed observations from potential security concerns.

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

## Key Learning

Network reconnaissance is not simply about identifying open ports. It involves understanding exposed services, interpreting service banners, identifying software versions, evaluating configuration indicators, and collecting evidence for further investigation.

The lab reinforces an evidence-based approach: an exposed service or outdated version is a security observation, not automatic proof of compromise or exploitability.

## Safety

All activities were conducted against an intentionally vulnerable virtual machine in a controlled lab environment. Scanning and testing must remain restricted to systems for which authorization has been obtained.
