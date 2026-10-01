# CMPG325-2026-066 · CLI-066: Kagiso Mechanical Engineering – Milestone 1 Design Review
## Mulondi Mbodi · Student 40779750 · North-West University (Mahikeng Campus) · 2026

> **About this repository:** 3rd-year CMPG 325 Computer Networks capstone. I designed a 52-staff extended-star 3-tier Cisco LAN for client CLI-066 Kagiso Mechanical Engineering (Rustenburg mining-support SME). 192.168.35.0/24 VLSM-split into 7 VLANs (Admin / Eng CAD / Production / Finance POPI / ICT / Mgmt-Servers / Guest WiFi) using largest-first allocation; 48% IP headroom retained for B-BBEE / Bakwena-SETA intern 2027 expansion. Milestone 1 submitted 28 Aug 2026.

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
| **Assigned IPv4 Private Block** | **192.168.35.0 / 24** (VLSM-subnetted into 7 VLAN subnets for Milestone 1) |
| **GitHub Repository URL** | **https://github.com/mulondimbodi/CMPG325-2026-066-CLI066-Mbodi-40779750** |

---

## 1 Project Overview

This is my portfolio of evidence for CMPG 325. The brief puts each student in the role of an independent network design consultant for a fictional South-African SME. My client is CLI-066 Kagiso Mechanical Engineering, a Rustenburg boiler-making / structural-steel / maintenance / NDT shop that serves the tier-one platinum mining houses on the Bushveld Complex. Around 52 permanent staff sit in 5 departments (Admin & HR, Engineering CAD, Production Workshop, Finance & Procurement, ICT & Technical Support), plus rotating contractors and visitors use the on-site Guest WiFi.

The LAN redesign is a pure extended-star 3-tier Cisco hierarchical build: Core / Distribution / Access. No rings, meshes, or partial-meshes — the rubric locks the topology for this module.

---

## 2 Milestone 1 — Client Design Review (Submitted 28 Aug 2026)

Everything marked for the 28 Aug M1 submission lives under `Milestone1/`. Four A4 colour PDFs are the documents I uploaded through the eFundi Turnitin dropbox; the `.pkt` is the Packet Tracer 9.0.1 draw-only topology I saved; the PNG is the full-canvas screenshot that sits as Figure 1 inside the Physical Topology PDF.

### Milestone 1 deliverables in this repo

| # | What it is | File(s) in `Milestone1/` | Notes |
|---|---|---|---|
| D1 | Client Requirements Specification | `CLI066_Mbodi_40779750_M1_D1_ClientRequirements.pdf` | 13 sections. Covers the 5-department headcount breakdown (total 52), shortfalls of the old flat network, chosen 3-tier architecture, VLAN schema, BOM with the mandatory Catalyst 2960-24TT Finance substitution (the 2960-8TC model I planned originally does not ship in Packet Tracer 9.0.1 so I swapped the Finance switch to 2960-24TT and documented why), performance targets, security & POPI, IPv4 constraints, design guardrails, working presumptions, terminology. |
| D2 | Physical Topology Design | `CLI066_Mbodi_40779750_M1_D2_PhysicalTopology.pdf` + `PhysicalTopology_Final_Mbodi_40779750.png` | BOM table, 25-row structured-cabling schedule, POPI Finance locked-tray separation rationale, R-EDGE/DLS1/ALS port map. The PNG (Figure 1) shows the whole Packet Tracer canvas: all 25 devices cabled green (UP/UP) except the DLS1↔ALS-ENG redundant trunk that STP has put into Orange Blocking for the Layer-2 loop prevention demo. |
| D3 | Logical Topology Design | `CLI066_Mbodi_40779750_M1_D3_LogicalTopology.pdf` | 7 VLAN schema (IDs 10 / 20 / 30 / 40 / 50 / 99 / 100). VLAN→port assignments across the four 2960 access switches. Trunk allow-list (802.1Q, native VLAN 999 unused placeholder so VLAN hopping attacks have no data-plane foothold). 7 SVI default-gateway pre-plan. Ingress / Egress ACL placement matrix for each SVI (Finance isolated; Engineering read-only to Finance; Guest WiFi fully dropped from internal subnets). |
| D4 | IP Addressing Plan | `CLI066_Mbodi_40779750_M1_D4_IPAddressingPlan.pdf` | VLSM 192.168.35.0/24 largest-first: /27 VLAN 20 Eng CAD (biggest department), six /28s filling .32–.127, free 124-IP contiguous headroom at .128–.251, R-EDGE↔DLS1 routed point-to-point /30 placed at the very top .252–.255. 18-device static IP roll (13 infrastructure + 4 department printers + 5 management spares). 7 DHCP scopes on SVR-DC with 89 concurrent leases today at 72 % utilisation. Overlap/gap-free arithmetic check. |
| PT | Packet Tracer 9.0.1 Saved Topology (draw phase, no IOS CLI) | `CLI066_Mbodi_40779750_M1.pkt` | 25 representative devices drawn & cabled. R-EDGE Gi0/0 (WAN) + Gi0/1 (LAN) stay factory-shutdown (red arrows) on purpose — Milestone 1 is physical only; `no shutdown` + addressing + routing is Milestone 2 Day-1 CLI work. |

