# Architecture

## Overview

Sentinel consists of two main components:

1. **Watchtower** - Raspberry Pi running core network services
2. **Forge** - Proxmox server running applications (future)

This document focuses on Watchtower.

## Network Topology 
```
         Internet
            │
  UDP 51820 (port forward)
            ▼
┌─────────────────────────────────────────┐
│               Home Router               │
│               192.168.0.1               │
└─────────────────────────────────────────┘
                   │
├──────────────────┬──────────────────────┐
     │             │              │
     ▼             ▼              ▼
┌─────────┐ ┌─────────────┐ ┌─────────────┐
│    PC   │ │  Watchtower │ │    Forge    │
│         │ │     Pi      │ │   Proxmox   │
│  .0.71  │ │   .0.200    │ │    .0.25    │
└─────────┘ └─────────────┘ └─────────────┘
```

## Watchtower Components

### Container Architecture

All services run in Docker containers:

```
Docker Host (Watchtower)
│
├── pihole (DNS)
│ ├── port 53 (DNS)
│ └── port 8080 (web UI)
│
├── wireguard (VPN)
│ └── port 51820/udp
│
├── vault (secrets)
│ └── port 8200
│
├── prometheus (metrics)
│ └── port 9090
│
├── grafana (dashboards)
│ └── port 3000
│
├── uptime-kuma (monitoring)
│ └── port 3001
│
└── node-exporter (system metrics)
└── port 9100 
```

### Data Flow

**DNS Resolution:**
```
Client ──> Pi-hole ──> Upstream DNS (Cloudflare/Google)
│
└── blocks ads/trackers 
```

**VPN Access:**
```
Phone (outside) ──> Router:51820 ──> WireGuard ──> Home Network
│
└── uses Pi-hole for DNS 
```

**Monitoring:**
```
Node Exporter ──> Prometheus ──> Grafana
(collects) (stores) (displays) 
```

## Data Persistence

| Service | Data Location | What's Stored |
|---------|---------------|---------------|
| Pi-hole | /opt/pihole/etc-pihole | Config, blocklists |
| Vault | /opt/vault/data | Encrypted secrets |
| Grafana | /opt/monitoring/grafana | Dashboards, users |
| Prometheus | Docker volume | Time-series metrics |
| Uptime Kuma | Docker volume | Monitor configs |
| WireGuard | /opt/wireguard/config | Keys, peer configs |

## Security Considerations

1. **Vault** starts sealed after restart - requires manual unseal
2. **WireGuard** uses public/private key pairs - no passwords
3. **Pi-hole** admin password in Ansible defaults (change in production)
4. **No HTTPS** internally - acceptable for homelab, add reverse proxy for production 


