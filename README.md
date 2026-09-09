# Calabanga-Municipal-Network-Infra-Project
A full enterprise network design and implementation for the Municipal Hall of Calabanga, a first-class municipality in Camarines Sur, built and tested as Cisco Packet Tracer simulation, treated throughout as a real municipal ICT engagement with real budget and operational constraints.

The Municipal Hall's current network is assumed to be a flat network with no proper segmentation, as real infrastructure details were not available. This project replaces it with a secure, segmented, and redundant network using 25 VLANs across 9 access switches and a redundant core, supporting 170+ endpoints. It includes centralized DHCP, staff and guest Wi-Fi, ACL-based network separation, Layer 2 security, and dual-WAN redundancy.

Architecture Highlights
* 25 VLANs organized into 4 trust zones (Public/Untrusted, Operational, General Departments, Restricted) across a realistic first-class municipality office structure.
* Redundant collapsed core, dual Layer 3 switches with HSRP per-VLAN gateway redundancy, STP root alignment.
* Centralized DHCP via relay (ip helper-address) to a single hardened server, rather than per-VLAN DHCP.
* ACL-based zone segmentation - including a documented, real-workflow exception (BPLO can reach Assessor/Treasurer records for business permit processing) rather than blanket allow/deny rules.
* Layer 2 hardening - port security with sticky MAC addresses, PortFast, BPDU Guard, DHCP snooping, and Dynamic ARP Inspection.
* Wireless - WLC-managed lightweight APs, separate Staff and Guest SSIDs mapped to isolated VLANs.
* Dual-WAN edge - NAT/PAT on redundant edge routers, floating static route failover, tested by actually simulating an ISP failure.

Design Philosophy
Every major design decision in this repository is tied to a stated reason, not just "because it's best practice":
* No VTP - a small IT team managing hand-me-down/reused switches is exactly the scenario where a single misconfigured switch with a stale, higher VTP revision number can wipe out every VLAN network-wide. Manual configuration trades convenience for eliminating that entire failure class.
* VLAN count driven by real port capacity, not convenience - the initial 4-access-switch design was revised to 9 after checking real device-count-per-wing against actual switch port capacity.
* CCTV cameras distributed by physical location, not centralized on one switch — cameras are physically mounted throughout the building; only the recording server needs to be centralized, and VLAN trunking (not dedicated cabling) carries their traffic back.
* Reflexive ACLs, arp access-list, ip source binding, and ip sla were all attempted and found unsupported in this Packet Tracer version — each is documented as a known simulator limitation with the real-world equivalent explained, rather than silently worked around or ignored.

The Debugging Story
This is one of the main parts of the project. Thirteen real issues were found and fixed using a step-by-step troubleshooting process instead of guessing. This included comparing device configurations, using static IPs to test DHCP problems, and using Packet Tracer's Simulation Mode to trace packets when needed. The hardest issue was an ACL applied to the wrong interface, which blocked server replies for four departments. After several false leads, Simulation Mode helped identify the actual cause. The full details are in docs/troubleshooting.md.

Tool
Cisco Packet Tracer — full build and testing environment.
