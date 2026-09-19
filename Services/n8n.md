# n8n

## Purpose

n8n provides workflow automation for tasks that benefit from event-driven or application-level orchestration.

It complements infrastructure automation tools such as Ansible and GitHub Actions.

## Role in the Homelab

The intended automation boundaries are:

```text
GitHub Actions
   └── Repository and infrastructure workflows

Ansible
   └── System configuration

n8n
   └── Application and event workflows
```

Using each platform for the type of automation it handles best reduces unnecessary coupling.

## Example Use Cases

Potential n8n workflows include:

- Notifications
- Webhook processing
- Service-to-service automation
- Scheduled application tasks
- API integrations
- Homelab event handling

## Deployment

n8n runs as a service workload and can be hosted on a Docker-capable system.

The underlying host should be provisioned and configured independently from the workflows running inside n8n.

## Data

Workflow definitions and application data may contain sensitive information.

Pay particular attention to:

- API credentials
- Webhook secrets
- Authentication tokens
- Service credentials
- Workflow execution data

## Networking

Only integrations that require access to n8n should be able to reach it.

External webhook exposure, if used, should be deliberate and protected appropriately.

## Documentation Boundary

General documentation may describe workflow architecture and purpose.

Do not copy credentials, webhook secrets, authentication headers, or sensitive payload examples into the repository.

## Related Documentation

- Architecture/Automation Architecture.md
- Services/Docker.md
- Services/Ansible.md
