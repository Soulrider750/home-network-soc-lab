# Hardening, Backup, and Operations Plan

## Infrastructure hardening

| Area | Planned control |
|---|---|
| OPNsense | Restrict GUI to approved Management/VPN clients; default-deny inter-VLAN rules; export configuration |
| Omada switch/AP | Management VLAN 10; controlled trunks; disabled/parked unused ports; firmware updates |
| NucBox hypervisor | Management VLAN 10 only; restricted admin access; updates; VLAN-aware bridge; no inter-VLAN routing |
| Four guest VMs | Minimal guest OS; distinct credentials, disks, Compose projects, patching, and guest firewalls |
| Public-projects VM | No personal-data mounts; protected tunnel credential; NGINX/app logs; no inbound WAN forward |
| Nextcloud VM | MFA, trusted domains/proxies when relevant, private access, application-consistent backup |
| Media/internal-services VM | Private Jellyfin access; approved media mounts; restricted admin interface |
| SOC VM | Private Wazuh dashboard; bounded log/index storage; monitored ingestion rules |

The optional Omada Controller needs a separate Management-zone placement decision. Keep VPN, hypervisor, Docker, OPNsense, and Wazuh administration off public hostnames.

## Backup plan

| Asset | Planned recovery copy |
|---|---|
| OPNsense and Omada configuration | Export after changes and verify import/recovery path |
| Hypervisor configuration and VM definitions | Off-host copy sufficient to rebuild VLAN-aware bridge and guests |
| Public-projects VM | Versioned deployment config, KEV data, portfolio files, tunnel configuration/credential held separately, and tested restore |
| Nextcloud VM | Coordinated app files, user data, configuration, database, and cache rebuild procedure |
| Media/internal-services VM | Jellyfin config/metadata and chosen media scope |
| SOC VM | Wazuh configuration, rules, and retention/rebuild plan; size log backups deliberately |
| Documentation and sanitized evidence | Versioned repository plus separate recovery copy |

Use a separate encrypted USB drive or NAS for off-host copies; confirm capacity against Nextcloud data, media, and Wazuh retention. Maintain an additional offline/off-site copy for critical data. A VM snapshot alone is not an application-consistent or off-host backup. Backup frequency, retention, and acceptable data loss are decisions to confirm after measurement; do not describe backups as automated until implemented.

## Restore and acceptance tests

- Restore one guest from off-host backup onto isolated storage and verify its assigned VLAN.
- Restore Nextcloud database and data as a matched set.
- Restore Jellyfin configuration and selected media.
- Restore KEV data and private serving, then test its public hostname and refresh schedule before retiring the old deployment.
- Rebuild Wazuh configuration and verify agents/log delivery.
- Confirm that restored VMs cannot reach forbidden VLANs.

## Operational routine

Review firewall denials, tunnel health, public HTTP responses, guest/host updates, Wazuh alerts, backup success, free storage, and restore evidence on a schedule chosen during implementation. Record capacity measurements before deciding whether a second physical host is needed.

## Later improvements

A UPS, additional off-host storage, IDS/IPS, uptime monitoring, and a separate public-project host remain possible future work. Deploy any new service only after choosing its VM, VLAN, access path, resource budget, and backup/monitoring plan.
