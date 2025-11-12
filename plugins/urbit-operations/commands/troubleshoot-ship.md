---
name: troubleshoot-ship
description: Systematic diagnostic workflows for common Urbit issues with 6 decision trees covering boot, memory, network, OTA, performance, and pier corruption
subagent_type: urbit-deployment-specialist
---

# Troubleshoot Ship Command

This command orchestrates systematic troubleshooting workflows for Urbit ships using decision-tree diagnostics. It covers the 6 most common issue categories with 2025-current solutions.

## Overview

The troubleshoot-ship command provides structured diagnostic paths for:

1. **Boot Failures**: Keyfile issues, double-booting, corrupted piers, binary mismatches
2. **Memory Issues**: "out of loom" errors, |meld and |pack optimization
3. **Network Problems**: Firewall configuration, Ames connectivity, NAT traversal
4. **OTA Update Failures**: Blocked apps, sponsor issues, hash mismatches
5. **Performance Degradation**: CPU usage, disk I/O, pier bloat, agent issues
6. **Pier Corruption**: Event log damage, restoration procedures, factory reset

## Configuration Options

Before running diagnostics, gather:

```yaml
Ship Information:
  ship_name: "~sampel-palnet"  # Ship patp
  ship_type: "planet"  # planet | comet | fake
  pier_path: "/home/user/urbit/sampel-palnet"
  deployment_type: "bare-metal"  # bare-metal | groundseg | vps

Issue Category:
  category: "boot-failure"  # boot-failure | memory | network | ota | performance | pier-corruption
  symptoms: "Ship crashes immediately with 'boot: invalid keyfile' error"

System Context:
  os: "Ubuntu 22.04"
  urbit_version: "v3.2"  # From urbit --version
  uptime_before_issue: "3 days"  # How long was ship running before issue
  recent_changes: "Attempted to restart ship after system reboot"
```

## Diagnostic Decision Trees

### 1. Boot Failure Diagnostics

**Invocation**: When ship fails to start or crashes during boot

**Decision Tree**:

```
┌─ START: Ship Won't Boot ─┐
│                           │
└───────────┬───────────────┘
            │
            ▼
    ┌───────────────┐
    │ Is this the   │
    │ FIRST boot of │ ──YES──┐
    │ this ship?    │        │
    └───────┬───────┘        │
            │                │
           NO                │
            │                ▼
            │         ┌──────────────┐
            │         │ Keyfile      │
            │         │ Validation   │
            │         └──────┬───────┘
            │                │
            │                ├─► File format check: head -n 1 keyfile.key (should start with '0w')
            │                ├─► Permissions check: ls -lh keyfile.key
            │                ├─► Correct ship match: Verify keyfile name matches ship patp
            │                └─► Binary compatibility: urbit --version
            │
            ▼
    ┌───────────────┐
    │ Have you      │
    │ booted this   │ ──YES──┐
    │ ship before?  │        │
    └───────┬───────┘        │
            │                │
           NO                ▼
            │         ┌──────────────┐
            │         │ KEYFILE      │
            │         │ CONSUMED!    │
            │         └──────┬───────┘
            │                │
            │                ├─► Keyfiles are SINGLE-USE ONLY
            │                ├─► After first successful boot, keyfile is consumed
            │                ├─► Solution: Start WITHOUT keyfile
            │                │   urbit /path/to/pier/  # Just pier path, NO keyfile
            │                └─► NEVER reuse keyfile (causes network ban)
            │
            ▼
    ┌───────────────┐
    │ Check Error   │
    │ Message Type  │
    └───────┬───────┘
            │
            ├─► "boot: invalid keyfile"
            │   └─► Wrong keyfile or corrupted
            │       ├─► Re-download from Bridge (bridge.urbit.org)
            │       ├─► Verify file integrity
            │       └─► chmod 644 keyfile.key
            │
            ├─► "boot: failed to load pier"
            │   └─► Corrupted pier directory
            │       ├─► Check disk space: df -h
            │       ├─► Check file permissions: ls -la pier/
            │       ├─► Restore from backup (if available)
            │       └─► Consider factory reset (last resort)
            │
            ├─► "bail: meme" or "out of loom"
            │   └─► Memory exhaustion (see Memory Diagnostics)
            │
            └─► "version mismatch"
                └─► Binary version incompatible with pier
                    ├─► Check pier version: cat pier/.urb/put/version
                    ├─► Update binary to matching version
                    └─► Or use: urbit next pier/ (auto-upgrade)
```

