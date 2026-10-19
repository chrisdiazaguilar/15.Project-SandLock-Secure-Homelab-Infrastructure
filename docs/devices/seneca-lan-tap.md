# Seneca Element LAN TAP

Debian 13.6 installed. Internal Intel I210 interfaces `enp2s0` (toward OPNsense igb2) and `enp3s0` (toward Cisco Gi1/0/1) form Linux bridge `br0`. No IP on members or bridge; no NAT. Suricata 7.0.10 configured for AF_PACKET on `enp2s0`; rules installed, configuration test passed, service enabled and running at recorded milestone.

## Permanent management
USB interface `enx8cae4cdd2eab` is permanently reserved for management and stays outside bridge `br0`. Approved Cisco port `Gi1/0/19` on VLAN 99. Earlier upstream management was 192.168.0.19/24 via 192.168.0.1; intended VLAN 99 static IP must be verified on the real device.

## Read-only checks
```bash
ip -br link
ip -br addr
bridge link
ip route
systemctl status suricata --no-pager
sudo suricata -T -c /etc/suricata/suricata.yaml
```

**Risk:** An inline software bridge is not a passive hardware bypass TAP: power failure can sever connectivity. Keep bypass cabling and recovery access ready.
