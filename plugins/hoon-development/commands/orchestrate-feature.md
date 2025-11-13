---
name: orchestrate-feature
description: Intelligent end-to-end orchestration for Hoon feature development. Dynamically coordinates design, implementation, review, testing, optimization, and deployment phases with cross-plugin integration.
subagent_type: feature-orchestrator
---

# Orchestrate Feature Command

Intelligent orchestration for complete Hoon feature development lifecycles that require dynamic decision-making, multi-agent coordination, and cross-cutting concerns spanning development through production deployment.

## When to Use This Command

Use `/hoon-development:orchestrate-feature` when you need:

✅ **Complete Feature Lifecycles:**
- New Gall agent development (small to complex)
- Major enhancements to existing agents
- Complex generator or thread development
- State migration workflows

✅ **Multi-Phase Development:**
- Design → Implement → Review → Test → Optimize → Deploy
- Test-driven development (TDD) workflows
- Iterative development with quality gates

✅ **Cross-Plugin Integration:**
- Features requiring production deployment (+ urbit-operations)
- Performance optimization requiring Nock analysis (+ nock-development)
- Full-stack development (Hoon + deployment + monitoring)

✅ **High-Quality Requirements:**
- Production-ready code with comprehensive testing
- Enterprise applications requiring security audits
- Educational deep-dives with detailed explanations

❌ **Do NOT Use for Simple Tasks:**
- Quick code snippet → Use hoon-expert agent directly
- Simple code review → Use code-reviewer agent directly
- One-off debugging → Use debugging-specialist agent directly
- Reviewing existing code → Use `/hoon-development:hoon-review` command

## How It Works

The feature-orchestrator is **intelligent**, not scripted. It:

1. **Analyzes** your feature requirements and constraints
2. **Decides** which agents to use and in what sequence
3. **Coordinates** multiple specialists for optimal outcomes
4. **Validates** each phase with quality gates
5. **Adapts** when requirements change or failures occur

## Development Patterns

### Pattern 1: New Gall Agent (Complete Lifecycle)

**User Goal:** Build a production-ready Gall agent from scratch

**Orchestration Flow:**
```markdown
Phase 1: Architecture & Design (3-5 days)
  → app-architect:
    - Design agent architecture
    - Design state model and data structures
    - Design API (pokes, scries, subscriptions)
    - Document design decisions
  → User approval checkpoint

Phase 2: Implementation (1-2 weeks)
  → hoon-expert:
    - Scaffold Gall agent structure
    - Implement state management
    - Implement poke handlers
    - Implement subscription model
    - Implement scry endpoints
  → Incremental validation throughout

Phase 3: Code Review (2-3 days)
  → code-reviewer:
    - Security review (permissions, validation)
    - Code quality review (style, patterns)
    - Type safety verification
    - Error handling review
  → hoon-expert: Address review findings
  → code-reviewer: Re-review if needed

Phase 4: Testing (3-5 days)
  → hoon-expert:
    - Write unit tests (target: 80%+ coverage)
    - Write integration tests
    - Write property-based tests (if applicable)
  → debugging-specialist:
    - Run test suite
    - Debug failures
    - Load testing
    - Memory profiling

Phase 5: Optimization (2-4 days, optional)
  → nock-development:optimization-specialist:
    - Profile Nock compilation
    - Identify bottlenecks
  → hoon-expert:
    - Implement optimizations
    - Verify performance improvements

Phase 6: Production Deployment (1-2 weeks, optional)
  → urbit-operations:deployment-orchestrator:
    - Deploy staging environment
    - User acceptance testing
    - Deploy production environment
    - Configure monitoring

Phase 7: Documentation & Handoff
  → hoon-expert:
    - Generate API documentation
    - Write user guide
    - Create maintenance runbook
```

**Timeline:** 3-6 weeks (depending on complexity and deployment needs)
**Deliverables:** Production Gall agent, tests, documentation, optional production deployment

### Pattern 2: Feature Enhancement (Existing Agent)

**User Goal:** Add new feature to existing Gall agent

