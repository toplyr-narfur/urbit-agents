---
name: groundseg-troubleshooting
description: Systematic diagnostic procedures for GroundSeg container platform issues including Docker configuration problems, UI accessibility, networking failures, ship lifecycle issues, and multi-ship coordination problems. Use when diagnosing GroundSeg issues, resolving Docker problems, debugging container networking, or troubleshooting ship management failures.
---

# GroundSeg Troubleshooting Skill

Systematic diagnostic procedures for GroundSeg container platform issues including Docker problems, networking failures, and ship lifecycle issues.

## Overview

GroundSeg troubleshooting focuses on container orchestration issues, Docker configuration problems, networking failures, and multi-ship coordination issues.

## 1. GroundSeg UI Not Accessible

### Symptoms
- Cannot access `http://nativeplanet.local`
- Cannot access `http://<server-ip>:3000`
- UI loads but shows errors

### Diagnostic Steps

```bash
# Check GroundSeg container running
docker ps | grep groundseg

# Check logs
docker logs groundseg-ui -f

# Verify port 3000 available
sudo netstat -tulpn | grep 3000

# Check firewall
sudo ufw status | grep 3000
```

### Common Resolutions

```bash
# Container not running
docker-compose up -d

# Port conflict
# Find process using 3000
sudo lsof -i :3000
# Kill conflicting process or change GroundSeg port

# Firewall blocking
sudo ufw allow 3000/tcp

# DNS resolution (nativeplanet.local)
# Check /etc/hosts
echo "127.0.0.1 nativeplanet.local" | sudo tee -a /etc/hosts
```

## 2. Ships Won't Start

### Symptoms
- Ship container crashes on startup
- Container starts but ship doesn't boot
- "Exited (1)" status

### Diagnostic Decision Tree

```bash
# Check container status
docker ps -a | grep ship-name

# View container logs
docker logs ship-name

# Check for common errors:
# - "out of loom" → Memory exhaustion
# - "permission denied" → Volume permissions
# - "address already in use" → Port conflict
```

### Resolution by Error Type

**Permission denied**:
```bash
# Fix pier ownership
sudo chown -R 1000:1000 ./piers/ship-name
chmod 700 ./piers/ship-name
```

**Port conflict**:
```bash
# Find conflicting port
sudo netstat -tulpn | grep <port>

# Change ship port in docker-compose.yml
ports:
  - "8081:8080"  # Use different external port
```

**Out of memory**:
```bash
# Increase container memory limit
# docker-compose.yml
mem_limit: 6g  # Increase from 4g
```

## 3. Network Connectivity Issues

### StarTram Not Working

**Symptoms**: Ships not accessible via *.arvo.network domain

**Diagnostics**:
```bash
# Check StarTram registration in GroundSeg UI
# Verify registration code entered correctly

# Check ship logs for StarTram connection
docker logs ship-name | grep -i startram

# Test DNS resolution
nslookup ship-name.arvo.network
```

**Resolutions**:
- Re-enter StarTram registration code
- Restart GroundSeg containers
- Contact Native Planet support for registration issues

### Anchor Not Working

**Symptoms**: Ships not accessible via custom domain

**Diagnostics**:
```bash
# Check Anchor VPS connectivity
ping anchor-vps-ip

# Check reverse tunnel/VPN
# On ship server:
ps aux | grep -E '(ssh|wireguard)'

# Verify Nginx on Anchor VPS
ssh anchor-vps "systemctl status nginx"

# Check DNS resolution
nslookup ship.yourdomain.com
# Should point to Anchor VPS IP
```

**Resolutions**:
- Restart reverse tunnel: `autossh -M 0 -R 8080:localhost:8080 anchor-vps`
- Verify Nginx config on Anchor VPS
- Check SSL certificate: `certbot certificates`

## 4. MinIO Issues

### MinIO Container Won't Start

```bash
# Check MinIO container
docker ps -a | grep minio

# View logs
docker logs minio

# Common issues:
# - Port 9000/9001 in use
# - Volume permissions
# - Insufficient disk space
```

**Resolutions**:
```bash
# Port conflict
sudo lsof -i :9000
sudo lsof -i :9001

# Fix permissions
sudo chown -R 1000:1000 ./minio-data

# Check disk space
df -h
```

### Cannot Access MinIO Console

```bash
# Verify firewall
sudo ufw allow 9001/tcp

# Check container logs
docker logs minio

# Test local access
curl http://localhost:9001
```

## 5. Docker Issues

