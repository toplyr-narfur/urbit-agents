# VPS Deployment Providers Skill

Comprehensive comparison of VPS providers for Urbit ship hosting including pricing, performance, features, and provider-specific optimizations (2025).

## Overview

Choosing the right VPS provider for Urbit ships involves balancing cost, performance, ease of use, and cloud-native features. This skill provides detailed comparisons based on 2025 pricing and performance benchmarks.

## Quick Comparison Table (2025)

| Provider | Entry Plan | 4GB RAM Plan | Best For | Urbit Suitability |
|----------|-----------|--------------|----------|-------------------|
| **Hetzner** | €4.15/mo (2GB) | €7.59/mo | Best value | ⭐⭐⭐⭐⭐ |
| **DigitalOcean** | $6/mo (1GB) | $24/mo | Beginners, docs | ⭐⭐⭐⭐⭐ |
| **Linode** | $5/mo (1GB) | $24/mo | Performance, cPanel | ⭐⭐⭐⭐⭐ |
| **Vultr** | $6/mo (1GB) | $24/mo | Global regions | ⭐⭐⭐⭐ |
| **AWS Lightsail** | $5/mo (512MB) | $20/mo | AWS integration | ⭐⭐⭐⭐ |
| **OVH** | €3.50/mo (2GB) | €11.99/mo | Europe, budget | ⭐⭐⭐ |

## Detailed Provider Analysis

### Hetzner Cloud

**Headquarters**: Germany (GDPR-compliant)

**Pricing** (2025):
- CX11: €4.15/mo - 2GB RAM, 20GB SSD, 1 vCPU
- **CX23: €3.49/mo - 4GB RAM, 40GB SSD, 2 vCPUs** ✅ **BEST VALUE 2025**
- CX21: €5.83/mo - 4GB RAM, 40GB SSD, 2 vCPUs (older generation)
- CX31: €10.45/mo - 8GB RAM, 80GB SSD, 2 vCPUs

**2025 Update**: New cost-optimized CX23 plan (€3.49/mo for 4GB RAM) offers 40% cost reduction vs CX21 with AMD EPYC-Genoa processors providing 30%+ faster disk/network performance.

**Strengths**:
- **Best price-to-performance ratio** (industry-leading)
- Excellent raw hardware performance
- Generous bandwidth (20TB+)
- European data centers (Finland, Germany)
- Simple, transparent pricing

**Weaknesses**:
- Smaller ecosystem compared to DigitalOcean/AWS
- Limited global regions (primarily Europe)
- Less extensive documentation

**Urbit-Specific**:
- **Best for**: Budget-conscious deployments, European users
- Single planet: **CX23 (€3.49/mo = ~$3.75/mo)** or CX21 (€5.83/mo = ~$6.50/mo)
- GroundSeg (3-5 ships): CX41 (€15.30/mo, 16GB RAM)

**CLI**:
```bash
# Install Hetzner CLI
brew install hcloud  # macOS
# Or: wget https://github.com/hetznercloud/cli/releases/download/v1.38.0/hcloud-linux-amd64.tar.gz

# Create server (use cx23 for best value in 2025)
hcloud server create --name urbit-ship --type cx23 --image ubuntu-22.04 --ssh-key my-key
```

---

### DigitalOcean

**Headquarters**: USA

**Pricing** (2025):
- Basic Droplet: $6/mo - 1GB RAM, 25GB SSD, 1 vCPU (increased from $5 in 2024)
- Basic Droplet: $20/mo - 4GB RAM, 80GB SSD, 2 shared vCPUs ✅ **Recommended for single planet**
- General Purpose: $63/mo - 8GB RAM, 160GB SSD, 2 vCPUs

**Note**: Basic Droplets use shared vCPUs (more affordable than dedicated vCPU plans at $24/mo+). Starting Jan 1, 2026, billing changes to per-second (minimum 60s or $0.01).

**Strengths**:
- **Most beginner-friendly** UI/UX
- **Extensive documentation** (best in class)
- Robust marketplace (1-click apps)
- Strong community support
- Cloud Firewalls included (no extra cost)
- Built-in monitoring and alerting
- $100 credit for new users (60 days)

**Weaknesses**:
- Higher pricing than Hetzner
- Recent price increases (2024-2025)

**Urbit-Specific**:
- **Best for**: First-time VPS users, need for good documentation
- Single planet: $20/mo (4GB Basic Droplet)
- GroundSeg (3-5 ships): $63/mo (8GB General Purpose)
- Popular in Urbit community tutorials

**Features**:
- **Volumes**: Block storage ($0.10/GB/mo) - detachable, persistent
- **Floating IPs**: Static IP ($4/mo if unused, free if attached)
- **Snapshots**: Backup images ($0.05/GB/mo)
- **Spaces**: S3-compatible object storage ($5/mo for 250GB)

