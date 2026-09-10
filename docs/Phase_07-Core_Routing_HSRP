# Inter-VLAN Routing (SVIs) & HSRP Gateway Redundancy
Right now, the VLANs are isolated from each other, which is correct for security and ACLs. However, departments still need access to shared resources like the internet, DHCP/DNS server, application/database server, and allowed printers. To allow this communication, each VLAN needs a Layer 3 gateway, provided by an SVI (Switched Virtual Interface).

Design: HSRP Active/Standby Per VLAN
Each VLAN has two SVIs — one on each core switch — that share a single virtual IP address used as the default gateway. If one core switch fails, the other takes over the virtual IP, so devices continue working without changing their gateway settings.

* CORE-SW-A  -  HSRP Active (priority 110)  -  10.10.30.2
* CORE-SW-B  -  HSRP Standby (priority 100, default)  -  10.10.30.3
* Virtual IP (the actual gateway devices use)  -  10.10.30.1

Testing Failover (worth doing once a few VLANs are configured)
1. From a PC in VLAN 30, ping 10.10.30.1 — should succeed (that's the gateway)
2. Shutdown the VLAN 30 SVI on CORE-SW-A (simulating a failure)
3. Ping again from the same PC — it should still work, because CORE-SW-B has taken over the virtual IP
4. No shutdown the SVI back on CORE-SW-A — with preempt configured, it reclaims active status automatically
