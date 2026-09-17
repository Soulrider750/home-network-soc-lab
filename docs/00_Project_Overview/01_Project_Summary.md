# Project Summary

## Purpose

This project plans a segmented home network and SOC monitoring lab that supports normal home use and public portfolio projects. The network separates personal devices, media clients, guests, IoT devices, management interfaces, public sites, private applications, SOC tools, and attack-testing systems by trust level.

## Goals

1. Build the network with OPNsense, a managed Omada switch, and VLAN-mapped wireless SSIDs.
2. Use default-deny routing between security zones.
3. Run four persistent VMs on the NucBox: Public projects, Nextcloud, Media/internal services, and SOC.
4. Host the CISA KEV dashboard and personal portfolio in the Public-projects VM on VLAN 81, reached through its own outbound Cloudflare Tunnel.
5. Keep Nextcloud on VLAN 80 and Jellyfin on VLAN 30 private to approved local/VPN users.
6. Centralize logs and controlled detection evidence in Wazuh on VLAN 70.
7. Document implementation, validation, backup restoration, and lessons learned without presenting planned work as deployed.

## Planned architecture

| Layer | Responsibility |
|---|---|
| OPNsense on Protectli | WAN, VLAN gateways, default-deny inter-VLAN firewall, VPN, DNS/DHCP, and logging |
| Omada switch and AP | Physical VLAN trunk, access ports, PoE, and VLAN-mapped SSIDs |
| NucBox hypervisor | VLAN-aware bridge, four isolated application VMs, management on VLAN 10 |
| Public-projects VM, VLAN 81 | KEV dashboard, portfolio, NGINX, and public-project tunnel connector |
| Nextcloud VM, VLAN 80 | Nextcloud, database, cache, and private access path |
| Media/internal-services VM, VLAN 30 | Jellyfin and reviewed future private services |
| SOC VM, VLAN 70 | Wazuh manager, indexer, dashboard, and supporting components |

Docker Compose is planned inside the application VMs. The optional Omada Controller needs a separate Management-zone placement decision; it is not assigned to a public or personal-data VM by default. Attack Lab test VMs are temporary resources outside the four persistent NucBox VMs.

## Current boundary

This repository is planning documentation. The revised four-VM design and migration are not deployed or validated by this update. The KEV dashboard already has a separate live Ubuntu deployment; its move to the NucBox must follow a verified cutover. The NucBox model, RAM, and storage are still to be confirmed before resource allocation.
