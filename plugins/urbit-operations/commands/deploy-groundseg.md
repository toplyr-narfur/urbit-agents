---
name: deploy-groundseg
description: Complete GroundSeg + networking deployment workflow for multi-ship container orchestration with 20 orchestrated phases
subagent_type: groundseg-operator
---

# Deploy GroundSeg Command

Complete orchestration workflow for deploying GroundSeg container platform with networking (StarTram or self-hosted Anchor) and MinIO S3 storage for multi-ship Urbit hosting.

## Purpose

This command guides operators through a comprehensive 20-phase deployment workflow for GroundSeg, from hardware assessment through comprehensive monitoring and validation. It supports both managed networking (StarTram) and self-hosted networking (Anchor).

## Configuration Options

### VPS Provider
- **DigitalOcean** - Popular, good docs, $6-12/month
- **Linode** - Reliable, simple pricing
- **Vultr** - Global locations, competitive pricing
- **Hetzner** - Excellent value, EU-based
- **Custom** - Any Ubuntu 22.04+ VPS

### Number of Ships
- **1-3 ships** - Basic setup (8GB RAM recommended)
- **4-10 ships** - Standard fleet (16GB RAM)
- **10+ ships** - Large fleet (32GB+ RAM, capacity planning)

### Networking Option
- **StarTram** (Managed) - Registration code entry, automatic configuration, easiest
- **Anchor** (Self-hosted) - Requires separate VPS + domain, full control, privacy-first
- **Both** - StarTram for convenience + Anchor for critical ships

### Storage
- **Local pier only** - Ships store data on VPS disk
- **MinIO S3** - Self-hosted object storage for media (one-click activation)

### Monitoring Level
- **Basic** - htop, system logs
- **Intermediate** - Netdata dashboard
- **Comprehensive** - Prometheus + Grafana (full observability)

## 20-Phase Workflow

### Phase 1: Hardware Assessment

**Objective**: Size infrastructure for ship count and determine VPS requirements.

**Resource Calculation**:
```
Per Ship Requirements:
- RAM: 2GB minimum, 3GB recommended
- CPU: 1 core (0.5-1.0 dedicated)
- Disk: 30GB minimum, 50GB recommended (SSD required)

Fleet Sizing:
- 1 ship: 4GB RAM, 1 CPU, 50GB SSD (basic $6/month VPS)
- 3 ships: 8GB RAM, 2 CPU, 150GB SSD ($12/month)
- 5 ships: 16GB RAM, 4 CPU, 250GB SSD ($24/month)
- 10 ships: 32GB RAM, 8 CPU, 500GB SSD ($48/month)

System Overhead:
- GroundSeg platform: +2GB RAM
- MinIO (if enabled): +1GB RAM, +20GB disk
- Docker: +512MB RAM
```

**Actions**:
1. Determine ship count (current + 6-12 month growth)
2. Calculate total resource requirements
3. Select appropriate VPS tier
4. Verify SSD storage (not HDD - critical for performance)
5. Plan for 20-30% overhead for growth

**Success Criteria**:
- VPS size selected with 20% headroom
- SSD storage confirmed
- Budget approved ($6-50/month typical)

---

### Phase 2: VPS Provisioning

**Objective**: Create and configure VPS instance.

**Actions**:
1. Create VPS account (DigitalOcean, Vultr, etc.)
2. Provision droplet/instance:
   - **OS**: Ubuntu 22.04 LTS (required)
   - **Region**: Choose nearest to primary users
   - **Storage**: SSD only (NVMe preferred)
   - **Backups**: Enable automatic backups (recommended)
3. Note public IP address
4. Configure SSH key authentication
5. Document VPS credentials securely

**Success Criteria**:
- VPS accessible via SSH
- Ubuntu 22.04 installed
- Public IP noted
- SSH key authentication working

**Security First**:
```bash
# Immediately after VPS creation, secure SSH
ssh root@your-vps-ip

# Change SSH port (optional but recommended)
sudo nano /etc/ssh/sshd_config
# Change: Port 2222 (or other non-standard)

# Restart SSH
sudo systemctl restart sshd
```

---

### Phase 3: Base System Configuration

**Objective**: Update system and configure firewall.

**Actions**:

**System Updates**:
```bash
# Update package lists
sudo apt update

# Upgrade all packages
sudo apt upgrade -y

# Install essential tools
sudo apt install -y \
  curl \
  wget \
  git \
  htop \
  ufw \
  ca-certificates
```

