# Lab 8 – Networking and Service Exposure

---

> **Repository path:** `labs/lab-8/lab-8-README.md`

This README lives in `labs/lab-8/lab-8-README.md` and guides you through exposing and securing your applications.

## Lab Overview
In this lab you will:
1. Create and configure Services of types ClusterIP, NodePort, and LoadBalancer.  
2. Expose a Service externally with a Route.  
3. Secure a Route using TLS and create a basic NetworkPolicy. citeturn5file0

## Objectives
- Understand internal and external networking in OpenShift.  
- Manage Services and Routes.  
- Secure application exposure with TLS and NetworkPolicies. citeturn5file0

## Step‑by‑Step Instructions
### 1. Create a ClusterIP Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: clusterip-svc
spec:
  selector:
    app: <app-name>
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
```
```bash
oc create -f clusterip-svc.yaml
```

### 2. Create a NodePort Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-svc
spec:
  selector:
    app: <app-name>
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: NodePort
```
```bash
oc create -f nodeport-svc.yaml
```

### 3. Create a LoadBalancer Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: lb-svc
spec:
  selector:
    app: <app-name>
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
```
```bash
oc create -f lb-svc.yaml
```

### 4. Expose a Service with a Route
```bash
oc expose service/clusterip-svc --name=external-route
oc get route external-route -o jsonpath='{.spec.host}{"\n"}'
curl http://$(oc get route external-route -o jsonpath='{.spec.host}')/
```

### 5. Secure the Route and Add a NetworkPolicy
```bash
# Create a TLS route
oc create route edge secure-route --service=clusterip-svc --cert=cert.crt --key=key.key

# Define a basic NetworkPolicy
cat <<EOF | oc apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: basic-policy
spec:
  podSelector:
    matchLabels:
      app: <app-name>
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 80
EOF
```

## Cleanup (optional)
```bash
oc delete route external-route secure-route
oc delete svc clusterip-svc nodeport-svc lb-svc
oc delete networkpolicy basic-policy
```

## Verification Checklist
- [ ] Services of all three types exist.  
- [ ] Route returns application response.  
- [ ] TLS-secured route responds over HTTPS.  
- [ ] NetworkPolicy restricts traffic to pods with label `role=frontend`.

## Next Steps
Proceed to **[Lab 9 – Advanced Deployment Strategies](../lab-9/lab-9-README.md)** for blue-green, A/B, and canary deployments.
© 2025 OpenShift Hands‑On Labs • MIT License
