# Three-rack assembly and cabling

Rack 1: OPNsense and Seneca mounted side-by-side; rest of the U positions to be confirmed.

## Critical connections
1. Upstream router to confirmed OPNsense WAN.
2. OPNsense `igb2` → Seneca `enp2s0`.
3. Seneca `enp3s0` → Cisco `Gi1/0/1` trunk.
4. Seneca **permanent** USB NIC `enx8cae4cdd2eab` → Cisco `Gi1/0/19` VLAN 99 access.
5. Add hosts, AP and capture nodes in stages only after management/trunk validation.

## Bring-up order
1. Assemble racks, power and label devices.
2. Validate switch, firewall and management paths.
3. Insert inline TAP with direct bypass cable available.
4. Validate VLAN 99 and one access VLAN at a time.
5. Connect PVE/PBS, storage, AP and Pi nodes.
6. Record physical port map and reboot recovery evidence in private inventory.

Final connections should be verified on-site.
