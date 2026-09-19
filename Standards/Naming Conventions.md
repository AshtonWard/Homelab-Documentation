# Naming Conventions

## Purpose

Consistent naming makes infrastructure easier to identify, automate, and troubleshoot.

Names should describe the system's role without requiring a separate lookup.

## Hostnames

Preferred format:

`<role><instance>`

Examples:

```text
pve01
pve02
ansible01
wazuh01
komodo01
```

The numeric suffix allows additional instances to be created without changing the naming model.

## Test Systems

Temporary or test systems may include a descriptive prefix.

Example:

`bootstrap-test01`

Names should still identify the system's purpose.

Avoid meaningless names such as:

```text
server1
ubuntu2
testvm
newvm
```

## DNS

Infrastructure should use consistent internal DNS names.

Applications and automation should prefer hostnames or FQDNs over hard-coded addresses whenever practical.

Exact production DNS records do not need to be duplicated in documentation.

## Ansible Groups

Ansible groups should describe a configuration role.

Examples:

```text
linux
docker
proxmox
security
test
```

A host may belong to multiple groups.

Group membership should answer:

"What configuration should this machine receive?"

rather than simply:

"What kind of machine is this?"

## GitHub Actions

Workflow names should describe the action being performed.

Examples:

```text
create-new-vm
create-new-group
run-playbook
```

Avoid names tied to a temporary implementation when the workflow represents a broader operation.

## General Rules

Names should be:

- Short
- Predictable
- Descriptive
- Automation-friendly
- Lowercase where practical
- Free of spaces for infrastructure identifiers

Consistency is more valuable than clever naming.
