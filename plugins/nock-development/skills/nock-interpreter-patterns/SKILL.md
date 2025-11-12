---
name: nock-interpreter-patterns
description: Design patterns and architectures for building Nock virtual machines including evaluation loops, pattern matching strategies, error handling, tail call optimization, and memory management. Use when building Nock interpreters, optimizing evaluation performance, or understanding interpreter architecture.
---

# Nock Interpreter Patterns Skill

Architectural patterns and best practices for building high-quality Nock interpreters.

## Core Interpreter Architecture

### Pattern 1: Direct Pattern Matching (Simple)
```python
def nock(subject, formula):
    """Direct pattern matching on formula head."""
    if not is_cell(formula):
        raise NockCrash("formula must be cell")

    op, rest = formula.head, formula.tail

    if op == 0: return slot(subject, rest)
    if op == 1: return rest
    if op == 2: return nock(nock(subject, rest.head), nock(subject, rest.tail))
    # ... rules 3-12
    raise NockCrash(f"unknown operator: {op}")
```

**Pros**: Simple, clear, easy to understand
**Cons**: No optimization, recursive (stack risk)

### Pattern 2: Jump Table (Fast)
```c
typedef noun (*rule_handler)(noun subject, noun rest);

rule_handler jump_table[13] = {
    rule_0_slot,
    rule_1_constant,
    rule_2_evaluate,
    // ... rules 3-12
};

noun nock(noun subject, noun formula) {
    if (!is_cell(formula)) crash("bad formula");

    noun op = slot(formula, 2);
    noun rest = slot(formula, 3);

    if (!is_atom(op) || op > 12) crash("unknown operator");

    return jump_table[op](subject, rest);
}
```

**Pros**: Fast dispatch, compiler-friendly
**Cons**: More code, language-specific

### Pattern 3: Bytecode Compilation (Fastest)
```rust
enum Instruction {
    Slot(u64),
    Constant(Noun),
    Evaluate(Box<Instruction>, Box<Instruction>),
    // ... all 13 rules as bytecode
}

fn compile(formula: &Noun) -> Instruction {
    // Translate Nock formula to bytecode once
}

fn execute(subject: &Noun, bytecode: &Instruction) -> Noun {
    // Execute precompiled bytecode (much faster)
}
```

**Pros**: Maximum performance, enables optimizations
**Cons**: Complexity, compilation overhead

## Tail Call Optimization

### Problem: Stack Overflow
```python
# Without TCO: stack grows with recursion depth
def nock_recursive(subject, formula):
    # ... rules ...
    if op == 9:  # Call
        core = nock(subject, c)  # Recursive call
        return nock(core, arm)    # Tail call (could optimize)
```

### Solution 1: Trampoline Pattern
```javascript
function nock_tco(subject, formula) {
    let thunk = () => nock_impl(subject, formula);

    // Execute thunks until we get a value
    while (typeof thunk === 'function') {
        thunk = thunk();
    }

    return thunk;
}

function nock_impl(subject, formula) {
    // Instead of: return nock(new_subject, new_formula)
    // Return: return () => nock_impl(new_subject, new_formula)
}
```

### Solution 2: Explicit Stack
```python
def nock_iterative(subject, formula):
    """Iterative evaluation with explicit stack."""
    stack = [(subject, formula)]

    while stack:
        subject, formula = stack.pop()

        # ... pattern match ...

        if op == 2:  # Evaluate
            # Push continuations onto stack
            stack.append((subject, rest.head))
            stack.append(('EVAL', rest.tail))  # Marker for second evaluation

    return result
```

## Error Handling Patterns

### Pattern 1: Exceptions (Python/Java)
```python
class NockCrash(Exception):
    pass

def nock(subject, formula):
    if axis == 0:
        raise NockCrash("axis 0 forbidden")
    # ... evaluation ...
```

### Pattern 2: Result Type (Rust)
```rust
enum NockError {
    Crash(&'static str),
    AxisZero,
    SlotInAtom,
    IncrementCell,
}

fn nock(subject: &Noun, formula: &Noun) -> Result<Noun, NockError> {
    if axis == 0 {
        return Err(NockError::AxisZero);
    }
    // ... evaluation ...
    Ok(result)
}
```

