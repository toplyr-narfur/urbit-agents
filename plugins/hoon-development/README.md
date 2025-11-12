# Hoon Development Plugin

Master Hoon programming language for Urbit application development through specialized agents, comprehensive skills, and workflow commands.

## Overview

The **hoon-development** plugin provides complete Hoon programming expertise through Claude Code's agent system, covering everything from language fundamentals through production Gall agent development.

### Key Features

- **5 Specialized Agents**: Domain experts for different aspects of Hoon development
- **18 Comprehensive Skills**: Modular knowledge packages from basics to advanced patterns
- **8 Workflow Commands**: Production-ready workflows for common development tasks
- **Progressive Learning**: Structured path from beginner to expert
- **Production Focus**: Real-world patterns and best practices

## Agents

### Core Development Agents

#### `hoon-expert` (Sonnet)
Master Hoon developer specializing in functional programming, type systems, and production-ready Urbit application development.

**Use for:**
- Complex Hoon code implementation
- Architectural decisions
- Performance optimization
- Production application development

**Skills**: hoon-fundamentals, rune-reference, advanced-patterns, stdlib-reference, functional-programming-patterns, type-system, gall-agents

---

#### `hoon-tutor` (Sonnet)
Educational specialist focusing on teaching Hoon through progressive exercises, interactive examples, and hands-on practice.

**Use for:**
- Learning Hoon from scratch
- Understanding complex concepts
- Structured tutorials
- Practice exercises

**Skills**: hoon-basics, hoon-fundamentals, rune-reference, functional-programming-patterns, gall-agents

---

#### `code-reviewer` (Sonnet)
Quality assurance specialist performing comprehensive Hoon code reviews with P0-P3 priority system.

**Use for:**
- Code quality assessment
- Security vulnerability detection
- Performance analysis
- Best practice enforcement

**Skills**: hoon-style-guide, functional-programming-patterns, type-system, advanced-patterns

---

#### `debugging-specialist` (Sonnet)
Error diagnosis expert for systematic debugging of Hoon code from compilation errors through runtime failures.

**Use for:**
- Debugging compilation errors
- Runtime error diagnosis
- Type system issues
- Performance problems

**Skills**: hoon-fundamentals, type-system, gall-agents, stdlib-reference

---

#### `app-architect` (Sonnet)
Gall agent architecture specialist for designing and implementing production Urbit applications.

**Use for:**
- Application architecture design
- Gall agent implementation
- State management patterns
- Production deployment

**Skills**: gall-agents, app-development-workflow, type-system, advanced-patterns, serialization

---

## Skills

Skills are modular knowledge packages activated by agents as needed. All skills include comprehensive examples, code patterns, and best practices.

### Foundation Skills (3)

1. **hoon-basics** - Brief beginner introduction to Hoon
2. **hoon-fundamentals** (2300+ lines) - Subject-oriented programming, noun model, type foundations
3. **rune-reference** - Complete reference for all 90+ runes across 13 families

### Language Skills (5)

4. **type-system** - Structural typing, auras, molds, type casting
5. **functional-programming-patterns** - Pure functions, higher-order functions, recursion
6. **advanced-patterns** - Doors, wet gates, mold builders, metaprogramming
7. **irregular-forms** - Complete irregular syntax reference
8. **hoon-style-guide** - Naming conventions and best practices

### Data Skills (3)

9. **data-structures** - Lists, sets, maps, mops, jars, jugs
10. **string-handling** - Cord, tape, term, knot types
11. **serialization** - JSON, vases, jam/cue, marks

### Library Skills (2)

12. **stdlib-reference** - Comprehensive standard library documentation
13. **zuse-libraries** - HTTP, JSON, crypto, scry utilities

### Application Skills (5)

14. **gall-agents** - Complete Gall agent development with all 10 arms
15. **generators** - Naked, %say, %ask generators for CLI tools
16. **parsing** - Parser combinators and custom parsers
17. **app-development-workflow** - End-to-end development process (see urbit-operations plugin for production deployment)
18. **sail-markup** - HTML templating in Hoon

## Commands

Workflow orchestrators for common development tasks. Each command provides a multi-phase structured approach.

### `/hoon-review`
**Subagent**: code-reviewer
**Purpose**: Comprehensive code review covering quality, security, performance, and maintainability

**5-Phase Workflow:**
1. Initial Assessment - Understand code purpose and structure
2. Correctness and Type Safety - Verify types and logic
3. Security Review - Identify vulnerabilities
4. Performance Analysis - Find bottlenecks
5. Code Quality - Assess readability and maintainability

**Output**: P0-P3 prioritized issues with fixes and examples

---

### `/hoon-optimize`
**Subagent**: hoon-expert
**Purpose**: Systematic performance optimization

**4-Phase Workflow:**
1. Profiling and Measurement - Identify actual bottlenecks
2. Algorithmic Analysis - Improve complexity
3. Data Structure Optimization - Select optimal structures
4. Caching and Memoization - Eliminate redundant work

**Output**: Measured performance improvements with benchmarks

---

