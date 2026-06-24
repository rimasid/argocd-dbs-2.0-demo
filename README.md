# argocd-dbs-2.0-demo

Demo repo for the **ArgoCD Beginner Session** — DevOps Malayalam Beginner Series 2.0.

In this session you will set up a local Kubernetes cluster using **kind**, install **ArgoCD via Helm**, and deploy a sample nginx app using ArgoCD's GitOps workflow.

---

## What You Will Learn

- What ArgoCD is and why GitOps matters
- How to spin up a local Kubernetes cluster with kind
- How to install ArgoCD using Helm
- How to create and sync an ArgoCD Application
- How ArgoCD reconciles drift (live demo)

---

## Prerequisites

| Tool | Version | Docs |
|------|---------|------|
| Docker | 20+ | https://docs.docker.com/get-docker/ |
| kubectl | latest | https://kubernetes.io/docs/tasks/tools/ |
| kind | v0.23+ | https://kind.sigs.k8s.io/docs/user/quick-start/#installation |
| Helm | v3.x | https://helm.sh/docs/intro/install/ |

---

## Repo Structure

```
argocd-dbs-2.0-demo/
├── docs/
│   ├── 01-kind-setup.md          # Create the kind cluster
│   ├── 02-argocd-helm-install.md # Install ArgoCD via Helm
│   └── 03-poc-guide.md           # Deploy nginx with ArgoCD
├── apps/
│   └── nginx/
│       ├── namespace.yaml
│       ├── deployment.yaml
│       └── service.yaml
└── argocd/
    └── nginx-app.yaml            # ArgoCD Application CR
```

---

## Quick Start

Follow the guides in order:

1. [Setup kind cluster](docs/01-kind-setup.md)
2. [Install ArgoCD with Helm](docs/02-argocd-helm-install.md)
3. [Deploy nginx via ArgoCD (POC)](docs/03-poc-guide.md)

---

## References

- ArgoCD Official Docs: https://argo-cd.readthedocs.io/en/stable/
- kind Docs: https://kind.sigs.k8s.io/
- Helm Docs: https://helm.sh/docs/
- ArgoCD Helm Chart: https://artifacthub.io/packages/helm/argo/argo-cd
