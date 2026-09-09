# Departments, VLAN Design & IP Addressing Scheme

Design Approach
Rather than using VLSM to save every possible IP address, this design gives each VLAN a /24 subnet within the private 10.10.0.0/16 range. This makes the network easier for a small MIS team to manage and troubleshoot. Using a simple VLAN ID → third-octet numbering scheme also makes ACLs and IP addresses easier to understand. In this case, easy management and maintenance are more important than saving a small number of IP addresses.
Numbering convention: VLAN 10 → 10.10.10.0/24, VLAN 20 → 10.10.20.0/24, etc. Gateway is always .1 (an SVI on the core Layer 3 switch).

VLAN & Department Table
VLAN        Department(s)                                    Subnet               Gateway
10          Office of the Mayor / Executive                  10.10.10.0/24        10.10.10.1
20	        Vice Mayor & Sangguniang Bayan                   10.10.20.0/24	      10.10.20.1
30	        Municipal Treasurer's Office                     10.10.30.0/24	      10.10.30.1
40	        Municipal Assessor's Office	                     10.10.40.0/24	      10.10.40.1
50	        Municipal Accounting Office                      10.10.50.0/24	      10.10.50.1
60	        Municipal Budget Office	                         10.10.60.0/24	      10.10.60.1
70	        Municipal Civil Registrar                        10.10.70.0/24	      10.10.70.1
80	        Municipal Planning & Development (MPDO)          10.10.80.0/24	      10.10.80.1
90	        Municipal Engineering Office                     10.10.90.0/24	      10.10.90.1
100	        Social Welfare & Development (MSWD)              10.10.100.0/24  	    10.10.100.1
110	        Municipal Agriculture Office                     10.10.110.0/24	      10.10.110.1
120	        Environment & Natural Resources (MENRO)          10.10.120.0/24	      10.10.120.1
130	        Business Permits & Licensing (BPLO)              10.10.130.0/24	      10.10.130.1
140	        Human Resource Management (HRMO)                 10.10.140.0/24	      10.10.140.1
150	        General Services Office (GSO)                    10.10.150.0/24	      10.10.150.1
160	        Disaster Risk Reduction & Mgmt (MDRRMO)          10.10.160.0/24       10.10.160.1
170	        Legal Office                                     10.10.170.0/24	      10.10.170.1
180	        IT/MIS Office + Server Room	6 + servers          10.10.180.0/24	      10.10.180.1
190	        Public Info, Tourism, Cooperative Dev (shared)   10.10.190.0/24	      10.10.190.1
200	        Records Section / Municipal Library              10.10.200.0/24	      10.10.200.1
210	        Staff WiFi		                                   10.10.210.0/24	      10.10.210.1
220	        Guest/Public WiFi	                               10.10.220.0/24	      10.10.220.1
230	        Public Kiosk Terminals                           10.10.230.0/24	      10.10.230.1
240	        CCTV / Security Cameras                          10.10.240.0/24	      10.10.240.1

VLAN Grouping Rationale
Smaller offices like Public Info, Tourism, and Cooperative Development are grouped into one VLAN (190) instead of having separate VLANs. Since these offices have only a few users, separate VLANs would add extra management work without much security benefit. Grouping offices with similar functions and security needs makes the network simpler and easier to manage.

Security-Sensitive VLANs (flagged for the ACL design)
These VLANs handle resident data or financial records and must be explicitly walled off from public-facing VLANs (220 Guest WiFi, 230 Kiosks):
* VLAN 30 — Treasurer's Office (tax/payment records)
* VLAN 40 — Assessor's Office (property records)
* VLAN 70 — Civil Registrar (birth/marriage/death records — PII)
* VLAN 100 — MSWD (sensitive social welfare case data)
* VLAN 180 — IT/MIS + servers (infrastructure access)
