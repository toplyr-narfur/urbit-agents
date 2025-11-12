---
name: nock-hoon-compilation
description: Understanding how Hoon high-level code compiles to Nock assembly. Covers compilation patterns, AST transformations, core generation, and analyzing compiler output. Use when debugging Hoon performance, understanding Hoon→Nock mapping, or optimizing compiled code.
---

# Nock-Hoon Compilation

How Hoon high-level functional language compiles down to Nock assembly.

## Compilation Overview

### The Pipeline
```
Hoon source → Parser → AST → Type checking → Nock generation

Example:
  Hoon: (add 2 3)
  AST: [%cnhp %add [%sand 2] [%sand 3]]
  Nock: [9 axis-for-add [0 2] [1 [2 3]]]  # Simplified
```

### Why Understand Compilation?
- **Performance**: Optimize slow Hoon by understanding Nock output
- **Debugging**: Trace Hoon behavior at Nock level
- **Learning**: See how high-level constructs map to primitives

## Basic Hoon→Nock Patterns

### Literals
```hoon
Hoon: 42
Nock: 42  # Atoms compile to themselves

Hoon: [1 2 3]
Nock: [1 [2 [3 0]]]  # Null-terminated list

Hoon: "hello"
Nock: [104 [101 [108 [108 [111 0]]]]]  # ASCII list
```

### Subject Access
```hoon
Hoon: +  # Variable from subject (head of tail)
Nock: [0 6]  # Slot axis 6 (tail, head)

Hoon: -  # Variable from subject (tail of tail)
Nock: [0 7]  # Slot axis 7 (tail, tail)

Hoon: .
Nock: [0 1]  # Identity (whole subject)
```

### Function Calls
```hoon
Hoon: (add 2 3)

Nock (conceptual):
[
  9              # Rule 9: invoke arm
  axis-for-add   # Find 'add' in stdlib
  [
    [1 stdlib]   # Subject: standard library core
    [1 [2 3]]    # Sample: arguments [2 3]
  ]
]
```

## Core Compilation Patterns

### Gates (Functions)
```hoon
Hoon:
|=  n=@
^-  @
(add n 1)

Compiles to Nock core:
[
  [formula-for-add-n-1]  # Battery (single arm)
  n                      # Sample (payload)
]

Invocation:
  - Create core with argument in payload
  - Invoke arm 2 (formula)
  - Formula accesses n via /[3 core] (tail)
```

### Arms in Cores
```hoon
Hoon:
|%
++  increment
  |=  n=@
  (add n 1)
++  decrement
  |=  n=@
  (sub n 1)
--

Compiles to:
[
  [increment-formula decrement-formula]  # Battery
  ~                                       # Context (empty)
]
```

### Doors (Stateful Cores)
```hoon
Hoon:
|_  state=@
++  read
  state
++  write
  |=  new=@
  +>(state new)  # Return new door with updated state
--

Compiles to:
[
  [read-formula write-formula]  # Battery
  state                          # Sample (door argument)
]
```

## Control Flow Compilation

### If-Then-Else
```hoon
Hoon:
?:  =(n 0)
  'zero'
'not zero'

Nock:
[
  6                    # Rule 6: if-then-else
  [5 [0 6] [1 0]]     # Test: =(n 0)
  [1 'zero']           # Then branch
  [1 'not zero']       # Else branch
]
```

### Pattern Matching (?-)
```hoon
Hoon:
?-  -.foo
  %a  'case-a'
  %b  'case-b'
==

Compiles to nested if-then-else:
[
  6
  [5 [0 2] [1 %a]]  # Test: foo.head == %a?
  [1 'case-a']       # Then: %a case
  [                  # Else: check %b
    6
    [5 [0 2] [1 %b]]
    [1 'case-b']
    !!  # Crash if no match
  ]
]
```

## Optimization Patterns

### Tail Call Optimization
```hoon
# Hoon with tail recursion
|=  n=@
?:  =(n 0)
  0
$(n (dec n))  # Tail call

# Compiles to loop (no stack growth)
# Nock uses Rule 9 (call) in tail position
```

### Constant Folding
```hoon
Hoon: (add 2 3)  # Compile-time constant

Nock: 5  # Compiler evaluates at compile time
```

## Analyzing Compiler Output

### Using !:(!,(...)) to See Nock
```hoon
> !:(!,(add 2 3))
[9 2 [[0 6] [1 2 3] 0 7]]  # Actual Nock formula
```

### Reading Generated Nock
```
[9 2 [[0 6] [1 2 3] 0 7]]

Break down:
  9: Rule 9 (call arm in core)
  2: Axis 2 (invoke arm at axis 2)
  Rest: Subject formula for call
    [0 6]: Get stdlib from subject
    [1 [2 3]]: Constant arguments
    [0 7]: Get context
```

## Performance Implications

### Efficient Hoon → Efficient Nock
```hoon
# Fast: Uses jetted stdlib
(add a b)  → Fast Nock (calls jetted add)

# Slow: Reimplements addition
=+  result=0
|-
?:  =(b 0)
  result
$(result +(result), b (dec b))
→ Slow Nock (loop with increment/decrement)
```

### Common Performance Issues
1. **Avoiding stdlib**: Reimplementing +add, +mul → slow
2. **Deep nesting**: Excessive subject extension (Rule 8) → stack growth
3. **Repeated computation**: Not caching results → redundant work

## Integration with nock-development Plugin

### Workflow
```
1. Write Hoon code in hoon-development plugin
2. Use /hoon-to-nock command to see compilation
3. Analyze Nock with nock-development tools
4. Optimize based on Nock understanding
5. Verify improvements with benchmarking
```

### Cross-Plugin Usage
```
# In hoon-development
Write app in Hoon → Compile to Nock

# In nock-development
Analyze Nock output → Identify bottlenecks → Suggest jets
```

## Summary

Hoon compiles to Nock via AST transformation: literals→atoms/cells, functions→cores with battery+payload, control flow→Rule 6 (if-then-else) + nested patterns. Gates (|=) become cores with sample in payload, arms accessible via Rule 9. Optimization: tail calls avoid stack growth, constant folding at compile time, use jetted stdlib (add/mul). Analyze output: !:(!,(...)) shows Nock formula. Performance: efficient Hoon uses stdlib jets, avoid reimplementing primitives, minimize deep nesting. Cross-plugin: hoon-development writes code, nock-development analyzes compiled output for optimization.