**Firewall Configuration (UFW)**:
```bash
# Default deny incoming, allow outgoing
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH (adjust port if changed)
sudo ufw allow 22/tcp  # or 2222/tcp if custom

# Allow GroundSeg UI
sudo ufw allow 3000/tcp

# Allow HTTP/HTTPS (for ship access)
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Allow Ames (Urbit networking)
sudo ufw allow 34543/udp

# Enable firewall
sudo ufw enable

# Verify rules
sudo ufw status verbose
```

**Success Criteria**:
- System fully updated
- Essential tools installed
- UFW enabled with correct rules
- Able to SSH after firewall enabled

---

### Phase 4: Docker Installation

**Objective**: Install Docker Engine (required for GroundSeg).

**Actions**:
```bash
# Docker will install automatically via GroundSeg installer
# But verify system is ready:

# Check kernel version (should be 5.x+)
uname -r

# Verify systemd
systemctl --version
```

**Note**: GroundSeg installer (`get.groundseg.app`) automatically installs Docker if not present. Manual Docker installation is optional.

**If Installing Docker Manually** (optional):
```bash
# Official Docker installation
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add current user to docker group
sudo usermod -aG docker $USER
newgrp docker

# Enable Docker service
sudo systemctl enable docker
sudo systemctl start docker

# Verify installation
docker --version
docker run hello-world
```

**Success Criteria**:
- System ready for Docker
- Systemd operational
- Kernel version 5.x+

---

### Phase 5: GroundSeg Installation

**Objective**: Install GroundSeg platform.

**Actions**:

**One-Line Installation** (2025 official method):
```bash
# Install GroundSeg (includes Docker if not present)
sudo wget -O - get.groundseg.app|bash

# Wait for installation (2-5 minutes)
# Script will:
# 1. Install Docker (if needed)
# 2. Download GroundSeg binary
# 3. Create systemd service
# 4. Start GroundSeg

# Verify installation
groundseg --version
```

**Alternative: GroundSeg Only** (if Docker already installed):
```bash
sudo wget -O - only.groundseg.app|bash
```

**Success Criteria**:
- GroundSeg binary installed
- Systemd service created
- Docker running (verify: `docker ps`)
- GroundSeg service active: `sudo systemctl status groundseg`

---

### Phase 6: GroundSeg Service Verification

**Objective**: Ensure GroundSeg platform is operational.

**Actions**:

**Check Service Status**:
```bash
# Verify GroundSeg service
sudo systemctl status groundseg

# Should show: active (running)

# Check Docker containers
docker ps -a

# View GroundSeg logs
sudo journalctl -u groundseg -n 50
```

**Access Web UI**:
- **Local Network**: Navigate to `http://nativeplanet.local` in browser
- **Via IP**: Navigate to `http://your-vps-ip:3000`

**Initial Setup**:
1. Web UI should load (GroundSeg dashboard)
2. Create admin credentials (first-time setup)
3. Verify dashboard accessible

**Success Criteria**:
- GroundSeg service running
- Web UI accessible
- Admin account created
- Dashboard functional

---

### Phase 7: Networking Option Selection

**Objective**: Choose between StarTram (managed) or Anchor (self-hosted) networking.

**Decision Matrix**:

| Feature | StarTram (Managed) | Anchor (Self-hosted) |
|---------|-------------------|---------------------|
| Setup Complexity | ⭐ Easy (code entry) | ⭐⭐⭐ Complex (VPS setup) |
| Cost | $10/month subscription | VPS cost ($5-10/month) |
| Privacy | Managed service | Full self-hosted |
| Control | Limited | Complete |
| Maintenance | None | Manual updates |
| Best For | Convenience, quick setup | Privacy, full control |

**StarTram Setup** → **Proceed to Phase 8**
**Anchor Setup** → **Proceed to Phase 8A (separate track)**
**Both** → **Do Phase 8 now, Phase 8A later**

---

### Phase 8: StarTram Network Setup (Managed Option)

**Objective**: Configure managed StarTram networking.

**Prerequisites**:
- StarTram subscription ($10/month)
- Registration code from Native Planet

**Actions**:

1. **Obtain Registration Code**:
   - Purchase StarTram subscription at nativeplanet.io/startram
   - Receive registration code via email

2. **Configure in GroundSeg**:
   - Open GroundSeg UI: `http://nativeplanet.local`
   - Navigate to **Settings** → **StarTram**
   - Enter registration code
   - Click "Activate"
   - Wait for confirmation (30-60 seconds)

