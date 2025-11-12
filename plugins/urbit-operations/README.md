# Urbit Operations Plugin

Production-ready Urbit ship deployment and fleet management through specialized agents, comprehensive skills, and workflow commands.

## Overview

The **urbit-operations** plugin provides complete Urbit infrastructure expertise through Claude Code's agent system, covering everything from single-ship bare-metal deployments through enterprise Kubernetes fleet orchestration.

### Key Features

- **6 Specialized Agents**: Domain experts for different deployment scenarios
- **10 Workflow Commands**: Production-ready orchestration for common operational tasks
- **23 Comprehensive Skills**: Modular knowledge packages from basics to enterprise patterns
- **Progressive Deployment**: Structured path from single ship to 100+ ship fleets
- **Production Focus**: Real-world patterns and best practices

## Agents

### Deployment Specialists

#### `urbit-deployment-specialist` (Sonnet)
Bare-metal planet deployment expert specializing in Ubuntu/Debian systems with production hardening.

**Use for:**
- Bare-metal server deployments
- Production system preparation
- Security hardening
- Systemd integration

**Skills**: urbit-fundamentals, ship-deployment-guide, urbit-troubleshooting, backup-disaster-recovery

---

#### `vps-deployment-specialist` (Sonnet)
Cloud VPS deployment expert optimized for DigitalOcean, Linode, Hetzner, Vultr, and AWS Lightsail.

**Use for:**
- VPS cloud deployments
- Provider selection
- Cost optimization
- Cloud-specific configurations

**Skills**: vps-deployment-providers, urbit-fundamentals, ship-deployment-guide, anchor-networking, network-security-advanced

---

#### `groundseg-operator` (Sonnet)
Docker-based multi-ship orchestration specialist for GroundSeg platform (5-20 ships).

**Use for:**
- GroundSeg installation
- Multi-ship container orchestration
- Docker troubleshooting
- MinIO S3 integration

**Skills**: groundseg-installation, groundseg-troubleshooting, container-security, multi-ship-orchestration, minio-s3-integration, urbit-fundamentals, ship-deployment-guide, urbit-troubleshooting

---

#### `fleet-manager` (Sonnet)
Enterprise Kubernetes fleet orchestration specialist for mission-critical deployments (25-1000+ ships).

**Use for:**
- Kubernetes StatefulSet deployments
- Enterprise fleet architecture
- Terraform Infrastructure-as-Code
- GitOps workflows

**Skills**: kubernetes-urbit, fleet-operations, multi-ship-orchestration, monitoring-observability, advanced-security-patterns, compliance-frameworks, disaster-recovery-advanced, backup-disaster-recovery

---

### Advisory & Optimization Specialists

#### `managed-hosting-advisor` (Sonnet)
Strategic advisor for hosting decisions, migrations, and build-vs-buy analysis.

**Use for:**
- Hosting provider comparison
- Migration planning
- Cost-benefit analysis
- Strategic recommendations

**Skills**: managed-hosting-comparison, vps-deployment-providers, urbit-fundamentals, fleet-operations

---

#### `performance-engineer` (Sonnet)
Performance optimization specialist for system tuning and bottleneck elimination.

**Use for:**
- Performance diagnostics
- System optimization
- Bottleneck identification
- Resource tuning

**Skills**: performance-optimization, performance-profiling, monitoring-observability, urbit-troubleshooting

---

## Skills

Skills are modular knowledge packages activated by agents as needed. All skills include comprehensive examples, configuration patterns, and operational procedures.

### Core Fundamentals (3)

1. **urbit-fundamentals** - Deep understanding of Urbit architecture (Nock, Hoon, Arvo, Vere, vanes, loom, Azimuth, Ames) — for Hoon programming, see hoon-development plugin
2. **ship-deployment-guide** - Step-by-step deployment procedures for planets, comets, and fake ships
3. **urbit-troubleshooting** - Systematic diagnostic procedures and decision trees

### Deployment & Infrastructure (5)

