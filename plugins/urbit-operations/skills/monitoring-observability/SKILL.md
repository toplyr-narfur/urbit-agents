---
name: monitoring-observability
description: Production monitoring and observability for Urbit deployments using Prometheus, Grafana, Node Exporter, and Alertmanager with metrics collection, visualization, alerting, and capacity planning. Use when implementing monitoring, setting up dashboards, configuring alerts, tracking ship health, or planning capacity.
---

# Monitoring and Observability Skill

Production monitoring and observability for Urbit ship deployments using Prometheus, Grafana, and cloud-native monitoring solutions (2025).

## Overview

Comprehensive monitoring ensures ship availability, performance tracking, capacity planning, and rapid incident response through metrics collection, visualization, and alert

ing.

## Monitoring Stack Architecture

**Industry-standard stack (2025)**:
- **Node Exporter**: Collects host metrics (CPU, RAM, disk, network)
- **Prometheus**: Time-series database, metrics storage, alerting
- **Grafana**: Visualization, dashboards, multi-source aggregation
- **Alertmanager**: Alert routing, deduplication, notifications

**Benefits**:
- Open-source, zero licensing fees
- Scalable (monitor unlimited servers)
- Industry-proven (used by major tech companies)
- Rich ecosystem (1000+ Grafana dashboards)

---

## Quick Setup: Prometheus + Grafana + Node Exporter

### Step 1: Install Node Exporter (Target Server)

```bash
# Download Node Exporter (check latest version)
wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz

# Extract
tar xvfz node_exporter-1.7.0.linux-amd64.tar.gz

# Move binary
sudo mv node_exporter-1.7.0.linux-amd64/node_exporter /usr/local/bin/

# Create systemd service
sudo nano /etc/systemd/system/node_exporter.service
```

```ini
[Unit]
Description=Node Exporter
After=network.target

[Service]
Type=simple
User=node_exporter
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

```bash
# Create user
sudo useradd --no-create-home --shell /bin/false node_exporter

# Start and enable
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter

# Verify (should return metrics)
curl http://localhost:9100/metrics
```

### Step 2: Install Prometheus (Monitoring Server)

**Best practice**: Install Prometheus on separate server from Urbit ships.

```bash
# Download Prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.48.0/prometheus-2.48.0.linux-amd64.tar.gz

# Extract
tar xvfz prometheus-2.48.0.linux-amd64.tar.gz

# Move files
sudo mv prometheus-2.48.0.linux-amd64 /opt/prometheus
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus

# Create config
sudo nano /etc/prometheus/prometheus.yml
```

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'urbit-ship-1'
    static_configs:
      - targets: ['ship1-ip:9100']
        labels:
          alias: 'sampel-palnet'

  - job_name: 'urbit-ship-2'
    static_configs:
      - targets: ['ship2-ip:9100']
        labels:
          alias: 'another-planet'
```

```bash
# Create systemd service
sudo nano /etc/systemd/system/prometheus.service
```

```ini
[Unit]
Description=Prometheus
After=network.target

[Service]
Type=simple
User=prometheus
ExecStart=/opt/prometheus/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus/ \
  --web.console.templates=/opt/prometheus/consoles \
  --web.console.libraries=/opt/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

```bash
# Create user
sudo useradd --no-create-home --shell /bin/false prometheus

# Set permissions
sudo chown -R prometheus:prometheus /opt/prometheus /etc/prometheus /var/lib/prometheus

# Start and enable
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus

# Verify (web UI)
# Visit: http://monitoring-server-ip:9090
```

### Step 3: Install Grafana

```bash
# Add Grafana repository
sudo apt-get install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"

# Add GPG key
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -

# Install
sudo apt update
sudo apt install grafana -y

# Start and enable
sudo systemctl enable grafana-server
sudo systemctl start grafana-server

# Verify
# Visit: http://monitoring-server-ip:3000
# Default login: admin/admin (change immediately)
```

### Step 4: Configure Grafana Data Source

1. Log in to Grafana: `http://monitoring-server-ip:3000`
2. Navigate: **Configuration** → **Data Sources** → **Add data source**
3. Select: **Prometheus**
4. URL: `http://localhost:9090` (if Grafana and Prometheus on same server)
5. Click: **Save & Test** (should show green checkmark)

### Step 5: Import Dashboard

1. Navigate: **Create** → **Import**
2. Enter Dashboard ID: **1860** (Node Exporter Full)
3. Select Prometheus data source
4. Click: **Import**

**Result**: Rich dashboard showing CPU, RAM, disk, network metrics.

---

## Recommended Grafana Dashboards (2025)

| Dashboard | ID | Description |
|-----------|-----|-------------|
| **Node Exporter Full** | 1860 | Complete host metrics (CPU, RAM, disk, network) |
| **Node Exporter for Prometheus Dashboard** | 11074 | Alternative host metrics view |
| **Docker Monitoring** | 893 | Container metrics (for GroundSeg) |
| **Nginx Monitoring** | 12708 | Nginx reverse proxy metrics |
| **Prometheus 2.0 Overview** | 3662 | Prometheus itself monitoring |

