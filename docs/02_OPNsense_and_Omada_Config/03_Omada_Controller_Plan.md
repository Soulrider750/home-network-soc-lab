# Omada Controller Plan

## Role and status

The Omada Controller is planned in a dedicated management container on the NucBox for central management of the Omada switch and access point. Its deployment is still pending. The container stays on VLAN 10 and is separate from the four application VMs.

## Management boundary

| Endpoint | Planned zone and access |
|---|---|
| Omada Controller container | NucBox, VLAN 10 Management; proposed reservation `10.10.10.10`, subject to conflict check |
| Omada switch | VLAN 10 management address; tagged trunk and client VLANs remain separate |
| Omada AP | VLAN 10 management address; SSIDs map clients to their own VLANs |
| Administrator | Approved admin device or VPN path to the controller UI only |
| Wazuh | Specific controller event/log forwarding if supported and validated |

The controller needs its documented management/adoption connectivity to the switch and AP. Keep these endpoints on VLAN 10 where possible. Do not give the controller a public hostname, WAN port forward, Cloudflare public tunnel route, or general access to application VLANs. If discovery or adoption must cross a VLAN boundary, document the exact source, destination, and ports before adding OPNsense rules.

## Capacity and installation gate

Size the dedicated container against the stated Intel Core i9-13900HK, 32 GB DDR5 RAM, and 1 TB SSD after verifying installed hardware, virtualization support, and headroom for the four VMs. Current Omada Software Controller v6 guidance lists supported 64-bit Linux releases and recommends a host with at least 16 GB RAM for stable operation. That is a host recommendation, not a per-controller RAM allocation. Verify the exact selected version, container compatibility, and NucBox headroom before installation. Running it in a NucBox management container is a lab implementation choice, not a claim of vendor-supported Proxmox LXC deployment.

## Build and recovery sequence

1. Reserve the controller address and confirm the NucBox/switch path carries VLAN 10 without making VLAN 10 an application VM interface.
2. Back up standalone switch/AP settings before adoption or management-VLAN changes.
3. Create the isolated management container, install the controller, restrict its UI to approved admin/VPN sources, and adopt the switch/AP one at a time.
4. Verify management connectivity, SSID-to-VLAN mapping, switch trunks, client DHCP, and default-deny firewall behavior after adoption.
5. Export a controller backup off the NucBox, record the version and device-inform settings, and rehearse a restore before depending on it for recovery.
6. Monitor controller health and administrative events, and keep credentials and backup archives outside the public repository.

The controller container is separate from the four persistent application VMs and does not add a fifth VM.

## References

- [Omada Controller user guide, including backup and restore](https://support.omadanetworks.com/en/document/111217/)
- [Omada Software Controller Linux installation](https://support.omadanetworks.com/uk/document/13088/)
- [Omada Management VLAN guidance](https://support.omadanetworks.com/us/document/110372/)
