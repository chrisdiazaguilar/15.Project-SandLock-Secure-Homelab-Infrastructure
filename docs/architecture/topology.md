# Preferred Network Topology

```mermaid
flowchart TD
  Internet --> Router[Upstream router]
  Router --> FW[OPNsense]
  FW -->|igb2 / VLAN trunk| TAP[Seneca Element br0]
  TAP -->|enp3s0 / Gi1/0/1| SW[Cisco Catalyst 2960X]
  SW --> Servers[Proxmox VE / PBS / R320]
  SW --> Pi[Command Pi and PineTapPi]
  SW --> AP[ASUS RT-N12 AP]
  SW -->|Gi1/0/19 / access VLAN 99| USB[Seneca permanent USB Ethernet management]
  MarkVII[Hak5 Pineapple Mark VII] -->|USB Ethernet / authorized SSH capture| Pi
```

Seneca built-in `enp2s0` connects toward OPNsense `igb2`; `enp3s0` toward Cisco `Gi1/0/1`. Both are transparent members of bridge `br0`; USB management stays separate. This diagram is the intended layout, not proof that every cable is currently connected.
