# App Development Workflow Skill

Complete workflow for developing, testing, and distributing Urbit applications including Gall agent development, desk publishing, and peer-to-peer distribution (2025).

## Overview

Urbit app development involves creating Gall agents (backend), React front-ends, packaging into desks, and distributing via peer-to-peer network. This skill covers the complete development lifecycle.

## Urbit Development Environment

### Prerequisites

1. **Fake ship** for development (never develop on live planet!)
2. **Development tools**: Hoon, Node.js, npm/yarn
3. **Code editor**: VS Code with Hoon syntax highlighting

### Setup Development Ship

```bash
# Create fake ship (any identity, offline)
urbit -F zod  # Or any ship name

# Important: -F flag = fake ship (no network, disposable)
```

**Why fake ships?**
- No network connectivity (fast, isolated)
- Disposable (can delete and recreate)
- Any identity (can test with ~zod, ~nec, etc.)
- Safe for experimentation

---

## Gall Agent Architecture

### What is a Gall Agent?

**Gall** = Arvo kernel module for userspace applications

**Agent** = State machine with event handlers:
```
(events, old-state) => (effects, new-state)
```

### Agent Arms (Event Handlers)

```hoon
|_  bowl:gall
++  on-init     :: Initialize agent (first boot)
++  on-save     :: Export state (for upgrades)
++  on-load     :: Import state (from upgrades)
++  on-poke     :: Handle poke (command/transaction)
++  on-watch    :: Handle subscription request
++  on-leave    :: Handle unsubscription
++  on-peek     :: Handle scry (read-only query)
++  on-agent    :: Handle updates from other agents
++  on-arvo     :: Handle kernel responses (Behn, Clay, Eyre, Iris)
++  on-fail     :: Handle crashes
--
```

### Minimal Gall Agent Example

```hoon
/+  default-agent, dbug
|%
+$  versioned-state
  $%  state-0
  ==
+$  state-0  [%0 counter=@ud]
+$  card  card:agent:gall
--
%-  agent:dbug
^-  agent:gall
|_  =bowl:gall
+*  this      .
    default   ~(. (default-agent this %|) bowl)
::
++  on-init
  ^-  (quip card _this)
  `this(state [%0 counter=0])
::
++  on-poke
  |=  [=mark =vase]
  ^-  (quip card _this)
  ?>  =(src.bowl our.bowl)  :: Auth: only ship owner
  ?+    mark  (on-poke:default mark vase)
      %noun
    =/  action  !<(?(%increment %decrement) vase)
    ?-  action
        %increment
      `this(counter +(counter.state))
        %decrement
      `this(counter (sub counter.state 1))
    ==
  ==
::
++  on-save   on-save:default
++  on-load   on-load:default
++  on-watch  on-watch:default
++  on-leave  on-leave:default
++  on-peek   on-peek:default
++  on-agent  on-agent:default
++  on-arvo   on-arvo:default
++  on-fail   on-fail:default
--
```

**Test in dojo**:
```
|start %counter
:counter &noun %increment
:counter &noun %increment
:counter &noun %decrement
```

---

## Desk Structure

### Required Files

```
myapp/
├── desk.bill          # Apps to start on installation
├── desk.docket-0      # App metadata, tile config
├── sys.kelvin         # Kernel version compatibility
├── app/
│   └── myapp.hoon     # Gall agent
├── sur/
│   └── myapp.hoon     # Shared structures
├── lib/
│   └── myapp.hoon     # Shared libraries
├── mar/
│   └── myapp/
│       └── action.hoon  # Mark files (data validators)
└── gen/
    └── myapp/
        └── command.hoon # Generators (CLI tools)
```

### desk.bill

Lists Gall agents to start on desk installation.

```hoon
:~  %myapp
==
```

### desk.docket-0

Metadata for app display in Grid (home screen).

```hoon
:~
  title+'My Urbit App'
  info+'A description of my app'
  color+0x4b.c934
  version+[0 1 0]
  website+'https://myapp.example'
  license+'MIT'
  base+'myapp'
  glob-ames+[~zod 0v0]
  image+'https://example.com/icon.svg'
