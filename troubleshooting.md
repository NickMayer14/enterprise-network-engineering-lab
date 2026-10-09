# Troubleshooting case studies

## 1. VLAN trunk mismatch
**Symptom:** Management VLAN/VRRP traffic failed to traverse SW2 uplink.  
**Cause:** Uplink operated in access mode rather than trunk mode.  
**Action:** Reconfigured uplink as an 802.1Q trunk and aligned native VLAN.  
**Lesson:** Verify operational switchport mode and allowed/native VLANs on both ends.

## 2. SNMP monitoring reachability
**Symptom:** Zabbix SNMP polling did not receive a response.  
**Cause:** Monitoring subnet lacked a routed path to the target.  
**Action:** Added the missing firewall route and tested with `snmpwalk`.  
**Lesson:** Check bidirectional routing and firewall policy before troubleshooting SNMP itself.

## 3. Firewall interface addressing conflict
**Symptom:** Secondary router could not reach FortiGate.  
**Cause:** An overlapping/conflicting FortiGate interface address.  
**Action:** Changed to VLAN-based firewall interface design.  
**Lesson:** Audit interface and VLAN addressing during L3 integration.

*These cases summarize the author's lab report. Add sanitized command output and dated test evidence when available.*
