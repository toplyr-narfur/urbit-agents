---
name: nock-essentials
description: Foundational Nock concepts including nouns (atoms and cells), reduction model, evaluation semantics, and basic data representation. Covers the minimal computational substrate: natural numbers, ordered pairs, and pattern matching. Use when learning Nock fundamentals, understanding the noun model, or implementing basic data structures for Nock interpreters.
---

# Nock Essentials Skill

Core foundational concepts for Nock assembly language: nouns, reduction model, and evaluation semantics.

## What is Nock?

**Nock** is a minimal, Turing-complete combinator calculus functioning as Urbit's assembly language and virtual machine specification.

### Key Characteristics
- **Minimal**: 13 reduction rules (0-12), 6 operators
- **Deterministic**: Same input always produces same output
- **Turing Complete**: Can compute any computable function
- **Frozen**: Specification will never change (frozen at Nock 4K)
- **Functional**: Pure, no side effects, referentially transparent

## Nouns: The Only Data Type

### Definition

```
noun := atom | cell

atom := unsigned integer (0, 1, 2, 3, ...)
cell := [head tail] where head and tail are nouns
```

**Everything is a noun**. No strings, floats, booleans, structs—just atoms and cells.

### Examples

```
Atoms:
0
42
9999999999999
340282366920938463463374607431768211455  # Large atoms are valid

Cells:
[1 2]              # Cell of two atoms
[[1 2] 3]          # Cell of cell and atom
[1 [2 3]]          # Cell of atom and cell
[[[1 2] 3] [4 5]]  # Nested cells

Invalid:
[]                 # Empty not allowed (must be atom or cell)
"hello"            # No strings (must represent as atoms or cells)
3.14               # No floats (only natural numbers)
```

### Binary Tree Structure

Every noun is a binary tree:
```
Atom: 42
  └─ leaf

Cell: [1 2]
   [·]
  /   \
 1     2

Nested: [[1 2] [3 4]]
      [·]
     /   \
   [·]   [·]
  /  \  /  \
 1   2 3   4

Deep: [1 [2 [3 4]]]
   [·]
  /   \
 1    [·]
     /   \
    2    [·]
        /   \
       3     4
```

## The Nock Function

### Signature

```
nock: (noun, noun) → noun
nock(subject, formula) = result
```

- **subject**: Data (environment, context)
- **formula**: Code (instructions to execute)
- **result**: Computed noun

### Evaluation Model

Nock reduces `(subject, formula)` pairs by pattern matching the formula against 13 rules:

```
nock(subject, formula):
  Match formula:
    [0 axis]     → slot(subject, axis)        # Tree addressing
    [1 const]    → const                      # Return constant
    [2 b c]      → nock(nock(a,b), nock(a,c)) # Evaluate
    [3 b]        → ?nock(a,b)                 # Cell test
    [4 b]        → +nock(a,b)                 # Increment
    [5 b c]      → =(nock(a,b), nock(a,c))    # Equality
    [6 b c d]    → if nock(a,b) then c else d # Conditional
    [7 b c]      → nock(nock(a,b), c)         # Compose
    [8 b c]      → nock([nock(a,b) a], c)     # Push
    [9 b c]      → nock(nock(a,c), slot(·,b)) # Call
    [10 b c]     → nock(a, c)  # Hint (simplified)
    [11 b c]     → nock(a, c)  # Hint (reserved)
    [12 b c]     → slot(a, c)  # Scry (reserved)
```

### Crash Semantics

If no rule matches or invalid operation occurs, Nock **crashes** (halts with error):

- Axis 0 (forbidden)
- Slot in atom (when axis > 1)
- Increment cell
- Malformed formula
- Explicit crash (rule 1)

## Data Representation

### Atom Encoding

Atoms are **natural numbers** but can encode any data:

```
Text (ASCII/UTF-8):
'hello' = [104 101 108 108 111]  # List of ASCII values
        = 478560413032  # Or single big atom (bytes concatenated)

Booleans (Loobean):
0 = yes (true)
1 = no (false)

Lists:
[1 2 3] = [1 [2 [3 0]]]  # Null-terminated (0 = nil)

Trees:
Binary search tree represented as nested cells
```

### Cell Encoding

Cells represent:
- **Pairs**: `[key value]`
- **Lists**: `[first rest]` (cons cells)
- **Trees**: `[left right]`
- **Tuples**: `[a [b [c 0]]]` (nested pairs)
- **Functions**: `[battery payload]` (cores)

## Reduction Example

### Simple: Increment 10

```
nock(10, [4 0 1])

Step 1: Match rule 4 (increment)
  [a 4 b] → +nock(a, b)

Step 2: Evaluate [0 1]
  nock(10, [0 1])

Step 3: Match rule 0 (slot/identity)
  [a 0 1] → /[1 a] → a
  Result: 10

Step 4: Apply increment
  +[10] → 11

Final result: 11
```

### Complex: Conditional

```
nock(_, [6 [1 0] [1 100] [1 200]])

Step 1: Match rule 6 (if-then-else)
  [a 6 test then else]

Step 2: Evaluate test
  nock(_, [1 0]) → 0  (constant)

Step 3: Test = 0 (loobean "yes"), take "then" branch
  nock(_, [1 100]) → 100

Final result: 100
```

## Implementation Patterns

### Python: Tagged Union

