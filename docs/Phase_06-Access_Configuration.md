# Access Switch Configurations

Hostnames of Switches
* SW-GF-A1 = Switch Ground Floor wing A1
* SW-GF-A2 = Switch Ground Floor wing A2
* SW-GF-B1= Switch Ground Floor wing B1
* SW-GF-B2 = Switch Ground Floor wing B2
* SW-2F-A1 = Switch 2nd Floor wing A1
* SW-2F-A2 = Switch 2nd Floor wing A2
* SW-2F-B1 = Switch 2nd Floor wing B1
* SW-2F-B2 = Switch 2nd Floor wing B2
* SW-2F-B3 = Switch 2nd Floor wing B3 (MIS/IT Room)
* CORE-SW-A = Core Switch A
* CORE-SW-B = Core Switch B
* EDGE-RTR-A = Edge Router A
* EDGE-RTR-B = Edge Router B

Manual Configuration (no VTP)
* vlan 10
   name Mayors-Office
* vlan 20
   name SB-SessionHall
* vlan 30
   name Treasurer
* vlan 40
   name Assessor
* vlan 50
   name Accounting
* vlan 60
   name Budget
* vlan 70
   name Civil-Registrar
* vlan 80
   name MPDO-Planning
* vlan 90
   name Engineering
* vlan 100
   name MSWD
* vlan 110
   name Agriculture
* vlan 120
   name MENRO
* vlan 130
   name BPLO
* vlan 140
   name HRMO
* vlan 150
   name GSO
* vlan 160
   name MDRRMO
* vlan 170
   name Legal
* vlan 180
   name IT-MIS-Servers
* vlan 190
   name PublicInfo-Tourism-Coop
* vlan 200
   name Records-Library
* vlan 210
   name Staff-WiFi
* vlan 220
   name Guest-WiFi
* vlan 230
   name Public-Kiosks
* vlan 240
   name CCTV
* vlan 999
   name Native-Unused

Core-to-Access Trunk Ports (on CORE-SW-A and CORE-SW-B)
Every port facing an access switch, and the core-to-core link, is a trunk carrying all VLANs.
Also trunk the port facing the WLC the same way (it needs to reach VLANs 210 and 220).

The edge router (fa0/24) is NOT part of this trunk
The core switches already perform all inter-VLAN routing (via SVIs + HSRP) for every department VLAN. The edge router's only job is NAT/PAT for internet-bound traffic — it never needs Layer 2 visibility into internal department VLANs like Treasurer or Civil Registrar. Trunking all 25 VLANs to an internet-facing router is unnecessary exposure.

Access Switch Trunk Uplinks (per-switch allowed VLAN lists)
Each access switch has two uplink ports (to CORE-SW-A and CORE-SW-B). Restrict allowed vlan to only what that switch actually carries — Staff WiFi (210) and CCTV (240) are included wherever that switch has an AP or camera:

* SW-GF-A1	= 30,40,210,220,240,999
* SW-GF-A2	= 70,130,210,220,230,240,999
* SW-GF-B1	= 20,100,210,220,240,999
* SW-GF-B2	= 190,200,210,220,240,999
* SW-2F-A1	= 10,140,170,210,220,240,999
* SW-2F-A2	= 50,60,150,210,220,240,999
* SW-2F-B1	= 80,90,210,240,220,999
* SW-2F-B2	= 110,120,160,210,220,240,999
* SW-2F-B3	= 180,210,240,220,999

Access Ports — Assigning End Devices
Every PC/printer/camera port is access mode, assigned to its one VLAN, with port security and BPDU Guard:
 [switchport access vlan 30]
 [switchport port-security]
 [switchport port-security maximum 2]
 [switchport port-security violation shutdown]
 [spanning-tree portfast]
 [spanning-tree bpduguard enable]

AP Port (per switch with an AP)
The AP's uplink port is also an trunk port to carry both traffic for VLAN 210 (Staff WiFi) & VLAN 220 (Guest WiFi)
