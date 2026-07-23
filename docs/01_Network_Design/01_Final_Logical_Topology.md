# Final Logical Topology

## Diagram

![Final Logical Topology](../../assets/diagrams/logical_topology_final.png)

## SVG Version

The SVG version is stored here:

`../../assets/diagrams/logical_topology_final.svg`

## Topology Explanation

The topology has four major layers:

1. **Edge layer:** Internet, ISP modem, and OPNsense firewall.
2. **Switching and wireless layer:** TP-Link Omada SG3210XHP-M2 managed switch and TP-Link Omada BE5000 access point.
3. **Service layer:** GMKtec NucBox Docker host running NGINX, Nextcloud Server, Jellyfin Server, and Wazuh.
4. **Security zones:** VLANs for Management, Trusted, Servers, Media, IoT, Guest, SOC, DMZ, Attack Lab, and Parking/Unused ports.

## Main Traffic Flows

| Flow | Path | Purpose |
|---|---|---|
| Normal internet access | Client VLAN → OPNsense → WAN | Everyday browsing and updates |
| Public Nextcloud access | WAN → OPNsense NAT → DMZ NGINX → Nextcloud Server | HTTPS cloud access |
| Public Jellyfin access | WAN → OPNsense NAT → DMZ NGINX → Jellyfin Server | HTTPS media access |
| Internal Jellyfin access | Media/Trusted VLAN → Server VLAN | Local streaming |
| Management access | Trusted admin device → Management VLAN | Administer OPNsense, switch, AP, and controller |
| Monitoring flow | Endpoints/services/firewall → Wazuh SOC VLAN | Log collection and alerts |
| Attack testing | Attack Lab VLAN → approved test targets | Controlled security testing |

## Key Security Boundaries

- Guest devices cannot reach internal VLANs.
- IoT devices cannot reach Trusted, Management, or Server VLANs by default.
- DMZ services cannot initiate traffic into Trusted devices.
- Management interfaces are only reachable from approved admin devices.
- Public access is limited to HTTPS through NGINX.
- Attack Lab traffic is blocked by default and only allowed to approved targets during tests.
