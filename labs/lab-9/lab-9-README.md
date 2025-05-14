# Lab 9 – Advanced Deployment Strategies

---

> **Repository path:** `labs/lab-9/lab-9-README.md`

This README lives in `labs/lab-9/lab-9-README.md` and walks you through zero‑downtime deployment patterns.

## Lab Overview
In this lab you will:
1. Implement a Blue-Green deployment.  
2. Set up an A/B deployment and route traffic.  
3. Perform a Canary deployment with weighted traffic shifts. citeturn5file1

## Objectives
- Use Deployment objects and Services/Routes to switch traffic.  
- Understand blue‑green, A/B, and canary patterns.  
- Minimize downtime and risk during rollouts. citeturn5file1

## Step‑by‑Step Instructions
### 1. Blue-Green Deployment
```bash
# Create Blue deployment
oc apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata: { name: blue-deploy }
spec:
  replicas: 3
  selector: { matchLabels: { app: sample-app, version: blue } }
  template:
    metadata: { labels: { app: sample-app, version: blue } }
    spec:
      containers: [{ name: app, image: <blue-image>, ports:[{containerPort:8080}] }]
EOF

# Create Green deployment (same config, version: green)
oc apply -f green-deploy.yaml

# Update Service selector to version green when ready
oc patch svc sample-app -p '{"spec":{"selector":{"app":"sample-app","version":"green"}}}'
```

### 2. A/B Deployment
```bash
# Deploy versions A and B
oc apply -f version-a.yaml
oc apply -f version-b.yaml

# Service continues to select both, traffic split via weighted Routes
oc create route canary ab-route --service=sample-app --port=80 --to-weight 50 --alternate-backends '{"kind":"Service","name":"version-b","weight":50}'
```

### 3. Canary Deployment
```bash
# Baseline deployment
oc apply -f baseline.yaml

# Create canary deployment with fewer replicas
oc apply -f canary.yaml

# Gradually shift traffic using alternateBackends
oc patch route sample-app-route -p '{"spec":{"alternateBackends":[{"kind":"Service","name":"canary-deploy","weight":10}]}}'
```

## Cleanup (optional)
```bash
oc delete deploy blue-deploy green-deploy version-a version-b baseline canary-deploy
oc delete svc sample-app
oc delete route sample-app-route ab-route
```

## Verification Checklist
- [ ] Blue and green versions exist and traffic can be switched.  
- [ ] A/B route balances traffic between versions A and B.  
- [ ] Canary route shows partial traffic to canary pods.

## Next Steps
Proceed to **[Lab 10 – Stateful Applications with StatefulSets](../lab-10/lab-10-README.md)**.
© 2025 OpenShift Hands‑On Labs • MIT License