### Pattern 3: Maybe/Option (Haskell)
```haskell
nock :: Noun -> Noun -> Either NockError Noun
nock subject formula
    | axis == 0 = Left (Crash "axis 0")
    | otherwise = Right result
```

## Memory Management Patterns

### Pattern 1: Reference Counting (C)
```c
typedef struct noun {
    uint32_t refcount;
    enum { ATOM, CELL } type;
    union {
        uint64_t atom;
        struct { noun* head; noun* tail; } cell;
    };
} noun;

void noun_incref(noun* n) { n->refcount++; }

void noun_decref(noun* n) {
    if (--n->refcount == 0) {
        if (n->type == CELL) {
            noun_decref(n->cell.head);
            noun_decref(n->cell.tail);
        }
        free(n);
    }
}
```

### Pattern 2: Arena Allocation
```c
typedef struct arena {
    char* memory;
    size_t offset;
    size_t capacity;
} arena;

noun* arena_alloc_cell(arena* a, noun* head, noun* tail) {
    noun* cell = (noun*)(a->memory + a->offset);
    a->offset += sizeof(noun);
    cell->type = CELL;
    cell->cell.head = head;
    cell->cell.tail = tail;
    return cell;
}

// Batch free entire arena at once (fast)
void arena_reset(arena* a) {
    a->offset = 0;  # Reset, don't free individual nouns
}
```

### Pattern 3: Garbage Collection (Haskell/Python)
```haskell
-- Automatic GC handles memory
data Noun = Atom Integer | Cell Noun Noun
-- GC reclaims unreachable nouns automatically
```

## Optimization Patterns

### Memoization (Pure Function Caching)
```python
from functools import lru_cache

@lru_cache(maxsize=10000)
def slot_cached(subject_id, axis):
    subject = get_noun_by_id(subject_id)
    return slot(subject, axis)

# Speedup: 10-100x for repeated slot lookups
```

### Interning (Deduplicate Atoms)
```python
_atom_cache = {}

def make_atom(value):
    """Interned atoms: same value = same object."""
    if value not in _atom_cache:
        _atom_cache[value] = Atom(value)
    return _atom_cache[value]

# Benefit: Equality by reference (fast), reduced memory
```

### Lazy Evaluation
```haskell
-- Defer computation until needed
nock :: Noun -> Noun -> Noun
nock subject formula = case formula of
    Cell (Atom 7) rest ->
        let newSubj = nock subject (fst rest)  # Lazy: only eval if needed
        in nock newSubj (snd rest)
```

## Testing Patterns

### Unit Tests
```python
def test_slot():
    assert slot([1, 2], 1) == [1, 2]  # Identity
    assert slot([1, 2], 2) == 1        # Head
    assert slot([1, 2], 3) == 2        # Tail

def test_increment():
    assert nock(_, [4, [1, 41]]) == 42
    with pytest.raises(NockCrash):
        nock(_, [4, [1, [1, 2]]])  # Cannot increment cell
```

### Property-Based Testing
```python
from hypothesis import given, strategies as st

@given(st.integers(min_value=0))
def test_increment_monotonic(n):
    """Increment always increases value."""
    result = nock(_, [4, [1, n]])
    assert result == n + 1

@given(st.integers(min_value=1, max_value=1000))
def test_slot_identity(axis):
    """Slot identity: /[1 n] = n."""
    noun = random_noun()
    assert slot(noun, 1) == noun
```

### Benchmark Suite
```python
def benchmark_decrement():
    """Stress test with complex formula."""
    formula = DECREMENT_FORMULA
    for n in [10, 100, 1000]:
        start = time.time()
        result = nock(n, formula)
        elapsed = time.time() - start
        assert result == n - 1
        print(f"decrement({n}): {elapsed*1000:.2f}ms")
```

## Summary

Interpreter patterns: direct pattern matching (simple), jump table (fast), bytecode compilation (fastest). Tail call optimization: trampoline pattern (JavaScript), explicit stack (Python), language TCO (Rust). Error handling: exceptions (Python), Result types (Rust), Either monad (Haskell). Memory: reference counting (C manual), arena allocation (batch free), GC (automatic). Optimizations: memoization (LRU cache), interning (deduplicate atoms), lazy evaluation (Haskell). Testing: unit tests (each rule), property-based (invariants), benchmarks (performance).
