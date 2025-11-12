---
name: compliance-frameworks
description: Master GDPR, HIPAA, SOC2, and ISO 27001 compliance for enterprise Urbit deployments with automated evidence collection and audit-ready documentation.
---

# Compliance Frameworks for Urbit Deployments

## Overview

This skill provides comprehensive compliance patterns, technical implementations, and documentation templates for running enterprise Urbit deployments that meet regulatory requirements including GDPR, HIPAA, SOC2, and ISO 27001.

**Use this skill when:**
- Deploying Urbit infrastructure for regulated industries (healthcare, finance, government)
- Preparing for compliance audits (SOC2 Type II, ISO 27001 certification)
- Implementing data protection requirements (GDPR, CCPA)
- Managing PHI (Protected Health Information) on Urbit
- Building audit trails and evidence collection systems
- Implementing security controls for compliance frameworks
- Preparing for security questionnaires and vendor assessments

**Key Capabilities:**
- GDPR compliance patterns (data residency, right to erasure, consent management)
- HIPAA compliance implementations (encryption, audit logging, BAA requirements)
- SOC2 Type II trust services criteria mapping and control implementation
- ISO 27001 ISMS (Information Security Management System) for Urbit fleets
- Automated evidence collection and audit trail generation
- Compliance-as-Code with policy enforcement automation
- Privacy-enhancing technologies (data minimization, pseudonymization)
- Breach notification and incident response procedures

---

## GDPR Compliance for Urbit Deployments

### Overview

The General Data Protection Regulation (GDPR) applies to any organization processing EU residents' personal data. Urbit deployments handling European user data must implement technical and organizational measures to ensure compliance.

### Data Protection Principles

**1. Lawfulness, Fairness, and Transparency**

```yaml
# Consent Management Configuration
# Store user consent records in Urbit %graph-store or custom agent

Consent Record Structure:
  User: ~sampel-palnet
  Purpose: "Marketing communications"
  Legal Basis: "Consent (Article 6(1)(a))"
  Timestamp: ~2024.12.15..10.30.00
  Consent Method: "Double opt-in via web form"
  IP Address: 192.168.1.100 (pseudonymized)
  Withdrawal Mechanism: "Send |consent-withdraw %marketing to ship"
```

**Implementation:**
```hoon
:: Consent tracking agent pseudocode
::
+$  consent-record
  $:  user=@p
      purpose=@t
      legal-basis=@t
      timestamp=@da
      method=@t
      ip-hash=@ux  :: Pseudonymized IP
      active=?
  ==

++  withdraw-consent
  |=  [user=@p purpose=@t]
  ^-  (quip card _state)
  =/  record  (~(get by consents.state) [user purpose])
  ?~  record
    ~&  "No consent record found"
    [~ state]
  =.  active.u.record  %.n
  =.  consents.state  (~(put by consents.state) [user purpose] u.record)
  ~&  "Consent withdrawn for {(trip purpose)}"
  [~ state]
```

**2. Purpose Limitation**

Document and enforce specific purposes for data processing:

```markdown
# Data Processing Purposes Registry

| Data Category | Purpose | Legal Basis | Retention Period |
|---------------|---------|-------------|------------------|
| Ship Identity (~sampel-palnet) | Service delivery | Contract | Account lifetime |
| DM Messages | Communication service | Contract | User-controlled (infinite) |
| S3 Backup Data | Disaster recovery | Legitimate interest | 90 days |
| Access Logs | Security monitoring | Legitimate interest | 30 days |
| Billing Information | Payment processing | Contract | 7 years (tax law) |
```

**3. Data Minimization**

Collect only necessary data:

```bash
# Urbit Deployment with Minimal Logging
# Disable unnecessary telemetry and logging

# In ship's .run file (GroundSeg) or Kubernetes ConfigMap
export URBIT_TELEMETRY=false
export LOG_LEVEL=error  # Only log errors, not debug/info
export DISABLE_IP_LOGGING=true  # Pseudonymize IP addresses
```

**4. Accuracy**

Provide users mechanisms to correct their data:

```hoon
:: User data correction agent
++  update-profile
  |=  [user=@p updates=(map @t @t)]
  ^-  (quip card _state)
  =/  profile  (~(get by profiles.state) user)
  ?~  profile
    [~ state]
  =/  new-profile
    %-  ~(gas by u.profile)
    ~(tap by updates)
  =.  profiles.state  (~(put by profiles.state) user new-profile)
  :_  state
  :~  [%give %fact ~[/updates] %profile-update !>(new-profile)]
  ==
```

**5. Storage Limitation**

Implement automated data retention and deletion:

```python
# Automated GDPR Data Retention Script
# Delete S3 backups older than retention period

import boto3
from datetime import datetime, timedelta

def delete_expired_backups(bucket_name, retention_days=90):
    """Delete S3 backups older than retention period."""
    s3 = boto3.client('s3')
    cutoff_date = datetime.now() - timedelta(days=retention_days)

    response = s3.list_objects_v2(Bucket=bucket_name, Prefix='urbit-backups/')

    deleted_count = 0
    for obj in response.get('Contents', []):
        if obj['LastModified'].replace(tzinfo=None) < cutoff_date:
            print(f"Deleting {obj['Key']} (age: {(datetime.now() - obj['LastModified'].replace(tzinfo=None)).days} days)")
            s3.delete_object(Bucket=bucket_name, Key=obj['Key'])
            deleted_count += 1

    print(f"Deleted {deleted_count} expired backups")

    # Log deletion for audit trail
    with open('/var/log/gdpr-deletions.log', 'a') as f:
        f.write(f"{datetime.now().isoformat()} - Deleted {deleted_count} backups older than {retention_days} days\n")

if __name__ == '__main__':
    delete_expired_backups('my-urbit-backups', retention_days=90)
```

**6. Integrity and Confidentiality**

Implement encryption at rest and in transit (see advanced-security-patterns skill).

### GDPR Data Subject Rights Implementation

**Right to Access (Article 15)**

```bash
#!/bin/bash
# GDPR Data Subject Access Request (DSAR) Script
# Export all data for a specific ship

SHIP=$1  # e.g., sampel-palnet
OUTPUT_DIR="/tmp/dsar-${SHIP}-$(date +%Y%m%d)"

mkdir -p "$OUTPUT_DIR"

echo "Generating GDPR Data Subject Access Request for $SHIP..."

# 1. Export ship's pier data
echo "Exporting ship data..."
tar -czf "$OUTPUT_DIR/ship-data.tar.gz" "/urbit/${SHIP}/.urb/put" "/urbit/${SHIP}/.urb/log"

# 2. Export S3 backups
echo "Exporting S3 backups..."
aws s3 sync "s3://my-urbit-backups/${SHIP}/" "$OUTPUT_DIR/s3-backups/"

# 3. Export access logs (pseudonymized)
echo "Exporting access logs..."
grep "$SHIP" /var/log/nginx/access.log > "$OUTPUT_DIR/access-logs.txt"

# 4. Generate data inventory
cat > "$OUTPUT_DIR/data-inventory.txt" <<EOF
GDPR Data Subject Access Request
Ship: $SHIP
Generated: $(date -u +"%Y-%m-%d %H:%M:%S UTC")

Data Categories Included:
1. Ship Identity and Configuration
2. User-Generated Content (DMs, Groups, Notebooks)
3. S3 Backup Data
4. Access Logs (last 30 days, IP addresses pseudonymized)
5. Billing Information (if applicable)

Data NOT Included:
- Aggregated/Anonymous Analytics (non-identifiable)
- Data Deleted Beyond Retention Period

Your Rights:
- Right to Rectification (update inaccurate data)
- Right to Erasure (delete your data)
- Right to Data Portability (receive in machine-readable format)
- Right to Object (opt-out of processing)

To exercise these rights, contact: privacy@example.com
EOF

# 5. Create encrypted archive
echo "Creating encrypted archive..."
zip -r -e "$OUTPUT_DIR.zip" "$OUTPUT_DIR"
echo "DSAR package created: $OUTPUT_DIR.zip (password required)"
```

**Right to Erasure / "Right to be Forgotten" (Article 17)**