### Import dashboards

```
1. https://grafana.com/grafana/dashboards/1860-node-exporter-full/
2. Navigate to Grafana → Create → Import
3. Enter dashboard ID → Load
4. Select Prometheus data source → Import
```

---

## Urbit-Specific Monitoring

### Ship Health Script

```bash
# /usr/local/bin/urbit-ship-health.sh
#!/bin/bash
set -euo pipefail

SHIP_NAME="sampel-palnet"
PIER_PATH="/home/urbit/$SHIP_NAME"

# Check if ship is running
if systemctl is-active --quiet urbit-$SHIP_NAME; then
    SHIP_STATUS=1  # Running
else
    SHIP_STATUS=0  # Stopped
fi

# Get pier size (GB)
PIER_SIZE=$(du -sb $PIER_PATH | awk '{print $1}')

# Write metrics to file for Node Exporter textfile collector
cat > /var/lib/node_exporter/textfile_collector/urbit_ship.prom <<EOF
# HELP urbit_ship_status Ship running status (1=running, 0=stopped)
# TYPE urbit_ship_status gauge
urbit_ship_status{ship="$SHIP_NAME"} $SHIP_STATUS

# HELP urbit_pier_size_bytes Pier size in bytes
# TYPE urbit_pier_size_bytes gauge
urbit_pier_size_bytes{ship="$SHIP_NAME"} $PIER_SIZE
EOF
```

```bash
# Create textfile collector directory
sudo mkdir -p /var/lib/node_exporter/textfile_collector

# Make script executable
sudo chmod +x /usr/local/bin/urbit-ship-health.sh

# Run every minute via cron
crontab -e
* * * * * /usr/local/bin/urbit-ship-health.sh
```

**Enable textfile collector**:
```bash
# Edit Node Exporter service
sudo nano /etc/systemd/system/node_exporter.service

# Modify ExecStart:
ExecStart=/usr/local/bin/node_exporter --collector.textfile.directory=/var/lib/node_exporter/textfile_collector

# Restart
sudo systemctl daemon-reload
sudo systemctl restart node_exporter
```

---

## Alerting with Prometheus Alertmanager

### Install Alertmanager

```bash
# Download
wget https://github.com/prometheus/alertmanager/releases/download/v0.26.0/alertmanager-0.26.0.linux-amd64.tar.gz

# Extract and install
tar xvfz alertmanager-0.26.0.linux-amd64.tar.gz
sudo mv alertmanager-0.26.0.linux-amd64 /opt/alertmanager

# Create config
sudo nano /etc/prometheus/alertmanager.yml
```

```yaml
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'alerts@yourdomain.com'
  smtp_auth_username: 'alerts@yourdomain.com'
  smtp_auth_password: 'your-app-password'

route:
  receiver: 'email-alerts'
  group_by: ['alertname', 'instance']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h

receivers:
  - name: 'email-alerts'
    email_configs:
      - to: 'admin@yourdomain.com'
        require_tls: true
```

```bash
# Create systemd service
sudo nano /etc/systemd/system/alertmanager.service
```

```ini
[Unit]
Description=Alertmanager
After=network.target

[Service]
Type=simple
User=prometheus
ExecStart=/opt/alertmanager/alertmanager \
  --config.file=/etc/prometheus/alertmanager.yml \
  --storage.path=/var/lib/alertmanager/

[Install]
WantedBy=multi-user.target
```

```bash
# Start
sudo mkdir /var/lib/alertmanager
sudo chown prometheus:prometheus /var/lib/alertmanager
sudo systemctl enable alertmanager
sudo systemctl start alertmanager
```

### Alert Rules for Urbit Ships

```bash
# Create alert rules
sudo nano /etc/prometheus/urbit_alerts.yml
```

```yaml
groups:
  - name: urbit_ship_alerts
    interval: 30s
    rules:
      - alert: ShipDown
        expr: urbit_ship_status == 0
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Urbit ship {{ $labels.ship }} is down"
          description: "Ship has been down for more than 5 minutes"

      - alert: HighCPU
        expr: 100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "High CPU usage on {{ $labels.instance }}"
          description: "CPU usage is above 80% for 10 minutes"

      - alert: HighMemory
        expr: (1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100 > 80
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "High memory usage on {{ $labels.instance }}"
          description: "Memory usage is above 80% for 10 minutes"

      - alert: LowDiskSpace
        expr: (1 - (node_filesystem_avail_bytes / node_filesystem_size_bytes)) * 100 > 85
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "Low disk space on {{ $labels.instance }}"
          description: "Disk usage is above 85% for 10 minutes"

      - alert: PierSizeGrowth
        expr: increase(urbit_pier_size_bytes[7d]) > 10737418240  # 10GB growth in 7 days
        labels:
          severity: info
        annotations:
          summary: "Rapid pier size growth on {{ $labels.ship }}"
          description: "Pier grew >10GB in past week, consider |pack or |meld"
```

