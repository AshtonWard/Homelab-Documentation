# Cisco Switching

## Purpose

Managed Cisco switching provides the physical Layer 2 foundation of the homelab network.

The switching layer transports VLANs between infrastructure components while maintaining clear network boundaries.

## Responsibilities

The switching layer provides:

- Physical Ethernet connectivity
- VLAN membership
- 802.1Q trunking
- Access ports
- Connectivity to virtualization hosts
- Connectivity to the firewall/router
- Basic Layer 2 protections

## VLAN Transport

Trunk links carry multiple VLANs between infrastructure devices where required.

Typical trunk relationships include:

```text
Firewall
   │
   ▼
Managed Switch
   │
   ├── Virtualization Host
   ├── Virtualization Host
   └── Other VLAN-Aware Infrastructure
```

Allowed VLAN lists should be intentional rather than automatically carrying every VLAN everywhere.

## Access Ports

Endpoints that do not need to understand VLAN tagging should normally use access ports assigned to the appropriate VLAN.

## Layer 2 Protections

Features such as PortFast and BPDU Guard can be used where appropriate to protect edge ports and improve endpoint behavior.

Their use should match the role of the interface.

## Virtualization

Proxmox hosts may require VLAN-aware trunk connectivity so virtual machines can be placed into different logical networks.

VLAN assignment at the virtualization layer should match the VLAN configuration transported by the physical switch.

## Troubleshooting

When diagnosing network problems, distinguish between:

- Physical link
- VLAN membership
- Trunk configuration
- Layer 3 routing
- Firewall policy
- DNS
- Application connectivity

A routing problem should not be solved by randomly modifying switch configuration, and a VLAN problem should not be treated as an application problem.

## Documentation Boundary

Exact switchport assignments and complete running configurations should not be maintained in general architecture documentation.

Sanitized configuration examples may be documented when they explain a reusable concept.

## Related Documentation

- Architecture/Network Architecture.md
- Standards/Network and VLAN Standards.md
- Runbooks/Troubleshooting Workflow.md
