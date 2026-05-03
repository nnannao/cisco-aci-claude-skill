# Cisco ACI High-Level Design (HLD) Template

Use this template to produce a draft HLD from the user's project inputs.
Fill each section from available information. Mark gaps and assumptions clearly.

**This is a draft for qualified review, not an approved production design.**

---

```
# [Project Name] — Cisco ACI High-Level Design
**Version:** Draft
**Date:** [date]
**Author:** [name]
**Status:** Draft — requires design review before implementation
```

## 1. Executive Summary

Brief overview of the project: what is being deployed, why, for whom,
and the expected outcome. 2-3 paragraphs maximum.

## 2. Business and Technical Objectives

| Objective | Type | Priority |
|-----------|------|----------|
| [e.g., Segment server and user traffic] | Technical | High |
| [e.g., Reduce east-west attack surface] | Security | High |
| [e.g., Support VMware workload mobility] | Operational | Medium |

## 3. Scope

### In Scope
- [List what this design covers]

### Out of Scope
- [List what this design explicitly does not cover]

### Assumptions
- [List assumptions made during design — mark each clearly]

## 4. Current-State Summary

Describe the existing network environment:
- Current data center topology (legacy switching, routing)
- Existing VLAN structure
- Current firewall and security architecture
- WAN/internet connectivity
- Virtualization platform
- Known pain points or limitations

## 5. Target-State Architecture

Describe the target ACI environment at a high level:
- ACI fabric role in the overall network
- How ACI integrates with existing infrastructure
- What changes for applications, users, and operations

*Include a logical architecture diagram description if possible.*

## 6. Physical Fabric Overview

| Component | Detail |
|-----------|--------|
| APIC cluster | [count, version, placement] |
| Spine switches | [model, count, uplink speed] |
| Leaf switches | [model, count, port density] |
| Pod design | [single pod / multi-pod] |
| Fabric uplinks | [speed, redundancy] |
| OOB management | [management network, IP scheme] |
| TEP pool | [IP range] |

## 7. Logical Fabric Design

### Tenant Strategy
Describe the tenant model: single tenant, per-application, per-business-unit,
per-environment, or hybrid. Explain the rationale.

### VRF Design
| VRF | Tenant | Purpose | Enforcement | Route Leaking |
|-----|--------|---------|-------------|---------------|
| [name] | [tenant] | [purpose] | Enforced/Unenforced | Yes/No |

### Bridge Domain Strategy
Describe BD design principles: L2 vs L3, subnet scope, ARP flooding,
unicast routing, endpoint move detection.

### EPG Strategy
Describe EPG grouping approach: by application tier, VLAN, function,
or security zone. Explain domain bindings and VLAN encapsulation approach.

### Application Profile Structure
Describe how APs organize EPGs within each tenant.

## 8. Segmentation and Policy Model

### Contract Strategy
Describe the approach: permit-all during migration, phased tightening,
whitelist from day one, or preferred groups.

### Contract Summary
| Contract | Scope | Provider EPG(s) | Consumer EPG(s) | Filters |
|----------|-------|----------------|-----------------|---------|
| [name] | [tenant/global] | [EPGs] | [EPGs] | [protocol/port] |

### Security Considerations
- Inter-tenant isolation approach
- Microsegmentation requirements
- Preferred group usage and risks
- vzAny usage and implications

## 9. L3Out and External Connectivity

| L3Out | Tenant | VRF | Protocol | Peer | Purpose |
|-------|--------|-----|----------|------|---------|
| [name] | [tenant] | [VRF] | BGP/OSPF/Static | [peer IP/AS] | [core/WAN/internet] |

### Routing Design
- BGP AS number strategy (fabric AS, external AS)
- Route reflector placement
- Route control (import/export policies)
- Default route sourcing

### External EPGs
| External EPG | L3Out | Subnets | Classification |
|-------------|-------|---------|----------------|
| [name] | [L3Out] | [subnets] | [purpose] |

## 10. Services Integration

### Firewall Integration
- Vendor and model
- Integration mode (GoTo, GoThrough, PBR, service graph)
- Traffic steering approach

### Load Balancer Integration
- Vendor and model
- Integration mode
- VIP and pool placement

### Shared Services
- Cross-tenant services (DNS, AD, monitoring, backup)
- How shared services are consumed (shared VRF, contract export, route leaking)

## 11. VMM / Virtualization Integration

| VMM Domain | Type | vCenter | vDS | Resolution | VLAN Pool |
|-----------|------|---------|-----|------------|-----------|
| [name] | VMware | [vCenter] | [vDS name] | Immediate/Lazy | [pool] |

### VMM Considerations
- EPG-to-port-group mapping approach
- VM mobility and endpoint learning
- Microsegmentation at VM level

## 12. High Availability and Resiliency

- APIC cluster redundancy (3-node minimum)
- Spine-leaf redundancy (dual-homed leaves, VPC)
- L3Out redundancy (dual BGP peers, BFD)
- Failure domain analysis (what happens when a spine/leaf/APIC fails?)
- Graceful restart and fast convergence settings

## 13. Migration Approach

### Strategy
[Big bang / phased VLAN migration / parallel run / hybrid]

### Migration Sequence
| Phase | Workloads | Approach | Dependencies | Rollback |
|-------|-----------|----------|-------------|----------|
| 1 | [first movers] | [method] | [dependencies] | [rollback method] |
| 2 | ... | ... | ... | ... |

### Rollback Considerations
- What triggers a rollback decision?
- How is rollback executed? (VLAN revert, routing change, config restore)
- Maximum acceptable rollback time

## 14. Operational Considerations

- Monitoring and alerting (APIC, Nexus Dashboard, third-party)
- APIC configuration backup schedule
- Firmware upgrade strategy
- Change management process
- Training requirements for operations team
- TAC support coverage

## 15. Risks and Dependencies

| Risk | Impact | Likelihood | Mitigation |
|------|--------|-----------|-----------|
| [risk description] | High/Med/Low | High/Med/Low | [mitigation] |

### Dependencies
| Dependency | Owner | Status | Impact if Delayed |
|-----------|-------|--------|-------------------|
| [dependency] | [owner] | [status] | [impact] |

## 16. Open Decisions

| Decision | Options | Recommendation | Owner | Due Date |
|----------|---------|---------------|-------|----------|
| [decision needed] | [options] | [recommendation] | [who decides] | [when] |

---

**Note:** This HLD is a draft produced with AI assistance. It requires review
by a qualified network architect before implementation. Design decisions,
security policies, and migration sequencing must be validated against
site-specific requirements and organizational standards.