```python
#!/usr/bin/env python3
# GDPR Right to Erasure Implementation
# Permanently delete all data for a ship

import os
import shutil
import boto3
import subprocess
from datetime import datetime

def erase_ship_data(ship_name):
    """
    Implement GDPR Right to Erasure.

    WARNING: This is IRREVERSIBLE. Verify legal grounds before executing.
    """

    print(f"=== GDPR RIGHT TO ERASURE: {ship_name} ===")
    print(f"Initiated: {datetime.utcnow().isoformat()}Z")

    erasure_log = []

    # 1. Stop the ship
    print("\n[1/7] Stopping ship...")
    try:
        subprocess.run(['docker', 'stop', f'urbit-{ship_name}'], check=True)
        erasure_log.append(f"Ship stopped: {ship_name}")
    except subprocess.CalledProcessError as e:
        print(f"Warning: Could not stop ship: {e}")

    # 2. Delete pier directory
    print("\n[2/7] Deleting pier directory...")
    pier_path = f"/urbit/{ship_name}"
    if os.path.exists(pier_path):
        shutil.rmtree(pier_path)
        erasure_log.append(f"Pier deleted: {pier_path}")
        print(f"Deleted: {pier_path}")

    # 3. Delete S3 backups
    print("\n[3/7] Deleting S3 backups...")
    s3 = boto3.client('s3')
    bucket = 'my-urbit-backups'
    prefix = f'{ship_name}/'

    response = s3.list_objects_v2(Bucket=bucket, Prefix=prefix)
    for obj in response.get('Contents', []):
        s3.delete_object(Bucket=bucket, Key=obj['Key'])
        erasure_log.append(f"S3 object deleted: {obj['Key']}")
    print(f"Deleted S3 backups: s3://{bucket}/{prefix}")

    # 4. Delete Docker volumes
    print("\n[4/7] Deleting Docker volumes...")
    try:
        subprocess.run(['docker', 'volume', 'rm', f'urbit-{ship_name}-data'], check=True)
        erasure_log.append(f"Docker volume deleted: urbit-{ship_name}-data")
    except subprocess.CalledProcessError:
        print("No Docker volume found")

    # 5. Purge access logs
    print("\n[5/7] Purging access logs...")
    subprocess.run(['sed', '-i', f'/{ship_name}/d', '/var/log/nginx/access.log'])
    erasure_log.append(f"Access logs purged for {ship_name}")

    # 6. Remove from databases
    print("\n[6/7] Removing from application databases...")
    # Example: Remove from billing database
    # db.execute("DELETE FROM subscriptions WHERE ship = ?", (ship_name,))
    erasure_log.append(f"Database records deleted for {ship_name}")

    # 7. Generate erasure certificate
    print("\n[7/7] Generating erasure certificate...")
    cert_path = f"/var/log/gdpr-erasures/{ship_name}-{datetime.utcnow().strftime('%Y%m%d')}.txt"
    os.makedirs(os.path.dirname(cert_path), exist_ok=True)

    with open(cert_path, 'w') as f:
        f.write(f"GDPR RIGHT TO ERASURE CERTIFICATE\n")
        f.write(f"="*50 + "\n")
        f.write(f"Ship: {ship_name}\n")
        f.write(f"Erasure Date: {datetime.utcnow().isoformat()}Z\n")
        f.write(f"Executed By: GDPR Compliance System\n\n")
        f.write(f"Actions Performed:\n")
        for action in erasure_log:
            f.write(f"  - {action}\n")
        f.write(f"\nThis certificate confirms that all personal data for {ship_name} ")
        f.write(f"has been permanently and irreversibly deleted in accordance with ")
        f.write(f"GDPR Article 17 (Right to Erasure).\n")

    print(f"\nErasure certificate: {cert_path}")
    print(f"\n✓ ERASURE COMPLETE for {ship_name}")

if __name__ == '__main__':
    import sys
    if len(sys.argv) != 2:
        print("Usage: ./gdpr-erase.py <ship-name>")
        sys.exit(1)

    ship = sys.argv[1]

    # Confirmation prompt
    confirm = input(f"WARNING: This will PERMANENTLY DELETE ALL DATA for {ship}. Type '{ship}' to confirm: ")
    if confirm != ship:
        print("Aborted.")
        sys.exit(1)

    erase_ship_data(ship)
```

**Right to Data Portability (Article 20)**

```bash
#!/bin/bash
# GDPR Data Portability - Export ship data in machine-readable format

SHIP=$1
OUTPUT_JSON="/tmp/${SHIP}-export-$(date +%Y%m%d).json"

echo "Exporting $SHIP data in JSON format..."

# Use Urbit's |export command (if available) or custom agent
# Example structure:

cat > "$OUTPUT_JSON" <<EOF
{
  "ship": "$SHIP",
  "export_date": "$(date -u +"%Y-%m-%dT%H:%M:%SZ")",
  "format_version": "1.0",
  "data": {
    "profile": {
      "nickname": "Sample",
      "avatar": "https://example.com/avatar.png",
      "bio": "Urbit user"
    },
    "groups": [
      {
        "name": "~bitbet-bolbel/urbit-community",
        "role": "member",
        "joined": "2024-01-15T10:30:00Z"
      }
    ],
    "messages": [
      {
        "timestamp": "2024-12-01T15:45:00Z",
        "from": "$SHIP",
        "to": "~zod",
        "content": "Hello, Urbit!"
      }
    ],
    "notebooks": [],
    "settings": {
      "timezone": "UTC",
      "theme": "dark"
    }
  }
}
EOF

echo "Export complete: $OUTPUT_JSON"
echo "This JSON file can be imported into another Urbit ship or compatible system."
```

### Data Residency and Localization

**EU Data Residency Implementation:**

```hcl
# Terraform: AWS EKS Cluster in EU Region (GDPR Compliance)

provider "aws" {
  region = "eu-west-1"  # Dublin, Ireland (EU)
}

resource "aws_eks_cluster" "urbit_eu" {
  name     = "urbit-fleet-eu"
  role_arn = aws_iam_role.eks_cluster.arn
  version  = "1.28"

  vpc_config {
    subnet_ids = aws_subnet.eu_private[*].id
    endpoint_private_access = true
    endpoint_public_access  = false  # No public access
  }

  tags = {
    Compliance = "GDPR"
    DataResidency = "EU"
    Environment = "production"
  }
}

# S3 Bucket with EU-only replication
resource "aws_s3_bucket" "urbit_backups_eu" {
  bucket = "urbit-backups-eu"

  tags = {
    Compliance = "GDPR"
    DataResidency = "EU"
  }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "urbit_backups_eu" {
  bucket = aws_s3_bucket.urbit_backups_eu.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm     = "aws:kms"
      kms_master_key_id = aws_kms_key.eu_data.id
    }
  }
}

# KMS Key in EU region only
resource "aws_kms_key" "eu_data" {
  description             = "KMS key for GDPR-compliant EU data"
  deletion_window_in_days = 30
  enable_key_rotation     = true
  multi_region            = false  # Single-region key (EU only)

  tags = {
    Compliance = "GDPR"
    Region = "EU"
  }
}

# Deny non-EU access via bucket policy
resource "aws_s3_bucket_policy" "eu_only" {
  bucket = aws_s3_bucket.urbit_backups_eu.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "DenyNonEUAccess"
        Effect    = "Deny"
        Principal = "*"
        Action    = "s3:*"
        Resource  = [
          aws_s3_bucket.urbit_backups_eu.arn,
          "${aws_s3_bucket.urbit_backups_eu.arn}/*"
        ]
        Condition = {
          StringNotEquals = {
            "aws:RequestedRegion" = "eu-west-1"
          }
        }
      }
    ]
  })
}
```

### GDPR Breach Notification

**72-Hour Breach Notification Procedure:**

```markdown
# GDPR Data Breach Response Plan

## Phase 1: Detection and Containment (0-4 hours)

1. **Incident Detection**
   - Automated alerts via monitoring (Prometheus, CloudWatch)
   - User reports
   - Third-party notifications

2. **Immediate Actions**
   - Isolate affected systems (quarantine pods/VMs)
   - Preserve evidence (logs, snapshots)
   - Activate incident response team

3. **Initial Assessment**
   - Number of affected data subjects
   - Type of data exposed (personal, sensitive, PHI)
   - Likelihood of harm to individuals

## Phase 2: Investigation (4-24 hours)

1. **Root Cause Analysis**
   - Review access logs
   - Analyze attack vectors
   - Identify vulnerabilities exploited

2. **Scope Determination**
   - List of affected ships (~sampel-palnet, ~zod, ...)
   - Data categories compromised
   - Geographic distribution (EU vs non-EU)

3. **Remediation**
   - Patch vulnerabilities
   - Reset compromised credentials
   - Restore from clean backups

## Phase 3: Notification (24-72 hours)

1. **Supervisory Authority Notification** (within 72 hours of detection)
   - Submit to relevant EU DPA (e.g., ICO, CNIL, BfDI)
   - Include: nature of breach, categories/number of data subjects, likely consequences, mitigation measures

2. **Data Subject Notification** (without undue delay if high risk)
   - Email to affected users
   - Describe breach in clear language
   - Provide guidance on protective measures

3. **Documentation**
   - Maintain breach register (GDPR Article 33(5))
   - Record: facts, effects, remedial actions

## Phase 4: Post-Incident (72+ hours)

1. **Lessons Learned**
   - Update security controls
   - Conduct security training
   - Review and update incident response plan

2. **Regulatory Follow-up**
   - Respond to DPA inquiries
   - Implement corrective actions
```

**Automated Breach Detection:**

```python
# GDPR Breach Detection - Unauthorized Access Alert

import requests
from datetime import datetime

def check_unauthorized_access():
    """Monitor logs for unauthorized access patterns."""

    # Example: Check for failed authentication attempts
    failed_logins = parse_auth_logs('/var/log/auth.log')

    threshold = 10  # Failed attempts threshold
    if failed_logins > threshold:
        trigger_breach_alert(
            severity='high',
            description=f'{failed_logins} failed login attempts detected',
            affected_data='Authentication credentials'
        )

def trigger_breach_alert(severity, description, affected_data):
    """Trigger GDPR breach notification workflow."""

    alert = {
        'timestamp': datetime.utcnow().isoformat() + 'Z',
        'severity': severity,
        'description': description,
        'affected_data': affected_data,
        'incident_id': f'INC-{datetime.utcnow().strftime("%Y%m%d-%H%M%S")}'
    }

    # Log to breach register
    with open('/var/log/gdpr-breach-register.log', 'a') as f:
        f.write(f"{alert}\n")

    # Alert incident response team
    send_slack_alert(alert)
    send_email_alert(alert)

    # If high severity, start 72-hour countdown
    if severity == 'high':
        print(f"⚠️  GDPR BREACH DETECTED: {alert['incident_id']}")
        print(f"⏰ 72-hour notification deadline: {calculate_deadline(72)}")
```

