# TP-Link Omada Switch and AP Plan

## Core switch

**Planned switch:** TP-Link Omada SG3210XHP-M2 / TL-SG3210XHP-M2. Its managed ports support VLAN trunks, access ports, and PoE+ for the Omada BE5000 AP.

## Proposed port plan

| Switch port | Mode | VLANs | Device/purpose |
|---|---|---|---|
| Port 1 | Trunk | 10, 20, 30, 40, 50, 60, 70, 80, 81, 90 | OPNsense LAN trunk |
| Port 2 | Trunk + PoE+ | 10, 20, 40, 50, 60, 90 | Omada AP |
| Port 3 | Trunk | 10, 30, 70, 80, 81 | NucBox VLAN-aware hypervisor bridge |
| Port 4 | Access | 40 | PS5 |
| Port 5 | Access | 40 | Living-room TV/streaming device |
| Port 6 | Access | 40 | Bedroom TV/media device |
| Port 7 | Access | 20 or 30 | Trusted workstation or private server |
| Port 8 | Access | 90 | Wired Attack Lab device |
| SFP+ ports | Future | TBD | Expansion only after a documented need |

Port 3 carries tagged VM VLANs and the Management VLAN; the hypervisor management address belongs only on VLAN 10. Verify native/untagged VLAN behavior and allowed VLAN lists on both ends so no VM is accidentally placed into another zone. Disable unused ports or assign them to unrouted VLAN 99.

## Wireless SSIDs

| SSID | VLAN | Purpose |
|---|---:|---|
| Home-Trusted | 20 | Personal devices |
| Home-Media | 40 | TVs and streaming clients |
| Home-IoT | 50 | Smart devices |
| Home-Guest | 60 | Visitors |
| Home-Lab | 90 | Optional test clients |

Keep AP management on VLAN 10 and block wireless clients from that interface. Enable mDNS reflection only where a tested casting use case needs it.

## Omada Controller integration

The Omada Controller is planned in a dedicated NucBox management container on VLAN 10 for central switch/AP management. Reserve a controller address (proposed `10.10.10.10`) and restrict its UI to approved admin/VPN clients. Keep switch and AP management addresses on VLAN 10, and verify that the controller can reach their documented adoption and management services without a broad application-VLAN rule.

Back up standalone switch/AP settings before adoption. Adopt one device at a time, then verify Port 1 and Port 3 trunks, AP SSID mappings, client DHCP, and management reachability. Export a controller backup off its host and test restoration and device reconnection. Container compatibility and resource allocation still need validation; see the [Omada Controller plan](03_Omada_Controller_Plan.md).
