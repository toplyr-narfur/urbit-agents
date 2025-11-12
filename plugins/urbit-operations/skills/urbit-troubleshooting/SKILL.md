---
name: urbit-troubleshooting
description: Systematic diagnostic procedures and decision trees for resolving Urbit issues including boot failures, network problems, performance degradation, OTA update failures, and pier corruption. Use when diagnosing ship problems, resolving errors, investigating performance issues, or implementing systematic troubleshooting workflows.
---

# Urbit Troubleshooting Skill

Systematic diagnostic procedures and decision trees for resolving common Urbit issues in production environments.

## Overview

This skill provides structured troubleshooting methodologies based on symptom analysis, diagnostic decision trees, and proven resolution patterns. Emphasis on systematic problem-solving over random guessing.

## Core Diagnostic Approach

1. **Identify symptoms**: Collect error messages, logs, system state
2. **Use decision trees**: Follow structured diagnostic paths
3. **Verify one variable**: Change one thing at a time
4. **Document solutions**: Build runbooks for future reference
5. **Implement prevention**: Address root causes, not just symptoms

## 1. Boot Failures

### Symptoms
- Ship won't start
- Crashes immediately on boot
- "boot: invalid keyfile" error
- "bail: meme" error

### Diagnostic Decision Tree

**Q1: Is this the first boot of this planet?**
- Yes → Keyfile validation (check format, permissions, correct ship)
- No → Q2

**Q2: Have you booted this ship before successfully?**
- Yes → Keyfile consumed (boot without keyfile: `urbit pier/`)
- No → Q3

**Q3: Check error message type:**
- "boot: invalid keyfile" → Wrong/corrupted keyfile (re-download from Bridge)
- "boot: failed to load pier" → Corrupted pier (restore from backup)
- "bail: meme" → Memory exhaustion (see Memory Issues)
- "version mismatch" → Update Urbit binary

### Common Resolutions

```bash
# Already booted once (most common)
urbit /path/to/pier  # No keyfile needed

# Keyfile issues
# Re-download from Bridge
chmod 644 keyfile.key  # Fix permissions

# Binary version mismatch
curl -L https://urbit.org/install/linux-x86_64/latest | tar xzk --transform='s/.*/urbit/g'
sudo mv urbit /usr/local/bin/

# Check logs for details
journalctl -u urbit-ship -n 100
```

## 2. Memory Issues

### Symptoms
- "out of loom" error
- "bail: meme" error
- Ship crashes randomly
- Slow performance

### Diagnostic Tools

```bash
# Check pier size
du -sh /path/to/pier

# Check system RAM
free -h

# In dojo - check memory breakdown
|mass
```

### Resolution Steps

**Quick fix (|pack)**:
```
# In dojo
|pack  # Defragmentation (10-30% reduction, fast)
```

**Thorough fix (|meld)**:
```
# In dojo (requires 8GB+ RAM)
|meld  # Deduplication (30-70% reduction, slow - hours)

# Monitor progress
# In terminal: watch -n 5 'du -sh /path/to/pier'
```

**Increase loom** (if system has RAM):
```bash
# Stop ship
# Restart with larger loom
urbit --loom 32 /path/to/pier  # 4GB loom

# IMPORTANT: Must use --loom 32 on every subsequent boot
```

### Prevention
- Run |pack weekly (automate via cron)
- Monitor pier size growth
- Plan loom size based on usage (4GB for heavy use)

## 3. Network Connectivity

### Symptoms
- Cannot access ship via HTTPS
- Ship shows offline to other ships
- "cannot reach sponsor" error
- |hi ~zod returns no ack

### Diagnostic Decision Tree

**HTTP/HTTPS issues:**
```bash
# Check ship HTTP server
curl http://localhost:8080

# Check firewall
sudo ufw status | grep -E '(80|443)'

# Check SSL certificate
sudo certbot certificates

# Check DNS
nslookup your-ship-domain.com

# Check Nginx (if using reverse proxy)
sudo systemctl status nginx
sudo nginx -t
```

