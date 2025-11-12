---
name: setup-monitoring
description: Automated monitoring stack setup for Urbit fleets with Prometheus, Grafana, alerting, and cloud-native integration
agents:
  - groundseg-operator
skills:
  - monitoring-observability
  - fleet-operations
  - performance-optimization
---

# Setup Monitoring

Automated deployment of production monitoring infrastructure for Urbit ship fleets using Prometheus, Grafana, and cloud-native monitoring.

## Overview

Establishes comprehensive monitoring for Urbit deployments with metrics collection, visualization, alerting, and log aggregation.

## Configuration Options

**Required**:
- Monitoring target: Single ship, multi-ship fleet, or GroundSeg deployment
- Alert email: For notifications

**Optional**:
- Cloud provider monitoring integration (DigitalOcean/Linode/AWS)
- Log aggregation (Loki)
- Custom dashboards
- Slack/PagerDuty integration

## Workflow

### Phase 1: Architecture Planning

**Recommended setup**:
- **Separate monitoring server**: Don't monitor ships from same VPS
- **Monitoring server sizing**: 2GB RAM, 20GB disk for <10 ships
- **Network access**: Monitoring server → ships (port 9100)

###Phase 2: Install Node Exporter (All Ships)

```bash
# On each ship server:
wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz
tar xvfz node_exporter-1.7.0.linux-amd64.tar.gz
sudo mv node_exporter-1.7.0.linux-amd64/node_exporter /usr/local/bin/

# Create systemd service
sudo tee /etc/systemd/system/node_exporter.service > /dev/null <<EOF
[Unit]
Description=Node Exporter
After=network.target

[Service]
Type=simple
User=node_exporter
ExecStart=/usr/local/bin/node_exporter --collector.textfile.directory=/var/lib/node_exporter/textfile_collector

[Install]
WantedBy=multi-user.target
EOF

# Create user and directories
sudo useradd --no-create-home --shell /bin/false node_exporter
sudo mkdir -p /var/lib/node_exporter/textfile_collector

# Start service
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter

# Verify
curl http://localhost:9100/metrics
```

### Phase 3: Install Urbit-Specific Metrics Collector

```bash
# Create Urbit health monitoring script
sudo tee /usr/local/bin/urbit-metrics.sh > /dev/null <<'EOF'
#!/bin/bash
set -euo pipefail

SHIP_NAME="${1:-sampel-palnet}"
PIER_PATH="/home/urbit/$SHIP_NAME"
METRICS_FILE="/var/lib/node_exporter/textfile_collector/urbit.prom"

# Ship status
if systemctl is-active --quiet urbit-$SHIP_NAME; then
    SHIP_STATUS=1
else
    SHIP_STATUS=0
fi

# Pier size (bytes)
if [ -d "$PIER_PATH" ]; then
    PIER_SIZE=$(du -sb "$PIER_PATH" 2>/dev/null | awk '{print $1}' || echo 0)
else
    PIER_SIZE=0
fi

# Write Prometheus metrics
cat > "$METRICS_FILE" <<METRICS
# HELP urbit_ship_status Ship running status (1=running, 0=stopped)
# TYPE urbit_ship_status gauge
urbit_ship_status{ship="$SHIP_NAME"} $SHIP_STATUS

# HELP urbit_pier_size_bytes Pier size in bytes
# TYPE urbit_pier_size_bytes gauge
urbit_pier_size_bytes{ship="$SHIP_NAME"} $PIER_SIZE
METRICS
EOF

sudo chmod +x /usr/local/bin/urbit-metrics.sh

# Add to cron (run every minute)
(crontab -l 2>/dev/null; echo "* * * * * /usr/local/bin/urbit-metrics.sh sampel-palnet") | crontab -
```

### Phase 4: Install Prometheus (Monitoring Server)

