---
name: fleet-operations
description: Large-scale Urbit ship fleet management for 5-100+ ships including resource planning, batch operations, automation, Infrastructure-as-Code with Terraform, Helm templating, and GitOps workflows. Use when managing ship fleets, planning large deployments, automating operations, or implementing IaC for Urbit infrastructure.
---

# Fleet Operations Skill

Large-scale Urbit ship fleet management including multi-ship orchestration, resource planning, batch operations, and automation (2025).

## Overview

Fleet operations manages 5+ Urbit ships with centralized monitoring, automated deployments, and standardized configurations.

## Fleet Sizing Guide

| Fleet Size | RAM | Storage | Monthly Cost | Management Tool |
|------------|-----|---------|--------------|-----------------|
| **5 ships** | 20GB | 250GB | $40-80 | GroundSeg |
| **10 ships** | 40GB | 500GB | $80-160 | GroundSeg + monitoring |
| **25 ships** | 100GB | 1.25TB | $200-400 | Kubernetes |
| **50 ships** | 200GB | 2.5TB | $400-800 | Kubernetes |
| **100+ ships** | 400GB+ | 5TB+ | $800+ | Enterprise K8s |

## Resource Planning

**Per-ship allocation**:
- RAM: 4GB (baseline), 6GB (growth headroom)
- Storage: 50GB (fresh), 100GB (1 year operation)
- CPU: 0.5 vCPU (idle), 1 vCPU (active)
- Bandwidth: 2TB+/month

**Overhead** (GroundSeg/K8s):
- System: +2GB RAM, +20GB disk
- Monitoring: +2GB RAM, +20GB disk per monitoring server

## GroundSeg Fleet Management

### Multi-Ship Deployment

```bash
# Install GroundSeg
sudo wget -O - get.groundseg.app|bash

# Access UI
# http://nativeplanet.local:3000

# Add ships via UI:
# 1. Click "Add Ship"
# 2. Upload keyfile OR import existing pier
# 3. Configure networking (StarTram or Anchor)
# 4. Start ship
# Repeat for each ship
```

### Batch Operations

```bash
# Stop all ships
cd /opt/groundseg
docker-compose stop

# Start all ships (staggered to avoid resource spikes)
for ship in $(docker ps -a --format '{{.Names}}' | grep urbit); do
    docker start $ship
    sleep 30  # Stagger startups
done

# Backup all piers
for pier in ./piers/*; do
    tar czf "backup-$(basename $pier)-$(date +%Y%m%d).tar.gz" "$pier"
done

# Monitor all ships
docker stats
```

### Fleet Configuration Management

```yaml
# fleet-config.yml
ships:
  - name: ship1
    loom: 32  # 4GB
    network: startram
    monitoring: enabled

  - name: ship2
    loom: 32
    network: anchor
    custom_domain: ship2.example.com

  - name: ship3
    loom: 31  # 2GB (comet)
    network: startram
    monitoring: enabled
```

## Kubernetes Fleet Deployment

### Basic K8s Architecture

```yaml
# ship-statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: urbit-fleet
spec:
  serviceName: "urbit"
  replicas: 10  # Number of ships
  selector:
    matchLabels:
      app: urbit
  template:
    metadata:
      labels:
        app: urbit
    spec:
      containers:
      - name: urbit
        image: urbit/urbit:latest
        resources:
          requests:
            memory: "4Gi"
            cpu: "500m"
          limits:
            memory: "6Gi"
            cpu: "1000m"
        volumeMounts:
        - name: pier
          mountPath: /urbit
        ports:
        - containerPort: 8080
          name: http
        - containerPort: 34543
          protocol: UDP
          name: ames
  volumeClaimTemplates:
  - metadata:
      name: pier
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 100Gi
```

### LoadBalancer Service

```yaml
# ship-loadbalancer.yaml
apiVersion: v1
kind: Service
metadata:
  name: urbit-lb
spec:
  type: LoadBalancer
  selector:
    app: urbit
  ports:
    - name: http
      port: 80
      targetPort: 8080
    - name: https
      port: 443
      targetPort: 8080
```

## Centralized Monitoring

### Prometheus Multi-Target

```yaml
# prometheus-fleet.yml
scrape_configs:
  - job_name: 'urbit-fleet'
    static_configs:
      - targets:
          - 'ship1:9100'
          - 'ship2:9100'
          - 'ship3:9100'
          - 'ship4:9100'
          - 'ship5:9100'
        labels:
          fleet: 'production'
```

### Grafana Fleet Dashboard

