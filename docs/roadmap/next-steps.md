# Next steps

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
