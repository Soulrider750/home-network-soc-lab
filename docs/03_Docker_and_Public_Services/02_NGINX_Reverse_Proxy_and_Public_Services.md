# NGINX Reverse Proxy and Public Services

## Purpose

NGINX provides the single public HTTPS entry point for Nextcloud and Jellyfin. Public users should never connect directly to Nextcloud or Jellyfin application ports.

## Public Names

| Public Name | Service | Backend |
|---|---|---|
| `cloud.yourdomain.com` | Nextcloud Server | Nextcloud container/service |
| `media.yourdomain.com` | Jellyfin Server | Jellyfin container/service |

## Recommended Public Flow

```text
Internet
  ↓
Static public IP
  ↓
OPNsense WAN NAT: TCP 443 only
  ↓
NGINX reverse proxy in VLAN 80 DMZ
  ↓
Nextcloud Server or Jellyfin Server backend
```

## Firewall Exposure

| Source | Destination | Port | Purpose |
|---|---|---|---|
| WAN | NGINX DMZ IP | TCP 443 | HTTPS public access |
| WAN | NGINX DMZ IP | TCP 80 optional | Redirect or certificate validation |
| WAN | Nextcloud direct | Block | Do not expose directly |
| WAN | Jellyfin direct | Block | Do not expose directly |
| WAN | Wazuh/OPNsense/Switch/AP | Block | Never expose management |

## Recommended NGINX Controls

- TLS certificates through Let's Encrypt or another trusted certificate authority.
- HTTP-to-HTTPS redirect.
- Security headers where compatible.
- Rate limiting for authentication-heavy paths.
- Upload/request body size adjusted for Nextcloud needs.
- Separate access and error logs for Nextcloud and Jellyfin.
- Logs forwarded to Wazuh.
- No direct exposure of Docker socket.
- No public admin panels.

## Certificate Options

- HTTP-01 validation can work if TCP 80 is temporarily or permanently forwarded to NGINX.
- DNS-01 validation is cleaner if your DNS provider supports API-based certificate automation.

## Split DNS Recommendation

Use OPNsense host overrides or internal DNS so internal devices resolve `cloud.yourdomain.com` and `media.yourdomain.com` to the internal NGINX DMZ IP. This avoids unnecessary hairpin NAT issues and makes internal access cleaner.
