# Cisco ISR 1100 upstream and OPNsense WAN integration

> **Latest checkpoint (November 19, 2026):** The ZBF change described in the historical notes below **has now been activated live**. OPNsense local/public DNS, TCP 443 and IPv4 HTTPS have passed; Cisco ZBF sessions and NAT translations are observed. The previous ingress ACL is detached. The reload safeguard was canceled. **Post-cutover startup-config save not yet confirmed**, nor VLAN/household isolation tests. [Read the current ZBF activation record](cisco-zbf-activation-2026-11-19.md).

> Status updated through November 16, 2026. This is an in-progress lab configuration: **router and firewall ICMP egress verified, but downstream clients, DNS/TCP, and isolation not validated end to end.** Private IPs are documented for project reproducibility; this repository is public.

## Intended path

Household ISP router (gateway `192.168.1.1/24`) → Cisco C1111-4P (`SL-RTR-01`) → OPNsense (`igb1` WAN) → OPNsense LAN/VLAN trunk → Seneca transparent TAP → Catalyst 2960X.

| Segment | Address/interface | Evidence/status |
| --- | --- | --- |
| Household LAN gateway | `192.168.1.1` | Verified by Dell network settings and Cisco ping |
| Cisco upstream | `Gi0/0/0`, DHCP lease `192.168.1.244/24` | Verified interface DHCP log and route |
| Cisco default gateway | `192.168.1.1` | Installed as DHCP-derived default route |
| Cisco downstream/transit | `Gi0/0/1`, `10.255.255.1/30` | Verified up/up and successful OPNsense-to-Cisco ping after faulty cable replacement |
| OPNsense WAN | `igb1`, intended `10.255.255.2/30` | Confirmed applied in OPNsense dashboard; default route via 10.255.255.1 |
| OPNsense LAN | `igb0` | Confirmed in OPNsense Interfaces → Assignments |
| OPNsense management | VLAN 99, `192.168.99.1/24` | Dell `eno2.99`, `192.168.99.10/24` pinged 4/4 |
| OPNsense VLAN trunk path | Previously documented `igb2` toward Seneca | Verify live VLAN parent mapping; LAN assignment is `igb0` |

## Verified work

- Cisco software: IOS XE `17.06.06a` (17.6 train, past lifecycle support); do not treat this as security-patched.
- Cisco SSH v2, VTY ACL `ROUTER-SSH-MGMT` permits source `192.168.250.0/24` and denies others; HTTP and HTTPS server disabled.
- Cisco `appxk9` and `securityk9` reported **IN USE**; this does not confirm entitlement. Smart Licensing reporting did not show an acknowledgment; no licensing changes made.
- DHCP configured on `Gi0/0/0`. Physical link came up, DHCP lease received and gateway ping succeeded 5/5 (4/6/12 ms min/avg/max).
- `SANDLOCK-WAN-IN` inbound ACL present on Cisco upstream. Restored ACL saved with DHCP and selected ICMP permits, then deny/log all remaining IPv4 (no TCP return allowance).
- OPNsense web UI reached over Dell wired, tagged VLAN 99 while Dell maintained separate Wi-Fi connectivity. Interface assignments positively identified `wan=igb1`, `lan=igb0`.
- In OPNsense WAN form: IPv4 changed from DHCP to Static IPv4, `10.255.255.2/30` entered, IPv6 changed from DHCPv6 to None, private-network blocking unchecked. Later OPNsense dashboard and routing screen confirmed applied WAN address and gateway (monitoring disabled).

## Security considerations / open items

