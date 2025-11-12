---
name: nock-metacircular-evaluation
description: Self-interpretation patterns in Nock including +mock (Nock-in-Nock), virtualization, sandboxing, and the Urbit lifecycle function. Covers running Nock formulas within Nock itself for testing, debugging, and secure execution. Use when implementing interpreters within interpreters or understanding Nock's self-hosting capabilities.
---

# Nock Metacircular Evaluation

Nock interpreting Nock: self-interpretation, virtualization, and the +mock pattern.

## Metacircular Evaluation Concept

### What is Metacircular?
```
Nock-in-Nock: A Nock formula that implements the Nock evaluator

*[nock-interpreter-formula [subject formula]]
  → Same result as native nock(subject, formula)

Use cases:
  - Testing (verify interpreter correctness)
  - Sandboxing (run untrusted code safely)
  - Debugging (trace execution at Nock level)
  - Virtualization (nested interpretation)
```

## +mock: Nock's Self-Interpreter

### The +mock Function (Hoon Standard Library)
```hoon
++  mock
  |=  [subject=* formula=*]
  ^-  (each * (pair path tang))
  %-  mook
  (mink [subject formula] $~)

# Implements Nock evaluation in Nock itself
# Returns: success (result) or failure (crash trace)
```

### Why +mock?
- **Pure Nock**: No escape to host runtime
- **Crash Handling**: Returns errors instead of crashing
- **Tracing**: Can instrument execution
- **Sandboxing**: Isolate untrusted computations

## Implementing Nock-in-Nock

### Simplified Pattern
```
nock-in-nock formula structure:

[
  battery  # Implementation of all 13 rules
  [subject formula]  # Payload (what to evaluate)
]

When invoked:
  - Extract subject and formula from payload
  - Pattern match formula head (operator)
  - Recursively interpret sub-formulas
  - Return result
```

### Challenges
```
1. Infinite regress: Interpreter needs interpreter
   Solution: Jet the metacircular interpreter

2. Performance: Nock-in-Nock is slow (100-1000x)
   Solution: Use for testing/debugging only

3. Crashes: How to catch and return errors?
   Solution: +mock returns (each * tang) not raw result

4. Scrying: Needs access to namespace
   Solution: +mink takes scry gate parameter
```

## Virtualization Pattern

### Sandboxed Execution
```hoon
# Run untrusted formula safely
=/  untrusted-formula  [4 [0 1]]  # Increment subject

=/  result
  (mock 42 untrusted-formula)

?-  result
  [%0 value]  value  # Success: return result
  [%1 trace]  ~      # Failure: handle error
==
```

### Nested Interpretation
```
Layer 1: Native runtime (C/Rust)
  ↓
Layer 2: Hoon compiler (Nock)
  ↓
Layer 3: User code (Nock via +mock)
  ↓
Layer 4: Potentially more nesting...

Use case: Run-time code generation, DSLs, safe eval
```

## Error Handling with +mock

### Success Case
```hoon
> (mock 10 [4 0 1])
[%0 p=11]  # %0 = success, p = result (10+1 = 11)
```

### Failure Case
```hoon
> (mock 10 [0 0])  # Axis 0 is illegal
[%1 p=~]  # %1 = failure, p = error trace

> (mock 10 [4 [1 [1 2]]])  # Increment cell
[%1 p=~]  # Crash: cannot increment cell
```

### Error Tracing
```hoon
# +mook: Convert raw +mock result to readable error
> (mook (mock 10 [0 0]))
[%2 p=~[[%leaf p="axis 0"]]]  # Readable error message
```

## +mink: Scrying Support

### Definition
```hoon
++  mink
  |=  [[subject formula] scry-gate]
  ^-  (each * (pair path tang))
  # Like +mock but with scry support (Rule 12)
```

### Usage
```hoon
# Provide scry function for namespace access
=/  scry-func
  |=  [ref=* path=*]
  ^-  (unit (unit))
  # ... lookup in namespace ...

=/  result
  (mink [subject formula] scry-func)
```

## Urbit Lifecycle Function

### The Lifecycle
```
Urbit runtime maintains:
  - State (Arvo kernel + userspace)
  - Events (input queue)
  - Effects (output queue)

Each event:
  1. Evaluate with +mink
  2. Produce new state + effects
  3. Commit state
  4. Apply effects

+mink enables:
  - Deterministic evaluation
  - Crash recovery (rollback)
  - Replay from log
```

### Pattern
```hoon
=/  state  *arvo-state
=/  event  *poke

=/  result
  (mink [state [%9 2 %poke event]] scry-gate)

?-  result
  [%0 new-state effects]
    # Success: commit state, apply effects
    (apply-effects effects)
    new-state

  [%1 crash-trace]
    # Failure: rollback, log error
    (log-crash crash-trace)
    state  # Keep old state
==
```

## Implementation Pattern (Conceptual)

### Core Nock-in-Nock Structure
```
nock-interpreter-core = [
  [
    rule-0-arm   # Slot implementation
    rule-1-arm   # Constant implementation
    rule-2-arm   # Evaluate implementation
    ...
    rule-12-arm  # Scry implementation
  ]
  ~  # No payload (pure function)
]

Main entry arm:
  |=  [subject=* formula=*]
  ^-  *
  ?~  formula  !!  # Crash if formula is atom

  =/  op  (head formula)
  =/  rest  (tail formula)

  ?+  op  !!  # Unknown operator
    %0  (invoke rule-0-arm [subject rest])
    %1  (invoke rule-1-arm [subject rest])
    ...
  ==
```

## Testing with Metacircular Evaluation

### Validation Strategy
```python
def validate_interpreter():
    """Verify our interpreter matches reference via +mock."""
    test_cases = [
        ([10], [0, 1], [10]),           # Identity
        ([_], [1, 42], [42]),           # Constant
        ([10], [4, [0, 1]], [11]),      # Increment
        # ... 100+ test cases
    ]

    for subject, formula, expected in test_cases:
        # Our implementation
        our_result = nock(subject, formula)

        # Reference (+mock in Urbit)
        ref_result = urbit_mock(subject, formula)

        assert our_result == ref_result == expected
```

## Summary

Metacircular evaluation: Nock implementing Nock (+mock pattern). Use cases: testing (verify correctness), sandboxing (safe execution), virtualization (nested interpretation), debugging (trace execution). +mock returns (each * tang): %0=success with result, %1=failure with trace. +mink extends +mock with scry support for Rule 12. Urbit lifecycle uses +mink for deterministic event processing, crash recovery, and replay. Challenge: performance (100-1000x slower), solution: jet the metacircular interpreter or use only for testing.
