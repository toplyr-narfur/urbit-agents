---
name: setup-production
description: Comprehensive 20-phase production hardening workflow for Urbit ships with enterprise security, monitoring, and operational excellence
subagent_type: urbit-deployment-specialist
---

# Setup Production Command

This command orchestrates a comprehensive 20-phase production hardening workflow that transforms a basic Urbit deployment into an enterprise-grade, secure, monitored, and maintainable production system.

## Overview

The setup-production workflow implements defense-in-depth security, operational monitoring, disaster recovery, and compliance-ready documentation for Urbit ships. It follows 2025 best practices for Ubuntu/Debian server hardening, systemd service security, and Urbit-specific production configurations.

## Configuration Options

```yaml
System Context:
  hostname: "urbit-prod-01"
  ship_name: "~sampel-palnet"
  pier_path: "/home/urbit/sampel-palnet"
  deployment_type: "bare-metal"  # bare-metal | vps | dedicated

Security Level:
  level: "enterprise"  # basic | standard | enterprise | maximum
  compliance_framework: "none"  # none | soc2 | hipaa | gdpr | iso27001

SSH Configuration:
  ssh_port: 22  # Recommend changing to non-standard port (e.g., 2222)
  ssh_allow_users: ["admin", "urbit-ops"]  # Specific users allowed SSH access
  fail2ban_enabled: true
  fail2ban_maxretry: 5
  fail2ban_bantime: 3600  # 1 hour

Firewall Configuration:
  firewall_type: "ufw"  # ufw | firewalld
  allowed_ports:
    - 22/tcp    # SSH (or custom port)
    - 80/tcp    # HTTP
    - 443/tcp   # HTTPS
    - 34543/udp # Ames
  rate_limiting: true

Monitoring Level:
  level: "comprehensive"  # basic | standard | comprehensive | enterprise
  alerting: true
  log_aggregation: true
  metrics_retention_days: 30

Backup Configuration:
  backup_enabled: true
  backup_schedule: "weekly"  # daily | weekly | custom
  backup_retention: 7  # Number of backups to keep
  backup_location: "/backups/urbit"
  offsite_backup: false  # Enable for production
  encryption: true

Documentation:
  runbook_creation: true
  incident_response_plan: true
  maintenance_schedules: true
```

## 20-Phase Production Hardening Workflow

### Phase 1: Security Audit Initiation

**Objective**: Document current security posture and establish baseline

**Tasks**:
1. Run system security scan:
   ```bash
   # Install Lynis security auditing tool
   sudo apt update
   sudo apt install lynis -y

   # Run comprehensive security audit
   sudo lynis audit system --quick

   # Save report
   sudo cp /var/log/lynis-report.dat /var/log/lynis-report-$(date +%Y%m%d).dat
   ```

2. Document current configuration:
   ```bash
   # System information
   uname -a > /tmp/security-audit.txt
   lsb_release -a >> /tmp/security-audit.txt

   # Network configuration
   ip addr show >> /tmp/security-audit.txt
   sudo ufw status verbose >> /tmp/security-audit.txt

   # Installed packages
   dpkg -l >> /tmp/security-audit.txt

   # Running services
   systemctl list-units --type=service --state=running >> /tmp/security-audit.txt
   ```

3. Identify existing vulnerabilities:
   ```bash
   # Check for available security updates
   sudo apt update
   sudo apt list --upgradable | grep security
   ```

**Success Criteria**:
- [ ] Lynis audit completed (baseline score documented)
- [ ] Current configuration documented
- [ ] Vulnerability list created
- [ ] Hardening roadmap defined

---

### Phase 2: SSH Hardening (2025 Best Practices)

**Objective**: Implement key-only authentication and brute-force protection

**Tasks**:
1. Configure SSH for maximum security:
   ```bash
   # Backup original SSH config
   sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

   # Edit SSH configuration
   sudo nano /etc/ssh/sshd_config
   ```

   **Critical SSH Settings** (2025 best practices):
   ```
   # Disable password authentication (key-only)
   PasswordAuthentication no
   PubkeyAuthentication yes

   # Disable root login
   PermitRootLogin no

   # Limit users who can SSH
   AllowUsers admin urbit-ops

   # Use SSH Protocol 2 only
   Protocol 2

   # Disable empty passwords
   PermitEmptyPasswords no

   # Disable X11 forwarding (unless needed)
   X11Forwarding no

   # Set client alive interval (prevent zombie connections)
   ClientAliveInterval 300
   ClientAliveCountMax 2

   # Limit authentication attempts
   MaxAuthTries 3
   MaxSessions 2

   # Strong ciphers only (2025 - quantum-resistant priority)
   Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-gcm@openssh.com,aes128-ctr
   MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com
   KexAlgorithms sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha256
   HostKeyAlgorithms ssh-ed25519-cert-v01@openssh.com,ssh-ed25519,rsa-sha2-512-cert-v01@openssh.com,rsa-sha2-512
   RequiredRSASize 3072

   # Optional: Change SSH port (reduces automated attacks)
   # Port 2222  # Uncomment to use non-standard port
   ```

2. Restart SSH service:
   ```bash
   # Test configuration
   sudo sshd -t

   # If OK, restart (IMPORTANT: Keep current session open!)
   sudo systemctl restart sshd

   # Test login from NEW terminal before closing current session
   ```

3. Set up SSH key authentication (if not already done):
   ```bash
   # On local machine (not server):
   ssh-keygen -t ed25519 -C "urbit-production-access"

   # Copy public key to server:
   ssh-copy-id -i ~/.ssh/id_ed25519.pub admin@server-ip

   # Test key-based login:
   ssh -i ~/.ssh/id_ed25519 admin@server-ip
   ```

**Success Criteria**:
- [ ] Password authentication disabled
- [ ] Root login disabled
- [ ] SSH key authentication working
- [ ] SSH configuration tested from new session
- [ ] Original session kept open during testing

---

### Phase 3: Fail2ban Installation and Configuration

**Objective**: Protect against brute-force attacks on SSH and other services

**Tasks**:
1. Install Fail2ban:
   ```bash
   sudo apt install fail2ban -y
   ```

2. Configure Fail2ban with 2025 best practices:
   ```bash
   # Create local configuration (overrides defaults)
   sudo nano /etc/fail2ban/jail.local
   ```

   **Fail2ban Configuration**:
   ```ini
   [DEFAULT]
   # Ban duration: 1 hour (3600 seconds)
   bantime = 3600

   # Find time: 10 minutes (600 seconds)
   # If maxretry failures occur within findtime, ban IP
   findtime = 600

   # Maximum retry attempts before ban
   maxretry = 5

   # Destination email for alerts (optional)
   destemail = admin@example.com
   sendername = Fail2Ban-Urbit

   # Action: ban and send email (or just ban)
   action = %(action_mwl)s

   [sshd]
   enabled = true
   port = ssh
   # If using custom SSH port: port = 2222
   logpath = /var/log/auth.log
   maxretry = 5
   bantime = 3600
   findtime = 600

   # Optional: Protect Nginx from attacks
   [nginx-http-auth]
   enabled = true
   port = http,https
   logpath = /var/log/nginx/error.log

   [nginx-limit-req]
   enabled = true
   port = http,https
   logpath = /var/log/nginx/error.log
   ```

3. Enable and start Fail2ban:
   ```bash
   # Start service
   sudo systemctl enable fail2ban
   sudo systemctl start fail2ban

   # Check status
   sudo systemctl status fail2ban

   # View banned IPs
   sudo fail2ban-client status sshd
   ```

4. Test Fail2ban (optional):
   ```bash
   # From another machine, attempt failed logins
   # After maxretry (5) failures, IP should be banned

   # Check ban status
   sudo fail2ban-client status sshd

   # Unban IP if needed (for testing)
   sudo fail2ban-client set sshd unbanip IP_ADDRESS
   ```

