# Step 2 — Install ArgoCD via Helm

**Official docs:** https://argo-cd.readthedocs.io/en/stable/operator-manual/installation/  
**Helm chart:** https://artifacthub.io/packages/helm/argo/argo-cd

---

## Add the Argo Helm Repository

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
```

Confirm the chart is available:
```bash
helm search repo argo/argo-cd
```

---

## Create the Namespace

```bash
kubectl create namespace argocd
```

---

## Install ArgoCD

```bash
helm install argocd argo/argo-cd \
  --namespace argocd \
  --wait
```

`--wait` blocks until all pods are healthy. This takes ~2 minutes on first run.

Verify pods are running:
```bash
kubectl get pods -n argocd
```

Expected — all pods should be `Running`:
```
NAME                                                READY   STATUS    
argocd-application-controller-0                     1/1     Running   
argocd-applicationset-controller-xxx                1/1     Running   
argocd-dex-server-xxx                               1/1     Running   
argocd-notifications-controller-xxx                 1/1     Running   
argocd-redis-xxx                                    1/1     Running   
argocd-repo-server-xxx                              1/1     Running   
argocd-server-xxx                                   1/1     Running   
```

---

## Access the ArgoCD UI

kind clusters don't expose LoadBalancer IPs, so use port-forward:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open in browser: https://localhost:8080

> Accept the self-signed certificate warning.

---

## Get the Initial Admin Password

```bash
kubectl get secret argocd-initial-admin-secret \
  -n argocd \
  -o jsonpath="{.data.password}" | base64 -d
echo
```

Log in with:
- **Username:** `admin`
- **Password:** (output from above command)

---

## (Optional) Install ArgoCD CLI

```bash
# macOS
brew install argocd

# Linux
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```

Login via CLI:
```bash
argocd login localhost:8080 \
  --username admin \
  --password <your-password> \
  --insecure
```

---

Next: [Deploy nginx via ArgoCD (POC)](03-poc-guide.md)
