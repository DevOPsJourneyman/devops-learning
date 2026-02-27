# Week 2 — Docker Fundamentals

## Notes
* Spent time going over Week 1. Successes. Support system needs work to keep 
  the roadmap on track.
* This week will be about continuing my Docker education. I have the basics down.
* I want to create a Flask app which is a food tracker.
* It's been a very productive week and I feel great about it.  3 apps that I use daily and a refurb for the home lab.  Feeling good going into week 3. 

---

## What I Built

### atlas-nutrition-tracker
A meal prep tracking app built with Flask. Manages daily meals, ingredient 
stock, shopping lists and food costs. Containerised with Docker, orchestrated 
with Docker Compose, data persisted with named volumes.
- [Repo link]

### atlas-dojo
A spaced repetition training tool using the SM-2 algorithm for DevOps interview 
prep. Contains 40+ cards covering Docker and GitHub fundamentals.
- [Repo link]

### atlas-status
A real-time container monitoring dashboard. Uses Docker socket mounting to 
connect directly to the Docker daemon and display live container stats.
- [Repo link]

All three apps pushed to Docker Hub under devopsjourneyman.

---

## Key Concepts Learned
- Docker images vs containers — image is the blueprint, container is the running instance
- Dockerfiles — layered build instructions, cache optimisation
- Docker Compose — multi-container orchestration with a single YAML file
- Named volumes — data persistence across container restarts
- Port mapping — exposing container ports to the host
- Docker socket mounting — giving a container access to the Docker daemon
- Docker Hub — pushing and pulling images from a public registry

---

## Docker Install

Installed Docker Engine on zeus01 (Ubuntu Server 24, headless) via terminal.
```bash
# Update package index
sudo apt update

# Install prerequisites
sudo apt install -y ca-certificates curl gnupg

# Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add Docker repo to apt sources
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine + Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Add user to docker group
sudo usermod -aG docker zeus
newgrp docker
```