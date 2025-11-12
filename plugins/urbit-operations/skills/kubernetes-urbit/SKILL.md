---
name: kubernetes-urbit
description: Production Kubernetes deployment for Urbit ships using StatefulSets, persistent volumes, LoadBalancers, and service mesh integration with automatic failover and centralized management for enterprise fleets (25+ ships). Use when deploying Kubernetes fleets, architecting enterprise infrastructure, implementing high availability, or managing mission-critical Urbit deployments.
---

# Kubernetes Urbit Deployment Skill

Production Kubernetes deployment for Urbit ships using StatefulSets, persistent volumes, and service mesh integration (2025).

## Overview

Kubernetes (K8s) for Urbit provides enterprise-grade orchestration with automatic failover, persistent storage, and horizontal infrastructure scaling. Despite Urbit ships being single-instance applications, K8s StatefulSets enable reliable hosting.

## Why Kubernetes for Urbit?

**Advantages**:
- **High availability**: Automatic pod rescheduling on node failure
- **Persistent storage**: Volumes survive pod restarts
- **Resource management**: CPU/memory limits prevent resource hogging
- **Automated recovery**: Self-healing infrastructure
- **Centralized management**: Manage 10+ ships from single control plane

**Challenges**:
- **Ames UDP**: Exposing UDP port 34543 requires LoadBalancer or NodePort
- **Single-instance constraint**: Ships cannot be horizontally scaled (n=1 replicas only)
- **Complexity**: Kubernetes overhead for small deployments (<5 ships)

**When to use**: Enterprise deployments (25+ ships), mission-critical applications requiring 99.9%+ uptime.

## Architecture

```
User → LoadBalancer → Service → StatefulSet → PersistentVolume
                                      ↓
                                  Urbit Pod (ship)
```

**Key components**:
- **StatefulSet**: Manages ship pod with stable network identity
- **PersistentVolumeClaim**: Stores pier data (survives pod deletion)
- **Service**: Exposes ship (HTTP + Ames UDP)
- **LoadBalancer**: Routes external traffic

## Kubernetes Manifest

### StatefulSet

```yaml
# urbit-statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: sampel-palnet
  namespace: urbit
spec:
  serviceName: "sampel-palnet"
  replicas: 1  # MUST be 1 (ships are single-instance)
  selector:
    matchLabels:
      app: urbit
      ship: sampel-palnet
  template:
    metadata:
      labels:
        app: urbit
        ship: sampel-palnet
    spec:
      containers:
      - name: urbit
        image: tloncorp/urbit:latest
        resources:
          requests:
            memory: "4Gi"
            cpu: "500m"
          limits:
            memory: "6Gi"
            cpu: "1000m"
        volumeMounts:
        - name: pier-storage
          mountPath: /urbit
        ports:
        - containerPort: 8080
          name: http
          protocol: TCP
        - containerPort: 34543
          name: ames
          protocol: UDP
        env:
        - name: SHIP_NAME
          value: "sampel-palnet"
        livenessProbe:
          httpGet:
            path: /
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 30
        readinessProbe:
          httpGet:
            path: /
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
  volumeClaimTemplates:
  - metadata:
      name: pier-storage
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "fast-ssd"
      resources:
        requests:
          storage: 100Gi
```

### Service (LoadBalancer)

```yaml
# urbit-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: sampel-palnet-lb
  namespace: urbit
spec:
  type: LoadBalancer
  selector:
    app: urbit
    ship: sampel-palnet
  ports:
    - name: http
      port: 80
      targetPort: 8080
      protocol: TCP
    - name: https
      port: 443
      targetPort: 8080
      protocol: TCP
    - name: ames
      port: 34543
      targetPort: 34543
      protocol: UDP
```

### Persistent Volume (manual provisioning example)

```yaml
# pier-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: sampel-palnet-pier
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: fast-ssd
  hostPath:
    path: /mnt/urbit/piers/sampel-palnet
```

## Deployment

```bash
# Create namespace
kubectl create namespace urbit

# Apply manifests
kubectl apply -f urbit-statefulset.yaml
kubectl apply -f urbit-service.yaml

# Check status
kubectl get pods -n urbit
kubectl get svc -n urbit

# Get LoadBalancer IP
kubectl get svc sampel-palnet-lb -n urbit -o jsonpath='{.status.loadBalancer.ingress[0].ip}'

# View logs
kubectl logs -n urbit sampel-palnet-0 -f

# Access dojo (exec into pod)
kubectl exec -it -n urbit sampel-palnet-0 -- /bin/bash
urbit attach /urbit/sampel-palnet
```

## Ames UDP Challenge

**Problem**: Kubernetes LoadBalancer may not preserve source IP for UDP traffic, breaking Ames protocol.

**Solutions**:

**1. NodePort (simplest)**:
```yaml
spec:
  type: NodePort
  externalTrafficPolicy: Local  # Preserves source IP
  ports:
    - name: ames
      port: 34543
      nodePort: 34543  # Expose on all cluster nodes
      protocol: UDP
```

**2. LoadBalancer with externalTrafficPolicy**:
```yaml
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local  # Preserves source IP, disables cross-node load balancing
```

**3. MetalLB (bare-metal clusters)**:
```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.12/config/manifests/metallb-native.yaml

# Configure IP pool
kubectl apply -f - <<EOF
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.1.240-192.168.1.250
EOF
```

## Helm Chart (Production Template)

```bash
# Install via Helm
helm repo add urbit https://charts.urbit.example
helm install sampel-palnet urbit/urbit-ship \
  --set ship.name=sampel-palnet \
  --set ship.keyfile=$(base64 < keyfile.key) \
  --set resources.memory=4Gi \
  --set storage.size=100Gi \
  --namespace urbit
```

## Monitoring Integration

### ServiceMonitor (Prometheus Operator)

```yaml
# urbit-servicemonitor.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: urbit-fleet
  namespace: urbit
spec:
  selector:
    matchLabels:
      app: urbit
  endpoints:
  - port: metrics  # Requires Node Exporter sidecar
    interval: 30s
```

## Best Practices

1. **StatefulSet replicas: 1**: Ships cannot be scaled horizontally
2. **PersistentVolumes**: Use dynamic provisioning (CSI drivers)
3. **Storage class**: SSD-backed (fast-ssd, gp3, premium-ssd)
4. **Resource limits**: Prevent OOM kills (memory: 6Gi limit, 4Gi request)
5. **Liveness/readiness probes**: HTTP health checks on port 8080
6. **externalTrafficPolicy: Local**: For Ames UDP (preserves source IP)
7. **Namespace isolation**: Separate namespace per environment (prod, staging)
8. **RBAC**: Least-privilege service accounts
9. **Network policies**: Restrict pod-to-pod communication
10. **Backup strategy**: Automate PV snapshots (VolumeSnapshot API)

## Scaling Fleet (25+ Ships)

```bash
# Deploy multiple ships (separate StatefulSets)
for ship in ship1 ship2 ship3; do
    kubectl apply -f urbit-statefulset.yaml \
        --namespace urbit \
        --set name=$ship
done

# Or use Helm with values file
helm install -f ships.yaml urbit/urbit-fleet
```

**ships.yaml**:
```yaml
ships:
  - name: ship1
    storage: 100Gi
  - name: ship2
    storage: 100Gi
  - name: ship3
    storage: 100Gi
```

## Disaster Recovery

```bash
# Snapshot PVC (requires VolumeSnapshot CRD)
kubectl apply -f - <<EOF
apiVersion: snapshot.storage.k8s.io/v1
kind: VolumeSnapshot
metadata:
  name: sampel-palnet-snapshot
  namespace: urbit
spec:
  volumeSnapshotClassName: csi-snapclass
  source:
    persistentVolumeClaimName: pier-storage-sampel-palnet-0
EOF

# Restore from snapshot
kubectl apply -f - <<EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pier-storage-restored
  namespace: urbit
spec:
  dataSource:
    name: sampel-palnet-snapshot
    kind: VolumeSnapshot
    apiGroup: snapshot.storage.k8s.io
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Gi
EOF
```

## Troubleshooting

**Pod stuck in Pending**:
- Check PVC status: `kubectl get pvc -n urbit`
- Verify storage class exists: `kubectl get sc`
- Check node resources: `kubectl describe node`

**Ames not working**:
- Verify UDP port exposed: `kubectl get svc -n urbit`
- Check externalTrafficPolicy: `kubectl get svc -o yaml`
- Test UDP: `nc -u LoadBalancer-IP 34543`

**Pod crashes (OOMKilled)**:
- Increase memory limit: `resources.limits.memory: "8Gi"`
- Run |pack in dojo before restarting

## Reference

- Kubernetes StatefulSets: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
- UrbitHost architecture: https://urbit.org/blog/urbithost-interview
- MetalLB: https://metallb.universe.tf/

## Summary

Kubernetes deployment for Urbit uses StatefulSets (stable network identity, single replica), PersistentVolumes (pier storage persistence), and LoadBalancer services (HTTP + Ames UDP exposure). Key challenge: Ames UDP requires externalTrafficPolicy: Local for source IP preservation. Production setup includes resource limits (4-6Gi RAM, 0.5-1 CPU), liveness/readiness probes, and dynamic volume provisioning. Best for enterprise fleets (25+ ships) requiring 99.9%+ uptime with automatic failover. Smaller deployments (<10 ships) better served by GroundSeg or VPS due to Kubernetes complexity overhead. Disaster recovery via VolumeSnapshots, monitoring via ServiceMonitor (Prometheus Operator).
