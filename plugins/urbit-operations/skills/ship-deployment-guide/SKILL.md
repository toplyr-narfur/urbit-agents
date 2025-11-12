---
name: ship-deployment-guide
description: Step-by-step procedures for deploying Urbit planets, comets, and fake ships on bare-metal Ubuntu/Debian systems with system preparation, binary installation, network configuration, systemd integration, and production validation. Use when deploying ships, booting from keyfiles, configuring networking, or setting up production environments.
---

# Ship Deployment Guide Skill

Step-by-step procedures for deploying Urbit planets, comets, and fake ships on bare-metal Ubuntu/Debian systems with production-ready configurations.

## Overview

This skill provides comprehensive deployment workflows for all three ship types (planets, comets, fake ships), covering system preparation, binary installation, ship initialization, network configuration, and production validation. Each workflow is designed for repeatability and production excellence.

## Learning Objectives

After mastering this skill, you will be able to:

1. **Deploy planets**: Boot permanent Urbit identities from keyfiles
2. **Deploy comets**: Generate free, self-hosted Urbit identities
3. **Deploy fake ships**: Create offline development environments
4. **Configure networking**: Set up firewalls, DNS, and SSL/TLS
5. **Systemd integration**: Create auto-restart service units
6. **Production validation**: Verify deployments meet operational standards

## Prerequisites

- Ubuntu 22.04+ or Debian 11+ system
- Root or sudo access
- Basic command-line proficiency
- Understanding of urbit-fundamentals skill (Nock, Arvo, Vere, loom, etc.)

## 1. System Preparation

### 1.1 System Requirements Validation

**Minimum requirements**:
- **RAM**: 2GB (4GB+ recommended for production)
- **Storage**: 30GB free (50GB+ recommended, SSD strongly preferred)
- **Network**: Stable internet connection, public IP (or NAT with port forwarding)
- **OS**: Ubuntu 22.04 LTS or Debian 11+

**Validation commands**:
```bash
# Check OS version
lsb_release -a
# Should show: Ubuntu 22.04+ or Debian 11+

# Check available RAM
free -h
# Should show: At least 2GB total memory

# Check disk space
df -h /
# Should show: At least 30GB available on root partition

# Check disk type (SSD strongly recommended)
lsblk -d -o name,rota
# rota=0 means SSD (good), rota=1 means HDD (slow, not recommended)

# Check internet connectivity
ping -c 3 google.com

# Check if using public IP or NAT
curl ifconfig.me
# Shows your public IP address
```

### 1.2 Dependency Installation

**Required dependencies**:
```bash
sudo apt update
sudo apt install -y curl libssl-dev
```

**Optional but recommended**:
```bash
# For monitoring
sudo apt install -y htop iotop sysstat

# For security (covered in production hardening)
sudo apt install -y ufw fail2ban

# For reverse proxy (if using custom domain)
sudo apt install -y nginx certbot python3-certbot-nginx
```

### 1.3 User Account Setup

**Create dedicated urbit user** (production best practice):
```bash
# Create user
sudo adduser urbit

# Set strong password or configure SSH key-only auth
# (Covered in setup-production command)

# Switch to urbit user for deployment
su - urbit
```

## 2. Urbit Binary Installation

### 2.1 Download and Install (2025 Method)

**Official installation command**:
```bash
# Download latest Linux x86_64 binary
cd /tmp
curl -L https://urbit.org/install/linux-x86_64/latest | tar xzk --transform='s/.*/urbit/g'

# Move to system PATH
sudo mv urbit /usr/local/bin/

# Verify installation
urbit --version
# Should output: Urbit vere v3.2 (or later)
```

**Alternative: Specific version**:
```bash
# Download specific Vere version (if needed for compatibility)
curl -L https://urbit.org/install/linux-x86_64/v3.2/urbit-v3.2-linux-x86_64.tgz | tar xzk
sudo mv urbit-v3.2-linux-x86_64/urbit /usr/local/bin/urbit
```

### 2.2 Binary Verification

```bash
# Check binary location
which urbit
# Should output: /usr/local/bin/urbit

# Check binary permissions
ls -la /usr/local/bin/urbit
# Should be executable: -rwxr-xr-x

# Test binary
urbit --version
# Should output version without errors
```

## 3. Planet Deployment

### 3.1 Planet Overview

**What is a planet?**
- Permanent Urbit identity owned forever
- ~4.3 billion possible planets (2^32 address space)
- Requires Azimuth identity (purchased or gifted)
- Sponsored by a star (provides OTA updates, routing)