**CLI (doctl)**:
```bash
# Install
brew install doctl  # macOS
# Or: snap install doctl

# Authenticate
doctl auth init

# Create droplet
doctl compute droplet create urbit-ship \
  --image ubuntu-22-04-x64 \
  --size s-2vcpu-4gb \
  --region nyc1 \
  --ssh-keys $(doctl compute ssh-key list --format ID --no-header)
```

---

### Linode (Akamai Cloud)

**Headquarters**: USA (acquired by Akamai in 2022)

**Pricing** (2025):
- Nanode: $5/mo - 1GB RAM, 25GB SSD, 1 vCPU
- Shared CPU 4GB: $24/mo - 4GB RAM, 80GB SSD, 2 shared vCPUs, 4TB bandwidth ✅ **Recommended for single planet**
- Dedicated CPU 4GB: $36/mo - 4GB RAM, 80GB SSD, 2 dedicated vCPUs, 4TB bandwidth
- Shared CPU 8GB: $48/mo - 8GB RAM, 160GB SSD, 4 vCPUs

**Note**: Shared CPU plans offer burstable performance (100% for short periods, <80% sustained average). Dedicated CPU plans provide 100% sustained CPU availability with competition-free guaranteed resources.

**Strengths**:
- **Generous bandwidth** (4-8TB included)
- **Transparent pricing** (no hidden fees)
- Strong cPanel integration
- Excellent all-around support
- 99.99% uptime SLA
- **Longview monitoring** (free, detailed)

**Weaknesses**:
- Less beginner-friendly than DigitalOcean
- Smaller marketplace

**Urbit-Specific**:
- **Best for**: Experienced users, need for bandwidth
- Single planet: $24/mo (Shared CPU 4GB) or $36/mo (Dedicated CPU 4GB)
- GroundSeg (3-5 ships): $48/mo (Shared CPU 8GB plan)

**Features**:
- **Block Storage**: Volumes ($0.10/GB/mo), up to 10TB
- **Backups**: Automated backups ($2-10/mo depending on plan)
- **NodeBalancers**: Load balancing ($10/mo)
- **Private VLAN**: Free private networking

**CLI (linode-cli)**:
```bash
# Install
pip3 install linode-cli

# Configure
linode-cli configure

# Create instance
linode-cli linodes create \
  --type g6-standard-2 \
  --region us-east \
  --image linode/ubuntu22.04 \
  --root_pass <password>
```

---

### Vultr

**Headquarters**: USA

**Pricing** (2025):
- Cloud Compute: $6/mo - 1GB RAM, 25GB SSD, 1 vCPU
- Regular Performance: $20/mo - 4GB RAM, 80GB SSD, 2 vCPUs, 3TB bandwidth ✅ **Recommended for single planet**
- High Performance (AMD/Intel): $24/mo - 4GB RAM, 100GB SSD, 2 vCPUs, 5TB bandwidth
- High Frequency: $24/mo - 4GB RAM, 128GB SSD, 2 vCPUs, 3TB bandwidth

**Strengths**:
- **Global footprint** (25+ locations)
- Competitive pricing
- Fast provisioning
- Good network performance
- Bare-metal options available

**Weaknesses**:
- Smaller community than DigitalOcean
- Less comprehensive documentation

**Urbit-Specific**:
- **Best for**: Global distribution, need for specific regions
- Single planet: $20/mo (4GB Regular Performance)

**Features**:
- **Block Storage**: Volumes ($0.10/GB/mo)
- **Reserved IPs**: Static IPs ($3/mo)
- **Snapshots**: Free (automatic + manual)

**CLI (vultr-cli)**:
```bash
# Install
brew install vultr/vultr-cli/vultr-cli

# Configure
export VULTR_API_KEY="your-api-key"

# Create instance
vultr-cli instance create --region ewr --plan vc2-2c-4gb --os 1743 --label urbit-ship
```

---

### AWS Lightsail

**Headquarters**: USA (Amazon Web Services)

**Pricing** (2025):
- $5/mo - 512MB RAM, 20GB SSD, 1 vCPU (insufficient for Urbit)
- $10/mo - 1GB RAM, 40GB SSD, 1 vCPU
- $20/mo - 2GB RAM, 60GB SSD, 1 vCPU ✅ **Minimum for single planet**
- $24/mo - 4GB RAM, 60GB SSD, 2 vCPUs ✅ **Recommended for single planet**

**Strengths**:
- **Fixed pricing** (no surprise bills like EC2)
- Free first month (trial)
- **Automatic snapshots** included (free)
- Integration with broader AWS ecosystem
- Good network performance

**Weaknesses**:
- Competitive pricing with other providers at 4GB tier
- Limited features compared to full EC2
- Requires AWS knowledge

**Urbit-Specific**:
- **Best for**: AWS users, need for AWS integrations (S3, CloudWatch)
- Single planet: $24/mo (4GB plan)
- Competitive with Hetzner/DigitalOcean at 4GB tier

