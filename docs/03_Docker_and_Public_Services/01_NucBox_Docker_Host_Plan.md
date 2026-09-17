# NucBox Four-VM Host Plan

## Status and scope

This is the **planned** NucBox architecture. The four VMs, VLAN 81, and service migrations have not been validated or deployed as part of this documentation update. The live KEV dashboard currently has its own Ubuntu deployment; moving it to the NucBox is a separate, tested cutover.

The owner-provided host specifications are an Intel Core i9-13900HK, 32 GB DDR5 RAM, and a 1 TB SSD. Record the exact NucBox SKU and verify installed hardware, usable storage after the hypervisor and VM disks, and virtualization support before assigning resources. The 32 GB RAM and 1 TB SSD are shared across the hypervisor, four VMs, and Omada management container. Measure Wazuh indexing and retention, Nextcloud storage growth, Jellyfin transcoding and media storage, backup size, and total host headroom before committing to simultaneous operation. Build the four VMs in phases rather than assuming all workloads fit at once.

## Host and VM layout

Use a virtualization platform such as Proxmox VE on the NucBox. Run Docker Compose **inside** the Linux application VMs, not as one shared Docker environment on the hypervisor. The hypervisor management interface belongs only on VLAN 10.

| Persistent VM | Workloads and supporting components | VLAN | Access |
|---|---|---:|---|
| Public projects | CISA KEV dashboard, personal portfolio, NGINX web origin/reverse proxy, public-project Cloudflare Tunnel connector | 81 | Public websites through an outbound tunnel; no inbound WAN forward |
| Nextcloud | Nextcloud app, database, cache, and any proxy specifically needed for it | 80 | Trusted clients or VPN; no public hostname in the baseline |
| Media/internal services | Jellyfin and future private services after individual review | 30 | Trusted/Media clients and VPN |
| SOC | Wazuh manager, indexer, dashboard, and supporting components | 70 | Restricted administration and log ingestion only |

The Omada Controller is planned in a dedicated management container on the NucBox, with a VLAN 10 interface and no application VM interface. Its resource allocation and container compatibility still need validation against the actual hardware and selected controller version. Do not give an application VM a VLAN 10 interface merely to host the controller.

Attack Lab test VMs are separate, temporary lab resources and are not part of these four persistent NucBox VMs.

## Supporting and optional services

| Item from the broader plan | Status and placement |
|---|---|
| NGINX and `cloudflared` | Supporting services in the Public-projects VM |
| Nextcloud database and cache | Supporting services inside the Nextcloud VM |
| Wazuh manager, indexer, and dashboard | Supporting services inside the SOC VM |
| Omada Controller | Dedicated NucBox management container on VLAN 10; validate capacity and compatibility before deployment |
| AdGuard DNS, Uptime Kuma, and CrowdSec/Fail2Ban-style controls | Possible later additions; choose a VM/zone, resource budget, and access policy before deployment |
| Suricata/Zeek sensing | Later network-monitoring work; placement must follow a separate visibility and capacity design |

The controller container and these possible later services do not add a fifth persistent NucBox VM to the current plan.

## One-NIC network design

The NucBox's Ethernet link connects to an Omada switch trunk carrying VLANs 10, 30, 70, 80, and 81. A VLAN-aware virtual bridge presents each VM with only its assigned VLAN. Keep the host's management address on VLAN 10 and restrict it to approved admin devices or VPN clients. OPNsense is the gateway and default-deny firewall between VLANs; do not route between security zones on the hypervisor, switch, or Docker host.

VM isolation and separate VLANs reduce cross-service access, but the VMs still share one physical host. Traffic between containers in the **same VM** or devices in the **same VLAN** may never cross OPNsense. Avoid multi-homing the public VM into personal or management VLANs. Use narrowly scoped firewall rules for logging, monitoring, updates, and administration.

## Data and operations boundaries

- Keep each VM's Compose files, data, and credentials separate. Do not mount Nextcloud data into the public-projects VM.
- Keep active application databases and Wazuh index data on appropriately sized local storage; use off-host storage for recoverable, application-consistent backups.
- Back up VM configuration and app data separately. Test restoration of each workload and of the public tunnel path.
- Restrict hypervisor, SSH, Docker, Wazuh, and application administration to approved management paths.
- Send guest, web, application, and hypervisor events to the SOC VM through specific log-ingestion rules.
- Confirm CPU/RAM/disk allocation, NIC and bridge behavior, VM startup order, and restore procedures on the actual hardware.

## Later host migration

A second machine, such as a ThinkCentre, is a later option if measurements show insufficient capacity or independent maintenance/host-level separation becomes necessary. Keep the public VM portable so it can move to a separate host on VLAN 81. Preserve public hostnames while revalidating the tunnel destination, firewall policy, external site access, logs, and backups before retiring the old deployment.
