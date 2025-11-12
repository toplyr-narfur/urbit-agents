---
name: network-security-advanced
description: Advanced VPS security hardening for Urbit deployments including SSH hardening, firewall configuration, intrusion detection, fail2ban, DDoS protection, VPN setup, and zero-trust principles following 2025 security best practices. Use when hardening VPS security, implementing defense-in-depth, configuring firewalls, or meeting security compliance requirements.
---

# Network Security Advanced Skill

Advanced VPS security hardening for Urbit ship deployments including SSH hardening, firewall configuration, intrusion detection, and zero-trust principles (2025).

## Overview

Advanced network security for Urbit VPS deployments implements defense-in-depth strategies, continuous monitoring, automated threat response, and compliance with 2025 security best practices.

## SSH Hardening (2025 Best Practices)

### Change Default SSH Port

**Security benefit**: Reduces automated attack surface by obscuring SSH access.

```bash
# Edit SSH config
sudo nano /etc/ssh/sshd_config

# Change port (use non-standard port 2000-65535)
Port 2222  # Example: avoid 22

# Restart SSH
sudo systemctl restart sshd
```

**Update firewall**:
```bash
sudo ufw allow 2222/tcp
sudo ufw delete allow 22/tcp
```

### Disable Root Login

**Critical**: Force administrative tasks through sudo with complete audit trails.

```bash
# /etc/ssh/sshd_config
PermitRootLogin no
```

### SSH Key-Only Authentication

**Foundational best practice**: Eliminates brute-force password attacks.

```bash
# /etc/ssh/sshd_config
PasswordAuthentication no
PubkeyAuthentication yes
ChallengeResponseAuthentication no
```

**Generate and upload key**:
```bash
# On local machine
ssh-keygen -t ed25519 -C "urbit-vps-key"

# Upload public key
ssh-copy-id -i ~/.ssh/id_ed25519.pub -p 2222 urbit@vps-ip

# Test before disabling passwords!
ssh -p 2222 urbit@vps-ip
```

### Additional SSH Hardening

```bash
# /etc/ssh/sshd_config

# Limit users
AllowUsers urbit admin
DenyUsers root

# Authentication limits
MaxAuthTries 3
MaxSessions 2

# Disable unused features
X11Forwarding no
AllowTcpForwarding no
PermitTunnel no

# Strong ciphers only (2025 - quantum-resistant priority)
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-gcm@openssh.com,aes128-ctr
MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com
KexAlgorithms sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha256

# Host key algorithms (2025)
HostKeyAlgorithms ssh-ed25519-cert-v01@openssh.com,ssh-ed25519,rsa-sha2-512-cert-v01@openssh.com,rsa-sha2-512

# RSA key size requirements (quantum countermeasure)
RequiredRSASize 3072

# Session timeouts
ClientAliveInterval 300
ClientAliveCountMax 2

# Restart to apply
sudo systemctl restart sshd
```

---

## Firewall Configuration (UFW)

### Basic UFW Setup

```bash
# Install UFW
sudo apt install ufw -y

# Default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow essential services
sudo ufw allow 2222/tcp comment 'SSH custom port'
sudo ufw allow 80/tcp comment 'HTTP'
sudo ufw allow 443/tcp comment 'HTTPS'
sudo ufw allow 34543/udp comment 'Urbit Ames'

# Enable firewall
sudo ufw enable

# Verify
sudo ufw status numbered
```

### IP Whitelisting (Trusted Access)

```bash
# Allow SSH from specific IPs only
sudo ufw delete allow 2222/tcp
sudo ufw allow from 203.0.113.10 to any port 2222 proto tcp comment 'SSH - Home IP'
sudo ufw allow from 203.0.113.20 to any port 2222 proto tcp comment 'SSH - Office IP'
```

### Rate Limiting

```bash
# Limit SSH connection attempts (prevents brute force)
sudo ufw limit 2222/tcp comment 'SSH rate limit'
```

**How it works**: Denies connections from IPs that attempt 6+ connections within 30 seconds.

### Advanced UFW Rules

```bash
# Block Docker bypass (important for GroundSeg)
sudo nano /etc/ufw/after.rules

# Add at end:
*filter
:DOCKER-USER - [0:0]
-A DOCKER-USER -j RETURN
COMMIT

# Reload UFW
sudo ufw reload
```

---

## Fail2ban (Intrusion Prevention)

### Installation and Configuration

```bash
# Install
sudo apt install fail2ban -y

# Create local config
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```

### SSH Jail Configuration (2025)

