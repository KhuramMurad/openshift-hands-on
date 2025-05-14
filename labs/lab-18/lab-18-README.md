# Lab 18 – Performance Tuning and Optimization

---

> **Repository path:** `labs/lab-18/lab-18-README.md`

This README lives in `labs/lab-18/lab-18-README.md` and covers performance diagnostics, resource tuning, and cluster parameter optimization. citeturn6file7

## Lab Overview
In this lab you will:
1. Identify performance bottlenecks with `oc adm top`.
2. Adjust resource requests and limits.
3. Tune cluster parameters via ConfigMaps.

## Objectives
- Diagnose bottlenecks with monitoring tools.  
- Optimize container resource settings.  
- Apply cluster‑wide performance configurations. citeturn6file7

---

## Step‑by‑Step Instructions

### 1. Monitor Performance
```bash
oc adm top pods
oc adm top nodes
```

### 2. Tune Requests & Limits
```bash
oc apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: perf-demo
spec:
  containers:
  - name: demo
    image: busybox
    command: ["sleep","3600"]
    resources:
      requests:
        cpu: "250m"
        memory: "64Mi"
      limits:
        cpu: "500m"
        memory: "128Mi"
EOF
```

### 3. Cluster Parameter Tuning
```bash
oc apply -f - <<EOF
apiVersion: v1
kind: ConfigMap
metadata:
  name: cluster-config
data:
  feature-gates: HugePages=true
  max-pods: "110"
EOF
```

---

## Verification Checklist
- [ ] Metrics collected via `oc adm top`.  
- [ ] Pods running with updated requests/limits.  
- [ ] ConfigMap applied and parameters reflected.

## Next Steps
Proceed to **[Lab 19 – Continuous Integration/Continuous Deployment](../lab-19/lab-19-README.md)**.
