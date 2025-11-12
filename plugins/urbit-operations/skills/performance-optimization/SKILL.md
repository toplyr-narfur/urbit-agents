---
name: performance-optimization
description: System-level and Urbit-specific performance tuning including I/O scheduler optimization, kernel parameters, disk performance, memory management, CPU pinning, and ship state optimization. Use when optimizing ship performance, tuning system parameters, eliminating bottlenecks, or improving responsiveness.
---

# Performance Optimization Skill

System-level and Urbit-specific performance tuning for production deployments including I/O optimization, kernel tuning, and ship state management.

## Overview

Performance optimization focuses on maximizing ship responsiveness, reducing resource consumption, and eliminating bottlenecks through systematic tuning.

## I/O Scheduler Optimization

### SSD Configuration (Recommended)

```bash
# Check current scheduler
cat /sys/block/sda/queue/scheduler

# Set to 'none' for NVMe SSDs (best performance)
echo "none" | sudo tee /sys/block/sda/queue/scheduler

# Or 'mq-deadline' for SATA SSDs
echo "mq-deadline" | sudo tee /sys/block/sda/queue/scheduler

# Make persistent (add to /etc/rc.local)
echo 'echo "none" > /sys/block/sda/queue/scheduler' | sudo tee -a /etc/rc.local
```

### HDD Configuration (If SSD Not Available)

```bash
# Use 'mq-deadline' for HDDs
echo "mq-deadline" | sudo tee /sys/block/sda/queue/scheduler

# Increase read-ahead (helps sequential reads)
sudo blockdev --setra 8192 /dev/sda
```

## Kernel Parameter Tuning

```bash
# Edit sysctl configuration
sudo nano /etc/sysctl.d/99-urbit-performance.conf
```

```
# Network performance
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216

# File descriptor limits
fs.file-max = 65536

# Virtual memory (reduce swappiness for better performance)
vm.swappiness = 10  # Only swap when necessary

# Disk I/O
vm.dirty_ratio = 15
vm.dirty_background_ratio = 5
```

```bash
# Apply changes
sudo sysctl -p /etc/sysctl.d/99-urbit-performance.conf
```

## Filesystem Optimization

### Ext4 Mount Options

```bash
# Edit /etc/fstab
sudo nano /etc/fstab
```

```
# Add noatime,nodiratime for reduced write overhead
UUID=xxx  /  ext4  defaults,noatime,nodiratime  0  1
```

```bash
# Remount with new options
sudo mount -o remount /
```

## Ship State Optimization

### Preventive Maintenance (|pack)

```bash
# Create weekly |pack cron job
crontab -e
```

```
# Run |pack every Sunday at 3 AM
0 3 * * 0 /usr/local/bin/urbit-pack.sh
```

```bash
# /usr/local/bin/urbit-pack.sh
#!/bin/bash
docker exec urbit-ship /bin/sh -c "echo '|pack' | urbit-worker dojo"
# Or for systemd ships:
# systemctl stop urbit-ship && urbit /path/to/pier --eval '|pack' && systemctl start urbit-ship
```

### Aggressive Cleanup (|meld)

```bash
# Monthly |meld (requires 8GB+ RAM)
# Create script
cat > /usr/local/bin/urbit-meld.sh << 'EOF'
#!/bin/bash
# REQUIRES: 8GB+ system RAM
echo "Starting |meld (this will take 1-4 hours)..."
docker exec urbit-ship /bin/sh -c "echo '|meld' | urbit-worker dojo"
echo "Monitoring pier size..."
watch -n 60 'du -sh /path/to/pier'
EOF

chmod +x /usr/local/bin/urbit-meld.sh

# Schedule monthly (first Sunday at 2 AM)
0 2 1-7 * 0 [ "$(date +\%d)" -le 7 ] && /usr/local/bin/urbit-meld.sh
```

## Loom Size Configuration

### Increasing Loom (If System Has RAM)

```bash
# For ships approaching 2GB state:
# Stop ship
systemctl stop urbit-ship

# Edit systemd service to use 4GB loom
sudo nano /etc/systemd/system/urbit-ship.service

# Add --loom 32 to ExecStart:
ExecStart=/usr/local/bin/urbit --loom 32 /home/urbit/pier

# Reload and start
sudo systemctl daemon-reload
systemctl start urbit-ship
```

**IMPORTANT**: Must use same `--loom` value on every boot

## CPU Optimization

### Process Priority

```bash
# Run ship with higher priority (systemd)
sudo nano /etc/systemd/system/urbit-ship.service

# Add to [Service] section:
Nice=-10  # Higher priority (-20 to 19, lower = higher priority)
IOSchedulingClass=realtime
IOSchedulingPriority=0
```

### CPU Affinity

```bash
# Pin ship to specific CPU cores (multi-core systems)
CPUAffinity=0 1 2 3  # Use cores 0-3
```

## Memory Optimization

### Swap Configuration

```bash
# Check current swap
free -h

# Disable swap (if 8GB+ RAM) for best performance
sudo swapoff -a
sudo nano /etc/fstab
# Comment out swap line

# Or reduce swappiness (already in sysctl config above)
vm.swappiness = 10
```

### Transparent Huge Pages

