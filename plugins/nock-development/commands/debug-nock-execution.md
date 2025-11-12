---
name: debug-nock-execution
description: Systematic debugging workflow for troubleshooting Nock formula execution from error analysis to resolution.
subagent_type: nock-specification-expert
---

# Debug Nock Execution Command

5-phase systematic debugging workflow for resolving Nock formula execution issues.

## Phase 1: Error Classification

**Goal**: Categorize the error to determine debugging strategy

**Error Types**:
1. **Crash**: Nock explicitly crashed (axis 0, slot in atom, increment cell)
2. **Wrong Result**: Formula executes but produces incorrect output
3. **Infinite Loop**: Formula never terminates
4. **Performance**: Formula is correct but too slow

**Tasks**:
1. Run formula and capture error/output
2. Identify error category
3. Collect context: subject, formula, expected result
4. Check for common pitfalls (loobean confusion, axis off-by-one)

**Success**: Error categorized, initial diagnosis complete

---

## Phase 2: Formula Analysis

**Goal**: Understand what the formula is supposed to do

**Tasks**:
1. **Break down formula structure**:
   - Identify operator (head of formula)
   - Identify arguments (tail of formula)
   - Map to reduction rule (0-12)
2. **Trace intended execution**:
   - What should each rule do?
   - What intermediate values are expected?
3. **Identify suspicious patterns**:
   - Axis 0 (always crashes)
   - Slot in atom (crash if axis > 1)
   - Increment cell (always crashes)
   - Unknown operator (crash)

**Success**: Formula structure understood, suspicious patterns identified

---

## Phase 3: Step-by-Step Trace

**Goal**: Execute formula manually to find where it diverges

**Tasks**:
1. **Manual reduction** (step-by-step):
   ```
   *[10 [4 [0 1]]]
     ↓ Rule 4 (increment)
   +*[10 [0 1]]
     ↓ Rule 0 (slot, axis 1 = identity)
   +/[1 10]
     ↓ Slot (axis 1 = identity)
   +[10]
     ↓ Increment
   11
   ```
2. **Compare with actual execution**:
   - Run interpreter step-by-step
   - Identify first divergence point
3. **Instrument code**:
   - Add logging at each rule
   - Print subject, formula, result
4. **Isolate problem**:
   - Extract minimal failing case
   - Remove complexity until bug is clear

**Success**: Exact point of failure identified

---

## Phase 4: Root Cause Analysis

**Goal**: Understand why the error occurs

**Common Issues**:
1. **Axis Calculation Wrong**:
   - Off-by-one in binary navigation
   - Incorrect head/tail selection
   - Fix: Verify axis → path mapping
2. **Rule Implementation Bug**:
   - Rule 6 (if-then-else) logic inverted
   - Rule 8 (push) wrong subject extension
   - Fix: Compare against specification
3. **Subject Confusion**:
   - Using wrong subject for evaluation
   - Not passing subject correctly in recursion
   - Fix: Trace subject through execution
4. **Type Confusion**:
   - Treating cell as atom or vice versa
   - Fix: Add type assertions
5. **Loobean Confusion**:
   - Treating 0 as false instead of true
   - Fix: Remember 0=yes, 1=no

**Tasks**:
1. Analyze failure point from Phase 3
2. Map to common issue category
3. Verify against Nock specification
4. Formulate fix hypothesis

**Success**: Root cause identified, fix planned

---

## Phase 5: Fix and Validate

**Goal**: Implement fix and verify correctness

**Tasks**:
1. **Implement fix**:
   - Modify code based on root cause
   - Keep changes minimal and focused
2. **Test fix**:
   - Re-run failing formula (should pass)
   - Run related test cases
   - Add regression test for this bug
3. **Validate broadly**:
   - Run full test suite
   - Check for unintended side effects
   - Benchmark to ensure no performance regression
4. **Document**:
   - Add comment explaining fix
   - Update tests with new edge case
   - Document in changelog

**Success**: Bug fixed, tests passing, documented

---

## Debugging Tools

### Manual Tracing
```python
def nock_trace(subject, formula, depth=0):
    """Trace Nock execution with logging."""
    indent = "  " * depth
    print(f"{indent}*[{subject} {formula}]")

    result = nock(subject, formula)

    print(f"{indent}→ {result}")
    return result
```

### Assertion-Based Debugging
```rust
fn nock(subject: &Noun, formula: &Noun) -> Result<Noun> {
    assert!(formula.is_cell(), "Formula must be cell");

    let op = slot(formula, 2)?;
    assert!(op.is_atom(), "Operator must be atom");

    // ... rest of implementation
}
```

### Breakpoint Debugging
```python
import pdb

def nock(subject, formula):
    if formula == SUSPICIOUS_FORMULA:
        pdb.set_trace()  # Debugger breakpoint

    # ... rest of implementation
```

## Common Pitfalls

1. **Axis 0**: Always forbidden, no exceptions
2. **Loobean Backwards**: 0=true, 1=false (counterintuitive)
3. **Slot in Atom**: `/[2 42]` crashes (42 has no head)
4. **Increment Cell**: `+[[1 2]]` crashes (cannot increment cells)
5. **Subject vs Formula**: First arg = subject, second = formula (don't swap!)
6. **Binary Axis**: Axis 4 = binary 100 = path [0,0] not [1,0,0]

## Next Steps

- Use `/build-nock-interpreter` to rebuild with fixes
- Use `/optimize-nock-performance` if issue is performance
- Use `/learn-nock-fundamentals` to deepen understanding

## Resources

- Nock Specification: https://docs.urbit.org/nock/specification
- Common Errors: Urbit developer forums
- Reference Implementation: https://github.com/urbit/urbit/tree/master/pkg/noun
