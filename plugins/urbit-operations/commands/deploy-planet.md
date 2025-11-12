---
name: deploy-planet
description: Complete bare-metal planet deployment workflow from system assessment to production validation with 10 orchestrated phases
subagent_type: urbit-deployment-specialist
---

# Deploy Planet Command

Complete orchestration workflow for deploying an Urbit planet on bare-metal Ubuntu/Debian systems with production hardening.

## Purpose

This command guides operators through a comprehensive 10-phase deployment workflow, from initial system assessment through production validation. It ensures every planet deployment follows best practices for security, performance, and long-term maintainability.

## Configuration Options

### Ship Type
- **Planet** (with keyfile) - Standard deployment
- **Comet** (no keyfile) - Free disposable ship for testing
- **Fake Ship** - Local development only

### Domain Configuration
- **arvo.network subdomain** - Free automatic SSL (easiest)
- **Custom domain** - Your own domain with Let's Encrypt

### Systemd Service
- **Yes** - Automatic startup and restart (recommended for production)
- **No** - Manual ship management

### Hardening Level
- **Basic** - Essential security only
- **Standard** - Recommended production security (default)
- **Strict** - Maximum security for sensitive deployments

## 10-Phase Workflow

### Phase 1: System Assessment

**Objective**: Verify system meets requirements before proceeding.

**Actions**:
1. Check OS version (Ubuntu 22.04+ or Debian 11+ required)
2. Verify RAM ≥2GB (4GB+ recommended)
3. Verify disk ≥30GB free (50GB+ recommended, SSD strongly preferred)
4. Test internet connectivity and bandwidth
5. Check for conflicting services on ports 80, 443, 34543

**Success Criteria**:
- Ubuntu 22.04/24.04 or Debian 11/12
- RAM ≥2GB available
- Disk ≥30GB free on SSD
- Internet accessible
- Required ports available

**If Requirements Not Met**:
- Insufficient RAM: Upgrade server or enable swap
- Insufficient disk: Free space or add storage
- HDD instead of SSD: Warn about 10× slower performance
- Port conflicts: Identify and resolve conflicting services

---

### Phase 2: Dependency Installation

**Objective**: Install required system packages and libraries.

**Actions**:
```bash
# Update package lists
sudo apt update

# Install dependencies
sudo apt install -y \
  curl \
  libssl-dev \
  openssl \
  ca-certificates \
  build-essential

# Verify installations
curl --version
openssl version
```

**Success Criteria**:
- All packages installed without errors
- curl and openssl functional

---

### Phase 3: Urbit Binary Installation

**Objective**: Download, verify, and install official Urbit binary.

**Actions**:
```bash
# Download and extract latest Linux x86_64 binary (2025 official method)
cd /tmp
curl -L https://urbit.org/install/linux-x86_64/latest | tar xzk --transform='s/.*/urbit/g'

# Install to system PATH
sudo mv urbit /usr/local/bin/

# Verify installation
urbit --version

# Set permissions (usually already set)
sudo chmod +x /usr/local/bin/urbit
```

**Success Criteria**:
- Binary installed to /usr/local/bin/urbit
- `urbit --version` returns version number
- Binary is executable

**Security Note**: Always download from official urbit.org, never third-party sources.

---

### Phase 4: Keyfile Preparation

**Objective**: Securely handle planet keyfile (planets only; skip for comets).

**Actions**:
1. Prompt user to upload keyfile securely (SCP/SFTP)
2. Verify keyfile format (starts with '0w')
3. Set proper permissions: `chmod 600 keyfile.key`
4. Validate keyfile matches ship name

**Critical Warnings**:
- **Keyfile is single-use**: After successful boot, it's consumed and useless
- **Never backup keyfile after boot**: It cannot be reused
- **Never share keyfile**: Compromises ship security
- **Must delete after boot**: Use `shred -u keyfile.key` for secure deletion

**Success Criteria**:
- Keyfile uploaded to server
- File format validated (starts with '0w')
- Permissions set correctly (600)
- Ship name matches keyfile

---

### Phase 5: Ship Boot

**Objective**: Boot ship and complete initial synchronization.

**Actions**:

**For Planet**:
```bash
# Create pier directory
mkdir -p ~/urbit-ships
cd ~/urbit-ships

# Boot with keyfile (replace with actual names)
urbit -w sampel-palnet -k /path/to/sampel-palnet.key
```

