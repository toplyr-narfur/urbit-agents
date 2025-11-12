---
name: nock-operators
description: Complete reference for Nock's 6 prefix operators (?, +, =, /, #, *) with formal semantics, implementation patterns, and edge cases. Covers type testing, increment, equality, tree addressing, editing, and evaluation. Use when implementing Nock operators, understanding operator semantics, or debugging operator behavior.
---

# Nock Operators Skill

Complete reference for Nock's six prefix operators that provide all computational primitives.

## Operator Overview

| Operator | Name | Purpose | Example |
|----------|------|---------|---------|
| `?` | Type Test | Cell vs Atom | `?[42]` → `1` (is atom) |
| `+` | Increment | Add 1 | `+[41]` → `42` |
| `=` | Equality | Deep compare | `=[1 1]` → `0` (equal) |
| `/` | Slot | Tree addressing | `/[2 [a b]]` → `a` |
| `#` | Edit | Replace (via hints) | Implicit in Rule 10 |
| `*` | Nock | Evaluate formula | `*[subj formula]` → result |

## `?` - Type Test (Cell vs Atom)

### Semantics
```
?[atom] → 1  (yes, is atom)
?[cell] → 0  (no, is cell)
```

### Implementation
```python
def type_test(noun):
    """? operator: returns 1 if atom, 0 if cell."""
    return 1 if is_atom(noun) else 0
```

### Loobean Logic
Nock uses **loobean** (backwards boolean):
- `0` = yes/true
- `1` = no/false

Why? Historical Lisp convention where `nil`=false, anything else=true.

### Examples
```
?[0]        → 1  # Atom
?[9999]     → 1  # Atom
?[[1 2]]    → 0  # Cell
?[[[a] b]]  → 0  # Cell (even nested)
```

## `+` - Increment

### Semantics
```
+[atom] → atom + 1
+[cell] → CRASH
```

### Implementation
```python
def increment(noun):
    ""+ operator: increments atom, crashes on cell."""
    if is_cell(noun):
        raise NockCrash("increment cell")
    return noun + 1
```

### Examples
```
+[0]    → 1
+[41]   → 42
+[999]  → 1000
+[[1 2]]→ CRASH (cannot increment cells)
```

### Use Cases
- **All arithmetic**: Builds addition, multiplication, etc.
- **Counting**: Loops, recursion counters
- **Successor**: Foundation of Peano arithmetic

## `=` - Equality (Deep Structural)

### Semantics
```
=[a b] → 0 if a ≡ b (equal)
=[a b] → 1 if a ≠ b (not equal)
```

### Implementation
```python
def noun_equals(a, b):
    """= operator: deep structural equality."""
    if type(a) != type(b):
        return 1  # Different types → not equal

    if is_atom(a):
        return 0 if a == b else 1

    # Both cells: recursive comparison
    head_eq = noun_equals(a.head, b.head)
    tail_eq = noun_equals(a.tail, b.tail)

    return 0 if (head_eq == 0 and tail_eq == 0) else 1
```

### Examples
```
=[0 0]          → 0  # Equal
=[42 42]        → 0  # Equal
=[1 2]          → 1  # Not equal
=[[1 2] [1 2]]  → 0  # Equal (deep comparison)
=[[1 2] [1 3]]  → 1  # Not equal
=[0 [0]]        → 1  # Different types (atom vs cell)
```

## `/` - Slot (Tree Addressing)

### Semantics
```
/[1 noun]    → noun           # Root (identity)
/[2 [a b]]   → a              # Head
/[3 [a b]]   → b              # Tail
/[2n a]      → /[n /[2 a]]    # Head of axis n
/[2n+1 a]    → /[n /[3 a]]    # Tail of axis n
/[0 a]       → CRASH          # Forbidden
/[n atom]    → CRASH (n>1)    # Cannot slot into atom
```

### Binary Navigation Algorithm
```
Axis in binary:
  1    = 1      → root
  2    = 10     → head (path: 0)
  3    = 11     → tail (path: 1)
  4    = 100    → head,head (path: 0,0)
  5    = 101    → head,tail (path: 0,1)
  6    = 110    → tail,head (path: 1,0)
  7    = 111    → tail,tail (path: 1,1)

Algorithm:
1. Convert axis to binary
2. Strip leading 1
3. For each bit: 0=head, 1=tail
```

