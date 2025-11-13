---
name: orchestrate-deployment
description: Intelligent multi-agent orchestration for complex Urbit deployments. Dynamically selects deployment paths, coordinates specialists, and handles full-stack workflows (dev → staging → prod) with cross-plugin integration.
subagent_type: deployment-orchestrator
---

# Orchestrate Deployment Command

Intelligent orchestration for complex Urbit deployment workflows that require dynamic decision-making, multi-agent coordination, and cross-cutting concerns.

## When to Use This Command

Use `/urbit-operations:orchestrate-deployment` when you need:

✅ **Complex Multi-Environment Deployments**
- Full-stack deployment (development → staging → production)
- Multi-region deployments with disaster recovery
- HIPAA/GDPR compliance-required deployments

✅ **Decision Support Required**
- Unsure which deployment model to use (bare-metal vs VPS vs GroundSeg vs Kubernetes)
- Need cost vs performance vs security trade-off analysis
- Multiple valid approaches, need optimization

✅ **Cross-Plugin Integration**
- Deploying a new Hoon application to production (hoon-development + urbit-operations)
- Performance optimization spanning Nock + deployment + monitoring
- Troubleshooting issues across multiple domains

✅ **High-Stakes Operations**
- Enterprise fleet deployments (50+ ships)
- Production deployments requiring validation gates
- Mission-critical deployments with strict SLAs

❌ **Do NOT Use for Simple Tasks**
- Single ship deployment → Use `/urbit-operations:deploy-planet` or `/urbit-operations:deploy-vps-planet`
- Monitoring setup only → Use `/urbit-operations:setup-monitoring`
- Simple troubleshooting → Use `/urbit-operations:troubleshoot-ship`

## How It Works

The deployment-orchestrator is **intelligent**, not scripted. It:

1. **Analyzes** your requirements, constraints, and goals
2. **Decides** which deployment path and agents to use
3. **Coordinates** multiple agents in the optimal sequence
4. **Validates** each phase before proceeding
5. **Adapts** when failures occur or requirements change

## Orchestration Patterns

### Pattern 1: Full-Stack Multi-Environment Deployment

**User Goal:** Deploy to production with dev and staging environments

**Orchestration Flow:**
```markdown
Phase 1: Requirements Analysis
  → deployment-orchestrator analyzes requirements
  → managed-hosting-advisor provides platform recommendations
  → User confirms platform selection

Phase 2: Development Environment
  → vps-deployment-specialist (for VPS) OR fleet-manager (for K8s)
  → Deploy development ship(s)
  → Validate basic functionality

Phase 3: Staging Environment
  → vps-deployment-specialist OR fleet-manager
  → Deploy staging ship(s)
  → urbit-deployment-specialist: Security hardening
  → performance-engineer: Monitoring setup
  → Validate performance and security

Phase 4: Production Environment
  → vps-deployment-specialist OR fleet-manager
  → Deploy production ship(s)
  → urbit-deployment-specialist: Full production hardening
  → performance-engineer: Full monitoring + alerts
  → Compliance validation (if required)

Phase 5: Handoff
  → Generate deployment report
  → Create runbook
  → Train operations team
```

**Timeline:** 2-4 weeks depending on scale

### Pattern 2: End-to-End Feature Development + Deployment

**User Goal:** Develop a new Hoon app and deploy to production

**Orchestration Flow:**
```markdown
Phase 1: Development (hoon-development plugin)
  → hoon-development:feature-orchestrator
    - Scaffold Gall agent
    - Implement and test feature
    - Code review

Phase 2: Optimization (nock-development plugin)
  → nock-development:optimization-specialist
    - Profile Nock execution
    - Optimize hot paths

Phase 3: Staging Deployment (urbit-operations)
  → vps-deployment-specialist: Deploy test ship
  → Install and test Gall agent
  → performance-engineer: Benchmark in staging

Phase 4: Production Deployment
  → urbit-deployment-specialist: Production hardening
  → Deploy to production ship
  → Setup monitoring

Phase 5: Validation
  → 7-day monitoring period
  → Performance validation
  → User acceptance testing
```

**Timeline:** 2-3 weeks
**Plugins Used:** hoon-development, nock-development, urbit-operations

### Pattern 3: Complex Troubleshooting Across Stack

**User Goal:** Ships are slow and occasionally crashing

**Orchestration Flow:**
```markdown
Phase 1: Initial Triage (5-15 minutes)
  → performance-engineer: Check dashboards
  → Identify symptoms and affected components

Phase 2: Root Cause Analysis (15-30 minutes)
  → If Hoon code issue:
    - hoon-development:debugging-specialist
  → If Nock optimization needed:
    - nock-development:optimization-specialist
  → If infrastructure issue:
    - urbit-deployment-specialist OR fleet-manager

Phase 3: Resolution (30-60 minutes)
  → Apply fixes based on root cause
  → Validate resolution
  → Monitor for regression

Phase 4: Prevention
  → Update monitoring alerts
  → Document issue and resolution
  → Implement preventive measures
```

