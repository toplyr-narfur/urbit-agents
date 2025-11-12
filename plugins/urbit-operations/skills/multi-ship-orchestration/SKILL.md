---
name: multi-ship-orchestration
description: Managing fleets of Urbit ships with GroundSeg including resource planning, lifecycle management, fleet sizing, monitoring, and operational workflows for coordinating multiple ships on shared infrastructure. Use when managing ship fleets, planning multi-ship deployments, allocating resources, or operating GroundSeg orchestration environments.
---

# Multi-Ship Orchestration Skill

Managing fleets of Urbit ships with GroundSeg including lifecycle management, resource allocation, monitoring, and operational workflows.

## Overview

Multi-ship orchestration involves coordinating multiple Urbit ships on shared infrastructure while maintaining isolation, performance, and operational efficiency.

## Resource Planning

### Per-Ship Requirements

**Minimum** (testing/light use):
- RAM: 2GB
- Storage: 25GB
- CPU: 0.5 cores

**Recommended** (production):
- RAM: 4GB
- Storage: 50GB+
- CPU: 1 core

### Fleet Sizing Examples

**10-ship fleet**:
- RAM: 40GB (4GB × 10)
- Storage: 500GB (50GB × 10)
- CPU: 10 cores

**50-ship fleet**:
- RAM: 200GB
- Storage: 2.5TB
- CPU: 50 cores

## GroundSeg Multi-Ship Management

### Ship Deployment via UI

1. Access GroundSeg UI (`http://nativeplanet.local`)
2. Click "Add Ship"
3. Configure:
   - Ship type (planet, comet, fake)
   - Resources (RAM, storage limits)
   - Networking (StarTram/Anchor, custom domain)
   - Storage backend (local/MinIO)
4. Deploy

### Batch Ship Deployment

For large fleets, use Docker Compose configuration:

```yaml
# docker-compose.yml
services:
  ship1:
    image: urbit/urbit:latest
    volumes:
      - ./piers/ship1:/urbit
    environment:
      - SHIP_NAME=sampel-palnet
    mem_limit: 4g

  ship2:
    image: urbit/urbit:latest
    volumes:
      - ./piers/ship2:/urbit
    environment:
      - SHIP_NAME=sampel-sampel
    mem_limit: 4g

  # ... additional ships
```

## Resource Allocation

### CPU Limits (Docker)

```yaml
services:
  ship1:
    cpus: '1.0'  # Limit to 1 CPU core
```

### Memory Limits

```yaml
services:
  ship1:
    mem_limit: 4g      # Hard limit
    mem_reservation: 2g  # Soft reservation
```

### Storage Management

**Per-ship storage allocation**:
```yaml
services:
  ship1:
    volumes:
      - type: volume
        source: ship1-data
        target: /urbit
        volume:
          driver: local
          driver_opts:
            type: none
            device: /mnt/ship-storage/ship1
            o: bind,size=50G  # 50GB limit
```

## Monitoring Fleet Health

### Container Status

```bash
# List all ship containers
docker ps --filter "ancestor=urbit/urbit"

# Check resource usage
docker stats

# View individual ship logs
docker logs ship1 -f
```

### Automated Health Checks

```bash
# Create monitoring script
cat > /usr/local/bin/fleet-monitor.sh << 'EOF'
#!/bin/bash
for ship in ship1 ship2 ship3; do
  if ! docker ps | grep -q $ship; then
    echo "ALERT: $ship is down"
    # Send alert (email, Slack, etc.)
  fi
done
EOF

chmod +x /usr/local/bin/fleet-monitor.sh

# Schedule via cron (every 5 minutes)
*/5 * * * * /usr/local/bin/fleet-monitor.sh
```

## Lifecycle Management

### Starting Ships

```bash
# Start all ships
docker-compose up -d

# Start specific ship
docker-compose up -d ship1

# Start with resource limits
docker-compose up -d --scale ship1=1
```

### Stopping Ships Gracefully

```bash
# Stop all ships
docker-compose down

# Stop specific ship
docker-compose stop ship1

# Graceful shutdown (important for event log consistency)
docker exec ship1 /bin/sh -c "echo 'ctrl+d' | urbit-worker"
```