3. **Verify Activation**:
   - StarTram status should show "Active"
   - Your unique HTTPS URL will be displayed (e.g., `https://your-id.startram.io`)
   - Test access from external network (phone, remote computer)

**What StarTram Provides**:
- Automatic HTTPS access to all ships
- No DNS configuration needed
- No port forwarding required
- Automatic SSL certificates
- Remote access from anywhere

**Success Criteria**:
- StarTram registration successful
- HTTPS URL accessible externally
- No manual networking configuration needed

**Skip to Phase 9** (MinIO setup)

---

### Phase 8A: Anchor Network Setup (Self-Hosted Option)

**Objective**: Deploy self-hosted Anchor networking (StarTram alternative).

**Prerequisites**:
- Separate VPS for Anchor (Ubuntu 22.04, 1GB RAM, $5/month)
- Domain name (e.g., anchor.yourdomain.com)

**Actions**:

**Step 1: Domain Configuration**:
```bash
# Point domain to Anchor VPS IP
# Create A record:
# anchor.yourdomain.com  A  <anchor-vps-ip>

# Verify DNS propagation
nslookup anchor.yourdomain.com
```

**Step 2: Anchor VPS Setup**:
```bash
# SSH into Anchor VPS
ssh root@anchor-vps-ip

# Clone Anchor repository
git clone https://github.com/Native-Planet/anchor
cd anchor

# Run installer (requires Python 3 + Ansible)
./install.sh

# Follow prompts:
# - Enter domain: anchor.yourdomain.com
# - Configure SSL (automatic Let's Encrypt)
# - Wait for Ansible playbook completion (5-10 minutes)
```

**Step 3: Configure GroundSeg to Use Anchor**:
- Open GroundSeg UI
- Navigate to **Settings** → **Anchor**
- Select "Custom Endpoint"
- Enter: `anchor.yourdomain.com`
- Save configuration

**Step 4: Verify Connection**:
- Check Anchor status in GroundSeg UI (should show "Connected")
- Test HTTPS access: `https://ship-name.anchor.yourdomain.com`

**Success Criteria**:
- Anchor VPS deployed and operational
- Domain resolves to Anchor VPS
- GroundSeg connected to Anchor
- Ships accessible via custom domain

**Anchor Advantages**:
- Full control over networking infrastructure
- Self-hosted (no third-party service)
- Open source
- No subscription fees (only VPS cost)

---

### Phase 9: MinIO S3 Installation (Optional)

**Objective**: Deploy self-hosted object storage for ship media.

**Benefits of MinIO**:
- Keeps media out of piers (smaller piers = better performance)
- Ships can store images, files in S3 buckets
- Self-hosted (data stays on your device)
- S3-compatible API (works with many apps)

**Actions**:

**Option A: One-Click Activation (Easiest - Recommended)**:
1. Open GroundSeg UI: `http://nativeplanet.local`
2. Navigate to **Settings** → **S3 Storage**
3. Click **"Enable MinIO"**
4. Wait for automatic deployment (1-2 minutes)
5. MinIO credentials displayed in UI (save securely)

**Option B: Manual Docker Deployment** (if you need custom config):
```bash
# Create MinIO data directory
sudo mkdir -p /opt/minio/data
sudo chown 1000:1000 /opt/minio/data

# Generate secure credentials
MINIO_ROOT_USER=admin
MINIO_ROOT_PASSWORD=$(openssl rand -base64 32)

# Save credentials
echo "MinIO User: $MINIO_ROOT_USER" > ~/minio-creds.txt
echo "MinIO Password: $MINIO_ROOT_PASSWORD" >> ~/minio-creds.txt
chmod 600 ~/minio-creds.txt

# Deploy MinIO container
docker run -d \
  --name minio \
  --restart always \
  -p 9000:9000 \
  -p 9001:9001 \
  -e "MINIO_ROOT_USER=$MINIO_ROOT_USER" \
  -e "MINIO_ROOT_PASSWORD=$MINIO_ROOT_PASSWORD" \
  -v /opt/minio/data:/data \
  minio/minio server /data --console-address ":9001"

# Verify running
docker ps | grep minio
```

**Access MinIO Console**:
- URL: `http://your-vps-ip:9001`
- Login with saved credentials
- Create buckets as needed (or auto-created per ship)

**Success Criteria**:
- MinIO service running
- Console accessible
- Credentials saved securely
- Ships can use S3 storage (verify in ship settings)

**Skip MinIO**: If ships won't store much media, you can skip this phase.

---

### Phase 10: First Ship Deployment (Pilot)

**Objective**: Deploy pilot ship to validate platform before full fleet deployment.