**Use cases**:
- Personal identity for long-term use
- Production applications
- Permanent group memberships
- Professional/business use

### 3.2 Obtaining a Planet

**Sources**:
1. **Tlon**: https://tlon.network (official, recommended)
2. **Urbit.live**: Secondary market for planets
3. **UrbitHost**: Managed hosting + planet
4. **Gifted**: From galaxy/star owner

**What you receive**:
- **Master ticket**: Mnemonic phrase (store securely offline)
- **Keyfile**: One-time use file for first boot (from Bridge)

### 3.3 Keyfile Generation (via Bridge)

**Step 1**: Access Bridge
- URL: https://bridge.urbit.org
- Connect Ethereum wallet (MetaMask, etc.) or use master ticket

**Step 2**: Select planet

**Step 3**: Generate keyfile
- Click "OS" → "Download Keyfile"
- Saves file: `sampel-palnet-1.key` (example)

**CRITICAL SECURITY**:
- Keyfile is **single-use only** (consumed on first boot)
- **NEVER backup keyfile** after first boot (useless and dangerous)
- **NEVER reuse keyfile** (causes network ban)
- Delete immediately after successful first boot

### 3.4 Planet Boot Procedure

**Step 1: Upload keyfile to server**
```bash
# From local machine (where keyfile downloaded)
scp sampel-palnet-1.key urbit@server-ip:/home/urbit/

# Or use SFTP, rsync, etc.
```

**Step 2: Create pier directory** (first boot only)
```bash
# As urbit user
cd /home/urbit

# Boot planet with keyfile
urbit -w sampel-palnet -k sampel-palnet-1.key

# Explanation of flags:
# -w : "new" - create new pier (first boot only)
# -k : "key" - path to keyfile
# sampel-palnet : pier directory name (should match ship name)
```

**Step 3: Initial sync and boot**
```
# Urbit will output:
urbit v3.2
boot: home is /home/urbit/sampel-palnet
loom: mapped 2048MB
lite: arvo formula 123456789
lite: core 987654321
boot: protected lick
pier: (12): live
pier: (13): syncing to sponsor ~sampel...
...
# This takes 5-30 minutes depending on network and sponsor
...
dojo> # You're in! Ship booted successfully
```

**Step 4: Verify boot success**
```
# In dojo (the > prompt):
+code
# Should output: web login code (e.g., ~sampel-palnet-sampel-palnet)

# This confirms:
# - Ship booted successfully
# - Dojo is accessible
# - HTTP server running
```

**Step 5: Delete keyfile** (CRITICAL)
```bash
# In NEW terminal (keep dojo running), as urbit user:
shred -u /home/urbit/sampel-palnet-1.key

# shred: Securely overwrites file before deleting
# -u: Remove file after shredding

# NEVER keep keyfile after successful boot!
```

### 3.5 Subsequent Boots (After First Boot)

**IMPORTANT**: After first boot, **DO NOT use keyfile**

```bash
# Stop ship (in dojo):
# Press ctrl+d (sends graceful shutdown)

# Restart ship (NO keyfile):
urbit /home/urbit/sampel-palnet

# Just the pier path, NO -w, NO -k flags
```

**Common mistake**: Trying to boot with keyfile again
```bash
# WRONG (will fail):
urbit -w sampel-palnet -k sampel-palnet-1.key
# Error: boot: invalid keyfile (already consumed)

# CORRECT:
urbit /home/urbit/sampel-palnet
```

### 3.6 Accessing Your Planet

**Via Web (Landscape)**:
```bash
# Get login code
# In dojo:
+code
# Output: ~sampel-palnet-sampel-palnet (example)

# Access in browser:
# - Local: http://localhost:8080 (or http://localhost on some systems)
# - Remote: http://server-ip:8080 (if firewall allows)

# Enter login code when prompted
```

**Via Dojo (Command Line)**:
```bash
# If ship running in foreground:
# Just type commands at dojo> prompt

# If ship running in background/systemd:
# Attach to running ship (advanced, requires setup)
```

## 4. Comet Deployment

### 4.1 Comet Overview

**What is a comet?**
- Free, self-generated Urbit identity
- Anyone can create without Ethereum/crypto
- No Azimuth registration (ephemeral identity)
- 2^128 possible comets (effectively unlimited)

**Use cases**:
- Testing Urbit without purchasing planet
- Temporary identities
- Disposable development ships
- Learning Urbit

**Limitations**:
- Less trusted by network (some groups may block comets)
- No identity recovery if pier corrupted (comet = pier)
- No sponsor services (bootstraps directly from Ames network)

### 4.2 Comet Boot Procedure

