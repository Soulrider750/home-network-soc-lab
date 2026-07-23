# Wazuh SOC Monitoring Plan

## Purpose

Wazuh provides the SOC monitoring layer for this project. It should collect logs, generate alerts, support file integrity monitoring, and provide screenshots for the portfolio case study.

## Placement

| Component | Zone |
|---|---|
| Wazuh Manager | VLAN 70 SOC |
| Wazuh Indexer | VLAN 70 SOC |
| Wazuh Dashboard | VLAN 70 SOC |
| Wazuh Agent on NucBox | NucBox host |
| Optional endpoint agents | Trusted devices, where practical |
| Syslog inputs | OPNsense and network/service logs |

## Log Sources

| Source | Events to Capture |
|---|---|
| OPNsense | Firewall blocks, WAN allows, inter-VLAN denied traffic |
| NGINX | Access logs, error logs, suspicious requests, auth-heavy paths |
| Nextcloud | Failed logins, admin events, application errors |
| Jellyfin | Failed logins, remote sessions, application errors |
| NucBox Linux host | SSH attempts, sudo usage, package changes, system logs |
| Docker | Container starts/stops/restarts, service errors |
| Wazuh FIM | Changes to key config directories |
| Attack Lab | Test activity notes and generated alerts |

## File Integrity Monitoring Targets

| Path | Purpose |
|---|---|
| `/etc/` | System configuration changes |
| `/srv/docker/nginx/` | Reverse proxy configuration |
| `/srv/docker/nextcloud/` | Nextcloud Compose/config files |
| `/srv/docker/jellyfin/` | Jellyfin Compose/config files |
| `/srv/docker/wazuh/` | SIEM configuration |
| Backup manifest/checksum files | Backup integrity evidence |

## Dashboards and Evidence

Capture sanitized screenshots of:

- Wazuh dashboard overview.
- Failed SSH login alert.
- File integrity alert.
- Docker container event.
- NGINX suspicious request event.
- Nextcloud failed login event.
- Jellyfin failed login event.
- OPNsense blocked inter-VLAN traffic.

## Suggested Alert Categories

| Alert Category | Portfolio Value |
|---|---|
| Authentication failures | SOC analyst basics |
| Port scanning | Reconnaissance detection |
| Firewall blocks | Segmentation validation |
| Public service login failures | Web service monitoring |
| File changes | FIM and host monitoring |
| Docker events | Container visibility |
| Admin actions | Privilege/account monitoring |