==
```

### sys.kelvin

Specifies kernel version compatibility.

```hoon
[%zuse 418]  # Current kelvin version (as of 2025)
```

---

## Development Workflow

### Phase 1: Local Development

**On fake ship**:
```bash
# 1. Start fake ship
urbit -F zod

# 2. Create desk
|merge %myapp our %base

# 3. Mount desk to Unix filesystem
|mount %myapp

# 4. Exit ship (Ctrl+D), edit files in zod/myapp/
# Copy your app files to zod/myapp/

# 5. Restart ship, commit changes
|commit %myapp

# 6. Install app
|install our %myapp
```

### Phase 2: Iterative Development

**File change workflow**:
```bash
# 1. Edit Hoon files in zod/myapp/
# 2. In dojo:
|commit %myapp    # Commit changes to Clay
|bump %myapp      # Restart apps in desk
```

**Test changes immediately** - no rebuild required!

### Phase 3: Testing

**Unit Tests** (tests/app/myapp.hoon):
```hoon
/+  *test, *myapp
|%
++  test-increment
  =/  initial  [%0 counter=0]
  =/  expected  [%0 counter=1]
  ;:  weld
    %+  expect-eq
      !>  expected
      !>  (increment initial)
  ==
--
```

**Run tests**:
```
-test %/tests ~
```

---

## Front-End Development (React)

### Setup React App

```bash
# Create React app
npx create-react-app myapp-ui

# Install Urbit HTTP API
cd myapp-ui
npm install @urbit/http-api
```

### Urbit API Integration

```javascript
// src/api.js
import Urbit from '@urbit/http-api';

const api = new Urbit('', '', 'myapp');
api.ship = window.ship;  // Set from index.html

// Subscribe to updates
api.subscribe({
  app: 'myapp',
  path: '/updates',
  event: (data) => console.log('Update:', data),
  err: () => console.log('Subscription error'),
  quit: () => console.log('Kicked from subscription')
});

// Poke (send command)
api.poke({
  app: 'myapp',
  mark: 'myapp-action',
  json: { increment: null }
});

// Scry (read-only query)
api.scry({
  app: 'myapp',
  path: '/counter'
}).then(data => console.log(data));

export default api;
```

### Build Glob (Front-End Bundle)

```bash
# Build production bundle
npm run build

# Upload glob to ship
cd build
ls | xargs -I {} curl -X POST -F "file=@{}" http://localhost:8080/~/my app/upload

# In dojo, get glob hash
.^(* %cx /=myapp=/desk/docket-0)
```

**Update desk.docket-0**:
```hoon
glob-ames+[~zod 0v5.abc.def.ghi]  # Use glob hash from above
```

---

## Publishing and Distribution

### Prepare for Publishing

**Checklist**:
- [ ] desk.bill lists all agents
- [ ] desk.docket-0 metadata complete
- [ ] sys.kelvin matches ship kelvin
- [ ] All mark files included
- [ ] All dependencies in desk (libraries, structures)
- [ ] Glob uploaded (if front-end exists)
- [ ] Tested on fake ship

### Publish with %treaty

```bash
# 1. Start publishing on live ship
:treaty|publish %myapp

# 2. Set treaty metadata (in dojo)
:treaty|add %myapp
```

### Distribution URL

**Share with users**:
```
web+urbitgraph://~sampel-palnet/myapp
```

**Or direct link**:
```
https://sampel-palnet.arvo.network/apps/grid/perma?patp=~sampel-palnet&desk=myapp
```

### User Installation

**Users install via**:
1. Grid → Get Apps → Search for publisher ship
2. Or paste installation link
3. Or via dojo: `|install ~sampel-palnet %myapp`

---

## Peer-to-Peer Updates

### Publishing Updates

```bash
# 1. Increment version in desk.docket-0
version+[0 2 0]  # 0.1.0 → 0.2.0

# 2. Commit changes
|commit %myapp

