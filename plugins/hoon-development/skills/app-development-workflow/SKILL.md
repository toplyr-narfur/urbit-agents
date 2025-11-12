---
name: app-development-workflow
description: Master the complete Urbit application development workflow including environment setup, project structure, local development with fake ships, testing, deployment to production, and distribution. Use when building production Urbit applications, setting up development environment, or managing application lifecycle from start to finish.
---

# App Development Workflow Skill

Master the complete Urbit application development workflow including project structure, local development, testing, deployment, and distribution. Use when building production Urbit applications from start to finish.

## Overview

This skill covers the end-to-end process of developing, testing, and deploying Urbit applications, including best practices for project organization, development tools, and release management.

## Learning Objectives

1. Set up development environment
2. Structure Urbit projects effectively
3. Develop locally with fast iteration
4. Test applications thoroughly
5. Deploy to production ships
6. Distribute applications to users
7. Manage application lifecycle

## 1. Development Environment Setup

### Prerequisites

```bash
# Install Urbit
curl -JLO https://urbit.org/install/linux64/latest
tar xzf ./linux64.tgz
./urbit

# Create development ship (fake ship)
./urbit -F zod  # Boot fake ~zod
```

### Recommended Tools

- **Text Editor**: VS Code with Hoon extension
- **Terminal**: iTerm2/Terminator/Alacritty
- **Version Control**: Git
- **Documentation**: Local docs at https://developers.urbit.org

## 2. Project Structure

### Standard Urbit Desk Layout

```
my-app/
├── desk.bill          # App dependencies
├── desk.ship          # Ship address (optional)
├── sys.kelvin         # Kelvin version
├── app/               # Gall agents
│   └── my-app.hoon
├── sur/               # Shared structures
│   └── my-app.hoon
├── lib/               # Shared libraries
│   └── my-app.hoon
├── mar/               # Marks (data types)
│   ├── my-app/action.hoon
│   └── my-app/update.hoon
├── gen/               # Generators
│   └── my-app/
│       └── test.hoon
└── ted/               # Threads
    └── my-app/
        └── process.hoon
```

### File Organization

#### `desk.bill` - Dependencies
```hoon
:~  %my-app
    %other-required-app
==
```

#### `sys.kelvin` - Version
```hoon
[%zuse 418]  # Kernel version
```

#### `sur/my-app.hoon` - Types
```hoon
|%
+$  action
  $%  [%create name=@t]
      [%delete id=@ud]
  ==
+$  update
  $%  [%created id=@ud name=@t]
      [%deleted id=@ud]
  ==
--
```

#### `lib/my-app.hoon` - Utilities
```hoon
|%
++  helper-function
  |=  input=@t
  ^-  @t
  (process input)
--
```

## 3. Local Development Workflow

### Setup Development Desk

```bash
# On your development ship
|new-desk %my-app  # Create new desk
|mount %my-app     # Mount to Unix filesystem

# Copy files
cp -r my-app/* ~/zod/my-app/

# Commit changes
|commit %my-app

# Install app
|install our %my-app
```

### Fast Iteration Loop

```bash
# 1. Edit code in your editor
# 2. Commit to ship
|commit %my-app

# 3. App auto-reloads
# View console output in dojo
```

### File Watching (Automated)

```bash
# Use inotifywait or similar
while inotifywait -r -e modify ~/my-app/; do
    cp -r ~/my-app/* ~/zod/my-app/
    echo "|commit %my-app" | nc localhost 12321
done
```

## 4. Development Best Practices

### Pattern 1: Incremental Development

