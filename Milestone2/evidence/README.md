# Milestone 2 Evidence Index

This folder contains the screenshots supporting the configured Packet Tracer implementation. The configuration screenshots document device state and verification commands. The service-test screenshots document DHCP, server access, DNS, and HTTP results.

## `M2_config_screenshots/`

| Filename | What it shows |
|---|---|
| `00_final_topology.png` | Final Packet Tracer topology with devices and cabling |
| `08_dls1_vlan_brief.png` | DLS1 VLAN database and port assignments |
| `09_dls1_trunks.png` | DLS1 trunk status, allowed VLANs, and forwarding state |
| `09_als_eng_vlan_and_trunks.png` | ALS-ENG VLAN assignments and both Engineering trunk links |
| `10_als_prd_vlan_and_trunks.png` | ALS-PRD VLAN assignments and Production trunk |
| `11_als_fin_vlan_and_trunks.png` | ALS-FIN VLAN assignments and Finance trunk |
| `10_dls1_ip_brief.png` | DLS1 SVI and routed-interface status |
| `12_redge_ip_interface_brief.png` | R-EDGE interface status and addressing |
| `11_dls1_ospf.png` | DLS1 OSPF neighbor verification |
| `13_redge_ospf_neighbor.png` | R-EDGE OSPF neighbor verification |
| `12_dls1_route.png` | DLS1 routing table and learned/default routes |
| `14_redge_routing_table.png` | R-EDGE routing table |
| `13_dls1_acls.png` | DLS1 named ACL definitions and counters |
| `15_stp_before_failover_part1.png` | STP state before Engineering failover, part 1 |
| `16_stp_before_failover_part2.png` | STP state before Engineering failover, part 2 |
| `17_stp_before_failover_part3.png` | STP state before Engineering failover, part 3 |
| `18_stp_after_failover_part1.png` | STP state after the active Engineering uplink was disconnected, part 1 |
| `18_stp_after_failover_part2.png` | STP state after failover, part 2 |
| `18_stp_after_failover_part3.png` | STP state after failover, part 3 |
| `19_dls1_port_security.png` | DLS1 port-security configuration/status |
| `20_port_security_violation_detected.png` | Port-security violation detected during testing |
| `21_port_security_violation_status.png` | Port status after the port-security violation |
| `22_stp_restored_part1.png` | STP state after the original topology was restored, part 1 |
| `23_stp_restored_part2.png` | Restored STP state, part 2 |
| `24_stp_restored_part3.png` | Restored STP state, part 3 |
| `26_dls1_vlsm_svis_and_helpers.png` | DLS1 VLSM SVI addresses, masks, DHCP helpers, and ACL attachments |

## `M2_service_tests/`

| Filename | What it shows |
|---|---|
| `01_engineering_dhcp.png` | Engineering PC DHCP lease, gateway, and DNS values |
| `02_admin_dhcp.png` | Admin PC DHCP lease, gateway, and DNS values |
| `03_engineering_server_ping.png` | Authorised Engineering access to the file/application server |
| `04_production_server_ping_denied.png` | Denied Production access to the file/application server |
| `05_svr_dc_dhcp.png` | SVR-DC DHCP service and configured scopes |
| `06_svr_dc_dns.png` | SVR-DC DNS service and `kagiso.local` record |
| `07_svr_fp_http.png` | SVR-FP HTTP service enabled |
| `08_guest_dhcp.png` | Guest VLAN 100 DHCP lease, gateway, and DNS values |
| `09_finance_dhcp.png` | Finance VLAN 40 DHCP lease, gateway, and DNS values |

## Additional configuration evidence

The Admin access-switch verification is stored in `M2_config_screenshots/25_als_adm_vlan_and_trunk.png` and shows VLAN 10 plus the `Gig0/2` trunk.
