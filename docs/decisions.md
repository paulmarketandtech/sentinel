# Decision Log

Technical decisions made during this project and why.

## Why Ansible over other tools?

| Option | Verdict | Reason |
|--------|---------|--------|
| Ansible | ✅ Chose | Agentless, simple YAML, good for bare metal |
| Terraform | ❌ Not for this | Better for cloud provisioning, not config management |
| Puppet/Chef | ❌ Overkill | Requires agents, more complex |
| Bash scripts | ❌ Not maintainable | No idempotency, hard to read |

## Why Docker for services?

**Considered:**
1. Bare metal installation
2. Docker containers
3. Kubernetes (K3s)

**Decision:** Docker

**Reasoning:**
- Pi-hole, Vault, etc. have official Docker images
- Easy rollback (just change image tag)
- Isolation between services
- K3s adds complexity not needed for single-node
- Bare metal makes updates/rollbacks painful

## Why Ubuntu over other distros?

| Option | Verdict | Reason |
|--------|---------|--------|
| Ubuntu Server | ✅ Chose | Best ARM support, LTS, huge community |
| Debian | ⚪ Good | Stable but older packages |
| Arch | ❌ No | Rolling release breaks automation |
| Alpine | ❌ No | Too minimal, debugging harder |

## Why Pi-hole over AdGuard Home?

Both are good. Chose Pi-hole because:
- More documentation available
- Larger community
- I wanted to learn it specifically

AdGuard Home advantages (for future consideration):
- Modern UI
- Built-in DoH/DoT
- Single binary

## Why WireGuard over OpenVPN?

| Aspect | WireGuard | OpenVPN |
|--------|-----------|---------|
| Speed | Faster | Slower |
| Config | Simple | Complex |
| Code | 4,000 lines | 100,000+ lines |
| Mobile | Better battery | More battery drain |

Easy choice.

## Why HashiCorp Vault?

**Purpose:** Centralized secrets for future Forge apps.

**Alternatives considered:**
- Environment variables - not secure, scattered
- Ansible Vault - good for Ansible secrets, not runtime
- Bitwarden - for human passwords, not app secrets

**Vault gives us:**
- API access for apps
- Audit logs
- Secret rotation (future)
- Industry standard

## Staging/Production Workflow

**Decision:** Develop on Proxmox VM, deploy to Pi.

**Why:**
- Break things without affecting real network
- Faster iteration (VM snapshots)
- Same code works on both (that's the point of IaC)

## Directory Structure

**Decision:** Role-based structure with separation.

roles/
├── common/ # shared setup
├── dns/ # Pi-hole only
├── vpn/ # WireGuard only
└── monitoring/ # all monitoring together 

**Why separate:**
- Can disable individual services
- Easier to test one role
- Clear ownership of tasks

**Why monitoring together:**
- Prometheus + Grafana + Uptime Kuma are related
- Usually deploy together
- Shared docker-compose makes networking easier


