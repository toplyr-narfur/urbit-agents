---
name: hoon-refactor
description: Systematic code refactoring workflow for improving Hoon code structure, readability, and maintainability while preserving correctness
subagent_type: hoon-expert
---

# Hoon Refactoring Command

Comprehensive workflow for safely refactoring Hoon code to improve structure, eliminate duplication, and enhance maintainability without changing behavior.

## Purpose

This command guides developers through systematic refactoring using proven patterns, ensuring each transformation preserves correctness through testing while improving code quality.

## When to Use

- Code duplication across multiple files
- Functions grown too large or complex
- Difficult to understand or modify code
- Poor separation of concerns
- Inconsistent naming or structure
- Before adding new features
- Regular code quality maintenance

## Refactoring Principles

1. **Preserve Behavior** - Never change functionality
2. **Test First** - Have tests before refactoring
3. **Small Steps** - One transformation at a time
4. **Verify Often** - Run tests after each change
5. **Commit Frequently** - Small, atomic commits
6. **Improve Readability** - Code should be clearer after

---

## 6-Phase Refactoring Workflow

### Phase 1: Assessment and Planning

**Objective**: Identify refactoring opportunities and plan transformations.

**Assessment Checklist**:

#### Code Smells to Identify

**Duplication**:
```hoon
::  Same logic in multiple places
++  process-user
  |=  user=@p
  =/  name  .^(@t %gx /=users=/name/(scot %p user)/noun)
  =/  email  .^(@t %gx /=users=/email/(scot %p user)/noun)
  [name email]

++  check-user
  |=  user=@p
  =/  name  .^(@t %gx /=users=/name/(scot %p user)/noun)  ::  Duplicate!
  =/  active  .^(? %gx /=users=/active/(scot %p user)/noun)
  [name active]
```

**Long Functions** (>50 lines):
```hoon
++  handle-action  ::  120 lines - too long!
  |=  [state=app-state action=action]
  ::  Validation (20 lines)
  ::  Business logic (60 lines)
  ::  State updates (20 lines)
  ::  Side effects (20 lines)
```

**Deep Nesting** (>3 levels):
```hoon
++  process
  |=  data=*
  ?:  condition-1
    ?:  condition-2
      ?:  condition-3
        ?:  condition-4  ::  Too deep!
          result
        default-4
      default-3
    default-2
  default-1
```

**Magic Numbers**:
```hoon
?:  (gth count 100)  ::  What is 100?
  'too many'
'ok'
```

**Poor Naming**:
```hoon
++  proc  ::  What does this do?
  |=  d=*  ::  What is d?
  =/  x  (get-data d)  ::  What is x?
  (transform x)
```

**Tight Coupling**:
```hoon
++  handle-create
  |=  [state=app-state name=@t]
  ::  Directly manipulates global state
  ::  Calls multiple external services
  ::  Hard to test or reuse
```

**Planning Steps**:
1. Document current behavior
2. Create comprehensive tests
3. Identify refactoring targets
4. Choose refactoring patterns
5. Estimate risk and effort

**Success Criteria**:
- All tests passing before refactoring
- Refactoring plan documented
- Risk assessment completed
- Incremental steps defined

---

### Phase 2: Extract Functions

**Objective**: Break large functions into smaller, focused pieces.

**Pattern 1: Extract Helper Function**

