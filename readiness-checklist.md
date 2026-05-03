# Cisco ACI Deployment Readiness Checklist

Use this framework to assess whether an ACI deployment project has the inputs,
decisions, and prerequisites needed to proceed. Walk through each section with
the user, collect what is available, and produce a readiness report.

## How to use this checklist

1. Present each section to the user and collect their inputs
2. For each item, capture: the answer, whether it is confirmed or assumed, and any open questions
3. At the end, produce the readiness report (template below)
4. Do not skip sections — missing information is valuable signal

---

## 1. Project Context

| Item | Input needed |
|------|-------------|
| Greenfield or brownfield? | New fabric, expansion, migration, or overlay? |
| Project driver | What is motivating this deployment? (new DC, refresh, segmentation, compliance) |
| Timeline and change windows | Target go-live, available maintenance windows, blackout dates |
| Stakeholders | Who approves design? Who executes? Who validates? Who owns post-deployment? |
| Scope boundaries | What is in scope and explicitly out of scope? |

## 2. Fabric Hardware and Software

| Item | Input needed |
|------|-------------|
| APIC cluster | How many APICs? Version? Physical or virtual? |
| Spine switches | Model(s), quantity, port density |
| Leaf switches | Model(s), quantity, port density, FEX requirements |
| Target ACI software version | APIC and switch firmware target |
| Licensing | ACI license tier (Essentials, Advantage, Premier) |
| Hardware staging | Are all devices racked, cabled, and powered? |

## 3. Physical Topology

| Item | Input needed |
|------|-------------|
| Spine-leaf cabling | Cabling plan documented? Which spine ports connect to which leaves? |
| Uplink speed | 40G, 100G, 400G spine-leaf links? |
| Pod design | Single pod or multi-pod? |
| OOB management | Out-of-band management network ready? IP assignments for APIC, spines, leaves? |
| Console access | Console server or direct console access available during deployment? |
| TEP pool | IP range for fabric TEP pool (typically /16, e.g., 10.0.0.0/16) |

## 4. Infrastructure Services

| Item | Input needed |
|------|-------------|
| NTP | NTP server IPs, stratum, reachability from fabric |
| DNS | DNS server IPs, domain name |
| AAA / TACACS+ / RADIUS | Authentication servers, shared secrets, admin roles |
| Syslog | Syslog server IPs, facility, severity level |
| SNMP | SNMP version, community strings or v3 credentials, trap destinations |
| SMTP / alerting | Email relay for fault notifications |

## 5. Naming Standards

| Item | Input needed |
|------|-------------|
| Naming convention | Documented naming standard for tenants, VRFs, BDs, EPGs, profiles, policies? |
| Examples | Provide examples: tenant names, BD names, EPG names, switch profile names |
| Tagging / annotations | Any metadata tagging requirements? |

## 6. Fabric Access Policies

| Item | Input needed |
|------|-------------|
| VLAN pools | Pool names, VLAN ranges, allocation mode (static vs dynamic) |
| Physical domains | Domain names, associated VLAN pools |
| L3 domains | L3 domain names, VLAN pools for routed interfaces |
| VMM domains | VMware vCenter integration? vDS names? |
| AEPs | Attachable entity profiles — which domains associate to which AEPs? |
| Interface policy groups | Access port groups, PC groups, VPC groups — speed, CDP/LLDP, storm control |
| Leaf switch profiles | Which leaves get which interface profiles? |
| Interface profiles | Port selector names, port ranges, policy group assignments |

## 7. Tenant and Network Design

| Item | Input needed |
|------|-------------|
| Tenant model | Single tenant? Per-application? Per-business-unit? Per-environment? |
| VRFs | VRF names, route leaking requirements, policy enforcement (enforced/unenforced) |
| Bridge domains | BD names, associated VRF, L2/L3, gateway IPs, ARP flooding, unicast routing |
| Subnets | Subnet IPs with masks, scope (public, private, shared), primary gateway |
| EPGs | EPG names, associated BD, domain bindings, VLAN encapsulation |
| Application profiles | AP grouping strategy |
| Static path bindings | Which EPGs bind to which leaf/port/VLAN combinations? |

## 8. Contracts and Segmentation

