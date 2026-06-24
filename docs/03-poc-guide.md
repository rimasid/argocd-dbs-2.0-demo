# Step 3 — Deploy nginx with ArgoCD (POC)

**Official docs:** https://argo-cd.readthedocs.io/en/stable/getting_started/

This guide walks you through creating an ArgoCD **Application** that watches this repo and deploys the nginx app from `apps/nginx/`.

---

## How It Works

```
GitHub Repo (this repo)          ArgoCD              kind Cluster
  apps/nginx/*.yaml   ──watch──>  Application  ──sync──>  nginx pods
```

ArgoCD continuously compares the **desired state** (Git) with the **live state** (cluster) and reconciles any drift.

---

## Apply the ArgoCD Application

The Application manifest is already in this repo at `argocd/nginx-app.yaml`.

Apply it to your cluster:
```bash
kubectl apply -f argocd/nginx-app.yaml
```

---

## Verify in the UI

1. Open https://localhost:8080 (port-forward must be running)
2. You should see the `nginx-demo` application
3. Click on it — you'll see the deployment, service, and namespace as a live graph
4. Status should move from `OutOfSync` → `Syncing` → `Synced`

---

## Verify via kubectl

```bash
kubectl get all -n nginx-demo
```

Expected:
```
NAME                         READY   STATUS    
pod/nginx-xxx-xxx            1/1     Running   

NAME                TYPE        CLUSTER-IP   PORT(S)   
service/nginx-svc   ClusterIP   10.x.x.x     80/TCP    

NAME                    READY   UP-TO-DATE   AVAILABLE
deployment.apps/nginx   1/1     1            1         
```

---

## Demo: GitOps Drift Reconciliation

This is the key concept to demonstrate live.

**Manually delete the deployment (simulate drift):**
```bash
kubectl delete deployment nginx -n nginx-demo
```

Watch ArgoCD detect and fix it automatically within ~3 minutes (default reconcile interval), or click **Sync** in the UI for an immediate fix.

**Scale the deployment manually (simulate config drift):**
```bash
kubectl scale deployment nginx -n nginx-demo --replicas=3
```

ArgoCD will bring it back to 1 replica (as defined in Git).

---

## Demo: Update via Git (the GitOps way)

1. Edit `apps/nginx/deployment.yaml` — change `replicas: 1` to `replicas: 2`
2. Commit and push to GitHub
3. ArgoCD detects the change and syncs automatically (or click Sync in UI)
4. Verify:
```bash
kubectl get pods -n nginx-demo
```

---

## Enable Auto-Sync (optional)

By default ArgoCD requires a manual sync. To enable auto-sync, edit `argocd/nginx-app.yaml` and add:

```yaml
spec:
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Then re-apply:
```bash
kubectl apply -f argocd/nginx-app.yaml
```

With `selfHeal: true`, ArgoCD automatically reverts any manual changes to match Git — this makes the drift demo instant.

---

## Cleanup

```bash
kubectl delete -f argocd/nginx-app.yaml
kubectl delete namespace nginx-demo
```

---

## References

- ArgoCD Application Spec: https://argo-cd.readthedocs.io/en/stable/operator-manual/application.yaml/
- Sync Policies: https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/
- Self Heal: https://argo-cd.readthedocs.io/en/stable/user-guide/auto_sync/