```bash
# Disable THP (can cause latency spikes)
echo "never" | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
echo "never" | sudo tee /sys/kernel/mm/transparent_hugepage/defrag

# Make persistent
sudo nano /etc/rc.local
# Add:
echo "never" > /sys/kernel/mm/transparent_hugepage/enabled
echo "never" > /sys/kernel/mm/transparent_hugepage/defrag
```

## Network Optimization

### TCP Tuning

```bash
# Already in sysctl config above, but key parameters:
net.ipv4.tcp_window_scaling = 1
net.ipv4.tcp_timestamps = 1
net.ipv4.tcp_sack = 1
net.core.netdev_max_backlog = 5000
net.ipv4.tcp_congestion_control = bbr  # Modern congestion control
```

### Ames Performance

```bash
# Increase UDP buffer sizes (already in sysctl config)
net.core.rmem_max = 16777216  # Ames uses UDP
```

## Application-Level Optimization

### Suspend Unused Apps

```
# In dojo - identify heavy apps
+vats

# Suspend unused apps
|suspend %bitcoin-wallet
|suspend %unused-app

# Resume when needed
|revive %bitcoin-wallet
```

### Gall Agent Optimization

- Review app code for inefficient patterns
- Use caching for frequently accessed data
- Minimize Clay filesystem operations
- Batch network requests (Ames/Iris)

## Monitoring Performance

### Real-Time Monitoring

```bash
# CPU, memory, disk I/O
htop

# Disk I/O details
iotop

# Network
iftop

# Combined stats
dstat
```

### Performance Baselines

```bash
# Establish baseline metrics
cat > /tmp/performance-baseline.sh << 'EOF'
#!/bin/bash
echo "=== Performance Baseline $(date) ==="
echo "CPU:"
top -bn1 | grep "Cpu(s)"
echo "Memory:"
free -h
echo "Disk I/O:"
iostat -x 1 3
echo "Network:"
sar -n DEV 1 3
echo "Pier size:"
du -sh /path/to/pier
EOF

chmod +x /tmp/performance-baseline.sh
/tmp/performance-baseline.sh > baseline-$(date +%Y%m%d).txt
```

## Benchmarking

### Pier Access Speed

```bash
# Test random I/O performance
sudo fio --name=randread --ioengine=libaio --iodepth=16 --rw=randread --bs=4k --direct=1 --size=1G --numjobs=1 --runtime=60 --group_reporting --filename=/path/to/pier/test
```

### Event Processing Speed

```
# In dojo - measure dojo response time
|mass  # Note response time
+vats  # Note response time

# Compare before/after optimization
```

## Common Bottlenecks

### Disk I/O Bottleneck

**Symptoms**: High iowait%, slow pier operations

**Solutions**:
- Upgrade to SSD (most effective)
- Optimize I/O scheduler
- Run |pack to reduce pier size
- Reduce ship count per server

### CPU Bottleneck

**Symptoms**: Sustained >80% CPU usage

**Solutions**:
- Suspend unused Gall apps
- Increase CPU priority (Nice=-10)
- Upgrade to faster CPU
- Distribute ships across servers

### Memory Bottleneck

**Symptoms**: Frequent "out of loom" errors

**Solutions**:
- Run |meld (requires 8GB RAM during execution)
- Increase loom size (--loom 32)
- Suspend memory-heavy apps
- Upgrade system RAM

## Performance Tuning Checklist

- [ ] SSD storage (NVMe preferred)
- [ ] I/O scheduler: 'none' (NVMe) or 'mq-deadline' (SATA SSD)
- [ ] Kernel parameters optimized (sysctl)
- [ ] Filesystem: noatime,nodiratime
- [ ] Swappiness: 10 (or swap disabled if 8GB+ RAM)
- [ ] Transparent Huge Pages: disabled
- [ ] Weekly |pack scheduled
- [ ] Monthly |meld scheduled (if 8GB+ RAM)
- [ ] Loom size: 4GB if ship approaching 2GB
- [ ] CPU priority: Nice=-10 (systemd)
- [ ] Unused Gall apps suspended
- [ ] Performance baseline documented
- [ ] Monitoring automated

## Best Practices

1. **SSD mandatory**: HDD too slow for production
2. **Preventive |pack**: Weekly automation prevents emergencies
3. **Monitor proactively**: Track pier size growth trends
4. **Baseline first**: Measure before optimizing
5. **One change at a time**: Isolate what improves performance
6. **Test thoroughly**: Verify ship functionality after changes
7. **Document baselines**: Compare before/after metrics
8. **Plan capacity**: Monitor trends, scale before hitting limits
9. **Regular cleanup**: Suspend unused apps, run |pack
10. **Hardware upgrade**: Sometimes better than optimization

## Reference

- Linux Performance: http://www.brendangregg.com/linuxperf.html
- sysctl tuning: https://www.kernel.org/doc/Documentation/sysctl/
- I/O schedulers: https://www.kernel.org/doc/Documentation/block/

## Summary

Performance optimization for Urbit ships involves multi-layered tuning: I/O scheduler configuration ('none' for NVMe SSD), kernel parameter tuning (sysctl for network/disk/memory), filesystem optimization (noatime, nodiratime), ship state management (weekly |pack, monthly |meld), and application-level optimization (suspend unused apps, loom size configuration). SSD storage is mandatory for production. Establish performance baselines before optimization, change one variable at a time, and monitor with htop/iotop/iostat. Common bottlenecks (disk I/O, CPU, memory) each have specific resolution strategies.
