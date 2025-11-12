# Container Security Skill

Docker and container security hardening for GroundSeg Urbit deployments including image security, runtime protection, and compliance.

## Overview

Container security for Urbit ships involves securing Docker images, runtime configurations, network isolation, and host system hardening.

## Docker Image Security

### Official Images Only

```yaml
services:
  urbit:
    image: urbit/urbit:latest  # Official image only
    # Never use: random-user/urbit:latest
```

### Image Scanning

```bash
# Scan for vulnerabilities
docker scan urbit/urbit:latest

# Use Trivy (open-source scanner)
docker run aquasec/trivy image urbit/urbit:latest
```

### Pin Specific Versions

```yaml
services:
  urbit:
    image: urbit/urbit:v3.2  # Specific version, not :latest
```

## Runtime Security

### Non-Root User

```dockerfile
# Dockerfile
USER urbit:urbit  # Run as non-root user
```

```yaml
# docker-compose.yml
services:
  urbit:
    user: "1000:1000"  # UID:GID (non-root)
```

### Read-Only Root Filesystem

```yaml
services:
  urbit:
    read_only: true
    tmpfs:
      - /tmp  # Writable temp directory only
    volumes:
      - ./pier:/urbit:rw  # Pier writable, everything else read-only
```

### Drop Capabilities

```yaml
services:
  urbit:
    cap_drop:
      - ALL  # Drop all capabilities
    cap_add:
      - NET_BIND_SERVICE  # Add only necessary capabilities
```

### Security Options

```yaml
services:
  urbit:
    security_opt:
      - no-new-privileges:true  # Prevent privilege escalation
      - apparmor=docker-default  # AppArmor profile
```

## Network Isolation

### Custom Networks

```yaml
networks:
  urbit-network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.28.0.0/16

services:
  ship1:
    networks:
      - urbit-network
```

### Port Exposure

```yaml
services:
  urbit:
    ports:
      - "127.0.0.1:8080:8080"  # Bind to localhost only
    # NOT: - "8080:8080"  # Binds to all interfaces (insecure)
```

## Resource Limits

```yaml
services:
  urbit:
    mem_limit: 4g
    memswap_limit: 4g  # No swap
    cpus: '1.0'
    pids_limit: 200  # Process limit
```

## Secrets Management

### Environment Variables (Avoid Hardcoding)

```yaml
# .env file (not committed to git)
MINIO_ACCESS_KEY=your-access-key
MINIO_SECRET_KEY=your-secret-key

# docker-compose.yml
services:
  urbit:
    env_file: .env
```

### Docker Secrets

```bash
# Create secret
echo "my-secret-password" | docker secret create urbit_password -

# Use in compose
services:
  urbit:
    secrets:
      - urbit_password

secrets:
  urbit_password:
    external: true
```

## Host System Security

### Docker Daemon Security

```bash
# /etc/docker/daemon.json
{
  "icc": false,  # Disable inter-container communication
  "userns-remap": "default",  # User namespace isolation
  "no-new-privileges": true,
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}

# Restart Docker
sudo systemctl restart docker
```

### Firewall (UFW)

```bash
# Block Docker bypass of UFW
sudo nano /etc/ufw/after.rules

# Add at end:
*filter
:DOCKER-USER - [0:0]
-A DOCKER-USER -j RETURN
COMMIT

# Restart UFW
sudo ufw reload
```

## Audit Logging

```yaml
services:
  urbit:
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
        labels: "ship=sampel-palnet,env=production"
```

```bash
# View logs with filtering
docker logs --since 1h urbit-ship
```

## Security Scanning

```bash
# Scan running containers
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  aquasec/trivy image --severity HIGH,CRITICAL urbit/urbit
```

## Compliance Hardening

### CIS Docker Benchmark (v1.6.0)

**Automated Compliance Checking** with Docker Bench for Security:

Docker Bench for Security is a script that checks for common best practices around deploying Docker containers in production. It implements the CIS Docker Benchmark v1.6.0 (latest as of 2025).

```bash
# Run CIS benchmark audit (CIS Docker Benchmark v1.6.0)
docker run --rm --net host --pid host --userns host --cap-add audit_control \
  -v /etc:/etc:ro \
  -v /var/lib:/var/lib:ro \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v /usr/lib/systemd:/usr/lib/systemd:ro \
  -v /etc/systemd:/etc/systemd:ro \
  docker/docker-bench-security

# Save report to file
docker run --rm --net host --pid host --userns host --cap-add audit_control \
  -v /etc:/etc:ro \
  -v /var/lib:/var/lib:ro \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v /usr/lib/systemd:/usr/lib/systemd:ro \
  -v /etc/systemd:/etc/systemd:ro \
  docker/docker-bench-security > docker-bench-report.txt

# Review the report for WARN and FAIL items
cat docker-bench-report.txt | grep -E "WARN|FAIL"
```

**Key Areas Checked**:
- Host configuration
- Docker daemon configuration
- Docker daemon configuration files
- Container images and build files
- Container runtime security
- Docker security operations
- Docker Swarm configuration

**Recommended Schedule**: Run weekly or after configuration changes.

**Source**: https://github.com/docker/docker-bench-security

## Best Practices Checklist

- [ ] Use official images only (urbit/urbit)
- [ ] Pin specific image versions (not :latest)
- [ ] Run as non-root user
- [ ] Drop all capabilities, add only necessary
- [ ] Read-only root filesystem
- [ ] Resource limits (CPU, memory, PIDs)
- [ ] Network isolation (custom networks)
- [ ] Localhost-only port binding
- [ ] Secrets via environment variables or Docker secrets
- [ ] Audit logging enabled
- [ ] Regular vulnerability scanning
- [ ] UFW configured to prevent Docker bypass
- [ ] Docker daemon hardening (/etc/docker/daemon.json)

## Reference

- Docker security: https://docs.docker.com/engine/security/
- CIS Docker Benchmark: https://www.cisecurity.org/benchmark/docker
- OWASP Docker Security: https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html

## Summary

Container security for Urbit ships requires multi-layered approach: official images only, runtime hardening (non-root user, dropped capabilities, read-only filesystem), network isolation (custom networks, localhost binding), resource limits, secrets management, and host security (Docker daemon hardening, UFW configuration). Regular vulnerability scanning, audit logging, and CIS benchmark compliance validation ensure ongoing security posture.
