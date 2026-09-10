# ACL Implementation

Placement Principle
Every ACL is applied inbound on the source VLAN's SVI. This means traffic is filtered as it leaves the VLAN, before it is routed to another network. For example, Guest (220) has a rule blocking access to the Treasurer VLAN (30), instead of making the Treasurer VLAN filter traffic from every other VLAN. Zone D (Treasurer/Assessor/Civil Registrar/MSWD) does not need its own ACL because the other zones already control access to it.

Lines Every ACL Starts With (and why)
[permit udp any any eq bootps]
[permit udp any host 10.10.180.10 eq domain]
These two lines were added based on the DHCP/DNS troubleshooting issue earlier in the project. DHCP broadcasts and DNS requests must be allowed before the deny rules are applied; otherwise, they can fail silently. This happened with Treasurer's DHCP before the trunk issue was found. Therefore, every ACL starts with these two lines as a direct lesson from that troubleshooting incident.

ACLs by Zone
1. ZONE-A-GUEST (Applied to VLAN 220):
DHCP/DNS only, deny all other 10.10.0.0/16, and permit internet.

2. ZONE-B-STAFFWIFI (Applied to VLAN 210):
DHCP/DNS, deny Zone D specifically, permit rest of internal + internet

3. ZONE-B-CCTV (Applied to VLAN 240):
Only reach 10.10.180.0/24 (server/NVR subnet), deny everything else internal

4. ZONE-C-GENERAL	(Applied to VLAN 10,20,60,80,90,110,120,140,150,160,170,190,200)
DHCP/DNS, deny Zone D, permit rest — general departments get normal internal + internet access

5. VLAN130-BPLO	(Applied to VLAN 130)
Explicit permit to Assessor(40) and Treasurer(30) — the documented permit-processing exception — then same deny-Zone-D-elsewhere logic as general departments.

Run on BOTH CORE-SW-A and CORE-SW-B
[ip access-list extended ZONE-A-GUEST
 permit udp any any eq bootps
 permit udp any host 10.10.180.10 eq domain
 deny ip any 10.10.30.0 0.0.0.255
 deny ip any 10.10.40.0 0.0.0.255
 deny ip any 10.10.70.0 0.0.0.255
 deny ip any 10.10.100.0 0.0.0.255
 deny ip any 10.10.0.0 0.0.255.255
 permit ip any any
ip access-list extended ZONE-B-STAFFWIFI
 permit udp any any eq bootps
 permit udp any host 10.10.180.10 eq domain
 deny ip any 10.10.30.0 0.0.0.255
 deny ip any 10.10.40.0 0.0.0.255
 deny ip any 10.10.70.0 0.0.0.255
 deny ip any 10.10.100.0 0.0.0.255
 permit ip any any
ip access-list extended ZONE-B-CCTV
 permit ip any 10.10.180.0 0.0.0.255
 deny ip any 10.10.0.0 0.0.255.255
 permit ip any any
ip access-list extended ZONE-C-GENERAL
 permit udp any any eq bootps
 permit udp any host 10.10.180.10 eq domain
 deny ip any 10.10.30.0 0.0.0.255
 deny ip any 10.10.40.0 0.0.0.255
 deny ip any 10.10.70.0 0.0.0.255
 deny ip any 10.10.100.0 0.0.0.255
 permit ip any any
ip access-list extended VLAN130-BPLO
 permit ip any 10.10.40.0 0.0.0.255
 permit ip any 10.10.30.0 0.0.0.255
 permit udp any any eq bootps
 permit udp any host 10.10.180.10 eq domain
 deny ip any 10.10.70.0 0.0.0.255
 deny ip any 10.10.100.0 0.0.0.255
 permit ip any any]

------------------ Apply each ACL inbound on its VLAN SVI ------------------
[interface vlan 220
 ip access-group ZONE-A-GUEST in
interface vlan 230
 ip access-group ZONE-A-KIOSK in
interface vlan 210
 ip access-group ZONE-B-STAFFWIFI in
interface vlan 240
 ip access-group ZONE-B-CCTV in
interface vlan 10
 ip access-group ZONE-C-GENERAL in
interface vlan 20
 ip access-group ZONE-C-GENERAL in
interface vlan 60
 ip access-group ZONE-C-GENERAL in
interface vlan 80
 ip access-group ZONE-C-GENERAL in
interface vlan 90
 ip access-group ZONE-C-GENERAL in
interface vlan 110
 ip access-group ZONE-C-GENERAL in
interface vlan 120
 ip access-group ZONE-C-GENERAL in
interface vlan 140
 ip access-group ZONE-C-GENERAL in
interface vlan 150
 ip access-group ZONE-C-GENERAL in
interface vlan 160
 ip access-group ZONE-C-GENERAL in
interface vlan 170
 ip access-group ZONE-C-GENERAL in
interface vlan 190
 ip access-group ZONE-C-GENERAL in
interface vlan 200
 ip access-group ZONE-C-GENERAL in
interface vlan 130
 ip access-group VLAN130-BPLO in]

Then to verify [show access-lists] & Re-run Doc 09's Test 3