**Conservative** (recommended for most deployments):
```ini
[sshd]
enabled = true
port = 2222  # Match your custom SSH port
filter = sshd
logpath = /var/log/auth.log
maxretry = 5  # Allow 5 failed attempts
bantime = 3600  # Ban for 1 hour (3600 seconds)
findtime = 600  # Within 10 minutes
```

**Stricter** (high-security environments):
```ini
[sshd]
enabled = true
port = 2222  # Match your custom SSH port
filter = sshd
logpath = /var/log/auth.log
maxretry = 5  # Allow 5 failed attempts
bantime = 1209600  # Ban for 2 weeks (1209600 seconds)
findtime = 86400  # Within 1 day (86400 seconds)
```

### Advanced Fail2ban Settings

```ini
[DEFAULT]
# Email alerts (optional)
destemail = admin@yourdomain.com
sendername = Fail2Ban
mta = sendmail
action = %(action_mwl)s  # Ban + email with logs

# Whitelist trusted IPs (never ban)
ignoreip = 127.0.0.1/8 ::1 203.0.113.10

# Global ban settings
bantime = 86400  # 24 hours
findtime = 600  # 10 minutes
maxretry = 5
```

### Additional Jails

```ini
# Nginx rate limiting
[nginx-limit-req]
enabled = true
filter = nginx-limit-req
logpath = /var/log/nginx/error.log
maxretry = 10
bantime = 7200

# Nginx authentication failures
[nginx-auth]
enabled = true
filter = nginx-auth
logpath = /var/log/nginx/error.log
maxretry = 5
bantime = 3600
```

### Manage Fail2ban

```bash
# Start and enable
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# Check status
sudo fail2ban-client status
sudo fail2ban-client status sshd

# Unban IP manually
sudo fail2ban-client set sshd unbanip 203.0.113.50
```

---

## Intrusion Detection (Suricata)

### Why Suricata?

Suricata automatically identifies malicious traffic and sources, which can be added to firewall blocklists.

### Installation

```bash
# Add repository
sudo add-apt-repository ppa:oisf/suricata-stable -y
sudo apt update

# Install
sudo apt install suricata -y

# Update rules
sudo suricata-update
```

### Basic Configuration

```bash
# Edit config
sudo nano /etc/suricata/suricata.yaml

# Set network interface
af-packet:
  - interface: eth0

# Home network (adjust to your VPS subnet)
HOME_NET: "[203.0.113.0/24]"

# Enable rules
rule-files:
  - suricata.rules
```

### Start and Monitor

```bash
# Start Suricata
sudo systemctl enable suricata
sudo systemctl start suricata

# Monitor alerts
sudo tail -f /var/log/suricata/fast.log

# View detailed events
sudo tail -f /var/log/suricata/eve.json | jq .
```

---

## TLS/SSL Hardening (Nginx)

### Modern TLS Configuration (2025)

```nginx
# /etc/nginx/sites-available/urbit-ship

server {
    listen 443 ssl http2;
    server_name ship.yourdomain.com;

    # SSL certificates (Let's Encrypt)
    ssl_certificate /etc/letsencrypt/live/ship.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/ship.yourdomain.com/privkey.pem;

    # TLS 1.3 only (2025 best practice)
    ssl_protocols TLSv1.3;

    # Strong ciphers (TLS 1.3 handles automatically)
    ssl_prefer_server_ciphers off;

    # SSL session cache
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # OCSP stapling
    ssl_stapling on;
    ssl_stapling_verify on;
    ssl_trusted_certificate /etc/letsencrypt/live/ship.yourdomain.com/chain.pem;

    # Security headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;

    # Disable server tokens
    server_tokens off;

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
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name ship.yourdomain.com;
    return 301 https://$server_name$request_uri;
}
```

### Test TLS Configuration

```bash
# Test SSL (should get A+ rating)
# Visit: https://www.ssllabs.com/ssltest/analyze.html?d=ship.yourdomain.com

# Or use testssl.sh
git clone https://github.com/drwetter/testssl.sh.git
cd testssl.sh
./testssl.sh ship.yourdomain.com
```

---

## Kernel Security (sysctl)

### Network Security Tuning

```bash
# Create security config
sudo nano /etc/sysctl.d/99-security.conf
```

```ini
# IP Forwarding (disable unless needed)
net.ipv4.ip_forward = 0
net.ipv6.conf.all.forwarding = 0

# SYN flood protection
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_syn_retries = 2
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_max_syn_backlog = 4096

# Disable ICMP redirect acceptance
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0

# Disable ICMP redirect sending
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0

# Enable source address verification (anti-spoofing)
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1

# Ignore ICMP ping requests (optional, may break diagnostics)
# net.ipv4.icmp_echo_ignore_all = 1

# Ignore bogus ICMP error responses
net.ipv4.icmp_ignore_bogus_error_responses = 1

# Log suspicious packets
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1
```

