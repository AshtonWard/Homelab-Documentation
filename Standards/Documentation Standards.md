# Documentation Standards

## Purpose

Homelab documentation should make the environment easier to understand, operate, troubleshoot, and rebuild without unnecessarily exposing sensitive operational information.

Assume that any documentation could eventually be shared outside of the environment.

## Document

Documentation should include:

- Architecture
- Design decisions
- Installation procedures
- Troubleshooting procedures
- Generic commands
- Lessons learned
- Service relationships
- Automation concepts
- Recovery procedures
- Known limitations
- Relevant technology choices

## Do Not Document

General documentation should not contain:

- Passwords
- API tokens
- Personal access tokens
- Private SSH keys
- Recovery codes
- Ansible Vault passwords
- Application onboarding keys
- Authentication cookies
- Secret environment variables
- Complete firewall rule sets
- Complete host/IP/port maps
- Sensitive externally reachable endpoints

## Addresses

Exact addresses should only be included when they provide meaningful operational value.

Prefer:

```text
<PROXMOX_HOST>
<ANSIBLE_CONTROLLER>
<SERVICE_HOST>
<IP_ADDRESS>
<GATEWAY>
```

when documenting reusable procedures.

## Screenshots

Before committing screenshots, check for:

- Credentials
- Tokens
- Browser address bars
- Internal addressing
- Public addressing
- Usernames
- Email addresses
- DNS records
- Session information
- Infrastructure maps

Crop or redact screenshots where appropriate.

## Commands

Prefer reusable commands:

```bash
qm clone <TEMPLATE_ID> <VM_ID> --name <VM_NAME>
```

instead of commands containing live environment values.

Commands copied from troubleshooting sessions should be reviewed before being committed.

## Secrets

Private repositories are not secret-management systems.

Sensitive values should remain in the appropriate secret store even when the repository itself is private.

## Historical Documentation

Old documentation can still be useful.

When information no longer represents the current environment, mark it clearly as historical rather than silently presenting it as current.

Suggested notice:

> [!NOTE]
> Historical configuration. Retained for reference and troubleshooting.

## Principle

Document enough information to explain and reproduce the design.

Avoid creating a single document that provides every detail necessary to map the environment.
