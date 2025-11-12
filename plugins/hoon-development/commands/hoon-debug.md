---
name: hoon-debug
description: Systematic debugging workflow for Hoon applications covering error diagnosis, trace analysis, state inspection, and crash recovery
subagent_type: debugging-specialist
---

# Hoon Debugging Command

Comprehensive debugging workflow for diagnosing and resolving issues in Hoon code, from simple type errors to complex runtime failures.

## Purpose

This command guides developers through systematic debugging using Hoon's built-in diagnostic tools, error messages, and inspection capabilities to quickly identify and fix issues.

## When to Use

- Code crashes or fails to compile
- Unexpected runtime behavior
- Type errors or nest-fail errors
- Agent state corruption
- Subscription or networking issues
- Performance degradation
- Integration test failures

## Debugging Philosophy

1. **Read the Error Message** - Hoon errors are precise
2. **Reproduce Reliably** - Consistent reproduction enables fixing
3. **Isolate the Problem** - Narrow down to smallest failing case
4. **Understand, Don't Guess** - Learn why it failed
5. **Fix the Root Cause** - Don't patch symptoms

---

## 5-Phase Debugging Workflow

### Phase 1: Error Classification

**Objective**: Categorize the error to apply appropriate diagnosis strategy.

**Error Categories**:

#### 1. Compile-Time Errors

**Type Mismatch (nest-fail)**:
```
nest-fail
%have @t
%want @ud
```
**Meaning**: Trying to use a cord (@t) where an unsigned integer (@ud) is expected.

**Diagnosis**:
```hoon
::  Identify the location
::  Check the expression that produced @t
::  Verify what expects @ud
::  Add explicit type cast or conversion

::  Example fix:
=/  text  '42'
=/  number  (scan (trip text) dem)  ::  Convert @t to @ud
(add number 10)
```

**Syntax Errors**:
```
syntax error at [line col]
```
**Common causes**:
- Missing `==` to close rune
- Mismatched parentheses
- Invalid rune usage
- Incorrect irregular syntax

**Missing Subject**:
```
find-fork
```
**Meaning**: Trying to use a name that doesn't exist in the subject.

**Diagnosis**:
```hoon
::  Check for typos in name
::  Verify import statements (/+ /= /-)
::  Check variable is in scope
::  Ensure library exports the name
```

#### 2. Runtime Errors

**Assertion Failure (?> ?< ?~)**:
```
ford: %ride failed to execute:
```
**Meaning**: An assertion failed at runtime.

**Crash (!!)** :
```
bail: oops
```
**Meaning**: Explicit crash or unexpected condition.

**Division by Zero**:
```
bail: %div
```

**Out of Memory**:
```
bail: %meme
```

#### 3. Logic Errors

Symptoms:
- Wrong output values
- Unexpected state changes
- Missing or extra data
- Incorrect control flow

---

### Phase 2: Reproduction and Isolation

**Objective**: Create minimal reproducible test case.

**Reproduction Steps**:

#### Step 1: Document Current Behavior

```hoon
::  What I did:
::  1. Poked agent with [%action %create 'test']
::  2. Expected state to contain new item
::  3. Actual: state unchanged

::  Environment:
::  - Ship: ~zod (fake)
::  - App version: 1.0.0
::  - Desk: %my-app
```

#### Step 2: Create Minimal Test Case

```hoon
::  Isolate the failing code
::  /gen/test-create.hoon
:-  %say
|=  *
:-  %noun
=/  state  *app-state
=/  action  [%create name='test']
(handle-create state action)

::  Run: +test-create
::  Expected: New item in state
::  Actual: ???
```

#### Step 3: Binary Search for Bug Location

```hoon
::  Add debug output at midpoint
++  handle-create
  |=  [state=app-state action=create-action]
  ^-  app-state
  ~&  >  'handle-create called'
  =/  new-item  (make-item name.action)
  ~&  >  [%new-item new-item]  ::  Check item creation
  =/  new-items  [new-item items.state]
  ~&  >  [%new-items new-items]  ::  Check list construction
  state(items new-items)

::  Iteratively narrow down by moving ~& statements
```

**Success Criteria**:
- Minimal code that reproduces issue
- Clear expected vs actual behavior
- Reproducible on clean fake ship

---

### Phase 3: Diagnostic Instrumentation

**Objective**: Add targeted debugging output to understand behavior.

**Debugging Techniques**:

#### Technique 1: Print Debugging with ~&

```hoon
::  Basic output
~&  >  'Debug message'
~&  >  [%label value]
~&  >  [%state-dump state]

::  Severity levels
~&  >    'Info'     ::  Normal output
~&  >>   'Warning'  ::  Important
~&  >>>  'Error'    ::  Critical

::  Multiple values
~&  >  :-  %debug-info
  :*  input=input
      state=state
      result=result
  ==
```