**Common Resolutions**:

1. **Already booted once**: `urbit /path/to/pier/` (no keyfile)
2. **Wrong keyfile**: Re-download from Bridge
3. **Corrupted keyfile**: Verify integrity, re-download
4. **Permission issue**: `chmod 644 keyfile.key`, `chmod 700 pier/`
5. **Binary mismatch**: Update Urbit binary:
   ```bash
   curl -L https://urbit.org/install/linux-x86_64/latest | tar xzk --transform='s/.*/urbit/g'
   sudo mv urbit /usr/local/bin/
   ```

---

### 2. Memory Issue Diagnostics

**Invocation**: "out of loom" errors, "bail: meme", ship crashes due to memory

**Decision Tree**:

```
┌─ START: Memory Problems ─┐
│                           │
└───────────┬───────────────┘
            │
            ▼
    ┌───────────────┐
    │ Check Error   │
    │ Message       │
    └───────┬───────┘
            │
            ├─► "out of loom" - Ship hit 2GB loom limit
            │   └─► Loom is Urbit's memory model (2GB max)
            │       ├─► Cannot increase loom size (architectural limit)
            │       └─► Must reduce ship state size (proceed below)
            │
            └─► "bail: meme" - General memory error
                └─► System RAM exhaustion or loom full
                    └─► Check both system RAM and ship state
```

**Resolution Steps**:

**Step 1: Check Current Memory Usage**

```bash
# Check system RAM
free -h

# Check ship state size
du -sh /path/to/pier/

# Inside dojo: Check memory breakdown
|mass
```

**Step 2: State Reduction (Choose ONE)**

**Option A: |pack (Fast Defragmentation)**
```
# In dojo:
|pack

# What it does:
# - Defragments ship state (like disk defrag)
# - Runs quickly (minutes)
# - Moderate state reduction (10-30%)
# - Low memory overhead during execution
# - Safe for production
```

**Option B: |meld (Aggressive Deduplication)**
```
# In dojo:
|meld

# What it does:
# - Deduplicates ship state (removes redundant data)
# - Runs SLOWLY (can take hours for large ships)
# - Significant state reduction (30-70%)
# - HIGH memory overhead (can use 8GB+ RAM during execution)
# - May cause temporary unresponsiveness
# - Best run during maintenance window

# IMPORTANT:
# - Ensure 8GB+ system RAM available
# - Run during low-traffic period
# - Monitor with: watch -n 5 'du -sh /path/to/pier/'
```

**Step 3: Prevent Future Issues**

```bash
# Schedule regular maintenance
# Add to crontab for weekly |pack:

# Create maintenance script
cat > /usr/local/bin/urbit-maintenance.sh << 'EOF'
#!/bin/bash
# Weekly Urbit maintenance

# Connect to dojo and run |pack
# (Requires dojo automation or manual execution)
echo "Run |pack in dojo weekly"
EOF

chmod +x /usr/local/bin/urbit-maintenance.sh
```

**Step 4: If Still Out of Loom After |meld**

```
# Check which apps are using most memory
+vats  # Lists all running agents with memory usage

# Suspend large/unused apps
|suspend %app-name

# Consider factory reset (LAST RESORT - loses all data)
# Only if ship is completely unusable and no backups
```

**Success Criteria**:
- [ ] Ship boots successfully
- [ ] |mass shows <1.8GB usage (safe margin below 2GB limit)
- [ ] No "out of loom" errors for 24 hours
- [ ] Regular |pack scheduled (weekly)