1. Cisco IOS XE 17.6 is out of support; plan supported upgrade or upstream protective architecture before treating the router as a long-term trusted security boundary.
2. The current inbound ACL is **stateless**, not a replacement for a firewall. It may drop legitimate UDP return traffic (e.g., DNS); the currently saved ACL blocks ordinary return TCP/UDP traffic; revisit policy before broad egress.
3. OPNsense WAN gateway `10.255.255.1` and default route were verified; monitoring is disabled, so a green status is not proof of internet connectivity.
4. Cisco `Gi0/0/1` ↔ OPNsense `igb1` physical link and OPNsense-to-Cisco ping (1/1) verified after replacing faulty cable.
5. Plan NAT/forwarding and intentionally **block SandLock-originated access to household LAN `192.168.1.0/24`**, except explicitly authorized paths. Check OPNsense outbound NAT and Cisco NAT design to avoid unnecessary layers.
6. Validate DNS, IPv4 return traffic, client internet egress, inter-VLAN policy, and management isolation before claiming cutover complete.
7. Back up OPNsense configuration securely offline; Cisco startup configuration was successfully saved (`[OK]`) after limited connectivity tests. **Do not publish raw configurations, secrets, logs, or license identifiers.**

## Next checkpoint

Resume by confirming which WAN cable was unplugged, verifying physical links and saved Cisco DHCP/ACL/default route, reviewing OPNsense outbound NAT, and planning NAT plus filtering and household isolation. Preserve VLAN 99 management access.

## November 15 follow-up: physical transit troubleshooting

- Confirmed OPNsense route table installed default via `10.255.255.1` on WAN `igb1`; gateway widget marked active with monitoring disabled (not itself a connectivity test).
- Confirmed OPNsense LAN `igb0` is `192.168.1.1/24`, overlapping household router `192.168.1.1/24`. **Conflict unresolved**; avoid changing LAN without reviewing DHCP and management access.
- Initial OPNsense ping from `10.255.255.2` to `10.255.255.1` failed with a network-down error; user identified and replaced faulty Ethernet cable on Cisco `Gi0/0/1` ↔ OPNsense `igb1`.
- **After cable replacement, OPNsense diagnostics successfully pinged Cisco `10.255.255.1` from `10.255.255.2`: 1 sent / 1 received / 0% loss.** Direct transit connectivity now verified.
- Pending: test OPNsense to household gateway and public IPv4; resolve LAN overlap; audit Cisco stateless upstream ACL, routing/NAT and isolation before general access; validate multiple packets and end-to-end clients.

## November 15, 2026 — Cisco save and revised pause state

- Cisco `Gi0/0/0` obtained DHCP lease `192.168.1.244/24`, default route via `192.168.1.1`, and successful pings to household gateway (5/5) and public `1.1.1.1` (5/5) while physically connected.
- Cisco `Gi0/0/1` remained up/up at `10.255.255.1/30`; OPNsense WAN `igb1` at `10.255.255.2/30` directly pinged Cisco successfully after faulty cable replacement.
- Named `SANDLOCK-WAN-IN` ACL recreated and verified on `Gi0/0/0`: DHCP replies, ICMP echo replies/unreachable/time-exceeded permitted; remaining inbound IPv4 denied/logged. **No ordinary TCP/UDP returns allowed; not stateful or ready for general access.**
- `copy running-config startup-config` returned `[OK]`; Cisco startup configuration saved. No Cisco NAT configuration was found, and OPNsense outbound NAT mode remains unverified.
- OPNsense `igb0` LAN `192.168.1.1/24` still overlaps household network `192.168.1.0/24`; unresolved.
- **Revised pause state (user confirmed): leave devices powered on; user unplugged a WAN network cable.** Cable endpoint not independently specified; verify exactly which link is disconnected before resuming. Do not record a power-off. Internet connectivity through disconnected link is not expected during the pause. No end-to-end SandLock client internet validation has been completed.

**Next session:** Identify/reconnect WAN cable intentionally; inspect interface/link state, DHCP lease, default gateway, ACL and management access. Review OPNsense outbound NAT, design Cisco NAT and safe return filtering, resolve LAN overlap and enforce household isolation before enabling general traffic. Never publish credentials, private keys or raw device configuration exports.

## November 16, 2026 — Session checkpoint (paused before OPNsense backup)

