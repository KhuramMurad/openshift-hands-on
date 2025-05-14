# Lab 15 – OpenShift on the Cloud

---

> **Repository path:** `labs/lab-15/lab-15-README.md`

This README lives in `labs/lab-15/lab-15-README.md` and covers deploying OpenShift on public clouds and leveraging cloud services.

## Lab Overview
In this lab you will:
1. Deploy an OpenShift cluster on AWS (or Azure/GCP).
2. Integrate cloud storage (EBS, Azure Disk) and load balancers.
3. Deploy a sample app and use cloud autoscaling.

## Objectives
- Understand cloud integration with OpenShift.  
- Configure PVs using cloud storage classes.  
- Leverage cloud autoscaling and monitoring.

---

## Step‑by‑Step Instructions

### 1. Create Cloud Cluster
```bash
openshift-install create cluster --dir=my-cluster --platform=aws
```

### 2. Cloud-Native Storage & Networking
```yaml
# EBS-backed PVC
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  storageClassName: aws-ebs
  accessModes: ["ReadWriteOnce"]
  resources:
    requests:
      storage: 5Gi
```
```bash
oc apply -f my-pvc.yaml
# LoadBalancer service
oc expose deployment/my-app --type=LoadBalancer
```

### 3. Sample App & Autoscaling
```bash
oc new-app my-sample-app
aws autoscaling create-auto-scaling-group --auto-scaling-group-name my-asg   --launch-configuration-name my-launch-config --min-size 1 --max-size 5
```

---

## Verification Checklist
- [ ] Cluster in AWS console.
- [ ] PVC bound to EBS volume.
- [ ] Sample app exposed via AWS LB.
- [ ] Autoscaling group scales nodes.

## Next Steps
Proceed to **[Lab 16 – High Availability and Disaster Recovery](../lab-16/lab-16-README.md)**.
