# platform-gitops

Declarative **GitOps configuration** for Kubernetes, managed by **Argo CD**.

This repository defines the **desired state** of all workloads and cluster tools
deployed to the Kubernetes platform.  
Argo CD continuously reconciles this configuration against the live cluster, ensuring every change is versioned, reviewed, and automatically applied.

---

## 🧭 Overview

This repository is part of a multi-repo proof-of-concept demonstrating modern **DevOps** and **GitOps** practices:

| Repository | Purpose |
|-------------|----------|
| [`platform-infra`](https://github.com/StefanFilkov/platform-infra) | Terraform code that provisions infrastructure and bootstraps Argo CD |
| **`platform-gitops`** | Declarative Kubernetes manifests and Argo CD ApplicationSets (this repo) |
| [`sealed-secrets`](https://github.com/StefanFilkov/sealed-secrets) | Encrypted Kubernetes secrets managed through Sealed Secrets |
| [`platform-pipelines`](https://github.com/StefanFilkov/platform-pipelines) | Reusable Azure DevOps pipeline templates used to build and deploy workloads |

---

## 📁 Repository Structure

```
applications/
├── app-k8s/ # Kubernetes manifests for each microservice
│ ├── admin-server/
│ │ └── admin-server.yaml
│ ├── api-gateway/
│ │ └── api-gateway.yaml
│ ├── config-server/
│ │ └── config-server.yaml
│ ├── customers-service/
│ │ └── customers-service.yaml
│ ├── discovery-server/
│ │ └── discovery-server.yaml
│ ├── genai-service/
│ │ └── genai-service.yaml
│ ├── vets-service/
│ │ └── vets-service.yaml
│ └── visits-service/
│ └── visits-service.yaml
│
├── app-sets/ # Argo CD ApplicationSets
│ ├── pet-clinic-application-set.yaml
│ └── cert-issuer-application-set.yaml
│
└── tools/ # Cluster-level tools managed through Argo CD
└── cert-issuer/
└── app/
└── cert.yaml
```


---

## ⚙️ How It Works

1. **Argo CD**, bootstrapped by `platform-infra`, monitors this repository.
2. The **ApplicationSets** in `applications/app-sets/` generate Argo CD `Application` resources dynamically.
3. Each application manifest under `app-k8s/` defines how a microservice is deployed.
4. **Cluster tools** (like Cert-Manager or Ingress controllers) are managed declaratively under `tools/`.
5. Any change merged into `main` is automatically synchronized to the cluster by Argo CD.

---

## 🧩 Adding a New Application

1. Create a folder under applications/app-k8s/<app-name>/.
2. Add a Kubernetes manifest (e.g. Deployment, Service, Ingress).
3. Reference it from an existing or new ApplicationSet in applications/app-sets/.
4. Commit and push — Argo CD will automatically deploy it.

---

## 🧰 Tooling
| Tool            | Purpose                                                      |
| --------------- | ------------------------------------------------------------ |
| Argo CD         | GitOps controller that reconciles manifests with the cluster |
| ApplicationSets | Define and manage multiple Argo CD Applications declaratively |
| Kubernetes YAML | Declarative app definitions and services                     |
| Cert-Manager    | Automatic certificate issuance (managed via ApplicationSet)  |
| Sealed Secrets  | Secure management of encrypted secrets                       |

---

## 🔄 Possible Workflows

### 1) Direct GitOps (single repo change)
**Flow:** Developer → PR in `platform-gitops` → Merge to `main` → Argo CD auto-syncs → Cluster updated  
**Notes:** Great for config-only changes and production environment.

### 2) CI updates GitOps (app repo is source of truth for images)
**Flow:** Developer → PR in *app repo* → Merge → CI builds & pushes images → CI opens PR to `platform-gitops` to bump image  
**Notes:** Good for non-production environments and fast development. For dev environments you can directly push to `platform-gitops`.


---

## 🛡️ Best Practices

- Keep manifests small and environment-specific.
- Store only **non-secret** configuration here (secrets go to the sealed-secrets repo).
- Use PRs for promoting configuration between environments.

---

## 🧠 Summary

platform-gitops is the single source of truth for everything deployed to the cluster —
applications, add-ons, and configurations — all managed declaratively through GitOps

--- 

Author: Stefan Filkov
Purpose: Declarative GitOps repository for managing Kubernetes workloads and platform tools with Argo CD.