### Packet Tracer canvas summary (M1 draw phase)

I placed 13 infrastructure devices symmetrically on the canvas: Cloud-PT ISP Handoff → ISR G2 2911 Core Router (R-EDGE) → Catalyst 3560-24PS L3 Distribution PoE Switch (DLS1) → 4× Catalyst 2960-24TT Access Switches (ALS-ADM / ALS-ENG / ALS-PRD / ALS-FIN), 2× PoE WiFi APs, 2× servers (SVR-DC left of DLS1 for DHCP+DNS+AD; SVR-FP right of DLS1 for File/Print/CAD-SNL), 2× static LPT-ICT-01/02 laptops sitting on VLAN 50 under DLS1. Then I added 12 skeleton end-users (2 PC pairs + 1 department printer × 4 departments flanking each access switch) to make the 25-device total the rubric expects.

I intentionally cabled a second trunk — DLS1 Fa0/22 ↔ ALS-ENG Fa0/22 — to create a Layer-2 loop. STP auto-converges and paints Orange Blocking dots on that link before any IOS CLI is written; that's the visual STP proof the rubric looks for in M1.

---

## 3 Milestones 2 & 3 — Coming after M1 marking

### Milestone 2 — Implementation & Validation

| # | Planned contents | Planned folder / filename | Status |
|---|---|---|---|
| M2.1 | Full running config on every device: VLANs, SVIs, DHCP relay, trunks, ACLs, OSPF, NAT/PAT, DHCP scopes, port security, PortFast, BPDU Guard, and Rapid-PVST+ | `Milestone2/CLI066_Mbodi_40779750_M2_CONFIGURED.pkt` | ✅ COMPLETE |
| M2.2 | Validation screenshot evidence covering topology, DHCP, DNS, HTTP, authorised/denied access, VLANs, trunks, routing, ACLs, STP, and port security | `Milestone2/evidence/M2_config_screenshots/` and `Milestone2/evidence/M2_service_tests/` | ✅ COMPLETE |
| M2.3 | Implementation document with configuration summary, test results, evidence references, and troubleshooting log | `Milestone2/01_Implementation_Report.md` | ✅ COMPLETE |

### Milestone 3 — Final Submission + Viva

| # | Planned contents | Planned file | Status |
|---|---|---|---|
| M3.1 | Final configured Packet Tracer .pkt (every feature working end-to-end, viva-ready) | `Milestone3/CLI066_Mbodi_40779750_M3_FINAL.pkt` | 🔴 NOT STARTED |
| M3.2 | 15–20 minute viva-voce video walking through: physical topology → IP plan → IOS config → inter-VLAN ping → DHCP lease issue → ACL deny test → STP failover demo | Uploaded directly to NWU eFundi (video files > 100 MB don't fit GitHub free tier) | 🔴 NOT STARTED |
| M3.3 | Final Technical Report, max 30 pages, Harvard-NWU referencing | `Milestone3/01_Final_Technical_Report.md` | 🔴 NOT STARTED |

---

## 4 Repository folder layout

```
ASSIGNMENT/
├── README.md                       this file – what the marker reads first
├── .gitignore                      hides Windows Thumbs.db, PT autosaves, my MD safety-backup folder
│
├── Milestone1/                     design-review submission (28 Aug 2026)
│   ├── CLI066_Mbodi_40779750_M1.pkt                     my Packet Tracer 9.0.1 file; 25 devices drawn
│   ├── PhysicalTopology_Final_Mbodi_40779750.png        D2 Figure 1 – full-canvas screenshot
│   ├── CLI066_Mbodi_40779750_M1_D1_ClientRequirements.pdf      D1 – requirements & BOM
│   ├── CLI066_Mbodi_40779750_M1_D2_PhysicalTopology.pdf        D2 – cabling & topology
│   ├── CLI066_Mbodi_40779750_M1_D3_LogicalTopology.pdf         D3 – VLAN & trunk & ACL plan
│   └── CLI066_Mbodi_40779750_M1_D4_IPAddressingPlan.pdf        D4 – VLSM 192.168.35.0/24 breakdown
│
├── Milestone2/                     completed implementation & test evidence
│   ├── CLI066_Mbodi_40779750_M2_CONFIGURED.pkt
│   ├── 01_Implementation_Report.md
│   ├── 02_Device_Configuration_Commands.md
│   └── evidence/
│       ├── README.md
│       ├── M2_config_screenshots/
│       └── M2_service_tests/
│
├── Milestone3/                     coming – final .pkt, final report, viva link
│   ├── CLI066_Mbodi_40779750_M3_FINAL.pkt
│   └── 01_Final_Technical_Report.md
│
```
