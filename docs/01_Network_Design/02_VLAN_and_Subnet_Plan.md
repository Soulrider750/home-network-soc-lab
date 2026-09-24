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

## September 23, 2026 observed state

Cleanup captures C05–C07 (`234511`, `234523`, and `234534`) still showed 13 switch VLAN records and the previously recorded PVIDs: 999 on Port 1, 10 on Port 2, 99 on Port 3, 40 on Port 4, 99 on Ports 5, 6, and 8–10, and 20 on Port 7. The membership views still showed Port 3 absent from VLAN 99 and Port 7 as a member of VLANs 10 and 20. These views do not prove tagged or untagged handling.

VLAN 999 remained visible only as a switch VLAN labeled `OPNsense` with Port 1 as its member. The evidence does not establish a routed VLAN 999 interface, subnet, gateway, or DHCP service, so none is added to the routed VLAN plan. Browser management remained visible at `10.10.10.1` for OPNsense, `10.10.10.2` for the switch, and `10.10.10.3` for the AP. These captures establish the addresses used for those sessions, not how the addresses were assigned or reserved. Cleanup capture C03 showed the `ADMIN_HOSTS` alias containing only `10.10.20.110`; it does not establish an exhaustive management-access policy review.

Cleanup status capture C08 (`234633`) showed the Trusted, Media, IoT, and Guest wireless roles still mapped to VLANs 20, 40, 50, and 60. It showed the AP's `Guest Network` feature enabled for the Guest role on both bands, but no guest peer-to-peer test was included. C08 reported the Attack Lab role's VLAN ID as `Disable`, while the earlier configuration capture showed VLAN 90. `Disable` in this field indicates that VLAN tagging was disabled; it does not prove that the SSID was off, and the latest broadcast toggle was not shown. VLAN 90 remains the intended plan unless the project owner changes it, while the current captured status differs. The documentation review made no additional live configuration changes and was not an exhaustive audit. See the [Stage 3 completion report](../00_Project_Overview/04_Stage_3_Completion_2026-09-23.md) for detailed evidence and limitations.

## Address inventory and proposed reservations

| Device/service | Address | Status |
|---|---|---|
| OPNsense Management gateway | 10.10.10.1 | Observed management address |
| Omada switch management | 10.10.10.2 | Observed management address |
| Omada AP management | 10.10.10.3 | Observed management address |
| NucBox hypervisor management | 10.10.10.20 | Proposed; not deployed |
| Omada Controller | 10.10.10.10 (proposed) | Proposed; not deployed |
| Media/internal-services VM | 10.10.30.20 | Proposed; not deployed |
| SOC VM | 10.10.70.10 | Proposed; not deployed |
| Nextcloud VM | 10.10.80.20 | Proposed; not deployed |
| Public-projects VM | 10.10.81.10 | Proposed; not deployed |

The first three management addresses were observed in browser sessions as described above; the captures do not establish their reservation method. The remaining entries are proposed future addresses, not deployed addresses. Confirm no conflicts before configuration. The public VM must not also receive a VLAN 80, 30, or 10 interface. Avoid VLAN 1 for production clients and keep VLAN 99 unrouted.
