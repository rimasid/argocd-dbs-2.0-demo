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

> This Application has **auto-sync enabled** (`automated.prune` and `automated.selfHeal` in `argocd/nginx-app.yaml`), so ArgoCD syncs on its own without a manual Sync click.

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

This is the key concept to demonstrate live. Because `selfHeal: true` is enabled, ArgoCD automatically reverts any manual change back to the state defined in Git.

**Manually delete the deployment (simulate drift):**
```bash
kubectl delete deployment nginx -n nginx-demo
```

ArgoCD detects the drift and recreates the deployment automatically — no manual Sync needed. Watch it reappear:
```bash
kubectl get deployment -n nginx-demo -w
```

**Scale the deployment manually (simulate config drift):**
```bash
kubectl scale deployment nginx -n nginx-demo --replicas=3
```

ArgoCD brings it back to 1 replica (as defined in Git) automatically.

> Self-heal reconciles within a few seconds to the default reconcile interval (~3 min). Click **Sync** in the UI if you want an immediate fix during the demo.

---

## Demo: Update via Git (the GitOps way)

1. Edit `apps/nginx/deployment.yaml` — change `replicas: 1` to `replicas: 2`
2. Commit and push to GitHub
3. ArgoCD detects the change and syncs automatically
4. Verify:
```bash
kubectl get pods -n nginx-demo
```

---

## About the Sync Policy

This demo ships with auto-sync enabled in `argocd/nginx-app.yaml`:

```yaml
spec:
  syncPolicy:
    automated:
      prune: true      # delete resources removed from Git
      selfHeal: true   # revert manual cluster changes back to Git
```

- **`selfHeal: true`** makes the drift demo above instant — manual changes are reverted automatically.
- **`prune: true`** means if you remove a manifest from `apps/nginx/` in Git, ArgoCD deletes the matching resource from the cluster.

To require a **manual** sync instead (click Sync in the UI), remove the `automated:` block and re-apply.

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