```bash
# Apply settings
sudo sysctl -p /etc/sysctl.d/99-security.conf
```

---

## Automated Security Updates

### Unattended Upgrades (2025)

```bash
# Install
sudo apt install unattended-upgrades -y

# Configure
sudo dpkg-reconfigure -plow unattended-upgrades
```

**Answer**: Yes to automatic installation of security updates.

### Advanced Configuration

```bash
# Edit config
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

```
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
    "${distro_id}ESMApps:${distro_codename}-apps-security";
    "${distro_id}ESM:${distro_codename}-infra-security";
};

// Auto-reboot if required
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "03:00";  // 3 AM

// Email notifications
Unattended-Upgrade::Mail "admin@yourdomain.com";
Unattended-Upgrade::MailReport "only-on-error";

// Remove unused dependencies
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
```

---

## Security Monitoring and Auditing

### Lynis Security Auditing

```bash
# Install
sudo apt install lynis -y

# Run audit
sudo lynis audit system

# Target: Hardening index ≥75
```

### Log Monitoring

```bash
# Monitor authentication logs
sudo tail -f /var/log/auth.log

# Monitor Fail2ban activity
sudo tail -f /var/log/fail2ban.log

# Monitor UFW blocks
sudo tail -f /var/log/ufw.log
```

### Automated Alerting

```bash
# Install logwatch
sudo apt install logwatch -y

# Configure daily email reports
sudo nano /etc/cron.daily/00logwatch
```

```bash
#!/bin/bash
/usr/sbin/logwatch --output mail --mailto admin@yourdomain.com --detail high
```

---

## Zero-Trust Principles

### 1. Principle of Least Privilege

```bash
# Limit sudo access
sudo visudo

# Only allow specific commands
urbit ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart urbit-*, /usr/bin/systemctl status urbit-*
```

### 2. Network Segmentation

```bash
# Use VPC/private networks for multi-ship
# Isolate public-facing services from internal
```

### 3. Multi-Factor Authentication (2FA)

```bash
# Install Google Authenticator
sudo apt install libpam-google-authenticator -y

# Configure for user
google-authenticator

# Enable in SSH
sudo nano /etc/pam.d/sshd
# Add: auth required pam_google_authenticator.so

# /etc/ssh/sshd_config
ChallengeResponseAuthentication yes
AuthenticationMethods publickey,keyboard-interactive
```

---

## Security Hardening Checklist

- [ ] SSH port changed from 22
- [ ] Root login disabled
- [ ] Password authentication disabled
- [ ] SSH key-only authentication configured
- [ ] UFW firewall enabled with minimal rules
- [ ] Fail2ban configured and running
- [ ] Suricata intrusion detection active (optional)
- [ ] TLS 1.3 enforced on Nginx
- [ ] Security headers configured
- [ ] Kernel security parameters tuned (sysctl)
- [ ] Unattended security updates enabled
- [ ] Lynis audit score ≥75
- [ ] Log monitoring automated (logwatch)
- [ ] IP whitelisting for SSH (if applicable)
- [ ] 2FA enabled (optional, advanced)

---

## Best Practices

1. **Defense in depth**: Multiple security layers (firewall, IDS, fail2ban)
2. **Principle of least privilege**: Minimal permissions, minimal services
3. **Regular updates**: Automated security patches
4. **Monitoring**: Continuous log analysis and alerting
5. **Testing**: Regularly audit security posture (Lynis)
6. **Documentation**: Record all security configurations
7. **Backup authentication**: Store SSH keys securely offline
8. **Incident response**: Have runbook for security incidents

---

## Reference

- SSH hardening: https://www.ssh.com/academy/ssh/sshd_config
- UFW guide: https://help.ubuntu.com/community/UFW
- Fail2ban: https://github.com/fail2ban/fail2ban
- Suricata: https://suricata.io/
- Mozilla SSL config: https://ssl-config.mozilla.org/
- CIS security benchmarks: https://www.cisecurity.org/cis-benchmarks

---

## Summary

Advanced network security for Urbit VPS deployments implements SSH hardening (key-only auth, custom port, root disabled), firewall configuration (UFW with IP whitelisting, rate limiting), intrusion prevention (Fail2ban with 1-hour bans), intrusion detection (Suricata), TLS 1.3 enforcement, kernel security tuning (sysctl), and automated security updates. Defense-in-depth approach combines multiple security layers with continuous monitoring (logwatch, Lynis audits), achieving hardening index ≥75. Zero-trust principles enforce least privilege and network segmentation.
