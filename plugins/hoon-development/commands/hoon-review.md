---
name: hoon-review
description: Comprehensive Hoon code review workflow covering quality, security, performance, and maintainability with actionable feedback
subagent_type: code-reviewer
---

# Hoon Code Review Command

Complete code review workflow for Hoon applications, libraries, and agents ensuring quality, security, performance, and maintainability through systematic analysis.

## Purpose

This command guides developers through a thorough code review process, identifying issues at multiple priority levels (P0-P3) and providing actionable improvements with examples and best practices.

## Review Scope Options

### File Scope
- **Single file** - Review one Hoon file
- **Module** - Review related files (app + sur + lib)
- **Full application** - Review entire desk
- **Specific arms** - Review selected functions/arms

### Review Focus
- **Comprehensive** - All aspects (default)
- **Security-focused** - Vulnerabilities and safety
- **Performance-focused** - Optimization opportunities
- **Style-focused** - Code conventions and readability

## 5-Phase Review Workflow

### Phase 1: Initial Assessment

**Objective**: Understand code purpose, structure, and context.

**Actions**:
1. Identify file type (app, lib, sur, mar, gen)
2. Read file header and comments
3. Understand data types and structures
4. Map out dependencies (imports, libraries)
5. Identify core functionality

**Questions to Answer**:
- What is this code's purpose?
- Who will use it (users, other code)?
- What are the key data flows?
- What are the external dependencies?

**Success Criteria**:
- Clear understanding of code purpose
- Dependencies mapped
- Core functionality identified
- Context documented

---

### Phase 2: Correctness and Type Safety

**Objective**: Verify code works as intended with proper types.

**Checks**:

**Type Safety**:
- [ ] All function return types explicit (`^-`)
- [ ] No unsafe casts or type violations
- [ ] Proper mold definitions
- [ ] Correct aura usage (@ud, @t, etc.)
- [ ] Variance handled correctly

**Logic Correctness**:
- [ ] Conditionals cover all cases
- [ ] Pattern matches exhaustive (`?-`)
- [ ] Edge cases handled (empty lists, null units)
- [ ] No unreachable code
- [ ] Recursion terminates

**Common Issues**:

**Issue: Missing Type Casts**
```hoon
::  ✗ Bad
++  process
  |=  input=@t
  (parse input)  ::  Return type unclear

::  ✓ Good
++  process
  |=  input=@t
  ^-  (unit @ud)
  (parse input)
```

**Issue: Non-exhaustive Pattern Match**
```hoon
::  ✗ Bad
?-  -.action
  %add     (handle-add)
  %delete  (handle-delete)
==  ::  Missing other cases!

::  ✓ Good
?+  -.action  !!  ::  Crash on unknown
  %add     (handle-add)
  %delete  (handle-delete)
==
```

**Success Criteria**:
- All type issues identified
- Logic errors found
- Suggested fixes provided

---

### Phase 3: Security Review

**Objective**: Identify security vulnerabilities and risks.

**Security Checklist**:

**Input Validation**:
- [ ] All external input validated
- [ ] Boundary checking on numbers
- [ ] Type enforcement on data
- [ ] No injection vulnerabilities

**State Management**:
- [ ] State versioning implemented
- [ ] Migration paths defined
- [ ] No state corruption risks
- [ ] Proper error handling

**Access Control**:
- [ ] Authentication checks (`=/  src.bowl`)
- [ ] Subscription access control
- [ ] No unauthorized data leaks
- [ ] Proper permission checks

**Cryptography**:
- [ ] No weak hash functions
- [ ] Proper random number generation
- [ ] Secure key handling
- [ ] No hardcoded secrets

**Common Vulnerabilities**:

**Issue: Missing Access Control**
```hoon
::  ✗ Bad: Anyone can subscribe
++  on-watch
  |=  =path
  :_  this
  ~[[%give %fact ~ %data !>(sensitive-data)]]

::  ✓ Good: Check authorization
++  on-watch
  |=  =path
  ?>  =(our.bowl src.bowl)  ::  Only owner
  :_  this
  ~[[%give %fact ~ %data !>(sensitive-data)]]
```

**Issue: No Input Validation**
```hoon
::  ✗ Bad: No validation
++  on-poke
  |=  [=mark =vase]
  =/  id  !<(@ud vase)
  (get-item id)  ::  Could crash!

::  ✓ Good: Validate input
++  on-poke
  |=  [=mark =vase]
  =/  id  !<(@ud vase)
  ?>  (lth id max-id)
  (get-item id)
```

**Success Criteria**:
- All security issues identified
- Risk level assessed
- Remediation steps provided

---

### Phase 4: Performance Analysis

**Objective**: Identify performance bottlenecks and optimization opportunities.

**Performance Checks**:

**Algorithmic Complexity**:
- [ ] No O(n²) in hot paths
- [ ] Appropriate data structures
- [ ] Efficient searches/lookups
- [ ] Tail recursion where possible

**Data Structure Selection**:
- [ ] Maps for key-value lookups
- [ ] Sets for membership tests
- [ ] Lists for sequential access
- [ ] Avoid lists for random access

**Optimization Opportunities**:
- [ ] Reduce repeated computations
- [ ] Cache expensive results
- [ ] Minimize traversals
- [ ] Use jet-accelerated functions

**Common Performance Issues**:

