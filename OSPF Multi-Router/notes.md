## OSPF Multi-Router Lab

### Topology
- R1, R2, R3 in a linear topology running OSPF Area 0
- PC1 on LAN 1 (192.168.1.0/24) connected via SW1 to R1 g0/0/0
- PC2 on LAN 2 (192.168.2.0/24) connected via SW2 to R3 g0/0/1
- Point-to-point /30 links between routers:
  - R1 g0/0/1 (10.0.0.1) ↔ R2 g0/0/0 (10.0.0.2) — 10.0.0.0/30
  - R2 g0/0/1 (10.0.0.5) ↔ R3 g0/0/0 (10.0.0.6) — 10.0.0.4/30

### IP Addressing
| Device | Interface  | IP Address       | Purpose        |
|--------|------------|------------------|----------------|
| R1     | Loopback0  | 1.1.1.1/32       | Router ID      |
| R1     | G0/0/0     | 192.168.1.1/24   | LAN 1 Gateway  |
| R1     | G0/0/1     | 10.0.0.1/30      | Link to R2     |
| R2     | Loopback0  | 2.2.2.2/32       | Router ID      |
| R2     | G0/0/0     | 10.0.0.2/30      | Link to R1     |
| R2     | G0/0/1     | 10.0.0.5/30      | Link to R3     |
| R3     | Loopback0  | 3.3.3.3/32       | Router ID      |
| R3     | G0/0/0     | 10.0.0.6/30      | Link to R2     |
| R3     | G0/0/1     | 192.168.2.1/24   | LAN 2 Gateway  |
| SW1    | Vlan1      | 192.168.1.254/24 | Management     |
| SW2    | Vlan1      | 192.168.2.254/24 | Management     |
| PC1    | NIC        | 192.168.1.10/24  | GW 192.168.1.1 |
| PC2    | NIC        | 192.168.2.10/24  | GW 192.168.2.1 |

### What I Configured

**R1:**
- Loopback0: 1.1.1.1/32 — activated in OSPF Area 0
  using 'ip ospf 1 area 0' directly on the interface
- G0/0/0: LAN-facing, passive-interface under router ospf
- G0/0/1: router-facing, ip ospf network point-to-point,
  ip ospf priority 1
- router ospf 1, router-id 1.1.1.1
- network commands for 192.168.1.0/24 and 10.0.0.0/30

**R2:**
- Loopback0: 2.2.2.2/32 — advertised into OSPF Area 0
  using 'ip ospf 2 area 0' directly on the interface,
  passive-interface configured so no Hellos are sent
- G0/0/0 and G0/0/1: both ip ospf network point-to-point,
  ip ospf priority 1
- router ospf 2, router-id 2.2.2.2
- network commands for both /30 links

**R3:**
- Loopback0: 3.3.3.3/32 — advertised into OSPF Area 0
  using 'ip ospf 3 area 0' directly on the interface,
  passive-interface configured so no Hellos are sent
- G0/0/0: router-facing, ip ospf network point-to-point,
  ip ospf priority 1
- G0/0/1: LAN-facing, passive-interface under router ospf
- router ospf 3, router-id 3.3.3.3
- network commands for 10.0.0.4/30 and 192.168.2.0/24

**SW1 and SW2:**
- Basic Layer 2 switches
- Management IP on Vlan1: SW1 = 192.168.1.254/24,
  SW2 = 192.168.2.254/24

### Notable Configuration Details
- OSPF process IDs differ per router (1, 2, 3) —
  process IDs are locally significant and do not
  need to match between routers for adjacency
- Loopback interfaces on all three routers are
  advertised into OSPF and set as passive-interface —
  they appear in the routing table of all routers
  as OSPF routes but send no Hello messages
- R1 uses 'ip ospf 1 area 0' on Loopback0 instead
  of a network command — both methods are valid

### Verification
- show ip ospf neighbor: Full state on all
  adjacencies ✅
- show ip route: O routes present for all remote
  subnets including loopback /32 routes ✅
- show ip ospf interface: point-to-point confirmed
  on router links, passive confirmed on LAN
  interfaces and all loopbacks ✅
- show ip ospf: Router ID confirmed as 1.1.1.1,
  2.2.2.2, 3.3.3.3 respectively ✅
- Ping from PC1 (192.168.1.10) to PC2
  (192.168.2.10): success ✅

### Known Packet Tracer Limitation
- Packet Tracer does not always honor loopback
  interfaces for automatic OSPF Router ID selection
  even after 'clear ip ospf process'
- Workaround: manually set router-id under ospf
  process with 'router-id x.x.x.x'
- In real Cisco IOS, loopback takes priority over
  physical interfaces for Router ID automatically

### What I Learned
- OSPF process IDs are locally significant — they
  do not need to match between routers for neighbor
  adjacency to form
- Two methods to advertise a network into OSPF:
  (1) network command under router ospf
  (2) ip ospf [process] area [area] on the interface
  directly — both achieve the same result
- Loopback interfaces should be advertised into OSPF
  and set as passive — they appear in all routers'
  routing tables as /32 host routes
- passive-interface on loopbacks prevents unnecessary
  Hello messages on interfaces that will never have
  OSPF neighbors
- /30 subnets are standard for point-to-point router
  links — only 2 usable hosts needed per link
- Loopback interfaces use /32 mask for Router IDs —
  they never go down ensuring OSPF stability
- ip ospf network point-to-point eliminates
  unnecessary DR/BDR election on two-router links
- OSPF routes appear as O in routing table with
  AD of 110 and cumulative cost [110/cost]
- Loopback /32 routes appear as O in other routers'
  routing tables once advertised into OSPF
- Always configure loopbacks BEFORE starting the
  OSPF process to avoid Router ID issues in PT
