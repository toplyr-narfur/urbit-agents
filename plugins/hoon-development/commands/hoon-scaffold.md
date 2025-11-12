---
name: hoon-scaffold
description: Project scaffolding workflow for creating new Urbit applications with proper structure, boilerplate, and best practices
subagent_type: app-architect
---

# Hoon Project Scaffolding Command

Complete workflow for bootstrapping new Urbit applications with proper structure, types, and development setup.

## Purpose

This command guides developers through creating well-structured Urbit projects, from initial desk setup through complete application architecture.

## When to Use

- Starting a new Urbit application
- Creating a new desk
- Building a library
- Setting up a generator suite
- Creating a mark (data type)
- Initializing project structure

## Scaffolding Principles

1. **Start Simple** - Minimal viable structure
2. **Follow Conventions** - Standard Urbit patterns
3. **Type Everything** - Sur files from start
4. **Test Early** - Set up testing infrastructure
5. **Document From Start** - Clear README and comments
6. **Version Properly** - State versioning from day one

---

## 6-Phase Scaffolding Workflow

### Phase 1: Project Planning

**Objective**: Define project scope and architecture.

**Project Template Selection**:

#### Template 1: Simple App (No Frontend)
```
Use when:
- Backend-only functionality
- CLI tools
- Background agents
- Data processing

Components:
- 1 Gall agent
- 1 sur file (types)
- 1-2 lib files (helpers)
- Generators for testing
```

#### Template 2: Full-Stack App
```
Use when:
- Web interface needed
- User-facing application
- Interactive features

Components:
- 1 Gall agent
- 1 sur file (types)
- 1 lib file (helpers)
- Marks for HTTP
- Sail templates
- Static assets
```

#### Template 3: Library
```
Use when:
- Reusable code
- Shared utilities
- No state needed

Components:
- 1 lib file
- 1 sur file (types)
- Generators for testing
- Documentation
```

**Planning Questions**:

```
1. Application Name: ________
2. Purpose: ________ (one sentence)
3. Key Features:
   - Feature 1: ________
   - Feature 2: ________
   - Feature 3: ________
4. State Requirements:
   - What data needs to persist? ________
   - Estimated size? ________
5. User Interface:
   - CLI only? ________
   - Web UI? ________
   - Both? ________
6. External Dependencies:
   - Other agents? ________
   - External APIs? ________
```

**Success Criteria**:
- Project scope defined
- Architecture chosen
- Dependencies identified
- File structure planned

---

### Phase 2: Desk Setup

**Objective**: Create desk with required files.

**Step 1: Create Desk**

```bash
::  In dojo
|new-desk %my-app
|mount %my-app

::  On Unix filesystem
cd ~/ship/my-app/
```

**Step 2: Required Files**

#### desk.bill (Dependencies)
```hoon
::  List required apps
:~  %my-app
==
```

#### sys.kelvin (Kernel Version)
```hoon
::  Current kernel version
[%zuse 418]
```

**Directory Structure**:

```
my-app/
├── desk.bill          # App dependencies
├── sys.kelvin         # Kernel version
├── app/               # Gall agents
│   └── my-app.hoon
├── sur/               # Shared types
│   └── my-app.hoon
├── lib/               # Libraries
│   └── my-app.hoon
├── mar/               # Marks
│   ├── my-app/
│   │   ├── action.hoon
│   │   └── update.hoon
├── gen/               # Generators
│   └── my-app/
│       └── test.hoon
└── README.md          # Documentation
```

**Success Criteria**:
- Desk created and mounted
- Required files present
- Directory structure complete

---

### Phase 3: Type Definition (sur/)

**Objective**: Define application types before implementation.

**Pattern 1: Simple App Types**

```hoon
::  sur/my-app.hoon
::
::  Type definitions for my-app
::
|%
::  +$  action: User actions
::
+$  action
  $%  [%create name=@t]
      [%delete id=@ud]
      [%update id=@ud name=@t]
  ==
::
::  +$  update: State updates broadcast to subscribers
::
+$  update
  $%  [%created id=@ud name=@t time=@da]
      [%deleted id=@ud time=@da]
      [%updated id=@ud name=@t time=@da]
  ==
::
::  +$  item: Data structure
::
+$  item
  $:  id=@ud
      name=@t
      created=@da
      modified=@da
  ==
--
```

**Pattern 2: Complex App Types**

