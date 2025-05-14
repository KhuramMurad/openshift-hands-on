# Lab 10 – Stateful Applications with StatefulSets

---

> **Repository path:** `labs/lab-10/lab-10-README.md`

This README lives in `labs/lab-10/lab-10-README.md` and teaches stable network IDs and storage.

## Lab Overview
In this lab you will:
1. Deploy a StatefulSet and explore its Pod identity.  
2. Simulate updates and rollbacks on a StatefulSet.  
3. Manage associated PVCs for persistent volumes. citeturn5file2

## Objectives
- Use StatefulSets for ordered, stable pod deployment.  
- Handle rolling updates and rollbacks for stateful apps.  
- Manage PVs/PVCs bound to StatefulSets. citeturn5file2

## Step‑by‑Step Instructions
### 1. Deploy a StatefulSet
```bash
cat <<EOF | oc apply -f -
apiVersion: apps/v1
kind: StatefulSet
metadata: { name: web }
spec:
  serviceName: "nginx"
  replicas: 3
  selector: { matchLabels: { app: nginx } }
  template:
    metadata: { labels: { app: nginx } }
    spec:
      containers: [{ name: nginx, image: nginx:1.21, ports:[{containerPort:80}] }]
  volumeClaimTemplates:
  - metadata: { name: www }
    spec:
      accessModes: ["ReadWriteOnce"]
      resources: { requests: { storage: 1Gi } }
EOF
```

### 2. Update and Rollback
```bash
oc set image statefulset/web nginx=nginx:1.22
oc rollout undo statefulset/web
```

### 3. Inspect PVCs
```bash
oc get pvc -l app=nginx
```

## Cleanup (optional)
```bash
oc delete statefulset web
oc delete svc nginx
oc delete pvc www-web-0 www-web-1 www-web-2
```

## Verification Checklist
- [ ] Pods `web-0`, `web-1`, `web-2` exist with stable hostnames.  
- [ ] PVCs bound and data persists across restarts.  
- [ ] Rollback restores previous image correctly.

## Next Steps
Proceed to **[Lab 11 – Managing Cluster Resources](../lab-11/lab-11-README.md)**.
© 2025 OpenShift Hands‑On Labs • MIT License
