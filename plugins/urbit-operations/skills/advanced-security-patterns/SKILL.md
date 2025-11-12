---
name: advanced-security-patterns
description: Master advanced security patterns for multi-tenant Urbit deployments including defense-in-depth, encryption at rest/transit, network segmentation, DDoS protection, and zero-trust architectures.
---

# Advanced Security Patterns for Urbit Deployments

## Overview

This skill provides comprehensive security patterns and implementations for securing Urbit fleets in production environments, especially multi-tenant deployments hosting 100-1,000+ ships.

**Use this skill when:**
- Deploying multi-tenant Urbit infrastructure (hosting multiple customers)
- Implementing defense-in-depth security strategies
- Meeting compliance requirements (HIPAA, GDPR, SOC 2, PCI-DSS)
- Protecting against DDoS attacks and network-level threats
- Implementing zero-trust network architectures
- Securing Urbit fleets on Kubernetes, GroundSeg, or VPS platforms
- Conducting security audits or penetration tests

**Key Security Layers:**
1. **Multi-Tenant Isolation:** Prevent cross-tenant access and resource interference
2. **Encryption at Rest:** Protect pier data with LUKS, dm-crypt, or cloud KMS
3. **Encryption in Transit:** TLS 1.2+ for HTTPS, encrypted Ames traffic
4. **Network Segmentation:** Firewalls, VPNs, service mesh, Network Policies
5. **DDoS Protection:** Rate limiting, Ames filtering, CloudFlare/AWS Shield
6. **Access Control:** MFA, RBAC, least privilege, audit logging
7. **Vulnerability Management:** Automated scanning, patching, security updates

---

## Multi-Tenant Isolation Architectures

### Pattern 1: Kubernetes Namespace-Based Isolation

**Use Case:** 100-1,000+ ships on shared Kubernetes cluster, each customer in isolated namespace.

**Architecture:**

```yaml
# Multi-Tenant Architecture

Cluster: urbit-production (AWS EKS)
Total Ships: 500 (50 customers × 10 ships each)

Isolation Strategy:
  - Namespace per customer: customer-{name}
  - NetworkPolicies: Default-deny all traffic, explicit allow rules
  - ResourceQuotas: CPU/memory/storage limits per namespace
  - RBAC: Customer admins can only access their namespace
  - Pod Security Standards: Enforce baseline or restricted profile

Security Controls:
  1. Network isolation (L3/L4)
  2. Resource isolation (CPU/memory quotas)
  3. Access control (RBAC)
  4. Audit logging (Kubernetes audit logs)
  5. Secrets isolation (namespace-scoped secrets)
```

**Implementation:**

**1. Namespace Template:**

```yaml
# namespace-template.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: customer-acme-corp
  labels:
    customer: acme-corp
    isolation-level: multi-tenant
    compliance: hipaa
  annotations:
    security.description: "Multi-tenant isolated namespace for ACME Corp"
---
# Default ResourceQuota
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: customer-acme-corp
spec:
  hard:
    requests.cpu: "10"       # 10 CPU cores
    requests.memory: 20Gi    # 20 GB RAM
    limits.cpu: "20"         # 20 CPU cores (burst)
    limits.memory: 40Gi      # 40 GB RAM (burst)
    persistentvolumeclaims: "10"  # Max 10 PVCs (ships)
    pods: "15"               # Max 15 pods (10 ships + sidecars)
---
# LimitRange (enforce minimum/maximum pod resources)
apiVersion: v1
kind: LimitRange
metadata:
  name: pod-limits
  namespace: customer-acme-corp
spec:
  limits:
    - max:
        cpu: "4"
        memory: 8Gi
      min:
        cpu: "100m"
        memory: 128Mi
      default:
        cpu: "1"
        memory: 2Gi
      defaultRequest:
        cpu: "500m"
        memory: 1Gi
      type: Container
```

**2. Default-Deny NetworkPolicy:**