**Verified from console and OPNsense screenshots:**

- Reconnected household-router LAN → Cisco `Gi0/0/0`. Cisco DHCP lease `192.168.1.244/24`, interface up/up, default route `0.0.0.0/0` via `192.168.1.1`; Cisco `Gi0/0/1` `10.255.255.1/30` up/up.
- `SANDLOCK-WAN-IN` extended ACL persisted across the previous pause: DHCP reply and selected ICMP permits, then `deny ip any any log` (more than 200 deny matches). It is **stateless and blocks ordinary TCP/UDP return traffic, including observed DNS replies**; general client internet use is not validated.
- OPNsense MANAGEMENT GUI `192.168.99.1` restored from Dell by recreating temporary tagged `eno2.99` with `192.168.99.10/24`; not a persistent host interface configuration.
- OPNsense Source NAT mode **Automatic Source NAT rule generation** (no manually listed rules). Cisco `Gi0/0/0` set `ip nat outside`, `Gi0/0/1` set `ip nat inside`; `SANDLOCK-NAT` standard ACL permits `10.255.255.2`, with overload to the `Gi0/0/0` DHCP interface.
- OPNsense diagnostic ping from `10.255.255.2` to `1.1.1.1`: **3 sent / 3 received**. Cisco translation table proved ICMP source NAT `10.255.255.2` → `192.168.1.244`, 1 active entry at test time; subsequent NAT statistics showed 139 hits, 27 misses and 0 active translations after entries expired. This confirms **ICMP path only**, not DNS/TCP or downstream clients.
- **Cisco NAT save to startup-config not confirmed** after November 16 changes. Earlier November 15 save remains verified; check/save current running config at next session.
- OPNsense VLAN devices confirmed **all nine VLANs on `igb2`**: tag 99 MANAGEMENT, 10 SERVERS, 20 WORKSTATIONS, 30 STORAGE, 40 WIFI-USERS, 50 SECURITY, 60 IOT, 70 LAB, 80 ACCESSPOINTS. `igb2` trunk link up; `igb1` WAN up; `igb0` LAN link down.
- `igb0` LAN remains `192.168.1.1/24`, conflicting with household router network `192.168.1.0/24`. Proposed separate `192.168.2.1/24` is **not applied**. VLAN 99 `192.168.99.1/24` must remain reachable during migration.
- Kea DHCPv4 service enabled; defined subnets: `192.168.20.0/24` WORKSTATIONS, `192.168.40.0/24` WIFI-USERS, `192.168.60.0/24` IOT, `192.168.70.0/24` LAB, `192.168.99.0/24` MANAGEMENT. No Kea `192.168.1.0/24` subnet observed.
- Dnsmasq enabled, interface selection includes LAN; LAN IPv4 DHCP range **`192.168.1.100–192.168.1.199`**, and LAN IPv6/RA range **`::1000–::2000`** with LAN constructor and SLAAC RA. Dnsmasq DNS listen port shown as `53053`. Review other DNS/DHCP dependencies before changing LAN; do not disable Dnsmasq globally.

**Actual stop:** On `Interfaces → Overview`, confirmed `igb0` has link down. **OPNsense configuration backup has not been downloaded/confirmed; no LAN readdress or DHCP change performed.** Equipment shutdown or cable disconnection was **not** requested/verified for this pause. Lenovo Ubuntu missing `sudo`; root password/recovery deferred; separate host maintenance item, not a network blocker.

**Resume in this order:** (1) Obtain secure offline OPNsense config backup at `System → Configuration → Backups`, never commit it; (2) check backups and LAN dependency/IPv6/RA settings; (3) plan and carefully migrate Dnsmasq LAN ranges and `igb0` to a non-overlapping subnet while retaining VLAN 99 access; (4) verify Cisco NAT startup persistence and design safe stateful return filtering rather than blanket WAN permit; (5) test DNS, TCP/UDP, client VLAN egress, inter-VLAN and household isolation; (6) capture validation evidence and save configs.


