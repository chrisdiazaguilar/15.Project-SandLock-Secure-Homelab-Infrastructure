# Project SandLock

Secure, segmented Proxmox homelab and defensive security monitoring platform.

> Documentation updated through November 19, 2026. Reconstructed from project discussions; verify against live systems. Do not publish passwords, private keys, packet captures, or raw device configuration exports.

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

**November 19, 2026 — Cisco ZBF live activation:** On the ISR C1111-4P, configured inside/outside security-zone membership, enabled stateful DNS/HTTP/HTTPS inspection, and detached the conflicting ingress ACL from the WAN interface. OPNsense external/local DNS, hostname lookup, TCP 443 and IPv4 HTTPS fetch all passed. Cisco inspection counters showed 145 sessions created; NAT showed 18 active translations. The rollback reload was canceled. **Post-cutover startup-config save and reboot persistence are not yet confirmed.** Downstream VLAN, household-network isolation, IPv6 behavior and router management reachability still require testing. [See verified ZBF change record](docs/network/cisco-zbf-activation-2026-11-19.md).


VLAN/firewall baseline validated during September; Seneca software and Suricata baseline established; PineTapPi rolling capture functional October 4; Command Pi pre-staged October 7. Final rack integration, complete management migration, Proxmox integration and end-to-end retesting remain.

**Publishing warning:** repository is public. Even private-range addresses and hardware inventories disclose architecture. Review before sharing further. No license for reuse has been granted.