```yaml
# network-policy-default-deny.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: customer-acme-corp
spec:
  podSelector: {}  # Apply to all pods in namespace
  policyTypes:
    - Ingress
    - Egress
---
# Allow DNS (required for Kubernetes)
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-dns
  namespace: customer-acme-corp
spec:
  podSelector: {}
  policyTypes:
    - Egress
  egress:
    - to:
        - namespaceSelector:
            matchLabels:
              name: kube-system
      ports:
        - protocol: UDP
          port: 53
---
# Allow ingress from load balancer only
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-ingress-from-lb
  namespace: customer-acme-corp
spec:
  podSelector:
    matchLabels:
      app: urbit-ship
  policyTypes:
    - Ingress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: ingress-nginx
      ports:
        - protocol: TCP
          port: 8080  # Urbit HTTP port
---
# Allow Ames (UDP 34343) from internet
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-ames
  namespace: customer-acme-corp
spec:
  podSelector:
    matchLabels:
      app: urbit-ship
  policyTypes:
    - Ingress
  ingress:
    - from:
        - ipBlock:
            cidr: 0.0.0.0/0  # Allow Ames from any IP
      ports:
        - protocol: UDP
          port: 34343
---
# Deny cross-namespace traffic (paranoid mode)
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-cross-namespace
  namespace: customer-acme-corp
spec:
  podSelector: {}
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              customer: acme-corp  # Only from same customer
  egress:
    - to:
        - namespaceSelector:
            matchLabels:
              customer: acme-corp  # Only to same customer
```

**3. RBAC for Customer Admins:**

```yaml
# rbac-customer-admin.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: customer-admin
  namespace: customer-acme-corp
rules:
  # Read-only access to pods and logs
  - apiGroups: [""]
    resources: ["pods", "pods/log"]
    verbs: ["get", "list", "watch"]

  # Access to services and ingresses
  - apiGroups: [""]
    resources: ["services"]
    verbs: ["get", "list"]
  - apiGroups: ["networking.k8s.io"]
    resources: ["ingresses"]
    verbs: ["get", "list"]

  # NO exec access (cannot shell into pods)
  # NO secret access (cannot view encryption keys)
  # NO delete access (cannot delete ships)

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: customer-admin-binding
  namespace: customer-acme-corp
subjects:
  - kind: User
    name: admin@acme-corp.com  # Customer admin user
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: customer-admin
  apiGroup: rbac.authorization.k8s.io
```

**4. Pod Security Standards:**

```yaml
# Enforce Pod Security Standards (PSS) at namespace level

# Option 1: Using Pod Security Admission (Kubernetes 1.23+)
apiVersion: v1
kind: Namespace
metadata:
  name: customer-acme-corp
  labels:
    pod-security.kubernetes.io/enforce: baseline
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted

# Baseline: Prevents known privilege escalations
# Restricted: Heavily restricted (best practice for untrusted workloads)

# Option 2: Using PodSecurityPolicy (deprecated but still in use)
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: urbit-ship-psp
spec:
  privileged: false  # No privileged containers
  allowPrivilegeEscalation: false
  requiredDropCapabilities:
    - ALL
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'persistentVolumeClaim'
  hostNetwork: false
  hostIPC: false
  hostPID: false
  runAsUser:
    rule: 'MustRunAsNonRoot'
  seLinux:
    rule: 'RunAsAny'
  fsGroup:
    rule: 'RunAsAny'
  readOnlyRootFilesystem: false  # Urbit needs writable /urbit
```

**Testing Isolation:**

```bash
# Test 1: Verify NetworkPolicy blocks cross-namespace traffic
kubectl run test-pod -n customer-acme-corp --image=nicolaka/netshoot --rm -it -- \
  curl --max-time 5 http://ship-service.customer-xyz-inc.svc.cluster.local

# Expected: Timeout (NetworkPolicy blocking)

# Test 2: Verify ResourceQuota limits
kubectl create deployment stress-test -n customer-acme-corp \
  --image=polinux/stress --replicas=100 \
  -- stress --cpu 8 --io 4 --vm 2 --vm-bytes 128M

# Expected: Quota exceeded error

# Test 3: Verify RBAC denies cross-namespace access
kubectl auth can-i get pods -n customer-xyz-inc --as=admin@acme-corp.com

# Expected: no

# Test 4: Verify Pod Security Standards
kubectl run privileged-pod -n customer-acme-corp --image=nginx --privileged

# Expected: Error (PSP/PSS denying privileged containers)
```

