# OPNsense Interface and VLAN Plan

## Hardware and responsibilities

The planned firewall is a Protectli Vault V1410 running OPNsense. It supplies WAN connectivity, VLAN gateways, DHCP/DNS, default-deny inter-VLAN policy, VPN access, and logs to Wazuh. The NucBox hypervisor and switch do not route between zones.

| Protectli port | Planned role |
|---|---|
| Port 1 | ISP/WAN |
| Port 2 | Trunk to Omada switch; VLANs 10–90 including new VLAN 81 |
| Port 3 | Optional emergency maintenance path |
| Port 4 | Future use |

## Routed VLANs

| Interface | VLAN | Purpose |
|---|---:|---|
| MGMT | 10 | Infrastructure and hypervisor administration |
| TRUSTED | 20 | Personal and approved admin devices |
| SERVERS | 30 | Jellyfin and private services |
| MEDIA | 40 | Media clients |
| IOT | 50 | Smart-home devices |
| GUEST | 60 | Visitors |
| SOC | 70 | Wazuh |
| NEXTCLOUD | 80 | Private Nextcloud application zone |
| PUBLIC_PROJECTS | 81 | KEV dashboard and portfolio tunnel origin |
| ATTACKLAB | 90 | Controlled test systems |

Create each interface and gateway on the OPNsense LAN trunk. Validate DHCP/reservations, DNS, outbound updates, and default-deny behavior from each zone. VLAN 99 remains an unrouted switch parking VLAN.

## WAN and remote access

The KEV dashboard and portfolio use an outbound Cloudflare Tunnel from VLAN 81, so they need no public WAN port forward or static public IP. Nextcloud and Jellyfin are private local/VPN services. Do not forward their application ports, Wazuh, Docker, SSH, hypervisor, OPNsense GUI, switch, or AP management to WAN.

Plan WireGuard or another approved VPN on OPNsense for private remote access. Scope VPN clients to the Nextcloud, Jellyfin, and administrative paths they actually need. A VPN endpoint may need its own narrowly scoped WAN rule; confirm its protocol, port, and authentication design during implementation.

## DNS and logging

Use public DNS only for the two approved public-site hostnames mapped to the public-project tunnel. Resolve private Nextcloud and Jellyfin names through internal DNS or VPN DNS; do not assume a public hostname grants access control. Forward OPNsense firewall and VPN events to the Wazuh ingestion endpoint through a specific rule.

Export the OPNsense configuration after baseline setup and major policy changes, then test recovery.
