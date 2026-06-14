### Topology
- SW3 (3560-24PS) = Layer 3 switch — performs inter-VLAN routing via SVIs
- SW1 (2960-24TT) = Layer 2 access switch — connected to SW2 via trunk (g0/1)
- SW2 (2960-24TT) = Layer 2 access switch — connected to SW1 (g0/1) and SW3 (g0/2)
- SW3 connects to SW2 via trunk (g0/1)

**VLAN assignments:**
- VLAN 10 (Sales): 192.168.10.0/24
  - PC-1 (SW1 f0/2), PC-2 (SW1 f0/1), PC-7 (SW2 f0/1)
- VLAN 20 (HR): 192.168.20.0/24
  - PC-3 (SW2 f0/3), PC-4 (SW2 f0/2)
- VLAN 30 (IT): 192.168.30.0/24
  - PC-5 (SW1 f0/3), PC-6 (SW1 f0/4)

**Trunk links:**
- SW1 g0/1 ↔ SW2 g0/1 — allows VLAN 10, 30 | Native VLAN 1001
- SW2 g0/2 ↔ SW3 g0/1 — allows VLAN 10, 20, 30 | Native VLAN 1001

### What I Configured

**SW3 (Layer 3 Switch) — SVIs:**
- ip routing — enables Layer 3 routing on the switch
- Vlan10: ip address 192.168.10.1 255.255.255.0 (gateway for VLAN 10)
- Vlan20: ip address 192.168.20.1 255.255.255.0 (gateway for VLAN 20)
- Vlan30: ip address 192.168.30.1 255.255.255.0 (gateway for VLAN 30)
- Vlan1: shutdown (security best practice)
- G0/1: trunk port, allowed VLANs 10, 20, 30, native VLAN 1001

**SW2 (Layer 2 Access Switch):**
- F0/1: access VLAN 10 (PC-7)
- F0/2: access VLAN 20 (PC-4)
- F0/3: access VLAN 20 (PC-3)
- G0/1: trunk to SW1, allowed VLANs 10, 30, native VLAN 1001
- G0/2: trunk to SW3, allowed VLANs 10, 20, 30, native VLAN 1001

**SW1 (Layer 2 Access Switch):**
- F0/1: access VLAN 10 (PC-2)
- F0/2: access VLAN 10 (PC-1)
- F0/3: access VLAN 30 (PC-5)
- F0/4: access VLAN 30 (PC-6)
- G0/1: trunk to SW2, allowed VLANs 10, 30, native VLAN 1001

### Verification
- show ip route (SW3): C routes present for VLAN 10, 20, 30 ✅
- show interfaces vlan 10/20/30: SVIs up/up ✅
- show ip interface brief (SW3): all SVIs show up/up ✅
- Ping from VLAN 10 host to VLAN 20 host - ✅
- Ping from VLAN 10 host to VLAN 30 host - ✅
- Ping from VLAN 20 host to VLAN 30 host - ✅

### What I Learned
- ip routing is mandatory on the Layer 3 switch —
  without it SVIs exist but inter-VLAN routing won't work
- SVIs must be up/up: VLAN must exist, at least one access
  port in that VLAN must be up, SVI must not be shutdown
- Unlike ROAS, no subinterfaces needed — SVIs handle
  routing internally without traffic leaving the switch
- Trunk allowed VLAN list matters — SW1 to SW2 trunk only
  allows VLANs 10 and 30 because SW1 has no VLAN 20 hosts.
  VLAN 20 only needs to traverse SW2 to SW3 trunk
- Native VLAN changed from default VLAN 1 to VLAN 1001
  on all trunk ports — security best practice to prevent
  VLAN hopping attacks
- Default gateway on all PCs must point to the SVI IP
  address on SW3 for their respective VLAN

### ROAS vs Layer 3 Switch Comparison
- ROAS: traffic leaves switch, goes to external router,
  comes back — creates bottleneck on physical interface
- Layer 3 Switch: inter-VLAN routing handled internally —
  faster, more scalable, no external router needed
- Layer 3 switch is preferred in enterprise environments
  for inter-VLAN routing
