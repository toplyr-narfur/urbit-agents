---
extended_thinking: true
---

You are orchestrating the deployment of a large-scale Urbit fleet (10-1,000+ ships). This is a complex, multi-phase workflow requiring careful planning, infrastructure provisioning, security configuration, and validation.

# Deploy Fleet Command - Orchestrated Fleet Deployment Workflow

## Overview

This command guides you through deploying production-ready Urbit fleets across Kubernetes, GroundSeg, or multi-VPS platforms with:

- Infrastructure-as-Code provisioning (Terraform)
- Multi-tenant security isolation
- Centralized monitoring and alerting
- Automated backups and disaster recovery
- Compliance controls (GDPR, HIPAA, SOC2)
- Cost optimization strategies

## Workflow Phases

You will execute the following phases in sequence, using specialized agents for complex tasks:

### Phase 1: Fleet Planning & Architecture Design
### Phase 2: Infrastructure Provisioning (IaC)
### Phase 3: Multi-Tenant Isolation Setup
### Phase 4: Monitoring Infrastructure Deployment
### Phase 5: Pilot Ship Deployment (Validation)
### Phase 6: Full Fleet Bulk Deployment
### Phase 7: Fleet Validation & Handoff

---

## Pre-Flight Checklist

Before beginning, gather the following information from the user:

**Required Information:**
1. **Fleet Size:** How many ships? (10-50, 50-200, 200-500, 500-1,000+)
2. **Platform Preference:** Kubernetes (EKS/GKE/AKS), GroundSeg, Multi-VPS, Bare Metal, or Hybrid
3. **Budget:** Monthly budget in USD
4. **Compliance Requirements:** GDPR, HIPAA, SOC2, ISO 27001, PCI-DSS (if applicable)
5. **Geographic Requirements:** US-only, EU data residency, multi-region
6. **Uptime SLA:** 99%, 99.9%, 99.99%
7. **Ship Types:** Planets, moons, comets, stars (with Azimuth point ownership)
8. **Existing Infrastructure:** Starting from scratch or integrating with existing systems

**Optional Information:**
- DNS domain for ships (e.g., *.fleet.example.com)
- Existing cloud accounts (AWS, GCP, Azure)
- Existing monitoring tools (Datadog, New Relic)
- Custom security requirements
- Integration needs (billing systems, support ticketing)

**Use the AskUserQuestion tool to collect this information if not provided.**

---

## Phase 1: Fleet Planning & Architecture Design

**Objective:** Design the optimal architecture for the user's fleet based on size, budget, and compliance requirements.

**Steps:**

### 1.1: Collect Requirements

If the user hasn't provided all required information, use the AskUserQuestion tool:

```
Questions to ask:
1. Fleet Size: "How many Urbit ships do you need to deploy?"
   - Options: "10-50 ships", "50-200 ships", "200-500 ships", "500-1,000+ ships"

2. Platform: "Which platform do you prefer for hosting?"
   - Options: "Kubernetes (managed EKS/GKE/AKS)", "GroundSeg (self-hosted containers)", "Multi-VPS (DigitalOcean/Linode)", "On-premises bare metal"

3. Compliance: "Do you have compliance requirements?"
   - Options: "No compliance needs", "GDPR (EU data residency)", "HIPAA (healthcare)", "SOC2 (enterprise)"
   - Multi-select: true

4. Budget: "What is your monthly budget for infrastructure?"
   - Options: "<$500/month", "$500-$2,000/month", "$2,000-$10,000/month", ">$10,000/month"
```

### 1.2: Invoke Fleet Manager Agent for Architecture Design

Once you have the requirements, invoke the `fleet-manager` agent to design the architecture:

```
Use the Task tool with:
- subagent_type: "urbit-operations::fleet-manager"
- description: "Design fleet architecture"
- prompt: "Design a fleet architecture for a [FLEET_SIZE]-ship Urbit fleet on [PLATFORM] with a budget of [BUDGET]/month. Compliance requirements: [COMPLIANCE]. Geographic requirements: [REGION].

**Deliverables Required:**
1. Platform Justification (why this platform for this fleet size)
2. Network Topology Design (VPCs, subnets, load balancers, DNS)
3. Storage Strategy (persistent volumes, backups, S3/object storage)
4. Multi-Tenant Isolation Design (namespaces, network policies, RBAC)
5. Cost Estimate (detailed monthly breakdown)
6. Scaling Plan (6-month and 12-month projections)
7. Architecture Diagram (ASCII or mermaid format)

Provide Terraform pseudocode or actual Terraform modules if applicable."
```

### 1.3: Review and Approve Architecture

Present the architecture design to the user and get approval:

```
Summary of Architecture:
- Platform: [e.g., AWS EKS]
- Node Configuration: [e.g., 10× m5.2xlarge (8 vCPU, 32 GB RAM)]
- Total Capacity: [FLEET_SIZE] ships
- Monthly Cost: $[AMOUNT] ([$ per ship])
- Estimated Deployment Time: [WEEKS] weeks

Key Features:
- Multi-tenant isolation via Kubernetes namespaces
- Automated backups to S3 (90-day retention)
- 99.9% uptime SLA with multi-AZ redundancy
- TLS via Let's Encrypt (automated renewal)
- Prometheus + Grafana monitoring

Do you approve this architecture? (yes/no)
```

If the user requests changes, iterate with the fleet-manager agent.

---

## Phase 2: Infrastructure Provisioning (IaC)

**Objective:** Provision cloud infrastructure using Terraform (or equivalent IaC tool).

**Steps:**

### 2.1: Generate Terraform Code

If using Kubernetes (EKS/GKE/AKS), generate Terraform code for:
- VPC and networking (subnets, security groups, NAT gateways)
- Kubernetes cluster (EKS/GKE/AKS)
- Node groups (EC2/GCE/Azure VMs)
- S3/GCS/Azure Blob Storage for backups
- KMS encryption keys
- IAM roles and policies
- DNS (Route53/Cloud DNS/Azure DNS)

**Example Terraform structure:**

```
terraform/
├── main.tf           # Provider configuration
├── vpc.tf            # VPC, subnets, networking
├── eks.tf            # EKS cluster (or gke.tf, aks.tf)
├── storage.tf        # S3 buckets, KMS keys
├── iam.tf            # IAM roles for IRSA
├── dns.tf            # Route53 hosted zone
├── variables.tf      # Input variables
├── outputs.tf        # Outputs (cluster endpoint, S3 bucket)
└── terraform.tfvars  # Variable values
```

If using GroundSeg or Multi-VPS, generate:
- Ansible playbooks for VPS provisioning
- GroundSeg setup scripts
- Network configuration (Tailscale/Anchor VPN)

### 2.2: Invoke Cloud Architect or Terraform Specialist

Use the appropriate agent to generate production-ready IaC:

```
Use Task tool with:
- subagent_type: "cloud-infrastructure::terraform-specialist"
- description: "Generate Terraform for fleet infrastructure"
- prompt: "Generate production-ready Terraform code for the following Urbit fleet infrastructure:

**Specifications:**
- Platform: [AWS EKS / GCP GKE / Azure AKS]
- Region: [us-west-2 / eu-west-1 / etc.]
- Fleet Size: [NUMBER] ships
- Node Configuration: [INSTANCE_TYPE], [NODE_COUNT] nodes
- Storage: [STORAGE_SIZE] GB per ship, S3 backups
- Networking: VPC [CIDR], public/private subnets, ALB/NLB
- Security: KMS encryption, security groups, IAM roles
- Compliance: [GDPR / HIPAA / SOC2]

**Required Terraform Modules:**
1. VPC with public/private subnets (multi-AZ)
2. EKS/GKE/AKS cluster with managed node groups
3. S3/GCS bucket with versioning and encryption
4. KMS keys for EBS/S3 encryption
5. IAM roles for service accounts (IRSA)
6. Route53/Cloud DNS hosted zone

**Outputs Required:**
- Cluster endpoint and kubeconfig
- S3 bucket name
- VPC ID and subnet IDs

Ensure code follows best practices: remote state (S3 backend), encryption, least privilege IAM, tagging."
```