**Step 1: Generate and boot comet**
```bash
# As urbit user
cd /home/urbit

# Boot new comet (self-generates identity)
urbit -c mycomet

# Explanation:
# -c : "comet" - generate new comet identity
# mycomet : pier directory name (can be any name)
```

**Step 2: Initial network sync**
```
# Urbit will output:
urbit v3.2
boot: home is /home/urbit/mycomet
loom: mapped 2048MB
boot: new comet ~sampel-sampel-sampel-sampel (example)
lite: arvo formula 123456789
...
pier: (10): live
# Comet bootstraps from Ames network (no sponsor)
...
dojo> # Comet booted successfully
```

**Identity generated**:
- Comet names are long: `~sampel-sampel-sampel-sampel` (8 syllables)
- Identity is deterministic from pier (cannot recover if pier lost)

**Step 3: Verify boot**
```
# In dojo:
+code
# Output: web login code

# Comet is ready to use
```

### 4.3 Comet Lifecycle

**Subsequent boots**:
```bash
# Stop comet (in dojo): ctrl+d

# Restart comet:
urbit /home/urbit/mycomet
```

**CRITICAL**: Comets cannot be recovered
- Comet identity = pier state
- If pier corrupted or lost, comet identity is **permanently lost**
- Cannot re-generate same comet (unlike planets with keyfile/master ticket)
- **Backup strategy**: Backup pier regularly if comet has valuable data

**When to delete a comet**:
- Testing complete
- Upgrading to planet
- Comet compromised/corrupted

```bash
# Delete comet pier
rm -rf /home/urbit/mycomet

# Generate new comet
urbit -c newcomet
# Creates entirely new identity
```

## 5. Fake Ship Deployment

### 5.1 Fake Ship Overview

**What is a fake ship?**
- Offline development ship (not connected to live network)
- Uses fake Azimuth keys (not registered on Ethereum)
- Can specify any identity (galaxy, star, planet, moon)
- Multiple fake ships can network with each other (same machine)

**Use cases**:
- Hoon development and testing
- Gall agent development
- Desk/app development
- Learning Urbit OS without network

**Advantages**:
- Fast boot (no network sync)
- Isolated environment (no network noise)
- Can use galaxy identity (full permissions)
- Easy reset/backup workflow

### 5.2 Fake Ship Boot Procedure

**Step 1: Boot fake ship**
```bash
# As urbit user
cd /home/urbit

# Boot fake ~zod (galaxy)
urbit -F zod

# Explanation:
# -F : "fake" - fake ship (not connected to network)
# zod : ship name (patp without ~)

# Can use any identity:
urbit -F sampel-palnet  # Fake planet
urbit -F litzod         # Fake star
urbit -F marzod         # Fake galaxy
```

**Step 2: Quick boot**
```
# Urbit will output:
urbit v3.2
boot: home is /home/urbit/zod
loom: mapped 2048MB
lite: arvo formula 123456789
boot: protected lick
pier: (5): live
# Fake ships boot in seconds (no network sync)
dojo> # Ready immediately
```

### 5.3 Fake Ship Development Workflow

**Efficient reset workflow** (using backup copies):

**Create backup of fresh fake ship**:
```bash
# Boot fresh fake ~zod
urbit -F zod

# After boot complete, stop ship (ctrl+d in dojo)

# Create backup
cp -r zod zod-backup

# Now you have:
# - zod/ : Working ship (modify/test here)
# - zod-backup/ : Pristine backup for quick reset
```

**Reset workflow**:
```bash
# Testing broke something? Quick reset:
rm -rf zod
cp -r zod-backup zod
urbit zod  # Boot from backup (seconds, not minutes)
```

**Multi-fake-ship networking**:
```bash
# Boot multiple fake ships
urbit -F zod    # Terminal 1
urbit -F bus    # Terminal 2 (another galaxy)

# Ships can communicate with each other
# In ~zod dojo:
|hi ~bus
# Should get ack from ~bus

# Useful for testing distributed apps
```

### 5.4 Fake Ship Limitations

**What fake ships CANNOT do**:
- Connect to live Urbit network
- Receive OTA updates from real sponsors
- Communicate with real planets/comets
- Access real Urbit groups/channels

**What fake ships CAN do**:
- Run Hoon code and Gall agents
- Test desk/app development
- Network with other fake ships (same machine)
- Full dojo access (all commands)