---

### Pattern 2: Container-Based Isolation (Docker/GroundSeg)

**Use Case:** 10-100 ships on GroundSeg multi-host setup, each ship in isolated container.

**Architecture:**

```yaml
# GroundSeg Multi-Host Architecture

Infrastructure:
  - 5× VPS servers (Hetzner, 16 GB RAM each)
  - Docker + GroundSeg on each host
  - Anchor VPN for inter-ship networking

Isolation Strategy:
  - One container per ship
  - Docker user namespaces (uid/gid remapping)
  - AppArmor or SELinux profiles
  - Resource limits (CPU/memory cgroups)
  - Network isolation (Docker bridge networks)

Security Controls:
  1. Container runtime isolation
  2. User namespace remapping (prevent root breakout)
  3. Mandatory Access Control (AppArmor/SELinux)
  4. Resource limits (cgroups)
  5. Network segmentation (Docker networks)
```

**Implementation:**

**1. Docker User Namespace Remapping:**

```bash
# /etc/docker/daemon.json
{
  "userns-remap": "urbit",
  "storage-driver": "overlay2",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}

# Create urbit user for namespace remapping
sudo useradd -r -s /bin/false urbit

# Configure subuid/subgid mapping
echo "urbit:100000:65536" | sudo tee -a /etc/subuid
echo "urbit:100000:65536" | sudo tee -a /etc/subgid

# Restart Docker
sudo systemctl restart docker

# Verify user namespace active
docker info | grep "userns"
# Output: userns
```

**2. AppArmor Profile for Urbit Containers:**

```bash
# /etc/apparmor.d/docker-urbit
#include <tunables/global>

profile docker-urbit flags=(attach_disconnected,mediate_deleted) {
  #include <abstractions/base>

  # Allow network access
  network inet stream,
  network inet dgram,
  network inet6 stream,
  network inet6 dgram,

  # Allow pier directory access
  /urbit/** rw,

  # Deny access to sensitive host directories
  deny /proc/sys/kernel/core_pattern w,
  deny /sys/kernel/uevent_helper w,
  deny /etc/shadow r,
  deny /etc/passwd w,
  deny /root/** rw,
  deny /home/** rw,

  # Allow common Linux capabilities
  capability net_bind_service,
  capability setuid,
  capability setgid,
  capability dac_override,

  # Deny dangerous capabilities
  deny capability sys_admin,
  deny capability sys_module,
  deny capability sys_rawio,
}
```

```bash
# Load AppArmor profile
sudo apparmor_parser -r /etc/apparmor.d/docker-urbit

# Run container with AppArmor
docker run -d \
  --name sampel-palnet \
  --security-opt apparmor=docker-urbit \
  --cpus="1.0" \
  --memory="2g" \
  --pids-limit=200 \
  -v /urbit/sampel-palnet:/urbit/sampel-palnet \
  urbit/urbit:latest
```

**3. Docker Resource Limits (cgroups):**

```bash
# docker-compose.yml for GroundSeg
version: '3.8'

services:
  sampel-palnet:
    image: urbit/urbit:latest
    container_name: sampel-palnet
    restart: unless-stopped

    # Resource limits
    deploy:
      resources:
        limits:
          cpus: '1.0'      # Max 1 CPU core
          memory: 2G       # Max 2 GB RAM
        reservations:
          cpus: '0.25'     # Reserved 0.25 cores
          memory: 512M     # Reserved 512 MB

    # Process limit (prevent fork bombs)
    pids_limit: 200

    # Security options
    security_opt:
      - no-new-privileges:true
      - apparmor:docker-urbit

    # Read-only root filesystem (except /urbit)
    read_only: false  # Urbit needs writable /tmp

    # Drop all capabilities except necessary ones
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE

    volumes:
      - /urbit/sampel-palnet:/urbit/sampel-palnet:rw

    networks:
      - urbit-network

networks:
  urbit-network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16
```

