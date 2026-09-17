# Sources and Documentation Links

The four-VM layout, VLAN 81 assignment, private Nextcloud/Jellyfin access, and separate Omada Controller container on VLAN 10 are planning choices. These references support implementation research; they are not deployment evidence.

## Vendor documentation

- [Proxmox VE Administration Guide: VLAN-aware bridge and management network](https://pve.proxmox.com/pve-docs/pve-admin-guide.pdf)
- [Proxmox VE containers: Docker application containers inside a QEMU VM](https://pve.proxmox.com/pve-docs/pct.1.html)
- [Cloudflare Tunnel firewall requirements](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/configure-tunnels/tunnel-with-firewall/)
- [Cloudflare Tunnel routing and public-route limits](https://developers.cloudflare.com/tunnel/concepts/routing/)
- [Nextcloud reverse proxy configuration](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/reverse_proxy_configuration.html)
- [Jellyfin networking and VPN access](https://jellyfin.org/docs/general/post-install/networking/)
- [Wazuh quickstart sizing](https://documentation.wazuh.com/current/quickstart.html)
- [Wazuh log collection](https://documentation.wazuh.com/current/user-manual/capabilities/log-data-collection/index.html)
- [Wazuh file integrity monitoring](https://documentation.wazuh.com/current/user-manual/capabilities/file-integrity/index.html)
- [Wazuh Docker monitoring](https://documentation.wazuh.com/current/user-manual/capabilities/container-security/monitoring-docker.html)
- [OPNsense VLAN and LAGG setup](https://docs.opnsense.org/manual/how-tos/vlan_and_lagg.html)
- [TP-Link Omada multi-network and SSID VLAN guidance](https://www.tp-link.com/us/support/faq/3091/)
- [TP-Link Omada management VLAN guidance](https://www.tp-link.com/us/support/faq/4094/)
- [Omada Controller user guide and backup/restore](https://support.omadanetworks.com/en/document/111217/)
- [Omada Software Controller Linux installation](https://support.omadanetworks.com/uk/document/13088/)
- [TP-Link Omada SG3210XHP-M2 product page](https://www.omadanetworks.com/us/business-networking/omada-switch-access-pro/sg3210xhp-m2/)
