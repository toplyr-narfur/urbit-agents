---
name: hoon-optimize
description: Systematic performance optimization workflow for Hoon code covering algorithmic complexity, data structures, caching, and runtime profiling
subagent_type: hoon-expert
---

# Hoon Performance Optimization Command

Comprehensive workflow for identifying and resolving performance bottlenecks in Hoon applications, from profiling through implementation.

## Purpose

This command guides developers through systematic performance optimization, ensuring improvements are measured, safe, and maintainable while avoiding premature optimization.

## When to Use

- Application experiencing slow response times
- High CPU or memory usage in Gall agents
- Timeout errors in computations
- Performance regression after changes
- Preparing for production scale
- Regular performance audits

## Optimization Principles

1. **Measure First** - Profile before optimizing
2. **Focus on Bottlenecks** - Optimize hot paths, not cold paths
3. **Maintain Correctness** - Never sacrifice correctness for speed
4. **Test Performance** - Benchmark before and after
5. **Consider Readability** - Balance performance with maintainability

---

## 4-Phase Optimization Workflow

### Phase 1: Profiling and Measurement

**Objective**: Identify actual bottlenecks through measurement.

**Actions**:
1. Add timing instrumentation
2. Run realistic workloads
3. Collect performance data
4. Identify hot paths
5. Establish baseline metrics

**Profiling Techniques**:

#### Manual Timing
```hoon
::  Measure execution time
++  timed
  |*  computation=$-(* *)
  =/  start  now.bowl
  =/  result  (computation)
  =/  end  now.bowl
  ~&  >  "Execution time: {<(sub end start)>}"
  result

::  Usage
=<  (timed |.(heavy-function arg))
```

#### Hint-Based Profiling
```hoon
::  Use ~> %bout hint
++  process-items
  |=  items=(list item)
  ~>  %bout
  %+  turn  items
  expensive-transform

::  Use ~> %mean for crash context
++  risky-operation
  |=  data=@t
  ~>  %mean.'risky-operation failed'
  (parse-and-process data)
```

#### Counting Operations
```hoon
++  count-operations
  =/  counter  0
  |=  items=(list @)
  =/  result  *(list @)
  |-
  ?~  items
    ~&  >  "Total operations: {<counter>}"
    (flop result)
  ~&  >>  "Processing item {<counter>}"
  $(items t.items, result [i.items result], counter +(counter))
```

**Success Criteria**:
- Baseline performance metrics captured
- Bottlenecks identified with evidence
- Hot paths documented
- Realistic test cases defined

---

### Phase 2: Algorithmic Analysis

**Objective**: Analyze and improve algorithmic complexity.

**Checks**:

**Complexity Analysis**:
- [ ] Identify O(n²) or worse in hot paths
- [ ] Check for nested loops
- [ ] Review recursive algorithms
- [ ] Analyze traversal patterns
- [ ] Identify repeated work

**Common Complexity Issues**:

#### Issue: O(n²) Nested Search

```hoon
::  ✗ Bad: O(n²) - nested list search
++  find-common
  |=  [a=(list @ud) b=(list @ud)]
  ^-  (list @ud)
  =/  result  *(list @ud)
  |-  ^-  (list @ud)
  ?~  a  result
  =/  found
    |-  ^-  ?
    ?~  b  %.n
    ?:  =(i.a i.b)  %.y
    $(b t.b)
  ?:  found
    $(a t.a, result [i.a result])
  $(a t.a)

::  ✓ Good: O(n) - use set intersection
++  find-common
  |=  [a=(list @ud) b=(list @ud)]
  ^-  (list @ud)
  =/  set-a  (~(gas in *(set @ud)) a)
  =/  set-b  (~(gas in *(set @ud)) b)
  ~(tap in (~(int in set-a) set-b))
```

#### Issue: Redundant Traversals

