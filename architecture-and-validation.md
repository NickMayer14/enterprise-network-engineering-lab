# Architecture and validation notes

## Physical components
- FortiGate 80E firewall
- Cisco 2900-series R1 and Cisco 2821 R2
- Cisco SW1 and SW2
- Zabbix monitoring server and NVR

## VLAN plan (from lab report)
| VLAN | Reported subnet | Intended use |
| --- | --- | --- |
| 10 | 10.10.10.0/24 | Users |
| 20 | 10.10.20.0/24 | Voice |
| 30 | 10.10.30.0/24 | Servers |
| 40 | 10.10.40.0/24 | NVR / cameras |
| 50 | 10.10.50.0/24 | Guests |
| 60 | 10.10.60.0/24 | IoT |
| 99 | 10.99.99.0/24 | Management / VRRP |

## Technical verification pending
1. The report places Zabbix in `10.10.200.0/24`, while the VLAN table lists Servers as VLAN 30 / `10.10.30.0/24`. Verify which subnet was actually configured.
2. The report refers to FortiGate as the default gateway for VLANs, while also describing a VRRP VIP of `10.99.99.254` on VLAN 99. Verify client default gateways, routing adjacency, and which traffic paths benefit from VRRP.
3. Confirm switch/router interface modes and any L2 assumptions from running configuration before publishing a definitive topology drawing.
4. A primary-router power-off test demonstrates VRRP state takeover, but verify packet loss, reachability and application continuity separately.

## Validation evidence to add
- Sanitized `show vrrp brief` / `show vrrp` outputs before and after failover
- Sanitized `show interfaces trunk` and `show vlan brief`
- Redacted Zabbix dashboard screenshots
- Ping/traceroute results from test clients
- Sanitized FortiGate routing and policy excerpts

**Security:** Never publish live credentials, SNMP community strings, management access tokens, device serial numbers or employer infrastructure details.
