# Lab 14 – Advanced Networking Concepts

---

> **Repository path:** `labs/lab-14/lab-14-README.md`

This README lives in `labs/lab-14/lab-14-README.md` and explores network policies, service meshes, and troubleshooting network issues. citeturn6file0

## Lab Overview
In this lab you will:
1. Create and enforce NetworkPolicies.
2. Install a basic service mesh (e.g., Istio).
3. Troubleshoot connectivity in a mesh environment.

## Objectives
- Control pod‑to‑pod traffic with NetworkPolicies.  
- Deploy and understand service mesh components.  
- Diagnose mesh connectivity issues. citeturn6file0

---

## Step‑by‑Step Instructions

### 1. NetworkPolicy Enforcement
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-network-policy
spec:
  podSelector:
    matchLabels:
      role: frontend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: backend
    ports:
    - protocol: TCP
      port: 80
```
```bash
oc apply -f my-network-policy.yaml
```

### 2. Service Mesh Setup with Istio
```bash
istioctl operator init
oc create namespace istio-operator
oc apply -f istio-operator.yaml
istioctl install -n istio-system
oc label namespace default istio-injection=enabled
```

### 3. Mesh Troubleshooting
```bash
# Check proxy status
istioctl proxy-status

# Analyze configuration
istioctl analyze
```

---

## Verification Checklist
- [ ] Traffic blocked/allowed per NetworkPolicy.
- [ ] Istio control plane components are Running.
- [ ] `istioctl analyze` reports no errors.

## Next Steps
Proceed to **[Lab 15 – OpenShift on the Cloud](../lab-15/lab-15-README.md)**.
