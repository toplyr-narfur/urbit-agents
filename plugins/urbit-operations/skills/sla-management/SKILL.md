---
name: sla-management
description: Service Level Agreement (SLA) management for Urbit deployments including Service Level Indicators (SLIs), Service Level Objectives (SLOs), availability targets, incident response, error budgets, and SLA frameworks. Use when defining SLAs, setting uptime targets, managing incidents, or implementing SRE practices.
---

# SLA Management Skill

Service Level Agreement (SLA) management for Urbit deployments including availability targets, incident response, and SLI/SLO/SLA frameworks.

## SLA Framework

### Service Level Indicators (SLIs)

**Availability**:
- Metric: Uptime percentage
- Measurement: `uptime`, systemd logs, external monitoring
- Formula: `(Total time - Downtime) / Total time * 100`

**Latency**:
- Metric: Dojo command response time (95th percentile)
- Measurement: Manual testing, instrumentation
- Target: <1 second

**Throughput**:
- Metric: Events processed per second
- Measurement: Event log growth rate
- Baseline: Varies by ship usage

**Error Rate**:
- Metric: Ship crashes per month
- Measurement: systemd restart count
- Target: <1 crash/month

### Service Level Objectives (SLOs)

| Service | SLO | Measurement Window | Error Budget |
|---------|-----|-------------------|--------------|
| **Availability** | 99.9% | Monthly | 43.2 min downtime |
| **Availability** | 99.95% | Monthly | 21.6 min downtime |
| **Latency (p95)** | <1s | Weekly | 5% requests >1s |
| **Latency (p99)** | <3s | Weekly | 1% requests >3s |
| **Error Rate** | <1% | Monthly | <7.2 hours errors |

### Service Level Agreements (SLAs)

**Customer-facing commitments**:
- Availability: 99.9% uptime
- Support response: <4 hours (business hours)
- Incident resolution: <24 hours (critical)
- Planned maintenance: <2 hours/month, announced 7 days advance

**Penalties** (if applicable):
- <99.9% uptime: 10% service credit
- <99% uptime: 25% service credit
- <95% uptime: 50% service credit

## Availability Calculation

```bash
#!/bin/bash
# calculate-uptime.sh

# Get system uptime (seconds)
UPTIME_SECONDS=$(awk '{print $1}' /proc/uptime | cut -d'.' -f1)

# Get total month seconds (30 days)
MONTH_SECONDS=$((30 * 24 * 60 * 60))

# Calculate downtime
DOWNTIME_SECONDS=$((MONTH_SECONDS - UPTIME_SECONDS))
DOWNTIME_MINUTES=$((DOWNTIME_SECONDS / 60))

# Calculate availability
AVAILABILITY=$(echo "scale=4; $UPTIME_SECONDS / $MONTH_SECONDS * 100" | bc)

echo "Monthly Availability: $AVAILABILITY%"
echo "Downtime: $DOWNTIME_MINUTES minutes"

# Check SLA compliance
if (( $(echo "$AVAILABILITY < 99.9" | bc -l) )); then
    echo "SLA VIOLATION: Below 99.9% target"
else
    echo "SLA COMPLIANT"
fi
```

## Incident Response

### Severity Levels

**Critical (P1)**:
- Ship completely down (no dojo, no web access)
- Data loss occurring
- Security breach
- **Response**: Immediate (24/7)
- **Resolution**: <4 hours

**High (P2)**:
- Degraded performance (slow responses)
- Some functionality unavailable
- Recent ship crashes
- **Response**: <2 hours (business hours)
- **Resolution**: <24 hours

**Medium (P3)**:
- Minor issues, workarounds available
- Non-critical features broken
- **Response**: <8 hours (business hours)
- **Resolution**: <1 week

**Low (P4)**:
- Cosmetic issues, feature requests
- **Response**: <5 business days
- **Resolution**: Backlog prioritization

### Incident Workflow

1. **Detection**: Monitoring alert, user report
2. **Triage**: Assess severity (P1-P4)
3. **Response**: Assign on-call engineer
4. **Mitigation**: Temporary fix, restore service
5. **Root cause analysis**: Identify underlying issue
6. **Resolution**: Permanent fix
7. **Postmortem**: Document lessons learned

### On-Call Rotation

```
Week 1: Engineer A (primary), Engineer B (secondary)
Week 2: Engineer B (primary), Engineer C (secondary)
Rotation: Weekly, Monday 9 AM handoff
```

**On-call responsibilities**:
- Monitor alerts (phone, email, Slack)
- Respond to P1 incidents immediately
- Escalate if unable to resolve within SLA
- Document all incidents

## Monitoring for SLA Compliance

### Availability Monitoring

