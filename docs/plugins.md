# Plugin Reference

This repository contains **3 specialized plugins** exclusively focused on Urbit development and operations.

## The 3 Plugins

### urbit-operations - Deployment & Fleet Management

Production Urbit ship deployment and fleet management with **6 agents**, **10 commands**, and **23 skills** for bare-metal, VPS, GroundSeg, Kubernetes, and managed hosting.

**Install:**
```bash
/plugin install urbit-operations
```

**Key Features:**
- Multiple deployment models (bare-metal, VPS, GroundSeg, Kubernetes)
- 20-phase production hardening
- Enterprise monitoring with Prometheus, Grafana, Loki
- Fleet operations for 100-1,000+ ships
- GDPR, HIPAA, SOC2, ISO 27001 compliance frameworks

[→ View urbit-operations documentation](../plugins/urbit-operations/README.md)

### hoon-development - Language & Application Development

Expert Hoon programming with **4 agents**, **7 commands**, and **18 skills** covering language architecture through production Gall agent development.

**Install:**
```bash
/plugin install hoon-development
```

**Key Features:**
- Production Hoon implementation and architecture
- P0-P3 prioritized code reviews
- Comprehensive testing and debugging workflows
- Gall agent development lifecycle
- Performance optimization workflows

[→ View hoon-development documentation](../plugins/hoon-development/README.md)

### nock-development - Assembly Language & Interpreter Expertise

Build Nock interpreters and optimize performance with **4 agents**, **6 commands**, and **12 skills** for implementing virtual machines, jetting, and Hoon→Nock analysis.

**Install:**
```bash
/plugin install nock-development
```

**Key Features:**
- Nock interpreter implementation in C, Python, Rust, Haskell, JavaScript
- Performance optimization (10x-1000x improvements)
- Jetting and native code acceleration
- Hoon→Nock compilation analysis
- Hands-on learning and implementation exercises

[→ View nock-development documentation](../plugins/nock-development/README.md)

## Installation

### Step 1: Add the Marketplace

```bash
/plugin marketplace add toplyr-narfur/urbit-agents
```

This makes the Urbit-focused plugins available for installation, but **does not load any agents or tools** into your context.

### Step 2: Install Plugins

Browse available plugins:

```bash
/plugin
```

Install all three for the complete Urbit workflow:

```bash
/plugin install urbit-operations
/plugin install hoon-development
/plugin install nock-development
```

Or install just the one you need for your current task.

## Complete Plugin Structure

```
urbit-agents/
├── .claude-plugin/
│   └── marketplace.json                    # Marketplace definition (all three plugins)
├── plugins/
│   ├── hoon-development/                   # Hoon programming plugin
│   │   ├── agents/                         # 4 development agents
│   │   ├── commands/                       # 7 development workflows
│   │   └── skills/                         # 18 Hoon skills
│   ├── nock-development/                   # Nock assembly language plugin
│   │   ├── agents/                         # 4 Nock agents
│   │   ├── commands/                       # 6 Nock workflows
│   │   └── skills/                         # 12 Nock skills
│   └── urbit-operations/                   # Infrastructure operations plugin
│       ├── agents/                         # 6 operations agents
│       ├── commands/                       # 10 operational workflows
│       └── skills/                         # 23 operations skills
├── docs/                                   # Documentation
└── README.md                               # Main documentation
```

## Workflow Integration

The three plugins work together seamlessly for end-to-end Urbit application development, low-level optimization, and deployment:

**Development → Assembly → Deployment**

1. Build application with Hoon (`hoon-development`)
2. Analyze Nock compilation output (`nock-development`)
3. Implement jets for performance (`nock-development`)
4. Deploy to production (`urbit-operations`)
5. Monitor and optimize (`urbit-operations`)

## See Also

- [README](../README.md) - Main documentation
- [Architecture](./architecture.md) - Design principles
- [Agent Reference](./agents.md) - Complete agent catalog
- [Agent Skills](./agent-skills.md) - Specialized skills documentation
- [Usage Guide](./usage.md) - Commands and workflows

