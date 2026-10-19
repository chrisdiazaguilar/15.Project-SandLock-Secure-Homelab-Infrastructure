# OPNsense firewall

Recorded baseline: OPNsense 26.7.1_1 AMD64. Earlier `igb0` LAN and `igb1` WAN; VLAN trunk on `igb2`. VLAN 99 MANAGEMENT configured at 192.168.99.1/24 with DHCP. `INTERNAL_VLANS` alias created; per-VLAN policy tightened; VLAN 60 isolation fixed. Broad legacy LAN permit-any rules disabled at September 19 baseline.

## Step-by-step validation
1. Check current OPNsense release, interface assignments and VLAN tags.
2. Confirm trunk parent `igb2`, VLAN 99 interface and allowed management sources.
3. Review VLAN DHCP/DNS and firewall rules.
4. Test per-VLAN isolation, especially VLAN 60; ensure intended egress works.
5. Confirm legacy broad rules remain disabled and WAN bogon/private options are set deliberately.
6. Save encrypted configuration backup offline; never commit a raw export.
