# WAN Edge — NAT, Default Routes, and Redundancy

Why a default route is needed first
Every core switch already has routes to the other internal networks. However, they still need a route for internet traffic. A default route (0.0.0.0/0) acts as a catch-all, sending any traffic without a specific route to the appropriate edge router.

NAT/PAT — the edge router's real job
The edge router's inside interface connects to the private 10.10.0.0/16 network, while the outside interface connects to the simulated ISP using a public-style address. ip nat inside source list ... overload enables PAT, allowing all internal devices to share one public IP address. This works like a typical small-site internet connection and also provides a security boundary, since outside devices cannot directly access internal addresses.

interface GigabitEthernet0/0/0    ! adjust to inside interface
 ip address 10.10.5.2 255.255.255.252 ! [10.10.6.2 for EDGE-RTR-B]
 ip nat inside
 no shutdown
 
interface GigabitEthernet0/0/1    ! adjust to outside/ISP-facing interface
 ip address 203.0.113.1 255.255.255.252 [203.0.113.1 for EDGE-RTR-B]
 ip nat outside
 no shutdown
 
! NAT: translate all internal 10.10.0.0/16 traffic using PAT (address overload)
ip access-list standard NAT-INSIDE-NETWORKS
 permit 10.10.0.0 0.0.255.255
 
ip nat inside source list NAT-INSIDE-NETWORKS interface GigabitEthernet0/0/1 overload
ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/0/1

Verifying NAT actually works
[show ip nat translations] & [show ip nat statistics]
Confirms hits/translations are actually occurring.

CORE-SW-A: primary route via EDGE-RTR-A, floating backup via EDGE-RTR-B (through the core-to-core link)
NOTE: IP SLA is not supported in this Packet Tracer version — failover relies on standard floating-static-route 
behavior instead (backup route activates only when the primary next-hop is fully unreachable)
 
! Route to reach CORE-SW-B's transit subnet via the core-to-core trunk
[ip route 10.10.6.0 255.255.255.252 10.10.10.3]
! Primary default route (default administrative distance = 1)
[ip route 0.0.0.0 0.0.0.0 10.10.5.2]
! Floating backup default route (administrative distance 5 — only used
! if the primary route above is removed, i.e. 10.10.5.2 becomes unreachable)
[ip route 0.0.0.0 0.0.0.0 10.10.6.2 5]

CORE-SW-B: mirrored — primary via EDGE-RTR-B, backup via EDGE-RTR-A
[ip route 10.10.5.0 255.255.255.252 10.10.10.2]
[ip route 0.0.0.0 0.0.0.0 10.10.6.2]
[ip route 0.0.0.0 0.0.0.0 10.10.5.2 5]


WAN Redundancy — How It Protects the Design
The project brief identified public-facing services like BPLO and Civil Registrar as a reason for maintaining network uptime. WAN redundancy helps keep these services available when the primary connection fails.
Limitation: Cisco IOS normally supports IP SLA and tracking for more advanced WAN monitoring, but this version of Packet Tracer does not support ip sla. Because of this, the design uses floating static routes, which are a valid Cisco method but have more limited failure detection.

* The primary default route has an administrative distance of 1.
* The backup route has an administrative distance of 5, so it stays inactive while the primary route is working.
* If the primary next-hop (10.10.5.2) becomes unreachable, the primary route is removed and the backup route through the other core/edge router becomes active.
* This can handle failures such as a failed edge router, transit link, or WAN-facing interface.
* However, it cannot detect an ISP-side failure if the immediate next-hop remains reachable. IP SLA would normally be used to detect this type of problem, but it is not available in this Packet Tracer version.

Testing the Failover
1. Check the normal state: Run show ip route | include 0.0.0.0. The primary default route via 10.10.5.2 should be active.
2. Simulate a failure: On EDGE-RTR-A, use shutdown on the interface connected to CORE-SW-A.
3. Check the route again: Run show ip route on CORE-SW-A. The primary route should disappear, and the backup route via 10.10.6.2 should become active.
4. Restore the connection: Use no shutdown on the interface. Once the primary route becomes available again, it automatically becomes active because it has a lower administrative distance.