**Actions**:

1. **Obtain Ship Keyfile** (for planet):
   - Download from Bridge (bridge.urbit.org)
   - Or from hosting provider
   - Save securely to local machine

2. **Boot Ship via GroundSeg UI**:
   - Open GroundSeg: `http://nativeplanet.local`
   - Click **"Boot New Ship"** or **"+"** button
   - Select ship type:
     - **"Boot Existing Urbit ID"** (planet with keyfile)
     - **"Boot New Comet"** (free, no keyfile)
   - Upload keyfile (planets only)
   - Enter ship name (e.g., sampel-palnet)
   - Allocate resources:
     - RAM: 2-3GB
     - Disk: 50GB
   - Assign HTTP port: 8080 (first ship)
   - Click "Boot"

3. **Monitor Boot Process**:
   - GroundSeg shows boot progress
   - Initial sync: 10-30 minutes (planet), 5-15 min (comet)
   - Status indicator: "Booting" → "Running"

4. **Access Ship**:
   - **Via GroundSeg UI**: Click ship name → "Open Ship"
   - **Via StarTram**: `https://sampel-palnet.your-startram-url.io`
   - **Via Anchor**: `https://sampel-palnet.anchor.yourdomain.com`
   - **Direct**: `http://your-vps-ip:8080`

5. **Verify Dojo Access**:
   - GroundSeg UI → Ship Controls → "Dojo"
   - Or click "Launch Terminal" in ship dashboard
   - Test command: `+vats` (lists running agents)

**Success Criteria**:
- Ship boots successfully
- Initial sync completes
- Ship accessible via chosen networking method
- Dojo commands responsive
- No errors in GroundSeg logs

**Troubleshooting**:
- Boot fails: Check GroundSeg logs (`docker logs <container-id>`)
- Can't access ship: Verify firewall rules, networking configuration
- Slow boot: Normal for first boot; large piers take longer

---

### Phase 11: Multi-Ship Configuration

**Objective**: Configure resource allocation and port management for fleet.

**Port Assignment Strategy**:
```
Ship 1: HTTP 8080
Ship 2: HTTP 8081
Ship 3: HTTP 8082
...
Ship N: HTTP 8080+(N-1)

Ames: All ships share UDP 34543 (NAT handled by GroundSeg)
```

**Resource Allocation Per Ship**:
```yaml
# Standard allocation (2GB RAM, 1 CPU)
Ship Template:
  RAM: 2GB
  CPU: 1 core
  Disk: 50GB
  HTTP Port: 8080+N

# High-performance (for active ships)
Premium Template:
  RAM: 4GB
  CPU: 2 cores
  Disk: 100GB
  HTTP Port: 8080+N
```

**Create Resource Plan**:
Document in `~/fleet-resources.md`:
```markdown
# Fleet Resource Allocation

## Ship 1: ~sampel-palnet
- RAM: 2GB
- CPU: 1 core
- Disk: 50GB
- HTTP Port: 8080
- Owner: Primary user

## Ship 2: ~zod
- RAM: 3GB
- CPU: 1 core
- Disk: 50GB
- HTTP Port: 8081
- Owner: Team member

... (repeat for all ships)

## Total Allocated
- RAM: 10GB / 16GB available (62% utilized)
- CPU: 5 cores / 8 available (62% utilized)
- Disk: 250GB / 500GB available (50% utilized)
```

**Success Criteria**:
- Port assignments documented
- Resource allocations planned
- No port conflicts
- Capacity headroom maintained (20%+)

---

### Phase 12: Fleet Deployment

**Objective**: Deploy remaining ships after pilot validation.

**Deployment Strategy**:
- Deploy in batches (3-5 ships at a time)
- Validate each batch before proceeding
- Monitor resource usage between batches

**Actions**:

**For Each Ship**:
1. Boot ship via GroundSeg UI (same process as Phase 10)
2. Assign unique HTTP port (8081, 8082, etc.)
3. Monitor boot progress
4. Verify accessibility
5. Test basic functionality

**Batch Deployment Example**:
```
Batch 1 (Ships 2-4):
- Boot ship 2 → Wait for sync → Verify
- Boot ship 3 → Wait for sync → Verify
- Boot ship 4 → Wait for sync → Verify
- Monitor resource usage (RAM, CPU, disk I/O)

If resources stable, proceed to Batch 2
```

**Monitor System Resources**:
```bash
# Real-time monitoring
htop  # Press F5 for tree view, see Docker containers

# Docker stats
docker stats  # CPU, RAM per container

# Disk usage
df -h /  # Check free space
du -sh /opt/groundseg/piers/*  # Per-ship pier sizes
```

