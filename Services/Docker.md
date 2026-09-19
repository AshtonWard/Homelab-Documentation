# Docker

## Purpose

Docker provides the primary container runtime for application and service workloads in the homelab.

Containerization allows services to be deployed with their dependencies while keeping the underlying host relatively standardized.

## Responsibilities

Docker hosts provide:

- Container execution
- Image management
- Container networking
- Persistent volume attachment
- Docker Compose workloads
- A common runtime for self-hosted services

## Host Provisioning

Docker should be installed through configuration management where practical.

The expected lifecycle is:

```text
Linux VM
   ↓
Baseline Bootstrap
   ↓
Docker Role
   ↓
Docker Engine
   ↓
Compose / Service Deployment
```

This keeps Docker installation consistent across hosts.

## Docker Compose

Docker Compose is preferred for multi-container applications where appropriate.

Compose definitions should describe:

- Services
- Networks
- Volumes
- Dependencies
- Required configuration

Secrets should not be embedded directly into version-controlled Compose files.

## Persistent Data

Containers should be treated as replaceable.

Important state should live in deliberate persistent storage rather than only inside a container filesystem.

Before removing or rebuilding a container, identify whether it owns persistent data.

## Networking

Container networking should be designed around the service's actual requirements.

Publishing a port is not the same as authorizing network access.

Firewall policy and network segmentation remain separate controls.

## Management

Komodo can provide centralized visibility and management for Docker-based services.

Docker itself remains the underlying runtime.

## Security

Containerized does not mean isolated by default.

Consider:

- Image provenance
- Image updates
- Host privileges
- Volume mounts
- Published ports
- Secrets
- Container capabilities
- Host patching

## Documentation Boundary

Do not document secret environment variables, private registry credentials, tokens, or complete externally exposed endpoint inventories.

## Related Documentation

- Services/Komodo.md
- Architecture/Automation Architecture.md
- Runbooks/Bootstrap a New Linux Host.md
