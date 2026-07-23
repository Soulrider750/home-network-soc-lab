# Build Phases and Step-by-Step Plan

## Phase 1: Final Design Preparation

- Confirm hardware purchases: Protectli Vault, Omada SG3210XHP-M2, Omada BE5000 AP.
- Confirm ISP modem handoff to OPNsense WAN.
- Request static public IP from ISP.
- Confirm domain registrar or DNS provider access.
- Finalize VLAN IDs and subnets.
- Print/save topology diagram and firewall matrix.

## Phase 2: OPNsense Core Build

- Install OPNsense on the Protectli Vault.
- Configure WAN on Port 1.
- Configure LAN trunk on Port 2.
- Create VLAN interfaces 10,20,30,40,50,60,70,80,90.
- Assign gateways and DHCP scopes.
- Configure DNS resolution/forwarding.
- Create basic outbound NAT.
- Create default block rules between VLANs.
- Add temporary allow rules for setup testing.
- Export initial OPNsense backup.

## Phase 3: Omada Switch and AP Build

- Adopt or configure SG3210XHP-M2.
- Set switch management to VLAN 10.
- Configure Port 1 as trunk to OPNsense.
- Configure Port 2 as trunk + PoE+ to AP.
- Configure Port 3 as trunk to NucBox.
- Configure access ports for media/trusted/lab devices.
- Disable unused ports or assign VLAN 99.
- Configure Omada BE5000 SSIDs and VLAN tags.
- Test each SSID receives the correct subnet.

## Phase 4: NucBox and Docker Build

- Install Linux server OS on the NucBox.
- Configure the NucBox switch port as trunk.
- Configure VLAN subinterfaces or document simpler Docker network design.
- Install Docker and Docker Compose.
- Create `/srv/docker/` folder structure.
- Deploy NGINX reverse proxy.
- Deploy Nextcloud Server stack.
- Deploy Jellyfin Server.
- Deploy Wazuh stack.
- Optional: deploy Omada Controller.
- Add Wazuh agent to the NucBox host.

## Phase 5: Public Service Build

- Create DNS records for `cloud.yourdomain.com` and `media.yourdomain.com`.
- Configure OPNsense NAT for TCP 443 to NGINX DMZ IP.
- Configure NGINX server blocks for Nextcloud and Jellyfin.
- Configure TLS certificates.
- Configure Nextcloud trusted domains and proxies.
- Confirm Jellyfin access through HTTPS reverse proxy.
- Confirm no direct WAN exposure of application ports.

## Phase 6: Firewall Hardening

- Restrict Management VLAN access to approved admin devices.
- Block Guest to all internal VLANs.
- Block IoT to Trusted, Management, Servers, SOC, and DMZ unless explicitly needed.
- Allow Media to Jellyfin only.
- Allow NGINX to Nextcloud/Jellyfin backends only.
- Block DMZ to Trusted.
- Restrict SOC dashboard access.
- Restrict Attack Lab traffic except for approved tests.
- Export OPNsense backup after rules are stable.

## Phase 7: SOC Monitoring

- Forward OPNsense logs to Wazuh or syslog collection path.
- Forward NGINX logs.
- Forward Nextcloud logs.
- Forward Jellyfin logs.
- Monitor Docker events.
- Configure Wazuh FIM paths.
- Capture baseline dashboard screenshots.

## Phase 8: Detection Validation

- Run failed SSH login test.
- Run port scan from Attack Lab VLAN.
- Trigger blocked IoT-to-Trusted traffic.
- Trigger Nextcloud failed login.
- Trigger Jellyfin failed login.
- Trigger NGINX suspicious request event.
- Trigger file modification alert.
- Trigger Docker container restart event.
- Document all alerts and screenshots.

## Phase 9: Portfolio Packaging

- Sanitize screenshots.
- Complete incident report templates.
- Complete case study write-up.
- Add diagrams and tables to GitHub README.
- Add lessons learned.
- Add resume bullets and LinkedIn summary.
- Keep sensitive data out of the public repository.