```bash
# On monitoring server:
wget https://github.com/prometheus/prometheus/releases/download/v2.48.0/prometheus-2.48.0.linux-amd64.tar.gz
tar xvfz prometheus-2.48.0.linux-amd64.tar.gz
sudo mv prometheus-2.48.0.linux-amd64 /opt/prometheus

# Create config directory
sudo mkdir -p /etc/prometheus /var/lib/prometheus

# Create configuration
sudo tee /etc/prometheus/prometheus.yml > /dev/null <<'EOF'
global:
  scrape_interval: 15s
  evaluation_interval: 15s

rule_files:
  - "/etc/prometheus/urbit_alerts.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'urbit-ships'
    static_configs:
      - targets:
          - 'ship1-ip:9100'
          - 'ship2-ip:9100'
        labels:
          environment: 'production'
EOF

# Create user
sudo useradd --no-create-home --shell /bin/false prometheus
sudo chown -R prometheus:prometheus /opt/prometheus /etc/prometheus /var/lib/prometheus

# Create systemd service
sudo tee /etc/systemd/system/prometheus.service > /dev/null <<EOF
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
EOF

# Start Prometheus
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus

# Verify
curl http://localhost:9090/-/healthy
```

### Phase 5: Install Grafana

```bash
# Add Grafana repository
sudo apt-get install -y software-properties-common
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"

# Install
sudo apt update
sudo apt install grafana -y

# Start and enable
sudo systemctl enable grafana-server
sudo systemctl start grafana-server

# Access at: http://monitoring-server:3000
# Default credentials: admin/admin (change immediately!)
```

### Phase 6: Configure Grafana

1. **Add Prometheus data source**:
   - Navigate: Configuration → Data Sources → Add Prometheus
   - URL: `http://localhost:9090`
   - Save & Test

2. **Import dashboards**:
   - Dashboard ID 1860 (Node Exporter Full)
   - Dashboard ID 893 (Docker monitoring - for GroundSeg)

3. **Create custom Urbit dashboard**:
   - Add panel: urbit_ship_status (gauge showing up/down)
   - Add panel: urbit_pier_size_bytes (graph showing growth over time)
   - Add panel: node_memory_MemAvailable_bytes (RAM usage)
   - Add panel: rate(node_cpu_seconds_total[5m]) (CPU usage)

### Phase 7: Configure Alerting

```bash
# Install Alertmanager
wget https://github.com/prometheus/alertmanager/releases/download/v0.26.0/alertmanager-0.26.0.linux-amd64.tar.gz
tar xvfz alertmanager-0.26.0.linux-amd64.tar.gz
sudo mv alertmanager-0.26.0.linux-amd64 /opt/alertmanager

# Configure Alertmanager
sudo tee /etc/prometheus/alertmanager.yml > /dev/null <<EOF
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'alerts@yourdomain.com'
  smtp_auth_username: 'alerts@yourdomain.com'
  smtp_auth_password: 'your-app-password'

route:
  receiver: 'email-alerts'
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h

receivers:
  - name: 'email-alerts'
    email_configs:
      - to: 'admin@yourdomain.com'
        require_tls: true
EOF

# Create systemd service
sudo tee /etc/systemd/system/alertmanager.service > /dev/null <<EOF
[Unit]
Description=Alertmanager
After=network.target

[Service]
Type=simple
User=prometheus
ExecStart=/opt/alertmanager/alertmanager \
  --config.file=/etc/prometheus/alertmanager.yml

[Install]
WantedBy=multi-user.target
EOF

# Start Alertmanager
sudo mkdir -p /var/lib/alertmanager
sudo chown prometheus:prometheus /var/lib/alertmanager
sudo systemctl enable alertmanager
sudo systemctl start alertmanager
```

### Phase 8: Define Alert Rules

