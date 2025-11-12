---
name: hoon-to-nock
description: Analyze how Hoon high-level code compiles to Nock assembly for performance optimization and debugging.
subagent_type: nock-fundamentals-tutor
---

# Hoon-to-Nock Command

4-phase workflow for understanding Hoon compilation to Nock, analyzing output, and optimizing performance.

## Phase 1: Hoon Code Preparation

**Goal**: Select Hoon code to analyze

**Tasks**:
1. **Choose target code**:
   - Simple expressions: `(add 2 3)`, `(mul 10 5)`
   - Functions/gates: `|=(n=@ (add n 1))`
   - Control flow: `?:(=(n 0) 'zero' 'nonzero')`
   - Complex patterns: Cores, doors, recursive functions
2. **Verify code compiles**:
   - Test in Urbit dojo or Hoon compiler
   - Ensure no syntax/type errors
3. **Document intent**:
   - What should the code do?
   - What is the expected Nock pattern?

**Success**: Valid Hoon code ready for compilation analysis

---

## Phase 2: Compile to Nock

**Goal**: Generate Nock output from Hoon input

**Methods**:

### Option 1: Urbit Dojo
```hoon
> !:(!,(add 2 3))
[9 2 [[0 6] [1 2 3] 0 7]]  # Actual Nock formula
```

### Option 2: Hoon Compiler (if available)
```bash
# Using hoon development tools
hoon-compile --show-nock myfile.hoon
```

### Option 3: Cross-Plugin Integration
```
# In hoon-development plugin
Write Hoon code → Compile → Show Nock output

# In nock-development plugin
Analyze Nock formula → Optimize → Suggest jets
```

**Tasks**:
1. Compile Hoon to Nock using chosen method
2. Save Nock output for analysis
3. Verify output with simple test execution

**Success**: Nock formula generated from Hoon input

---

## Phase 3: Analyze Nock Output

**Goal**: Understand the compiled Nock formula structure

**Analysis Steps**:
1. **Identify pattern**:
   - Rule 9 (call): Function invocation
   - Rule 6 (if): Conditional logic
   - Rule 8 (push): Variable binding
   - Core structure: `[battery payload]`

2. **Break down formula**:
   ```
   [9 2 [[0 6] [1 2 3] 0 7]]

   Analysis:
     9: Rule 9 (call arm in core)
     2: Axis 2 (invoke arm at axis 2)
     Rest: Subject formula
       [0 6]: Get stdlib from subject
       [1 [2 3]]: Constant arguments [2 3]
       [0 7]: Get context
   ```

3. **Trace execution**:
   - Manually reduce formula step-by-step
   - Verify it matches expected Hoon behavior
   - Identify expensive operations

4. **Map Hoon → Nock**:
   - Which Hoon construct → which Nock pattern?
   - How are variables accessed (slot)?
   - How are functions called (Rule 9)?

**Success**: Complete understanding of Nock output

---

## Phase 4: Optimization Analysis

**Goal**: Identify performance improvements

**Optimization Opportunities**:

1. **Jetting Candidates**:
   - Frequently-called functions (add, mul, dec)
   - Complex loops (factorial, fibonacci)
   - Cryptographic operations (SHA-256)
   - Recommendation: Jet these in native code

2. **Algorithmic Issues**:
   - O(n²) patterns (nested loops)
   - Redundant computations (not memoized)
   - Inefficient data structures (list vs map)
   - Fix in Hoon code, recompile

3. **Compilation Artifacts**:
   - Excessive subject extension (Rule 8)
   - Unnecessary core creation
   - Dead code in battery
   - May need Hoon refactoring

**Tasks**:
1. Profile Nock execution (if possible)
2. Identify hot paths and slow operations
3. Determine if issue is:
   - Hoon algorithm (fix in Hoon)
   - Nock compilation (compiler optimization)
   - Runtime performance (add jets)
4. Recommend specific optimizations

**Success**: Optimization plan with expected speedups

---

## Example: Simple Addition

### Hoon Code
```hoon
(add 2 3)
```

### Compiled Nock
```
[9 2 [[0 6] [1 2 3] 0 7]]
```

### Analysis
```
Rule 9: Call arm in core
  Axis 2: Invoke first arm (the 'add' function)
  Subject:
    [0 6]: Get stdlib core from subject (add is in stdlib)
    [1 [2 3]]: Arguments [2 3]
    [0 7]: Context (unused for add)

Optimization: 'add' is jetted in production Urbit → very fast
```

## Example: Conditional

### Hoon Code
```hoon
?:(=(n 0) 'zero' 'nonzero')
```

### Compiled Nock
```
[6 [5 [0 6] [1 0]] [1 'zero'] [1 'nonzero']]
```

### Analysis
```
Rule 6: If-then-else
  Test: [5 [0 6] [1 0]]  → =(n, 0)
    [0 6]: Get n from subject
    [1 0]: Constant 0
    [5 ...]: Equality test
  Then: [1 'zero']  → Constant 'zero'
  Else: [1 'nonzero']  → Constant 'nonzero'

Optimization: Equality is jetted → fast
```

## Example: Recursive Function

### Hoon Code
```hoon
|=  n=@
?:  =(n 0)
  0
$(n (dec n))  # Tail recursion
```

### Compiled Nock
```
Core structure:
[
  [formula-for-body]  # Battery (single arm)
  n                   # Sample (argument)
]

Body uses Rule 9 (call) in tail position → loop (no stack growth)
Decrement is jetted → fast
```

### Analysis
```
Tail call optimization: No stack growth (safe)
Jetting opportunity: If this is called frequently, jet the whole core
Performance: Good (tail recursive + jetted decrement)
```

## Cross-Plugin Workflow

### Integrated Optimization
```
1. [hoon-development] Write Hoon code
2. [hoon-development] Compile, test functionality
3. [nock-development] /hoon-to-nock → Analyze output
4. [nock-development] Identify slow patterns
5. [hoon-development] Refactor Hoon (if algorithm issue)
6. [nock-development] Add jets (if runtime issue)
7. [both] Benchmark improvements
```

## Next Steps

- Use `/optimize-nock-performance` to implement jets
- Use `/debug-nock-execution` if compilation is wrong
- Use `/build-nock-interpreter` to add jet support
- Collaborate with `hoon-development` plugin for Hoon refactoring

## Resources

- Hoon Compilation: https://developers.urbit.org/reference/hoon
- Nock Specification: https://docs.urbit.org/nock/specification
- Jetting Guide: https://docs.urbit.org/nock/jetting
- Cross-plugin integration: See hoon-development and nock-development READMEs
