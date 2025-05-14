# Lab 17 – Custom Resource Definitions (CRDs) and Operators

---

> **Repository path:** `labs/lab-17/lab-17-README.md`

This README lives in `labs/lab-17/lab-17-README.md` and covers CRD creation, custom resources, and Operator usage. citeturn6file14

## Lab Overview
In this lab you will:
1. Create a CustomResourceDefinition.
2. Deploy a Custom Resource.
3. Install and use an Operator.

## Objectives
- Extend the API with CRDs.  
- Manage lifecycle of custom resources.  
- Automate workloads with Operators. citeturn6file14

---

## Step‑by‑Step Instructions

### 1. Define a CRD
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: myapps.example.com
spec:
  group: example.com
  names:
    plural: myapps
    kind: MyApp
  scope: Namespaced
  versions:
  - name: v1
    served: true
    storage: true
```
```bash
oc apply -f my-crd.yaml
```

### 2. Create a Custom Resource
```yaml
apiVersion: example.com/v1
kind: MyApp
metadata:
  name: myapp-instance
spec:
  replicas: 3
```
```bash
oc apply -f myapp-instance.yaml
```

### 3. Install an Operator
```bash
oc apply -f operator.yaml
oc get csv -n openshift-operators
```

---

## Verification Checklist
- [ ] CRD `myapps.example.com` exists.
- [ ] Custom resource instance created.
- [ ] Operator CSV is Installed.

## Next Steps
Proceed to **[Lab 18 – Performance Tuning and Optimization](../lab-18/lab-18-README.md)**.