---

### Pattern 3: VM-Based Isolation (VPS per Customer)

**Use Case:** High-security multi-tenant deployments (healthcare, finance) requiring strict isolation.

**Architecture:**

```yaml
# VM-Based Multi-Tenancy

Infrastructure:
  - 50 customers × 1 dedicated VPS each
  - Hetzner/DigitalOcean/Vultr cloud VPS
  - 1-10 ships per VPS

Isolation Strategy:
  - Complete VM isolation (hypervisor-level)
  - Separate OS per customer
  - Dedicated IP per customer
  - Network-level firewall (iptables, AWS Security Groups)

Security Controls:
  1. Hardware virtualization (KVM/Xen)
  2. VM escape prevention (hypervisor hardening)
  3. Network segmentation (VPC, security groups)
  4. OS-level hardening (minimal attack surface)
  5. Dedicated encryption keys per VM
```

**Implementation:**

**1. Terraform: VPS per Customer:**

```hcl
# terraform/vps-multi-tenant.tf

variable "customers" {
  description = "List of customer names"
  type        = list(string)
  default     = ["acme-corp", "xyz-inc", "example-co"]
}

resource "digitalocean_droplet" "customer_vps" {
  for_each = toset(var.customers)

  name   = "urbit-${each.value}"
  region = "nyc3"
  size   = "s-2vcpu-2gb"  # 2 vCPU, 2 GB RAM
  image  = "ubuntu-22-04-x64"

  # SSH key for access
  ssh_keys = [digitalocean_ssh_key.terraform.id]

  # Firewall (only HTTPS, Ames, SSH)
  vpc_uuid = digitalocean_vpc.main.id

  # User data (install Docker, GroundSeg)
  user_data = templatefile("${path.module}/user-data.sh", {
    customer_name = each.value
  })

  tags = [
    "customer:${each.value}",
    "urbit-fleet",
    "multi-tenant"
  ]
}

# Dedicated firewall per customer
resource "digitalocean_firewall" "customer_firewall" {
  for_each = toset(var.customers)

  name = "urbit-${each.value}-firewall"

  droplet_ids = [digitalocean_droplet.customer_vps[each.value].id]

  # Inbound rules
  inbound_rule {
    protocol         = "tcp"
    port_range       = "22"
    source_addresses = ["YOUR_ADMIN_IP/32"]  # SSH only from admin
  }

  inbound_rule {
    protocol         = "tcp"
    port_range       = "443"
    source_addresses = ["0.0.0.0/0", "::/0"]  # HTTPS from anywhere
  }

  inbound_rule {
    protocol         = "udp"
    port_range       = "34343"
    source_addresses = ["0.0.0.0/0", "::/0"]  # Ames from anywhere
  }

  # Outbound rules (allow all)
  outbound_rule {
    protocol              = "tcp"
    port_range            = "1-65535"
    destination_addresses = ["0.0.0.0/0", "::/0"]
  }

  outbound_rule {
    protocol              = "udp"
    port_range            = "1-65535"
    destination_addresses = ["0.0.0.0/0", "::/0"]
  }
}

output "customer_ips" {
  value = {
    for customer, droplet in digitalocean_droplet.customer_vps :
    customer => droplet.ipv4_address
  }
}
```

---

## Encryption Strategies

### Encryption at Rest: LUKS Pier Encryption

**Use Case:** Protect Urbit pier data from physical disk theft or unauthorized access.

**Implementation:**

