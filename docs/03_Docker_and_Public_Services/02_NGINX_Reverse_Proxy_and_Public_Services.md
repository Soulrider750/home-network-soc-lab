# Public Projects and Cloudflare Tunnel Plan

## Purpose

The public-projects VM on VLAN 81 will host the CISA KEV dashboard and personal portfolio. NGINX serves or routes the approved web origins, and a dedicated `cloudflared` connector establishes an **outbound** tunnel to Cloudflare. This is the public access plan for those two sites only. The existing live KEV deployment is not changed by this plan.

## Proposed request path

```text
Visitor → HTTPS at Cloudflare → outbound Cloudflare Tunnel
        → public-projects VM, VLAN 81 → NGINX
        → KEV dashboard or portfolio site
```

| Service | Planned exposure | Data boundary |
|---|---|---|
| KEV dashboard | Public hostname through the public-project tunnel | Dashboard-generated data and configuration stay in the public VM |
| Personal portfolio | Public hostname through the same public-project tunnel | Static site and deployment configuration stay in the public VM |
| Nextcloud | Private trusted/VPN access in VLAN 80 | No route from the public-project connector |
| Jellyfin | Private trusted/Media/VPN access in VLAN 30 | No public-tunnel video route |
| Wazuh and administration | Private management access | No public hostname |

A tunnel can map more than one hostname to separate local services. Keep its credentials outside Git and restrict the connector to the origins it actually serves. Cloudflare Tunnel itself does not add authentication to a published public hostname. If any private application is later published, use a separate access design and connector rather than broadening this public connector across VLANs.

## Network policy

- No inbound WAN port forward is required for the KEV dashboard or portfolio when served exclusively through the tunnel. A static public IP is not a dependency for these sites.
- Allow the public connector outbound traffic to Cloudflare's documented tunnel endpoints on UDP 7844 (QUIC) and TCP 7844 (HTTP/2), plus required DNS and update traffic. Recheck the current Cloudflare destination list before implementation.
- Permit only the public VM's necessary outbound feed retrieval, updates, and log delivery. Block public-project access to Nextcloud data, Jellyfin, Wazuh administration, and the Management VLAN.
- Keep NGINX access/error logs and application logs for Wazuh. OPNsense can see the encrypted tunnel connection, not the visitors' underlying HTTP requests.

## Validation and migration

Verify the VLAN and hypervisor bridge, private NGINX origins, tunnel target, public HTTPS responses, both hostnames, logging, backup restore, and blocked public-to-personal paths. Move the KEV dashboard only through its own verified backup and release workflow. The new deployment must pass external and operational checks before the current live origin is retired.

Cloudflare's current public-route guidance calls for a specific paid service to serve video and other large files. This plan keeps Jellyfin and private Nextcloud transfers off the ordinary public tunnel.
