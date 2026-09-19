# OPNsense

## Purpose

OPNsense provides the routing and firewall layer for the homelab.

It sits between logical network segments and controls how those segments communicate.

This document describes design responsibilities rather than reproducing the live firewall configuration.

## Responsibilities

OPNsense provides:

- Inter-VLAN routing
- Firewall policy enforcement
- DHCP services
- DNS integration
- Network segmentation
- Controlled access between trust zones

Additional network services may be added as requirements evolve.

## Segmentation

The network is divided by function and trust level.

Examples include:

- Management
- Security
- Services
- Game Servers
- User Devices
- Lab / Testing

OPNsense provides the Layer 3 boundary between these networks.

## Firewall Philosophy

Inter-VLAN access should be intentional.

The preferred model is:

```text
Source
   ↓
Required Service
   ↓
Explicit Policy
   ↓
Destination
```

Broad access between networks should be avoided when a narrower rule can satisfy the requirement.

## DHCP

DHCP provides dynamic addressing where static or predictable addressing is unnecessary.

DHCP pools should not overlap addressing reserved for infrastructure.

## DNS

Internal DNS allows infrastructure and applications to reference stable names instead of embedding addresses throughout configuration.

DNS should be treated as a core infrastructure dependency.

When troubleshooting connectivity, verify DNS independently from routing and firewall policy.

## Security

The firewall is one security layer, not the entire security model.

Network policy works alongside:

- Host firewalls
- Authentication
- Patch management
- Endpoint monitoring
- Application security
- VLAN segmentation

## Change Management

Before changing routing or firewall policy:

1. Identify the required source.
2. Identify the required destination.
3. Identify the required service.
4. Determine whether DNS is involved.
5. Apply the narrowest practical rule.
6. Verify expected communication.
7. Verify unrelated access was not unintentionally introduced.

## Documentation Boundary

Do not reproduce the complete active firewall ruleset, public addressing, VPN credentials, certificates, or externally reachable management endpoints in general documentation.

## Related Documentation

- Architecture/Network Architecture.md
- Standards/Network and VLAN Standards.md
- Runbooks/Troubleshooting Workflow.md
