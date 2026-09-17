# Wazuh SOC Monitoring Plan

## Purpose and placement

Wazuh is planned in the NucBox SOC VM on VLAN 70. The manager, indexer, dashboard, and supporting components remain in that VM. Its dashboard is reachable only through approved management/VPN paths; monitored systems receive access only to required ingestion endpoints.

Wazuh shares physical hardware with the services it monitors, so a NucBox outage affects both workloads and monitoring. Host-level logs and off-host backup evidence remain important.

## Log sources

| Source | Planned evidence |
|---|---|
| OPNsense | Inter-VLAN blocks, VPN connections, and firewall policy changes |
| NucBox hypervisor | Host authentication, updates, VM lifecycle, and configuration changes |
| Public-projects VM | NGINX requests/errors, KEV refresh status, portfolio origin, tunnel health, Linux auth, Docker events |
| Nextcloud VM | Authentication, app errors, database/backup status, Linux auth, Docker events |
| Media/internal-services VM | Jellyfin authentication/playback errors, Linux auth, Docker events |
| SOC VM | Wazuh service health, storage, and administrator actions |
| Temporary Attack Lab | Approved exercise activity and resulting alerts |

Because the public tunnel is encrypted across the WAN, OPNsense cannot inspect individual HTTP requests inside it. Correlate NGINX, application, tunnel, and guest telemetry for public-site visibility.

## File integrity monitoring

Set guest-specific targets for service configuration, deployment manifests, and sensitive system paths. Avoid recursive monitoring of large Nextcloud file stores, media libraries, or Wazuh index data unless capacity and noise are measured. Keep backup manifests and restores independently verifiable.

## Validation evidence

- Failed SSH and application logins.
- Public NGINX request and KEV refresh events.
- Blocked VLAN 81-to-Nextcloud/Servers/Management paths.
- Approved agent/syslog delivery from each VM and OPNsense.
- File integrity changes to selected configuration files.
- Docker lifecycle events and Wazuh service health.
- Off-host backup and isolated restore of SOC configuration.

Confirm Wazuh CPU, RAM, disk, and retention requirements against the actual NucBox before setting guest resources.
