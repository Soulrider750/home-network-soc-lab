# Jellyfin Server Plan

## Purpose and placement

Jellyfin provides media streaming from the NucBox **Media/internal-services VM** on VLAN 30 (`10.10.30.0/24`). Local Trusted and Media clients may reach only the Jellyfin application port. Remote users connect through the planned VPN, not an ordinary Cloudflare public hostname.

| Component | Placement |
|---|---|
| Jellyfin application and metadata database | Media/internal-services VM, VLAN 30 |
| Media files | Storage sized and mounted for this VM; no public-VM share |
| Media clients | VLAN 40 Media and approved VLAN 20 Trusted clients |
| Logs | Specific forwarding path to Wazuh in VLAN 70 |

## Security requirements

- Do not forward Jellyfin application ports from the WAN or publish it through the public-project tunnel.
- Use VPN for remote streaming and administration; restrict admin functions to approved devices.
- Use strong accounts and avoid public guest accounts.
- Limit Media VLAN access to the Jellyfin service; block Media-to-Trusted and unrelated Server services.
- Keep the guest OS and Jellyfin updated, and monitor authentication and service errors.
- Validate transcoding performance and storage demand on the actual NucBox before setting VM resources.

## Validation and recovery

Test local and VPN playback, blocked WAN/public-tunnel access, blocked cross-zone paths, and failed-login events in Wazuh. Back up Jellyfin configuration and metadata separately from replaceable media; decide media backup scope by capacity and test a restore. Cloudflare's public-route guidance for video and large files is another reason this plan uses private access.
