# Milestone 2 Device Configuration Commands

This worksheet documents the final Milestone 2 Packet Tracer configuration. Cisco IOS commands are shown for routers and switches. Packet Tracer server services and server IP settings were configured through the server GUI, not Cisco CLI.

## DLS1: Catalyst 3560 distribution switch

```text
enable
configure terminal
hostname DLS1
no ip domain-lookup
ip routing
spanning-tree mode rapid-pvst
spanning-tree vlan 1,10,20,30,40,50,99,100 root primary
vlan 10
 name ADMIN_HR
vlan 20
 name ENGINEERING_DESIGN
vlan 30
 name PRODUCTION_FLOOR
vlan 40
 name FINANCE_PROCURE
vlan 50
 name ICT_TECHNICAL
vlan 99
 name MGMT_SERVERS
vlan 100
 name GUEST_WIFI
vlan 999
 name UNUSED_NATIVE
exit
interface Vlan10
 ip address 192.168.35.33 255.255.255.240
 ip helper-address 192.168.35.114
 no shutdown
interface Vlan20
 ip address 192.168.35.1 255.255.255.224
 ip helper-address 192.168.35.114
 no shutdown
interface Vlan30
 ip address 192.168.35.49 255.255.255.240
 ip helper-address 192.168.35.114
 no shutdown
interface Vlan40
 ip address 192.168.35.97 255.255.255.240
 ip helper-address 192.168.35.114
 no shutdown
interface Vlan50
 ip address 192.168.35.65 255.255.255.240
 ip helper-address 192.168.35.114
 no shutdown
interface Vlan99
 ip address 192.168.35.113 255.255.255.240
 ip helper-address 192.168.35.114
 no shutdown
interface Vlan100
 ip address 192.168.35.81 255.255.255.240
 ip helper-address 192.168.35.114
 no shutdown
interface GigabitEthernet0/1
 no switchport
 ip address 192.168.35.254 255.255.255.252
 no shutdown
router ospf 1
 router-id 2.2.2.2
 passive-interface default
 no passive-interface GigabitEthernet0/1
 network 192.168.35.0 0.0.0.255 area 0
exit
ip route 0.0.0.0 0.0.0.0 192.168.35.253
interface range FastEthernet0/21 - 24
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport nonegotiate
 spanning-tree guard root
 no shutdown
exit
interface FastEthernet0/21
 switchport trunk allowed vlan 1,20,50,99
interface FastEthernet0/22
 switchport trunk allowed vlan 1,20,50,99
interface FastEthernet0/23
 switchport trunk native vlan 1
 switchport trunk allowed vlan 1,30,99,100
interface FastEthernet0/24
 switchport trunk native vlan 999
 switchport trunk allowed vlan 40,999
interface GigabitEthernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport nonegotiate
 switchport trunk allowed vlan 1,10,99
 no shutdown
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 99
 spanning-tree portfast
 no shutdown
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 99
 spanning-tree portfast
 no shutdown
end
copy running-config startup-config
```

## R-EDGE: ISR 2911 core router

```text
enable
configure terminal
hostname R-EDGE
no ip domain-lookup
interface GigabitEthernet0/0
 ip address dhcp
 ip nat outside
 no shutdown
interface GigabitEthernet0/1
 ip address 192.168.35.253 255.255.255.252
 ip nat inside
 no shutdown
router ospf 1
 router-id 1.1.1.1
 network 192.168.35.252 0.0.0.3 area 0
 default-information originate always
exit
ip route 0.0.0.0 0.0.0.0 dhcp
access-list 1 permit 192.168.35.0 0.0.0.255
ip nat inside source list 1 interface GigabitEthernet0/0 overload
end
copy running-config startup-config
```

## Access switch pattern

Use the matching VLAN and DLS1 uplink from the table below. The end-device port assignments follow the Milestone 1 logical topology.

| Switch | User VLAN(s) | Access-switch uplink port(s) | DLS1 uplink port(s) | Primary allowed VLANs |
|---|---|---|---|---|
| ALS-ADM | 10 | Gi0/1 | Gig0/2 | 1,10,99 |
| ALS-ENG | 20 and 50 | Gi0/1 and Gi0/2 | Fa0/21 and Fa0/22 | 1,20,50,99 |
| ALS-PRD | 30 and 100 | Gi0/1 | Fa0/23 | 1,30,99,100 |
| ALS-FIN | 40 | Gi0/1 | Fa0/24 | 40,999 |

For each access switch, configure the hostname, VLANs, user access ports, uplink, PortFast, and BPDU Guard. Keep unused ports shutdown.

