# Claude Code Workflows & Agents

A comprehensive production-ready system combining **83 specialized AI agents**, **15 multi-agent workflow orchestrators**, and **42 development tools** for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Overview

This unified repository provides everything needed for intelligent automation and multi-agent orchestration across modern software development:

- **83 Specialized Agents** - Domain experts with deep knowledge (architecture, languages, infrastructure, quality, data/AI, business)
- **15 Workflow Orchestrators** - Multi-agent coordination systems for complex operations
- **42 Development Tools** - Focused utilities for specific tasks

## System Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and configured
- Git for repository management

## Installation

### Plugin Marketplace (Recommended - Available October 9, 2025)

Install workflow-based plugin collections directly from the Claude Code plugin marketplace:

```bash
# Add the marketplace
/plugin marketplace add wshobson/agents

# Browse available plugins
/plugin list

# Install workflow-based plugins
/plugin install full-stack-development
/plugin install security-hardening
/plugin install data-ml-pipeline
/plugin install incident-response

# Install infrastructure/specialist plugins
/plugin install cloud-infrastructure
/plugin install language-specialists
/plugin install seo-content-suite
```

### Manual Installation

```bash
cd ~/.claude
git clone https://github.com/wshobson/agents.git
```

All agents, workflows, and tools will be automatically available to Claude Code.

## Repository Structure

```
agents/
├── agents/                        # 83 specialized AI agents
│   ├── backend-architect.md
│   ├── frontend-developer.md
│   └── ... (all agent definitions)
├── workflows/                     # 15 multi-agent orchestrators
│   ├── feature-development.md
│   ├── full-stack-feature.md
│   ├── security-hardening.md
│   └── ... (workflow commands)
├── tools/                         # 42 development utilities
│   ├── api-scaffold.md
│   ├── security-scan.md
│   └── ... (tool commands)
└── README.md
```

## Workflow Commands

Multi-agent orchestration systems that coordinate complex, cross-domain tasks:

### Core Development Workflows

| Command | Purpose | Agent Coordination |
|---------|---------|-------------------|
| `feature-development` | End-to-end feature implementation | Backend, frontend, testing, deployment |
| `full-stack-feature` | Complete multi-tier implementation | Backend API, frontend UI, mobile, database |
| `full-review` | Multi-perspective code analysis | Architecture, security, performance, quality |
| `smart-fix` | Intelligent problem resolution | Dynamic agent selection based on issue type |
| `tdd-cycle` | Test-driven development orchestration | Test writer, implementer, refactoring specialist |

### Process Automation Workflows

| Command | Purpose | Scope |
|---------|---------|-------|
| `git-workflow` | Version control process automation | Branching strategies, commit standards, PR templates |
| `improve-agent` | Agent optimization | Prompt engineering, performance tuning |
| `legacy-modernize` | Codebase modernization | Architecture migration, dependency updates |
| `multi-platform` | Cross-platform development | Web, mobile, desktop coordination |
| `workflow-automate` | CI/CD pipeline automation | Build, test, deploy, monitor |

### Advanced Orchestration Workflows

| Command | Primary Focus | Specialized Agents |
|---------|---------------|-------------------|
| `security-hardening` | Security-first development | Threat modeling, vulnerability assessment |
| `data-driven-feature` | ML-powered functionality | Data science, feature engineering, model deployment |
| `ml-pipeline` | End-to-end ML infrastructure | MLOps, data engineering, model serving |
| `performance-optimization` | System-wide optimization | Profiling, caching, query optimization |
| `incident-response` | Production issue resolution | Diagnostics, root cause analysis, hotfix deployment |

## Development Tools

Focused, single-purpose utilities for specific development operations:

### AI and Machine Learning
- `langchain-agent` - LangChain agent development
- `ai-assistant` - AI-powered development assistance
- `ai-review` - AI-based code review

### API Development
- `api-scaffold` - API endpoint scaffolding
- `api-mock` - API mocking and testing

### Testing & Quality
- `tdd-red` - Red phase (failing tests)
- `tdd-green` - Green phase (passing implementation)
- `tdd-refactor` - Refactor phase
- `test-harness` - Test infrastructure setup

### Security & Compliance
- `security-scan` - Vulnerability scanning
- `compliance-check` - Compliance validation

### Infrastructure & Operations
- `k8s-manifest` - Kubernetes manifest generation
- `docker-optimize` - Docker optimization
- `monitor-setup` - Monitoring infrastructure
- `deploy-checklist` - Deployment validation

### Code Quality
- `code-explain` - Code explanation
- `code-migrate` - Code migration
- `refactor-clean` - Code refactoring
- `pr-enhance` - Pull request enhancement

### And 20+ more tools for debugging, documentation, data validation, cost optimization, and developer workflows

## Usage

### Workflow Invocation