---

### 3. Network Connectivity Diagnostics

**Invocation**: Cannot access ship via HTTPS, Ames connectivity issues, ship shows as offline

**Decision Tree**:

```
┌─ START: Network Issues ─┐
│                          │
└───────────┬──────────────┘
            │
            ▼
    ┌───────────────┐
    │ What is not   │
    │ working?      │
    └───────┬───────┘
            │
            ├─► Cannot access ship via web (HTTPS)
            │   └─► HTTP/HTTPS Configuration Issue
            │       │
            │       ├─► Step 1: Check ship HTTP server
            │       │   └─► In dojo: +code  # Should show web login code
            │       │   └─► Check ship HTTP port (usually 8080 or 80)
            │       │   └─► Try local access: http://localhost:8080
            │       │
            │       ├─► Step 2: Check firewall (UFW)
            │       │   └─► sudo ufw status verbose
            │       │   └─► Should show: 80/tcp ALLOW, 443/tcp ALLOW
            │       │   └─► Add if missing:
            │       │       sudo ufw allow 80/tcp
            │       │       sudo ufw allow 443/tcp
            │       │
            │       ├─► Step 3: Check SSL certificate
            │       │   └─► For arvo.network:
            │       │       └─► In dojo: :acme|check
            │       │   └─► For custom domain:
            │       │       └─► sudo certbot certificates
            │       │       └─► Check nginx config: sudo nginx -t
            │       │
            │       └─► Step 4: Check DNS
            │           └─► nslookup your-domain.com
            │           └─► Should resolve to server public IP
            │           └─► Check public IP: curl ifconfig.me
            │
            └─► Ship shows as offline to other ships (Ames)
                └─► Ames Networking Issue (UDP port 34543)
                    │
                    ├─► Step 1: Check Ames firewall
                    │   └─► sudo ufw status verbose
                    │   └─► Should show: 34543/udp ALLOW
                    │   └─► Add if missing:
                    │       sudo ufw allow 34543/udp
                    │
                    ├─► Step 2: Check NAT/Port Forwarding (if behind router)
                    │   └─► Router must forward UDP 34543 to ship server
                    │   └─► Configure in router admin panel
                    │   └─► Test with: nc -u -l 34543 (on server)
                    │   └─► Send test packet from external network
                    │
                    ├─► Step 3: Test Ames connectivity
                    │   └─► In dojo: |hi ~zod  # Ping ~zod (network bootstrap)
                    │   └─► Should see: "ack" response
                    │   └─► If no response, Ames not working
                    │
                    └─► Step 4: Check Ames network configuration
                        └─► In dojo: +trouble  # Network diagnostics
                        └─► Restart networking: :azimuth-tracker|kick ~zod
                        └─► Force Ames reset (last resort):
                            |bonk  # Resets Ames networking
```

**Common Network Resolutions**:

1. **HTTPS not working**:
   ```bash
   # Check local access first
   curl http://localhost:8080

   # Check firewall
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp

   # Check nginx (if using reverse proxy)
   sudo systemctl status nginx
   sudo nginx -t
   ```

2. **Ames connectivity (ship-to-ship)**:
   ```bash
   # Open UDP port 34543
   sudo ufw allow 34543/udp

   # Verify router port forwarding (if behind NAT)
   # Configure router to forward UDP 34543 to server IP

   # Test in dojo
   |hi ~zod  # Should get "ack" response
   ```

3. **DNS not resolving**:
   ```bash
   # Check DNS propagation
   nslookup your-ship.arvo.network

   # If custom domain, verify A record points to:
   curl ifconfig.me  # Your server's public IP
   ```