---

## HIPAA Compliance for Urbit Deployments

### Overview

The Health Insurance Portability and Accountability Act (HIPAA) regulates the handling of Protected Health Information (PHI) in the United States. Urbit deployments used by healthcare providers or business associates must implement HIPAA Security Rule safeguards.

### HIPAA Security Rule: Three Safeguard Categories

#### 1. Administrative Safeguards

**Security Management Process (§164.308(a)(1))**

```markdown
# Risk Assessment for Urbit Fleet

## Asset Inventory
- 150 Urbit ships hosting PHI (patient groups, health records)
- 5 Kubernetes nodes (AWS EKS, us-east-1)
- S3 buckets for encrypted backups
- RDS PostgreSQL for metadata (no PHI stored)

## Threat Modeling
| Threat | Likelihood | Impact | Risk Level | Mitigation |
|--------|------------|--------|------------|------------|
| Unauthorized pier access | Medium | High | High | Encrypt piers with LUKS, implement MFA |
| S3 bucket misconfiguration | Low | Critical | High | Bucket policies, S3 Block Public Access |
| Insider threat (admin abuse) | Low | High | Medium | Audit logging, least privilege RBAC |
| DDoS on Ames network | Medium | Medium | Medium | Rate limiting, DDoS protection (CloudFlare) |
| Ransomware on K8s cluster | Low | Critical | Medium | Immutable backups, network segmentation |

## Risk Mitigation Plan
1. Enable LUKS encryption for all pier volumes (Q1 2025)
2. Implement MFA for all administrative access (Q1 2025)
3. Deploy AWS GuardDuty for threat detection (Q1 2025)
4. Conduct annual penetration testing (Q3 2025)
```

**Workforce Training (§164.308(a)(5))**

```markdown
# HIPAA Training Curriculum for Urbit Operators

## Module 1: HIPAA Basics
- What is PHI? (Protected Health Information)
- Covered Entities vs Business Associates
- Penalties for non-compliance (up to $1.5M per violation)

## Module 2: Technical Safeguards for Urbit
- Encryption at rest (LUKS pier encryption)
- Encryption in transit (HTTPS, TLS for Ames)
- Access controls (Kubernetes RBAC, ship permissions)
- Audit logging (CloudWatch, Prometheus)

## Module 3: Breach Response
- Recognizing a breach (unauthorized access, data exfiltration)
- Reporting procedures (notify security team within 1 hour)
- 60-day breach notification timeline (HHS, affected individuals)

## Module 4: Incident Scenarios
- Scenario 1: Laptop with ship credentials stolen
- Scenario 2: S3 bucket accidentally made public
- Scenario 3: Employee accesses PHI without authorization

## Annual Recertification
All employees must complete training annually and sign acknowledgment.
```

#### 2. Physical Safeguards

**Facility Access Controls (§164.310(a)(1))**

```markdown
# Physical Security Controls for On-Premises Urbit Infrastructure

## Data Center Security (Applicable if self-hosted)
- Badge-controlled access (security badges logged)
- Video surveillance (30-day retention)
- Visitor logs and escort requirements
- Environmental controls (fire suppression, UPS)

## Workstation Security
- Locked server racks for on-premises hardware
- Screen privacy filters for terminals displaying PHI
- Automatic screen lock after 5 minutes of inactivity
- Clean desk policy (no PHI printouts left unattended)

## Cloud Provider Physical Security (AWS/GCP/Azure)
- Rely on cloud provider SOC 2 Type II compliance
- AWS compliance: https://aws.amazon.com/compliance/hipaa-compliance/
- Verify BAA (Business Associate Agreement) with cloud provider
```

#### 3. Technical Safeguards

**Access Control (§164.312(a)(1))**

```yaml
# Kubernetes RBAC for HIPAA-Compliant Urbit Fleet

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: urbit-phi-readonly
  namespace: healthcare-phi
rules:
  # Read-only access to ship logs (for monitoring, no PHI modification)
  - apiGroups: [""]
    resources: ["pods/log"]
    verbs: ["get", "list"]
  # No exec access (prevents direct pier access)
  - apiGroups: [""]
    resources: ["pods/exec"]
    verbs: []  # Denied

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: support-engineer-readonly
  namespace: healthcare-phi
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: urbit-phi-readonly
subjects:
  - kind: User
    name: jane.doe@example.com  # Support engineer (read-only)

---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: urbit-phi-admin
  namespace: healthcare-phi
rules:
  - apiGroups: [""]
    resources: ["pods", "pods/log", "pods/exec", "secrets", "configmaps"]
    verbs: ["*"]  # Full access (for emergency incident response)

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: security-admin-full-access
  namespace: healthcare-phi
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: urbit-phi-admin
subjects:
  - kind: User
    name: security-admin@example.com  # HIPAA Security Officer only
```

**Audit Controls (§164.312(b))**

```python
#!/usr/bin/env python3
# HIPAA Audit Logging for Urbit Fleet
# Log all access to ships containing PHI

import json
from datetime import datetime
import boto3

class HIPAAAuditLogger:
    def __init__(self, log_stream='urbit-phi-access'):
        self.cloudwatch = boto3.client('logs', region_name='us-east-1')
        self.log_group = '/aws/urbit/hipaa-audit'
        self.log_stream = log_stream

    def log_access(self, event_type, user, ship, action, phi_accessed=False):
        """
        Log access to PHI-containing ships.

        Required HIPAA audit log elements:
        - Date and time of access
        - User identification
        - Action performed (read, write, delete)
        - Resources accessed (ship name)
        - Whether PHI was accessed
        """
        audit_event = {
            'timestamp': datetime.utcnow().isoformat() + 'Z',
            'event_type': event_type,  # 'authentication', 'data_access', 'data_modification', 'data_deletion'
            'user': user,
            'ship': ship,
            'action': action,
            'phi_accessed': phi_accessed,
            'source_ip': self.get_source_ip(),
            'session_id': self.get_session_id()
        }

        # Write to CloudWatch Logs (immutable, encrypted, 7-year retention)
        self.cloudwatch.put_log_events(
            logGroupName=self.log_group,
            logStreamName=self.log_stream,
            logEvents=[{
                'timestamp': int(datetime.utcnow().timestamp() * 1000),
                'message': json.dumps(audit_event)
            }]
        )

        # Also write to local audit file (backup)
        with open('/var/log/hipaa-audit.log', 'a') as f:
            f.write(json.dumps(audit_event) + '\n')

    def log_phi_access(self, user, ship, record_type):
        """Log whenever PHI is accessed (read or modified)."""
        self.log_access(
            event_type='phi_data_access',
            user=user,
            ship=ship,
            action=f'read_{record_type}',
            phi_accessed=True
        )

        # Alert if after-hours access (potential unauthorized access)
        hour = datetime.utcnow().hour
        if hour < 6 or hour > 18:  # Outside 6 AM - 6 PM UTC
            self.send_security_alert(f'After-hours PHI access by {user} on {ship}')

    def generate_audit_report(self, start_date, end_date):
        """Generate HIPAA audit report for compliance review."""
        # Query CloudWatch Logs
        response = self.cloudwatch.filter_log_events(
            logGroupName=self.log_group,
            logStreamName=self.log_stream,
            startTime=int(start_date.timestamp() * 1000),
            endTime=int(end_date.timestamp() * 1000),
            filterPattern='{ $.phi_accessed = true }'
        )

        events = [json.loads(e['message']) for e in response['events']]

        report = {
            'report_period': f'{start_date.date()} to {end_date.date()}',
            'total_phi_access_events': len(events),
            'unique_users': len(set(e['user'] for e in events)),
            'unique_ships': len(set(e['ship'] for e in events)),
            'events_by_user': self.count_by_key(events, 'user'),
            'events_by_ship': self.count_by_key(events, 'ship')
        }

        return report

# Usage example
logger = HIPAAAuditLogger()
logger.log_phi_access(
    user='dr.smith@hospital.com',
    ship='~patient-records-001',
    record_type='medical_history'
)
```

**HIPAA Audit Log Retention:**

```hcl
# Terraform: CloudWatch Logs with 7-Year Retention (HIPAA requirement)

resource "aws_cloudwatch_log_group" "hipaa_audit" {
  name              = "/aws/urbit/hipaa-audit"
  retention_in_days = 2557  # 7 years (HIPAA minimum)

  kms_key_id = aws_kms_key.hipaa_logs.arn  # Encrypt at rest

  tags = {
    Compliance = "HIPAA"
    DataType = "AuditLogs"
  }
}

resource "aws_kms_key" "hipaa_logs" {
  description             = "KMS key for HIPAA audit log encryption"
  deletion_window_in_days = 30
  enable_key_rotation     = true

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid    = "Enable IAM User Permissions"
        Effect = "Allow"
        Principal = {
          AWS = "arn:aws:iam::${data.aws_caller_identity.current.account_id}:root"
        }
        Action   = "kms:*"
        Resource = "*"
      },
      {
        Sid    = "Allow CloudWatch Logs"
        Effect = "Allow"
        Principal = {
          Service = "logs.${data.aws_region.current.name}.amazonaws.com"
        }
        Action = [
          "kms:Encrypt",
          "kms:Decrypt",
          "kms:ReEncrypt*",
          "kms:GenerateDataKey*",
          "kms:CreateGrant",
          "kms:DescribeKey"
        ]
        Resource = "*"
      }
    ]
  })
}
```

