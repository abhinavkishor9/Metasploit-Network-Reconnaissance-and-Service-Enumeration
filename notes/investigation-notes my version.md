# Investigation Notes
## 1. Investigation Summary

This investigation used Metasploit auxiliary scanner modules to identify exposed network services on a Metasploitable 2 virtual machine.

The assessment focused on TCP port discovery and service version identification.

The target was reachable from Ubuntu WSL, and the selected TCP scan identified 24 accessible ports. SSH and HTTP enumeration returned software and configuration information.

No exploitation or payload execution was performed.

## 2. Environment Details

| Parameter | Value |
|---|---|
| Attacker platform | Ubuntu 24.04.5 LTS (WSL) |
| Attacker hostname | DESKTOP-9MMM37V |
| Attacker IP | 172.31.162.144 |
| Target platform | Metasploitable 2 |
| Target hostname | metasploitable |
| Target IP | 192.168.174.129 |
| Target interface | eth0 |
| Additional interface | eth1 |
| Metasploit version | 6.5.12-dev |
| Database | PostgreSQL |
| Workspace | Metasploit-Labs |

## 3. Target Network Investigation

### Interface Verification

Command:

```bash
ip addr
```

Observed target interfaces:

- `lo`: Loopback interface.
- `eth0`: UP, with IPv4 address `192.168.174.129/24`.
- `eth1`: UP, without an IPv4 address in the collected output.

The active IPv4 configuration was associated with eth0.

### Routing Table

Command:

```bash
ip route
```

Observed output:

```text
192.168.174.0/24 dev eth0 proto kernel scope link src 192.168.174.129
```

This indicates that the target has a directly connected route for the `192.168.174.0/24` network through eth0.

No default route was displayed in the supplied output.

### Connectivity Verification

From Ubuntu WSL:

```bash
ping -c 4 192.168.174.129
```

Observed result:

```text
4 packets transmitted, 4 received, 0% packet loss
```

Average response time:

```text
1.117 ms
```

The successful ICMP responses confirmed connectivity between Ubuntu WSL and Metasploitable 2.

## 4. Metasploit Preparation

Started Metasploit:

```bash
msfconsole
```

Verified database connectivity:

```text
db_status
```

Observed:

```text
[*] Connected to msf. Connection type: postgresql.
```

Selected the investigation workspace:

```text
workspace Metasploit-Labs
```

This workspace was used to organize the assessment.

## 5. TCP Port Discovery

### Module

```text
auxiliary/scanner/portscan/tcp
```

### Configuration

```text
set RHOSTS 192.168.174.129
set PORTS 21,22,23,25,53,80,111,139,445,512,513,514,1099,1524,2049,2121,3306,3632,5432,5900,6000,6667,8009,8180
set THREADS 1
set CONCURRENCY 10
set TIMEOUT 1000
```

### Result

All 24 selected TCP ports were reported as open.

| Port | Common service association |
|---:|---|
| 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 111 | RPCbind |
| 139 | NetBIOS |
| 445 | SMB |
| 512 | rexec |
| 513 | rlogin |
| 514 | rsh |
| 1099 | Java RMI |
| 1524 | Potential shell service |
| 2049 | NFS |
| 2121 | Alternate FTP |
| 3306 | MySQL |
| 3632 | distccd |
| 5432 | PostgreSQL |
| 5900 | VNC |
| 6000 | X11 |
| 6667 | IRC |
| 8009 | AJP |
| 8180 | Alternate HTTP |

### Interpretation

The target exposes multiple network services, including remote administration, file-sharing, database, and web-related services.

The scan confirms that TCP connections were accepted on the selected ports. Port associations are preliminary and require service-specific enumeration.

The scan did not cover every possible TCP port.

## 6. SSH Version Enumeration

### Module

```text
auxiliary/scanner/ssh/ssh_version
```

### Configuration

```text
set RHOSTS 192.168.174.129
set RPORT 22
```

### Observed SSH Banner

