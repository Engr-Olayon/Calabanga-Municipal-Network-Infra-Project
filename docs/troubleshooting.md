# Troubleshooting & Debugging Log

This document records real issues found and fixed during the build and testing of this network — not hypothetical scenarios. Each entry follows: Symptom → Diagnosis → Root Cause → Fix → Lesson. Several of these issues took hours and required testing different possible causes. The false leads are included on purpose because proving that a possible cause is wrong is also an important troubleshooting skill.

1. DHCP Pool Conflict — Packet Tracer's Built-In "serverPool"
* Symptom: IT department PCs received the wrong IP (the gateway's own address, 10.10.180.1) instead of a proper DHCP lease.
* Diagnosis: Checked the DHCP server's pool configuration — the custom POOL-IT pool looked correct. But Packet Tracer's DHCP service ships with a built-in, non-deletable default pool called [serverPool]. Once its start address was edited to fit the VLAN 180 range, it began overlapping with POOL-IT.
* Root Cause: Two DHCP pools covering the same subnet — Packet Tracer's DHCP engine doesn't reliably arbitrate which pool answers a given request when ranges overlap.
* Fix: Moved serverPool to a completely unused subnet (192.168.99.0/24) outside the real addressing scheme, permanently neutralizing it without needing to delete it (which isn't possible in Packet Tracer).
* Lesson: Simulator platforms sometimes ship default objects that can't be removed — the fix is to make them harmless, not to fight the tool to delete them.

2.  Native VLAN Mismatch — Trunk Ports Not Aligned
* Symptom: %CDP-4-NATIVE_VLAN_MISMATCH warnings appearing on every access-switch trunk link.
* Diagnosis: Core switches were configured with switchport trunk native vlan 999 (a deliberate hardening choice — moving off default VLAN 1 to reduce VLAN-hopping risk), but the corresponding access switches' uplink ports were still on the default native VLAN 1.
* Root Cause: The native VLAN hardening was applied only to one side of each trunk link.
* Fix: Applied switchport trunk native vlan 999 to the matching uplink ports on every access switch.
* Lesson: A trunk's native VLAN is a property of both ends of the link — changing it on only one side doesn't just fail to help, it actively creates a mismatch that risks VLAN traffic leaking across the (now-disagreeing) untagged channel.

3. Guest WiFi Broken — WLC "Local Switching" and Incomplete VLAN Trunking
* Symptom: Staff WiFi worked perfectly; Guest WiFi clients got no IP address at all (0.0.0.0) on every AP.
* Diagnosis: The WLC's WLANs were set to "Local switching, local authentication" — meaning each AP's own physical switchport, not the WLC's uplink, is responsible for carrying both VLANs' traffic. The AP ports were access-mode, single-VLAN (210) only.
* Root Cause: Local switching requires the AP's switchport to trunk every VLAN any of its WLANs use. A single-VLAN access port had no path for Guest (220) traffic at all.
* Fix: Converted every AP's switchport to trunk mode, carrying both 210 and 220. This then surfaced a second layer of the same bug: several access switches' uplinks toward the core had never been updated to carry VLAN 220 either (that VLAN was originally only planned for one specific switch, before the design evolved to broadcast Guest WiFi from every AP building-wide) — requiring switchport trunk allowed vlan add 220 across the remaining switches.
* Lesson: A single design decision (which WLC switching mode to use) has real, cascading infrastructure requirements. Understanding why a setting exists (local vs. central switching) prevents chasing the symptom without fixing the actual architectural requirement.

4. STP Root Bridge Misaligned with HSRP Active Router
* Symptom: Using Simulation Mode to trace a packet's path revealed it consistently traveling through CORE-SW-B before reaching CORE-SW-A, even though CORE-SW-A is HSRP-active for every VLAN.
* Diagnosis: show spanning-tree summary showed CORE-SW-B as root bridge for nearly every VLAN, while HSRP priorities favored CORE-SW-A.
* Root Cause: HSRP priority and STP root role were configured independently, with no attempt to align them — Spanning Tree decides the Layer 2 forwarding path with zero awareness of which router is allowed to make Layer 3 routing decisions.
* Fix: Ran [spanning-tree vlan 10-240 root primary] on CORE-SW-A, [spanning-tree vlan 10-240 root secondary] on CORE-SW-B.

5. Port Security
* Symptom: Deliberately tested "what happens if someone unplugs a PC and plugs in a different device" — expected a port-security violation, but the new device connected successfully with no issue at all.
* Diagnosis: Checked show port-security interface after the swap — it showed the new device's MAC as if it were the first and only device ever seen on that port, with zero violations recorded.
* Root Cause: Dynamic (non-sticky) secure MAC addresses are held only in the switch's active memory and are cleared the moment the physical link goes down — unplugging a cable is exactly that kind of event. The switch had no memory of the original device to compare against.
* Fix: Enabled switchport port-security mac-address sticky on every end-device access port, which writes the learned MAC into the actual running configuration, surviving a link-down/up cycle.
* Lesson: Port security's default (non-sticky) behavior protects against a different threat model (an additional device plugged in alongside the legitimate one while it stays connected) than the one most people assume it covers (device-swap after unplugging). Deliberately testing the attack scenario you intend to defend against — rather than assuming a feature does what its name implies — caught a real, meaningful gap.

