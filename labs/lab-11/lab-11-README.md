# Lab 11 – Managing Cluster Resources

---

> **Repository path:** `labs/lab-11/lab-11-README.md`

This README lives in `labs/lab-11/lab-11-README.md` and covers resource quotas and limits.

## Lab Overview
In this lab you will:
1. Set resource requests and limits on Pods.  
2. Configure ResourceQuotas and LimitRanges.  
3. Observe utilization with built-in monitoring. citeturn5file3

## Objectives
- Ensure fair resource distribution with quotas.  
- Prevent resource exhaustion via requests/limits.  
- Monitor consumption and adjust policies. citeturn5file3

## Step‑by‑Step Instructions
### 1. Requests & Limits
```bash
oc apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata: { name: resource-demo }
spec:
  containers:
  - name: demo
    image: busybox
    command: ["sleep","3600"]
    resources:
      requests: { cpu: "250m", memory: "64Mi" }
      limits:   { cpu: "500m", memory: "128Mi" }
EOF
```

### 2. ResourceQuota & LimitRange
```bash
oc apply -f - <<EOF
apiVersion: v1
kind: ResourceQuota
metadata: { name: project-quota }
spec:
  hard:
    requests.cpu: "1"
    requests.memory: "1Gi"
    limits.cpu: "2"
    limits.memory: "2Gi"
EOF

cat <<EOF | oc apply -f -
apiVersion: v1
kind: LimitRange
metadata: { name: project-limits }
spec:
  limits:
  - type: Container
    defaultRequest: { cpu: "100m", memory: "128Mi" }
    default:        { cpu: "200m", memory: "256Mi" }
EOF
```

### 3. Monitor Usage
```bash
oc adm top pods
```

## Cleanup (optional)
```bash
oc delete quota project-quota
oc delete limitrange project-limits
oc delete pod resource-demo
```

## Verification Checklist
- [ ] Pod created with specified requests/limits.  
- [ ] Quota prevents resource over-allocation.  
- [ ] `oc adm top pods` shows expected metrics.

## Next Steps
Proceed to **[Lab 12 – Security and Compliance in OpenShift](../lab-12/lab-12-README.md)**.
© 2025 OpenShift Hands‑On Labs • MIT License
