# Cisco Catalyst WS-C2960X-24PS-L

Primary trunk `Gi1/0/1` carries VLANs 10,20,30,40,50,60,70,80,99, and connects through Seneca `enp3s0` and `br0` toward OPNsense.

| Port | VLAN / role |
|---|---|
| Gi1/0/1 | 802.1Q trunk |
| Gi1/0/6 | VLAN 20 test |
| Gi1/0/7 | VLAN 30 STORAGE |
| Gi1/0/8 | VLAN 40 WIFI-USERS |
| Gi1/0/9 | VLAN 50 SECURITY |
| Gi1/0/10 | VLAN 60 IOT |
| Gi1/0/19 | Permanent Seneca USB Ethernet management, access VLAN 99 |

Assignments reflect past testing and approved plans; audit live configuration after rack assembly.

## Read-only CLI checks
```text
show vlan brief
show interfaces trunk
show interfaces status
show running-config interface gigabitEthernet1/0/1
show running-config interface gigabitEthernet1/0/19
```
