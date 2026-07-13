# Refresh Period

## Notes
- Work got busy — office move ate the time I'd normally put toward the roadmap. Studying paused after Week 5.
- Picking it back up now, but too much time passed to jump straight into Week 6 (Ansible) — taking 2 weeks to refresh Docker, K3s, and CI/CD before trusting that foundation again.
- This isn't new material — it's a deliberate review pass over Weeks 2–5, using my own notes as the primary source instead of starting from scratch.
- Homelab changed since March: the k3s cluster (zeus01–03) and the VMs it lived on were destroyed in a later migration — rebuilding it is part of this refresh, not just reading about it.
- Hardware also changed: 4 Proxmox nodes now instead of 2. atlas04 is a dedicated GPU/inference box (Astro/Ramon) — keep k3s off it. atlas03 is NFS/storage.

---

## Refresh Plan

### Docker
**Review:**
- `week-02.md` — images vs containers, Dockerfile layering, Compose, named volumes, port mapping, socket mounting
- Wiki `Docker-Homelab.md` — atlas-specific Dockerfile pattern, `.dockerignore`, exec-vs-shell CMD

**Lab:**
- Rebuild one Dockerfile from memory, diff against the real one in `atlas-lab/`
- `docker build` / `run` / `logs` / `exec -it`
- Named volume — kill container, confirm data survived
- Pull the 3 existing images from Docker Hub (`devopsjourneyman/atlas-dojo`, `atlas-nutrition-tracker`, `atlas-status` — still live) and run one locally

### K3s
**Review:**
- `week-03.md` + `week-04.md` — pods/deployments/services, ConfigMaps/Secrets, PVC vs PV, containerd-not-Docker gotcha, NodePort collisions
- `interview-prep/devops-interview-questions.md` Week 3 section — self-check from memory first
- Wiki `k3s-Cluster.md` + `k8s-Troubleshooting.md`
- `atlas-lab/docs/02-k3s-lab-prep.md` — original VM provisioning steps

**Lab:**
- Provision control-plane VM (4vCPU/8GB) on atlas01 or atlas02, worker VM (2vCPU/4GB) on the other
- Install k3s, join worker
- Redeploy `atlas-dojo` from Docker Hub — write Deployment + Service YAML from memory before checking old manifests
- Recreate the PVC for `atlas-nutrition-tracker`
- Deliberately trigger and fix one thing from the old troubleshooting notes

### CI/CD
**Review:**
- `week-05.md` — pipeline structure, secrets management, the secret-name-typo failure
- `interview-prep/devops-interview-questions.md` Week 5–6 section — self-check
- `atlas-dojo`/`atlas-nutrition-tracker` `.github/workflows/ci.yml` — read before touching

**Lab:**
- Push a trivial change, confirm the dormant pipeline still runs green
- Pull the freshly-built image onto the rebuilt k3s cluster, `k3s ctr images import`, `kubectl rollout restart`

---

## Next
Week 6 (Ansible) resumes once this refresh is done.
