# Lab 6 – Troubleshooting Common Container, Pod, and Cluster Events

---

> **Repository path:** `labs/lab-6/lab-6-README.md`

This README lives in `labs/lab-6/lab-6-README.md` and guides you through diagnosing and resolving common container and pod issues, monitoring cluster events, and using OpenShift documentation to troubleshoot effectively.

## Lab Overview
In this lab you will:
- Simulate common pod failures and practice troubleshooting.
- Monitor cluster events and set up alerts.
- Use OpenShift documentation to resolve a simulated cluster issue.

## Objectives
- Diagnose and troubleshoot common container and pod issues.  
- Understand cluster events and alerts and how to respond.  
- Utilize OpenShift documentation effectively for troubleshooting.

## Step-by-Step Tasks

### 1. Simulate Common Pod Failures and Practice Troubleshooting
**Objective:** Build diagnostic skills by simulating pod failures.  
**Explanation:** Understanding how to diagnose and resolve common pod failures is essential for application stability.
```bash
# Simulate a CrashLoopBackOff failure
oc create deployment my-app --image=crash-loop-image:tag

# Check pod logs to identify the issue
oc logs pod/my-app-pod

# Resolve the issue by updating to a correct image
oc set image deployment/my-app my-app-container=correct-image:tag
```

### 2. Monitor Cluster Events and Set Up Alerts
**Objective:** Proactively identify issues by monitoring events and configuring alerts.  
**Explanation:** Alerts enable you to respond before problems impact users.
```bash
# View recent cluster events
oc get events

# Create an alert based on a specific event type
oc create alert my-alert --from-event-type Normal
```

### 3. Use OpenShift Documentation to Resolve a Simulated Cluster Issue
**Objective:** Practice navigating documentation to troubleshoot cluster problems.  
**Explanation:** Effective use of docs accelerates problem resolution.
- Visit the [OpenShift Documentation](https://docs.openshift.com) and search for relevant topics based on the simulated issue.

## Cleanup (optional)
```bash
oc delete project demo-lab6
```

## Verification Checklist
- [ ] Pod failure was simulated and logs analyzed.  
- [ ] Issue resolved by updating the deployment.  
- [ ] Alerts created and visible via `oc get alerts`.  
- [ ] Documentation consulted and solution applied.

## Next Steps
Proceed to **[Lab 7 – Monitoring and Logging](../lab-7/lab-7-README.md)** for deeper insights into cluster metrics and log analysis.

---
© 2025 OpenShift Hands-On Labs • MIT License
