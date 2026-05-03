# Cisco ACI Low-Level Design (LLD) Framework

Use this framework to produce a structured LLD from design inputs. Each section
includes the data to capture, design decisions to surface, and human-review gates.

**This is a design framework, not a production-ready configuration generator.
Every section requires qualified engineer review before implementation.**

---

```
# [Project Name] — Cisco ACI Low-Level Design
**Version:** Draft
**Date:** [date]
**Author:** [name]
**Status:** Draft — requires implementation review
**Related HLD:** [reference]
```

## 1. Fabric Access Policies

### 1.1 VLAN Pools

| VLAN Pool | Mode | Range(s) | Purpose | Decision | Rationale |
|-----------|------|----------|---------|----------|-----------|
| | static/dynamic | | | | |

**Decision prompt:** Static pools for bare-metal and fixed VLAN assignments.
Dynamic pools for VMM-managed workloads. Confirm pool sizing allows for growth.

**Review gate:** Verify no VLAN range overlaps across pools.

### 1.2 Physical Domains

| Domain | Type | VLAN Pool | Mode | Purpose |
|--------|------|-----------|------|---------|
| | physical/l3out/vmm | | static/dynamic | |

**Decision prompt:** Each domain type serves a different attachment model.
Physical for bare-metal, L3 for routed interfaces, VMM for hypervisor integration.
Confirm domain-to-pool relationships match the intended use.

### 1.3 AEPs (Attachable Access Entity Profiles)

| AEP | Associated Domains | Purpose |
|-----|-------------------|---------|
| | [domain/type, domain/type] | |

**Decision prompt:** AEPs link domains to interface policies. Confirm that each
AEP covers the right combination of physical and L3 domains for its intended ports.

**Review gate:** Verify no unintended VLAN exposure through overly broad AEP-to-domain mappings.

### 1.4 Interface Policy Groups

| Policy Group | Type | Speed | CDP | LLDP | LACP | AEP | Purpose |
|-------------|------|-------|-----|------|------|-----|---------|
| | access/pc/vpc | | on/off | on/off | active/passive | | |

**Decision prompt:** Storm control, port security, MCP (Mis-Cabling Protocol),
and BPDU guard settings are site-specific. Confirm with the operations team.

### 1.5 Leaf Interface Profiles

| Interface Profile | Port Selectors | Policy Group | Ports |
|------------------|---------------|--------------|-------|
| | | | |

**Decision prompt:** Match interface profiles to the physical cabling plan.
Every connected port needs a selector with the correct policy group.

### 1.6 Leaf Switch Profiles

| Switch Profile | Switch Selector | Node ID(s) | Interface Profile |
|---------------|----------------|------------|-------------------|
| | | | |

**Decision prompt:** Confirm node IDs match the physical fabric discovery.
Verify each leaf has exactly one switch profile.

---

## 2. Tenant Configuration

### 2.1 Tenants

| Tenant | Purpose | Owner | Notes |
|--------|---------|-------|-------|
| | | | |

### 2.2 VRFs

| VRF | Tenant | Enforcement | BD Enforcement | IP Learning | Route Leaking | Decision | Rationale |
|-----|--------|-------------|---------------|-------------|---------------|----------|-----------|
| | | enforced/unenforced | yes/no | enabled | to which VRFs? | | |

**Decision prompt:** Unenforced VRFs allow all traffic — useful during migration
but must be tightened. Document the timeline for enforcement if starting unenforced.

**Review gate:** Route leaking between VRFs has security implications. Confirm with security team.

### 2.3 Bridge Domains

| BD | Tenant | VRF | Type | Gateway | Mask | ARP Flood | Unicast Route | UMU | HBR | Decision | Rationale |
|----|--------|-----|------|---------|------|-----------|---------------|-----|-----|----------|-----------|
| | | | L2/L3 | | | yes/no | yes/no | flood/proxy | yes/no | | |

Key:
- UMU = Unknown MAC Unicast (flood or proxy)
- HBR = Host-Based Routing