**Ames issues (ship-to-ship):**
```bash
# Check Ames firewall
sudo ufw status | grep 34543

# Test connectivity (in dojo)
|hi ~zod  # Should get "ack"

# Check router port forwarding (if NAT)
# UDP 34543 → ship server IP

# Check sponsor connectivity
+trouble  # Network diagnostics
```

### Common Resolutions

```bash
# Firewall blocking Ames
sudo ufw allow 34543/udp

# SSL certificate expired
sudo certbot renew
sudo systemctl restart nginx

# DNS not resolving
# Verify A record points to server IP

# Sponsor unreachable (in dojo)
|ota ~litzod  # Change sponsor
```

## 4. OTA Update Failures

### Symptoms
- "OTA update blocked by app"
- "cannot reach sponsor"
- "hash mismatch" error
- Ship stuck on old version

### Diagnostic Steps

**Check blocking apps:**
```
# In dojo
+vats  # List running agents
```

**Test sponsor connectivity:**
```
# In dojo
|hi ~sponsor-ship
```

### Resolution Workflow

**Blocked by app:**
```
# In dojo
|bump  # Suspends blocking apps
# Wait 5-10 minutes for OTA to complete
```

**Sponsor unreachable:**
```
# In dojo
|hi ~sponsor-patp  # Test connectivity

# Change sponsor if needed
|ota ~litzod  # Use alternate OTA source
```

**Corrupted updates:**
```bash
# Stop ship (ctrl+d in dojo)

# Clear OTA cache
rm -rf pier/.urb/put/*.jam

# Restart ship
urbit pier/

# Force fresh OTA (in dojo)
|install our %base
```

## 5. Performance Degradation

### Symptoms
- Slow dojo responses
- Laggy web interface
- High CPU usage
- High disk I/O

### Diagnostic Tools

```bash
# Check CPU usage
top -p $(pgrep urbit)

# Check disk I/O
iostat -x 5

# Check pier size
du -sh pier/

# In dojo - check agent CPU/memory
+vats
```

### Resolution by Bottleneck

**High CPU:**
```
# In dojo - suspend heavy agents
+vats  # Identify heavy apps
|suspend %app-name

# Monitor improvement
# Terminal: top -p $(pgrep urbit)
```

**Slow disk I/O:**
```bash
# Check disk type
lsblk -d -o name,rota
# rota=1 (HDD) → Upgrade to SSD

# Optimize I/O scheduler (SSD)
echo "none" | sudo tee /sys/block/sda/queue/scheduler

# Check disk space
df -h /path/to/pier
# If >85%, run |pack or |meld
```

**Pier bloat:**
```
# In dojo
|pack  # Quick defragmentation
# Or
|meld  # Aggressive deduplication (slow)
```

## 6. Pier Corruption

### Symptoms
- Event log errors
- Ship crashes unpredictably
- Data loss
- Won't boot after crash

### Severity Assessment

**Mild corruption (ship boots but erratic):**
```
# In dojo
|meld  # Can fix minor state inconsistencies
```

