# Wazuh

## Purpose

Wazuh provides centralized security monitoring for the homelab.

It collects and analyzes security information from managed systems and provides visibility into endpoint activity.

## Architecture

Conceptually:

```text
Linux Hosts ─────┐
                 │
Infrastructure ──┼──► Wazuh Platform
                 │
Service Hosts ───┘
```

Agents on managed systems communicate with the centralized Wazuh infrastructure.

## Responsibilities

Wazuh can provide capabilities such as:

- Endpoint monitoring
- Log collection
- Security-event analysis
- File-integrity monitoring
- Vulnerability-related visibility
- Centralized security telemetry

The exact enabled capabilities may evolve over time.

## Network Placement

Security infrastructure is separated from general application workloads.

Other network segments should only receive the communication required to send appropriate security telemetry or perform approved administration.

## Agent Deployment

Wazuh agents can be installed through Ansible.

The desired deployment pattern is:

```text
Managed Host
    ↓
Ansible Security Role
    ↓
Wazuh Agent
    ↓
Central Wazuh Platform
```

This makes agent deployment repeatable across supported systems.

## DNS

Agents and administrative systems should prefer stable DNS names where practical rather than embedding addresses throughout configuration.

DNS failure should be considered independently from Wazuh failure during troubleshooting.

## Troubleshooting

For an agent that is not reporting:

1. Confirm the endpoint is online.
2. Confirm DNS resolution.
3. Confirm routing.
4. Confirm firewall policy.
5. Confirm the agent is installed.
6. Confirm the agent service is running.
7. Confirm the configured manager reference.
8. Review agent and manager logs.

## Resource Planning

Security analytics platforms can be resource-intensive.

CPU, memory, storage growth, and indexer behavior should be monitored as additional endpoints and telemetry sources are added.

## Documentation Boundary

Do not document agent enrollment secrets, administrative credentials, complete security rules, or a comprehensive list of monitored endpoints.

## Related Documentation

- Architecture/Network Architecture.md
- Services/Ansible.md
- Runbooks/Troubleshooting Workflow.md