**Timeline:** 1-2 hours for triage and fix, 24-48 hours monitoring

### Pattern 4: Enterprise Fleet Deployment with Compliance

**User Goal:** Deploy 50-ship fleet with HIPAA compliance

**Orchestration Flow:**
```markdown
Phase 1: Planning (Week 1)
  → managed-hosting-advisor: Platform selection
  → fleet-manager: Architecture design
  → Cost estimation and approval

Phase 2: Infrastructure (Week 2)
  → fleet-manager: Provision Kubernetes/GroundSeg
  → urbit-deployment-specialist: Security baseline
  → Setup networking and storage

Phase 3: Security & Compliance (Week 2-3)
  → urbit-deployment-specialist: HIPAA hardening
    - LUKS encryption
    - Audit logging
    - Access controls
  → Compliance validation

Phase 4: Monitoring (Week 3)
  → performance-engineer:
    - Prometheus + Grafana
    - PagerDuty integration
    - SLA monitoring

Phase 5: Ship Deployment (Week 3-4)
  → fleet-manager: Bulk ship deployment
  → Staged rollout with validation

Phase 6: Handoff (Week 4)
  → Operations training
  → Documentation
  → 30-day support period
```

**Timeline:** 4 weeks
**Deliverables:** Production fleet, compliance audit report, runbooks

## Command Invocation

### Interactive Mode (Recommended)

The orchestrator will ask clarifying questions to understand your needs:

```bash
/urbit-operations:orchestrate-deployment
```

**Example Questions:**
- What is your deployment goal? (single ship, small fleet, enterprise)
- What is your experience level? (beginner, intermediate, expert)
- Do you have infrastructure preferences? (bare-metal, VPS, containers, Kubernetes)
- Do you have compliance requirements? (HIPAA, GDPR, SOC2)
- What is your budget? ($0-50/month, $50-500/month, $500+/month)
- What is your timeline? (immediate, 1 week, 1 month)

### Direct Mode (Advanced)

Provide requirements directly:

```markdown
Request: Deploy a 50-ship production fleet on Kubernetes with HIPAA compliance, monitoring, and disaster recovery. Budget $2,000/month, timeline 4 weeks.
```

The orchestrator will:
1. Analyze requirements
2. Generate multi-phase plan
3. Request approval
4. Execute orchestration
5. Deliver production-ready fleet

## Expected Outputs

### During Orchestration

**Phase Reports:**
Each completed phase generates a report:
```markdown
Phase 2 Complete: Infrastructure Provisioning

Accomplishments:
- AWS EKS cluster created (us-west-2)
- 10 worker nodes provisioned (m5.2xlarge)
- S3 backup bucket configured
- VPC networking configured

Outputs:
- Cluster endpoint: https://ABC123.eks.us-west-2.amazonaws.com
- Node count: 10
- Storage: 5TB EBS gp3

Issues Encountered:
- None

Next Phase: Security & Compliance Hardening
Proceed? (yes/no)
```

### Final Deployment Report

```markdown
Deployment Complete: Enterprise Urbit Fleet

Summary:
- Platform: AWS EKS (Kubernetes)
- Ships Deployed: 50
- Region: us-west-2 (primary), us-east-1 (DR)
- Monthly Cost: $1,850 (under $2,000 budget)
- Deployment Time: 27 days (within 4-week timeline)

Deliverables:
✓ Production Kubernetes cluster
✓ 50 ships deployed and validated
✓ HIPAA compliance audit passed
✓ Monitoring stack (Prometheus, Grafana, PagerDuty)
✓ Disaster recovery configured (cross-region backups)
✓ Operations runbook
✓ Team training completed

Access:
- Fleet Dashboard: https://grafana.fleet.example.com
- Ship URLs: https://*.fleet.example.com
- Backup Bucket: s3://fleet-backups-2025

Security:
✓ LUKS encryption enabled
✓ RBAC configured (multi-tenant isolation)
✓ Network policies enforced
✓ TLS certificates automated
✓ Audit logging (7-year retention)
✓ Penetration testing passed

Performance:
✓ 99.9% uptime SLA achieved
✓ <50ms response time
✓ Auto-scaling configured (10-20 nodes)
✓ Cost per ship: $37/month

Compliance:
✓ HIPAA audit passed
✓ Encryption at rest and in transit
✓ Access controls validated
✓ Incident response plan documented

Next Steps:
1. Monitor dashboards daily for first 30 days
2. Schedule monthly optimization reviews
3. Plan Q2 capacity (forecast: 75 ships)
4. Quarterly compliance re-audit

Handoff:
- Operations team: 4 members trained
- On-call rotation: Active (PagerDuty)
- Support: 30-day included, then monthly retainer
```

## Success Criteria

Deployment is considered successful when:

✅ **Functional Requirements Met:**
- All ships deployed and accessible
- HTTPS working with valid certificates
- Ames networking functional
- Dojo responsive (<3 seconds)

✅ **Performance Targets Met:**
- Uptime ≥99.9% (or specified SLA)
- Response time <50ms (or specified target)
- Resource utilization optimized

