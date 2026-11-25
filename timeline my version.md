# Timeline 
### 1. Metasploitable 2 Preparation

- Started the Metasploitable 2 virtual machine in VMware.
- Verified the target's network configuration.
- Identified eth0 with IPv4 address 192.168.174.129/24.
- Identified eth1 as UP without an IPv4 address in the collected output.
- Reviewed the target routing table.

### 2. Network Connectivity Verification

- Reviewed Ubuntu WSL network configuration.
- Identified Ubuntu WSL address 172.31.162.144/20.
- Compared attacker and target network subnets.
- Executed an ICMP connectivity test.
- Confirmed four successful replies with zero packet loss.
- Determined that no manual network changes were necessary.

### 3. Metasploit Preparation

- Started Metasploit Framework.
- Verified PostgreSQL database connectivity.
- Selected the Metasploit-Labs workspace.

### 4. TCP Port Discovery

- Loaded `auxiliary/scanner/portscan/tcp`.
- Configured the target address.
- Selected 24 TCP ports for scanning.
- Executed the scan.
- Recorded all 24 selected ports as TCP OPEN.

### 5. SSH Enumeration

- Loaded `auxiliary/scanner/ssh/ssh_version`.
- Configured TCP port 22.
- Identified OpenSSH 4.7p1.
- Recorded the SSH server banner.
- Examined advertised encryption, HMAC, and key-exchange algorithms.
- Recorded deprecated algorithms as configuration observations.

### 6. HTTP Enumeration

- Loaded `auxiliary/scanner/http/http_version`.
- Configured TCP port 80.
- Identified Apache 2.2.8 on Ubuntu.
- Recorded WebDAV DAV/2 information.
- Identified PHP 5.2.4-2ubuntu5.10 from the server response.

### 7. SMB Enumeration

- Confirmed TCP ports 139 and 445 were open from the initial scan.
- Selected the Metasploit SMB version scanner.
- SMB implementation and version identification remain pending.

