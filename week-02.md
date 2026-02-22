# Notes for the week

* Spent time going over Week 1. Successes.  Support system needs work to keep the roadmap on track. 
* This week will be about continuing my Docker eduation.  I have the basics down.
* I want to create a flash app which is a food tracker. 




## Docker install
* Studied the basic definitions of Docker items.
* Installed docker on zeus01 using terminal.  The VM has no head.
* Instructions I used to install:
#### Update package index
sudo apt update

#### Install prerequisites (lets apt use HTTPS repos)
sudo apt install -y ca-certificates curl gnupg

#### Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

#### Add Docker's repo to apt sources
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

#### Install Docker Engine + Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

#### Add yourself to docker group (so you don't need sudo every time)
sudo usermod -aG docker zeus

#### Apply group change
newgrp docker
