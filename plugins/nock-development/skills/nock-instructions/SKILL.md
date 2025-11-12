---
name: nock-instructions
description: Complete reference for Nock's 13 reduction rules (0-12) with formal semantics, reduction examples, and implementation patterns. Covers slot, constant, evaluate, cell test, increment, equality, if-then-else, composition, push, call, and hints. Use when implementing the Nock evaluator, understanding reduction semantics, or debugging formula execution.
---

# Nock Instructions Skill

Complete reference for Nock's 13 reduction rules that define the evaluation semantics of the Nock virtual machine.

## Rule Summary Table

| Rule | Pattern | Reduction | Purpose |
|------|---------|-----------|---------|
| 0 | `[a 0 b]` | `/[b a]` | Slot (tree addressing) |
| 1 | `[a 1 b]` | `b` | Constant |
| 2 | `[a 2 b c]` | `*[*[a b] *[a c]]` | Evaluate (recurse) |
| 3 | `[a 3 b]` | `?*[a b]` | Cell test |
| 4 | `[a 4 b]` | `+*[a b]` | Increment |
| 5 | `[a 5 b c]` | `=*[a b] *[a c]` | Equality |
| 6 | `[a 6 b c d]` | if-then-else | Conditional |
| 7 | `[a 7 b c]` | `*[*[a b] c]` | Composition |
| 8 | `[a 8 b c]` | `*[[*[a b] a] c]` | Push (extend subject) |
| 9 | `[a 9 b c]` | `*[*[a c] 2 [0 1] 0 b]` | Call (invoke arm) |
| 10 | `[a 10 [b c] d]` / `[a 10 b c]` | hint processing | Edit hint |
| 11 | `[a 11 b c]` | `*[a c]` | Jet hint (reserved) |
| 12 | `[a 12 b c]` | `/[c a]` | Scry (reserved) |

## Rule 0: Slot (Tree Addressing)

### Formal Definition
```
*[a 0 b] → /[b a]
```

**Purpose**: Extract subtree at axis `b` from subject `a`

### Examples
```
*[[10 20] [0 2]]  → /[2 [10 20]]  → 10 (head)
*[[10 20] [0 3]]  → /[3 [10 20]]  → 20 (tail)
*[42 [0 1]]       → /[1 42]       → 42 (identity)
```

### Use Cases
- Variable access from subject
- Extracting parts of data structures
- Identity operation (axis 1)

## Rule 1: Constant

### Formal Definition
```
*[a 1 b] → b
```

**Purpose**: Return constant `b`, ignoring subject `a`

### Examples
```
*[anything [1 42]]     → 42
*[999 [1 [1 2 3]]]    → [1 2 3]
```

### Use Cases
- Literals in code
- Data embedding
- Ignoring context

## Rule 2: Evaluate (Recurse)

### Formal Definition
```
*[a 2 b c] → *[*[a b] *[a c]]
```

**Purpose**: Evaluate `b` and `c`, then nock first result with second

### Reduction Steps
```
*[subject [2 formula-b formula-c]]
  Step 1: new-subject = *[subject formula-b]
  Step 2: new-formula = *[subject formula-c]
  Step 3: *[new-subject new-formula]
```

### Examples
```
*[10 [2 [1 100] [1 [4 0 1]]]]
  → *[*[10 [1 100]] *[10 [1 [4 0 1]]]]
  → *[100 [4 0 1]]
  → +*[100 [0 1]]
  → +100
  → 101
```

### Use Cases
- Function application
- Dynamic formula construction
- Controlled evaluation

## Rule 3: Cell Test

### Formal Definition
```
*[a 3 b] → ?*[a b]
```

**Purpose**: Test if `*[a b]` is cell (0) or atom (1)

### Examples
```
*[_ [3 [1 42]]]      → ?42      → 1 (is atom)
*[_ [3 [1 [1 2]]]]   → ?[1 2]   → 0 (is cell)
```

