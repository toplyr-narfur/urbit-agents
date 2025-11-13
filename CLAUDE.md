# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is the **urbit-operations** marketplace - a specialized fork of claude-code-workflows focused exclusively on Urbit ship deployment and fleet management. It provides production-ready infrastructure for deploying and managing Urbit ships at any scale through Claude Code's agent system.

### Key Architecture

- **Single Plugin Focus**: Unlike the upstream marketplace with 64+ plugins, this repository contains only the `urbit-operations` plugin
- **23 Specialized Skills**: Modular knowledge packages covering Urbit fundamentals through enterprise fleet operations
- **6 Domain Agents**: Specialists for bare-metal deployment, VPS platforms, GroundSeg, Kubernetes fleets, hosting advisory, and performance engineering
- **10 Operational Commands**: Production workflows for deployment, hardening, monitoring, troubleshooting, and migration
- **Progressive Disclosure**: Three-tier architecture (metadata → instructions → resources) for token efficiency

## Repository Structure

```
urbit-agents/
├── .claude-plugin/
│   └── marketplace.json          # Plugin marketplace definition
├── plugins/
│   └── urbit-operations/         # The single Urbit-focused plugin
│       ├── agents/               # 6 specialized agents (*.md files)
│       ├── commands/             # 10 operational workflows (*.md files)
│       └── skills/               # 23 knowledge packages (directories with SKILL.md)
├── docs/                         # Architecture and usage documentation
└── README.md                     # User-facing documentation
```

## Agent Architecture

### Agent Files Structure

All agents are Markdown files in `plugins/urbit-operations/agents/` with YAML frontmatter:

```yaml
---
name: agent-name
description: What the agent does
model: sonnet|haiku           # Sonnet for complex reasoning, Haiku for deterministic tasks
tools: []                      # Optional tool restrictions
skills:                        # Skills this agent can activate
  - skill-name-1
  - skill-name-2
---
# Agent content (system prompt)
```

### The 6 Agents

1. **urbit-deployment-specialist** (Sonnet) - Bare-metal Ubuntu/Debian deployments
2. **vps-deployment-specialist** (Haiku) - VPS cloud deployments (DigitalOcean, Linode, etc.)
3. **groundseg-operator** (Haiku) - Docker-based multi-ship management with GroundSeg
4. **fleet-manager** (Sonnet) - Enterprise Kubernetes orchestration for 100+ ships
5. **managed-hosting-advisor** (Sonnet) - Decision frameworks for hosting options
6. **performance-engineer** (Sonnet) - Bottleneck identification and optimization

## Skill Architecture

### Skill Directory Structure

Each skill is a directory in `plugins/urbit-operations/skills/` containing a `SKILL.md` file:

```
skills/
└── skill-name/
    └── SKILL.md              # Skill content with YAML frontmatter
```

### SKILL.md Format

```yaml
---
name: skill-name              # Hyphen-case, matches directory name
description: What the skill does. Use when [activation criteria]. # <1024 chars
---
# Skill content with progressive disclosure
```

### The 23 Skills (Organized by Category)

**Core Fundamentals (3):**
- urbit-fundamentals - Nock, Hoon, Arvo, vanes, Azimuth, Ames
- ship-deployment-guide - Step-by-step deployment procedures
- urbit-troubleshooting - Systematic diagnostics

**Deployment & Infrastructure (5):**
- vps-deployment-providers - Platform-specific guides
- groundseg-installation - Docker setup
- groundseg-troubleshooting - Container diagnostics
- kubernetes-urbit - StatefulSets, GitOps
- multi-ship-orchestration - Resource allocation

**Networking & Security (4):**
- anchor-networking - Reverse proxy, SSL
- network-security-advanced - Zero-trust networking
- container-security - Docker hardening
- advanced-security-patterns - Defense-in-depth

**Storage & Backup (3):**
- minio-s3-integration - Self-hosted S3
- backup-disaster-recovery - Automated backups
- disaster-recovery-advanced - Multi-region failover

**Monitoring & Operations (4):**
- monitoring-observability - Prometheus/Grafana integration
- performance-optimization - Disk I/O, memory tuning
- performance-profiling - Bottleneck identification
- sla-management - Uptime targets, incident response

**Management (3):**
- fleet-operations - Terraform IaC, Helm templating
- managed-hosting-comparison - Tlon vs self-hosting
- app-development-workflow - Local development

**Compliance (1):**
- compliance-frameworks - GDPR, HIPAA, SOC2

## Commands Architecture

Commands are workflow orchestrators in `plugins/urbit-operations/commands/` with YAML frontmatter:

```yaml
---
name: command-name
description: What the command does
subagent_type: agent-name     # Agent that executes this workflow
---
# Command content (workflow steps)
```

### The 10 Commands

1. **deploy-planet** - Bare-metal planet deployment (10 phases)
2. **deploy-vps-planet** - VPS-optimized deployment
3. **deploy-groundseg** - Multi-ship Docker orchestration
4. **deploy-fleet** - Kubernetes fleet deployment
5. **setup-production** - 20-phase security hardening
6. **setup-monitoring** - Observability stack deployment
7. **setup-cicd-pipeline** - CI/CD for ship updates
8. **troubleshoot-ship** - Systematic diagnostics
9. **migrate-deployment** - Zero-downtime migration
10. **optimize-performance** - Performance tuning

## Development Guidelines