```bash
#!/bin/bash
# setup-luks-pier.sh - Create LUKS-encrypted pier volume

SHIP_NAME="sampel-palnet"
PIER_SIZE="50G"
MOUNT_POINT="/urbit/${SHIP_NAME}"

# Generate random encryption key (store securely!)
ENCRYPTION_KEY=$(openssl rand -base64 32)
echo "$ENCRYPTION_KEY" > /root/luks-keys/${SHIP_NAME}.key
chmod 600 /root/luks-keys/${SHIP_NAME}.key

# Create loop device file
dd if=/dev/zero of=/var/lib/urbit-piers/${SHIP_NAME}.img bs=1M count=$((50 * 1024))

# Set up LUKS encryption
echo -n "$ENCRYPTION_KEY" | cryptsetup luksFormat /var/lib/urbit-piers/${SHIP_NAME}.img -

# Open LUKS container
echo -n "$ENCRYPTION_KEY" | cryptsetup luksOpen /var/lib/urbit-piers/${SHIP_NAME}.img ${SHIP_NAME}-crypt -

# Create ext4 filesystem
mkfs.ext4 /dev/mapper/${SHIP_NAME}-crypt

# Mount encrypted volume
mkdir -p "$MOUNT_POINT"
mount /dev/mapper/${SHIP_NAME}-crypt "$MOUNT_POINT"

# Set ownership
chown -R urbit:urbit "$MOUNT_POINT"

echo "✓ LUKS encrypted pier created: $MOUNT_POINT"
echo "Encryption key: /root/luks-keys/${SHIP_NAME}.key (PROTECT THIS!)"
```

**Kubernetes Persistent Volume with LUKS:**

```yaml
# luks-pier-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: sampel-palnet-pier-luks
spec:
  capacity:
    storage: 50Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: luks-encrypted
  local:
    path: /mnt/luks-piers/sampel-palnet
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
                - node-with-luks-setup

---
# Init container to unlock LUKS before ship starts
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sampel-palnet
spec:
  template:
    spec:
      initContainers:
        - name: unlock-luks
          image: ubuntu:22.04
          command:
            - sh
            - -c
            - |
              apt-get update && apt-get install -y cryptsetup
              echo "$LUKS_KEY" | cryptsetup luksOpen /dev/xvdf sampel-palnet-crypt
          env:
            - name: LUKS_KEY
              valueFrom:
                secretKeyRef:
                  name: sampel-palnet-luks-key
                  key: encryption-key
          securityContext:
            privileged: true  # Required for cryptsetup
          volumeMounts:
            - name: host-dev
              mountPath: /dev

      containers:
        - name: urbit
          image: urbit/urbit:latest
          volumeMounts:
            - name: pier
              mountPath: /urbit

      volumes:
        - name: host-dev
          hostPath:
            path: /dev
        - name: pier
          persistentVolumeClaim:
            claimName: sampel-palnet-pier
```

### Encryption in Transit: TLS 1.2+ with Strong Ciphers

**Implementation:**

```nginx
# nginx.conf - TLS configuration for Urbit HTTPS

server {
    listen 443 ssl http2;
    server_name sampel-palnet.fleet.example.com;

    # TLS 1.2 and 1.3 only (disable TLS 1.0/1.1)
    ssl_protocols TLSv1.2 TLSv1.3;

    # Strong cipher suites (prioritize forward secrecy)
    ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
    ssl_prefer_server_ciphers on;

    # TLS session caching
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # OCSP stapling (verify certificate validity)
    ssl_stapling on;
    ssl_stapling_verify on;
    resolver 8.8.8.8 8.8.4.4 valid=300s;

    # Certificates (Let's Encrypt or commercial CA)
    ssl_certificate /etc/letsencrypt/live/fleet.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/fleet.example.com/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/fleet.example.com/chain.pem;

    # Security headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
    add_header X-Frame-Options "DENY" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    # Proxy to Urbit
    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name sampel-palnet.fleet.example.com;
    return 301 https://$host$request_uri;
}
```

---

## Network Segmentation and DDoS Mitigation

### Ames Network Security

**Challenge:** Urbit's Ames protocol (UDP port 34343) is exposed to the internet and vulnerable to DDoS attacks.

**Mitigation Strategies:**

**1. Rate Limiting (iptables):**

