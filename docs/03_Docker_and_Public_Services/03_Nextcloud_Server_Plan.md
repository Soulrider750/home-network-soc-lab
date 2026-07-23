# Nextcloud Server Plan

## Purpose

Nextcloud Server provides self-hosted cloud storage and remote file access at:

`https://cloud.yourdomain.com`

## Placement

| Component | Recommended Zone |
|---|---|
| NGINX reverse proxy | VLAN 80 DMZ |
| Nextcloud Server app | VLAN 80 DMZ or tightly controlled app network |
| Database | VLAN 30 Servers or private Docker network |
| Redis/cache | Private Docker network |
| Nextcloud data | Server storage with strong backups |
| Logs | Forwarded to Wazuh SOC VLAN |

## Recommended Stack

- Nextcloud application container
- MariaDB or PostgreSQL database container
- Redis container
- NGINX reverse proxy
- Scheduled backups
- Wazuh monitoring of logs and key file paths

## Security Requirements

- Do not expose Nextcloud directly to the WAN.
- Access Nextcloud only through NGINX HTTPS reverse proxy.
- Configure trusted domains.
- Configure trusted proxies.
- Use strong admin credentials.
- Enable MFA for the admin account.
- Keep Nextcloud and apps updated.
- Limit installed apps to what is needed.
- Forward Nextcloud logs to Wazuh.
- Monitor failed logins.
- Back up app config, database, and user data.
- Test restore procedures.

## Firewall Rules

| Source | Destination | Action | Purpose |
|---|---|---|---|
| WAN | NGINX | Allow TCP 443 | Public HTTPS entry |
| NGINX | Nextcloud app | Allow app port only | Reverse proxy flow |
| Nextcloud app | Database | Allow database port only | Application dependency |
| Nextcloud app | Internet | Allow limited outbound | Updates, app store, notifications |
| Nextcloud/DMZ | Trusted | Block | Prevent lateral movement |
| Trusted admin device | Nextcloud admin UI | Allow HTTPS | Administration |

## Evidence to Capture

- NGINX virtual host config with sensitive values removed.
- OPNsense NAT rule screenshot.
- OPNsense firewall rule screenshot.
- Nextcloud trusted domain/proxy configuration snippet with secrets removed.
- Wazuh alert for failed Nextcloud login.
- Backup and restore test notes.
