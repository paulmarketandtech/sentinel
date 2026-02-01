# Watchtower Setup Guide

Step-by-step guide to deploy Watchtower on a Raspberry Pi.

## Prerequisites

### Hardware
- Raspberry Pi 4 (2GB+ RAM recommended)
- SD card (32GB+)
- Ethernet connection (recommended)

### Software
- Ubuntu Server 24.04 LTS (64-bit)
- Ansible 2.15+ on your local machine

## Step 1: Install Ubuntu on Pi

1. Download Ubuntu Server for Pi:
   https://ubuntu.com/download/raspberry-pi

2. Flash to SD card using Raspberry Pi Imager

3. Boot Pi and find its IP:
   ```bash
   nmap -sn 192.168.0.0/24

4. SSH in (default: ubuntu/ubuntu):
   ssh ubuntu@<PI_IP>

5. Create your user:
   sudo adduser paul
   sudo usermod -aG sudo paul
   sudo visudo
   # Add: paul ALL=(ALL) NOPASSWD: ALL

6. Copy SSH key from your machine:
   ssh-copy-id paul@<PI_IP>

## Step 2: Configure Inventory

Edit ansible/inventory/production.yml:
all:
  children:
    watchtower:
      hosts:
        watchtower-prod:
          ansible_host: 192.168.0.200  # your Pi IP
          ansible_user: paul 


## Step 3: Deploy

cd ansible
ansible-playbook -i inventory/production.yml playbooks/watchtower.yml

# Takes about 15 minutes.


## Step 4: Post-Deployment 

Initialize Vault 

```bash

```
ssh paul@<PI_IP>
docker exec -it vault vault operator init



Save the unseal keys and root token securely.

Unseal Vault:
docker exec -it vault vault operator unseal <key1>
docker exec -it vault vault operator unseal <key2>
docker exec -it vault vault operator unseal <key3> 

Get WireGuard Config 
docker exec wireguard /app/show-peer 1 

Scan QR code with WireGuard app.

Configure Router
Forward UDP port 51820 to your Pi's IP.

Step 5: Verify Services
Service	URL	Default Login
Pi-hole	http://PI_IP:8080/admin	changeme
Vault	http://PI_IP:8200	root token
Grafana	http://PI_IP:3000	admin/changeme
Uptime Kuma	http://PI_IP:3001	create on first visit

# Updating
Pull latest changes and re-run: 

git pull
ansible-playbook -i inventory/production.yml playbooks/watchtower.yml 


# Troubleshooting
Service not running
ssh paul@<PI_IP>
docker ps -a
docker logs <container_name> 

Re-deploy single role 
# Edit playbook temporarily to include only one role, or:
ansible-playbook -i inventory/production.yml playbooks/watchtower.yml --tags dns 