#### Technique 2: Type Inspection

```hoon
::  See the type of an expression
!<  ::  Shows mold when used incorrectly
!>  ::  Creates vase (type + value)

::  Example:
=/  val  42
=/  vased  !>(val)
~&  >  [%type p.vased]  ::  Shows #t/@ud
~&  >  [%value q.vased]  ::  Shows 42
```

#### Technique 3: Crash Context with ~>

```hoon
::  Add context to crashes
++  parse-input
  |=  text=@t
  ~>  %mean.'parse-input: invalid format'
  (scan (trip text) parser)

::  Stack trace shows: "parse-input: invalid format"
```

#### Technique 4: Conditional Debugging

```hoon
::  Debug only specific cases
++  process
  |=  [id=@ud data=*]
  ?.  =(id 42)
    (normal-process data)
  ~&  >>>  'Debugging ID 42'
  ~&  >  [%data data]
  =/  result  (normal-process data)
  ~&  >  [%result result]
  result
```

#### Technique 5: State Snapshots

```hoon
::  Gall agent debugging
++  on-poke
  |=  [=mark =vase]
  ^-  (quip card _this)
  ~&  >  [%before-state state]
  =/  [cards new-state]  (handle-poke mark vase state)
  ~&  >  [%after-state new-state]
  ~&  >  [%cards-produced (lent cards)]
  [cards this(state new-state)]
```

---

### Phase 4: Interactive Debugging

**Objective**: Use dojo and runtime tools to inspect live state.

**Dojo Debugging Commands**:

#### Inspect Agent State

```bash
::  View state with dbug library
:app-name +dbug [%state]

::  View subscriptions
:app-name +dbug [%subscriptions]

::  View full debug info
:app-name +dbug
```

#### Scry for Data

```hoon
::  Read agent state
.^(state-type %gx /=app-name=/state/noun)

::  Read specific data
.^(@ud %gx /=app-name=/count/noun)

::  Check file contents
.^(@ %cx /=app-name=/app/app-name/hoon)
```

#### Test Functions in Dojo

```hoon
::  Load library
=lib /+  my-lib

::  Test function
(add:lib 5 10)

::  Test with state
=/  state  *app-state
(handle-action:app state [%create 'test'])
```

#### Manual Agent Interaction

```hoon
::  Send poke
:app-name &noun [%action %create 'test']

::  Subscribe
:app-name|watch /updates

::  Unsubscribe
:app-name|leave

::  Check subscriptions
:app-name +dbug [%subscriptions]
```

---

### Phase 5: Error Analysis and Resolution

**Objective**: Understand root cause and implement fix.

**Common Errors and Solutions**:

#### Error 1: nest-fail

```
nest-fail
%have (list @ud)
%want (list @ta)
```

**Root Cause**: Type mismatch between produced and expected types.

**Solution Strategy**:
1. Find where the type is produced
2. Find where it's consumed
3. Add conversion or fix the type

```hoon
::  Fix option 1: Convert the value
=/  numbers  ~[1 2 3]
=/  terms  (turn numbers |=(n=@ud `@ta`n))  ::  Convert to @ta

::  Fix option 2: Fix the type expectation
=/  numbers  ~[1 2 3]
=/  process  |=(items=(list @ud) ...)  ::  Change from (list @ta)
```

#### Error 2: find-fork-d (Missing name)

```
find-fork-d
%forkd
[<line> <col>]
```

**Root Cause**: Reference to undefined name.

**Solution Strategy**:
1. Check for typos: `proces` vs `process`
2. Verify imports: `/+ lib` before use
3. Check scope: name defined before use
4. Verify library exports

```hoon
::  Missing import
::  ✗ Bad
(helper-function data)  ::  find-fork-d

::  ✓ Good
/+  *my-lib
(helper-function data)
```

#### Error 3: Assertion Failures

```
ford: %ride failed to execute:
  ?> (gth count 0)
```

**Root Cause**: Runtime assertion violated.

**Solution Strategy**:
1. Check input validation
2. Verify preconditions
3. Add defensive checks

```hoon
::  ✗ Bad: Crashes on invalid input
++  divide
  |=  [a=@ud b=@ud]
  ?>  (gth b 0)  ::  Assertion fails if b=0
  (div a b)

::  ✓ Good: Returns unit for error case
++  divide
  |=  [a=@ud b=@ud]
  ^-  (unit @ud)
  ?:  =(b 0)  ~
  `(div a b)
```

#### Error 4: Infinite Loops

**Symptoms**:
- Agent stops responding
- Dojo hangs
- High CPU usage

**Diagnosis**:
```hoon
::  Add termination logging
=/  iterations  0
|-
~&  >  [%iteration iterations]
?:  (gth iterations 1.000)  ::  Safety limit
  ~&  >>>  'Loop exceeded limit!'
  !!
