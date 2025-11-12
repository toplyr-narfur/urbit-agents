---
name: learn-nock-fundamentals
description: Interactive 5-phase learning path for mastering Nock fundamentals from nouns through cores.
subagent_type: nock-fundamentals-tutor
---

# Learn Nock Fundamentals Command

Interactive 5-phase curriculum for implementers learning Nock from scratch.

## Phase 1: Atoms, Cells, and Nouns

**Goal**: Master the only data type in Nock

**Concepts**:
- Atoms: Natural numbers (0, 1, 2, ...)
- Cells: Ordered pairs [a b]
- Nouns: atom | cell (everything is a noun)

**Exercises**:
1. Identify atom vs cell: `42`, `[1 2]`, `[[1 2] 3]`
2. Build trees: `[1 [2 [3 4]]]`
3. Understand: No strings, floats, or booleans—only nouns

**Success**: Can construct and identify any noun structure

---

## Phase 2: Tree Addressing and Slot

**Goal**: Navigate binary trees using axes

**Concepts**:
- Axis 1 = root (identity)
- Axis 2 = head, Axis 3 = tail
- Binary encoding: `2n = head(n)`, `2n+1 = tail(n)`

**Exercises**:
1. Calculate `/[4 [[a b] c]]` → `a`
2. Find axis for element in `[[1 2] [3 4]]`
3. Implement slot function

**Success**: Can navigate any tree using axis notation

---

## Phase 3: Operators and Instructions

**Goal**: Understand Nock's 6 operators and 13 rules

**Operators**:
- `?`: Type test (0=cell, 1=atom)
- `+`: Increment
- `=`: Equality (0=equal, 1=not)
- `/`: Slot (tree addressing)
- `*`: Nock (evaluator)

**Rules 0-5** (essentials):
- 0: Slot - `*[a 0 b]` → `/[b a]`
- 1: Constant - `*[a 1 b]` → `b`
- 2: Evaluate - `*[a 2 b c]` → `*[*[a b] *[a c]]`
- 4: Increment - `*[a 4 b]` → `+*[a b]`
- 5: Equality - `*[a 5 b c]` → `=*[a b] *[a c]`

**Exercises**:
1. Trace: `*[10 [4 [0 1]]]` → `11`
2. Implement Rules 0-5
3. Test: Increment, equality, slot operations

**Success**: Can evaluate simple Nock formulas

---

## Phase 4: Composition and Control Flow

**Goal**: Master advanced reduction rules

**Rules 6-9**:
- 6: If-then-else - Conditional branching
- 7: Composition - Pipe operator
- 8: Push - Extend subject
- 9: Call - Invoke arm in core

**Exercises**:
1. If-then-else: `*[_ [6 test then else]]`
2. Composition: `*[10 [7 [4 0 1] [4 0 1]]]` → `12`
3. Subject extension: `*[10 [8 [4 0 1] [0 2]]]`

**Success**: Can use all 13 reduction rules

---

## Phase 5: Cores, Batteries, and Arms

**Goal**: Understand Nock's function/object pattern

**Concepts**:
- Core: `[battery payload]`
- Battery: Code (formulas)
- Payload: Data (arguments, environment)
- Arms: Named formulas in battery

**Exercises**:
1. Build counter core: `[[inc-arm dec-arm] 42]`
2. Invoke arm: `*[_ [9 2 [1 core]]]`
3. Understand closures: payload captures environment

**Success**: Can build and invoke cores

---

## Decrement Challenge

**Final Exercise**: Implement decrement using only increment and equality

**Formula**:
```
[8 [1 0] 8 [1 6 [5 [0 7] 4 0 6] [0 6] 9 2 [0 2] [4 0 6] 0 7] 9 2 0 1]
```

**Strategy**: Count from 0 until `+[counter] == n`, return `counter`

**Success**: Understand complex Nock computation

---

## Next Steps

- Use `/build-nock-interpreter` to implement what you learned
- Use `/nock-implement-exercise` for more hands-on practice
- Use `/hoon-to-nock` to see how Hoon compiles to Nock

## Resources

- Nock Definition: https://docs.urbit.org/nock/definition
- Decrement Exercise: https://docs.urbit.org/nock/decrement
- Core Academy: https://docs.urbit.org/build-on-urbit/core-academy/ca00
