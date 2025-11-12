# Urbit Agents: Operations & Development

> **⚡ Optimized for Sonnet 4.5 & Haiku 4.5** — All agents tuned for latest models with hybrid orchestration
>
> **🚀 Urbit-Specific Expertise** — Complete Urbit stack from Hoon development to fleet operations

A comprehensive production-ready system for [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) with **two specialized plugins** for the complete Urbit development and operations lifecycle:

## Plugins

### **urbit-operations** - Deployment & Fleet Management
Production Urbit ship deployment and fleet management with **6 agents**, **10 commands**, and **23 skills** for bare-metal, VPS, GroundSeg, Kubernetes, and managed hosting.

[→ View urbit-operations documentation](#urbit-operations-plugin)

### **hoon-development** - Language & Application Development
Master Hoon programming with **5 agents**, **8 commands**, and **18 skills** covering language fundamentals through production Gall agent development.

[→ View hoon-development documentation](#hoon-development-plugin)

## Overview

This marketplace provides the complete Urbit development and operations lifecycle through two specialized plugins:

### Complete Workflow: Learn → Build → Deploy

**hoon-development** → Master Hoon programming and build Gall agents
**urbit-operations** → Deploy and manage production Urbit infrastructure

### Combined Capabilities

**11 Specialized Agents** - Expert assistance across development and operations:
- **5 Development Agents** - Hoon learning, code review, debugging, architecture, optimization
- **6 Operations Agents** - Deployment, fleet management, hosting advisory, performance engineering

**18 Operational Commands** - Production workflows for the entire lifecycle:
- **8 Development Commands** - Learn, scaffold, review, test, debug, refactor, optimize, migrate
- **10 Operations Commands** - Deploy, harden, monitor, troubleshoot, migrate infrastructure

**41 Knowledge Skills** - Comprehensive expertise from fundamentals to enterprise:
- **18 Development Skills** - Hoon language, types, Gall agents, parsing, data structures
- **23 Operations Skills** - Deployment, security, monitoring, fleet management, compliance

### Key Features

**Development (hoon-development):**
- **Progressive Learning**: 30-day structured path from Hoon basics to advanced patterns
- **Production Best Practices**: Code review with P0-P3 prioritization, security analysis
- **Testing & Debugging**: Comprehensive testing workflows, systematic error diagnosis
- **Gall Agent Development**: Complete lifecycle from scaffolding to state migrations
- **Interactive Tutorials**: Hands-on exercises with progressive difficulty

**Operations (urbit-operations):**
- **Multiple Deployment Models**: Bare-metal, VPS, GroundSeg (Docker), Kubernetes, managed hosting
- **Production-Grade Security**: 20-phase hardening for business-critical infrastructure
- **Fleet Operations**: Manage 100-1,000+ ships with Kubernetes, Terraform, and Helm
- **Enterprise Monitoring**: Prometheus, Grafana, Loki, and AlertManager integration
- **Compliance-Ready**: GDPR, HIPAA, SOC2, and ISO 27001 implementation frameworks
- **Infrastructure-as-Code**: Terraform templates, Helm charts, reproducible deployments
- **Migration Support**: Zero-downtime migrations between providers and deployment models

### How It Works

**Development Workflow (hoon-development):**
1. **Learn Hoon** - Interactive tutorials with `hoon-tutor` agent
2. **Build Applications** - Scaffold projects with `app-architect` agent
3. **Review & Test** - Quality assurance with `code-reviewer` and comprehensive testing
4. **Debug & Optimize** - Systematic debugging and performance tuning
5. **Deploy** - Hand off to urbit-operations for production deployment

**Operations Workflow (urbit-operations):**
1. **Choose Deployment** - Bare-metal, VPS, GroundSeg, or Kubernetes based on scale
2. **Deploy Infrastructure** - Automated setup with deployment specialists
3. **Harden Security** - 20-phase production hardening baseline
4. **Monitor & Scale** - Enterprise observability and fleet management
5. **Optimize Performance** - Systematic profiling and tuning

**Example End-to-End**:
```
/hoon-learn          # Learn Hoon (hoon-development)
  ↓
/hoon-scaffold       # Build a Gall agent (hoon-development)
  ↓
/hoon-review         # Quality assurance (hoon-development)
  ↓
/deploy-vps-planet   # Deploy to production (urbit-operations)
  ↓
/setup-production    # Security hardening (urbit-operations)
```

## Quick Start

### Step 1: Add the Marketplace

Add this marketplace to Claude Code:

```bash
/plugin marketplace add toplyr-narfur/urbit-agents
```

This makes the urbit-operations plugin available for installation, but **does not load any agents or tools** into your context.

### Step 2: Install Plugins

Browse available plugins:

```bash
/plugin
```

Install both plugins for the complete Urbit workflow:

```bash
# For Hoon development and Gall agent building
/plugin install hoon-development

# For deployment and infrastructure management
/plugin install urbit-operations
```

**hoon-development** loads: **5 agents, 8 commands, 18 skills**
**urbit-operations** loads: **6 agents, 10 commands, 23 skills**

Or install just the one you need for your current task.

### Prerequisites

**For Hoon Development (hoon-development):**
- Basic understanding of functional programming concepts
- Urbit ship (planet, moon, or fake ship for testing)
- Text editor with Hoon syntax support (VS Code recommended)
- Curiosity and willingness to learn a unique programming paradigm

**For Urbit Operations (urbit-operations):**
- **For bare-metal**: Ubuntu 22.04/24.04 or Debian 11/12 server with root access
- **For VPS**: Account with DigitalOcean, Linode, Vultr, AWS Lightsail, Hetzner, or OVH
- **For GroundSeg**: Docker and Docker Compose installed, 4GB+ RAM per ship
- **For Kubernetes**: kubectl access to cluster, Helm 3 installed
- **Ship credentials**: Master ticket or ownership proof from Azimuth PKI

## Documentation

### Plugin Guides

**hoon-development:**
- **[Plugin Overview](plugins/hoon-development/README.md)** - Complete development documentation
- **[Development Agents](plugins/hoon-development/agents/)** - All 5 agents with activation criteria
- **[Development Commands](plugins/hoon-development/commands/)** - 8 workflow commands
- **[Hoon Skills Library](plugins/hoon-development/skills/)** - 18 comprehensive skills

**urbit-operations:**
- **[Plugin Overview](plugins/urbit-operations/README.md)** - Complete operations documentation
- **[Operations Agents](plugins/urbit-operations/agents/)** - All 6 agents with activation criteria
- **[Operations Commands](plugins/urbit-operations/commands/)** - 10 operational workflows
- **[Operations Skills Library](plugins/urbit-operations/skills/)** - 23 specialized knowledge packages

### Quick Links

**Development:**
- [Learning Hoon](#hoon-development-plugin) - 30-day curriculum and interactive tutorials
- [Code Review](plugins/hoon-development/commands/hoon-review.md) - P0-P3 prioritized reviews
- [Testing](plugins/hoon-development/commands/hoon-test.md) - Comprehensive testing workflows
- [Debugging](plugins/hoon-development/commands/hoon-debug.md) - Systematic error resolution

**Operations:**
- [Installation](#quick-start) - Get started in 2 steps
- [Deployment Workflows](#popular-use-cases) - Common deployment scenarios
- [Fleet Management](#fleet-operations) - 100-1,000+ ship orchestration
- [Migration Guide](plugins/urbit-operations/commands/migrate-deployment.md) - Cross-provider migration
- [Troubleshooting](plugins/urbit-operations/commands/troubleshoot-ship.md) - Diagnostic workflows

**Integration:**
- [Workflow Integration](#workflow-integration-development--operations) - End-to-end examples

---

## Hoon Development Plugin

Master Hoon programming language for building Urbit applications with comprehensive learning paths, development workflows, and production best practices.

### Features

- **5 Specialized Agents** - Expert guidance from learning through production deployment
- **8 Workflow Commands** - Structured multi-phase development workflows
- **18 Comprehensive Skills** - Progressive knowledge from fundamentals to advanced patterns
- **30-Day Learning Path** - Structured curriculum from beginner to expert
- **P0-P3 Code Reviews** - Production-grade quality assurance
- **TDD Workflows** - Test-driven development with comprehensive coverage

### Agents

#### `hoon-expert` (Sonnet)
Master Hoon developer for complex code implementation, architectural decisions, and performance optimization.

**Use for:** Production application development, complex Hoon patterns, performance tuning

#### `hoon-tutor` (Sonnet)
Educational specialist teaching Hoon through progressive exercises and interactive examples.

**Use for:** Learning Hoon from scratch, understanding complex concepts, structured tutorials

#### `code-reviewer` (Sonnet)
Quality assurance specialist performing comprehensive code reviews with P0-P3 prioritization.

**Use for:** Code quality assessment, security vulnerability detection, best practice enforcement

#### `debugging-specialist` (Sonnet)
Error diagnosis expert for systematic debugging from compilation errors to runtime failures.

**Use for:** Debugging compilation errors, runtime diagnosis, type system issues

#### `app-architect` (Sonnet)
Gall agent architecture specialist for designing production Urbit applications.

**Use for:** Application architecture, Gall agent implementation, state management

### Skills Breakdown

**Foundation (3 skills):**
- **hoon-basics** - Beginner introduction to Hoon syntax and concepts
- **hoon-fundamentals** - Subject-oriented programming, noun model (2300+ lines)
- **rune-reference** - Complete reference for 90+ runes across 13 families

**Language (5 skills):**
- **type-system** - Structural typing, auras, molds, type casting
- **functional-programming-patterns** - Pure functions, higher-order functions, recursion
- **advanced-patterns** - Doors, wet gates, mold builders, metaprogramming
- **irregular-forms** - Irregular syntax shortcuts and best practices
- **hoon-style-guide** - Naming conventions and code organization

**Data (3 skills):**
- **data-structures** - Lists, sets, maps, mops, jars, jugs
- **string-handling** - Cord, tape, term, knot types
- **serialization** - JSON, vases, jam/cue, marks

**Libraries (2 skills):**
- **stdlib-reference** - Comprehensive standard library documentation
- **zuse-libraries** - HTTP, JSON, crypto, scry utilities

**Applications (5 skills):**
- **gall-agents** - Complete Gall agent development with 10 lifecycle arms
- **generators** - Naked, %say, %ask generators for CLI tools
- **parsing** - Parser combinators and custom parsers
- **app-development-workflow** - End-to-end development process
- **sail-markup** - HTML templating in Hoon

### Commands

**`/hoon-review`** - 5-phase code review (quality, security, performance, maintainability)
**`/hoon-optimize`** - 4-phase performance optimization with benchmarking
**`/hoon-debug`** - 5-phase systematic debugging workflow
**`/hoon-refactor`** - 6-phase safe refactoring while preserving correctness
**`/hoon-test`** - 5-phase testing (unit, integration, property-based, TDD)
**`/hoon-learn`** - 6-level interactive learning path (30-day curriculum)
**`/hoon-migrate`** - 5-phase safe state migration for Gall agents
**`/hoon-scaffold`** - 6-phase project bootstrapping with best practices

### Use Cases

**Learning Hoon**: 30-day structured curriculum
```bash
/hoon-learn
"Explain how subject-oriented programming works"
"Practice writing recursive functions"
```

**Building Applications**: Complete Gall agent lifecycle
```bash
/hoon-scaffold  # Bootstrap project with desk structure
"Build a counter app with state persistence"
/hoon-test      # Comprehensive unit and integration testing
/hoon-review    # P0-P3 prioritized quality review
```

**Code Quality**: Production-grade assurance
```bash
/hoon-review    # Security, performance, maintainability analysis
/hoon-optimize  # Systematic performance profiling and tuning
/hoon-refactor  # Safe restructuring with test preservation
```

**State Migrations**: Zero-downtime upgrades
```bash
/hoon-migrate   # Guided migration with validation and safety checks
```

**Debugging**: Systematic error resolution
```bash
/hoon-debug     # Classification, reproduction, instrumentation, resolution
```

[→ View complete hoon-development documentation](plugins/hoon-development/README.md)

---

## Urbit Operations Plugin

Production-grade Urbit ship deployment and fleet management across all hosting models.

## What's New

### Agent Skills (23 skills across 7 categories)

Specialized knowledge packages following Anthropic's progressive disclosure architecture:

**Core Fundamentals (3 skills):**
- **urbit-fundamentals** - Nock, Hoon, Arvo, vanes, Azimuth, Ames networking
- **ship-deployment-guide** - Step-by-step deployment across all platforms
- **urbit-troubleshooting** - Systematic diagnostics for boot failures, network issues, crashes

**Deployment & Infrastructure (5 skills):**
- **vps-deployment-providers** - Platform-specific guides for DigitalOcean, Linode, Vultr, AWS, Hetzner, OVH
- **groundseg-installation** - Docker setup, Anchor networking, MinIO S3 integration
- **groundseg-troubleshooting** - Container issues, DNS configuration, SSL certificates
- **kubernetes-urbit** - StatefulSets, persistent volumes, GitOps with ArgoCD/Flux
- **multi-ship-orchestration** - Resource allocation, network isolation, batch operations

**Networking & Security (4 skills):**
- **anchor-networking** - Reverse proxy, Let's Encrypt SSL, custom domains
- **network-security-advanced** - Zero-trust networking, VPNs, network policies
- **container-security** - User namespaces, capabilities, AppArmor, Seccomp
- **advanced-security-patterns** - Defense-in-depth, intrusion detection, security automation

**Storage & Backup (3 skills):**
- **minio-s3-integration** - Self-hosted S3 for ship storage and backups
- **backup-disaster-recovery** - Automated backups, restoration procedures, testing
- **disaster-recovery-advanced** - Multi-region failover, cross-region replication, RTO/RPO planning

**Monitoring & Operations (4 skills):**
- **monitoring-observability** - Prometheus, Grafana, Loki, AlertManager integration
- **performance-optimization** - Disk I/O tuning, memory optimization, network throughput
- **performance-profiling** - Bottleneck identification, capacity planning, growth projections
- **sla-management** - Uptime targets, incident response, error budgets

**Management (3 skills):**
- **fleet-operations** - Terraform IaC, Helm templating, staged OTA updates, cost forecasting
- **managed-hosting-comparison** - Tlon vs Red Horizon vs UrbitHost vs self-hosting
- **app-development-workflow** - Local development, desk publishing, distribution

**Compliance (1 skill):**
- **compliance-frameworks** - GDPR, HIPAA, SOC2, ISO 27001 implementation

[→ View complete skills documentation](plugins/urbit-operations/skills/)

### Hybrid Model Orchestration

Strategic model assignment for optimal performance and cost:
- **2 Haiku agents** - Fast execution for deterministic deployment tasks
- **4 Sonnet agents** - Complex reasoning for fleet architecture and performance tuning

Orchestration patterns combine models for efficiency:
```
Sonnet (planning) → Haiku (execution) → Sonnet (validation)
```

## Popular Use Cases

### Single Planet Deployment (Bare-Metal)

```bash
/urbit-operations:deploy-planet
```

10-phase automated deployment: system validation → Urbit installation → network configuration → systemd service → SSL/TLS → production hardening → monitoring setup → backup automation → verification

Activates: `urbit-deployment-specialist` agent + `ship-deployment-guide` skill

### VPS Planet Deployment

```bash
/urbit-operations:deploy-vps-planet
```

Cloud-optimized deployment with provider-specific configurations for DigitalOcean, Linode, Vultr, AWS Lightsail, Hetzner, or OVH. Includes block storage setup, snapshot automation, and DNS configuration.

Activates: `vps-deployment-specialist` agent + `vps-deployment-providers` skill

### GroundSeg Multi-Ship Setup

```bash
/urbit-operations:deploy-groundseg
```

Docker-based orchestration for 5-20 ships: container setup → Anchor reverse proxy → Let's Encrypt SSL → MinIO S3 storage → resource allocation → security hardening → monitoring

Activates: `groundseg-operator` agent + `groundseg-installation` + `anchor-networking` skills

### Enterprise Kubernetes Fleet

```bash
/urbit-operations:deploy-fleet
```

Large-scale deployment for 100-1,000+ ships: platform selection → Terraform infrastructure → Helm chart templating → StatefulSet configuration → monitoring stack → staged OTA updates → multi-region setup → cost optimization

Activates: `fleet-manager` agent + `kubernetes-urbit` + `fleet-operations` + `multi-ship-orchestration` skills

### Production Hardening

```bash
/urbit-operations:setup-production
```

20-phase security baseline: SSH hardening → firewall configuration → fail2ban → automatic updates → log aggregation → intrusion detection → resource limits → kernel tuning → compliance frameworks

Activates: `urbit-deployment-specialist` + `advanced-security-patterns` + `compliance-frameworks` skills

### Migration Between Providers

```bash
/urbit-operations:migrate-deployment
```

Zero-downtime migration workflows: Tlon → VPS, VPS → bare-metal, bare-metal → Kubernetes, managed → self-hosted. Includes backup verification, DNS cutover planning, and rollback procedures.

Activates: `managed-hosting-advisor` + `backup-disaster-recovery` skills

### Performance Optimization

```bash
/urbit-operations:optimize-performance
```

Systematic troubleshooting: bottleneck profiling → disk I/O tuning → memory optimization → network throughput → resource rightsizing → capacity planning

Activates: `performance-engineer` agent + `performance-profiling` + `performance-optimization` skills

[→ View all deployment workflows](plugins/urbit-operations/commands/)

## Fleet Operations

For operators managing multiple ships, the plugin provides enterprise-grade fleet management:

### Infrastructure-as-Code

```bash
# Generate Terraform configuration for 100-ship fleet
"Create Terraform configuration for 100 Urbit ships on Kubernetes with monitoring"
```

Produces production-ready IaC with:
- StatefulSet definitions with persistent volumes
- Horizontal pod autoscaling
- Network policies for isolation
- Prometheus ServiceMonitors
- GitOps ArgoCD/Flux configuration

### Centralized Monitoring

```bash
/urbit-operations:setup-monitoring
```

Deploys observability stack:
- **Prometheus** - Ship metrics, resource utilization, Ames network stats
- **Grafana** - Pre-built dashboards for fleet overview, ship health, performance
- **Loki** - Centralized log aggregation from all ships
- **AlertManager** - SLA-based alerting with PagerDuty/Slack integration

### Staged OTA Updates

```bash
"Perform staged OTA update for 500-ship fleet with 10% canary rollout"
```

Orchestrates safe updates:
1. Canary deployment (10% of fleet)
2. Health validation and rollback criteria
3. Gradual rollout (25% → 50% → 100%)
4. Automated rollback on failure
5. Compliance audit trail

[→ View fleet operations guide](plugins/urbit-operations/skills/fleet-operations/)

## Architecture Highlights

### Deployment Model Selection

Progressive complexity based on scale and requirements:

1. **Tlon Managed Hosting** - Zero-ops, managed service (ideal for new users)
2. **VPS Deployment** - Single ship, full control, moderate complexity
3. **Bare-Metal** - Maximum performance, security, privacy for critical ships
4. **GroundSeg (Docker)** - 5-20 ships with container orchestration
5. **Kubernetes** - 100+ ships, multi-region, enterprise-grade
6. **Hybrid** - Strategic mix (critical ships on bare-metal, others on Kubernetes)

[→ View deployment model comparison](plugins/urbit-operations/skills/managed-hosting-comparison/)

### Security Layers (Defense-in-Depth)

1. **Network**: UFW firewall, fail2ban, rate limiting, DDoS protection
2. **System**: SSH hardening, automatic security updates, kernel tuning
3. **Container**: User namespaces, capabilities drop, AppArmor/Seccomp profiles
4. **Kubernetes**: Network policies, RBAC, Pod Security Standards, secrets management
5. **Application**: Urbit access control, +code management, secure desk distribution
6. **Compliance**: GDPR, HIPAA, SOC2, ISO 27001 frameworks

### Production Hardening Checklist

20-phase security baseline ensures business-ready deployments:

- ✅ SSH key-only authentication with fail2ban
- ✅ UFW firewall (allow 80/443, Ames port only)
- ✅ Automatic unattended-upgrades for security patches
- ✅ SystemD service with automatic restart on failure
- ✅ SSL/TLS with Let's Encrypt auto-renewal
- ✅ Resource limits (CPU, memory, file descriptors)
- ✅ Log rotation and centralized aggregation
- ✅ Monitoring with Prometheus node exporter
- ✅ Automated daily backups with retention policy
- ✅ Disaster recovery plan with tested restoration

[→ View complete hardening guide](plugins/urbit-operations/commands/setup-production.md)

### Progressive Disclosure (Skills)

Three-tier architecture for token efficiency:
1. **Metadata** - Name and activation criteria (always loaded)
2. **Instructions** - Core deployment guidance (loaded when activated)
3. **Resources** - Templates, scripts, examples (loaded on demand)

## Workflow Integration: Development + Operations

The two plugins work together seamlessly for end-to-end Urbit application development and deployment.

### Example: Building and Deploying a Custom Gall Agent

**Phase 1: Learning (hoon-development)**
```bash
/plugin install hoon-development
/hoon-learn                    # Complete 30-day curriculum
```

**Phase 2: Development (hoon-development)**
```bash
/hoon-scaffold                 # Bootstrap project structure
# Implement Gall agent code
/hoon-test                     # Unit and integration testing
/hoon-review                   # P0-P3 quality assurance
/hoon-optimize                 # Performance tuning
```

**Phase 3: Deployment (urbit-operations)**
```bash
/plugin install urbit-operations
/deploy-vps-planet             # Deploy ship to production VPS
/setup-production              # 20-phase security hardening
/setup-monitoring              # Prometheus + Grafana observability
```

**Phase 4: Operations (urbit-operations)**
```bash
/optimize-performance          # System-level tuning
/setup-cicd-pipeline           # Automated OTA deployments
/troubleshoot-ship             # Diagnostic workflows as needed
```

### Cross-Plugin Workflows

**Development → Deployment**:
- Build app locally with fake ships (`/hoon-scaffold` + testing)
- Deploy to production infrastructure (`/deploy-planet`)
- Monitor performance and optimize (`/optimize-performance`)

**Operations → Development**:
- Identify performance bottleneck in production (`/optimize-performance`)
- Debug and optimize Hoon code (`/hoon-debug` + `/hoon-optimize`)
- Test improvements locally, redeploy via CI/CD

**Continuous Improvement**:
```
Code (hoon-development)
  → Review (/hoon-review)
  → Test (/hoon-test)
  → Deploy (urbit-operations: /deploy-*)
  → Monitor (urbit-operations: /setup-monitoring)
  → Profile (urbit-operations: /optimize-performance)
  → Optimize Code (hoon-development: /hoon-optimize)
  → Repeat
```

### Repository Structure

```
urbit-agents/
├── .claude-plugin/
│   └── marketplace.json                    # Marketplace definition (both plugins)
├── plugins/
│   ├── hoon-development/                   # Hoon programming plugin
│   │   ├── agents/                         # 5 development agents
│   │   │   ├── hoon-expert.md
│   │   │   ├── hoon-tutor.md
│   │   │   ├── code-reviewer.md
│   │   │   ├── debugging-specialist.md
│   │   │   └── app-architect.md
│   │   ├── commands/                       # 8 development workflows
│   │   │   ├── hoon-review.md
│   │   │   ├── hoon-optimize.md
│   │   │   ├── hoon-debug.md
│   │   │   ├── hoon-refactor.md
│   │   │   ├── hoon-test.md
│   │   │   ├── hoon-learn.md
│   │   │   ├── hoon-migrate.md
│   │   │   └── hoon-scaffold.md
│   │   ├── skills/                         # 18 Hoon skills
│   │   │   ├── hoon-basics/
│   │   │   ├── hoon-fundamentals/
│   │   │   ├── rune-reference/
│   │   │   ├── type-system/
│   │   │   ├── gall-agents/
│   │   │   ├── parsing/
│   │   │   └── ... (12 more skills)
│   │   └── README.md
│   └── urbit-operations/                   # Infrastructure operations plugin
│       ├── agents/                         # 6 operations agents
│       │   ├── urbit-deployment-specialist.md
│       │   ├── vps-deployment-specialist.md
│       │   ├── groundseg-operator.md
│       │   ├── fleet-manager.md
│       │   ├── managed-hosting-advisor.md
│       │   └── performance-engineer.md
│       ├── commands/                       # 10 operational workflows
│       │   ├── deploy-planet.md
│       │   ├── deploy-vps-planet.md
│       │   ├── deploy-groundseg.md
│       │   ├── deploy-fleet.md
│       │   ├── setup-production.md
│       │   ├── setup-monitoring.md
│       │   ├── setup-cicd-pipeline.md
│       │   ├── troubleshoot-ship.md
│       │   ├── migrate-deployment.md
│       │   └── optimize-performance.md
│       ├── skills/                         # 23 operations skills
│       │   ├── urbit-fundamentals/
│       │   ├── ship-deployment-guide/
│       │   ├── urbit-troubleshooting/
│       │   ├── vps-deployment-providers/
│       │   ├── groundseg-installation/
│       │   ├── kubernetes-urbit/
│       │   ├── fleet-operations/
│       │   ├── monitoring-observability/
│       │   ├── performance-optimization/
│       │   └── ... (14 more skills)
│       └── README.md
├── CLAUDE.md                               # Project instructions for Claude
├── docs/                                   # Documentation
└── README.md                               # This file
```

## Contributing

This repository focuses exclusively on Urbit development and operations. To contribute:

**For hoon-development plugin:**
1. Create skills, agents, or commands in `plugins/hoon-development/`
2. Focus on Hoon programming, Gall agents, and application development
3. Include comprehensive code examples and exercises
4. Ensure educational value for learners and practical value for developers

**For urbit-operations plugin:**
1. Create skills, agents, or commands in `plugins/urbit-operations/`
2. Focus on deployment, infrastructure, and operational workflows
3. Include production-ready examples and templates
4. Test thoroughly across deployment models

**General guidelines:**
1. Follow naming conventions (lowercase, hyphen-separated)
2. Write clear activation criteria for Urbit-specific scenarios
3. Update plugin definition in `.claude-plugin/marketplace.json`
4. Add comprehensive documentation
5. Maintain cross-plugin references where workflows intersect

For upstream Claude Code workflows plugin contributions, see [wshobson/agents](https://github.com/wshobson/agents).

## Resources

### Urbit Documentation
- [Urbit.org](https://urbit.org) - Official Urbit documentation
- [Operators Manual](https://operators.urbit.org) - Ship operation guide
- [Developer Documentation](https://developers.urbit.org) - Hoon and app development
- [GroundSeg Manual](https://manual.groundseg.app) - GroundSeg deployment guide
- [Urbit Guide](https://urbitguide.com) - Community tutorials

### Claude Code Documentation
- [Claude Code Overview](https://docs.claude.com/en/docs/claude-code/overview)
- [Plugins Guide](https://docs.claude.com/en/docs/claude-code/plugins)
- [Subagents Guide](https://docs.claude.com/en/docs/claude-code/sub-agents)
- [Agent Skills Guide](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview)
- [Slash Commands Reference](https://docs.claude.com/en/docs/claude-code/slash-commands)

### This Repository

**hoon-development plugin:**
- [Plugin Overview](plugins/hoon-development/README.md)
- [Development Agents](plugins/hoon-development/agents/)
- [Development Commands](plugins/hoon-development/commands/)
- [Hoon Skills Library](plugins/hoon-development/skills/)

**urbit-operations plugin:**
- [Plugin Overview](plugins/urbit-operations/README.md)
- [Operations Agents](plugins/urbit-operations/agents/)
- [Operations Commands](plugins/urbit-operations/commands/)
- [Operations Skills Library](plugins/urbit-operations/skills/)

## License

MIT License - see [LICENSE](LICENSE) file for details.

---

**Forked from**: [wshobson/agents](https://github.com/wshobson/agents) - Comprehensive Claude Code workflow plugins
**Maintained by**: [toplyr-narfur](https://github.com/toplyr-narfur)
