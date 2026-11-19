# Next steps

## Current checkpoint — November 19, 2026 (OPNsense audit; household isolation not applied)

**Verified:** Cisco ISR ZBF/NAT and OPNsense IPv4 DNS/HTTPS tests succeeded earlier; Cisco startup save and zone readback confirmed (reboot persistence not tested). OPNsense default route uses `10.255.255.1` via WAN `igb1`; trunk `igb2` is active at 1 Gbps. Nine VLAN interfaces (IDs 10–80, 99) are active and parented to `igb2`. Dell management `eno2.99` (`192.168.99.10/24`) reaches OPNsense `192.168.99.1` by tagged VLAN 99, independently of Dell Wi-Fi. PF enabled; Unbound listening on port 53 and Dnsmasq on UDP 67 and TCP/UDP 53053. Dnsmasq active config: `/usr/local/etc/dnsmasq.conf`, with ranges for nine VLANs and old LAN.

**Backups:** `/conf/config.xml` copied locally as `opnsense-pre-lan-migration-20261119.xml`; SHA-256 matched source and the independently transferred Dell copy. Earlier pre-ZBF backup preserved. Backup files and hashes are not published.

**Security finding:** `INTERNAL_VLANS` includes SandLock VLANs and `192.168.1.0/24`; standard VLAN block-before-allow rules exist, but MANAGEMENT VLAN 99 has broad pass-to-internal and pass-to-any policies. This does **not** demonstrate household isolation. Household Wi-Fi seen on Dell is `192.168.0.0/24`; Cisco ISP-facing network is `192.168.1.0/24`. Inspect actual management firewall rule ordering, exceptions, traffic paths and both household-side ranges before applying narrowly scoped blocks.

**Outstanding risk:** Legacy OPNsense `igb0` is link-down but configured at `192.168.1.1/24`, overlapping the Cisco upstream subnet. A possible replacement `192.168.2.1/24` has **not** been confirmed unused across all networks; migration must account for Dnsmasq LAN DHCPv4 `192.168.1.100–199`, IPv6 `::1000–::2000`/RA and rollback. OPNsense serial RJ45 recovery remains unverified; previous USB-to-RJ45 attempt failed. Dell web/SSH management is working but is not an independent recovery console.

### Next actions (current; no changes applied during audit)

1. In OPNsense web UI, inspect MANAGEMENT (VLAN 99) firewall rule ordering and aliases; design explicit SandLock-to-household restrictions, preserving Dell management and documented approved exceptions. Test from actual VLAN clients before claiming isolation.
2. Establish a tested out-of-band OPNsense recovery method or a proven safe rollback path before disruptive firewall, LAN, DHCP or routing changes.
3. Verify proposed LAN subnet is unused and plan coordinated `igb0` readdressing plus Dnsmasq DHCP/IPv6 RA changes; do not renumber during an inspection session.
4. Test downstream VLAN clients through the Catalyst/Seneca TAP, capture/monitoring and firewall isolation; verify Cisco management/self-zone and unsolicited inbound protections; investigate IPv6 and needed additional protocols.
5. Proxmox (Ryzen), PBS (i7) and Dell R320 NAS **require hardware work** and are deferred to a separate phase; do not report them deployed.
6. Continue updating this checkpoint and changelog only for newly verified milestones; keep sensitive configs and backups out of public GitHub.

[Existing detailed Cisco ZBF change record](../network/cisco-zbf-activation-2026-11-19.md). Earlier checkpoints below remain historical.

### Historical checkpoints (below)

The following checkpoints describe prior states, not the current live ZBF configuration.

## Current pause point — November 19, 2026

**Confirmed:** Cisco NAT/routing active, but inbound WAN ACL `SANDLOCK-WAN-IN` is denying DNS/HTTPS response traffic, so OPNsense DNS and firmware access remain broken. `securityk9` reported IN USE and Cisco ZBF inspection CLI is available. No zones or policy applied. On-device Cisco **running-config** backup verified at `flash:SL-RTR-01-pre-ZBF.cfg` (7,350 bytes); this is not an off-device backup or a confirmed post-NAT startup save.

1. Securely export OPNsense configuration offline and verify an out-of-band recovery route; keep sensitive backups outside public GitHub.
2. Confirm Cisco local-console recovery, capture rollback procedure and set a timed fail-safe before any security-zone/interface changes.
3. Design and stage supported ZBF stateful inspect policies for inside-initiated traffic, default-deny unsolicited inbound, and router self-zone management requirements. Coordinate migration from the current stateless WAN ACL; avoid broad `permit ip any any`.
4. Test TCP 443, UDP/TCP 53, Unbound resolution, firmware check, unsolicited inbound blocking, OPNsense VLAN policy and household-network isolation before persisting a new configuration.
5. Resolve existing OPNsense LAN `192.168.1.1/24` overlap separately, only after auditing Dnsmasq DHCP/RA and management access.

See [November 19 diagnostics](../network/cisco-isr1100-opnsense-wan.md).


## Current pause point — November 16, 2026

**Next action: Back up OPNsense configuration securely offline at System → Configuration → Backups.** The backup has **not** been confirmed. LAN `igb0` is link-down but still configured at `192.168.1.1/24`, overlapping household `192.168.1.0/24`. Preserve VLAN 99 management access and all nine VLANs on `igb2`.

1. Export and securely store OPNsense backup; do not place it, credentials or license IDs in GitHub.
2. Before changing `igb0`, audit Dnsmasq LAN IPv4 `192.168.1.100–199`, IPv6 `::1000–::2000`, RA, other DHCP dependencies and rollback access. Consider `192.168.2.1/24` only after checking subnet availability; this is not yet applied.
3. Change the LAN IP and associated Dnsmasq ranges in a controlled order; verify `192.168.99.1` management remains accessible.
4. Confirm Cisco NAT running-config survives startup: the new `SANDLOCK-NAT` overload is verified live but a **new post-NAT startup save is not confirmed**. Perform save after review.
5. Replace/augment stateless `SANDLOCK-WAN-IN` with an appropriate protected return-traffic design (currently DNS/TCP/UDP responses blocked); avoid broad inbound permits.
6. Test client IPv4/IPv6 behavior, DNS, outbound TCP/UDP, VLAN access policy, household-network isolation and inter-VLAN controls; document measured results rather than assumptions.
7. Update GitHub with confirmed outcomes only.

## Broader SandLock roadmap

1. Finish three-rack assembly and confirm U assignments and safe power.
2. Label cables, switch ports and each device interface.
3. Reconnect switch and validate Gi1/0/1 trunk and Gi1/0/19 management VLAN 99.
4. Validate permanent Seneca USB management static address and persistence.
5. Revalidate bridge and Suricata after inline insertion.
6. Bring Command Pi VLAN 99 online; test ASUS AP and monitor reachability.
7. Integrate Proxmox VE/PBS, storage and authorized alerting.
8. Run backup **restore** drills and document recovery steps.
9. Develop optional JavaScript dashboard and packet-analysis VM.