**Orchestration Flow:**
```markdown
Phase 1: Analysis (1 day)
  → hoon-expert:
    - Analyze existing codebase
    - Understand current architecture
    - Identify integration points
  → app-architect:
    - Design feature integration
    - Assess impact on existing state

Phase 2: Implementation (3-7 days)
  → hoon-expert:
    - Implement new feature
    - Maintain backward compatibility
    - Update tests

Phase 3: Review (1-2 days)
  → code-reviewer:
    - Verify no regressions
    - Review new code
  → hoon-expert: Address findings

Phase 4: Testing (2-3 days)
  → debugging-specialist:
    - Test new feature
    - Test existing functionality (regression tests)
    - Integration testing

Phase 5: Optional Deployment
  → urbit-operations:deployment-orchestrator (if production)
```

**Timeline:** 1-2 weeks
**Deliverables:** Enhanced agent, updated tests, documentation

### Pattern 3: Test-Driven Development (TDD)

**User Goal:** Develop feature using TDD methodology

**Orchestration Flow:**
```markdown
For Each Feature Increment:

  Iteration N (Red → Green → Refactor):

  1. Red Phase:
    → hoon-expert: Write failing test for desired behavior
    → debugging-specialist: Verify test fails appropriately

  2. Green Phase:
    → hoon-expert: Implement minimal code to pass test
    → debugging-specialist: Verify test passes

  3. Refactor Phase:
    → hoon-expert: Refactor for quality
    → debugging-specialist: Verify tests still pass
    → code-reviewer: Review refactored code

  Repeat until feature complete

Final Validation:
  → debugging-specialist: Run full test suite
  → code-reviewer: Comprehensive review
```

**Timeline:** Varies by feature complexity
**Benefits:** High test coverage, fewer bugs, clean design

### Pattern 4: Performance Optimization

**User Goal:** Optimize slow or resource-intensive Gall agent

**Orchestration Flow:**
```markdown
Phase 1: Profiling (1 day)
  → debugging-specialist:
    - Profile runtime performance
    - Identify bottlenecks (CPU, memory, I/O)
    - Establish baseline metrics

Phase 2: Analysis (1 day)
  → nock-development:optimization-specialist:
    - Analyze Nock compilation
    - Identify inefficient patterns
  → hoon-expert:
    - Analyze Hoon code patterns
    - Identify algorithmic improvements

Phase 3: Optimization (2-5 days)
  → hoon-expert:
    - Implement optimizations:
      - Data structure improvements
      - Algorithm optimizations
      - Caching strategies
  → nock-development:optimization-specialist:
    - Optimize Nock-level issues

Phase 4: Validation (1-2 days)
  → debugging-specialist:
    - Benchmark before/after
    - Verify performance targets met
    - Ensure no regressions
  → code-reviewer:
    - Review optimizations
    - Verify correctness maintained
```

**Timeline:** 5-10 days
**Target:** 50%+ performance improvement
**Deliverables:** Optimized code, benchmark reports

### Pattern 5: State Migration

**User Goal:** Update Gall agent state structure safely

**Orchestration Flow:**
```markdown
Phase 1: Migration Design (2-3 days)
  → app-architect:
    - Design new state structure
    - Design migration path (old → new)
    - Plan versioning strategy
    - Validate no data loss

Phase 2: Implementation (3-5 days)
  → hoon-expert:
    - Implement on-load migration arm
    - Implement state versioning
    - Handle edge cases (missing data, corrupt state)
    - Implement rollback mechanism

Phase 3: Testing (3-5 days)
  → debugging-specialist:
    - Create test dataset (old state variations)
    - Test migration (all scenarios)
    - Verify data integrity
    - Test rollback
  → hoon-expert: Fix migration issues

Phase 4: Staged Deployment (1-2 weeks)
  → urbit-operations:deployment-orchestrator:
    - Deploy to dev ship (test migration)
    - Deploy to staging (validate with real data)
    - Deploy to production (careful monitoring)
    - Monitor for 7 days
```

**Timeline:** 2-3 weeks
**Risk:** High (state corruption risk)
**Mitigation:** Comprehensive testing, staged rollout, rollback plan

## Command Invocation

### Interactive Mode (Recommended)

The orchestrator will ask clarifying questions:

```bash
/hoon-development:orchestrate-feature
```

**Example Questions:**
- What type of feature are you developing? (new Gall agent, enhancement, generator, other)
- What is the complexity? (simple, moderate, complex)
- Do you need production deployment? (yes/no)
- What is your Hoon experience level? (beginner, intermediate, expert)
- What is your timeline? (urgent, 1 week, 1 month, flexible)
- Do you want to use TDD? (yes/no)