```bash
# Full-stack feature development
/workflows:feature-development implement OAuth2 authentication

# Security hardening
/workflows:security-hardening perform security audit and remediation

# ML pipeline
/workflows:ml-pipeline build recommendation system with monitoring

# Incident response
/workflows:incident-response debug production memory leak
```

### Tool Invocation

```bash
# API scaffolding
/tools:api-scaffold create user management endpoints

# Security scanning
/tools:security-scan perform vulnerability assessment

# Documentation generation
/tools:doc-generate create API documentation
```

### Direct Agent Access

Agents are automatically available and can be explicitly invoked:

```bash
"Use backend-architect to design the authentication API"
"Have security-auditor scan for OWASP vulnerabilities"
"Get performance-engineer to optimize this bottleneck"
```

## Agent Categories

### Architecture & System Design (7 agents)
backend-architect, cloud-architect, kubernetes-architect, hybrid-cloud-architect, graphql-architect, terraform-specialist, architect-review

### Programming Languages (15 agents)
javascript-pro, typescript-pro, python-pro, golang-pro, rust-pro, java-pro, csharp-pro, c-pro, cpp-pro, ruby-pro, php-pro, scala-pro, elixir-pro, django-pro, fastapi-pro

### Infrastructure & Operations (9 agents)
devops-troubleshooter, deployment-engineer, database-admin, database-optimizer, database-architect, network-engineer, incident-responder, performance-engineer, observability-engineer

### Security & Quality (9 agents)
code-reviewer, security-auditor, backend-security-coder, frontend-security-coder, mobile-security-coder, test-automator, tdd-orchestrator, debugger, error-detective

### Frontend & Mobile (7 agents)
frontend-developer, ui-ux-designer, ui-visual-validator, mobile-developer, ios-developer, flutter-expert, unity-developer

### Data & AI (6 agents)
data-scientist, data-engineer, ml-engineer, mlops-engineer, ai-engineer, prompt-engineer

### Documentation (5 agents)
docs-architect, api-documenter, reference-builder, tutorial-engineer, mermaid-expert

### Business & Operations (6 agents)
business-analyst, hr-pro, legal-advisor, customer-support, sales-automator, content-marketer

### SEO & Content (10 agents)
seo-content-writer, seo-content-auditor, seo-keyword-strategist, seo-meta-optimizer, seo-structure-architect, seo-snippet-hunter, seo-content-refresher, seo-cannibalization-detector, seo-authority-builder, seo-content-planner

### Specialized Domains (7 agents)
blockchain-developer, quant-analyst, risk-manager, payment-integration, minecraft-bukkit-pro, legacy-modernizer, context-manager

### Utilities (3 agents)
search-specialist, dx-optimizer, sql-pro

## Multi-Agent Orchestration Examples

### Full-Stack Development
```bash
/workflows:full-stack-feature implement user dashboard with analytics
```
**Orchestrates**: backend-architect → graphql-architect → frontend-developer → mobile-developer → test-automator → security-auditor → performance-engineer → deployment-engineer

### Security Hardening
```bash
/workflows:security-hardening implement security best practices
```
**Orchestrates**: security-auditor → backend-security-coder → frontend-security-coder → mobile-security-coder → test-automator

### Data/ML Pipeline
```bash
/workflows:ml-pipeline build customer churn prediction model
```
**Orchestrates**: data-scientist → data-engineer → ml-engineer → mlops-engineer → ai-engineer → performance-engineer

### Incident Response
```bash
/workflows:incident-response debug high CPU usage in production
```
**Orchestrates**: incident-responder → devops-troubleshooter → debugger → error-detective → observability-engineer

## Model Configuration

Agents are assigned to specific Claude models based on task complexity:

| Model | Count | Use Cases |
|-------|-------|-----------|
| **Opus** | 22 | Complex architecture, critical analysis, security audits, business operations |
| **Sonnet** | 50 | Standard development, engineering tasks, quality assurance |
| **Haiku** | 11 | Quick focused tasks, SEO optimization, reference building |

## Multi-Agent Orchestration Patterns

### Sequential Processing
```
backend-architect → frontend-developer → test-automator → security-auditor
```

### Parallel Execution
```
performance-engineer + database-optimizer → Merged optimization
```

### Conditional Routing
```
debugger → [backend-architect | frontend-developer | devops-troubleshooter]
```

### Validation Pipeline
```
feature-development → security-auditor → performance-engineer → Validated release
```

## Migration from Commands Repository

This repository now includes all functionality from the separate `commands` repository. The commands repo is being deprecated in favor of this unified structure. All workflows and tools are now available in a single installation.

## Contributing

To add new agents, workflows, or tools:

1. Place agent definitions in `agents/` directory
2. Place workflow orchestrators in `workflows/` directory
3. Place tool commands in `tools/` directory
4. Follow existing naming conventions (lowercase, hyphen-separated)
5. Include proper frontmatter in markdown files

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Resources

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Subagents Documentation](https://docs.anthropic.com/en/docs/claude-code/sub-agents)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)