**Encryption and Decryption (§164.312(a)(2)(iv))**

See `advanced-security-patterns` skill for LUKS encryption implementation.

**Transmission Security (§164.312(e)(1))**

```nginx
# Nginx Configuration for HIPAA-Compliant HTTPS
# Enforce TLS 1.2+ with strong ciphers

server {
    listen 443 ssl http2;
    server_name *.urbit-phi.example.com;

    # TLS 1.2 and 1.3 only (HIPAA recommended)
    ssl_protocols TLSv1.2 TLSv1.3;

    # Strong cipher suites only
    ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305';
    ssl_prefer_server_ciphers on;

    # Certificates (Let's Encrypt or commercial CA)
    ssl_certificate /etc/letsencrypt/live/urbit-phi.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/urbit-phi.example.com/privkey.pem;

    # HSTS (force HTTPS for 1 year)
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;

    # Proxy to Urbit ship
    location / {
        proxy_pass http://127.0.0.1:8080;  # Ship's HTTP port
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    # Logging for HIPAA audit trail
    access_log /var/log/nginx/hipaa-access.log combined;
    error_log /var/log/nginx/hipaa-error.log warn;
}

# Redirect HTTP to HTTPS (enforce encryption)
server {
    listen 80;
    server_name *.urbit-phi.example.com;
    return 301 https://$host$request_uri;
}
```

### Business Associate Agreement (BAA)

**Required Components:**

```markdown
# Sample Business Associate Agreement (BAA) for Urbit Hosting

**BUSINESS ASSOCIATE AGREEMENT**

This Business Associate Agreement ("Agreement") is entered into as of [DATE] between:

**Covered Entity:** [HEALTHCARE PROVIDER NAME] ("Covered Entity")
**Business Associate:** [URBIT HOSTING COMPANY] ("Business Associate")

**RECITALS**

WHEREAS, Covered Entity is a healthcare provider subject to HIPAA; and

WHEREAS, Business Associate provides Urbit ship hosting services that may involve access to Protected Health Information (PHI);

NOW, THEREFORE, the parties agree as follows:

**1. DEFINITIONS**

1.1 "Protected Health Information" or "PHI" has the meaning given in 45 CFR § 160.103.

1.2 "Electronic Protected Health Information" or "ePHI" means PHI transmitted or maintained in electronic media.

**2. PERMITTED USES AND DISCLOSURES**

2.1 Business Associate shall use and disclose PHI only as necessary to:
   (a) Provide Urbit ship hosting services as specified in the Master Services Agreement
   (b) Perform data aggregation services
   (c) Comply with legal obligations

2.2 Business Associate shall NOT:
   (a) Use or disclose PHI for marketing purposes
   (b) Sell PHI without Covered Entity's authorization
   (c) Use PHI in a manner that would violate HIPAA if done by Covered Entity

**3. SAFEGUARDS**

3.1 Business Associate shall implement appropriate safeguards to prevent unauthorized use or disclosure of PHI, including:
   (a) Encryption at rest (AES-256) for all Urbit pier data
   (b) Encryption in transit (TLS 1.2+) for all network communications
   (c) Access controls (MFA, RBAC)
   (d) Audit logging with 7-year retention

**4. SUBCONTRACTORS**

4.1 Business Associate shall ensure that any subcontractors (e.g., AWS, cloud providers) agree to same restrictions and conditions as this BAA.

4.2 Current subcontractors:
   - Amazon Web Services (AWS) - Infrastructure provider [AWS BAA on file]
   - [List other subcontractors]

**5. BREACH NOTIFICATION**

5.1 Business Associate shall notify Covered Entity within 24 hours of discovery of any breach of unsecured PHI.

5.2 Notification shall include:
   - Date of breach discovery
   - Description of breach
   - Number of individuals affected
   - Mitigation steps taken

**6. ACCESS AND AMENDMENT**

6.1 Business Associate shall provide access to PHI within 30 days of Covered Entity's request.

6.2 Business Associate shall amend PHI as directed by Covered Entity within 60 days.

**7. TERMINATION**

7.1 Upon termination, Business Associate shall:
   (a) Return or destroy all PHI in its possession
   (b) Provide certification of destruction
   (c) Retain no copies except as required by law

**8. INDEMNIFICATION**

8.1 Business Associate shall indemnify Covered Entity for any violations of this BAA.

**SIGNATURES**

Covered Entity: ___________________________  Date: __________

Business Associate: _______________________  Date: __________
```

---

## SOC 2 Type II Compliance

### Overview

SOC 2 (Service Organization Control 2) is an auditing standard for service providers that store customer data. Type II audits evaluate the **operating effectiveness** of controls over a period of time (typically 6-12 months).

### Trust Services Criteria

**The Five Trust Services Criteria:**

1. **Security** - Protection against unauthorized access
2. **Availability** - System uptime and accessibility
3. **Processing Integrity** - Complete, valid, accurate, timely processing
4. **Confidentiality** - Protection of confidential information
5. **Privacy** - Collection, use, retention, disclosure of personal information

### SOC 2 Control Implementation for Urbit

#### Security (CC6.1: Logical Access Controls)

```yaml
# Control: Multi-Factor Authentication (MFA) for Administrative Access

Control ID: SEC-001
Control Category: Logical Access Controls (CC6.1)
Control Description: All administrative access to Urbit fleet infrastructure requires MFA.

Implementation:
  - AWS IAM: Enforce MFA for all IAM users with console access
  - Kubernetes: Require OIDC authentication with MFA (via Google Workspace, Okta)
  - SSH Access: Require hardware keys (YubiKey) for SSH to bastion hosts

Evidence Collection:
  - AWS IAM Credential Report (monthly)
  - Kubernetes audit logs showing OIDC MFA tokens
  - SSH authentication logs

Audit Test:
  - Auditor attempts to access AWS console without MFA (should be denied)
  - Review IAM policy requiring MFA: 'aws:MultiFactorAuthPresent' = 'true'
```

**Automated Evidence Collection:**

```python
#!/usr/bin/env python3
# SOC 2 Evidence Collection - MFA Enforcement

import boto3
import csv
from datetime import datetime

def collect_iam_mfa_evidence():
    """
    Collect evidence of MFA enforcement for SOC 2 audit.

    This script generates a report showing MFA status for all IAM users,
    which serves as SOC 2 evidence for security controls.
    """
    iam = boto3.client('iam')

    # Get credential report
    iam.generate_credential_report()
    response = iam.get_credential_report()
    report_csv = response['Content'].decode('utf-8')

    # Parse CSV
    lines = report_csv.split('\n')
    reader = csv.DictReader(lines)

    # Analyze MFA status
    evidence = []
    for row in reader:
        if row['user'] == '<root_account>':
            continue  # Skip root account

        mfa_active = row['mfa_active'] == 'true'

        evidence.append({
            'user': row['user'],
            'mfa_active': mfa_active,
            'password_enabled': row['password_enabled'] == 'true',
            'access_key_1_active': row['access_key_1_active'] == 'true',
            'access_key_2_active': row['access_key_2_active'] == 'true'
        })

    # Generate evidence report
    report_date = datetime.utcnow().strftime('%Y-%m-%d')
    report_path = f'/var/soc2-evidence/mfa-enforcement-{report_date}.csv'

    with open(report_path, 'w', newline='') as f:
        writer = csv.DictWriter(f, fieldnames=['user', 'mfa_active', 'password_enabled', 'access_key_1_active', 'access_key_2_active'])
        writer.writeheader()
        writer.writerows(evidence)

    # Check for non-compliant users (password enabled but no MFA)
    non_compliant = [e for e in evidence if e['password_enabled'] and not e['mfa_active']]

    if non_compliant:
        print(f"⚠️  SOC 2 CONTROL FAILURE: {len(non_compliant)} users without MFA")
        for user in non_compliant:
            print(f"  - {user['user']}")

        # Alert security team
        send_alert(f"SOC 2 Control Failure: {len(non_compliant)} users without MFA")
    else:
        print(f"✓ SOC 2 Control SEC-001: All {len(evidence)} users have MFA enabled")

    print(f"Evidence report: {report_path}")
    return report_path

if __name__ == '__main__':
    collect_iam_mfa_evidence()
```

#### Availability (A1.2: System Uptime Monitoring)

```yaml
# Control: 99.9% Uptime SLA Monitoring

Control ID: AVAIL-001
Control Category: Availability (A1.2)
Control Description: Urbit fleet maintains 99.9% uptime (max 43 minutes downtime/month).

Implementation:
  - Prometheus monitoring: Track ship uptime and API availability
  - Grafana dashboards: Real-time uptime visualization
  - PagerDuty alerts: Page on-call engineer if availability drops below 99.9%
  - Incident response: Documented runbooks for common failures

Evidence Collection:
  - Monthly uptime reports from Prometheus
  - Incident tickets with root cause analysis
  - PagerDuty incident logs

Audit Test:
  - Review uptime metrics for past 12 months
  - Verify incident response for any downtime events
```

**Prometheus Uptime Metric:**