```hoon
::  Before: Long function
++  validate-and-create
  |=  [state=app-state name=@t email=@t age=@ud]
  ^-  [result=? state=app-state]
  ::  Validation logic (15 lines)
  ?.  (validate-name name)
    [%.n state]
  ?.  (validate-email email)
    [%.n state]
  ?.  &((gte age 18) (lte age 120))
    [%.n state]
  ::  Creation logic (20 lines)
  =/  id  (generate-id state)
  =/  user  [id=id name=name email=email age=age created=now.bowl]
  =/  new-users  (~(put by users.state) id user)
  [%.y state(users new-users)]

::  After: Extracted helpers
++  validate-user-input
  |=  [name=@t email=@t age=@ud]
  ^-  ?
  &(
    (validate-name name)
    (validate-email email)
    (validate-age age)
  )

++  validate-age
  |=  age=@ud
  ^-  ?
  &((gte age 18) (lte age 120))

++  create-user
  |=  [state=app-state name=@t email=@t age=@ud]
  ^-  [id=@ud state=app-state]
  =/  id  (generate-id state)
  =/  user  [id=id name=name email=email age=age created=now.bowl]
  =/  new-users  (~(put by users.state) id user)
  [id state(users new-users)]

++  validate-and-create
  |=  [state=app-state name=@t email=@t age=@ud]
  ^-  [(unit @ud) state=app-state]
  ?.  (validate-user-input name email age)
    [~ state]
  =/  [id new-state]  (create-user state name email age)
  [`id new-state]
```

**Pattern 2: Extract Complex Expression**

```hoon
::  Before: Unclear inline logic
=/  qualified
  &(
    (gte age.user 18)
    (is-verified email.user)
    =(country.user 'US')
    (lte (lent violations.user) 3)
    (gth balance.user 100)
  )

::  After: Named helper
++  is-qualified-user
  |=  user=user-data
  ^-  ?
  &(
    (gte age.user 18)
    (is-verified email.user)
    =(country.user 'US')
    (has-acceptable-violations violations.user)
    (has-minimum-balance balance.user)
  )

++  has-acceptable-violations
  |=  violations=(list violation)
  ^-  ?
  (lte (lent violations) 3)

++  has-minimum-balance
  |=  balance=@ud
  ^-  ?
  (gth balance 100)
```

---

### Phase 3: Eliminate Duplication

**Objective**: Remove repeated code through abstraction.

**Pattern 1: Extract Common Code**

```hoon
::  Before: Duplicated scry logic
++  get-user-name
  |=  user=@p
  ^-  @t
  .^(@t %gx /=users=/name/(scot %p user)/noun)

++  get-user-email
  |=  user=@p
  ^-  @t
  .^(@t %gx /=users=/email/(scot %p user)/noun)

++  get-user-age
  |=  user=@p
  ^-  @ud
  .^(@ud %gx /=users=/age/(scot %p user)/noun)

::  After: Parameterized function
++  scry-user-field
  |*  [user=@p field=@tas return-type=mold]
  ^-  return-type
  .^(return-type %gx /=users=/[field]/(scot %p user)/noun)

::  Usage
=/  name  (scry-user-field user %name @t)
=/  email  (scry-user-field user %email @t)
=/  age  (scry-user-field user %age @ud)
```

**Pattern 2: Parameterize Behavior**

```hoon
::  Before: Similar functions
++  filter-active-users
  |=  users=(list user)
  ^-  (list user)
  (skim users |=(u=user active.u))

++  filter-verified-users
  |=  users=(list user)
  ^-  (list user)
  (skim users |=(u=user verified.u))

::  After: Single parameterized function
++  filter-users-by
  |*  [users=(list user) predicate=$-(user ?)]
  ^-  (list user)
  (skim users predicate)

::  Usage
=/  active  (filter-users-by users |=(u=user active.u))
=/  verified  (filter-users-by users |=(u=user verified.u))
```

**Pattern 3: Extract to Library**

```hoon
::  Before: Duplicated across multiple files
::  app/app-a.hoon
++  format-timestamp
  |=  time=@da
  ^-  @t
  (crip (scow %da time))

::  app/app-b.hoon
++  format-timestamp  ::  Duplicate!
  |=  time=@da
  ^-  @t
  (crip (scow %da time))

::  After: Shared library
::  lib/time-utils.hoon
|%
++  format-timestamp
  |=  time=@da
  ^-  @t
  (crip (scow %da time))
--