### Direct Mode (Advanced)

Provide requirements directly:

```markdown
Request: Build a distributed task management Gall agent with state synchronization, comprehensive testing, and production deployment. Timeline: 4 weeks.
```

The orchestrator will:
1. Analyze requirements
2. Generate multi-phase plan
3. Request approval
4. Execute orchestration
5. Deliver production-ready feature

## Expected Outputs

### During Orchestration

**Phase Reports:**
Each completed phase generates a report:
```markdown
Phase 2 Complete: Implementation

Accomplishments:
- Gall agent scaffolded (/app/task-manager.hoon)
- State management implemented
- Poke handlers: create-task, update-task, delete-task
- Subscription model: task-updates
- Scry endpoints: /x/tasks, /x/task/[id]

Code Statistics:
- Lines of code: 450
- Functions: 18
- Type definitions: 12

Issues Encountered:
- Initial subscription pattern had race condition
- Fixed with queue-based synchronization

Next Phase: Code Review
Proceed? (yes/no)
```

### Final Feature Report

```markdown
Feature Complete: Task Manager Gall Agent

Summary:
- Feature: Production-ready task management system
- Development Time: 21 days (3 weeks)
- Code Quality: Reviewed and approved
- Test Coverage: 88% (exceeds 80% target)

Deliverables:
✓ /app/task-manager.hoon (Gall agent, 650 lines)
✓ /sur/task-manager.hoon (type definitions, 120 lines)
✓ /mar/task-action.hoon (action mark)
✓ /mar/task-update.hoon (update mark)
✓ /tests/task-manager.hoon (comprehensive tests, 320 lines)
✓ docs/api.md (API documentation)
✓ docs/user-guide.md (user documentation)

Code Review Results:
✓ Security: No vulnerabilities (permission checks validated)
✓ Performance: Optimized (handles 1000+ tasks efficiently)
✓ Style: Follows Hoon style guide
✓ Documentation: Comprehensive inline comments

Testing Results:
✓ Unit tests: 52/52 passed
✓ Integration tests: 12/12 passed
✓ Property-based tests: 8/8 passed
✓ Load test: 1000 tasks (stable, 4MB memory)
✓ Stress test: 100 concurrent operations (no race conditions)

Optimization Results:
✓ Task lookup: O(log n) (map-based indexing)
✓ Subscription fan-out: O(n) (optimized from O(n²))
✓ Memory usage: 4MB for 1000 tasks (within target)
✓ Performance: 85% faster than initial implementation

Production Deployment: (Optional)
✓ Staging: Deployed and validated
✓ Production: Deployed to ~sampel-palnet
✓ Monitoring: Grafana dashboard configured
✓ SLA: 99.9% uptime (7-day monitoring period)

Next Steps:
1. User onboarding and documentation
2. Collect user feedback
3. Plan v1.1 enhancements (suggested features)
4. Quarterly code review

Files Generated:
- Source code: /home/user/task-manager/
- Tests: /home/user/task-manager/tests/
- Documentation: /home/user/task-manager/docs/
- Benchmarks: /home/user/task-manager/benchmarks/
```

## Success Criteria

Feature is considered complete when:

✅ **Functional Requirements Met:**
- All specified features implemented
- Feature works as designed
- No critical bugs

✅ **Quality Requirements Met:**
- Code review passed (security, style, correctness)
- Test coverage ≥80% (or specified target)
- Documentation complete

✅ **Performance Requirements Met:**
- Performance targets achieved
- Memory usage within limits
- No performance regressions

✅ **Deployment Requirements Met:** (if applicable)
- Staging deployment successful
- Production deployment successful
- Monitoring configured

## Orchestrator Decision Logic

The orchestrator uses this decision matrix:

### Feature Type Selection

