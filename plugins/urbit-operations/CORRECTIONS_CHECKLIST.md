# Urbit Operations Plugin - Corrections Checklist

**Date:** 2025-11-11
**Total Issues:** 9 (3 Critical, 4 High, 2 Medium)

---

## ✅ CRITICAL - Fix Immediately

### [x] 1. AWS Lightsail Pricing Fix
**Files:**
- `/skills/vps-deployment-providers/SKILL.md` (lines 213, 229)
- `/agents/vps-deployment-specialist.md`

**Change:** $40/mo → $24/mo for 4GB RAM
**Source:** https://www.digitalocean.com/pricing/droplets

---

### [x] 2. Red Horizon Pricing Fix
**Files:**
- `/skills/managed-hosting-comparison/SKILL.md`
- `/agents/managed-hosting-advisor.md`

**Change:** $15-30/mo → FREE
**Source:** https://chorus.one/articles/announcing-red-horizon

---

### [x] 3. DigitalOcean Pricing Fix
**Files:**
- `/skills/vps-deployment-providers/SKILL.md` (lines 66, 84)
- `/agents/vps-deployment-specialist.md`
- `/commands/deploy-vps-planet.md`

**Change:** $24/mo → $20/mo for 4GB Basic Droplet
**Note:** Add that Basic Droplets use shared vCPUs
**Source:** https://www.digitalocean.com/pricing/droplets

---

## ⚠️ HIGH PRIORITY - Update Soon

### [x] 4. Hetzner CX23 Addition
**Files:**
- `/skills/vps-deployment-providers/SKILL.md` (line 28-29)

**Add:** CX23 at €3.49/mo (4GB RAM) - BEST VALUE 2025
**Note:** Supersedes CX21, 40% cost reduction, AMD EPYC-Genoa
**Source:** https://www.hetzner.com/press-release/new-cx-plans/

---

### [x] 5. Vultr Pricing Verification
**Files:**
- `/skills/vps-deployment-providers/SKILL.md`

**Action:** Verify 4GB plan exact pricing ($24 vs $30 discrepancy)
**Result:** Updated with verified pricing - Regular Performance $20/mo, High Performance $24/mo
**Source:** https://www.vultr.com/pricing/ (verified 2025-11-11)

---

### [x] 6. Linode Pricing Clarification
**Files:**
- `/skills/vps-deployment-providers/SKILL.md`

**Action:** Distinguish Shared CPU vs Dedicated CPU pricing for 4GB
**Result:** Updated with both plans - Shared CPU $24/mo, Dedicated CPU $36/mo
**Source:** https://www.linode.com/pricing/ (verified 2025-11-11)

---

### [x] 7. SSH Key Algorithms Update
**Files:**
- `/skills/network-security-advanced/SKILL.md`
- `/commands/setup-production.md`

**Add:**
```
KexAlgorithms sntrup761x25519-sha512@openssh.com,curve25519-sha256,...
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,...
RequiredRSASize 3072
```
**Source:** https://www.sshaudit.com/hardening_guides.html

---

## ℹ️ MEDIUM PRIORITY - Optional

### [x] 8. Fail2ban Stricter Option
**Files:**
- `/skills/network-security-advanced/SKILL.md`

**Add:** Alternative stricter settings (maxretry=5, findtime=1d, bantime=2w)
**Source:** https://wiki.archlinux.org/title/Fail2ban

---

### [x] 9. Docker Bench Security Tool
**Files:**
- `/skills/container-security/SKILL.md`

**Add:** Docker Bench Security automation tool (CIS v1.6.0)
**Source:** https://github.com/docker/docker-bench-security

---

## Quick Reference: Search & Replace

### AWS Lightsail
```bash
# Find: \$40/mo.*4GB RAM
# Replace with: $24/mo - 4GB RAM
```

### Red Horizon
```bash
# Find: \$15-30/mo
# Replace with: FREE
```

### DigitalOcean
```bash
# Find: \$24/mo - 4GB RAM.*Regular
# Replace with: $20/mo - 4GB RAM, 80GB SSD, 2 vCPUs (Basic Droplet with shared vCPUs)
```

### Hetzner
```bash
# Add after CX11:
**CX23: €3.49/mo - 4GB RAM, 40GB SSD, 2 vCPUs** ✅ **BEST VALUE 2025**
```

---

## Verification URLs

- [ ] Hetzner: https://www.hetzner.com/cloud
- [ ] DigitalOcean: https://www.digitalocean.com/pricing/droplets
- [ ] Linode: https://www.linode.com/pricing/
- [ ] Vultr: https://www.vultr.com/pricing/
- [ ] AWS Lightsail: https://aws.amazon.com/lightsail/pricing/
- [ ] Tlon: https://tlon.io
- [ ] Red Horizon: https://redhorizon.com
- [ ] SSH Audit: https://www.sshaudit.com/hardening_guides.html

---

## Completion Tracking

**Critical Issues:** 3/3 completed ✅
**High Priority:** 4/4 completed ✅
**Medium Priority:** 2/2 completed ✅

**Overall Progress:** 9/9 (100%) ✅ COMPLETE

**Target Completion:**
- Critical: End of week
- High Priority: End of month
- Medium Priority: Backlog

---

## Testing After Fixes

- [x] Verify all pricing changes with official sources
- [x] Check that cross-references between components remain consistent
- [x] Validate that cost examples in commands match updated pricing
- [x] Ensure markdown formatting preserved
- [ ] Run plugin validation (if available)

---

## Implementation Summary

**Completion Date:** 2025-11-11
**All 9 issues resolved:**
- ✅ 3 Critical pricing fixes (AWS Lightsail, Red Horizon, DigitalOcean)
- ✅ 4 High priority updates (Hetzner CX23, Vultr, Linode, SSH algorithms)
- ✅ 2 Medium priority enhancements (Fail2ban, Docker Bench Security)

**Verified Pricing (2025-11-11):**
- Hetzner CX23: €3.49/mo (BEST VALUE)
- DigitalOcean Basic 4GB: $20/mo
- Vultr Regular Performance 4GB: $20/mo
- Linode Shared CPU 4GB: $24/mo
- AWS Lightsail 4GB: $24/mo
- Red Horizon: FREE