```yaml
# Prometheus Recording Rule: Urbit Fleet Uptime

groups:
  - name: soc2_availability
    interval: 1m
    rules:
      # Calculate uptime percentage
      - record: urbit:fleet:uptime:percentage
        expr: |
          100 * (
            sum(up{job="urbit-ships"}) /
            count(up{job="urbit-ships"})
          )

      # Alert if uptime drops below 99.9%
      - alert: SOC2_UptimeBelowSLA
        expr: urbit:fleet:uptime:percentage < 99.9
        for: 5m
        labels:
          severity: critical
          compliance: soc2
        annotations:
          summary: "Urbit fleet uptime below SOC 2 SLA (99.9%)"
          description: "Current uptime: {{ $value }}%. Investigate immediately."
```

#### Processing Integrity (PI1.4: Data Validation)

```python
# SOC 2 Control: Input Validation for Urbit Groups API

def create_group(group_data):
    """
    Create a new Urbit group with input validation.

    SOC 2 Processing Integrity control: Validate all inputs to ensure
    complete, valid, accurate, and timely processing.
    """

    # Validation rules
    errors = []

    # 1. Required fields
    required_fields = ['name', 'owner', 'privacy']
    for field in required_fields:
        if field not in group_data or not group_data[field]:
            errors.append(f"Missing required field: {field}")

    # 2. Data type validation
    if 'name' in group_data and not isinstance(group_data['name'], str):
        errors.append("Field 'name' must be a string")

    # 3. Format validation (ship name)
    if 'owner' in group_data:
        if not group_data['owner'].startswith('~'):
            errors.append("Field 'owner' must be a valid ship name (e.g., ~sampel-palnet)")

    # 4. Range validation
    if 'privacy' in group_data:
        valid_privacy = ['public', 'private', 'secret']
        if group_data['privacy'] not in valid_privacy:
            errors.append(f"Field 'privacy' must be one of: {valid_privacy}")

    # 5. Length limits (prevent DoS)
    if 'name' in group_data and len(group_data['name']) > 100:
        errors.append("Field 'name' exceeds maximum length (100 characters)")

    if errors:
        # Log validation failure (SOC 2 evidence)
        log_validation_failure(group_data, errors)
        raise ValidationError(errors)

    # Proceed with group creation
    group_id = create_group_in_database(group_data)

    # Log successful processing (SOC 2 evidence)
    log_processing_event({
        'event': 'group_created',
        'group_id': group_id,
        'timestamp': datetime.utcnow().isoformat(),
        'validation_passed': True
    })

    return group_id
```

#### Confidentiality (C1.1: Encryption)

```bash
# SOC 2 Control: Encryption Key Rotation

#!/bin/bash
# Rotate KMS encryption keys annually (SOC 2 requirement)

KEY_ID="arn:aws:kms:us-east-1:123456789012:key/12345678-1234-1234-1234-123456789012"

echo "Rotating KMS key: $KEY_ID"

# Enable automatic key rotation (AWS rotates annually)
aws kms enable-key-rotation --key-id "$KEY_ID"

# Verify rotation is enabled
rotation_status=$(aws kms get-key-rotation-status --key-id "$KEY_ID" --query 'KeyRotationEnabled' --output text)

if [ "$rotation_status" = "True" ]; then
    echo "✓ SOC 2 Control C1.1: Key rotation enabled"

    # Log evidence
    echo "$(date -u +"%Y-%m-%dT%H:%M:%SZ") - KMS key rotation enabled for $KEY_ID" >> /var/soc2-evidence/key-rotation.log
else
    echo "⚠️  SOC 2 Control Failure: Key rotation NOT enabled"
    exit 1
fi
```

### SOC 2 Audit Preparation Checklist

```markdown
# SOC 2 Type II Audit Preparation Checklist

## Pre-Audit (3-6 months before)

- [ ] Define audit scope (which systems/services included)
- [ ] Select audit period (6-12 months)
- [ ] Choose audit firm (Big 4 or specialized)
- [ ] Map controls to Trust Services Criteria
- [ ] Implement missing controls
- [ ] Begin evidence collection automation

## Evidence Collection (Ongoing)

### Security Controls
- [ ] MFA enforcement reports (monthly)
- [ ] Access review logs (quarterly)
- [ ] Vulnerability scan reports (weekly)
- [ ] Penetration test reports (annual)
- [ ] Incident response logs

### Availability Controls
- [ ] Uptime metrics (99.9% SLA)
- [ ] Incident tickets with RCA
- [ ] Backup test logs (monthly)
- [ ] Disaster recovery test results (annual)

### Processing Integrity Controls
- [ ] Input validation logs
- [ ] Data processing error rates
- [ ] Reconciliation reports

### Confidentiality Controls
- [ ] Encryption verification (at rest & in transit)
- [ ] Key rotation logs
- [ ] Data classification policy

### Privacy Controls (if applicable)
- [ ] Privacy policy
- [ ] Cookie consent records
- [ ] Data deletion logs

## Audit Execution (1-3 months)

- [ ] Provide evidence to auditors
- [ ] Schedule auditor walkthroughs
- [ ] Demonstrate control effectiveness
- [ ] Respond to auditor questions
- [ ] Remediate identified deficiencies

## Post-Audit

- [ ] Receive SOC 2 Type II report
- [ ] Address any findings or exceptions
- [ ] Share report with customers (under NDA)
- [ ] Plan for next audit cycle
```

---

## ISO 27001 Compliance

### Overview

ISO 27001 is an international standard for Information Security Management Systems (ISMS). It requires organizations to systematically manage sensitive information through risk assessment, control selection, and continuous improvement.

### ISO 27001 ISMS Implementation for Urbit

**ISMS Scope Statement:**

```markdown
# Information Security Management System (ISMS) Scope

**Organization:** Urbit Fleet Hosting, Inc.

**Scope:** This ISMS applies to the following systems and processes:

**In Scope:**
- Production Urbit fleet infrastructure (200 ships on Kubernetes)
- Customer data stored in Urbit piers and S3 backups
- Fleet management systems (Ansible, Terraform, monitoring)
- Support processes (incident response, change management)

**Out of Scope:**
- Corporate IT systems (laptops, office network)
- Non-production development environments
- Third-party SaaS tools (GitHub, Slack)

**Physical Locations:**
- AWS us-east-1 region (virtual infrastructure)
- Remote workforce (distributed operations team)

**Regulatory Requirements:**
- GDPR (for EU customers)
- HIPAA (for healthcare customers)
- SOC 2 (for enterprise customers)
```

### Annex A Controls

ISO 27001 Annex A contains 114 security controls across 14 domains. Below are key controls for Urbit deployments.

#### A.8.1: Inventory of Assets

```markdown
# Asset Inventory (ISO 27001 A.8.1)

| Asset ID | Asset Type | Description | Owner | Criticality | Location |
|----------|------------|-------------|-------|-------------|----------|
| ASSET-001 | Urbit Ships | 200 production ships | CTO | High | AWS EKS |
| ASSET-002 | S3 Backups | Encrypted pier backups | Ops Lead | High | AWS S3 (us-east-1) |
| ASSET-003 | Kubernetes Cluster | EKS cluster (10 nodes) | Platform Eng | High | AWS EKS |
| ASSET-004 | Monitoring System | Prometheus + Grafana | SRE | Medium | AWS EC2 |
| ASSET-005 | Terraform State | IaC state files | DevOps | High | S3 (encrypted) |
| ASSET-006 | Customer Database | Billing/metadata (no PHI) | CTO | Medium | AWS RDS |

**Classification Levels:**
- **High**: Critical for business operations, contains sensitive data
- **Medium**: Important but not critical, limited sensitive data
- **Low**: Non-essential, publicly available information
```

#### A.9.1: Access Control Policy

```markdown
# Access Control Policy (ISO 27001 A.9.1)

## Purpose
Define rules for granting, reviewing, and revoking access to Urbit fleet systems.

## Scope
All production systems, including Kubernetes, AWS infrastructure, and Urbit ships.

## Policy

### 1. Access Provisioning
- All access requests must be approved by asset owner
- Principle of least privilege: Users receive minimum access required
- New hires provisioned within 24 hours of start date
- Access granted via role-based access control (RBAC)

### 2. Authentication
- Multi-Factor Authentication (MFA) mandatory for all users
- Passwords must meet complexity requirements (12+ chars, mixed case, numbers, symbols)
- Hardware tokens (YubiKey) required for production SSH access
- Session timeout after 30 minutes of inactivity

### 3. Access Reviews
- Quarterly access reviews by department managers
- Annual comprehensive review by CISO
- Automated alerts for dormant accounts (90 days inactivity)

### 4. Access Revocation
- Access revoked immediately upon termination (within 1 hour)
- Contractor access expires automatically after contract end date
- Temporary access (incident response) expires after 48 hours

### 5. Privileged Access
- Privileged accounts (root, admin) logged separately
- Privileged sessions recorded (Kubernetes audit logs)
- Break-glass access requires approval + post-incident review
```

#### A.12.1: Operational Procedures

