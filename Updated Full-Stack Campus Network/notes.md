# Updated Full-Stack Campus Network — Management Plane: SSH + Syslog + SNMP + FTP/TFTP

## Overview
Add-on to Lab Full-Stack Campus Network. Adds a dedicated
Management Server on VLAN 99 and configures the full management
plane across R-EDGE, DSW-CORE, ASW1, and ASW2. Covers SSH
hardening, centralized Syslog, SNMPv2c community strings, and
IOS backup via FTP and TFTP.

---

## Topology Addition
Management Server added to VLAN 99 via an ASW1 access port.
All management traffic (SSH, Syslog, SNMP, FTP/TFTP) flows
through the existing VLAN 99 management SVI on DSW-CORE.

```
Existing Lab Full-Stack Campus Network topology unchanged +

DSW-CORE (SVI VLAN 99: 192.168.99.1/24)
       |
     ASW1 (Fa0/10 — access port, VLAN 99)
       |
  Mgmt-Server (192.168.99.50/24, GW: 192.168.99.1)
```

---

## Management Server
| Parameter | Value |
|---|---|
| IP Address | 192.168.99.50 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.99.1 |
| Connected To | ASW1 Fa0/10 — access, VLAN 99 |
| Services | Syslog, SNMP NMS, TFTP server, FTP server |

---

## IP Addressing (VLAN 99 addition noted)
| Device | Interface | IP Address | Notes |
|---|---|---|---|
| R-WAN | Gi0/0/0 | 203.0.113.2/30 | Simulated WAN |
| R-EDGE | Gi0/0/0 (→ R-WAN) | 203.0.113.1/30 | |
| R-EDGE | Gi0/0/1 (→ DSW-CORE) | 10.50.1.1/30 | |
| DSW-CORE | Gi1/0/24 routed port | 10.50.1.2/30 | Uplink to R-EDGE |
| DSW-CORE | SVI VLAN 10 | 192.168.10.1/24 | ENGINEERING GW |
| DSW-CORE | SVI VLAN 20 | 192.168.20.1/24 | HR GW |
| DSW-CORE | SVI VLAN 30 | 192.168.30.1/24 | SALES GW |
| DSW-CORE | SVI VLAN 99 | 192.168.99.1/24 | MANAGEMENT GW |
| Mgmt-Server | NIC | 192.168.99.50/24 | GW: 192.168.99.1 |

---

## VLAN Table
| VLAN | Name | Purpose |
|---|---|---|
| 10 | ENGINEERING | Data |
| 20 | HR | Data |
| 30 | SALES | Data |
| 99 | MANAGEMENT | Native + Management |

---

## What I Configured

### R-EDGE

**SSH:**
```
hostname R-EDGE
ip domain-name corp.local
crypto key generate rsa modulus 1024
username netadmin secret Cisco123!
ip ssh version 2
line vty 0 4
 login local
 transport input ssh
 logging synchronous
```

**Syslog:**
```
logging host 192.168.99.50
logging trap debugging
service timestamps log datetime msec
logging buffered 16384 debugging
```

**SNMP:**
```
snmp-server community NetReadOnly RO
snmp-server community NetReadWrite RW
```

**FTP credentials:**
```
ip ftp username Cisco
ip ftp password ccna
```

**IOS Backup:**
```
copy flash: tftp:
copy flash: ftp:
```
(Router prompts interactively for server IP and filename)

---

### DSW-CORE

**SSH:**
```
hostname DSW-CORE
ip domain-name corp.local
crypto key generate rsa modulus 1024
username netadmin secret Cisco123!
ip ssh version 2
line vty 0 4
 login local
 transport input ssh
 logging synchronous
```

**Syslog:**
```
logging host 192.168.99.50
logging trap debugging
service timestamps log datetime msec
logging buffered 16384 debugging
```

**SNMP:**
```
snmp-server community NetReadOnly RO
snmp-server community NetReadWrite RW
```

---

### ASW1

**Management Server access port:**
```
interface fa0/10
 switchport mode access
 switchport access vlan 99
```

**Syslog:**
```
logging host 192.168.99.50
logging trap debugging
service timestamps log datetime msec
```

---

### ASW2

**Syslog:**
```
logging host 192.168.99.50
logging trap debugging
service timestamps log datetime msec
```

---

