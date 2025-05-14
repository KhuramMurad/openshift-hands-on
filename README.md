# OpenShift Hands-On Labs

Welcome to the **OpenShift Hands-On Labs** repository – a curated, step-by-step collection of practical exercises designed to take you from a complete OpenShift beginner to a confident cluster operator and developer. Each lab builds on the previous, covering essential OpenShift concepts, tools, and best practices in a hands-on format.

---

## 📂 Repository Structure

```
.
├── labs
│   ├── lab-1
│   │   └── lab-1-README.md
│   ├── lab-2
│   │   └── lab-2-README.md
│   ├── lab-3
│   │   └── lab-3-README.md
│   ├── lab-4
│   │   └── lab-4-README.md
│   └── lab-N
│       └── lab-N-README.md
├── LICENSE
└── README.md      ← You are here
```

---

## 🔑 Skills Covered

By working through these labs, you will develop hands‑on proficiency in:

- **Cluster Management**  
  - Installing and configuring local clusters (CRC, kind, Minishift)  
  - Navigating the Web Console and `oc` CLI  
  - Creating and managing Projects (namespaces) and RBAC roles

- **Application Deployment**  
  - Deploying from container images and OpenShift Templates  
  - Managing Deployments, ReplicaSets, and scaling strategies  
  - Exposing workloads via Services and Routes  

- **Storage & Configuration**  
  - Creating ConfigMaps and Secrets for application data  
  - Provisioning PersistentVolumes and using PVCs  
  - Dynamic provisioning with StorageClasses and StatefulSets  

- **Reliability & Updates**  
  - Health checks (readiness, liveness) and resource limits  
  - Rolling updates, rollbacks, and deployment strategies (blue‑green, canary)  

- **Troubleshooting & Monitoring**  
  - Inspecting logs, events, and pod status  
  - Integrating Prometheus, Grafana, and EFK stack  
  - Diagnosing common container, pod, and network issues  

- **Advanced Concepts**  
  - Managing custom resources with CRDs and Operators  
  - Performance tuning and cluster optimization  
  - CI/CD pipelines with Jenkins, ArgoCD, and OpenShift Pipelines  
  - Security and compliance: network policies, SCCs, and image scanning  
  - High availability, disaster recovery, and multi‑cloud deployments  

---

## 🦸 Author Expertise

This lab series is authored by **Khuram Murad**, an AIOps Engineer at Al Nafi International College:

- 5+ years of experience in Kubernetes and OpenShift environments  
- Hands‑on practice deploying production‑grade clusters on-premises and in the cloud  
- Expertise in automation (Ansible, Terraform), GitOps (ArgoCD), and CI/CD (Jenkins, Tekton)  
- Deep understanding of OpenShift architecture: Operators, CRDs, SDN, and storage plugins  
- Proven track record of designing secure, scalable, and highly available container platforms  

---

## 🚀 Getting Started

1. **Clone the repository**  
   ```bash
   git clone https://github.com/KhuramMurad/openshift-hands-on.git
   cd openshift-hands-on
   ```

2. **Choose your lab**  
   Each lab lives in `labs/lab-<number>/lab-<number>-README.md`. Start with **Lab 1** and follow the sequential instructions.

3. **Spin up a cluster**  
   For local practice, install CodeReady Containers (CRC) or use a shared OpenShift environment.

4. **Follow the step-by-step guide**  
   Run shell commands, apply YAML manifests, and use built-in automation scripts to accelerate your learning.

---

## 🎓 Who Should Use This?

- **New learners** looking for a structured, hands‑on path to mastering OpenShift.  
- **Developers** who want to understand how containerized apps run on OpenShift.  
- **Administrators and DevOps engineers** aiming to automate, secure, and scale enterprise clusters.  

Each lab includes detailed explanations, verification checklists, and optional scripts—so you can apply what you learn immediately and see real results.

---

## 📄 License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.

---

**Happy learning and happy clustering!**