**Features**:
- **Static IPs**: 5 free static IPs per account
- **Snapshots**: Automatic + manual (included)
- **Load Balancers**: $18/mo
- **Managed Databases**: $15/mo+

**CLI (aws lightsail)**:
```bash
# Included in AWS CLI
aws lightsail create-instances \
  --instance-names urbit-ship \
  --availability-zone us-east-1a \
  --blueprint-id ubuntu_22_04 \
  --bundle-id medium_2_0
```

---

### OVH

**Headquarters**: France (European focus)

**Pricing** (2025):
- VPS Starter: €3.50/mo - 2GB RAM, 20GB SSD, 1 vCPU
- VPS Value: €11.99/mo - 4GB RAM, 80GB SSD, 2 vCPUs ✅ **Budget option**

**Strengths**:
- **Very competitive pricing** (Europe)
- Strong European presence
- Good for GDPR compliance
- DDoS protection included

**Weaknesses**:
- Less polished UI
- Limited English documentation
- Smaller community

**Urbit-Specific**:
- **Best for**: European budget deployments
- Single planet: €11.99/mo (~$13/mo)
- Good Hetzner alternative

---

## Performance Benchmarks (2025)

Based on community benchmarks and performance testing:

**Best Overall Performance**: Vultr, AWS Lightsail (low latency, fast disk I/O)

**Best Value Performance**: Hetzner (excellent raw hardware for price)

**Best Beginner Experience**: DigitalOcean (UI, documentation, support)

**Best Bandwidth**: Linode (4-8TB included vs 1-2TB competitors)

---

## Decision Framework

### Choose **Hetzner** if:
- Budget is priority ($6.50/mo for 4GB)
- Comfortable with less documentation
- European deployment acceptable
- Maximum performance per dollar

### Choose **DigitalOcean** if:
- First VPS deployment
- Need excellent documentation
- Want polished UI/UX
- Community resources important
- Don't mind paying premium ($24/mo)

### Choose **Linode** if:
- Need high bandwidth (4-8TB)
- cPanel integration required
- Want transparent pricing
- Experienced with VPS management

### Choose **Vultr** if:
- Need specific geographic regions
- Want fast provisioning
- Global distribution important

### Choose **AWS Lightsail** if:
- Already using AWS ecosystem
- Need AWS service integrations
- Want fixed pricing (vs EC2)
- Budget allows ($40/mo for 4GB)

### Choose **OVH** if:
- European deployment required
- Absolute minimum budget
- GDPR compliance critical

---

## Urbit Community Recommendations

Based on Urbit community tutorials and discussions:

1. **DigitalOcean** - Most popular in tutorials (ease of use)
2. **Hetzner** - Growing popularity (price-to-performance)
3. **Linode** - Established choice (reliability)
4. **Vultr** - Alternative option (regions)
5. **AWS Lightsail** - Less common (cost)

---

## Cost Optimization

**Single Planet** (annual cost):
- Hetzner CX21: €70/year (~$78/year) ⭐ **Best value**
- DigitalOcean 4GB: $288/year
- Linode 4GB: $288/year
- Vultr 4GB: $288/year
- AWS Lightsail 4GB: $480/year

**Savings strategies**:
- **Hetzner**: No prepay discount needed (already cheapest)
- **DigitalOcean**: Annual prepay (5-10% discount potential)
- **Linode**: Annual prepay option
- **Monitor usage**: Downsize if underutilized after 30 days
- **Reserved IPs**: Use provider's free/included static IPs

---

## Best Practices

1. **Start small**: Begin with 4GB plan, scale up if needed
2. **Use block storage**: For production piers (data persistence)
3. **Enable monitoring**: Provider monitoring (free) + custom
4. **Automate backups**: Provider snapshots + offsite S3
5. **Test before committing**: Use free trials/credits
6. **Document choice**: Record provider, plan, costs for team
7. **Regional compliance**: Consider data residency requirements

---

## Reference

- DigitalOcean pricing: https://www.digitalocean.com/pricing
- Linode pricing: https://www.linode.com/pricing/
- Hetzner pricing: https://www.hetzner.com/cloud
- Vultr pricing: https://www.vultr.com/pricing/
- AWS Lightsail pricing: https://aws.amazon.com/lightsail/pricing/
- Urbit hosting guide: https://blog.remilia.org/urbit-cloud-host/

---

## Summary

VPS provider selection for Urbit ships prioritizes 4GB RAM minimum, SSD storage, and reliable networking. Hetzner offers best value (€5.83/mo), DigitalOcean best beginner experience ($24/mo), Linode best bandwidth and support ($24/mo), Vultr best global reach ($24/mo), and AWS Lightsail best AWS integration ($40/mo). Community recommends DigitalOcean for ease of use, Hetzner for budget deployments. All providers support Urbit's modest requirements (2GB RAM minimum), with decision based on budget, experience level, geographic needs, and ecosystem preferences.