### Use Cases
- Type discrimination
- Pattern matching
- Dynamic dispatch based on data shape

## Rule 4: Increment

### Formal Definition
```
*[a 4 b] → +*[a b]
```

**Purpose**: Add 1 to result of `*[a b]`

### Examples
```
*[_ [4 [1 41]]]  → +41  → 42
*[10 [4 [0 1]]]  → +10  → 11
```

### Use Cases
- Arithmetic (foundation for all math)
- Counting, iteration
- Successor function

## Rule 5: Equality

### Formal Definition
```
*[a 5 b c] → =*[a b] *[a c]
```

**Purpose**: Deep equality test (0=equal, 1=not equal)

### Examples
```
*[_ [5 [1 10] [1 10]]]  → =10 10   → 0 (equal)
*[_ [5 [1 10] [1 20]]]  → =10 20   → 1 (not equal)
```

### Use Cases
- Comparisons
- Conditionals (equality tests)
- Data validation

## Rule 6: If-Then-Else

### Formal Definition
```
*[a 6 b c d] → *[a if *[a b]=0 then c else d]
```

**Simplified**: Test `b`, if 0 (true) execute `c`, else execute `d`

### Examples
```
*[_ [6 [1 0] [1 100] [1 200]]]
  → Test: *[_ [1 0]] = 0
  → 0 = true, take "then" branch
  → *[_ [1 100]]
  → 100

*[_ [6 [1 1] [1 100] [1 200]]]
  → Test: *[_ [1 1]] = 1
  → 1 = false, take "else" branch
  → *[_ [1 200]]
  → 200
```

### Use Cases
- Conditional execution
- Branching logic
- Pattern matching

## Rule 7: Composition (Pipe)

### Formal Definition
```
*[a 7 b c] → *[*[a b] c]
```

**Purpose**: Evaluate `b`, use result as new subject for `c`

### Examples
```
*[10 [7 [4 0 1] [4 0 1]]]
  → *[*[10 [4 0 1]] [4 0 1]]
  → *[11 [4 0 1]]  # First increment: 10→11
  → 12             # Second increment: 11→12
```

### Use Cases
- Function composition
- Pipelines (Unix-style `|`)
- Sequential transformations

## Rule 8: Push (Extend Subject)

### Formal Definition
```
*[a 8 b c] → *[[*[a b] a] c]
```

**Purpose**: Evaluate `b`, prepend to subject, execute `c` with extended subject

### Examples
```
*[10 [8 [4 0 1] [0 2]]]
  → *[[*[10 [4 0 1]] 10] [0 2]]
  → *[[11 10] [0 2]]  # Extended subject: [11 10]
  → /[2 [11 10]]
  → 11
```

### Use Cases
- Local variable binding
- Stack frames
- Lexical scoping

## Rule 9: Call (Invoke Arm in Core)

### Formal Definition
```
*[a 9 b c] → *[*[a c] 2 [0 1] 0 b]
```

**Simplified**: Evaluate `c` to produce core, invoke arm at axis `b`

### Conceptual Model
```
core = [battery payload]
arm = /[b battery]
*[core arm]  # Execute arm with core as subject
```

### Examples
```
# Invoke arm 2 in simple core
*[_ [9 2 [1 [[formula-1 formula-2] data]]]]
  → Evaluate core formula
  → Extract arm 2 (formula-1)
  → Execute arm with core as subject
```

