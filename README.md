# Claude Code Workflows & Agents

A comprehensive production-ready system combining **84 specialized AI agents**, **15 multi-agent workflow orchestrators**, and **44 development tools** organized into **62 focused, single-purpose plugins** for [Claude Code](https://docs.claude.com/en/docs/claude-code/overview).

## Overview

This unified repository provides everything needed for intelligent automation and multi-agent orchestration across modern software development:

- **62 Focused Plugins** - Granular, single-purpose plugins optimized for minimal token usage and composability
- **84 Specialized Agents** - Domain experts with deep knowledge across architecture, languages, infrastructure, quality, data/AI, documentation, business operations, and SEO
- **15 Workflow Orchestrators** - Multi-agent coordination systems for complex operations like full-stack development, security hardening, ML pipelines, and incident response
- **44 Development Tools** - Optimized utilities including project scaffolding, security scanning, test automation, and infrastructure setup

### Version 1.1.0 Highlights

- **Marketplace Refactored**: 36 plugins → 62 focused plugins (+72% granularity)
- **Tools Expanded**: 6 additional tools (test generation, component scaffolding, XSS scanning, project scaffolding for Python/TypeScript/Rust)
- **100% Agent Coverage**: All plugins have at least one agent
- **Better Discoverability**: 23 clear categories with 1-6 plugins each
- **Optimized Architecture**: Average 3.4 components per plugin (follows Anthropic's 2-8 pattern)

## Installation

Add this marketplace to Claude Code:

```bash
/plugin marketplace add https://github.com/wshobson/agents
```

Then browse and install plugins using:

```bash
/plugin
```

## Quick Start - Essential Plugins

> 💡 **Getting Started?** Install these popular plugins for immediate productivity gains.

### Development Essentials

**code-documentation** - Documentation and technical writing
```bash
/plugin install code-documentation
```
Automated doc generation, code explanation, and tutorial creation for comprehensive technical documentation.

**debugging-toolkit** - Smart debugging and developer experience
```bash
/plugin install debugging-toolkit
```
Interactive debugging, error analysis, and DX optimization for faster problem resolution.

**git-pr-workflows** - Git automation and PR enhancement
```bash
/plugin install git-pr-workflows
```
Git workflow automation, pull request enhancement, and team onboarding processes.

### Full-Stack Development

**backend-development** - Backend API design and architecture
```bash
/plugin install backend-development
```
RESTful and GraphQL API design with test-driven development and modern backend architecture patterns.

**frontend-mobile-development** - UI and mobile development
```bash
/plugin install frontend-mobile-development
```
React/React Native component development with automated scaffolding and cross-platform implementation.

**full-stack-orchestration** - End-to-end feature development
```bash
/plugin install full-stack-orchestration
```
Multi-agent coordination from backend → frontend → testing → security → deployment.

### Testing & Quality

**unit-testing** - Automated test generation
```bash
/plugin install unit-testing
```
Generate pytest (Python) and Jest (JavaScript) unit tests automatically with comprehensive edge case coverage.

**code-review-ai** - AI-powered code review
```bash
/plugin install code-review-ai
```
Architectural analysis, security assessment, and code quality review with actionable feedback.

### Infrastructure & Operations

**cloud-infrastructure** - Cloud architecture design
```bash
/plugin install cloud-infrastructure
```
AWS/Azure/GCP architecture, Kubernetes setup, Terraform IaC, and multi-cloud cost optimization.

**incident-response** - Production incident management
```bash
/plugin install incident-response
```
Rapid incident triage, root cause analysis, and automated resolution workflows for production systems.

### Language Support

**python-development** - Python project scaffolding
```bash
/plugin install python-development
```
FastAPI/Django project initialization with modern tooling (uv, ruff) and production-ready architecture.

**javascript-typescript** - JavaScript/TypeScript scaffolding
```bash
/plugin install javascript-typescript
```
Next.js, React + Vite, and Node.js project setup with pnpm and TypeScript best practices.

---

## Complete Plugin Reference

> 📋 **All 62 Plugins** - Browse by category to find specialized plugins for your workflow.

### 🎨 Development (4 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **debugging-toolkit** | Interactive debugging and DX optimization | `/plugin install debugging-toolkit` |
| **backend-development** | Backend API design with GraphQL and TDD | `/plugin install backend-development` |
| **frontend-mobile-development** | Frontend UI and mobile development | `/plugin install frontend-mobile-development` |
| **multi-platform-apps** | Cross-platform app coordination (web/iOS/Android) | `/plugin install multi-platform-apps` |

### 📚 Documentation (2 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **code-documentation** | Documentation generation and code explanation | `/plugin install code-documentation` |
| **documentation-generation** | OpenAPI specs, Mermaid diagrams, tutorials | `/plugin install documentation-generation` |

### 🔄 Workflows (3 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **git-pr-workflows** | Git automation and PR enhancement | `/plugin install git-pr-workflows` |
| **full-stack-orchestration** | End-to-end feature orchestration | `/plugin install full-stack-orchestration` |
| **tdd-workflows** | Test-driven development methodology | `/plugin install tdd-workflows` |

### ✅ Testing (1 plugin)

| Plugin | Description | Install |
|--------|-------------|---------|
| **unit-testing** | Automated unit test generation (Python/JavaScript) | `/plugin install unit-testing` |

### 🔍 Quality (3 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **code-review-ai** | AI-powered architectural review | `/plugin install code-review-ai` |
| **comprehensive-review** | Multi-perspective code analysis | `/plugin install comprehensive-review` |
| **performance-testing-review** | Performance analysis and test coverage review | `/plugin install performance-testing-review` |

### 🛠️ Utilities (4 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **code-refactoring** | Code cleanup and technical debt management | `/plugin install code-refactoring` |
| **dependency-management** | Dependency auditing and version management | `/plugin install dependency-management` |
| **error-debugging** | Error analysis and trace debugging | `/plugin install error-debugging` |
| **team-collaboration** | Team workflows and standup automation | `/plugin install team-collaboration` |

### 🤖 AI & ML (4 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **llm-application-dev** | LLM apps and prompt engineering | `/plugin install llm-application-dev` |
| **agent-orchestration** | Multi-agent system optimization | `/plugin install agent-orchestration` |
| **context-management** | Context persistence and restoration | `/plugin install context-management` |
| **machine-learning-ops** | ML training pipelines and MLOps | `/plugin install machine-learning-ops` |

### 📊 Data (2 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **data-engineering** | ETL pipelines and data warehouses | `/plugin install data-engineering` |
| **data-validation-suite** | Schema validation and data quality | `/plugin install data-validation-suite` |

### 🗄️ Database (2 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **database-design** | Database architecture and schema design | `/plugin install database-design` |
| **database-migrations** | Database migration automation | `/plugin install database-migrations` |

### 🚨 Operations (4 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **incident-response** | Production incident management | `/plugin install incident-response` |
| **error-diagnostics** | Error tracing and root cause analysis | `/plugin install error-diagnostics` |
| **distributed-debugging** | Distributed system tracing | `/plugin install distributed-debugging` |
| **observability-monitoring** | Metrics, logging, tracing, and SLO | `/plugin install observability-monitoring` |

### ⚡ Performance (2 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **application-performance** | Application profiling and optimization | `/plugin install application-performance` |
| **database-cloud-optimization** | Database query and cloud cost optimization | `/plugin install database-cloud-optimization` |

### ☁️ Infrastructure (5 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **deployment-strategies** | Deployment patterns and rollback automation | `/plugin install deployment-strategies` |
| **deployment-validation** | Pre-deployment checks and validation | `/plugin install deployment-validation` |
| **kubernetes-operations** | K8s manifests and GitOps workflows | `/plugin install kubernetes-operations` |
| **cloud-infrastructure** | AWS/Azure/GCP cloud architecture | `/plugin install cloud-infrastructure` |
| **cicd-automation** | CI/CD pipeline configuration | `/plugin install cicd-automation` |

### 🔒 Security (4 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **security-scanning** | SAST analysis and vulnerability scanning | `/plugin install security-scanning` |
| **security-compliance** | SOC2/HIPAA/GDPR compliance | `/plugin install security-compliance` |
| **backend-api-security** | API security and authentication | `/plugin install backend-api-security` |
| **frontend-mobile-security** | XSS/CSRF prevention and mobile security | `/plugin install frontend-mobile-security` |

### 🔄 Modernization (2 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **framework-migration** | Framework upgrades and migration planning | `/plugin install framework-migration` |
| **codebase-cleanup** | Technical debt reduction and cleanup | `/plugin install codebase-cleanup` |

### 🌐 API (2 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **api-scaffolding** | REST/GraphQL API generation | `/plugin install api-scaffolding` |
| **api-testing-observability** | API testing and monitoring | `/plugin install api-testing-observability` |

### 📢 Marketing (4 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **seo-content-creation** | SEO content writing and planning | `/plugin install seo-content-creation` |
| **seo-technical-optimization** | Meta tags, keywords, and schema markup | `/plugin install seo-technical-optimization` |
| **seo-analysis-monitoring** | Content analysis and authority building | `/plugin install seo-analysis-monitoring` |
| **content-marketing** | Content strategy and web research | `/plugin install content-marketing` |

### 💼 Business (3 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **business-analytics** | KPI tracking and financial reporting | `/plugin install business-analytics` |
| **hr-legal-compliance** | HR policies and legal templates | `/plugin install hr-legal-compliance` |
| **customer-sales-automation** | Support and sales automation | `/plugin install customer-sales-automation` |

### 💻 Languages (6 plugins)

| Plugin | Description | Install |
|--------|-------------|---------|
| **python-development** | Python 3.12+ with Django/FastAPI | `/plugin install python-development` |
| **javascript-typescript** | JavaScript/TypeScript with Node.js | `/plugin install javascript-typescript` |
| **systems-programming** | Rust, Go, C, C++ for systems development | `/plugin install systems-programming` |
| **jvm-languages** | Java, Scala, C# with enterprise patterns | `/plugin install jvm-languages` |
| **web-scripting** | PHP and Ruby for web applications | `/plugin install web-scripting` |
| **functional-programming** | Elixir with OTP and Phoenix | `/plugin install functional-programming` |

### 🔗 Blockchain (1 plugin)

| Plugin | Description | Install |
|--------|-------------|---------|
| **blockchain-web3** | Smart contracts and DeFi protocols | `/plugin install blockchain-web3` |

### 💰 Finance (1 plugin)

| Plugin | Description | Install |
|--------|-------------|---------|
| **quantitative-trading** | Algorithmic trading and risk management | `/plugin install quantitative-trading` |

### 💳 Payments (1 plugin)

| Plugin | Description | Install |
|--------|-------------|---------|
| **payment-processing** | Stripe/PayPal integration and billing | `/plugin install payment-processing` |

### 🎮 Gaming (1 plugin)

| Plugin | Description | Install |
|--------|-------------|---------|
| **game-development** | Unity and Minecraft plugin development | `/plugin install game-development` |

### ♿ Accessibility (1 plugin)

| Plugin | Description | Install |
|--------|-------------|---------|
| **accessibility-compliance** | WCAG auditing and inclusive design | `/plugin install accessibility-compliance` |

## Repository Structure

```
claude-agents/
├── .claude-plugin/
│   └── marketplace.json          # 62 focused plugins (v1.1.0)
├── agents/                        # 84 specialized AI agents
│   ├── backend-architect.md
│   ├── frontend-developer.md
│   ├── python-pro.md
│   └── ... (all agent definitions)
├── workflows/                     # 15 multi-agent orchestrators
│   ├── feature-development.md
│   ├── full-stack-feature.md
│   ├── security-hardening.md
│   └── ... (workflow commands)
├── tools/                         # 44 development tools
│   ├── test-generate.md          # Automated test generation
│   ├── component-scaffold.md     # React/RN scaffolding
│   ├── xss-scan.md               # XSS vulnerability scanner
│   ├── python-scaffold.md        # Python project init
│   ├── typescript-scaffold.md    # TypeScript project init
│   ├── rust-project.md           # Rust project init
│   └── ... (other tools)
└── README.md                      # This file
```

## Usage

### Agent Invocation

After installing plugins, agents are automatically available. Invoke them explicitly in natural language:

```
"Use backend-architect to design the authentication API"
"Have security-auditor scan for OWASP vulnerabilities"
"Get performance-engineer to optimize this database query"
```

Claude Code automatically selects and coordinates the appropriate agents based on your request.

### Multi-Agent Orchestration Examples

Plugins provide pre-configured multi-agent workflows that coordinate complex operations:

**Full-Stack Development Plugin**
```
"Implement user dashboard with real-time analytics"
```
→ backend-architect → graphql-architect → frontend-developer → mobile-developer → test-automator → security-auditor → performance-engineer → deployment-engineer

**Security Hardening Plugin**
```
"Perform security audit and implement OWASP best practices"
```
→ security-auditor → backend-security-coder → frontend-security-coder → mobile-security-coder → test-automator

**Data/ML Pipeline Plugin**
```
"Build customer churn prediction model with deployment"
```
→ data-scientist → data-engineer → ml-engineer → mlops-engineer → ai-engineer → performance-engineer

**Incident Response Plugin**
```
"Debug production memory leak and create runbook"
```
→ incident-responder → devops-troubleshooter → debugger → error-detective → observability-engineer

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
| [terraform-specialist](agents/terraform-specialist.md) | sonnet | Infrastructure as Code with Terraform modules and state management |
| [dx-optimizer](agents/dx-optimizer.md) | sonnet | Developer experience optimization and tooling improvements |

#### Database Management

| Agent | Model | Description |
|-------|-------|-------------|
| [database-optimizer](agents/database-optimizer.md) | sonnet | Query optimization, index design, migration strategies |
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
| Opus | 23 | Complex reasoning, architecture, and critical analysis |

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
| Infrastructure | 10 | `devops-troubleshooter`, `deployment-engineer`, `terraform-specialist`, `dx-optimizer`, `database-admin`, `database-optimizer`, `network-engineer`, `flutter-expert`, `api-documenter`, `tutorial-engineer` |
| Quality & Testing | 4 | `test-automator`, `tdd-orchestrator`, `debugger`, `error-detective` |
| Business & Support | 6 | `business-analyst`, `risk-manager`, `content-marketer`, `customer-support`, `mermaid-expert`, `legacy-modernizer` |
| Data & Content | 5 | `data-engineer`, `payment-integration`, `seo-content-auditor`, `seo-authority-builder`, `seo-content-writer` |

### Opus Model Agents

| Category | Count | Agents |
|----------|-------|--------|
| Architecture & Design | 5 | `backend-architect`, `cloud-architect`, `hybrid-cloud-architect`, `kubernetes-architect`, `database-architect` |
| Security & Code Review | 4 | `code-reviewer`, `security-auditor`, `backend-security-coder`, `frontend-security-coder`, `mobile-security-coder` |
| Performance & Observability | 3 | `performance-engineer`, `observability-engineer`, `incident-responder` |
| AI/ML Complex | 5 | `ai-engineer`, `ml-engineer`, `mlops-engineer`, `data-scientist`, `prompt-engineer` |
| Business Critical | 5 | `docs-architect`, `hr-pro`, `legal-advisor`, `quant-analyst`, `risk-manager` |

## Architecture & Design Principles

### Version 1.1.0 Refactoring

This marketplace follows industry best practices with a focus on granularity, composability, and minimal token usage:

#### Single Responsibility Principle
- Each plugin does **one thing well** (Unix philosophy)
- Clear, focused purposes (describable in 5-10 words)
- Average plugin size: **3.4 components** (follows Anthropic's 2-8 pattern)
- **Zero bloated plugins** - all plugins focused and purposeful

#### Granular Plugin Architecture
- **36 plugins → 62 plugins** (+72% more granular)
- **23 clear categories** with 1-6 plugins each for easy discovery
- Split into focused domains:
  - **Development**: 4 plugins (debugging, backend, frontend, multi-platform)
  - **Security**: 4 plugins (scanning, compliance, backend-api, frontend-mobile)
  - **Operations**: 4 plugins (incident, diagnostics, distributed, observability)
  - **Languages**: 6 plugins (Python, JS/TS, systems, JVM, scripting, functional)
  - **Infrastructure**: 5 plugins (deployment, validation, K8s, cloud, CI/CD)
  - And 18 more specialized categories

#### Tools & Capabilities (v1.1.0)
- **44 development tools** including:
  - `test-generate.md` - Automated unit test generation (pytest/Jest)
  - `component-scaffold.md` - React/React Native scaffolding
  - `xss-scan.md` - XSS vulnerability scanner with secure patterns
  - `python-scaffold.md` - Python project scaffolding (FastAPI/Django)
  - `typescript-scaffold.md` - TypeScript project scaffolding (Next.js/Vite)
  - `rust-project.md` - Rust project scaffolding (cargo/Axum)
- **100% agent coverage** - all plugins have at least one agent
- **Language-specific plugins** - 6 dedicated plugins for language experts

#### Performance & Quality
- **Optimized token usage** - smaller plugins load faster
- **Better context efficiency** - granular plugins reduce unnecessary context
- **Improved discoverability** - clear categories and focused purposes
- **Component reuse** - shared agents/tools across related plugins
- **100% component coverage** - all 84 agents utilized

### Design Philosophy

**Composability Over Bundling**
- Mix and match plugins based on needs
- Workflow orchestrators compose focused plugins
- No forced feature bundling

**Context Efficiency**
- Smaller tools = faster processing
- Better fit in LLM context windows
- More accurate, focused responses

**Maintainability**
- Single-purpose = easier updates
- Clear boundaries = isolated changes
- Less duplication = simpler maintenance

**Discoverability**
- Clear plugin names convey purpose
- Logical categorization
- Easy to find the right tool


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

- [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code/overview)
- [Claude Code Plugins Guide](https://docs.claude.com/en/docs/claude-code/plugins)
- [Subagents Documentation](https://docs.claude.com/en/docs/claude-code/sub-agents)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)
