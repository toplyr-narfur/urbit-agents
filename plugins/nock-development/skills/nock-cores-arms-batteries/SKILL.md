---
name: nock-cores-arms-batteries
description: Core structure pattern in Nock including batteries (code), payloads (data), arms (named formulas), and core invocation via Rule 9. Covers the fundamental code/data pattern used throughout Urbit for functions, objects, and modules. Use when understanding cores, implementing function calls, or building Hoon-compatible structures.
---

# Nock Cores, Arms, and Batteries Skill

Complete guide to Nock's core pattern: the fundamental structure for code organization and function invocation.

## Core Fundamentals

### Definition
```
core := [battery payload]

battery  = code (Nock formulas / functions)
payload  = data (environment / state / context)
arm      = named formula in battery
```

### Purpose
Cores provide:
- **Encapsulation**: Bundle code with data
- **Function calls**: Invoke arms in battery
- **Closures**: Payload captures environment
- **Objects**: Battery=methods, payload=state

## Core Structure

### Basic Pattern
```
core = [[arm1 arm2 arm3 ...] payload]
         |___ battery _____|   |_data_|

Visual:
       [core]
      /      \
  battery   payload
   /  |  \     |
 arm1 arm2... data
```

### Example: Counter Core
```
counter-core = [
  [increment-formula decrement-formula]  # Battery (2 arms)
  42                                      # Payload (current value)
]

Structure:
       [·]
      /   \
    [·]    42
   /   \
 inc   dec
```

## Arm Invocation (Rule 9)

### Formal Definition
```
*[subject [9 axis core-formula]]

Steps:
1. Evaluate core-formula to produce core
2. Extract arm at axis from core's battery
3. Execute arm with core as subject
```

### Detailed Reduction
```
*[_ [9 2 [1 [[formula] data]]]]
  ↓ Rule 9
*[core [2 [0 1] [0 2]]]  # Standard Rule 9 expansion
  ↓ core = [[formula] data]
*[[[formula] data] [2 [0 1] [0 2]]]
  ↓ Rule 2 (evaluate)
*[*[core [0 1]] *[core [0 2]]]
  ↓ Rule 0 (slot)
*[core formula]  # core is subject, formula from battery
  ↓ Execute formula with core as subject
result
```

### Example: Invoke Increment Arm
```
core = [[inc-formula dec-formula] 10]

*[anything [9 4 [1 core]]]  # Invoke axis 4 (first arm)
  → core = [[inc-formula dec-formula] 10]
  → battery = [inc-formula dec-formula]
  → arm = inc-formula (at axis 4 of core)
  → *[core arm]  # Execute with core as subject
```

## Battery Organization

### List of Arms
```
battery = [arm1 [arm2 [arm3 0]]]  # Null-terminated list

Axes:
  axis 4:  arm1       (head of battery)
  axis 10: arm2       (head of tail of battery)
  axis 20: arm3       (head of tail of tail)
```

### Tuple of Arms
```
battery = [arm1 arm2]  # Simple pair

Axes:
  axis 4: arm1  (head)
  axis 5: arm2  (tail)
```

## Payload Patterns

### Simple Data
```
core = [battery 42]  # Payload is single atom

Access in arm:
  /[3 core] → 42  # Tail of core = payload
```

### Structured Payload
```
core = [battery [counter max-value]]

Access in arm:
  /[6 core] → counter    # tail, head
  /[7 core] → max-value  # tail, tail
```

### Nested Cores (Closure)
```
outer-core = [outer-battery [inner-core data]]

Arms in outer-core can access:
  - Own battery: /[2 core]
  - Own payload: /[3 core]
  - Inner core: /[6 core]
  - Captured data: /[7 core]
```

## Common Core Patterns

### Function Core (Closure)
```hoon
|=  arg=@  # Gate (function)
^-  @
(add arg 1)

Compiles to Nock core:
[
  [formula-for-add-arg-1]  # Battery (single arm)
  arg                       # Payload (argument)
]

Invocation:
  - Pass argument as payload
  - Invoke arm with core as subject
  - Formula accesses arg via /[3 core]
```

### Object Core (State + Methods)
```
object = [
  [get-method set-method]  # Battery (methods)
  [private-state]          # Payload (encapsulated state)
]

Methods can:
  - Read state: /[3 core]
  - Call other methods: recursive core invocation
  - Return new core with updated state
```

