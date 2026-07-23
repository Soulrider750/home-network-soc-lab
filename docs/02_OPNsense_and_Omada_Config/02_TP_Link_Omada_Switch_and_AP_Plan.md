# TP-Link Omada Switch and AP Plan

## Core Switch

**Final switch:** TP-Link Omada SG3210XHP-M2 / TL-SG3210XHP-M2.

## Why This Switch Fits

- 8 x 2.5GbE PoE+ ports support the Omada BE5000 access point and future multigig devices.
- 2 x 10G SFP+ uplinks provide future expansion for NAS, uplinks, or another switch.
- Omada management keeps the switch and AP in the same ecosystem.
- VLAN, ACL, QoS, IGMP snooping, and monitoring-friendly features support the lab design.
- PoE+ reduces cable clutter by powering the AP directly from the switch.

## Switch Port Plan

| Switch Port | Mode | VLANs | Device/Purpose |
|---|---|---|---|
| Port 1 | Trunk | 10,20,30,40,50,60,70,80,90 | OPNsense LAN trunk |
| Port 2 | Trunk + PoE+ | 10,20,40,50,60,90 | Omada BE5000 AP |
| Port 3 | Trunk | 10 optional, 30,70,80 | NucBox Docker host |
| Port 4 | Access | 40 | PS5 |
| Port 5 | Access | 40 | Living room TV or streaming device |
| Port 6 | Access | 40 | Bedroom TV or media device |
| Port 7 | Access | 20 or 30 | Trusted workstation or internal server device |
| Port 8 | Access | 90 | Wired attack lab/test machine |
| SFP+ 1 | Future trunk/uplink | TBD | NAS, future switch, or high-speed host |
| SFP+ 2 | Future trunk/uplink | TBD | Reserved expansion |

## Unused Ports

Unused ports should be disabled or placed in VLAN 99 with no DHCP and no internal routing.

## Wireless SSID Plan

| SSID | VLAN | Purpose |
|---|---|---|
| Home-Trusted | 20 | MacBook, Lenovo laptop, Samsung phone |
| Home-Media | 40 | Smart TVs, Roku/Fire TV, media clients |
| Home-IoT | 50 | Echo Dot, Shark vacuum, smart litterbox |
| Home-Guest | 60 | Visitors and temporary devices |
| Home-Lab | 90 | Optional wireless lab/testing devices |

## AP Management

The AP management interface should live in VLAN 10. Wireless clients should not be able to access VLAN 10.

## Omada Controller

The Omada Controller can be run as a container on the NucBox or installed on a separate management device. If it runs on the NucBox, place it in the Management VLAN path and restrict access to approved admin devices.

## mDNS and Casting

Casting and device discovery may break when Trusted and Media devices are separated. Only enable mDNS reflection/repeating for the minimum VLANs and services needed. Do not broadly open Media-to-Trusted access.
