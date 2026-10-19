# Validation and troubleshooting

## Recorded
- VLAN 20 validation used during initial testing.
- VLAN 60 isolation fixed in September.
- MANAGEMENT VLAN 99 available as OPNsense management path September 19.
- Seneca Suricata test passed; service active at software staging.
- PineTapPi rolling packet capture verified October 4.
- Command Pi baseline backup SHA-256 verification passed October 7.

## Post-rack checklist
- [ ] Verify physical links, Cisco trunk and access VLANs.
- [ ] Verify management VLAN 99 and permanent USB NIC reachability.
- [ ] Verify DHCP, DNS, isolation and internet egress for each VLAN.
- [ ] Repeat VLAN 60 isolation test.
- [ ] Verify Suricata alerts and capture visibility.
- [ ] Verify PineTapPi disk rotation and free space.
- [ ] Verify Proxmox and PBS backup/restore.
- [ ] Verify Command Pi dashboard and alerts.
- [ ] Verify recovery after safe power cycle.

Use read-only status commands, pings, routing inspection and OPNsense firewall logs; keep sensitive test artifacts out of GitHub.
