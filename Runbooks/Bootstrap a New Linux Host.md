# Bootstrap a New Linux Host

## Purpose

Bootstrap converts a newly provisioned Linux VM into a system ready for workload-specific configuration.

## Expected Starting State

The system should have:

- Supported Linux installation
- Working network connectivity
- DNS resolution
- SSH access
- Python available
- Ansible controller connectivity

The standardized VM template should already satisfy most prerequisites.

## Verify Connectivity

From the Ansible controller:

```bash
ansible <HOSTNAME> -m ping
```

If this fails, do not immediately modify the playbook.

First verify:

1. DNS
2. Routing
3. Firewall policy
4. SSH
5. Authentication
6. Inventory

## Verify Inventory

```bash
ansible-inventory --graph
```

Inspect a specific host:

```bash
ansible-inventory --host <HOSTNAME>
```

Use the appropriate Vault authentication mechanism when encrypted variables are required.

## Syntax Check

Before running a modified playbook:

```bash
ansible-playbook playbooks/bootstrap.yml \
  --syntax-check
```

## Bootstrap

Run the baseline configuration:

```bash
ansible-playbook playbooks/bootstrap.yml \
  --limit <HOSTNAME>
```

## Apply Workload Configuration

After bootstrap, apply the role appropriate for the system.

Examples may include:

- Docker
- Security monitoring
- Application services
- Infrastructure tooling

Avoid placing every possible package or service in the base configuration.

The base role should establish a consistent platform.

Workload roles should establish the machine's purpose.

## Verify

Confirm:

- Playbook completes successfully.
- Re-running the playbook does not create unexpected changes.
- Required services are running.
- DNS still resolves correctly.
- The host is reachable.
- Security monitoring is functioning where applicable.

## Failure Principle

When bootstrap fails, identify which layer failed before changing code.

```text
Provisioning
    ↓
Network
    ↓
DNS
    ↓
SSH
    ↓
Inventory
    ↓
Ansible
    ↓
Role
    ↓
Application
```

Troubleshoot from the bottom-most dependency upward.
