# Cisco ISR 1100 upstream and OPNsense WAN integration

> Status as observed November 12–15, 2026. This is an in-progress lab configuration, **not** validated end-to-end connectivity. Private IPs are documented for project reproducibility; this repository is public.

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
