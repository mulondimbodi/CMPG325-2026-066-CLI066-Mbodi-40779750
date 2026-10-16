# CMPG 325 Final Technical Report

**Project:** CMPG325-2026-066  
**Client:** CLI-066 Kagiso Mechanical Engineering  
**Student:** Mulondi Mbodi (40779750)  
**Institution:** North-West University, Mahikeng Campus  
**Academic year:** 2026

## 1. Executive summary

This report presents the final implementation of a three-tier extended-star Cisco campus network for Kagiso Mechanical Engineering. The implementation uses IPv4 VLSM addressing, departmental VLANs, inter-VLAN routing, DHCP relay, OSPF, ACL-based security, Rapid-PVST+ redundancy, port security, DNS, and an internal HTTP/file application server.

The working Packet Tracer project is `CLI066_Mbodi_40779750_M3_FINAL.pkt`. Supporting configuration records and evidence are stored in `Milestone2/`.

## 2. Requirements and design

The design supports Admin/HR, Engineering Design, Production, Finance/Procurement, ICT Technical, Management/Servers, and Guest WiFi. The topology contains one edge router, one Layer 3 distribution switch, four access switches, servers, end-user devices, printers, and a redundant Engineering uplink.

The assigned IPv4 block is `192.168.35.0/24`. Largest-first VLSM allocation provides Engineering with the largest subnet and separate /28 networks for the remaining VLANs.

## 3. Addressing plan

| VLAN | Purpose | Network | Gateway |
|---:|---|---|---|
| 10 | Admin / HR | `192.168.35.32/28` | `192.168.35.33` |
| 20 | Engineering Design | `192.168.35.0/27` | `192.168.35.1` |
| 30 | Production Workshop | `192.168.35.48/28` | `192.168.35.49` |
| 40 | Finance / Procurement | `192.168.35.96/28` | `192.168.35.97` |
| 50 | ICT Technical | `192.168.35.64/28` | `192.168.35.65` |
| 99 | Management / Servers | `192.168.35.112/28` | `192.168.35.113` |
| 100 | Guest WiFi | `192.168.35.80/28` | `192.168.35.81` |
| Core link | R-EDGE to DLS1 | `192.168.35.252/30` | `.253` / `.254` |

## 4. Implementation summary

DLS1 provides the seven SVIs, DHCP relay to `192.168.35.114`, inter-VLAN routing, OSPF Area 0, Rapid-PVST+ root-primary operation, trunking, and ACL enforcement. R-EDGE provides the routed core link, OSPF, default-route configuration, and NAT/PAT configuration.

Access-switch uplink mapping was verified with CDP:

- ALS-ADM `Gi0/1` to DLS1 `Gig0/2`
- ALS-ENG `Gi0/1` to DLS1 `Fa0/21`
- ALS-ENG `Gi0/2` to DLS1 `Fa0/22`
- ALS-PRD `Gi0/1` to DLS1 `Fa0/23`
- ALS-FIN `Gi0/1` to DLS1 `Fa0/24`

SVR-DC is configured through the Packet Tracer GUI with static address `192.168.35.114/28`, DHCP, and DNS. SVR-FP is configured through the GUI with static address `192.168.35.119/28` and HTTP enabled.

## 5. Assigned networking feature

The assigned feature is IPv4 VLSM subnetting. It is implemented through the seven non-overlapping VLAN networks, correct SVI masks and gateways, and DHCP scopes matching each VLSM range. DHCP leases were verified for Engineering, Admin, Guest, and Finance.

The client change request is enforced with seven named inbound ACLs. Engineering, Admin, and ICT are authorised for appropriate server access; Production, Finance, and Guest traffic is restricted according to the CR4 policy.

## 6. Validation results

| Test | Result |
|---|---|
| VLSM subnet and SVI verification | PASS |
| Engineering, Admin, Guest, and Finance DHCP leases | PASS |
| VLAN and trunk verification | PASS |
| OSPF neighbor and routing verification | PASS |
| Engineering authorised server access | PASS |
| Production denied server access | PASS |
| ACL counters and policy verification | PASS |
| STP redundant-link failover and restoration | PASS |
| Port-security violation detection | PASS |
| DNS and HTTP service verification | PASS |
| NAT/PAT Internet verification | Not fully testable because Cloud-PT supplied no usable WAN address or next hop |

Full screenshots are indexed in `Milestone2/evidence/README.md`.

## 7. Security and resilience

