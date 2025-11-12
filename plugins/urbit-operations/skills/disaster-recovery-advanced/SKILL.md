# Disaster Recovery Advanced Skill

Enterprise-grade disaster recovery for Urbit deployments including multi-region replication, automated recovery orchestration, and business continuity planning.

## Advanced DR Strategies

### Multi-Region Backup Replication

**Objective**: Geographic redundancy for disaster scenarios (datacenter failure, regional outage).

```bash
# Automated multi-region backup script
#!/bin/bash
set -euo pipefail

SHIP_NAME="sampel-palnet"
PIER_PATH="/home/urbit/$SHIP_NAME"
DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_FILE="$SHIP_NAME-$DATE.tar.gz"

# Stop ship
systemctl stop urbit-$SHIP_NAME
sleep 30

# Create backup
tar czf /tmp/$BACKUP_FILE $PIER_PATH

# Restart ship
systemctl start urbit-$SHIP_NAME

# Upload to multiple regions
aws s3 cp /tmp/$BACKUP_FILE s3://urbit-backups-us-east/$BACKUP_FILE
aws s3 cp /tmp/$BACKUP_FILE s3://urbit-backups-eu-west/$BACKUP_FILE --region eu-west-1
aws s3 cp /tmp/$BACKUP_FILE s3://urbit-backups-ap-southeast/$BACKUP_FILE --region ap-southeast-1

# Verify uploads
for bucket in urbit-backups-us-east urbit-backups-eu-west urbit-backups-ap-southeast; do
    aws s3 ls s3://$bucket/$BACKUP_FILE || echo "ERROR: Upload failed to $bucket"
done

# Cleanup local
rm /tmp/$BACKUP_FILE
```

### Automated Recovery Orchestration

**Scenario**: Primary VPS fails, automatically provision and restore to secondary region.

```bash
#!/bin/bash
# recovery-orchestrator.sh

PRIMARY_IP="203.0.113.10"
BACKUP_REGION="us-west-2"

# Health check primary
if ! ping -c 3 $PRIMARY_IP &> /dev/null; then
    echo "PRIMARY FAILURE DETECTED"

    # Provision new VPS (Terraform)
    cd /opt/disaster-recovery/terraform
    terraform apply -auto-approve -var="region=$BACKUP_REGION"

    # Get new IP
    NEW_IP=$(terraform output -raw ship_ip)

    # Restore latest backup
    LATEST_BACKUP=$(aws s3 ls s3://urbit-backups-$BACKUP_REGION/ | sort | tail -1 | awk '{print $4}')
    ssh root@$NEW_IP "aws s3 cp s3://urbit-backups-$BACKUP_REGION/$LATEST_BACKUP /tmp/ && tar xzf /tmp/$LATEST_BACKUP -C /home/urbit/"

    # Start ship
    ssh root@$NEW_IP "systemctl start urbit-ship"

    # Update DNS
    # (Automate with Route53, CloudFlare API, etc.)
    echo "UPDATE DNS: ship.yourdomain.com → $NEW_IP"

    # Send alert
    curl -X POST https://hooks.slack.com/services/YOUR/WEBHOOK/URL \
        -d "{\"text\":\"DR activated: Ship restored to $NEW_IP\"}"
fi
```

### Continuous Data Protection (CDP)

**Objective**: Near-zero RPO (Recovery Point Objective) using real-time replication.

**Method 1: ZFS Replication** (if using ZFS filesystem):
```bash
# Snapshot every 5 minutes
*/5 * * * * zfs snapshot tank/urbit/pier@$(date +\%Y\%m\%d-\%H\%M)

# Replicate to remote server
*/10 * * * * zfs send -i tank/urbit/pier@previous tank/urbit/pier@latest | ssh backup-server zfs receive tank/backup/pier
```

**Method 2: rsync Continuous Sync**:
```bash
# Sync every minute (ship must be stopped for restore)
* * * * * rsync -az --delete /home/urbit/pier/ backup-server:/backups/pier-live/
```

**Warning**: Live pier replication only safe if ship NOT running on backup.

### Immutable Backups

**Objective**: Ransomware protection, prevent backup deletion.

```bash
# S3 Object Lock (WORM - Write Once Read Many)
aws s3api put-object-lock-configuration \
    --bucket urbit-backups \
    --object-lock-configuration '{
        "ObjectLockEnabled": "Enabled",
        "Rule": {
            "DefaultRetention": {
                "Mode": "COMPLIANCE",
                "Days": 90
            }
        }
    }'

# Upload backup with retention
aws s3api put-object \
    --bucket urbit-backups \
    --key ship-backup.tar.gz \
    --body ship-backup.tar.gz \
    --object-lock-mode COMPLIANCE \
    --object-lock-retain-until-date "2025-12-31T00:00:00Z"
```