```bash
# Create alert rules
sudo tee /etc/prometheus/urbit_alerts.yml > /dev/null <<'EOF'
groups:
  - name: urbit_alerts
    interval: 30s
    rules:
      - alert: ShipDown
        expr: urbit_ship_status == 0
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Urbit ship {{ $labels.ship }} is down"
          description: "Ship {{ $labels.ship }} has been down for 5 minutes"

      - alert: HighCPU
        expr: 100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "High CPU on {{ $labels.instance }}"

      - alert: HighMemory
        expr: (1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100 > 80
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "High memory on {{ $labels.instance }}"

      - alert: LowDiskSpace
        expr: (1 - (node_filesystem_avail_bytes / node_filesystem_size_bytes)) * 100 > 85
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "Low disk space on {{ $labels.instance }}"

      - alert: RapidPierGrowth
        expr: increase(urbit_pier_size_bytes[7d]) > 10737418240
        labels:
          severity: info
        annotations:
          summary: "Pier {{ $labels.ship }} grew >10GB in 7 days"
          description: "Consider running |pack or |meld"
EOF

# Reload Prometheus
sudo systemctl reload prometheus
```

### Phase 9: Cloud Monitoring Integration

**DigitalOcean**:
- Enable in Droplet settings (automatic, free)
- Configure alert policies (CPU >80%, disk >85%)

**Linode Longview**:
```bash
curl -s https://lv.linode.com/<your-id> | sudo bash
```

**AWS CloudWatch** (Lightsail):
```bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i amazon-cloudwatch-agent.deb
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```

### Phase 10: Verification and Testing

**Check metrics collection**:
```bash
# Prometheus targets
curl http://localhost:9090/api/v1/targets | jq .

# Verify metrics
curl http://localhost:9090/api/v1/query?query=urbit_ship_status | jq .
```

**Test alerts**:
```bash
# Stop a ship (trigger ShipDown alert)
sudo systemctl stop urbit-ship

# Wait 5 minutes, check Alertmanager
curl http://localhost:9093/api/v2/alerts | jq .

# Verify email received
# Start ship back up
sudo systemctl start urbit-ship
```

## Best Practices

1. **Separate monitoring server**: Don't monitor from same VPS as ships
2. **Firewall**: Restrict Prometheus/Grafana access (SSH tunnel or VPN)
3. **Backup dashboards**: Export Grafana JSON regularly
4. **Alert tuning**: Adjust thresholds to avoid false positives
5. **Retention**: 15 days Prometheus, longer in object storage if needed
6. **Security**: Change default Grafana password immediately
7. **Testing**: Regularly test alert delivery
8. **Documentation**: Document dashboard URLs, alert contacts

## Monitoring Checklist

- [ ] Node Exporter on all ships
- [ ] Urbit metrics script running (cron)
- [ ] Prometheus collecting metrics
- [ ] Grafana dashboards configured
- [ ] Alertmanager email working
- [ ] Alert rules defined (ship down, CPU, RAM, disk)
- [ ] Cloud monitoring integrated (if applicable)
- [ ] Alerts tested (trigger and verify)
- [ ] Team trained on dashboard access
- [ ] Documentation created

## Troubleshooting

**Metrics not appearing**:
- Check Node Exporter running: `systemctl status node_exporter`
- Verify firewall allows port 9100
- Check Prometheus targets: http://localhost:9090/targets

**Alerts not sending**:
- Verify Alertmanager running: `systemctl status alertmanager`
- Check SMTP credentials
- Test email with: `amtool alert add test severity=warning`

**Grafana not loading dashboards**:
- Verify Prometheus data source configured
- Check Prometheus connectivity from Grafana
- Review browser console for errors

## Cross-References

- **monitoring-observability**: Detailed monitoring architecture
- **fleet-operations**: Multi-ship fleet management
- **performance-optimization**: System tuning based on metrics

## Summary

Monitoring setup for Urbit fleets deploys Node Exporter (metrics collection), Prometheus (time-series database), Grafana (visualization), and Alertmanager (notifications). Urbit-specific metrics track ship status and pier size growth. Alert rules cover ship downtime (5min threshold), high CPU/RAM (80%, 10min), low disk (85%, 10min), and rapid pier growth (>10GB/week). Best practices: separate monitoring server, firewall restrictions, regular alert testing, and cloud monitoring integration (DigitalOcean/Linode/AWS). Typical setup time: 2-4 hours for single monitoring server covering multiple ships.