?~  items  result
$(items t.items, iterations +(iterations))
```

**Solution**:
```hoon
::  Ensure base case is reachable
::  ✗ Bad: items never becomes ~
|-
?~  items  result
$(items items)  ::  Never progresses!

::  ✓ Good: Proper recursion
|-
?~  items  result
$(items t.items)  ::  Consumes list
```

#### Error 5: State Corruption

**Symptoms**:
- Unexpected state values
- Missing data
- Type errors on state access

**Diagnosis**:
```hoon
::  Validate state invariants
++  check-invariants
  |=  state=app-state
  ^-  ?
  &(
    (lte (lent items.state) max-items)
    ?=(%0 -.state)  ::  Version check
    (check-unique-ids items.state)
  )

++  on-load
  |=  old-vase=vase
  =/  old  !<(versioned-state old-vase)
  =/  new  (migrate old)
  ?>  (check-invariants new)  ::  Validate after migration
  `this(state new)
```

---

## Debugging Tools Reference

### Built-in Debugging Arms

```hoon
++  on-fail  ::  Called when agent crashes
  |=  [=term =tang]
  ^-  (quip card _this)
  ~&  >>>  [%crash term]
  ~&  >>>  tang
  `this

++  on-poke  ::  Add error handling
  |=  [=mark =vase]
  ^-  (quip card _this)
  =/  result
    %-  mule  |.
    (handle-poke mark vase)
  ?-  -.result
    %&  p.result
    %|
      ~&  >>>  [%poke-error p.result]
      `this
  ==
```

### Debugging Library Integration

```hoon
::  /+  dbug at top of agent
/+  default-agent, dbug

::  Wrap agent
%-  agent:dbug
^-  agent:gall
|_  =bowl:gall
::  ... agent arms
--
```

### Error Formatting

```hoon
::  Pretty-print tang (error stack)
++  print-tang
  |=  =tang
  ^-  tape
  %-  zing
  %+  turn  tang
  |=  =tank
  (trip (of-wall:format (wash [0 80] tank)))
```

---

## Debugging Checklist

### Compile-Time
- [ ] All imports present and correct
- [ ] Type casts explicit and accurate
- [ ] Runes properly closed
- [ ] Names spelled correctly
- [ ] Subject contains referenced names

### Runtime
- [ ] Inputs validated before use
- [ ] Assertions have fallbacks
- [ ] Error cases handled gracefully
- [ ] Recursive functions terminate
- [ ] State transitions validated

### Testing
- [ ] Minimal reproduction case created
- [ ] Expected behavior documented
- [ ] Debug output strategically placed
- [ ] Fix verified with tests
- [ ] Regression test added

---

## Advanced Debugging Patterns

### Pattern 1: Bisection Debugging

```hoon
::  Mark known-good points
++  complex-function
  |=  input=@
  =/  step1  (process-step-1 input)
  ~&  >  [%checkpoint-1 step1]  ::  Known good here?
  =/  step2  (process-step-2 step1)
  ~&  >  [%checkpoint-2 step2]  ::  Or here?
  =/  step3  (process-step-3 step2)
  ~&  >  [%checkpoint-3 step3]  ::  Or here?
  step3
```

### Pattern 2: Differential Debugging

```hoon
::  Compare working vs broken versions
++  test-both-versions
  |=  input=@
  =/  old-result  (old-implementation input)
  =/  new-result  (new-implementation input)
  ~&  >  [%old old-result]
  ~&  >  [%new new-result]
  ~&  >  [%match =(old-result new-result)]
  new-result
```

### Pattern 3: Time-Travel Debugging

```hoon
::  Log all state changes
|_  state=[history=(list app-state) current=app-state]
++  update
  |=  new-state=app-state
  ^-  _state
  :_  current=new-state
  history=[current.state history.state]

++  rollback
  |=  steps=@ud
  ^-  _state
  =/  target  (snag steps history.state)
  state(current target)
--
```

---

## Integration with Skills

This command references:
- **hoon-fundamentals** - Understanding errors
- **type-system** - Type debugging
- **gall-agents** - Agent debugging
- **functional-programming-patterns** - Logic debugging
- **stdlib-reference** - Standard library behavior

---

## Common Pitfalls

1. **Ignoring error messages** - Read them carefully
2. **Guessing instead of measuring** - Add debug output
3. **Changing too much at once** - Isolate changes
4. **Not creating tests** - Prevent regressions
5. **Removing debug code too early** - Keep for future issues

---

## Success Metrics

- **Root cause identified** - Know why it failed
- **Minimal fix applied** - Don't over-engineer
- **Tests pass** - Including new regression test
- **Debug output removed** - Clean production code
- **Documentation updated** - Explain the fix
