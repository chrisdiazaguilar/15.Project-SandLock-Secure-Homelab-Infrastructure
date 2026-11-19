# Cisco Zone-Based Firewall (ZBF) activation — 2026-11-19

> **Scope:** Private, non-customer-facing SandLock homelab. One authorized remote user via private Tailscale access. No intentionally public-facing services or inbound port forwarding. This is a lab change record, not a certification of production security.

## Why ZBF replaced the inbound WAN ACL

The earlier stateless `SANDLOCK-WAN-IN` ingress ACL allowed DHCP and selected ICMP messages but ended in `deny ip any any log`. Cisco logs confirmed it blocked legitimate responses from public DNS and HTTPS endpoints. ICMP pings worked, but DNS queries and TCP 443 timed out from OPNsense.

A classic ACL matches packets independently. ZBF `inspect` maintains state for permitted outbound connections and allows matching return traffic. ACLs remain appropriate for narrowly scoped restrictions such as `ROUTER-SSH-MGMT`; ZBF is better suited here for stateful inside-to-outside inspection. ZBF is not universally superior: it adds configuration and protocol-inspection complexity and needs a tested self-zone/management design.

## Observed change and verified results

- Platform: Cisco ISR C1111-4P (`SL-RTR-01`), IOS XE 17.06.06a; software support/update eligibility remains a risk.
- The existing outbound policy `SL-ZBF-OUTBOUND` inspects `SL-ZBF-WEB-DNS` (DNS, HTTP, HTTPS); `class-default` drops unmatched interzone traffic.
- Existing zone pair: `SL-INSIDE-TO-OUTSIDE` from `SL-INSIDE` to `SL-OUTSIDE`.
- During controlled cutover, assigned OPNsense-facing `Gi0/0/1` to `SL-INSIDE`, ISP-facing `Gi0/0/0` to `SL-OUTSIDE`, and detached the conflicting `SANDLOCK-WAN-IN` ACL **from the interface**. The named ACL may still exist in configuration.
- NAT retained: overload using the ISP-facing interface; inside source ACL `SANDLOCK-NAT` limited to OPNsense WAN transit source.
- Scheduled a 30-minute reload safeguard, then canceled it following functional tests. `show reload` confirmed **no reload scheduled**. Lenovo USB console via `picocom` in `tmux` provided an independent management path.
- OPNsense firewall-originated tests: public DNS via `drill @1.1.1.1 example.com` succeeded; localhost Unbound via `drill @127.0.0.1 example.com` succeeded; `getent hosts example.com` resolved names; TCP connect to public port 443 succeeded; IPv4 HTTPS fetch `fetch -4 -o /dev/null https://example.com/` transferred 577 bytes.
- Non-forced `fetch` previously returned `Transient resolver failure` despite successful IPv4; IPv6 or address-selection issues remain to investigate, not conclusively diagnosed.
- Cisco live ZBF counters: 145 inspection sessions created, DNS packets 284, TCP packets 36; default class dropped 25 packets / 2,250 bytes. Cisco NAT had 18 active dynamic translations, 477 hits, with zero reported NAT drop counters in the observed snapshot. These are **point-in-time measurements**, not load or long-term security tests.
- Both VTY groups remained SSH-only with `ROUTER-SSH-MGMT` access-class. The dedicated management subnet was **not** verified for working SSH reachability in this cutover.

## Persistence status

**Confirmed after activation:** `copy running-config startup-config` returned `Building configuration... [OK]` on November 19, 2026. A subsequent `show startup-config | include zone-member security` readback confirmed both `SL-OUTSIDE` and `SL-INSIDE` entries, and `show reload` confirmed no scheduled reboot. Controlled reboot verification remains pending; do not claim reboot-tested persistence. Existing on-device pre-ZBF flash copy and an OPNsense configuration backup have separate evidence and should not be confused with GitHub backups.

## Remaining work before calling private homelab network production-ready

1. Confirm router management recovery path, unwanted ingress rejection, self-zone behavior and interzone rule handling.
2. Confirm IPv4 vs IPv6 hostname fetch behavior; decide IPv6 policy rather than assuming a DNS problem.
3. Review needed protocols beyond DNS/HTTP/HTTPS (NTP, ICMP diagnostics, firmware, VPN/control traffic), and explicitly permit only what is required.
4. Verify downstream VLAN client egress, OPNsense firewall/VLAN isolation and SandLock-to-household-network isolation. Resolve overlapping LAN subnet and DHCP/RA dependencies separately.
5. Verify software-update strategy for the unsupported IOS XE release; test fail-safe recovery, backup restore and monitoring.
6. Startup-config zone membership was verified; document a controlled reboot test (if/when appropriate). **Never publish raw running configs, secrets, or device backups to this public repository.**

## Safe operations note

A timed reload returns to startup configuration *only if startup has not been overwritten*. Remote access via tmux/USB console improves recovery options but does not guarantee recovery from reboot or hardware failures.
