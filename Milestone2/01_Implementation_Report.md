# Milestone 2 Implementation Report

**Project:** CMPG325-2026-066  
**Client:** CLI-066 Kagiso Mechanical Engineering (Rustenburg)  
**Student:** Mulondi Mbodi (40779750)  
**Review milestone:** 2 October 2026

## 1. Scope

Milestone 2 converts the Milestone 1 draw-only topology into a working Cisco Packet Tracer implementation. The assigned technical challenge is IPv4 VLSM subnetting using `192.168.35.0/24`. The client change request is a new file/application server that is reachable only by authorised departments.

This report records the final device configuration, verification output, test results, screenshots, and troubleshooting history for the completed Milestone 2 implementation.

## 2. Addressing baseline

| VLAN | Purpose | Subnet | Gateway |
|---:|---|---|---|
| 10 | Admin / HR | `192.168.35.32/28` | `192.168.35.33` |
| 20 | Engineering Design | `192.168.35.0/27` | `192.168.35.1` |
| 30 | Production Workshop | `192.168.35.48/28` | `192.168.35.49` |
| 40 | Finance / Procurement | `192.168.35.96/28` | `192.168.35.97` |
| 50 | ICT Technical | `192.168.35.64/28` | `192.168.35.65` |
| 99 | Management / Servers | `192.168.35.112/28` | `192.168.35.113` |
| 100 | Guest WiFi | `192.168.35.80/28` | `192.168.35.81` |
| Core link | R-EDGE to DLS1 routed link | `192.168.35.252/30` | R-EDGE `.253`, DLS1 `.254` |

**Implementation correction:** `192.168.35.112` is the VLAN 99 network address and must not be assigned to DLS1. DLS1 uses `192.168.35.113` as the VLAN 99 SVI/management address. `SVR-DC` uses `192.168.35.114`.

## 3. Per-device configuration summary

| Device | Hostname | Key IP addresses | Services and features |
|---|---|---|---|
| Distribution switch | DLS1 | VLAN 10 `.33/28`, VLAN 20 `.1/27`, VLAN 30 `.49/28`, VLAN 40 `.97/28`, VLAN 50 `.65/28`, VLAN 99 `.113/28`, VLAN 100 `.81/28`, routed link `.254/30` | Inter-VLAN routing, DHCP relay, OSPF Area 0, Rapid-PVST+ root primary, trunks, ACLs, port security |
| Core router | R-EDGE | LAN `.253/30`, WAN address obtained by DHCP where supported | OSPF Area 0, default route, NAT/PAT configuration |
| Admin access switch | ALS-ADM | VLAN 99 management `.115/28` | VLAN 10 access ports, `Gig0/2` trunk to DLS1, PortFast, BPDU Guard, port security |
| Engineering access switch | ALS-ENG | VLAN 99 management `.116/28` | VLAN 20 and 50 access ports, redundant trunks on DLS1 `Fa0/21` and `Fa0/22`, PortFast, BPDU Guard, port security |
| Production access switch | ALS-PRD | VLAN 99 management `.117/28` | VLAN 30 and 100 access ports, `Fa0/23` trunk to DLS1, PortFast, BPDU Guard, port security |
| Finance access switch | ALS-FIN | VLAN 99 management `.118/28` | VLAN 40 access ports, `Fa0/24` native VLAN 999 trunk to DLS1, PortFast, BPDU Guard, port security |
| Domain/DHCP/DNS server | SVR-DC | `192.168.35.114/28`, gateway `.113` | GUI-configured DHCP with seven scopes, GUI-configured DNS, `kagiso.local` record |
| File/application server | SVR-FP | `192.168.35.119/28`, gateway `.113` | GUI-configured HTTP service and file/application endpoint |

## 4. Planned implementation

- Configure VLANs 10, 20, 30, 40, 50, 99, and 100 on DLS1 and access switches.
- Configure access ports and trunk allow-lists according to the Milestone 1 logical topology.
- Configure DLS1 SVIs, `ip routing`, and DHCP relay to `192.168.35.114`.
- Configure the R-EDGE to DLS1 routed `/30` link.
- Configure OSPF Area 0 between R-EDGE and DLS1.
- Configure the default route and PAT on R-EDGE where supported by the Packet Tracer ISP model.
- Configure DHCP scopes on SVR-DC using the VLSM ranges and exclusions.
- Configure the file/application server on VLAN 99 and restrict access with ACLs.
- Configure port security, PortFast, BPDU Guard, Rapid-PVST+, and the Engineering redundant-link failover demonstration.

## 5. Acceptance tests

