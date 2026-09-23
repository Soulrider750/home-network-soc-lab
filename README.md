# Segmented Home Network and SOC Monitoring Lab

![Project status](https://img.shields.io/badge/status-core%20network%20operational%20%7C%20VLANs%20planned-2563eb)
![Focus](https://img.shields.io/badge/focus-network%20security%20%7C%20SOC-0f172a)

> **Current milestone:** Stage 3 — VLAN segmentation, Step 1 complete. The core network is operational; VLANs, the four-VM layout, and service migration remain planned.

## Project summary

![Planned four-VM NucBox topology](assets/diagrams/logical_topology_final.png)

This project plans a segmented home network, public cybersecurity portfolio services, private home services, and a SOC monitoring lab. OPNsense will route and filter traffic between VLANs; the Omada switch and access point will carry the wired and wireless zones. The NucBox will use a VLAN-aware hypervisor and four persistent VMs.

| NucBox VM | Planned workloads | VLAN | Access |
|---|---|---:|---|
| **Public projects** | CISA KEV dashboard, personal portfolio, NGINX, Cloudflare Tunnel connector | **81** Public-project DMZ | Public sites through an outbound tunnel |
| **Nextcloud** | Nextcloud, database, cache, any local proxy needed for the app | **80** Nextcloud app zone | Approved Trusted/VPN clients |
| **Media/internal services** | Jellyfin and separately reviewed private services | **30** Servers | Approved Trusted/Media/VPN clients |
| **SOC** | Wazuh manager, indexer, dashboard, supporting components | **70** SOC | Restricted dashboard and log ingestion |

Docker Compose is planned **inside** the Linux VMs. The NucBox hypervisor's management address stays on VLAN 10. The Omada Controller is planned in a separate NucBox management container on VLAN 10; its resource allocation still needs hardware validation. Temporary Attack Lab systems are outside these four persistent NucBox VMs. The broader plan also includes NGINX, `cloudflared`, Nextcloud's database/cache, and Wazuh's supporting components in their respective VMs. AdGuard DNS, Uptime Kuma, CrowdSec/Fail2Ban-style controls, and additional sensing are later candidates that require separate placement and capacity decisions.

The planned NucBox has an Intel Core i9-13900HK, 32 GB DDR5 RAM, and a 1 TB SSD (owner-provided specifications). The exact NucBox SKU, usable storage, virtualization support, and workload capacity still need validation. The live KEV dashboard remains on its separately documented Ubuntu deployment until a tested migration is complete; this repository does not claim that the four-VM design is already running.

## Network and trust boundaries

| VLAN | Zone | Planned purpose |
|---:|---|---|
| 10 | Management | OPNsense, Omada switch/AP, NucBox hypervisor, Omada Controller |
| 20 | Trusted | Personal and approved admin devices |
| 30 | Servers | Jellyfin and other private services |
| 40 | Media | TVs, streaming devices, consoles |
| 50 | IoT | Smart-home devices |
| 60 | Guest | Internet-only visitors |
| 70 | SOC | Wazuh |
| 80 | Nextcloud app zone | Private Nextcloud VM |
| 81 | Public-project DMZ | KEV dashboard and portfolio VM |
| 90 | Attack Lab | Temporary controlled test systems |
| 99 | Parking | Unused switch ports; no routed subnet |

The one-NIC NucBox connects to an Omada switch trunk. A VLAN-aware virtual bridge gives each application VM its assigned VLAN, while **OPNsense** remains the router and default-deny firewall between zones. The public-projects VM must have no direct route or shared data mount into Nextcloud, Jellyfin, or Management. Traffic between containers in one VM or devices in one VLAN may not cross OPNsense, so guest and container controls still matter.

## Public and private access

```text
Public visitor → Cloudflare HTTPS → outbound tunnel
               → Public-projects VM, VLAN 81 → NGINX → KEV / portfolio

Approved local or VPN user → OPNsense → Nextcloud VM, VLAN 80
Approved local or VPN user → OPNsense → Jellyfin VM, VLAN 30
Approved admin or VPN user → Management (including Omada Controller) / SOC interfaces
```

The public-project connector needs only its own site origins and documented Cloudflare egress, including UDP/TCP 7844. The KEV and portfolio websites need no inbound WAN port forward or static public IP when published exclusively through that tunnel. Nextcloud and Jellyfin remain private; Wazuh, Docker, OPNsense, Omada, and hypervisor management receive no public hostname. Cloudflare Tunnel publication alone does not authenticate visitors.

Cloudflare's [public-route guidance](https://developers.cloudflare.com/tunnel/concepts/routing/) calls for a specific paid service to serve video and other large files. VPN access avoids designing Jellyfin streaming or private Nextcloud transfers around the ordinary public tunnel.

## Monitoring and recovery

Wazuh is planned to ingest OPNsense events, hypervisor and guest activity, NGINX and application logs, Docker events, and selected file-integrity changes. OPNsense sees the encrypted public tunnel connection, so public HTTP visibility depends on origin and application logs.

All four VMs share the NucBox's physical failure risk. Keep each VM's data, deployment files, and credentials separate; make application-consistent off-host backups and test restores. Measure CPU, RAM, disk, Wazuh retention, and Jellyfin transcoding before assigning resources or deciding whether a second host is needed. A later ThinkCentre could host the public-projects VM while retaining its VLAN 81 policy and public hostnames after a verified cutover.

## Implementation Status

**Updated:** September 22, 2026

**Current milestone:** Stage 3 — VLAN segmentation, Step 1 complete

The core network is operational, and household devices are connected through the Omada AP. OPNsense preparation, the core network cutover, and pre-VLAN preparation are complete.

Pre-VLAN internet performance was measured using [Measurement Lab](https://www.measurementlab.net/) on September 22, 2026:

| Connection | Download | Upload | Latency |
|---|---:|---:|---:|
| Wired | 898.45 Mbps | 39.51 Mbps | 14 ms |
| Wireless | 758.75 Mbps | 39.34 Mbps | 15 ms |

The wireless test used the 5 GHz band at approximately 5 feet from the AP.

The initial OPNsense configuration backup was successfully restored. Separate encrypted pre-VLAN backups for OPNsense, the switch, and the AP are stored on the Parrot OS administration device and an external 2 TB Seagate drive. Direct recovery access has been tested through switch port 7 and Protectli physical port 3.

Installed versions are OPNsense `26.7.4-1`, switch firmware `3.0.29`, and AP firmware `1.2.4`. Omada currently operates in standalone mode; Controller migration is planned.

VLAN segmentation, firewall isolation, new SSIDs, application hosting, and SOC monitoring remain planned. The replacement wireless networks will use new SSIDs, with the current household SSID retained temporarily during testing and migration.

Architecture diagrams and configuration plans describe the intended design unless explicitly labeled as implemented and validated.

See the [implementation progress report](docs/00_Project_Overview/03_Implementation_Progress_2026-09-22.md) for backup, recovery-port, and validation details.

## Status and validation roadmap

### Documented target design

- [x] Four VM roles and separate public-project VLAN 81
- [x] Private access plan for Nextcloud and Jellyfin
- [x] Outbound tunnel plan for KEV and portfolio
- [x] Revised network, firewall, service, SOC, and recovery plans

### Still to implement and verify

- [ ] Record the exact NucBox SKU; verify the i9-13900HK, 32 GB DDR5 RAM, 1 TB SSD, virtualization support, usable storage, and capacity for four VMs plus the Omada management container
- [ ] Configure OPNsense and Omada VLAN 81; adopt switch/AP into the Omada Controller and test VLANs and default-deny routing
- [ ] Install hypervisor with management on VLAN 10 and VLAN-aware bridge
- [ ] Create and harden the four VMs in stages
- [ ] Deploy and test Nextcloud, Jellyfin, Wazuh, and portfolio workloads
- [ ] Stage and validate KEV migration without interrupting its current live deployment
- [ ] Verify public HTTPS, private VPN paths, denied cross-zone access, logs, and backups
- [ ] Run authorized Attack Lab tests and capture sanitized evidence
- [ ] Test off-host recovery and document measured limits

## Documentation

### Project overview

- [Project Summary](docs/00_Project_Overview/01_Project_Summary.md)
- [Build Phases](docs/00_Project_Overview/02_Build_Phases.md)
- [Implementation Progress — September 22, 2026](docs/00_Project_Overview/03_Implementation_Progress_2026-09-22.md)

### Network design

- [Planned Logical Topology](docs/01_Network_Design/01_Final_Logical_Topology.md)
- [VLAN and Subnet Plan](docs/01_Network_Design/02_VLAN_and_Subnet_Plan.md)
- [Firewall Rule Matrix](docs/01_Network_Design/03_Firewall_Rule_Matrix.md)

### OPNsense and Omada

- [OPNsense Interface and VLAN Plan](docs/02_OPNsense_and_Omada_Config/01_OPNsense_Interface_and_VLAN_Plan.md)
- [Omada Switch and Access Point Plan](docs/02_OPNsense_and_Omada_Config/02_TP_Link_Omada_Switch_and_AP_Plan.md)
- [Omada Controller Plan](docs/02_OPNsense_and_Omada_Config/03_Omada_Controller_Plan.md)

### NucBox VMs and services

- [NucBox Four-VM Host Plan](docs/03_Docker_and_Public_Services/01_NucBox_Docker_Host_Plan.md)
- [Public Projects and Cloudflare Tunnel](docs/03_Docker_and_Public_Services/02_NGINX_Reverse_Proxy_and_Public_Services.md)
- [Nextcloud Server Plan](docs/03_Docker_and_Public_Services/03_Nextcloud_Server_Plan.md)
- [Jellyfin Server Plan](docs/03_Docker_and_Public_Services/04_Jellyfin_Server_Plan.md)

### SOC, testing, and operations

- [Wazuh SOC Monitoring Plan](docs/04_SOC_and_Monitoring/01_Wazuh_SOC_Monitoring_Plan.md)
- [Attack Lab Plan](docs/05_Attack_Lab_and_Detections/01_Attack_Lab_Plan.md)
- [Hardening, Backup, and Operations](docs/06_Hardening_Backup_and_Operations/01_Hardening_Backup_and_Operations.md)
- [Planning Milestone](docs/07_Portfolio_Deliverables/01_Portfolio_Case_Study_Planning_Milestone.md)
- [Lessons Learned](docs/07_Portfolio_Deliverables/02_Lessons_Learned.md)
- [Sources and Documentation Links](docs/08_References/Sources_and_Documentation_Links.md)

## Responsible use

Testing is limited to systems and networks owned by the operator or explicitly authorized for testing. Intentionally vulnerable targets remain isolated. Planned controls are not treated as verified until implementation evidence is captured.
