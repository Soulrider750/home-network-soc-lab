# VLAN and Subnet Plan

The plan uses `10.10.x.0/24` addressing. OPNsense supplies each routed VLAN's gateway. VLAN 81 is the separate public-project zone approved for the KEV dashboard and portfolio.

| VLAN | Name | Subnet | Gateway | DHCP | Purpose |
|---|---|---|---|---|---|
| 10 | Management | 10.10.10.0/24 | 10.10.10.1 | Limited/reserved | OPNsense, switch, AP, hypervisor, Omada Controller |
| 20 | Trusted | 10.10.20.0/24 | 10.10.20.1 | Yes | Personal and approved admin devices |
| 30 | Servers | 10.10.30.0/24 | 10.10.30.1 | Reserved/static | Media/internal-services VM, Jellyfin, private services |
| 40 | Media | 10.10.40.0/24 | 10.10.40.1 | Yes | TVs, streaming devices, consoles, media clients |
| 50 | IoT | 10.10.50.0/24 | 10.10.50.1 | Yes | Smart-home devices |
| 60 | Guest | 10.10.60.0/24 | 10.10.60.1 | Yes | Internet-only visitor access |
| 70 | SOC | 10.10.70.0/24 | 10.10.70.1 | Reserved/static | SOC VM and Wazuh ingestion/dashboard |
| 80 | Nextcloud app zone | 10.10.80.0/24 | 10.10.80.1 | Reserved/static | Private Nextcloud VM, separate from public projects |
| 81 | Public-project DMZ | 10.10.81.0/24 | 10.10.81.1 | Reserved/static | Public-projects VM, KEV, portfolio, NGINX, tunnel connector |
| 90 | Attack Lab | 10.10.90.0/24 | 10.10.90.1 | Yes or reserved | Temporary test systems and approved lab targets |
| 99 | Parking/Unused | No routed subnet | None | No | Disabled or unused switch ports |

## Proposed address reservations

| Device/service | Suggested address |
|---|---|
| OPNsense Management gateway | 10.10.10.1 |
| Omada switch management | 10.10.10.2 |
| Omada AP management | 10.10.10.3 |
| NucBox hypervisor management | 10.10.10.20 |
| Omada Controller | 10.10.10.10 (proposed) |
| Media/internal-services VM | 10.10.30.20 |
| SOC VM | 10.10.70.10 |
| Nextcloud VM | 10.10.80.20 |
| Public-projects VM | 10.10.81.10 |

These are proposed reservations, not deployed addresses. Confirm no conflicts before configuration. The public VM must not also receive a VLAN 80, 30, or 10 interface. Avoid VLAN 1 for production clients and keep VLAN 99 unrouted.