### `/hoon-debug`
**Subagent**: debugging-specialist
**Purpose**: Systematic debugging from errors through resolution

**5-Phase Workflow:**
1. Error Classification - Categorize error type
2. Reproduction and Isolation - Create minimal test case
3. Diagnostic Instrumentation - Add targeted debugging
4. Interactive Debugging - Use dojo and runtime tools
5. Error Analysis and Resolution - Understand and fix root cause

**Output**: Root cause identification and fix implementation

---

### `/hoon-refactor`
**Subagent**: hoon-expert
**Purpose**: Safe code refactoring while preserving correctness

**6-Phase Workflow:**
1. Assessment and Planning - Identify refactoring opportunities
2. Extract Functions - Break large functions into pieces
3. Eliminate Duplication - Remove repeated code
4. Improve Structure - Reorganize for clarity
5. Enhance Readability - Make intention clear
6. Verify and Commit - Ensure behavior preserved

**Output**: Improved code structure with passing tests

---

### `/hoon-test`
**Subagent**: hoon-expert
**Purpose**: Comprehensive testing workflow

**5-Phase Workflow:**
1. Test Planning - Identify what to test
2. Unit Testing - Test individual functions
3. Integration Testing - Test Gall agents
4. Property-Based Testing - Verify invariants
5. Test-Driven Development (TDD) - Red-Green-Refactor

**Output**: Complete test suite with high coverage

---

### `/hoon-learn`
**Subagent**: hoon-tutor
**Purpose**: Interactive learning path for mastering Hoon

**6-Level Path:**
1. Hoon Fundamentals (Day 1-3) - Basics and syntax
2. Control Flow and Functions (Day 4-7) - Conditionals and gates
3. Data Structures (Day 8-12) - Lists, maps, sets
4. Type System (Day 13-17) - Molds and tagged unions
5. Gall Agents (Day 18-25) - Stateful applications
6. Advanced Patterns (Day 26-30) - Doors, wet gates, parsers

**Output**: Hands-on exercises with solutions

---

### `/hoon-migrate`
**Subagent**: app-architect
**Purpose**: Safe state migration for Gall agents

**5-Phase Workflow:**
1. Version Planning - Plan migration strategy
2. Migration Implementation - Write migration code
3. Validation and Safety - Ensure data integrity
4. Testing Strategy - Verify all paths work
5. Deployment and Monitoring - Deploy safely

**Output**: Zero-downtime, zero-data-loss migrations

---

### `/hoon-scaffold`
**Subagent**: app-architect
**Purpose**: Bootstrap new Urbit projects

**6-Phase Workflow:**
1. Project Planning - Define scope and architecture
2. Desk Setup - Create desk with required files
3. Type Definition (sur/) - Define types before implementation
4. Library Implementation (lib/) - Implement business logic
5. Agent Scaffolding (app/) - Create Gall agent structure
6. Supporting Files - Add marks, generators, docs

**Output**: Well-structured project ready for development

---

## Related Plugins

### urbit-operations

After building your Hoon applications with **hoon-development**, deploy them to production infrastructure with the **urbit-operations** plugin.

**Workflow**: Learn Hoon → Build Gall Agents → Deploy Infrastructure

**urbit-operations provides:**
- Bare-metal and VPS deployment (`/deploy-planet`, `/deploy-vps-planet`)
- Multi-ship fleet management (`/deploy-groundseg`, `/deploy-fleet`)
- Production hardening and monitoring (`/setup-production`, `/setup-monitoring`)
- Troubleshooting and performance optimization
- Development environment setup (fake ships, local testing)

**When to use urbit-operations:**
- After scaffolding a new project with `/hoon-scaffold`
- When deploying Gall agents to production
- For setting up local development environments
- When managing fleets of Urbit ships

See: `/plugin install urbit-operations`

---

## Installation

```bash
# In Claude Code, install the plugin
/install hoon-development

# Verify installation
/plugins
```

## Quick Start

### Learning Hoon
```bash
# Start interactive learning
/hoon-learn

# Get help with specific concept
"Explain how the subject works in Hoon"
```

### Building an Application
```bash
# Scaffold new project
/hoon-scaffold

# Implement features with hoon-expert agent
"Build a Gall agent that tracks counter state"

# Review code
/hoon-review

# Test thoroughly
/hoon-test

# Deploy to production (requires urbit-operations plugin)
/deploy-vps-planet  # or /deploy-planet for bare-metal
```

### Deploying to Production

Once your application is built and tested, use the **urbit-operations** plugin to deploy:

```bash
# Install urbit-operations plugin
/plugin install urbit-operations

# Deploy to VPS
/deploy-vps-planet

# Or deploy to bare-metal
/deploy-planet

# Harden production security
/setup-production
```

### Debugging Issues
```bash
# Debug errors
/hoon-debug

# Optimize performance
/hoon-optimize
```

### State Migration
```bash
# Plan and execute migration
/hoon-migrate
```

## Usage Examples

### Example 1: Learning Hoon Basics

```
User: I want to learn Hoon from scratch