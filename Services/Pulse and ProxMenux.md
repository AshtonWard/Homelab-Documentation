# Pulse and ProxMenux

## Purpose

Pulse and ProxMenux provide supporting visibility and management capabilities for the Proxmox environment.

They supplement the native Proxmox interface rather than replacing the virtualization platform itself.

## Pulse

Pulse provides infrastructure monitoring and visibility into Proxmox resources.

Its role is observational:

- Surface infrastructure health
- Improve visibility across virtualization resources
- Assist with operational monitoring

Monitoring systems should not become the only source of truth for the state of the underlying infrastructure.

## ProxMenux

ProxMenux provides additional tooling and convenience around Proxmox management.

It should be treated as a supporting management utility.

## Relationship to Proxmox

```text
             Proxmox
                │
       ┌────────┴────────┐
       ▼                 ▼
     Pulse            ProxMenux
  Monitoring        Management Aid
```

The authoritative virtualization state remains in Proxmox.

## Network Placement

Monitoring and management tooling should be placed according to the access it requires.

A monitoring service does not automatically need unrestricted administrative access to every network.

## Security

Supporting tools that can inspect or administer infrastructure should be treated according to the privilege they actually possess.

Administrative credentials and internal management endpoints should not be reproduced in this document.

## Related Documentation

- Services/Proxmox.md
- Architecture/Homelab Architecture.md
- Architecture/Network Architecture.md