The design uses VLAN segmentation, named ACLs, management isolation, port security with sticky MAC addresses, PortFast, BPDU Guard, Rapid-PVST+, and a redundant Engineering uplink. The STP test demonstrated that the standby Engineering path forwards after the primary path is disconnected and that the original state can be restored.

## 8. Limitations and lessons learned

Packet Tracer's Cloud-PT model did not provide a usable Internet DHCP lease or ISP next hop, so NAT/PAT could not be demonstrated end-to-end. Internal routing and all available services were validated instead. The implementation also required explicit dot1q encapsulation on the Catalyst 3560 and careful CDP verification because the physical port labels differed from the initial worksheet.

## 9. Conclusion

The final Packet Tracer implementation satisfies the internal network, VLSM, service, security, and resilience requirements demonstrated by the evidence pack. The GitHub portfolio contains the project file, technical documentation, PDFs, and organised screenshots. The 15-20 minute INSET video demonstration has been recorded and uploaded.

## 10. Rubric traceability matrix

| Rubric criterion | Weight | Evidence | Result |
|---|---|---|---|
| Client requirements and network design | 15% | M1 PDFs (`Milestone1/`), topology screenshots, BOM, cabling schedule | Met |
| Packet Tracer implementation and functionality | 35% | `M3_FINAL.pkt`, DHCP leases, DNS, HTTP, inter-VLAN ping, OSPF, STP | Met |
| Assigned networking feature (VLSM) | 20% | Addressing plan, VLSM arithmetic check, SVI/DHCP verification | Met |
| GitHub portfolio | 15% | README, MD/PDF reports, evidence folder, commit history | Met |
| Technical demonstration (INSET video) | 15% | `project_video.mp4` — recorded and uploaded | Met |

---

## 11. References

Cisco Systems. (2023). *Catalyst 3560 Series Switches — LAN Base Software Configuration Guide*. Cisco Press.

Cisco Systems. (2023). *Catalyst 2960 Series Switches — LAN Lite Software Configuration Guide*. Cisco Press.

Cisco Systems. (2024). *IOS IP Configuration Guide — Configuring DHCP Relay Agent*. Cisco Press.

Deest, G. and Haviv, Y. (2009). *Variable Length Subnet Masks (VLSM) and Classless Inter-Domain Routing (CIDR)*. In: *Internetworking with TCP/IP*, Vol. 1. 6th edn. Upper Saddle River, NJ: Pearson Education.

Droms, R. (1987). *RFC 950: Internet Standard Subnetting Procedure*. Internet Engineering Task Force.

Ferguson, J. and Huston, G. (2023). *Internetworking with TCP/IP*. 6th edn. Upper Saddle River, NJ: Pearson Education.

Free Software Foundation. (2024). *Wireshark User's Guide* [Online]. Available at: https://www.wireshark.org/docs/wsug_html_chunked/ [Accessed 16 Oct. 2026].

Goralski, W. (2017). *The Illustrated Network*. 2nd edn. Amsterdam: Elsevier.

Information Sciences Institute. (2011). *RFC 5737 — IPv4 Address Blocks Reserved for Documentation*. Internet Engineering Task Force.

Keen, G. (2017). *Networking Essentials*. 4th edn. Boston, MA: Cengage Learning.

Lammle, T. (2020). *CCNA Routing and Switching Study Guide*. Indianapolis, IN: Sybex.

Meyers, M. (2023). *CompTIA Network+ Certification All-in-One Exam Guide*. 8th edn. New York, NY: McGraw-Hill.

Parkhurst, R. (2023). *OSPF Design Guide*. Cisco Systems.

Ruehle, R. (2024). *Packet Tracer Lab Manual*. 4th edn. Indianapolis, IN: Cisco Press.

Stevens, R. (2018). *TCP/IP Illustrated*. 2nd edn. Boston, MA: Addison-Wesley.

Tanenbaum, A. (2021). *Computer Networks*. 6th edn. Harlow: Pearson Education.

Westcott, D. and Gerald, D. (2022). *Computer Networking Demystified*. New York, NY: McGraw-Hill.

---

## 12. Submission references

- Final Packet Tracer file: `Milestone3/CLI066_Mbodi_40779750_M3_FINAL.pkt`
- Milestone 2 implementation report: `Milestone2/01_Implementation_Report.pdf`
- Milestone 2 configuration worksheet: `Milestone2/02_Device_Configuration_Commands.pdf`
- Evidence index: `Milestone2/evidence/README.md`
- GitHub portfolio: `https://github.com/mulondimbodi/CMPG325-2026-066-CLI066-Mbodi-40779750`