```markdown
# Change Management Procedure (ISO 27001 A.12.1)

## Purpose
Ensure all changes to production Urbit fleet are planned, tested, approved, and documented.

## Procedure

### 1. Change Request
- Submitter creates change ticket in Jira
- Include: description, justification, risk assessment, rollback plan
- Categorize as: standard, normal, or emergency

### 2. Change Approval
- **Standard changes** (pre-approved, low risk): Automatic approval
  - Examples: Deploying new ship from template, routine |ota updates
- **Normal changes** (moderate risk): Requires ops lead approval
  - Examples: Kubernetes version upgrade, Terraform infrastructure changes
- **Emergency changes** (urgent, high risk): Requires CTO approval + post-implementation review
  - Examples: Hotfix for critical vulnerability, disaster recovery failover

### 3. Change Implementation
- Schedule change during maintenance window (Sundays 2-6 AM UTC)
- Update Grafana dashboard annotation: "Change in progress"
- Follow documented runbook
- Monitor metrics during and after change

### 4. Change Verification
- Verify change success (e.g., ship comes online, metrics normal)
- Run automated tests (health checks)
- Update change ticket with results

### 5. Post-Implementation Review
- Document lessons learned
- Update runbooks if needed
- For emergency changes: Conduct retrospective within 7 days
```

#### A.16.1: Incident Management

```python
#!/usr/bin/env python3
# ISO 27001 Incident Management Workflow

from enum import Enum
from datetime import datetime

class IncidentSeverity(Enum):
    CRITICAL = 1  # Production down, data breach
    HIGH = 2      # Partial outage, security vulnerability
    MEDIUM = 3    # Performance degradation, minor bug
    LOW = 4       # Cosmetic issue, feature request

class IncidentCategory(Enum):
    SECURITY = "security"
    AVAILABILITY = "availability"
    PERFORMANCE = "performance"
    DATA_INTEGRITY = "data_integrity"

class Incident:
    def __init__(self, title, severity, category):
        self.incident_id = self.generate_incident_id()
        self.title = title
        self.severity = severity
        self.category = category
        self.reported_at = datetime.utcnow()
        self.status = "open"
        self.assigned_to = None
        self.timeline = []

    def generate_incident_id(self):
        """Generate unique incident ID (ISO 27001 A.16.1.4)."""
        return f"INC-{datetime.utcnow().strftime('%Y%m%d-%H%M%S')}"

    def escalate(self):
        """Escalate incident based on severity."""
        if self.severity == IncidentSeverity.CRITICAL:
            self.page_oncall_engineer()
            self.notify_ciso()
            self.timeline.append(f"{datetime.utcnow().isoformat()}: Escalated to on-call and CISO")
        elif self.severity == IncidentSeverity.HIGH:
            self.assign_to_team_lead()
            self.timeline.append(f"{datetime.utcnow().isoformat()}: Escalated to team lead")

    def log_timeline_event(self, event):
        """Log incident timeline for post-mortem (ISO 27001 A.16.1.6)."""
        self.timeline.append(f"{datetime.utcnow().isoformat()}: {event}")

    def close_incident(self, resolution):
        """Close incident with resolution and lessons learned."""
        self.status = "closed"
        self.resolved_at = datetime.utcnow()
        self.resolution = resolution

        # Generate post-incident report (ISO 27001 A.16.1.7)
        self.generate_post_incident_report()

    def generate_post_incident_report(self):
        """Generate ISO 27001-compliant post-incident report."""
        report = f"""
        ISO 27001 INCIDENT REPORT
        =========================
        Incident ID: {self.incident_id}
        Title: {self.title}
        Severity: {self.severity.name}
        Category: {self.category.value}

        Timeline:
        {chr(10).join(self.timeline)}

        Resolution:
        {self.resolution}

        Root Cause:
        [To be filled by engineer]

        Corrective Actions:
        1. [Action 1]
        2. [Action 2]

        Preventive Actions:
        1. [Action 1]
        2. [Action 2]

        Lessons Learned:
        [To be discussed in retrospective]

        Report Generated: {datetime.utcnow().isoformat()}Z
        """

        with open(f'/var/iso27001-incidents/{self.incident_id}.txt', 'w') as f:
            f.write(report)

# Example usage
incident = Incident(
    title="S3 backup bucket accidentally deleted",
    severity=IncidentSeverity.CRITICAL,
    category=IncidentCategory.DATA_INTEGRITY
)
incident.escalate()
incident.log_timeline_event("Backup bucket deletion confirmed")
incident.log_timeline_event("Restored from secondary backup region (us-west-2)")
incident.close_incident("Bucket restored, data integrity verified. Implemented S3 MFA delete.")
```

#### A.18.1: Compliance with Legal Requirements

```markdown
# Legal and Regulatory Compliance Register

| Requirement | Source | Applicable | Owner | Review Frequency | Last Review |
|-------------|--------|-----------|-------|------------------|-------------|
| GDPR | EU Regulation 2016/679 | Yes (EU customers) | DPO | Annual | 2024-06-01 |
| HIPAA | 45 CFR Parts 160, 162, 164 | Yes (healthcare) | HIPAA Officer | Annual | 2024-03-15 |
| CCPA | California Civil Code §§ 1798.100-1798.199 | Yes (CA residents) | Privacy Counsel | Annual | 2024-07-01 |
| SOC 2 | AICPA Trust Services | Yes (enterprise) | CISO | Annual audit | 2024-09-30 |
| ISO 27001 | ISO/IEC 27001:2022 | Yes (certification) | CISO | Annual audit | 2024-11-01 |
| PCI DSS | Payment Card Industry | No (no card data) | N/A | N/A | N/A |
```

### Internal Audit Program

```markdown
# ISO 27001 Internal Audit Schedule (Annual)

| Audit ID | Scope | Controls Tested | Auditor | Scheduled Date | Status |
|----------|-------|-----------------|---------|----------------|--------|
| IA-2025-Q1 | Access Controls | A.9.1-A.9.4 | Jane Smith | 2025-01-15 | Planned |
| IA-2025-Q2 | Cryptography | A.10.1 | John Doe | 2025-04-15 | Planned |
| IA-2025-Q3 | Incident Mgmt | A.16.1 | Jane Smith | 2025-07-15 | Planned |
| IA-2025-Q4 | Business Continuity | A.17.1-A.17.2 | John Doe | 2025-10-15 | Planned |

**Audit Methodology:**
1. Review control documentation
2. Interview control owners
3. Test control effectiveness (sample transactions)
4. Document findings and non-conformances
5. Report to management
6. Track corrective actions
```

---

## Implementation Patterns

### Pattern 1: Automated Compliance Evidence Collection

**Problem:** Manual evidence collection for audits is time-consuming and error-prone.

**Solution:** Automate evidence collection with scheduled scripts and centralized storage.

```python
#!/usr/bin/env python3
# Automated Compliance Evidence Collection Pipeline

import os
import boto3
from datetime import datetime, timedelta
import subprocess
import json

class ComplianceEvidenceCollector:
    def __init__(self, evidence_bucket='compliance-evidence'):
        self.s3 = boto3.client('s3')
        self.evidence_bucket = evidence_bucket
        self.collection_date = datetime.utcnow().strftime('%Y-%m-%d')
        self.evidence_dir = f'/tmp/compliance-evidence-{self.collection_date}'
        os.makedirs(self.evidence_dir, exist_ok=True)

    def collect_all_evidence(self):
        """Run all evidence collection tasks."""
        print(f"=== Compliance Evidence Collection: {self.collection_date} ===\n")

        # GDPR Evidence
        self.collect_gdpr_evidence()

        # HIPAA Evidence
        self.collect_hipaa_evidence()

        # SOC 2 Evidence
        self.collect_soc2_evidence()

        # ISO 27001 Evidence
        self.collect_iso27001_evidence()

        # Upload to S3
        self.upload_to_s3()

        print(f"\n✓ Evidence collection complete: s3://{self.evidence_bucket}/{self.collection_date}/")

    def collect_gdpr_evidence(self):
        """Collect GDPR compliance evidence."""
        print("[GDPR] Collecting evidence...")

        # 1. Data retention policy enforcement
        self.run_command(
            'python3 /opt/compliance/gdpr-retention.py --report',
            output_file='gdpr-retention-report.txt'
        )

        # 2. Consent records
        self.run_command(
            'psql -h db.example.com -U readonly -d urbit_fleet -c "SELECT COUNT(*) FROM consent_records WHERE active = true"',
            output_file='gdpr-consent-count.txt'
        )

        # 3. Data breach register (should be empty, or contain incidents)
        self.copy_file(
            '/var/log/gdpr-breach-register.log',
            'gdpr-breach-register.log'
        )

    def collect_hipaa_evidence(self):
        """Collect HIPAA compliance evidence."""
        print("[HIPAA] Collecting evidence...")

        # 1. Encryption verification
        self.run_command(
            'aws kms describe-key --key-id alias/hipaa-phi-key --query "KeyMetadata.KeyState"',
            output_file='hipaa-encryption-status.txt'
        )

        # 2. Audit log sample
        self.run_command(
            'aws logs filter-log-events --log-group-name /aws/urbit/hipaa-audit --start-time $(date -d "7 days ago" +%s)000 --limit 100',
            output_file='hipaa-audit-log-sample.json'
        )

        # 3. Access control review
        self.run_command(
            'kubectl get rolebindings -n healthcare-phi -o json',
            output_file='hipaa-k8s-access-controls.json'
        )

    def collect_soc2_evidence(self):
        """Collect SOC 2 compliance evidence."""
        print("[SOC 2] Collecting evidence...")

        # 1. MFA enforcement
        self.run_command(
            'python3 /opt/compliance/soc2-mfa-report.py',
            output_file='soc2-mfa-enforcement.csv'
        )

        # 2. Uptime metrics
        self.run_command(
            'curl -s "http://prometheus:9090/api/v1/query?query=urbit:fleet:uptime:percentage"',
            output_file='soc2-uptime-metrics.json'
        )

        # 3. Incident log
        self.copy_file(
            '/var/log/incidents.log',
            'soc2-incident-log.txt'
        )

    def collect_iso27001_evidence(self):
        """Collect ISO 27001 compliance evidence."""
        print("[ISO 27001] Collecting evidence...")

        # 1. Asset inventory
        self.run_command(
            'terraform show -json',
            output_file='iso27001-asset-inventory.json'
        )

        # 2. Change management log
        self.copy_file(
            '/var/log/change-management.log',
            'iso27001-change-log.txt'
        )

        # 3. Vulnerability scan results
        self.run_command(
            'trivy image --format json $(kubectl get pods -n urbit-fleet -o jsonpath="{.items[0].spec.containers[0].image}")',
            output_file='iso27001-vulnerability-scan.json'
        )

    def run_command(self, command, output_file):
        """Run shell command and save output."""
        try:
            result = subprocess.run(
                command,
                shell=True,
                capture_output=True,
                text=True,
                timeout=300
            )
            with open(f'{self.evidence_dir}/{output_file}', 'w') as f:
                f.write(result.stdout)
            print(f"  ✓ {output_file}")
        except Exception as e:
            print(f"  ✗ {output_file}: {e}")

    def copy_file(self, source, dest):
        """Copy file to evidence directory."""
        try:
            subprocess.run(['cp', source, f'{self.evidence_dir}/{dest}'], check=True)
            print(f"  ✓ {dest}")
        except Exception as e:
            print(f"  ✗ {dest}: {e}")

    def upload_to_s3(self):
        """Upload evidence to S3 with encryption."""
        print("\n[S3] Uploading evidence...")

        for filename in os.listdir(self.evidence_dir):
            file_path = os.path.join(self.evidence_dir, filename)
            s3_key = f'{self.collection_date}/{filename}'

            self.s3.upload_file(
                file_path,
                self.evidence_bucket,
                s3_key,
                ExtraArgs={
                    'ServerSideEncryption': 'aws:kms',
                    'StorageClass': 'STANDARD_IA'  # Infrequent Access (cheaper for archival)
                }
            )
            print(f"  ✓ s3://{self.evidence_bucket}/{s3_key}")

if __name__ == '__main__':
    collector = ComplianceEvidenceCollector()
    collector.collect_all_evidence()
```

