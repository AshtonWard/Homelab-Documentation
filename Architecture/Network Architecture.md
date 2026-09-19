# Network Architecture

## Purpose

The homelab network uses VLAN segmentation to separate systems based on their function and trust requirements.

The goal is not simply to create multiple networks. Segmentation should make traffic flows easier to understand, restrict unnecessary communication, and provide clear boundaries between infrastructure roles.

This document intentionally avoids maintaining a complete host, address, port, or firewall-rule inventory.

## Logical Network Segments

The environment currently uses dedicated segments for:

- Management
- Security
- Services
- Game Servers
- User Devices
- Lab / Testing

Each segment represents a functional or security boundary.

## Management

The Management network contains systems responsible for operating the infrastructure.

Examples include:

- Hypervisors
- Network infrastructure
- Automation controllers
- Administrative interfaces

Access to management services should be more restrictive than access to general application services.

## Security

The Security network contains systems responsible for monitoring and protecting the environment.

Examples include:

- SIEM infrastructure
- Security monitoring
- Log aggregation
- Future security tooling

Systems on other networks may initiate specific telemetry or agent communication toward this network when required.

## Services

The Services network hosts shared applications and infrastructure services.

Examples may include:

- Container hosts
- Automation platforms
- Monitoring applications
- Home automation
- Internal application services
- Remote-access infrastructure

Service placement should be based on function rather than convenience.

## Game Servers

Game servers are isolated from management and security infrastructure.

This allows externally accessible or higher-risk application workloads to be controlled independently from core infrastructure.

## User Devices

The User network contains normal client devices.

Client devices should not automatically receive administrative access to management infrastructure simply because they are inside the local network.

## Lab / Testing

The Lab network is intended for systems that may be intentionally insecure, temporary, experimental, or used for security testing.

Examples may include:

- Windows domain labs
- Test clients
- Kali Linux
- Vulnerability testing
- Temporary experiments

Isolation is especially important for this segment.

## Routing

OPNsense performs routing between network segments.

Inter-VLAN communication should be explicitly allowed based on service requirements rather than broadly permitted by default.

Conceptually:

```text
Users ───────────────► Selected Services
Services ────────────► Required Infrastructure
Managed Systems ─────► Security Monitoring
Administration ──────► Management Interfaces
Lab ─────────────────► Explicitly Approved Destinations
```

The actual firewall implementation is maintained separately.

## Switching

Physical VLAN transport is provided by managed switching.

Trunk interfaces carry multiple VLANs where required, particularly toward virtualization hosts and network infrastructure.

Access interfaces should normally belong to a single appropriate VLAN.

## Virtualization

Proxmox bridges are VLAN-aware where required.

Virtual machines are attached to the network segment appropriate for their role.

Network placement should be considered part of VM design rather than something decided after deployment.

## DNS

Internal DNS provides stable names for infrastructure and services.

Applications and automation should prefer DNS names over hard-coded IP addresses when practical.

This allows addressing to change without requiring configuration changes throughout the environment.

## DHCP and Static Addressing

Dynamic addressing is appropriate for ordinary client systems.

Infrastructure systems and services requiring predictable addressing should use controlled static addressing or reservations according to the network standards.

## Security Principle

Network segmentation does not replace host security.

VLANs, firewall rules, authentication, endpoint security, patching, and monitoring should operate as complementary controls.
