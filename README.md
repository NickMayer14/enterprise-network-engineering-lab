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

![Physical network equipment](lab-photo-1.jpg.heic)


[Watch the physical lab walkthrough](media/lab-demo.mp4)

## What I implemented

- Built a segmented Cisco/Fortinet lab with separate networks for users, voice, servers, cameras/NVR, guests, IoT and management.
- Configured VLAN membership and 802.1Q trunks between switching and routing components, plus basic STP edge protections.
- Configured VRRP on two Cisco routers and tested takeover by powering off the primary router.
- Integrated FortiGate routing, NAT, DHCP and security policy functionality.
- Configured SNMPv2 monitoring and Zabbix checks for router/interface status, CPU and VRRP state.
- Investigated configuration faults and documented root causes and fixes.



## 🛠️ Network Troubleshooting & Problem Resolution

This section documents two hands-on troubleshooting scenarios designed around my Enterprise Network Engineering Lab, featuring Cisco routers and switches, VLAN segmentation, PAgP EtherChannel, and VRRP high availability.

> **Lab Status:** These case represent planned troubleshooting exercises based on the lab topology. Final results and command outputs will be documented after testing.

---

### 🔗 Case 01: PAgP EtherChannel Negotiation Failure

**Category:** Layer 2 Switching | **Technologies:** Cisco IOS, PAgP, EtherChannel, 802.1Q

#### 🚨 Problem Description

During the configuration of a redundant inter-switch connection between **SW1 and SW2**, the EtherChannel bundle failed to establish successfully.

The expected `Port-channel1` interface did not become operational, preventing the physical links from functioning as a single logical connection.

#### 🔍 Symptoms

- EtherChannel member interfaces were not bundled successfully.
- `Port-channel1` was down or not operating as expected.
- Connectivity between switches was potentially affected.
- Link aggregation and redundancy were unavailable.

#### 🧪 Troubleshooting & Investigation

The following Cisco IOS commands were selected to investigate the issue:

```cisco
show etherchannel summary
show pagp neighbor
show interfaces trunk
show interfaces status
show running-config interface port-channel 1
```

**Diagnostic focus:**

1. Verify EtherChannel operational status.
2. Confirm PAgP negotiation between neighboring switches.
3. Compare channel-group configuration on both devices.
4. Verify trunk settings and VLAN consistency.
5. Identify interfaces that failed to join the bundle.

#### 🎯 Root Cause

**Simulated misconfiguration:** Both switches were configured in PAgP `auto` mode.

PAgP `auto` is passive. When both ends use `auto`, neither switch initiates negotiation, so the EtherChannel does not form.

| Device | Incorrect Mode | Correct Mode |
|---|---|---|
| SW1 | `auto` | `desirable` |
| SW2 | `auto` | `auto` |

#### 🔧 Corrective Configuration

**SW1:**

```cisco
configure terminal
interface range GigabitEthernet0/47 - 48
 channel-group 1 mode desirable
exit
interface Port-channel1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,50,60,99
end
```

**SW2:**

```cisco
configure terminal
interface range GigabitEthernet0/47 - 48
 channel-group 1 mode auto
exit
interface Port-channel1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,50,60,99
end
```

*Interface numbers Gi0/47–48 are proposed lab ports and must be verified against the actual cabling and switch hardware.*

#### ✅ Expected Verification

```cisco
show etherchannel summary
show pagp neighbor
show interfaces trunk
```

Expected EtherChannel status:

```text
Group  Port-channel  Protocol  Ports
1      Po1(SU)       PAgP      Gi0/47(P) Gi0/48(P)
```

**Expected outcome:** Port-channel1 becomes operational, both physical links join the bundle, and the required VLANs are forwarded across the trunk.

**Skills demonstrated:** EtherChannel troubleshooting, PAgP negotiation, Cisco switching, trunk configuration, Layer 2 fault isolation.

---

### 🔄 Case 02: VRRP Gateway Failover Failure

**Category:** Layer 3 Routing | **Technologies:** Cisco IOS, VRRP, High Availability, Gateway Redundancy