### Adding or Modifying Agents

1. Agent files are in `plugins/urbit-operations/agents/`
2. Use hyphen-case for filenames: `agent-name.md`
3. Include complete YAML frontmatter
4. Choose `model: sonnet` for complex reasoning, `model: haiku` for deterministic tasks
5. Reference relevant skills in the `skills:` array
6. Update `.claude-plugin/marketplace.json` if adding new agents

### Adding or Modifying Skills

1. Create directory in `plugins/urbit-operations/skills/skill-name/`
2. Add `SKILL.md` file with YAML frontmatter
3. Follow progressive disclosure: metadata → core instructions → detailed resources
4. Include "Use when" activation criteria in description
5. Update `.claude-plugin/marketplace.json` skills array
6. Test activation criteria work as expected

### Adding or Modifying Commands

1. Command files are in `plugins/urbit-operations/commands/`
2. Use hyphen-case for filenames: `command-name.md`
3. Include YAML frontmatter with `subagent_type` pointing to the executing agent
4. Structure as multi-phase workflows with clear success criteria
5. Update `.claude-plugin/marketplace.json` if adding new commands

### File Naming Conventions

- **Agents**: `agent-name.md` (hyphen-case)
- **Commands**: `command-name.md` (hyphen-case)
- **Skills**: Directory `skill-name/` with `SKILL.md` inside
- **Always use hyphen-case**, never camelCase or snake_case

### Markdown Frontmatter Standards

All agent, command, and skill files MUST have valid YAML frontmatter:
- Start with `---` on the first line
- End with `---` after the frontmatter
- Include all required fields for the file type
- Use proper YAML syntax (lists with `-`, proper indentation)

## Urbit-Specific Context

### Critical Urbit Concepts

1. **Keyfile Security**:
   - Keyfiles are consumed on first boot and become useless
   - NEVER backup keyfiles after first boot
   - NEVER reuse keyfiles (causes network ban)
   - Workflow: Upload → Boot → Verify → DELETE

2. **Pier Management**:
   - A "pier" is an Urbit ship's directory containing all state
   - NEVER backup a live pier (corrupts event log)
   - Safe backup: Stop ship → Wait 30s → Verify stopped → Backup → Restart

3. **Network Architecture**:
   - Port 80/443 TCP: HTTP/HTTPS
   - Port 34543 UDP: Ames (peer-to-peer networking)
   - Ames requires NAT traversal or port forwarding

4. **System Components**:
   - Nock: Functional assembly language
   - Hoon: High-level functional language
   - Arvo: OS kernel
   - Vanes: 10 kernel modules (Gall, Ames, Clay, Eyre, etc.)
   - Loom: Memory model with 2GB-4GB limit

### Deployment Models (Progressive Complexity)

1. **Tlon Managed Hosting**: Zero-ops managed service
2. **VPS Deployment**: Single ship, full control
3. **Bare-Metal**: Maximum performance and security
4. **GroundSeg (Docker)**: 5-20 ships with container orchestration
5. **Kubernetes**: 100+ ships, multi-region, enterprise-grade
6. **Hybrid**: Strategic mix of deployment models

### Common Urbit Commands

Operators use these commands in the Dojo (Urbit CLI):
- `+code` - Get web login code
- `|pack` - Compress and garbage collect pier
- `|ota` - Trigger OTA update
- `|public` - Expose ship to public internet
- `.^` - Read from kernel state
- `|rein` - Enable/disable agents

## Testing and Validation

### Testing New Agents/Skills/Commands

1. Test activation criteria work correctly
2. Verify frontmatter parses without errors
3. Ensure referenced skills exist and are correctly named
4. Check that agent model selection (Sonnet vs Haiku) is appropriate
5. Validate against actual Urbit deployment scenarios when possible

### marketplace.json Validation

After modifying the plugin structure:
1. Verify JSON syntax is valid
2. Ensure all file paths in arrays exist
3. Check that agent/command/skill names match filenames (excluding `.md`)
4. Confirm version numbers are updated appropriately

## Common Pitfalls to Avoid

1. **Don't create generic development agents** - This is Urbit-specific only
2. **Don't add non-Urbit plugins** - Keep this repository focused on urbit-operations
3. **Don't use CamelCase or snake_case** - Always use hyphen-case for names
4. **Don't reference skills that don't exist** - Verify skill names in agents
5. **Don't forget frontmatter** - All .md files need YAML frontmatter
6. **Don't mix upstream workflows** - This is a fork, not the main marketplace

## Contributing

This repository is maintained independently and focuses exclusively on Urbit development and operations. 

Contributions should focus on Urbit-specific tools and workflows. Follow the naming conventions and architecture patterns established in this repository.

For support or questions, reach out to **~toplyr-narfur** on Tlon.

## Important Resources

### Urbit Documentation
- [Urbit.org](https://urbit.org) - Official documentation
- [Operators Manual](https://operators.urbit.org) - Ship operation guide
- [GroundSeg Manual](https://manual.groundseg.app) - GroundSeg deployment
- [Developer Docs](https://developers.urbit.org) - Hoon and app development

### Claude Code Documentation
- [Plugins Guide](https://docs.claude.com/en/docs/claude-code/plugins)
- [Subagents Guide](https://docs.claude.com/en/docs/claude-code/sub-agents)
- [Agent Skills Specification](https://github.com/anthropics/skills/blob/main/agent_skills_spec.md)