4. **vps-deployment-providers** - Comprehensive VPS provider comparison (Hetzner, DigitalOcean, Linode, Vultr, AWS, OVH)
5. **groundseg-installation** - Complete GroundSeg Docker platform setup
6. **groundseg-troubleshooting** - Container platform diagnostics
7. **kubernetes-urbit** - Production Kubernetes StatefulSets and service mesh
8. **multi-ship-orchestration** - Fleet management and resource allocation

### Networking & Security (4)

9. **anchor-networking** - Self-hosted reverse proxy and SSL/TLS termination
10. **network-security-advanced** - VPS hardening, SSH security, firewall configuration, intrusion detection
11. **container-security** - Docker security and GroundSeg hardening
12. **advanced-security-patterns** - Defense-in-depth, zero-trust, multi-tenant isolation

### Storage & Backup (3)

13. **minio-s3-integration** - Self-hosted S3 storage for Urbit ships
14. **backup-disaster-recovery** - Comprehensive backup strategies and recovery procedures
15. **disaster-recovery-advanced** - Enterprise multi-region replication and automated failover

### Monitoring & Operations (4)

16. **monitoring-observability** - Prometheus, Grafana, metrics collection, alerting
17. **performance-optimization** - System-level tuning (I/O, kernel, memory, CPU)
18. **performance-profiling** - Advanced profiling techniques (CPU, memory, I/O tracing)
19. **sla-management** - SLI/SLO/SLA frameworks and incident response

### Management (3)

20. **fleet-operations** - Large-scale fleet management, Terraform IaC, Helm templating
21. **managed-hosting-comparison** - Tlon vs Red Horizon vs self-hosting analysis
22. **app-development-workflow** - Local development, Gall agents, desk publishing (see hoon-development plugin for building apps)

### Compliance (1)

23. **compliance-frameworks** - GDPR, HIPAA, SOC2, ISO 27001 for enterprise deployments

## Commands

Workflow orchestrators for common operational tasks. Each command provides a multi-phase structured approach.

### `/deploy-planet`
**Subagent**: urbit-deployment-specialist
**Purpose**: Bare-metal planet deployment from system assessment to production validation

**10-Phase Workflow:**
1. System Assessment - Validate requirements
2. System Preparation - Install dependencies
3. Urbit Binary - Download and verify
4. Keyfile Management - Secure handling
5. Ship Initialization - First boot
6. Network Configuration - Ports and firewall
7. Systemd Integration - Auto-restart
8. Security Hardening - Production lockdown
9. Monitoring Setup - Health checks
10. Production Validation - Final verification

---

### `/deploy-vps-planet`
**Subagent**: vps-deployment-specialist
**Purpose**: Cloud VPS-optimized deployment

**8-Phase Workflow:**
1. Provider Selection - Choose optimal VPS
2. VPS Provisioning - Create and configure
3. SSH Hardening - Secure access
4. System Preparation - Dependencies
5. Ship Deployment - Urbit installation
6. DNS & SSL - Domain configuration
7. Monitoring - CloudWatch/metrics
8. Validation - Health checks

---

### `/deploy-groundseg`
**Subagent**: groundseg-operator
**Purpose**: Multi-ship Docker orchestration with GroundSeg

**8-Phase Workflow:**
1. Planning - Resource allocation
2. Server Preparation - Docker installation
3. GroundSeg Installation - Platform setup
4. MinIO Integration - S3 storage
5. Ship Provisioning - Bulk deployment
6. Network Configuration - Anchor/StarTram
7. Monitoring - Container health
8. Validation - Fleet verification

---

### `/deploy-fleet`
**Subagent**: fleet-manager
**Purpose**: Enterprise Kubernetes fleet deployment

**10-Phase Workflow:**
1. Architecture Design - Fleet planning
2. Kubernetes Setup - Cluster preparation
3. Storage Configuration - PersistentVolumes
4. Networking - LoadBalancers and ingress
5. Ship Deployment - StatefulSets
6. GitOps Integration - ArgoCD/Flux
7. Monitoring Stack - Prometheus/Grafana
8. Security Policies - NetworkPolicies
9. Backup Automation - Velero
10. Production Validation - SLA verification

---