**Decision prompt:** L3 BDs with proxy UMU and HBR enabled are the modern default.
L2-only BDs need flood mode. Confirm each BD's routing requirement.

**Review gate:** Subnet scope (public, private, shared) affects route advertisement
and contract enforcement. Verify scope for each subnet.

### 2.4 Subnets

| Subnet | BD | Scope | Primary | Purpose |
|--------|-----|-------|---------|---------|
| | | public,shared / private | yes/no | |

### 2.5 Application Profiles

| AP | Tenant | Grouping Strategy | EPGs |
|----|--------|--------------------|------|
| | | by-app / by-tier / by-zone | |

### 2.6 EPGs

| EPG | AP | Tenant | BD | VLAN | Domain(s) | Preferred Group | Decision | Rationale |
|-----|-----|--------|-----|------|-----------|----------------|----------|-----------|
| | | | | | | yes/no | | |

**Decision prompt:** Domain binding determines where the EPG is deployable.
VLAN encapsulation must be unique per leaf per physical domain.

**Review gate:** Preferred group EPGs communicate freely — confirm this matches
the intended security posture.

---

## 3. Contracts and Filters

### 3.1 Filters

| Filter | Entries (protocol / dst port) | Purpose |
|--------|------------------------------|---------|
| | | |

### 3.2 Contracts

| Contract | Tenant | Scope | Subject | Filter(s) | Direction | Decision | Rationale |
|----------|--------|-------|---------|-----------|-----------|----------|-----------|
| | | tenant/global/vrf | | | both/in/out | | |

### 3.3 Contract Assignments

| EPG | Provides | Consumes | Rationale |
|-----|----------|----------|-----------|
| | [contracts] | [contracts] | |

**Decision prompt:** Contract scope determines reachability boundaries.
Global scope contracts cross tenant boundaries — confirm this is intentional.

**Review gate:** Verify that every communication path has a contract.
Undocumented "allow-all" contracts should have a documented tightening timeline.

---

## 4. L3Out Configuration

### 4.1 L3Out Summary

| L3Out | Tenant | VRF | Domain | Protocol | VLAN | Purpose |
|-------|--------|-----|--------|----------|------|---------|
| | | | | BGP/OSPF/Static | | |

### 4.2 Node Profiles

| L3Out | Node Profile | Nodes | Loopback IPs | Interface Type |
|-------|-------------|-------|-------------|----------------|
| | | | | SVI/routed/sub-if |

### 4.3 Interface Profiles

| L3Out | Interface Profile | Path | IP/Mask | MTU | Encap |
|-------|------------------|------|---------|-----|-------|
| | | | | | |

### 4.4 BGP Peers

| L3Out | Peer IP | Remote AS | Local AS | Address Family | BFD | Decision | Rationale |
|-------|---------|-----------|----------|---------------|-----|----------|-----------|
| | | | | IPv4-ucast | yes/no | | |

### 4.5 External EPGs

| External EPG | L3Out | Subnets | Scope | Contracts | Decision |
|-------------|-------|---------|-------|-----------|----------|
| | | | import/export/shared | provided/consumed | |

**Review gate:** External EPG subnet classification affects security policy.
Confirm 0.0.0.0/0 placement and scope with security team.

---

## 5. VMM Integration

| VMM Domain | vCenter | Datacenter | vDS | VLAN Pool | Resolution | Deployment |
|-----------|---------|------------|-----|-----------|------------|------------|
| | | | | | immediate/lazy | immediate/lazy |

**Decision prompt:** Immediate resolution creates port-groups as soon as EPGs
are associated. Lazy waits until a VM is placed. Choose based on operational preference.

---

## 6. Naming Conventions

| Object Type | Pattern | Example |
|-------------|---------|---------|
| Tenant | | |
| VRF | | |
| BD | | |
| EPG | | |
| AP | | |
| Contract | | |
| L3Out | | |
| Switch Profile | | |
| Interface Profile | | |
| VLAN Pool | | |
| Domain | | |
| AEP | | |

---

## 7. Implementation Sequence

