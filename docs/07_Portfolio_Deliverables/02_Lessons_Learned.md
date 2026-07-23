# Lessons Learned

Use this file as a running journal while building the project.

## What Worked Well

- Separating devices by trust level made the firewall requirements easier to define.
- Planning validation evidence before implementation created clearer success criteria.
- 

## What Was Difficult

- The single-NIC NucBox required a documented balance between cost, complexity, and isolation.
- Public Nextcloud and Jellyfin access required separating application access from management access.

## Problems Encountered

| Problem | Cause | Fix | Evidence |
|---|---|---|---|
|  |  |  |  |

## Design Tradeoffs

| Tradeoff | Decision | Reason |
|---|---|---|
| One physical NucBox host for multiple security zones | Accepted for lab | Cost and simplicity; documented as a limitation |
| Docker instead of VMs | Accepted initially | Faster deployment; future Proxmox upgrade possible |
| Public Jellyfin access | Allowed through NGINX only | Remote convenience with reduced exposure |
| AdGuard DNS | Used for filtering/resolution | Authoritative DNS still managed elsewhere |

## Future Improvements

- Add UPS.
- Add NAS or external backup drive.
- Add WireGuard VPN.
- Add IDS/IPS after baseline stability.
- Add Zeek/Suricata sensor with switch port mirroring.
- Add Proxmox or separate hosts for stronger segmentation.
- Add a formal asset inventory.
- Add vulnerability scanning reports.
