# Command Pi — mission control

A Raspberry Pi planned as a lightweight dashboard for OPNsense, Cisco 2960X, Seneca/Suricata, PineTapPi, Proxmox VE/PBS, storage, VLAN health and security alerts.

Pre-staging completed October 7. Temporary `eth0` address 192.168.0.15/24 and `wlan0` 192.168.0.10/24; Tailscale was also configured. VLAN 99 profile staged but **not activated** until rack/switch integration. ASUS RT-N12 switched into AP mode but not fully validated.

Baseline backup created under `/var/backups/sandlock-command/`; SHA-256 verification completed successfully. Backup archive is not part of the repository.

Next: rack cable integration, management migration, service connectivity, monitoring dashboards and alert workflows.