```hoon
::  sur/my-app.hoon
|%
::  Core data types
+$  user-id  @p
+$  item-id  @ud
+$  tag  @tas
::
::  User data
+$  user
  $:  id=user-id
      name=@t
      email=@t
      created=@da
      active=?
  ==
::
::  Item data
+$  item
  $:  id=item-id
      owner=user-id
      title=@t
      content=@t
      tags=(set tag)
      created=@da
      modified=@da
  ==
::
::  Actions (incoming)
+$  action
  $%  ::  User actions
      [%create-user name=@t email=@t]
      [%update-user id=user-id name=@t]
      [%delete-user id=user-id]
      ::  Item actions
      [%create-item title=@t content=@t]
      [%update-item id=item-id title=@t content=@t]
      [%delete-item id=item-id]
      [%add-tag id=item-id tag=tag]
  ==
::
::  Updates (outgoing)
+$  update
  $%  ::  User updates
      [%user-created id=user-id name=@t]
      [%user-updated id=user-id name=@t]
      [%user-deleted id=user-id]
      ::  Item updates
      [%item-created id=item-id title=@t]
      [%item-updated id=item-id title=@t]
      [%item-deleted id=item-id]
  ==
--
```

**Pattern 3: Versioned State from Start**

```hoon
::  sur/my-app.hoon
|%
+$  item  [id=@ud name=@t]
::
::  State version 0
+$  state-0
  $:  items=(list item)
      next-id=@ud
  ==
::
::  Versioned state (add new versions here)
+$  versioned-state
  $%  [%0 state-0]
      ::  [%1 state-1]  ::  Add when migrating
  ==
--
```

**Success Criteria**:
- All types defined
- Action/update types clear
- State versioned from start
- Documentation comments added

---

### Phase 4: Library Implementation (lib/)

**Objective**: Implement pure business logic.

**Pattern 1: Helper Functions**

```hoon
::  lib/my-app.hoon
::
::  Utility functions for my-app
::
/-  *my-app
::
|%
::  +$  generate-id: Create unique ID
::
++  generate-id
  |=  next-id=@ud
  ^-  @ud
  next-id
::
::  +$  make-item: Create new item
::
++  make-item
  |=  [id=@ud name=@t now=@da]
  ^-  item
  [id name now now]
::
::  +$  find-item: Find item by ID
::
++  find-item
  |=  [items=(list item) target-id=@ud]
  ^-  (unit item)
  |-  ^-  (unit item)
  ?~  items  ~
  ?:  =(id.i.items target-id)  `i.items
  $(items t.items)
::
::  +$  update-item: Update item name
::
++  update-item
  |=  [item=item new-name=@t now=@da]
  ^-  item
  item(name new-name, modified now)
--
```

**Pattern 2: State Operations**

```hoon
::  lib/my-app.hoon
/-  *my-app
::
|%
::  +$  add-item: Add item to state
::
++  add-item
  |=  [state=state-0 name=@t now=@da]
  ^-  [id=@ud state=state-0]
  =/  id  next-id.state
  =/  item  [id name now now]
  =/  new-items  [item items.state]
  [id state(items new-items, next-id +(next-id.state))]
::
::  +$  remove-item: Remove item from state
::
++  remove-item
  |=  [state=state-0 target-id=@ud]
  ^-  state-0
  state(items (skip items.state |=(i=item =(id.i target-id))))
::
::  +$  get-item: Retrieve item
::
++  get-item
  |=  [state=state-0 target-id=@ud]
  ^-  (unit item)
  (find items.state |=(i=item =(id.i target-id)))
--
```

**Success Criteria**:
- Pure functions (no side effects)
- Well-documented
- Reusable
- Tested

---

### Phase 5: Agent Scaffolding (app/)

**Objective**: Create Gall agent structure.

**Minimal Agent Template**:

```hoon
::  app/my-app.hoon
::
::  My App - Brief description
::
/-  *my-app
/+  default-agent, dbug, *my-app
::
|%
+$  versioned-state  versioned-state:my-app
+$  card  card:agent:gall
--
::
=|  state=versioned-state
::
%-  agent:dbug
^-  agent:gall
::
|_  =bowl:gall
+*  this  .
    def   ~(. (default-agent this %|) bowl)
::
++  on-init
  ^-  (quip card _this)
  ~&  >  '%my-app initialized'
  `this(state [%0 items=~ next-id=0])
::
++  on-save
  ^-  vase
  !>(state)
::
++  on-load
  |=  old-vase=vase
  ^-  (quip card _this)
  =/  old  !<(versioned-state old-vase)
  ?-  -.old
    %0  `this(state old)
  ==
