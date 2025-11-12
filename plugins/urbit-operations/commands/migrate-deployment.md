---
name: migrate-deployment
description: Automated ship migration workflow between hosting environments (Tlon ↔ VPS ↔ Managed ↔ Self-hosting) with zero-data-loss procedures
agents:
  - managed-hosting-advisor
  - urbit-deployment-specialist
skills:
  - disaster-recovery-advanced
  - backup-disaster-recovery
  - managed-hosting-comparison
---

# Migrate Deployment

Automated workflow for migrating Urbit ships between hosting environments with comprehensive backup validation and minimal downtime.

## Migration Scenarios

1. **Tlon → VPS**: Gain full control, install custom apps
2. **VPS → Managed**: Reduce management burden
3. **Self-Hosting → VPS/Managed**: Improve uptime, reduce electricity
4. **Managed → VPS**: Need more customization
5. **VPS → VPS**: Change providers (better pricing, regions)

## Pre-Migration Checklist

Before starting migration:

- [ ] Identify source and destination environments
- [ ] Backup current pier (critical!)
- [ ] Verify destination environment readiness
- [ ] Document current configuration (DNS, SSL, ports)
- [ ] Schedule maintenance window (off-peak hours)
- [ ] Notify users of planned downtime (if applicable)
- [ ] Test backup integrity
- [ ] Verify sufficient destination resources (RAM, disk)

## Universal Migration Workflow

### Phase 1: Pre-Migration Backup

**Critical**: Never migrate without verified backup.

```bash
# On source environment:
# 1. Stop ship gracefully
systemctl stop urbit-ship  # Or: Ctrl+D in dojo

# 2. Wait for complete shutdown
sleep 30

# 3. Verify ship stopped
ps aux | grep urbit  # Should show nothing

# 4. Create timestamped backup
DATE=$(date +%Y%m%d-%H%M%S)
tar czf ship-migration-backup-$DATE.tar.gz /path/to/pier/

# 5. Verify backup integrity
tar tzf ship-migration-backup-$DATE.tar.gz > /dev/null && echo "Backup valid"

# 6. Calculate checksum
sha256sum ship-migration-backup-$DATE.tar.gz > backup-checksum.txt
```

###Phase 2: Destination Environment Preparation

**For VPS destination**:
```bash
# Provision new VPS (DigitalOcean, Linode, etc.)
# Install Urbit binary
curl -L https://urbit.org/install/linux-x86_64/latest | tar xzk --transform='s/.*/urbit/g'
sudo mv urbit /usr/local/bin/

# Create urbit user
sudo useradd -m -s /bin/bash urbit

# Configure firewall
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 34543/udp
sudo ufw enable
```

**For managed hosting destination**:
- Contact provider (Red Horizon, UrbitHost)
- Provide pier backup file
- Confirm deployment slot availability

**For self-hosting destination**:
- Install GroundSeg: `sudo wget -O - get.groundseg.app|bash`
- Prepare hardware (sufficient RAM/disk)
- Configure network (StarTram or Anchor)

### Phase 3: Pier Transfer

**To VPS**:
```bash
# Transfer backup to destination
scp ship-migration-backup-$DATE.tar.gz urbit@new-vps-ip:/home/urbit/

# Verify checksum on destination
ssh urbit@new-vps-ip
sha256sum ship-migration-backup-$DATE.tar.gz
# Compare with backup-checksum.txt

# Extract pier
tar xzf ship-migration-backup-$DATE.tar.gz -C /home/urbit/

# Set permissions
chown -R urbit:urbit /home/urbit/ship-name/
chmod 700 /home/urbit/ship-name/
```

**To managed hosting**:
- Upload backup via provider's portal OR
- Email backup link to provider support
- Provider handles extraction and deployment

**To self-hosting (GroundSeg)**:
```bash
# Transfer backup to GroundSeg host
scp ship-migration-backup-$DATE.tar.gz groundseg-server:/opt/groundseg/piers/

# Extract into GroundSeg piers directory
tar xzf ship-migration-backup-$DATE.tar.gz -C /opt/groundseg/piers/

# Add ship to GroundSeg via web UI
# Access: http://nativeplanet.local:3000
```

### Phase 4: Ship Startup and Validation

**On VPS**:
```bash
# Method 1: Direct startup (testing)
sudo -u urbit urbit /home/urbit/ship-name

# Method 2: Systemd service (production)
sudo systemctl start urbit-ship-name

# Verify startup
journalctl -u urbit-ship-name -n 50 -f
```

**On managed hosting**:
- Wait for provider confirmation
- Test access via provider's URL

**On self-hosting**:
- Start ship via GroundSeg web UI
- Monitor container logs: `docker logs -f ship-name`

### Phase 5: Networking Reconfiguration

**Update DNS** (if using custom domain):
```bash
# Point domain to new IP
# A record: ship.yourdomain.com → new-ip-address
# Wait for DNS propagation (5-60 minutes)
```

**Configure SSL** (VPS only):
```bash
# Install Nginx and Certbot
sudo apt install nginx certbot python3-certbot-nginx -y

# Obtain SSL certificate
sudo certbot --nginx -d ship.yourdomain.com

# Verify HTTPS access
curl https://ship.yourdomain.com
```

**Test Ames connectivity**:
```
# In dojo:
|hi ~zod
# Should receive response within ~1 minute
```

### Phase 6: Validation and Testing

**Functional tests**:
- [ ] Dojo accessible (direct or via SSH)
- [ ] Web interface loads (Landscape/Grid)
- [ ] Ames connectivity working (`|hi ~zod`)
- [ ] Groups/channels accessible
- [ ] Apps functional (test each installed app)
- [ ] Subscriptions active (check for updates)
- [ ] Messages sending/receiving
- [ ] Files/media accessible (if using S3)

