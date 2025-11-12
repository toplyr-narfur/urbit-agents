---
name: nock-implement-exercise
description: Hands-on guided exercises for implementing Nock patterns from increment through decrement challenge.
subagent_type: nock-interpreter-engineer
---

# Nock Implement Exercise Command

6-phase hands-on curriculum with progressively challenging Nock implementation exercises.

## Phase 1: Basic Operations (Warmup)

**Goal**: Implement simple Nock formulas

### Exercise 1.1: Identity
```
Task: Implement formula that returns subject unchanged
Formula: [0 1]
Test: *[42 [0 1]] → 42
```

### Exercise 1.2: Constant
```
Task: Return constant, ignore subject
Formula: [1 VALUE]
Test: *[_ [1 99]] → 99
```

### Exercise 1.3: Increment
```
Task: Add 1 to subject
Formula: [4 [0 1]]
Test: *[41 [4 [0 1]]] → 42
```

**Success**: All 3 basic formulas working

---

## Phase 2: Tree Navigation

**Goal**: Master slot (tree addressing)

### Exercise 2.1: Head and Tail
```
Task: Extract head and tail from cell
Head formula: [0 2]
Tail formula: [0 3]
Test: *[[10 20] [0 2]] → 10
Test: *[[10 20] [0 3]] → 20
```

### Exercise 2.2: Deep Navigation
```
Task: Navigate nested structure
Subject: [[1 2] [3 4]]
Axes:
  4 → 1  (head of head)
  5 → 2  (tail of head)
  6 → 3  (head of tail)
  7 → 4  (tail of tail)

Test all 4 axes
```

### Exercise 2.3: Calculate Axis
```
Task: Given tree and target element, calculate axis
Example: In [[a b] c], what axis reaches 'b'?
Answer: Axis 5 (binary 101: head, tail)
```

**Success**: Can navigate any tree structure

---

## Phase 3: Conditionals

**Goal**: Implement if-then-else logic

### Exercise 3.1: Simple Branch
```
Task: Return 'yes' if subject is 0, 'no' otherwise
Formula: [6 [5 [0 1] [1 0]] [1 'yes'] [1 'no']]
  Test: [5 [0 1] [1 0]] checks if subject == 0
  Then: [1 'yes']
  Else: [1 'no']

Test: *[0 formula] → 'yes'
Test: *[1 formula] → 'no'
```

### Exercise 3.2: Min of Two Numbers
```
Task: Return minimum of [a b]
Strategy: If a < b then a else b
Hint: No less-than, use equality in loop

Test: *[[5 10] formula] → 5
Test: *[[15 3] formula] → 3
```

**Success**: Conditional logic working

---

## Phase 4: Recursion and Loops

**Goal**: Implement recursive computations

### Exercise 4.1: Factorial (Simple)
```
Task: Compute n! using recursion
Formula structure:
  - Base case: if n == 0 then 1
  - Recursive: else n * factorial(n-1)

Hint: Use Rule 9 (call) for recursion

Test: *[0 formula] → 1
Test: *[5 formula] → 120
```

### Exercise 4.2: List Length
```
Task: Count elements in null-terminated list
List: [a [b [c 0]]]  # 3 elements
Base: if list == 0 then 0
Recursive: else 1 + length(tail)

Test: *[0 formula] → 0  # Empty list
Test: *[[1 [2 [3 0]]] formula] → 3
```

**Success**: Recursive algorithms implemented

---

## Phase 5: Cores and Arms

**Goal**: Build function cores

### Exercise 5.1: Counter Core
```
Task: Build core with increment/decrement arms
Structure:
[
  [inc-arm dec-arm]  # Battery
  n                  # Payload (state)
]

inc-arm: [4 [0 3]]  # +payload
dec-arm: [decrement formula]

Test: Create core with n=42
Test: Invoke increment → 43
Test: Invoke decrement → 41
```

