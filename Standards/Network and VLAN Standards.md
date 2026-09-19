# Network and VLAN Standards

## Purpose

This document defines conventions used when creating networks and assigning systems.

It intentionally describes the addressing model without maintaining a complete network inventory.

## VLAN Design

VLANs should represent meaningful functional or security boundaries.

Current categories include:

- Management
- Security
- Services
- Game Servers
- Users
- Lab / Testing

A new VLAN should not be created solely because a new application is deployed.

## Addressing

Each VLAN uses its own IPv4 subnet.

The addressing convention should make it possible to recognize the associated network from an address while remaining simple enough to administer.

Exact active host assignments belong in the appropriate infrastructure configuration rather than this document.

## Infrastructure Reservation

A predictable portion of each subnet should be reserved for network and core infrastructure.

Examples of systems that may occupy reserved addressing include:

- Gateway
- Switching
- Hypervisor interfaces
- Core infrastructure services

General servers should begin after the reserved infrastructure range.

## Static Addressing

Static or predictable addressing is appropriate for:

- Hypervisors
- Network infrastructure
- DNS-dependent services
- Automation controllers
- Security infrastructure
- Servers referenced by other systems

## DHCP

DHCP is preferred for ordinary endpoints and systems that do not require a stable address.

DHCP pools must not overlap ranges reserved for static infrastructure.

## VM IDs

Virtual machine identifiers should follow a predictable convention where practical.

The convention may encode information such as:

- Network placement
- Host address allocation

The VM ID is an administrative identifier and should not be treated as a security boundary.

## Documentation

Avoid maintaining duplicate address inventories across multiple Markdown documents.

The authoritative configuration should remain with the systems responsible for the network and automation.

Documentation should describe the convention and design.
