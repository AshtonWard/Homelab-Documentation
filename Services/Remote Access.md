# Remote Access

## Purpose

Remote-access services provide controlled access to selected homelab resources when the operator is away from the local network.

Remote access should reduce the need to expose administrative interfaces directly to the Internet.

## Design Goals

Remote access should provide:

- Strong authentication
- Minimal public exposure
- Controlled access to internal resources
- Clear separation from general application publishing
- Revocable access

## Architecture

The preferred conceptual model is:

```text
Remote Device
     │
     ▼
Authenticated Remote-Access Layer
     │
     ▼
Authorized Internal Resources
```

Administrative services should not be made publicly reachable merely for convenience.

## Technologies

Overlay-network and zero-trust access technologies can be evaluated for this role.

The specific implementation may evolve as requirements change.

## Access Scope

Remote access should grant only the connectivity required for the intended task.

Examples include:

- Infrastructure administration
- Access to selected internal services
- Automation control

Access to one remote-access service should not imply unrestricted access to every network segment.

## Security

Protect:

- Authentication credentials
- Enrollment keys
- Device authorization
- Access policies
- Recovery information

Review authorized devices periodically.

## Documentation Boundary

Do not document live enrollment keys, public endpoints, complete access-control rules, or other information that would unnecessarily expose the remote-access design.

## Related Documentation

- Architecture/Network Architecture.md
- Standards/Documentation Standards.md
