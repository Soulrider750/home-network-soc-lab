# Portfolio Case Study — Planning Milestone

## Project Title

**Segmented Home Network and SOC Monitoring Lab**

## Status

**Planning and architecture complete; implementation and validation in progress.**

## Problem Statement

A flat home network places unrelated devices in the same trust boundary. Personal computers, guest devices, IoT systems, media clients, infrastructure management interfaces, and public-facing services may be able to communicate more broadly than necessary. This increases lateral-movement risk and makes monitoring and troubleshooting more difficult.

## Proposed Solution

The planned solution separates devices and services into VLANs based on trust level and function. OPNsense will control routing and enforce a default-deny firewall policy. TP-Link Omada equipment will provide wired and wireless segmentation. Public services will be available only through an NGINX HTTPS reverse proxy in the DMZ. Wazuh will centralize security logs, file integrity alerts, and validation evidence. An isolated Attack Lab VLAN will support controlled tests against approved lab targets.

## Planning Deliverables Completed

- Logical topology
- VLAN and subnet plan
- Static addressing recommendations
- OPNsense physical-interface and VLAN plan
- Switch trunk and access-port map
- Wireless SSID-to-VLAN plan
- Default-deny firewall rule matrix
- NucBox and Docker service architecture
- NGINX reverse-proxy and public-service plan
- Nextcloud and Jellyfin security requirements
- Wazuh log-source and file integrity monitoring plan
- Attack Lab safety rules and validation scenarios
- Hardening, backup, restore, and operations plan
- Portfolio evidence checklist

## Security Principles Applied

- Least privilege
- Default deny
- Network segmentation
- Defense in depth
- Reduced public attack surface
- Separation of management traffic
- Centralized monitoring
- Controlled validation
- Secure remote administration
- Backup and recovery planning

## Key Tradeoff

The planned environment uses one NucBox host for several services that logically belong to different zones. VLAN subinterfaces, Docker networks, host firewall rules, and OPNsense policy can reduce exposure, but this design is not equivalent to physical separation. The limitation is accepted for the initial home-lab build because of cost and simplicity, and a future version may separate workloads with virtual machines or dedicated hosts.

## Planned Validation Evidence

- Correct DHCP assignment by VLAN
- Blocked Guest and IoT access to internal networks
- Restricted administrator access to Management and SOC interfaces
- HTTPS-only public access through NGINX
- No direct public application or management ports
- OPNsense firewall deny logs
- Failed-authentication alerts
- Port-scan evidence
- File integrity monitoring alerts
- Docker event monitoring
- Backup restoration results

## Professional Relevance

The planning stage demonstrates the ability to translate a security problem into a documented technical architecture. The implementation stage will demonstrate network administration, firewall configuration, Linux and Docker deployment, reverse-proxy configuration, SIEM onboarding, detection validation, troubleshooting, and evidence-based reporting.