#### 🚨 Problem Description

The lab uses two Cisco routers, **R1 and R2**, to provide gateway redundancy.

During a simulated primary-router failure, the backup router was expected to assume the active gateway role. However, inconsistent VRRP settings prevented the intended failover behavior.

#### 🔍 Symptoms

- Loss of connectivity when the primary router became unavailable.
- Backup router did not assume the expected VRRP Master role.
- The virtual gateway became unreachable.
- High availability was not functioning as designed.

#### 🧪 Troubleshooting & Investigation

```cisco
show vrrp brief
show vrrp
show ip interface brief
show running-config | section interface
show ip route
```

**Diagnostic focus:**

1. Check VRRP Master and Backup states.
2. Compare VRRP group numbers on R1 and R2.
3. Verify that both routers use the same virtual IP.
4. Confirm interface addressing and Layer 2 connectivity.
5. Review router priorities and preemption settings.

#### 🎯 Root Cause

**Configuration inconsistency identified in the saved lab configuration:**

- R2 included VRRP group `1` with virtual IP `10.99.99.254`.
- R1 had a VRRP configuration for VLAN 10 but did not include the matching VRRP group on its `10.99.99.2/24` interface in the saved configuration.

This mismatch could prevent the intended redundancy for the `10.99.99.0/24` transit network.

#### 🔧 Corrective Configuration

**R1: Primary Router**

```cisco
configure terminal
interface GigabitEthernet0/1
 ip address 10.99.99.2 255.255.255.0
 vrrp 1 ip 10.99.99.254
 vrrp 1 priority 150
 vrrp 1 preempt
 no shutdown
end
```

**R2: Backup Router**

```cisco
configure terminal
interface GigabitEthernet0/1
 ip address 10.99.99.3 255.255.255.0
 vrrp 1 ip 10.99.99.254
 vrrp 1 priority 100
 vrrp 1 preempt
 no shutdown
end
```

#### 📊 Expected VRRP Roles

| Parameter | R1 (Primary) | R2 (Backup) |
|---|---|---|
| Physical IP | `10.99.99.2` | `10.99.99.3` |
| VRRP Group | `1` | `1` |
| Virtual IP | `10.99.99.254` | `10.99.99.254` |
| Priority | `150` | `100` |
| Normal Role | Master | Backup |
| Preemption | Enabled | Enabled |

#### ✅ Expected Verification

```cisco
show vrrp brief
ping 10.99.99.254
```

**Failover test procedure:**

1. Confirm R1 is Master and R2 is Backup.
2. Start continuous ping tests to the VRRP virtual IP.
3. Simulate failure of the active router or its relevant interface.
4. Confirm that R2 transitions to Master.
5. Restore R1 and verify the expected preemption behavior.

**Expected outcome:** R2 assumes the Master role after R1 becomes unavailable, maintaining reachability to the shared virtual gateway.

*Full client traffic failover also depends on the routing and gateway configuration for the other VLANs.*

**Skills demonstrated:** VRRP configuration, router redundancy, gateway failover, Layer 3 troubleshooting, high-availability validation.

---

### 📋 Troubleshooting Skills Summary

| Scenario | Networking Concepts | Troubleshooting Focus |
|---|---|---|
| **01: EtherChannel Failure** | PAgP, Layer 2, 802.1Q | Negotiation and trunk consistency |
| **02: VRRP Failover Failure** | VRRP, Layer 3, redundancy | Virtual gateway and failover behavior |

**Lab Environment:** Cisco R1/R2, Cisco SW1/SW2, FortiGate 80E, segmented VLAN architecture.

**Documentation approach:** Configuration analysis → fault isolation → corrective configuration → verification testing.



## About

**Mykyta Mamaiev** · CCNA-certified IT infrastructure professional based in Harrison, New Jersey. Experience supporting enterprise network-connected systems and collaborating with Network Engineering at The New School.

*All content is from my personal lab. No employer configurations, credentials or internal diagrams are included.*
