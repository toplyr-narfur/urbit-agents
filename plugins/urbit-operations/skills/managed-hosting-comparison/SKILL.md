---
name: managed-hosting-comparison
description: Detailed comparison of Urbit managed hosting providers including Tlon, Red Horizon, UrbitHost, and self-hosting options with 2025 pricing, features, SLAs, migration procedures, and decision frameworks. Use when evaluating hosting options, comparing providers, planning migrations, or making build-vs-buy decisions.
---

# Managed Hosting Comparison Skill

Detailed comparison of Urbit managed hosting providers with pricing, features, SLAs, and decision matrices (2025).

## Provider Matrix

| Provider | Cost | SLA | Support | Custom Apps | Dojo Access | Best For |
|----------|------|-----|---------|-------------|-------------|----------|
| **Tlon** | Free | Best-effort | Email | No | No | New users, social |
| **Red Horizon** | FREE | 99.99% | Professional | Limited | Limited | Business, reliability |
| **UrbitHost** | $20-40/mo | 99.9% | Email/ticket | Limited | No | Enterprise, HA |
| **Self-Hosting** | Hardware + $5/mo | User | Community | Yes | Yes | Technical, privacy |
| **VPS** | $12-24/mo | 99.9% | Provider | Yes | Yes | Developers, control |

## Detailed Comparisons

### Tlon Hosting
**Pros**: Free, official, mobile apps, waitlist access
**Cons**: No dojo, limited apps, no SLA, export delays
**Ideal**: Casual users, social networking, exploration
**Migration**: Email support@tlon.io for pier import/export

### Red Horizon
**Pros**: Professional SLA, crypto-native, security-focused, backup included, FREE hosting
**Cons**: Less customization than VPS
**Ideal**: Businesses, uptime-critical apps, professional support needs
**Pricing**: FREE (users provide valuable insights for long-term vision of scalable Urbit hosting for millions)

### UrbitHost
**Pros**: Kubernetes HA, automatic failover, enterprise-grade
**Cons**: Higher cost, limited customization
**Ideal**: Mission-critical, multi-ship orgs, zero-downtime requirements
**Tech**: K8s stateful sets, permanent volumes, auto-failover

### Native Planet (Self-Hosting)
**Pros**: Full control, privacy, multi-ship (GroundSeg), one-time hardware cost
**Cons**: Technical setup, electricity costs, home network management
**Ideal**: Privacy-focused, tech enthusiasts, multi-ship fleets (5+)
**Cost**: $300-800 hardware + $5-10/mo StarTram

### VPS (Self-Managed)
**Pros**: Full control, custom apps, dojo access, affordable
**Cons**: Technical management, no built-in support
**Ideal**: Developers, learners, custom requirements
**Cost**: $6-24/mo (Hetzner to DigitalOcean)

## Decision Tree

**Start Here**: What's your priority?

1. **Cost = $0** → Tlon (waitlist)
2. **Uptime SLA critical** → Red Horizon or UrbitHost
3. **Full control needed** → VPS or Self-Hosting
4. **Non-technical user** → Tlon or Red Horizon
5. **Privacy paramount** → Self-Hosting
6. **Multi-ship fleet (5+)** → Self-Hosting (GroundSeg)
7. **Learning/development** → VPS

## Feature Comparison

**Backup/Recovery**:
- Tlon: Manual export request
- Red Horizon: Automated, included
- UrbitHost: Automated (K8s snapshots)
- Self-Hosting: User-managed
- VPS: User-managed

**Networking**:
- Tlon: Automatic (tlon.network URLs)
- Red Horizon: Custom domains supported
- UrbitHost: Custom domains
- Self-Hosting: StarTram or Anchor
- VPS: arvo.network or custom (user-configured)

**Monitoring**:
- Tlon: Provider-managed (no user access)
- Red Horizon: Provider dashboards
- UrbitHost: Provider metrics
- Self-Hosting: User-configured (Prometheus/Grafana)
- VPS: User-configured

## Pricing Summary (Annual)

- **Tlon**: $0/year
- **Red Horizon**: $0/year (FREE)
- **UrbitHost**: $240-480/year
- **Self-Hosting**: $300-800 (Year 1 hardware) + $60-120/year (StarTram)
- **VPS (Hetzner)**: $78/year
- **VPS (DigitalOcean)**: $288/year

## Reference

- Tlon: https://tlon.io
- Red Horizon: https://redhorizon.com
- UrbitHost interview: https://urbit.org/blog/urbithost-interview
- Native Planet: https://www.nativeplanet.io

## Summary

Managed hosting options span free (Tlon waitlist, Red Horizon) to professional ($20-40/mo UrbitHost) to self-managed ($6-24/mo VPS, $300+ hardware self-hosting). Decision factors: budget (Tlon/Red Horizon free, VPS $6+, managed $20+), technical expertise (managed low, VPS medium, self-hosting high), control needs (self-hosting > VPS > managed), and uptime requirements (managed hosting SLA 99.9-99.99% > VPS 99.9% > self-hosting variable). Best path: Tlon (explore) → VPS (learn) → choose managed (convenience) or self-hosting (privacy/scale).