| Step | Task | Dependencies | Validation | Owner |
|------|------|-------------|-----------|-------|
| 1 | Fabric discovery and initial setup | Hardware racked, cabled, powered | APIC discovers all nodes | |
| 2 | Infrastructure services (NTP, DNS, AAA, syslog) | APIC accessible | Time sync verified, AAA login tested | |
| 3 | Fabric access policies (VLAN pools, domains, AEPs) | Step 2 complete | Policies visible in APIC | |
| 4 | Interface policies (profiles, selectors, policy groups) | Step 3 complete | Port status verified | |
| 5 | Tenant / VRF / BD / EPG deployment | Step 4 complete | EPGs resolvable, BDs operational | |
| 6 | Static path bindings | Step 5 complete | VLAN active on correct ports | |
| 7 | Contracts and filters | Step 5 complete | Traffic permitted per contract | |
| 8 | L3Out and external connectivity | Steps 5-7 complete | BGP/OSPF adjacency up, routes exchanged | |
| 9 | VMM integration | vCenter accessible, Step 3 complete | Port-groups created, VMs reachable | |
| 10 | Services integration (FW, LB) | Steps 5-8 complete | Service graph active, traffic flowing | |
| 11 | Migration cutover | All above validated | Workloads accessible from new fabric | |
| 12 | Post-deployment validation | Step 11 complete | Full test plan executed | |

---

## 8. Validation Plan

| Test | Method | Expected Result | Pass/Fail | Notes |
|------|--------|----------------|-----------|-------|
| APIC cluster health | APIC GUI / API | 3 APICs fully fit | | |
| All nodes discovered | `fabricNode.json` | All spines and leaves present | | |
| NTP sync | `topSystem.json` | All nodes synced | | |
| Spine-leaf connectivity | Fabric health score | No fabric link faults | | |
| VLAN pools deployed | `fvnsVlanInstP.json` | All pools with correct ranges | | |
| EPG resolution | `fvAEPg.json` | EPGs bound to correct domains | | |
| Static path active | `fvRsPathAtt.json` | Paths deployed on correct ports | | |
| L3Out adjacency | `bgpPeerEntry.json` | BGP peers established | | |
| End-to-end reachability | Ping/traceroute | Workloads reachable across fabric | | |
| Contract enforcement | Traffic test | Permitted traffic flows, denied traffic blocked | | |

**Review gate:** Validation must be executed by the deployment team and results
documented before handoff to operations.

---

## 9. Rollback Plan

| Trigger | Rollback Action | Time Estimate | Owner |
|---------|----------------|---------------|-------|
| Fabric discovery failure | Re-image APIC, re-cable | 2-4 hours | |
| Incorrect VLAN/EPG binding | Remove static path, verify port | 15 minutes | |
| L3Out adjacency failure | Revert to legacy routing path | 30 minutes | |
| Workload unreachable after migration | Re-trunk legacy VLAN to original switch | 15-30 minutes | |
| Full deployment failure | Restore APIC config backup, revert cabling | 4-8 hours | |

**Decision prompt:** Define the rollback decision authority and communication
chain before deployment begins. Document the point-of-no-return if one exists.

---

## 10. As-Built Documentation Checklist

After deployment, produce documentation covering:

- [ ] Final fabric topology diagram (physical and logical)
- [ ] APIC version and node inventory
- [ ] Tenant / VRF / BD / EPG / contract configuration summary
- [ ] L3Out peering and routing summary
- [ ] Interface and cabling documentation
- [ ] IP address assignments
- [ ] VLAN pool and domain mappings
- [ ] Deviations from the LLD (with rationale)
- [ ] Validation test results
- [ ] Known issues and workarounds
- [ ] Operational runbook (monitoring, backup, firmware upgrade procedures)
- [ ] Handoff sign-off

---

**Note:** This LLD framework is produced with AI assistance. Every design table,
decision prompt, and configuration detail requires review by a qualified network
engineer before implementation. Site-specific judgment — around security policy,
migration risk, integration dependencies, and operational procedures — cannot be
automated.