```text
SSH-2.0-OpenSSH_4.7p1 Debian-8ubuntu1
```

### Identified Information

| Attribute | Result |
|---|---|
| Protocol | SSH-2.0 |
| Product | OpenSSH |
| Version | 4.7p1 |
| OS identification | Ubuntu 8.04 |
| Vendor identification | OpenBSD for OpenSSH |
| Target port | TCP 22 |

### Cryptographic Observations

The scanner reported several deprecated algorithms.

Examples:

```text
3des-cbc
blowfish-cbc
arcfour
arcfour128
hmac-md5
hmac-md5-96
diffie-hellman-group1-sha1
```

It also reported stronger alternatives, including:

```text
aes128-ctr
aes192-ctr
aes256-ctr
diffie-hellman-group-exchange-sha256
```

### Interpretation

The SSH server advertises legacy cryptographic algorithms alongside stronger alternatives.

This indicates that the SSH cryptographic configuration warrants further review.

The scanner output does not establish which algorithms are negotiated in an actual connection.

The SSH host-key fingerprint was also returned by the scanner and can serve as an identification artifact.

## 7. HTTP Version Enumeration

### Module

```text
auxiliary/scanner/http/http_version
```

### Configuration

```text
set RHOSTS 192.168.174.129
set RPORT 80
```

### Observed Server Response

```text
Apache/2.2.8 (Ubuntu) DAV/2
Powered by PHP/5.2.4-2ubuntu5.10
```

### Identified Components

| Component | Identified version or information |
|---|---|
| Web server | Apache |
| Apache version | 2.2.8 |
| Operating system identification | Ubuntu |
| WebDAV | DAV/2 |
| PHP | 5.2.4-2ubuntu5.10 |

### Interpretation

The HTTP server discloses its Apache and PHP versions.

WebDAV is also advertised, which warrants further review of enabled methods, permissions, and access controls.

The banner alone does not confirm that WebDAV write operations are permitted or that a particular vulnerability is present.

## 8. SMB Enumeration

The initial TCP scan identified:

- TCP 139 as open.
- TCP 445 as open.

These ports are commonly associated with NetBIOS and SMB.

The following module was selected for further enumeration:

```text
auxiliary/scanner/smb/smb_version
```

SMB version identification is pending collection and analysis of the actual scanner output.

No Samba version or operating-system result should be recorded until the scanner confirms it.

## 9. Consolidated Findings

| Investigation area | Finding | Evidence status |
|---|---|---|
| Network connectivity | Target reachable from WSL | Confirmed |
| Target interface | eth0 has 192.168.174.129/24 | Confirmed |
| Additional interface | eth1 has no IPv4 address in output | Confirmed observation |
| TCP scan | 24 selected ports open | Confirmed |
| SSH | OpenSSH 4.7p1 advertised | Confirmed |
| SSH cryptography | Deprecated algorithms advertised | Confirmed |
| HTTP | Apache 2.2.8 identified | Confirmed |
| PHP | PHP 5.2.4 disclosed | Confirmed |
| WebDAV | DAV/2 advertised | Confirmed |
| SMB | Ports 139 and 445 open | Confirmed |
| SMB implementation/version | Not yet identified | Pending |

## 10. Security Assessment

### Confirmed Observations

- Multiple network services are accessible.
- SSH advertises an older OpenSSH version.
- SSH advertises deprecated cryptographic algorithms.
- Apache and PHP version information is disclosed.
- WebDAV functionality is advertised.
- SMB-related ports are accessible.

### Potential Security Concerns

- Legacy software may contain known security weaknesses.
- Deprecated cryptographic algorithms may weaken SSH security.
- Exposed remote-access services increase the attack surface.
- WebDAV configuration may require additional access-control validation.
- SMB exposure requires further protocol and configuration assessment.

### Not Established

- Exploitation of any vulnerability.
- Unauthorized access.
- Successful authentication.
- Remote code execution.
- System compromise.
- Actual vulnerability exploitability.