### 2.3: Review Terraform Code

Review the generated Terraform code with the user:

```
I've generated Terraform code for your fleet infrastructure. Key files:

1. **vpc.tf** - VPC with CIDR [CIDR], 3 AZs, public/private subnets
2. **eks.tf** - EKS cluster version [VERSION], [NODE_COUNT] nodes
3. **storage.tf** - S3 bucket with KMS encryption, 90-day lifecycle
4. **iam.tf** - IAM roles for pod service accounts (IRSA)
5. **dns.tf** - Route53 zone for [DOMAIN]

Estimated provisioning time: 20-30 minutes

Proceed with Terraform apply? (yes/no)
```

### 2.4: Execute Terraform Apply

Once approved, guide the user through Terraform deployment:

```bash
# Initialize Terraform
cd terraform/
terraform init

# Review plan
terraform plan -out=tfplan

# Apply infrastructure
terraform apply tfplan

# Save outputs
terraform output -json > ../outputs.json
```

**Note:** If you cannot execute commands directly, provide the user with a bash script they can run.

### 2.5: Verify Infrastructure Provisioning

After Terraform completes, verify infrastructure:

```bash
# Configure kubectl (for Kubernetes)
aws eks update-kubeconfig --region [REGION] --name [CLUSTER_NAME]

# Verify cluster
kubectl get nodes

# Verify S3 bucket
aws s3 ls s3://[BUCKET_NAME]

# Verify networking
kubectl get svc
```

---

## Phase 3: Multi-Tenant Isolation Setup

**Objective:** Configure security controls for multi-tenant isolation (namespaces, network policies, RBAC, resource quotas).

**Steps:**

### 3.1: Invoke Security Auditor for Multi-Tenant Design

Use the security-auditor agent to design isolation controls:

```
Use Task tool with:
- subagent_type: "full-stack-orchestration::security-auditor"
- description: "Design multi-tenant isolation"
- prompt: "Design multi-tenant security controls for a [FLEET_SIZE]-ship Urbit fleet on Kubernetes. Each customer should have isolated ships with no cross-tenant access.

**Requirements:**
1. Namespace-based isolation (one namespace per customer)
2. NetworkPolicies to prevent cross-namespace traffic
3. ResourceQuotas to prevent resource exhaustion
4. RBAC roles for customer admins (read-only access to their namespace)
5. PodSecurityStandards (baseline or restricted profile)

**Deliverables:**
1. Namespace template YAML
2. Default-deny NetworkPolicy YAML
3. ResourceQuota template YAML (10 ships, 20 GB RAM, 10 CPU per customer)
4. RBAC Role and RoleBinding templates
5. PodSecurityPolicy or PodSecurityStandard configuration

Ensure compliance with [COMPLIANCE_REQUIREMENTS] (e.g., HIPAA, GDPR)."
```

### 3.2: Deploy Multi-Tenant Resources

Apply the generated Kubernetes resources:

```bash
# Create namespace template
kubectl apply -f k8s/namespace-template.yaml

# Apply default-deny NetworkPolicy to all namespaces
kubectl apply -f k8s/network-policy-default-deny.yaml

# Create ResourceQuota template
kubectl apply -f k8s/resource-quota-template.yaml

# Set up RBAC
kubectl apply -f k8s/rbac-customer-admin.yaml

# Enable PodSecurityStandards
kubectl label namespace --all pod-security.kubernetes.io/enforce=baseline
```

### 3.3: Test Isolation

Verify multi-tenant isolation works:

```bash
# Test 1: Cross-namespace traffic blocking
kubectl run test-pod -n customer-a --image=nicolaka/netshoot --rm -it -- curl http://ship-service.customer-b.svc.cluster.local
# Expected: Connection timeout (NetworkPolicy blocking)

# Test 2: Resource quota enforcement
kubectl create deployment test -n customer-a --image=nginx --replicas=50
# Expected: Quota exceeded error (ResourceQuota limiting)

# Test 3: RBAC permissions
kubectl auth can-i get pods -n customer-b --as=customer-a-admin
# Expected: no (RBAC denying cross-namespace access)
```

---

## Phase 4: Monitoring Infrastructure Deployment

**Objective:** Deploy Prometheus, Grafana, Loki, and AlertManager for fleet observability.

**Steps:**

### 4.1: Deploy kube-prometheus-stack via Helm

```bash
# Add Prometheus Helm repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Install kube-prometheus-stack
helm install kube-prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --set prometheus.prometheusSpec.retention=7d \
  --set prometheus.prometheusSpec.storageSpec.volumeClaimTemplate.spec.resources.requests.storage=50Gi \
  --set grafana.adminPassword=[SECURE_PASSWORD]

# Wait for deployment
kubectl wait --for=condition=ready pod -l app.kubernetes.io/name=prometheus -n monitoring --timeout=300s
```

### 4.2: Configure Prometheus Scrape Configs

Add scrape configs for Urbit ships:

```yaml
# prometheus-scrape-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-additional-scrape-configs
  namespace: monitoring
data:
  scrape-configs.yaml: |
    - job_name: 'urbit-ships'
      kubernetes_sd_configs:
        - role: pod
          namespaces:
            names:
              - urbit-fleet
      relabel_configs:
        - source_labels: [__meta_kubernetes_pod_label_app]
          regex: urbit-ship
          action: keep
        - source_labels: [__meta_kubernetes_pod_label_ship]
          target_label: ship
        - source_labels: [__meta_kubernetes_pod_ip]
          target_label: __address__
          replacement: $1:9090  # Prometheus exporter port
```

### 4.3: Invoke Observability Engineer for Dashboards

```
Use Task tool with:
- subagent_type: "observability-monitoring::observability-engineer"
- description: "Create Grafana dashboards"
- prompt: "Create Grafana dashboards for monitoring a [FLEET_SIZE]-ship Urbit fleet on Kubernetes.

**Required Dashboards:**
1. Fleet Overview Dashboard:
   - Total ships, ships online/offline
   - Fleet uptime percentage (99.9% SLA target)
   - Total pier size (GB)
   - CPU/memory usage across fleet
   - Top 10 ships by resource usage

2. Per-Ship Metrics Dashboard:
   - Individual ship CPU/memory usage
   - Pier size growth over time
   - Restart count
   - Ames packet rate
   - HTTP request latency

3. Cost Tracking Dashboard:
   - EC2/GCE instance costs
   - Storage costs (EBS/persistent disks)
   - Data transfer costs
   - Total monthly cost

4. Alerts Dashboard:
   - Active alerts
   - Alert history
   - Mean time to resolve (MTTR)

Provide JSON dashboard definitions compatible with Grafana 10.x."
```

### 4.4: Deploy Grafana Dashboards

Import the generated dashboards:

```bash
# Import dashboards via ConfigMap
kubectl create configmap grafana-dashboards \
  --from-file=fleet-overview.json \
  --from-file=per-ship-metrics.json \
  --from-file=cost-tracking.json \
  --from-file=alerts.json \
  -n monitoring

# Label for Grafana auto-discovery
kubectl label configmap grafana-dashboards grafana_dashboard=1 -n monitoring
```

### 4.5: Configure Alerts

Deploy Prometheus alerts:

