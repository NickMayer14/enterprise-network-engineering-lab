# Enterprise Network Engineering Lab | Configuration Examples

**Status:** Reconstructed Cisco IOS templates, NOT exported running-configs. Based on the public project documentation and user-supplied lab details. Optional PAgP and DHCP relay are proposed changes, not proof of deployment.

## Devices
- FortiGate 80E: 10.99.99.1/24; firewall, NAT, DHCP in documented lab. FortiOS configuration is not invented here.
- R1 Cisco 2900 series: 10.99.99.2/24, VRRP 99 priority 150.
- R2 Cisco 2821: 10.99.99.3/24, VRRP 99 priority 100.
- SW1: 10.99.99.11; SW2: 10.99.99.12.
- VRRP VIP 10.99.99.254/24, VLAN 99 only.
- Zabbix: 10.10.200.10/24, directly connected to FortiGate on an unspecified LAN port; gateway IP and VLAN ID unverified.

## VLANs
| VLAN | Function | Network |
|---|---|---|
| 10 | Users | 10.10.10.0/24 |
| 20 | Voice | 10.10.20.0/24 |
| 30 | Servers | 10.10.30.0/24 |
| 40 | NVR / cameras | 10.10.40.0/24 |
| 50 | Guests | 10.10.50.0/24 |
| 60 | IoT / secure | 10.10.60.0/24 |
| 99 | Management / VRRP | 10.99.99.0/24 |

## Topology caution
The report documents two router-facing trunks on **SW1** (Gi0/1 -> R1, Gi0/3 -> R2) and a SW1-SW2 trunk. Do not assume R2 is physically connected to SW2. Router-to-FortiGate links are described but exact ports and routing interfaces are not verified. The native VLAN 99 is explicitly documented. VLAN 10-60 gateway addresses and routing architecture have not been validated, so those L3 interfaces are intentionally omitted rather than fabricated.

## PAgP EtherChannel
- SW1 `desirable`, SW2 `auto`, `Port-channel1`, trunk allowing 10,20,30,40,50,60,99, native VLAN 99.
- Replace placeholders with **two verified unused switch-to-switch ports** on each side.
- Configure both ends in a maintenance window. PAgP support is platform-dependent.
- Existing SW1-SW2 trunk must be migrated safely, not left as a parallel forwarding link that could cause a loop.

## DHCP
The original README describes DHCP on FortiGate, but not validated relay placement. `configs/DHCP-relay-OPTIONAL-template.cfg` is a design example. DHCP relay must be on the actual client VLAN L3 gateway, with a reachable server and matching scopes; it must not be blindly pasted onto both routers.

## Files
- `configs/R1-reconstructed-ios.cfg`
- `configs/R2-reconstructed-ios.cfg`
- `configs/SW1-reconstructed-ios.cfg`
- `configs/SW2-reconstructed-ios.cfg`
- `configs/DHCP-relay-OPTIONAL-template.cfg`
- `docs/VERIFY.md`

**Do not label these as captured running-configs until you obtain and sanitize actual device output.**
