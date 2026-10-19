# VLAN and addressing reference

| VLAN | Name / status | Gateway |
|---|---|---|
| 10 | Defined; label to confirm | Verify |
| 20 | Test / validation | Verify |
| 30 | STORAGE | Verify |
| 40 | WIFI-USERS | Verify |
| 50 | SECURITY | Verify |
| 60 | IOT | Verify |
| 70 | Defined; label to confirm | Verify |
| 80 | Defined; label to confirm | Verify |
| 99 | MANAGEMENT | OPNsense 192.168.99.1/24; DHCP configured |

The older upstream management network was 192.168.0.0/24. Do not confuse temporary upstream DHCP addresses with final VLAN 99 addresses. Verify all other subnets, scopes, DHCP and firewall policies live rather than guessing.
