# CCNA Home Labs — Karl

Practice labs built in Cisco Packet Tracer while studying
for the CCNA 200-301 certification using Jeremy's IT Lab
(YouTube) and NetworkChuck's Summer of CCNA 2026.

## Labs Completed

| Project | Topic | Devices Used | Status |
|---------|-------|-------------|--------|
| Inter-VLAN Routing via ROAS | Router on a Stick using subinterfaces for inter-VLAN routing | R1 (ISR 1331), SW1 (2960-24TT) | ✅ |
| Inter-VLAN Routing via Layer 3 Switch | SVI-based inter-VLAN routing using a multilayer switch | SW1, SW2 (2960-24TT), SW3 (3560-24PS) | ✅ |
| OSPF Multi-Router | OSPF Area 0 across three routers with loopback Router IDs, point-to-point links, and passive interfaces | R1, R2, R3 (ISR 4331), SW1, SW2 (2960-24TT) | ✅ |
| EtherChannel + Inter-VLAN Routing | LACP EtherChannel between access switches integrated into existing L3 Switch inter-VLAN routing topology | SW1, SW2 (2960-24TT), SW3 (3560-24PS) | ✅ |
| STP/RSTP (Rapid PVST+) | Per-VLAN Rapid Spanning Tree across five switches with Root Bridge election, load balancing, PortFast, BPDU Guard, and Root Guard | SW1-SW5 (2960-24TT) | ✅ |
| Full-Stack Campus Network — EtherChannel + OSPF + DHCP Relay + ACL Integration | Multi-technology integration lab: LACP EtherChannel, OSPF with default route redistribution, centralized DHCP with ip helper-address relay, Extended Named ACL for inter-VLAN traffic restriction, Rapid PVST+ root bridge, NTP hierarchy, L2 switch management plane | R-WAN, R-EDGE (ISR 4331), DSW-CORE (3650), ASW1, ASW2 (2960-24TT) | ✅ |

## Tools Used
- Cisco Packet Tracer
- Jeremy's IT Lab (YouTube)

## Certifications In Progress
- CCNA 200-301 (Target: Fall 2026)

## Key Concepts Practiced
- VLAN creation and access port assignment
- Trunk port configuration (802.1Q)
- Native VLAN security best practice (non-default VLAN)
- Router on a Stick (ROAS) via subinterfaces
- Layer 3 inter-VLAN routing via SVIs
- ip routing command on multilayer switches
- SVI up/up conditions
- EtherChannel (LACP Active/Active) — bundling,
  trunk configuration, link failure behavior
- EtherChannel (PAgP Desirable/Desirable) — Cisco
  proprietary negotiation protocol
- L3 switch trunk requirement — switchport trunk
  encapsulation dot1q before switchport mode trunk
- STP interaction with EtherChannel — logical
  single link, no ports blocked
- Rapid PVST+ — per-VLAN STP instances
- Root Bridge election and priority manipulation
- Per-VLAN load balancing via PVST+
- STP port roles — Root, Designated, Alternate/Blocked
- PortFast and BPDU Guard on access ports
- Root Guard on root bridge downlinks
- Secondary Root Bridge design and failover
- OSPF single-area configuration (Area 0)
- OSPF Router ID — manual configuration and
  loopback interfaces
- OSPF passive-interface on LAN-facing ports
- OSPF point-to-point network type on router links
- Two methods of OSPF network advertisement:
  network command vs ip ospf [process] area [area]
- /30 subnets for point-to-point router connections
- OSPF neighbor adjacency and Full state verification
- OSPF process IDs — locally significant
- OSPF default route redistribution —
  default-information originate
- L3 switch routed port (no switchport) for
  uplink to router
- DHCP server configuration — pools, exclusions,
  default-router, dns-server, lease
- DHCP relay — ip helper-address on SVI to forward
  client Discover to centralized DHCP server
- Extended Named ACL — inter-VLAN traffic restriction
  applied inbound on SVI
- ACL placement — inbound on source SVI to block
  traffic as close to source as possible
- NTP hierarchy — ntp master, ntp server,
  stratum levels, synchronization verification
- L2 switch management plane — VLAN SVI +
  ip default-gateway required for off-subnet
  management reachability (NTP, SSH, SNMP)
- FHRPs (HSRP, VRRP, GLBP) — conceptual understanding

## Repository Structure
Each lab folder contains:
- topology.png — Packet Tracer network diagram
- config-[device].txt — full running configuration per device
- notes.md — topology summary, what was configured,
  verification steps, and lessons learned
