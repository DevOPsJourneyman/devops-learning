# DevOps Interview Questions — El Capitán Roadmap

*Living document — updated as roadmap progresses*

---

## Week 3 — Kubernetes Fundamentals

### Q: What's the difference between a Pod and a Deployment?

**A:** A Pod is just a running container (or group of containers). A Deployment is a controller that ensures the declared state — if a pod crashes or a node goes down, Kubernetes automatically reconciles back to the desired replica count. In production, you almost never create Pods directly; you create Deployments.

---

### Q: What happens if a pod crashes in production?

**A:** The Deployment controller detects the replica count dropped below desired state and immediately schedules a replacement. The new pod can land on any healthy node. Traffic continues flowing through the Service to the remaining pods during recovery.

---

### Q: Explain ClusterIP vs NodePort vs LoadBalancer.

**A:** 
- **ClusterIP** — internal only, pods inside the cluster can reach it
- **NodePort** — exposes the service on every node's IP at a static port (30000-32767), accessible from outside the cluster
- **LoadBalancer** — provisions a real external IP via cloud provider (AWS/GCP/Azure) or requires something like MetalLB on bare metal

"NodePort works anywhere. LoadBalancer requires cloud integration."

---

### Q: What is a Service in Kubernetes?

**A:** Services are stable abstractions — they don't run or crash. They're routing rules that persist in etcd. The pods behind them can come and go; the Service just finds whatever pods match its selector via labels.

---

### Q: What's the difference between ConfigMap and Secret?

**A:** ConfigMap is for non-sensitive configuration (app settings, feature flags). Secret is for sensitive data (passwords, API keys, tokens). Both inject values into pods as environment variables or mounted files. Secrets are base64 encoded — not encrypted. In production, you'd use an external secrets manager like HashiCorp Vault.

---

### Q: Why use Kubernetes instead of just Docker Compose?

**A:** Docker Compose runs containers on a single host — if that host dies, everything dies. Kubernetes spreads workloads across multiple nodes and self-heals. It handles scheduling, scaling, rolling updates, and recovery automatically. Compose is for local dev; Kubernetes is for production resilience.

---

### Q: What is Helm and why would you use it?

**A:** Helm is a package manager for Kubernetes. Instead of writing all the manifests manually, I can deploy complex apps with one command and customize them via values. A Helm chart bundles Deployments, Services, ConfigMaps — everything an app needs — into a reusable package.

---

### Q: How do you perform a rolling update?

**A:** Update the Deployment's image with `kubectl set image` and Kubernetes handles the rolling update — zero downtime. It spins up new pods with the new spec before killing the old ones. If something breaks, `kubectl rollout undo` reverts to the previous version instantly.

---

### Q: How do you troubleshoot a pod that won't start or isn't working?

**A:** Follow this flow:
1. `kubectl get pods` — is it Running, Pending, CrashLoopBackOff?
2. `kubectl describe pod <name>` — check Events for scheduling issues, image pull errors
3. `kubectl logs <pod>` — see what the app is actually doing

The logs tell the truth. Pods can show "Running" but the app inside might be misconfigured.

---

### Q: What's a Namespace and why use them?

**A:** A logical boundary inside the cluster. Resources in different namespaces are isolated by default. Use them to separate environments (dev/staging/prod) or teams. You can delete an entire namespace and everything inside it goes — clean separation.

---

### Q: What does `kubectl apply -f` do?

**A:** It sends the YAML manifest to the Kubernetes API server, which creates or updates the resources defined in the file. Every kubectl command is an API call under the hood.

---

### Q: Why do Deployments use random pod names?

**A:** Pods are cattle, not pets. They're disposable. You don't name them because you don't care *which* pod handles traffic — you care that *enough* pods exist. The Deployment manages them; you manage the Deployment.

---

---

## Week 5–6 — CI/CD & GitHub Actions

### Q: What is CI/CD and why does it matter?

**A:** CI (Continuous Integration) means every code push is automatically built and tested. CD (Continuous Delivery/Deployment) means a verified build is automatically pushed to a registry or deployed. It matters because it removes manual steps from the release process — catching broken builds before they reach production and making deployments repeatable and auditable.

---

### Q: Describe a CI/CD pipeline you've built. What does each stage do?

**A:** I built a three-job GitHub Actions pipeline for atlas-dojo:
1. **Lint** — hadolint analyses the Dockerfile for bad practices before anything builds
2. **Build & Test** — image is built, container is started, curl smoke tests the app is actually responding
3. **Push** — only runs if build and test pass; authenticated push to Docker Hub

Each job uses `needs:` to chain dependencies — push never runs if the smoke test fails.

---

### Q: How do you store secrets in a CI/CD pipeline?

**A:** GitHub repository secrets — stored encrypted in GitHub, referenced in the workflow YAML as `${{ secrets.SECRET_NAME }}`. Never hardcoded in the workflow file. In my pipeline, Docker Hub credentials are stored as `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` and injected at the push stage only.

---

### Q: What's the difference between a job and a step in GitHub Actions?

**A:** A step is a single command or action within a job. A job is a group of steps that run together on the same runner. Jobs run in parallel by default — use `needs:` to make them sequential. Steps within a job always run sequentially and share the same filesystem.

---

### Q: What is `actions/checkout` and why is it in every job?

**A:** It clones the repository onto the runner so the job has access to the code. Each job gets a fresh runner with nothing on it — if you forget checkout, the job can't see your files. It needs to be in every job that touches the codebase.

---

### Q: What does `push: false` with `load: true` do in a Docker build action?

**A:** Builds the image and loads it into the runner's local Docker daemon without pushing it to a registry. Used in the build-and-test job so the smoke test can run the container locally — you need the image available before you've verified it works.

---

### Q: Why use `curl --fail` in a smoke test?

**A:** Without `--fail`, curl returns exit code 0 even on HTTP error responses (404, 500). `--fail` makes curl return a non-zero exit code on server errors, which causes the pipeline step to fail. Without it, a broken app would pass the smoke test as long as the server responds with anything.

---





## Quick Reference — Commands That Impress

```bash
kubectl get pods -A                    # All namespaces at once
kubectl describe pod <name>            # Deep dive + events
kubectl logs -l app=<label>            # Logs by label selector
kubectl exec -it <pod> -- env          # Inspect env vars inside
kubectl rollout restart deployment/x   # Force redeploy
kubectl rollout undo deployment/x      # Instant rollback
helm install <name> <chart> -n <ns>    # One-command deploy
```

---

## Quick Reference — CI/CD Commands & Syntax
```yaml
# Job dependency chain
jobs:
  push:
    needs: build-and-test   # Only runs if build-and-test passes

# Reference a secret
${{ secrets.DOCKERHUB_TOKEN }}

# Build without pushing (for smoke test)
uses: docker/build-push-action@v5
with:
  push: false
  load: true
  tags: myapp:test

# Smoke test pattern
run: docker run -d -p 5000:5000 --name test myapp:test
run: sleep 3
run: curl --fail http://localhost:5000
run: docker stop test
```

---

*Last updated: Week 6 — 23 March 2026*
