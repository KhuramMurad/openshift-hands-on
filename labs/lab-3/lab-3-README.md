# Lab 3 – Managing Storage

---

> **Repository path:** `labs/lab-3/lab-3-README.md`

This README lives in `labs/lab-3/lab-3-README.md` and guides you through managing storage in OpenShift.

## Lab Overview
In this lab you will learn to manage storage for application configuration and data. You’ll provision persistent storage volumes for block and file-based data, use storage classes for dynamic provisioning, and deploy stateful workloads that require stable storage.

## Objectives
- Manage storage for application configuration and data.  
- Provision persistent storage volumes for block and file-based data.  
- Use storage classes and manage non-shared storage with StatefulSets. citeturn2file0

## Step‑by‑Step Tasks

### 1. Create and Use Secrets for Sensitive Data
**Objective:** Demonstrate the secure handling of sensitive data using Secrets.  
**Explanation:** OpenShift Secrets store sensitive information (passwords, tokens) securely.  
```bash
# Create a generic secret
oc create secret generic my-secret --from-literal=PASSWORD=my-password

# Use the secret in a pod YAML (my-pod-with-secret.yaml)
oc create -f my-pod-with-secret.yaml
``` citeturn2file0

### 2. Create and Use ConfigMaps for Configuration Data
**Objective:** Illustrate the use of ConfigMaps for decoupling configuration from images.  
**Explanation:** ConfigMaps allow you to inject configuration artifacts into pods.  
```bash
# Create a ConfigMap with a literal key-value
oc create configmap my-config --from-literal=CONFIG_KEY=CONFIG_VALUE

# Use the ConfigMap in a pod YAML (my-pod-with-configmap.yaml)
oc create -f my-pod-with-configmap.yaml
``` citeturn2file0

### 3. Provision Persistent Storage and Attach It to a Pod
**Objective:** Provision storage and mount it into your application pod.  
**Explanation:** PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs) ensure data persists across pod restarts.  
```bash
# Create a PersistentVolume (my-persistent-volume.yaml)
oc create -f my-persistent-volume.yaml

# Create a PersistentVolumeClaim (my-persistent-volume-claim.yaml)
oc create -f my-persistent-volume-claim.yaml

# Use the PVC in a pod YAML (my-pod-with-pvc.yaml)
oc create -f my-pod-with-pvc.yaml
``` citeturn2file0

### 4. Understand and Use StorageClasses
**Objective:** Explain and utilize StorageClasses for dynamic volume provisioning.  
**Explanation:** StorageClasses describe “classes” of storage (performance tiers, backends).  
```bash
# Create a StorageClass (my-storage-class.yaml)
oc create -f my-storage-class.yaml

# Reference it in your PVC (my-pvc-with-storage-class.yaml)
oc create -f my-pvc-with-storage-class.yaml
``` citeturn2file0

### 5. Deploy a StatefulSet with Persistent Storage
**Objective:** Deploy a StatefulSet that provides stable identities and storage for each replica.  
**Explanation:** StatefulSets manage stateful applications where each pod needs its own persistent storage.  
```bash
# Create a StatefulSet (my-stateful-set.yaml)
oc create -f my-stateful-set.yaml
``` citeturn2file0

## Cleanup (optional)
```bash
oc delete project demo-lab3
```

## Verification Checklist
- [ ] Secret `my-secret` exists and is mounted in the pod.  
- [ ] ConfigMap `my-config` used by a pod.  
- [ ] PVC bound to a PV and pod uses mounted volume.  
- [ ] StorageClass dynamically provisions new PVs.  
- [ ] StatefulSet pods are Running with associated persistent volumes.

## Next Steps
Proceed to **[Lab 4 – Configuring Applications for Reliability](../lab-4/README.md)** to learn health checks, resource limits, and autoscaling.

---
© 2025 OpenShift Hands‑On Labs • MIT License
