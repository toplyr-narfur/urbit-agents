---
name: nock-tree-addressing
description: Binary tree addressing system using axis/slot notation for navigating noun structures. Covers axis encoding (1=root, 2=head, 3=tail, binary paths), slot calculation algorithms, and efficient tree traversal patterns. Use when implementing slot operations, understanding tree navigation, or optimizing tree addressing performance.
---

# Nock Tree Addressing Skill

Complete guide to Nock's binary tree addressing system for navigating noun structures using axes.

## Axis Fundamentals

### Basic Axiom
Every noun is a binary tree. Axes provide addresses for navigating this tree.

### Axis Encoding
```
Axis 1 = root (identity)
Axis 2 = head of root
Axis 3 = tail of root
Axis 2n = head of axis n
Axis 2n+1 = tail of axis n
```

### Visual Model
```
Tree: [a [b c]]

     [·] (axis 1: root)
    /   \
   a    [·] (axis 2: a, axis 3: [b c])
       /   \
      b     c  (axis 6: b, axis 7: c)

Complete axis mapping:
  1: [a [b c]]  (root)
  2: a          (head)
  3: [b c]      (tail)
  4: CRASH      (a is atom, cannot take head)
  5: CRASH      (a is atom, cannot take tail)
  6: b          (head of tail)
  7: c          (tail of tail)
```

## Binary Path Encoding

### Algorithm
To navigate axis `n`:
1. Convert `n` to binary
2. Strip leading `1`
3. For each remaining bit:
   - `0` → take head
   - `1` → take tail

### Examples
```
Axis 1 (binary: 1):
  Path: [] (empty path = identity)

Axis 2 (binary: 10):
  Path: [0] → head

Axis 3 (binary: 11):
  Path: [1] → tail

Axis 4 (binary: 100):
  Path: [0, 0] → head, head

Axis 5 (binary: 101):
  Path: [0, 1] → head, tail

Axis 6 (binary: 110):
  Path: [1, 0] → tail, head

Axis 7 (binary: 111):
  Path: [1, 1] → tail, tail

Axis 8 (binary: 1000):
  Path: [0, 0, 0] → head, head, head
```

## Implementation Patterns

### Recursive (Simple)
```python
def slot(subject, axis):
    """Tree addressing via recursion."""
    if axis == 0:
        raise NockCrash("axis 0 forbidden")
    if axis == 1:
        return subject
    if not is_cell(subject):
        raise NockCrash("slot in atom")

    # Even: head of (axis // 2)
    # Odd: tail of (axis // 2)
    if axis % 2 == 0:
        return slot(subject.head, axis // 2)
    else:
        return slot(subject.tail, axis // 2)
```

### Iterative (Efficient)
```python
def slot_iterative(subject, axis):
    """Fast iterative tree addressing."""
    if axis == 0:
        raise NockCrash("axis 0")
    if axis == 1:
        return subject

    # Binary path extraction
    depth = axis.bit_length() - 1  # Depth in tree
    current = subject

    # Navigate tree using binary encoding
    for i in range(depth - 1, -1, -1):
        if not is_cell(current):
            raise NockCrash("slot in atom")

        # Check bit at position i
        if (axis >> i) & 1 == 0:
            current = current.head
        else:
            current = current.tail

    return current
```

### Bitwise (Maximum Performance)
```rust
fn slot_fast(subject: &Noun, mut axis: u64) -> Result<Noun, NockError> {
    if axis == 0 {
        return Err(NockError::Crash("axis 0"));
    }
    if axis == 1 {
        return Ok(subject.clone());
    }

    // Find highest set bit (tree depth)
    let depth = 63 - axis.leading_zeros();
    let mut current = subject;

    // Navigate tree using binary path
    for i in (0..depth - 1).rev() {
        match current {
            Noun::Atom(_) => return Err(NockError::Crash("slot in atom")),
            Noun::Cell(ref head, ref tail) => {
                // Bit 0 = head, bit 1 = tail
                current = if (axis >> i) & 1 == 0 {
                    head
                } else {
                    tail
                };
            }
        }
    }

    Ok(current.clone())
}
```

## Axis Calculation

### Formula: Parent to Child
```
head(n) = 2 * n
tail(n) = 2 * n + 1

Example:
  axis 3 (tail of root)
    head(3) = 6 (tail, head)
    tail(3) = 7 (tail, tail)
```

### Formula: Child to Parent
```
parent(n) = n // 2  (integer division)

Example:
  axis 7 (tail, tail)
    parent(7) = 3 (tail of root)
  axis 6 (tail, head)
    parent(6) = 3 (tail of root)
```

## Common Patterns

### Accessing Tuple Elements
```
Tuple: [a [b [c 0]]]  (3-element list)

axis 2: a      (first element)
axis 6: b      (second: tail, head)
axis 14: c     (third: tail, tail, head)

Pattern: Elements at axes 2, 6, 14, 30, 62, ...
Formula: 2, then 2n+2 for next element
```

### Core Structure
```
Core: [[battery] payload]

axis 1: whole core
axis 2: battery (head)
axis 3: payload (tail)

Arms in battery (if battery is list):
  axis 4: first arm  (head of battery)
  axis 5: rest of arms (tail of battery)
```

## Edge Cases and Crashes

### Axis 0: Always Forbidden
```
/[0 anything] → CRASH
```

### Slot in Atom
```
/[2 42]   → CRASH (42 is atom, has no head)
/[3 999]  → CRASH (999 is atom, has no tail)
/[1 42]   → 42    (OK: axis 1 is identity)
```

### Deep Navigation
```
Tree: [1 2]

/[1 [1 2]]  → [1 2]   (OK: identity)
/[2 [1 2]]  → 1       (OK: head)
/[3 [1 2]]  → 2       (OK: tail)
/[4 [1 2]]  → CRASH   (1 is atom, cannot take head)
```

## Performance Optimization

### Memoization
```python
from functools import lru_cache

@lru_cache(maxsize=10000)
def slot_cached(subject_id, axis):
    """Cached slot lookups for repeated access."""
    subject = get_noun_by_id(subject_id)
    return slot(subject, axis)

# Speedup: 100x for frequently-accessed axes
```

### Precomputed Paths
```python
# Precompute binary paths for common axes
AXIS_PATHS = {
    1: [],
    2: [0],
    3: [1],
    4: [0, 0],
    5: [0, 1],
    6: [1, 0],
    7: [1, 1],
    # ... extend as needed
}

def slot_precomputed(subject, axis):
    """Use precomputed paths for common axes."""
    if axis in AXIS_PATHS:
        path = AXIS_PATHS[axis]
        current = subject
        for direction in path:
            if not is_cell(current):
                raise NockCrash("slot in atom")
            current = current.head if direction == 0 else current.tail
        return current
    else:
        return slot_iterative(subject, axis)  # Fallback
```

## Summary

Nock tree addressing: axis system for binary tree navigation. Axis 1=root, 2=head, 3=tail, 2n=head(n), 2n+1=tail(n). Binary encoding: convert axis to binary, strip leading 1, interpret bits as path (0=head, 1=tail). Crashes: axis 0 (forbidden), slot in atom when axis>1. Implementation: iterative with bit manipulation (fast), recursive (simple), memoized (repeated access). Common pattern: tuple access via axes 2, 6, 14, 30 (formula: 2, then 2n+2).
