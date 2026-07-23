# Jellyfin Server Plan

## Purpose

Jellyfin Server provides internal and remote media streaming at:

`https://media.yourdomain.com`

## Placement

| Component | Recommended Zone |
|---|---|
| NGINX reverse proxy | VLAN 80 DMZ |
| Jellyfin Server | VLAN 30 Servers |
| Media clients | VLAN 40 Media and VLAN 20 Trusted |
| Logs | Forwarded to Wazuh SOC VLAN |

## Security Requirements

- Do not expose Jellyfin port 8096 directly to the internet.
- Use NGINX HTTPS reverse proxy for remote access.
- Restrict Jellyfin administration to trusted/admin devices.
- Use strong passwords for Jellyfin users.
- Avoid public guest accounts.
- Forward Jellyfin logs to Wazuh.
- Monitor failed login attempts and unusual remote access.
- Keep server updated.

## Firewall Rules

| Source | Destination | Action | Purpose |
|---|---|---|---|
| WAN | NGINX | Allow TCP 443 | Public HTTPS entry |
| NGINX | Jellyfin Server | Allow Jellyfin backend port only | Reverse proxy flow |
| Media VLAN | Jellyfin Server | Allow Jellyfin access | Local streaming |
| Trusted VLAN | Jellyfin Server | Allow Jellyfin access | Admin/user access |
| Jellyfin Server | Trusted VLAN | Block by default | Prevent lateral movement |
| Guest/IoT | Jellyfin Server | Block by default | Reduce exposure |

## Evidence to Capture

- NGINX reverse proxy config with sensitive values removed.
- OPNsense rule showing remote access path.
- Jellyfin remote access working through HTTPS.
- Wazuh alert/log view for Jellyfin failed login or access log event.
- Screenshot showing no direct WAN port forward to Jellyfin.