```hoon
::  ✗ Bad: Multiple traversals
++  process-list
  |=  items=(list @ud)
  =/  evens  (skim items is-even)
  =/  doubled  (turn evens double)
  =/  filtered  (skim doubled is-valid)
  filtered

::  ✓ Good: Single traversal
++  process-list
  |=  items=(list @ud)
  %+  murn  items
  |=  n=@ud
  ^-  (unit @ud)
  ?.  (is-even n)  ~
  =/  doubled  (double n)
  ?.  (is-valid doubled)  ~
  `doubled
```

#### Issue: Inefficient Recursion

```hoon
::  ✗ Bad: Non-tail recursive (stack overflow risk)
++  sum-list
  |=  items=(list @ud)
  ^-  @ud
  ?~  items  0
  (add i.items $(items t.items))

::  ✓ Good: Tail recursive with accumulator
++  sum-list
  |=  items=(list @ud)
  ^-  @ud
  =/  acc  0
  |-  ^-  @ud
  ?~  items  acc
  $(items t.items, acc (add i.items acc))
```

**Success Criteria**:
- Complexity reduced to O(n log n) or better for hot paths
- Nested loops eliminated or justified
- Tail recursion implemented where applicable
- Traversal count minimized

---

### Phase 3: Data Structure Optimization

**Objective**: Select optimal data structures for access patterns.

**Data Structure Selection Matrix**:

| Operation | List | Map | Set | Mop | Jar |
|-----------|------|-----|-----|-----|-----|
| Lookup | O(n) | O(log n) | O(log n) | O(log n) | O(log n) |
| Insert | O(1) | O(log n) | O(log n) | O(log n) | O(log n) |
| Membership | O(n) | O(log n) | O(log n) | O(log n) | O(log n) |
| Iteration | O(n) | O(n) | O(n) | O(n) | O(n) |
| Order | Preserve | Sorted | Sorted | Custom | Sorted |

**Optimization Patterns**:

#### Pattern 1: List → Map for Lookups

```hoon
::  ✗ Bad: O(n) lookup
+$  user  [id=@ud name=@t email=@t]
=/  users  ~[[1 'alice' 'a@ex.com'] [2 'bob' 'b@ex.com']]

++  find-user
  |=  [id=@ud users=(list user)]
  ^-  (unit user)
  |-  ^-  (unit user)
  ?~  users  ~
  ?:  =(id.i.users id)  `i.users
  $(users t.users)

::  ✓ Good: O(log n) lookup
+$  user  [name=@t email=@t]
=/  users  (my ~[[1 ['alice' 'a@ex.com']] [2 ['bob' 'b@ex.com']]])

++  find-user
  |=  [id=@ud users=(map @ud user)]
  ^-  (unit user)
  (~(get by users) id)
```

#### Pattern 2: List → Set for Membership

```hoon
::  ✗ Bad: O(n) membership check
++  is-admin
  |=  [user=@p admins=(list @p)]
  ^-  ?
  |-  ^-  ?
  ?~  admins  %.n
  ?:  =(user i.admins)  %.y
  $(admins t.admins)

::  ✓ Good: O(log n) membership check
++  is-admin
  |=  [user=@p admins=(set @p)]
  ^-  ?
  (~(has in admins) user)
```

#### Pattern 3: Map → Mop for Custom Ordering

```hoon
::  Use mop for time-ordered data
++  event-log
  ^-  (mop @da event lte)
  %-  ~(gas by *((mop @da event) lte))
  :~  [~2024.1.1 [%created 'item']]
      [~2024.1.2 [%updated 'item']]
      [~2024.1.3 [%deleted 'item']]
  ==

::  Efficient range queries
++  events-between
  |=  [start=@da end=@da log=(mop @da event lte)]
  ^-  (list event)
  %+  turn
    %+  tap:((on @da event) lte)
      (lot:((on @da event) lte) log `start `end)
  |=([k=@da v=event] v)
```

#### Pattern 4: Jar for Multi-Value Maps

