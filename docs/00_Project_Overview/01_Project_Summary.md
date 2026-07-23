# Project Summary

## Purpose

This project builds a segmented home network and SOC monitoring lab that supports normal home use while also serving as a serious IT and cybersecurity portfolio project.

The network separates devices by trust level and function. Personal devices, IoT devices, guests, media devices, management interfaces, public-facing services, internal services, SOC monitoring tools, and attack testing systems each receive their own VLAN and firewall policy.

## Goals

1. Build a functional home network using professional design concepts.
2. Implement VLAN segmentation with OPNsense and a managed Omada switch.
3. Map multiple wireless SSIDs to VLANs through the Omada BE5000 AP.
4. Host Nextcloud Server, Jellyfin Server, Wazuh, and NGINX on the NucBox with Docker.
5. Expose Nextcloud and Jellyfin safely through HTTPS reverse proxy instead of direct application port forwards.
6. Centralize logs and detection evidence in Wazuh.
7. Run controlled attack simulations from an isolated Attack Lab VLAN.
8. Package the project into a portfolio-ready case study.

## Why This Project Matters

A normal home network is usually flat. That means a compromised IoT device, guest device, or media device may be able to reach personal laptops, phones, file shares, or server services. This project reduces that risk by separating the network into trust zones and only allowing necessary traffic.

For IT roles, the project demonstrates network design, subnetting, VLANs, DHCP, DNS, managed switching, wireless segmentation, Docker hosting, and troubleshooting.

For cybersecurity roles, the project demonstrates firewall policy, segmentation, DMZ design, public service hardening, log collection, Wazuh SIEM use, simulated attack detection, and incident documentation.

## Final Architecture Summary

- OPNsense handles WAN access, VLAN gateways, DHCP, DNS forwarding, NAT, firewall rules, VPN, and logging.
- The TP-Link Omada SG3210XHP-M2 acts as the core managed switch.
- The TP-Link Omada BE5000 provides VLAN-mapped SSIDs.
- The NucBox hosts Docker services on trunked VLANs or carefully separated Docker networks.
- NGINX handles public HTTPS reverse proxy for Nextcloud and Jellyfin.
- Wazuh collects security logs and provides SOC evidence.
- VLAN 90 provides an isolated space for simulated attacks and controlled testing.
