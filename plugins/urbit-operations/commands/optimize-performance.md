---
name: optimize-performance
description: Automated performance optimization workflow for Urbit ships including profiling, bottleneck identification, and systematic tuning
agents:
  - performance-engineer
skills:
  - performance-optimization
  - performance-profiling
  - monitoring-observability
---

# Optimize Performance

Automated workflow for diagnosing and resolving Urbit ship performance issues through systematic profiling and optimization.

## Workflow Phases

### Phase 1: Baseline Measurement

**Collect current metrics**:
```bash
# System resources
echo "=== CPU ===" && top -bn1 | grep "Cpu(s)"
echo "=== Memory ===" && free -h
echo "=== Disk ===" && df -h
echo "=== I/O ===" && iostat -x 1 3
echo "=== Network ===" && iftop -t -s 5

# Pier size
du -sh /path/to/pier

# Ship-specific (in dojo)
|mass  # Memory breakdown
+vats  # Active apps
```

**Document baseline** (`/var/log/performance-baseline-$(date +%Y%m%d).txt`).

### Phase 2: Bottleneck Identification

**Interactive prompts**:
- Primary symptom? (Slow dojo / High CPU / Out of loom / Slow |pack)
- When did degradation start?
- Recent changes? (New apps / OS updates / Hardware changes)

**Automated diagnostics**:
```bash
# CPU bottleneck check
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
if (( $(echo "$CPU_USAGE > 80" | bc -l) )); then
    echo "BOTTLENECK: CPU saturation detected"
fi

# Memory bottleneck check
MEM_PERCENT=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100}')
if [ "$MEM_PERCENT" -gt 80 ]; then
    echo "BOTTLENECK: Memory pressure detected"
fi

# Disk I/O bottleneck check
IOWAIT=$(iostat -c 1 2 | tail -1 | awk '{print $4}' | cut -d'.' -f1)
if [ "$IOWAIT" -gt 20 ]; then
    echo "BOTTLENECK: Disk I/O saturation detected"
fi

# Pier size check
PIER_SIZE_GB=$(du -sb /path/to/pier | awk '{printf "%.0f", $1/1073741824}')
if [ "$PIER_SIZE_GB" -gt 50 ]; then
    echo "WARNING: Large pier size ($PIER_SIZE_GB GB), consider |pack or |meld"
fi
```

### Phase 3: Quick Wins (Immediate Optimizations)

**No-downtime improvements**:
```bash
# 1. I/O scheduler (if not already optimized)
CURRENT_SCHEDULER=$(cat /sys/block/sda/queue/scheduler | grep -o '\[.*\]' | tr -d '[]')
if [ "$CURRENT_SCHEDULER" != "none" ] && [ "$CURRENT_SCHEDULER" != "mq-deadline" ]; then
    echo "none" | sudo tee /sys/block/sda/queue/scheduler
    echo "I/O scheduler optimized: $CURRENT_SCHEDULER → none"
fi

# 2. Filesystem mount options
if ! grep -q "noatime" /proc/mounts; then
    echo "RECOMMEND: Add noatime,nodiratime to /etc/fstab"
fi

# 3. Swappiness (if not optimized)
CURRENT_SWAP=$(cat /proc/sys/vm/swappiness)
if [ "$CURRENT_SWAP" -gt 10 ]; then
    sudo sysctl vm.swappiness=10
    echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.conf
fi

# 4. Transparent Huge Pages (disable if enabled)
if [ "$(cat /sys/kernel/mm/transparent_hugepage/enabled)" != "[never]" ]; then
    echo "never" | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
    echo "never" | sudo tee /sys/kernel/mm/transparent_hugepage/defrag
fi
```

### Phase 4: Ship-Level Optimization

**Requires ship restart or dojo access**:

```bash
# In dojo:
# 1. Identify heavy apps
|mass

# 2. Suspend unused apps
|suspend %bitcoin-wallet
|suspend %unused-app

# 3. Run |pack (defragmentation)
|pack
# Wait 10-60 minutes for completion
```

**Loom sizing** (if approaching 2GB limit):
```bash
# Edit systemd service
sudo systemctl edit urbit-ship

# Add loom parameter
ExecStart=/usr/local/bin/urbit --loom 32 /path/to/pier  # 4GB

# Restart
sudo systemctl daemon-reload
sudo systemctl restart urbit-ship
```

### Phase 5: Kernel Tuning

**Apply sysctl optimizations**:
```bash
# Create optimizations file
sudo tee /etc/sysctl.d/99-urbit-perf.conf > /dev/null <<EOF
# Network buffers
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216

# Virtual memory
vm.swappiness = 10
vm.dirty_ratio = 15
vm.dirty_background_ratio = 5

# File descriptors
fs.file-max = 65536
EOF

# Apply
sudo sysctl -p /etc/sysctl.d/99-urbit-perf.conf
```