```hoon
::  ✗ Bad: Manual multi-value management
+$  user-tags  (map @p (list @tas))
=/  tags  *(map @p (list @tas))

++  add-tag
  |=  [user=@p tag=@tas tags=user-tags]
  ^-  user-tags
  =/  existing  (~(get by tags) user)
  ?~  existing
    (~(put by tags) user ~[tag])
  (~(put by tags) user [tag u.existing])

::  ✓ Good: Use jar
+$  user-tags  (jar @p @tas)
=/  tags  *(jar @p @tas)

++  add-tag
  |=  [user=@p tag=@tas tags=user-tags]
  ^-  user-tags
  (~(add ja tags) user tag)
```

**Success Criteria**:
- Data structures match access patterns
- No O(n) lookups in hot paths
- Memory usage optimized
- Code remains readable

---

### Phase 4: Caching and Memoization

**Objective**: Eliminate redundant computation through caching.

**Caching Strategies**:

#### Strategy 1: Result Caching

```hoon
::  Cache expensive computations
|_  state=[cache=(map @ud @ud)]
++  fibonacci
  |=  n=@ud
  ^-  [@ud _state]
  =/  cached  (~(get by cache.state) n)
  ?^  cached  [u.cached state]
  ?:  (lte n 1)  [n state]
  =/  [a new-state-1]  $(n (dec n), state state)
  =/  [b new-state-2]  $(n (sub n 2), state new-state-1)
  =/  result  (add a b)
  =/  new-cache  (~(put by cache.new-state-2) n result)
  [result state(cache new-cache)]
--
```

#### Strategy 2: Request Deduplication

```hoon
::  Deduplicate identical requests
|_  state=[pending=(map request-id (list duct)) results=(map request-id result)]
++  on-poke
  |=  [=mark =vase]
  ^-  (quip card _this)
  =/  req  !<(request vase)
  =/  req-id  (hash-request req)
  ::  Check if result cached
  =/  cached  (~(get by results.state) req-id)
  ?^  cached
    :_  this
    ~[[%give %fact ~ %result !>(u.cached)]]
  ::  Check if request pending
  =/  in-flight  (~(get by pending.state) req-id)
  ?^  in-flight
    ::  Add to waitlist
    `this(pending (~(put by pending.state) req-id [duct u.in-flight]))
  ::  Start new request
  :_  this(pending (~(put by pending.state) req-id ~[duct]))
  ~[[%pass /request/(scot %ud req-id) %agent [our %service] %poke %request !>(req)]]
--
```

#### Strategy 3: Time-Based Cache Invalidation

```hoon
::  TTL-based caching
+$  cached-value  [value=* expires=@da]
|_  state=[cache=(map @t cached-value)]
++  get-cached
  |=  [key=@t now=@da]
  ^-  (unit *)
  =/  entry  (~(get by cache.state) key)
  ?~  entry  ~
  ?:  (gth now expires.u.entry)  ~
  `value.u.entry

++  put-cached
  |=  [key=@t value=* ttl=@dr now=@da]
  ^-  _state
  =/  expires  (add now ttl)
  state(cache (~(put by cache.state) key [value expires]))
--
```

#### Strategy 4: LRU Cache

```hoon
::  Least Recently Used cache with size limit
+$  lru-entry  [value=* last-used=@da]
|_  state=[cache=(map @t lru-entry) max-size=@ud]
++  get
  |=  [key=@t now=@da]
  ^-  [(unit *) _state]
  =/  entry  (~(get by cache.state) key)
  ?~  entry  [~ state]
  =/  updated  (~(put by cache.state) key value.u.entry^now)
  [`value.u.entry state(cache updated)]

++  put
  |=  [key=@t value=* now=@da]
  ^-  _state
  ?:  (gte (lent ~(tap by cache.state)) max-size.state)
    ::  Evict LRU item
    =/  lru-key
      =/  entries  ~(tap by cache.state)
      =/  oldest  (rear entries)
      |-  ^-  @t
      ?~  entries  -.oldest
      ?:  (lth last-used.+.i.entries last-used.+.oldest)
        $(entries t.entries, oldest i.entries)
      $(entries t.entries)
    =/  evicted  (~(del by cache.state) lru-key)
    state(cache (~(put by evicted) key [value now]))
  state(cache (~(put by cache.state) key [value now]))