### Use Cases
- Function calls
- Method invocation
- Core evaluation (Hoon's fundamental pattern)

## Rule 10: Edit Hint

### Formal Definition
```
*[a 10 [b c] d] → *[a c] (dynamic hint, then d)
*[a 10 b c]     → *[a c] (static hint, just c)
```

**Purpose**: Optimization hints, metadata, jet registration

### Examples
```
# Static hint (ignored by spec)
*[subj [10 [0 marker] formula]]  → *[subj formula]

# Dynamic hint (evaluate hint, then body)
*[subj [10 [[1 clue] hint-formula] body]]
  → Process hint
  → *[subj body]
```

### Use Cases
- Jet registration (`%fast` hint)
- Profiling markers
- Debug metadata
- Optimization directives

## Rule 11: Jet Hint (Reserved)

### Formal Definition
```
*[a 11 b c] → *[a c]
```

**Purpose**: Reserved for implementation-specific jetting

**Spec Behavior**: Ignore hint, evaluate `c`

**Implementations May**: Accelerate with native code if jet available

### Use Cases
- Native code acceleration
- Performance optimization
- Runtime hooks

## Rule 12: Scry (Reserved)

### Formal Definition
```
*[a 12 b c] → /[c a]
```

**Purpose**: Referentially transparent data access (pure read-only query)

**Status**: Reserved for future Urbit lifecycle function

### Use Cases
- Pure data access
- Namespace queries
- Referential transparency guarantees

## Implementation Pattern

### Complete Evaluator (Python)

```python
def nock(subject, formula):
    """Main Nock evaluator implementing all 13 rules."""
    if not is_cell(formula):
        raise NockCrash("formula must be cell")

    op = slot(formula, 2)  # Head of formula
    rest = slot(formula, 3)  # Tail of formula

    if not is_atom(op):
        raise NockCrash("operator must be atom")

    # Rule 0: Slot
    if op == 0:
        return slot(subject, rest)

    # Rule 1: Constant
    if op == 1:
        return rest

    # Rule 2: Evaluate
    if op == 2:
        b = slot(rest, 2)
        c = slot(rest, 3)
        return nock(nock(subject, b), nock(subject, c))

    # Rule 3: Cell test
    if op == 3:
        return 0 if is_cell(nock(subject, rest)) else 1

    # Rule 4: Increment
    if op == 4:
        result = nock(subject, rest)
        if is_cell(result):
            raise NockCrash("increment cell")
        return result + 1

    # Rule 5: Equality
    if op == 5:
        b = slot(rest, 2)
        c = slot(rest, 3)
        return 0 if nock(subject, b) == nock(subject, c) else 1

    # Rule 6: If-then-else
    if op == 6:
        test = slot(rest, 2)
        then_branch = slot(rest, 6)
        else_branch = slot(rest, 7)
        condition = nock(subject, test)
        branch = then_branch if condition == 0 else else_branch
        return nock(subject, branch)

    # Rule 7: Composition
    if op == 7:
        b = slot(rest, 2)
        c = slot(rest, 3)
        return nock(nock(subject, b), c)

    # Rule 8: Push
    if op == 8:
        b = slot(rest, 2)
        c = slot(rest, 3)
        return nock([nock(subject, b), subject], c)

    # Rule 9: Call
    if op == 9:
        b = slot(rest, 2)
        c = slot(rest, 3)
        core = nock(subject, c)
        arm = slot(core, 2)  # Battery is head of core
        arm_formula = slot(arm, b)
        return nock(core, arm_formula)

    # Rule 10: Hint
    if op == 10:
        hint = slot(rest, 2)
        body = slot(rest, 3)
        # Simplified: ignore hint, evaluate body
        return nock(subject, body)

    # Rule 11: Jet hint
    if op == 11:
        return nock(subject, slot(rest, 3))

    # Rule 12: Scry
    if op == 12:
        return slot(subject, slot(rest, 3))

    raise NockCrash(f"unknown operator: {op}")
```

## Summary

13 Nock reduction rules: 0(slot), 1(constant), 2(evaluate), 3(cell test), 4(increment), 5(equality), 6(if-then-else), 7(composition), 8(push), 9(call), 10(edit hint), 11(jet hint), 12(scry). Pattern match formula head (operator) to select rule. Core pattern: Rule 9 invokes arms in cores `[battery payload]`. Composition patterns: Rule 7 (pipe), Rule 8 (extend subject). Hints (10-11) enable optimization without changing semantics. All rules deterministic, pure functional evaluation.
