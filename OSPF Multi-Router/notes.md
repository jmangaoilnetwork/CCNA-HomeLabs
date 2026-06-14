## OSPF Multi-Router Lab

### Topology
- R1, R2, R3 in a linear topology running OSPF Area 0
- PC1 on LAN 1 (192.168.1.0/24) connected via SW1 to R1 g0/0/0
- PC2 on LAN 2 (192.168.2.0/24) connected via SW2 to R3 g0/0/1
- Point-to-point /30 links between routers:
  - R1 g0/0/1 (10.0.0.1) ↔ R2 g0/0/0 (10.0.0.2) — 10.0.0.0/30
  - R2 g0/0/1 (10.0.0.5) ↔ R3 g0/0/0 (10.0.0.6) — 10.0.0.4/30

### IP Addressing
| Device | Interface    | IP Address        | Purpose         |
|--------|-------------|-------------------|-----------------|
| R1     | Loopback0   | 1.1.1.1/32        | Router ID       |
| R1     | G0/0/0      | 192.168.1.1/24    | LAN 1 Gateway   |
| R1     | G0/0/1      | 10.0.0.1/30       | Link to R2      |
| R2     | Loopback0   | 2.2.2.2/32        | Router ID       |
| R2     | G0/0/0      | 10.0.0.2/30       | Link to R1      |
| R2     | G0/0/1      | 10.0.0.5/30       | Link to R3      |
| R3     | Loopback0   | 3.3.3.3/32        | Router ID       |
| R3     | G0/0/0      | 10.0.0.6/30       | Link to R2      |
| R3     | G0/0/1      | 192.168.2.1/24    | LAN 2 Gateway   |
| SW1    | Vlan1       | 192.168.1.254/24  | Management      |
| SW2    | Vlan1       | 192.168.2.254/24  | Management      |
| PC1    | NIC         | 192.168.1.10/24   | GW 192.168.1.1  |
| PC2    | NIC         | 192.168.2.10/24   | GW 192.168.2.1  |

### What I Configured

**R1:**
- Loopback0: 1.1.1.1/32 — activated directly in OSPF
  Area 0 using 'ip ospf 1 area 0' on the interface
- G0/0/0: LAN-facing, passive-interface configured
  under router ospf — no OSPF Hellos sent to SW1
- G0/0/1: router-facing, ip ospf network point-to-point
  configured, ip ospf priority 1
- router ospf 1, router-id 1.1.1.1
- network commands for 192.168.1.0/24 and 10.0.0.0/30

**R2:**
- Loopback0: 2.2.2.2/32
- G0/0/0 and G0/0/1: both ip ospf network point-to-point,
  ip ospf priority 1
- router ospf 2, router-id 2.2.2.2
- network commands for both /30 links

**R3:**
- Loopback0: 3.3.3.3/32
- G0/0/0: router-facing, ip ospf network point-to-point,
  ip ospf priority 1
- G0/0/1: LAN-facing, passive-interface configured
  under router ospf — no OSPF Hellos sent to SW2
- router ospf 3, router-id 3.3.3.3
- network commands for 10.0.0.4/30 and 192.168.2.0/24

**SW1 and SW2:**
- Basic Layer 2 switches — no VLAN configuration needed
- Management IP on Vlan1: SW1 = 192.168.1.254/24,
  SW2 = 192.168.2.254/24
- No default gateway configured — not needed for
  basic connectivity in this lab

### Notable Configuration Differences from Standard
- R1 Loopback0 uses 'ip ospf 1 area 0' directly on
  the interface instead of a network command under
  router ospf — both methods are valid
- R2 and R3 Loopback0 interfaces are NOT explicitly
  advertised via network command or interface command
  in this lab — only used for Router ID stability
- OSPF process IDs differ per router (1, 2, 3) —
  process IDs are locally significant and do not
  need to match between routers for adjacency

### Verification
- show ip ospf neighbor: Full state on all
  adjacencies ✅
- show ip route: O routes present for remote
  subnets on all routers ✅
- show ip ospf interface: point-to-point network
  type confirmed on router-to-router links ✅
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
  (2) ip ospf [process] area [area] directly on
  the interface — both achieve the same result
- /30 subnets are standard for point-to-point
  router links — only 2 usable hosts needed
- Loopback interfaces use /32 mask for Router IDs —
  they never go down ensuring OSPF stability
- passive-interface stops Hello messages on LAN
  ports but still advertises the subnet into OSPF
- ip ospf network point-to-point eliminates
  unnecessary DR/BDR election on two-router links
- OSPF routes appear as O in routing table with
  AD of 110 and cumulative cost [110/cost]
- log-adjacency-changes under router ospf logs
  neighbor state changes to console — useful
  for troubleshooting
- Always configure loopbacks BEFORE starting the
  OSPF process to avoid Router ID issues
