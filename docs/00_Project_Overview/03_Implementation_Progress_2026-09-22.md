# Implementation Progress: Core Network and Pre-VLAN Preparation

> Historical snapshot from September 22. For the later completed stage and reviewed Parrot evidence, see the [September 23 Stage 3 completion record](04_Stage_3_Completion_2026-09-23.md).

**Updated:** September 22, 2026

**Current stage:** Stage 3 — VLAN segmentation and new SSIDs

**Stage status:** In progress; Step 1 complete

## Deployment status

| Milestone | Status |
|---|---|
| OPNsense preparation and initial configuration | Complete |
| Initial OPNsense backup and restoration test | Validated |
| Core network cutover | Complete |
| Household migration to the Omada AP | Validated |
| Pre-VLAN backups and inventory | Complete |
| Direct recovery access | Tested |
| VLAN segmentation and firewall isolation | Planned |
| New SSIDs and device migration | Planned |
| Omada Controller migration | Planned |
| NucBox virtual machines and application services | Planned |
| SOC monitoring and detection validation | Planned |

Implementation and test results in this report were recorded by the project owner. Planned capabilities are not presented as deployed.

## Firewall preparation and recovery

OPNsense was installed and configured on the Protectli V1410. The ISP connection uses DHCP. A static public IP address was requested but was not available. Although the assigned WAN address has remained stable, it is treated as dynamic.

Parrot OS is the primary administration device. An initial OPNsense configuration backup was saved to the administration machine and copied to external storage. A full configuration restoration using that backup completed successfully.

The restoration validates the Stage 1 OPNsense recovery procedure. The newer pre-VLAN backups have not been separately restore-tested.

## Core network and wireless cutover

Household devices were migrated to the Omada AP using the previous network’s SSID and password. Multiple devices connected successfully.

The current network remains flat while the segmented replacement networks are prepared. The Omada switch and AP operate in standalone mode. Migration to an Omada Controller is planned as a separate change.

## Performance baseline

**Test date:** September 22, 2026

**Test service:** [Measurement Lab](https://www.measurementlab.net/)

| Connection | Download | Upload | Latency |
|---|---:|---:|---:|
| Wired | 898.45 Mbps | 39.51 Mbps | 14 ms |
| Wireless | 758.75 Mbps | 39.34 Mbps | 15 ms |

The wireless test used the **5 GHz band**, with the client approximately **5 feet from the AP**. Speeds are reported in megabits per second.

These measurements establish an observed internet-performance baseline before VLAN segmentation. They do not measure inter-VLAN throughput or validate firewall isolation.

The test-client model and adapter details were not recorded in this report. Future comparisons should record those details and use comparable test conditions.

## Pre-VLAN preparation

Stage 3, Step 1 is complete. Separate encrypted configuration backups were exported for:

- OPNsense.
- The Omada switch.
- The Omada AP.

Copies are retained privately on the Parrot OS administration device and an external 2 TB Seagate drive.

Current management addresses, firmware versions, physical port roles, performance measurements, and recovery access were recorded before VLAN changes.

Raw configuration exports, encryption passwords, credentials, and public WAN addressing are excluded from this repository.

## Equipment and installed versions

| Component | Role | Installed version |
|---|---|---|
| Protectli V1410 | OPNsense firewall and router | OPNsense `26.7.4-1` |
| Omada SG3210XHP-M2 | Managed switch and AP power | Firmware `3.0.29` |
| Omada EAP723 / BE5000 | Household wireless access | Firmware `1.2.4` |
| Parrot OS device | Primary administration and testing | Not recorded |
| GMKtec NucBox | Planned virtualization and application host | Not recorded |
| External 2 TB Seagate drive | Secondary backup storage | Not applicable |

Versions reflect the recorded state on September 22, 2026.

## Current management addresses

| Device | Management address |
|---|---|
| OPNsense | `10.255.250.1` |
| Omada AP | `10.255.250.181` |
| Omada switch | `10.255.250.153` |

These private addresses describe the current flat network. They will be updated as management services move to the planned Management VLAN.

Network-based management through these addresses is distinct from direct local console access.

## Confirmed physical port roles

### Protectli V1410

| Physical port | Current role | Status |
|---|---|---|
| Port 1 | LAN | In use |
| Port 2 | WAN | In use |
| Port 3 | Direct recovery access | Tested |
| Port 4 | Unused | Proposed VLAN trunk |

OPNsense interface names have not yet been reliably mapped to the physical ports. No `igc` identifier is asserted in this report. The interface corresponding to physical port 4 must be verified before creating the VLAN trunk.

### Omada switch

| Physical port | Confirmed role | Status |
|---|---|---|
| Port 7 | Direct management/recovery access | Tested |

Other switch-port assignments remain defined in the planning documentation until their actual connections are recorded and verified.

The tested recovery ports must be accounted for before applying the proposed VLAN port map.

## Recovery resources and validation

| Resource | Purpose | Recorded result |
|---|---|---|
| Parrot OS device | Administration and primary backup storage | Encrypted backups retained |
| External 2 TB Seagate drive | Additional backup copy | Encrypted backups retained |
| Omada switch port 7 | Direct management/recovery access | Access tested successfully |
| Protectli physical port 3 | Direct management/recovery access | Access tested successfully |
| Monitor | Local console access for OPNsense and NucBox | Available |
| Initial OPNsense backup | Configuration recovery | Full restoration completed successfully |

Successful access through the recovery ports does not constitute a restoration test of the switch or AP backups.

## Wireless migration decision

The segmented networks will use completely new SSIDs. The existing household SSID will remain available temporarily while the new networks are configured and tested.

Devices will move to their assigned networks in groups. The old SSID will be retired after all required devices have migrated and normal connectivity has been verified.

The planned SSID-to-VLAN mappings remain design targets until configuration and isolation testing are complete.

## Validation record

| Check | Result |
|---|---|
| Initial OPNsense configuration restoration | Passed |
| Separate encrypted pre-VLAN exports | Complete for OPNsense, switch, and AP |
| Backup copies on administration device and external drive | Complete |
| Switch port 7 recovery access | Passed |
| Protectli physical port 3 recovery access | Passed |
| Multiple household wireless clients | Connected successfully |
| Wired internet performance | Baseline recorded |
| Wireless internet performance | Baseline recorded |
| VLAN addressing and DHCP | Not yet tested |
| Inter-VLAN isolation | Not yet tested |
| Restricted infrastructure management | Not yet tested |
| Omada Controller adoption | Not yet performed |

## Next implementation checkpoint

The next checkpoint is to identify the OPNsense interface associated with unused physical port 4 and confirm the switch connections while preserving the tested recovery paths.

Subsequent work includes VLAN interfaces, DHCP and DNS configuration, firewall policies, switch VLAN membership, new SSIDs, and device migration. Each network will be tested for correct addressing, internet access, and intended isolation before being marked validated.

After segmentation is complete, updated configuration backups and sanitized validation results will be added to the project record.