**For Comet**:
```bash
# Boot comet (generates identity)
cd ~/urbit-ships
urbit -c my-comet
```

**For Fake Ship** (development):
```bash
# Boot fake ship
cd ~/urbit-ships
urbit -F zod
```

**Monitor Initial Sync**:
- Wait 10-30 minutes for planet initial sync
- Comet: 5-15 minutes
- Fake ship: Instant (no sync)
- Watch for "dojo>" prompt indicating readiness

**Verify Dojo Access**:
- Press Enter to see dojo prompt
- Test command: `+vats` (lists running agents)
- Exit dojo: Ctrl+D (graceful shutdown)

**Success Criteria**:
- Ship boots without errors
- Initial sync completes
- Dojo prompt accessible
- Basic dojo commands work

**Common Issues**:
- "invalid keyfile": Keyfile format wrong or already used
- Hangs during sync: Network issue, check connectivity
- Crashes immediately: Insufficient RAM, check resources

---

### Phase 6: Network Configuration

**Objective**: Configure firewall and enable external access.

**Actions**:

**UFW Firewall Setup**:
```bash
# Install UFW if not present
sudo apt install ufw

# Default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH (verify SSH port first!)
sudo ufw allow 22/tcp

# Allow HTTP/HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Allow Ames (Urbit networking)
sudo ufw allow 34543/udp

# Enable firewall
sudo ufw enable

# Verify rules
sudo ufw status verbose
```

**Port Forwarding** (if behind NAT):
- Router must forward 80, 443, 34543 to server
- Document router-specific configuration
- Test external connectivity