```bash
# /etc/iptables/rules.v4 - Rate limit Ames packets

# Allow established connections
-A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Rate limit NEW Ames connections (max 100 packets/sec per IP)
-A INPUT -p udp --dport 34343 -m state --state NEW -m recent --set --name AMES
-A INPUT -p udp --dport 34343 -m state --state NEW -m recent --update --seconds 1 --hitcount 100 --name AMES -j DROP

# Allow Ames (after rate limiting)
-A INPUT -p udp --dport 34343 -j ACCEPT

# Drop everything else
-A INPUT -j DROP
```

**2. CloudFlare Spectrum (DDoS Protection):**

```yaml
# cloudflare-spectrum.yaml - Protect Ames via CloudFlare

# CloudFlare Spectrum Configuration (via UI or API)
# - Protocol: UDP
# - Origin Port: 34343
# - Proxy Port: 34343
# - DDoS Protection: Enabled
# - IP Firewall: Enabled (geo-blocking, rate limiting)

# Cost: ~$20/month per application

# Benefits:
# - 100 Tbps DDoS mitigation
# - Global anycast network
# - Automatic IP reputation filtering
# - Rate limiting and geo-blocking
```

**3. AWS Shield Advanced (for EKS deployments):**

```hcl
# terraform/aws-shield.tf

resource "aws_shield_protection" "ames_nlb" {
  name         = "urbit-ames-protection"
  resource_arn = aws_lb.ames_nlb.arn  # Network Load Balancer for Ames

  tags = {
    Name = "Urbit Ames DDoS Protection"
    Cost = "$3,000/month"  # Shield Advanced pricing
  }
}

resource "aws_shield_protection_group" "urbit_fleet" {
  protection_group_id = "urbit-fleet-protection-group"
  aggregation         = "MAX"
  pattern             = "ARBITRARY"
  members             = [aws_shield_protection.ames_nlb.id]

  tags = {
    Name = "Urbit Fleet DDoS Protection Group"
  }
}
```

### HTTP/HTTPS Security

**1. Web Application Firewall (WAF):**

```hcl
# terraform/aws-waf.tf

resource "aws_wafv2_web_acl" "urbit_waf" {
  name  = "urbit-fleet-waf"
  scope = "REGIONAL"  # For ALB (use CLOUDFRONT for CloudFront)

  default_action {
    allow {}
  }

  # Rule 1: Rate limiting (max 2,000 requests per 5 minutes per IP)
  rule {
    name     = "rate-limit"
    priority = 1

    action {
      block {}
    }

    statement {
      rate_based_statement {
        limit              = 2000
        aggregate_key_type = "IP"
      }
    }

    visibility_config {
      cloudwatch_metrics_enabled = true
      metric_name                = "RateLimitRule"
      sampled_requests_enabled   = true
    }
  }

  # Rule 2: AWS Managed Rules (OWASP Top 10, known bad inputs)
  rule {
    name     = "aws-managed-rules"
    priority = 2

    override_action {
      none {}
    }

    statement {
      managed_rule_group_statement {
        vendor_name = "AWS"
        name        = "AWSManagedRulesCommonRuleSet"
      }
    }

    visibility_config {
      cloudwatch_metrics_enabled = true
      metric_name                = "AWSManagedRules"
      sampled_requests_enabled   = true
    }
  }

  # Rule 3: Geo-blocking (block high-risk countries)
  rule {
    name     = "geo-block"
    priority = 3

    action {
      block {}
    }

    statement {
      geo_match_statement {
        country_codes = ["CN", "RU", "KP"]  # Block China, Russia, North Korea
      }
    }

    visibility_config {
      cloudwatch_metrics_enabled = true
      metric_name                = "GeoBlockRule"
      sampled_requests_enabled   = true
    }
  }

  visibility_config {
    cloudwatch_metrics_enabled = true
    metric_name                = "UrbitFleetWAF"
    sampled_requests_enabled   = true
  }
}

# Associate WAF with ALB
resource "aws_wafv2_web_acl_association" "alb" {
  resource_arn = aws_lb.urbit_alb.arn
  web_acl_arn  = aws_wafv2_web_acl.urbit_waf.arn
}
```

---

## Best Practices