### ALS-ADM

```text
enable
configure terminal
hostname ALS-ADM
vlan 10
 name ADMIN_HR
vlan 99
 name MGMT_SERVERS
interface range FastEthernet0/1 - 3
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 1
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 no shutdown
interface GigabitEthernet0/1
 switchport mode trunk
 switchport nonegotiate
 switchport trunk allowed vlan 1,10,99
 no shutdown
interface Vlan99
 ip address 192.168.35.115 255.255.255.240
 no shutdown
ip default-gateway 192.168.35.113
end
copy running-config startup-config
```

### ALS-ENG

```text
enable
configure terminal
hostname ALS-ENG
vlan 20
 name ENGINEERING_DESIGN
vlan 50
 name ICT_TECHNICAL
vlan 99
 name MGMT_SERVERS
interface range FastEthernet0/1 - 3
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 1
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 no shutdown
interface range FastEthernet0/17 - 19
 switchport mode access
 switchport access vlan 50
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown
interface GigabitEthernet0/1
 switchport mode trunk
 switchport nonegotiate
 switchport trunk allowed vlan 1,20,50,99
 no shutdown
interface GigabitEthernet0/2
 switchport mode trunk
 switchport nonegotiate
 switchport trunk allowed vlan 1,20,50,99
 no shutdown
interface Vlan99
 ip address 192.168.35.116 255.255.255.240
 no shutdown
ip default-gateway 192.168.35.113
end
copy running-config startup-config
```

### ALS-PRD

```text
enable
configure terminal
hostname ALS-PRD
vlan 30
 name PRODUCTION_FLOOR
vlan 100
 name GUEST_WIFI
vlan 99
 name MGMT_SERVERS
interface range FastEthernet0/1 - 3
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 1
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 no shutdown
interface GigabitEthernet0/1
 switchport mode trunk
 switchport nonegotiate
 switchport trunk allowed vlan 1,30,99,100
 no shutdown
interface Vlan99
 ip address 192.168.35.117 255.255.255.240
 no shutdown
ip default-gateway 192.168.35.113
end
copy running-config startup-config
```

### ALS-FIN

```text
enable
configure terminal
hostname ALS-FIN
vlan 40
 name FINANCE_PROCURE
vlan 99
 name MGMT_SERVERS
vlan 999
 name UNUSED_NATIVE
interface range FastEthernet0/1 - 3
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 1
 switchport port-security violation shutdown
 switchport port-security mac-address sticky
 no shutdown
interface GigabitEthernet0/1
 switchport mode trunk
 switchport nonegotiate
 switchport trunk native vlan 999
 switchport trunk allowed vlan 40,999
 no shutdown
interface Vlan99
 ip address 192.168.35.118 255.255.255.240
 no shutdown
ip default-gateway 192.168.35.113
end
copy running-config startup-config
```

## Server and DHCP configuration

### SVR-DC: DHCP + DNS server

`SVR-DC` is a Packet Tracer Server-PT device, so its IP settings and services were configured through the GUI rather than Cisco IOS CLI.

| Setting | Value |
|---|---|
| IPv4 address | `192.168.35.114` |
| Subnet mask | `255.255.255.240` |
| Default gateway | `192.168.35.113` |
| DNS server | `192.168.35.114` |
| DHCP service | On |
| DNS service | On |
| DNS record | `kagiso.local` / file-server record to `192.168.35.119` |

DHCP was configured in `SVR-DC -> Services -> DHCP`, not with IOS commands. The seven GUI scopes are:

| Scope | Network/mask | Default gateway | DNS server |
|---|---|---|---|
| VLAN10_ADMIN | `192.168.35.32/28` | `192.168.35.33` | `192.168.35.114` |
| VLAN20_ENGINEERING | `192.168.35.0/27` | `192.168.35.1` | `192.168.35.114` |
| VLAN30_PRODUCTION | `192.168.35.48/28` | `192.168.35.49` | `192.168.35.114` |
| VLAN40_FINANCE | `192.168.35.96/28` | `192.168.35.97` | `192.168.35.114` |
| VLAN50_ICT | `192.168.35.64/28` | `192.168.35.65` | `192.168.35.114` |
| VLAN99_MANAGEMENT | `192.168.35.112/28` | `192.168.35.113` | `192.168.35.114` |
| VLAN100_GUEST | `192.168.35.80/28` | `192.168.35.81` | `192.168.35.114` |

Evidence: `05_svr_dc_dhcp.png` and `06_svr_dc_dns.png`.

