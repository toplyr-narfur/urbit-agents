# Claude Code Workflows & Agents

A comprehensive production-ready system combining **83 specialized AI agents**, **15 multi-agent workflow orchestrators**, and **42 development tools** for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Overview

This unified repository provides everything needed for intelligent automation and multi-agent orchestration across modern software development:

- **83 Specialized Agents** - Domain experts with deep knowledge across architecture, languages, infrastructure, quality, data/AI, documentation, business operations, and SEO
- **15 Workflow Orchestrators** - Multi-agent coordination systems for complex operations like full-stack development, security hardening, ML pipelines, and incident response
- **42 Development Tools** - Focused utilities for specific tasks including API scaffolding, security scanning, test automation, and infrastructure setup

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

## Agent Categories

### Architecture & System Design

#### Core Architecture

| Agent | Model | Description |
|-------|-------|-------------|
| [backend-architect](agents/backend-architect.md) | opus | RESTful API design, microservice boundaries, database schemas |
| [frontend-developer](agents/frontend-developer.md) | sonnet | React components, responsive layouts, client-side state management |
| [graphql-architect](agents/graphql-architect.md) | opus | GraphQL schemas, resolvers, federation architecture |
| [architect-reviewer](agents/architect-review.md) | opus | Architectural consistency analysis and pattern validation |
| [cloud-architect](agents/cloud-architect.md) | opus | AWS/Azure/GCP infrastructure design and cost optimization |
| [hybrid-cloud-architect](agents/hybrid-cloud-architect.md) | opus | Multi-cloud strategies across cloud and on-premises environments |
| [kubernetes-architect](agents/kubernetes-architect.md) | opus | Cloud-native infrastructure with Kubernetes and GitOps |

#### UI/UX & Mobile

| Agent | Model | Description |
|-------|-------|-------------|
| [ui-ux-designer](agents/ui-ux-designer.md) | sonnet | Interface design, wireframes, design systems |
| [ui-visual-validator](agents/ui-visual-validator.md) | sonnet | Visual regression testing and UI verification |
| [mobile-developer](agents/mobile-developer.md) | sonnet | React Native and Flutter application development |
| [ios-developer](agents/ios-developer.md) | sonnet | Native iOS development with Swift/SwiftUI |
| [flutter-expert](agents/flutter-expert.md) | sonnet | Advanced Flutter development with state management |

### Programming Languages

#### Systems & Low-Level

| Agent | Model | Description |
|-------|-------|-------------|
| [c-pro](agents/c-pro.md) | sonnet | System programming with memory management and OS interfaces |
| [cpp-pro](agents/cpp-pro.md) | sonnet | Modern C++ with RAII, smart pointers, STL algorithms |
| [rust-pro](agents/rust-pro.md) | sonnet | Memory-safe systems programming with ownership patterns |
| [golang-pro](agents/golang-pro.md) | sonnet | Concurrent programming with goroutines and channels |

#### Web & Application

| Agent | Model | Description |
|-------|-------|-------------|
| [javascript-pro](agents/javascript-pro.md) | sonnet | Modern JavaScript with ES6+, async patterns, Node.js |
| [typescript-pro](agents/typescript-pro.md) | sonnet | Advanced TypeScript with type systems and generics |
| [python-pro](agents/python-pro.md) | sonnet | Python development with advanced features and optimization |
| [ruby-pro](agents/ruby-pro.md) | sonnet | Ruby with metaprogramming, Rails patterns, gem development |
| [php-pro](agents/php-pro.md) | sonnet | Modern PHP with frameworks and performance optimization |

#### Enterprise & JVM

| Agent | Model | Description |
|-------|-------|-------------|
| [java-pro](agents/java-pro.md) | sonnet | Modern Java with streams, concurrency, JVM optimization |
| [scala-pro](agents/scala-pro.md) | sonnet | Enterprise Scala with functional programming and distributed systems |
| [csharp-pro](agents/csharp-pro.md) | sonnet | C# development with .NET frameworks and patterns |

#### Specialized Platforms