1. **Defense-in-Depth:** Layer multiple security controls (network, container, OS, application)
2. **Least Privilege:** Grant minimum necessary permissions (RBAC, IAM policies)
3. **Encryption Everywhere:** Encrypt at rest (LUKS, KMS) and in transit (TLS 1.2+)
4. **Network Segmentation:** Isolate tenants with NetworkPolicies or separate VPCs
5. **Automated Patching:** Keep Kubernetes, OS, and Urbit up-to-date
6. **Audit Logging:** Log all access and changes (Kubernetes audit logs, CloudWatch)
7. **MFA for Admin Access:** Require multi-factor authentication for all admin accounts
8. **Regular Security Audits:** Conduct penetration tests and vulnerability scans quarterly
9. **Incident Response Plan:** Document procedures for security incidents and breaches
10. **Immutable Infrastructure:** Use IaC (Terraform) to prevent configuration drift

---

## Common Pitfalls

1. **Flat Networks:** No segmentation = lateral movement after compromise
2. **Weak TLS Configuration:** TLS 1.0/1.1 or weak ciphers = vulnerable to MITM attacks
3. **No Rate Limiting:** Ames DDoS = fleet offline
4. **Shared Namespaces:** Cross-tenant data leakage and noisy neighbor problems
5. **Privileged Containers:** Containers running as root = container escape risk
6. **No NetworkPolicies:** Default Kubernetes allows all traffic = insecure by default
7. **Unencrypted Pier Storage:** Disk theft = pier data compromise
8. **Missing Audit Logs:** Can't investigate incidents without logs
9. **Weak Secrets Management:** Hardcoded passwords in code/config = credential leakage
10. **No Security Updates:** Unpatched vulnerabilities = easy exploit targets

---

## Related Skills

**Phase 3: Fleet & Compliance**
- `compliance-frameworks` - GDPR, HIPAA, SOC2 compliance patterns (complementary to security)
- `fleet-manager` - Fleet-scale security operations and incident response

**Phase 2: Platform Operations**
- `backup-strategies` - Secure backup storage and encryption for disaster recovery
- `performance-profiling` - Identify security overhead from encryption and isolation

**Phase 1: Core Urbit Operations**
- `networking-fundamentals` - Secure network configuration (firewalls, VPNs, DNS)
- `deployment-basics` - Basic security hardening for individual ships

**Related Workflows (Security, Cloud, DevOps)**
- `full-stack-orchestration::security-auditor` - Comprehensive security audits and vulnerability assessments
- `cloud-infrastructure::kubernetes-architect` - Advanced Kubernetes security patterns (Pod Security, RBAC, Network Policies)
- `cloud-infrastructure::network-engineer` - Network security design (VPNs, firewalls, DDoS protection)
- `observability-monitoring::observability-engineer` - Security monitoring and alerting (intrusion detection, audit logging)

---

## Resources

### Multi-Tenant Security
- [Kubernetes Multi-Tenancy Working Group](https://github.com/kubernetes-sigs/multi-tenancy)
- [NIST Multi-Tenancy Architecture](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-146.pdf)
- [Hierarchical Namespaces (HNC)](https://github.com/kubernetes-sigs/hierarchical-namespaces)

### Encryption
- [LUKS Documentation](https://gitlab.com/cryptsetup/cryptsetup)
- [AWS KMS Best Practices](https://docs.aws.amazon.com/kms/latest/developerguide/best-practices.html)
- [TLS Configuration Guide (Mozilla)](https://wiki.mozilla.org/Security/Server_Side_TLS)

### Network Security
- [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- [Calico Network Policies](https://docs.tigera.io/calico/latest/network-policy/)
- [AWS WAF Documentation](https://docs.aws.amazon.com/waf/)
- [CloudFlare DDoS Protection](https://www.cloudflare.com/ddos/)

### Security Standards
- [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)

### Tools
- [Falco](https://falco.org/) - Runtime security monitoring
- [Trivy](https://github.com/aquasecurity/trivy) - Container vulnerability scanning
- [Kube-bench](https://github.com/aquasecurity/kube-bench) - CIS Kubernetes compliance checking
- [AppArmor](https://apparmor.net/) - Mandatory Access Control
