# Week 4 — Kubernetes Workloads + Infrastructure

## Notes
- Deploying real apps (not just nginx) made the concepts stick. Writing the 
  PVC manifest myself and watching it bind was different from following a tutorial.
- The Docker socket limitation on k3s was a genuine surprise. atlas-status 
  worked perfectly in Compose, broken in Kubernetes. That's a real-world 
  production problem, not a lab exercise.
- Writing manifests from scratch (with hints available) is better than copying 
  examples. The YAML indentation errors were frustrating but now I understand 
  why indentation *is* the structure.
- Santiago went down when the Pi lost its PSU. Rebuilt on an LXC container in 
  under 30 minutes because the data lived on TrueNAS. That's stateless compute 
  in practice — container is disposable, data survives.
- Four weeks in and haven't quit. Git, Docker, Kubernetes — tangible skills that 
  are already changing how I think about my current role.

## What I Built
- Deployed atlas-status, atlas-nutrition-tracker, and atlas-dojo as K8s workloads
- Wrote PVC manifest for atlas-nutrition-tracker SQLite persistence
- Debugged NodePort conflict using `kubectl get services -A`
- Rebuilt Santiago (n8n) on atlas01 LXC after Pi PSU failure — NFS mount 
  preserved all workflows and data
- Updated kubernetes/README.md with all deployed apps and NodePort map

## Key Concepts

| Term | What it is |
|---|---|
| PersistentVolumeClaim | Request for storage — decouples data from pod lifecycle |
| PersistentVolume | The actual storage (directory on node, NFS share, cloud disk) |
| local-path provisioner | k3s built-in — automatically creates PVs from PVCs |
| NodePort | Exposes service on every node at a static port (30000-32767) |
| containerd | k3s runtime — not Docker. Docker socket doesn't exist on nodes. |

## Biggest Learnings
- **PVC vs PV:** PVC is the request, PV is the actual storage. k3s handles 
  the PV automatically via local-path provisioner — you just write the PVC.
- **Docker Compose → Kubernetes translation:** `volumes:` becomes a PVC + 
  volumeMount. `environment:` becomes env vars or ConfigMap. `restart: unless-stopped` 
  is handled by the Deployment controller automatically.
- **Runtime matters:** k3s uses containerd, not Docker. Apps that depend on 
  the Docker socket break silently — pods run but app fails. Always check what 
  runtime your cluster uses.
- **Stateless compute:** Santiago rebuilt in 30 minutes because compute was 
  separate from state. Same principle as K8s PVCs — pods are cattle, data is not.

## Commands I Actually Used
```bash
kubectl get services -A          # Check NodePorts across ALL namespaces
kubectl apply -f pvc.yaml        # Create persistent storage claim
kubectl describe pod <name>      # Debug pending pods — check Events section
kubectl get pods -w              # Watch pods come up in real time
pct create <id> ...              # Create LXC container on Proxmox
pct start / pct enter <id>       # Start and shell into LXC
```

## What Clicked
Storage in Kubernetes mirrors the same principle as TrueNAS in the homelab — 
keep data off the compute layer. When the Pi died, Santiago survived because 
data lived on TrueNAS. When a pod dies, app data survives because it lives in 
a PVC. Same pattern, different scale.