| ID | Test | Expected result | Evidence |
|---|---|---|---|
| VLSM-01 | Verify VLAN 20 mask, network, gateway, and broadcast | `192.168.35.0/27`, gateway `.1`, broadcast `.31` | PASS - `26_dls1_vlsm_svis_and_helpers.png` |
| VLSM-02 | Verify all seven VLAN ranges do not overlap | All ranges remain inside `192.168.35.0/24` | PASS - `26_dls1_vlsm_svis_and_helpers.png` |
| VLSM-03 | Obtain DHCP leases in representative client VLANs | Correct subnet, gateway, and DNS are received | PASS - `01_engineering_dhcp.png`, `02_admin_dhcp.png`, `08_guest_dhcp.png`, `09_finance_dhcp.png` |
| ROUTE-01 | Verify OSPF neighbour and learned routes | R-EDGE and DLS1 form an Area 0 adjacency | PASS - `11_dls1_ospf.png`, `12_dls1_route.png` |
| CORE-01 | Ping an authorised host across VLANs | ICMP succeeds where policy permits | PASS - `03_engineering_server_ping.png` |
| SERVER-01 | Access the file/application server from Engineering | Authorised access succeeds | PASS - `03_engineering_server_ping.png` |
| SERVER-02 | Access the file/application server from Production | Access is denied | PASS - `04_production_server_ping_denied.png` |
| SEC-01 | Attempt Finance/Production access to the server | ACL blocks the traffic | PASS - `04_production_server_ping_denied.png`, `13_dls1_acls.png` |
| SEC-02 | Verify management ACL policy | Non-authorised management traffic is denied | PASS - `13_dls1_acls.png` |
| STP-01 | Verify the Engineering redundant uplink state | STP identifies the root and forwarding/alternate paths | PASS - `15_stp_before_failover_part1.png`, `16_stp_before_failover_part2.png`, `17_stp_before_failover_part3.png`, `18_stp_after_failover_part1.png` |
| NAT-01 | Test permitted Internet access | PAT translates internal traffic where ISP simulation supports it | NOT VERIFIED - Cloud-PT supplied no usable WAN address/next hop |

## 6. Evidence register

Screenshots are stored under `evidence/` and indexed in [evidence/README.md](../evidence/README.md).

- [x] Final Packet Tracer topology and saved M2 file - `00_final_topology.png`
- [x] VLSM subnet and gateway verification - `01_engineering_dhcp.png`
- [x] All seven VLSM SVIs, masks, DHCP helpers, and ACL attachments - `26_dls1_vlsm_svis_and_helpers.png`
- [x] DHCP leases for representative VLANs - `01_engineering_dhcp.png`, `02_admin_dhcp.png`
- [x] Guest and Finance DHCP leases - `08_guest_dhcp.png`, `09_finance_dhcp.png`
- [x] ALS-ADM VLAN and trunk verification - `25_als_adm_vlan_and_trunk.png`
- [x] `show vlan brief` - `08_dls1_vlan_brief.png`
- [x] `show interfaces trunk` - `09_dls1_trunks.png`
- [x] `show ip interface brief` - `10_dls1_ip_brief.png`
- [x] `show ip route` and OSPF neighbour state - `11_dls1_ospf.png`, `12_dls1_route.png`
- [x] Successful authorised server access - `03_engineering_server_ping.png`
- [x] Failed unauthorised server access - `04_production_server_ping_denied.png`
- [x] ACL hit counters after negative tests - `13_dls1_acls.png`
- [x] STP root and failover evidence - `15_stp_before_failover_part1.png`, `16_stp_before_failover_part2.png`, `17_stp_before_failover_part3.png`, `18_stp_after_failover_part1.png`
- [x] Port-security evidence - `19_dls1_port_security.png`, `20_port_security_violation_detected.png`, `21_port_security_violation_status.png`
- [x] DHCP, DNS, and HTTP service screens - `05_svr_dc_dhcp.png`, `06_svr_dc_dns.png`, `07_svr_fp_http.png`

## 7. Security policy: final ACLs

The seven named inbound ACLs below implement the CR4 access policy. The entries are listed in final IOS sequence order.

| ACL | Final entries | Effect |
|---|---|---|
| `ACL-ENG-IN` | `10 deny ip any 192.168.35.96 0.0.0.15`; `20 permit ip any host 192.168.35.119`; `30 permit ip any any` | Engineering cannot reach Finance and can reach the file server |
| `ACL-ADMIN-IN` | `10 deny ip any 192.168.35.96 0.0.0.15`; `20 permit ip any host 192.168.35.119`; `30 permit ip any any` | Admin cannot reach Finance and can reach the file server |
| `ACL-FIN-IN` | `10 deny ip any 192.168.35.0 0.0.0.255`; `20 permit ip any host 192.168.35.119`; `30 permit ip any any` | Finance internal-access policy |
| `ACL-PRD-IN` | `10 deny ip any host 192.168.35.119`; `20 deny ip any 192.168.35.96 0.0.0.15`; `30 permit ip any any` | Production is denied the file server and Finance |
| `ACL-ICT-IN` | `10 deny ip any 192.168.35.96 0.0.0.15`; `20 permit ip any 192.168.35.112 0.0.0.15`; `30 permit ip any any` | ICT cannot reach Finance and may reach management/server VLAN 99 |
| `ACL-MGMT-IN` | `10 permit ip 192.168.35.64 0.0.0.15 any`; `20 permit ip 192.168.35.112 0.0.0.15 any`; `30 deny ip any any` | Only ICT and management/server traffic is permitted |
| `ACL-GUEST-IN` | `10 deny ip any 192.168.35.0 0.0.0.255`; `20 permit ip any any` | Guest traffic is denied internal project networks |

