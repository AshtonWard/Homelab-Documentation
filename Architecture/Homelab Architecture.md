# Homelab Architecture

## Purpose

The homelab is a segmented environment used for infrastructure engineering, networking, automation, security, containerization, monitoring, and general technical experimentation.

The environment is designed around several principles:

- Infrastructure as code where practical
- Network segmentation
- Repeatable deployments
- Centralized automation
- Centralized security monitoring
- DNS-based service discovery
- Separation of infrastructure and application workloads
- Safe experimentation without impacting core infrastructure

This document intentionally describes the environment at a logical level. Exact addresses, credentials, authentication material, and detailed security rules are maintained outside of general documentation.

## High-Level Architecture

```text
                         Internet
                            │
                            ▼
                     Firewall / Router
                            │
                            ▼
                      Core Switching
                            │
        ┌───────────────────┼────────────────────┐
        │                   │                    │
        ▼                   ▼                    ▼
   Management           Services             Security
        │                   │                    │
        ├──────┐       ┌────┴────┐          Monitoring
        │      │       │         │
        ▼      ▼       ▼         ▼
     Proxmox Proxmox  Docker   Internal
      Node    Node    Services  Services
        │
        ▼
   Virtual Machines
        │
        ├── Automation
        ├── Security
        ├── Monitoring
        ├── Application Services
        ├── Game Servers
        └── Lab Systems
```

## Core Infrastructure

### Virtualization

Proxmox VE provides the primary virtualization platform.

Multiple Proxmox nodes are used to host virtual machines, containers, and supporting infrastructure.

A standardized Linux VM template provides a common starting point for new systems. Cloud-Init is used for initial machine configuration where appropriate.

## Network Edge

OPNsense provides routing and firewall functionality between network segments.

Its responsibilities include:

- Inter-VLAN routing
- Firewall policy enforcement
- DHCP services
- DNS integration
- Network segmentation
- Controlled communication between trust zones

Firewall rules should follow least-privilege principles where practical.

## Switching

Cisco switching provides physical network connectivity and VLAN transport.

The switching layer supports:

- 802.1Q VLAN trunking
- Access VLANs
- VLAN-aware virtualization hosts
- Network segmentation
- Infrastructure connectivity

Switch configuration should remain intentionally separate from general architecture documentation when it contains environment-specific operational details.

## Automation

Ansible is the primary configuration-management platform.

GitHub Actions provides workflow orchestration and allows infrastructure tasks to be initiated remotely through controlled workflows.

Typical deployment flow:

```text
GitHub
   │
   ▼
GitHub Actions
   │
   ▼
Self-Hosted Runner
   │
   ├── Proxmox Provisioning
   │
   ▼
Virtual Machine
   │
   ▼
Ansible
   │
   ▼
Configured System
```

## Security

Security monitoring is centralized through Wazuh.

Endpoints can run Wazuh agents that report to the central security platform.

Security workloads are separated from general application workloads through network segmentation.

Sensitive security configuration should not be reproduced in general documentation.

## Containers and Services

Containerized applications are primarily managed using Docker.

Komodo provides container and server management capabilities.

Service workloads are separated from core management infrastructure whenever practical.

## Observability

Monitoring services provide visibility into infrastructure health, virtualization resources, and application availability.

The observability stack may evolve as different monitoring platforms are evaluated.

## Design Philosophy

The homelab should be treated as a small infrastructure environment rather than a collection of unrelated servers.

New systems should ideally follow the same lifecycle:

1. Define the workload.
2. Determine the appropriate network segment.
3. Provision the system.
4. Register it with automation.
5. Apply baseline configuration.
6. Apply workload-specific configuration.
7. Enable security monitoring.
8. Add operational documentation where useful.

The goal is repeatability without making experimentation unnecessarily rigid.
