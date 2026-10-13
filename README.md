Hands-on, independently built network engineering lab using physical Cisco routers and switches and a FortiGate 80E firewall. The goal is to practice designing, configuring, validating, monitoring, and troubleshooting a small enterprise-style network.



# Enterprise Network Engineering Lab
![Enterprise Network Topology](Enterprise%20Network%20Engineering%20Lab%20Diagram.png)
**Cisco Routing & Switching · Fortinet FortiGate · VRRP · VLAN Segmentation · Zabbix**

## Network Device Configurations

Cisco IOS and FortiGate configurations covering VLANs,
802.1Q trunking, VRRP, PAgP EtherChannel, SSH, DHCP, and routing.

[View Network Device Configurations](Network_Lab_Updated_Configs_REVIEW.txt)
## Lab at a glance

| Area | Equipment / technology |
| --- | --- |
| Firewall | Fortinet FortiGate 80E |
| Routing | Cisco 2900-series router (R1), Cisco 2821 (R2) |
| Switching | Two Cisco switches (SW1 and SW2) |
| Segmentation | VLANs 10, 20, 30, 40, 50, 60 and management VLAN 99 |
| Resiliency | VRRP first-hop redundancy with physical failover testing |
| Layer 2 | 802.1Q trunks, STP, PortFast, BPDU Guard |
| Layer 3 / security | Static routing, DHCP, NAT, firewall policies |
| Monitoring | Zabbix with SNMPv2 polling and traps |
| Connected system | Network video recorder (NVR) on camera VLAN |

## Physical lab

![Physical network equipment](lab-photo-1.jpg)


[Watch the physical lab walkthrough](media/lab-demo.mp4)

## What I implemented

- Built a segmented Cisco/Fortinet lab with separate networks for users, voice, servers, cameras/NVR, guests, IoT and management.
- Configured VLAN membership and 802.1Q trunks between switching and routing components, plus basic STP edge protections.
- Configured VRRP on two Cisco routers and tested takeover by powering off the primary router.
- Integrated FortiGate routing, NAT, DHCP and security policy functionality.
- Configured SNMPv2 monitoring and Zabbix checks for router/interface status, CPU and VRRP state.
- Investigated configuration faults and documented root causes and fixes.

## Troubleshooting case studies

Problem #1
After configuring PAgP EtherChannel between SW1 and SW2, the Port-channel1 interface failed to establish successfully.
Symptoms
- Port-channel1 remained down or individual interfaces were not bundled.
- Intermittent connectivity between switches.
- Devices connected to SW2 experienced network connectivity issues.
Troubleshooting Commands
show etherchannel summary
show pagp neighbor
show interfaces trunk
show interfaces status
show running-config interface port-channel 1


Root Cause
Both switches were configured with PAgP mode auto. Since neither switch actively initiated negotiation, EtherChannel could not form.
Resolution
Configured SW1 in desirable mode and SW2 in auto mode.
SW1:
interface range GigabitEthernet0/47 - 48
 channel-group 1 mode desirable


SW2:
interface range GigabitEthernet0/47 - 48
 channel-group 1 mode auto


Verification
Executed:
show etherchannel summary


The expected output showed Po1(SU) with member interfaces marked (P), confirming that the EtherChannel was operational.
Skills Demonstrated: Cisco Switching, PAgP, EtherChannel, Layer 2 Troubleshooting.



## About

**Mykyta Mamaiev** · CCNA-certified IT infrastructure professional based in Harrison, New Jersey. Experience supporting enterprise network-connected systems and collaborating with Network Engineering at The New School.

*All content is from my personal lab. No employer configurations, credentials or internal diagrams are included.*
