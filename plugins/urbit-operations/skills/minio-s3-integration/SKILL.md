# MinIO S3 Integration Skill

Self-hosted S3-compatible object storage for Urbit ships with GroundSeg including setup, bucket management, and application integration.

## Overview

MinIO provides S3-compatible storage for Urbit ships, enabling efficient handling of large files (images, videos, documents) outside the ship's pier, preventing loom bloat and improving performance.

## Why MinIO for Urbit

**Problems it solves**:
- **Pier bloat**: Large files in Clay filesystem consume loom memory
- **Performance**: Offload media storage from ship state
- **Scalability**: S3 storage scales independently of ship loom
- **Compatibility**: S3 API compatible with existing tools/SDKs

**Use cases**:
- Media storage (images, videos)
- Document management
- File sharing applications
- Backup storage

## MinIO Deployment with GroundSeg

### Option A: One-Click Activation (2025 Recommended)

1. Access GroundSeg UI (`http://nativeplanet.local`)
2. Navigate to **Storage** settings
3. Click **"Enable MinIO"**
4. Automatic Docker container deployment
5. MinIO accessible at: `http://<server-ip>:9001`

### Option B: Manual Docker Configuration

```yaml
# docker-compose.yml
version: '3.8'

services:
  minio:
    image: minio/minio:latest
    ports:
      - "9000:9000"    # S3 API
      - "9001:9001"    # Web console
    volumes:
      - ./minio-data:/data
    environment:
      MINIO_ROOT_USER: admin
      MINIO_ROOT_PASSWORD: <strong-password-here>
    command: server /data --console-address ":9001"
    restart: always
```

```bash
# Start MinIO
docker-compose up -d minio

# Access web console
# Browser: http://<server-ip>:9001
# Login: admin / <password-from-env>
```

## Initial Configuration

### Create Buckets

**Via Web Console**:
1. Access MinIO console (`http://<server-ip>:9001`)
2. Login with root credentials
3. Click **Buckets** → **Create Bucket**
4. Bucket name: `urbit-media` (example)
5. Set bucket policy (public or private)

**Via CLI (mc)**:
```bash
# Install MinIO client
wget https://dl.min.io/client/mc/release/linux-amd64/mc
chmod +x mc
sudo mv mc /usr/local/bin/

# Configure alias
mc alias set myminio http://localhost:9000 admin <password>

# Create bucket
mc mb myminio/urbit-media

# List buckets
mc ls myminio
```

### Access Credentials

**Create access key/secret for application**:
1. MinIO Console → **Access Keys**
2. Click **Create Access Key**
3. Save:
   - Access Key: `AKIAIOSFODNN7EXAMPLE`
   - Secret Key: `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY`

## Bucket Policies

### Public Read Bucket (for public media)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {"AWS": ["*"]},
      "Action": ["s3:GetObject"],
      "Resource": ["arn:aws:s3:::urbit-media/*"]
    }
  ]
}
```

Apply policy:
```bash
mc policy set download myminio/urbit-media
```

### Private Bucket (requires authenticated access)

```bash
mc policy set private myminio/urbit-private
```

## Urbit Application Integration

### S3 Configuration in Urbit App

Example Hoon configuration:
```hoon
+$  s3-config
  $:  endpoint='http://minio-server:9000'
      access-key='AKIAIOSFODNN7EXAMPLE'
      secret-key='wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY'
      bucket='urbit-media'
      region='us-east-1'
  ==
```

### Upload Workflow

1. User uploads file via Urbit app (web interface)
2. App receives file data
3. App calls S3 API (via `%iris` HTTP client vane)
4. File stored in MinIO bucket
5. App stores S3 URL in Clay (small metadata, not file data)
6. Ship state remains small (only URL stored, not file)

## Security Best Practices

### Access Control

```bash
# Create dedicated user for Urbit app (not root)
mc admin user add myminio urbit-app <password>

# Grant read/write to specific bucket only
mc admin policy set myminio readwrite user=urbit-app
```

### SSL/TLS

**Option 1: Reverse Proxy (Nginx)**

```nginx
server {
    listen 443 ssl;
    server_name s3.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/s3.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/s3.yourdomain.com/privkey.pem;

    location / {
        proxy_pass http://localhost:9000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

**Option 2: MinIO Built-in TLS**

```bash
# Generate certificates
mkdir -p /opt/minio/certs
openssl req -new -x509 -days 365 -nodes \
  -out /opt/minio/certs/public.crt \
  -keyout /opt/minio/certs/private.key

# Update docker-compose.yml
volumes:
  - ./minio-data:/data
  - ./minio-certs:/root/.minio/certs
```

## Monitoring and Maintenance

### Disk Usage

```bash
# Check MinIO storage usage
mc admin info myminio

# Check specific bucket size
mc du myminio/urbit-media
```

### Health Checks

```bash
# MinIO health status
mc admin info myminio

# Check if responsive
curl http://localhost:9000/minio/health/live
```

### Backup Strategy

```bash
# Backup MinIO data
docker-compose stop minio
tar czf minio-backup-$(date +%Y%m%d).tar.gz ./minio-data
docker-compose start minio

# Offsite backup (to another S3-compatible storage)
mc mirror myminio/urbit-media s3-remote/urbit-media-backup
```

## Performance Optimization

### Caching

```yaml
# Add Nginx caching for S3 objects
proxy_cache_path /var/cache/nginx/s3 levels=1:2 keys_zone=s3_cache:10m max_size=10g inactive=60m;

server {
    location / {
        proxy_cache s3_cache;
        proxy_cache_valid 200 60m;
        proxy_pass http://localhost:9000;
    }
}
```

### Object Lifecycle

```bash
# Auto-delete objects older than 90 days (if needed)
mc ilm add myminio/urbit-temp \
  --expiry-days 90 \
  --prefix "temp/"
```

## Troubleshooting

### Issue: Cannot connect to MinIO

```bash
# Check container running
docker ps | grep minio

# Check logs
docker logs minio

# Verify port 9000/9001 not in use
sudo netstat -tulpn | grep -E '(9000|9001)'
```

### Issue: Access denied

- Verify access key/secret correct
- Check bucket policy (public vs private)
- Confirm user has required permissions

### Issue: Slow uploads

- Check network bandwidth
- Enable compression in app
- Use multipart uploads for large files (>100MB)

## Best Practices

1. **Dedicated access keys**: One key per application, not root
2. **Bucket policies**: Least privilege (public only if necessary)
3. **SSL/TLS**: Always use HTTPS for production
4. **Monitoring**: Check disk usage weekly
5. **Backups**: Automated daily backups to offsite location
6. **Lifecycle policies**: Auto-delete temporary files
7. **Versioning**: Enable for critical buckets (optional)
8. **Firewall**: Restrict port 9000/9001 to trusted IPs
9. **Resource limits**: Set storage quotas per bucket
10. **Documentation**: Document bucket purposes and access policies

## Reference

- MinIO documentation: https://min.io/docs/minio/linux/index.html
- S3 API compatibility: https://min.io/product/s3-compatibility
- MinIO client (mc): https://min.io/docs/minio/linux/reference/minio-mc.html

## Summary

MinIO provides self-hosted S3-compatible storage for Urbit ships, preventing pier bloat by offloading large files. Deploy via GroundSeg one-click activation or manual Docker configuration. Create buckets, configure access policies (public/private), and integrate with Urbit apps via S3 API. Security requires dedicated access keys, SSL/TLS, and least-privilege policies. Monitor disk usage, implement backup strategies, and optimize with caching and lifecycle policies.