**Success Criteria**:
- [ ] Fail2ban installed and running
- [ ] SSH jail configured (bantime=3600, maxretry=5, findtime=600)
- [ ] Nginx jails configured (if using reverse proxy)
- [ ] Ban/unban functionality tested
- [ ] Alerts configured (if using email)

---

### Phase 4: Firewall Configuration (UFW)

**Objective**: Implement default-deny firewall with minimal attack surface

**Tasks**:
1. Install UFW (if not already installed):
   ```bash
   sudo apt install ufw -y
   ```

2. Configure firewall rules (BEFORE enabling):
   ```bash
   # CRITICAL: Allow SSH FIRST to avoid lockout
   sudo ufw allow 22/tcp
   # Or if using custom SSH port:
   # sudo ufw allow 2222/tcp

   # Allow HTTP and HTTPS (for ship web access)
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp

   # Allow Ames networking (UDP 34543)
   sudo ufw allow 34543/udp

   # Set default policies
   sudo ufw default deny incoming
   sudo ufw default allow outgoing

   # Optional: Rate limiting for SSH (prevents brute-force)
   sudo ufw limit 22/tcp
   ```

3. Enable firewall:
   ```bash
   # Enable UFW (will prompt for confirmation)
   sudo ufw enable

   # Verify status
   sudo ufw status verbose

   # Should show:
   # Status: active
   # 22/tcp  ALLOW IN  (or 2222/tcp if custom port)
   # 80/tcp  ALLOW IN
   # 443/tcp ALLOW IN
   # 34543/udp ALLOW IN
   ```

4. Verify connectivity:
   ```bash
   # Test SSH from new session (DON'T CLOSE CURRENT SESSION)
   # Test from local machine:
   ssh admin@server-ip

   # Test HTTP (if ship running):
   curl http://localhost:8080
   ```

**Success Criteria**:
- [ ] UFW enabled with default deny incoming
- [ ] SSH port allowed (tested from new session)
- [ ] HTTP/HTTPS ports allowed (80, 443)
- [ ] Ames port allowed (34543/udp)
- [ ] Rate limiting enabled on SSH
- [ ] Firewall status shows 4-5 rules total

---

### Phase 5: Automatic Security Updates

**Objective**: Enable automatic installation of security patches

**Tasks**:
1. Install unattended-upgrades:
   ```bash
   sudo apt install unattended-upgrades apt-listchanges -y
   ```

2. Configure automatic updates:
   ```bash
   # Reconfigure with interactive prompts
   sudo dpkg-reconfigure -plow unattended-upgrades
   # Select "Yes" to enable automatic updates
   ```

3. Customize update behavior (optional):
   ```bash
   sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
   ```

   **Key Settings**:
   ```
   // Automatically upgrade packages from security repositories
   Unattended-Upgrade::Allowed-Origins {
       "${distro_id}:${distro_codename}-security";
       // Optionally enable stable updates:
       // "${distro_id}:${distro_codename}-updates";
   };

   // Automatically reboot if required (for kernel updates)
   Unattended-Upgrade::Automatic-Reboot "false";
   // If enabling auto-reboot, set time:
   // Unattended-Upgrade::Automatic-Reboot-Time "02:00";

   // Email notification
   Unattended-Upgrade::Mail "admin@example.com";
   Unattended-Upgrade::MailReport "only-on-error";
   ```

4. Enable automatic updates schedule:
   ```bash
   sudo nano /etc/apt/apt.conf.d/20auto-upgrades
   ```

   **Configuration**:
   ```
   APT::Periodic::Update-Package-Lists "1";
   APT::Periodic::Download-Upgradeable-Packages "1";
   APT::Periodic::AutocleanInterval "7";
   APT::Periodic::Unattended-Upgrade "1";
   ```

5. Test configuration:
   ```bash
   # Dry run (shows what would be upgraded)
   sudo unattended-upgrade --dry-run --debug
   ```

**Success Criteria**:
- [ ] Unattended-upgrades installed and configured
- [ ] Security updates automatic
- [ ] Email notifications configured (optional)
- [ ] Auto-reboot policy set (recommend: false for production)
- [ ] Dry-run test successful

---

### Phase 6: User Account Security

**Objective**: Create dedicated non-root user for Urbit operations

**Tasks**:
1. Create dedicated urbit user:
   ```bash
   # Create user with home directory
   sudo adduser urbit

   # Set strong password (or use SSH key only)
   # Password complexity: 16+ chars, mixed case, numbers, symbols
   ```

2. Configure sudo access (if needed):
   ```bash
   # Add urbit user to sudo group (optional, use with caution)
   # sudo usermod -aG sudo urbit

   # Better: Use sudoers for specific commands only
   sudo visudo

   # Add line for limited sudo:
   # urbit ALL=(ALL) NOPASSWD: /bin/systemctl restart urbit-*, /bin/journalctl
   ```

3. Set up SSH key for urbit user:
   ```bash
   # As urbit user
   su - urbit
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh

   # Copy authorized_keys from admin user or add new key
   # Option 1: Copy from admin
   sudo cp /home/admin/.ssh/authorized_keys /home/urbit/.ssh/
   sudo chown urbit:urbit /home/urbit/.ssh/authorized_keys
   chmod 600 /home/urbit/.ssh/authorized_keys

   # Option 2: Add new key
   # Paste public key into:
   nano ~/.ssh/authorized_keys
   chmod 600 ~/.ssh/authorized_keys
   ```

4. Disable root account (optional, high security):
   ```bash
   # Lock root password
   sudo passwd -l root

   # Verify root is locked
   sudo passwd -S root
   # Should show: "L" (locked)
   ```

5. Set up password policies (PAM):
   ```bash
   # Install password quality checker
   sudo apt install libpam-pwquality -y

   # Configure password requirements
   sudo nano /etc/security/pwquality.conf
   ```

   **Password Policy**:
   ```
   # Minimum password length
   minlen = 16

   # Require at least one digit
   dcredit = -1

   # Require at least one uppercase
   ucredit = -1

   # Require at least one lowercase
   lcredit = -1

   # Require at least one other character
   ocredit = -1

   # Number of characters that must be different from old password
   difok = 8
   ```

**Success Criteria**:
- [ ] Dedicated 'urbit' user created
- [ ] SSH key authentication working for urbit user
- [ ] Sudo access properly restricted (if needed)
- [ ] Root account locked (optional)
- [ ] Password policy enforced (16+ chars, complexity)

---

### Phase 7: Service Account Isolation

**Objective**: Isolate Urbit ship service from system

**Tasks**:
1. Move pier to urbit user home:
   ```bash
   # If pier currently in different location
   sudo mv /path/to/current/pier /home/urbit/sampel-palnet

   # Set ownership
   sudo chown -R urbit:urbit /home/urbit/sampel-palnet

   # Set permissions (pier should be private)
   chmod 700 /home/urbit/sampel-palnet
   ```

2. Verify urbit binary permissions:
   ```bash
   # Check urbit binary
   ls -la /usr/local/bin/urbit

   # Should be executable by all:
   # -rwxr-xr-x root root /usr/local/bin/urbit

   # If not, fix:
   sudo chmod 755 /usr/local/bin/urbit
   ```

**Success Criteria**:
- [ ] Pier owned by urbit:urbit
- [ ] Pier permissions set to 700 (owner-only access)
- [ ] Urbit binary executable
- [ ] Service can start as urbit user

---

### Phase 8: Systemd Service Hardening (2025 Best Practices)

**Objective**: Create hardened systemd service with security sandboxing