### Phase 6: Scheduled Maintenance

**Automate preventive maintenance**:
```bash
# Weekly |pack
(crontab -l 2>/dev/null; echo "0 3 * * 0 docker exec urbit-ship /bin/sh -c \"echo '|pack' | urbit-worker dojo\"") | crontab -

# Monthly |meld (if 8GB+ RAM)
(crontab -l; echo "0 2 1 * * docker exec urbit-ship /bin/sh -c \"echo '|meld' | urbit-worker dojo\"") | crontab -
```

### Phase 7: Verification

**Measure improvements**:
```bash
# Re-run baseline measurements
echo "=== POST-OPTIMIZATION METRICS ===" > /var/log/performance-after-$(date +%Y%m%d).txt

# CPU, Memory, Disk, Network (same as Phase 1)
# Compare before/after

# Calculate improvements
echo "CPU improvement: X%"
echo "Memory freed: XGB"
echo "Disk I/O latency reduced: Xms"
```

### Phase 8: Capacity Planning

**Project future needs**:
```bash
# Calculate monthly pier growth rate
CURRENT_SIZE=$(du -sb /path/to/pier | awk '{print $1}')
PREVIOUS_SIZE=$(cat /var/log/pier-size-30d-ago.txt 2>/dev/null || echo $CURRENT_SIZE)
MONTHLY_GROWTH=$(( (CURRENT_SIZE - PREVIOUS_SIZE) / 1073741824 ))

echo "Monthly pier growth: ${MONTHLY_GROWTH}GB"

# Estimate time to disk full
DISK_FREE_GB=$(df /path/to/pier | tail -1 | awk '{print int($4/1048576)}')
MONTHS_REMAINING=$(( DISK_FREE_GB / (MONTHLY_GROWTH + 1) ))

echo "Estimated months until disk full: $MONTHS_REMAINING"

if [ "$MONTHS_REMAINING" -lt 3 ]; then
    echo "ACTION REQUIRED: Upgrade storage within 3 months"
fi
```

## Configuration Options

**Required**:
- Ship name or pier path
- Current performance issue (if known)

**Optional**:
- Skip system-level changes (for managed hosting)
- Aggressive mode (includes |meld)
- Dry-run (show recommendations without applying)

## Expected Improvements

**Typical gains** (before → after):

| Optimization | CPU | RAM | Disk I/O | Dojo Response |
|--------------|-----|-----|----------|---------------|
| I/O scheduler | -5% | - | -40% latency | -30% |
| |pack | -10% | -20% | -30% | -40% |
| |meld | -15% | -40% | -20% | -50% |
| App suspension | -20% | -30% | - | -20% |
| SSD upgrade | - | - | -90% latency | -70% |
| Combined | -30-50% | -50-70% | -80-95% latency | -60-80% |

## Troubleshooting

**No improvement after optimization**:
- Verify changes applied (`cat /sys/block/sda/queue/scheduler`, `sysctl -a | grep swappiness`)
- Check for external bottlenecks (network, provider throttling)
- Consider hardware upgrade (CPU, RAM, SSD)

**Ship won't start after loom change**:
- Ensure consistent `--loom` value on every boot
- Verify sufficient system RAM (loom 32 requires 6GB+ system RAM)

**|pack/|meld stuck**:
- Be patient (can take 1-4 hours for large piers)
- Monitor progress: `journalctl -u urbit-ship -f`
- If >6 hours: force restart, restore from backup

## Best Practices

1. **Measure twice, optimize once**: Always baseline first
2. **One change at a time**: Isolate what helps
3. **Document everything**: Changes, results, dates
4. **Test thoroughly**: Verify ship functionality after each change
5. **Automate maintenance**: Schedule |pack, monitoring
6. **Plan capacity**: Monitor trends, upgrade proactively
7. **Hardware first**: SSD upgrade often better ROI than tuning
8. **Preventive > reactive**: Weekly |pack prevents emergencies

## Cross-References

- **performance-optimization**: Detailed tuning guide
- **performance-profiling**: Advanced profiling techniques
- **monitoring-observability**: Set up continuous monitoring
- **troubleshoot-ship**: Diagnostic procedures

## Summary

Performance optimization workflow systematically identifies and resolves bottlenecks through 8 phases: baseline measurement, bottleneck identification (CPU/RAM/I/O/network), quick wins (I/O scheduler, swappiness, THP), ship-level optimization (|pack, app suspension, loom sizing), kernel tuning (sysctl network/VM parameters), scheduled maintenance (weekly |pack, monthly |meld), verification (before/after metrics), and capacity planning (growth projection). Typical improvements: 30-50% CPU reduction, 50-70% RAM savings, 80-95% disk latency reduction, 60-80% faster dojo response. Best practices: measure first, one change at a time, automate maintenance, hardware upgrade often better ROI than software tuning.
