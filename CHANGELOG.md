# Changelog

## 2026-11-19 — OPNsense network audit and verified pre-migration backup
- Verified all nine OPNsense VLANs (10–80, 99) active with parent `igb2`; WAN `igb1` default route via `10.255.255.1`. Dell VLAN 99 `eno2.99` connectivity to `192.168.99.1` passed.
- Confirmed PF enabled, Unbound listening on 53, and Dnsmasq running on UDP 67 and TCP/UDP 53053 with configured DHCP ranges for nine VLANs and legacy LAN.
- Copied current `/conf/config.xml` to separate local pre-LAN-migration backup; verified equal SHA-256 checksums on OPNsense and after transfer to Dell. Preserved earlier pre-ZBF backup. No sensitive exports committed.
- Audited `INTERNAL_VLANS` and found MANAGEMENT VLAN 99 broad allow policies; household access is **not yet proven blocked**. Legacy `igb0` `192.168.1.1/24` remains disconnected and overlaps upstream subnet.
- OPNsense RJ45 serial console not working with attempted USB-to-RJ45 cable; direct recovery method unverified. No firewall, DHCP, routing or interface changes applied. Household isolation paused at MANAGEMENT rule review. Ryzen Proxmox, i7 PBS and R320 NAS all require hardware work.
- Active next steps are consolidated in [current roadmap checkpoint](docs/roadmap/next-steps.md); prior Cisco ZBF change record left intact.

## 2026-11-19 — Cisco ZBF activation and persistence verification
- Assigned Gi0/0/1 to SL-INSIDE and Gi0/0/0 to SL-OUTSIDE; detached the restrictive SANDLOCK-WAN-IN interface ACL. SL-ZBF-OUTBOUND inspects DNS, HTTP, HTTPS and drops unmatched interzone traffic; SSH VTY ACL retained.
- OPNsense direct/local DNS, system hostname lookup, TCP 443 and IPv4 HTTPS fetch (577 bytes) succeeded. Earlier non-forced hostname fetch failed; IPv6/address selection investigation remains open.
- Cisco inspection reported 145 created sessions, 284 DNS packets, 36 TCP packets, 25 default drops; NAT reported 18 active translations at observation time.
- Timed reload was canceled; no reload scheduled. Cisco running-to-startup save returned [OK]. Subsequent readback showed both SL-OUTSIDE and SL-INSIDE zone-member assignments in startup-config.
- Still untested: post-reboot persistence, inbound blocking, management reachability, household/VLAN isolation and broader protocol coverage. See [single detailed change record](docs/network/cisco-zbf-activation-2026-11-19.md).

## 2026-11-19 — Cisco WAN ACL return-traffic diagnosis and pre-ZBF flash checkpoint
- On OPNsense, confirmed Unbound running on port 53 and Dnsmasq on 53053; local DNS returned SERVFAIL. Direct external UDP/TCP DNS failed; HTTPS TCP connection timed out, while prior ICMP ping succeeded.
- Captured outbound OPNsense WAN SYN packets without observed return SYN-ACK. Cisco WAN ACL `SANDLOCK-WAN-IN` explicitly logged denied HTTPS and DNS return packets; NAT translations and correct default routes independently verified.
- Verified ISR C1111-4P IOS XE 17.06.06a, securityk9/appxk9 reported IN USE, and ZBF inspection CLI help available. No ZBF policies or zone assignments applied.
- Verified VTY SSH access limited by `ROUTER-SSH-MGMT` to dedicated management subnet.
- Backed up Cisco **running config** to `flash:SL-RTR-01-pre-ZBF.cfg` (7,350 bytes), verified with flash directory listing. Not a confirmed startup-config save or offline restore-ready backup.
- **No live firewall policy changes made.** Next: offline OPNsense backup and safe ZBF cutover/rollback plan, then DNS/HTTPS and isolation validation. No credential hashes or raw device exports committed.


## 2026-11-16 — Cisco/OPNsense NAT verification and LAN dependency audit
- Reconnected Cisco upstream and verified DHCP `192.168.1.244`, gateway `192.168.1.1`, transit `10.255.255.1/30`, and persisted restrictive WAN ACL.
- Confirmed OPNsense Automatic Source NAT; configured Cisco inside/outside NAT and overload only for `10.255.255.2`. Verified firewall-originated ICMP ping to `1.1.1.1` (3/3) and Cisco ICMP NAT translation; DNS/TCP/client internet remains unverified.
- Restored Dell VLAN 99 management; confirmed all nine OPNsense VLANs on `igb2`, with LAN `igb0` link down.
- Audited Kea DHCP subnets and discovered Dnsmasq LAN IPv4/IPv6 ranges requiring migration; overlapping `192.168.1.1/24` LAN still unchanged.
- **Paused immediately before secure OPNsense configuration backup.** Backup not confirmed; Cisco NAT post-change save not confirmed; no shutdown confirmed.
- Updated [WAN integration record](docs/network/cisco-isr1100-opnsense-wan.md) and [next steps](docs/roadmap/next-steps.md).

## 2026-11-15 — Cisco startup save and pause
- Verified Cisco DHCP WAN, routed upstream internet ping (5/5), transit link to OPNsense and restored stateless inbound ACL.
- Saved Cisco configuration to startup-config (`[OK]`); Cisco NAT absent, OPNsense outbound NAT unverified.
- Documented LAN subnet overlap and pending isolation, NAT, and end-to-end verification.
- **Revised shutdown plan:** equipment stays powered on; user disconnected a WAN cable for pause (exact endpoint to confirm at resumption).
- Updated [Cisco/OPNsense checkpoint](docs/network/cisco-isr1100-opnsense-wan.md).

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