**Success Criteria**:
- UFW enabled with correct rules
- Ports 80, 443, 34543 accessible from internet
- SSH access maintained (don't lock yourself out!)

---

### Phase 7: DNS and SSL/TLS Setup

**Objective**: Configure domain and SSL certificate.

**Option A: arvo.network (Free, Easiest)**:

In ship dojo:
```
# Request arvo.network subdomain
:acme|on

# Verify subdomain assigned
# Check https://<ship-name>.arvo.network
```

Wait 5-10 minutes for DNS propagation and certificate issuance.

**Option B: Custom Domain with Let's Encrypt**:

**Step 1: DNS Configuration**:
```bash
# Create A record pointing to server IP
# Example: sampel-palnet.example.com A 12.34.56.78
```

**Step 2: Install Nginx and Certbot**:
```bash
sudo apt install nginx certbot python3-certbot-nginx
```

**Step 3: Configure Nginx**:

**Note**: Ship HTTP port is typically `80` inside the ship (accessed as `localhost:80` or `localhost:8080` depending on configuration).

Create `/etc/nginx/sites-available/urbit-ship`:
```nginx
server {
    listen 80;
    server_name sampel-palnet.example.com;

    location / {
        proxy_pass http://localhost:80;  # Adjust if ship uses different port
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Enable site:
```bash
sudo ln -s /etc/nginx/sites-available/urbit-ship /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

**Step 4: Obtain Certificate**:
```bash
sudo certbot --nginx -d sampel-palnet.example.com
```

**Step 5: Configure Auto-Renewal**:
```bash
sudo systemctl enable certbot.timer
sudo systemctl start certbot.timer
```

**Success Criteria**:
- Domain resolves to server IP
- HTTPS accessible externally
- Valid SSL certificate (test at https://www.ssllabs.com/ssltest/)
- Certificate auto-renewal configured

---

### Phase 8: Systemd Service Creation

**Objective**: Enable automatic ship startup and restart.

**Actions**:

**Create Service File**:
`/etc/systemd/system/urbit@.service`:
```ini
[Unit]
Description=Urbit Ship - %i
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=urbit
WorkingDirectory=/home/urbit/urbit-ships
ExecStart=/usr/local/bin/urbit %i
Restart=always
RestartSec=10
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

**Create Dedicated User** (recommended):
```bash
sudo useradd -m -s /bin/bash urbit
sudo chown -R urbit:urbit ~/urbit-ships
```

**Enable and Start Service**:
```bash
sudo systemctl daemon-reload
sudo systemctl enable urbit@sampel-palnet
sudo systemctl start urbit@sampel-palnet

# Verify status
sudo systemctl status urbit@sampel-palnet

# View logs
sudo journalctl -u urbit@sampel-palnet -f
```

**Success Criteria**:
- Service file created and valid
- Service starts without errors
- Ship accessible after service start
- Service survives reboot test
- Logs accessible via journalctl

---

### Phase 9: Production Hardening

**Objective**: Implement security baseline and performance tuning.

**Security Hardening** (based on hardening level):

**Basic**:
- SSH key-only authentication
- UFW firewall enabled
- Automatic security updates

**Standard** (Default):
- All Basic hardening
- fail2ban installation and configuration
- Minimal services (disable unnecessary)
- Secure pier permissions (700, urbit:urbit)

**Strict**:
- All Standard hardening
- AppArmor/SELinux profiles
- Kernel hardening (sysctl tuning)
- Auditd logging
- Intrusion detection

**Example Standard Hardening**:

**SSH Hardening**:
```bash
# Edit /etc/ssh/sshd_config
sudo nano /etc/ssh/sshd_config

# Set:
# PasswordAuthentication no
# PermitRootLogin no
# PubkeyAuthentication yes

sudo systemctl restart sshd
```

**Install fail2ban**:
```bash
sudo apt install fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

**Automatic Security Updates**:
```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades
```

**Performance Tuning**:
```bash
# I/O scheduler for SSD
echo deadline | sudo tee /sys/block/sda/queue/scheduler

# Configure swap (4GB)
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# Set swappiness
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

**Success Criteria**:
- Security hardening implemented per selected level
- SSH hardened (key-only auth)
- fail2ban active
- Automatic updates enabled
- Swap configured
- I/O scheduler optimized
- Pier permissions secure (700)

---

### Phase 10: Production Validation

**Objective**: Comprehensive testing and documentation.

**Validation Checklist**:

**Ship Functionality**:
- [ ] Ship boots successfully
- [ ] Dojo commands responsive (<3 seconds)
- [ ] HTTP/HTTPS accessible externally
- [ ] SSL certificate valid
- [ ] Ames networking functional (check peers)

**Network Connectivity**:
```bash
# Test HTTP
curl -I http://localhost:8080

# Test HTTPS externally
curl -I https://sampel-palnet.example.com

# Test Ames port
nc -zuv localhost 34543
```

**Systemd Service**:
- [ ] Service starts automatically at boot
- [ ] Service restarts after crash
- [ ] Logs accessible and clean
- [ ] Graceful shutdown works (Ctrl+D in dojo)

**Security Validation**:
- [ ] SSH password auth disabled
- [ ] fail2ban active
- [ ] UFW rules correct
- [ ] Pier permissions secure (700, urbit:urbit)
- [ ] Automatic updates enabled

**Performance Baseline**:
```bash
# Record baseline metrics
echo "Deployment Baseline - $(date)" > ~/ship-baseline.txt
echo "Pier size: $(du -sh ~/urbit-ships/sampel-palnet)" >> ~/ship-baseline.txt
echo "RAM usage: $(free -h)" >> ~/ship-baseline.txt
echo "Disk usage: $(df -h /)" >> ~/ship-baseline.txt
echo "Dojo latency: <manual test>" >> ~/ship-baseline.txt
```

**Create Runbook**:
Document in `~/ship-runbook.md`:
```markdown
# Ship Operation Runbook

## Ship Details
- Name: ~sampel-palnet
- Deploy Date: 2025-01-15
- Server: example.com (12.34.56.78)
- Domain: https://sampel-palnet.example.com
- Pier: /home/urbit/urbit-ships/sampel-palnet

## Maintenance Procedures

### Weekly
- Check logs: `sudo journalctl -u urbit@sampel-palnet -n 100`
- Verify automatic updates: `sudo unattended-upgrades --dry-run`
- Monitor disk usage: `df -h /`

### Monthly
- Review security advisories
- Update Urbit binary if new version
- Test backup restoration
- Run |pack in dojo (memory optimization)

### Quarterly
- Full security audit
- Performance review (compare to baseline)
- Update documentation

## Troubleshooting
- Ship won't start: Check `sudo systemctl status urbit@sampel-palnet`
- OTA fails: Verify sponsor connectivity, check logs
- High memory: Run |pack in dojo
- SSL certificate expired: Run `sudo certbot renew`

## Emergency Contacts
- Operator: <contact info>
- Urbit Support: support@urbit.org
```

**Success Criteria**:
- All validation checklist items passed
- Baseline metrics recorded
- Runbook created and reviewed
- Operator trained on maintenance procedures
- Emergency procedures documented

---

## Post-Deployment

### Immediate Next Steps
1. Install essential apps (e.g., Groups, Talk)
2. Join Urbit community groups
3. Configure privacy settings
4. Set up safe backup procedure (weekly schedule)

### Maintenance Schedule

**Weekly**:
- Check logs for errors or warnings
- Verify automatic updates applied
- Monitor disk space (alert at 85%)
- Quick security check (SSH attempts, firewall logs)

**Monthly**:
- Review security advisories
- Update Urbit binary if new version available
- Test backup restoration procedure
- Run |pack for memory optimization
- Review and rotate logs

**Quarterly**:
- Full security audit (lynis or similar)
- Performance review (compare to baseline)
- Documentation updates
- Review and update runbook
- Test disaster recovery procedures

### Success Metrics

**Availability**: >99% uptime
**Performance**: Dojo commands <3 seconds
**Security**: No unauthorized access attempts succeed
**Maintenance**: All procedures documented and followed

---

## Common Issues and Resolutions

### Boot Failures
**Issue**: Ship won't boot or crashes immediately
**Diagnostics**:
- Check logs: `sudo journalctl -u urbit@sampel-palnet -n 100`
- Verify keyfile (first boot only)
- Check disk space: `df -h`
- Verify binary version: `urbit --version`

**Solutions**:
- Keyfile already used: Boot without keyfile
- Insufficient disk: Free space or add storage
- Corrupted pier: Restore from backup
- Binary mismatch: Update/downgrade binary

### Network Problems
**Issue**: Can't access ship via HTTPS or Ames offline
**Diagnostics**:
- Test firewall: `sudo ufw status`
- Test ports: `nc -zv localhost 8080`
- Check DNS: `nslookup your-domain.com`
- Verify SSL: `curl -I https://your-domain.com`

**Solutions**:
- Firewall blocking: Verify UFW rules
- Port not listening: Restart ship service
- DNS not propagated: Wait for TTL expiry
- SSL expired: Run `sudo certbot renew`

### Performance Issues
**Issue**: Ship slow, high CPU/RAM, or disk I/O bottleneck
**Diagnostics**:
- Check resources: `top`, `htop`
- Check I/O: `iotop`
- Check pier size: `du -sh ~/urbit-ships/sampel-palnet`

**Solutions**:
- High memory: Run |pack in dojo
- Disk I/O: Check I/O scheduler, consider SSD upgrade
- Pier bloat: Run |pack, clean unused desks
- CPU high: Check for misbehaving apps

---

## Rollback Procedures

If deployment fails at any phase:

**Phase 1-4**: No rollback needed (no changes made)

**Phase 5 (Ship Boot)**:
- Stop ship: Ctrl+D in dojo or `sudo systemctl stop urbit@ship-name`
- Remove pier: `rm -rf ~/urbit-ships/ship-name`
- Restart from Phase 4

**Phase 6-7 (Network/SSL)**:
- Revert firewall: `sudo ufw disable`
- Remove nginx config: `sudo rm /etc/nginx/sites-enabled/urbit-ship`

**Phase 8-10 (Service/Hardening)**:
- Stop and disable service: `sudo systemctl stop urbit@ship-name && sudo systemctl disable urbit@ship-name`
- Remove service file: `sudo rm /etc/systemd/system/urbit@.service`
- Revert security changes (restore SSH config, remove fail2ban)

**Complete Rollback** (nuclear option):
- Stop all services
- Remove pier directory
- Revert all configuration changes
- Start from Phase 1

---

## Estimated Timeline

**Total Time**: 1-3 hours (varies by experience level)

- Phase 1 (System Assessment): 5-10 min
- Phase 2 (Dependencies): 5-10 min
- Phase 3 (Binary Install): 5 min
- Phase 4 (Keyfile Prep): 5 min
- Phase 5 (Ship Boot): 15-30 min (sync time)
- Phase 6 (Network Config): 10-15 min
- Phase 7 (DNS/SSL): 15-30 min (DNS propagation)
- Phase 8 (Systemd): 10-15 min
- Phase 9 (Hardening): 20-40 min (depends on level)
- Phase 10 (Validation): 15-30 min

**First-time operators**: Allow 3-4 hours
**Experienced operators**: 1-2 hours

---

## Integration

This command invokes the **urbit-deployment-specialist** agent throughout all phases for expert guidance, troubleshooting, and decision-making.

The agent references these skills:
- urbit-fundamentals (architecture understanding)
- ship-deployment-guide (step-by-step procedures)
- urbit-troubleshooting (diagnostic workflows)
- performance-optimization (system tuning)