--
```

**Success Criteria**:
- Redundant computations eliminated
- Cache hit rate >80% for cacheable operations
- Memory usage within acceptable limits
- Cache invalidation strategy defined

---

## Optimization Checklist

### Algorithmic
- [ ] No O(n²) or worse in hot paths
- [ ] Tail recursion used where possible
- [ ] Repeated work eliminated
- [ ] Early exit conditions implemented
- [ ] Batching applied to repetitive operations

### Data Structures
- [ ] Map/set used instead of list for lookups
- [ ] Appropriate structure for access pattern
- [ ] Minimal data copying
- [ ] Efficient serialization format

### Caching
- [ ] Expensive computations cached
- [ ] Duplicate requests deduplicated
- [ ] Cache invalidation strategy defined
- [ ] Memory limits enforced

### Code Quality
- [ ] Tests verify correctness
- [ ] Benchmarks show improvement
- [ ] Code remains readable
- [ ] Comments explain trade-offs

---

## Performance Targets

### Response Time Goals
- **Interactive actions**: <100ms
- **Data queries**: <500ms
- **Background tasks**: <5s
- **Batch operations**: Progress indication

### Resource Limits
- **Memory**: Stay within loom limits (2-4GB)
- **CPU**: Minimize blocking computations
- **Storage**: Efficient pier size growth

---

## Advanced Optimization Techniques

### Technique 1: Lazy Evaluation

```hoon
::  Delay computation until needed
++  lazy-list
  |*  [head=* tail=$-(* *)]
  [head tail]

++  take-lazy
  |*  [n=@ lazy=*]
  ^-  (list)
  ?:  =(n 0)  ~
  [-.lazy $(n (dec n), lazy (+.lazy ~))]
```

### Technique 2: Stream Processing

```hoon
::  Process items incrementally
++  stream-process
  |*  [items=(list) transform=$-(@)]
  ^-  (list)
  ?~  items  ~
  [(transform i.items) $(items t.items)]
```

### Technique 3: Parallel Decomposition

```hoon
::  Split work across multiple cards
++  parallel-map
  |=  [items=(list item) process=$-(item result)]
  ^-  (quip card _this)
  =/  cards
    %+  turn  items
    |=  item=item
    [%pass /process/(scot %ud idx) %agent [our %worker] %poke %item !>(item)]
  [cards this]
```

---

## Benchmarking Template

```hoon
::  /gen/benchmark.hoon
:-  %say
|=  [[now=@da eny=@uvJ bec=beak] [iterations=@ud ~] ~]
:-  %noun
=/  start  now
=/  results  *(list [name=@t time=@dr])
::  Test 1: Baseline
=/  t1-start  now
=/  count  iterations
|-
?:  =(count 0)
  =/  t1-end  now
  =/  results  [[%baseline (sub t1-end t1-start)] results]
  ::  Test 2: Optimized
  =/  t2-start  now
  =/  count  iterations
  |-
  ?:  =(count 0)
    =/  t2-end  now
    =/  results  [[%optimized (sub t2-end t2-start)] results]
    ::  Results
    %+  turn  (flop results)
    |=([name=@t time=@dr] [name time])
  $(count (dec count))  ::  optimized version
$(count (dec count))  ::  baseline version
```

---

## Integration with Skills

This command references:
- **hoon-fundamentals** - Core concepts
- **advanced-patterns** - Optimization techniques
- **data-structures** - Structure selection
- **functional-programming-patterns** - Algorithm design
- **stdlib-reference** - Performance characteristics
- **gall-agents** - Agent optimization

---

## Common Mistakes to Avoid

1. **Premature optimization** - Profile first
2. **Micro-optimization** - Focus on hot paths
3. **Breaking correctness** - Test thoroughly
4. **Over-caching** - Monitor memory usage
5. **Complexity creep** - Balance performance with readability

---

## Success Metrics

- **Measured improvement**: Benchmarks show >50% speedup
- **No regressions**: All tests still pass
- **Resource efficiency**: Memory/CPU within limits
- **Code quality**: Maintained or improved readability
- **Documentation**: Trade-offs explained in comments
