# Segmented Home Network and SOC Monitoring Lab

![Project status](https://img.shields.io/badge/status-planning%20complete%20%7C%20implementation%20in%20progress-2563eb)
![Focus](https://img.shields.io/badge/focus-network%20security%20%7C%20SOC-0f172a)
![Documentation](https://img.shields.io/badge/documentation-in%20progress-0891b2)

> **Current milestone:** Stage 1 complete: planning and architecture baseline. The next stage is OPNsense deployment and core network validation.

![Segmented Home Network and SOC Monitoring Lab logical topology](assets/diagrams/logical_topology_final.png)

## Project Summary

This project redesigns a standard home network as a segmented, security-focused environment that also functions as a practical SOC monitoring lab. Instead of allowing personal computers, smart-home devices, guests, media systems, servers, management interfaces, and public services to share one flat network, the design separates them by trust level and purpose.

The planned environment uses:

- **OPNsense** for routing, VLAN gateways, DHCP, DNS, NAT, firewall policy, VPN, and logging
- **TP-Link Omada SG3210XHP-M2** for managed switching, VLAN trunks, access ports, and PoE+
- **TP-Link Omada BE5000** for VLAN-mapped wireless networks
- **GMKtec NucBox** as a Linux and Docker service host
- **NGINX** as the only planned public HTTPS entry point
- **Nextcloud** for self-hosted cloud storage
- **Jellyfin** for media services
- **Wazuh** for centralized logging, alerting, and file integrity monitoring
- An isolated **Attack Lab VLAN** for controlled security testing

The completed planning package includes the logical topology, VLAN and subnet plan, switch-port design, wireless SSID mapping, firewall rule matrix, Docker service architecture, reverse-proxy design, SOC monitoring plan, validation scenarios, and backup and recovery strategy.

## Project Objectives

1. Replace a flat home network with clearly defined security zones.
2. Enforce default-deny communication between VLANs.
3. Separate management, trusted, server, media, IoT, guest, SOC, DMZ, and testing systems.
4. Publish approved services only through an HTTPS reverse proxy.
5. Centralize security-relevant logs in Wazuh.
6. Validate firewall and detection controls through safe, controlled tests.
7. Document the complete lifecycle as a professional portfolio case study.

## Architecture at a Glance

| Layer | Technology | Planned Responsibility |
|---|---|---|
| Edge and routing | OPNsense on Protectli Vault | WAN, VLAN gateways, DHCP, DNS, NAT, VPN, firewall rules, logging |
| Core switching | TP-Link Omada SG3210XHP-M2 | 2.5GbE switching, VLAN trunks, access ports, PoE+, unused-port control |
| Wireless | TP-Link Omada BE5000 | SSID-to-VLAN mapping for trusted, media, IoT, guest, and lab clients |
| Service host | GMKtec NucBox, Linux, Docker Compose | NGINX, Nextcloud, Jellyfin, Wazuh, optional Omada Controller |
| Public boundary | NGINX reverse proxy | Single HTTPS entry point for approved services |
| Monitoring | Wazuh SIEM | Log collection, alerting, dashboards, and file integrity monitoring |
| Security testing | Attack Lab VLAN | Controlled scans, authentication tests, and validation exercises |

## VLAN and Subnet Plan

| VLAN | Security Zone | Subnet | Purpose |
|---|---|---|---|
| 10 | Management | `10.10.10.0/24` | OPNsense, switch, AP, and controller management |
| 20 | Trusted | `10.10.20.0/24` | Personal computers, phone, and approved administrator devices |
| 30 | Servers | `10.10.30.0/24` | Internal services, storage, databases, and backups |
| 40 | Media | `10.10.40.0/24` | TVs, streaming devices, game consoles, and media clients |
| 50 | IoT | `10.10.50.0/24` | Smart-home and lower-trust connected devices |
| 60 | Guest | `10.10.60.0/24` | Internet-only access for visitors |
| 70 | SOC | `10.10.70.0/24` | Wazuh components, dashboards, and log collection |
| 80 | DMZ | `10.10.80.0/24` | NGINX and approved public-service paths |
| 90 | Attack Lab | `10.10.90.0/24` | Kali, test targets, and controlled simulations |
| 99 | Parking | No routed subnet | Disabled or unused switch ports |

## Security Design

The planned network follows a **default-deny inter-VLAN model**. Traffic is allowed only when a documented administrative or service requirement exists.

Key design controls include:

- Guest devices receive internet access but cannot reach internal VLANs.
- IoT devices are blocked from Trusted, Management, SOC, and Server networks by default.
- Management interfaces are accessible only from approved administrator devices.
- Public WAN traffic is limited to HTTPS through NGINX in the DMZ.
- Nextcloud, Jellyfin, Wazuh, Docker, OPNsense, switch, and AP management interfaces are not exposed directly to the internet.
- DMZ systems cannot initiate connections into the Trusted VLAN.
- Media clients can access only the services required for streaming.
- Attack Lab access is blocked by default and temporarily allowed only to approved test targets.
- Unused switch ports are disabled or assigned to VLAN 99.
- Remote administration is planned through WireGuard instead of public management pages.

## Planned Public-Service Flow

```text
Internet
  ↓
OPNsense WAN and NAT
  ↓ TCP 443 only
NGINX reverse proxy in VLAN 80
  ↓ approved backend connection
Nextcloud or Jellyfin
```

This design prevents direct application-port exposure and creates one controlled public entry point for TLS termination, access logging, rate limiting, and backend routing.

## SOC Monitoring Plan

Wazuh is planned to collect and analyze events from:

- OPNsense firewall and inter-VLAN deny logs
- NGINX access and error logs
- Nextcloud authentication and application logs
- Jellyfin authentication and session logs
- Linux authentication, sudo, package, and system activity
- Docker container start, stop, restart, and error events
- File integrity monitoring for system and service configuration paths

### Planned Detection Validation

| Scenario | Expected Evidence |
|---|---|
| Failed SSH authentication | Linux authentication event and Wazuh alert |
| Port scan from Attack Lab | OPNsense log and Wazuh or IDS evidence |
| Blocked IoT-to-Trusted connection | Firewall deny event |
| Failed Nextcloud login | Nextcloud log and Wazuh event |
| Failed Jellyfin login | Jellyfin log and Wazuh event |
| Suspicious NGINX request | NGINX access/error log and Wazuh event |
| Configuration-file change | Wazuh file integrity monitoring alert |
| Docker container restart | Docker event or service log |
| New Linux test account | Authentication or system audit event |

Each test will be documented with its objective, source, target, steps, expected result, actual result, evidence, analyst notes, recommended response, and cleanup actions.

## Current Project Status

### Planning Stage — Complete

- [x] Defined project purpose, scope, and security goals
- [x] Created the final logical topology
- [x] Designed ten VLAN and trust zones
- [x] Assigned subnets, gateways, and addressing conventions
- [x] Planned OPNsense interfaces and physical ports
- [x] Planned switch trunks, access ports, and unused-port handling
- [x] Mapped wireless SSIDs to VLANs
- [x] Created a default-deny firewall rule matrix
- [x] Designed the NucBox and Docker service architecture
- [x] Designed the NGINX and DMZ public-service path
- [x] Planned Nextcloud and Jellyfin security controls
- [x] Planned Wazuh log sources and file integrity monitoring
- [x] Defined safe Attack Lab rules and validation scenarios
- [x] Created hardening, backup, recovery, and operations plans
- [x] Created the portfolio evidence checklist

### Implementation and Validation — Next

- [ ] Install and configure OPNsense
- [ ] Create VLAN interfaces, gateways, DHCP scopes, aliases, and baseline firewall rules
- [ ] Configure Omada trunks, access ports, SSIDs, and management VLAN
- [ ] Install and harden the Linux/Docker host
- [ ] Deploy NGINX, Nextcloud, Jellyfin, and Wazuh
- [ ] Configure TLS, split DNS, and approved public access
- [ ] Forward logs and enable file integrity monitoring
- [ ] Execute detection-validation scenarios
- [ ] Capture sanitized screenshots and test reports
- [ ] Perform backup restoration tests
- [ ] Record lessons learned and final risk review

## Design Decisions and Limitations

### One physical service host

The NucBox has one Ethernet interface and is planned to host services associated with multiple security zones. The advanced design uses a trunk connection, Linux VLAN subinterfaces, carefully scoped Docker networks, host firewall rules, and OPNsense policy.

This is a practical home-lab tradeoff, but it does not provide the same isolation as separate physical hosts or virtual machines. A compromise of the host could affect multiple services.

### Wazuh shares hardware with monitored services

Hosting the Wazuh stack on the NucBox keeps the project affordable, but reduces monitoring independence. A future version may move the SOC stack to separate hardware or a dedicated virtualization host.

### Service placement is still subject to implementation testing

The final choice between VLAN subinterfaces, macvlan/ipvlan networks, private Docker networks, or a simplified server-VLAN design will be validated during deployment. Any changes will be documented with the security and operational tradeoffs.

## Evidence Roadmap

The following evidence will be added as implementation progresses:

- OPNsense VLAN interfaces, aliases, NAT, and firewall rules
- Omada trunk, access-port, and SSID configuration
- DHCP and connectivity tests from each network
- NGINX TLS and reverse-proxy validation
- Nextcloud and Jellyfin HTTPS access
- Wazuh dashboard and agent status
- Failed-login alerts
- Port-scan and firewall-block evidence
- File integrity monitoring alerts
- Docker event monitoring
- Backup and restore test results
- Sanitized physical topology photographs
- Incident-style detection reports
- Lessons learned and final project review

## Skills Demonstrated

### Demonstrated in the completed planning stage

- Network architecture and logical topology design
- TCP/IP subnetting and address planning
- VLAN and trust-zone design
- Firewall policy and access-control planning
- DMZ and reverse-proxy architecture
- Security monitoring strategy
- Threat-informed validation planning
- Risk analysis and design tradeoff evaluation
- Backup and recovery planning
- Technical documentation

### To be demonstrated during implementation

- OPNsense administration
- Managed switch and wireless VLAN configuration
- Linux server administration
- Docker and Docker Compose deployment
- NGINX reverse proxy and TLS configuration
- Nextcloud and Jellyfin administration
- Wazuh SIEM deployment and log onboarding
- File integrity monitoring
- Detection validation and alert analysis
- Incident-style reporting and troubleshooting

## Documentation

### Project Overview
- [Project Summary](docs/00_Project_Overview/01_Project_Summary.md)
- [Build Phases](docs/00_Project_Overview/02_Build_Phases.md)

### Network Design
- [Logical Topology](docs/01_Network_Design/01_Final_Logical_Topology.md)
- [VLAN and Subnet Plan](docs/01_Network_Design/02_VLAN_and_Subnet_Plan.md)
- [Firewall Rule Matrix](docs/01_Network_Design/03_Firewall_Rule_Matrix.md)

### OPNsense and Omada
- [OPNsense Interface and VLAN Plan](docs/02_OPNsense_and_Omada_Config/01_OPNsense_Interface_and_VLAN_Plan.md)
- [Omada Switch and Access Point Plan](docs/02_OPNsense_and_Omada_Config/02_TP_Link_Omada_Switch_and_AP_Plan.md)

### Docker and Public Services
- [NucBox Docker Host Plan](docs/03_Docker_and_Public_Services/01_NucBox_Docker_Host_Plan.md)
- [NGINX Reverse Proxy and Public Services](docs/03_Docker_and_Public_Services/02_NGINX_Reverse_Proxy_and_Public_Services.md)
- [Nextcloud Server Plan](docs/03_Docker_and_Public_Services/03_Nextcloud_Server_Plan.md)
- [Jellyfin Server Plan](docs/03_Docker_and_Public_Services/04_Jellyfin_Server_Plan.md)

### SOC, Testing, and Operations
- [Wazuh SOC Monitoring Plan](docs/04_SOC_and_Monitoring/01_Wazuh_SOC_Monitoring_Plan.md)
- [Attack Lab Plan](docs/05_Attack_Lab_and_Detections/01_Attack_Lab_Plan.md)
- [Hardening, Backup, and Operations](docs/06_Hardening_Backup_and_Operations/01_Hardening_Backup_and_Operations.md)
- [Lessons Learned](docs/07_Portfolio_Deliverables/02_Lessons_Learned.md)
- [Sources and Documentation Links](docs/08_References/Sources_and_Documentation_Links.md)

## Responsible-Use Statement

All testing in this project is limited to systems and networks I own or have explicit permission to test. Intentionally vulnerable systems will remain isolated. Public internet scanning and testing against third-party systems are outside the project scope.

## Final Goal

The completed lab will document the full lifecycle of a small security-engineering project: requirements, architecture, implementation, segmentation, hardening, monitoring, controlled validation, troubleshooting, recovery testing, and professional reporting.
