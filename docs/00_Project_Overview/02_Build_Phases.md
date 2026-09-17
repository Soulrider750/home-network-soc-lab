# Build Phases and Step-by-Step Plan

The four-VM architecture is a target state, not a claim that the services have been moved. Keep the existing live KEV origin in service until the NucBox replacement passes its own acceptance checks.

## Phase 1: Confirm the design

- Confirm NucBox model, virtualization support, RAM, local storage, backup capacity, and room for Wazuh indexing and Jellyfin transcoding.
- Confirm the OPNsense, Omada switch, AP, trunk port, domain, and DNS inventory.
- Reserve VLAN 81 (`10.10.81.0/24`) for public projects; retain VLANs 80, 30, 70, and 10 for Nextcloud, internal services, SOC, and management.
- Save the current topology and firewall plan. A static public IP is not required for the planned public-project tunnel.

## Phase 2: OPNsense and switching

- Configure OPNsense WAN, trunk, VLAN gateways, DHCP/DNS, VPN, and default-deny inter-VLAN rules, including VLAN 81.
- Configure Omada switch Port 3 as the NucBox trunk for VLANs 10, 30, 70, 80, and 81; map SSIDs and access ports.
- Validate each zone's addressing, allowed traffic, denied traffic, and OPNsense logs before hosting services.
- Export OPNsense and Omada configuration backups.

## Phase 3: NucBox hypervisor and VMs

- Install and harden a virtualization platform such as Proxmox VE; keep management on VLAN 10 only.
- Configure a VLAN-aware virtual bridge on the single NucBox Ethernet trunk. Give each application VM only its assigned VLAN.
- Create the Public-projects, Nextcloud, Media/internal-services, and SOC VMs in stages. Size each from measured use and available host capacity.
- Install and harden guest operating systems, then Docker Compose inside the VMs as needed. Keep per-VM credentials, data, and configuration separate.
- Decide optional Omada Controller placement within the Management zone before deploying it.

## Phase 4: Private applications and SOC

- Deploy Nextcloud with its database and cache inside the Nextcloud VM; allow approved local and VPN clients only.
- Deploy Jellyfin in the Media/internal-services VM; allow approved local and VPN clients only.
- Deploy Wazuh in the SOC VM and onboard OPNsense, the hypervisor, guests, NGINX, applications, and Docker events through narrow log-ingestion rules.
- Test private access, blocked public access, guest boundaries, and backup/restore for each workload.

## Phase 5: Public projects

- Stage the portfolio and the KEV dashboard in the Public-projects VM with NGINX and a dedicated outbound `cloudflared` connector.
- Configure public hostnames in Cloudflare Tunnel; do not open WAN forwards to these websites.
- Test Cloudflare egress, both HTTPS sites, origin and application logs, and blocked access from VLAN 81 into personal-data or management zones.
- Migrate the KEV dashboard only after its source, data, images, schedules, backup, and rollback checks are complete. Verify the public hostname before retiring the old origin.

## Phase 6: Security validation and portfolio evidence

- Verify VPN-only paths for Nextcloud and Jellyfin and management-only paths for Wazuh and infrastructure.
- Run authorized, controlled Attack Lab tests against approved targets, with temporary test resources separate from the four service VMs.
- Capture sanitized firewall, tunnel, VM, application, Wazuh, and restore evidence.
- Document deviations, measured capacity, recovery limits, and lessons learned.

## Later option

Consider a second host such as a ThinkCentre when measured capacity, independent maintenance, or stronger physical separation warrants it. Keep the Public-projects VM and its VLAN policy portable so its host can change without redesigning the public URLs.
