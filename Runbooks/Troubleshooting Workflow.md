# Troubleshooting Workflow

## Purpose

This runbook provides a consistent troubleshooting process for homelab systems.

The goal is to identify the failing layer before changing configuration.

## 1. Define the Failure

Describe what is actually failing.

Bad:

"The server doesn't work."

Better:

"Ansible cannot establish SSH connectivity to the host."

Better still:

"The hostname resolves correctly and ICMP succeeds, but TCP/22 cannot be reached from the Ansible controller."

Specific symptoms reduce unnecessary changes.

## 2. Check the System

Determine whether the machine is running.

For virtual machines, verify the VM state from the virtualization platform.

## 3. Check Network Connectivity

Test basic connectivity.

```bash
ping <HOSTNAME>
```

If necessary, test the address directly.

If address connectivity works but hostname connectivity does not, investigate DNS before investigating routing.

## 4. Check DNS

```bash
dig <HOSTNAME>
```

or:

```bash
nslookup <HOSTNAME>
```

Confirm the returned address is expected.

## 5. Check the Path

Inspect routing:

```bash
ip route
```

Trace the path when useful:

```bash
traceroute <HOSTNAME>
```

Consider:

- Source VLAN
- Destination VLAN
- Gateway
- Firewall policy
- Return routing

## 6. Check the Port

A responding host does not guarantee that the required application is reachable.

Test the required service rather than relying only on ICMP.

## 7. Check the Service

On the target:

```bash
systemctl status <SERVICE>
```

Inspect recent logs:

```bash
journalctl -u <SERVICE> --since "30 minutes ago"
```

## 8. Check Automation

For Ansible problems:

```bash
ansible <HOSTNAME> -m ping
```

Then increase verbosity if required:

```bash
ansible <HOSTNAME> -m ping -vvv
```

Validate inventory:

```bash
ansible-inventory --graph
```

Validate playbook syntax:

```bash
ansible-playbook <PLAYBOOK> --syntax-check
```

## 9. Identify the Layer

Think in layers:

```text
Application
    ↑
Service
    ↑
Configuration
    ↑
Automation
    ↑
Authentication
    ↑
DNS
    ↑
Routing / Firewall
    ↑
Network
    ↑
Virtualization
    ↑
Hardware
```

Start at the lowest layer known to work and move upward.

## 10. Change One Thing

Avoid making several unrelated configuration changes simultaneously.

Make one justified change.

Test again.

This makes it possible to identify what actually fixed the problem.

## 11. Document the Result

Document unusual failures when the solution would be useful in the future.

Capture:

- Symptom
- Cause
- Verification
- Fix
- Lesson learned

Do not record credentials or other sensitive values in the troubleshooting note.
