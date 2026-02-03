# 🛡️ Sentinel

Infrastructure as Code for a self-hosted network backbone.

Built to learn. Documented to teach. Designed for production.

## What is this?

Sentinel automates the deployment of core network services on a Raspberry Pi (or any Ubuntu server). One command sets up DNS, VPN, monitoring, and secrets management.

```bash
ansible-playbook -i inventory/production.yml playbooks/watchtower.yml
```


15 minutes from bare Ubuntu to fully configured infrastructure.

Architecture

```
```
```
┌─────────────────────────────────────────────────────────────┐
│                        HOME NETWORK                         │
│                                                             │
│   ┌─────────────────────────────────────────────────────┐   │
│   │              Watchtower (Raspberry Pi)              │   │
│   │                                                     │   │
│   │   ┌───────────┐  ┌───────────┐  ┌───────────────┐   │   │
│   │   │  Pi-hole  │  │ WireGuard │  │     Vault     │   │   │
│   │   │    DNS    │  │    VPN    │  │    Secrets    │   │   │
│   │   │   :8080   │  │  :51820   │  │    :8200      │   │   │
│   │   └───────────┘  └───────────┘  └───────────────┘   │   │
│   │                                                     │   │
│   │   ┌───────────┐  ┌───────────┐  ┌───────────────┐   │   │
│   │   │Prometheus │  │  Grafana  │  │  Uptime Kuma  │   │   │
│   │   │  Metrics  │  │ Dashboards│  │   Monitoring  │   │   │
│   │   │   :9090   │  │   :3000   │  │    :3001      │   │   │
│   │   └───────────┘  └───────────┘  └───────────────┘   │   │
│   │                                                     │   │
│   └─────────────────────────────────────────────────────┘   │
│                              │                              │
│                              │ monitors                     │
│                              ▼                              │
│   ┌─────────────────────────────────────────────────────┐   │
│   │                   Forge (Proxmox)                   │   │
│   │                                                     │   │
│   │                 Debian + K3s + Apps                 │   │
│   │      (stock screener, Immich, NextCloud etc.)       │   │
│   │                                                     │   │
│   └─────────────────────────────────────────────────────┘   │
│                                                             │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               │ WireGuard VPN
                               ▼
                           INTERNET
                        (secure access)
```



Services
Service	Port	Purpose
Pi-hole	8080	DNS server + ad blocking
WireGuard	51820/udp	VPN for remote access
Vault	8200	Secrets management
Prometheus	9090	Metrics collection
Grafana	3000	Dashboards and visualization
Uptime Kuma	3001	Uptime monitoring and alerts

Quick Start
Prerequisites
-Ubuntu Server (22.04+) on Raspberry Pi or VM
-Ansible 2.15+ on your local machine
-SSH key access to target server

Deploy
# Clone repo
git clone https://github.com/paulmarketandtech/sentinel.git
cd sentinel/ansible

# Update inventory with your server IP
vim inventory/production.yml

# Run
ansible-playbook -i inventory/production.yml playbooks/watchtower.yml


Access Services
After deployment:

Service	URL
Pi-hole	http://YOUR_IP:8080/admin
Vault	http://YOUR_IP:8200
Grafana	http://YOUR_IP:3000
Uptime Kuma	http://YOUR_IP:3001


Project Structure

```
```
sentinel/
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   ├── staging.yml        # Proxmox VM for testing
│   │   └── production.yml     # Raspberry Pi
│   ├── playbooks/
│   │   └── watchtower.yml     # Main playbook
│   └── roles/
│       ├── common/            # Base packages, SSH, timezone
│       ├── dns/               # Pi-hole
│       ├── vpn/               # WireGuard
│       ├── vault/             # HashiCorp Vault
│       └── monitoring/        # Prometheus + Grafana + Uptime Kuma
├── docs/
│   ├── architecture.md
│   ├── decisions.md
│   └── setup/
│       └── watchtower.md
└── README.md 
```
```


Development Workflow
1. Make changes to roles
2. Test on staging VM
   ansible-playbook -i inventory/staging.yml playbooks/watchtower.yml
3. Deploy to production
   ansible-playbook -i inventory/production.yml playbooks/watchtower.yml

Tech Stack
Tool	Purpose
Ansible	Configuration management
Docker	Container runtime
Ubuntu Server	Base OS

Roadmap
```
```
[x] Core infrastructure (DNS, VPN, Monitoring, Vault)
[] Automated backups
[] Terraform for Proxmox VM provisioning
[] GitHub Actions CI/CD
[] Ansible Vault for secrets encryption
```
```


Lessons Learned
See decisions.md for detailed reasoning behind technical choices.

Key takeaways:

Test on staging before production
Small, focused roles are easier to maintain
Idempotency matters - run playbooks multiple times safely
Infrastructure as Code IS documentation


License
MIT
