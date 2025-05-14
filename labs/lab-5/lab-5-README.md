# Lab 5 – Managing Application Updates and Rollbacks

---

> **Repository path:** `labs/lab-5/lab-5-README.md`

In this lab you’ll master updating live applications, safely rolling back failed deployments, and automating updates via ImageStreams and triggers.

---

## Lab Overview
Continuous delivery requires smooth rollouts and the ability to recover quickly from failures. In this exercise you will:

1. Update a deployment to a new container image and monitor its rollout.  
2. Simulate a failed update and perform a rollback.  
3. Create an ImageStream and configure automatic deployment triggers based on image changes. citeturn4file0  

---

## Objectives
| # | Goal | Confirmed When |
|---|------|----------------|
| 1 | Update deployment image and watch rollout | `oc rollout status deployment/my-app` shows success |
| 2 | Simulate failure and rollback | `oc rollout undo deployment/my-app` restores previous version |
| 3 | Create and verify an ImageStream | `oc get is/my-app-image-stream` exists |
| 4 | Configure and test image-change triggers | New push to ImageStream auto-triggers deployment citeturn4file0 |

---

## Prerequisites
- A running OpenShift 4.x cluster and `oc` CLI logged in  
- Permission to create Projects, Deployments, ImageStreams, and DeploymentConfigs  
- A sample application deployed as `my-app` in your current project  

---

## Quick‑Start Clone
```bash
# Clone or update your workspace if needed:
git clone https://github.com/KhuramMurad/openshift-hands-on.git
cd openshift-hands-on
mkdir -p labs/lab-5
```

---

## Step‑by‑Step Instructions

### 1  Create / Switch to Project
```bash
oc new-project demo-lab5 || oc project demo-lab5
```

### 2  Update the Application Image & Monitor Rollout
```bash
# Use a new version tag
oc set image deployment/my-app my-app-container=new-image:tag

# Watch the rollout status
oc rollout status deployment/my-app
```

### 3  Simulate a Failed Deployment & Rollback
```bash
# Push a bad image to trigger failure
oc set image deployment/my-app my-app-container=nonexistent-image:tag

# Observe failure (e.g., CrashLoopBackOff) in pods
oc get pods -w

# Roll back to the last known-good revision
oc rollout undo deployment/my-app
```

### 4  Create an ImageStream and DeploymentConfig with Triggers
Save this as `my-app-imagestream.yaml`:
```yaml
apiVersion: image.openshift.io/v1
kind: ImageStream
metadata:
  name: my-app-image-stream
spec:
  lookupPolicy:
    local: false
  tags:
  - name: latest
    from:
      kind: DockerImage
      name: my-app-container:latest
```
```bash
oc apply -f my-app-imagestream.yaml
```

Save a DeploymentConfig `my-app-dc.yaml`:
```yaml
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: my-app
spec:
  replicas: 1
  triggers:
  - type: ImageChange
    imageChangeParams:
      automatic: true
      containerNames:
      - my-app-container
      from:
        kind: ImageStreamTag
        name: 'my-app-image-stream:latest'
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: image-registry.openshift-image-registry.svc:5000/$(oc project -q)/my-app-image-stream:latest
        ports:
        - containerPort: 8080
```
```bash
oc apply -f my-app-dc.yaml
```

### 5  Test Automated Trigger
```bash
# Simulate pushing a new image tag
oc import-image my-app-image-stream:latest --from=my-app-container:another-tag --confirm

# Watch that the DeploymentConfig auto-rolls out
oc rollout status dc/my-app
```

### 6  Cleanup (optional)
```bash
oc delete project demo-lab5
```

---

## Verification Checklist
- [ ] `oc get deployment/my-app` shows the new-image:tag revision.  
- [ ] Rollback restores the previous image.  
- [ ] `oc get is/my-app-image-stream` lists tag “latest”.  
- [ ] `oc get dc/my-app` shows an ImageChange trigger and automatic rollouts. citeturn4file0  

---

## Automation Script (optional)
Save as `lab5-update-rollback.sh`:
```bash
#!/usr/bin/env bash
set -euo pipefail
NAMESPACE=demo-lab5
APP=my-app
STREAM=my-app-image-stream
IMAGE=my-app-container:latest

oc new-project "$NAMESPACE" || oc project "$NAMESPACE"

# Update image and monitor
oc set image deployment/"$APP" "$APP-container"="$IMAGE"
oc rollout status deployment/"$APP"

# Simulate failure and rollback
oc set image deployment/"$APP" "$APP-container"=bad-image:tag || true
echo "Rolling back…"
oc rollout undo deployment/"$APP"

# Setup imagestream and DC triggers
oc apply -f my-app-imagestream.yaml
oc apply -f my-app-dc.yaml

# Simulate import and auto-trigger
oc import-image "$STREAM:latest" --from=my-app-container:new-tag --confirm
oc rollout status dc/"$APP"

echo "Lab 5 complete!"
```
```bash
chmod +x lab5-update-rollback.sh
./lab5-update-rollback.sh
```

---

## Next Steps
Continue to **[Lab 6 – Troubleshooting Common Container, Pod, and Cluster Events](../lab-6/README.md)** to practice diagnosing and resolving issues in your cluster.

---
© 2025 OpenShift Hands‑On Labs • MIT License
