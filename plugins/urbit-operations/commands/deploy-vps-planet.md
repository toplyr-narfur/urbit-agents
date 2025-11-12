---
name: deploy-vps-planet
description: Automated VPS planet deployment workflow with provider-specific optimizations, cloud-native integrations, and production hardening
agents:
  - vps-deployment-specialist
skills:
  - urbit-fundamentals
  - ship-deployment-guide
  - vps-deployment-providers
  - network-security-advanced
  - monitoring-observability
---

# Deploy VPS Planet

Automated workflow for deploying Urbit planets on Virtual Private Server platforms with provider-specific optimizations and cloud-native integrations.

## Pre-Flight Checklist

Before executing this workflow, gather:

1. **Planet Identity**: Ship name (e.g., sampel-palnet)
2. **Keyfile**: Planet keyfile from Bridge (single-use)
3. **VPS Provider**: DigitalOcean, Linode, Vultr, AWS Lightsail, Hetzner, or OVH
4. **Domain** (optional): Custom domain for SSL/TLS
5. **Budget**: $6-24/month for single planet (Hetzner ~$6, DigitalOcean $20, AWS $24)

## Workflow Phases

### Phase 1: Provider Selection and Server Provisioning

**Interactive prompts**:
- VPS provider (DigitalOcean/Linode/Vultr/AWS Lightsail/Hetzner/OVH)
- Region selection (closest to you for lowest latency)
- Instance size (recommended: 4GB RAM, 2 vCPUs, 50GB SSD)
- SSH key configuration

**Automated actions**:
- Provision VPS instance via provider API/CLI
- Assign static IP (Floating IP, Reserved IP, or Static IP)
- Configure cloud firewall (ports 22, 80, 443, 34543/udp)
- Generate and upload SSH keys

### Phase 2: Base System Configuration

**Automated actions**:
- Connect to VPS via SSH
- Update system packages: `apt update && apt upgrade -y`
- Install dependencies: `curl`, `openssl`, `libssl-dev`, `pkg-config`, `build-essential`
- Create urbit user: `useradd -m -s /bin/bash urbit`
- Configure SSH hardening (key-only auth, disable root login)
- Install and configure UFW firewall
- Install Fail2ban with SSH jail

### Phase 3: Urbit Binary Installation

**Automated actions**:
```bash
# Download and install Urbit binary (2025)
curl -L https://urbit.org/install/linux-x86_64/latest | tar xzk --transform='s/.*/urbit/g'
sudo mv urbit /usr/local/bin/
chmod +x /usr/local/bin/urbit
urbit --version  # Verify installation
```

### Phase 4: Ship Deployment

**Interactive prompts**:
- Upload keyfile to VPS
- Confirm ship name

**Automated actions**:
```bash
# Transfer keyfile securely
scp sampel-palnet-1.key urbit@vps-ip:/home/urbit/

# Boot ship with keyfile
sudo -u urbit urbit -w sampel-palnet -k /home/urbit/sampel-palnet-1.key

# Secure keyfile deletion
shred -u /home/urbit/sampel-palnet-1.key
```

### Phase 5: Systemd Service Configuration

**Automated actions**:
- Create systemd service: `/etc/systemd/system/urbit-sampel-palnet.service`
- Enable security options: `PrivateTmp`, `ProtectSystem=strict`, `NoNewPrivileges`
- Set restart policy: `Restart=always`, `RestartSec=10`
- Enable and start service

**Service template**:
```ini
[Unit]
Description=Urbit ship: sampel-palnet
After=network.target

[Service]
Type=simple
User=urbit
WorkingDirectory=/home/urbit
ExecStart=/usr/local/bin/urbit sampel-palnet
Restart=always
RestartSec=10

# Security options
PrivateTmp=yes
ProtectSystem=strict
ReadWritePaths=/home/urbit
NoNewPrivileges=yes
MemoryDenyWriteExecute=yes

[Install]
WantedBy=multi-user.target
```

### Phase 6: VPS-Specific Optimizations

**Provider-specific actions**:

**DigitalOcean**:
- Attach Block Storage volume (optional, for pier storage)
- Configure DigitalOcean Monitoring with alert policies
- Enable automated snapshots

