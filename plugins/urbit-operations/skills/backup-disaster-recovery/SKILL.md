# Backup and Disaster Recovery Skill

Comprehensive backup strategies, recovery procedures, and business continuity planning for Urbit ship deployments.

## Overview

Effective backup and disaster recovery ensures ship availability, data protection, and rapid recovery from failures including hardware failures, data corruption, and operational errors.

## Critical Backup Principles

### 1. NEVER Backup Live Piers

**CRITICAL**: Backing up a running ship corrupts the event log

**Safe Backup Procedure**:
```bash
# 1. Stop ship gracefully
# In dojo: ctrl+d
# Or: systemctl stop urbit-ship

# 2. Wait for complete shutdown
sleep 30

# 3. Verify ship stopped
ps aux | grep urbit  # Should show nothing

# 4. Backup pier
tar czf ship-backup-$(date +%Y%m%d).tar.gz /path/to/pier

# 5. Restart ship
urbit /path/to/pier
# Or: systemctl start urbit-ship
```

### 2. 3-2-1 Backup Rule

- **3** copies of data (original + 2 backups)
- **2** different storage types (local disk + cloud)
- **1** offsite backup (different geographic location)

### 3. Test Restorations Quarterly

Untested backups = no backups

## Automated Backup Script

```bash
# /usr/local/bin/urbit-backup.sh
#!/bin/bash
set -euo pipefail

# Configuration
SHIP_NAME="sampel-palnet"
PIER_PATH="/home/urbit/$SHIP_NAME"
BACKUP_DIR="/backups/urbit"
DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_FILE="$BACKUP_DIR/$SHIP_NAME-$DATE.tar.gz"
RETENTION=7  # Keep last 7 backups
LOG_FILE="/var/log/urbit-backup.log"

# Logging
exec > >(tee -a "$LOG_FILE")
exec 2>&1

echo "=== Urbit Backup Started: $(date) ==="

# Step 1: Stop ship gracefully
echo "Stopping ship..."
systemctl stop urbit-$SHIP_NAME

# Step 2: Wait for complete shutdown
echo "Waiting for ship to stop..."
sleep 30

# Step 3: Verify ship stopped
if pgrep -f "urbit.*$SHIP_NAME" > /dev/null; then
    echo "ERROR: Ship still running! Aborting backup."
    systemctl start urbit-$SHIP_NAME
    exit 1
fi

# Step 4: Create backup
echo "Creating backup..."
tar czf "$BACKUP_FILE" -C "$(dirname "$PIER_PATH")" "$(basename "$PIER_PATH")"

# Step 5: Verify backup integrity
echo "Verifying backup..."
if ! tar tzf "$BACKUP_FILE" > /dev/null; then
    echo "ERROR: Backup verification failed!"
    rm -f "$BACKUP_FILE"
    systemctl start urbit-$SHIP_NAME
    exit 1
fi

# Step 6: Restart ship
echo "Restarting ship..."
systemctl start urbit-$SHIP_NAME

# Step 7: Verify ship started
sleep 10
if ! systemctl is-active --quiet urbit-$SHIP_NAME; then
    echo "ERROR: Ship failed to restart!"
    exit 1
fi

# Step 8: Cleanup old backups
echo "Cleaning up old backups (keeping last $RETENTION)..."
ls -t $BACKUP_DIR/$SHIP_NAME-*.tar.gz | tail -n +$((RETENTION + 1)) | xargs -r rm -f

# Step 9: Report
BACKUP_SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
echo "Backup completed: $BACKUP_FILE ($BACKUP_SIZE)"

# Step 10: Offsite backup (optional)
# aws s3 cp "$BACKUP_FILE" s3://urbit-backups/

echo "=== Backup Finished: $(date) ==="
```

```bash
# Make executable
chmod +x /usr/local/bin/urbit-backup.sh

# Schedule via cron (weekly: Sunday 3 AM)
sudo crontab -e
0 3 * * 0 /usr/local/bin/urbit-backup.sh
```

## Offsite Backup Options

### AWS S3

