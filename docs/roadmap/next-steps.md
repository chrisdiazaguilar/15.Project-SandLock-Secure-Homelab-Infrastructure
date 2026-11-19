# Next steps

## Current checkpoint — November 19, 2026 (after ZBF activation)

**Verified live:** Cisco ISR ZBF zones and outbound DNS/HTTP/HTTPS inspection are active; conflicting ingress ACL detached from Gi0/0/0. OPNsense public/local DNS and IPv4 HTTPS work. 145 Cisco inspection sessions and 18 NAT translations observed; reload safeguard canceled.

1. Verify **post-cutover Cisco startup-config save** (not yet evidenced), then planned reboot persistence after recovery readiness is confirmed.
2. Validate authorized management via VLAN 250 and absence of accidental WAN management exposure; check Cisco self-zone behavior and unsolicited inbound traffic.
3. Verify SandLock cannot reach household LAN except explicitly approved traffic, and test OPNsense VLAN isolation and downstream client egress.
4. Review NTP, ICMP, firmware and other needed protocols; investigate IPv6/hostname fetch discrepancies.
5. Resolve OPNsense LAN subnet overlap and Dnsmasq DHCP/RA dependency with a separate rollback plan; complete backup/restore testing, monitoring and software-upgrade strategy.

[Verified change record](../network/cisco-zbf-activation-2026-11-19.md). Earlier checkpoints below remain historical.


## Current state — November 19, 2026, after ZBF cutover

Cisco ZBF is **active in the running configuration** and public DNS, localhost Unbound, TCP 443, and IPv4 HTTPS succeeded from OPNsense. A scheduled emergency reload was canceled. **Post-cutover startup-config save is not yet evidenced**; downstream client egress and isolation remain unvalidated. See [activation record](../network/cisco-zbf-activation-2026-11-19.md).

1. Verify management reachability and unsolicited-inbound blocking before persisting firewall changes; capture startup save confirmation when completed.
2. Test IPv6 DNS/HTTPS/address selection and add only required protocols beyond DNS/HTTP/HTTPS.
3. Validate OPNsense-to-household network isolation and client VLAN egress/inter-VLAN rules.
4. Securely back up OPNsense configuration off-device; do not upload secrets or raw configs to public GitHub.
5. Resolve overlapping OPNsense LAN subnet and Dnsmasq DHCP/RA dependencies in a separate managed change.
6. Review IOS XE support/update path, monitoring, recovery drills, and longer-term stability.

### Historical checkpoints below (superseded by active ZBF status)


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