### Exercise 5.2: Calculator Core
```
Task: Build core with add/sub/mul arms
Structure:
[
  [add-arm sub-arm mul-arm]
  [a b]  # Payload (arguments)
]

Test: *[core [9 4 [0 1]]] → a + b  (add)
Test: *[core [9 5 [0 1]]] → a - b  (sub)
Test: *[core [9 6 [0 1]]] → a * b  (mul)
```

**Success**: Can build and invoke cores

---

## Phase 6: The Decrement Challenge

**Goal**: Implement decrement using only increment and equality

### Background
```
Problem: Nock has increment but no decrement
Solution: Count from 0 until +counter == n, return counter

Example: decrement(5)
  counter=0: +0=1 ≠ 5, continue
  counter=1: +1=2 ≠ 5, continue
  counter=2: +2=3 ≠ 5, continue
  counter=3: +3=4 ≠ 5, continue
  counter=4: +4=5 = 5, return 4 ✓
```

### Formula (Complete)
```
[8 [1 0] 8 [1 6 [5 [0 7] 4 0 6] [0 6] 9 2 [0 2] [4 0 6] 0 7] 9 2 0 1]
```

### Breakdown
```
[8 [1 0] ...]
  Push 0 onto subject (counter = 0)
  Subject: [0 n]

[8 [1 LOOP-CORE] ...]
  Push loop core onto subject
  Subject: [LOOP-CORE [0 n]]

LOOP-CORE = [6 [5 [0 7] 4 0 6] [0 6] 9 2 [0 2] [4 0 6] 0 7]
  Test: [5 [0 7] 4 0 6]  → =(n, +(counter))
  Then: [0 6]  → return counter
  Else: [9 2 [0 2] [4 0 6] 0 7]  → recurse with counter+1

[9 2 0 1]
  Invoke arm 2 (the loop) with final subject
```

### Tasks
1. Understand each component of the formula
2. Trace execution manually for n=3
3. Implement in your Nock interpreter
4. Test: *[42 DECREMENT] → 41
5. Benchmark: How long does it take?
6. Optimize: Can you make it faster? (Hint: Jet it!)

**Success**: Decrement working, formula understood

---

## Bonus Challenges

### Challenge 1: Fibonacci
```
Task: Compute nth Fibonacci number
Base: fib(0)=0, fib(1)=1
Recursive: fib(n) = fib(n-1) + fib(n-2)

Test: *[0 formula] → 0
Test: *[1 formula] → 1
Test: *[10 formula] → 55
```

### Challenge 2: List Reverse
```
Task: Reverse null-terminated list
Input: [1 [2 [3 0]]]
Output: [3 [2 [1 0]]]

Hint: Use accumulator pattern with Rule 8
```

### Challenge 3: Binary Search
```
Task: Find element in sorted list
Input: [target [sorted-list]]
Output: Index of target or crash if not found

Hint: Compare target with middle element, recurse
```

**Success**: Advanced patterns mastered

---

## Validation and Testing

### Test Framework
```python
exercises = [
    # (name, subject, formula, expected)
    ("identity", 42, [0, 1], 42),
    ("constant", 99, [1, 777], 777),
    ("increment", 41, [4, [0, 1]], 42),
    ("head", [10, 20], [0, 2], 10),
    # ... all exercises
]

def test_exercises():
    for name, subject, formula, expected in exercises:
        result = nock(subject, formula)
        assert result == expected, f"{name} failed"
        print(f"✓ {name}")
```

### Benchmarking
```python
import time

def benchmark_exercise(name, subject, formula):
    start = time.perf_counter()
    result = nock(subject, formula)
    elapsed = time.perf_counter() - start
    print(f"{name}: {elapsed*1000:.3f}ms")
    return result
```

## Next Steps

- Use `/build-nock-interpreter` to implement solutions
- Use `/optimize-nock-performance` to speed up decrement
- Use `/debug-nock-execution` if exercises fail
- Share solutions with community

## Resources

- Decrement Exercise: https://docs.urbit.org/nock/decrement
- Core Academy: https://docs.urbit.org/build-on-urbit/core-academy/ca00
- Community Implementations: https://docs.urbit.org/nock/implementations
