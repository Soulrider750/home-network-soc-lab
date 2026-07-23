# Firewall Rule Matrix

## Core Policy

The firewall should follow a default-deny model between VLANs. Only allow traffic that has a clear purpose.

## WAN Rules

| Source | Destination | Action | Ports | Reason |
|---|---|---|---|---|
| WAN | DMZ NGINX | Allow | TCP 443 | Public HTTPS access to Nextcloud and Jellyfin through reverse proxy |
| WAN | DMZ NGINX | Optional Allow | TCP 80 | HTTP-to-HTTPS redirect or HTTP-01 certificate validation if needed |
| WAN | OPNsense Management | Block | Any | Do not expose firewall management to the internet |
| WAN | Nextcloud/Jellyfin direct ports | Block | Any | Do not bypass reverse proxy |

## Inter-VLAN Rules

| Source | Destination | Action | Reason |
|---|---|---|---|
| Trusted | Internet | Allow | Normal daily use |
| Trusted admin device | Management | Allow selected ports | Administer OPNsense, switch, AP, and controller |
| Trusted admin device | SOC | Allow Wazuh dashboard | Review alerts and dashboards |
| Trusted admin device | Server/DMZ | Allow selected admin ports | Manage Docker services and servers |
| Trusted | Server | Allow selected service ports | Access Jellyfin and internal services |
| Trusted | DMZ | Allow HTTPS | Access Nextcloud and public service names internally |
| Media | Server | Allow Jellyfin application path | Local streaming |
| Media | Trusted | Block | Protect personal devices |
| Media | Management | Block | Protect infrastructure |
| IoT | Internet | Allow limited outbound | Smart device cloud access |
| IoT | Trusted | Block | Prevent lateral movement to personal devices |
| IoT | Management | Block | Protect firewall, switch, and AP |
| IoT | Server | Block by default | Reduce attack paths |
| Guest | Internet | Allow | Guest internet access |
| Guest | All internal VLANs | Block | Guest isolation |
| DMZ NGINX | Nextcloud Server | Allow app port only | Reverse proxy backend access |
| DMZ NGINX | Jellyfin Server | Allow app port only | Reverse proxy backend access |
| DMZ | Trusted | Block | Stop public service compromise from spreading |
| DMZ | Management | Block | Protect infrastructure |
| Server | Internet | Allow limited outbound | Updates, package downloads, image pulls |
| Server | Trusted | Block by default | Prevent server compromise from spreading |
| SOC | Internet | Allow limited outbound | Updates and threat intelligence downloads |
| Monitored devices | SOC/Wazuh | Allow agent/syslog traffic | Centralized monitoring |
| Attack Lab | Internet | Allow optional limited outbound | Updates and package downloads |
| Attack Lab | Internal VLANs | Block by default | Prevent accidental exposure |
| Attack Lab | Approved test targets | Allow during tests only | Controlled simulations |
| Any | VLAN 99 | Block | Parking VLAN should not be usable |

## Recommended Management Rule

Create an alias named `ADMIN_DEVICES` containing the IP addresses of your MacBook, Lenovo laptop, or dedicated admin workstation. Allow Management VLAN access only from this alias.

## Recommended Service Aliases

| Alias | Members |
|---|---|
| ADMIN_DEVICES | Approved admin device IPs |
| PUBLIC_PROXY | NGINX reverse proxy IP |
| WAZUH_SERVERS | Wazuh manager/indexer/dashboard IPs |
| SERVER_SERVICES | Jellyfin, storage, database, backup IPs |
| ATTACK_LAB_TEST_TARGETS | VMs intentionally used for testing |

## Important Note

Since Nextcloud and Jellyfin are public-facing through NGINX, direct exposure of their application ports should be avoided. Public WAN access should terminate at NGINX over HTTPS.
