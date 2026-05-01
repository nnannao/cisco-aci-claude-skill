# Cisco ACI Claude Skill

AI-assisted Cisco ACI analysis and deployment workflows for network engineers and consultants.

**Built by [Blue Sodium](https://bluesodium.com)** — Cisco ACI specialists since day one.

## What this is

This skill helps Claude reason through Cisco ACI environments using structured review workflows for tenants, VRFs, bridge domains, EPGs, contracts, L3Outs, and segmentation policy.

It is designed for expert-assisted analysis, documentation, change planning, and deployment — with human approval at every step.

## Example use cases

- Review ACI tenant design for segmentation issues
- Explain EPG-to-EPG communication paths
- Analyze contract intent and gaps
- Generate migration/change review checklists
- Produce client-facing ACI assessment notes
- Help document existing ACI fabric policy structure
- Read live fabric state (health, faults, firmware, endpoints)
- Build and deploy tenant/VRF/BD/EPG configurations from natural language or spreadsheet
- Create static path bindings, contracts, L3Outs, and VLAN pools

## Read and write

**Reads** pull live data from the APIC REST API — fabric health, tenant inventory, faults, firmware versions, endpoints. Always safe, no approval needed beyond standard Claude Code permissions.

**Writes require human approval.** Claude builds the JSON payload, shows it to you, and waits for you to approve the execution. Nothing is pushed to APIC without your explicit confirmation. This is the correct workflow — no network engineer wants an AI pushing configs without review.

## What it is not

- Not an autonomous network change tool
- Not a replacement for Cisco expertise
- Not a vulnerability scanner

## Why it exists

Most AI tools can summarize configs, but they do not naturally understand how network engineers reason through ACI policy models. This skill encodes repeatable analysis patterns from real network consulting work.

## Install

```bash
# One-liner install (global — available in all projects)
curl -o ~/.claude/skills/aci-operator.md \
  https://raw.githubusercontent.com/nnannao/cisco-aci-claude-skill/main/aci-operator.md
```

Or clone and copy:

```bash
git clone https://github.com/nnannao/cisco-aci-claude-skill.git
cp cisco-aci-claude-skill/aci-operator.md ~/.claude/skills/
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

## Usage

```
/aci show me the fabric health
/aci list all tenants
/aci create a tenant called ACME with a server VLAN on 10.10.10.0/24
/aci what firmware are the switches running?
/aci show all faults
/aci add VLAN 100 to leaf 101 port 1/1 on EPG Servers
/aci audit the fabric
/aci deploy from /path/to/my-aci-deploy.xlsx
```

## Spreadsheet deployment

A template spreadsheet is included at [`templates/aci-deploy-template.xlsx`](templates/aci-deploy-template.xlsx) with these tabs:

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

Download the template, fill in your data, and tell Claude the path. It reads all tabs, builds the full ACI JSON tree, shows a dry-run summary, and deploys on your confirmation.

## Requirements

- [Claude Code](https://claude.ai/code)
- Network access to your APIC
- APIC admin credentials
- `curl` and `python3` (included on macOS/Linux)

## License

MIT — see [LICENSE](LICENSE).

## About Blue Sodium

[Blue Sodium](https://bluesodium.com) specializes in Cisco ACI, data center networking, and network security. 30+ years of infrastructure experience, from programming to CCIE to AI-assisted operations.