**Issue: O(n²) Complexity**
```hoon
::  ✗ Bad: O(n²) - nested list search
++  find-duplicates
  |=  items=(list @ud)
  |-  ^-  (list @ud)
  ?~  items  ~
  =/  count
    (lent (skim items |=(n=@ =(n i.items))))
  ?:  (gth count 1)
    [i.items $(items t.items)]
  $(items t.items)

::  ✓ Good: O(n) - single pass with set
++  find-duplicates
  |=  items=(list @ud)
  ^-  (list @ud)
  =/  seen  *(set @ud)
  =/  dupes  *(set @ud)
  |-  ^-  (list @ud)
  ?~  items  ~(tap in dupes)
  ?:  (~(has in seen) i.items)
    $(items t.items, dupes (~(put in dupes) i.items))
  $(items t.items, seen (~(put in seen) i.items))
```

**Issue: Wrong Data Structure**
```hoon
::  ✗ Bad: List for lookups - O(n)
=/  users  ~[[id=1 name='Alice'] [id=2 name='Bob']]
(find users |=(u=user =(id.u target-id)))

::  ✓ Good: Map for lookups - O(log n)
=/  users  (my ~[[1 'Alice'] [2 'Bob']])
(~(get by users) target-id)
```

**Success Criteria**:
- Performance bottlenecks identified
- Complexity analysis provided
- Optimization recommendations with examples

---

### Phase 5: Code Quality and Maintainability

**Objective**: Assess readability, organization, and maintainability.

**Quality Checks**:

**Naming and Conventions**:
- [ ] Descriptive variable names
- [ ] Consistent naming style (kebab-case)
- [ ] Clear function purposes
- [ ] No magic numbers

**Code Organization**:
- [ ] Logical arm ordering
- [ ] Related functions grouped
- [ ] Proper file separation (sur/lib/app)
- [ ] Reasonable function sizes (<100 lines)

**Documentation**:
- [ ] File headers present
- [ ] Complex logic explained
- [ ] Public API documented
- [ ] Edge cases noted

**Error Handling**:
- [ ] Graceful error handling
- [ ] Informative error messages
- [ ] No silent failures
- [ ] Proper use of units/results

**Complexity**:
- [ ] Functions not too complex (cyclomatic <10)
- [ ] Deeply nested code refactored
- [ ] Clear control flow
- [ ] Minimal cognitive load

**Common Quality Issues**:

**Issue: Magic Numbers**
```hoon
::  ✗ Bad
?:  (gth count 100)
  'too many'
'ok'

::  ✓ Good
++  max-count  100
?:  (gth count max-count)
  'too many'
'ok'
```

**Issue: Poor Naming**
```hoon
::  ✗ Bad
=/  x  (process-data)
=/  fn  |=(a=@ (mul a 2))

::  ✓ Good
=/  processed-users  (process-data)
=/  double  |=(n=@ud (mul n 2))
```

**Success Criteria**:
- Code quality assessed
- Maintainability issues identified
- Refactoring suggestions provided

---

## Review Output Format

### Summary Section
```
## Code Review Summary

**File**: /app/my-app.hoon
**Lines of Code**: 450
**Overall Assessment**: Good with important improvements needed

**Critical Issues (P0)**: 1
**Important Issues (P1)**: 3
**Suggestions (P2)**: 5
**Nits (P3)**: 8
```

### Issue Breakdown

**P0: Critical Issues (Must Fix)**
- Security vulnerabilities
- Correctness bugs
- Data corruption risks
- Breaking changes

**P1: Important Issues (Should Fix)**
- Performance problems
- Maintainability concerns
- Missing error handling
- Resource leaks

**P2: Suggestions (Nice to Have)**
- Style improvements
- Simplification opportunities
- Documentation gaps
- Test coverage

**P3: Nits (Optional)**
- Whitespace consistency
- Comment style
- Naming improvements
- Minor refactoring

### Example Output

```markdown
## P0: Critical Issues (Must Fix)

### 1. Missing State Versioning (Line 12)
**Problem**: State changes will break existing piers
**Impact**: Users will lose data on updates
**Fix**:
\`\`\`hoon
+$  versioned-state
  $%  [%0 state-0]
  ==
\`\`\`

## P1: Important Issues (Should Fix)

### 2. O(n) Lookup in Hot Path (Line 45)
**Problem**: Linear search for every request
**Impact**: Slow for large datasets (>100 items)
**Fix**: Use map instead of list
[Code example...]

## Positive Highlights

✅ Clear type definitions
✅ Good error handling in critical paths
✅ Well-structured state management
```

---

## Integration with Skills

This command references:
- **hoon-style-guide** - Naming and conventions
- **functional-programming-patterns** - Code patterns
- **type-system** - Type correctness
- **hoon-fundamentals** - Core concepts
- **stdlib-reference** - Idiomatic usage
- **gall-agents** - Agent-specific patterns

## Best Practices

1. **Review in order** - Don't skip phases
2. **Be specific** - Cite line numbers and provide examples
3. **Balance criticism** - Note good patterns too
4. **Prioritize correctly** - P0 before P3
5. **Educate** - Explain why, not just what
6. **Provide alternatives** - Show better approaches
7. **Consider context** - Prototype vs production

## Common Pitfalls to Avoid

- ❌ Nitpicking without high-impact issues
- ❌ Vague feedback ("make this better")
- ❌ Inconsistent standards
- ❌ Ignoring positive aspects
- ❌ Over-focusing on style vs substance

## Success Metrics

- All critical issues identified
- Actionable feedback provided
- Code examples for improvements
- Learning resources referenced
- Developer can fix independently