::  app/app-a.hoon and app/app-b.hoon
/+  time-utils
::  Use (format-timestamp:time-utils time)
```

---

### Phase 4: Improve Structure

**Objective**: Reorganize code for clarity and maintainability.

**Pattern 1: Introduce Explaining Variable**

```hoon
::  Before: Complex condition
?:  &(
      (gte (sub now.bowl last-update.state) ~d1)
      (lth (lent pending.state) 100)
      (~(has in active-users.state) src.bowl)
    )
  (process-request)
default

::  After: Named conditions
=/  is-stale  (gte (sub now.bowl last-update.state) ~d1)
=/  has-capacity  (lth (lent pending.state) 100)
=/  is-authorized  (~(has in active-users.state) src.bowl)
?:  &(is-stale has-capacity is-authorized)
  (process-request)
default
```

**Pattern 2: Replace Magic Numbers with Named Constants**

```hoon
::  Before: Magic numbers
?:  (gth count 100)
  'too many'
?:  (lth size 1.024)
  'too small'
?:  (gth age 18)
  'adult'
'minor'

::  After: Named constants
++  max-count  100
++  min-size  1.024
++  adult-age  18

?:  (gth count max-count)
  'too many'
?:  (lth size min-size)
  'too small'
?:  (gth age adult-age)
  'adult'
'minor'
```

**Pattern 3: Simplify Conditional Logic**

```hoon
::  Before: Nested conditions
++  get-discount
  |=  [age=@ud member=? purchases=@ud]
  ^-  @ud
  ?:  member
    ?:  (gte purchases 10)
      20
    ?:  (gte purchases 5)
      15
    10
  ?:  (gte age 65)
    ?:  (gte purchases 10)
      15
    10
  ?:  (gte purchases 10)
    10
  5

::  After: Early return pattern
++  get-discount
  |=  [age=@ud member=? purchases=@ud]
  ^-  @ud
  ?:  &(member (gte purchases 10))  20
  ?:  &(member (gte purchases 5))   15
  ?:  member                        10
  ?:  &((gte age 65) (gte purchases 10))  15
  ?:  (gte age 65)                 10
  ?:  (gte purchases 10)           10
  5
```

**Pattern 4: Introduce Type Alias**

```hoon
::  Before: Repeated complex types
++  process-users
  |=  users=(map @ud [name=@t email=@t age=@ud active=?])
  ^-  (list [id=@ud name=@t])
  ...

++  filter-users
  |=  [users=(map @ud [name=@t email=@t age=@ud active=?]) min-age=@ud]
  ^-  (map @ud [name=@t email=@t age=@ud active=?])
  ...

::  After: Named type
+$  user  [name=@t email=@t age=@ud active=?]
+$  user-map  (map @ud user)

++  process-users
  |=  users=user-map
  ^-  (list [id=@ud name=@t])
  ...

++  filter-users
  |=  [users=user-map min-age=@ud]
  ^-  user-map
  ...
```

---

### Phase 5: Enhance Readability

**Objective**: Make code intention clear and self-documenting.

**Pattern 1: Improve Naming**

```hoon
::  Before: Unclear names
++  proc
  |=  d=*
  =/  x  (get d)
  =/  y  (calc x)
  (put y)

::  After: Descriptive names
++  process-user-data
  |=  user-id=@ud
  =/  user-info  (fetch-user-info user-id)
  =/  computed-score  (calculate-user-score user-info)
  (update-user-score computed-score)
```

**Pattern 2: Add Documentation**

```hoon
::  Before: No documentation
++  transform
  |=  [items=(list @) threshold=@]
  %+  turn  items
  |=  n=@
  ?:  (gth n threshold)  (mul n 2)  n

::  After: Documented
::  Double all values above threshold, leave others unchanged
::
::  Arguments:
::    items: List of numbers to transform
::    threshold: Values above this are doubled
::
::  Returns:
::    Transformed list with values >threshold doubled
::
::  Example:
::    (transform ~[1 5 10] 3)  →  ~[1 5 20]
++  transform
  |=  [items=(list @) threshold=@]
  ^-  (list @)
  %+  turn  items
  |=  n=@
  ?:  (gth n threshold)  (mul n 2)  n
