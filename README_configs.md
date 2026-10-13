# Enterprise Network Engineering Lab | Configuration 


## Devices
- FortiGate 80E: 10.99.99.1/24; 
- R1 Cisco 2900 series: 10.99.99.2/24, VRRP 99 priority 150.
- R2 Cisco 2821: 10.99.99.3/24, VRRP 99 priority 100.
- SW1: 10.99.99.11; SW2: 10.99.99.12.
- VRRP VIP 10.99.99.254/24, VLAN 99 only.
- Zabbix: 10.10.200.10/24, directly connected to FortiGate ; 

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


## Files
- `configs/R1-reconstructed-ios.cfg`
- `configs/R2-reconstructed-ios.cfg`
- `configs/SW1-reconstructed-ios.cfg`
- `configs/SW2-reconstructed-ios.cfg`
- `configs/DHCP-relay-OPTIONAL-template.cfg`
- `docs/VERIFY.md`

**Do not label these as captured running-configs until you obtain and sanitize actual device output.**
