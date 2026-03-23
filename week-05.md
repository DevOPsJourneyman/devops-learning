# Week 5 — CI/CD + GitHub Actions

## Notes
- First normal-schedule week of the roadmap. Expected to be the strongest week. 
  Mixed result — technical output was the best yet, foundation habits struggled.
- Building the CI/CD pipeline from scratch mid-week with zero deliverables 
  completed felt like a crisis. By Friday it was green and pushing to Docker Hub. 
  That turnaround is the pattern I need to trust.
- Secrets in GitHub Actions felt abstract until the pipeline failed because I 
  named the secret wrong. One typo, one failed run, concept locked in permanently.
- Santiago v2 (Hermes Agent) replaced the n8n bot. Claude Haiku via OpenRouter, 
  Telegram interface, SOUL.md personality file, morning and evening crons. 
  First AI agent I've built that feels like infrastructure, not a tutorial.
- Six weeks in. CI/CD is live. The pipeline connects everything — Git triggers 
  the build, Docker packages it, the registry stores it. The roadmap makes sense 
  as a system now.

---

## What I Built

### CI/CD Pipeline — atlas-dojo
GitHub Actions workflow triggered on push. Builds the Docker image and pushes 
to Docker Hub automatically. README badge shows live pipeline status.
- [atlas-dojo repo](https://github.com/DevOpsJourneyman/atlas-dojo)

### Santiago v2 — Hermes Agent
AI accountability bot rebuilt on hermes-lxc (Debian 12 LXC, atlas01). Claude 
Haiku via OpenRouter API. Telegram interface. SOUL.md personality file. 
Morning (7am) and evening (9:30pm) cron pings. Persistent data on TrueNAS via NFS.
- Live infrastructure — not a portfolio piece, runs daily

### Brainscape — 180 Cards Across 6 Starter Decks
Full wave 1 inventory rebuilt to Blueprint v1.1 standards:
kubectl · Git · Linux Fundamentals · Docker · Networking · CI/CD

### Claude Code Custom Skills
5 custom skills built in the atlas-lab namespace. Claude Code operational as 
a development accelerator.

---

## Key Concepts Learned
- GitHub Actions workflows — YAML structure, triggers, jobs, steps
- Secrets management — storing credentials in GitHub repo secrets, referencing 
  via `${{ secrets.NAME }}` syntax
- Docker Hub push from pipeline — authenticate, build, tag, push in automated sequence
- Status badges — linking build status to README for immediate visual credibility
- AI agent architecture — LLM API + Telegram gateway + personality files + cron scheduling
- Stateless compute reinforced — hermes-lxc is disposable, bot data lives on TrueNAS

---

## Pipeline Structure
```yaml
# Trigger
on: push

# Jobs
build:
  - checkout
  - login to Docker Hub (secrets)
  - build and push image
```

---

## Commands I Actually Used
```bash
# GitHub Actions — secrets referenced in YAML
${{ secrets.DOCKERHUB_USERNAME }}
${{ secrets.DOCKERHUB_TOKEN }}

# Verify pipeline triggered
# GitHub → repo → Actions tab → workflow run

# LXC management (Santiago v2 build)
pct create 201 ...           # Create hermes-lxc
pct start 201
pct enter 201

# Docker inside LXC
docker compose up -d
docker compose logs -f
```

---

## What Clicked
The pipeline is just a script that runs in the cloud on every push. Once that 
clicked, the YAML stopped looking like magic. Every `uses:` is a pre-built 
action someone else wrote. Every `run:` is a shell command. The complexity is 
just composition — small steps chained together, each one passing its result 
to the next.