# PineTapPi — wireless capture

Raspberry Pi 3 Model B formerly hosted RaspAPI (Node/Express job API); repurposed as PineTapPi, Debian 13.7. During staging eth0 received 192.168.0.14/24, gateway 192.168.0.1. Self-powered 1 TB EXT4 USB disk mounts at `/mnt/pinecap`.

One Hak5 WiFi Pineapple Mark VII is connected over USB Ethernet with separate power. The Pi receives authorized raw capture via SSH from `wlan1mon` and rotates local files under `/mnt/pinecap/pineapple/wireless.pcap*`: 250 MB per file, maximum 400. On October 4, `pinetap-wireless.service` was confirmed active.

## Authentication fix
The Mark VII used OpenSSH `sshd` and root's `/root/.ssh/authorized_keys`, not Dropbear. An initial key had an incompatible passphrase for unattended service use. A dedicated passphrase-free RSA key enabled noninteractive capture. Never publish the key, restrict access, and consider command-restricted credentials.

## Validation
```bash
hostnamectl
systemctl status pinetap-wireless.service --no-pager
findmnt /mnt/pinecap
ls -lh /mnt/pinecap/pineapple/
```

Only capture traffic from networks/devices where authorization exists. Do not commit PCAPs.
