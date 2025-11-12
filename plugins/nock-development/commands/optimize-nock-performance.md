---
name: optimize-nock-performance
description: Systematic performance optimization workflow including profiling, bottleneck identification, jetting, and benchmarking.
subagent_type: nock-optimization-specialist
---

# Optimize Nock Performance Command

6-phase workflow for systematically improving Nock interpreter performance from profiling through production deployment.

## Phase 1: Profiling and Measurement

**Goal**: Establish baseline performance and identify bottlenecks

**Tasks**:
1. **Benchmark current performance**:
   - Simple operations: slot, increment, equality (target: <1µs each)
   - Complex formulas: decrement (target: <100µs jetted, <10ms pure)
   - Throughput: ops/second (target: 10,000+ baseline, 1M+ jetted)
2. **Profile with language tools**:
   - C/Rust: perf, flamegraph
   - Python: cProfile, line_profiler
   - JavaScript: V8 profiler, Chrome DevTools
3. **Identify hot paths** (common bottlenecks):
   - Slot/tree addressing (typically 40-60% of time)
   - Cell allocation (typically 20-30% of time)
   - Pattern matching (typically 10-20% of time)
   - BigInt operations (typically 5-15% of time)
4. **Document findings**: Create performance report with profiler output

**Success Criteria**: Baseline metrics established, top 3 bottlenecks identified

---

## Phase 2: Bottleneck Identification

**Goal**: Analyze and prioritize optimization opportunities

**Tasks**:
1. **Categorize bottlenecks**:
   - Algorithmic (O(n²) → O(n log n))
   - Data structure (linked list → array)
   - Implementation (recursive → iterative)
   - Memory (excessive allocation/copying)
2. **Estimate impact**:
   - Calculate % of total time for each bottleneck
   - Prioritize by impact × ease (Pareto principle: 80% improvement from 20% effort)
3. **Plan optimizations**:
   - Low-hanging fruit first (memoization, iterative algorithms)
   - High-impact next (jetting critical operations)
   - Diminishing returns last (micro-optimizations)

**Success Criteria**: Prioritized optimization roadmap with expected speedups

---

## Phase 3: Jetting Opportunities

**Goal**: Identify formulas that benefit from native code acceleration

**Tasks**:
1. **Find jet candidates** (high-value operations):
   - Decrement: O(n) Nock → O(1) native (1000x+ speedup)
   - Arithmetic: add, mul, div (100-1000x speedup)
   - Cryptography: SHA-256, ed25519 (1M+ speedup)
   - List operations: reverse, concat (10-100x speedup)
2. **Analyze profitability**:
   - Execution frequency × speedup = total impact
   - Implementation complexity vs benefit
3. **Design jet registry**:
   - Hash map: formula → native function
   - State tracking: cold/warm/hot
4. **Plan validation strategy**:
   - Cold: always validate
   - Warm: periodic sampling
   - Hot: full trust after extensive testing

**Success Criteria**: Top 5-10 jet candidates identified and prioritized

---

## Phase 4: Jet Implementation

**Goal**: Implement native code acceleration for high-value operations

**Tasks**:
1. **Implement core jets**:
   ```rust
   fn jet_decrement(subject: &Noun) -> Result<Noun> {
       match subject {
           Noun::Atom(0) => Err(Crash("decrement 0")),
           Noun::Atom(n) => Ok(Noun::Atom(n - 1)),
           _ => Err(Crash("decrement cell"))
       }
   }
   ```
2. **Build jet registry**:
   - Register formula → jet mappings
   - Implement hint processing (Rule 10: %fast hint)
   - Add state management (cold/warm/hot)
3. **Integrate with evaluator**:
   - Check for jet before Nock evaluation
   - Validate jet output (cold state)
   - Fallback to Nock if jet fails
4. **Test extensively**:
   - Verify jets compute identical results to Nock
   - Test edge cases (0, large atoms, cells)
   - Property-based testing for invariants

**Success Criteria**: 5-10 jets implemented, validated, and integrated

---

## Phase 5: Benchmarking

**Goal**: Measure performance improvements and validate optimizations

**Tasks**:
1. **Run comprehensive benchmarks**:
   - Before/after comparison for each optimization
   - Individual operation timings (µs per op)
   - Throughput measurements (ops/second)
   - Memory usage (heap allocation, GC pressure)
2. **Create benchmark suite**:
   ```python
   benchmarks = [
       ("increment", [_, [4, [1, 41]]], 10000),
       ("decrement", [42, DECREMENT_FORMULA], 1000),
       ("arithmetic", test_add_mul_div, 5000),
       # ... more benchmarks
   ]
   ```
3. **Measure speedups**:
   - Calculate improvement ratio (new_time / old_time)
   - Target: 10x overall, 100-1000x for jetted operations
4. **Regression testing**:
   - Verify correctness unchanged
   - Check for performance regressions in optimizations
5. **Document results**:
   - Benchmark report with before/after metrics
   - Speedup breakdown by optimization

**Success Criteria**: 10x+ overall speedup, all tests passing, results documented

---

## Phase 6: Production Validation

**Goal**: Deploy optimized interpreter to production with monitoring

**Tasks**:
1. **Final validation**:
   - Run full test suite (unit, integration, property-based)
   - Stress test with large/complex formulas
   - Memory leak detection (Valgrind, heaptrack)
   - Concurrency safety (if applicable)
2. **Deploy with monitoring**:
   - Add performance metrics collection
   - Track ops/second, latency, errors
   - Monitor memory usage, GC pauses
3. **Production jet state management**:
   - Start jets in cold state
   - Validate extensively (1M+ executions)
   - Promote to warm after validation
   - Promote to hot for verified production jets
4. **Continuous monitoring**:
   - Alert on performance degradation
   - Track jet validation failures
   - Identify new optimization opportunities

**Success Criteria**: Production deployment successful, monitoring in place, performance maintained

---

## Performance Targets

### Throughput
- Baseline (interpreted): 10,000 - 100,000 ops/sec
- Optimized (memoized): 100,000 - 1,000,000 ops/sec
- Jetted: 1,000,000 - 10,000,000 ops/sec
- Compiled: 10,000,000+ ops/sec

### Latency
- Simple operations (slot): < 1 µs
- Moderate (increment): < 10 µs
- Complex (decrement jetted): < 100 µs
- Complex (decrement pure): < 10 ms

### Memory
- Noun overhead: < 24 bytes/cell
- Stack depth: 10,000+ recursive calls
- Heap usage: O(n) where n = total noun size

## Common Optimizations

1. **Memoization**: LRU cache for slot lookups (10-100x speedup)
2. **Iterative algorithms**: Replace recursion with loops (2-5x speedup)
3. **Jump tables**: Fast rule dispatch (1.5-2x speedup)
4. **Arena allocation**: Batch memory allocation (2-3x speedup)
5. **Jetting**: Native code for common operations (100-1000x speedup)

## Next Steps

- Use `/build-nock-interpreter` if you haven't built an interpreter yet
- Use `/debug-nock-execution` to troubleshoot slow formulas
- Contribute jets to community: https://github.com/urbit/urbit/tree/master/pkg/noun

## Resources

- Jetting Documentation: https://docs.urbit.org/nock/jetting
- Urbit Vere (Reference Runtime): https://github.com/urbit/urbit
- Performance Discussions: Urbit developer forums
