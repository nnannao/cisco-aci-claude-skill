# Sample ACI Deployment Package — Meridian Financial Services

*This is a demo using fictional data to illustrate the output format and depth
produced by the Cisco ACI Deployment Skill.*

---

## Readiness Assessment (Excerpt)

### Project Context
- **Type:** Greenfield ACI deployment in a new colocation data center
- **Driver:** Consolidating two legacy Nexus 7K/5K environments into a single ACI fabric
- **Timeline:** 8-week implementation window, go-live target June 15
- **Scope:** Core data center switching, firewall integration, VMware workloads

### Status by Category

| Category | Status | Notes |
|----------|--------|-------|
| Hardware & Software | Ready | All hardware staged, APIC 6.0(4d) targeted |
| Physical Topology | Ready | Cabling plan reviewed and approved |
| Infrastructure Services | Partial | NTP confirmed, AAA/TACACS config pending |
| Naming Standards | Partial | Tenant naming agreed, interface naming TBD |
| Fabric Access Policies | Not Ready | VLAN pools defined, AEP/domain mapping not finalized |
| Tenant & Network Design | Partial | 3 tenants scoped, BD/EPG mapping 70% complete |
| Contracts & Segmentation | Not Ready | Strategy agreed (phased), no contracts defined yet |
| L3Out & External Connectivity | Partial | Core L3Out designed, WAN L3Out pending ISP details |
| Services Integration | Partial | Palo Alto PBR planned, design not started |
| VMM Integration | Ready | vCenter 8.0, vDS planned, domain/pool defined |
| Migration & Cutover | Not Ready | No migration sequence documented |
| Operational Readiness | Not Ready | No monitoring or backup plan documented |

### Blockers
1. **AAA configuration not finalized.** TACACS server IPs and shared secrets not provided by security team.
2. **Migration sequence not documented.** Cannot proceed with cutover planning without workload dependency mapping.

### Risks
1. **Contract strategy is "phased tightening" but no timeline defined.** Risk of running permit-all indefinitely.
2. **Palo Alto integration design not started.** PBR service graph requires lead time for testing.
3. **No rollback plan documented.** 8-week window is tight if a major issue requires reverting.

### Missing Information
- TACACS server details (IP, shared secret, admin roles)
- WAN ISP BGP peering details (peer IP, remote AS, prefix limits)
- Firewall PBR redirect IP addresses
- Migration workload dependency matrix
- Operational monitoring tool selection

### Assumptions
- `[ASSUMPTION]` All leaf switches are dual-homed to both spines
- `[ASSUMPTION]` VMware vDS will be managed by ACI VMM domain (not standalone)
- `[ASSUMPTION]` No multi-pod or multi-site requirement in initial deployment

### Recommended Next Steps
1. Finalize AAA/TACACS configuration with security team (blocker)
2. Document migration sequence with application owners (blocker)
3. Complete contract and filter definitions with network security team
4. Begin Palo Alto PBR integration design
5. Define operational monitoring and backup procedures

---

## HLD Excerpt

### Target-State Architecture

Meridian Financial Services will deploy a single-pod ACI fabric in their
Newark colocation facility. The fabric replaces two legacy Nexus 7K/5K
environments and consolidates all data center switching into a leaf-spine
architecture managed by a 3-node APIC cluster.

The fabric hosts three tenants: Production, Development, and Shared Services.
Each tenant has its own VRF with enforced policy. Shared Services (DNS, AD,
SIEM) are consumed cross-tenant via contract export.

External connectivity is provided by two L3Outs: a core L3Out peering with
the campus network via BGP, and a WAN L3Out peering with the ISP for
internet-bound traffic routed through the Palo Alto firewall via PBR.

### Physical Fabric

| Component | Detail |
|-----------|--------|
| APIC cluster | 3x APIC-SERVER-M3, version 6.0(4d) |
| Spines | 2x N9K-C9336C-FX2, 100G uplinks |
| Leaves | 6x N9K-C93180YC-FX, 48x 1/10/25G + 6x 40/100G |
| TEP pool | 10.0.0.0/16 |
| OOB management | 172.16.0.0/24 |

### Tenant Model

| Tenant | VRF | Purpose | Enforcement |
|--------|-----|---------|-------------|
| MFS-Prod | MFS-Prod-VRF | Production workloads | Enforced |
| MFS-Dev | MFS-Dev-VRF | Development and test | Enforced |
| MFS-Shared | MFS-Shared-VRF | DNS, AD, SIEM, backup | Enforced |

---

## LLD Excerpt

### VLAN Pools

