# Hardening, Backup, and Operations Plan

## Infrastructure Hardening

| Area | Control |
|---|---|
| OPNsense | Disable WAN management, strong admin password, restrict GUI to Management VLAN, regular config backups |
| Switch | Management VLAN only, disable unused ports, VLAN 99 for parking, update firmware |
| AP | Management VLAN only, strong admin credentials, separate SSIDs, guest isolation |
| NucBox | SSH keys, restricted admin access, host firewall, regular updates, Wazuh agent |
| Docker | Separate Compose projects, least exposed ports, no public Docker socket, backups of Compose files |
| Nextcloud | MFA, trusted domains/proxies, updates, backups, limited apps |
| Jellyfin | Strong accounts, no direct WAN exposure, admin access restricted |
| NGINX | HTTPS, logs, rate limits, security headers, no unnecessary exposed paths |
| Wazuh | Admin access restricted, dashboards not public, monitored storage capacity |

## Backup Plan

| Asset | Backup Method | Frequency |
|---|---|---|
| OPNsense config | Export encrypted config backup | After major changes and monthly |
| Switch/AP config | Export Omada backup | After major changes and monthly |
| Docker Compose files | Private repo or backup folder | After changes |
| Nextcloud database | Database dump | Daily or weekly |
| Nextcloud data | File backup to external drive/NAS | Daily or weekly |
| Jellyfin config | Config backup | Weekly |
| Jellyfin media | External drive/NAS backup | Based on change rate |
| Wazuh config | Config backup | Weekly/monthly |
| Important screenshots/docs | Portfolio backup | After each lab phase |

## Recovery Tests

A backup plan is only useful if restore is tested. Document at least one restore test for:

- OPNsense config import.
- Nextcloud database and data restore.
- Jellyfin config restore.
- Docker Compose redeployment.

## Operational Routine

| Task | Cadence |
|---|---|
| Review Wazuh alerts | Weekly during project phase |
| Review OPNsense firewall logs | Weekly |
| Check backups | Weekly |
| Apply OS/container updates | Monthly or as needed |
| Export config backups | After major changes |
| Capture portfolio screenshots | After each detection scenario |
| Review firewall rules | Monthly or after topology changes |

## Future Improvements

1. Add a UPS for modem, firewall, switch, AP, and NucBox.
2. Add external storage or NAS for more reliable backups.
3. Add WireGuard VPN for secure remote administration.
4. Add Suricata on OPNsense after baseline rules are stable.
5. Add Zeek or a dedicated IDS sensor using switch port mirroring.
6. Add a USB/Thunderbolt 2.5GbE adapter or future host with multiple NICs for stronger service separation.
7. Consider Proxmox later if you want stronger isolation between Wazuh, DMZ, and server workloads.
8. Add CrowdSec or Fail2Ban-style protection for public service logs.
9. Add uptime monitoring with Uptime Kuma.
10. Add asset inventory and vulnerability scan reports.
