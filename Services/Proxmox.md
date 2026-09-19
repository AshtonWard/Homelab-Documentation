# Proxmox

## Purpose

Proxmox VE is the primary virtualization platform for the homelab.

It provides the compute layer used to host virtual machines, containers, and supporting infrastructure.

This document describes Proxmox's role in the environment rather than maintaining a complete inventory of nodes, addresses, or VM assignments.

## Responsibilities

Proxmox is responsible for:

- Virtual machine lifecycle management
- Linux container hosting where appropriate
- Virtual networking
- Storage presentation to workloads
- Resource allocation
- VM templates
- Snapshots and operational recovery features
- Providing the compute target for infrastructure automation

## Node Model

The environment uses multiple Proxmox nodes.

Nodes may have different hardware capabilities and therefore do not need to host identical workloads.

Workload placement should consider:

- CPU requirements
- Memory requirements
- Storage requirements
- Network requirements
- Hardware passthrough
- Availability requirements

## VM Templates

Standardized templates are used to reduce repeated installation work.

A Linux template provides a known starting point with the packages and configuration required for automated deployment.

The intended lifecycle is:

```text
Template
   ↓
Clone
   ↓
Cloud-Init
   ↓
Boot
   ↓
Ansible Bootstrap
   ↓
Workload Configuration
```

Templates should remain relatively generic.

Application-specific configuration belongs in Ansible or the application's deployment process.

## Networking

Proxmox participates in the VLAN-aware network design.

Virtual machines should be connected to the network segment appropriate for their role.

The virtualization layer should transport VLANs without weakening the segmentation enforced by the switching and firewall layers.

## Automation

Proxmox VM provisioning can be initiated through GitHub Actions.

The automation layer handles initial VM creation and then transitions configuration responsibility to Ansible.

This separation keeps VM lifecycle management distinct from operating-system configuration.

## Administration

Management interfaces belong to the management plane.

Administrative access should not be unnecessarily exposed to general client or application networks.

## Documentation Boundary

Do not maintain a complete VM-to-IP map, authentication information, or detailed management endpoint inventory in this document.

Operational values should remain in the systems responsible for managing them.

## Related Documentation

- Architecture/Homelab Architecture.md
- Architecture/Network Architecture.md
- Architecture/Automation Architecture.md
- Runbooks/Create a New VM.md