| VLAN Pool | Mode | Range | Purpose | Decision | Rationale |
|-----------|------|-------|---------|----------|-----------|
| MFS-Prod-Static | static | 100-299 | Production bare-metal and physical appliances | Confirmed | Static for predictable VLAN-to-port mapping |
| MFS-Dev-Static | static | 300-399 | Development bare-metal | Confirmed | Separate pool isolates dev VLANs |
| MFS-VMM-Dynamic | dynamic | 1000-1199 | VMware managed EPGs | Confirmed | Dynamic for vCenter-driven port-group creation |

### Bridge Domains (Production Tenant)

| BD | VRF | Gateway | Mask | ARP Flood | Unicast Route | UMU | HBR | Decision | Rationale |
|----|-----|---------|------|-----------|---------------|-----|-----|----------|-----------|
| MFS-Web-BD | MFS-Prod-VRF | 10.10.10.1 | /24 | no | yes | proxy | yes | Confirmed | L3 BD, proxy mode for scalability |
| MFS-App-BD | MFS-Prod-VRF | 10.10.20.1 | /24 | no | yes | proxy | yes | Confirmed | L3 BD, standard settings |
| MFS-DB-BD | MFS-Prod-VRF | 10.10.30.1 | /24 | no | yes | proxy | yes | Confirmed | L3 BD, database tier |
| MFS-Legacy-BD | MFS-Prod-VRF | — | — | yes | no | flood | no | `[ASSUMPTION]` | L2-only for legacy VLAN stretch during migration. Confirm with app team. |

### Contracts (Production Tenant)

| Contract | Subject | Filter | Provider | Consumer | Decision | Rationale |
|----------|---------|--------|----------|----------|----------|-----------|
| Web-to-App | Web-App-Subj | TCP/8080,8443 | MFS-App-EPG | MFS-Web-EPG | Confirmed | Standard web-to-app flow |
| App-to-DB | App-DB-Subj | TCP/1433,3306 | MFS-DB-EPG | MFS-App-EPG | Confirmed | SQL Server + MySQL |
| Shared-DNS | DNS-Subj | UDP/53, TCP/53 | MFS-DNS-EPG (exported) | All tenants | Confirmed | Cross-tenant DNS via contract export |
| Temp-Permit-All | Allow-All-Subj | default (permit) | — | — | `[REVIEW]` | Migration only. Tightening deadline: go-live + 30 days. Document removal plan. |

---

## Risk Analysis

| Risk | Impact | Likelihood | Mitigation |
|------|--------|-----------|-----------|
| Permit-all contract persists beyond migration | High — no segmentation enforcement | Medium | Document tightening deadline, assign contract owner, create removal runbook |
| Palo Alto PBR integration delays deployment | High — internet traffic has no inspection path | Medium | Begin PBR design immediately, have a static-route fallback for initial go-live |
| Legacy VLAN stretch during migration creates broadcast domain spanning both fabrics | Medium — broadcast storms, endpoint flapping | Low | Limit stretch duration, enable MCP on ACI, monitor endpoint moves |
| TACACS failure locks out admin access | High — cannot manage fabric | Low | Configure local fallback admin account on all APICs |
| Single ISP L3Out is a single point of failure | Medium — internet outage if ISP link fails | Medium | Confirm SLA with ISP, plan secondary ISP L3Out in phase 2 |

---

## Validation Considerations

| Test | Method | Expected Result |
|------|--------|----------------|
| Fabric health after initial setup | APIC health score | Score > 95, no critical faults |
| NTP sync all nodes | `topSystem.json` — check `currentTime` | All nodes within 1 second of NTP source |
| Spine-leaf reachability | Fabric health, ISIS adjacency | All links up, no fabric faults |
| EPG VLAN resolution | Deploy test EPG, check port | VLAN active on expected leaf port |
| L3Out BGP adjacency | `bgpPeerEntry.json` | BGP state = established, routes received |
| Cross-tenant DNS resolution | Ping DNS from prod tenant workload | DNS resolution works from all VRFs |
| PBR redirect (post-integration) | Traceroute from EPG through firewall | Traffic traverses Palo Alto before reaching internet |
| Migration workload reachability | Ping from migrated server to all dependencies | No connectivity loss after VLAN cutover |

---

## Rollback Considerations

- **Pre-migration checkpoint:** APIC config export before each migration phase
- **VLAN revert:** If a workload loses connectivity after migration, re-trunk the legacy VLAN on the original Nexus switch. ACI static path can remain — traffic follows the shortest path.
- **L3Out revert:** If BGP adjacency fails on the ACI L3Out, revert default route to legacy core router. Confirm legacy routing path is still operational before cutover.
- **Full rollback trigger:** If more than 3 production workloads are unreachable after 30 minutes of troubleshooting, invoke full rollback to legacy environment.
- **Rollback authority:** Network lead and change manager jointly decide. Escalation to VP of Infrastructure if rollback extends beyond the maintenance window.

---

*This sample was generated using the Cisco ACI Deployment Skill by Blue Sodium.
All names, IP addresses, and project details are fictional.*
