---
name: nock-performance-profiling
description: Performance analysis techniques for Nock interpreters including CPU profiling, memory profiling, benchmarking methodologies, and bottleneck identification. Use when optimizing Nock implementations, measuring performance improvements, or diagnosing slow execution.
---

# Nock Performance Profiling

Techniques for measuring, analyzing, and optimizing Nock interpreter performance.

## Profiling Tools by Language

### C/C++ (perf, gprof, Valgrind)
```bash
# CPU profiling with perf
perf record -g ./nock-interpreter benchmark.nock
perf report --stdio

# Memory profiling with Valgrind massif
valgrind --tool=massif ./nock-interpreter
ms_print massif.out.*

# Callgrind (detailed call graph)
valgrind --tool=callgrind ./nock-interpreter
kcachegrind callgrind.out.*
```

### Python (cProfile, memory_profiler)
```bash
# CPU profiling
python -m cProfile -s cumulative nock.py

# Memory profiling
python -m memory_profiler nock.py

# Line profiler (detailed)
kernprof -l -v nock.py
```

### Rust (flamegraph, criterion)
```bash
# Flamegraph visualization
cargo flamegraph --bin nock-bench

# Criterion benchmarking
cargo bench

# perf integration
perf record --call-graph=dwarf cargo run --release
```

### JavaScript (Node.js profiler)
```bash
# V8 profiling
node --prof nock.js
node --prof-process isolate-*-v8.log > profile.txt

# Chrome DevTools
node --inspect-brk nock.js  # Then attach Chrome
```

## Benchmarking Methodology

### Microbenchmarks (Individual Operations)
```python
import time

def benchmark_operation(op, iterations=10000):
    times = []
    for _ in range(iterations):
        start = time.perf_counter()
        result = op()
        end = time.perf_counter()
        times.append(end - start)

    return {
        'mean': statistics.mean(times),
        'median': statistics.median(times),
        'p95': statistics.quantiles(times, n=20)[18],
        'min': min(times),
        'max': max(times)
    }

# Example: benchmark slot operation
result = benchmark_operation(lambda: slot([[1, 2], 3], 4))
```

### Stress Tests (Complex Formulas)
```python
# Decrement: Good stress test (complex, slow in pure Nock)
decrement = [8, [1, 0], ...]  # Full formula

def stress_test_decrement():
    for n in [10, 100, 1000, 10000]:
        start = time.time()
        result = nock(n, decrement)
        elapsed = time.time() - start
        print(f"decrement({n}): {elapsed*1000:.2f}ms, {n/elapsed:.0f} ops/sec")
```

## Bottleneck Identification

### Hotspot Analysis
```
Common bottlenecks in Nock interpreters:

1. Slot (tree addressing) - 40-60% of time
   → Optimize with memoization or bitwise navigation

2. Cell allocation - 20-30% of time
   → Use arena allocation or object pooling

3. Pattern matching - 10-20% of time
   → Use jump tables or bytecode compilation

4. BigInt operations - 5-15% of time
   → Use native integer types where possible
```

### Profiling Example (Python)
```python
import cProfile
import pstats

profiler = cProfile.Profile()
profiler.enable()

# Run Nock computations
for i in range(1000):
    nock(test_subject, test_formula)

profiler.disable()

stats = pstats.Stats(profiler)
stats.sort_stats('cumulative')
stats.print_stats(20)  # Top 20 functions

# Output:
#   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
#    50000    2.5s     0.05ms   4.2s     0.08ms nock.py:42(slot)
#    30000    1.8s     0.06ms   2.1s     0.07ms nock.py:103(nock)
#    ...
```

## Optimization Patterns

### Before Optimization
```python
def slot_naive(subject, axis):
    """Slow: recursive with no caching."""
    if axis == 1: return subject
    if axis % 2 == 0:
        return slot(subject[0], axis // 2)
    else:
        return slot(subject[1], axis // 2)

# Benchmark: 500 ns per call
```

### After Optimization
```python
from functools import lru_cache

@lru_cache(maxsize=10000)
def slot_cached(subject_id, axis):
    """Fast: memoized lookups."""
    subject = get_noun(subject_id)

    # Iterative instead of recursive
    depth = axis.bit_length() - 1
    current = subject
    for i in range(depth - 1, -1, -1):
        current = current.head if (axis >> i) & 1 == 0 else current.tail

    return current

# Benchmark: 50 ns per call (10x improvement)
```

## Memory Profiling

### Python memory_profiler
```python
from memory_profiler import profile

@profile
def nock_memory_test():
    """Profile memory usage."""
    large_subject = build_large_tree(depth=20)
    result = nock(large_subject, formula)

# Output:
# Line #    Mem usage    Increment   Line Contents
# ================================================
#     42     50.2 MiB      0.0 MiB   def nock_memory_test():
#     43    450.8 MiB    400.6 MiB       large_subject = build_large_tree(20)
#     44    451.2 MiB      0.4 MiB       result = nock(large_subject, formula)
```

### Rust (heap profiling)
```bash
# Heaptrack
heaptrack ./target/release/nock-bench

# dhat (Valgrind)
valgrind --tool=dhat ./target/release/nock-bench
```

## Performance Targets

### Throughput (Operations per Second)
```
Interpreted Nock:     10,000 - 100,000 ops/sec
Optimized Nock:      100,000 - 1,000,000 ops/sec
Jetted Nock:       1,000,000 - 10,000,000 ops/sec
Compiled Nock:    10,000,000+ ops/sec
```

### Latency (Per Operation)
```
Simple (slot):          < 1 µs
Moderate (increment):   < 10 µs
Complex (decrement):    < 100 µs (jetted), < 10 ms (pure)
Crypto (SHA-256):       < 1 ms (jetted), seconds (pure)
```

## Summary

Profile with language-specific tools: perf/gprof (C), cProfile (Python), flamegraph (Rust), --prof (Node.js). Benchmark methodology: microbenchmarks (individual ops), stress tests (complex formulas like decrement). Common bottlenecks: slot (40-60%), allocation (20-30%), pattern matching (10-20%). Optimization strategies: memoization (LRU cache), iterative vs recursive, bitwise operations, arena allocation. Performance targets: 10K-10M ops/sec depending on optimization level, <1µs simple ops, <100µs complex (jetted).
