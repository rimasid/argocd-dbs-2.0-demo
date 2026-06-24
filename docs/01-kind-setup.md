# Step 1 — Set Up a kind Cluster

**Official docs:** https://kind.sigs.k8s.io/docs/user/quick-start/

---

## Install kind

**macOS (Homebrew)**
```bash
brew install kind
```

**Linux**
```bash
# AMD64
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

**Windows (Chocolatey)**
```powershell
choco install kind
```

Verify:
```bash
kind version
```

---

## Create the Cluster

```bash
kind create cluster --name argocd-demo
```

This creates a single-node cluster named `argocd-demo`. kind runs Kubernetes nodes as Docker containers so Docker must be running.

Verify the cluster is up:
```bash
kubectl cluster-info --context kind-argocd-demo
kubectl get nodes
```

Expected output — node should be in `Ready` state:
```
NAME                       STATUS   ROLES           AGE   VERSION
argocd-demo-control-plane  Ready    control-plane   30s   v1.x.x
```

---

## (Optional) Multi-node Cluster

For a more realistic setup, create a cluster config file:

```bash
cat <<EOF > kind-cluster.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
EOF

kind create cluster --name argocd-demo --config kind-cluster.yaml
```

---

## Delete the Cluster (cleanup after session)

```bash
kind delete cluster --name argocd-demo
```

---

Next: [Install ArgoCD with Helm](02-argocd-helm-install.md)
