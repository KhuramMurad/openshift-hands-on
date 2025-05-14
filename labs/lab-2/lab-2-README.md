# Lab 2 – Deploying Applications on OpenShift

---

> **Repository path:** `labs/lab-2/README.md`
>
> You will deploy a sample Node.js application two different ways (from a public container image and from a parameterised OpenShift template), manage its replica count, and expose it externally with a Service + Route.

---

## Lab Overview
This lab builds on the cluster‑basics from Lab 1 and focuses on **application deployment workflows**. You will:

1. Deploy an application directly from a container image. 
2. Deploy the same application via an OpenShift template for repeatability. 
3. Scale the deployment and observe ReplicaSets. 
4. Create a Service and expose the app to the outside world with a Route. 

These tasks map to the formal objectives in the original PDF lab sheet — deploy from images/templates, manage deployments and replica sets, and expose services.

---

## Objectives
| # | Goal | Confirmed When |
|---|------|---------------|
| 1 | Deploy `nodeinfo` app from quay.io image | Pod reaches **Running** state |
| 2 | Deploy same app using template parameters | All template objects created |
| 3 | Scale deployment to 3 replicas | `oc get pods` shows 3 pods |
| 4 | Inspect ReplicaSets | `oc get rs` displays desired replicas |
| 5 | Expose the app | You can `curl` the route URL and receive JSON |

---

## Prerequisites
* **Working cluster access** (local CRC or shared) – same as Lab 1.
* **`oc` CLI** in your `PATH`.
* Logged in as a user permitted to create projects, deployments, and routes.

---

## Quick‑Start Clone
```bash
# If you haven't already
 git clone https://github.com/KhuramMurad/openshift-hands-on.git
 cd openshift-hands-on

# Create lab‑2 folder if missing
 mkdir -p labs/lab-2
```

---

## Step‑by‑Step Instructions
### 1  Create / Switch to a Project
```bash
oc new-project demo-lab2 || oc project demo-lab2
```

### 2  Deploy from a Container Image
```bash
oc new-app --name=nodeinfo --image=quay.io/redhatworkshops/nodejs-health
# Watch rollout
oc rollout status deployment/nodeinfo
```
`oc get pods -w` should show a single **Running** pod created by a Deployment.

### 3  Deploy the Same App via a Template (optional)
Templates capture a repeatable set of objects. Example template file `nodeinfo-template.yaml`:
```yaml
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: nodeinfo-template
parameters:
- name: APP_NAME
  value: nodeinfo-tpl
- name: IMAGE
  value: quay.io/redhatworkshops/nodejs-health
objects:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: ${APP_NAME}
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: ${APP_NAME}
    template:
      metadata:
        labels:
          app: ${APP_NAME}
      spec:
        containers:
        - name: app
          image: ${IMAGE}
          ports:
          - containerPort: 8080
```
Apply and instantiate:
```bash
oc apply -f nodeinfo-template.yaml
oc new-app nodeinfo-template -p APP_NAME=nodeinfo-tpl -p IMAGE=quay.io/redhatworkshops/nodejs-health
```

### 4  Scale the Deployment & View ReplicaSets
```bash
# Increase to three replicas
oc scale deployment/nodeinfo --replicas=3

# Check ReplicaSets
oc get rs
```
ReplicaSets ensure the desired number of pod replicas are running.

### 5  Expose the Application Externally
```bash
# Create a Service (ClusterIP by default)
oc expose deployment/nodeinfo --port=8080 --name=nodeinfo-svc

# Create a Route for external access
oc expose service/nodeinfo-svc

# Find the route URL
oc get route/nodeinfo-svc -o=jsonpath='{.spec.host}{"\n"}'

# Test with curl
curl http://$(oc get route/nodeinfo-svc -o=jsonpath='{.spec.host}')/healthz
```

### 6  Cleanup (optional)
```bash
oc delete project demo-lab2
```

---

## Automation Script (optional)
Save as `lab2-deploy.sh` in this folder:
```bash
#!/usr/bin/env bash
set -euo pipefail
APP=nodeinfo
IMG=quay.io/redhatworkshops/nodejs-health
NAMESPACE=demo-lab2

oc new-project "$NAMESPACE" || oc project "$NAMESPACE"

echo "Deploying $APP from image…"
oc new-app --name "$APP" --image "$IMG"
oc rollout status deployment/"$APP"

echo "Scaling to 3 replicas…"
oc scale deployment/"$APP" --replicas=3

oc expose deployment/"$APP" --port=8080 --name="$APP"-svc
oc expose service/"$APP"-svc

ROUTE=$(oc get route "$APP"-svc -o=jsonpath='{.spec.host}')
echo "App available at: http://$ROUTE"
```
Run with:
```bash
chmod +x lab2-deploy.sh
./lab2-deploy.sh
```

---

## Verification Checklist
- [ ] `oc get pods` shows 3 running `nodeinfo` pods.
- [ ] `oc get rs` lists a ReplicaSet with **desired=3, current=3**.
- [ ] `curl http://<route>` returns HTTP 200.

---

## Next Steps
Move on to **[Lab 3 – Managing Storage](../lab-3/README.md)** to learn persisting data with PVCs, ConfigMaps, and Secrets.

---
© 2025 OpenShift Hands‑On Labs • MIT License
