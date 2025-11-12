---
# Nock Development Plugin

Production-ready Nock assembly language expertise for building interpreters, optimizing performance, and understanding Urbit's computational substrate.

## Overview

The **nock-development** plugin provides complete Nock assembly language mastery through Claude Code's agent system, covering everything from fundamentals through production interpreter implementation and optimization.

### Key Features

- **4 Specialized Agents**: Expert assistance for interpretation, optimization, specification, and education
- **6 Operational Commands**: Production workflows from learning to building to optimizing
- **12 Comprehensive Skills**: Modular knowledge from nouns to jets to Hoon compilation
- **Implementation Focus**: Built for engineers building Nock interpreters and optimizing performance
- **Cross-Referenced**: Heavy integration with hoon-development plugin for Hoon→Nock analysis

## Agents

### Implementation Specialists

#### `nock-interpreter-engineer` (Sonnet)
Expert Nock interpreter builder specializing in implementing Nock virtual machines across multiple programming languages.

**Use for:**
- Building Nock interpreters from scratch
- Porting Nock to new languages (C, Python, Rust, Haskell, JavaScript)
- Understanding runtime behavior and evaluation loops
- Implementing pattern matching and tree operations

**Skills**: nock-interpreter-patterns, nock-multi-language-implementations, nock-specification-reference, nock-essentials, nock-operators, nock-instructions

---

#### `nock-optimization-specialist` (Sonnet)
Performance tuning expert specializing in Nock optimization, jetting, profiling, and runtime acceleration.

**Use for:**
- Optimizing Nock execution speed (10x-1000x improvements)
- Implementing jets (native code acceleration)
- Profiling performance bottlenecks
- Achieving production-grade throughput (1M+ ops/sec)

**Skills**: nock-jetting-optimization, nock-performance-profiling, nock-metacircular-evaluation, nock-specification-reference, nock-interpreter-patterns

---

#### `nock-specification-expert` (Haiku)
Formal semantics expert specializing in Nock specification, operator definitions, and canonical behavior.

**Use for:**
- Understanding formal Nock behavior
- Resolving edge cases and crash conditions
- Validating interpreter correctness
- Teaching specification details

**Skills**: nock-specification-reference, nock-operators, nock-instructions, nock-essentials

---

#### `nock-fundamentals-tutor` (Sonnet)
Educational specialist teaching Nock fundamentals to implementers through hands-on exercises and progressive learning.

**Use for:**
- Learning Nock from scratch
- Understanding core concepts (atoms, cells, trees, cores)
- Preparing to build interpreters
- Teaching Nock to others

**Skills**: nock-essentials, nock-tree-addressing, nock-cores-arms-batteries, nock-operators, nock-instructions, nock-hoon-compilation

---

## Skills

Skills are modular knowledge packages activated by agents as needed. All skills include comprehensive examples, implementation patterns, and best practices.

### Foundation (3 skills)

