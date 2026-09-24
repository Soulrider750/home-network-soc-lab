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

## Wireless roles

The public documentation uses functional roles instead of the household SSID names.

| Wireless role | VLAN | Purpose |
|---|---:|---|
| Trusted | 20 | Personal devices |
| Media | 40 | TVs and streaming clients |
| IoT | 50 | Smart devices |
| Guest | 60 | Visitors |
| Attack Lab | 90 | Optional test clients |

Keep AP management on VLAN 10 and block wireless clients from that interface. Enable mDNS reflection only where a tested casting use case needs it.

## September 23, 2026 observed state

Cleanup captures C05–C07 (`234511`, `234523`, and `234534`) repeat the switch state recorded earlier: 13 VLAN records and the following unchanged PVIDs. This is process evidence rather than a live configuration audit, and it does not replace the proposed future port plan above.

| Port | Observed PVID |
|---|---:|
| Port 1 | 999 |
| Port 2 | 10 |
| Port 3 | 99 |
| Port 4 | 40 |
| Ports 5, 6, 8, 9, and 10 | 99 |
| Port 7 | 20 |

The cleanup VLAN membership views still showed Port 3 absent from VLAN 99 and Port 7 as a member of both VLAN 10 and VLAN 20. Those views establish membership only; they do not prove whether each membership is tagged or untagged. VLAN 999 remained labeled `OPNsense` with Port 1 as its only member. The captures do not establish a routed interface, gateway, or subnet for VLAN 999.

Earlier wireless configuration captures showed the Trusted role mapped to VLAN 20, Media to VLAN 40, IoT to VLAN 50, Guest to VLAN 60, and Attack Lab to VLAN 90. Cleanup status capture C08 (`234633`) still showed the first four mappings and showed the AP's `Guest Network` feature enabled for the Guest role on both bands. No guest peer-to-peer test was shown, so the capture does not prove same-VLAN client isolation.

C08 reported the Attack Lab role's VLAN ID as `Disable`, which differs from both the earlier VLAN 90 capture and the intended VLAN 90 plan above. This indicates that VLAN tagging was disabled in that status view; it does not prove the SSID itself was off. The latest capture does not show the broadcast toggle, so it also does not establish whether that SSID remained hidden. VLAN 90 remains the intended plan unless the project owner changes it. The documentation review made no additional live configuration changes and was not an exhaustive audit. See the [Stage 3 completion report](../00_Project_Overview/04_Stage_3_Completion_2026-09-23.md) for the evidence history and limitations.

## Omada Controller integration

The Omada Controller is planned in a dedicated NucBox management container on VLAN 10 for central switch/AP management. Reserve a controller address (proposed `10.10.10.10`) and restrict its UI to approved admin/VPN clients. Keep switch and AP management addresses on VLAN 10, and verify that the controller can reach their documented adoption and management services without a broad application-VLAN rule.

Back up standalone switch/AP settings before adoption. Adopt one device at a time, then verify Port 1 and Port 3 trunks, AP SSID mappings, client DHCP, and management reachability. Export a controller backup off its host and test restoration and device reconnection. Container compatibility and resource allocation still need validation; see the [Omada Controller plan](03_Omada_Controller_Plan.md).
