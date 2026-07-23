# VLAN and Subnet Plan

The project uses a consistent `10.10.x.0/24` addressing pattern. Each VLAN receives its own gateway on OPNsense.

| VLAN | Name | Subnet | Gateway | DHCP | Purpose |
|---|---|---|---|---|---|
| 10 | Management | 10.10.10.0/24 | 10.10.10.1 | Limited/reserved | OPNsense, switch, AP management, optional Omada Controller |
| 20 | Trusted | 10.10.20.0/24 | 10.10.20.1 | Yes | MacBook, Lenovo laptop, Samsung S25 Ultra, admin workstation |
| 30 | Servers | 10.10.30.0/24 | 10.10.30.1 | Reserved/static | Jellyfin backend, internal services, databases, backups |
| 40 | Media | 10.10.40.0/24 | 10.10.40.1 | Yes | PS5, smart TVs, Roku/Fire TV, Meta Quest, media clients |
| 50 | IoT | 10.10.50.0/24 | 10.10.50.1 | Yes | Echo Dot, Shark vacuum, smart litterbox, smart home devices |
| 60 | Guest | 10.10.60.0/24 | 10.10.60.1 | Yes | Guest Wi-Fi, internet-only access |
| 70 | SOC | 10.10.70.0/24 | 10.10.70.1 | Reserved/static | Wazuh SIEM, dashboards, log collection |
| 80 | DMZ | 10.10.80.0/24 | 10.10.80.1 | Reserved/static | NGINX reverse proxy, public Nextcloud app path |
| 90 | Attack Lab | 10.10.90.0/24 | 10.10.90.1 | Yes or reserved | Kali/test VMs, vulnerable lab systems, controlled simulations |
| 99 | Parking/Unused | No routed subnet | None | No | Disabled or unused switch ports |

## Static Address Suggestions

| Device/Service | Suggested IP |
|---|---|
| OPNsense Management Gateway | 10.10.10.1 |
| SG3210XHP-M2 Management IP | 10.10.10.2 |
| Omada BE5000 Management IP | 10.10.10.3 |
| Omada Controller, if used | 10.10.10.10 |
| NucBox host, Server VLAN | 10.10.30.10 |
| Jellyfin Server | 10.10.30.20 |
| NGINX reverse proxy, DMZ | 10.10.80.10 |
| Nextcloud Server | 10.10.80.20 |
| Wazuh Dashboard | 10.10.70.10 |
| Kali Attack VM | 10.10.90.10 |
| Ubuntu test target | 10.10.90.20 |

## Notes

- Use DHCP reservations for important infrastructure if static addressing is not configured directly on the device.
- Avoid using VLAN 1 for production client traffic.
- Use VLAN 99 as a parking VLAN for unused switch ports.
- Keep management interfaces off guest, IoT, media, and attack lab networks.