```yaml
# prometheus-alerts.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-urbit-alerts
  namespace: monitoring
data:
  urbit-alerts.yaml: |
    groups:
      - name: urbit_fleet
        rules:
          - alert: UrbitShipOffline
            expr: up{job="urbit-ships"} == 0
            for: 5m
            labels:
              severity: critical
            annotations:
              summary: "Urbit ship {{ $labels.ship }} offline"

          - alert: UrbitFleetUptimeBelowSLA
            expr: (sum(up{job="urbit-ships"}) / count(up{job="urbit-ships"})) * 100 < 99.9
            for: 10m
            labels:
              severity: critical
            annotations:
              summary: "Fleet uptime {{ $value }}% below 99.9% SLA"

          - alert: UrbitShipHighMemory
            expr: container_memory_working_set_bytes{pod=~".*-urbit-.*"} > 3.5 * 1024 * 1024 * 1024
            for: 15m
            labels:
              severity: warning
            annotations:
              summary: "Ship {{ $labels.pod }} high memory usage"
```

### 4.6: Integrate PagerDuty (Optional)

If the user requires on-call alerting:

```yaml
# alertmanager-config.yaml
apiVersion: v1
kind: Secret
metadata:
  name: alertmanager-config
  namespace: monitoring
stringData:
  alertmanager.yaml: |
    global:
      resolve_timeout: 5m

    route:
      receiver: 'pagerduty'
      routes:
        - match:
            severity: critical
          receiver: 'pagerduty'

    receivers:
      - name: 'pagerduty'
        pagerduty_configs:
          - service_key: '[PAGERDUTY_INTEGRATION_KEY]'
            description: '{{ range .Alerts }}{{ .Annotations.summary }}{{ end }}'
```

---

## Phase 5: Pilot Ship Deployment (Validation)

**Objective:** Deploy 5-10 pilot ships to validate infrastructure before full fleet deployment.

**Steps:**

### 5.1: Prepare Pilot Ship List

Create a list of pilot ships:

```
pilot-ships.txt:
~sampel-palnet
~timluc-miptev
~ritmur-lipsyg
~master-morzod
~norsyr-torryn
~rovnys-ricfer
~wicdev-wisryt
~fabled-faster
~haddef-sigwen
~rabsef-bicrym
```

### 5.2: Deploy Helm Chart for Urbit Ships

Use the Helm chart generated by fleet-manager (or create one):

```bash
# Deploy pilot ships
for SHIP in $(cat pilot-ships.txt); do
  echo "Deploying $SHIP..."

  helm install $SHIP ./helm/urbit-ship \
    --namespace urbit-fleet \
    --create-namespace \
    --set ship.name=$SHIP \
    --set ship.initMethod=fresh \
    --set backup.s3Bucket=[S3_BUCKET] \
    --set networking.tls.domain=${SHIP}.fleet.example.com \
    --set networking.tls.issuer=letsencrypt-prod \
    --wait \
    --timeout 10m

  echo "✓ $SHIP deployed"
done
```

### 5.3: Validate Pilot Deployment

Run validation checks:

```bash
# Check pod status
kubectl get pods -n urbit-fleet

# Check TLS certificates
kubectl get certificates -n urbit-fleet

# Check Ingress
kubectl get ingress -n urbit-fleet

# Test ship accessibility
for SHIP in $(cat pilot-ships.txt); do
  echo "Testing $SHIP..."
  curl -I https://${SHIP}.fleet.example.com
done

# Check Prometheus metrics
kubectl port-forward -n monitoring svc/kube-prometheus-prometheus 9090:9090 &
curl http://localhost:9090/api/v1/query?query=up{job=\"urbit-ships\"}
```

### 5.4: Performance Testing

Conduct basic performance tests:

```bash
# Load testing (simple HTTP requests)
for SHIP in $(cat pilot-ships.txt); do
  echo "Load testing $SHIP..."
  ab -n 1000 -c 10 https://${SHIP}.fleet.example.com/
done

# Monitor resource usage during load test
kubectl top pods -n urbit-fleet
```

### 5.5: Pilot Review and Go/No-Go Decision

Present pilot results to user:

```
Pilot Deployment Results:
- Ships Deployed: 10/10
- Ships Online: 10/10 (100%)
- Average Boot Time: 3 minutes
- TLS Certificates: 10/10 valid
- Average Memory Usage: 2.8 GB per ship
- Average CPU Usage: 0.6 cores per ship
- Performance: 50ms p99 latency (HTTP)

Issues Found: [None / List any issues]

Recommendation: [PROCEED / FIX_ISSUES_FIRST]

Proceed to full fleet deployment? (yes/no)
```

