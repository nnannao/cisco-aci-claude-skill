# Cisco ACI Claude Code Skill

A [Claude Code](https://claude.ai/code) skill that lets you manage Cisco ACI fabrics through natural language. Read fabric state, create tenants, deploy EPGs, audit health, and push configurations — all by talking to Claude.

**Built by [Blue Sodium](https://bluesodium.com)** — Cisco ACI specialists since day one.

## Demo

```
You: show me the fabric health
Claude: *logs into APIC, pulls health scores, node status, active faults*
       Fabric health: 95. 6 leaves, 2 spines, 3 controllers.
       2 minor faults (interface down on leaf-103 e1/48, NTP sync warning).

You: create a tenant called ACME with a VRF, a BD for servers on 10.10.10.0/24,
     and an EPG called Servers mapped to the BD
Claude: *builds JSON payload, shows it*
       Here's the deployment payload. Should I push this to APIC?

You: yes
Claude: *POSTs to APIC*
       Done. Tenant ACME created with VRF, BD (10.10.10.1/24), and Servers EPG.
```

## Install

Copy the skill file to your Claude Code skills directory:

```bash
# Global (available in all projects)
cp aci-operator.md ~/.claude/skills/

# Or project-specific
mkdir -p .claude/skills/
cp aci-operator.md .claude/skills/
```

Restart Claude Code. You'll see `/aci` in your available slash commands.

## Setup

Set your APIC credentials as environment variables:

```bash
export APIC_HOST=https://10.1.1.1
export APIC_USERNAME=admin
export APIC_PASSWORD=your-password
export APIC_DOMAIN=              # optional: for remote auth domains
```

Or add them to your shell profile (`~/.zshrc`, `~/.bashrc`).

## Usage

Use the `/aci` slash command or just ask Claude about ACI in any conversation:

```
/aci show me the fabric health
/aci list all tenants
/aci create a tenant called ACME with a server VLAN on 10.10.10.0/24
/aci what firmware are the switches running?
/aci show all faults
/aci add VLAN 100 to leaf 101 port 1/1 on EPG Servers
/aci audit the fabric
```

## What It Can Do

### Read (always safe)
- Fabric health scores and node status
- Tenant, VRF, BD, EPG, subnet inventory
- Contract and filter configuration
- L3Out and BGP peer status
- Switch/spine profiles and interface policies
- VLAN pools, domains, AEPs
- Firmware versions across all nodes
- Active faults by severity
- Learned endpoints (MAC/IP)

### Write (dry-run first, then confirm)
- Create/modify/delete tenants, VRFs, BDs, EPGs
- Add subnets to bridge domains
- Create contracts and attach to EPGs
- Configure static path bindings (VLAN-to-port)
- Set up L3Outs with BGP/OSPF/static routing
- Create switch profiles, interface profiles, policy groups
- Configure VLAN pools, physical domains, AEPs
- Spreadsheet-driven bulk deployment

### Audit
- Full fabric inventory report
- Health score analysis per node
- Fault summary and trending
- Firmware version audit
- Unused EPG detection
- Contract hit analysis

## Safety

- **Dry-run by default.** Every write operation shows the JSON payload before pushing. You must confirm.
- **No credential storage.** Credentials come from environment variables only.
- **Read before write.** The skill checks if objects exist before creating them.
- **Delete protection.** Tenant/VRF/BD deletions require explicit confirmation with impact summary.

## Spreadsheet Deployment

A template spreadsheet is included at [`templates/aci-deploy-template.xlsx`](templates/aci-deploy-template.xlsx). It has these tabs pre-formatted with sample data:

| Tab | Purpose |
|-----|---------|
| **Tenants** | Tenant, VRF, BD, EPG, gateway, VLAN, domain mapping |
| **Leaf Profiles** | Switch profiles and interface selector profiles |
| **VPC Profiles** | VPC pairs with node IDs and policy groups |
| **vlan pools** | VLAN pool names, modes, and ranges |
| **domains** | Physical and L3 domains with VLAN pool bindings |
| **aaep** | Attachable Access Entity Profiles with domain links |
| **L3Out** | L3Out definitions with nodes, VRF, loopbacks |
| **Device Inventory** | Fabric node reference (APICs, spines, leaves) |

**To use:**
1. Download the template and fill in your data
2. Tell Claude the path:

```
/aci deploy from /path/to/my-aci-deploy.xlsx
```

Claude reads all tabs, builds the full ACI JSON tree, shows a dry-run summary, and deploys on your confirmation.

## Requirements

- Claude Code (claude.ai/code)
- Network access to your APIC
- APIC admin credentials
- `curl` and `python3` (included on macOS/Linux)

## License

MIT — see [LICENSE](LICENSE).

## About Blue Sodium

[Blue Sodium](https://bluesodium.com) specializes in Cisco ACI, data center networking, and network security. 30+ years of infrastructure experience, from programming to CCIE to AI-assisted operations.