```bash
# Install AWS CLI
sudo apt install awscli -y

# Configure credentials
aws configure

# Upload backup
aws s3 cp "$BACKUP_FILE" s3://urbit-backups/$(date +%Y/%m/%d)/

# Lifecycle policy (delete >90 days)
aws s3api put-bucket-lifecycle-configuration --bucket urbit-backups --lifecycle-configuration file://lifecycle.json
```

```json
{
  "Rules": [{
    "Id": "DeleteOldBackups",
    "Status": "Enabled",
    "Prefix": "",
    "Expiration": { "Days": 90 }
  }]
}
```

### Backblaze B2 (Cost-Effective)

```bash
# Install B2 CLI
sudo pip3 install b2

# Authorize account
b2 authorize-account <application-key-id> <application-key>

# Upload
b2 upload-file urbit-backups "$BACKUP_FILE" "$(basename $BACKUP_FILE)"
```

### Rsync to Remote Server

```bash
# Sync backups to remote server
rsync -avz --delete $BACKUP_DIR/ backup-server:/backups/urbit/

# Over SSH with compression
rsync -avz -e "ssh -p 2222" $BACKUP_DIR/ user@backup-server:/backups/
```

## Recovery Procedures

### Standard Recovery (From Backup)

```bash
# 1. Stop broken ship
systemctl stop urbit-$SHIP_NAME

# 2. Move corrupted pier
mv /home/urbit/$SHIP_NAME /home/urbit/$SHIP_NAME.corrupted

# 3. Extract backup
tar xzf $BACKUP_FILE -C /home/urbit/

# 4. Verify extraction
ls -la /home/urbit/$SHIP_NAME

# 5. Set ownership
chown -R urbit:urbit /home/urbit/$SHIP_NAME
chmod 700 /home/urbit/$SHIP_NAME

# 6. Start restored ship
systemctl start urbit-$SHIP_NAME

# 7. Verify successful boot
journalctl -u urbit-$SHIP_NAME -n 50 -f

# 8. Access dojo
# Should see dojo prompt without errors
```

### Point-in-Time Recovery

```bash
# List available backups
ls -lh /backups/urbit/sampel-palnet-*.tar.gz

# Choose backup from specific date
tar xzf /backups/urbit/sampel-palnet-20250101-030000.tar.gz -C /home/urbit/
```

### Partial Recovery (Corrupted Files)

```bash
# Extract specific files from backup
tar xzf backup.tar.gz path/to/specific/file
```

## Disaster Scenarios

### Scenario 1: Hardware Failure (Total Loss)

**Recovery Steps**:
1. Provision new server (same OS version)
2. Install dependencies, Urbit binary
3. Create urbit user account
4. Download latest backup from offsite storage
5. Extract pier
6. Start ship
7. Verify all functionality

**RTO** (Recovery Time Objective): 2-4 hours
**RPO** (Recovery Point Objective): Last backup (24 hours max)

### Scenario 2: Pier Corruption

**Recovery Steps**:
1. Attempt event replay: `urbit-worker replay pier/`
2. If fails, restore from latest backup
3. If no backup, factory reset (data loss)

**RTO**: 30 minutes
**RPO**: Last backup

### Scenario 3: Accidental Deletion

**Recovery Steps**:
1. Stop any running ship processes
2. Restore from most recent backup
3. Verify data integrity

**RTO**: 15 minutes
**RPO**: Last backup

### Scenario 4: Keyfile Loss (Planet)

**Impact**: Cannot factory reset without keyfile

**Prevention**:
- **Master ticket**: Store securely offline (password manager, paper wallet)
- **Master ticket = recovery**: Can generate new keyfile via Bridge
- **NEVER store keyfile after first boot** (consumed, useless)

**Recovery**:
1. Access Bridge with master ticket
2. Perform factory reset (generates NEW keyfile)
3. Boot fresh ship with new keyfile
4. WARNING: All groups/channels/messages lost (no data recovery)

### Scenario 5: Comet Loss

**Impact**: Comet = pier (no identity recovery)

**Prevention**:
- Regular backups (only recovery method)
- Upgrade to planet for valuable comets

**Recovery**:
- Restore from backup (if available)
- If no backup: Comet permanently lost, generate new (different identity)