## November 19, 2026 — DNS/HTTPS failure traced to Cisco inbound WAN ACL; pre-ZBF backup verified

**Checkpoint status: diagnostic cause identified; mitigation NOT implemented; end-to-end internet egress NOT complete.**

- OPNsense Unbound is running (confirmed via `configctl unbound status`, PID observed). It listens on TCP/UDP 53; Dnsmasq listens on TCP/UDP 53053. `service unbound onestatus` misleadingly reported not running, so OPNsense's management command was used as the authoritative process check.
- On OPNsense, `drill @127.0.0.1 example.com` returned `SERVFAIL`; direct `drill @1.1.1.1 example.com` and TCP DNS query also failed. Firmware repository check reported a mirror/repository fetch failure. This is an egress/return-traffic issue, not evidence that Unbound itself is stopped.
- OPNsense's default route for 1.1.1.1 is via Cisco transit gateway `10.255.255.1` through WAN `igb1`. TCP connections from OPNsense to 1.1.1.1 ports 53 and 443 timed out; prior ICMP connectivity passed.
- WAN capture on OPNsense `igb1` observed outbound TCP SYNs from `10.255.255.2` to `1.1.1.1:443`, but no SYN-ACK response in that capture.
- Cisco `SL-RTR-01`: DHCP WAN on `Gi0/0/0`, transit `Gi0/0/1` up/up, default route to household gateway, and dynamic overload NAT active for OPNsense transit source. NAT translations appeared; they did **not** establish successful return reachability.
- Cisco WAN interface has `ip access-group SANDLOCK-WAN-IN in`. The stateless ACL permits DHCP replies and select ICMP, then `deny ip any any log`. The Cisco explicitly logged denied inbound return packets from external HTTPS server `1.1.1.1:443` and DNS source port 53. This **confirms WAN ingress ACL drops legitimate return traffic**; other failure modes are not yet excluded.
- Cisco IOS XE `17.06.06a`; `securityk9` and `appxk9` were reported `IN USE` (this is not confirmation of update entitlement/support). The CLI recognizes `zone security`, `policy-map type inspect`, and `class-map type inspect` syntax. No ZBF security policy or zone assignment has been created.
- Router SSH management remains restricted to its dedicated management subnet by `ROUTER-SSH-MGMT` on VTY 0–15. Avoid modifying VTY or management interface while testing new security policy.
- **Verified Cisco on-device backup:** `copy running-config flash:SL-RTR-01-pre-ZBF.cfg` completed; `dir flash:SL-RTR-01-pre-ZBF.cfg` verified the file exists, 7,350 bytes, November 19. This is a flash copy of **running configuration**, NOT proof of startup-config save, off-device backup, tested recovery, or secure OPNsense backup.
- Proposed direction is inspected/stateful return traffic with default-deny unsolicited ingress, coordinating/removing the conflicting stateless inbound ACL at controlled cutover only after rollback, console-access and management continuity checks. **No ACL, NAT, zone, or DNS changes made at this checkpoint.**
- Remaining risks: aging IOS XE patch/update eligibility; existing OPNsense LAN subnet overlap and DHCP dependencies; household LAN isolation, downstream clients and VLAN egress unvalidated.

**Next safe sequence:** secure an offline OPNsense configuration export; confirm direct/console recovery access; stage/document a supported ZBF inside-to-outside inspection policy and zone-pair with explicit default-deny reverse path; plan existing `SANDLOCK-WAN-IN` coexistence/cutover and an automatic rollback method; apply one controlled change at a time; verify TCP 443, UDP/TCP DNS, firmware reachability, blocked unsolicited inbound, household isolation, and all VLAN policies; persist config only after successful validation.

**Repository safety:** Do not commit the raw flash backup, full running-config, credentials/hashes, private keys, logs exposing sensitive identities, or complete screenshots of secrets.