### `/setup-production`
**Subagent**: urbit-deployment-specialist
**Purpose**: 20-phase security hardening and production readiness

**Security Layers:**
- SSH hardening and key management
- Firewall rules (ufw/iptables)
- Fail2ban intrusion detection
- Automatic security updates
- Log aggregation
- Backup automation
- SSL/TLS certificates
- Resource limits

---

### `/setup-monitoring`
**Subagent**: fleet-manager
**Purpose**: Comprehensive observability stack deployment

**Components:**
- Prometheus metrics collection
- Grafana dashboards
- Alertmanager notifications
- Node Exporter system metrics
- Custom ship health checks
- Capacity planning

---

### `/setup-cicd-pipeline`
**Subagent**: vps-deployment-specialist
**Purpose**: Automated CI/CD for ship deployments and OTA updates

**Pipeline Stages:**
- Automated testing
- Staging deployment
- Production rollout
- Rollback procedures
- Infrastructure-as-Code

---

### `/troubleshoot-ship`
**Subagent**: Multiple agents (systematic triage)
**Purpose**: Systematic diagnostics and problem resolution

**Decision Trees:**
- Boot failures
- Network connectivity
- Performance degradation
- OTA update issues
- Pier corruption
- Resource exhaustion

---

### `/migrate-deployment`
**Subagent**: managed-hosting-advisor
**Purpose**: Zero-downtime migration between hosting platforms

**Migration Paths:**
- Tlon → Self-hosted
- VPS → GroundSeg
- GroundSeg → Kubernetes
- Provider → Provider

---

### `/optimize-performance`
**Subagent**: performance-engineer
**Purpose**: Systematic performance tuning and optimization

**Optimization Areas:**
- I/O scheduler tuning
- Kernel parameters
- Memory management
- CPU pinning
- Disk performance
- Network optimization

---

## Related Plugins

### hoon-development

Before deploying Urbit ships with **urbit-operations**, learn Hoon and build custom Gall agents with the **hoon-development** plugin.

**Workflow**: Learn Hoon → Build Gall Agents → Deploy Infrastructure

**hoon-development provides:**
- Interactive Hoon learning (`/hoon-learn`) from beginner to expert
- Gall agent development and architecture (`app-architect` agent)
- Code review and optimization (`/hoon-review`, `/hoon-optimize`)
- Testing and debugging workflows (`/hoon-test`, `/hoon-debug`)
- Project scaffolding (`/hoon-scaffold`)
- 18 comprehensive skills covering Hoon fundamentals through advanced patterns

**When to use hoon-development:**
- Learning Hoon programming language
- Building custom Gall agents for your Urbit ships
- Developing Urbit applications and desks
- Understanding Urbit's functional programming model
- Optimizing and debugging Hoon code

See: `/plugin install hoon-development`

---

### nock-development

Optimize your deployed Urbit ships' performance by understanding how Hoon compiles to Nock assembly with the **nock-development** plugin. Analyze runtime behavior, implement jets, and achieve 100x-1000x performance improvements.

**Workflow**: Deploy Ships → Profile Performance → Analyze Nock → Implement Jets → Redeploy Optimized

**nock-development provides:**
- Hoon→Nock compilation analysis (`/hoon-to-nock`)
- Nock interpreter implementation (`/build-nock-interpreter`)
- Performance optimization and jetting (`/optimize-nock-performance`)
- Nock fundamentals learning (`/learn-nock-fundamentals`)
- Systematic debugging (`/debug-nock-execution`)
- Hands-on implementation exercises (`/nock-implement-exercise`)

**When to use nock-development:**
- After deploying Urbit ships, when optimizing performance bottlenecks
- Understanding how your Hoon applications compile to Nock assembly
- Implementing native jets for 100x-1000x performance improvements
- Building custom Nock interpreters or runtimes
- Debugging low-level execution issues in production ships
- Learning the computational substrate underlying Urbit