The ACL definitions and counters are shown in `evidence/M2_config_screenshots/13_dls1_acls.png`.

## 8. Troubleshooting log

| Date | Problem | Cause | Resolution | Evidence |
|---|---|---|---|---|
| 2026-10-01 | Engineering clients received APIPA addresses | Incorrect DHCP pool/gateway values and stale client lease state | Corrected the Engineering scope to `192.168.35.0/27` with gateway `192.168.35.1`, then renewed DHCP | `01_engineering_dhcp.png` |
| 2026-10-01 | SVR-DC could not reach the routed network | VLAN 99 ACL did not permit the management/server subnet | Corrected the management ACL policy and verified reachability | `13_dls1_acls.png`, `12_dls1_route.png` |
| 2026-10-01 | Trunk and cable labels did not match the worksheet | Physical Packet Tracer cabling used different switch-port labels | Updated the worksheet to match the actual ports and retained the redundant Engineering uplinks for STP testing | `00_final_topology.png`, `09_dls1_trunks.png` |
| 2026-10-01 | DLS1 rejected trunk configuration | Catalyst 3560 required explicit 802.1Q trunk encapsulation | Added `switchport trunk encapsulation dot1q` before `switchport mode trunk` | `09_dls1_trunks.png` |
| 2026-10-01 | Native VLAN mismatch appeared on trunks | Worksheet labels did not match the physical cable mapping | Set the Engineering trunk native VLAN to 1 and Finance trunk native VLAN to 999 | `09_dls1_trunks.png`, `09_als_fin_vlan_and_trunks.png` |
| 2026-10-01 | ACL deny rules were ineffective | IOS sequence ordering placed permit rules before deny rules | Recreated the ACLs with deny entries first and verified hit counters | `13_dls1_acls.png` |
| 2026-10-01 | SVR-DC could not reach DLS1 | Management ACL did not initially permit the server/management subnet | Corrected `ACL-MGMT-IN` and verified the routed path | `12_dls1_route.png`, `13_dls1_acls.png` |
| 2026-10-01 | ALS-ADM uplink did not match the worksheet | The access switch used `Gi0/1` to DLS1 `Gig0/2`, not the earlier assumed port pair | Corrected the worksheet and Gig0/2 trunk configuration | `09_dls1_trunks.png` |
| 2026-10-01 | Port-security violation required evidence | A test device/unknown MAC triggered the configured security policy | Captured the detected violation and resulting port status | `20_port_security_violation_detected.png`, `21_port_security_violation_status.png` |

## 9. Final status

- R-EDGE Gi0/0 and Gi0/1: **Enabled and up**
- OSPF R-EDGE/DLS1 adjacency: **FULL**
- R-EDGE learned seven VLSM VLAN routes: **Verified**
- R-EDGE default route: **Configured in running configuration; Cloud-PT supplied no WAN address or next-hop, so the route is not installed as an active route**
- Packet Tracer implementation: **Complete and saved as `CLI066_Mbodi_40779750_M2_CONFIGURED.pkt`**
- Assigned VLSM feature: **Implemented and verified across the VLAN/SVI addressing plan**
- Services and ACLs: **Implemented and verified with DHCP, DNS, HTTP, authorised access, and denied access tests**
- Testing evidence: **Captured under `evidence/M2_config_screenshots/` and `evidence/M2_service_tests/`**
- Evidence index: **Available at `evidence/README.md`**
- GitHub portfolio update: **Ready for publication**

## 10. Tested, passed, and not tested

Passed tests include VLSM addressing, representative DHCP leases, VLAN and trunk state, OSPF adjacency, internal routing, DNS and HTTP service availability, authorised Engineering server access, denied Production server access, ACL counters, STP failover/restoration, and port-security violation detection. The DHCP, DNS, and HTTP services were configured through Packet Tracer GUI screens, while routing, switching, STP, and ACLs were configured through IOS CLI.

NAT/PAT could not be fully tested because the Packet Tracer Cloud-PT handoff did not provide a usable WAN address or ISP next hop. This limitation is documented below rather than reported as a false pass.

## 11. Packet Tracer WAN limitation

The simulated Cloud-PT handoff brought R-EDGE `GigabitEthernet0/0` to an up/up state but did not supply a DHCP address or ISP next-hop. Packet Tracer also rejected `default-information originate always` and `ip route 0.0.0.0 0.0.0.0 dhcp` on the selected IOS image. A supported interface-based default route and the OSPF `default-information originate` command were retained in the running configuration, while the absence of an active Internet default route is recorded rather than presented as a passing Internet test. Internal OSPF reachability to all seven VLSM VLANs was verified successfully.