**Linode**:
- Configure Linode Backup Service
- Enable Longview monitoring
- Set up private VLAN (if multi-ship)

**AWS Lightsail**:
- Configure CloudWatch monitoring
- Set up automatic snapshots
- Attach static IP

**All providers**:
- Optimize I/O scheduler for SSD: `echo "none" > /sys/block/sda/queue/scheduler`
- Configure sysctl network tuning
- Enable unattended-upgrades

### Phase 7: Networking Configuration

**Interactive prompts**:
- Use arvo.network domain (automatic SSL) or custom domain?

**Option A: arvo.network (Automatic)**:
- Ship accessible at: `https://sampel-palnet.arvo.network`
- No additional configuration required

**Option B: Custom Domain**:

**Automated actions**:
- Install Nginx: `apt install nginx -y`
- Configure reverse proxy for ship
- Install Certbot: `apt install certbot python3-certbot-nginx -y`
- Obtain SSL certificate: `certbot --nginx -d ship.yourdomain.com`
- Configure auto-renewal

**Nginx config**:
```nginx
server {
    server_name ship.yourdomain.com;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/ship.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/ship.yourdomain.com/privkey.pem;
}

server {
    listen 80;
    server_name ship.yourdomain.com;
    return 301 https://$server_name$request_uri;
}
```

### Phase 8: Cloud-Native Monitoring

**Automated actions**:

**DigitalOcean Monitoring**:
- Enable built-in monitoring in Droplet settings
- Configure alert policies (CPU >80%, disk >85%)
- Set up email/SMS notifications

**Linode Longview**:
```bash
# Install Longview agent
curl -s https://lv.linode.com/<longview-id> | sudo bash
```

**AWS CloudWatch** (Lightsail):
```bash
# Install CloudWatch agent
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i amazon-cloudwatch-agent.deb
```

**Custom monitoring**:
- Install monitoring script for ship health
- Monitor pier size growth
- Alert on memory usage >80%

### Phase 9: Automated Backup Configuration

**Automated actions**:
- Enable provider backup service (DigitalOcean Snapshots, Linode Backups, AWS Lightsail Snapshots)
- Create custom backup script: `/usr/local/bin/urbit-backup.sh`
- Schedule weekly backups via cron: `0 3 * * 0`
- Configure offsite backup to provider's object storage (DigitalOcean Spaces, AWS S3)

**Backup script**:
```bash
#!/bin/bash
set -euo pipefail

SHIP_NAME="sampel-palnet"
BACKUP_DIR="/backups/urbit"
DATE=$(date +%Y%m%d-%H%M%S)

# Stop ship
systemctl stop urbit-$SHIP_NAME
sleep 30

# Backup
tar czf $BACKUP_DIR/$SHIP_NAME-$DATE.tar.gz /home/urbit/$SHIP_NAME

# Restart
systemctl start urbit-$SHIP_NAME

# Upload to cloud storage
aws s3 cp $BACKUP_DIR/$SHIP_NAME-$DATE.tar.gz s3://urbit-backups/
```

### Phase 10: Performance Tuning

**Automated actions**:
- Set I/O scheduler: `none` (NVMe) or `mq-deadline` (SATA SSD)
- Configure filesystem: `noatime,nodiratime` in `/etc/fstab`
- Set swappiness: `vm.swappiness=10`
- Schedule weekly |pack: `0 3 * * 0 docker exec ship /bin/sh -c "echo '|pack' | urbit-worker dojo"`

### Phase 11: Security Hardening

**Automated actions**:
- Configure cloud firewall (provider-managed)
- Enable UFW on VPS (defense in depth)
- Install and configure Fail2ban
- Set up automatic security updates: `dpkg-reconfigure -plow unattended-upgrades`
- Disable unnecessary services
- Configure SSH: key-only auth, no root login

### Phase 12: Disaster Recovery Setup

**Automated actions**:
- Create initial snapshot/image
- Test snapshot restoration (dry-run)
- Configure cross-region snapshot replication (if supported)
- Document recovery procedures in `/home/urbit/README-RECOVERY.md`

### Phase 13: Cost Optimization

**Automated actions**:
- Right-size instance based on baseline usage (wait 7 days, then analyze)
- Enable bandwidth monitoring
- Configure billing alerts
- Document monthly cost baseline