**Success Criteria**:
- All ships deployed successfully
- Resource utilization <80% (RAM, CPU, disk)
- All ships accessible via networking
- No system instability or crashes

---

### Phase 13: Container Security Hardening

**Objective**: Implement production security for ship containers.

**Actions**:

**1. Enable User Namespace Remapping** (Docker daemon):
```bash
# Edit Docker daemon config
sudo nano /etc/docker/daemon.json

# Add user namespace remapping
{
  "userns-remap": "default",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}

# Restart Docker
sudo systemctl restart docker

# Note: GroundSeg will need to be restarted
sudo systemctl restart groundseg
```

**2. GroundSeg Security Settings**:
- In GroundSeg UI → Settings → Security
- Enable options (if available):
  - Container isolation
  - Resource limits enforcement
  - Network isolation

**3. Firewall Hardening**:
```bash
# Restrict GroundSeg UI to local network only (if desired)
sudo ufw delete allow 3000/tcp
sudo ufw allow from 192.168.1.0/24 to any port 3000

# Limit SSH rate (prevent brute force)
sudo ufw limit 22/tcp
```

**4. Regular Security Updates**:
```bash
# Enable automatic security updates
sudo apt install unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades
```

**Success Criteria**:
- User namespace remapping active
- Docker logs rotated (prevent disk fill)
- Firewall rules hardened
- Automatic updates enabled
- GroundSeg UI access controlled

---

### Phase 14: Performance Optimization

**Objective**: Tune system for optimal multi-ship performance.

**Actions**:

**I/O Scheduler Optimization** (SSD):
```bash
# Check current scheduler
cat /sys/block/sda/queue/scheduler

# Set to deadline (good for SSDs with Docker)
echo deadline | sudo tee /sys/block/sda/queue/scheduler

# Make persistent
echo 'ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/scheduler}="deadline"' | \
  sudo tee /etc/udev/rules.d/60-scheduler.rules

# For NVMe
echo none | sudo tee /sys/block/nvme0n1/queue/scheduler
```

**Swap Configuration**:
```bash
# Configure swap (1.5× RAM recommended)
sudo fallocate -l 24G /swapfile  # For 16GB RAM VPS
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# Tune swappiness for containers
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

**Docker Storage Driver**:
```bash
# Verify overlay2 (recommended)
docker info | grep "Storage Driver"

# Should show: overlay2
```

**Success Criteria**:
- I/O scheduler optimized for SSD/NVMe
- Swap configured and active
- Swappiness tuned (10)
- overlay2 storage driver confirmed

---

### Phase 15: Monitoring Infrastructure Deployment

**Objective**: Set up monitoring based on selected level.

**Option A: Basic Monitoring** (htop + system logs):
```bash
# Install htop
sudo apt install htop

# View real-time resources
htop  # F5 for tree view

# View GroundSeg logs
sudo journalctl -u groundseg -f

# View Docker logs
docker logs -f <container-id>
```

**Option B: Intermediate Monitoring** (Netdata):
```bash
# Install Netdata
wget -O /tmp/netdata-kickstart.sh https://my-netdata.io/kickstart.sh
sh /tmp/netdata-kickstart.sh

# Access dashboard: http://your-vps-ip:19999
```

**Option C: Comprehensive Monitoring** (Prometheus + Grafana):
- Detailed setup in **setup-monitoring** command
- Deploys full observability stack
- Alerting, dashboards, long-term metrics

**Success Criteria**:
- Monitoring tools operational
- System resource metrics visible
- Per-ship metrics available (intermediate/comprehensive)
- Alerts configured (comprehensive only)

---

### Phase 16: Backup Procedures

**Objective**: Document and test safe backup strategy.

**CRITICAL PIER BACKUP WARNING**:
```
⚠️  NEVER BACKUP LIVE PIERS ⚠️

Docker containers must be stopped before pier backup.
Backing up running piers will corrupt the event log.
```

**Safe Backup Procedure**:
```bash
#!/bin/bash
# safe-backup-all-ships.sh

# Stop all ship containers
docker stop $(docker ps -q)

# Wait for complete shutdown
sleep 30

# Backup piers
BACKUP_DIR="/opt/backups/$(date +%Y%m%d-%H%M%S)"
mkdir -p $BACKUP_DIR

