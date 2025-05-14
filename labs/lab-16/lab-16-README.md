# Lab 16 – High Availability and Disaster Recovery

---

> **Repository path:** `labs/lab-16/lab-16-README.md`

This README lives in `labs/lab-16/lab-16-README.md` and guides you through HA deployments, node failure simulation, and ETCD backups. citeturn6file13

## Lab Overview
In this lab you will:
1. Configure a highly available application.
2. Simulate a node failure.
3. Practice ETCD backup and restore.

## Objectives
- Eliminate single points of failure with replica deployments.  
- Test resilience by draining nodes.  
- Secure cluster data with ETCD snapshots. citeturn6file13

---

## Step‑by‑Step Instructions

### 1. Highly Available Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image:latest
```
```bash
oc apply -f ha-deploy.yaml
```

### 2. Simulate Node Failure
```bash
oc adm drain <node-name> --force --ignore-daemonsets --delete-local-data
```

### 3. ETCD Snapshot Backup
```bash
etcdctl snapshot save snapshot.db
etcdctl snapshot restore snapshot.db
```

---

## Verification Checklist
- [ ] Pods distributed across nodes.
- [ ] Node drained and workloads rescheduled.
- [ ] ETCD snapshot created and restored successfully.

## Next Steps
Proceed to **[Lab 17 – Custom Resource Definitions and Operators](../lab-17/lab-17-README.md)**.
