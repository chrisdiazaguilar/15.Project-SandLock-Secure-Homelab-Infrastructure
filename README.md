# Project SandLock

Secure, segmented Proxmox homelab and defensive security monitoring platform.

> Documentation snapshot: October 19, 2026. Reconstructed from project discussions; verify against live systems. Do not publish passwords, private keys, packet captures, or raw device configuration exports.

## Goals
- VLAN segmentation and OPNsense firewall policies
- Cisco Catalyst 2960X managed switching
- Transparent Debian/Suricata inline LAN monitoring
- PineTapPi wireless capture from one authorized Hak5 Pineapple Mark VII
- Proxmox VE/PBS, backups, local services and security labs
- Command Pi mission-control dashboard

## Documentation
- [Topology](docs/architecture/topology.md) · [Hardware](docs/architecture/inventory.md)
- [VLANs](docs/network/vlans-and-addressing.md) · [OPNsense](docs/network/opnsense.md) · [Cisco switch](docs/network/cisco-2960x.md) · [ISR1100 upstream integration](docs/network/cisco-isr1100-opnsense-wan.md)
- [Seneca LAN TAP](docs/devices/seneca-lan-tap.md) · [PineTapPi](docs/devices/pinetappi.md) · [Command Pi](docs/devices/command-pi.md) · [Proxmox](docs/devices/proxmox.md)
- [Rack & cabling](docs/operations/rack-and-cabling.md) · [Validation](docs/operations/validation.md)
- [Milestones](docs/roadmap/milestones.md) · [Next steps](docs/roadmap/next-steps.md) · [Security](SECURITY.md)

## Status

**November 19, 2026 update:** Cisco NAT and default routes verified, but the inbound Cisco WAN ACL is dropping DNS/HTTPS return traffic; OPNsense DNS resolution and firmware update access remain unresolved. IOS XE ZBF inspection commands are available, but no security zones or policies have been applied. The router's running configuration has been copied to verified flash backup `SL-RTR-01-pre-ZBF.cfg` (7,350 bytes); this does not establish off-device backup or startup-config persistence. [Diagnostics and next actions](docs/network/cisco-isr1100-opnsense-wan.md).


**November 2026 WAN update:** Cisco ISR1100 has a verified DHCP upstream connection to the household router; OPNsense WAN is confirmed as `igb1`, with static transit address entered but not yet verified applied. See [integration checkpoint](docs/network/cisco-isr1100-opnsense-wan.md). Full NAT, gateway, firewall and end-to-end connectivity remain pending.

VLAN/firewall baseline validated during September; Seneca software and Suricata baseline established; PineTapPi rolling capture functional October 4; Command Pi pre-staged October 7. Final rack integration, complete management migration, Proxmox integration and end-to-end retesting remain.

**Publishing warning:** repository is public. Even private-range addresses and hardware inventories disclose architecture. Review before sharing further. No license for reuse has been granted.
