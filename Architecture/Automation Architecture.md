# Automation Architecture

## Purpose

Automation is used to make infrastructure deployment repeatable and reduce manual configuration.

The primary automation components are:

- GitHub
- GitHub Actions
- Self-hosted GitHub runners
- Proxmox
- Ansible
- PowerShell
- Cloud-Init

Each component has a distinct responsibility.

## Automation Flow

```text
                    GitHub
                       │
                       ▼
                GitHub Actions
                       │
                       ▼
              Self-Hosted Runner
                       │
              ┌────────┴────────┐
              │                 │
              ▼                 ▼
           Proxmox          Repository
              │                 │
              ▼                 ▼
         Clone Template     Update Inventory
              │                 │
              └────────┬────────┘
                       ▼
                   Start VM
                       │
                       ▼
                Wait for Host
                       │
                       ▼
                    Ansible
                       │
              ┌────────┴────────┐
              │                 │
              ▼                 ▼
         Base Configuration   Roles
                                │
                     ┌──────────┼──────────┐
                     ▼          ▼          ▼
                  Docker     Security    Services
```

## GitHub

GitHub stores the automation source code and provides the interface used to initiate workflows.

Infrastructure operations should be represented as controlled workflows rather than arbitrary remote shell access whenever practical.

## GitHub Actions

GitHub Actions provides workflow orchestration.

Examples of workflow responsibilities include:

- Creating virtual machines
- Updating Ansible inventory
- Creating inventory groups
- Running Ansible playbooks
- Deploying Docker
- Applying security configuration

Workflow inputs should expose only the information required to perform the operation.

## Self-Hosted Runner

A self-hosted runner bridges GitHub Actions with resources inside the homelab.

This allows GitHub workflows to initiate internal automation without directly exposing internal management interfaces to the Internet.

Because the runner can perform privileged infrastructure operations, it should be treated as a sensitive administrative system.

## Proxmox

Proxmox is responsible for VM lifecycle operations.

The automation layer may perform operations such as:

1. Clone a standardized template.
2. Assign VM configuration.
3. Configure initial networking through Cloud-Init.
4. Start the VM.
5. Wait for the system to become reachable.

After the machine is available, responsibility transitions primarily to Ansible.

## Cloud-Init

Cloud-Init provides the initial configuration required for a new Linux system to become reachable.

Its role should remain relatively small.

Cloud-Init establishes the machine.

Ansible configures the machine.

## Ansible

Ansible is responsible for configuration management after provisioning.

The controller establishes SSH connections to managed targets and applies playbooks and roles.

Conceptually:

```text
                 Ansible Controller
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
      Linux Host    Docker Host   Proxmox Host
```

Managed systems do not need to run an Ansible agent.

## Inventory

Inventory defines which systems exist and how they are grouped.

Groups should represent useful configuration boundaries.

Examples:

```text
linux
docker
proxmox
security
test
```

Host-specific configuration belongs in host variables when appropriate.

Shared configuration belongs in group variables or role defaults.

## Roles

Reusable configuration should be implemented through Ansible roles.

Example responsibility separation:

```text
roles/
├── base
├── docker
├── security
└── service-specific roles
```

Roles should be idempotent whenever practical.

Running the same role multiple times should converge toward the desired state rather than repeatedly modifying an already-correct system.

## Secrets

Secrets must not be stored directly in playbooks, workflows, documentation, or plain-text inventory.

Sensitive values should use appropriate secret-management mechanisms such as:

- Ansible Vault
- GitHub Actions secrets
- Application-specific secret stores

Documentation may describe how a secret is used, but should never contain the secret itself.

## Design Goal

The long-term goal is for infrastructure operations to become increasingly declarative.

The operator should specify the desired result.

Automation should perform the repeatable implementation.
