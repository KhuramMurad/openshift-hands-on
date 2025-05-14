# Lab 13 – Automating Tasks with OpenShift Jobs and CronJobs

---

> **Repository path:** `labs/lab-13/lab-13-README.md`

This README lives in `labs/lab-13/lab-13-README.md` and covers creating one-time and recurring tasks using OpenShift Jobs and CronJobs. citeturn6file10

## Lab Overview
In this lab you will:
1. Create a Job to run a task to completion.
2. Schedule recurring tasks with a CronJob.
3. Monitor and manage Job and CronJob execution.

## Objectives
- Understand use of Jobs for batch processing.
- Schedule and manage CronJobs for recurring tasks.
- Monitor execution status and logs of Jobs and CronJobs. citeturn6file10

---

## Step‑by‑Step Instructions

### 1. Create a One‑Time Job
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    spec:
      containers:
      - name: my-container
        image: my-image
        command: ["my-task-command"]
      restartPolicy: OnFailure
  backoffLimit: 4
```
```bash
oc apply -f my-job.yaml
oc get jobs
oc logs job/my-job
``` citeturn6file10

### 2. Create a Recurring CronJob
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: my-cronjob
spec:
  schedule: "*/5 * * * *"  # every 5 minutes
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: my-container
            image: my-image
            command: ["my-recurring-task-command"]
          restartPolicy: OnFailure
```
```bash
oc apply -f my-cronjob.yaml
oc get cronjobs
``` citeturn6file10

### 3. Monitor and Manage Jobs
```bash
# View Jobs and Pods
oc get jobs
oc get pods -l job-name=my-job

# View logs
oc logs pod/$(oc get pods -l job-name=my-job -o jsonpath='{.items[0].metadata.name}')

# Clean up
oc delete job/my-job
oc delete cronjob/my-cronjob
``` citeturn6file10

---

## Verification Checklist
- [ ] Job `my-job` completes successfully.
- [ ] CronJob `my-cronjob` schedules pods at the defined interval.
- [ ] Logs for both Job and CronJob can be fetched.

## Next Steps
Proceed to **[Lab 14 – Advanced Networking Concepts](../lab-14/lab-14-README.md)** for network policy and service-mesh deep dive.
