# GroundSeg Installation Skill

Complete installation and initial configuration of the GroundSeg container orchestration platform for multi-ship Urbit deployments.

## Overview

GroundSeg is a Docker-based platform for managing multiple Urbit ships with built-in web UI, StarTram/Anchor networking, MinIO S3 storage integration, and automated ship lifecycle management.

## Prerequisites

- Ubuntu 22.04+ or Debian 11+ server
- 4GB+ RAM (2GB per ship recommended)
- 50GB+ storage (25GB per ship recommended)
- Public IP or NAT with port forwarding
- Root or sudo access

## Installation Methods

### Method 1: One-Line Install (2025 Official)

```bash
# Download and run installation script
sudo wget -O - get.groundseg.app|bash

# Script performs:
# - Docker and Docker Compose installation
# - GroundSeg application download
# - Initial configuration
# - Service startup
```

### Method 2: Manual Installation

```bash
# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Install Docker Compose
sudo apt install docker-compose -y

# Download GroundSeg
git clone https://github.com/Native-Planet/GroundSeg.git
cd GroundSeg

# Configure environment
cp .env.example .env
nano .env  # Edit configuration

# Start GroundSeg
docker-compose up -d
```

## Post-Installation Configuration

### Access GroundSeg UI

**Local network**:
```
http://nativeplanet.local
```

**VPS/cloud** (if local domain doesn't resolve):
```
http://<server-public-ip>:3000
```

### Initial Setup Wizard

1. **Admin account**: Create username/password
2. **Network configuration**: Choose StarTram (managed) or Anchor (self-hosted)
3. **Storage configuration**: Local or MinIO S3
4. **Ship deployment**: Add first ship (planet, comet, or fake)

## Directory Structure

```
/opt/groundseg/             # Default installation
├── docker-compose.yml      # Container orchestration
├── .env                    # Environment configuration
├── piers/                  # Ship data (piers)
│   ├── sampel-palnet/     # Individual ship pier
│   └── other-ship/
├── minio-data/            # MinIO S3 storage (if enabled)
└── logs/                  # Application logs
```

## Firewall Configuration

```bash
# Essential ports
sudo ufw allow 22/tcp       # SSH
sudo ufw allow 80/tcp       # HTTP
sudo ufw allow 443/tcp      # HTTPS
sudo ufw allow 34543/udp    # Ames (each ship)
sudo ufw allow 3000/tcp     # GroundSeg UI

# Enable firewall
sudo ufw enable
```

## Networking Options

### StarTram (Managed - Easiest)

1. Access GroundSeg UI
2. Navigate to Networking settings
3. Select "StarTram"
4. Enter registration code (from Native Planet)
5. Ships automatically get: `<ship-name>.arvo.network` domains with SSL

### Anchor (Self-Hosted)

1. Deploy Anchor VPS separately (see anchor-networking skill)
2. Configure GroundSeg to use Anchor VPS
3. Set up custom domains pointing to Anchor VPS
4. Configure reverse tunnels or VPN connection

## Storage Options

### Local Storage (Default)

```yaml
# docker-compose.yml
volumes:
  - ./piers:/opt/piers
```

### MinIO S3 (Recommended for Production)

**Option A: One-Click UI Activation** (2025):
1. Access GroundSeg UI
2. Navigate to Storage settings
3. Click "Enable MinIO"
4. Automatic Docker container deployment
5. S3-compatible storage ready

**Option B: Manual Docker Configuration**:
```yaml
# docker-compose.yml
services:
  minio:
    image: minio/minio
    ports:
      - "9000:9000"
      - "9001:9001"
    volumes:
      - ./minio-data:/data
    environment:
      MINIO_ROOT_USER: admin
      MINIO_ROOT_PASSWORD: <strong-password>
    command: server /data --console-address ":9001"
```

## Systemd Service (Auto-Start)

```bash
# Create systemd service
sudo nano /etc/systemd/system/groundseg.service
```

```ini
[Unit]
Description=GroundSeg Multi-Ship Orchestration
After=docker.service
Requires=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/opt/groundseg
ExecStart=/usr/bin/docker-compose up -d
ExecStop=/usr/bin/docker-compose down

[Install]
WantedBy=multi-user.target
```

```bash
# Enable and start
sudo systemctl enable groundseg
sudo systemctl start groundseg
```

## Verification

```bash
# Check containers running
docker ps

# Should show:
# - groundseg-ui (web interface)
# - urbit-ship containers (one per ship)
# - minio (if S3 enabled)

# Check logs
docker-compose logs -f

# Access UI
# Browser: http://nativeplanet.local (or http://<ip>:3000)
```

## Common Issues

### Issue: UI not accessible

```bash
# Check container status
docker ps | grep groundseg

# Check logs
docker logs groundseg-ui

# Verify port 3000 not in use
sudo netstat -tulpn | grep 3000
```

### Issue: Ships won't start

```bash
# Check ship container logs
docker logs <ship-container-name>

# Check available disk space
df -h

# Check RAM usage
free -h
```

### Issue: MinIO not starting

```bash
# Check MinIO container
docker ps | grep minio

# Check MinIO logs
docker logs minio

# Verify ports 9000/9001 available
sudo netstat -tulpn | grep -E '(9000|9001)'
```

## Upgrade Procedure

```bash
# Stop GroundSeg
cd /opt/groundseg
docker-compose down

# Backup configuration
tar czf groundseg-backup-$(date +%Y%m%d).tar.gz .env piers/

# Pull latest version
git pull origin main

# Update containers
docker-compose pull
docker-compose up -d
```

## Best Practices

1. **Separate VPS for Anchor**: If using self-hosted networking
2. **Backup regularly**: Piers + configuration + MinIO data
3. **Monitor resources**: 2GB RAM per ship minimum
4. **Use SSD storage**: Significantly improves pier I/O performance
5. **Enable MinIO for production**: S3-compatible storage, scalability
6. **Firewall hardening**: Only open necessary ports
7. **Update regularly**: Check for GroundSeg updates monthly

## Reference

- Official manual: https://manual.groundseg.app
- Installation: `get.groundseg.app`
- UI access: `http://nativeplanet.local`
- GitHub: https://github.com/Native-Planet/GroundSeg

## Summary

GroundSeg installation via one-line command (`sudo wget -O - get.groundseg.app|bash`) sets up Docker-based multi-ship orchestration. Access UI at `http://nativeplanet.local`, configure networking (StarTram or Anchor), enable MinIO S3 storage (one-click in UI), and deploy ships through web interface. Requires firewall configuration for Ames (34543/udp per ship) and systemd service for auto-start. Monitor with `docker ps` and `docker-compose logs`.
