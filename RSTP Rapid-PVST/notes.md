## STP/RSTP (Rapid PVST+) Lab

### Topology
- SW1 — Root Bridge (distribution), connects to SW2 and SW3
- SW2 — Secondary Root VLAN 10, connects to SW1, SW3, SW4
- SW3 — Secondary Root VLAN 20, connects to SW1, SW2, SW5
- SW4 — Access switch, connects to SW2
- SW5 — Access switch, connects to SW3
- Redundant link: SW2 G0/2 ↔ SW3 G0/2
- All switches Layer 2 only — no routing

### VLANs
- VLAN 10 (Sales): PC1 (SW4), PC3 (SW5), PC5 (SW2)
- VLAN 20 (HR): PC2 (SW4), PC4 (SW5), PC6 (SW2)
- VLAN 99 (Management): all switch SVIs
- VLAN 1001 (Native): all trunk ports

### STP Configuration
- All switches: spanning-tree mode rapid-pvst
- SW1: priority 4096 for VLAN 10 and 20 (Root Bridge)
- SW2: priority 8192 VLAN 10, 16384 VLAN 20
- SW3: priority 8192 VLAN 20, 16384 VLAN 10
- Root Guard on SW1 G0/1 and G0/2
- PortFast + BPDU Guard on all PC-facing ports

### Expected STP Behavior — VLAN 10
- Root Bridge: SW1
- SW3 G0/2 blocked (Alternate)
- SW2 G0/2 designated (Forwarding)

### Expected STP Behavior — VLAN 20
- Root Bridge: SW1
- SW2 G0/2 blocked (Alternate)
- SW3 G0/2 designated (Forwarding)

### Verification
- show spanning-tree summary: rapid-pvst mode ✅
- show spanning-tree vlan 10 SW1: this bridge is root ✅
- show spanning-tree vlan 10 SW3: Gi0/2 Altn/BLK ✅
- show spanning-tree vlan 20 SW2: Gi0/2 Altn/BLK ✅
- PortFast and BPDU Guard confirmed on access ports ✅
- Same-VLAN pings success ✅
- Cross-VLAN ping fails ✅ (no routing — expected)
- SW1 shutdown: SW2 becomes VLAN 10 root,
  SW3 becomes VLAN 20 root ✅

  ### What I Learned
- Rapid PVST+ runs a separate STP instance per VLAN —
  each VLAN independently elects its own Root Bridge
  and maintains its own port roles and states
- spanning-tree mode rapid-pvst must be configured
  on every switch in the topology — a mismatch causes
  the affected switch to fall back to classic STP
- Root Bridge election is deterministic — lowest
  priority wins, lowest MAC address breaks ties.
  Never leave Root Bridge election to chance in
  production — always manually set priorities
- Per-VLAN load balancing is achievable with PVST+
  by assigning different secondary root bridges per
  VLAN — VLAN 10 prefers SW2 as backup, VLAN 20
  prefers SW3, distributing traffic across both uplinks
- The same physical link (SW2-SW3 G0/2) can be
  Forwarding for one VLAN and Blocked for another
  simultaneously — this is unique to PVST+
- Blocked ports are called Alternate ports in
  Rapid PVST+ — they instantly transition to
  Forwarding when the active path fails without
  waiting for timers
- Rapid PVST+ converges significantly faster than
  classic STP — Alternate ports transition to
  Forwarding almost immediately using the RSTP
  proposal/agreement mechanism instead of timers
- PortFast skips Listening and Learning states
  on access ports — end devices get network access
  instantly instead of waiting 30 seconds
- BPDU Guard immediately err-disables a PortFast
  port if a BPDU is received — protects the topology
  from accidental switch connections on access ports
- Root Guard on SW1's downlinks prevents SW2 or SW3
  from ever advertising a superior BPDU and stealing
  the Root Bridge role — critical in production
- Management VLANs (VLAN 99) should be separate
  from data VLANs — switch management traffic stays
  isolated from user traffic
- Native VLAN should always be changed from default
  VLAN 1 to an unused VLAN (1001) — prevents VLAN
  hopping security vulnerability
- Cross-VLAN communication requires a router or
  Layer 3 switch — pure Layer 2 switching isolates
  VLANs from each other completely
- When the Root Bridge fails, the secondary root
  bridge takes over automatically — no manual
  intervention needed, STP reconverges on its own
- Secondary root bridge design is critical —
  without it, any switch could become Root Bridge
  during failover based on MAC address alone,
  potentially creating a suboptimal topology