**Success Criteria**:
- [ ] Ship accessible via HTTPS from external network
- [ ] SSL certificate valid (check at https://your-domain)
- [ ] |hi ~zod returns "ack" (Ames working)
- [ ] +code shows web login code
- [ ] Firewall allows: 80/tcp, 443/tcp, 34543/udp

---

### 4. OTA Update Failure Diagnostics

**Invocation**: Ship stuck on old version, OTA updates not applying, update hash mismatches

**Decision Tree**:

```
┌─ START: OTA Not Working ─┐
│                           │
└───────────┬───────────────┘
            │
            ▼
    ┌───────────────┐
    │ What is the   │
    │ symptom?      │
    └───────┬───────┘
            │
            ├─► "OTA update blocked by app"
            │   └─► App preventing update installation
            │       │
            │       ├─► Step 1: Identify blocking app
            │       │   └─► Check error message (usually shows app name)
            │       │   └─► In dojo: +vats  # List running agents
            │       │
            │       └─► Step 2: Suspend blocking app
            │           └─► In dojo: |bump  # Suspends blocking apps
            │           └─► Or manually: |suspend %app-name
            │           └─► Wait for OTA to complete
            │           └─► Resume: |revive %app-name
            │
            ├─► "cannot reach sponsor"
            │   └─► Network connectivity to OTA sponsor lost
            │       │
            │       ├─► Step 1: Check Ames connectivity (see Network Diagnostics)
            │       │   └─► In dojo: |hi ~sponsor-ship
            │       │
            │       ├─► Step 2: Verify sponsor is online
            │       │   └─► Default sponsor: ~zod or star that spawned you
            │       │   └─► Check with: +trouble
            │       │
            │       └─► Step 3: Change sponsor (if current unreliable)
            │           └─► In dojo: |ota ~litzod  # Use ~litzod as sponsor
            │           └─► Or use your galaxy's other stars
            │           └─► Wait for reconnection
            │
            └─► "hash mismatch" or "update corrupted"
                └─► Update files corrupted during transfer
                    │
                    ├─► Step 1: Clear OTA cache
                    │   └─► Stop ship gracefully (ctrl+d in dojo)
                    │   └─► rm -rf pier/.urb/put/*.jam  # Clear cached updates
                    │   └─► Restart ship
                    │
                    ├─► Step 2: Force OTA retry
                    │   └─► In dojo: |ota ~sponsor-ship %force
                    │   └─► Or: |install our %base  # Force base system update
                    │
                    └─► Step 3: Manual update (last resort)
                        └─► Check Urbit binary version: urbit --version
                        └─► Update binary if outdated:
                            curl -L https://urbit.org/install/linux-x86_64/latest | tar xzk --transform='s/.*/urbit/g'
                            sudo mv urbit /usr/local/bin/
```

**OTA Resolution Workflow**:

1. **Blocked by App**:
   ```
   # In dojo:
   |bump  # Auto-suspends blocking apps

   # Wait 5-10 minutes for OTA to complete
   # Check status: |ota-status (if available)

   # Apps resume automatically after OTA
   ```

2. **Sponsor Unreachable**:
   ```
   # Test connectivity
   |hi ~sponsor-patp

   # If no response, change sponsor
   |ota ~litzod  # Alternate OTA source

   # Or use your galaxy's infrastructure star
   ```

3. **Corrupted Updates**:
   ```bash
   # Stop ship
   # In dojo: ctrl+d (graceful shutdown)

   # Clear OTA cache
   rm -rf /path/to/pier/.urb/put/*.jam

   # Restart
   urbit /path/to/pier/

   # Force fresh OTA
   |install our %base
   ```

**Success Criteria**:
- [ ] Ship running latest kelvin version
- [ ] No blocked OTA messages in logs
- [ ] |ota-status shows "up to date" (if available)
- [ ] All apps running normally after update
- [ ] No hash mismatch errors

---

### 5. Performance Degradation Diagnostics

**Invocation**: Ship running slowly, high CPU usage, slow web interface, laggy dojo

**Decision Tree**:

```
┌─ START: Performance Issues ─┐
│                              │
└───────────┬──────────────────┘
            │
            ▼
    ┌───────────────┐
    │ Identify      │
    │ Bottleneck    │
    └───────┬───────┘
            │
            ├─► High CPU Usage
            │   └─► Ship consuming excessive CPU
            │       │
            │       ├─► Step 1: Check CPU usage
            │       │   └─► top -p $(pgrep urbit)  # Monitor Urbit CPU
            │       │   └─► If >80% sustained, investigate
            │       │
            │       ├─► Step 2: Identify heavy agents
            │       │   └─► In dojo: +vats  # Show agent CPU/memory
            │       │   └─► Suspend heavy agents: |suspend %app-name
            │       │
            │       └─► Step 3: System-level checks
            │           └─► iostat -x 5  # Check if CPU waiting on I/O
            │           └─► If high iowait%, disk is bottleneck (see Disk I/O)
            │
            ├─► Slow Disk I/O
            │   └─► Pier read/write operations slow
            │       │
            │       ├─► Step 1: Check disk performance
            │       │   └─► iostat -x 5  # Check %util and await
            │       │   └─► If %util >80% or await >50ms, disk saturated
            │       │
            │       ├─► Step 2: Verify disk type
            │       │   └─► lsblk -d -o name,rota
            │       │   └─► rota=1 means HDD (SLOW - should use SSD)
            │       │   └─► rota=0 means SSD (good)
            │       │
            │       ├─► Step 3: Check disk space
            │       │   └─► df -h /path/to/pier
            │       │   └─► If >85% full, performance degrades
            │       │   └─► Run |pack or |meld to reduce state
            │       │
            │       └─► Step 4: I/O scheduler optimization
            │           └─► For SSD:
            │               echo "none" | sudo tee /sys/block/sdX/queue/scheduler
            │           └─► For HDD:
            │               echo "mq-deadline" | sudo tee /sys/block/sdX/queue/scheduler
            │
            ├─► Pier Bloat (Large State)
            │   └─► Pier directory growing excessively large
            │       │
            │       ├─► Step 1: Check pier size
            │       │   └─► du -sh pier/
            │       │   └─► Check subdirectories: du -h pier/ | sort -h | tail -20
            │       │
            │       ├─► Step 2: Run state reduction
            │       │   └─► |pack  # Quick defragmentation
            │       │   └─► Or |meld  # Aggressive deduplication (slow)
            │       │
            │       └─► Step 3: Clean logs
            │           └─► Safe to remove old logs:
            │               rm -f pier/.urb/log/*  # Old runtime logs
            │           └─► Event log is immutable (DO NOT DELETE)
            │
            └─► Slow Web Interface
                └─► Landscape/web UI loading slowly
                    │
                    ├─► Step 1: Check network latency
                    │   └─► ping your-ship-domain.com
                    │   └─► High latency? Network issue
                    │
                    ├─► Step 2: Check SSL/reverse proxy
                    │   └─► sudo systemctl status nginx
                    │   └─► Check nginx error log: /var/log/nginx/error.log
                    │
                    └─► Step 3: Check ship HTTP server
                        └─► In dojo: +code  # Verify HTTP working
                        └─► Restart eyre (HTTP vane): |reset-cache
```

**Performance Optimization Actions**:

1. **CPU Optimization**:
   ```
   # Suspend heavy agents
   +vats  # Identify CPU-heavy apps
   |suspend %bitcoin  # Example: suspend heavy app

   # Monitor improvement
   top -p $(pgrep urbit)
   ```

2. **Disk I/O Optimization**:
   ```bash
   # For SSD (disable I/O scheduler)
   echo "none" | sudo tee /sys/block/sda/queue/scheduler

   # For HDD (use mq-deadline)
   echo "mq-deadline" | sudo tee /sys/block/sda/queue/scheduler

   # Increase read-ahead (helps sequential reads)
   sudo blockdev --setra 8192 /dev/sda
   ```

3. **State Reduction**:
   ```
   # Quick fix (5-15 minutes)
   |pack

   # Deep cleanup (1-4 hours, requires 8GB RAM)
   |meld
   ```

4. **Monitoring Setup**:
   ```bash
   # Monitor CPU, RAM, disk
   watch -n 5 'top -b -n 1 | grep urbit; free -h; iostat -x 1 1'
   ```

**Success Criteria**:
- [ ] CPU usage <50% sustained
- [ ] Disk iowait <10%
- [ ] Pier size stable or decreasing
- [ ] Web interface responsive (<2s page loads)
- [ ] Dojo commands execute quickly

---

### 6. Pier Corruption Diagnostics

**Invocation**: Event log errors, ship crashes unpredictably, data loss, recovery needed

**Decision Tree**:

```
┌─ START: Pier Corruption ─┐
│                           │
└───────────┬───────────────┘
            │
            ▼
    ┌───────────────┐
    │ Assess        │
    │ Severity      │
    └───────┬───────┘
            │
            ├─► Ship boots but behaves erratically
            │   └─► Mild corruption (recoverable)
            │       │
            │       ├─► Step 1: Verify event log integrity
            │       │   └─► Check for errors in: journalctl -u urbit-ship
            │       │   └─► Look for: "crud" events, "bail" errors
            │       │
            │       ├─► Step 2: Run |meld
            │       │   └─► Can fix minor state inconsistencies
            │       │   └─► In dojo: |meld
            │       │   └─► Wait for completion (may take hours)
            │       │
            │       └─► Step 3: Validate ship health
            │           └─► |mass  # Check memory layout
            │           └─► +vats  # Check agents running
            │           └─► Test web access: +code
            │
            ├─► Ship won't boot, crashes on startup
            │   └─► Severe corruption (restoration needed)
            │       │
            │       ├─► Step 1: Attempt replay (if partial event log damage)
            │       │   └─► urbit-worker replay pier/  # Replay events
            │       │   └─► If fails, event log severely damaged
            │       │
            │       ├─► Step 2: Restore from backup
            │       │   └─► CRITICAL: Use backup taken while ship was STOPPED
            │       │   └─► Backups of running ships are CORRUPTED
            │       │   └─► Safe restoration:
            │       │       # Stop current (broken) ship
            │       │       mv broken-pier broken-pier.backup
            │       │       # Extract backup
            │       │       tar xzf ship-backup-YYYY-MM-DD.tar.gz
            │       │       # Start restored ship
            │       │       urbit restored-pier/
            │       │
            │       └─► Step 3: If no backup available
            │           └─► Factory reset (LAST RESORT - loses ALL data)
            │           └─► Only for planets (can re-boot from keyfile)
            │           └─► Comets: Lost forever (identity = pier)
            │
            └─► Need to prevent future corruption
                └─► Implement safe backup procedures
                    │
                    ├─► Safe Backup Workflow:
                    │   1. In dojo: ctrl+d (graceful shutdown)
                    │   2. Wait 30 seconds (ensure process stopped)
                    │   3. Verify stopped: ps aux | grep urbit
                    │   4. Backup: tar czf ship-$(date +%Y%m%d).tar.gz pier/
                    │   5. Restart: urbit pier/
                    │
                    ├─► Automated Backup Script:
                    │   └─► Create /usr/local/bin/backup-urbit.sh:
                    │       #!/bin/bash
                    │       # Safe Urbit backup
                    │       PIER="/home/user/urbit/sampel-palnet"
                    │       BACKUP_DIR="/backups/urbit"
                    │       DATE=$(date +%Y%m%d-%H%M)
                    │
                    │       # Stop ship (requires systemd)
                    │       systemctl stop urbit-sampel-palnet
                    │       sleep 30
                    │
                    │       # Backup
                    │       tar czf "$BACKUP_DIR/sampel-palnet-$DATE.tar.gz" "$PIER"
                    │
                    │       # Restart
                    │       systemctl start urbit-sampel-palnet
                    │
                    │       # Keep last 7 backups
                    │       ls -t $BACKUP_DIR/*.tar.gz | tail -n +8 | xargs rm -f
                    │
                    └─► Schedule weekly backups (crontab):
                        # Backup every Sunday at 3 AM
                        0 3 * * 0 /usr/local/bin/backup-urbit.sh
```

**Corruption Prevention Best Practices**:

1. **NEVER Backup Live Piers**:
   ```bash
   # WRONG (will create corrupted backup):
   tar czf backup.tar.gz pier/  # While ship is RUNNING

   # CORRECT (safe backup):
   # In dojo: ctrl+d (stop ship)
   sleep 30  # Wait for process to stop
   tar czf backup.tar.gz pier/
   urbit pier/  # Restart
   ```

2. **Graceful Shutdown Always**:
   ```
   # In dojo (preferred):
   ctrl+d

   # Via systemd:
   sudo systemctl stop urbit-ship

   # NEVER:
   kill -9 $(pgrep urbit)  # Can corrupt event log
   ```

3. **Regular Backup Schedule**:
   ```bash
   # Weekly automated backups
   # Add to crontab: crontab -e
   0 3 * * 0 /usr/local/bin/backup-urbit.sh
   ```

4. **Backup Verification**:
   ```bash
   # Test backup integrity
   tar tzf backup.tar.gz > /dev/null
   echo $?  # Should return 0 (success)

   # Test restoration (quarterly)
   mkdir /tmp/restore-test
   tar xzf backup.tar.gz -C /tmp/restore-test
   urbit /tmp/restore-test/pier/ --dry-run  # Test boot
   ```

**Factory Reset (Last Resort)**:

**For Planets** (can re-boot with keyfile from Bridge):
```bash
# 1. Obtain fresh keyfile from Bridge (bridge.urbit.org)
# 2. CRITICAL: Must perform factory reset on Bridge first
#    (generates new keyfile, invalidates old identity)
# 3. Delete old pier
rm -rf /path/to/old-pier/
# 4. Boot with new keyfile
urbit -w new-ship-name -k new-keyfile.key
```

**For Comets** (identity = pier, cannot re-boot):
```bash
# Comets cannot be factory reset
# If pier corrupted beyond repair:
# 1. Delete old comet: rm -rf old-comet/
# 2. Generate NEW comet (different identity):
urbit -c new-comet
# NOTE: All groups, channels, data from old comet LOST
```

**Success Criteria**:
- [ ] Ship boots successfully
- [ ] Event log replays without errors
- [ ] All agents functioning: +vats
- [ ] Web access working: +code
- [ ] No crash for 24+ hours
- [ ] Automated backups scheduled
- [ ] Backup restoration tested

---

## Integration with urbit-deployment-specialist

This command is orchestrated by the urbit-deployment-specialist agent, which provides:

- Deep Urbit architecture knowledge (event log structure, Arvo vanes, loom memory model)
- Systematic diagnostic discipline (decision trees over random guessing)
- Production-hardening context (how issues relate to deployment configuration)
- Cross-reference to urbit-troubleshooting skill for additional decision trees

## Integration with Skills

Relies on:

- **urbit-troubleshooting**: Extended diagnostic procedures and edge cases
- **urbit-fundamentals**: Understanding Urbit internals (Nock, Hoon, Arvo, Vere, loom)
- **performance-optimization**: System-level tuning (I/O scheduler, kernel params, filesystem)
- **backup-disaster-recovery**: Safe backup procedures, restoration workflows

## Success Criteria (Overall)

After completing troubleshoot-ship workflow:

- [ ] Root cause identified via systematic decision tree
- [ ] Issue resolved or escalation path defined
- [ ] Documentation updated with resolution steps
- [ ] Preventive measures implemented (monitoring, automation)
- [ ] No recurrence for 7+ days
- [ ] Runbook created for future similar issues

## Operational Philosophy

**"Check logs first, use decision trees not guesses, verify one variable at a time, document everything."**

Troubleshooting is not about luck—it's about systematic elimination of variables, disciplined diagnostics, and building institutional knowledge through runbooks. Every resolved issue should create documentation for the next operator.