```hoon
::  Start with minimal agent
|_  =bowl:gall
++  on-init  `this
++  on-save  !>(~)
++  on-load  |=(vase `this)
::  Add arms as needed
--

::  Then add state
=|  state=[count=@ud]
|_  =bowl:gall
++  on-init  `this(count 0)
...

::  Then add functionality
++  on-poke
  |=  [=mark =vase]
  ?>  =(mark %noun)
  `this(count +(count.state))
```

### Pattern 2: Separate Concerns

```hoon
::  sur/my-app.hoon - Types only
::  lib/my-app.hoon - Pure logic
::  app/my-app.hoon - Gall integration
```

### Pattern 3: Use Debugger

```hoon
/+  default-agent, dbug

%-  agent:dbug
^-  agent:gall
|_  =bowl:gall
...

::  Debug in dojo
:my-app +dbug
:my-app +dbug [%state]
```

## 5. Testing

### Unit Tests (Generators)

```hoon
::  /gen/test-my-app.hoon
/-  *my-app
/+  *my-app
::
:-  %say
|=  *
:-  %noun
=/  tests
  :~  ['parse-valid' .=(`42 (parse-number '42'))]
      ['parse-invalid' .=(~ (parse-number 'abc'))]
      ['compute' .=(84 (double 42))]
  ==
%+  turn  tests
|=([name=@t result=?] [name result])

::  Run: +test-my-app
```

### Integration Tests

```hoon
::  /gen/test-integration.hoon
:-  %say
|=  *
:-  %noun
::  Send poke
[%pass /test %agent [our %my-app] %poke %my-action !>([%create 'test'])]
::  Verify state
=/  state  .^(vase %gx /=my-app=/dbug/state/noun)
!<([count=@ud] state)
```

### Property Testing

```hoon
++  test-roundtrip
  |=  [iterations=@ud eny=@uvJ]
  =/  rng  ~(. og eny)
  =/  passed  0
  |-
  ?:  =(iterations 0)  passed
  =^  val  rng  (rads:rng 1.000)
  =/  serialized  (to-json val)
  =/  deserialized  (from-json serialized)
  ?:  =(val deserialized)
    $(iterations (dec iterations), passed +(passed))
  $(iterations (dec iterations))
```

## 6. Debugging Techniques

### Console Logging

```hoon
~&  >  "Debug value: {<value>}"
~&  >>  "Warning: {<message>}"
~&  >>>  "Error: {<error>}"
```

### State Inspection

```bash
# View agent state
:my-app +dbug [%state]

# View subscriptions
:my-app +dbug [%subscriptions]

# View full state
:my-app +dbug
```

### Trace Execution

```hoon
~&  >  [%function-called arg]
~/  %function-name
...
~&  >  [%function-result result]
```

### Crash Analysis

```hoon
# Ship crashes? Check dojo output
# Look for stack trace
# Identify failing arm

# Add try-catch
=/  result
  %-  mule  |.
  (risky-operation)
?-  -.result
  %&  p.result
  %|  ~&  >>>  p.result  !!
==
```

## 7. Performance Optimization

### Profiling

```hoon
::  Use ~> %bout hint
++  expensive-function
  |=  n=@ud
  ~>  %bout
  (heavy-computation n)

::  Check runtime in console
```

### Optimization Checklist

1. **Use appropriate data structures**
   - Map for lookups
   - Set for membership
   - List for sequential

2. **Minimize traversals**
   ```hoon
   ::  ✗ Bad: Multiple passes
   =/  evens  (skim list is-even)
   =/  doubled  (turn evens double)

   ::  ✓ Good: Single pass
   %+  turn
     (skim list is-even)
   double
   ```

3. **Cache expensive computations**
   ```hoon
   |_  state=[cache=(map @ud @ud)]
   ++  fib
     |=  n=@ud
     =/  cached  (~(get by cache.state) n)
     ?^  cached  u.cached
     =/  result  (compute-fib n)
     ...
   --
   ```

4. **Use tail recursion**
   ```hoon
   ::  ✓ Good
   =/  acc  0
   |-
   ?~  items  acc
   $(items t.items, acc (add i.items acc))
   ```

## 8. Deployment

### Deploying to Fake Ship

```bash
# Already covered in local development
|install our %my-app
```

### Deploying to Real Ship

```bash
# 1. Mount desk on real ship
|new-desk %my-app
|mount %my-app

