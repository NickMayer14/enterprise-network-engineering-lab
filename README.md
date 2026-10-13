# Cisco PAgP EtherChannel + DHCP Server/Relay Lab Extension

This is an **optional, reproducible configuration extension**, not evidence that the current live lab has these features enabled. Existing VLANs, VRRP, Zabbix and FortiGate configuration must be preserved. **Do not paste every file blindly.**

## Existing lab assumptions verified from the repository

- SW1 and SW2 are Cisco switches with an 802.1Q inter-switch trunk.
- VLANs: 10 Users (10.10.10.0/24), 20 Voice (10.10.20.0/24), 30 Servers (10.10.30.0/24), 40 NVR (10.10.40.0/24), 50 Guests (10.10.50.0/24), 60 IoT (10.10.60.0/24), 99 Management (10.99.99.0/24).
- Native VLAN 99 was described in the original lab report; confirm the current running config.
- R1 management IP 10.99.99.2, R2 10.99.99.3; VRRP VIP 10.99.99.254 is documented **only for VLAN 99**.
- FortiGate 80E already provides DHCP according to README; do not introduce competing DHCP servers on the same VLAN.
- Zabbix 10.10.200.10 connects directly to FortiGate. This extension does not change Zabbix.

## File index

1. `01-SW1-PAgP.cfg` and `02-SW2-PAgP.cfg`: example IOS PAgP desirable/auto trunk EtherChannel. **Example ports Gi0/1 and Gi0/2 are placeholders**; use the actual two SW1–SW2 physical links. In the original report SW1 Gi0/1 was assigned to R1, so **do not use Gi0/1 on SW1 without remapping**.
2. `03-DHCP-SERVER-R1-OPTION.cfg`: optional DHCP pools hosted on R1 IOS. This is a proposed design, not the existing FortiGate DHCP service. Use only if you intentionally move DHCP serving to R1 and confirm L3 routing.
3. `04-DHCP-RELAY-GATEWAY-TEMPLATE.cfg`: relay configuration on whichever L3 interfaces are the **actual client VLAN default gateways**. Replace interface names and gateway addresses with verified values; do not assume VLAN 99 VRRP applies to client VLANs.
4. `05-VERIFICATION.md`: validation, rollout and rollback.

## DHCP design choice

The existing README says **FortiGate provides DHCP**. In that existing design, if FortiGate is also the directly connected gateway for a VLAN, **DHCP relay is unnecessary**. If the VLAN gateway is on a Cisco L3 interface and the DHCP server is elsewhere, add `ip helper-address` on that gateway and ensure routing both ways.

For an explicit *DHCP Server + Relay* exercise without introducing a new physical server, this package uses **R1 as an optional IOS DHCP server at 10.99.99.2** and relay agents on the client VLAN gateways. This is an **alternative architecture** to the existing FortiGate DHCP setup. Before using it, disable overlapping FortiGate scopes for the affected VLANs, verify return routes to each subnet, and check IOS feature support.

**High availability caveat:** A single R1 DHCP server is not redundant when R1 fails. VRRP on VLAN 99 does not provide DHCP server HA. Production deployments need a separate resilient DHCP design.

## Suggested README addition (after Switching paragraph)

```markdown
### PAgP EtherChannel and DHCP Relay (Optional Lab Extension)

A reproducible configuration example for a Cisco PAgP EtherChannel (`Port-channel1`) between SW1 and SW2, plus an alternative Cisco IOS DHCP server and relay design, is provided in [network-lab-additions](network-lab-additions/README.md). The extension includes configuration templates, implementation prerequisites, verification commands, and rollback steps. These features should be marked **implemented** only after validation on physical equipment.
```

Once EtherChannel is tested, replace the planned LACP bullet with an accurate PAgP implementation note. PAgP is Cisco-proprietary; LACP is a separate protocol and should not be described as already configured.