```bash
# Update Prometheus config to load rules
sudo nano /etc/prometheus/prometheus.yml

# Add:
rule_files:
  - "/etc/prometheus/urbit_alerts.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']

# Reload Prometheus
sudo systemctl reload prometheus
```

---

## Cloud-Native Monitoring Integration

### DigitalOcean Monitoring

**Built-in**: Enabled by default on all Droplets

**Features**:
- CPU, memory, disk, bandwidth graphs
- Alert policies (email/SMS)
- Free, no additional cost

**Configure alert**:
1. Navigate: DigitalOcean Dashboard → Monitoring → Alerts
2. Click: **Create Alert Policy**
3. Configure: CPU > 80% for 10 minutes
4. Notification: Email to admin@yourdomain.com

### Linode Longview

**Installation**:
```bash
# Get client command from Linode dashboard
curl -s https://lv.linode.com/<your-longview-id> | sudo bash
```

**Features**:
- Free tier: 10-minute resolution
- Pro tier: 1-minute resolution, longer retention
- Process-level insights
- Real-time monitoring

### AWS CloudWatch (Lightsail)

```bash
# Install CloudWatch agent
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i amazon-cloudwatch-agent.deb

# Configure wizard
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard

# Start agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -s \
  -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json
```

**Features**:
- Metrics: CPU, disk, memory (requires agent)
- Alarms: Email notifications via SNS
- Logs: Centralized logging
- Integration with AWS ecosystem

---

## Log Aggregation

### Loki + Promtail (Lightweight Alternative)

```bash
# Install Loki (log aggregation)
wget https://github.com/grafana/loki/releases/download/v2.9.3/loki-linux-amd64.zip
unzip loki-linux-amd64.zip
sudo mv loki-linux-amd64 /usr/local/bin/loki

# Install Promtail (log collector)
wget https://github.com/grafana/loki/releases/download/v2.9.3/promtail-linux-amd64.zip
unzip promtail-linux-amd64.zip
sudo mv promtail-linux-amd64 /usr/local/bin/promtail

# Basic Promtail config
sudo nano /etc/promtail-config.yml
```

```yaml
server:
  http_listen_port: 9080

clients:
  - url: http://localhost:3100/loki/api/v1/push

scrape_configs:
  - job_name: system
    static_configs:
      - targets:
          - localhost
        labels:
          job: varlogs
          __path__: /var/log/*log

  - job_name: urbit
    static_configs:
      - targets:
          - localhost
        labels:
          job: urbit
          __path__: /home/urbit/*/. urb/put/*.log
```

**Add Loki to Grafana**:
1. Configuration → Data Sources → Add Loki
2. URL: `http://localhost:3100`
3. Explore logs in Grafana

---

## Best Practices

1. **Separate monitoring server**: Don't monitor Urbit ships from same VPS
2. **Multiple data sources**: Combine Prometheus + cloud monitoring
3. **Alert fatigue**: Set appropriate thresholds (avoid false positives)
4. **Retention**: 15 days Prometheus, longer in cloud storage
5. **Backup Grafana**: Export dashboards regularly
6. **Security**: Firewall Prometheus/Grafana ports (use SSH tunnel or VPN)
7. **Testing**: Regularly test alert delivery
8. **Documentation**: Document dashboard URLs, alert contacts

---

## Monitoring Checklist

- [ ] Node Exporter installed on all ships
- [ ] Prometheus collecting metrics
- [ ] Grafana dashboards configured
- [ ] Alertmanager configured with email
- [ ] Alerts defined (CPU, RAM, disk, ship down)
- [ ] Cloud monitoring enabled (DO/Linode/AWS)
- [ ] Urbit-specific metrics (pier size, ship status)
- [ ] Log aggregation configured (Loki or CloudWatch)
- [ ] Alert testing completed
- [ ] Documentation created (dashboard URLs, alert contacts)

---

## Reference

- Prometheus: https://prometheus.io/docs/
- Grafana: https://grafana.com/docs/
- Node Exporter: https://github.com/prometheus/node_exporter
- Grafana dashboards: https://grafana.com/grafana/dashboards/

---

## Summary

Production monitoring for Urbit ships uses Prometheus (time-series database), Grafana (visualization), and Node Exporter (metrics collection) as industry-standard stack (open-source, zero cost). Monitor host metrics (CPU, RAM, disk, network), Urbit-specific metrics (pier size, ship status), and implement alerting via Alertmanager (email/SMS). Cloud-native integration supplements with DigitalOcean Monitoring, Linode Longview, or AWS CloudWatch. Best practices: separate monitoring server, appropriate alert thresholds, log aggregation with Loki, and regular testing of alert delivery.