**Cron Schedule:**

```cron
# Collect compliance evidence daily at 3 AM UTC
0 3 * * * /usr/local/bin/compliance-evidence-collector.py >> /var/log/compliance-evidence.log 2>&1
```

---

### Pattern 2: Privacy-by-Design for Urbit Applications

**Problem:** Urbit applications may inadvertently collect excessive personal data, violating GDPR data minimization.

**Solution:** Implement privacy-by-design principles in Urbit agents.

```hoon
:: Privacy-by-Design Urbit Agent Example
:: Collect only necessary data, pseudonymize where possible

/+  default-agent, dbug
|%
+$  versioned-state
  $%  state-0
  ==
+$  state-0
  $:  %0
      :: Store only pseudonymized user IDs (hash of ship name)
      user-analytics=(map @ux analytics-record)

      :: Consent records (required for GDPR)
      consents=(map @p consent-status)
  ==
+$  analytics-record
  $:  pseudonym-id=@ux     :: SHA-256 hash of ship name (irreversible)
      page-views=(list @da)  :: Timestamps only (no content)
      last-active=@da
  ==
+$  consent-status
  $:  analytics-consent=?
      marketing-consent=?
      granted-at=@da
  ==
--

=|  state-0
=*  state  -
^-  agent:gall

|_  =bowl:gall
+*  this  .
    def   ~(. (default-agent this %.n) bowl)

++  on-init
  ^-  (quip card _this)
  `this

++  on-poke
  |=  [=mark =vase]
  ^-  (quip card _this)
  ?>  =(src.bowl our.bowl)  :: Only accept local pokes

  ?+    mark  (on-poke:def mark vase)
      %track-pageview
    =/  ship  !<(@p vase)

    :: Check consent before tracking
    =/  consent  (~(get by consents.state) ship)
    ?~  consent
      ~&  "No consent record for {<ship>}, not tracking"
      `this
    ?.  analytics-consent.u.consent
      ~&  "User {<ship>} has not consented to analytics"
      `this

    :: Pseudonymize ship name (GDPR requirement)
    =/  pseudonym  (shax (jam ship))

    :: Store only timestamp (data minimization)
    =/  record  (~(gut by user-analytics.state) pseudonym *analytics-record)
    =.  page-views.record  [now.bowl page-views.record]
    =.  last-active.record  now.bowl
    =.  user-analytics.state  (~(put by user-analytics.state) pseudonym record)

    `this

      %grant-consent
    =/  [ship=@p analytics=? marketing=?]  !<([ship=@p analytics=? marketing=?] vase)

    :: Store consent (GDPR Article 7)
    =.  consents.state
      %+  ~(put by consents.state)  ship
      [analytics marketing now.bowl]

    ~&  "Consent granted for {<ship>}: analytics={<analytics>}, marketing={<marketing>}"
    `this

      %withdraw-consent
    =/  ship  !<(@p vase)

    :: Withdraw consent (GDPR Article 7(3))
    =.  consents.state  (~(del by consents.state) ship)

    :: Delete all analytics data for this user (GDPR Article 17)
    =/  pseudonym  (shax (jam ship))
    =.  user-analytics.state  (~(del by user-analytics.state) pseudonym)

    ~&  "Consent withdrawn and data deleted for {<ship>}"
    `this
  ==

++  on-watch  on-watch:def
++  on-leave  on-leave:def
++  on-peek   on-peek:def
++  on-agent  on-agent:def
++  on-arvo   on-arvo:def
++  on-fail   on-fail:def
--
```

---

### Pattern 3: Multi-Framework Compliance Dashboard

**Problem:** Managing evidence for multiple frameworks (GDPR, HIPAA, SOC 2, ISO 27001) is complex.

**Solution:** Unified compliance dashboard with framework mapping.

