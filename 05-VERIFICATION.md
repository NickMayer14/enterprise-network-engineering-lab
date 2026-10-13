# Deployment, validation, and rollback

## Preflight

1. Capture sanitized `show running-config`, `show interfaces status`, `show interfaces trunk`, `show vlan brief`, `show spanning-tree`, `show etherchannel summary` from both switches.
2. Identify **two dedicated SW1–SW2 physical links**. The current repository documents one trunk, not a second cable. **Do not repurpose a router uplink**; SW1 Gi0/1 was identified as a router uplink in the original report.
3. Verify both switches support PAgP and `channel-group` on chosen ports; check platform-specific syntax. Use same speed/duplex, native VLAN, allowed VLAN list and trunk mode.
4. Use console/out-of-band access and a maintenance window. Stage SW2 and SW1 carefully; avoid two unbundled parallel L2 links unless STP is known healthy.
5. Confirm native VLAN 99 and VLAN list against actual running configs. Never paste template port IDs unchanged.

## PAgP checks

```text
show etherchannel summary
show etherchannel 1 detail
show pagp neighbor
show interfaces port-channel 1 trunk
show interfaces trunk
show spanning-tree vlan 99
show interfaces status
```

Expected: `Po1(SU)` (or equivalent for the platform) and member interfaces marked `P`; trunk carries 10,20,30,40,50,60,99; VLAN 99 remains reachable. Unplug one **member** link and verify traffic still passes, then reconnect it.

## DHCP preflight

- Determine the real default gateway for VLANs 10–60. VLAN 99 VRRP does **not** establish gateways for VLANs 10–60.
- Identify the existing FortiGate DHCP scopes and **do not run two servers for the same scope**.
- For the optional IOS R1 DHCP server design, verify routing from each VLAN gateway to `10.99.99.2` and reverse routes from R1 back to each VLAN subnet.
- Confirm client VLANs need DHCP (server/NVR/IoT VLANs may be statically assigned).
- Confirm firewall policies allow the required traffic and relay reaches the server.

## DHCP checks (Cisco IOS)

```text
show ip dhcp pool
show ip dhcp binding
show ip dhcp server statistics
show running-config | section dhcp
show ip interface vlan 10
show ip route
```

On router subinterfaces use `show ip interface <interface>`. Test a client in each enabled VLAN with a new DHCP lease; check assigned subnet, default gateway, DNS, ping gateway, and cross-VLAN policy behavior.

## Rollback

For the EtherChannel, **first verify the original working single-link trunk configuration**. During maintenance, restore the original inter-switch trunk and remove `channel-group 1` from the newly assigned member interfaces. Remove `interface Port-channel1` only when no longer used; avoid removing an existing shared Port-channel by mistake.

For DHCP, remove the newly introduced `ip helper-address 10.99.99.2` from each changed gateway, remove only the **newly created** IOS DHCP pools, and restore original FortiGate DHCP scopes if you had disabled them. Save configurations only after tests pass.

## Documentation / evidence

Publish sanitized `show etherchannel summary`, `show pagp neighbor`, `show interfaces trunk`, DHCP binding screenshots/outputs, client test results, and a topology diagram updated to show two SW1–SW2 member links into Po1. Do not publish passwords, real SNMP community strings, or employer configurations.