```markdown
IF (feature == "new Gall agent"):
  IF (complexity == "simple"):
    → Agents: app-architect (light), hoon-expert, code-reviewer
    → Timeline: 1-2 weeks
  ELSE IF (complexity == "complex"):
    → Agents: app-architect, hoon-expert, code-reviewer, debugging-specialist
    → Optional: nock-development:optimization-specialist
    → Timeline: 3-4 weeks

ELSE IF (feature == "enhancement"):
  → Agents: hoon-expert (analyze), code-reviewer, hoon-expert (implement)
  → Timeline: 1-2 weeks

ELSE IF (feature == "bug fix"):
  → Agents: debugging-specialist, hoon-expert
  → Timeline: 1-3 days

ELSE IF (feature == "performance optimization"):
  → Agents: debugging-specialist, nock-development:optimization-specialist, hoon-expert
  → Timeline: 5-10 days
```

### Testing Strategy Selection

```markdown
IF (tdd_requested == true):
  → Use TDD workflow (Red → Green → Refactor)
  → Test coverage: 100% by design

ELSE IF (production_deployment == true):
  → Comprehensive testing (unit + integration + load)
  → Test coverage target: 85%+

ELSE:
  → Standard testing (unit + basic integration)
  → Test coverage target: 80%
```

### Deployment Integration

```markdown
IF (deploy_to_production == true):
  → Add Phase: Staging deployment
  → Add Phase: Production deployment (urbit-operations:deployment-orchestrator)
  → Add Phase: Monitoring setup
  → Add: 7-day monitoring period

ELSE IF (deploy_to_staging == true):
  → Add Phase: Staging deployment only

ELSE:
  → Skip deployment phases (local development only)
```

## Failure Handling

The orchestrator handles failures gracefully:

### Code Review Failures (Iterate)
- Security issue found
- Style violations
- Type errors

**Action:**
1. hoon-expert: Address findings
2. code-reviewer: Re-review
3. Repeat until approved

### Test Failures (Debug)
- Tests failing
- Edge cases missed
- Performance regression

**Action:**
1. debugging-specialist: Diagnose failures
2. hoon-expert: Fix issues
3. Rerun tests
4. Repeat until all pass

### Design Issues (Refactor)
- Architecture doesn't scale
- State model needs redesign
- API design issues

**Action:**
1. app-architect: Redesign affected components
2. hoon-expert: Refactor implementation
3. code-reviewer: Validate refactoring
4. Resume workflow

## Integration with Other Commands

The orchestrator can invoke other Hoon development commands:

- `/hoon-development:hoon-review` - Code review workflows
- `/hoon-development:hoon-optimize` - Performance optimization
- `/hoon-development:hoon-debug` - Debugging workflows
- `/hoon-development:hoon-test` - Comprehensive testing
- `/hoon-development:hoon-scaffold` - Project scaffolding
- `/hoon-development:hoon-migrate` - State migration workflows

Cross-plugin commands:
- `/urbit-operations:orchestrate-deployment` - Production deployment
- `/nock-development:optimize-nock-performance` - Nock optimization

## Estimated Timeline

**Simple Feature** (generator, simple enhancement):
- Timeline: 3-7 days
- Agents: 2-3
- Phases: 3-4

**Medium Feature** (small Gall agent, moderate enhancement):
- Timeline: 1-3 weeks
- Agents: 3-4
- Phases: 5-6

**Complex Feature** (complex Gall agent, state migration):
- Timeline: 3-6 weeks
- Agents: 4-6
- Phases: 6-8

**With Production Deployment:**
- Add: +1-2 weeks
- Additional Agents: +2-3 (urbit-operations)

## Support

During orchestration:
- **Real-time progress updates** at each phase
- **Quality gate checkpoints** requiring approval
- **Detailed error messages** with remediation steps
- **Iteration support** for review/test failures

After feature completion:
- **Code documentation** (inline + API docs)
- **User guide** for end users
- **Maintenance runbook** for developers
- **Optional training** for team handoff

## Related Commands

**Other hoon-development commands:**
- `/hoon-review` - Code review only
- `/hoon-optimize` - Performance optimization only
- `/hoon-debug` - Debugging only
- `/hoon-test` - Testing only
- `/hoon-scaffold` - Project scaffolding only
- `/hoon-migrate` - State migration only
- `/hoon-refactor` - Code refactoring only

**Cross-plugin orchestrators:**
- `/urbit-operations:orchestrate-deployment` - Production deployment
- `/nock-development:orchestrate-interpreter` - Nock interpreter development

---

The feature-orchestrator provides **intelligent, adaptive orchestration** for complete Hoon feature development. It thinks, decides, and coordinates—delivering production-quality features through expert multi-agent collaboration.