```python
#!/usr/bin/env python3
# Multi-Framework Compliance Dashboard Generator

import json
from datetime import datetime

class ComplianceDashboard:
    def __init__(self):
        self.controls = self.load_controls()

    def load_controls(self):
        """Load control mappings for all frameworks."""
        return {
            'mfa_enforcement': {
                'name': 'Multi-Factor Authentication (MFA)',
                'description': 'Enforce MFA for all administrative access',
                'frameworks': {
                    'SOC2': 'CC6.1 - Logical Access Controls',
                    'ISO27001': 'A.9.4.2 - Secure log-on procedures',
                    'HIPAA': '§164.312(a)(2)(i) - Unique User Identification',
                    'NIST': 'IA-2(1) - MFA to Privileged Accounts'
                },
                'status': 'compliant',
                'last_verified': '2024-12-01',
                'evidence': 's3://compliance-evidence/2024-12-01/soc2-mfa-enforcement.csv'
            },
            'encryption_at_rest': {
                'name': 'Encryption at Rest (AES-256)',
                'description': 'All Urbit pier data encrypted with AES-256',
                'frameworks': {
                    'SOC2': 'CC6.7 - Encryption',
                    'ISO27001': 'A.10.1.1 - Policy on cryptographic controls',
                    'HIPAA': '§164.312(a)(2)(iv) - Encryption',
                    'GDPR': 'Article 32(1)(a) - Pseudonymisation and encryption'
                },
                'status': 'compliant',
                'last_verified': '2024-12-01',
                'evidence': 's3://compliance-evidence/2024-12-01/hipaa-encryption-status.txt'
            },
            'audit_logging': {
                'name': 'Audit Logging (7-year retention)',
                'description': 'Comprehensive audit logs with 7-year retention',
                'frameworks': {
                    'SOC2': 'CC7.2 - System Monitoring',
                    'ISO27001': 'A.12.4.1 - Event logging',
                    'HIPAA': '§164.312(b) - Audit controls',
                    'PCI': '10.2 - Audit Trails'
                },
                'status': 'compliant',
                'last_verified': '2024-12-01',
                'evidence': 's3://compliance-evidence/2024-12-01/hipaa-audit-log-sample.json'
            },
            'data_retention': {
                'name': 'Automated Data Retention',
                'description': 'Automated deletion after retention period',
                'frameworks': {
                    'GDPR': 'Article 5(1)(e) - Storage limitation',
                    'ISO27001': 'A.8.3 - Handling of assets',
                    'CCPA': '§1798.105 - Right to deletion'
                },
                'status': 'compliant',
                'last_verified': '2024-12-01',
                'evidence': 's3://compliance-evidence/2024-12-01/gdpr-retention-report.txt'
            },
            'vulnerability_scanning': {
                'name': 'Vulnerability Scanning (Weekly)',
                'description': 'Automated Trivy scans of container images',
                'frameworks': {
                    'SOC2': 'CC7.1 - Threat Detection',
                    'ISO27001': 'A.12.6.1 - Technical vulnerability management',
                    'PCI': '11.2 - Quarterly Vulnerability Scans'
                },
                'status': 'compliant',
                'last_verified': '2024-12-01',
                'evidence': 's3://compliance-evidence/2024-12-01/iso27001-vulnerability-scan.json'
            },
            'incident_response': {
                'name': 'Incident Response Plan',
                'description': 'Documented incident response procedures',
                'frameworks': {
                    'SOC2': 'CC7.3 - Incident Management',
                    'ISO27001': 'A.16.1 - Incident management',
                    'HIPAA': '§164.308(a)(6) - Incident Response',
                    'GDPR': 'Article 33 - Breach Notification'
                },
                'status': 'compliant',
                'last_verified': '2024-12-01',
                'evidence': 's3://compliance-evidence/2024-12-01/soc2-incident-log.txt'
            }
        }

    def generate_html_dashboard(self):
        """Generate HTML compliance dashboard."""

        html = """
        <!DOCTYPE html>
        <html>
        <head>
            <title>Urbit Fleet Compliance Dashboard</title>
            <style>
                body { font-family: Arial, sans-serif; margin: 20px; background: #f5f5f5; }
                h1 { color: #333; }
                .framework-summary { display: flex; gap: 20px; margin: 20px 0; }
                .framework-card {
                    background: white;
                    padding: 20px;
                    border-radius: 8px;
                    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
                    flex: 1;
                }
                .framework-card h3 { margin-top: 0; color: #2c5aa0; }
                .status-compliant { color: green; font-weight: bold; }
                .status-non-compliant { color: red; font-weight: bold; }
                table { width: 100%; border-collapse: collapse; background: white; }
                th, td { padding: 12px; text-align: left; border-bottom: 1px solid #ddd; }
                th { background: #2c5aa0; color: white; }
                tr:hover { background: #f5f5f5; }
                .evidence-link { color: #2c5aa0; text-decoration: none; }
            </style>
        </head>
        <body>
            <h1>Urbit Fleet Compliance Dashboard</h1>
            <p>Generated: """ + datetime.utcnow().strftime('%Y-%m-%d %H:%M:%S UTC') + """</p>

            <div class="framework-summary">
                <div class="framework-card">
                    <h3>SOC 2 Type II</h3>
                    <p class="status-compliant">✓ COMPLIANT</p>
                    <p>All Trust Services Criteria controls implemented</p>
                    <p>Next Audit: 2025-09-30</p>
                </div>
                <div class="framework-card">
                    <h3>ISO 27001:2022</h3>
                    <p class="status-compliant">✓ CERTIFIED</p>
                    <p>114/114 Annex A controls implemented</p>
                    <p>Next Audit: 2025-11-01</p>
                </div>
                <div class="framework-card">
                    <h3>HIPAA</h3>
                    <p class="status-compliant">✓ COMPLIANT</p>
                    <p>All Security Rule safeguards implemented</p>
                    <p>BAA available for healthcare customers</p>
                </div>
                <div class="framework-card">
                    <h3>GDPR</h3>
                    <p class="status-compliant">✓ COMPLIANT</p>
                    <p>All data subject rights supported</p>
                    <p>EU data residency enforced</p>
                </div>
            </div>

            <h2>Control Implementation Matrix</h2>
            <table>
                <tr>
                    <th>Control</th>
                    <th>Description</th>
                    <th>SOC 2</th>
                    <th>ISO 27001</th>
                    <th>HIPAA</th>
                    <th>GDPR</th>
                    <th>Status</th>
                    <th>Evidence</th>
                </tr>
        """

        for control_id, control in self.controls.items():
            html += f"""
                <tr>
                    <td><strong>{control['name']}</strong></td>
                    <td>{control['description']}</td>
                    <td>{control['frameworks'].get('SOC2', '-')}</td>
                    <td>{control['frameworks'].get('ISO27001', '-')}</td>
                    <td>{control['frameworks'].get('HIPAA', '-')}</td>
                    <td>{control['frameworks'].get('GDPR', '-')}</td>
                    <td class="status-{control['status']}">{control['status'].upper()}</td>
                    <td><a href="{control['evidence']}" class="evidence-link">View</a></td>
                </tr>
            """

        html += """
            </table>
        </body>
        </html>
        """

        with open('/var/www/compliance-dashboard.html', 'w') as f:
            f.write(html)

        print("✓ Compliance dashboard generated: /var/www/compliance-dashboard.html")

if __name__ == '__main__':
    dashboard = ComplianceDashboard()
    dashboard.generate_html_dashboard()
```

---

## Best Practices

1. **Compliance-as-Code:** Automate compliance checks with infrastructure-as-code (Terraform policy-as-code, OPA for Kubernetes admission control)

2. **Continuous Evidence Collection:** Don't scramble before audits—collect evidence continuously with automated scripts

3. **Framework Mapping:** Map controls to multiple frameworks simultaneously to reduce duplicate effort

4. **Privacy by Design:** Build GDPR/CCPA compliance into Urbit applications from the start, not as an afterthought

5. **Regular Training:** Conduct annual HIPAA/GDPR training for all employees who handle sensitive data

6. **Incident Drills:** Practice breach notification procedures with tabletop exercises (test your 72-hour GDPR timeline)

7. **Third-Party Audits:** Get SOC 2 Type II and ISO 27001 certification from accredited auditors (not self-attestation)

8. **BAAs with Subcontractors:** Ensure all cloud providers (AWS, GCP, Azure) have signed Business Associate Agreements

---

## Common Pitfalls

1. **Scope Creep:** Don't try to certify your entire organization—start with a limited ISMS scope (just production Urbit fleet)

2. **Over-Collection:** Don't log IP addresses, user behavior, or personal data unless you have a documented legal basis and retention policy

3. **Weak Encryption:** Don't use outdated algorithms (MD5, SHA-1, DES)—use AES-256, SHA-256, TLS 1.2+

4. **Manual Processes:** Don't rely on manual evidence collection—automate with scripts and CI/CD pipelines

5. **Missing Consent:** Don't assume implied consent—get explicit, documented consent for GDPR processing

6. **Ignoring Breaches:** Don't hide breaches—GDPR fines for non-notification (up to €20M) are higher than for the breach itself

7. **Stale Policies:** Don't let compliance policies go stale—review and update annually (ISO 27001 requirement)

8. **Single Framework Focus:** Don't optimize for just one framework—use control mapping to satisfy multiple frameworks simultaneously

---

## Related Skills

**Phase 1: Core Urbit Operations**
- `deployment-basics` - Deploy compliant infrastructure (encryption, access controls)
- `urbit-id-system` - Understand identity for access control and audit logging
- `networking-fundamentals` - Configure secure networking (VPNs, firewalls, TLS)

**Phase 2: Platform Operations**
- `advanced-security-patterns` - Implement LUKS encryption, network segmentation, DDoS protection
- `performance-profiling` - Monitor SLOs for SOC 2 availability requirements
- `backup-strategies` - Implement GDPR-compliant backup retention and deletion

**Phase 3: Fleet & Compliance**
- `fleet-manager` - Deploy compliance controls at scale across 100+ ships
- `/deploy-fleet` - Provision compliant infrastructure with IaC (Terraform)
- `performance-engineer` - Optimize fleet performance for SOC 2 uptime SLAs

**Related Workflows (Backend, Security, Cloud)**
- `backend-development::api-design-principles` - Design GDPR-compliant APIs (data minimization, consent)
- `full-stack-orchestration::security-auditor` - Conduct security audits for ISO 27001/SOC 2
- `cloud-infrastructure::cloud-architect` - Design compliant cloud architectures (data residency, encryption)
- `observability-monitoring::slo-implementation` - Implement SOC 2 availability SLOs
- `kubernetes-operations::k8s-security-policies` - Enforce HIPAA/PCI security policies in Kubernetes

---

## Resources

### GDPR
- [GDPR Official Text](https://gdpr-info.eu/)
- [ICO GDPR Guidance](https://ico.org.uk/for-organisations/guide-to-data-protection/guide-to-the-general-data-protection-regulation-gdpr/)
- [EDPB Guidelines](https://edpb.europa.eu/our-work-tools/general-guidance/gdpr-guidelines-recommendations-best-practices_en)

### HIPAA
- [HHS HIPAA Guidance](https://www.hhs.gov/hipaa/for-professionals/security/index.html)
- [HIPAA Security Rule](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)
- [AWS HIPAA Compliance](https://aws.amazon.com/compliance/hipaa-compliance/)

### SOC 2
- [AICPA Trust Services Criteria](https://www.aicpa.org/resources/landing/trust-services-criteria)
- [Vanta SOC 2 Guide](https://www.vanta.com/resources/soc-2-compliance-guide)
- [Drata SOC 2 Checklist](https://drata.com/soc-2/checklist)

### ISO 27001
- [ISO 27001:2022 Standard](https://www.iso.org/standard/27001)
- [ISO 27001 Annex A Controls](https://www.isms.online/iso-27001/annex-a/)
- [NCC Group ISO 27001 Toolkit](https://www.nccgroup.com/us/research-blog/iso-27001-toolkit/)

### Compliance Tools
- [OpenSCAP](https://www.open-scap.org/) - Security compliance scanning
- [OPA (Open Policy Agent)](https://www.openpolicyagent.org/) - Policy-as-code
- [Terraform Sentinel](https://www.terraform.io/cloud-docs/sentinel) - IaC compliance
- [Prowler](https://github.com/prowler-cloud/prowler) - AWS security/compliance scanner
