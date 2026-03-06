# Week 3 — Kubernetes Fundamentals

## What I Built

- 3-node k3s cluster: zeus01 (control plane), zeus02 + zeus03 (workers)
- Deployed nginx and atlas-dojo via YAML manifests
- Configured Services, ConfigMaps, Secrets, Namespaces
- Installed Helm and deployed nginx chart

## Key Concepts

| Term | What it is |
|------|-----------|
| Pod | Smallest deployable unit — one or more containers |
| Deployment | Manages pods, ensures replicas, handles updates |
| Service | Stable network endpoint — routes traffic to pods |
| ConfigMap | Non-sensitive config injected into pods |
| Secret | Sensitive data (passwords, keys) — base64 encoded |
| Namespace | Logical isolation — separate dev/staging/prod |
| Helm | Package manager — deploy complex apps from charts |

## Biggest Learnings

- **Self-healing**: Delete a pod, Deployment recreates it instantly. That's the point — declare desired state, K8s maintains it.
- **Docker vs Kubernetes**: Docker runs containers. Kubernetes orchestrates them — scheduling, scaling, networking, recovery.
- **Troubleshooting flow**: `kubectl get pods` → `kubectl describe pod` → `kubectl logs`. Logs told me atlas-status was on port 5000, not 5003.

## Commands I Actually Used
```bash
kubectl get pods -A              # All pods, all namespaces
kubectl describe pod <name>      # Detailed state + events
kubectl logs <pod>               # Container output
kubectl apply -f <file>          # Create/update from manifest
kubectl delete -f <file>         # Remove what's in the manifest
kubectl exec -it <pod> -- env    # Check env vars inside pod
kubectl rollout restart deployment/<name>  # Force redeploy
helm install <name> <chart>      # Deploy from Helm chart
```

## What Clicked

The separation of concerns: Deployments manage pods, Services route traffic, ConfigMaps/Secrets handle config. Each piece does one job. Breaking things and watching K8s fix them made it real.
