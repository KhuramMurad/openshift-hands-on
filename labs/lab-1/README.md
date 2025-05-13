# Lab 1 – Managing OpenShift Container Platform

---

> **Repository:** <https://github.com/KhuramMurad/openshift-hands-on>

This README lives in `labs/lab-1/README.md` and is the **single source of truth** for performing Lab 1. Clone the repo, follow the setup notes to run a local OpenShift cluster, and work through the step‑by‑step tasks.

```bash
# Clone once (or pull to update)
git clone https://github.com/KhuramMurad/openshift-hands-on.git
cd openshift-hands-on/labs/lab-1
```

---

## Lab Overview
This lab introduces the **core OpenShift management tools**―the Web Console and the `oc` CLI―and walks you through creating and managing projects (namespaces). These are the foundational skills needed for every subsequent lab in this repository.

## Objectives
1. Log in to the OpenShift Web Console and explore the major sections.
2. Log in to the cluster with the `oc` command‑line interface.
3. Create a new project (namespace) from the CLI.
4. List existing projects.
5. Delete the project you created.

## Prerequisites
| Requirement | Notes |
|-------------|-------|
| **Local OpenShift 4.x cluster** | Quick instructions provided below for Windows & Linux (CRC) |
| Cluster credentials | A user with *project‑create* permission (cluster‑admin or developer) |
| **oc** CLI | Download from the cluster **Help → Command‑line tools** page and place in `PATH` |
| Bash or PowerShell | Needed only if you want to run the automation script |

---

## Setting Up a Local Cluster (before you start 🔧)
If you don’t have access to a shared OpenShift environment, spin up a local **CodeReady Containers (CRC)** instance. CRC gives you a single‑node OpenShift 4.x cluster that’s perfect for these labs.

### Windows 10/11 (CRC + Hyper‑V)
1. Install **Hyper‑V** (Control Panel → Turn Windows features on/off → check *Hyper‑V*).
2. Download the latest CRC ZIP from <https://developers.redhat.com/content-gateway/rest/mirror/pub/openshift-v4/clients/crc/latest/>.
3. Extract and add the CRC binary to `PATH` (e.g. `C:\crc` directory).
4. Open **PowerShell** (Run as Administrator) and initialise:
   ```powershell
   crc setup
   crc config set cpus 6
   crc config set memory 16384   # 16 GiB recommended
   crc start -p C:\path\to\pull-secret.txt
   ```
5. Note the console URL and admin credentials printed at the end. Add them to your password vault.

### Linux (CRC + libvirt/VirtualBox)
1. Install dependencies (example for Fedora‑based distros):
   ```bash
   sudo dnf install NetworkManager libvirt-daemon-kvm qemu-kvm podman -y
   sudo systemctl enable --now libvirtd
   ```
2. Download CRC tarball, extract, and move binary into `/usr/local/bin`.
3. Run the one‑time setup and start:
   ```bash
   crc setup
   crc config set cpus 4
   crc config set memory 12288   # 12 GiB minimum
   crc start -p ~/pull-secret.txt
   ```
4. Export the `KUBECONFIG` printed by `crc start` or use the login command:
   ```bash
   eval $(crc oc-env)
   ```
5. Verify:
   ```bash
   oc status
   ```

> **Tip 💡** CRC defaults give you a DNS entry such as `console-openshift-console.apps-crc.testing`. Add `crc.testing` to your local resolver if necessary (see CRC docs).

With the cluster running, you can proceed to the actual Lab 1 tasks below.

---

## Step‑by‑Step Instructions
### 1  Log in to the Web Console
1. Browse to the console URL provided by CRC or your shared cluster (e.g. `https://console-openshift-console.apps-crc.testing`).
2. Use the *kubeadmin* credentials or a developer user account.
3. Explore the left navigation:
   * **Overview** – cluster health / capacity.
   * **Projects** – list and switch namespaces.
   * **Compute → Nodes/Pods** – running workloads.
   * **Storage / Networking / Builds / Operators** – you’ll revisit these in later labs.

### 2  Log in with the CLI
```bash
# Replace <cluster‑api> and <user>
oc login <cluster‑api> -u <user>
# Example:
oc login https://api.crc.testing:6443 -u developer
```
> Supply your password or a token when prompted.

### 3  Create a New Project
```bash
oc new-project demo-lab1
```

### 4  List All Projects
```bash
oc get projects   # or: oc projects
```

### 5  Delete the Project (Cleanup)
```bash
oc delete project demo-lab1
```

Deleting the project removes all resources created during this lab and keeps the cluster tidy for the next exercise.

---

## Automation Script (optional)
For repeatability you can execute the helper script below. Place it alongside this README as `lab1-automation.sh`.

```bash
#!/usr/bin/env bash
# Lab 1 helper – automate project create/list/delete
set -euo pipefail

if [[ $# -lt 3 ]]; then
  echo "Usage: $0 <cluster_url> <username> <project_name> [--clean]" >&2
  exit 1
fi
CLUSTER_URL="$1"; USER="$2"; PROJECT="$3"; CLEAN="${4:-}"
read -s -p "Password for $USER: " PASSWORD; echo

oc login "$CLUSTER_URL" -u "$USER" -p "$PASSWORD"
echo "Creating project $PROJECT …" && oc new-project "$PROJECT"

echo "Current projects:" && oc get projects

if [[ "$CLEAN" == "--clean" ]]; then
  echo "Cleaning up – deleting $PROJECT …" && oc delete project "$PROJECT"
  oc wait --for=delete project/"$PROJECT" --timeout=120s || true
fi

echo "Lab 1 complete!"
```

Make it executable and run:
```bash
chmod +x lab1-automation.sh
./lab1-automation.sh https://api.crc.testing:6443 developer demo-lab1 --clean
```

---

## Verification Checklist
- [ ] Able to log in via Web Console and locate cluster resources.
- [ ] `oc whoami` returns your username.
- [ ] `demo-lab1` project exists after creation step.
- [ ] Project successfully deleted (optional cleanup).

## Key Concepts Recap
* **Web Console vs. CLI** – two complementary interaction surfaces.
* **Project (Namespace)** – tenancy boundary for OpenShift resources.
* **Role‑Based Access Control (RBAC)** determines who can create projects.

## Next Steps
Proceed to **[Lab 2 – Deploying Applications](../lab-2/README.md)** where you’ll deploy your first application using container images and templates.

---
© 2025 OpenShift Hands‑On Labs • MIT License
