# Anchor Networking Skill

Self-hosted networking solution for Urbit ships providing StarTram-like functionality with full infrastructure control.

## Overview

Anchor is a self-hosted alternative to StarTram (Tlon's managed networking service). It provides reverse proxy, SSL/TLS termination, and NAT traversal for Urbit ships while maintaining complete infrastructure ownership.

## StarTram vs Anchor Comparison

**StarTram** (Managed):
- Hosted by Tlon
- Registration code entry in GroundSeg UI
- Zero infrastructure management
- Automatic SSL/TLS
- Best for: Simplicity, quick setup

**Anchor** (Self-Hosted):
- Requires separate VPS + domain
- Full infrastructure control
- Manual SSL/TLS setup (Let's Encrypt)
- Best for: Privacy, control, enterprise requirements

## Anchor Architecture

```
User (HTTPS) → Anchor VPS (Nginx + SSL) → Your Ship Server (Docker/GroundSeg)
                     ↓
              Custom Domain (yourdomain.com)
              Let's Encrypt SSL
```

## Anchor Deployment Requirements

### Infrastructure
- **Anchor VPS**: Separate server (can be small: 1GB RAM, 10GB disk)
- **Ship Server**: Your main Urbit server (Docker/GroundSeg)
- **Domain**: Custom domain with DNS control
- **SSL**: Let's Encrypt certificate automation

### Network Configuration
- Anchor VPS: Public IP, ports 80/443 open
- Ship Server: Can be behind NAT (no port forwarding needed)
- Tunnel: Anchor connects to ship server (reverse tunnel or VPN)

## Anchor Installation (Basic Setup)

### Step 1: Anchor VPS Preparation

```bash
# Ubuntu 22.04 VPS
sudo apt update
sudo apt install nginx certbot python3-certbot-nginx -y
```

### Step 2: Domain DNS Configuration

```
# Create A record
anchor.yourdomain.com  A  <anchor-vps-public-ip>

# Or use subdomain per ship
ship1.yourdomain.com  A  <anchor-vps-public-ip>
ship2.yourdomain.com  A  <anchor-vps-public-ip>
```

### Step 3: Nginx Reverse Proxy Configuration

```nginx
# /etc/nginx/sites-available/ship1
server {
    listen 80;
    server_name ship1.yourdomain.com;

    location / {
        proxy_pass http://ship-server-ip:8080;  # Ship HTTP port
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

### Step 4: SSL/TLS with Let's Encrypt

```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/ship1 /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx

# Obtain certificate
sudo certbot --nginx -d ship1.yourdomain.com
# Auto-configures HTTPS redirect
```

### Step 5: Tunnel Configuration (if Ship Behind NAT)

**Option A: SSH Reverse Tunnel**:
```bash
# On ship server, tunnel to Anchor VPS
ssh -R 8080:localhost:8080 anchor-vps-user@anchor-vps-ip -N

# Keep tunnel alive with autossh
sudo apt install autossh -y
autossh -M 0 -R 8080:localhost:8080 anchor-vps-user@anchor-vps-ip
```

**Option B**: WireGuard VPN (preferred for production)
- Ship server connects to Anchor VPS via VPN
- Anchor proxies to ship's VPN IP
- Persistent, encrypted tunnel

## GroundSeg + Anchor Integration

GroundSeg can use Anchor for networking instead of StarTram:

1. **Deploy Anchor VPS**: Separate server with Nginx + SSL
2. **Configure GroundSeg**: Point to Anchor VPS in network settings
3. **DNS**: Ship domains point to Anchor VPS public IP
4. **Tunnel**: GroundSeg ships connect to Anchor via reverse tunnel or VPN

**Benefit**: Self-hosted networking with GroundSeg multi-ship orchestration

## Best Practices

1. **Separate VPS**: Don't run Anchor on same server as ships (isolation)
2. **Automated SSL renewal**: Certbot auto-renews (verify with `certbot renew --dry-run`)
3. **Monitoring**: Monitor Anchor VPS uptime (ships depend on it)
4. **Backup configs**: Backup Nginx configs and SSL certificates
5. **Firewall**: UFW on Anchor VPS (allow 22, 80, 443)

## When to Use Anchor vs StarTram

**Use StarTram if:**
- Want zero infrastructure management
- Quick setup priority
- Trust Tlon's managed service

**Use Anchor if:**
- Need full infrastructure control
- Privacy requirements (no third-party)
- Enterprise compliance (data sovereignty)
- Already have domain/DNS infrastructure
- Want to learn self-hosted networking

## Reference

- GroundSeg documentation: https://manual.groundseg.app
- Nginx reverse proxy: https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/
- Let's Encrypt: https://letsencrypt.org

## Summary

Anchor provides self-hosted networking for Urbit ships as an alternative to StarTram. Requires separate VPS, custom domain, Nginx reverse proxy, and Let's Encrypt SSL. Best for users needing infrastructure control, privacy, or enterprise requirements. Can integrate with GroundSeg for multi-ship orchestration with self-hosted networking.
