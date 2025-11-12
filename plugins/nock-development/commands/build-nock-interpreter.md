---
name: build-nock-interpreter
description: Step-by-step workflow for building production-ready Nock interpreters from language selection through testing and optimization.
subagent_type: nock-interpreter-engineer
---

# Build Nock Interpreter Command

Complete 8-phase workflow for implementing a Nock virtual machine in your chosen language.

## Phase 1: Language Selection

**Goal**: Choose implementation language based on requirements

**Tasks**:
1. Evaluate language options (C, Python, Rust, Haskell, JavaScript)
2. Consider factors:
   - Performance needs (C/Rust for speed, Python for prototyping)
   - Type safety (Rust/Haskell for safety, Python for flexibility)
   - Deployment target (JavaScript for browser, C for embedded)
   - Team expertise (use familiar language)
3. Select big integer library (GMP, BigInt, num-bigint, Integer)
4. Choose memory strategy (GC, reference counting, arena allocation)

**Success Criteria**: Language chosen, libraries identified, development environment ready

---

## Phase 2: Parser Setup

**Goal**: Parse Nock text notation to internal data structures

**Tasks**:
1. Define noun data structure:
   ```
   enum/tagged union: Atom(int) | Cell(Noun, Noun)
   ```
2. Implement parser for notation:
   ```
   42          → Atom(42)
   [1 2]       → Cell(Atom(1), Atom(2))
   [[1 2] 3]   → Cell(Cell(Atom(1), Atom(2)), Atom(3))
   ```
3. Handle arbitrary-precision atoms
4. Add pretty-printer (noun → text)
5. Test parser round-trip: parse(print(noun)) = noun

**Success Criteria**: Can parse and print any noun correctly

---

## Phase 3: Evaluator Core

**Goal**: Implement all 13 Nock reduction rules

**Tasks**:
1. Implement `nock(subject, formula)` function
2. Pattern match on formula head (operator 0-12)
3. Implement each rule:
   - Rule 0: Slot (tree addressing)
   - Rule 1: Constant
   - Rule 2: Evaluate (recursion)
   - Rules 3-5: Cell test, increment, equality
   - Rule 6: If-then-else
   - Rules 7-9: Composition, push, call
   - Rules 10-12: Hints (simplified: ignore and evaluate body)
4. Handle crashes: axis 0, slot in atom, increment cell
5. Add basic error messages

**Success Criteria**: All 13 rules implemented, basic tests passing

---

## Phase 4: Tree Operations

**Goal**: Optimize slot (tree addressing) performance

**Tasks**:
1. Implement slot function:
   - Axis 1 = identity
   - Even axis = head of (axis // 2)
   - Odd axis = tail of (axis // 2)
2. Add binary navigation optimization:
   ```
   Convert axis to binary, strip leading 1, interpret bits as path
   ```
3. Implement iterative version (avoid recursion depth issues)
4. Add memoization for frequently-accessed axes
5. Benchmark slot performance

**Success Criteria**: Slot handles all valid axes, optimized for speed

---

## Phase 5: Operator Implementation

**Goal**: Implement auxiliary operators (?, +, =, /)

**Tasks**:
1. Type test (`?`): Return 1 if atom, 0 if cell
2. Increment (`+`): Add 1 to atom, crash on cell
3. Equality (`=`): Deep structural comparison, return 0 if equal
4. Slot (`/`): Already implemented in Phase 4
5. Add comprehensive operator tests
6. Verify loobean logic (0=yes, 1=no)

**Success Criteria**: All operators work correctly with edge cases

---

## Phase 6: Testing

**Goal**: Validate interpreter against specification

**Tasks**:
1. **Unit tests** (each rule in isolation):
   - Test all 13 rules with simple inputs
   - Verify crash conditions (axis 0, slot in atom, etc.)
2. **Integration tests** (complex formulas):
   - Decrement formula (stress test)
   - Nested compositions
   - Core invocation patterns
3. **Property-based tests**:
   - Slot identity: /[1 a] = a
   - Increment monotonicity: +[a] > a
   - Equality commutativity: =[a b] = =[b a]
4. **Edge cases**:
   - Large atoms (> 2^64)
   - Deep nesting (> 1000 levels)
   - Empty subjects

**Success Criteria**: 100% test coverage, all tests passing

---

## Phase 7: Optimization

**Goal**: Improve performance through profiling and tuning

**Tasks**:
1. **Profile** to find bottlenecks:
   - Use language-specific profiler (perf, cProfile, flamegraph)
   - Identify hot paths (usually slot, allocation, pattern matching)
2. **Optimize hot paths**:
   - Memoize slot lookups (LRU cache)
   - Use jump tables for rule dispatch (C/Rust)
   - Optimize cell allocation (arena, pooling)
   - Add tail call optimization (trampoline pattern)
3. **Benchmark** improvements:
   - Measure ops/second before and after
   - Target: 10,000+ ops/sec for interpreted Nock
4. **Add jetting** (optional):
   - Implement native increment, decrement
   - Register jets for common stdlib functions

**Success Criteria**: 10x performance improvement over naive implementation

---

## Phase 8: Documentation

**Goal**: Document API, architecture, and usage

**Tasks**:
1. **API documentation**:
   - nock(subject, formula) → result
   - Noun construction functions
   - Error types and handling
2. **Architecture notes**:
   - Data structure design decisions
   - Optimization strategies used
   - Performance characteristics (O(n) complexity)
3. **Usage examples**:
   - Evaluating simple formulas
   - Building cores
   - Handling errors
4. **Contributing guide**:
   - Code style
   - Testing requirements
   - Performance benchmarks

**Success Criteria**: Clear documentation for users and contributors

---

## Final Deliverables

1. ✅ Working Nock interpreter in chosen language
2. ✅ Comprehensive test suite (unit, integration, property-based)
3. ✅ Performance optimizations (memoization, iterative algorithms)
4. ✅ Documentation (API, architecture, examples)
5. ✅ Benchmarks (ops/second, latency measurements)
6. ✅ Optional: Jetting support for common operations

## Next Steps

After completing this workflow:
- Use `/optimize-nock-performance` to further tune your implementation
- Use `/debug-nock-execution` to troubleshoot formula evaluation
- Contribute your implementation to community (https://docs.urbit.org/nock/implementations)
- Integrate with Urbit runtime or build standalone tools

## Resources

- Nock Specification: https://docs.urbit.org/nock/specification
- Reference Implementations: https://docs.urbit.org/nock/implementations
- Decrement Exercise: https://docs.urbit.org/nock/decrement
- Community Implementations: https://github.com/urbit (search "nock")
