# Urbit Agents: Operations & Development

> **⚡ Optimized for Sonnet 4.5 & Haiku 4.5** — All agents tuned for latest models with hybrid orchestration
>
> **🚀 Urbit-Specific Expertise** — Complete Urbit stack from Hoon development to fleet operations

A comprehensive production-ready system for [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) with **three specialized plugins** for the complete Urbit development, low-level Nock expertise, and operations lifecycle:

## About

This repository provides specialized Claude Code plugins exclusively for Urbit development and operations. It is maintained as an independent project focused on helping operators and developers deploy, manage, and optimize Urbit infrastructure at any scale.

For questions, feedback, or support, reach out to **~toplyr-narfur** on Tlon (Urbit's social network).

## Support This Project

If you find these tools valuable, consider supporting the development:

[![Bitcoin Donation](https://img.shields.io/badge/Bitcoin-Donate-F7931A?style=flat-square&logo=bitcoin)](bitcoin:bc1qwkkkg6q9lpe464nzuq054xsawj7t5ykpvd7arn)

**Bitcoin Wallet**: `bc1qwkkkg6q9lpe464nzuq054xsawj7t5ykpvd7arn`

Your support helps maintain and expand these operational tools.

## Plugins

### **urbit-operations** - Deployment & Fleet Management
Production Urbit ship deployment and fleet management with **6 agents**, **10 commands**, and **23 skills** for bare-metal, VPS, GroundSeg, Kubernetes, and managed hosting.

[→ View urbit-operations documentation](#urbit-operations-plugin)

### **hoon-development** - Language & Application Development
Expert Hoon programming with **4 agents**, **7 commands**, and **18 skills** covering language architecture through production Gall agent development.

[→ View hoon-development documentation](#hoon-development-plugin)

### **nock-development** - Assembly Language & Interpreter Expertise
Build Nock interpreters and optimize performance with **4 agents**, **6 commands**, and **12 skills** for implementing virtual machines, jetting, and Hoon→Nock analysis.

[→ View nock-development documentation](#nock-development-plugin)

## Overview

This marketplace provides the complete Urbit development and operations lifecycle through three specialized plugins:

### Complete Workflow: Build → Understand → Deploy

**hoon-development** → Expert Hoon programming and production Gall agents
**nock-development** → Nock assembly, interpreter implementation, performance optimization
**urbit-operations** → Deploy and manage production Urbit infrastructure

### Combined Capabilities

**14 Specialized Agents** - Expert assistance across development, assembly, and operations:
- **4 Development Agents** - Expert code implementation, review, debugging, architecture
- **4 Nock Agents** - Interpreter implementation, optimization, specification, fundamentals
- **6 Operations Agents** - Deployment, fleet management, hosting advisory, performance engineering

**23 Operational Commands** - Production workflows for the entire lifecycle:
- **7 Development Commands** - Scaffold, review, test, debug, refactor, optimize, migrate
- **6 Nock Commands** - Build interpreters, optimize, learn fundamentals, debug, analyze Hoon→Nock
- **10 Operations Commands** - Deploy, harden, monitor, troubleshoot, migrate infrastructure

**53 Knowledge Skills** - Comprehensive expertise from fundamentals to enterprise:
- **18 Development Skills** - Hoon language, types, Gall agents, parsing, data structures
- **12 Nock Skills** - Assembly fundamentals, interpreters, jetting, performance profiling
- **23 Operations Skills** - Deployment, security, monitoring, fleet management, compliance

### Key Features

**Development (hoon-development):**
- **Expert Development**: Production-ready Hoon implementation and architecture
- **Production Best Practices**: Code review with P0-P3 prioritization, security analysis
- **Testing & Debugging**: Comprehensive testing workflows, systematic error diagnosis
- **Gall Agent Development**: Complete lifecycle from scaffolding to state migrations
- **Performance Optimization**: Systematic profiling and optimization workflows

**Assembly & Optimization (nock-development):**
- **Interpreter Implementation**: Build Nock VMs in C, Python, Rust, Haskell, JavaScript
- **Performance Optimization**: Achieve 10x-1000x speedups through jetting and profiling
- **Hoon→Nock Analysis**: Understand compilation output and identify optimization opportunities
- **Hands-On Exercises**: Progressive challenges from increment through decrement
- **Production Runtime**: Cold/hot/warm jet validation, metacircular evaluation

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
1. **Scaffold Applications** - Bootstrap projects with `app-architect` agent
2. **Implement Features** - Expert code development with `hoon-expert` agent
3. **Review & Test** - Quality assurance with `code-reviewer` and comprehensive testing
4. **Debug & Optimize** - Systematic debugging and performance tuning
5. **Analyze Nock** - Hand off to nock-development for low-level optimization
6. **Deploy** - Hand off to urbit-operations for production deployment

**Assembly Workflow (nock-development):**
1. **Learn Fundamentals** - Master Nock from atoms through cores
2. **Build Interpreters** - Implement Nock VMs in multiple languages
3. **Analyze Compilation** - Understand how Hoon compiles to Nock
4. **Optimize Performance** - Profile, jet, and achieve 100x-1000x speedups
5. **Debug Execution** - Systematic Nock formula debugging

**Operations Workflow (urbit-operations):**
1. **Choose Deployment** - Bare-metal, VPS, GroundSeg, or Kubernetes based on scale
2. **Deploy Infrastructure** - Automated setup with deployment specialists
3. **Harden Security** - 20-phase production hardening baseline
4. **Monitor & Scale** - Enterprise observability and fleet management
5. **Optimize Performance** - Systematic profiling and tuning

**Example End-to-End**:
```
/hoon-scaffold             # Scaffold project (hoon-development)
  ↓
"Build a task queue agent" # Implement features (hoon-development)
  ↓
/hoon-review               # Quality assurance (hoon-development)
  ↓
/hoon-to-nock              # Analyze Nock output (nock-development)
  ↓
/optimize-nock-performance # Jet slow formulas (nock-development)
  ↓
/deploy-vps-planet         # Deploy to production (urbit-operations)
  ↓
/setup-production          # Security hardening (urbit-operations)
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

Install all three plugins for the complete Urbit workflow:

```bash
# For Hoon development and Gall agent building
/plugin install hoon-development

# For Nock assembly language and interpreter development
/plugin install nock-development

# For deployment and infrastructure management
/plugin install urbit-operations
```

**hoon-development** loads: **4 agents, 7 commands, 18 skills**
**nock-development** loads: **4 agents, 6 commands, 12 skills**
**urbit-operations** loads: **6 agents, 10 commands, 23 skills**

Or install just the one you need for your current task.

### Prerequisites

**For Hoon Development (hoon-development):**
- Working knowledge of functional programming concepts
- Experience with Hoon syntax and subject-oriented programming
- Urbit ship (planet, moon, or fake ship for testing)
- Text editor with Hoon syntax support (VS Code recommended)

**For Nock Development (nock-development):**
- Programming experience in at least one of: C, Python, Rust, Haskell, JavaScript
- Understanding of functional programming and recursion
- Basic knowledge of assembly languages or virtual machines (helpful but not required)
- Interest in building interpreters, compilers, or language runtimes

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
- **[Development Agents](plugins/hoon-development/agents/)** - All 4 agents with activation criteria
- **[Development Commands](plugins/hoon-development/commands/)** - 7 workflow commands
- **[Hoon Skills Library](plugins/hoon-development/skills/)** - 18 comprehensive skills

**nock-development:**
- **[Plugin Overview](plugins/nock-development/README.md)** - Complete Nock assembly documentation
- **[Nock Agents](plugins/nock-development/agents/)** - All 4 agents with activation criteria
- **[Nock Commands](plugins/nock-development/commands/)** - 6 workflow commands
- **[Nock Skills Library](plugins/nock-development/skills/)** - 12 specialized skills

**urbit-operations:**
- **[Plugin Overview](plugins/urbit-operations/README.md)** - Complete operations documentation
- **[Operations Agents](plugins/urbit-operations/agents/)** - All 6 agents with activation criteria
- **[Operations Commands](plugins/urbit-operations/commands/)** - 10 operational workflows
- **[Operations Skills Library](plugins/urbit-operations/skills/)** - 23 specialized knowledge packages

### Quick Links

**Development:**
- [Code Review](plugins/hoon-development/commands/hoon-review.md) - P0-P3 prioritized reviews
- [Testing](plugins/hoon-development/commands/hoon-test.md) - Comprehensive testing workflows
- [Debugging](plugins/hoon-development/commands/hoon-debug.md) - Systematic error resolution
- [Scaffolding](plugins/hoon-development/commands/hoon-scaffold.md) - Project bootstrapping

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

Expert Hoon programming for production Urbit application development with specialized agents, comprehensive workflows, and best practices.

### Features

- **4 Specialized Agents** - Expert development, code review, debugging, architecture
- **7 Workflow Commands** - Structured multi-phase production workflows
- **18 Comprehensive Skills** - Advanced knowledge from syntax reference to metaprogramming
- **P0-P3 Code Reviews** - Production-grade quality assurance
- **TDD Workflows** - Test-driven development with comprehensive coverage
- **Performance Optimization** - Systematic profiling and tuning

### Agents

#### `hoon-expert` (Sonnet)
Expert Hoon developer for complex code implementation, architectural decisions, and performance optimization.

**Use for:** Production application development, complex Hoon patterns, performance tuning

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
- **hoon-basics** - Quick reference for syntax fundamentals, rune forms, and common idioms
- **hoon-fundamentals** - Architectural reference for subject-oriented programming and noun model
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
**`/hoon-migrate`** - 5-phase safe state migration for Gall agents
**`/hoon-scaffold`** - 6-phase project bootstrapping with best practices

### Use Cases

**Building Applications**: Complete Gall agent lifecycle
```bash
/hoon-scaffold  # Bootstrap project with desk structure
"Build a distributed task queue with priority scheduling"
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

## Nock Development Plugin

Production-ready Nock assembly language expertise for building interpreters, optimizing performance, and understanding Urbit's computational substrate.

### Features

- **4 Specialized Agents** - Expert assistance from learning through production optimization
- **6 Workflow Commands** - Structured multi-phase workflows from learning to deployment
- **12 Comprehensive Skills** - Progressive knowledge from nouns to jets to Hoon compilation
- **Implementation Focus** - Built for engineers building Nock interpreters and optimizing performance
- **Multi-Language Support** - Patterns for C, Python, Rust, Haskell, JavaScript
- **Cross-Referenced** - Heavy integration with hoon-development for Hoon→Nock analysis

### Agents

#### `nock-interpreter-engineer` (Sonnet)
Expert Nock interpreter builder specializing in implementing Nock virtual machines across multiple programming languages.

**Use for:** Building Nock interpreters, porting Nock to new languages, understanding runtime behavior

#### `nock-optimization-specialist` (Sonnet)
Performance tuning expert specializing in Nock optimization, jetting, profiling, and runtime acceleration.

**Use for:** Optimizing Nock execution speed (10x-1000x improvements), implementing jets, profiling bottlenecks

#### `nock-specification-expert` (Haiku)
Formal semantics expert specializing in Nock specification, operator definitions, and canonical behavior.

**Use for:** Understanding formal Nock behavior, resolving edge cases, validating interpreter correctness

#### `nock-fundamentals-tutor` (Sonnet)
Educational specialist teaching Nock fundamentals to implementers through hands-on exercises and progressive learning.

**Use for:** Learning Nock from scratch, understanding core concepts, preparing to build interpreters

### Skills Breakdown

**Foundation (3 skills):**
- **nock-essentials** - Nouns (atoms/cells), reduction model, evaluation semantics
- **nock-operators** - Complete reference for 6 operators (?, +, =, /, #, *) with formal semantics
- **nock-instructions** - All 13 reduction rules (0-12) with examples and implementation patterns

**Implementation (2 skills):**
- **nock-interpreter-patterns** - Design patterns for building Nock VMs: evaluation loops, error handling, TCO
- **nock-multi-language-implementations** - Language-specific patterns across C, Python, Rust, Haskell, JavaScript

**Optimization (2 skills):**
- **nock-jetting-optimization** - Jet acceleration system: hint processing, cold/hot/warm states, native code
- **nock-performance-profiling** - Performance analysis: CPU/memory profiling, benchmarking, bottleneck identification

**Advanced (3 skills):**
- **nock-tree-addressing** - Binary tree addressing: axis encoding, slot calculation, efficient traversal
- **nock-cores-arms-batteries** - Core structure pattern: batteries (code), payloads (data), arm invocation
- **nock-metacircular-evaluation** - Self-interpretation patterns: +mock (Nock-in-Nock), virtualization

**Integration & Reference (2 skills):**
- **nock-hoon-compilation** - How Hoon compiles to Nock: AST transformations, core generation, optimization
- **nock-specification-reference** - Complete formal Nock 4K specification: all rules, pseudo-functions, crash conditions

### Commands

**`/build-nock-interpreter`** - 8-phase workflow for building production-ready Nock interpreters
**`/optimize-nock-performance`** - 6-phase systematic performance optimization from profiling through production
**`/learn-nock-fundamentals`** - 5-phase interactive learning path for mastering Nock from nouns through cores
**`/debug-nock-execution`** - 5-phase systematic debugging from error analysis to resolution
**`/hoon-to-nock`** - 4-phase workflow analyzing how Hoon compiles to Nock for optimization
**`/nock-implement-exercise`** - 6-phase hands-on guided exercises from increment through decrement challenge

### Use Cases

**Learning Nock**: Progressive fundamentals curriculum
```bash
/learn-nock-fundamentals
"Explain how tree addressing works in Nock"
"Practice implementing basic Nock formulas"
```

**Building Interpreters**: Complete VM implementation lifecycle
```bash
/build-nock-interpreter  # 8-phase interpreter development
"Build a Nock interpreter in Python"
/nock-implement-exercise # Hands-on exercises
```

**Performance Optimization**: 100x-1000x speedups
```bash
/optimize-nock-performance  # Profile, jet, benchmark
"Jet the decrement formula for 1000x speedup"
```

**Hoon→Nock Analysis**: Understand compilation
```bash
/hoon-to-nock  # Analyze Hoon compilation output
"How does (add 2 3) compile to Nock?"
"Identify jetting opportunities in my Hoon code"
```

**Debugging**: Systematic error resolution
```bash
/debug-nock-execution  # Classification, tracing, root cause, fix
"Why does *[42 [0 0]] crash?"
```

[→ View complete nock-development documentation](plugins/nock-development/README.md)

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

## Workflow Integration: Development + Assembly + Operations

The three plugins work together seamlessly for end-to-end Urbit application development, low-level optimization, and deployment.

### Example: Building and Deploying a Custom Gall Agent

**Phase 1: Development (hoon-development)**
```bash
/plugin install hoon-development
/hoon-scaffold                 # Bootstrap project structure
# Implement Gall agent code
/hoon-test                     # Unit and integration testing
/hoon-review                   # P0-P3 quality assurance
/hoon-optimize                 # Performance tuning
```

**Phase 2: Low-Level Optimization (nock-development)**
```bash
/plugin install nock-development
/hoon-to-nock                  # Analyze how Hoon compiles to Nock
/optimize-nock-performance     # Identify jetting opportunities
# Implement native jets for 100x-1000x speedup
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

**Development → Assembly → Deployment**:
- Build app locally with fake ships (`/hoon-scaffold` + testing)
- Analyze Nock compilation output (`/hoon-to-nock`)
- Identify and implement jets for performance (`/optimize-nock-performance`)
- Deploy to production infrastructure (`/deploy-planet`)
- Monitor performance and optimize (`/optimize-performance`)

**Operations → Assembly → Development**:
- Identify performance bottleneck in production (`/optimize-performance`)
- Analyze Nock execution patterns (`/debug-nock-execution`)
- Implement jets for slow formulas (`/build-nock-interpreter`)
- Debug and optimize Hoon code (`/hoon-debug` + `/hoon-optimize`)
- Test improvements locally, redeploy via CI/CD

**Understanding Nock for Hoon Developers**:
- Build production Hoon code (`/hoon-scaffold`, `/hoon-review`)
- Learn how Hoon compiles to Nock (`/learn-nock-fundamentals`)
- Analyze your Hoon code's Nock output (`/hoon-to-nock`)
- Build custom Nock interpreter for debugging (`/build-nock-interpreter`)

**Building Urbit Runtime**:
- Learn Nock fundamentals (`/learn-nock-fundamentals`)
- Build interpreter in chosen language (`/build-nock-interpreter`)
- Implement jets for stdlib (`/optimize-nock-performance`)
- Deploy custom runtime to production (`/deploy-planet` + `/setup-production`)

**Continuous Improvement**:
```
Code (hoon-development)
  → Review (/hoon-review)
  → Test (/hoon-test)
  → Analyze Nock (nock-development: /hoon-to-nock)
  → Jet Slow Formulas (nock-development: /optimize-nock-performance)
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
│   └── marketplace.json                    # Marketplace definition (all three plugins)
├── plugins/
│   ├── hoon-development/                   # Hoon programming plugin
│   │   ├── agents/                         # 4 development agents
│   │   │   ├── hoon-expert.md
│   │   │   ├── code-reviewer.md
│   │   │   ├── debugging-specialist.md
│   │   │   └── app-architect.md
│   │   ├── commands/                       # 7 development workflows
│   │   │   ├── hoon-review.md
│   │   │   ├── hoon-optimize.md
│   │   │   ├── hoon-debug.md
│   │   │   ├── hoon-refactor.md
│   │   │   ├── hoon-test.md
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
│   ├── nock-development/                   # Nock assembly language plugin
│   │   ├── agents/                         # 4 Nock agents
│   │   │   ├── nock-interpreter-engineer.md
│   │   │   ├── nock-optimization-specialist.md
│   │   │   ├── nock-specification-expert.md
│   │   │   └── nock-fundamentals-tutor.md
│   │   ├── commands/                       # 6 Nock workflows
│   │   │   ├── build-nock-interpreter.md
│   │   │   ├── optimize-nock-performance.md
│   │   │   ├── learn-nock-fundamentals.md
│   │   │   ├── debug-nock-execution.md
│   │   │   ├── hoon-to-nock.md
│   │   │   └── nock-implement-exercise.md
│   │   ├── skills/                         # 12 Nock skills
│   │   │   ├── nock-essentials/
│   │   │   ├── nock-operators/
│   │   │   ├── nock-instructions/
│   │   │   ├── nock-interpreter-patterns/
│   │   │   ├── nock-jetting-optimization/
│   │   │   ├── nock-performance-profiling/
│   │   │   ├── nock-tree-addressing/
│   │   │   ├── nock-cores-arms-batteries/
│   │   │   ├── nock-hoon-compilation/
│   │   │   └── ... (3 more skills)
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
2. Focus on expert Hoon programming, Gall agents, and production application development
3. Include comprehensive code examples and best practices
4. Ensure practical value for experienced developers

**For nock-development plugin:**
1. Create skills, agents, or commands in `plugins/nock-development/`
2. Focus on Nock assembly, interpreter implementation, and performance optimization
3. Include multi-language code examples (C, Python, Rust, Haskell, JavaScript)
4. Provide implementation patterns for engineers building Nock VMs

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

For contributions to this repository, see the [Contributing](#contributing) section above and follow the established patterns in the three plugins.

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

**nock-development plugin:**
- [Plugin Overview](plugins/nock-development/README.md)
- [Nock Agents](plugins/nock-development/agents/)
- [Nock Commands](plugins/nock-development/commands/)
- [Nock Skills Library](plugins/nock-development/skills/)

**urbit-operations plugin:**
- [Plugin Overview](plugins/urbit-operations/README.md)
- [Operations Agents](plugins/urbit-operations/agents/)
- [Operations Commands](plugins/urbit-operations/commands/)
- [Operations Skills Library](plugins/urbit-operations/skills/)

## License

MIT License - see [LICENSE](LICENSE) file for details.
