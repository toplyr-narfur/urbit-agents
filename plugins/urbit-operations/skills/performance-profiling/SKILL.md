# Performance Profiling Skill

Advanced performance profiling techniques for Urbit ships including CPU profiling, memory analysis, I/O tracing, and application-level profiling.

## System-Level Profiling Tools

### CPU Profiling

**htop** (interactive):
```bash
htop
# Press F5: Tree view
# Press F6: Sort by CPU%
# Press F9: Kill process
```

**perf** (Linux performance counter):
```bash
# Install
sudo apt install linux-tools-generic -y

# Profile Urbit process
sudo perf record -p $(pgrep urbit) -g -- sleep 60

# Analyze
sudo perf report
```

**vmstat** (system statistics):
```bash
vmstat 1 10  # 10 samples, 1 second intervals
# Watch: us (user CPU), sy (system CPU), wa (I/O wait)
```

### Memory Profiling

**valgrind** (memory leak detection):
```bash
valgrind --leak-check=full --log-file=valgrind.log urbit pier/
# Analyze valgrind.log for leaks
```

**smem** (memory by process):
```bash
sudo apt install smem -y
sudo smem -tk  # Total memory usage
```

**Ship-level** (dojo):
```
|mass  # Memory breakdown by vane/app
# Sample output:
# %clay: 234MB
# %gall: 456MB
#   %bitcoin-wallet: 123MB
#   %groups: 234MB
```

### Disk I/O Profiling

**iostat** (I/O statistics):
```bash
iostat -x 5  # Extended stats, 5-second intervals
# Key metrics:
# %util: Disk utilization (>80% = bottleneck)
# await: Average wait time (ms)
# r/s, w/s: Reads/writes per second
```

**iotop** (I/O by process):
```bash
sudo iotop -o  # Show only active I/O
# Identify which process causing I/O load
```

**ioping** (disk latency):
```bash
sudo apt install ioping -y
ioping -c 10 /path/to/pier
# Measure actual disk response time
```

### Network Profiling

**iftop** (bandwidth by connection):
```bash
sudo iftop -i eth0
# Shows real-time bandwidth usage per connection
```

**nethogs** (bandwidth by process):
```bash
sudo apt install nethogs -y
sudo nethogs eth0
```

**tcpdump** (packet capture):
```bash
# Capture Ames UDP traffic
sudo tcpdump -i any udp port 34543 -w ames-capture.pcap
# Analyze with Wireshark
```

## Urbit-Specific Profiling

### Pier Size Analysis

```bash
# Breakdown by directory
du -h --max-depth=1 /path/to/pier | sort -h

# Find largest files
find /path/to/pier -type f -size +100M -exec ls -lh {} \; | sort -k5 -hr

# Clay desk sizes
du -sh /path/to/pier/.urb/put/*

# Event log size
ls -lh /path/to/pier/.urb/log
```

### Event Processing Speed

```bash
# Measure dojo response time
time urbit attach pier <<< '+vats'

# Should be <1 second for healthy ship
```

### App Performance (dojo)

```
# List apps by memory
|mass

# Identify slow apps (manual testing)
# Time each app operation:
:app-name +some-generator
# Note response time

# Suspend heavy apps temporarily
|suspend %heavy-app
# Re-test system performance
```

## Profiling Workflow

1. **Establish baseline**: Collect metrics when ship performing normally
2. **Reproduce issue**: Trigger performance problem
3. **Collect profiles**: Run profiling tools during issue
4. **Analyze results**: Identify bottleneck (CPU/RAM/I/O/network)
5. **Hypothesize cause**: Based on profile data
6. **Test fix**: Apply optimization
7. **Verify improvement**: Re-profile, compare to baseline

## Common Profile Patterns

**CPU-bound**:
- `htop`: Urbit process using >80% CPU constantly
- `perf`: High cycles in specific functions
- **Cause**: Heavy computation, inefficient algorithms
- **Fix**: Optimize code, suspend apps, upgrade CPU

**Memory-bound**:
- `|mass`: Total >1.8GB (approaching 2GB loom limit)
- `smem`: Urbit RSS >4GB
- **Cause**: Memory leaks, large state, many apps
- **Fix**: |meld, suspend apps, increase loom

**I/O-bound**:
- `iostat`: %util >80%, await >20ms
- `iotop`: Urbit process high disk I/O
- **Cause**: Slow disk, fragmented pier, HDD
- **Fix**: Upgrade to NVMe SSD, |pack, I/O scheduler

**Network-bound**:
- `iftop`: Saturated bandwidth
- `nethogs`: Urbit high network usage
- **Cause**: Many subscriptions, large file transfers, DDoS
- **Fix**: Rate limiting, bandwidth upgrade, firewall

## Performance Regression Detection

```bash
#!/bin/bash
# performance-regression-detector.sh

# Run weekly, compare to baseline

BASELINE_CPU=25  # %
BASELINE_RAM=60  # %
BASELINE_IOWAIT=5  # %

CURRENT_CPU=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1 | cut -d'.' -f1)
CURRENT_RAM=$(free | grep Mem | awk '{printf "%d", $3/$2 * 100}')
CURRENT_IOWAIT=$(iostat -c 1 2 | tail -1 | awk '{print $4}' | cut -d'.' -f1)

if [ "$CURRENT_CPU" -gt "$((BASELINE_CPU + 20))" ]; then
    echo "REGRESSION: CPU usage increased by >20%"
fi

if [ "$CURRENT_RAM" -gt "$((BASELINE_RAM + 20))" ]; then
    echo "REGRESSION: RAM usage increased by >20%"
fi

if [ "$CURRENT_IOWAIT" -gt "$((BASELINE_IOWAIT + 10))" ]; then
    echo "REGRESSION: I/O wait increased by >10%"
fi
```

## Best Practices

1. **Profile in production**: Synthetic tests may not reflect real usage
2. **Minimize observer effect**: Profiling tools consume resources
3. **Profile duration**: 60+ seconds for statistical significance
4. **Compare apples-to-apples**: Same workload, same time of day
5. **Archive profiles**: Keep historical data for trend analysis
6. **Automate regression detection**: Weekly profiling + alerting
7. **Document baselines**: What's "normal" for your ship

## Summary

Performance profiling for Urbit uses system tools (htop, iostat, iotop, iftop) for CPU/RAM/I/O/network analysis and Urbit-specific methods (|mass for memory breakdown, pier size analysis, event processing timing). Profiling workflow: baseline → reproduce issue → collect profiles → analyze → hypothesize → fix → verify. Common patterns: CPU-bound (>80% usage, fix: optimize code), memory-bound (approaching loom limit, fix: |meld), I/O-bound (high %util/await, fix: SSD upgrade), network-bound (saturated bandwidth, fix: rate limiting). Automated regression detection compares weekly metrics to baseline, alerts on >20% degradation.