```

**Pattern 3: Use Intention-Revealing Names**

```hoon
::  Before: What vs Why
=/  result
  ?:  =(0 (mod n 2))
    'even'
  'odd'

::  After: Express intention
++  is-even  |=(n=@ =(0 (mod n 2)))

=/  parity
  ?:  (is-even n)
    'even'
  'odd'
```

**Pattern 4: Consistent Formatting**

```hoon
::  Before: Inconsistent style
++  func-a|=(n=@ (add n 1))
++  func-b
|=  n=@
  (mul n 2)
++  func-c  |=  n=@  (sub n 1)

::  After: Consistent style
++  func-a
  |=  n=@
  (add n 1)

++  func-b
  |=  n=@
  (mul n 2)

++  func-c
  |=  n=@
  (sub n 1)
```

---

### Phase 6: Verify and Commit

**Objective**: Ensure refactoring preserved behavior and commit changes.

**Verification Steps**:

#### 1. Run All Tests
```bash
::  Unit tests
+test-all

::  Integration tests
+test-integration

::  Manual verification
+verify-behavior
```

#### 2. Check for Regressions
```hoon
::  Compare outputs
++  test-refactoring
  =/  old-result  (old-implementation input)
  =/  new-result  (new-implementation input)
  .=  old-result  new-result
```

#### 3. Review Changes
```bash
::  Git diff to verify changes
git diff

::  Ensure only intended changes
::  No behavior modifications
::  Only structural improvements
```

#### 4. Commit Atomically
```bash
::  Each refactoring as separate commit
git add .
git commit -m "refactor: extract user validation logic"

git add .
git commit -m "refactor: introduce user-data type alias"

git add .
git commit -m "refactor: eliminate duplicate scry code"
```

---

## Refactoring Patterns Reference

### Extract Method
**When**: Function too long or doing multiple things
**How**: Move code block to new function with descriptive name

### Inline Function
**When**: Function body as clear as name
**How**: Replace function call with body

### Extract Variable
**When**: Complex expression hard to understand
**How**: Name intermediate results

### Inline Variable
**When**: Variable used once and adds no clarity
**How**: Replace with expression

### Rename
**When**: Name doesn't reveal intention
**How**: Change to descriptive name, update references

### Move Function
**When**: Function in wrong location (wrong file/core)
**How**: Move to appropriate location

### Extract Type
**When**: Complex type repeated
**How**: Create named type alias

### Replace Conditional with Polymorphism
**When**: Type-based branching
**How**: Use mold matching or doors

---

## Refactoring Checklist

### Before Refactoring
- [ ] All tests passing
- [ ] Behavior documented
- [ ] Refactoring plan created
- [ ] Risk assessed
- [ ] Version control committed

### During Refactoring
- [ ] One change at a time
- [ ] Tests run after each change
- [ ] Behavior unchanged
- [ ] Code compiles
- [ ] No debugging code added

### After Refactoring
- [ ] All tests still passing
- [ ] No new warnings/errors
- [ ] Documentation updated
- [ ] Code review requested
- [ ] Changes committed

---

## Integration with Skills

This command references:
- **hoon-fundamentals** - Core concepts
- **hoon-style-guide** - Naming and formatting
- **functional-programming-patterns** - Refactoring patterns
- **advanced-patterns** - Doors and polymorphism
- **type-system** - Type refactoring

---

## Common Mistakes to Avoid

1. **Changing behavior** - Refactor should preserve functionality
2. **Skipping tests** - Always verify after each change
3. **Too many changes at once** - Small, incremental steps
4. **Not committing frequently** - Commit after each successful refactoring
5. **Premature abstraction** - Wait for duplication before abstracting

---

## Success Metrics

- **Behavior preserved** - All tests pass
- **Complexity reduced** - Cyclomatic complexity lower
- **Duplication eliminated** - DRY principle followed
- **Readability improved** - Code reviewer confirms
- **Maintainability enhanced** - Easier to modify
