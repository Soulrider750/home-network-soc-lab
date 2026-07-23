# OPNsense Interface and VLAN Plan

## Hardware

Firewall appliance: Protectli Vault V1410-4 Port micro appliance.

## Physical Port Plan

| Protectli Port | OPNsense Role | Purpose |
|---|---|---|
| Port 1 | WAN | Connects to ISP modem |
| Port 2 | LAN trunk | Connects to TP-Link Omada SG3210XHP-M2 and carries VLANs 10-90 |
| Port 3 | Emergency/Maintenance | Optional direct admin access or isolated break-glass network |
| Port 4 | Future Use | Optional second WAN, dedicated lab network, or future expansion |

## OPNsense Responsibilities

- WAN interface and internet connectivity
- VLAN interfaces and gateways
- DHCP scopes or reservations
- DNS forwarding/resolution
- NAT and port forwarding
- Inter-VLAN firewall rules
- WireGuard VPN for secure remote admin
- Syslog/log forwarding to Wazuh
- Optional IDS/IPS after the base design is stable

## VLAN Interface Creation

Create VLAN interfaces on the LAN trunk parent interface. Assign each VLAN as its own interface in OPNsense.

Recommended interface names:

| Interface Name | VLAN | Description |
|---|---|---|
| MGMT | 10 | Management |
| TRUSTED | 20 | Trusted devices |
| SERVERS | 30 | Internal servers |
| MEDIA | 40 | Media devices |
| IOT | 50 | IoT devices |
| GUEST | 60 | Guest Wi-Fi |
| SOC | 70 | Wazuh monitoring |
| DMZ | 80 | Public-facing services |
| ATTACKLAB | 90 | Simulated attack lab |

## NAT and Public Services

Only forward WAN traffic to the NGINX reverse proxy in the DMZ.

| WAN Port | Forward To | Purpose |
|---|---|---|
| TCP 443 | 10.10.80.10 | HTTPS reverse proxy |
| TCP 80 | 10.10.80.10, optional | Redirect to HTTPS or certificate validation |

Do not forward WAN traffic directly to Nextcloud, Jellyfin, Wazuh, Docker, SSH, OPNsense management, switch management, or AP management.

## DNS Notes

- Use AdGuard DNS as the resolver/filtering service if desired.
- Domain records for `cloud.yourdomain.com` and `media.yourdomain.com` must be set at the authoritative DNS provider for the domain.
- Use OPNsense host overrides or split DNS so internal clients resolve public service names to the internal NGINX DMZ address when appropriate.

## Recommended VPN

Add WireGuard on OPNsense for remote administration. Remote management should happen over VPN, not through public web management interfaces.
