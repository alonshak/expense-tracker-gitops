# Expense Tracker — GitOps Repository

This repository defines the **desired state** of the Expense Tracker platform using a **GitOps-first** approach.

It does **not** contain application source code or infrastructure provisioning logic.
Instead, it describes *how applications should run* on Kubernetes, with Git serving as the **single source of truth**.

All deployments are managed declaratively by **ArgoCD**.

---

## 🧠 Repository Responsibility

| Repository | Responsibility |
|---|---|
| expense-tracker-infra | Cloud infrastructure (VPC, EKS, nodes) |
| expense-tracker-app | Application source code (frontend & backend) |
| expense-tracker-gitops | Kubernetes desired state & delivery model |

---

## 🟦 Milestone 7 — GitOps Wiring (App-of-Apps)

### 🎯 Objective
Establish a real GitOps control plane by wiring ArgoCD to the GitOps repository using the **App-of-Apps** pattern, making Git the single source of truth for all future deployments.

---

### 🧱 What Was Implemented

- Created a **root ArgoCD Application (App-of-Apps)** responsible for managing all child applications.
- Connected ArgoCD to the `expense-tracker-gitops` repository.
- Configured ArgoCD to automatically sync from the `main` branch.
- Enabled automated reconciliation (`prune` and `selfHeal`) to enforce Git as the desired state.
- Performed a **one-time bootstrap apply** to initialize GitOps control.

---

### 📂 Repository Structure (GitOps Control Plane)

expense-tracker-gitops/
├── argocd/
│   └── app-of-apps.yaml
├── apps/
│   └── expense-tracker/
│       ├── staging/
│       └── production/
├── charts/
├── docs/
└── README.md


- **argocd/app-of-apps.yaml**  
  Root ArgoCD Application that instructs ArgoCD to manage everything under the `apps/` directory.

- **apps/**  
  Reserved for environment-specific ArgoCD Applications (introduced in later milestones).

---

### 🔁 GitOps Flow

1. ArgoCD continuously watches the `expense-tracker-gitops` repository.
2. The App-of-Apps application monitors the `apps/` directory.
3. Any Application added under `apps/` is automatically discovered and reconciled.
4. Manual deployments via `kubectl` are intentionally avoided.

---

### ✅ Result

- ArgoCD is fully operational and synced with Git.
- The `main` branch is the **single source of truth**.
- No workloads are deployed yet — by design.
- The cluster is ready for declarative application delivery via GitOps.

---

### 🚫 Explicitly Out of Scope (by design)

- Helm charts
- Application deployments
- CI/CD pipelines
- Ingress or domain configuration

---

## 🟦 Milestone 8 — Application Packaging (Helm)

### 🎯 Objective
Prepare the Expense Tracker application for deployment by packaging all components as **Helm charts**, without deploying anything to the cluster yet.

---

### 🧱 What Was Implemented

- Created Helm charts for all application components:
  - **frontend** — React application served via Nginx
  - **backend** — Python Flask REST API
  - **postgres** — PostgreSQL database
- Defined a consistent Helm chart structure for each component:
  - `Chart.yaml` for chart metadata
  - `values.yaml` for configurable parameters
  - `templates/` for Kubernetes manifests
- Implemented Kubernetes primitives using Helm templates:
  - Deployments and Services for stateless components (frontend, backend)
  - StatefulSet with persistent storage for PostgreSQL
- Ensured strict separation of concerns:
  - Application logic
  - Configuration
  - Kubernetes resource definitions
- No resources were deployed to the cluster at this stage — charts are **prepared only**.

---

### 📂 Helm Charts Structure

charts/
├── frontend/
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│       ├── deployment.yaml
│       └── service.yaml
├── backend/
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│       ├── deployment.yaml
│       └── service.yaml
└── postgres/
    ├── Chart.yaml
    ├── values.yaml
    └── templates/
        ├── statefulset.yaml
        └── service.yaml

---

### ✅ Result

- Helm charts are fully defined and versioned in Git.
- The application is ready for GitOps-based deployment via ArgoCD.
- Environment-specific customization (staging / production) can be handled via values files.
- This milestone establishes the foundation for CI pipelines and GitOps deployment.

---