### Implementation
```python
def slot(subject, axis):
    """/ operator: tree addressing."""
    if axis == 0:
        raise NockCrash("axis 0")
    if axis == 1:
        return subject
    if is_atom(subject):
        raise NockCrash("slot in atom")

    # Binary navigation
    if axis % 2 == 0:  # Even: head
        return slot(subject.head, axis // 2)
    else:  # Odd: tail
        return slot(subject.tail, axis // 2)
```

### Examples
```
/[1 [a b]]        → [a b]     # Identity
/[2 [a b]]        → a         # Head
/[3 [a b]]        → b         # Tail
/[4 [[a b] c]]    → a         # Head of head
/[5 [[a b] c]]    → b         # Tail of head
/[6 [[a b] c]]    → c         # Head of tail
/[7 [a [b c]]]    → c         # Tail of tail
/[0 anything]     → CRASH     # Axis 0 forbidden
/[2 atom]         → CRASH     # Cannot slot into atom
```

## `#` - Edit (Implicit in Hints)

### Semantics
```
# operator only appears implicitly in Rule 10 (hints)
Used for:
- Selective replacement in tree
- Metadata annotations
- Jet hints
```

### No Direct Usage
The `#` operator doesn't appear in user formulas directly—it's embedded in Rule 10.

## `*` - Nock (The Evaluator)

### Semantics
```
*[subject formula] → nock(subject, formula)
```

This is **the Nock function itself**—evaluates formula against subject.

### Recursive Definition
```
*[a [0 b]]    → /[b a]           # Slot
*[a [1 b]]    → b                # Constant
*[a [2 b c]]  → *[*[a b] *[a c]] # Evaluate
... (all 13 rules)
```

### Examples
```
*[10 [0 1]]        → 10          # Slot (identity)
*[_ [1 42]]        → 42          # Constant
*[10 [4 [0 1]]]    → 11          # Increment
```

## Operator Composition

### Building Complex Operations

**Addition** (using only increment and recursion):
```hoon
|=  [a=@ b=@]  # Takes two atoms
^-  @         # Returns atom
?:  =(b 0)    # If b == 0
  a           # Return a
$(a +(a), b (dec b))  # Else: recurse with a+1, b-1
```

**Multiplication** (using addition):
```hoon
|=  [a=@ b=@]
^-  @
?:  =(b 0)
  0
(add a $(b (dec b)))
```

### Operator Hierarchy
```
1. / (slot) - Most primitive, used by all others
2. ? + = - Axiomatic operations
3. * - Evaluation (uses all others)
4. # - Editing (high-level)
```

## Common Pitfalls

1. **Axis 0**: Always crashes, no exception
2. **Loobean Confusion**: `0=true`, `1=false` (backwards!)
3. **Increment Cells**: `+[[1 2]]` crashes (atoms only)
4. **Slot in Atom**: `/[2 42]` crashes (axis>1 in atom)
5. **Equality Returns Loobean**: `=[a b]` returns `0` or `1`, not boolean

## Performance Considerations

### Slot Optimization
```rust
// Fast slot using bit manipulation
fn slot_fast(subject: &Noun, mut axis: u64) -> Result<Noun> {
    if axis == 0 { return Err(Crash); }
    if axis == 1 { return Ok(subject.clone()); }

    let depth = 63 - axis.leading_zeros();
    let mut current = subject;

    for i in (0..depth).rev() {
        match current {
            Noun::Atom(_) => return Err(Crash),
            Noun::Cell(h, t) => {
                current = if (axis >> i) & 1 == 0 { h } else { t };
            }
        }
    }

    Ok(current.clone())
}
```

### Memoization
```python
from functools import lru_cache

@lru_cache(maxsize=10000)
def slot_cached(subject_id, axis):
    """Cached slot for repeated lookups."""
    subject = get_noun_by_id(subject_id)
    return slot(subject, axis)
```

## Summary

Six Nock operators: `?` (type test: 1=atom, 0=cell), `+` (increment atom, crash cell), `=` (deep equality: 0=equal, 1=not), `/` (tree addressing via binary axis), `#` (editing via hints), `*` (evaluation itself). Loobean logic: 0=yes/true, 1=no/false. Slot navigation: axis 1=root, 2=head, 3=tail, 2n=head of n, 2n+1=tail of n. Crashes: axis 0, slot in atom (axis>1), increment cell. All arithmetic builds from increment; all data access via slot.