### Module Core (Library)
```
stdlib = [
  [add mul div mod ...]  # Battery (many utility functions)
  ~                      # Payload (empty, functions are pure)
]

Usage:
  - Import core into subject
  - Call arms by axis
  - No shared state (pure functions)
```

## Arm Formula Patterns

### Accessing Payload
```hoon
# Hoon
|=  n=@
^-  @
(add n 1)

# Nock (simplified)
[
  [[4 [0 3]]]  # Battery: increment payload
  _            # Payload: argument n
]

Formula [4 [0 3]]:
  - [0 3] → /[3 core] → payload → n
  - [4 ...] → +n → n+1
```

### Calling Other Arms (Recursion)
```
[
  [
    [formula-base-case]    # arm 4
    [formula-recursive]    # arm 5
  ]
  argument
]

Recursive arm formula:
  [9 4 [0 1]]  # Call arm 4 (base case) with current core
```

### Returning New Core (State Update)
```
# Stateful core that returns updated version
[
  [[update-formula read-formula]]
  state
]

update-formula:
  [8 [4 [0 3]] [0 1]]  # Extend subject with state+1, return new core
    → [[new-state old-state] core]
    → Build new core with updated payload
```

## Core Composition

### Nested Cores
```
outer = [
  [outer-arms]
  [
    inner-core  # Inner core in payload
    outer-data
  ]
]

Access pattern:
  - Outer arms: /[2 outer]
  - Inner core: /[6 outer]
  - Outer data: /[7 outer]
  - Inner arms: /[12 outer] (head of inner-core's battery)
```

### Door (Core-Building Core)
```hoon
|_  state=@  # Door (core builder)
++  increment
  |=  n=@
  (add state n)
--

Compiles to:
[
  [[arm-increment-formula]]  # Battery
  state                      # Sample (door argument)
]

Invocation:
  1. Create door with initial state
  2. Invoke arms, which close over state
  3. Arms return gates (function cores)
```

## Implementation Patterns

### Core Construction
```python
def make_core(battery, payload):
    """Create core from battery and payload."""
    return Cell(battery, payload)

def invoke_arm(core, arm_axis):
    """Rule 9: Invoke arm in core."""
    if not is_cell(core):
        raise NockCrash("invoke arm in atom")

    battery = slot(core, 2)  # Head of core
    arm_formula = slot(battery, arm_axis)

    # Execute arm with core as subject
    return nock(core, arm_formula)
```

### Example: Counter Core
```python
# Define arms (formulas)
increment = [4, [0, 3]]  # +/[3 core] (increment payload)
decrement = [8, [1, 0], [8, [1, [6, [5, [0, 7], [4, 0, 6]], [0, 6], [9, 2, [0, 2], [[4, 0, 6], 0, 7]]]], [9, 2, 0, 1]]]  # Complex

# Create core
counter = make_core(
    Cell(increment, decrement),  # Battery
    Atom(42)                     # Payload
)

# Invoke increment arm (axis 4)
result = invoke_arm(counter, 4)  # → 43
```

## Subject-Oriented Programming

### The Subject
In `*[subject formula]`, the subject is often a core:
```
subject = [standard-library user-code]
          |__ core __|

Formula can:
  - Access stdlib: /[2 subject]
  - Access user code: /[3 subject]
  - Call stdlib functions: [9 axis-for-function [0 2]]
```

### Subject Extension (Rule 8)
```
*[subject [8 new-binding formula]]
  → *[[new-binding subject] formula]

Effect:
  - Prepend new-binding to subject
  - Formula sees extended subject
  - Like lexical scope / let binding
```

## Summary

Cores are Nock's fundamental code pattern: `[battery payload]` where battery=formulas, payload=data. Rule 9 invokes arms: evaluate core-formula, extract arm from battery, execute with core as subject. Batteries organize arms (at axes 4, 10, 20 for lists; 4, 5 for pairs). Payloads capture environment (closures), hold arguments (gates/functions), or store state (objects). Core composition: nesting cores in payloads enables modules, closures, and complex scoping. Subject-oriented programming: subject is often a core, formulas navigate via slot, arms invoke via Rule 9.
