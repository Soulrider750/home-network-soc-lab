# Planned Logical Topology

This diagram separates **where systems live** from **which routes are allowed**. It describes the proposed design; VLANs, VMs, the Omada Controller, and firewall rules still require implementation and testing.

![Planned home network zones and numbered routes](../../assets/diagrams/logical_topology_final.png)

## Zones and physical path

OPNsense on the Protectli is the gateway and default-deny firewall for routed VLANs. Its LAN trunk reaches the Omada switch. The switch connects the AP and carries VLANs 10, 30, 70, 80, and 81 to the NucBox's single Ethernet port. A VLAN-aware bridge tags each NucBox workload with its assigned VLAN; it must not route between them.

| Zone | Planned systems | Access boundary |
|---|---|---|
| VLAN 10 Management | OPNsense, Omada switch/AP, NucBox hypervisor, separate Omada Controller container | Approved administrator or VPN clients only |
| VLAN 81 Public-project DMZ | KEV dashboard, portfolio, NGINX, public-project `cloudflared` in one VM | Public sites through outbound tunnel |
| VLAN 80 Nextcloud app | Nextcloud app, database, cache in one VM | Approved Trusted/VPN clients only |
| VLAN 30 Servers | Jellyfin and reviewed private services in one VM | Approved Trusted/Media/VPN clients only |
| VLAN 70 SOC | Wazuh manager, indexer, dashboard in one VM | Specific log ingestion and restricted dashboard access |
| VLANs 20/40/50/60/90/99 | Trusted/Media clients, IoT, Guest, temporary Attack Lab, unused-port parking | Each has its own documented policy |

The Omada Controller belongs in a separate NucBox management container on VLAN 10. Its resource allocation still needs validation in the [controller plan](../02_OPNsense_and_Omada_Config/03_Omada_Controller_Plan.md); the container does not count as one of the four application VMs.

## Numbered routes

| Label | Planned path |
|---|---|
| R1 Public web | Visitor → Cloudflare HTTPS → established outbound tunnel → VLAN 81 NGINX → KEV or portfolio |
| R2 Private files | Approved Trusted/VPN client → OPNsense → VLAN 80 Nextcloud application port |
| R3 Private media | Approved Trusted/Media/VPN client → OPNsense → VLAN 30 Jellyfin application port |
| R4 Administration | Approved admin/VPN client → VLAN 10 infrastructure and separate Omada Controller container, or restricted VLAN 70 Wazuh dashboard |
| R5 Telemetry | OPNsense and monitored VMs → specific Wazuh ingestion endpoints on VLAN 70 |
| X Blocked | Direct WAN access to application/management ports; Public-project VM → personal or management zones; Guest/IoT/Attack Lab → internal zones by default |

The public VM initiates its Cloudflare Tunnel **outbound** across OPNsense and the Omada trunk; the public request returns through that established tunnel. No inbound WAN website port forward is planned. A VPN endpoint on OPNsense may need a separate narrowly scoped WAN rule.

Traffic between devices in the same VLAN or containers inside one VM can bypass OPNsense's inter-VLAN policy. Guest firewalls, container boundaries, and application authentication remain necessary. All four VMs and any NucBox-hosted controller still share one physical failure domain, so off-host backups and restore tests are part of the plan.