# 3. Publish update
:treaty|publish %myapp
```

**Users automatically receive update** via Kiln (package manager).

---

## CI/CD Integration

### Automated Testing (GitHub Actions)

See `/setup-cicd-pipeline` command for complete GitLab CI/CD configuration including:
- Validation on fake ship
- Automated test execution
- Desk tarball creation
- Deployment to production ship

---

## Development Tools (2025)

### Hoon Language Server

```bash
# Install via npm
npm install -g @urbit/hoon-language-server

# VS Code extension
code --install-extension urbit-pilled.hoon-language-server
```

### Debugging with +dbug

```hoon
/+  default-agent, dbug  # Import dbug

%-  agent:dbug  # Wrap agent
^-  agent:gall
|_  =bowl:gall
  # ... agent code ...
--
```

**Debug commands**:
```
:myapp +dbug [%state]      # View current state
:myapp +dbug [%bowl]       # View bowl
:myapp +dbug [%subscriptions]  # View subscriptions
```

---

## Best Practices

1. **Always develop on fake ships** (never on live planet)
2. **Commit frequently** (Clay version control)
3. **Test thoroughly** (unit tests, integration tests)
4. **Desk self-containment**: Include ALL dependencies (marks, libs, structures)
5. **Version semantically**: Follow semver (major.minor.patch)
6. **Document APIs**: Write clear sur/ structure definitions
7. **Security**: Validate poke sources (`?>  =(src.bowl our.bowl)`)
8. **Graceful upgrades**: Use on-save/on-load for state migration
9. **Error handling**: Never crash (use on-fail)
10. **Performance**: Minimize state size, use caching

---

## Common Patterns

### State Management

```hoon
+$  state-0  [%0 =counter =users]
+$  state-1  [%1 =counter =users =settings]  # Add settings

++  on-load
  |=  old-vase=vase
  =/  old  !<(versioned-state old-vase)
  ?-  -.old
    %0  `this(state [%1 counter.old users.old settings=~])  # Migrate
    %1  `this(state old)  # Already current version
  ==
```

### Subscription (Server)

```hoon
++  on-watch
  |=  =path
  ^-  (quip card _this)
  ?+  path  (on-watch:default path)
      [%updates ~]
    :_  this
    [%give %fact ~ %json !>((updates-to-json state))]~
  ==
```

### HTTP API Endpoint

```hoon
++  on-poke
  |=  [=mark =vase]
  ?+  mark  (on-poke:default mark vase)
      %handle-http-request
    =/  req  !<([@ta inbound-request:eyre] vase)
    :_  this
    [%give %fact ~[/http-response/[-.req]] %http-response-header !>(response)]~
  ==
```

---

## Troubleshooting

**Agent won't start**:
- Check syntax: `:myapp +dbug %state`
- Review logs: `.^(wain %cx /=myapp=/app/myapp/hoon)`

**Subscription not working**:
- Verify path in on-watch
- Check permissions (source ship allowed?)

**Glob not loading**:
- Verify glob hash in desk.docket-0
- Check Eyre serving: `http://localhost:8080/apps/myapp/`

**Desk won't install**:
- Verify sys.kelvin matches ship kelvin: `.^(@ud %cz %$)`
- Check all marks exist in desk
- Ensure desk.bill lists all agents

---

## Reference

- App School (full course): https://developers.urbit.org/guides/core/app-school
- Software Distribution: https://developers.urbit.org/guides/additional/software-distribution
- Gall Reference: https://developers.urbit.org/reference/arvo/gall/gall
- HTTP API: https://github.com/urbit/js-http-api
- Hoon School: https://developers.urbit.org/guides/core/hoon-school

---

## Summary

Urbit app development uses Gall agents (backend state machines with event handlers), React front-ends (via @urbit/http-api), and desks for distribution. Development workflow: create fake ship (-F flag), mount desk to filesystem (|mount), edit files, commit changes (|commit), and test iteratively. Distribution via %treaty agent enables peer-to-peer installation with automatic updates through Kiln. Desk structure requires desk.bill (agent list), desk.docket-0 (metadata), sys.kelvin (kernel compatibility), and self-contained dependencies (marks, libs, structures). Best practices: always use fake ships for development, commit frequently, test thoroughly, and implement proper state migration for upgrades.