**CRITICAL WARNING**: Never re-initialize same identity
```bash
# Scenario: You booted fake ~zod, used it, shut down

# WRONG (causes problems):
urbit -F zod  # Re-initializes ~zod (new identity)
# Any fake ships that talked to old ~zod will have communication issues

# CORRECT:
urbit zod  # Boot existing pier (preserves identity)

# Or delete and start completely fresh:
rm -rf zod
urbit -F zod  # New ~zod, but treat as different ship
```

## 6. Network Configuration

### 6.1 Firewall Configuration (UFW)

**Essential ports**:
```bash
# Install UFW
sudo apt install ufw -y

# Allow SSH (CRITICAL - do this BEFORE enabling firewall)
sudo ufw allow 22/tcp

# Allow HTTP/HTTPS (for web access)
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Allow Ames networking (UDP 34543)
sudo ufw allow 34543/udp

# Enable firewall
sudo ufw enable

# Verify
sudo ufw status verbose
```

**Expected output**:
```
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
443/tcp                    ALLOW       Anywhere
34543/udp                  ALLOW       Anywhere
```

### 6.2 Router Configuration (if behind NAT)

**Port forwarding setup**:
1. Access router admin panel (usually http://192.168.1.1)
2. Find "Port Forwarding" section
3. Create rule:
   - External Port: 34543
   - Internal Port: 34543
   - Protocol: UDP
   - Internal IP: Ship server IP (e.g., 192.168.1.100)

**Test port forwarding**:
```bash
# From ship dojo:
|hi ~zod
# Should see "ack" response (indicates Ames working)
```

### 6.3 DNS Configuration

**Using arvo.network** (free subdomain):
- Automatic: Ship registers with arvo.network on boot
- Access: https://sampel-palnet.arvo.network
- SSL: Automatically provisioned

**Using custom domain**:
```bash
# Step 1: Point domain to server IP
# Create DNS A record:
# myship.example.com  A  <server-public-ip>

# Step 2: Verify DNS propagation
nslookup myship.example.com
# Should return server IP

# Step 3: Configure ship (in dojo)
:acme|set-domain 'myship.example.com'

# Step 4: Set up reverse proxy (see SSL/TLS section)
```

### 6.4 SSL/TLS Configuration

**Option A: arvo.network** (easiest)
- Automatic SSL via Let's Encrypt
- Zero configuration required
- Access: https://sampel-palnet.arvo.network

**Option B: Custom domain with Nginx**
```bash
# Install Nginx and Certbot
sudo apt install nginx certbot python3-certbot-nginx -y

# Create Nginx site config
sudo nano /etc/nginx/sites-available/myship
```

**Nginx configuration**:
```nginx
server {
    listen 80;
    server_name myship.example.com;

    location / {
        proxy_pass http://localhost:8080;  # Adjust if ship uses different port
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

**Enable and obtain SSL**:
```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/myship /etc/nginx/sites-enabled/

# Test config
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx

# Obtain Let's Encrypt certificate
sudo certbot --nginx -d myship.example.com

# Follow prompts (auto-configures HTTPS redirect)
```

## 7. Systemd Service Setup

### 7.1 Create Systemd Service File

**For planet/comet** (production deployment):
```bash
sudo nano /etc/systemd/system/urbit-sampel-palnet.service
```

**Service file content**:
```ini
[Unit]
Description=Urbit Ship - ~sampel-palnet
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=urbit
Group=urbit
WorkingDirectory=/home/urbit
ExecStart=/usr/local/bin/urbit /home/urbit/sampel-palnet

# Restart policy
Restart=always
RestartSec=10

# Resource limits
LimitNOFILE=65536
MemoryMax=4G

# Security (basic - see setup-production for hardening)
NoNewPrivileges=yes
PrivateTmp=yes

# Logging
StandardOutput=journal
StandardError=journal
SyslogIdentifier=urbit-sampel-palnet

[Install]
WantedBy=multi-user.target
```

### 7.2 Enable and Start Service

```bash
# Reload systemd daemon
sudo systemctl daemon-reload

# Enable service (start on boot)
sudo systemctl enable urbit-sampel-palnet

# Start service
sudo systemctl start urbit-sampel-palnet

# Check status
sudo systemctl status urbit-sampel-palnet

# View logs
sudo journalctl -u urbit-sampel-palnet -n 50 -f
```

### 7.3 Service Management

```bash
# Stop ship
sudo systemctl stop urbit-sampel-palnet

# Restart ship
sudo systemctl restart urbit-sampel-palnet

# Disable service (don't start on boot)
sudo systemctl disable urbit-sampel-palnet

# Check if service is running
sudo systemctl is-active urbit-sampel-palnet
```

## 8. Production Validation

### 8.1 Boot Validation Checklist

- [ ] Ship boots without errors
- [ ] Dojo accessible (`+code` returns login code)
- [ ] Web interface accessible (http://localhost:8080)
- [ ] Ames connectivity (`|hi ~zod` returns ack)
- [ ] Disk space adequate (`df -h /home/urbit`)
- [ ] Systemd service running (`systemctl status urbit-ship`)

### 8.2 Network Validation

```bash
# Test HTTP access
curl http://localhost:8080
# Should return HTML (ship web interface)

# Test Ames connectivity (in dojo)
|hi ~zod
# Should see: ack from ~zod within seconds

# Check firewall
sudo ufw status | grep -E '(80|443|34543)'
# Should show all three ports allowed
```

### 8.3 Security Validation

```bash
# Check ship running as correct user
ps aux | grep urbit
# User column should show "urbit", NOT "root"

# Check pier permissions
ls -la /home/urbit/sampel-palnet
# Should show: drwx------ urbit urbit (owner-only access)

# Verify keyfile deleted
find /home/urbit -name "*.key" -type f
# Should return NOTHING (no keyfiles found)
```

### 8.4 Performance Validation

```bash
# Check pier size
du -sh /home/urbit/sampel-palnet
# Should be <2GB for new ships

# Check memory usage
free -h
# Should have sufficient free RAM (>2GB recommended)

# Check system load
uptime
# Load average should be <1.0 for single ship
```

## 9. Common Issues and Solutions

### Issue 1: "boot: invalid keyfile"

**Cause**: Keyfile already used or corrupted

**Solutions**:
- If first boot: Re-download keyfile from Bridge
- If subsequent boot: Don't use keyfile (`urbit /path/to/pier`)
- Verify keyfile matches ship name

### Issue 2: "out of loom"

**Cause**: Ship state exceeds 2GB loom limit

**Solutions**:
```bash
# Quick fix (in dojo):
|pack  # Defragmentation

# Thorough fix (requires 8GB RAM):
|meld  # Deduplication

# Or increase loom (restart required):
urbit --loom 32 /path/to/pier  # 4GB loom
```

### Issue 3: Ship won't connect to network

**Cause**: Firewall blocking Ames (UDP 34543)

**Solutions**:
```bash
# Check firewall
sudo ufw status | grep 34543

# Allow Ames
sudo ufw allow 34543/udp

# Check router port forwarding (if behind NAT)
# Test connectivity (in dojo):
|hi ~zod
```

### Issue 4: Cannot access web interface

**Cause**: HTTP port not accessible or ship not running

**Solutions**:
```bash
# Check ship running
sudo systemctl status urbit-ship

# Check HTTP listening
sudo netstat -tulpn | grep urbit
# Should show localhost:8080 (or :80)

# Test local access
curl http://localhost:8080

# Check firewall (if accessing remotely)
sudo ufw allow 80/tcp
```

## 10. Best Practices Summary

1. **System preparation**: Verify requirements before starting (RAM, disk, network)
2. **Use SSD**: Significantly improves performance (event log I/O)
3. **Dedicated user**: Run ship as non-root user (security)
4. **Keyfile handling**: Use once, delete immediately (shred -u)
5. **Firewall essential**: Configure before exposing to internet
6. **Systemd for production**: Auto-restart, logging, resource limits
7. **Monitoring**: Check logs regularly (journalctl)
8. **Backup strategy**: Stop ship before backup (prevent corruption)
9. **SSL/TLS**: Use HTTPS for production (arvo.network or custom domain)
10. **Documentation**: Document your deployment (configuration, credentials, procedures)

## 11. Next Steps

After successful deployment:

1. **Production hardening**: Apply setup-production command (20-phase security)
2. **Monitoring**: Set up automated monitoring and alerting
3. **Backups**: Configure automated backup schedule
4. **OTA updates**: Verify ship receiving updates from sponsor
5. **Performance optimization**: Tune system for production workload
6. **Documentation**: Create operational runbook for your deployment

## 12. Reference

- Official installation guide: https://docs.urbit.org/manual/getting-started/self-hosted/cli
- Ship troubleshooting: https://docs.urbit.org/manual/os/ship-troubleshooting
- Urbit operators manual: https://docs.urbit.org/manual

## Summary

This guide covers complete deployment procedures for all three ship types (planets, comets, fake ships) on bare-metal Ubuntu/Debian systems. Planets require keyfiles and Azimuth identities (permanent), comets are self-generated (free, ephemeral), and fake ships are for offline development. All deployments should follow production best practices: dedicated user accounts, systemd service management, firewall configuration, and SSL/TLS for web access.
