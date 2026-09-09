# Hardware Selection
Chosen from Cisco equipment available in Packet Tracer's device library, with selections justified against the project brief's budget constraint. It's not just "the newest model available."

Core / Distribution Layer
Device	                      Model	                Qty	    Why
Core/Distribution Switch	    Catalyst 3650-24PS	  2       Multilayer (Layer 3) switch — used for inter-VLAN routing, HSRP, and ACLs at the core. 

Access Layer
Device	                      Model	                Qty	    Why
Access Switch                 Catalyst 3560-24PS    9       PoE-capable access switch — runs only in Layer 2 mode, with no routing or SVIs configured. (Only available switch that has PoE)

WAN Edge
Device	                      Model	                Qty	    Why
Edge Router	                  Cisco ISR 4321	      2       Supports NAT/PAT, HSRP, IP SLA-based failover routing — needed for the dual-WAN redundancy design. Two units for WAN gateway redundancy.

Wireless
Device	                      Model	                              Qty	                        Why
Wireless LAN Controller	      Cisco 2504 WLC	                    1                           Centrally manages all APs, pushes the Staff/Guest SSID config.
Access Point	                Cisco Aironet 1260 (lightweight)	  9 (one per access switch)   Lightweight AP managed by the WLC

Servers
Device	                      Model	                              Qty	                        Why
DHCP/DNS Server	              Generic PT Server	                  1	                          Centralized DHCP for all 25 VLANs instead of configuring DHCP pools switch-by-switch — more realistic and manageable at this scale
Application/DB Server	        Generic PT Server	                  1	                          Represents the permit/assessment system referenced in the ACL design (BPLO ↔ Assessor ↔ Treasurer workflow)

End Devices
Generic Packet Tracer PCs and a printer or two per department VLAN, per the device counts in doc 02. Don't need to place all ~170 individually — only placing 2 representative PCs per Department.