**Panels**:
- Total ships (gauge)
- Ships up/down (status)
- Aggregate CPU usage (graph)
- Aggregate RAM usage (graph)
- Total pier size (graph)
- Pier growth rate per ship (table)

## Automated Fleet Operations

### Mass |pack Script

```bash
#!/bin/bash
# fleet-pack.sh - Run |pack on all ships

SHIPS=("ship1" "ship2" "ship3" "ship4" "ship5")

for ship in "${SHIPS[@]}"; do
    echo "Running |pack on $ship..."
    docker exec $ship /bin/sh -c "echo '|pack' | urbit-worker dojo" || echo "Failed: $ship"
    sleep 60  # Wait between ships
done
```

### Rolling Updates

```bash
#!/bin/bash
# rolling-update.sh - Update ships one at a time (zero fleet downtime)

SHIPS=("ship1" "ship2" "ship3" "ship4" "ship5")

for ship in "${SHIPS[@]}"; do
    echo "Updating $ship..."

    # Stop ship
    docker stop $ship

    # Backup
    tar czf "backup-$ship-$(date +%Y%m%d).tar.gz" "/opt/groundseg/piers/$ship"

    # Update container image
    docker pull urbit/urbit:latest

    # Start ship
    docker start $ship

    # Wait for healthy
    sleep 120

    # Verify
    if ! docker ps | grep -q $ship; then
        echo "ERROR: $ship failed to start!"
        # Rollback: restore backup
        exit 1
    fi

    echo "$ship updated successfully"
done
```

## Fleet-Wide Configuration

### Standardized systemd Template

```ini
# /etc/systemd/system/urbit@.service
[Unit]
Description=Urbit ship: %i
After=network.target

[Service]
Type=simple
User=urbit
WorkingDirectory=/home/urbit
ExecStart=/usr/local/bin/urbit %i
Restart=always
RestartSec=10

# Security
PrivateTmp=yes
ProtectSystem=strict
ReadWritePaths=/home/urbit

[Install]
WantedBy=multi-user.target
```

**Usage**:
```bash
# Enable/start multiple ships
systemctl enable urbit@ship1
systemctl enable urbit@ship2
systemctl start urbit@{ship1,ship2,ship3}
```

## Cost Optimization for Fleets

### Shared Infrastructure

- **Single monitoring server**: Monitor all ships from one Prometheus/Grafana instance
- **Shared backup storage**: S3 bucket with lifecycle policies
- **Reserved instances**: AWS/GCP reserved pricing (30-50% savings for 1-year commit)

### Rightsizing

```bash
# Analyze actual usage after 30 days
docker stats --no-stream > fleet-stats.txt

# Identify underutilized ships
awk '$3 < 50 {print $2}' fleet-stats.txt  # Ships using <50% RAM
```

## Best Practices

1. **Staggered startups**: Avoid simultaneous ship boots (resource spikes)
2. **Rolling operations**: Update/pack one ship at a time
3. **Centralized monitoring**: Single Grafana for entire fleet
4. **Automated backups**: Daily backups for all ships, centralized storage
5. **Configuration management**: Ansible/Terraform for fleet-wide changes
6. **Resource quotas**: Set per-ship limits to prevent resource hogging
7. **Health checks**: Automated ship status monitoring
8. **Documentation**: Inventory spreadsheet (ship names, IPs, purposes)
9. **Access control**: Centralized SSH key management
10. **Disaster recovery**: Multi-region backup replication for critical ships

## Troubleshooting

**Ship A affects Ship B performance**:
- Resource contention: Increase host resources or distribute ships
- Set resource limits (Docker/K8s)

**Mass outage** (all ships down):
- Check host system resources (`htop`, `df -h`)
- Review Docker/K8s logs
- Restart ships sequentially (not all at once)

**Inconsistent ship behavior**:
- Verify consistent Urbit binary versions across fleet
- Check for configuration drift
- Use Ansible/Terraform for standardization

## Summary

Fleet operations for 5+ Urbit ships requires resource planning (4GB RAM, 50-100GB disk per ship), orchestration tooling (GroundSeg for <25 ships, Kubernetes for 25+), and centralized monitoring (Prometheus/Grafana). Batch operations enable mass |pack, rolling updates, and staggered startups. Cost optimization: shared monitoring infrastructure, reserved instances (30-50% savings), and rightsizing based on utilization. Best practices: rolling operations (zero-downtime), automated backups, configuration management (Ansible/Terraform), and comprehensive documentation. Typical fleet costs: 5 ships ($40-80/mo), 10 ships ($80-160/mo), 25+ ships ($200+/mo) with Kubernetes management.
