# Lab 19 – Continuous Integration/Continuous Deployment (CI/CD)

---

> **Repository path:** `labs/lab-19/lab-19-README.md`

This README lives in `labs/lab-19/lab-19-README.md` and covers setting up CI/CD pipelines in OpenShift.

## Lab Overview
In this lab you will:
1. Create a BuildConfig linked to a source repo.
2. Define a CI/CD pipeline (e.g., Jenkinsfile).
3. Trigger deployments on successful builds.

## Objectives
- Automate builds with BuildConfigs.  
- Implement pipeline stages for build, test, deploy.  
- Set triggers for automatic deployment.

---

## Step‑by‑Step Instructions

### 1. Create a BuildConfig
```bash
oc new-build <git-repo-url> --name=myapp
```

### 2. Define a Pipeline
```groovy
pipeline {
  agent any
  stages {
    stage('Build') { steps { sh 'oc start-build myapp --from-dir=.' } }
    stage('Test')  { steps { sh 'oc exec dc/myapp -- ./run-tests.sh' } }
    stage('Deploy'){ steps { sh 'oc rollout latest dc/myapp' } }
  }
}
```

### 3. Set Build/Deploy Triggers
```bash
oc set triggers bc/myapp --from-config
```

---

## Verification Checklist
- [ ] BuildConfig `myapp` created.  
- [ ] Pipeline runs through all stages.  
- [ ] New code pushes trigger builds and deployments.

## Next Steps
You have completed all core labs! Review any lab or explore OpenShift Operator development and custom integrations.
