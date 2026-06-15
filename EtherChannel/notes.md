## EtherChannel + Inter-VLAN Routing Lab (LACP + L3 Switch)

### Topology
- SW3 (3560 L3 Switch) — distribution layer, performs
  inter-VLAN routing via SVIs for VLANs 10, 20, 30
- SW2 (2960) — access layer, connects to SW3 via trunk
  (G0/1) and to SW1 via EtherChannel (Po1)
- SW1 (2960) — access layer, connects to SW2 via
  EtherChannel (Po1) using LACP Active/Active
- EtherChannel: SW1 F0/21-23 ↔ SW2 F0/21-23
  bundled into Port-Channel 1

### VLANs
- VLAN 10 (Sales): 192.168.10.0/24 — PC-1, PC-2 (SW1),
  PC-7 (SW2)
- VLAN 20 (HR): 192.168.20.0/24 — PC-3, PC-4 (SW2)
- VLAN 30 (IT): 192.168.30.0/24 — PC-5, PC-6 (SW1)

### What Changed From Original L3 Switch Lab
- Replaced single trunk link between SW1 and SW2
  with a 3-link LACP EtherChannel (Port-Channel 1)
- SW3 configuration unchanged — ip routing and SVIs
  remain exactly the same

### What I Configured
- EtherChannel on SW1 and SW2 using LACP Active/Active
- Port-Channel 1 configured as trunk, native VLAN 1001,
  allowed VLANs 10 and 30
- Member interfaces F0/21-23 matching trunk settings
  on both switches

### Verification
- show etherchannel summary: Po1 SU, all ports P ✅
- show interfaces trunk: Port-channel1 as trunk ✅
- Ping same VLAN across EtherChannel: success ✅
- Ping inter-VLAN via SW3 SVIs: success ✅
- Shutdown F0/21, ping still passes on 2 links ✅
- Restore F0/21, rejoins EtherChannel automatically ✅

### What I Learned
- EtherChannel integrates into existing topologies
  without changing any other device configurations
- STP treats Port-Channel as one logical link —
  no ports blocked between SW1 and SW2
- Inter-VLAN routing path: SW1 → EtherChannel →
  SW2 → SW3 SVIs → back down to destination
- Link failure is transparent — no STP reconvergence
  needed, traffic shifts to remaining links instantly
- Native VLAN must match on both sides of EtherChannel
  trunk or VLAN hopping risk is introduced
- Allowed VLANs on Port-Channel must match member
  interfaces exactly or EtherChannel won't form