---

## Phase 6: Full Fleet Bulk Deployment

**Objective:** Deploy the full fleet of [FLEET_SIZE] ships with automated orchestration.

**Steps:**

### 6.1: Generate Full Ship List

Create the complete ship list:

```
# If user provides ship list
full-fleet-ships.txt (200 ships)

# If generating comets automatically
for i in {1..200}; do
  echo "~comet-${i}" >> full-fleet-ships.txt
done
```

### 6.2: Batch Deployment Strategy

Deploy ships in batches to avoid overloading cluster:

```python
#!/usr/bin/env python3
# batch-deploy.py - Deploy ships in controlled batches

import subprocess
import time

BATCH_SIZE = 50  # Deploy 50 ships per batch
WAIT_TIME = 300  # Wait 5 minutes between batches

with open('full-fleet-ships.txt') as f:
    ships = [line.strip() for line in f.readlines()]

total_ships = len(ships)
batches = [ships[i:i+BATCH_SIZE] for i in range(0, total_ships, BATCH_SIZE)]

print(f"Deploying {total_ships} ships in {len(batches)} batches")

for batch_num, batch in enumerate(batches, 1):
    print(f"\n[Batch {batch_num}/{len(batches)}] Deploying {len(batch)} ships...")

    for ship in batch:
        cmd = [
            "helm", "install", ship, "./helm/urbit-ship",
            "--namespace", "urbit-fleet",
            "--set", f"ship.name={ship}",
            "--set", "ship.initMethod=fresh",
            "--set", "backup.s3Bucket=[S3_BUCKET]",
            "--set", f"networking.tls.domain={ship}.fleet.example.com",
            "--wait",
            "--timeout", "10m"
        ]
        subprocess.run(cmd, check=True)

    print(f"✓ Batch {batch_num} complete")

    if batch_num < len(batches):
        print(f"Waiting {WAIT_TIME}s before next batch...")
        time.sleep(WAIT_TIME)

print("\n✓ Full fleet deployment complete")
```

### 6.3: Monitor Deployment Progress

Watch deployment in real-time:

```bash
# Watch pod status
watch kubectl get pods -n urbit-fleet

# Monitor cluster resources
kubectl top nodes

# Check Grafana dashboard
# Open Grafana UI and monitor "Fleet Overview" dashboard
```

### 6.4: Handle Deployment Failures

If any ships fail to deploy:

```bash
# List failed deployments
helm list -n urbit-fleet --failed

# Check pod logs
kubectl logs -n urbit-fleet [FAILED_POD]

# Delete and retry
helm delete [FAILED_SHIP] -n urbit-fleet
helm install [FAILED_SHIP] ./helm/urbit-ship [...]
```

---

## Phase 7: Fleet Validation & Handoff

**Objective:** Validate full fleet health and hand off to operations team.

**Steps:**

### 7.1: Fleet Health Check

Run comprehensive health checks:

```bash
# Check all pods running
kubectl get pods -n urbit-fleet | grep -v Running | wc -l
# Expected: 0 (all pods running)

# Check TLS certificates
kubectl get certificates -n urbit-fleet | grep -v True | wc -l
# Expected: 0 (all certs ready)

# Check Prometheus metrics
curl -s http://prometheus:9090/api/v1/query?query=up{job=\"urbit-ships\"} | jq '.data.result | length'
# Expected: [FLEET_SIZE] (all ships reporting metrics)

# Check fleet uptime
curl -s http://prometheus:9090/api/v1/query?query=urbit:fleet:uptime:percentage | jq '.data.result[0].value[1]'
# Expected: >99.9 (above SLA)
```

### 7.2: Generate Fleet Report

Create a comprehensive fleet deployment report:

