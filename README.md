# CMPG325-2026-066 · CLI-066: Kagiso Mechanical Engineering — Final Submission
## Mulondi Mbodi · Student 40779750 · North-West University (Mahikeng Campus) · 2026

> **About this repository:** 3rd-year CMPG 325 Computer Networks capstone. I designed and implemented a 52-staff extended-star 3-tier Cisco LAN for client CLI-066 Kagiso Mechanical Engineering (Rustenburg mining-support SME). 192.168.35.0/24 VLSM-split into 7 VLANs with 48% IP headroom retained for B-BBEE / Bakwena-SETA intern 2027 expansion. All milestones complete — Packet Tracer implementation, testing evidence, technical report, and INSET video demonstration.

| Field | Value |
|:---|:---|
| **Student Name** | Mulondi Mbodi |
| **Student Number** | **40779750** |
| **Module Code** | CMPG 325 – Computer Networks |
| **Academic Institution** | **North-West University (Mahikeng Campus)** |
| **Academic Year** | **2026** |
| **Module Group** | CMPG325-2026-066 |
| **Client Identifier** | **CLI-066** |
| **Client Industry** | Mechanical Engineering / Mining Support Services (Bushveld Complex / Rustenburg / B-BBEE Mining Charter) |
| **Assigned IPv4 Private Block** | **192.168.35.0 / 24** (VLSM-subnetted into 7 VLAN subnets) |
| **GitHub Repository URL** | **https://github.com/mulondimbodi/CMPG325-2026-066-CLI066-Mbodi-40779750** |

---

## 1 Project Overview

This is my complete portfolio of evidence for CMPG 325. The project involved designing and simulating a three-tier Cisco campus network for Kagiso Mechanical Engineering, a Rustenburg mechanical engineering firm with 52 staff across five departments (Admin & HR, Engineering CAD, Production Workshop, Finance & Procurement, ICT & Technical Support), plus rotating contractors using Guest WiFi.

The LAN is a pure extended-star 3-tier Cisco hierarchical build: Core / Distribution / Access. The assigned networking challenge was IPv4 VLSM subnetting, implemented across seven VLANs with DHCP relay, OSPF routing, ACL-based security, Rapid-PVST+ redundancy, and port security.

---

## 2 Milestone 1 — Client Design Review (Submitted 28 Aug 2026)

All marked for the 28 Aug M1 submission lives under `Milestone1/`.

| # | Deliverable | File(s) | Status |
|---|---|---|---|
| D1 | Client Requirements Specification | `CLI066_Mbodi_40779750_M1_D1_ClientRequirements.pdf` | Complete |
| D2 | Physical Topology Design | `CLI066_Mbodi_40779750_M1_D2_PhysicalTopology.pdf` + PNG | Complete |
| D3 | Logical Topology Design | `CLI066_Mbodi_40779750_M1_D3_LogicalTopology.pdf` | Complete |
| D4 | IP Addressing Plan | `CLI066_Mbodi_40779750_M1_D4_IPAddressingPlan.pdf` | Complete |
| PT | Packet Tracer 9.0.1 topology (draw phase) | `CLI066_Mbodi_40779750_M1.pkt` | Complete |

---

## 3 Milestone 2 — Implementation & Validation (Submitted 2 Oct 2026)

All implementation and testing evidence lives under `Milestone2/`.

| # | Deliverable | File(s) | Status |
|---|---|---|---|
| M2.1 | Full configured Packet Tracer | `CLI066_Mbodi_40779750_M2_CONFIGURED.pkt` | Complete |
| M2.2 | Testing evidence screenshots | `Milestone2/evidence/M2_config_screenshots/` + `Milestone2/evidence/M2_service_tests/` | Complete |
| M2.3 | Implementation report | `01_Implementation_Report.md` / `.pdf` | Complete |
| M2.4 | Device configuration commands | `02_Device_Configuration_Commands.md` / `.pdf` | Complete |

### Implemented features

