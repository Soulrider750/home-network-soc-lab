# Attack Lab Plan

## Purpose

The Attack Lab VLAN provides a safe place to run controlled tests without placing attack tools on the Trusted VLAN.

## Placement

| System | VLAN | Purpose |
|---|---|---|
| Kali Linux VM | VLAN 90 | Nmap, web checks, authentication test simulations |
| Ubuntu test target | VLAN 90 or Server VLAN during specific labs | Safe target for SSH and logging tests |
| OWASP Juice Shop/DVWA | VLAN 90 or isolated Docker network | Web app testing practice |
| Metasploitable-style target | VLAN 90 only | Vulnerable host practice, isolated |

## Rules

- Do not attack systems you do not own.
- Do not run public internet scans.
- Do not run tests against your ISP or third-party networks.
- Only test against your own lab devices.
- Keep intentionally vulnerable systems isolated.
- Enable firewall rules only when a test requires them, then disable them afterward.

## Detection Scenarios

| Scenario | Tool/Test | Expected Evidence |
|---|---|---|
| Failed SSH login attempts | SSH/manual attempts or Hydra against test target | Wazuh auth alerts |
| Port scan | Nmap from Kali to test server | OPNsense logs, Wazuh/IDS event if enabled |
| Blocked IoT-to-Trusted traffic | Attempt connection from IoT VLAN to Trusted device | OPNsense deny logs |
| Nextcloud failed login | Browser login attempts to cloud subdomain | Nextcloud logs and Wazuh event |
| Jellyfin failed login | Browser login attempts to media subdomain | Jellyfin logs and Wazuh event |
| NGINX suspicious request | Curl/Nikto-style requests against your own service | NGINX logs and Wazuh event |
| File modification | Change monitored config file | Wazuh FIM alert |
| Docker event | Restart container | Docker event/log in Wazuh |
| New Linux user | Create test account on Ubuntu target | Auth/system log alert |
| EICAR test file | Safe test file where AV/FIM applies | Security alert/evidence |

## Test Report Format

Each detection scenario should include:

1. Objective.
2. Target system.
3. Source system.
4. Commands or steps used.
5. Expected result.
6. Actual result.
7. Screenshot of alert/log.
8. Analyst notes.
9. Recommended response.
10. Cleanup steps.
