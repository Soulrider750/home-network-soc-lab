# Portfolio Case Study — Revised Planning Milestone

## Project title and status

**Segmented Home Network and SOC Monitoring Lab.** The repository documents a revised target architecture; implementation and validation of the four NucBox VMs remain pending. The separately hosted KEV dashboard is already live, but its NucBox migration is future work.

## Problem and proposed solution

A flat home network gives unrelated devices and services more access to each other than they need. This design uses OPNsense and Omada VLANs to separate personal devices, public projects, private applications, monitoring, and controlled test systems.

The NucBox will run four persistent VMs: Public projects on VLAN 81 (KEV dashboard, personal portfolio, NGINX, Cloudflare Tunnel connector); Nextcloud on VLAN 80; Media/internal services on VLAN 30 (Jellyfin); and SOC on VLAN 70 (Wazuh). Docker is planned inside the application VMs. A VLAN-aware bridge and switch trunk connect them, while OPNsense enforces default-deny inter-VLAN rules. The hypervisor management interface stays on VLAN 10.

Public sites use an outbound Cloudflare Tunnel; Nextcloud and Jellyfin use approved local/VPN access. Wazuh and infrastructure administration remain private. The Omada Controller is planned in a separate NucBox management container on VLAN 10; its capacity and compatibility remain to be validated.

## Planning deliverables

- Four-VM service placement and VLAN 81 public-project zone.
- Revised OPNsense, Omada trunk/controller, firewall, DNS, and access plans.
- Separate public-tunnel, Nextcloud, Jellyfin, and Wazuh plans.
- Build phases, controlled tests, backup and restore requirements.
- Capacity and migration acceptance criteria.

## Key tradeoff and validation

VMs and VLANs give meaningful logical separation, while all four VMs still share a physical NucBox, its storage, and failure risk. The owner-reported Intel Core i9-13900HK, 32 GB DDR5 RAM, and 1 TB SSD are the planning baseline; the exact SKU, usable capacity, and workload headroom remain to be validated. The design does not claim that a VM bridge or Docker network automatically sends same-zone traffic through OPNsense.

Implementation evidence should include Omada adoption and controller recovery, VLAN placement, denied public-to-private traffic, private VPN access, public tunnel responses, application and Wazuh logs, measured resource use, and off-host restore tests. The KEV cutover requires separate verified operational evidence before the existing origin is retired.

This document describes planning skills. VM deployment, firewall effectiveness, recovery, and detection outcomes become demonstrated skills only after testing and evidence capture.