### Phase 14: Documentation

**Automated actions**:
Generate deployment documentation in `/home/urbit/DEPLOYMENT.md`:
- VPS provider and plan details
- IP addresses (public, private if applicable)
- Domain configuration
- Firewall rules
- Backup schedule and locations
- Monitoring endpoints
- Emergency contacts

### Phase 15: Validation and Testing

**Automated checks**:
- Ship boots successfully: `systemctl status urbit-sampel-palnet`
- Dojo accessible via SSH: `urbit attach sampel-palnet`
- Web interface accessible: `curl https://sampel-palnet.arvo.network` or custom domain
- Ames connectivity: Test with `|hi ~zod` in dojo
- Firewall rules active: `ufw status`
- SSL certificate valid: `certbot certificates`
- Monitoring operational: Check provider dashboard
- Backups configured: Verify cron job with `crontab -l`

### Phase 16: Handoff

**Final report**:
```
=== VPS Planet Deployment Complete ===

Ship: sampel-palnet
Provider: DigitalOcean (or selected provider)
Region: NYC1 (or selected region)
IP: 123.45.67.89
Domain: https://sampel-palnet.arvo.network
          OR https://ship.yourdomain.com

Access:
  SSH: ssh urbit@123.45.67.89
  Dojo: urbit attach sampel-palnet
  Web: https://sampel-palnet.arvo.network

Monitoring: https://cloud.digitalocean.com/monitoring
Backups: Weekly (Sundays 3 AM) + Provider snapshots
Cost: $20/month (Basic 4GB Droplet) + $1/GB/month (Snapshots)

Next steps:
1. Log in to ship via web interface
2. Complete initial configuration in Landscape
3. Join groups, install apps
4. Monitor pier size growth (weekly)
5. Review monthly costs

Emergency contacts:
- Provider support: [provider support URL]
- Urbit help: https://urbit.org/get-started
```

## Configuration Options

**Required**:
- VPS provider
- Ship name
- Keyfile path
- SSH key

**Optional**:
- Custom domain
- Block storage (DigitalOcean/Linode)
- Multi-region snapshots
- Enhanced monitoring
- Private networking (multi-ship setups)

## Best Practices

1. **Static IP**: Always use (prevents IP changes on restart)
2. **Block storage**: Use for production (data persistence across instance changes)
3. **Automated backups**: Enable both provider backups AND custom offsite backups
4. **Monitoring**: Use provider monitoring (free) + custom ship health checks
5. **Firewall**: Cloud firewall (managed) + UFW (defense in depth)
6. **SSH keys**: Never use password authentication
7. **Update automation**: Enable unattended-upgrades for security patches
8. **Snapshot testing**: Quarterly disaster recovery drills
9. **Cost monitoring**: Review monthly, rightsize after 30 days
10. **Documentation**: Keep deployment details in `/home/urbit/DEPLOYMENT.md`

## Troubleshooting

If deployment fails:

1. **Instance provisioning fails**: Check provider API credentials, quota limits
2. **SSH connection fails**: Verify firewall allows port 22, check SSH key
3. **Ship won't boot**: Check logs with `journalctl -u urbit-sampel-palnet -n 50`
4. **SSL certificate fails**: Verify DNS points to VPS IP, check Certbot logs
5. **Monitoring not working**: Check agent installation, verify API keys

Use `/troubleshoot-ship` command for ship-specific issues.

## Cross-References

- **vps-deployment-specialist**: Agent with VPS expertise
- **deploy-planet**: Base bare-metal deployment command
- **setup-production**: Production hardening workflow
- **vps-deployment-providers**: Detailed provider comparisons
- **network-security-advanced**: Advanced VPS security configurations
- **monitoring-observability**: Cloud-native monitoring integration

## Summary

Automated VPS planet deployment combines cloud provider APIs for provisioning, base system hardening (SSH, firewall, Fail2ban), Urbit ship deployment with systemd service, provider-specific optimizations (block storage, monitoring, snapshots), SSL/TLS configuration (arvo.network or custom domain), automated backups, and comprehensive validation. VPS deployments leverage cloud-native features while maintaining infrastructure-as-code practices for reproducibility.