| Agent | Model | Description |
|-------|-------|-------------|
| [elixir-pro](agents/elixir-pro.md) | sonnet | Elixir with OTP patterns and Phoenix frameworks |
| [django-pro](agents/django-pro.md) | sonnet | Django development with ORM and async views |
| [fastapi-pro](agents/fastapi-pro.md) | sonnet | FastAPI with async patterns and Pydantic |
| [unity-developer](agents/unity-developer.md) | sonnet | Unity game development and optimization |
| [minecraft-bukkit-pro](agents/minecraft-bukkit-pro.md) | sonnet | Minecraft server plugin development |
| [sql-pro](agents/sql-pro.md) | sonnet | Complex SQL queries and database optimization |

### Infrastructure & Operations

#### DevOps & Deployment

| Agent | Model | Description |
|-------|-------|-------------|
| [devops-troubleshooter](agents/devops-troubleshooter.md) | sonnet | Production debugging, log analysis, deployment troubleshooting |
| [deployment-engineer](agents/deployment-engineer.md) | sonnet | CI/CD pipelines, containerization, cloud deployments |
| [terraform-specialist](agents/terraform-specialist.md) | opus | Infrastructure as Code with Terraform modules and state management |
| [dx-optimizer](agents/dx-optimizer.md) | sonnet | Developer experience optimization and tooling improvements |

#### Database Management

| Agent | Model | Description |
|-------|-------|-------------|
| [database-optimizer](agents/database-optimizer.md) | opus | Query optimization, index design, migration strategies |
| [database-admin](agents/database-admin.md) | sonnet | Database operations, backup, replication, monitoring |
| [database-architect](agents/database-architect.md) | opus | Database design from scratch, technology selection, schema modeling |

#### Incident Response & Network

| Agent | Model | Description |
|-------|-------|-------------|
| [incident-responder](agents/incident-responder.md) | opus | Production incident management and resolution |
| [network-engineer](agents/network-engineer.md) | sonnet | Network debugging, load balancing, traffic analysis |

### Quality Assurance & Security

#### Code Quality & Review

| Agent | Model | Description |
|-------|-------|-------------|
| [code-reviewer](agents/code-reviewer.md) | opus | Code review with security focus and production reliability |
| [security-auditor](agents/security-auditor.md) | opus | Vulnerability assessment and OWASP compliance |
| [backend-security-coder](agents/backend-security-coder.md) | opus | Secure backend coding practices, API security implementation |
| [frontend-security-coder](agents/frontend-security-coder.md) | opus | XSS prevention, CSP implementation, client-side security |
| [mobile-security-coder](agents/mobile-security-coder.md) | opus | Mobile security patterns, WebView security, biometric auth |

#### Testing & Debugging

| Agent | Model | Description |
|-------|-------|-------------|
| [test-automator](agents/test-automator.md) | sonnet | Comprehensive test suite creation (unit, integration, e2e) |
| [tdd-orchestrator](agents/tdd-orchestrator.md) | sonnet | Test-Driven Development methodology guidance |
| [debugger](agents/debugger.md) | sonnet | Error resolution and test failure analysis |
| [error-detective](agents/error-detective.md) | sonnet | Log analysis and error pattern recognition |

#### Performance & Observability

| Agent | Model | Description |
|-------|-------|-------------|
| [performance-engineer](agents/performance-engineer.md) | opus | Application profiling and optimization |
| [observability-engineer](agents/observability-engineer.md) | opus | Production monitoring, distributed tracing, SLI/SLO management |
| [search-specialist](agents/search-specialist.md) | haiku | Advanced web research and information synthesis |

### Data & AI

#### Data Engineering & Analytics

| Agent | Model | Description |
|-------|-------|-------------|
| [data-scientist](agents/data-scientist.md) | opus | Data analysis, SQL queries, BigQuery operations |
| [data-engineer](agents/data-engineer.md) | sonnet | ETL pipelines, data warehouses, streaming architectures |

#### Machine Learning & AI