**Cross-plugin workflow:**
1. Build Gall agents with **hoon-development** (`/hoon-scaffold`, `/hoon-test`)
2. Deploy to infrastructure with **urbit-operations** (`/deploy-planet`, `/setup-production`)
3. Profile performance with **urbit-operations** (`/optimize-performance`)
4. Analyze Nock output with **nock-development** (`/hoon-to-nock`)
5. Implement jets for bottlenecks (`/optimize-nock-performance`)
6. Redeploy optimized ships with **urbit-operations**

See: `/plugin install nock-development`

---

## Installation

```bash
# In Claude Code, install the plugin
/plugin install urbit-operations

# Verify installation
/plugins
```

## Quick Start

### Deploying Your First Ship

```bash
# Option 1: Bare-metal deployment
/deploy-planet

# Option 2: VPS cloud deployment
/deploy-vps-planet

# Option 3: Multi-ship Docker orchestration
/deploy-groundseg
```

### Production Hardening

```bash
# Secure your deployment
/setup-production

# Add monitoring
/setup-monitoring
```

### Fleet Management

```bash
# Enterprise Kubernetes deployment
/deploy-fleet
```

### Troubleshooting

```bash
# Systematic diagnostics
/troubleshoot-ship
```

## Usage Examples

### Example 1: Single Ship VPS Deployment

```
User: I want to deploy my planet ~sampel-palnet on a DigitalOcean droplet

Claude: I'll help you deploy your planet on DigitalOcean. Let me activate the vps-deployment-specialist agent.

[Agent activates vps-deployment-providers and ship-deployment-guide skills]

Recommended configuration:
- Droplet: 4GB RAM / 2 vCPUs / 80GB SSD ($24/mo)
- Region: Choose closest to your location
- OS: Ubuntu 22.04 LTS

Let's begin the deployment...
```

### Example 2: Multi-Ship GroundSeg Fleet

```
User: I need to host 10 Urbit ships for my organization

Claude: For 10 ships, I recommend GroundSeg on a dedicated server. Let me activate the groundseg-operator agent.

[Agent activates groundseg-installation and multi-ship-orchestration skills]

Server requirements for 10 ships:
- RAM: 40-60GB
- Storage: 500GB-1TB
- CPU: 10+ cores
- Provider: Hetzner AX41 ($44/mo) or similar

I'll guide you through:
1. GroundSeg installation
2. MinIO S3 setup for efficient storage
3. Bulk ship provisioning
4. Monitoring and alerts
```

### Example 3: Enterprise Kubernetes Fleet

```
User: We need to deploy 100 Urbit ships with 99.9% uptime SLA

Claude: For 100 ships with enterprise SLA requirements, we'll use Kubernetes. Let me activate the fleet-manager agent.

[Agent activates kubernetes-urbit, fleet-operations, monitoring-observability, sla-management skills]

Architecture:
- Kubernetes cluster (3 control plane nodes, 10+ worker nodes)
- StatefulSets for each ship (n=1 replica with PersistentVolumes)
- LoadBalancer for UDP 34543 (Ames)
- Prometheus/Grafana monitoring
- Automated backups with Velero
- GitOps with ArgoCD

Let's design the infrastructure...
```

## Architecture Overview

### Deployment Models (Progressive Complexity)

1. **Tlon Managed Hosting** (Easiest)
   - Zero operations
   - Free tier available
   - Limited customization

2. **VPS Single Ship** (Beginner-Friendly)
   - Full control
   - $12-24/month
   - 1 ship per VPS

3. **Bare-Metal** (Advanced)
   - Maximum performance
   - Hardware ownership
   - Enterprise security

4. **GroundSeg (Docker)** (5-20 ships)
   - Container orchestration
   - Web UI management
   - $40-160/month

5. **Kubernetes** (25-1000+ ships)
   - Enterprise orchestration
   - Multi-region
   - Mission-critical SLAs

## Support

- **Documentation**: [Urbit Operators Manual](https://operators.urbit.org)
- **GroundSeg Docs**: [manual.groundseg.app](https://manual.groundseg.app)
- **Community**: Urbit groups and forums
- **Issues**: [GitHub repository](https://github.com/toplyr-narfur/urbit-agents/issues)

## Contributing

This plugin is maintained as part of the urbit-agents marketplace fork. Contributions welcome!

## License

MIT
