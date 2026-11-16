# Next steps

## Resume WAN work (equipment left powered on, WAN cable unplugged)
1. Identify which WAN cable is disconnected; verify expected physical topology before reconnecting.
2. Confirm Cisco startup config and live DHCP/ACL/default route after reconnecting; maintain console access.
3. Verify OPNsense VLAN 99 management, WAN gateway and outbound NAT mode.
4. Plan Cisco NAT and stateful or otherwise appropriate return-traffic protection; do not treat existing ACL as a stateful firewall.
5. Resolve OPNsense LAN `192.168.1.1/24` overlap with household `192.168.1.0/24` safely.
6. Validate household isolation, VLAN policies, DNS, public internet egress, and document evidence; securely back up firewall config.

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
