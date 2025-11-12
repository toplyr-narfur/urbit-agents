# Urbit Operations: Production Deployment & Fleet Management

> **⚡ Optimized for Sonnet 4.5 & Haiku 4.5** — All agents tuned for latest models with hybrid orchestration
>
> **🚀 Urbit-Specific Expertise** — 23 specialized skills for production-grade Urbit infrastructure

A comprehensive production-ready system combining **6 specialized agents**, **10 operational commands**, and **23 deployment skills** organized into a single focused plugin for [Claude Code](https://docs.claude.com/en/docs/claude-code/overview), purpose-built for Urbit ship deployment and fleet management.

## Overview

This plugin provides everything needed for deploying, managing, and operating Urbit ships at any scale:

- **6 Specialized Agents** - Domain experts covering bare-metal deployment, VPS platforms, GroundSeg orchestration, fleet management, hosting advisory, and performance engineering
- **10 Operational Commands** - Production-ready workflows for deployment, hardening, monitoring, troubleshooting, and migration
- **23 Deployment Skills** - Modular knowledge packages covering fundamentals through enterprise fleet operations
- **Multiple Deployment Models** - Bare-metal, VPS (DigitalOcean, Linode, Vultr, AWS, Hetzner, OVH), GroundSeg containers, Kubernetes, and managed hosting
- **Scalability** - From single planets to 1,000+ ship fleets with multi-region support

### Key Features

- **Production-Grade Deployments**: 20-phase security hardening for business-critical Urbit infrastructure
- **Fleet Operations**: Comprehensive tooling for managing 100-1,000+ ships with Kubernetes, Terraform, and Helm
- **Multiple Hosting Models**: Expert guidance across bare-metal, VPS, GroundSeg, managed hosting, and hybrid deployments
- **Compliance-Ready**: GDPR, HIPAA, SOC2, and ISO 27001 implementation frameworks
- **Infrastructure-as-Code**: Terraform templates, Helm charts, and Ansible playbooks for reproducible deployments
- **Enterprise Monitoring**: Prometheus, Grafana, Loki, and AlertManager integration for fleet observability
- **Migration Support**: Zero-downtime migration strategies between hosting providers and deployment models
- **Performance Optimization**: Systematic profiling and tuning for disk I/O, memory, CPU, and network bottlenecks

### How It Works

The urbit-operations plugin provides specialized agents for each deployment scenario:

- **Bare-Metal Deployments** - Ubuntu/Debian server setup with systemd, SSL/TLS, and production hardening
- **VPS Cloud Deployments** - Platform-specific optimization for major cloud providers
- **GroundSeg Orchestration** - Docker-based multi-ship management with Anchor networking and MinIO S3
- **Kubernetes Fleets** - Enterprise-grade orchestration for 100+ ships with GitOps and progressive rollouts
- **Managed Hosting Advisory** - Decision frameworks for Tlon, Red Horizon, UrbitHost vs self-hosting
- **Performance Engineering** - Bottleneck identification, capacity planning, and SLA management

**Example**: Deploying a production planet activates the deployment specialist, ship-deployment-guide skill, and production hardening workflow (~500 tokens), providing complete setup automation.

## Quick Start

### Step 1: Add the Marketplace

Add this marketplace to Claude Code:

```bash
/plugin marketplace add toplyr-narfur/urbit-agents
```

This makes the urbit-operations plugin available for installation, but **does not load any agents or tools** into your context.

### Step 2: Install the Plugin

Browse available plugins:

```bash
/plugin
```

Install the urbit-operations plugin:

```bash
/plugin install urbit-operations
```

The plugin loads **6 specialized agents, 10 commands, and 23 skills** into Claude's context.

### Prerequisites

Before deploying Urbit ships, ensure you have:

- **For bare-metal**: Ubuntu 22.04/24.04 or Debian 11/12 server with root access
- **For VPS**: Account with DigitalOcean, Linode, Vultr, AWS Lightsail, Hetzner, or OVH
- **For GroundSeg**: Docker and Docker Compose installed, 4GB+ RAM per ship
- **For Kubernetes**: kubectl access to cluster, Helm 3 installed
- **Ship credentials**: Master ticket or ownership proof from Azimuth PKI

## Documentation

### Core Guides

- **[Plugin Overview](plugins/urbit-operations/README.md)** - Complete plugin documentation
- **[Agent Reference](plugins/urbit-operations/agents/)** - All 6 agents with activation criteria
- **[Command Reference](plugins/urbit-operations/commands/)** - 10 operational workflows
- **[Skills Library](plugins/urbit-operations/skills/)** - 23 specialized knowledge packages

### Quick Links

- [Installation](#quick-start) - Get started in 2 steps
- [Deployment Workflows](#popular-use-cases) - Common deployment scenarios
- [Fleet Management](#fleet-operations) - 100-1,000+ ship orchestration
- [Migration Guide](plugins/urbit-operations/commands/migrate-deployment.md) - Cross-provider migration
- [Troubleshooting](plugins/urbit-operations/commands/troubleshoot-ship.md) - Diagnostic workflows

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

### Repository Structure

```
urbit-agents/
├── .claude-plugin/
│   └── marketplace.json          # Plugin definition
├── plugins/
│   └── urbit-operations/
│       ├── agents/                # 6 specialized agents
│       │   ├── urbit-deployment-specialist.md
│       │   ├── vps-deployment-specialist.md
│       │   ├── groundseg-operator.md
│       │   ├── fleet-manager.md
│       │   ├── managed-hosting-advisor.md
│       │   └── performance-engineer.md
│       ├── commands/              # 10 operational workflows
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
│       └── skills/                # 23 specialized skills
│           ├── urbit-fundamentals/
│           ├── ship-deployment-guide/
│           ├── urbit-troubleshooting/
│           ├── vps-deployment-providers/
│           ├── groundseg-installation/
│           ├── kubernetes-urbit/
│           ├── fleet-operations/
│           ├── monitoring-observability/
│           ├── performance-optimization/
│           └── ... (14 more skills)
├── docs/                          # Documentation
└── README.md                      # This file
```

## Contributing

This fork focuses exclusively on Urbit operations. To contribute:

1. Create skills, agents, or commands in `plugins/urbit-operations/`
2. Follow naming conventions (lowercase, hyphen-separated)
3. Write clear activation criteria for Urbit-specific scenarios
4. Include production-ready examples and templates
5. Test thoroughly across deployment models
6. Update plugin definition in `.claude-plugin/marketplace.json`

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
- [Plugin Overview](plugins/urbit-operations/README.md)
- [Agent Reference](plugins/urbit-operations/agents/)
- [Command Reference](plugins/urbit-operations/commands/)
- [Skills Library](plugins/urbit-operations/skills/)

## License

MIT License - see [LICENSE](LICENSE) file for details.

---

**Forked from**: [wshobson/agents](https://github.com/wshobson/agents) - Comprehensive Claude Code workflow plugins
**Maintained by**: [toplyr-narfur](https://github.com/toplyr-narfur)