## What Changed From Lab Full-Stack Campus Network
- Added Mgmt-Server on VLAN 99 via ASW1 access port
- ASW1 server port to VLAN 99
- Added SSH hardening to R-EDGE and DSW-CORE
  (SSHv2, RSA 1024, login local, transport input ssh only)
- Added centralized Syslog to R-EDGE, DSW-CORE, ASW1, ASW2
  all pointing to 192.168.99.50
- Added SNMPv2c RO and RW community strings to
  R-EDGE and DSW-CORE
- Added FTP credentials on R-EDGE
- Performed IOS backup from R-EDGE to Mgmt-Server
  via both TFTP and FTP

---

## Verification Steps
- [✅] show ip ssh on R-EDGE — SSH v2 enabled, modulus 1024
- [✅] show ip ssh on DSW-CORE — SSH v2 enabled, modulus 1024
- [✅] Telnet to R-EDGE — rejected (transport input ssh blocks it)
- [✅] Telnet to DSW-CORE — rejected
- [✅] SSH from Mgmt-Server to R-EDGE — accepted, login local works
- [✅] show logging on R-EDGE — buffered messages with timestamps
- [✅] show logging on DSW-CORE — buffered messages with timestamps
- [✅] Trigger interface event — Syslog messages appear on
      Mgmt-Server Syslog tab
- [✅] show snmp community on R-EDGE — NetReadOnly RO,
      NetReadWrite RW present
- [✅] show snmp community on DSW-CORE — same as above
- [✅] show flash: on R-EDGE — IOS filename confirmed before backup
- [✅] copy flash: tftp: — IOS image transferred to
      Mgmt-Server successfully
- [✅] copy flash: ftp: — IOS image transferred to
      Mgmt-Server via FTP successfully
- [✅] Mgmt-Server ping 192.168.99.1 — success (VLAN 99 SVI)
- [✅] Mgmt-Server ping 10.50.1.1 — success (R-EDGE, through OSPF)
- [✅] Mgmt-Server ping 203.0.113.2 — success (R-WAN, end-to-end)

---

## What I Learned
- SSH prerequisites must be configured in strict order:
  hostname → ip domain-name → crypto key generate rsa →
  username secret → ip ssh version 2 → VTY line config.
  Missing or reordering any step causes connection failure.
- transport input ssh on VTY lines completely blocks Telnet —
  no fallback is available unless explicitly re-added.
- logging synchronous prevents Syslog messages from
  interrupting CLI input mid-command on VTY lines —
  it reprints the current command after each log message.
- SNMPv2c RO community strings only permit read operations
  (Get/GetNext/GetBulk). SetRequests using an RO string
  are silently dropped by the agent — no error is returned.
- ip ftp username and ip ftp password configure credentials
  the router uses when acting as an FTP client copying files
  to/from a server. These are not FTP server credentials.
- copy flash: tftp: and copy flash: ftp: both prompt
  interactively for the server IP and filename — the full
  path does not need to be specified in the command itself.
- Management Server access port must be explicitly assigned
  to VLAN 99 on ASW1. In PT, ports retain their previous
  VLAN assignment from prior lab builds — forgetting to
  reassign the port places the server in the wrong broadcast
  domain, causing ARP to time out and all management traffic
  to drop before it leaves the host. Symptom: ICMP drops
  at the server, ARP request never receives a reply.
- Logging levels set the threshold for which messages are
  forwarded — configuring level N sends levels 0 through N.
  Lower number = higher severity.

---

## Packet Tracer Limitations
- logging trap only accepts the debugging keyword in PT.
  Real IOS accepts all severity level keywords. In production,
  appropriate levels per device tier would be:
  - Core/Distribution: informational (level 6)
  - Access layer: warnings (level 4)
  - Debug-level logging (level 7) is never used in production
    due to excessive CPU and storage overhead.

- snmp-server host and snmp-server enable traps are not
  supported in Packet Tracer. Only community string
  configuration is functional:
    snmp-server community NetReadOnly RO
    snmp-server community NetReadWrite RW
  In real IOS, you would also define a trap destination
  (snmp-server host) and enable specific trap categories
  (snmp-server enable traps). SNMP trap and Inform
  functionality should be verified in GNS3 or on
  real hardware.

- FTP file transfer (copy flash: ftp:) may behave
  inconsistently in PT. TFTP is the more reliable option
  for IOS file operations in a simulated environment.