**Performance validation**:
```bash
# Check pier integrity
# In dojo:
|mass  # Memory usage
+vats  # Active apps

# System resources
htop  # CPU/RAM
df -h  # Disk space
```

### Phase 7: Decommission Source Environment

**Only after 24-48 hours of stable operation!**

```bash
# Source VPS:
sudo systemctl stop urbit-ship-name
sudo systemctl disable urbit-ship-name

# Keep backup for 30 days before deletion
# Archive final backup offsite (S3, B2)

# Cancel VPS subscription (after 30-day retention)
```

**Source managed hosting**:
- Cancel subscription
- Request final pier export (optional)
- Maintain backup for disaster recovery

**Source self-hosting**:
- Keep local ship stopped
- Archive pier as disaster recovery backup
- Repurpose hardware (or keep as backup host)

## Provider-Specific Migration Guides

### From Tlon Hosting

```bash
# 1. Email support@tlon.io requesting pier export
# Subject: "Pier Export Request - ~ship-name"
# Body: "Please provide a pier backup for migration to self-hosting."

# 2. Await pier backup (1-5 business days)

# 3. Download backup file from provided link

# 4. Verify integrity (check file size reasonable)

# 5. Follow universal workflow above
```

**Note**: Tlon does not guarantee pier exports; request early.

### To Tlon Hosting

```bash
# 1. Sign up for Tlon waitlist (tlon.io)

# 2. Create pier backup on current host

# 3. Email support@tlon.io: "Import Existing Pier - ~ship-name"
# Attach pier backup (if <50MB) or provide download link

# 4. Await confirmation (may take 1-2 weeks)

# 5. Access via Tlon mobile apps or web interface
```

### Between VPS Providers

```bash
# Same workflow as Phase 1-7 above
# Fastest migration (full control of both environments)
# Typical downtime: 1-2 hours
```

### From GroundSeg to VPS

```bash
# 1. Stop ship in GroundSeg web UI

# 2. Backup from GroundSeg host
tar czf ship-backup.tar.gz /opt/groundseg/piers/ship-name/

# 3. Transfer to VPS (via scp)

# 4. Extract and start on VPS

# 5. Update DNS if using custom domain

# 6. Test functionality

# 7. Keep GroundSeg ship stopped (backup)
```

## Rollback Procedures

**If migration fails**:

```bash
# 1. Stop ship on destination
systemctl stop urbit-ship-name  # Or provider equivalent

# 2. Restart ship on source environment
systemctl start urbit-ship-name

# 3. Verify source functionality

# 4. Investigate destination issues

# 5. Retry migration after fixing issues
```

## Downtime Estimates

| Migration Path | Typical Downtime | Complexity |
|----------------|------------------|------------|
| VPS → VPS | 1-2 hours | Low |
| Self-Hosting → VPS | 2-4 hours | Low-Medium |
| VPS → Managed | 4-24 hours | Low (provider handles) |
| Managed → VPS | 1-5 days | Medium (depends on export) |
| Tlon → VPS | 1-7 days | Medium (export delay) |
| GroundSeg → VPS | 2-4 hours | Low |

## Best Practices

1. **Backup before everything**: Test backup validity before migration
2. **Off-peak hours**: Migrate during low-usage times (Sunday 2-6 AM)
3. **Notify users**: If running community ship, announce downtime
4. **Keep source running**: Don't decommission until destination stable (24-48h)
5. **Document configuration**: DNS, SSL, custom apps, settings
6. **Test rollback**: Ensure you can revert if issues arise
7. **Staged migration**: Test on fake ship first with same procedure
8. **Monitor closely**: Watch logs for 24 hours post-migration
9. **Verify subscriptions**: Some may need manual re-subscription
10. **Update bookmarks**: New URLs, IP addresses, connection details

## Troubleshooting

**Pier won't boot on destination**:
- Check Urbit version compatibility
- Verify sufficient RAM (4GB+ for most ships)
- Review logs: `journalctl -u urbit-ship -n 100`
- Try replay: `urbit-worker replay pier/`

**Networking issues**:
- Verify firewall allows Ames (34543/udp)
- Test with `|hi ~zod` (may take 10-15 min to propagate)
- Check DNS propagation: `nslookup ship.yourdomain.com`

**Apps not working**:
- Run |pack in dojo
- Reinstall app: `|install ~app-provider %app-name`
- Check app-specific configuration

**Performance degradation**:
- Verify destination has equal/better resources than source
- Check SSD vs HDD (must use SSD)
- Run |pack (defragmentation)
- Monitor with `htop`, `iostat`

## Cross-References

- **backup-disaster-recovery**: Comprehensive backup strategies
- **managed-hosting-comparison**: Detailed provider comparisons
- **deploy-planet**: New bare-metal deployment
- **deploy-vps-planet**: New VPS deployment
- **troubleshoot-ship**: Diagnostic procedures

## Summary

Ship migration between hosting environments follows universal workflow: backup validation, destination preparation, pier transfer, startup validation, networking reconfiguration, functional testing, and source decommission. Critical principle: NEVER migrate without verified backup. Provider-specific considerations: Tlon exports (1-7 days), managed hosting (4-24h provider-handled), VPS-to-VPS (1-2h fastest). Best practices: migrate off-peak, maintain source 24-48h post-migration, test rollback procedures, verify subscriptions and apps post-migration. Downtime varies: VPS-to-VPS (1-2h) to Tlon export delays (1-7 days).