### Restarting Ships

```bash
# Restart all ships
docker-compose restart

# Restart specific ship
docker-compose restart ship1
```

## Operational Workflows

### OTA Update Management

**Per-ship OTA monitoring**:
- Track each ship's sponsor connectivity
- Monitor |bump commands for blocked updates
- Coordinate update windows (maintenance periods)

**Batch OTA troubleshooting**:
```bash
# Check all ships for OTA status
for ship in ship1 ship2 ship3; do
  echo "=== $ship OTA Status ==="
  docker exec $ship urbit-worker dojo <<< "+vats"
done
```

### Maintenance Windows

**Schedule fleet maintenance**:
1. Notify users of maintenance window
2. Stop all ships gracefully
3. Perform |meld on ships approaching loom limits
4. Backup piers
5. Update GroundSeg/Docker
6. Restart ships
7. Verify all ships healthy

### Backup Orchestration

```bash
# Fleet backup script
#!/bin/bash
BACKUP_DIR="/backups/fleet-$(date +%Y%m%d)"
mkdir -p $BACKUP_DIR

# Stop all ships
docker-compose down

# Wait for complete shutdown
sleep 60

# Backup each pier
for pier in ./piers/*; do
  tar czf "$BACKUP_DIR/$(basename $pier).tar.gz" "$pier"
done

# Restart ships
docker-compose up -d

# Upload to offsite (S3, etc.)
aws s3 sync $BACKUP_DIR s3://urbit-fleet-backups/$(date +%Y%m%d)/
```

## Performance Optimization

### Staggered Startups

```bash
# Avoid CPU/RAM spike from simultaneous starts
for i in {1..10}; do
  docker-compose up -d ship$i
  sleep 30  # Wait 30s between ship starts
done
```

### Load Balancing

**Distribute ships across servers**:
- Use multiple GroundSeg instances (geographic distribution)
- DNS round-robin for ship access
- Separate networking infrastructure per region

### Resource Quotas

```yaml
# Set fleet-wide resource limits
services:
  ship1:
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 4G
        reservations:
          cpus: '0.5'
          memory: 2G
```

## Security Isolation

### Network Isolation

```yaml
services:
  ship1:
    networks:
      - ship1-network

  ship2:
    networks:
      - ship2-network

networks:
  ship1-network:
  ship2-network:
```

### User Isolation

- Separate Linux users per ship (non-Docker)
- AppArmor/SELinux profiles
- Container security policies

## Scaling Strategies

### Vertical Scaling

- Add more RAM/CPU to server
- Increase loom size for existing ships (`--loom 32`)
- Upgrade to SSD storage

### Horizontal Scaling

- Deploy additional GroundSeg servers
- Distribute ships across servers
- Geographic distribution for latency optimization

## Best Practices

1. **Resource reservation**: Always reserve 20% overhead (system + Docker)
2. **Monitoring**: Automated health checks every 5 minutes
3. **Staggered operations**: Start/stop ships in batches (avoid resource spikes)
4. **Backup strategy**: Automated daily backups, offsite storage
5. **Update coordination**: Maintenance windows for fleet-wide updates
6. **Isolation**: Network and user isolation between ships
7. **Documentation**: Maintain fleet inventory (ship names, resources, ownership)
8. **Capacity planning**: Monitor growth trends, plan for scaling
9. **Disaster recovery**: Test recovery procedures quarterly
10. **Access control**: Restrict GroundSeg UI access (VPN, IP whitelist)

## Summary

Multi-ship orchestration requires careful resource planning (4GB RAM, 50GB storage per ship), automated monitoring (Docker stats, health checks), lifecycle management (graceful shutdowns, staggered starts), and operational workflows (OTA coordination, maintenance windows, backup orchestration). GroundSeg provides web UI for ship management while Docker Compose enables batch operations. Fleet scaling involves both vertical (more resources per server) and horizontal (multiple servers) strategies. Security isolation through network segmentation and resource quotas prevents cross-ship interference.