### Docker Daemon Not Running

```bash
# Check Docker status
sudo systemctl status docker

# Start Docker
sudo systemctl start docker

# Enable on boot
sudo systemctl enable docker
```

### Docker Compose Not Found

```bash
# Install Docker Compose
sudo apt install docker-compose -y

# Or use Docker Compose plugin
sudo apt install docker-compose-plugin -y
# Use: docker compose (instead of docker-compose)
```

### Disk Space Exhaustion

```bash
# Check Docker disk usage
docker system df

# Clean up
docker system prune -a  # Remove unused containers, images, volumes

# Remove specific dangling images
docker image prune

# Remove stopped containers
docker container prune
```

## 6. Performance Degradation

### Symptoms
- Slow ship responsiveness
- High CPU usage
- High memory usage
- Slow Docker operations

### Diagnostic Tools

```bash
# Check container resource usage
docker stats

# Check host resources
htop

# Check disk I/O
iostat -x 5

# Check specific ship resources
docker stats ship-name
```

### Resolution by Bottleneck

**CPU saturation**:
```yaml
# Limit CPU per container
cpus: '1.0'
```

**Memory saturation**:
```bash
# Run |meld on ships
docker exec ship-name urbit-worker dojo <<< "|meld"

# Increase container memory
mem_limit: 6g
```

**Disk I/O saturation**:
- Upgrade to SSD
- Reduce ship count per server
- Optimize I/O scheduler (see performance-optimization skill)

## 7. Update and Upgrade Issues

### GroundSeg Update Failed

```bash
# Check current version
docker-compose --version
docker --version

# Backup before update
tar czf groundseg-backup-$(date +%Y%m%d).tar.gz docker-compose.yml .env piers/

# Pull latest
cd /opt/groundseg
git pull origin main

# Update containers
docker-compose pull
docker-compose up -d
```

### Ship OTA Update Stuck

```bash
# Access ship dojo
docker exec -it ship-name /bin/sh -c "urbit-worker dojo"

# In dojo:
+vats  # Check for blocking apps
|bump  # Suspend blocking apps

# If stuck, change sponsor
|ota ~litzod
```

## 8. Data Corruption

### Pier Corruption in Container

```bash
# Check container filesystem
docker exec ship-name ls -la /urbit

# Restore from backup
docker-compose stop ship-name
sudo mv piers/ship-name piers/ship-name.corrupted
sudo tar xzf ship-name-backup.tar.gz -C piers/
docker-compose start ship-name
```

### Volume Issues

```bash
# List volumes
docker volume ls

# Inspect volume
docker volume inspect groundseg_ship-name

# Remove dangling volumes (after backup!)
docker volume prune
```

## Best Practices

1. **Check logs first**: `docker logs <container>` for all issues
2. **Container status**: `docker ps -a` shows all containers (running and stopped)
3. **Resource monitoring**: `docker stats` for real-time resource usage
4. **Backup before changes**: Always backup piers before troubleshooting
5. **One change at a time**: Isolate what fixes the issue
6. **Document solutions**: Update runbooks with resolutions
7. **Regular cleanup**: `docker system prune` monthly
8. **Monitor disk space**: Docker images consume significant space
9. **Test locally first**: Use fake ships to test configurations
10. **Keep logs**: `docker-compose logs > debug.log` for support

## Emergency Procedures

### Complete GroundSeg Reset (Last Resort)

```bash
# WARNING: This stops all ships and removes containers
# Backup first!
tar czf complete-backup-$(date +%Y%m%d).tar.gz piers/ .env docker-compose.yml

# Stop all containers
docker-compose down

# Remove volumes (optional, dangerous)
# docker volume prune

# Remove all containers and images
docker system prune -a

# Reinstall GroundSeg
sudo wget -O - get.groundseg.app|bash

# Restore piers
# Extract from backup
```

## Reference

- GroundSeg manual: https://manual.groundseg.app
- Docker troubleshooting: https://docs.docker.com/config/daemon/
- Docker Compose: https://docs.docker.com/compose/

## Summary

GroundSeg troubleshooting involves Docker container diagnostics (`docker ps`, `docker logs`), networking validation (StarTram/Anchor connectivity, DNS resolution), resource monitoring (`docker stats`), and systematic isolation of issues. Common problems include UI inaccessibility (port conflicts, firewall), ships not starting (permissions, memory), networking failures (tunnel issues, DNS), and performance degradation (resource limits). Always check logs first, backup before changes, and document resolutions for future reference.