- VLANs 10, 20, 30, 40, 50, 99, 100 with correct VLSM subnetting
- Inter-VLAN routing via SVIs on DLS1 (Catalyst 3560)
- OSPF Area 0 with FULL adjacency between R-EDGE and DLS1
- DHCP relay to SVR-DC (192.168.35.114) with 7 scopes
- DNS service (kagiso.local) and HTTP file/application server
- 7 named ACLs enforcing CR4 (authorised file server access only)
- Port security, PortFast, BPDU Guard on all access ports
- Rapid-PVST+ with redundant Engineering uplink (automatic failover)
- NAT/PAT configured (not verifiable — Cloud-PT WAN limitation)

### Testing summary

| Test | Result |
|---|---|
| VLSM verification | PASS |
| DHCP leases (Engineering, Admin, Guest, Finance) | PASS |
| OSPF neighbor adjacency | PASS |
| Authorised server access (Engineering) | PASS |
| Denied server access (Production) | PASS |
| ACL hit counters | PASS |
| STP failover and restoration | PASS |
| Port security violation detection | PASS |
| DNS and HTTP services | PASS |
| NAT/PAT Internet access | Not verifiable (Cloud-PT limitation) |

---

## 4 Milestone 3 — Final Submission & Viva (Oct 2026)

All final deliverables live under `Milestone3/`.

| # | Deliverable | File | Status |
|---|---|---|---|
| M3.1 | Final configured Packet Tracer | `CLI066_Mbodi_40779750_M3_FINAL.pkt` | Complete |
| M3.2 | INSET video demonstration | `project_video.mp4` | Complete — submitted via eFundi |
| M3.3 | Final Technical Report | `01_Final_Technical_Report.pdf` | Complete |
| M3.4 | Submission manifest | `SUBMISSION_MANIFEST.pdf` | Complete |

### Video demonstration

The 15-20 minute INSET video covers:
- Introduction and client requirements
- Network design and topology walkthrough
- VLSM addressing (assigned networking challenge)
- Packet Tracer implementation (VLANs, trunks, DHCP, OSPF, ACLs, STP failover, port security)
- Design decisions and troubleshooting
- Limitations (NAT/PAT not verifiable in Packet Tracer)
- Conclusion and reflection

---

## 5 Repository folder layout

```
ASSIGNMENT/
├── README.md
├── .gitignore
│
├── Milestone1/                     design-review submission (28 Aug 2026)
│   ├── CLI066_Mbodi_40779750_M1.pkt
│   ├── PhysicalTopology_Final_Mbodi_40779750.png
│   ├── CLI066_Mbodi_40779750_M1_D1_ClientRequirements.pdf
│   ├── CLI066_Mbodi_40779750_M1_D2_PhysicalTopology.pdf
│   ├── CLI066_Mbodi_40779750_M1_D3_LogicalTopology.pdf
│   └── CLI066_Mbodi_40779750_M1_D4_IPAddressingPlan.pdf
│
├── Milestone2/                     implementation & test evidence
│   ├── CLI066_Mbodi_40779750_M2_CONFIGURED.pkt
│   ├── 01_Implementation_Report.md
│   ├── 01_Implementation_Report.pdf
│   ├── 02_Device_Configuration_Commands.md
│   ├── 02_Device_Configuration_Commands.pdf
│   └── evidence/
│       ├── M2_config_screenshots/  (26 screenshots)
│       └── M2_service_tests/      (7 screenshots)
│
├── Milestone3/                     final submission
│   ├── CLI066_Mbodi_40779750_M3_FINAL.pkt
│   ├── project_video.mp4           (INSET video — submitted via eFundi)
│   ├── 01_Final_Technical_Report.md
│   ├── 01_Final_Technical_Report.pdf
│   ├── SUBMISSION_MANIFEST.md
│   └── SUBMISSION_MANIFEST.pdf
│
```

---

## 6 Final checklist

- [x] M1 design review submitted (28 Aug 2026)
- [x] M2 implementation and testing complete (2 Oct 2026)
- [x] M3 final Packet Tracer saved
- [x] M3 technical report with Harvard-NWU references
- [x] M3 INSET video recorded and submitted via eFundi
- [x] Evidence folder organised
- [x] Submission manifest created
- [x] M3 pushed to GitHub

---

**Generated:** October 2026
**Student:** Mulondi Mbodi (40779750)
