# Cisco ACI Deployment Skill for Claude Code

AI-assisted readiness assessment, HLD/LLD drafting, implementation planning, validation, and as-built documentation for enterprise Cisco ACI projects.

**Built by [Blue Sodium](https://bluesodium.com)** — Cisco ACI deployment specialists.

## What this is

A [Claude Code](https://claude.ai/code) skill that accelerates Cisco ACI project delivery. It combines live APIC fabric insight with structured deployment workflows used in real consulting engagements.

It is designed for infrastructure teams, consultants, MSPs, and deployment stakeholders who need to plan, document, validate, and deliver ACI projects with fewer gaps and less risk.

## What it does

### Live fabric awareness
Connect to any APIC and pull real-time fabric state — health scores, tenant inventory, faults, firmware versions, endpoints, and policy configuration. Reads are safe. Writes require your explicit approval before anything is pushed.

### Deployment workflows

| Workflow | What it produces |
|----------|-----------------|
| **Readiness assessment** | Gap analysis, blockers, risks, missing prerequisites, discovery questions |
| **HLD generation** | Draft high-level design with architecture, segmentation strategy, integration approach |
| **LLD generation** | Structured low-level design with design tables, decision prompts, validation gates |
| **Implementation planning** | Sequenced deployment steps with dependencies and checkpoints |
| **Validation planning** | Lightweight test plan tied to design intent |
| **As-built documentation** | Post-deployment documentation outline and handoff package |

### Consulting-oriented analysis
- Review ACI tenant design for segmentation issues
- Analyze contract intent and communication paths
- Identify deployment risks and missing details
- Generate client-facing assessment notes
- Document existing fabric policy structure

## Example prompts

```
/aci Create a deployment readiness assessment from these discovery notes.

/aci Generate a draft HLD for a greenfield ACI fabric using this project scope.

/aci Create an LLD for these tenants, VRFs, BDs, EPGs, and L3Outs.

/aci Review this proposed ACI design and identify risks, gaps, and validation tests.

/aci Show me the fabric health and active faults.

/aci Create an as-built documentation outline after this deployment.
```

## What it is not

- Not an autonomous network change tool — writes require human approval
- Not a replacement for qualified ACI design review or production validation
- Not a vulnerability scanner
- Not a fully automated production design generator

The skill drafts, analyzes, and accelerates. The engineer reviews, decides, and approves.

## Why it exists

Most AI tools can summarize configs. They do not naturally understand how network engineers reason through ACI policy models, or how deployment teams plan, sequence, and validate fabric builds.

This skill encodes repeatable analysis and deployment patterns from real ACI consulting work — readiness assessments, design documentation, implementation sequencing, and project handoff.

## Install

```bash
# One-liner install (global — available in all projects)
curl -o ~/.claude/skills/aci-operator.md \
  https://raw.githubusercontent.com/nnannao/cisco-aci-claude-skill/main/aci-operator.md
```

Or clone the repo:

```bash
git clone https://github.com/nnannao/cisco-aci-claude-skill.git
cp cisco-aci-claude-skill/aci-operator.md ~/.claude/skills/
```

Restart Claude Code. You'll see `/aci` in your available slash commands.

## Setup (for live APIC access)

Set your APIC credentials as environment variables:

```bash
export APIC_HOST=https://10.1.1.1
export APIC_USERNAME=admin
export APIC_PASSWORD=your-password
export APIC_DOMAIN=              # optional: for remote auth domains
```

Live APIC access is optional. The readiness, HLD, and LLD workflows work from discovery notes, design inputs, and spreadsheets without a live fabric connection.

## Spreadsheet deployment

A deployment template is included at [`templates/aci-deploy-template.xlsx`](templates/aci-deploy-template.xlsx) with tabs for tenants, leaf profiles, VPC profiles, VLAN pools, domains, AEPs, L3Outs, and device inventory.

```
/aci deploy from /path/to/my-aci-deploy.xlsx
```

Claude reads the spreadsheet, builds the ACI configuration tree, shows a dry-run summary, and deploys on your confirmation.

## Sample output

See [`examples/sample-aci-deployment-package.md`](examples/sample-aci-deployment-package.md) for a complete example showing readiness assessment, HLD excerpt, LLD excerpt, risk analysis, and validation considerations using demo data.

## Included resources

| File | Purpose |
|------|---------|
| `aci-operator.md` | Main skill file — install this to `~/.claude/skills/` |
| `readiness-checklist.md` | Deployment readiness assessment framework |
| `hld-template.md` | High-level design document template |
| `lld-template.md` | Low-level design framework with decision prompts |
| `examples/sample-aci-deployment-package.md` | Sample output with demo data |
| `templates/aci-deploy-template.xlsx` | Spreadsheet deployment template |

## Deployment outcomes

- Faster project planning with structured readiness workflows
- Better documentation through consistent HLD/LLD templates
- Fewer missed prerequisites caught late in the project
- Clearer implementation sequencing with dependency tracking
- Reduced deployment risk through validation and rollback planning
- Cleaner project handoff with as-built documentation

## Requirements

- [Claude Code](https://claude.ai/code)
- `curl` and `python3` (included on macOS/Linux)
- APIC access (optional — only needed for live fabric queries and deployment)

## License

MIT — see [LICENSE](LICENSE).

## About Blue Sodium

[Blue Sodium](https://bluesodium.com) specializes in Cisco ACI, data center networking, and network security. 30+ years of infrastructure experience, from programming to CCIE to AI-assisted network operations.
