# Firewall Rule Matrix

## Core policy

Use default deny between VLANs. OPNsense is the inter-VLAN gateway. Replace broad zone-to-zone allows with source, destination, protocol, and port rules after each service endpoint is confirmed. No application VM should route between VLANs.

## WAN and tunnel policy

| Source | Destination | Planned action | Purpose |
|---|---|---|---|
| Internet | KEV, portfolio, Nextcloud, Jellyfin, Wazuh, hypervisor, or management ports | Block inbound | No direct application or management WAN forwards |
| Internet | OPNsense VPN endpoint | Allow only if configured | Authenticated private remote access |
| Public-projects VM, VLAN 81 | Cloudflare documented tunnel endpoints | Allow outbound UDP/TCP 7844 | QUIC/HTTP2 connector traffic |
| Public-projects VM, VLAN 81 | Approved DNS, CISA feed, and software-update destinations | Narrow outbound allow | Resolve, refresh, and patch public sites |
| Other application VMs | Internet | Narrow outbound allow as needed | Updates and documented dependencies |

Cloudflare's required tunnel destinations can change; verify its current firewall documentation when implementing the rules. Tunnel publication requires no static public IP or inbound WAN port forward for the two public sites.

## Inter-VLAN rules

| Source | Destination | Planned action | Purpose |
|---|---|---|---|
| Approved admin or VPN client | Management VLAN 10 | Allow selected admin ports | OPNsense, switch, AP, hypervisor, optional controller |
| Approved admin or VPN client | SOC VM, VLAN 70 | Allow dashboard/admin ports | Review alerts |
| Approved Trusted or VPN client | Nextcloud VM, VLAN 80 | Allow application port only | Private file access |
| Approved Trusted, Media, or VPN client | Jellyfin VM, VLAN 30 | Allow application port only | Private playback |
| Media VLAN 40 | Other Server VLAN services and Trusted VLAN | Block | Limit media-client reach |
| Guest and IoT VLANs | Internal zones | Block by default | Prevent lateral access |
| Public-projects VM, VLAN 81 | Nextcloud, Server, Trusted, Management, SOC dashboard | Block | Preserve public/personal boundary |
| Nextcloud VM, VLAN 80 | Public projects, Trusted, Management | Block by default | Prevent lateral access |
| Monitored hosts/services | Wazuh ingestion endpoint, VLAN 70 | Allow specific agent/syslog ports | Centralized monitoring |
| Attack Lab VLAN 90 | Internal zones | Block by default | Prevent accidental exposure |
| Attack Lab VLAN 90 | Approved test target | Temporary allow | Controlled exercise |
| Any | VLAN 99 | Block | Parking network |

Allow response traffic through stateful rules. A required cross-zone dependency must be documented as an exact exception and tested both ways; a broad public-DMZ-to-Servers rule is not part of this plan. Traffic inside one VLAN or one VM may not traverse OPNsense and needs guest/container controls too.

## Suggested aliases

| Alias | Members |
|---|---|
| ADMIN_DEVICES | Approved administrator device/VPN addresses |
| PUBLIC_PROJECTS_VM | Public VM address on VLAN 81 |
| NEXTCLOUD_VM | Nextcloud VM address on VLAN 80 |
| MEDIA_SERVICES_VM | Media/internal-services VM address on VLAN 30 |
| WAZUH_INGEST | Wazuh manager ingestion addresses/ports |
| ATTACK_LAB_TEST_TARGETS | Explicitly approved lab targets |
