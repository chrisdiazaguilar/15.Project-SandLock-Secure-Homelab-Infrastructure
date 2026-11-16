# Cisco ISR 1100 upstream and OPNsense WAN integration

> Status as observed November 12–15, 2026. This is an in-progress lab configuration, **not** validated end-to-end connectivity. Private IPs are documented for project reproducibility; this repository is public.

## Intended path

Household ISP router (gateway `192.168.1.1/24`) → Cisco C1111-4P (`SL-RTR-01`) → OPNsense (`igb1` WAN) → OPNsense LAN/VLAN trunk → Seneca transparent TAP → Catalyst 2960X.

| Segment | Address/interface | Evidence/status |
| --- | --- | --- |
| Household LAN gateway | `192.168.1.1` | Verified by Dell network settings and Cisco ping |
| Cisco upstream | `Gi0/0/0`, DHCP lease `192.168.1.244/24` | Verified interface DHCP log and route |
| Cisco default gateway | `192.168.1.1` | Installed as DHCP-derived default route |
| Cisco downstream/transit | `Gi0/0/1`, `10.255.255.1/30` | Verified in running configuration; link to firewall not yet verified |
| OPNsense WAN | `igb1`, intended `10.255.255.2/30` | Device assignment confirmed in OPNsense GUI; address entered in form, final applied state not verified |
| OPNsense LAN | `igb0` | Confirmed in OPNsense Interfaces → Assignments |
| OPNsense management | VLAN 99, `192.168.99.1/24` | Dell `eno2.99`, `192.168.99.10/24` pinged 4/4 |
| OPNsense VLAN trunk path | Previously documented `igb2` toward Seneca | Verify live VLAN parent mapping; LAN assignment is `igb0` |

## Verified work

- Cisco software: IOS XE `17.06.06a` (17.6 train, past lifecycle support); do not treat this as security-patched.
- Cisco SSH v2, VTY ACL `ROUTER-SSH-MGMT` permits source `192.168.250.0/24` and denies others; HTTP and HTTPS server disabled.
- Cisco `appxk9` and `securityk9` reported **IN USE**; this does not confirm entitlement. Smart Licensing reporting did not show an acknowledgment; no licensing changes made.
- DHCP configured on `Gi0/0/0`. Physical link came up, DHCP lease received and gateway ping succeeded 5/5 (4/6/12 ms min/avg/max).
- `SANDLOCK-WAN-IN` inbound ACL present on Cisco upstream. Existing preliminary ACL permits DHCP replies, selected ICMP replies, and TCP packets with ACK/RST set, then denies other IPv4.
- OPNsense web UI reached over Dell wired, tagged VLAN 99 while Dell maintained separate Wi-Fi connectivity. Interface assignments positively identified `wan=igb1`, `lan=igb0`.
- In OPNsense WAN form: IPv4 changed from DHCP to Static IPv4, `10.255.255.2/30` entered, IPv6 changed from DHCPv6 to None, private-network blocking unchecked. **Saving/applying and gateway activation have not been confirmed by a later status screen.**

## Security considerations / open items

1. Cisco IOS XE 17.6 is out of support; plan supported upgrade or upstream protective architecture before treating the router as a long-term trusted security boundary.
2. The current inbound ACL is **stateless**, not a replacement for a firewall. It may drop legitimate UDP return traffic (e.g., DNS); ACK-flag matching can also allow spoofed traffic. Revisit policy before enabling broad egress.
3. Ensure OPNsense WAN gateway `10.255.255.1` is configured and default route verified; inspect current gateway entries before creating one.
4. Validate physical Cisco `Gi0/0/1` ↔ OPNsense `igb1` connection and link state, then ping in both directions with approved rule coverage.
5. Plan NAT/forwarding and intentionally **block SandLock-originated access to household LAN `192.168.1.0/24`**, except explicitly authorized paths. Check OPNsense outbound NAT and Cisco NAT design to avoid unnecessary layers.
6. Validate DNS, IPv4 return traffic, client internet egress, inter-VLAN policy, and management isolation before claiming cutover complete.
7. Back up OPNsense configuration securely offline and save Cisco startup configuration only after successful verification. **Do not publish raw configurations, secrets, logs, or license identifiers.**

## Next checkpoint

In OPNsense: review **System → Gateways → Configuration**; confirm or configure gateway `10.255.255.1` for `igb1`, and verify whether WAN form changes were applied. Next, physically connect Cisco `Gi0/0/1` to OPNsense `igb1`, check link status and test controlled routing. Preserve functioning VLAN 99 management access.
