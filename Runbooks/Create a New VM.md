# Create a New VM

## Purpose

This runbook describes the standard process for creating a Linux virtual machine in the homelab.

Two deployment methods are supported:

1. Automated deployment
2. Manual deployment

Automated deployment is preferred for normal operations.

## Before Deployment

Determine:

- VM name
- VM ID
- Network segment
- Address assignment
- Required Ansible groups
- Workload purpose
- Resource requirements

Do not create a VM before determining its network placement.

## Automated Deployment

The standard deployment workflow is:

```text
GitHub Workflow
      │
      ▼
Clone Template
      │
      ▼
Configure Cloud-Init
      │
      ▼
Start VM
      │
      ▼
Update Inventory
      │
      ▼
Wait for Connectivity
      │
      ▼
Run Bootstrap
```

Provide the required workflow inputs.

After completion, verify:

- VM is running.
- Network connectivity works.
- DNS resolution works.
- SSH connectivity works.
- Host appears in Ansible inventory.
- Bootstrap completes successfully.

## Manual Deployment

When automation is unavailable, the equivalent Proxmox process can be performed manually.

Example:

```bash
qm clone <TEMPLATE_ID> <VM_ID> --name <VM_NAME>

qm set <VM_ID> \
  --ipconfig0 ip=<IP_ADDRESS>/<PREFIX>,gw=<GATEWAY>

qm start <VM_ID>
```

## Verify Connectivity

```bash
ping <HOSTNAME>
```

Verify SSH:

```bash
ssh <HOSTNAME>
```

Verify DNS:

```bash
dig <HOSTNAME>
```

## Verify Ansible

```bash
ansible <HOSTNAME> -m ping
```

Inspect inventory:

```bash
ansible-inventory --graph
```

## Apply Configuration

Run the appropriate bootstrap or workload playbook.

Example:

```bash
ansible-playbook playbooks/bootstrap.yml \
  --limit <HOSTNAME>
```

Additional roles can then be applied according to the system's purpose.

## Completion

A deployment is complete when:

- VM is running
- Network configuration is correct
- DNS works
- SSH works
- Ansible can manage the host
- Baseline configuration has been applied
- Required workload configuration has been applied
- Security monitoring has been considered