```python
from dataclasses import dataclass
from typing import Union

@dataclass
class Atom:
    value: int

@dataclass
class Cell:
    head: 'Noun'
    tail: 'Noun'

Noun = Union[Atom, Cell]

def is_atom(noun: Noun) -> bool:
    return isinstance(noun, Atom)

def is_cell(noun: Noun) -> bool:
    return isinstance(noun, Cell)

# Example usage
atom = Atom(42)
cell = Cell(Atom(1), Atom(2))
nested = Cell(cell, Atom(3))  # [[1 2] 3]
```

### Rust: Enum + Box

```rust
#[derive(Debug, Clone, PartialEq, Eq)]
pub enum Noun {
    Atom(u64),  // Or BigInt for arbitrary precision
    Cell(Box<Noun>, Box<Noun>),
}

impl Noun {
    pub fn is_atom(&self) -> bool {
        matches!(self, Noun::Atom(_))
    }

    pub fn is_cell(&self) -> bool {
        matches!(self, Noun::Cell(_, _))
    }

    pub fn make_cell(head: Noun, tail: Noun) -> Noun {
        Noun::Cell(Box::new(head), Box::new(tail))
    }
}

// Example usage
let atom = Noun::Atom(42);
let cell = Noun::make_cell(Noun::Atom(1), Noun::Atom(2));
```

### Haskell: Algebraic Data Type

```haskell
data Noun = Atom Integer
          | Cell Noun Noun
          deriving (Show, Eq)

isAtom :: Noun -> Bool
isAtom (Atom _) = True
isAtom _ = False

isCell :: Noun -> Bool
isCell (Cell _ _) = True
isCell _ = False

-- Example usage
atom = Atom 42
cell = Cell (Atom 1) (Atom 2)
nested = Cell cell (Atom 3)  -- [[1 2] 3]
```

### JavaScript: Objects

```javascript
class Noun {
    static atom(value) {
        return { type: 'atom', value };
    }

    static cell(head, tail) {
        return { type: 'cell', head, tail };
    }

    static isAtom(noun) {
        return noun.type === 'atom';
    }

    static isCell(noun) {
        return noun.type === 'cell';
    }
}

// Example usage
const atom = Noun.atom(42);
const cell = Noun.cell(Noun.atom(1), Noun.atom(2));
const nested = Noun.cell(cell, Noun.atom(3));  // [[1 2] 3]
```

## Type Testing Implementation

```python
def noun_type_test(noun):
    """Nock ? operator: type test."""
    if is_atom(noun):
        return Atom(1)  # Yes, is atom (loobean)
    else:
        return Atom(0)  # No, is cell

def noun_equals(a, b):
    """Nock = operator: deep structural equality."""
    if is_atom(a) and is_atom(b):
        return Atom(0) if a.value == b.value else Atom(1)

    if is_cell(a) and is_cell(b):
        head_eq = noun_equals(a.head, b.head)
        tail_eq = noun_equals(a.tail, b.tail)

        # Both must be equal (both return 0)
        if head_eq.value == 0 and tail_eq.value == 0:
            return Atom(0)
        else:
            return Atom(1)

    # Different types (atom vs cell)
    return Atom(1)
```

## Memory Considerations

### Atom Size
- **Atoms can be arbitrarily large**: Use BigInt or arbitrary-precision libraries
- **C**: GMP (GNU Multiple Precision) library
- **Python**: Native `int` (already arbitrary precision)
- **Rust**: `num-bigint` crate
- **JavaScript**: `BigInt` (ES2020+)
- **Haskell**: Native `Integer` type

### Cell Allocation
- **Heap allocation**: Cells typically require dynamic memory
- **Reference counting**: Track noun lifetimes
- **Garbage collection**: Automatic memory management
- **Arena allocation**: Batch allocation for performance

## Why This Design?

### Minimalism
- **Simplicity**: Only two data types (atom, cell)
- **Uniformity**: Everything is a noun
- **Frozen spec**: Never changes, perfect for VM substrate

### Universality
- **Turing complete**: Can compute anything computable
- **Self-contained**: No dependencies on host environment
- **Deterministic**: Perfect for distributed consensus (Urbit)

### Practicality
- **Binary trees**: Efficient addressing and navigation
- **Immutability**: Safe concurrency and caching
- **Serialization**: Trivial to serialize/deserialize nouns

## Common Patterns

### Lists
```
[1 2 3 4] represented as:
[1 [2 [3 [4 0]]]]  # 0 = nil terminator

Implementation:
nil = Atom(0)
list = Cell(Atom(1), Cell(Atom(2), Cell(Atom(3), Cell(Atom(4), nil))))
```

### Key-Value Pairs
```
{"name": "Alice", "age": 30} as:
[[["name" "Alice"] ["age" 30]] 0]  # List of pairs
```

### Binary Trees
```
      5
     / \
    3   7
   / \
  1   4

Represented as:
[5 [[3 [1 4]] 7]]
```

## Summary

Nock essentials: **nouns** (atoms = natural numbers, cells = ordered pairs), **reduction** (pattern match formula against 13 rules), **evaluation** (nock(subject, formula) = result), **crashes** (explicit failures), **minimalism** (frozen spec, two data types, Turing complete). Atoms encode all data via natural numbers; cells encode all structure via binary trees. Implementation requires: data structure for noun, pattern matching for rules, tree addressing for slot, type testing and equality.
