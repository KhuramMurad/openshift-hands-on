# Lab 7 – Monitoring and Logging

---

> **Repository path:** `labs/lab-7/lab-7-README.md`

This README lives in `labs/lab-7/lab-7-README.md` and shows you how to configure monitoring tools, fetch and analyze logs, and set up alerts in your OpenShift cluster.

---

## Lab Overview
In this lab you will:
1. Configure and use built-in monitoring tools (Prometheus & Grafana).  
2. Fetch and analyze logs from Pods and containers.  
3. Set up alerts for common warning signs.

## Objectives
- Set up and interpret cluster monitoring tools.  
- Understand and analyze logs for different resources.  
- Diagnose issues based on events and alerts. citeturn4file0

## Prerequisites
- **Cluster-admin access** or equivalent to install monitoring components.  
- **`oc` CLI** in your PATH and logged in to the target cluster.  
- A sample application (e.g., `nodeinfo`) deployed to generate metrics and logs.

## Quick‑Start Clone
```bash
git clone https://github.com/KhuramMurad/openshift-hands-on.git
cd openshift-hands-on
mkdir -p labs/lab-7
```

---

## Step‑by‑Step Instructions

### 1  Configure and Use Built-in Monitoring Tools
**Objective:** Configure and utilize the built-in monitoring tools to observe cluster metrics.  
**Explanation:** OpenShift’s built-in monitoring stack (Prometheus & Grafana) provides essential insights into cluster health.  
```bash
# Install the cluster monitoring operator
oc apply -f https://github.com/openshift/cluster-monitoring-operator/releases/download/v0.10.0/cluster-monitoring.yaml

# Forward Grafana port locally
oc port-forward -n openshift-monitoring service/grafana 3000
```
Visit http://localhost:3000, log in (admin/prom-operator), and explore dashboards. citeturn4file0

### 2  Fetch and Analyze Logs of a Pod and a Container
**Objective:** Learn how to fetch and analyze logs from pods and containers.  
**Explanation:** Logs help identify errors and abnormal behavior.  
```bash
# Fetch logs from a Pod
oc logs pod/<pod-name>

# Fetch logs from a specific container
oc logs pod/<pod-name> -c <container-name>
```
Use `-f` for live streaming.

### 3  Set Up Alerts for Common Warning Signs
**Objective:** Set up alerts for conditions like high CPU usage to proactively catch issues.  
**Explanation:** Prometheus rules can trigger alerts when metrics exceed thresholds.  
```bash
# Create an Alert for high CPU usage (example)
oc create alert high-cpu-alert --expr '100 * (1 - avg(rate(container_cpu_usage_seconds_total{container_name!="POD"}[5m])) by (pod_name, namespace) / on (pod_name, namespace) group_left sum(kube_pod_container_resource_limits_cpu_cores) * 100) > 90'
```
Verify with `oc get alerts`. citeturn4file0

---

## Automation Script (optional)
Save this as `lab7-monitor.sh` alongside the README:
```bash
#!/usr/bin/env bash
set -euo pipefail

# 1) Install monitoring operator
oc apply -f https://github.com/openshift/cluster-monitoring-operator/releases/download/v0.10.0/cluster-monitoring.yaml

# 2) Port-forward Grafana
oc port-forward -n openshift-monitoring service/grafana 3000 &

# 3) Fetch sample app logs
echo "Logs for sample app:"
oc logs pod/$(oc get pods -l app=nodeinfo -o jsonpath='{.items[0].metadata.name}')

# 4) Create CPU alert
oc create alert high-cpu-alert --expr '100 * (1 - avg(rate(container_cpu_usage_seconds_total{container_name!="POD"}[5m])) by (pod_name, namespace) / on (pod_name, namespace) group_left sum(kube_pod_container_resource_limits_cpu_cores) * 100) > 90'

echo "Cluster monitoring set up, Grafana on localhost:3000, CPU alert created."
```

---

## Verification Checklist
- [ ] Grafana dashboard reachable at http://localhost:3000.  
- [ ] `oc logs` commands successfully fetch logs.  
- [ ] Alert `high-cpu-alert` appears in `oc get alerts`.  

## Next Steps
Proceed to **[Lab 8 – Networking and Service Exposure](../lab-8/lab-8-README.md)** to learn exposing applications and securing routes.

---
© 2025 OpenShift Hands‑On Labs • MIT License
