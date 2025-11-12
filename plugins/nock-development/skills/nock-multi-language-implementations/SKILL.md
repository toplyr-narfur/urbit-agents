---
name: nock-multi-language-implementations
description: Language-specific Nock implementation patterns across C, Python, Rust, Haskell, JavaScript, and others. Covers idiomatic approaches, type systems, memory models, and language-specific optimizations. Use when porting Nock to new languages or understanding different implementation philosophies.
---

# Nock Multi-Language Implementations

Language-specific patterns for implementing Nock interpreters across different programming paradigms.

## C Implementation

**Characteristics**: Manual memory, maximum performance, reference implementation

```c
typedef struct noun noun;
struct noun {
    uint32_t mug;  // Hash cache
    union {
        uint64_t atom;
        struct { noun* head; noun* tail; } cell;
    };
};

noun* nock(noun* subject, noun* formula) {
    // Pattern match on formula type
    // Manual memory management
    // GMP for big integers
}
```

**Libraries**: GMP (arbitrary precision), custom allocators
**Resources**: https://github.com/urbit/urbit/tree/master/pkg/noun

## Python Implementation

**Characteristics**: Rapid prototyping, dynamic typing, easy debugging

```python
from dataclasses import dataclass

@dataclass
class Atom:
    value: int  # Native int (arbitrary precision)

@dataclass
class Cell:
    head: 'Noun'
    tail: 'Noun'

def nock(subject, formula):
    # Pattern matching via if/elif
    # Exceptions for crashes
    # functools.lru_cache for memoization
```

**Best Practices**: Use dataclasses, type hints, pytest for testing

## Rust Implementation

**Characteristics**: Memory safety, zero-cost abstractions, performance

```rust
#[derive(Clone, Debug, PartialEq)]
pub enum Noun {
    Atom(u64),  // Or BigUint
    Cell(Rc<Noun>, Rc<Noun>),
}

fn nock(subject: &Noun, formula: &Noun) -> Result<Noun, NockError> {
    match formula {
        Noun::Cell(op, rest) => match op {
            Noun::Atom(0) => slot(subject, rest),
            // ... all rules
        },
        _ => Err(NockError::BadFormula),
    }
}
```

**Libraries**: num-bigint, Rc/Arc for sharing
**Benefits**: Compile-time safety, pattern matching

## Haskell Implementation

**Characteristics**: Functional purity, lazy evaluation, elegant types

```haskell
data Noun = Atom Integer | Cell Noun Noun
    deriving (Show, Eq)

nock :: Noun -> Noun -> Either NockError Noun
nock subj (Cell (Atom 0) axis) = slot subj axis
nock subj (Cell (Atom 1) constant) = Right constant
nock subj (Cell (Atom 2) (Cell b c)) =
    nock subj b >>= \newSubj ->
    nock subj c >>= \newForm ->
    nock newSubj newForm
-- ... all rules
```

**Benefits**: Pattern matching, monadic error handling, laziness

## JavaScript Implementation

**Characteristics**: Browser/Node compatibility, JSON integration

```javascript
class Noun {
    static atom(n) { return {type: 'atom', value: BigInt(n)}; }
    static cell(h, t) { return {type: 'cell', head: h, tail: t}; }
}

function nock(subject, formula) {
    if (formula.type !== 'cell') throw new Error("bad formula");

    const op = slot(formula, 2);
    const rest = slot(formula, 3);

    switch(op.value) {
        case 0n: return slot(subject, rest);
        case 1n: return rest;
        // ... all rules
    }
}
```

**Libraries**: BigInt (ES2020+), trampolines for TCO

## Summary

C: manual memory (GMP), maximum performance, reference implementation. Python: dataclasses, native BigInt, rapid prototyping. Rust: enum pattern matching, Rc sharing, Result types. Haskell: algebraic data types, Either monad, lazy evaluation. JavaScript: BigInt, objects for nouns, trampoline TCO. Choose language based on: performance needs (C/Rust), prototyping speed (Python), type safety (Rust/Haskell), deployment target (JavaScript for browser).
