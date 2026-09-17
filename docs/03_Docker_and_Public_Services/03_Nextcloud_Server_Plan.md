# Nextcloud Server Plan

## Purpose and placement

Nextcloud provides private cloud storage in its own NucBox VM on VLAN 80 (`10.10.80.0/24`). VLAN 80 is the existing application/DMZ zone, but **this Nextcloud plan is private**: access comes from approved trusted devices or through VPN, not from a public hostname or the public-project tunnel.

| Component | Placement |
|---|---|
| Nextcloud application | Nextcloud VM, VLAN 80 |
| Database and cache | Private container network inside the same VM |
| Optional local reverse proxy | Inside the Nextcloud VM, if required for TLS or app routing |
| User files and app configuration | Dedicated persistent storage and off-host backups |
| Logs | Specific forwarding path to Wazuh in VLAN 70 |

The database and cache must not be placed in the public-projects VM or shared through a broad Server VLAN mount. Confirm storage growth and backup capacity on the actual NucBox.

## Access and security

- Allow Nextcloud only from approved Trusted/VPN sources to its application endpoint. No WAN port forward or public Cloudflare hostname is planned.
- Restrict its admin interface, SSH, and Docker management to approved administrator paths.
- Configure trusted domains and, only if a proxy is deployed, explicit trusted proxy addresses and forwarded-client settings.
- Use strong credentials and MFA for administrators; keep the app and dependencies updated.
- Permit only the outbound services needed for updates and notifications, and a narrow log path to Wazuh.
- Deny Nextcloud-initiated access to the public-projects VM, Trusted devices, and Management VLAN by default.

## Backup and validation

Take application-consistent backups of user files, configuration, and database together; keep a recoverable copy off the NucBox and test a restore. Verify VPN access, denied WAN/public-tunnel access, denied cross-zone traffic, failed-login logging, and successful recovery. Public file sharing is outside this baseline and would require a separate access and transfer review.