✅ **Security Requirements Met:**
- Encryption enabled (at rest and in transit)
- Firewall configured correctly
- Access controls in place
- Audit logging enabled (if required)

✅ **Operational Requirements Met:**
- Monitoring and alerting active
- Backups automated and tested
- Runbook documented
- Team trained

✅ **Budget and Timeline Met:**
- Deployment within budget
- Completed within timeline
- Cost per ship optimized

## Orchestrator Decision Logic

The orchestrator uses this decision matrix:

### Platform Selection

```markdown
IF (single ship) AND (beginner):
  → Use vps-deployment-specialist
  → Rationale: Easiest path, cloud-native

ELSE IF (single ship) AND (expert) AND (maximum security):
  → Use urbit-deployment-specialist (bare-metal)
  → Rationale: Full control, maximum hardening

ELSE IF (5-20 ships) AND (self-hosted):
  → Use groundseg-operator
  → Rationale: Container orchestration, cost-effective

ELSE IF (50+ ships) OR (enterprise) OR (auto-scaling):
  → Use fleet-manager (Kubernetes)
  → Rationale: Enterprise features, HA, scalability

ELSE IF (unsure):
  → Use managed-hosting-advisor (first)
  → Then route based on recommendation
```

### Security Level Selection

```markdown
IF (HIPAA OR GDPR OR SOC2):
  → Use urbit-deployment-specialist (strict hardening)
  → Include: LUKS, SELinux, audit logging, compliance validation

ELSE IF (production):
  → Use urbit-deployment-specialist (standard hardening)
  → Include: fail2ban, UFW, automatic updates

ELSE IF (development):
  → Use vps-deployment-specialist (basic security)
  → Include: UFW, SSH hardening only
```

### Monitoring Level Selection

```markdown
IF (enterprise fleet):
  → Use performance-engineer (full stack)
  → Include: Prometheus, Grafana, Loki, PagerDuty, SLO monitoring

ELSE IF (production):
  → Use performance-engineer (standard monitoring)
  → Include: Prometheus, Grafana, basic alerts

ELSE IF (development):
  → Skip monitoring (add later if needed)
```

## Failure Handling

The orchestrator handles failures gracefully:

### Transient Failures (Retry)
- Network timeout during deployment
- Temporary API rate limits
- Resource temporarily unavailable

**Action:** Retry with exponential backoff (3 attempts)

### Configuration Errors (Fix and Retry)
- Firewall misconfiguration
- DNS propagation delay
- Certificate issuance failure

**Action:**
1. Diagnose root cause
2. Apply fix
3. Retry from failed phase

### Critical Failures (Escalate)
- Insufficient budget for selected platform
- Compliance requirements cannot be met
- Technical blocker (unsupported platform)

**Action:**
1. Document failure
2. Provide diagnostic report to user
3. Suggest alternatives
4. Wait for user decision

## Integration with Other Commands

The orchestrator can invoke other operational commands as needed:

- `/deploy-planet` - For individual planet deployments
- `/deploy-vps-planet` - For VPS-based deployments
- `/deploy-groundseg` - For GroundSeg multi-ship setup
- `/setup-production` - For production hardening
- `/setup-monitoring` - For monitoring infrastructure
- `/troubleshoot-ship` - For diagnostic workflows
- `/optimize-performance` - For performance tuning

## Estimated Timeline

**Simple Orchestration** (single ship to production):
- Timeline: 1-3 days
- Agents: 2-3
- Phases: 3-5

**Medium Orchestration** (small fleet with monitoring):
- Timeline: 1-2 weeks
- Agents: 3-4
- Phases: 5-7

**Complex Orchestration** (enterprise fleet with compliance):
- Timeline: 3-4 weeks
- Agents: 4-6
- Phases: 8-12

## Support

During orchestration:
- **Real-time progress updates** at each phase
- **Validation checkpoints** requiring approval
- **Detailed error messages** with remediation steps
- **Rollback procedures** if deployment fails

After deployment:
- **30-day monitoring** (for complex deployments)
- **Runbook** for operations team
- **Training materials** and documentation
- **Follow-up optimization** review

## Related Commands

**Other urbit-operations commands:**
- `/deploy-planet` - Bare-metal single ship deployment
- `/deploy-vps-planet` - VPS single ship deployment
- `/deploy-groundseg` - Multi-ship Docker deployment
- `/setup-production` - Production hardening
- `/setup-monitoring` - Monitoring infrastructure
- `/troubleshoot-ship` - Diagnostic workflows
- `/migrate-deployment` - Migration between platforms
- `/optimize-performance` - Performance optimization

**Cross-plugin orchestrators:**
- `/hoon-development:orchestrate-feature` - Complete Hoon feature development
- `/nock-development:orchestrate-interpreter` - Nock interpreter development

---

The deployment-orchestrator provides **intelligent, adaptive orchestration** for complex Urbit operations. It thinks, decides, and coordinates—going far beyond simple scripted workflows.
