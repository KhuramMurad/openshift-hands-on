# Lab 12 – Security and Compliance in OpenShift

---

> **Repository path:** `labs/lab-12/lab-12-README.md`

This README lives in `labs/lab-12/lab-12-README.md` and walks you through securing your cluster and workloads.

## Lab Overview
In this lab you will:
1. Configure Pod securityContexts.  
2. Define Roles and RoleBindings for least-privilege.  
3. Audit the cluster for compliance.

## Objectives
- Harden containers via security contexts.  
- Apply RBAC for fine-grained access control.  
- Perform an audit to detect misconfigurations. citeturn5file4

## Step‑by‑Step Instructions
### 1. Pod SecurityContext
```bash
oc apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata: { name: secure-pod }
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: app
    image: nginx
    securityContext:
      capabilities:
        add: ["NET_ADMIN"]
EOF
```

### 2. Roles & RoleBindings
```bash
oc apply -f - <<EOF
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata: { namespace: default, name: pod-reader }
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get","list","watch"]
EOF

oc apply -f - <<EOF
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata: { namespace: default, name: read-pods }
subjects:
- kind: User
  name: "developer"
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
EOF
```

### 3. Cluster Audit
```bash
oc adm audit --path=audit.log
```

## Cleanup (optional)
```bash
oc delete pod secure-pod
oc delete role pod-reader
oc delete rolebinding read-pods
```

## Verification Checklist
- [ ] Pod runs under non-root user.  
- [ ] Role permits pod-listing only.  
- [ ] Audit log generated at `audit.log`.

## Next Steps
You have completed the core hands‑on labs! Explore **Lab 13** for task automation or revisit any lab as needed.
© 2025 OpenShift Hands‑On Labs • MIT License
