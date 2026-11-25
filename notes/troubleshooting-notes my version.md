# Troubleshooting Notes 
## 1. Overview

This document records the configuration checks, connectivity verification, and issues encountered while preparing and conducting the Metasploit reconnaissance lab.

The objective was to establish reliable communication between Ubuntu WSL and Metasploitable 2 without making unnecessary changes to the existing environment.

## 2. Metasploitable 2 Network Interface Verification

### Observation

The target had two network interfaces:

- eth0
- eth1

Command:

```bash
ip addr
```

Observed configuration:

```text
eth0:
inet 192.168.174.129/24

eth1:
No IPv4 address displayed
```

Both interfaces were reported as UP.

### Assessment

The target's active IPv4 address was assigned to eth0.

The presence of eth1 does not mean that it has a working IPv4 configuration.

No additional network configuration was required for the reconnaissance activities completed so far.

## 3. Routing Table Verification

Command:

```bash
ip route
```

Observed output:

```text
192.168.174.0/24 dev eth0 proto kernel scope link src 192.168.174.129
```

### Assessment

The target has a directly connected route for the 192.168.174.0/24 network.

No default route was displayed.

This did not prevent communication with the attacker machine during the lab.

## 4. WSL-to-Target Connectivity

### Initial Network Configuration

Ubuntu WSL:

```text
172.31.162.144/20
```

Metasploitable 2:

```text
192.168.174.129/24
```

The machines were assigned addresses from different subnets.

### Connectivity Test

Command:

```bash
ping -c 4 192.168.174.129
```

Observed result:

```text
4 packets transmitted, 4 received, 0% packet loss
```

Average latency:

```text
1.117 ms
```

### Resolution

Communication was already working between the two environments.

No manual route, adapter modification, or firewall change was necessary.

### Lesson

Different IP subnets do not automatically imply that two machines cannot communicate. Routing between the networks can provide connectivity.

Always test actual reachability before changing network settings.

## 5. Metasploit Database Verification

### Command

```text
db_status
```

### Expected Result

```text
[*] Connected to msf. Connection type: postgresql.
```

### Assessment

The Metasploit database was successfully connected.

The existing Metasploit-Labs workspace was selected for the investigation.

No database reinitialization was necessary.

## 6. TCP Port Scanner Configuration

### Module

```text
auxiliary/scanner/portscan/tcp
```

### Configuration Used

```text
set RHOSTS 192.168.174.129
set PORTS 21,22,23,25,53,80,111,139,445,512,513,514,1099,1524,2049,2121,3306,3632,5432,5900,6000,6667,8009,8180
set THREADS 1
set CONCURRENCY 10
set TIMEOUT 1000
```

### Observation

The scanner successfully completed against one target.

All 24 selected ports were reported as TCP OPEN.

### Lesson

The RHOSTS option must contain the intended target address.

The PORTS option controls which ports are checked. A scan of a selected list should not be described as a complete TCP port scan.

## 7. SSH Version Scanner

### Module

```text
auxiliary/scanner/ssh/ssh_version
```

### Observation

The scanner successfully identified:

```text
OpenSSH_4.7p1 Debian-8ubuntu1
```

It also returned cryptographic algorithm information.

### Assessment

The scanner worked successfully.

The presence of deprecated algorithms is a configuration observation, not proof that a connection negotiated one of those algorithms.

## 8. HTTP Version Scanner

### Module

```text
auxiliary/scanner/http/http_version
```

### Observation

The scanner returned:

```text
Apache/2.2.8 (Ubuntu) DAV/2
Powered by PHP/5.2.4-2ubuntu5.10
```

### Assessment

HTTP version enumeration completed successfully.

The response demonstrated that server banners can disclose software and technology information.

## 9. SMB Enumeration

### Module Selected

```text
auxiliary/scanner/smb/smb_version
```

The initial TCP scan confirmed that ports 139 and 445 were open.

SMB version identification remains pending.

No troubleshooting failure has been established for this stage.

