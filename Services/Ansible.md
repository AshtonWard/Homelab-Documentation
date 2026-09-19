# Ansible

## Purpose

Ansible is the primary configuration-management platform for the homelab.

It converts newly provisioned systems into consistently configured infrastructure and applies workload-specific configuration through reusable playbooks and roles.

## Controller and Targets

The environment follows a controller-to-target model.

```text
             Ansible Controller
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
   Linux Host   Docker Host   Infrastructure
```

Managed Linux systems generally do not require an Ansible agent.

## Responsibilities

Ansible is used for:

- Baseline operating-system configuration
- Package installation
- Docker installation
- Security-agent deployment
- Service configuration
- Repeatable administrative tasks

## Inventory

Inventory describes managed systems and their group membership.

Groups should represent meaningful configuration boundaries.

Host-specific values belong in host variables when appropriate.

Shared configuration belongs in group variables, role defaults, or other appropriate reusable locations.

## Playbooks

Playbooks define the desired operation.

Examples include:

- Bootstrap a system
- Apply security configuration
- Configure a Docker host
- Perform a repeatable administrative action

## Roles

Roles separate reusable responsibilities.

A role should generally answer:

> What capability should this system have?

rather than:

> What commands did I happen to run on this machine?

## Idempotency

Automation should converge toward the desired state.

Running a playbook again should normally result in few or no changes when the target is already configured correctly.

Unexpected repeated changes should be investigated.

## Secrets

Sensitive Ansible variables should be encrypted using an appropriate mechanism such as Ansible Vault.

Vault passwords and decrypted secret values must not be committed to documentation.

## GitHub Actions Integration

GitHub Actions can initiate Ansible operations through the self-hosted runner.

This provides a controlled method for performing infrastructure operations when direct access to the homelab is unavailable.

## Troubleshooting Order

For an unreachable Ansible target, check:

1. Target state
2. Network connectivity
3. DNS
4. Routing and firewall policy
5. SSH
6. Authentication
7. Inventory
8. Ansible configuration
9. Playbook or role logic

## Related Documentation

- Architecture/Automation Architecture.md
- Runbooks/Bootstrap a New Linux Host.md
- Runbooks/Troubleshooting Workflow.md
- Setups/Ansible/Ansible Crash Course.md
