# Komodo

## Purpose

Komodo provides centralized management and orchestration for Docker-based infrastructure.

It complements Ansible rather than replacing it.

Ansible prepares and configures the host. Komodo manages container-oriented operations after the host is ready.

## Architecture

The deployment consists conceptually of:

```text
                  Komodo Core
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
         Periphery  Periphery  Periphery
             │         │         │
             ▼         ▼         ▼
          Docker     Docker     Docker
           Host       Host       Host
```

## Core

Komodo Core provides the central management interface and coordination layer.

It should be treated as an administrative service.

## Periphery

Periphery runs on managed servers and allows them to communicate with Komodo Core.

Periphery installation can be automated as part of preparing a Docker host.

Authentication and onboarding material must remain outside general documentation.

## Database

The current architecture uses a MongoDB-compatible database layer backed by PostgreSQL-compatible document storage rather than depending on a traditional MongoDB deployment.

This design was selected after compatibility limitations were encountered with the available hardware and software environment.

The implementation may evolve as Komodo and its supported database options change.

## Relationship with Ansible

The responsibilities are intentionally different:

```text
Ansible
   └── Configure server
       ├── Base packages
       ├── Docker
       └── Periphery

Komodo
   └── Operate container workloads
       ├── Deploy
       ├── Observe
       └── Manage
```

This prevents the container-management platform from becoming responsible for building its own underlying hosts.

## Security

Komodo has administrative control over container infrastructure.

Protect:

- Administrative access
- Onboarding credentials
- Service credentials
- Database credentials
- Agent communication

Do not commit onboarding keys or authentication material to documentation.

## Troubleshooting

When a server does not appear correctly in Komodo, separate the problem into layers:

1. Docker host health
2. Periphery service state
3. DNS
4. Network connectivity
5. Core reachability
6. Authentication/onboarding
7. Komodo configuration

## Related Documentation

- Services/Docker.md
- Services/Ansible.md
- Architecture/Automation Architecture.md
