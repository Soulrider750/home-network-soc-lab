# Lessons Learned

Use this file as a running journal during implementation. The observations below are **planning decisions**, not tested outcomes.

## Planning decisions and tradeoffs

| Decision | Reason | Validation still needed |
|---|---|---|
| Four VMs on one NucBox | Separate public projects, personal data, internal media, and SOC workloads now | Confirm host capacity, virtual bridge isolation, and restore behavior |
| Public-projects VLAN 81 | Put KEV and portfolio in a different routed zone from Nextcloud | Test blocked cross-zone paths |
| Nextcloud and Jellyfin through VPN | Keep private files and video off the ordinary public-project tunnel | Test client access and performance |
| Cloudflare Tunnel for public sites | Publish KEV and portfolio without inbound website port forwards | Verify tunnel target, public HTTPS, and logging |
| Omada Controller container on VLAN 10 | Centralize switch/AP management without exposing the controller | Verify container compatibility, capacity, adoption, and recovery |

A NucBox outage affects every hosted VM. Wazuh also shares this failure domain. Off-host backups and restore tests are required before claiming recovery capability. Another host can be added later when capacity or maintenance needs justify it.

## Implementation journal

| Date | Problem or observation | Cause | Change | Evidence |
|---|---|---|---|---|
|  |  |  |  |  |