```markdown
# Fleet Deployment Report

**Deployment Date:** [DATE]
**Fleet Size:** [NUMBER] ships
**Platform:** [EKS/GKE/AKS/GroundSeg]
**Region:** [REGION]

## Summary

- Total Ships Deployed: [NUMBER]
- Ships Online: [NUMBER] ([PERCENTAGE]%)
- Deployment Duration: [HOURS] hours
- Deployment Success Rate: [PERCENTAGE]%

## Infrastructure

- Cluster: [CLUSTER_NAME]
- Nodes: [NODE_COUNT]× [INSTANCE_TYPE]
- Total CPU: [CORES] cores
- Total Memory: [GB] GB
- Total Storage: [TB] TB

## Monitoring

- Prometheus: ✓ Operational
- Grafana: ✓ Operational ([URL])
- Alerts: ✓ Configured
- Uptime: [PERCENTAGE]% (target: 99.9%)

## Cost

- Monthly Infrastructure Cost: $[AMOUNT]
- Cost per Ship: $[AMOUNT]
- Within Budget: [YES/NO]

## Compliance

- Multi-Tenant Isolation: ✓ Implemented
- Encryption at Rest: ✓ Enabled (KMS)
- Encryption in Transit: ✓ Enabled (TLS 1.2+)
- Audit Logging: ✓ Enabled
- Backup: ✓ Daily backups to S3

## Issues and Resolutions

[List any issues encountered and how they were resolved]

## Next Steps

1. Train operations team on fleet management
2. Establish on-call rotation (PagerDuty)
3. Schedule 30-day review
4. Plan for scaling to [NEXT_SIZE] ships

**Deployment Status:** ✓ COMPLETE
```

### 7.3: Operations Handoff

Provide operations team with:

1. **Access Credentials:**
   - Kubeconfig for Kubernetes cluster
   - AWS/GCP/Azure console access
   - Grafana admin credentials
   - PagerDuty access

2. **Documentation:**
   - Architecture diagram
   - Runbooks for common operations (ship restart, OTA updates, scaling)
   - Incident response procedures
   - Escalation contacts

3. **Training Materials:**
   - Fleet management guide
   - Monitoring and alerting overview
   - Terraform infrastructure guide
   - Backup and disaster recovery procedures

### 7.4: Schedule Follow-Up Reviews

Set up regular review cadence:

```
Review Schedule:
- 7-day check-in: Quick health check, address any immediate issues
- 30-day review: Performance analysis, cost optimization, lessons learned
- 60-day review: Capacity planning, scaling roadmap
- 90-day review: Compliance audit, security review, full fleet assessment
```

---

## Success Criteria

The fleet deployment is considered successful when:

- ✅ All [FLEET_SIZE] ships deployed and online
- ✅ Fleet uptime ≥ 99.9% over 7 days
- ✅ TLS certificates valid for all ships
- ✅ Monitoring dashboards operational
- ✅ Alerts configured and tested
- ✅ Backups running (verified restore test)
- ✅ Multi-tenant isolation verified
- ✅ Cost within budget (±10%)
- ✅ No critical security vulnerabilities
- ✅ Operations team trained and on-call rotation active

---

## Rollback Procedures

If the deployment fails catastrophically:

### Rollback Phase 6 (Full Fleet Deployment)

```bash
# Delete all deployed ships
helm list -n urbit-fleet -o json | jq -r '.[].name' | xargs -I {} helm delete {} -n urbit-fleet

# Verify all pods deleted
kubectl get pods -n urbit-fleet
```

### Rollback Phase 4 (Monitoring)

```bash
# Delete monitoring stack
helm delete kube-prometheus -n monitoring
kubectl delete namespace monitoring
```

### Rollback Phase 3 (Multi-Tenant Isolation)

```bash
# Remove network policies
kubectl delete networkpolicy --all -n urbit-fleet

# Remove resource quotas
kubectl delete resourcequota --all -n urbit-fleet
```

### Rollback Phase 2 (Infrastructure)

