# Calabanga Municipal Hall Network Infrastructure Design

Scenario
The Municipality of Calabanga, a first-class municipality in Camarines Sur, currently operates its Municipal Hall on a legacy, largely unmanaged network, a mix of unmanaged switches, ad-hoc cabling added department-by-department over the years, no formal VLAN segmentation, and a single flat broadcast domain shared by every office. There is no wireless infrastructure for staff, and public-facing transactions (business permits, civil registry requests) have no dedicated network path separate from internal administrative traffic.
This project designs a modernized, segmented, and secured network infrastructure for the Municipal Hall, treated as a real municipal ICT engagement, including the budget and operational constraints an LGU (Local Government Unit) actually faces.

Objectives
* Segment the network by department/function using VLANs, eliminating the current flat network's security and broadcast-domain risks
* Provide staff and guest wireless access, isolated from each other and from sensitive internal VLANs
* Establish a secured WAN edge to the ISP, with attention to uptime for public-facing services
* Design for 170+ endpoints, reflecting a realistically sized first-class municipality deployment
* Deliver a design that can be implemented in phases, matching how LGU ICT budgets are actually allocated (annual budget cycles, not a single lump-sum project)

In terms of Budget:
LGU ICT budgets are allocated annually through the Annual Investment Plan (AIP), there's no single upfront capital budget for a full rip-and-replace.
So, the design must be phased, with each phase independently deliverable and budget-justifiable.

In terms of Reusable Equipments:
Existing unmanaged switches and cabling in some departments are still usable at Layer 1.
Phase 1 reuses existing cabling/switches where viable instead of assuming a full hardware refresh.

In terms of Physical constraint:
Single building, multiple floors/wings, no existing structured cabling closet per floor.
Requires a defined wiring closet / distribution point per floor as part of Phase 1.

In terms of Security Requirement:
Sensitive resident data (civil registry, treasury/tax records) must not be reachable from staff and guest WiFi VLANs.
Drives the ACL and segmentation design.

Assumptions
Since exact figures for Calabanga's actual Municipal Hall office layout aren't publicly available, this design uses a typical first-class Philippine municipality office structure. This is explicitly noted as an assumption — the design methodology is what matters and transfers directly to the real office list if this were an actual engagement.

Success Criteria
1. Every department has its own VLAN and appropriately sized subnet with room for 30-50% growth.
2. No public-facing or guest device can reach internal administrative VLANs.
3. Staff wireless is available and authenticated separately from guest wireless.
4. The design survives a single link/device failure on the core without a full outage (redundancy).
5. The full design is phased and each phase has a rough cost/priority justification.