### SVR-FP: file/application server on management VLAN

`SVR-FP` is also configured through the Packet Tracer GUI.

| Setting | Value |
|---|---|
| IPv4 address | `192.168.35.119` |
| Subnet mask | `255.255.255.240` |
| Default gateway | `192.168.35.113` |
| HTTP service | On |

Evidence: `07_svr_fp_http.png`.

## Final ACL policy (CR4)

The seven named inbound ACLs below are the final DLS1 policy. IOS sequence numbers are shown in their final corrected order. The file/application server is `192.168.35.119`.

### ACL-ENG-IN

```text
10 deny ip any 192.168.35.96 0.0.0.15
20 permit ip any host 192.168.35.119
30 permit ip any any
```

Engineering traffic cannot reach Finance, but may reach the file server and other permitted destinations.

### ACL-ADMIN-IN

```text
10 deny ip any 192.168.35.96 0.0.0.15
20 permit ip any host 192.168.35.119
30 permit ip any any
```

Admin traffic cannot reach Finance, but may reach the file server and other permitted destinations.

### ACL-FIN-IN

```text
10 deny ip any 192.168.35.0 0.0.0.255
20 permit ip any host 192.168.35.119
30 permit ip any any
```

Finance is isolated from internal address space by the final applied policy.

### ACL-PRD-IN

```text
10 deny ip any host 192.168.35.119
20 deny ip any 192.168.35.96 0.0.0.15
30 permit ip any any
```

Production cannot reach the file server or Finance.

### ACL-ICT-IN

```text
10 deny ip any 192.168.35.96 0.0.0.15
20 permit ip any 192.168.35.112 0.0.0.15
30 permit ip any any
```

ICT cannot reach Finance and may reach the management/server subnet.

### ACL-MGMT-IN

```text
10 permit ip 192.168.35.64 0.0.0.15 any
20 permit ip 192.168.35.112 0.0.0.15 any
30 deny ip any any
```

Only ICT and the management/server subnet are permitted through the management policy.

### ACL-GUEST-IN

```text
10 deny ip any 192.168.35.0 0.0.0.255
20 permit ip any any
```

Guest traffic is denied access to internal RFC1918 project networks and permitted elsewhere.

The configured ACL names, entries, and counters are documented in `13_dls1_acls.png`.

## Verification commands

Run these before closing the M2 evidence pack:

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show ip ospf neighbor
show spanning-tree root
show interfaces status
show access-lists
ping 192.168.35.119 source 192.168.35.1
ping 192.168.35.119 source 192.168.35.65
ping 192.168.35.119 source 192.168.35.97
nslookup file-server 192.168.35.114
```

Expected result: authorised Engineering / Admin / ICT users reach the server, while Finance and Production attempts are denied. Capture the successful ping, denied ping, DHCP lease output, and ACL hit counters as the final evidence set.

## Final evidence checklist

- [x] `show vlan brief` on DLS1 - `08_dls1_vlan_brief.png`
- [x] VLSM SVI addresses and DHCP helpers - `26_dls1_vlsm_svis_and_helpers.png`
- [x] `show interfaces trunk` on DLS1 and access switches - `09_dls1_trunks.png`
- [x] `show ip ospf neighbor` on R-EDGE and DLS1 - `11_dls1_ospf.png`
- [x] DHCP lease screenshots for VLAN 20 and VLAN 10 - `01_engineering_dhcp.png`, `02_admin_dhcp.png`
- [x] DHCP lease screenshots for VLAN 100 and VLAN 40 - `08_guest_dhcp.png`, `09_finance_dhcp.png`
- [x] ALS-ADM VLAN and trunk verification - `25_als_adm_vlan_and_trunk.png`
- [x] DHCP service screen on SVR-DC (GUI, not `show ip dhcp binding`) - `05_svr_dc_dhcp.png`
- [x] Successful ping from Engineering to `192.168.35.119` - `03_engineering_server_ping.png`
- [x] Failed ping from Production to `192.168.35.119` - `04_production_server_ping_denied.png`
- [x] `show access-lists` with deny counters incrementing - `13_dls1_acls.png`
- [x] STP root and redundant-link evidence - `15_stp_before_failover_part1.png`, `16_stp_before_failover_part2.png`, `17_stp_before_failover_part3.png`, `18_stp_after_failover_part1.png`, `22_stp_restored_part1.png`
- [x] Final Packet Tracer save for `Milestone2/CLI066_Mbodi_40779750_M2_CONFIGURED.pkt`
