# Topology, Wireless, WAN & Redundancy

Design Choice: Collapsed Core (2-Tier)
A full 3-tier design (core, distribution, and access) is mainly used for larger campuses with multiple buildings. Since Calabanga Municipal Hall is a single building, a full 3-tier design would add unnecessary cost and complexity. Instead, this design uses a collapsed core with two Layer 3 switches handling both core and distribution functions, plus access switches for each floor or wing.

Why Redundancy Matters?
BPLO and Civil Registrar are public-facing services that need to stay available. In fact most of the departments need to stay available at all times. So, if the core switch or ISP connection fails, residents may not be able to process permits or certificates. This makes network redundancy important for service continuity.

Redundancy Design
* WAN
Dual ISP links (primary + backup, different providers if budget allows) > Single ISP outage taking down all public services
* WAN Gateway
HSRP between two edge routers = One router failing doesn't kill the internet path
* Core/Distribution
Redundant switch pair, HSRP/GLBP on SVIs (department gateways) > One core switch failing doesn't take VLANs offline
* Access-to-Core links
Dual uplinks from each access switch to both core switches, with STP (Rapid PVST+) > A single cable/port failure doesn't isolate a floor; STP prevents loops from the redundant links
* Access Layer
STP PortFast + BPDU Guard on end-device ports = Prevents accidental loops from someone plugging in an unmanaged switch

Spanning Tree note — since each access switch has two connections to the core switches, STP is needed to prevent loops and broadcast storms. It blocks the backup path during normal operation and uses it if the main path fails. Redundancy without STP can cause more problems than it solves.

Wireless Design
SSID	                VLAN	  Notes
CalabangaGov-Staff	  210		  Staff only, isolated from Zone D per the ACL design
CalabangaGov-Guest	  220	    Public lobby/waiting-area access, internet-only

WAN Edge
* Primary & Secondary ISP link on the edge router, for default & standby route out.
* NAT/PAT on the edge router for all internal VLANs to reach the internet.