# 2. Copy files from development
scp -r ~/fake-zod/my-app/* ~/real-ship/my-app/

# 3. Commit and install
|commit %my-app
|install our %my-app
```

### Deploying to Remote Ship

```bash
# Use Clay sync
# On target ship
|sync %my-app ~source-ship %my-app

# Or use +hood/commit
# Publish from source
|public %my-app

# Subscribe on target
|install ~source-ship %my-app
```

## 9. Distribution

### Publishing to Network

```bash
# 1. Make desk public
|public %my-app

# 2. Users can install
|install ~your-ship %my-app
```

### Creating Install Instructions

```markdown
# My App Installation

## Prerequisites
- Urbit ship running
- Access to dojo

## Installation
1. In your ship's dojo:
   |install ~publisher-ship %my-app

2. Start the app:
   :my-app

3. Access web UI:
   http://localhost:8080/apps/my-app
```

### Versioning

```hoon
::  In app/my-app.hoon
::
::  Version 1.0.0
::
+$  versioned-state
  $%  [%0 state-0]
  ==
```

## 10. Maintenance and Updates

### State Migrations

```hoon
+$  versioned-state
  $%  [%0 state-0]
      [%1 state-1]
  ==

++  on-load
  |=  old-vase=vase
  =/  old  !<(versioned-state old-vase)
  ?-  -.old
    %0  `this(state (migrate-0-to-1 +.old))
    %1  `this(state +.old)
  ==
```

### Pushing Updates

```bash
# Update code
# Commit changes
|commit %my-app

# Subscribers auto-update
# (if they have auto-update enabled)
```

### Deprecation Policy

```hoon
::  Mark deprecated features
::  app/my-app.hoon
::
::  DEPRECATED: Use new-function instead
::  Will be removed in v2.0
++  old-function
  |=  n=@ud
  ~&  >>>  "Warning: old-function is deprecated"
  (new-function n)
```

## 11. Common Workflows

### Workflow 1: New Feature

```bash
# 1. Create feature branch (in git)
git checkout -b feature/new-thing

# 2. Add types to sur/
# 3. Add logic to lib/
# 4. Add actions to app/
# 5. Test with generator
+test-new-feature

# 6. Commit
|commit %my-app

# 7. Test on fake ship
# 8. Deploy to real ship
# 9. Merge to main
git merge feature/new-thing
```

### Workflow 2: Bug Fix

```bash
# 1. Reproduce bug
# 2. Add test case
# 3. Fix code
# 4. Verify test passes
# 5. Deploy hotfix
|commit %my-app
```

### Workflow 3: Refactor

```bash
# 1. Ensure tests exist
# 2. Refactor incrementally
# 3. Run tests after each change
# 4. Commit working state
# 5. Continue refactoring
```

## 12. Best Practices Checklist

- [ ] Version your state (always)
- [ ] Write tests for core logic
- [ ] Document public APIs
- [ ] Handle errors gracefully
- [ ] Log important events
- [ ] Use descriptive names
- [ ] Keep functions small
- [ ] Separate concerns (sur/lib/app)
- [ ] Test on fake ship first
- [ ] Provide migration paths
- [ ] Document installation process
- [ ] Consider backwards compatibility

## Resources

- [App School](https://developers.urbit.org/guides/core/app-school/intro) - Complete guide
- [App Workbook](https://developers.urbit.org/guides/core/app-school-full-stack) - Full-stack development
- [Distribution Guide](https://developers.urbit.org/guides/additional/dist/dist) - Publishing apps

## Summary

Urbit app development workflow:
1. **Setup** - Dev ship, project structure
2. **Develop** - Fast iteration, incremental
3. **Test** - Unit, integration, property tests
4. **Debug** - Logging, state inspection, profiling
5. **Optimize** - Data structures, algorithms, caching
6. **Deploy** - Fake ship → real ship → network
7. **Distribute** - Public desk, install instructions
8. **Maintain** - Migrations, updates, deprecation

Following this workflow ensures building robust, maintainable Urbit applications that users can easily install and use.
