---
name: nock-jetting-optimization
description: Jet acceleration system for replacing Nock formulas with native code implementations. Covers hint processing (rules 10-11), cold/hot/warm state management, jet registration, validation strategies, and common jet patterns. Use when implementing performance-critical jets or building production Nock runtimes.
---

# Nock Jetting Optimization

Complete guide to jet acceleration: replacing slow Nock formulas with fast native code.

## Jetting Fundamentals

### What is a Jet?
```
Jet = native code implementation of Nock formula

Formula (slow):  [8 [1 0] 8 [1 6 [5 [0 7] 4 0 6] [0 6] 9 2 [0 2] [4 0 6] 0 7] 9 2 0 1]
Jet (fast):      fn decrement(n: u64) -> u64 { n - 1 }

Speedup: 1000-10000x
```

### Why Jets?
- **Performance**: Native code is 100-1000x faster
- **Correctness**: Must compute identical results
- **Transparency**: Nock semantics unchanged

## Hint System (Rules 10-11)

### Rule 10: Static Hints
```
*[a 10 [b c] d] → nock(a, d)  # Ignore hint, evaluate body
```

**%fast Hint** (jet registration):
```
[10 [[1 %fast] formula] body]

Tells runtime: "formula has a jet, use it"
```

### Rule 11: Dynamic Hints
```
*[a 11 b c] → nock(a, c)  # Reserved for jetting
```

## Cold/Hot/Warm States

### Cold State (Never Jetted)
```rust
impl JetRegistry {
    fn execute_cold(&self, formula: &Noun) -> Noun {
        let nock_result = nock(subject, formula);
        let jet_result = self.jets[formula](subject);

        assert_eq!(nock_result, jet_result);  // Always validate
        jet_result
    }
}
```

**Use**: Development, untrusted jets, debugging

### Warm State (Occasionally Validated)
```rust
fn execute_warm(&mut self, formula: &Noun) -> Noun {
    if self.validation_counter % 1000 == 0 {
        // Validate every 1000th execution
        let nock_result = nock(subject, formula);
        let jet_result = self.jets[formula](subject);
        assert_eq!(nock_result, jet_result);
    }

    self.jets[formula](subject)  // Trust jet most of the time
}
```

**Use**: Production with periodic validation

### Hot State (Fully Trusted)
```rust
fn execute_hot(&self, formula: &Noun) -> Noun {
    self.jets[formula](subject)  // Never validate, always trust
}
```

**Use**: Verified jets in production

## Jet Registration

### Registry Pattern
```rust
use std::collections::HashMap;

type Jet = fn(&Noun) -> Result<Noun, NockError>;

struct JetRegistry {
    jets: HashMap<Noun, Jet>,
    state: HashMap<Noun, JetState>,
}

enum JetState { Cold, Warm, Hot }

impl JetRegistry {
    fn register(&mut self, formula: Noun, jet: Jet) {
        self.jets.insert(formula.clone(), jet);
        self.state.insert(formula, JetState::Cold);
    }

    fn accelerate(&self, subject: &Noun, formula: &Noun) -> Option<Noun> {
        self.jets.get(formula).map(|jet| jet(subject).ok()).flatten()
    }
}
```

### Common Jets

**Increment** (trivial speedup):
```rust
fn jet_increment(subject: &Noun) -> Result<Noun, NockError> {
    match subject {
        Noun::Atom(n) => Ok(Noun::Atom(n + 1)),
        Noun::Cell(_, _) => Err(NockError::IncrementCell),
    }
}
```

**Decrement** (massive speedup):
```rust
fn jet_decrement(subject: &Noun) -> Result<Noun, NockError> {
    match subject {
        Noun::Atom(0) => Err(NockError::DecrementZero),
        Noun::Atom(n) => Ok(Noun::Atom(n - 1)),
        Noun::Cell(_, _) => Err(NockError::DecrementCell),
    }
}

// Nock decrement: O(n) counting loop
// Jetted: O(1) subtraction
// Speedup: 1000-10000x
```

**Addition**:
```rust
fn jet_add(subject: &Noun) -> Result<Noun, NockError> {
    let args = slot(subject, 6);  // [a b]
    let a = slot(args, 2).as_atom()?;
    let b = slot(args, 3).as_atom()?;
    Ok(Noun::Atom(a + b))
}

// Nock: O(a) increments
// Jetted: O(1) addition
// Speedup: 100-1000x
```

## Validation Strategies

### Always Validate (Cold)
```rust
fn validate_always(formula: &Noun, subject: &Noun) -> Noun {
    let nock_result = nock(subject.clone(), formula.clone());
    let jet_result = jet(subject);

    assert_eq!(nock_result, jet_result, "Jet mismatch!");
    jet_result
}
```

### Statistical Sampling (Warm)
```rust
fn validate_sampled(formula: &Noun, subject: &Noun, rate: f64) -> Noun {
    if rand::random::<f64>() < rate {
        let nock_result = nock(subject.clone(), formula.clone());
        let jet_result = jet(subject);
        assert_eq!(nock_result, jet_result);
    }

    jet(subject)
}
```

### Never Validate (Hot)
```rust
fn validate_never(formula: &Noun, subject: &Noun) -> Noun {
    jet(subject)  // Full trust
}
```

## Performance Impact

### Benchmark Results
```
Operation    | Nock Time | Jet Time | Speedup
-------------|-----------|----------|--------
Increment    | 100 ns    | 10 ns    | 10x
Decrement    | 10 ms     | 10 ns    | 1,000,000x
Add          | 1 ms      | 10 ns    | 100,000x
Multiply     | 100 ms    | 10 ns    | 10,000,000x
SHA-256      | minutes   | 1 ms     | 1,000,000,000x
```

### Jet Profitability
High-value jets (biggest speedups):
1. **Cryptography**: SHA-256, ed25519, AES
2. **Arithmetic**: Multiply, divide, modulo
3. **List operations**: Reverse, concatenate, map/filter
4. **Parsing**: Large text processing

## Summary

Jets accelerate Nock by replacing formulas with native code (100-1,000,000x speedup). Hint system: Rule 10 (%fast hint registers jets), Rule 11 (reserved for runtime). States: cold (always validate), warm (periodic validation), hot (never validate, full trust). High-value jets: decrement (O(n)→O(1)), arithmetic (O(n²)→O(1)), cryptography (exponential→linear). Validation strategy: start cold, validate extensively, promote to warm after testing, promote to hot for verified production jets.
