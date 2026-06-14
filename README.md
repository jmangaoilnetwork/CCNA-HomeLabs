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

## Tools Used
- Cisco Packet Tracer
- Jeremy's IT Lab (YouTube)
- NetworkChuck Summer of CCNA 2026

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
- OSPF single-area configuration (Area 0)
- OSPF Router ID — manual configuration and loopback interfaces
- OSPF passive-interface on LAN-facing ports
- OSPF point-to-point network type on router links
- Two methods of OSPF network advertisement:
  network command vs ip ospf [process] area [area]
- /30 subnets for point-to-point router connections
- OSPF neighbor adjacency and Full state verification
- OSPF process IDs — locally significant, don't need to match
- FHRPs (HSRP, VRRP, GLBP) — conceptual understanding

## Repository Structure
Each lab folder contains:
- topology.png — Packet Tracer network diagram
- config-[device].txt — full running configuration per device
- notes.md — topology summary, what was configured,
  verification steps, and lessons learned