## RTO/RPO Targets

| Scenario | RTO (Recovery Time) | RPO (Data Loss) | Strategy |
|----------|---------------------|-----------------|----------|
| **VPS Failure** | < 4 hours | < 24 hours | Daily backups, manual restore |
| **Datacenter Outage** | < 2 hours | < 1 hour | Multi-region, automated failover |
| **Ransomware** | < 8 hours | < 24 hours | Immutable backups, isolated restore |
| **Pier Corruption** | < 1 hour | < 24 hours | Automated backup validation |
| **Human Error** | < 30 min | < 1 hour | Point-in-time recovery, frequent backups |

## High Availability Patterns

### Active-Passive HA

**Setup**:
- Primary ship: Active (serving traffic)
- Secondary server: Standby (ship NOT running, pier synced)
- Healthcheck monitor: Detects primary failure

**Failover**:
1. Monitor detects primary down
2. Start ship on secondary
3. Update DNS to secondary IP
4. **Downtime**: 5-15 minutes (DNS propagation)

### Backup Verification Automation

```bash
#!/bin/bash
# verify-backup.sh - Run weekly

BACKUP_FILE="/backups/latest-backup.tar.gz"
TEST_DIR="/tmp/backup-verification-$(date +%s)"

# Extract to temp directory
mkdir -p $TEST_DIR
tar xzf $BACKUP_FILE -C $TEST_DIR

# Verify pier structure
if [ ! -f "$TEST_DIR/pier/.urb/chk/control.jam" ]; then
    echo "ERROR: Invalid pier structure"
    # Send alert
    exit 1
fi

# Attempt dry-run boot (requires fake ship)
timeout 60 urbit $TEST_DIR/pier --dry-run || echo "WARNING: Dry-run boot failed"

# Cleanup
rm -rf $TEST_DIR

echo "Backup verification: PASSED"
```

## Disaster Scenarios & Runbooks

### Scenario: Complete Datacenter Loss

**Runbook**:
1. Confirm primary region inaccessible (>30 min outage)
2. Execute DR plan:
   ```bash
   /opt/dr/activate-secondary-region.sh
   ```
3. Provision new VPS in secondary region (Terraform)
4. Restore latest backup from geo-replicated storage
5. Start ship, verify functionality
6. Update DNS (A record to new IP)
7. Monitor for 24 hours
8. Document incident, update DR plan

**Expected RTO**: 2-4 hours

### Scenario: Crypto-Locker/Ransomware

**Runbook**:
1. **Isolate** infected system (disconnect network)
2. Do NOT pay ransom
3. Identify last clean backup (pre-infection)
4. Provision clean VPS
5. Restore from immutable backup (S3 Object Lock)
6. Verify no malware in restored pier
7. Start ship on clean system
8. Analyze infection vector, patch vulnerability

**Expected RTO**: 4-8 hours

### Scenario: Accidental |nuke (Factory Reset)

**Recovery**:
1. **Stop ship immediately** (if still running)
2. Do NOT issue any more dojo commands
3. Restore pier from most recent backup
4. If no backup: **Data loss permanent**
5. Prevention: Disable |nuke in production (requires code modification)

## Business Continuity Planning

### DR Documentation

Required documents:
1. **DR Plan**: Step-by-step recovery procedures
2. **Contact List**: On-call admins, vendor support numbers
3. **Asset Inventory**: All ships, IPs, providers, credentials
4. **Backup Locations**: S3 buckets, regions, retention policies
5. **RTO/RPO Targets**: Per-scenario recovery objectives

### DR Testing Schedule

- **Monthly**: Backup verification (automated)
- **Quarterly**: Restore test (full restoration to test environment)
- **Annually**: Full DR drill (failover to secondary region, all teams)

### DR Metrics

Track and report:
- Backup success rate (target: 100%)
- Average backup size
- Backup duration
- Recovery test success rate
- Time since last successful DR drill

## Reference

- AWS S3 Object Lock: https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html
- Disaster Recovery on AWS: https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/

## Summary

Advanced disaster recovery for Urbit deployments implements multi-region backup replication (3 geographic locations), automated recovery orchestration (Terraform + scripting), continuous data protection (5-minute snapshots), and immutable backups (S3 Object Lock, 90-day retention). RTO targets: VPS failure <4h, datacenter outage <2h, ransomware <8h. High availability patterns: active-passive with health monitoring, automated failover. Business continuity requires quarterly DR testing, documented runbooks for scenarios (datacenter loss, ransomware, human error), and tracked metrics (backup success rate, recovery test results). Prevention > recovery: backup verification automation, immutable storage, geo-replication.