```bash
# Destroy Terraform infrastructure
cd terraform/
terraform destroy -auto-approve
```

**WARNING:** This will delete ALL infrastructure and data. Only use in emergency situations.

---

## Related Agents

**Primary Agent:**
- `fleet-manager` - Elite fleet operations specialist (invoked multiple times throughout workflow)

**Supporting Agents:**
- `cloud-infrastructure::terraform-specialist` - Generate IaC for infrastructure provisioning
- `full-stack-orchestration::security-auditor` - Design multi-tenant security controls
- `observability-monitoring::observability-engineer` - Create monitoring dashboards and alerts
- `cloud-infrastructure::kubernetes-architect` - Advanced Kubernetes architecture patterns
- `full-stack-orchestration::deployment-engineer` - CI/CD and deployment automation

---

## Related Skills

**Phase 3: Fleet & Compliance**
- `advanced-security-patterns` - Multi-tenant isolation, encryption, network segmentation
- `compliance-frameworks` - GDPR, HIPAA, SOC2 compliance implementation

**Phase 2: Platform Operations**
- `performance-profiling` - Performance optimization for fleet ships
- `backup-strategies` - Backup and disaster recovery implementation

**Phase 1: Core Urbit Operations**
- `deployment-basics` - Individual ship deployment fundamentals
- `networking-fundamentals` - DNS, TLS, load balancer configuration

---

## Best Practices

1. **Always Start with Pilot:** Deploy 5-10 ships first to validate architecture
2. **Batch Deployments:** Deploy in batches of 50-100 ships to avoid cluster overload
3. **Monitor Continuously:** Watch Grafana dashboards during deployment
4. **Test Backups Early:** Verify S3 backups working before full fleet deployment
5. **Document Everything:** Keep detailed notes for operations team handoff
6. **Plan for Failure:** Have rollback procedures ready before starting
7. **Involve Stakeholders:** Get architecture approval before provisioning infrastructure
8. **Budget Buffer:** Plan for 10-20% cost overruns in first month
9. **Security First:** Implement multi-tenant isolation from day 1
10. **Automate Ruthlessly:** Use IaC for everything (Terraform, Helm, Ansible)

---

## Common Pitfalls

1. **Skipping Pilot Phase:** Full fleet failures are expensive; always validate with pilot
2. **Underprovisioning Nodes:** Ships can use more memory than expected; plan for 4 GB average
3. **No Monitoring:** Can't troubleshoot what you can't see; deploy monitoring first
4. **Ignoring Compliance:** Implementing GDPR/HIPAA controls after deployment is hard; design in from start
5. **Manual Configuration:** Doesn't scale; use Helm charts and automation
6. **Single Points of Failure:** No multi-AZ redundancy = downtime during AZ outages
7. **No Backup Testing:** Backups are useless if you can't restore; test before going live
8. **Insufficient Documentation:** Operations team can't manage what they don't understand
9. **Tight Timelines:** Fleet deployments take 2-4 weeks; rushing leads to mistakes
10. **No Rollback Plan:** Always have a way to undo changes

---

## Execution Instructions

When this command is invoked:

1. **Collect Requirements:** Use AskUserQuestion if information is missing
2. **Execute Phases Sequentially:** Follow phases 1-7 in order
3. **Invoke Agents as Needed:** Use Task tool to call fleet-manager and other specialized agents
4. **Validate Each Phase:** Don't proceed to next phase until current phase is validated
5. **Document Progress:** Keep user informed of progress and decisions
6. **Handle Failures Gracefully:** If a phase fails, offer to retry or rollback
7. **Get Approval:** Ask for user approval before expensive operations (Terraform apply, full fleet deployment)
8. **Provide Clear Outputs:** Show exact commands, scripts, and configurations
9. **Estimate Timelines:** Give realistic estimates for each phase
10. **Celebrate Success:** Acknowledge completion of major milestones

---

**Ready to deploy your Urbit fleet at scale. Let's begin with Phase 1: Fleet Planning & Architecture Design.**
