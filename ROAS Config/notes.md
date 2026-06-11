### Topology
- R1 (ISR 1331) connected to SW1 (2960-24TT) via trunk link (R1 g0/0/0 ↔ SW1 g0/1)
- VLAN 10 (Sales): 192.168.1.0/26 — PC0 on SW1 f0/10
- VLAN 20 (HR): 192.168.1.64/26 — PC2 on SW1 f0/11
- VLAN 30 (IT): 192.168.1.128/26 — PC1 on SW1 f0/12
- Native VLAN: 1001 (custom, not default VLAN 1 — security best practice)

### What I Configured

**R1 Subinterfaces:**
- G0/0/0.10 — encapsulation dot1Q 10, IP 192.168.1.62/26 (gateway for VLAN 10)
- G0/0/0.20 — encapsulation dot1Q 20, IP 192.168.1.126/26 (gateway for VLAN 20)
- G0/0/0.30 — encapsulation dot1Q 30, IP 192.168.1.190/26 (gateway for VLAN 30)
- Physical interface G0/0/0 — no IP address, no shutdown

**SW1 Trunk Port:**
- G0/1 configured as trunk toward R1
- Allowed VLANs: 10, 20, 30
- Native VLAN set to 1001

**SW1 Access Ports:**
- F0/10 — access VLAN 10 (PC0)
- F0/11 — access VLAN 20 (PC2)
- F0/12 — access VLAN 30 (PC1)

### Verification
- show ip route: C routes present for all three VLAN subnets 
- show ip interface brief: all subinterfaces up/up 
- Ping from VLAN 10 (PC0) to VLAN 20 (PC2) - Success!!
- Ping from VLAN 10 (PC0) to VLAN 30 (PC1) - Success!!
- Ping from VLAN 20 (PC2) to VLAN 30 (PC1) - Success!!

### What I Learned
- Physical interface must be up with no IP address assigned —
  IP addresses go on subinterfaces only
- encapsulation dot1Q number must exactly match the VLAN ID
- Subinterface number doesn't have to match VLAN ID but
  it's best practice to keep them the same (e.g. .10 = VLAN 10)
- Native VLAN should be changed from default VLAN 1 to an
  unused VLAN (1001) to prevent VLAN hopping attacks
- Trunk port on the switch must allow the VLANs being routed —
  if a VLAN isn't in the allowed list, traffic won't pass
- Gateway IP for each VLAN is assigned to the subinterface,
  not the physical interface

### Subnetting Used
- /26 = 64 addresses, 62 usable hosts per VLAN
- VLAN 10: 192.168.1.0/26 — usable: .1–.62, broadcast: .63
- VLAN 20: 192.168.1.64/26 — usable: .65–.126, broadcast: .127
- VLAN 30: 192.168.1.128/26 — usable: .129–.190, broadcast: .191
- Gateway IPs assigned at the last usable address of each subnet
