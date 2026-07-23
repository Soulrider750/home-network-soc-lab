# NucBox Docker Host Plan

## Hardware

GMKtec NucBox Mini PC Workstation:

- Intel i9-13900HK
- 32GB DDR5 RAM
- 1TB SSD
- One Ethernet port

## Role

The NucBox is the central service host for:

- NGINX reverse proxy
- Nextcloud Server
- Jellyfin Server
- Wazuh SIEM
- Optional Omada Controller

## Network Challenge

The NucBox has one Ethernet port, but the project places services into different trust zones. The switch port connected to the NucBox should therefore be configured as a trunk.

## Recommended Advanced Approach

Use Linux VLAN subinterfaces and Docker networks.

Example VLAN-facing design:

| VLAN | Host Interface Concept | Service |
|---|---|---|
| VLAN 30 | `eth0.30` | Jellyfin Server, internal services, storage/backups |
| VLAN 70 | `eth0.70` | Wazuh SIEM |
| VLAN 80 | `eth0.80` | NGINX reverse proxy and Nextcloud Server |
| VLAN 10 | `eth0.10`, optional | Omada Controller management path |

Docker can then bind published services to the appropriate VLAN IPs, or use macvlan/ipvlan-style networking where appropriate.

## Simpler Acceptable Approach

Keep the NucBox host primarily in the Server VLAN, use separate Docker networks, bind exposed ports carefully, and enforce access with OPNsense and the host firewall. This is easier, but it should be documented as a tradeoff because Docker network separation on one host is not the same as fully separate physical servers.

## Recommended Host Hardening

- Use a minimal Linux server OS.
- Restrict SSH to admin devices.
- Use SSH keys and disable password-based SSH if practical.
- Enable a host firewall.
- Keep Docker and OS packages updated.
- Store Docker Compose files in a private repository or backed-up folder.
- Use separate Compose projects for NGINX, Nextcloud, Jellyfin, Wazuh, and Omada Controller.
- Back up config files, databases, and media/cloud data.
- Install the Wazuh agent on the NucBox host.
- Monitor Docker logs and security-relevant directories.

## Recommended Directory Structure on Host

```text
/srv/docker/
├── nginx/
├── nextcloud/
├── jellyfin/
├── wazuh/
├── omada-controller/
└── backups/
```

## Important Tradeoff

Because Wazuh, Nextcloud, Jellyfin, and NGINX are on one physical host, compromise of the NucBox host could affect multiple services. This is acceptable for a home lab if documented clearly, but the future enterprise-style upgrade would be to split services across VMs or separate hosts.