**Severe corruption (won't boot):**
```bash
# Attempt event replay
urbit-worker replay pier/

# If fails, event log damaged
# Restore from backup (if available)
```

### Safe Backup Procedure

**CRITICAL: Never backup live pier**

```bash
# 1. Stop ship gracefully
# In dojo: ctrl+d
# Or: systemctl stop urbit-ship

# 2. Wait 30 seconds
sleep 30

# 3. Verify stopped
ps aux | grep urbit  # Should show nothing

# 4. Backup
tar czf ship-backup-$(date +%Y%m%d).tar.gz pier/

# 5. Restart
urbit pier/  # Or systemctl start urbit-ship
```

### Recovery from Backup

```bash
# Stop broken ship
mv broken-pier broken-pier.backup

# Restore backup
tar xzf ship-backup-YYYY-MM-DD.tar.gz

# Boot restored ship
urbit restored-pier/
```

### Factory Reset (Last Resort)

**For Planets** (can re-boot):
```bash
# 1. Perform factory reset on Bridge (bridge.urbit.org)
#    This generates NEW keyfile, invalidates old identity

# 2. Delete old pier
rm -rf /path/to/old-pier

# 3. Boot with new keyfile
urbit -w new-ship-name -k new-keyfile.key

# WARNING: All data lost (groups, channels, messages)
```

**For Comets** (cannot recover):
- Comet identity = pier (no recovery possible)
- Delete old comet, generate new (different identity)
- All data permanently lost

## 7. Systematic Diagnostic Workflow

### Step 1: Gather Information

```bash
# Ship logs
journalctl -u urbit-ship -n 200 > ship-logs.txt

# System information
uname -a > system-info.txt
free -h >> system-info.txt
df -h >> system-info.txt

# Network status
sudo ufw status verbose > firewall-status.txt
ip addr show > network-config.txt

# Pier information
du -sh pier/ > pier-info.txt
ls -la pier/ >> pier-info.txt
```

### Step 2: Identify Symptom Pattern

- Boot failure → Decision Tree #1 (Boot Failures)
- Memory errors → Decision Tree #2 (Memory Issues)
- Network issues → Decision Tree #3 (Network Connectivity)
- OTA problems → Decision Tree #4 (OTA Update Failures)
- Slow performance → Decision Tree #5 (Performance Degradation)
- Crashes/corruption → Decision Tree #6 (Pier Corruption)

### Step 3: Apply Diagnostic Tree

Follow structured decision tree (don't skip steps)

### Step 4: Implement Solution

Change ONE variable at a time, verify effect

### Step 5: Document Resolution

```bash
# Create incident report
cat > incident-$(date +%Y%m%d).md << EOF
# Incident Report

**Date**: $(date)
**Symptom**: [Description]
**Diagnostic Path**: [Decision tree followed]
**Root Cause**: [What was wrong]
**Resolution**: [What fixed it]
**Prevention**: [How to prevent recurrence]
EOF
```

### Step 6: Implement Prevention

- Add monitoring for early detection
- Automate preventive maintenance (|pack weekly)
- Update runbooks
- Share learnings with team

## 8. Common Dojo Commands for Troubleshooting

```
# System information
|mass           # Memory breakdown
+vats           # Running agents (CPU/memory)
+code           # Web login code (HTTP server check)
+trouble        # Network diagnostics

# Memory management
|pack           # Defragmentation (quick)
|meld           # Deduplication (slow, thorough)

# Networking
|hi ~zod        # Test Ames connectivity
|ota ~litzod    # Change OTA sponsor

# OTA management
|bump           # Suspend blocking apps for OTA
|install our %base  # Force OTA update

# Application management
|suspend %app   # Suspend app
|revive %app    # Resume app
|nuke %app      # Uninstall app

# Emergency
|reset-cache    # Clear Eyre HTTP cache
|bonk           # Reset Ames networking (last resort)
```

## 9. Best Practices

1. **Check logs first**: `journalctl -u urbit-ship -n 100`
2. **Use decision trees**: Structured diagnostics, not random changes
3. **One variable at a time**: Isolate what actually fixes the issue
4. **Document everything**: Build institutional knowledge
5. **Preventive maintenance**: Weekly |pack, monitoring, backups
6. **Graceful shutdowns**: Always ctrl+d (never kill -9)
7. **Backup when stopped**: Prevent event log corruption
8. **Test backups quarterly**: Verify restoration works
9. **Monitor proactively**: Catch issues before they become critical
10. **Build runbooks**: Turn troubleshooting experiences into documentation

## Summary

Effective Urbit troubleshooting requires systematic diagnostic approaches using decision trees, thorough log analysis, and careful variable isolation. Common issues (boot failures, memory exhaustion, network connectivity, OTA problems, performance degradation, pier corruption) each have structured diagnostic paths. Prevention through monitoring, regular maintenance (|pack), safe backup procedures, and runbook creation is more valuable than reactive troubleshooting. Always document resolutions to build institutional knowledge.