**Tasks**:
1. Create systemd service file:
   ```bash
   sudo nano /etc/systemd/system/urbit-sampel-palnet.service
   ```

   **Hardened Systemd Service** (2025 security options):
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
   MemoryHigh=3G

   # Security Hardening (2025 Best Practices)
   # Prevent privilege escalation
   NoNewPrivileges=yes

   # Private /tmp directory (isolates temp files)
   PrivateTmp=yes

   # Restrict device access
   PrivateDevices=yes

   # Protect system files
   ProtectSystem=strict
   ReadWritePaths=/home/urbit

   # Protect /home directories of other users
   ProtectHome=yes

   # Restrict namespace usage (container isolation)
   RestrictNamespaces=yes

   # Prevent real-time scheduling (DoS protection)
   RestrictRealtime=yes

   # Prevent SUID/SGID execution
   RestrictSUIDSGID=yes

   # Prevent write+execute memory (exploit mitigation)
   MemoryDenyWriteExecute=yes

   # Lock personality (prevent exploit techniques)
   LockPersonality=yes

   # Restrict address families (limit networking)
   RestrictAddressFamilies=AF_INET AF_INET6 AF_UNIX

   # System call filtering (advanced - test thoroughly)
   # SystemCallFilter=@system-service
   # SystemCallErrorNumber=EPERM

   # Logging
   StandardOutput=journal
   StandardError=journal
   SyslogIdentifier=urbit-sampel-palnet

   [Install]
   WantedBy=multi-user.target
   ```

2. Reload systemd and enable service:
   ```bash
   # Reload systemd daemon
   sudo systemctl daemon-reload

   # Enable service to start on boot
   sudo systemctl enable urbit-sampel-palnet

   # Start service
   sudo systemctl start urbit-sampel-palnet

   # Check status
   sudo systemctl status urbit-sampel-palnet
   ```

3. Analyze service security:
   ```bash
   # Use systemd-analyze to check security score
   systemd-analyze security urbit-sampel-palnet

   # Should show score of 2.5 or better (lower is more secure)
   # Exposure level: medium or better
   ```

4. Test service functionality:
   ```bash
   # Check logs
   sudo journalctl -u urbit-sampel-palnet -n 50 -f

   # Test restart
   sudo systemctl restart urbit-sampel-palnet

   # Test auto-restart on failure
   sudo systemctl kill -s SIGKILL urbit-sampel-palnet
   # Should auto-restart within 10 seconds
   sudo systemctl status urbit-sampel-palnet
   ```

**Success Criteria**:
- [ ] Systemd service created with hardening options
- [ ] Service enabled and running
- [ ] Security score ≤ 2.5 (systemd-analyze)
- [ ] Auto-restart on failure working
- [ ] Logs visible in journalctl
- [ ] Ship accessible via dojo and web

---

### Phase 9: Disable Unnecessary Services

**Objective**: Minimize attack surface by disabling unused services

**Tasks**:
1. List running services:
   ```bash
   systemctl list-units --type=service --state=running
   ```

2. Identify unnecessary services (common candidates):
   - `bluetooth.service` (if no Bluetooth hardware)
   - `cups.service` (printing, usually not needed on servers)
   - `avahi-daemon.service` (mDNS, rarely needed)
   - `ModemManager.service` (modem support)

3. Disable unnecessary services:
   ```bash
   # Example: Disable Bluetooth
   sudo systemctl stop bluetooth.service
   sudo systemctl disable bluetooth.service

   # Example: Disable CUPS
   sudo systemctl stop cups.service
   sudo systemctl disable cups.service

   # Verify
   systemctl list-units --type=service --state=running
   ```

4. Document disabled services:
   ```bash
   echo "Disabled services for security hardening:" > /tmp/disabled-services.txt
   systemctl list-unit-files --type=service --state=disabled >> /tmp/disabled-services.txt
   ```

**Success Criteria**:
- [ ] Unnecessary services identified
- [ ] Services disabled and stopped
- [ ] No impact on Urbit ship functionality
- [ ] Disabled services documented

---

### Phase 10: Kernel Hardening (sysctl)

**Objective**: Harden kernel parameters for security and networking

**Tasks**:
1. Create sysctl security configuration:
   ```bash
   sudo nano /etc/sysctl.d/99-urbit-security.conf
   ```

   **Sysctl Security Configuration**:
   ```
   # Network Security

   # Prevent IP spoofing (reverse path filtering)
   net.ipv4.conf.all.rp_filter = 1
   net.ipv4.conf.default.rp_filter = 1

   # Disable IP forwarding (not a router)
   net.ipv4.ip_forward = 0
   net.ipv6.conf.all.forwarding = 0

   # Disable source routing
   net.ipv4.conf.all.accept_source_route = 0
   net.ipv4.conf.default.accept_source_route = 0
   net.ipv6.conf.all.accept_source_route = 0
   net.ipv6.conf.default.accept_source_route = 0

   # Ignore ICMP redirects
   net.ipv4.conf.all.accept_redirects = 0
   net.ipv4.conf.default.accept_redirects = 0
   net.ipv6.conf.all.accept_redirects = 0
   net.ipv6.conf.default.accept_redirects = 0

   # Ignore secure ICMP redirects
   net.ipv4.conf.all.secure_redirects = 0
   net.ipv4.conf.default.secure_redirects = 0

   # Ignore ICMP ping requests (optional, reduces DoS risk)
   # net.ipv4.icmp_echo_ignore_all = 1

   # Log suspicious packets
   net.ipv4.conf.all.log_martians = 1
   net.ipv4.conf.default.log_martians = 1

   # Protect against SYN flood attacks
   net.ipv4.tcp_syncookies = 1
   net.ipv4.tcp_syn_retries = 2
   net.ipv4.tcp_synack_retries = 2
   net.ipv4.tcp_max_syn_backlog = 4096

   # Kernel Security

   # Restrict kernel pointer access (prevents some exploits)
   kernel.kptr_restrict = 2

   # Restrict dmesg access
   kernel.dmesg_restrict = 1

   # Disable kernel module loading (optional, high security)
   # kernel.modules_disabled = 1

   # Performance (Urbit-specific)

   # Increase file descriptor limit
   fs.file-max = 65536

   # Increase network buffers (helps with Ames)
   net.core.rmem_max = 16777216
   net.core.wmem_max = 16777216
   net.ipv4.tcp_rmem = 4096 87380 16777216
   net.ipv4.tcp_wmem = 4096 65536 16777216
   ```

2. Apply sysctl configuration:
   ```bash
   # Test configuration (dry-run)
   sudo sysctl -p /etc/sysctl.d/99-urbit-security.conf

   # If no errors, apply permanently
   sudo sysctl --system
   ```

3. Verify settings:
   ```bash
   # Check specific values
   sysctl net.ipv4.ip_forward
   sysctl net.ipv4.conf.all.rp_filter
   sysctl kernel.kptr_restrict
   ```

**Success Criteria**:
- [ ] Sysctl security configuration created
- [ ] Settings applied without errors
- [ ] IP forwarding disabled
- [ ] Reverse path filtering enabled
- [ ] ICMP redirects ignored
- [ ] SYN flood protection enabled
- [ ] Ship networking still functional

---

### Phase 11: AppArmor Enforcement

**Objective**: Enable Mandatory Access Control (MAC) for additional security layer

**Tasks**:
1. Check AppArmor status:
   ```bash
   sudo aa-status
   ```

2. Ensure AppArmor is enabled:
   ```bash
   # Check if running
   sudo systemctl status apparmor

   # Enable if not running
   sudo systemctl enable apparmor
   sudo systemctl start apparmor
   ```

3. Verify AppArmor profiles:
   ```bash
   # List loaded profiles
   sudo aa-status

   # Should show profiles in enforce mode
   # Common profiles: /usr/sbin/tcpdump, /usr/bin/man, etc.
   ```

4. Create custom AppArmor profile for Urbit (optional, advanced):
   ```bash
   # Generate profile template
   sudo aa-genprof /usr/local/bin/urbit

   # This is ADVANCED - requires testing Urbit operations
   # to build complete profile. Skip for initial hardening.
   ```

**Success Criteria**:
- [ ] AppArmor installed and running
- [ ] System profiles in enforce mode
- [ ] No impact on Urbit ship functionality

---

### Phase 12: Logging and Auditing

**Objective**: Configure comprehensive logging for security monitoring and incident response

**Tasks**:
1. Install auditd (Linux audit daemon):
   ```bash
   sudo apt install auditd audispd-plugins -y
   ```

2. Configure audit rules:
   ```bash
   sudo nano /etc/audit/rules.d/urbit-security.rules
   ```

   **Audit Rules**:
   ```
   # Audit all authentication attempts
   -w /var/log/auth.log -p wa -k auth_log

   # Audit SSH configuration changes
   -w /etc/ssh/sshd_config -p wa -k sshd_config_changes

   # Audit user creation/modification
   -w /etc/passwd -p wa -k user_modification
   -w /etc/group -p wa -k group_modification
   -w /etc/shadow -p wa -k shadow_modification

   # Audit sudo usage
   -w /etc/sudoers -p wa -k sudoers_changes
   -w /var/log/sudo.log -p wa -k sudo_log

   # Audit systemd service changes
   -w /etc/systemd/system/ -p wa -k systemd_changes

   # Audit Urbit pier access (adjust path)
   -w /home/urbit/sampel-palnet/ -p wa -k urbit_pier_access

   # Audit kernel module loading
   -w /sbin/insmod -p x -k kernel_module
   -w /sbin/rmmod -p x -k kernel_module
   -w /sbin/modprobe -p x -k kernel_module
   ```

3. Reload audit rules:
   ```bash
   sudo augenrules --load

   # Restart auditd
   sudo systemctl restart auditd

   # Verify rules loaded
   sudo auditctl -l
   ```

4. Configure rsyslog for centralized logging (optional):
   ```bash
   sudo nano /etc/rsyslog.d/50-urbit.conf
   ```

   **Rsyslog Configuration**:
   ```
   # Urbit ship logs
   :programname, isequal, "urbit-sampel-palnet" /var/log/urbit/sampel-palnet.log

   # SSH logs
   :programname, isequal, "sshd" /var/log/ssh/sshd.log

   # Fail2ban logs
   :programname, isequal, "fail2ban" /var/log/fail2ban/fail2ban.log
   ```

   Restart rsyslog:
   ```bash
   sudo mkdir -p /var/log/urbit /var/log/ssh /var/log/fail2ban
   sudo systemctl restart rsyslog
   ```

5. Set up log rotation:
   ```bash
   sudo nano /etc/logrotate.d/urbit
   ```

   **Logrotate Configuration**:
   ```
   /var/log/urbit/*.log {
       daily
       rotate 30
       compress
       delaycompress
       missingok
       notifempty
       create 0640 urbit urbit
   }

   /var/log/ssh/*.log {
       weekly
       rotate 12
       compress
       delaycompress
       missingok
       notifempty
   }
   ```

**Success Criteria**:
- [ ] Auditd installed and running
- [ ] Security audit rules loaded
- [ ] Rsyslog configured for centralized logs
- [ ] Log rotation configured (30-day retention)
- [ ] Logs writing to /var/log/urbit/

---

### Phase 13: SSL/TLS Hardening

**Objective**: Configure strong SSL/TLS for HTTPS access

**Tasks**:
1. Install Certbot and Nginx:
   ```bash
   sudo apt install nginx certbot python3-certbot-nginx -y
   ```

2. Configure Nginx reverse proxy:
   ```bash
   sudo nano /etc/nginx/sites-available/sampel-palnet
   ```

   **Nginx Configuration** (hardened):
   ```nginx
   # Rate limiting zone
   limit_req_zone $binary_remote_addr zone=urbit_limit:10m rate=10r/s;

   server {
       listen 80;
       server_name sampel-palnet.arvo.network;  # Or custom domain

       # Redirect HTTP to HTTPS
       return 301 https://$server_name$request_uri;
   }

   server {
       listen 443 ssl http2;
       server_name sampel-palnet.arvo.network;

       # SSL Configuration (will be managed by Certbot)
       ssl_certificate /etc/letsencrypt/live/sampel-palnet.arvo.network/fullchain.pem;
       ssl_certificate_key /etc/letsencrypt/live/sampel-palnet.arvo.network/privkey.pem;

       # SSL Hardening (2025 Best Practices)
       ssl_protocols TLSv1.2 TLSv1.3;
       ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384';
       ssl_prefer_server_ciphers on;

       # HSTS (HTTP Strict Transport Security)
       add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

       # Additional Security Headers
       add_header X-Frame-Options "SAMEORIGIN" always;
       add_header X-Content-Type-Options "nosniff" always;
       add_header X-XSS-Protection "1; mode=block" always;
       add_header Referrer-Policy "no-referrer-when-downgrade" always;

       # Rate limiting
       limit_req zone=urbit_limit burst=20 nodelay;

       # Proxy to Urbit ship
       location / {
           proxy_pass http://localhost:8080;  # Adjust port if needed
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;

           # WebSocket support
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection "upgrade";

           # Timeouts
           proxy_connect_timeout 60s;
           proxy_send_timeout 60s;
           proxy_read_timeout 60s;
       }

       # Logging
       access_log /var/log/nginx/sampel-palnet.access.log;
       error_log /var/log/nginx/sampel-palnet.error.log;
   }
   ```

3. Enable site and obtain SSL certificate:
   ```bash
   # Enable site
   sudo ln -s /etc/nginx/sites-available/sampel-palnet /etc/nginx/sites-enabled/

   # Test nginx configuration
   sudo nginx -t

   # Restart nginx
   sudo systemctl restart nginx

   # Obtain Let's Encrypt certificate
   sudo certbot --nginx -d sampel-palnet.arvo.network

   # Follow prompts:
   # - Enter email for renewal notifications
   # - Agree to Terms of Service
   # - Choose to redirect HTTP to HTTPS (option 2)
   ```

4. Set up automatic certificate renewal:
   ```bash
   # Test renewal
   sudo certbot renew --dry-run

   # Certbot auto-installs renewal cron job
   # Verify:
   sudo systemctl status certbot.timer
   ```

5. Test SSL configuration:
   ```bash
   # Test SSL grade at SSL Labs
   # Visit: https://www.ssllabs.com/ssltest/analyze.html?d=sampel-palnet.arvo.network
   # Target: A+ rating

   # Test locally
   curl -I https://sampel-palnet.arvo.network
   ```

**Success Criteria**:
- [ ] Nginx installed and configured as reverse proxy
- [ ] SSL certificate obtained from Let's Encrypt
- [ ] HTTPS enforced (HTTP redirects to HTTPS)
- [ ] HSTS header set (31536000 seconds)
- [ ] Security headers configured
- [ ] SSL Labs grade: A or A+
- [ ] Auto-renewal working (certbot.timer active)
- [ ] Rate limiting configured (10 req/s)

---

### Phase 14: Urbit-Specific Security

**Objective**: Implement Urbit-specific security configurations

**Tasks**:
1. Secure pier permissions:
   ```bash
   # Set pier ownership
   sudo chown -R urbit:urbit /home/urbit/sampel-palnet

   # Set restrictive permissions
   chmod 700 /home/urbit/sampel-palnet

   # Verify
   ls -la /home/urbit/
   # Should show: drwx------ urbit urbit sampel-palnet
   ```

2. Keyfile security audit:
   ```bash
   # CRITICAL: Verify keyfile was deleted after first boot
   find /home/urbit -name "*.key" -type f

   # Should return NOTHING
   # If keyfile found, DELETE immediately:
   # sudo shred -u /path/to/keyfile.key
   ```

3. Dojo access control:
   ```bash
   # Dojo runs in urbit user context (already secured by systemd)
   # Access only via:
   # 1. SSH as urbit user + attach to screen/tmux
   # 2. systemd service (no interactive dojo by default)

   # For production, disable interactive dojo (optional)
   # Ship managed entirely via systemd, accessed only via web
   ```

4. Verify ship configuration:
   ```bash
   # Check ship is not running with elevated privileges
   ps aux | grep urbit
   # User column should show "urbit", NOT "root"

   # Check listening ports
   sudo netstat -tulpn | grep urbit
   # Should show localhost:8080 (HTTP) - not exposed directly
   # Ames networking handled by kernel, not bound port
   ```

5. Configure ship HTTP settings (in dojo):
   ```
   # In dojo (access via urbit user):
   su - urbit
   # Attach to ship if running in tmux/screen

   # Generate web login code
   +code

   # Configure domain (if using custom domain)
   :acme|set-domain 'sampel-palnet.example.com'

   # Disable HTTP access entirely (optional, HTTPS-only)
   # :acme|listen 0
   # WARNING: Only do this AFTER confirming HTTPS working
   ```

**Success Criteria**:
- [ ] Pier owned by urbit:urbit, permissions 700
- [ ] No keyfiles found in system
- [ ] Ship running as urbit user (not root)
- [ ] Dojo access restricted to urbit user
- [ ] Ship accessible via HTTPS (not direct HTTP)
- [ ] Web login code functional (+code)

---

### Phase 15: Backup Configuration

**Objective**: Implement automated, encrypted backups with offsite storage

**Tasks**:
1. Create backup directory structure:
   ```bash
   sudo mkdir -p /backups/urbit
   sudo chown urbit:urbit /backups/urbit
   chmod 750 /backups/urbit
   ```

2. Create backup script:
   ```bash
   sudo nano /usr/local/bin/backup-urbit.sh
   ```

   **Backup Script** (safe, encrypted):
   ```bash
   #!/bin/bash
   # Safe Urbit backup script
   # CRITICAL: Stops ship before backup to prevent corruption

   set -euo pipefail  # Exit on error, undefined var, pipe failure

   # Configuration
   SHIP_NAME="sampel-palnet"
   PIER_PATH="/home/urbit/$SHIP_NAME"
   BACKUP_DIR="/backups/urbit"
   DATE=$(date +%Y%m%d-%H%M%S)
   BACKUP_FILE="$BACKUP_DIR/$SHIP_NAME-$DATE.tar.gz"
   RETENTION=7  # Keep last 7 backups

   # Logging
   LOG_FILE="/var/log/urbit-backup.log"
   exec > >(tee -a "$LOG_FILE")
   exec 2>&1

   echo "=== Urbit Backup Started: $(date) ==="

   # Step 1: Stop ship gracefully
   echo "Stopping ship..."
   systemctl stop urbit-$SHIP_NAME

   # Step 2: Wait for process to fully stop
   echo "Waiting for ship to stop completely..."
   sleep 30

   # Step 3: Verify ship stopped
   if pgrep -f "urbit.*$SHIP_NAME" > /dev/null; then
       echo "ERROR: Ship still running! Aborting backup."
       systemctl start urbit-$SHIP_NAME
       exit 1
   fi

   # Step 4: Backup pier
   echo "Creating backup..."
   tar czf "$BACKUP_FILE" -C /home/urbit "$SHIP_NAME"

   # Step 5: Verify backup integrity
   echo "Verifying backup..."
   if ! tar tzf "$BACKUP_FILE" > /dev/null; then
       echo "ERROR: Backup verification failed!"
       rm -f "$BACKUP_FILE"
       systemctl start urbit-$SHIP_NAME
       exit 1
   fi

   # Step 6: Restart ship
   echo "Restarting ship..."
   systemctl start urbit-$SHIP_NAME

   # Step 7: Wait for ship to start
   sleep 10

   # Step 8: Verify ship started successfully
   if ! systemctl is-active --quiet urbit-$SHIP_NAME; then
       echo "ERROR: Ship failed to restart!"
       exit 1
   fi

   # Step 9: Encrypt backup (optional, requires gpg)
   # echo "Encrypting backup..."
   # gpg --symmetric --cipher-algo AES256 "$BACKUP_FILE"
   # rm -f "$BACKUP_FILE"  # Remove unencrypted backup
   # BACKUP_FILE="$BACKUP_FILE.gpg"

   # Step 10: Cleanup old backups
   echo "Cleaning up old backups (keeping last $RETENTION)..."
   ls -t $BACKUP_DIR/$SHIP_NAME-*.tar.gz | tail -n +$((RETENTION + 1)) | xargs -r rm -f

   # Step 11: Report backup size
   BACKUP_SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
   echo "Backup completed: $BACKUP_FILE ($BACKUP_SIZE)"
   echo "=== Backup Finished: $(date) ==="
   ```

3. Make script executable:
   ```bash
   sudo chmod +x /usr/local/bin/backup-urbit.sh
   ```

4. Test backup script:
   ```bash
   # Test run
   sudo /usr/local/bin/backup-urbit.sh

   # Verify backup created
   ls -lh /backups/urbit/

   # Verify ship restarted
   sudo systemctl status urbit-sampel-palnet
   ```

5. Schedule automated backups:
   ```bash
   sudo crontab -e
   ```

   **Cron Schedule** (weekly backups on Sunday 3 AM):
   ```
   # Urbit backup - Every Sunday at 3:00 AM
   0 3 * * 0 /usr/local/bin/backup-urbit.sh

   # Or daily backups at 2:00 AM:
   # 0 2 * * * /usr/local/bin/backup-urbit.sh
   ```

6. Set up offsite backup (optional, recommended for production):
   ```bash
   # Option 1: S3 sync
   # Install AWS CLI: sudo apt install awscli
   # Add to backup script:
   # aws s3 cp "$BACKUP_FILE" s3://my-urbit-backups/

   # Option 2: rsync to remote server
   # Add to backup script:
   # rsync -avz "$BACKUP_FILE" backup-server:/backups/urbit/

   # Option 3: Backblaze B2 (cost-effective)
   # Install b2 CLI: sudo pip3 install b2
   # Add to backup script:
   # b2 upload-file my-bucket "$BACKUP_FILE" "$SHIP_NAME-$DATE.tar.gz"
   ```

**Success Criteria**:
- [ ] Backup directory created (/backups/urbit)
- [ ] Backup script created and executable
- [ ] Test backup successful (ship stops → backup → restarts)
- [ ] Backup integrity verified (tar tzf succeeds)
- [ ] Cron job scheduled (weekly or daily)
- [ ] Retention policy enforced (7 backups kept)
- [ ] Offsite backup configured (optional)
- [ ] Backup logs writing to /var/log/urbit-backup.log

---

### Phase 16: Monitoring Implementation

**Objective**: Deploy comprehensive monitoring and alerting

**Tasks**:
1. Install monitoring tools:
   ```bash
   # Install monitoring essentials
   sudo apt install sysstat iotop htop -y
   ```

2. Enable sysstat (system performance monitoring):
   ```bash
   # Enable data collection
   sudo nano /etc/default/sysstat
   # Set: ENABLED="true"

   # Start service
   sudo systemctl enable sysstat
   sudo systemctl start sysstat
   ```

3. Create monitoring script:
   ```bash
   sudo nano /usr/local/bin/urbit-monitor.sh
   ```

   **Monitoring Script**:
   ```bash
   #!/bin/bash
   # Urbit monitoring script

   SHIP_NAME="sampel-palnet"
   LOG_FILE="/var/log/urbit-monitor.log"
   ALERT_EMAIL="admin@example.com"  # Configure email alerts

   # Thresholds
   CPU_THRESHOLD=80
   MEM_THRESHOLD=85
   DISK_THRESHOLD=85

   # Get metrics
   CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1 | cut -d'.' -f1)
   MEM_USAGE=$(free | grep Mem | awk '{printf("%.0f", $3/$2 * 100)}')
   DISK_USAGE=$(df -h /home/urbit | tail -1 | awk '{print $5}' | cut -d'%' -f1)

   # Check ship is running
   if ! systemctl is-active --quiet urbit-$SHIP_NAME; then
       echo "ALERT: Ship $SHIP_NAME is DOWN at $(date)" | tee -a $LOG_FILE
       # Send alert (requires mail configured)
       # echo "Ship $SHIP_NAME is DOWN" | mail -s "URGENT: Urbit Ship Down" $ALERT_EMAIL
   fi

   # Check CPU
   if [ "$CPU_USAGE" -gt "$CPU_THRESHOLD" ]; then
       echo "ALERT: High CPU usage: $CPU_USAGE% at $(date)" | tee -a $LOG_FILE
   fi

   # Check memory
   if [ "$MEM_USAGE" -gt "$MEM_THRESHOLD" ]; then
       echo "ALERT: High memory usage: $MEM_USAGE% at $(date)" | tee -a $LOG_FILE
   fi

   # Check disk
   if [ "$DISK_USAGE" -gt "$DISK_THRESHOLD" ]; then
       echo "ALERT: High disk usage: $DISK_USAGE% at $(date)" | tee -a $LOG_FILE
   fi

   # Log metrics
   echo "$(date +%Y-%m-%d\ %H:%M:%S) CPU:$CPU_USAGE% MEM:$MEM_USAGE% DISK:$DISK_USAGE%" >> $LOG_FILE
   ```

4. Make monitoring script executable and schedule:
   ```bash
   sudo chmod +x /usr/local/bin/urbit-monitor.sh

   # Add to crontab (run every 5 minutes)
   sudo crontab -e
   # Add line:
   */5 * * * * /usr/local/bin/urbit-monitor.sh
   ```

5. Set up log monitoring with logwatch (optional):
   ```bash
   sudo apt install logwatch -y

   # Configure logwatch
   sudo nano /etc/logwatch/conf/logwatch.conf
   # Set: MailTo = admin@example.com
   # Set: Detail = High

   # Logwatch runs daily via cron
   ```

6. Install and configure monitoring dashboard (optional, advanced):
   ```bash
   # For advanced monitoring, consider:
   # - Prometheus + Grafana (comprehensive metrics)
   # - Netdata (real-time monitoring, easy setup)

   # Quick setup with Netdata:
   bash <(curl -Ss https://get.netdata.cloud/kickstart.sh)
   # Access at: http://server-ip:19999
   ```

**Success Criteria**:
- [ ] Sysstat installed and collecting metrics
- [ ] Monitoring script created and scheduled (every 5 minutes)
- [ ] Alerts configured (CPU >80%, MEM >85%, DISK >85%)
- [ ] Ship status checked in monitoring
- [ ] Monitoring logs writing to /var/log/urbit-monitor.log
- [ ] Optional: Dashboard accessible (Netdata or Grafana)

---

### Phase 17: Incident Response Plan

**Objective**: Create documented procedures for incident response

**Tasks**:
1. Create incident response runbook:
   ```bash
   sudo mkdir -p /opt/urbit/docs
   sudo nano /opt/urbit/docs/incident-response.md
   ```

   **Incident Response Runbook** (template):
   ```markdown
   # Urbit Ship Incident Response Plan

   ## Ship Information
   - Ship Name: ~sampel-palnet
   - Pier Path: /home/urbit/sampel-palnet
   - Service: urbit-sampel-palnet.service

   ## Incident Severity Levels

   ### P1 (Critical) - Ship Down
   - Impact: Complete service outage
   - Response Time: Immediate (< 15 minutes)
   - Escalation: Alert on-call admin

   ### P2 (High) - Degraded Performance
   - Impact: Service slow but functional
   - Response Time: < 1 hour
   - Escalation: Email admin

   ### P3 (Medium) - Minor Issues
   - Impact: Non-critical functionality affected
   - Response Time: < 4 hours
   - Escalation: Log ticket

   ## Common Incidents

   ### Incident 1: Ship Won't Start
   **Symptoms**: systemctl status shows failed
   **Resolution**:
   1. Check logs: `journalctl -u urbit-sampel-palnet -n 100`
   2. Check disk space: `df -h /home/urbit`
   3. Check pier permissions: `ls -la /home/urbit/sampel-palnet`
   4. Attempt restart: `systemctl restart urbit-sampel-palnet`
   5. If fails, see troubleshoot-ship command

   ### Incident 2: Out of Memory
   **Symptoms**: "out of loom" errors in logs
   **Resolution**:
   1. Access dojo (as urbit user)
   2. Run |pack (quick fix)
   3. If urgent, run |meld (requires 8GB RAM)
   4. Monitor: `du -sh /home/urbit/sampel-palnet`

   ### Incident 3: SSL Certificate Expired
   **Symptoms**: HTTPS not working, browser warnings
   **Resolution**:
   1. Check cert: `sudo certbot certificates`
   2. Renew: `sudo certbot renew`
   3. Restart nginx: `systemctl restart nginx`

   ### Incident 4: Ames Connectivity Issues
   **Symptoms**: Cannot reach other ships
   **Resolution**:
   1. Check firewall: `sudo ufw status | grep 34543`
   2. Test connectivity in dojo: `|hi ~zod`
   3. Check router port forwarding (UDP 34543)

   ## Contact Information
   - On-Call Admin: +1-555-0100
   - Email: admin@example.com
   - Urbit Community: https://urbit.org/community

   ## Post-Incident Review
   After resolving incident:
   1. Document root cause
   2. Update runbook if needed
   3. Implement preventive measures
   4. Share learnings with team
   ```

2. Create escalation procedures:
   ```bash
   sudo nano /opt/urbit/docs/escalation.md
   ```

   **Escalation Matrix**:
   ```markdown
   # Escalation Procedures

   ## Level 1: Automated Monitoring
   - Monitoring script detects issue
   - Logs to /var/log/urbit-monitor.log
   - Email alert sent (if configured)

   ## Level 2: On-Call Response
   - Admin receives alert
   - Response time: < 15 minutes for P1
   - Follow incident response runbook

   ## Level 3: Community Support
   - If issue unresolved after 1 hour
   - Post to Urbit Community Support
   - Check GitHub issues: github.com/urbit/urbit

   ## Level 4: Professional Support
   - For enterprise deployments
   - Contact Tlon (commercial support)
   - Engage Urbit consultants
   ```

3. Set up emergency contacts:
   ```bash
   sudo nano /opt/urbit/docs/contacts.txt
   ```

   ```
   === Emergency Contacts ===

   Primary Admin: Jane Doe
   Phone: +1-555-0100
   Email: admin@example.com

   Secondary Admin: John Smith
   Phone: +1-555-0101
   Email: john@example.com

   Urbit Community Support:
   - https://urbit.org/community
   - Urbit Operators group: ~bitbet-bolbel/urbit-community

   GitHub Issues:
   - https://github.com/urbit/urbit/issues
   ```

**Success Criteria**:
- [ ] Incident response runbook created
- [ ] Common incidents documented with resolutions
- [ ] Escalation procedures defined
- [ ] Emergency contacts documented
- [ ] Runbooks stored in /opt/urbit/docs/

---

### Phase 18: Security Audit (Final)

**Objective**: Perform comprehensive security validation

**Tasks**:
1. Run Lynis security audit (final):
   ```bash
   sudo lynis audit system --quick

   # Compare to baseline (Phase 1)
   diff /var/log/lynis-report-BASELINE.dat /var/log/lynis.log
   ```

2. Review security score improvement:
   ```bash
   # Extract hardening index
   grep "Hardening index" /var/log/lynis.log

   # Target: 75+ (out of 100)
   # Before hardening: typically 50-60
   # After hardening: typically 75-85
   ```

3. systemd service security check:
   ```bash
   systemd-analyze security urbit-sampel-palnet

   # Target exposure: MEDIUM or better
   # Security score: ≤ 2.5 (lower is better)
   ```

4. SSL/TLS validation:
   ```bash
   # Test SSL configuration
   # Visit: https://www.ssllabs.com/ssltest/
   # Enter: sampel-palnet.arvo.network
   # Target: A or A+ rating
   ```

5. Firewall validation:
   ```bash
   sudo ufw status verbose

   # Verify only essential ports open:
   # - 22/tcp (or custom SSH port)
   # - 80/tcp
   # - 443/tcp
   # - 34543/udp
   ```

6. Penetration testing (optional, advanced):
   ```bash
   # Install security testing tools
   sudo apt install nmap nikto -y

   # Port scan from external network
   # nmap -sS -sV -p- external-ip
   # Should only show open: 22, 80, 443

   # Web vulnerability scan
   # nikto -h https://sampel-palnet.arvo.network
   ```

**Success Criteria**:
- [ ] Lynis hardening index: 75+
- [ ] systemd security score: ≤ 2.5
- [ ] SSL Labs rating: A or A+
- [ ] Firewall: Only 4 ports open
- [ ] No critical vulnerabilities found
- [ ] Security improvements documented

---

### Phase 19: Documentation and Runbook Creation

**Objective**: Create comprehensive operational documentation

**Tasks**:
1. Create master operational runbook:
   ```bash
   sudo nano /opt/urbit/docs/operations-runbook.md
   ```

   **Operations Runbook** (comprehensive):
   ```markdown
   # Urbit Ship Operations Runbook
   ## Ship: ~sampel-palnet

   ## System Information
   - OS: Ubuntu 22.04 LTS
   - Urbit Version: [check with: urbit --version]
   - Deployment Type: Bare-metal production
   - Pier Location: /home/urbit/sampel-palnet
   - Service: urbit-sampel-palnet.service

   ## Daily Operations

   ### Starting Ship
   ```
   sudo systemctl start urbit-sampel-palnet
   ```

   ### Stopping Ship (Graceful)
   ```
   sudo systemctl stop urbit-sampel-palnet
   # Wait 30 seconds before any filesystem operations
   ```

   ### Checking Status
   ```
   sudo systemctl status urbit-sampel-palnet
   journalctl -u urbit-sampel-palnet -n 50 -f
   ```

   ### Accessing Dojo
   ```
   # SSH as urbit user
   su - urbit

   # If ship running in tmux/screen, attach
   # Otherwise, access via web interface
   ```

   ### Getting Web Login Code
   ```
   # In dojo:
   +code
   # Returns: ~sampel-palnet-sampel-palnet (example)
   ```

   ## Weekly Maintenance

   ### Check Logs
   ```
   journalctl -u urbit-sampel-palnet --since "7 days ago" | grep -i error
   ```

   ### Check Disk Space
   ```
   df -h /home/urbit
   du -sh /home/urbit/sampel-palnet
   ```

   ### Verify Backups
   ```
   ls -lh /backups/urbit/
   # Should see weekly backups (7 retained)
   ```

   ### Check Security Updates
   ```
   sudo apt update
   sudo apt list --upgradable | grep security
   ```

   ## Monthly Maintenance

   ### Review Security Advisories
   - Check Urbit security updates: https://github.com/urbit/urbit/security
   - Ubuntu security notices: https://ubuntu.com/security/notices

   ### Update Urbit Binary (if needed)
   ```
   curl -L https://urbit.org/install/linux-x86_64/latest | tar xzk --transform='s/.*/urbit/g'
   sudo mv urbit /usr/local/bin/
   urbit --version
   ```

   ### Test Backup Restoration
   ```
   # Extract latest backup to test location
   tar tzf /backups/urbit/sampel-palnet-LATEST.tar.gz
   # Verify integrity (should complete without errors)
   ```

   ### Run Security Audit
   ```
   sudo lynis audit system --quick
   grep "Hardening index" /var/log/lynis.log
   ```

   ## Quarterly Maintenance

   ### Full Security Review
   - Run Lynis audit
   - Review fail2ban logs: `sudo fail2ban-client status sshd`
   - Review audit logs: `sudo ausearch -k auth_log`

   ### Performance Review
   - Check pier size growth trend
   - Review CPU/memory usage trends
   - Optimize if needed (|pack or |meld)

   ### Documentation Updates
   - Update runbooks with lessons learned
   - Review incident response procedures
   - Update emergency contacts

   ### Disaster Recovery Test
   - Perform full backup restoration test on separate system
   - Verify ship boots from backup
   - Document recovery time objective (RTO)

   ## Emergency Procedures

   See: /opt/urbit/docs/incident-response.md

   ## Contacts

   See: /opt/urbit/docs/contacts.txt
   ```

2. Create maintenance schedule:
   ```bash
   sudo nano /opt/urbit/docs/maintenance-schedule.md
   ```

   **Maintenance Schedule**:
   ```markdown
   # Urbit Ship Maintenance Schedule

   ## Automated Tasks
   - **Backups**: Weekly (Sunday 3:00 AM) - Cron
   - **Monitoring**: Every 5 minutes - Cron
   - **Security Updates**: Daily - unattended-upgrades
   - **Log Rotation**: Daily - logrotate

   ## Manual Tasks

   ### Daily
   - [ ] Check monitoring alerts (email)
   - [ ] Verify ship is running: `systemctl status urbit-sampel-palnet`

   ### Weekly
   - [ ] Review logs for errors
   - [ ] Check disk space
   - [ ] Verify backups completed successfully
   - [ ] Check fail2ban status (banned IPs)

   ### Monthly
   - [ ] Review Urbit security updates
   - [ ] Test backup integrity
   - [ ] Update Urbit binary (if new version)
   - [ ] Run Lynis security audit
   - [ ] Review SSL certificate status

   ### Quarterly
   - [ ] Full security audit
   - [ ] Performance review
   - [ ] Disaster recovery test
   - [ ] Documentation review
   - [ ] Team training/knowledge transfer

   ### Annually
   - [ ] Comprehensive security assessment
   - [ ] Infrastructure capacity planning
   - [ ] Vendor/dependency review
   - [ ] Compliance audit (if applicable)
   ```

3. Create configuration inventory:
   ```bash
   sudo nano /opt/urbit/docs/configuration-inventory.md
   ```

   **Configuration Inventory**:
   ```markdown
   # Configuration Inventory

   ## System Configuration
   - OS: Ubuntu 22.04 LTS
   - Hostname: urbit-prod-01
   - IP Address: [server IP]
   - DNS: sampel-palnet.arvo.network

   ## Security Configuration
   - SSH Port: 22 (or custom)
   - Fail2ban: Enabled (bantime=3600, maxretry=5)
   - UFW: Enabled (default deny, allow 22,80,443,34543/udp)
   - AppArmor: Enabled (enforce mode)
   - Automatic Updates: Enabled

   ## Urbit Configuration
   - Ship: ~sampel-palnet
   - Pier: /home/urbit/sampel-palnet
   - Service: urbit-sampel-palnet.service
   - User: urbit
   - HTTP Port: 8080 (localhost only)
   - Ames Port: 34543/udp

   ## SSL/TLS Configuration
   - Certificate Provider: Let's Encrypt
   - Domain: sampel-palnet.arvo.network
   - Renewal: Automatic (certbot.timer)
   - Protocols: TLSv1.2, TLSv1.3
   - SSL Grade: A+ (SSL Labs)

   ## Monitoring Configuration
   - Monitoring Script: /usr/local/bin/urbit-monitor.sh
   - Schedule: Every 5 minutes
   - Alerts: Email to admin@example.com
   - Thresholds: CPU 80%, MEM 85%, DISK 85%

   ## Backup Configuration
   - Backup Script: /usr/local/bin/backup-urbit.sh
   - Schedule: Weekly (Sunday 3:00 AM)
   - Retention: 7 backups
   - Location: /backups/urbit
   - Encryption: No (optional)
   - Offsite: No (recommended for production)

   ## File Locations
   - Urbit binary: /usr/local/bin/urbit
   - Pier: /home/urbit/sampel-palnet
   - Backups: /backups/urbit
   - Logs: /var/log/urbit/, journalctl
   - Documentation: /opt/urbit/docs
   - Scripts: /usr/local/bin/
   ```

**Success Criteria**:
- [ ] Operations runbook created
- [ ] Maintenance schedule documented
- [ ] Configuration inventory complete
- [ ] All documentation in /opt/urbit/docs/
- [ ] Runbooks accessible to all operators

---

### Phase 20: Production Validation and Handoff

**Objective**: Final validation and operational handoff

**Tasks**:
1. Comprehensive system validation:
   ```bash
   # Create validation script
   sudo nano /opt/urbit/scripts/production-validation.sh
   ```

   **Validation Script**:
   ```bash
   #!/bin/bash
   # Production validation checklist

   echo "=== Urbit Production Validation ==="
   echo

   # Check 1: Ship is running
   echo -n "Ship running: "
   if systemctl is-active --quiet urbit-sampel-palnet; then
       echo "✓ PASS"
   else
       echo "✗ FAIL"
   fi

   # Check 2: SSH hardened
   echo -n "SSH password auth disabled: "
   if grep -q "^PasswordAuthentication no" /etc/ssh/sshd_config; then
       echo "✓ PASS"
   else
       echo "✗ FAIL"
   fi

   # Check 3: Firewall enabled
   echo -n "Firewall enabled: "
   if sudo ufw status | grep -q "Status: active"; then
       echo "✓ PASS"
   else
       echo "✗ FAIL"
   fi

   # Check 4: Fail2ban running
   echo -n "Fail2ban running: "
   if systemctl is-active --quiet fail2ban; then
       echo "✓ PASS"
   else
       echo "✗ FAIL"
   fi

   # Check 5: SSL certificate valid
   echo -n "SSL certificate: "
   if sudo certbot certificates 2>/dev/null | grep -q "VALID"; then
       echo "✓ PASS"
   else
       echo "✗ FAIL or N/A"
   fi

   # Check 6: Backups scheduled
   echo -n "Backup cron job: "
   if sudo crontab -l | grep -q "backup-urbit.sh"; then
       echo "✓ PASS"
   else
       echo "✗ FAIL"
   fi

   # Check 7: Monitoring active
   echo -n "Monitoring script: "
   if sudo crontab -l | grep -q "urbit-monitor.sh"; then
       echo "✓ PASS"
   else
       echo "✗ FAIL"
   fi

   # Check 8: Systemd security hardening
   echo -n "Systemd hardening: "
   SCORE=$(systemd-analyze security urbit-sampel-palnet 2>/dev/null | grep "Overall exposure level" | awk '{print $NF}')
   if [[ "$SCORE" =~ (OK|MEDIUM) ]]; then
       echo "✓ PASS ($SCORE)"
   else
       echo "✗ FAIL ($SCORE)"
   fi

   # Check 9: Disk space
   echo -n "Disk space: "
   USAGE=$(df -h /home/urbit | tail -1 | awk '{print $5}' | cut -d'%' -f1)
   if [ "$USAGE" -lt 85 ]; then
       echo "✓ PASS ($USAGE% used)"
   else
       echo "✗ FAIL ($USAGE% used)"
   fi

   # Check 10: Documentation exists
   echo -n "Documentation: "
   if [ -f "/opt/urbit/docs/operations-runbook.md" ]; then
       echo "✓ PASS"
   else
       echo "✗ FAIL"
   fi

   echo
   echo "=== Validation Complete ==="
   ```

2. Run production validation:
   ```bash
   chmod +x /opt/urbit/scripts/production-validation.sh
   /opt/urbit/scripts/production-validation.sh

   # All checks should show ✓ PASS
   ```

3. Create handoff checklist:
   ```bash
   sudo nano /opt/urbit/docs/handoff-checklist.md
   ```

   **Handoff Checklist**:
   ```markdown
   # Production Handoff Checklist

   ## Pre-Handoff Validation
   - [ ] All 20 hardening phases completed
   - [ ] Production validation script passes all checks
   - [ ] Lynis hardening index ≥ 75
   - [ ] SSL Labs rating: A or A+
   - [ ] Ship accessible via HTTPS
   - [ ] Backups tested and verified

   ## Documentation Handoff
   - [ ] Operations runbook reviewed
   - [ ] Maintenance schedule understood
   - [ ] Incident response plan reviewed
   - [ ] Emergency contacts updated
   - [ ] Configuration inventory accurate

   ## Access Handoff
   - [ ] SSH keys provided to operators
   - [ ] Dojo access documented
   - [ ] Web login code provided
   - [ ] Monitoring alerts configured
   - [ ] Backup access verified

   ## Training Completed
   - [ ] Daily operations demonstrated
   - [ ] Ship start/stop procedures reviewed
   - [ ] Backup/restore process demonstrated
   - [ ] Incident response walkthrough completed
   - [ ] Monitoring dashboard reviewed

   ## Production Go-Live
   - [ ] Final security audit completed
   - [ ] All stakeholders notified
   - [ ] Monitoring actively watching
   - [ ] On-call rotation established
   - [ ] Handoff complete

   Signed: ________________  Date: ________
   ```

4. Final security audit report:
   ```bash
   sudo lynis audit system --quick

   # Generate summary report
   echo "=== Final Security Audit ===" > /opt/urbit/docs/final-security-report.txt
   grep "Hardening index" /var/log/lynis.log >> /opt/urbit/docs/final-security-report.txt
   echo >> /opt/urbit/docs/final-security-report.txt
   systemd-analyze security urbit-sampel-palnet >> /opt/urbit/docs/final-security-report.txt
   ```

**Success Criteria**:
- [ ] Production validation: 10/10 checks pass
- [ ] Handoff checklist: 100% complete
- [ ] Final security audit: Hardening index ≥ 75
- [ ] Documentation: Complete and reviewed
- [ ] Training: All operators trained
- [ ] Production handoff: Signed and dated

---

## Complete Success Criteria (All Phases)

After completing all 20 phases, verify:

### Security
- [ ] SSH: Key-only auth, root disabled, Fail2ban active
- [ ] Firewall: Default deny, 4 ports open (22, 80, 443, 34543/udp)
- [ ] SSL/TLS: A+ rating, HSTS enabled, auto-renewal working
- [ ] Systemd: Security score ≤ 2.5, hardening options enabled
- [ ] Lynis: Hardening index ≥ 75

### Operations
- [ ] Ship: Running as urbit user, systemd-managed, auto-restart
- [ ] Backups: Weekly automated, tested, 7 retained
- [ ] Monitoring: Every 5 minutes, alerts configured
- [ ] Logging: Centralized, rotated, 30-day retention
- [ ] Updates: Automatic security updates enabled

### Documentation
- [ ] Operations runbook: Complete
- [ ] Incident response: Documented
- [ ] Maintenance schedule: Defined
- [ ] Configuration inventory: Accurate
- [ ] Handoff checklist: Signed

### Compliance (if applicable)
- [ ] Audit logs: Enabled (auditd)
- [ ] Access controls: Documented
- [ ] Encryption: At rest (optional), in transit (HTTPS)
- [ ] Incident procedures: Defined
- [ ] Regular audits: Scheduled

## Integration with urbit-deployment-specialist

This command is orchestrated by the urbit-deployment-specialist agent, leveraging:

- Deep security expertise (SSH, firewalls, systemd hardening)
- Production operational discipline (monitoring, backups, documentation)
- Systematic implementation approach (20 phases, no shortcuts)
- Enterprise-grade compliance understanding

## Operational Philosophy

**"Production-ready from day one, security-first always, comprehensive documentation over tribal knowledge."**

Every production deployment must be secure, monitored, documented, and maintainable from the moment it goes live. There are no "temporary" solutions or "we'll harden it later" approaches. Security and operational excellence are built in from the start, never bolted on afterward.
