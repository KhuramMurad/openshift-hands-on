# Lab 4 – Configuring Applications for Reliability

---

> **Repository path:** `labs/lab-4/lab-4-README.md`

In this lab you will ensure your application remains healthy and responsive by configuring health probes, setting resource boundaries, and scaling applications manually and automatically. citeturn3file0

---

## Objectives
| # | Goal | Confirmed When |
|---|------|---------------|
| 1 | Implement liveness and readiness probes | `oc describe pod` shows probes configured |
| 2 | Configure resource requests and limits | `oc describe pod` displays configured `requests` and `limits` |
| 3 | Manually scale a Deployment | `oc get pods` shows the desired number of replicas |
| 4 | Configure autoscaling based on CPU utilization | `oc get hpa` shows HPA status and targets |

---

## Prerequisites
- A working OpenShift 4.x cluster with `oc` CLI logged in.  
- Permissions to create projects, deployments, and autoscalers.  
- Basic familiarity with Deployments and Pods (Lab 1 & 2).

---

## Quick‑Start Clone
```bash
# If not already cloned:
git clone https://github.com/KhuramMurad/openshift-hands-on.git
cd openshift-hands-on
mkdir -p labs/lab-4
```

---

## Step‑by‑Step Instructions

### 1  Create or Switch to Your Project
```bash
oc new-project demo-lab4 || oc project demo-lab4
```

### 2  Implement Liveness and Readiness Probes
Create a YAML file `my-app-deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
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
        image: my-app-image
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 15
          periodSeconds: 20
        readinessProbe:
          httpGet:
            path: /readiness
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 10
```
Apply it:
```bash
oc apply -f my-app-deployment.yaml
```
Verify probes:
```bash
oc describe pod -l app=my-app
``` citeturn3file0

### 3  Set Resource Requests and Limits
Create a YAML `my-pod-resources.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-pod
spec:
  containers:
  - name: resource-container
    image: my-container-image
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```
Apply:
```bash
oc apply -f my-pod-resources.yaml
```
Check:
```bash
oc describe pod resource-pod
``` citeturn3file0

### 4  Manually Scale the Deployment
```bash
# Increase to 5 replicas
oc scale deployment my-app --replicas=5
oc get pods -l app=my-app
```

### 5  Configure Horizontal Pod Autoscaler (HPA)
```bash
oc autoscale deployment my-app --cpu-percent=70 --min=3 --max=10
oc get hpa
```
Generate load to test (e.g., `curl` loops) and observe HPA adjusting replicas. citeturn3file0

### 6  Cleanup (optional)
```bash
oc delete project demo-lab4
```

---

## Verification Checklist
- [ ] Pods have liveness and readiness probes configured.  
- [ ] Pod `resource-pod` shows correct `requests` and `limits`.  
- [ ] Deployment `my-app` scales to 5 replicas manually.  
- [ ] HPA created, targets CPU at ~70%, and scales within bounds.

---

## Automation Script (optional)
Save as `lab4-configure.sh`:
```bash
#!/usr/bin/env bash
set -euo pipefail
NAMESPACE=demo-lab4
DEPLOY=demo-lab4
IMAGE=my-app-image

oc new-project "$NAMESPACE" || oc project "$NAMESPACE"

# Apply deployment with probes
cat <<EOF | oc apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
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
        image: $IMAGE
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 15
          periodSeconds: 20
        readinessProbe:
          httpGet:
            path: /readiness
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 10
EOF

# Set resources
oc apply -f my-pod-resources.yaml

# Manual scale
oc scale deployment my-app --replicas=5

# Autoscale
oc autoscale deployment my-app --cpu-percent=70 --min=3 --max=10

echo "Lab 4 complete: http://$(oc get route -l app=my-app -o jsonpath='{.items[0].spec.host}')"
```
Make it executable and run:
```bash
chmod +x lab4-configure.sh
./lab4-configure.sh
```

---

## Next Steps
Proceed to **[Lab 5 – Managing Application Updates and Rollbacks](../lab-5/README.md)** to learn update strategies, rollbacks, and image streams.

---
© 2025 OpenShift Hands‑On Labs • MIT License