## GroundSeg Multi-Ship Backup

```bash
# Backup all ships in GroundSeg
#!/bin/bash
BACKUP_DIR="/backups/groundseg-$(date +%Y%m%d)"
mkdir -p $BACKUP_DIR

# Stop GroundSeg (stops all ships)
cd /opt/groundseg
docker-compose down

# Wait for shutdown
sleep 60

# Backup each pier
for pier in ./piers/*; do
    tar czf "$BACKUP_DIR/$(basename $pier).tar.gz" "$pier"
done

# Backup configuration
cp docker-compose.yml $BACKUP_DIR/
cp .env $BACKUP_DIR/

# Backup MinIO data (if using)
tar czf "$BACKUP_DIR/minio-data.tar.gz" ./minio-data

# Restart GroundSeg
docker-compose up -d

# Upload to S3
aws s3 sync $BACKUP_DIR s3://groundseg-backups/$(date +%Y%m%d)/
```

## Backup Verification

### Integrity Check

```bash
# Verify backup file integrity
tar tzf backup.tar.gz > /dev/null && echo "Backup valid" || echo "Backup corrupted"
```

### Test Restoration (Quarterly)

```bash
# Create test environment
mkdir /tmp/restore-test

# Extract backup
tar xzf backup.tar.gz -C /tmp/restore-test

# Attempt boot (dry-run)
urbit /tmp/restore-test/pier --dry-run

# Cleanup
rm -rf /tmp/restore-test
```

## Business Continuity Planning

### Documentation Requirements

1. **Recovery procedures**: Step-by-step restoration guide
2. **Contact information**: On-call admin, vendor support
3. **Access credentials**: Stored securely (password manager)
4. **Backup locations**: Where backups stored (local, offsite)
5. **RTO/RPO targets**: Defined for each failure scenario

### Maintenance Schedule

- **Daily**: Automated backup execution
- **Weekly**: Verify backup success (check logs)
- **Monthly**: Review backup size trends, cleanup old backups
- **Quarterly**: Test restoration procedure, update documentation
- **Annually**: Review disaster recovery plan, update procedures

## Best Practices Checklist

- [ ] Automated daily backups configured
- [ ] Backups stop ship before creation (never live)
- [ ] Backup verification (tar tzf) succeeds
- [ ] Offsite storage configured (S3/B2/rsync)
- [ ] 3-2-1 rule implemented
- [ ] Retention policy: 7 daily, 4 weekly, 12 monthly
- [ ] Master ticket stored securely offline
- [ ] Recovery procedures documented
- [ ] Quarterly restoration tests scheduled
- [ ] Monitoring backup success (log analysis, alerts)
- [ ] RTO/RPO targets defined
- [ ] Team trained on recovery procedures

## Common Mistakes to Avoid

1. **Backing up live pier**: Corrupts event log (always stop first)
2. **No offsite backups**: Single point of failure
3. **Untested restorations**: Backups may be invalid
4. **Storing keyfile after boot**: Security risk, useless
5. **No master ticket backup**: Cannot factory reset planet
6. **Insufficient retention**: Can't recover from discovered corruption
7. **No monitoring**: Backup failures go unnoticed
8. **Weak access controls**: Backup credentials compromised
9. **No documentation**: Recovery delayed, errors made
10. **No testing**: Recovery procedures fail when needed

## Reference

- Urbit backup guide: https://docs.urbit.org/manual/os/ship-troubleshooting
- AWS S3 CLI: https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3.html
- Backblaze B2: https://www.backblaze.com/b2/docs/

## Summary

Effective Urbit backup requires stopping ships before backup (never backup live piers), implementing 3-2-1 rule (3 copies, 2 media types, 1 offsite), automated scheduling (weekly via cron), backup verification (tar integrity check), and quarterly restoration testing. Recovery procedures vary by disaster scenario (hardware failure, pier corruption, keyfile loss, comet loss) with defined RTO/RPO targets. Master tickets must be stored securely offline for planet recovery. GroundSeg deployments require multi-ship backup orchestration. Document all procedures, monitor backup success, and test restorations regularly.