```bash
# External uptime monitoring (cron every 5 minutes)
#!/bin/bash
if ! curl -f -s https://ship.yourdomain.com > /dev/null; then
    # Ship down, send alert
    curl -X POST https://hooks.slack.com/services/YOUR/WEBHOOK \
        -d '{"text":"ALERT: Ship down, SLA at risk"}'
fi
```

**Third-party uptime monitors**:
- UptimeRobot: Free, 5-minute checks
- Pingdom: Paid, 1-minute checks, multiple locations
- StatusCake: Free tier available

### Latency Monitoring

```bash
# Measure dojo latency
time_start=$(date +%s%N)
urbit attach pier <<< '+vats' > /dev/null
time_end=$(date +%s%N)
latency_ms=$(( (time_end - time_start) / 1000000 ))

if [ "$latency_ms" -gt 1000 ]; then
    echo "WARNING: Latency ${latency_ms}ms exceeds 1000ms SLO"
fi
```

### Error Budget Tracking

```yaml
# error-budget.yml
monthly_budget_minutes: 43.2  # 99.9% = 43.2 min downtime/month

incidents:
  - date: 2025-01-05
    duration_minutes: 15
    cause: "VPS maintenance"

  - date: 2025-01-12
    duration_minutes: 8
    cause: "Ship crash, OOM"

total_downtime: 23  # Sum of incidents
remaining_budget: 20.2  # 43.2 - 23
budget_consumed_percent: 53.2  # (23 / 43.2) * 100

status: "healthy"  # Or "at_risk" if >80%, "exceeded" if >100%
```

## SLA Reporting

### Monthly SLA Report Template

```
=== Monthly SLA Report: January 2025 ===

Availability:
  Target: 99.9%
  Actual: 99.94%
  Status: ✓ COMPLIANT
  Downtime: 25 minutes (budget: 43.2 min)

Latency (p95):
  Target: <1s
  Actual: 0.8s
  Status: ✓ COMPLIANT

Incidents:
  P1 (Critical): 0
  P2 (High): 1 (ship crash, resolved in 4h)
  P3 (Medium): 3
  P4 (Low): 12

Top Issues:
  1. Ship crash (OOM) - 1 occurrence
  2. Slow dojo response - 2 occurrences

Improvements:
  - Increased loom size to 4GB (prevents OOM)
  - Scheduled weekly |pack (improves performance)

Next Month Focus:
  - Implement automated failover (reduce P1 MTTR)
  - Add latency monitoring (proactive detection)
```

## Best Practices

1. **Define SLAs realistically**: Based on actual capabilities, not aspirations
2. **Measure continuously**: Automated monitoring, not manual checks
3. **Track error budget**: Know how much downtime remains
4. **Incident postmortems**: Blameless, focus on systems/processes
5. **Communicate proactively**: Notify users of incidents immediately
6. **Automate reporting**: Monthly SLA reports from metrics, not manual compilation
7. **Review quarterly**: Adjust SLOs based on actual performance trends
8. **Document everything**: Incidents, resolutions, lessons learned
9. **Test disaster recovery**: Quarterly DR drills to validate RTO/RPO
10. **Customer feedback**: SLA targets should align with user expectations

## SLA Violation Response

**If SLA breached**:
1. **Immediate notification**: Alert stakeholders
2. **Root cause analysis**: Why did it breach?
3. **Corrective action plan**: How to prevent recurrence?
4. **Service credits**: If contractually obligated
5. **SLA review**: Are targets realistic? Need infrastructure upgrade?

## Compliance Checklist

- [ ] SLIs defined and measurable
- [ ] SLOs set (realistic targets)
- [ ] SLAs documented (customer-facing)
- [ ] Monitoring automated (availability, latency, errors)
- [ ] Alerting configured (on-call rotation)
- [ ] Incident response procedures documented
- [ ] Error budget tracking active
- [ ] Monthly SLA reporting automated
- [ ] Postmortem process established
- [ ] Disaster recovery tested (quarterly)

## Summary

SLA management for Urbit deployments defines Service Level Indicators (availability, latency, error rate), Objectives (99.9% uptime = 43.2 min downtime/month), and Agreements (customer commitments with penalties). Availability calculated from system uptime, measured continuously via external monitoring (UptimeRobot, Pingdom). Incident response tiered by severity (P1 critical <4h resolution, P2 high <24h, P3 medium <1 week). Error budget tracking monitors monthly downtime consumption (healthy <80%, at-risk >80%, exceeded >100%). Monthly SLA reports document compliance, incidents, improvements. Best practices: define realistic SLAs, measure continuously, track error budget, conduct blameless postmortems, automate reporting, review quarterly, test DR procedures.