| Item | Input needed |
|------|-------------|
| Contract strategy | Permit-all initially? Whitelist model? Phased tightening? |
| Contract definitions | Contract names, subjects, filters (protocols, ports) |
| Provider/consumer mappings | Which EPGs provide and consume which contracts? |
| Preferred groups | Any EPGs using preferred group for open communication? |
| vzAny | Any VRF-level vzAny contracts planned? |
| Microsegmentation | Any intra-EPG segmentation requirements? |

## 9. L3Out and External Connectivity

| Item | Input needed |
|------|-------------|
| L3Out count | How many L3Outs? (core, WAN, internet, DMZ, etc.) |
| Routing protocol | OSPF, BGP, EIGRP, or static per L3Out? |
| BGP AS numbers | Fabric BGP AS, external peer AS, route reflector nodes |
| Peering details | Peer IPs, VLAN, interface (SVI, routed, sub-interface) |
| Route control | Import/export policies, prefix lists, route maps |
| External EPGs | External EPG subnets and classifications |
| Default route | Where does 0.0.0.0/0 come from? |

## 10. Services Integration

| Item | Input needed |
|------|-------------|
| Firewall integration | Vendor, model, integration mode (GoTo, GoThrough, PBR)? |
| Load balancer | Vendor, model, integration mode? |
| Service graph | Any L4-L7 service graph requirements? |
| Shared services | Cross-tenant shared services (DNS, AD, monitoring)? |

## 11. VMM / Virtualization Integration

| Item | Input needed |
|------|-------------|
| VMware vCenter | Version, cluster names, vDS configuration |
| VMM domain | Domain name, VLAN pool, resolution immediacy |
| DVS attachment | Which EPGs attach to VMM domain vs physical domain? |
| Microsegmentation | VM-level EPG assignment? |
| Other hypervisors | Hyper-V, KVM, OpenStack, Kubernetes? |

## 12. Migration and Cutover

| Item | Input needed |
|------|-------------|
| Migration approach | Big bang, phased VLAN migration, or parallel run? |
| Migration sequence | Which workloads move first? Dependencies? |
| VLAN stretching | Any VLANs stretched between legacy and ACI during migration? |
| Rollback plan | What triggers a rollback? How do you revert? |
| Change window | Duration, time of day, approval process |
| Communication plan | Who gets notified before, during, and after? |

## 13. Operational Readiness

| Item | Input needed |
|------|-------------|
| Monitoring | What monitors ACI post-deployment? (APIC, Nexus Dashboard, third-party) |
| Alerting | Fault severity thresholds, escalation paths |
| Backup | APIC config backup strategy and schedule |
| Documentation | As-built documentation requirements and handoff format |
| Training | Does the operations team need ACI training? |
| Support | Cisco SmartNet / TAC contract in place? |

---

## Readiness Report Output Format

After collecting inputs, produce this report:

```
# ACI Deployment Readiness Assessment
**Project:** [name]
**Date:** [date]
**Assessed by:** [name/tool]

## Readiness Summary
[1-2 paragraph summary: overall readiness level, key strengths, primary concerns]

## Status by Category
| Category | Status | Notes |
|----------|--------|-------|
| Project Context | Ready / Partial / Not Ready | ... |
| Hardware & Software | Ready / Partial / Not Ready | ... |
| Physical Topology | Ready / Partial / Not Ready | ... |
| Infrastructure Services | Ready / Partial / Not Ready | ... |
| Naming Standards | Ready / Partial / Not Ready | ... |
| Fabric Access Policies | Ready / Partial / Not Ready | ... |
| Tenant & Network Design | Ready / Partial / Not Ready | ... |
| Contracts & Segmentation | Ready / Partial / Not Ready | ... |
| L3Out & External Connectivity | Ready / Partial / Not Ready | ... |
| Services Integration | Ready / Partial / Not Ready | ... |
| VMM Integration | Ready / Partial / Not Ready | ... |
| Migration & Cutover | Ready / Partial / Not Ready | ... |
| Operational Readiness | Ready / Partial / Not Ready | ... |

## Blockers
[Items that must be resolved before deployment can proceed]

## Risks
[Items that could impact deployment success if not addressed]

## Missing Information
[Items where no input was provided — these need follow-up]

## Assumptions
[Items where assumptions were made in the absence of confirmed inputs]

## Discovery Questions
[Specific questions to ask stakeholders to close gaps]

## Recommended Next Steps
[Prioritized list of actions to move toward deployment readiness]
```
