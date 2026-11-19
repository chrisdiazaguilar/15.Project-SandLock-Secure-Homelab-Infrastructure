# Changelog

## 2026-11-19 — Cisco ZBF activated; IPv4 DNS/HTTPS verified
- Replaced the restrictive stateless WAN *interface* ACL with Cisco ZBF zone membership: ISP-facing Gi0/0/0 in SL-OUTSIDE, OPNsense-facing Gi0/0/1 in SL-INSIDE.
- Outbound SL-ZBF-OUTBOUND inspects DNS, HTTP and HTTPS and drops unmatched interzone traffic. VTY SSH restrictions remain present.
- OPNsense verified public DNS, local Unbound DNS, system name lookup, TCP 443 and IPv4 HTTPS fetch (577 bytes). Non-forced HTTPS fetch failed previously; IPv6/address selection not conclusively diagnosed.
- Cisco verified 145 inspection session creations, 25 unmatched packet drops, 18 active NAT translations at sample time. Reload safeguard successfully canceled.
- **Pending:** proof of post-cutover startup-config save, management accessibility, unsolicited inbound and household/VLAN isolation tests, supported update strategy. Do not mark production validation complete.
- Details: [ZBF activation record](docs/network/cisco-zbf-activation-2026-11-19.md).


## 2026-11-19 — Live Cisco ZBF cutover, restored IPv4 DNS/HTTPS
- Assigned Cisco `Gi0/0/1` to `SL-INSIDE`, `Gi0/0/0` to `SL-OUTSIDE`, and removed the incompatible `SANDLOCK-WAN-IN` interface attachment. Stateful `SL-ZBF-OUTBOUND` with DNS/HTTP/HTTPS inspection and default drop is operating.
- OPNsense confirmed direct and Unbound DNS, system name lookup, TCP 443 connection and IPv4 HTTPS fetch. Unforced HTTPS hostname fetch had failed; IPv6 investigation remains open.
- Cisco counters captured 145 inspection sessions, 284 DNS packets, 36 TCP packets, 25 class-default dropped packets; 18 active NAT translations at test time.
- Temporary reload protection was scheduled and canceled; `show reload` showed no scheduled reload. Lenovo `tmux`/USB console access was validated.
- **Post-cutover startup-config save NOT confirmed** by screenshots; no validated unwanted-inbound or VLAN/household isolation results yet. See [activation record](docs/network/cisco-zbf-activation-2026-11-19.md). Sensitive configuration exports are not published.


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