1. **nock-essentials** - Nouns (atoms/cells), reduction model, evaluation semantics, basic data representation
2. **nock-operators** - Complete reference for 6 operators (?, +, =, /, #, *) with formal semantics
3. **nock-instructions** - All 13 reduction rules (0-12) with examples and implementation patterns

### Implementation (2 skills)

4. **nock-interpreter-patterns** - Design patterns for building Nock VMs: evaluation loops, error handling, TCO, memory management
5. **nock-multi-language-implementations** - Language-specific patterns across C, Python, Rust, Haskell, JavaScript

### Optimization (2 skills)

6. **nock-jetting-optimization** - Jet acceleration system: hint processing, cold/hot/warm states, native code acceleration
7. **nock-performance-profiling** - Performance analysis techniques: CPU/memory profiling, benchmarking, bottleneck identification

### Advanced (3 skills)

8. **nock-tree-addressing** - Binary tree addressing: axis encoding, slot calculation, efficient tree traversal
9. **nock-cores-arms-batteries** - Core structure pattern: batteries (code), payloads (data), arm invocation
10. **nock-metacircular-evaluation** - Self-interpretation patterns: +mock (Nock-in-Nock), virtualization, sandboxing

### Integration & Reference (2 skills)

11. **nock-hoon-compilation** - How Hoon compiles to Nock: AST transformations, core generation, optimization analysis
12. **nock-specification-reference** - Complete formal Nock 4K specification: all rules, pseudo-functions, crash conditions

## Commands

Workflow orchestrators for common development tasks. Each command provides a multi-phase structured approach.

### `/build-nock-interpreter`
**Subagent**: nock-interpreter-engineer
**Purpose**: Step-by-step workflow for building production-ready Nock interpreters

**8-Phase Workflow:**
1. Language Selection - Choose implementation language and libraries
2. Parser Setup - Parse Nock notation to internal data structures
3. Evaluator Core - Implement all 13 reduction rules
4. Tree Operations - Optimize slot (tree addressing) performance
5. Operator Implementation - Implement auxiliary operators (?, +, =, /)
6. Testing - Validate against specification (unit, integration, property-based)
7. Optimization - Profile and tune for 10x+ performance improvement
8. Documentation - API docs, architecture notes, usage examples

---

### `/optimize-nock-performance`
**Subagent**: nock-optimization-specialist
**Purpose**: Systematic performance optimization from profiling through production deployment

**6-Phase Workflow:**
1. Profiling and Measurement - Establish baseline, identify bottlenecks
2. Bottleneck Identification - Analyze and prioritize optimization opportunities
3. Jetting Opportunities - Identify formulas for native code acceleration
4. Jet Implementation - Build native jets with cold/warm/hot validation
5. Benchmarking - Measure improvements, validate correctness
6. Production Validation - Deploy with monitoring, continuous performance tracking

---

### `/learn-nock-fundamentals`
**Subagent**: nock-fundamentals-tutor
**Purpose**: Interactive learning path for mastering Nock from nouns through cores

**5-Phase Workflow:**
1. Atoms, Cells, and Nouns - Master the only data type
2. Tree Addressing and Slot - Navigate binary trees using axes
3. Operators and Instructions - Understand 6 operators and 13 rules
4. Composition and Control Flow - Master advanced reduction rules
5. Cores, Batteries, and Arms - Understand function/object pattern

---

### `/debug-nock-execution`
**Subagent**: nock-specification-expert
**Purpose**: Systematic debugging from error analysis to resolution

**5-Phase Workflow:**
1. Error Classification - Categorize error (crash, wrong result, infinite loop, performance)
2. Formula Analysis - Understand formula structure and intended execution
3. Step-by-Step Trace - Execute manually to find divergence point
4. Root Cause Analysis - Identify why error occurs
5. Fix and Validate - Implement fix, test, document

---

### `/hoon-to-nock`
**Subagent**: nock-fundamentals-tutor
**Purpose**: Analyze how Hoon compiles to Nock for optimization and debugging

**4-Phase Workflow:**
1. Hoon Code Preparation - Select target Hoon code for analysis
2. Compile to Nock - Generate Nock output (using dojo or compiler)
3. Analyze Nock Output - Understand compiled formula structure
4. Optimization Analysis - Identify performance improvements (jetting, algorithm, compilation)

---

### `/nock-implement-exercise`
**Subagent**: nock-interpreter-engineer
**Purpose**: Hands-on guided exercises from increment through decrement challenge

**6-Phase Workflow:**
1. Basic Operations - Identity, constant, increment
2. Tree Navigation - Head/tail, deep navigation, axis calculation
3. Conditionals - If-then-else, min of two numbers
4. Recursion and Loops - Factorial, list length
5. Cores and Arms - Counter core, calculator core
6. Decrement Challenge - Implement decrement using only increment/equality

---

## Related Plugins

### hoon-development

Build Hoon applications with **hoon-development**, then understand their compiled Nock output with **nock-development**.

**Workflow**: Learn Hoon → Build Gall Agents → Analyze Compiled Nock → Optimize Performance

**Cross-plugin usage:**
- **hoon-development**: Write Hoon code, compile to Nock
- **nock-development**: Analyze Nock formulas, identify bottlenecks, suggest jets
- **Integration**: Use `/hoon-to-nock` command to bridge both plugins

**When to use nock-development:**
- After compiling Hoon code, analyze performance
- Understand how Hoon constructs map to Nock primitives
- Optimize slow Hoon code by analyzing underlying Nock
- Build Nock interpreters for Urbit runtime

See: `/plugin install hoon-development`

---

## Installation

```bash
# In Claude Code, install the plugin
/plugin install nock-development

# Verify installation
/plugins
```

## Quick Start

### Building Your First Interpreter

```bash
# Learn Nock fundamentals
/learn-nock-fundamentals

# Build interpreter in your chosen language
/build-nock-interpreter

# Optimize for production
/optimize-nock-performance
```

### Hands-On Learning

```bash
# Progressive exercises
/nock-implement-exercise

# Start with basics, work up to decrement challenge
```

### Performance Optimization

```bash
# Profile existing interpreter
/optimize-nock-performance

# Add jets for 100-1000x speedup
```

### Hoon→Nock Analysis

```bash
# Analyze how Hoon compiles to Nock
/hoon-to-nock

# Identify optimization opportunities
```

## Usage Examples

### Example 1: Building a Python Nock Interpreter

```
User: I want to build a Nock interpreter in Python

Claude: I'll help you build a production-ready Nock interpreter in Python. Let me activate the nock-interpreter-engineer agent.

[Agent activates nock-interpreter-patterns, nock-multi-language-implementations, nock-specification-reference skills]

Recommended approach for Python:
- Use dataclasses for Noun type (Atom, Cell)
- Native int for atoms (already arbitrary precision)
- functools.lru_cache for memoization
- Pattern matching via if/elif (simple and clear)

Let's begin with the data structures...

[Guides through complete implementation]
```

### Example 2: Optimizing Slow Nock Execution

```
User: My Nock interpreter is too slow, how do I optimize it?

Claude: I'll help optimize your Nock implementation. Let me activate the nock-optimization-specialist agent.

[Agent activates nock-jetting-optimization, nock-performance-profiling skills]

First, let's profile to identify bottlenecks:
1. Use cProfile (Python) to find hot paths
2. Likely culprits: slot (40-60%), allocation (20-30%)

Optimization strategy:
1. Memoize slot lookups (10-100x speedup)
2. Use iterative slot instead of recursive (2-5x)
3. Jet decrement formula (1000x+ speedup)

Let me guide you through each optimization...
```

### Example 3: Understanding Hoon Compilation

```
User: How does this Hoon code compile to Nock: (add 2 3)?

Claude: I'll analyze how Hoon compiles to Nock. Let me activate the nock-fundamentals-tutor agent.

[Agent activates nock-hoon-compilation skill]

The Hoon expression (add 2 3) compiles to:
[9 2 [[0 6] [1 2 3] 0 7]]

Breakdown:
  9: Rule 9 (call arm in core)
  2: Axis 2 (invoke first arm)
  Subject formula:
    [0 6]: Get stdlib core (add is in stdlib)
    [1 [2 3]]: Arguments [2 3]
    [0 7]: Context

Optimization: 'add' is jetted in production Urbit → O(1) native addition instead of O(n) Nock increments.

Would you like me to show how to implement this jet?
```

### Example 4: Debugging Formula Execution

```
User: This Nock formula crashes: *[42 [0 0]]

Claude: I'll help debug this crash. Let me activate the nock-specification-expert agent.

[Agent activates nock-specification-reference skill]

Diagnosis:
Formula: [0 0]
Rule: Rule 0 (slot/tree addressing)
Operation: /[0 42]  # Slot with axis 0

Problem: Axis 0 is ALWAYS forbidden in Nock specification.

Why: Axis numbering starts at 1 (root/identity). Axis 0 has no defined meaning and always crashes.

Fix: Use axis 1 for identity:
  *[42 [0 1]] → 42

This is a specification requirement, not an implementation bug.
```

## Architecture Overview

### Nock Development Models (Progressive Complexity)

1. **Learning** (Days 1-5)
   - Understand nouns, operators, rules
   - Trace formulas manually
   - Build mental model

2. **Simple Interpreter** (Days 6-15)
   - Implement in chosen language
   - Pattern matching on rules
   - Basic tests passing

3. **Optimized Interpreter** (Days 16-30)
   - Memoization, iterative algorithms
   - 10x+ performance improvement
   - Comprehensive test suite

4. **Production Runtime** (Days 31-60)
   - Jetting (100-1000x speedup)
   - Cold/warm/hot state management
   - Production monitoring

5. **Urbit Integration** (Days 61+)
   - Full Urbit runtime compatibility
   - All stdlib jets implemented
   - Contributing to urbit/urbit

## Support

- **Nock Documentation**: [Urbit Nock Docs](https://docs.urbit.org/nock/)
- **Specification**: [Nock 4K Spec](https://docs.urbit.org/nock/specification)
- **Community Implementations**: [Reference Impls](https://docs.urbit.org/nock/implementations)
- **Urbit Developer Resources**: [developers.urbit.org](https://developers.urbit.org)
- **Issues**: [GitHub repository](https://github.com/toplyr-narfur/urbit-agents/issues)

## Contributing

This plugin is maintained as part of the urbit-agents marketplace fork. Contributions welcome!

## License

MIT