::
++  on-poke
  |=  [=mark =vase]
  ^-  (quip card _this)
  ?>  =(src.bowl our.bowl)
  ?+    mark  (on-poke:def mark vase)
      %my-app-action
    =/  act  !<(action vase)
    ?-    -.act
        %create
      =/  [id new-state]  (add-item +.state name.act now.bowl)
      :_  this(state [%0 new-state])
      ~[[%give %fact ~[/updates] %my-app-update !>([%created id name.act now.bowl])]]
    ::
        %delete
      :_  this(state [%0 (remove-item +.state id.act)])
      ~[[%give %fact ~[/updates] %my-app-update !>([%deleted id.act now.bowl])]]
    ::
        %update
      ::  TODO: Implement update
      `this
    ==
  ==
::
++  on-watch
  |=  =path
  ^-  (quip card _this)
  ?>  =(src.bowl our.bowl)
  ?+    path  (on-watch:def path)
      [%updates ~]
    :_  this
    :~  [%give %fact ~ %my-app-update !>([%init items.+.state])]
    ==
  ==
::
++  on-peek
  |=  =path
  ^-  (unit (unit cage))
  ?+    path  (on-peek:def path)
      [%x %items ~]
    ``my-app-items+!>(items.+.state)
  ::
      [%x %count ~]
    ``atom+!>((lent items.+.state))
  ==
::
++  on-leave  on-leave:def
++  on-agent  on-agent:def
++  on-arvo   on-arvo:def
++  on-fail   on-fail:def
--
```

**Success Criteria**:
- All 10 arms present
- State properly managed
- Actions handled
- Subscriptions work
- Scries implemented

---

### Phase 6: Supporting Files

**Objective**: Add marks, generators, and documentation.

**Marks (mar/)**:

```hoon
::  mar/my-app/action.hoon
/-  *my-app
::
|_  act=action
++  grab
  |%
  ++  noun  action
  --
++  grow
  |%
  ++  noun  act
  --
++  grad  %noun
--
```

```hoon
::  mar/my-app/update.hoon
/-  *my-app
::
|_  upd=update
++  grab
  |%
  ++  noun  update
  --
++  grow
  |%
  ++  noun  upd
  --
++  grad  %noun
--
```

**Generator for Testing**:

```hoon
::  gen/my-app/test.hoon
::
::  Test my-app functionality
::
/-  *my-app
/+  *my-app
::
:-  %say
|=  *
:-  %noun
::
=/  tests
  :~  ['generate-id' .=(0 (generate-id 0))]
      ['make-item' ?=(item (make-item 0 'test' ~2024.1.1))]
      ['add-item-increases-count'
        =/  state  [%0 items=~ next-id=0]
        =/  [id new-state]  (add-item state 'test' ~2024.1.1)
        .=(1 (lent items.new-state))
      ]
  ==
::
%+  turn  tests
|=  [name=@t result=?]
~&  ?:(result > "✓ {<name>}" >>> "✗ {<name>}")
[name result]
```

**README.md**:

```markdown
# My App

Brief description of what this app does.

## Installation

Install on your ship:
```
|install ~your-ship %my-app
```

## Usage

Start the app:
```
:my-app
```

Create an item:
```
:my-app &my-app-action [%create 'my-item']
```

List items:
```
.^((list item) %gx /=my-app=/items/noun)
```

## Development

Mount desk:
```
|mount %my-app
```

Test:
```
+my-app/test
```

## License

MIT
```

---

## Scaffolding Templates

### Template: Counter App
```hoon
::  Minimal counter with increment/decrement
State: [count=@ud]
Actions: [%inc] [%dec]
Updates: [%count @ud]
```

### Template: Todo List
```hoon
::  Simple todo list
State: [items=(list item)]
Actions: [%create] [%complete] [%delete]
Updates: [%created] [%completed] [%deleted]
```

### Template: Key-Value Store
```hoon
::  Generic storage
State: [data=(map @t @t)]
Actions: [%put key=@t val=@t] [%del key=@t]
Updates: [%updated key=@t] [%deleted key=@t]
```

### Template: Chat App
```hoon
::  Simple chat
State: [messages=(list message)]
Actions: [%send text=@t]
Updates: [%message from=@p text=@t time=@da]
```

---

## Scaffolding Checklist

### Planning
- [ ] Project scope defined
- [ ] Architecture chosen
- [ ] Dependencies identified
- [ ] File structure planned

### Setup
- [ ] Desk created
- [ ] desk.bill created
- [ ] sys.kelvin set
- [ ] Directory structure created

### Types (sur/)
- [ ] Data types defined
- [ ] Actions defined
- [ ] Updates defined
- [ ] State versioned

### Logic (lib/)
- [ ] Helper functions written
- [ ] State operations implemented
- [ ] Pure functions (no side effects)
- [ ] Documented

### Agent (app/)
- [ ] All 10 arms present
- [ ] on-init creates state
- [ ] on-poke handles actions
- [ ] on-watch manages subscriptions
- [ ] on-peek provides scries

### Supporting
- [ ] Marks created
- [ ] Test generator added
- [ ] README written
- [ ] Comments added

---

## Integration with Skills

This command references:
- **gall-agents** - Agent structure
- **type-system** - Type definition
- **app-development-workflow** - Development process
- **hoon-style-guide** - Conventions

---

## Success Metrics

- **Compiles cleanly** - No errors on first commit
- **Tests pass** - Generator runs successfully
- **Well-structured** - Follows conventions
- **Documented** - README and comments present
- **Versioned** - State versioned from start