| Agent | Model | Description |
|-------|-------|-------------|
| [ai-engineer](agents/ai-engineer.md) | opus | LLM applications, RAG systems, prompt pipelines |
| [ml-engineer](agents/ml-engineer.md) | opus | ML pipelines, model serving, feature engineering |
| [mlops-engineer](agents/mlops-engineer.md) | opus | ML infrastructure, experiment tracking, model registries |
| [prompt-engineer](agents/prompt-engineer.md) | opus | LLM prompt optimization and engineering |

### Documentation & Technical Writing

| Agent | Model | Description |
|-------|-------|-------------|
| [docs-architect](agents/docs-architect.md) | opus | Comprehensive technical documentation generation |
| [api-documenter](agents/api-documenter.md) | sonnet | OpenAPI/Swagger specifications and developer docs |
| [reference-builder](agents/reference-builder.md) | haiku | Technical references and API documentation |
| [tutorial-engineer](agents/tutorial-engineer.md) | sonnet | Step-by-step tutorials and educational content |
| [mermaid-expert](agents/mermaid-expert.md) | sonnet | Diagram creation (flowcharts, sequences, ERDs) |

### Business & Operations

#### Business Analysis & Finance

| Agent | Model | Description |
|-------|-------|-------------|
| [business-analyst](agents/business-analyst.md) | sonnet | Metrics analysis, reporting, KPI tracking |
| [quant-analyst](agents/quant-analyst.md) | opus | Financial modeling, trading strategies, market analysis |
| [risk-manager](agents/risk-manager.md) | sonnet | Portfolio risk monitoring and management |

#### Marketing & Sales

| Agent | Model | Description |
|-------|-------|-------------|
| [content-marketer](agents/content-marketer.md) | sonnet | Blog posts, social media, email campaigns |
| [sales-automator](agents/sales-automator.md) | haiku | Cold emails, follow-ups, proposal generation |

#### Support & Legal

| Agent | Model | Description |
|-------|-------|-------------|
| [customer-support](agents/customer-support.md) | sonnet | Support tickets, FAQ responses, customer communication |
| [hr-pro](agents/hr-pro.md) | opus | HR operations, policies, employee relations |
| [legal-advisor](agents/legal-advisor.md) | opus | Privacy policies, terms of service, legal documentation |

### SEO & Content Optimization

| Agent | Model | Description |
|-------|-------|-------------|
| [seo-content-auditor](agents/seo-content-auditor.md) | sonnet | Content quality analysis, E-E-A-T signals assessment |
| [seo-meta-optimizer](agents/seo-meta-optimizer.md) | haiku | Meta title and description optimization |
| [seo-keyword-strategist](agents/seo-keyword-strategist.md) | haiku | Keyword analysis and semantic variations |
| [seo-structure-architect](agents/seo-structure-architect.md) | haiku | Content structure and schema markup |
| [seo-snippet-hunter](agents/seo-snippet-hunter.md) | haiku | Featured snippet formatting |
| [seo-content-refresher](agents/seo-content-refresher.md) | haiku | Content freshness analysis |
| [seo-cannibalization-detector](agents/seo-cannibalization-detector.md) | haiku | Keyword overlap detection |
| [seo-authority-builder](agents/seo-authority-builder.md) | sonnet | E-E-A-T signal analysis |
| [seo-content-writer](agents/seo-content-writer.md) | sonnet | SEO-optimized content creation |
| [seo-content-planner](agents/seo-content-planner.md) | haiku | Content planning and topic clusters |

### Specialized Domains

| Agent | Model | Description |
|-------|-------|-------------|
| [blockchain-developer](agents/blockchain-developer.md) | sonnet | Web3 apps, smart contracts, DeFi protocols |
| [payment-integration](agents/payment-integration.md) | sonnet | Payment processor integration (Stripe, PayPal) |
| [legacy-modernizer](agents/legacy-modernizer.md) | sonnet | Legacy code refactoring and modernization |
| [context-manager](agents/context-manager.md) | haiku | Multi-agent context management |

## Model Configuration

Agents are assigned to specific Claude models based on task complexity and computational requirements. The system uses three model tiers:

