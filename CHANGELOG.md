# Changelog

## 2026-11-15
- Documented verified Cisco ISR1100 `Gi0/0/0` DHCP connection, default route, ACL limitations, and gateway ping success.
- Verified OPNsense live interface assignments (`WAN=igb1`, `LAN=igb0`) and VLAN 99 Dell management connectivity.
- Recorded proposed `10.255.255.2/30` OPNsense WAN setting as entered, **not yet confirmed applied**; static gateway and full end-to-end validation remain pending.
- Added [ISR1100–OPNsense integration checkpoint](docs/network/cisco-isr1100-opnsense-wan.md).

## 2026-11-11
- Added WAN cutover validation to the documented OPNsense milestones. End-to-end validation is pending.

## 2026-10-19
- Initial Project SandLock documentation based on recorded homelab work.
- Added architecture, equipment, network configuration, security, validation, rack planning and roadmap.
- Distinguished completed work from staged or planned work.