# Backup each pier
for pier in /opt/groundseg/piers/*; do
  ship_name=$(basename $pier)
  tar czf $BACKUP_DIR/$ship_name.tar.gz -C /opt/groundseg/piers $ship_name
done

# Backup GroundSeg configuration
cp -r /opt/nativeplanet/groundseg/settings $BACKUP_DIR/gs-settings

# Restart ships
docker start $(docker ps -a -q)

echo "Backup complete: $BACKUP_DIR"
```

**Backup Schedule**:
- Weekly: Full pier backups (Sunday 3am)
- Daily: Configuration backups
- Monthly: Offsite backup sync

**Restoration Test** (quarterly):
```bash
# Test restore procedure
# 1. Stop test ship
# 2. Restore from backup
# 3. Verify ship boots correctly
```

**Success Criteria**:
- Backup script created and tested
- Automated backup schedule configured (cron/systemd timer)
- Offsite backup location configured
- Restoration procedure documented and tested

---

### Phase 17: Disaster Recovery Planning

**Objective**: Create runbook for VPS failure scenarios.

**Disaster Scenarios**:

**Scenario 1: VPS Disk Failure**
- Restore piers from backup to new VPS
- Reconfigure GroundSeg
- Update DNS (if using Anchor)
- Restart ships

**Scenario 2: Complete VPS Loss**
- Provision new VPS (same specs)
- Re-run phases 3-6 (system setup, Docker, GroundSeg)
- Restore piers from offsite backup
- Reconfigure networking
- Validate all ships operational

**Scenario 3: Single Ship Corruption**
- Stop corrupted ship
- Restore pier from last known good backup
- Restart ship
- Verify sync and functionality

**Create DR Runbook** (`~/disaster-recovery.md`):
```markdown
# GroundSeg Disaster Recovery Runbook

## Emergency Contacts
- VPS Provider Support: [contact info]
- DNS Provider: [contact info]
- Backup Location: [path/URL]

## Critical Information
- VPS Specs: [details]
- StarTram/Anchor Config: [details]
- Ship Count: [N]
- Backup Schedule: Weekly, Sunday 3am
- Last Successful Backup: [check /opt/backups/latest]

## Recovery Procedures
[Detailed steps for each scenario]
```

**Success Criteria**:
- DR runbook created and reviewed
- Emergency contacts documented
- Recovery procedures tested (simulation)
- Team trained on DR process

---

### Phase 18: Documentation Creation

**Objective**: Create comprehensive operational documentation.

**Documents to Create**:

**1. Fleet Architecture Diagram**:
```
[Internet]
    ↓
[StarTram/Anchor]
    ↓
[GroundSeg VPS - 16GB RAM, 4 CPU, 500GB SSD]
    ├── Ship 1: ~sampel-palnet (8080)
    ├── Ship 2: ~zod (8081)
    ├── Ship 3: ~marzod (8082)
    ├── Ship 4: ~fintud (8083)
    └── Ship 5: ~rovnys (8084)

[MinIO S3]
    ├── ship1-media/
    ├── ship2-media/
    └── ...
```

**2. Ship Inventory** (`~/fleet-inventory.md`):
```markdown
| Ship | Patp | HTTP Port | RAM | Disk | Owner | Purpose |
|------|------|-----------|-----|------|-------|---------|
| 1 | ~sampel-palnet | 8080 | 2GB | 50GB | Admin | Primary |
| 2 | ~zod | 8081 | 3GB | 50GB | User1 | Dev |
...
```

**3. Maintenance Procedures** (`~/maintenance-runbook.md`):
```markdown
## Weekly Maintenance
- [ ] Check GroundSeg logs for errors
- [ ] Verify all ships running (`docker ps`)
- [ ] Monitor disk usage (`df -h`)
- [ ] Review resource usage (`htop`, `docker stats`)

## Monthly Maintenance
- [ ] Update GroundSeg (`sudo systemctl restart groundseg`)
- [ ] Update VPS packages (`sudo apt update && sudo apt upgrade`)
- [ ] Run |pack in all ships (memory optimization)
- [ ] Test backup restoration
- [ ] Review security logs

## Quarterly Maintenance
- [ ] Full DR drill
- [ ] Security audit
- [ ] Capacity planning review
- [ ] Documentation updates
```

**4. Troubleshooting Guide** (`~/troubleshooting.md`):
Common issues and resolutions

**Success Criteria**:
- All 4 documentation files created
- Architecture diagram accurate
- Ship inventory complete
- Maintenance procedures clear
- Troubleshooting guide helpful

---

### Phase 19: Operational Training

**Objective**: Train team on GroundSeg operations.

**Training Topics**:

1. **Basic Operations**:
   - Accessing GroundSeg UI
   - Starting/stopping ships
   - Viewing logs
   - Monitoring resources

2. **Ship Management**:
   - Booting new ships
   - Accessing dojo
   - Running |pack (memory optimization)
   - Handling OTA updates

3. **Troubleshooting**:
   - Ship won't boot
   - Container restarts
   - High resource usage
   - Network connectivity issues

4. **Backup & Recovery**:
   - Running manual backup
   - Verifying backup integrity
   - Restoration procedure

5. **Emergency Procedures**:
   - Incident escalation
   - DR activation
   - Emergency contacts

**Training Materials**:
- Live demo of GroundSeg UI
- Hands-on practice with test ship
- Review of runbooks and documentation
- Q&A session

**Success Criteria**:
- All operators trained
- Training materials created
- Team comfortable with basic operations
- Emergency procedures understood

---

### Phase 20: Comprehensive Validation

**Objective**: End-to-end testing and handoff.

**Validation Checklist**:

**Platform Health**:
- [ ] GroundSeg service running (`sudo systemctl status groundseg`)
- [ ] All ship containers running (`docker ps`)
- [ ] No errors in GroundSeg logs (`sudo journalctl -u groundseg -n 100`)
- [ ] Docker daemon healthy (`docker info`)

**Ship Functionality**:
- [ ] All ships accessible via networking (StarTram/Anchor)
- [ ] Dojo responsive in all ships (<3 seconds)
- [ ] Web interfaces load correctly
- [ ] Ships can communicate (Ames networking functional)

**Networking Validation**:
- [ ] StarTram/Anchor status: Active
- [ ] HTTPS certificates valid
- [ ] External access working (test from phone/remote computer)
- [ ] Firewall rules correct (`sudo ufw status`)

**Storage Validation**:
- [ ] MinIO operational (if enabled)
- [ ] Disk usage reasonable (<70%)
- [ ] Backup system functional
- [ ] Offsite backups verified

**Security Validation**:
- [ ] SSH key-only authentication
- [ ] Automatic security updates enabled
- [ ] User namespace remapping active
- [ ] Firewall hardened

**Performance Baseline**:
```bash
# Record baseline metrics
echo "GroundSeg Deployment Baseline - $(date)" > ~/baseline-metrics.txt
echo "VPS: $(cat /proc/cpuinfo | grep 'model name' | head -1)" >> ~/baseline-metrics.txt
echo "RAM Total: $(free -h | grep Mem: | awk '{print $2}')" >> ~/baseline-metrics.txt
echo "RAM Used: $(free -h | grep Mem: | awk '{print $3}')" >> ~/baseline-metrics.txt
echo "Disk Total: $(df -h / | tail -1 | awk '{print $2}')" >> ~/baseline-metrics.txt
echo "Disk Used: $(df -h / | tail -1 | awk '{print $3}')" >> ~/baseline-metrics.txt
echo "Ships Running: $(docker ps | grep -c urbit)" >> ~/baseline-metrics.txt
```

**Load Testing** (optional, for large fleets):
- Simulate concurrent access to all ships
- Monitor resource usage under load
- Verify no crashes or performance degradation

**Success Criteria**:
- All checklist items passed
- Baseline metrics recorded
- Documentation complete and accurate
- Team trained and confident
- Platform ready for production use

**Handoff**:
- Present all documentation to stakeholders
- Demonstrate platform functionality
- Transfer credentials securely
- Schedule first maintenance check-in (1 week)

---

## Post-Deployment

### Immediate Next Steps
1. Install apps on ships (Groups, Talk, etc.)
2. Configure ship privacy settings
3. Join Urbit community
4. Monitor resource usage (first 48 hours closely)

### Maintenance Schedule

**Weekly**:
- Check logs for errors/warnings
- Verify all ships running
- Monitor disk space (alert at 85%)
- Review resource utilization

**Monthly**:
- Update VPS packages
- Update GroundSeg (check for new releases)
- Run |pack in all ships (memory optimization)
- Test backup restoration
- Review and rotate logs

**Quarterly**:
- Full DR drill
- Security audit
- Performance review (compare to baseline)
- Capacity planning (project 6-12 months)
- Update documentation

### Success Metrics

**Availability**: >99% uptime per ship
**Performance**: Dojo commands <3 seconds
**Resource Utilization**: <80% RAM, <80% disk
**Security**: No unauthorized access attempts succeed
**Maintenance**: All procedures documented and followed

---

## Common Issues and Resolutions

### GroundSeg Service Won't Start
**Symptoms**: `sudo systemctl status groundseg` shows failed
**Diagnostics**:
```bash
sudo journalctl -u groundseg -n 100
docker ps -a
```
**Solutions**:
- Docker not running: `sudo systemctl start docker`
- Port conflict: Check if port 3000 already in use
- Corrupted installation: Reinstall via `sudo wget -O - get.groundseg.app|bash`

### Ship Container Keeps Restarting
**Symptoms**: `docker ps` shows ship restarting repeatedly
**Diagnostics**:
```bash
docker logs <container-id> --tail 100
docker inspect <container-id>
```
**Solutions**:
- Insufficient RAM: Increase ship RAM allocation
- Corrupted pier: Restore from backup
- Resource limits too low: Adjust in GroundSeg UI

### Can't Access Ships Externally
**Symptoms**: Ships work locally but not via StarTram/Anchor
**Diagnostics**:
- Check StarTram/Anchor status in GroundSeg UI
- Test local access: `curl http://localhost:8080`
- Check firewall: `sudo ufw status`
- Verify DNS: `nslookup your-domain.com`

**Solutions**:
- StarTram inactive: Re-enter registration code
- Anchor disconnected: Check Anchor VPS health, restart Anchor service
- Firewall blocking: Verify ports 80/443 open
- DNS not propagated: Wait for TTL expiry (5 min - 24 hours)

### High Disk Usage
**Symptoms**: `df -h` shows >85% disk utilization
**Diagnostics**:
```bash
du -sh /opt/groundseg/piers/*  # Pier sizes
du -sh /opt/minio/data/*  # MinIO usage (if enabled)
docker system df  # Docker storage usage
```
**Solutions**:
- Large piers: Run |pack in ships
- Old Docker images: `docker system prune -a`
- Full backups: Clean old backups, keep last 3-4
- MinIO bloat: Review bucket policies, lifecycle rules
- Upgrade VPS disk: Resize volume

---

## Rollback Procedures

**Phase 1-4**: No rollback needed (no changes made)

**Phase 5-6 (GroundSeg Installation)**:
```bash
# Uninstall GroundSeg
sudo systemctl stop groundseg
sudo systemctl disable groundseg
sudo rm /etc/systemd/system/groundseg.service
sudo rm /usr/local/bin/groundseg
sudo rm -rf /opt/nativeplanet
```

**Phase 7-9 (Networking/MinIO)**:
- StarTram: Unregister in UI
- Anchor: Delete Anchor VPS
- MinIO: `docker stop minio && docker rm minio`

**Phase 10-12 (Ship Deployment)**:
- Stop ships: `docker stop <container-id>`
- Remove containers: `docker rm <container-id>`
- Remove piers: `sudo rm -rf /opt/groundseg/piers/<ship-name>`

**Complete Rollback**:
- Delete all ship data
- Uninstall GroundSeg
- Remove Docker (if desired): `sudo apt purge docker-ce docker-ce-cli containerd.io`
- Revert firewall: `sudo ufw reset`

---

## Estimated Timeline

**Total Time**: 4-8 hours (varies by ship count and experience)

- Phases 1-2 (Assessment, VPS): 30 min
- Phase 3 (System Config): 15 min
- Phases 4-6 (Docker, GroundSeg): 15 min
- Phase 7-8 (StarTram) OR Phase 8A (Anchor): 30 min / 2 hours
- Phase 9 (MinIO): 10 min (one-click) or 30 min (manual)
- Phase 10 (Pilot Ship): 30-45 min (sync time)
- Phase 11-12 (Fleet Deployment): 1-3 hours (depends on ship count)
- Phases 13-15 (Security, Performance, Monitoring): 1-2 hours
- Phases 16-20 (Backup, DR, Documentation, Training, Validation): 2-3 hours

**First-time operators**: Allow 8-10 hours
**Experienced operators**: 4-6 hours
**Large fleets (10+ ships)**: Add 1-2 hours per 10 ships

---

## Integration

This command invokes the **groundseg-operator** agent throughout all phases for expert guidance, troubleshooting, and decision-making.

The agent references these skills:
- groundseg-installation (platform setup)
- anchor-networking (self-hosted networking) OR startram (managed networking)
- multi-ship-orchestration (fleet management)
- minio-s3-integration (storage setup)
- container-security (Docker hardening)
- groundseg-troubleshooting (diagnostics)
- performance-optimization (system tuning)
- backup-disaster-recovery (safe backups)