### Model Distribution Summary

| Model | Agent Count | Use Case |
|-------|-------------|----------|
| Haiku | 11 | Quick, focused tasks with minimal computational overhead |
| Sonnet | 50 | Standard development and specialized engineering tasks |
| Opus | 22 | Complex reasoning, architecture, and critical analysis |

### Haiku Model Agents

| Category | Agents |
|----------|--------|
| Context & Reference | `context-manager`, `reference-builder`, `sales-automator`, `search-specialist` |
| SEO Optimization | `seo-meta-optimizer`, `seo-keyword-strategist`, `seo-structure-architect`, `seo-snippet-hunter`, `seo-content-refresher`, `seo-cannibalization-detector`, `seo-content-planner` |

### Sonnet Model Agents

| Category | Count | Agents |
|----------|-------|--------|
| Programming Languages | 18 | All language-specific agents (JavaScript, Python, Java, C++, etc.) |
| Frontend & UI | 5 | `frontend-developer`, `ui-ux-designer`, `ui-visual-validator`, `mobile-developer`, `ios-developer` |
| Infrastructure | 8 | `devops-troubleshooter`, `deployment-engineer`, `dx-optimizer`, `database-admin`, `network-engineer`, `flutter-expert`, `api-documenter`, `tutorial-engineer` |
| Quality & Testing | 4 | `test-automator`, `tdd-orchestrator`, `debugger`, `error-detective` |
| Business & Support | 6 | `business-analyst`, `risk-manager`, `content-marketer`, `customer-support`, `mermaid-expert`, `legacy-modernizer` |
| Data & Content | 5 | `data-engineer`, `payment-integration`, `seo-content-auditor`, `seo-authority-builder`, `seo-content-writer` |

### Opus Model Agents

| Category | Count | Agents |
|----------|-------|--------|
| Architecture & Design | 7 | `architect-reviewer`, `backend-architect`, `cloud-architect`, `hybrid-cloud-architect`, `kubernetes-architect`, `graphql-architect`, `terraform-specialist` |
| Critical Analysis | 6 | `code-reviewer`, `security-auditor`, `performance-engineer`, `observability-engineer`, `incident-responder`, `database-optimizer` |
| AI/ML Complex | 5 | `ai-engineer`, `ml-engineer`, `mlops-engineer`, `data-scientist`, `prompt-engineer` |
| Business Critical | 4 | `docs-architect`, `hr-pro`, `legal-advisor`, `quant-analyst` |

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

### Common Workflow Patterns

**Feature Development**
```
"Implement user authentication"
→ backend-architect → frontend-developer → test-automator → security-auditor
```

**Performance Optimization**
```
"Optimize checkout process"
→ performance-engineer → database-optimizer → frontend-developer
```

**Production Incidents**
```
"Debug high memory usage"
→ incident-responder → devops-troubleshooter → error-detective → performance-engineer
```

**Infrastructure Setup**
```
"Set up disaster recovery"
→ database-admin → database-optimizer → terraform-specialist
```

**ML Pipeline Development**
```
"Build ML pipeline with monitoring"
→ mlops-engineer → ml-engineer → data-engineer → performance-engineer
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

## Contributing

To add new agents, workflows, or tools:

1. Create a new `.md` file in the appropriate directory with frontmatter
2. Use lowercase, hyphen-separated naming convention
3. Write clear activation criteria in the description
4. Define comprehensive system prompt with expertise areas

### Subagent Format

Each subagent is defined as a Markdown file with frontmatter:

```markdown
---
name: subagent-name
description: Activation criteria for this subagent
model: haiku|sonnet|opus  # Optional: Model selection
tools: tool1, tool2       # Optional: Tool restrictions
---

System prompt defining the subagent's expertise and behavior
```

### Model Selection Criteria

- **haiku**: Simple, deterministic tasks with minimal reasoning
- **sonnet**: Standard development and engineering tasks
- **opus**: Complex analysis, architecture, and critical operations

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Resources

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Subagents Documentation](https://docs.anthropic.com/en/docs/claude-code/sub-agents)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)
